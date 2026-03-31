# BambangShop Receiver App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create SubscriberRequest model struct.`
    -   [ ] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Notification repository.`
    -   [ ] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Commit: `Implement receive_notification function in Notification service.`
    -   [ ] Commit: `Implement receive function in Notification controller.`
    -   [ ] Commit: `Implement list_messages function in Notification service.`
    -   [ ] Commit: `Implement list function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1

1. Kita ingin menambah data ke dalam Vec (write) dan membaca daftar notifikasi (read) dari berbagai bagian aplikasi. Oleh karena itu, kita butuh mekanisme untuk mencegah terjadinya data race dengan `RwLock` tersebut. Penggunaan `RwLock` disini lebih baik dibandingkan `Mutex` karena Read-Write Lock (atau `RwLock`) mengizinkan banyak reader secara bersamaan selama tidak ada yang menulis. `RwLock` hanya mengunci akses thread lain jika ada yang ingin menulis (writer). Jika kita menggunakan `Mutex`, maka hanya ada satu thread saja yang bisa mengakses data pada satu waktu, baik untuk write maupun read. Akibatnya, jika ada banyak reader yang mau mengakses data tersebut, mereka harus menunggu akses diberikan kepada mereka, padahal operasi yang dilakukan hanya read dan tidak ada perubahan data.

2. Di Rust, variabel static bersifat global. Jika Rust mengizinkan kita mengubah variabel static secara bebas (misalnya `static mut`), maka kemungkinan terjadinya data race akan tinggi pada multi-thread environment. Prinsip yang digunakan oleh Rust adalah "Shared XOR Mutable". Maksud dari prinsip tersebut adalah, kita boleh berbagi data ke banyak pihak (shared) atau mengubah data tersebut (mutable), tetapi tidak keduanya pada saat yang sama. Pada Rust, variabel static juga harus bisa diinisialisasi pada saat compile-time, berbeda dengan Java yang runtime. Tipe data seperti `Vec::new()` dan `DashMap::new()` melakukan alokasi memori pada saat runtime, yang mana bukan nilai konstan. Penggunaan `lazy_static` berguna untuk memecahkan masalah ini dengan cara menunda inisialisasi variabel tersebut sampai pertama kali diakses (lazy initialization). `lazy static` juga memastikan inisialisasi tersebut hanya terjadi satu kali dan thread-safe.

#### Reflection Subscriber-2

1. Ya, saya mencoba untuk memahami beberapa bagian kode dari `src/lib.rs` yang diberikan. Hal-hal yang saya pelajari dari bagian kode tersebut:
- Manajemen resource menggunakan `lazy_static` pada `REQWEST_CLIENT` dan `APP_CONFIG`. Keuntungan yang diberikan adalah penerapan Singleton Pattern dan akses global yang aman.
- Konfigurasi aplikasi menggunakan Figment dan Dotenv. Pada bagian `AppConfig::generate()`, terdapat penggabungan nilai `default()` untuk fallback, file `.env` (`dotenvy`), dan environment variable pada sistem (`Env::prefixed("APP_")`). Selain itu, environment variable dengan awalan `APP_` akan overwrite nilai default. Konfigurasi ini akan berguna pada saat deployment, dimana kita bisa mengubah URL tanpa menyentuh kode program.
- Standarisasi error handling dengan mendefinisikan `ErrorReponse` dan fungsi `compose_error_response`. Dengan standarisasi ini, pesan error yang dikirimkan ke user akan memiliki format JSON yang sama yaitu mengandung `status_code` dan `message`. Selain itu, ada juga type aliasing `pub type Result<T, E = Error>` yang menyederhanakan penulisan `std::result::Result<T, Custom<Json<ErrorResponse>>>` menjadi `Result<T>`.
- Penggunaan Getters agar Rust secara otomatis menyediakan fungsi untuk mendapatkan nilai tertentu (Getter)
- Penggunaan Serde (Serialize and Deserialize) untuk mengubah data menjadi JSON dan sebaliknya yang merupakan standar pada framework Rocket.

2. Observer Pattern memudahkan penambahan Subscriber baru karena adanya decoupling (pemisahan ketergantungan). Main app (Publisher) tidak perlu tahu bagaimana Receiver bekerja. Selama Receiver memiliki endpoint HTTP yang sesuai, kita bisa menambahkan instance Receiver dengan mudah tanpa perlu perubahan kode di Main (Publisher) app. Masalah yang akan muncul jika ada lebih dari satu instance Main app adalah daftar Subscriber saat ini disimpan di dalam `DashMap`. Misalkan ada dua instance Main app yaitu instance A dan B, instance A tidak akan tahu Subscriber yang ada pada instance B, begitupun sebaliknya. Untuk melakukan sinkronisasi data, kita bisa menggunakan database eksternal (misal PostgreSQL) yang bisa diakses oleh semua instance Main app. Database ini akan menjadi tempat penyimpanan data-data Subscriber, menggantikan `DashMap`.

3. Saya sudah pernah mencoba melakukan testing di Postman. Fitur Tests bisa dilakukan dengan membuat JavaScript yang berjalan otomatis setelah melakukan request. Script tersebut bisa mengecek response body secara otomatis. Pengecekan ini mencakup validasi status code dan pengecekan struktur JSON. Selain itu, kita bisa menggunakan Collection Runner untuk melakukan beberapa request beruntun, misalnya adalah subscribe ke suatu produk (Request 1) lalu melakukan trigger notifikasi di Publisher (Request 2).
