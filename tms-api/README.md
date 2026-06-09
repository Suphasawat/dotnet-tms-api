# Task Management System API

Backend API สำหรับระบบจัดการงาน (Task Management System) พัฒนาด้วย ASP.NET Core และใช้ MySQL เป็นฐานข้อมูล โดยมี React เป็น Frontend

โปรเจกต์นี้จัดทำขึ้นเพื่อเรียนรู้การพัฒนา REST API ด้วย ASP.NET Core ตั้งแต่การออกแบบ API, Authentication, การเชื่อมต่อฐานข้อมูล ไปจนถึงการนำ API ไปใช้งานร่วมกับ Frontend

## สถานะโปรเจกต์

โปรเจกต์อยู่ระหว่างการพัฒนา ปัจจุบันเป็น ASP.NET Core Minimal API เริ่มต้นบน .NET 10 และยังไม่ได้ติดตั้งหรือเชื่อมต่อ MySQL

## Features

- Login / Logout
- สร้าง อ่าน แก้ไข และลบงาน (CRUD)
- กำหนดสถานะงาน
  - `Pending`
  - `InProgress`
  - `Done`
- ค้นหางานจากชื่อหรือรายละเอียด
- Dashboard สรุปจำนวนงานทั้งหมดและจำนวนงานในแต่ละสถานะ

## Tech Stack

### Backend

- ASP.NET Core
- C#
- REST API
- Entity Framework Core (วางแผนใช้สำหรับจัดการฐานข้อมูล)
- JWT Authentication (วางแผนใช้สำหรับ Login และยืนยันตัวตน)

### Database

- MySQL

### Frontend

- React (แยก repository จาก Backend)

## Requirements

เครื่องมือที่ควรติดตั้งก่อนเริ่มพัฒนา:

- [.NET 10 SDK](https://dotnet.microsoft.com/download/dotnet/10.0)
- MySQL Server
- Git
- IDE เช่น Visual Studio, Visual Studio Code หรือ JetBrains Rider

ตรวจสอบเวอร์ชัน .NET:

```bash
dotnet --version
```

## Getting Started

1. Clone repository

```bash
git clone <repository-url>
cd tms-api
```

2. Restore dependencies

```bash
dotnet restore
```

3. Run project

```bash
dotnet run
```

เมื่อรันด้วย profile ที่อยู่ในโปรเจกต์ API จะเปิดที่:

- HTTP: `http://localhost:5105`
- HTTPS: `https://localhost:7160`

OpenAPI document ใน Development environment:

```text
http://localhost:5105/openapi/v1.json
```

## Database Configuration

เมื่อเพิ่ม Entity Framework Core และ MySQL provider แล้ว ให้กำหนด Connection String ผ่าน User Secrets หรือ Environment Variables เพื่อไม่ให้ข้อมูลสำคัญถูก commit ลง Git

ตัวอย่างโครงสร้างค่า:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Port=3306;Database=tms_db;User=<username>;Password=<password>;"
  }
}
```

ตัวอย่างการตั้งค่าด้วย .NET User Secrets:

```bash
dotnet user-secrets init
dotnet user-secrets set "ConnectionStrings:DefaultConnection" "Server=localhost;Port=3306;Database=tms_db;User=<username>;Password=<password>;"
```

> หมายเหตุ: โปรเจกต์ยังไม่ได้ติดตั้ง Entity Framework Core และ MySQL provider คำสั่ง migration จะเพิ่มภายหลังเมื่อส่วนฐานข้อมูลพร้อมใช้งาน

## Planned Data Model

### User

| Field | Description |
| --- | --- |
| `Id` | รหัสผู้ใช้ |
| `Username` | ชื่อผู้ใช้ |
| `PasswordHash` | รหัสผ่านที่ผ่านการ hash |
| `CreatedAt` | วันที่สร้างบัญชี |

### Task

| Field | Description |
| --- | --- |
| `Id` | รหัสงาน |
| `Title` | ชื่องาน |
| `Description` | รายละเอียดงาน |
| `Status` | สถานะ `Pending`, `InProgress` หรือ `Done` |
| `DueDate` | วันครบกำหนด |
| `CreatedAt` | วันที่สร้าง |
| `UpdatedAt` | วันที่แก้ไขล่าสุด |
| `UserId` | เจ้าของงาน |

## Planned API Endpoints

### Authentication

| Method | Endpoint | Description |
| --- | --- | --- |
| `POST` | `/api/auth/login` | เข้าสู่ระบบ |
| `POST` | `/api/auth/logout` | ออกจากระบบ |

### Tasks

| Method | Endpoint | Description |
| --- | --- | --- |
| `GET` | `/api/tasks` | ดูรายการงานและค้นหางาน |
| `GET` | `/api/tasks/{id}` | ดูรายละเอียดงาน |
| `POST` | `/api/tasks` | สร้างงาน |
| `PUT` | `/api/tasks/{id}` | แก้ไขงาน |
| `DELETE` | `/api/tasks/{id}` | ลบงาน |
| `PATCH` | `/api/tasks/{id}/status` | เปลี่ยนสถานะงาน |

ตัวอย่างการค้นหาและกรองงาน:

```text
GET /api/tasks?search=report&status=InProgress
```

### Dashboard

| Method | Endpoint | Description |
| --- | --- | --- |
| `GET` | `/api/dashboard/summary` | สรุปจำนวนงานทั้งหมดและแยกตามสถานะ |

ตัวอย่าง Response:

```json
{
  "total": 10,
  "pending": 3,
  "inProgress": 4,
  "done": 3
}
```

## Suggested Project Structure

```text
tms-api/
├── Controllers/
├── Data/
├── DTOs/
├── Models/
├── Repositories/
├── Services/
├── Program.cs
├── appsettings.json
└── tms-api.csproj
```

## Learning Goals

- เข้าใจโครงสร้างและการทำงานของ ASP.NET Core
- ออกแบบ REST API และ HTTP status codes
- ใช้ Dependency Injection
- เชื่อมต่อ MySQL ด้วย Entity Framework Core
- จัดการ Authentication และ Authorization
- แยก Entity, DTO, Service และ Controller ตามหน้าที่
- จัดการ Validation และ Error Response
- เชื่อมต่อ Backend API เข้ากับ React Frontend

## Roadmap

- [ ] ออกแบบ Models และ DTOs
- [ ] เชื่อมต่อ MySQL และสร้าง Migrations
- [ ] พัฒนา Login / Logout และ JWT Authentication
- [ ] พัฒนา CRUD งาน
- [ ] เพิ่มการค้นหาและกรองตามสถานะ
- [ ] พัฒนา Dashboard Summary
- [ ] ตั้งค่า CORS สำหรับ React Frontend
- [ ] เพิ่ม Validation และ Global Exception Handling
- [ ] เพิ่ม Unit Tests และ Integration Tests

## License

โปรเจกต์นี้จัดทำขึ้นเพื่อการเรียนรู้
