# Postfix Recipient Tracing Lab

Trace a benign email through a local mail server to identify all delivered recipients, including addresses absent from the visible message headers.

**Abdellah Bayar · 5 October 2026**

## Result

**Five local mailboxes received the same message.** Alice's received copy lists three visible recipients: Alice in `To`, and Bob and Edoardo in `Cc`. Postfix delivery records identify two additional recipients, Carol and Vittorio, included as blind-copy recipients in the controlled setup.

| Recipient | Visible in Alice's copy | Confirmed local delivery |
|---|---|---|
| `alice@lab.test` | To | Yes |
| `bob@lab.test` | Cc | Yes |
| `carol@lab.test` | No | Yes |
| `edoardo@lab.test` | Cc | Yes |
| `vittorio@lab.test` | No | Yes |

## Investigation method

1. Inspect Alice's received message and record its Message-ID and visible recipients.
2. Find that Message-ID in the Postfix log and identify the associated queue ID.
3. Follow every delivery record for that queue and count the distinct successful recipients.
4. Compare the delivered recipients with the visible `To` and `Cc` headers.

Reference Message-ID: `<20261005091903.003053@soc-mail-lab>`

Reference queue ID: `20B261E01F8`

The SMTP server accepted five envelope recipients. Each has a corresponding `status=sent (delivered to maildir)` record. Server logs establish successful mailbox delivery, rather than just acceptance into the queue.

## Environment

- Windows Hyper-V with an Ubuntu Server 24.04.5 LTS VM.
- Postfix 3.8.6, Swaks 20240103.0 and rsyslog.
- Five local recipient accounts, Maildir storage and disabled interactive login.
- Local-only SMTP listening on loopback; the test message was sent to `127.0.0.1:25` within the VM.
- Domain: `lab.test`.

## Documentation

- [Analysis report (PDF)](Postfix-Recipient-Tracing-Lab-Analysis-Abdellah-Bayar.pdf) - lab purpose, technologies, evidence, delivery timeline and findings.
- [Complete reproduction guide (PDF)](Postfix-Recipient-Tracing-Lab-Reproduction-Guide.pdf) - VM installation, mail configuration, commands, investigation, evidence export and troubleshooting.
- [Investigation findings and delivery timeline](Recipient-Tracing.md)
- [Step-by-step reproduction guide](Recipient-Tracing-Reproduction-Guide.md)

## Original evidence

- [Alice's received message](soc-trace-alice-02.eml)
- [Prepared message before transmission](soc-trace-message-02.eml)
- [SMTP session transcript](soc-trace-smtp-02.txt)
- [Postfix delivery log extract](soc-trace-delivery-02.log)
- [SHA-256 checksums](Recipient-Tracing-Checksums.sha256)

The four evidence files were exported from the VM and preserved without editing. The log extract contains the records for the reference queue. Git attributes preserve the exported files' line endings when the repository is cloned.

## Scope

This is a controlled experiment using a harmless plain-text message and five lab accounts. It establishes local delivery, without measuring opening, clicking or replying.

Recipient roles are known from the setup. In production, a delivered address absent from `To` and `Cc` could also result from forwarding or a distribution list; its absence alone would not prove blind-copy delivery.

## References

- [Ubuntu: Postfix configuration and mail logs](https://ubuntu.com/server/docs/how-to/mail-services/install-postfix/)
- [Swaks reference](https://www.jetmore.org/john/code/swaks/latest/doc/ref.txt)
- [Git: preserving file line endings with attributes](https://git-scm.com/docs/gitattributes)
