# Oracle PL/SQL – Nyelvi elemek szintaxisa

## 1. Változók létrehozása

PL/SQL-ben a változókat a deklarációs részben, a `DECLARE` kulcsszó után kell megadni. A változók a `BEGIN` utáni végrehajtható részben használhatók.

### 1.1. Általános szintaxis

```sql
DECLARE
    valtozonev adattipus;
BEGIN
    -- Utasítások
END;
/
```

### 1.2. Konkrét változók deklarálása

```sql
DECLARE
    v_name VARCHAR2(100);
    v_age NUMBER;
    v_date DATE;
    v_active BOOLEAN;
BEGIN
    v_name := 'Anna';
    v_age := 25;
    v_date := SYSDATE;
    v_active := TRUE;
END;
/
```

A `:=` operátor szolgál az értékadásra.

### 1.3. Kezdőérték megadása

```sql
DECLARE
    v_counter NUMBER := 0;
    v_name VARCHAR2(50) DEFAULT 'Ismeretlen';
BEGIN
    NULL;
END;
/
```

A kezdőérték megadható a `:=` operátorral vagy a `DEFAULT` kulcsszóval.

### 1.4. Konstans deklarálása

A `CONSTANT` kulcsszóval olyan változó hozható létre, amelynek értéke a deklarálás után nem módosítható.

```sql
DECLARE
    c_pi CONSTANT NUMBER := 3.14159;
BEGIN
    NULL;
END;
/
```

### 1.5. `%TYPE` használata

A `%TYPE` segítségével egy változó adattípusa egy táblamező vagy egy másik változó adattípusához igazítható.

```sql
DECLARE
    v_name employees.last_name%TYPE;
BEGIN
    NULL;
END;
/
```

Ebben az esetben a `v_name` változó típusa megegyezik az `employees.last_name` mező típusával.

Másik változó típusának átvétele:

```sql
DECLARE
    v_first NUMBER;
    v_second v_first%TYPE;
BEGIN
    NULL;
END;
/
```

### 1.6. `%ROWTYPE` használata

A `%ROWTYPE` segítségével egy teljes táblasor szerkezetének megfelelő rekordváltozó hozható létre.

```sql
DECLARE
    v_employee employees%ROWTYPE;
BEGIN
    SELECT *
    INTO v_employee
    FROM employees
    WHERE employee_id = 100;
END;
/
```

A rekord mezői a táblamezők nevével érhetők el:

```sql
DBMS_OUTPUT.PUT_LINE(v_employee.last_name);
```

---

## 2. Ciklusok (LOOP)

A PL/SQL három alapvető ciklustípust biztosít:

- Egyszerű `LOOP`
- `WHILE LOOP`
- `FOR LOOP`

### 2.1. Egyszerű LOOP

Az egyszerű `LOOP` addig fut, amíg a ciklust explicit módon meg nem szakítjuk.

**Szintaxis:**

```sql
LOOP
    -- Utasítások

    EXIT WHEN feltetel;
END LOOP;
```

Az `EXIT WHEN` utasítás akkor szakítja meg a ciklust, ha a feltétel igaz.

**Példa:**

```sql
DECLARE
    v_counter NUMBER := 1;
BEGIN
    LOOP
        DBMS_OUTPUT.PUT_LINE(v_counter);

        v_counter := v_counter + 1;

        EXIT WHEN v_counter > 5;
    END LOOP;
END;
/
```

A ciklus az 1-től 5-ig terjedő számokat írja ki.

A `LOOP` használható az `EXIT` utasítással is:

```sql
LOOP
    -- Utasítások

    IF feltetel THEN
        EXIT;
    END IF;
END LOOP;
```

### 2.2. WHILE LOOP

A `WHILE LOOP` addig hajtja végre az utasításokat, amíg a megadott feltétel igaz.

**Szintaxis:**

```sql
WHILE feltetel LOOP
    -- Utasítások
END LOOP;
```

**Példa:**

```sql
DECLARE
    v_counter NUMBER := 1;
BEGIN
    WHILE v_counter <= 5 LOOP
        DBMS_OUTPUT.PUT_LINE(v_counter);

        v_counter := v_counter + 1;
    END LOOP;
END;
/
```

A feltétel minden iteráció előtt ellenőrzésre kerül. Ha már az első ellenőrzéskor hamis, a ciklus egyszer sem fut le.

### 2.3. FOR LOOP

