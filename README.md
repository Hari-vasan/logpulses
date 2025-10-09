# FastAPI Request Logger

[![Python Version](https://img.shields.io/badge/python-3.8%2B-blue.svg)](https://www.python.org/downloads/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.100%2B-009688.svg)](https://fastapi.tiangolo.com/)

A comprehensive, production-ready logging middleware for FastAPI applications that provides detailed insights into every request and response with zero configuration.

## ✨ Features

- 🚀 **Zero Configuration** - Just add one line of middleware
- 📊 **Comprehensive Logging** - Captures everything:
  - Request details (method, route, headers, body, query params)
  - Response details (status, body, size)
  - Performance metrics (execution time, memory usage)
  - System metrics (CPU, memory)
  - Network information (interface type - WiFi/Ethernet, IP, traffic)
- 🔄 **Works with All Request Types** - GET, POST, PUT, PATCH, DELETE
- 💪 **No Body Consumption Issues** - Routes can freely use `request.json()` and `request.body()`
- 🎯 **Smart Network Detection** - Automatically identifies WiFi vs Ethernet
- 📝 **Beautiful JSON Output** - Pretty-printed, structured logs
- ⚡ **High Performance** - Minimal overhead using ASGI-level interception
- 🛡️ **Production Ready** - Error handling and edge case coverage

## 📦 Installation

```bash
pip install fastapi-request-logger
```

Or install from source:

```bash
git clone https://github.com/yourusername/fastapi-request-logger.git
cd fastapi-request-logger
pip install -e .
```

## 🚀 Quick Start

```python
from fastapi import FastAPI, Request
from fastapi_request_logger import RequestLoggingMiddleware

app = FastAPI()

# That's it! One line to enable comprehensive logging
app.add_middleware(RequestLoggingMiddleware)

# Now write your routes as normal
@app.get("/users")
async def get_users():
    return {"users": []}

@app.post("/users")
async def create_user(request: Request):
    data = await request.json()  # Works perfectly!
    return {"user": data}
```

## 📋 Log Output Example

```json
{
  "timestamp": "2025-10-09 12:30:45",
  "request": {
    "route": "/users",
    "method": "POST",
    "fullUrl": "http://localhost:8000/users",
    "clientIp": "127.0.0.1",
    "userAgent": "Mozilla/5.0...",
    "size": "156 bytes",
    "body": {
      "name": "John Doe",
      "email": "john@example.com"
    }
  },
  "response": {
    "status": 200,
    "success": true,
    "size": "89 bytes",
    "body": {
      "id": 1,
      "name": "John Doe",
      "email": "john@example.com"
    }
  },
  "performance": {
    "processingTime": "12.45 ms",
    "memoryUsed": "2.34 KB"
  },
  "system": {
    "cpuUsage": "15.2%",
    "memoryUsage": {
      "total": "16.00 GB",
      "used": "8.45 GB",
      "available": "7.55 GB",
      "percent": "52.8%"
    }
  },
  "network": {
    "interface": "Wi-Fi",
    "type": "WiFi",
    "ip": "192.168.1.100",
    "netmask": "255.255.255.0",
    "isActive": true,
    "bytesSent": "45.67 MB",
    "bytesRecv": "123.45 MB"
  },
  "server": {
    "instanceId": "a1b2c3d4-e5f6-7890",
    "platform": "Windows",
    "hostname": "MY-LAPTOP"
  }
}
```

## 🔧 Configuration

### Exclude Specific Paths

```python
# Don't log health checks or metrics endpoints
app.add_middleware(
    RequestLoggingMiddleware,
    exclude_paths=["/health", "/metrics", "/docs"]
)
```

### Custom Configuration (Advanced)

```python
from fastapi_request_logger import RequestLoggingMiddleware, LogConfig

config = LogConfig(
    exclude_paths=["/health", "/metrics"],
    max_body_size=5000,
    log_headers=False
)

app.add_middleware(
    RequestLoggingMiddleware,
    exclude_paths=config.exclude_paths
)
```

## 📖 Use Cases

### Development
- **Debug API issues** - See exact request/response data
- **Monitor performance** - Track slow endpoints
- **Understand traffic** - See which routes are called most

### Production
- **Request tracing** - Full audit trail of all API calls
- **Performance monitoring** - Identify bottlenecks
- **Error tracking** - Capture failed requests with full context
- **Compliance** - Log all data access for regulatory requirements

### Testing
- **Integration testing** - Verify request/response formats
- **Load testing** - Monitor system resources under load
- **API documentation** - Generate examples from real requests

## 🎯 Why This Library?

**Problem:** Most FastAPI logging solutions either:
- Consume the request body, breaking `request.json()`
- Require decorators on every route
- Don't capture comprehensive metrics
- Have complex setup

**Solution:** This library works at the ASGI level to:
- ✅ Intercept requests before routes see them
- ✅ Cache body data for reuse
- ✅ Require zero changes to existing routes
- ✅ Capture system, network, and performance metrics
- ✅ One-line setup

## 🔄 How It Works

1. **ASGI Interception** - Middleware sits at the ASGI layer
2. **Body Caching** - Request body is read and cached
3. **Transparent Proxying** - Cached body is provided to routes
4. **Response Capture** - Response is intercepted and logged
5. **Metrics Collection** - System/network metrics gathered
6. **Structured Logging** - Everything output as JSON

## 🛠️ Requirements

- Python 3.8+
- FastAPI 0.100+
- Starlette 0.27+
- psutil 5.9+

## 📝 API Reference

### `RequestLoggingMiddleware`

Main middleware class for request logging.

**Parameters:**
- `app` - FastAPI application instance
- `exclude_paths` (optional) - List of paths to exclude from logging

**Example:**
```python
app.add_middleware(RequestLoggingMiddleware, exclude_paths=["/health"])
```

### `LogConfig`

Configuration class for advanced settings.

**Parameters:**
- `exclude_paths` - List of paths to exclude
- `log_request_body` - Whether to log request bodies (default: True)
- `log_response_body` - Whether to log response bodies (default: True)
- `max_body_size` - Maximum body size to log in bytes (default: 5000)
- `log_headers` - Whether to log headers (default: False)
- `sensitive_fields` - List of field names to redact (default: ["password", "token", "secret", "api_key"])

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- FastAPI community for the amazing framework
- Starlette for the solid foundation
- psutil for system metrics

## 📧 Contact

- **Author:** Your Name
- **Email:** your.email@example.com
- **GitHub:** [@yourusername](https://github.com/yourusername)

## 🔗 Links

- [Documentation](https://github.com/yourusername/fastapi-request-logger#readme)
- [Issue Tracker](https://github.com/yourusername/fastapi-request-logger/issues)
- [PyPI Package](https://pypi.org/project/fastapi-request-logger/)

---

**Star ⭐ this repo if you find it useful!**