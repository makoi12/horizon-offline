# Horizon Rust Edition - Offline Crack

هذا المشروع هو نسخة مبنية من محرك **Horizon** (ماين كرافت بلغة Rust). 
الهدف: تشغيل اللعبة **بدون حساب مايكروسوفت** وبدون اللانشر الرسمي.

## كيف يعمل الكراك؟
الكراك يعتمد على بناء العميل مباشرة من الكود المصدري (`cargo build`). 
- يستخرج الموارد محلياً من نسخة ماين كرافت 26.3 الموجودة على جهازك عبر أداة `horizon-prepare`.
- يشغل اللعبة في وضع اللاعب الواحد (Singleplayer)، الذي يعمل كسيرفر داخلي محلي.
- لا يقوم بأي اتصال بخوادم Mojang ولن يطلب أي توثيق.
- يستخدم `lavapipe` (Vulkan Software Renderer) ليعمل على كروت الشاشة القديمة التي لا تدعم Vulkan.

---

## 1. المتطلبات وتنزيل الأدوات (Prerequisites)

قبل أن تبدأ، يجب أن تتأكد من وجود هذه الأدوات في نظامك (تم الاختبار على Linux Mint / Ubuntu):

### أ. تثبيت Rust (لغة البناء)
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source "$HOME/.cargo/env"
```

### ب. تثبيت Java 25 (لاستخراج الموارد)
```bash
sudo apt update
sudo apt install -y openjdk-25-jdk
```

### ج. تثبيت مكتبات النظام (للصوت، الأيدي، والريندرر)
```bash
sudo apt install -y build-essential pkg-config libasound2-dev libudev-dev mesa-vulkan-drivers vulkan-tools
```

### د. تنزيل ملف سيرفر ماين كرافت 26.3
المشروع لا يقوم بتحميل اللعبة تلقائياً. يجب أن تمتلك نسخة أصلية في `~/.minecraft`. لتنزيل ملف السيرفر المطلوب، استخدم الأمر التالي:
```bash
curl -L -o /tmp/server-26.3.jar "https://piston-data.mojang.com/v1/objects/33680f5f2ac32864d6d7cf5e56a705fdb3e05f4c/server.jar"
```

---

## 2. طريقة التحميل والبناء

```bash
git clone https://github.com/makoi12/horizon-offline.git
cd horizon-offline

# بناء اللعبة
cargo build --release -p horizon-client --bin horizon-client

# بناء أداة استخراج الموارد
cargo build --release -p horizon-prepare --bin horizon-prepare
```

---

## 3. استخراج الموارد (Resources)
تحتاج إلى ملف السيرفر الذي قمنا بتنزيله في الخطوة (د):

```bash
target/release/horizon-prepare --prepare --minecraft-dir ~/.minecraft --server-jar /tmp/server-26.3.jar --root .
```

---

## 4. تشغيل اللعبة

### الطريقة الأولى: أمر مباشر وسريع
انسخ هذا السطر كاملاً في التيرمنال:

```bash
cd ~/gerustfree/horizon-main && VK_ICD_FILENAMES=/usr/share/vulkan/icd.d/lvp_icd.x86_64.json HORIZON_HOME=$HOME/horizon-offline HORIZON_TELEMETRY_URL=off target/release/horizon-client --resources .local/horizon-client/26.3 --world offline --seed 1 --render-distance 4
```

### الطريقة الثانية: سكربت بايثون (مريح للتشغيل الدائم)
احفظ هذا الكود في ملف باسم `play.py` ثم شغله بـ `python3 play.py`:

```python
import os
import sys
import subprocess
from pathlib import Path

REPO_DIR = Path.home() / "gerustfree" / "horizon-main"
CLIENT_BIN = REPO_DIR / "target" / "release" / "horizon-client"
RESOURCES = REPO_DIR / ".local" / "horizon-client" / "26.3"
GAME_HOME = Path.home() / "horizon-offline"
VULKAN_ICD = "/usr/share/vulkan/icd.d/lvp_icd.x86_64.json"

if not CLIENT_BIN.is_file() or not RESOURCES.is_dir():
    print("[x] Error: Client or Resources missing. Read the README.")
    sys.exit(1)

GAME_HOME.mkdir(parents=True, exist_ok=True)
env = os.environ.copy()
env["HORIZON_HOME"] = str(GAME_HOME)
env["HORIZON_TELEMETRY_URL"] = "off"
if Path(VULKAN_ICD).exists():
    env["VK_ICD_FILENAMES"] = VULKAN_ICD

cmd = [
    str(CLIENT_BIN),
    "--resources", str(RESOURCES),
    "--world", "offline",
    "--seed", "1",
    "--render-distance", "4"
]

print("[*] Starting Horizon Rust Edition...")
subprocess.run(cmd, cwd=GAME_HOME, env=env)
```

---

## 5. حل المشاكل (Troubleshooting)

- **مشكلة: خطأ في `alsa-sys` أو `libudev` أثناء البناء**
  تأكد من تثبيت المكتبات في الخطوة (ج). إذا استمرت المشكلة، يمكنك البناء بدون صوت بـ `--no-default-features`.
  
- **مشكلة: خطأ `no suitable adapter found` أو `wgpu`**
  كرت الشاشة لا يدعم Vulkan. تأكد من تثبيت `mesa-vulkan-drivers` وأنك قمت بتصدير مسار `VK_ICD_FILENAMES` كما في أوامر التشغيل.

- **مشكلة: شاشة سوداء أو FPS منخفض جداً**
  هذا طبيعي على الأجهزة القديمة. قم بتقليل `--render-distance` إلى `2` أو `4`. 
