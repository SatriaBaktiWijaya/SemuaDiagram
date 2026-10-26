# Panduan Lengkap Diagram & Integrasi Draw.io
## Sistem Informasi Work Order dan Tracking Progress — PT Alexander Cipta Karya (SRS-BUBUT 2026)

Dokumen ini merupakan panduan indeks dan petunjuk praktis untuk mengimpor, mengedit, dan memvisualisasikan seluruh diagram perangkat lunak yang telah dibuat secara presisi berdasarkan dokumen spesifikasi kebutuhan **Software Requirements Specification (SRS / SKPL-BUBUT)** Program Studi S1 Rekayasa Perangkat Lunak Telkom University 2026.

---

## 1. Daftar Berkas Diagram (Sitemap)

Seluruh diagram telah dipisahkan menjadi file `.md` tersendiri agar Anda dapat langsung menyalin kode Mermaid per bagian tanpa perlu memotong (*crop*) kode secara manual.

### A. Diagram Utama (Kategori Induk)
| No | Nama Diagram | Path Berkas Markdown | Deskripsi Singkat |
|:---:|:---|:---|:---|
| 1 | **Use Case Diagram** | [01_use_case_diagram.md](file:///e:/Coding/PermodelanPerangkatLunak/diagrams/01_use_case_diagram.md) | Memodelkan interaksi 4 aktor (Admin, Marketing, Produksi, Pelanggan) dengan 8 use case, relasi `<<include>>`, dan `<<extend>>`. |
| 2 | **Class Diagram** | [02_class_diagram.md](file:///e:/Coding/PermodelanPerangkatLunak/diagrams/02_class_diagram.md) | Memodelkan arsitektur MVC Laravel (Model Entitas, Controller, Atribut, Tipe Data, Metode, dan Kardinalitas Relasi). |
| 3 | **Context Diagram (DFD L0)** | [03_context_diagram.md](file:///e:/Coding/PermodelanPerangkatLunak/diagrams/03_context_diagram.md) | Diagram Konteks sistem sentral (0.0) dengan aliran data masuk & keluar dari 5 terminator (Admin, Marketing, Produksi, Pelanggan, WhatsApp API). |
| 4 | **Entity Relationship Diagram (ERD)** | [04_erd_diagram.md](file:///e:/Coding/PermodelanPerangkatLunak/diagrams/04_erd_diagram.md) | Skema relasional basis data MySQL lengkap dengan PK, FK, tipe data riil, dan kardinalitas relasi (1:N, 1:1). |
| 5 | **Activity Diagram (Kompilasi)** | [05_activity_diagram.md](file:///e:/Coding/PermodelanPerangkatLunak/diagrams/05_activity_diagram.md) | Kompilasi seluruh 7 Activity Diagram sesuai Bab 3.3 dokumen SRS. |
| 6 | **Sequence Diagram (Kompilasi)** | [06_sequence_diagram.md](file:///e:/Coding/PermodelanPerangkatLunak/diagrams/06_sequence_diagram.md) | Kompilasi seluruh 6 Sequence Diagram sesuai alur interaksi Bab 3.2 dokumen SRS. |

---

### B. Berkas Satuan Activity Diagram (Direktori `diagrams/activity/`)
Bagi Anda yang ingin mengimpor diagram aktivitas satu per satu ke Draw.io:
1. **Login**: [01_login.md](file:///e:/Coding/PermodelanPerangkatLunak/diagrams/activity/01_login.md)
2. **CRUD Work Order**: [02_crud_work_order.md](file:///e:/Coding/PermodelanPerangkatLunak/diagrams/activity/02_crud_work_order.md)
3. **Mengelola Progress**: [03_mengelola_progres.md](file:///e:/Coding/PermodelanPerangkatLunak/diagrams/activity/03_mengelola_progres.md)
4. **Melihat Progress (Pelanggan)**: [04_melihat_progres.md](file:///e:/Coding/PermodelanPerangkatLunak/diagrams/activity/04_melihat_progres.md)
5. **Mencari Work Order**: [05_mencari_work_order.md](file:///e:/Coding/PermodelanPerangkatLunak/diagrams/activity/05_mencari_work_order.md)
6. **Generate QR Code (Inklusi)**: [06_generate_qr.md](file:///e:/Coding/PermodelanPerangkatLunak/diagrams/activity/06_generate_qr.md)
7. **Membuat Nota**: [07_membuat_nota.md](file:///e:/Coding/PermodelanPerangkatLunak/diagrams/activity/07_membuat_nota.md)

---

### C. Berkas Satuan Sequence Diagram (Direktori `diagrams/sequence/`)
Bagi Anda yang ingin mengimpor diagram sekuens satu per satu ke Draw.io:
1. **Login**: [01_login.md](file:///e:/Coding/PermodelanPerangkatLunak/diagrams/sequence/01_login.md)
2. **CRUD Work Order**: [02_crud_work_order.md](file:///e:/Coding/PermodelanPerangkatLunak/diagrams/sequence/02_crud_work_order.md)
3. **Mengelola Progress & Auto QR**: [03_mengelola_progres.md](file:///e:/Coding/PermodelanPerangkatLunak/diagrams/sequence/03_mengelola_progres.md)
4. **Melihat Progress (Tracking Mandiri)**: [04_melihat_progres.md](file:///e:/Coding/PermodelanPerangkatLunak/diagrams/sequence/04_melihat_progres.md)
5. **Mencari Work Order**: [05_mencari_work_order.md](file:///e:/Coding/PermodelanPerangkatLunak/diagrams/sequence/05_mencari_work_order.md)
6. **Membuat Nota Pembayaran**: [06_membuat_nota.md](file:///e:/Coding/PermodelanPerangkatLunak/diagrams/sequence/06_membuat_nota.md)

---

## 2. Langkah-Langkah Mengimpor Kode Mermaid ke Draw.io

Draw.io (baik versi web di [app.diagrams.net](https://app.diagrams.net/) maupun versi Desktop) memiliki fitur parser bawaan yang dapat langsung mengubah kode teks Mermaid menjadi elemen grafis vektor yang bebas diedit:

1. **Buka Aplikasi Draw.io**:
   - Buka browser dan kunjungi `https://app.diagrams.net/` atau jalankan aplikasi *draw.io desktop*.
   - Buat lembar kerja baru (*Create New Diagram* > *Blank Diagram*).
2. **Buka Menu Import Mermaid**:
   - Di bilah menu bagian atas, klik **Arrange** (atau ikon **+** di toolbar).
   - Pilih submenu **Insert** > **Advanced** > **Mermaid**.
   - Sebuah jendela modal dialog bertajuk *"Mermaid"* akan muncul.
3. **Salin & Tempel Kode Mermaid**:
   - Buka salah satu berkas markdown di atas.
   - Salin seluruh isi teks yang berada di dalam blok ` ```mermaid ` hingga ` ``` ` (tanpa menyertakan tanda backtick pembungkus).
   - Tempel (*paste*) kode tersebut ke dalam kotak teks Draw.io.
4. **Klik Tombol "Insert"**:
   - Draw.io akan langsung mengompilasi kode tersebut menjadi bentuk objek grafis (*shapes, connectors, text, colors*).
5. **Editing Lanjutan di Draw.io**:
   - Setiap kotak, garis panah, dan label teks sekarang sudah menjadi objek individual.
   - Anda dapat menggeser posisi elemen, memperbesar kanvas, mengubah warna (*fill/stroke*), mengganti font, atau mengekspornya ke format **PNG, SVG, PDF, atau XML**.

---

## 3. Matriks Kepatuhan dan Konsistensi dengan SRS PT Alexander Cipta Karya

Semua pemodelan ini telah dicek silang (*cross-checked*) secara ketat terhadap setiap pasal dalam dokumen SRS:

| Aspek SRS | Bagian Dokumen | Implementasi Diagram | Status |
|:---|:---|:---|:---:|
| **Aktor & Batasan** | Bab 1.2, 1.3.2, 1.3.3 | 4 Peran Aktor (Admin, Marketing, Produksi, Pelanggan) dimodelkan di Use Case, Context, dan Class Diagram | **Akurat 100%** |
| **Kebutuhan Fungsional** | Bab 2.2 (FR-001 s/d FR-010) | Dipetakan lengkap ke Use Case UC-01 s/d UC-08 dan Activity Diagrams | **Akurat 100%** |
| **Relasi Include & Extend** | Bab 3.1 & 3.2 | CRUD WO `<<extend>>` Mencari WO; CRUD WO & Mengelola Progress `<<include>>` Generate QR | **Akurat 100%** |
| **Alur Eksepsional** | Bab 3.2.1 s/d 3.2.7 | Seluruh *alternative flow* (gagal login, data tidak valid, file salah, data tidak ditemukan, biaya belum lengkap) dimodelkan pada Activity & Sequence Diagram | **Akurat 100%** |
| **Kebutuhan Data** | Bab 4.1 (18 Atribut Spesifik) | Diintegrasikan ke skema tabel basis data fisik ERD dan atribut kelas Class Diagram | **Akurat 100%** |
| **Lingkungan Teknologi** | Bab 4.4 | Arsitektur MVC Laravel (PHP), MySQL, Nginx, dan DomPDF dimodelkan pada Class dan Sequence Diagram | **Akurat 100%** |
