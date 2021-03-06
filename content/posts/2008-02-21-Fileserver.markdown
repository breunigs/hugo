---
layout: post
title:  "Dokumentation: Aufbau meines Fileservers"
date:   2008-02-21 10:00:00
categories: Artikel
Aliases:
  - /Artikel/Fileserver
---



<h3>Inhaltsverzeichnis</h3>
<ul>
	<li class="level1">Einleitung</li>
		<li class="level2"><a href="#skalierungsprobleme">Update: Mehr Karten und Skalierungsprobleme</a></li>
	<li class="level1"><a href="#gehaeuse">Das Gehäuse (Yeong Yang W201)</a></li>
	<li class="level1"><a href="#installation">Installation</a></li>
		<li class="level2"><a href="#kernel">Kernel kompilieren</a></li>
	<li class="level1"><a href="#grundeinstellungen">Grundeinstellungen</a></li>
	<li class="level1"><a href="#initrd">Initrd erstellen</a></li>
		<li class="level2"><a href="#update_initrd">Update: Neue initrd</a></li>
	<li class="level1"><a href="#bootloader">Bootloader installieren</a></li>
	<li class="level1"><a href="#nach_boot">Nach dem Boot</a></li>
	<li class="level1"><a href="#automount">Script zum automatischen Mounten</a></li>
	<li class="level1"><a href="#encrypted_swap">Encrypted swap</a></li>
	<li class="level1"><a href="#anwendungen">Abschluss: Serveranwendungen installieren</a></li>
</ul>

<p>
Als System habe ich mir für Gentoo Linux entschieden, da ich ohnehin vorhabe,
die Serverprogramme selbst zu kompilieren um die nicht benötigten Features von
vorneherein draußen zu lassen und etwas Geschwindigkeit herauszuholen. Außerdem
klappte die Einrichtung der Festplattenverschlüsselung und des Software-RAIDs
erstaunlich problemlos.
</p>

<p>
Die Komponenten des Systems sind schon etwas älter, haben mir aber gute Dienste
erwiesen, sodass ich sie weiterhin verwenden werde. Im einzelnen sind das:
</p>
<ul>
	<li>AMD Athlon XP 2400+</li>
	<li>1024 MB Infineon DDR-RAM</li>
	<li>EPoX 8RDA3 Mainboard (nForce2-400 Chipsatz, weder Onboard-LAN noch SATA)</li>
	<li>Promise SATA300 TX4 (SATA-Controller mit 4 Anschlüssen)</li>
	<li>D-Link DGE-528T Gigabit-LAN-Karte</li>
	<li>WDC WD2000JB-00GVA0 (200 GB)</li>
	<li>WDC WD2500JB-00GVA0 (250 GB)</li>
	<li>WDC WD1200JB-75CRA0 (120 GB)</li>
	<li>WDC WD740GD-00FL (74 GB, Systemplatte)</li>
	<li>SAMSUNG HD400LJ (400 GB)</li>
</ul>
<p>
(Ja, der Festplattenbestand ist historisch gewachsen ;-).)
</p>
<p>
Eingebaut wird das ganze in ein 19&quot;-Rackgehäuse von Yeong-Yang. Alle
Platten sollen komplett verschlüsselt werden, die Heimverzeichnisse der
einzelnen User sollen zudem noch auf einem Software-RAID-1 abgelegt werden.
</p>

<a name="skalierungsprobleme"><h2>Update: Mehr Karten und Skalierungsprobleme</h2></a>

<p>
Nachdem ich im Laufe der Zeit für Asterisk eine Fritz!ISDN PCI-Karte einbaute
und dann noch der 3WARE-RAID-Controller dazukam, stürzte der Rechner scheinbar
unregelmäßig ab. Genauere Analysen brachten dann zu Tage, dass das Problem sich
bei hoher Interrupt-Load zeigte. Durch den Vollausbau (Ethernet, ISDN, 2xSATA)
an PCI-Karten war diese Auslastung erheblich höher als früher.
</p>

