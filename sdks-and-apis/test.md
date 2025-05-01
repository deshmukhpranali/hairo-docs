# Crypto Create API
## Overview
This API allows you to create a new Hedera account.  The API takes a `CryptoCreateTransactionBody` message as input and returns a `TransactionResponse` upon successful submission to the network.  Creating an account requires an initial HBAR balance deposit, which will be deducted from the payer account.  Several options are available for configuring the new account, including key management, auto-renewal, staking, and alias settings.
## Request
### CryptoCreateTransactionBody
```protobuf
message CryptoCreateTransactionBody {
    // Removed prior to oldest available history
    reserved 4,5;
    Key key = 1;
    uint64 initialBalance = 2;
    AccountID proxyAccountID = 3 [deprecated = true];
    uint64 sendRecordThreshold = 6 [deprecated = true];
    uint64 receiveRecordThreshold = 7 [deprecated = true];
    bool receiverSigRequired = 8;
    Duration autoRenewPeriod = 9;
    ShardID shardID = 10;
    RealmID realmID = 11;
    Key newRealmAdminKey = 12 [deprecated = true];
    string memo = 13;
    int32 max_automatic_token_associations = 14;
    oneof staked_id {
        AccountID staked_account_id = 15;
        int64 staked_node_id = 16;
    }
    bool decline_reward = 17;
    bytes alias = 18;
}
```
| Field | Type | Description |
|---|---|---|
| `key` | `Key` | **REQUIRED.** The account's key. This key represents the account owner and is required for most actions. Must contain at least one primitive (cryptographic) key. |
| `initialBalance` | `uint64` | The initial HBAR balance of the new account in tinybar. This amount will be deducted from the payer account. |
| `proxyAccountID` | `AccountID` | **DEPRECATED.** Use `staked_id` instead. |
| `sendRecordThreshold` | `uint64` | **DEPRECATED.**  Removed prior to the first available history. |
| `receiveRecordThreshold` | `uint64` | **DEPRECATED.** Removed prior to the first available history. |
| `receiverSigRequired` | `bool` | If true, the account holder must authorize all incoming token transfers. |
| `autoRenewPeriod` | `Duration` | The auto-renew period for the account.  Defines the interval between automatic renewal attempts. |
| `shardID` | `ShardID` | The shard in which the account is created. Currently, this MUST be `0`. |
| `realmID` | `RealmID` | The realm in which the account is created. Currently, this MUST be `0`. |
| `newRealmAdminKey` | `Key` | **DEPRECATED.** This field was never used and is not expected to be used in the future. |
| `memo` | `string` | A short description of the account (maximum 100 UTF-8 bytes). |
| `max_automatic_token_associations` | `int32` | Maximum number of tokens that can be auto-associated with this account. `0` requires manual association, `-1` indicates no limit. |
| `staked_account_id` | `AccountID` | ID of the account to which this account is staking its balances.  Use `0.0.0` if not staking. |
| `staked_node_id` | `int64` | ID of the node this account is staked to. Use `-1` if not staking. |
| `decline_reward` | `bool` | If true, the account declines staking rewards. |
| `alias` | `bytes` | The account's alias.  Must be a valid ED25519, compressed ECDSA(secp256k1), or 20-byte EVM address. |
## Response
The response is a standard `TransactionResponse` message, indicating the success or failure of the transaction submission.  It does not contain the newly created account ID directly. You can obtain the new account ID from the record of the transaction.
## Enums and Constants
This API doesn't define any specific enums or constants within the provided Protobuf definition. It relies on types like `Key`, `AccountID`, `Duration`, `ShardID`, and `RealmID` which are defined in other Protobuf files (`basic_types.proto` and `duration.proto`).
## Example Usage (Conceptual)
```java
// Create a new Key
Key newAccountKey = ...;
// Create a new CryptoCreateTransactionBody
CryptoCreateTransactionBody createTxnBody = CryptoCreateTransactionBody.newBuilder()
        .setKey(newAccountKey)
        .setInitialBalance(1000) // 1000 tinybar
        .setAutoRenewPeriod(Duration.newBuilder().setSeconds(7890000).build()) // 90 days
        .build();
// Build the transaction, sign it with the payer key, and submit to the network
TransactionResponse response = ...;
// Check the response status
if (response.getNodeTransactionPrecheckCode() == ResponseCodeEnum.OK) {
    // Transaction submitted successfully
} else {
    // Handle error
}
```
This example demonstrates the basic steps involved in creating a new account.  Actual implementation details will vary depending on the client library used.  Remember to consult the specific client library documentation for complete examples and best practices.
