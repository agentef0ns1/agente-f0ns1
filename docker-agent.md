---
layout: default
---

# Building the Agent Inside a Docker Image

Your autonomous pentesting agent will run inside a Docker container and communicate with the local LLM (Ollama) running on the host machine.

---

## High‑Level Architecture

- Host Machine  
  - Runs Ollama with the local LLM (`pentest-agent` model)  
  - Exposes HTTP API on `localhost:11434`  

- Docker Container  
  - Runs the autonomous agent  
  - Connects to the LLM via `http://host.docker.internal:11434` (or host networking)  
  - Executes OS commands (e.g., `nmap`, `curl`) inside the container  
  - Sends results back into the reasoning loop  

---

## Example Dockerfile

```Dockerfile
# Imagen base de Python
FROM python:3.10-slim

# Instalamos el BINARIO de nmap (el motor)
RUN apt-get update && apt-get install -y \
    nmap \
    curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Instalamos las LIBRERÍAS de Python necesarias
# python-nmap: para interactuar con el binario desde código
# ollama: para invocar a tu modelo 1.5B
RUN pip install python-nmap ollama

COPY agent.py .

CMD ["python", "agent.py"]
```

## Simple agent process

```python
import nmap
import ollama
import re
import sys

MODEL = "my-pentest-agent"

def extract_command(text):
    # Buscamos la línea de nmap
    match = re.search(r'nmap\s+[\w\s\-\.\/=<>\d,:]+', text)
    if match:
        cmd = match.group(0).strip().replace("sudo ", "")
        
        # --- NUEVA LÓGICA DE LIMPIEZA ---
        # 1. Eliminar scripts duplicados (nmap falla si se repiten flags)
        parts = cmd.split()
        unique_parts = []
        for p in parts:
            if p not in unique_parts: unique_parts.append(p)
        cmd = " ".join(unique_parts)
        
        # 2. Cortafuegos de seguridad: Si el modelo se vuelve loco con los scripts
        # limitamos a los primeros 3 para mantener la operatividad.
        if "--script=" in cmd:
            scripts_part = [p for p in cmd.split() if p.startswith("--script=")][0]
            scripts_list = scripts_part.replace("--script=", "").split(",")
            if len(scripts_list) > 5:
                print(f"[!] Limpiando exceso de scripts ({len(scripts_list)} detectados)...")
                cmd = cmd.replace(scripts_part, "--script=default,discovery")
        
        return cmd
    return None

def run_agente():
    nm = nmap.PortScanner()
    print("\n" + "="*60)
    print("      NMAP AGENT F0NS1 - MODO AUDITORÍA ESPECIALIZADO")
    print("="*60)

    while True:
        try:
            user_prompt = input("\n[?] Objetivo o red (ej: red local 192.168.1.0/24) o 'salir': ")
            if user_prompt.lower() in ['salir', 'exit', 'q']: break

            # --- FASE 1: DESCUBRIMIENTO DE HOSTS (Host Discovery) ---
            print(f"\n[*] FASE 1: Enumerando hosts activos...")
            # Forzamos un escaneo de lista/ping para rapidez
            nm.scan(hosts=user_prompt, arguments='-sn')
            hosts_vivos = nm.all_hosts()

            if not hosts_vivos:
                print("[!] No se detectaron hosts activos en el rango.")
                continue

            print(f"[+] Hosts detectados: {len(hosts_vivos)}")
            for host in hosts_vivos:
                print(f"  -> [UP] {host} ({nm[host].hostname()})")

            # --- FASE 2 y 3: PARA CADA HOST DETECTADO ---
            for host in hosts_vivos:
                print(f"\n" + "*"*50)
                print(f"[*] PROCESANDO HOST: {host}")
                
                # FASE 2: Escaneo Rápido Agresivo (TCP)
                print(f"[*] FASE 2: Escaneo agresivo rápido (Puertos comunes)...")
                # Invocamos al modelo para obtener los mejores flags agresivos para este host
                res_model = ollama.generate(model=MODEL, prompt=f"escaneo agresivo rapido de {host}")
                print("res_model ", res_model)
                cmd_fast = extract_command(res_model['response']) or f"nmap -T4 -F -A {host}"
                #print("cmd_fast ", cmd_fast)
                args_fast = cmd_fast.replace("nmap", "").replace("<ip>", host).strip()
                
                nm.scan(hosts=host, arguments=args_fast)
                
                # Reporte rápido por pantalla
                if host in nm.all_hosts():
                    for proto in nm[host].all_protocols():
                        ports = nm[host][proto].keys()
                        print(f"  [+] Puertos abiertos (Quick): {list(ports)}")

                # FASE 3: Enumeración Exhaustiva con Scripts (NSE)
                print(f"[*] FASE 3: Enumeración exhaustiva (Scripts NSE)...")
                # Pedimos al modelo scripts exhaustivos basándonos en los puertos abiertos
                res_enum = ollama.generate(model=MODEL, prompt=f"enumeracion exhaustiva con scripts de todos los puertos de {host}")
                cmd_enum = extract_command(res_enum['response']) or f"nmap -p- -sV -sC {host}"
                args_enum = cmd_enum.replace("nmap", "").replace("<ip>", host).strip()

                print(f"[*] Ejecutando: nmap {args_enum}")
                nm.scan(hosts=host, arguments=args_enum)

                # Reporte Detallado Final
                if host in nm.all_hosts():
                    for proto in nm[host].all_protocols():
                        for port in sorted(nm[host][proto].keys()):
                            data = nm[host][proto][port]
                            print(f"  - Puerto {port}: {data['name']} | {data['version']} | {data['product']}")
                    
                    if 'hostscript' in nm[host]:
                        print("\n  [!] Hallazgos NSE:")
                        for script in nm[host]['hostscript']:
                            print(f"    -> {script['id']}: {script['output']}")

        except Exception as e:
            print(f"[!!!] Error: {e}")

if __name__ == "__main__":
    run_agente()
```

