# matte-gui — desktop bulk processor for MATTE

[ภาษาไทยอยู่ด้านล่าง](#th)

- 💻 **Web app (interactive):** <https://matte.signthai.com>
- 📦 **Latest binaries:** [Releases](https://github.com/spinsoft-transcription/matte-gui-release/releases/latest)

---

## What is MATTE-gui?

**MATTE** — *Matting, AI-grade, Toning & Tracking Engine* — is a
chromakey + 3D-LUT + auto-frame compositor. It comes in two flavours
that share the same look pipeline:

| Tool | What it's for |
|---|---|
| **Web app** ([matte.signthai.com](https://matte.signthai.com)) | Tune one clip end-to-end: chromakey edges, AI grade (Gemini / OpenRouter / Replicate), fit a 3D LUT, preview the composite. Export `config.json` + `look.cube`. |
| **`matte-gui` (this app)** | Apply a previously-tuned look to **a folder of clips** in one shot. No tuning UI — it consumes the web app's exported `config.json` + `look.cube` and bulk-renders. |

### When to use which

- **Web app** — when you're still *deciding* the look. It's the only
  place where AI grading + LUT fitting live.
- **GUI** — when the look is locked and you need to render the same
  recipe across many clips. The bulk queue keeps going while you do
  something else.

The typical workflow: tune in the web app → **⚙ Export Config** +
save the `.cube` → open both in the GUI → point at an input folder
→ press *PROCESS ALL QUEUED*.

### Features

- Bulk-render a folder of `.mp4` / `.mov` / `.mkv` clips against one
  look (chromakey + 3D-LUT + grade).
- Per-clip auto-framing — zoom + shift are detected from a pose
  model so each subject lands centred without hand-tweaking.
- Composite onto a background image **or** render to `.webm` with a
  transparent alpha channel for downstream NLE work.
- Cancellable queue with live per-clip progress.
- Persistent settings — pick your `config.json` / `.cube` / input /
  output paths once, they're remembered next launch.

---

## Install

### macOS — Homebrew (recommended)

**Apple Silicon only** (M1 or newer, macOS 11 Big Sur+).

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

1. Install ffmpeg (either works):
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

Apache-2.0.

---

<a id="th"></a>

# matte-gui — โปรแกรม bulk processor สำหรับ MATTE

[English above](#matte-gui--desktop-bulk-processor-for-matte)

- 💻 **เวอร์ชันเว็บ (ใช้แบบ interactive):** <https://matte.signthai.com>
- 📦 **ดาวน์โหลด binary:** [Releases](https://github.com/spinsoft-transcription/matte-gui-release/releases/latest)

---

## MATTE-gui คืออะไร?

**MATTE** ย่อมาจาก *Matting, AI-grade, Toning & Tracking Engine*
เป็นโปรแกรม chromakey + 3D-LUT + auto-frame compositor มี 2 แบบ
ที่ใช้ pipeline เดียวกัน:

| เครื่องมือ | ใช้ตอนไหน |
|---|---|
| **เวอร์ชันเว็บ** ([matte.signthai.com](https://matte.signthai.com)) | ปรับ 1 คลิปครบทุกขั้นตอน: เลือก key สี, AI grade (Gemini / OpenRouter / Replicate), fit 3D LUT, preview composite แล้ว export `config.json` + `look.cube` |
| **`matte-gui` (โปรแกรมนี้)** | นำ look ที่ปรับไว้แล้วไปใช้กับ **คลิปทั้งโฟลเดอร์** ทีเดียว ไม่มี UI ปรับค่า — ใช้ `config.json` + `look.cube` ที่ export มาจากเว็บแล้ว bulk render |

### ใช้ตัวไหนตอนไหน

- **เว็บ** — เมื่อยังกำลัง *ปรับ look อยู่* เป็นที่เดียวที่ AI grade
  + LUT fit ทำงานได้
- **GUI** — เมื่อ look ลงตัวแล้ว ต้องการ render สูตรเดียวกันลงคลิป
  หลาย ๆ ตัว คิวงาน bulk จะรันต่อไปเรื่อย ๆ ระหว่างที่คุณไปทำ
  อย่างอื่นได้

Workflow ที่ใช้กันคือ: ปรับใน web app → กด **⚙ Export Config**
+ save `.cube` → เปิดทั้ง 2 ไฟล์ใน GUI → ชี้ไปที่โฟลเดอร์ input
→ กด *PROCESS ALL QUEUED*

### Features

- Bulk render คลิป `.mp4` / `.mov` / `.mkv` ทั้งโฟลเดอร์ด้วย look
  เดียว (chromakey + 3D-LUT + grade)
- Auto-framing แต่ละคลิป — ตรวจจับ zoom + shift จากโมเดล pose
  เพื่อให้ subject อยู่กลางเฟรมโดยไม่ต้องตั้งเอง
- Composite ลงรูป background หรือ render เป็น `.webm` ที่มี alpha
  channel โปร่งใส (เอาไปประกอบใน NLE ต่อได้)
- คิวงานยกเลิกได้ มี progress แบบ real-time ต่อคลิป
- จำ settings ไว้ — เลือกไฟล์ `config.json` / `.cube` / input /
  output ครั้งเดียว จะใช้ต่อในครั้งถัดไป

---

## วิธีติดตั้ง

### macOS — ติดตั้งผ่าน Homebrew (แนะนำ)

**รองรับเฉพาะ Apple Silicon** (M1 ขึ้นไป, macOS 11 Big Sur ขึ้นไป)

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

Apache-2.0
