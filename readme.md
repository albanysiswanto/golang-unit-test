# Unit Test di Go-Lang
![vscode](https://img.shields.io/badge/VSCode-0078D4?style=for-the-badge&logo=visual%20studio%20code&logoColor=white)
![go](https://img.shields.io/badge/Go-00ADD8?style=for-the-badge&logo=go&logoColor=white)
![github](https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white)
[![PkgGoDev](https://pkg.go.dev/badge/github.com/stretchr/testify)](https://pkg.go.dev/github.com/stretc)

## Menjalankan unit test
untuk menjalankan unit test:
1. ``go test`` menjalankan semua unit test
2. ``go test -v`` memberi informasi detail function mana saja yang telah di test
3. ``go test -v -run TestNamaFunction`` menjalankan unit test yang diinginkan
4. ``go test -v ./...`` untuk merunning semua package yang ada di dalam folder unit test.

## Mengagalkan test
Mengagalkan unit test menggunakan ``panic`` bukanlah hal yang bagus. Go-Lang sendiri menediakan cara untuk mengagalkan unit test menggunakan testing.T. Terdapat function ``Fail()``, ``FailNow()``, ``Error()``, dan ``Fatal()``jika ingin mangagalkan unit test.

1. ``Fail()`` akan mengagalkan unit test, namun tetap melanjutkan eksekusi unit test. Namun diakhir ketika selesai, maka unit test tersebut dianggap gagal.
```go
func TestHelloWorld(t *testing.T) {
	result := HelloWorld("Albany")
	if result != "Hello Albany" {
		// unit test gagal
		t.Fail()
	}
}
```
2. ``FailNow()`` akan mengagalkan saat ini juga, tanpa melanjutkan eksekusi unit test.
```go
func TestHelloWorld(t *testing.T) {
	result := HelloWorld("Albany")
	if result != "Hello Albany" {
		// unit test gagal
		t.FailNow()
	}
}
```
3. ``Erorr()`` function lebih seperti melakukan log (print) error, namun setelah melakukan log error, dia akan secara otomatis memanggil function ``Fail()``.
```go
func TestHelloWorld(t *testing.T) {
	result := HelloWorld("Albany")
	if result != "Hello Albany" {
		// unit test gagal
		t.Error("Result must be 'Hello World Albany'")
	}
}
```
4. ``Fatal()`` mirip dengan ``Error()``, hanya saja, setelah melakukan log error, dia akan memanggil function ``FailNow()``.
```go
func TestHelloWorld(t *testing.T) {
	result := HelloWorld("Albany")
	if result != "Hello Albany" {
		// unit test gagal
		t.Fatal("Result must be 'Hello World Albany'")
	}
}
```

## Assertions
- Salah satu library yang paling populer di Go-Lang adalah 
[Testify](https://github.com/stretchr/testify) .
```go
go get github.com/stretchr/testify
```
### Assert vs Require
- Assert = ``Fail()``
```go
func TestHelloWorldWithAssert(t *testing.T) {
	result := HelloWorld("Albany")
	assert.Equal(t, "Hello Albany", result, "Result must be 'Hello World Albany'")
	fmt.Println("TestHelloWorldWithAssert done")
}
```
- Require = ``FailNow()``
```go
func TestHelloWorldWithRequire(t *testing.T) {
	result := HelloWorld("Albany")
	require.Equal(t, "Hello Albany", result, "Result must be 'Hello World Albany'")
	fmt.Println("TestHelloWorldWithRequire done")
}
```
## Skip test
Skip test adalah proses dimana kita ingin membatalkan unit test **Bukan Mengagalkan**. Di Go-Lang terdapat function ``Skip()`` untuk membatalkan unit test.
```go
func TestSkip(t *testing.T) {
	if runtime.GOOS == "windows" {
		t.Skip("Can't run on Windows")
	}

	result := HelloWorld("Albany")
	require.Equal(t, "Hello Albany", result, "Result must be 'Hello World Albany'")
}
```

## Before and After Test
Jikalau kode yang kita lakukan sebelum dan sesudah selalu sama antar unit test function, maka membuat manual di unit test function nya adalah hal yang membosankan dan terlalu banyak kode yang duplikat. Di Go-Lang terdapat fitur yang bernama ``testing.M`` . Fitur ini bernama Main, dimana digunakan untuk mengatur eksekusi unit test, namun hal ini juga bisa kita gunakan untuk melakukan Before dan After di unit test.
```go
func TestMain(m *testing.M) {
	// Before Unit Test
	fmt.Println("BEFORE UNIT TEST")

	// Mengjalankan semua unit test
	m.Run()

	// Mengjalankan semua unit test
	fmt.Println("AFTER UNIT TEST")
}
```

## Sub test
Go-Lang mendukung fitur pembuatan function unit test didalam function unit test. Untuk membuat Sub test, kita bisa menggunakan function ``Run()``
```go
func TestSubTest(t *testing.T) {
	t.Run("Albany", func(t *testing.T) {
		result := HelloWorld("Albany")
		require.Equal(t, "Hello Albany", result, "Result must be 'Hello World Albany'")
	})
}
```
- Jika ingin menjalankan Sub test nya saja kalian bisa menggunakan perintah ``go test -v -run TestNameSubTest/SubTest``

## Table test
Table test yaitu dimana kita menyediakan data berupa slice yang berisi parameter dan ekpetasi hasil dari unit test. Lalu slice bersebut kita iterasi menggunakan **Sub test**.
```go
func TestTableTest(t *testing.T) {
	// Slice
	tests := []struct {
		name     string
		request  string
		expected string
	}{
		{
			name:     "Albany",
			request:  "Albany",
			expected: "Hello Albany",
		},
		{
			name:     "Budi",
			request:  "Budi",
			expected: "Hello Budi",
		},
	}

	for _, tests := range tests {
		t.Run(tests.name, func(t *testing.T) {
			result := HelloWorld(tests.request)
			require.Equal(t, tests.expected, result)
		})
	}
}
```

## Testify Mock
### Contoh kasus:
**Aplikasi Query ke Databse:** Kita akan coba contoh kasus dengan membuat contoh aplikasi golang yang melakukan query ke database. Dimana kida akan buat layer service sebagai business logic, dan layer Repository sebagai jembatan ke database. Agar kode kita mudah untuk di test, disarankan agar membuat _kontrak_ berupa _Interface_.

1. Pertama kita akan membuat folder ``Entity`` dan file contohnya ``catogory.go`` untuk merepresentasikan sebuah data dari database.
```go
package entity

type Category struct {
	Id   string
	Name string
}
```
2. Lalu berikutnya kita akan membuat folder ``Repository`` dan file contohnya ``category_repository`` untuk membuat kontrak berupa interface agar dapat menjalankan mock dengan baik.
```go
package repository

import "belajar-golang-unit-test/entity"

type CategoryRepository interface {
	FindById(id string) *entity.Category
}
```
3. Selanjutnya kita akan membuat Service nya, Idealnya kita membuat interface. Tetapi saat ini kita akan menggunakan Struct agar memudahkan kita. Sekarang kita akan membuat folder ``Service`` dan file ``category_service.go``.
```go
package service

import (
	"belajar-golang-unit-test/entity"
	"belajar-golang-unit-test/repository"
	"errors"
)

type CategoryService struct {
	Repository repository.CategoryRepository
}

func (service *CategoryService) Get(id string) (*entity.Category, error) {
	category := service.Repository.FindById(id)
	if category == nil {
		return nil, errors.New("Category not found")
	} else {
		return category, nil
	}
}
```
4. Setelah semua selesai, sekarang kita akan Menggunakan ``Testify Mock``.
Kode: Category Repository Mock. ``(file name : repository/category_repository_mock.go)``
```go
package repository

import (
	"belajar-golang-unit-test/entity"
	"github.com/stretchr/testify/mock"
)

type CategoryRepositoryMock struct {
	Mock mock.Mock
}

func (repository *CategoryRepositoryMock) FindById(id string) *entity.Category {
	arguments := repository.Mock.Called(id)
	if arguments.Get(0) == nil {
		return nil
	} else {
		category := arguments.Get(0).(entity.Category)
		return &category
	}
}

```
5. Selanjutnya kita akan melakukan unit test terhadap kode mock yang sudah kita buat tadi. Sekarang kita akan membuat file ``category_service_test.go`` pada folder ``Service``.
```go
package service

import (
	"belajar-golang-unit-test/entity"
	"belajar-golang-unit-test/repository"
	"testing"

	"github.com/stretchr/testify/assert"
	"github.com/stretchr/testify/mock"
)

var categoryRepository = &repository.CategoryRepositoryMock{Mock: mock.Mock{}}
var categoryService = CategoryService{Repository: categoryRepository}

func TestCategorySevice_GetNotFound(t *testing.T) {
	// program mock
	categoryRepository.Mock.On("FindById", "1").Return(nil)

	category, err := categoryService.Get("1")
	assert.Nil(t, category)
	assert.NotNil(t, err)
}

func TestCategorySevice_GetSuccess(t *testing.T) {
	category := entity.Category{
		Id:   "1",
		Name: "Laptop",
	}

	// program mock
	categoryRepository.Mock.On("FindById", "2").Return(category)

	result, err := categoryService.Get("2")
	assert.Nil(t, err)
	assert.NotNil(t, result)
	assert.Equal(t, category.Id, result.Id)
	assert.Equal(t, category.Name, result.Name)
}
```
