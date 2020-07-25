1.SELECT
COUNT (u_id)
FROM
users;

2. SELECT * FROM transfers;
SELECT
COUNT(u_id)
FROM transfers
WHERE send_amount_currency='CFA';

3.SELECT * FROM agent_transactions;
SELECT
COUNT (atx_id)
FROM agent_transactions
GROUP BY
when_created;

4.SELECT
COUNT (atx_id)
FROM agent_transactions
WHERE EXTRACT(YEAR FROM when_created)=2018
GROUP BY EXTRACT(MONTH FROM when_created)

5.WITH agentwithdrawers AS
(SELECT COUNT(agent_id) AS netwithdrawers FROM agent_transactions
HAVING COUNT(amount) IN (SELECT COUNT(amount) FROM agent_transactions WHERE amount > -1
AND amount !=0 HAVING COUNT (amount)>(SELECT COUNT(amount)
FROM agent_transactions WHERE amount < 1 AND amount !=0)))
SELECT netwithdrawers FROM agentwithdrawers

6.SELECT agents.city,
COUNT (amount)
FROM agent_transactions
INNER JOIN agents
ON agents.agent_id = agent_transactions.agent_id
WHERE agent_transactions.when_created > current_date - interval'7 days'
GROUP BY agents.city;

7.SELECT count(atx.amount) AS "atx volume", count (ag.city) AS "city", count (ag.country) AS "Country"
FROM agent_transactions AS atx INNER JOIN agents AS ag ON
atx.atx_id = ag.agent_id
GROUP BY ag.country

8.SELECT w.ledger_location as "Country", tn.send_amount_currency AS "Kind",
sum (tn.send_amount_scalar) AS "Volume"
FROM transfers AS tn INNER JOIN wallets AS w ON tn.transfer_id =w.wallet_id
GROUP BY w.ledger_location, tn.send_amount_currency

9.SELECT COUNT(transfers.source_wallet_id) 
AS Unique_Senders, 
COUNT (transfer_id) 
AS Transaction_Count, transfers.kind 
AS Transfer_Kind, wallets.ledger_location 
AS Country, 
SUM (transfers.send_amount_scalar) 
AS Volume 
FROM transfers 
INNER JOIN wallets 
ON transfers.source_wallet_id = wallets.wallet_id 
WHERE (transfers.when_created > (NOW() - INTERVAL '1 week')) 
GROUP BY wallets.ledger_location, transfers.kind;

10.SELECT tn.send_amount_scalar, tn.source_wallet_id, w.wallet_id
FROM transfers AS tn INNER JOIN wallets AS w ON tn.transfer_id =w.wallet_id
WHERE tn.send_amount_scalar > 10000000
AND (tn.send_amount_currency = 'CFA' AND tn.when_created > CURRENT_DATE-INTERVAL '1 month')