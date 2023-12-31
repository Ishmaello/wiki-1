---
id: set-up-hashicorp-vault
title: ตั้งค่า Hashicorp Vault
description: "ตั้งค่า Hashicorp Vault ให้กับ Polygon Edge"
keywords:
  - docs
  - polygon
  - edge
  - hashicorp
  - vault
  - secrets
  - manager
---

## ภาพรวม {#overview}

ปัจจุบันนี้ Polygon Edge มีความเชื่อมโยงกับการเก็บรักษาข้อมูลลับเกี่ยวกับรันไทม์ที่สำคัญอยู่ 2 ส่วน:
*  **คีย์ส่วนตัวของตัวตรวจสอบความถูกต้อง**ที่โหนดใช้งาน หากโหนดนั้นเป็นตัวตรวจสอบความถูกต้อง
*  **คีย์ส่วนตัวสำหรับการดำเนินการในเครือข่าย**ที่ libp2p ใช้งาน เพื่อเข้าร่วมและสื่อสารกับเพียร์อื่นๆ

:::warning
คีย์ส่วนตัวของตัวตรวจสอบความถูกต้องเป็นคีย์เฉพาะสำหรับโหนดตัวตรวจสอบความถูกต้องแต่ละโหนดจะ<b>ไม่</b>มีการแชร์คีย์เดียวกันให้กับตัวตรวจสอบความถูกต้องทั้งหมด เนื่องจากอาจส่งผลต่อความปลอดภัยของเชนของคุณ
:::

โปรดอ่านข้อมูลเพิ่มเติมที่[คู่มือเกี่ยวกับการจัดการคีย์ส่วนตัว](/docs/edge/configuration/manage-private-keys)

บรรดาโมดูลของ Polygon Edge **ไม่จำเป็นต้องรู้ถึงวิธีการเก็บรักษาข้อมูลลับ**และในท้ายที่สุด โมดูลไม่จำเป็นต้องสนใจว่ามีการเก็บข้อมูลลับไว้ในเซิร์ฟเวอร์ที่ตั้งอยู่ไกลออกไปหรือภายในดิสก์ของโหนด

สิ่งเดียวที่โมดูลต้องทราบเกี่ยวกับการเก็บข้อมูลลับก็คือ **ความรู้เกี่ยวกับการใช้ข้อมูลลับ** กล่าวคือ **ทราบว่าข้อมูลลับใดที่ต้องรับหรือบันทึก**รายละเอียดเกี่ยวกับการนำไปใช้อย่างละเอียดกับการดำเนินการเหล่านี้ต้องได้รับการ Delegate ไปยัง `SecretsManager` ซึ่งแน่นอนว่าคือ Abstraction

ตอนนี้ตัวดำเนินการโหนดที่เริ่มใช้ Polygon Edge สามารถระบุตัวจัดการข้อมูลลับที่ต้องการและเมื่อ
มีการสร้างอินสแตนซ์ของตัวจัดการข้อมูลลับที่ถูกต้อง โมดูลต่างๆ สามารถดำเนินการกับข้อมูลลับนั้นผ่านอินเทอร์เฟซที่กล่าวถึงโดยไม่ใส่ใจว่า มีการเก็บข้อมูลลับไว้ในดิสก์หรือเซิร์ฟเวอร์

