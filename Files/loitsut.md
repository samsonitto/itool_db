# iTool loitsuhistoria

```sql
select toolID from transaction where userOwnerID = 2 AND transactionPlannedEndDate >= '2019-03-26 00:00:00';
+--------+
| toolID |
+--------+
|      2 |
|      1 |
+--------+
```

```sql
select userName, toolID from user inner join transaction on UserID = userOwnerID where userOwnerID = 2 AND transactionPlannedEndDate >= '2019-03-26 00:00:00';
+----------+--------+
| userName | toolID |
+----------+--------+
| kk       |      2 |
| kk       |      1 |
+----------+--------+
```

```sql
select userName, userPicture, toolID from user inner join transaction on UserID = userOwnerID where userOwnerID = 2 AND transactionPlannedEndDate >= '2019-03-26 00:00:00';
+----------+-------------+--------+
| userName | userPicture | toolID |
+----------+-------------+--------+
| kk       | amanda.jpg  |      2 |
| kk       | amanda.jpg  |      1 |
+----------+-------------+--------+
```

```sql
select concat(userName,' ',userSurname) as 'Full name', userPicture, toolID from user inner join transaction on UserID = userOwnerID where userOwnerID = 2 AND transactionPlannedEndDate >= '2019-03-26 00:00:00';
+-----------+-------------+--------+
| Full name | userPicture | toolID |
+-----------+-------------+--------+
| kk kk     | amanda.jpg  |      2 |
| kk kk     | amanda.jpg  |      1 |
+-----------+-------------+--------+
```

### Haetaan kaikki työkalut olkoon lainassa tai ei

```sql
SELECT tool.toolID, tool.userOwnerID, tool.toolCategoryID, toolPicture, toolName, toolDescription, toolPrice, toolCondition, toolCategoryName, userLocation, transactionPlannedEndDate, ActualEndDate FROM tool INNER JOIN toolCategory ON tool.toolCategoryID = toolCategory.toolCategoryID LEFT JOIN transaction ON tool.toolID = transaction.toolID LEFT JOIN tr_completion ON transaction.transactionID = tr_completion.transactionID INNER JOIN user ON tool.userOwnerID = userID;
```

### Lisätään uudet työkalukategoriat:

```sql
insert into toolCategory (toolCategoryName, toolCategoryDescription)
values ('Hionta', 'Monitoimihiomakone, hiomapaperit ja muut hiomatarvikkeet'),
('Hitsauskoneet ja tarvikkeet','Täältä löydät laajan valikoiman hitsauslaitteita, kaasupolttimia ja plasmaleikkureita.'),
('Juottaminen','Kaikki juottamiseen'),
('Kiillotuskoneet ja kiillotustarvikkeet','Hyvillä kiillotustarvikkeilla on helppoa saada auto tai vene kiiltämään kuin uusi.'),
('Käsityökalut','Vasarat, ruuvimeisselit, hylsyavaimet ja muut käsityökalut'),
('Leikkaustyökalut','Puukot, veitset ja veitsenterottimet'),
('Leikkuuterät','Katkaisulaikat ja timanttilaikat'),
('Mittavälineet','Mittavälineet ja mittalaitteet'),
('Paineilma','Kompressorit ja paineilmatyökalut'),
('Poranterät','Poranterät eri materiaaleille'),
('Työkalujen säilyttäminen','Kaikki työkalut ovat siististi paikallaan ja järjestyksessä'),
('Työpajan varustus','Varusteet työpajalle ja verstaalle'),
('Työstökoneet','Penkkihiomakoneet ja muut työstökoneet'),
('Sähkötyökalut','Sähköllä toimivat työkalut');
```

### Lisätään uusi työkalu "Vasara"

```sql
INSERT INTO tool (toolName, toolDescription, toolPrice, toolCondition, toolCategoryID, userOwnerID)
VALUES ('Vasara','Ihan perus vasara',0.5,'uudenveroinen',5,12),
('Vasara','Iso vasara',1,'Hyvä',11,11);
```

### Luodaan uusi näkymä, jossa näkyy työkalun ominaisuudet, sijainti, suunniteltu palautuspäivä ja palautuspäivä riipumatta siitä onko työkalu ollut missä transactiossa:

```sql
create view all_tools as SELECT tool.toolID, tool.userOwnerID, tool.toolCategoryID, toolPicture, toolName, toolDescription, toolPrice, toolCondition, toolCategoryName, userLocation, transactionPlannedEndDate, ActualEndDate FROM tool INNER JOIN toolCategory ON tool.toolCategoryID = toolCategory.toolCategoryID LEFT JOIN transaction ON tool.toolID = transaction.toolID LEFT JOIN tr_completion ON transaction.transactionID = tr_completion.transactionID INNER JOIN user ON tool.userOwnerID = userID;
```

