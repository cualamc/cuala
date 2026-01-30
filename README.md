# RevOpsDashboard_Subscription
**Overview**

A Power BI dashboard using designed to bridge the gap between Marketing activity (#HubSpot), Sales pipeline (#Salesforce), and Financial realization (#Stripe). This project demonstrates a "Lead-to-Cash" narrative, focusing on executive-level clarity, data integrity across multiple platforms, and actionable team-level insights.

**Data Process**
- [Source Files.zip](https://github.com/user-attachments/files/24968936/Source.Files.zip) were generated through ChatGPT.
- Under stripe_subscriptions using Power Query (M Language), the unit_price was normalized to a monthly rate (monthlymrr) and and effective_end_date was calculated based on the status of the subscription.

<img width="443" height="424" alt="image" src="https://github.com/user-attachments/assets/43793a32-9f41-497d-81a5-85c0e0a81f31" />
<img width="371" height="335" alt="image" src="https://github.com/user-attachments/assets/a30b997a-69ac-43df-b3d3-2f0951cba64b" />

**Dashboard Overview**
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
