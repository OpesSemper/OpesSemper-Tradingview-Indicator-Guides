# GBT — Gamma Bomb Trap

## Indicator สำหรับอ่านสนามรบ: WHERE (ราคาไหน) + WHEN (เวลาไหน)

GBT แปลง Open Interest และ Intraday Volume จาก CME ให้กลายเป็นแผนที่ของ dealer hedging pressure บน chart พร้อม session timing จาก Quarterly Theory เพื่อให้รู้ว่า "ราคาไหนมี mechanical force" และ "ช่วงเวลาไหนตลาดจะขยับ"

---

## 📥 Inputs — ข้อมูลที่ต้องใส่

### Data Input

| Input | คืออะไร | วิธีใส่ |
|---|---|---|
| **Future Symbol** | Symbol ของ futures ที่วิเคราะห์ | เลือกจาก dropdown เช่น `COMEX:GC1!` |
| **OI Data CSV** | ข้อมูล Open Interest จาก CME (end-of-day) | Copy ข้อความทั้งหมดจากไฟล์ `OIData.txt` แล้ว paste |
| **Intraday Volume CSV** | ข้อมูล Volume ที่เทรดระหว่างวัน จาก CME | Copy จากไฟล์ `IntradayData.txt` แล้ว paste |

ข้อมูลทั้ง 2 ชุดมี format เดียวกัน: บรรทัดแรกเป็น header (บอก DTE, spot price), บรรทัดที่ 2 เป็นหัวคอลัมน์, บรรทัดที่ 3 เป็นต้นไปเป็นข้อมูล `Strike, Call, Put, IV`

### Price Grid

| Input | คืออะไร | ค่าแนะนำ |
|---|---|---|
| **Major Level Step** | ขนาดของ grid ที่ใช้จัดกลุ่ม strike prices | Gold = `25` |

### GEX / Model

| Input | คืออะไร | ค่าแนะนำ |
|---|---|---|
| **Risk-Free Rate** | อัตราดอกเบี้ย (ใช้ใน Black-76) | `0.045` |
| **Contract Multiplier** | ตัวคูณ contract (Gold 1 contract = 100 oz) | `100` |
| **Heatmap lookback** | จำนวน bars ที่ drawings ยืดย้อนกลับไป | `48` |
| **Intraday Vol weight (α)** | สัดส่วนของ intraday volume ใน composite GEX | `0.4` |
| **Block Trade trigger** | Vol/OI ratio ขั้นต่ำที่จะถือว่าเป็น block trade | `2.0` |

α = 0 ใช้เฉพาะ OI, α = 1 ใช้เฉพาะ Volume, α = 0.4 ผสมทั้งสอง (แนะนำ)

### Quarterly Theory

| Input | คืออะไร |
|---|---|
| **Show Session Backgrounds** | แสดงสี background แยกตาม session |
| **Show True Open Lines** | แสดงเส้นราคาเปิดของแต่ละ session |
| **Show 90-min Quarter borders** | แสดงเส้นแบ่ง 90 นาทีภายใน session |
| **Timezone** | timezone สำหรับคำนวณ session (ปกติ = `America/New_York`) |

### Display

| Input | คืออะไร |
|---|---|
| **Label Mode** | `Compact` = แสดงแค่ชื่อย่อ+ราคา (hover ดูรายละเอียด), `Full` = แสดงทุกอย่าง |
| **GEX Heatmap** | boxes สีแสดงความเข้มของ GEX ทุก price level |
| **Dealer Hedge Zones** | boxes แสดงเฉพาะ strike ที่ dealer hedging pressure สูง |
| **Gamma Flip Zone** | box สีเหลือง ณ จุดที่ GEX เปลี่ยนเครื่องหมาย |
| **Max Pain** | เส้นราคาที่ option buyers ขาดทุนมากที่สุด |
| **Call/Put Walls** | เส้น resistance (Call Wall) และ support (Put Wall) |
| **Vanna & Charm** | เส้นประแสดง pressure จาก IV change และ time decay |
| **Volume Pressure** | label แสดง net call vs put volume bias |
| **Block Alerts** | box+label เมื่อตรวจพบ block trade |
| **Dashboard Table** | ตารางสรุปข้อมูลทุก layer |

### Colors — ปรับสีได้ทุกองค์ประกอบ

---

## 📊 Chart Drawings — สิ่งที่เห็นบน chart

### เส้นแนวนอน (Lines)

| เส้น | สี (default) | ลักษณะ | ความหมาย |
|---|---|---|---|
| **CW (Call Wall)** | 🟢 เขียวอ่อน | ทึบ | Resistance — strike เหนือ spot ที่ dealer ต้องขาย futures มากที่สุด |
| **PW (Put Wall)** | 🔴 แดง | ทึบ | Support — strike ใต้ spot ที่ dealer ต้องซื้อ futures มากที่สุด |
| **MP (Max Pain OI)** | 🟣 ม่วง | ทึบ | ราคาที่ option buyers ขาดทุนรวมสูงสุด (จาก OI) — แรงดึงดูดเมื่อใกล้ expiry |
| **MPv (Max Pain Vol)** | 🟣 ม่วงจาง | ประ | Max Pain จาก intraday volume — แสดงการเปลี่ยนแปลงระหว่างวัน |
| **Vanna** | 🔵 ฟ้า | ประ | ทิศ pressure จาก IV change: "IV↑=SELL" = ถ้า IV ขึ้น dealer จะขาย futures |
| **Charm** | 🟠 ส้ม | ประ | ทิศ pressure จาก time decay: "T=SELL" = เวลาผ่านไป dealer จะขาย futures |

