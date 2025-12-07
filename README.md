# Chat Application Backend

A microservices-based backend for a real-time chat application built with Node.js, Express, TypeScript, MongoDB, and Socket.IO.

## Architecture

The backend is divided into three independent microservices:

### 1. **User Service** (Port: 5000)
Handles user authentication, profile management, and user data.

**Key Features:**
- User registration and login with OTP verification
- JWT-based authentication
- User profile management
- Redis-based OTP caching
- RabbitMQ integration for async email sending

**Technologies:**
- Express.js
- MongoDB
- Redis
- RabbitMQ
- JWT

### 2. **Chat Service** (Port: 5002)
Manages chat operations, messaging, and real-time communication.

**Key Features:**
- Create and manage chat conversations
- Send text and image messages
- Real-time messaging with Socket.IO
- Message seen status tracking
- Online user tracking
- Typing indicators
- Image upload to Cloudinary

**Technologies:**
- Express.js
- MongoDB
- Socket.IO
- Cloudinary
- Multer

### 3. **Mail Service** (Port: 5001)
Handles email sending for OTP delivery.

**Key Features:**
- OTP email delivery
- RabbitMQ consumer for async email processing
- Nodemailer integration with Gmail SMTP

**Technologies:**
- Express.js
- RabbitMQ
- Nodemailer

## Prerequisites

- Node.js (v16 or higher)
- MongoDB
- Redis
- RabbitMQ
- Cloudinary account (for image uploads)
- Gmail account (for email service)

## Installation

### 1. Clone the repository
```bash
git clone <repository-url>
cd backend
```

### 2. Install dependencies for each service

```bash
# User Service
cd user
npm install

# Chat Service
cd ../chat
npm install

# Mail Service
cd ../mail
npm install
```

## Environment Setup

Create a `.env` file in each service directory with the following variables:

### User Service (`.env`)
```env
PORT=5000
MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/
JWT_SECRET=your_jwt_secret_key
REDIS_URL=redis://localhost:6379
Rabbitmq_Host=localhost
Rabbitmq_Username=guest
Rabbitmq_Password=guest
CLIENT_ORIGINS=http://localhost:3000
```

### Chat Service (`.env`)
```env
PORT=5002
MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/
JWT_SECRET=your_jwt_secret_key
USER_SERVICE=http://localhost:5000
Cloud_Name=your_cloudinary_name
Api_Key=your_cloudinary_api_key
Api_Secret=your_cloudinary_api_secret
CLIENT_ORIGINS=http://localhost:3000
```

### Mail Service (`.env`)
```env
PORT=5001
Rabbitmq_Host=localhost
Rabbitmq_Username=guest
Rabbitmq_Password=guest
EMAIL_USER=your_gmail@gmail.com
EMAIL_PASSWORD=your_app_specific_password
```

## Running the Services

### Development Mode

```bash
# User Service
cd user
npm run dev

# Chat Service (in another terminal)
cd chat
npm run dev

# Mail Service (in another terminal)
cd mail
npm run dev
```

### Production Mode

```bash
# Build all services
cd user && npm run build
cd ../chat && npm run build
cd ../mail && npm run build

# Start services
cd user && npm start
cd ../chat && npm start
cd ../mail && npm start
```

## API Endpoints

### User Service

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| POST | `/api/v1/login` | Send OTP to email | No |
| POST | `/api/v1/verify` | Verify OTP | No |
| GET | `/api/v1/me` | Get current user profile | Yes |
| GET | `/api/v1/user/all` | Get all users | Yes |
| GET | `/api/v1/user/:id` | Get user by ID | No |
| POST | `/api/v1/update/user` | Update user profile | Yes |

### Chat Service

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| POST | `/api/v1/chat/new` | Create new chat | Yes |
| GET | `/api/v1/chat/all` | Get all chats | Yes |
| POST | `/api/v1/message` | Send message | Yes |
| GET | `/api/v1/message/:chatId` | Get messages by chat | Yes |

## Socket Events

### Client → Server
- `typing` - User typing indicator
- `stopTyping` - User stopped typing
- `joinChat` - Join a chat room
- `leaveChat` - Leave a chat room

### Server → Client
- `newMessage` - New message received
- `messagesSeen` - Messages marked as seen
- `userTyping` - User is typing
- `userStoppedTyping` - User stopped typing
- `getOnlineUser` - List of online users

## Database Models

### User Model
```typescript
{
  name: String,
  email: String (unique),
  createdAt: Date,
  updatedAt: Date
}
```

### Chat Model
```typescript
{
  users: [String],
  latestMessage: {
    text: String,
    sender: String
  },
  createdAt: Date,
  updatedAt: Date
}
```

### Message Model
```typescript
{
  chatId: ObjectId,
  sender: String,
  text: String,
  image: {
    url: String,
    publicId: String
  },
  messageType: "text" | "image",
  seen: Boolean,
  seenAt: Date,
  createdAt: Date,
  updatedAt: Date
}
```

## Project Structure

```
backend/
├── user/
│   ├── src/
│   │   ├── config/
│   │   ├── controllers/
│   │   ├── middleware/
│   │   ├── model/
│   │   ├── routes/
│   │   └── index.ts
│   ├── package.json
│   └── tsconfig.json
├── chat/
│   ├── src/
│   │   ├── config/
│   │   ├── controllers/
│   │   ├── middlewares/
│   │   ├── models/
│   │   ├── routes/
│   │   └── index.ts
│   ├── package.json
│   └── tsconfig.json
└── mail/
    ├── src/
    │   ├── consumer.ts
    │   └── index.ts
    ├── package.json
    └── tsconfig.json
```

## Technology Stack

- **Runtime:** Node.js
- **Language:** TypeScript
- **Framework:** Express.js
- **Database:** MongoDB
- **Cache:** Redis
- **Message Queue:** RabbitMQ
- **Real-time:** Socket.IO
- **Authentication:** JWT
- **Email:** Nodemailer
- **File Storage:** Cloudinary
- **File Upload:** Multer

## License

ISC
