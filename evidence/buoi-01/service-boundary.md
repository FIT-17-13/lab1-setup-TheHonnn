# Service Boundary của nhóm

## 1. Thông tin nhóm

- Tên nhóm: Nhom 5
- Lớp: CNTT 17-13
- Thành viên: 4
- Service nhóm phụ trách: Analytics
- Sản phẩm tổng thể của lớp: Smart Campus Operations Platform

## 2. Actor

- Thiết bị IoT (ESP32, cảm biến): Cung cấp dữ liệu nhiệt độ, độ ẩm, chuyển động.
- Camera IP: Cung cấp luồng hình ảnh/video để phân tích.
- Đầu đọc RFID: Ghi nhận sự kiện quẹt thẻ ra/vào.
- Người dùng (Sinh viên/Nhân viên): Thực hiện hành động quẹt thẻ hoặc di chuyển trong khu vực giám sát.

## 3. System Boundary

Nhóm em xây phần nào?

Phần nhóm kiểm soát:

- Logic tổng hợp dữ liệu từ nhiều nguồn khác nhau (IoT, Camera, Access Gate, Core).
- Hệ thống tính toán các chỉ số (metric) như trung bình, tổng hợp theo thời gian.
- Cơ sở dữ liệu lưu trữ các báo cáo và thống kê (PostgreSQL, TimescaleDB, hoặc MongoDB).
- Các endpoint trả về báo cáo JSON cho Dashboard.

Phần nhóm chỉ tích hợp:

- Dữ liệu thô từ các cảm biến thông qua IoT Ingestion Service.
- Dữ liệu sự kiện hình ảnh từ Camera Stream / AI Vision Service.
- Dữ liệu lượt ra/vào từ Access Gate Service.
- Dữ liệu về các quyết định nghiệp vụ và cảnh báo từ Core Business Service.

## 4. Service Boundary

Service của nhóm có trách nhiệm gì?
- Tổng hợp dữ liệu từ nhiều service khác để tạo metric, thống kê và báo cáo.
- Cung cấp các chỉ số vận hành như: số lượt ra/vào theo giờ, nhiệt độ trung bình, số lượng cảnh báo trong ngày.


Service KHÔNG làm gì?
- KHÔNG trực tiếp nhận dữ liệu từ phần cứng (việc này của IoT Ingestion, Access Gate).
- KHÔNG thực hiện phân tích hình ảnh (việc này của AI Vision).
- KHÔNG ra quyết định xử lý nghiệp vụ hay tạo cảnh báo (việc này của Core Business).
- KHÔNG trực tiếp gửi tin nhắn/email cho người dùng (việc này của Notification).

## 5. Input / Output

### Input

- Dữ liệu cảm biến (nhiệt độ, độ ẩm, chuyển động).
- Dữ liệu phát hiện đối tượng từ camera/AI.
- Dữ liệu lượt ra/vào (gate_id, card_id, direction).
- Dữ liệu cảnh báo và quyết định nghiệp vụ.


### Output

- Báo cáo tổng hợp dưới dạng JSON (ví dụ: total_access, total_alerts, avg_temperature).
Các metric phục vụ hiển thị trên Dashboard.

## 6. API dự kiến
```
Method	Endpoint	                           Mục đích
GET	    /health	                                Kiểm tra trạng thái hoạt động của service.
GET   	/api/v1/analytics/daily-summary	        Lấy báo cáo tổng hợp trong ngày (lượt truy cập, cảnh báo, nhiệt độ).
GET	    /api/v1/analytics/metrics/access	    Lấy thống kê số lượt ra/vào theo khung giờ.
GET	    /api/v1/analytics/metrics/environment	Lấy thống kê nhiệt độ trung bình theo phòng/khu vực.

```
## 7. Phụ thuộc service khác

- Service này gọi đến service nào?
- IoT Ingestion: Lấy dữ liệu cảm biến.
- Camera Stream / AI Vision: Lấy dữ liệu sự kiện camera.
- Access Gate: Lấy dữ liệu ra/vào.24
- Core Business: Lấy dữ liệu cảnh báo.

Service nào gọi đến service này?
- Dashboard/ U: Gọi để lấy dữ liệu hiển thị báo cáo.
## 8. Sơ đồ minh họa (service_boundary.drawio.svg)
linkStyle default interpolate linear
Có thể vẽ bằng Mermaid, draw.io, Ludichart hoặc ảnh chụp sơ đồ.
```mermaid
flowchart LR
    linkStyle default interpolate linear

    subgraph Upstream ["Upstream Services"]
        direction TB
        A["IoT Ingestion"]
        B["AI Vision"]
        C["Access Gate"]
        D["Core Business"]
    end

    subgraph Analytics ["ANALYTICS SERVICE BOUNDARY"]
        W["JSON"]
        E["Processing & Analytics<br>Aggregate data<br>Generate metrics<br>Create reports"]
        F[(Database)]
        
        E --- |"reports / metrics"| F
        
    end

    G["Dashboard / Admin UI"]

    A -- "POST/sensor events" --> E
    B -- "POST/detection events" --> E
    C -- "POST/access logs" --> E
    D -- "POST/alerts events" --> E

    E -- "GET/analytics" --> G
    
    classDef sourceNode fill:#eaf7f2,stroke:#8ac2a5,stroke-width:1.5px,color:#0c5b45
    classDef coreNode fill:#ffffff,stroke:#4f46e5,stroke-width:2px,color:#1e1b4b,rx:10,ry:10
    classDef dbNode fill:#ffffff,stroke:#64748b,stroke-width:1.5px,color:#334155
    classDef uiNode fill:#fff1f2,stroke:#fb7185,stroke-width:1.5px,color:#881337

    class A,B,C,D sourceNode
    class E coreNode
    class F dbNode
    class G,W uiNode

    style Upstream fill:none,stroke:#cbd5e1,stroke-width:1px,stroke-dasharray: 4 4,color:#94a3b8
    style Analytics fill:#f8faff,stroke:#4f46e5,stroke-width:3px,color:#4f46e5,rx:20,ry:20

```

