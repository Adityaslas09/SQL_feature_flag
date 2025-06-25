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
