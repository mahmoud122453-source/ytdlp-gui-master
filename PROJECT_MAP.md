# PROJECT_MAP — YT-DLP GUI

خريطة المشروع كما هو في هذه النسخة. لا تفترض ملفات أو أصنافاً غير مذكورة هنا.

المستودع الأصلي: https://github.com/aliencaocao/ytdlp-gui  
الوصف في `version.yml`: ProductName **YT-DLP GUI**، CompanyName **Billy Cao**.

---

## 1. الغرض

واجهة سطح مكتب لتنزيل فيديو/صوت من المصادر التي يدعمها **yt-dlp**، مع طابور مهام، اختيار صيغ، وتحويل صوت عبر FFmpeg. مكتوبة بـ Python وTkinter في ملف واحد.

---

## 2. هيكل المجلدات

```text
ytdlp-gui-master/
  yt-dlp-gui.py          نقطة الدخول والمنطق والواجهة
  requirements.txt
  README.md
  RUN_GUIDE.md           أُضيف للتدريس
  TEACHING_GUIDE.md      أُضيف للتدريس
  FILE_TEACHING_ORDER.md أُضيف للتدريس
  PROJECT_MAP.md         هذا الملف
  FINAL_REPORT.md        تقرير المدرّس النهائي (لا يُدرَّس للطلاب)
  CLAUDE.md              ملاحظات لأدوات التطوير
  build.txt
  version.yml
  yt-dlp-gui.spec
  yt-dlp-gui-linux.spec
  icon.png
  icon.ico
  home.png / queue.png / details.png
  .gitignore
  .github/workflows/     windows_build.yml, linux_build.yml, auto_release.yml
  .venv/                 بيئة محلية (غير مرفوعة)
  ffmpeg.exe / ffprobe.exe   ثنائيات محلية مطلوبة على Windows (غير مرفوعة، نمط *.exe)
```

لا توجد حزمة `src/` ولا اختبارات وحدة في المستودع.

---

## 3. هيكل الملفات المهمة

| ملف | دور |
|---|---|
| `yt-dlp-gui.py` | التطبيق بالكامل |
| `requirements.txt` | اعتماديات pip |
| `README.md` | توثيق أصلي |
| ملفات spec / build / version | تعبئة PyInstaller |
| أيقونات ولقطات | موارد واجهة |
| workflows | بناء CI وتحميل FFmpeg/Deno |

---

## 4. المعمارية

تطبيق أحادي الملف:

- **طبقة عرض:** Tkinter + ttk (`root`, إطارات، أزرار، `ScrolledWindow`, `DownloadTask` كـ `Frame`).
- **طبقة تنسيق:** دوال `handle_*` و`detect_and_handle` تبني `ydl_opts`.
- **طبقة محرك:** `yt_dlp.YoutubeDL`.
- **طبقة نظام:** `ffmpeg.exe` عبر مفتاح `ffmpeg_location`؛ Deno اختياري على PATH.

```text
User → GUI → Validation → yt-dlp options → YoutubeDL
     → download/extract_info → hooks → (FFmpeg) → outtmpl file
```

هذا المسار مطابق للكود.

---

## 5. تدفق التنفيذ

1. تنفيذ `python yt-dlp-gui.py` يحمّل الوحدة من أعلى لأسفل (لا يوجد `if __name__ == '__main__'`).
2. ضبط السجل، `download_queue`, `ongoing_task`.
3. محاولة إضافة Deno إلى PATH؛ تحديد موقع FFmpeg.
4. تعريف الدوال والأصناف.
5. `root = Tk()` ثم الأيقونة والعنوان وشريط الحالة.
6. قراءة آخر مجلد حفظ (سجل Windows أو ملف Linux).
7. بناء حقول URL وPath والأزرار والطابور.
8. `do_tasks()` يجدول نفسه كل 500ms.
9. `status('Ready')` ثم `root.mainloop()`.

عند الضغط على زر:

