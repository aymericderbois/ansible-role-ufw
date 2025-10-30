# Ansible Role: UFW

[![MIT License](https://img.shields.io/badge/license-MIT-brightgreen.svg)](LICENSE)

Rôle Ansible minimaliste pour installer et configurer [UFW](https://help.ubuntu.com/community/UFW) (Uncomplicated Firewall) sur Debian 11/12/13.

## Fonctionnalités

- Installation de UFW via APT
- Configuration des politiques par défaut (incoming/outgoing)
- Autorisation automatique SSH (configurable)
- Gestion des règles de firewall personnalisées
- Activation et démarrage automatique du service

## Plateformes supportées

- Debian 11 (Bullseye)
- Debian 12 (Bookworm)
- Debian 13 (Trixie)

## Utilisation

### Installation basique

```yaml
- hosts: servers
  become: true
  roles:
    - aymericderbois.ufw
```

Configuration par défaut :
- Politique incoming : `deny`
- Politique outgoing : `allow`
- SSH autorisé sur le port 22

### Configuration personnalisée

```yaml
- hosts: servers
  become: true
  roles:
    - aymericderbois.ufw
  vars:
    ufw_default_incoming_policy: "deny"
    ufw_default_outgoing_policy: "allow"
    ufw_allow_ssh: true
    ufw_ssh_port: "2222"
    ufw_rules:
      - rule: allow
        port: "80"
        proto: tcp
        comment: "HTTP"
      - rule: allow
        port: "443"
        proto: tcp
        comment: "HTTPS"
```

### Règles avancées

```yaml
- hosts: servers
  become: true
  roles:
    - aymericderbois.ufw
  vars:
    ufw_rules:
      - rule: allow
        port: "3306"
        proto: tcp
        src: "192.168.1.0/24"
        comment: "MySQL from local network"
      - rule: allow
        port: "53"
        proto: udp
        comment: "DNS"
      - rule: deny
        port: "23"
        proto: tcp
        comment: "Block telnet"
```

## Variables

| Variable                       | Défaut   | Description                                    |
|--------------------------------|----------|------------------------------------------------|
| `ufw_default_incoming_policy`  | `deny`   | Politique par défaut pour le trafic entrant    |
| `ufw_default_outgoing_policy`  | `allow`  | Politique par défaut pour le trafic sortant    |
| `ufw_allow_ssh`                | `true`   | Autoriser SSH automatiquement                  |
| `ufw_ssh_port`                 | `22`     | Port SSH à autoriser                           |
| `ufw_rules`                    | `[]`     | Liste des règles personnalisées                |

### Format de `ufw_rules`

```yaml
ufw_rules:
  - rule: allow|deny|limit|reject    # Action (obligatoire)
    port: "80"                        # Port (optionnel)
    proto: tcp|udp                    # Protocole (défaut: tcp)
    direction: in|out                 # Direction (optionnel)
    src: "192.168.1.0/24"            # Source (optionnel)
    dst: "10.0.0.1"                  # Destination (optionnel)
    interface: "eth0"                 # Interface réseau (optionnel)
    comment: "Description"            # Commentaire (optionnel)
```

## Tests

### Avec `make`

```bash
# Lancer les linters
make lint

# Lancer les tests Molecule (Debian 12)
make test

# Lancer les tests sur toutes les plateformes
make test-all
```

### Avec `uv`

```bash
uv run ansible-lint
uv run yamllint .
uv run molecule test
```

## Licence

MIT - [aymericderbois](https://github.com/aymericderbois)

## Auteur

Ce rôle a été créé par [aymericderbois](https://github.com/aymericderbois).
