# 02. Class Diagram — Sistem Informasi PT Alexander Cipta Karya

Diagram Kelas (*Class Diagram*) ini memodelkan struktur berorientasi objek sistem berbasis arsitektur **MVC (Model-View-Controller)** pada kerangka kerja **Laravel (PHP)** sesuai dengan Bab 4.1 (Kebutuhan Data) dan Bab 4.4 (Kebutuhan Perangkat Lunak) pada dokumen SRS.

---

## 1. Kode Mermaid (Siap Di-import ke Draw.io)

> **Cara Import ke Draw.io:**
> 1. Buka [draw.io](https://app.diagrams.net/).
> 2. Klik menu **Arrange** > **Insert** > **Advanced** > **Mermaid**.
> 3. Salin (*copy*) kode di dalam blok di bawah ini dan tempel (*paste*) ke Draw.io.
> 4. Klik **Insert**.

```mermaid
classDiagram
    direction TB

    %% Entitas Model Pengguna
    class User {
        -String id_user
        -String username
        -String password
        -String nama_lengkap
        -String role
        -DateTime created_at
        +login(username, password) Boolean
        +logout() Void
        +getRole() String
    }

    %% Entitas Model Pelanggan
    class Pelanggan {
        -String id_pelanggan
        -String nama_pelanggan
        -String nomor_telepon
        -String alamat
        -DateTime created_at
        +tambahPelanggan() Boolean
        +updateProfil() Boolean
        +getRiwayatPesanan() List~WorkOrder~
    }

    %% Entitas Model Work Order
    class WorkOrder {
        -String id_work_order
        -Date tanggal_wo
        -String id_pelanggan
        -String id_user
        -String status_wo
        -Decimal total_biaya
        -DateTime created_at
        +createWO() Boolean
        +updateWO() Boolean
        +deleteWO() Boolean
        +searchWO(keyword) List~WorkOrder~
        +getDetailWO() WorkOrder
    }

    %% Entitas Model Barang / Item Pekerjaan
    class Barang {
        -String id_barang
        -String id_work_order
        -String nama_barang
        -String spesifikasi_barang
        -Integer jumlah
        +simpanBarang() Boolean
        +updateBarang() Boolean
        +deleteBarang() Boolean
    }

    %% Entitas Model Progres Pengerjaan
    class ProgresPengerjaan {
        -String id_progres
        -String id_work_order
        -String id_user_produksi
        -DateTime tanggal_update
        -String status_progres
        -String dokumentasi_progres
        -String qr_code_tracking
        -String catatan_pengerjaan
        +tambahProgres() Boolean
        +updateStatus() Boolean
        +uploadDokumentasi(file) String
        +generateQRCode() String
        +getRiwayatProgres() List~ProgresPengerjaan~
    }

    %% Entitas Model Nota Pembayaran
    class NotaPembayaran {
        -String id_nota
        -String id_work_order
        -String id_admin
        -Date tanggal_nota
        -Decimal rincian_biaya
        -Decimal total_biaya
        -String status_pembayaran
        -String bukti_pembayaran
        +kalkulasiTotal() Decimal
        +generateNotaPDF() File
        +konfirmasiPembayaran() Boolean
        +cetakNota() Void
    }

    %% Controller Layer (Aplikasi MVC Laravel)
    class AuthController {
        +showLoginForm()
        +authenticate(Request request)
        +logout()
    }

    class WorkOrderController {
        +index()
        +create()
        +store(Request request)
        +show(id_wo)
        +edit(id_wo)
        +update(Request request, id_wo)
        +destroy(id_wo)
        +search(keyword)
    }

    class ProgresController {
        +index(id_wo)
        +updateProgress(Request request)
        +uploadMedia(File file)
        +completeProgress(id_wo)
    }

    class TrackingController {
        +trackByQR(String qr_token)
        +showPublicProgress(String id_wo)
    }

    class NotaController {
        +create(id_wo)
        +store(Request request)
        +exportPDF(id_nota)
        +preview(id_nota)
    }

    %% Relasi Asosiasi Antar Model (Domain Relationships)
    User "1" --> "0..*" WorkOrder : "membuat/menginput"
    Pelanggan "1" --> "1..*" WorkOrder : "memesan"
    WorkOrder "1" *-- "1..*" Barang : "terdiri atas (Komposisi)"
    WorkOrder "1" *-- "1..*" ProgresPengerjaan : "memiliki tahapan (Komposisi)"
    User "1" --> "0..*" ProgresPengerjaan : "memperbarui (Produksi)"
    WorkOrder "1" -- "0..1" NotaPembayaran : "menghasilkan"
    User "1" --> "0..*" NotaPembayaran : "menerbitkan (Admin)"

    %% Relasi Controller terhadap Model (Dependency)
    AuthController ..> User : "mengautentikasi"
    WorkOrderController ..> WorkOrder : "mengelola"
    WorkOrderController ..> Barang : "mengelola"
    ProgresController ..> ProgresPengerjaan : "mengelola"
    TrackingController ..> ProgresPengerjaan : "menampilkan"
    NotaController ..> NotaPembayaran : "memproses"
```

---

## 2. Deskripsi Elemen dan Struktur Kelas

### A. Lapisan Model (Data Domain)

1. **`User`**:
   - Menyimpan akun petugas bengkel dengan atribut peran (`role`: Admin, Marketing, Produksi).
   - Menangani proses enkripsi password dan validasi autentikasi.

2. **`Pelanggan`**:
   - Menyimpan data identitas pelanggan (*Nama Pelanggan, Nomor Telepon, Alamat*).
   - Menghubungkan riwayat pesanan pelanggan ke berbagai dokumen *Work Order*.

3. **`WorkOrder`**:
   - Entitas sentral yang menyimpan instruksi pengerjaan jasa bubut/manufaktur.
   - Menyimpan status keseluruhan (misal: *Pending, In Progress, Ready for Billing, Completed*).

4. **`Barang`**:
   - Menyimpan detail teknis barang yang dikerjakan (*Nama Barang, Spesifikasi Barang/Ukuran/Bahan, Jumlah*).
   - Terikat langsung secara kuat (*Composition*) dengan `WorkOrder`.

5. **`ProgresPengerjaan`**:
   - Mencatat setiap pembaruan pengerjaan yang dilakukan operator bengkel bubut (Produksi).
   - Menyimpan tautan berkas foto dokumentasi (*file path*) dan kode unik pelacakan QR Code.

6. **`NotaPembayaran`**:
   - Dibuat oleh Admin ketika pekerjaan telah selesai atau masuk termin pembayaran.
   - Mengkalkulasi subtotal rincian biaya pesanan menjadi dokumen tagihan digital yang dapat diunduh (PDF) atau dicetak.

---

### B. Lapisan Controller (MVC Laravel)

- **`AuthController`**: Menangani otentikasi sesi login pengguna internal bengkel sesuai hak akses peran.
- **`WorkOrderController`**: Mengatur alur pembuatan, pengubahan, penghapusan, dan filter pencarian pesanan kerja.
- **`ProgresController`**: Memproses input tahapan pengerjaan dan penerimaan unggahan foto dokumentasi dari operator lapangan.
- **`TrackingController`**: Menangani permintaan publik dari pelanggan yang memindai QR Code untuk menampilkan status pengerjaan secara aman tanpa login.
- **`NotaController`**: Mengelola perhitungan tagihan, pembuatan nomor nota unik, dan konversi ke dokumen PDF.
