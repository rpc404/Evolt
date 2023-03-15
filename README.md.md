**Tech Team of Evolution**
- Andrei Serban
- Nicolae

The OperatorId: 10712001 was created on our Integration environment. 

### Please provide us with:
  1. Callback URLs for all four methods: **authentication, debit, credit, and rollback**.
  2. Link to your website or a valid authentication token.
  3. Two test accounts with money for tests.

Lobby URL:  https://playint.tableslive.com/auth/ 
Back Office URL:  https://boint.tableslive.com 

### **IP addresses that need to be whitelisted**:
- 52.16.138.24 - Not Responding
- 52.16.33.81 - Not Responsing
- 52.16.124.91 - Not Responding
- 82.76.44.56 - Responding
![[Pasted image 20230315131538.png]]

- 178.16.20.237 - Not Responding

- 109.97.118.250 - Responding
![[Pasted image 20230315131631.png]]

- 109.102.212.194 - Responding
![[Pasted image 20230315131709.png]]

- 145.239.222.15 - Not Responding

**Currencies**: NPR / INR / PKR
**Back Office user/pass**: Playpoint / A12345678! - **Unauthorized Credentials**

The Security Hash Key will be provided after you will provide us with the callback URLs.

### Link for manual testing tool:
https://boint.tableslive.com/office.php?action=maintenance&sub_act=integration_jsons&operator=10712001  

**Below you can find a file with test cases.**
Downloaded Excel File: 

### Please find below the security measures you should implement on your end:
• **SSL Communication**: All the communication with the operator should be done over SSL secured channel. Mandatory
• **IP Whitelisting**: An operator must whitelist the Ezugi server IPs on his servers and should receive any Ezugi related requests only from the registered whitelisted Ips. Any requests related to Ezugi that are not being submitted from the registered Ezugi IPs should be discarded and Ezugi should be notified. Mandatory 
• **Hash Signature Check**: An operator will receive on all requests coming from Ezugi a hash signature signed by a secret key that will be issued by Ezugi specifically for the operator. The hash signature will be created by using SHA256 hash algorithm and will create a signature by using a secret key and the request that is sent to the operator. An output format is base64 encoding. The operator should compare the received hash signature with his own signature that will be create by using the secret key and the received request. Mandatory. The latest version of API is attached. 
• **New Token Creation on Authentication**: On each authentication for the same player, our system will have to receive a newly generated token. Token reuse will not be accepted. Please review your current implementation 
• **Token Format**: A recommended token format for all our operators is UUID. A token should have at least 20 symbols which should be a mix of digits and characters. Please review your current implementation 
• **Token Expiration**: Any token (initial authentication or financial) issued by an operator should have an expiration date and should expire if it will not be used after a short period of time. Initial authentication tokens should expire after 30-60 seconds while financial tokens, if not used, it should expire after 30-40 minutes. Please review your current implementation 
• **Debit-Credit correlation**: Each credit transaction has a key named debitTransactionId. You should validate that each credit has the corresponding debit. Each debit could be related only to one credit. This function could be used for round closure. Mandatory.
• **Round Financial Requests Counting**: On each game round, the number of debit requests equals the number of credit requests. If an operator receives several credit requests that is bigger than the number debit requests carrying the same game id, table id, round id, and seat id (Blackjack only) the credit requests should be discarded and Ezugi should be notified. Please review your current implementation
In addition, we completed push balance API new implementation. If this functionality can be relevant for you, we will assist you to implement and test it. Push balance API and help doc hash signature check are attached.

[[Live Casino API v3.1.8.pdf]]
[[Push Balance 2.0.2.pdf]]
[[Third Party API v2.0.pdf]]
[[Debit - Credit Correlation v.2.pdf]]
[[Hash Signature Check v.1.2.pdf]]

The player’s balance is shown in our system based on the Timestamp. The balance with the biggest (latest) timestamp will be shown. That's why it's important to set a correct timestamp to each transaction. The most critical is the last balance in a round, which always must be the one with the biggest timestamp. If the last balance does not have the correct timestamp, then the player may see invalid balance after the round will end or even will be prevented from further play due to insufficient funds, despite he won.

You may not change timestamp if you are not changing balance, but you have always to change timestamp if balance was updated.
Set the timestamp when you completed to process the response. In this case last balance will always have the biggest TS.
Example:
You have 3 credit transactions.
15$, 5$ and 10$. Client's balance before credits - 100$

You may send in this order:
Credit 15, Balance 115, TS 1
Credit 5, Balance 120, TS 2
Credit 10, Balance 130 TS 3
Or in this order:
Credit 10, Balance 110, TS 1
Credit 5, Balance 115, TS 2
Credit 15, Balance 130 TS 3
Or in this order:
Credit 10, Balance 110, TS 1
Credit 15, Balance 125, TS 2
Credit 5, Balance 130 TS 3
Or even like this:
Credit 10, Balance 110, TS 1
Credit 15, Balance 110, TS 1
Credit 5, Balance 130 TS 2

The order does not matter, but, as you see, the correct balance has the biggest TS every time.

### Please, find below logic for timestamp usage.
**Valid logic**:
Bn>Bn+1 if Tn ≥Tn+1
Bn<Bn+1 if Tn ≤Tn+1
Bn=Bn+1 if Tn = Tn+1

**Invalid logic**:
Bn<Bn+1 if Tn ≥Tn+1
Bn>Bn+1 if Tn≤Tn+1
Bn<>Bn+1 if Tn = Tn+1

When:
-	Bn is a balance, which get to our system with a timestamp Tn.
-	Bn+1 is a balance, which get to our system with a timestamp Tn+1.
-	Relevant for all debit or all credit calls which are sent for the same player in multiple action round as BJ.
---
Please note that our integration uses POST Methods and only accepts HTTP status code 200, as we have our own set of errors (See Live Casino API documentation).

Also please note that to consider the integration finished, we will pass through a series of tests consisted in the following:

1. **Financial test** – a series of test cases which are meant to strengthen the logic and security of the operator’s system.

2. **Player Stress test** – a Blackjack run that mimics the perfect round, including the maximum number of transactions possible, to ensure that the operator can handle multiple simultaneous transactions coming from the same user.

3. **Player Stress test** – which is like the test above, but this one ensures that the operator can handle multiple transactions from multiple users.

4. **All Games test** – This is a run through all the games available on the integration environment (1 from each GameId) to ensure that all are accepted by the operator’s system and all transactions are processed properly.

This set of four runs (plus few additional tests) are mandatory to be ran and passed by the operator before reaching the live environment. 