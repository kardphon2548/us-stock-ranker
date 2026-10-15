# US Stock Ranker

เว็บคัดกรองหุ้นสหรัฐสำหรับศึกษาการลงทุนระยะยาว ใช้ข้อมูลจริงจาก SEC EDGAR และราคาปิดรายวันจาก Yahoo Finance พร้อมคะแนนและเหตุผลที่ข้อมูลบางหุ้นยังไม่เพียงพอสำหรับจัดอันดับ

**รุ่นนี้ใช้งบรายปีล่าสุด (FY) ไม่ใช่ TTM และราคาปิดไม่ใช่ราคาซื้อขายเรียลไทม์** น้ำหนักคะแนนเป็นโมเดลเริ่มต้นที่ยังไม่ผ่าน backtest แบบ point-in-time ใช้จัดลำดับหุ้นที่จะศึกษาต่อ

## เปิดเว็บ

หลัง workflow เผยแพร่สำเร็จ เปิด [US Stock Ranker](https://kardphon2548.github.io/us-stock-ranker/) ได้ทั้งคอมพิวเตอร์และมือถือ โดยไม่ต้องเปิดคอมพิวเตอร์ของเจ้าของเว็บทิ้งไว้

การเผยแพร่ครั้งแรกอาจใช้ snapshot ที่ทดสอบแล้วใน `source.zip` ตรวจวันที่ข้อมูลและสถานะการอัปเดตบนหน้าเว็บเสมอ

## เปิดการอัปเดตรายวัน

1. ใน repository เปิด **Settings → Secrets and variables → Actions → Secrets → New repository secret**
2. ตั้ง Name เป็น `SEC_USER_AGENT` และใส่ชื่อแอปตามด้วยอีเมลติดต่อจริงของคุณเป็นค่า Secret เช่น `USStockRanker your-contact@example.com` โดยเปลี่ยนอีเมลตัวอย่างก่อนบันทึก
3. ไป **Settings → Pages** ตรวจว่า Source เป็น **GitHub Actions**
4. เปิด **Actions → Update stock ranking and publish → Run workflow** เปิด `refresh_data` แล้วกดรัน
5. รอ `build` และ `deploy` ผ่าน จึงตรวจวันที่ข้อมูลใหม่บนเว็บ

หากยังไม่ได้เพิ่ม Secret งานตามเวลาจะหยุดที่ขั้นตรวจการตั้งค่า และเว็บยังแสดง snapshot เดิม การปิด `refresh_data` ในการรันด้วยตัวเองหมายถึงเผยแพร่ snapshot ที่อยู่ใน `source.zip` ซึ่งอาจเก่ากว่าข้อมูลบนเว็บ

ตารางรันคือจันทร์–ศุกร์ **22:40 UTC หรือประมาณ 05:40 น. อังคาร–เสาร์ในไทย** งานอาจล่าช้าหรือถูกข้าม และ scheduled workflow ใน public repository จะถูกปิดอัตโนมัติหากไม่มีกิจกรรมใน repository 60 วัน ควรตรวจ Actions และวันที่ข้อมูลอย่างน้อยเดือนละครั้ง [เงื่อนไข GitHub](https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows#schedule)

## โค้ดอยู่ที่ไหน และแก้ไขอย่างไร

โค้ดครบชุดอยู่ใน **`source.zip`** เพื่อรักษาโครงสร้างโฟลเดอร์ขณะอัปโหลดผ่านหน้าเว็บ GitHub ไฟล์ workflow ที่ `.github/workflows/update.yml` จะแตกไฟล์ไปที่ `project/` จากนั้นรันทดสอบ เก็บข้อมูล และเผยแพร่ `project/dist`

1. ดาวน์โหลด `source.zip` แล้วแตกไฟล์ด้วย **Extract All**
2. เปิด `README-th.md` ในไฟล์ที่แตกแล้วเพื่อดูคู่มือฉบับเต็ม
3. หน้าเว็บอยู่ที่ `dist/index.html`; รายชื่อหุ้นอยู่ที่ `scripts/universe.json`; สูตรคะแนนอยู่ที่ `scripts/scoring.py`
4. หากมี Python เปิด Terminal ในโฟลเดอร์ที่แตกไฟล์แล้ว รัน `py -m http.server 8000 --directory dist` และเปิด `http://localhost:8000`
5. หลังแก้ไข ให้บีบอัดสิ่งที่อยู่ข้างในโฟลเดอร์นั้นเป็น `source.zip` ใหม่ โดยเปิด ZIP แล้วต้องเห็น `dist`, `scripts` และ `tests` ทันที ไม่ซ้อนใต้ชื่อโฟลเดอร์อีกชั้น
6. อัปโหลดทับ `source.zip` ใน repository และรัน workflow ใหม่ การแก้ไฟล์ที่แตกในเครื่องจะยังไม่เปลี่ยนเว็บจนกว่าจะอัปโหลด ZIP และเผยแพร่สำเร็จ

Workflow ใช้ `--strict`: ถ้าดึงแหล่งข้อมูลดิบไม่ครบ จะหยุดรอบนั้นโดยคงเว็บเดิมไว้ รายการบัญชีที่ขาดไม่ถูกเติมศูนย์ และหุ้นที่ขาดตัวชี้วัดสำคัญจะพักอันดับ

## ค่าใช้จ่ายและข้อมูล

GitHub Pages ใช้ได้กับ public repository บน GitHub Free และ standard GitHub-hosted runners ใน public repository ใช้ได้ฟรีตามเงื่อนไขปัจจุบัน ระบบนี้ไม่ใช้ larger runner หรือซื้อโดเมนเอง เงื่อนไขและโควตาอาจเปลี่ยนได้ จึงไม่รับประกันฟรีถาวรหรือเวลาพร้อมใช้งาน [GitHub Pages](https://docs.github.com/en/pages/getting-started-with-github-pages/what-is-github-pages), [Actions billing](https://docs.github.com/en/billing/concepts/product-billing/github-actions)

SEC ไม่ต้องมี API key แต่ต้องระบุตัวตนและเคารพนโยบายการเข้าถึง ส่วน Yahoo endpoint เป็นแหล่งข้อมูลไม่เป็นทางการ อาจหยุดหรือเปลี่ยนได้ การเข้าถึงฟรีไม่ได้ให้สิทธิ์เผยแพร่ข้อมูลต่อหรือทำบริการเชิงพาณิชย์โดยอัตโนมัติ Public Pages เปิดให้คนอื่นเข้าถึง JSON ได้ จึงต้องตรวจสิทธิ์กับผู้ให้บริการก่อนขยายการใช้งาน [SEC API](https://www.sec.gov/search-filings/edgar-application-programming-interfaces), [ข้อจำกัดจากโครงการ yfinance](https://github.com/ranaroussi/yfinance)

ตัวชี้วัดมูลค่าตลาดและ EV ใช้หุ้นเฉลี่ย diluted จากงบเป็นค่าประมาณ จักรวาลหุ้นเป็นตัวอย่างที่คัดไว้ล่วงหน้า ไม่ใช่หุ้นทั้งสหรัฐ อ่านค่าจริง วันที่ข้อมูล กลุ่มเปรียบเทียบ และคำเตือนประกอบคะแนนเสมอ
