# h4
## Tiivistelmä

- SSH 

## a) SSHouto. Lisää uusi portti, jossa SSHd kuuntelee.

Asennettiin ssh `sudo apt -y install ssh`.

Tehtiin sshd_config tiedosto `sudoedit /etc/ssh/sshd_config`

Avattiin Port 22 ja lisättiin Port 1234 sshd_config asetustiedostoon:

<img width="550" height="399" alt="port 22 1234" src="https://github.com/user-attachments/assets/c7580514-1cfa-4105-a237-93847a672d41" />


Asennettiin netcat, jotta voitiin testata portin kuuntelemista `sudo apt -y install netcat-openbsd`

Testattiin, että portti kuuntelee `nc -vz localhost 1234`:

```
Connection to localhost (::1) 1234 port [tcp/*] succeeded!
```

Portti kykeni kuuntelemaan.

Kokeiltiin kirjautua porttiin `ssh -p 1234 foo@localhost`, mutta pääsy oli kielletty:

```
Permission denied, please try again.
```

Kokeiltiin kirjautua uudelleen superuser oikeuksilla sudo:

```
The authenticity of host '[localhost]:1234 ([::1]:1234)' can't be established.
ED25519 key fingerprint is SHA256:4gqSLowiYH3WbWLRYLAzQlw9YMxKUbtDWzn4jEqr7CE.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[localhost]:1234' (ED25519) to the list of known hosts.

```

Pääsy oli edelleen kielletty.

Tehtiin moduulikansio Salttiin varten /srv/salt/ssh.

Tehtiin init.sls tiedosto `sudoedit /srv/salt/ssh/init.sls` ja testattiin tilafunktio ensin käsin:

```
openssh-server:
  pkg.installed
```

Testattiin ssh moduuli Saltilla `sudo salt '*' state.apply ssh`:

choy:
----------
          ID: openssh-server
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 21:36:22.581191
    Duration: 82.131 ms
     Changes:   

Summary for choy
------------
Succeeded: 1
Failed:    0
------------

Moduuli toimi, lisätään nyt lisää tilafunktioita ohjeita seuraten:

```
/etc/ssh/sshd_config:
  file.managed:
    - source: salt://sshd_config
```

Testattiin moduuli:

choy:
----------
          ID: openssh-server
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 21:56:20.902671
    Duration: 76.103 ms
     Changes:   
----------
          ID: /etc/ssh/sshd_config
    Function: file.managed
      Result: False
     Comment: Source file salt://sshd_config not found in saltenv 'base'
     Started: 21:56:20.979782
    Duration: 4.82 ms
     Changes:   

Summary for choy
------------
Succeeded: 1
Failed:    1
------------

sshd_config tiedostoa ei löytynyt Saltin kansiosta. 

Tehtiin kansioon asetustiedosto `sudoedit /srv/salt/ssh/sshd_config` ja muistettiin lisätä uudet portit 22 ja 1234.

Testattiin muutos ja idempotentti:

Summary for choy
------------
Succeeded: 2 (changed=1)
Failed:    0
------------


choy:
----------
          ID: openssh-server
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 23:22:33.651480
    Duration: 77.459 ms
     Changes:   
----------
          ID: /etc/ssh/sshd_config
    Function: file.managed
      Result: True
     Comment: File /etc/ssh/sshd_config is in the correct state
     Started: 23:22:33.729937
    Duration: 10.004 ms
     Changes:   

Summary for choy
------------
Succeeded: 2
Failed:    0
------------

Moduuli Saltilla toimi oikein, se lisäsi sshd_config asetustiedoston kun sitä ei ollut eikä tehnyt muutoksia kun oli olemassa.

Lisättiin seurantaominaisuus ssh moduulille, moduulin pitäisi käynnistää ssh automaattisesti mikäli se ei ollut päällä:

```
ssh:
 service.running:
   - watch:
     - file: /etc/ssh/sshd_config
```

## Lähteet
