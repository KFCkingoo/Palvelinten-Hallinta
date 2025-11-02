# h2 Infraa koodina

## x)
- 'hello' moduulin luonti ja testaus

## a)

Otettiin käyttöön micro editori ``sudo apt-get -y install micro`` + ``export EDITOR=micro``.

Tehtiin kansio 'hello' moduulille ``sudo mkdir -p /srv/salt/hello/``.

Mentiin kansioon ``cd /srv/salt/hello/``.

Editoitiin 'hello' moduuli ``sudoedit init.sls``. Sinne lisättiin: 

``/tmp/hellotero:``

`` file.managed``
  
Testattiin moduuli paikallisesti ``sudo salt-call --local state.apply hello``:

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

Testattiin, että moduuli toimii oikein toisella tavalla ``ls /tmp/hellochoy``:

``hellochoy``

Moduuli toimi.



## b)

## c)

## d)

## Lähteet

Karvinen, Tero 2024. https://terokarvinen.com/2024/hello-salt-infra-as-code/

