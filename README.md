# SQL_feature_flag

# Feature Flag System (SQL-Only Demo)

This repository demonstrates a **SQL-only feature flag system** built entirely with MySQL. It supports:

- ✅ Flag creation, deletion, toggling
- ✅ User and group targeting
- ✅ Percentage rollout (in full version)
- ✅ Audit and soft-delete capability

---

## 📁 Files

- `feature_flag_demo.sql`: Contains schema + seed data + sample queries

---

## 🚀 How to Use

1. Open MySQL Workbench or phpMyAdmin
2. Create a new schema/database (e.g. `feature_flags`)
3. Run the contents of `feature_flag_demo.sql`
4. Explore the feature flag system with these examples:

---

## 🔍 Example Queries

```sql
-- View all flags
SELECT * FROM feature_flags;

-- Toggle a flag
UPDATE feature_flags SET enabled = NOT enabled WHERE name = 'dark_mode_ui';

-- Assign to user
INSERT INTO feature_flag_users (feature_flag_id, user_id, enabled, created_at)
VALUES (1, 1, TRUE, NOW());

-- Flags for user Alice
SELECT DISTINCT ff.name
FROM feature_flags ff
JOIN feature_flag_users ffu ON ff.id = ffu.feature_flag_id
WHERE ffu.user_id = (SELECT id FROM users WHERE username = 'alice') AND ffu.enabled = TRUE;








-- =========================================================
-- Feature Flag System - SQL-Only Demo Script
-- Generated: 2025-06-22
-- =========================================================

-- Drop tables if they exist (for demo reset)
DROP TABLE IF EXISTS feature_flag_dependencies, feature_flag_expiry, feature_flag_audit,
feature_flag_groups, feature_flag_users, feature_flag_history, history,
soft_deleted_flags, feature_flag_tags, tags, percentage_rollout,
flag_dependencies, audit_logs, feature_flag_environment, environments,
feature_flag_targets, user_groups, users, feature_flags;

-- Create tables (simplified for demo)
CREATE TABLE feature_flags (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    enabled BOOLEAN NOT NULL,
    expiry_date DATETIME
);

CREATE TABLE users (
    created_at DATETIME NOT NULL,
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL,
    updated_at DATETIME
);

CREATE TABLE user_groups (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    created_at DATETIME NOT NULL,
    updated_at DATETIME
);

CREATE TABLE feature_flag_users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    feature_flag_id INT,
    user_id INT,
    enabled BOOLEAN,
    created_at DATETIME
);

CREATE TABLE feature_flag_groups (
    id INT PRIMARY KEY AUTO_INCREMENT,
    feature_flag_id INT,
    user_group_id INT,
    enabled BOOLEAN,
    created_at DATETIME
);

-- Insert sample flags
INSERT INTO feature_flags (name, description, enabled, expiry_date) VALUES
('dark_mode_ui', 'Enable dark mode for selected users', TRUE, '2025-12-31 23:59:59'),
('beta_checkout', 'Enable new checkout flow', FALSE, NULL),
('ai_recommendations', 'AI-powered product recommendations', TRUE, '2025-09-01 00:00:00');

-- Insert sample users
INSERT INTO users (created_at, username, email) VALUES
(NOW(), 'alice', 'alice@example.com'),
(NOW(), 'bob', 'bob@example.com');

-- Insert user groups
INSERT INTO user_groups (name, description, created_at) VALUES
('Admins', 'Admin-level access group', NOW()),
('Beta Testers', 'Early access users', NOW());

-- Assign flags to users
INSERT INTO feature_flag_users (feature_flag_id, user_id, enabled, created_at)
VALUES (1, 1, TRUE, NOW()); -- dark_mode_ui to Alice

-- Assign flags to groups
INSERT INTO feature_flag_groups (feature_flag_id, user_group_id, enabled, created_at)
VALUES (2, 2, TRUE, NOW()); -- beta_checkout to Beta Testers

-- Demo Queries
-- View all flags:
-- SELECT * FROM feature_flags;

-- Toggle a flag:
-- UPDATE feature_flags SET enabled = NOT enabled WHERE name = 'dark_mode_ui';

-- Flags for Alice:
-- SELECT DISTINCT ff.name FROM feature_flags ff
-- JOIN feature_flag_users ffu ON ff.id = ffu.feature_flag_id
-- WHERE ffu.user_id = (SELECT id FROM users WHERE username = 'alice') AND ffu.enabled = TRUE;

