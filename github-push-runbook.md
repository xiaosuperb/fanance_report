# GitHub Push Runbook

## Scope
For `/home/node/.openclaw/workspace/reports` pushing to GitHub from OpenClaw.

## Current Working Setup
- Elevated exec enabled for `webchat` and `dingtalk`
- SSH uses GitHub over port `443`
- Effective SSH key:
  - Private key: `/home/node/.ssh/id_ed25519_github`
  - Public key: `/home/node/.ssh/id_ed25519_github.pub`
- SSH config: `/home/node/.ssh/config`
- Git remote: `git@github.com:xiaosuperb/fanance_report.git`

## SSH Config In Use
```sshconfig
Host github.com
  HostName ssh.github.com
  Port 443
  User git
  IdentityFile /home/node/.ssh/id_ed25519_github
  IdentitiesOnly yes
  StrictHostKeyChecking accept-new
```

## Quick Verification
```bash
ssh -T git@github.com
```
Expected result:
- `Hi xiaosuperb! You've successfully authenticated, but GitHub does not provide shell access.`

## Push Workflow
```bash
cd /home/node/.openclaw/workspace/reports
git status --short
git add .
git commit -m "your message"
git push origin main
```

## Safe Non-Destructive Test
```bash
git -C /home/node/.openclaw/workspace/reports push --dry-run origin main
```
Expected result:
- `Everything up-to-date` or a normal dry-run push summary

## Common Failure Points
- `Host key verification failed`
  - Check `/home/node/.ssh/config`
  - Check `/home/node/.ssh/known_hosts`
  - Re-test with `ssh -T git@github.com`
- `Permission denied (publickey)`
  - Check that `/home/node/.ssh/id_ed25519_github` exists
  - Check that the public key is still added to GitHub
  - Check file permissions: `600` for private key, `700` for `.ssh`
- Push blocked in agent flow
  - Confirm elevated exec is still enabled for the current provider

## Notes
- This setup avoids blocked outbound SSH on port `22` by using `ssh.github.com:443`.
- If GitHub key rotation is needed, replace the key pair and update GitHub with the new public key.
