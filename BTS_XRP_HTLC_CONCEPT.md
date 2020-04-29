![](https://github.com/ioBanker/DOCUMENTS/blob/master/htlc_sections.png)

## Summery:

Atomic Swap Page will connect to both, BitShares and XRP networks using browser based UI client through Public (WebSocket Secure) protocol.


## Requirements:

"XRP Taker" MUST USE BTS USERNAME and SECRET KEY.
"BTS Taker" MUST USE XRP ADDRESS and SECRET KEY.
"XRP taker" will only use (Section #1, #2 and #3) during the complete process of the Atomic Swap to receive XRP.
"BTS taker" will only use (Section #4 and #5) during the complete process of the Atomic Swap to receive BTS.

# The below describes how the "Atomic Swap" page works:

# 1) My Atomic Swap Proposals (Section #1 in screenshot):

The purpose of "My Atomic Swap proposals" section is to broadcast to Public BitShares UI the willing of the "XRP taker" to exchange their BTS into XRP using Atomic Swap Feature; also to feed the necessary information to satisfy the Atomic Swap Exchange user experience.

In this section, "XRP taker" can create an "Atomic Swap proposal" and can view their earlier created proposals.

When "XRP taker" click on the button "CREATE NEW PROPOSAL"; A popup screen will appear asking them to fill the information of their "Atomic Swap proposal".

The Atomic Swap proposal's form will ask the "XRP taker" for the following data to be filled to satisfy the exchange:

A. Amount of BTS. (Condition: <= BTS Available Balance after deducting the active other created proposal's).

B. Desired Amount of XRP. (Condition: Numeric).

C. Proposal Expiry time. (Condition: > 1 hour).

D. My XRP Address. (Condition: It must be a vailed XRP Address).

E. My XRP Tag. (Optional).

F. Upon submission; the form will fetch the data into custom_operation function onChain (BitShares) that saves the provided information including "ASPuniqueid", "ASPbtsusername" and "ASPstatus".

# Conditions

"XRP taker" created "Atomic Swap proposal" will show only their active "Atomic Swap proposal", their expired "Atomic Swap proposal" will be filtered out and they will have the ability to cancel the proposal by clicking on cancel.

The "Atomic Swap proposal" will cost a fraction of BTS and it can be cancelled by the creator "XRP taker" at any time.

# Methods

Custom_operation function must save the following:

(UniqueID,active,order_time,expiration,close_time,bitshares_account,taker_bitshares_account,bitshares_amount,blockchain,blockchain_asset_precision,token_contract,blockchain_account,taker_blockchain_account,tag,blockchain_amount)

# 2) External Lookup for XRP HTLC Match (Section #2 in screenshot):

The purpose of "External Lookup for XRP HTLC Match" section is to notify the "XRP taker" that an XRP HTLC was created offChain that matches their active "Atomic Swap proposal" so they can carry on with submitting the onChain BTS HTLC in a single click.

In this section, "XRP taker" can see a list for incoming offChain created "XRP HTLC" which matches their own active "Atomic Swap Proposals".

"XRP taker" will click on "Create HTLC" then UI will create onChain "BTS HTLC" for the target BTSUSERNAME "BTS taker" which is appearing in the list at XRP MEMO's field.

# Conditions

UI will filter out any incoming "XRP HTLC" that expires before "BTS HTLC" proposed timing for expiry.

# Methods

For checking the incoming "XRP HTLC"; UI will be continuously executing XRP offChain command such as:

{ "id": 5, "command": "account_objects", "account": "MY XRP ACCOUNT ID", "ledger_index": "validated", "type": "escrow" }

Reference: https://xrpl.org/look-up-escrows.html

For creating "BTS HTLC" after clicking on "Create HTLC"; UI will get the (image,BTS.Account) from "XRP HTLC" which was matching the active "My Atomic Swap Proposal" and include these records within the "BTS HTLC" execution command:

htlc_create <from> <to> <amount> <symbol> <hash_algo> <preimage_hash> <preimage_length> <expiration> <broadcast>

Reference: https://github.com/bitshares/bitshares-core/wiki/HTLC

# 3) External Finish Transaction (Section #3 in screenshot):

The purpose of "External Finish Transaction" section is to receive the XRP at "XRP taker" XRP Address offChain; "XRP taker" will finish the transaction in a single click.

In this section, "XRP taker" can see a list for their outgoing onChain Redeemed "BTS HTLC" which contains "PRE-IMAGE" and "offChain transaction ID" that matches their incoming offChain "XRP HTLC".

# Conditions

UI will filter out any outgoing onChain Redeemed "BTS HTLC" doesn't belong to an existing incoming offChain "XRP HTLC".

# Methods

For checking outgoing onChain Redeemed "BTS HTLC"; UI will be continuously executing BTS onChain command such as:

$>list_account_balances My.BTS.Account

Reference: https://github.com/bitshares/bitshares-core/wiki/HTLC

For finishing the "XRP HTLC" offChain by clicking on "Finish HTLC"; UI will execute XRP offChain command such as:

{ "Account": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn", "TransactionType": "EscrowFinish", "Owner": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn", "OfferSequence": 7, "Condition": "A0258020E3B0C44298FC1C149AFBF4C8996FB92427AE41E4649B934CA495991B7852B855810100", "Fulfillment": "A0028000" }

Reference: https://xrpl.org/escrowfinish.html

# 4) Atomic Swap Proposal Active Orders (Section #4 in screenshot):

The purpose of "Atomic Swap Proposal Active Orders" section is to view all the active Atomic Swap proposals onChain and to give the ability for "BTS taker" to create the initial offChain "XRP HTLC" by a single click.

In this section, "BTS taker" will choose his desired amount of BTS from the list and click on create, a popup screen will ask them for a "PRE-IMAGE/SECRET WORD" for the transaction and when they submit, the UI will create a transaction with the information provided within that particular atomic swap active proposal and include the defined "PRE-IMAGE" within the transaction.

# Conditions

UI will show a warning screen says that if the user forgets his defined "PRE-IMAGE", they cannot redeem the BTS transaction once it's made.

# Methods

For Creating the "XRP HTLC" offChain; UI will execute XRP offChain command such as:

{ "Account": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn", "TransactionType": "EscrowCreate", "Memos": [ { "Memo": { "MemoType": "687474703a2f2f6578616d706c652e636f6d2f6d656d6f2f67656e65726963", "MemoData": "BTSUSERNAME,ID" } } ], "Amount": "10000", "Destination": "rsA2LpzuawewSBQXkiju3YQTMzW13pAAdW", "CancelAfter": 533257958, "FinishAfter": 533171558, "Condition": "A0258020E3B0C44298FC1C149AFBF4C8996FB92427AE41E4649B934CA495991B7852B855810100", "DestinationTag": 23480, "SourceTag": 11747 }

Reference: https://xrpl.org/escrowcreate.html

# 5) HTLC Finish Transaction (Section #5 in screenshot):

The purpose of "HTLC Finish Transaction" is to let the "BTS Taker" redeem the "BTS HTLC" after entering their associated "PRE-IMAGE/SECRET WORD" in the text box.

In this section, "BTS taker" will be monitoring the "BTS HTLC" transaction that coming to them to appear, once the transaction appears, they will be able to enter "PRE-IMAGE/SECRET WORD" in the text box then submit; the UI will execute the redeem command onChain.

# Conditions

UI will keep querying the BTS.USERNAME to check for their incoming "BTS HTLC".

# Methods

For redeeming the "BTS HTLC" incoming transaction; UI will execute BTS onChain command such as:

htlc_redeem <htlc_database_id> <fee_paying_account> <preimage> <broadcast>

Reference: https://github.com/bitshares/bitshares-core/wiki/HTLC
