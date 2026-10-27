# 06. Sequence Diagram — Sistem Informasi PT Alexander Cipta Karya

Dokumen ini memuat seluruh pemodelan interaksi antar objek (*Sequence Diagram*) sistem informasi PT Alexander Cipta Karya yang dirancang berdasarkan skenario utama (*main flow*) dan skenario eksepsional (*alternative flow*) pada Bab 3.2 dokumen SRS (SKPL-BUBUT halaman 18–32), diimplementasikan dalam arsitektur **MVC Laravel + Database MySQL**.

Terdapat 7 Sequence Diagram lengkap yang selaras 100% dengan Use Case dan Activity Diagram:
1. **SD-01: Melakukan Login (SRS 3.2.1)**
2. **SD-02: Melakukan CRUD Work Order & Auto Generate QR di Awal (SRS 3.2.2)**
3. **SD-03: Mengelola Progress (SRS 3.2.3)** *(Tanpa generate QR saat update progres)*
4. **SD-04: Melihat Progress / Pelanggan Tracking (SRS 3.2.4)**
5. **SD-05: Mencari Work Order (SRS 3.2.5)**
6. **SD-06: Mengenerate QR Code — Inklusi Otomatis Work Order (SRS 3.2.6)** *(1 kali di awal)*
7. **SD-07: Membuat Nota Pembayaran (SRS 3.2.7)**

> **Tips Import ke Draw.io:**
> Salin masing-masing blok kode Mermaid di bawah ini, lalu pada draw.io pilih menu **Arrange** > **Insert** > **Advanced** > **Mermaid**. File satuan mandiri juga tersedia di direktori `diagrams/sequence/`.

---

## 1. SD-01: Melakukan Login (SRS 3.2.1)

Memodelkan aliran autentikasi peran (*role-based*) untuk Admin, Marketing, dan Produksi, mencakup skenario berhasil dan gagal.

```mermaid
sequenceDiagram
    autonumber
    actor Pengguna as Pengguna<br/>(Admin/Mkt/Prod)
    participant LoginUI as View: Login Page
    participant AuthCtrl as Controller: AuthController
    participant UserModel as Model: User
    participant DB as Database (MySQL)
    participant DashUI as View: Dashboard

    Pengguna->>LoginUI: 1. Buka halaman login
    LoginUI-->>Pengguna: 2. Tampilkan form input kredensial
    Pengguna->>LoginUI: 3. Masukkan username & password, klik tombol Login
    LoginUI->>AuthCtrl: 4. submitLogin(username, password)
    AuthCtrl->>UserModel: 5. findByUsername(username)
    UserModel->>DB: 6. SELECT * FROM users WHERE username = ?
    DB-->>UserModel: 7. Return data pengguna / null
    UserModel-->>AuthCtrl: 8. Objek User / null

    alt Kredensial Valid & Password Cocok
        AuthCtrl->>AuthCtrl: 9. Set session pengguna & identifikasi role
        AuthCtrl-->>DashUI: 10. Redirect ke dashboard sesuai role
        DashUI-->>Pengguna: 11. Tampilkan halaman Dashboard (Admin/Mkt/Prod)
    else Kredensial Salah / Tidak Ditemukan
        AuthCtrl-->>LoginUI: 9b. Return response gagal otentikasi
        LoginUI-->>Pengguna: 10b. Tampilkan pesan kesalahan & minta input ulang
    end
```

---

## 2. SD-02: Melakukan CRUD Work Order & Auto Generate QR di Awal (SRS 3.2.2)

Memodelkan siklus hidup Work Order oleh Admin/Marketing yang mencakup penambahan (*Create*) dengan **pembuatan QR Code 1 kali di awal**, penampilan detail (*Read*), pengubahan (*Update*), dan penghapusan (*Delete*).

