# BBS Web Server

A lightweight, barebones web server implementation written in C++ using Windows Sockets (Winsock2). This project demonstrates basic HTTP server functionality and TCP socket programming concepts.

## 🚀 Features

- **HTTP/1.1 Support**: Handles basic GET requests
- **Static File Serving**: Serves HTML files from the `wwwroot` directory
- **TCP Socket Communication**: Built on top of a custom TCP listener class
- **Multi-client Support**: Can handle multiple simultaneous connections
- **Error Handling**: Returns appropriate HTTP status codes (200 OK, 404 Not Found)
- **Automatic Index Serving**: Serves `index.html` when accessing the root path (`/`)

## 📁 Project Structure

```
bbs web server/
├── main.cpp                    # Entry point of the application
├── WebServer.h/.cpp           # Web server implementation (HTTP layer)
├── TcpListener.h/.cpp         # TCP socket listener base class
├── MultiClientChat.h/.cpp     # Multi-client chat functionality
├── wwwroot/                   # Web content directory
│   ├── index.html            # Default homepage
│   └── foo.html              # Sample HTML page
├── bbs web server.sln        # Visual Studio solution file
└── bbs web server.vcxproj    # Visual Studio project file
```

## 🛠️ Building the Project

### Prerequisites

- **Visual Studio 2019/2022** with C++ development tools
- **Windows SDK** (for Winsock2 support)
- **Windows 10/11** (tested environment)

### Build Steps

1. **Open the Solution**:
   ```bash
   # Open Visual Studio and load the solution
   bbs web server.sln
   ```

2. **Build Configuration**:
   - Set build configuration to `Debug` or `Release`
   - Set platform to `x64`

3. **Build the Project**:
   - Press `Ctrl+Shift+B` or go to `Build > Build Solution`
   - The executable will be generated in `x64/Debug/` or `x64/Release/`

### Command Line Build (Alternative)

```cmd
# Using MSBuild (requires Visual Studio Build Tools)
msbuild "bbs web server.sln" /p:Configuration=Debug /p:Platform=x64
```

## 🚀 Running the Server

### Direct Execution

1. **Navigate to the executable directory**:
   ```cmd
   cd "x64\Debug"
   ```

2. **Run the server**:
   ```cmd
   "bbs web server.exe"
   ```

3. **Server will start on**:
   - **IP Address**: `0.0.0.0` (all interfaces)
   - **Port**: `8080`

### Testing the Server

Open your web browser and navigate to:
- `http://localhost:8080` - Serves the default index.html
- `http://localhost:8080/foo.html` - Serves the foo.html page
- `http://localhost:8080/nonexistent.html` - Returns 404 error

## 🔧 Using PuTTY for Testing

PuTTY can be used to test the raw TCP/HTTP functionality of the server by sending manual HTTP requests.

### Setting Up PuTTY Connection