### Lisätään uusi työkalu "Iso jakoavain"

```sql
insert into tool (toolName, toolDescription, toolPrice, toolCondition, toolCategoryID, userOwnerID) values ('Iso jakoavain','Isonkokoinen jakoavain, voi käyttää aseena',0.7,'uudenveroinen',6,12);
```

### Valitaan kaikki saatavilla olevat työkalut, jos Active.UserID = 1, ('2019-04-07 12:00:00' - on DateTime.Now):

```sql
SELECT * FROM all_tools where userOwnerID != 1 and (transactionPlannedEndDate < '2019-04-07 12:00:00' || transactionPlannedEndDate is null) AND (ActualEndDate < '2019-04-07 12:00:00' || ActualEndDate IS NULL);
```


### Valitaan kaikki saatavilla olevat työkalut käyttämällä CURRENT_TIMESTAMP, jos Active.UserID = 1:

```sql
SELECT * FROM all_tools WHERE userOwnerID != 1 AND (transactionPlannedEndDate < CURRENT_TIMESTAMP || transactionPlannedEndDate IS NULL) AND (ActualEndDate < CURRENT_TIMESTAMP || ActualEndDate IS NULL);
+--------+-------------+----------------+-------------+------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
| toolID | userOwnerID | toolCategoryID | toolPicture | toolName         | toolDescription                               | toolPrice | toolCondition | toolCategoryName | userLocation | transactionPlannedEndDate | ActualEndDate |
+--------+-------------+----------------+-------------+------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
|      3 |           2 |              3 | NULL        | Hitachi porakone | 2h akku                                       |      3.00 | hyv▒          | S▒hk▒porakoneet  | Jyv▒skyl▒    | 2019-04-01 15:50:10       | NULL          |
|      4 |           3 |              4 | NULL        | Metallisaha      | ohut                                          |      1.50 | uudenveroinen | Saha             | Lappeenranta | 2019-03-28 15:57:10       | NULL          |
|      6 |          12 |              5 | NULL        | Vasara           | Ihan perus vasara                             |      0.50 | uudenveroinen | Vasarat          | Jgvjqs       | NULL                      | NULL          |
|      7 |          12 |              6 | NULL        | Iso jakoavain    | Isonkokoinen jakoavain, voi käyttää aseena |      0.70 | uudenveroinen | Jakoavaimet      | Jgvjqs       | NULL                      | NULL          |
+--------+-------------+----------------+-------------+------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+

```

### Luodaan näkymä joka näyttää lainassa olevat työkalut:

```sql
CREATE VIEW rented_tools AS SELECT * FROM all_tools WHERE transactionPlannedEndDate > CURRENT_TIMESTAMP;
+--------+-------------+----------------+-------------+------------------+-----------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
| toolID | userOwnerID | toolCategoryID | toolPicture | toolName         | toolDescription | toolPrice | toolCondition | toolCategoryName | userLocation | transactionPlannedEndDate | ActualEndDate |
+--------+-------------+----------------+-------------+------------------+-----------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
|      3 |           2 |              3 | NULL        | Hitachi porakone | 2h akku         |      3.00 | hyv▒          | S▒hk▒porakoneet  | Jyv▒skyl▒    | 2019-04-12 10:50:26       | NULL          |
+--------+-------------+----------------+-------------+------------------+-----------------+-----------+---------------+------------------+--------------+---------------------------+---------------+

```

### Näytetään kaikki saatavilla olevat työkalut, jos Active.UserID = 1

```sql
SELECT * FROM all_tools WHERE userOwnerID != 1 AND toolID NOT IN (SELECT toolID FROM rented_tools);
+--------+-------------+----------------+-------------+---------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
| toolID | userOwnerID | toolCategoryID | toolPicture | toolName      | toolDescription                               | toolPrice | toolCondition | toolCategoryName | userLocation | transactionPlannedEndDate | ActualEndDate |
+--------+-------------+----------------+-------------+---------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
|      4 |           3 |              4 | NULL        | Metallisaha   | ohut                                          |      1.50 | uudenveroinen | Saha             | Lappeenranta | 2019-03-28 15:57:10       | NULL          |
|      6 |          12 |              5 | NULL        | Vasara        | Ihan perus vasara                             |      0.50 | uudenveroinen | Vasarat          | Jgvjqs       | NULL                      | NULL          |
|      7 |          12 |              6 | NULL        | Iso jakoavain | Isonkokoinen jakoavain, voi käyttää aseena |      0.70 | uudenveroinen | Jakoavaimet      | Jgvjqs       | NULL                      | NULL          |
+--------+-------------+----------------+-------------+---------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+

```

