requests 1

    SELECT u.username, COUNT(o.id) AS order_count 
    FROM users u 
    LEFT JOIN customer_orders o ON u.id = o.user_id 
    GROUP BY u.username 
    ORDER BY order_count DESC;


requests 2 

    SELECT username FROM users WHERE status = 'active'
    UNION
    SELECT username FROM users WHERE created_at < '2022-01-01';


requests 3

    DELIMITER $$
    CREATE TRIGGER update_timestamp 
    BEFORE UPDATE ON users 
    FOR EACH ROW 
    SET NEW.updated_at = NOW();
    $$
    DELIMITER ;


requests 4

    DELIMITER $$
    CREATE PROCEDURE add_user(
        IN p_username VARCHAR(50), 
        IN p_email VARCHAR(100), 
        IN p_password VARCHAR(255)
    )
    BEGIN
        INSERT INTO users (username, email, password) 
        VALUES (p_username, p_email, p_password);
    END $$
    DELIMITER ;


requests  5

    START TRANSACTION;

    INSERT INTO customer_orders (user_id, product_id, quantity, total) 
    VALUES (1, 1, 2, 1999.98);

    UPDATE products 
    SET stock = stock - 2 
    WHERE id = 1;

    COMMIT;


requests 6

    SELECT p.name, o.quantity 
    FROM products p 
    LEFT JOIN customer_orders o ON p.id = o.product_id;


requests 7

    SELECT user_id, COUNT(*) AS order_count 
    FROM customer_orders 
    GROUP BY user_id;


requests 8

    INSERT INTO products (name, price, description) 
    VALUES ('Laptop', 999.99, 'High-performance laptop');


requests 9

SELECT c.name, COUNT(p.id) AS product_count 
FROM categories c
LEFT JOIN products p ON c.id = p.category_id
GROUP BY c.name;

requests 10

CREATE VIEW user_order_summary AS 
SELECT u.username, SUM(o.total) AS total_spent 
FROM users u 
JOIN orders o ON u.id = o.user_id 
GROUP BY u.username;

requests 11

CREATE TABLE user_collections (
    id SERIAL PRIMARY KEY,
    user_id INT REFERENCES users(id),
    collection_name VARCHAR(100) NOT NULL
);

INSERT INTO user_collections (user_id, collection_name) 
VALUES (1, 'My Favorite Products');

SELECT * FROM user_collections 
WHERE user_id = 1;

CREATE TABLE collection_products (
    id SERIAL PRIMARY KEY,
    collection_id INT REFERENCES user_collections(id),
    product_id INT REFERENCES products(id)
);

INSERT INTO collection_products (collection_id, product_id) 
VALUES (1, 1);

SELECT p.name 
FROM products p
JOIN collection_products cp ON p.id = cp.product_id
WHERE cp.collection_id = 1;

SELECT COUNT(*) 
FROM collection_products 
WHERE collection_id = 1;


requests  12

SELECT product_id, AVG(rating) AS average_rating 
FROM reviews 
GROUP BY product_id;

requests 13

CREATE TABLE product_category (
    product_id INT REFERENCES products(id),
    category_id INT REFERENCES categories(id),
    PRIMARY KEY (product_id, category_id)
);

requests 14

INSERT INTO archived_orders (user_id, product_id, quantity, total, created_at) 
SELECT user_id, product_id, quantity, total, created_at 
FROM orders 
WHERE created_at < '2022-01-01';

requests 15

CREATE OR REPLACE FUNCTION update_modified_at() 
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = CURRENT_TIMESTAMP;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER product_update_trigger 
BEFORE UPDATE ON products 
FOR EACH ROW EXECUTE PROCEDURE update_modified_at();

requests 16

CREATE TABLE reviews (
    id SERIAL PRIMARY KEY,
    user_id INT REFERENCES users(id),
    product_id INT REFERENCES products(id),
    rating INT CHECK (rating >= 1 AND rating <= 5),
    review TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

requests  17

CREATE TABLE user_roles (
    id SERIAL PRIMARY KEY,
    user_id INT REFERENCES users(id),
    role VARCHAR(50)
);

requests 18

   CREATE TABLE users (
       id INT AUTO_INCREMENT PRIMARY KEY,
       username VARCHAR(50) NOT NULL UNIQUE,
       email VARCHAR(100) NOT NULL,
       password VARCHAR(255) NOT NULL,
       created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
   );


requests 19

   CREATE TABLE products (
       id INT AUTO_INCREMENT PRIMARY KEY,
       name VARCHAR(100) NOT NULL,
       price DECIMAL(10, 2) NOT NULL,
       description TEXT,
       created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
   );

requests 20

    CREATE TABLE files (
        id INT AUTO_INCREMENT PRIMARY KEY,
        user_id INT,
        file_name VARCHAR(255),
        file_data LONGBLOB,
        uploaded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
        FOREIGN KEY (user_id) REFERENCES users(id)
    );






















































































































































