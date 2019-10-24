---
title: Kapcsolódás SQL Database a C és C++ a | használatával Microsoft Docs
description: Ebben a rövid útmutatóban a mintakód segítségével modern alkalmazást hozhat létre a felhőben található hatékony, a Azure SQL Databaset használó, nagy teljesítményű C++ és a felhőben.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: cpp
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/12/2018
ms.openlocfilehash: c06a16071b1e22e7aa788ff5f15ce8afbf17da04
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568924"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Kapcsolódás SQL Database a C és aC++

A bejegyzés célja, hogy a C C++ és a fejlesztők csatlakozni tudjanak az Azure SQL-adatbázishoz. Szakaszokra bontva, így a legjobban felhasználható szakaszra ugorhat.

## <a name="prerequisites-for-the-cc-tutorial"></a>A C/C++ oktatóanyag előfeltételei

Győződjön meg róla, hogy rendelkezik az alábbi elemekkel:

* Aktív Azure-fiók. Ha még nincs fiókja, regisztrálhat az [Azure ingyenes próbaverziójára](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). A minta létrehozásához C++ és futtatásához telepítenie kell a nyelvi összetevőket.
* A [Visual Studio Linux fejlesztése](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Ha Linux rendszeren fejleszt, a Visual Studio Linux-bővítményt is telepítenie kell.

## <a id="AzureSQL"></a>Azure SQL Database és SQL Server virtuális gépeken
Az Azure SQL Microsoft SQL Server épül, és magas rendelkezésre állást, teljesítményt és skálázható szolgáltatást biztosít. Számos előnnyel jár a SQL Azure használata a helyszíni adatbázison keresztül. A SQL Azure nem kell telepítenie, beállítania, karbantartani vagy kezelnie az adatbázist, de csak az adatbázis tartalmát és szerkezetét. Az olyan adatbázisok esetében, mint például a hibatűrés és a redundancia, az olyan általános dolgok, amiket az összes beépített.

Az Azure jelenleg két lehetőséget kínál az SQL Server-munkaterhelések üzemeltetésére: Azure SQL Database, adatbázis-szolgáltatás és Virtual Machines (VM) SQL Server. Nem fogjuk részletesen megjelenni a két különbség között, kivéve, hogy az Azure SQL Database a legjobb megoldás az új felhőalapú alkalmazások számára, hogy kihasználhassa a Cloud Services által nyújtott költségmegtakarítást és a teljesítmény optimalizálását. Ha a helyszíni alkalmazások felhőbe való áttelepítését vagy kiterjesztését tervezi, az Azure-beli virtuális gépen futó SQL Server jobban dolgozhat Önnek. A cikk egyszerű megtartásához hozzon létre egy Azure SQL Database-adatbázist.

## <a id="ODBC"></a>Adatelérési technológiák: ODBC és OLE DB
Az Azure SQL DB-hez való csatlakozás nem különbözik, és jelenleg kétféleképpen lehet csatlakozni az adatbázisokhoz: ODBC (nyílt adatbázis-kapcsolat) és OLE DB (objektum-csatolási és beágyazási adatbázis). Az elmúlt években a Microsoft az ODBC-vel igazította a [natív kapcsolati](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/)adathozzáférést. Az ODBC viszonylag egyszerű, és sokkal gyorsabb, mint a OLE DB. Az egyetlen kivétel az, hogy az ODBC a régi C stílusú API-t használja.

## <a id="Create"></a>1. lépés:  A Azure SQL Database létrehozása
Tekintse meg az [első lépéseket ismertető oldalt](sql-database-single-database-get-started.md) , amelyből megtudhatja, hogyan hozhat létre egy mintaadatbázis-adatbázist.  Azt is megteheti, hogy ezt a [rövid két perces videót](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) használja az Azure Portal használatával létrehozott Azure SQL Database-adatbázis létrehozásához.

## <a id="ConnectionString"></a>2. lépés:  A kapcsolatok karakterláncának beolvasása
Az Azure SQL Database üzembe helyezése után el kell végeznie az alábbi lépéseket a kapcsolódási adatok meghatározásához és az ügyfél IP-címének a tűzfal-hozzáféréshez való hozzáadásához.

A [Azure Portal](https://portal.azure.com/)az adatbázis áttekintés szakaszának részeként megjelenített **adatbázis** -kapcsolati karakterláncok megjelenítésével nyissa meg az Azure SQL Database ODBC-kapcsolati karakterláncát:

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Másolja az **ODBC (tartalmazza a Node. js fájlt) [SQL Authentication]** karakterláncot. Ezt a karakterláncot később használjuk az C++ ODBC parancssori tolmácsból való kapcsolódáshoz. Ez a karakterlánc részletes adatokat tartalmaz, például az illesztőprogramot, a kiszolgálót és az egyéb adatbázis-kapcsolati paramétereket.

## <a id="Firewall"></a>3. lépés:  Adja hozzá az IP-címet a tűzfalhoz
Nyissa meg az adatbázis-kiszolgáló tűzfal szakaszát, és adja hozzá az [ügyfél IP-címét a tűzfalhoz ezekkel](sql-database-configure-firewall-settings.md) a lépésekkel, és ellenőrizze, hogy sikerült-e sikeres kapcsolatot létesíteni:

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

Ekkor konfigurálta az Azure SQL DB-t, és készen áll a C++ kódokhoz való kapcsolódásra.

## <a id="Windows"></a>4. lépés: Csatlakozás Windows C/C++ alkalmazásból
Az Azure SQL-ADATBÁZIShoz egyszerűen kapcsolódhat az [ODBC használatával a Windowsban](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) a Visual Studióval építhető minta használatával. A minta egy ODBC parancssori tolmácsot valósít meg, amely az Azure SQL-ADATBÁZIShoz való kapcsolódáshoz használható. Ez a példa egy adatbázis forrásának (DSN) fájlját veszi igénybe parancssori argumentumként, vagy pedig a korábban a Azure Portalból másolt részletes kapcsolatok sztringjét. Hozza létre a projekt tulajdonságlapját, és illessze be a (z) paramétert az itt látható módon:

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Győződjön meg arról, hogy a megfelelő hitelesítési adatokat adja meg az adatbázishoz az adatbázis-kapcsolati karakterlánc részeként.

Indítsa el az alkalmazást a létrehozásához. A sikeres kapcsolatok érvényesítéséhez a következő ablaknak kell megjelennie. Az adatbázis-kapcsolat érvényesítéséhez olyan alapszintű SQL-parancsokat is futtathat, mint például a **create Table (tábla létrehozása** ):

![SQL-parancsok](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Azt is megteheti, hogy létrehoz egy DSN-fájlt a varázsló segítségével, amely akkor indul el, ha nincs megadva parancs argumentum. Azt javasoljuk, hogy ezt a lehetőséget is kipróbálhatja. Ezt a DSN-fájlt használhatja az automatizáláshoz és a hitelesítési beállítások védelméhez:

![DSN-fájl létrehozása](./media/sql-database-develop-cplusplus-simple/datasource.png)

Gratulálunk! Sikeresen csatlakozott az Azure SQL-hez a és C++ az ODBC használatával Windows rendszeren. A Linux platformon is megteheti az olvasást.

## <a id="Linux"></a>5. lépés: Csatlakozás linuxos C/C++ alkalmazásból
Ha még nem hallotta a híreket, a Visual Studio mostantól lehetővé teszi a C++ Linux-alkalmazások fejlesztését is. Erről az új forgatókönyvről a [Visual C++ for Linux fejlesztői](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) blogjában olvashat. A Linuxra való kiépítéshez szüksége van egy távoli gépre, amelyen a Linux-disztribúció fut. Ha még nem rendelkezik ilyennel, a [Linux Azure Virtual Machines](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)használatával gyorsan beállíthatja a virtuális gépeket.

Ebben az oktatóanyagban tegyük fel, hogy Ubuntu 16,04 Linux-disztribúció van beállítva. A lépéseket az Ubuntu 15,10, a Red Hat 6 és a Red Hat 7 rendszerre is alkalmazni kell.

A következő lépésekkel telepítheti az SQL és az ODBC számára szükséges kódtárakat a disztribúcióban:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Indítsa el a Visual studiót. Az eszközök – > lehetőségek – > platformfüggetlen – > Csatlakozáskezelő, vegyen fel egy kapcsolódást a linuxos Box-hoz:

![Eszközök beállításai](./media/sql-database-develop-cplusplus-simple/tools.png)

Miután létrejött a kapcsolat az SSH-val, hozzon létre egy üres Project-(Linux-) sablont:

![Új projekt sablonja](./media/sql-database-develop-cplusplus-simple/template.png)

Ezután hozzáadhat egy [új C-forrásfájlt, és lecserélheti ezt a tartalmat](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Az ODBC API-k SQLAllocHandle, a SQLSetConnectAttr és a SQLDriverConnect használatával inicializálhatja és létrehozhatja a kapcsolatot az adatbázissal.
A Windows ODBC-mintához hasonlóan a SQLDriverConnect hívást is le kell cserélnie az adatbázis-kapcsolati karakterlánc paraméterei közül a Azure Portal korábban átmásolt adatokból.

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

A fordítás előtt a legutolsó teendő, ha az **ODBC** -t függvénytár-függőségként adja hozzá:

![ODBC hozzáadása bemeneti kódtárként](./media/sql-database-develop-cplusplus-simple/lib.png)

Az alkalmazás elindításához nyissa meg a Linux-konzolt a hibakeresési menüből:

![Linux-konzol](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Ha sikeresen megtörtént a hozzáférés, az aktuális adatbázis neve jelenik meg a Linux-konzolon:

![A Linux-konzol ablakának kimenete](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Gratulálunk! Sikeresen elvégezte az oktatóanyagot, és most már csatlakozhat az Azure SQL- C++ adatbázishoz Windows-és Linux-platformokon.

## <a id="GetSolution"></a>A teljes C/C++ oktatóanyag megoldás beszerzése
Az ebben a cikkben szereplő összes mintát tartalmazó GetStarted-megoldás megtalálható a GitHubon:

* [ODBC C++ Windows-minta](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), töltse le C++ a Windows ODBC-mintát az Azure SQL-hez való kapcsolódáshoz
* [ODBC C++ Linux](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29)-minta, töltse le C++ a Linux ODBC-mintát az Azure SQL-hez való kapcsolódáshoz

## <a name="next-steps"></a>További lépések
* Tekintse át a [SQL Database fejlesztés áttekintését](sql-database-develop-overview.md)
* További információ az [ODBC API](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/) -referenciáról

## <a name="additional-resources"></a>További források
* [Tervezési minták az Azure SQL Database-t használó több-bérlős SaaS-alkalmazásokhoz](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Fedezze fel az [SQL Database összes képességét](https://azure.microsoft.com/services/sql-database/)

