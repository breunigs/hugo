---
layout: post
title:  "Migrating KVM virtual machines to a new host"
date:   2012-10-05 14:12:00
categories: Artikel
Aliases:
  - /Artikel/kvm_migration
---


<p>
About two years ago, I rented a dedicated server to host a few virtual
machines. It’s been running fine ever since, but at some point, faster and more
energy-efficient hardware is available for the same price. I decided to
rent a new server mostly for the additional RAM, which is very valuable when
hosting virtual machines — other resources such as CPU, disk and network are
usually not a bottleneck.
</p>

<p>
This article documents the different steps I used to migrate my (KVM and
libvirt) virtual machines from my old server (called <strong>OLD</strong> with
IP address 192.168.2.2 from now on) to my new server (called
<strong>NEW</strong> with IP address 192.168.9.9 from now on).
</p>

<p>
Note that I did not do a live migration because I don’t use network storage
which is accessible from both machines. I needed to transfer the data from one
machine to the other.
</p>

<h2>The plan</h2>

<ol>
<li>Transfer the virtual machine’s data to NEW.</li>
<li>Stop the virtual machine on OLD, boot it up on NEW.</li>
<li>Redirect traffic from OLD to NEW (the IPs are transferred to the new server
later on).</li>
</ol>

<p>
The obvious problem is that the virtual machine is still running between step 1
and 2 and therefore it might modify the data. Normally, to avoid
inconsistencies, you use an LVM snapshot, but that doesn’t help you with
additional, consistent data which the virtual machine may write. The solution
is described below, but let’s start with the tunnel setup first.
</p>

<h2>Traffic redirection: preparation</h2>

<p>
To be able to boot up the virtual machine on NEW and use it immediately
(without contacting the hoster to change the routing table), we set up a tunnel
from OLD to NEW. <strong>Beware:</strong> Depending on the routing setup at
your hoster, there might be a reverse-path filter in our way. Such a filter
blocks packets which come from the wrong path, in our case it might only allow
packets originating from OLD, but not from NEW. Therefore, double-check first
if you can use a tunnel like this at your hoster before you follow this
article.
</p>

<p>
On OLD, add the following entries to <code>/etc/network/interfaces</code>:
</p>

<pre>
auto legacy
iface legacy inet tunnel
	mode     gre
	local    192.168.2.2
	endpoint 192.168.9.9
	address  10.0.1.1
	netmask  255.255.255.0
	ttl      255

auto legacy6
iface legacy6 inet tunnel
	mode     sit
	local    192.168.2.2
	endpoint 192.168.9.9
	address  fd26:a975:9d12::1
	netmask  48
</pre>

<p>
On NEW, add the same entries, but swap the "local" and "endpoint" fields and
use a different IP address (e.g. 10.0.1.2 and fd26:a975:9d12::2). The IPv6
address is an automatically generated <a
href="http://en.wikipedia.org/wiki/Unique_local_address">RFC4193 (Unique local
address)<a> address, generated with <a
href="http://www.hznet.de/tools/generate-rfc4193-addr">Holger Zuleger’s
generate-rfc4193-addr.sh</a>.
</p>

<p>
Afterwards, bring up the networks with <code>ifup legacy</code> and <code>ifup
legacy6</code> on OLD and NEW and check that they can ping each other.
</p>

<h2>Transferring the data</h2>

<p>
We will transfer the data in two steps: First, we just copy the whole block
device over, then we shut down the virtual machine and copy only the
differences. Copying the differences is usually done in a few seconds, while
a block device transfer of a 10 GiB device takes about 20 minutes, so this
technique reduces the total down time for the virtual machine to a minimum.
If you tend to chose too big disk sizes for your VMs, waiting for a long time
to transfer them might be a lesson to choose smaller sizes in the future… :-)
</p>

<p>
First, create the new logical volume and take a snapshot of the old one (the
snapshot size determines how much data the virtual machine can write as long as
it’s still running):
</p>

<pre>
NEW # lvcreate -L 20G -n domu-web newhost
OLD # lvcreate --snapshot -L10G -n web-lvmsync oldhost/domu-web
</pre>

<p>
Then, transfer the data, compressed, with a progress bar. I just love the power
of pipes when seeing such a command :-).
</p>

<pre>
OLD # dd if=/dev/oldhost/domu-web bs=1M | pv -ptrb -s 20G | \
      gzip -3c | ssh new '(gunzip -c - | dd of=/dev/newhost/domu-web)'
</pre>

<h2>Transferring the differences</h2>

<p>
Afterwards, shut down the VM, use <a
href="https://github.com/mpalmer/lvmsync">lvmsync</a> to transfer the
differences and boot the VM on NEW:
</p>

<pre>
web # shutdown -h now
OLD # lvmsync /dev/oldhost/web-lvmsync new:/dev/newhost/domu-weg
NEW # virsh create /etc/libvirt/qemu/web.xml
</pre>

<p>
You can copy the file <code>web.xml</code> from OLD and modify it, or, if you
have many changes in your setup, start with a fresh one (pay attention to
keeping the same MAC address).
</p>

<h2>Redirecting the traffic</h2>

<p>
The final step is easy: just add the appropriate route(s) on OLD (don’t forget
to make them persistent in <code>/etc/network/interfaces</code>):
</p>

<pre>
OLD # ip -4 route add 79.140.39.195/32 via 10.0.1.1 dev legacy
OLD # ip -6 route add 2001:4d88:100e:2::/64 via fd26:a975:9d12::1 dev legacy6
</pre>

<p>
And we’re done! The total downtime of the VM is a few minutes — the time it
needs to shut down, transfer differences and boot up again. Booting might take
longer than normal if a file system check is necessary, thus it’s not done in a
few seconds probably.
</p>