### Lisätään työkaluja kuvien kanssa

```sql
INSERT INTO tool (toolName, toolDescription, toolPrice, toolCondition, toolCategoryID, userOwnerID, toolPicture)
VALUES ('Vasara','Iso vasara',1,'Hyvä',5,11, 'hammer.png'),
('Ruuvimeisseli','Talttapäinen iso meisseli',0.3,'Uudenveroinen',5,10,'screwdriver.png'),
('Jakoavain','Pieni jakoavain',0.01,'Kelvoton',5,12,'wrench.png'),
('Ruuviväännin','Sähköllä toimiva ruuviväännin',3,'Hyvä',14,4,'escrewdriver.png'),
('Porakone','Perus porakone',2.5,'Uudenveroinen',14,9,'drill.png');
```

### Poistetaan näkymä all_tools ja luodaan uudestaan all_tools, joka sisälttää userLesseeID:

```sql
CREATE VIEW all_tools AS SELECT tool.toolID, tool.userOwnerID, transaction.userLesseeID, tool.toolCategoryID, toolPicture, toolName, toolDescription, toolPrice, toolCondition, toolCategoryName, userLocation, transactionPlannedEndDate, ActualEndDate FROM tool INNER JOIN toolCategory ON tool.toolCategoryID = toolCategory.toolCategoryID LEFT JOIN transaction ON tool.toolID = transaction.toolID LEFT JOIN tr_completion ON transaction.transactionID = tr_completion.transactionID INNER JOIN user ON tool.userOwnerID = userID;
+--------+-------------+--------------+----------------+------------------+----------------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------------+
| toolID | userOwnerID | userLesseeID | toolCategoryID | toolPicture      | toolName                   | toolDescription                               | toolPrice | toolCondition | toolCategoryName | userLocation | transactionPlannedEndDate | ActualEndDate       |
+--------+-------------+--------------+----------------+------------------+----------------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------------+
|      1 |           1 |            1 |              1 | NULL             | Ristip▒inen ruuvimeisseli  | keskikokoinen                                 |      1.00 | uudenveroinen | Ruuvimeisseli    | Jyv▒skyl▒    | 2019-03-26 15:57:10       | NULL                |
|      2 |           1 |            1 |              1 | NULL             | Talttap▒inen ruuvimeisseli | pieni                                         |      0.50 | huono         | Ruuvimeisseli    | Jyv▒skyl▒    | 2019-03-26 15:50:10       | 2019-03-29 15:50:10 |
|      2 |           1 |            1 |              1 | NULL             | Talttap▒inen ruuvimeisseli | pieni                                         |      0.50 | huono         | Ruuvimeisseli    | Jyv▒skyl▒    | 2019-03-26 15:50:10       | 2019-03-29 15:50:10 |
|      3 |           2 |            4 |              3 | NULL             | Hitachi porakone           | 2h akku                                       |      3.00 | hyv▒          | S▒hk▒porakoneet  | Jyv▒skyl▒    | 2019-04-01 15:50:10       | 2019-04-07 13:46:23 |
|      3 |           2 |            1 |              3 | NULL             | Hitachi porakone           | 2h akku                                       |      3.00 | hyv▒          | S▒hk▒porakoneet  | Jyv▒skyl▒    | 2019-04-12 10:50:26       | NULL                |
|      4 |           3 |            2 |              4 | NULL             | Metallisaha                | ohut                                          |      1.50 | uudenveroinen | Saha             | Lappeenranta | 2019-03-28 15:57:10       | NULL                |
|      6 |          12 |            1 |              5 | NULL             | Vasara                     | Ihan perus vasara                             |      0.50 | uudenveroinen | Vasarat          | Jgvjqs       | 2019-04-10 14:00:34       | NULL                |
|      7 |          12 |            1 |              6 | NULL             | Iso jakoavain              | Isonkokoinen jakoavain, voi käyttää aseena |      0.70 | uudenveroinen | Jakoavaimet      | Jgvjqs       | 2019-04-14 14:08:27       | NULL                |
|     13 |          11 |         NULL |             11 | hammer.png       | Vasara                     | Iso vasara                                    |      1.00 | Hyvä         | Käsityökalut   | Zsgnsa       | NULL                      | NULL                |
|     14 |          10 |         NULL |             11 | screwdriver.png  | Ruuvimeisseli              | Talttapäinen iso meisseli                    |      0.30 | Uudenveroinen | Käsityökalut   | Dsouly       | NULL                      | NULL                |
|     15 |          12 |         NULL |             11 | wrench.png       | Jakoavain                  | Pieni jakoavain                               |      0.01 | Kelvoton      | Käsityökalut   | Jgvjqs       | NULL                      | NULL                |
|     16 |           4 |         NULL |             20 | escrewdriver.png | Ruuviväännin             | Sähköllä toimiva ruuviväännin            |      3.00 | Hyvä         | Sähkötyökalut | Jyv▒skyl▒    | NULL                      | NULL                |
|     17 |           9 |         NULL |             20 | drill.png        | Porakone                   | Perus porakone                                |      2.50 | Uudenveroinen | Sähkötyökalut | Oulu         | NULL                      | NULL                |
+--------+-------------+--------------+----------------+------------------+----------------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------------+

```

