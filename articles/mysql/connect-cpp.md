---
title: "Csatlakozás a MySQL-hez készült Azure Database-hez a C++ segítségével | Microsoft Docs"
description: "Ez a rövid útmutató egy C++-mintakódot biztosít, amellyel csatlakozhat a MySQL-hez készült Azure Database-hez, illetve adatokat kérdezhet le róla."
services: mysql
author: seanli1988
ms.author: seal
manager: janders
editor: jasonwhowell
ms.service: mysql
ms.custom: mvc
ms.devlang: C++
ms.topic: quickstart
ms.date: 09/22/2017
ms.openlocfilehash: 92620c8081b1f0f5c96cc3ae09465b3526e74042
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-mysql-use-connectorc-to-connect-and-query-data"></a>A MySQL-hez készült Azure Database: Csatlakozás és adatlekérdezés összekötő/C++ használatával
A gyors üzembe helyezés bemutatja, hogyan kell csatlakoztatni egy Azure-adatbázis a MySQL egy C++ alkalmazás használatával. Azt is bemutatja, hogyan lehet SQL-utasítások használatával adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban. Ez a témakör azt feltételezi, hogy jártas C++ használatával történő fejlesztéséhez és, hogy még nem ismeri a MySQL az Azure-adatbázis használata.

## <a name="prerequisites"></a>Előfeltételek
A gyors üzembe helyezés a kiindulási pontként az alábbi útmutatókban valamelyikével létrehozott erőforrásokat használ:
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](./quickstart-create-mysql-server-database-using-azure-cli.md)

