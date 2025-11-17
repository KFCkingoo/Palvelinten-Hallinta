# h4 Pkg-file-service

Tämä raportti on tehty Tero Karvisen Palvelinten Hallinta-toteutuksen myötä syksyllä 2025.

## Tiivistelmä

- Daemonien eli taustapalvelujen hallinta Saltilla.
- pkg-file-service asentaa ohjelman, korvaa tiedoston ja uudelleenkäynnistää daemonit käyttämään määriteltyjä asetuksia.

## a) SSHouto. Lisää uusi portti, jossa SSHd kuuntelee.

> Ohjeiden lukemisessa meni hieman sekaisin, joten tehtävän kulku voi olla yleistä monimutkaisempi ja sekavassa järjestyksessä. Etenkin raportin alussa, /etc/ssh/sshd_config tiedostoa ei olisi tarvittu tehdä.

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

Edettiin tehtäviä ja testattiin jos asetustiedostossa on tarpeelliset määritykset.

Tehtiin moduulikansio Salttiin /srv/salt/ssh.

Tehtiin init.sls tiedosto `sudoedit /srv/salt/ssh/init.sls` ja testattiin tilafunktio ensin käsin asentamalla openssh-server:

```
openssh-server:
  pkg.installed
```

Testattiin ssh moduuli Saltilla `sudo salt '*' state.apply ssh`:


----------
          ID: openssh-server
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 21:36:22.581191
    Duration: 82.131 ms
     Changes:   

Summary
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

Summary
------------
Succeeded: 1
Failed:    1
------------

sshd_config tiedostoa ei löytynyt Saltin kansiosta. 

Tehtiin kansioon asetustiedosto `sudoedit /srv/salt/ssh/sshd_config` ja muistettiin lisätä uudet portit 22 ja 1234.

Testattiin muutos ja idempotentti:

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

Summary 
------------
Succeeded: 2
Failed:    0
------------

Moduuli Saltilla toimi oikein, se lisäsi sshd_config asetustiedoston kun sitä ei ollut eikä tehnyt muutoksia kun se oli olemassa.

Mutta koska tehtiin uusi sshd_config ja lisättiin pelkästään Port 22 ja 1234, asetustiedostosta /etc/ssh/sshd_config poistettiin kaikki asetukset ja siinä luki vain Port 22 ja 1234.

Poistettiin openssh-server `sudo apt remove --purge openssh-server` ja asennettiin se uudestaan, jotta saatiin oletus asetustiedosto.

> Koska Tero Karvisen Pkg-File-Service ohjeessa on annettu esimerkki asetukset, edellinen toiminto ja ongelma oletus asetustiedostosta päätyi turhaksi. Sen olisi voinut suoraan kopioida Saltin asetustiedostoon ja ssh-moduulin file.managed tilafunktio olisi muokannut uudet tiedot /etc/ssh/sshd_config asetustiedostoon.

Kopioitiin Tero Karvisen esimerkki asetukset Saltin /srv/salt/ssh/sshd_config asetustiedostoon ja lisättiin halutut portit:

<img width="461" height="600" alt="srv config" src="https://github.com/user-attachments/assets/bac7cb48-b2d7-41ed-a8ec-6167a286e7dd" />

Lisättiin seurantaominaisuus ssh-moduulille, moduulin pitäisi käynnistää ssh-daemoni automaattisesti mikäli se ei ollut päällä:

```
ssh:
 service.running:
   - watch:
     - file: /etc/ssh/sshd_config
```

Testattiin moduuli:


----------
          ID: openssh-server
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 00:04:07.398123
    Duration: 77.451 ms
     Changes:   
----------
          ID: /etc/ssh/sshd_config
    Function: file.managed
      Result: True
     Comment: File /etc/ssh/sshd_config updated
     Started: 00:04:07.476598
    Duration: 17.516 ms
     Changes:   
              ----------
              diff:
                  --- 
                  +++ 
                  @@ -1,124 +1,30 @@
                  -
----------
          ID: ssh
    Function: service.running
      Result: True
     Comment: Service restarted
     Started: 00:04:07.537999
    Duration: 122.698 ms
     Changes:   
              ----------
              ssh:
                  True

