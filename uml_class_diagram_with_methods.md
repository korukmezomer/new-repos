# UML Sınıf Diyagramı - Freelancer Platformu

```mermaid
classDiagram
    class BaseEntity {
        <<abstract>>
        +int id
        +datetime createdAt
        +datetime updatedAt
        +boolean isActive
        +datetime deletedAt
    }

    class Users {
        +string name
        +string email
        +string password
        +enum role
        +text bio
        +text skills
        +string profileImage
        +float ratingAverage
        +int totalReviews
        +string location
        +boolean isVerified
        +decimal balance
        +register()
        +login()
        +updateProfile()
        +recalcRating()
    }

    class Projects {
        +string title
        +text description
        +decimal budgetMin
        +decimal budgetMax
        +date deadline
        +enum status
        +string attachment
        +int views
        +int employerId *FK
        +int freelancerId *FK
        +int acceptedBidId *FK
        +int categoryId *FK
        +int paymentMethodId *FK
        +create()
        +updateStatus()
        +assignFreelancer()
    }

    class Categories {
        +string name
        +text description
        +create()
        +update()
    }

    class Bids {
        +decimal price
        +int durationDays
        +text coverLetter
        +enum status
        +int projectId *FK
        +int freelancerId *FK
        +placeBid()
        +accept()
        +reject()
    }

    class ProjectTasks {
        +string title
        +text description
        +enum status
        +int priority
        +datetime dueDate
        +datetime completedDate
        +int projectId *FK
        +int assignedTo *FK
        +create()
        +updateStatus()
        +assignTask()
        +completeTask()
    }

    class PaymentMethods {
        +string name
        +text description
        +enum type
        +boolean isActive
        +decimal feePercentage
        +create()
        +update()
    }

    class MileStonePayments {
        +string milestoneName
        +text description
        +decimal amount
        +enum status
        +int sequence
        +datetime dueDate
        +datetime completedDate
        +text completionNote
        +int projectId *FK
        +int taskId *FK
        +create()
        +markCompleted()
        +requestPayment()
    }

    class Transactions {
        +decimal amount
        +enum status
        +enum transactionType
        +datetime paymentDate
        +string transactionRef
        +int milestonePaymentId *FK
        +int projectId *FK
        +int payerId *FK
        +int receiverId *FK
        +processPayment()
        +refund()
    }

    class Messages {
        +text content
        +boolean isRead
        +datetime dateSent
        +int senderId *FK
        +int receiverId *FK
        +int projectId *FK
        +sendMessage()
        +markRead()
    }

    class Reviews {
        +int rating
        +text comment
        +datetime dateGiven
        +int projectId *FK
        +int fromUserId *FK
        +int toUserId *FK
        +addReview()
        +updateReview()
    }

    class Notifications {
        +string title
        +text message
        +boolean isRead
        +datetime sentAt
        +int userId *FK
        +sendNotification()
        +markRead()
    }

    class Attachments {
        +string filePath
        +string fileType
        +string fileName
        +datetime uploadedAt
        +int projectId *FK
        +int messageId *FK
        +uploadFile()
        +download()
    }

    class AuditLogs {
        +string actionType
        +string entityType
        +text details
        +datetime actionDate
        +int adminId *FK
        +log()
    }

    class AdminService {
        +deactivateUser()
        +viewSystemStats()
        +manageCategories()
    }

    %% INHERITANCE
    BaseEntity <|-- Users
    BaseEntity <|-- Projects
    BaseEntity <|-- Categories
    BaseEntity <|-- Bids
    BaseEntity <|-- ProjectTasks
    BaseEntity <|-- PaymentMethods
    BaseEntity <|-- MileStonePayments
    BaseEntity <|-- Transactions
    BaseEntity <|-- Messages
    BaseEntity <|-- Reviews
    BaseEntity <|-- Notifications
    BaseEntity <|-- Attachments
    BaseEntity <|-- AuditLogs

    %% RELATIONSHIPS
    Users ||--o{ Projects
    Users ||--o{ Bids
    Users ||--o{ ProjectTasks
    Users ||--o{ Transactions
    Users ||--o{ Messages
    Users ||--o{ Reviews
    Users ||--o{ Notifications
    Users ||--o{ AuditLogs
    
    Projects ||--o{ Bids
    Projects ||--o{ ProjectTasks
    Projects ||--o{ MileStonePayments
    Projects ||--o{ Transactions
    Projects ||--o{ Messages
    Projects ||--o{ Reviews
    Projects ||--o{ Attachments
    Projects }o--|| Bids
    Projects }o--|| PaymentMethods
    
    Categories ||--o{ Projects
    
    Bids ||--o{ MileStonePayments
    
    ProjectTasks ||--o{ MileStonePayments
    ProjectTasks }o--|| Users
    
    MileStonePayments ||--o{ Transactions
    
    Messages ||--o{ Attachments
    
    AdminService ..> Users
    AdminService ..> Projects
    AdminService ..> Categories
```
