# Analisis Perbaikan

## Identitas Praktikan

* **Nama Lengkap:** Dimas Rafif Zaidan
* **NIM:** H1H024043

---

## Permasalahan 1

### Gejala

Perintah `docker compose up -d` gagal dijalankan dengan error syntax pada file `docker-compose.yml`.

### Penyebab

Pada baris pertama definisi services, tanda titik dua (`:`) tidak disertakan sehingga menjadi `services` alih-alih `services:`. Hal ini menyebabkan YAML parser gagal membaca struktur file.

### Solusi

Mengubah baris `services` menjadi:

```yaml
services:
```

---

## Permasalahan 2

### Gejala

Container `web1` berhasil berjalan tetapi tidak dapat terhubung ke database. Log menampilkan error koneksi ke host `mysql`.

### Penyebab

Nilai `DB_HOST` pada service `web1` diset ke `mysql`, padahal nama service database dalam `docker-compose.yml` adalah `db`. Docker Compose menggunakan nama service sebagai hostname di dalam network internal.

### Solusi

Mengubah konfigurasi:

```yaml
DB_HOST: mysql
```

menjadi:

```yaml
DB_HOST: db
```

---

## Permasalahan 3

### Gejala

Container `web2` berhasil berjalan namun selalu gagal terkoneksi ke database dengan error autentikasi.

### Penyebab

Nilai `DB_PASS` pada service `web2` diset ke `wrongpassword`, sedangkan password yang didefinisikan pada service `db` (`MYSQL_PASSWORD`) adalah `student123`.

### Solusi

Mengubah konfigurasi:

```yaml
DB_PASS: wrongpassword
```

menjadi:

```yaml
DB_PASS: student123
```

---

## Permasalahan 4

### Gejala

Docker gagal melakukan build untuk service `web3` dengan pesan error:

```
unable to prepare context: path "web33" not found
```

### Penyebab

Nilai `context` pada service `web3` diset ke `./web33`, padahal folder yang tersedia adalah `./web3`. Hal ini menyebabkan Docker tidak menemukan Dockerfile untuk membangun image `web3`.

### Solusi

Mengubah konfigurasi:

```yaml
context: ./web33
```

menjadi:

```yaml
context: ./web3
```

---

## Permasalahan 5

### Gejala

Setelah semua container berhasil berjalan, load balancing tidak mendistribusikan request ke `web3`. Hanya `web1` dan `web2` yang menerima traffic dari Nginx.

### Penyebab

Service `web3` hanya terhubung ke network `backend` dan tidak terhubung ke network `frontend`. Karena Nginx berada pada network `frontend`, maka `web3` tidak dapat dijangkau.

### Solusi

Menambahkan network `frontend` pada konfigurasi service `web3`:

```yaml
web3:
  networks:
    - frontend
    - backend
```

---

## Permasalahan 6

### Gejala

Docker memberikan warning bahwa volume yang digunakan oleh service `db` tidak konsisten dengan definisi volume pada bagian `volumes`.

### Penyebab

Service `db` menggunakan volume `db-data`, sedangkan pada bagian `volumes` nama yang didefinisikan adalah `database-data`.

### Solusi

Menyamakan nama volume menjadi:

```yaml
volumes:
  db-data:
```

---

## Permasalahan 7

### Gejala

Container Nginx gagal dijalankan dan konfigurasi tidak dapat diparsing dengan benar.

### Penyebab

File konfigurasi `nginx.conf` dan file SQL masih mengandung karakter markdown berupa tanda ``` yang seharusnya tidak menjadi bagian dari isi file. Selain itu, konfigurasi port expose masih menggunakan port `8080`, sedangkan kebutuhan sistem menggunakan port `80`.

### Solusi

* Menghapus seluruh karakter markdown berupa tanda ``` pada file konfigurasi Nginx dan file SQL.
* Menyesuaikan konfigurasi port expose dari `8080` menjadi `80` agar sesuai dengan kebutuhan sistem.
