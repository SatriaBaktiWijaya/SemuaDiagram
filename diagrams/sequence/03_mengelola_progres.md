# Sequence Diagram: Mengelola Progress (SRS 3.2.3)

> **Catatan Alur:** Proses update progres oleh Bagian Produksi **hanya memperbarui data status dan mengunggah dokumentasi pengerjaan**, tidak mengenerate QR Code baru (QR Code pelacakan telah digenerate 1 kali di awal saat Work Order pertama kali dibuat).

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
