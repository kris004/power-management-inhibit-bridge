# power-management-inhibit-bridge

A small Linux D-Bus bridge from the legacy session-bus
`org.freedesktop.PowerManagement.Inhibit` API to systemd-logind's `idle`
inhibitors.

The intended users are Electron/Chromium applications and minimal Wayland
sessions where an application expects the freedesktop power-management API but
the desktop portal stack does not export it.

> **Status:** Initial project scaffold. The D-Bus service is not implemented
> yet and the current executable is only a placeholder.

## Design goals

- Translate only idle-inhibition requests; do not block explicit/manual
  suspend.
- Hold each logind inhibitor file descriptor for exactly the lifetime of its
  corresponding D-Bus request.
- Release leases on explicit uninhibit, client disconnect, daemon shutdown, or
  failure.
- Integrate as an eagerly started systemd user service while remaining useful
  outside any one desktop environment.
- Produce a fully static `x86_64-unknown-linux-musl` binary.

## Development build

The repository pins Rust 1.97.1 and the musl target in
`rust-toolchain.toml`. Ensure rustup's proxies are first on `PATH`, then build:

```sh
PATH="$HOME/.cargo/bin:$PATH" cargo build --release
```

The resulting executable is under
`target/x86_64-unknown-linux-musl/release/`.

## License

GNU General Public License v3.0 only. See [LICENSE](LICENSE).