1. `lambda` يقرأ `url_input.get()` و`path_input.get()`.
2. `detect_and_handle(url, path, mode)` حيث `mode` هو `video_best` أو `audio_best` أو `customize`.
3. تحقق فارغ/URL/`path`.
4. خيط يستدعي `extract_flat_info`.
5. إن وُجدت `entries` أو `_type == playlist` → `show_playlist_selector`.
6. وإلا `_handle_single` → أحد المعالجات الثلاثة.
7. المعالج ينشئ `DownloadTask` ويضيفه إلى `download_queue`.
8. `do_tasks` يشغّل الاستخراج ثم `start_task` → خيط `download()`.
9. `progress_hook` يحدّث النسبة والحالة؛ عند الانتهاء يزيل المهمة من الطابور ويصفّر `ongoing_task`.

حفظ الملف: `outtmpl` يصبح `os.path.join(path, '%(title)s.%(ext)s')` (وللصوت غالباً `*_audio.%(ext)s`).

---

## 6. تدفق البيانات

```text
URL نصي
  → is_valid_url
  → extract_flat_info / extract_info  → dict من yt-dlp
  → parse_info / parse_format        → بنية مبسطة للعرض
  → ydl_opts (نسخة من ydl_base_opts + format + outtmpl + hooks)
  → YoutubeDL.download
  → dict الحالة في progress_hook (_percent_str, _default_template)
  → StringVar / IntVar على الواجهة
  → ملف على القرص
```

---

## 7. مخطط الاعتماد

```text
yt-dlp-gui.py
  imports:
    copy, ctypes, logging, os, re, sys, threading, urllib.parse
    tkinter, ttk, filedialog, messagebox
    typing
    winreg (Windows فقط)
    yt_dlp
    sanitize_filename.sanitize
  يعتمد على ملفات:
    icon.png (إقلاع الواجهة)
    ffmpeg.exe (Windows، مفضّل محلي)
    deno.exe (اختياري)
  لا يستورد أي وحدة محلية أخرى.

لا يوجد ملف بايثون آخر يعتمد عليه.

requirements.txt ← يثبّت ما يستورده yt-dlp-gui.py (+ أدوات البناء)

yt-dlp-gui.spec → يحزّم yt-dlp-gui.py + icon.png + ffmpeg.exe + ffprobe.exe + deno.exe
```

---

## 8. الأصناف المهمة

| الصنف | الأب | الدور |
|---|---|---|
| `ScrolledWindow` | `Frame` | حاوية قابلة للتمرير بـ Canvas وScrollbars |
| `DownloadTask` | `Frame` | بطاقة مهمة: استخراج، تقدم، تشغيل التنزيل |

لا توجد dataclasses في المشروع.

---

## 9. الدوال المهمة

| دالة | الدور |
|---|---|
| `get_res_path` | مسار مورد مع دعم PyInstaller `_MEIPASS` |
| `resolve_ffmpeg_location` | FFmpeg محلي أو الاسم للبحث في PATH (إضافة تشغيل) |
| `handle_private_video` | حوار متصفح للكوكيز عند فيديو خاص — متقدم/حساس |
| `download` | `YoutubeDL.download` |
| `extract_info` | بيانات كاملة بدون تنزيل |
| `extract_flat_info` | اكتشاف سريع للقوائم |
| `status` | شريط الحالة وعنوان النافذة والسجل |
| `parse_info` / `parse_format` | تبسيط بيانات الصيغ |
| `is_valid_url` | scheme + netloc |
| `get_entry_url` | رابط عنصر قائمة |
| `show_playlist_selector` | اختيار المقاطع |
| `_queue_selected_entries` / `_chain_customize` | طابور القائمة |
| `detect_and_handle` | بوابة الأزرار |
| `_handle_single` | توجيه الوضع |
| `handle_download_video_best` | أفضل فيديو |
| `handle_download_audio_best` | `bestaudio` |
| `handle_download_info` | نافذة التخصيص |
| `select_save_path` | حوار مجلد + حفظ المسار |
| `do_tasks` | مجدول الطابور |

---

## 10. المكتبات الخارجية

