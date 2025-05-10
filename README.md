# wk-8-
-- Database: LibraryManagementSystem
-- Description: This system manages books, members, and borrowing activities of a library.
-- Author: Gemini AI
-- Date: May 10, 2025

-- Drop existing tables if they exist to start fresh
DROP TABLE IF EXISTS BookLoans;
DROP TABLE IF EXISTS Books;
DROP TABLE IF EXISTS Members;
DROP TABLE IF EXISTS Authors;
DROP TABLE IF EXISTS Genres;
DROP TABLE IF EXISTS BookAuthors;
DROP TABLE IF EXISTS BookGenres;

-- Table: Authors
CREATE TABLE Authors (
    AuthorID INT PRIMARY KEY AUTO_INCREMENT,
    FirstName VARCHAR(100) NOT NULL,
    LastName VARCHAR(100) NOT NULL,
    Nationality VARCHAR(50)
);

-- Table: Genres
CREATE TABLE Genres (
    GenreID INT PRIMARY KEY AUTO_INCREMENT,
    GenreName VARCHAR(50) UNIQUE NOT NULL
);

-- Table: Books
CREATE TABLE Books (
    BookID INT PRIMARY KEY AUTO_INCREMENT,
    Title VARCHAR(255) NOT NULL,
    ISBN VARCHAR(20) UNIQUE NOT NULL,
    PublicationYear INT,
    Publisher VARCHAR(100),
    TotalCopies INT NOT NULL DEFAULT 1,
    AvailableCopies INT NOT NULL DEFAULT 1,
    CHECK (TotalCopies >= 0),
    CHECK (AvailableCopies >= 0 AND AvailableCopies <= TotalCopies)
);

-- Table: Members
CREATE TABLE Members (
    MemberID INT PRIMARY KEY AUTO_INCREMENT,
    FirstName VARCHAR(100) NOT NULL,
    LastName VARCHAR(100) NOT NULL,
    MembershipDate DATE NOT NULL,
    Email VARCHAR(100) UNIQUE,
    Phone VARCHAR(20) UNIQUE
);

-- Table: BookAuthors (Many-to-Many relationship between Books and Authors)
CREATE TABLE BookAuthors (
    BookID INT,
    AuthorID INT,
    PRIMARY KEY (BookID, AuthorID),
    FOREIGN KEY (BookID) REFERENCES Books(BookID) ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY (AuthorID) REFERENCES Authors(AuthorID) ON DELETE CASCADE ON UPDATE CASCADE
);

-- Table: BookGenres (Many-to-Many relationship between Books and Genres)
CREATE TABLE BookGenres (
    BookID INT,
    GenreID INT,
    PRIMARY KEY (BookID, GenreID),
    FOREIGN KEY (BookID) REFERENCES Books(BookID) ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY (GenreID) REFERENCES Genres(GenreID) ON DELETE CASCADE ON UPDATE CASCADE
);

-- Table: BookLoans (One-to-Many relationship between Members and BookLoans, One-to-Many between Books and BookLoans)
CREATE TABLE BookLoans (
    LoanID INT PRIMARY KEY AUTO_INCREMENT,
    BookID INT NOT NULL,
    MemberID INT NOT NULL,
    LoanDate DATE NOT NULL,
    DueDate DATE NOT NULL,
    ReturnDate DATE,
    FOREIGN KEY (BookID) REFERENCES Books(BookID) ON DELETE RESTRICT ON UPDATE CASCADE,
    FOREIGN KEY (MemberID) REFERENCES Members(MemberID) ON DELETE RESTRICT ON UPDATE CASCADE,
    CHECK (DueDate >= LoanDate),
    CHECK (ReturnDate IS NULL OR ReturnDate >= LoanDate)
);

-- Sample Data (Optional - for testing)
INSERT INTO Authors (FirstName, LastName, Nationality) VALUES
('Jane', 'Austen', 'British'),
('George', 'Orwell', 'British'),
('Chinua', 'Achebe', 'Nigerian');

INSERT INTO Genres (GenreName) VALUES
('Fiction'),
('Science Fiction'),
('Historical Fiction'),
('African Literature');

INSERT INTO Books (Title, ISBN, PublicationYear, Publisher, TotalCopies, AvailableCopies) VALUES
('Pride and Prejudice', '978-0141439518', 1813, 'Penguin Classics', 5, 5),
('Nineteen Eighty-Four', '978-0451524935', 1949, 'Signet Classics', 3, 3),
('Things Fall Apart', '978-0385474542', 1958, 'Anchor Books', 4, 4);

INSERT INTO Members (FirstName, LastName, MembershipDate, Email, Phone) VALUES
('Alice', 'Smith', '2024-01-15', 'alice.smith@example.com', '254712345678'),
('Bob', 'Johnson', '2024-03-20', 'bob.johnson@example.com', '254798765432');

INSERT INTO BookAuthors (BookID, AuthorID) VALUES
(1, 1),
(2, 2),
(3, 3);

INSERT INTO BookGenres (BookID, GenreID) VALUES
(1, 1),
(3, 1),
(2, 2),
(3, 4);

INSERT INTO BookLoans (BookID, MemberID, LoanDate, DueDate) VALUES
(1, 1, '2025-05-01', '2025-05-15'),
(2, 2, '2025-05-05', '2025-05-19');