```mermaid
sequenceDiagram
    autonumber
    actor Staf as Admin / Marketing
    participant WOUI as View: WorkOrderPage
    participant WOCtrl as Controller: WorkOrderController
    participant QRService as Service: QRCodeGenerator
    participant FileStorage as Server File Storage
    participant WOModel as Model: WorkOrder
    participant BarangModel as Model: Barang
    participant DB as Database (MySQL)

    %% Aksi Menampilkan Daftar
    Staf->>WOUI: 1. Buka menu Work Order
    WOUI->>WOCtrl: 2. index()
    WOCtrl->>WOModel: 3. getAllWorkOrders()
    WOModel->>DB: 4. SELECT * FROM work_orders ORDER BY created_at DESC
    DB-->>WOModel: 5. Dataset Work Orders
    WOModel-->>WOCtrl: 6. Return daftar WO
    WOCtrl-->>WOUI: 7. Render tabel Work Order
    WOUI-->>Staf: 8. Tampilkan daftar Work Order

    %% Sub-skenario: Tambah Data Baru & Generate QR Code di Awal
    opt Aksi Tambah Work Order (Create) & Auto Generate QR Code di Awal
        Staf->>WOUI: 9. Klik tombol "Tambah Work Order"
        WOUI-->>Staf: 10. Tampilkan form WO kosong
        Staf->>WOUI: 11. Input data pelanggan & barang, klik Simpan
        WOUI->>WOCtrl: 12. store(Request $request)
        WOCtrl->>WOCtrl: 13. Validasi kelengkapan form
        WOCtrl->>WOModel: 14. create(data_wo)
        WOModel->>DB: 15. INSERT INTO work_orders (...)
        WOCtrl->>BarangModel: 16. create(data_barang)
        BarangModel->>DB: 17. INSERT INTO barang (...)
        DB-->>WOCtrl: 18. Commit transaksi sukses (id_wo terbit)

        %% Auto Generate QR Code 1 Kali di Awal Pembuatan
        WOCtrl->>QRService: 19. generateQRCode(id_wo)
        QRService->>FileStorage: 20. Simpan file gambar QR unik (.png)
        FileStorage-->>QRService: 21. Return qr_image_path
        QRService-->>WOCtrl: 22. Return token_qr & qr_image_path
        WOCtrl->>WOModel: 23. updateQRCode(id_wo, qr_image_path, token_qr)
        WOModel->>DB: 24. UPDATE work_orders SET qr_code_path = ?, qr_token = ? WHERE id = id_wo
        DB-->>WOCtrl: 25. QR Code berhasil disimpan & ditautkan ke database pesanan

        WOCtrl-->>WOUI: 26. Flash session notifikasi sukses & kirim data QR Code
        WOUI-->>Staf: 27. Tampilkan data baru pada tabel WO beserta opsi cetak / unduh QR Code
    end

    %% Sub-skenario: Ubah Data
    opt Aksi Ubah Work Order (Update)
        Staf->>WOUI: 28. Pilih baris data & klik "Ubah"
        WOUI->>WOCtrl: 29. edit(id_wo)
        WOCtrl-->>WOUI: 30. Kirim data WO & form edit
        Staf->>WOUI: 31. Ubah atribut & klik Simpan
        WOUI->>WOCtrl: 32. update(id_wo, Request $request)
        WOCtrl->>WOModel: 33. update(data_perubahan)
        WOModel->>DB: 34. UPDATE work_orders SET ... WHERE id = id_wo
        DB-->>WOCtrl: 35. Status pembaruan berhasil
        WOCtrl-->>WOUI: 36. Tampilkan notifikasi perubahan berhasil
    end

    %% Sub-skenario: Hapus Data
    opt Aksi Hapus Work Order (Delete)
        Staf->>WOUI: 37. Pilih data & klik "Hapus"
        WOUI-->>Staf: 38. Tampilkan dialog konfirmasi hapus
        Staf->>WOUI: 39. Klik tombol "Ya, Konfirmasi Hapus"
        WOUI->>WOCtrl: 40. destroy(id_wo)
        WOCtrl->>WOModel: 41. delete(id_wo)
        WOModel->>DB: 42. DELETE FROM work_orders WHERE id = id_wo
        DB-->>WOCtrl: 43. Status hapus berhasil
        WOCtrl-->>WOUI: 44. Refresh tabel & tampilkan notifikasi berhasil
    end
```

