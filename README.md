# RevOpsDashboard_Subscription
<img width="1429" height="797" alt="image" src="https://github.com/user-attachments/assets/719aff8d-e5ae-46bd-8ab8-ef292e2fa920" />
A Power BI dashboard using designed to bridge the gap between Marketing activity (#HubSpot), Sales pipeline (#Salesforce), and Financial realization (#Stripe). This project demonstrates a "Lead-to-Cash" narrative, focusing on executive-level clarity, data integrity across multiple platforms, and actionable team-level insights.

## **Data Process**
- [Source Files.zip](https://github.com/user-attachments/files/24968936/Source.Files.zip) were generated through ChatGPT.
- Under stripe_subscriptions using Power Query (M Language), the unit_price was normalized to a monthly rate (monthlymrr) and and effective_end_date was calculated based on the status of the subscription.

<img width="443" height="424" alt="image" src="https://github.com/user-attachments/assets/43793a32-9f41-497d-81a5-85c0e0a81f31" />
<img width="371" height="335" alt="image" src="https://github.com/user-attachments/assets/a30b997a-69ac-43df-b3d3-2f0951cba64b" />

## **Dashboard Overview**
<img width="1429" height="797" alt="image" src="https://github.com/user-attachments/assets/719aff8d-e5ae-46bd-8ab8-ef292e2fa920" />

**Financial Performance (Stripe Data)**
- Total MRR ($148.64K): The current Monthly Recurring Revenue, showing a steady growth trend from $16K in early 2025 to a peak of $149K in early 2026.
- Average Revenue per User (ARPU) ($2.91K): A key efficiency metric indicating high-value customers.
- Cross-Sell Ratio (1.7x): This highlights that, on average, customers are subscribed to nearly two different products or services.
- Revenue Mix: The Strategic Business Unit (SBU) Retainer is the primary revenue driver, accounting for 47.73% ($80.48K) of the total MRR.

**Sales & Marketing Funnel (HubSpot & Salesforce Data)**
- Marketing Qualified Leads (7): The number of high-intent prospects delivered by marketing in the current period.
- New Pipeline Created ($88.02K): The total dollar value of new opportunities entered into the sales cycle (Salesforce).
- Win Rate (40%): A strong performance indicator showing that the sales team successfully closes 4 out of every 10 qualified opportunities.

**Predictive Forecasting**
- Projected Revenue ($35.21K): Using the 40% historical win rate applied against the $88.02K in new pipeline, the dashboard forecasts approximately $35K in upcoming realized revenue.
- Visual Gauge: The gauge shows the business is currently pacing toward the lower end of the $50K–$60K target, signaling a potential need for increased lead volume or higher deal values to hit the upper goal.

## **Dashboard Process Details**

### Financial Performance KPIs

<img width="340.5" height="340.5" alt="image" src="https://github.com/user-attachments/assets/3348fce9-d8d6-4118-99bf-6b425f501201" />
<img width="317" height="153.5" alt="image" src="https://github.com/user-attachments/assets/d0921154-7a74-42b8-9848-549375252360" />

**For Total MRR:**

- a calendar table was created to match subscription end dates of MRR (this calendar table is also used as the universal "date" reference for the rest of the dashboard)
- By using a CROSSJOIN and date filters, it determines which subscriptions were "live" during a given month's window. This shows a table that allows you to report on active MRR and subscriber counts for any point in the past
```dax
mrr_snapshot = 
FILTER(
    ADDCOLUMNS(
        CROSSJOIN(
            stripe_subscriptions, DISTINCT('Calendar'[Month])),
        "MonthStart",LOOKUPVALUE('Calendar'[MonthStart],'Calendar'[Month],'Calendar'[Month]),
        "MonthEnd", LOOKUPVALUE('Calendar'[MonthEnd],'Calendar'[Month],'Calendar'[Month])
    ),
stripe_subscriptions[subscription_start_date]<=[MonthEnd]
&& stripe_subscriptions[effective_end_date]>=[MonthStart]
&&stripe_subscriptions[subscription_status]<>"canceled"
)
```
- For here a dax measure was created [totalmrr] which summarized [mrr_snapshot]
- this measure was used in the Total MRR by Month Bar Chart and Total MRR Card
- the Total MRR by Plan Availed Bar Chart was subsequently created using the sum of 'stripe_subscriptions'[monthlymrr] and 'stripe_subscriptions'[plan_name]

**For Total Active Subscription Count:**
- a card visual using Count(Distinct) of 'stripe_subscriptions'[stripe_subscription_id] filtered by "Active" 'stripe_subscriptions'[subscription_status]

**For Average Revenue per Subscription(ARPS), Average Revenue per User(ARPU) and Cross-Sell Ratio:**

```dax
arps = [totalmrr]/DISTINCTCOUNT(stripe_subscriptions[stripe_subscription_id])

arpu = [totalmrr]/DISTINCTCOUNT(stripe_subscriptions[stripe_customer_id])

crosssellratio = 
VAR totalsubs = count('stripe_subscriptions'[stripe_subscription_id])
VAR uniqueusers = DISTINCTCOUNT(stripe_subscriptions[stripe_customer_id])
VAR ratio = DIVIDE(totalsubs,uniqueusers,0)
Return
"Cross-Sell Ratio: "&FORMAT(ratio,"0.0") & "x"
```

### Sales and Marketing KPIs

<img width="343" height="73.5" alt="image" src="https://github.com/user-attachments/assets/cf145d5e-465b-44e7-aa4d-d0d9e9c293e3" />

**For Marketing Qualified Leads:**
```dax
MQL Count = 
CALCULATE(
    COUNT('hubspot_contacts'[hubspot_contact_id]),
    NOT(ISBLANK('hubspot_contacts'[became_mql_date])),
    USERELATIONSHIP('Calendar'[Date], 'hubspot_contacts'[became_mql_date])
)
```

**For New Pipeline Created:**
- sum of amount from salesforce_opportunities.csv 

**For WinRate%:**
```dax
_Win Rate %_
winrate = DIVIDE(
    CALCULATE(COUNT(salesforce_opportunities[sf_opportunity_id]),salesforce_opportunities[is_won]=true),
    CALCULATE(COUNT(salesforce_opportunities[sf_opportunity_id]),salesforce_opportunities[is_closed]=true),
 0)
```
### Projected Revenue

<img width="341.5" height="108.5" alt="image" src="https://github.com/user-attachments/assets/6707a332-75cf-4ebf-829a-8cc819bf652d" />

```dax
Projected Revenue = 
VAR currentpipeline = SUM('salesforce_opportunities'[amount]) 
VAR globalwinrate = [winrate] 
RETURN 
    currentpipeline * globalwinrate
```

### Date Tags

<img width="1306" height="42" alt="image" src="https://github.com/user-attachments/assets/5e083d33-49a3-4d7e-8d99-5d62bef107cc" />

```dax
Dashboard Header = 
VAR MinDate = MIN('Calendar'[Date])
VAR MaxDate = MAX('Calendar'[Date])
RETURN
IF(
    ISFILTERED('Calendar'[Date]),
    "Executive Revenue Summary | " & FORMAT(MinDate, "MMM dd, yyyy") & " to " & FORMAT(MaxDate, "MMM dd, yyyy"),
    "Executive Revenue Summary | Showing All Historical Data")
```  

<img width="240" height="23" alt="image" src="https://github.com/user-attachments/assets/7c4fee7e-b966-4905-b2f4-abe88d467898" />

 - a new table was created with one entry: 1/1/2026
 - a new column in the table was added to reflect NYC time zone (GMT-5) instead of (GMT+8) had this column not been added.
 - the first column was removed (we only needed the GMT-5 time zone)

```dax
Last Update Label = 
"Last Updated: " & FORMAT(MAX('lastupdate'[CurrentTime]), "MMM dd, hh:mm tt")
```
