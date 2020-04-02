---
title: Csatlakozás AZ SQL-adatbázishoz C és C++ használatával
description: Ebben a gyorsindításban a mintakód segítségével egy modern alkalmazást hozhat létre C++ technológiával, és amelyet egy hatékony relációs adatbázis támogat a felhőben az Azure SQL Database használatával.
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
ms.openlocfilehash: ed8e5eaa0ff9b58f80473b052aacfb9f01d45055
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529222"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Csatlakozás AZ SQL-adatbázishoz C és C++ használatával

Ez a bejegyzés az Azure SQL DB-hez csatlakozni próbáló C és C++ fejlesztőknek szól. Szakaszokra van bontva, így arra a szakaszra ugorhat, amely a legjobban megragadja az érdeklődését.

## <a name="prerequisites-for-the-cc-tutorial"></a>A C/C++ oktatóanyag előfeltételei

Győződjön meg róla, hogy rendelkezik az alábbi elemekkel:

* Aktív Azure-fiók. Ha még nincs fiókja, regisztrálhat az [Azure ingyenes próbaverziójára](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). A minta létrehozásához és futtatásához telepítenie kell a C++ nyelvi összetevőket.
* [Visual Studio Linux fejlesztés](https://docs.microsoft.com/cpp/linux/?view=vs-2019). Ha Linuxon dolgozik, telepítenie kell a Visual Studio Linux bővítményt is.

## <a name="azure-sql-database-and-sql-server-on-virtual-machines"></a><a id="AzureSQL"></a>Azure SQL Database és SQL Server virtuális gépeken
Az Azure SQL a Microsoft SQL Server kiszolgálóra épül, és úgy lett kialakítva, hogy magas rendelkezésre állású, nagy teljesítményű és méretezhető szolgáltatást nyújtson. Az SQL Azure használatának számos előnye van a saját, a helyszínen futó adatbázison keresztül. Az SQL Azure-ral nem kell telepítenie, beállítani, karbantartania vagy kezelnie az adatbázist, hanem csak az adatbázis tartalmát és szerkezetét. Az olyan adatbázisok, mint a hibatűrés és a redundancia jellemző dolgok mind be vannak építve.

Az Azure jelenleg két lehetőség üzemelteti az SQL server számítási feladatok: Azure SQL-adatbázis, adatbázis szolgáltatásként és SQL-kiszolgáló virtuális gépeken (VM). Nem fogunk részletekbe bocsátkozni a kettő közötti különbségekről, kivéve, hogy az Azure SQL-adatbázis a legjobb választás az új felhőalapú alkalmazások számára, hogy kihasználhassa a felhőszolgáltatások által nyújtott költségmegtakarítást és teljesítményoptimalizálást. Ha azt fontolgatja, hogy áttelepíti vagy kiterjeszti a helyszíni alkalmazásokat a felhőre, az Azure virtuális gépen lévő SQL-kiszolgáló jobban kiműködhet az Ön számára. Ha a cikk hez a dolgok egyszerűek maradnak, hozzon létre egy Azure SQL-adatbázist.

## <a name="data-access-technologies-odbc-and-ole-db"></a><a id="ODBC"></a>Adatelérési technológiák: ODBC és OLE DB
Az Azure SQL DB-hez való csatlakozás nem különbözik, és jelenleg két módon lehet csatlakozni az adatbázisokhoz: ODBC (Open Database connectivity) és OLE DB (Objektumcsatolás és -beágyazás i. adatbázis). Az elmúlt években a Microsoft az [ODBC-hez igazodott a natív relációs adatok eléréséhez.](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/) ODBC viszonylag egyszerű, és sokkal gyorsabb, mint az OLE DB. Az egyetlen kikötés itt az, hogy ODBC nem használ egy régi C-stílusú API.The only caveat here is that ODBC does use an old C-style API.

## <a name="step-1--creating-your-azure-sql-database"></a><a id="Create"></a>1. lépés: Az Azure SQL-adatbázis létrehozása
Tekintse meg az [első lépések lapot,](sql-database-single-database-get-started.md) hogy hogyan hozhat létre mintaadatbázist.  Másik lehetőségként kövesse ezt a [rövid, kétperces videót](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) egy Azure SQL-adatbázis létrehozásához az Azure Portalhasználatával.

## <a name="step-2--get-connection-string"></a><a id="ConnectionString"></a>2. lépés: Kapcsolati karakterlánc bekése
Az Azure SQL-adatbázis kiépítése után el kell végeznie a következő lépéseket a kapcsolati adatok meghatározásához és az ügyfél IP-címének hozzáadásához a tűzfalhoz való hozzáféréshez.

Az [Azure Portalon](https://portal.azure.com/)nyissa meg az Azure SQL-adatbázis ODBC-kapcsolati karakterláncát az adatbázis áttekintő szakaszának részeként felsorolt **adatbázis-kapcsolati karakterláncok megjelenítése** használatával:

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Másolja az **ODBC (Tartalmazza a Node.js fájlt) [SQL-hitelesítés]** karakterlánc tartalmára. Ezt a karakterláncot később használjuk a C++ ODBC parancssori értelmezőből való csatlakozáshoz. Ez a karakterlánc olyan részleteket tartalmaz, mint például az illesztőprogram, a kiszolgáló és az adatbázis egyéb kapcsolati paraméterei.

## <a name="step-3--add-your-ip-to-the-firewall"></a><a id="Firewall"></a>3. lépés: Az IP hozzáadása a tűzfalhoz
Nyissa meg az adatbázis-kiszolgáló tűzfalszakaszát, és adja hozzá az [ügyfél IP-címét a tűzfalhoz az alábbi lépésekkel,](sql-database-configure-firewall-settings.md) hogy megbizonyosodjon arról, hogy sikeres kapcsolatot tudunk létesíteni:

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

Ezen a ponton konfigurálta az Azure SQL DB-t, és készen áll a C++ kódból való csatlakozásra.

## <a name="step-4-connecting-from-a-windows-cc-application"></a><a id="Windows"></a>4. lépés: Csatlakozás Windows C/C++ alkalmazásból
Könnyedén csatlakozhat az [Azure SQL DB-hoz az ODBC windowsos használatával ezzel a visual](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) studióval épülő mintával. A minta egy ODBC parancssori értelmezőt valósít meg, amely az Azure SQL DB-hez való csatlakozáshoz használható. Ez a minta egy adatbázis-forrásnévfájl (DSN) fájlt vesz fel parancssori argumentumként, vagy az Azure Portalról korábban másolt részletes kapcsolati karakterláncot. A projekt tulajdonságlapjának létrehozása és a kapcsolati karakterlánc beillesztése parancsargumentumként az itt látható módon:

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Győződjön meg arról, hogy az adatbázis kapcsolati karakterláncának részeként adja meg az adatbázis megfelelő hitelesítési adatait.

Indítsa el az alkalmazást a létrehozásához. A sikeres kapcsolatot érvényesítő következő ablaknak kell megtekintenie. Néhány alapvető SQL-parancsot is futtathat, **például táblát hozhat létre** az adatbázis-kapcsolat érvényesítéséhez:

![SQL-parancsok](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Másik lehetőségként létrehozhat egy DSN-fájlt a varázsló val, amely akkor indul el, ha nincs megadva parancsargumentum. Javasoljuk, hogy próbálja meg ezt a lehetőséget is. Ezt a DSN-fájlt használhatja az automatizáláshoz és a hitelesítési beállítások védelméhez:

![DSN-fájl létrehozása](./media/sql-database-develop-cplusplus-simple/datasource.png)

Gratulálunk! Ezzel sikeresen csatlakozott az Azure SQL-hez a C++ és az ODBC használatával Windows rendszeren. Folytathatja az olvasást, hogy ugyanezt tegye a Linux platformon is.

## <a name="step-5-connecting-from-a-linux-cc-application"></a><a id="Linux"></a>5. lépés: Csatlakozás Linux C/C++ alkalmazásból
Abban az esetben, ha még nem hallotta a híreket, a Visual Studio most lehetővé teszi a C++ Linux alkalmazás fejlesztését is. Erről az új forgatókönyvről a [Visual C++ for Linux Development](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) blogban olvashat. A Linux hoz való építéshez egy távoli gépre van szükség, ahol a Linux disztribúció fut. Ha nem rendelkezik elérhetővel, a Linux Azure virtuális gépek használatával gyorsan [beállíthatja.](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Ebben a bemutatóban tegyük fel, hogy van egy Ubuntu 16.04 Linux disztribúció. Az itt leírt lépések nek az Ubuntu 15.10, a Red Hat 6 és a Red Hat 7-re is vonatkozniuk kell.

A következő lépések telepítik a distro hoz szükséges SQL és ODBC könyvtárakat:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Indítsa el a Visual Studiót. Az Eszközök -> beállítások -> Cross Platform -> Connection Manager csoportban adjon hozzá egy kapcsolatot a Linux dobozához:

![Eszközök beállításai](./media/sql-database-develop-cplusplus-simple/tools.png)

Az SSH-n keresztüli kapcsolat létrejötte után hozzon létre egy Üres projekt (Linux) sablont:

![Új projektsablon](./media/sql-database-develop-cplusplus-simple/template.png)

Ezután hozzáadhat egy [új C forrásfájlt, és lecserélheti erre a tartalomra](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Az SQLAllocHandle, AZ SQLSetConnectAttr és az SQLDriverConnect ODBC API-k használatával inicializálhatja és kapcsolatot hozhat létre az adatbázissal.
A Windows ODBC-mintához hasonlóan le kell cserélnie az SQLDriverConnect hívást az Azure Portalról korábban másolt adatbázis-kapcsolati karakterlánc-paraméterek részleteire.

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Az utolsó dolog, amit a fordítás előtt meg kell tenni, hogy **add odbc** könyvtárfüggőségként:

![ODBC hozzáadása beviteli tárként](./media/sql-database-develop-cplusplus-simple/lib.png)

Az alkalmazás elindításához hozza létre a Linux konzolt a **Hibakeresési** menüből:

![Linux konzol](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Ha a kapcsolat sikeres volt, most látnia kell az aktuális adatbázisnevet a Linux konzolon:

![Linux konzolablak kimenete](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Gratulálunk! Sikeresen befejezte az oktatóanyagot, és most már csatlakozhat az Azure SQL DB-hoz C++ -ból Windows és Linux platformokon.

## <a name="get-the-complete-cc-tutorial-solution"></a><a id="GetSolution"></a>A teljes C/C++ oktatóanyag-megoldás beszerezni
A GetStarted megoldás, amely tartalmazza az összes mintát ebben a cikkben a GitHubon:

* [ODBC C++ Windows-minta](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), Töltse le a Windows C++ ODBC mintát az Azure SQL-hez való csatlakozáshoz
* [ODBC C++ Linux minta](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), Töltse le a Linux C++ ODBC mintát az Azure SQL-hez való csatlakozáshoz

## <a name="next-steps"></a>További lépések
* Az [SQL-adatbázis-fejlesztési áttekintés áttekintése](sql-database-develop-overview.md)
* További információ az [ODBC API-referenciáról](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>További források
* [Tervezési minták az Azure SQL Database-t használó több-bérlős SaaS-alkalmazásokhoz](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Fedezze fel az [SQL Database összes képességét](https://azure.microsoft.com/services/sql-database/)

