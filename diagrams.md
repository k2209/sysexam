# UML Diagrams

## 1. UML Class Diagram

A simple class diagram for an **Online Library System**.

```mermaid
classDiagram
    class User {
        +int id
        +String name
        +String email
        +login()
        +logout()
    }

    class Member {
        +String membershipId
        +Date expiryDate
        +borrowBook()
        +returnBook()
    }

    class Librarian {
        +String staffId
        +addBook()
        +removeBook()
        +manageMembers()
    }

    class Book {
        +int bookId
        +String title
        +String author
        +boolean isAvailable
        +getDetails()
    }

    class BorrowRecord {
        +int recordId
        +Date borrowDate
        +Date returnDate
        +getStatus()
    }

    User <|-- Member
    User <|-- Librarian
    Member "1" --> "0..*" BorrowRecord : has
    BorrowRecord "1" --> "1" Book : refers to
    Librarian "1" --> "0..*" Book : manages
```

---

## 2. Use Case Diagram

A simple use case diagram for the same **Online Library System**.

```mermaid
graph TB
    subgraph Library System
        UC1([Search Book])
        UC2([Borrow Book])
        UC3([Return Book])
        UC4([View Borrow History])
        UC5([Add Book])
        UC6([Remove Book])
        UC7([Manage Members])
        UC8([Generate Reports])
    end

    Member((Member)) --> UC1
    Member --> UC2
    Member --> UC3
    Member --> UC4

    Librarian((Librarian)) --> UC1
    Librarian --> UC5
    Librarian --> UC6
    Librarian --> UC7
    Librarian --> UC8
```

> **Note:** These diagrams use [Mermaid](https://mermaid.js.org/) syntax.
> They render automatically in GitHub, GitLab, Notion, Obsidian, and many other markdown viewers.

---

## Summary

| Diagram        | Purpose                                      |
|----------------|----------------------------------------------|
| Class Diagram  | Shows structure — classes, attributes, relationships |
| Use Case Diagram | Shows behavior — who does what in the system |
