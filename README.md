# Module 8 - High Level Networking

## Reflections

1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

    Unary RPC is a simple request-response model where the client sends a single request and receives a single response from the server. This is suitable for scenarios where the interaction is straightforward and does not require continuous data exchange, such as fetching user details, getting an item from a database, or submitting a form.

    Server streaming RPC allows the server to send multiple responses to a single client request. This is useful for scenarios where the client needs to receive a stream of data, such as real-time updates or large file transfers.

    Bi-directional streaming RPC enables both the client and server to send and receive streams of messages. This is ideal for scenarios like chat applications or collaborative editing, where real-time, two-way communication is required.

2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

    While gRPC has built-in support for authentication to enhance security, implementing it in Rust requires attention to detail. For authentication, developers can use TLS (Transport Layer Security) to encrypt data in transit and ensure that only authorized clients can access the service. Additionally, implementing token-based authentication (e.g., JWT) can help manage user sessions and permissions effectively. Authorization can be handled by defining roles and permissions within the service, ensuring that users only have access to the resources they are authorized to interact with. Data encryption is crucial for protecting sensitive information, and using TLS for secure communication is a standard practice. Developers should also consider implementing proper error handling and logging to monitor and respond to potential security threats effectively.

3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

    Handling bidirectional streaming requires managing asynchronous concurrency carefully. As seen in the tutorial, using `tokio::spawn` and `mpsc` channels introduces complexities like managing channel buffer limits to prevent memory overflow, handling dropped network connections gracefully without crashing the server, and ensuring thread safety when sharing state across multiple active chat streams.

4. What are the advantages and disadvantages of using the `tokio_stream::wrappers::ReceiverStream` for streaming responses in Rust gRPC services?

    The advantage of using `tokio_stream::wrappers::ReceiverStream` is that it provides a convenient way to convert an `mpsc::Receiver` into a stream that can be used in gRPC responses. This allows for easy integration of asynchronous message passing with gRPC's streaming capabilities. However, a disadvantage of this approach is that it tightly couples your stream to a bounded channel (e.g., buffer size of 10). If the buffer fills up because the client is slow to consume messages, it can block the async producer tasks or cause message drops.

5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

    The gRPC code can be structured using a layered architecture, separating concerns into different modules or layers. The Protobuf code (proto layer), the gRPC transport and routing handlers (Tonic layer), and the core business logic can be separated into distinct modules. This allows for better code reuse, as the business logic can be tested independently of the gRPC transport layer. Additionally, using traits and interfaces can help abstract away implementation details, making it easier to swap out components or add new features without affecting existing code. Dependency injection can also be used to manage dependencies and promote loose coupling between components, further enhancing maintainability and extensibility.

6. In the `MyPaymentService` implementation, what additional steps might be necessary to handle more complex payment processing logic?

    The current `MyPaymentService` immediately returns a hardcoded successful result. In a real-world application, additional steps would include validating the payment request (e.g., checking for valid payment methods, ensuring sufficient funds), integrating with external payment gateways or services, implementing database transactions to ensure atomicity, handling various edge cases (e.g., network failures, timeouts), and implementing proper error handling and logging. Additionally, it may be necessary to implement retry logic for transient errors and to ensure that sensitive information is handled securely throughout the payment processing workflow.

7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

    gRPC natively supports generating client and server code across multiple programming languages. This heavily impacts distributed systems by making polyglot microservice architectures much easier to build and maintain, as the strict Protobuf contract guarantees interoperability. However, as for 2026, it is not natively supported (yet) by all web browsers, requiring additional workarounds such as API gateways.

8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

    HTTP/2 supports request/response multiplexing over a single TCP connection, eliminating the head-of-line blocking found in HTTP/1.1. It also introduces binary framing and header compression (HPack) to drastically reduce payload sizes. But, it is more complex to implement and harder to debug manually with simple network tools compared to plain-text HTTP/1.1.

9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

    REST strictly follows a unary request/response cycle, meaning it relies on workarounds like constant client polling or separate WebSocket integrations to achieve true real-time updates. gRPC handles real-time communication natively through HTTP/2's continuous bidirectional streaming, making it significantly more responsive and efficient for live data.

10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

    Protobuf encodes data into a strict, binary format, meaning it automatically validates messages against an API contract and uses far fewer CPU resources to parse. This makes it extremely fast and lightweight. JSON is schema-less, plain-text, and easy for humans to read, but requires extra manual validation steps and is inherently slower for machines to parse. In other world, while JSON is made for humans, Protobuf is made for machines.
