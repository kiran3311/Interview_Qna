# Fullstack Developer Interview Questions & Answers

## Frontend Technologies

### React.js

#### Basic Level Questions

**Q1: What is React and what are its key features?**

**Answer:**
React is an open-source JavaScript library developed by Facebook for building user interfaces, particularly single-page applications. It follows a component-based architecture.

**Key Features:**
- **Component-Based Architecture**: Build encapsulated components that manage their own state
- **Virtual DOM**: Creates a virtual representation of the real DOM for efficient updates
- **JSX**: JavaScript syntax extension that allows writing HTML-like code in JavaScript
- **One-way Data Binding**: Data flows down from parent to child components
- **Declarative**: Describe what the UI should look like for any given state

**Example:**
```jsx
// Simple React component
function Welcome({ name }) {
  return <h1>Hello, {name}!</h1>;
}

// Usage
<Welcome name="John" />
```

---

**Q2: Explain the difference between functional and class components.**

**Answer:**

**Class Components:**
- ES6 classes that extend React.Component
- Have lifecycle methods
- Use `this.state` for state management
- More verbose syntax

**Functional Components:**
- Simple JavaScript functions that return JSX
- Use React Hooks for state and lifecycle management
- Cleaner, more concise syntax
- Better performance (no class instantiation overhead)

**Examples:**

```jsx
// Class Component
class ClassComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }
  
  componentDidMount() {
    console.log('Component mounted');
  }
  
  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Increment
        </button>
      </div>
    );
  }
}

// Functional Component (Modern approach)
import { useState, useEffect } from 'react';

function FunctionalComponent() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    console.log('Component mounted');
  }, []);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}
```

---

**Q3: What are props and how do you pass data between components?**

**Answer:**
Props (properties) are read-only data passed from parent components to child components. They enable data flow and component communication.

**Key Points:**
- Props are immutable within the receiving component
- Data flows one-way (parent to child)
- Can pass any data type: strings, numbers, objects, functions, etc.

**Example:**
```jsx
// Parent Component
function App() {
  const user = {
    name: 'John Doe',
    email: 'john@example.com',
    age: 30
  };

  const handleClick = () => {
    alert('Button clicked!');
  };

  return (
    <div>
      <UserProfile 
        user={user}
        isActive={true}
        onButtonClick={handleClick}
      />
    </div>
  );
}

// Child Component
function UserProfile({ user, isActive, onButtonClick }) {
  return (
    <div className={isActive ? 'active' : 'inactive'}>
      <h2>{user.name}</h2>
      <p>Email: {user.email}</p>
      <p>Age: {user.age}</p>
      <button onClick={onButtonClick}>
        Click Me
      </button>
    </div>
  );
}

// Props destructuring with default values
function Button({ text = 'Click', variant = 'primary', onClick }) {
  return (
    <button 
      className={`btn btn-${variant}`}
      onClick={onClick}
    >
      {text}
    </button>
  );
}
```

#### Medium Level Questions

**Q4: Explain React component lifecycle methods and their equivalents in functional components.**

**Answer:**

**Class Component Lifecycle Methods:**

1. **Mounting Phase:**
   - `constructor()`: Initialize state and bind methods
   - `componentDidMount()`: Execute after component is mounted

2. **Updating Phase:**
   - `componentDidUpdate()`: Execute after component updates
   - `getSnapshotBeforeUpdate()`: Capture info before DOM update

3. **Unmounting Phase:**
   - `componentWillUnmount()`: Cleanup before component unmounts

**Functional Component Equivalents using Hooks:**

```jsx
// Class Component
class LifecycleExample extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0, data: null };
  }

  async componentDidMount() {
    console.log('Component mounted');
    // Fetch data
    const response = await fetch('/api/data');
    const data = await response.json();
    this.setState({ data });
  }

  componentDidUpdate(prevProps, prevState) {
    if (prevState.count !== this.state.count) {
      console.log('Count updated:', this.state.count);
    }
  }

  componentWillUnmount() {
    console.log('Component will unmount');
    // Cleanup subscriptions, timers, etc.
  }

  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Increment
        </button>
      </div>
    );
  }
}

// Functional Component Equivalent
import { useState, useEffect } from 'react';

function LifecycleExample() {
  const [count, setCount] = useState(0);
  const [data, setData] = useState(null);

  // componentDidMount equivalent
  useEffect(() => {
    console.log('Component mounted');
    
    const fetchData = async () => {
      const response = await fetch('/api/data');
      const result = await response.json();
      setData(result);
    };

    fetchData();

    // componentWillUnmount equivalent (cleanup function)
    return () => {
      console.log('Component will unmount');
      // Cleanup subscriptions, timers, etc.
    };
  }, []); // Empty dependency array = componentDidMount + componentWillUnmount

  // componentDidUpdate equivalent
  useEffect(() => {
    console.log('Count updated:', count);
  }, [count]); // Runs when count changes

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}
```

---

**Q5: What are React Hooks? Explain useState, useEffect, and useContext.**

**Answer:**

React Hooks are functions that let you use state and other React features in functional components. They were introduced in React 16.8.

**1. useState Hook:**
Manages local component state.

```jsx
import { useState } from 'react';

function Counter() {
  // useState returns [currentState, setterFunction]
  const [count, setCount] = useState(0);
  const [name, setName] = useState('');
  const [user, setUser] = useState({ name: '', email: '' });

  const handleIncrement = () => {
    setCount(count + 1);
    // Or use functional update for complex state updates
    setCount(prevCount => prevCount + 1);
  };

  const handleUserUpdate = (field, value) => {
    setUser(prevUser => ({
      ...prevUser,
      [field]: value
    }));
  };

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={handleIncrement}>Increment</button>
      
      <input 
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Enter name"
      />
    </div>
  );
}
```

**2. useEffect Hook:**
Handles side effects (data fetching, subscriptions, DOM manipulation).

```jsx
import { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [windowWidth, setWindowWidth] = useState(window.innerWidth);

  // Effect with cleanup (componentDidMount + componentWillUnmount)
  useEffect(() => {
    const handleResize = () => setWindowWidth(window.innerWidth);
    
    window.addEventListener('resize', handleResize);
    
    // Cleanup function
    return () => window.removeEventListener('resize', handleResize);
  }, []); // Empty deps = run once

  // Effect with dependency (componentDidUpdate)
  useEffect(() => {
    if (!userId) return;
    
    const fetchUser = async () => {
      setLoading(true);
      try {
        const response = await fetch(`/api/users/${userId}`);
        const userData = await response.json();
        setUser(userData);
      } catch (error) {
      this.emit('user:error', error);
      throw error;
    }
  }
  
  async deleteUser(id) {
    const userIndex = this.users.findIndex(user => user.id === id);
    if (userIndex > -1) {
      const user = this.users[userIndex];
      this.users.splice(userIndex, 1);
      this.emit('user:deleted', user);
      return true;
    }
    return false;
  }
}

// Using the event-driven service
const userService = new UserService();

// Event listeners
userService.on('user:creating', (userData) => {
  console.log('Creating user:', userData.email);
});

userService.on('user:created', (user) => {
  console.log('User created:', user.id);
  // Send welcome email, update analytics, etc.
});

userService.on('user:deleted', (user) => {
  console.log('User deleted:', user.id);
  // Clean up user data, send confirmation, etc.
});

userService.on('user:error', (error) => {
  console.error('User service error:', error.message);
  // Log error, send alert, etc.
});

// File system operations (Node.js built-in modules)
const fs = require('fs').promises;
const path = require('path');

class FileManager {
  constructor(basePath) {
    this.basePath = basePath;
  }
  
  async readFile(filename) {
    try {
      const filePath = path.join(this.basePath, filename);
      const content = await fs.readFile(filePath, 'utf8');
      return content;
    } catch (error) {
      if (error.code === 'ENOENT') {
        throw new Error('File not found');
      }
      throw error;
    }
  }
  
  async writeFile(filename, content) {
    const filePath = path.join(this.basePath, filename);
    await fs.writeFile(filePath, content, 'utf8');
    return filePath;
  }
  
  async appendFile(filename, content) {
    const filePath = path.join(this.basePath, filename);
    await fs.appendFile(filePath, content, 'utf8');
    return filePath;
  }
  
  async deleteFile(filename) {
    const filePath = path.join(this.basePath, filename);
    await fs.unlink(filePath);
  }
  
  async listFiles() {
    const files = await fs.readdir(this.basePath);
    const fileStats = await Promise.all(
      files.map(async (file) => {
        const filePath = path.join(this.basePath, file);
        const stats = await fs.stat(filePath);
        return {
          name: file,
          size: stats.size,
          isDirectory: stats.isDirectory(),
          modified: stats.mtime
        };
      })
    );
    return fileStats;
  }
}

// Process and environment handling
console.log('Node.js version:', process.version);
console.log('Platform:', process.platform);
console.log('Current working directory:', process.cwd());
console.log('Environment variables:', {
  NODE_ENV: process.env.NODE_ENV,
  PORT: process.env.PORT
});

// Graceful shutdown
process.on('SIGTERM', () => {
  console.log('SIGTERM received. Shutting down gracefully...');
  server.close(() => {
    console.log('Process terminated');
    process.exit(0);
  });
});

process.on('SIGINT', () => {
  console.log('SIGINT received. Shutting down gracefully...');
  server.close(() => {
    console.log('Process terminated');
    process.exit(0);
  });
});

// Unhandled errors
process.on('uncaughtException', (error) => {
  console.error('Uncaught Exception:', error);
  process.exit(1);
});

process.on('unhandledRejection', (reason, promise) => {
  console.error('Unhandled Rejection at:', promise, 'reason:', reason);
  process.exit(1);
});
```