<p>
Das Problem äußerte sich dadurch, dass die System-Festplatte, welche am
Promise-Controller hing, plötzlich nicht mehr vorhanden war. Nach dem Tausch
des Controllers durch einen Dawicontrol DC154 traten die selben Probleme zwar
weiterhin auf, aber die Festplatte verschwand nicht. Der Promise-Treiber (oder
Controller?) konnte sich also von einer SATA-Exception nicht erholen. Beim
Dawicontrol (mit SiL-Chipsatz) klappte das, aber dass das Problem immernoch
auftrat, war merkwürdig.
</p>

<p>
Da auch verschiedene APIC-Modi nichts bewirkten (diese sind für dynamische
IRQ-Zuteilung zuständig), entschied ich mich, die Hardware auszutauschen (also
CPU, Mainboard, RAM). Mittlerweile läuft das System mit den neuen Komponenten
stabil. Mehr zu den neuen Komponenten gibt’s in einem bald folgenden Artikel.
</p>

<a name="gehaeuse"><h1>Das Gehäuse (Yeong Yang W201)</h1></a>
<p>
Da ich mit Yeong Yang-Gehäusen auch bisher schon gute Erfahrungen gemacht hatte
und die Testberichte sehr überzeugend klangen, bestellte ich mir das
zugegebenermaßen schwer zu bekommende W201 bei <a
href="http://www.pc-planet.de/" target="_blank">pc-planet.de</a>. Es ist auf
EATX-Boards ausgelegt, hat aber passende Löcher für normale ATX- und
MATX-Boards.
</p>

<p>
Der Einbau von Netzteil, Mainboard und PCI-Karten klappte problemlos. Zu
beachten ist, dass die beiden kleinen Lüfter direkt über den PCI-Karten erst
fest eingebaut werden sollten, wenn man sich sicher ist, dass mit den Karten
soweit alles in Ordnung ist, da man die Lüfter vor jedem Kartentausch
abschrauben muss ;-).
</p>

<p>
Die Festplatten werden in separat kaufbaren Käfigen untergebracht, jeweils 5
Stück pro Käfig. Diese sind so ausgelegt, dass die Festplatten so ausgerichtet
sind, wie sie es in einem normalen Gehäuse (Tower) sind, wenn man das Gehäuse
ins Rack einbaut (man könnte es auch aufrecht anbringen).
</p>

<p>
Von diesen Käfigen kann man zwei Stück einbauen, somit hätte man also Platz für
10 Festplatten. Außerdem sind noch zwei 5,25&quot;-Einschübe frei und das
Gehäuse bietet noch eine etwas außergewöhnliche Befestigungsmöglichkeit auf dem
vorderen Käfig (siehe Bild ;-)). Macht insgesamt 14 Festplatten.
</p>

<p>
Die eingebauten Lüfter sind angenehm leise (für ’nen Server, also erwartet
keinen Super-Silent-PC).
</p>

<p>
Alles in allem bin ich mit dem Gehäuse sehr zufrieden, alles klappte
problemlos, ich hab’ mich nicht am Gehäuse, sondern am Inhalt der PCI-Karten
geschnitten ;-). <a href="/Galerie/Rackcase" title="Bildergalerie:
Rackgehäuse">Bilder habe ich natürlich auch gemacht…</a>
</p>

<a name="installation"><h1>Installation</h1></a>
<p>
Ich habe die Live-CD Gentoo 2006.1 verwendet, es sollte mit neueren Versionen
aber auch funktionieren.
</p>
<p>
Nach dem Booten in die Live-CD empfiehlt es sich, nachzusehen, ob alle
Festplatten und (zumindest die wichtigsten) Geräte erkannt wurden. Somit kann
man später Hardware-Fehler ausschließen, falls etwas nicht funktioniert.
</p>

<p>
Anschließend werden wir die Systemplatte (mit <code>fdisk</code>) formatieren,
ich habe sie bei mir in 50 MB für den Kernel und initrd (viel zu groß, ich
weiß), 2 GB Swap und den Rest (von 74 GB in diesem Fall) für <code>/</code>
aufgeteilt.
</p>

<p>
Die Festplatte ist bei mir am zweiten SATA-Port angeschlossen, die Devices sind
jedoch umgedreht numeriert. Ob das am Treiber oder am Controller selbst liegt,
weiß ich nicht. Jedenfalls booten wir deshalb von <code>/dev/sdc</code>, statt
von <code>/dev/sda</code> (der Controller nimmt einfach die erste Platte, die
er findet).
</p>

