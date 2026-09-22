# Confidential Ubuntu

A bare Ubuntu workspace that runs inside a confidential VM. You log in as root
over `tinfoil ssh`.

## Deploy

Run the **Tinfoil Release** workflow with a version, then deploy
by repo + tag from the dashboard or:

    tinfoil container create my-cvm \
      --repo tinfoilsh/confidential-ubuntu \
      --tag v0.3.1 \
      --host ...

## Connect

The CVM boots an attested `host-ssh` key that sshd serves as its host key and
the quote endorses. Verify it once and pin it for plain `ssh`:

    tinfoil attest-ssh <enclave-host> --repo tinfoilsh/confidential-ubuntu-tinfoil-test --name workspace --install
    ssh workspace

Or tunnel over the enclave's attested TLS connection, so verifying the
enclave and connecting to it are the same step:

    export TINFOIL_TUNNEL_API_KEY=...
    tinfoil ssh workspace

## Unlock

A volume can be unlocked from within the CVM by sending it the volume key over SSH:

    head -c 64 /dev/urandom > workspace.key    # keep it; losing it loses the data
    tinfoil ssh workspace -- workspace-unlock < workspace.key
