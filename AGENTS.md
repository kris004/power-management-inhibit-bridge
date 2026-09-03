# Repository Guidelines

This is a public Rust project. Treat every file, commit, branch, tag, and
reachable object as potentially public.

## Public boundary

- Never commit secrets, private network details, personal data, private links,
  workstation identifiers, or unnecessary absolute paths.
- Keep documentation, examples, fixtures, and defaults portable for external
  contributors.
- Preserve unrelated work and stage only the paths owned by the current task.
- Keep `Cargo.lock` committed for the application binary.

## Architecture invariants

- Implement the session-bus `org.freedesktop.PowerManagement.Inhibit`
  interface and bridge leases to systemd-logind on the system bus.
- Map requests only to logind's `idle` inhibitor class so explicit/manual
  suspend remains available.
- Associate every held inhibitor file descriptor with both its returned cookie
  and originating D-Bus sender.
- Release leases on explicit uninhibit, sender disappearance, shutdown, and
  error paths. Do not trade correctness here for a simpler lifecycle.
- Avoid desktop-specific assumptions unless they are documented and optional.

## Development and validation

- Use stable Rust 1.97.1, edition 2024, and the pinned musl target.
- Prefer safe Rust. Any `unsafe` block needs a narrow justification and a test
  covering its contract.
- Before committing implementation changes, run:

  ```sh
  PATH="$HOME/.cargo/bin:$PATH" cargo fmt --all -- --check
  PATH="$HOME/.cargo/bin:$PATH" cargo clippy --all-targets --all-features -- -D warnings
  PATH="$HOME/.cargo/bin:$PATH" cargo test --all-targets --all-features
  PATH="$HOME/.cargo/bin:$PATH" cargo build --release
  ```

- Keep live logind/session-bus interaction out of unit tests. Use an isolated
  bus or a narrowly controlled integration harness for behavior tests.
- Do not install binaries, enable user units, or modify the live workstation
  without explicit authorization.

## Publication and releases

- This request authorizes the initial public scaffold push only. Future pushes,
  visibility changes, releases, and deployment require explicit authorization.
- Before the first public release, scan the working tree and reachable history
  for secrets and environment-specific material.
- Releases require a hardware-backed OpenPGP-signed annotated version tag on
  protected `main`; never substitute an unsigned or lightweight tag.
- Pin third-party GitHub Actions to full commit SHAs with least-privilege job
  permissions. Build and publish the same verified artifact bytes.
- Never rewrite, delete, or recreate a published release tag as routine
  recovery.
