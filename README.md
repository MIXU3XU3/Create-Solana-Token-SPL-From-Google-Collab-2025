# Create-Solana-Token-SPL-From-Google-Collab-2025
Don't Try This if You not wanna rich on 2025 





Panduan Lengkap: Membuat Token Solana (Token-2022) di Google Colab
Buka notebook Google Colab baru dan ikuti langkah-langkah di bawah ini. Setiap blok kode ditujukan untuk dijalankan di sel terpisah.

Pra-Langkah: Lewati Setup WSL
Karena Google Colab sudah berjalan di lingkungan Linux (Ubuntu), kita bisa melewatkan seluruh Pre-Step untuk Windows. Kita langsung mulai ke instalasi.

Langkah 1: Instalasi Solana CLI & Alat Pendukung
Installer resmi ini akan menyiapkan semua yang kita butuhkan: Solana CLI, Rust, Anchor, dll.

Python

# Jalankan sel ini untuk menginstal semua alat yang dibutuhkan
!curl --proto '=https' --tlsv1.2 -sSfL https://solana-install.solana.workers.dev | bash
Setelah instalasi selesai, kita perlu menambahkan path instalasi agar terminal Colab bisa menemukan perintah solana.

Python

# Tambahkan path Solana ke environment session ini
import os
os.environ['PATH'] = f"/root/.local/share/solana/install/active_release/bin:{os.environ['PATH']}"

# Verifikasi semua instalasi
!solana --version
!spl-token --version
Anda akan melihat output versi dari solana-cli dan spl-token.

Langkah 2: Membuat dan Mengatur Wallet
Kita akan membuat wallet baru khusus untuk sesi Colab ini dan mengisinya dengan SOL dari Devnet.

Python

# 1. Atur jaringan ke Devnet
!solana config set --url devnet

# 2. Buat file wallet baru di dalam environment Colab
!solana-keygen new --outfile /content/devnet.json --no-bip39-passphrase

# 3. Atur wallet yang baru dibuat sebagai default
!solana config set --keypair /content/devnet.json

# 4. Tampilkan konfigurasi dan alamat wallet Anda
print("--- Konfigurasi Saat Ini ---")
!solana config get
print("\n--- Alamat Wallet Anda ---")
!solana address

# 5. Minta 2 SOL gratis untuk biaya transaksi di Devnet
print("\n--- Meminta Airdrop... ---")
!solana airdrop 2

# 6. Cek saldo akhir
print("\n--- Saldo Wallet ---")
!solana balance
PENTING: Simpan seed phrase (frasa pemulihan) yang muncul saat membuat wallet. Jika sesi Colab Anda terputus, Anda bisa memulihkannya.

Langkah 3: Membuat Token Mint (Menggunakan Token-2022)
Ini adalah langkah kunci. Kita membuat "cetakan" token dengan program Token-2022 dan langsung mengaktifkan ekstensi metadata.

Python

# Perintah untuk membuat token baru dengan program Token-2022 dan ekstensi metadata
!spl-token create-token --program-id TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb --enable-metadata --decimals 9
SANGAT PENTING: Salin dan simpan Token Mint Address yang muncul di output. Kita akan menyebutnya <MINT_ADDRESS>.

Langkah 4: Membuat Akun Token di Wallet Anda
Setiap wallet perlu "kantong" khusus untuk menyimpan token jenis baru.

Python

# Ganti <MINT_ADDRESS> dengan alamat yang Anda dapatkan dari Langkah 3
!spl-token create-account <MINT_ADDRESS>
Langkah 5: Mencetak (Minting) Token
Sekarang kita cetak token dari "cetakan" ke "kantong" yang baru kita buat.

Python

# Ganti <MINT_ADDRESS> dengan alamat token Anda
# Angka 1000000 adalah jumlah token yang dicetak (sesuaikan jika perlu)
!spl-token mint <MINT_ADDRESS> 1000000