---

**Q16: Explain the event-driven, non-blocking I/O model.**

**Answer:**

Node.js uses an event-driven, non-blocking I/O model that allows it to handle thousands of concurrent connections efficiently with a single thread.

**Key Concepts:**

1. **Event-Driven**: Operations emit events when completed
2. **Non-blocking**: Functions return immediately, don't wait for I/O operations
3. **Callback/Promise-based**: Handle results asynchronously
4. **Event Loop**: Manages execution of callbacks and events

**Detailed Examples:**

```javascript
const fs = require('fs').promises;
const http = require('http');
const { promisify } = require('util');

// BLOCKING vs NON-BLOCKING comparison

// ❌ BLOCKING (Synchronous) - BAD
function blockingExample() {
  console.log('Start');
  
  try {
    // This blocks the entire event loop
    const data1 = fs.readFileSync('file1.txt', 'utf8');
    const data2 = fs.readFileSync('file2.txt', 'utf8');
    const data3 = fs.readFileSync('file3.txt', 'utf8');
    
    console.log('All files read');
    return [data1, data2, data3];
  } catch (error) {
    console.error('Error:', error.message);
  }
  
  console.log('End');
}

// ✅ NON-BLOCKING (Asynchronous) - GOOD
async function nonBlockingExample() {
  console.log('Start');
  
  try {
    // These operations don't block the event loop
    const [data1, data2, data3] = await Promise.all([
      fs.readFile('file1.txt', 'utf8'),
      fs.readFile('file2.txt', 'utf8'),
      fs.readFile('file3.txt', 'utf8')
    ]);
    
    console.log('All files read');
    return [data1, data2, data3];
  } catch (error) {
    console.error('Error:', error.message);
  }
  
  console.log('End');
}

// Event-driven HTTP server example
const EventEmitter = require('events');

class RequestLogger extends EventEmitter {
  logRequest(req) {
    const logData = {
      method: req.method,
      url: req.url,
      timestamp: new Date().toISOString(),
      userAgent: req.headers['user-agent']
    };
    
    this.emit('request:logged', logData);
    return logData;
  }
}

const logger = new RequestLogger();

// Event listeners
logger.on('request:logged', (logData) => {
  console.log(`${logData.method} ${logData.url} - ${logData.timestamp}`);
});

logger.on('request:logged', (logData) => {
  // Save to database (non-blocking)
  saveLogToDatabase(logData);
});

logger.on('request:logged', (logData) => {
  // Send to analytics (non-blocking)
  sendToAnalytics(logData);
});

// HTTP server with non-blocking operations
const server = http.createServer(async (req, res) => {
  // Log request (event-driven)
  logger.logRequest(req);
  
  res.setHeader('Content-Type', 'application/json');
  
  try {
    if (req.url === '/api/data') {
      // Multiple non-blocking operations running concurrently
      const [userData, productData, orderData] = await Promise.all([
        fetchUserData(),
        fetchProductData(),
        fetchOrderData()
      ]);
      
      res.statusCode = 200;
      res.end(JSON.stringify({
        users: userData,
        products: productData,
        orders: orderData
      }));
      
    } else if (req.url === '/api/heavy-operation') {
      // CPU-intensive operation offloaded to worker thread
      const result = await performHeavyOperation();
      res.statusCode = 200;
      res.end(JSON.stringify({ result }));
      
    } else if (req.url === '/api/stream') {
      // Streaming response
      res.setHeader('Content-Type', 'text/plain');
      const stream = createDataStream();
      stream.pipe(res);
      
    } else {
      res.statusCode = 404;
      res.end(JSON.stringify({ error: 'Not found' }));
    }
    
  } catch (error) {
    res.statusCode = 500;
    res.end(JSON.stringify({ error: 'Internal server error' }));
  }
});

// Simulated async operations
async function fetchUserData() {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve([
        { id: 1, name: 'John' },
        { id: 2, name: 'Jane' }
      ]);
    }, 100);
  });
}

async function fetchProductData() {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve([
        { id: 1, name: 'Laptop', price: 1000 },
        { id: 2, name: 'Phone', price: 500 }
      ]);
    }, 150);
  });
}

async function fetchOrderData() {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve([
        { id: 1, userId: 1, productId: 1, quantity: 1 },
        { id: 2, userId: 2, productId: 2, quantity: 2 }
      ]);
    }, 80);
  });
}

async function saveLogToDatabase(logData) {
  // Simulate database save
  return new Promise((resolve) => {
    setTimeout(() => {
      console.log('Log saved to database');
      resolve();
    }, 50);
  });
}

async function sendToAnalytics(logData) {
  // Simulate analytics API call
  return new Promise((resolve) => {
    setTimeout(() => {
      console.log('Data sent to analytics');
      resolve();
    }, 30);
  });
}

// Worker threads for CPU-intensive operations
const { Worker, isMainThread, parentPort, workerData } = require('worker_threads');

async function performHeavyOperation() {
  return new Promise((resolve, reject) => {
    const worker = new Worker(__filename, {
      workerData: { operation: 'heavy-calculation' }
    });
    
    worker.on('message', resolve);
    worker.on('error', reject);
    worker.on('exit', (code) => {
      if (code !== 0) {
        reject(new Error(`Worker stopped with exit code ${code}`));
      }
    });
  });
}

// Worker thread code (runs in separate thread)
if (!isMainThread) {
  if (workerData.operation === 'heavy-calculation') {
    // Simulate CPU-intensive work
    let result = 0;
    for (let i = 0; i < 10000000; i++) {
      result += Math.sqrt(i);
    }
    parentPort.postMessage(result);
  }
}

// Streams for handling large data
const { Readable } = require('stream');

function createDataStream() {
  let counter = 0;
  
  return new Readable({
    read() {
      if (counter < 1000) {
        this.push(`Data chunk ${counter++}\n`);
      } else {
        this.push(null); // End of stream
      }
    }
  });
}

// Connection pooling for database
class ConnectionPool {
  constructor(maxConnections = 10) {
    this.maxConnections = maxConnections;
    this.connections = [];
    this.waitingQueue = [];
    this.activeConnections = 0;
  }
  
  async getConnection() {
    if (this.connections.length > 0) {
      return this.connections.pop();
    }
    
    if (this.activeConnections < this.maxConnections) {
      this.activeConnections++;
      return this.createConnection();
    }
    
    // Wait for available connection
    return new Promise((resolve) => {
      this.waitingQueue.push(resolve);
    });
  }
  
  releaseConnection(connection) {
    if (this.waitingQueue.length > 0) {
      const resolve = this.waitingQueue.shift();
      resolve(connection);
    } else {
      this.connections.push(connection);
    }
  }
  
  createConnection() {
    return {
      id: Math.random().toString(36),
      query: (sql) => {
        return new Promise((resolve) => {
          setTimeout(() => resolve([]), 100);
        });
      }
    };
  }
}

const pool = new ConnectionPool(5);

// Using connection pool
async function performDatabaseOperation(query) {
  const connection = await pool.getConnection();
  
  try {
    const result = await connection.query(query);
    return result;
  } finally {
    pool.releaseConnection(connection);
  }
}

// Graceful shutdown handling
let isShuttingDown = false;

process.on('SIGTERM', gracefulShutdown);
process.on('SIGINT', gracefulShutdown);

async function gracefulShutdown(signal) {
  if (isShuttingDown) return;
  
  console.log(`${signal} received. Starting graceful shutdown...`);
  isShuttingDown = true;
  
  // Stop accepting new connections
  server.close(async () => {
    console.log('HTTP server closed');
    
    // Close database connections
    // await database.close();
    
    // Wait for ongoing operations to complete
    await new Promise(resolve => setTimeout(resolve, 1000));
    
    console.log('Graceful shutdown complete');
    process.exit(0);
  });
  
  // Force shutdown after timeout
  setTimeout(() => {
    console.error('Could not close connections in time, forcefully shutting down');
    process.exit(1);
  }, 10000);
}

server.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

---

### Express.js

#### Medium Level Questions

**Q17: How do you create middleware in Express.js and what are the different types?**

**Answer:**

Express.js middleware are functions that execute during the request-response cycle. They have access to the request object (req), response object (res), and the next middleware function (next).

**Types of Middleware:**

1. **Application-level middleware**
2. **Router-level middleware**
3. **Error-handling middleware**
4. **Built-in middleware**
5. **Third-party middleware**

**Comprehensive Examples:**

```javascript
const express = require('express');
const cors = require('cors');
const helmet = require('helmet');
const rateLimit = require('express-rate-limit');
const jwt = require('jsonwebtoken');
const app = express();

// 1. APPLICATION-LEVEL MIDDLEWARE

