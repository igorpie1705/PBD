# Funkcje SQL

## Sprawdzenie ilości miejsc na dane studia

```sql
CREATE FUNCTION GetMaxStudyCapacity(@StudiesID int)
RETURNS int
AS
BEGIN
    DECLARE @MaxCapacity int;

    SELECT @MaxCapacity = MIN(Rooms.PlaceLimit)
    FROM StationaryMeeting
    JOIN StudiesMeeting ON StationaryMeeting.MeetingID = StudiesMeeting.MeetingID
    JOIN Subjects ON StudiesMeeting.SubjectID = Subjects.SubjectID
    JOIN Rooms ON StationaryMeeting.RoomID = Rooms.RoomID
    WHERE Subjects.SubjectID = @StudiesID;

    RETURN @MaxCapacity
END
```

## Sprawdzenie wolnych miejsc na dany kierunek studiów

```sql
CREATE FUNCTION HowManyStudyVacancies(@StudiesID INT)
RETURNS INT
AS
BEGIN
    DECLARE @MaximumCapacity INT = dbo.GetMaxStudyCapacity(@StudiesID);
    IF @MaximumCapacity IS NULL
    BEGIN
        RETURN NULL;
    END
    DECLARE @CurrentCapacity INT = (
        SELECT COUNT(*)
        FROM Students AS s
        JOIN StudieDetails AS sd
          ON s.StudentID = sd.StudentID
        WHERE sd.StudieID = @StudiesID
    );
    RETURN @MaximumCapacity - @CurrentCapacity;
END
```

## Harmonogram spotkania na studiach

```sql
CREATE FUNCTION GetStudySchedule(@StudiesID int)
RETURNS TABLE
AS
RETURN
    (SELECT MeetingID, MeetingName, MeetingDate, MeetingDuration
    FROM dbo.ALL_STUDIES_TIMETABLE
    WHERE StudieID = @StudiesID)
```

## Harmonogram kursu

```sql
CREATE FUNCTION GetCourseSchedule(@CourseID int)
RETURNS TABLE
AS
RETURN
    (SELECT ModuleID, ModuleName, ModuleDate, ModuleDuration
    FROM dbo.ALL_COURSES_TIMETABLE
    WHERE CourseID = @CourseID)
```

## Sprawdzenie liczby osób zapisanych na dany webinar

```sql
CREATE FUNCTION [dbo].[GetWebinarAttendance]
(
    @WebinarID INT
)
RETURNS INT
AS
BEGIN
    DECLARE @AttendanceCount INT;

    SELECT @AttendanceCount = COUNT(*)
    FROM WebinarAccess
    WHERE WebinarID = @WebinarID
      AND HasAccess = 1;

    RETURN @AttendanceCount;
END;
GO
```

## Sprawdzenie daty wygaśnięcia dostępu do webinaru płatnego

```sql
CREATE FUNCTION CheckWebinarAccess(@WebinarID INT)
RETURNS BIT
AS
BEGIN
    DECLARE @IsExpired BIT;

    SELECT @IsExpired =
        CASE
            WHEN w.IsFree = 0 AND wd.ExpireDate < GETDATE() THEN 1
            ELSE 0
        END
    FROM Webinars w
    INNER JOIN WebinarDetails wd ON w.WebinarID = wd.WebinarID
    WHERE w.WebinarID = @WebinarID;

    RETURN @IsExpired;
END;
```

## Sprawdzenie liczby zapisanych na dany kierunek studiów

```sql
CREATE FUNCTION GetStudentCount(@StudieID INT)
RETURNS INT
AS
BEGIN
    DECLARE @StudentCount INT;

    SELECT @StudentCount = COUNT(*)
    FROM StudieDetails
    WHERE StudieID = @StudieID;

    RETURN @StudentCount;
END;
```

## Sprawdzenie czy uczestnik zdał kurs (>=80% zdanych modułów)

