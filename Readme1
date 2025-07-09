🚗 WhatNext Vision Motors: Shaping the Future of Mobility with Innovation and Excellence

📘 Project Overview

WhatNext Vision Motors is a pioneering force in the automotive industry, committed to delivering a customer-centric experience through technological innovation. This Salesforce CRM-based project was built to enhance customer satisfaction, optimize dealer assignment, manage vehicle stock efficiently, and automate key business processes.

🔧 Core Features

1. Salesforce CRM Implementation

Store and manage vehicle, dealer, customer, and order information.

Efficiently handle test drive bookings and service requests.

Custom Lightning App: WhatNext Vision Motors

2. Process Automation

Auto-assign orders to the nearest dealer based on the customer's location.

Prevent order creation if the selected vehicle is out of stock.

Send automated email reminders for scheduled test drives.

3. Apex Development

Trigger handler enforces stock validation and stock deduction.

Batch Apex Job updates pending orders based on stock changes.

Scheduled job runs daily to keep order statuses updated.

🧱 Data Model Overview

🔹 Custom Objects & Relationships

Object

Purpose

Relationships

Vehicle__c

Stores vehicle information

Related to Dealer & Orders

Vehicle_Dealer__c

Stores dealer information

Related to Orders

Vehicle_Customer__c

Stores customer information

Related to Orders & Test Drives

Vehicle_Order__c

Tracks vehicle purchases

Related to Customer & Vehicle

Vehicle_Test_Drive__c

Tracks test drive bookings

Related to Customer & Vehicle

Vehicle_Service_Request__c

Tracks vehicle service requests

Related to Customer & Vehicle

📀 Key Fields by Object

🔸 Vehicle__c

Vehicle_Name__c (Text)

Vehicle_Model__c (Picklist: Sedan, SUV, EV)

Stock_Quantity__c (Number)

Price__c (Currency)

Dealer__c (Lookup to Dealer)

Status__c (Picklist: Available, Out of Stock, Discontinued)

🔸 Vehicle_Dealer__c

Dealer_Name__c, Dealer_Location__c, Dealer_Code__c, Phone__c, Email__c

🔸 Vehicle_Customer__c

Customer_Name__c (Text)

Email__c (Email)

Phone__c (Phone)

Address__c (Text)

Preferred_Vehicle_Type__c (Picklist)

🔸 Vehicle_Order__c

Customer__c, Vehicle__c, Order_Date__c, Status__c

🔸 Vehicle_Test_Drive__c

Customer__c, Vehicle__c, Test_Drive_Date__c, Status__c

🔸 Vehicle_Service_Request__c

Customer__c, Vehicle__c, Service_Date__c, Issue_Description__c, Status__c

⚙️ Flows

🔸 Flow: Auto Assign Dealer

Type: Record-TriggeredObject: Vehicle_Order__cTrigger Condition: Status = "Pending"Steps:

Get customer address.

Query dealers with matching location.

Update dealer field on order.

🔸 Flow: Test Drive Email Reminder

Type: Scheduled PathObject: Vehicle_Test_Drive__cCondition: Status = "Scheduled"Trigger: 1 day before Test_Drive_Date__cSteps:

Get customer email.

Send formatted reminder email.

🧐 Apex Logic

🔸 Trigger: VehicleOrderTrigger

trigger VehicleOrderTrigger on Vehicle_Order__c (before insert, before update, after insert, after update) {
    VehicleOrderTriggerHandler.handleTrigger(
        Trigger.new, Trigger.oldMap,
        Trigger.isBefore, Trigger.isAfter,
        Trigger.isInsert, Trigger.isUpdate
    );
}

🔸 Apex Class: VehicleOrderTriggerHandler

public class VehicleOrderTriggerHandler {
    public static void handleTrigger(List<Vehicle_Order__c> newOrders, Map<Id, Vehicle_Order__c> oldOrders,
        Boolean isBefore, Boolean isAfter, Boolean isInsert, Boolean isUpdate) {

        if (isBefore && (isInsert || isUpdate)) {
            preventOrderIfOutOfStock(newOrders);
        }

        if (isAfter && (isInsert || isUpdate)) {
            updateStockOnOrderPlacement(newOrders);
        }
    }

    private static void preventOrderIfOutOfStock(List<Vehicle_Order__c> orders) {
        Set<Id> vehicleIds = new Set<Id>();
        for (Vehicle_Order__c order : orders) {
            if (order.Vehicle__c != null) vehicleIds.add(order.Vehicle__c);
        }
        Map<Id, Vehicle__c> stockMap = new Map<Id, Vehicle__c>(
            [SELECT Id, Stock_Quantity__c FROM Vehicle__c WHERE Id IN :vehicleIds]
        );
        for (Vehicle_Order__c order : orders) {
            if (stockMap.containsKey(order.Vehicle__c) && stockMap.get(order.Vehicle__c).Stock_Quantity__c <= 0) {
                order.addError('This vehicle is out of stock. Order cannot be placed.');
            }
        }
    }

