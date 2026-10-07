# DQLab Store – Sales Performance Analysis (2009–2012)

Analisis data penjualan DQLab Store untuk menjawab: **bagaimana mengalokasikan ulang resource ke kategori yang profitable dan mengoptimalkan strategi diskon untuk memaksimalkan profit?** Seluruh analisis dikerjakan di **Microsoft Excel** dengan formula hidup (SUMIFS, COUNTIFS, AVERAGEIFS, SUMPRODUCT, INDEX/MATCH), bukan angka hasil hardcode.

![Dashboard](images/dashboard.png)

## Ringkasan Temuan

| Pertanyaan | Temuan |
|---|---|
| Kategori paling profitable? | **Technology** (±Rp1,65 miliar, 38% profit), diikuti Furniture (35%) dan Office Supplies (27%). Office Supplies punya order terbanyak, tetapi profit per order hanya ±Rp0,38 juta vs ±Rp1,3 juta di dua kategori lain. |
| Return risk? | Relatif seragam: 10,5% (Technology) – 11,8% (Office Supplies). |
| Apakah diskon efektif? | **Tidak.** Diskon memakan ±15–17% gross profit (total ±Rp852 juta selama 4 tahun), tetapi qty/order dan sales/order sama saja di semua level diskon (\|r\| ≤ 0,06). |
| Kategori paling sensitif diskon? | **Tidak ada** kategori yang sensitif (semua \|r\| < 0,10). |

## Rekomendasi

| Kategori | Alokasi marketing | Cap diskon | Δ Profit (4 thn) |
|---|---|---|---|
| Technology | 38% | 5% | +Rp53 juta (+3,2%) |
| Furniture | 35% | 3% | +Rp88 juta (+5,8%) |
| Office Supplies | 27% | 5% | +Rp40 juta (+3,5%) |
| **Total** | **100%** | | **+Rp181 juta (+4,2%)** |

- Alokasi marketing proporsional dengan share profit (profit sudah net diskon dan return, sehingga risiko return ikut terhitung).
- Dampak di atas mengasumsikan **5% volume hilang** pada pesanan yang diskonnya dipotong. Tanpa volume loss, dampaknya +Rp293 juta (+6,8%).
- **Break-even volume loss ±13%**: kebijakan tetap menguntungkan selama volume yang hilang di bawah angka itu. Pada 20% volume loss, dampaknya menjadi negatif (−Rp156 juta).
- Disarankan roll-out bertahap lewat A/B test, dimulai dari Furniture (biaya diskon per order terbesar).

## ⚠️ Keterbatasan Penting

1. **Dataset tidak memiliki biaya (HPP/COGS)**, sehingga profit sebenarnya tidak dapat dihitung. Analisis memakai **Profit Proxy** = Sales Finished × Gross Margin − Diskon, dengan **gross margin 30% flat sebagai asumsi** (bukan data). Karena margin sama untuk semua kategori, perbedaan profit antar kategori hanya berasal dari revenue, diskon, dan return. Ganti margin di sheet `Asumsi` dengan angka asli dan seluruh workbook akan menghitung ulang.
2. **Analisis observasional**, bukan eksperimen. Diskon tersebar hampir merata 0–10% sehingga mendekati "eksperimen alami", tetapi kausalitas tetap perlu divalidasi dengan A/B test.
3. Ada indikasi return rate lebih rendah pada diskon tinggi untuk Office Supplies (13,5% pada diskon 0% → 8,6% pada 7%+) dan Technology. Efek ini tidak dimasukkan dalam skenario; bila nyata, penghematan dari pemotongan diskon akan lebih kecil.
4. Data berhenti di 2012; angka Rupiah adalah basis historis, bukan proyeksi.

## Dataset

`dqlab_sales.xlsx`: 5.499 baris × 10 kolom, periode 1 Jan 2009 – 30 Des 2012, 3 kategori produk (Furniture, Office Supplies, Technology) dengan 17 sub-kategori. Mata uang IDR.

| Kolom | Keterangan |
|---|---|
| order_id | Nomor order |
| order_status | Order Finished / Returned / Cancelled |
| customer | Nama pelanggan |
| order_date | Tanggal order |
| order_quantity | Jumlah barang |
| sales | Penjualan (IDR) |
| discount | Persentase diskon |
| discount_value | sales × discount (IDR) |
| product_category, product_sub_category | Kategori dan sub-kategori produk |

**Pembersihan data:** tidak ada nilai kosong; `discount_value` = `sales × discount` di seluruh baris; 3 order *Cancelled* (±Rp39 juta, semua Office Supplies) dikeluarkan; 3 `order_id` ganda (bukan duplikat penuh) dibiarkan; 3 order Furniture berdiskon di atas 10% dipertahankan dan dimasukkan ke bucket 7%+.

## Metodologi

- **Net Revenue** = Σ (sales − discount_value) hanya untuk *Order Finished*. Order *Returned* dianggap di-refund.
- **Return Rate** = Returned Sales ÷ Gross Sales (by value).
- **Profit Proxy** = Sales Finished × Gross Margin − discount_value.
- **Margin Proxy** = Profit Proxy ÷ Gross Sales (Finished + Returned), sehingga return ikut menekan margin.
- **Sensitivitas diskon** = korelasi Pearson antara % diskon dan qty/order serta sales/order per kategori (dihitung manual dengan `SUMPRODUCT`), ditambah perbandingan bucket diskon (0%, 1–3%, 4–6%, 7%+). Dianggap "sensitif" bila \|r\| ≥ 0,10 (aturan praktis, bukan uji formal).
- **Skenario**: diskon dipotong ke cap per kategori, pesanan yang diskonnya dipotong kehilangan sejumlah volume (parameter), lalu profit dibandingkan dengan baseline. Dilengkapi uji sensitivitas 0–20% dan break-even.

## Struktur Workbook (`DQLab_Sales_Analysis.xlsx`)

| Sheet | Isi |
|---|---|
| Dashboard | 6 KPI dan 6 chart |
| Dokumentasi | Tujuan, data, definisi metrik, langkah kerja, temuan, keterbatasan |
| Ringkasan | Jawaban sub-question, alokasi marketing, cap diskon, dampak profit, uji sensitivitas |
| Asumsi | Input yang bisa diubah: margin per kategori, cap diskon, volume loss, ambang korelasi |
| Kategori | Revenue, margin, return per kategori dan sub-kategori, tren tahunan |
| Diskon | Qty, sales, dan return per bucket diskon; korelasi; biaya diskon |
| Data | Data mentah + 12 kolom helper berformula |

## Cara Memakai

1. Buka `DQLab_Sales_Analysis.xlsx` di Excel (atau LibreOffice).
2. Mulai dari sheet **Dashboard**, lalu **Ringkasan**.
3. Ubah sel biru/kuning di sheet **Asumsi** (margin, cap diskon, volume loss) untuk mencoba skenario lain; semua sheet dan chart ikut berubah.

## Validasi

Workbook dihitung ulang tanpa error (±66 ribu formula) dan angka kunci (profit baseline, profit skenario, penghematan diskon, korelasi) dicocokkan dengan perhitungan independen di Python/pandas dengan selisih nol.

## Langkah Lanjutan

- Isi HPP/margin asli per kategori dan sub-kategori.
- Analisis pelanggan (repeat vs one-time).
- Analisis diskon per sub-kategori.
- A/B test cap diskon sebelum roll-out penuh.

## Struktur Repository

```
.
├── README.md
├── DQLab_Sales_Analysis.xlsx
└── images/
    └── dashboard.png
```
