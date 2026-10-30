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
- **نظام التشغيل:** Linux
- **Rust:** لتجميع اللعبة
- **Java 25:** لاستخراج الموارد
- **Minecraft 26.3:** في مجلد `~/.minecraft`
- **مكتبات النظام:** `sudo apt install -y libasound2-dev libudev-dev mesa-vulkan-drivers vulkan-tools`

## طريقة التشغيل

### 1. بناء اللعبة
```bash
cargo build --release -p horizon-client --bin horizon-client
cargo build --release -p horizon-prepare --bin horizon-prepare
