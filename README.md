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


CREATE TABLE feature_flags (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    enabled BOOLEAN NOT NULL,
    expiry_date DATETIME
) ENGINE=InnoDB;

CREATE TABLE users (
    created_at DATETIME NOT NULL,
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL,
    updated_at DATETIME
) ENGINE=InnoDB;

CREATE TABLE user_groups (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    created_at DATETIME NOT NULL,
    updated_at DATETIME
) ENGINE=InnoDB;

CREATE TABLE feature_flag_targets (
    id INT PRIMARY KEY AUTO_INCREMENT,
    feature_flag_id INT NOT NULL,
    user_id INT,
    user_group_id INT,
    created_at DATETIME NOT NULL,
    updated_at DATETIME,
    FOREIGN KEY (feature_flag_id) REFERENCES feature_flags(id),
    FOREIGN KEY (user_id) REFERENCES users(id),
    FOREIGN KEY (user_group_id) REFERENCES user_groups(id)
) ENGINE=InnoDB;

CREATE TABLE environments (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    created_at DATETIME NOT NULL,
    updated_at DATETIME
) ENGINE=InnoDB;

CREATE TABLE feature_flag_environment (
    id INT PRIMARY KEY AUTO_INCREMENT,
    environment_id INT NOT NULL,
    created_at DATETIME NOT NULL,
    feature_flag_id INT NOT NULL,
    updated_at DATETIME,
    FOREIGN KEY (environment_id) REFERENCES environments(id),
    FOREIGN KEY (feature_flag_id) REFERENCES feature_flags(id)
) ENGINE=InnoDB;

CREATE TABLE audit_logs (
    id INT PRIMARY KEY AUTO_INCREMENT,
    feature_flag_id INT NOT NULL,
    action VARCHAR(50) NOT NULL,
    actor VARCHAR(255) NOT NULL,
    timestamp DATETIME NOT NULL,
    FOREIGN KEY (feature_flag_id) REFERENCES feature_flags(id)
) ENGINE=InnoDB;

CREATE TABLE flag_dependencies (
    id INT PRIMARY KEY AUTO_INCREMENT,
    feature_flag_id INT NOT NULL,
    depends_on_flag_id INT NOT NULL,
    created_at DATETIME NOT NULL,
    updated_at DATETIME,
    FOREIGN KEY (feature_flag_id) REFERENCES feature_flags(id),
    FOREIGN KEY (depends_on_flag_id) REFERENCES feature_flags(id)
) ENGINE=InnoDB;

CREATE TABLE percentage_rollout (
    id INT PRIMARY KEY AUTO_INCREMENT,
    feature_flag_id INT NOT NULL,
    percentage INT NOT NULL,
    created_at DATETIME NOT NULL,
    updated_at DATETIME,
    FOREIGN KEY (feature_flag_id) REFERENCES feature_flags(id)
) ENGINE=InnoDB;

CREATE TABLE tags (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    created_at DATETIME NOT NULL,
    updated_at DATETIME
) ENGINE=InnoDB;

CREATE TABLE feature_flag_tags (
    id INT PRIMARY KEY AUTO_INCREMENT,
    feature_flag_id INT NOT NULL,
    tag_id INT NOT NULL,
    created_at DATETIME NOT NULL,
    updated_at DATETIME,
    FOREIGN KEY (feature_flag_id) REFERENCES feature_flags(id),
    FOREIGN KEY (tag_id) REFERENCES tags(id)
) ENGINE=InnoDB;

CREATE TABLE soft_deleted_flags (
    id INT PRIMARY KEY AUTO_INCREMENT,
    feature_flag_id INT NOT NULL,
    deleted_at DATETIME NOT NULL,
    reason TEXT,
    created_at DATETIME NOT NULL,
    FOREIGN KEY (feature_flag_id) REFERENCES feature_flags(id)
) ENGINE=InnoDB;

CREATE TABLE history (
    id INT PRIMARY KEY AUTO_INCREMENT,
    feature_flag_id INT NOT NULL,
    action VARCHAR(50) NOT NULL,
    actor VARCHAR(255) NOT NULL,
    timestamp DATETIME NOT NULL,
    FOREIGN KEY (feature_flag_id) REFERENCES feature_flags(id)
) ENGINE=InnoDB;

CREATE TABLE feature_flag_history (
    id INT PRIMARY KEY AUTO_INCREMENT,
    feature_flag_id INT NOT NULL,
    previous_state BOOLEAN NOT NULL,
    new_state BOOLEAN NOT NULL,
    timestamp DATETIME NOT NULL,
    FOREIGN KEY (feature_flag_id) REFERENCES feature_flags(id)
) ENGINE=InnoDB;

