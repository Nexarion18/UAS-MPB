# Proyek Business Process Management (BPM) - Kelompok 5

Repositori ini berisi dokumentasi, model proses bisnis (BPMN & DMN), formulir, serta tangkapan layar (screenshots) dari implementasi proyek BPM Kelompok 5.

## 👥 Anggota Kelompok 5

1. **Fendi Permadi** - 2310512093
2. **Rifqi Arrayan Muttaqien** - 2310512124
3. **Naufan Fardan Nurhuda** - 2310512112
4. **Ariq Adlan Hadiyan** - 2310512080

---

## 📝 Deskripsi Proyek

Proyek ini bertujuan untuk mengotomasi proses bisnis **Warehouse Management System (WMS)** pada **Satglow** menggunakan CIB Seven (Camunda 7 Fork) untuk meningkatkan efisiensi, transparansi, dan kecepatan proses gudang. Sistem mencakup 5 proses bisnis utama yang terintegrasi dengan tabel keputusan DMN dan notifikasi real-time via Discord Webhook.

---

## 🛠️ Tech Stack

| Komponen | Detail |
|----------|--------|
| Process Engine | CIB Seven (Camunda 7 Fork) |
| Server | `http://cibseven2.foul.one` |
| Tenant ID | `55555` |
| Modeler | Camunda Modeler 5.46.1 |
| DMN Version | DMN 1.3 (`http://www.omg.org/spec/DMN/20191111/MODEL/`) |
| Integrasi Eksternal | Discord Webhook (HTTP Connector) |
| Candidate Groups | `gudangSG`, `adminSG`, `managerSG` |

---

## 📁 Struktur Folder

```text
kelompok5/
├── model/
│   ├── as-is/                              # Model proses bisnis SEBELUM otomasi
│   │   ├── satglow_wms_asis_inbound.bpmn
│   │   └── satglow_wms_outbound_asis.bpmn
│   ├── to-be/                              # Model proses bisnis SETELAH otomasi (DEPLOY INI)
│   │   ├── inbound_to_be(TOBE1).bpmn       # Proses 8 - Inbound
│   │   ├── tobe_outbound_v2.bpmn           # Proses 7 - Outbound & FEFO
│   │   ├── Proses4_PredictiveRestocking.bpmn
│   │   ├── Proses5_StockOpnameMultiGudang.bpmn
│   │   └── tobe_monitoring_v2.bpmn         # Proses 6 - AI Monitoring (uses DMN)
│   └── forms dan dmn/                      # Form & DMN files (DEPLOY INI JUGA)
│       ├── diagram_1.dmn                   # Decision Table: Penentuan Diskon Clearance
│       ├── input_inbound_tobe.form         # Form_InputInbound
│       ├── scan_barcode.form               # Form_ScanBarcode
│       ├── form_4predicativeforstocking.form # Form_ReviewPO
│       ├── review_promo_sg.form            # Form_ReviewPromo
│       └── scan_fisik_gudang.form          # Form_ScanFisik
├── screenshots/
│   ├── deployment/
│   ├── tasklist/
│   ├── cockpit/
│   └── simulation/
├── laporan/
│   └── laporan-proyek-bpm.pdf
├── video/
│   └── link-video.txt
└── README.md
```

---

## 🗺️ BPMN → Form → DMN Mapping

| Proses | File BPMN | Process ID | Form | DMN |
|--------|-----------|------------|------|-----|
| P8 - Inbound | `inbound_to_be(TOBE1).bpmn` | `Process_Satglow_inbound` | `Form_InputInbound` | - |
| P7 - Outbound & FEFO | `tobe_outbound_v2.bpmn` | `Process_Outbound_WMS` | `Form_ScanBarcode` | - |
| P4 - Predictive Restocking | `Proses4_PredictiveRestocking.bpmn` | `Process_PredictiveRestocking` | `Form_ReviewPO` | - |
| P5 - Stock Opname | `Proses5_StockOpnameMultiGudang.bpmn` | `Process_StockOpname` | `Form_ScanFisik` | - |
| P6 - AI Monitoring | `tobe_monitoring_v2.bpmn` | `Process_Satglow_monitoring` | `Form_ReviewPromo` | `Decision_Diskon` |

### DMN Detail

| Key | File | Input | Output | Rules |
|-----|------|-------|--------|-------|
| `Decision_Diskon` | `diagram_1.dmn` | `sisaHari` (integer) | `statusPromo` (string) | ≤30 → "Diskon 70%", [31..60] → "Diskon 50%", [61..90] → "Diskon 30%" |

---

## 🔔 Discord Webhook Integration

Semua Service Task terintegrasi Discord menggunakan **HTTP Connector** dengan JavaScript script untuk payload.

### Daftar Task yang Mengirim Notifikasi Discord