Emellett a következőket kell elvégezni:
- A [.NET-keretrendszer](https://www.microsoft.com/net/download) telepítése
- A [Visual Studio](https://www.visualstudio.com/downloads/) telepítése
- A [MySQL-összekötő/C++](https://dev.mysql.com/downloads/connector/cpp/) telepítése 
- A [Boost](http://www.boost.org/) telepítése

## <a name="install-visual-studio-and-net"></a>A Visual Studio és a .NET telepítése
A jelen szakaszban ismertetett lépések feltételezik, hogy Ön rendelkezik .NET-fejlesztési tapasztalatokkal.

### <a name="windows"></a>**Windows**
- Telepítse a Visual Studio 2017 Közösség, amely egy bővíthető, szabad IDE az Android, iOS, Windows, valamint webalkalmazás modern alkalmazások és adatbázis-alkalmazások létrehozására vonatkozó kiemelt, teljes és a felhőalapú szolgáltatások. A teljes .NET-keretrendszer vagy csak a .NET Core telepítése: a gyorsindító kódrészletek sem működik. Ha már rendelkezik a számítógépen telepített Visual Studio, ugorjon a következő két lépést.
   1. Töltse le a [Visual Studio 2017 telepítőt](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15). 
   2. Futtassa a telepítőt, és kövesse a telepítési utasításokat a telepítés befejezéséhez.

### <a name="configure-visual-studio"></a>**A Visual Studio konfigurálása**
1. A Visual Studióban a project property (projekttulajdonság) > configuration properties (konfigurációs tulajdonságok) > C/C++ > linker (kapcsolószerkesztő) > general (általános) > additional library directories (további kódtárkönyvtárak) menüpontban adja hozzá a c++-összekötő lib\opt könyvtárát (pl.: C:\Program Files (x86)\MySQL\MySQL Connector C++ 1.1.9\lib\opt).
2. A Visual Studio projekt tulajdonság > konfigurációs tulajdonságok > C/C++ > Általános > További könyvtárak tartalmazza:
   - Adja hozzá tartalmaznak / c ++-összekötő könyvtár (pl.: C:\Program Files (x86) \MySQL\MySQL összekötő C++ 1.1.9\include\).
   - Adja hozzá a program könyvtár gyökérkönyvtár (pl.: C:\boost_1_64_0\).
3. A Visual Studio projekt tulajdonság > konfigurációs tulajdonságok > C/C++ > linker > bemeneti > További függőségek, a szövegmező mysqlcppconn.lib vegyen fel.
4. A C++ összekötő könyvtármappa a 3. lépésben mysqlcppconn.dll másolja az alkalmazás futtatható fájlja könyvtárába, vagy adja hozzá a környezeti változóhoz, az alkalmazás találja meg.

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le a MySQL-hez készült Azure Database-hez való csatlakozáshoz szükséges kapcsolatadatokat. Ehhez szükség lesz a teljes kiszolgálónévre és bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Azure-portálon a bal oldali menüben kattintson a **összes erőforrás**, és a létrehozott kiszolgáló keressen (például **myserver4demo**).
3. Kattintson a kiszolgálónévre.
4. Válassza ki a kiszolgálót **tulajdonságok** lapon, majd jegyezze fel a **kiszolgálónév** és **kiszolgálói rendszergazda bejelentkezési név**.
 ![A MySQL-hez készült Azure Database-kiszolgáló neve](./media/connect-cpp/1_server-properties-name-login.png)
5. Ha elfelejti a kiszolgálói bejelentkezési adatok, navigáljon a **áttekintése** lapon megtekintéséhez a rendszergazdai bejelentkezési nevet, és ha szükséges a jelszó alaphelyzetbe állítása.

## <a name="connect-create-table-and-insert-data"></a>Csatlakozás, táblák létrehozása és adatok beszúrása
Az alábbi kód segítségével csatlakozzon, és az adatok betöltése a **CREATE TABLE** és **INSERT INTO** SQL-utasításokat. A kód az sql::Driver osztályt használja a connect() metódussal a MySQL-lel létesített kapcsolat létrehozásához. A kód ezután a createStatement() és az execute() metódust használja az adatbázis-parancsok futtatásához. 

Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/prepared_statement.h>
using namespace std;

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::Statement *stmt;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver>connect("tcp://myserver4demo.mysql.database.azure.com:3306/quickstartdb", "myadmin@myserver4demo", "server_admin_password");
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to database. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }

    stmt = con>createStatement();
    stmt>execute("DROP TABLE IF EXISTS inventory");
    cout << "Finished dropping table (if existed)" << endl;
    stmt>execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);");
    cout << "Finished creating table" << endl;
    delete stmt;

    pstmt = con>prepareStatement("INSERT INTO inventory(name, quantity) VALUES(?,?)");
    pstmt>setString(1, "banana");
    pstmt>setInt(2, 150);
    pstmt>execute();
    cout << "One row inserted." << endl;

    pstmt>setString(1, "orange");
    pstmt>setInt(2, 154);
    pstmt>execute();
    cout << "One row inserted." << endl;

    pstmt>setString(1, "apple");
    pstmt>setInt(2, 100);
    pstmt>execute();
    cout << "One row inserted." << endl;
    
    delete pstmt;   
    delete con;
    system("pause");
    return 0;

```

## <a name="read-data"></a>Adatok olvasása

Az alábbi kód segítségével csatlakozzon, és az adatok olvasása a egy **válasszon** SQL-utasításban. A kód az sql::Driver osztályt használja a connect() metódussal a MySQL-lel létesített kapcsolat létrehozásához. A kód ezután a prepareStatement() és az executeQuery() metódust használja a select-parancsok futtatásához. A kód a következő next() használja ahhoz, hogy a rekordok az eredmények között. Végezetül az használ getInt() és getString() elemezni a bejegyzésben szereplő értékeket.

Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg. 

```csharp
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver>connect("tcp://myserver4demo.mysql.database.azure.com:3306/quickstartdb", "myadmin@myserver4demo", "server_admin_password");
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to database. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }   

//  select  
    pstmt = con>prepareStatement("SELECT * FROM inventory;");
    result = pstmt>executeQuery();  
    
    while (result>next())
        printf("Reading from table=(%d, %s, %d)\n", result>getInt(1), result>getString(2).c_str(), result>getInt(3));   
    
    delete result;
    delete pstmt;   
    delete con;
    system("pause");
    return 0;
}
```

## <a name="update-data"></a>Adatok frissítése
Az alábbi kód segítségével csatlakozzon, és az adatok olvasása a egy **frissítés** SQL-utasításban. A kód az sql::Driver osztályt használja a connect() metódussal a MySQL-lel létesített kapcsolat létrehozásához. A kód ezután a prepareStatement() és az executeQuery() metódust használja az update-parancsok futtatásához. 

Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg. 

```csharp
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/prepared_statement.h>
using namespace std;

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver>connect("tcp://myserver4demo.mysql.database.azure.com:3306/quickstartdb", "myadmin@myserver4demo", "server_admin_password");
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to database. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }   

    //update
    pstmt = con>prepareStatement("UPDATE inventory SET quantity = ? WHERE name = ?");
    pstmt>setInt(1, 200);
    pstmt>setString(2, "banana");
    pstmt>executeQuery();
    printf("Row updated\n");
    
    delete con;
    delete pstmt;
    system("pause");
    return 0;
}
```


## <a name="delete-data"></a>Adat törlése
Az alábbi kód segítségével csatlakozzon, és az adatok olvasása a egy **törlése** SQL-utasításban. A kód az sql::Driver osztályt használja a connect() metódussal a MySQL-lel létesített kapcsolat létrehozásához. A kód ezután a prepareStatement() és az executeQuery() metódust használja a delete-parancsok futtatásához.

Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg. 

```csharp
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver>connect("tcp://myserver4demo.mysql.database.azure.com:3306/quickstartdb", "myadmin@myserver4demo", "server_admin_password");
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to database. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }
        
    //delete
    pstmt = con>prepareStatement("DELETE FROM inventory WHERE name = ?");
    pstmt>setString(1, "orange");
    result = pstmt>executeQuery();
    printf("Row deleted\n");    
    
    delete pstmt;
    delete con;
    delete result;
    system("pause");
    return 0;
}
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [MySQL-adatbázis migrálása a MySQL-hez készült Azure Database-be memóriakép és visszaállítás használatával](concepts-migrate-dump-restore.md)
