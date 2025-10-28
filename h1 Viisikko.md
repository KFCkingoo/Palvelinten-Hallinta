# 1. Hello Salt

## x)

- Salt asennus Debianiin
- Salt-komentojen suoritus paikallisesti
- Nopea ohje Salt orjista ja mastereista
- Ohjeita raportin kirjoittamiseen

## b)

  Asennettu Salt Linuxille
  
  Kun yrittää manuaaliasesti täyttää lokaalista salt-komentoa salt-call eteenpäin tab-painikkeella, tapahtuu loop-virhe:
  
  <img width="865" height="77" alt="error" src="https://github.com/user-attachments/assets/bc9af864-86ea-400f-87b7-0858b80f35a4" />

  Sen voi ohittaa copy pastella tai käyttämättä tab-painiketta
  
  Asennettu wget repo tiedostoille ja luotu Saltille repo kansio saltrepo:

  <img width="801" height="350" alt="Näyttökuva (12)" src="https://github.com/user-attachments/assets/7c78f97b-65ca-4f01-a5b2-7809a20765d1" />

  Annettu luotto-oikeudet avaimelle:

  <img width="805" height="78" alt="Näyttökuva (13)" src="https://github.com/user-attachments/assets/c34d7cf3-2704-4798-b81d-c9c30e4a78de" />

  Asennettu Salt ja tarkistettu versio:

  <img width="809" height="180" alt="Näyttökuva (28)" src="https://github.com/user-attachments/assets/40db8e43-6822-4263-8bda-e7e7d9f76c85" />

  Testattu toimivuus:
 
  local:
----------
          ID: /tmp/hellotero
    Function: file.managed
      Result: True
     Comment: File /tmp/hellotero exists with proper permissions. No changes made.
     Started: 11:18:09.037749
    Duration: 5.987 ms
     Changes:   

Summary for local
------------
Succeeded: 1
Failed:    0
------------

  
## c)

  Tarkistettu, että sovellus on asennettu käyttämällä pkg.installed tilafunktiota:

  local:
----------
          ID: tree
    Function: pkg.installed
      Result: True
     Comment: The following packages were installed/updated: tree
     Started: 11:22:56.677462
    Duration: 3919.423 ms
     Changes:   
              ----------
              tree:
                  ----------
                  new:
                      2.2.1-1
                  old:

Summary for local
------------
Succeeded: 1 (changed=1)
Failed:    0
------------

  Koska tree sovellus oli absent tilassa, se asennettiin
  

  Testattu file.managed tilafunktiota:

  local:
----------
          ID: /tmp/hellochoy
    Function: file.managed
      Result: True
     Comment: Empty file
     Started: 11:30:23.991853
    Duration: 5.744 ms
     Changes:   
              ----------
              new:
                  file /tmp/hellochoy created

Summary for local
------------
Succeeded: 1 (changed=1)
Failed:    0
------------

  Koska tiedostoa ei ollut olemassa, komento teki uuden tiedoston
  

  Testattu service.running tilafunktiota apache2 palvelulla:

  local:
----------
          ID: apache2
    Function: service.running
      Result: True
     Comment: Started service apache2
     Started: 11:33:02.446794
    Duration: 206.37 ms
     Changes:   
              ----------
              apache2:
                  True

Summary for local
------------
Succeeded: 1 (changed=1)
Failed:    0
------------

  Palvelu apache2 on asennettu, joten palvelua suoritetaan
  

  Testattu user.present tilafunktiota, tarkistettu käyttäjää choykkeli:

  local:
----------
          ID: choykkeli
    Function: user.present
      Result: True
     Comment: New user choykkeli created
     Started: 11:36:28.598420
    Duration: 96.15 ms
     Changes:   
              ----------
              fullname:
              gid:
                  1002
              groups:
                  - choykkeli
              home:
                  /home/choykkeli
              homephone:
              name:
                  choykkeli
              other:
              passwd:
                  x
              roomnumber:
              shell:
                  /bin/sh
              uid:
                  1002
              workphone:

Summary for local
------------
Succeeded: 1 (changed=1)
Failed:    0
------------

  Ko. käyttäjää ei ollut olemassa, joten komento loi käyttäjän
  

  Testattu cmd.run tilafunktiota, sovelluksen suoritusta:

  local:
----------
          ID: touch /tmp/foo
    Function: cmd.run
      Result: True
     Comment: Command "touch /tmp/foo" run
     Started: 11:39:22.214405
    Duration: 4.061 ms
     Changes:   
              ----------
              pid:
                  4471
              retcode:
                  0
              stderr:
              stdout:

Summary for local
------------
Succeeded: 1 (changed=1)
Failed:    0
------------

  Sovellusta suoritettiin
  

  Testattu idempotentti creates komennolla

  local:
----------
          ID: touch /tmp/foo
    Function: cmd.run
      Result: True
     Comment: /tmp/foo exists
     Started: 11:46:33.837479
    Duration: 1020.335 ms
     Changes:   

Summary for local
------------
Succeeded: 1
Failed:    0
------------

  Tiedosto oli olemassa, ei muutoksia tehty
  
  
  Testattu idempotentti myös file.managed tilafunktiolla

  local:
----------
          ID: /tmp/foo
    Function: file.managed
      Result: True
     Comment: File /tmp/foo exists with proper permissions. No changes made.
     Started: 11:52:58.483222
    Duration: 5.558 ms
     Changes:   

Summary for local
------------
Succeeded: 1
Failed:    0
------------

  Tiedosto oli olemassa, ei muutoksia tehty

## d)

  Kun pkg.installed tree komentoa ajettiin, se tarkisti onko tree asennettu. Jos se on asennettu niin ei tehty mitään muutoksia, toisin jos tree on 'absent' eli ei asennettu niin siihen tehtiin muutoksia (asennus).

  Ajettiin pkg.installed tree komentoa paikallisesti

  local:
----------
          ID: tree
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 23:20:29.830127
    Duration: 78.576 ms
     Changes:   

Summary for local
------------
Succeeded: 1
Failed:    0

  Tree sovellus on jo valmiiksi asennettu, joten sille ei tehdä muutoksia. Jos sitä ei olisi asennettu, niin siihen olisi tehty asennus
  
## Lähteet

Karvinen, Tero 2021. Run Salt Command Locally. https://terokarvinen.com/2021/salt-run-command-locally/.