| Proses | Task ID | Trigger / Kondisi |
|--------|---------|-------------------|
| P8 Inbound | `Task_GenerateForm` | Setelah staff mengakses modul |
| P8 Inbound | `Task_AlokasiIDBatch` | Gateway `isValid == true` |
| P8 Inbound | `Task_TampilkanAlertError` | Gateway `isValid == false` |
| P8 Inbound | `Task_SimpanDatabase` | Akhir proses (sukses) |
| P7 Outbound | `Task_BuatPickingList` | Setelah FEFO selesai |
| P7 Outbound | `Task_FinalUpdate` | Akhir proses (sukses) |
| P4 Restocking | `Task_NotifStokAman` | Gateway `stokCukup == true` |
| P4 Restocking | `Task_NotifPODitolak` | Gateway `managerPODecision == 'tolak'` |
| P4 Restocking | `Task_KirimPOSupplier` | Gateway `managerPODecision == 'setuju'` |
| P5 Opname | `Task_OtomatisKomparasiStok` | Setelah scan fisik (dynamic: ada/tidak selisih) |
| P5 Opname | `Task_NotifAdjustmentDitolak` | Gateway `adjustmentDecision == 'tolak'` |
| P5 Opname | `Task_ExecuteFinalAdjustment` | Gateway `adjustmentDecision == 'setuju'` |
| P6 Monitoring | `Task_GenerateNotifikasi` | Setelah DMN dieksekusi |
| P6 Monitoring | `Task_TutupNotifikasi` | Gateway `managerSetuju == 'tolak'` |
| P6 Monitoring | `Task_UpdateHargaClearance` | Gateway `managerSetuju == 'setuju'` |

---

## 🚀 Deployment Guide

### Via CIB Seven Web UI
1. Buka `http://cibseven2.foul.one/camunda/app/cockpit/`
2. Login dengan akun yang diberikan dosen.
3. Klik **Deployments** → **Create Deployment**.
4. Upload semua file dari `model/to-be/` dan `model/forms dan dmn/`.
5. Set **Tenant ID** = `55555`.
6. Klik **Deploy**.

### Via REST API
```bash
curl -X POST "http://cibseven2.foul.one/engine-rest/deployment/create" \
  -F "deployment-name=satglow-wms-kelompok5" \
  -F "tenant-id=55555" \
  -F "data=@model/to-be/inbound_to_be(TOBE1).bpmn" \
  -F "data=@model/to-be/tobe_outbound_v2.bpmn" \
  -F "data=@model/to-be/Proses4_PredictiveRestocking.bpmn" \
  -F "data=@model/to-be/Proses5_StockOpnameMultiGudang.bpmn" \
  -F "data=@model/to-be/tobe_monitoring_v2.bpmn" \
  -F "data=@model/forms dan dmn/diagram_1.dmn" \
  -F "data=@model/forms dan dmn/input_inbound_tobe.form" \
  -F "data=@model/forms dan dmn/scan_barcode.form" \
  -F "data=@model/forms dan dmn/form_4predicativeforstocking.form" \
  -F "data=@model/forms dan dmn/review_promo_sg.form" \
  -F "data=@model/forms dan dmn/scan_fisik_gudang.form"
```

---

## 📋 Variabel Proses (Cheat Sheet)

### P8 - Inbound

| Variabel | Tipe | Default | Digunakan di |
|----------|------|---------|--------------|
| `konfirmasiTerima` | string | "OK" | Task_TerimaBarang |
| `infoModul` | string | "Klik Complete..." | Task_AksesModulInbound |
| `namaBarang` | string | - | Form_InputInbound |
| `qtyBarang` | string | - | Form_InputInbound |
| `isValid` | boolean | - | Form_InputInbound → Gateway |

### P7 - Outbound & FEFO

| Variabel | Tipe | Default | Digunakan di |
|----------|------|---------|--------------|
| `orderID` | string | "ORD-778899" | Start Event |
| `pickingListInfo` | string | "Ambil 5 Pcs..." | Task_BacaPickingList |
| `konfirmasiAmbil` | string | "OK" | Task_AmbilBarang |
| `cekFisikAdmin` | boolean | true | Task_CrossCheck |
| `adminDecision` | string | "APPROVE" | Task_ApproveResi |

### P4 - Predictive Restocking

| Variabel | Tipe | Default | Digunakan di |
|----------|------|---------|--------------|
| `stokCukup` | boolean | false | Start Event → Gateway |
| `jumlahRestok` | long | 1000 | Start Event |
| `managerPODecision` | string | - | Form_ReviewPO → Gateway |

### P5 - Stock Opname

| Variabel | Tipe | Default | Digunakan di |
|----------|------|---------|--------------|
| `adaSelisih` | boolean | true | Start Event → Gateway |
| `batchID` | string | - | Form_ScanFisik |
| `jumlahFisik` | long | - | Form_ScanFisik |
| `adjustmentDecision` | string | "setuju" | Task_ApproveStockAdjustment → Gateway |

### P6 - AI Monitoring & Clearance (DMN)

| Variabel | Tipe | Default | Digunakan di |
|----------|------|---------|--------------|
| `stokBerisiko` | boolean | true | Start Event → Gateway |
| `sisaHari` | long | 25 | Start Event → DMN Input |
| `statusPromo` | string | *(output DMN)* | DMN Output → Discord payload |
| `managerSetuju` | string | - | Form_ReviewPromo → Gateway |

