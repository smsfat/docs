# RESTful API for SMSFAT
#### Table of contents
* [Base URL](#base-url)
* [Constructing the Request Header](#constructing-the-request-header)
* [Endpoints](#endpoints)
* [Request/Response Examples](#requestresponse-examples)
* [Error Codes](#error-codes)

## Base URL
- The base URL is: https://smsfat.com

## Constructing the Request Header
All authenticated requests require an **API Key** sent via the Header.

### **Example Request Header**
```http
Accept: application/json
Authorization: Bearer {API_KEY}
Content-Type: application/json
```

## Endpoints
All secure endpoints require **Authentication**.

### **Secure Endpoints V1**
:lock: _Authentication required_

| Method | Endpoint | Description | Required Parameters |
|--------|---------|-------------|---------------------|
| `GET` | [`/api/v1/open/my/balance`](#get-apiv1openmybalance) | Get current credit balance | No parameters |
| `GET` | [`/api/v1/open/my/sender`](#get-apiv1openmysender) | Get sender list | No parameters |
| `POST` | [`/api/v1/open/sms/campaign/send`](#post-apiv1opensmscampaignsend) | Send campaign message | `sendmessageName`, `message`, `phonelist`, `schedule`, `senderId` |
| `POST` | [`/api/v1/open/sms/otp/send`](#post-apiv1opensmsotpsend) | Send OTP message | `target`, `senderId (optional)` |
| `POST` | [`/api/v1/open/sms/otp/verify`](#post-apiv1opensmsotpverify) | Verify OTP | `target`, `otpCode`, `otpRef` |
| `POST` | [`/api/v1/open/sms/refund/:campaignId`](#post-apiv1opensmsrefundcampaignid) | Refund credits | `campaignId` |
| `GET` | [`/api/v1/open/my/campaigns`](#get-apiv1openmycampaigns) | Get all campaigns | `page`, `perPage`, `search (optional)`, `startDate (optional)`, `endDate (optional)` |
| `GET` | [`/api/v1/open/my/campaign/:id/summary`](#get-apiv1openmycampaignidsummary) | Get campaign report summary | `id` |
| `GET` | [`/api/v1/open/my/campaign/:id/phones`](#get-apiv1openmycampaignidphones) | Get phone-level campaign report | `id`, `page`, `perPage`, `search (optional)` |
| `GET` | [`/api/v1/open/my/dashboard`](#get-apiv1openmydashboard) | Get dashboard overview | `startDate (optional)`, `endDate (optional)` |

## Request/Response Examples

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
    "sendmessageName": "Campaign Name",
    "message": "This is a test message",
    "phonelist": "065XXXXXX1\n065XXXXXX2\n065XXXXXX3\n065XXXXXX4", // Max 50000 numbers
    "schedule": "2025-02-02T22:12:36.147Z", // Scheduled send time. Use current time (new Date()) for immediate send
    "senderId": "00000000-0000-0000-0000-000000000000" // Use MARKETING type sender only
}
```

**Response:**
```json
{
    "code": 1000,
    "error": "",
    "message": "Success",
    "data": { /* Created campaign data */ }
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
    "senderId": "00000000-0000-0000-0000-000000000000" // OTP type only | senderId is OPTIONAL. Use when you want a custom sender instead of the system default
}
```

**Response:**
```json
{
    "code": 1000,
    "error": "",
    "message": "Success",
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
    "otpCode": "123456",
    "otpRef": "AAAA"
}
```

**Response:**
```json
{
    "code": 1000,
    "error": "",
    "message": "Verification successful"
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
    "message": "Credit refund successful"
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

| Query Parameter | Type | Required | Description |
|----------------|------|----------|-------------|
| `page` | number | Yes | Page number |
| `perPage` | number | Yes | Items per page |
| `search` | string | No | Search by sender name |
| `startDate` | ISO 8601 | No | Filter start date |
| `endDate` | ISO 8601 | No | Filter end date |

**Response:**
```json
{
    "code": 1000,
    "error": "",
    "message": "",
    "data": [
        {
            "sendmessageId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "sendmessageName": "Campaign Name",
            "message": "Message content",
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
            "sendmessageName": "Campaign Name",
            "message": "Message content",
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

**Counter fields:**

| Field | Description |
|-------|-------------|
| `fail` | Number of failed messages |
| `expired` | Number of expired messages |
| `blocklist` | Number of blocked messages |
| `invalid_msn` | Number of invalid phone numbers |
| `pending` | Number of pending messages |
| `success` | Number of successful messages |
| `total` | Total number of messages |
| `refund_credit` | Credits eligible for refund (failed messages) |
| `credit_use_total` | Total credits used |

### **GET /api/v1/open/my/campaign/:id/phones**
**Request:**
```http
GET /api/v1/open/my/campaign/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/phones?page=1&perPage=20&search= HTTP/1.1
Host: smsfat.com
Accept: application/json
Authorization: Bearer {API_KEY}
Content-Type: application/json
```

| Query Parameter | Type | Required | Description |
|----------------|------|----------|-------------|
| `page` | number | Yes | Page number |
| `perPage` | number | Yes | Items per page |
| `search` | string | No | Search by phone number |

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

**Phone status values:**

| Status | Description |
|--------|-------------|
| `PENDING` | Waiting to be sent |
| `SENDING` | Currently sending |
| `SUCCESS` | Delivered successfully |
| `FAIL` | Delivery failed |
| `EXPIRED` | Message expired |
| `BLOCKLIST` | Phone number is blocklisted |
| `INVALID_MSN` | Invalid phone number |

### **GET /api/v1/open/my/dashboard**
**Request:**
```http
GET /api/v1/open/my/dashboard?startDate=2025-01-01T00:00:00.000Z&endDate=2025-12-31T23:59:59.999Z HTTP/1.1
Host: smsfat.com
Accept: application/json
Authorization: Bearer {API_KEY}
Content-Type: application/json
```

| Query Parameter | Type | Required | Description |
|----------------|------|----------|-------------|
| `startDate` | ISO 8601 | No | Filter start date (defaults to start of today) |
| `endDate` | ISO 8601 | No | Filter end date (defaults to end of today) |

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

**Dashboard fields:**

| Field | Description |
|-------|-------------|
| `summary.credit` | Total credits used in the period |
| `summary.counts` | SMS count breakdown by status |
| `chart.donut.refund` | Credits eligible for refund |
| `chart.donut.using` | Credits successfully used |
| `chart.column.labels` | Date labels (YYYY-MM-DD) |
| `chart.column.success` | Success count per date |
| `chart.column.fail` | Failure count per date |

## Error Codes
| Error Code | Description |
|-----------|-------------|
| `1000` | No error (OK) |
| `1001` | Unauthorized |
| `1002` | Insufficient credit balance |
| `1003` | Invalid message |
| `1004` | Internal server error |
| `2001` | Campaign send failed |
| `2002` | Campaign not found |
| `2003` | Refund period expired (30 days) |
| `2004` | Campaign already refunded |
| `2005` | Refund not ready (must wait 24 hours) |
| `3001` | OTP send failed |
| `3002` | OTP verification failed / Invalid OTP |
| `4001` | Campaign not found (Report) |
| `4002` | Report retrieval failed |

> **Note:** Please use a valid API Key for accessing authenticated API endpoints.
