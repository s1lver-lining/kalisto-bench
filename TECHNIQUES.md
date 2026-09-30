# Vulnerability and Technique Catalogue

This is the paper's Appendix F: every technique named on an attack path in the
scenario catalogue ([`SCENARIOS.md`](SCENARIOS.md)), in the order it first
appears (from `small_1` to `big_2`). Sourced from each technique's `info.yml`
under URSID's `techniques/<kind>/<name>/`, plus a handful of disclosure dates not
stated there (sudo, activemq, dizquetv, salt-master, overlayfs), verified against
public CVE records.

## How to read the CVE column

A dash (—) marks a technique that is **not a public CVE**. Thirteen of the thirty
fall into this bucket:

- **Exposed credentials (operational mistake, not a software flaw)** — four
  cases: `creds_stored_in_bash_history`, `creds_in_git_history`,
  `creds_in_encrypted_vault`, `creds_in_steganography`.
- **A legitimate service, trust relationship, or known misconfiguration class**
  with no single disclosure to cite — `sshd`, `telnet`, `docker_socket_escape`,
  `suid_gtfobins_priv_esc`, `ftp_cleartext_sync`, `ops_fleet_controller`,
  `redis_unauth`.
- **Vulnerabilities built for this benchmark's own applications** —
  `ssrf_internal_leak` (the Nimbus Ops portal on `big_1`) and `casino_ejs_ssti`
  (the Casino Limit intranet website).

One special case: **`roundcube_deserialize_rce`** keeps its name for wiring
stability but is a *gathering* step in practice. The CVE-2025-49113
object-injection RCE it references could not be reliably weaponised in this
image, so the position was re-cast as credential gathering — a sniffed mailbox
credential logs into webmail and reads a deploy secret out of an email.

## Catalogue

| Technique | CVE | Affected software / OS | Description |
|---|---|---|---|
| `creds_stored_in_bash_history` | — | any Linux, bash | Plaintext credentials pasted into shell commands persist in `~/.bash_history`. |
| `sshd` | — | OpenSSH server | Lateral movement over SSH using already-stolen credentials. |
| `sudo` | CVE-2019-14287 | sudo < 1.8.28 (Linux) | `sudo -u#-1` bypasses a `Runas ALL=(ALL,!root)` exclusion by mapping UID −1 to root. |
| `jenkins_xstream_deserialize` | CVE-2016-0792 | Jenkins < 1.650 (LTS < 1.642.2) | Unsafe XStream/Groovy deserialization via `/createItem` gives unauthenticated RCE. |
| `activemq_jsp_upload_rce` | CVE-2016-3088 | Apache ActiveMQ 5.x < 5.14.0 | Unauthenticated PUT then MOVE uploads and relocates a JSP webshell for RCE. |
| `creds_in_git_history` | — | git (any) | A secret "removed" in a later commit is still recoverable from git's object history. |
| `dizquetv` | CVE-2024-58286 | dizqueTV 1.5.3 | Unsanitised FFMPEG-path setting in the web UI injects shell commands. |
| `creds_in_encrypted_vault` | — | any Linux, OpenSSL vault | Vault is genuinely AES-encrypted, but the master password is offline-crackable. |
| `pidfd_getfd_race` | CVE-2026-46333 | Linux kernel ≥ 4.10 (Debian/Ubuntu/Fedora defaults) | Races `pidfd_getfd()` against a setuid-root process's exit to steal its file descriptor. |
| `erlang_ssh_rce` | CVE-2025-32433 | Erlang/OTP `ssh` ≤ 27.3.2 / 26.2.5.10 / 25.3.2.19 | SSH daemon evaluates a pre-auth channel request as Erlang code. |
| `telnet_auth_bypass` | CVE-2026-24061 | GNU inetutils-telnetd < 2.6 | `telnetd` passes `USER="-f root"` straight to `login`, bypassing authentication. |
| `creds_in_steganography` | — | any Linux, generic image file | Credentials appended past an image's EOF marker, trivially recovered. |
| `telnet` | — | netkit-telnet / in.telnetd | Lateral movement over Telnet using already-stolen credentials. |
| `copyfail` | CVE-2026-31431 | Linux kernel (authenc crypto template) | Writes 4 attacker-chosen bytes into any readable file's page cache. |
| `react2shell` | CVE-2025-55182 | Next.js (React Server Components) | Malformed React Server Component payload reaches a Node.js exec primitive. |
| `ssrf_internal_leak` | — | Nimbus Ops portal (built for `big_1`) | Unauthenticated healthcheck fetches attacker URLs, leaking an internal secret. |
| `docker_socket_escape` | — | Docker Engine, "docker" group membership | `docker`-group membership lets a user bind-mount the host filesystem as root. |
| `salt_master_rce` | CVE-2020-11651 / CVE-2020-11652 | SaltStack Salt < 2019.2.4, 3000.x < 3000.2 | Unauthenticated ZeroMQ channel dumps the root key or broadcasts commands to minions. |
| `proftpd_mod_sql_rce` | CVE-2026-42167 | ProFTPD ≤ 1.3.9 (mod_sql, PostgreSQL backend) | Unescaped FTP `USER` value reaches SQL, up to `COPY TO PROGRAM` for RCE. |
| `cve_2023_0386_overlayfs_priv_esc` | CVE-2023-0386 | Linux kernel 5.11–5.15.90, 5.16–6.1.8 (OverlayFS) | OverlayFS copy-up mishandles capabilities, escalating an unprivileged user to root. |
| `casino_ejs_ssti` | — | Casino Limit intranet website (Node.js/EJS) | Query param reactivates a disabled EJS include; a malicious upload completes the RCE. |
| `solr_velocity_rce` | CVE-2019-17558 | Apache Solr 5.0.0–8.3.1 | An unauthenticated Config API call enables Velocity templates, then a query renders one for RCE. |
| `suid_gtfobins_priv_esc` | — | any Linux, a setuid-root GTFOBins binary | A setuid-root utility (`env`/`find`/`python`) is re-exec'd in a mode that spawns a root shell. |
| `ftp_cleartext_sync` | — | any Linux, FTP client (cleartext) | A scheduled job authenticates to an FTP drop in cleartext; the password exists only on the wire. |
| `roundcube_deserialize_rce` | CVE-2025-49113 | Roundcube 1.6.10 | A sniffed mailbox credential logs into webmail and reads a deploy secret out of an email. |
| `vite_file_read` | CVE-2025-30208 | Vite dev server 6.2.2 (fixed 6.2.3) | A dev-server URL suffix bypasses the file allow-list, reading any file including a private key. |
| `tomcat_partial_put_rce` | CVE-2025-24813 | Apache Tomcat 9.0.0.M1–9.0.98, 10.1.x, 11.0.x | A partial PUT writes a serialized session object; a crafted JSESSIONID then triggers deserialization RCE. |
| `ops_fleet_controller` | — | any, SSH deploy-key trust relationship | A deploy controller's SSH key is trusted as root by every node it manages. |
| `redis_unauth` | — | Redis (no `requirepass`, non-loopback bind) | An unauthenticated client relocates Redis's database file to write an SSH key to disk. |
| `langflow_rce` | CVE-2025-3248 | Langflow < 1.3.0 | An unauthenticated endpoint execs attacker Python, but here the loot leads nowhere — a dead end. |
