# iTool loitsuhistoria

```sql
select toolID from transaction where userOwnerID = 2 AND transactionPlannedEndDate >= '2019-03-26 00:00:00';
```
+--------+
| toolID |
+--------+
|      2 |
|      1 |
+--------+
2 rows in set (0.01 sec)

```sql
select userName, toolID from user inner join transaction on UserID = userOwnerID where userOwnerID = 2 AND transactionPlannedEndDate >= '2019-03-26 00:00:00';
```
+----------+--------+
| userName | toolID |
+----------+--------+
| kk       |      2 |
| kk       |      1 |
+----------+--------+
2 rows in set (0.00 sec)

```sql
select userName, userPicture, toolID from user inner join transaction on UserID = userOwnerID where userOwnerID = 2 AND transactionPlannedEndDate >= '2019-03-26 00:00:00';
```
+----------+-------------+--------+
| userName | userPicture | toolID |
+----------+-------------+--------+
| kk       | amanda.jpg  |      2 |
| kk       | amanda.jpg  |      1 |
+----------+-------------+--------+
2 rows in set (0.00 sec)

```sql
select concat(userName,' ',userSurname) as 'Full name', userPicture, toolID from user inner join transaction on UserID = userOwnerID where userOwnerID = 2 AND transactionPlannedEndDate >= '2019-03-26 00:00:00';
```
+-----------+-------------+--------+
| Full name | userPicture | toolID |
+-----------+-------------+--------+
| kk kk     | amanda.jpg  |      2 |
| kk kk     | amanda.jpg  |      1 |
+-----------+-------------+--------+
2 rows in set (0.00 sec)

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
```

+--------+-------------+----------------+-------------+------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
| toolID | userOwnerID | toolCategoryID | toolPicture | toolName         | toolDescription                               | toolPrice | toolCondition | toolCategoryName | userLocation | transactionPlannedEndDate | ActualEndDate |
+--------+-------------+----------------+-------------+------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
|      3 |           2 |              3 | NULL        | Hitachi porakone | 2h akku                                       |      3.00 | hyv▒          | S▒hk▒porakoneet  | Jyv▒skyl▒    | 2019-04-01 15:50:10       | NULL          |
|      4 |           3 |              4 | NULL        | Metallisaha      | ohut                                          |      1.50 | uudenveroinen | Saha             | Lappeenranta | 2019-03-28 15:57:10       | NULL          |
|      6 |          12 |              5 | NULL        | Vasara           | Ihan perus vasara                             |      0.50 | uudenveroinen | Vasarat          | Jgvjqs       | NULL                      | NULL          |
|      7 |          12 |              6 | NULL        | Iso jakoavain    | Isonkokoinen jakoavain, voi käyttää aseena |      0.70 | uudenveroinen | Jakoavaimet      | Jgvjqs       | NULL                      | NULL          |
+--------+-------------+----------------+-------------+------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+

### Luodaan näkymä joka näyttää lainassa olevat työkalut:

```sql
CREATE VIEW rented_tools AS SELECT * FROM all_tools WHERE transactionPlannedEndDate > CURRENT_TIMESTAMP;
```
+--------+-------------+----------------+-------------+------------------+-----------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
| toolID | userOwnerID | toolCategoryID | toolPicture | toolName         | toolDescription | toolPrice | toolCondition | toolCategoryName | userLocation | transactionPlannedEndDate | ActualEndDate |
+--------+-------------+----------------+-------------+------------------+-----------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
|      3 |           2 |              3 | NULL        | Hitachi porakone | 2h akku         |      3.00 | hyv▒          | S▒hk▒porakoneet  | Jyv▒skyl▒    | 2019-04-12 10:50:26       | NULL          |
+--------+-------------+----------------+-------------+------------------+-----------------+-----------+---------------+------------------+--------------+---------------------------+---------------+

### Näytetään kaikki saatavilla olevat työkalut, jos Active.UserID = 1

```sql
SELECT * FROM all_tools WHERE userOwnerID != 1 AND toolID NOT IN (SELECT toolID FROM rented_tools);
```
+--------+-------------+----------------+-------------+---------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
| toolID | userOwnerID | toolCategoryID | toolPicture | toolName      | toolDescription                               | toolPrice | toolCondition | toolCategoryName | userLocation | transactionPlannedEndDate | ActualEndDate |
+--------+-------------+----------------+-------------+---------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
|      4 |           3 |              4 | NULL        | Metallisaha   | ohut                                          |      1.50 | uudenveroinen | Saha             | Lappeenranta | 2019-03-28 15:57:10       | NULL          |
|      6 |          12 |              5 | NULL        | Vasara        | Ihan perus vasara                             |      0.50 | uudenveroinen | Vasarat          | Jgvjqs       | NULL                      | NULL          |
|      7 |          12 |              6 | NULL        | Iso jakoavain | Isonkokoinen jakoavain, voi käyttää aseena |      0.70 | uudenveroinen | Jakoavaimet      | Jgvjqs       | NULL                      | NULL          |
+--------+-------------+----------------+-------------+---------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+

### Lisätään työkaluja kuvien kanssa

```sql
INSERT INTO tool (toolName, toolDescription, toolPrice, toolCondition, toolCategoryID, userOwnerID, toolPicture)
VALUES ('Vasara','Iso vasara',1,'Hyvä',11,11, 'hammer.png'),
('Ruuvimeisseli','Talttapäinen iso meisseli',0.3,'Uudenveroinen',11,10,'screwdriver.png'),
('Jakoavain','Pieni jakoavain',0.01,'Kelvoton',11,12,'wrench.png'),
('Ruuviväännin','Sähköllä toimiva ruuviväännin',3,'Hyvä',20,4,'escrewdriver.png'),
('Porakone','Perus porakone',2.5,'Uudenveroinen',20,9,'drill.png');
```

### Poistetaan näkymä all_tools ja luodaan uudestaan all_tools, joka sisälttää userLesseeID:

```sql
CREATE VIEW all_tools AS SELECT tool.toolID, tool.userOwnerID, transaction.userLesseeID, tool.toolCategoryID, toolPicture, toolName, toolDescription, toolPrice, toolCondition, toolCategoryName, userLocation, transactionPlannedEndDate, ActualEndDate FROM tool INNER JOIN toolCategory ON tool.toolCategoryID = toolCategory.toolCategoryID LEFT JOIN transaction ON tool.toolID = transaction.toolID LEFT JOIN tr_completion ON transaction.transactionID = tr_completion.transactionID INNER JOIN user ON tool.userOwnerID = userID;
```
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

### Poistetaan näkymä rented_tools ja luodaan uudestaan rented_tools, joka sisältää userLesseeID:

```sql
CREATE VIEW rented_tools AS SELECT * FROM all_tools WHERE transactionPlannedEndDate > CURRENT_TIMESTAMP;
```
+--------+-------------+--------------+----------------+-------------+------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
| toolID | userOwnerID | userLesseeID | toolCategoryID | toolPicture | toolName         | toolDescription                               | toolPrice | toolCondition | toolCategoryName | userLocation | transactionPlannedEndDate | ActualEndDate |
+--------+-------------+--------------+----------------+-------------+------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+
|      3 |           2 |            1 |              3 | NULL        | Hitachi porakone | 2h akku                                       |      3.00 | hyv▒          | S▒hk▒porakoneet  | Jyv▒skyl▒    | 2019-04-12 10:50:26       | NULL          |
|      6 |          12 |            1 |              5 | NULL        | Vasara           | Ihan perus vasara                             |      0.50 | uudenveroinen | Vasarat          | Jgvjqs       | 2019-04-10 14:00:34       | NULL          |
|      7 |          12 |            1 |              6 | NULL        | Iso jakoavain    | Isonkokoinen jakoavain, voi käyttää aseena |      0.70 | uudenveroinen | Jakoavaimet      | Jgvjqs       | 2019-04-14 14:08:27       | NULL          |
+--------+-------------+--------------+----------------+-------------+------------------+-----------------------------------------------+-----------+---------------+------------------+--------------+---------------------------+---------------+

### Poistetaan näkymä rented_tools ja luodaan uudestaan rented_tools, joka sisältää myös ei palautettuja ajallaan työkaluja:

```sql
CREATE VIEW rented_tools AS SELECT * FROM all_tools WHERE (transactionPlannedEndDate IS NOT NULL AND ActualEndDate IS NULL);
```
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

### Poistetaan näkymä all_tools ja luodaan uusi näkymä all_tools, joka sisältää kaikkien vanhojen kenttien lisäksi transactionID:

```sql
CREATE VIEW all_tools AS SELECT tool.toolID, tool.userOwnerID, transaction.userLesseeID, transaction.transactionID, tool.toolCategoryID, toolPicture, toolName, toolDescription, toolPrice, toolCondition, toolCategoryName, userLocation, transactionPlannedEndDate, ActualEndDate FROM tool INNER JOIN toolCategory ON tool.toolCategoryID = toolCategory.toolCategoryID LEFT JOIN transaction ON tool.toolID = transaction.toolID LEFT JOIN tr_completion ON transaction.transactionID = tr_completion.transactionID INNER JOIN user ON tool.userOwnerID = userID;
```
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

Päivitetään rented_tools näkymä, luomalla se uudestaan. Näkymässä näkyy nyt transactionID vanhojen ominaisuuksien lisäksi.