<p>
Wir erstellen dann die Dateisysteme für die jeweiligen Partitionen: ext2 für
/boot, verschlüsseltes ext3 für / und das verschlüsselte Swap setzen wir später
auf.
</p>
<pre>
# mkfs.ext2 /dev/sdc1
# cryptsetup -c blowfish-cbc-essiv:sha256 -y -s 256 luksFormat /dev/sdc3
# cryptsetup luksOpen /dev/sdc3 root
# mkfs.ext2 -j /dev/mapper/root
</pre>

<p>
Dann führen wir eine ganz normale Gentoo-Installation durch: Wir laden das
Stage-3-Archiv und Portage herunter und entpacken das ganze:
</p>
<pre>
# mount /dev/mapper/root /mnt/gentoo
# cd /mnt/gentoo
# wget http://gentoo.osuosl.org/releases/x86/current/stages/stage3-i686-2006.1.tar.bz2
# tar xjpf stage3*
# cd usr
# wget http://gentoo.osuosl.org/snapshots/portage-latest.tar.bz2
# tar xjf portage*
</pre>
<p>
Anschließend kopieren wir uns noch die Nameserver-Konfiguration (die, im
Gegensatz zu den IP-Einstellungen, nicht erhalten bleiben nach dem Changeroot)
und /proc und /dev:
</p>
<pre>
# cp /etc/resolv.conf /mnt/gentoo/etc/resolv.conf
# mount -t proc none /mnt/gentoo/proc
# mount -o bind /dev /mnt/gentoo/dev
</pre>

<p>
Dann wechseln wir in die neue Gentoo-Installation:
</p>
<pre>
# chroot /mnt/gentoo /bin/bash
# env-update
# source /etc/profile
# export PS1="(chroot) $PS1"
</pre>
<p>
Wir aktualisieren den Portage-Tree und dann Portage selbst (falls es ein Update
gibt):
</p>
<pre>
# emerge --sync
# emerge portage
</pre>
<p>
Danach setzen wir das Serverprofil:
</p>

<pre>
# rm /etc/make.profile
# ln -s /usr/portage/profiles/default-linux/x86/2006.1/server /etc/make.profile
</pre>

<p>
Und passen die Einstellungen zum Kompilieren an unsere Plattform und Wünsche
an:
</p>
<p class="filenameHeader">/etc/make.conf</p>
<pre>
CFLAGS="-O2 -march=athlon-xp -pipe"
USE="-ldap -x11"
</pre>

<p>
Anschließend wählen wir noch die Locales:
</p>
<p class="filenameHeader">/etc/locale.gen</p>
<pre>
en_US ISO-8859-1
de_DE ISO-8859-1
de_DE@euro ISO-8859-15
</pre>

<p>
…und generieren die selbigen:
</p>
<pre>
# locale-gen
</pre>
<p>
Noch kurz die Zeitzone setzen, dann geht's ans Kernelbacken:
</p>
<pre>
# cp /usr/share/zoneinfo/Europe/Berlin /etc/localtime
</pre>

<a name="kernel"><h2>Kernel kompilieren</h2></a>

<p>
Zuerst lassen wir uns die letzten Kernel-Sources herunterladen und wechseln in das Verzeichnis derselbigen:
</p>
<pre>
# USE="-doc symlink" emerge gentoo-sources
# cd /usr/src/linux
</pre>

<p>
Dann konfigurieren wir den Kernel nach unseren Wünschen. Dabei ist zu beachten,
dass <strong>unbedingt</strong> Treiber für den entsprechenden
IDE-/SATA-Controller (bei mir <code>CONFIG_BLK_DEV_AMD74XX</code> und
<code>CONFIG_SATA_SX4</code>) fest im Kernel einkompiliert sind, ebenso das
Dateisystem der root-Partition, sonst können wir den Rechner später nicht
booten. Außerdem brauchen wir im Kernel einkompilierte Unterstützung für das
Crypt-Target des Device-Mappers (<code>CONFIG_DM_CRYPT</code>) und die
Crypto-Algorithmen (<code>CONFIG_CRYPTO, CONFIG_CRYPTO_CBC,
CONFIG_CRYPTO_BLOWFISH, CONFIG_CRYPTO_SHA256</code>).
</p>

