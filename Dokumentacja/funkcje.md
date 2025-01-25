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
