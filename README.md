# Ansible-role-haproxy-setup

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/willbrid/ansible-role-haproxy-setup/blob/main/LICENSE)

Le rôle **ansible-role-haproxy-setup** permet d’installer et de configurer **HAProxy**, avec l'**exporteur prometheus** intégré, de manière simple, flexible et déclarative. Grâce à une structure YAML claire, les utilisateurs peuvent définir leurs blocs **global**, **defaults**, **frontends**, **backends** et **stats** sans effort, tout en gardant un contrôle total sur les options avancées de **HAProxy**.

## Exigences

- Distributions RedHat ou Debian utilisant **systemd** comme gestionnaire de services.
- HAProxy version >= 2.6

## Description des Variables

## Dépendances

Aucune.

## Exemple Playbook

- Installation du rôle

```bash
mkdir -p $HOME/install-haproxy/roles
```

```bash
vim $HOME/install-haproxy/requirements.yml
```

```yaml
- name: ansible-role-haproxy-setup
  src: git+https://github.com/willbrid/ansible-role-haproxy-setup.git
  version: v0.0.1
```

```bash
cd $HOME/install-haproxy && ansible-galaxy install -r requirements.yml --roles-path roles
```

> Note: On suppose qu’un fichier `hosts.ini` (dans le repertoire `$HOME/install-haproxy`) est défini, contenant l’inventaire des serveurs de groupe `server`, utilisant des distributions `Debian` ou `RedHat`.

> Note: On suppose qu'un repertoire `files` (dans le repertoire `$HOME/install-haproxy`) est défini et contient un fichier `server.crt`.

- Utilisation du rôle dans un playbook

```bash
vim $HOME/install-haproxy/playbook.yml
```

--- Exemple de contenu du fichier playbook.yml pour `Rocky linux 9`

```yaml
---
- hosts: server
  become: true

  vars:
    haproxy_version: "2.8"
    haproxy_use_full_version: false
    haproxy_use_community_repo: true
    haproxy_frontend_ssl_enable: true
    haproxy_frontend_ssl_certificates:
      - {src_filename: "files/server.crt", dest_filename: "server.crt"}
    haproxy_repo_community: "https://packages.zenetys.com/latest/redhat/$releasever/RPMS/$basearch"
    haproxy_repo_keyring_url: "https://packages.zenetys.com/RPM-GPG-KEY-ZENETYS-ZLAB-Packaging-v1"
    haproxy_repo_package_name: "haproxy28z"
    haproxy_config_defaults:
      - name: "http"
        custom_options:
          - "mode http"
          - "option httplog"
          - "log global"
          - "timeout client 1m"
          - "timeout server 1m"
          - "timeout connect 10s"
          - "timeout http-keep-alive 2m"
          - "timeout queue 15s"
    haproxy_config_frontends:
      - name: "frontend_site1"
        binds:
          - "*:8000"
        from_default: "http"
        default_backend: "backend_site1"
      - name: "frontend_site2"
        binds:
          - "*:8080"
          - "*:8443"
        from_default: "http"
        default_backend: "backend_site2"
        custom_options:
          - "mode http"
    haproxy_config_backends:
      - name: "backend_site1"
        from_default: "http"
        servers:
          - "server site1-web 127.0.0.1:8081 check"
      - name: "backend_site2"
        from_default: "http"
        custom_options:
          - "balance roundrobin"
        servers:
          - "server site2-web1 127.0.0.1:8084 check"
          - "server site2-web2 127.0.0.1:8085 check"
          - "server site2-web3 127.0.0.1:8086 check"

  roles:
    - ansible-role-haproxy-setup
```

--- Exemple de contenu du fichier playbook.yml pour `Debian 12`

```yaml
- hosts: server
  become: true

  vars:
    haproxy_version: "2.8"
    haproxy_use_full_version: false
    haproxy_use_community_repo: true
    haproxy_frontend_ssl_enable: false
    haproxy_frontend_ssl_certificates: []
    haproxy_repo_community: "deb [signed-by=/usr/share/keyrings/haproxy-archive-keyring.gpg] http://haproxy.debian.net bookworm-backports-2.8 main"
    haproxy_repo_keyring_url: "https://haproxy.debian.net/haproxy-archive-keyring.gpg"
    haproxy_repo_keyring_path: "/usr/share/keyrings/haproxy-archive-keyring.gpg"
    haproxy_config_defaults:
      - name: "http"
        custom_options:
          - "mode http"
          - "option httplog"
          - "log global"
          - "timeout client 1m"
          - "timeout server 1m"
          - "timeout connect 10s"
          - "timeout http-keep-alive 2m"
          - "timeout queue 15s"
    haproxy_config_frontends:
      - name: "frontend_site1"
        binds:
          - "*:8000"
        from_default: "http"
        default_backend: "backend_site1"
      - name: "frontend_site2"
        binds:
          - "*:8080"
          - "*:8443"
        from_default: "http"
        default_backend: "backend_site2"
        custom_options:
          - "mode http"
    haproxy_config_backends:
      - name: "backend_site1"
        from_default: "http"
        servers:
          - "server site1-web 127.0.0.1:8081 check"
      - name: "backend_site2"
        from_default: "http"
        custom_options:
          - "balance roundrobin"
        servers:
          - "server site2-web1 127.0.0.1:8084 check"
          - "server site2-web2 127.0.0.1:8085 check"
          - "server site2-web3 127.0.0.1:8086 check"

  roles:
    - ansible-role-haproxy-setup
```

