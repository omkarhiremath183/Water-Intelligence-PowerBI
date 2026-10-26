POWER BI CAPSTONE PROJECT: WATER UTILITY OPERATIONS & CUSTOMER SERVICE ANALYTICS
======================================================================================

All data in this project is synthetic and created for learning/interview practice.
It does not represent real customers, organizations, or transactions.

FILES
-----
Customers.csv   One row per customer. CustomerID is unique; RegionID links to Regions.
Regions.csv     One row per region. RegionID is unique.
Invoices.csv    One row per invoice. InvoiceID is unique; CustomerID links to Customers.
Consumption.csv One row per reading. ReadingID is unique; CustomerID links to Customers.
Complaints.csv  One row per complaint. ComplaintID is unique; CustomerID links to Customers.
Date.csv        One row per calendar day from 2024-01-01 through 2026-12-31.

RECOMMENDED MODEL
-----------------
1. Customers[CustomerID] (1) -> Invoices[CustomerID] (*)
2. Customers[CustomerID] (1) -> Consumption[CustomerID] (*)
3. Customers[CustomerID] (1) -> Complaints[CustomerID] (*)
4. Regions[RegionID] (1) -> Customers[RegionID] (*)
5. Date[Date] (1) -> Invoices[BillDate] (*), active
6. Date[Date] (1) -> Consumption[ReadingDate] (*), active
7. Date[Date] (1) -> Complaints[OpenDate] (*), active

Use single-direction filtering from dimensions to facts. Keep the relationships from Date to each fact table separate.
Mark Date as the date table using Date[Date]. Ensure all date fields are typed as Date, not Text.

IMPORTANT DATE NOTE
-------------------
Date.csv includes dates through 2026-12-31. Fact data is generated through September 2026.
Use Date[YearMonth] sorted by Date[Date] or Date[MonthNumber] as appropriate. If you use Month alone across multiple years, it will group the same month together.

SUGGESTED FIRST MEASURES
------------------------
Total Billed = SUM(Invoices[BilledAmount])
Total Paid = SUM(Invoices[PaidAmount])
Outstanding Amount = [Total Billed] - [Total Paid]
Collection Rate = DIVIDE([Total Paid], [Total Billed], 0)
Total Consumption = SUM(Consumption[ConsumptionUnits])
Complaint Count = COUNTROWS(Complaints)
Closed Complaints = CALCULATE([Complaint Count], Complaints[Status] = "Closed")

DATA QUALITY EXERCISES
----------------------
- Check that CustomerID and RegionID are unique in their dimension tables.
- Find duplicate InvoiceID, ComplaintID, and ReadingID values.
- Reconcile invoice totals in Power BI against a source aggregation.
- Test what happens when relationships are inactive or filtering direction is changed.
- Build a monthly trend using Date[YearMonth] and sort it chronologically.
- Create a measure that counts overdue unpaid/partially paid invoices as of a selected reporting date.
- Define and validate SLA compliance using an explicit business threshold by complaint priority/category.

KNOWN MODELLING CONSIDERATIONS
------------------------------
- The Date table has one active relationship to each fact table in the recommended model.
- If you need to analyze complaints by CloseDate instead of OpenDate, create an inactive relationship from Date[Date] to Complaints[CloseDate] and use USERELATIONSHIP in the relevant measure.
- RLS practice: create a separate UserRegion mapping table with user principal names and RegionID, then configure and test dynamic RLS. The supplied Manager names are fictional and are not login identities.
- Do not connect fact tables directly to one another.
