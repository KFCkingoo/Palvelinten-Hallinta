# h3 Soitto kotiin

Tämä tehtäväraportti on tehty Tero Karvisen Palvelinten Hallinta-toteutuksen myötä.

PC:
Windows 11 Home, version 25H2

AMD Ryzen 7 5700X3D

16 GB RAM

## x) Lue ja tiivistä

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

Mentiin kyseiseen Linux-VM terminaalille `vagrant ssh`:

````
Linux bookworm 6.1.0-29-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.123-1 (2025-01-02) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
````

Päästiin terminaalille:

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

Luotiin Vagrantfile ja asennettavat konfiguraatiot `edit Vagrantfile`:

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

Luotiin hakemisto `mkdir saltdemo`.

Luotiin hakemistoon Vagrantfile `edit Vagrantfile` komennolla. 


Mennään 3



Karvisen ohjeessa on valmis template 3 koneelle, mutta tehtävä vaati 2 konetta joten poistettiin templatesta t002 kone:

```
# -*- mode: ruby -*-
# vi: set ft=ruby :
# Copyright 2014-2023 Tero Karvinen http://TeroKarvinen.com

$minion = <<MINION
sudo apt-get update
sudo apt-get -qy install salt-minion
echo "master: 192.168.12.3">/etc/salt/minion
sudo service salt-minion restart
echo "See also: https://terokarvinen.com/2023/salt-vagrant/"
MINION

$master = <<MASTER
sudo apt-get update
sudo apt-get -qy install salt-master
echo "See also: https://terokarvinen.com/2023/salt-vagrant/"
MASTER

Vagrant.configure("2") do |config|
	config.vm.box = "debian/bullseye64"

	config.vm.define "t001" do |t001|
		t001.vm.provision :shell, inline: $minion
		t001.vm.network "private_network", ip: "192.168.12.100"
		t001.vm.hostname = "t001"
	end

	config.vm.define "tmaster", primary: true do |tmaster|
		tmaster.vm.provision :shell, inline: $master
		tmaster.vm.network "private_network", ip: "192.168.12.3"
		tmaster.vm.hostname = "tmaster"
	end
end
```

Käynnistettiin koneet `vagrant up`:

