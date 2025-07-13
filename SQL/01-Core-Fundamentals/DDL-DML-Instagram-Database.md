# 01-Core Fundamentals - Instagram Database ke Saath

Bhai, **SQL fundamentals** seekhenge **Instagram jaise app** ka database banate hue! 🔥

Real scenario: Tumhe **Instagram clone** banana hai. Tables kaise banayenge? Data kaise insert karenge? Update kaise karenge? Sab practical examples ke saath! 💪

---

## 🎯 **What We'll Build: Instagram Database**

### **Tables Banayenge:**
- **USERS** - Profiles, followers count, bio
- **POSTS** - Photos, captions, likes count  
- **COMMENTS** - Post comments, user reactions
- **FOLLOWS** - Who follows whom relationship
- **LIKES** - Post likes tracking

### **Real Operations:**
- New user signup
- Post upload karna
- Comment dalna
- Like/unlike functionality
- Follow/unfollow system

---

## 📊 **DDL - Data Definition Language**

**DDL matlab:** Database structure banane ke commands - tables, columns, constraints sab kuch!

### **CREATE TABLE - Instagram Users**

```sql
-- Instagram Users Table
CREATE TABLE users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(30) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    full_name VARCHAR(100) NOT NULL,
    bio TEXT,
    profile_pic_url VARCHAR(255),
    followers_count INT DEFAULT 0,
    following_count INT DEFAULT 0,
    posts_count INT DEFAULT 0,
    is_verified BOOLEAN DEFAULT FALSE,
    is_private BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

**Real Example:**
```sql
-- Virat Kohli ka Instagram profile
CREATE TABLE users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(30) UNIQUE NOT NULL,          -- 'virat.kohli'
    email VARCHAR(100) UNIQUE NOT NULL,            -- 'virat@gmail.com'
    full_name VARCHAR(100) NOT NULL,               -- 'Virat Kohli'
    bio TEXT,                                      -- 'Cricketer. Fitness enthusiast.'
    followers_count INT DEFAULT 0,                 -- 50000000 (5 crore followers!)
    is_verified BOOLEAN DEFAULT FALSE              -- TRUE (blue tick!)
);
```

### **CREATE TABLE - Instagram Posts**

```sql
-- Instagram Posts Table
CREATE TABLE posts (
    post_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    caption TEXT,
    image_url VARCHAR(255) NOT NULL,
    likes_count INT DEFAULT 0,
    comments_count INT DEFAULT 0,
    location VARCHAR(100),
    is_archived BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE
);
```

**Real Example:**
```sql
-- Virat ka post about match win
INSERT INTO posts VALUES (
    1,                                              -- post_id
    1,                                              -- user_id (Virat)
    'What a match! Team effort! 🏏🇮🇳',              -- caption
    'https://instagram.com/virat_match_pic.jpg',    -- image_url
    500000,                                         -- likes_count (5 lakh likes!)
    25000,                                          -- comments_count
    'Wankhede Stadium, Mumbai',                     -- location
    FALSE                                           -- not archived
);
```

### **CREATE TABLE - Comments System**

```sql
-- Instagram Comments Table
CREATE TABLE comments (
    comment_id INT PRIMARY KEY AUTO_INCREMENT,
    post_id INT NOT NULL,
    user_id INT NOT NULL,
    comment_text TEXT NOT NULL,
    likes_count INT DEFAULT 0,
    parent_comment_id INT,  -- For replies to comments
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (post_id) REFERENCES posts(post_id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE,
    FOREIGN KEY (parent_comment_id) REFERENCES comments(comment_id)
);
```

### **ALTER TABLE - Table Modify Karna**

```sql
-- New feature add karna: Story highlights
ALTER TABLE users ADD COLUMN highlights_count INT DEFAULT 0;

-- Column modify karna
ALTER TABLE users MODIFY COLUMN bio VARCHAR(500);

-- Column name change karna  
ALTER TABLE users CHANGE COLUMN profile_pic_url avatar_url VARCHAR(255);

-- Column drop karna
ALTER TABLE users DROP COLUMN highlights_count;

-- Constraint add karna
ALTER TABLE users ADD CONSTRAINT check_followers 
CHECK (followers_count >= 0);
```

### **DROP TABLE - Table Delete Karna**

```sql
-- Temporary table delete
DROP TABLE temp_posts;

-- Main table delete (careful!)
DROP TABLE posts;  -- Saara data delete ho jaayega!
```

---

## 🔄 **DML - Data Manipulation Language**

**DML matlab:** Data ke saath operations - insert, update, delete, select!

### **INSERT - New Data Add Karna**

#### **Single User Insert:**
```sql
-- Naya user signup kar raha hai
INSERT INTO users (username, email, full_name, bio) 
VALUES (
    'rahul_sharma',
    'rahul@gmail.com', 
    'Rahul Sharma',
    'Photographer | Travel Lover 📸'
);
```

#### **Multiple Users Insert:**
```sql
-- Multiple users ek saath add karna
INSERT INTO users (username, email, full_name, bio, is_verified) VALUES 
('priyanka_chopra', 'priyanka@gmail.com', 'Priyanka Chopra', 'Actor | Producer', TRUE),
('akshaykumar', 'akshay@gmail.com', 'Akshay Kumar', 'Actor | Fitness Freak', TRUE),
('deepikapadukone', 'deepika@gmail.com', 'Deepika Padukone', 'Actor | Mental Health Advocate', TRUE);
```

#### **Instagram Post Upload:**
```sql
-- User apna photo upload kar raha hai
INSERT INTO posts (user_id, caption, image_url, location)
VALUES (
    2,  -- rahul_sharma ka user_id
    'Beautiful sunset at Marine Drive! 🌅 #Mumbai #Photography',
    'https://instagram.com/rahul_sunset_pic.jpg',
    'Marine Drive, Mumbai'
);
```

### **UPDATE - Existing Data Modify Karna**

#### **Profile Update:**
```sql
-- User apna bio update kar raha hai
UPDATE users 
SET bio = 'Professional Photographer | Travel Blogger | Canon Ambassador 📸',
    updated_at = CURRENT_TIMESTAMP
WHERE username = 'rahul_sharma';
```

#### **Post Likes Increase:**
```sql
-- Koi post ko like kar raha hai
UPDATE posts 
SET likes_count = likes_count + 1
WHERE post_id = 1;

-- Multiple posts ki likes update
UPDATE posts 
SET likes_count = likes_count + 100
WHERE user_id = 1 AND created_at >= '2024-01-01';
```

#### **Verification Badge Dena:**
```sql
-- User ko blue tick mil gaya!
UPDATE users 
SET is_verified = TRUE,
    updated_at = CURRENT_TIMESTAMP
WHERE username = 'rahul_sharma' AND followers_count > 100000;
```

### **DELETE - Data Remove Karna**

#### **Specific Comment Delete:**
```sql
-- Spam comment delete kar rahe hain
DELETE FROM comments 
WHERE comment_text LIKE '%spam%' OR comment_text LIKE '%fake%';
```

#### **Old Posts Archive:**
```sql
-- 2 saal purane posts delete karna
DELETE FROM posts 
WHERE created_at < DATE_SUB(CURRENT_DATE, INTERVAL 2 YEAR);
```

#### **Inactive Users Remove:**
```sql
-- Jo users 1 saal se active nahi hain
DELETE FROM users 
WHERE updated_at < DATE_SUB(CURRENT_DATE, INTERVAL 1 YEAR)
AND posts_count = 0;
```

---

## 🔍 **SELECT - Data Retrieve Karna**

### **Basic SELECT Queries:**

#### **All Users List:**
```sql
-- Saare users ka list
SELECT * FROM users;

-- Specific columns only
SELECT username, full_name, followers_count, is_verified 
FROM users;
```

#### **Verified Users Only:**
```sql
-- Sirf blue tick wale users
SELECT username, full_name, followers_count
FROM users 
WHERE is_verified = TRUE
ORDER BY followers_count DESC;
```

#### **Popular Posts:**
```sql
-- Sabse zyada likes wale posts
SELECT p.caption, u.username, p.likes_count, p.created_at
FROM posts p
JOIN users u ON p.user_id = u.user_id
WHERE p.likes_count > 10000
ORDER BY p.likes_count DESC
LIMIT 10;
```

### **WHERE Clause with Real Conditions:**

#### **User Search:**
```sql
-- Username ya name se search
SELECT username, full_name, bio
FROM users 
WHERE username LIKE '%rahul%' 
   OR full_name LIKE '%Rahul%';
```

#### **Recent Posts:**
```sql
-- Last 24 hours ke posts
SELECT p.caption, u.username, p.created_at
FROM posts p
JOIN users u ON p.user_id = u.user_id
WHERE p.created_at >= DATE_SUB(NOW(), INTERVAL 24 HOUR)
ORDER BY p.created_at DESC;
```

#### **Location-based Posts:**
```sql
-- Mumbai ke posts
SELECT caption, location, likes_count
FROM posts 
WHERE location LIKE '%Mumbai%'
ORDER BY likes_count DESC;
```

### **ORDER BY and LIMIT:**

#### **Top Influencers:**
```sql
-- Sabse zyada followers wale users
SELECT username, full_name, followers_count
FROM users 
WHERE is_verified = TRUE
ORDER BY followers_count DESC
LIMIT 20;
```

#### **Recent Comments:**
```sql
-- Latest comments with user info
SELECT c.comment_text, u.username, c.created_at
FROM comments c
JOIN users u ON c.user_id = u.user_id
ORDER BY c.created_at DESC
LIMIT 50;
```

---

## 🎯 **Real-World Instagram Scenarios**

### **Scenario 1: New User Onboarding**
```sql
-- Step 1: User signup
INSERT INTO users (username, email, full_name) 
VALUES ('new_user_123', 'newuser@gmail.com', 'New User');

-- Step 2: Profile setup
UPDATE users 
SET bio = 'Just joined Instagram! 🎉',
    profile_pic_url = 'https://default-avatar.jpg'
WHERE username = 'new_user_123';
```

### **Scenario 2: Viral Post**
```sql
-- Post going viral - likes rapidly increasing
UPDATE posts 
SET likes_count = likes_count + 5000,
    comments_count = comments_count + 500
WHERE post_id = 123;

-- User ki followers bhi badh gayi
UPDATE users 
SET followers_count = followers_count + 1000
WHERE user_id = (SELECT user_id FROM posts WHERE post_id = 123);
```

### **Scenario 3: Content Moderation**
```sql
-- Inappropriate content remove karna
DELETE FROM posts 
WHERE caption LIKE '%inappropriate_content%';

-- Spam comments clean up
DELETE FROM comments 
WHERE comment_text LIKE '%buy now%' 
   OR comment_text LIKE '%click here%';
```

### **Scenario 4: Analytics Query**
```sql
-- Top performing posts of the month
SELECT 
    u.username,
    p.caption,
    p.likes_count,
    p.comments_count,
    (p.likes_count + p.comments_count * 2) AS engagement_score
FROM posts p
JOIN users u ON p.user_id = u.user_id
WHERE p.created_at >= DATE_SUB(CURRENT_DATE, INTERVAL 1 MONTH)
ORDER BY engagement_score DESC
LIMIT 10;
```

---

## 🔥 **Pro Tips for Instagram Database**

### **Performance Optimization:**
```sql
-- Indexes for faster queries
CREATE INDEX idx_posts_user_id ON posts(user_id);
CREATE INDEX idx_posts_created_at ON posts(created_at);
CREATE INDEX idx_users_username ON users(username);
CREATE INDEX idx_comments_post_id ON comments(post_id);
```

### **Data Consistency:**
```sql
-- Triggers for automatic count updates
DELIMITER //
CREATE TRIGGER update_posts_count 
AFTER INSERT ON posts
FOR EACH ROW
BEGIN
    UPDATE users 
    SET posts_count = posts_count + 1 
    WHERE user_id = NEW.user_id;
END//
DELIMITER ;
```

### **Security Constraints:**
```sql
-- Username validation
ALTER TABLE users ADD CONSTRAINT check_username 
CHECK (LENGTH(username) >= 3 AND username REGEXP '^[a-zA-Z0-9_.]+$');

-- Email validation
ALTER TABLE users ADD CONSTRAINT check_email 
CHECK (email REGEXP '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$');
```

---

## 📋 **Practice Exercises**

### **Easy Level:**
1. Instagram mein sabse zyada followers wala user find karo
2. Last week ke saare posts show karo
3. Verified users ki count nikalo

### **Medium Level:**
1. Har user ka average likes per post calculate karo
2. Most commented post find karo
3. Users jo abhi tak koi post nahi kiye

### **Hard Level:**
1. Top 10 engaging users (likes + comments ratio)
2. Monthly growth rate of users
3. Posts with highest engagement rate

---

## 🚀 **Next Steps**

**Completed:** ✅ DDL & DML with Instagram database  
**Next Module:** **02-Query-Mastery** - Complex SELECT queries with Flipkart database

**Real Learning:** Ab tumhe pata chal gaya ki real apps kaise database use karte hain! Instagram jaise platform banane ke liye SQL fundamentals clear hain! 💪

**Ready for next level?** Flipkart ke e-commerce database ke saath advanced querying seekhenge! 🛒⚡
