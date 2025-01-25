# Widoki

## Widok przedstawiający harmonogram wszystkich studiów

```sql
CREATE VIEW ALL_STUDIES_TIMETABLE AS
SELECT s.StudieID, sm.MeetingID, sm.MeetingName, sm.MeetingDate, sm.MeetingDuration
FROM dbo.Studies AS s
INNER JOIN dbo.Subjects AS su
ON s.StudieID = su.StudieID
INNER JOIN dbo.StudiesMeeting AS sm
ON su.SubjectID = sm.SubjectID
```

## Widok przedstawiający harmonogram wszystkich kursów

```sql
CREATE VIEW ALL_COURSES_TIMETABLE AS
SELECT c.CourseID, cm.ModuleID, cm.ModuleName, cm.ModuleDate, cm.ModuleDuration
FROM Courses AS c
INNER JOIN CourseModules AS cm
ON c.CourseID = cm.CourseID
```

## Widok przedstawiający harmonogram wszystkich webinarów

```sql
CREATE VIEW ALL_WEBINARS_TIMETABLE AS
SELECT w.WebinarID, w.WebinarName, w.WebinarStartDate
FROM Webinars AS w
```
