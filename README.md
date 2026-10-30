# Horizon Rust Edition - Offline Crack

هذا المشروع هو نسخة مبنية من محرك **Horizon** (ماين كرافت بلغة Rust). 
الهدف: تشغيل اللعبة **بدون حساب مايكروسوفت** وبدون اللانشر الرسمي.

## كيف يعمل الكراك؟
الكراك يعتمد على بناء العميل مباشرة من الكود المصدري (`cargo build`). 
- يستخرج الموارد محلياً من نسخة ماين كرافت 26.3 الموجودة على جهازك عبر أداة `horizon-prepare`.
- يشغل اللعبة في وضع اللاعب الواحد (Singleplayer)، الذي يعمل كسيرفر داخلي محلي. 
- لا يقوم بأي اتصال بخوادم Mojang ولن يطلب أي توثيق.
- يستخدم `lavapipe` (Vulkan Software Renderer) ليعمل على كروت الشاشة القديمة التي لا تدعم Vulkan.

## المتطلبات
- **نظام التشغيل:** Linux (تم الاختبار على Linux Mint 22.3)
- **Rust:** لتجميع اللعبة
- **Java 25:** لاستخراج الموارد
- **Minecraft 26.3:** في مجلد `~/.minecraft`
- **مكتبات النظام:** `sudo apt install -y libasound2-dev libudev-dev mesa-vulkan-drivers vulkan-tools`

## طريقة التشغيل
1. بناء اللعبة:
\`\`\`bash
cargo build --release -p horizon-client --bin horizon-client
cargo build --release -p horizon-prepare --bin horizon-prepare
\`\`\`
2. استخراج الموارد (تحتاج ملف سيرفر 26.3):
\`\`\`bash
target/release/horizon-prepare --prepare --minecraft-dir ~/.minecraft --server-jar /path/to/server.jar --root .
\`\`\`
3. تشغيل اللعبة:
\`\`\`bash
export VK_ICD_FILENAMES=/usr/share/vulkan/icd.d/lvp_icd.x86_64.json
export HORIZON_HOME=$HOME/horizon-offline
target/release/horizon-client --resources .local/horizon-client/26.3 --world offline --seed 1 --render-distance 4
\`\`\`
