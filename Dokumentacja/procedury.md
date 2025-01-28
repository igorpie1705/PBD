# Procedury SQL

## Dodanie webinaru

```sql
CREATE PROCEDURE AddWebinar
    @WebinarID INT,
    @PlatformID INT,
    @TeacherID INT,
    @WebinarName NVARCHAR(64),
    @WebinarDescription varchar(128),
    @WebinarPrice DECIMAL(10, 2),
    @WebinarStartDate DATETIME,
    @IsFree BIT
AS
BEGIN
    BEGIN TRY
        -- Sprawdzenie istnienia nauczyciela
        IF NOT EXISTS (SELECT 1 FROM Employees WHERE EmployeeID = @TeacherID)
        BEGIN
            RAISERROR('Nie znaleziono nauczyciela.', 16, 1);
        END

        -- Wstawianie nowego webinaru
        SET IDENTITY_INSERT Webinars ON;
        INSERT INTO Webinars (WebinarID, PlatformID, TeacherID, WebinarName,
                              WebinarDescription, WebinarPrice, WebinarStartDate,
                              IsFree)
        VALUES (@WebinarID, @PlatformID, @TeacherID, @WebinarName,
                @WebinarDescription, @WebinarPrice, @WebinarStartDate,
                @IsFree);
        SET IDENTITY_INSERT Webinars OFF;

        -- Komunikat o powodzeniu
        PRINT N'Webinar został pomyślnie dodany.';
    END TRY
    BEGIN CATCH
        -- Obsługa błędów
        DECLARE @ErrorMessage NVARCHAR(4000) = ERROR_MESSAGE();
        DECLARE @ErrorSeverity INT = ERROR_SEVERITY();
        DECLARE @ErrorState INT = ERROR_STATE();
        RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
GO
```

## Usunięcie webinaru

```sql
CREATE PROCEDURE DeleteWebinar
    @WebinarID INT
AS
BEGIN
    BEGIN TRY
        -- Sprawdzenie istnienia webinaru
        IF NOT EXISTS (SELECT 1 FROM Webinars WHERE WebinarID = @WebinarID)
        BEGIN
            RAISERROR(N'Nie znaleziono webinaru do usunięcia.', 16, 1);
        END

        -- Usunięcie webinaru
        DELETE FROM Webinars
        WHERE WebinarID = @WebinarID;

        -- Komunikat o powodzeniu
        PRINT N'Webinar został pomyślnie usunięty.';
    END TRY
    BEGIN CATCH
        -- Obsługa błędów
        DECLARE @ErrorMessage NVARCHAR(4000) = ERROR_MESSAGE();
        DECLARE @ErrorSeverity INT = ERROR_SEVERITY();
        DECLARE @ErrorState INT = ERROR_STATE();
        RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
GO
```

## Edytowanie szczegółów webinaru

```sql
CREATE PROCEDURE UpdateWebinar
    @WebinarID INT,
    @PlatformID INT = NULL,
    @TeacherID INT = NULL,
    @WebinarName NVARCHAR(64) = NULL,
    @WebinarDescription VARCHAR(128) = NULL,
    @WebinarPrice DECIMAL(10, 2) = NULL,
    @WebinarStartDate DATETIME = NULL,
    @IsFree BIT = NULL
AS
BEGIN
    BEGIN TRY
        -- Sprawdzenie istnienia webinaru
        IF NOT EXISTS (SELECT 1 FROM Webinars WHERE WebinarID = @WebinarID)
        BEGIN
            RAISERROR('Nie znaleziono webinaru do edycji.', 16, 1);
        END

        -- Aktualizacja szczegółów webinaru
        UPDATE Webinars
        SET
            PlatformID = ISNULL(@PlatformID, PlatformID),
            TeacherID = ISNULL(@TeacherID, TeacherID),
            WebinarName = ISNULL(@WebinarName, WebinarName),
            WebinarDescription = ISNULL(@WebinarDescription, WebinarDescription),
            WebinarPrice = ISNULL(@WebinarPrice, WebinarPrice),
            WebinarStartDate = ISNULL(@WebinarStartDate, WebinarStartDate),
            IsFree = ISNULL(@IsFree, IsFree)
        WHERE WebinarID = @WebinarID;

        -- Komunikat o powodzeniu
        PRINT N'Szczegóły webinaru zostały pomyślnie zaktualizowane.';
    END TRY
    BEGIN CATCH
        -- Obsługa błędów
        DECLARE @ErrorMessage NVARCHAR(4000) = ERROR_MESSAGE();
        DECLARE @ErrorSeverity INT = ERROR_SEVERITY();
        DECLARE @ErrorState INT = ERROR_STATE();
        RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
GO
```

