# setup-mocksms

GitHub Action: install and start the [mocksms](https://github.com/Aeomar999/CommPit)
sandbox in CI, outputting its URLs. Your tests send SMS, OTP and email to
it instead of a real provider; nothing is delivered.

## Usage

```yaml
- uses: Aeomar999/setup-mocksms@v1
  id: mocksms
  with:
    otp-code: '123456' # fixed code for deterministic assertions

- run: |
    curl "${{ steps.mocksms.outputs.http-url }}/api/v1/otp/latest?to=%2B15005550006" \
      -H "X-Mocksms: true"
```

## Inputs

| Input | Default | Meaning |
|---|---|---|
| `version` | `latest` | Release tag to install (e.g. `v0.4.0`) |
| `http-port` | `4010` | HTTP port to serve |
| `smtp-port` | `1025` | SMTP port to serve |
| `otp-code` | `123456` | Fixed OTP code for testing |
| `data-dir` | _(fresh temp dir)_ | Data directory; empty isolates every run |

## Outputs

| Output | Meaning |
|---|---|
| `http-url` | Base HTTP URL, e.g. `http://127.0.0.1:4010` |
| `smtp-host` | SMTP host (`127.0.0.1`) |
| `smtp-port` | SMTP port |

## Notes

- Binaries come from CommPit releases (Linux/macOS/Windows, amd64 + Linux
  arm64; Apple Silicon runs the x86_64 build).
- The server runs in the background for the rest of the job. Use a fresh
  `data-dir` (the default) per run for isolation.
- Reset between suites with `mocksms reset --project <id>`, or point each
  suite at its own credential so traffic lands in separate projects.
