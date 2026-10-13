# APK Build + GitHub Release Workflow — AgentMobile

**نسخه‌ی سوم:** علاوه بر ساخت APK و انتشار GitHub Release، توضیحات خودکار Release حالا شامل **بنر تصویری** و **خلاصه‌ی فارسی** هم هست.

## ۱) تشخیص پروژه
- **نوع پروژه:** اندروید بومی (Native Android) — Kotlin + Jetpack Compose
- **هسته‌ی Rust:** ساب‌ماژول `zeroclaw` + ماژول `:lib` که از طریق uniffi/gobley به کریت Rust (`agentmobile/zeroclaw-ffi`) وصل می‌شود.
- **شاخه‌ی پیش‌فرض:** `master`
- **ماژول تولید APK:** `:app` → `app/build/outputs/apk/{debug,release}/`
- **نسخه‌ی اپ:** `versionName = 0.0.37` و `versionCode = 39`

| مورد | مقدار | منبع |
| --- | --- | --- |
| JDK | 17 (temurin) | `app/build.gradle.kts` |
| Android SDK | API 35 (minSdk 28، targetSdk 35) | `app/build.gradle.kts` |
| Android NDK | `27.2.12479018` (r27c) | `ci.yml` |
| Rust | stable + `aarch64-linux-android`, `x86_64-linux-android` | README + `ci.yml` |
| Gradle | Wrapper `8.10.2` (AGP 8.8.1، Kotlin 2.0.20) | `gradle-wrapper.properties` |

## ۲) تریگرها و رفتار دو job

| رویداد | job `build-apk` (artifact) | job `release` (GitHub Release) |
| --- | --- | --- |
| `push` روی `main`/`master` | ✅ اجرا | ❌ اجرا نمی‌شود (فقط artifact) |
| `push` tag با الگوی `v*` (مثل `v0.0.37`) | ✅ اجرا | ✅ اجرا با نام همان tag |
| `workflow_dispatch` بدون پرکردن `release_tag` | ✅ اجرا | ❌ اجرا نمی‌شود |
| `workflow_dispatch` با `release_tag` پر | ✅ اجرا | ✅ اجرا با همان tag |

- **job دوم** با `needs: build-apk` بعد از پایان موفق بیلد اجرا می‌شود.
- از `softprops/action-gh-release@v2` استفاده می‌کند با `permissions: contents: write`.
- نام assetها: `AgentMobile-<version>-debug.apk` و `AgentMobile-<version>-release.apk` (مثلاً `AgentMobile-0.0.37-debug.apk`).
- **اجرای مجدد برای همان tag** خطا نمی‌دهد: به لطف `overwrite_files: true` فایل‌های هم‌نام جایگزین می‌شوند و Release موجود به‌روزرسانی می‌شود. (`fail_on_unmatched_files: true` هم تضمین می‌کند اگر APK پیدا نشد، job با خطا متوقف شود نه اینکه Release خالی بسازد.)

## ۳) ساختار توضیحات (body) خودکار Release

ترتیب دقیق بدنه‌ی Release که در مرحله‌ی `Prepare release assets and notes` ساخته می‌شود:

1. **بنر تصویری** (تصویر پروژه AgentMobile)
2. **خلاصه‌ی فارسی** — شامل: نام و نسخه‌ی اپ، نوع امضا (release/debug)، فهرست فایل‌های APK ضمیمه‌شده، **لینک دانلود مستقیم هر asset**، و در صورت امضا با کلید debug یک **هشدار فارسی** با نام چهار Secret لازم.
3. **جدول اطلاعات بیلد** (Build info) — نسخه، نوع امضا، commit، trigger
4. **لیست assetها** (APK assets)
5. **changelog ساده** از commitها نسبت به tag `v*` قبلی (اگر tag قبلی نبود، ۲۰ commit آخر)

### بنر از کجا می‌آید؟
مرحله‌ی ساخت توضیحات، تصویر بنر را به این ترتیب انتخاب می‌کند (اولی که موجود باشد برنده است):

| اولویت | منبع | نحوه‌ی تنظیم |
| --- | --- | --- |
| ۱ | Repository **Variable** با نام `RELEASE_BANNER_URL` | `Settings → Secrets and variables → Actions → Variables` |
| ۲ | فایل کامیت‌شده‌ی `.github/assets/release-banner.png` در ریپو | فایل بنر ضمیمه را در همین مسیر بگذار و push کن |
| ۳ | URL پایدار fallback | همین حالا کار می‌کند (تصویر میزبانی‌شده) |

