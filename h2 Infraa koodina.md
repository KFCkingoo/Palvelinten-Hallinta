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

Luotiin 'helloucmd' kansio ``sudo mkdir hellocmd`` ja kansioon luotiin init.sls tiedosto ``sudoedit init.sls``, tiedostoon lisättiin:

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

## Lähteet

Karvinen, Tero 2024. https://terokarvinen.com/2024/hello-salt-infra-as-code/
Salt Project 2025. Salt overview - Salt user guide. https://docs.saltproject.io/salt/user-guide/en/latest/topics/overview.html#rules-of-yaml
Salt Project 2025. The Top File. https://docs.saltproject.io/en/latest/ref/states/top.html