---

## 3. SD-03: Mengelola Progress (SRS 3.2.3)

Memodelkan pembaruan tahapan pengerjaan oleh tim Produksi, penyimpanan dokumen bukti foto, dan penyelesaian status pengerjaan. **Proses ini tidak mengenerate QR Code baru**, karena QR Code pelacakan telah dibuat satu kali di awal saat Work Order dibuat.

```mermaid
sequenceDiagram
    autonumber
    actor Prod as Bagian Produksi
    participant ProgUI as View: ProgresPage
    participant ProgCtrl as Controller: ProgresController
    participant FileStorage as Server File Storage
    participant ProgModel as Model: ProgresPengerjaan
    participant WOModel as Model: WorkOrder
    participant DB as Database (MySQL)

    Prod->>ProgUI: 1. Pilih Work Order yang sedang dikerjakan
    ProgUI->>ProgCtrl: 2. getDetail(id_wo)
    ProgCtrl-->>ProgUI: 3. Tampilkan detail pesanan & form update progres
    Prod->>ProgUI: 4. Input keterangan tahapan kerja & unggah berkas foto pengerjaan
    Prod->>ProgUI: 5. Klik tombol "Simpan Progres"
    ProgUI->>ProgCtrl: 6. storeProgres(id_wo, file, status, keterangan)
    
    ProgCtrl->>ProgCtrl: 7. Validasi format gambar (JPG/PNG, ukuran maks)
    ProgCtrl->>FileStorage: 8. Simpan berkas foto ke storage server
    FileStorage-->>ProgCtrl: 9. Return file_path dokumentasi
    
    ProgCtrl->>ProgModel: 10. insertProgres(id_wo, status, file_path, keterangan)
    ProgModel->>DB: 11. INSERT INTO progres_pengerjaan (id_wo, status, foto_path, keterangan, created_at)
    DB-->>ProgModel: 12. Record progres tersimpan
    ProgModel-->>ProgCtrl: 13. Return status sukses
    
    ProgCtrl-->>ProgUI: 14. Tampilkan notifikasi: "Data progres dan dokumentasi berhasil diperbarui"
    ProgUI-->>Prod: 15. Tampilkan riwayat progres pengerjaan terbaru
    
    opt Konfirmasi Pengerjaan Selesai (Finish)
        Prod->>ProgUI: 16. Klik "Selesaikan Pekerjaan"
        ProgUI-->>Prod: 17. Tampilkan konfirmasi dialog
        Prod->>ProgUI: 18. Klik "Ya, Pekerjaan Selesai"
        ProgUI->>ProgCtrl: 19. markAsComplete(id_wo)
        ProgCtrl->>WOModel: 20. updateStatus(id_wo, 'Selesai')
        WOModel->>DB: 21. UPDATE work_orders SET status = 'Selesai' WHERE id = id_wo
        DB-->>WOModel: 22. Status berhasil diperbarui
        WOModel-->>ProgCtrl: 23. Konfirmasi update status
        ProgCtrl-->>ProgUI: 24. Tampilkan notifikasi status pengerjaan telah selesai
        ProgUI-->>Prod: 25. Perbarui status Work Order menjadi "Selesai"
    end
```

---

## 4. SD-04: Melihat Progress (Pelanggan / Tracking Mandiri) (SRS 3.2.4)

Memodelkan pelacakan mandiri oleh pelanggan menggunakan pemindaian QR Code tracking kamera ponsel tanpa perlu melakukan login kredensial.

