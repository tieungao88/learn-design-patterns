Quyết định sử dụng Design Pattern Strategy cho bài toán lựa chọn phương tiện di chuyển đến sân bay dựa trên những lý do sau:

### 1. **Phân tích bài toán:**
- Bạn cần một ứng dụng có thể chọn giữa nhiều phương thức di chuyển khác nhau (bus, cab, bicycle) để đến sân bay.
- Mỗi phương thức di chuyển có thể có cách thức triển khai khác nhau.
- Ứng dụng cần linh hoạt để có thể dễ dàng thêm các phương thức di chuyển mới trong tương lai (ví dụ: đi bộ, đi tàu).

### 2. **Yêu cầu về tính linh hoạt và mở rộng:**
- Cần dễ dàng thêm hoặc thay đổi các phương thức di chuyển mà không ảnh hưởng đến phần còn lại của hệ thống.
- Tách biệt logic của từng phương thức di chuyển để dễ bảo trì.

### 3. **Tránh mã phình to và khó bảo trì:**
- Nếu tất cả logic của các phương thức di chuyển được đưa vào một lớp duy nhất, lớp đó sẽ trở nên phức tạp và khó bảo trì.
- Việc thay đổi hoặc thêm mới phương thức di chuyển sẽ làm tăng nguy cơ phát sinh lỗi.

### 4. **Tăng cường tính khả thi và dễ hiểu:**
- Sử dụng các chiến lược riêng biệt cho từng phương thức di chuyển giúp mã dễ hiểu hơn.
- Các chiến lược có thể được phát triển, kiểm tra và bảo trì một cách độc lập.

### Cách tiếp cận để quyết định thiết kế:

1. **Xác định Interface chung:**
   - Tất cả các phương thức di chuyển đều có một điểm chung là di chuyển đến sân bay. Do đó, chúng ta cần một interface chung để định nghĩa hành vi này. Đây là lý do chúng ta tạo ra interface `TravelStrategy` với phương thức `travelToAirport()`.

2. **Triển khai các chiến lược cụ thể:**
   - Với mỗi phương thức di chuyển (bus, cab, bicycle), chúng ta triển khai interface `TravelStrategy` trong các lớp cụ thể như `BusStrategy`, `CabStrategy`, và `BicycleStrategy`.

3. **Thiết kế lớp Context:**
   - Lớp `TravelContext` giữ một tham chiếu đến đối tượng `TravelStrategy` hiện tại. Lớp này có phương thức `setStrategy(TravelStrategy strategy)` để thay đổi chiến lược di chuyển.
   - Phương thức `executeStrategy()` trong `TravelContext` gọi phương thức `travelToAirport()` của chiến lược hiện tại để thực hiện việc di chuyển.

4. **Client thiết lập chiến lược:**
   - Trong mã client, chúng ta có thể tạo các đối tượng chiến lược cụ thể và thiết lập chúng cho `TravelContext`. Sau đó, gọi phương thức `executeStrategy()` để thực thi chiến lược di chuyển đã chọn.

## Minh họa code python

### Định nghĩa lớp cơ sở trừu tượng `TravelStrategy`:
```python
from abc import ABC, abstractmethod

class TravelStrategy(ABC):
    @abstractmethod
    def travel_to_airport(self):
        pass
```
- `TravelStrategy` kế thừa từ `ABC` (Abstract Base Class) để trở thành một lớp cơ sở trừu tượng.
- Phương thức `travel_to_airport()` được đánh dấu với `@abstractmethod`, yêu cầu các lớp con phải triển khai phương thức này.

### Triển khai các lớp chiến lược cụ thể:
#### 1. `BusStrategy`:
```python
class BusStrategy(TravelStrategy):
    def travel_to_airport(self):
        print("Taking the bus to the airport.")
```
- `BusStrategy` kế thừa `TravelStrategy` và triển khai phương thức `travel_to_airport()` với logic cụ thể cho việc di chuyển bằng xe buýt.

#### 2. `CabStrategy`:
```python
class CabStrategy(TravelStrategy):
    def travel_to_airport(self):
        print("Taking a cab to the airport.")
```
- `CabStrategy` cũng kế thừa `TravelStrategy` và triển khai phương thức `travel_to_airport()` với logic cụ thể cho việc di chuyển bằng taxi.

#### 3. `BicycleStrategy`:
```python
class BicycleStrategy(TravelStrategy):
    def travel_to_airport(self):
        print("Riding a bicycle to the airport.")
```
- `BicycleStrategy` kế thừa `TravelStrategy` và triển khai phương thức `travel_to_airport()` với logic cụ thể cho việc di chuyển bằng xe đạp.

### Lớp Context `TravelContext`:
```python
class TravelContext:
    def __init__(self):
        self.strategy = None

    def set_strategy(self, strategy: TravelStrategy):
        self.strategy = strategy

    def execute_strategy(self):
        if self.strategy:
            self.strategy.travel_to_airport() # exe method def travel_to_airport(self) của đối tượng hiện tại
                                                # (`self.strategy` được truyền vào ).
        else:
            print("No strategy set.")
```
- `TravelContext` có một thuộc tính `strategy` để giữ đối tượng chiến lược hiện tại.
- `set_strategy(strategy)` dùng để thay đổi chiến lược hiện tại.
- `execute_strategy()` gọi phương thức `travel_to_airport()` của chiến lược hiện tại để thực thi việc di chuyển.

### Mã client để thiết lập và sử dụng chiến lược:
```python
if __name__ == "__main__":
    context = TravelContext()

    # Chọn chiến lược Bus
    context.set_strategy(BusStrategy())
    context.execute_strategy()  # Output: Taking the bus to the airport.

    # Chọn chiến lược Cab
    context.set_strategy(CabStrategy())
    context.execute_strategy()  # Output: Taking a cab to the airport.

    # Chọn chiến lược Bicycle
    context.set_strategy(BicycleStrategy())
    context.execute_strategy()  # Output: Riding a bicycle to the airport.
```
- Client tạo ra đối tượng `TravelContext`.
- Client thiết lập các chiến lược khác nhau (`BusStrategy`, `CabStrategy`, `BicycleStrategy`) và thực thi chiến lược bằng cách gọi `execute_strategy()`.

### Tóm tắt:
Việc sử dụng kế thừa (inheritance) trong Python cho phép các lớp con như `BusStrategy`, `CabStrategy`, `BicycleStrategy` kế thừa từ lớp cơ sở trừu tượng `TravelStrategy` và triển khai phương thức trừu tượng `travel_to_airport()`. Điều này giúp tách biệt và quản lý các phương thức di chuyển khác nhau một cách linh hoạt và dễ bảo trì.
