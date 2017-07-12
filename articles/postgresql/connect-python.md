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
ms.date: 06/23/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 0027d25bcaa3376c5a29299f3ec88809ebf1d2d8
ms.contentlocale: hu-hu
ms.lasthandoff: 06/26/2017

---
<a id="azure-database-for-postgresql-use-python-to-connect-and-query-data" class="xliff"></a>

# A PostgreSQL-hez készült Azure Database: Csatlakozás és adatlekérdezés a Python használatával
Ez a rövid útmutató ismerteti, hogyan használható a [Python](https://python.org) a PostgreSQL-hez készült Azure Database-hez való csatlakozáshoz, majd hogyan lehet az SQL-utasítások használatával adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban a Mac OS, Ubuntu Linux és a Windows platformokról. A jelen cikkben ismertetett lépések feltételezik, hogy Ön rendelkezik fejlesztési tapasztalatokkal a Python használatával kapcsolatosan, a PostgreSQL-hez készült Azure Database használatában pedig még járatlan.

<a id="prerequisites" class="xliff"></a>

## Előfeltételek
Ebben a rövid útmutatóban a következő útmutatók valamelyikében létrehozott erőforrásokat használunk kiindulási pontként:
- [DB létrehozása – portál](quickstart-create-server-database-portal.md)
- [DB létrehozása – CLI](quickstart-create-server-database-azure-cli.md)

Emellett a következőkre van szükség:
- telepített [python](https://www.python.org/downloads/)
- telepített [pip](https://pip.pypa.io/en/stable/installing/) csomag (a pip már telepítve van, ha a [python.org](https://python.org) webhelyről letöltött Python 2 >=2.7.9 vagy Python 3 >=3.4 bináris fájlokat használ, viszont szükség van a pip frissítésére)

<a id="install-the-python-connection-libraries-for-postgresql" class="xliff"></a>

## A PostgreSQL-hez készült Python-adatkapcsolattárak telepítése
Telepítse a [psycopg2](http://initd.org/psycopg/docs/install.html) csomagot, amely lehetővé teszi a csatlakozást és az adatbázis lekérdezését. A psycopg2 a [PyPI webhelyen férhető hozzá](https://pypi.python.org/pypi/psycopg2/) [kerék](http://pythonwheels.com/)csomagok formájában a leggyakoribb platformok esetében (Linux, OSX, Windows), ezért előfordulhat, hogy pip-telepítést kell használnia az összes függőséget magában foglaló modul bináris verziójának beszerzéséhez:

```cmd
pip install psycopg2
```
Ügyeljen arra, hogy naprakész verziójú pipet használ (amelynek frissítését például a `pip install -U pip` használatával végezheti el)

<a id="get-connection-information" class="xliff"></a>

## Kapcsolatadatok lekérése
Kérje le a PostgreSQL-hez készült Azure Database-hez való csatlakozáshoz szükséges kapcsolatadatokat. A teljes kiszolgálónévre és bejelentkezési hitelesítő adatokra van szükség.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Az Azure Portal baloldali menüjében kattintson az **Összes erőforrás** lehetőségre, és keressen rá az újonnan létrehozott kiszolgálóra **mypgserver-20170401**.
3. Kattintson a **mypgserver-20170401** kiszolgálónévre.
4. Válassza ki a kiszolgáló **Áttekintés** oldalát. Jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**.
 ![A PostgreSQL-hez készült Azure Database – Kiszolgáló-rendszergazdai bejelentkezés](./media/connect-python/1-connection-string.png)
5. Amennyiben elfelejtette a kiszolgáló bejelentkezési adatait, lépjen az **Overview** (Áttekintés) oldalra, és itt megtudhatja a kiszolgáló rendszergazdájának bejelentkezési nevét, valamint szükség esetén visszaállíthatja a jelszót.
   
<a id="connect-create-table-and-insert-data" class="xliff"></a>

## Csatlakozás, táblák létrehozása és adatok beszúrása
Az alábbi kód használatával csatlakozhat és töltheti be az adatokat az **INSERT** SQL-utasítással használt [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) függvény segítségével. A [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) függvény az SQL-lekérdezés PostgreSQL-adatbázison való végrehajtására szolgál. Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)
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

conn.commit()
```

<a id="read-data" class="xliff"></a>

## Adatok olvasása
Az alábbi kód használatával végezheti el a beillesztett adatok olvasását a **SELECT** SQL-utasítással használt [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) függvény segítségével. Ez a függvény lekérdezést fogad el, és olyan eredményt ad vissza, amely a [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) használatával ismételhető. Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print "Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2]))

conn.commit()
```

<a id="update-data" class="xliff"></a>

## Adatok frissítése
Az alábbi kód használatával végezheti el a korábban beillesztett leltári sor frissítését az **UPDATE** SQL-utasítással használt [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) függvény segítségével. Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print "Updated 1 row of data"

conn.commit()
```

<a id="delete-data" class="xliff"></a>

## Adat törlése
Az alábbi kód használatával végezheti el valamely korábban beillesztett leltári tétel törlését a **DELETE** SQL-utasítással használt [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) függvény segítségével. Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print ("Deleted 1 row of data")

conn.commit()
```

<a id="next-steps" class="xliff"></a>

## Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása az Exportálás és importálás lehetőség használatával](./howto-migrate-using-export-and-import.md)

