Factory Method là một thiết kế mẫu sáng tạo (creational design pattern) cung cấp một giao diện để tạo ra các đối tượng trong lớp cha, nhưng cho phép các lớp con thay đổi loại đối tượng sẽ được tạo ra. Để làm rõ định nghĩa này, chúng ta sẽ xem xét một ví dụ cụ thể.

### Ví dụ: Giao hàng

Giả sử chúng ta đang xây dựng một hệ thống giao hàng và cần các phương tiện giao hàng khác nhau như xe tải và tàu thủy. Chúng ta sẽ sử dụng Factory Method để tạo ra các đối tượng phương tiện giao hàng.

### Phương pháp tư duy để áp dụng Factory Method Pattern

Để áp dụng Factory Method Pattern một cách hiệu quả, bạn có thể tuân theo các bước tư duy sau:

1. **Xác định các loại đối tượng cần tạo**: Trong ví dụ về giao hàng, các loại phương tiện giao hàng như xe tải và tàu thủy là các đối tượng cụ thể cần tạo.

2. **Xác định hành vi chung của các đối tượng**: Tất cả các phương tiện giao hàng đều có hành vi chung là "giao hàng". Điều này có thể được biểu diễn qua một giao diện hoặc lớp cơ sở.

3. **Tạo giao diện hoặc lớp cơ sở**: Tạo một giao diện hoặc lớp cơ sở mà các đối tượng cụ thể sẽ triển khai. Trong ví dụ này, đó là lớp `Transport` với phương thức `deliver()`.

4. **Tạo các lớp cụ thể triển khai giao diện hoặc lớp cơ sở**: Tạo các lớp cụ thể như `Truck` và `Ship` triển khai phương thức `deliver()`.

5. **Xác định nơi tạo đối tượng**: Xác định lớp nào sẽ chịu trách nhiệm tạo ra các đối tượng cụ thể. Đây là nơi bạn sẽ áp dụng Factory Method. Trong ví dụ này, đó là lớp `Logistics`.

6. **Tạo lớp cơ sở với phương thức Factory**: Tạo lớp cơ sở (như `Logistics`) với một phương thức Factory trừu tượng (`create_transport()`), mà các lớp con sẽ triển khai để trả về các đối tượng cụ thể.

7. **Tạo các lớp cụ thể triển khai phương thức Factory**: Tạo các lớp cụ thể như `RoadLogistics` và `SeaLogistics` triển khai phương thức `create_transport()` để trả về các đối tượng cụ thể.

8. **Sử dụng lớp cơ sở và phương thức Factory**: Sử dụng lớp cơ sở để gọi phương thức Factory và thực hiện hành vi của các đối tượng cụ thể mà không cần biết chính xác loại đối tượng nào được tạo ra.

### Mã nguồn ban đầu không áp dụng Factory Method Pattern

Dưới đây là mã nguồn ban đầu không áp dụng Factory Method Pattern, trong đó tất cả các logic tạo đối tượng đều nằm trong một lớp duy nhất. Điều này làm cho mã nguồn trở nên rối và khó mở rộng.

#### Mã nguồn ban đầu:

```python
class Logistics:
    def plan_delivery(self, transport_type):
        if transport_type == "truck":
            self.deliver_by_truck()
        elif transport_type == "ship":
            self.deliver_by_ship()
        else:
            print("Unknown transport type")

    def deliver_by_truck(self):
        print("Delivering by land in a truck.")

    def deliver_by_ship(self):
        print("Delivering by sea in a ship.")

if __name__ == "__main__":
    logistics = Logistics()

    # Kế hoạch giao hàng bằng xe tải
    logistics.plan_delivery("truck")  # Output: Delivering by land in a truck.

    # Kế hoạch giao hàng bằng tàu thủy
    logistics.plan_delivery("ship")  # Output: Delivering by sea in a ship.
```
### Nhược điểm của mã nguồn ban đầu:

1. **Không tuân thủ nguyên tắc mở-đóng (Open/Closed Principle)**: Để thêm một phương tiện giao hàng mới, bạn phải sửa đổi phương thức `plan_delivery()` và thêm logic mới. Điều này làm cho mã nguồn khó mở rộng và dễ bị lỗi khi thay đổi.

2. **Không linh hoạt**: Các logic tạo đối tượng và hành vi cụ thể được gộp chung trong một lớp. Điều này làm cho mã nguồn trở nên phức tạp và khó bảo trì.

3. **Khó bảo trì**: Mỗi khi bạn cần thay đổi logic của một phương tiện giao hàng cụ thể, bạn phải sửa đổi lớp `Logistics`, dẫn đến nguy cơ làm hỏng các phần khác của mã.

### Mã nguồn áp dụng Factory Method Pattern

Dưới đây là mã nguồn áp dụng Factory Method Pattern, giúp tách biệt logic tạo đối tượng và hành vi cụ thể, làm cho mã nguồn dễ bảo trì và mở rộng hơn.

#### Định nghĩa các lớp:

```python
from abc import ABC, abstractmethod

# Giao diện cho phương tiện giao hàng
class Transport(ABC):
    @abstractmethod
    def deliver(self):
        pass

# Lớp cụ thể cho xe tải
class Truck(Transport):
    def deliver(self):
        print("Delivering by land in a truck.")

# Lớp cụ thể cho tàu thủy
class Ship(Transport):
    def deliver(self):
        print("Delivering by sea in a ship.")

# Lớp cơ sở cho Factory
class Logistics(ABC):
    @abstractmethod
    def create_transport(self) -> Transport:
        pass

    def plan_delivery(self):
        transport = self.create_transport()
        transport.deliver()

# Lớp cụ thể cho giao hàng bằng xe tải
class RoadLogistics(Logistics):
    def create_transport(self) -> Transport:
        return Truck()

# Lớp cụ thể cho giao hàng bằng tàu thủy
class SeaLogistics(Logistics):
    def create_transport(self) -> Transport:
        return Ship()

# Sử dụng Factory Method
if __name__ == "__main__":
    road_logistics = RoadLogistics()
    sea_logistics = SeaLogistics()

    # Kế hoạch giao hàng bằng xe tải
    road_logistics.plan_delivery()  # Output: Delivering by land in a truck.

    # Kế hoạch giao hàng bằng tàu thủy
    sea_logistics.plan_delivery()  # Output: Delivering by sea in a ship.
```

### Lợi ích của việc áp dụng Factory Method Pattern:

1. **Tuân thủ nguyên tắc mở-đóng**: Bạn có thể thêm các phương tiện giao hàng mới bằng cách tạo các lớp chiến lược mới mà không cần thay đổi lớp `Logistics`.

2. **Linh hoạt**: Logic tạo đối tượng và hành vi cụ thể được tách biệt. Lớp `Logistics` chỉ biết cách sử dụng đối tượng `Transport`, không cần biết chi tiết về các loại phương tiện giao hàng cụ thể.

3. **Dễ bảo trì**: Mỗi phương tiện giao hàng có logic riêng của mình trong các lớp cụ thể, giúp mã nguồn dễ bảo trì và giảm nguy cơ lỗi khi thay đổi.
