# SISTEM BASIS DATA — Keamanan & Hak Akses (Firebird SQL)

## 1. Instalasi (Firebird + FlameRobin) & Setup

### 🔗 Link Unduhan:
- Firebird: [https://firebirdsql.org/en/firebird-5-0](https://firebirdsql.org/en/firebird-5-0)
- FlameRobin GUI: [https://github.com/mariuz/flamerobin/releases/tag/0.9.7](https://github.com/mariuz/flamerobin/releases/tag/0.9.7)

### 🔧 Setup Awal:
- Set password default: `masterkey` (default Firebird)
- Gunakan FlameRobin untuk manajemen GUI atau CLI (isql) untuk akses terminal.

---

## 2. Langkah-Langkah Memulai via CLI (Command Prompt)

1. Buka CMD → Run as Administrator  
2. Arahkan ke folder `isql`:
   - 64-bit:  
    `cd "C:\Program Files\Firebird\Firebird_5_0\bin"`
   - 32-bit:  
    `cd "C:\Program Files (x86)\Firebird\Firebird_5_0\bin"`

3. Jalankan Firebird SQL CLI:
    ```bash
    isql
    ```

4. Buat Database:

    ```sql
    CREATE DATABASE 'C:\FirebirdDB\SECURITY_PROJECT.FDB' 
    USER 'SYSDBA' PASSWORD 'masterkey';
    ```

5. Buat Tabel:

    ```sql
    CREATE TABLE employees (
        id INT PRIMARY KEY,
        name VARCHAR(50),
        role VARCHAR(30)
    );

    CREATE TABLE attendance (
        id INT PRIMARY KEY,
        employee_id INT,
        attend_date DATE,
        status VARCHAR(20)
    );

    COMMIT;
    ```

6. Masukkan Data Awal:

    ```sql
    INSERT INTO employees VALUES (1, 'MAULANA', 'Admin');
    INSERT INTO employees VALUES (2, 'ZAHRA', 'Staff');

    INSERT INTO attendance VALUES (1, 1, CURRENT_DATE, 'Present');
    COMMIT;
    ```

7. Uji Query:

    ```sql
    SELECT * FROM employees;
    SELECT * FROM attendance;
    ```

8. Reconnect Database jika perlu:

    ```bash
    isql -user 'SYSDBA' -password 'masterkey'
    CONNECT "C:\FirebirdDB\SECURITY_PROJECT.FDB";
    ```

---

## 3. Pengaturan Hak Akses

### 👥 Daftar User:

* MAULANA GHANI ROLANDA → `maulana`
* DIDIK SETIAWAN → `didik`
* ZAHRA TSUROYYA POETRI → `zahra`
* ARSYA FATHIHA RAHMAN → `arsya`
* ROZHAK → `rozhak`

### 🧑‍💻 Pembuatan User:

```sql
CREATE USER maulana PASSWORD 'pwd_maulana';
CREATE USER didik PASSWORD 'pwd_didik';
CREATE USER zahra PASSWORD 'pwd_zahra';
CREATE USER arsya PASSWORD 'pwd_arsya';
CREATE USER rozhak PASSWORD 'pwd_rozhak';
COMMIT;
```

### 🔐 GRANT Hak Akses:

```sql
GRANT SELECT, INSERT ON employees TO maulana;

GRANT SELECT, INSERT ON attendance TO zahra;

GRANT UPDATE (name) ON employees TO didik;
GRANT SELECT ON employees TO didik;

GRANT DELETE, SELECT ON attendance TO arsya;

GRANT SELECT ON employees TO rozhak;
GRANT SELECT ON attendance TO rozhak;

COMMIT;
```

---

## 4. Contoh Tabel Pengaturan Akses

| No | Subject | Access | Object                |
| -- | ------- | ------ | --------------------- |
| #  | A       | Own    | Table 1               |
| #  | C       | Own    | Table 2               |
| #  | D       | Own    | Table 3               |
| 1  | B       | Read   | Table 1               |
| 2  | B       | Update | Table 1 (col x)       |
| 3  | B       | Insert | Table 2 (col f, h, d) |
| 4  | C       | Insert | Table 1               |
| 5  | D       | Delete | Table 2 (any row)     |
| n  | etc     | …      | …                     |

---

## 5. Simulasi Login & Praktik Akses

### 🔄 Logout dari isql

```sql
EXIT;
```

### 🔑 Login & Akses Data:

#### 🧑 Maulana

```bash
isql
CONNECT "C:\FirebirdDB\SECURITY_PROJECT.FDB" USER 'maulana' PASSWORD 'pwd_maulana';

SELECT * FROM employees;
INSERT INTO employees VALUES (3, 'MAULANA TEST', 'Admin');
COMMIT;
```

#### 🧑 Zahra

```bash
isql
CONNECT "C:\FirebirdDB\SECURITY_PROJECT.FDB" USER 'zahra' PASSWORD 'pwd_zahra';

SELECT * FROM attendance;
INSERT INTO attendance VALUES (2, 2, CURRENT_DATE, 'Present');
COMMIT;
```

#### 🧑 Didik

```bash
isql
CONNECT "C:\FirebirdDB\SECURITY_PROJECT.FDB" USER 'didik' PASSWORD 'pwd_didik';

SELECT * FROM employees;
UPDATE employees SET name = 'DIDIK EDIT' WHERE id = 2;
COMMIT;
```

#### 🧑 Arsya

```bash
isql
CONNECT "C:\FirebirdDB\SECURITY_PROJECT.FDB" USER 'arsya' PASSWORD 'pwd_arsya';

SELECT * FROM attendance;
DELETE FROM attendance WHERE id = 2;
COMMIT;
```

#### 🧑 Rozhak

```bash
isql
CONNECT "C:\FirebirdDB\SECURITY_PROJECT.FDB" USER 'rozhak' PASSWORD 'pwd_rozhak';

SELECT * FROM employees;
SELECT * FROM attendance;
```

---

## 🔐 6. REVOKE Hak Akses

### 🔸 Perintah REVOKE

```sql
REVOKE SELECT ON employees FROM rozhak;
COMMIT;
```

### 🔸 Uji Akses Setelah REVOKE

```sql
SELECT * FROM employees;
```

---