<p>
Um den Kernel nun zu kompilieren, die ausgewählten Module (bei mir lediglich
der TUN-treiber für OpenVPN, <code>CONFIG_TUN</code>) zu installieren und den
Kernel in die <code>/boot</code>-Partition zu kopieren nutzen wir folgenden
Befehl:
</p>
<pre>
# make &amp;&amp; make modules_install
# cp arch/i386/boot/bzImage /boot/kernel-2.6.19-r5-stability
</pre>

<a name="grundeinstellungen"><h2>Grundeinstellungen</h2></a>

<p>
Nun sollten wir dem System noch klarmachen, welche (virtuellen) Laufwerke er
wohin zu mounten hat bei Systemstart:
</p>
<p class="filenameHeader">/etc/fstab</p>
<pre>
/dev/sdc1		/boot		ext2	noauto,noatime		1	2
/dev/mapper/root	/		ext3	noatime			0	1
proc			/proc		proc	defaults		0	0
shm			/dev/shm	tmpfs	nodev,nosuid,noexec	0	0
</pre>

<p>
Dann setzen wir noch den Hostname:
</p>
<p class="filenameHeader">/etc/conf.d/hostname</p>
<pre>
HOSTNAME="stability"
</pre>

<p>
Und erledigen die Netzwerkkonfiguration:
</p>
<p class="filenameHeader">/etc/conf.d/net</p>
<pre>
dns_servers_eth0="192.168.1.2 192.168.1.1 194.25.2.129"
dns_sortlist_eth0="192.168.1.0/24"
config_eth0=( "192.168.1.5 netmask 255.255.255.0 brd 192.168.1.255" )
routes_eth0=( "default gw 192.168.1.1" )
</pre>

<p>
Dieses Interface möchten wir beim Systemstart automatisch konfigurieren lassen:
</p>
<pre>
# rc-update add net.eth0 default
</pre>

<p>
Ein Root-Passwort sollten wir nicht vergessen:
</p>
<pre>
# passwd
</pre>

<p>
Falls alle Stränge reißen, möchten wir uns eventuell über Serielle Konsole anmelden:
</p>
<pre>
# echo "tts/0" &gt;&gt; /etc/securetty
</pre>

<p>Zu guter letzt installieren wir noch ein paar benötigte Systemprogramme:
<pre>
# emerge syslog-ng vixie-cron slocate
# rc-update add syslog-ng default
# rc-update add vixie-cron default
</pre>

<a name="initrd"><h2>Initrd erstellen</h2></a>
<p>
Die Initrd ist eine Ramdisk (wie das rd im Namen erkennen lässt), welche nach
Laden des Kernels und vor dem Laden von <code>init</code> ausgeführt wird. Wir
brauchen eine initrd, damit wir die verschlüsselte Platte mounten können, denn
das System soll ja davon booten.
</p>

<p>
Auf dem neuen System ist übrigens noch kein <code>cryptsetup-luks</code>
installiert, was wir nun nachholen sollten:
</p>
<pre>
# emerge cryptsetup-luks
</pre>

<p>
Eine solche Ramdisk ist nichts anderes, als ein (Gzip-)komprimierte ext2-Image,
welches wir mit den folgenden Befehlen erzeugen und mounten können:
</p>
<pre>
# cd /mnt
# mkdir ramdisk
# dd if=/dev/zero of=initrd bs=1024 count=4096
# mke2fs -F ./initrd
# mount -o loop initrd ramdisk
</pre>

<p>
Nun legen wir also in <code>/mnt/ramdisk</code> den Inhalt der Ramdisk ab – wir
brauchen ein paar Programme und Bibliotheken, sowie das Initscript selbst:
</p>
<pre>
# mkdir ramdisk/{bin,dev,lib,sbin,proc}
# cp -a /bin/{bash,cat,chroot,cryptsetup,echo,mkdir,mknod,mount,rm,sed,sleep,umount} ramdisk/bin/
# cp -a /sbin/{blockdev,pivot_root} ramdisk/sbin
# ln -s bash ramdisk/bin/sh
</pre>

