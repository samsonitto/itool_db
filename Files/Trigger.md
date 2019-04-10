### Luodaan triggeri rating-talululle sitä varten, että arvioija ei voisi arvioida itsensä, eikä voi antaa arvioita enemmän kuin kerran/transaktio ja arvio pitää olla asteikolla 1-5


```sql
 DELIMITER $$
create trigger ratingTrigger before insert on rating for each row
begin 
if new.rating> 5 || new.rating < 1 || (new.raterID = ( select raterID from rating) and new.transactionID = (select transactionID from rating)) || new.raterID = new.ratedID
then
 CALL `'Only 1-5! One rating per transaction! Can't rate yourself!'`;
 end if;
 end;
 $$
```

# Testit

## Negatiiviset testit

### Annettu arvio: 8 (ei pitäisi mennä läpi)

```sql
INSERT INTO rating (ratingFeedback, raterID, ratedID, transactionID, rating)
VALUES ('iajdsfoisajhfoisajhfd', 1,2,5,8);
```

TULOS: 15:10:36	INSERT INTO rating (ratingFeedback, raterID, ratedID, transactionID, rating) VALUES ('iajdsfoisajhfoisajhfd', 1,2,5,8)	Error Code: 1305. PROCEDURE M3156_3.'Only 1-5! One rating per transaction! Can't rate yourself!' does not exist	0.015 sec


### Annetaan arvio itselle (ei pitäisi mennä läpi)
```sql
INSERT INTO rating (ratingFeedback, raterID, ratedID, transactionID, rating)
VALUES ('iajdsfoisajhfoisajhfd', 1,1,5,4);
```

TULOS: 15:12:44	INSERT INTO rating (ratingFeedback, raterID, ratedID, transactionID, rating) VALUES ('iajdsfoisajhfoisajhfd', 1,1,5,4)	Error Code: 1305. PROCEDURE M3156_3.'Only 1-5! One rating per transaction! Can't rate yourself!' does not exist	0.015 sec


### Annetaan arvio toisen kerran samalle henkilölle (ei pitäisi mennä läpi)

```sql
INSERT INTO rating (ratingFeedback, raterID, ratedID, transactionID, rating)
VALUES ('iajdsfoisajhfoisajhfd', 1,2,5,5);
```

TULOS: 15:20:39	INSERT INTO rating (ratingFeedback, raterID, ratedID, transactionID, rating) VALUES ('iajdsfoisajhfoisajhfd', 1,2,5,5)	Error Code: 1242. Subquery returns more than 1 row	0.000 sec

## Positiivistet testit

### Annetaan arvio 5 transaktion vastapulelle (pitää mennä läpi)

```sql
INSERT INTO rating (ratingFeedback, raterID, ratedID, transactionID, rating)
VALUES ('iajdsfoisajhfoisajhfd', 1,2,5,5);
```

TULOS: 15:18:14	INSERT INTO rating (ratingFeedback, raterID, ratedID, transactionID, rating) VALUES ('iajdsfoisajhfoisajhfd', 1,2,5,5)	1 row(s) affected	0.000 sec


### Näiden testien jälkeen löysimme ongelmie triggerissä (Error Code: 1242. Subquery returns more than 1 row).


# Uusi trigger (toimii)

```sql
 DELIMITER $$
create trigger ratingTrigger before insert on rating for each row
begin 
if new.rating> 5 || new.rating < 1 || (new.raterID IN ( select raterID from rating) and new.transactionID IN (select transactionID from rating)) || new.raterID = new.ratedID
then
 CALL `'Only 1-5! One rating per transaction! Can't rate yourself!'`;
 end if;
 end;
 $$
 ```