### Poistetaan näkymä rented_tools ja luodaan uudestaan rented_tools, joka sisältää userLesseeID:

```sql
CREATE VIEW rented_tools AS SELECT * FROM all_tools WHERE transactionPlannedEndDate > CURRENT_TIMESTAMP;
+--------+-------------+--------------+----------------+-------------+------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
| toolID | userOwnerID | userLesseeID | toolCategoryID | toolPicture | toolName         | toolDescription                               | toolPrice | toolCondition | toolCategoryName | userLocation | transactionPlannedEndDate | ActualEndDate |
+--------+-------------+--------------+----------------+-------------+------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
|      3 |           2 |            1 |              3 | NULL        | Hitachi porakone | 2h akku                                       |      3.00 | hyv▒          | S▒hk▒porakoneet  | Jyv▒skyl▒    | 2019-04-12 10:50:26       | NULL          |
|      6 |          12 |            1 |              5 | NULL        | Vasara           | Ihan perus vasara                             |      0.50 | uudenveroinen | Vasarat          | Jgvjqs       | 2019-04-10 14:00:34       | NULL          |
|      7 |          12 |            1 |              6 | NULL        | Iso jakoavain    | Isonkokoinen jakoavain, voi käyttää aseena |      0.70 | uudenveroinen | Jakoavaimet      | Jgvjqs       | 2019-04-14 14:08:27       | NULL          |
+--------+-------------+--------------+----------------+-------------+------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+

```

### Poistetaan näkymä rented_tools ja luodaan uudestaan rented_tools, joka sisältää myös ei palautettuja ajallaan työkaluja:

```sql
CREATE VIEW rented_tools AS SELECT * FROM all_tools WHERE (transactionPlannedEndDate IS NOT NULL AND ActualEndDate IS NULL);
+--------+-------------+--------------+----------------+-------------+---------------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
| toolID | userOwnerID | userLesseeID | toolCategoryID | toolPicture | toolName                  | toolDescription                               | toolPrice | toolCondition | toolCategoryName | userLocation | transactionPlannedEndDate | ActualEndDate |
+--------+-------------+--------------+----------------+-------------+---------------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
|      1 |           1 |            1 |              1 | NULL        | Ristip▒inen ruuvimeisseli | keskikokoinen                                 |      1.00 | uudenveroinen | Ruuvimeisseli    | Jyv▒skyl▒    | 2019-03-26 15:57:10       | NULL          |
|      4 |           3 |            2 |              4 | NULL        | Metallisaha               | ohut                                          |      1.50 | uudenveroinen | Saha             | Lappeenranta | 2019-03-28 15:57:10       | NULL          |
|      3 |           2 |            1 |              3 | NULL        | Hitachi porakone          | 2h akku                                       |      3.00 | hyv▒          | S▒hk▒porakoneet  | Jyv▒skyl▒    | 2019-04-12 10:50:26       | NULL          |
|      6 |          12 |            1 |              5 | NULL        | Vasara                    | Ihan perus vasara                             |      0.50 | uudenveroinen | Vasarat          | Jgvjqs       | 2019-04-10 14:00:34       | NULL          |
|      7 |          12 |            1 |              6 | NULL        | Iso jakoavain             | Isonkokoinen jakoavain, voi käyttää aseena |      0.70 | uudenveroinen | Jakoavaimet      | Jgvjqs       | 2019-04-14 14:08:27       | NULL          |
|     15 |          12 |            1 |             11 | wrench.png  | Jakoavain                 | Pieni jakoavain                               |      0.01 | Kelvoton      | Käsityökalut   | Jgvjqs       | 2019-04-09 15:08:49       | NULL          |
+--------+-------------+--------------+----------------+-------------+---------------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+

```