A `FOR LOOP` egy meghatározott számtartományon halad végig. A ciklusváltozót a PL/SQL automatikusan kezeli.

**Szintaxis:**

```sql
FOR ciklusvaltozo IN also_hatar..felso_hatar LOOP
    -- Utasítások
END LOOP;
```

**Példa:**

```sql
BEGIN
    FOR i IN 1..5 LOOP
        DBMS_OUTPUT.PUT_LINE(i);
    END LOOP;
END;
/
```

A ciklusváltozó automatikusan növekszik, ezért külön értékadással nem kell módosítani.

#### Fordított irányú FOR LOOP

A `REVERSE` kulcsszóval a ciklus csökkenő sorrendben halad végig a tartományon.

```sql
BEGIN
    FOR i IN REVERSE 1..5 LOOP
        DBMS_OUTPUT.PUT_LINE(i);
    END LOOP;
END;
/
```

A kiírt értékek sorrendje: 5, 4, 3, 2, 1.

### 2.4. Ciklus megszakítása és átugrása

A ciklus futása az `EXIT` segítségével megszakítható:

```sql
EXIT;
```

Feltételes megszakítás:

```sql
EXIT WHEN feltetel;
```

A `CONTINUE` segítségével az aktuális iteráció hátralévő része átugorható:

```sql
CONTINUE;
```

Feltételes átugrás:

```sql
CONTINUE WHEN feltetel;
```

**Példa:**

```sql
BEGIN
    FOR i IN 1..10 LOOP
        CONTINUE WHEN MOD(i, 2) = 0;

        DBMS_OUTPUT.PUT_LINE(i);
    END LOOP;
END;
/
```

Ez a példa csak a páratlan számokat írja ki.

---

## 3. Típusok (TYPE)

A PL/SQL-ben a `TYPE` kulcsszóval saját adattípusok hozhatók létre. A típus lehet például rekord vagy kollekció.

Fontos különbség, hogy a PL/SQL-blokkon belül deklarált típus nem azonos az adatbázisban önállóan létrehozott SQL-típussal.

### 3.1. RECORD típus

A `RECORD` segítségével olyan összetett típus hozható létre, amely különböző adattípusú mezőket tartalmazhat.

**Szintaxis:**

```sql
DECLARE
    TYPE tipusnev IS RECORD (
        mezo1 adattipus,
        mezo2 adattipus,
        mezo3 adattipus
    );

    valtozonev tipusnev;
BEGIN
    NULL;
END;
/
```

**Példa:**

```sql
DECLARE
    TYPE ty_employee IS RECORD (
        employee_id NUMBER,
        employee_name VARCHAR2(100),
        salary NUMBER
    );

    v_employee ty_employee;
BEGIN
    v_employee.employee_id := 1;
    v_employee.employee_name := 'Anna';
    v_employee.salary := 500000;

    DBMS_OUTPUT.PUT_LINE(v_employee.employee_name);
END;
/
```

A rekord mezői ponttal érhetők el, például `v_employee.salary`.

### 3.2. Kollekciótípus létrehozása

A kollekció több azonos típusú elemet tárolhat. A PL/SQL-ben többféle kollekciótípus létezik, például az asszociatív tömb, a `VARRAY` és a beágyazott tábla.

#### 3.2.1. Asszociatív tömb

```sql
DECLARE
    TYPE tipusnev IS TABLE OF adattipus
        INDEX BY PLS_INTEGER;

    valtozonev tipusnev;
BEGIN
    NULL;
END;
/
```

**Példa:**

```sql
DECLARE
    TYPE ty_numbers IS TABLE OF NUMBER
        INDEX BY PLS_INTEGER;

    v_numbers ty_numbers;
BEGIN
    v_numbers(1) := 10;
    v_numbers(2) := 20;

    DBMS_OUTPUT.PUT_LINE(v_numbers(1));
END;
/
```

Az asszociatív tömb index alapján tárolja az elemeket.

#### 3.2.2. Beágyazott tábla (Nested Table)

PL/SQL-en belüli deklarálás:

```sql
DECLARE
    TYPE tipusnev IS TABLE OF adattipus;

    valtozonev tipusnev;
BEGIN
    NULL;
END;
/
```

A változót inicializálni kell, mielőtt elemeket helyezünk bele:

