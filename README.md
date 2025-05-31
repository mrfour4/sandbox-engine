# Sandbox Engine

This repository provides a secure, self-hosted environment for executing arbitrary code submissions

## ✅ System Requirements

* **OS**: Ubuntu 22.04 (recommended)
* **Required Tools**:

  * Docker
  * Docker Compose

> ⚠️ Judge0 has only been tested on Linux. Windows/Mac are not officially supported.


## 🔧 Pre-deployment: GRUB Configuration (Ubuntu 22.04)

> This step ensures compatibility with cgroup v1, which Judge0 relies on.

1. Open GRUB config file:

   ```bash
   sudo nano /etc/default/grub
   ```

2. Locate the line:

   ```
   GRUB_CMDLINE_LINUX=""
   ```

   Modify it to include:

   ```
   GRUB_CMDLINE_LINUX="systemd.unified_cgroup_hierarchy=0"
   ```

3. Apply the update:

   ```bash
   sudo update-grub
   sudo reboot
   ```

## 🚀 Deployment Steps

### 1. Clone the Repository

```bash
git clone https://github.com/mrfour4/sandbox-engine
cd sandbox-engine
```

### 2. Configure environment variables

1. Generate two secure random passwords from [this site](https://www.random.org/passwords/?num=1&len=32&format=plain&rnd=new).

2. Open `judge0.conf` and set:

```env
REDIS_PASSWORD=your_generated_redis_password
POSTGRES_PASSWORD=your_generated_postgres_password
```

> ⚠️ Do **not** commit this file with secrets in a public repository.

### 3. Start Services

```bash
docker-compose up -d db redis
sleep 10
docker-compose up -d
sleep 5
```


## 🔪 Test Access

Once all services are up, open your browser and visit:

```
http://<YOUR_SERVER_IP>:2358/docs
```

You should see the Swagger UI to test `/submissions`.


## 👎 Stopping the Server

* Gracefully stop all services:

  ```bash
  docker-compose down
  ```

* To pause (without deleting containers):

  ```bash
  docker-compose stop
  ```

## 📬 Example Submission (via `curl`)

```bash
curl -X POST http://<YOUR_SERVER_IP>:2358/submissions \
-H "Content-Type: application/json" \
-d '{
  "source_code": "print(\"Hello, World\")",
  "language_id": 71,
  "stdin": "",
  "expected_output": "Hello, World\n"
}'
```