<p>
Außerdem müssen wir die Device-Node der Console (fix) und der zu öffnenden
Festplatte erstellen, welche wir mit <code>file /dev/sdc</code> herausfinden:
</p>
<pre>
# mknod ramdisk/dev/console c 5 1
# file /dev/sdc3
/dev/sdc3: block special (8/35)
# mknod ramdisk/dev/sdc3 b 8 35
</pre>

<p>
Für jedes der kopierten Programme brauchen wir noch die entsprechenden
Libraries, welche man mit dem <code>ldd</code>-Befehl herausfinden kann:
</p>
<pre>
cp /lib/{ld-2.4.so,libblkid.so.1.0,libc-2.4.so,libdevmapper.so.1.02} ramdisk/lib
cp /lib/{libdl-2.4.so,libncurses.so.5.5,libuuid.so.1.2,libz.so.1} ramdisk/lib
cp /lib/{ld-linux.so.2,libblkid.so.1,libc.so.6,libdl.so.2,libncurses.so.5,libuuid.so.1} ramdisk/lib
</pre>

<p>
Anschließend erzeugen wir das eigentliche Script (stammt größtenteils aus dem
Gentoo-Wiki, siehe Quellen):
</p>
<p class="filenameHeader">ramdisk/sbin/init</p>
<pre>
#!/bin/sh

echo "INITRD Loading :-)"
ROOT_DEV=/dev/sdc3
ROOT_MAP=root

export PATH=/bin:/sbin
mount -t proc proc /proc
CMDLINE=`cat /proc/cmdline`
sh /sbin/devmap_mknod.sh
if [ $? -ne 0 ]; then
        echo Creation of /dev/mapper/control failed
        exit 0
fi

echo "Ok, let's have a look at the headers..."
cryptsetup luksDump ${ROOT_DEV}

count=0
sesam=1
while [ ${sesam} -ne 0 ]; do
        if [ "$count" = "3" ]; then
                echo "Hm, doesnt seem to work. Go play with the shell. Press Ctrl+D when done."
                bash
                echo System halted
                exit 0
        fi

        count=$(( $count + 1 ))
        echo "Enter passphrase:"
        read -s pass
        echo
        echo ${pass} | cryptsetup luksOpen ${ROOT_DEV} ${ROOT_MAP}
        sesam=$?
done
echo "Mounting root..."
mount /dev/mapper/${ROOT_MAP} /mnt
if [ $? -ne 0 ]; then
        echo "Mounting root failed"
        cryptsetup luksClose ${ROOT_MAP}
        exit 0
fi

echo "Booting..."
umount /proc
cd /mnt
mkdir initrd
pivot_root . initrd
echo "Starting init"
exec chroot . /bin/sh &lt;&lt;- EOF &gt;/dev/console 2&gt;&amp;1
umount initrd
rm -rf initrd
blockdev --flushbufs /dev/ram0
echo "Exec init ${CMDLINE}"
exec /sbin/init ${CMDLINE}
EOF
</pre>

<p>
Im Gegensatz zu der Originalversion von Gentoo-Wiki gibt diese Version beim
Boot die Header des zu mountenden Devices aus, wodurch man sieht, ob die
Device-Node korrekt erzeugt wurde, und ob mit dem LUKS-Header alles in Ordnung
ist. Außerdem bekommt man nach drei erfolglosen Versuchen, das Passwort
einzugeben, eine Shell. Diese Zeilen sollte man, nachdem das System
ordnungsgemäß bootet, entfernen.
</p>

<p>
Kleiner Tipp: Bei mir verursachte ein Passwort, welches Sonderzeichen wie
Doppelpunkte und Klammern enthielt, Probleme. Die Platte ließ sich einfach
nicht mounten, trotz korrekter Eingabe des Passworts. Letztendlich habe ich ein
anderes gewählt. Man sollte auch darauf achten, dass das Tastaturlayout noch
nicht umgestellt ist, das heißt, dass man mit dem Layout <code>en_US</code>
tippt (vertauschte Position von y und z und der Sonderzeichen).
</p>

