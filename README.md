2025/3/24
window iocp model complied by c++11/14

[NOTE](NOTE.md)


这段代码是一个基于IOCP（Input/Output Completion Port，输入/输出完成端口）模型的网络服务器程序，主要用于处理大量的并发网络连接。IOCP是Windows提供的一种高效的异步I/O模式，适用于高并发的网络编程。下面是对代码主要部分的解释：

### 类和成员变量
- `CIOCP`：主类，负责IOCP的初始化、运行和停止。
- `CIOContext`：表示一个I/O操作上下文，包含操作类型、套接字、缓冲区等。
- `CSocketContext`：表示一个套接字上下文，包含套接字、地址和I/O上下文池。

### 构造函数和析构函数
- `CIOCP::CIOCP()`：初始化成员变量，设置线程数。
- `CIOCP::~CIOCP()`：调用`stop()`函数停止服务器，释放资源。

### 主要函数
- `bool CIOCP::run(int nPort)`：启动服务器，监听指定端口。包括初始化IOCP、启动工作线程、创建监听套接字等步骤。
- `void CIOCP::stop()`：停止服务器，关闭所有套接字和线程，释放资源。
- `bool CIOCP::SendData(sp_socket_wptr& sp_sockContext, char* data, int nSize)`：向指定套接字发送数据。
- `void CIOCP::SetCallBack()`：设置回调函数，用于处理连接建立、接收完成、发送完成、连接关闭和连接错误等事件。
- `bool CIOCP::DoInitIOCP()`：初始化IOCP，创建完成端口并启动工作线程。
- `bool CIOCP::DoListenSocket(int nPort)`：创建监听套接字，并绑定到IOCP。
- `bool CIOCP::DoAccpet(sp_socket_wptr& sp_listenContext, sp_iocontext_wptr& sp_ioContext)`：处理接受新连接的请求。
- `bool CIOCP::DoRecv(sp_socket_wptr& sp_sockContext, sp_iocontext_wptr& sp_ioContext)`：处理接收数据的请求。
- `bool CIOCP::DoSend(sp_socket_wptr& sp_sockContext, sp_iocontext_wptr& sp_ioContext)`：处理发送数据的请求。
- `bool CIOCP::SetSocketOpt(SOCKET_TYPE s)`：设置套接字选项。
- `bool CIOCP::RemoveClientContext(sp_socket_wptr& sp_sockContext)`：移除客户端上下文。
- `bool CIOCP::RelateIOCP(sp_socket_wptr& sp_sockContext)`：将套接字关联到IOCP。
- `bool CIOCP::GetLocalHostName(std::string& strHost)`：获取本地主机名。
- `bool CIOCP::DoBind(SOCKET_TYPE s, sockaddr* addr, int namelen)`：将套接字绑定到指定地址。
- `bool CIOCP::DoListen(SOCKET_TYPE s, int backlog)`：使套接字进入监听状态。
- `void CIOCP::GetSockadrInfo(sockaddr* ss, std::string& ipaddr, int& nPort)`：获取套接字的IP地址和端口号。
- `bool CIOCP::PostAccept(sp_socket_wptr& sp_listenContext, sp_iocontext_wptr& sp_ioContext)`：投递接受连接的请求。
- `bool CIOCP::PostRecv(sp_socket_wptr& sp_listenContext, sp_iocontext_wptr& sp_ioContext)`：投递接收数据的请求。
- `bool CIOCP::PostSend(sp_socket_wptr& sp_socketContext, sp_iocontext_wptr& sp_ioContext)`：投递发送数据的请求。
- `bool CIOCP::IsSocketAlive(SOCKET sock)`：检查套接字是否存活。
- `bool CIOCP::HandleIoError(sp_socket_wptr& sp_sockContext,int dwErr)`：处理I/O错误。
- `void CIOCP::HandleIoType(sp_socket_wptr& sp_sockContext, sp_iocontext_wptr& sp_ioContext, IO_TYPE nType)`：根据I/O类型处理不同的I/O请求。
- `bool CIOCP::DoInitStart()`：初始化网络库。
- `DWORD CIOCP::WorkerThreadProc(LPVOID lpParam)`：工作线程函数，处理IOCP的完成请求。

 
1. **线程安全**：代码中使用了互斥锁（`std::mutex`）和条件变量（`std::condition_variable`）来保证线程安全，特别是在处理客户端上下文和I/O上下文时。
2. **资源管理**：使用了智能指针（`std::shared_ptr`和`std::weak_ptr`）来管理资源，避免内存泄漏。
3. **错误处理**：在网络编程中，错误处理非常重要，代码中通过检查函数返回值和错误码来处理各种错误情况。
4. **回调函数**：通过设置回调函数来处理不同的网络事件，使代码更加模块化和可扩展。
5. **IOCP模型**：利用Windows的IOCP模型实现高效的网络I/O，适用于高并发场景。

 ![image](https://github.com/user-attachments/assets/0cff1f45-1ba5-4895-9dc2-a064bdce85fc)

 2025/3/26  更新套接字上下文使用缓存池
 2025/3/28  更新使用无锁队列

