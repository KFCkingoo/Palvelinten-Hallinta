# h3 Soitto kotiin

Tämä tehtäväraportti on tehty Tero Karvisen Palvelinten Hallinta-toteutuksen myötä.

PC:

Windows 11 Home, version 25H2

AMD Ryzen 7 5700X3D

16 GB RAM

## x) Lue ja tiivistä

- Vagrantin asennus ja käyttö. Sen kautta voi luoda helposti virtuaalikoneiden ja ssh kirjautumisien automatisointia.
- Saltilla voi ohjata useampia minion-koneita master-koneella.
- Topfilen avulla voidaan suorittaa useita moduuleja. 

## a) Hello Vagrant!

Vagrant asennus Windowsiin, tarkistettu versio `vagrant --version`:

<img width="379" height="73" alt="Näyttökuva (45)" src="https://github.com/user-attachments/assets/890abbbf-438b-4360-bfbf-f1d04f96626e" />

## b) Linux Vagrant

Tehdään kansio `mkdir vagrantProjects`. 

Helpottaakseen seuraavia tehtäväntekoa, luotiin alihakemisto `mkdir linux`.

Asennettiin uusi yksittäinen Linux-virtuaalikone seuraamalla Karvisen ohjeita `vagrant init debian/bookworm64`:

````
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
````

Komento loi Vagrantfile tiedoston, asetettiin varsinainen Linux-VM pystyyn `vagrant up`:

```
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Box 'debian/bookworm64' could not be found. Attempting to find and install...
    default: Box Provider: virtualbox
    default: Box Version: >= 0
==> default: Loading metadata for box 'debian/bookworm64'
    default: URL: https://vagrantcloud.com/api/v2/vagrant/debian/bookworm64
==> default: Adding box 'debian/bookworm64' (v12.20250126.1) for provider: virtualbox (amd64)
    default: Downloading: https://vagrantcloud.com/debian/boxes/bookworm64/versions/12.20250126.1/providers/virtualbox/amd64/vagrant.box
    default:
==> default: Successfully added box 'debian/bookworm64' (v12.20250126.1) for 'virtualbox (amd64)'!
==> default: Importing base box 'debian/bookworm64'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'debian/bookworm64' version '12.20250126.1' is up to date...
==> default: Setting the name of the VM: linux_default_1762717014805_1684
Vagrant is currently configured to create VirtualBox synced folders with
the `SharedFoldersEnableSymlinksCreate` option enabled. If the Vagrant
guest is not trusted, you may want to disable this option. For more
information on this option, please refer to the VirtualBox manual:
  https://www.virtualbox.org/manual/ch04.html#sharedfolders
This option can be disabled globally with an environment variable:
  VAGRANT_DISABLE_VBOXSYMLINKCREATE=1
or on a per folder basis within the Vagrantfile:
  config.vm.synced_folder '/host/path', '/guest/path', SharedFoldersEnableSymlinksCreate: false
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default:
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default:
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
    default: The guest additions on this VM do not match the installed version of
    default: VirtualBox! In most cases this is fine, but in rare cases it can
    default: prevent things such as shared folders from working properly. If you see
    default: shared folder errors, please make sure the guest additions within the
    default: virtual machine match the version of VirtualBox you have installed on
    default: your host and reload your VM.
    default:
    default: Guest Additions Version: 6.0.0 r127566
    default: VirtualBox Version: 7.2
==> default: Mounting shared folders...
    default: C:/Users/vagrantProjects/linux => /vagrant
==> default: Machine 'default' has a post `vagrant up` message. This is a message
==> default: from the creator of the Vagrantfile, and not from Vagrant itself:
==> default:
==> default: Vanilla Debian box. See https://app.vagrantup.com/debian for help and bug reports
```

Mentiin kyseiseen Linux-VM terminaalille `vagrant ssh`.

<img width="259" height="33" alt="Näyttökuva (46)" src="https://github.com/user-attachments/assets/850d00ce-ad08-4490-a761-1d4bc70ebb02" />

Lähdettiin pois `exit` komennolla. 

Kokeiltiin poistaa VM `vagrant destroy` komennolla:

```
	default: Are you sure you want to destroy the 'default' VM? [y/N] y
==> default: Forcing shutdown of VM...
==> default: Destroying VM and associated drives...
```

