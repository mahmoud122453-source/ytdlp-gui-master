# TEACHING_GUIDE — YT-DLP GUI

دليل المدرّس لتعليم المشروع كما هو موجود في المستودع، دون إعادة كتابته.

المشروع الأصلي: [YT-DLP GUI](https://github.com/aliencaocao/ytdlp-gui) — Billy Cao (aliencaocao).

---

## هدف المشروع التعليمي

تعليم كيف يربط تطبيق سطح مكتب مكتوب بـ **Python + Tkinter** محرك تنزيل جاهزاً (**yt-dlp**) مع طوابير، خيوط خلفية، واختيار صيغ، مع الاعتماد على **FFmpeg** كبرنامج خارجي للدمج والتحويل.

الهدف ليس بناء Downloader من الصفر، ولا شرح داخل yt-dlp.

---

## مستوى الطلاب

- أساسي إلى متوسط في Python.
- سبق لهم: متغيرات، دوال، قواميس، استثناءات، استيراد وحدات.
- لم يُشترط سبق Tkinter أو threading أو yt-dlp.

---

## Prerequisites

قبل الدرس:

1. Python 3.10–3.14 مع Tkinter.
2. القدرة على إنشاء venv وتثبيت `requirements.txt`.
3. فهم أن FFmpeg ملف `.exe` وليس مكتبة pip.
4. التزام قانوني: تنزيل محتوى مسموح فقط.

---

## Learning outcomes

بعد المسار يستطيع الطالب أن:

- يشغّل `yt-dlp-gui.py` من بيئة افتراضية.
- يرسم مسار المستخدم من الرابط حتى الملف على القرص.
- يميّز بين واجهة Tkinter ومنطق yt-dlp.
- يشرح دور `ydl_base_opts` و`progress_hooks`.
- يشرح لماذا FFmpeg لازم لدمج الفيديو والصوت على YouTube.
- يقرأ `detect_and_handle` كبوابة للأزرار الثلاثة.
- يؤجّل التعبئة بـ PyInstaller وكوكيز المتصفح إلى مستوى متقدم.

---

## ما الذي سيتعلمه الطلاب

من الملف الوحيد تقريباً `yt-dlp-gui.py`:

- GUI حدثي (Tkinter / ttk).
- التحقق من الرابط بـ `urllib.parse`.
- استدعاء مكتبة خارجية كصندوق أسود مضبوط بخيارات.
- طابور مهام وكائن `DownloadTask`.
- خيوط `daemon` مع `widget.after(0, ...)` لتحديث الواجهة.
- تحليل قواميس الصيغ (`parse_info` / `parse_format`).
- مسارات الملفات و`outtmpl`.
- معالجة أخطاء عبر `messagebox` و`try/except`.

---

## معمارية المشروع (كما في الكود)

المشروع **ملف بايثون واحد** (~960 سطراً) وليس حزمة متعددة الوحدات.

```text
المستخدم
  → نافذة Tkinter (حقل URL، Path، ثلاثة أزرار، طابور)
  → detect_and_handle (تحقق + اكتشاف قائمة تشغيل في خيط)
  → فيديو واحد أو show_playlist_selector
  → handle_download_video_best / handle_download_audio_best / handle_download_info
  → DownloadTask في download_queue
  → do_tasks كل 500ms
  → extract_info ثم download عبر yt_dlp.YoutubeDL
  → progress_hook / postprocessor_hook
  → FFmpeg عند الدمج أو تحويل الصوت
  → ملف في المجلد المختار حسب outtmpl
```

لا يوجد `asyncio`. لا يوجد `subprocess` في كود المشروع؛ yt-dlp يستدعي FFmpeg داخلياً.

---

## فلسفة التدريس

1. **ملفات قليلة، كتل كثيرة.** لا تدرّس المستودع سطراً بسطر من المجلد.
2. **yt-dlp صندوق أسود في المستوى 1:** ماذا نستدعي، أي خيارات، ماذا يعود.
3. **لا تبسيط بهدم المعمارية.** أظهر الطابور والخيوط بعد أن يفهم الطالب الزر → الدالة → YoutubeDL.
4. **لا كوكيز متصفح في الحصة الأولى.**
5. **لا PyInstaller حتى مشروع نهائي متقدم.**

---

## ترتيب التدريس الموصى به

انظر أيضاً `FILE_TEACHING_ORDER.md`.

1. README + الغرض + الحدود القانونية.
2. `requirements.txt` والفرق بين حزمة Python وFFmpeg.
3. تشغيل `yt-dlp-gui.py` ومشاهدة الواجهة.
4. الواردات والحالة العامة (`download_queue`, `ydl_base_opts`).
5. بناء الواجهة (`root`, الحقول, الأزرار).
6. `is_valid_url` و`detect_and_handle`.
7. `extract_info` / `download` و`YoutubeDL`.
8. `handle_download_video_best` و`handle_download_audio_best`.
9. `DownloadTask` و`do_tasks`.
10. `progress_hook` وشريط التقدم.
11. `parse_info` / `parse_format` ثم `handle_download_info`.
12. القوائم `show_playlist_selector` (متوسط).
13. FFmpeg و`postprocessors` (متوسط).
14. Windows registry وDPI و`get_res_path` (متقدم).
15. `handle_private_video` وملفات `.spec` (متقدم / لاحقاً).

---

## الملفات التي تُدرَّس

| أولوية | ملف |
|---|---|
| أساسي | `yt-dlp-gui.py` |
| أساسي | `requirements.txt` |
| مفيد | `README.md` |
| مفيد | `RUN_GUIDE.md` |

---

## الملفات التي تُؤجَّل

- `yt-dlp-gui.spec` / `yt-dlp-gui-linux.spec` / `build.txt` / `version.yml`
- `.github/workflows/*`
- `icon.png` / `icon.ico` / لقطات الشاشة (أصول)
- `ffmpeg.exe` / `ffprobe.exe` / `deno.exe` (أدوات، ليست دروس كود)
- `CLAUDE.md` (ملاحظات لأدوات AI، ليست مادة طالب)

---

## كتل الكود

التفصيل في القسم المقابل داخل `PROJECT_MAP.md` و`FILE_TEACHING_ORDER.md`. الوحدات الكبرى داخل `yt-dlp-gui.py`:

- الواردات والإعداد
- المسارات وFFmpeg/Deno
- `ydl_base_opts`
- `download` / `extract_info` / `extract_flat_info`
- `ScrolledWindow`
- `DownloadTask`
- إنشاء `root` وشريط الحالة
- التحليل والتحقق من URL
- بوابة التنزيل والمعالجات
- واجهة التخصيص
- الطابور `do_tasks`
- تجميع الويدجت في الأسفل

---

## المفاهيم

انظر الجرد الكامل في التقرير النهائي وفي `PROJECT_MAP.md`. الأهم للحصة:

قواميس، دوال، أصناف ترث `Frame`، أحداث أزرار، خيوط، hooks، خيارات yt-dlp، مسارات ملفات.

---

## اعتماد المفاهيم على بعضها

```text
Python أساسي
  → استيراد مكتبات
    → Tkinter ويدجت
      → ربط command=
        → التحقق من URL
          → نسخة من ydl_base_opts
            → YoutubeDL.extract_info / download
              → DownloadTask + طابور
                → progress_hooks + after()
                  → FFmpeg postprocessors
                    → قوائم تشغيل وcustomize
```

---

## تمارين

1. غيّر النص الافتراضي لشريط الحالة.
2. أضف `print` داخل `is_valid_url` وجرّب روابط صحيحة وخاطئة.
3. اشرح ماذا يحدث لو `path` فارغ (الكود موجود مسبقاً).
4. ارسم مخططاً من الزر إلى `download_queue.append`.
5. من `ydl_base_opts` اختر ثلاثة مفاتيح واشرحها بكلماتك.

---

## تحديات

1. لماذا `noplaylist` False في الخيارات الأساسية وTrue عند تنزيل عنصر واحد؟
2. لماذا `do_tasks` يستخدم `after(500)` بدل حلقة `while` على الخيط الرئيسي؟
3. ماذا يحدث إذا نجح التنزيل قبل انتهاء postprocessing؟ (انظر تعليق `progress_hook` عند `finished`).

---

## أخطاء الطلاب الشائعة

- `pip install ffmpeg` بدل وضع `ffmpeg.exe`.
- تشغيل `python yt-dlp-gui.py` خارج الـ venv فيظهر `No module named yt_dlp`.
- انتظار ملف اسمه `main.py` (GitHub README يذكره خطأ؛ الملف الحقيقي هنا `yt-dlp-gui.py`).
- محاولة فهم كل مفاتيح `ydl_base_opts` في الحصة الأولى.
- استدعاء دوال Tkinter مباشرة من خيط التنزيل دون `after`.
- رفع `ffmpeg.exe` أو `.venv` إلى Git.

---

## عروض مقترحة

1. تشغيل الواجهة وإدخال رابط فارغ ثم غير صالح.
2. استخراج بيانات مقطع عام قصير عبر Customize (إن سمح الوقت والشبكة).
3. مقارنة تنزيل صوت فقط مقابل فيديو مع/بدون FFmpeg (أظهر رسالة الدمج إن غاب FFmpeg).
4. إظهار عنصر في الطابور وشريط التقدم.

---

## واجب منزلي

- تلخيص معماري من 15 سطراً بمسار البيانات.
- جدول: زر الواجهة → الدالة → أهم مفتاح في `ydl_opts`.
- سؤال أخلاقي: متى يكون التنزيل غير مسموح؟

---

## تحدّي المشروع النهائي

بدون إعادة كتابة التطبيق:

- إضافة تسمية توضّح عدد المهام في الطابور، **أو**
- تعطيل أزرار التنزيل بينما `ongoing_task` صحيح مع إعادة التمكين عند الانتهاء، **أو** (متقدم)
- قراءة `build.txt` وشرح ما يفعله PyInstaller دون تنفيذ بناء كامل.

لا تطلب من المبتدئ تنفيذ `handle_private_video` أو استخراج كوكيز.
