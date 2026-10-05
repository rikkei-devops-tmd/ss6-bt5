BAI 5: THIET KE VA CAU HINH DICH VU SYSTEMD SERVICE CHO UNG DUNG JAVA

MUC TIEU
- Thiet ke cau hinh dich vu Systemd (Service file) de he dieu hanh quan ly vong doi ung dung tu dong.
- Chay ung dung duoi quyen user gioi han (non-root) de nang cao bao mat.
- Cai dat chinh sach tu dong khoi dong lai dich vu khi co su co.

BOI CANH
Dong goi ung dung Spring Boot thanh dich vu nen Systemd:
- He dieu hanh tu khoi tao khi boot may.
- Tu khoi dong lai khi gap loi sap dot ngot.


1. TAO TAI KHOAN HE THONG JAVA-RUNNER

Tao user he thong khong co quyen dang nhap shell:

```bash
sudo useradd -r -s /usr/sbin/nologin java-runner
```

Kiem tra user da tao:

```bash
id java-runner
```

Ket qua mau:
uid=999(java-runner) gid=999(java-runner) groups=999(java-runner)


2. NOI DUNG FILE CAU HINH DICH VU

Duong dan: /etc/systemd/system/java-app.service

```ini
[Unit]
Description=Java Application Service
After=network.target

[Service]
User=java-runner
ExecStart=/usr/bin/sleep 1000
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Giai thich tung phan:
- [Unit]
  - Description   : mo ta dich vu.
  - After         : dam bao dich vu khoi chay sau khi mang san sang.
- [Service]
  - User          : chay duoi danh nghia user java-runner (non-root, bao mat).
  - ExecStart     : lenh khoi chay (su dung sleep 1000 de mo phong ung dung dang chay).
  - Restart       : tu dong khoi dong lai khi tien trinh bi sap bat thuong.
  - RestartSec    : cho 10 giay truoc khi khoi dong lai.
- [Install]
  - WantedBy      : kích hoat dich vu trong che do da nguoi dung (multi-user.target).


3. TRIEN KHAI DICH VU

Sao chep file service vao thu muc he thong:

```bash
sudo cp java-app.service /etc/systemd/system/java-app.service
```

Nap lai cau hinh systemd:

```bash
sudo systemctl daemon-reload
```

Khoi chay dich vu:

```bash
sudo systemctl start java-app.service
```

(Tuy chon) Bat dich vu tu dong khoi chay khi boot:

```bash
sudo systemctl enable java-app.service
```


4. KIEM TRA TRANG THAI DICH VU

```bash
sudo systemctl status java-app.service
```

Ket qua dau ra mau:

```
java-app.service - Java Application Service
     Loaded: loaded (/etc/systemd/system/java-app.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2026-10-05 08:40:00 UTC; 5s ago
   Main PID: 15234 (sleep)
      Tasks: 1 (limit: 2339)
     Memory: 264.0K
        CPU: 2ms
     CGroup: /system.slice/java-app.service
             └─15234 /usr/bin/sleep 1000

Oct 05 08:40:00 ubuntu-server systemd[1]: Started Java Application Service.
```

Xac nhan:
- Trang thai: "Active: active (running)".
- Chay duoi user: java-runner.
- PID tien trinh: 15234.

Xem log nhat ky bang journalctl:

```bash
journalctl -u java-app.service
```

Ket qua mau:

```
Oct 05 08:40:00 ubuntu-server systemd[1]: Started Java Application Service.
```


5. TONG KET CAC LENH DA THUC HIEN

```bash
sudo useradd -r -s /usr/sbin/nologin java-runner
id java-runner
sudo cp java-app.service /etc/systemd/system/java-app.service
sudo systemctl daemon-reload
sudo systemctl start java-app.service
sudo systemctl enable java-app.service
sudo systemctl status java-app.service
journalctl -u java-app.service
```
