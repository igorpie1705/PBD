# Kategoria Courses

## Tabela Rooms

Zawiera informacje o salach wykładowych:

- **RoomID** int – klucz główny, identyfikator sali.
- **RoomName** nvarchar(64) – nazwa sali.
- **PlaceLimit** int – maksymalna liczba miejsc w sali.

```sql
-- Table: Rooms
CREATE TABLE Rooms (
    RoomID int NOT NULL,
    RoomName nvarchar(64) NOT NULL,
    PlaceLimit int NOT NULL,
    CONSTRAINT Rooms_pk PRIMARY KEY (RoomID)
);
```

## Tabela StationaryMeeting

Zawiera informacje o spotkaniach stacjonarnych:

- **MeetingID** int – klucz główny, identyfikator spotkania.
- **RoomID** int – identyfikator sali, gdzie odbywa się spotkanie.

```sql
-- Table: StationaryMeeting
CREATE TABLE StationaryMeeting (
    MeetingID int NOT NULL,
    RoomID int NOT NULL,
    CONSTRAINT StationaryMeeting_pk PRIMARY KEY (MeetingID)
);
```

## Tabela OnlineSyncMeeting

Zawiera informacje o spotkaniach online synchronicznych:

- **MeetingID** int – klucz główny, identyfikator spotkania.
- **PlatformID** int – identyfikator platformy.
- **MeetingUrl** nvarchar(128) – link do spotkania.

```sql
-- Table: OnlineSyncMeeting
CREATE TABLE OnlineSyncMeeting (
    MeetingID int NOT NULL,
    PlatformID int NOT NULL,
    MeetingUrl nvarchar(128) NOT NULL,
    CONSTRAINT OnlineSyncMeeting_pk PRIMARY KEY (MeetingID)
);
```

## Tabela OnlineAsyncMeeting

Zawiera informacje o spotkaniach online asynchronicznych:

- **MeetingID** int – klucz główny, identyfikator spotkania.
- **RecordingUrl** nvarchar(128) – link do nagrania wideo.

```sql
-- Table: OnlineAsyncMeeting
CREATE TABLE OnlineAsyncMeeting (
    MeetingID int NOT NULL,
    RecordingUrl nvarchar(128) NOT NULL,
    CONSTRAINT OnlineAsyncMeeting_pk PRIMARY KEY (MeetingID)
);
```

## Tabela StudiesMeeting

Zawiera informacje o spotkaniach w ramach studiów:

- **MeetingID** int – klucz główny, identyfikator spotkania.
- **SubjectID** int – identyfikator przedmiotu.
- **TeacherID** int – identyfikator nauczyciela.
- **MeetingName** nvarchar(64) – nazwa spotkania.
- **MeetingPrice** money – cena za uczestnictwo.
- **MeetingDate** datetime – data spotkania.
- **MeetingDuration** time(0) – czas trwania spotkania.
- **TranslatorID** int (nullable) – identyfikator tłumacza.
- **LanguageID** int (nullable) – identyfikator języka.

```sql
-- Table: StudiesMeeting
CREATE TABLE StudiesMeeting (
    MeetingID int NOT NULL,
    SubjectID int NOT NULL,
    TeacherID int NOT NULL,
    MeetingName nvarchar(64) NOT NULL,
    MeetingPrice money NOT NULL,
    MeetingDate datetime NOT NULL,
    MeetingDuration time(0) NOT NULL,
    TranslatorID int NULL,
    LanguageID int NULL,
    CONSTRAINT StudiesMeeting_pk PRIMARY KEY (MeetingID)
);
```

## Tabela MeetingDetails

Zawiera szczegóły dotyczące obecności studentów na spotkaniach:

- **MeetingID** int – klucz główny, identyfikator spotkania.
- **StudentID** int – identyfikator studenta.
- **Presence** bit – obecność studenta (1 = obecny, 0 = nieobecny).

```sql
-- Table: MeetingDetails
CREATE TABLE MeetingDetails (
    MeetingID int NOT NULL,
    StudentID int NOT NULL,
    Presence bit NOT NULL,
    CONSTRAINT MeetingDetails_pk PRIMARY KEY (MeetingID, StudentID)
);
```

