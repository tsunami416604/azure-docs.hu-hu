---
title: "Csatlakozás a PostgreSQL-hez készült Azure Database-hez a Pythonnal | Microsoft Docs"
description: "Biztosítja a PostgreSQL-hez készült Azure Database-hez csatlakozásra és adatlekérdezésre használható Python-kódmintát."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: python
ms.topic: hero-article
ms.date: 07/07/2017
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 3cd090b02887857a68271f021e3580e05660d1dc
ms.contentlocale: hu-hu
ms.lasthandoff: 07/13/2017

---
# <a name="azure-database-for-postgresql-use-python-to-connect-and-query-data"></a>A PostgreSQL-hez készült Azure Database: Csatlakozás és adatlekérdezés a Python használatával
Ez a rövid útmutató ismerteti, hogyan használható a [Python](https://python.org) a PostgreSQL-hez készült Azure Database-hez való csatlakozáshoz, majd hogyan lehet az SQL-utasítások használatával adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban a Mac OS, Ubuntu Linux és a Windows platformokról. A jelen cikkben ismertetett lépések feltételezik, hogy Ön rendelkezik fejlesztési tapasztalatokkal a Python használatával kapcsolatosan, a PostgreSQL-hez készült Azure Database használatában pedig még járatlan.

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató az alábbi útmutatók valamelyikében létrehozott erőforrásokat használja kiindulópontként:
- [DB létrehozása – portál](quickstart-create-server-database-portal.md)
- [DB létrehozása – CLI](quickstart-create-server-database-azure-cli.md)

Emellett a következőkre van szükség:
- telepített [python](https://www.python.org/downloads/)
- telepített [pip](https://pip.pypa.io/en/stable/installing/) csomag (a pip már telepítve van, ha a [python.org](https://python.org) webhelyről letöltött Python 2 >=2.7.9 vagy Python 3 >=3.4 bináris fájlokat használ, viszont szükség van a pip frissítésére)

## <a name="install-the-python-connection-libraries-for-postgresql"></a>A PostgreSQL-hez készült Python-adatkapcsolattárak telepítése
Telepítse a [psycopg2](http://initd.org/psycopg/docs/install.html) csomagot, amely lehetővé teszi a csatlakozást és az adatbázis lekérdezését. A psycopg2 a [PyPI webhelyen férhető hozzá](https://pypi.python.org/pypi/psycopg2/) [kerék](http://pythonwheels.com/)csomagok formájában a leggyakoribb platformok esetében (Linux, OSX, Windows), ezért előfordulhat, hogy pip-telepítést kell használnia az összes függőséget magában foglaló modul bináris verziójának beszerzéséhez:

```cmd
pip install psycopg2
```
Ügyeljen arra, hogy naprakész verziójú pipet használ (amelynek frissítését például a `pip install -U pip` használatával végezheti el)

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le a PostgreSQL-hez készült Azure-adatbázishoz való csatlakozáshoz szükséges kapcsolatadatokat. Szüksége lesz a teljes kiszolgálónévre és a bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Az Azure Portal baloldali menüjében kattintson az **Összes erőforrás** lehetőségre, és keressen rá az újonnan létrehozott kiszolgálóra **mypgserver-20170401**.
3. Kattintson a **mypgserver-20170401** kiszolgálónévre.
4. Válassza ki a kiszolgáló **Áttekintés** oldalát. Jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**.
 ![PostgreSQL-hez készült Azure-adatbázis – Kiszolgáló-rendszergazdai bejelentkezés](./media/connect-python/1-connection-string.png)
5. Amennyiben elfelejtette a kiszolgálója bejelentkezési adatait, lépjen az **Áttekintés** oldalra, ahol kikeresheti a kiszolgáló-rendszergazda bejelentkezési nevét, valamint szükség esetén új jelszót kérhet.

## <a name="how-to-run-python-code"></a>Python-kód futtatása
- A kedvenc szövegszerkesztőjével hozza létre a postgres.py nevű fájlt, és mentse egy projektmappába. Másolja és illessze be az alább látható mintakódot a szövegfájlba, és mentse azt. Válassza az UTF-8 kódolást, ha a fájlt a Windows operációs rendszerben menti. 
- A kód futtatásához nyissa meg a parancssort vagy a bash rendszerhéjat. Módosítsa a könyvtárat a projektmappájára, például: `cd postgresql`. Ezután írja be a python-parancsot, majd a fájlnevet, például: `python postgresql.py`.

> [!NOTE]
> A Python 3. verziójától kezdődően a `SyntaxError: Missing parentheses in call to 'print'` hibát láthatja az alábbi kódblokkok futtatásakor. Ha ez történik, cserélje le a `print "string"` parancs minden hívását egy zárójelet használó függvényhívással, például a következővel: `print("string")`.

## <a name="connect-create-table-and-insert-data"></a>Csatlakozás, táblák létrehozása és adatok beszúrása
Az alábbi kód használatával csatlakozhat és töltheti be az adatokat az **INSERT** SQL-utasítással használt [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) függvény segítségével. A [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) függvény az SQL-lekérdezés PostgreSQL-adatbázison való végrehajtására szolgál. Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print "Finished dropping table (if existed)"

# Create table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print "Finished creating table"

# Insert some data into table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print "Inserted 3 rows of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="read-data"></a>Adatok olvasása
Az alábbi kód használatával végezheti el a beillesztett adatok olvasását a **SELECT** SQL-utasítással használt [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) függvény segítségével. Ez a függvény lekérdezést fogad el, és olyan eredményt ad vissza, amely a [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) használatával ismételhető. Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print "Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2]))

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>Adatok frissítése
Az alábbi kód használatával végezheti el a korábban beillesztett leltári sor frissítését az **UPDATE** SQL-utasítással használt [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) függvény segítségével. Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print "Updated 1 row of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>Adat törlése
Az alábbi kód használatával végezheti el valamely korábban beillesztett leltári tétel törlését a **DELETE** SQL-utasítással használt [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) függvény segítségével. Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print "Deleted 1 row of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md)