Linux-virtuaalikone ja sen tiedostot poistettiin.

## c) Kaksin kaunihimpi

Koska Windowsissa ei ole nano-tekstieditoria, käytettiin Microsoft Edit-tekstieditoria tehtävässä.

Luotiin hakemisto `mkdir twohost`.

Luotiin sinne Vagrantfile ja asennettavat konfiguraatiot `edit Vagrantfile`, Karvisen ohjeessa template:

````
# -*- mode: ruby -*-
# vi: set ft=ruby :
# Copyright 2019-2021 Tero Karvinen http://TeroKarvinen.com

$tscript = <<TSCRIPT
set -o verbose
apt-get update
apt-get -y install tree
echo "Done - set up test environment - https://terokarvinen.com/search/?q=vagrant"
TSCRIPT

Vagrant.configure("2") do |config|
	config.vm.synced_folder ".", "/vagrant", disabled: true
	config.vm.synced_folder "shared/", "/home/vagrant/shared", create: true
	config.vm.provision "shell", inline: $tscript
	config.vm.box = "debian/bullseye64"

	config.vm.define "t001" do |t001|
		t001.vm.hostname = "t001"
		t001.vm.network "private_network", ip: "192.168.88.101"
	end

	config.vm.define "t002", primary: true do |t002|
		t002.vm.hostname = "t002"
		t002.vm.network "private_network", ip: "192.168.88.102"
	end
	
end
````

Asennetaan Linux-virtuaalikoneet pystyyn `vagrant up` komennolla:

