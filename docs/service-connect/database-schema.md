# Database Schema — Service Connect App

Database: MySQL.

This schema is a planning baseline for the modular monolith. Use migrations such as Flyway or Liquibase in the actual product repository.

## Main Tables

### users

```sql
CREATE TABLE users (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  email VARCHAR(255) NOT NULL UNIQUE,
  phone VARCHAR(30),
  password_hash VARCHAR(255),
  role VARCHAR(30) NOT NULL,
  status VARCHAR(30) NOT NULL,
  created_at DATETIME NOT NULL,
  updated_at DATETIME NOT NULL
);
```

### customer_profiles

```sql
CREATE TABLE customer_profiles (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  user_id BIGINT NOT NULL UNIQUE,
  full_name VARCHAR(255) NOT NULL,
  phone VARCHAR(30),
  address VARCHAR(500),
  created_at DATETIME NOT NULL,
  updated_at DATETIME NOT NULL,
  CONSTRAINT fk_customer_user FOREIGN KEY (user_id) REFERENCES users(id)
);
```

### provider_profiles

```sql
CREATE TABLE provider_profiles (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  user_id BIGINT NOT NULL UNIQUE,
  business_name VARCHAR(255) NOT NULL,
  contact_name VARCHAR(255),
  phone VARCHAR(30),
  service_area VARCHAR(500),
  verification_status VARCHAR(30) NOT NULL,
  score DECIMAL(5,2) DEFAULT 0,
  created_at DATETIME NOT NULL,
  updated_at DATETIME NOT NULL,
  CONSTRAINT fk_provider_user FOREIGN KEY (user_id) REFERENCES users(id)
);
```

### service_categories

```sql
CREATE TABLE service_categories (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(255) NOT NULL,
  code VARCHAR(100) NOT NULL UNIQUE,
  active BOOLEAN NOT NULL DEFAULT TRUE,
  created_at DATETIME NOT NULL,
  updated_at DATETIME NOT NULL
);
```

### leads

```sql
CREATE TABLE leads (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  customer_id BIGINT NOT NULL,
  category_id BIGINT,
  title VARCHAR(255) NOT NULL,
  description TEXT,
  location_text VARCHAR(500),
  contact_name VARCHAR(255),
  contact_phone VARCHAR(30),
  status VARCHAR(50) NOT NULL,
  credit_cost INT,
  max_accept_count INT NOT NULL DEFAULT 3,
  accepted_count INT NOT NULL DEFAULT 0,
  verified_by BIGINT,
  verified_at DATETIME,
  created_at DATETIME NOT NULL,
  updated_at DATETIME NOT NULL,
  CONSTRAINT fk_lead_customer FOREIGN KEY (customer_id) REFERENCES customer_profiles(id),
  CONSTRAINT fk_lead_category FOREIGN KEY (category_id) REFERENCES service_categories(id),
  CONSTRAINT fk_lead_verified_by FOREIGN KEY (verified_by) REFERENCES users(id)
);
```

### lead_assignments

```sql
CREATE TABLE lead_assignments (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  lead_id BIGINT NOT NULL,
  provider_id BIGINT NOT NULL,
  status VARCHAR(50) NOT NULL,
  assigned_by BIGINT NOT NULL,
  assigned_at DATETIME NOT NULL,
  unlocked_at DATETIME,
  contacted_at DATETIME,
  result_status VARCHAR(50),
  created_at DATETIME NOT NULL,
  updated_at DATETIME NOT NULL,
  CONSTRAINT uq_lead_provider UNIQUE (lead_id, provider_id),
  CONSTRAINT fk_assignment_lead FOREIGN KEY (lead_id) REFERENCES leads(id),
  CONSTRAINT fk_assignment_provider FOREIGN KEY (provider_id) REFERENCES provider_profiles(id),
  CONSTRAINT fk_assignment_admin FOREIGN KEY (assigned_by) REFERENCES users(id)
);
```

### provider_credit_wallets

```sql
CREATE TABLE provider_credit_wallets (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  provider_id BIGINT NOT NULL UNIQUE,
  balance INT NOT NULL DEFAULT 0,
  created_at DATETIME NOT NULL,
  updated_at DATETIME NOT NULL,
  CONSTRAINT fk_wallet_provider FOREIGN KEY (provider_id) REFERENCES provider_profiles(id)
);
```

### credit_transactions

```sql
CREATE TABLE credit_transactions (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  provider_id BIGINT NOT NULL,
  lead_id BIGINT,
  assignment_id BIGINT,
  amount INT NOT NULL,
  type VARCHAR(50) NOT NULL,
  reason VARCHAR(255),
  idempotency_key VARCHAR(100),
  created_by BIGINT,
  created_at DATETIME NOT NULL,
  CONSTRAINT uq_credit_idempotency UNIQUE (idempotency_key),
  CONSTRAINT fk_credit_provider FOREIGN KEY (provider_id) REFERENCES provider_profiles(id),
  CONSTRAINT fk_credit_lead FOREIGN KEY (lead_id) REFERENCES leads(id),
  CONSTRAINT fk_credit_assignment FOREIGN KEY (assignment_id) REFERENCES lead_assignments(id),
  CONSTRAINT fk_credit_created_by FOREIGN KEY (created_by) REFERENCES users(id)
);
```

## Recommended Indexes

```sql
CREATE INDEX idx_users_role_status ON users(role, status);
CREATE INDEX idx_leads_status_created ON leads(status, created_at);
CREATE INDEX idx_leads_customer_created ON leads(customer_id, created_at);
CREATE INDEX idx_assignments_provider_status ON lead_assignments(provider_id, status);
CREATE INDEX idx_assignments_lead_status ON lead_assignments(lead_id, status);
CREATE INDEX idx_credit_provider_created ON credit_transactions(provider_id, created_at);
```

## Schema Rules

1. Every mutable business table should have `created_at` and `updated_at`.
2. Use enum-like strings initially for readability; migrate to lookup tables only when needed.
3. Store credit transaction ledger permanently; never update old ledger rows.
4. Wallet balance is derived operational state and must be protected by transaction locks.
5. Lead contact fields are sensitive and must not be returned before provider unlock.
6. Use migration files for all schema changes.
