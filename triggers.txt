-- Create and use database
CREATE DATABASE IF NOT EXISTS college;
USE college;

-- Drop tables if they exist (for clean execution)
DROP TABLE IF EXISTS student_log;
DROP TABLE IF EXISTS student;

-- Create main table
CREATE TABLE student (
    roll INT PRIMARY KEY,
    name VARCHAR(50),
    marks INT
);

-- Create log table
CREATE TABLE student_log (
    id INT AUTO_INCREMENT PRIMARY KEY,
    action VARCHAR(20),
    roll INT,
    old_marks INT,
    new_marks INT,
    time_stamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Set delimiter for triggers
DELIMITER //

-- AFTER INSERT Trigger
CREATE TRIGGER student_after_insert
AFTER INSERT ON student
FOR EACH ROW
BEGIN
    INSERT INTO student_log (action, roll, new_marks)
    VALUES ('INSERT', NEW.roll, NEW.marks);
END//

-- AFTER UPDATE Trigger
CREATE TRIGGER student_after_update
AFTER UPDATE ON student
FOR EACH ROW
BEGIN
    INSERT INTO student_log (action, roll, old_marks, new_marks)
    VALUES ('UPDATE', NEW.roll, OLD.marks, NEW.marks);
END//

-- AFTER DELETE Trigger
CREATE TRIGGER student_after_delete
AFTER DELETE ON student
FOR EACH ROW
BEGIN
    INSERT INTO student_log (action, roll, old_marks)
    VALUES ('DELETE', OLD.roll, OLD.marks);
END//

-- Reset delimiter
DELIMITER ;

-- Insert sample data (to test INSERT trigger)
INSERT INTO student VALUES 
(1, 'Ramesh', 80),
(2, 'Priya', 90);

-- Update sample data (to test UPDATE trigger)
UPDATE student SET marks = 95 WHERE roll = 1;

-- Delete sample data (to test DELETE trigger)
DELETE FROM student WHERE roll = 2;

-- Show log table output
SELECT * FROM student_log;