## Dodanie nowego kursu

```sql
CREATE PROCEDURE [dbo].[AddCourse]
    @CourseID INT,
    @CourseName NVARCHAR(64),
    @CourseDescription NVARCHAR(128),
    @CourseLength INT,
    @CoursePrice DECIMAL(10, 2),
    @CourseCoordinatorID INT
AS
BEGIN
    BEGIN TRY
        -- Sprawdzenie istnienia koordynatora
        IF NOT EXISTS (
            SELECT 1
            FROM Employees e
            INNER JOIN dbo.EmployeeType et ON e.EmployeeType = et.EmployeeType
            WHERE e.EmployeeID = @CourseCoordinatorID
              AND et.EmployeeName LIKE 'Coordinator'
        )
        BEGIN
            RAISERROR('Koordynator o podanym ID nie istnieje.', 16, 1);
            RETURN;
        END

        -- Wstawianie nowego kursu
        SET IDENTITY_INSERT Courses ON;
        INSERT INTO Courses (CourseID, CourseName, CourseDescription, CourseLength, CoursePrice, CourseCoordinatorID)
        VALUES (@CourseID, @CourseName, @CourseDescription, @CourseLength, @CoursePrice, @CourseCoordinatorID);
        SET IDENTITY_INSERT Courses OFF;

        -- Komunikat o powodzeniu
        PRINT N'Kurs został pomyślnie dodany.';
    END TRY
    BEGIN CATCH
        -- Obsługa błędów
        DECLARE @ErrorMessage NVARCHAR(4000) = ERROR_MESSAGE();
        DECLARE @ErrorSeverity INT = ERROR_SEVERITY();
        DECLARE @ErrorState INT = ERROR_STATE();
        RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
GO
```

## Usunięcie kursu

```sql
CREATE PROCEDURE [dbo].[DeleteCourse]
    @CourseID INT
AS
BEGIN
    BEGIN TRY
        -- Sprawdzenie istnienia kursu
        IF NOT EXISTS (SELECT 1 FROM Courses WHERE CourseID = @CourseID)
        BEGIN
            RAISERROR('Kurs o podanym ID nie istnieje.', 16, 1);
            RETURN;
        END

        -- Usuwanie kursu
        DELETE FROM Courses
        WHERE CourseID = @CourseID;

        -- Komunikat o powodzeniu
        PRINT N'Kurs został pomyślnie usunięty.';
    END TRY
    BEGIN CATCH
        -- Obsługa błędów
        DECLARE @ErrorMessage NVARCHAR(4000) = ERROR_MESSAGE();
        DECLARE @ErrorSeverity INT = ERROR_SEVERITY();
        DECLARE @ErrorState INT = ERROR_STATE();
        RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
GO
```

## Edytowanie kursu

