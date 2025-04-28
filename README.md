# Tài liệu sử dụng Google Sheets REST API

## Giới thiệu

API này cho phép bạn thực hiện các thao tác CRUD (Tạo, Đọc, Cập nhật, Xóa) với dữ liệu Google Sheets thông qua các lệnh REST đơn giản. API hỗ trợ thao tác với một bản ghi hoặc nhiều bản ghi cùng lúc.

## Thông tin cơ bản

- **URL API**: `https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID/exec`
- **Phương thức hỗ trợ**: GET, POST
- **Định dạng dữ liệu trả về**: JSON

## Các API có sẵn

### 1. Tìm kiếm dữ liệu (`find`)

#### Sử dụng GET

```
GET ?action=find&sheet=TenSheet
```

Hoặc tìm theo ID:
```
GET ?action=find&sheet=TenSheet&id=123
```

#### Sử dụng POST

```json
{
  "action": "find",
  "sheet": "TenSheet",
  "id": "123" // Không bắt buộc, nếu không có thì trả về tất cả dữ liệu
}
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

#### Sử dụng GET

```
GET ?action=add&sheet=TenSheet&data={"ten":"Nguyễn Văn A","tuoi":"30"}
```

#### Sử dụng POST

```json
{
  "action": "add",
  "sheet": "TenSheet",
  "data": {
    "ten": "Nguyễn Văn A",
    "tuoi": "30"
  }
}
```

#### Phản hồi

```json
{
  "status": "success",
  "message": "Thêm dữ liệu thành công"
}
```

### 3. Thêm nhiều bản ghi cùng lúc (`adds`)

#### Sử dụng GET

```
GET ?action=adds&sheet=TenSheet&data=[{"ten":"Nguyễn Văn A","tuoi":"30"},{"ten":"Trần Thị B","tuoi":"25"}]
```

#### Sử dụng POST (Khuyên dùng)

```json
{
  "action": "adds",
  "sheet": "TenSheet",
  "data": [
    {"ten": "Nguyễn Văn A", "tuoi": "30"},
    {"ten": "Trần Thị B", "tuoi": "25"}
  ]
}
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

#### Sử dụng GET

```
GET ?action=edit&sheet=TenSheet&id=123&data={"ten":"Tên Mới"}
```

#### Sử dụng POST

```json
{
  "action": "edit",
  "sheet": "TenSheet",
  "id": "123",
  "data": {
    "ten": "Tên Mới"
  }
}
```

#### Phản hồi

```json
{
  "status": "success",
  "message": "Cập nhật dữ liệu thành công"
}
```

### 5. Cập nhật nhiều bản ghi cùng lúc (`edits`)

#### Sử dụng GET

```
GET ?action=edits&sheet=TenSheet&data=[{"id":"123","ten":"Tên Mới"},{"id":"124","tuoi":"35"}]
```

#### Sử dụng POST (Khuyên dùng)

```json
{
  "action": "edits",
  "sheet": "TenSheet",
  "data": [
    {"id": "123", "ten": "Tên Mới"},
    {"id": "124", "tuoi": "35"}
  ]
}
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

#### Sử dụng GET

```
GET ?action=delete&sheet=TenSheet&id=123
```

#### Sử dụng POST

```json
{
  "action": "delete",
  "sheet": "TenSheet",
  "id": "123"
}
```

#### Phản hồi

```json
{
  "status": "success",
  "message": "Xóa dữ liệu thành công"
}
```

### 7. Xóa nhiều bản ghi cùng lúc (`deletes`)

#### Sử dụng GET

```
GET ?action=deletes&sheet=TenSheet&ids=["123","124","125"]
```

#### Sử dụng POST (Khuyên dùng)

```json
{
  "action": "deletes",
  "sheet": "TenSheet",
  "ids": ["123", "124", "125"]
}
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
- "Lỗi định dạng JSON trong body"

## Ví dụ sử dụng với JavaScript

### Sử dụng phương thức GET

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

### Sử dụng phương thức POST (Khuyên dùng cho dữ liệu lớn)

```javascript
// Thêm dữ liệu sử dụng POST
async function addDataWithPost() {
    const API_URL = 'https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID/exec';
    
    const postData = {
        action: "add",
        sheet: "Sheet1",
        data: {
            id: Date.now().toString(),
            ten: "Nguyễn Văn A",
            tuoi: "30"
        }
    };
    
    try {
        const response = await fetch(API_URL, {
            method: 'POST',
            body: JSON.stringify(postData),
            headers: {
                'Content-Type': 'application/json'
            }
        });
        const result = await response.json();
        console.log(result);
    } catch (error) {
        console.error('Lỗi:', error);
    }
}

// Cập nhật nhiều bản ghi sử dụng POST
async function editMultipleWithPost() {
    const API_URL = 'https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID/exec';
    
    const postData = {
        action: "edits",
        sheet: "Sheet1",
        data: [
            {id: "123", ten: "Tên Đã Sửa 1"},
            {id: "124", tuoi: "45"}
        ]
    };
    
    try {
        const response = await fetch(API_URL, {
            method: 'POST',
            body: JSON.stringify(postData),
            headers: {
                'Content-Type': 'application/json'
            }
        });
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
   - Triển khai với "Who has access" được cấu hình phù hợp

4. **Hiệu suất**:
   - Thao tác hàng loạt (adds, edits, deletes) hiệu quả hơn so với nhiều thao tác đơn lẻ
   - Nên giới hạn số lượng bản ghi trong một lần gọi API để tránh vượt quá thời gian thực thi

5. **Xử lý CORS**:
   - Google Apps Script tự động xử lý CORS khi triển khai làm Web App
   - Đảm bảo cấu hình triển khai cho phép truy cập từ nguồn bạn cần

## Xử lý lỗi phổ biến

1. **Lỗi CORS**: Đảm bảo triển khai API với "Who has access" là "Anyone"
2. **Lỗi "Invalid argument"**: Kiểm tra lại định dạng dữ liệu JSON
3. **Lỗi "Sheet not found"**: Xác nhận tên sheet đã chính xác
4. **Lỗi "ID not found"**: Kiểm tra ID bạn đang tìm kiếm có tồn tại trong sheet không
5. **Lỗi "Script has exceeded maximum execution time"**: Giảm khối lượng dữ liệu xử lý trong một lần gọi

Nếu bạn cần hỗ trợ thêm hoặc gặp vấn đề khác, vui lòng kiểm tra lỗi trả về từ API để biết thêm chi tiết.
