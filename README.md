# Đánh giá hiệu năng hệ thống Message Queue: EMQX, VerneMQ, RabbitMQ

## 📌 Mục tiêu

Đồ án này nhằm đánh giá hiệu năng của ba hệ thống Message Broker phổ biến sử dụng giao thức MQTT: **EMQX**, **VerneMQ**, và **RabbitMQ**. Qua đó, nhóm thực hiện:
- Triển khai cụm broker (cluster) cho từng hệ thống.
- Cấu hình các mức QoS (0, 1, 2) để kiểm tra độ ổn định, tốc độ và độ tin cậy.
- Sử dụng các công cụ giám sát (Prometheus + Grafana).
- Đo lường số gói tin gửi thành công, bị mất, độ trễ (latency), và thông lượng (throughput).

## ⚙️ Môi trường thử nghiệm

- **Hệ điều hành:** Ubuntu Server 22.04 LTS
- **Số lượng máy:** 3 máy ảo (VM), mỗi máy đóng vai trò broker hoặc publisher/subscriber
- **Các công nghệ sử dụng:**
  - Docker & Docker Compose
  - Prometheus & Grafana
  - MQTT (QoS 0/1/2)
  - Python (client publish/subscribe)

## 📂 Cấu trúc dự án

```bash
.
├── emqx/
│   ├── docker-compose.yml
│   └── config/
├── vernemq/
│   ├── docker-compose.yml
│   └── config/
├── rabbitmq/
│   ├── docker-compose.yml
│   └── config/
├── monitoring/
│   ├── prometheus.yml
│   └── grafana/
├── publisher/
│   ├── publish_qos_0.py
│   ├── publish_qos_1.py
│   └── publish_qos_2.py
├── subscriber/
│   ├── subscribe_qos_0.py
│   ├── subscribe_qos_1.py
│   └── subscribe_qos_2.py
└── README.md
```

## 🚀 Hướng dẫn triển khai

### 1. Triển khai EMQX Cluster

```bash
cd emqx
docker-compose up -d
```

- Truy cập dashboard: `http://<host>:18083`
- Tài khoản mặc định: `admin/public`

### 2. Triển khai VerneMQ Cluster

```bash
cd vernemq
docker-compose up -d
```

- Truy cập: `http://<host>:8888`

### 3. Triển khai RabbitMQ với MQTT Plugin

```bash
cd rabbitmq
docker-compose up -d
```

- Mở giao diện web: `http://<host>:15672`
- Tài khoản mặc định: `guest/guest`

Kích hoạt plugin MQTT:
```bash
rabbitmq-plugins enable rabbitmq_mqtt
```

## 📊 Giám sát với Prometheus & Grafana

Khởi động stack giám sát:
```bash
cd monitoring
docker-compose up -d
```

- Prometheus: `http://localhost:9090`
- Grafana: `http://localhost:3000` (Tài khoản mặc định: `admin/admin`)

## 🧪 Thử nghiệm và đo hiệu năng

Chạy publisher:
```bash
cd publisher
python3 publish_qos_0.py
python3 publish_qos_1.py
python3 publish_qos_2.py
```

Chạy subscriber:
```bash
cd subscriber
python3 subscribe_qos_0.py
python3 subscribe_qos_1.py
python3 subscribe_qos_2.py
```

## 📈 Tiêu chí đánh giá

| Tiêu chí          | EMQX           | VerneMQ         | RabbitMQ          |
|-------------------|----------------|-----------------|-------------------|
| Throughput        | ✅ Cao          | ✅ Trung bình   | ⚠️ Thấp (MQTT plugin) |
| Packet Loss       | ✅ Thấp         | ✅ Thấp         | ⚠️ Tăng nếu tải cao   |
| Latency           | ✅ Thấp         | ✅ Thấp         | ⚠️ Cao hơn           |
| MQTT Native       | ✅ V5.0         | ✅ V5.0         | ⚠️ MQTT v3 qua plugin |
| Cluster dễ dàng   | ✅ Có sẵn       | ✅ Erlang       | ⚠️ Phức tạp hơn      |

## 📚 Tài liệu tham khảo

- [EMQX Documentation](https://www.emqx.io/docs)
- [VerneMQ GitHub](https://github.com/vernemq/vernemq)
- [RabbitMQ MQTT Plugin](https://www.rabbitmq.com/mqtt.html)
- [Prometheus](https://prometheus.io/)
- [Grafana](https://grafana.com/)

## 👩‍💻 Nhóm thực hiện

- **Đoàn Thị Khánh Linh** – 22520758
- **Phạm Gia Linh** – 22520776
- **Lâm Thiên Phát** – 22521068
- **GVHD**: ThS. Đặng Lê Bảo Chương
