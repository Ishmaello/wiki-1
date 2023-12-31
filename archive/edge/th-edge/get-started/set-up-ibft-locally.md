---
id: set-up-ibft-locally
title: การตั้งค่าภายใน
description: "คู่มือการตั้งค่าภายในแบบเป็นขั้นตอน"
keywords:
  - docs
  - polygon
  - edge
  - local
  - setup
  - genesis
---

:::caution คู่มือนี้มีไว้สำหรับวัตถุประสงค์ในการทดสอบเท่านั้น

คู่มือด้านล่างจะแนะนำวิธีตั้งค่าเครือข่าย Polygon Edge ในอุปกรณ์ภายในของคุณเพื่อวัตถุประสงค์ในการทดสอบและพัฒนา

กระบวนการแตกต่างอย่างมากจากวิธีที่คุณต้องการตั้งค่าเครือข่าย Polygon Edge สำหรับสถานการณ์การใช้งานจริงบนผู้ให้บริการคลาวด์: **[การตั้งค่า Cloud](/docs/edge/get-started/set-up-ibft-on-the-cloud)**

:::


## ข้อกำหนด {#requirements}

ดู[การติดตั้ง](/docs/edge/get-started/installation)เพื่อติดตั้ง Polygon Edge

## ภาพรวม {#overview}

![การตั้งค่าภายใน](/img/edge/ibft-setup/local.svg)

