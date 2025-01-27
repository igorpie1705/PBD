# Kategoria Orders

## Tabela Orders

Zawiera listę zamówienień:

- **OrderID** int – klucz główny, identyfikator zamówienia.
- **OrderDate** date – Data zamówienia.
- **PaymentLink** nvarchar(128) – link do płatności.

```sql
CREATE TABLE Orders (
   OrderID int  NOT NULL,
   OrderDate date  NOT NULL,
   PaymentLink nvarchar(128)  NOT NULL CHECK (Len(PaymentLink) > 0),
   CONSTRAINT Orders_pk PRIMARY KEY (OrderID)
);
```

## Tabela OrderDetails

Zawiera szczegóły dotyczące poszczególnych zamówień:

- **OrderID** int - klucz główny, identyfikator zamówienia.
- **StatusID** int - identyfikator statusu.
- **StudentID** int - identyfikator kupującego.
- **ProductType** int - identyfikator rodzaju nabywanego produktu.
- **ProductID** int - identyfikator produktu w ramach danego typu produktu.
- **Price** money - cena produktu.

```sql
CREATE TABLE OrderDetails (
   OrderID int  NOT NULL,
   StatusID int  NOT NULL,
   StudentID int  NOT NULL,
   ProductType int  NOT NULL COMMENT '''''studies'''', ''''webinar'''', ''''course'''', ''''' CHECK (ProductType IN BETWEEN 0 AND 2),
   ProductID int  NOT NULL,
   Price money  NOT NULL CHECK (Price > 0),
   CONSTRAINT OrderDetails_pk PRIMARY KEY (OrderID)
);
```

## Tabela Statuses

Zawiera opisy poszczególnych statusów

- **StatusID** int - klucz główny, identyfiaktor statusu.
- **Description** text - opis statusu.

```sql
CREATE TABLE Statuses (
   StatusID int  NOT NULL,
   Description text  NOT NULL CHECK (Len(Description) > 0),
   CONSTRAINT Statuses_pk PRIMARY KEY (StatusID)
);
```