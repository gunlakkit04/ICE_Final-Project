Canva https://canva.link/m8us5gcmvufgvns

เครื่องต้องมี node.js, npm.js, git hub windown, vscode, Xampp

ทำการเปิดหน้า Xampp Apache,Mysql

เปิดหน้า cmd ขึ้นมาก่อนเริ่มต้นที่
git clone https://github.com/gunlakkit04/final

แล้วเข้าไฟล์ cd final

เปิดหน้า Terminal ใน vscode มา 2 หน้า

Terminal หน้าที่ 1 
1. cd 01_api
2. npm init -y
3. npm i express mysql2 cors dotenv


มาที่ localhost/phpmyadmin/ เพื่อมาสร้าง databases กดที่ New->SQL  แล้วใส่โค๊ตนี้ไป

------------------------------------------------------------------------------------------------------------------------------------
CREATE DATABASE IF NOT EXISTS farm_management
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci;

USE farm_management;

CREATE TABLE IF NOT EXISTS farm (
    farm_id INT AUTO_INCREMENT PRIMARY KEY,
    farm_name VARCHAR(100) NOT NULL,
    location VARCHAR(200),
    phone VARCHAR(20)
);

CREATE TABLE IF NOT EXISTS house (
    house_id INT AUTO_INCREMENT PRIMARY KEY,
    house_name VARCHAR(100) NOT NULL,
    farm_id INT NOT NULL,
    CONSTRAINT fk_house_farm
      FOREIGN KEY (farm_id) REFERENCES farm(farm_id)
      ON DELETE RESTRICT
      ON UPDATE CASCADE
);

CREATE TABLE IF NOT EXISTS employee (
    employee_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    phone VARCHAR(20),
    position VARCHAR(50),
    farm_id INT NOT NULL,
    CONSTRAINT fk_employee_farm
      FOREIGN KEY (farm_id) REFERENCES farm(farm_id)
      ON DELETE RESTRICT
      ON UPDATE CASCADE
);

CREATE TABLE IF NOT EXISTS batch (
    batch_id INT AUTO_INCREMENT PRIMARY KEY,
    breed VARCHAR(100) NOT NULL,
    start_date DATE NOT NULL,
    end_date DATE NULL,
    status VARCHAR(20) NOT NULL,
    initial_qty INT NOT NULL DEFAULT 0,
    current_qty INT NOT NULL DEFAULT 0,
    house_id INT NOT NULL,
    CONSTRAINT fk_batch_house
      FOREIGN KEY (house_id) REFERENCES house(house_id)
      ON DELETE RESTRICT
      ON UPDATE CASCADE
);

CREATE TABLE IF NOT EXISTS receive (
    receive_id INT AUTO_INCREMENT PRIMARY KEY,
    receive_date DATE NOT NULL,
    quantity INT NOT NULL DEFAULT 0,
    avg_weight DECIMAL(5,2),
    batch_id INT NOT NULL,
    source_farm_id INT,
    employee_id INT NOT NULL,
    CONSTRAINT fk_receive_batch
      FOREIGN KEY (batch_id) REFERENCES batch(batch_id)
      ON DELETE RESTRICT
      ON UPDATE CASCADE,
    CONSTRAINT fk_receive_source_farm
      FOREIGN KEY (source_farm_id) REFERENCES farm(farm_id)
      ON DELETE RESTRICT
      ON UPDATE CASCADE,
    CONSTRAINT fk_receive_employee
      FOREIGN KEY (employee_id) REFERENCES employee(employee_id)
      ON DELETE RESTRICT
      ON UPDATE CASCADE
);

CREATE TABLE IF NOT EXISTS feeding (
    feed_id INT AUTO_INCREMENT PRIMARY KEY,
    feed_date DATE NOT NULL,
    feed_name VARCHAR(100),
    feed_amount DECIMAL(10,2) DEFAULT 0,
    water_amount DECIMAL(10,2) DEFAULT 0,
    batch_id INT NOT NULL,
    employee_id INT NOT NULL,
    CONSTRAINT fk_feeding_batch
      FOREIGN KEY (batch_id) REFERENCES batch(batch_id)
      ON DELETE RESTRICT
      ON UPDATE CASCADE,
    CONSTRAINT fk_feeding_employee
      FOREIGN KEY (employee_id) REFERENCES employee(employee_id)
      ON DELETE RESTRICT
      ON UPDATE CASCADE
);

CREATE TABLE IF NOT EXISTS production (
    production_id INT AUTO_INCREMENT PRIMARY KEY,
    production_date DATE NOT NULL,
    egg_qty INT NOT NULL DEFAULT 0,
    broken_qty INT DEFAULT 0,
    total_weight_egg DECIMAL(10,2) DEFAULT 0,
    egg_size VARCHAR(20),
    batch_id INT NOT NULL,
    employee_id INT NOT NULL,
    CONSTRAINT fk_production_batch
      FOREIGN KEY (batch_id) REFERENCES batch(batch_id)
      ON DELETE RESTRICT
      ON UPDATE CASCADE,
    CONSTRAINT fk_production_employee
      FOREIGN KEY (employee_id) REFERENCES employee(employee_id)
      ON DELETE RESTRICT
      ON UPDATE CASCADE
);

