# Service Boundary của nhóm

## 1. Thông tin nhóm

- Tên nhóm: Nhom 6
- Lớp: CNTT 17-13
- Thành viên: 2
- Service nhóm phụ trách: A5
- Sản phẩm tổng thể của lớp: 

## 2. Actor

Ai tương tác với hệ thống/service?

## 3. System Boundary

Nhóm em xây phần nào?

Phần nhóm kiểm soát:

- ...

Phần nhóm chỉ tích hợp:

- ...

## 4. Service Boundary

Service của nhóm có trách nhiệm gì?

Service KHÔNG làm gì?

## 5. Input / Output

### Input

- ...

### Output

- ...

## 6. API dự kiến

| Method | Endpoint | Mục đích |
|---|---|---|
| GET | /health | Kiểm tra service |
| POST | ... | ... |

## 7. Phụ thuộc service khác

Service này gọi đến service nào?

Service nào gọi đến service này?

## 8. Sơ đồ minh họa

Có thể vẽ bằng Mermaid, draw.io, Ludichart hoặc ảnh chụp sơ đồ.

```mermaid
flowchart LR
    User[Actor] --> Service[Service của nhóm]
    Service --> DB[(Database)]
    Service --> Other[Service khác]