```sql
CREATE PROCEDURE [dbo].[UpdateCourse]
    @CourseID INT,
    @CourseName NVARCHAR(64) = NULL,
    @CourseDescription NVARCHAR(128) = NULL,
    @CourseLength INT = NULL,
    @CoursePrice DECIMAL(10, 2) = NULL,
    @CourseCoordinatorID INT = NULL
AS
BEGIN
    BEGIN TRY
        -- Sprawdzenie istnienia kursu
        IF NOT EXISTS (SELECT 1 FROM Courses WHERE CourseID = @CourseID)
        BEGIN
            RAISERROR('Kurs o podanym ID nie istnieje.', 16, 1);
            RETURN;
        END

        -- Sprawdzenie istnienia koordynatora (jeśli podano)
        IF @CourseCoordinatorID IS NOT NULL AND NOT EXISTS (
            SELECT 1
            FROM Employees e
            INNER JOIN dbo.EmployeeType et ON e.EmployeeType = et.EmployeeType
            WHERE e.EmployeeID = @CourseCoordinatorID
              AND et.EmployeeName LIKE 'Coordinator'
        )
        BEGIN
            RAISERROR('Koordynator o podanym ID nie istnieje.', 16, 1);
            RETURN;
        END

        -- Aktualizacja szczegółów kursu
        UPDATE Courses
        SET
            CourseName = ISNULL(@CourseName, CourseName),
            CourseDescription = ISNULL(@CourseDescription, CourseDescription),
            CourseLength = ISNULL(@CourseLength, CourseLength),
            CoursePrice = ISNULL(@CoursePrice, CoursePrice),
            CourseCoordinatorID = ISNULL(@CourseCoordinatorID, CourseCoordinatorID)
        WHERE CourseID = @CourseID;

        -- Komunikat o powodzeniu
        PRINT N'Szczegóły kursu zostały pomyślnie zaktualizowane.';
    END TRY
    BEGIN CATCH
        -- Obsługa błędów
        DECLARE @ErrorMessage NVARCHAR(4000) = ERROR_MESSAGE();
        DECLARE @ErrorSeverity INT = ERROR_SEVERITY();
        DECLARE @ErrorState INT = ERROR_STATE();
        RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
GO
```

## Dodanie modułu do kursu

```sql
CREATE PROCEDURE [dbo].[AddCourseModule]
    @CourseID INT,
    @ModuleName NVARCHAR(64),
    @ModuleDescription NVARCHAR(128),
    @ModuleDate DATETIME,
    @ModuleDuration NVARCHAR(20),
    @TeacherID INT,
    @TranslatorID INT = NULL,
    @LanguageID INT
AS
BEGIN
    BEGIN TRY
        -- Sprawdzenie istnienia kursu
        IF NOT EXISTS (SELECT 1 FROM Courses WHERE CourseID = @CourseID)
        BEGIN
            RAISERROR('Kurs o podanym ID nie istnieje.', 16, 1);
        END

        -- Sprawdzenie istnienia nauczyciela
        IF NOT EXISTS (SELECT 1 FROM Employees WHERE EmployeeID = @TeacherID)
        BEGIN
            RAISERROR('Nauczyciel o podanym ID nie istnieje.', 16, 1);
            RETURN;
        END

        -- Sprawdzenie istnienia tłumacza (jeśli podano)
        IF @TranslatorID IS NOT NULL AND NOT EXISTS (SELECT 1 FROM Employees WHERE EmployeeID = @TranslatorID)
        BEGIN
            RAISERROR(N'Tłumacz o podanym ID nie istnieje.', 16, 1);
            RETURN;
        END

        -- Sprawdzenie istnienia języka
        IF NOT EXISTS (SELECT 1 FROM Language WHERE LanguageID = @LanguageID)
        BEGIN
            RAISERROR(N'Język o podanym ID nie istnieje.', 16, 1);
            RETURN;
        END

        -- Wstawianie nowego modułu do tabeli Course Modules
        INSERT INTO CourseModules (
            CourseID, ModuleName, ModuleDescription, ModuleDate,
            ModuleDuration, TeacherID, TranslatorID, LanguageID
        )
        VALUES (
            @CourseID, @ModuleName, @ModuleDescription, @ModuleDate,
            @ModuleDuration, @TeacherID, @TranslatorID, @LanguageID
        );

        -- Komunikat o powodzeniu
        PRINT N'Moduł został pomyślnie dodany do kursu.';
    END TRY
    BEGIN CATCH
        -- Obsługa błędów
        DECLARE @ErrorMessage NVARCHAR(4000) = ERROR_MESSAGE();
        DECLARE @ErrorSeverity INT = ERROR_SEVERITY();
        DECLARE @ErrorState INT = ERROR_STATE();
        RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
GO
```

## Dodanie studiów

