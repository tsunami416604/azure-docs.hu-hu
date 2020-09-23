---
title: 'Gyors útmutató: kapcsolat a Python-Azure Database for PostgreSQL-Single Serverrel'
description: Ez a rövid útmutató olyan Python-kódrészleteket tartalmaz, amelyekkel összekapcsolhatók és lekérhető Azure Database for PostgreSQL – egyetlen kiszolgálóról származó adatok lekérdezése.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 11/07/2019
ms.openlocfilehash: 9fb0c02bcf040b1d27831e72d31ff07a7c38ad0a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90901821"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Rövid útmutató: a Python használata az Azure Database for PostgreSQL-kiszolgálóval való kapcsolódáshoz és az adatlekérdezéshez

Ebben a rövid útmutatóban egy Azure Database for PostgreSQL használhat a Python használatával macOS, Ubuntu Linux vagy Windows rendszeren. A rövid útmutató bemutatja, hogyan csatlakozhat az adatbázishoz, és hogyan lehet SQL-utasításokkal adatokat lekérdezni, beszúrni, frissíteni és törölni. A cikk feltételezi, hogy már ismeri a Pythont, de a Azure Database for PostgreSQL használatának új módszere.

> [!TIP]
> Ha Django-alkalmazást szeretne felépíteni a PostgreSQL-sel, akkor adja ki az oktatóanyagot, [és telepítsen egy Django-webalkalmazást a PostgreSQL](../app-service/containers/tutorial-python-postgresql-app.md) oktatóanyaggal.


## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- A rövid útmutató befejezése [: hozzon létre egy Azure Database for PostgreSQL kiszolgálót a Azure Portal vagy a](quickstart-create-server-database-portal.md) rövid útmutatóban [: hozzon létre egy Azure Database for POSTGRESQL az Azure CLI használatával](quickstart-create-server-database-azure-cli.md).
  
- [Python](https://www.python.org/downloads/) 2.7.9 + vagy 3.4 +.
  
- Legújabb [pip](https://pip.pypa.io/en/stable/installing/) -csomag telepítője.

## <a name="install-the-python-libraries-for-postgresql"></a>Python-kódtárak telepítése a PostgreSQL-hez
A [psycopg2](https://pypi.python.org/pypi/psycopg2/) modul lehetővé teszi a PostgreSQL-adatbázishoz való csatlakozást és azok lekérdezését, és Linux, MacOS vagy Windows [Wheel](https://pythonwheels.com/) csomagként érhető el. Telepítse a modul bináris verzióját, beleértve az összes függőséget. A `psycopg2` telepítéssel és a követelményekkel kapcsolatos további információkért lásd: [telepítés](http://initd.org/psycopg/docs/install.html). 

A telepítéséhez `psycopg2` Nyisson meg egy terminált vagy egy parancssort, és futtassa a parancsot `pip install psycopg2` .

## <a name="get-database-connection-information"></a>Adatbázis-kapcsolatok adatainak beolvasása
Azure Database for PostgreSQL-adatbázishoz való csatlakozáshoz a teljes kiszolgálónév és a bejelentkezési hitelesítő adatok szükségesek. Ezt az információt a Azure Portalból kérheti le.

1. A [Azure Portal](https://portal.azure.com/)keresse meg és válassza ki a Azure Database for PostgreSQL-kiszolgáló nevét. 
1. A kiszolgáló **Áttekintés** lapján másolja a teljes **kiszolgálónevet** és a **rendszergazdai felhasználónevet**. A teljes **kiszolgálónév** mindig a form * \<my-server-name> . postgres.database.Azure.com*, a **rendszergazdai Felhasználónév** pedig mindig *\<my-admin-username>@\<my-server-name>* az űrlap. 
   
   Szüksége lesz a rendszergazdai jelszavára is. Ha elfelejti, ezt a lapról állíthatja vissza. 
   
   :::image type="content" source="./media/connect-python/1-connection-string.png" alt-text="Azure Database for PostgreSQL-kiszolgáló neve":::

## <a name="how-to-run-the-python-examples"></a>A Python-példák futtatása

A cikkben szereplő kódok mindegyike esetében:

1. Hozzon létre egy új fájlt egy szövegszerkesztőben. 
   
1. Adja hozzá a példában szereplő kódot a fájlhoz. A kódban cserélje le a következőket:
   - `<server-name>` és a `<admin-username>` Azure Portalból másolt értékekkel.
   - `<admin-password>` a kiszolgáló jelszavával.
   - `<database-name>` a Azure Database for PostgreSQL-adatbázis nevével. A kiszolgáló létrehozásakor a rendszer automatikusan létrehozta a *postgres* nevű alapértelmezett adatbázist. Átnevezheti az adatbázist, vagy létrehozhat egy új adatbázist az SQL-parancsok használatával. 
   
1. Mentse a fájlt a Project mappájába *.* file kiterjesztéssel, például *postgres-INSERT.py*. Windows esetén győződjön meg arról, hogy az UTF-8 kódolás van kiválasztva a fájl mentésekor. 
   
1. A fájl futtatásához váltson egy parancssori felületen a Project mappára, és írja be például a következőt `python` : `python postgres-insert.py` .

## <a name="create-a-table-and-insert-data"></a>Tábla létrehozása és az adatbeszúrás
A következő kódrészlet a [psycopg2. csatlakoztatási](http://initd.org/psycopg/docs/connection.html) függvénnyel csatlakozik a Azure Database for PostgreSQL-adatbázishoz, és betölti az adatait egy SQL **Insert** utasítással. Az [cursor.exeCute](http://initd.org/psycopg/docs/cursor.html#execute) függvény végrehajtja az SQL-lekérdezést az adatbázison. 

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

:::image type="content" source="media/connect-python/2-example-python-output.png" alt-text="Parancssori kimenet":::

## <a name="read-data"></a>Adatok olvasása
A következő kódrészlet a Azure Database for PostgreSQL-adatbázishoz kapcsolódik, és az SQL **Select** utasítással az adatok beolvasásához használja az [cursor.exeCute](http://initd.org/psycopg/docs/cursor.html#execute) kifejezést. Ez a függvény elfogad egy lekérdezést, és visszaad egy eredményhalmaz értékét a [cursor. fetchall ()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall)használatával. 

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
A következő kódrészlet a Azure Database for PostgreSQL-adatbázishoz kapcsolódik, és az SQL **Update** utasítással az adatok frissítésére szolgáló [cursor.exeCute](http://initd.org/psycopg/docs/cursor.html#execute) -t használja. 

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
A következő kódrészlet a Azure Database for PostgreSQL-adatbázishoz kapcsolódik, és az SQL **delete** utasítással [cursor.exearanyos](http://initd.org/psycopg/docs/cursor.html#execute) használatával törli a korábban beszúrt leltározási elemeket. 

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

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md)
