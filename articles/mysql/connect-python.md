---
title: Csatlakozás python használatával - Azure Database for MySQL
description: Ez a rövid útmutató számos Python-mintakódot biztosít, amelyekkel csatlakozhat a MySQL-hez készült Azure-adatbázishoz, illetve adatokat kérdezhet le róla.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
ms.devlang: python
ms.topic: quickstart
ms.date: 01/09/2020
ms.openlocfilehash: c9ea155f3cc71dd961a3780e3b188a6d062606bc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067901"
---
# <a name="quickstart-use-python-to-connect-and-query-data-with-azure-database-for-mysql"></a>Rövid útmutató: A Python használatával adatokat csatlakoztathat és kérdezhessen le a MySQL Azure Database szolgáltatással

Ebben a rövid útmutatóban a Python használatával csatlakozhat egy Azure Database for MySQL-hez. Ezután SQL-utasításokkal lekérdezheti, beszúrhatja, frissítheti és törölheti az adatbázisban lévő adatokat Mac, Ubuntu Linux és Windows platformokról. 

Ez a témakör feltételezi, hogy ismeri a Python használatával történő fejlesztést, de még csak most dolgozik az Azure Database for MySQL használatával.

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy Azure-adatbázis a MySQL-kiszolgálóhoz. [Hozzon létre egy Azure Database for MySQL-kiszolgálót az Azure Portal használatával,](quickstart-create-mysql-server-database-using-azure-portal.md) vagy [hozzon létre egy Azure-adatbázist a MySQL-kiszolgálóhoz az Azure CLI használatával.](quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-python-and-the-mysql-connector"></a>A Python és a MySQL-összekötő telepítése

Telepítse a Pythont és a Python MySQL-összekötőt a számítógépre az alábbi lépésekkel: 

> [!NOTE]
> Ez a rövid útmutató egy nyers SQL-lekérdezési megközelítést használ a MySQL-hez való csatlakozáshoz. Ha webes keretrendszert használ, használja a keretrendszer ajánlott összekötőjét, például [a Mysqlclient](https://pypi.org/project/mysqlclient/) for Django.

1. Töltse le és telepítse a [Python 3.7-es vagy újabb operációs](https://www.python.org/downloads/) rendszeréhez. Győződjön meg arról, hogy python-t ad hozzá a , `PATH`mert a MySQL-összekötő ezt igényli.
   
1. Nyisson meg `bash` egy parancssort vagy rendszerhéjat, és ellenőrizze a Python-verziót a nagyV kapcsolóval való futtatással. `python -V`
   
1. A `pip` csomagtelepítő a Python legújabb verzióiban található. Frissítsen `pip` a legújabb `pip install -U pip`verzióra a futtatásával. 
   
   Ha `pip` nincs telepítve, letöltheti és telepítheti a alkalmazással. `get-pip.py` További információt a [Telepítés című témakörben talál.](https://pip.pypa.io/en/stable/installing/) 
   
1. A `pip` Python MySQL-összekötőjének és függőségeinek telepítéséhez használható:
   
   ```bash
   pip install mysql-connector-python
   ```
   
   A MySQL Python-összekötőjét is telepítheti [a mysql.com.](https://dev.mysql.com/downloads/connector/python/) A MySQL Connector for Python alkalmazásról további információt a [MySQL Connector/Python Developer Guide című témakörben talál.](https://dev.mysql.com/doc/connector-python/en/) 

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

Az Azure Portalon szerezheti be a MySQL Azure Database-hez való csatlakozáshoz szükséges kapcsolati információkat. Szüksége van a kiszolgáló nevére, az adatbázis nevére és a bejelentkezési hitelesítő adataira.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
   
1. A portál keresősávjában keresse meg és jelölje ki a létrehozott Azure Database for MySQL-kiszolgálót, például a **mydemoserver**t.
   
   ![Azure Database for MySQL-kiszolgáló neve](./media/connect-python/1_server-overview-name-login.png)
   
1. A kiszolgáló **áttekintése** lapon jegyezze fel a **kiszolgáló nevét** és a **kiszolgáló rendszergazdai bejelentkezési nevét**. Ha elfelejti a jelszavát, ezen az oldalon is visszaállíthatja a jelszót.
   
   ![Azure Database for MySQL-kiszolgáló neve](./media/connect-python/azure-database-for-mysql-server-overview-name-login.png)

## <a name="run-the-python-examples"></a>A Python-példák futtatása

A cikkben szereplő minden egyes kódpéldához:

1. Új fájl létrehozása szövegszerkesztőben.
1. Adja hozzá a példakódot a fájlhoz. A kódban cserélje `<mydemoserver>` `<myadmin>`le `<mypassword>`a `<mydatabase>` , , és helyőrzőket a MySQL kiszolgáló és adatbázis értékeire.
1. Mentse a fájlt egy *.py* kiterjesztésű projektmappába, például *C:\pythonmysql\createtable.py* vagy */home/username/pythonmysql/createtable.py*.
1. A kód futtatásához nyisson `bash` meg egy parancssort vagy egy `cd pythonmysql`parancsértelmezőt, és módosítsa a könyvtárat a projekt mappájába, például . Írja `python` be a parancsot, majd `python createtable.py`a fájl nevet, például , és nyomja le az Enter billentyűt. 
   
   > [!NOTE]
   > Windows rendszerben, ha a *python.exe* nem található, előfordulhat, hogy hozzá kell adnia a Python elérési utat `C:\python27\python.exe createtable.py`a PATH környezeti változóhoz, vagy meg kell adnia a *python.exe*teljes elérési útját.

## <a name="create-a-table-and-insert-data"></a>Táblázat létrehozása és adatok beszúrása

A következő kód dal csatlakozhat a kiszolgálóhoz és az adatbázishoz, táblát hozhat létre, és **INSERT** SQL utasítással tölthet be adatokat. 

A kód importálja a mysql.connector könyvtárat, és a [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvény t használja a Connect() függvényt a MySQL Azure Database szolgáltatáshoz való csatlakozáshoz a konfigurációs gyűjtemény [argumentumai](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) használatával. A kód egy kurzort használ a kapcsolaton, és a [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus végrehajtja az SQL-lekérdezést a MySQL-adatbázison. 

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

A kód importálja a mysql.connector könyvtárat, és a [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvény t használja a Connect() függvényt a MySQL Azure Database szolgáltatáshoz való csatlakozáshoz a konfigurációs gyűjtemény [argumentumai](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) használatával. A kód egy kurzort használ a kapcsolaton, és a [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus végrehajtja az SQL-lekérdezést a MySQL-adatbázison. 

A kód beolvassa az adatsorokat a [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) metódus használatával, az `for` eredményt egy gyűjteménysorban tartja, és egy iterátort használ a sorok on-át.

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

A kód importálja a mysql.connector könyvtárat, és a [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvény t használja a Connect() függvényt a MySQL Azure Database szolgáltatáshoz való csatlakozáshoz a konfigurációs gyűjtemény [argumentumai](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) használatával. A kód egy kurzort használ a kapcsolaton, és a [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus végrehajtja az SQL-lekérdezést a MySQL-adatbázison. 

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

A kód importálja a mysql.connector könyvtárat, és a [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) függvény t használja a Connect() függvényt a MySQL Azure Database szolgáltatáshoz való csatlakozáshoz a konfigurációs gyűjtemény [argumentumai](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) használatával. A kód egy kurzort használ a kapcsolaton, és a [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) metódus végrehajtja az SQL-lekérdezést a MySQL-adatbázison. 

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