<p>
Hier noch das Script um die device-mapper-Nodes anzulegen:
</p>
<p class="filenameHeader">ramdisk/sbin/devmap_mknod.sh</p>
<pre>
#!/bin/sh

# Startup script to create the device-mapper control device
# on non-devfs systems.
# Non-zero exit status indicates failure.
DM_DIR="mapper"
DM_NAME="device-mapper"

set -e

DIR="/dev/$DM_DIR"
CONTROL="$DIR/control"

# Check for devfs, procfs
if test -e /dev/.devfsd ; then
        echo "devfs detected: devmap_mknod.sh script not required."
        exit
fi
if test ! -e /proc/devices ; then
        echo "procfs not found; please create $CONTROL manually."
        exit 1
fi
# Get major, minor, and mknod
MAJOR=$(sed -n 's/^ *\([0-9]\+\) \+misc$/\1/p' /proc/devices)
MINOR=$(sed -n "s/^ *\([0-9]\+\) \+$DM_NAME\$/\1/p" /proc/misc)

if test -z "$MAJOR" -o -z "$MINOR" ; then
        echo "$DM_NAME kernel module not loaded: can't create $CONTROL."
        exit 1
fi

mkdir -p --mode=755 $DIR
test -e $CONTROL && rm -f $CONTROL

echo "Creating $CONTROL character device with major:$MAJOR minor:$MINOR."
mknod --mode=600 $CONTROL c $MAJOR $MINOR
</pre>

<p>
Dieser Schritt ist wichtig, sonst wird gar nichts ausgeführt:
</p>
<pre>
# chmod 755 ramdisk/sbin/{init,devmap_mknod.sh}
</pre>

<p>
So, das war’s. Nun unmounten wir den ramdisk-Ordner, packen die initrd mit
<code>gzip</code> und kopieren sie in die <code>/boot</code>-Partition:
</p>
<pre>
# umount ramdisk
# gzip initrd
# cp initrd.gz /boot/
</pre>

<a name="update_initrd"><h3>Update: Neue initrd</h3></a>

<p>
Mittlerweile läuft eine neue <code>initrd</code>, welche den Schlüssel
automatisch von einem anderen, vertrauenswürdigen Rechner via LAN lädt. Auf
diese werde ich in einem bald folgenden Artikel genauer eingehen. Für die neue
Hardware (siehe Update ganz oben) wurde dies notwendig, da ich keine
PCI-Express-Grafikkarte übrig habe (und auf dem neuen Board keine
PCI-Steckplätze mehr frei) und somit im Regelfall blind das Passwort eingeben
müsste.
</p>

<a name="bootloader"><h2>Bootloader installieren</h2></a>

<p>
Als Bootloader verwende ich am liebsten GRUB, das hat keinen besonderen Grund –
er funktioniert einfach und ich kenne mich einigermaßen damit aus :-). Also
rauf damit auf’s System:
</p>
<pre>
emerge grub
</pre>

<p>
Die Konfigurationsdatei von GRUB ist <code>/boot/grub/menu.lst</code>. Dort
bringen wir GRUB nun bei, welchen Kernel auf welcher Festplatte mit welcher
Initrd er booten soll:
</p>
<p class="filenameHeader">/boot/grub/menu.lst</p>
<pre>
default 0
timeout 5

title=Gentoo Linux 2.6.19-r5-stability
root (hd0,0)
kernel /kernel-2.6.19-r5-stability root=/dev/ram0 ide0=0x1f0,0x3f6,14 rw
initrd /initrd.gz
boot
</pre>

<p>
Die Nummern der Festplatte bekommt man übrigens per Tab-Completion in GRUB
selbst heraus. Es macht also nichts, wenn hier (hd0,0) steht, obwohl die
Festplatte eigentlich (hd1,2) wäre, wir können das vor dem ersten Boot nochmal
prüfen und gegebenenfalls editieren.
</p>