### Poistetaan näkymä all_tools ja luodaan uusi näkymä all_tools, joka sisältää kaikkien vanhojen kenttien lisäksi transactionID:

```sql
CREATE VIEW all_tools AS SELECT tool.toolID, tool.userOwnerID, transaction.userLesseeID, transaction.transactionID, tool.toolCategoryID, toolPicture, toolName, toolDescription, toolPrice, toolCondition, toolCategoryName, userLocation, transactionPlannedEndDate, ActualEndDate FROM tool INNER JOIN toolCategory ON tool.toolCategoryID = toolCategory.toolCategoryID LEFT JOIN transaction ON tool.toolID = transaction.toolID LEFT JOIN tr_completion ON transaction.transactionID = tr_completion.transactionID INNER JOIN user ON tool.userOwnerID = userID;
+--------+-------------+--------------+---------------+----------------+------------------+----------------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------------+
| toolID | userOwnerID | userLesseeID | transactionID | toolCategoryID | toolPicture      | toolName                   | toolDescription                               | toolPrice | toolCondition | toolCategoryName | userLocation | transactionPlannedEndDate | ActualEndDate       |
+--------+-------------+--------------+---------------+----------------+------------------+----------------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------------+
|      1 |           1 |            1 |             2 |              1 | NULL             | Ristip▒inen ruuvimeisseli  | keskikokoinen                                 |      1.00 | uudenveroinen | Ruuvimeisseli    | Jyv▒skyl▒    | 2019-03-26 15:57:10       | NULL                |
|      2 |           1 |            1 |             1 |              1 | NULL             | Talttap▒inen ruuvimeisseli | pieni                                         |      0.50 | huono         | Ruuvimeisseli    | Jyv▒skyl▒    | 2019-03-26 15:50:10       | 2019-03-29 15:50:10 |
|      2 |           1 |            1 |             1 |              1 | NULL             | Talttap▒inen ruuvimeisseli | pieni                                         |      0.50 | huono         | Ruuvimeisseli    | Jyv▒skyl▒    | 2019-03-26 15:50:10       | 2019-03-29 15:50:10 |
|      3 |           2 |            4 |             3 |              3 | NULL             | Hitachi porakone           | 2h akku                                       |      3.00 | hyv▒          | S▒hk▒porakoneet  | Jyv▒skyl▒    | 2019-04-01 15:50:10       | 2019-04-07 13:46:23 |
|      3 |           2 |            1 |             5 |              3 | NULL             | Hitachi porakone           | 2h akku                                       |      3.00 | hyv▒          | S▒hk▒porakoneet  | Jyv▒skyl▒    | 2019-04-12 10:50:26       | NULL                |
|      4 |           3 |            2 |             4 |              4 | NULL             | Metallisaha                | ohut                                          |      1.50 | uudenveroinen | Saha             | Lappeenranta | 2019-03-28 15:57:10       | NULL                |
|      6 |          12 |            1 |             6 |              5 | NULL             | Vasara                     | Ihan perus vasara                             |      0.50 | uudenveroinen | Vasarat          | Jgvjqs       | 2019-04-10 14:00:34       | NULL                |
|      7 |          12 |            1 |             7 |              6 | NULL             | Iso jakoavain              | Isonkokoinen jakoavain, voi käyttää aseena |      0.70 | uudenveroinen | Jakoavaimet      | Jgvjqs       | 2019-04-14 14:08:27       | NULL                |
|     13 |          11 |         NULL |          NULL |             11 | hammer.png       | Vasara                     | Iso vasara                                    |      1.00 | Hyvä         | Käsityökalut   | Zsgnsa       | NULL                      | NULL                |
|     14 |          10 |         NULL |          NULL |             11 | screwdriver.png  | Ruuvimeisseli              | Talttapäinen iso meisseli                    |      0.30 | Uudenveroinen | Käsityökalut   | Dsouly       | NULL                      | NULL                |
|     15 |          12 |            1 |             8 |             11 | wrench.png       | Jakoavain                  | Pieni jakoavain                               |      0.01 | Kelvoton      | Käsityökalut   | Jgvjqs       | 2019-04-09 15:08:49       | NULL                |
|     16 |           4 |         NULL |          NULL |             20 | escrewdriver.png | Ruuviväännin             | Sähköllä toimiva ruuviväännin            |      3.00 | Hyvä         | Sähkötyökalut | Jyv▒skyl▒    | NULL                      | NULL                |
|     17 |           9 |         NULL |          NULL |             20 | drill.png        | Porakone                   | Perus porakone                                |      2.50 | Uudenveroinen | Sähkötyökalut | Oulu         | NULL                      | NULL                |
+--------+-------------+--------------+---------------+----------------+------------------+----------------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------------+

```