```sql
DECLARE
    TYPE ty_numbers IS TABLE OF NUMBER;

    v_numbers ty_numbers := ty_numbers();
BEGIN
    v_numbers.EXTEND;
    v_numbers(1) := 10;

    DBMS_OUTPUT.PUT_LINE(v_numbers(1));
END;
/
```

A `ty_numbers()` a kollekció inicializálására szolgál. Az `EXTEND` új elemet ad hozzá a kollekcióhoz.

#### 3.2.3. VARRAY

A `VARRAY` meghatározott maximális elemszámú kollekció.

```sql
DECLARE
    TYPE tipusnev IS VARRAY(max_elemszam) OF adattipus;

    valtozonev tipusnev;
BEGIN
    NULL;
END;
/
```

**Példa:**

```sql
DECLARE
    TYPE ty_numbers IS VARRAY(5) OF NUMBER;

    v_numbers ty_numbers := ty_numbers(10, 20, 30);
BEGIN
    DBMS_OUTPUT.PUT_LINE(v_numbers(1));
END;
/
```

A `VARRAY` legfeljebb öt elemet tárolhat.

### 3.3. SQL-szintű objektumtípus létrehozása

Az adatbázisban önállóan létrehozható objektumtípus a `CREATE TYPE` utasítással.

**Szintaxis:**

```sql
CREATE OR REPLACE TYPE tipusnev AS OBJECT (
    mezo1 adattipus,
    mezo2 adattipus
);
/
```

**Példa:**

```sql
CREATE OR REPLACE TYPE ty_vacc_type AS OBJECT (
    vacc_type_id NUMBER,
    vacc_name VARCHAR2(100)
);
/
```

Az így létrehozott típus objektumokat reprezentálhat, amelyek mezői ponttal érhetők el.

### 3.4. SQL-szintű kollekciótípus létrehozása

SQL-szintű beágyazott tábla az `AS TABLE OF` szintaxissal hozható létre.

```sql
CREATE OR REPLACE TYPE tipusnev AS TABLE OF elemtipus;
/
```

**Példa:**

```sql
CREATE OR REPLACE TYPE ty_vacc_type_l
AS TABLE OF ty_vacc_type;
/
```

Ez a típus a korábban létrehozott `ty_vacc_type` objektumtípus elemeiből álló kollekciót definiál.

A két típus együtt használható például a `BULK COLLECT` utasítással történő adatlekérésnél.

---
### Konstruktorok (CONSTRUCTOR)

Az Oracle objektumtípusokhoz automatikusan létrejön egy alapértelmezett konstruktor, amelynek neve megegyezik az objektumtípus nevével. A konstruktor az objektum attribútumainak értékeit paraméterként kapja meg, a deklarációban meghatározott sorrendben.

#### 1. Alapértelmezett konstruktor

Szintaxis:

```
tipusnev(ertek1, ertek2, ertek3)
```

Példa – objektumtípus létrehozása:

```
CREATE OR REPLACE TYPE ty_employee AS OBJECT (
    employee_id NUMBER,
    employee_name VARCHAR2(100),
    salary NUMBER
);
/
```

Objektum létrehozása konstruktorral:

```
DECLARE
    v_employee ty_employee;
BEGIN
    v_employee := ty_employee(
        1,
        'Anna',
        500000
    );
END;
/
```

A `ty_employee(1, 'Anna', 500000)` konstruktor létrehozza az objektumpéldányt, és hozzárendeli az értékeket az attribútumokhoz.

#### 2. Saját konstruktor (CONSTRUCTOR FUNCTION)

Az Oracle-ben saját konstruktor is definiálható a `CONSTRUCTOR FUNCTION` segítségével. Ezzel egyedi inicializálási logika valósítható meg az objektum létrehozásakor.

Szintaxis – konstruktor deklarálása:

```
CREATE OR REPLACE TYPE tipusnev AS OBJECT (
    attribútum1 adattipus,
    attribútum2 adattipus,

    CONSTRUCTOR FUNCTION tipusnev (
        parameter1 adattipus,
        parameter2 adattipus
    ) RETURN SELF AS RESULT
);
/
```

Konstruktor megvalósítása:

```
CREATE OR REPLACE TYPE BODY tipusnev AS

    CONSTRUCTOR FUNCTION tipusnev (
        parameter1 adattipus,
        parameter2 adattipus
    ) RETURN SELF AS RESULT IS
    BEGIN
        SELF.attribútum1 := parameter1;
        SELF.attribútum2 := parameter2;

        RETURN;
    END;

END;
/
```