```
Bringing machine 't001' up with 'virtualbox' provider...
Bringing machine 't002' up with 'virtualbox' provider...
==> t001: Box 'debian/bullseye64' could not be found. Attempting to find and install...
    t001: Box Provider: virtualbox
    t001: Box Version: >= 0
==> t001: Loading metadata for box 'debian/bullseye64'
    t001: URL: https://vagrantcloud.com/api/v2/vagrant/debian/bullseye64
==> t001: Adding box 'debian/bullseye64' (v11.20241217.1) for provider: virtualbox (amd64)
    t001: Downloading: https://vagrantcloud.com/debian/boxes/bullseye64/versions/11.20241217.1/providers/virtualbox/amd64/vagrant.box
    t001:
==> t001: Successfully added box 'debian/bullseye64' (v11.20241217.1) for 'virtualbox (amd64)'!
==> t001: Importing base box 'debian/bullseye64'...
==> t001: Matching MAC address for NAT networking...
==> t001: Checking if box 'debian/bullseye64' version '11.20241217.1' is up to date...
==> t001: Setting the name of the VM: twohost_t001_1762718878165_93494
==> t001: Clearing any previously set network interfaces...
==> t001: Preparing network interfaces based on configuration...
    t001: Adapter 1: nat
    t001: Adapter 2: hostonly
==> t001: Forwarding ports...
    t001: 22 (guest) => 2222 (host) (adapter 1)
==> t001: Booting VM...
==> t001: Waiting for machine to boot. This may take a few minutes...
    t001: SSH address: 127.0.0.1:2222
    t001: SSH username: vagrant
    t001: SSH auth method: private key
    t001:
    t001: Vagrant insecure key detected. Vagrant will automatically replace
    t001: this with a newly generated keypair for better security.
    t001:
    t001: Inserting generated public key within guest...
    t001: Removing insecure key from the guest if it's present...
    t001: Key inserted! Disconnecting and reconnecting using new SSH key...
==> t001: Machine booted and ready!
==> t001: Checking for guest additions in VM...
    t001: The guest additions on this VM do not match the installed version of
    t001: VirtualBox! In most cases this is fine, but in rare cases it can
    t001: prevent things such as shared folders from working properly. If you see
    t001: shared folder errors, please make sure the guest additions within the
    t001: virtual machine match the version of VirtualBox you have installed on
    t001: your host and reload your VM.
    t001:
    t001: Guest Additions Version: 6.0.0 r127566
    t001: VirtualBox Version: 7.2
==> t001: Setting hostname...
==> t001: Configuring and enabling network interfaces...
==> t001: Mounting shared folders...
    t001: C:/Users/vagrantProjects/twohost/shared => /home/vagrant/shared
==> t001: Running provisioner: shell...
    t001: Running: inline script
    t001: apt-get update
    t001: Get:1 https://security.debian.org/debian-security bullseye-security InRelease [27.2 kB]
    t001: Get:2 https://deb.debian.org/debian bullseye InRelease [75.1 kB]
    t001: Get:3 https://deb.debian.org/debian bullseye-updates InRelease [44.0 kB]
    t001: Get:4 https://security.debian.org/debian-security bullseye-security/main Sources [272 kB]
    t001: Get:5 https://security.debian.org/debian-security bullseye-security/main amd64 Packages [422 kB]
    t001: Get:6 https://security.debian.org/debian-security bullseye-security/main Translation-en [281 kB]
    t001: Get:7 https://deb.debian.org/debian bullseye/main Sources [8500 kB]
    t001: Get:8 https://deb.debian.org/debian bullseye/main amd64 Packages [8066 kB]
    t001: Get:9 https://deb.debian.org/debian bullseye/main Translation-en [6235 kB]
    t001: Get:10 https://deb.debian.org/debian bullseye-updates/main Sources [7908 B]
    t001: Get:11 https://deb.debian.org/debian bullseye-updates/main amd64 Packages [18.8 kB]
    t001: Get:12 https://deb.debian.org/debian bullseye-updates/main Translation-en [10.5 kB]
    t001: Fetched 24.0 MB in 3s (8751 kB/s)
    t001: Reading package lists...
    t001: apt-get -y install tree
    t001: Reading package lists...
    t001: Building dependency tree...
    t001: Reading state information...
    t001: The following NEW packages will be installed:
    t001:   tree
    t001: 0 upgraded, 1 newly installed, 0 to remove and 70 not upgraded.
    t001: Need to get 49.6 kB of archives.
    t001: After this operation, 118 kB of additional disk space will be used.
    t001: Get:1 https://deb.debian.org/debian bullseye/main amd64 tree amd64 1.8.0-1+b1 [49.6 kB]
    t001: dpkg-preconfigure: unable to re-open stdin: No such file or directory
    t001: Fetched 49.6 kB in 0s (1501 kB/s)
    t001: Selecting previously unselected package tree.
(Reading database ... 25141 files and directories currently installed.)
    t001: Preparing to unpack .../tree_1.8.0-1+b1_amd64.deb ...
    t001: Unpacking tree (1.8.0-1+b1) ...
    t001: Setting up tree (1.8.0-1+b1) ...
    t001: Processing triggers for man-db (2.9.4-2) ...
    t001: echo "Done - set up test environment - https://terokarvinen.com/search/?q=vagrant"
    t001: Done - set up test environment - https://terokarvinen.com/search/?q=vagrant
==> t002: Box 'debian/bullseye64' could not be found. Attempting to find and install...
    t002: Box Provider: virtualbox
    t002: Box Version: >= 0
==> t002: Loading metadata for box 'debian/bullseye64'
    t002: URL: https://vagrantcloud.com/api/v2/vagrant/debian/bullseye64
==> t002: Adding box 'debian/bullseye64' (v11.20241217.1) for provider: virtualbox (amd64)
==> t002: Importing base box 'debian/bullseye64'...
==> t002: Matching MAC address for NAT networking...
==> t002: Checking if box 'debian/bullseye64' version '11.20241217.1' is up to date...
==> t002: Setting the name of the VM: twohost_t002_1762718924231_67365
==> t002: Fixed port collision for 22 => 2222. Now on port 2200.
==> t002: Clearing any previously set network interfaces...
==> t002: Preparing network interfaces based on configuration...
    t002: Adapter 1: nat
    t002: Adapter 2: hostonly
==> t002: Forwarding ports...
    t002: 22 (guest) => 2200 (host) (adapter 1)
==> t002: Booting VM...
==> t002: Waiting for machine to boot. This may take a few minutes...
    t002: SSH address: 127.0.0.1:2200
    t002: SSH username: vagrant
    t002: SSH auth method: private key
    t002:
    t002: Vagrant insecure key detected. Vagrant will automatically replace
    t002: this with a newly generated keypair for better security.
    t002:
    t002: Inserting generated public key within guest...
    t002: Removing insecure key from the guest if it's present...
    t002: Key inserted! Disconnecting and reconnecting using new SSH key...
==> t002: Machine booted and ready!
==> t002: Checking for guest additions in VM...
    t002: The guest additions on this VM do not match the installed version of
    t002: VirtualBox! In most cases this is fine, but in rare cases it can
    t002: prevent things such as shared folders from working properly. If you see
    t002: shared folder errors, please make sure the guest additions within the
    t002: virtual machine match the version of VirtualBox you have installed on
    t002: your host and reload your VM.
    t002:
    t002: Guest Additions Version: 6.0.0 r127566
    t002: VirtualBox Version: 7.2
==> t002: Setting hostname...
==> t002: Configuring and enabling network interfaces...
==> t002: Mounting shared folders...
    t002: C:/Users/vagrantProjects/twohost/shared => /home/vagrant/shared
==> t002: Running provisioner: shell...
    t002: Running: inline script
    t002: apt-get update
    t002: Get:1 https://deb.debian.org/debian bullseye InRelease [75.1 kB]
    t002: Get:2 https://security.debian.org/debian-security bullseye-security InRelease [27.2 kB]
    t002: Get:3 https://deb.debian.org/debian bullseye-updates InRelease [44.0 kB]
    t002: Get:4 https://security.debian.org/debian-security bullseye-security/main Sources [272 kB]
    t002: Get:5 https://security.debian.org/debian-security bullseye-security/main amd64 Packages [422 kB]
    t002: Get:6 https://security.debian.org/debian-security bullseye-security/main Translation-en [281 kB]
    t002: Get:7 https://deb.debian.org/debian bullseye/main Sources [8500 kB]
    t002: Get:8 https://deb.debian.org/debian bullseye/main amd64 Packages [8066 kB]
    t002: Get:9 https://deb.debian.org/debian bullseye/main Translation-en [6235 kB]
    t002: Get:10 https://deb.debian.org/debian bullseye-updates/main Sources [7908 B]
    t002: Get:11 https://deb.debian.org/debian bullseye-updates/main amd64 Packages [18.8 kB]
    t002: Get:12 https://deb.debian.org/debian bullseye-updates/main Translation-en [10.5 kB]
    t002: Fetched 24.0 MB in 3s (8758 kB/s)
    t002: Reading package lists...
    t002: apt-get -y install tree
    t002: Reading package lists...
    t002: Building dependency tree...
    t002: Reading state information...
    t002: The following NEW packages will be installed:
    t002:   tree
    t002: 0 upgraded, 1 newly installed, 0 to remove and 70 not upgraded.
    t002: Need to get 49.6 kB of archives.
    t002: After this operation, 118 kB of additional disk space will be used.
    t002: Get:1 https://deb.debian.org/debian bullseye/main amd64 tree amd64 1.8.0-1+b1 [49.6 kB]
    t002: dpkg-preconfigure: unable to re-open stdin: No such file or directory
    t002: Fetched 49.6 kB in 0s (1575 kB/s)
    t002: Selecting previously unselected package tree.
(Reading database ... 25141 files and directories currently installed.)
    t002: Preparing to unpack .../tree_1.8.0-1+b1_amd64.deb ...
    t002: Unpacking tree (1.8.0-1+b1) ...
    t002: Setting up tree (1.8.0-1+b1) ...
    t002: Processing triggers for man-db (2.9.4-2) ...
    t002: echo "Done - set up test environment - https://terokarvinen.com/search/?q=vagrant"
    t002: Done - set up test environment - https://terokarvinen.com/search/?q=vagrant

==> t001: Machine 't001' has a post `vagrant up` message. This is a message
==> t001: from the creator of the Vagrantfile, and not from Vagrant itself:
==> t001:
==> t001: Vanilla Debian box. See https://app.vagrantup.com/debian for help and bug reports

==> t002: Machine 't002' has a post `vagrant up` message. This is a message
==> t002: from the creator of the Vagrantfile, and not from Vagrant itself:
==> t002:
==> t002: Vanilla Debian box. See https://app.vagrantup.com/debian for help and bug reports
```

