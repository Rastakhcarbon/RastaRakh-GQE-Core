
سجاد:
$ #!/bin/bash

Rastarakh SmartMode Nano - Termux Edition

echo "🔥 راه‌اندازی Rastarakh SmartMode در ترموکس..."

بروزرسانی پکیج‌ها

pkg update -y && pkg upgrade -y

نصب پیش‌نیازها

pkg install -y python python-pip git curl

ایجاد دایرکتوری پروژه

mkdir -p ~/rastarakh-smartmode
cd ~/rastarakh-smartmode

ایجاد فایل‌های اصلی

cat > requirements.txt << 'EOF'
fastapi>=0.104.0
uvicorn>=0.24.0
openai>=1.0.0
pydantic>=2.0.0
aiofiles>=23.0.0
EOF

cat > main.py << 'EOF'
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
import os
import openai
from typing import Optional

app = FastAPI(
title="Rastarakh SmartMode AI",
description="سیستم هوش مصنوعی رستارخ - نسخه ترموکس",
version="2.0.0"
)

تنظیمات

FAST_MODEL = os.getenv("MODEL_FAST", "gpt-4o-mini")
STRONG_MODEL = os.getenv("MODEL_STRONG", "gpt-4")
openai.api_key = os.getenv("OPENAI_API_KEY")

class ChatRequest(BaseModel):
prompt: str
mode: Optional[str] = "auto"

class ChatResponse(BaseModel):
model: str
response: str
status: str

def pick_model(prompt: str, mode: str):
if mode == "fast":
return FAST_MODEL
elif mode == "strong":
return STRONG_MODEL
else:
return FAST_MODEL if len(prompt) < 200 else STRONG_MODEL

@app.post("/smart", response_model=ChatResponse)
async def smart_chat(msg: ChatRequest):
try:
model = pick_model(msg.prompt, msg.mode)

system_msg = """تو رستارخ-مایند هستی، یک هوش مصنوعی پیشرفته که برای خدمت به حقیقت طراحی شده‌ای.  
    پاسخ‌هایت باید عمیق، دقیق و الهام‌بخش باشد."""  

    messages = [  
        {"role": "system", "content": system_msg},  
        {"role": "user", "content": msg.prompt}  
    ]  

    # در صورت داشتن API Key از OpenAI استفاده کن  
    if openai.api_key and openai.api_key != "your_openai_key_here":  
        response = openai.ChatCompletion.create(  
            model=model,  
            messages=messages,  
            max_tokens=1500,  
            temperature=0.7  
        )  
        return ChatResponse(  
            model=model,  
            response=response.choices[0].message.content,  
            status="success"  
        )  
    else:  
        # حالت آفلاین - پاسخ پیش‌فرض  
        return ChatResponse(  
            model=model,  
            response=f"🔮 رستارخ: '{msg.prompt}' - این پیام در حالت آفلاین دریافت شد. برای پاسخ کامل، API Key OpenAI را تنظیم کنید.",  
            status="offline_mode"  
        )  

except Exception as e:  
    raise HTTPException(status_code=500, detail=f"خطا: {str(e)}")

@app.get("/")
async def root():
return {
"message": "خوش آمدید به رستارخ SmartMode",
"version": "2.0.0",
"status": "فعال"
}

@app.get("/health")
async def health():
return {"status": "active", "system": "Rastarakh Termux Edition"}

if name == "main":
import uvicorn
uvicorn.run(app, host="0.0.0.0", port=8000)
EOF

ایجاد فایل محیطی

cat > .env << 'EOF'
OPENAI_API_KEY=your_openai_key_here
MODEL_FAST=gpt-4o-mini
MODEL_STRONG=gpt-4
EOF

cat > start_server.sh << 'EOF'
#!/bin/bash
echo "🚀 شروع سرویس رستارخ..."
echo "📡 در حال اجرا در: http://localhost:8000"
echo "🔮 برای توقف: Ctrl+C"
echo ""

