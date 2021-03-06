---
title: "mxallowd"
date: 2014-11-07T09:49:22+01:00
slug: "mxallowd.en"
---
<div id="ml">
		<p> An meine deutschen Besucher: Es gibt eine <a href="/mxallowd" id="ml_link">deutsche version</a> dieser Seite. </p>
	</div>
	<div id="content"><p>
mxallowd is a daemon for linux/netfilter (using libnetfilter_queue) or BSD/pf
(via pflog) which implements a slightly improved <a
href="http://nolisting.org/">nolisting</a> mechanism. Basically your nameserver
has to be configured to return two MX ip addresses of which one does not run a
mail server on port 25 (the one with higher priority). Most spammers try to
connect directly to the first mailserver – mxallowd blocks that. You have to
connect to the first one and then to the second one, direct connections do not
work. Real mailservers (not spammers) have to try all MX ip addresses in order
(sorted by priority) until they succeed in delivering the mail.
</p>

<p>
The problem with nolisting is that some spammers try (probably because of the
nolisting) to connect directly to the second MX ("direct-to-second-mx"). This
is where mxallowd turns in: You cannot connect to the second mailserver aswell,
except if you have tried connecting to the first mailserver before (you are
whitelisted then).
</p>

<p>
This problem could be solved using iptables with the module ipt_recent aswell,
if it wasn’t for one little detail: Some providers (for example Google Mail)
use the same DNS name but different ip addresses when trying the mailservers in
order. So ipt_recent, which works solely using ip addresses, does not let mails
from Google Mail through. mxallowd in contrary whitelists all ip addresses of
the DNS name (except if you specify the option --no-rdns-whitelist of course).
</p>

<h2>Setup on Linux</h2>

<p>
In order to let mxallowd handle the connections, one has to add the following
iptables-rule:

<pre>
iptables -A INPUT -p tcp --dport 25 -m state --state NEW -j NFQUEUE --queue-num 23
</pre>

<p>
If inserting this rule fails you have to insert the queue module into the
kernel using <code>modprobe nfnetlink_queue</code>.
</p>

<p>
You can modify this rule of course to handle, for example, only certain ip
addresses or to accept connections from certain ip addresses (whitelisting, use
-j ACCEPT at the end of the rule).
</p>

<h2>Setup on BSD</h2>

<p>
Your /etc/pf.conf could look like this:
</p>

<pre>
table  persist

real_mailserver="192.168.1.4"
fake_mailserver="192.168.1.3"

real_mailserver6="2001:dead:beef::1"
fake_mailserver6="2001:dead:beef::2"

pass in quick log on fxp0 proto tcp from  \
	     to $real_mailserver port smtp
pass in quick log on fxp0 inet6 proto tcp from  \
	     to $real_mailserver6 port smtp
block in log on fxp0 proto tcp \
	      to { $fake_mailserver $real_mailserver } port smtp
block in log on fxp0 inet6 proto tcp \
	      to { $fake_mailserver6 $real_mailserver6 } port smtp
</pre>

<p>
The important things are that the table mx-white exist and that the pass- and
the block-rules specify the log modifier. If you use another pflog-interface,
you can tell mxallowd this via parameter.
</p>

<h2>Help, i cannot send mails anymore!</h2>

<p>
That’s right – if you use the same mailserver to send your mails, your
mailclient will probably try only the first ip address (which does not run a
mailserver). I’d recommend sending mails via SMTPS (SSL) because its port (465)
will not be filtered if you don’t explicitly set it up (see "Setup").
Alternatively, you could run your mailserver additionally on another port which
only you use (spammers won’t do a portscan, if they don’t even use
standard-compliant mailers…). If you have a static ip address you can whitelist
it in iptables of course (see "Setup").
</p>
</div>
	<h3>Downloads</h3>
	<ul id="downloads"><li><a class="download_filename" href="/mxallowd/mxallowd.1.9.tar.bz2"><span class="download_name">mxallowd 1.9</span></a> (<span class="download_size">33K</span>, <a class="download_gpg" href="/mxallowd/mxallowd.1.9.tar.bz2.asc">GPG signature</a>)</li></ul>
	<h3>License</h3>
	<p><span class="name">mxallowd</span> is free open source software under the <span class="license">GPL2</span>.</p>
	<div id="development">
		<h3>Development</h3>
		<p>Current development can be followd <a class="dev_url" href="http://code.stapelberg.de/git/mxallowd">in gitweb</a>.</p>
	</div>
	<h3>Feedback</h3>
	<p>If you would like to drop me a message, please <a href="/Impressum">send me an email</a>.</p>
</div>