### Päivitetään rented_tools näkymä, luomalla se uudestaan. Näkymässä näkyy nyt transactionID vanhojen ominaisuuksien lisäksi.

### Luodaan 3 käyttäjää (user)

```sql
INSERT INTO user (userName, userSurname, userAddress, userEmail, userLocation, paymentMethod, userMobile, userPassword, userPicture)
VALUES ('Samson', 'Azizyan', 'Lehtorannantie 6 a 3', 'samson@samson.fi', 'Jyväskylä', 'Paypal', '0400000000', 'samson', 'samson_profile.jpg'),
('Jaber','Askari','Keurusaatana 666','jaber@jaber.fi','Jyväskylä','VISA','0404040404','jaber','jaber.jpg'),
('Joel','Aalto','Jossainkaukana 69','joel@joel.fi','Lappeenranta','MasterCard','0404040404','joel','joel.jpg');
```

### Luodaan 3 samaa käyttäjää salatuilla salasanoilla

```sql
INSERT INTO user (userName, userSurname, userAddress, userEmail, userLocation, paymentMethod, userMobile, userPassword, userPicture)
VALUES ('Samson', 'Azizyan', 'Lehtorannantie 6 a 3', 'samson@samson.fi', 'Jyväskylä', 'Paypal', '0400000000', MD5('samson'), 'samson_profile.jpg'),
('Jaber','Askari','Keurusaatana 666','jaber@jaber.fi','Jyväskylä','VISA','0404040404',MD5('jaber'),'jaber.jpg'),
('Joel','Aalto','Jossainkaukana 69','joel@joel.fi','Lappeenranta','MasterCard','0404040404',MD5('joel'),'joel.jpg');
```

### Lisätään 3 transaktiota ilman actualEndDate:a

```sql
INSERT INTO transaction (transactionStartDate, transactionPlannedEndDate, userOwnerID, userLesseeID, toolID)
VALUES (CURRENT_TIMESTAMP, (SELECT date_add(CURRENT_TIMESTAMP, INTERVAL 5 DAY)), 1, 2, 6),
(CURRENT_TIMESTAMP, (SELECT date_add(CURRENT_TIMESTAMP, INTERVAL 7 DAY)), 2, 1, 7),
(CURRENT_TIMESTAMP, (SELECT date_add(CURRENT_TIMESTAMP, INTERVAL 10 DAY)), 3, 4, 8);
```

### Lisätään puuttuva "rating int(1)" kolumni rating tauluun

```sql
ALTER TABLE rating ADD rating INT(1) NOT NULL;
```

### Luodaan uusi "all_tools" näkymä, koska ollaan yhdistetty "tr_completion" ja "transaction" taulut yhteen

```sql
CREATE VIEW all_tools AS SELECT tool.toolID, tool.userOwnerID, transaction.userLesseeID, transaction.transactionID, tool.toolCategoryID, toolPicture, toolName, toolDescription, toolPrice, toolCondition, toolCategoryName, userLocation, transactionPlannedEndDate, actualEndDate FROM tool INNER JOIN toolCategory ON tool.toolCategoryID = toolCategory.toolCategoryID LEFT JOIN transaction ON tool.toolID = transaction.toolID INNER JOIN user ON tool.userOwnerID = userID;
+--------+-------------+--------------+---------------+----------------+------------------+---------------+-------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
| toolID | userOwnerID | userLesseeID | transactionID | toolCategoryID | toolPicture      | toolName      | toolDescription               | toolPrice | toolCondition | toolCategoryName | userLocation | transactionPlannedEndDate | actualEndDate |
+--------+-------------+--------------+---------------+----------------+------------------+---------------+-------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
|      6 |          11 |            2 |             4 |              5 | hammer.png       | Vasara        | Iso vasara                    |      1.00 | Hyv▒          | K▒sity▒kalut     | Tampere      | 2019-04-15 14:06:44       | NULL          |
|      7 |          10 |            1 |             5 |              5 | screwdriver.png  | Ruuvimeisseli | Talttap▒inen iso meisseli     |      0.30 | Uudenveroinen | K▒sity▒kalut     | Vaasa        | 2019-04-17 14:06:44       | NULL          |
|      8 |          12 |            4 |             6 |              5 | wrench.png       | Jakoavain     | Pieni jakoavain               |      0.01 | Kelvoton      | K▒sity▒kalut     | Lahti        | 2019-04-20 14:06:44       | NULL          |
|      9 |           4 |         NULL |          NULL |             14 | escrewdriver.png | Ruuviv▒▒nnin  | S▒hk▒ll▒ toimiva ruuviv▒▒nnin |      3.00 | Hyv▒          | S▒hk▒ty▒kalut    | Oulu         | NULL                      | NULL          |
|     10 |           9 |         NULL |          NULL |             14 | drill.png        | Porakone      | Perus porakone                |      2.50 | Uudenveroinen | S▒hk▒ty▒kalut    | Lahti        | NULL                      | NULL          |
+--------+-------------+--------------+---------------+----------------+------------------+---------------+-------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
```

