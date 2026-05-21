# matte-gui — desktop bulk processor for MATTE

[ภาษาไทยอยู่ด้านล่าง](#th)

Public release artifacts for
[`spinsoft-transcription/matte`](https://github.com/spinsoft-transcription/matte)
(the source repo is private — this sibling repo exists only to host
downloadable binaries so `brew install` + casual Windows downloads
keep working).

- 💻 **Web app (interactive):** <https://matte.signthai.com>
- 📦 **Latest binaries:** [Releases](https://github.com/spinsoft-transcription/matte-gui-release/releases/latest)

---

## What is MATTE-gui?

**MATTE** — *Matting, AI-grade, Toning & Tracking Engine* — is a
chromakey + 3D-LUT + auto-frame compositor. It ships in two flavours:

| Tool | What it's for | How it runs |
|---|---|---|
| **Web app** ([matte.signthai.com](https://matte.signthai.com)) | Tune one clip end-to-end: chromakey edges, AI grade (Gemini / OpenRouter / Replicate), fit a 3D LUT, preview the composite. Export `config.json` + `look.cube`. | Entirely in the browser — `ffmpeg.wasm` + `onnxruntime-web` + a pure-Rust wasm-bindgen bundle. No server. |
| **`matte-gui` (this repo)** | Apply a previously-tuned look to **a folder of clips** in one shot. No UI for tuning — it consumes the web app's exported `config.json` + `look.cube` and bulk-renders. | Native iced desktop app. Shells out to system `ffmpeg` / `ffprobe`. Uses `tract-onnx` (no GPU required) for per-clip auto-framing. |

### When to use the GUI vs. the web app

- **Use the web app** when you're still *deciding* the look — keying
  thresholds, AI grade prompt, LUT fit, framing. It's the only place
  where AI grading + LUT fitting live.
- **Use the GUI** when the look is locked and you need to render the
  same recipe across many clips. Native ffmpeg is ~3-5× faster than
  the wasm path, and the bulk queue keeps going while you do
  something else.

The typical workflow is: tune on the web app → **⚙ Export Config**
+ save the `.cube` → open both in the GUI → point at an input folder
→ press *PROCESS ALL QUEUED*.

### Why Rust?

The pipeline was originally a Python notebook stack (`numpy`,
`opencv`, `ffmpeg-python`). The math + filter-graph builders +
LUT fitter were ported to Rust as `crates/core`, and `crates/core`
is the **single source of truth** shared by both flavours:

```
crates/core   (pure Rust — params, filter graphs, LUT fit, framing solver)
├── crates/ffio_web      → wasm-bindgen bundle for the web app
└── native/crates/core_native → orchestration for matte-gui
    └── matte-gui (this binary, iced UI)
```

Byte-equality between the Python notebooks and the Rust core is
guarded by tests in `crates/core/tests/python_parity.rs`.

---

## Install

### macOS — Homebrew (recommended)

**Apple Silicon only** (M1 or newer, macOS 11 Big Sur+). Intel Macs
need to build from source — see the [project README](https://github.com/spinsoft-transcription/matte#build-from-source).

```sh
brew install spinsoft-transcription/matte/matte-gui
```

This drops `matte-gui.app` into `/Applications` (Launchpad +
Spotlight find it) and pulls in `ffmpeg` automatically.

Launch from Launchpad / Spotlight, or:

```sh
open -a matte-gui
```

Upgrade later:

```sh
brew update && brew upgrade --cask matte-gui
```

Uninstall — `brew uninstall --cask matte-gui` keeps your settings;
add `--zap` to also wipe
`~/Library/Application Support/com.spinsoft.MATTE/`.

> ⚠️ **Unsigned binary.** Current v0.1.x releases ship without an
> Apple Developer ID. The Cask auto-strips the quarantine attribute
> in a postflight hook, so first launch should "just work" on most
> macOS versions. If Gatekeeper still flags it, use **System
> Settings → Privacy & Security → Open Anyway**.

### Windows — portable zip

1. Install ffmpeg (any of these works):
   ```powershell
   winget install --id Gyan.FFmpeg
   ```
   ```powershell
   choco install ffmpeg
   ```
2. Download the latest
   [`matte-gui-vX.Y.Z-windows-x86_64.zip`](https://github.com/spinsoft-transcription/matte-gui-release/releases/latest).
3. Extract anywhere (e.g. `%LOCALAPPDATA%\matte-gui\`).
4. Double-click `matte-gui.exe`.

> ⚠️ **Unsigned binary.** Windows SmartScreen will show
> *"Windows protected your PC"* the first time. Click
> **More info → Run anyway**.

### Linux

No prebuilt binary yet — build from source. See the
[project README](https://github.com/spinsoft-transcription/matte#build-from-source).

---

## Use

1. Open `matte-gui`.
2. **Pick a `config.json`** (exported from the web app's *⚙ Export
   Config* button).
3. **Pick a `look.cube`** (exported from the web app's *Look LUT*
   tab).
4. **Pick an input directory** of `.mp4` / `.mov` / `.mkv` clips.
5. **Pick an output directory** — a mirrored sub-tree will be
   created.
6. *(Optional)* Pick a **background image**. If you skip this, MATTE
   renders to `.webm` with a transparent alpha channel (great for
   downstream compositing in Premiere / Resolve / FCP).
7. Click **PROCESS ALL QUEUED**.

Settings persist across launches:

- **macOS:** `~/Library/Application Support/com.spinsoft.MATTE/settings.json`
- **Windows:** `%APPDATA%\spinsoft\MATTE\settings.json`

---

## Verify a download

Every asset is paired with a `.sha256` file. To check:

```sh
# macOS
shasum -a 256 -c matte-gui-vX.Y.Z-macos-arm64.tar.gz.sha256
```

```powershell
# Windows (PowerShell)
Get-FileHash matte-gui-vX.Y.Z-windows-x86_64.zip -Algorithm SHA256
# Compare to the contents of matte-gui-vX.Y.Z-windows-x86_64.zip.sha256
```

---

## License

Apache-2.0 — see the source repo for the full text.

---

<a id="th"></a>

# matte-gui — โปรแกรม bulk processor สำหรับ MATTE

[English above](#matte-gui--desktop-bulk-processor-for-matte)

Repo นี้เก็บไฟล์ binary ของ release ของโปรเจ็กต์
[`spinsoft-transcription/matte`](https://github.com/spinsoft-transcription/matte)
(source code อยู่ใน repo ที่เป็น private — repo นี้มีไว้เพื่อให้
`brew install` + การดาวน์โหลดบน Windows ใช้งานได้ปกติเท่านั้น)

- 💻 **เวอร์ชันเว็บ (ใช้แบบ interactive):** <https://matte.signthai.com>
- 📦 **ดาวน์โหลด binary:** [Releases](https://github.com/spinsoft-transcription/matte-gui-release/releases/latest)

---

## MATTE-gui คืออะไร?

**MATTE** ย่อมาจาก *Matting, AI-grade, Toning & Tracking Engine*
เป็นโปรแกรม chromakey + 3D-LUT + auto-frame compositor มี 2 แบบ:

| เครื่องมือ | ใช้ตอนไหน | ทำงานอย่างไร |
|---|---|---|
| **เวอร์ชันเว็บ** ([matte.signthai.com](https://matte.signthai.com)) | ปรับ 1 คลิปครบทุกขั้นตอน: เลือก key สี, AI grade (Gemini / OpenRouter / Replicate), fit 3D LUT, preview composite แล้ว export `config.json` + `look.cube` | รันใน browser ทั้งหมด ใช้ `ffmpeg.wasm` + `onnxruntime-web` + Rust wasm-bindgen ไม่ต้องมี server |
| **`matte-gui` (repo นี้)** | นำ look ที่ปรับไว้แล้วไปใช้กับ **คลิปทั้งโฟลเดอร์** ทีเดียว ไม่มี UI ปรับค่า — ใช้ `config.json` + `look.cube` ที่ export มาจากเว็บแล้ว bulk render | เป็น native iced desktop app เรียก `ffmpeg` / `ffprobe` ของระบบ + ใช้ `tract-onnx` (ไม่ต้องมี GPU) สำหรับ auto-framing แต่ละคลิป |

### ใช้ GUI หรือใช้เว็บดี?

- **ใช้เว็บ** เมื่อกำลัง *ปรับ look อยู่* — ตั้งค่า keying, prompt
  ของ AI grade, fit LUT, จัด framing เวอร์ชันเว็บเป็นที่เดียวที่
  AI grade + LUT fit ทำงานได้
- **ใช้ GUI** เมื่อ look ลงตัวแล้ว และต้องการ render สูตรเดียวกันลง
  คลิปหลาย ๆ ตัว `ffmpeg` แบบ native เร็วกว่า wasm ประมาณ 3-5 เท่า
  และคิวงาน bulk จะรันต่อไปเรื่อย ๆ ในระหว่างที่คุณไปทำอย่างอื่นได้

Workflow ที่ใช้กันคือ: ปรับใน web app → กด **⚙ Export Config**
+ save `.cube` → เปิดทั้ง 2 ไฟล์ใน GUI → ชี้ไปที่โฟลเดอร์ input
→ กด *PROCESS ALL QUEUED*

### ทำไมเขียนด้วย Rust?

Pipeline ตัวนี้แรกสุดเป็น Python notebook (`numpy`, `opencv`,
`ffmpeg-python`) ภายหลังได้ port ส่วนคำนวณ + filter-graph
builders + LUT fitter ไปเป็น Rust ในชื่อ `crates/core`
และ `crates/core` คือ **source of truth ตัวเดียว** ที่ใช้ร่วมกัน
ทั้ง 2 เวอร์ชัน:

```
crates/core   (Rust ล้วน — params, filter graphs, LUT fit, framing solver)
├── crates/ffio_web      → wasm-bindgen bundle สำหรับ web app
└── native/crates/core_native → orchestration สำหรับ matte-gui
    └── matte-gui (binary ตัวนี้ ใช้ iced UI)
```

Output ของ Python notebooks กับ Rust core เท่ากันทุก byte
และถูกตรวจสอบไว้ใน `crates/core/tests/python_parity.rs`

---

## วิธีติดตั้ง

### macOS — ติดตั้งผ่าน Homebrew (แนะนำ)

**รองรับเฉพาะ Apple Silicon** (M1 ขึ้นไป, macOS 11 Big Sur ขึ้นไป)
ถ้าใช้ Mac แบบ Intel ต้อง build เองจาก source —
ดู [README ของโปรเจ็กต์หลัก](https://github.com/spinsoft-transcription/matte#build-from-source)

```sh
brew install spinsoft-transcription/matte/matte-gui
```

คำสั่งนี้จะติดตั้ง `matte-gui.app` ไว้ใน `/Applications`
(หาเจอใน Launchpad + Spotlight) และ install `ffmpeg` ให้
โดยอัตโนมัติ

เปิดผ่าน Launchpad / Spotlight หรือสั่งจาก terminal:

```sh
open -a matte-gui
```

อัปเดตเวอร์ชันใหม่:

```sh
brew update && brew upgrade --cask matte-gui
```

ถอนการติดตั้ง — `brew uninstall --cask matte-gui`
จะเก็บ settings ไว้ ถ้าต้องการลบ settings ด้วยให้เพิ่ม `--zap`
จะลบ `~/Library/Application Support/com.spinsoft.MATTE/` ด้วย

> ⚠️ **Binary ไม่ได้ sign.** Release v0.1.x ยังไม่มี Apple
> Developer ID Cask จะ strip quarantine ออกให้อัตโนมัติ ดังนั้น
> เปิดครั้งแรกก็ใช้ได้เลยใน macOS ส่วนใหญ่ ถ้า Gatekeeper ยัง
> blocking ให้ไปที่ **System Settings → Privacy & Security →
> Open Anyway**

### Windows — ดาวน์โหลด zip ใช้ได้เลย

1. ติดตั้ง ffmpeg ก่อน (เลือกอันใดอันหนึ่ง):
   ```powershell
   winget install --id Gyan.FFmpeg
   ```
   ```powershell
   choco install ffmpeg
   ```
2. ดาวน์โหลดไฟล์
   [`matte-gui-vX.Y.Z-windows-x86_64.zip`](https://github.com/spinsoft-transcription/matte-gui-release/releases/latest)
   เวอร์ชันล่าสุด
3. แตก zip ไปที่ไหนก็ได้ (เช่น `%LOCALAPPDATA%\matte-gui\`)
4. double-click `matte-gui.exe`

> ⚠️ **Binary ไม่ได้ sign.** SmartScreen จะขึ้นข้อความ
> *"Windows protected your PC"* ในการเปิดครั้งแรก ให้กด
> **More info → Run anyway**

### Linux

ยังไม่มี binary สำเร็จรูป — ต้อง build เองจาก source
ดู [README ของโปรเจ็กต์หลัก](https://github.com/spinsoft-transcription/matte#build-from-source)

---

## วิธีใช้งาน

1. เปิด `matte-gui`
2. **เลือก `config.json`** ที่ export มาจาก web app
   (ปุ่ม *⚙ Export Config*)
3. **เลือก `look.cube`** ที่ export มาจากแท็บ *Look LUT*
   ใน web app
4. **เลือกโฟลเดอร์ input** ที่มีคลิป `.mp4` / `.mov` / `.mkv`
5. **เลือกโฟลเดอร์ output** — โครงสร้างโฟลเดอร์ของ input
   จะถูก mirror ออกมาให้
6. *(ไม่บังคับ)* เลือก **ภาพ background** ถ้าไม่เลือก จะ render
   ออกมาเป็น `.webm` ที่มี alpha channel โปร่งใส
   (เอาไปประกอบใน Premiere / Resolve / FCP ต่อได้)
7. กด **PROCESS ALL QUEUED**

Settings จะถูก save ไว้ที่:

- **macOS:** `~/Library/Application Support/com.spinsoft.MATTE/settings.json`
- **Windows:** `%APPDATA%\spinsoft\MATTE\settings.json`

---

## ตรวจสอบไฟล์ที่ดาวน์โหลด

ทุก asset จะมีไฟล์ `.sha256` คู่กัน ตรวจสอบได้ดังนี้:

```sh
# macOS
shasum -a 256 -c matte-gui-vX.Y.Z-macos-arm64.tar.gz.sha256
```

```powershell
# Windows (PowerShell)
Get-FileHash matte-gui-vX.Y.Z-windows-x86_64.zip -Algorithm SHA256
# เทียบกับเนื้อหาในไฟล์ matte-gui-vX.Y.Z-windows-x86_64.zip.sha256
```

---

## License

Apache-2.0 — ดูข้อความ license ฉบับเต็มได้ที่ repo หลัก
