# 02-Query Mastery - Flipkart E-commerce Database ke Saath

Bhai, ab **advanced querying** ka asli maza aayega! 🔥 **Flipkart jaise e-commerce platform** ka database banayenge aur **complex business queries** likhenge!

Real scenario: Tumhe **Flipkart ka backend developer** banana hai. Product search kaise optimize kare? Sales analytics kaise nikale? Customer behavior kaise analyze kare? Sab industry-level examples ke saath! 💪

---

## 🎯 **What We'll Build: Flipkart E-commerce Database**

### **Tables Structure:**
- **CUSTOMERS** - User profiles, addresses, preferences
- **PRODUCTS** - Items, categories, pricing, ratings
- **ORDERS** - Purchase history, payment details
- **ORDER_ITEMS** - Individual items in each order
- **CATEGORIES** - Product categorization
- **SELLERS** - Vendor/seller information
- **REVIEWS** - Product reviews and ratings

### **Real Business Queries:**
- Product search with filters (price, rating, category)
- Sales analytics (revenue, top products, trends)
- Customer segmentation (VIP customers, frequent buyers)
- Inventory insights (low stock, bestsellers)
- Recommendation engine data

---

## 🏗️ **Flipkart Database Schema**

### **CUSTOMERS Table:**
```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(100) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    phone VARCHAR(15),
    date_of_birth DATE,
    gender ENUM('Male', 'Female', 'Other'),
    total_orders INT DEFAULT 0,
    total_spent DECIMAL(12,2) DEFAULT 0.00,
    loyalty_points INT DEFAULT 0,
    is_prime_member BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_login TIMESTAMP
);
```

### **CATEGORIES Table:**
```sql
CREATE TABLE categories (
    category_id INT PRIMARY KEY AUTO_INCREMENT,
    category_name VARCHAR(100) NOT NULL,
    parent_category_id INT,
    description TEXT,
    is_active BOOLEAN DEFAULT TRUE,
    
    FOREIGN KEY (parent_category_id) REFERENCES categories(category_id)
);
```

