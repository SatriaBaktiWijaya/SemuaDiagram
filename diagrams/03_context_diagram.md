# 03. Context Diagram (Diagram Konteks / DFD Level 0) — Sistem Informasi PT Alexander Cipta Karya

Diagram Konteks (*Context Diagram* / DFD Level 0) ini menggambarkan batasan sistem (*system boundary*), seluruh entitas eksternal (*external entities/terminators*) yang berinteraksi langsung dengan sistem, serta aliran data masuk (*input*) dan aliran informasi keluar (*output*) sesuai Bab 1.2, Bab 4.1, dan Bab 4.2 dokumen SRS.

---

## 1. Kode Mermaid (Siap Di-import ke Draw.io)

> **Cara Import ke Draw.io:**
> 1. Buka [draw.io](https://app.diagrams.net/).
> 2. Klik menu **Arrange** > **Insert** > **Advanced** > **Mermaid**.
> 3. Salin (*copy*) kode di dalam blok di bawah ini dan tempel (*paste*) ke Draw.io.
> 4. Klik **Insert**.

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

## 2. Tabel Rincian Aliran Data (Data Flow Breakdown)

### A. Aliran Data Masuk (*Input Flows*)

| Entitas Asal | Nama Aliran Data (*Data Input*) | Deskripsi / Parameter Data |
|:---|:---|:---|
| **Admin** | Kredensial Login | `username`, `password` |
| | Input/Update Data Work Order | `id_pelanggan`, `nama_barang`, `spesifikasi_barang`, `jumlah`, `status_wo` |
| | Kata Kunci Pencarian | Parameter teks (Nomor ID WO atau Nama Pelanggan) |
| | Data Biaya & Konfirmasi Nota | `id_work_order`, `rincian_biaya`, status penerbitan tagihan |
| **Marketing** | Kredensial Login | `username`, `password` |
| | Pembuatan Work Order Baru | Data identitas pemesan (`nama`, `no_telp`, `alamat`), instruksi pesanan bubut |
| | Kata Kunci Pencarian | Parameter teks pencarian pesanan |
| **Produksi** | Kredensial Login | `username`, `password` |
| | Update Status Pengerjaan | `id_work_order`, `status_progres` (Received/Process/Finished), catatan teknis |
| | Dokumentasi Pengerjaan | File unggahan foto/dokumen kondisi riil barang pengerjaan |
| | Konfirmasi Penyelesaian | Aksi validasi status final pekerjaan bubut telah selesai |
| **Pelanggan** | Token / Scan QR Code | String URL unik atau token ID Work Order hasil pindai kamera |
| | Klik Konsultasi WhatsApp | Pemicu (*event*) klik tombol kontak resmi perusahaan |

---

### B. Aliran Data Keluar (*Output Flows*)

| Entitas Tujuan | Nama Aliran Data (*Data Output*) | Deskripsi / Manfaat Informasi |
|:---|:---|:---|
| **Admin** | Info Autentikasi & Dashboard | Notifikasi status berhasil masuk sistem dan menu navigasi kerja |
| | Data Lengkap Work Order | Daftar pesanan aktif, arsip pesanan, serta riwayat progres bengkel |
| | Dokumen Nota Pembayaran | File PDF nota tagihan resmi yang siap dicetak untuk diserahkan ke pelanggan |
| **Marketing** | Gambar & Tautan QR Code | File QR Code yang siap dibagikan ke pelanggan untuk tracking mandiri |
| | Status & Progres Pesanan | Informasi terkini mengenai pesanan pelanggan untuk koordinasi pelayanan |
| **Produksi** | Daftar Tugas Work Order | Antrean pekerjaan mesin bubut yang siap diproses beserta spesifikasi teknis |
| | Notifikasi Sukses Update | Konfirmasi bahwa foto progres berhasil disimpan ke server |
| **Pelanggan** | Halaman Tracking Progres | Status pengerjaan terkini, tanggal update, dan foto dokumentasi bengkel |
| | Tampilan Rincian Biaya | Detail biaya pengerjaan dan status pembayaran tagihan |
| **WhatsApp API** | Parameter Redireksi WA | Membuka percakapan langsung ke nomor WhatsApp resmi PT Alexander Cipta Karya |
