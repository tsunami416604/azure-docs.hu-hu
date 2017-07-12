---
title: "Csatlakozás a PostgreSQL-hez készült Azure Database-hez a Ruby használatával | Microsoft Docs"
description: "Az alábbi gyors útmutatóban egy olyan Ruby-kódminta található, amely a PostgreSQL-hez készült Azure Database csatlakoztatására és adatlekérdezésre használható."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: ruby
ms.topic: hero-article
ms.date: 06/30/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: a04188aed40a46e8cb64dfeb1230fb9b3c6ab679
ms.contentlocale: hu-hu
ms.lasthandoff: 07/04/2017

---

<a id="azure-database-for-postgresql-use-ruby-to-connect-and-query-data" class="xliff"></a>

# A PostgreSQL-hez készült Azure Database: csatlakozás és adatlekérdezés a Ruby használatával
Ebben a gyors útmutatóban azt szemléltetjük, hogy miként lehet egy [Ruby](https://www.ruby-lang.org)-alkalmazás használatával csatlakozni a PostgreSQL-hez készült Azure Database-hez. Bemutatjuk, hogy az SQL-utasítások használatával hogyan kérdezhetők le, illeszthetők be, frissíthetők és törölhetők az adatok az adatbázisban. A jelen cikk azt feltételezi, hogy Ön már rendelkezik Ruby-fejlesztési tapasztalatokkal, de a PostgreSQL-hez készült Azure Database használatában pedig még járatlan.

<a id="prerequisites" class="xliff"></a>

## Előfeltételek
Ebben a rövid útmutatóban a következő útmutatók valamelyikében létrehozott erőforrásokat használunk kiindulási pontként:
- [DB létrehozása – portál](quickstart-create-server-database-portal.md)
- [DB létrehozása – Azure CLI](quickstart-create-server-database-azure-cli.md)

<a id="install-ruby" class="xliff"></a>

## A Ruby telepítése
Telepítse a Rubyt saját számítógépén. 

<a id="windows" class="xliff"></a>

### Windows
- Töltse le, és telepítse a [Ruby](http://rubyinstaller.org/downloads/) legújabb verzióját.
- Az MSI telepítő befejező képernyőjén jelölje be a „Run 'ridk install' to install MSYS2 and development toolchain” (A ridk install parancs futtatása az MSYS2 és a fejlesztési eszközlánc telepítéséhez) jelölőnégyzetet. Kattintson a **Befejezés** gombra a következő telepítő elindításához.
- Ekkor elindul l aRubyInstaller2 for Windows telepítője. Az MSYS2-adattárfrissítés telepítéséhez nyomja le a 2 billentyűt. Miután befejeződik a telepítés, és visszatér a telepítő parancssorába, zárja be a parancssor ablakát.
- Nyisson meg egy új parancssort (cmd) a Start menüből.
- Ellenőrizze a Ruby-telepítés sikerességét a telepített verzió megtekintésével: `ruby -v`.
- Ellenőrizze a Gem-telepítés sikerességét a telepített verzió megtekintésével: `gem -v`.
- A következő parancs futtatásával készítse el a PostgreSQL-modult a Ruby-hoz a Gem használatával: `gem install pg`.

<a id="macos" class="xliff"></a>

### MacOS
- A következő parancs futtatásával telepítse a Rubyt a Homebrew használatával: `brew install ruby`. A további telepítési lehetőségekről a Ruby [telepítési dokumentációjából](https://www.ruby-lang.org/en/documentation/installation/#homebrew) tájékozódhat
- Ellenőrizze a Ruby-telepítés sikerességét a telepített verzió megtekintésével: `ruby -v`.
- Ellenőrizze a Gem-telepítés sikerességét a telepített verzió megtekintésével: `gem -v`.
- A következő parancs futtatásával készítse el a PostgreSQL-modult a Ruby-hoz a Gem használatával: `gem install pg`.

<a id="linux-ubuntu" class="xliff"></a>

### Linux (Ubuntu)
- A következő parancs futtatásával telepítse a Rubyt: `sudo apt-get install ruby-full`. A további telepítési lehetőségekről a Ruby [telepítési dokumentációjából](https://www.ruby-lang.org/en/documentation/installation/) tájékozódhat.
- Ellenőrizze a Ruby-telepítés sikerességét a telepített verzió megtekintésével: `ruby -v`.
- Telepítse a legújabb Gem-frissítéseket a következő parancs futtatásával: `sudo gem update --system`.
- Ellenőrizze a Gem-telepítés sikerességét a telepített verzió megtekintésével: `gem -v`.
- A következő parancs futtatásával telepítse a gcc, a make és az egyéb összeállítási eszközöket: `sudo apt-get install build-essential`.
- Telepítése a PostgreSQL-kódtárakat a `sudo apt-get install libpq-dev` parancs futtatásával.
- Hozza létre a Ruby pg modult a Gem használatával a `sudo gem install pg` parancs futtatásával.

<a id="run-ruby-code" class="xliff"></a>

## Ruby-kód futtatása 
- Mentse a kódot egy szövegfájlba, és mentse a fájlt egy projektmappába az .rb fájlkiterjesztéssel, például: `C:\rubypostgres\read.rb` vagy `/home/username/rubypostgres/read.rb`
- A kód futtatásához nyissa meg a parancssort vagy a bash rendszerhéjat. Lépjen a projektmappába a `cd rubypostgres` paranccsal, majd írja be a `ruby read.rb` parancsot az alkalmazás futtatásához.

<a id="get-connection-information" class="xliff"></a>

## Kapcsolatadatok lekérése
Kérje le a PostgreSQL-hez készült Azure Database csatlakoztatásához szükséges kapcsolatadatokat. Ehhez szükség lesz a teljes kiszolgálónévre és bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, és keressen rá a létrehozott kiszolgálóra, például **mypgserver-20170401**.
3. Kattintson a **mypgserver-20170401** kiszolgálónévre.
4. Válassza ki a kiszolgáló **Áttekintés** oldalát. Jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**.
 ![Azure-adatbázis PostgreSQL-hez - Kiszolgáló-rendszergazdai bejelentkezés](./media/connect-ruby/1-connection-string.png)
5. Amennyiben elfelejtette a kiszolgáló bejelentkezési adatait, lépjen az **Overview** (Áttekintés) oldalra, és itt megtudhatja a kiszolgáló rendszergazdájának bejelentkezési nevét. Szükség esetén állítsa alaphelyzetbe a jelszót.

<a id="connect-and-create-a-table" class="xliff"></a>

## Csatlakozás és tábla létrehozása
A következő kód segítségével csatlakozzon, és hozzon létre egy táblát a **CREATE TABLE** SQL-utasítással, majd az **INSERT INTO** SQL-utasítással adjon hozzá sorokat a táblához.

A kód egy [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) objektumot használ a [new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) konstruktorral a PostgreSQL-hez készült Azure Database-hez való kapcsolódáshoz. Ezután meghívja az [exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) metódust a DROP, CREATE TABLE és INSERT INTO parancsok futtatásához. A kód a [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error) osztály használatával ellenőrzi a hibákat. Végül pedig a [close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) metódus meghívásával bontja a kapcsolatot, mielőtt kilép.

Cserélje le a `host`, `database`, `user`, és `password` karakterláncokat a saját értékekre. 
```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mypgserver-20170401.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mypgserver-20170401')
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

<a id="read-data" class="xliff"></a>

## Adatok olvasása
Az alábbi kód használatával csatlakozhat és végezheti el az adatok olvasását az adott **SELECT** SQL-utasítás segítségével. 

A kód egy [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) objektumot használ a [new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) konstruktorral a PostgreSQL-hez készült Azure Database-hez való kapcsolódáshoz. Ezután meghívja az [exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) metódust a SELECT parancs futtatásához, az eredményeket az eredményhalmazban megőrizve. Az eredményhalmaz gyűjtése többször is végrehajtódik a `resultSet.each do` ciklus használatával, megőrizve az aktuális sor értékeit a `row` változóban. A kód a [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error) osztály használatával ellenőrzi a hibákat. Végül pedig a [close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) metódus meghívásával bontja a kapcsolatot, mielőtt kilép.

Cserélje le a `host`, `database`, `user`, és `password` karakterláncokat a saját értékekre. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mypgserver-20170401.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mypgserver-20170401')
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

<a id="update-data" class="xliff"></a>

## Adatok frissítése
Az alábbi kód használatával csatlakozhat és végezheti el az adatok módosítását egy **UPDATE** SQL-utasítás segítségével.

A kód egy [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) objektumot használ a [new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) konstruktorral a PostgreSQL-hez készült Azure Database-hez való kapcsolódáshoz. Ezután meghívja az [exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) metódust az UPDATE parancs futtatásához. A kód a [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error) osztály használatával ellenőrzi a hibákat. Végül pedig a [close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) metódus meghívásával bontja a kapcsolatot, mielőtt kilép.

Cserélje le a `host`, `database`, `user`, és `password` karakterláncokat a saját értékekre. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mypgserver-20170401.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mypgserver-20170401')
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


<a id="delete-data" class="xliff"></a>

## Adat törlése
Az alábbi kód használatával csatlakozhat és végezheti el az adatok olvasását egy **DELETE** SQL-utasítás segítségével. 

A kód egy [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) objektumot használ a [new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) konstruktorral a PostgreSQL-hez készült Azure Database-hez való kapcsolódáshoz. Ezután meghívja az [exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) metódust az UPDATE parancs futtatásához. A kód a [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error) osztály használatával ellenőrzi a hibákat. Végül pedig a [close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) metódus meghívásával bontja a kapcsolatot, mielőtt kilép.

Cserélje le a `host`, `database`, `user`, és `password` karakterláncokat a saját értékekre. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mypgserver-20170401.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mypgserver-20170401')
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

<a id="next-steps" class="xliff"></a>

## Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása az Exportálás és importálás lehetőség használatával](./howto-migrate-using-export-and-import.md)

