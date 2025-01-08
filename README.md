# Chat App

## Technical Requirements

1. WebSocket API
   • Use AWS API Gateway to handle WebSocket routes:
   • $connect: Authenticate and establish a connection.
   • $disconnect: Clean up user session data.
   • sendMessage: Send a message to a group or individual.
   • broadcastMessage: Deliver a message to all members of a group.
   • typingIndicator: Broadcast typing events to relevant users.

2. Backend Logic (AWS Lambda)
   • Extend Lambda functions:
   • $connect: Authenticate users, store connection and session details in DynamoDB.
   • $disconnect: Remove connection/session data from DynamoDB.
   • sendMessage:
   • Validate message payload.
   • Write the message to DynamoDB.
   • Notify recipients via postToConnection.
   • typingIndicator:
   • Validate the typing event payload.
   • Broadcast the event to other users in the group.
   • handleFileUpload (new):
   • Generate a signed URL for uploading files to an S3 bucket.
   • Notify the chat group about the uploaded file.

3. Data Storage
   • DynamoDB:
   • Connection Table:
   • Primary Key: ConnectionId.
   • Attributes: UserId, ChatRoomId, LastConnectedAt.
   • Messages Table:
   • Primary Key: ChatRoomId (Partition Key), MessageId (Sort Key).
   • Attributes: SenderId, Content, Timestamp, FileUrl (optional).
   • Chat Rooms Table:
   • Primary Key: ChatRoomId.
   • Attributes: RoomName, Members (list of UserIds).
   • S3 Bucket for File Storage:
   • Store uploaded files.
   • Use signed URLs for secure access.

4. Typing Indicators
   • Use WebSocket messages for real-time typing notifications:
   • Payload: { "action": "typingIndicator", "userId": "123", "chatRoomId": "456" }.
   • Notify all members in the chatRoomId except the sender.

5. File Uploads
   • Process:

   1. User requests a signed URL via WebSocket.
   2. Backend generates and returns the URL.
   3. User uploads the file directly to S3 using the signed URL.
   4. Backend sends a WebSocket message to notify users in the chat room about the new file.

6. Group Chats
   • Users join and leave chat rooms dynamically.
   • Each message includes chatRoomId to ensure messages are routed correctly.
   • Notify all members in the room of new messages or typing indicators.

7. Infrastructure as Code (Terraform)
   • Add resources for:
   • S3 bucket with appropriate permissions for signed URLs.
   • Updated DynamoDB tables for chat rooms and file storage metadata.

8. CI/CD Pipeline
   • Add integration tests for:
   • Typing indicators.
   • File upload workflow.
   • Group chat functionality.

Would you like help designing the architecture or API payloads for these new features?