```sql
CREATE FUNCTION StudentPassedCourse(@StudentID int, @CourseID int)
RETURNS BIT
AS
BEGIN
    IF NOT EXISTS (SELECT * FROM Students WHERE StudentID = @StudentID)
    BEGIN
        RETURN 0;
    END;

    IF NOT EXISTS (SELECT * FROM Courses WHERE CourseID = @CourseID)
    BEGIN
        RETURN 0;
    END;

    IF NOT EXISTS (SELECT * FROM Courses AS c
        JOIN CourseDetails AS cd ON cd.CourseID = c.CourseID
        WHERE StudentID = @StudentID AND cd.CourseID = @CourseID)
    BEGIN
        RETURN 0;
    END


    DECLARE @ModulesPassed FLOAT = (
        SELECT COUNT(*)
        FROM CourseModules cm
        JOIN ModulesPassed mp ON mp.ModuleID = cm.ModuleID
        WHERE mp.StudentID = @StudentID AND cm.CourseID = @CourseID
    );

    DECLARE @TotalModules FLOAT = (
        SELECT COUNT(*)
        FROM CourseModules cm
        WHERE cm.CourseID = @CourseID
    );

    IF @TotalModules > 0 AND @ModulesPassed / @TotalModules >= 0.8
    BEGIN
        RETURN 1;
    END;

    RETURN 0;
    
END;
```

## Zliczenie frekwencji studenta na danym przedmiocie na studiach

```sql
CREATE FUNCTION GetSubjectAttendanceForStudent(@StudentID int, @SubjectID int)
RETURNS REAL
AS
BEGIN
    IF NOT EXISTS (SELECT * FROM Students WHERE StudentID = @StudentID)
    BEGIN
        RETURN 0.0;
    END

    IF NOT EXISTS (SELECT * FROM Subjects WHERE SubjectID = @SubjectID)
    BEGIN
        RETURN 0.0;
    END;

    IF NOT EXISTS (SELECT 1 FROM Subjects AS s
        JOIN SubjectDetails AS sd ON sd.SubjectID = s.SubjectID
        WHERE StudentID = @StudentID AND sd.SubjectID = @SubjectID)
    BEGIN
        RETURN 0.0;
    END


    DECLARE @AttendanceCount FLOAT = 0.0;
    DECLARE @MeetingsCount FLOAT = 0.0;
    SELECT @AttendanceCount = COUNT(*)
    FROM StudiesMeeting sm
    JOIN MeetingDetails md ON md.MeetingID = sm.MeetingID
    WHERE StudentID = @StudentID AND Presence = 1 AND SubjectID = @SubjectID AND MeetingDate < GETDATE();

    SELECT @MeetingsCount = COUNT(*)
    FROM StudiesMeeting sm
    JOIN MeetingDetails md ON md.MeetingID = sm.MeetingID
    WHERE StudentID = @StudentID AND SubjectID = @SubjectID AND MeetingDate < GETDATE();

    RETURN @AttendanceCount / @MeetingsCount;
END;
GO
```

## Sprawdzenie czy student odbył praktyki 2 razy w roku i czy miał na nich 100% frekwencji

```sql
CREATE FUNCTION HasCompletedYearlyInternships(@StudentID INT, @Year INT)
RETURNS BIT
AS
BEGIN
    DECLARE @Result BIT;
    IF (SELECT COUNT(*)
        FROM InternshipDetails id
        JOIN Internships i ON i.InternshipID = id.InternshipID
        WHERE id.StudentID = @StudentID
        AND id.DaysAttended = 14 
        AND YEAR(i.InternshipStartDate) = @Year) = 2
    BEGIN
        SET @Result = 1;
    END
    ELSE
    BEGIN
        SET @Result = 0;
    END

    RETURN @Result
END;
```

## Harmonogram zajęć dla studenta

```sql
CREATE FUNCTION GetStudentsSchedule(@StudentID INT)
RETURNS TABLE
AS
RETURN
(
    SELECT 
        sm.MeetingID AS EventID,
        'StudiesMeeting' AS EventType,
        sm.MeetingDate AS EventDate,
        sm.MeetingDuration AS Duration
    FROM 
        StudiesMeeting sm
	JOIN MeetingDetails md ON md.MeetingID = sm.MeetingID
    WHERE
        md.StudentID = @StudentID

    UNION ALL

    SELECT 
        w.WebinarID AS EventID,
        'Webinar' AS EventType,
        w.WebinarStartDate AS EventDate,
        NULL AS Duration
    FROM 
        Webinars w
	JOIN WebinarAccess wa ON wa.WebinarID = w.WebinarID
    WHERE
        wa.StudentID = @StudentID

    UNION ALL

    SELECT 
        c.CourseID AS EventID,
        'Course' AS EventType,
        c.ModuleDate AS EventDate,
        c.ModuleDuration AS Duration
    FROM 
        CourseModules c
	JOIN CourseDetails cd ON cd.CourseID = c.CourseID
    WHERE
        cd.StudentID = @StudentID
)
```

