```sql
-- MySQL Workbench Forward Engineering

SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0;
SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0;
SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='TRADITIONAL,ALLOW_INVALID_DATES';

-- -----------------------------------------------------
-- Schema M3156_3
-- -----------------------------------------------------

-- -----------------------------------------------------
-- Schema M3156_3
-- -----------------------------------------------------
CREATE SCHEMA IF NOT EXISTS `M3156_3` DEFAULT CHARACTER SET utf8 ;  --Luo scheman M3156_3, jos sellaista ei vielä ole 
USE `M3156_3` ;

-- -----------------------------------------------------
-- Table `M3156_3`.`user`                                           --Taulun 'user' luonti
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `M3156_3`.`user` (                       --Sarakkeiden määritys user-tauluun
  `userID` INT NOT NULL AUTO_INCREMENT,                             --NOT NULL: userID ei saa olla tyhjä (NULL), AUTO_INCREMENT: sarake täyttyy automaattisesti sen mukaan kun lisätään uusia usereita
  `userName` VARCHAR(45) NOT NULL,                                  --VARCHAR(45): userName on 45 merkin pituinen maksimissaan merkkijono 
  `userSurname` VARCHAR(45) NOT NULL,
  `userAddress` VARCHAR(128) NOT NULL,
  `userEmail` VARCHAR(128) NOT NULL,
  `userLocation` VARCHAR(128) NOT NULL,
  `paymentMethod` VARCHAR(128) NOT NULL,
  `userMobile` VARCHAR(45) NOT NULL,
  `userPassword` VARCHAR(45) NOT NULL,
  `userPicture` VARCHAR(128) NULL,                                  --NULL: userPicture ei ola pakko antaa uusille usereille
  PRIMARY KEY (`userID`))                                           --PRIMARY KEY: userID on taulun pääavain, sen avulla voi viitata muista taulusta
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `M3156_3`.`toolCategory`                                   --Taulun 'toolCategory' luonti
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `M3156_3`.`toolCategory` (               --Sarakkeiden määritys toolCategory-tauluun
  `toolCategoryID` INT NOT NULL AUTO_INCREMENT,                     --INT: ainoastaan kokonaislukuja
  `toolCategoryName` VARCHAR(128) NOT NULL,
  `toolCategoryDescription` VARCHAR(1000) NOT NULL,
  PRIMARY KEY (`toolCategoryID`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `M3156_3`.`tool`                                           --Taulun 'tool' luonti
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `M3156_3`.`tool` (                       --Sarakkeiden määritys tool-tauluun
  `toolID` INT NOT NULL AUTO_INCREMENT,
  `toolName` VARCHAR(128) NOT NULL,
  `toolDescription` VARCHAR(1000) NOT NULL,
  `toolPrice` DECIMAL(10,2) NOT NULL,                               --DECIMAL(10,2): 10 numeron pituinen maksimissaan desimaaliluku, näytetään 2 desimaalin tarkkuudella
  `toolCondition` VARCHAR(45) NOT NULL,
  `toolCategoryID` INT NOT NULL,
  `userOwnerID` INT NOT NULL,
  `toolPicture` VARCHAR(128) NULL,
  PRIMARY KEY (`toolID`),
  INDEX `fk_tool_toolCategory1_idx` (`toolCategoryID` ASC),
  INDEX `fk_tool_user1_idx` (`userOwnerID` ASC),
  CONSTRAINT `fk_tool_toolCategory1`
    FOREIGN KEY (`toolCategoryID`)                                  --toolCategoryID: viiteavain, viittaa "toolCategory"-taulun primääriavaimeen
    REFERENCES `M3156_3`.`toolCategory` (`toolCategoryID`)
    ON DELETE NO ACTION                                             --viiteavainta ei voi poistaa
    ON UPDATE NO ACTION,                                            --viiteavainta ei voi päivittää
  CONSTRAINT `fk_tool_user1`
    FOREIGN KEY (`userOwnerID`)
    REFERENCES `M3156_3`.`user` (`userID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `M3156_3`.`transaction`                                    --Taulun 'transaction' luonti
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `M3156_3`.`transaction` (                --Sarakkeiden määritys transaction-tauluun
  `transactionID` INT NOT NULL AUTO_INCREMENT,
  `transactionStartDate` DATETIME NOT NULL,                         --DATETIME: päivämäärä tyyppinen sarake
  `transactionPlannedEndDate` DATETIME NOT NULL,
  `userOwnerID` INT NOT NULL,
  `userLesseeID` INT NOT NULL,
  `toolID` INT NOT NULL,
  `actualEndDate` DATETIME NULL,
  PRIMARY KEY (`transactionID`),
  INDEX `fk_transaction_user1_idx` (`userOwnerID` ASC),
  INDEX `fk_transaction_user2_idx` (`userLesseeID` ASC),
  INDEX `fk_transaction_tool1_idx` (`toolID` ASC),
  CONSTRAINT `fk_transaction_user1`
    FOREIGN KEY (`userOwnerID`)
    REFERENCES `M3156_3`.`user` (`userID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_transaction_user2`
    FOREIGN KEY (`userLesseeID`)
    REFERENCES `M3156_3`.`user` (`userID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_transaction_tool1`
    FOREIGN KEY (`toolID`)
    REFERENCES `M3156_3`.`tool` (`toolID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `M3156_3`.`comment`                                        --Taulun 'comment' luonti
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `M3156_3`.`comment` (                    --Sarakkeiden määritys comment-tauluun
  `commentID` INT NOT NULL AUTO_INCREMENT,
  `commentDate` DATETIME NOT NULL,
  `commentText` VARCHAR(1000) NULL,
  `userID` INT NOT NULL,
  `commentParentID` INT NULL,
  `toolID` INT NOT NULL,
  PRIMARY KEY (`commentID`),
  INDEX `fk_return_tr_user1_idx` (`userID` ASC),
  INDEX `fk_return_tr_return_tr1_idx` (`commentParentID` ASC),
  INDEX `fk_comment_tool1_idx` (`toolID` ASC),
  CONSTRAINT `fk_return_tr_user1`
    FOREIGN KEY (`userID`)
    REFERENCES `M3156_3`.`user` (`userID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_return_tr_return_tr1`
    FOREIGN KEY (`commentParentID`)
    REFERENCES `M3156_3`.`comment` (`commentID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_comment_tool1`
    FOREIGN KEY (`toolID`)
    REFERENCES `M3156_3`.`tool` (`toolID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `M3156_3`.`rating`                                         --Taulun 'rating' luonti
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `M3156_3`.`rating` (                     --Sarakkeiden määritys rating-tauluun
  `ratingID` INT NOT NULL AUTO_INCREMENT,
  `ratingFeedback` VARCHAR(1000) NULL,
  `raterID` INT NOT NULL,
  `ratedID` INT NOT NULL,
  `transactionID` INT NOT NULL,
  PRIMARY KEY (`ratingID`),
  INDEX `fk_rating_user1_idx` (`raterID` ASC),
  INDEX `fk_rating_user2_idx` (`ratedID` ASC),
  INDEX `fk_rating_transaction1_idx` (`transactionID` ASC),
  CONSTRAINT `fk_rating_user1`
    FOREIGN KEY (`raterID`)
    REFERENCES `M3156_3`.`user` (`userID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_rating_user2`
    FOREIGN KEY (`ratedID`)
    REFERENCES `M3156_3`.`user` (`userID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_rating_transaction1`
    FOREIGN KEY (`transactionID`)
    REFERENCES `M3156_3`.`transaction` (`transactionID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


SET SQL_MODE=@OLD_SQL_MODE;
SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS;
SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS;
```