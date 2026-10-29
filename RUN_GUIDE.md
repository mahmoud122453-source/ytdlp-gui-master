# تشغيل مشروع YT-DLP GUI

دليل تشغيل **YT-DLP GUI** من المصدر على Windows. الأوامر هنا مطابقة لما تم التحقق منه على هذا الجهاز.

هذا المجلد: `download_from-youtube` / `ytdlp-gui-master`.  
المشروع الأصلي: [aliencaocao/ytdlp-gui](https://github.com/aliencaocao/ytdlp-gui)  
نقطة الدخول الفعلية: **`yt-dlp-gui.py`**. لا يوجد `main.py` في المستودع (README على GitHub ما زال يذكر `main.py` خطأً في فقرة البناء).  
المحرك يدعم مصادر yt-dlp كلها، وليس YouTube فقط.

---

## 1. متطلبات التشغيل

- Windows 10 أو أحدث (تم التحقق على Windows 10).
- Python **3.10 حتى 3.14** (وثّق المشروع ذلك في README؛ تم التشغيل هنا على **Python 3.12.0**).
- Tkinter (يأتي عادةً مع مثبت Python الرسمي لـ Windows).
- حزم Python من `requirements.txt`.
- **FFmpeg و FFprobe كملفات تنفيذية** (`ffmpeg.exe` و `ffprobe.exe`) بجانب السكربت، أو على PATH.
- اتصال إنترنت لاستخراج البيانات والتنزيل.
- `deno.exe` اختياري: المشروع يحاول إيجاده ويكتفي بتحذير إن غاب.

FFmpeg **ليس** حزمة `pip`. لا تنفّذ `pip install ffmpeg`.

---

## 2. إصدار Python

تحقق:

```powershell
python --version
```

النتيجة التي تم التحقق منها في هذا العمل:

```text
Python 3.12.0
```

README الأصلي يطلب Python 3.10–3.14. سير عمل GitHub يستخدم 3.14 للبناء، وهذا لا يمنع التشغيل على 3.12.

---

## 3. إنشاء virtual environment

من مجلد المشروع:

```powershell
python -m venv .venv
```

هذا ينشئ مجلد `.venv` محلياً ولا يُرفع إلى Git (مضاف إلى `.gitignore`).

---

## 4. تفعيل البيئة على Windows

PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
```

إذا منع Execution Policy التفعيل:

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\.venv\Scripts\Activate.ps1
```

بدون تفعيل يمكنك استدعاء المفسّر مباشرة (هذا الأمر الذي تم التحقق منه):

```powershell
.\.venv\Scripts\python.exe yt-dlp-gui.py
```

---

## 5. تثبيت dependencies

بعد تفعيل البيئة، أو باستخدام مفسّر البيئة:

```powershell
.\.venv\Scripts\python.exe -m pip install --upgrade pip
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
```

محتوى `requirements.txt` الفعلي:

- `yt_dlp[default,curl-cffi] >= 2025.11.12` — محرك التنزيل (تشغيلي).
- `sanitize_filename` — تنظيف أسماء الملفات (تشغيلي).
- `pyinstaller` — بناء exe (تطوير/حزم، غير لازم لمجرد التشغيل).
- `pyinstaller-versionfile` — توليد `version.txt` للبناء (تطوير/حزم).

تم تثبيت هذه القائمة بنجاح، وثبت `yt-dlp` بالإصدار **2026.08.19**.

---

## 6. التأكد من Tkinter

```powershell
.\.venv\Scripts\python.exe -c "import tkinter; print(tkinter.TkVersion)"
```

النتيجة التي تم التحقق منها: `8.6`.

إذا فشل الاستيراد: أعد تثبيت Python من python.org مع تفعيل **tcl/tk**. Tkinter ليست حزمة `pip` في هذا المشروع.

---

## 7. التأكد من yt-dlp

```powershell
.\.venv\Scripts\python.exe -c "import yt_dlp; print(yt_dlp.version.__version__)"
```

النتيجة التي تم التحقق منها: `2026.08.19`.

---

## 8. التأكد من FFmpeg

المشروع يبحث أولاً عن `ffmpeg.exe` **في نفس مجلد** `yt-dlp-gui.py` عبر الدالة `get_res_path` ثم `resolve_ffmpeg_location`. إن لم يجده، يمرّر الاسم `ffmpeg.exe` لـ yt-dlp ليبحث في PATH.

لماذا FFmpeg مطلوب؟

- YouTube يقدّم غالباً فيديو وصوتاً كمسارين منفصلين.
- زر **Download Video** يطلب أفضل جودة ثم يدمج المسارين.
- تحويل الصوت في نافذة **Customize Downloads** يستخدم معالج `FFmpegExtractAudio`.

بدون FFmpeg: الواجهة تفتح، وتنزيل الصوت فقط أو فيديو بدون دمج قد ينجح، أما الدمج/التحويل فيفشل برسالة من yt-dlp.

ضع الملفين في مجلد المشروع:

- `ffmpeg.exe`
- `ffprobe.exe`

المصدر الذي يذكره README الأصلي:

[yt-dlp FFmpeg Builds — `ffmpeg-master-latest-win64-gpl.zip`](https://github.com/yt-dlp/FFmpeg-Builds/releases/tag/latest)

بعد النسخ:

```powershell
.\ffmpeg.exe -version
.\ffprobe.exe -version
```

تم التحقق هنا من:

```text
ffmpeg version N-126416-g9997fd0606-20260905
ffprobe version N-126416-g9997fd0606-20260905
```

الملفات `*.exe` مستثناة في `.gitignore`؛ لا ترفعها إلى Git.

---

## 9. التأكد من PATH

اختياري إذا كانت الثنائيات بجانب السكربت:

```powershell
where.exe python
where.exe ffmpeg
where.exe ffprobe
```

في بيئة الاختبار: Python موجود، و`ffmpeg`/`ffprobe` لم يكونا على PATH حتى نُسخا إلى مجلد المشروع. ذلك كافٍ لأن الكود يفضّل الملف المحلي.

`deno.exe` غير موجود في النسخة المستنسخة؛ ظهر التحذير `Deno not found!` والتنزيل من YouTube نجح بدونه في الاختبار.

---

## 10. طريقة تشغيل المشروع

الأمر الذي تم التحقق منه (يفتح النافذة حتى تغلقها):

```powershell
.\.venv\Scripts\python.exe yt-dlp-gui.py
```

بعد التفعيل:

```powershell
python yt-dlp-gui.py
```

لا يوجد `main.py`. لا يوجد أمر `python -m` خاص بالمشروع.

عند النجاح تظهر نافذة عنوانها تقريباً: `YT-DLP GUI - Ready`.

---

## 11. طريقة استخدام البرنامج

1. الصق رابطاً في حقل **URL**.
2. تأكد من مجلد الحفظ في **Path** (الافتراضي غالباً مجلد التنزيلات، أو آخر مسار محفوظ في سجل Windows: `HKCU\Software\YT-DLP GUI`).
3. **Choose** لاختيار مجلد آخر.
4. اختر أحد الأزرار:
   - **Download Video**: أفضل فيديو (قد يدمج صوتاً عبر FFmpeg).
   - **Download Audio**: `format: bestaudio`.
   - **Customize Downloads**: استخراج الصيغ ثم اختيار فيديو/صوت/تحويل صوتي.
5. راقب **Download Queue**: العنوان، المدة، الحجم، شريط التقدم، الحالة.
6. إذا كان الرابط قائمة تشغيل، تظهر نافذة اختيار مقاطع.

---

## 12. مثال على الاستخدام

مثال تم التحقق من استخراج بياناته وتنزيله (محتوى عام قصير لأغراض الاختبار التعليمي):

```text
https://www.youtube.com/watch?v=jNQXAC9IVRw
```

1. شغّل البرنامج.
2. الصق الرابط.
3. اختر مجلد حفظ تملك صلاحية الكتابة فيه.
4. اضغط **Download Audio** لتجربة سريعة بدون دمج، أو **Download Video** بعد توفر FFmpeg.

لا تستخدم روابط خاصة أو مدفوعة أو محمية.

---

## 13. المشاكل الشائعة

| المشكلة | العلامة |
|---|---|
| عدم تثبيت الحزم | `ModuleNotFoundError: No module named 'yt_dlp'` |
| غياب FFmpeg بجانب الملف (قبل التعديل) | تعطل عند تحميل الوحدة |
| غياب FFmpeg (بعد التعديل) | تحذير في السجل؛ فشل الدمج |
| Tkinter غير مثبت | فشل `import tkinter` |
| رابط فارغ/غير صالح | رسالة `URL is empty!` أو `URL is invalid!` |
| مجلد الحفظ فارغ | `Download directory is empty!` |
| صيغة غير موجودة | فشل التنزيل من yt-dlp |
| فيديو خاص | نافذة اختيار متصفح للكوكيز — لا تستخدمها في الدرس لتجاوز حماية |

---

## 14. حلول المشاكل

1. **yt_dlp غير موجود**: أنشئ `.venv` وثبّت `requirements.txt` ثم شغّل بمفسّر البيئة.
2. **FFmpeg**: انسخ `ffmpeg.exe` و`ffprobe.exe` بجانب `yt-dlp-gui.py`.
3. **الأيقونة**: يحتاج `icon.png` بجانب السكربت (`root.iconphoto`). الملف موجود في المستودع.
4. **PowerShell Execution Policy**: استخدم المسار الكامل لـ `.\.venv\Scripts\python.exe`.
5. **مسار عربي في المجلد**: التشغيل من هذا المسار نجح؛ إن ظهرت مشاكل ترميز نادرة شغّل من مجلد بمسار إنجليزي مؤقتاً.

---

## 15. أخطاء yt-dlp المحتملة

- `This video is unavailable`: الرابط غير صالح أو المقطع محذوف.
- `Requested format is not available`: الصيغة المختارة غير موجودة (مثل طلب `18` بينما الموقع لا يقدّمها).
- `merging of multiple formats but ffmpeg is not installed`: زر الفيديو يحتاج FFmpeg.
- تغييرات YouTube: حدّث `yt-dlp` داخل البيئة:
  ```powershell
  .\.venv\Scripts\python.exe -m pip install -U "yt_dlp[default,curl-cffi]"
  ```
- فيديو خاص/مقيد: البرنامج قد يعرض نافذة كوكيز المتصفح. **لا** تلقّن الطلاب استخراج كوكيز أو تجاوز قيود.

---

## 16. مشاكل FFmpeg

- الملف يجب أن يكون التنفيذ الفعلي `ffmpeg.exe` وليس حزمة Python.
- التحويل الصوتي في التخصيص يضيف `postprocessors` بمفتاح `FFmpegExtractAudio`.
- `ffprobe.exe` يُستخدم عادةً مع FFmpeg للفحص؛ README يطلب نسخه مع `ffmpeg.exe`.

---

## 17. مشاكل Windows

- DPI: الكود يستدعي `SetProcessDpiAwareness(2)` ويسكت إن فشل.
- حفظ المسار: `winreg` تحت `Software\YT-DLP GUI`.
- البناء إلى exe يحتاج أيضاً `icon.ico` و`deno.exe` حسب `build.txt` و`yt-dlp-gui.spec` — هذا للتعبئة وليس للدرس الأول.

---

## 18. ملاحظات قانونية وحقوق المحتوى

- الأداة غلاف تعليمي لـ yt-dlp.
- نزّل فقط محتوى مسموحاً لك بتنزيله.
- لا تستخدم البرنامج لكسر حماية أو تجاوز تسجيل الدخول أو CAPTCHA.
- ميزة الكوكيز من المتصفح موجودة في الكود (`cookiesfrombrowser`) ولا تُدرَّس كطريقة للوصول غير المصرّح به.
- احترم شروط الموقع ورخصة المقطع.

---

## 19. كيفية إيقاف البرنامج

- أغلق نافذة التطبيق (زر الإغلاق).
- في الطرفية التي شغّلت منها السكربت: `Ctrl+C`.
- الخيوط المستخدمة `daemon=True` فتتوقف مع العملية الرئيسية.

لا يوجد زر إلغاء تنزيل داخل الواجهة الحالية.

---

## 20. كيفية تحديث dependencies إذا كان ذلك مطلوبًا

```powershell
.\.venv\Scripts\python.exe -m pip install -U -r requirements.txt
```

لتحديث المحرك فقط:

```powershell
.\.venv\Scripts\python.exe -m pip install -U "yt_dlp[default,curl-cffi]"
```

لا تحدّث عشوائياً أثناء الحصة إلا إذا فشل الاستخراج بسبب تغيير في الموقع.