## Tabela Studies

Zawiera informacje o kierunkach studiów:

- **StudieID** int – klucz główny, identyfikator kierunku.
- **StudieName** nvarchar(64) – nazwa kierunku.
- **StudieDescription** nvarchar(128) – opis kierunku.
- **StudieEntryFee** money – opłata za wstęp.
- **StudieCoordinatorID** int – identyfikator koordynatora kierunku.
- **PlaceLimit** int – limit miejsc na kierunku.

```sql
-- Table: Studies
CREATE TABLE Studies (
    StudieID int NOT NULL,
    StudieName nvarchar(64) NOT NULL,
    StudieDescription nvarchar(128) NOT NULL,
    StudieEntryFee money NOT NULL,
    StudieCoordinatorID int NOT NULL,
    PlaceLimit int NOT NULL,
    CONSTRAINT Studies_pk PRIMARY KEY (StudieID)
);
```

## Tabela StudyDetails

Zawiera szczegóły dotyczące studentów na kierunkach:

- **StudieID** int – klucz główny, identyfikator kierunku.
- **StudentID** int – identyfikator studenta.
- **StudieGrade** int – ocena studenta.

```sql
-- Table: StudyDetails
CREATE TABLE StudyDetails (
    StudieID int NOT NULL,
    StudentID int NOT NULL,
    StudieGrade int NOT NULL,
    CONSTRAINT StudyDetails_pk PRIMARY KEY (StudieID, StudentID)
);
```

## Tabela Subjects

Zawiera informacje o przedmiotach:

- **SubjectID** int – klucz główny, identyfikator przedmiotu.
- **StudieID** int – identyfikator kierunku.
- **SubjectName** nvarchar(64) – nazwa przedmiotu.
- **SubjectDescription** nvarchar(128) – opis przedmiotu.
- **CoordinatorID** int – identyfikator koordynatora przedmiotu.

```sql
-- Table: Subjects
CREATE TABLE Subjects (
    SubjectID int NOT NULL,
    StudieID int NOT NULL,
    SubjectName nvarchar(64) NOT NULL,
    SubjectDescription nvarchar(128) NOT NULL,
    CoordinatorID int NOT NULL,
    CONSTRAINT Subjects_pk PRIMARY KEY (SubjectID)
);
```

## Tabela SubjectDetails

Zawiera szczegóły dotyczące ocen studentów z przedmiotów:

- **SubjectID** int – klucz główny, identyfikator przedmiotu.
- **StudentID** int – identyfikator studenta.
- **SubjectGrade** int – ocena studenta.

```sql
-- Table: SubjectDetails
CREATE TABLE SubjectDetails (
    SubjectID int NOT NULL,
    StudentID int NOT NULL,
    SubjectGrade int NOT NULL,
    CONSTRAINT SubjectDetails_pk PRIMARY KEY (SubjectID, StudentID)
);
```

## Tabela Internships

Zawiera informacje o praktykach:

- **InternshipID** int – klucz główny, identyfikator praktyki.
- **StudieID** int – identyfikator kierunku.
- **InternshipDuration** int – czas trwania praktyki (w dniach).
- **InternshipStartDate** datetime – data rozpoczęcia praktyki.

```sql
-- Table: Internships
CREATE TABLE Internships (
    InternshipID int NOT NULL,
    StudieID int NOT NULL,
    InternshipDuration int NOT NULL,
    InternshipStartDate datetime NOT NULL,
    CONSTRAINT Internships_pk PRIMARY KEY (InternshipID)
);
```

## Tabela InternshipDetails

Zawiera szczegóły dotyczące obecności studentów na praktykach:

- **InternshipID** int – klucz główny, identyfikator praktyki.
- **StudentID** int – identyfikator studenta.
- **DaysAttended** int – liczba dni obecności na praktyce.

```sql
-- Table: InternshipDetails
CREATE TABLE InternshipDetails (
    InternshipID int NOT NULL,
    StudentID int NOT NULL,
    DaysAttended int NOT NULL,
    CONSTRAINT InternshipDetails_pk PRIMARY KEY (InternshipID, StudentID)
);
```
