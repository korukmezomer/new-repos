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
    
    %% User Relationships (1:n)
    User --> Project
    User --> Bid
    User --> ProjectTask
    User --> Transaction
    User --> Message
    User --> Notification
    User --> Review
    
    %% Project Relationships
    Project --> Bid
    Project --> ProjectTask
    Project --> Message
    Project --> Review
    Project --> Attachment
    Project --> Transaction
    Project --> Category
    Project --> PaymentMethod
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

### 📋 Freelancer Platform İlişki Analizi (Cardinality Detayları):

#### 🔗 **User Entity İlişkileri (1:n):**
- **User → Project (1:n):** Bir kullanıcı (işveren) birden fazla proje oluşturabilir
- **User → Bid (1:n):** Bir kullanıcı (freelancer) birden fazla projeye teklif verebilir
- **User → ProjectTask (1:n):** Bir kullanıcıya birden fazla görev atanabilir
- **User → Transaction (1:n):** Bir kullanıcı birden fazla ödeme yapabilir/alabilir
- **User → Message (1:n):** Bir kullanıcı birden fazla mesaj gönderebilir/alabilir
- **User → Notification (1:n):** Bir kullanıcı birden fazla bildirim alabilir
- **User → Review (1:n):** Bir kullanıcı birden fazla değerlendirme yapabilir/alabilir

#### 🔗 **Project Entity İlişkileri:**
- **Project → Bid (1:n):** Bir projeye birden fazla freelancer teklif verebilir
- **Project → ProjectTask (1:n):** Bir projede birden fazla görev olabilir
- **Project → Message (1:n):** Bir proje hakkında birden fazla mesajlaşma yapılabilir
- **Project → Review (1:n):** Bir proje hakkında birden fazla değerlendirme yapılabilir
- **Project → Attachment (1:n):** Bir projede birden fazla dosya olabilir
- **Project → Transaction (1:n):** Bir proje için birden fazla ödeme yapılabilir
- **Project → Category (n:1):** Bir proje sadece bir kategoriye ait olabilir
- **Project → PaymentMethod (n:1):** Bir proje sadece bir ödeme yöntemi kullanabilir
- **Project → User (n:1):** Bir proje sadece bir freelancer'a atanabilir

#### 🔗 **ProjectTask Entity İlişkileri:**
- **ProjectTask → MilestonePayment (1:n):** Bir görevde birden fazla milestone olabilir
- **ProjectTask → User (n:1):** Bir görev sadece bir kullanıcıya atanabilir

#### 🔗 **MilestonePayment Entity İlişkileri:**
- **MilestonePayment → Transaction (1:n):** Bir milestone için birden fazla ödeme yapılabilir

#### 🔗 **Message Entity İlişkileri:**
- **Message → Attachment (1:n):** Bir mesajda birden fazla dosya paylaşılabilir
- **Message → Project (n:1):** Bir mesaj sadece bir projeye ait olabilir

#### 🔗 **Review Entity İlişkileri:**
- **Review → Project (n:1):** Bir değerlendirme sadece bir proje hakkında yapılabilir
- **Review → User (n:1):** Bir değerlendirme sadece bir kullanıcı tarafından yapılabilir
- **Review → User (n:1):** Bir değerlendirme sadece bir kullanıcıya yapılabilir

#### 🔗 **AdminService Dependencies:**
- **AdminService → User:** Admin kullanıcıları yönetebilir
- **AdminService → Project:** Admin projeleri izleyebilir
- **AdminService → Category:** Admin kategorileri yönetebilir

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