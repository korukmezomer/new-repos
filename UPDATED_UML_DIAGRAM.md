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

    %% ENTITY - ENUM RELATIONSHIPS
    User --> UserRole
    Project --> ProjectStatus
    Bid --> BidStatus
    ProjectTask --> TaskStatus
    MilestonePayment --> MilestoneStatus
    PaymentMethod --> PaymentMethodType
    Transaction --> TransactionStatus
    Transaction --> TransactionType

    %% FREELANCER PLATFORM BUSINESS RELATIONSHIPS WITH CARDINALITY
    
    %% User Core Relationships (1:n)
    User --> Project
    User --> Bid
    User --> ProjectTask
    User --> Transaction
    User --> Message
    User --> Notification
    User --> Review
    
    %% Project Core Relationships
    Project --> Bid
    Project --> ProjectTask
    Project --> Message
    Project --> Review
    Project --> Attachment
    Project --> Transaction
    Project --> Category
    Project --> PaymentMethod
    Project --> Bid
    Project --> User
    
    %% Task and Milestone Relationships
    ProjectTask --> MilestonePayment
    ProjectTask --> User
    MilestonePayment --> Transaction
    
    %% Message Relationships
    Message --> Attachment
    Message --> Project
    
    %% Review Relationships
    Review --> Project
    Review --> User
    
    %% Admin Service Dependencies
    AdminService ..> User
    AdminService ..> Project
    AdminService ..> Category
```

## Sadeleştirme Değişiklikleri

### ✅ Yapılan İyileştirmeler:

1. **BaseEntity Tablo Olarak Gösterildi:** BaseEntity artık inheritance okları olmadan normal bir tablo olarak duruyor
2. **İlişki Cardinality Bilgileri:** Her ilişkinin cardinality'si açıklama bölümünde detaylı şekilde belirtildi
3. **İlişki Okları Sadeleştirildi:** Karmaşık syntax yerine basit `-->` okları kullanıldı
4. **Enum İlişkileri Basitleştirildi:** Enum'larla entity'ler arasındaki ilişkiler sadeleştirildi
5. **Çoktan Çok İlişkiler:** User ↔ Review ilişkisi kaldırıldı, sadece Project üzerinden yönetiliyor

### 📋 Freelancer Platform İlişki Analizi:

#### 🏢 İşveren (Employer) İlişkileri:
- **User → Project (1:n):** Bir işveren birden fazla proje oluşturabilir
- **User → Message (1:n):** İşveren projeler hakkında mesajlaşabilir
- **User → Transaction (1:n):** İşveren ödemeler yapar
- **User → Review (1:n):** İşveren freelancer'ları değerlendirir

#### 💼 Freelancer İlişkileri:
- **User → Bid (1:n):** Bir freelancer birden fazla projeye teklif verebilir
- **User → ProjectTask (1:n):** Freelancer'a birden fazla görev atanabilir
- **User → Message (1:n):** Freelancer projeler hakkında mesajlaşabilir
- **User → Transaction (1:n):** Freelancer ödemeler alır
- **User → Review (1:n):** Freelancer işverenleri değerlendirir

#### 📋 Proje Yaşam Döngüsü İlişkileri:
- **Project → Bid (1:n):** Bir projeye birden fazla freelancer teklif verebilir
- **Project → ProjectTask (1:n):** Proje birden fazla görev içerebilir
- **Project → Message (1:n):** Proje hakkında mesajlaşma yapılabilir
- **Project → Review (1:n):** Proje tamamlandıktan sonra değerlendirme yapılabilir
- **Project → Attachment (1:n):** Projeye dosya eklenebilir
- **Project → Transaction (1:n):** Proje için ödemeler yapılabilir
- **Project → Category (n:1):** Proje bir kategoriye ait olmalı
- **Project → PaymentMethod (n:1):** Proje bir ödeme yöntemi kullanmalı
- **Project → Bid (1:1):** Proje sadece bir teklifi kabul edebilir
- **Project → User (n:1):** Proje bir freelancer'a atanır

#### ⚙️ Görev ve Ödeme İlişkileri:
- **ProjectTask → MilestonePayment (1:n):** Görev birden fazla milestone'a sahip olabilir
- **ProjectTask → User (n:1):** Görev bir kullanıcıya atanır
- **MilestonePayment → Transaction (1:n):** Milestone için ödeme yapılabilir

#### 💬 Mesajlaşma İlişkileri:
- **Message → Attachment (1:n):** Mesajda dosya paylaşılabilir
- **Message → Project (n:1):** Mesaj bir projeye ait olmalı

#### ⭐ Değerlendirme İlişkileri:
- **Review → Project (n:1):** Değerlendirme bir proje hakkında yapılır
- **Review → User (n:1):** Değerlendirme bir kullanıcı tarafından yapılır
- **Review → User (n:1):** Değerlendirme bir kullanıcıya yapılır

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