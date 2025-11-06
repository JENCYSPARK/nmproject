🏥 Medical Inventory Management (MIM)

📘 Overview

The Medical Inventory Management System (MIM) is a Salesforce CRM-based solution designed to automate and streamline the management of medical supplies within hospitals and pharmacies.
It integrates Salesforce’s automation, analytics, and customization tools to ensure real-time inventory tracking, expiry monitoring, and supplier management.

🎯 Objectives

1. Centralized Inventory Data – Maintain structured records of medicines, suppliers, and stock transactions.
2. Automated Reorder Alerts – Notify users when stock levels fall below predefined thresholds.
3. Expiry Tracking – Monitor expiry dates and alert staff for timely replacement or disposal.
4. Supplier Management – Manage supplier profiles, payment details, and purchase histories.
5. Operational Efficiency – Reduce manual effort through automation using Flows and Process Builder.
6. Role-Based Access Control – Define permissions for Admin, Pharmacist, and Store Manager.
7. Data-Driven Insights – Generate dashboards and reports for informed decision-making.

💡 Features

* Real-time stock visibility
* Expiry and reorder alerts via Salesforce automation tools
* Role-based dashboards and reports
* Apex trigger for automatic purchase order cost calculation
* Supplier and purchase order linkage
* Intuitive Lightning App interface

⚙ Technical Design

Custom Objects

| Object                    | Description                                                 |
| ------------------------- | ----------------------------------------------------------- |
|  Medicine (Product)       | Details of each medicine (name, price, stock, expiry date). |
|  Supplier                 | Supplier details and contact information.                   |
|  Purchase Order           | Tracks orders placed with suppliers.                        |
|  Order Item               | Links medicines to purchase orders.                         |
|  Inventory Transaction    | Logs inflow and outflow of stock.                           |

Relationships

* Supplier → Purchase Order: Lookup
* Purchase Order → Order Item: Master-Detail
* Medicine → Order Item: Lookup

⚡ Salesforce Components Used

* *Custom Objects & Relationships*
* *Apex Trigger and Handler Class* (for total order cost calculation)
* *Flows, Process Builder, and Validation Rules*
* *Dashboards & Reports*
* *Profiles, Roles, and Permission Sets*
* *Lightning App Builder*

🧩 Apex Trigger Example

apex
trigger CalculateTotalAmountTrigger on Order_Item__c (after insert, after update, after delete, after undelete) {
    CalculateTotalAmountHandler.calculateTotal(
        Trigger.new, Trigger.old, Trigger.isInsert, 
        Trigger.isUpdate, Trigger.isDelete, Trigger.isUndelete
    );
}


### *Handler Class*

apex
public class CalculateTotalAmountHandler {
    public static void calculateTotal(
        List<Order_Item__c> newItems, List<Order_Item__c> oldItems, 
        Boolean isInsert, Boolean isUpdate, Boolean isDelete, Boolean isUndelete
    ) {
        Set<Id> parentIds = new Set<Id>();
        if (isInsert || isUpdate || isUndelete) {
            for (Order_Item__c item : newItems) parentIds.add(item.Purchase_Order_Id__c);
        }
        if (isUpdate || isDelete) {
            for (Order_Item__c item : oldItems) parentIds.add(item.Purchase_Order_Id__c);
        }

        if (!parentIds.isEmpty()) {
            List<AggregateResult> results = [
                SELECT Purchase_Order_Id__c, SUM(Amount__c) totalAmount
                FROM Order_Item__c
                WHERE Purchase_Order_Id__c IN :parentIds
                GROUP BY Purchase_Order_Id__c
            ];

            List<Purchase_Order__c> ordersToUpdate = new List<Purchase_Order__c>();
            for (AggregateResult r : results) {
                ordersToUpdate.add(new Purchase_Order__c(
                    Id = (Id)r.get('Purchase_Order_Id__c'),
                    Total_Order_cost__c = (Decimal)r.get('totalAmount')
                ));
            }
            if (!ordersToUpdate.isEmpty()) update ordersToUpdate;
        }
    }
}


📊 Dashboards and Reports

* *Reports:* Purchase Orders by Supplier (Summary)
* *Dashboard:* Medical Inventory Dashboard with visual charts and KPIs

🧠 Best Practices Followed

* Trigger-Handler design pattern
* Bulkified Apex logic
* Exception handling and logging
* Validation rules for data accuracy
* Role-based security configuration
* Dynamic dashboards for real-time analytics

🧪 Testing & Validation

* All Flows, triggers, and automation tools tested successfully.
* Data integrity and user permissions validated.
* Email alerts verified (minor spam filter adjustments needed).

🚀 Future Enhancements

* *Mobile App Integration* for stock management on the go
* *ERP Integration* with accounting systems
* *AI-based Forecasting* for medicine demand prediction

📚 References

* [Salesforce Developer Signup](https://developer.salesforce.com/signup)
* [Trailhead by Salesforce](https://trailhead.salesforce.com)
* [Salesforce Help](https://help.salesforce.com)
* [SalesforceBen](https://www.salesforceben.com)
* [Admin Salesforce](https://admin.salesforce.com)


