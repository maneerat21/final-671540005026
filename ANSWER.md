# ANSWER — น.ส.มณรัตน์ ศิริกุล 671540005026-3

## Part A — ข้อมูลจาก image kamthornsa/ksu-promo:1.0

| คำถาม | คำตอบ | คำสั่งที่ใช้ |
| --- | --- | --- |
| Port | 3000/tcp (ExposedPorts แสดง `{"3000/tcp":{},"80/tcp":{}}` โดย 80 มาจาก base nginx ส่วน 3000 เจ้าของ image เพิ่มเอง) | `docker inspect --format "{{json .Config.ExposedPorts}}" kamthornsa/ksu-promo:1.0` |
| CMD | `["nginx","-g","daemon off;"]` (มี ENTRYPOINT เป็น `["/docker-entrypoint.sh"]`) | `docker inspect --format "ENTRYPOINT={{json .Config.Entrypoint}} CMD={{json .Config.Cmd}}" kamthornsa/ksu-promo:1.0` |
| Base image | Alpine Linux 3.24.2 พร้อม nginx 1.31.6 (หลักฐาน: `ADD alpine-minirootfs-3.24.2-x86_64.tar.gz /`, `ENV NGINX_VERSION=1.31.6`, os-release) | `docker history --no-trunc kamthornsa/ksu-promo:1.0` และ `docker run --rm --entrypoint cat kamthornsa/ksu-promo:1.0 /etc/os-release` |

## Part B — รหัสลับบนหน้าโปรโมชั่น

รหัสลับ (tag 1.0): `ESPRESSO-47`

## Part C — คำถาม

1. หน้าโปรโมชั่นถูกฝังอยู่ใน image สำเร็จรูป (COPY ไฟล์ตอน build) เราไม่มีซอร์สโค้ดและ Dockerfile ของมัน จึงแก้ข้อความไม่ได้ ส่วนหน้าเมนูเราเขียน index.html และ Dockerfile เอง แก้ไฟล์แล้ว build image ใหม่ได้

2. ทดลอง `docker pull kamthornsa/ksu-promo` (ไม่ใส่ tag) แล้วขึ้น error `kamthornsa/ksu-promo:latest: not found` จึงไม่ได้หน้าเว็บเลย เพราะถ้าไม่ใส่ tag Docker จะใช้ `latest` แต่เจ้าของ image ไม่ได้ push tag นี้ไว้ จึงต้องระบุ tag เช่น 1.0 เสมอ

3. ฝั่งซ้าย (8091) คือพอร์ตของเครื่องโฮสต์ที่เปิดผ่านเบราว์เซอร์ ฝั่งขวา (3000) คือพอร์ตภายใน container ที่ nginx ของ promo ฟังอยู่ ฝั่งขวาไม่ใช่ 80 เพราะ image นี้ตั้งค่า nginx ใหม่ให้ใช้พอร์ต 3000 (เห็นจาก EXPOSE 3000 ใน docker history)

4. หน้าเว็บจะไม่เปลี่ยน ยังเป็นเวอร์ชันเดิม เพราะ index.html ถูก COPY เข้า image ตอน build เท่านั้น และ `up -d` จะใช้ image เดิมที่มีอยู่ ไม่ build ใหม่ คำสั่งที่ถูกต้องคือ `docker compose up -d --build`

5. ลิงก์ใช้ `http://localhost:8091` เพราะลิงก์ถูกเปิดจากเบราว์เซอร์บนเครื่องผู้ใช้ ซึ่งอยู่นอกเครือข่ายของ Docker จึงเข้าผ่านพอร์ตที่ map ไว้บนโฮสต์ ชื่อ `promo:3000` ใช้ได้เฉพาะระหว่าง container ด้วยกันใน network ของ Compose เบราว์เซอร์ไม่รู้จักชื่อ service

## ภาพหน้าจอ

![Menu](01-menu.png)
![Promo](02-promo.png)