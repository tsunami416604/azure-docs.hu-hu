---
title: Csatlakozás SQL Database adatbázishoz C és C++ segítségével |} A Microsoft Docs
description: Ez a rövid útmutatóban a mintakód segítségével hozhat létre egy olyan alkalmazás, c++ és a egy hatékony relációs adatbázis a felhőben az Azure SQL Database által.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: cpp
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/12/2018
ms.openlocfilehash: 6ebef74cee6f919fe6b8cf666db06e0ab22cec73
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55566924"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Csatlakozás SQL Database adatbázishoz C és C++ használatával

Ebben a bejegyzésben szeretne csatlakozni az Azure SQL DB C és C++ fejlesztői célozza meg. Azt az szakaszból így leginkább az érdeklődését rögzíti szakasz is ugorhat.

## <a name="prerequisites-for-the-cc-tutorial"></a>A C/C++ oktatóanyag előfeltételei

Győződjön meg róla, hogy rendelkezik az alábbi elemekkel:

* Aktív Azure-fiók. Ha még nincs fiókja, regisztrálhat az [Azure ingyenes próbaverziójára](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). A C++ nyelvi összetevőkkel való létrehozásához és a minta futtatásához telepíteni kell.
* [A Visual Studio Linux fejlesztési](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Ha Linux rendszeren fejleszt, a Visual Studio Linux bővítmény is telepítenie kell.

## <a id="AzureSQL"></a>Az Azure SQL Database és az SQL Server virtuális gépeken
Az Azure SQL épül Microsoft SQL Server és a egy magas rendelkezésre állású, nagy teljesítményű és skálázható szolgáltatást biztosít. Nincsenek SQL Azure-val a saját adatbázis, a helyszínen fut, számos előnnyel jár. Az SQL Azure nem kell telepíteni, állítsa be, kezelése vagy kezelheti az adatbázis, de csak a tartalom és az adatbázis szerkezetének. Tipikus dolog, hogy aggódnia, hibatűrés és a redundancia összes beépített adatbázisok.

Az Azure jelenleg rendelkezik üzemeltető SQL server számítási feladatok esetében két lehetőség közül választhat: Az Azure SQL database-adatbázis és az SQL server a virtuális gépek (VM). Nem tudjuk kap ezt a két közötti különbségekről részletesen, azzal a különbséggel, hogy az Azure SQL database a legjobb választás az új felhőalapú alkalmazások kihasználhatja a költségmegtakarításokat és a felhőszolgáltatások által teljesítményoptimalizálás nyújt. Áttelepítésével vagy kiterjesztése a felhőre a helyszíni alkalmazások használatát fontolgatja, ha az SQL server Azure virtuális gépen a Önnek jobban működnek. Az egyszerűség ebben a cikkben, hozzunk létre egy Azure SQL database.

## <a id="ODBC"></a>Adat-hozzáférési technológiák: ODBC- és OLE DB
Csatlakozás az Azure SQL DB semmiben nem különbözik, és jelenleg két módon adatbázisaihoz való kapcsolódásra: ODBC (Nyissa meg az adatbázis-kapcsolat) és az OLE-DB (objektum csatolását és beágyazása adatbázis). Az utóbbi években, a Microsoft az igazított [natív relációs adatok elérésére vonatkozó ODBC](https://blogs.msdn.microsoft.com/sqlnativeclient/2011/08/29/microsoft-is-aligning-with-odbc-for-native-relational-data-access/). ODBC viszonylag egyszerű, de még sokkal gyorsabb, mint az OLE DB. Itt csak csoportosítani, hogy az ODBC egy régi C stílusú API-t használja.

## <a id="Create"></a>1. lépés:  Az Azure SQL-adatbázis létrehozása
Tekintse meg a [első lépések lap](sql-database-get-started-portal.md) megtudhatja, hogyan hozhat létre egy mintaadatbázist.  Másik lehetőségként kövesse ezt [rövid kétperces videó](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) hozhat létre egy Azure SQL database, az Azure portal használatával.

## <a id="ConnectionString"></a>2. lépés:  Kapcsolati sztring lekérése
Miután az Azure SQL database kiépítését követően kell végezzük el az alábbi lépések végrehajtásával állapítsa meg a kapcsolati adatokat, és tűzfalhozzáférés az ügyfél IP-cím hozzáadása.

A [az Azure portal](https://portal.azure.com/), lépjen az Azure SQL Database ODBC kapcsolati karakterlánc használatával a **adatbázis kapcsolati karakterláncainak megjelenítése** felsorolva az Áttekintés szakaszban az adatbázis részeként:

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Másolja ki a tartalmát a **ODBC (tartalmazza a node.js-t) [SQL-hitelesítés]** karakterlánc. Ez a karakterlánc később arra használjuk a C++ ODBC parancssori interpret elérését. Ez a karakterlánc például az illesztőprogram, a kiszolgáló és az egyéb adatbázis-kapcsolódási paraméterek biztosít.

## <a id="Firewall"></a>3. lépés:  A tűzfalhoz az IP-Címének hozzáadása
Nyissa meg az adatbázis-kiszolgáló a tűzfal szakaszban, és adja hozzá a [ügyfél IP-Címét az alábbi lépések végrehajtásával a tűzfalhoz](sql-database-configure-firewall-settings.md) , győződjön meg arról is, hogy a sikeres kapcsolat létrehozásához:

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

Ezen a ponton konfigurálta az Azure SQL-Adatbázisba, és készen áll a C++-code-ból kapcsolódni.

## <a id="Windows"></a>4. lépés: A Windows C/C++-alkalmazás csatlakoztatása
Könnyedén csatlakozhat a [ODBC használatával Ez a minta használata a Windows Azure SQL DB](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) , amely összeállítja a Visual Studio használatával. A minta az Azure SQL DB-hez való kapcsolódáshoz használható parancssori ODBC-értelmező valósítja meg. Ez a minta egy adatbázis forrás neve (DSN) fájl, argumentumként a parancssorban vagy részletes kapcsolati karakterlánc, hogy a az Azure Portalról korábban kimásolt vesz igénybe. A tulajdonság lapot a projekthez, és illessze be a kapcsolati karakterláncot egy parancs argumentumaként itt látható módon:

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Győződjön meg arról, adja meg a megfelelő hitelesítési adatait az adatbázis az adott adatbázis-kapcsolati karakterlánc részeként.

Indítsa el a építheti fel az alkalmazást. A sikeres kapcsolat ellenőrzése az alábbi ablak kell megjelennie. Például néhány alapvető SQL-parancsokat is futtathat **tábla létrehozása** az adatbázis-kapcsolat ellenőrzése:

![SQL-parancsok](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Azt is megteheti sikerült létrehozni a varázslóval elindul, amikor nincs parancs argumentumait DSN fájlba. Azt javasoljuk, hogy megpróbálja ezt a beállítást. Automation és a hitelesítési beállítások védelme a DSN-fájlt is használhat:

![Create DSN File](./media/sql-database-develop-cplusplus-simple/datasource.png)

Gratulálunk! Most már sikeresen csatlakoztatta az Azure SQL, C++ és az ODBC segítségével a Windows. Az előbbiek Linux rendszeren, valamint az olvasási továbbra is.

## <a id="Linux"></a>5. lépés: A Linux C/C++-alkalmazás csatlakoztatása
Abban az esetben a hírek még nem meghallgatni, Visual Studio mostantól lehetővé teszi, valamint C++ Linux-alkalmazás fejlesztése. Ez az új forgatókönyv olvashat a [Linux-fejlesztéshez a Visual C++](https://blogs.msdn.microsoft.com/vcblog/2016/03/30/visual-c-for-linux-development/) blog. Hozhat létre Linux rendszerű, szüksége van egy, a Linux-disztribúció futtató távoli gépen. Ha még nem érhető el, állíthatja be gyorsan egy [Linux rendszerű Azure-beli virtuális gépek](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Ebben az oktatóanyagban tegyük fel, hogy rendelkezik-e egy Ubuntu 16.04 Linux-disztribúció, állítsa be. A lépések itt is érvényesek Ubuntu 15.10, Red Hat 6 és Red Hat-7.

Az alábbi lépéseket az SQL és az ODBC a disztribúció a szükséges kódtárak telepítése:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Indítsa el a Visual Studiót. Eszközök -> Beállítások -> Cross Platform -> kezelő, vegyen fel egy kapcsolatot a Linux mezőben:

![Eszközök beállításai](./media/sql-database-develop-cplusplus-simple/tools.png)

SSH-kapcsolat létrejötte után hozzon létre egy üres projektsablonnal (Linux):

![Új webesprojekt-sablon](./media/sql-database-develop-cplusplus-simple/template.png)

Ezután hozzáadhatja egy [új C forrás-fájlt, és cserélje le a tartalmat](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Az ODBC-API-k SQLAllocHandle SQLSetConnectAttr és SQLDriverConnect használ, meg kell tudni inicializálni és a egy kapcsolatot az adatbázis.
Például a Windows ODBC-mintával kell a SQLDriverConnect hívás cserélje le az adatbázis-kapcsolati karakterlánc paraméterei az Azure Portalról korábban másolt információt.

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Az utolsó lépés előtt fordítása hozzáadása **odbc** könyvtár függőségei:

![ODBC-bemeneti könyvtár hozzáadása](./media/sql-database-develop-cplusplus-simple/lib.png)

Az alkalmazás indításához, a Linux konzol csatlakozva a **Debug** menüben:

![Linux konzol](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Ha a kapcsolat sikeres volt, meg kell jelennie az aktuális adatbázis nevét, a Linux-konzolban:

![Linux konzolablak kimenete](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Gratulálunk! Sikeresen végrehajtotta az oktatóanyagot, és most már csatlakoztatható az Azure SQL DB-hez a c++ segítségével a Windows és Linux platformokon.

## <a id="GetSolution"></a>A teljes C/C++ oktatóanyag megoldás beszerzése
Ez a cikk a github webhelyen szereplő összes mintát tartalmazó GetStarted-megoldás találhatja meg:

* [Minta ODBC C++ Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), töltse le a Windows C++ ODBC-mintát szeretne csatlakozni az Azure SQL
* [Minta ODBC C++ Linux](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), töltse le a Linux C++ ODBC-mintát szeretne csatlakozni az Azure SQL

## <a name="next-steps"></a>További lépések
* Tekintse át a [SQL Database fejlesztési áttekintése](sql-database-develop-overview.md)
* További információ a [ODBC API-referencia](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>További források
* [Tervezési minták az Azure SQL Database-t használó több-bérlős SaaS-alkalmazásokhoz](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Fedezze fel az [SQL Database összes képességét](https://azure.microsoft.com/services/sql-database/)

