# blog_project1111
project_blog/
│
├── config/
│   ├── database.php
│
├── public/
│   ├── index.php               # Halaman utama (list artikel)
│   ├── article.php             # Halaman semua artikel
│   ├── post.php                # Baca artikel
│   ├── search.php               
│   ├── about.php             
│   ├── contact.php                
│   ├── blog.php             
│   ├── comment_add.php             
│   ├── share.php             
│
│   ├── assets/
│   │   ├── css/
│   │   ├── js/
│   │   └── img/
│
├── uploads/
│
├── admin/
│   ├── login.php
│   ├── register.php
│   ├── dashboard.php
│   ├── logout.php
│   ├── profile.php
│   │
│   ├── article/
│   │   ├── index.php           # List artikel
│   │   ├── create.php          # Tambah artikel
│   │   ├── edit.php            # Edit artikel
│   │   ├── delete.php          # Hapus artikel
│   │
││   ├── categories/
│   │   ├── create.php           
│   │   ├── delete.php          
│   │   ├── edit.php            
│   │   ├── list.php          
│   │
││   ├── media/
│   │   ├── delete.php           
│   │   ├── list.php          
│   │   ├── test.php            
│   │   ├── upload.php          
│   │
││   ├── comments/
│   │   ├── delete.php           
│   │   ├── list.php          
│   │
││   ├── posts/
│   │   ├── create.php           
│   │   ├── delete.php          
│   │   ├── edit.php            
│   │   ├── list.php          
│   │
││   ├── media/
│   │   ├── create.php           
│   │   ├── delete.php          
│   │   ├── edit.php            
│   │   ├── list.php          
│   │
││   ├── tags/
│   │   ├── create.php           
│   │   ├── delete.php          
│   │   ├── edit.php            
│   │   ├── list.php          
│   │
││   ├── user/
│   │   ├── detail_user.php           
│   │   ├── delete_user.php          
│   │   ├── edit_user.php            
│   │   ├── list_user.php          
│
├── user/
│   ├── login.php
│   ├── register.php
│   ├── dashboard.php
│   ├── logout.php
│   ├── change_password.php
│   ├── delete_comment.php
│   ├── liked_post.php
│   ├── my_comment.php
│   ├── profile.php
│   ├── saved_post.php
│   ├── unsave_comment.php
│   ├── update_comment.php

-- Database: project_blog
CREATE DATABASE IF NOT EXISTS project_blog;
USE project_blog;

-- Tabel untuk admin (terpisah dari user)
CREATE TABLE admins (
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(100) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL,
    full_name VARCHAR(255),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

-- Tabel untuk user biasa
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(100) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL,
    full_name VARCHAR(255),
    profile_image VARCHAR(255) DEFAULT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

-- Tabel untuk kategori artikel
CREATE TABLE categories (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Tabel artikel/blog posts
CREATE TABLE articles (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    slug VARCHAR(255) UNIQUE NOT NULL,
    content LONGTEXT NOT NULL,
    excerpt TEXT,
    featured_image VARCHAR(255) DEFAULT NULL,
    category_id INT,
    author_id INT NOT NULL,
    is_published BOOLEAN DEFAULT FALSE,
    published_at TIMESTAMP NULL DEFAULT NULL,
    view_count INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (category_id) REFERENCES categories(id) ON DELETE SET NULL,
    FOREIGN KEY (author_id) REFERENCES admins(id) ON DELETE CASCADE
);

-- Tabel komentar dari user
CREATE TABLE comments (
    id INT PRIMARY KEY AUTO_INCREMENT,
    article_id INT NOT NULL,
    user_id INT NOT NULL,
    parent_id INT DEFAULT NULL,
    content TEXT NOT NULL,
    is_approved BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (article_id) REFERENCES articles(id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (parent_id) REFERENCES comments(id) ON DELETE CASCADE
);

-- Tabel untuk likes/favorit artikel
CREATE TABLE article_likes (
    id INT PRIMARY KEY AUTO_INCREMENT,
    article_id INT NOT NULL,
    user_id INT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE KEY unique_like (article_id, user_id),
    FOREIGN KEY (article_id) REFERENCES articles(id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);

-- Insert admin default (password: admin123)
INSERT INTO admins (username, email, password, full_name) 
VALUES ('admin', 'admin@blog.com', '$2y$10$YourHashedPasswordHere', 'Administrator');

-- Insert kategori default
INSERT INTO categories (name, slug, description) VALUES
('Teknologi', 'teknologi', 'Artikel tentang teknologi terbaru'),
('Pemrograman', 'pemrograman', 'Tutorial dan tips pemrograman'),
('Desain', 'desain', 'Artikel tentang desain dan kreativitas'),
('Bisnis', 'bisnis', 'Tips dan strategi bisnis');

-- Insert user contoh
INSERT INTO users (username, email, password, full_name) 
VALUES ('johndoe', 'john@example.com', '$2y$10$YourHashedPasswordHere', 'John Doe');

-- Tabel untuk tags
CREATE TABLE tags (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    color VARCHAR(20) DEFAULT '#6c757d',
    post_count INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

-- Tabel relasi artikel dengan tags (many-to-many)
CREATE TABLE article_tags (
    id INT PRIMARY KEY AUTO_INCREMENT,
    article_id INT NOT NULL,
    tag_id INT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE KEY unique_article_tag (article_id, tag_id),
    FOREIGN KEY (article_id) REFERENCES articles(id) ON DELETE CASCADE,
    FOREIGN KEY (tag_id) REFERENCES tags(id) ON DELETE CASCADE
);

-- Insert contoh tags
INSERT INTO tags (name, slug, description, color) VALUES
('PHP', 'php', 'Tag untuk artikel tentang PHP', '#4f5b93'),
('JavaScript', 'javascript', 'Tag untuk artikel tentang JavaScript', '#f0db4f'),
('CSS', 'css', 'Tag untuk artikel tentang CSS', '#264de4'),
('HTML', 'html', 'Tag untuk artikel tentang HTML', '#e34c26'),
('MySQL', 'mysql', 'Tag untuk artikel tentang MySQL', '#00758f'),
('Tutorial', 'tutorial', 'Tag untuk artikel tutorial', '#28a745'),
('Tips', 'tips', 'Tag untuk artikel tips dan trik', '#17a2b8');

-- Tambahkan kolom profile_image ke tabel admins
ALTER TABLE admins ADD COLUMN profile_image VARCHAR(255) DEFAULT NULL AFTER full_name;

-- Tabel untuk saved posts (bookmarks)
CREATE TABLE saved_posts (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    article_id INT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE KEY unique_save (user_id, article_id),
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (article_id) REFERENCES articles(id) ON DELETE CASCADE
);

Kode config/database.php
<?php 
// KONEKSI DATABASE - SUPER SIMPLE 
$host = "localhost"; 
$user = "root"; 
$pass = ""; 
$db = "project_blog"; 
// Konek ke database 
$konek = mysqli_connect($host, $user, $pass, $db); 
// Kalo gagal, tampilkan error sederhana 
if(!$konek) { 
die("Gagal konek ke database. Cek XAMPP nyala atau belum!"); 
} 
echo "<!-- Koneksi database berhasil -->"; 
?>