<p>
Wozu eigentlich die Angabe von ide0? Tja, anscheinend habe ich in meiner
Kernelkonfiguration irgendetwas anders gemacht, als derjenige, der den Kernel
der Gentoo Live-CD gebaut hat. Dort findet der Kernel nämlich den ide0-Channel.
Bei meinem Kernel findet er ihn nicht, außer man zeigt ihm ganz genau den Weg.
Die Werte sind übrigens Standardwerte, wie in
<code>Documentation/ide.txt</code> nachzulesen ist. Wenn jemand ’nen Hinweis
hat, wie man das Ändern kann, immer her damit :-).
</p>

<p>
Nun installieren wir GRUB noch in den Master Boot Record der Festplatte und
leiten dann den Reboot ein:
</p>

<pre>
# grub-install /dev/sdc
# exit
# umount /mnt/gentoo/boot /mnt/gentoo/proc /mnt/gentoo/dev /mnt/gentoo
# cryptsetup luksClose root
# reboot
</pre>

<a name="nach_boot"><h2>Nach dem Boot</h2></a>

<p>
Je nach Lärmpegel des Servers werden wir nun den befreiendsten Schritt
durchführen: SSHd für Remote-Zugriff installieren und gegebenenfalls den
Rechner in ein anderes Zimmer tragen ;-):
</p>

<pre>
# emerge openssh
# rc-update add sshd default
# /etc/init.d/sshd start
</pre>

<p>
Dann werden wir eine Benutzerkennung und eine Gruppe anlegen, damit wir nicht
immer als root arbeiten müssen:
</p>

<pre>
# groupadd staff
# useradd -m -u 101 -g staff -G cdrom,portage,usb,video,wheel -s /bin/bash michael -c "Michael Stapelberg"
</pre>

<p>
Und wenn wir es uns gerade gemütlich machen, installieren wir doch auch gleich
einen gescheiten Editor, allerdings ohne Perl/Python-Bindings:
</p>

<pre>
USE="-perl -python" emerge vim
</pre>

<p>
Jetzt formatieren wir die restlichen Festplatten mit
<code>cryptsetup-luks</code> und ext3, setzen den reservierten Speicher auf 0
(nicht notwendig bei Datenfestplatten), erstellen die Mountpoints und mounten
die Festplatten:
</p>
<pre>
cryptsetup -c blowfish-cbc-essiv:sha256 -y -s 256 luksFormat /dev/sda
cryptsetup -c blowfish-cbc-essiv:sha256 -y -s 256 luksFormat /dev/hda2
cryptsetup -c blowfish-cbc-essiv:sha256 -y -s 256 luksFormat /dev/hdb2
cryptsetup -c blowfish-cbc-essiv:sha256 -y -s 256 luksFormat /dev/hdc

cryptsetup luksOpen /dev/sda backup
cryptsetup luksOpen /dev/hda2 movies
cryptsetup luksOpen /dev/hdb2 wii
cryptsetup luksOpen /dev/hdc mp3

mkfs.ext2 -j /dev/mapper/backup
mkfs.ext2 -j /dev/mapper/movies
mkfs.ext2 -j /dev/mapper/wii
mkfs.ext2 -j /dev/mapper/mp3

tune2fs -m 0 /dev/mapper/backup
tune2fs -m 0 /dev/mapper/movies
tune2fs -m 0 /dev/mapper/wii
tune2fs -m 0 /dev/mapper/mp3

mkdir /fs/Backup
mkdir /fs/Movies
mkdir /fs/Wii
mkdir /fs/MP3

mount /dev/mapper/backup /fs/Backup
mount /dev/mapper/movies /fs/Movies
mount /dev/mapper/wii /fs/Wii
mount /dev/mapper/mp3 /fs/MP3
</pre>

<p>
Außerdem richten wir das RAID-1 für die Homedirs ein:
</p>
<pre>
emerge mdadm
mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/hda1 /dev/hdb1
</pre>

<p>
Und formatieren/mounten dieses ebenfalls:
</p>
<pre>
cryptsetup -c blowfish-cbc-essiv:sha256 -y -s 256 luksFormat /dev/md0
cryptsetup luksOpen /dev/md0 home
mkfs.ext2 -j /dev/mapper/home
mount /dev/mapper/home /home
</pre>

