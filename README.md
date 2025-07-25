# Wallet_Risk_Scoring_with_Etherscan_API
# Problem Statement

We are given a list of wallet addresses for which we need to develop a **wallet risk scoring model** that assigns a **score between 0 and 1000** to each wallet. Higher scores indicate reliable and responsible users, while lower scores indicate risky or inactive wallets.

Due to the deprecation of direct Aave subgraph endpoints, this solution uses **Etherscan public blockchain data** to derive wallet features and calculate the risk score based on historical transaction behaviour and current financial exposure.

---

## Data Collection Method

I used the **Etherscan API** to collect wallet-level data. Specifically:

- **Transaction Count (`tx_count`):**  
  Retrieved using the `txlist` endpoint to capture the number of transactions performed by each wallet, representing its activity level.

- **ETH Balance (`eth_balance`):**  
  Retrieved using the `balance` endpoint to get the current holding or exposure of each wallet in ETH.

Both these API calls were made sequentially for each wallet with a delay in between to respect Etherscan’s rate limits.

---

## Feature Selection Rationale

I selected the following two features for risk scoring:

1. **Transaction Count:**  
   Active wallets with higher transaction counts are more likely to be genuine users interacting with DeFi protocols regularly, indicating responsible usage patterns. Very low transaction counts could indicate dormant or bot wallets, which carry higher risk for lending platforms.

2. **ETH Balance:**  
   The ETH balance of a wallet indicates its financial capacity and exposure. Wallets with higher balances have better ability to manage market volatility and reduce default or liquidation risks, making them more reliable.

---

## Scoring Method

1. **Normalisation:**  
   Both features were normalised using **Min-Max Scaling** to bring them between 0 and 1 for comparability.

2. **Weighted Sum Calculation:**  
   A weighted scoring model was implemented with:
   - **60% weight to transaction count**, since activity is a strong indicator of reliability.  
   - **40% weight to ETH balance**, since balance shows financial exposure and capacity.

3. **Final Score Scaling:**  
   The weighted sum was scaled to produce a **risk score between 0 and 1000**, where:
   - **Higher scores** reflect active and financially stable wallets  
   - **Lower scores** reflect inactive, minimal balance, or potentially risky wallets.

---

## Justification of the Risk Indicators Used

Both indicators are widely used in DeFi risk analysis:

- **Transaction Count:** Captures user behaviour, activeness, and real protocol usage, crucial for assessing reliability and engagement.  
- **ETH Balance:** Reflects financial stability and the wallet’s ability to manage market risks or repayments if using lending protocols.

Due to the absence of direct protocol-level transaction data, these two public blockchain features provide a **robust, scalable, and justified scoring approach** for wallet risk profiling in this assignment.

---

## Hurdles Faced & Solutions

This section highlights the **key challenges** faced during this assignment and how I resolved them.

### **1. Deprecation of The Graph Subgraph Endpoints**

**Problem:**  
While implementing the data fetching pipeline, the Aave V2 subgraph endpoint returned a deprecation message, making it unusable for fetching wallet supply and borrow data.

**Solution:**  
I verified endpoint status on The Graph Explorer, tried alternative Messari endpoints, but they were also deprecated. Finally, I shifted to using **Etherscan API** to fetch general blockchain data for wallets.

---

### **2. Flipside Crypto Studio Deprecation**

**Problem:**  
Flipside Crypto Studio was suggested for SQL queries but showed “Query execution disabled - Studio is deprecated.”

**Solution:**  
I attempted using their Velocity and Rocket platforms but faced access restrictions and execution limitations. Due to time constraints, I proceeded with the **Etherscan API approach** for timely assignment completion.

---

### **3. Zero Data for Some Wallets**

**Problem:**  
Some wallets returned zero transactions and zero ETH balance, leading to final risk scores of 0 after normalisation and scoring.

**Solution:**  
I confirmed this was logically valid as such wallets had no activity or balance, hence were riskier. Optionally, a baseline minimum score could be added to avoid exact zeros if required.

---

### **4. Etherscan API Rate Limits**

**Problem:**  
Fetching data for multiple wallets in a loop triggered rate-limit errors due to Etherscan’s 5 requests/sec policy.

**Solution:**  
Implemented `time.sleep(0.2)` between API calls to stay within allowed limits, preventing request failures.

---

### **5. Limited Feature Set Due to Data Constraints**

**Problem:**  
Due to unavailability of protocol-level transaction data (borrow amounts, repayments, liquidations), feature engineering was limited to tx_count and eth_balance.

**Solution:**  
Built a simple weighted heuristic model using these two features, with clear explanation of this limitation and possible extensions in future work.

---

## **Key Learnings**

- Always verify **API endpoint status** before implementation.  
- Maintain **alternative data collection strategies** for critical analysis tasks.  
- Implement **error handling and logging** to debug API and data issues effectively.  
- Document hurdles and solutions to strengthen future project planning and professional communication.

---

## Author

**Name:** Saurabh Verma  
**LinkedIn:** https://www.linkedin.com/in/saurabh-verma-5385155b/

---
> **This file summarises my approach, implementation logic, and problem-solving mindset during this wallet risk scoring assignment.**