1. **Download and Install PuTTY**:
   - Download from [https://www.putty.org/](https://www.putty.org/)
   - Install or use the portable version

2. **Configure Raw TCP Connection**:
   - **Host Name**: `localhost` (or `127.0.0.1`)
   - **Port**: `8080`
   - **Connection Type**: Select `Raw`
   - **Terminal Settings**: 
     - Go to `Terminal` → Check `Implicit CR in every LF`
     - Go to `Terminal` → Check `Implicit LF in every CR`

3. **Connect to the Server**:
   - Click `Open` to establish the connection
   - You should see a blank terminal window (this is normal)

### Sending HTTP Requests via PuTTY

Once connected, you can type HTTP requests manually:

#### Example 1: Request Homepage
```http
GET / HTTP/1.1
Host: localhost:8080

```
**Note**: Press `Enter` twice after the `Host` line to send the request.

**Expected Response**:
```http
HTTP/1.1 200 OK
Cache-Control: no-cache, private
Content-Type: text/html
Content-Length: [content-length]

<!DOCTYPE html>
<html>
...
```

#### Example 2: Request Specific File
```http
GET /foo.html HTTP/1.1
Host: localhost:8080

```

#### Example 3: Test 404 Error
```http
GET /nonexistent.html HTTP/1.1
Host: localhost:8080

```

**Expected Response**:
```http
HTTP/1.1 404 OK
Cache-Control: no-cache, private
Content-Type: text/html
Content-Length: 27

<h1>404 Not Found</h1>
```

### PuTTY Session Configuration Tips

1. **Save Session**: Save your configuration as "BBS Web Server Test" for quick access
2. **Logging**: Enable session logging (`Session` → `Logging`) to capture server responses
3. **Terminal Settings**: 
   - Set `Local echo` to `Force on` to see what you're typing
   - Set `Local line editing` to `Force on` for better editing experience

### Advanced Testing with PuTTY

#### Testing Keep-Alive Connections
```http
GET / HTTP/1.1
Host: localhost:8080
Connection: keep-alive

```

#### Testing with Custom Headers
```http
GET /index.html HTTP/1.1
Host: localhost:8080
User-Agent: PuTTY-Test/1.0
Accept: text/html

```

## 🔍 Code Overview

### Core Components

#### `main.cpp`
- Entry point that initializes the web server on `0.0.0.0:8080`
- Calls the server initialization and run methods

#### `WebServer` Class
- Inherits from `TcpListener`
- Implements HTTP request parsing and response generation
- Handles file serving from the `wwwroot` directory
- Generates appropriate HTTP headers and status codes

#### `TcpListener` Class
- Base class providing TCP socket functionality
- Manages client connections using `select()` for multiplexing
- Provides virtual methods for handling client events

### HTTP Request Flow

1. **Client connects** → `onClientConnected()` is called
2. **Client sends HTTP request** → `onMessageReceived()` parses the request
3. **Server processes request**:
   - Extracts the requested file path
   - Attempts to open file from `wwwroot` directory
   - Generates HTTP response with appropriate status code
4. **Server sends response** → Client receives HTML content or error page
5. **Client disconnects** → `onClientDisconnected()` is called

## 🎯 Usage Examples

### Adding New Web Pages

1. Create new HTML files in the `wwwroot` directory
2. Access them via `http://localhost:8080/filename.html`

### Testing with curl (Alternative to PuTTY)

```bash
# Basic GET request
curl -v http://localhost:8080/

# Request specific file
curl -v http://localhost:8080/foo.html

# Test 404 error
curl -v http://localhost:8080/missing.html
```

## 🐛 Troubleshooting

### Common Issues

1. **Port Already in Use**:
   - Error: `bind failed with error`
   - Solution: Change port in `main.cpp` or kill process using port 8080

2. **Files Not Found**:
   - Ensure `wwwroot` directory is in the same location as the executable
   - Check file paths and permissions

3. **Connection Refused**:
   - Verify server is running
   - Check Windows Firewall settings
   - Ensure correct IP address and port

### Debug Mode

When running in Debug mode, you can:
- Set breakpoints in Visual Studio
- Monitor socket connections
- Inspect HTTP request parsing

## 🔮 Future Enhancements

- **POST Request Support**: Handle form submissions
- **MIME Type Detection**: Serve different content types (CSS, JS, images)
- **Directory Listing**: Show directory contents when no index file exists
- **Configuration File**: External configuration for port, root directory, etc.
- **SSL/TLS Support**: HTTPS implementation
- **Logging System**: Request/response logging to files
- **Authentication**: Basic HTTP authentication support

## 📝 License

This project is provided as-is for educational purposes. Feel free to modify and distribute according to your needs.

## 🤝 Contributing

This is a learning project, but contributions and improvements are welcome! Feel free to:
- Submit bug reports
- Suggest enhancements
- Add new features
- Improve documentation

---

**Happy coding! 🚀**