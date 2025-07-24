# RESTful API for SMSFAT
#### Table of contents
* [Base URL](#base-url)
* [Constructing the Request Header](#constructing-the-request-header)
* [Endpoints](#endpoints)
* [ตัวอย่าง Request/Response](#ตัวอย่าง-requestresponse)
* [Error Codes](#error-codes)

## Base URL
- The base URL is: https://smsfat.com

## Constructing the Request Header
ทุกคำขอที่ต้องการการตรวจสอบสิทธิ์จำเป็นต้องส่ง **API Key** ผ่าน Header

### **ตัวอย่าง Request Header**
```http
Accept: application/json
Authorization: Bearer {API_KEY}
Content-Type: application/json
```

## Endpoints
ทุก Endpoint ที่มีความปลอดภัยจำเป็นต้องมีการตรวจสอบสิทธิ์ (**Authentication**)

### **Secure Endpoints V1**
🔒 _ต้องมีการตรวจสอบสิทธิ์_

| Method | Endpoint | คำอธิบาย | พารามิเตอร์ที่ต้องส่ง |
|--------|---------|-----------|------------------|
| `GET` | [`/api/v1/open/my/balance`](#get-apiv1openmybalance) | ใช้สำหรับรับค่าเครดิตคงเหลือปัจจุบัน | ไม่มีพารามิเตอร์ |
| `GET` | [`/api/v1/open/my/sender`](#get-apiv1openmysender) | ดึงรายการเซ็นเดอร์ | ไม่มีพารามิเตอร์ |
| `POST` | [`/api/v1/open/sms/campaign/send`](#post-apiv1opensmscampaignsend) | ส่งข้อความแคมเปญ | `sendmessageName`, `message`, `phonelist`, `schedule`, `senderId` |
| `POST` | [`/api/v1/open/sms/otp/send`](#post-apiv1opensmsotpsend) | ส่งข้อความ OTP | `target`, `senderId (optional)` |
| `POST` | [`/api/v1/open/sms/otp/verify`](#post-apiv1opensmsotpverify) | ยืนยัน OTP | `target`, `otpCode`, `otpRef` |

## ตัวอย่าง Request/Response

### **GET /api/v1/open/my/balance**
**Request:**
```http
GET /api/v1/open/my/balance HTTP/1.1
Host: smsfat.com
Accept: application/json
Authorization: Bearer {API_KEY}
Content-Type: application/json
```

**Response:**
```json
{
    "code": 1000,
    "error": "",
    "message": "",
    "data": {
        "balance": 100
    }
}
```

### **GET /api/v1/open/my/sender**
**Request:**
```http
GET /api/v1/open/my/sender HTTP/1.1
Host: smsfat.com
Accept: application/json
Authorization: Bearer {API_KEY}
Content-Type: application/json
```

**Response:**
```json
{
    "code": 1000,
    "error": "",
    "message": "",
    "data": [
        {
            "type": "OTP",
            "status": "APPROVE",
            "senderId": "00000000-0000-0000-0000-000000000000",
            "senderName": "sample"
        }
    ]
}
```

### **POST /api/v1/open/sms/campaign/send**
**Request:**
```http
POST /api/v1/open/sms/campaign/send HTTP/1.1
Host: smsfat.com
Accept: application/json
Authorization: Bearer {API_KEY}
Content-Type: application/json

{
    "sendmessageName": "ชื่อแคมเปญ",
    "message": "นี่คือข้อความทดสอบ",
    "phonelist": "065XXXXXX1\n065XXXXXX2\n065XXXXXX3\n065XXXXXX4", // สูงสุด 50000 เบอร์
    "schedule": "2025-02-02T22:12:36.147Z", // ตั้งเวลาที่ต้องการส่ง หากต้องการส่งทันทีให้ใช้เป็นเวลาปัจจุบัน (new Date())
    "senderId": "00000000-0000-0000-0000-000000000000" // ใช้เซ็นเดอร์ที่เป็นประเภท MARKETING เท่านั้น
}
```

**Response:**
```json
{
    "code": 1000,
    "error": "",
    "message": "สำเร็จ",
    "data": { /* ข้อมูลของแคมเปญที่สร้าง */ }
}
```

### **POST /api/v1/open/sms/otp/send**
**Request:**
```http
POST /api/v1/open/sms/otp/send HTTP/1.1
Host: smsfat.com
Accept: application/json
Authorization: Bearer {API_KEY}
Content-Type: application/json

{   
    "target": "065XXXXXXX",
    "senderId": "00000000-0000-0000-0000-000000000000" // type OTP only | senderId เป็น OPTIONAL หรือไม่จำเป็นต้องแนบไปกับ payload ก็ได้ ไอดีของเซ็นเดอร์ใช้ในกรณีที่ไม่ต้องการส่งผ่านเซ็นเดอร์ค่าเริ่มต้นของระบบหรือต้องการใช้เซ็นเดอร์ของตัวเอง
}
```

**Response:**
```json
{
    "code": 1000,
    "error": "",
    "message": "สำเร็จ",
    "data": {
        "ref": "AAAA"
    }
}
```

### **POST /api/v1/open/sms/otp/verify**
**Request:**
```http
POST /api/v1/open/sms/otp/verify HTTP/1.1
Host: smsfat.com
Accept: application/json
Authorization: Bearer {API_KEY}
Content-Type: application/json

{   
    "target": "065XXXXXXX",
    "otpCode": "123456" ,
    "otpRef": "AAAA"
}
```

**Response:**
```json
{
    "code": 1000,
    "error": "",
    "message": "ยืนยันสำเร็จ"
}
```

## Error Codes
| รหัสข้อผิดพลาด | คำอธิบาย |
|---------------|-----------|
| `1000` | ไม่มีข้อผิดพลาด (OK) |
| `1001` | ไม่ได้รับอนุญาต (Unauthorized) |
| `1002` | จำนวนเครดิตคงเหลือของท่านมีไม่พอ (Insufficient) |
| `1003` | ข้อความไม่ถูกต้อง |
| `2001` | ไม่สามารถส่งข้อความแคมเปญได้ |
| `3001` | ไม่สามารถส่งข้อความ OTP ได้ |
| `3002` | ไม่สามารถยืนยัน OTP / OTP ไม่ถูกต้อง |

📌 **หมายเหตุ:** กรุณาใช้ API Key ที่ถูกต้องสำหรับการเข้าถึง API ที่ต้องมีการตรวจสอบสิทธิ์