```sql
CREATE PROCEDURE [dbo].[AddStudie]
    @StudieName NVARCHAR(64),
    @StudieDescription NVARCHAR(128),
    @StudieEntryFee DECIMAL(10, 2),
    @StudieCoordinatorID INT,
    @PlaceLimit INT
AS
BEGIN
    BEGIN TRY
        -- Sprawdzenie istnienia koordynatora
        IF NOT EXISTS (SELECT 1 FROM Employees WHERE EmployeeID = @StudieCoordinatorID)
        BEGIN
            RAISERROR('Koordynator o podanym ID nie istnieje.', 16, 1);
            RETURN;
        END

        -- Wstawianie nowego rekordu do tabeli Studies
        INSERT INTO Studies (StudieName, StudieDescription, StudieEntryFee, StudieCoordinatorID, PlaceLimit)
        VALUES (@StudieName, @StudieDescription, @StudieEntryFee, @StudieCoordinatorID, @PlaceLimit);

        PRINT N'Studia zostały pomyślnie dodane.';
    END TRY
    BEGIN CATCH
        -- Obsługa błędów
        DECLARE @ErrorMessage NVARCHAR(4000) = ERROR_MESSAGE();
        DECLARE @ErrorSeverity INT = ERROR_SEVERITY();
        DECLARE @ErrorState INT = ERROR_STATE();
        RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
GO
```

## Edytowanie studiów

```sql
CREATE PROCEDURE [dbo].[UpdateStudie]
    @StudieID INT,
    @StudieName NVARCHAR(64) = NULL,
    @StudieDescription NVARCHAR(128) = NULL,
    @StudieEntryFee DECIMAL(10, 2) = NULL,
    @StudieCoordinatorID INT = NULL,
    @PlaceLimit INT = NULL
AS
BEGIN
    BEGIN TRY
        -- Sprawdzenie istnienia studiów
        IF NOT EXISTS (SELECT 1 FROM Studies WHERE StudieID = @StudieID)
        BEGIN
            RAISERROR(N'Studia o podanym ID nie istnieją.', 16, 1);
            RETURN;
        END

        -- Aktualizacja danych w tabeli Studies
        UPDATE Studies
        SET
            StudieName = ISNULL(@StudieName, StudieName),
            StudieDescription = ISNULL(@StudieDescription, StudieDescription),
            StudieEntryFee = ISNULL(@StudieEntryFee, StudieEntryFee),
            StudieCoordinatorID = ISNULL(@StudieCoordinatorID, StudieCoordinatorID),
            PlaceLimit = ISNULL(@PlaceLimit, PlaceLimit)
        WHERE StudieID = @StudieID;

        PRINT N'Studia zostały pomyślnie zaktualizowane.';
    END TRY
    BEGIN CATCH
        -- Obsługa błędów
        DECLARE @ErrorMessage NVARCHAR(4000) = ERROR_MESSAGE();
        DECLARE @ErrorSeverity INT = ERROR_SEVERITY();
        DECLARE @ErrorState INT = ERROR_STATE();
        RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
GO
```

## Usunięcie studiów

```sql
CREATE PROCEDURE [dbo].[DeleteStudie]
    @StudieID INT
AS
BEGIN
    BEGIN TRY
        -- Sprawdzenie istnienia studiów
        IF NOT EXISTS (SELECT 1 FROM Studies WHERE StudieID = @StudieID)
        BEGIN
            RAISERROR(N'Studia o podanym ID nie istnieją.', 16, 1);
            RETURN;
        END

        -- Usuwanie rekordu z tabeli Studies
        DELETE FROM Studies
        WHERE StudieID = @StudieID;

        PRINT N'Studia zostały pomyślnie usunięte.';
    END TRY
    BEGIN CATCH
        -- Obsługa błędów
        DECLARE @ErrorMessage NVARCHAR(4000) = ERROR_MESSAGE();
        DECLARE @ErrorSeverity INT = ERROR_SEVERITY();
        DECLARE @ErrorState INT = ERROR_STATE();
        RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
GO
```

## Dodanie przedmiotu do studiów

