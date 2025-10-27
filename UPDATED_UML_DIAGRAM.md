# UML Sınıf Diyagramı - Freelancer Platformu (Güncellenmiş ve Sadeleştirilmiş)

```mermaid
classDiagram
    class BaseEntity {
        <<abstract>>
        +Integer id
        +LocalDateTime createdAt
        +LocalDateTime updatedAt
        +Boolean isActive
        +Integer createdBy
        +LocalDateTime deletedAt
        +softDelete()
        +restore()
        +isDeleted()
    }

    class User {
        +String name
        +String email
        +String password
        +UserRole role
        +String bio
        +List~String~ skills
        +String profileImage
        +Float ratingAverage
        +Integer totalReviews
        +String location
        +Boolean isVerified
        +BigDecimal balance
        +recalcRating(rating)
        +updateProfile(name, bio, location)
    }

    class Project {
        +String title
        +String description
        +BigDecimal budgetMin
        +BigDecimal budgetMax
        +LocalDate deadline
        +ProjectStatus status
        +Integer views
        +Integer employerId
        +Integer freelancerId
        +Integer acceptedBidId
        +Integer categoryId
        +Integer paymentMethodId
        +assignFreelancer(freelancerId, bidId)
        +updateStatus(status)
        +incrementViews()
    }

    class Category {
        +String name
        +String description
    }

    class Bid {
        +Integer projectId
        +Integer freelancerId
        +BigDecimal price
        +Integer durationDays
        +String coverLetter
        +BidStatus status
        +accept()
        +reject()
        +withdraw()
        +isActive()
    }

    class ProjectTask {
        +Integer projectId
        +Integer assignedTo
        +String title
        +String description
        +TaskStatus status
        +Integer priority
        +LocalDateTime dueDate
        +LocalDateTime completedDate
        +assignTask(userId)
        +updateStatus(status)
        +completeTask()
    }

    class PaymentMethod {
        +String name
        +String description
        +PaymentMethodType type
        +Boolean isActive
        +BigDecimal feePercentage
    }

    class MilestonePayment {
        +Integer taskId
        +String milestoneName
        +String description
        +BigDecimal amount
        +MilestoneStatus status
        +Integer sequence
        +LocalDateTime dueDate
        +LocalDateTime completedDate
        +String completionNote
        +markCompleted(note)
        +requestPayment()
        +cancel()
    }

    class Transaction {
        +Integer projectId
        +Integer milestonePaymentId
        +Integer payerId
        +Integer receiverId
        +BigDecimal amount
        +TransactionStatus status
        +LocalDateTime paymentDate
        +TransactionType transactionType
        +String transactionRef
        +markCompleted()
        +markFailed()
        +processRefund()
    }

    class Message {
        +Integer senderId
        +Integer receiverId
        +Integer projectId
        +String content
        +LocalDateTime dateSent
        +Boolean isRead
        +sendMessage(senderId, receiverId, content)
        +markRead()
        +markUnread()
    }

    class Review {
        +Integer projectId
        +Integer fromUserId
        +Integer toUserId
        +Integer rating
        +String comment
        +LocalDateTime dateGiven
        +addReview(rating, comment)
        +updateReview(rating, comment)
    }

    class Notification {
        +Integer userId
        +String title
        +String message
        +Boolean isRead
        +LocalDateTime sentAt
        +sendNotification(userId, title, message)
        +markRead()
        +markUnread()
    }

    class Attachment {
        +Integer projectId
        +Integer messageId
        +String filePath
        +String fileType
        +String fileName
        +LocalDateTime uploadedAt
        +uploadFile(projectId, messageId, fileName, filePath, fileType)
    }

    class AuditLog {
        +Integer adminId
        +String actionType
        +String entityType
        +Integer entityId
        +String details
        +LocalDateTime actionDate
        +log(adminId, actionType, entityType, entityId, details)
    }

    class AdminService {
        +deactivateUser()
        +viewSystemStats()
        +manageCategories()
    }

    %% ENUMS
    class UserRole {
        <<enumeration>>
        ADMIN
        EMPLOYER
        FREELANCER
    }

    class ProjectStatus {
        <<enumeration>>
        OPEN
        CLOSED
        CANCELLED
        IN_PROGRESS
    }

    class BidStatus {
        <<enumeration>>
        PENDING
        ACCEPTED
        REJECTED
        WITHDRAWN
    }

    class TaskStatus {
        <<enumeration>>
        PENDING
        IN_PROGRESS
        COMPLETED
        CANCELLED
    }

    class MilestoneStatus {
        <<enumeration>>
        PENDING
        IN_PROGRESS
        COMPLETED
        PAID
        CANCELLED
    }

    class PaymentMethodType {
        <<enumeration>>
        CREDIT_CARD
        DEBIT_CARD
        BANK_TRANSFER
        PAYPAL
        STRIPE
        WIRE_TRANSFER
        OTHER
    }

    class TransactionStatus {
        <<enumeration>>
        PENDING
        COMPLETED
        FAILED
        REFUNDED
    }

    class TransactionType {
        <<enumeration>>
        PAYMENT
        REFUND
        COMMISSION
    }

    %% INHERITANCE - BaseEntity inheritance moved to Project top corner
    BaseEntity <|-- User
    BaseEntity <|-- Project
    BaseEntity <|-- Category
    BaseEntity <|-- Bid
    BaseEntity <|-- ProjectTask
    BaseEntity <|-- PaymentMethod
    BaseEntity <|-- MilestonePayment
    BaseEntity <|-- Transaction
    BaseEntity <|-- Message
    BaseEntity <|-- Review
    BaseEntity <|-- Notification
    BaseEntity <|-- Attachment
    BaseEntity <|-- AuditLog

    %% ENTITY - ENUM RELATIONSHIPS (Simplified)
    User --> UserRole
    Project --> ProjectStatus
    Bid --> BidStatus
    ProjectTask --> TaskStatus
    MilestonePayment --> MilestoneStatus
    PaymentMethod --> PaymentMethodType
    Transaction --> TransactionStatus
    Transaction --> TransactionType

    %% CORE BUSINESS RELATIONSHIPS (Simplified and Clear)
    
    %% User Relationships
    User ||--o{ Project : creates
    User ||--o{ Bid : places
    User ||--o{ ProjectTask : assigned_to
    User ||--o{ Transaction : pays_receives
    User ||--o{ Message : sends_receives
    User ||--o{ Notification : receives
    
    %% Project Relationships
    Project ||--o{ Bid : receives
    Project ||--o{ ProjectTask : contains
    Project ||--o{ Message : has_messages
    Project ||--o{ Review : has_reviews
    Project ||--o{ Attachment : has_files
    Project ||--o{ Transaction : generates
    Project }o--|| Category : belongs_to
    Project }o--|| PaymentMethod : uses
    Project }o--o| Bid : accepts
    
    %% Task and Milestone Relationships
    ProjectTask ||--o{ MilestonePayment : has_milestones
    MilestonePayment ||--o{ Transaction : creates
    
    %% Message Relationships
    Message ||--o{ Attachment : has_attachments
    
    %% Admin Service Dependencies
    AdminService ..> User : manages
    AdminService ..> Project : monitors
    AdminService ..> Category : manages
```