| الاسم | لماذا | ما يُفهم | ما يُترك صندوقاً أسود |
|---|---|---|---|
| `yt_dlp` | الاستخراج والتنزيل | `YoutubeDL`, `download`, `extract_info`, `sanitize_info`, الأخطاء `DownloadError`/`ExtractorError`, `version` | بروتوكولات الموقع، JS extractors |
| `sanitize_filename` | تنظيف العنوان لاسم ملف | `sanitize()` | قواعد أنظمة الملفات |
| Tkinter/ttk | الواجهة | Frame, Button, Entry, Label, Progressbar, Toplevel, messagebox, filedialog, after | تفاصيل ttk الداخلية |
| `curl_cffi` وغيرها | extras لـ yt-dlp عبر pip | أنها تُثبَّت مع المتطلبات | الاستخدام المباشر — غير موجود في كود GUI |

---

## 11. الأدوات الخارجية

| أداة | النوع | كيف يتوقعها المشروع |
|---|---|---|
| FFmpeg | تنفيذي | `ffmpeg_location` → `ffmpeg.exe` بجانب الملف على Windows |
| FFprobe | تنفيذي | يُرفق في البناء README/CI؛ مفيد للفحص |
| Deno | تنفيذي اختياري | يُضاف مجلده إلى PATH إن وُجد |
| UPX | ضغط exe | CI/بناء فقط |

---

## 12. تفاعل المستخدم

1. فتح التطبيق → `Ready`.
2. لصق URL واختيار Path.
3. Download Video / Audio / Customize.
4. إن كانت قائمة: اختيار المقاطع ثم تنزيل موحّد أو تخصيص.
5. ظهور بطاقة في Download Queue: استخراج ثم تنزيل ثم Finished.
6. لا يوجد زر إلغاء في الواجهة الحالية.
7. فيديو خاص قد يفتح حوار متصفح — خارج منهج المبتدئ وخارج أي تجاوز حماية.

---

## جرد مفاهيم موجود فعلياً في الكود

**أساسيات Python:** متغيرات، نصوص، أعداد، منطقي، قوائم، قواميس، شروط، حلقات `for`/`while` (في الحوارات)، دوال، return، وسوم افتراضية، استثناءات، imports، وحدات، ملفات (`open` على Linux)، f-strings، شرائح نصوص، `lambda`، unpacking بسيط.

**غير موجود بشكل بارز:** sets، comprehensions كثيرة، decorators كتعريف `@`، async/await، dataclasses، subprocess مباشر، logging.handlers معقّد.

**متوسط:** أصناف، وراثة `Frame`، `super()`، type hints (`dict[str, Any]`, `Callable`, `Union`)، threading، context manager `with YoutubeDL()`، callbacks (`command=`, hooks، `trace_add`)، regex، `copy.deepcopy`، `global`.

**مشروع:** GUI حدثي، تحقق URL، تنزيل، progress callbacks، تنفيذي خارجي، وسائط متعددة، طابور، مسارات، إعدادات سجل Windows.

---

## مستويات التجريد

**المستوى 1 (مبتدئ يجب أن يفهم):** نافذة، حقل، زر، التحقق، `YoutubeDL(opts)`، الطابور كقائمة مهام، الملف يُحفظ في Path.

**المستوى 2 (متوسط):** hooks، `after`، فصل video/audio formats، `noplaylist`، postprocessors، قوائم تشغيل.

**المستوى 3 (لاحقاً):** PyInstaller `_MEIPASS`، cookiesfrombrowser، extractor_args، DPI، تفاصيل Canvas/MouseWheel، CI.

---

## ما يُخفى ابتداءً (يبقى في الكود)

| مقطع | لماذا متقدم | متى يعود | متطلب |
|---|---|---|---|
| `handle_private_video` | كوكيز متصفح وحساسية قانونية | درس أخلاقيات/سطح مكتب متقدم | أمن الويب |
| `ScrolledWindow` كاملة | Canvas وربط عجلة معقّد | درس Tkinter متقدم | ويدجت أساسية |
| `handle_download_info` بالكامل | تداخل متغيرات ودوال داخلية | بعد نجاح التنزيل البسيط | قواميس + Tk |
| `extractor_args` skip dash/hls | تفاصيل بروتوكول YouTube | عند مناقشة نقص الصيغ | yt-dlp خيارات |
| spec/CI | بناء لا تشغيل | نهاية الدورة | فهم المصدر أولاً |