### Luodaan uusi "rented_tools" näkymä
```sql
CREATE VIEW rented_tools AS SELECT * FROM all_tools WHERE (transactionPlannedEndDate IS NOT NULL AND actualEndDate IS NULL);
+--------+-------------+--------------+---------------+----------------+-----------------+---------------+---------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
| toolID | userOwnerID | userLesseeID | transactionID | toolCategoryID | toolPicture     | toolName      | toolDescription           | toolPrice | toolCondition | toolCategoryName | userLocation | transactionPlannedEndDate | actualEndDate |
+--------+-------------+--------------+---------------+----------------+-----------------+---------------+---------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
|      6 |          11 |            2 |             4 |              5 | hammer.png      | Vasara        | Iso vasara                |      1.00 | Hyv▒          | K▒sity▒kalut     | Tampere      | 2019-04-15 14:06:44       | NULL          |
|      7 |          10 |            1 |             5 |              5 | screwdriver.png | Ruuvimeisseli | Talttap▒inen iso meisseli |      0.30 | Uudenveroinen | K▒sity▒kalut     | Vaasa        | 2019-04-17 14:06:44       | NULL          |
|      8 |          12 |            4 |             6 |              5 | wrench.png      | Jakoavain     | Pieni jakoavain           |      0.01 | Kelvoton      | K▒sity▒kalut     | Lahti        | 2019-04-20 14:06:44       | NULL          |
+--------+-------------+--------------+---------------+----------------+-----------------+---------------+---------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
```

### Palautetaan työkalu omistajalleen
```sql
UPDATE transaction SET actualEndDate = CURRENT_TIMESTAMP WHERE transactionID = 4 AND actualEndDate IS NULL;
+---------------+----------------------+---------------------------+-------------+--------------+--------+---------------------+
| transactionID | transactionStartDate | transactionPlannedEndDate | userOwnerID | userLesseeID | toolID | actualEndDate       |
+---------------+----------------------+---------------------------+-------------+--------------+--------+---------------------+
|             4 | 2019-04-10 14:06:44  | 2019-04-15 14:06:44       |           1 |            2 |      6 | 2019-04-10 17:22:33 |
|             5 | 2019-04-10 14:06:44  | 2019-04-17 14:06:44       |           2 |            1 |      7 | NULL                |
|             6 | 2019-04-10 14:06:44  | 2019-04-20 14:06:44       |           3 |            4 |      8 | NULL                |
|             7 | 2019-04-10 14:31:17  | 2019-04-22 14:31:17       |           4 |            1 |      9 | NULL                |
+---------------+----------------------+---------------------------+-------------+--------------+--------+---------------------+
```

### Lisätään muutama kommettia

```sql
INSERT INTO comment (commentDate, commentText, userID, commentParentID, toolID)
VALUES (CURRENT_TIMESTAMP, 'Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industrys standard dummy text ever since the 1500s', 1, null, 6),
(CURRENT_TIMESTAMP, 'when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries', 2, null, 6),
(CURRENT_TIMESTAMP,'It is a long established fact that a reader will be distracted by the readable content of a page when looking at its layout. The point of using Lorem Ipsum is that it has a more-or-less normal distribution of letters', 3,1,6);

+-----------+---------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------+-----------------+--------+
| commentID | commentDate         | commentText                                                                                                                                                                                                              | userID | commentParentID | toolID |
+-----------+---------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------+-----------------+--------+
|         1 | 2019-04-10 17:43:25 | Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industrys standard dummy text ever since the 1500s                                                                   |      1 |            NULL |      6 |
|         2 | 2019-04-10 17:45:42 | Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industrys standard dummy text ever since the 1500s                                                                   |      1 |            NULL |      6 |
|         3 | 2019-04-10 17:45:42 | when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries                                                                                     |      2 |            NULL |      6 |
|         4 | 2019-04-10 17:45:42 | It is a long established fact that a reader will be distracted by the readable content of a page when looking at its layout. The point of using Lorem Ipsum is that it has a more-or-less normal distribution of letters |      3 |               1 |      6 |
+-----------+---------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------+-----------------+--------+
```