--- Exemple de contenu du fichier playbook.yml pour `Debian 11`

```yaml
- hosts: server
  become: true

  vars:
    haproxy_version: "2.8"
    haproxy_use_full_version: false
    haproxy_use_community_repo: true
    haproxy_frontend_ssl_enable: false
    haproxy_frontend_ssl_certificates: []
    haproxy_repo_community: "deb [signed-by=/usr/share/keyrings/haproxy-archive-keyring.gpg] http://haproxy.debian.net bullseye-backports-2.8 main"
    haproxy_repo_keyring_url: "https://haproxy.debian.net/haproxy-archive-keyring.gpg"
    haproxy_repo_keyring_path: "/usr/share/keyrings/haproxy-archive-keyring.gpg"
    haproxy_config_defaults:
      - name: "http"
        custom_options:
          - "mode http"
          - "option httplog"
          - "log global"
          - "timeout client 1m"
          - "timeout server 1m"
          - "timeout connect 10s"
          - "timeout http-keep-alive 2m"
          - "timeout queue 15s"
    haproxy_config_frontends:
      - name: "frontend_site1"
        binds:
          - "*:8000"
        from_default: "http"
        default_backend: "backend_site1"
      - name: "frontend_site2"
        binds:
          - "*:8080"
          - "*:8443"
        from_default: "http"
        default_backend: "backend_site2"
        custom_options:
          - "mode http"
    haproxy_config_backends:
      - name: "backend_site1"
        from_default: "http"
        servers:
          - "server site1-web 127.0.0.1:8081 check"
      - name: "backend_site2"
        from_default: "http"
        custom_options:
          - "balance roundrobin"
        servers:
          - "server site2-web1 127.0.0.1:8084 check"
          - "server site2-web2 127.0.0.1:8085 check"
          - "server site2-web3 127.0.0.1:8086 check"

  roles:
    - ansible-role-haproxy-setup
```

--- Exemple de contenu du fichier playbook.yml pour `Ubuntu 24.04`

```yaml
- hosts: server
  become: true

  vars:
    haproxy_version: "2.8"
    haproxy_use_full_version: false
    haproxy_use_community_repo: true
    haproxy_frontend_ssl_enable: false
    haproxy_frontend_ssl_certificates: []
    haproxy_repo_community: 'ppa:vbernat/haproxy-2.8'
    haproxy_repo_dependencies: ["software-properties-common"]
    haproxy_config_defaults:
      - name: "http"
        custom_options:
          - "mode http"
          - "option httplog"
          - "log global"
          - "timeout client 1m"
          - "timeout server 1m"
          - "timeout connect 10s"
          - "timeout http-keep-alive 2m"
          - "timeout queue 15s"
    haproxy_config_frontends:
      - name: "frontend_site1"
        binds:
          - "*:8000"
        from_default: "http"
        default_backend: "backend_site1"
      - name: "frontend_site2"
        binds:
          - "*:8080"
          - "*:8443"
        from_default: "http"
        default_backend: "backend_site2"
        custom_options:
          - "mode http"
    haproxy_config_backends:
      - name: "backend_site1"
        from_default: "http"
        servers:
          - "server site1-web 127.0.0.1:8081 check"
      - name: "backend_site2"
        from_default: "http"
        custom_options:
          - "balance roundrobin"
        servers:
          - "server site2-web1 127.0.0.1:8084 check"
          - "server site2-web2 127.0.0.1:8085 check"
          - "server site2-web3 127.0.0.1:8086 check"

  roles:
    - ansible-role-haproxy-setup
```

--- Exemple de contenu du fichier playbook.yml pour `Ubuntu 22.04`

```yaml
- hosts: server
  become: true

  vars:
    haproxy_version: "2.8"
    haproxy_use_full_version: false
    haproxy_use_community_repo: true
    haproxy_frontend_ssl_enable: false
    haproxy_frontend_ssl_certificates: []
    haproxy_repo_community: 'ppa:vbernat/haproxy-2.8'
    haproxy_repo_dependencies: ["software-properties-common", "dirmngr"]
    haproxy_config_defaults:
      - name: "http"
        custom_options:
          - "mode http"
          - "option httplog"
          - "log global"
          - "timeout client 1m"
          - "timeout server 1m"
          - "timeout connect 10s"
          - "timeout http-keep-alive 2m"
          - "timeout queue 15s"
    haproxy_config_frontends:
      - name: "frontend_site1"
        binds:
          - "*:8000"
        from_default: "http"
        default_backend: "backend_site1"
      - name: "frontend_site2"
        binds:
          - "*:8080"
          - "*:8443"
        from_default: "http"
        default_backend: "backend_site2"
        custom_options:
          - "mode http"
    haproxy_config_backends:
      - name: "backend_site1"
        from_default: "http"
        servers:
          - "server site1-web 127.0.0.1:8081 check"
      - name: "backend_site2"
        from_default: "http"
        custom_options:
          - "balance roundrobin"
        servers:
          - "server site2-web1 127.0.0.1:8084 check"
          - "server site2-web2 127.0.0.1:8085 check"
          - "server site2-web3 127.0.0.1:8086 check"

  roles:
    - ansible-role-haproxy-setup
```

- Exécution du playbook 

```bash
cd $HOME/install-haproxy && ansible-playbook -i hosts.ini playbook.yml
```

## Licence

MIT

## Informations sur l'auteur

William Bridge NGASSAM