> برای اینکه تصویر داخل خود Release رندر شود، در حالت ۲ آدرس به‌صورت **URL مطلق raw** ساخته می‌شود:
> `${GITHUB_SERVER_URL}/${GITHUB_REPOSITORY}/raw/${GITHUB_SHA}/.github/assets/release-banner.png`
> (مسیر نسبی خالی در صفحه‌ی Release به مخزن resolve می‌شد و تصویر نمایش داده نمی‌شد.)

**گذاشتن بنر در ریپو (پیشنهادی):**
```bash
mkdir -p .github/assets
cp release-banner.png .github/assets/release-banner.png
git add .github/assets/release-banner.png
git commit -m "ci: add release banner asset"
git push origin master
```
فایل `release-banner.png` در پوشه‌ی همین نسخه همراه است و می‌توانی همان را استفاده کنی.

## ۴) نحوه‌ی نصب و اجرا

```bash
# در ریشه‌ی ریپوی AgentMobile
mkdir -p .github/workflows .github/assets
cp build-apk.yml .github/workflows/build-apk.yml
cp release-banner.png .github/assets/release-banner.png
git add .github/workflows/build-apk.yml .github/assets/release-banner.png
git commit -m "ci: add APK build + release workflow"
git push origin master
```

## ۵) چطور tag بزنی و Release را ببینی

```bash
# 1) tag را روی commit فعلی بساز و push کن
git tag v0.0.37
git push origin v0.0.37

# یا اگر نسخه‌ی اپ را جلو بردی، اول versionName/versionCode را در app/build.gradle.kts به‌روز کن
# و بعد tag جدید را بزن (مثلاً v0.0.38)
```

بعد از push:
1. به تب **Actions** برو؛ workflow با نام **Build APK** اجرا می‌شود.
2. وقتی job `Publish GitHub Release` سبز شد، به تب **Releases** برو.
3. Release با نام `v0.0.37` ظاهر می‌شود؛ **بنر و خلاصه‌ی فارسی** بالای صفحه دیده می‌شوند و APKها در بخش **Assets** قابل دانلود هستند (لینک مستقیم هم دارند، مثل `.../releases/download/v0.0.37/AgentMobile-0.0.37-release.apk`).

### اجرای دستی با tag دلخواه
از **Actions → Build APK → Run workflow**، فیلد اختیاری `release_tag` را پر کن (مثلاً `v0.0.37`). اگر خالی بگذاری فقط artifact ساخته می‌شود و Release‌ای نمی‌سازد.

## ۶) امضای واقعی release (اختیاری)
`app/build.gradle.kts` فقط وقتی امضای واقعی را فعال می‌کند که این کلیدها در `local.properties` باشند. برای امضای واقعی، این ۴ Secret را در `Settings → Secrets and variables → Actions` بساز:

| Secret | مقدار |
| --- | --- |
| `RELEASE_KEYSTORE_BASE64` | خروجی `base64 -w0 release.jks` |
| `RELEASE_STORE_PASSWORD` | رمز keystore |
| `RELEASE_KEY_ALIAS` | نام alias کلید |
| `RELEASE_KEY_PASSWORD` | رمز کلید |

```bash
keytool -genkeypair -v -keystore release.jks -keyalg RSA -keysize 2048 -validity 10000 -alias mykey
base64 -w0 release.jks > keystore.base64.txt   # محتوایش را در RELEASE_KEYSTORE_BASE64 بگذار
```

> تا وقتی Secrets نباشند، APK ریلیز با **کلید debug** امضا می‌شود و این هشدار **به‌صورت فارسی** در توضیحات Release و در Job Summary درج می‌شود.
> بعد از افزودن Secrets، دوباره همان tag را push کن (یا workflow را دستی با همان tag اجرا کن)؛ چون `overwrite_files: true` است، assetها روی همان Release به‌روزرسانی می‌شوند و خطای «tag already exists» رخ نمی‌دهد.

## ۷) اعتبارسنجی
این فایل با `actionlint` (نسخه ۱.۷.۱۲) بررسی شده و **بدون خطا** پاس شده است. علاوه بر آن، مرحله‌ی `Prepare release assets and notes` در یک مخزن گیت آزمایشی شبیه‌سازی و در چهار حالت تست شده است:
- بنر کامیت‌شده → URL مطلق raw ✅
- بدون بنر و بدون متغیر → URL پایدار fallback ✅
- متغیر `RELEASE_BANNER_URL` ست‌شده → همان URL ✅
- عدم وجود APK → خطای واضح و exit 1 ✅

خروجی بدنه‌ی Release هم از نظر **UTF-8 بودن متن فارسی** بررسی شد (۳۰۷ کاراکتر فارسی، بدون مشکل encoding).
