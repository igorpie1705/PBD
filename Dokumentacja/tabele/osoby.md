# Kategoria People

## Tabela Employees

Tabela zawierająca pracowników:

- **EmployeeID** int - klucz główny, identyfikator pracownika.
- **Email** nvarchar(64) - adres email.
- **Password** nvarchar(64) - zaszyfrowane hasło.
- **FirstName** nvarchar(64) - imię.
- **LastName** nvarchar(64) - nazwisko.
- **Address** nvarchar(64) - adres.
- **Phone** nvarchar(16) - nr telefonu.
- **HireDate** date - data przyjęcia pracownika.
- **EmployeeType** int - typ pracownika.

```sql
CREATE TABLE Employees (
   EmployeeID in  NOT NULL CHECK (EmployeeID > 0),
   Email nvarchar(64)  NOT NULL CHECK (email LIKE '%_@__%.__%'),
   Password nvarchar(64)  NOT NULL,
   FirstName nvarchar(64)  NOT NULL,
   LastName nvarchar(64)  NOT NULL,
   Address nvarchar(64)  NOT NULL,
   Phone nvarchar(16)  NOT NULL,
   HireDate date  NOT NULL CHECK (HireDate <= GETDATE()),
   EmployeeType int  NOT NULL CHECK (EmployeeType BETWEEN 1 AND 2),
   CONSTRAINT Employees_pk PRIMARY KEY (EmployeeID)
);
```

## Tabela EmployeeType

Spis rodzajów pracowników

- **EmployeeType** int - klucz głowny, identyfikator typu pracownika
- **EmployeeName** nvarchar(64) - nazwa typu pracownika

```sql
CREATE TABLE EmployeeType (
   EmployeeType int  NOT NULL CHECK (EmployeeType BETWEEN 1 AND 2),
   EmployeeName nvarchar(64)  NOT NULL CHECK (EmployeeName IN ('Teacher', 'Coordinator')),
   CONSTRAINT EmployeeType_pk PRIMARY KEY (EmployeeType)
);
```

## Tabela EmployeeLanguages

Tabela łącząca pracowników z językami, którymi się oni posługują.

- **EmployeeID** int - identyfikator pracownika.
- **LanguageID** int - identyfikator języka.

```sql
CREATE TABLE EmployeeLanguages (
   EmployeeID int  NOT NULL CHECK (EmployeeID > 0),
   LanguageID int  NOT NULL CHECK (LanguageID > 0),
   CONSTRAINT EmployeeLanguages_pk PRIMARY KEY (EmployeeID,LanguageID)
);
```

## Tabela Language

Spis wszystkich języków.

- **LanguageID** int - klucz główny, identyfikator języka.
- **LanguageName** nvarchar(64) - nazwa języka.

```sql
CREATE TABLE Language (
   LanguageID int  NOT NULL CHECK (LanguageID > 0),
   LanguageName nvarchar(64)  NOT NULL,
   CONSTRAINT Language_pk PRIMARY KEY (LanguageID)
);
```

## Tabela Translators

Lista obecnych tłumaczy.

- **TranslatorID** int - klucz główny, identyfikator tłumacza.
- **Email** nvarchar(64) - adres email.
- **Password** nvarchar(64) - zaszyfrowane hasło.
- **FirstName** nvarchar(64) - imię.
- **LastName** nvarchar(64) - nazwisko.
- **Address** nvarchar(64) - adres.
- **Phone** nvarchar(16) - nr telefonu.
- **HireDate** date - data przyjęcia.

```sql
CREATE TABLE Translators (
   TranslatorID int  NOT NULL CHECK (TranslatorID > 0),
   Email nvarchar(64)  NOT NULL CHECK (email LIKE '%_@__%.__%'),
   Password nvarchar(64)  NOT NULL,
   FirstName nvarchar(64)  NOT NULL,
   LastName nvarchar(64)  NOT NULL,
   Address nvarchar(64)  NOT NULL,
   Phone nvarchar(16)  NOT NULL,
   HireDate date  NOT NULL CHECK (HireDate <= GETDATE()),
   CONSTRAINT Translators_pk PRIMARY KEY (TranslatorID)
);
```


## Tabela Students

Tabela ze studentami.

- **StudentID** int - klucz głowny, identyfikator studenta.
- **Email** nvarchar(64) - adres email.
- **Password** nvarchar(64) - zaszyfrowane hasło.
- **FirstName** nvarchar(64) - imię.
- **LastName** nvarchar(64) - nazwisko.
- **Address** nvarchar(64) - adres.
- **City** nvarchar(64) - miasto.
- **Country** nvarchar(64) - państwo.

```sql
CREATE TABLE Students (
   StudentID int  NOT NULL CHECK (StudentID > 0),
   Email nvarchar(64)  NOT NULL CHECK (email LIKE '%_@__%.__%'),
   Password nvarchar(64)  NOT NULL,
   FirstName nvarchar(64)  NOT NULL,
   LastName nvarchar(64)  NOT NULL,
   Address nvarchar(64)  NOT NULL,
   City nvarchar(64)  NOT NULL,
   Country nvarchar(64)  NOT NULL,
   CONSTRAINT Students_pk PRIMARY KEY (StudentID)
);
```

## Tabela ModulesPassed

Tabela trzymająca informacje o ukończonych modułach kursów.

- **StudentID** - identyfikator studenta.
- **ModuleID** - identyfikator modułu kursu.

```sql
CREATE TABLE ModulesPassed (
   StudentID int  NOT NULL CHECK (StudentID > 0),
   ModuleID int  NOT NULL CHECK (ModuleID > 0),
   CONSTRAINT ModulesPassed_pk PRIMARY KEY (StudentID)
);
```
