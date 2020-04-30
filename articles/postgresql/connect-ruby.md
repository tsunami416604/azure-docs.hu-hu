---
title: Kapcsolódjon a Ruby-Azure Database for PostgreSQL – egyetlen kiszolgálóval
description: Ez a rövid útmutató egy Ruby-kódrészletet tartalmaz, amellyel összekapcsolhatók és beolvashatók az Azure Database for PostgreSQL-kiszolgálóról származó adatok.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: 5dd4cfe9bee0db6f14f736c79fe91770641008fb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "74766905"
---
# <a name="azure-database-for-postgresql---single-server-use-ruby-to-connect-and-query-data"></a>Azure Database for PostgreSQL – egyetlen kiszolgáló: a Ruby használata a kapcsolódáshoz és az adatlekérdezéshez
Ebben a gyors útmutatóban azt szemléltetjük, hogy miként lehet egy [Ruby](https://www.ruby-lang.org)-alkalmazás használatával csatlakozni a PostgreSQL-hez készült Azure Database-hez. Bemutatjuk, hogy az SQL-utasítások használatával hogyan kérdezhetők le, illeszthetők be, frissíthetők és törölhetők az adatok az adatbázisban. A jelen cikkben ismertetett lépések feltételezik, hogy Ön rendelkezik fejlesztési tapasztalatokkal a Ruby használatával kapcsolatban, az Azure Database for PostgreSQL használatában pedig még járatlan.

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató az alábbi útmutatók valamelyikében létrehozott erőforrásokat használja kiindulópontként:
- [DB létrehozása – portál](quickstart-create-server-database-portal.md)
- [DB létrehozása – Azure CLI](quickstart-create-server-database-azure-cli.md)

A következőket is telepítenie kell:
- [Ruby](https://www.ruby-lang.org/en/downloads/)
- Ruby PG, a Rubyhoz tartozó PostgreSQL-modul

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le a PostgreSQL-hez készült Azure-adatbázishoz való csatlakozáshoz szükséges kapcsolatadatokat. Szüksége lesz a teljes kiszolgálónévre és a bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Az Azure Portal bal oldali menüjében kattintson a **Minden erőforrás** lehetőségre, és keressen rá a létrehozott kiszolgálóra (például **mydemoserver**).
3. Kattintson a kiszolgálónévre.
4. A kiszolgáló **Áttekintés** paneléről jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**. Ha elfelejti a jelszavát, ezen a panelen új jelszót is tud kérni.
 ![Azure Database for PostgreSQL-kiszolgáló neve](./media/connect-ruby/1-connection-string.png)

> [!NOTE]
> Az `@` Azure postgres-beli felhasználónévben szereplő szimbólum az összes kapcsolatban lévő `%40` karakterláncnak megfelelően lett kódolva. 

## <a name="connect-and-create-a-table"></a>Csatlakozás és tábla létrehozása
A következő kód segítségével csatlakozzon, és hozzon létre egy táblát a **CREATE TABLE** SQL-utasítással, majd az **INSERT INTO** SQL-utasítással adjon hozzá sorokat a táblához.

A kód egy [PG::Connection](https://www.rubydoc.info/gems/pg/PG/Connection) objektumot használ a [new()](https://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) konstruktorral a PostgreSQL-hez készült Azure Database-hez való kapcsolódáshoz. Ezután meghívja az [exec()](https://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) metódust a DROP, CREATE TABLE és INSERT INTO parancsok futtatásához. A kód a [PG::Error](https://www.rubydoc.info/gems/pg/PG/Error) osztály használatával ellenőrzi a hibákat. Végül pedig a [close()](https://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) metódus meghívásával bontja a kapcsolatot, mielőtt kilép.

Cserélje le a `host`, `database`, `user`, és `password` sztringeket a saját értékekre. 


```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database'

    # Drop previous table of same name if one exists
    connection.exec('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    connection.exec('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    connection.exec("INSERT INTO inventory VALUES(1, 'banana', 150)")
    connection.exec("INSERT INTO inventory VALUES(2, 'orange', 154)")
    connection.exec("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="read-data"></a>Adatok olvasása
Az alábbi kód használatával csatlakozhat és végezheti el az adatok olvasását **SELECT** SQL-utasítás segítségével. 

A kód egy [PG::Connection](https://www.rubydoc.info/gems/pg/PG/Connection) objektumot használ a [new()](https://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) konstruktorral a PostgreSQL-hez készült Azure Database-hez való kapcsolódáshoz. Ezután meghívja az [exec()](https://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) metódust a SELECT parancs futtatásához, az eredményeket az eredményhalmazban megőrizve. Az eredményhalmaz gyűjtése többször is végrehajtódik a `resultSet.each do` ciklus használatával, megőrizve az aktuális sor értékeit a `row` változóban. A kód a [PG::Error](https://www.rubydoc.info/gems/pg/PG/Error) osztály használatával ellenőrzi a hibákat. Végül pedig a [close()](https://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) metódus meghívásával bontja a kapcsolatot, mielőtt kilép.

Cserélje le a `host`, `database`, `user`, és `password` sztringeket a saját értékekre. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :database => dbname, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    resultSet = connection.exec('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="update-data"></a>Adatok frissítése
A következő kód használatával csatlakozhat, és frissítheti az adatokat az **UPDATE** SQL-utasítással.

A kód egy [PG::Connection](https://www.rubydoc.info/gems/pg/PG/Connection) objektumot használ a [new()](https://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) konstruktorral a PostgreSQL-hez készült Azure Database-hez való kapcsolódáshoz. Ezután meghívja az [exec()](https://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) metódust az UPDATE parancs futtatásához. A kód a [PG::Error](https://www.rubydoc.info/gems/pg/PG/Error) osztály használatával ellenőrzi a hibákat. Végül pedig a [close()](https://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) metódus meghívásával bontja a kapcsolatot, mielőtt kilép.

Cserélje le a `host`, `database`, `user`, és `password` sztringeket a saját értékekre. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
    puts 'Updated 1 row of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```


## <a name="delete-data"></a>Adat törlése
A következő kód használatával csatlakozhat, és beolvashatja az adatokat a **DELETE** SQL-utasítással. 

A kód egy [PG::Connection](https://www.rubydoc.info/gems/pg/PG/Connection) objektumot használ a [new()](https://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) konstruktorral a PostgreSQL-hez készült Azure Database-hez való kapcsolódáshoz. Ezután meghívja az [exec()](https://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) metódust az UPDATE parancs futtatásához. A kód a [PG::Error](https://www.rubydoc.info/gems/pg/PG/Error) osztály használatával ellenőrzi a hibákat. Végül pedig a [close()](https://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) metódus meghívásával bontja a kapcsolatot, mielőtt kilép.

Cserélje le a `host`, `database`, `user`, és `password` sztringeket a saját értékekre. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md)
