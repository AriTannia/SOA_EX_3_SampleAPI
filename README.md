# README - SampleAPI

## Giới thiệu
**SampleAPI** là một RESTful API được xây dựng bằng ASP.NET Core, sử dụng MongoDB để quản lý thông tin sách. API cung cấp các chức năng CRUD cơ bản cho đối tượng `Book`.

---

## Cấu trúc dự án

### 1. **Controllers**
#### **BooksController.cs**
- **Namespace**: `SampleAPI.Controllers`
- Điều khiển các yêu cầu HTTP liên quan đến tài nguyên `Book`.

**Các phương thức chính:**
- `GET /api/books`: Lấy danh sách tất cả sách.
- `GET /api/books/{id}`: Lấy thông tin sách theo ID.
- `POST /api/books`: Thêm sách mới.
- `PUT /api/books/{id}`: Cập nhật sách theo ID.
- `DELETE /api/books/{id}`: Xóa sách theo ID.

---

### 2. **Models**
#### **Book.cs**
- **Namespace**: `SampleAPI.Models`
- Mô hình dữ liệu cho đối tượng `Book`.

**Các thuộc tính:**
- `Id`: ID của sách (chuỗi ObjectId).
- `BookName`: Tên sách.
- `Price`: Giá sách (kiểu `decimal`).
- `Category`: Thể loại sách.
- `Author`: Tác giả.

#### **BookStoreDatabaseSettings.cs**
- **Namespace**: `SampleAPI.Models`
- Cấu hình kết nối cơ sở dữ liệu.

**Các thuộc tính:**
- `ConnectionString`: Chuỗi kết nối tới MongoDB.
- `DatabaseName`: Tên cơ sở dữ liệu.
- `BooksCollectionName`: Tên của bộ sưu tập sách.

---

### 3. **Services**
#### **BooksService.cs**
- **Namespace**: `SampleAPI.Services`
- Cung cấp các phương thức thao tác với cơ sở dữ liệu.

**Các phương thức chính:**
- `GetAsync()`: Lấy danh sách tất cả sách.
- `GetAsync(string id)`: Lấy sách theo ID.
- `CreateAsync(Book newBook)`: Thêm sách mới.
- `UpdateAsync(string id, Book updatedBook)`: Cập nhật sách theo ID.
- `RemoveAsync(string id)`: Xóa sách theo ID.

**Khởi tạo MongoDB Client:**
```csharp
var mongoClient = new MongoClient(bookStoreDatabaseSettings.Value.ConnectionString);
var mongoDatabase = mongoClient.GetDatabase(bookStoreDatabaseSettings.Value.DatabaseName);
_booksCollection = mongoDatabase.GetCollection<Book>(bookStoreDatabaseSettings.Value.BooksCollectionName);
```

---

### 4. Program.cs
Cấu hình và khởi chạy ứng dụng. 
```csharp
builder.Services.Configure<BookStoreDatabaseSettings>(
    builder.Configuration.GetSection("BookStoreDatabase")
);
builder.Services.AddSingleton<BooksService>();
builder.Services.AddControllers()
    .AddJsonOptions(options => options.JsonSerializerOptions.PropertyNamingPolicy = null);
builder.Services.AddSwaggerGen();
```

Thiết lập Swagger và pipeline:
```csharp
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}
app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();
app.Run();
