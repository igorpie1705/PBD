# Kategoria Webinars

## Tabela WebinarAccess

Zawiera informacje o dostępie studentów do webinarów:

- **WebinarID** int – klucz główny, identyfikator webinaru
- **StudentID** int – identyfikator studenta
- **HasAccess** bit – informacja, czy student ma dostęp do webinaru

```sql
-- Table: WebinarAccess
CREATE TABLE WebinarAccess (
    WebinarID int NOT NULL,
    StudentID int NOT NULL,
    HasAccess bit NOT NULL,
    CONSTRAINT WebinarAccess_pk PRIMARY KEY (WebinarID, StudentID)
);
```

## Tabela Platforms

Zawiera informacje o platformach wykorzystywanych do webinarów:

- **PlatformID** int – klucz główny, identyfikator platformy
- **PlatformName** nvarchar(64) – nazwa platformy

```sql
-- Table: Platforms
CREATE TABLE Platforms (
    PlatformID int NOT NULL,
    PlatformName nvarchar(64) NOT NULL,
    CONSTRAINT Platforms_pk PRIMARY KEY (PlatformID)
);
```

## Tabela Webinars

Zawiera informacje o webinarach:

- **WebinarID** int – klucz główny, identyfikator webinaru
- **PlatformID** int – identyfikator platformy
- **TeacherID** int – identyfikator nauczyciela
- **WebinarName** nvarchar(64) – nazwa webinaru
- **WebinarDescription** nvarchar(128) – opis webinaru
- **WebinarPrice** decimal(10, 2) – cena webinaru
- **WebinarStartDate** datetime – data rozpoczęcia webinaru
- **IsFree** bit – informacja, czy webinar jest darmowy

```sql
-- Table: Webinars
CREATE TABLE Webinars (
    WebinarID int NOT NULL,
    PlatformID int NOT NULL,
    TeacherID int NOT NULL,
    WebinarName nvarchar(64) NOT NULL,
    WebinarDescription nvarchar(128) NULL,
    WebinarPrice decimal(10, 2) NOT NULL,
    WebinarStartDate datetime NOT NULL,
    IsFree bit NOT NULL,
    CONSTRAINT Webinars_pk PRIMARY KEY (WebinarID)
);
```

## Tabela WebinarDetails

Zawiera szczegóły webinarów, takie jak nagrania i daty ważności:

- **WebinarID** int – klucz główny, identyfikator webinaru
- **ExpireDate** datetime – data wygaśnięcia webinaru
- **RecordingUrl** nvarchar(128) – link do nagrania webinaru
- **Deleted** bit – informacja, czy webinar został usunięty

```sql
-- Table: WebinarDetails
CREATE TABLE WebinarDetails (
    WebinarID int NOT NULL,
    ExpireDate datetime NOT NULL,
    RecordingUrl nvarchar(128) NOT NULL,
    Deleted bit NOT NULL,
    CONSTRAINT WebinarDetails_pk PRIMARY KEY (WebinarID)
);
```