- `CONSTRUCTOR FUNCTION`: saját konstruktor deklarálása.
- `RETURN SELF AS RESULT`: a konstruktor az aktuális objektumpéldányt adja vissza.
- `SELF`: az aktuális objektumpéldányra hivatkozik.
- `SELF.attribútum`: az objektum adott attribútumának elérésére szolgál.
- `RETURN`: visszaadja az inicializált objektumot.

Konkrét példa:

```
CREATE OR REPLACE TYPE ty_person AS OBJECT (
    person_id NUMBER,
    person_name VARCHAR2(100),

    CONSTRUCTOR FUNCTION ty_person (
        p_id NUMBER,
        p_name VARCHAR2
    ) RETURN SELF AS RESULT
);
/
```

A konstruktor megvalósítása:

```
CREATE OR REPLACE TYPE BODY ty_person AS

    CONSTRUCTOR FUNCTION ty_person (
        p_id NUMBER,
        p_name VARCHAR2
    ) RETURN SELF AS RESULT IS
    BEGIN
        SELF.person_id := p_id;
        SELF.person_name := UPPER(p_name);

        RETURN;
    END;

END;
/
```

Meghívás:

```
DECLARE
    v_person ty_person;
BEGIN
    v_person := ty_person(1, 'Anna');

    DBMS_OUTPUT.PUT_LINE(v_person.person_name);
END;
/
```

A példában a konstruktor a név értékét automatikusan nagybetűssé alakítja az objektum létrehozásakor.

## 4. Package-ek

A **package** összetartozó PL/SQL-elemeket fog össze, például típusokat, változókat, konstansokat, eljárásokat és függvényeket.

A package két fő részből áll:

1. **Package specification (specifikáció):** meghatározza a kívülről elérhető elemeket.
2. **Package body (törzs):** tartalmazza az eljárások és függvények megvalósítását.

### 4.1. Package specification

**Szintaxis:**

```sql
CREATE OR REPLACE PACKAGE package_nev AS

    -- Típusok
    -- Változók és konstansok
    -- Procedure deklarációk
    -- Function deklarációk

END package_nev;
/
```

**Példa:**

```sql
CREATE OR REPLACE PACKAGE pkg_employee AS

    c_max_salary CONSTANT NUMBER := 1000000;

    TYPE ty_employee IS RECORD (
        employee_id NUMBER,
        employee_name VARCHAR2(100)
    );

    PROCEDURE print_employee(
        p_employee_id IN NUMBER
    );

    FUNCTION get_employee_name(
        p_employee_id IN NUMBER
    ) RETURN VARCHAR2;

END pkg_employee;
/
```

A specifikációban deklarált elemek a megfelelő jogosultságok és láthatósági szabályok mellett a package-en kívülről is elérhetők.

### 4.2. Package body

**Szintaxis:**

```sql
CREATE OR REPLACE PACKAGE BODY package_nev AS

    -- Eljárások és függvények megvalósítása

END package_nev;
/
```

**Példa:**

```sql
CREATE OR REPLACE PACKAGE BODY pkg_employee AS

    PROCEDURE print_employee(
        p_employee_id IN NUMBER
    ) IS
    BEGIN
        DBMS_OUTPUT.PUT_LINE(p_employee_id);
    END print_employee;


    FUNCTION get_employee_name(
        p_employee_id IN NUMBER
    ) RETURN VARCHAR2 IS
    BEGIN
        RETURN 'Anna';
    END get_employee_name;

END pkg_employee;
/
```

A package törzsének eljárásai és függvényei a specifikációban deklarált aláírással összhangban valósítják meg a műveleteket.

### 4.3. Package-ben deklarált elemek használata

A package nyilvános elemei a package nevével hivatkozhatók.

**Eljárás meghívása:**

```sql
BEGIN
    pkg_employee.print_employee(100);
END;
/
```

**Függvény meghívása:**

```sql
DECLARE
    v_name VARCHAR2(100);
BEGIN
    v_name := pkg_employee.get_employee_name(100);

    DBMS_OUTPUT.PUT_LINE(v_name);
END;
/
```

A package-ben deklarált típusra is hivatkozhatunk:

```sql
DECLARE
    v_employee pkg_employee.ty_employee;
BEGIN
    v_employee.employee_id := 1;
    v_employee.employee_name := 'Anna';
END;
/
```

