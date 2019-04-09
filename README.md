# TTZC0800 (Tietokannat) kurssin Harjoitustyö

# Sisällysluettelo

* [Vaatimusmäärittely](#vaatimusmäärittely)
    * [Johdanto](#johdanto)
    * [Yleiskuvaus](#yleiskuvaus)
    * [Toiminnot](#toiminnot)
    * [Ulkoiset liittymät](#luokkakaavio)
    * [Muut ominaisuudet](#sijoittelunäkymä)

# Vaatimusmäärittely

### Työkalujenlainauspalvelu (Über for tools)

* "iTool"-tietokanta
* Työryhmä: Samson Azizyan (M3156), Joel Aalto (), Jaber Askari ()
* Versio 0.1 9.4.2019

## Johdanto

Tietokanta tehdään työkalulainaussovellusta varten. Yrittäjyys tunnilla piti väkisin keksiä ideoita palvelua (sovellusta) varten. Voitetiin meidän iTool idealla huulirasvat.
Tarkoitus on tehdä tietokantaratkaisun hiekkalaatikossa toimivaa sovellusta varten. Samson toteuttaa käyttöliittymän sovellusta varten käyttöliittymäohjelmointi kurssin projektityönä.
Tavoitteena on pystyä vuokraamaan muden sovelluksen käyttäjien vuokralle jätettyjä työkaluja.

## Yleiskuvaus

Jokainen iTool palveluun luonut tunnukset pystyy kirjautumaan palveluun. Käyttäjä pystyy selaamaan kaikki työkalut tai suodattamaan sijainnin tai työkalutyypin mukaan.
Käyttäjä myös pystyy hakemaan "haku"-palkilla työkaluja nimien mukaan. Käyttäjä voi vuokrata työkaluja hinnan mukaan (€/24h). Käyttäjät pystyy kommentoimaan työkaluja ja vastamaan kommenteihin.
Käyttäjät voi jättä arvion toisistaan liittyen transaktioneihin.

## Toiminnot

### Pakollisia toimintoja ovat:
1. Käyttäjätietojen (ID, nimi, email, salasana) hallinta
2. Työkalutietojen (ID, nimi, hinta) hallinta
3. Vuokralla olevien työkalujen hallinta
4. Saatavilla olevien työkalujen hallinta
5. Omien työkalujen hallinta

## Ulkoiset liittymät

Käyttöliittymänä käytetään Samsonin tekemän harjoitustyötä käyttöliittymäohjelmoinnin kurssilla. Käyttöliittymä koostuu 5-6 WPF ikkunasta ja toiminnallisuus on ohjelmoitu c#:lla.


## Muut ominaisuudet