2 virtuaalikonetta asennettu pystyyn.

Testattiin, että koneet pystyvät pingata toisiaan t001 koneella ensin `vagrant ssh t001`:

<img width="880" height="218" alt="Näyttökuva (47)" src="https://github.com/user-attachments/assets/c25b6e66-7e32-40c1-84df-d1496770ef11" />

Päästiin t001 terminaalille.

Testattiin ping komento toiselle koneelle t002 `ping -c 1 192.168.88.102`:

```
PING 192.168.88.102 (192.168.88.102) 56(84) bytes of data.
64 bytes from 192.168.88.102: icmp_seq=1 ttl=64 time=0.377 ms

--- 192.168.88.102 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.377/0.377/0.377/0.000 ms
```

Ping komento t002 koneelle onnistui.

Poistuttiin t001 koneelta `exit`.

Testattiin t002 terminaaliin `vagrant ssh t002`:

<img width="904" height="220" alt="Näyttökuva (48)" src="https://github.com/user-attachments/assets/b212e002-318f-459e-b215-4c32b2774d7f" />

Päästiin t002 terminaaliin.

Testattiin ping komento t001 koneelle `ping -c 1 192.168.88.101`:

```
PING 192.168.88.101 (192.168.88.101) 56(84) bytes of data.
64 bytes from 192.168.88.101: icmp_seq=1 ttl=64 time=0.182 ms

--- 192.168.88.101 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.182/0.182/0.182/0.000 ms
```

