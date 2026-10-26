# Activity Diagram: Mencari Work Order (SRS 3.3.5)

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
