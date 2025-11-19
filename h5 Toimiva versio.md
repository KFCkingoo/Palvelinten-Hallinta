# h5 Toimiva versio

Tämä raportti on tehty Tero Karvisen 2025 syksyllä Palvelinten Hallinta-toteutuksen myötä.

## x) Lue ja tiivistä.

#### Chacon and Straub 2014: 1.3 Getting Started - What is Git?

- Gitillä voi tehdä ja seurata muutoksia versionhallinnan kautta
- Git tallentaa tiedot ikäänkuin snapshotteina
- Useat operaatiot tapahtuvat lokaalisesti Gitissä

#### 'git add . && git commit; git pull && git push'

- git add . merkitsee muokatun tiedoston nykyisessä versiossa staging-alueelle eli seuraavaan commit snapshottiin varten (Chacon & Straub 2014)
- git commit tallentaa datan lokaaliseen tietokantaan (Chacon & Straub 2014)
- git pull näyttää version muutokset ja yhdistää sen lokaaliseen versioon (Smith 2024)
- git push siirtää commitin etärepoon (Smith 2024)

## a) Online. Tee uusi varasto GitHubiin

Tehtiin uusi repo GitHubiin nimeltä snow, tehtiin sinne myös README.md ja GPL3 lisenssi luomisvaiheessa.

<img width="1532" height="626" alt="Näyttökuva (71)" src="https://github.com/user-attachments/assets/5ff8ce1d-eede-49af-9b9c-a1427fde8040" />

## b) Dolly. Kloonaa

Kloonattiin repo `git clone git@github.com:KFCkingoo/snow.git`

Tehtiin muutokset tiedostoon README.md ja puskettiin weppiliittymään:

```
nano README.md
git add . && git commit
git pull && git push
```

<img width="869" height="485" alt="Näyttökuva (72)" src="https://github.com/user-attachments/assets/2bf6051a-a908-48c1-98b9-6461be0cbfbb" />

## c) Doh! Tee tyhmä muutos gittiin

Poistettiin README.md ja palautettiin versio `git reset --hard`. 

README.md oli tallennettu aiempaan versioon ennen muutoksia:

<img width="446" height="132" alt="VirtualBox_DebianTeroKarvinenCom_18_11_2025_13_11_55" src="https://github.com/user-attachments/assets/10fa6506-190d-4355-9edc-e5598524a46d" />

d) Tukki. Tarkastele ja selitä varastosi lokia

Tarkistettiin repon loki `git log`:

```
Author: Choy Chung <choy.chung@myy.haaga-helia.fi>
Date:   Tue Nov 18 13:04:26 2025 +0200

    added text
```

Lokissa näkyi muutoksen selitys ja muutoksen tekijä.

## e) Suolattu rakki. Aja Salt-tiloja omasta varastostasi.

Kokeiltiin jos saatiin ajettua tilaa omasta reposta. Ajettiin Salt-tila Tero Karvisen repolla /snow/suolax/ `make`:

```
local:
  Name: /tmp/hello-salt-from-git - Function: file.managed - Result: Changed - Started: 13:59:07.612400 - Duration: 6.11 ms
  Name: tree - Function: pkg.installed - Result: Clean - Started: 13:59:08.272564 - Duration: 91.85 ms
  Name: favourite-packages - Function: pkg.installed - Result: Changed - Started: 13:59:08.364597 - Duration: 6355.249 ms

Summary for local
------------
Succeeded: 3 (changed=2)
Failed:    0
------------
```

Tarkistettiin tila:

<img width="435" height="59" alt="VirtualBox_DebianTeroKarvinenCom_18_11_2025_14_08_19" src="https://github.com/user-attachments/assets/499539fb-523c-47fa-985d-f56f82cf9886" />

Tero Karvisen hakemistosta ajettiin Salt-tila omasta reposta.

Ajettiin myös edellisen raporttia varten tehty oma Salt-tila snow-reposta:

<img width="917" height="675" alt="VirtualBox_DebianTeroKarvinenCom_18_11_2025_14_38_22" src="https://github.com/user-attachments/assets/97cbdda2-c879-43ac-a24c-3e64c2b98a46" />

Tehtiin myös snow-repoon tilafunktiot ja ajettiin Salt-tila  `sudo salt-call --local --file-root snow/create/salt/ state.apply`:

local:
----------
          ID: tree
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 18:31:33.242330
    Duration: 81.066 ms
     Changes:   
----------
          ID: /tmp/u-can-del-me
    Function: file.managed
      Result: True
     Comment: Empty file
     Started: 18:31:33.324689
    Duration: 1.473 ms
     Changes:   
              ----------
              new:
                  file /tmp/u-can-del-me created

<img width="329" height="40" alt="VirtualBox_DebianTeroKarvinenCom_19_11_2025_18_38_24" src="https://github.com/user-attachments/assets/7fe6a5be-63f7-4fe5-8679-33818938ab89" />
                  

Summary for local
------------
Succeeded: 2 (changed=1)
Failed:    0
------------




## Lähteet

Chacon, S. & Straub, B. 2014. Pro Git. 2. painos. Git. E-kirja. https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F

Smith, O. 19.11.2024. GIT Push and Pull Tutorial. Datacamp. https://www.datacamp.com/tutorial/git-push-pull

Tero Karvinen 2024. https://github.com/terokarvinen/suolax/
