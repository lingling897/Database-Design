-- 创建港口堆存费计算系统数据库
CREATE DATABASE IF NOT EXISTS port_fee DEFAULT CHARACTER SET utf8mb4;
USE port_fee;

-- 创建港口信息表
CREATE TABLE IF NOT EXISTS ports (
    id INT PRIMARY KEY AUTO_INCREMENT,
    port_name VARCHAR(100) NOT NULL,
    country VARCHAR(50) NOT NULL,
    free_days INT DEFAULT 14,
    fee_rate DECIMAL(10, 2) DEFAULT 10.00,
    create_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 创建货物类型表
CREATE TABLE IF NOT EXISTS cargo_types (
    id INT PRIMARY KEY AUTO_INCREMENT,
    type_name VARCHAR(50) NOT NULL,
    fee_multiplier DECIMAL(5, 2) DEFAULT 1.00,
    create_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 创建堆存记录主表
CREATE TABLE IF NOT EXISTS storage_records (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    port_id INT NOT NULL,
    cargo_type_id INT NOT NULL,
    entry_date DATE NOT NULL,
    exit_date DATE NOT NULL,
    quantity DECIMAL(15, 3) NOT NULL,
    port_type ENUM('装运港', '卸货港') NOT NULL,
    trade_term ENUM('CIF', 'CFR', 'FOB') NOT NULL,
    storage_days INT,
    fee DECIMAL(15, 2),
    responsible_party VARCHAR(20),
    create_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (port_id) REFERENCES ports(id),
    FOREIGN KEY (cargo_type_id) REFERENCES cargo_types(id)
);

-- 创建费用明细子表
CREATE TABLE IF NOT EXISTS fee_details (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    record_id BIGINT NOT NULL,
    day_index INT NOT NULL,
    date DATE NOT NULL,
    quantity DECIMAL(15, 3) NOT NULL,
    rate DECIMAL(10, 2) NOT NULL,
    daily_fee DECIMAL(15, 2) NOT NULL,
    FOREIGN KEY (record_id) REFERENCES storage_records(id)
);

-- 创建视图：堆存费汇总统计
CREATE OR REPLACE VIEW vw_storage_fee_summary AS
SELECT 
    p.port_name,
    ct.type_name AS cargo_type,
    sr.port_type,
    sr.trade_term,
    sr.responsible_party,
    COUNT(sr.id) AS record_count,
    SUM(sr.quantity) AS total_quantity,
    SUM(sr.fee) AS total_fee
FROM 
    storage_records sr
JOIN 
    ports p ON sr.port_id = p.id
JOIN 
    cargo_types ct ON sr.cargo_type_id = ct.id
GROUP BY 
    p.port_name, ct.type_name, sr.port_type, sr.trade_term, sr.responsible_party;    