```mermaid
sequenceDiagram
    autonumber
    actor Pelanggan as Pelanggan / Customer
    participant Kamera as Kamera / Browser HP
    participant TrackUI as View: TrackingPublicPage
    participant TrackCtrl as Controller: TrackingController
    participant ProgModel as Model: ProgresPengerjaan
    participant DB as Database (MySQL)

    Pelanggan->>Kamera: 1. Pindai (scan) QR Code pada lembar pesanan/nota
    Kamera->>TrackUI: 2. Buka URL: /tracking?token=QR_CODE_STRING
    TrackUI->>TrackCtrl: 3. showPublicProgress(qr_token)
    TrackCtrl->>ProgModel: 4. findProgressByToken(qr_token)
    ProgModel->>DB: 5. SELECT p.*, w.* FROM progres_pengerjaan p JOIN work_orders w ON ... WHERE qr_token = ?
    DB-->>ProgModel: 6. Return data progres atau null
    ProgModel-->>TrackCtrl: 7. Objek Progress Data / null

    alt Data Progres Ditemukan & Tersedia
        TrackCtrl-->>TrackUI: 8. Render informasi progres, foto bengkel, & status terkini
        TrackUI-->>Pelanggan: 9. Menampilkan riwayat pengerjaan barang secara transparan
    else Data Belum Tersedia / Kode Tidak Dikenali
        TrackCtrl-->>TrackUI: 8b. Return status 404 / informasi kosong
        TrackUI-->>Pelanggan: 9b. Tampilkan pesan: "Informasi progres belum tersedia"
    end
```

---

## 5. SD-05: Mencari Work Order (SRS 3.2.5)

Memodelkan proses pencarian data pesanan secara cepat menggunakan nomor ID Work Order atau Nama Pelanggan.

```mermaid
sequenceDiagram
    autonumber
    actor Staf as Admin / Marketing
    participant WOUI as View: WorkOrderPage
    participant WOCtrl as Controller: WorkOrderController
    participant WOModel as Model: WorkOrder
    participant DB as Database (MySQL)

    Staf->>WOUI: 1. Masukkan kata kunci pada kolom pencarian (ID WO / Nama Pelanggan)
    Staf->>WOUI: 2. Tekan Enter / klik ikon cari
    WOUI->>WOCtrl: 3. search(keyword)
    WOCtrl->>WOModel: 4. searchWorkOrders(keyword)
    WOModel->>DB: 5. SELECT * FROM work_orders WHERE id_wo LIKE %key% OR nama_pelanggan LIKE %key%
    DB-->>WOModel: 6. Hasil query data (List / Empty)
    WOModel-->>WOCtrl: 7. Kumpulan record yang cocok

    alt Data Ditemukan
        WOCtrl-->>WOUI: 8. Kirim kumpulan record yang relevan
        WOUI-->>Staf: 9. Tampilkan baris data Work Order yang cocok di tabel
    else Data Tidak Ditemukan
        WOCtrl-->>WOUI: 8b. Kirim respon kosong
        WOUI-->>Staf: 9b. Tampilkan pesan peringatan: "Data tidak ditemukan"
    end
```

---

## 6. SD-06: Mengenerate QR Code — Inklusi Otomatis Work Order (SRS 3.2.6)

Memodelkan proses otomatis sistem (*system-triggered include*) dalam menghasilkan kode matriks 2D (QR Code) unik untuk pelacakan, yang dieksekusi **satu kali di awal saat data Work Order baru berhasil dibuat**. Sesuai diagram alur Draw.io:
1. Sistem Mendeteksi Work Order Berhasil Dibuat
2. Sistem Mengambil Nomor ID Work Order yang Sedang Diproses
3. Sistem Mengenerate Gambar QR Code Untuk Terhubung ke ID WO
4. Sistem Menyimpan & Menautkan QR Code ke Database Pesanan
5. Sistem Menyediakan QR Code Untuk Dilihat / Diunduh

