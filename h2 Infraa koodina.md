# h2 Infraa koodina

Tämä raportti on luotu Tero Karvisen Palvelinten Hallinta-kurssia varten.

## x)
- 'hello' moduulin luonti ja testaus
- YAML struktuuri
- top.sls tiedoston avulla suoritetaan useita muutoksia ja moduuleja

## a)

Otettiin käyttöön micro editori ``sudo apt-get -y install micro`` + ``export EDITOR=micro``.

Tehtiin kansio 'hello' moduulille ``sudo mkdir -p /srv/salt/hello/``.

On tärkeää luoda tiedostot oikeille hakemistolle, joten suunnattiin kyseiseen hakemistoon ``cd /srv/salt/hello/``.

Luotiin sinne sls tiedosto ``sudoedit init.sls``. Sinne lisättiin:

```
/tmp/hellochoy:
  file.managed
```
  
Testattiin init.sls tiedostoa paikallisesti ``sudo salt-call --local state.apply hello``:

local:
----------
          ID: /tmp/hellochoy
    Function: file.managed
      Result: True
     Comment: File /tmp/hellochoy exists with proper permissions. No changes made.
     Started: 15:10:47.707511
    Duration: 6.448 ms
     Changes:   

Summary for local
------------
Succeeded: 1
Failed:    0
------------

Tiedosto oli olemassa, joten ei muutoksia tehty.

Testattiin, että hello moduuli toimii oikein toisella tavalla ``ls /tmp/hellochoy``:

``/tmp/hellochoy``

Moduuli toimi.

## b)

Tehtiin top.sls tiedosto ``sudoedit top.sls`` ja lisättiin:

```
base:
  '*':
    hello
```

Testattiin top.sls ``sudo salt-call --local state.apply``:

local:
----------
          ID: /tmp/hellochoy
    Function: file.managed
      Result: True
     Comment: File /tmp/hellochoy exists with proper permissions. No changes made.
     Started: 19:27:51.888376
    Duration: 4.782 ms
     Changes:   

Summary for local
------------
Succeeded: 1
Failed:    0
------------

top.sls toimi, mutta sinä hetkellä tehtäviä oli vain 1 moduuli luotu. Seuraavissa tehtävissä tuli luotua lisää tilafunktioita.

## c)

Tehtiin omat kansiot eri tilafunktioille komennolla ``sudo mkdir`` kuten ``sudo mkdir hellopkg``.

Luotiin 'hellopkg' kansioon seuraava init.sls tiedosto ``sudoedit init.sls`` ja lisättiin:

```
tree:
  pkg.installed
```

Testattu toimivuus ``sudo salt-call --local state.apply hellopkg``:

local:
----------
          ID: tree
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 19:49:40.954364
    Duration: 76.022 ms
     Changes:   

Summary for local
------------
Succeeded: 1
Failed:    0
------------

Tree oli asennettu, ei muutoksia tehty. Moduuli toimi oikein.

Luotiin 'hellofile' kansio ``sudo mkdir hellofile`` ja kansioon luotiin init.sls tiedosto ``sudoedit init.sls``. Koska oli aikaisemmin tehty jo 'hello' moduuli niin otettiin siitä mallia:

```
/tmp/hellochoy:
  file.managed
```

Testattu moduuli ``sudo salt-call --local state.apply hellofile``:

local:
----------
          ID: /tmp/hellochoy
    Function: file.managed
      Result: True
     Comment: File /tmp/hellochoy exists with proper permissions. No changes made.
     Started: 20:04:29.292490
    Duration: 5.885 ms
     Changes:   

Summary for local
------------
Succeeded: 1
Failed:    0
------------

Moduuli toimi oikein. Tiedosto oli olemassa.

Luotiin 'hellosrv' kansio ``sudo mkdir hellosrv`` ja kansioon luotiin init.sls tiedosto ``sudoedit init.sls``, tiedostoon lisättiin:

