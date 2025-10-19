# 🛸 Project BlackOil

> *"The Truth is in the Code"*

**A mysterious AI homelab stack inspired by the iconic black oil from The X-Files - your gateway to experimenting with artificial intelligence.**

![Project BlackOil Logo](media/logo_white.png)

---

## 🎯 The Conspiracy

Just like the mysterious black substance from The X-Files, **BlackOil** is the hidden foundation for your AI experiments: powerful, versatile, and shrouded in mystery. 

> *"As the black oil harbored alien life, BlackOil harbors the future of artificial intelligence in your homelab."*

## ✨ Features

### 🚀 **One-Command Deployment**
```bash
ansible-playbook docker-install.yml --ask-become-pass
# or
docker compose -f /opt/blackoil-stack/docker-compose.yml up -d
```

### Included services
- Portainer (Docker GUI)
- Prometheus + Node Exporter (monitoring)
- Grafana (dashboards)
- Postgres + pgAdmin
- TypeMill (CMS)
- Open-WebUI (local LLM frontend)
- Ollama (local LLM runtime)
- JupyterLab (notebook server)
- Flowise (visual LLM workflow builder)
- n8n (workflow automation)
- Optional NVIDIA GPU support for compatible hosts

### Notable additions (updated)
- JupyterLab with optional GPU support (NVIDIA variables wired; device_requests removed for broad compose compatibility).
- Open-WebUI presets: bundled presets to auto-configure Ollama and Jupyter endpoints.
- Flowise and n8n services added, with persistence directories and simple auth defaults.
- Brave Search provider preset for Open-WebUI (mountable presets file).
- Auto-generated default password (UUID) available as ansible var `default_password`.
- Daily SMB sync cron example to back up the project directory.
- Ansible playbook installs nvidia-container-toolkit when available and ensures compose is present.

## ⚙️ Quick config pointers

- Ports & access (defaults)
- Portainer: {{ portainer_port }} (compose var)
- Grafana: {{ grafana_port }}
- WebUI: {{ webui_port }}
- Ollama: {{ ollama_port }}
- Jupyter: {{ jupyter_port }} (token via `jupyter_token` or `default_password`)
- Flowise: {{ flowise_port }} (API key via `flowise_api_key` / defaults to `default_password`)   # added
- n8n: {{ n8n_port }} (basic auth: user `{{ n8n_user }}`, password `n8n_password` / defaults to `default_password`)  # added

- Flowise
  - Data dir: ./flowise/data (persisted)
  - Configure `FLOWISE_API_KEY` in the playbook or export `FLOWISE_API_KEY` to use a custom key.
  - If using external vector DBs or embeddings, add the required services and env vars per Flowise docs.

- n8n
  - Data dir: ./n8n/data (persisted)
  - Basic auth is enabled by default (`N8N_BASIC_AUTH_ACTIVE=true`). Use `n8n_user` and `n8n_password` in the playbook (or override via env).
  - For production use, configure Postgres and Redis persistence per n8n docs.


- Jupyter
  - Exposed on port configured by `jupyter_port` (default 8888).
  - Token set via `jupyter_token` or falls back to `default_password`.
  - GPU: the compose template sets NVIDIA env vars; if your host supports Compose device_requests you may enable GPU allocation — otherwise use the nvidia runtime.

- Open-WebUI presets & Brave Search
  - Presets are provided in ./webui/presets/presets.json and mounted into the container.
  - Brave Search query URL: `https://search.brave.com/search?q=%s` (preset included).


## 🔐 Passwords & secrets
- `default_password` is generated in the playbook with `lookup('pipe','uuidgen')`.
- To print the generated password during the playbook run:
  - Add the debug task (already included): `- debug: msg="Default password: {{ default_password }}"`
  - To avoid leaking secrets, mark tasks `no_log: true` where appropriate.

## 🛠 Troubleshooting
- Compose validation errors (e.g. `device_requests` not allowed): upgrade Docker Compose or remove unsupported keys from the template.
- Duplicate YAML keys: ensure the jupyter service block has a single `deploy` or no `deploy` section.
- If GPU not visible: verify `nvidia-container-toolkit` is installed and Docker restarted.

## 🧭 Where things live
- Playbook: docker-install.yml
- Templates: templates/docker-compose.yml.j2
- Project root (deployed): /opt/blackoil-stack
- WebUI presets: ./webui/presets/presets.json

## 📣 Final notes
This README documents the recent additions: Jupyter + GPU considerations, Open-WebUI presets (Ollama, Jupyter, Brave Search), generated default password, and SMB backup automation. Re-run the Ansible playbook after editing templates or presets to apply changes.