    private static void updateStockOnOrderPlacement(List<Vehicle_Order__c> orders) {
        Set<Id> vehicleIds = new Set<Id>();
        for (Vehicle_Order__c order : orders) {
            if (order.Vehicle__c != null && order.Status__c == 'Confirmed') vehicleIds.add(order.Vehicle__c);
        }
        Map<Id, Vehicle__c> stockMap = new Map<Id, Vehicle__c>(
            [SELECT Id, Stock_Quantity__c FROM Vehicle__c WHERE Id IN :vehicleIds]
        );
        List<Vehicle__c> updates = new List<Vehicle__c>();
        for (Vehicle_Order__c order : orders) {
            Vehicle__c vehicle = stockMap.get(order.Vehicle__c);
            if (vehicle != null && vehicle.Stock_Quantity__c > 0) {
                vehicle.Stock_Quantity__c -= 1;
                updates.add(vehicle);
            }
        }
        if (!updates.isEmpty()) update updates;
    }
}

🔀 Batch Job for Order Confirmation

🔸 Apex Class: VehicleOrderBatch

global class VehicleOrderBatch implements Database.Batchable<sObject> {
    global Database.QueryLocator start(Database.BatchableContext bc) {
        return Database.getQueryLocator([
            SELECT Id, Status__c, Vehicle__c
            FROM Vehicle_Order__c
            WHERE Status__c = 'Pending'
        ]);
    }

    global void execute(Database.BatchableContext bc, List<Vehicle_Order__c> orders) {
        Set<Id> vehicleIds = new Set<Id>();
        for (Vehicle_Order__c order : orders) {
            if (order.Vehicle__c != null) vehicleIds.add(order.Vehicle__c);
        }

        Map<Id, Vehicle__c> stockMap = new Map<Id, Vehicle__c>(
            [SELECT Id, Stock_Quantity__c FROM Vehicle__c WHERE Id IN :vehicleIds]
        );

        List<Vehicle_Order__c> toUpdateOrders = new List<Vehicle_Order__c>();
        List<Vehicle__c> toUpdateVehicles = new List<Vehicle__c>();

        for (Vehicle_Order__c order : orders) {
            Vehicle__c vehicle = stockMap.get(order.Vehicle__c);
            if (vehicle != null && vehicle.Stock_Quantity__c > 0) {
                order.Status__c = 'Confirmed';
                vehicle.Stock_Quantity__c -= 1;
                toUpdateOrders.add(order);
                toUpdateVehicles.add(vehicle);
            }
        }

        if (!toUpdateOrders.isEmpty()) update toUpdateOrders;
        if (!toUpdateVehicles.isEmpty()) update toUpdateVehicles;
    }

    global void finish(Database.BatchableContext bc) {
        System.debug('Vehicle order batch job completed.');
    }
}

🕒 Scheduled Job

🔸 Apex Scheduler: VehicleOrderBatchScheduler

global class VehicleOrderBatchScheduler implements Schedulable {
    global void execute(SchedulableContext sc) {
        Database.executeBatch(new VehicleOrderBatch(), 50);
    }
}

🔸 Cron Job Setup

String cronExp = '0 0 0 * * ?'; // Every day at 12:00 AM
System.schedule('Daily Vehicle Order Processing', cronExp, new VehicleOrderBatchScheduler());

🧑‍💼 Developer Setup Guide

🔹 Step 1: Create Developer Org

Visit: https://developer.salesforce.com/signup

Fill form with:

Role: Developer

Company: Your College Name

Username format: yourname@college.com

🔹 Step 2: Object Creation

Go to Setup → Object Manager → Create Custom Object

Objects:

Vehicle__c

Vehicle_Dealer__c

Vehicle_Customer__c

Vehicle_Order__c

Vehicle_Test_Drive__c

Vehicle_Service_Request__c

🔹 Step 3: Lightning App

App Name: WhatNext Vision Motors

Navigation Items: Vehicle, Dealer, Customer, Order, Test Drive, Service Request, Reports

📈 Learning Outcomes

✅ Salesforce Object and Relationship Design

✅ Lightning App Setup

✅ Flow Automation with Scheduled Paths

✅ Apex Trigger Handling

✅ Batch and Scheduled Apex Jobs

✅ Real-world CRM Automation Techniques