```
Bringing machine 't001' up with 'virtualbox' provider...
Bringing machine 'tmaster' up with 'virtualbox' provider...
==> t001: Importing base box 'debian/bullseye64'...
==> t001: Matching MAC address for NAT networking...
==> t001: Checking if box 'debian/bullseye64' version '11.20241217.1' is up to date...
==> t001: Setting the name of the VM: saltdemo_t001_1762721532454_6961
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
    t001: C:/Users/vagrantProjects/saltdemo => /vagrant
==> t001: Running provisioner: shell...
    t001: Running: inline script
    t001: Get:1 https://deb.debian.org/debian bullseye InRelease [75.1 kB]
    t001: Get:2 https://security.debian.org/debian-security bullseye-security InRelease [27.2 kB]
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
    t001: Fetched 24.0 MB in 3s (8587 kB/s)
    t001: Reading package lists...
    t001: Reading package lists...
    t001: Building dependency tree...
    t001: Reading state information...
    t001: E: Unable to locate package salt-minion
    t001: /tmp/vagrant-shell: line 3: /etc/salt/minion: No such file or directory
    t001: Failed to restart salt-minion.service: Unit salt-minion.service not found.
    t001: See also: https://terokarvinen.com/2023/salt-vagrant/
==> tmaster: Importing base box 'debian/bullseye64'...
==> tmaster: Matching MAC address for NAT networking...
==> tmaster: Checking if box 'debian/bullseye64' version '11.20241217.1' is up to date...
==> tmaster: Setting the name of the VM: saltdemo_tmaster_1762721588834_2067
==> tmaster: Fixed port collision for 22 => 2222. Now on port 2200.
==> tmaster: Clearing any previously set network interfaces...
==> tmaster: Preparing network interfaces based on configuration...
    tmaster: Adapter 1: nat
    tmaster: Adapter 2: hostonly
==> tmaster: Forwarding ports...
    tmaster: 22 (guest) => 2200 (host) (adapter 1)
==> tmaster: Booting VM...
==> tmaster: Waiting for machine to boot. This may take a few minutes...
    tmaster: SSH address: 127.0.0.1:2200
    tmaster: SSH username: vagrant
    tmaster: SSH auth method: private key
    tmaster:
    tmaster: Vagrant insecure key detected. Vagrant will automatically replace
    tmaster: this with a newly generated keypair for better security.
    tmaster:
    tmaster: Inserting generated public key within guest...
    tmaster: Removing insecure key from the guest if it's present...
    tmaster: Key inserted! Disconnecting and reconnecting using new SSH key...
==> tmaster: Machine booted and ready!
==> tmaster: Checking for guest additions in VM...
    tmaster: The guest additions on this VM do not match the installed version of
    tmaster: VirtualBox! In most cases this is fine, but in rare cases it can
    tmaster: prevent things such as shared folders from working properly. If you see
    tmaster: shared folder errors, please make sure the guest additions within the
    tmaster: virtual machine match the version of VirtualBox you have installed on
    tmaster: your host and reload your VM.
    tmaster:
    tmaster: Guest Additions Version: 6.0.0 r127566
    tmaster: VirtualBox Version: 7.2
==> tmaster: Setting hostname...
==> tmaster: Configuring and enabling network interfaces...
==> tmaster: Mounting shared folders...
    tmaster: C:/Users/vagrantProjects/saltdemo => /vagrant
==> tmaster: Running provisioner: shell...
    tmaster: Running: inline script
    tmaster: Get:1 https://deb.debian.org/debian bullseye InRelease [75.1 kB]
    tmaster: Get:2 https://security.debian.org/debian-security bullseye-security InRelease [27.2 kB]
    tmaster: Get:3 https://deb.debian.org/debian bullseye-updates InRelease [44.0 kB]
    tmaster: Get:4 https://security.debian.org/debian-security bullseye-security/main Sources [272 kB]
    tmaster: Get:5 https://security.debian.org/debian-security bullseye-security/main amd64 Packages [422 kB]
    tmaster: Get:6 https://security.debian.org/debian-security bullseye-security/main Translation-en [281 kB]
    tmaster: Get:7 https://deb.debian.org/debian bullseye/main Sources [8500 kB]
    tmaster: Get:8 https://deb.debian.org/debian bullseye/main amd64 Packages [8066 kB]
    tmaster: Get:9 https://deb.debian.org/debian bullseye/main Translation-en [6235 kB]
    tmaster: Get:10 https://deb.debian.org/debian bullseye-updates/main Sources [7908 B]
    tmaster: Get:11 https://deb.debian.org/debian bullseye-updates/main amd64 Packages [18.8 kB]
    tmaster: Get:12 https://deb.debian.org/debian bullseye-updates/main Translation-en [10.5 kB]
    tmaster: Fetched 24.0 MB in 3s (9224 kB/s)
    tmaster: Reading package lists...
    tmaster: Reading package lists...
    tmaster: Building dependency tree...
    tmaster: Reading state information...
    tmaster: E: Unable to locate package salt-master
    tmaster: See also: https://terokarvinen.com/2023/salt-vagrant/

==> t001: Machine 't001' has a post `vagrant up` message. This is a message
==> t001: from the creator of the Vagrantfile, and not from Vagrant itself:
==> t001:
==> t001: Vanilla Debian box. See https://app.vagrantup.com/debian for help and bug reports

==> tmaster: Machine 'tmaster' has a post `vagrant up` message. This is a message
==> tmaster: from the creator of the Vagrantfile, and not from Vagrant itself:
==> tmaster:
==> tmaster: Vanilla Debian box. See https://app.vagrantup.com/debian for help and bug reports
```

Tuloksesta näkyi, että salt ei ole asennettu.

Luotiin hakemisto `sudo mkdir saltrepo/`.

Asennettiin repot:

