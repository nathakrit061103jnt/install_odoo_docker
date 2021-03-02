# การติดตั้ง Odoo บน Docker โดยใช้ Docker Compose

## การติดตั้ง Docker บน Ubuntu 18

```git
sudo apt-get update
```

```git
  sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

```git
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

```git
sudo apt-key fingerprint 0EBFCD88
```

```git
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

```git
  sudo apt-get update
```

```git
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

```git
apt-cache madison docker-ce
```

```git
sudo apt-get update
```

## ติดตั้ง git บน Ubuntu 18

```git
sudo apt-get install git
```

## ติดตั้ง Docker Compose บน Ubuntu 18

```
sudo apt-get update
```

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.28.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

```
sudo chmod +x /usr/local/bin/docker-compose
```

```
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

- เช็กเวอร์ชันของ Docker Compose

```
docker-compose --version
```

### ติดตั้ง Odoo โดยใช้ Docker Compose File

Services ที่เราจะทำการติดตั้งมา 4 Service คือ

- Odoo
- ฐานข้อมูล Postgres DB เพื่อใช้ในการเก็บข้อมูลของ Odoo Aplication
- Pg Admin ใช้จัดการฐานข้อมูล Postgres DB
- Portainer ใช้ในการจัดการ Docker ผ่าน GUI Web Application เพื่อสะดวกในการใช้งาน Docker

  1.  สร้าง docker-compose.yml

  ```yml
  version: "2"
  services:
    web:
      image: odoo
      depends_on:
        - db
      ports:
        - "8069:8069"
      volumes:
        - odoo-web-data:/var/lib/odoo
        - ./config:/etc/odoo
        - ./addons:/mnt/extra-addons
    db:
      image: postgres
      environment:
        - POSTGRES_DB=postgres
        - POSTGRES_PASSWORD=odoo
        - POSTGRES_USER=odoo
        - PGDATA=/var/lib/postgresql/data/pgdata
      volumes:
        - odoo-db-data:/var/lib/postgresql/data/pgdata

    pgadmin:
      image: dpage/pgadmin4
      depends_on:
        - db
      ports:
        - "53603:53603"
        - "80:80"
      volumes:
        - pgadmin-data:/var/lib/pgadmin
      environment:
      PGADMIN_DEFAULT_EMAIL: admin@example.com
      PGADMIN_DEFAULT_PASSWORD: root

    portainer:
      image: portainer/portainer
      command: -H unix:///var/run/docker.sock
      restart: always
      ports:
        - 9000:9000
      volumes:
        - /var/run/docker.sock:/var/run/docker.sock
        - portainer_data:/data

  volumes:
  odoo-web-data:
  odoo-db-data:
  portainer_data:
  pgadmin-data:
  ```

  2.  ทำการ Buil File docker-compose.yml เพื่อ Run Services ต่าง ๆ

  ```yml
  docker-compose up -d
  ```

### Port ที่ใช้ใน Service ต่าง ๆ

- odoo ใช้ port tcp 9000
- postgresQL ใช้ port tcp 5432
- pgadmin ใช้ port tcp 80,53603
- portainer ใช้ port tcp 9000

### email username password ของเเต่ละ Service

- postgresQL

```
  POSTGRES_DB=postgres
  POSTGRES_PASSWORD=odoo
  POSTGRES_USER=odoo
```

- pgadmin

```
    EMAIL: admin@example.com
    PASSWORD: root
```
