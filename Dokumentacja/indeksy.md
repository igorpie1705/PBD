# Indeksy Ram Czasowych oraz Cen

## Indeksy Ram Czasowych

```sql
CREATE INDEX CourseModule_Time ON CourseModules (ModuleDate, ModuleDuration);
CREATE INDEX Internships_Time ON Internships (InternshipStartDate, InternshipDuration);
CREATE INDEX StudiesMeeting_Time ON StudiesMeeting (MeetingDate, MeetingDuration);
CREATE INDEX Webinar_Time ON Webinars (WebinarStartDate);
```

## Indeksy Cen

```sql
CREATE INDEX Studies_Price ON Studies (StudieEntryFee);
CREATE INDEX StudiesMeeting_Price ON StudiesMeeting (MeetingPrice);
CREATE INDEX Course_Price ON Courses (CoursePrice);
CREATE INDEX Webinar_Price ON Webinars (WebinarPrice);
```

# Indeksy po Kluczach Obcych

## Tabela CourseDetails

```sql
CREATE INDEX CourseDetails_CourseID ON CourseDetails (CourseID);
CREATE INDEX CourseDetails_StudentID ON CourseDetails (StudentID);
```

## Tabela CourseModules

```sql
CREATE INDEX CourseModules_CourseID ON CourseModules (CourseID);
CREATE INDEX CourseModules_TeacherID ON CourseModules (TeacherID);
CREATE INDEX CourseModules_LanguageID ON CourseModules (LanguageID);
CREATE INDEX CourseModules_TranslatorID ON CourseModules (TranslatorID);
```

## Tabela Courses

```sql
CREATE INDEX Courses_CourseCoordinatorID ON Courses (CourseCoordinatorID);
```

## Tabela EmployeeLanguages

```sql
CREATE INDEX EmployeeLanguages_EmployeeID ON EmployeeLanguages (EmployeeID);
CREATE INDEX EmployeeLanguages_LanguageID ON EmployeeLanguages (LanguageID);
```

## Tabela Employees

```sql
CREATE INDEX Employees_EmployeeType ON Employees (EmployeeType);
```

## Tabela InternshipDetails

```sql
CREATE INDEX InternshipDetails_InternshipID ON InternshipDetails (InternshipID);
CREATE INDEX InternshipDetails_StudentID ON InternshipDetails (StudentID);
```

## Tabela Internships

```sql
CREATE INDEX Internships_StudiesID ON Internships (StudieID);
```

## Tabela MeetingDetails

```sql
CREATE INDEX MeetingDetails_StudentID ON MeetingDetails (StudentID);
CREATE INDEX MeetingDetails_MeetingID ON MeetingDetails (MeetingID);
```

## Tabela ModulesPassed

```sql
CREATE INDEX ModulesPassed_ModuleID ON ModulesPassed (ModuleID);
CREATE INDEX ModulesPassed_StudentID ON ModulesPassed (StudentID);
```

## Tabela OnlineAsyncMeeting

```sql
CREATE INDEX OnlineAsyncMeeting_MeetingID ON OnlineAsyncMeeting (MeetingID);
```

## Tabela OnlineAsyncModule

```sql
CREATE INDEX OnlineAsyncModule_ModuleID ON OnlineAsyncModule (ModuleID);
```

## Tabela OnlineSyncMeeting

```sql
CREATE INDEX OnlineSyncMeeting_MeetingID ON OnlineSyncMeeting (MeetingID);
```

## Tabela OnlineSyncModule

```sql
CREATE INDEX OnlineSyncModule_ModuleID ON OnlineSyncModule (ModuleID);
CREATE INDEX OnlineSyncModule_PlatformID ON OnlineSyncModule (PlatformID);
```

## Tabela OrderDetails

```sql
CREATE INDEX OrderDetails_ProductID ON OrderDetails (ProductID);
CREATE INDEX OrderDetails_StudentID ON OrderDetails (StudentID);
CREATE INDEX OrderDetails_StatusID ON OrderDetails (StatusID);
CREATE INDEX OrderDetails_OrderID ON OrderDetails (OrderID);
```

## Tabela StationaryMeeting

```sql
CREATE INDEX StationaryMeeting_RoomID ON StationaryMeeting (RoomID);
CREATE INDEX StationaryMeeting_MeetingID ON StationaryMeeting (MeetingID);
```

## Tabela StationaryModule

```sql
CREATE INDEX StationaryModule_RoomID ON StationaryModule (RoomID);
CREATE INDEX StationaryModule_ModuleID ON StationaryModule (ModuleID);
```

## Tabela StudieDetails

```sql
CREATE INDEX StudiesDetails_StudiesID ON StudieDetails (StudieID);
CREATE INDEX StudiesDetails_StudentID ON StudieDetails (StudentID);
```

## Tabela Studies

```sql
CREATE INDEX Studies_StudieCoordinatorID ON Studies (StudieCoordinatorID);
```

## Tabela StudiesMeeting

```sql
CREATE INDEX StudiesMeeting_SubjectID ON StudiesMeeting (SubjectID);
CREATE INDEX StudiesMeeting_TeacherID ON StudiesMeeting (TeacherID);
CREATE INDEX StudiesMeeting_TranslatorID ON StudiesMeeting (TranslatorID);
```

## Tabela SubjectDetails

```sql
CREATE INDEX SubjectDetails_SubjectID ON SubjectDetails (SubjectID);
CREATE INDEX SubjectDetails_StudentID ON SubjectDetails (StudentID);
```

## Tabela Subjects

```sql
CREATE INDEX Subjects_StudieID ON Subjects (StudieID);
CREATE INDEX Subjects_CoordinatorID ON Subjects (CoordinatorID);
```

## Tabela WebinarAccess

```sql
CREATE INDEX WebinarAccess_StudentID ON WebinarAccess (StudentID);
CREATE INDEX WebinarAccess_WebinarID ON WebinarAccess (WebinarID);
```

## Tabela Webinars

```sql
CREATE INDEX Webinars_PlatformID ON Webinars (PlatformID);
CREATE INDEX Webinars_TeacherID ON Webinars (TeacherID);
CREATE INDEX Webinars_TranslatorID ON Webinars (TranslatorID);
CREATE INDEX Webinars_LanguageID ON Webinars (LanguageID);
```

## Tabela TranslatorsLanguages

```sql
CREATE INDEX TranslatorsLanguages_TranslatorID ON TranslatorsLanguages (TranslatorID);
CREATE INDEX TranslatorsLanguages_LanguageID ON TranslatorsLanguages (LanguageID);
```

## Tabela WebinarDetails

```sql
CREATE INDEX WebinarDetails_StudentID ON WebinarDetails (StudentID);
CREATE INDEX WebinarDetails_WebinarID ON WebinarDetails (WebinarID);
```
