# Sequence Diagram: Melakukan CRUD Work Order & Auto Generate QR di Awal (SRS 3.2.2 & 3.2.6)

> **Catatan Alur:** Diagram ini memodelkan siklus hidup Work Order oleh Admin/Marketing. Saat Work Order baru dibuat (*Create*), sistem secara otomatis mengenerate QR Code pelacakan **1 kali di awal**, menautkannya ke database pesanan, dan menyediakannya untuk dilihat, diunduh, atau dicetak pada lembar kerja/nota.

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
