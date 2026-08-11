# Keycloak Password Denylist

Publishes the SecLists one-million-entry password list as a signed OCI image
for use with Kubernetes image volumes and Keycloak.

## Published image

```text
ghcr.io/apeiros-innovations/keycloak-password-denylist:<SecLists-ref>.<revision>
```

## Source

[`vendir.yml`](vendir.yml) downloads
`xato-net-10-million-passwords-1000000.txt` from the configured SecLists
release into `dist/rootfs`.

```bash
mise install
vendir sync
```

Commit `vendir.lock.yml` whenever the configured SecLists release changes.

## Keycloak consumption

Use the digest reported by the release workflow:

```yaml
volumes:
  - name: password-blacklist
    image:
      reference: ghcr.io/apeiros-innovations/keycloak-password-denylist:2026.1.1@sha256:<digest>
      pullPolicy: IfNotPresent
```

Mount the image into the Keycloak password-blacklist directory:

```yaml
volumeMounts:
  - name: password-blacklist
    mountPath: /opt/keycloak/data/password-blacklists
    readOnly: true
```

Configure the corresponding Keycloak password policy:

```text
passwordBlacklist(xato-net-10-million-passwords-1000000.txt)
```

The repository uses the term `denylist`; Keycloak retains `blacklist` in its
directory and policy names.