---

## 5. Procedure – tárolt eljárás

A `PROCEDURE` olyan eljárást definiál, amely műveleteket hajt végre. Paramétereket fogadhat, és `OUT` vagy `IN OUT` paramétereken keresztül értékeket is visszaadhat.

### 5.1. Procedure létrehozása

**Szintaxis:**

```sql
CREATE OR REPLACE PROCEDURE procedure_nev (
    parameter1 [IN | OUT | IN OUT] adattipus,
    parameter2 [IN | OUT | IN OUT] adattipus
)
AS
    -- Lokális változók
BEGIN
    -- Utasítások
EXCEPTION
    -- Hibakezelés
END procedure_nev;
/
```

Az `AS` helyett az `IS` kulcsszó is használható.

### 5.2. Paraméterátadási módok

| Mód | Jelentés |
|---|---|
| `IN` | Bemenő paraméter. Az eljárás értéket kap. Ez az alapértelmezett mód. |
| `OUT` | Kimenő paraméter. Az eljárás ezen keresztül adhat vissza értéket. |
| `IN OUT` | Bemenő és kimenő paraméter. Az eljárás megkapja, majd módosíthatja az értéket. |

### 5.3. Procedure példa

```sql
CREATE OR REPLACE PROCEDURE increase_salary (
    p_salary IN OUT NUMBER
)
AS
BEGIN
    p_salary := p_salary * 1.10;
END increase_salary;
/
```

Az eljárás 10%-kal növeli a bemenő fizetésértéket, majd az új értéket visszaadja a paraméteren keresztül.

**Meghívás:**

```sql
DECLARE
    v_salary NUMBER := 500000;
BEGIN
    increase_salary(v_salary);

    DBMS_OUTPUT.PUT_LINE(v_salary);
END;
/
```

Az eljárás meghívásakor az `IN OUT` paraméterhez olyan változót kell átadni, amely módosítható.

### 5.4. Procedure `OUT` paraméterrel

```sql
CREATE OR REPLACE PROCEDURE get_double (
    p_number IN NUMBER,
    p_result OUT NUMBER
)
AS
BEGIN
    p_result := p_number * 2;
END get_double;
/
```

**Meghívás:**

```sql
DECLARE
    v_result NUMBER;
BEGIN
    get_double(10, v_result);

    DBMS_OUTPUT.PUT_LINE(v_result);
END;
/
```

A `v_result` értéke 20 lesz.

---

## 6. Function – tárolt függvény

A `FUNCTION` olyan PL/SQL-programot definiál, amelynek kötelezően van visszatérési értéke. A visszatérési érték típusát a `RETURN` kulcsszó után kell megadni.

### 6.1. Function létrehozása

**Szintaxis:**

```sql
CREATE OR REPLACE FUNCTION function_nev (
    parameter1 [IN] adattipus,
    parameter2 [IN] adattipus
)
RETURN visszateresi_tipus
AS
    -- Lokális változók
BEGIN
    -- Utasítások

    RETURN visszateresi_ertek;
EXCEPTION
    -- Hibakezelés
END function_nev;
/
```

A függvény paraméterei alapértelmezés szerint `IN` módúak. A `RETURN` utasítás adja vissza a függvény eredményét.

### 6.2. Function példa

```sql
CREATE OR REPLACE FUNCTION calculate_sum (
    p_a IN NUMBER,
    p_b IN NUMBER
)
RETURN NUMBER
AS
BEGIN
    RETURN p_a + p_b;
END calculate_sum;
/
```

**Meghívás PL/SQL-blokkból:**

```sql
DECLARE
    v_result NUMBER;
BEGIN
    v_result := calculate_sum(10, 20);

    DBMS_OUTPUT.PUT_LINE(v_result);
END;
/
```

Az eredmény 30.

### 6.3. Function meghívása SQL-lekérdezésből

A függvény bizonyos feltételek teljesülése esetén SQL-utasításban is használható.

```sql
SELECT calculate_sum(10, 20)
FROM dual;
```

Az, hogy egy PL/SQL-függvény SQL-ből meghívható-e, függ többek között a függvény működésétől és az Oracle SQL–PL/SQL interoperabilitási szabályaitól.

---

## 7. BULK COLLECT

A `BULK COLLECT` lehetővé teszi, hogy egy SQL-lekérdezés több eredménysorát egyszerre egy PL/SQL-kollekcióba töltsük.

