---
title: 'Gyors útmutató: kapcsolat a Python használatával – Azure Database for MySQL'
description: Ez a rövid útmutató számos Python-mintakódot biztosít, amelyekkel csatlakozhat a MySQL-hez készült Azure-adatbázishoz, illetve adatokat kérdezhet le róla.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: aad160f8a4ec96a1e0e627684c598062079b57e0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90896302"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql"></a>Rövid útmutató: a Python használatával csatlakozhat és lekérdezheti Azure Database for MySQL

Ebben a rövid útmutatóban a Python használatával csatlakozik egy Azure Database for MySQLhoz. Ezután SQL-utasításokkal adatokat lehet lekérdezni, beszúrni, frissíteni és törölni az adatbázisban a Mac, Ubuntu Linux és Windows platformokról. 

Ez a témakör azt feltételezi, hogy már ismeri a fejlesztést a Python használatával, de most ismerkedik a Azure Database for MySQLával.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy Azure Database for MySQL-kiszolgáló. [Hozzon létre egy Azure Database for MySQL kiszolgálót Azure Portal használatával](quickstart-create-mysql-server-database-using-azure-portal.md) , vagy [hozzon létre egy Azure Database for MySQL kiszolgálót az Azure CLI használatával](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!IMPORTANT] 
> Győződjön meg arról, hogy az IP-cím, amelyhez csatlakozik, a [Azure Portal](./howto-manage-firewall-using-portal.md) vagy az [Azure CLI](./howto-manage-firewall-using-cli.md) használatával adja hozzá a kiszolgáló tűzfalszabály-szabályait.

## <a name="install-python-and-the-mysql-connector"></a>A Python és a MySQL-összekötő telepítése

Telepítse a Pythont és a Pythonhoz készült MySQL-összekötőt a számítógépre a következő lépések végrehajtásával: 

> [!NOTE]
> Ez a rövid útmutató egy nyers SQL-lekérdezési módszert használ a MySQL-hez való kapcsolódáshoz. Ha webes keretrendszert használ, használja az ajánlott összekötőt a keretrendszerhez, például [mysqlclient](https://pypi.org/project/mysqlclient/) for Django.

1. Töltse le és telepítse a [Python 3,7-es vagy újabb](https://www.python.org/downloads/) verzióját az operációs rendszerének. Ügyeljen arra, hogy a Pythont hozzáadja a szolgáltatáshoz `PATH` , mert a MySQL-összekötő ehhez szükséges.
   
1. Nyisson meg egy parancssort vagy egy `bash` rendszerhéjat, és `python -V` a nagybetűs V kapcsolóval futtassa a Python-verziót.
   
1. A `pip` csomag telepítőjének részét képezi a Python legújabb verziói. Frissítsen `pip` a legújabb verzióra a futtatásával `pip install -U pip` . 
   
   Ha `pip` nincs telepítve, letöltheti és telepítheti azt a használatával `get-pip.py` . További információ: [telepítés](https://pip.pypa.io/en/stable/installing/). 
   
1. `pip`A használatával telepítheti a MySQL-összekötőt a Pythonhoz és annak függőségeihez:
   
   ```bash
   pip install mysql-connector-python
   ```
   
   A [MySQL.com](https://dev.mysql.com/downloads/connector/python/)-ből is telepítheti a MySQL-hez készült Python-összekötőt. További információ a Pythonhoz készült MySQL-összekötőről: [MySQL Connector/Python fejlesztői útmutató](https://dev.mysql.com/doc/connector-python/en/). 

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

Szerezze be a Azure Database for MySQLhoz való kapcsolódáshoz szükséges kapcsolati adatokat a Azure Portalból. Szüksége lesz a kiszolgáló nevére, az adatbázis nevére és a bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
   
1. A portálon keresse meg és válassza ki a létrehozott Azure Database for MySQL-kiszolgálót, például **mydemoserver**.
   
   :::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="A MySQL-hez készült Azure Database-kiszolgáló neve":::
   
1. A kiszolgáló **Áttekintés** lapján jegyezze fel a **kiszolgáló nevét** és a **kiszolgáló-rendszergazdai bejelentkezési nevet**. Ha elfelejti a jelszavát, akkor ezen a lapon is visszaállíthatja a jelszót.
   
   :::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="A MySQL-hez készült Azure Database-kiszolgáló neve":::

## <a name="run-the-python-examples"></a>A Python-példák futtatása

A cikkben szereplő kódok mindegyike esetében:

1. Hozzon létre egy új fájlt egy szövegszerkesztőben.
1. Adja hozzá a példában szereplő kódot a fájlhoz. A kódban cserélje le a `<mydemoserver>` , `<myadmin>` ,, `<mypassword>` és `<mydatabase>` helyőrzőket a MySQL-kiszolgáló és-adatbázis értékeire.
1. Mentse a fájlt a Project mappába egy *.* file kiterjesztésű bővítménnyel, például *C:\pythonmysql\createtable.py* vagy */Home/username/pythonmysql/createtable.py elérési úton*.
1. A kód futtatásához nyisson meg egy parancssort vagy egy `bash` rendszerhéjat, és módosítsa a könyvtárat a Project mappájába, például: `cd pythonmysql` . Írja be a `python` parancsot, majd a fájlnevet, `python createtable.py` majd nyomja le az ENTER billentyűt. 
   
   > [!NOTE]
   > Windows rendszeren, ha *python.exe* nem található, lehet, hogy hozzá kell adnia a Python elérési útját a PATH környezeti változóhoz, vagy meg kell adnia a *python.exe*teljes elérési útját, például: `C:\python27\python.exe createtable.py` .

## <a name="create-a-table-and-insert-data"></a>Tábla létrehozása és az adatbeszúrás

A következő kód használatával csatlakozhat a kiszolgálóhoz és az adatbázishoz, létrehozhat egy táblát, és betöltheti az adatok betöltését egy **Insert** SQL-utasítás használatával. 

A kód importálja a MySQL. Connector függvénytárat, és a [csatlakozás ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvénnyel csatlakozik Azure Database for MySQLhoz a konfigurációs gyűjtemény [argumentumai](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) segítségével. A kód egy kurzort használ a kapcsolatban, és a [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus hajtja végre az SQL-lekérdezést a MySQL-adatbázison. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
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

A kód importálja a MySQL. Connector függvénytárat, és a [csatlakozás ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvénnyel csatlakozik Azure Database for MySQLhoz a konfigurációs gyűjtemény [argumentumai](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) segítségével. A kód egy kurzort használ a kapcsolatban, és a [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus hajtja végre az SQL-lekérdezést a MySQL-adatbázison. 

A kód beolvassa az adatsorokat a [fetchall ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) metódussal, megtartja az eredményhalmaz egy gyűjtemény sorában, és egy `for` iterációt használ a sorok áthurkolása érdekében.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
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

A kód importálja a MySQL. Connector függvénytárat, és a [csatlakozás ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvénnyel csatlakozik Azure Database for MySQLhoz a konfigurációs gyűjtemény [argumentumai](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) segítségével. A kód egy kurzort használ a kapcsolatban, és a [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus hajtja végre az SQL-lekérdezést a MySQL-adatbázison. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
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

A kód importálja a MySQL. Connector függvénytárat, és a [csatlakozás ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvénnyel csatlakozik Azure Database for MySQLhoz a konfigurációs gyűjtemény [argumentumai](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) segítségével. A kód egy kurzort használ a kapcsolatban, és a [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus hajtja végre az SQL-lekérdezést a MySQL-adatbázison. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
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
