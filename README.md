## ✅ **Tổng hợp các phương thức thường dùng với `createQueryBuilder`**

```ts
const query = dataSource.createQueryBuilder('alias');
```

### 🧱 1. **`select()`** – chọn cột nào
```ts
.select(['user.id', 'user.name'])
```

### 🔗 2. **`innerJoin()` / `leftJoin()`**
```ts
.innerJoin('user.profile', 'profile')
.leftJoin('user.posts', 'posts')
```

### 🔍 3. **`innerJoinAndSelect()` / `leftJoinAndSelect()`**
Tương tự join nhưng tự động select luôn field của bảng join:
```ts
.leftJoinAndSelect('user.profile', 'profile')
```

### 🧪 4. **`where()` / `andWhere()` / `orWhere()`**
```ts
.where('user.age > :age', { age: 18 })
.andWhere('user.status = :status', { status: 'active' })
.orWhere('user.isAdmin = true')
```

### 🧠 5. **`orderBy()`**
```ts
.orderBy('user.createdAt', 'DESC')
```

### 📈 6. **`groupBy()` + `having()`**
```ts
.groupBy('user.department')
.having('COUNT(user.id) > 10')
```

### 🧮 7. **`addSelect()`**
Thêm field mới vào `select`, ví dụ:
```ts
.addSelect('COUNT(post.id)', 'postCount')
```

### 🎛 8. **`limit()` + `offset()` (hoặc dùng `take()` + `skip()` cho pagination)**
```ts
.take(10).skip(20)
```

### 🛑 9. **`getOne()` / `getMany()` / `getRawOne()` / `getRawMany()`**
- `getOne()` – trả về entity đầu tiên hoặc `undefined`
- `getMany()` – trả về mảng entity
- `getRawOne()` – trả về raw object (không map thành entity)
- `getRawMany()` – trả về mảng raw object

---

## 💡 **Best Practices khi dùng `createQueryBuilder` trong NestJS**

### ✅ 1. **Đặt alias rõ ràng**
```ts
.createQueryBuilder('course')
.leftJoinAndSelect('course.category', 'category')
```

### ✅ 2. **Tránh lặp lại điều kiện bằng cách dùng `if`**
```ts
if (user.isAdmin) {
  query.andWhere('user.role = :role', { role: 'admin' });
}
```

### ✅ 3. **Tránh `getRawMany()` nếu bạn cần full entity**
- Dùng `getMany()` để có entity đúng kiểu
- Chỉ dùng `getRawMany()` khi:
  - Select custom fields
  - Query phức tạp không map được sang entity

### ✅ 4. **Dùng `addSelect()` thay vì `select([...])` nếu muốn giữ select trước đó**

### ✅ 5. **Tránh hard-code tên cột, nếu có thể**
Dùng entity alias + property name để tránh lỗi refactor:
```ts
.orderBy('user.createdAt') // tốt hơn 'users.created_at'
```

### ✅ 6. **Đừng quên `.getQueryAndParameters()` để debug SQL**
```ts
console.log(query.getQuery(), query.getParameters());
```

---

## 🛠 Ví dụ thực tế NestJS

```ts
const courses = await this.courseRepo
  .createQueryBuilder('course')
  .leftJoinAndSelect('course.category', 'category')
  .leftJoinAndSelect('category.company', 'company')
  .where('company.id = :companyId', { companyId: user.companyId })
  .andWhere('course.isPublished = :published', { published: true })
  .orderBy('course.updatedAt', 'DESC')
  .take(10)
  .skip(0)
  .getMany();
```