```mermaid
sequenceDiagram
    autonumber
    actor Staf as Admin / Marketing
    participant WOUI as View: WorkOrderPage
    participant WOCtrl as Controller: WorkOrderController
    participant QRService as Service: QRCodeGenerator
    participant FileStorage as Server File Storage
    participant WOModel as Model: WorkOrder
    participant DB as Database (MySQL)

    Note over Staf, DB: Dipicu 1 Kali di Awal Saat Work Order Berhasil Dibuat (SRS 3.2.6)
    
    Staf->>WOUI: 1. Input data pesanan & submit form Work Order
    WOUI->>WOCtrl: 2. store(Request $request)
    WOCtrl->>WOModel: 3. create(data_wo)
    WOModel->>DB: 4. INSERT INTO work_orders (...)
    DB-->>WOCtrl: 5. Work Order berhasil tersimpan (ID WO terbit)
    
    %% Alur Sesuai Flowchart Draw.io
    WOCtrl->>WOCtrl: 6. Sistem mendeteksi Work Order berhasil dibuat
    WOCtrl->>WOCtrl: 7. Sistem mengambil nomor ID Work Order (id_wo) yang sedang diproses
    
    WOCtrl->>QRService: 8. generateTrackingQR(id_wo, tracking_url)
    QRService->>QRService: 9. Sistem mengenerate gambar QR Code untuk terhubung ke ID WO
    QRService->>FileStorage: 10. Simpan file gambar matriks QR (.png) ke direktori storage
    FileStorage-->>QRService: 11. Return qr_image_path
    QRService-->>WOCtrl: 12. Return token_unik & qr_image_path
    
    WOCtrl->>WOModel: 13. updateQRCode(id_wo, qr_image_path, token_unik)
    WOModel->>DB: 14. UPDATE work_orders SET qr_code_path = ?, qr_token = ? WHERE id = id_wo
    DB-->>WOCtrl: 15. Sistem menyimpan & menautkan QR Code ke database pesanan
    
    WOCtrl-->>WOUI: 16. Respon sukses pendaftaran pesanan & payload data QR Code
    WOUI-->>Staf: 17. Sistem menyediakan QR Code untuk dilihat / diunduh / dicetak
```

---

## 7. SD-07: Membuat Nota Pembayaran (SRS 3.2.7)

Memodelkan pemeriksaan kelengkapan biaya oleh Admin, kalkulasi tagihan total, pembentukan berkas PDF nota, dan pencetakan dokumen resmi.

```mermaid
sequenceDiagram
    autonumber
    actor Admin as Staf Admin
    participant NotaUI as View: NotaPage
    participant NotaCtrl as Controller: NotaController
    participant PDFService as Service: PDFGenerator (DomPDF)
    participant NotaModel as Model: NotaPembayaran
    participant DB as Database (MySQL)

    Admin->>NotaUI: 1. Pilih Work Order yang selesai dikerjakan
    NotaUI->>NotaCtrl: 2. prepareInvoice(id_wo)
    NotaCtrl-->>NotaUI: 3. Tampilkan rincian biaya pengerjaan & suku cadang
    Admin->>NotaUI: 4. Klik tombol "Buat / Cetak Nota"
    NotaUI->>NotaCtrl: 5. generateInvoice(id_wo)

    alt Rincian Biaya Belum Lengkap
        NotaCtrl-->>NotaUI: 6a. Return pesan peringatan data biaya belum lengkap
        NotaUI-->>Admin: 7a. Tampilkan alert: "Harap lengkapi rincian biaya dahulu!"
    else Rincian Biaya Lengkap & Valid
        NotaCtrl->>NotaCtrl: 6b. Kalkulasi total biaya akhir (Jasa Bubut + Material)
        NotaCtrl->>NotaModel: 7b. create(id_nota, id_wo, total_biaya, ...)
        NotaModel->>DB: 8b. INSERT INTO nota_pembayaran (...)
        DB-->>NotaCtrl: 9b. Record nota tersimpan
        
        NotaCtrl->>PDFService: 10b. renderPDF(invoice_template, data_nota)
        PDFService-->>NotaCtrl: 11b. Berkas dokumen PDF nota resmi
        
        NotaCtrl-->>NotaUI: 12b. Kirim file PDF / pratinjau cetak
        NotaUI-->>Admin: 13b. Tampilkan dokumen nota di layar untuk konfirmasi / cetak
    end
```
