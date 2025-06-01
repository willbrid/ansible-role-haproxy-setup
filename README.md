# Ansible-role-haproxy-setup

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/willbrid/ansible-role-haproxy-setup/blob/main/LICENSE)

Le rôle **ansible-role-haproxy-setup** permet d’installer et de configurer **HAProxy**, avec l'**exporteur prometheus** intégré, de manière simple, flexible et déclarative. Grâce à une structure YAML claire, les utilisateurs peuvent définir leurs blocs **global**, **defaults**, **frontends**, **backends** et **stats** sans effort, tout en gardant un contrôle total sur les options avancées de **HAProxy**.

## Exigences

- Distributions RedHat ou Debian utilisant **systemd** comme gestionnaire de services.
- HAProxy version >= 2.6

## Description des Variables

|Nom|Type|Description|Obligatoire|Valeur par défaut|
|---|----|-----------|-----------|-----------------|
`haproxy_version`|str|version de haproxy à installer. Format : x.y ou x.y.z|non|`"2.8"`
`haproxy_use_full_version`|bool|indique s’il faut forcer l’utilisation d’une version complète de haproxy au format x.y.z|non|`false`
`haproxy_use_community_repo`|bool|indique s’il faut forcer l’utilisation d’un dépôt communautaire de haproxy|non|`false`
`haproxy_repo_community`|str|définition du dépôt communautaire : url, ppa ou une option apt signed-by|non|`""`
`haproxy_repo_package_name`|str|le nom du package haproxy dans un dépôt spécifique : s'il n'est pas mentionné alors la chaine de caractères `haproxy` sera utilisée|non|`""`
`haproxy_repo_dependencies`|list(str)|paquets de dépendances à installer avant l'installation de haproxy|non|`[]`
`haproxy_repo_keyring_url`|str|l'URL de la clé gpg du dépôt|non|`""`
`haproxy_repo_keyring_path`|str|le chemin complet du fichier de clé gpg téléchargé du dépôt|non|`""`
`haproxy_frontend_ssl_enable`|bool|indique s'il faut autoriser la création du repertoire de certificats et uploader les fichiers certificats à renseigner dans les blocs frontend|non|`false`
`haproxy_frontend_ssl_certificates`|list(dict)|permet de renseigner une liste de certificat à uploader dont chaque item est un dictionnaire de clés : `src_filename` et `dest_filename`|non|`[]`
`haproxy_frontend_ssl_certificates[].src_filename`|str|chemin relatif ou complet du fichier certificat à uploader|oui|``
`haproxy_frontend_ssl_certificates[].dest_filename`|str|chemin relatif ou complet de l'emplacement de destination du fichier certificat à uploader|oui|``
`haproxy_config_global`|dict|dictionnaire de configuration globale|oui|``
`haproxy_config_global.enable_zero_warning`|bool|clé de `haproxy_config_global` permettant d'activer l'option `zero_warning`|non|`true`
`haproxy_config_global.maxconn`|int|clé de `haproxy_config_global` permettant de configurer l'option `maxconn`|non|`4000`
`haproxy_config_global.logs`|list(str)|clé de `haproxy_config_global` permettant de configurer les lignes d'options `log`|non|dépendant de la distribution 
`haproxy_config_global.stats_socket_configs`|list(str)|clé de `haproxy_config_global` permettant de configurer les lignes de `stats socket`|non|`[]`
`haproxy_config_global.security_ssl_policies`|list(str)|clé de `haproxy_config_global` permettant de configurer les politiques de sécurité ssl|non|``
`haproxy_config_global.custom_options`|list(str)|clé de `haproxy_config_global` permettant de configurer les options personnalisées|non|``
`haproxy_config_defaults`|list(dict)|liste de configuration des blocs `defaults`|non|`[]`
`haproxy_config_defaults[].name`|str|nom unique d'un bloc `defaults`|oui|``
`haproxy_config_defaults[].custom_options`|list(str)|liste d'options personnalisées d'un bloc `defaults`|oui|``
`haproxy_config_frontends`|list(dict)|liste de configuration des blocs `frontend`|non|`[]`
`haproxy_config_frontends[].name`|str|nom unique d'un bloc `frontend`|oui|``
`haproxy_config_frontends[].binds`|list(str)|liste d'options `bind` d'un bloc `frontend`|oui|``
`haproxy_config_frontends[].from_default`|str|nom d'un bloc par défaut `defaults` pour un bloc `frontend`|non|`""`
`haproxy_config_frontends[].default_backend`|str|nom d'un backend par défaut pour un bloc `frontend`|non|`""`
`haproxy_config_frontends[].custom_options`|list(str)|liste d'options personnalisées d'un bloc `frontend`|non|`[]`
`haproxy_config_backends`|list(dict)|liste de configuration des blocs `backend`|non|`[]`
`haproxy_config_backends[].name`|str|nom unique d'un bloc `backend`|oui|``
`haproxy_config_backends[].from_default`|str|nom d'un bloc par défaut `defaults` pour un bloc `backend`|non|`""`
`haproxy_config_backends[].servers`|list(str)|liste d'options `server` d'un bloc `backend`|oui|``
`haproxy_config_backends[].custom_options`|list(str)|liste d'options personnalisées d'un bloc `backend`|non|`[]`
`haproxy_config_stats`|dict|dictionnaire de configuration du bloc du service statistique|oui|``
`haproxy_config_stats.enable`|bool|clé de `haproxy_config_stats` pour indiquer s'il faut activer le service statistique|non|`true`
`haproxy_config_stats.name`|str|clé de `haproxy_config_stats` pour le nom du bloc du service statistique|oui|``
`haproxy_config_stats.from_default`|str|clé de `haproxy_config_stats` pour le nom d'un bloc par défaut `defaults` pour le bloc du service statistique|non|`""`
`haproxy_config_stats.bind`|str|clé de `haproxy_config_stats` pour indiquer le port d'écoute du service statistique|oui|``
`haproxy_config_stats.uri`|str|clé de `haproxy_config_stats` pour indiquer l'uri du service statistique|non|`"/stats"`
`haproxy_config_stats.exporter_enable`|bool|clé de `haproxy_config_stats` pour activer ou non l'exporteur prometheus au niveau du service statistique|non|`true`
`haproxy_config_stats.exporter_path`|str|clé de `haproxy_config_stats` pour configurer l'uri de l'exporteur prometheus au niveau du service statistique|non|`"/metrics"`
`haproxy_config_stats.custom_options`|list(str)|clé de `haproxy_config_stats` pour configurer les options personnalisées du service statistique|non|`[]`

> Note: Pour la configuration d'un dépôt `RedHat`, il faudrati consulter le site [https://github.com/haproxy/wiki/wiki/Packages](https://github.com/haproxy/wiki/wiki/Packages).

> Note: Pour la configuration d'un dépôt `Debian` ou `Ubuntu` (les variables : `haproxy_repo_community`, `haproxy_repo_keyring_url`, `haproxy_repo_keyring_path` ou `haproxy_repo_dependencies`), il faudrait consulter le site [https://haproxy.debian.net/](https://haproxy.debian.net/).

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
