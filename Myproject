-- Create the database
CREATE DATABASE ClinicBookingSystem;
USE ClinicBookingSystem;

-- Patients table (1-to-many with Appointments)
CREATE TABLE Patients (
    patient_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    date_of_birth DATE NOT NULL,
    gender CHAR(1) CHECK (gender IN ('M', 'F', 'O')),
    address VARCHAR(100),
    city VARCHAR(50),
    state VARCHAR(50),
    zip_code VARCHAR(20),
    phone VARCHAR(20) NOT NULL,
    email VARCHAR(100) UNIQUE,
    insurance_provider VARCHAR(50),
    insurance_policy_number VARCHAR(50),
    registration_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT chk_email CHECK (email LIKE '%@%.%')
);

-- Doctors table (1-to-many with Appointments and DoctorSpecialties)
CREATE TABLE Doctors (
    doctor_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    license_number VARCHAR(50) UNIQUE NOT NULL,
    specialization VARCHAR(100),
    phone VARCHAR(20) NOT NULL,
    email VARCHAR(100) UNIQUE,
    hire_date DATE NOT NULL,
    active_status BOOLEAN DEFAULT TRUE,
    CONSTRAINT chk_doctor_email CHECK (email LIKE '%@%.%')
);

-- Specialties table (many-to-many with Doctors through DoctorSpecialties)
CREATE TABLE Specialties (
    specialty_id INT PRIMARY KEY AUTO_INCREMENT,
    specialty_name VARCHAR(100) UNIQUE NOT NULL,
    description TEXT
);

-- DoctorSpecialties junction table (many-to-many relationship)
CREATE TABLE DoctorSpecialties (
    doctor_id INT NOT NULL,
    specialty_id INT NOT NULL,
    PRIMARY KEY (doctor_id, specialty_id),
    FOREIGN KEY (doctor_id) REFERENCES Doctors(doctor_id) ON DELETE CASCADE,
    FOREIGN KEY (specialty_id) REFERENCES Specialties(specialty_id) ON DELETE CASCADE
);

-- Appointments table (connects Patients, Doctors, and Rooms)
CREATE TABLE Appointments (
    appointment_id INT PRIMARY KEY AUTO_INCREMENT,
    patient_id INT NOT NULL,
    doctor_id INT NOT NULL,
    room_id INT,
    appointment_date DATE NOT NULL,
    start_time TIME NOT NULL,
    end_time TIME NOT NULL,
    status ENUM('Scheduled', 'Completed', 'Cancelled', 'No-Show') DEFAULT 'Scheduled',
    reason TEXT,
    notes TEXT,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (patient_id) REFERENCES Patients(patient_id) ON DELETE CASCADE,
    FOREIGN KEY (doctor_id) REFERENCES Doctors(doctor_id) ON DELETE CASCADE,
    CONSTRAINT chk_appointment_time CHECK (end_time > start_time)
);

-- Rooms table (1-to-many with Appointments)
CREATE TABLE Rooms (
    room_id INT PRIMARY KEY AUTO_INCREMENT,
    room_number VARCHAR(20) UNIQUE NOT NULL,
    room_type VARCHAR(50) NOT NULL,
    floor_number INT NOT NULL,
    capacity INT DEFAULT 1,
    available BOOLEAN DEFAULT TRUE
);

-- Add foreign key to Appointments after Rooms is created
ALTER TABLE Appointments
ADD FOREIGN KEY (room_id) REFERENCES Rooms(room_id) ON DELETE SET NULL;

-- MedicalRecords table (1-to-1 with Appointments)
CREATE TABLE MedicalRecords (
    record_id INT PRIMARY KEY AUTO_INCREMENT,
    appointment_id INT UNIQUE,
    patient_id INT NOT NULL,
    doctor_id INT NOT NULL,
    diagnosis TEXT,
    treatment TEXT,
    prescription TEXT,
    notes TEXT,
    record_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (appointment_id) REFERENCES Appointments(appointment_id) ON DELETE SET NULL,
    FOREIGN KEY (patient_id) REFERENCES Patients(patient_id) ON DELETE CASCADE,
    FOREIGN KEY (doctor_id) REFERENCES Doctors(doctor_id) ON DELETE CASCADE
);

-- Billing table (1-to-1 with Appointments)
CREATE TABLE Billing (
    bill_id INT PRIMARY KEY AUTO_INCREMENT,
    appointment_id INT UNIQUE,
    patient_id INT NOT NULL,
    total_amount DECIMAL(10, 2) NOT NULL,
    insurance_covered DECIMAL(10, 2) DEFAULT 0,
    patient_balance DECIMAL(10, 2) NOT NULL,
    billing_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    payment_status ENUM('Pending', 'Paid', 'Partially Paid', 'Insurance Claim') DEFAULT 'Pending',
    due_date DATE,
    FOREIGN KEY (appointment_id) REFERENCES Appointments(appointment_id) ON DELETE SET NULL,
    FOREIGN KEY (patient_id) REFERENCES Patients(patient_id) ON DELETE CASCADE
);

-- Payments table (1-to-many with Billing)
CREATE TABLE Payments (
    payment_id INT PRIMARY KEY AUTO_INCREMENT,
    bill_id INT NOT NULL,
    amount DECIMAL(10, 2) NOT NULL,
    payment_method ENUM('Cash', 'Credit Card', 'Debit Card', 'Insurance', 'Check') NOT NULL,
    payment_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    transaction_reference VARCHAR(100),
    notes TEXT,
    FOREIGN KEY (bill_id) REFERENCES Billing(bill_id) ON DELETE CASCADE
);

-- Staff table (clinic staff who aren't doctors)
CREATE TABLE Staff (
    staff_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    role VARCHAR(50) NOT NULL,
    phone VARCHAR(20) NOT NULL,
    email VARCHAR(100) UNIQUE,
    hire_date DATE NOT NULL,
    active_status BOOLEAN DEFAULT TRUE,
    CONSTRAINT chk_staff_email CHECK (email LIKE '%@%.%')
);

-- Schedule table (for doctor availability)
CREATE TABLE Schedule (
    schedule_id INT PRIMARY KEY AUTO_INCREMENT,
    doctor_id INT NOT NULL,
    day_of_week ENUM('Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday') NOT NULL,
    start_time TIME NOT NULL,
    end_time TIME NOT NULL,
    is_available BOOLEAN DEFAULT TRUE,
    FOREIGN KEY (doctor_id) REFERENCES Doctors(doctor_id) ON DELETE CASCADE,
    CONSTRAINT chk_schedule_time CHECK (end_time > start_time)
);

-- Create indexes for better performance
CREATE INDEX idx_appointment_date ON Appointments(appointment_date);
CREATE INDEX idx_patient_name ON Patients(last_name, first_name);
CREATE INDEX idx_doctor_name ON Doctors(last_name, first_name);
CREATE INDEX idx_billing_status ON Billing(payment_status);
