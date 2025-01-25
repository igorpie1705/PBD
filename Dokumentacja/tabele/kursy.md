# Kategoria Courses

## Tabela Courses

Zawiera informacje o kursach:

- **CourseID** int – klucz główny, identyfikator kursu
- **CourseName** nvarchar(64) – nazwa kursu
- **CourseDescription** nvarchar(128) – opis kursu
- **CourseLength** int – czas trwania kursu w dniach
- **CoursePrice** decimal(10, 2) – cena kursu
- **CourseCoordinatorID** int – identyfikator koordynatora kursu

```sql
-- Table: Courses
CREATE TABLE Courses (
    CourseID int NOT NULL,
    CourseName nvarchar(64) NOT NULL,
    CourseDescription nvarchar(128) NOT NULL,
    CourseLength int NOT NULL,
    CoursePrice decimal(10, 2) NOT NULL,
    CourseCoordinatorID int NOT NULL,
    CONSTRAINT Courses_pk PRIMARY KEY (CourseID)
);
```

## Tabela CourseDetails

Zawiera listę studentów zapisanych na dany kurs:

- **CourseID** int – część klucza głównego, identyfikator kursu
- **StudentID** int – część klucza głównego, identyfikator studenta

```sql
-- Table: CourseDetails
CREATE TABLE CourseDetails (
    CourseID int NOT NULL,
    StudentID int NOT NULL,
    CONSTRAINT CourseDetails_pk PRIMARY KEY (CourseID, StudentID)
);
```

## Tabela CourseModules

Zawiera informacje o zajęciach w ramach kursu:

- **ModuleID** int – klucz główny, identyfikator zajęć
- **ModuleName** nvarchar(64) – nazwa modułu
- **ModuleDescription** nvarchar(128) – opis modułu
- **ModuleDate** datetime – data rozpoczęcia zajęć
- **ModuleDuration** nvarchar(20) – czas trwania zajęć
- **TeacherID** int – identyfikator nauczyciela prowadzącego zajęcia
- **TranslatorID** int – identyfikator tłumacza
- **LanguageID** int – identyfikator języka

```sql
-- Table: CourseModules
CREATE TABLE CourseModules (
    ModuleID int NOT NULL,
    ModuleName nvarchar(64) NOT NULL,
    ModuleDescription nvarchar(128) NOT NULL,
    ModuleDate datetime NOT NULL,
    ModuleDuration nvarchar(20) NOT NULL,
    TeacherID int NOT NULL,
    TranslatorID int NOT NULL,
    LanguageID int NOT NULL,
    CONSTRAINT CourseModules_pk PRIMARY KEY (ModuleID)
);
```

## Tabela OnlineAsyncModule

Zawiera informacje o modułach prowadzonych online w trybie asynchronicznym:

- **ModuleID** int – klucz główny, identyfikator modułu
- **RecordingUrl** nvarchar(128) – link do nagrania wideo dla uczestników

```sql
-- Table: OnlineAsyncModule
CREATE TABLE OnlineAsyncModule (
    ModuleID int NOT NULL,
    RecordingUrl nvarchar(128) NOT NULL,
    CONSTRAINT OnlineAsyncModule_pk PRIMARY KEY (ModuleID)
);
```

## Tabela StationaryModule

Zawiera informacje o modułach stacjonarnych:

- **ModuleID** int – klucz główny, identyfikator modułu
- **RoomID** int – identyfikator pokoju, w którym odbywa się moduł

```sql
-- Table: StationaryModule
CREATE TABLE StationaryModule (
    ModuleID int NOT NULL,
    RoomID int NOT NULL,
    CONSTRAINT StationaryModule_pk PRIMARY KEY (ModuleID)
);
```

## Tabela OnlineSyncModule

Zawiera informacje o modułach prowadzonych online w trybie synchronicznym:

- **ModuleID** int – klucz główny, identyfikator modułu
- **PlatformID** int – identyfikator platformy używanej do prowadzenia zajęć
- **RecordingUrl** nvarchar(128) – link do nagrania wideo dla uczestników

```sql
-- Table: OnlineSyncModule
CREATE TABLE OnlineSyncModule (
    ModuleID int NOT NULL,
    PlatformID int NOT NULL,
    RecordingUrl nvarchar(128) NOT NULL,
    CONSTRAINT OnlineSyncModule_pk PRIMARY KEY (ModuleID)
);
```