Ping komento t001 koneelle onnistui.

Poistuttiin t002 koneelta `exit`.

Koneet voivat pingata toisiaan.

Poistettiin koneet `vagrant destroy`:

```
	t002: Are you sure you want to destroy the 't002' VM? [y/N] y
==> t002: Forcing shutdown of VM...
==> t002: Destroying VM and associated drives...
    t001: Are you sure you want to destroy the 't001' VM? [y/N] y
==> t001: Forcing shutdown of VM...
==> t001: Destroying VM and associated drives...
```

## d) Herra-orja verkossa

Käytettiin edellisen tehtävän hakemistoa twohost.

Luotiin hakemistoon Vagrantfile `edit Vagrantfile` komennolla, sinne lisättiin Karvisen template:

```
# -*- mode: ruby -*-
# vi: set ft=ruby :
# Copyright 2019-2021 Tero Karvinen http://TeroKarvinen.com

$tscript = <<TSCRIPT
set -o verbose
apt-get update
apt-get -y install tree
echo "Done - set up test environment - https://terokarvinen.com/search/?q=vagrant"
TSCRIPT

Vagrant.configure("2") do |config|
	config.vm.synced_folder ".", "/vagrant", disabled: true
	config.vm.synced_folder "shared/", "/home/vagrant/shared", create: true
	config.vm.provision "shell", inline: $tscript
	config.vm.box = "debian/bullseye64"

	config.vm.define "t001" do |t001|
		t001.vm.hostname = "t001"
		t001.vm.network "private_network", ip: "192.168.88.101"
	end

	config.vm.define "t002", primary: true do |t002|
		t002.vm.hostname = "t002"
		t002.vm.network "private_network", ip: "192.168.88.102"
	end
	
end
```

Asennettiin Salt koneille seuraavilla prosesseilla.

Luotiin Saltille repo `mkdir saltrepo`.

Mentiin repoon `cd saltrepo/`.

Ladattiin 2 tiedostoa:
```
wget https://packages.broadcom.com/artifactory/api/security/keypair/SaltProjectKey/public
wget https://github.com/saltstack/salt-install-guide/releases/latest/download/salt.sources
```
Komento `gpg --show-key --with-fingerprint public` ei toiminut, virheenä:

>-bash: gpg: command not found

Ohitettiin virhe ja jatkettiin saltin asennusta.

Lisättiin avain `sudo cp public /etc/apt/keyrings/salt-archive-keyring.pgp`, tuli virhe:

>cp: cannot create regular file '/etc/apt/keyrings/salt-archive-keyring.pgp': No such file or directory

Luotiin hakemisto `sudo mkdir /etc/apt/keyrings/` ja uudelleenyritys onnistui.
Jatkettiin salt avaimien lisäystä `sudo cp salt.sources /etc/apt/sources.list.d/`.

Asennettiin t001 master Salt `sudo apt-get install salt-master`.

Poistuttiin `exit`.

Tehtiin t002 minion Salt samalla prosessilla.

