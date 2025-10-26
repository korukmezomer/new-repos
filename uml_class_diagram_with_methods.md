# UML Sınıf Diyagramı (Metodlarla) - Freelancer Platformu

```mermaid
classDiagram
    class Users {
        +int id
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
        +datetime createdAt
        +datetime updatedAt
        +boolean isActive
        +datetime deletedAt
        +register(name, email, password)
        +login(email, password)
        +updateProfile(bio, skills, profileImage, location)
        +verifyEmail(token)
        +resetPassword(email)
        +changePassword(oldPwd, newPwd)
        +recalcRating(userId)
    }

    class Projects {
        +int id
        +string title
        +text description
        +decimal budgetMin
        +decimal budgetMax
        +date deadline
        +int employerId
        +int acceptedBidId
        +int categoryId
        +enum status
        +string attachment
        +int views
        +datetime createdAt
        +datetime updatedAt
        +boolean isActive
        +datetime deletedAt
        +create(title, description, budgetMin, budgetMax, categoryId, deadline)
        +update(projectId, ...)
        +close(projectId)
        +cancel(projectId)
        +updateStatus(projectId, status)
        +incrementViews(projectId)
        +attachFile(projectId, filePath, type)
        +search(query, filters)
        +browse(page, sort)
        +assignFreelancer(bidId)
    }

    class Categories {
        +int id
        +string name
        +text description
        +datetime createdAt
        +datetime updatedAt
        +boolean isActive
        +datetime deletedAt
        +create(name, description)
        +update(id, name, description)
        +delete(id)
        +list()
    }

    class Bids {
        +int id
        +int projectId
        +int freelancerId
        +decimal price
        +int durationDays
        +text coverLetter
        +enum status
        +datetime createdAt
        +datetime updatedAt
        +datetime deletedAt
        +placeBid(projectId, price, durationDays, coverLetter)
        +accept(bidId)
        +reject(bidId)
        +withdraw(bidId)
        +listByProject(projectId)
    }

    class MileStonePayment {
        +int id
        +int projectId
        +int bidId
        +string milestoneName
        +text description
        +decimal amount
        +enum status
        +datetime dueDate
        +datetime completedDate
        +text completionNote
        +datetime createdAt
        +datetime updatedAt
        +create(projectId, bidId, milestoneName, description, amount, dueDate)
        +markCompleted(milestoneId, completionNote)
        +requestPayment(milestoneId)
        +processPayment(milestoneId)
        +updateStatus(milestoneId, status)
        +listByProject(projectId)
    }

    class Messages {
        +int id
        +int senderId
        +int receiverId
        +int projectId
        +text content
        +datetime dateSent
        +boolean isRead
        +datetime createdAt
        +datetime updatedAt
        +datetime deletedAt
        +sendMessage(projectId, receiverId, content)
        +markRead(messageId)
        +delete(messageId)
        +history(projectId)
        +addAttachment(messageId, filePath)
    }

    class Reviews {
        +int id
        +int projectId
        +int fromUserId
        +int toUserId
        +int rating
        +text comment
        +datetime dateGiven
        +datetime createdAt
        +datetime updatedAt
        +datetime deletedAt
        +addReview(projectId, toUserId, rating, comment)
        +updateReview(reviewId, rating, comment)
        +deleteReview(reviewId)
        +listByUser(userId)
    }

    class Transactions {
        +int id
        +int milestonePaymentId
        +int projectId
        +int payerId
        +int receiverId
        +decimal amount
        +enum status
        +datetime paymentDate
        +enum transactionType
        +datetime createdAt
        +datetime updatedAt
        +processPayment(milestonePaymentId, receiverId, amount)
        +refund(transactionId)
        +updateStatus(transactionId, status)
        +listByProject(projectId)
        +calculateCommission(amount)
    }

    class Notifications {
        +int id
        +int userId
        +string title
        +text message
        +boolean isRead
        +datetime sentAt
        +datetime createdAt
        +datetime updatedAt
        +sendNotification(userId, title, message)
        +markRead(notificationId)
        +delete(notificationId)
        +listByUser(userId)
    }

    class Attachments {
        +int id
        +int projectId
        +int messageId
        +string filePath
        +string fileType
        +datetime uploadedAt
        +datetime createdAt
        +datetime deletedAt
        +uploadFile(projectId, file)
        +download(attachmentId)
        +deleteAttachment(attachmentId)
        +listByProject(projectId)
    }

    class AuditLogs {
        +int id
        +int adminId
        +string actionType
        +string entityType
        +int entityId
        +text details
        +datetime actionDate
        +datetime createdAt
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

    %% Associations - Users
    Users "1" --> "N" Projects : employer
    Users "1" --> "N" Projects : freelancer
    Users "1" --> "N" Bids : freelancer
    Users "1" --> "N" Messages : sender
    Users "1" --> "N" Messages : receiver
    Users "1" --> "N" Reviews : from_user
    Users "1" --> "N" Reviews : to_user
    Users "1" --> "N" Transactions : payer
    Users "1" --> "N" Transactions : receiver
    Users "1" --> "N" Notifications : recipient
    Users "1" --> "N" AuditLogs : admin

    %% Associations - Projects
    Projects "1" --> "N" Bids
    Projects "1" --> "1" Bids : accepted_bid
    Projects "1" --> "N" Messages
    Projects "1" --> "N" Reviews
    Projects "1" --> "N" MileStonePayment
    Projects "1" --> "N" Attachments
    Projects "N" --> "1" Categories

    %% Associations - Bids
    Bids "1" --> "N" MileStonePayment

    %% Associations - MileStonePayment
    MileStonePayment "1" --> "N" Transactions

    %% Associations - Messages
    Messages "1" --> "N" Attachments

    %% Admin service associations
    AdminService ..> Users : manages
    AdminService ..> Projects : manages
    AdminService ..> Bids : monitors
    AdminService ..> Messages : monitors
    AdminService ..> Transactions : monitors
    AdminService ..> Notifications : manages
    AdminService ..> Categories : manages
    AdminService ..> AuditLogs : logs
```
