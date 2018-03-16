---
title: "Csatlakozás SQL Database adatbázishoz C és C++ |} Microsoft Docs"
description: "A gyors üzembe helyezési példakód használatával a modern alkalmazás C++ és egy hatékony relációs adatbázis a felhőben az Azure SQL Database biztonsági mentés létrehozása."
services: sql-database
author: edmacauley
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.devlang: cpp
ms.topic: article
ms.date: 03/06/2017
ms.author: edmacauley
ms.openlocfilehash: 659b413b2686a50a0681e7307b51188e6e6d0859
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Csatlakozás SQL Database adatbázishoz C és C++ használatával
A feladás egy vagy több fejlesztésére C és C++ fejlesztők számára az Azure SQL Adatbázishoz csatlakozni próbál. Az bontani szakaszok, akkor a, amely a legjobban rögzíti az érdeklődését szakaszra is ugorhat. 

## <a name="prerequisites-for-the-cc-tutorial"></a>A C/C++-oktatóanyag előfeltételei
Győződjön meg róla, hogy rendelkezik az alábbi elemekkel:

* Aktív Azure-fiók. Ha még nincs fiókja, regisztrálhat az [Azure ingyenes próbaverziójára](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). A C++ nyelvi összetevők build és a minta futtatásához telepíteni kell.
* [Visual Studio Linux fejlesztői](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Ha Linux rendszeren fejleszt, a Visual Studio Linux bővítmény is telepítenie kell. 

## <a id="AzureSQL"></a>Az Azure SQL Database és SQL Server virtuális gépeken
Azure SQL a Microsoft SQL Server épül, és egy magas rendelkezésre állású, performant és méretezhető szolgáltatást biztosít. Nincsenek számos előnyt keresztül a saját adatbázis helyi futó SQL Azure használatával. Az SQL Azure nem kell telepíteni, állítsa be, karbantartása vagy az adatbázis, de csak a tartalmat, és az adatbázis szerkezetének kezelése. Azt az adatbázisok, például a hibatűrés és a redundancia összes beépített foglalkoznia tipikus tényezőket. 

Azure SQL server-munkaterhelések két lehetősége van: Azure SQL-adatbázis, a szolgáltatás és a virtuális gépek (VM) SQL server adatbázis. Nem Microsoft kap e két közötti különbségekről részletes, azzal a különbséggel, hogy az Azure SQL-adatbázis a legjobb megoldás új felhőalapú alkalmazásokhoz, hogy kihasználja a költséghatékony, és biztosítja, hogy a szolgáltatások teljesítmény optimalizálása. Ha tervezi az áttelepítésével vagy kiterjesztése a felhőbe a helyszíni alkalmazások, az SQL server Azure virtuális gépen működhetnek jobban meg. Ebben a cikkben egyszerű dolgot megtartásához hozzon létre egy Azure SQL-adatbázis. 

## <a id="ODBC"></a>Adat-hozzáférési technológiák: ODBC és OLE DB
Kapcsolódás Azure SQL Database ugyanolyan helyzetet teremt, és jelenleg két módon adatbázisaihoz való kapcsolódásra: ODBC (Nyissa meg az adatbázis-kapcsolat) és az OLE-DB (objektum csatolása és beágyazása adatbázis). Az elmúlt években Microsoft összhangban van [a natív relációs adatok elérése ODBC](https://blogs.msdn.microsoft.com/sqlnativeclient/2011/08/29/microsoft-is-aligning-with-odbc-for-native-relational-data-access/). ODBC viszonylag egyszerű, de még sokkal gyorsabb, mint az OLE DB. Itt csak szerint, az ODBC egy régi C-stílusú API-t használja. 

## <a id="Create"></a>1. lépés: Az Azure SQL-adatbázis létrehozása
Tekintse meg a [első lépések lap](sql-database-get-started-portal.md) megtudhatja, hogyan hozhat létre a minta-adatbázist.  Azt is megteheti, amelyeket követve ez [rövid kétperces videó](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) az Azure portál használatával az Azure SQL-adatbázis létrehozásához.

## <a id="ConnectionString"></a>2. lépés: A kapcsolati karakterlánc beolvasása
Miután az Azure SQL-adatbázis van megadva, kell hajthat végre az alábbi lépéseket a kapcsolati adatokat, és adja hozzá a tűzfalhozzáférés az ügyfél IP-Címek. 

A [Azure-portálon](https://portal.azure.com/), navigáljon az Azure SQL-adatbázis ODBC kapcsolati karakterlánc használatával a **adatbázis-kapcsolati karakterláncok megjelenítése** felsorolt a áttekintés szakaszban az adatbázis részeként: 

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

A tartalom másolása a **(tartalmazza a Node.js) [SQL-hitelesítés] ODBC** karakterlánc. Használjuk a karakterlánc később a C++ ODBC parancssori parancsértelmező csatlakozni. Ez a karakterlánc részletesen bemutatja a például az illesztőprogram, a kiszolgáló és a más adatbázis paramétereit. 

## <a id="Firewall"></a>3. lépés: Az IP-cím hozzáadása a tűzfalhoz
Nyissa meg az adatbázis-kiszolgáló a tűzfal szakaszát, és adja hozzá a [ügyfél IP-cím kötése a tűzfal, az alábbi lépések végrehajtásával](sql-database-configure-firewall-settings.md) győződjön meg arról is létesítünk a sikeres kapcsolat: 

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

Ezen a ponton konfigurálta-e az Azure SQL-Adatbázisba, és készen áll a C++ kódból csatlakozni. 

## <a id="Windows"></a>4. lépés: Csatlakozás Windows C/C++-alkalmazás
Egyszerűen csatlakozhat a [ODBC használatával Ez a minta használata Windows Azure SQL DB](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) , amely a Visual Studio létrehozza. A minta csatlakozni az Azure SQL Database használható parancssori ODBC-értelmező valósítja meg. Ez a minta időt vesz igénybe, vagy egy adatbázis adatforrás neve (DSN) fájl parancssori argumentumként vagy a részletes kapcsolati karakterláncot, amely azt kimásolt Azure-portálról. Ebben a projektben a tulajdonság lapot, és illessze be a kapcsolati karakterláncot egy parancs argumentumként, ahogy az itt látható: 

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Győződjön meg arról, hogy megfelelő hitelesítési adatainak megadása az adatbázis az adott adatbázis-kapcsolati karakterlánc részeként. 

Indítsa el az alkalmazás létrehozásához. A sikeres kapcsolat ellenőrzése a következő ablak kell megjelennie. Néhány alapvető SQL-parancsok például is futtathat **tábla létrehozása** az adatbázis-kapcsolat ellenőrzése:

![SQL-parancsok](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Másik lehetőségként létrehozhatja egy DSN fájlt, a varázsló, amely elindul, amikor nincs parancs argumentum van megadva. Azt javasoljuk, hogy ez a beállítás meg. A DSN-fájl használható automatizálási védelem, valamint a hitelesítési beállításokat: 

![DSN-fájl létrehozása](./media/sql-database-develop-cplusplus-simple/datasource.png)

Gratulálunk! Most már sikeresen csatlakozott C++ és ODBC használata a Windows Azure SQL. Ehhez a Linux platformon, valamint olvasási tovább. 

## <a id="Linux"></a>5. lépés: Csatlakozás egy Linux-C/C++-alkalmazás
Ha még nem kérték egyes a híreket, miközben a Visual Studio lehetővé teszi az kifejleszthet C++ Linux alkalmazást is. Az új forgatókönyv olvashat a [Linux fejlesztési Visual C++](https://blogs.msdn.microsoft.com/vcblog/2016/03/30/visual-c-for-linux-development/) blog. Linux létrehozásához szüksége a Linux distro futtató távoli számítógép. Ha még nem rendelkezik érhető el, beállíthat egy be gyorsan segítségével [Linux Azure virtuális gépek](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Ebben az oktatóanyagban Tételezzük fel, hogy rendelkezik-e az Ubuntu 16.04 Linux terjesztési beállítása. Az itt lépéseket Ubuntu 15.10, Red Hat 6 és Red Hat 7 is alkalmazni kell. 

Az alábbi lépéseket a könyvtárak a distro szükséges az SQL és az ODBC telepítése:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Indítsa el a Visual Studio. Az eszközök -> Beállítások -> Cross Platform -> Csatlakozáskezelő, kapcsolat hozzáadása a Linux mezőbe: 

![Eszközök beállításai](./media/sql-database-develop-cplusplus-simple/tools.png)

SSH-n keresztül kapcsolat létrejötte után hozzon létre egy üres projektsablon (Linux): 

![Új projekt sablon](./media/sql-database-develop-cplusplus-simple/template.png)

Ezután hozzáadhat egy [új C forrásfájl, és cserélje le ezt a tartalmat](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Az ODBC API-k SQLAllocHandle SQLSetConnectAttr és SQLDriverConnect használ, meg kell tudni inicializálása és kapcsolatot létrehozni az adatbázist. Például a Windows ODBC mintával kell a SQLDriverConnect hívás cserélje le az adatbázis-kapcsolati karakterlánc paraméterek másolja az Azure portálról korábban adataiból. 

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Az utolsó lépés előtt fordítása hozzáadása **odbc** könyvtár függőségei: 

![ODBC egy bemeneti könyvtár hozzáadása](./media/sql-database-develop-cplusplus-simple/lib.png)

Az alkalmazás indításához a Linux konzol elindítani a **Debug** menüben: 

![Linux-konzol](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Ha a kapcsolat sikeres volt, meg kell jelennie az aktuális adatbázis neve, a Linux-konzolban: 

![Linux konzol kimenetét](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Gratulálunk! Sikeresen végrehajtotta az oktatóanyag, és most csatlakozhatnak az Azure SQL Database C++ Windows és Linux platformokon.

## <a id="GetSolution"></a>A teljes C/C++-oktatóanyag megoldás beszerzése
Ez a cikk a github webhelyen szereplő összes mintát tartalmazó GetStarted-megoldás található:

* [ODBC C++ Windows minta](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), töltse le a Windows C++ ODBC minta kapcsolódni az Azure SQL
* [ODBC C++ Linux minta](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), töltse le a Linux C++ ODBC minta Azure SQL való kapcsolódáshoz

## <a name="next-steps"></a>További lépések
* Tekintse át a [SQL adatbázis-fejlesztői áttekintés](sql-database-develop-overview.md)
* További információ a [ODBC API-referencia](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>További források
* [Tervezési minták az Azure SQL Database-t használó több-bérlős SaaS-alkalmazásokhoz](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Fedezze fel az [SQL Database összes képességét](https://azure.microsoft.com/services/sql-database/)

