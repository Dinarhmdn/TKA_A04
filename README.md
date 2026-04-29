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
```sh
ansible all -i inventory.yml -m ping
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