```
apache2:
  service.running
```

Testattu moduuli ``sudo salt-call --local state.apply hellosrv``:

local:
----------
          ID: apache2
    Function: service.running
      Result: True
     Comment: Started service apache2
     Started: 20:11:17.048980
    Duration: 178.567 ms
     Changes:   
              ----------
              apache2:
                  True

Summary for local
------------
Succeeded: 1 (changed=1)
Failed:    0
------------

Moduuli toimi oikein. Palvelu ei ollut käynnissä, joten se käynnistettiin.

Luotiin 'hellouser' kansio ``sudo mkdir hellouser`` ja kansioon luotiin init.sls tiedosto ``sudoedit init.sls``, tiedostoon lisättiin:

```
choykkeli:
  user.present
```
Testattu moduuli ``sudo salt-call --local state.apply hellouser``:

local:
----------
          ID: choykkeli
    Function: user.present
      Result: True
     Comment: User choykkeli is present and up to date
     Started: 20:14:59.329745
    Duration: 26.658 ms
     Changes:   

Summary for local
------------
Succeeded: 1
Failed:    0
------------

Moduuli toimi oikein. Käyttäjä oli olemassa.

Luotiin 'hellocmd' kansio ``sudo mkdir hellocmd`` ja kansioon luotiin init.sls tiedosto ``sudoedit init.sls``, tiedostoon lisättiin:

```
/tmp/hellochoy:
  cmd.run:
    - creates: /tmp/hellochoy
```

Testattu moduuli ``sudo salt-call --local state.apply hellocmd``:

local:
----------
          ID: /tmp/hellochoy
    Function: cmd.run
        Name: touch /tmp/hellochoy
      Result: True
     Comment: /tmp/hellochoy exists
     Started: 20:47:53.728099
    Duration: 989.55 ms
     Changes:   

Summary for local
------------
Succeeded: 1
Failed:    0
------------

Moduuli toimi oikein. Moduuli yritti luoda tiedoston, mutta se oli jo olemassa, joten se ohitti muutoksen.

## d)

Luotiin 'hellomulti' kansio ``sudo mkdir hellomulti`` ja kansioon luotiin init.sls tiedosto ``sudoedit init.sls``, tiedostoon lisättiin:

```
base:
  pkg.installed:
    - name: tree
  file.managed:
    - name: /tmp/hellochoy
```

local:
----------
          ID: base
    Function: pkg.installed
        Name: tree
      Result: True
     Comment: All specified packages are already installed
     Started: 21:20:11.002869
    Duration: 76.28 ms
     Changes:   
----------
          ID: base
    Function: file.managed
        Name: /tmp/hellochoy
      Result: True
     Comment: File /tmp/hellochoy exists with proper permissions. No changes made.
     Started: 21:20:11.080164
    Duration: 1.236 ms
     Changes:   

Summary for local
------------
Succeeded: 2
Failed:    0
------------

Moduuli toimi oikein, eikä tehnyt mitään muutoksia. Testattiin 'hellopkg' moduulilla, että ne ovat idempotentteja ``sudo salt-call --local state.apply hellopkg``:

local:
----------
          ID: tree
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 21:26:07.589898
    Duration: 76.376 ms
     Changes:   

Summary for local
------------
Succeeded: 1
Failed:    0
------------

Moduuli ei tehnyt muutoksia. Seuraavaksi testattiin 'hellofile' moduulilla ``sudo salt-call --local state.apply hellofile``:

local:
----------
          ID: /tmp/hellochoy
    Function: file.managed
      Result: True
     Comment: File /tmp/hellochoy exists with proper permissions. No changes made.
     Started: 21:29:42.216106
    Duration: 5.859 ms
     Changes:   

Summary for local
------------
Succeeded: 1
Failed:    0
------------

Moduuli ei tehnyt muutoksia. Testattiin 'hellomulti' moduulia uudelleen ``sudo salt-call --local state.apply hellomulti``:

