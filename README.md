# TKA_A04 MODUL 3

## Praktikan 1 (Dina Rahmadani - 5027241065)
- Membuat `inventory.yml`
- membuat `playbook.yml`
- membuat `roles/docker_install/tasks/main.yml`

#### Setup
1. Membuat Virtual Machine
```sh
# Kalau multipass di windows tambahin 
multipass.exe launch jammy --name vm1 --network "Wi-Fi"
multipass.exe launch jammy --name vm2 --network "Wi-Fi"

# cek ip address
multipass.exe list
```
Note: kalau multipass-nya di wsl pakai `multipass`

2. Memperbaruhi `inventory.yml`, ganti ip vm1 dan vm2

3. Menyiapkan Akses SSH
```sh
# Buat SSH Key (jika belum punya)
ssh-keygen -t rsa -b 4096 -N "" -f ~/.ssh/id_rsa

# Masukkan Key kedalam VM
multipass.exe exec vm1 -- bash -c "echo '$(cat ~/.ssh/id_rsa.pub)' >> ~/.ssh/authorized_keys"
multipass.exe exec vm2 -- bash -c "echo '$(cat ~/.ssh/id_rsa.pub)' >> ~/.ssh/authorized_keys"
```

### Cek

#### Uji Koneksi Node (Ping Test)
> **Note:** File `ansible.cfg` sudah ditambahkan untuk menonaktifkan `host_key_checking`.
```sh
ansible all -m ping
```

#### Eksekusi Playbook (Pemasangan Docker & Firewall)
```sh
ansible-playbook -i inventory.yml playbook.yml
```

#### Pembuktian Langsung (Verifikasi Manual)
- Masuk ke node: `ssh ubuntu@ip_vm1_atau_vm2`
- Lalu Tes Docker di shh: `docker run hello-world`
- Tes Firewall (UFW) di ssh: `sudo ufw status`

## Praktikan 2 (Ica Zika Hamizah - 5027241058)
- Membuat folder `backend_setup`
- Update `playbook.yml`

### Setup
1. Buat File Kredensial Rahasia (Ansible Vault)
```sh
ansible-vault create secrets.yml

# Terminal akan memintamu membuat password baru (ingat baik-baik password ini).
# Setelah editor terbuka, masukkan variabel ini:

db_name: kcks_db
db_username: admin_kcks
backend_port: 3000
db_password: password_super_rahasia
jwt_secret: secret_token_12345
```

2. Eksekusi Playbook
```sh
ansible-playbook -i inventory.yml playbook.yml --ask-vault-pass
```

### Cek

```sh
ssh ubuntu@<IP_VM1_KAMU>

# Cek daftar container yang sedang berjalan:
docker ps

# Indikator Sukses: container menjalankan postgres:15-alpine (di port 5432) 
# dan satu lagi menjalankan aplikasi Node.js kamu (di port 3000)
```

## Praktikan 3 (Ahmad Rafi Fadhillah Dwiputra - 5027241068)
- Membuat folder `frontend_setup` di dalam `roles/`
- Membuat template `docker-compose.yml.j2` dan `config.js.j2`
- Menambahkan konfigurasi port dan url di `inventory.yml`
- Update `playbook.yml` untuk memanggil role `frontend_setup` di `node2`

### Setup & Eksekusi Playbook
1. Jalankan playbook seperti biasa, pastikan password vault sama dengan yang dibuat oleh Praktikan 2:
```sh
ansible-playbook -i inventory.yml playbook.yml --ask-vault-pass
```

### Cek & Testing Frontend
Gunakan SSH Tunneling di terminal WSL untuk menembus isu *Double Layer Virtualization*:
1. **Tunnel Frontend:** `ssh -L 8080:localhost:8080 ubuntu@<IP_VM2>`
2. **Tunnel Backend:** `ssh -L 3000:localhost:3000 ubuntu@<IP_VM1>`

**Skenario Test (Browser: `localhost:8080`):**
- Register user baru (Berhasil)
- Register user sama (Gagal)
- Login salah password (Gagal)
- Login berhasil (Berhasil + Token JWT)