# ANSWER.md

รหัสนักศึกษา: 671540005026-3

## Part A — สำรวจ image kamthornsa/ksu-promo:1.0

| คำถาม | คำตอบ | คำสั่งที่ใช้ |
|---|---|---|
| image เปิด port อะไร | <ผลจาก ExposedPorts เช่น 80/tcp> | `docker inspect --format "{{json .Config.ExposedPorts}}" kamthornsa/ksu-promo:1.0` |
| คำสั่งที่รันตอน container เริ่มทำงาน | <ENTRYPOINT + CMD ที่ได้> | `docker inspect --format "ENTRYPOINT={{json .Config.Entrypoint}} CMD={{json .Config.Cmd}}" kamthornsa/ksu-promo:1.0` |
| base image คืออะไร | <ตามหลักฐานจาก history / os-release> | `docker history --no-trunc kamthornsa/ksu-promo:1.0` และ `docker run --rm --entrypoint cat kamthornsa/ksu-promo:1.0 /etc/os-release` |

### ผลลัพธ์ที่ได้จริง

```
<วางผลลัพธ์ของคำสั่ง (ก) ตรงนี้> 
```
{"3000/tcp":{},"80/tcp":{}}
```
<วางผลลัพธ์ของคำสั่ง (ข) ตรงนี้>
```
ENTRYPOINT=["/docker-entrypoint.sh"] CMD=["nginx","-g","daemon off;"]
```
<วางผลลัพธ์ของ docker history / os-release ตรงนี้>
```
จาก `docker history`: เลเยอร์ล่างสุดคือ
`ADD alpine-minirootfs-3.24.2-x86_64.tar.gz /` ตามด้วย `ENV NGINX_VERSION=1.31.6`
และ `EXPOSE map[80/tcp:{}]` (จาก base) ส่วนเลเยอร์บนสุดของเจ้าของ image คือ
`COPY default.conf /etc/nginx/conf.d/default.conf`, `COPY index.html /usr/share/nginx/html/` และ `EXPOSE [3000/tcp]`

จาก `docker inspect ... .Config.Env`:
```
["PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin","NGINX_VERSION=1.31.6","PKG_RELEASE=1","DYNPKG_RELEASE=1","NJS_VERSION=1.0.1","NJS_RELEASE=1","ACME_VERSION=0.4.1"]
```

จาก `/etc/os-release`:
```
NAME="Alpine Linux"
ID=alpine
VERSION_ID=3.24.2
PRETTY_NAME="Alpine Linux v3.24"
```

### หมายเหตุ

- พอร์ต 8080 บนเครื่องถูกใช้งานอยู่ จึงใช้พอร์ตอื่นแทนตอนรัน