// Basic logging middleware
const requestLogger = (req, res, next) => {
  const timestamp = new Date().toISOString();
  const method = req.method;
  const url = req.originalUrl;
  const ip = req.ip;
  
  console.log(`[${timestamp}] ${method} ${url} - ${ip}`);
  
  // Store start time for response time calculation
  req.startTime = Date.now();
  
  next(); // Pass control to next middleware
};

// Response time middleware
const responseTime = (req, res, next) => {
  res.on('finish', () => {
    const duration = Date.now() - req.startTime;
    console.log(`Response time: ${duration}ms`);
  });
  next();
};

// Request validation middleware
const validateRequest = (schema) => {
  return (req, res, next) => {
    const { error } = schema.validate(req.body);
    if (error) {
      return res.status(400).json({
        error: 'Validation failed',
        details: error.details.map(detail => detail.message)
      });
    }
    next();
  };
};

// Authentication middleware
const authenticate = async (req, res, next) => {
  try {
    const token = req.header('Authorization')?.replace('Bearer ', '');
    
    if (!token) {
      return res.status(401).json({ error: 'Access denied. No token provided.' });
    }
    
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (error) {
    res.status(401).json({ error: 'Invalid token.' });
  }
};

// Authorization middleware
const authorize = (roles) => {
  return (req, res, next) => {
    if (!req.user) {
      return res.status(401).json({ error: 'User not authenticated' });
    }
    
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({ error: 'Access forbidden. Insufficient permissions.' });
    }
    
    next();
  };
};

// Request size limiter middleware
const requestSizeLimiter = (maxSize) => {
  return (req, res, next) => {
    const contentLength = parseInt(req.headers['content-length'] || 0);
    
    if (contentLength > maxSize) {
      return res.status(413).json({
        error: 'Request entity too large',
        maxSize: `${maxSize} bytes`
      });
    }
    
    next();
  };
};

// Cache control middleware
const cacheControl = (maxAge) => {
  return (req, res, next) => {
    if (req.method === 'GET') {
      res.set('Cache-Control', `public, max-age=${maxAge}`);
    }
    next();
  };
};

// Apply application-level middleware
app.use(requestLogger);
app.use(responseTime);
app.use(helmet()); // Security headers
app.use(cors()); // CORS handling
app.use(express.json({ limit: '10mb' })); // Parse JSON bodies
app.use(express.urlencoded({ extended: true })); // Parse URL-encoded bodies
app.use(requestSizeLimiter(10 * 1024 * 1024)); // 10MB limit

// Rate limiting
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // Limit each IP to 100 requests per windowMs
  message: 'Too many requests from this IP, please try again later.',
  standardHeaders: true,
  legacyHeaders: false,
});
app.use('/api', limiter);

// 2. ROUTER-LEVEL MIDDLEWARE

const userRouter = express.Router();
const adminRouter = express.Router();

// Middleware specific to user routes
userRouter.use((req, res, next) => {
  console.log('User router middleware');
  next();
});

// Middleware for admin routes only
adminRouter.use(authenticate);
adminRouter.use(authorize(['admin', 'super-admin']));

// User routes
userRouter.get('/profile', authenticate, async (req, res) => {
  try {
    const user = await getUserById(req.user.id);
    res.json(user);
  } catch (error) {
    res.status(500).json({ error: 'Failed to fetch user profile' });
  }
});

userRouter.post('/profile', authenticate, validateRequest(userUpdateSchema), async (req, res) => {
  try {
    const updatedUser = await updateUser(req.user.id, req.body);
    res.json(updatedUser);
  } catch (error) {
    res.status(500).json({ error: 'Failed to update user profile' });
  }
});

// Admin routes
adminRouter.get('/users', async (req, res) => {
  try {
    const users = await getAllUsers();
    res.json(users);
  } catch (error) {
    res.status(500).json({ error: 'Failed to fetch users' });
  }
});

adminRouter.delete('/users/:id', async (req, res) => {
  try {
    await deleteUser(req.params.id);
    res.status(204).send();
  } catch (error) {
    res.status(500).json({ error: 'Failed to delete user' });
  }
});

// Mount routers
app.use('/api/user', userRouter);
app.use('/api/admin', adminRouter);

// 3. ERROR-HANDLING MIDDLEWARE

// Custom error class
class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = true;
    
    Error.captureStackTrace(this, this.constructor);
  }
}

// Error handling middleware (must be last)
const errorHandler = (err, req, res, next) => {
  let error = { ...err };
  error.message = err.message;
  
  console.error(err);
  
  // Mongoose bad ObjectId
  if (err.name === 'CastError') {
    const message = 'Resource not found';
    error = new AppError(message, 404);
  }
  
  // Mongoose duplicate key
  if (err.code === 11000) {
    const message = 'Duplicate field value entered';
    error = new AppError(message, 400);
  }
  
  // Mongoose validation error
  if (err.name === 'ValidationError') {
    const message = Object.values(err.errors).map(val => val.message).join(', ');
    error = new AppError(message, 400);
  }
  
  res.status(error.statusCode || 500).json({
    success: false,
    error: error.message || 'Server Error',
    ...(process.env.NODE_ENV === 'development' && { stack: err.stack })
  });
};

// 404 handler (place before error handler)
const notFound = (req, res, next) => {
  const error = new AppError(`Not found - ${req.originalUrl}`, 404);
  next(error);
};

// 4. BUILT-IN MIDDLEWARE EXAMPLES

// Serve static files with caching
app.use('/static', cacheControl(86400), express.static('public')); // 1 day cache

// 5. CUSTOM MIDDLEWARE EXAMPLES

// API versioning middleware
const apiVersioning = (req, res, next) => {
  const version = req.header('API-Version') || '1.0';
  req.apiVersion = version;
  next();
};

// Request ID middleware
const requestId = (req, res, next) => {
  req.id = require('crypto').randomUUID();
  res.set('X-Request-ID', req.id);
  next();
};

// Content negotiation middleware
const contentNegotiation = (req, res, next) => {
  const acceptHeader = req.headers.accept || 'application/json';
  
  if (acceptHeader.includes('application/xml')) {
    req.responseFormat = 'xml';
  } else if (acceptHeader.includes('text/html')) {
    req.responseFormat = 'html';
  } else {
    req.responseFormat = 'json';
  }
  
  next();
};

// Database connection middleware
const dbConnection = async (req, res, next) => {
  try {
    req.db = await getDbConnection();
    next();
  } catch (error) {
    next(new AppError('Database connection failed', 500));
  }
};

// Apply custom middleware
app.use(requestId);
app.use(apiVersioning);
app.use(contentNegotiation);

// Route with multiple middleware
app.get('/api/data',
  dbConnection,
  authenticate,
  cacheControl(300), // 5 minutes cache
  async (req, res, next) => {
    try {
      const data = await req.db.getData();
      
      switch (req.responseFormat) {
        case 'xml':
          res.set('Content-Type', 'application/xml');
          res.send(convertToXML(data));
          break;
        case 'html':
          res.render('data', { data });
          break;
        default:
          res.json(data);
      }
    } catch (error) {
      next(error);
    }
  }
);

// Conditional middleware
app.use('/api/secure', (req, res, next) => {
  if (process.env.NODE_ENV === 'production') {
    // Only apply in production
    return authenticate(req, res, next);
  }
  next();
});

// Middleware factory
const createCacheMiddleware = (cacheStore) => {
  return (req, res, next) => {
    const key = req.originalUrl;
    const cachedResponse = cacheStore.get(key);
    
    if (cachedResponse) {
      return res.json(cachedResponse);
    }
    
    // Override res.json to cache the response
    const originalJson = res.json.bind(res);
    res.json = (data) => {
      cacheStore.set(key, data, 300); // Cache for 5 minutes
      return originalJson(data);
    };
    
    next();
  };
};

// Use cache middleware
const cache = new Map();
app.use('/api/cached', createCacheMiddleware(cache));

// Apply error handling (must be last)
app.use(notFound);
app.use(errorHandler);

// Helper functions (would normally be in separate modules)
async function getUserById(id) {
  // Simulate database call
  return { id, name: 'John Doe', email: 'john@example.com' };
}

async function updateUser(id, data) {
  // Simulate database update
  return { id, ...data, updatedAt: new Date() };
}

async function getAllUsers() {
  // Simulate database call
  return [
    { id: 1, name: 'John Doe', email: 'john@example.com' },
    { id: 2, name: 'Jane Smith', email: 'jane@example.com' }
  ];
}

async function deleteUser(id) {
  // Simulate database deletion
  return true;
}

async function getDbConnection() {
  // Simulate database connection
  return {
    getData: async () => ({ data: 'sample data' })
  };
}

function convertToXML(data) {
  // Simple XML conversion
  return `<?xml version="1.0"?><data>${JSON.stringify(data)}</data>`;
}

const userUpdateSchema = {
  validate: (data) => {
    if (!data.name || !data.email) {
      return { error: { details: [{ message: 'Name and email required' }] } };
    }
    return { error: null };
  }
};

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});

