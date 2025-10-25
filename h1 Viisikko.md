# Tiivistelmä

- Salt asennus Debianiin
- Salt-komentojen suoritus paikallisesti
- Nopea ohje Salt orjista ja mastereista
- Ohjeita raportin kirjoittamiseen

## b)

  Asennettu Salt Linuxille
  
  Kun yrittää manuaaliasesti täyttää lokaalista salt-komentoa salt-call eteenpäin tab-painikkeella, tapahtuu loop-virhe
  
  <img width="865" height="77" alt="error" src="https://github.com/user-attachments/assets/bc9af864-86ea-400f-87b7-0858b80f35a4" />

  Sen voi ohittaa copy pastella tai käyttämättä tab-painiketta
  
  Salt asennettu Debianiin

## c)

  pkg.installed - Tarkistaa onko sovellusta asennettu

  file.managed - Tiedoston tai kansion tarkistus
  
  service.running - Palvelun käynnissäolo tarkistus
  
  user.present - Käyttäjän tarkistus
  
  cmd.run - Sovelluksen suoritus

## d)

  Kun pkg.installed tree komentoa ajettiin, se tarkisti onko tree asennettu. Jos se on asennettu niin ei tehty mitään muutoksia, toisin jos tree on 'absent' eli ei asennettu niin siihen tehtiin muutoksia (asennus).

  Tulokset kun komentoa ajettiin ja siihen ei tehty muutoksia:

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

  
  
## Lähteet

Karvinen, Tero 2021. Run Salt Command Locally. https://terokarvinen.com/2021/salt-run-command-locally/.
