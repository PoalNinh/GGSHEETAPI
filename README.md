# Tài liệu sử dụng Google Sheets REST API

## Giới thiệu

API này cho phép bạn thực hiện các thao tác CRUD (Tạo, Đọc, Cập nhật, Xóa) với dữ liệu Google Sheets thông qua các lệnh REST đơn giản. API hỗ trợ thao tác với một bản ghi hoặc nhiều bản ghi cùng lúc.

## Thông tin cơ bản

- **URL API**: `https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID/exec`
- **Phương thức hỗ trợ**: GET, POST
- **Định dạng dữ liệu trả về**: JSON

## Tham số chung

| Tham số | Mô tả |
|---------|-------|
| `action` | Hành động cần thực hiện (`find`, `add`, `adds`, `edit`, `edits`, `delete`, `deletes`) |
| `sheet` | Tên của sheet cần thao tác (mặc định: "Sheet1") |

## Các API có sẵn

### 1. Tìm kiếm dữ liệu (`find`)

#### Tìm tất cả dữ liệu

```
GET ?action=find&sheet=TenSheet
```

#### Tìm theo ID

```
GET ?action=find&sheet=TenSheet&id=123
```

#### Phản hồi

```json
{
  "status": "success",
  "data": [
    {"id": "1", "ten": "Nguyễn Văn A", "tuoi": "30"},
    {"id": "2", "ten": "Trần Thị B", "tuoi": "25"}
  ]
}
```

### 2. Thêm một bản ghi (`add`)

```
GET/POST ?action=add&sheet=TenSheet&data={"ten":"Nguyễn Văn A","tuoi":"30"}
```

#### Phản hồi

```json
{
  "status": "success",
  "message": "Thêm dữ liệu thành công"
}
```

### 3. Thêm nhiều bản ghi cùng lúc (`adds`)

```
GET/POST ?action=adds&sheet=TenSheet&data=[{"ten":"Nguyễn Văn A","tuoi":"30"},{"ten":"Trần Thị B","tuoi":"25"}]
```

#### Phản hồi

```json
{
  "status": "success",
  "message": "Thêm nhiều dữ liệu thành công",
  "count": 2
}
```

### 4. Cập nhật một bản ghi (`edit`)

```
GET/POST ?action=edit&sheet=TenSheet&id=123&data={"ten":"Tên Mới"}
```

#### Phản hồi

```json
{
  "status": "success",
  "message": "Cập nhật dữ liệu thành công"
}
```

### 5. Cập nhật nhiều bản ghi cùng lúc (`edits`)

```
GET/POST ?action=edits&sheet=TenSheet&data=[{"id":"123","ten":"Tên Mới"},{"id":"124","tuoi":"35"}]
```

#### Phản hồi

```json
{
  "status": "success",
  "message": "Cập nhật nhiều dữ liệu thành công",
  "count": 2
}
```

### 6. Xóa một bản ghi (`delete`)

```
GET/POST ?action=delete&sheet=TenSheet&id=123
```

#### Phản hồi

```json
{
  "status": "success",
  "message": "Xóa dữ liệu thành công"
}
```

### 7. Xóa nhiều bản ghi cùng lúc (`deletes`)

```
GET/POST ?action=deletes&sheet=TenSheet&ids=["123","124","125"]
```

#### Phản hồi

```json
{
  "status": "success",
  "message": "Xóa nhiều dữ liệu thành công",
  "count": 3
}
```

## Mã trạng thái và thông báo lỗi

| Trạng thái | Mô tả |
|------------|-------|
| `success` | Thao tác thành công |
| `error` | Có lỗi xảy ra |

Các thông báo lỗi phổ biến:
- "Không tìm thấy sheet: [tên sheet]"
- "Không tìm thấy cột ID"
- "Không tìm thấy ID"
- "Hành động không hợp lệ"

## Ví dụ sử dụng với JavaScript

```javascript
// Tìm tất cả dữ liệu
async function findAll() {
    const API_URL = 'https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID/exec';
    try {
        const response = await fetch(`${API_URL}?action=find&sheet=Sheet1`);
        const data = await response.json();
        console.log(data);
    } catch (error) {
        console.error('Lỗi:', error);
    }
}

// Thêm dữ liệu mới
async function addData() {
    const API_URL = 'https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID/exec';
    const data = {
        id: Date.now().toString(),
        ten: "Nguyễn Văn A",
        tuoi: "30"
    };
    
    try {
        const response = await fetch(`${API_URL}?action=add&sheet=Sheet1&data=${JSON.stringify(data)}`);
        const result = await response.json();
        console.log(result);
    } catch (error) {
        console.error('Lỗi:', error);
    }
}
```

## Lưu ý quan trọng

1. **Cấu trúc dữ liệu**:
   - Sheet của bạn cần có hàng đầu tiên là tên các cột
   - Nên có một cột "id" để xác định duy nhất mỗi bản ghi

2. **Giới hạn**:
   - Google Apps Script có giới hạn về thời gian thực thi (30 giây)
   - Phương thức GET có giới hạn về độ dài URL (~2000 ký tự), nên dùng POST cho dữ liệu lớn

3. **Bảo mật**:
   - Nếu API bạn dùng cho dữ liệu nhạy cảm, hãy cân nhắc thiết lập xác thực

## Cách sử dụng với các ngôn ngữ/công nghệ khác

### PHP

```php
// Tìm tất cả dữ liệu
function findAll() {
    $api_url = 'https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID/exec?action=find&sheet=Sheet1';
    $response = file_get_contents($api_url);
    $data = json_decode($response, true);
    print_r($data);
}
```

### Python

```python
import requests
import json

# Tìm tất cả dữ liệu
def find_all():
    api_url = 'https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID/exec?action=find&sheet=Sheet1'
    response = requests.get(api_url)
    data = response.json()
    print(data)
```

### jQuery

```javascript
// Tìm tất cả dữ liệu
function findAll() {
    const API_URL = 'https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID/exec';
    
    $.ajax({
        url: API_URL,
        type: 'GET',
        data: {
            'action': 'find',
            'sheet': 'Sheet1'
        },
        success: function(response) {
            console.log(response);
        },
        error: function(error) {
            console.error('Lỗi:', error);
        }
    });
}
```

## Xử lý lỗi phổ biến

1. **Lỗi CORS**: Đảm bảo triển khai API với "Who has access" là "Anyone"
2. **Lỗi "Invalid argument"**: Kiểm tra lại định dạng dữ liệu JSON
3. **Lỗi "Sheet not found"**: Xác nhận tên sheet đã chính xác
4. **Lỗi "ID not found"**: Kiểm tra ID bạn đang tìm kiếm có tồn tại trong sheet không

Nếu bạn cần hỗ trợ thêm hoặc gặp vấn đề khác, vui lòng kiểm tra lỗi trả về từ API để biết thêm chi tiết.
