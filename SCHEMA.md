
### SQL Script for Schema and Initial Data

```sql
--------------------------------------------------
--
-- DDL Script for MariaDB 
--
--------------------------------------------------
CREATE DATABASE IF NOT EXISTS `eth`
  DEFAULT CHARACTER SET = 'utf8mb4'
  DEFAULT COLLATE = 'utf8mb4_unicode_ci';

USE `eth`;

--------------------------------------------------
-- Table `chain` 
--------------------------------------------------
CREATE TABLE IF NOT EXISTS `chain` (
  `id` INT NOT NULL COMMENT 'chain ID',
  `name` VARCHAR(200) NOT NULL COMMENT 'chain name',
  `json_rpc` VARCHAR(300) NULL COMMENT 'JSON-RPC endpoint for this chain',
  `explorer_url` VARCHAR(300) NULL COMMENT 'block explorer URL for this chain',
  `is_valid` ENUM('Y', 'N') NOT NULL DEFAULT 'Y' COMMENT 'whether or not this chain is valid',
  `descr` TEXT NULL COMMENT 'description for this chain',
  PRIMARY KEY (`id`))
ENGINE = InnoDB
DEFAULT CHARACTER SET = utf8mb4
COLLATE = utf8mb4_unicode_ci;

--------------------------------------------------
-- Table `eth_acct` 
--------------------------------------------------
CREATE TABLE IF NOT EXISTS `eth_acct` (
  `chain_id` INT NOT NULL COMMENT 'Ethereum network',
  `addr` CHAR(42) NOT NULL COMMENT 'account address in 40 length hexadecimal with ‘0x’ prefix',
  `pub_key` VARCHAR(130) NULL COMMENT 'public key in 128 length hexadecimal (for 64 bytes) with ‘0x‘ prefix',
  `veiled_prv_key` VARCHAR(1000) NULL COMMENT 'encrypted private key',
  `is_valid` ENUM('Y', 'N') NOT NULL DEFAULT 'Y' COMMENT 'whether or not this account is valid',
  `created_at` TIMESTAMP NULL COMMENT 'when this account is opened',
  `invalid_at` TIMESTAMP NULL COMMENT 'when this account is invalidated',
  PRIMARY KEY (`chain_id`, `addr`),
  INDEX `eth_acct_idx1` (`created_at` ASC) VISIBLE,
  CONSTRAINT `eth_acct_fk1`
    FOREIGN KEY (`chain_id`)
    REFERENCES `chain` (`id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB
DEFAULT CHARACTER SET = utf8mb4
COLLATE = utf8mb4_unicode_ci
COMMENT = 'Ethereum account - EOA or proxy address';

ALTER TABLE `eth_acct` 
ADD CONSTRAINT `eth_acct_chk_addr` CHECK(`addr` RLIKE '^(?-i)0x[0-9a-f]+$');

```