# Proyek Business Process Management (BPM) - Kelompok 5

Repositori ini berisi dokumentasi, model proses bisnis (BPMN & DMN), formulir, serta tangkapan layar (screenshots) dari implementasi proyek BPM Kelompok 5.

## 👥 Anggota Kelompok 5
1. **[Nama Anggota 1]** - [NIM]
2. **[Nama Anggota 2]** - [NIM]
3. **[Nama Anggota 3]** - [NIM]
4. **[Nama Anggota 4]** - [NIM]

---

## 📁 Struktur Folder

Berikut adalah penjelasan mengenai struktur folder proyek ini:

```text
kelompok5/
├── laporan/
│   └── laporan-proyek-bpm.pdf    # Laporan lengkap proyek dalam format PDF
├── model/
│   ├── as-is.bpmn                # Model proses bisnis saat ini (sebelum otomasi)
│   ├── to-be.bpmn                # Model proses bisnis usulan (setelah otomasi)
│   ├── decision.dmn              # Aturan keputusan (Decision Model and Notation)
│   └── forms/                    # Formulir Camunda (.form / HTML) yang digunakan
├── screenshots/
│   ├── deployment/               # Tangkapan layar proses deployment ke Camunda
│   ├── tasklist/                 # Tangkapan layar antarmuka Tasklist saat dijalankan
│   ├── cockpit/                  # Tangkapan layar monitoring proses di Cockpit
│   └── simulation/               # Tangkapan layar hasil simulasi proses (jika ada)
├── video/
│   └── link-video.txt            # Tautan video presentasi/demo proyek
└── README.md                     # Panduan dan deskripsi proyek ini
```

---

## 📝 Deskripsi Proyek
*Tuliskan deskripsi singkat mengenai proses bisnis yang Anda analisis dan otomasikan. Contoh:*
> Proyek ini bertujuan untuk mengotomasi proses bisnis **[Nama Proses, misal: Pengajuan Cuti Karyawan]** pada **[Nama Instansi/Perusahaan]** menggunakan Camunda BPM platform untuk meningkatkan efisiensi, transparansi, dan kecepatan proses.

---

## 🛠️ Teknologi yang Digunakan
* **Camunda Modeler** (untuk mendesain BPMN & DMN)
* **Camunda Platform 7 / 8** (Process Engine)
* **[Teknologi lain, misal: Java, Spring Boot, Node.js, atau Camunda Forms]**

---

## 🚀 Cara Menjalankan Model
1. Unduh dan pasang **Camunda Modeler**.
2. Clone repositori ini:
   ```bash
   git clone <url-repo-github-anda>
   ```
3. Buka file `.bpmn` dan `.dmn` dari folder `model/` menggunakan Camunda Modeler.
4. Lakukan deployment model ke Camunda Engine lokal Anda.
5. Akses **Camunda Tasklist** (`http://localhost:8080/camunda/app/tasklist`) untuk memulai dan menguji proses.

---

## 🎥 Video Demonstrasi
Tautan penjelasan dan demo jalannya sistem dapat diakses pada file [video/link-video.txt](./video/link-video.txt) atau langsung di: **[Tempel Link Video Anda di Sini]**
