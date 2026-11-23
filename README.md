# SIMULASI PEMINJAMAN DAN PENGEMBALIAN BUKU

# Data buku: [ID, Judul, Kategori, Status]
inventaris = [
    ["B1", "Kalkulus", "Matematika", "Tersedia"],
    ["B2", "Fisika Dasar", "Fisika", "Tersedia"],
    ["B3", "Kimia Dasar", "Kimia", "Tersedia"],
    ["B4", "Algoritma", "Informatika", "Tersedia"],
    ["B5", "Statistika", "Matematika", "Tersedia"]
]

# Data peminjaman: [Nama, ID Buku, Hari Pinjam]
peminjam_aktif = []

BATAS_PINJAM = 7
DENDA_PER_HARI = 2000


# FUNGSI INPUT HARI
def input_hari(pesan):
    while True:
        try:
            hari = int(input(pesan))

            if hari >= 1:
                return hari
            else:
                print("Hari harus dimulai dari angka 1.")

        except ValueError:
            print("Input harus berupa angka.")


# 1. FUNGSI PENCATATAN PEMINJAMAN
def catat_peminjaman():
    nama = input("Nama peminjam: ")
    id_buku = input("ID buku yang ingin dipinjam: ")
    hari_pinjam = input_hari("Hari ke berapa buku dipinjam: ")

    ditemukan = False

    for buku in inventaris:
        if buku[0] == id_buku:
            ditemukan = True

            if buku[3] == "Tersedia":
                buku[3] = "Dipinjam"
                peminjam_aktif.append([nama, id_buku, hari_pinjam])

                print("Peminjaman berhasil!")
                print("Batas peminjaman:", hari_pinjam + BATAS_PINJAM)
            else:
                print("Buku sedang dipinjam.")

            break

    if ditemukan == False:
        print("ID buku tidak ditemukan.")


#  FUNGSI PENGECEKAN DURASI DAN STATUS
def cek_status_pinjaman():
    if len(peminjam_aktif) == 0:
        print("Tidak ada peminjaman aktif.")
        return

    hari_ini = input_hari("Hari simulasi saat ini: ")

    # Memastikan hari pengecekan tidak lebih awal
    # dari hari peminjaman yang paling baru
    hari_terakhir_pinjam = max(pinjam[2] for pinjam in peminjam_aktif)

    if hari_ini < hari_terakhir_pinjam:
        print("Hari pengecekan tidak boleh lebih awal")
        print("dari hari peminjaman.")
        return

    for pinjam in peminjam_aktif:
        durasi = hari_ini - pinjam[2]
        sisa = BATAS_PINJAM - durasi

        print("Nama:", pinjam[0])
        print("ID Buku:", pinjam[1])
        print("Durasi pinjam:", durasi, "hari")

        if durasi > BATAS_PINJAM:
            print("Status: TERLAMBAT")
            print("Terlambat:", durasi - BATAS_PINJAM, "hari")
        elif sisa == 0:
            print("Status: Batas pengembalian hari ini")
        else:
            print("Status: Belum terlambat")
            print("Sisa waktu:", sisa, "hari")


# 3. FUNGSI PERHITUNGAN DENDA
def hitung_denda(hari_terlambat):
    if hari_terlambat > 0:
        total_denda = hari_terlambat * DENDA_PER_HARI
    else:
        total_denda = 0

    return total_denda


# 4. FUNGSI PENGEMBALIAN BUKU
def proses_pengembalian():
    id_buku = input("ID buku yang dikembalikan: ")
    hari_kembali = input_hari("Hari ke berapa buku dikembalikan: ")

    ditemukan = False

    for pinjam in peminjam_aktif:
        if pinjam[1] == id_buku:
            ditemukan = True

            # Validasi hari pengembalian
            if hari_kembali < pinjam[2]:
                print("Hari pengembalian tidak boleh")
                print("lebih awal dari hari peminjaman.")
                return

            durasi = hari_kembali - pinjam[2]
            hari_terlambat = durasi - BATAS_PINJAM

            if hari_terlambat < 0:
                hari_terlambat = 0

            denda = hitung_denda(hari_terlambat)

            print("--- STRUK PENGEMBALIAN ---")
            print("Nama peminjam:", pinjam[0])
            print("ID Buku:", pinjam[1])
            print("Durasi pinjam:", durasi, "hari")
            print("Hari terlambat:", hari_terlambat)
            print("Total denda: Rp", denda)

            if denda == 0:
                print("Status: Tidak ada denda")
            else:
                print("Status: Denda harus dibayar")

            # Mengubah status buku menjadi tersedia
            for buku in inventaris:
                if buku[0] == id_buku:
                    buku[3] = "Tersedia"

            # Menghapus transaksi peminjaman aktif
            peminjam_aktif.remove(pinjam)

            print("Buku berhasil dikembalikan.")
            print("Status buku: Tersedia")

            break

    if ditemukan == False:
        print("Buku tidak sedang dipinjam.")


# 5. MENAMPILKAN DAFTAR BUKU
def tampilkan_buku():
    print("--- DAFTAR INVENTARIS BUKU ---")

    for buku in inventaris:
        print("ID:", buku[0],
              "Judul:", buku[1],
              "Kategori:", buku[2],
              "Status:", buku[3])


# 6. MENAMPILKAN PEMINJAMAN AKTIF
def tampilkan_peminjaman():
    print("--- DAFTAR PEMINJAMAN AKTIF ---")

    if len(peminjam_aktif) == 0:
        print("Tidak ada buku yang sedang dipinjam.")
    else:
        for pinjam in peminjam_aktif:
            print("Nama:", pinjam[0],
                  "ID Buku:", pinjam[1],
                  "Hari Pinjam:", pinjam[2])


# 7. FUNGSI MENAMBAHKAN BUKU
def tambah_buku():
    judul = input("Nama buku: ")
    kategori = input("Kategori buku: ")

    id_buku = "B" + str(len(inventaris) + 1)

    inventaris.append([id_buku, judul, kategori, "Tersedia"])

    print("Buku berhasil ditambahkan!")
    print("ID Buku:", id_buku)
    print("Judul:", judul)
    print("Kategori:", kategori)
    print("Status: Tersedia")


# MENU UTAMA
while True:
    print("===== SISTEM PEMINJAMAN BUKU =====")
    print("1. Tampilkan daftar buku")
    print("2. Pinjam buku")
    print("3. Cek status peminjaman")
    print("4. Tampilkan peminjaman aktif")
    print("5. Kembalikan buku")
    print("6. Tambah buku")
    print("7. Keluar")

    pilihan = input("Pilih menu: ")

    if pilihan == "1":
        tampilkan_buku()
    elif pilihan == "2":
        catat_peminjaman()
    elif pilihan == "3":
        cek_status_pinjaman()
    elif pilihan == "4":
        tampilkan_peminjaman()
    elif pilihan == "5":
        proses_pengembalian()
    elif pilihan == "6":
        tambah_buku()
    elif pilihan == "7":
        print("Program selesai.")
        break
    else:
        print("Pilihan tidak valid.")
