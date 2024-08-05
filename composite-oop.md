Composite là một trong các mẫu thiết kế cấu trúc (structural design pattern) trong lập trình hướng đối tượng. Nó cho phép bạn cấu trúc các đối tượng theo dạng cây và sau đó làm việc với các cấu trúc này như thể chúng là các đối tượng riêng lẻ. Dưới đây là một giải thích chi tiết về Composite Design Pattern:

### Mục đích của Composite Design Pattern
Composite Design Pattern được sử dụng để biểu diễn các hệ thống phân cấp mà trong đó các đối tượng có thể là các đối tượng phức tạp (có thể chứa các đối tượng khác) hoặc các đối tượng đơn giản (không chứa đối tượng khác). Nó cung cấp một cách để xử lý cả hai loại đối tượng theo cách nhất quán.

### Các thành phần chính
1. **Component (Thành phần):**
   - Đây là một giao diện hoặc lớp trừu tượng khai báo các phương thức mà các đối tượng trong cấu trúc cây cần phải triển khai.
   - Ví dụ: Một giao diện `Graphic` với các phương thức `draw()` và `add()`, `remove()` (nếu cần).

2. **Leaf (Lá):**
   - Đây là các đối tượng đơn giản, không thể chứa các đối tượng khác. Chúng thực thi giao diện `Component`.
   - Ví dụ: Một lớp `Circle` hoặc `Rectangle` thực thi giao diện `Graphic`.

3. **Composite (Hợp thành):**
   - Đây là các đối tượng phức tạp, có thể chứa các đối tượng khác (bao gồm cả `Leaf` và `Composite`). Chúng cũng thực thi giao diện `Component`.
   - Ví dụ: Một lớp `CompositeGraphic` chứa danh sách các đối tượng `Graphic` khác và thực thi các phương thức `draw()`, `add()`, `remove()`.

### Cách hoạt động
Composite Design Pattern cho phép bạn thao tác với các đối tượng riêng lẻ và các tổ hợp đối tượng theo cách nhất quán. Bạn có thể gọi các phương thức trên cả các đối tượng đơn giản và các đối tượng hợp thành mà không cần phân biệt chúng.

### Ví dụ
Tính giá của một order:
- Các sản phẩm riêng lẻ
- Các sản phẩm theo combo, có các loại combo to bọc combo con.
Cần tính được giá của toàn bộ sản phầm trong order này.

### Định nghĩa lại các lớp

1. **Giao diện chung (Component)**
2. **Lớp Product (Leaf)**
3. **Lớp Box (Composite) với phương thức mới**

```python
# Định nghĩa giao diện chung (Component)
from abc import ABC, abstractmethod

class Item(ABC):
    @abstractmethod
    def get_price(self):
        pass

# Định nghĩa lớp Product (Leaf)
class Product(Item):
    def __init__(self, price):
        self.price = price
    
    def get_price(self):
        return self.price

# Định nghĩa lớp Box (Composite) với phương thức mới
class Box(Item):
    def __init__(self):
        self.items = []
    
    def add_item(self, item):
        self.items.append(item)
    
    def remove_item(self, item):
        self.items.remove(item)
    
    def calculate_total_price(self):
        total = 0
        for item in self.items:
            total += item.get_price()  # Gọi phương thức get_price() của từng item
        return total
    
    # Triển khai phương thức get_price để tương thích với giao diện Item
    def get_price(self):
        return self.calculate_total_price()

# Sử dụng hệ thống
if __name__ == "__main__":
    # Tạo các sản phẩm đơn giản
    product1 = Product(10.0)
    product2 = Product(20.0)
    product3 = Product(30.0)

    # Tạo các hộp và thêm các sản phẩm vào hộp
    box1 = Box()
    box1.add_item(product1)
    box1.add_item(product2)

    box2 = Box()
    box2.add_item(box1)  # Thêm hộp nhỏ vào hộp lớn
    box2.add_item(product3)

    # Tính tổng giá của đơn hàng
    total_price = box2.calculate_total_price()
    print("Total price of the order:", total_price)  # Output: 60.0
```

### Giải thích chi tiết

1. **Giao diện `Item`**:
    - Giao diện này vẫn giữ nguyên với phương thức `get_price()`.

2. **Lớp `Product`**:
    - Lớp này không thay đổi, vẫn triển khai phương thức `get_price()` để trả về giá của sản phẩm.

3. **Lớp `Box`**:
    - Phương thức `calculate_total_price()` mới được thêm vào để tính tổng giá của tất cả các `item` trong hộp.
    - Phương thức `get_price()` được triển khai để tương thích với giao diện `Item`, và phương thức này gọi `calculate_total_price()` để trả về tổng giá.

4. **Sử dụng hệ thống**:
    - Khi gọi `box2.calculate_total_price()`, phương thức này sẽ tính tổng giá của tất cả các `item` trong `box2`.

### Tóm tắt

Bằng cách thay đổi tên phương thức trong lớp `Box`, bạn có thể làm cho mã nguồn trở nên rõ ràng và tường minh hơn mà vẫn giữ được tính nhất quán và tính đa hình của hệ thống. Phương thức `get_price()` vẫn được giữ lại để đảm bảo rằng lớp `Box` tương thích với giao diện `Item`, và nó chỉ đơn giản là gọi `calculate_total_price()` để tính toán tổng giá trị.

