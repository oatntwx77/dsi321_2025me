Tutorial 07: สร้าง Data Pipeline แบบเรียลไทม์แบบ End-to-End ด้วย Prefect 3 และ lakeFS

ภาพรวมของโปรเจกต์
ยินดีต้อนรับสู่ Tutorial 07 จากโปรเจกต์ papapipeline ซึ่งออกแบบมาเพื่อให้นักศึกษาได้เรียนรู้การสร้าง data pipeline แบบเรียลไทม์แบบครบวงจร (end-to-end) โดยบทเรียนนี้ต่อยอดจากแนวคิดใน tutorial ก่อนหน้า (เช่น tutorial03) ช่วยให้นักศึกษาออกแบบ pipeline ที่:

ดึงข้อมูลทุก ๆ 15 นาทีจากแหล่งข้อมูลที่เลือก

จัดการกระบวนการทำงานด้วย Prefect 3

จัดเก็บข้อมูลไว้ใน data lake ของ lakeFS ผ่านโปรโตคอล S3A

แสดงผลข้อมูลผ่าน Streamlit

บทเรียนนี้เน้นแนวทาง data engineering สมัยใหม่ เช่น workflow orchestration, การจัดเก็บข้อมูลใน data lake, และการควบคุมเวอร์ชันด้วย GitHub

ในตัวอย่างนี้ เราจะใช้ OpenWeatherMap API เพื่อดึงข้อมูลสภาพอากาศ (เช่น อุณหภูมิ ความชื้น ความเร็วลม) ของเมือง เช่น กรุงเทพฯ โดยสามารถปรับ pipeline เพื่อใช้แหล่งข้อมูลอื่นได้ เช่น Twitter API หรือ sensor data เพียงแค่แก้ไข schema และสคริปต์ให้เหมาะสม

สิ่งที่ควรเตรียมก่อนเริ่ม
Python 3.8 ขึ้นไป

Docker และ Docker Compose

บัญชี GitHub พร้อม SSH