```sql
CREATE PROCEDURE [dbo].[AddSubject]
    @StudieID INT,
    @SubjectName NVARCHAR(64),
    @SubjectDescription NVARCHAR(128),
    @CoordinatorID INT
AS
BEGIN
    BEGIN TRY
        -- Sprawdzenie istnienia studiów
        IF NOT EXISTS (SELECT 1 FROM Studies WHERE StudieID = @StudieID)
        BEGIN
            RAISERROR(N'Studia o podanym ID nie istnieją.', 16, 1);
            RETURN;
        END

        -- Sprawdzenie istnienia koordynatora
        IF NOT EXISTS (SELECT 1 FROM Employees WHERE EmployeeID = @CoordinatorID)
        BEGIN
            RAISERROR('Koordynator o podanym ID nie istnieje.', 16, 1);
            RETURN;
        END

        -- Dodanie przedmiotu do tabeli Subjects
        INSERT INTO Subjects (StudieID, SubjectName, SubjectDescription, CoordinatorID)
        VALUES (@StudieID, @SubjectName, @SubjectDescription, @CoordinatorID);

        PRINT N'Przedmiot został pomyślnie dodany.';
    END TRY
    BEGIN CATCH
        -- Obsługa błędów
        DECLARE @ErrorMessage NVARCHAR(4000) = ERROR_MESSAGE();
        DECLARE @ErrorSeverity INT = ERROR_SEVERITY();
        DECLARE @ErrorState INT = ERROR_STATE();
        RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
GO
```

## Edytowanie szczegółów przedmiotu w studiach

```sql
CREATE PROCEDURE [dbo].[UpdateSubjectDetails]
    @SubjectID INT,
    @StudentID INT,
    @SubjectGrade INT = NULL
AS
BEGIN
    BEGIN TRY
        -- Sprawdzenie istnienia przedmiotu
        IF NOT EXISTS (SELECT 1 FROM Subjects WHERE SubjectID = @SubjectID)
        BEGIN
            RAISERROR('Przedmiot o podanym ID nie istnieje.', 16, 1);
            RETURN;
        END

        -- Sprawdzenie istnienia studenta
        IF NOT EXISTS (SELECT 1 FROM Students WHERE StudentID = @StudentID)
        BEGIN
            RAISERROR('Student o podanym ID nie istnieje.', 16, 1);
            RETURN;
        END

        -- Aktualizacja szczegółów w tabeli SubjectDetails
        UPDATE SubjectDetails
        SET
            SubjectGrade = ISNULL(@SubjectGrade, SubjectGrade)
        WHERE SubjectID = @SubjectID AND StudentID = @StudentID;

        PRINT N'Szczegóły przedmiotu zostały pomyślnie zaktualizowane.';
    END TRY
    BEGIN CATCH
        -- Obsługa błędów
        DECLARE @ErrorMessage NVARCHAR(4000) = ERROR_MESSAGE();
        DECLARE @ErrorSeverity INT = ERROR_SEVERITY();
        DECLARE @ErrorState INT = ERROR_STATE();
        RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
GO
```

## Dodanie nowego stażu

```sql
CREATE PROCEDURE [dbo].[AddInternship]
    @StudieID INT,
    @InternshipDuration INT,
    @InternshipStartDate DATE
AS
BEGIN
    BEGIN TRY
        -- Sprawdzenie istnienia studiów
        IF NOT EXISTS (SELECT 1 FROM Studies WHERE StudieID = @StudieID)
        BEGIN
            RAISERROR(N'Studia o podanym ID nie istnieją.', 16, 1);
            RETURN;
        END

        -- Dodanie nowego stażu
        INSERT INTO Internships (StudieID, InternshipDuration, InternshipStartDate)
        VALUES (@StudieID, @InternshipDuration, @InternshipStartDate);

        PRINT N'Staż został pomyślnie dodany.';
    END TRY
    BEGIN CATCH
        -- Obsługa błędów
        DECLARE @ErrorMessage NVARCHAR(4000) = ERROR_MESSAGE();
        DECLARE @ErrorSeverity INT = ERROR_SEVERITY();
        DECLARE @ErrorState INT = ERROR_STATE();
        RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
GO
```

## Edytowanie szczegółów stażu