บทความนี้ให้รายละเอียดขั้นตอนที่จำเป็นในการศึกษาเกี่ยวกับ Polygon Edge และใช้งานกับเซิร์ฟเวอร์ [Hashicorp Vault](https://www.vaultproject.io/)

:::info คู่มือก่อนหน้านี้
ก่อนอ่านบทความนี้ เรา**ขอแนะนำ**ให้คุณอ่านบทความต่างๆ ใน[**การตั้งค่าภายใน**](/docs/edge/get-started/set-up-ibft-locally)ตลอดจนบทความ[**การตั้งค่าในระบบคลาวด์**](/docs/edge/get-started/set-up-ibft-on-the-cloud)
:::


## ข้อกำหนดเบื้องต้น {#prerequisites}

บทความนี้ถือว่า **มีการตั้งค่า**อินสแตนซ์ด้านฟังก์ชันการทำงานของเซิร์ฟเวอร์ Hashicorp Vault แล้ว

นอกจากนี้ เซิร์ฟเวอร์ Hashicorp Vault ที่ใช้งานกับ Polygon Edge ต้อง**เปิดใช้งานที่เก็บข้อมูล KV** ก่อน

ข้อมูลที่จำเป็นต้องมีก่อนดำเนินการต่อไป มีดังต่อไปนี้:
* **URL ของเซิร์ฟเวอร์** (API URL ของเซิร์ฟเวอร์ Hashicorp Vault)
* **โทเค็น** (โทเค็นที่ใช้ในการเข้าถึงเครื่องพื้นที่จัดเก็บ KV)

## ขั้นตอนที่ 1 - สร้างค่ากำหนดของตัวจัดการข้อมูลลับ {#step-1-generate-the-secrets-manager-configuration}

เพื่อให้ Polygon Edge สามารถสื่อสารกับเซิร์ฟเวอร์ Vault ได้อย่างราบรื่นไม่มีสะดุด จำเป็นต้องแยกวิเคราะห์
ไฟล์กำหนดค่าที่สร้างมาแล้ว ที่มีข้อมูลที่จำเป็นทั้งหมดสำหรับพื้นที่จัดเก็บข้อมูลลับในเซิร์ฟเวอร์ Vault

เพื่อสร้างค่ากำหนดนั้น ให้เรียกใช้คำสั่งดังต่อไปนี้:

```bash
polygon-edge secrets generate --dir <PATH> --token <TOKEN> --server-url <SERVER_URL> --name <NODE_NAME>
```

พารามิเตอร์แสดงถึง:
* `PATH` เป็นพาธซึ่งเป็นปลายทางในการส่งออกไฟล์กำหนดค่า`./secretsManagerConfig.json` ที่เป็นค่าเริ่มต้น
* `TOKEN` เป็นโทเค็นซึ่งใช้ในการเข้าถึง ตามที่ระบุไว้แล้วในส่วน[ข้อกำหนดเบื้องต้น](/docs/edge/configuration/secret-managers/set-up-hashicorp-vault#prerequisites)
* `SERVER_URL` เป็น URL ของ API สำหรับเซิร์ฟเวอร์ Vault ตามที่ระบุไว้ในส่วน[ข้อกำหนดเบื้องต้น](/docs/edge/configuration/secret-managers/set-up-hashicorp-vault#prerequisites)ด้วย
* `NODE_NAME` คือชื่อของโหนดปัจจุบันที่มีการตั้งค่าการกำหนดค่า Vault ให้ซึ่งสามารถมีค่าแบบกำหนดเอง`polygon-edge-node` ที่เป็นค่าเริ่มต้น

:::caution ชื่อโหนด

โปรดระวังเมื่อระบุชื่อโหนด

Polygon Edge ใช้ชื่อโหนดเฉพาะเพื่อติดตามข้อมูลลับซึ่งโหนดสร้างและใช้ในอินสแตนซ์ของ Vaultการระบุชื่อโหนดที่มีอยู่อาจส่งผลให้เกิดการเขียนทับข้อมูลในเซิร์ฟเวอร์ Vault

เก็บข้อมูลลับไว้ในพาธหลักนี้: `secrets/node_name`
:::

## ขั้นตอนที่ 2 - เริ่มใช้คีย์ลับโดยใช้ค่ากำหนด {#step-2-initialize-secret-keys-using-the-configuration}

ตอนนี้ เมื่อเรามีไฟล์กำหนดค่าแล้ว เราสามารถเริ่มใช้คีย์ลับที่จำเป็นกับไฟล์กำหนดค่าที่ตั้งค่าไว้ในขั้นตอนที่ 1 โดยใช้ `--config`:

```bash
polygon-edge secrets init --config <PATH>
```

พารามิเตอร์ `PATH` เป็นที่ตั้งของพารามิเตอร์ตัวจัดการข้อมูลลับที่สร้างขึ้นก่อนหน้านี้ในขั้นตอนที่ 1

## ขั้นตอนที่ 3 - สร้างไฟล์ Genesis {#step-3-generate-the-genesis-file}

ควรสร้างไฟล์ Genesis ในลักษณะเดียวกันกับคู่มือ[**การตั้งค่าภายใน**](/docs/edge/get-started/set-up-ibft-locally)และ[**การตั้งค่าในระบบคลาวด์**](/docs/edge/get-started/set-up-ibft-on-the-cloud) พร้อมกับการแก้ไขเพิ่มเติมเล็กน้อย

เนื่องจากมีการใช้ Hashicorp Vault แทนระบบไฟล์ภายใน จึงควรเพิ่มที่อยู่ของตัวตรวจสอบความถูกต้องผ่านค่าสถานะ `--ibft-validator`:
```bash
polygon-edge genesis --ibft-validator <VALIDATOR_ADDRESS> ...
```

## ขั้นตอนที่ 4 - เริ่มใช้ไคลเอ็นต์ของ Polygon Edge {#step-4-start-the-polygon-edge-client}

ตอนนี้ เมื่อมีการกำหนดคีย์และสร้างไฟล์ Genesis แล้ว ขั้นตอนสุดท้ายของกระบวนการนี้คือ การเริ่มต้นPolygon Edge โดยใช้คำสั่ง `server`

ใช้คำสั่ง`server`  ในลักษณะเดียวกันกับที่ได้ระบุไว้ในคู่มือที่กล่าวถึงก่อนหน้านี้ โดยมีการเพิ่มเติมเล็กน้อย ได้แก่ ค่าสถานะ `--secrets-config`:
```bash
polygon-edge server --secrets-config <PATH> ...
```

พารามิเตอร์ `PATH` เป็นที่ตั้งของพารามิเตอร์ตัวจัดการข้อมูลลับที่สร้างขึ้นก่อนหน้านี้ในขั้นตอนที่ 1