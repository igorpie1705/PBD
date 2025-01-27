# Triggery

## trg_after_payment
Odpowiada za dodanie uprawnień do zakupupionej usługi po jej opłaceniu.
```sql
CREATE TRIGGER trg_after_payment
ON OrderDetails
AFTER UPDATE
AS
BEGIN
    -- Sprawdzamy, czy StatusID zmienił się na 4 - opłacony
    IF EXISTS (SELECT 1 FROM inserted WHERE StatusID = 4 AND StatusID <> (SELECT StatusID FROM deleted))
    BEGIN
        DECLARE @ProductType INT, @ProductID INT, @StudentID INT;
        DECLARE order_cursor CURSOR FOR
        SELECT ProductType, ProductID, StudentID
        FROM inserted
        WHERE StatusID = 4;

        OPEN order_cursor;
        FETCH NEXT FROM order_cursor INTO @ProductType, @ProductID, @StudentID;

        WHILE @@FETCH_STATUS = 0
        BEGIN
            IF @ProductType = 0
            BEGIN
                INSERT INTO WebinarAccess (WebinarID, StudentID, HasAccess)
                VALUES (@ProductID, @StudentID, 1);
            END
            ELSE IF @ProductType = 1
            BEGIN
                INSERT INTO CourseDetails (CourseID, StudentID)
                VALUES (@ProductID, @StudentID);
            END
            ELSE IF @ProductType = 2
            BEGIN
                INSERT INTO StudieDetails (StudieID, StudentID)
                VALUES (@ProductID, @StudentID);
            END

            FETCH NEXT FROM order_cursor INTO @ProductType, @ProductID, @StudentID;
        END

        CLOSE order_cursor;
        DEALLOCATE order_cursor;
    END
END
```
## trg_after_delete_webinar
Pozbywa się danych związanych z webinarem po jego usunięciu.
```sql
CREATE TRIGGER trg_after_delete_webinar
ON Webinars
AFTER DELETE
AS
BEGIN
    DELETE FROM WebinarAccess
    WHERE WebinarID IN (SELECT WebinarID FROM deleted);

    DELETE FROM WebinarDetails
    WHERE WebinarID IN (SELECT WebinarID FROM deleted);

    DELETE FROM OrderDetails
    WHERE ProductType = 1
    AND ProductID IN (SELECT WebinarID FROM deleted)
    AND StatusID IN (0, 5); -- 0 - w koszyku, 5 - nieopłacony
END
```
## trg_after_delete_course
Pozbywa się danych związanych z kursem po jego usunięciu.
```sql
CREATE TRIGGER trg_after_delete_course
ON Courses
AFTER DELETE
AS
BEGIN
    DELETE FROM CourseModules
    WHERE CourseID IN (SELECT CourseID FROM deleted);

    DELETE FROM CourseDetails
    WHERE CourseID IN (SELECT CourseID FROM deleted);

    DELETE FROM OrderDetails
    WHERE ProductType = 1
    AND ProductID IN (SELECT CourseID FROM deleted)
    AND StatusID IN (0, 5); -- koszyk, nieopłacony
END
```
## trg_after_delete_study
Pozbywa się danych związanych ze studiami po ich usunięciu.
```sql
CREATE TRIGGER trg_after_delete_study
ON Studies
AFTER DELETE
AS
BEGIN
    DELETE FROM Internships
    WHERE StudieID IN (SELECT StudieID FROM deleted);

    DELETE FROM Subjects
    WHERE StudieID IN (SELECT StudieID FROM deleted);

    DELETE FROM OrderDetails
    WHERE ProductType = 2
    AND ProductID IN (SELECT StudieID FROM deleted)
    AND StatusID IN (0, 5);
END
```