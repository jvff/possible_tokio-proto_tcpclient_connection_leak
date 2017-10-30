# Possible `tokio-proto::TcpClient` connection leak

This is a simple test scenario that shows what appears to be a resource leak when using `tokio_proto::TcpClient`.

Run `cargo test` to see that one test succeeds while the other fails. The one that succeeds drops the `tokio_core::reactor::Core` before the test assertion, while the one that fails drops it at the end of the program.

## Brief description of test

The test is rather simple. An "echo server" is spawned in a separate thread, which receives a single connection. When that connection is closed, it flags a boolean value that it has finished. The test is simply to connect to the "echo server" and see if the the server has finished (meaning the connection has closed). The connection should be closed when the `send_message` function ends, because thats where the `TcpClient` lives (i.e., its lifetime is bound to that function's scope), and the `send_message` function doesn't return anything, so it shouldn't leak anything related to the connection by moving something out of the function through the return type.
