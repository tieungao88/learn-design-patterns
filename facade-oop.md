**Facade Design Pattern** là một trong những mô hình thiết kế (design pattern) thuộc nhóm cấu trúc (Structural Patterns) trong lập trình hướng đối tượng. Mục tiêu của mô hình này là cung cấp một giao diện đơn giản, dễ sử dụng cho một hệ thống phức tạp.
### 1. **Khái niệm chính**
- **Facade** là một lớp (class) cung cấp một giao diện đơn giản để tương tác với một hệ thống phức tạp của các lớp khác. Nó che giấu những chi tiết bên trong của hệ thống, chỉ cung cấp những chức năng cần thiết cho người sử dụng.
- **Lợi ích chính** của Facade là giúp giảm độ phức tạp khi tương tác với hệ thống, giúp mã nguồn dễ đọc và bảo trì hơn.

### 2. **Ví dụ về Facade**
- An app that uploads short funny videos with cats to social media could potentially use a professional video conversion library. However, all that it really needs is a class with the single method encode(filename, format). After creating such a class and connecting it with the video conversion library, you’ll have your first facade.
### Full Source Code
```python
from abc import ABC, abstractmethod

# Abstract class (interface)
class VideoConverter(ABC):
    @abstractmethod
    def load_video_file(self, filename):
        pass

    @abstractmethod
    def convert_to_format(self, format):
        pass

    @abstractmethod
    def save_file(self, filename):
        pass

# Implementation of the interface
class ComplexVideoConverterLibrary(VideoConverter):
    def load_video_file(self, filename):
        print(f"Loading video file: {filename}")
    
    def convert_to_format(self, format):
        print(f"Converting video to format: {format}")
    
    def save_file(self, filename):
        print(f"Saving converted file as: {filename}")

# Facade class
class VideoUploader:
    def __init__(self, converter: VideoConverter):
        self.converter = converter
    
    def encode(self, filename, format):
        self.converter.load_video_file(filename)
        self.converter.convert_to_format(format)
        output_filename = f"{filename.split('.')[0]}_converted.{format}"
        self.converter.save_file(output_filename)
        print(f"Video {output_filename} is ready to be uploaded to social media.")

# Client code
if __name__ == "__main__":
    converter = ComplexVideoConverterLibrary() # tạo một đối tượng converter từ lóp ComplexVideoConverterLibrary
    uploader = VideoUploader(converter) # truyền converter vào đối tượng self.converter của class VideoUploader
    uploader.encode("funny_cat_video.mp4", "avi")
```
Bất kỳ biến nào được gán bằng cú pháp `self.<tên_biến>` sẽ trở thành một thuộc tính (attribute) của đối tượng đó và có thể được truy cập từ các phương thức khác trong cùng đối tượng.

Đoạn code Python sử dụng Facade Pattern ở trên có thể được đánh giá dựa trên nguyên tắc SOLID. SOLID là một tập hợp năm nguyên tắc hướng đối tượng để tạo ra phần mềm dễ bảo trì, mở rộng và hiểu hơn. Chúng ta sẽ xem xét từng nguyên tắc SOLID và cách chúng áp dụng cho đoạn code này:

### 1. **Single Responsibility Principle (SRP) - Nguyên tắc trách nhiệm duy nhất**
- **Nguyên tắc**: Mỗi lớp chỉ nên có một trách nhiệm duy nhất, hay một lý do để thay đổi.
- **Đánh giá**: 
  - **ComplexVideoConverterLibrary**: Lớp này tuân thủ SRP vì nó chỉ có một trách nhiệm là quản lý quá trình chuyển đổi video (load, convert, save).
  - **VideoUploader**: Lớp này tuân thủ SRP vì nó chỉ có một trách nhiệm là đóng gói quá trình chuyển đổi video và cung cấp giao diện đơn giản cho việc sử dụng.

### 2. **Open/Closed Principle (OCP) - Nguyên tắc đóng/mở**
- **Nguyên tắc**: Các lớp nên mở để mở rộng, nhưng đóng để sửa đổi.
- **Đánh giá**:
  - **ComplexVideoConverterLibrary**: Nếu cần thêm các phương thức chuyển đổi mới hoặc định dạng mới, ta có thể mở rộng lớp này bằng cách thêm các phương thức mới mà không cần sửa đổi các phương thức hiện tại.
  - **VideoUploader**: Tuân thủ OCP, vì lớp này có thể mở rộng để hỗ trợ thêm các định dạng hoặc thư viện chuyển đổi khác mà không cần thay đổi mã hiện tại.

### 3. **Liskov Substitution Principle (LSP) - Nguyên tắc thay thế Liskov**
- **Nguyên tắc**: Các đối tượng của lớp con nên có thể thay thế được các đối tượng của lớp cha mà không làm thay đổi tính đúng đắn của chương trình.
- **Đánh giá**: 
  - Nguyên tắc này đã được áp dụng trong trường hợp này. Class `ComplexVideoConverterLibrary` phụ thuộc vào một class ảo `VideoConverter` do đó nếu cần thay thế `ComplexVideoConverterLibrary` bằng một lớp khác, `VideoUploader` sẽ vẫn hoạt động bình thường.

### 4. **Interface Segregation Principle (ISP) - Nguyên tắc phân chia giao diện**
- **Nguyên tắc**: Nên tránh tạo ra các giao diện lớn mà chứa quá nhiều phương thức không liên quan. Thay vào đó, nên tạo ra các giao diện nhỏ hơn và chuyên biệt.
- **Đánh giá**:
  - **ComplexVideoConverterLibrary**: Không áp dụng ISP trực tiếp vì không sử dụng giao diện trong ví dụ này, nhưng giả sử có, lớp này có thể cần nhiều giao diện nhỏ hơn để xử lý từng bước của quá trình chuyển đổi video.
  - **VideoUploader**: Tuân thủ ISP vì nó chỉ cung cấp một giao diện đơn giản với một phương thức `encode`.

### 5. **Dependency Inversion Principle (DIP) - Nguyên tắc đảo ngược sự phụ thuộc**
- **Nguyên tắc**: Các module cấp cao không nên phụ thuộc vào các module cấp thấp. Cả hai nên phụ thuộc vào các trừu tượng. Các trừu tượng không nên phụ thuộc vào chi tiết. Chi tiết nên phụ thuộc vào các trừu tượng.
- **Đánh giá**:
  - **VideoUploader**: Lớp `VideoUploader` phụ thuộc vào `VideoConverter` lớp trừu tượng (abstract class) mà `ComplexVideoConverterLibrary` triển khai. Điều này sẽ cho phép dễ dàng thay thế `ComplexVideoConverterLibrary` bằng một triển khai khác mà không cần thay đổi lớp `VideoUploader`.