<a name="automount"><h2>Script zum automatischen Mounten</h2></a>
<p>
Damit wir nun nicht jedesmal nach dem Boot die ganzen Festplatten manuell
mounten müssen, nutzen wir folgendes Script:
</p>
<p class="filenameHeader">/root/mountHDDs.sh</p>
<pre>
#!/bin/sh
status=1
while [ ${status} -ne 0 ]; do
	read -s pass
	echo ${pass} | cryptsetup luksOpen /dev/sda backup
	status=$?
done
# Sobald das richtige Passwort eingegeben wurde und die erste Festplatte gemountet wurde, folgen die anderen
echo ${pass} | cryptsetup luksOpen /dev/hda2 movies
echo ${pass} | cryptsetup luksOpen /dev/hdb2 wii
echo ${pass} | cryptsetup luksOpen /dev/hdc mp3

mount /dev/mapper/backup /fs/Backup
mount /dev/mapper/movies /fs/Movies
mount /dev/mapper/wii /fs/Wii
mount /dev/mapper/mp3 /fs/MP3

mdadm -As /dev/md0
echo ${pass} | cryptsetup luksOpen /dev/md0 home
mount /dev/mapper/home /home
</pre>

<p>
Ich gehe davon aus, dass für alle verschlüsselten Partitionen (außer der
Root-Partition eventuell) das selbe Passwort verwendet wurde. Wer kann sich
schon so viele ausreichend sichere Passwörter merken?
</p>

<a name="encrypted_swap"><h2>Encrypted swap</h2></a>
<p>
Bei Gentoo befindet sich die Initialisierung der Swap-Partitionen in der Datei
<code>/etc/init.d/localmount</code>. Dort ersetzen wir die Zeile
&quot;/sbin/swapon -a&quot; durch folgende Zeilen:
</p>
<pre>
cryptsetup -c blowfish -s 64 -d /dev/urandom create swap0 /dev/sdc2
mkswap /dev/mapper/swap0
swapon /dev/mapper/swap0
</pre>
<p>
Dadurch wird bei jedem Boot eine verschlüsselte Partition erstellt, auf die
dann ausgelagert wird. Der Key wird aus <code>/dev/urandom</code> genommen, ist
also zufällig. Das macht aber nichts, denn mit den Swap-Daten können wir nach
einem Reboot eh nichts anfangen – vielleicht aber jemand anders, um
nachzuweisen, dass bestimmte Dinge im RAM waren, also gehen wir lieber sicher,
dass wirklich niemand etwas mehr damit anstellen kann :-).
</p>

<a name="anwendungen"><h2>Abschluss: Serveranwendungen installieren</h2></a>
<p>
So, der Server läuft. Jetzt erstmal zurücklehnen, Getränk und Konsumgut seiner
Wahl konsumieren und entspannen. Danach folgt der etwas einfachere Teil: Die
Installation von den benötigten Diensten.
</p>

<p>
Bei mir sind das <code>apache</code> (Webserver), <code>dhcpd</code>
(DHCP-Server), <code>bind</code> (DNS-Server), <code>vsftpd</code>
(FTP-Server), <code>mysql</code> (Datenbank) und <code>mt-daapd</code> (iTunes
Musikserver).
</p>

<p>
Die Übernahme der Konfigurationsdateien von Debian Linux lief weitestgehend
problemlos, alles in allem war das die schnellste Serverinstallation, die ich
je hatte ;-).
</p>

<p>
Da dieser Teil sehr individuell ist, überlasse ich die genaue Konfiguration
euch. Viel Spaß :-).
</p>

<h2>Quellen</h2>
<p>
Diese Seiten haben mir beim Installieren geholfen und sind einen Besuch wert:
</p>
<ul>
	<li>
	<a href="http://en.gentoo-wiki.com/wiki/DM-Crypt"
	title="Gentoo-Wiki:DM-Crypt">
	http://en.gentoo-wiki.com/wiki/DM-Crypt</a>
	</li>
	<li>
	<a href="http://www.gentoo.org/doc/de/handbook/handbook-x86.xml"
	target="_blank" title="Gentoo
	Handbuch">http://www.gentoo.org/doc/de/handbook/handbook-x86.xml</a>
	</li>
</ul>