ในคู่มือนี้ เป้าหมายของเราคือการสร้างเครือข่ายบล็อกเชน `polygon-edge` ที่ใช้งานได้และใช้ได้กับ[โปรโตคอลฉันทามติ IBFT](https://github.com/ethereum/EIPs/issues/650)เครือข่ายบล็อกเชนจะประกอบด้วยโหนดจำนวน 4 โหนด ซึ่งเป็นโหนดตัวตรวจสอบความถูกต้องทั้งหมด จึงสามารถคัดเลือกได้ทั้งเพื่อเสนอบล็อกและตรวจสอบความถูกต้องของบล็อกซึ่งรับมาจากผู้เสนออื่นๆทั้ง 4 โหนดจะทำงานบนเครื่องเดียวกัน เนื่องจากแนวคิดของคู่มือนี้คือเพื่อให้คุณมีคลัสเตอร์ IBFT ที่ทำงานได้อย่างสมบูรณ์ในระยะเวลาน้อยที่สุด

เพื่อให้บรรลุเป้าหมายนั้น เราจะให้คำแนะนำกับคุณผ่าน 4 ขั้นตอนง่ายๆ ดังต่อไปนี้:

1. การเริ่มต้นไดเรกทอรีข้อมูลจะสร้างคีย์ตัวตรวจสอบความถูกต้องทั้งสองคีย์ให้กับแต่ละโหนดจาก 4 โหนด และเริ่มต้นไดเรกทอรีข้อมูลบล็อกเชนที่ว่างเปล่าคีย์ตัวตรวจสอบความถูกต้องมีความสำคัญ เนื่องจากเราจำเป็นต้องเริ่มต้นระบบบล็อก Genesis ด้วยชุดตัวตรวจสอบความถูกต้องเริ่มต้น โดยใช้คีย์เหล่านี้
2. การเตรียมสตริงการเชื่อมต่อสำหรับบูตโหนดจะเป็นข้อมูลที่สำคัญสำหรับทุกโหนดที่เราจะเรียกใช้ ในส่วนที่ว่าจะเชื่อมต่อกับโหนดใดเมื่อเริ่มต้นครั้งแรก
3. การสร้างไฟล์ `genesis.json` จะต้องมีการใช้ทั้งคีย์ตัวตรวจสอบความถูกต้องที่สร้างขึ้นใน**ขั้นตอนที่ 1** ที่ใช้สำหรับการตั้งค่าตัวตรวจสอบความถูกต้องเริ่มต้นของเครือข่ายในบล็อก Genesis และสตริงการเชื่อมต่อบูตโหนดจาก**ขั้นตอนที่ 2** เป็นอินพุต
4. การเรียกใช้โหนดทั้งหมดเป็นเป้าหมายสุดท้ายของคู่มือนี้ และจะเป็นขั้นตอนสุดท้ายที่เราทำ เราจะแนะนำโหนดว่าจะใช้ไดเรกทอรีข้อมูลใด และตำแหน่งที่จะค้นหา `genesis.json` ซึ่งเริ่มต้นสถานะเครือข่ายเบื้องต้น

เนื่องจากทั้งสี่โหนดจะทำงานบน localhost จึงคาดว่า ในระหว่างกระบวนการตั้งค่า ไดเรกทอรีข้อมูลทั้งหมดสำหรับแต่ละโหนดจะอยู่ในไดเรกทอรีหลักเดียวกัน

:::info จำนวนตัวตรวจสอบความถูกต้อง

ไม่มีขั้นต่ำของจำนวนโหนดในคลัสเตอร์ กล่าวคือ คุณสามารถใช้คลัสเตอร์ที่ประกอบด้วยโหนดตัวตรวจสอบความถูกต้องเพียงโหนดเดียวได้โปรดจำไว้ว่า หากคุณใช้คลัสเตอร์ที่ประกอบด้วยโหนด_เดียว_ จะ**ไม่มีการทนทานต่อการหยุดทำงาน**และ**ไม่มีการรับประกัน BFT**

จำนวนโหนดขั้นต่ำที่แนะนำเพื่อให้ได้รับการรับประกันจาก BFT อยู่ที่ 4 โหนด เนื่องจากในคลัสเตอร์ที่ประกอบด้วย 4 โหนด การขัดข้องของโหนด 1 โหนดยังสามารถยอมให้เกิดขึ้นได้ โดยมีโหนดอีก 3 โหนดซึ่งใช้งานโดยปกติ

:::

## ขั้นตอนที่ 1: เริ่มต้นโฟลเดอร์ข้อมูลสำหรับ IBFT และสร้างคีย์ตัวตรวจสอบความถูกต้อง {#step-1-initialize-data-folders-for-ibft-and-generate-validator-keys}

ในการเริ่มต้นใช้งาน IBFT คุณต้องเริ่มต้นโฟลเดอร์ข้อมูลหนึ่งโฟลเดอร์สำหรับแต่ละโหนด:

````bash
polygon-edge secrets init --data-dir test-chain-1
````

````bash
polygon-edge secrets init --data-dir test-chain-2
````

````bash
polygon-edge secrets init --data-dir test-chain-3
````

````bash
polygon-edge secrets init --data-dir test-chain-4
````

คำสั่งเหล่านี้แต่ละคำสั่งจะพิมพ์คีย์ตัวตรวจสอบความถูกต้อง คีย์สาธารณะ bls และ [ID โหนด](https://docs.libp2p.io/concepts/peer-id/) คุณจะต้องใช้ ID โหนดสำหรับโหนดรายแรกเพื่อสามารถดำเนินตามขั้นตอนถัดไป

### เอารหัสออก {#outputting-secrets}
สามารถเรียกเก็บเอาต์พุตของลับได้อีกหากจำเป็น

```bash
polygon-edge secrets output --data-dir test-chain-4
```

## ขั้นตอนที่ 2: เตรียมสตริงการเชื่อมต่อ multiaddr สำหรับบูตโหนด {#step-2-prepare-the-multiaddr-connection-string-for-the-bootnode}

เพื่อให้โหนดสร้างการเชื่อมต่อได้สำเร็จ จะต้องรู้ว่าจะเชื่อมต่อกับเซิร์ฟเวอร์ `bootnode` ใดเพื่อให้ได้ให้ได้ข้อมูลเกี่ยวกับโหนดทั้งหมดที่เหลืออยู่ในเครือข่าย `bootnode` ซึ่งบางที่เรียกว่าเซิร์ฟเวอร์ `rendezvous` ตามคำศัพท์พิเศษของ p2p

`bootnode`ไม่ใช่ตัวสำรองของ โหนด Polygon - edgeโหนด Polygon-edge ทุกโหนด สามารถใช้เป็น`bootnode`โหนด แต่ทุกโหนด Polygon Edge ต้องมีชุดบูตโหนดที่ระบุ ซึ่งจะได้รับการติดต่อเพื่อให้ข้อมูลเกี่ยวกับวิธีเชื่อมต่อโหนดทั้งหมดที่เหลืออยู่ในเครือข่าย

เพื่อสร้างสตริงการเชื่อมต่อเพื่อระบุบูตโหนด เราจะต้องใช้[รูปแบบ multaddr](https://docs.libp2p.io/concepts/addressing/):
```
/ip4/<ip_address>/tcp/<port>/p2p/<node_id>
```

ในคู่มือนี้ เราจะใช้โหนดแรกและโหนดที่สองเป็นบูตโหนดสำหรับโหนดที่เหลือทั้งหมดสิ่งที่เกิดขึ้นในสถานการณ์นี้คือบรรดาโหนดที่เชื่อมต่อกับ `node 1` หรือ `node 2` จะได้รับข้อมูลเกี่ยวกับวิธีการเชื่อมต่อกันและกันผ่านการใช้บูตโหนดที่ได้ติดต่อร่วมกัน

:::info คุณต้องระบุบูตโหนดอย่างน้อยหนึ่งโหนดเพื่อเริ่มใช้โหนด

ต้องมีบูตโหนดอย่างน้อย**หนึ่ง**โหนด เพื่อทำให้โหนดอื่นๆ ในเครือข่ายสามารถค้นหากันได้แนะนำให้ใช้บูตโหนดมากขึ้น เนื่องจากให้ความสามารถในการฟื้นฟูกับเครือข่ายในกรณีที่เกิดการขัดข้องในคู่มือนี้ เราจะระบุสองโหนด แต่คุณสามารถทำการเปลี่ยนแปลงได้ในระหว่างการดำเนินการ โดยจะไม่ส่งผลกระทบต่อความสมบูรณ์ของไฟล์ `genesis.json`

:::

เนื่องจากเราทำงานบน localhost เราจึงคาดเดาได้ว่า `<ip_address>` คือ `127.0.0.1`

สำหรับ `<port>` เราจะใช้ `10001` เนื่องจากเราจะกำหนดค่าเซิร์ฟเวอร์ libp2p สำหรับ `node 1` เพื่อรอรับคำสั่งในพอร์ตนี้ในภายหลัง

สุดท้ายแล้ว เราต้องการ `<node_id>` ซึ่งเราสามารถได้รับจากเอาต์พุตคำสั่งที่เรียกใช้มาก่อน ได้แก่ คำสั่ง `polygon-edge secrets init --data-dir test-chain-1` (ซึ่งเราได้ใช้เพื่อสร้างคีย์และไดเรกทอรีข้อมูลสำหรับ `node1`)

หลังจากมีการประกอบแล้ว สตริงการเชื่อมต่อ multiaddr ซึ่งใช้กับ `node 1` ที่เราจะใช้เป็นบูตโหนด จะมีลักษณะเช่นนี้ (เพียงแต่ `<node_id>` ในปลายทางต้องมีค่าอื่น):
```
/ip4/127.0.0.1/tcp/10001/p2p/16Uiu2HAmJxxH1tScDX2rLGSU9exnuvZKNM9SoK3v315azp68DLPW
```
ในทางเดียวกัน เราสร้าง multiaddr สำหรับบูตโหนดที่สอง ตามที่ระบุไว้ด้านล่าง
```
/ip4/127.0.0.1/tcp/20001/p2p/16Uiu2HAmS9Nq4QAaEiogE4ieJFUYsoH28magT7wSvJPpfUGBj3Hq
```

:::info ชื่อโฮสต์ DNS ที่ใช้แทน IP

Polygon Edge รองรับการใช้ชื่อโฮสต์ DNS สำหรับการกำหนดค่าโหนดนี่เป็นคุณสมบัติที่มีประโยชน์มากสำหรับการปรับใช้ในระบบคลาวด์ เนื่องจาก IP ของโหนดอาจเปลี่ยนได้ด้วยหลายสาเหตุ

รูปแบบ multiaddr ซึ่งใช้สำหรับสตริงการเชื่อมต่อในระหว่างการใช้ชื่อโฮสต์ DNS ตามที่ปรากฏไว้ดังต่อไปนี้:`/dns4/sample.hostname.com/tcp/<port>/p2p/nodeid`

:::


## ขั้นตอนที่ 3: สร้างไฟล์ genesis ที่มีโหนดจำนวน 4 โหนดเป็นตัวตรวจสอบความถูกต้อง {#step-3-generate-the-genesis-file-with-the-4-nodes-as-validators}

````bash
polygon-edge genesis --consensus ibft --ibft-validators-prefix-path test-chain- --bootnode /ip4/127.0.0.1/tcp/10001/p2p/16Uiu2HAmJxxH1tScDX2rLGSU9exnuvZKNM9SoK3v315azp68DLPW --bootnode /ip4/127.0.0.1/tcp/20001/p2p/16Uiu2HAmS9Nq4QAaEiogE4ieJFUYsoH28magT7wSvJPpfUGBj3Hq
````

สิ่งที่คำสั่งนี้ทำ:

* `--ibft-validators-prefix-path` กำหนดพาธโฟลเดอร์นำหน้าไปยังพาธที่ระบุ ซึ่ง IBFT ใน Polygon Edge สามารถใช้งานได้ใช้ไดเรกทอรีนี้เพื่อสร้างโฟลเดอร์ `consensus/` ซึ่งเก็บคีย์ส่วนตัวของตัวตรวจสอบความถูกต้องไว้ ต้องใช้คีย์สาธารณะของตัวตรวจสอบความถูกต้องเพื่อสร้างไฟล์ Genesis ซึ่งเป็นรายการเริ่มต้นของโหนดการเริ่มต้นระบบค่าสถานะนี้ใช้ได้ก็ต่อเมื่อตั้งค่าเครือข่ายบน localhost เนื่องจากในสถานการณ์จริง เราไม่สามารถคาดหวังว่าไดเรกทอรีข้อมูลทั้งหมดของโหนดจะอยู่ในระบบไฟล์เดียวกัน ซึ่งเราสามารถอ่านคีย์สาธารณะได้อย่างง่ายดาย
*  `--bootnode` กำหนดที่อยู่ของบูตโหนด ซึ่งจะทำให้โหนดสามารถค้นหากันเองได้
เราจะใช้สตริง multiaddr ของ `node 1` ตามที่กล่าวถึงใน**ขั้นตอนที่ 2**

ผลลัพธ์ของคำสั่งนี้คือไฟล์ `genesis.json` ซึ่งมีบล็อก Genesis ของบล็อกเชนใหม่ของเรา พร้อมด้วยชุดตัวตรวจสอบความถูกต้องที่กำหนดไว้ล่วงหน้า และการกำหนดค่าสำหรับโหนดที่จะติดต่อก่อนเพื่อสร้างการเชื่อมต่อ

:::info สลับไปยัง ECDSA

BLS คือโหมดการตรวจสอบความถูกต้องเริ่มต้นของตัวต่อเนื่องของบล็อกหากคุณต้องการให้เชนของคุณทำงานในโหมด ECDSA คุณสามารถใช้`—ibft-validator-type`แฟล็ก ด้วยอาร์กิวเมนต์`ecdsa`:

```
genesis --ibft-validator-type ecdsa
```
:::
:::info บัญชียอดคงเหลือแบบ Premining

คุณอาจต้องการตั้งค่าเครือข่ายบล็อกเชนของคุณโดยให้ที่อยู่บางส่วนมียอดคงเหลือ "ที่วางไว้ล่วงหน้า"

ในการดำเนินการนี้ ให้ส่งผ่านค่าสถานะ `--premine` ต่างๆ ตามที่คุณต้องการไปยังแต่ละที่อยู่ที่คุณต้องการให้เริ่มต้นด้วยยอดคงเหลือที่กำหนดในบล็อกเชน

ตัวอย่างเช่น หากเราต้องการวาง 1000 ETH ไว้ล่วงหน้าให้กับที่อยู่ `0x3956E90e632AEbBF34DEB49b71c28A83Bc029862` ในบล็อก genesis ของเรา เราจึงต้องเสนออาร์กิวเมนต์ดังต่อไปนี้:

```
--premine=0x3956E90e632AEbBF34DEB49b71c28A83Bc029862:1000000000000000000000
```

**โปรดทราบว่าจำนวนที่วางไว้ล่วงหน้า มีหน่วยเป็น WEI ไม่ใช่ ETH**

:::

:::info กำหนดขีดจำกัดค่าแก๊สต่อบล็อก

ขีดจำกัดค่าแก๊สเริ่มต้นในแต่ละบล็อกอยู่ที่ `5242880`มีการเขียนค่านี้เขียนไว้ในไฟล์ genesis แต่คุณอาจต้องการเพิ่ม / ลดค่านั้น

เพื่อดำเนินการเช่นนั้น คุณสามารถใช้ค่าสถานะ `--block-gas-limit` ตามด้วยค่าที่จำเป็นต้องใช้ ตามที่ระบุไว้ด้านล่าง :

```shell
--block-gas-limit 1000000000
```
:::

:::info กำหนดขีดจำกัดของ File Descriptor ของระบบ

ขีดจำกัดตัวอธิบายของไฟล์ปริยาย (จำนวนสูงสุดของไฟล์ที่เปิด) สามารถใช้ได้ต่ำและบน Linux ทุกอย่างคือไฟล์หากคาดว่าจะมีโหนดที่มีแบบผ่านมาตรฐาน คุณสามารถพิจารณาเพิ่มขีดจำกัดนี้ได้ตรวจสอบเอกสารอย่างเป็นทางการของเลเวโดของคุณเพื่อหารายละเอียดเพิ่มเติม

#### ตรวจสอบขีดจำกัดปัจจุบันของระบบปฏิบัติการ (ไฟล์ที่เปิดอยู่) {#check-current-os-limits-open-files}
```shell title="ulimit -n"
1024 # Ubuntu default
```

#### เพิ่มขีดจำกัดสำหรับไฟล์ที่เปิดอยู่ {#increase-open-files-limit}
- วิ่ง`polygon-edge`บนหน้าผา (shel)
  ```shell title="Set FD limit for the current session"
  ulimit -n 65535 # affects only current session, limit won't persist after logging out
  ```

  ```shell title="Edit /etc/security/limits.conf"
  # add the following lines to the end of the file to modify FD limits
  *               soft    nofile          65535 # sets FD soft limit for all users
  *               hard    nofile          65535 # sets FD hard limit for all users

  # End of file
  ```
บันทึกไฟล์และรีสตาร์ทระบบด้วย

- การใช้งาน`polygon-edge`ในพื้นหลังเป็นบริการ

หาก`polygon-edge`ทำงานเป็นบริการระบบโดยใช้เครื่องมือเช่น ขีดจำกัดตัวอธิบาย`systemd`ไฟล์Nameควรมีการจัดการโดยใช้`systemd`ระบบ
  ```shell title="Edit /etc/systemd/system/polygon-edge.service"
  [Service]
   ...
  LimitNOFILE=65535
  ```

### การแก้ไขปัญหา {#troubleshooting}
```shell title="Watch FD limits of polygon edge running process"
watch -n 1 "ls /proc/$(pidof polygon-edge)/fd | wc -l"
```

```shell title="Check max FD limits for polygon-edge running process"
cat /proc/$(pidof polygon-edge)/limits
```
:::


## ขั้นตอนที่ 4: เรียกใช้ไคลเอ็นต์ทั้งหมด {#step-4-run-all-the-clients}

เนื่องจากเรากำลังพยายามเรียกใช้เครือข่าย Polygon Edge ที่ประกอบด้วยโหนด 4 โหนดที่ต่างอยู่ในเครื่องเดียวกัน เราจึงต้องระมัดระวังเพื่อหลีกเลี่ยงปัญหาพอร์ตที่ขัดแย้งกันนี่คือเหตุผลที่เราจะใช้การให้เหตุผลต่อไปนี้ในการกำหนดพอร์ตรอรับการสื่อสารของแต่ละเซิร์ฟเวอร์ของโหนด:

- `10000` สำหรับเซิร์ฟเวอร์ gRPC ของ `node 1`, `20000` สำหรับเซิร์ฟเวอร์ GRPC ของ `node 2` เป็นต้น
- `10001` สำหรับเซิร์ฟเวอร์ libp2p ของ `node 1`, `20001` สำหรับเซิร์ฟเวอร์ libp2p ของ `node 2` เป็นต้น
- `10002` สำหรับเซิร์ฟเวอร์ JSON-RPC ของ `node 1`, `20002` สำหรับเซิร์ฟเวอร์ JSON-RPC ของ `node 2` เป็นต้น

ในการเรียกใช้ไคลเอ็นต์**แรก** (บันทึกพอร์ต `10001` ไว้ เนื่องจากจะมีการใช้เป็นส่วนหนึ่งของ libp2p multiaddr ใน**ขั้นตอนที่ 2** ควบคู่ไปกับ ID โหนดของ node 1:

````bash
polygon-edge server --data-dir ./test-chain-1 --chain genesis.json --grpc-address :10000 --libp2p :10001 --jsonrpc :10002 --seal
````

ในการเรียกใช้ไคลเอ็นต์**ที่สอง**:

````bash
polygon-edge server --data-dir ./test-chain-2 --chain genesis.json --grpc-address :20000 --libp2p :20001 --jsonrpc :20002 --seal
````

ในการเรียกใช้ไคลเอ็นต์**ที่สาม**:

````bash
polygon-edge server --data-dir ./test-chain-3 --chain genesis.json --grpc-address :30000 --libp2p :30001 --jsonrpc :30002 --seal
````

ในการเรียกใช้ไคลเอ็นต์**ที่สี่**:

````bash
polygon-edge server --data-dir ./test-chain-4 --chain genesis.json --grpc-address :40000 --libp2p :40001 --jsonrpc :40002 --seal
````

เพื่อสรุปสิ่งที่ได้ทำไปแล้วโดยสังเขป:

* ไดเรกทอรีสำหรับข้อมูลไคลเอ็นต์ได้รับการระบุเป็น **./test-chain-\***
* เซิร์ฟเวอร์ GRPC เริ่มทำงานบนพอร์ต **10000**, **20000**, **30000** และ **40000** สำหรับแต่ละโหนดตามลำดับ
* เซิร์ฟเวอร์ libp2p เริ่มทำงานบนพอร์ต **10001**, **20001**, **30001** และ **40001** สำหรับแต่ละโหนดตามลำดับ
* เซิร์ฟเวอร์ JSON-RPC เริ่มทำงานบนพอร์ต **10002**, **20002**, **30002** และ **40002** สำหรับแต่ละโหนดตามลำดับ
* ค่าสถานะ *seal* หมายความว่าโหนดที่กำลังเริ่มต้นกำลังจะเข้าร่วมในการซีลบล็อก
* ค่าสถานะ *chain* ระบุว่าควรใช้ไฟล์ Genesis ใดสำหรับการกำหนดค่าเชน

เนื้อหาเกี่ยวกับโครงสร้างของไฟล์ genesis อยู่ในส่วน[คำสั่ง CLI](/docs/edge/get-started/cli-commands)

หลังจากเรียกใช้คำสั่งก่อนหน้าหน้า คุณได้กำหนดเครือข่าย Polygon Edge ซึ่งประกอบด้วย 4 โหนด ที่สามารถซีลบล็อกและคืนค่า
จากกรณีโหนดล้มเหลว

:::info เริ่มใช้ไคลเอ็นต์โดยใช้ไฟล์กำหนดค่า

แทนที่จะระบุพารามิเตอร์การกำหนดค่าทั้งหมดเป็นอาร์กิวเมนต์ CLI เรายังสามารถเริ่มต้นไคลเอ็นต์ผ่านการใช้ไฟล์กำหนดค่า โดยให้เรียกใช้คำสั่งดังต่อไปนี้:

````bash
polygon-edge server --config <config_file_path>
````
ตัวอย่าง:

````bash
polygon-edge server --config ./test/config-node1.json
````
ปัจจุบัน เรารองรับ`yaml`และไฟล์การตั้งค่า`json`ตามฐาน สามารถค้นหาไฟล์การตั้งค่าตัวอย่าง **[ได้ที่นี่](/docs/edge/configuration/sample-config)**

:::

:::info ขั้นต่างๆ ในการเรียกใช้โหนดที่ไม่ใช่โหนดผู้ตรวจสอบ

โหนดที่ไม่ใช่โหนดตัวตรวจสอบความถูกต้องจะซิงค์บรรดาบล็อกล่าสุดที่ได้รับมาจากโหนดตัวตรวจสอบความถูกต้อง คุณสามารถเริ่มโหนดที่ไม่ใช่โหนดตัวตรวจสอบความถูกต้องโดยเรียกใช้คำสั่งดังต่อไปนี้

````bash
polygon-edge server --data-dir <directory_path> --chain <genesis_filename> --grpc-address <portNo> --libp2p <portNo> --jsonrpc <portNo>
````
ตัวอย่างเช่น คุณสามารถเพิ่มไคลเอ็นต์ที่ไม่ใช่ไคลเอ็นต์ตัวตรวจสอบความถูกต้องตัว**ที่ห้า**ได้ โดยการเรียกใช้คำสั่งดังต่อไปนี้ :

````bash
polygon-edge server --data-dir ./test-chain --chain genesis.json --grpc-address :50000 --libp2p :50001 --jsonrpc :50002
````
:::

:::info กำหนดขีดจำกัดราคา
เริ่มต้นโหนด Polygon Edge ได้ด้วย**ขีดจำกัดราคา**ที่กำหนดค่าไว้สำหรับธุรกรรมที่เข้ามา

หน่วยที่ใช้สำหรับขีดจำกัดราคาคือ `wei`

การกำหนดขีดจำกัดราคาหมายความว่า ธุรกรรมใดๆ ที่ดำเนินการโดยโหนดปัจจุบันจะต้องมีราคาแก๊ส**สูงกว่า**ขีดจำกัดราคาที่กำหนดไว้ มิฉะนั้นจะไม่รวมอยู่ในบล็อก

การที่โหนดส่วนใหญ่ยอมรับขีดจำกัดราคาที่กำหนดจะทำให้เกิดบังคับใช้กฎที่ระบุว่าธุรกรรมในเครือข่ายต้องไม่ต่ำกว่าขีดจำกัดราคาที่กำหนด

ค่าเริ่มต้นสำหรับขีดจำกัดราคาคือ `0` หมายความว่า ตามค่าเริ่มต้น จะไม่มีการบังคับใช้เลย

ตัวอย่างการใช้ค่าสถานะ `--price-limit`:
````bash
polygon-edge server --price-limit 100000 ...
````

โปรดจำไว้ว่า ขีดจำกัดราคาจะ**ได้รับการบังคับใช้กับธุรกรรมที่ไม่ใช่ธุรกรรมภายในเท่านั้น** หมายความว่าขีดจำกัดราคาจะไม่นำมาใช้กับธุรกรรมที่เพิ่มเป็นการภายในกับโหนด
:::

:::info WebSocket URL
ตามค่าเริ่มต้น เมื่อคุณเรียกใช้ Polygon Edge จะมีการสร้าง WebSocket URL โดยใช้ที่ตั้งของเชนเป็นหลักใช้โครงสร้าง URL `wss://` ใช้กับลิงก์ HTTPS และ `ws://` สำหรับ HTTP

Localhost WebSocket URL:
````bash
ws://localhost:10002/ws
````
โปรดจำไว้ว่าหมายเลขพอร์ตขึ้นอยู่กับพอร์ต JSON-RPC ที่คุณได้เลือกไว้สำหรับโหนดดังกล่าว

Edgenet WebSocket URL:
````bash
wss://rpc-edgenet.polygon.technology/ws
````
:::



## ขั้นตอนที่ 5: โต้ตอบกับเครือข่าย polygon-edge {#step-5-interact-with-the-polygon-edge-network}

คุณได้ตั้งค่าไคลเอ็นต์ที่ทำงานอยู่ไว้อย่างน้อย 1 ไคลเอ็นต์ ตอนนี้คุณสามารถดำเนินการต่อและโต้ตอบกับบล็อกเชนโดยใช้บัญชีที่คุณวางไว้ล่วงหน้าข้างต้นและโดยระบุ JSON-RPC URL ไปยังโหนดใดโหนดหนึ่งจาก 4 โหนด:
- Node 1: `http://localhost:10002`
- Node 2: `http://localhost:20002`
- Node 3: `http://localhost:30002`
- Node 4: `http://localhost:40002`

ปฏิบัติตามคู่มือนี้เพื่อออกคำสั่งสำหรับตัวดำเนินการไปยังคลัสเตอร์ที่สร้างขึ้นใหม่: [วิธีค้นหาข้อมูลตัวดำเนินการ](/docs/edge/working-with-node/query-operator-info) (พอร์ต GRPC สำหรับคลัสเตอร์ที่เราสร้างคือ `10000`/`20000`/`30000`/`40000` สำหรับแต่ละโหนด ตามลำดับ)
