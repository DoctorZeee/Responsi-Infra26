# Analisis Perbaikan

## Identitas Praktikan

- **Nama Lengkap:** Dimas Rafif Zaidan
- **NIM:** H1H024043

---

## Permasalahan 1

### Gejala
Perintah `docker compose up -d` gagal dijalankan dengan error syntax pada file `docker-compose.yml`.

### Penyebab
Pada baris pertama definisi services, tanda titik dua (`:`) tidak disertakan sehingga menjadi `services` alih-alih `services:`. Ini menyebabkan YAML parser gagal membaca struktur file.

### Solusi
Mengubah baris `services` menjadi `services:` pada `docker-compose.yml`.

---

## Permasalahan 2

### Gejala
Container `web1` berhasil berjalan tetapi tidak dapat terhubung ke database. Log menampilkan error koneksi ke host `mysql`.

### Penyebab
Nilai `DB_HOST` pada service `web1` diset ke `mysql`, padahal nama service database dalam `docker-compose.yml` adalah `db`. Docker Compose menggunakan nama service sebagai hostname di dalam network internal.

### Solusi
Mengubah `DB_HOST: mysql` menjadi `DB_HOST: db` pada konfigurasi environment service `web1`.

---

## Permasalahan 3

### Gejala
Container `web2` berhasil berjalan namun selalu gagal terkoneksi ke database dengan error autentikasi.

### Penyebab
Nilai `DB_PASS` pada service `web2` diset ke `wrongpassword`, sedangkan password yang didefinisikan pada service `db` (`MYSQL_PASSWORD`) adalah `student123`.

### Solusi
Mengubah `DB_PASS: wrongpassword` menjadi `DB_PASS: student123` pada konfigurasi environment service `web2`.

---

## Permasalahan 4

### Gejala
Docker gagal melakukan build untuk service `web3` dengan pesan error: `unable to prepare context: path "web33" not found`.

### Penyebab
Nilai `context` pada service `web3` diset ke `./web33` (dengan dua huruf `3`), padahal folder yang tersedia adalah `./web3`. Ini menyebabkan Docker tidak menemukan Dockerfile untuk membangun image web3.

### Solusi
Mengubah `context: ./web33` menjadi `context: ./web3` pada konfigurasi build service `web3`.

---

## Permasalahan 5

### Gejala
Setelah semua container berhasil berjalan, load balancing tidak mendistribusikan request ke `web3`. Hanya `web1` dan `web2` yang menerima traffic dari Nginx.

### Penyebab
Service `web3` hanya terhubung ke network `backend`, tidak ke network `frontend`. Karena Nginx hanya berada di network `frontend`, Nginx tidak dapat menjangkau `web3` sehingga upstream ke web3 selalu gagal.

### Solusi
Menambahkan network `frontend` pada konfigurasi networks service `web3`:
```yaml
web3:
  networks:
    - frontend
    - backend
```

---

## Permasalahan 6

### Gejala
Docker memberikan warning bahwa volume yang digunakan oleh service `db` tidak konsisten dengan definisi volume di bagian `volumes`.

### Penyebab
Service `db` mendefinisikan mount volume dengan nama `db-data`, namun pada bagian `volumes` di bawah file `docker-compose.yml` nama yang terdefinisi adalah `database-data`. Ini menyebabkan inkonsistensi nama volume.

### Solusi
Menyamakan nama volume menjadi `db-data` pada bagian `volumes`:
```yaml
volumes:
  db-data:
```
Mengubah bagian sql dan config nginx karena ada tanda "```" kemudian mengubah posrt expose dari 8080 jadi 80 
---