### กล่อง (Boxes)

| กล่อง | สี | ความหมาย |
|---|---|---|
| **Dealer Hedge Zone** 🟩 | เขียว (โปร่ง) | Long Gamma — dealer ขายขาขึ้น/ซื้อขาลง → กดความผันผวน, ราคา mean-revert |
| **Dealer Hedge Zone** 🟥 | แดง (โปร่ง) | Short Gamma — dealer ซื้อขาขึ้น/ขายขาลง → ขยายความผันผวน, ราคาเป็น trend |
| **Gamma Flip** 🟡 | เหลือง (โปร่ง) | จุดแบ่ง Long/Short Gamma — เหนือ = mean-revert, ใต้ = trend |
| **Block Alert** 💜 | ชมพู/ม่วง | ตรวจพบ block trade — "CALL Block" = institutional ซื้อ call, "PUT Block" = ซื้อ put |
| **GEX Heatmap** | เขียว/แดง (เข้มตาม intensity) | ความเข้ม GEX ทุก price level (ปิดโดย default) |

### พื้นหลัง (Background)

| สี background | Session | เวลา (EST) | Volatility |
|---|---|---|---|
| เทาเข้ม จางมาก | **Sydney** | 17:00–23:00 | ต่ำสุด |
| ชมพูอ่อน | **Tokyo** | 23:00–05:00 | ปานกลาง |
| ฟ้าอ่อน | **London** | 05:00–11:00 | สูง |
| เขียวอ่อน | **New York** | 11:00–17:00 | สูง |
| ขาวเกือบโปร่ง | **High Vol Zone** | London Q3-Q4 + NY Q1-Q2 | ⚡ สูงสุด |

### เส้น Session True Open (plot.style_linebr)

| เส้น | สี | ความหมาย |
|---|---|---|
| **Sydney Open** | เทา | ราคาเปิดของ Sydney session — bias reference |
| **Tokyo Open** | ชมพู | ราคาเปิดของ Tokyo session |
| **London Open** | ฟ้า | ราคาเปิดของ London session |
| **NY Open** | เขียว | ราคาเปิดของ New York session |

ราคาเหนือ True Open = bullish bias สำหรับ session นั้น, ใต้ = bearish bias

### แท่งเทียน (Bar Color)

| สีแท่ง | ความหมาย |
|---|---|
| 🟢 เขียว | Futures bar ปิดสูงกว่าเปิด (up bar) |
| 🔴 แดง | Futures bar ปิดต่ำกว่าเปิด (down bar) |

### Volume Pressure Label

| Label | ความหมาย |
|---|---|
| **VOL C 65%** | Call volume คิดเป็น 65% ของ total → bullish intraday flow |
| **VOL P 70%** | Put volume คิดเป็น 70% → bearish intraday flow |

---

## 📋 Dashboard Table — ตารางสรุป

| แถว | ข้อมูล | ตัวอย่าง | อธิบาย |
|---|---|---|---|
| **Regime** | LONG γ / SHORT γ | `LONG γ` `Mean-Revert` | Net GEX เป็นบวก = dealer กดราคา, ลบ = dealer เร่งราคา |
| **Net GEX** | ค่า GEX รวม | `+2.45M` | ยิ่งเลขใหญ่ = hedging pressure ยิ่งเยอะ |
| **CW / PW** | Call Wall / Put Wall | `5260 / 5100` | R = Resistance, S = Support |
| **Flip / MP** | Gamma Flip / Max Pain | `5152 / 5150` | "Above flip" = อยู่ฝั่ง Long Gamma |
| **Session** | session + QT phase | `LON` `Q3 Manip ⚡` | session ปัจจุบัน + phase ใน XAMD cycle + high vol flag |
| **V / C** | Vanna / Charm flow | `+1.2K / -0.8K` | ทิศของ hedging pressure จาก IV change และ time decay |
| **DTE** | Days to Expiry | `V:13.2h` `OI:0.55d` | เวลาที่เหลือก่อน expiry (V = จาก Intraday data) |

---

## 🔄 Quarterly Theory — XAMD Cycle

แต่ละ session (6 ชั่วโมง) แบ่งเป็น 4 quarters × 90 นาที:

| Quarter | Phase | พฤติกรรม |
|---|---|---|
| **Q1** | X — Continuation/Reversal | ต่อจาก session ก่อนหรือเริ่ม reverse |
| **Q2** | A — Accumulation | สร้าง range, consolidate |
| **Q3** | M — Manipulation | ⚠️ Judas Swing — sweep liquidity, false breakout |
| **Q4** | D — Distribution | ✅ Real move — expansion |

---

## ⚠️ หมายเหตุ

- ข้อมูล OI/Volume ต้อง paste ใหม่เมื่อมี data ใหม่จาก CME
- GEX สมมติว่า dealer เป็น counterparty (ถูกต้องส่วนใหญ่ แต่ไม่ 100%)
- เมื่อ DTE ต่ำมาก (< 1 วัน) gamma จะ extreme → walls แข็งแรงแต่เปลี่ยนเร็ว
- สำหรับ entry triggers (CRT + VWAP) ดูที่ **GBT-Flow**