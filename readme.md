# Belajar unit test di golang

## Menjalankan unit test
untuk menjalankan unit test:
1. ``go test`` menjalankan semua unit test
2. ``go test -v`` memberi informasi detail function mana saja yang telah di test
3. ``go test -v -run TestNamaFunction`` menjalankan unit test yang diinginkan
4. ``go test -v ./...`` untuk merunning semua package yang ada di dalam folder unit test.

## Menggalkan test
Mengagalkan unit test menggunakan ``panic`` bukanlah hal yang bagus. Go-Lang sendiri menediakan cara untuk mengagalkan unit test menggunakan testing.T. Terdapat function ``Fail()``, ``FailNow()``, ``Error()``, dan ``Fatal()``jika ingin mangagalkan unit test.

1. ``Fail()`` akan mengagalkan unit test, namun tetap melanjutkan eksekusi unit test. Namun diakhir ketika selesai, maka unit test tersebut dianggap gagal.
2. ``Fail()`` akan mengagalkan saat ini juga, tanpa melanjutkan eksekusi unit test.
3. ``Erorr()`` function lebih seperti melakukan log (print) error, namun setelah melakukan log error, dia akan secara otomatis memanggil function ``Fail()``.
4. ``Fatal()`` mirip dengan ``Error()``, hanya saja, setelah melakukan log error, dia akan memanggil function ``FailNow()``.

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