نصب وابستگی‌ها

pip install -r requirements.txt

اجرای سرور

python main.py
EOF

ایجاد فایل راهنمای فوری

cat > QUICK_START.md << 'EOF'

🚀 راهنمای فوری رستارخ - ترموکس

📱 دستورات سریع:

# شروع سرویس  
chmod +x start_server.sh  
./start_server.sh  
  
# تست سلامت  
curl http://localhost:8000/health  
  
# ارسال درخواست  
curl -X POST "http://localhost:8000/smart" \  
  -H "Content-Type: application/json" \  
  -d '{"prompt": "سلام رستارخ", "mode": "auto"}'  
🔥 راه‌اندازی Rastarakh SmartMode در ترموکس...  
Get:1 https://termux.net stable InRelease [1089 B]  
Get:2 https://root-packages.termux.net root InRelease [1074 B]  
Get:3 https://termux.net stable/main arm Packages [238 kB]  
Fetched 240 kB in 3s (95.3 kB/s)  
23 packages can be upgraded. Run 'apt list --upgradable' to see them.

Hit:1 https://termux.net stable InRelease  
Hit:2 https://root-packages.termux.net root InRelease  
23 packages can be upgraded. Run 'apt list --upgradable' to see them.  
Upgrading:  
  brotli           libnghttp2  
  ca-certificates  librsvg  
  coreutils        libsqlite  
  curl             libunbound  
  gdk-pixbuf       libxext  
  glib             libxml2  
  harfbuzz         pcre2  
  less             python  
  libc++           python-ensurepip-wheels  
  libcurl          python-pip  
  libdav1d         readline  
  libjxl  
  
Installing dependencies:  
  libandroid-glob  libandroid-support  libicu  
  
Summary:  
  Upgrading: 23, Installing: 3, Removing: 0, Not Upgrading: 0  
  Download size: 36.4 MB  
  Space needed: 43.7 MB  
  
