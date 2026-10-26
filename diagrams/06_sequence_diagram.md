# 06. Sequence Diagram — Sistem Informasi PT Alexander Cipta Karya

Dokumen ini memuat seluruh pemodelan interaksi antar objek (*Sequence Diagram*) sistem informasi PT Alexander Cipta Karya yang dirancang berdasarkan skenario utama (*main flow*) dan skenario eksepsional (*alternative flow*) pada Bab 3.2 dokumen SRS (SKPL-BUBUT halaman 18–32), diimplementasikan dalam arsitektur **MVC Laravel + Database MySQL**.

Terdapat 6 Sequence Diagram lengkap:
1. **SD-01: Melakukan Login (SRS 3.2.1)**
2. **SD-02: Melakukan CRUD Work Order (SRS 3.2.2)**
3. **SD-03: Mengelola Progress & Auto Generate QR (SRS 3.2.3 & 3.2.6)**
4. **SD-04: Melihat Progress / Pelanggan Tracking (SRS 3.2.4)**
5. **SD-05: Mencari Work Order (SRS 3.2.5)**
6. **SD-06: Membuat Nota Pembayaran (SRS 3.2.7)**

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

## 2. SD-02: Melakukan CRUD Work Order (SRS 3.2.2)

Memodelkan siklus hidup Work Order oleh Admin/Marketing yang mencakup penambahan (*Create*), penampilan detail (*Read*), pengubahan (*Update*), dan penghapusan (*Delete*).

```mermaid
sequenceDiagram
    autonumber
    actor Staf as Admin / Marketing
    participant WOUI as View: WorkOrderPage
    participant WOCtrl as Controller: WorkOrderController
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

    %% Sub-skenario: Tambah Data Baru
    opt Aksi Tambah Work Order (Create)
        Staf->>WOUI: 9. Klik tombol "Tambah Work Order"
        WOUI-->>Staf: 10. Tampilkan form WO kosong
        Staf->>WOUI: 11. Input data pelanggan & barang, klik Simpan
        WOUI->>WOCtrl: 12. store(Request $request)
        WOCtrl->>WOCtrl: 13. Validasi kelengkapan form
        WOCtrl->>WOModel: 14. create(data_wo)
        WOModel->>DB: 15. INSERT INTO work_orders (...)
        WOCtrl->>BarangModel: 16. create(data_barang)
        BarangModel->>DB: 17. INSERT INTO barang (...)
        DB-->>WOCtrl: 18. Commit transaksi sukses
        WOCtrl-->>WOUI: 19. Flash session notifikasi sukses & reload
        WOUI-->>Staf: 20. Tampilkan data baru pada tabel WO
    end

    %% Sub-skenario: Ubah Data
    opt Aksi Ubah Work Order (Update)
        Staf->>WOUI: 21. Pilih baris data & klik "Ubah"
        WOUI->>WOCtrl: 22. edit(id_wo)
        WOCtrl-->>WOUI: 23. Kirim data WO & form edit
        Staf->>WOUI: 24. Ubah atribut & klik Simpan
        WOUI->>WOCtrl: 25. update(id_wo, Request $request)
        WOCtrl->>WOModel: 26. update(data_perubahan)
        WOModel->>DB: 27. UPDATE work_orders SET ... WHERE id = id_wo
        DB-->>WOCtrl: 28. Status pembaruan berhasil
        WOCtrl-->>WOUI: 29. Tampilkan notifikasi perubahan berhasil
    end

    %% Sub-skenario: Hapus Data
    opt Aksi Hapus Work Order (Delete)
        Staf->>WOUI: 30. Pilih data & klik "Hapus"
        WOUI-->>Staf: 31. Tampilkan dialog konfirmasi hapus
        Staf->>WOUI: 32. Klik tombol "Ya, Konfirmasi Hapus"
        WOUI->>WOCtrl: 33. destroy(id_wo)
        WOCtrl->>WOModel: 34. delete(id_wo)
        WOModel->>DB: 35. DELETE FROM work_orders WHERE id = id_wo
        DB-->>WOCtrl: 36. Status hapus berhasil
        WOCtrl-->>WOUI: 37. Refresh tabel & tampilkan notifikasi berhasil
    end
```

---

## 3. SD-03: Mengelola Progress & Auto Generate QR Code (SRS 3.2.3 & 3.2.6)

Memodelkan pembaruan tahapan pengerjaan oleh tim Produksi, penyimpanan dokumen bukti foto, otomatisasi pembuatan QR Code pelacakan, serta penyelesaian status pengerjaan.

```mermaid
sequenceDiagram
    autonumber
    actor Prod as Bagian Produksi
    participant ProgUI as View: ProgresPage
    participant ProgCtrl as Controller: ProgresController
    participant FileStorage as Server File Storage
    participant QRService as Service: QRCodeGenerator
    participant ProgModel as Model: ProgresPengerjaan
    participant WOModel as Model: WorkOrder
    participant DB as Database (MySQL)

    Prod->>ProgUI: 1. Pilih Work Order yang sedang dikerjakan
    ProgUI->>ProgCtrl: 2. getDetail(id_wo)
    ProgCtrl-->>ProgUI: 3. Tampilkan detail pesanan & form update progres
    Prod->>ProgUI: 4. Input keterangan status & unggah berkas foto pengerjaan
    Prod->>ProgUI: 5. Klik tombol "Simpan Progres"
    ProgUI->>ProgCtrl: 6. storeProgres(id_wo, file, status)
    
    ProgCtrl->>ProgCtrl: 7. Validasi format gambar (JPG/PNG, ukuran maks)
    ProgCtrl->>FileStorage: 8. Simpan berkas foto ke storage server
    FileStorage-->>ProgCtrl: 9. Return file_path dokumen
    
    ProgCtrl->>QRService: 10. generateTrackingQR(id_wo)
    QRService->>FileStorage: 11. Simpan gambar barcode QR unik
    FileStorage-->>QRService: 12. Return qr_image_path
    QRService-->>ProgCtrl: 13. Return token_qr & qr_image_path
    
    ProgCtrl->>ProgModel: 14. insertProgres(id_wo, status, foto_path, qr_path)
    ProgModel->>DB: 15. INSERT INTO progres_pengerjaan (...)
    DB-->>ProgCtrl: 16. Record tersimpan
    
    ProgCtrl-->>ProgUI: 17. Tampilkan notifikasi progres berhasil disimpan & tampilkan QR
    
    opt Konfirmasi Pengerjaan Selesai (Finish)
        Prod->>ProgUI: 18. Klik "Selesaikan Pekerjaan"
        ProgUI-->>Prod: 19. Tampilkan konfirmasi dialog
        Prod->>ProgUI: 20. Klik "Ya, Pekerjaan Selesai"
        ProgUI->>ProgCtrl: 21. markAsComplete(id_wo)
        ProgCtrl->>WOModel: 22. updateStatus(id_wo, 'Selesai')
        WOModel->>DB: 23. UPDATE work_orders SET status = 'Selesai' WHERE id = id_wo
        DB-->>ProgCtrl: 24. Berhasil diperbarui
        ProgCtrl-->>ProgUI: 25. Tampilkan notifikasi status pengerjaan selesai
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

## 6. SD-06: Membuat Nota Pembayaran (SRS 3.2.7)

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
