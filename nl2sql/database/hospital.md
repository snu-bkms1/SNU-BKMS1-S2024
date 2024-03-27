## Table Creation

```sql
CREATE TABLE Physician (
  EmployeeID SERIAL PRIMARY KEY,
  Name TEXT NOT NULL,
  Position TEXT NOT NULL,
  SSN INTEGER NOT NULL UNIQUE
);

CREATE TABLE Department (
  DepartmentID SERIAL PRIMARY KEY,
  Name TEXT NOT NULL,
  Head INTEGER NOT NULL REFERENCES Physician(EmployeeID)
);

CREATE TABLE Affiliated_With (
  Physician INTEGER NOT NULL REFERENCES Physician(EmployeeID),
  Department INTEGER NOT NULL REFERENCES Department(DepartmentID),
  PrimaryAffiliation BOOLEAN NOT NULL,
  PRIMARY KEY(Physician, Department)
);

CREATE TABLE Procedure (
  Code SERIAL PRIMARY KEY,
  Name TEXT NOT NULL,
  Cost REAL NOT NULL
);

CREATE TABLE Trained_In (
  Physician INTEGER NOT NULL REFERENCES Physician(EmployeeID),
  Treatment INTEGER NOT NULL REFERENCES Procedure(Code),
  CertificationDate TIMESTAMP NOT NULL,
  CertificationExpires TIMESTAMP NOT NULL,
  PRIMARY KEY(Physician, Treatment)
);

CREATE TABLE Patient (
  SSN INTEGER PRIMARY KEY,
  Name TEXT NOT NULL,
  Address TEXT NOT NULL,
  Phone TEXT NOT NULL,
  InsuranceID INTEGER NOT NULL,
  PCP INTEGER NOT NULL REFERENCES Physician(EmployeeID)
);

CREATE TABLE Nurse (
  EmployeeID SERIAL PRIMARY KEY,
  Name TEXT NOT NULL,
  Position TEXT NOT NULL,
  Registered BOOLEAN NOT NULL,
  SSN INTEGER NOT NULL UNIQUE
);

CREATE TABLE Appointment (
  AppointmentID SERIAL PRIMARY KEY,
  Patient INTEGER NOT NULL REFERENCES Patient(SSN),
  PrepNurse INTEGER REFERENCES Nurse(EmployeeID),
  Physician INTEGER NOT NULL REFERENCES Physician(EmployeeID),
  Start TIMESTAMP NOT NULL,
  End TIMESTAMP NOT NULL,
  ExaminationRoom TEXT NOT NULL
);

CREATE TABLE Medication (
  Code SERIAL PRIMARY KEY,
  Name TEXT NOT NULL,
  Brand TEXT NOT NULL,
  Description TEXT NOT NULL
);

CREATE TABLE Prescribes (
  Physician INTEGER NOT NULL REFERENCES Physician(EmployeeID),
  Patient INTEGER NOT NULL REFERENCES Patient(SSN),
  Medication INTEGER NOT NULL REFERENCES Medication(Code),
  Date TIMESTAMP NOT NULL,
  Appointment INTEGER REFERENCES Appointment(AppointmentID),
  Dose TEXT NOT NULL,
  PRIMARY KEY(Physician, Patient, Medication, Date)
);

CREATE TABLE Block (
  Floor INTEGER NOT NULL,
  Code INTEGER NOT NULL,
  PRIMARY KEY(Floor, Code)
);

CREATE TABLE Room (
  Number SERIAL PRIMARY KEY,
  Type TEXT NOT NULL,
  BlockFloor INTEGER NOT NULL,
  BlockCode INTEGER NOT NULL,
  Unavailable BOOLEAN NOT NULL,
  FOREIGN KEY(BlockFloor, BlockCode) REFERENCES Block(Floor, Code)
);

CREATE TABLE On_Call (
  Nurse INTEGER NOT NULL REFERENCES Nurse(EmployeeID),
  BlockFloor INTEGER NOT NULL,
  BlockCode INTEGER NOT NULL,
  Start TIMESTAMP NOT NULL,
  End TIMESTAMP NOT NULL,
  PRIMARY KEY(Nurse, BlockFloor, BlockCode, Start, End),
  FOREIGN KEY(BlockFloor, BlockCode) REFERENCES Block(Floor, Code)
);

CREATE TABLE Stay (
  StayID SERIAL PRIMARY KEY,
  Patient INTEGER NOT NULL REFERENCES Patient(SSN),
  Room INTEGER NOT NULL REFERENCES Room(Number),
  Start TIMESTAMP NOT NULL,
  End TIMESTAMP NOT NULL
);

CREATE TABLE Undergoes (
  Patient INTEGER NOT NULL REFERENCES Patient(SSN),
  Procedure INTEGER NOT NULL REFERENCES Procedure(Code),
  Stay INTEGER NOT NULL REFERENCES Stay(StayID),
  Date TIMESTAMP NOT NULL,
  Physician INTEGER NOT NULL REFERENCES Physician(EmployeeID),
  AssistingNurse INTEGER REFERENCES Nurse(EmployeeID),
  PRIMARY KEY(Patient, Procedure, Stay, Date)
);
```