Tuli samoja virheitä ja edettiin samalla prosessilla.

Asennettiin t002 minion salt `sudo apt-get install salt-minion salt-master`.

Tehtiin t002-koneelle avain `sudoedit /etc/salt/minion`, sinne lisättiin master ip ja id:

```
master: 192.168.88.101
id: choy
```

Käynnistettiin minion uudelleen jotta muutokset tehtiin `sudoedit systemctl restart salt-minion.service`.

Poistuttiin `exit`.

Kirjauduttiin t001 master-koneelle `vagrant ssh t001`.

Hyväksyttiin minion-avain `sudo salt-key -A`:

```
The following keys are going to be accepted:
Unaccepted Keys:
choy
Proceed? [n/Y] y
Key for minion choy accepted.
```

Testattiin, että master voi komentaa minionia `sudo salt '*' cmd.run 'whoami`:

```
choy:
    root
```

t001 master-kone komensi minionia.

## e) Kokeiluja verkon yli

Luotiin kansiot moduuleja varten:

```
sudo mkdir -p /srv/salt/hellouser
sudo mkdir -p /srv/salt/hellofile
sudo mkdir -p /srv/salt/hellopkg
```

Annettiin tilafunktiot moduuleille esim. hellouser `sudoedit /srv/salt/hellouser/init.sls`:

```
choykkely:
  user.present
```

```
/tmp/testii:
  file.managed
```

```
tree:
  pkg.installed
```

Tehtiin top file, jotta voidaan suorittaa kaikki tilat kerralla `sudoedit /srv/salt/top.sls`:

```
base:
  '*':
    hellouser
	hellofile
	hellopkg
```

Testattiin tilafunktiot ja tuli virhe:

```
choy:
    Data failed to compile:
----------
    Malformed topfile (state declarations not formed as a list)
----------
    No matching sls found for 'hellouser hellofile hellopkg' in env 'base'
ERROR: Minions returned with non-zero exit code
```

Puuttui ranskalaiset viivat listalta, korjattiin se `sudoedit /srv/salt/top.sls`:

```
base:
  '*':
    - hellouser
	- hellofile
	- hellopkg
```

Uudelleentestaus:

choy:
----------
          ID: choykkely
    Function: user.present
      Result: True
     Comment: New user choykkely created
     Started: 00:58:13.129697
    Duration: 46.925 ms
     Changes:
              ----------
              fullname:
              gid:
                  1001
              groups:
                  - choykkely
              home:
                  /home/choykkely
              homephone:
              name:
                  choykkely
              other:
              passwd:
                  x
              roomnumber:
              shell:
                  /bin/sh
              uid:
                  1001
              workphone:
----------
          ID: /tmp/testii
    Function: file.managed
      Result: True
     Comment: Empty file
     Started: 00:58:13.177879
    Duration: 6.166 ms
     Changes:
              ----------
              new:
                  file /tmp/testii created
----------
          ID: tree
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 00:58:13.889105
    Duration: 28.857 ms
     Changes:

Summary for choy
------------
Succeeded: 3 (changed=2)
Failed:    0
------------

Tilafunktiot toimi ilman virheitä.

Testattiin idempotentti suorittamalla tilafunktiot uudelleen:

choy:
----------
          ID: choykkely
    Function: user.present
      Result: True
     Comment: User choykkely is present and up to date
     Started: 00:59:31.648399
    Duration: 34.061 ms
     Changes:
----------
          ID: /tmp/testii
    Function: file.managed
      Result: True
     Comment: File /tmp/testii exists with proper permissions. No changes made.
     Started: 00:59:31.684275
    Duration: 1.622 ms
     Changes:
----------
          ID: tree
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 00:59:32.395924
    Duration: 26.037 ms
     Changes:

Summary for choy
------------
Succeeded: 3
Failed:    0
------------

Tilafunktiot suorittivat toiminnot oikein ja muutoksia ei tehty idempotentin testauksessa.

## Lähteet

Karvinen, T. 2025. https://terokarvinen.com/palvelinten-hallinta/#laksyt

Karvinen, T. 2023. https://terokarvinen.com/2023/salt-vagrant/#infra-as-code---your-wishes-as-a-text-file

Karvinen, T. 2021. https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/