module.exports = app;
```

---

### Database Technologies

#### Basic Level Questions

**Q18: Explain the differences between SQL and NoSQL databases.**

**Answer:**

SQL and NoSQL databases have fundamental differences in structure, querying, scalability, and use cases.

**SQL Databases (Relational):**
- Structured data with predefined schemas
- ACID properties (Atomicity, Consistency, Isolation, Durability)
- Use SQL for querying
- Vertical scaling (scale up)
- Strong consistency

**NoSQL Databases (Non-relational):**
- Flexible schema or schema-less
- BASE properties (Basically Available, Soft state, Eventual consistency)
- Various query languages/APIs
- Horizontal scaling (scale out)
- Eventual consistency

**Detailed Comparison with Examples:**

```javascript
// SQL DATABASE EXAMPLES

// 1. Using SQL Server with Node.js
const sql = require('mssql');

const sqlConfig = {
  user: 'username',
  password: 'password',
  database: 'mydb',
  server: 'localhost',
  pool: {
    max: 10,
    min: 0,
    idleTimeoutMillis: 30000
  },
  options: {
    encrypt: true,
    trustServerCertificate: false
  }
};

class SQLUserService {
  constructor() {
    this.pool = null;
  }
  
  async connect() {
    try {
      this.pool = await sql.connect(sqlConfig);
      console.log('Connected to SQL Server');
    } catch (error) {
      console.error('SQL Server connection error:', error);
    }
  }
  
  // SQL queries with complex joins and transactions
  async getUserWithOrders(userId) {
    try {
      const request = this.pool.request();
      request.input('userId', sql.Int, userId);
      
      const result = await request.query(`
        SELECT 
          u.id as user_id,
          u.name,
          u.email,
          u.created_at as user_created,
          o.id as order_id,
          o.total_amount,
          o.status,
          o.created_at as order_created,
          oi.product_id,
          oi.quantity,
          oi.price,
          p.name as product_name,
          p.description
        FROM users u
        LEFT JOIN orders o ON u.id = o.user_id
        LEFT JOIN order_items oi ON o.id = oi.order_id
        LEFT JOIN products p ON oi.product_id = p.id
        WHERE u.id = @userId
        ORDER BY o.created_at DESC, oi.id
      `);
      
      return this.transformSQLResult(result.recordset);
    } catch (error) {
      console.error('SQL query error:', error);
      throw error;
    }
  }
  
  // Transaction example
  async createOrderWithItems(userId, orderData) {
    const transaction = new sql.Transaction(this.pool);
    
    try {
      await transaction.begin();
      
      // Create order
      const orderRequest = transaction.request();
      orderRequest.input('userId', sql.Int, userId);
      orderRequest.input('totalAmount', sql.Decimal(10, 2), orderData.total);
      
      const orderResult = await orderRequest.query(`
        INSERT INTO orders (user_id, total_amount, status, created_at)
        OUTPUT INSERTED.id
        VALUES (@userId, @totalAmount, 'pending', GETDATE())
      `);
      
      const orderId = orderResult.recordset[0].id;
      
      // Create order items
      for (const item of orderData.items) {
        const itemRequest = transaction.request();
        itemRequest.input('orderId', sql.Int, orderId);
        itemRequest.input('productId', sql.Int, item.productId);
        itemRequest.input('quantity', sql.Int, item.quantity);
        itemRequest.input('price', sql.Decimal(10, 2), item.price);
        
        await itemRequest.query(`
          INSERT INTO order_items (order_id, product_id, quantity, price)
          VALUES (@orderId, @productId, @quantity, @price)
        `);
        
        // Update product inventory
        const inventoryRequest = transaction.request();
        inventoryRequest.input('productId', sql.Int, item.productId);
        inventoryRequest.input('quantity', sql.Int, item.quantity);
        
        await inventoryRequest.query(`
          UPDATE products 
          SET inventory_count = inventory_count - @quantity
          WHERE id = @productId AND inventory_count >= @quantity
        `);
        console.error('Failed to fetch user:', error);
      } finally {
        setLoading(false);
      }
    };

    fetchUser();
  }, [userId]); // Runs when userId changes

  // Effect without cleanup (componentDidUpdate)
  useEffect(() => {
    document.title = user ? `Profile: ${user.name}` : 'Profile';
  }, [user]);

  if (loading) return <div>Loading...</div>;
  
  return (
    <div>
      <h1>{user?.name}</h1>
      <p>Window width: {windowWidth}px</p>
    </div>
  );
}
```

**3. useContext Hook:**
Consumes context values without nesting Consumer components.

```jsx
import { createContext, useContext, useState } from 'react';

// Create Context
const ThemeContext = createContext();
const UserContext = createContext();

// Provider Component
function App() {
  const [theme, setTheme] = useState('light');
  const [user, setUser] = useState({ name: 'John', role: 'admin' });

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      <UserContext.Provider value={{ user, setUser }}>
        <Header />
        <MainContent />
      </UserContext.Provider>
    </ThemeContext.Provider>
  );
}

// Consumer Components
function Header() {
  const { theme, setTheme } = useContext(ThemeContext);
  const { user } = useContext(UserContext);

  return (
    <header className={`header ${theme}`}>
      <h1>Welcome, {user.name}</h1>
      <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
        Toggle Theme
      </button>
    </header>
  );
}

function MainContent() {
  const { theme } = useContext(ThemeContext);
  
  return (
    <main className={`main ${theme}`}>
      <p>Main content goes here</p>
    </main>
  );
}
```

#### High Level Questions

**Q6: Design a custom hook for managing API calls with loading, error, and data states.**

**Answer:**

Custom hooks encapsulate stateful logic that can be reused across components. Here's a comprehensive API management hook:

```jsx
import { useState, useEffect, useCallback, useRef } from 'react';

// Custom hook for API calls
function useApi(url, options = {}) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const abortControllerRef = useRef(null);

  const fetchData = useCallback(async (customUrl, customOptions = {}) => {
    const finalUrl = customUrl || url;
    const finalOptions = { ...options, ...customOptions };

    if (!finalUrl) {
      setError(new Error('URL is required'));
      return;
    }

    // Cancel previous request
    if (abortControllerRef.current) {
      abortControllerRef.current.abort();
    }

    // Create new abort controller
    abortControllerRef.current = new AbortController();
    
    setLoading(true);
    setError(null);

    try {
      const response = await fetch(finalUrl, {
        ...finalOptions,
        signal: abortControllerRef.current.signal,
        headers: {
          'Content-Type': 'application/json',
          ...finalOptions.headers,
        },
      });

      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }

      const result = await response.json();
      setData(result);
      return result;
    } catch (err) {
      if (err.name !== 'AbortError') {
        setError(err);
        throw err;
      }
    } finally {
      setLoading(false);
    }
  }, [url, options]);

  // Auto-fetch on mount if URL is provided
  useEffect(() => {
    if (url) {
      fetchData();
    }

    // Cleanup on unmount
    return () => {
      if (abortControllerRef.current) {
        abortControllerRef.current.abort();
      }
    };
  }, [fetchData]);

  const refetch = useCallback(() => fetchData(), [fetchData]);
  
  const reset = useCallback(() => {
    setData(null);
    setError(null);
    setLoading(false);
  }, []);

  return {
    data,
    loading,
    error,
    refetch,
    fetchData,
    reset,
  };
}

// Advanced version with caching
function useApiWithCache(url, options = {}) {
  const cacheRef = useRef(new Map());
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  const getCacheKey = (url, options) => {
    return `${url}-${JSON.stringify(options)}`;
  };

  const fetchData = useCallback(async (customUrl, customOptions = {}) => {
    const finalUrl = customUrl || url;
    const finalOptions = { ...options, ...customOptions };
    const cacheKey = getCacheKey(finalUrl, finalOptions);

    // Check cache first
    if (cacheRef.current.has(cacheKey)) {
      const cachedData = cacheRef.current.get(cacheKey);
      setData(cachedData);
      return cachedData;
    }

    setLoading(true);
    setError(null);

    try {
      const response = await fetch(finalUrl, {
        ...finalOptions,
        headers: {
          'Content-Type': 'application/json',
          ...finalOptions.headers,
        },
      });

      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }

      const result = await response.json();
      
      // Cache the result
      cacheRef.current.set(cacheKey, result);
      
      setData(result);
      return result;
    } catch (err) {
      setError(err);
      throw err;
    } finally {
      setLoading(false);
    }
  }, [url, options]);

  useEffect(() => {
    if (url) {
      fetchData();
    }
  }, [fetchData]);

  const clearCache = useCallback(() => {
    cacheRef.current.clear();
  }, []);

  return {
    data,
    loading,
    error,
    refetch: fetchData,
    clearCache,
  };
}

