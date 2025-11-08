# ERD (Entity-Relationship Diagram) - Labaku Aplikasi Pencatatan Keuangan UMKM

Berikut adalah representasi Entity-Relationship Diagram (ERD) untuk aplikasi Labaku, yang menggambarkan entitas utama dan hubungan antar entitas.

---

## 📚 Entitas (Entities) dan Atribut (Attributes)

### 1. Pengguna (Users)
*   **user_id** (PRIMARY KEY, UUID)
*   username (TEXT, UNIQUE)
*   password_hash (TEXT)
*   email (TEXT, UNIQUE, NULLABLE)
*   nama_lengkap (TEXT)
*   tanggal_registrasi (DATETIME)
*   tanggal_last_login (DATETIME, NULLABLE)

### 2. Kategori (Categories)
*   **category_id** (PRIMARY KEY, UUID)
*   user_id (FOREIGN KEY ke Users.user_id)
*   nama_kategori (TEXT, UNIQUE per user_id)
*   tipe_kategori (TEXT, ENUM: 'Pemasukan', 'Pengeluaran')
*   is_default (BOOLEAN, default: FALSE)
*   tanggal_dibuat (DATETIME)
*   tanggal_diupdate (DATETIME)

### 3. Transaksi (Transactions)
*   **transaction_id** (PRIMARY KEY, UUID)
*   user_id (FOREIGN KEY ke Users.user_id)
*   category_id (FOREIGN KEY ke Categories.category_id)
*   tipe_transaksi (TEXT, ENUM: 'Pemasukan', 'Pengeluaran')
*   jumlah (REAL, > 0)
*   deskripsi (TEXT, NULLABLE)
*   tanggal_transaksi (DATETIME)
*   tanggal_dibuat (DATETIME)
*   tanggal_diupdate (DATETIME)

---

## 🔗 Hubungan Antar Entitas (Relationships)

### 1. Pengguna dan Kategori
*   **Jenis Hubungan:** One-to-Many
*   **Penjelasan:** Setiap `Pengguna` dapat memiliki banyak `Kategori`. Setiap `Kategori` dimiliki oleh satu `Pengguna`.
*   **Representasi:** `Users` 1 -- * N `Categories`
    *   `category_id` di `Categories` memiliki `user_id` sebagai Foreign Key yang merujuk ke `user_id` di `Users`.

### 2. Pengguna dan Transaksi
*   **Jenis Hubungan:** One-to-Many
*   **Penjelasan:** Setiap `Pengguna` dapat memiliki banyak `Transaksi`. Setiap `Transaksi` dicatat oleh satu `Pengguna`.
*   **Representasi:** `Users` 1 -- * N `Transactions`
    *   `transaction_id` di `Transactions` memiliki `user_id` sebagai Foreign Key yang merujuk ke `user_id` di `Users`.

### 3. Kategori dan Transaksi
*   **Jenis Hubungan:** One-to-Many
*   **Penjelasan:** Setiap `Kategori` dapat digunakan untuk banyak `Transaksi`. Setiap `Transaksi` dikaitkan dengan satu `Kategori`.
*   **Representasi:** `Categories` 1 -- * N `Transactions`
    *   `transaction_id` di `Transactions` memiliki `category_id` sebagai Foreign Key yang merujuk ke `category_id` di `Categories`.

---
# ERD (Entity-Relationship Diagram) - Labaku Aplikasi Pencatatan Keuangan UMKM

Berikut adalah representasi Entity-Relationship Diagram (ERD) untuk aplikasi Labaku, yang menggambarkan entitas utama dan hubungan antar entitas menggunakan sintaksis Mermaid.

```mermaid
erDiagram
    Users ||--o{ Categories : "has"
    Users ||--o{ Transactions : "records"
    Categories ||--o{ Transactions : "categorizes"

    Users {
        UUID user_id PK "Primary Key"
        TEXT username UK "Unique Username"
        TEXT password_hash
        TEXT email "Nullable, Unique"
        TEXT nama_lengkap
        DATETIME tanggal_registrasi
        DATETIME tanggal_last_login "Nullable"
    }

    Categories {
        UUID category_id PK "Primary Key"
        UUID user_id FK "Foreign Key to Users"
        TEXT nama_kategori UK "Unique per User"
        TEXT tipe_kategori "ENUM: Pemasukan, Pengeluaran"
        BOOLEAN is_default "Default: FALSE"
        DATETIME tanggal_dibuat
        DATETIME tanggal_diupdate
    }

    Transactions {
        UUID transaction_id PK "Primary Key"
        UUID user_id FK "Foreign Key to Users"
        UUID category_id FK "Foreign Key to Categories"
        TEXT tipe_transaksi "ENUM: Pemasukan, Pengeluaran"
        REAL jumlah "Must be > 0"
        TEXT deskripsi "Nullable"
        DATETIME tanggal_transaksi
        DATETIME tanggal_dibuat
        DATETIME tanggal_diupdate
    }
---
**Penting:**

*   **Mermaid** adalah pilihan yang jauh lebih baik jika platform Anda mendukungnya, karena akan menghasilkan diagram visual yang mudah dibaca. Cukup salin dan tempel kode Mermaid ke file `.md` Anda, dan jika platform mendukungnya, diagram akan otomatis muncul.
*   Saya menggunakan `UUID` untuk semua Primary Key. Ini adalah praktik yang baik untuk aplikasi modern dan distribusikan (terutama jika ada sinkronisasi offline-first), karena mengurangi potensi konflik ID.
*   Saya menambahkan kolom `tanggal_dibuat` dan `tanggal_diupdate` di hampir setiap tabel. Ini adalah praktik yang sangat direkomendasikan untuk melacak perubahan data.
*   Untuk `tipe_kategori` dan `tipe_transaksi`, saya menggunakan `ENUM` untuk menunjukkan bahwa nilainya terbatas pada pilihan tertentu. Dalam implementasi Drift, ini biasanya diwakili oleh tipe `TextColumn` dengan batasan `check()` atau enum Dart.
*   Pertimbangkan apakah Anda memerlukan entitas `Accounts` (misalnya: Kas Tunai, Rekening Bank) jika UMKM ingin melacak uang mereka di berbagai "tempat". Untuk aplikasi awal yang sederhana, `Transactions` mungkin cukup.

Pilih opsi yang paling sesuai dengan kebutuhan dan lingkungan Anda!