## Insert Data

```sql
INSERT INTO Physician (EmployeeID, Name, Position, SSN) VALUES
(1, 'John Dorian', 'Staff Internist', 111111111),
(2, 'Elliot Reid', 'Attending Physician', 222222222),
(3, 'Christopher Turk', 'Surgical Attending Physician', 333333333),
(4, 'Percival Cox', 'Senior Attending Physician', 444444444),
(5, 'Bob Kelso', 'Head Chief of Medicine', 555555555),
(6, 'Todd Quinlan', 'Surgical Attending Physician', 666666666),
(7, 'John Wen', 'Surgical Attending Physician', 777777777),
(8, 'Keith Dudemeister', 'MD Resident', 888888888),
(9, 'Molly Clock', 'Attending Psychiatrist', 999999999);

INSERT INTO Department (DepartmentID, Name, Head) VALUES
(1, 'General Medicine', 4),
(2, 'Surgery', 7),
(3, 'Psychiatry', 9);

INSERT INTO Affiliated_With (Physician, Department, PrimaryAffiliation) VALUES
(1, 1, TRUE),
(2, 1, TRUE),
(3, 1, FALSE),
(3, 2, TRUE),
(4, 1, TRUE),
(5, 1, TRUE),
(6, 2, TRUE),
(7, 1, FALSE),
(7, 2, TRUE),
(8, 1, TRUE),
(9, 3, TRUE);

INSERT INTO Procedure (Code, Name, Cost) VALUES
(1, 'Reverse Rhinopodoplasty', 1500.0),
(2, 'Obtuse Pyloric Recombobulation', 3750.0),
(3, 'Folded Demiophtalmectomy', 4500.0),
(4, 'Complete Walletectomy', 10000.0),
(5, 'Obfuscated Dermogastrotomy', 4899.0),
(6, 'Reversible Pancreomyoplasty', 5600.0),
(7, 'Follicular Demiectomy', 25.0);

INSERT INTO Patient (SSN, Name, Address, Phone, InsuranceID, PCP) VALUES
(100000001, 'John Smith', '42 Foobar Lane', '555-0256', 68476213, 1),
(100000002, 'Grace Ritchie', '37 Snafu Drive', '555-0512', 36546321, 2),
(100000003, 'Random J. Patient', '101 Omgbbq Street', '555-1204', 65465421, 2),
(100000004, 'Dennis Doe', '1100 Foobaz Avenue', '555-2048', 68421879, 3);

INSERT INTO Nurse (EmployeeID, Name, Position, Registered, SSN) VALUES
(101, 'Carla Espinosa', 'Head Nurse', TRUE, 111111110),
(102, 'Laverne Roberts', 'Nurse', TRUE, 222222220),
(103, 'Paul Flowers', 'Nurse', FALSE, 333333330);
```