// Usage examples
function UserList() {
  const { data: users, loading, error, refetch } = useApi('/api/users');

  if (loading) return <div>Loading users...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <div>
      <button onClick={refetch}>Refresh</button>
      <ul>
        {users?.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
}

function UserProfile({ userId }) {
  const { 
    data: user, 
    loading, 
    error, 
    fetchData 
  } = useApi(); // No initial URL

  useEffect(() => {
    if (userId) {
      fetchData(`/api/users/${userId}`);
    }
  }, [userId, fetchData]);

  const updateUser = async (updates) => {
    try {
      await fetchData(`/api/users/${userId}`, {
        method: 'PUT',
        body: JSON.stringify(updates),
      });
    } catch (error) {
      console.error('Update failed:', error);
    }
  };

  return (
    <div>
      {loading && <p>Loading...</p>}
      {error && <p>Error: {error.message}</p>}
      {user && (
        <div>
          <h2>{user.name}</h2>
          <button onClick={() => updateUser({ status: 'active' })}>
            Activate User
          </button>
        </div>
      )}
    </div>
  );
}
```

---

### Next.js

#### Basic Level Questions

**Q7: What is Next.js and what problems does it solve?**

**Answer:**

Next.js is a React framework that provides additional structure, features, and optimizations for building production-ready applications.

**Problems Next.js Solves:**

1. **SEO and Performance**: Server-side rendering improves SEO and initial page load
2. **Routing**: File-based routing system eliminates need for router configuration
3. **Bundle Optimization**: Automatic code splitting and optimization
4. **Development Experience**: Hot reload, built-in CSS support, TypeScript support
5. **Deployment**: Easy deployment with Vercel platform
6. **API Routes**: Built-in API functionality

**Key Features:**

```jsx
// File-based routing
// pages/index.js → /
// pages/about.js → /about
// pages/blog/[slug].js → /blog/post-1, /blog/post-2, etc.

// pages/index.js
import { GetServerSideProps } from 'next';

export default function HomePage({ posts }) {
  return (
    <div>
      <h1>Welcome to My Blog</h1>
      {posts.map(post => (
        <div key={post.id}>
          <h2>{post.title}</h2>
          <p>{post.excerpt}</p>
        </div>
      ))}
    </div>
  );
}

// Server-side rendering
export const getServerSideProps: GetServerSideProps = async () => {
  const res = await fetch('https://api.example.com/posts');
  const posts = await res.json();

  return {
    props: {
      posts,
    },
  };
};

// API Routes
// pages/api/users/[id].js
export default function handler(req, res) {
  const { id } = req.query;
  
  if (req.method === 'GET') {
    // Fetch user by ID
    res.status(200).json({ id, name: 'John Doe' });
  } else if (req.method === 'PUT') {
    // Update user
    const updatedUser = { id, ...req.body };
    res.status(200).json(updatedUser);
  } else {
    res.setHeader('Allow', ['GET', 'PUT']);
    res.status(405).end(`Method ${req.method} Not Allowed`);
  }
}
```

---

**Q8: Explain the difference between CSR, SSR, and SSG in Next.js.**

**Answer:**

**1. Client-Side Rendering (CSR):**
- JavaScript executes in the browser
- Initial HTML is minimal, content loaded via JS
- Good for dynamic, interactive applications
- SEO challenges, slower initial paint

```jsx
// CSR Example - Traditional React approach
import { useState, useEffect } from 'react';

function ClientSideRendering() {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch('/api/posts')
      .then(res => res.json())
      .then(data => {
        setPosts(data);
        setLoading(false);
      });
  }, []);

  if (loading) return <div>Loading...</div>;

  return (
    <div>
      {posts.map(post => (
        <article key={post.id}>
          <h2>{post.title}</h2>
          <p>{post.content}</p>
        </article>
      ))}
    </div>
  );
}
```

**2. Server-Side Rendering (SSR):**
- HTML generated on server for each request
- Better SEO and initial page load
- Higher server load
- Good for dynamic content that changes frequently

```jsx
// SSR Example - getServerSideProps
import { GetServerSideProps } from 'next';

interface Post {
  id: string;
  title: string;
  content: string;
  author: string;
}

interface Props {
  posts: Post[];
  user: any;
}

export default function ServerSideRendering({ posts, user }: Props) {
  return (
    <div>
      <h1>Welcome, {user.name}</h1>
      {posts.map(post => (
        <article key={post.id}>
          <h2>{post.title}</h2>
          <p>By: {post.author}</p>
          <p>{post.content}</p>
        </article>
      ))}
    </div>
  );
}

// This function runs on every request
export const getServerSideProps: GetServerSideProps = async (context) => {
  const { req } = context;
  
  // Access cookies, headers, etc.
  const userToken = req.cookies.token;
  
  // Fetch data that requires authentication or real-time data
  const [postsRes, userRes] = await Promise.all([
    fetch('https://api.example.com/posts'),
    fetch('https://api.example.com/user', {
      headers: { Authorization: `Bearer ${userToken}` }
    })
  ]);

  const posts = await postsRes.json();
  const user = await userRes.json();

  // If user is not authenticated, redirect
  if (!user) {
    return {
      redirect: {
        destination: '/login',
        permanent: false,
      },
    };
  }

  return {
    props: {
      posts,
      user,
    },
  };
};
```

**3. Static Site Generation (SSG):**
- HTML generated at build time
- Best performance and SEO
- Content is static until next build
- Perfect for blogs, documentation, marketing sites

```jsx
// SSG Example - getStaticProps
import { GetStaticProps, GetStaticPaths } from 'next';

interface Post {
  id: string;
  title: string;
  content: string;
  date: string;
}

interface Props {
  post: Post;
}

export default function BlogPost({ post }: Props) {
  return (
    <article>
      <h1>{post.title}</h1>
      <time>{post.date}</time>
      <div dangerouslySetInnerHTML={{ __html: post.content }} />
    </article>
  );
}

// Generate static paths at build time
export const getStaticPaths: GetStaticPaths = async () => {
  const res = await fetch('https://api.example.com/posts');
  const posts = await res.json();

  const paths = posts.map((post: Post) => ({
    params: { id: post.id },
  }));

  return {
    paths,
    fallback: 'blocking', // or false, true, 'blocking'
  };
};

// Generate static props at build time
export const getStaticProps: GetStaticProps = async ({ params }) => {
  const res = await fetch(`https://api.example.com/posts/${params?.id}`);
  const post = await res.json();

  if (!post) {
    return {
      notFound: true,
    };
  }

  return {
    props: {
      post,
    },
    revalidate: 60, // ISR - revalidate every 60 seconds
  };
};
```

**When to use each:**

- **CSR**: Dashboards, admin panels, apps with lots of user interaction
- **SSR**: E-commerce product pages, user-specific content, real-time data
- **SSG**: Blogs, documentation, marketing sites, content that doesn't change often

---

### JavaScript & TypeScript

#### Basic Level Questions

**Q9: Explain the difference between `var`, `let`, and `const`.**

**Answer:**

**1. `var`:**
- Function-scoped or globally-scoped
- Hoisted and initialized with `undefined`
- Can be redeclared and reassigned
- Creates property on global object (in browser: `window`)

**2. `let`:**
- Block-scoped
- Hoisted but not initialized (Temporal Dead Zone)
- Cannot be redeclared in same scope
- Can be reassigned

**3. `const`:**
- Block-scoped
- Hoisted but not initialized (Temporal Dead Zone)
- Cannot be redeclared or reassigned
- Must be initialized at declaration
- Object/array contents can still be modified

**Examples:**

```javascript
// Hoisting behavior
console.log(varVariable); // undefined (not error)
console.log(letVariable); // ReferenceError: Cannot access before initialization
console.log(constVariable); // ReferenceError: Cannot access before initialization

var varVariable = 'var';
let letVariable = 'let';
const constVariable = 'const';

// Scope differences
function scopeExample() {
  if (true) {
    var varScoped = 'var'; // Function scoped
    let letScoped = 'let'; // Block scoped
    const constScoped = 'const'; // Block scoped
  }
  
  console.log(varScoped); // 'var' - accessible
  console.log(letScoped); // ReferenceError - not accessible
  console.log(constScoped); // ReferenceError - not accessible
}

// Redeclaration
var name = 'John';
var name = 'Jane'; // OK

let age = 25;
let age = 30; // SyntaxError: Identifier 'age' has already been declared

const city = 'NYC';
const city = 'LA'; // SyntaxError: Identifier 'city' has already been declared

// Reassignment
var a = 1;
a = 2; // OK

let b = 1;
b = 2; // OK

const c = 1;
c = 2; // TypeError: Assignment to constant variable

// Objects and arrays with const
const user = { name: 'John' };
user.name = 'Jane'; // OK - modifying property
user.age = 30; // OK - adding property

const numbers = [1, 2, 3];
numbers.push(4); // OK - modifying array
numbers[0] = 0; // OK - modifying element

// user = {}; // TypeError - reassigning
// numbers = []; // TypeError - reassigning

// Loop behavior difference
console.log('var in loops:');
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // Prints: 3, 3, 3
}

console.log('let in loops:');
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 200); // Prints: 0, 1, 2
}

// Temporal Dead Zone example
function temporalDeadZoneExample() {
  console.log(typeof undeclaredVariable); // "undefined"
  console.log(typeof letVariable); // ReferenceError
  
  let letVariable = 'Hello';
}
```

---

**Q10: What are closures and provide an example.**

**Answer:**

A closure is a function that has access to variables from its outer (enclosing) scope even after the outer function has finished executing. Closures are created when a function is defined inside another function and the inner function references variables from the outer function.

**How Closures Work:**
1. Inner function has access to outer function's variables
2. Outer function's variables remain accessible even after outer function returns
3. Each closure maintains its own copy of the outer variables

**Examples:**

```javascript
// Basic Closure Example
function outerFunction(x) {
  // This is the outer function's scope
  
  function innerFunction(y) {
    // Inner function has access to x and y
    return x + y;
  }
  
  return innerFunction;
}

