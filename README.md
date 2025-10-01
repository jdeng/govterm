# govterm

Go bindings for libvterm, forked from https://github.com/mattn/go-libvterm and embedding the original C sources from https://www.leonerd.org.uk/code/libvterm for convenience.

## Overview

`govterm` wraps libvterm through cgo to provide terminal emulation capabilities from Go. The package exposes the pieces of libvterm that are currently needed by the project (terminal creation, screen access, keyboard helpers, colors, etc.).

## Embedded libvterm

- The libvterm source tree under `libvterm/` is synced from https://www.leonerd.org.uk/code/libvterm and is built directly by cgo via `#cgo CFLAGS: -I${SRCDIR}/libvterm/include -I${SRCDIR}/libvterm`.
- The C sources are embedded in this repository for convenience; you do **not** need a system-wide libvterm installation or `pkg-config` when using this module.
- When updating to a newer upstream release, refresh the `libvterm/` directory from the upstream source and keep the original `libvterm/LICENSE` alongside the Go bindings.

## Requirements

- Go 1.18 or newer
- A C toolchain for cgo (e.g. `gcc` or `clang`)

## Installation

```bash
go get github.com/jdeng/govterm
```

## Usage

```go
package main

import (
    "log"

    "github.com/jdeng/govterm"
)

func main() {
    vt := govterm.New(25, 80)
    defer vt.Close()

    vt.SetUTF8(true)

    screen := vt.ObtainScreen()
    screen.Reset(true)

    if _, err := vt.Write([]byte("\033[31mHello \033[32mGo\033[0m")); err != nil {
        log.Fatal(err)
    }
    screen.Flush()

    cell, err := screen.GetCellAt(0, 0)
    if err != nil {
        log.Fatal(err)
    }
    _ = cell
}
```

## Potential Enhancements

- Expose the remaining libvterm parser callbacks (control, escape, CSI, OSC, DCS) to Go.
- Provide higher-level helpers for diffing screen state and batching damaged regions.
- Add Go tests covering keyboard input, color conversions, and screen callbacks.
- Offer a streaming helper that wires libvterm to an `io.Reader`/`io.Writer` pair.

## License

The Go bindings are available under the MIT License. The vendored libvterm sources retain their upstream license (see `libvterm/LICENSE`).