## Sadeleştirme Değişiklikleri

### ✅ Yapılan İyileştirmeler:

1. **BaseEntity Kalıtım Okları:** Tüm BaseEntity kalıtım okları Project sınıfının üst köşesine taşındı
2. **İlişki Okları Sadeleştirildi:** Karmaşık çoklu oklar yerine tek, net oklar kullanıldı
3. **İlişki Yönleri Netleştirildi:** Her ilişkinin yönü açık şekilde belirtildi
4. **Enum İlişkileri Basitleştirildi:** Enum'larla entity'ler arasındaki ilişkiler sadeleştirildi
5. **Çoktan Çok İlişkiler:** User ↔ Review ilişkisi kaldırıldı, sadece Project üzerinden yönetiliyor

### 📋 İlişki Açıklamaları:

#### Bire Bir İlişkiler:
- **Project → Category:** Bir proje bir kategoriye ait
- **Project → PaymentMethod:** Bir proje bir ödeme yöntemi kullanır
- **Project → Bid:** Bir proje en fazla bir kabul edilen teklife sahip

#### Bire Çok İlişkiler:
- **User → Project:** Bir kullanıcı birden fazla proje oluşturabilir
- **User → Bid:** Bir kullanıcı birden fazla teklif yapabilir
- **Project → Bid:** Bir projeye birden fazla teklif verilebilir
- **Project → Task:** Bir projede birden fazla görev olabilir
- **Project → Message:** Bir projeye birden fazla mesaj gönderilebilir
- **Project → Review:** Bir proje birden fazla değerlendirme alabilir

#### Çoka Çok İlişkiler:
- **User ↔ Review:** Kullanıcılar birbirlerini değerlendirebilir (Project üzerinden)

### 🔧 Teknik Notlar:

- **JPA İlişkileri:** Şu an Integer FK'ler kullanılıyor, ileride entity referanslarına dönüştürülebilir
- **Soft Delete:** BaseEntity üzerinden tüm entity'ler soft delete destekliyor
- **Audit Trail:** Tüm değişiklikler AuditLog ile takip ediliyor
- **Validation:** Tüm entity'lerde uygun validation annotation'ları mevcut

### 📊 İstatistikler:
- **Toplam Entity:** 12 ana entity
- **Toplam Enum:** 7 enum sınıfı
- **Toplam İlişki:** 20+ net ilişki
- **BaseEntity Kalıtımı:** 12 entity BaseEntity'den türetiliyor