Hagyományos `SELECT ... INTO` esetén a lekérdezés egyetlen sort adhat vissza. A `BULK COLLECT` ezzel szemben több sort is képes kollekcióba gyűjteni.

### 7.1. BULK COLLECT – alapvető szintaxis

```sql
SELECT oszlopok
BULK COLLECT INTO kollekcio
FROM tabla
WHERE feltetel;
```

A célváltozónak a lekérdezés eredményével kompatibilis típusú kollekciónak kell lennie.

### 7.2. BULK COLLECT objektumtípussal

Ha az eredmény objektumtípusú elemekből álló kollekcióba kerül, az objektumkonstruktort is használhatjuk.

**Objektumtípus létrehozása:**

```sql
CREATE OR REPLACE TYPE ty_vacc_type AS OBJECT (
    vacc_type_id NUMBER,
    vacc_name VARCHAR2(100)
);
/
```

**Kollekciótípus létrehozása:**

```sql
CREATE OR REPLACE TYPE ty_vacc_type_l
AS TABLE OF ty_vacc_type;
/
```

**Adatok lekérése:**

```sql
DECLARE
    v_list ty_vacc_type_l;
BEGIN
    SELECT ty_vacc_type(
               t.vacc_type_id,
               t.vacc_name
           )
    BULK COLLECT INTO v_list
    FROM vacc_type t;

    FOR i IN 1..v_list.COUNT LOOP
        DBMS_OUTPUT.PUT_LINE(v_list(i).vacc_type_id);
        DBMS_OUTPUT.PUT_LINE(v_list(i).vacc_name);
    END LOOP;
END;
/
```

Ebben a példában:

- A `ty_vacc_type` egy objektumtípus.
- A `ty_vacc_type_l` a `ty_vacc_type` elemeit tartalmazó kollekciótípus.
- A `BULK COLLECT INTO v_list` a lekérdezés összes visszaadott sorát a kollekcióba tölti.
- A `COUNT` megadja a kollekció elemeinek számát.
- A `v_list(i)` az `i` indexű objektumot jelöli.
- A `v_list(i).vacc_type_id` és a `v_list(i).vacc_name` az adott objektum mezőit éri el.

### 7.3. BULK COLLECT WHERE feltétellel

A `BULK COLLECT` szokásos SQL-feltételekkel is kombinálható.

```sql
DECLARE
    v_list ty_vacc_type_l;
BEGIN
    SELECT ty_vacc_type(
               t.vacc_type_id,
               t.vacc_name
           )
    BULK COLLECT INTO v_list
    FROM vacc_type t
    WHERE t.vacc_type_id LIKE '%B%';

    FOR i IN 1..v_list.COUNT LOOP
        DBMS_OUTPUT.PUT_LINE(v_list(i).vacc_name);
    END LOOP;
END;
/
```

A kollekcióba csak azok a sorok kerülnek, amelyek megfelelnek a `WHERE` feltételnek.

### 7.4. Kollekciók összefűzése `MULTISET UNION ALL` segítségével

SQL-szintű beágyazott táblák esetén a `MULTISET UNION ALL` két kollekció elemeit egyesíti. Az `ALL` változat megtartja a többször előforduló elemeket is.

```sql
DECLARE
    v_list1 ty_vacc_type_l;
    v_list2 ty_vacc_type_l;
    v_result ty_vacc_type_l;
BEGIN
    SELECT ty_vacc_type(
               t.vacc_type_id,
               t.vacc_name
           )
    BULK COLLECT INTO v_list1
    FROM vacc_type t
    WHERE t.vacc_name LIKE '%B%';

    SELECT ty_vacc_type(
               t.vacc_type_id,
               t.vacc_name
           )
    BULK COLLECT INTO v_list2
    FROM vacc_type t
    WHERE t.vacc_name LIKE '%A%';

    v_result := v_list1 MULTISET UNION ALL v_list2;

    FOR i IN 1..v_result.COUNT LOOP
        DBMS_OUTPUT.PUT_LINE(v_result(i).vacc_type_id);
        DBMS_OUTPUT.PUT_LINE(v_result(i).vacc_name);
    END LOOP;
END;
/
```

A `MULTISET UNION ALL` használatához a két kollekció típusának kompatibilisnek kell lennie.

### 7.5. BULK COLLECT kurzorral és LIMIT-tel

