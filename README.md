**ERP Order Sync via REST Endpoint and Platform Events**

Asynchronous Salesforce integration that receives order status updates from an ERP system, publishes a Platform Event, and processes orders in the background.

**Problem**

ERP sends order updates through HTTP POST requests. Salesforce must respond immediately without performing heavy processing in the REST endpoint.

**Payload**

- orderId
- status
- lineItems (array)
- timestamp

**Processing**

1. Receive order update via REST API
2. Deserialize JSON payload
3. Publish **OrderSync__e** Platform Event
4. Return **HTTP 202 Accepted**
5. Process event asynchronously
6. Update matching Order and Order Items
7. If no Order is found, create a **SyncError__c** record

**Components**

- **ERPOrderSyncResource** – Apex REST endpoint
- **OrderSync__e** – Platform Event
- **OrderSyncTrigger** – Platform Event Trigger
- **OrderSyncTriggerHandler** – Business logic handler
- **SyncError__c** – Error logging object
- **ERPOrderSyncTest** – Test class

**Success Path**

- Order found by External Order ID
- Update Order Status
- Update related Order Items

**Failure Path**

- No matching Order found
- Create **SyncError__c** record with payload, error message, timestamp, and retry count