## Harmonogram zajęć dla nauczyciela

```sql
CREATE FUNCTION GetTeachersSchedule(@TeacherID INT)
RETURNS TABLE
AS
RETURN
(
    SELECT 
        sm.MeetingID AS EventID,
        'StudiesMeeting' AS EventType,
        sm.MeetingDate AS EventDate,
        sm.MeetingDuration AS Duration
    FROM 
        StudiesMeeting sm
    WHERE
        sm.TeacherID = @TeacherID

    UNION ALL

    SELECT 
        w.WebinarID AS EventID,
        'Webinar' AS EventType,
        w.WebinarStartDate AS EventDate,
        NULL AS Duration
    FROM 
        Webinars w
    WHERE
        w.TeacherID = @TeacherID

    UNION ALL

    SELECT 
        c.CourseID AS EventID,
        'Course' AS EventType,
        c.ModuleDate AS EventDate,
        c.ModuleDuration AS Duration
    FROM 
        CourseModules c
    WHERE
        c.TeacherID = @TeacherID
)
```

## Harmonogram zajęć dla tłumacza

```sql
CREATE FUNCTION GetTranslatorsSchedule (@TranslatorID INT)
RETURNS TABLE
AS
RETURN
(
    SELECT 
        m.MeetingID, 
        m.TranslatorID, 
        m.MeetingDate, 
        m.MeetingDuration
    FROM 
        StudiesMeeting m
    WHERE
        m.TranslatorID = @TranslatorID
);
```

## Harmonogram zajęć dla tłumacza

```sql
CREATE FUNCTION GetTranslatorsSchedule (@TranslatorID INT)
RETURNS TABLE
AS
RETURN
(
    SELECT 
        m.MeetingID, 
        m.TranslatorID, 
        m.MeetingDate, 
        m.MeetingDuration
    FROM 
        StudiesMeeting m
    WHERE
        m.TranslatorID = @TranslatorID
);

```

## Policzenie średniej ocen studenta na studiach

```sql
CREATE FUNCTION GetStudentAverageGrade(@StudentID INT, @StudieID INT)
RETURNS DECIMAL(5,2)
AS
BEGIN
    DECLARE @AverageGrade DECIMAL(5,2);

    SELECT @AverageGrade = AVG(sd.SubjectGrade)
    FROM Subjects s
    JOIN SubjectDetails sd ON sd.SubjectID = s.SubjectID
    WHERE sd.StudentID = @StudentID AND s.StudieID = @StudieID

    IF @AverageGrade IS NULL
    BEGIN
        SET @AverageGrade = 0;
    END

    RETURN @AverageGrade;
END;
```

## Harmonogram sali

```sql
CREATE FUNCTION dbo.GetRoomSchedule (@RoomID INT)
RETURNS TABLE
AS
RETURN
(
    SELECT 
        sm.MeetingID, 
        sm.RoomID, 
        stm.MeetingDate, 
        stm.MeetingDuration
    FROM 
        StationaryMeeting sm
    JOIN 
        StudiesMeeting stm ON sm.MeetingID = stm.MeetingID
    WHERE
        sm.RoomID = @RoomID
    ORDER BY 
        stm.MeetingDate
);
```

## Sprawdzenie czy dany tłumacz zna dany język

```sql
CREATE FUNCTION CheckTranslatorLanguage(@TranslatorID int, @LanguageID int)
RETURNS bit
AS
BEGIN
    DECLARE @Result bit;

    IF EXISTS(
        SELECT 1
        FROM EmployeeLanguages
        WHERE EmployeeID = @TranslatorID AND LanguageID = @LanguageID
    )
    BEGIN
        SET @Result = 1;
    END
    ELSE
    BEGIN
        SET @Result = 0;
    END

    RETURN @Result
END;
```