const addFive = outerFunction(5);
console.log(addFive(3)); // 8 - x (5) is still accessible

// Practical Example: Counter
function createCounter() {
  let count = 0; // Private variable
  
  return {
    increment: () => ++count,
    decrement: () => --count,
    getCount: () => count
  };
}

const counter1 = createCounter();
const counter2 = createCounter();

console.log(counter1.increment()); // 1
console.log(counter1.increment()); // 2
console.log(counter2.increment()); // 1 - separate closure
console.log(counter1.getCount());  // 2

// Module Pattern using Closures
const calculatorModule = (function() {
  let result = 0; // Private variable
  
  function add(x) { result += x; }
  function multiply(x) { result *= x; }
  function getResult() { return result; }
  function reset() { result = 0; }
  
  // Public interface
  return {
    add,
    multiply,
    getResult,
    reset
  };
})();

calculatorModule.add(5);
calculatorModule.multiply(2);
console.log(calculatorModule.getResult()); // 10

// Function Factory with Closures
function multiplierFactory(multiplier) {
  return function(x) {
    return x * multiplier;
  };
}

const double = multiplierFactory(2);
const triple = multiplierFactory(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15

// Event Handlers with Closures
function setupButtons() {
  const buttons = document.querySelectorAll('.btn');
  
  for (let i = 0; i < buttons.length; i++) {
    // Closure captures the current value of i
    buttons[i].addEventListener('click', function() {
      console.log(`Button ${i} clicked`);
    });
  }
}

// Closure in setTimeout
function delayedGreeting(name) {
  const message = `Hello, ${name}!`;
  
  setTimeout(function() {
    // Closure has access to 'message' and 'name'
    console.log(message);
  }, 1000);
}

delayedGreeting('John'); // Prints "Hello, John!" after 1 second

// Advanced: Currying with Closures
function curry(fn) {
  return function curried(...args) {
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    } else {
      return function(...nextArgs) {
        return curried.apply(this, args.concat(nextArgs));
      };
    }
  };
}

function add(a, b, c) {
  return a + b + c;
}

const curriedAdd = curry(add);

console.log(curriedAdd(1)(2)(3)); // 6
console.log(curriedAdd(1, 2)(3)); // 6
console.log(curriedAdd(1)(2, 3)); // 6

// Memory Management with Closures
function createLargeDataHandler() {
  const largeData = new Array(1000000).fill('data');
  
  return function processData() {
    // This closure keeps largeData in memory
    return largeData.length;
  };
}

// Be careful - largeData won't be garbage collected
const handler = createLargeDataHandler();
```

**Common Pitfalls:**

```javascript
// Problem: Loop with var
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // Prints: 3, 3, 3
}

// Solution 1: Use let
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // Prints: 0, 1, 2
}

// Solution 2: IIFE (Immediately Invoked Function Expression)
for (var i = 0; i < 3; i++) {
  (function(index) {
    setTimeout(() => console.log(index), 100); // Prints: 0, 1, 2
  })(i);
}
```

---

#### Medium Level Questions

**Q11: Explain prototypal inheritance in JavaScript.**

**Answer:**

JavaScript uses prototypal inheritance where objects can inherit directly from other objects. Every object has a hidden `[[Prototype]]` property that points to another object (its prototype).

**How Prototypal Inheritance Works:**

```javascript
// Creating objects with prototypal inheritance

// Method 1: Object.create()
const animalPrototype = {
  makeSound() {
    console.log(`${this.name} makes a sound`);
  },
  eat() {
    console.log(`${this.name} is eating`);
  }
};

const dog = Object.create(animalPrototype);
dog.name = 'Buddy';
dog.breed = 'Golden Retriever';
dog.bark = function() {
  console.log(`${this.name} barks`);
};

dog.makeSound(); // "Buddy makes a sound"
dog.bark(); // "Buddy barks"

// Method 2: Constructor Functions
function Animal(name, species) {
  this.name = name;
  this.species = species;
}

Animal.prototype.makeSound = function() {
  console.log(`${this.name} makes a sound`);
};

Animal.prototype.getInfo = function() {
  return `${this.name} is a ${this.species}`;
};

function Dog(name, breed) {
  // Call parent constructor
  Animal.call(this, name, 'Canine');
  this.breed = breed;
}

// Set up inheritance
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

// Add methods specific to Dog
Dog.prototype.bark = function() {
  console.log(`${this.name} barks`);
};

Dog.prototype.fetch = function() {
  console.log(`${this.name} fetches the ball`);
};

const myDog = new Dog('Max', 'Labrador');
myDog.makeSound(); // "Max makes a sound" (inherited)
myDog.bark(); // "Max barks" (own method)
console.log(myDog.getInfo()); // "Max is a Canine" (inherited)

// Method 3: ES6 Classes (syntactic sugar over prototypal inheritance)
class Vehicle {
  constructor(make, model, year) {
    this.make = make;
    this.model = model;
    this.year = year;
  }
  
  start() {
    console.log(`${this.make} ${this.model} is starting`);
  }
  
  getAge() {
    return new Date().getFullYear() - this.year;
  }
}

class Car extends Vehicle {
  constructor(make, model, year, doors) {
    super(make, model, year); // Call parent constructor
    this.doors = doors;
  }
  
  honk() {
    console.log(`${this.make} ${this.model} honks`);
  }
  
  // Override parent method
  start() {
    console.log('Turning key...');
    super.start(); // Call parent method
    console.log('Engine running');
  }
}

const myCar = new Car('Toyota', 'Camry', 2020, 4);
myCar.start(); // Uses overridden method
myCar.honk(); // "Toyota Camry honks"
console.log(myCar.getAge()); // Inherited method

// Prototype Chain Exploration
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function() {
  return `Hello, I'm ${this.name}`;
};

const john = new Person('John');

console.log(john.greet()); // "Hello, I'm John"

// Checking the prototype chain
console.log(john.hasOwnProperty('name')); // true
console.log(john.hasOwnProperty('greet')); // false
console.log(john.__proto__ === Person.prototype); // true
console.log(Person.prototype.__proto__ === Object.prototype); // true
console.log(Object.prototype.__proto__); // null

// Dynamic prototype modification
Person.prototype.sayGoodbye = function() {
  return `Goodbye from ${this.name}`;
};

console.log(john.sayGoodbye()); // "Goodbye from John"

// Mixin Pattern (Multiple Inheritance Simulation)
const CanFly = {
  fly() {
    console.log(`${this.name} is flying`);
  }
};

const CanSwim = {
  swim() {
    console.log(`${this.name} is swimming`);
  }
};

function Duck(name) {
  this.name = name;
}

// Combine multiple behaviors
Object.assign(Duck.prototype, CanFly, CanSwim);

const donald = new Duck('Donald');
donald.fly(); // "Donald is flying"
donald.swim(); // "Donald is swimming"
```

**Advanced Prototype Patterns:**

```javascript
// Factory Pattern with Prototypes
function createPerson(name, age) {
  const person = Object.create({
    greet() {
      return `Hi, I'm ${this.name}, ${this.age} years old`;
    },
    
    haveBirthday() {
      this.age++;
      return `Happy birthday! Now I'm ${this.age}`;
    }
  });
  
  person.name = name;
  person.age = age;
  
  return person;
}

const alice = createPerson('Alice', 25);
console.log(alice.greet()); // "Hi, I'm Alice, 25 years old"
console.log(alice.haveBirthday()); // "Happy birthday! Now I'm 26"

// Prototype-based Composition
const Eventful = {
  on(event, callback) {
    this.events = this.events || {};
    this.events[event] = this.events[event] || [];
    this.events[event].push(callback);
  },
  
  emit(event, data) {
    this.events = this.events || {};
    if (this.events[event]) {
      this.events[event].forEach(callback => callback(data));
    }
  }
};

function Button(label) {
  this.label = label;
}

Object.assign(Button.prototype, Eventful);

Button.prototype.click = function() {
  this.emit('click', { button: this.label });
};

const button = new Button('Submit');
button.on('click', (data) => console.log(`Button ${data.button} clicked!`));
button.click(); // "Button Submit clicked!"
```

---

**Q12: What is the event loop and how does it work?**

**Answer:**

The Event Loop is the mechanism that handles asynchronous operations in JavaScript. Since JavaScript is single-threaded, the event loop allows it to perform non-blocking operations by offloading operations to the browser or Node.js APIs and handling their completion.

**Event Loop Components:**

1. **Call Stack**: Where function executions are tracked
2. **Web APIs/Node APIs**: Handle async operations (setTimeout, HTTP requests, etc.)
3. **Callback Queue**: Holds completed async operation callbacks
4. **Microtask Queue**: Holds Promise callbacks and other microtasks
5. **Event Loop**: Manages the execution order

**How it Works:**

```javascript
// Event Loop Example
console.log('1'); // Synchronous - goes to call stack immediately

setTimeout(() => {
  console.log('2'); // Asynchronous - goes to Web API, then callback queue
}, 0);

Promise.resolve().then(() => {
  console.log('3'); // Microtask - goes to microtask queue
});

console.log('4'); // Synchronous - goes to call stack immediately

// Output: 1, 4, 3, 2

