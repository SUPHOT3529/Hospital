# ระบบจัดตารางเวรพยาบาล (Supabase Edition)

ระบบจัดตารางเวรพยาบาลออนไลน์ พัฒนาต่อยอดจาก "ระบบจัดตารางเวรพยาบาล Standalone Edition"
โดยเพิ่มการเชื่อมต่อฐานข้อมูล **Supabase** (Auth + Database) เพื่อให้ข้อมูลซิงก์ข้ามเครื่อง/เบราว์เซอร์ได้
แทนที่จะเก็บอยู่ใน LocalStorage ของเบราว์เซอร์เดียวเหมือนเดิม

หน้าเว็บทั้งหมดยังเป็นไฟล์ `index.html` ไฟล์เดียว (HTML/CSS/JS inline) — ไม่ต้องมี backend เซิร์ฟเวอร์แยก
ใช้ Supabase เป็น backend (ฐานข้อมูล + ระบบ login) และ GitHub Pages เป็นตัว host หน้าเว็บ

## โครงสร้างไฟล์

- `index.html` — ตัวโปรแกรมทั้งหมด (หน้าเว็บที่ GitHub Pages จะ serve)
- `supabase_schema.sql` — SQL สำหรับสร้างตารางและตั้งค่าความปลอดภัย (RLS) ใน Supabase
- `README.md` — คู่มือฉบับนี้

## วิธีตั้งค่าใหม่ (ทำครั้งเดียว)

### 1) สร้างตารางใน Supabase

1. เข้า [Supabase Dashboard](https://supabase.com/dashboard) → เลือกโปรเจกต์ของคุณ
2. ไปที่เมนู **SQL Editor** → New query
3. คัดลอกเนื้อหาทั้งหมดจากไฟล์ `supabase_schema.sql` ในโปรเจกต์นี้ → วาง → กด **Run**
4. (แนะนำ) ไปที่ **Authentication → Providers → Email** แล้วปิด "Confirm email"
   ถ้าต้องการให้สมัครสมาชิกแล้วใช้งานได้ทันทีโดยไม่ต้องกดยืนยันในอีเมล

### 2) ใส่ค่า API Key ลงในโค้ด

1. ไปที่ **Settings → API** ในหน้า Supabase Dashboard
2. คัดลอก **Project URL** และ **anon public key** (ห้ามใช้ service_role key)
3. เปิดไฟล์ `index.html` ในโปรเจกต์นี้ (บน GitHub เว็บก็แก้ได้ กดปุ่มดินสอ ✏ ที่ไฟล์)
4. หาบรรทัดประมาณนี้ (ใกล้ด้านบนของ `<script>`):
   ```js
   const SUPABASE_URL = 'YOUR_SUPABASE_URL';
   const SUPABASE_ANON_KEY = 'YOUR_SUPABASE_ANON_KEY';
   ```
5. แทนที่ด้วยค่าจริงของคุณ เช่น:
   ```js
   const SUPABASE_URL = 'https://xxxxxxxxxxxx.supabase.co';
   const SUPABASE_ANON_KEY = 'eyJhbGciOiJI....(ยาวๆ)';
   ```
6. Commit การแก้ไข

> ถ้าไม่ใส่ค่าเหล่านี้ ระบบจะยังทำงานได้ปกติแบบเดิม (เก็บข้อมูลใน LocalStorage เครื่องเดียว ไม่มีหน้า login)

### 3) เปิดใช้งาน GitHub Pages (ทำครั้งเดียว)

1. ไปที่ repo นี้บน GitHub → **Settings → Pages**
2. ในหัวข้อ **Build and deployment → Source** เลือก **Deploy from a branch**
3. **Branch** เลือก `main` และโฟลเดอร์ `/ (root)` → กด **Save**
4. รอประมาณ 1-2 นาที เว็บจะพร้อมใช้งานที่ `https://suphot3529.github.io/Hospital/`

### 4) เข้าใช้งานครั้งแรก

1. เปิดลิงก์เว็บ → จะเห็นหน้า "เข้าสู่ระบบ"
2. กด **"ยังไม่มีบัญชี? สมัครสมาชิก"** → กรอกอีเมล + รหัสผ่าน (อย่างน้อย 6 ตัวอักษร) → สมัคร
3. เข้าสู่ระบบ ระบบจะดึง/บันทึกข้อมูลจาก Supabase อัตโนมัติทุกครั้งที่กด "บันทึก"

## การอัปเดตโค้ดในอนาคต

แก้ไขไฟล์ `index.html` แล้ว commit ขึ้น branch `main` — GitHub Pages จะ deploy เวอร์ชันใหม่ให้อัตโนมัติภายในไม่กี่นาที

## หมายเหตุด้านความปลอดภัยของข้อมูล

- ข้อมูลตารางเวรของแต่ละบัญชีผู้ใช้ถูกจำกัดสิทธิ์ด้วย Row Level Security (RLS) ใน Supabase
  แต่ละบัญชีเห็น/แก้ไขได้เฉพาะข้อมูลของตัวเอง
- ถ้าหลายคนในหอผู้ป่วยต้องใช้ข้อมูลชุดเดียวกันร่วมกัน (บัญชีเดียวกัน) ให้ใช้อีเมล/รหัสผ่านชุดเดียวกันในการเข้าสู่ระบบทุกเครื่อง
- อย่านำ **anon key** ไปใช้แทน service_role key ในที่อื่น และอย่า commit service_role key หรือรหัสผ่านฐานข้อมูลขึ้น GitHub เด็ดขาด
