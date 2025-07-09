# 🚗 WhatNext Vision Motors: Shaping the Future of Mobility with Innovation and Excellence

## 📘 Project Overview

**WhatNext Vision Motors** is a pioneering force in the automotive industry, committed to delivering a customer-centric experience through technological innovation. This Salesforce CRM-based project was built to enhance customer satisfaction, optimize dealer assignment, manage vehicle stock efficiently, and automate key business processes.

---

## 🔧 Core Features

### 1. **Salesforce CRM Implementation**
- Store and manage vehicle, dealer, customer, and order information.
- Efficiently handle test drive bookings and service requests.
- Custom Lightning App: *WhatNext Vision Motors*

### 2. **Process Automation**
- Auto-assign orders to the nearest dealer based on the customer's location.
- Prevent order creation if the selected vehicle is out of stock.
- Send automated email reminders for scheduled test drives.

### 3. **Apex Development**
- Trigger handler enforces stock validation and stock deduction.
- Batch Apex Job updates pending orders based on stock changes.
- Scheduled job runs daily to keep order statuses updated.

---

## 🧱 Data Model Overview

### 🔹 Custom Objects & Relationships

| Object                     | Purpose                          | Relationships                    |
|---------------------------|----------------------------------|----------------------------------|
| Vehicle__c                | Stores vehicle information        | Related to Dealer & Orders       |
| Vehicle_Dealer__c         | Stores dealer information         | Related to Orders                |
| Vehicle_Customer__c       | Stores customer information       | Related to Orders & Test Drives  |
| Vehicle_Order__c          | Tracks vehicle purchases          | Related to Customer & Vehicle    |
| Vehicle_Test_Drive__c     | Tracks test drive bookings        | Related to Customer & Vehicle    |
| Vehicle_Service_Request__c| Tracks vehicle service requests   | Related to Customer & Vehicle    |

---

## 📀 Key Fields by Object

### 🔸 Vehicle__c
- `Vehicle_Name__c` (Text)
- `Vehicle_Model__c` (Picklist: Sedan, SUV, EV)
- `Stock_Quantity__c` (Number)
- `Price__c` (Currency)
- `Dealer__c` (Lookup to Dealer)
- `Status__c` (Picklist: Available, Out of Stock, Discontinued)

### 🔸 Vehicle_Dealer__c
- `Dealer_Name__c`, `Dealer_Location__c`, `Dealer_Code__c`, `Phone__c`, `Email__c`

### 🔸 Vehicle_Customer__c
- `Customer_Name__c` (Text)
- `Email__c` (Email)
- `Phone__c` (Phone)
- `Address__c` (Text)
- `Preferred_Vehicle_Type__c` (Picklist)

### 🔸 Vehicle_Order__c
- `Customer__c`, `Vehicle__c`, `Order_Date__c`, `Status__c`

### 🔸 Vehicle_Test_Drive__c
- `Customer__c`, `Vehicle__c`, `Test_Drive_Date__c`, `Status__c`

### 🔸 Vehicle_Service_Request__c
- `Customer__c`, `Vehicle__c`, `Service_Date__c`, `Issue_Description__c`, `Status__c`

---

## ⚙️ Flows

### 🔸 Flow: Auto Assign Dealer
**Type**: Record-Triggered  
**Object**: Vehicle_Order__c  
**Trigger Condition**: Status = "Pending"  
**Steps**:
1. Get customer address.
2. Query dealers with matching location.
3. Update dealer field on order.

### 🔸 Flow: Test Drive Email Reminder
**Type**: Scheduled Path  
**Object**: Vehicle_Test_Drive__c  
**Condition**: Status = "Scheduled"  
**Trigger**: 1 day before `Test_Drive_Date__c`  
**Steps**:
1. Get customer email.
2. Send formatted reminder email.




🙌 Credits

Project Owner: Sohan D Souza
Platform: SkillWallet