# Cek saldo token Anda
!spl-token balance <MINT_ADDRESS>
Langkah 6 & 7: Menyiapkan File Metadata di Colab
Ini adalah bagian di mana Colab sedikit berbeda. Kita akan membuat file metadata.json dan mengupload logo langsung di Colab sebelum mengunggahnya ke IPFS.

Upload Logo Anda:

Di panel kiri Colab, klik ikon Folder.

Klik ikon "Upload to session storage".

Pilih file logo Anda (misalnya mytoken-logo.png).

Buat metadata.json:

Jalankan sel kode di bawah ini. Ini akan membuat file metadata.json di Colab.

Ganti <YOUR_WALLET_ADDRESS> dengan alamat wallet Anda dari Langkah 2.

Python

%%writefile metadata.json
{
  "name": "MyToken Colab",
  "symbol": "MTC",
  "description": "Token yang dibuat di Google Colab menggunakan panduan Token-2022 edisi 2025.",
  "image": "mytoken-logo.png",
  "external_url": "https://solana.com",
  "attributes": [
    { "trait_type": "Platform", "value": "Google Colab" },
    { "trait_type": "Network", "value": "Solana Devnet" }
  ],
  "properties": {
    "files": [{ "uri": "mytoken-logo.png", "type": "image/png" }],
    "category": "image",
    "creators": [{ "address": "<YOUR_WALLET_ADDRESS>", "share": 100 }]
  }
}
Langkah 8: Upload ke IPFS (Pinata/Storacha)
Sekarang file metadata.json dan mytoken-logo.png sudah ada di Colab. Kita perlu mengunggahnya ke layanan IPFS agar mendapatkan URL publik.

Di panel kiri Colab (ikon Folder), Anda akan melihat kedua file tersebut.

Download kedua file itu ke komputer Anda.

Buka situs web seperti Pinata atau Storacha.

Gunakan fitur "Upload Folder" dan pilih folder di komputer Anda yang berisi kedua file tersebut.

Setelah selesai, layanan akan memberi Anda sebuah CID (Content Identifier). Contoh: bafybeihabc123...

Simpan CID ini!

Langkah 9: Menghubungkan Metadata ke Token
Ini adalah langkah final. Kita akan memberi tahu blockchain Solana di mana menemukan metadata untuk token kita menggunakan URL IPFS.

Python

# Ganti <MINT_ADDRESS> dengan alamat token Anda.
# Ganti <FOLDER_CID> dengan CID yang Anda dapatkan dari Pinata/Storacha.

!spl-token initialize-metadata <MINT_ADDRESS> "MyToken Colab" "MTC" "https://gateway.pinata.cloud/ipfs/<FOLDER_CID>/metadata.json"
Catatan: Anda bisa menggunakan gateway lain seperti https://storacha.network/ipfs/... jika mau.

Selesai! Token Anda sekarang memiliki metadata on-chain. Buka Solana Explorer (https://explorer.solana.com/address/<MINT_ADDRESS>?cluster=devnet) dan setelah beberapa saat, Anda akan melihat nama, simbol, dan gambar token Anda muncul! Dompet seperti Solflare atau Phantom juga akan menampilkannya dengan benar.

Langkah Tambahan (Opsional di Colab)
Langkah 10: Transfer Token
Python

# Ganti <MINT_ADDRESS> dan <RECIPIENT_ADDRESS>
!spl-token transfer <MINT_ADDRESS> 100 <RECIPIENT_ADDRESS>
Langkah 11: Update Metadata
Jika Anda perlu mengubah sesuatu, perintahnya sangat mudah.

Python

# Ganti <MINT_ADDRESS> dan <NEW_FOLDER_CID> jika Anda mengupload metadata baru
# Contoh mengubah nama saja:
!spl-token update-metadata <MINT_ADDRESS> --name "MyToken Colab Pro"

# Contoh mengubah URI:
!spl-token update-metadata <MINT_ADDRESS> --uri "https://gateway.pinata.cloud/ipfs/<NEW_FOLDER_CID>/metadata.json"

