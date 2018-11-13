---
title: Csatlakozás az Azure Database for MySQL-hez a Pythonnal
description: Ez a rövid útmutató számos Python-mintakódot biztosít, amelyekkel csatlakozhat a MySQL-hez készült Azure-adatbázishoz, illetve adatokat kérdezhet le róla.
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: 54b25aa141da15224d5d8034ba54783d3633f5be
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50914736"
---
# <a name="azure-database-for-mysql-use-python-to-connect-and-query-data"></a>A MySQL-hez készült Azure-adatbázis: Csatlakozás és adatlekérdezés a Python használatával
Ez a rövid útmutató ismerteti, hogyan használható a [Python](https://python.org) a MySQL-hez készült Azure-adatbázishoz való csatlakozáshoz. Az SQL-utasítások használatával kérdez le, szúr be, frissít és töröl adatokat az adatbázisban a Mac OS, Ubuntu Linux és a Windows platformról. Ez a témakör azt feltételezi, hogy a Python használata terén rendelkezik fejlesztési tapasztalatokkal, de az Azure Database for MySQL használatában még járatlan.

## <a name="prerequisites"></a>Előfeltételek
Ebben a rövid útmutatóban a következő útmutatók valamelyikében létrehozott erőforrásokat használunk kiindulási pontként:
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-python-and-the-mysql-connector"></a>A Python és a MySQL-összekötő telepítése
Telepítse a [Pythont](https://www.python.org/downloads/) és a [Python MySQL-összekötőjét](https://dev.mysql.com/downloads/connector/python/) a saját gépére. Kövesse a platformjának lépéseket a megfelelő lenti szakaszban. 

> [!NOTE]
> Ez a rövid útmutató nyers SQL-lekérdezést használ a MySQL-hez való csatlakozásra a lekérdezések futtatásához. Ha webes keretrendszert használ, használja az adott keretrendszerhez ajánlott összekötőt. Például a Djangóhoz a [mysqlclient](https://pypi.org/project/mysqlclient/) használata ajánlott.
>

### <a name="windows"></a>Windows
1. Töltse le és telepítse a Python 2.7-es verziót a [python.org](https://www.python.org/downloads/windows/) webhelyről. 
2. A parancssor elindításával ellenőrizze a Python telepítését. Futtassa a `C:\python27\python.exe -V` parancsot a nagybetűs V kapcsolóval a verziószám megtekintéséhez.
3. Telepítse a MySQL a Python verziójának megfelelő Python-összekötőjét a [mysql.com](https://dev.mysql.com/downloads/connector/python/) webhelyről.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. A Linux (Ubuntu) rendszeren a Python általában az alapértelmezett telepítés részeként van telepítve.
2. A bash rendszerhéj elindításával ellenőrizze a Python telepítését. Futtassa a `python -V` parancsot a nagybetűs V kapcsolóval a verziószám megtekintéséhez.
3. A PIP telepítésének ellenőrzéséhez futtassa a `pip show pip -V` parancsot a verziószám megtekintéséhez. 
4. A PIP szerepelhet a Python egyes verzióiban. Ha a PIP nincs telepítve, a [PIP](https://pip.pypa.io/en/stable/installing/) csomagot a `sudo apt-get install python-pip` parancs futtatásával telepítheti.
5. A PIP legújabb verziójára való frissítéshez futtassa a `pip install -U pip` parancsot.
6. A PIP paranccsal telepítse a Python MySQL-összekötőjét és annak függőségeit:

   ```bash
   sudo pip install mysql-connector-python-rf
   ```
 
### <a name="macos"></a>MacOS
1. A Mac OS rendszeren a Python általában az alapértelmezett telepítés részeként van telepítve.
2. A bash rendszerhéj elindításával ellenőrizze a Python telepítését. Futtassa a `python -V` parancsot a nagybetűs V kapcsolóval a verziószám megtekintéséhez.
3. A PIP telepítésének ellenőrzéséhez futtassa a `pip show pip -V` parancsot a verziószám megtekintéséhez.
4. A PIP szerepelhet a Python egyes verzióiban. Ha a PIP nincs telepítve, telepítheti a [PIP](https://pip.pypa.io/en/stable/installing/) csomagot.
5. A PIP legújabb verziójára való frissítéshez futtassa a `pip install -U pip` parancsot.
6. A PIP paranccsal telepítse a Python MySQL-összekötőjét és annak függőségeit:

   ```bash
   pip install mysql-connector-python-rf
   ``` 

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le a MySQL-hez készült Azure Database-hez való csatlakozáshoz szükséges kapcsolatadatokat. Ehhez szükség lesz a teljes kiszolgálónévre és bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Az Azure Portal bal oldali menüjében kattintson a **Minden erőforrás** lehetőségre, és keressen rá a létrehozott kiszolgálóra (például **mydemoserver**).
3. Kattintson a kiszolgálónévre.
4. A kiszolgáló **Áttekintés** paneléről jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**. Ha elfelejti a jelszavát, ezen a panelen új jelszót is tud kérni.
 ![A MySQL-hez készült Azure Database-kiszolgáló neve](./media/connect-python/1_server-overview-name-login.png)

## <a name="run-python-code"></a>A Python-kód futtatása
- Mentse a kódot egy szövegfájlba, és mentse a fájlt egy projektmappába .py kiterjesztéssel, például a C:\pythonmysql\createtable.py vagy /home/username/pythonmysql/createtable.py elérési úton.
- A kód futtatásához nyissa meg a parancssort vagy a Bash rendszerhéjat. Módosítsa a könyvtárat a projektmappájára: `cd pythonmysql`. Ezután írja be a python-parancsot, majd a fájlnevet (`python createtable.py`) az alkalmazás futtatásához. Ha a Windows operációs rendszeren nem található a python.exe, előfordulhat, hogy meg kell adnia a futtatható fájl teljes elérési útját, vagy a Python-útvonalat a path környezeti változóhoz adhatja. `C:\python27\python.exe createtable.py`

## <a name="connect-create-table-and-insert-data"></a>Csatlakozás, táblák létrehozása és adatok beszúrása
Az alábbi kód használatával csatlakozhat a kiszolgálóhoz, létrehozhat egy táblát és betöltheti az adatokat egy **INSERT** SQL-utasítással. 

A kódban a mysql.connector-kódtár lesz importálva. A [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvénnyel csatlakozhat a MySQL-hez készült Azure-adatbázishoz a config gyűjtemény [kapcsolati argumentumaival](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html). A kód egy kurzort használ a kapcsolaton, és a [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus hajtja végre az SQL-lekérdezést a MySQL-adatbázisban. 

Cserélje le a `host`, `user`, `password` és `database` paramétert azokkal az értékekkel, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="read-data"></a>Adatok olvasása
A következő kóddal csatlakozhat, és beolvashatja az adatokat a **SELECT** SQL-utasítással. 

A kódban a mysql.connector-kódtár lesz importálva. A [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvénnyel csatlakozhat a MySQL-hez készült Azure-adatbázishoz a config gyűjtemény [kapcsolati argumentumaival](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html). A kód egy kurzort használ a kapcsolaton, és a [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus hajtja végre az SQL-utasítást a MySQL-adatbázisban. Az adatsorokat a rendszer a [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) metódussal olvassa be. Az eredményhalmaz egy gyűjteménysorba kerül, és a sorokon történő végighaladásra egy „for” iterátor szolgál.

Cserélje le a `host`, `user`, `password` és `database` paramétert azokkal az értékekkel, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="update-data"></a>Adatok frissítése
Az alábbi kód használatával csatlakozhat és végezheti el az adatok módosítását egy **UPDATE** SQL-utasítás segítségével. 

A kódban a mysql.connector-kódtár lesz importálva.  A [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvénnyel csatlakozhat a MySQL-hez készült Azure-adatbázishoz a config gyűjtemény [kapcsolati argumentumaival](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html). A kód egy kurzort használ a kapcsolaton, és a [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus hajtja végre az SQL-utasítást a MySQL-adatbázisban. 

Cserélje le a `host`, `user`, `password` és `database` paramétert azokkal az értékekkel, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="delete-data"></a>Adat törlése
A következő kód használatával csatlakozhat, és eltávolíthatja az adatokat a **DELETE** SQL-utasítással. 

A kódban a mysql.connector-kódtár lesz importálva.  A [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvénnyel csatlakozhat a MySQL-hez készült Azure-adatbázishoz a config gyűjtemény [kapcsolati argumentumaival](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html). A kód egy kurzort használ a kapcsolaton, és a [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus hajtja végre az SQL-lekérdezést a MySQL-adatbázisban. 

Cserélje le a `host`, `user`, `password` és `database` paramétert azokkal az értékekkel, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established.")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password.")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist.")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./concepts-migrate-import-export.md)
