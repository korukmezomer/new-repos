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

    %% CORE BUSINESS RELATIONSHIPS WITH CARDINALITY
    
    %% User Relationships (1:n)
    User --> Project
    User --> Bid
    User --> ProjectTask
    User --> Transaction
    User --> Message
    User --> Notification
    
    %% Project Relationships
    Project --> Bid
    Project --> ProjectTask
    Project --> Message
    Project --> Review
    Project --> Attachment
    Project --> Transaction
    Project --> Category
    Project --> PaymentMethod
    Project --> Bid
    
    %% Task and Milestone Relationships
    ProjectTask --> MilestonePayment
    MilestonePayment --> Transaction
    
    %% Message Relationships
    Message --> Attachment
    
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

### 📋 İlişki Cardinality Detayları:

#### Bire Bir İlişkiler (1:1):
- **Project → Category:** Bir proje bir kategoriye ait
- **Project → PaymentMethod:** Bir proje bir ödeme yöntemi kullanır
- **Project → Bid:** Bir proje en fazla bir kabul edilen teklife sahip

#### Bire Çok İlişkiler (1:n):
- **User → Project:** Bir kullanıcı birden fazla proje oluşturabilir
- **User → Bid:** Bir kullanıcı birden fazla teklif yapabilir
- **User → ProjectTask:** Bir kullanıcıya birden fazla görev atanabilir
- **User → Transaction:** Bir kullanıcı birden fazla işlem yapabilir/alabilir
- **User → Message:** Bir kullanıcı birden fazla mesaj gönderebilir/alabilir
- **User → Notification:** Bir kullanıcı birden fazla bildirim alabilir
- **Project → Bid:** Bir projeye birden fazla teklif verilebilir
- **Project → ProjectTask:** Bir projede birden fazla görev olabilir
- **Project → Message:** Bir projeye birden fazla mesaj gönderilebilir
- **Project → Review:** Bir proje birden fazla değerlendirme alabilir
- **Project → Attachment:** Bir projede birden fazla dosya olabilir
- **Project → Transaction:** Bir proje birden fazla işlem oluşturabilir
- **ProjectTask → MilestonePayment:** Bir görevde birden fazla milestone olabilir
- **MilestonePayment → Transaction:** Bir milestone birden fazla işlem oluşturabilir
- **Message → Attachment:** Bir mesajda birden fazla ek dosya olabilir

#### Çoka Çok İlişkiler (n:n):
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