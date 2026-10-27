# Sequence Diagram: Mengenerate QR Code (SRS 3.2.6 - Inklusi Otomatis Work Order)

Diagram ini memodelkan fungsi inklusi otomatis sistem (*include*) yang dipicu **1 kali di awal** saat pembuatan data Work Order baru berhasil dilakukan, sebagaimana dimodelkan pada diagram alur Draw.io (Sistem mendeteksi Work Order berhasil dibuat -> Ambil ID WO -> Generate Gambar QR Code -> Simpan & tautkan ke database -> Sediakan untuk dilihat/diunduh).

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
