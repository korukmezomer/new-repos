classDiagram
    class BaseEntity {
        +int id
        +datetime createdAt
        +datetime updatedAt
        +boolean isActive
        +int createdBy
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
        +register(name, email, password)
        +login(email, password)
        +updateProfile(bio, skills, profileImage, location)
        +verifyEmail(token)
        +resetPassword(email)
        +changePassword(oldPwd, newPwd)
        +recalcRating(userId)
    }

    class Projects {
        +string title
        +text description
        +decimal budgetMin
        +decimal budgetMax
        +date deadline
        +int employerId
        +int categoryId
        +enum status
        +string attachment
        +int views
        +create(title, description, budgetMin, budgetMax, categoryId, deadline)
        +update(projectId, ...)
        +close(projectId)
        +cancel(projectId)
        +updateStatus(projectId, status)
        +incrementViews(projectId)
        +attachFile(projectId, filePath, type)
        +search(query, filters)
        +browse(page, sort)
    }

    class Categories {
        +string name
        +text description
        +create(name, description)
        +update(id, name, description)
        +delete(id)
        +list()
    }

    class Bids {
        +int projectId
        +int freelancerId
        +decimal price
        +int durationDays
        +text coverLetter
        +enum status
        +placeBid(projectId, price, durationDays, coverLetter)
        +accept(bidId)
        +reject(bidId)
        +withdraw(bidId)
        +listByProject(projectId)
    }

    class Messages {
        +int senderId
        +int receiverId
        +int projectId
        +text content
        +datetime dateSent
        +boolean isRead
        +sendMessage(projectId, receiverId, content)
        +markRead(messageId)
        +delete(messageId)
        +history(projectId)
        +addAttachment(messageId, filePath)
    }

    class Reviews {
        +int projectId
        +int fromUserId
        +int toUserId
        +int rating
        +text comment
        +datetime dateGiven
        +addReview(projectId, toUserId, rating, comment)
        +updateReview(reviewId, rating, comment)
        +deleteReview(reviewId)
        +listByUser(userId)
    }

    class Transactions {
        +int projectId
        +int payerId
        +int receiverId
        +int milestoneId
        +decimal amount
        +enum status
        +datetime paymentDate
        +enum transactionType
        +processPayment(projectId, receiverId, amount)
        +refund(transactionId)
        +updateStatus(transactionId, status)
        +listByProject(projectId)
        +calculateCommission(amount)
    }

    class PaymentPlans {
        +int projectId
        +string planType  %% "upfront", "half", "milestone"
        +decimal totalAmount
        +decimal paidAmount
        +enum status
        +createPlan(projectId, planType, totalAmount)
        +updateProgress(planId, paidAmount)
        +completePlan(planId)
        +getOutstandingAmount(planId)
    }

    class Milestones {
        +int paymentPlanId
        +string description
        +decimal amount
        +date dueDate
        +enum status  %% "pending", "released", "cancelled"
        +createMilestone(paymentPlanId, description, amount, dueDate)
        +releasePayment(milestoneId)
        +cancelMilestone(milestoneId)
        +listByProject(projectId)
    }

    class Notifications {
        +int userId
        +string title
        +text message
        +boolean isRead
        +datetime sentAt
        +sendNotification(userId, title, message)
        +markRead(notificationId)
        +delete(notificationId)
        +listByUser(userId)
    }

    class Attachments {
        +int projectId
        +int messageId
        +string filePath
        +string fileType
        +datetime uploadedAt
        +uploadFile(projectId, file)
        +download(attachmentId)
        +deleteAttachment(attachmentId)
        +listByProject(projectId)
    }

    class AuditLogs {
        +int adminId
        +string actionType
        +string entityType
        +int entityId
        +text details
        +datetime actionDate
        +log(adminId, actionType, entityType, entityId, details)
        +list(filter)
    }

    class AdminService {
        +deactivateUser(userId)
        +activateUser(userId)
        +removeProject(projectId)
        +reviewReport(reportId)
        +viewSystemStats()
    }

    %% Inheritance
    BaseEntity <|-- Users
    BaseEntity <|-- Projects
    BaseEntity <|-- Categories
    BaseEntity <|-- Bids
    BaseEntity <|-- Messages
    BaseEntity <|-- Reviews
    BaseEntity <|-- Transactions
    BaseEntity <|-- Notifications
    BaseEntity <|-- Attachments
    BaseEntity <|-- AuditLogs
    BaseEntity <|-- PaymentPlans
    BaseEntity <|-- Milestones

    %% Associations
    Users "1" --> "N" Projects : oluşturur
    Users "1" --> "N" Bids
    Users "1" --> "N" Messages
    Users "1" --> "N" Reviews
    Users "1" --> "N" Transactions
    Users "1" --> "N" Notifications
    Users "1" --> "N" AuditLogs

    Categories "1" --> "N" Projects

    Projects "1" --> "N" Bids
    Projects "1" --> "N" Messages
    Projects "1" --> "N" Reviews
    Projects "1" --> "N" Transactions
    Projects "1" --> "N" Attachments
    Projects "1" --> "1" PaymentPlans

    PaymentPlans "1" --> "N" Milestones
    Milestones "1" --> "N" Transactions

    Messages "1" --> "N" Attachments
    
    %% Admin service associations
    AdminService ..> Users : yönetir
    AdminService ..> Projects : yönetir
    AdminService ..> Bids : denetler
    AdminService ..> Messages : denetler
    AdminService ..> Transactions : denetler
    AdminService ..> Notifications : yönetir
    AdminService ..> Categories : yönetir
    AdminService ..> AuditLogs : kayıt tutar

