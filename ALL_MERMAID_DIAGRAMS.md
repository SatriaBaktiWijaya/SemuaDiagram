# 📋 Master Kumpulan Semua Diagram Mermaid (All-in-One)
### Sistem Informasi Work Order & Tracking Progress — PT Alexander Cipta Karya (SRS-BUBUT 2026)

> **💡 Petunjuk Cepat Import ke Draw.io:**
> 1. Buka [draw.io](https://app.diagrams.net/).
> 2. Klik menu **Arrange** > **Insert** > **Advanced** > **Mermaid**.
> 3. Salin (*copy*) salah satu blok kode Mermaid di bawah ini dan tempel (*paste*) ke Draw.io.
> 4. Klik **Insert**.

---

## 📑 Daftar Isi Cepat (Klik untuk Langsung Lompat ke Diagram)

1. [01. Use Case Diagram](#01-use-case-diagram)
2. [02. Class Diagram (Arsitektur MVC Laravel)](#02-class-diagram-arsitektur-mvc-laravel)
3. [03. Context Diagram (DFD Level 0)](#03-context-diagram-dfd-level-0)
4. [04. Entity Relationship Diagram (ERD Database MySQL)](#04-entity-relationship-diagram-erd-database-mysql)
5. **Activity Diagrams (7 Alur Lengkap)**:
   - [5.1 Activity Diagram: Melakukan Login](#51-activity-diagram-melakukan-login)
   - [5.2 Activity Diagram: Melakukan CRUD Work Order](#52-activity-diagram-melakukan-crud-work-order)
   - [5.3 Activity Diagram: Mengelola Progress](#53-activity-diagram-mengelola-progress)
   - [5.4 Activity Diagram: Melihat Progress (Pelanggan)](#54-activity-diagram-melihat-progress-pelanggan)
   - [5.5 Activity Diagram: Mencari Work Order](#55-activity-diagram-mencari-work-order)
   - [5.6 Activity Diagram: Mengenerate QR (Inklusi)](#56-activity-diagram-mengenerate-qr-inklusi)
   - [5.7 Activity Diagram: Membuat Nota Pembayaran](#57-activity-diagram-membuat-nota-pembayaran)
6. **Sequence Diagrams (7 Interaksi Lengkap)**:
   - [6.1 Sequence Diagram: Melakukan Login](#61-sequence-diagram-melakukan-login)
   - [6.2 Sequence Diagram: Melakukan CRUD Work Order & Auto QR di Awal](#62-sequence-diagram-melakukan-crud-work-order--auto-qr-di-awal)
   - [6.3 Sequence Diagram: Mengelola Progress](#63-sequence-diagram-mengelola-progress)
   - [6.4 Sequence Diagram: Melihat Progress (Pelanggan)](#64-sequence-diagram-melihat-progress-pelanggan)
   - [6.5 Sequence Diagram: Mencari Work Order](#65-sequence-diagram-mencari-work-order)
   - [6.6 Sequence Diagram: Mengenerate QR Code (Inklusi Otomatis Work Order)](#66-sequence-diagram-mengenerate-qr-code-inklusi-otomatis-work-order)
   - [6.7 Sequence Diagram: Membuat Nota Pembayaran](#67-sequence-diagram-membuat-nota-pembayaran)

---

## 01. Use Case Diagram

Diagram Use Case memodelkan batasan sistem, 4 kelas pengguna/aktor, dan relasi `<<include>>` serta `<<extend>>` sesuai Bab 3.1 & 3.2 SRS.

### 🌟 Opsi A: Format PlantUML (Direkomendasikan di Draw.io untuk Stickman Otomatis)
> **Cara Pakai di Draw.io:** Menu **Arrange** > **Insert** > **Advanced** > **PlantUML**. Paste kode di bawah, lalu klik **Insert**. Seluruh aktor seketika berwujud **Stickman (UML Actor)** asli bawaan Draw.io!

```plantuml
@startuml
left to right direction
skinparam actorStyle awesome
skinparam packageStyle rectangle

actor "Admin\n(10 Pengguna)" as Admin
actor "Marketing\n(3 Pengguna)" as Marketing
actor "Produksi\n(45 Pengguna)" as Produksi
actor "Pelanggan / Customer\n(Publik)" as Customer

rectangle "Sistem Informasi Work Order & Tracking Progress\nPT Alexander Cipta Karya" {
  usecase "UC-01: Melakukan Login" as UC1
  usecase "UC-02: Melakukan CRUD Work Order" as UC2
  usecase "UC-03: Mengelola Progress" as UC3
  usecase "UC-04: Melihat Progress" as UC4
  usecase "UC-05: Mencari Work Order" as UC5
  usecase "UC-06: Mengenerate QR (Inklusi)" as UC6
  usecase "UC-07: Membuat Nota" as UC7
  usecase "UC-08: Konsultasi WhatsApp Resmi" as UC8
}

Produksi --> UC1
Produksi --> UC3

Admin --> UC1
Admin --> UC2
Admin --> UC5
Admin --> UC7

Marketing --> UC1
Marketing --> UC2
Marketing --> UC5

Customer --> UC4
Customer --> UC8

UC2 ..> UC6 : <<include>>
UC5 ..> UC2 : <<extend>>
@enduml
```

---

### 🌟 Opsi B: Format Mermaid Resmi Use Case (`usecase-beta` dengan Keyword `actor`)
> **Cara Pakai di Draw.io:** Menu **Arrange** > **Insert** > **Advanced** > **Mermaid**.

```mermaid
usecase-beta
    direction LR

    actor Admin("Admin")
    actor Marketing("Marketing")
    actor Produksi("Produksi")
    actor Customer("Pelanggan")

    systemBoundary "Sistem Informasi Work Order & Tracking Progress PT Alexander Cipta Karya"
        UC1("UC-01: Melakukan Login")
        UC2("UC-02: Melakukan CRUD Work Order")
        UC3("UC-03: Mengelola Progress")
        UC4("UC-04: Melihat Progress")
        UC5("UC-05: Mencari Work Order")
        UC6("UC-06: Mengenerate QR Code")
        UC7("UC-07: Membuat Nota")
        UC8("UC-08: Konsultasi WhatsApp")
    end

    Admin --> UC1
    Admin --> UC2
    Admin --> UC5
    Admin --> UC7

    Marketing --> UC1
    Marketing --> UC2
    Marketing --> UC5

    Produksi --> UC1
    Produksi --> UC3

    Customer --> UC4
    Customer --> UC8

    UC2 ..> : include UC6
    UC2 ..> : extend UC5
```

---

### 🌟 Opsi C: Format Mermaid Flowchart (Visual Ikon Orang)

```mermaid
flowchart LR
    %% Definisi Aktor dengan Ikon Orang
    subgraph Kiri [" "]
        direction TB
        Produksi["👤<br/><b>Produksi</b><br/>(45 Pengguna)"]
        Customer["👤<br/><b>Pelanggan / Customer</b><br/>(Publik / Tamu)"]
    end

    subgraph Kanan [" "]
        direction TB
        Admin["👤<br/><b>Admin</b><br/>(10 Pengguna)"]
        Marketing["👤<br/><b>Marketing</b><br/>(3 Pengguna)"]
    end

    %% Batasan Sistem (System Boundary)
    subgraph Sistem ["Sistem Informasi Work Order & Tracking Progress PT Alexander Cipta Karya"]
        UC1(["UC-01: Melakukan Login"])
        UC2(["UC-02: Melakukan CRUD Work Order"])
        UC3(["UC-03: Mengelola Progress"])
        UC4(["UC-04: Melihat Progress"])
        UC5(["UC-05: Mencari Work Order"])
        UC6(["UC-06: Mengenerate QR (Inklusi)"])
        UC7(["UC-07: Membuat Nota"])
        UC8(["UC-08: Konsultasi WhatsApp Resmi"])
    end

    %% Relasi Aktor ke Use Case
    Produksi --- UC1
    Produksi --- UC3

    Admin --- UC1
    Admin --- UC2
    Admin --- UC5
    Admin --- UC7

    Marketing --- UC1
    Marketing --- UC2
    Marketing --- UC5

    Customer --- UC4
    Customer --- UC8

    %% Relasi Antar Use Case (Include & Extend)
    UC2 -.->|&lt;&lt;extend&gt;&gt;| UC5
    UC2 -.->|&lt;&lt;include&gt;&gt;| UC6

    %% Styling Visual untuk Draw.io
    classDef actorStyle fill:#E3F2FD,stroke:#1565C0,stroke-width:2px,color:#0D47A1,font-size:13px;
    classDef usecaseStyle fill:#FFFFFF,stroke:#37474F,stroke-width:2px,color:#263238,font-size:12px;
    classDef includeStyle fill:#FFF8E1,stroke:#FFA000,stroke-width:1.5px,color:#5D4037,stroke-dasharray: 4 4;
    classDef systemStyle fill:#FAFAFA,stroke:#90A4AE,stroke-width:2px,color:#37474F;

    class Produksi,Customer,Admin,Marketing actorStyle;
    class UC1,UC2,UC3,UC4,UC7,UC8 usecaseStyle;
    class UC5,UC6 includeStyle;
    class Sistem systemStyle;
```

---

## 02. Class Diagram (Arsitektur MVC Laravel)

Diagram Kelas memodelkan struktur berorientasi objek sistem berbasis arsitektur MVC (Model-View-Controller) pada kerangka kerja Laravel (PHP) sesuai Bab 4.1 dan Bab 4.4 SRS.

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

## 03. Context Diagram (DFD Level 0)

Diagram Konteks menggambarkan batasan sistem, entitas eksternal (*terminator*), dan aliran data masuk/keluar (*input/output data flows*) sesuai Bab 1.2, 4.1, dan 4.2 SRS.

```mermaid
flowchart TB
    %% Entitas Eksternal (Terminator)
    subgraph Pengguna_Internal ["Pengguna Internal Perusahaan"]
        Admin["Admin Bengkel"]
        Marketing["Tim Marketing"]
        Produksi["Operator Produksi"]
    end

    subgraph Pihak_Eksternal ["Pengguna & Layanan Eksternal"]
        Pelanggan["Pelanggan / Customer"]
        WhatsAppAPI["WhatsApp Official API Gateway"]
    end

    %% Entitas Sentral (Sistem)
    SistemCentral(("0.0<br/><b>Sistem Informasi Work Order<br/>& Tracking Progress</b><br/>PT Alexander Cipta Karya"))

    %% Aliran Data Admin
    Admin -->|1. Data Login Kredensial<br/>2. Data Pengelolaan WO (CRUD)<br/>3. Kata Kunci Pencarian WO<br/>4. Data Rincian Biaya & Konfirmasi Nota| SistemCentral
    SistemCentral -->|1. Status Login & Hak Akses<br/>2. Daftar & Detail Data WO<br/>3. Hasil Filter Pencarian WO<br/>4. Dokumen Nota Pembayaran (PDF/Print)| Admin

    %% Aliran Data Marketing
    Marketing -->|1. Data Login Kredensial<br/>2. Data Pelanggan & WO Baru/Edit<br/>3. Kata Kunci Pencarian WO| SistemCentral
    SistemCentral -->|1. Status Login & Hak Akses<br/>2. Daftar & Detail Data WO<br/>3. Berkas Gambar / Link QR Code Tracking| Marketing

    %% Aliran Data Produksi
    Produksi -->|1. Data Login Kredensial<br/>2. Pembaruan Status Progres Kerja<br/>3. Berkas Foto / Dokumentasi Pengerjaan<br/>4. Konfirmasi Penyelesaian Pekerjaan| SistemCentral
    SistemCentral -->|1. Status Login & Hak Akses<br/>2. Daftar WO yang Sedang Dikerjakan<br/>3. Notifikasi Berhasil Update & Generate QR| Produksi

    %% Aliran Data Pelanggan
    Pelanggan -->|1. Scan QR Code Tracking / Akses Link Unik<br/>2. Klik Tombol Konsultasi WhatsApp| SistemCentral
    SistemCentral -->|1. Informasi Status Progres & Foto Dokumentasi<br/>2. Tampilan Rincian Biaya & Nota Pembayaran<br/>3. Redireksi Tautan ke Layanan WhatsApp| Pelanggan

    %% Aliran Layanan WhatsApp
    SistemCentral -->|Tautan Pesan Konsultasi Berisi No. Resmi| WhatsAppAPI

    %% Styling Warna dan Bentuk
    classDef systemProcess fill:#E3F2FD,stroke:#1565C0,stroke-width:3px,font-size:14px,color:#0D47A1;
    classDef internalEntity fill:#FFF3E0,stroke:#E65100,stroke-width:2px,font-size:12px,font-weight:bold,color:#BF360C;
    classDef externalEntity fill:#E8F5E9,stroke:#2E7D32,stroke-width:2px,font-size:12px,font-weight:bold,color:#1B5E20;

    class SistemCentral systemProcess;
    class Admin,Marketing,Produksi internalEntity;
    class Pelanggan,WhatsAppAPI externalEntity;
```

---

## 04. Entity Relationship Diagram (ERD Database MySQL)

Diagram Hubungan Entitas memodelkan skema basis data relasional sistem (*MySQL/XAMPP*) berdasarkan 18 atribut Kebutuhan Data (Bab 4.1 SRS).

```mermaid
erDiagram
    USERS ||--o{ WORK_ORDERS : "menginput"
    USERS ||--o{ PROGRES_PENGERJAAN : "memperbarui"
    USERS ||--o{ NOTA_PEMBAYARAN : "menerbitkan"
    
    PELANGGAN ||--o{ WORK_ORDERS : "memesan"
    
    WORK_ORDERS ||--|{ BARANG : "berisi"
    WORK_ORDERS ||--o{ PROGRES_PENGERJAAN : "memiliki_tahapan"
    WORK_ORDERS ||--o| NOTA_PEMBAYARAN : "menghasilkan"

    USERS {
        varchar id_user PK "ID Unik Pengguna"
        varchar username "Username Login"
        varchar password "Kata Sandi Terenkripsi"
        varchar nama_lengkap "Nama Lengkap Karyawan"
        varchar role "Admin | Marketing | Produksi"
        timestamp created_at "Waktu Akun Dibuat"
    }

    PELANGGAN {
        varchar id_pelanggan PK "ID Unik Pelanggan"
        varchar nama_pelanggan "Nama Lengkap Pelanggan"
        varchar nomor_telepon "Nomor Telepon/WA"
        text alamat_pelanggan "Alamat Pelanggan"
        timestamp created_at "Waktu Registrasi Data"
    }

    WORK_ORDERS {
        varchar id_work_order PK "ID Unik WO (Auto-generated)"
        varchar id_pelanggan FK "Relasi ke Pelanggan"
        varchar id_user FK "ID Pembuat (Admin/Marketing)"
        date tanggal_wo "Tanggal Pembuatan WO"
        varchar status_wo "Antrean | Proses | Selesai"
        decimal total_estimasi_biaya "Estimasi Biaya Awal"
        timestamp created_at "Waktu Rekam Sistem"
    }

    BARANG {
        varchar id_barang PK "ID Unik Barang (Auto-generated)"
        varchar id_work_order FK "Relasi ke Work Order"
        varchar nama_barang "Nama Barang/Benda Kerja"
        text spesifikasi_barang "Detail Teknis Spesifikasi"
        int jumlah "Kuantitas Barang"
        varchar satuan "Pcs | Unit | Set"
    }

    PROGRES_PENGERJAAN {
        varchar id_progres PK "ID Unik Progres Pengerjaan"
        varchar id_work_order FK "Relasi ke Work Order"
        varchar id_user_produksi FK "ID Operator Produksi"
        timestamp tanggal_update "Waktu Update Progres"
        varchar status_progres "Received | Process | Finished"
        varchar dokumentasi_progres "Path/URL File Foto Pengerjaan"
        varchar qr_code_tracking "Path/String QR Code Unik"
        text catatan_pengerjaan "Keterangan Kondisi Pekerjaan"
    }

    NOTA_PEMBAYARAN {
        varchar id_nota PK "ID Unik Nota (Auto-generated)"
        varchar id_work_order FK "Relasi ke Work Order"
        varchar id_user_admin FK "ID Admin Pembuat Nota"
        date tanggal_nota "Tanggal Penerbitan Nota"
        decimal rincian_biaya "Biaya Rincian Layanan/Bahan"
        decimal total_biaya "Total Akhir Tagihan"
        varchar status_pembayaran "Belum Bayar | DP | Lunas"
        varchar bukti_pembayaran "Path/URL File Bukti Transfer"
        timestamp created_at "Waktu Nota Dibuat"
    }
```

---

## 5. Kumpulan Activity Diagram (Bab 3.3 SRS)

### 5.1 Activity Diagram: Melakukan Login

```mermaid
flowchart TD
    Start([● Mulai]) --> A1[Buka Halaman Login]
    A1 --> S1[Sistem Menampilkan Form Login]
    S1 --> A2[Masukkan Username dan Password]
    A2 --> A3[Klik Tombol Login]
    A3 --> S2{Data Login Valid?}
    
    S2 -->|Ya| S3[Sistem Menampilkan Dashboard Sesuai Peran]
    S3 --> EndNode([◉ Selesai])
    
    S2 -->|Tidak| S4[Sistem Menampilkan Pesan Gagal Login]
    S4 --> A2

    classDef startEnd fill:#E8F5E9,stroke:#2E7D32,stroke-width:2px,color:#1B5E20;
    classDef action fill:#FFFFFF,stroke:#37474F,stroke-width:1.5px,color:#263238;
    classDef decision fill:#FFF9C4,stroke:#FBC02D,stroke-width:2px,color:#F57F17;
    
    class Start,EndNode startEnd;
    class A1,S1,A2,A3,S3,S4 action;
    class S2 decision;
```

---

### 5.2 Activity Diagram: Melakukan CRUD Work Order

```mermaid
flowchart TD
    Start([● Mulai]) --> A1[Pilih Menu Work Order]
    A1 --> S1[Sistem Menampilkan Daftar Work Order]
    S1 --> A2{Pilih Aksi Pengelolaan}

    %% Cabang Tambah
    A2 -->|Aksi Tambah| T1[Klik Tombol Tambah Work Order]
    T1 --> T2[Sistem Tampilkan Form Work Order Kosong]
    T2 --> T3[Isi Data Pesanan & Klik Simpan]
    T3 --> T4{Data Valid & Lengkap?}
    T4 -->|Tidak| T5[Sistem Tampilkan Pesan Kesalahan]
    T5 --> T3
    T4 -->|Ya| T6[Sistem Simpan Data Baru ke Database]
    T6 --> T7[Sistem Perbarui Daftar Work Order]
    T7 --> T8[Sistem Tampilkan Notifikasi Berhasil]
    T8 --> EndNode([◉ Selesai])

    %% Cabang Ubah
    A2 -->|Aksi Ubah| U1[Pilih Baris Data & Klik Tombol Ubah]
    U1 --> U2[Sistem Tampilkan Form Berisi Data Terpilih]
    U2 --> U3[Ubah Data Pesanan & Klik Simpan]
    U3 --> U4{Perubahan Valid?}
    U4 -->|Tidak| U5[Sistem Tampilkan Pesan Kesalahan]
    U5 --> U3
    U4 -->|Ya| U6[Sistem Simpan Perubahan ke Database]
    U6 --> T7

    %% Cabang Hapus
    A2 -->|Aksi Hapus| H1[Pilih Data & Klik Tombol Hapus]
    H1 --> H2[Sistem Tampilkan Pop-up Konfirmasi Hapus]
    H2 --> H3{Konfirmasi Hapus Data?}
    H3 -->|Batal| S1
    H3 -->|Ya Setuju| H4[Sistem Hapus Data dari Database]
    H4 --> T7

    %% Cabang Lihat
    A2 -->|Aksi Lihat| L1[Pilih Data & Klik Tombol Lihat]
    L1 --> L2[Sistem Menampilkan Detail Work Order Lengkap]
    L2 --> EndNode

    classDef startEnd fill:#E8F5E9,stroke:#2E7D32,stroke-width:2px,color:#1B5E20;
    classDef action fill:#FFFFFF,stroke:#37474F,stroke-width:1.5px,color:#263238;
    classDef decision fill:#FFF9C4,stroke:#FBC02D,stroke-width:2px,color:#F57F17;
    
    class Start,EndNode startEnd;
    class A1,S1,T1,T2,T3,T5,T6,T7,T8,U1,U2,U3,U5,U6,H1,H2,H4,L1,L2 action;
    class A2,T4,U4,H3 decision;
```

---

### 5.3 Activity Diagram: Mengelola Progress

```mermaid
flowchart TD
    Start([● Mulai]) --> P1[Pilih Work Order yang Sedang Dikerjakan]
    P1 --> S1[Sistem Tampilkan Detail WO & Form Progres]
    S1 --> P2[Isi Informasi Progres Pengerjaan]
    P2 --> P3[Unggah Foto / Dokumen Pendukung Pengerjaan]
    P3 --> P4[Klik Tombol Simpan]
    P4 --> S2{Data dan Format File Valid?}
    
    S2 -->|Tidak| S3[Sistem Tampilkan Pesan Kesalahan Unggah]
    S3 --> P2
    
    S2 -->|Ya| S4[Sistem Menyimpan Data Progres Baru]
    S4 --> S6[Sistem Tampilkan Notifikasi Berhasil Simpan]
    S6 --> P5{Pekerjaan Telah Selesai Semua?}

    P5 -->|Belum Selesai| EndNode([◉ Selesai])
    
    P5 -->|Ya, Tandai Selesai| P6[Klik Tombol Selesaikan Progres]
    P6 --> S7[Sistem Tampilkan Dialog Konfirmasi Penyelesaian]
    S7 --> P7{Konfirmasi Penyelesaian Progres?}
    
    P7 -->|Batal| S8[Tutup Dialog & Pertahankan Status Sebelumnya]
    S8 --> EndNode
    
    P7 -->|Ya Selesai| S9[Sistem Ubah Status WO Menjadi Selesai]
    S9 --> S10[Sistem Tampilkan Notifikasi Selesai Berhasil]
    S10 --> EndNode

    classDef startEnd fill:#E8F5E9,stroke:#2E7D32,stroke-width:2px,color:#1B5E20;
    classDef action fill:#FFFFFF,stroke:#37474F,stroke-width:1.5px,color:#263238;
    classDef decision fill:#FFF9C4,stroke:#FBC02D,stroke-width:2px,color:#F57F17;
    
    class Start,EndNode startEnd;
    class P1,S1,P2,P3,P4,S3,S4,S6,P6,S7,S8,S9,S10 action;
    class S2,P5,P7 decision;
```

---

### 5.4 Activity Diagram: Melihat Progress (Pelanggan)

```mermaid
flowchart TD
    Start([● Mulai]) --> C1[Pelanggan Scan QR Code / Buka URL Tracking]
    C1 --> S1[Sistem Menerima Parameter & Mengambil Data Progres]
    S1 --> S2{Data Progres Tersedia di Sistem?}
    
    S2 -->|Ya| S3[Sistem Tampilkan Informasi Progres & Foto Dokumentasi]
    S3 --> C2[Pelanggan Memantau Status Pengerjaan Terkini]
    C2 --> EndNode([◉ Selesai])
    
    S2 -->|Tidak| S4[Sistem Tampilkan Pesan: Data Progres Belum Tersedia]
    S4 --> EndNode

    classDef startEnd fill:#E8F5E9,stroke:#2E7D32,stroke-width:2px,color:#1B5E20;
    classDef action fill:#FFFFFF,stroke:#37474F,stroke-width:1.5px,color:#263238;
    classDef decision fill:#FFF9C4,stroke:#FBC02D,stroke-width:2px,color:#F57F17;
    
    class Start,EndNode startEnd;
    class C1,S1,S3,C2,S4 action;
    class S2 decision;
```

---

### 5.5 Activity Diagram: Mencari Work Order

```mermaid
flowchart TD
    Start([● Mulai]) --> A1[Masukkan Kata Kunci Pencarian Pada Kolom Input<br/>(Contoh: ID Work Order / Nama Pelanggan)]
    A1 --> S1[Sistem Memfilter Data Work Order Sesuai Kata Kunci]
    S1 --> S2{Data Sesuai Ditemukan?}
    
    S2 -->|Ya| S3[Sistem Menampilkan Daftar Hasil Pencarian yang Relevan]
    S3 --> EndNode([◉ Selesai])
    
    S2 -->|Tidak| S4[Sistem Menampilkan Pesan Peringatan: Data Tidak Ditemukan]
    S4 --> EndNode

    classDef startEnd fill:#E8F5E9,stroke:#2E7D32,stroke-width:2px,color:#1B5E20;
    classDef action fill:#FFFFFF,stroke:#37474F,stroke-width:1.5px,color:#263238;
    classDef decision fill:#FFF9C4,stroke:#FBC02D,stroke-width:2px,color:#F57F17;
    
    class Start,EndNode startEnd;
    class A1,S1,S3,S4 action;
    class S2 decision;
```

---

### 5.6 Activity Diagram: Mengenerate QR (Inklusi Otomatis Work Order)

```mermaid
flowchart TD
    Start([● Mulai]) --> S1[Sistem Mendeteksi Work Order Berhasil Dibuat]
    S1 --> S2[Sistem Mengambil Nomor ID Work Order yang Sedang Diproses]
    S2 --> S3[Sistem Mengenerate Gambar QR Code Untuk Terhubung ke ID WO]
    S3 --> S4[Sistem Menyimpan & Menautkan QR Code ke Database Pesanan]
    S4 --> S5[Sistem Menyediakan QR Code Untuk Dilihat / Diunduh]
    S5 --> EndNode([◉ Selesai])

    classDef startEnd fill:#E8F5E9,stroke:#2E7D32,stroke-width:2px,color:#1B5E20;
    classDef action fill:#FFFFFF,stroke:#37474F,stroke-width:1.5px,color:#263238;
    
    class Start,EndNode startEnd;
    class S1,S2,S3,S4,S5 action;
```

---

### 5.7 Activity Diagram: Membuat Nota Pembayaran

```mermaid
flowchart TD
    Start([● Mulai]) --> A1[Admin Memilih Work Order yang Siap Ditagihkan]
    A1 --> S1[Sistem Menampilkan Rincian Biaya Pesanan Pelanggan]
    S1 --> A2[Admin Menekan Tombol: Buat / Cetak Nota]
    A2 --> S2{Data Rincian Biaya Sudah Lengkap & Valid?}
    
    S2 -->|Tidak| S3[Sistem Tampilkan Peringatan: Lengkapi Rincian Biaya Dahulu]
    S3 --> EndNode([◉ Selesai])
    
    S2 -->|Ya| S4[Sistem Menghitung Total Biaya Akhir]
    S4 --> S5[Sistem Menghasilkan Dokumen Nota Resmi Format PDF / Siap Cetak]
    S5 --> S6[Sistem Menampilkan Pratinjau Dokumen Nota untuk Dikonfirmasi]
    S6 --> EndNode

    classDef startEnd fill:#E8F5E9,stroke:#2E7D32,stroke-width:2px,color:#1B5E20;
    classDef action fill:#FFFFFF,stroke:#37474F,stroke-width:1.5px,color:#263238;
    classDef decision fill:#FFF9C4,stroke:#FBC02D,stroke-width:2px,color:#F57F17;
    
    class Start,EndNode startEnd;
    class A1,S1,A2,S3,S4,S5,S6 action;
    class S2 decision;
```

---

## 6. Kumpulan Sequence Diagram (Bab 3.2 SRS)

### 6.1 Sequence Diagram: Melakukan Login

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

### 6.2 Sequence Diagram: Melakukan CRUD Work Order

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

### 6.3 Sequence Diagram: Mengelola Progress

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

### 6.4 Sequence Diagram: Melihat Progress (Pelanggan)

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

### 6.5 Sequence Diagram: Mencari Work Order

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

### 6.6 Sequence Diagram: Mengenerate QR Code (Inklusi Otomatis Work Order)

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

### 6.7 Sequence Diagram: Membuat Nota Pembayaran

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
