### Initial Table Creation Queries
##### We should keep these updated so we have a reference on how to implement tables for deployment purposes also a reference to use if we don't have access to the database for some reason
---

#### item_list
```
CREATE TABLE `InvManSys`.`item_list` (
  `item_code` CHAR(8) NOT NULL COMMENT 'Primary Key\nWill be Used to select item information',
  `item_name` VARCHAR(30) NOT NULL COMMENT 'Item names must have names, but they do not have to be unique (not recommended)',
  `item_desc` TEXT NULL COMMENT 'Default Description of item; and is not necessary',
  `type_code` CHAR(8) NOT NULL COMMENT 'Used to index items from inventory list.\nNOT NULL but \"None\" Type should exist in case item is too general',
  `package_code` CHAR(8) NOT NULL COMMENT 'How this item is packaged.\nSelected from a pre-set values\nNOT NULL',
  PRIMARY KEY (`item_code`),
  UNIQUE INDEX `item_code_UNIQUE` (`item_code` ASC))
COMMENT = 'List of Items available for Purchase,Sale,Transfer';
```   
#### warehouse_list
```
CREATE TABLE `InvManSys`.`warehouse_list` (
  `warehouse_code` CHAR(8) NOT NULL COMMENT 'Primary Key for Warehouses',
  `warehouse_name` VARCHAR(30) NOT NULL COMMENT 'NOT NULL\nName of Warehouse (Warehouse 2, Cold Storage, etc.)',
  `warehouse_desc` TEXT NULL COMMENT 'Optional Default Description of Warehouse',
  `is_freezer` TINYINT NOT NULL DEFAULT 0 COMMENT 'Does this warehouse house frozen goods?\nNeeds to be kept track of for laws and regulations regarding food safety and maintenance regulation',
  `is_outdoor` TINYINT NOT NULL DEFAULT 0 COMMENT 'are the contents of this warehouse subject to outdoor conditions\n\nCannot be selected with is_freezer',
  PRIMARY KEY (`warehouse_code`),
  UNIQUE INDEX `warehouse_code_UNIQUE` (`warehouse_code` ASC))
COMMENT = 'List of warehouses items can be stored at in inventory';
```   
#### inventory
```
CREATE TABLE `InvManSys`.`inventory` (
  `item_code` CHAR(8) NOT NULL COMMENT 'Same as item_list',
  `warehouse_code` CHAR(8) NOT NULL COMMENT 'Same as warehouse_list\nComposite Key w/ lot_code',
  `lot_code` CHAR(10) NOT NULL COMMENT 'Unique Identifier for this lot',
  `quantity_held` INT NOT NULL COMMENT 'current amount held and available',
  `vendor_code` CHAR(8) NOT NULL COMMENT 'same as vendor_list',
  `cost` DOUBLE NOT NULL COMMENT 'Cost per item of this lot',
  `quantity_commited` INT NOT NULL DEFAULT 0 COMMENT 'Amount from lot commited to sales order',
  `initial_quantity` INT NOT NULL COMMENT 'Initial Amount obtained (store for AVG)',
  PRIMARY KEY (`warehouse_code`, `lot_code`))
COMMENT = 'Actual Contents of inventory.\n\nWill need specific views \n\nComposite key is to ensure lots that split and move to a separate warehouse will still be valid';
```   
#### vendor_list
```
CREATE TABLE `InvManSys`.`vendor_list` (
  `vendor_code` CHAR(8) NOT NULL,
  `vendor_name` VARCHAR(30) NULL,
  `vendor_address` VARCHAR(30) NULL,
  `vendor_phone` INT(11) NULL COMMENT 'only store numbers, smaller than string of chars, and 11 characters because of country code(US is 1+(856)692-0000 )',
  `vendor_fax` INT(11) NULL COMMENT 'only store numbers, smaller than string of chars, and 11 characters because of country code(US is 1+(856)692-0000 )',
  `vendor_email` VARCHAR(45) NULL,
  PRIMARY KEY (`vendor_code`))
COMMENT = 'Information on specific vendors\n\nThis table is pretty self explanatory so minimal documentation';
```   
#### customer_list
```
CREATE TABLE `InvManSys`.`customer_list` (
`customer_code` CHAR(8) NOT NULL,
  `customer_name` VARCHAR(30) NULL,
  `customer_address` VARCHAR(30) NULL,
  `customer_phone` INT(11) NULL COMMENT 'only store numbers, smaller than string of chars, and 11 characters because of country code(US is 1+(856)692-0000 )',
  `customer_fax` INT(11) NULL COMMENT 'only store numbers, smaller than string of chars, and 11 characters because of country code(US is 1+(856)692-0000 )',
  `customer_email` VARCHAR(45) NULL,
  PRIMARY KEY (`customer_code`))
COMMENT = 'Information on specific vendors\n\nThis table is pretty self explanatory so minimal documentation';
```   
#### purchase_order_header
```
CREATE TABLE `InvManSys`.`purchase_order_header` (
  `po_code` CHAR(8) NOT NULL,
  `vendor_code` CHAR(8) NOT NULL,
  `date` DATE NOT NULL,
  `time` TIME NOT NULL,
  `quantity_on_order` INT NOT NULL DEFAULT 0,
  `item_count` INT NOT NULL DEFAULT 0 COMMENT 'Count of unique item codes on order',
  `subtotal` DOUBLE NOT NULL DEFAULT 0,
  `tax` DOUBLE NOT NULL DEFAULT 0,
  `total` DOUBLE NOT NULL DEFAULT 0 COMMENT 'May drop calculating fields for views intead',
  PRIMARY KEY (`po_code`),
  UNIQUE INDEX `po_code_UNIQUE` (`po_code` ASC))
COMMENT = 'Header informatino for Purchase Orders\nNone null because things need to be tracked for recall/regulations';
```   
#### purchase_order_detail
```
CREATE TABLE `InvManSys`.`purchase_order_detail` (
  `line_key` INT NOT NULL,
  `item_code` CHAR(8) NOT NULL,
  `name_override` VARCHAR(30) NULL COMMENT 'Override default name',
  `desc_override` TEXT NULL COMMENT 'override default description',
  `quantity_ordered` INT NOT NULL DEFAULT 0,
  `price` DOUBLE NOT NULL DEFAULT 0,
  `subtotal` DOUBLE NULL DEFAULT 0,
  `tax_code` CHAR(8) NULL COMMENT 'tax_code to find ',
  `tax` DOUBLE NULL DEFAULT 0,
  `total` DOUBLE NULL DEFAULT 0,
  PRIMARY KEY (`line_key`, `item_code`),
  UNIQUE INDEX `line_key_UNIQUE` (`line_key` ASC))
COMMENT = 'Aggregate of all lines in all purchase orders. Views indexed by po_code/line key';
```   
#### sales_order_header
```
CREATE TABLE `InvManSys`.`sales_order_header` (
  `so_code` CHAR(8) NOT NULL,
  `vendor_code` CHAR(8) NOT NULL,
  `date` DATE NOT NULL,
  `time` TIME NOT NULL,
  `quantity_ordered` INT NOT NULL,
  `item_count` INT NOT NULL,
  `subtotal` DOUBLE NOT NULL DEFAULT 0,
  `tax` DOUBLE NOT NULL DEFAULT 0,
  `total` DOUBLE NOT NULL DEFAULT 0,
  PRIMARY KEY (`so_code`),
  UNIQUE INDEX `so_code_UNIQUE` (`so_code` ASC))
COMMENT = 'Header information for sales orders	';
```   
#### sales_order_detail
```
CREATE TABLE `InvManSys`.`sales_order_detail` (
  `line_key` CHAR(3) NOT NULL,
  `item_code` CHAR(8) NOT NULL,
  `name_override` VARCHAR(30) NULL,
  `desc_override` TEXT NULL,
  `quantity_ordered` INT NOT NULL,
  `price` DOUBLE NOT NULL,
  `sub` DOUBLE NOT NULL,
  `tax_code` CHAR(3) NOT NULL,
  `total` DOUBLE NOT NULL,
  PRIMARY KEY (`line_key`));
  ```   
  #### receipt_of_goods_header
  ```
CREATE TABLE `InvManSys`.`receipt_of_goods_header` (
  `receipt_code` CHAR(8) NOT NULL,
  `po_code` CHAR(8) NULL,
  `status` CHAR(1) NULL,
  `vendor_code` CHAR(8) NULL,
  `date` DATE NULL,
  `time` TIME NULL,
  PRIMARY KEY (`receipt of good`))
COMMENT = 'Header for ROG after PO has been claimed';
```   
#### receipt_of_goods_detail
```
CREATE TABLE `InvManSys`.`receipt_of_goods_detail` (
  `receipt_code` CHAR(8) NOT NULL,
  `line_key` CHAR(3) NOT NULL,
  `lot_code` CHAR(10) NOT NULL,
  `quantity` INT NOT NULL,
  `price` DOUBLE NOT NULL DEFAULT 0,
  `tax_code` CHAR(3) NOT NULL,
  `tax` DOUBLE NOT NULL DEFAULT 0,
  `subtotal` DOUBLE NOT NULL DEFAULT 0,
  `total` DOUBLE NOT NULL DEFAULT 0,
  PRIMARY KEY (`receipt_code`, `line_key`));
```   
#### sale_shipping_header
```
CREATE TABLE `InvManSys`.`sale_shipping_header` (
  `sale_ship_code` CHAR(8) NOT NULL,
  `so_code` CHAR(8) NOT NULL COMMENT 'Sales Order Code',
  `cust_code` CHAR(8) NOT NULL,
  `shipped_quantity` INT NULL,
  `warehouse_code` CHAR(8) NOT NULL COMMENT 'Which Warehouse is this portion of the order coming from?',
  PRIMARY KEY (`sale_ship_code`, `warehouse_code`, `so_code`))
COMMENT = 'Contains header information for SO being Shipped';
```   
#### sale_shipping_detail
```
CREATE TABLE `InvManSys`.`sale_ship_detail` (
  `sale_ship_code` CHAR(8) NOT NULL,
  `line_key` CHAR(3) NOT NULL,
  `lot_code` CHAR(10) NOT NULL,
  `shipped_quantity` INT NULL,
  `shipped_price` DOUBLE NULL,
  `shipped_tax` DOUBLE NULL,
  `shipped_total` DOUBLE NULL,
  PRIMARY KEY (`sale_ship_code`, `line_key`, `lot_code`))
COMMENT = 'Aggregate details of all shipping jobs';
```   
#### transfer_header
```
CREATE TABLE `InvManSys`.`transfer_header` (
  `transfer_code` INT NOT NULL,
  `quantity` INT NULL,
  `from_warehouse_code` CHAR(8) NULL,
  `to_warehouse_code` CHAR(8) NULL,
  `date` DATE NULL,
  `time` TIME NULL,
  PRIMARY KEY (`transfer_code`))
COMMENT = 'Header information for warehouse transfers';
```   
#### transfer_detail
```
CREATE TABLE `InvManSys`.`transfer_detail` (
  `transfer_code` CHAR(8) NOT NULL,
  `line_key` CHAR(3) NOT NULL,
  `item_code` CHAR(8) NOT NULL,
  `lot_code` CHAR(10) NOT NULL,
  `transfer_quantity` INT NOT NULL,
  `is_frozen` TINYINT NOT NULL DEFAULT 0,
  `is_outside` TINYINT NOT NULL DEFAULT 0,
  PRIMARY KEY (`transfer_code`, `line_key`, `lot_code`))
COMMENT = 'aggregate details of all transfers';
```   
#### tax_table
```
CREATE TABLE `InvManSys`.`tax_table` (
  `tax_code` CHAR(8) NOT NULL,
  `tax_rate` DOUBLE NOT NULL COMMENT 'Percentage of tax',
  `tax_name` VARCHAR(30) NOT NULL COMMENT 'Name of tax code',
  `tax_desc` TEXT COMMENT 'Explanation of tax_code',
  PRIMARY KEY(`tax_code`))
COMMENT = 'List of specified tax rates to be added by user as seen fit';
```   
#### product_type
```
CREATE TABLE `InvManSys`.`product_type` (
  `type_code` CHAR(4) NOT NULL,
  `type_name` VARCHAR(30) NULL,
  `type_desc` TEXT NULL,
  `default_tax_code` CHAR(8) NULL,
  PRIMARY KEY (`type_code`))
COMMENT = 'List of product types';
```   
#### country_code
```
CREATE TABLE `InvManSys`.`country_code` (
  `country_code` CHAR(3) NOT NULL,
  `country_name` VARCHAR(3) NOT NULL,
  `country_description` TEXT,
  PRIMARY KEY(`country_code`))
COMMENT = "List of country specifications on items in storage. Needed for regulation";
```   
#### transfer_shipping_header
```
CREATE TABLE `InvManSys`.`transfer_shipping_header` (
  `transfer_ship_code` CHAR(8) NOT NULL,
  `transfer_code` CHAR(8) NULL,
  `from_warehouse_code` CHAR(8) NULL,
  `to_warehouse_code` CHAR(8) NULL,
  `date` DATE NULL,
  `time` TIME NULL,
  PRIMARY KEY (`transfer_ship_code`));
```   
#### transfer_shipping_detail
```
CREATE TABLE `InvManSys`.`transfer_shipping_detail` (
  `transfer_ship_code` CHAR(8) NOT NULL,
  `transfer_code` CHAR(8) NOT NULL,
  `line_key` VARCHAR(45) NOT NULL,
  `item_code` VARCHAR(45) NULL,
  `lot_code` VARCHAR(45) NOT NULL,
  `transfer_quantity` INT NULL,
  `is_frozen` TINYINT NULL,
  PRIMARY KEY (`transfer_ship_code`, `line_key`, `lot_code`, `transfer_code`));
```   
####








