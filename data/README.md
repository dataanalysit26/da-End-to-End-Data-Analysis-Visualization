## 📊 Dataset Information

The dataset used in this project was sourced from Kaggle and contains transactional data from an e-commerce platform.

**Dataset Source:**
https://kaggle.com/datasets/carrie1/ecommerce-data

The dataset consists of **541,910 transaction records** and **8 columns**, representing customer purchases, product details, and geographic sales distribution.

### Dataset Structure

| Column Name           | Description                               |
| --------------------- | ----------------------------------------- |
| InvoiceNo             | Unique identifier for each transaction    |
| StockCode             | Product code used to identify each item   |
| Description           | Description of the purchased product      |
| Quantity              | Number of items purchased per transaction |
| InvoiceDate           | Date and time of the transaction          |
| UnitPrice             | Price per unit of the product             |
| CustomerID            | Unique identifier for each customer       |
| Country               | Country where the transaction occurred    |

## 🧹 Data Cleaning Process

To ensure analytical accuracy and eliminate inconsistencies within the dataset, several data cleaning and preprocessing steps were performed.

### 1. Data Normalization & Case Sensitivity

* **TRIM** was applied to the `StockCode` and `Description` fields to remove leading and trailing spaces (e.g., preventing duplicates such as `"White Mug"`).
* **LOWER** was applied to text fields to ensure consistency and prevent regex or query mismatches caused by case sensitivity.
* **TO_TEXT** was applied to `InvoiceNo` and `StockCode` to prevent automatic type interpretation issues during Google Sheets **QUERY** operations.

### 2. Handling Product Returns

Within the dataset, returned orders were identified by invoices starting with the letter **"C"**.

However, several scenarios were observed during data validation:

* Some customers purchased multiple items and returned **only a portion of the order** (e.g., buying 10 units and returning 2).
* Removing all negative quantity values would therefore introduce **calculation errors**.

Key considerations:

* If a customer purchased and returned the **same quantity**, the net revenue impact would naturally be **zero**, so such transactions do not distort total revenue calculations.
* Partial returns required additional validation.

To manage this scenario:

* A **Boolean TRUE/FALSE flag** was created in the raw dataset.
* Transactions were grouped by **CustomerID and StockCode**.
* Quantities were aggregated and rows with totals **greater than zero were marked as TRUE**.

Due to the large dataset size, this calculation was performed **once**, and results were pasted as **static values** to improve performance.

> **Special Note:**
> In similar datasets, discounted purchases (e.g., buying 10 items but being charged for 8) may create discrepancies during return transactions. Such scenarios require additional validation when performing revenue calculations.

### 3. Invalid Data Filtering

Transactions were filtered using **regex conditions** applied within QUERY operations.

Filters applied:

**StockCode exclusions**

```
post | d | c2 | dot | m | bank charges | samples | amazonfee
```

**Description exclusions**

```
adjust bad debt | bank charges | samples | amazonfee
```

Additional filtering rules:

* `UnitPrice > 0` was enforced.
* Rows representing **stock adjustments, damaged items, test entries, or manual corrections** were removed.

Although some `UnitPrice = 0` records may represent **promotions or giveaways**, these were excluded as their impact on the overall dataset was observed to be minimal.

These excluded rows typically represent:

* Inventory movements
* Erroneous entries
* Manual tests
* Damaged product adjustments
* Promotional or free shipments

Because these records are not explicitly labeled, conducting precise analytical evaluation would be unreliable.

### 4. Missing Customer ID Handling

Rows with missing `CustomerID` values were reviewed.

It was observed that these transactions still represent **valid purchases**, likely caused by:

* Customers completing purchases without creating an account
* System-related identification issues

To ensure these transactions were not excluded from the analysis, missing values were replaced with the label:

```
guest
```

This allowed the transactions to remain part of the dataset while still being distinguishable during customer-level analysis.