CREATE TABLE feature_flag_users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    feature_flag_id INT NOT NULL,
    user_id INT NOT NULL,
    enabled BOOLEAN NOT NULL,
    created_at DATETIME NOT NULL,
    FOREIGN KEY (feature_flag_id) REFERENCES feature_flags(id),
    FOREIGN KEY (user_id) REFERENCES users(id)
) ENGINE=InnoDB;

CREATE TABLE feature_flag_groups (
    id INT PRIMARY KEY AUTO_INCREMENT,
    feature_flag_id INT NOT NULL,
    user_group_id INT NOT NULL,
    enabled BOOLEAN NOT NULL,
    created_at DATETIME NOT NULL,
    FOREIGN KEY (feature_flag_id) REFERENCES feature_flags(id),
    FOREIGN KEY (user_group_id) REFERENCES user_groups(id)
) ENGINE=InnoDB;

CREATE TABLE feature_flag_audit (
    id INT PRIMARY KEY AUTO_INCREMENT,
    feature_flag_id INT NOT NULL,
    action VARCHAR(50) NOT NULL,
    actor VARCHAR(255) NOT NULL,
    timestamp DATETIME NOT NULL,
    FOREIGN KEY (feature_flag_id) REFERENCES feature_flags(id)
) ENGINE=InnoDB;

CREATE TABLE feature_flag_expiry (
    id INT PRIMARY KEY AUTO_INCREMENT,
    feature_flag_id INT NOT NULL,
    expiry_date DATETIME NOT NULL,
    created_at DATETIME NOT NULL,
    updated_at DATETIME,
    FOREIGN KEY (feature_flag_id) REFERENCES feature_flags(id)
) ENGINE=InnoDB;

CREATE TABLE feature_flag_dependencies (
    id INT PRIMARY KEY AUTO_INCREMENT,
    feature_flag_id INT NOT NULL,
    dependent_flag_id INT NOT NULL,
    created_at DATETIME NOT NULL,
    updated_at DATETIME,
    FOREIGN KEY (feature_flag_id) REFERENCES feature_flags(id),
    FOREIGN KEY (dependent_flag_id) REFERENCES feature_flags(id)
) ENGINE=InnoDB;

INSERT INTO feature_flags (name, description, enabled, expiry_date)
VALUES 
('dark_mode_ui', 'Enable dark mode for selected users', TRUE, '2025-12-31 23:59:59'),
('beta_checkout', 'Enable new checkout flow', FALSE, NULL),
('ai_recommendations', 'AI-powered product recommendations', TRUE, '2025-09-01 00:00:00');

INSERT INTO users (created_at, username, email)
VALUES 
(NOW(), 'alice', 'alice@example.com'),
(NOW(), 'bob', 'bob@example.com');

INSERT INTO user_groups (name, description, created_at)
VALUES 
('Admins', 'Admin-level access group', NOW()),
('Beta Testers', 'Early access users', NOW());

INSERT INTO feature_flag_targets (feature_flag_id, user_id, user_group_id, created_at)
VALUES 
(1, 1, NULL, NOW()),     -- dark_mode_ui for alice
(2, NULL, 2, NOW());     -- beta_checkout for Beta Testers group

INSERT INTO environments (name, description, created_at)
VALUES 
('Production', 'Live system', NOW()),
('Staging', 'Pre-production environment', NOW());

INSERT INTO feature_flag_environment (environment_id, feature_flag_id, created_at)
VALUES 
(1, 1, NOW()),  -- dark_mode_ui in Production
(2, 2, NOW());  -- beta_checkout in Staging

INSERT INTO audit_logs (feature_flag_id, action, actor, timestamp)
VALUES 
(1, 'created', 'admin', NOW()),
(2, 'disabled', 'bob', NOW());

INSERT INTO flag_dependencies (feature_flag_id, depends_on_flag_id, created_at)
VALUES 
(3, 1, NOW());  -- ai_recommendations depends on dark_mode_ui

INSERT INTO percentage_rollout (feature_flag_id, percentage, created_at)
VALUES 
(1, 50, NOW()),  -- 50% rollout of dark_mode_ui
(2, 10, NOW());  -- 10% rollout of beta_checkout

INSERT INTO tags (name, description, created_at)
VALUES 
('UI', 'User interface features', NOW()),
('Beta', 'Beta testing features', NOW());

INSERT INTO feature_flag_tags (feature_flag_id, tag_id, created_at)
VALUES 
(1, 1, NOW()),  -- dark_mode_ui tagged as UI
(2, 2, NOW());  -- beta_checkout tagged as Beta

INSERT INTO soft_deleted_flags (feature_flag_id, deleted_at, reason, created_at)
VALUES 
(3, NOW(), 'Deprecated feature', NOW());  -- ai_recommendations soft-deleted

INSERT INTO history (feature_flag_id, action, actor, timestamp)
VALUES 
(1, 'enabled', 'alice', NOW());

