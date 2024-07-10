# คุณสมบัติการปรับแต่ง Loot ในเกม SCUM

เอกสารนี้อธิบายเกี่ยวกับคุณสมบัติการปรับแต่ง loot ในเกม SCUM การปรับแต่ง loot ทำผ่านการสร้างและการแก้ไขไฟล์ JSON ต่างๆ ความรู้พื้นฐานเกี่ยวกับ JSON เป็นสิ่งจำเป็น หากคุณไม่เคยได้ยินเกี่ยวกับ JSON หรือจำเป็นต้องทบทวนความรู้ นี่คือลิงก์ที่มีประโยชน์:

- [JSON](https://www.json.org/)
- [W3Schools JSON Intro](https://www.w3schools.com/js/js_json_intro.asp)

ไฟล์ JSON ที่เกี่ยวข้องกับ loot ส่วนใหญ่สามารถส่งออกจากเกมได้ผ่านคำสั่งแอดมินต่างๆ ขณะที่บางไฟล์ต้องสร้างขึ้นเอง เส้นทางหลักที่พบไฟล์ JSON คือ:

- เซิร์ฟเวอร์มัลติเพลเยอร์: `<Server>\SCUM\Saved\Config\WindowsServer\Loot`
- โหมดผู้เล่นคนเดียว: `%LocalAppData%\SCUM\Saved\Config\WindowsNoEditor\Loot`

ไฟล์ JSON จะถูกโหลดเมื่อเริ่มเกม และการเปลี่ยนแปลงใดๆ สามารถโหลดใหม่ได้ผ่านคำสั่ง `#ReloadLootCustomizationsAndResetSpawners` เพื่อทดสอบการปรับเปลี่ยนได้อย่างรวดเร็ว คำสั่งนี้จะถูกอธิบายรายละเอียดในภายหลัง รวมถึงกระบวนการส่งออก สร้าง และแก้ไขไฟล์ JSON ที่เกี่ยวข้องกับ loot

## ความหายาก

ความหายากถูกใช้ตลอดการปรับแต่ง loot โดยมันกำหนดความน่าจะเป็นในการเลือกวัตถุจากชุดของวัตถุ วัตถุเป็นคำที่เป็นนามธรรมและสามารถหมายถึงอะไรก็ได้ คุณสามารถคิดว่าวัตถุเป็นไอเท็มชั่วคราว แต่ละวัตถุในชุดจะได้รับการกำหนดความหายากซึ่งสามารถเป็นค่าดังต่อไปนี้:

- **Abundant:** มีโอกาสถูกเลือก 32 เท่าของ Extremely Rare
- **Common:** มีโอกาสถูกเลือก 16 เท่าของ Extremely Rare
- **Uncommon:** มีโอกาสถูกเลือก 8 เท่าของ Extremely Rare
- **Rare:** มีโอกาสถูกเลือก 4 เท่าของ Extremely Rare
- **Very Rare:** มีโอกาสถูกเลือก 2 เท่าของ Extremely Rare
- **Extremely Rare:** มีโอกาสถูกเลือกเท่ากับวัตถุ Extremely Rare อื่นๆ ในชุด

ยกตัวอย่างเช่น ชุดของวัตถุดังต่อไปนี้ (วัตถุในตัวอย่างนี้คือไอเท็ม):

`{ Apple (Abundant), Banana (Common), Lemon (Uncommon), Kiwi (Rare), Mango (Very Rare), Watermelon (Extremely Rare) }`

แอปเปิ้ลมีโอกาสถูกเลือกมากกว่ากล้วย 2 เท่า มากกว่าเลม่อน 4 เท่า มากกว่ากีวี 8 เท่า มากกว่าแมงโก้ 16 เท่า และมากกว่าสตรอเบอรี่ 32 เท่า ในทางกลับกัน กล้วยมีโอกาสถูกเลือกน้อยกว่าแอปเปิ้ล 2 เท่า มากกว่าเลม่อน 2 เท่า มากกว่ากีวี 4 เท่า มากกว่าแมงโก้ 8 เท่า และมากกว่าสตรอเบอรี่ 16 เท่า การเลือกวัตถุอื่นๆ เมื่อเปรียบเทียบกับผลไม้อื่นๆ สามารถอนุมานได้ในลักษณะเดียวกัน

อีกวิธีหนึ่งในการมองความหายากคือผ่านรูเล็ตที่พื้นที่ที่ครอบครองโดยวัตถุกำหนดความน่าจะเป็นในการถูกเลือก สำหรับตัวอย่างข้างต้น รูเล็ตจะดูดังนี้:

![รูเล็ตความหายาก](https://via.placeholder.com/600x300.png?text=Example+Roulette)

ตามที่เห็น แอปเปิ้ลครอบครองพื้นที่มากที่สุด ดังนั้นมันมีโอกาสถูกเลือกมากที่สุด ตรงกันข้ามกับแตงโมที่ครอบครองพื้นที่น้อยที่สุด ดังนั้นมันมีโอกาสถูกเลือกน้อยที่สุด ผลไม้อื่นๆ อยู่ระหว่างนั้น

มาดูตัวอย่างอีกครั้ง:

`{ Apple|Common, Banana|Common, Lemon|Rare, Watermelon|Very Rare }`

ครั้งนี้ ความหายากบางอย่าง "หายไป" และเรามีผลไม้สองชนิดที่มีความหายากเดียวกัน วัตถุที่มีความหายากเดียวกันจะถูกจัดกลุ่มเข้าด้วยกัน เพื่อกำหนดวัตถุที่ถูกเลือกในกลุ่ม จะมีการทอยลูกเต๋าอีกหนึ่งครั้งโดยมีโอกาสเท่ากันสำหรับวัตถุทั้งหมดที่มีความหายากเดียวกัน มาดูรูเล็ตสำหรับตัวอย่างข้างต้น:

![รูเล็ตความหายาก](https://via.placeholder.com/600x300.png?text=Example+Roulette)

ในกรณีนี้ แอปเปิ้ลหรือกล้วยมีโอกาสถูกเลือกมากกว่าเลม่อน 4 เท่า และมากกว่าสตรอเบอรี่ 8 เท่า ในรูเล็ตข้างต้น แอปเปิ้ลหรือกล้วยจะถูกเลือกก่อน จากนั้นลูกเต๋าจะถูกทอยอีกครั้งเพื่อเลือกแอปเปิ้ลหรือกล้วย แต่ละอย่างมีโอกาสถูกเลือก 50%

## โหนดต้นไม้ Loot

### ภาพรวม

ก่อนที่จะอธิบายว่าโหนดต้นไม้ loot คืออะไร มาครอบคลุมแรงจูงใจสำหรับพวกมันกันเถอะ สมมติว่าเรามีบาร์ที่สามารถเกิดไอเท็มดังต่อไปนี้:

- Beer
- Absinthe
- Chips
- Hazelnuts
- 1H_KitchenKnife

เราต้องกำหนดความน่าจะเป็นให้กับไอเท็มเพราะมันไม่มีเหตุผลที่ Hazelnuts จะมีโอกาสเกิดเท่ากับ Beer อย่างน้อยก็ไม่ใช่ในบาร์ เรามีความหายากที่ระบุความน่าจะเป็นอยู่แล้ว ดังนั้นลองทำดู:

- Beer (Abundant)
- Absinthe (Very Rare)
- Chips (Uncommon)
- Hazelnuts (Rare)
- 1H_KitchenKnife (Extremely Rare)

ปัญหาของวิธีนี้คือความน่าจะเป็นเหล่านี้มีเหตุผลในบาร์ แต่จะไม่มีเหตุผลที่อื่น ตัวอย่างเช่น หากเราต้องการเกิดไอเท็มในครัวเรือน 1H_KitchenKnife จะมีโอกาสมากกว่า Hazelnuts หรือ Absinthe นอกจากนี้ หากเรากำลังจัดการกับไอเท็มจำนวนมาก ความหายากจะไม่เพียงพอที่จะแยกแยะ หนึ่งในวิธีแก้ไขคือการใช้ตัวเลขสำหรับความน่าจะเป็นแทนความหายาก แต่สิ่งนี้อาจยุ่งยากในการปรับแต่งและการบำรุงรักษา

เราแก้ไขปัญหานี้โดยการจัดหมวดหมู่ไอเท็มและความหายากของพวกมันในรูปต้นไม้ โหนดต้นไม้แต่ละโหนดมีชื่อและความหายาก โหนดแม่หมายถึงกระเป๋าของไอเท็มที่มีลักษณะร่วมกันบางประการ ตัวอย่างเช่น โหนดแม่อาจเป็นบาร์ที่มีไอเท็มที่มีเหตุผลในบาร์ นอกจากนี้ โหนดบาร์อาจมีถุงอื่นๆ เช่น เครื่องดื่ม อาหาร และอาวุธ ซึ่งจะมีไอเท็มที่มีเหตุผลสำหรับถุงเหล่านั้น มาจัดไอเท็ม 5 อย่างข้างต้นในรูปต้นไม้:


การเลือกไอเท็มจากต้นไม้จะทำจากซ้ายไปขวาหรือในคำศัพท์ของต้นไม้ จากโหนดรากไปยังโหนดใบ ในตัวอย่างข้างต้น โหนด Bar เป็นโหนดราก ดังนั้นการเลือกจะเริ่มจากที่นั่น สิ่งต่อไปที่ต้องเลือกคือโหนดลูกของ Bar โหนด Bar มีลูกสามตัว: Drinks (Abundant), Food (Uncommon) และ Weapons (Very Rare) การเลือกโหนดลูกทำตามความหายากตามที่อธิบายในส่วนความหายาก สมมติว่าโหนด Food ถูกเลือก กระบวนการจะดำเนินต่อไปจนกว่าจะถึงโหนดใบ โหนด Food มีลูกสองตัว: Chips (Abundant) และ Hazelnuts (Common) การเลือกทำตามความหายากและสมมติว่าโหนด Chips ถูกเลือก โหนด Chips เป็นโหนดใบและการเลือกหยุดที่นั่น ผู้เล่นจะได้รับไอเท็ม Chips

กระบวนการเดียวกันนี้ใช้สำหรับต้นไม้ที่ใหญ่ที่สุด การเลือกเริ่มจากราก เลือกเด็กตามความหายาก และดำเนินต่อไปจนกว่าจะถึงโหนดใบ โหนดใบจะเป็นไอเท็มเสมอ โหนดที่มีโหนดลูกจะเป็นถุงไอเท็มเสมอ

เราจะเรียกต้นไม้ประเภทนี้ว่า ต้นไม้การลูต และโหนดภายในต้นไม้ดังกล่าวว่า โหนดต้นไม้การลูต (หรือโหนดสั้นๆ)

### รหัสโหนด

เพื่อให้โหนดถูกใช้ที่ไหนสักแห่งพวกมันต้องถูกอ้างอิงอย่างใดอย่างหนึ่ง ตัวอย่างเช่น สมมติว่าเรามีสามสถานที่ในบาร์:

- ลิ้นชักที่มีเครื่องมือครัวบาร์
- ตู้เย็นที่มีเครื่องดื่มต่างๆ
- โต๊ะบาร์ที่สามารถมีทั้งเครื่องดื่มหรืออาหาร

เราจะใช้โหนดจากส่วนก่อนหน้าเพื่อกำหนดว่าไอเท็มใดสามารถเกิดได้ในแต่ละสถานที่:

- ลิ้นชักได้โหนด Weapons
- ตู้เย็นได้โหนด Drinks
- โต๊ะได้โหนด Drinks และ Food

ในตัวอย่างข้างต้นเราใช้ชื่อโหนดเพื่อระบุว่าโหนดใดให้ไอเท็มสำหรับสถานที่ใด นี่เป็นเรื่องที่ดีสำหรับต้นไม้ที่ง่ายเช่นในตัวอย่าง แต่ปัญหาคือจริงๆ แล้วต้นไม้การลูตที่ใช้ในเกมมีขนาดใหญ่มากและชื่อโหนดไม่ต้องไม่ซ้ำกัน ตัวอย่างเช่น เราอาจมีโหนดชื่อ Weapons ทั้งในบาร์และในสถานีตำรวจ เราต้องมีวิธีที่แม่นยำกว่าในการระบุว่าโหนดใดถูกอ้างอิง นี่คือที่มาของรหัสโหนด โหนดแต่ละตัวมีรหัสที่ระบุเฉพาะมัน รหัสโหนดสำหรับโหนดเฉพาะถูกสร้างโดยการนำชื่อของโหนดบรรพบุรุษมาต่อหน้าชื่อโหนดนั้นในขณะที่แยกชื่อทั้งหมดด้วยเครื่องหมายจุด มาลองใช้รหัสแทนชื่อดิบเพื่อกำหนดโหนดให้สถานที่ข้างต้น:

- ลิ้นชักได้โหนด Bar.Weapons
- ตู้เย็นได้โหนด Bar.Drinks
- โต๊ะได้โหนด Bar.Drinks และ Bar.Food

อีกวิธีหนึ่งในการมองรหัสโหนดคือพวกมันเป็นเส้นทางในระบบไฟล์ โหนดรากและโหนดภายใน (ถุง) อาจเป็นโฟลเดอร์และโหนดใบ (ไอเท็ม) อาจเป็นไฟล์ ความแตกต่างที่สำคัญเพียงอย่างเดียวคือเครื่องหมายจุดเป็นเครื่องหมายแยกแทนที่จะเป็นเครื่องหมายทับ

### สเปค JSON

โหนดถูกระบุโดยใช้วัตถุ JSON โหนดแต่ละตัวเป็นวัตถุ JSON ที่มีสองคุณลักษณะดังนี้:

- **Name:** ชื่อของโหนด ต้องไม่ซ้ำกันในกลุ่มพี่น้อง
- **Rarity:** ความหายากของโหนด สามารถเป็นค่าหนึ่งในค่าที่ระบุในส่วนความหายาก

โหนดที่มีโหนดลูก (ถุง) มีคุณลักษณะเพิ่มเติมหนึ่งอย่าง:

- **Children:** อาร์เรย์ของวัตถุที่เป็นโหนดลูกของโหนดนี้

โหนดที่เป็นตัวแทนของไอเท็ม (โหนดใบ) สามารถมีคุณลักษณะเพิ่มเติมหนึ่งอย่าง:

- **Variations:** อาร์เรย์ของสตริง ไอเท็มบางรายการเป็นตัวแทนของไอเท็มเดียวกันทางตรรกะแต่มี "สกิน" ต่างกัน ตัวอย่างคือเสื้อผ้าชิ้นเดียวกันที่มีสีต่างกัน ไอเท็มที่เป็นตัวแปรของสีสามารถระบุในอาร์เรย์การแปรผันของโหนดไอเท็มได้ เมื่อโหนดไอเท็มที่มีการแปรผันถูกเลือก ไอเท็มที่แทนด้วยโหนดหรือหนึ่งในตัวแปรของมันจะถูกเลือกโดยใช้โอกาสเท่ากัน

นี่คือ JSON ที่จะดูเหมือนต้นไม้ในส่วนก่อนหน้า:

```json
{
  "Name": "Bar",
  "Children": [
    {
      "Name": "Drinks",
      "Rarity": "Abundant",
      "Children": [
        {
          "Name": "Beer",
          "Rarity": "Abundant"
        },
        {
          "Name": "Absinthe",
          "Rarity": "Rare"
        }
      ]
    },
    {
      "Name": "Food",
      "Rarity": "Uncommon",
      "Children": [
        {
          "Name": "Chips",
          "Rarity": "Abundant"
        },
        {
          "Name": "Hazelnuts",
          "Rarity": "Common"
        }
      ]
    },
    {
      "Name": "Weapons",
      "Rarity": "Very Rare",
      "Children": [
        {
          "Name": "1H_KitchenKnife"
        }
      ]
    }
  ]
}

