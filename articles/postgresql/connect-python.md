---
title: Csatlakozás python- Azure Database for PostgreSQL - Single Server
description: Ez a rövid útmutató python-kódmintákat biztosít, amelyek segítségével adatokat csatlakoztathat és lekérdezhet az Azure Database for PostgreSQL – Single Server szolgáltatásból.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: python
ms.topic: quickstart
ms.date: 11/07/2019
ms.openlocfilehash: 3694c0b74393068538a0c8f496444a1541d88fee
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76769063"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Rövid útmutató: A Python használatával adatokat csatlakoztathat és kérdezhessen a PostgreSQL Azure Database for PostgreSQL szolgáltatásban – Egyetlen kiszolgáló

Ebben a rövid útmutatóban egy Azure Database for PostgreSQL használatával dolgozik a Python használatával macOS, Ubuntu Linux vagy Windows rendszeren. A rövid útmutató bemutatja, hogyan csatlakozhat az adatbázishoz, és hogyan használhatja az SQL-utasításokat az adatok lekérdezéséhez, beszúrásához, frissítéséhez és törléséhez. A cikk feltételezi, hogy ismeri a Pythont, de még új, az Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- A [rövid útmutató befejezése: Hozzon létre egy Azure Database for PostgreSQL-kiszolgálót az Azure Portalon](quickstart-create-server-database-portal.md) vagy a [Rövid útmutatóban: Hozzon létre egy Azure Database for PostgreSQL-t az Azure CLI használatával.](quickstart-create-server-database-azure-cli.md)
  
- [Python](https://www.python.org/downloads/) 2.7.9+ vagy 3.4+.
  
- Legújabb [pip](https://pip.pypa.io/en/stable/installing/) csomag telepítő.

## <a name="install-the-python-libraries-for-postgresql"></a>A PostgreSQL Python-kódtárainak telepítése
A [psycopg2](https://pypi.python.org/pypi/psycopg2/) modul lehetővé teszi a PostgreSQL-adatbázishoz való csatlakozást és lekérdezést, és Linux, macOS vagy Windows [kerékcsomagként](https://pythonwheels.com/) érhető el. Telepítse a modul bináris verzióját, beleértve az összes függőséget. A telepítéssel `psycopg2` és a követelményekkel kapcsolatos további tudnivalókért olvassa el a [Telepítés című témakört.](http://initd.org/psycopg/docs/install.html) 

A `psycopg2`telepítéshez nyisson meg egy terminált vagy parancssort, és futtassa a parancsot. `pip install psycopg2`

## <a name="get-database-connection-information"></a>Adatbázis-kapcsolat adatainak beírása
A PostgreSQL-adatbázishoz való csatlakozáshoz a teljesen minősített kiszolgálónévre és bejelentkezési hitelesítő adatokra van szükség. Ezeket az információkat az Azure Portalon kaphatja meg.

1. Az [Azure Portalon](https://portal.azure.com/)keresse meg és válassza ki az Azure Database for PostgreSQL-kiszolgáló nevét. 
1. A kiszolgáló **áttekintése** lapon másolja a teljesen minősített **kiszolgáló nevét** és a **rendszergazdai felhasználónevet**. A teljesen minősített **kiszolgálónév** mindig a * \<saját kiszolgálónév>.postgres.database.azure.com*formában, a **rendszergazdai felhasználónév** pedig mindig a * \<saját admin-felhasználónév>@\<my-server-name>* formában. 
   
   Szüksége van a rendszergazdai jelszavára is. Ha elfelejti, visszaállíthatja ezt az oldalt. 
   
   ![Azure Database for PostgreSQL-kiszolgáló neve](./media/connect-python/1-connection-string.png)

## <a name="how-to-run-the-python-examples"></a>A Python-példák futtatása

A cikkben szereplő minden egyes kódpéldához:

1. Új fájl létrehozása szövegszerkesztőben. 
   
1. Adja hozzá a példakódot a fájlhoz. A kódban cserélje ki a következőket:
   - `<server-name>`és `<admin-username>` az Azure Portalról másolt értékekkel.
   - `<admin-password>`a kiszolgáló jelszavával.
   - `<database-name>`az Azure Database for PostgreSQL adatbázis nevével. A rendszer automatikusan létrehoz egy *postgres* nevű alapértelmezett adatbázist a kiszolgáló létrehozásakor. Az adatbázist átnevezheti, vagy sql-parancsokkal új adatbázist hozhat létre. 
   
1. Mentse a fájlt a projektmappába *.py* kiterjesztéssel, például *postgres-insert.py*. Windows esetén győződjön meg arról, hogy az UTF-8 kódolás ki van jelölve a fájl mentésekor. 
   
1. A fájl futtatásához váltson a projektmappára egy `python` parancssori felületen, `python postgres-insert.py`és írja be a fájlnevet, például .

## <a name="create-a-table-and-insert-data"></a>Táblázat létrehozása és adatok beszúrása
A következő kódpélda csatlakozik az Azure Database for PostgreSQL adatbázishoz a [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) függvény használatával, és betölti az adatokat egy SQL **INSERT** utasítással. A [kurzor.execute](http://initd.org/psycopg/docs/cursor.html#execute) függvény végrehajtja az SQL-lekérdezést az adatbázison. 

```Python
import psycopg2

# Update connection string information 
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")

# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")

# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")

# Clean up
conn.commit()
cursor.close()
conn.close()
```

A kód sikeres futtatásakor a következő kimenetet hozza létre:

![Parancssori kimenet](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>Adatok olvasása
A következő kódpélda csatlakozik az Azure Database for PostgreSQL adatbázishoz, és [a cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) és az SQL **SELECT** utasítás t használja az adatok olvasásához. Ez a függvény elfogad egy lekérdezést, és a [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall)segítségével visszaadja az eredményhalmazt. 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>Adatok frissítése
A következő kódpélda csatlakozik az Azure Database for PostgreSQL adatbázishoz, és [a cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) használatával az SQL **UPDATE** utasítást használja az adatok frissítéséhez. 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>Adat törlése
A következő kódpélda csatlakozik az Azure Database for PostgreSQL adatbázishoz, és [a cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) használatával az SQL **DELETE** utasítással törli a korábban beszúrt készletelemet. 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md)