INSERT INTO feature_flag_history (feature_flag_id, previous_state, new_state, timestamp)
VALUES 
(1, FALSE, TRUE, NOW());

INSERT INTO feature_flag_users (feature_flag_id, user_id, enabled, created_at)
VALUES 
(1, 1, TRUE, NOW());  -- Alice has dark_mode_ui enabled

INSERT INTO feature_flag_groups (feature_flag_id, user_group_id, enabled, created_at)
VALUES 
(2, 2, TRUE, NOW());  -- beta_checkout enabled for Beta Testers group

INSERT INTO feature_flag_audit (feature_flag_id, action, actor, timestamp)
VALUES 
(1, 'created', 'admin', NOW());

INSERT INTO feature_flag_expiry (feature_flag_id, expiry_date, created_at)
VALUES 
(1, '2025-12-31 23:59:59', NOW());

INSERT INTO feature_flag_dependencies (feature_flag_id, dependent_flag_id, created_at)
VALUES 
(3, 1, NOW());  -- ai_recommendations depends on dark_mode_ui

-- 1. Show all feature flags
SELECT * FROM feature_flags;

-- 2. Show active flags for user 'alice'
SELECT f.name, f.enabled
FROM feature_flags f
JOIN feature_flag_targets t ON t.feature_flag_id = f.id
JOIN users u ON u.id = t.user_id
WHERE u.username = 'alice';

-- 3. Show flags assigned to group 'Beta Testers'
SELECT f.name, g.name AS group_name
FROM feature_flags f
JOIN feature_flag_targets t ON t.feature_flag_id = f.id
JOIN user_groups g ON g.id = t.user_group_id
WHERE g.name = 'Beta Testers';

-- 4. Toggle a feature flag OFF
UPDATE feature_flags SET enabled = FALSE WHERE name = 'dark_mode_ui';

-- 5. Log toggle
INSERT INTO audit_logs (feature_flag_id, action, actor, timestamp)
VALUES (1, 'disabled', 'admin', NOW());

-- 6. Show all flags rolled out to production
SELECT f.name
FROM feature_flags f
JOIN feature_flag_environment fe ON fe.feature_flag_id = f.id
JOIN environments e ON e.id = fe.environment_id
WHERE e.name = 'Production';

-- 7. Show percentage rollout flags
SELECT f.name, p.percentage
FROM feature_flags f
JOIN percentage_rollout p ON p.feature_flag_id = f.id;

SELECT * FROM feature_flags;

UPDATE feature_flags SET enabled = NOT enabled WHERE name = 'dark_mode_ui';

-- Assign dark_mode_ui to Alice
INSERT INTO feature_flag_users (feature_flag_id, user_id, enabled, created_at)
VALUES (
    (SELECT id FROM feature_flags WHERE name = 'dark_mode_ui'),
    (SELECT id FROM users WHERE username = 'alice'),
    TRUE,
    NOW()
);

-- Assign beta_checkout to Beta Testers group
INSERT INTO feature_flag_groups (feature_flag_id, user_group_id, enabled, created_at)
VALUES (
    (SELECT id FROM feature_flags WHERE name = 'beta_checkout'),
    (SELECT id FROM user_groups WHERE name = 'Beta Testers'),
    TRUE,
    NOW()
);
INSERT INTO soft_deleted_flags (feature_flag_id, deleted_at, reason, created_at)
VALUES (
    (SELECT id FROM feature_flags WHERE name = 'ai_recommendations'),
    NOW(),
    'Deprecated feature',
    NOW()
);
UPDATE feature_flags
SET expiry_date = NOW()
WHERE name = 'dark_mode_ui';
SELECT DISTINCT ff.name
FROM feature_flags ff
LEFT JOIN feature_flag_users ffu ON ff.id = ffu.feature_flag_id AND ffu.user_id = (SELECT id FROM users WHERE username = 'alice') AND ffu.enabled = TRUE
LEFT JOIN feature_flag_groups ffg ON ff.id = ffg.feature_flag_id
LEFT JOIN user_groups ug ON ffg.user_group_id = ug.id
LEFT JOIN feature_flag_targets fft ON ff.id = fft.feature_flag_id
WHERE ff.enabled = TRUE
   OR ffu.id IS NOT NULL
   OR ffg.enabled = TRUE;

DELETE FROM feature_flag_users
WHERE feature_flag_id = (SELECT id FROM feature_flags WHERE name = 'dark_mode_ui')
  AND user_id = (SELECT id FROM users WHERE username = 'alice');


-- Flags for Alice:
-- SELECT DISTINCT ff.name FROM feature_flags ff
-- JOIN feature_flag_users ffu ON ff.id = ffu.feature_flag_id
-- WHERE ffu.user_id = (SELECT id FROM users WHERE username = 'alice') AND ffu.enabled = TRUE;