```sql
CREATE PROCEDURE [dbo].[UpdateInternshipDetails]
    @InternshipID INT,
    @StudentID INT,
    @DaysAttended INT = NULL
AS
BEGIN
    BEGIN TRY
        -- Sprawdzenie istnienia stażu
        IF NOT EXISTS (SELECT 1 FROM Internships WHERE InternshipID = @InternshipID)
        BEGIN
            RAISERROR(N'Staż o podanym ID nie istnieje.', 16, 1);
            RETURN;
        END

        -- Sprawdzenie istnienia studenta
        IF NOT EXISTS (SELECT 1 FROM Students WHERE StudentID = @StudentID)
        BEGIN
            RAISERROR('Student o podanym ID nie istnieje.', 16, 1);
            RETURN;
        END

        -- Aktualizacja szczegółów stażu
        UPDATE InternshipDetails
        SET
            DaysAttended = ISNULL(@DaysAttended, DaysAttended)
        WHERE InternshipID = @InternshipID AND StudentID = @StudentID;

        PRINT N'Szczegóły stażu zostały pomyślnie zaktualizowane.';
    END TRY
    BEGIN CATCH
        -- Obsługa błędów
        DECLARE @ErrorMessage NVARCHAR(4000) = ERROR_MESSAGE();
        DECLARE @ErrorSeverity INT = ERROR_SEVERITY();
        DECLARE @ErrorState INT = ERROR_STATE();
        RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
GO
```

## Dodanie studenta

```sql
CREATE PROCEDURE AddStudent
@Email nvarchar(64), @Password nvarchar(64), @FirstName nvarchar(64), @LastName nvarchar(64), @Address nvarchar(64), @City nvarchar(64), @Country nvarchar(64)
AS
BEGIN
    DECLARE @HashedPassword VARBINARY(64);
    SET @HashedPassword = HASHBYTES('SHA2_256', @Password);

    INSERT INTO Students (Email, Password, FirstName, LastName, Address, City, Country)
    VALUES (@Email, @HashedPassword, @FirstName, @LastName, @Address, @City, @Country)
END;
```

## Edytowanie studenta

```sql
CREATE PROCEDURE EditStudent
    @StudentId INT,
    @Email NVARCHAR(64) = NULL, 
    @Password NVARCHAR(64) = NULL, 
    @FirstName NVARCHAR(64) = NULL, 
    @LastName NVARCHAR(64) = NULL, 
    @Address NVARCHAR(64) = NULL, 
    @City NVARCHAR(64) = NULL, 
    @Country NVARCHAR(64) = NULL
AS
BEGIN
    DECLARE @HashedPassword VARBINARY(64);

    IF @Password IS NOT NULL
    BEGIN
        SET @HashedPassword = HASHBYTES('SHA2_256', CONVERT(NVARCHAR(MAX), @Password));
    END

    UPDATE Students
    SET 
        Email = ISNULL(@Email, Email), 
        Password = CASE 
                      WHEN @Password IS NOT NULL THEN @HashedPassword
                      ELSE Password
                   END,
        FirstName = ISNULL(@FirstName, FirstName), 
        LastName = ISNULL(@LastName, LastName),
        Address = ISNULL(@Address, Address),
        City = ISNULL(@City, City),
        Country = ISNULL(@Country, Country)
    WHERE StudentId = @StudentId;
END;
```

## Usuwanie studenta

```sql
CREATE PROCEDURE RemoveStudent
    @StudentID INT
AS
BEGIN
    DELETE FROM Students
    WHERE StudentID = @StudentID;

    IF @@ROWCOUNT = 0
    BEGIN
        PRINT 'Nie znaleziono studenta o podanym ID.';
    END
    ELSE
    BEGIN
        PRINT 'Student został usunięty.';
    END
END;
```

## Dodanie pracownika

```sql
CREATE PROCEDURE AddEmployee
@EmployeeID int, @Email nvarchar(64), @Password nvarchar(64), @FirstName nvarchar(64), @LastName nvarchar(64), @Phone nvarchar(64), @HireDate date, @EmployeeType int
AS
BEGIN
    DECLARE @HashedPassword VARBINARY(64);
    SET @HashedPassword = HASHBYTES('SHA2_256', @Password);

    INSERT INTO Employees (EmployeeID, Email, Password, FirstName, LastName, Phone, HireDate, EmployeeType)
    VALUES (@EmployeeID, @Email, @HashedPassword, @FirstName, @LastName, @Phone, @HireDate, @EmployeeType)
END;
```