local:
----------
          ID: base
    Function: pkg.installed
        Name: tree
      Result: True
     Comment: All specified packages are already installed
     Started: 21:30:58.290034
    Duration: 74.741 ms
     Changes:   
----------
          ID: base
    Function: file.managed
        Name: /tmp/hellochoy
      Result: True
     Comment: File /tmp/hellochoy exists with proper permissions. No changes made.
     Started: 21:30:58.365729
    Duration: 1.213 ms
     Changes:   

Summary for local
------------
Succeeded: 2
Failed:    0
------------

'hellomulti' moduuli toimi oikein eikä tehnyt muutoksia, testattu useamman kerran ennen muiden moduulien testausta ja jälkeen.

Meillä oli tehty aikaisemmin myös top.sls tiedosto, joten hyödynnettiin tiedosta käyttämällä sitä. Mentiin oikeaan hakemistoon ``cd /srv/salt/`` ja lisättiin tilafunktiot top.sls tiedostoon:

```
base:
  '*':
    -hellopkg
    -hellofile
    -hellosrv
    -hellouser
```

Testattu top.sls tiedosto ``sudo salt-call --local state.apply``:

local:
----------
          ID: tree
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 21:35:01.115025
    Duration: 75.94 ms
     Changes:   
----------
          ID: /tmp/hellochoy
    Function: file.managed
      Result: True
     Comment: File /tmp/hellochoy exists with proper permissions. No changes made.
     Started: 21:35:01.191964
    Duration: 1.204 ms
     Changes:   
----------
          ID: apache2
    Function: service.running
      Result: True
     Comment: The service apache2 is already running
     Started: 21:35:01.193667
    Duration: 45.275 ms
     Changes:   
----------
          ID: choykkeli
    Function: user.present
      Result: True
     Comment: User choykkeli is present and up to date
     Started: 21:35:01.239526
    Duration: 1.277 ms
     Changes:   

Summary for local
------------
Succeeded: 4
Failed:    0
------------

Muutoksia ei tehty. Lisättiin 'hellomulti' moduuli top.sls tiedostoon ja testattiin uudelleen ``sudo salt-call --local state.apply``:

local:
----------
          ID: tree
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 21:37:50.550652
    Duration: 75.985 ms
     Changes:   
----------
          ID: /tmp/hellochoy
    Function: file.managed
      Result: True
     Comment: File /tmp/hellochoy exists with proper permissions. No changes made.
     Started: 21:37:50.627636
    Duration: 1.238 ms
     Changes:   
----------
          ID: apache2
    Function: service.running
      Result: True
     Comment: The service apache2 is already running
     Started: 21:37:50.629368
    Duration: 44.442 ms
     Changes:   
----------
          ID: choykkeli
    Function: user.present
      Result: True
     Comment: User choykkeli is present and up to date
     Started: 21:37:50.674523
    Duration: 1.334 ms
     Changes:   
----------
          ID: base
    Function: pkg.installed
        Name: tree
      Result: True
     Comment: All specified packages are already installed
     Started: 21:37:50.675919
    Duration: 10.344 ms
     Changes:   
----------
          ID: base
    Function: file.managed
        Name: /tmp/hellochoy
      Result: True
     Comment: File /tmp/hellochoy exists with proper permissions. No changes made.
     Started: 21:37:50.686324
    Duration: 1.247 ms
     Changes:   

Summary for local
------------
Succeeded: 6
Failed:    0
------------

Testi ei tehnyt muutoksia, joten 'hellomulti' ja muut moduulit ovat idempotentteja.

## Lähteet

Karvinen, Tero 2024. https://terokarvinen.com/2024/hello-salt-infra-as-code/
Salt Project 2025. Salt overview - Salt user guide. https://docs.saltproject.io/salt/user-guide/en/latest/topics/overview.html#rules-of-yaml
Salt Project 2025. The Top File. https://docs.saltproject.io/en/latest/ref/states/top.html

