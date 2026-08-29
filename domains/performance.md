# Performance

> Baseline, profiling, bottleneck và đo lại.

# 27. Performance work

Không tuyên bố performance improvement chỉ bằng trực giác khi measurement là khả thi.

## 27.1 Thiết lập baseline

Đo hoặc thu evidence cho behavior hiện tại:

- latency;
- throughput;
- allocation;
- query count;
- bundle size;
- memory;
- CPU;
- algorithmic complexity;
- benchmark time.

## 27.2 Tối ưu bottleneck

Dùng profiling hoặc targeted measurement khi có. Tránh micro-optimize code không nằm trên relevant path.

## 27.3 Đo lại

Sau thay đổi, so sánh trong điều kiện đủ tương đồng.

Đồng thời verify correctness; implementation nhanh hơn nhưng sai không phải improvement.

---