API Key จาก OpenWeatherMap (สมัครที่ https://openweathermap.org)

lakeFS (แบบ local หรือ remote) พร้อมข้อมูล S3A Credentials

ความรู้พื้นฐานเกี่ยวกับ Prefect 3, Docker, และ Git

ขั้นตอนการติดตั้งระบบ
1. โคลนโปรเจกต์
bash
คัดลอก
แก้ไข
git clone https://github.com/wasit7/papapipeline.git
cd papapipeline/tutorial07
2. ติดตั้งไลบรารี
bash
คัดลอก
แก้ไข
pip install -r requirements.txt
3. ตั้งค่า Environment Variables
ตั้งค่า API Key:

bash
คัดลอก
แก้ไข
export OPENWEATHER_API_KEY=your-api-key
แก้ไขไฟล์ src/pipeline.py, src/setup.py, และ visualization/app.py ให้ใช้ข้อมูลดังนี้:

python
คัดลอก
แก้ไข
LAKEFS_ENDPOINT = "http://localhost:8000"
LAKEFS_ACCESS_KEY = "your-lakefs-access-key"
LAKEFS_SECRET_KEY = "your-lakefs-secret-key"
LAKEFS_REPO = "student-repo"
LAKEFS_BRANCH = "main"
4. รันสคริปต์ตั้งค่า lakeFS
bash
คัดลอก
แก้ไข
python src/setup.py
5. เริ่มใช้งาน Docker
bash
คัดลอก
แก้ไข
docker-compose up -d
6. สั่ง Deploy Pipeline ด้วย Prefect
bash
คัดลอก
แก้ไข
python src/pipeline.py deploy
จะสร้าง deployment ชื่อว่า data-pipeline ใน work pool default-agent-pool โดยรันทุก ๆ 15 นาที

วิธีใช้งาน
Prefect UI: ดูสถานะการรัน pipeline ได้ที่ http://localhost:4200

Streamlit Dashboard: แสดงผลข้อมูลแบบเรียลไทม์ที่ http://localhost:8501

lakeFS: เข้าถึงข้อมูลที่จัดเก็บไว้ที่ http://localhost:8000 (repo: student-repo, branch: main)

การเชื่อมต่อกับ GitHub
สั่ง push โค้ดขึ้น GitHub:

bash
คัดลอก
แก้ไข
git add .
git commit -m "Updated pipeline code"
git push origin main
หมายเหตุ: ต้องมี commit อย่างน้อย 3 ครั้งเพื่อผ่านเกณฑ์ KPI

โครงสร้างของข้อมูล (Schema)
กำหนดไว้ในไฟล์ SCHEMA.md ตัวอย่างเช่น:

json
คัดลอก
แก้ไข
{
  "columns": ["timestamp", "city", "temperature", "humidity", "wind_speed"],
  "types": ["TEXT", "TEXT", "REAL", "INTEGER", "REAL"],
  "key_columns": ["temperature", "humidity", "wind_speed"]
}
timestamp: เวลาที่เก็บข้อมูล (รูปแบบ ISO)

city: ชื่อเมือง เช่น กรุงเทพฯ

temperature: อุณหภูมิ (°C)

humidity: ความชื้น (%)

wind_speed: ความเร็วลม (m/s)

คอลัมน์หลัก (key_columns) ใช้ตรวจสอบคุณภาพข้อมูล (ห้ามมี missing)

ผลลัพธ์เมื่อทำสำเร็จ
เก็บข้อมูลได้มากกว่า 500 records ภายใน 1 สัปดาห์ (672 records จากการเก็บ 4 ครั้ง/ชม. × 24 ชม. × 7 วัน)

จัดเก็บข้อมูลใน lakeFS เป็นไฟล์ Parquet ที่ตรงตาม schema

มีอัตราการรัน Prefect สำเร็จมากกว่า 90% ภายใน 24 ชม. (96 ครั้ง)

แสดงข้อมูลแบบเรียลไทม์ใน Streamlit พร้อมตารางและกราฟอุณหภูมิ

มี GitHub repo ที่ประกอบด้วยโค้ด 8 ไฟล์, commit 3 ครั้งขึ้นไป และรายงานสมบูรณ์

แนวทางแก้ปัญหา
API Error: ตรวจสอบว่า OPENWEATHER_API_KEY ตั้งค่าแล้ว

lakeFS: ตรวจสอบว่า server รันอยู่และ credential ถูกต้อง (docker run -p 8000:8000 treeverse/lakefs)

Docker: พอร์ต 4200 (Prefect) และ 8501 (Streamlit) ต้องว่าง ถ้ามีปัญหา:

bash
คัดลอก
แก้ไข
docker-compose down && docker-compose up -d
Prefect Error: ตรวจสอบ log ใน Prefect UI ว่า work pool ทำงานอยู่หรือไม่

GitHub Error: ตั้งค่า SSH key:

bash
คัดลอก
แก้ไข
ssh-keygen -t ed25519 -C "your_email@example.com"
cat ~/.ssh/id_ed25519.pub
จากนั้นนำ key ไปเพิ่มที่ GitHub (Settings → SSH Keys)

การปรับแต่งเพิ่มเติม
หากใช้แหล่งข้อมูลอื่น เช่น Twitter API หรือ sensor:

แก้ไข src/pipeline.py ให้ดึงข้อมูลจากแหล่งใหม่

อัปเดต SCHEMA.md ให้สอดคล้องกับข้อมูลใหม่

แก้ไข visualization/app.py เพื่อแสดงข้อมูลที่เกี่ยวข้อง

แก้ไข REPORT.md เพื่อเขียนรายงานให้เหมาะสม

ตรวจสอบว่า lakeFS เก็บข้อมูลได้ 500+ records และ pipeline ทำงานทุก 15 นาที

รายชื่อไฟล์ในโปรเจกต์
src/pipeline.py: สคริปต์ pipeline ที่ใช้ Prefect

src/setup.py: สคริปต์สำหรับตั้งค่าสิ่งแวดล้อมและ lakeFS

visualization/app.py: โค้ดสำหรับ Streamlit dashboard

docker-compose.yml: ไฟล์ Docker config

SCHEMA.md: ไฟล์ schema ข้อมูล

REPORT.md: รายงานโปรเจกต์

README.md: คำแนะนำการใช้งาน

requirements.txt: รายการไลบรารี Python

ไลบรารีที่ต้องใช้ (จาก requirements.txt)
prefect==3.0.0

pandas==2.2.2

requests==2.31.0

streamlit==1.38.0

plotly==5.22.0

boto3==1.34.0

pyarrow==15.0.0

ขอบคุณ
บทเรียนนี้พัฒนาต่อยอดจาก papapipeline โดย Wasit Limprasert ได้แรงบันดาลใจจาก tutorial03 ซึ่งใช้ Prefect 3, Docker, และ GitHub
