 
```sql
 DELIMITER $$
create trigger ratingTrigger before insert on rating for each row
begin 
if new.rating> 5 || new.rating < 1 || (new.raterID = ( select raterID from rating) and new.transactionID = (select transactionID from rating))
then
 CALL `'Wrong input! Rating should be between 1 to 5!'`;
 end if;
 end;
 $$
```