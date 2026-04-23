# Digitalisierung

Lokales Dokumenten-System auf einer VirtualBox-VM (Ubuntu). Alles läuft lokal — kein Cloud-Zugriff, keine externen Dienste.

## Stack

- **Paperless-ngx** — Dokumentenarchiv mit OCR → http://localhost:8010
- **AnythingLLM** — KI-Chat über eigene Dokumente → http://localhost:3002
- **Ollama** — lokales LLM, kein Cloud-Modell

## Voraussetzungen

- Docker + Docker Compose
- Ollama mit den benötigten Modellen (`gemma4:e4b`, `nomic-embed-text:latest`)
- Python 3 mit `requests` (`pip install requests`)
- `~/privat/.env` mit den API-Keys (siehe unten)

## Starten

```bash
cd ~/privat/dokumente
docker compose up -d
```

## Konfiguration

`~/privat/.env` (Vorlage: `.env.example`):
```
PAPERLESS_TOKEN=         # API-Token aus Paperless unter Einstellungen → API-Token
ANYTHINGLLM_TOKEN=       # API-Token aus AnythingLLM unter Einstellungen
PAPERLESS_DBPASS=        # Beliebiges sicheres Passwort für die Postgres-Datenbank
PAPERLESS_SECRET_KEY=    # Langer zufälliger String, z.B. mit: python3 -c "import secrets; print(secrets.token_hex(32))"
```

## Skripte

### `paperless_to_anythingllm.py`
Synchronisiert neue Dokumente aus Paperless in den AnythingLLM-Workspace.

```bash
python3 ~/paperless_to_anythingllm.py
```

Bereits synchronisierte Dokument-IDs werden in `~/synced_ids.json` gespeichert.

### `chat.py`
Chat mit den eigenen Dokumenten über AnythingLLM.

```bash
# Interaktiver Modus
chat

# Einzelne Frage
chat Was steht in meiner letzten Rechnung?
```

Der Alias `chat` muss in `~/.bashrc` eingetragen sein:
```bash
alias chat='~/chat.py'
```

## Einrichtung

Skripte klonen und Alias setzen:

```bash
git clone git@github.com:MarcelH-git/digitalisierung.git
cp digitalisierung/chat.py digitalisierung/paperless_to_anythingllm.py ~/
echo "alias chat='~/chat.py'" >> ~/.bashrc
source ~/.bashrc
```

Dann `~/privat/.env` mit den API-Keys anlegen (siehe Konfiguration oben).

## Zugriff von Windows

SSH-Tunnel für die Web-Oberflächen:

```powershell
ssh -p 2222 -L 3002:127.0.0.1:3002 -L 8010:127.0.0.1:8010 BENUTZER@127.0.0.1
```

Dann im Browser `http://localhost:3002` (AnythingLLM) bzw. `http://localhost:8010` (Paperless).