```
vagrant@tmaster:~/saltrepo$ wget https://packages.broadcom.com/artifactory/api/security/keypair/SaltProjectKey/public
--2025-11-09 21:42:59--  https://packages.broadcom.com/artifactory/api/security/keypair/SaltProjectKey/public
Resolving packages.broadcom.com (packages.broadcom.com)... 54.185.186.5, 44.226.59.123, 100.21.156.46
Connecting to packages.broadcom.com (packages.broadcom.com)|54.185.186.5|:443... connected.
HTTP request sent, awaiting response... 200
Length: unspecified [application/json]
Saving to: ‘public’

public                            [ <=>                                              ]   2.42K  --.-KB/s    in 0s

2025-11-09 21:43:00 (75.2 MB/s) - ‘public’ saved [2475]

vagrant@tmaster:~/saltrepo$ wget https://github.com/saltstack/salt-install-guide/releases/latest/download/salt.sources
--2025-11-09 21:43:10--  https://github.com/saltstack/salt-install-guide/releases/latest/download/salt.sources
Resolving github.com (github.com)... 140.82.121.3
Connecting to github.com (github.com)|140.82.121.3|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://github.com/saltstack/salt-install-guide/releases/download/v1.16.0/salt.sources [following]
--2025-11-09 21:43:10--  https://github.com/saltstack/salt-install-guide/releases/download/v1.16.0/salt.sources
Reusing existing connection to github.com:443.
HTTP request sent, awaiting response... 302 Found
Location: https://release-assets.githubusercontent.com/github-production-release-asset/876954320/d01c8ad2-de05-4b28-86ed-73b732ea1d7f?sp=r&sv=2018-11-09&sr=b&spr=https&se=2025-11-09T22%3A36%3A56Z&rscd=attachment%3B+filename%3Dsalt.sources&rsct=application%2Foctet-stream&skoid=96c2d410-5711-43a1-aedd-ab1947aa7ab0&sktid=398a6654-997b-47e9-b12b-9515b896b4de&skt=2025-11-09T21%3A36%3A08Z&ske=2025-11-09T22%3A36%3A56Z&sks=b&skv=2018-11-09&sig=7myqzSxv8CqBKQ5Dl2ZYBBQsJ5CYMhrk9U7ugYYZVqU%3D&jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmVsZWFzZS1hc3NldHMuZ2l0aHVidXNlcmNvbnRlbnQuY29tIiwia2V5Ijoia2V5MSIsImV4cCI6MTc2MjcyNDg5MCwibmJmIjoxNzYyNzI0NTkwLCJwYXRoIjoicmVsZWFzZWFzc2V0cHJvZHVjdGlvbi5ibG9iLmNvcmUud2luZG93cy5uZXQifQ.yaZ9lj83CFS_NhDaUdPhowI8Y-lRqHDLshInUgh8BC4&response-content-disposition=attachment%3B%20filename%3Dsalt.sources&response-content-type=application%2Foctet-stream [following]
--2025-11-09 21:43:10--  https://release-assets.githubusercontent.com/github-production-release-asset/876954320/d01c8ad2-de05-4b28-86ed-73b732ea1d7f?sp=r&sv=2018-11-09&sr=b&spr=https&se=2025-11-09T22%3A36%3A56Z&rscd=attachment%3B+filename%3Dsalt.sources&rsct=application%2Foctet-stream&skoid=96c2d410-5711-43a1-aedd-ab1947aa7ab0&sktid=398a6654-997b-47e9-b12b-9515b896b4de&skt=2025-11-09T21%3A36%3A08Z&ske=2025-11-09T22%3A36%3A56Z&sks=b&skv=2018-11-09&sig=7myqzSxv8CqBKQ5Dl2ZYBBQsJ5CYMhrk9U7ugYYZVqU%3D&jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmVsZWFzZS1hc3NldHMuZ2l0aHVidXNlcmNvbnRlbnQuY29tIiwia2V5Ijoia2V5MSIsImV4cCI6MTc2MjcyNDg5MCwibmJmIjoxNzYyNzI0NTkwLCJwYXRoIjoicmVsZWFzZWFzc2V0cHJvZHVjdGlvbi5ibG9iLmNvcmUud2luZG93cy5uZXQifQ.yaZ9lj83CFS_NhDaUdPhowI8Y-lRqHDLshInUgh8BC4&response-content-disposition=attachment%3B%20filename%3Dsalt.sources&response-content-type=application%2Foctet-stream
Resolving release-assets.githubusercontent.com (release-assets.githubusercontent.com)... 185.199.110.133, 185.199.109.133, 185.199.108.133, ...
Connecting to release-assets.githubusercontent.com (release-assets.githubusercontent.com)|185.199.110.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 717 [application/octet-stream]
Saving to: ‘salt.sources’

salt.sources                  100%[=================================================>]     717  --.-KB/s    in 0s

2025-11-09 21:43:10 (97.5 MB/s) - ‘salt.sources’ saved [717/717]
```

Lisättiin salt avaimet:

```
sudo cp public /etc/apt/keyrings/salt-archive-keyring.pgp
sudo cp salt.sources /etc/apt/sources.list.d/
```
Puuttui keyrings/ hakemisto joten luotiin se `sudo mkdir /etc/apt/keyrings/` ja kokeiltiin uudelleen onnistuneesti.

Asennettiin salt-master `sudo apt-get install salt-master`.

Toistettiin sama prosessi t001 koneelle mutta salt-minion asennus `sudo apt-get install salt-minion`.



Mentiin tmaster terminaaliin `vagrant ssh tmaster`.

Päästiin tmaster terminaaliin.

## Lähteet

https://developer.hashicorp.com/vagrant/docs/cli/version
https://terokarvinen.com/palvelinten-hallinta/#laksyt
https://terokarvinen.com/2023/salt-vagrant/#infra-as-code---your-wishes-as-a-text-file
https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/

