# Crosstown Bus Publisher Analysis

### a. Berapa banyak data yang dikirim oleh program publisher ke message broker dalam satu kali jalan?

Dalam satu kali eksekusi, program publisher akan mengirim lima buah pesan `UserCreatedEventMessage` ke message broker. Masing-masing pesan terdiri dari dua field yaitu `user_id` dan `user_name`, yang keduanya bertipe `String`. Ukuran total data yang dikirim tergantung pada panjang string-nya karena protokol Borsh menyimpan string dalam format panjang + isi. Secara kasar, setiap pesan bisa berukuran sekitar 20–50 byte, tergantung kontennya. Jika kita asumsikan tiap pesan rata-rata berukuran 40 byte, maka total data yang dikirim dalam sekali run adalah sekitar 5 x 40 = 200 byte. Ini masih tergolong sangat kecil dan ringan untuk sebuah sistem message queue. Meski demikian, dalam skenario real-world, frekuensi dan volume pesan bisa jauh lebih besar, jadi penting untuk mempertimbangkan efisiensi serialisasi seperti yang dilakukan Borsh.

### b. URL `amqp://guest:guest@localhost:5672` sama seperti yang ada di program subscriber. Apa artinya?

URL `amqp://guest:guest@localhost:5672` adalah endpoint koneksi ke RabbitMQ server yang digunakan baik oleh publisher maupun subscriber. Ini menunjukkan bahwa keduanya terhubung ke broker yang sama, menggunakan kredensial yang sama (username dan password: `guest`). `localhost` berarti server RabbitMQ-nya berjalan di mesin lokal, bukan di server remote. Port `5672` adalah port default untuk protokol AMQP. Dengan menggunakan URL yang sama, publisher dapat mengirim pesan ke broker yang juga bisa dibaca oleh subscriber secara real-time. Ini penting karena menunjukkan bahwa sistem messaging bekerja di atas satu broker pusat. Jika URL-nya berbeda, maka kemungkinan besar publisher dan subscriber tidak akan bisa berkomunikasi satu sama lain karena broker-nya berbeda.

![Image](https://github.com/user-attachments/assets/3ccaf443-068e-4228-a695-770527e1c794)

Pada percobaan ini, program publisher dijalankan untuk mengirim lima pesan UserCreatedEventMessage ke message broker RabbitMQ. Namun, berdasarkan output terminal, yang dieksekusi justru file subscriber.exe, kemungkinan karena konfigurasi di Cargo.toml atau main.rs mengarah ke program subscriber. Setelah program subscriber dijalankan, terlihat bahwa semua pesan berhasil diterima dan dicetak ke layar sebanyak dua kali, menandakan bahwa publisher kemungkinan sempat dijalankan dua kali atau pesan-pesan lama masih tersimpan di antrian RabbitMQ. Hal ini menunjukkan bahwa komunikasi antara publisher dan subscriber sudah berhasil dilakukan melalui AMQP.

![Image](https://github.com/user-attachments/assets/4a7fb57b-096d-4dca-a46e-8a3d51fa58fb)

![Image](https://github.com/user-attachments/assets/ffe1a688-acc0-424f-876d-bdf388b4c9ef)

Saat publisher dijalankan, RabbitMQ menerima pesan dan langsung mengirimkannya ke subscriber tanpa ada yang tertahan di antrian. Grafik message rate menunjukkan lonjakan singkat saat pesan dikirim, lalu kembali nol. Ini menandakan bahwa publisher dan subscriber sudah berjalan dan terhubung dengan baik.

![Image](https://github.com/user-attachments/assets/ab542467-9227-4efd-9465-0f1691159e81)






