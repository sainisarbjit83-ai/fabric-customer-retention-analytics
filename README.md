# fabric-customer-retention-analytics

End-to-end Microsoft Fabric data pipeline and Power BI analytics project for customer retention and revenue insights.



\## Business Objective



The goal of this project is to help a subscription-based software company understand:



\- Customer retention and churn

\- Active and churned subscriptions

\- Revenue and recurring revenue

\- Revenue lost from customer churn

\- Customer and product performance

\- Churn trends across customer segments and acquisition channels



\## Solution Architecture



```text

CSV / Excel / SQL Server

&#x20;         |

&#x20;         v

&#x20;  Fabric Data Pipeline

&#x20;         |

&#x20;         v

&#x20;  Bronze Lakehouse

&#x20;  Raw Source Data

&#x20;         |

&#x20;         v

&#x20;   Silver Lakehouse

&#x20;Data Cleaning \& Quality

&#x20;         |

&#x20;         v

&#x20;   Gold Warehouse

&#x20;  Dimensional Model

&#x20;         |

&#x20;         v

&#x20;Power BI Semantic Model

&#x20;         |

&#x20;         v

&#x20;   Power BI Report