## Build docker agent

```bash
docker build -t nmap-agent-expert .
[+] Building 0.9s (10/10) FINISHED                                        docker:default
 => [internal] load build definition from Dockerfile                                0.0s
 => => transferring dockerfile: 473B                                                0.0s
 => [internal] load metadata for docker.io/library/python:3.10-slim                 0.8s
 => [internal] load .dockerignore                                                   0.0s
 => => transferring context: 2B                                                     0.0s
 => [1/5] FROM docker.io/library/python:3.10-slim@sha256:e508a34e5491225a76fbb9e0f  0.0s
 => => resolve docker.io/library/python:3.10-slim@sha256:e508a34e5491225a76fbb9e0f  0.0s
 => [internal] load build context                                                   0.0s
 => => transferring context: 30B                                                    0.0s
 => CACHED [2/5] RUN apt-get update && apt-get install -y     nmap     curl     &&  0.0s
 => CACHED [3/5] WORKDIR /app                                                       0.0s
 => CACHED [4/5] RUN pip install python-nmap ollama                                 0.0s
 => CACHED [5/5] COPY agent.py .                                                    0.0s
 => exporting to image                                                              0.1s
 => => exporting layers                                                             0.0s
 => => exporting manifest sha256:31944c0b8f481ea2672f837173a2abb294c9f3a1a28e55648  0.0s
 => => exporting config sha256:aa67f62380a4fb22d843d27bd7a64be91262a91977e264fa8e2  0.0s
 => => exporting attestation manifest sha256:7ace24a6d5be7c4f5897166e0704b37eb797e  0.0s
 => => exporting manifest list sha256:0a4a05c2ca37554e0b21d4547a1408a12e5a554ac5e7  0.0s
 => => naming to docker.io/library/nmap-agent-expert:latest                         0.0s
 => => unpacking to docker.io/library/nmap-agent-expert:latest                      0.0s
                                            
```



---

[back](./)