# 05. Activity Diagram — Sistem Informasi PT Alexander Cipta Karya

Dokumen ini memuat seluruh pemodelan alur kerja (*Activity Diagram*) sistem informasi PT Alexander Cipta Karya yang disusun berdasarkan Bab 3.3 dokumen SRS (SKPL-BUBUT halaman 33–40).

Terdapat 7 Activity Diagram lengkap:
1. **AD-01: Melakukan Login (3.3.1)**
2. **AD-02: Melakukan CRUD Work Order (3.3.2)**
3. **AD-03: Mengelola Progress (3.3.3)**
4. **AD-04: Melihat Progress / Pelanggan (3.3.4)**
5. **AD-05: Mencari Work Order (3.3.5)**
6. **AD-06: Mengenerate QR (Inklusi) (3.3.6)**
7. **AD-07: Membuat Nota (3.3.7)**

> **Tips Import ke Draw.io:**
> Anda dapat menyalin masing-masing blok kode Mermaid di bawah ini, lalu pada draw.io pilih **Arrange** > **Insert** > **Advanced** > **Mermaid** untuk mengimpor diagram yang diinginkan satu per satu. File satuan mandiri juga tersedia pada direktori `diagrams/activity/`.

---

## 1. AD-01: Melakukan Login (SRS 3.3.1)

Diagram aktivitas otentikasi pengguna internal (Admin, Marketing, Produksi) dengan validasi kredensial dan penanganan kondisi gagal (*alternative flow*).

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

## 2. AD-02: Melakukan CRUD Work Order (SRS 3.3.2)

Menggambarkan 4 cabang percabangan aksi pengelolaan Work Order oleh Admin/Marketing: Tambah, Ubah, Hapus, dan Lihat detail.

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

## 3. AD-03: Mengelola Progress (SRS 3.3.3)

Diagram aktivitas bagian Produksi dalam memperbarui progres pengerjaan barang, mengunggah bukti foto pengerjaan, otomatis memicu generate QR, dan menyelesaikan pesanan.

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
    S4 --> S5[Sistem Mengenerate / Memperbarui QR Code Tracking]
    S5 --> S6[Sistem Tampilkan Notifikasi Berhasil Simpan]
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
    class P1,S1,P2,P3,P4,S3,S4,S5,S6,P6,S7,S8,S9,S10 action;
    class S2,P5,P7 decision;
```

---

## 4. AD-04: Melihat Progress (Pelanggan) (SRS 3.3.4)

Diagram aktivitas pelanggan saat melacak status pesanan kerja secara mandiri melalui pemindaian QR Code tracking atau membuka URL pelacakan.

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

## 5. AD-05: Mencari Work Order (SRS 3.3.5)

Diagram aktivitas pencarian cepat data Work Order oleh Admin atau Marketing menggunakan kata kunci tertentu.

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

## 6. AD-06: Mengenerate QR (Inklusi Otomatis) (SRS 3.3.6)

Diagram alur proses otomatis sistem (*sub-process / include*) dalam menciptakan kode batang 2D (QR Code) unik yang ditautkan ke ID Work Order.

```mermaid
flowchart TD
    Start([● Mulai]) --> S1[Sistem Menerima Trigger dari Pembaruan / Pembuatan Data Progres]
    S1 --> S2[Sistem Mengambil Nomor ID Work Order yang Sedang Diproses]
    S2 --> S3[Sistem Mengenerate Gambar QR Code Unik Terhubung ke ID WO]
    S3 --> S4[Sistem Menyimpan & Menautkan QR Code ke Database Pesanan]
    S4 --> S5[Sistem Menyediakan QR Code untuk Dilihat / Diunduh]
    S5 --> EndNode([◉ Selesai])

    classDef startEnd fill:#E8F5E9,stroke:#2E7D32,stroke-width:2px,color:#1B5E20;
    classDef action fill:#FFFFFF,stroke:#37474F,stroke-width:1.5px,color:#263238;
    
    class Start,EndNode startEnd;
    class S1,S2,S3,S4,S5 action;
```

---

## 7. AD-07: Membuat Nota Pembayaran (SRS 3.3.7)

Diagram aktivitas Admin dalam memeriksa kelengkapan rincian biaya pengerjaan bubut, melakukan perhitungan total kalkulasi, dan mencetak dokumen tagihan resmi (*invoice* PDF).

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
