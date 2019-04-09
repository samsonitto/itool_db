# TTZC0800 (Tietokannat) kurssin Harjoitustyö

# Sisällysluettelo

* [Vaatimusmäärittely](#arkkitehtuurikuvaus)
    * [Johdanto](#sekvenssikaavio-kirjautuminen)
    * [Yleiskuvaus](#tilakonekaavio)
    * [Toiminnot](#käsitemalli)
    * [Ulkoiset liittymät](#luokkakaavio)
    * [Muut ominaisuudet](#sijoittelunäkymä)

#Vaatimusmäärittely

### Työkalujenlainauspalvelu (Über for tools)

* "iTool"-tietokanta
* Työryhmä: Samson Azizyan (M3156), Joel Aalto (), Jaber Askari ()
* Versio 0.1 9.4.2019

## Johdanto

Tietokanta tehdään työkalulainaussovellusta varten. Yrittäjyys tunnilla piti väkisin keksiä ideoita palvelua (sovellusta) varten. Voitetiin meidän iTool idealla huulirasvat.
Tarkoitus on tehdä tietokantaratkaisun hiekkalaatikossa toimivaa sovellusta varten. Samson toteuttaa käyttöliittymän sovellusta varten käyttöliittymäohjelmointi kurssin projektityönä.
Tavoitteena on pystyä vuokraamaan muden sovelluksen käyttäjien vuokralle jätettyjä työkaluja.

## Yleiskuvaus

```sql
insert into comment (commentDate, commentText, userID, commentParentID, toolID)
values (CURRENT_TIMESTAMP, 'Tämä on työllisyyskysymyksiin keskittyvä vaalikone. Selvitämme ehdokkaiden ja äänestäjien näkemyksiä muun muassa yhteiskuntaan, toimeentuloon, työttömyyteen, työn tulevaisuuteen ja yrittäjyyteen liittyvistä aiheista. Osallistu keskusteluun: #duunivaalit', 2,3,16),
(CURRENT_TIMESTAMP, 'Lähetä opettajalle Slack- tai sähköposti-teaseri, kun työ on valmis ja repository on valmis tarkistettavaksi. Reposta on löydettävä kaikki harjoitustyöhön liittyvä eli  lähdekoodit, tarvittavat konfigurointi, data-tiedostot, Readme ja muut tarvittavat tiedostot. Jos olet käyttänyt jotain erillistä tietokantapalvelinta, liitä mukaan tietokannan luontiskriptit, ohjeet käyttäjätunnusten ja oikeuksien antamisesta ja skripti jolla luodaan testauksessa tarvittava data.', 1,3,16);
```