---

## 🧪 Test Scenarios (Quick Reference)

### Happy Path (Semua Disetujui)

```text
P8: Start → OK → Akses Modul → [DC] → Input (isValid=true) → [DC: Valid] → [DC: Simpan DB] → End
P7: Start (ORD-001) → FEFO → [DC: Picking List] → Pick → Scan → Reserve → CrossCheck → APPROVE → Label → [DC: Final Update] → End
P4: Start (stokCukup=false) → Forecast → Draft PO → Manager (setuju) → [DC: Kirim PO] → End
P5: Start (adaSelisih=true) → Freeze → Scan → [DC: Ada Selisih] → Manager (setuju) → [DC: Adjustment] → End
P6: Start (sisaHari=25) → DMN (Diskon 70%) → [DC: Hasil DMN] → Manager (setuju) → Konfirmasi → [DC: Update Harga] → End
```

### Rejection Path (Ditolak / Aman)

```text
P8: Start → OK → Akses Modul → [DC] → Input (isValid=false) → [DC: Error] → Revisi → Loop ke Input
P4: Start (stokCukup=true) → Forecast → [DC: Stok Aman] → End
P4: Start (stokCukup=false) → Forecast → Draft PO → Manager (tolak) → [DC: PO Rejected] → End
P5: Start (adaSelisih=false) → Freeze → Scan → [DC: Stok Sesuai] → End
P5: Start (adaSelisih=true) → Freeze → Scan → [DC: Ada Selisih] → Manager (tolak) → [DC: Adj Rejected] → End
P6: Start (stokBerisiko=false) → End (Aman)
P6: Start (sisaHari=45) → DMN (Diskon 50%) → [DC: Hasil DMN] → Manager (tolak) → [DC: Clearance Rejected] → End
```

> Keterangan: `[DC: ...]` = Notifikasi Discord terkirim di task tersebut

---

## 🔍 REST API Endpoints (Debugging)

Endpoint yang sering dipakai untuk verifikasi dan debugging di CIB Seven:

```bash
# Cek semua deployment
GET http://cibseven2.foul.one/engine-rest/deployment?tenantIdIn=55555

# Cek semua process definition
GET http://cibseven2.foul.one/engine-rest/process-definition?tenantIdIn=55555

# Cek semua decision definition (DMN)
GET http://cibseven2.foul.one/engine-rest/decision-definition?tenantIdIn=55555

# Cek history eksekusi DMN (bukti DMN jalan)
GET http://cibseven2.foul.one/engine-rest/history/decision-instance?tenantIdIn=55555

# Cek running process instances
GET http://cibseven2.foul.one/engine-rest/process-instance?tenantIdIn=55555

# Cek active user tasks
GET http://cibseven2.foul.one/engine-rest/task?tenantIdIn=55555

# Cek variabel dari process instance tertentu
GET http://cibseven2.foul.one/engine-rest/process-instance/{id}/variables
```

---

## ⚠️ Troubleshooting

### `HTCL-02005 Request url required`
**Penyebab:** Parameter `url` pada HTTP Connector tidak terbaca oleh CIB Seven jika ditulis sebagai plain string.
**Solusi:** Wrap value `url` dan `method` dalam JavaScript script:
```xml
<camunda:inputParameter name="url">
  <camunda:script scriptFormat="javascript">"https://discord.com/api/webhooks/...";</camunda:script>
</camunda:inputParameter>
```

### `no decision definition deployed with key 'Decision_Diskon' and tenant-id '55555'`
**Penyebab:** DMN belum di-deploy dengan Tenant ID yang sama dengan BPMN.
**Solusi:** Deploy ulang `diagram_1.dmn` dengan Tenant ID `55555`. Verifikasi: `GET /engine-rest/decision-definition`.

### `TypeError: (intermediate value).getBytes is not a function`
**Penyebab:** CIB Seven menggunakan GraalVM. Pemanggilan Java class (`java.net.URL`, `.getBytes()`) dalam `<camunda:script>` tidak kompatibel.
**Solusi:** Jangan gunakan Execution Listener + Java interop. Gunakan Service Task + HTTP Connector standar.

### DMN tidak muncul di Cockpit Decisions tab
**Penyebab:** Limitasi UI CIB Seven versi Community/Basic.
**Verifikasi:** DMN tetap berfungsi di backend. Cek via `GET /engine-rest/decision-definition` dan `GET /engine-rest/history/decision-instance`.

---

## ⛔ Known Limitations

1. **Cockpit Basic** tidak menampilkan DMN Decision History secara visual (fitur Enterprise).
2. **HTTP Connector** pada CIB Seven kadang inkonsisten antara plain string vs JavaScript script untuk parameter `url`. Selalu gunakan JavaScript script wrapper.
3. **Folder name** `forms dan dmn` mengandung spasi — bisa menyebabkan issue pada beberapa CLI tools.

---

## 🎥 Video Demonstrasi
Tautan penjelasan dan demo jalannya sistem dapat diakses pada file [video/link-video.txt](./video/link-video.txt) atau langsung di: **[Tempel Link Video Anda di Sini]**