### **SELLERS Table:**
```sql
CREATE TABLE sellers (
    seller_id INT PRIMARY KEY AUTO_INCREMENT,
    seller_name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone VARCHAR(15),
    address TEXT,
    gst_number VARCHAR(15),
    rating DECIMAL(3,2) DEFAULT 0.00,
    total_products INT DEFAULT 0,
    is_verified BOOLEAN DEFAULT FALSE,
    commission_rate DECIMAL(4,2) DEFAULT 5.00,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### **PRODUCTS Table:**
```sql
CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    seller_id INT NOT NULL,
    category_id INT NOT NULL,
    product_name VARCHAR(200) NOT NULL,
    description TEXT,
    brand VARCHAR(100),
    model VARCHAR(100),
    price DECIMAL(10,2) NOT NULL,
    discount_percentage DECIMAL(5,2) DEFAULT 0.00,
    final_price DECIMAL(10,2) GENERATED ALWAYS AS (price * (1 - discount_percentage/100)) STORED,
    stock_quantity INT DEFAULT 0,
    min_stock_level INT DEFAULT 10,
    average_rating DECIMAL(3,2) DEFAULT 0.00,
    total_reviews INT DEFAULT 0,
    image_urls JSON,
    specifications JSON,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (seller_id) REFERENCES sellers(seller_id),
    FOREIGN KEY (category_id) REFERENCES categories(category_id)
);
```

### **ORDERS Table:**
```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    total_amount DECIMAL(12,2) NOT NULL,
    discount_applied DECIMAL(10,2) DEFAULT 0.00,
    shipping_charge DECIMAL(8,2) DEFAULT 0.00,
    final_amount DECIMAL(12,2) NOT NULL,
    payment_method ENUM('Credit Card', 'Debit Card', 'UPI', 'Net Banking', 'COD', 'Wallet') NOT NULL,
    payment_status ENUM('Pending', 'Completed', 'Failed', 'Refunded') DEFAULT 'Pending',
    order_status ENUM('Placed', 'Confirmed', 'Shipped', 'Delivered', 'Cancelled', 'Returned') DEFAULT 'Placed',
    shipping_address TEXT NOT NULL,
    estimated_delivery DATE,
    actual_delivery TIMESTAMP,
    
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);
```

### **ORDER_ITEMS Table:**
```sql
CREATE TABLE order_items (
    order_item_id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    unit_price DECIMAL(10,2) NOT NULL,
    total_price DECIMAL(12,2) NOT NULL,
    
    FOREIGN KEY (order_id) REFERENCES orders(order_id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

### **REVIEWS Table:**
```sql
CREATE TABLE reviews (
    review_id INT PRIMARY KEY AUTO_INCREMENT,
    product_id INT NOT NULL,
    customer_id INT NOT NULL,
    order_id INT NOT NULL,
    rating INT CHECK (rating >= 1 AND rating <= 5),
    review_text TEXT,
    is_verified_purchase BOOLEAN DEFAULT TRUE,
    helpful_count INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (product_id) REFERENCES products(product_id),
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id),
    FOREIGN KEY (order_id) REFERENCES orders(order_id),
    
    UNIQUE KEY unique_customer_product_order (customer_id, product_id, order_id)
);
```

---

## 📊 **Sample Data Insertion - Flipkart Style**

### **Categories Data:**
```sql
-- Main categories insert
INSERT INTO categories (category_name, parent_category_id, description) VALUES 
('Electronics', NULL, 'Electronic gadgets and devices'),
('Fashion', NULL, 'Clothing and accessories'),
('Home & Kitchen', NULL, 'Home appliances and kitchenware'),
('Books', NULL, 'Books and educational material'),
('Sports', NULL, 'Sports and fitness equipment');

-- Sub-categories
INSERT INTO categories (category_name, parent_category_id, description) VALUES 
('Smartphones', 1, 'Mobile phones and accessories'),
('Laptops', 1, 'Computers and laptops'),
('Mens Clothing', 2, 'Clothing for men'),
('Womens Clothing', 2, 'Clothing for women'),
('Kitchen Appliances', 3, 'Kitchen gadgets and appliances');
```

### **Sellers Data:**
```sql
-- Popular sellers add karna
INSERT INTO sellers (seller_name, email, phone, rating, is_verified) VALUES 
('Samsung Official Store', 'samsung@flipkart.com', '9876543210', 4.5, TRUE),
('Apple Authorized Store', 'apple@flipkart.com', '9876543211', 4.8, TRUE),
('Fashion Hub', 'fashion@hub.com', '9876543212', 4.2, TRUE),
('Electronics World', 'electronics@world.com', '9876543213', 4.0, TRUE),
('Kitchen Kings', 'kitchen@kings.com', '9876543214', 4.3, TRUE);
```

### **Customers Data:**
```sql
-- Customer profiles create karna
INSERT INTO customers (email, password_hash, first_name, last_name, phone, gender, is_prime_member) VALUES 
('rahul.sharma@gmail.com', 'hashed_password_1', 'Rahul', 'Sharma', '9123456789', 'Male', TRUE),
('priya.singh@gmail.com', 'hashed_password_2', 'Priya', 'Singh', '9123456790', 'Female', FALSE),
('amit.kumar@gmail.com', 'hashed_password_3', 'Amit', 'Kumar', '9123456791', 'Male', TRUE),
('sneha.patel@gmail.com', 'hashed_password_4', 'Sneha', 'Patel', '9123456792', 'Female', TRUE),
('vikash.gupta@gmail.com', 'hashed_password_5', 'Vikash', 'Gupta', '9123456793', 'Male', FALSE);
```

### **Products Data:**
```sql
-- Popular products add karna
INSERT INTO products (seller_id, category_id, product_name, brand, price, discount_percentage, stock_quantity, average_rating, total_reviews) VALUES 
(1, 6, 'Samsung Galaxy S24 Ultra 256GB', 'Samsung', 124999.00, 10.00, 50, 4.5, 1250),
(2, 6, 'iPhone 15 Pro 128GB', 'Apple', 134900.00, 5.00, 30, 4.7, 890),
(1, 7, 'Samsung Galaxy Book Pro 15.6"', 'Samsung', 85999.00, 15.00, 25, 4.3, 450),
(3, 8, 'Levis Mens Jeans', 'Levis', 2999.00, 25.00, 100, 4.2, 650),
(5, 10, 'Philips Air Fryer', 'Philips', 8999.00, 20.00, 40, 4.4, 320);
```

---

## 🔍 **Advanced SELECT Queries - Real Business Cases**

### **1. Product Search & Filtering (Core Flipkart Feature)**

#### **Basic Product Search:**
```sql
-- Search products by name (like Flipkart search bar)
SELECT 
    product_id,
    product_name,
    brand,
    final_price,
    average_rating,
    total_reviews,
    stock_quantity
FROM products 
WHERE product_name LIKE '%Samsung%' 
   AND is_active = TRUE
   AND stock_quantity > 0
ORDER BY average_rating DESC, total_reviews DESC;
```

#### **Advanced Price Filter:**
```sql
-- Products between price range with sorting options
SELECT 
    p.product_name,
    p.brand,
    p.price,
    p.discount_percentage,
    p.final_price,
    p.average_rating,
    c.category_name,
    s.seller_name
FROM products p
JOIN categories c ON p.category_id = c.category_id
JOIN sellers s ON p.seller_id = s.seller_id
WHERE p.final_price BETWEEN 5000 AND 50000
   AND p.average_rating >= 4.0
   AND p.stock_quantity > 0
   AND c.category_name = 'Electronics'
ORDER BY 
   CASE WHEN p.discount_percentage > 20 THEN 1 ELSE 2 END,  -- High discount first
   p.final_price ASC;
```

#### **Category-wise Product Listing:**
```sql
-- Category page products with filters
SELECT 
    p.product_id,
    p.product_name,
    p.brand,
    p.final_price,
    p.average_rating,
    p.total_reviews,
    p.stock_quantity,
    CASE 
        WHEN p.stock_quantity = 0 THEN 'Out of Stock'
        WHEN p.stock_quantity <= p.min_stock_level THEN 'Limited Stock'
        ELSE 'In Stock'
    END AS stock_status,
    CASE 
        WHEN p.discount_percentage > 30 THEN 'Hot Deal'
        WHEN p.discount_percentage > 15 THEN 'Good Deal'
        ELSE 'Regular Price'
    END AS deal_type
FROM products p
WHERE p.category_id = 6  -- Smartphones category
   AND p.is_active = TRUE
ORDER BY 
   CASE WHEN p.discount_percentage > 25 THEN 1 ELSE 2 END,
   p.average_rating DESC,
   p.final_price ASC
LIMIT 20;
```

### **2. Sales Analytics (Business Intelligence)**

#### **Monthly Revenue Analysis:**
```sql
-- Monthly sales revenue calculation
SELECT 
    YEAR(order_date) AS year,
    MONTH(order_date) AS month,
    MONTHNAME(order_date) AS month_name,
    COUNT(*) AS total_orders,
    SUM(final_amount) AS total_revenue,
    AVG(final_amount) AS average_order_value,
    COUNT(DISTINCT customer_id) AS unique_customers
FROM orders 
WHERE payment_status = 'Completed'
   AND order_date >= DATE_SUB(CURRENT_DATE, INTERVAL 12 MONTH)
GROUP BY YEAR(order_date), MONTH(order_date)
ORDER BY year DESC, month DESC;
```

#### **Top Selling Products:**
```sql
-- Best selling products analysis
SELECT 
    p.product_name,
    p.brand,
    c.category_name,
    s.seller_name,
    SUM(oi.quantity) AS total_units_sold,
    SUM(oi.total_price) AS total_revenue,
    AVG(oi.unit_price) AS average_selling_price,
    COUNT(DISTINCT oi.order_id) AS number_of_orders,
    p.average_rating,
    p.stock_quantity
FROM order_items oi
JOIN products p ON oi.product_id = p.product_id
JOIN categories c ON p.category_id = c.category_id
JOIN sellers s ON p.seller_id = s.seller_id
JOIN orders o ON oi.order_id = o.order_id
WHERE o.payment_status = 'Completed'
   AND o.order_date >= DATE_SUB(CURRENT_DATE, INTERVAL 3 MONTH)
GROUP BY p.product_id
ORDER BY total_units_sold DESC, total_revenue DESC
LIMIT 20;
```

#### **Seller Performance Dashboard:**
```sql
-- Seller performance metrics
SELECT 
    s.seller_name,
    s.rating AS seller_rating,
    COUNT(DISTINCT p.product_id) AS total_products,
    COUNT(DISTINCT oi.order_id) AS total_orders,
    SUM(oi.total_price) AS total_revenue,
    AVG(oi.unit_price) AS average_product_price,
    SUM(oi.quantity) AS total_units_sold,
    AVG(p.average_rating) AS average_product_rating,
    s.commission_rate,
    SUM(oi.total_price) * s.commission_rate / 100 AS total_commission
FROM sellers s
LEFT JOIN products p ON s.seller_id = p.seller_id
LEFT JOIN order_items oi ON p.product_id = oi.product_id
LEFT JOIN orders o ON oi.order_id = o.order_id
WHERE o.payment_status = 'Completed' OR o.payment_status IS NULL
   AND o.order_date >= DATE_SUB(CURRENT_DATE, INTERVAL 6 MONTH)
GROUP BY s.seller_id
ORDER BY total_revenue DESC;
```

### **3. Customer Analytics (CRM Insights)**

#### **Customer Segmentation:**
```sql
-- VIP customers identification
SELECT 
    c.customer_id,
    CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
    c.email,
    c.total_orders,
    c.total_spent,
    c.loyalty_points,
    c.is_prime_member,
    CASE 
        WHEN c.total_spent > 100000 THEN 'Diamond'
        WHEN c.total_spent > 50000 THEN 'Platinum'
        WHEN c.total_spent > 25000 THEN 'Gold'
        WHEN c.total_spent > 10000 THEN 'Silver'
        ELSE 'Bronze'
    END AS customer_tier,
    DATEDIFF(CURRENT_DATE, MAX(o.order_date)) AS days_since_last_order,
    AVG(o.final_amount) AS average_order_value
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
WHERE o.payment_status = 'Completed'
GROUP BY c.customer_id
HAVING c.total_orders > 0
ORDER BY c.total_spent DESC;
```

#### **Customer Purchase Behavior:**
```sql
-- Purchase pattern analysis
SELECT 
    c.customer_id,
    CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
    COUNT(DISTINCT o.order_id) AS total_orders,
    COUNT(DISTINCT oi.product_id) AS unique_products_bought,
    COUNT(DISTINCT p.category_id) AS categories_explored,
    AVG(o.final_amount) AS avg_order_value,
    SUM(o.final_amount) AS total_spent,
    MIN(o.order_date) AS first_purchase,
    MAX(o.order_date) AS last_purchase,
    DATEDIFF(MAX(o.order_date), MIN(o.order_date)) AS customer_lifetime_days,
    GROUP_CONCAT(DISTINCT cat.category_name ORDER BY cat.category_name) AS preferred_categories
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
JOIN order_items oi ON o.order_id = oi.order_id
JOIN products p ON oi.product_id = p.product_id
JOIN categories cat ON p.category_id = cat.category_id
WHERE o.payment_status = 'Completed'
GROUP BY c.customer_id
HAVING total_orders >= 3
ORDER BY total_spent DESC
LIMIT 50;
```

### **4. Inventory Management Queries**

#### **Low Stock Alert:**
```sql
-- Products running out of stock
SELECT 
    p.product_id,
    p.product_name,
    p.brand,
    c.category_name,
    s.seller_name,
    p.stock_quantity,
    p.min_stock_level,
    p.stock_quantity - p.min_stock_level AS stock_difference,
    AVG(oi.quantity) AS avg_daily_sales,
    p.stock_quantity / NULLIF(AVG(oi.quantity), 0) AS days_of_stock_left,
    p.final_price,
    p.average_rating
FROM products p
JOIN categories c ON p.category_id = c.category_id
JOIN sellers s ON p.seller_id = s.seller_id
LEFT JOIN order_items oi ON p.product_id = oi.product_id
LEFT JOIN orders o ON oi.order_id = o.order_id 
WHERE p.stock_quantity <= p.min_stock_level
   AND p.is_active = TRUE
   AND (o.order_date >= DATE_SUB(CURRENT_DATE, INTERVAL 30 DAY) OR o.order_date IS NULL)
GROUP BY p.product_id
ORDER BY stock_difference ASC, avg_daily_sales DESC;
```

#### **Dead Stock Analysis:**
```sql
-- Products with no sales in last 90 days
SELECT 
    p.product_id,
    p.product_name,
    p.brand,
    p.final_price,
    p.stock_quantity,
    p.created_at,
    DATEDIFF(CURRENT_DATE, p.created_at) AS days_since_added,
    COALESCE(last_sale.last_sale_date, 'Never') AS last_sale_date,
    COALESCE(total_sales.total_sold, 0) AS total_units_sold,
    p.stock_quantity * p.final_price AS inventory_value
FROM products p
LEFT JOIN (
    SELECT 
        oi.product_id, 
        MAX(o.order_date) AS last_sale_date
    FROM order_items oi
    JOIN orders o ON oi.order_id = o.order_id
    WHERE o.payment_status = 'Completed'
    GROUP BY oi.product_id
) last_sale ON p.product_id = last_sale.product_id
LEFT JOIN (
    SELECT 
        oi.product_id,
        SUM(oi.quantity) AS total_sold
    FROM order_items oi
    JOIN orders o ON oi.order_id = o.order_id
    WHERE o.payment_status = 'Completed'
    GROUP BY oi.product_id
) total_sales ON p.product_id = total_sales.product_id
WHERE (last_sale.last_sale_date IS NULL 
       OR last_sale.last_sale_date < DATE_SUB(CURRENT_DATE, INTERVAL 90 DAY))
   AND p.is_active = TRUE
   AND p.stock_quantity > 0
ORDER BY inventory_value DESC, days_since_added DESC;
```

### **5. Review & Rating Analysis**

#### **Product Review Summary:**
```sql
-- Detailed review analysis for products
SELECT 
    p.product_name,
    p.brand,
    p.average_rating,
    p.total_reviews,
    COUNT(r.review_id) AS detailed_reviews,
    AVG(r.rating) AS calculated_avg_rating,
    SUM(CASE WHEN r.rating = 5 THEN 1 ELSE 0 END) AS five_star_reviews,
    SUM(CASE WHEN r.rating = 4 THEN 1 ELSE 0 END) AS four_star_reviews,
    SUM(CASE WHEN r.rating = 3 THEN 1 ELSE 0 END) AS three_star_reviews,
    SUM(CASE WHEN r.rating = 2 THEN 1 ELSE 0 END) AS two_star_reviews,
    SUM(CASE WHEN r.rating = 1 THEN 1 ELSE 0 END) AS one_star_reviews,
    ROUND((SUM(CASE WHEN r.rating >= 4 THEN 1 ELSE 0 END) * 100.0 / COUNT(r.review_id)), 2) AS positive_review_percentage
FROM products p
LEFT JOIN reviews r ON p.product_id = r.product_id
WHERE p.total_reviews > 10
GROUP BY p.product_id
ORDER BY calculated_avg_rating DESC, p.total_reviews DESC;
```

#### **Recent Reviews with Customer Info:**
```sql
-- Latest product reviews for moderation
SELECT 
    r.review_id,
    p.product_name,
    CONCAT(c.first_name, ' ', SUBSTR(c.last_name, 1, 1), '.') AS customer_name,
    r.rating,
    r.review_text,
    r.helpful_count,
    r.is_verified_purchase,
    r.created_at,
    DATEDIFF(CURRENT_DATE, r.created_at) AS days_ago
FROM reviews r
JOIN products p ON r.product_id = p.product_id
JOIN customers c ON r.customer_id = c.customer_id
WHERE r.created_at >= DATE_SUB(CURRENT_DATE, INTERVAL 7 DAY)
ORDER BY r.created_at DESC, r.rating ASC
LIMIT 100;
```

---

## 🎯 **Advanced WHERE Clause Techniques**

### **Complex Filtering Combinations:**
```sql
-- Multi-condition product filtering (Real Flipkart filters)
SELECT 
    p.product_name,
    p.brand,
    p.final_price,
    p.average_rating,
    p.discount_percentage,
    c.category_name
FROM products p
JOIN categories c ON p.category_id = c.category_id
WHERE p.final_price BETWEEN 10000 AND 50000
   AND p.average_rating >= 4.0
   AND p.discount_percentage > 15
   AND p.stock_quantity > 0
   AND p.brand IN ('Samsung', 'Apple', 'OnePlus', 'Xiaomi')
   AND c.category_name LIKE '%Electronics%'
   AND p.total_reviews >= 50
   AND NOT (p.product_name LIKE '%Refurbished%' OR p.product_name LIKE '%Used%')
ORDER BY 
   (p.average_rating * 0.4 + (p.discount_percentage/100) * 0.3 + (p.total_reviews/1000) * 0.3) DESC;
```

### **Date Range Filtering:**
```sql
-- Order analysis for specific periods
SELECT 
    DATE(order_date) AS order_day,
    COUNT(*) AS total_orders,
    SUM(final_amount) AS daily_revenue,
    AVG(final_amount) AS avg_order_value,
    COUNT(DISTINCT customer_id) AS unique_customers
FROM orders 
WHERE order_date >= '2024-01-01'
   AND order_date < '2024-02-01'
   AND payment_status IN ('Completed', 'Pending')
   AND order_status NOT IN ('Cancelled', 'Returned')
GROUP BY DATE(order_date)
ORDER BY order_day DESC;
```

### **Pattern Matching & Text Search:**
```sql
-- Advanced product search with multiple patterns
SELECT 
    product_id,
    product_name,
    brand,
    final_price,
    average_rating
FROM products 
WHERE (
    product_name LIKE '%iPhone%' 
    OR product_name LIKE '%Samsung Galaxy%'
    OR product_name LIKE '%OnePlus%'
    OR (brand = 'Xiaomi' AND product_name LIKE '%Pro%')
)
AND final_price BETWEEN 15000 AND 80000
AND average_rating >= 4.0
ORDER BY 
   CASE 
       WHEN product_name LIKE '%iPhone%' THEN 1
       WHEN product_name LIKE '%Samsung Galaxy%' THEN 2
       WHEN product_name LIKE '%OnePlus%' THEN 3
       ELSE 4
   END,
   average_rating DESC;
```

---

## 📈 **ORDER BY Advanced Techniques**

### **Multi-level Sorting:**
```sql
-- Product listing with priority sorting
SELECT 
    p.product_name,
    p.brand,
    p.final_price,
    p.average_rating,
    p.discount_percentage,
    p.stock_quantity,
    CASE 
        WHEN p.stock_quantity = 0 THEN 'Out of Stock'
        WHEN p.stock_quantity <= 5 THEN 'Limited Stock'
        ELSE 'Available'
    END AS availability_status
FROM products p
WHERE p.category_id = 6  -- Electronics
   AND p.is_active = TRUE
ORDER BY 
   CASE WHEN p.stock_quantity = 0 THEN 2 ELSE 1 END,  -- Available products first
   CASE WHEN p.discount_percentage > 25 THEN 1 ELSE 2 END,  -- High discount products
   p.average_rating DESC,  -- Best rated products
   p.total_reviews DESC,   -- Most reviewed products
   p.final_price ASC;      -- Lower price first
```

### **Dynamic Sorting Based on Conditions:**
```sql
-- Sorting based on user preferences
SELECT 
    product_name,
    brand,
    final_price,
    average_rating,
    discount_percentage,
    (final_price * (discount_percentage/100)) AS discount_amount
FROM products 
WHERE category_id = 8  -- Fashion category
   AND stock_quantity > 0
ORDER BY 
   CASE 
       WHEN @sort_by = 'price_low_high' THEN final_price
       WHEN @sort_by = 'price_high_low' THEN -final_price
       WHEN @sort_by = 'rating' THEN -average_rating
       WHEN @sort_by = 'discount' THEN -discount_percentage
       ELSE -average_rating  -- Default: best rated first
   END;
```

---

## 🔢 **LIMIT & OFFSET for Pagination**

### **Product Pagination (Real Flipkart Style):**
```sql
-- Page 1: First 20 products
SELECT 
    product_id,
    product_name,
    brand,
    final_price,
    average_rating,
    stock_quantity
FROM products 
WHERE category_id = 6 
   AND is_active = TRUE
   AND stock_quantity > 0
ORDER BY average_rating DESC, total_reviews DESC
LIMIT 20 OFFSET 0;

-- Page 2: Next 20 products  
SELECT 
    product_id,
    product_name,
    brand,
    final_price,
    average_rating,
    stock_quantity
FROM products 
WHERE category_id = 6 
   AND is_active = TRUE
   AND stock_quantity > 0
ORDER BY average_rating DESC, total_reviews DESC
LIMIT 20 OFFSET 20;

-- Page 3: Next 20 products
SELECT 
    product_id,
    product_name,
    brand,
    final_price,
    average_rating,
    stock_quantity
FROM products 
WHERE category_id = 6 
   AND is_active = TRUE
   AND stock_quantity > 0
ORDER BY average_rating DESC, total_reviews DESC
LIMIT 20 OFFSET 40;
```

### **Dynamic Pagination Function:**
```sql
-- Generic pagination query
SET @page_number = 3;
SET @page_size = 20;
SET @offset_value = (@page_number - 1) * @page_size;

SELECT 
    product_id,
    product_name,
    final_price,
    average_rating
FROM products 
WHERE is_active = TRUE
ORDER BY average_rating DESC
LIMIT @page_size OFFSET @offset_value;
```

---

## 🎯 **Real-World Business Scenarios**

### **Scenario 1: Flash Sale Analytics**
```sql
-- Flash sale performance analysis
SELECT 
    p.product_name,
    p.brand,
    p.price AS original_price,
    p.final_price AS sale_price,
    p.discount_percentage,
    COUNT(oi.order_item_id) AS units_sold_during_sale,
    SUM(oi.total_price) AS sale_revenue,
    AVG(o.order_date) AS avg_sale_time
FROM products p
JOIN order_items oi ON p.product_id = oi.product_id
JOIN orders o ON oi.order_id = o.order_id
WHERE p.discount_percentage > 40  -- Flash sale discounts
   AND o.order_date BETWEEN '2024-01-15 00:00:00' AND '2024-01-15 23:59:59'  -- Flash sale day
   AND o.payment_status = 'Completed'
GROUP BY p.product_id
ORDER BY units_sold_during_sale DESC;
```

### **Scenario 2: Recommendation Engine Data**
```sql
-- Customers who bought this also bought
SELECT 
    p2.product_name AS recommended_product,
    p2.brand,
    p2.final_price,
    p2.average_rating,
    COUNT(*) AS co_purchase_frequency,
    AVG(p2.final_price) AS avg_price_of_recommendations
FROM order_items oi1
JOIN order_items oi2 ON oi1.order_id = oi2.order_id AND oi1.product_id != oi2.product_id
JOIN products p1 ON oi1.product_id = p1.product_id
JOIN products p2 ON oi2.product_id = p2.product_id
WHERE p1.product_id = 1  -- Target product: Samsung Galaxy S24
   AND p2.stock_quantity > 0
GROUP BY p2.product_id
HAVING co_purchase_frequency >= 3
ORDER BY co_purchase_frequency DESC, p2.average_rating DESC
LIMIT 10;
```

### **Scenario 3: Customer Retention Analysis**
```sql
-- Customers at risk of churning
SELECT 
    c.customer_id,
    CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
    c.total_orders,
    c.total_spent,
    MAX(o.order_date) AS last_order_date,
    DATEDIFF(CURRENT_DATE, MAX(o.order_date)) AS days_since_last_order,
    AVG(o.final_amount) AS avg_order_value,
    CASE 
        WHEN DATEDIFF(CURRENT_DATE, MAX(o.order_date)) > 180 THEN 'High Risk'
        WHEN DATEDIFF(CURRENT_DATE, MAX(o.order_date)) > 90 THEN 'Medium Risk'
        WHEN DATEDIFF(CURRENT_DATE, MAX(o.order_date)) > 30 THEN 'Low Risk'
        ELSE 'Active'
    END AS churn_risk_level
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
WHERE o.payment_status = 'Completed'
   AND c.total_orders >= 3  -- Only existing customers
GROUP BY c.customer_id
HAVING days_since_last_order > 30
ORDER BY c.total_spent DESC, days_since_last_order DESC;
```

---

## 📋 **Practice Exercises - E-commerce Scenarios**

### **Easy Level:**
1. **Product Search:** Find all smartphones under ₹20,000 with rating > 4.0
2. **Category Analysis:** Count products in each category
3. **Customer List:** Show all Prime members with their total spent

### **Medium Level:**
1. **Sales Report:** Monthly revenue for last 6 months
2. **Inventory Alert:** Products with stock < 10 units
3. **Review Analysis:** Products with most 5-star reviews

### **Hard Level:**
1. **Customer Segmentation:** Classify customers by purchase behavior
2. **Product Performance:** Calculate conversion rate by category
3. **Seasonal Trends:** Identify best-selling products by month

---

## 🚀 **Performance Tips for E-commerce Queries**

### **Indexing Strategy:**
```sql
-- Essential indexes for e-commerce
CREATE INDEX idx_products_category_active ON products(category_id, is_active);
CREATE INDEX idx_products_price_rating ON products(final_price, average_rating);
CREATE INDEX idx_orders_customer_date ON orders(customer_id, order_date);
CREATE INDEX idx_order_items_product ON order_items(product_id);
CREATE INDEX idx_reviews_product_rating ON reviews(product_id, rating);
```

### **Query Optimization:**
```sql
-- Optimized product search
SELECT 
    p.product_id,
    p.product_name,
    p.final_price,
    p.average_rating
FROM products p
USE INDEX (idx_products_category_active)
WHERE p.category_id = 6 
   AND p.is_active = TRUE
   AND p.final_price BETWEEN 10000 AND 50000
ORDER BY p.average_rating DESC
LIMIT 20;
```

---

## 🎯 **Next Steps**

**Completed:** ✅ Advanced SELECT queries with Flipkart e-commerce database  
**Next Module:** **03-Joins-Champion** - Master all JOINs with WhatsApp messaging system

**Real Learning:** Ab tumhe pata hai ki **Flipkart jaise platforms** kaise complex business queries handle karte hain! E-commerce analytics, customer insights, inventory management - sab kuch! 💪

**Ready for JOINs?** WhatsApp ke messaging database ke saath **relational data mastery** seekhenge! 💬⚡