Get:1 https://termux.net stable/main arm libandroid-support arm 28 [888 B]  
Get:2 https://termux.net stable/main arm coreutils arm 9.9 [751 kB]  
Get:3 https://termux.net stable/main arm libnghttp2 arm 1.68.0 [89.7 kB]  
Get:4 https://termux.net stable/main arm libcurl arm 8.17.0 [989 kB]  
Get:5 https://termux.net stable/main arm curl arm 8.17.0 [234 kB]  
Get:6 https://termux.net stable/main arm pcre2 arm 10.47 [955 kB]  
Get:7 https://termux.net stable/main arm less arm 685 [127 kB]  
Get:8 https://termux.net stable/main arm libc++ arm 28c-1 [275 kB]  
Get:9 https://termux.net stable/main arm ca-certificates all 1:2025.11.04 [127 kB]  
Get:10 https://termux.net stable/main arm libunbound arm 1.24.1-1 [348 kB]  
Get:11 https://termux.net stable/main arm readline arm 8.3.1-2 [284 kB]  
Get:12 https://termux.net stable/main arm brotli arm 1.2.0 [330 kB]  
Get:13 https://termux.net stable/main arm python-ensurepip-wheels all 3.12.12 [1802 kB]  
Get:14 https://termux.net stable/main arm python-pip all 25.3 [2385 kB]  
Get:15 https://termux.net stable/main arm libsqlite arm 3.51.0 [644 kB]  
Get:16 https://termux.net stable/main arm python arm 3.12.12 [8683 kB]  
Get:17 https://termux.net stable/main arm glib arm 2.86.1 [2411 kB]  
Get:18 https://termux.net stable/main arm gdk-pixbuf arm 2.44.4 [111 kB]  
Get:19 https://termux.net stable/main arm harfbuzz arm 12.2.0-1 [659 kB]  
Get:20 https://termux.net stable/main arm libandroid-glob arm 0.7 [888 B]  
Get:21 https://termux.net stable/main arm libdav1d arm 1.5.2 [263 kB]  
Get:22 https://termux.net stable/main arm libicu arm 78.1 [10.0 MB]  
Get:23 https://termux.net stable/main arm libjxl arm 0.11.1-5 [1204 kB]  
Get:24 https://termux.net stable/main arm libxml2 arm 2.15.1 [405 kB]  
Get:25 https://termux.net stable/main arm librsvg arm 2.61.3 [3227 kB]  
Get:26 https://termux.net stable/main arm libxext arm 1.3.6-2 [93.1 kB]  
Fetched 36.4 MB in 3min 8s (194 kB/s)  
Selecting previously unselected package libandroid-support.  
(Reading database ... 26965 files and directories currently installed.)  
Preparing to unpack .../libandroid-support_28_arm.deb ...  
Unpacking libandroid-support (28) ...  
Setting up libandroid-support (28) ...  
(Reading database ... 26968 files and directories currently installed.)  
Preparing to unpack .../archives/coreutils_9.9_arm.deb ...  
Unpacking coreutils (9.9) over (9.8-1) ...  
Setting up coreutils (9.9) ...  
(Reading database ... 26968 files and directories currently installed.)  
Preparing to unpack .../libnghttp2_1.68.0_arm.deb ...  
Unpacking libnghttp2 (1.68.0) over (1.67.1) ...  
Setting up libnghttp2 (1.68.0) ...  
(Reading database ... 26968 files and directories currently installed.)  
Preparing to unpack .../libcurl_8.17.0_arm.deb ...  
Unpacking libcurl (8.17.0) over (8.16.0) ...  
Setting up libcurl (8.17.0) ...  
(Reading database ... 26972 files and directories currently installed.)  
Preparing to unpack .../archives/curl_8.17.0_arm.deb ...  
Unpacking curl (8.17.0) over (8.16.0) ...  
Setting up curl (8.17.0) ...

