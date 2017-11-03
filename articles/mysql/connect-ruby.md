---
title: "Csatlakozás a MySQL-hez készült Azure-adatbázishoz a Rubyval | Microsoft Docs"
description: "Ez a rövid útmutató számos Ruby-mintakódot biztosít, amelyekkel csatlakozhat a MySQL-hez készült Azure-adatbázishoz, illetve adatokat kérdezhet le róla."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 09/22/2017
ms.openlocfilehash: 10f774262015cb19e158a687138b4618ce50063b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-mysql-use-ruby-to-connect-and-query-data"></a>A MySQL-hez készült Azure-adatbázis: Csatlakozás és adatlekérdezés a Ruby használatával
Ez a rövid útmutató ismerteti, hogyan használható egy [Ruby](https://www.ruby-lang.org)-alkalmazás és a [mysql2](https://rubygems.org/gems/mysql2) gem a MySQL-hez készült Azure-adatbázishoz való csatlakozáshoz Windows, Ubuntu Linux és Mac platformról. Azt is bemutatja, hogyan lehet SQL-utasítások használatával adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban. Ez a témakör azt feltételezi, hogy jártas használatával Ruby és, hogy még nem ismeri a MySQL az Azure-adatbázis használata.

## <a name="prerequisites"></a>Előfeltételek
Ebben a rövid útmutatóban a következő útmutatók valamelyikében létrehozott erőforrásokat használunk kiindulási pontként:
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-ruby"></a>A Ruby telepítése
Ruby, Gem és MySQL2 szalagtár telepítse a saját számítógépén. 

### <a name="windows"></a>Windows
1. Töltse le és telepítse a [Ruby](http://rubyinstaller.org/downloads/) 2.3-as verzióját.
2. Nyisson meg egy új parancssort (cmd) a Start menüből.
3. Lépjen a Ruby-könyvtárba a 2.3-as verzióhoz. `cd c:\Ruby23-x64\bin`
4. A Ruby telepítésének ellenőrzéséhez futtassa a `ruby -v` parancsot a telepített verzió megtekintéséhez.
5. A Gem telepítésének ellenőrzéséhez futtassa a `gem -v` parancsot a telepített verzió megtekintéséhez.
6. A Gem használatával állítsa össze a Mysql2 modult a Rubyhoz a `gem install mysql2` parancs futtatásával.

### <a name="macos"></a>MacOS
1. A következő parancs futtatásával telepítse a Rubyt a Homebrew használatával: `brew install ruby`. A további telepítési lehetőségekről a Ruby [telepítési dokumentációjából](https://www.ruby-lang.org/en/documentation/installation/#homebrew) tájékozódhat.
2. A Ruby telepítésének ellenőrzéséhez futtassa a `ruby -v` parancsot a telepített verzió megtekintéséhez.
3. A Gem telepítésének ellenőrzéséhez futtassa a `gem -v` parancsot a telepített verzió megtekintéséhez.
4. A Gem használatával állítsa össze a Mysql2 modult a Rubyhoz a `gem install mysql2` parancs futtatásával.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. A következő parancs futtatásával telepítse a Rubyt: `sudo apt-get install ruby-full`. A további telepítési lehetőségekről a Ruby [telepítési dokumentációjából](https://www.ruby-lang.org/en/documentation/installation/) tájékozódhat.
2. A Ruby telepítésének ellenőrzéséhez futtassa a `ruby -v` parancsot a telepített verzió megtekintéséhez.
3. Telepítse a legújabb Gem-frissítéseket a következő parancs futtatásával: `sudo gem update --system`.
4. A Gem telepítésének ellenőrzéséhez futtassa a `gem -v` parancsot a telepített verzió megtekintéséhez.
5. A következő parancs futtatásával telepítse a gcc, a make és az egyéb összeállítási eszközöket: `sudo apt-get install build-essential`.
6. Telepítse a MySQL-ügyfél fejlesztői könyvtárait a `sudo apt-get install libmysqlclient-dev` parancs futtatásával.
7. A Gem használatával állítsa össze a mysql2 modult a Rubyhoz a `sudo gem install mysql2` parancs futtatásával.

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le a MySQL-hez készült Azure Database-hez való csatlakozáshoz szükséges kapcsolatadatokat. Ehhez szükség lesz a teljes kiszolgálónévre és bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Azure-portálon a bal oldali menüben kattintson a **összes erőforrás**, majd keresse meg a kiszolgáló rendelkezik gyűrött (például **myserver4demo**).
3. Kattintson a **myserver4demo** kiszolgálónévre.
4. Válassza ki a kiszolgálót **tulajdonságok** lapon, majd jegyezze fel a **kiszolgálónév** és **kiszolgálói rendszergazda bejelentkezési név**.
 ![MySQL-hez készült Azure-adatbázis – Kiszolgáló-rendszergazdai bejelentkezés](./media/connect-ruby/1_server-properties-name-login.png)
5. Ha elfelejti a kiszolgálói bejelentkezési adatok, navigáljon a **áttekintése** lapon megtekintéséhez a rendszergazdai bejelentkezési nevet, és ha szükséges a jelszó alaphelyzetbe állítása.

## <a name="run-ruby-code"></a>Ruby-kód futtatása 
1. Az alábbi szakaszok a Ruby kód beillesztése szövegfájlok, és mentse a fájlok a fájl kiterjesztése .rb projekt mappába (például `C:\rubymysql\createtable.rb` vagy `/home/username/rubymysql/createtable.rb`).
2. A kódra, a parancssor megnyitása, vagy a Bash rendszerhéjat. Lépjen a projektmappára: `cd rubymysql`
3. Írja be a Ruby parancsot, majd a fájl neve, mint például `ruby createtable.rb` az alkalmazás futtatásához.
4. A Windows operációs rendszer Ruby alkalmazás esetén nem a a path környezeti változóval szeretne alkalmazás futtatására tett a csomópont, mint például a teljes útvonalat használja`"c:\Ruby23-x64\bin\ruby.exe" createtable.rb`

## <a name="connect-and-create-a-table"></a>Csatlakozás és tábla létrehozása
Az alábbi kód segítségével csatlakozzon, és hozzon létre egy táblát **CREATE TABLE** SQL-utasítást, és **INSERT INTO** SQL-utasítások sorok felvételének a táblába.

A kód a [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) class .new() metódust használja a MySQL-hez készült Azure-adatbázishoz való csatlakozáshoz. Ezután többször meghívja a [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) metódust a DROP, a CREATE TABLE és az INSERT INTO parancs futtatásához. Végül pedig a [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) metódus meghívásával bontja a kapcsolatot, mielőtt kilép.

Cserélje le a `host`, `database`, `username`, és `password` karakterláncokat a saját értékekre. 
```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('myserver4demo.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@myserver4demo')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Drop previous table of same name if one exists
    client.query('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    client.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    client.query("INSERT INTO inventory VALUES(1, 'banana', 150)")
    client.query("INSERT INTO inventory VALUES(2, 'orange', 154)")
    client.query("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="read-data"></a>Adatok olvasása
Az alábbi kód segítségével csatlakozzon, és az adatok olvasása a egy **válasszon** SQL-utasításban. 

A kód egy [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) class.new() metódus MySQL Azure adatbázishoz való kapcsolódáshoz. Ezután meghívja a [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) metódust a SELECT parancsok futtatásához. Végül pedig a [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) metódus meghívásával bontja a kapcsolatot, mielőtt kilép.

Cserélje le a `host`, `database`, `username`, és `password` karakterláncokat a saját értékekre. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('myserver4demo.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@myserver4demo')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Read data
    resultSet = client.query('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end
    puts 'Read ' + resultSet.count.to_s + ' row(s).'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="update-data"></a>Adatok frissítése
Az alábbi kód segítségével csatlakozzon, és az adatok frissítése a használatával egy **frissítése** SQL-utasításban.

A kód a [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) class .new() metódust használja a MySQL-hez készült Azure-adatbázishoz való csatlakozáshoz. Ezután meghívja a [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) metódust az UPDATE parancsok futtatásához. Végül pedig a [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) metódus meghívásával bontja a kapcsolatot, mielőtt kilép.

Cserélje le a `host`, `database`, `username`, és `password` karakterláncokat a saját értékekre. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('myserver4demo.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@myserver4demo')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Update data
   client.query('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
   puts 'Updated 1 row of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```


## <a name="delete-data"></a>Adat törlése
Az alábbi kód segítségével csatlakozzon, és az adatok olvasása a egy **törlése** SQL-utasításban. 

A kód a [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) class .new() metódust használja a MySQL-hez készült Azure-adatbázishoz való csatlakozáshoz. Ezután meghívja a [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) metódust a DELETE parancsok futtatásához. Végül pedig a [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) metódus meghívásával bontja a kapcsolatot, mielőtt kilép.

Cserélje le a `host`, `database`, `username`, és `password` karakterláncokat a saját értékekre. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('myserver4demo.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@myserver4demo')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Delete data
    resultSet = client.query('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./concepts-migrate-import-export.md)
