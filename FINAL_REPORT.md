# التقرير النهائي — YT-DLP GUI

تقرير المدرّس لهذا المشروع في المسار:

`D:\project_lv3\download_from-youtube\ytdlp-gui-master`

نقطة الدخول: **`yt-dlp-gui.py`** (ليس `main.py`). المستودع الأصلي: [aliencaocao/ytdlp-gui](https://github.com/aliencaocao/ytdlp-gui).

---

# 1. حالة المشروع

- **يعمل** (بعد بيئة افتراضية + FFmpeg محلي + أيقونة بجانب السكربت).
- السبب: التطبيق ملف Tkinter واحد يغلّف **yt-dlp**. الإقلاع كان يتعطل إذا غاب `ffmpeg.exe`؛ أُضيفت `resolve_ffmpeg_location()` حتى يبحث PATH بدل الإقلاع الفاشل. التنزيل من مصادر yt-dlp (اختُبر على YouTube عام) نجح.

# 2. البيئة

- **Python:** 3.12.0 — README يطلب 3.10–3.14
- **OS:** Windows 10
- **Virtual Environment:** `.venv` داخل مجلد المشروع
- **yt-dlp:** 2026.08.19 عبر `yt_dlp[default,curl-cffi]`
- **FFmpeg / FFprobe:** ثنائيان محليان (`ffmpeg.exe` / `ffprobe.exe`)، ليسا حزمة pip، ومستثنيان بـ `.gitignore`
- **Tkinter:** 8.6
- **Deno:** اختياري؛ غيابه تحذير فقط

# 3. ما تم اختباره

| Test | Result | Status |
|---|---|---|
| 1. الإقلاع | نافذة `YT-DLP GUI - Ready` | نجح |
| 2. رسم الواجهة | حقول URL/Path وأزرار Video/Audio/Customize والطابور | نجح |
| 3. رابط صالح | `is_valid_url` True لروابط YouTube / youtu.be | نجح |
| 4. رابط غير صالح | رسائل empty/invalid | نجح |
| 5. استخراج البيانات | عنوان ومدة وصيغ | نجح |
| 6. تنزيل فيديو | دمج عبر FFmpeg على مقطع عام قصير | نجح |
| 7. تنزيل صوت | `bestaudio` | نجح |
| 8–9. اختيار الجودة/الصيغة من الواجهة | المنطق موجود؛ النقر اليدوي على Customize جزئي | جزئي |
| 10. مجلد الحفظ | رفض المسار الفارغ | نجح |
| 11. شريط التقدم | `progress_hook` موجود | جزئي في الاختبار الآلي |
| 12–18. اكتمال الملف وحجمه وffprobe | ملفات > 0 ومدة صحيحة على مقطع قصير | نجح |
| إلغاء التنزيل | لا يوجد زر في الواجهة | غير مدعوم |

مقطع اختبار عام قصير: `https://www.youtube.com/watch?v=jNQXAC9IVRw`. لم تُستخدم روابط خاصة ولم تُستخرج كوكيز.

# 4. المشاكل التي وجدتها

**Problem:** `No module named 'yt_dlp'`  
**Cause:** الحزم غير مثبتة على Python النظام.  
**Fix:** `.venv` ثم `pip install -r requirements.txt`  
**Verification:** الاستيراد والإقلاع.

**Problem:** تعطل عند غياب `ffmpeg.exe`  
**Cause:** `get_res_path('ffmpeg.exe')` عند تحميل الوحدة؛ الثنائيات ليست في Git.  
**Fix:** `resolve_ffmpeg_location()` + نسخ `ffmpeg.exe` و`ffprobe.exe` بجانب السكربت.  
**Verification:** دمج فيديو نجح بعد التوفير.

**Problem:** README على GitHub يذكر `main.py`  
**Cause:** توثيق قديم.  
**Fix:** في هذه النسخة صُحّح README إلى `yt-dlp-gui.py`.  
**Verification:** قراءة README المحلي.

**Problem:** نسخة هذا المجلد بلا `icon.png` كانت ستفشل عند `iconphoto`  
**Cause:** الأصل غير منسوخ بالكامل.  
**Fix:** جلب `icon.png` من المستودع الأصلي، وتجاوز الأيقونة إن فسدت أو غابت.  
**Verification:** الملف موجود بجانب السكربت.

**غير مُصلح عن عمد:** عدم زر إلغاء؛ كوكيز الفيديو الخاص خارج منهج المبتدئ؛ فقرة Planned features في README الأصلي ما زالت تقول إن القوائم «مخططة» بينما الكود فيه `show_playlist_selector`.

# 5. الملفات التي تم تعديلها

- `yt-dlp-gui.py` — `resolve_ffmpeg_location`؛ عدم تعطيل الإقلاع إن غابت الأيقونة
- `README.md` — تشغيل من المصدر، اسم الملف الصحيح، روابط أدلة التدريس، ملاحظة استخدام مسؤول
- `.gitignore` — `.venv/` و`__pycache__/`
- `RUN_GUIDE.md` / `TEACHING_GUIDE.md` / `FILE_TEACHING_ORDER.md` / `PROJECT_MAP.md` — مواءمة الاسم والمسار مع هذا المشروع

# 6. الملفات التي تم إنشاؤها

- `RUN_GUIDE.md`
- `TEACHING_GUIDE.md`
- `FILE_TEACHING_ORDER.md`
- `PROJECT_MAP.md`
- **`FINAL_REPORT.md`** (هذا الملف)
- `.venv/` (محلي)
- `ffmpeg.exe` / `ffprobe.exe` عند توفرهما محلياً (لا تُرفع)

# 7. أهم ملفات المشروع

1. `yt-dlp-gui.py` — التطبيق كله
2. `requirements.txt` — yt-dlp وsanitize_filename (+ PyInstaller للبناء)
3. `README.md`
4. `icon.png` — للأيقونة عند الإقلاع
5. `ffmpeg.exe` / `ffprobe.exe` — دمج وتحويل

# 8. ترتيب تدريس الملفات

1. `README.md`
2. `RUN_GUIDE.md`
3. `requirements.txt`
4. `yt-dlp-gui.py` (كتل المبتدئ)
5. نفس الملف (تخصيص الصيغ والقوائم)
6. `PROJECT_MAP.md` / `TEACHING_GUIDE.md` للمدرّس
7. لاحقاً: `build.txt` → `yt-dlp-gui.spec` → `version.yml` → workflows

التفصيل: `FILE_TEACHING_ORDER.md`. لا ترسل `FINAL_REPORT.md` للطلاب في الحصة الأولى.

# 9. المفاهيم التي سيتعلمها الطلاب

دوال وقواميس واستثناءات واستيراد؛ Tkinter وأحداث؛ تحقق URL؛ `ydl_base_opts`؛ `YoutubeDL`؛ طابور `DownloadTask`؛ خيوط و`after`؛ hooks للتقدم؛ `outtmpl`؛ FFmpeg كبرنامج خارجي.

# 10. المفاهيم المتقدمة التي يجب تأجيلها

`handle_private_video` وكوكيز المتصفح؛ Canvas في `ScrolledWindow`؛ `extractor_args`؛ DPI و`winreg` بعمق؛ PyInstaller وCI.

# 11. المتطلبات السابقة للطلاب

Python أساسي، venv وpip، فهم أن FFmpeg ليس مكتبة pip، تنزيل محتوى مسموح فقط. Tkinter وthreading وyt-dlp ليست شرطاً مسبقاً.

# 12. المسار التعليمي المقترح

تشغيل قانوني وجولة واجهة → GUI والتحقق → yt-dlp كصندوق أسود → طابور وخيوط → صيغ وFFmpeg → قوائم وتعبئة اختيارياً.

# 13. طريقة تشغيل المشروع

من مجلد `ytdlp-gui-master` بعد تثبيت المتطلبات ووجود `ffmpeg.exe` و`icon.png`:

```powershell
.\.venv\Scripts\python.exe yt-dlp-gui.py
```

# 14. الاختبار النهائي

عمل: الإقلاع، الواجهة، التحقق، استخراج بيانات، صوت، فيديو مع دمج، ملفات بحجم > 0.

لم يُثبت بالنقر اليدوي بالكامل: نافذة Customize، تشغيل الملف في مشغّل وسائط (فُحص بـ ffprobe).

غير موجود: إلغاء من الواجهة.

# 15. توصيتك كمدرس

- **اشرح أولاً:** README ثم التشغيل ثم `requirements.txt` ثم من `yt-dlp-gui.py`: الواردات، النافذة، `is_valid_url`، `detect_and_handle`، `YoutubeDL`، `DownloadTask`.
- **لا تعطِ الطلاب ابتداءً:** `.spec` وCI ودرس الكوكيز وبناء exe.
- **الأهم:** ملف واحد يغلّف yt-dlp؛ الأزرار تبني خيارات وتضع مهمة في طابور؛ FFmpeg يدمج المسارات.
- **تقسيم الدروس:** تشغيل → GUI → yt-dlp → طابور → صيغ/FFmpeg → اختياري قوائم/exe.

أرسل للمساعد: `README.md` → `RUN_GUIDE.md` → `requirements.txt` → `yt-dlp-gui.py` مع تعليم «اشرح كتل اليوم فقط».
