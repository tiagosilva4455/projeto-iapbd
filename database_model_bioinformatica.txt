-- MySQL Workbench Forward Engineering

SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0;
SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0;
SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';

-- -----------------------------------------------------
-- Schema Project_TJM
-- -----------------------------------------------------

-- -----------------------------------------------------
-- Schema Project_TJM
-- -----------------------------------------------------
CREATE SCHEMA IF NOT EXISTS `Project_TJM` DEFAULT CHARACTER SET utf8 ;
-- -----------------------------------------------------
-- Schema Project_TJM
-- -----------------------------------------------------

-- -----------------------------------------------------
-- Table `Project_TJM`.`LOCUS`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `Project_TJM`.`LOCUS` (
  `Id_locus` INT NOT NULL AUTO_INCREMENT,
  `Accession` VARCHAR(100) NOT NULL,
  `Sequence_length` INT NULL,
  `Molecule_type` VARCHAR(20) NULL,
  `GB_division` VARCHAR(10) NULL,
  `Source` VARCHAR(200) NULL,
  `Organism` VARCHAR(200) NULL,
  `Locus_name` VARCHAR(45) NULL,
  PRIMARY KEY (`Id_locus`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `Project_TJM`.`ORIGIN`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `Project_TJM`.`ORIGIN` (
  `Id_origin` INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
  `Length` INT NULL,
  `Adenine` INT NULL,
  `Citosine` INT NULL,
  `Timine` INT NULL,
  `Guanine` INT NULL,
  `Sequence` LONGTEXT NULL)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `Project_TJM`.`GENBANK`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `Project_TJM`.`GENBANK` (
  `Id_GB` INT NOT NULL AUTO_INCREMENT,
  `GB_id` VARCHAR(45) NOT NULL,
  `Modification_date` VARCHAR(12) NULL,
  `Definition` VARCHAR(200) NULL,
  `Version` VARCHAR(20) NULL,
  `LOCUS_Id_locus` INT NOT NULL,
  `ORIGIN_Id_origin` INT NULL,
  PRIMARY KEY (`Id_GB`),
  INDEX `fk_GENBANK_LOCUS1_idx` (`LOCUS_Id_locus` ASC),
  INDEX `fk_GENBANK_ORIGIN1_idx` (`ORIGIN_Id_origin` ASC),
  CONSTRAINT `fk_GENBANK_LOCUS1`
    FOREIGN KEY (`LOCUS_Id_locus`)
    REFERENCES `Project_TJM`.`LOCUS` (`Id_locus`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_GENBANK_ORIGIN1`
    FOREIGN KEY (`ORIGIN_Id_origin`)
    REFERENCES `Project_TJM`.`ORIGIN` (`Id_origin`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `Project_TJM`.`FEATURES`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `Project_TJM`.`FEATURES` (
  `Id_F` INT NOT NULL AUTO_INCREMENT,
  `Gene` VARCHAR(45) NULL,
  `Intron` VARCHAR(45) NULL,
  `Source_location` VARCHAR(45) NULL,
  `Exon` VARCHAR(45) NULL,
  `Regulatory` VARCHAR(45) NULL,
  PRIMARY KEY (`Id_F`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `Project_TJM`.`CDS`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `Project_TJM`.`CDS` (
  `Id_CDS` INT NOT NULL AUTO_INCREMENT,
  `Translation_seq` LONGTEXT NULL,
  `Location` VARCHAR(45) NULL,
  `Product` VARCHAR(45) NULL,
  `Protein_ID` VARCHAR(45) NULL,
  PRIMARY KEY (`Id_CDS`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `Project_TJM`.`PUBMED`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `Project_TJM`.`PUBMED` (
  `Id_PM` INT NOT NULL AUTO_INCREMENT,
  `PM_ID` INT NOT NULL,
  `Journal` VARCHAR(100) NULL,
  `Title` VARCHAR(500) NULL,
  `Abstract` LONGTEXT NULL,
  PRIMARY KEY (`Id_PM`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `Project_TJM`.`AFFILIATION`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `Project_TJM`.`AFFILIATION` (
  `Id_affiliation` INT NOT NULL AUTO_INCREMENT,
  `Description` LONGTEXT NULL,
  PRIMARY KEY (`Id_affiliation`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `Project_TJM`.`AUTHORS`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `Project_TJM`.`AUTHORS` (
  `Id_Author` INT NOT NULL AUTO_INCREMENT,
  `Name` VARCHAR(200) NULL,
  `AFFILIATION_Id_affiliation` INT NULL,
  PRIMARY KEY (`Id_Author`),
  INDEX `fk_AUTHORS_AFFILIATION1_idx` (`AFFILIATION_Id_affiliation` ASC),
  CONSTRAINT `fk_AUTHORS_AFFILIATION1`
    FOREIGN KEY (`AFFILIATION_Id_affiliation`)
    REFERENCES `Project_TJM`.`AFFILIATION` (`Id_affiliation`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `Project_TJM`.`PM_GB_relationship`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `Project_TJM`.`PM_GB_relationship` (
  `PUBMED_Id_PM` INT NOT NULL,
  `GENBANK_Id_GB` INT NOT NULL,
  PRIMARY KEY (`PUBMED_Id_PM`, `GENBANK_Id_GB`),
  INDEX `fk_PUBMED_has_GENBANK_GENBANK1_idx` (`GENBANK_Id_GB` ASC),
  INDEX `fk_PUBMED_has_GENBANK_PUBMED1_idx` (`PUBMED_Id_PM` ASC),
  CONSTRAINT `fk_PUBMED_has_GENBANK_PUBMED1`
    FOREIGN KEY (`PUBMED_Id_PM`)
    REFERENCES `Project_TJM`.`PUBMED` (`Id_PM`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_PUBMED_has_GENBANK_GENBANK1`
    FOREIGN KEY (`GENBANK_Id_GB`)
    REFERENCES `Project_TJM`.`GENBANK` (`Id_GB`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `Project_TJM`.`PM_AUTHORS_relationship`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `Project_TJM`.`PM_AUTHORS_relationship` (
  `PUBMED_Id_PM` INT NOT NULL,
  `AUTHORS_Id_Author` INT NOT NULL,
  PRIMARY KEY (`PUBMED_Id_PM`, `AUTHORS_Id_Author`),
  INDEX `fk_PUBMED_has_AUTHORS_AUTHORS1_idx` (`AUTHORS_Id_Author` ASC),
  INDEX `fk_PUBMED_has_AUTHORS_PUBMED1_idx` (`PUBMED_Id_PM` ASC),
  CONSTRAINT `fk_PUBMED_has_AUTHORS_PUBMED1`
    FOREIGN KEY (`PUBMED_Id_PM`)
    REFERENCES `Project_TJM`.`PUBMED` (`Id_PM`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_PUBMED_has_AUTHORS_AUTHORS1`
    FOREIGN KEY (`AUTHORS_Id_Author`)
    REFERENCES `Project_TJM`.`AUTHORS` (`Id_Author`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `Project_TJM`.`GB_FT_relationship`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `Project_TJM`.`GB_FT_relationship` (
  `GENBANK_Id_GB` INT NOT NULL,
  `FEATURES_Id_F` INT NOT NULL,
  PRIMARY KEY (`GENBANK_Id_GB`, `FEATURES_Id_F`),
  INDEX `fk_GENBANK_has_FEATURES_FEATURES1_idx` (`FEATURES_Id_F` ASC),
  INDEX `fk_GENBANK_has_FEATURES_GENBANK1_idx` (`GENBANK_Id_GB` ASC),
  CONSTRAINT `fk_GENBANK_has_FEATURES_GENBANK1`
    FOREIGN KEY (`GENBANK_Id_GB`)
    REFERENCES `Project_TJM`.`GENBANK` (`Id_GB`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_GENBANK_has_FEATURES_FEATURES1`
    FOREIGN KEY (`FEATURES_Id_F`)
    REFERENCES `Project_TJM`.`FEATURES` (`Id_F`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `Project_TJM`.`CDS_GB_relationship`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `Project_TJM`.`CDS_GB_relationship` (
  `CDS_Id_CDS` INT NOT NULL,
  `GENBANK_Id_GB` INT NOT NULL,
  PRIMARY KEY (`CDS_Id_CDS`, `GENBANK_Id_GB`),
  INDEX `fk_CDS_has_GENBANK_GENBANK1_idx` (`GENBANK_Id_GB` ASC),
  INDEX `fk_CDS_has_GENBANK_CDS1_idx` (`CDS_Id_CDS` ASC),
  CONSTRAINT `fk_CDS_has_GENBANK_CDS1`
    FOREIGN KEY (`CDS_Id_CDS`)
    REFERENCES `Project_TJM`.`CDS` (`Id_CDS`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_CDS_has_GENBANK_GENBANK1`
    FOREIGN KEY (`GENBANK_Id_GB`)
    REFERENCES `Project_TJM`.`GENBANK` (`Id_GB`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;

SET SQL_MODE=@OLD_SQL_MODE;
SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS;
SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS;