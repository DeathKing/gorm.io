---
title: Membuat
layout: page
---

## Buat Catatan

```go
user := User{Name: "Jinzhu", Age: 18, Birthday: time.Now()}

db.NewRecord(user) // => returns `true` as primary key is blank

db.Create(&user)

db.NewRecord(user) // => return `false` after `user` created
```

## Nilai Bawaan

Anda dapat mendefinisikan nilai default dari sebuah field dengan sebuah tanda. Contohnya:

```go
type Animal struct {
    ID   int64
    Name string `gorm:"default:'galeone'"`
    Age  int64
}
```

Lalu penambahan SQL akan mengecualikan fields yang tidak memiliki nilai atau yang memiliki [nilai nol](https://tour.golang.org/basics/12). Setelah memasukkan record ke database, gorm akan memuat nilai-nilai field tersebut dari database.

```go
var animal = Animal{Age: 99, Name: ""}
db.Create(&animal)
// INSERT INTO animals("age") values('99');
// SELECT name from animals WHERE ID=111; // the returning primary key is 111
// animal.Name => 'galeone'
```

**CATATAN** semua field yang memiliki nilai nol, seperti `0`, `''`, `false` atau [nilai nol](https://tour.golang.org/basics/12) lain, tidak akan disimpan ke database tapi akan menggunakan nilai default. Apabila anda ingin mencegal hal ini, gunakan tipe pointer atau scanner/valuer, seperti:

```go
// Use pointer value
type User struct {
  gorm.Model
  Name string
  Age  *int `gorm:"default:18"`
}

// Use scanner/valuer
type User struct {
  gorm.Model
  Name string
  Age  sql.NullInt64 `gorm:"default:18"`
}
```

## Menetapkan Nilai Bidang Dalam Hooks

Apabila anda ingin memperbaharui nilai dari sebuah field dalam hook `BeforeCreate`, anda dapat menggunakan `scope.SetColumn`, contohnya:

```go
func (user *User) BeforeCreate(scope *gorm.Scope) error {
  scope.SetColumn("ID", uuid.New())
  return nil
}
```

## Pilihan Membuat Tambahan

```go
// Add extra SQL option for inserting SQL
db.Set("gorm:insert_option", "ON CONFLICT").Create(&product)
// INSERT INTO products (name, code) VALUES ("name", "code") ON CONFLICT;
```