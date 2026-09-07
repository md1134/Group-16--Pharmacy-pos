# Pharmacy POS – System Walkthrough

**Project:** Pharmacy Point of Sale (POS) System  
**Phase:** Elaboration  
**Contributor:** Joshua Kamunda  
**Role:** System Walkthrough – The Problem and the System in Use

---

## 1. Purpose

The Pharmacy POS system is designed to support the daily operations of pharmacies by integrating sales, inventory management, clinical workflows, prescriptions, procurement, and user roles into one system.

The system addresses the problems associated with manual pharmacy operations, including difficulties in tracking stock, processing sales, managing prescriptions, monitoring cash, and maintaining pharmacy records.

The system also supports multiple pharmacies while keeping their data isolated from one another.

---

## 2. System Dashboard

The dashboard provides users with an overview of the current pharmacy operations.

It displays information such as:

- Today's takings
- Current stock count
- Low-stock alerts
- Clinic waiting queue
- Pharmacy information and branding

The dashboard obtains its information from the database rather than relying on mock or hard-coded data.

This allows users to see the current state of the pharmacy and quickly identify important issues requiring attention.

---

## 3. Point of Sale (POS)

The POS module is used by the cashier to process customer sales.

When a product is selected, the system obtains the authoritative product price from the server/database rather than trusting a price supplied by the browser.

This is important because the browser should not be trusted to determine the final price of a transaction.

The POS also checks whether the selected medicine requires a prescription before allowing the sale to proceed.

---

## 4. Completing a Sale

When a sale is completed, several related operations have to succeed together.

The system records:

- The sale
- Sale items
- Payment
- Stock movement

These operations are handled within a database transaction.

If an error occurs during the transaction, the changes are rolled back.

Therefore, the system avoids situations where a sale is recorded but stock is not updated, or where stock is reduced without a corresponding completed sale.

This provides consistency between sales, payments, and inventory.

---

## 5. Prescription Guard

The system prevents prescription-only medicines from being sold without the required prescription.

For a prescription medicine, the system performs validation before allowing the sale.

The checks include verifying that:

- A prescription exists
- The prescription belongs to the correct pharmacy
- The prescription has been verified
- The prescription is still valid
- The medicine is listed on the prescription
- The quantity being sold is authorized

If the prescription requirements are not satisfied, the sale is refused.

Importantly, when the sale is refused, the system does not record the sale or perform the corresponding stock movement.

This prevents unauthorized dispensing of prescription-only medicines.

---

## 6. Cash and Till Management

The system supports cash/till management for cashiers.

The expected cash in the drawer is calculated from:

**Opening float + recorded cash sales = Expected drawer cash**

Non-cash payments are not included in the physical cash expected in the drawer.

At closing, the cashier can enter the physical amount of cash counted.

The system can then determine the difference between:

- Expected cash
- Actual physical cash

This makes it possible to identify a cash surplus or shortage.

---

## 7. Inventory Management

The inventory system keeps track of medicines and their batches.

A product batch contains information such as:

- Batch number
- Expiry information
- Quantity

Batch-level tracking is important in a pharmacy because medicines with different expiry dates and batch numbers need to be distinguishable.

It also supports expiry management, recalls, and traceability.

Stock movements are recorded when inventory changes.

---

## 8. Procurement Workflow

The procurement process connects suppliers with inventory.

The general workflow is:

**Supplier → Purchase Order → Purchase Order Items → Receiving → Product Batch → Stock Movement**

When stock is received, the system can create or update the relevant product batch and record the corresponding stock movement.

This provides a traceable relationship between purchased stock and the inventory available for sale.

---

## 9. Clinical Workflow

The system also supports a clinical workflow for pharmacies that provide clinical services.

The workflow is:

**Patient Registration → Vitals/Triage → Doctor → Prescription → Pharmacist Verification → Dispensing**

The doctor can manage patients, record clinical information, and issue prescriptions.

The pharmacist then verifies the prescription before dispensing the medicine.

This separates clinical prescription activities from the dispensing and sales process.

---

## 10. User Roles

The system provides different roles according to the responsibilities of users.

### SuperAdmin

The SuperAdmin operates at the platform level.

Responsibilities include:

- Managing pharmacies
- Pharmacy onboarding
- Platform-wide administration

### Admin

The Admin manages the operations of an individual pharmacy.

Responsibilities include:

- Staff management
- Suppliers
- Reports
- Pharmacy settings

### Pharmacist

The Pharmacist is responsible for pharmacy and dispensing activities.

Responsibilities include:

- Dispensing medicines
- Managing prescriptions
- Managing stock
- Procurement activities

### Doctor

The Doctor handles clinical activities.

Responsibilities include:

- Patient management
- Triage
- Recording vitals
- Creating prescriptions

### Cashier

The Cashier mainly handles sales and till operations.

Responsibilities include:

- Processing POS sales
- Handling payments
- Managing their own till

---

## 11. Multi-Tenancy and Data Isolation

The system is designed to support multiple pharmacies using the same database.

Each pharmacy's data must remain isolated from other pharmacies.

PostgreSQL Row-Level Security (RLS) is used to enforce these data boundaries.

A key security principle is:

> A missing tenant filter should result in no data being returned rather than exposing another pharmacy's data.

This protects pharmacy information from accidental cross-pharmacy access.

---

## 12. Fail-Closed Behaviour

The system follows a fail-closed approach.

The system should not invent information or claim that an operation succeeded when the required database or data source is unavailable.

For example, if an operation cannot be completed successfully, the system should report the failure rather than displaying misleading success information.

This improves the reliability and trustworthiness of the system.

---

## 13. Smart Invoice Scope

The system should not be presented as a ZRA Smart Invoice system.

The system may record a Smart Invoice reference after approved fiscalisation, but it does not generate fake or self-created Smart Invoice references.

Therefore, Smart Invoice functionality should be described only within the scope actually implemented by the system.

---

## 14. Demonstration Flow

A practical demonstration of the system can follow this sequence:

1. Log into the system using a valid user account.
2. Display the dashboard.
3. Navigate to the POS.
4. Select a product.
5. Demonstrate that the product price comes from the server/database.
6. Complete a sale.
7. Show the resulting sale and stock changes.
8. Demonstrate the prescription guard using a prescription-only medicine.
9. Show that an invalid or missing prescription causes the sale to be refused.
10. Demonstrate the cash/till information.
11. Navigate to inventory and show batch information.
12. Demonstrate the procurement flow.
13. Show the clinical workflow from patient registration through prescription and pharmacist verification.

---

## 15. Contribution

My contribution during the Elaboration phase focuses on documenting and explaining the problem and the system in use.

I documented the major operational workflows demonstrated by the system, including:

- Dashboard usage
- POS sales
- Sale completion
- Prescription validation
- Cash/till management
- Inventory and batch tracking
- Procurement
- Clinical workflow
- User roles
- Multi-pharmacy data isolation
- Fail-closed system behaviour

This documentation supports the system walkthrough and provides a clear explanation of how the implemented system addresses the identified pharmacy operational problems.

---

## 16. Key System Principles

The following principles are important when explaining the system:

1. **The server is authoritative for important business data.**
2. **Database transactions maintain consistency during sales.**
3. **Prescription-only medicines require valid authorization.**
4. **Inventory is tracked at batch level.**
5. **Cash and non-cash payments are handled differently for till reconciliation.**
6. **Different users have different responsibilities and permissions.**
7. **Pharmacy data is isolated using multi-tenancy and PostgreSQL RLS.**
8. **The system fails closed instead of inventing or exposing unreliable data.**