## Edytowanie pracownika

```sql
CREATE PROCEDURE EditEmployee
    @EmployeeID INT,
    @Email NVARCHAR(64) = NULL, 
    @Password NVARCHAR(64) = NULL, 
    @FirstName NVARCHAR(64) = NULL, 
    @LastName NVARCHAR(64) = NULL, 
    @Phone NVARCHAR(64) = NULL, 
    @HireDate DATE = NULL, 
    @EmployeeType INT = NULL
AS
BEGIN
    DECLARE @HashedPassword VARBINARY(64);

    IF @Password IS NOT NULL
    BEGIN
        SET @HashedPassword = HASHBYTES('SHA2_256', CONVERT(NVARCHAR(MAX), @Password));
    END

    UPDATE Employees
    SET 
        Email = ISNULL(@Email, Email), 
        Password = CASE 
                      WHEN @Password IS NOT NULL THEN @HashedPassword
                      ELSE Password
                   END,
        FirstName = ISNULL(@FirstName, FirstName), 
        LastName = ISNULL(@LastName, LastName),
        Phone = ISNULL(@Phone, Phone),
        HireDate = ISNULL(@HireDate, HireDate),
        EmployeeType = ISNULL(@EmployeeType, EmployeeType)
    WHERE EmployeeID = @EmployeeID;
END;
```

## Usuwanie pracownika

```sql
CREATE PROCEDURE RemoveEmployee
    @EmployeeID INT
AS
BEGIN
    DELETE FROM Employees
    WHERE EmployeeID = @EmployeeID;

    IF @@ROWCOUNT = 0
    BEGIN
        PRINT 'Nie znaleziono pracownika o podanym ID.';
    END
    ELSE
    BEGIN
        PRINT 'Pracownik został pomyślnie usunięty.';
    END
END;
```

## Dodanie tłumacza

```sql
CREATE PROCEDURE AddTranslator
@TranslatorID int, @Email nvarchar(64), @Password nvarchar(64), @FirstName nvarchar(64), @LastName nvarchar(64), @Address nvarchar(64), @Phone nvarchar(64), @HireDate date
AS
BEGIN
    DECLARE @HashedPassword VARBINARY(64);
    SET @HashedPassword = HASHBYTES('SHA2_256', @Password);

    INSERT INTO Employees (TranslatorID, Email, Password, FirstName, LastName, Address, Phone, HireDate)
    VALUES (@TranslatorID, @Email, @HashedPassword, @FirstName, @LastName, @Address, @Phone, @HireDate)
END;
```

## Edytowanie tłumacza

```sql
CREATE PROCEDURE EditTranslator
    @TranslatorID INT,
    @Email NVARCHAR(64) = NULL, 
    @Password NVARCHAR(64) = NULL, 
    @FirstName NVARCHAR(64) = NULL, 
    @LastName NVARCHAR(64) = NULL,  
    @Phone NVARCHAR(64) = NULL, 
    @HireDate DATE = NULL
AS
BEGIN
    DECLARE @HashedPassword VARBINARY(64);

    IF @Password IS NOT NULL
    BEGIN
        SET @HashedPassword = HASHBYTES('SHA2_256', CONVERT(NVARCHAR(MAX), @Password));
    END

    UPDATE Translators
    SET 
        Email = ISNULL(@Email, Email), 
        Password = CASE 
                      WHEN @Password IS NOT NULL THEN @HashedPassword
                      ELSE Password
                   END,
        FirstName = ISNULL(@FirstName, FirstName), 
        LastName = ISNULL(@LastName, LastName),
        Phone = ISNULL(@Phone, Phone),
        HireDate = ISNULL(@HireDate, HireDate)
    WHERE TranslatorID = @TranslatorID;
END;
```

## Usuwanie tłumacza

```sql
CREATE PROCEDURE RemoveTranslator
    @TranslatorID INT
AS
BEGIN
    DELETE FROM Employees
    WHERE TranslatorID = @TranslatorID;

    IF @@ROWCOUNT = 0
    BEGIN
        PRINT 'Nie znaleziono tłumacza o podanym ID.';
    END
    ELSE
    BEGIN
        PRINT 'Tłumacz został pomyślnie usunięty.';
    END
END;
```