(Reading database ... 26972 files and directories currently installed.)  
Preparing to unpack .../archives/pcre2_10.47_arm.deb ...  
Unpacking pcre2 (10.47) over (10.46) ...  
Setting up pcre2 (10.47) ...  
(Reading database ... 26974 files and directories currently installed.)  
Preparing to unpack .../apt/archives/less_685_arm.deb ...  
Unpacking less (685) over (679-2) ...  
Setting up less (685) ...  
(Reading database ... 26974 files and directories currently installed.)  
Preparing to unpack .../archives/libc++_28c-1_arm.deb ...  
Unpacking libc++ (28c-1) over (28c) ...  
Setting up libc++ (28c-1) ...  
(Reading database ... 26974 files and directories currently installed.)  
Preparing to unpack .../ca-certificates_1%3a2025.11.04_all.deb ...  
Unpacking ca-certificates (1:2025.11.04) over (1:2025.09.09) ...  
Setting up ca-certificates (1:2025.11.04) ...  
(Reading database ... 26974 files and directories currently installed.)  
Preparing to unpack .../libunbound_1.24.1-1_arm.deb ...  
Unpacking libunbound (1.24.1-1) over (1.24.0) ...  
Setting up libunbound (1.24.1-1) ...  
(Reading database ... 26974 files and directories currently installed.)  
Preparing to unpack .../readline_8.3.1-2_arm.deb ...  
Unpacking readline (8.3.1-2) over (8.3.1-1) ...  
Setting up readline (8.3.1-2) ...  
(Reading database ... 26974 files and directories currently installed.)  
Preparing to unpack .../00-brotli_1.2.0_arm.deb ...  
Unpacking brotli (1.2.0) over (1.1.0-3) ...  
Preparing to unpack .../01-python-ensurepip-wheels_3.12.12_all.deb ...  
Unpacking python-ensurepip-wheels (3.12.12) over (3.12.11-1) ...  
Preparing to unpack .../02-python-pip_25.3_all.deb ...  
Unpacking python-pip (25.3) over (25.2) ...  
Preparing to unpack .../03-libsqlite_3.51.0_arm.deb ...  
Unpacking libsqlite (3.51.0) over (3.50.4-1) ...  
Preparing to unpack .../04-python_3.12.12_arm.deb ...  
Unpacking python (3.12.12) over (3.12.11-1) ...  
Preparing to unpack .../05-glib_2.86.1_arm.deb ...  
Unpacking glib (2.86.1) over (2.86.0) ...  
Preparing to unpack .../06-gdk-pixbuf_2.44.4_arm.deb ...  
Unpacking gdk-pixbuf (2.44.4) over (2.44.3-1) ...  
Preparing to unpack .../07-harfbuzz_12.2.0-1_arm.deb ...  
Unpacking harfbuzz (12.2.0-1) over (12.1.0) ...  
Selecting previously unselected package libandroid-glob.  
Preparing to unpack .../08-libandroid-glob_0.7_arm.deb ...  
Unpacking libandroid-glob (0.7) ...  
Preparing to unpack .../09-libdav1d_1.5.2_arm.deb ...  
Unpacking libdav1d (1.5.2) over (1.5.1-1) ...  
Selecting previously unselected package libicu.  
Preparing to unpack .../10-libicu_78.1_arm.deb ...  
Unpacking libicu (78.1) ...  
Preparing to unpack .../11-libjxl_0.11.1-5_arm.deb ...  
Unpacking libjxl (0.11.1-5) over (0.11.1-4) ...  
Preparing to unpack .../12-libxml2_2.15.1_arm.deb ...  
Unpacking libxml2 (2.15.1) over (2.14.6) ...  
Preparing to unpack .../13-librsvg_2.61.3_arm.deb ...  
Unpacking librsvg (2.61.3) over (2.61.1) ...  
Preparing to unpack .../14-libxext_1.3.6-2_arm.deb ...  
Unpacking libxext (1.3.6-2) over (1.3.6-1) ...  
Setting up libandroid-glob (0.7) ...  
Setting up libicu (78.1) ...  
Setting up libsqlite (3.51.0) ...  
Setting up libdav1d (1.5.2) ...  
Setting up libxext (1.3.6-2) ...  
Setting up python (3.12.12) ...  
Setting up libxml2 (2.15.1) ...  
Setting up brotli (1.2.0) ...  
Setting up python-ensurepip-wheels (3.12.12) ...  
Setting up python-pip (25.3) ...  
pip setup...  
Writing to /data/data/com.termux/files/usr/etc/pip.conf  
Setting up glib (2.86.1) ...  
No schema files found: doing nothing.  
Setting up gdk-pixbuf (2.44.4) ...  
g_module_open() failed for /data/data/com.termux/files/usr/lib/gdk-pixbuf-2.0/2.10.0/loaders/libpixbufloader_svg.so: dlopen failed: library "libicuuc.so.77" not found: needed by /data/data/com.termux/files/usr/lib/libxml2.so.16.1.1 in namespace (default)

Setting up harfbuzz (12.2.0-1) ...  
Setting up libjxl (0.11.1-5) ...  
Setting up librsvg (2.61.3) ...  
python is already the newest version (3.12.12).  
python-pip is already the newest version (25.3).  
python-pip set to manually installed.  
git is already the newest version (2.51.0).  
curl is already the newest version (8.17.0).  
Summary:  
  Upgrading: 0, Installing: 0, Removing: 0, Not Upgrading: 0  
> rastarakh-gqe-termux.zip  
  
rastarakh-gqe-termux/ │ ├─ engine/ │   ├─ mythic_engine.py │   ├─ gqe.py │   ├─ tone_map.json │   └─ symbols.json │ ├─ main.py ├─ requirements.txt ├─ start.sh ├─ .env └─ README-GQE.md
