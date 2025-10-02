# DevOps Intern Final Assessment

**Name:** Ankita tapase
**Date:** 2025-10-02

This repository demonstrates a small, realistic DevOps pipeline using Linux scripting, Docker, GitHub Actions CI, Nomad deployment, and basic monitoring with Grafana Loki (+ Promtail).

---

## Repo Map

```
.
├── README.md
├── hello.py
├── Dockerfile
├── scripts/
│   └── sysinfo.sh
├── .github/
│   └── workflows/
│       └── ci.yml
├── nomad/
│   └── hello.nomad
├── monitoring/
│   ├── loki_setup.txt
│   └── promtail-config.yml
└── mlflow/ (optional extra)
    └── log_dummy.py
```

---

## 1) Git & GitHub Setup

- Create a new public repo (e.g., `devops-intern-final`) and push these files.
- Add this README, code, configs, and screenshots as required.

✅ **Output:** GitHub repo initialized with README and sample script.

---

## 2) Linux & Scripting Basics

**Script:** `scripts/sysinfo.sh`

Runs a quick system info printout.

```bash
chmod +x scripts/sysinfo.sh
./scripts/sysinfo.sh
```

✅ **Output:** `scripts/sysinfo.sh` is executable and working.

---

## 3) Docker Basics

Containerize the `hello.py` app.

**Build the image:**

```bash
docker build -t hello-devops:latest .
```

**Run the container:**

```bash
docker run --rm hello-devops:latest
```

You should see:

```
Hello, DevOps!
```

✅ **Output:** `Dockerfile` + working container run.

---

## 4) CI/CD with GitHub Actions

A simple CI runs `python hello.py` on every push.

**Badge (update USER/REPO):**  
![CI](https://github.com/USER/REPO/actions/workflows/ci.yml/badge.svg)

> Replace `USER/REPO` with your actual GitHub path once pushed.

✅ **Output:** Working CI visible in your repo's **Actions** tab.

---

## 5) Job Deployment with Nomad

The Nomad job runs the Docker image as a tiny service. Ensure your Nomad client can pull/run the `hello-devops:latest` image (either build on the node or push to a registry and update the image reference).

**Run:**

```bash
nomad job run nomad/hello.nomad
nomad status hello-devops
nomad allocations -job hello-devops
```

**Logs:**

```bash
alloc_id=$(nomad status -short hello-devops | awk 'NR==1{print $1}')
nomad alloc logs -stdout "$alloc_id"
```

✅ **Output:** `nomad/hello.nomad` committed and runnable.

---

## 6) Monitoring with Grafana Loki (+ Promtail)

Below uses Docker for quick local setup. See detailed commands in `monitoring/loki_setup.txt`.

**Quick start (summary):**

```bash
# Loki
docker run -d --name=loki -p 3100:3100 grafana/loki:2.9.0

# Promtail (scrapes Docker logs)
docker run -d --name=promtail   -v /var/log:/var/log   -v /var/lib/docker/containers:/var/lib/docker/containers:ro   -v $(pwd)/monitoring/promtail-config.yml:/etc/promtail/config.yml   grafana/promtail:2.9.0   -config.file=/etc/promtail/config.yml
```

**Query logs via LogCLI (example):**

```bash
docker run --rm grafana/logcli:2.9.0 --addr=http://host.docker.internal:3100 query   --limit=20 '{{job="docker-logs"}}'
```

> On Linux, replace `host.docker.internal` with your host IP (e.g., `http://127.0.0.1:3100`).

✅ **Output:** `monitoring/loki_setup.txt` describing setup and example commands.

---

## 7) Extra Credit (Optional): MLflow

A tiny MLflow script logs a dummy experiment (local file-based tracking).

Run:

```bash
pip install mlflow
python mlflow/log_dummy.py
```

View the UI:

```bash
mlflow ui --backend-store-uri mlruns --host 0.0.0.0 --port 5000
```

Open `http://localhost:5000` in your browser.

✅ **Output:** `mlflow/log_dummy.py` present and runnable.

---

## Screenshots (to add after you run things)

Create a `screenshots/` folder and add evidence:
- GitHub Actions green run
- `docker run` output
- Nomad `status` / `alloc logs`
- Loki/Promtail/logcli or Grafana UI queries

---

## Notes

- If Nomad cannot pull `hello-devops:latest`, push the image to a registry (e.g., Docker Hub) and set `image = "docker.io/<user>/hello-devops:latest"` inside `nomad/hello.nomad`.
- For Promtail to read container logs, the host Docker logs must be mounted read-only: `/var/lib/docker/containers:/var/lib/docker/containers:ro`.
- Replace `USER/REPO` in the CI badge after pushing the repo.

---

## License

MIT (or your choice)
