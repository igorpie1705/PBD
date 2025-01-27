# Role użytkowników bazy

## Role_Admin
Administrator bazy, posiada wszystkie uprawnienia związane z edycją danych.
```sql
CREATE ROLE Role_Admin;
GRANT SELECT, INSERT, UPDATE, DELETE ON SCHEMA :: dbo TO Role_Admin;
GRANT EXECUTE TO Role_Admin;
```
## Role_Student
Student, posiada uprawnienia potrzebne do składania zamówień oraz uprawnienia do odczytu informacji o zajęciach.
```sql
CREATE ROLE Role_Student;
GRANT SELECT, INSERT, UPDATE, DELETE ON Orders TO Role_Student;
GRANT SELECT, INSERT, UPDATE, DELETE ON OrderDetails TO Role_Student;
GRANT SELECT ON Webinars TO Role_Student;
GRANT SELECT ON WebinarDetails TO Role_Student;
GRANT SELECT ON Platforms TO Role_Student;
GRANT SELECT ON Studies TO Role_Student;
GRANT SELECT ON StudiesMeeting TO Role_Student;
GRANT SELECT ON StationaryMeeting TO Role_Student;
GRANT SELECT ON Rooms TO Role_Student;
GRANT SELECT ON OnlineSyncMeeting TO Role_Student;
GRANT SELECT ON OnlineAsyncMeeting TO Role_Student;
GRANT SELECT ON MeetingDetail TO Role_Student;
GRANT SELECT ON Internships TO Role_Student;
GRANT SELECT ON InternshipDetails TO Role_Student;
GRANT SELECT ON Subjects TO Role_Student;
GRANT SELECT ON Courses TO Role_Student;
GRANT SELECT ON CourseModules TO Role_Student;
GRANT SELECT ON OnlineSyncModule TO Role_Student;
GRANT SELECT ON StationaryModule TO Role_Student;
GRANT SELECT ON OnlineAsyncModule TO Role_Student;
GRANT SELECT ON ModulesPassed TO Role_Student;
```
## Role_Teacher
Nauczyciel, posiada uprawnienia do odczytu tabel związanych z pracownikami, usługami oraz ma dostęp do edycji szczegółów spotkań i języków, którymi się posługuje.
```sql
CREATE ROLE Role_Teacher;
GRANT SELECT ON EmployeeType TO Role_Teacher;
GRANT SELECT ON Employees TO Role_Teacher;
GRANT SELECT ON Translators TO Role_Teacher;
GRANT SELECT ON Language TO Role_Teacher;
GRANT SELECT ON Students TO Role_Teacher;
GRANT SELECT ON ModulesPassed TO Role_Teacher;
GRANT SELECT ON Courses TO Role_Teacher;
GRANT SELECT ON CourseModules TO Role_Teacher;
GRANT SELECT ON CourseDetails TO Role_Teacher;
GRANT SELECT ON OnlineSyncModule TO Role_Teacher;
GRANT SELECT ON StationaryModule TO Role_Teacher;
GRANT SELECT ON Rooms TO Role_Teacher;
GRANT SELECT ON OnlineAsyncModule TO Role_Teacher;
GRANT SELECT ON Studies TO Role_Teacher;
GRANT SELECT ON StudiesMeeting TO Role_Teacher;
GRANT SELECT ON OnlineAsyncMeeting TO Role_Teacher;
GRANT SELECT ON OnlineSyncMeeting TO Role_Teacher;
GRANT SELECT ON StationaryMeeting TO Role_Teacher;
GRANT SELECT ON SubjectDetails TO Role_Teacher;
GRANT SELECT ON Subjects TO Role_Teacher;
GRANT SELECT ON Webinars TO Role_Teacher;
GRANT SELECT ON WebinarDetails TO Role_Teacher;
GRANT SELECT ON Platforms TO Role_Teacher;
GRANT SELECT, INSERT, UPDATE ON EmployeeLanguages TO Role_Teacher;
GRANT SELECT, INSERT, UPDATE ON MeetingDetails TO Role_Teacher;
GRANT SELECT, INSERT, UPDATE ON StudieDetails TO Role_Teacher;
```
## Role_Translator
Tłumacz, posiada uprawnienia do odczytu tabel związanych z pracownikami, usługami oraz ma dostęp do edycji języków, którymi się posługuje.
```sql
CREATE ROLE Role_Translator;
GRANT SELECT, INSERT, UPDATE ON EmployeeLanguages TO Role_Translator;
GRANT SELECT ON Employees TO Role_Translator;
GRANT SELECT ON Translators TO Role_Translator;
GRANT SELECT ON EmployeeType TO Role_Translator;
GRANT SELECT ON StudiesMeeting TO Role_Translator;
GRANT SELECT ON StationaryMeeting TO Role_Translator;
GRANT SELECT ON Rooms TO Role_Translator;
GRANT SELECT ON OnlineSyncMeeting TO Role_Translator;
GRANT SELECT ON OnlineAsyncMeeting TO Role_Translator;
GRANT SELECT ON Subjects TO Role_Translator;
```