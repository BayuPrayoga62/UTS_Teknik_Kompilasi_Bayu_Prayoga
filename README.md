# UTS_Teknik_Kompilasi_Bayu_Prayoga

**Nama**        : Bayu Prayoga

**NIM**         : 231011402771

**Kelas**       : 06TPLE003

**Mata Kuliah** : Teknik Kompilasi

---

# Mini Compiler - Operator Pangkat (`^`)

Repositori ini berisi implementasi Mini Compiler sederhana menggunakan metode *Recursive Descent Parsing* di Python. Tugas utama dari modifikasi ini adalah menambahkan dukungan untuk operator pangkat (`^`) beserta penanganan *Operator Precedence* (hierarki operator) yang benar.

## Perubahan dan Fitur yang Ditambahkan

Berikut adalah ringkasan modifikasi yang dilakukan pada *source code* awal:

---

### 1. Pembaruan Lexer (Regular Expression)
* **Lokasi:** Fungsi `__init__` pada *class* `MiniCompiler`.
* **Perubahan:** Menambahkan karakter `\^` ke dalam himpunan Regular Expression pada variabel `self._tokens` agar proses *Lexical Analysis* (Lexer) dapat mengenali dan mengekstrak simbol pangkat sebagai token yang valid.
* **Kode:** ```python
    self._tokens = iter(re.findall(r'[a-zA-Z_]\w*|\d+(?:\.\d+)?|[+*/()\-\^]', source) + ['?'])
    ```

---

### 2. Implementasi Fungsi `power()`
* **Lokasi:** *Class* `MiniCompiler`.
* **Perubahan:** Membuat metode baru bernama `power()` untuk mem-parsing operator pangkat. Fungsi ini dibangun dengan logika perulangan `while` yang membaca token `^`, mengevaluasi sisi kiri dan kanan menggunakan `self.factor()`, lalu menggabungkannya ke dalam *Abstract Syntax Tree* (AST) melalui node `BinOp`.
* **Kode:**
    ```python
    def power(self):
        node = self.factor()
        while self._current == '^':
            op = self._current
            self.advance()
            node = BinOp(left=node, op=op, right=self.factor())
        return node
    ```

---

### 3. Penyesuaian *Operator Precedence* pada `term()`
* **Lokasi:** Fungsi `term()` pada *class* `MiniCompiler`.
* **Perubahan:** Untuk memastikan operator pangkat (`^`) memiliki hierarki/prioritas yang lebih tinggi daripada perkalian (`*`) dan pembagian (`/`), rujukan fungsi di dalam `term()` diubah. Pemanggilan `self.factor()` diganti menjadi `self.power()`. Ini memastikan kompilator mengevaluasi pangkat terlebih dahulu.
* **Kode:**
    ```python
    def term(self):
        node = self.power() 
        while self._current in ('*', '/'):
            op = self._current
            self.advance()
            node = BinOp(left=node, op=op, right=self.power()) 
        return node
    ```

---

## Hasil (Three Address Code)
Dengan modifikasi di atas, ketika diberikan input seperti `a ^ 2 + b * c`, kompilator akan menghasilkan *Three Address Code* (TAC) yang benar, di mana operasi pangkat (`^`) dan perkalian (`*`) dieksekusi terlebih dahulu dan disimpan dalam variabel *temporary* (seperti `t1`, `t2`) sebelum dijumlahkan (`+`).
