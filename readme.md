# TUGAS EVALUASI MANDIRI Komunikasi Inter-service dengan gRPC (Polyglot)

## Tujuan Pratikum
1.	Mahasiswa memahami arsitektur Schema First Development menggunakan Protocol Buffers.
2.	Mahasiswa mampu menginstalasi komponen compiler gRPC (protoc).
3.	Mahasiswa berhasil membuat gRPC Server menggunakan Go.
4.	Mahasiswa berhasil membuat gRPC Client menggunakan Python dan Node.js untuk berkomunikasi dengan server Go yang sama.

<br><br><br>

## Langkah - Langkah
### 1. Modifikasi Kontrak Data

Pada tugas evaluasi mandiri ini dilakukan modifikasi terhadap file `user.proto` dengan menambahkan satu field baru bernama `phone` pada message `UserResponse`.

Perubahan kode:

```proto
message UserResponse {
  string name = 1;
  string email = 2;
  string phone = 3;
}
```

Penambahan field `phone` bertujuan agar server Go dapat mengirimkan informasi nomor telepon pengguna kepada client Python dan Node.js melalui komunikasi gRPC.

### 2. Generate Ulang Kode

Setelah file `user.proto` diperbarui, dilakukan proses generate ulang kode sesuai bahasa pemrograman yang digunakan.

**Go**

Perintah generate:

```powershell
protoc --go_out=go-server/pb --go_opt=paths=source_relative --go-grpc_out=go-server/pb --go-grpc_opt=paths=source_relative user.proto
```

Hasil generate berupa:
- `user_pb.go`
- `user_grpc.pb.go`

**Python**

Perintah generate:

```powershell
python -m grpc_tools.protoc -I. --python_out=python-client --grpc_python_out=python-client user.proto
```

Hasil generate berupa:
- `user_pb2.py`
- `user_pb2_grpc.py`

**Node.js**

Node.js menggunakan pendekatan dynamic runtime generation melalui library `@grpc/proto-loader`. Oleh karena itu, tidak diperlukan proses generate kode secara terpisah. Client membaca file `user.proto` secara langsung ketika aplikasi dijalankan.

### 3. Modifikasi Go Server

Pada Go Server dilakukan penambahan nilai nomor telepon pada data respons pengguna.

Contoh implementasi:

```go
return &pb.UserResponse{
    Name:  "Budi Santoso",
    Email: "budi@universitas.ac.id",
    Phone: "081234567890",
}, nil
```

Dengan perubahan tersebut, server mengirimkan tiga informasi, yaitu nama, email, dan nomor telepon.

### 4. Modifikasi Client

**Python Client**

Pada Python Client ditambahkan perintah untuk menampilkan field `phone` dari respons server.

```python
print(f"Respon dari Server -> Nama: {response.name}, Email: {response.email}, Phone: {response.phone}")
```

**Node.js Client**

Pada Node.js Client ditambahkan properti `response.phone` agar nomor telepon dapat ditampilkan.

```javascript
console.log(
    `Respon dari Server -> Nama: ${response.name}, Email: ${response.email}, Phone: ${response.phone}`
);
```

### 5. Pengujian Program

Pengujian dilakukan dengan menjalankan Go Server, Python Client, dan Node.js Client menggunakan terminal yang berbeda.

**Menjalankan Go Server:**

```powershell
cd go-server
go run main.go
```

**Menjalankan Python Client:**

```powershell
cd python-client
python client.py
```

**Menjalankan Node.js Client:**

```powershell
cd node-client
node client.js
```

Hasil yang diharapkan dari kedua client:

```text
Mengirim request ke Go Server...
Respon dari Server -> Nama: Budi Santoso, Email: budi@universitas.ac.id, Phone: 081234567890
```

Pengujian dinyatakan berhasil apabila Python Client dan Node.js Client dapat menerima serta menampilkan nama, email, dan nomor telepon yang dikirimkan oleh Go Server.

### 6. Analisis Pendekatan Kompilasi

Dalam implementasi gRPC menggunakan beberapa bahasa pemrograman, terdapat perbedaan pendekatan antara static code generation dan dynamic runtime generation.

Pada bahasa Go dan Python digunakan pendekatan static code generation. Kode program dihasilkan terlebih dahulu berdasarkan file `.proto` sebelum aplikasi dijalankan. Apabila terdapat perubahan pada kontrak data, seperti penambahan field `phone`, proses generate ulang harus dilakukan agar kode yang digunakan dapat mengenali perubahan tersebut.

Sementara itu, Node.js menggunakan pendekatan dynamic runtime generation melalui library `@grpc/proto-loader`. File `.proto` dibaca secara langsung ketika aplikasi berjalan sehingga tidak membutuhkan proses generate kode secara terpisah. Dengan pendekatan ini, perubahan pada kontrak data dapat digunakan setelah file `.proto` diperbarui dan aplikasi dijalankan kembali.

### 7. Kesimpulan

Berdasarkan tugas yang telah dikerjakan, dapat disimpulkan bahwa:

1. File `user.proto` berfungsi sebagai kontrak komunikasi antara server dan client.
2. Penambahan field `phone` memungkinkan server mengirimkan data nomor telepon kepada client.
3. Go dan Python memerlukan proses generate ulang ketika terdapat perubahan pada file `.proto`.
4. Node.js dapat membaca file `.proto` secara langsung menggunakan `@grpc/proto-loader`.
5. Komunikasi gRPC memungkinkan aplikasi yang menggunakan bahasa pemrograman berbeda untuk saling bertukar data.

### 8. Pengumpulan

Setelah seluruh perubahan selesai, simpan perubahan menggunakan Git.

```bash
git add .
git commit -m "update-grpc"
git push
```