CREATE TABLE IF NOT EXISTS environment (
    env_id INT AUTO_INCREMENT PRIMARY KEY,
    recorded_at DATETIME NOT NULL,
    temperature DECIMAL(5,2),
    humidity DECIMAL(5,2),
    light DECIMAL(10,2),
    ammonia DECIMAL(5,2),
    cleaning BOOLEAN NOT NULL DEFAULT FALSE,
    light_status BOOLEAN NOT NULL DEFAULT FALSE,
    detail TEXT,
    house_id INT NOT NULL,
    employee_id INT NOT NULL,
    CONSTRAINT fk_environment_house
      FOREIGN KEY (house_id) REFERENCES house(house_id)
      ON DELETE RESTRICT
      ON UPDATE CASCADE,
    CONSTRAINT fk_environment_employee
      FOREIGN KEY (employee_id) REFERENCES employee(employee_id)
      ON DELETE RESTRICT
      ON UPDATE CASCADE
);

CREATE TABLE IF NOT EXISTS incident (
    incident_id INT AUTO_INCREMENT PRIMARY KEY,
    incident_date DATE NOT NULL,
    incident_type VARCHAR(50) NOT NULL,
    detail TEXT,
    qty_affected INT DEFAULT 0,
    batch_id INT NOT NULL,
    employee_id INT NOT NULL,
    CONSTRAINT fk_incident_batch
      FOREIGN KEY (batch_id) REFERENCES batch(batch_id)
      ON DELETE RESTRICT
      ON UPDATE CASCADE,
    CONSTRAINT fk_incident_employee
      FOREIGN KEY (employee_id) REFERENCES employee(employee_id)
      ON DELETE RESTRICT
      ON UPDATE CASCADE
);

CREATE TABLE IF NOT EXISTS users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    role VARCHAR(20) NOT NULL,
    employee_id INT NOT NULL,
    CONSTRAINT fk_users_employee
      FOREIGN KEY (employee_id) REFERENCES employee(employee_id)
      ON DELETE RESTRICT
      ON UPDATE CASCADE
);

INSERT INTO farm (farm_name, location, phone) VALUES
('Happy Farm', 'Chiang Mai', '089-123-4567'),
('Green Valley Farm', 'Lampang', '081-555-1234');

INSERT INTO house (house_name, farm_id) VALUES
('House A', 1),
('House B', 1),
('House C', 2);

INSERT INTO employee (name, phone, position, farm_id) VALUES
('Somchai', '081-222-3333', 'Manager', 1),
('Suda', '082-444-5555', 'Staff', 1),
('Anan', '083-666-7777', 'Admin', 2);

INSERT INTO batch (breed, start_date, end_date, status, initial_qty, current_qty, house_id) VALUES
('Hy-Line Brown', '2026-04-19', NULL, 'active', 10000, 9500, 1),
('Lohmann Brown', '2026-04-20', NULL, 'active', 12000, 11800, 2),
('ISA Brown', '2026-03-01', '2026-04-30', 'inactive', 8000, 7600, 3);

INSERT INTO receive (receive_date, quantity, avg_weight, batch_id, source_farm_id, employee_id) VALUES
('2026-04-19', 10000, 35.50, 1, 2, 1),
('2026-04-20', 12000, 36.00, 2, 1, 3);

INSERT INTO feeding (feed_date, feed_name, feed_amount, water_amount, batch_id, employee_id) VALUES
('2026-04-20', 'Starter Feed', 150.50, 200.00, 1, 1),
('2026-04-20', 'Layer Feed', 180.00, 250.00, 2, 2);

INSERT INTO production (production_date, egg_qty, broken_qty, total_weight_egg, egg_size, batch_id, employee_id) VALUES
('2026-04-20', 800, 20, 500.75, 'L', 1, 1),
('2026-04-20', 920, 15, 560.20, 'XL', 2, 2);

INSERT INTO environment (recorded_at, temperature, humidity, light, ammonia, cleaning, light_status, detail, house_id, employee_id) VALUES
('2026-04-20 08:00:00', 30.50, 70.25, 500.00, 10.50, TRUE, TRUE, 'Normal', 1, 1),
('2026-04-20 08:15:00', 29.80, 68.10, 480.00, 9.80, FALSE, TRUE, 'Stable condition', 2, 2);

INSERT INTO incident (incident_date, incident_type, detail, qty_affected, batch_id, employee_id) VALUES
('2026-04-20', 'Sick', 'Mild respiratory symptoms', 12, 1, 1),
('2026-04-20', 'Mortality', 'Unexpected loss during the night', 4, 2, 2);

-------------------------------------------------------------------------------------------------------------------------------------------
ทดสอบเข้า http://localhost:3001/health 
จะขึ้น
{
  "status": "ok",
  "db": true
}
แบบนี้คือใช้งานได้

Terminal หน้าที่ 2
1. cd 02_frontend
2. npm install
3. npm run dev

กลับมาที่ Terminal หน้า1
1. cd 01_api
2. node index.js

ตอนนี้สามารถเข้า localhost/3000 แล้วก็สามารถใช้งาน  Wed ได้แล้ว