Nagy mennyiségű adat feldolgozásakor célszerű lehet az adatokat kisebb adagokban lekérni. Erre használható a `FETCH ... BULK COLLECT INTO ... LIMIT`.

**Általános szintaxis:**

```sql
DECLARE
    CURSOR cursor_nev IS
        SELECT oszlopok
        FROM tabla;

    TYPE tipusnev IS TABLE OF cursor_nev%ROWTYPE;

    v_collection tipusnev;
BEGIN
    OPEN cursor_nev;

    LOOP
        FETCH cursor_nev
        BULK COLLECT INTO v_collection
        LIMIT elemszam;

        EXIT WHEN v_collection.COUNT = 0;

        FOR i IN 1..v_collection.COUNT LOOP
            -- Feldolgozás
            NULL;
        END LOOP;
    END LOOP;

    CLOSE cursor_nev;
END;
/
```

A `LIMIT` meghatározza, hogy egyetlen `FETCH` során legfeljebb hány sor kerüljön a kollekcióba.

**Fontos:** a `BULK COLLECT`-tel közvetlenül végrehajtott `SELECT` esetén a teljes eredményhalmaz bekerülhet a kollekcióba. A kurzoros, `LIMIT`-tel végzett változat lehetőséget ad az adatok adagokban történő feldolgozására.

---

## 8. Kivételkezelés (EXCEPTION)

A PL/SQL-blokkokban a futás közben keletkező hibák kezelésére az `EXCEPTION` rész használható.

**Szintaxis:**

```sql
DECLARE
    -- Deklarációk
BEGIN
    -- Végrehajtható utasítások
EXCEPTION
    WHEN hiba_neve THEN
        -- Hibakezelés
    WHEN OTHERS THEN
        -- Egyéb hibák kezelése
END;
/
```

**Példa:**

```sql
DECLARE
    v_result NUMBER;
BEGIN
    v_result := 10 / 0;
EXCEPTION
    WHEN ZERO_DIVIDE THEN
        DBMS_OUTPUT.PUT_LINE('Nullával való osztás történt.');
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('Egyéb hiba történt.');
END;
/
```

A `WHEN OTHERS` az egyéb, korábban külön nem kezelt kivételeket fogja meg. Éles alkalmazásokban célszerű a hibákat megfelelően naplózni vagy továbbadni, nem csupán kiírni.

---

## 9. Összefoglaló szintaxisok

| Nyelvi elem | Alapvető szintaxis |
|---|---|
| Változó deklarálása | `valtozo adattipus;` |
| Változó értékadása | `valtozo := ertek;` |
| Konstans | `nev CONSTANT adattipus := ertek;` |
| Táblaoszlop típusának átvétele | `valtozo tabla.oszlop%TYPE;` |
| Teljes sor típusának átvétele | `valtozo tabla%ROWTYPE;` |
| Egyszerű ciklus | `LOOP ... EXIT WHEN feltetel; END LOOP;` |
| WHILE ciklus | `WHILE feltetel LOOP ... END LOOP;` |
| FOR ciklus | `FOR i IN also..felso LOOP ... END LOOP;` |
| RECORD típus | `TYPE nev IS RECORD (...);` |
| PL/SQL-kollekció | `TYPE nev IS TABLE OF adattipus;` |
| SQL-objektumtípus | `CREATE TYPE nev AS OBJECT (...);` |
| SQL-kollekciótípus | `CREATE TYPE nev AS TABLE OF elemtipus;` |
| Package specifikáció | `CREATE OR REPLACE PACKAGE nev AS ... END nev;` |
| Package törzs | `CREATE OR REPLACE PACKAGE BODY nev AS ... END nev;` |
| Procedure | `CREATE OR REPLACE PROCEDURE nev (...) AS ... BEGIN ... END;` |
| Function | `CREATE OR REPLACE FUNCTION nev (...) RETURN tipus AS ... BEGIN RETURN ertek; END;` |
| BULK COLLECT | `SELECT ... BULK COLLECT INTO kollekcio FROM tabla;` |
| Kurzoros BULK COLLECT | `FETCH kurzor BULK COLLECT INTO kollekcio LIMIT elemszam;` |
| Kollekciók egyesítése | `kollekcio1 MULTISET UNION ALL kollekcio2` |
| Kivételkezelés | `EXCEPTION WHEN hiba_neve THEN ...` |