Summary
------------
Succeeded: 3 (changed=2)
Failed:    0
------------


Moduuli toimi halutulla tavalla ja openssh-server oli jo asennettu valmiiksi aikaisempien testauksien takia ennen sshd_config tiedoston lopullista muutosta, daemoni käynnistyi myös päälle. 

Testattiin idempotentti:


----------
          ID: openssh-server
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 00:12:42.981196
    Duration: 74.576 ms
     Changes:   
----------
          ID: /etc/ssh/sshd_config
    Function: file.managed
      Result: True
     Comment: File /etc/ssh/sshd_config is in the correct state
     Started: 00:12:43.056851
    Duration: 10.661 ms
     Changes:   
----------
          ID: ssh
    Function: service.running
      Result: True
     Comment: The service ssh is already running
     Started: 00:12:43.068262
    Duration: 46.652 ms
     Changes:   

Summary
------------
Succeeded: 3
Failed:    0
------------

Ei tehnyt muutoksia, moduuli on idempotentti ja ssh-daemoni pysyi päällä eikä tehnyt muutoksia asetustiedostoon, koska siihen ei tehty muutoksia.

Testattiin vielä, että moduuli osasi lisätä sshd_config tiedoston /etc/ssh/ kansioon jos se poistettiin:


----------
          ID: openssh-server
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 00:36:10.714244
    Duration: 77.363 ms
     Changes:   
----------
          ID: /etc/ssh/sshd_config
    Function: file.managed
      Result: True
     Comment: File /etc/ssh/sshd_config updated
     Started: 00:36:10.792658
    Duration: 10.515 ms
     Changes:   
              ----------
              diff:
                  New file
              mode:
                  0644
----------
          ID: ssh
    Function: service.running
      Result: True
     Comment: Service restarted
     Started: 00:36:10.847988
    Duration: 125.914 ms
     Changes:   
              ----------
              ssh:
                  True

Summary
------------
Succeeded: 3 (changed=2)
Failed:    0
------------

Ajettuaan tilan, moduuli loi sshd_config tiedoston ja ssh-daemoni käynnistettiin uudelleen.

Yritettiin yhdistää käyttäjänä porttiin 1234 jos se nyt toimisi oikein `ssh -p 1234 foo@localhost`:

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ED25519 key sent by the remote host is
SHA256:zHwF2Evai1o7eO4QLLoU2LoOg/r/yc+J/lPmxUj7M48.
Please contact your system administrator.
Add correct host key in /home/choyc/.ssh/known_hosts to get rid of this message.
Offending ED25519 key in /home/choyc/.ssh/known_hosts:1
  remove with:
  ssh-keygen -f '/home/choyc/.ssh/known_hosts' -R '[localhost]:1234'
Host key for [localhost]:1234 has changed and you have requested strict checking.
Host key verification failed.
```

Poistettiin avain `ssh-keygen -f '/home/choyc/.ssh/known_hosts' -R '[localhost]:1234'`

Kokeiltiin kirjautua uudelleen ja tuli samat ongelmat kuin aikaisemmin raportin alussa:

```
Permission denied, please try again.
foo@localhost: Permission denied (publickey,password).
```

Tarkistettua käyttäjää, kyseistä foo käyttäjää ei olekaan luotu.

Tärkeää on kuitenkin, että yhteys toimi ja suorittaa tilan minioniin `nc -vz localhost 1234`:

```
Connection to localhost (::1) 1234 port [tcp/*] succeeded!
```

## Lähteet

Ucartz 2025. How to Install and Use netcat Command on Linux? https://www.ucartz.com/clients/knowledgebase/658/How-to-Install-and-Use-netcat-Command-on-Linux.html

Tero Karvinen 2025. Palvelinten Hallinta. Tehtävänanto h4 Pkg-file-service. https://terokarvinen.com/palvelinten-hallinta/#laksyt

Tero Karvinen 2018. Pkg-File-Service – Control Daemons with Salt – Change SSH Server Port. https://terokarvinen.com/2018/04/03/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/?fromSearch=karvinen%20salt%20ssh