// Detailed breakdown:
// 1. console.log('1') - call stack, executes immediately
// 2. setTimeout - goes to Web API, timer starts
// 3. Promise.resolve().then() - creates microtask
// 4. console.log('4') - call stack, executes immediately
// 5. Call stack empty, event loop checks microtask queue first
// 6. console.log('3') executes (microtask)
// 7. Microtask queue empty, event loop checks callback queue
// 8. console.log('2') executes (callback)
```

**Practical Examples:**

```javascript
// Example 1: Understanding execution order
function example1() {
  console.log('Start');
  
  setTimeout(() => console.log('Timeout 1'), 0);
  setTimeout(() => console.log('Timeout 2'), 0);
  
  Promise.resolve()
    .then(() => console.log('Promise 1'))
    .then(() => console.log('Promise 2'));
  
  console.log('End');
}

example1();
// Output: Start, End, Promise 1, Promise 2, Timeout 1, Timeout 2

// Example 2: Nested setTimeout vs Promise
function example2() {
  setTimeout(() => {
    console.log('Timeout 1');
    Promise.resolve().then(() => console.log('Promise in timeout'));
  }, 0);
  
  Promise.resolve().then(() => {
    console.log('Promise 1');
    setTimeout(() => console.log('Timeout in promise'), 0);
  });
}

example2();
// Output: Promise 1, Timeout 1, Promise in timeout, Timeout in promise

// Example 3: Blocking the event loop
function blockingExample() {
  console.log('Before blocking');
  
  setTimeout(() => console.log('This will be delayed'), 0);
  
  // Blocking operation
  const start = Date.now();
  while (Date.now() - start < 2000) {
    // Block for 2 seconds
  }
  
  console.log('After blocking');
}

blockingExample();
// Output: Before blocking, After blocking (2 sec delay), This will be delayed

// Example 4: Event Loop with fetch
async function fetchExample() {
  console.log('1');
  
  fetch('https://jsonplaceholder.typicode.com/posts/1')
    .then(response => response.json())
    .then(data => console.log('Fetch result'));
  
  setTimeout(() => console.log('2'), 0);
  
  await Promise.resolve();
  console.log('3');
  
  Promise.resolve().then(() => console.log('4'));
}

fetchExample();
// Output: 1, 3, 4, 2, Fetch result (network dependent)
```

**Advanced Event Loop Concepts:**

```javascript
// Microtask vs Macrotask priority
function priorityExample() {
  console.log('=== Start ===');
  
  // Macrotasks (callback queue)
  setTimeout(() => console.log('setTimeout 1'), 0);
  setImmediate(() => console.log('setImmediate')); // Node.js only
  
  // Microtasks (microtask queue)
  Promise.resolve().then(() => {
    console.log('Promise 1');
    return Promise.resolve();
  }).then(() => console.log('Promise 2'));
  
  queueMicrotask(() => console.log('queueMicrotask'));
  
  process.nextTick(() => console.log('nextTick')); // Node.js only - highest priority
  
  console.log('=== End ===');
}

// Event Loop visualization function
function visualizeEventLoop() {
  const steps = [];
  
  const log = (message, type = 'sync') => {
    steps.push({ message, type, time: Date.now() });
    console.log(`[${type.toUpperCase()}] ${message}`);
  };
  
  log('Step 1: Synchronous');
  
  setTimeout(() => log('Step 4: setTimeout (macrotask)', 'async'), 0);
  
  Promise.resolve().then(() => log('Step 3: Promise (microtask)', 'async'));
  
  log('Step 2: Synchronous');
  
  // After all synchronous code
  setTimeout(() => {
    console.log('\n=== Execution Order ===');
    steps.forEach((step, index) => {
      console.log(`${index + 1}. ${step.message}`);
    });
  }, 10);
}

visualizeEventLoop();

// Practical application: Task scheduler
class TaskScheduler {
  constructor() {
    this.tasks = [];
    this.running = false;
  }
  
  addTask(task, priority = 'normal') {
    this.tasks.push({ task, priority });
    if (!this.running) {
      this.processTasks();
    }
  }
  
  processTasks() {
    this.running = true;
    
    const processNext = () => {
      if (this.tasks.length === 0) {
        this.running = false;
        return;
      }
      
      // Sort by priority
      this.tasks.sort((a, b) => {
        const priorities = { high: 3, normal: 2, low: 1 };
        return priorities[b.priority] - priorities[a.priority];
      });
      
      const { task } = this.tasks.shift();
      
      // Use microtask for high priority, macrotask for others
      if (task.priority === 'high') {
        Promise.resolve().then(() => {
          task();
          processNext();
        });
      } else {
        setTimeout(() => {
          task();
          processNext();
        }, 0);
      }
    };
    
    processNext();
  }
}

const scheduler = new TaskScheduler();
scheduler.addTask(() => console.log('Normal task 1'), 'normal');
scheduler.addTask(() => console.log('High priority task'), 'high');
scheduler.addTask(() => console.log('Low priority task'), 'low');
scheduler.addTask(() => console.log('Normal task 2'), 'normal');

// Performance monitoring
function measureEventLoopLag() {
  const start = process.hrtime.bigint();
  
  setImmediate(() => {
    const lag = Number(process.hrtime.bigint() - start) / 1000000; // Convert to ms
    console.log(`Event loop lag: ${lag.toFixed(2)}ms`);
  });
}

setInterval(measureEventLoopLag, 1000);
```

---

### TypeScript

#### Basic Level Questions

**Q13: What is TypeScript and what are its benefits?**

**Answer:**

TypeScript is a statically typed superset of JavaScript developed by Microsoft. It adds optional type annotations and compiles to plain JavaScript.

**Key Benefits:**

1. **Type Safety**: Catch errors at compile time
2. **Better IDE Support**: Enhanced autocomplete, refactoring, navigation
3. **Self-Documenting Code**: Types serve as documentation
4. **Improved Maintainability**: Easier to refactor and understand large codebases
5. **Modern JavaScript Features**: Use latest JS features with backwards compatibility
6. **Gradual Adoption**: Can be introduced incrementally

**Basic TypeScript Examples:**

```typescript
// Basic Types
let name: string = 'John';
let age: number = 30;
let isActive: boolean = true;
let hobbies: string[] = ['reading', 'swimming'];
let tuple: [string, number] = ['John', 30];

// Any type (avoid when possible)
let dynamicValue: any = 'hello';
dynamicValue = 42;
dynamicValue = true;

// Union types
let id: string | number;
id = 'user123';
id = 123;

// Object types
let user: {
  name: string;
  age: number;
  email?: string; // Optional property
} = {
  name: 'John',
  age: 30
};

// Function types
function greet(name: string): string {
  return `Hello, ${name}!`;
}

const add = (a: number, b: number): number => a + b;

// Function with optional parameters
function createUser(name: string, age: number, email?: string): object {
  return { name, age, ...(email && { email }) };
}

// Function with default parameters
function multiply(a: number, b: number = 1): number {
  return a * b;
}

// Rest parameters
function sum(...numbers: number[]): number {
  return numbers.reduce((total, num) => total + num, 0);
}

// Interface definitions
interface User {
  readonly id: number; // Readonly property
  name: string;
  email: string;
  age?: number; // Optional property
  isActive: boolean;
}

interface UserMethods {
  updateEmail(newEmail: string): void;
  deactivate(): void;
}

// Extending interfaces
interface AdminUser extends User {
  role: 'admin' | 'super-admin';
  permissions: string[];
}

// Implementing interfaces
class UserService implements UserMethods {
  private users: User[] = [];
  
  updateEmail(newEmail: string): void {
    // Implementation
  }
  
  deactivate(): void {
    // Implementation
  }
  
  addUser(user: User): void {
    this.users.push(user);
  }
  
  getUser(id: number): User | undefined {
    return this.users.find(user => user.id === id);
  }
}

// Type aliases
type Status = 'pending' | 'approved' | 'rejected';
type UserRole = 'user' | 'admin' | 'moderator';

interface Task {
  id: string;
  title: string;
  status: Status;
  assignedTo: User;
}

// Generic types
function identity<T>(arg: T): T {
  return arg;
}

const stringResult = identity<string>('hello'); // Type: string
const numberResult = identity(42); // Type inferred as number

// Generic interfaces
interface Repository<T> {
  findById(id: string): T | null;
  save(entity: T): T;
  delete(id: string): boolean;
}

class UserRepository implements Repository<User> {
  private users: User[] = [];
  
  findById(id: string): User | null {
    return this.users.find(user => user.id.toString() === id) || null;
  }
  
  save(user: User): User {
    this.users.push(user);
    return user;
  }
  
  delete(id: string): boolean {
    const index = this.users.findIndex(user => user.id.toString() === id);
    if (index > -1) {
      this.users.splice(index, 1);
      return true;
    }
    return false;
  }
}

// Enum types
enum Color {
  Red = 'red',
  Green = 'green',
  Blue = 'blue'
}

enum HttpStatus {
  OK = 200,
  NotFound = 404,
  InternalServerError = 500
}

// Using enums
function setThemeColor(color: Color): void {
  document.body.style.backgroundColor = color;
}

setThemeColor(Color.Blue);

// Type assertions
let someValue: unknown = 'this is a string';
let strLength: number = (someValue as string).length;
// Alternative syntax: let strLength: number = (<string>someValue).length;

