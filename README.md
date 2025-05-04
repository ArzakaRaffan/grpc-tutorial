## Module 8 Reflection

### 1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?
1. Unary RPC: Unary menyediakan service dimana klien akan mengirimkan satu request dan server juga membalas dengan satu response saja. Unary cocok digunakan untuk operasi sederhana seperti login, ambil data, dll.
2. Server Streaming: Server Streaming berarti klien mengirimkan satu request dan server mengirim banyak respons secara bertahap. Cocok digunakan untuk notifikasi.
3. Bi-directional Streaming RPC: RPC Method ini idealnya digunakan dalam aplikasi real-time seperti chat, game, dan lain lain karena klien dan server akan saling mengirim pesan secara terus-menerus selama koneksi aktif.

### 2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?
Untuk data encryption, secara default gRPC sudah menyediakan dan mendukung Transport Layer Security (TLS). Sementara untuk otentikasi, gRPC tidak memiliki sistem bawaan sehingga pengguna harus mengimplementasikan beberapa secure authentication seperti menggunakan JWT, API Key, token OAuth, dan lain-lain. Sama halnya dengan otorisasi, pengguna dapat mengimplementasikan Role-based Access Control (RBAC) secara mandiri.

### 3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?
Beberapa tantangan yang dihadapi ketika membuat sebuah gRPC dengan bidirectional streaming adalah munculnya race condition dan juga deadlock sehingga dibutuhkan struktur yang thread-safe seperti menggunakan `Arc<Mutex<T>>` atau `Arc<RwLock<T>>` untuk berbagi data mutable dan `tokio::sync` atau `mpsc` untuk komunikasi antar task. Lalu akan terjadi juga penumpukan buffer bila request terlalu banyak menyebabkan server overload/crash. Dapat dibatasi dengan menggunakan `mpsc(jumlah_channel)`

### 4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?
kelebihan dari `tokio_stream::wrappers::ReceiverStream` adalah integrasi yang mudah antara `tokio::mpsc` dengan gRPC dan kemudahan menyambungkan logika async dengan stream respons. Kekurangannya sendiri adalah `tokio_stream::wrappers::ReceiverStream` berbasis bounded/unbounded channel yang dapat menyebabkan bottleneck jika buffer tidak dikelola dengan baik

### 5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?
Tentunya dengan pemisahan layer yang jelas seperti pemisahan kode antara proto, service, handlers, dll. Kemudian abstraksikan behavior-behavior yang terdapat di dalam project. Untuk menyempurnakan, gunakan design pattern yang sesuai dengan kebutuhan project gRPC rust tersebut.

### 6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?
Untuk meng-handle logic yang lebih kompleks, dapat disertakan beberapa hal seperti validasi data masukan dimana pengguna akan dicek validitasnya, pengecekan nominal, dan lain-lain. Logging dan audit juga penting untuk setiap transaksi guna melacak jika ada kesalahan yang terjadi. Penanganan error juga harus ditambahkan seperti kasus timeout, gagal koneksi, kesalahan otentikasi, dll.

### 7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?
Dampak yang ditimbulkan dari penggunaan gRPC sebagai communication protocol adalah terjadinya efisiensi dimana komunikasi antara client dan server menjadi lebih cepat dan hemat bandwith. Selain itu, gRPC juga mendukung multibahasa dimana protobuf dapat digunakan di rust, python, java, dll. Namun, dampak berupa ketergantungan terhadap HTTP/2 juga muncul dari pengadopsian gRPC sebagai communication protocol ini.

### 8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?
Kelebihan HTTP/2 dibanding HTTP/1.1 + WebSocket untuk REST APIs adalah HTTP/2 memungkinkan lebih banyak request dan respons berjalan paralel tidak seperti HTTP/1.1 yang harus menunggu suatu respons untuk selesai sebelum memulai yang lain. Penggunaan HTTP/2 juga mendukung streaming native dimana gRPC yang dibangun di atas HTTP/2 dapat mendukung client streaming (pengiriman data kepada server terus-menerus), server streaming (server kirim data secara bertahap), dan juga bidirectional streaming (klien dan server saling mengirim data secara bersamaan). Namun kekurangan dari HTTP/2 ini adalah HTTP/2 kurang didukung di browser karena browser tidak dapat membuat request HTTP/2 sehingga membutuhkan gRPC-Web sebagai penghubung antara gRPC dengan web browser

### 9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?
REST API (Request-Response Model) menggunakan pendekatan lama yakni klien mengirim satu permintaan (request), lalu server merespons satu kali juga dimana model ini cocok untuk operasi sederhana dan operasi CRUD. bidirectional streaming yang didukung oleh gRPC mendukung pengiriman data terus-menerus dalam satu koneksi antara server dan klien (dua arah secara bersamaan) dimana ini sangat cocok untuk real-time communication dan responsiveness

### 10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?
gRPC dengan protocol buffers dilengkapi dengan validasi data otomatis karena sudah didefinisikan di `proto` dan payload yang lebih kecil karena menggunakan format biner. Namun proto ini kurang fleksibel karena memerlukan adanya regenerasi kode jika terjadi perubaha skema. Sementara REST API dengan JSON sangat fleksibel dan mudah dimodifikasi tanpa perlu compile ulang. JSON juga sangat mudah dibaca (human-readable) sehingga mudah dalam debugging manual. Namun JSON tidak memiliki validasi otomatis dan harus dilakukan manual serta JSON lebih lambat dan berat karena formatnya teks (bukan biner) dibandingkan dengan protobuf yang menggunakan format biner.



