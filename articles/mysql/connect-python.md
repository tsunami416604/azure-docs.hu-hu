---
title: "Csatlakozás a MySQL-hez készült Azure-adatbázishoz a Pythonnal | Microsoft Docs"
description: "Ez a rövid útmutató számos Python-mintakódot biztosít, amelyekkel csatlakozhat a MySQL-hez készült Azure-adatbázishoz, illetve adatokat kérdezhet le róla."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.devlang: python
ms.topic: hero-article
ms.date: 07/12/2017
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: c42ecff1830782ac6646c47c61b17379d122836a
ms.contentlocale: hu-hu
ms.lasthandoff: 07/12/2017

---
# <a name="azure-database-for-mysql-use-python-to-connect-and-query-data"></a>A MySQL-hez készült Azure-adatbázis: Csatlakozás és adatlekérdezés a Python használatával
Ez a rövid útmutató ismerteti, hogyan használható a [Python](https://python.org) a MySQL-hez készült Azure-adatbázishoz való csatlakozáshoz. Az SQL-utasítások használatával kérdez le, szúr be, frissít és töröl adatokat az adatbázisban a Mac OS, Ubuntu Linux és a Windows platformról. A jelen cikkben ismertetett lépések feltételezik, hogy Ön rendelkezik fejlesztési tapasztalatokkal a Python használatával kapcsolatosan, a MySQL-hez készült Azure-adatbázis használatában pedig még járatlan.

## <a name="prerequisites"></a>Előfeltételek
Ebben a rövid útmutatóban a következő útmutatók valamelyikében létrehozott erőforrásokat használunk kiindulási pontként:
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-python-and-the-mysql-connector"></a>A Python és a MySQL-összekötő telepítése
Telepítse a [Pythont](https://www.python.org/downloads/) és a [Python MySQL-összekötőjét](https://dev.mysql.com/downloads/connector/python/) a saját gépére. Kövesse az egyes platformoknak megfelelő lépéseket:

### <a name="windows"></a>Windows
1. Töltse le és telepítse a Python 2.7-es verziót a [python.org](https://www.python.org/downloads/windows/) webhelyről. 
2. A parancssor elindításával ellenőrizze a Python telepítését. Futtassa a `C:\python27\python.exe -V` parancsot a nagybetűs V kapcsolóval a verziószám megtekintéséhez.
3. Telepítse a MySQL a Python verziójának megfelelő Python-összekötőjét a [mysql.com](https://dev.mysql.com/downloads/connector/python/) webhelyről.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. A Linux (Ubuntu) rendszeren a Python általában az alapértelmezett telepítés részeként van telepítve.
2. A bash rendszerhéj elindításával ellenőrizze a Python telepítését. Futtassa a `python -V` parancsot a nagybetűs V kapcsolóval a verziószám megtekintéséhez.
3. A PIP telepítésének ellenőrzéséhez futtassa a `pip show pip -V` parancsot a verziószám megtekintéséhez. 
4. A PIP szerepelhet a Python egyes verzióiban. Ha a PIP nincs telepítve, a [PIP] (https://pip.pypa.io/en/stable/installing/) csomagot a `sudo apt-get install python-pip` parancs futtatásával telepítheti.
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

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, és keressen rá a létrehozott kiszolgálóra (például: **myserver4demo**).
3. Kattintson a **myserver4demo** kiszolgálónévre.
4. Válassza a kiszolgáló **tulajdonságlapját**. Jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**.
 ![MySQL-hez készült Azure-adatbázis – Kiszolgáló-rendszergazdai bejelentkezés](./media/connect-python/1_server-properties-name-login.png)
5. Amennyiben elfelejtette a kiszolgálója bejelentkezési adatait, lépjen az **Áttekintés** oldalra, ahol kikeresheti a kiszolgáló-rendszergazda bejelentkezési nevét, valamint szükség esetén új jelszót kérhet.
   

## <a name="run-python-code"></a>A Python-kód futtatása
- Mentse a kódot egy szövegfájlba, és mentse a fájlt egy projektmappába .py kiterjesztéssel, például a C:\pythonmysql\createtable.py vagy /home/username/pythonmysql/createtable.py elérési úton.
- A kód futtatásához nyissa meg a parancssort vagy a bash rendszerhéjat. Módosítsa a könyvtárat a projektmappájára: `cd pythonmysql`. Ezután írja be a python-parancsot, majd a fájlnevet (`python createtable.py`) az alkalmazás futtatásához. Ha a Windows operációs rendszeren nem található a python.exe, megadhatja a futtatható fájl teljes elérési útját, vagy a Python-útvonalat a path környezeti változóhoz adhatja. `C:\python27\python.exe createtable.py`

## <a name="connect-create-table-and-insert-data"></a>Csatlakozás, táblák létrehozása és adatok beszúrása
Az alábbi kód használatával csatlakozhat a kiszolgálóhoz, hozhat létre táblát, és töltheti be az adatokat egy **INSERT** SQL-utasítással. 

A kódban a mysql.connector-kódtár lesz importálva. A [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvénnyel csatlakozhat a MySQL-hez készült Azure-adatbázishoz a config gyűjtemény [kapcsolati argumentumaival](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html). A kód egy kurzort használ a kapcsolaton, és a [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus hajtja végre az SQL-lekérdezést a MySQL-adatbázisban. 

Cserélje le a `host`, `user`, `password` és `database` paramétert azokkal az értékekkel, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'myserver4demo.mysql.database.azure.com',
  'user':'myadmin@myserver4demo',
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

## <a name="read-data"></a>Adatok beolvasása
A következő kóddal csatlakozhat, és beolvashatja az adatokat a **SELECT** SQL-utasítással. 

A kódban a mysql.connector-kódtár lesz importálva. A [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvénnyel csatlakozhat a MySQL-hez készült Azure-adatbázishoz a config gyűjtemény [kapcsolati argumentumaival](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html). A kód egy kurzort használ a kapcsolaton, és a [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus hajtja végre az SQL-utasítást a MySQL-adatbázisban. Az adatsorokat a rendszer a [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) metódussal olvassa be. Az eredményhalmaz egy gyűjteménysorba kerül, és a sorokon történő végighaladásra egy „for” iterátor szolgál.

Cserélje le a `host`, `user`, `password` és `database` paramétert azokkal az értékekkel, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'myserver4demo.mysql.database.azure.com',
  'user':'myadmin@myserver4demo',
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
A következő kód használatával csatlakozhat, és frissítheti az adatokat az **UPDATE** SQL-utasítással. 

A kódban a mysql.connector-kódtár lesz importálva.  A [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvénnyel csatlakozhat a MySQL-hez készült Azure-adatbázishoz a config gyűjtemény [kapcsolati argumentumaival](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html). A kód egy kurzort használ a kapcsolaton, és a [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus hajtja végre az SQL-utasítást a MySQL-adatbázisban. 

Cserélje le a `host`, `user`, `password` és `database` paramétert azokkal az értékekkel, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'myserver4demo.mysql.database.azure.com',
  'user':'myadmin@myserver4demo',
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
  'host':'myserver4demo.mysql.database.azure.com',
  'user':'myadmin@myserver4demo',
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

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./concepts-migrate-import-export.md)

