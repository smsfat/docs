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
| `POST` | [`/api/v1/open/sms/refund/:campaignId`](#post-apiv1opensmsrefundcampaignid) | คืนเครดิต | `campaignId` |
| `GET` | [`/api/v1/open/my/campaigns`](#get-apiv1openmycampaigns) | ดึงรายการแคมเปญทั้งหมด | `page`, `perPage`, `search (optional)`, `startDate (optional)`, `endDate (optional)` |
| `GET` | [`/api/v1/open/my/campaign/:id/summary`](#get-apiv1openmycampaignidsummary) | ดูสรุปรายงานแคมเปญ | `id` |
| `GET` | [`/api/v1/open/my/campaign/:id/phones`](#get-apiv1openmycampaignidphones) | ดูรายงานรายเบอร์ของแคมเปญ | `id`, `page`, `perPage`, `search (optional)` |
| `GET` | [`/api/v1/open/my/dashboard`](#get-apiv1openmydashboard) | ดูแดชบอร์ดสรุปภาพรวม | `startDate (optional)`, `endDate (optional)` |

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

### **POST /api/v1/open/sms/refund/:campaignId**
**Request:**
```http
POST /api/v1/open/sms/refund/:campaignId HTTP/1.1
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
    "message": "คืนเครดิตสำเร็จ"
}
```

### **GET /api/v1/open/my/campaigns**
**Request:**
```http
GET /api/v1/open/my/campaigns?page=1&perPage=20&search=&startDate=2025-01-01T00:00:00.000Z&endDate=2025-12-31T23:59:59.999Z HTTP/1.1
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
            "sendmessageId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "sendmessageName": "ชื่อแคมเปญ",
            "message": "ข้อความ",
            "type": "MARKETING",
            "credit": 10,
            "creditPerUse": 1,
            "schedule": "2025-02-02T22:12:36.147Z",
            "refNo": "ABCDEFGHIJKLMNO",
            "msgType": "TH",
            "refund": false,
            "status": "SUCCESS",
            "createdAt": "2025-02-02T22:12:36.147Z",
            "updatedAt": "2025-02-02T22:12:40.000Z",
            "sender": {
                "senderName": "sample"
            }
        }
    ],
    "meta": {
        "currentPage": 1,
        "lastPage": 1,
        "next": null,
        "perPage": 20,
        "prev": null,
        "total": 1
    }
}
```

### **GET /api/v1/open/my/campaign/:id/summary**
**Request:**
```http
GET /api/v1/open/my/campaign/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/summary HTTP/1.1
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
        "data": {
            "sendmessageId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "sendmessageName": "ชื่อแคมเปญ",
            "message": "ข้อความ",
            "type": "MARKETING",
            "credit": 10,
            "creditPerUse": 1,
            "schedule": "2025-02-02T22:12:36.147Z",
            "refNo": "ABCDEFGHIJKLMNO",
            "msgType": "TH",
            "refund": false,
            "status": "SUCCESS",
            "createdAt": "2025-02-02T22:12:36.147Z",
            "updatedAt": "2025-02-02T22:12:40.000Z",
            "sender": {
                "senderName": "sample",
                "type": "MARKETING"
            }
        },
        "counter": {
            "fail": 0,
            "expired": 0,
            "blocklist": 0,
            "invalid_msn": 0,
            "pending": 0,
            "success": 10,
            "total": 10
        },
        "refund_credit": 0,
        "credit_use_total": 10
    }
}
```

### **GET /api/v1/open/my/campaign/:id/phones**
**Request:**
```http
GET /api/v1/open/my/campaign/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/phones?page=1&perPage=20&search= HTTP/1.1
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
            "uuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "refNo": "ABCDEFGHIJKLMNO",
            "phoneNumber": "66651234567",
            "errorCode": null,
            "status": "SUCCESS",
            "sendmessageId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "createdAt": "2025-02-02T22:12:36.147Z",
            "updatedAt": "2025-02-02T22:12:40.000Z"
        }
    ],
    "meta": {
        "currentPage": 1,
        "lastPage": 1,
        "next": null,
        "perPage": 20,
        "prev": null,
        "total": 1
    }
}
```

### **GET /api/v1/open/my/dashboard**
**Request:**
```http
GET /api/v1/open/my/dashboard?startDate=2025-01-01T00:00:00.000Z&endDate=2025-12-31T23:59:59.999Z HTTP/1.1
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
        "summary": {
            "credit": 500,
            "counts": {
                "PENDING": 0,
                "SUCCESS": 450,
                "FAIL": 20,
                "EXPIRED": 10,
                "BLOCKLIST": 15,
                "INVALID_MSN": 5
            }
        },
        "chart": {
            "donut": {
                "refund": 50,
                "using": 450
            },
            "column": {
                "labels": ["2025-01-01", "2025-01-02", "2025-01-03"],
                "success": [150, 200, 100],
                "fail": [10, 15, 25]
            }
        }
    }
}
```

## Error Codes
| รหัสข้อผิดพลาด | คำอธิบาย |
|---------------|-----------|
| `1000` | ไม่มีข้อผิดพลาด (OK) |
| `1001` | ไม่ได้รับอนุญาต (Unauthorized) |
| `1002` | จำนวนเครดิตคงเหลือของท่านมีไม่พอ (Insufficient) |
| `1003` | ข้อความไม่ถูกต้อง |
| `1004` | เกิดข้อผิดพลาดในระบบ (Internal Error ) |
| `2001` | ไม่สามารถส่งข้อความแคมเปญได้ (Campaign Send Failed) |
| `2002` | ไม่พบข้อมูลแคมเปญ (Campaign Not Found) |
| `2003` | เกินระยะเวลา 30 วัน (Refund Period Expired) |
| `2004` | เคมเปญนี้ได้ทำการขอคืนเครดิตไปแล้ว (Already Refunded) |
| `2005` | ต้องรอ 24 ชม. ก่อนขอคืนเครดิตได้ (Refund Not Ready) |
| `3001` | ไม่สามารถส่งข้อความ OTP ได้ |
| `3002` | ไม่สามารถยืนยัน OTP / OTP ไม่ถูกต้อง |
| `4001` | ไม่พบข้อมูลแคมเปญ (Campaign Not Found) |
| `4002` | ไม่สามารถดึงรายงานได้ (Report Failed) |

📌 **หมายเหตุ:** กรุณาใช้ API Key ที่ถูกต้องสำหรับการเข้าถึง API ที่ต้องมีการตรวจสอบสิทธิ์