// Literal types
type ButtonVariant = 'primary' | 'secondary' | 'danger';
type Size = 'small' | 'medium' | 'large';

interface Button {
  variant: ButtonVariant;
  size: Size;
  disabled?: boolean;
}

function createButton(config: Button): HTMLButtonElement {
  const button = document.createElement('button');
  button.className = `btn btn-${config.variant} btn-${config.size}`;
  button.disabled = config.disabled || false;
  return button;
}
```

---

**Q14: Explain interfaces vs types in TypeScript.**

**Answer:**

Both interfaces and types can define object shapes, but they have different capabilities and use cases.

**Interfaces:**
- Primarily for object shapes
- Can be extended and merged
- Support declaration merging
- Better for OOP patterns
- Can be implemented by classes

**Types:**
- More flexible, can define any type
- Support union, intersection, and complex types
- Cannot be merged
- Better for functional programming patterns
- More concise for complex type manipulations

**Detailed Comparison:**

```typescript
// INTERFACES

// Basic interface
interface User {
  id: number;
  name: string;
  email: string;
}

// Interface extension
interface AdminUser extends User {
  role: string;
  permissions: string[];
}

// Multiple inheritance
interface Timestamped {
  createdAt: Date;
  updatedAt: Date;
}

interface AuditableUser extends User, Timestamped {
  lastLoginAt?: Date;
}

// Interface merging (declaration merging)
interface Window {
  customProperty: string;
}

interface Window {
  anotherProperty: number;
}

// Now Window has both customProperty and anotherProperty

// Implementing interfaces in classes
interface Flyable {
  fly(): void;
  altitude: number;
}

interface Swimmable {
  swim(): void;
  depth: number;
}

class Duck implements Flyable, Swimmable {
  altitude: number = 0;
  depth: number = 0;
  
  fly(): void {
    this.altitude = 100;
    console.log('Duck is flying');
  }
  
  swim(): void {
    this.depth = 5;
    console.log('Duck is swimming');
  }
}

// TYPES

// Basic type
type UserType = {
  id: number;
  name: string;
  email: string;
};

// Type intersection (similar to extends)
type AdminUserType = UserType & {
  role: string;
  permissions: string[];
};

// Union types
type Status = 'loading' | 'success' | 'error';
type ID = string | number;

// Complex type manipulations
type EventHandler<T> = (event: T) => void;
type AsyncEventHandler<T> = (event: T) => Promise<void>;

// Conditional types
type NonNullable<T> = T extends null | undefined ? never : T;

// Mapped types
type Partial<T> = {
  [P in keyof T]?: T[P];
};

type Required<T> = {
  [P in keyof T]-?: T[P];
};

// Template literal types
type CSSProperty = 'margin' | 'padding' | 'border';
type CSSDirection = 'top' | 'right' | 'bottom' | 'left';
type CSSPropertyWithDirection = `${CSSProperty}-${CSSDirection}`;
// Results in: 'margin-top' | 'margin-right' | ... etc.

// Function types
type Calculator = (a: number, b: number) => number;
type AsyncDataFetcher<T> = (url: string) => Promise<T>;

// Tuple types
type Coordinates = [number, number];
type NamedCoordinates = [x: number, y: number];

// When to use Interface vs Type

// Use Interface when:
// 1. Defining object shapes
interface ApiResponse {
  data: unknown;
  status: number;
  message: string;
}

// 2. You might need to extend or merge
interface BaseComponent {
  render(): void;
}

interface BaseComponent {
  destroy(): void; // Declaration merging
}

// 3. Implementing in classes
interface Validator {
  validate(value: unknown): boolean;
}

class EmailValidator implements Validator {
  validate(value: unknown): boolean {
    return typeof value === 'string' && value.includes('@');
  }
}

// Use Type when:
// 1. Creating union types
type Theme = 'light' | 'dark' | 'auto';

// 2. Complex type manipulations
type DeepPartial<T> = {
  [P in keyof T]?: T[P] extends object ? DeepPartial<T[P]> : T[P];
};

// 3. Function types
type Middleware = (req: Request, res: Response, next: NextFunction) => void;

// 4. Conditional logic
type ApiResult<T> = T extends string 
  ? { message: T }
  : T extends number
  ? { code: T }
  : { data: T };

// Practical examples combining both

// Interface for base structure
interface BaseEntity {
  id: string;
  createdAt: Date;
  updatedAt: Date;
}

// Type for operations
type EntityOperations<T> = {
  create: (data: Omit<T, keyof BaseEntity>) => Promise<T>;
  update: (id: string, data: Partial<T>) => Promise<T>;
  delete: (id: string) => Promise<boolean>;
  findById: (id: string) => Promise<T | null>;
};

// Combining interface and type
interface Product extends BaseEntity {
  name: string;
  price: number;
  category: string;
}

type ProductService = EntityOperations<Product> & {
  findByCategory: (category: string) => Promise<Product[]>;
  updatePrice: (id: string, newPrice: number) => Promise<Product>;
};

// Advanced pattern: Discriminated unions with interfaces
interface LoadingState {
  status: 'loading';
}

interface SuccessState {
  status: 'success';
  data: unknown;
}

interface ErrorState {
  status: 'error';
  error: string;
}

type AsyncState = LoadingState | SuccessState | ErrorState;

function handleState(state: AsyncState) {
  switch (state.status) {
    case 'loading':
      return 'Loading...';
    case 'success':
      return `Data: ${JSON.stringify(state.data)}`;
    case 'error':
      return `Error: ${state.error}`;
    default:
      // TypeScript ensures exhaustive checking
      const exhaustiveCheck: never = state;
      return exhaustiveCheck;
  }
}
```

---

## Backend Technologies

### Node.js

#### Basic Level Questions

**Q15: What is Node.js and how does it work?**

**Answer:**

Node.js is a JavaScript runtime built on Chrome's V8 JavaScript engine that allows you to run JavaScript on the server side. It uses an event-driven, non-blocking I/O model that makes it efficient for building scalable network applications.

**Key Characteristics:**

1. **Single-threaded Event Loop**: Handles multiple concurrent operations
2. **Non-blocking I/O**: Operations don't wait for completion
3. **Built-in Modules**: File system, HTTP, crypto, etc.
4. **NPM Ecosystem**: Largest package repository
5. **Cross-platform**: Runs on Windows, macOS, Linux

**How Node.js Works:**

```javascript
// Basic Node.js server
const http = require('http');
const url = require('url');
const fs = require('fs').promises;

const server = http.createServer(async (req, res) => {
  const parsedUrl = url.parse(req.url, true);
  const path = parsedUrl.pathname;
  const method = req.method;
  
  // Set CORS headers
  res.setHeader('Access-Control-Allow-Origin', '*');
  res.setHeader('Content-Type', 'application/json');
  
  try {
    if (method === 'GET' && path === '/api/users') {
      // Simulate async database operation
      const users = await getUsersFromDatabase();
      res.statusCode = 200;
      res.end(JSON.stringify(users));
      
    } else if (method === 'POST' && path === '/api/users') {
      // Handle POST request
      let body = '';
      
      req.on('data', chunk => {
        body += chunk.toString();
      });
      
      req.on('end', async () => {
        try {
          const userData = JSON.parse(body);
          const newUser = await createUser(userData);
          res.statusCode = 201;
          res.end(JSON.stringify(newUser));
        } catch (error) {
          res.statusCode = 400;
          res.end(JSON.stringify({ error: 'Invalid JSON' }));
        }
      });
      
    } else if (method === 'GET' && path === '/api/file') {
      // File operations (non-blocking)
      try {
        const fileContent = await fs.readFile('./data.txt', 'utf8');
        res.statusCode = 200;
        res.end(JSON.stringify({ content: fileContent }));
      } catch (error) {
        res.statusCode = 404;
        res.end(JSON.stringify({ error: 'File not found' }));
      }
      
    } else {
      res.statusCode = 404;
      res.end(JSON.stringify({ error: 'Route not found' }));
    }
    
  } catch (error) {
    res.statusCode = 500;
    res.end(JSON.stringify({ error: 'Internal server error' }));
  }
});

// Simulate database operations
async function getUsersFromDatabase() {
  // Simulate async operation
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve([
        { id: 1, name: 'John Doe', email: 'john@example.com' },
        { id: 2, name: 'Jane Smith', email: 'jane@example.com' }
      ]);
    }, 100);
  });
}

async function createUser(userData) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (!userData.name || !userData.email) {
        reject(new Error('Name and email are required'));
      } else {
        resolve({
          id: Date.now(),
          ...userData,
          createdAt: new Date().toISOString()
        });
      }
    }, 50);
  });
}

const PORT = process.env.PORT || 3000;
server.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});

// Event-driven architecture examples
const EventEmitter = require('events');

class UserService extends EventEmitter {
  constructor() {
    super();
    this.users = [];
  }
  
  async createUser(userData) {
    try {
      this.emit('user:creating', userData);
      
      // Simulate validation
      if (!userData.email) {
        throw new Error('Email is required');
      }
      
      const user = {
        id: Date.now(),
        ...userData,
        createdAt: new Date()
      };
      
      this.users.push(user);
      this.emit('user:created', user);
      
      return user;
    } catch (error) {