## Dodawanie produktu do koszyka

```sql
CREATE PROCEDURE dbo.AddProductToCart
    @OrderID INT,
    @ProductID INT,
    @ProductType INT,
    @Price MONEY
AS
BEGIN
    INSERT INTO OrderDetails (OrderID, ProductID, ProductType, Price, StatusID)
    VALUES (@OrderID, @ProductID, @ProductType, @Price, 0); -- 0 oznacza produkt w koszyku

    IF @@ROWCOUNT = 0
    BEGIN
        PRINT 'Nie udało się dodać produktu do koszyka.';
    END
    ELSE
    BEGIN
        PRINT 'Produkt został pomyślnie dodany do koszyka.';
    END
END;
```

## Usuwanie produktu z koszyka

```sql
CREATE PROCEDURE dbo.RemoveProductFromCart
    @OrderID INT,
    @ProductID INT,
    @ProductType INT
AS
BEGIN
    DELETE FROM OrderDetails
    WHERE OrderID = @OrderID AND ProductID = @ProductID AND ProductType = @ProductType;

    IF @@ROWCOUNT = 0
    BEGIN
        PRINT 'Nie znaleziono produktu do usunięcia z koszyka.';
    END
    ELSE
    BEGIN
        PRINT 'Produkt został pomyślnie usunięty z koszyka.';
    END
END;
```

## Składanie zamówienia

```sql
CREATE PROCEDURE dbo.MakeOrder
    @OrderID INT
AS
BEGIN
    UPDATE OrderDetails
    SET StatusID = 5
    WHERE OrderID = @OrderID;

    IF @@ROWCOUNT = 0
    BEGIN
        PRINT 'Nie znaleziono zamówienia o podanym ID w szczegółach zamówienia.';
    END
    ELSE
    BEGIN
        PRINT 'Zamówienie zostało pomyślnie złożone.';
    END
END;
```

## Manualne dodawanie zamówienia

```sql
CREATE PROCEDURE dbo.ManuallyAddOrder
    @OrderID INT,
    @OrderDate DATETIME,
    @PaymentLink NVARCHAR(MAX)
AS
BEGIN
    INSERT INTO Orders (OrderID, OrderDate, PaymentLink)
    VALUES (@OrderID, @OrderDate, @PaymentLink);

    IF @@ROWCOUNT = 0
    BEGIN
        PRINT 'Nie udało się dodać nowego zamówienia.';
    END
    ELSE
    BEGIN
        PRINT 'Nowe zamówienie zostało pomyślnie dodane.';
    END
END;
```

## Manualne dodawanie szczegółów zamówienia

```sql
CREATE PROCEDURE dbo.ManuallyAddOrderDetails
    @OrderID INT,
    @StatusID INT,
    @StudentID INT,
    @ProductType INT,
    @ProductID INT,
    @Price MONEY
AS
BEGIN
    INSERT INTO OrderDetails (OrderID, StatusID, StudentID, ProductType, ProductID, Price)
    VALUES (@OrderID, @StatusID, @StudentID, @ProductType, @ProductID, @Price);

    IF @@ROWCOUNT = 0
    BEGIN
        PRINT 'Nie udało się dodać szczegółów zamówienia.';
    END
    ELSE
    BEGIN
        PRINT 'Szczegóły zamówienia zostały pomyślnie dodane.';
    END
END;
```

## Manualna zamiana statusu zamówienia

```sql
CREATE PROCEDURE dbo.ManuallyUpdateOrderStatus
    @OrderID INT,
    @StatusID INT
AS
BEGIN
    UPDATE OrdersDetails
    SET StatusID = @StatusID
    WHERE OrderID = @OrderID;

    IF @@ROWCOUNT = 0
    BEGIN
        PRINT 'Nie znaleziono zamówienia o podanym ID.';
    END
    ELSE
    BEGIN
        PRINT 'Status zamówienia został pomyślnie zaktualizowany.';
    END
END;
```