### Haetaan käyttäjän 1 kaikki omistamat työkalut, jotka on lainassa

```sql
SELECT * FROM rented_tools WHERE userOwnerID = 1;
+--------+-------------+--------------+---------------+----------------+-------------+----------+---------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
| toolID | userOwnerID | userLesseeID | transactionID | toolCategoryID | toolPicture | toolName | toolDescription     | toolPrice | toolCondition | toolCategoryName | userLocation | transactionPlannedEndDate | actualEndDate |
+--------+-------------+--------------+---------------+----------------+-------------+----------+---------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
|     12 |           1 |            3 |             9 |              5 |             | Kirves   | Hyv▒ kirves saatana |      2.00 | Pristine      | K▒sity▒kalut     | Jyv▒skyl▒    | 2019-04-14 17:36:13       | NULL          |
+--------+-------------+--------------+---------------+----------------+-------------+----------+---------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
```

### Haetaan käyttäjän kaikki työkalut, joita käyttäjä 1 on vuokrannut

```sql
SELECT * FROM rented_tools WHERE userLesseeID = 1;
+--------+-------------+--------------+---------------+----------------+------------------+---------------+-------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
| toolID | userOwnerID | userLesseeID | transactionID | toolCategoryID | toolPicture      | toolName      | toolDescription               | toolPrice | toolCondition | toolCategoryName | userLocation | transactionPlannedEndDate | actualEndDate |
+--------+-------------+--------------+---------------+----------------+------------------+---------------+-------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
|      7 |          10 |            1 |             5 |              5 | screwdriver.png  | Ruuvimeisseli | Talttap▒inen iso meisseli     |      0.30 | Uudenveroinen | K▒sity▒kalut     | Vaasa        | 2019-04-17 14:06:44       | NULL          |
|      9 |           4 |            1 |             7 |             14 | escrewdriver.png | Ruuviv▒▒nnin  | S▒hk▒ll▒ toimiva ruuviv▒▒nnin |      3.00 | Hyv▒          | S▒hk▒ty▒kalut    | Oulu         | 2019-04-22 14:31:17       | NULL          |
+--------+-------------+--------------+---------------+----------------+------------------+---------------+-------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
```

### Haetaan kaikki Rovanniemellä saatavilla olevat työkalut

```sql
SELECT toolName, toolCondition, toolPrice, toolCategoryName, all_tools.userLocation as 'toolLocation', concat(userName,' ', userSurname) as 'owner' FROM all_tools INNER JOIN user ON userOwnerID = userID WHERE all_tools.userLocation = 'Rovanniemi';
+--------------+---------------+-----------+-----------------------------+--------------+---------------+
| toolName     | toolCondition | toolPrice | toolCategoryName            | toolLocation | owner         |
+--------------+---------------+-----------+-----------------------------+--------------+---------------+
| Rakennuskyn▒ | Ok            |     13.30 | Juottaminen                 | Rovanniemi   | Lwfzhp Dbzmob |
| Miekka       | Poor          |     19.60 | K▒sity▒kalut                | Rovanniemi   | Lwfzhp Dbzmob |
| Kirves       | Good          |     20.60 | S▒hk▒ty▒kalut               | Rovanniemi   | Lwfzhp Dbzmob |
| Kirves       | Pristine      |      1.30 | S▒hk▒ty▒kalut               | Rovanniemi   | Mylkyc Ymaeai |
| Miekka       | Ok            |     20.70 | Leikkuuter▒t                | Rovanniemi   | Mylkyc Ymaeai |
| Porakone     | Good          |      8.90 | Hitsauskoneet ja tarvikkeet | Rovanniemi   | Mylkyc Ymaeai |
| Saha         | Ok            |      6.70 | Ty▒kalujen s▒ilytt▒minen    | Rovanniemi   | Mylkyc Ymaeai |
| Pensseli     | Poor          |     10.10 | Poranter▒t                  | Rovanniemi   | Mylkyc Ymaeai |
+--------------+---------------+-----------+-----------------------------+--------------+---------------+
```