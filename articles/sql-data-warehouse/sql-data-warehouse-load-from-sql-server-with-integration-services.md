---
title: Adatok betöltése az SQL Server be Azure SQL Data Warehouse (SSIS) |} Microsoft Docs
description: Bemutatja, hogyan tárolt adatok mozgatása az adatforrások széles SQL-adatraktár SQL Server Integration Services (SSIS) csomag létrehozásához.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: ''
ms.assetid: e2c252e9-0828-47c2-a808-e3bea46c134a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 03/30/2017
ms.author: cakarst;douglasl;barbkess
ms.openlocfilehash: 6c9cebdd715b6997d0633bc725a3945ba9e0c357
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2018
ms.locfileid: "30928826"
---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>Adatok betöltése az SQL Server be Azure SQL Data Warehouse (SSIS)
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

Adatok betöltése az SQL Serverről az Azure SQL Data Warehouse az SQL Server Integration Services (SSIS) csomag létrehozása. Opcionálisan átalakításának, átalakítási és nagybetűhasználatának az adatokat, ahogy keresztülhalad az SSIS-adatfolyam.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

* Az integrációs szolgáltatások új projekt létrehozása a Visual Studióban.
* Kapcsolódás az adatforrásokhoz, beleértve az SQL Server (mint a forrás-) és az SQL Data Warehouse (célként).
* Tervezze meg az SSIS-csomagot, amely a forrásból származó adatokat tölt a célhelyre történő.
* Futtassa a SSIS az adatok betöltésére.

Ez az oktatóanyag az SQL Server, az adatforrással. SQL Server sikerült fut, a helyszíni vagy egy Azure virtuális gépen.

## <a name="basic-concepts"></a>Alapfogalmak
A csomag nincs SSIS munkaegysége. Kapcsolódó csomagok projektek vannak csoportosítva. Projektek és a Tervező csomagok létrehozása a Visual Studio és az SQL Server Data Tools összetevővel. A tervezési folyamat az egy visual folyamat, amelyben húzza és összetevők dobja el az eszközkészletből a Tervező felületére, csatlakoztassa őket, és állítsa be tulajdonságaikat. Ha befejezte a csomag, opcionálisan telepítése az SQL Server átfogó kezelési, figyelési és biztonsági.

## <a name="options-for-loading-data-with-ssis"></a>Beállítások SSIS az adatok betöltése
SQL Server Integration Services (SSIS), amely számos lehetőséget biztosít csatlakozik, és az adatok betöltése az SQL Data Warehouse rugalmas eszközkészlet.

1. Az ADO NET cél használatával csatlakozhat az SQL Data Warehouse. Ez az oktatóanyag egy ADO NET cél használja, mert a legkevesebb konfigurációs beállításokat.
2. Az OLE DB cél használatával csatlakozhat az SQL Data Warehouse. Ez a beállítás által biztosított ADO NET cél némileg jobb teljesítményt.
3. Az Azure Blob feltöltése feladat segítségével az adatok az Azure Blob Storage tesztelése. A SSIS SQL végrehajtása a feladat segítségével indítsa el az adatok betöltése az SQL Data Warehouse Polybase parancsfájlból. Ez a beállítás az itt felsorolt három közül a legjobb teljesítményt biztosít. Ahhoz, hogy az Azure Blob feltöltése feladat, töltse le a [Microsoft SQL Server 2016 Integration Services funkciócsomag Azure][Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]. A Polybase kapcsolatos további információkért lásd: [PolyBase-útmutatóban][PolyBase Guide].

## <a name="before-you-start"></a>Előkészületek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

1. **Az SQL Server Integration Services (SSIS)**. SSIS az SQL Server, és próbaverziójáról vagy az SQL Server licenccel rendelkező verzió szükséges. Ahhoz, hogy az SQL Server 2016 Preview próbaverzióját, lásd: [SQL Server értékelések][SQL Server Evaluations].
2. **Visual Studio**. Az ingyenes Visual Studio Community Edition megtekinteni [Visual Studio Community][Visual Studio Community].
3. **SQL Server Data Tools for Visual Studio (SSDT)**. Ahhoz, hogy az SQL Server Data Tools for Visual Studio, lásd: [letöltése SQL Server Data Tools (SSDT)][Download SQL Server Data Tools (SSDT)].
4. **Az adatokat**. Ez az oktatóanyag betöltését az SQL Data Warehouse használja a mintaadatok az SQL Server a forrásadatok az AdventureWorks mintaadatbázishoz tárolja. Az AdventureWorks mintaadatbázishoz megtekinteni [AdventureWorks 2014 mintaadatbázisokat][AdventureWorks 2014 Sample Databases].
5. **Egy SQL Data Warehouse-adatbázis és az engedélyek**. Ez az oktatóanyag az SQL Data Warehouse-példányhoz, és adatokat tölt be. Van engedélye a tábla létrehozásához és adatok betöltésére.
6. **Egy tűzfalszabály**. Előtt létre szeretne hozni egy tűzfalszabályt SQL Data warehouse a helyi számítógép IP-címmel feltöltheti az adatait az SQL Data warehouse rendelkezik.

## <a name="step-1-create-a-new-integration-services-project"></a>1. lépés: Új Integration Services-projekt létrehozása
1. Indítsa el a Visual Studio.
2. Az a **fájl** menü **új |} Projekt**.
3. Keresse meg a **telepítve |} Sablonok |} Üzleti intelligencia |} Az integrációs szolgáltatások** projekt típusok.
4. Válassza ki **Integration Services-projekt**. Adjon meg értékeket a **neve** és **hely**, majd válassza ki **OK**.

A Visual Studio megnyílik, és létrehoz egy új Integration Services (SSIS) projektet. Majd megnyílik a Visual Studio a tervező az egyetlen új SSIS-csomag (Package.dtsx) a projektben. A következő területeken képernyő jelenik meg:

* A bal oldali a **eszközkészlet** SSIS-összetevők.
* A középső, a tervezési felülethez, több lappal. Általában használja legalább az **vezérlő Flow** és a **adatfolyam** lapokon.
* A jobb oldalon a **Megoldáskezelőben** és a **tulajdonságok** ablaktábla.
  
    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>2. lépés: Az alapszintű adatfolyam létrehozása
1. Húzzon Data Flow feladat az eszközkészletből a Tervező felület (a a **vezérlő Flow** lap).
   
    ![][02]
2. Kattintson duplán az adatok áramlását feladat váltson át az adatfolyam-lapon.
3. Az eszközkészlet egyéb adatforrások listájából húzza az ADO.NET forrás a Tervező felületére. A kijelölt adatforrás adapterrel, módosítsa a nevét, hogy **SQL Server-adatforrás** a a **tulajdonságok** ablaktáblán.
4. Az eszközkészlet egyéb célok listájából húzza az ADO.NET cél ADO.NET forrás alatt a Tervező felületére. A cél adapterrel kijelölését, módosítsa a nevét, hogy **SQL DW cél** a a **tulajdonságok** ablaktáblán.
   
    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>3. lépés: A forrás-adapter konfigurálásához.
1. A forrás adapter megnyitásához kattintson duplán a **ADO.NET forrás szerkesztő**.
   
    ![][03]
2. A a **Csatlakozáskezelő** lapján a **ADO.NET forrás szerkesztő**, kattintson a **új** megjelenítő gombra a **ADO.NET Csatlakozáskezelő** nyissa meg a listában a **konfigurálása ADO.NET Csatlakozáskezelő** párbeszédpanel és hozza létre, amelyből ez az oktatóanyag adatokat tölt az SQL Server-adatbázis biztonságoskapcsolat-beállításainak.
   
    ![][04]
3. Az a **konfigurálása ADO.NET Csatlakozáskezelő** párbeszédpanel, kattintson a **új** gombra kattintva nyissa meg a **Csatlakozáskezelő** párbeszédpanel és hozza létre az új kapcsolat.
   
    ![][05]
4. Az a **Csatlakozáskezelő** párbeszédpanelen tegye a következőket.
   
   1. A **szolgáltató**, válassza ki az SqlClient adatszolgáltatója.
   2. A **kiszolgálónév**, adja meg az SQL Server nevét.
   3. Az a **jelentkezzen be a kiszolgálóra** szakaszban válassza ki vagy adja meg a hitelesítési adatokat.
   4. Az a **csatlakozás az adatbázishoz** területen válassza ki az AdventureWorks mintaadatbázishoz.
   5. Kattintson a **tesztkapcsolat**.
      
       ![][06]
   6. Kattintson a párbeszédpanel, amely a kapcsolat vizsgálati eredményeket jelent, **OK** való visszatéréshez a **Csatlakozáskezelő** párbeszédpanel megnyitásához.
   7. Az a **Csatlakozáskezelő** párbeszédpanel, kattintson a **OK** való visszatéréshez a **konfigurálása ADO.NET Csatlakozáskezelő** párbeszédpanel.
5. Az a **konfigurálása ADO.NET Csatlakozáskezelő** párbeszédpanel, kattintson a **OK** való visszatéréshez a **ADO.NET adatforrás-szerkesztő**.
6. Az a **ADO.NET forrás szerkesztő**, a a **a tábla vagy a nézet neve** listáról válassza ki a **Sales.SalesOrderDetail** tábla.
   
    ![][07]
7. Kattintson a **előzetes** az első 200 sorokat a forrás táblázatban szereplő adatok a **lekérdezés eredményének villámnézete** párbeszédpanel megnyitásához.
   
    ![][08]
8. Az a **lekérdezés eredményének villámnézete** párbeszédpanel, kattintson a **Bezárás** való visszatéréshez a **ADO.NET forrás szerkesztő**.
9. Az a **ADO.NET forrás szerkesztő**, kattintson a **OK** az adatforrás konfigurálásának befejezéséhez.

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>4. lépés: A forrás-adaptert csatlakoztatni a céladapter
1. Válassza ki a tervezési felülethez a forrás-adapteréből.
2. Válassza ki a kék nyíl, amely kiterjeszti a forrás-adapternek, és húzza azt a cél-szerkesztő, amíg a helyen igazodik.
   
    ![][10]
   
    A tipikus SSIS-csomag segítségével számos más összetevők a forrás- és a cél Between SSIS eszközkészletből átalakításának, átalakítási és nagybetűhasználatának az adatokat, ahogy keresztülhalad az SSIS-adatfolyam. Ez a példa lehető legegyszerűbb megtartásához azt csatlakozik a forrás közvetlenül a cél.

## <a name="step-5-configure-the-destination-adapter"></a>5. lépés: Konfigurálja a céladapter
1. A célként megadott adapter megnyitásához kattintson duplán a **ADO.NET cél szerkesztő**.
   
    ![][11]
2. A a **Csatlakozáskezelő** lapján a **ADO.NET cél szerkesztő**, kattintson a **új** megjelenítő gombra a **Csatlakozáskezelő** nyissa meg a listában a **konfigurálása ADO.NET Csatlakozáskezelő** párbeszédpanel és hozza létre a kapcsolat beállításait az Azure SQL Data Warehouse-adatbázist, amelybe ez az oktatóanyag adatokat tölt.
3. Az a **konfigurálása ADO.NET Csatlakozáskezelő** párbeszédpanel, kattintson a **új** gombra kattintva nyissa meg a **Csatlakozáskezelő** párbeszédpanel és hozza létre az új kapcsolat.
4. Az a **Csatlakozáskezelő** párbeszédpanelen tegye a következőket.
   1. A **szolgáltató**, válassza ki az SqlClient adatszolgáltatója.
   2. A **kiszolgálónév**, adja meg az SQL Data Warehouse nevét.
   3. Az a **jelentkezzen be a kiszolgálóra** szakaszban jelölje be **használja az SQL Server-hitelesítési** , és írja be a hitelesítési adatokat.
   4. Az a **csatlakozás az adatbázishoz** területen válasszon ki egy létező SQL Data Warehouse-adatbázist.
   5. Kattintson a **tesztkapcsolat**.
   6. Kattintson a párbeszédpanel, amely a kapcsolat vizsgálati eredményeket jelent, **OK** való visszatéréshez a **Csatlakozáskezelő** párbeszédpanel megnyitásához.
   7. Az a **Csatlakozáskezelő** párbeszédpanel, kattintson a **OK** való visszatéréshez a **konfigurálása ADO.NET Csatlakozáskezelő** párbeszédpanel.
5. Az a **konfigurálása ADO.NET Csatlakozáskezelő** párbeszédpanel, kattintson a **OK** való visszatéréshez a **ADO.NET cél szerkesztő**.
6. Az a **ADO.NET cél szerkesztő**, kattintson a **új** mellett a **használni kívánt táblát vagy nézetet** nyissa meg a listában a **Create Table** párbeszédpanel cél új tábla létrehozása, amely megfelel a forrástábla oszloplistával együtt.
   
    ![][12a]
7. Az a **Create Table** párbeszédpanelen tegye a következőket.
   
   1. A célként megadott tábla nevének módosítása **értékesítési rendelési adatot**.
   2. Távolítsa el a **rowguid** oszlop. A **uniqueidentifier** adattípusa nem támogatott az SQL Data Warehouse.
   3. Módosítja az adattípust, a **LineTotal** oszlop **pénz**. A **decimális** adattípusa nem támogatott az SQL Data Warehouse. További információt a támogatott adattípusok, lásd: [CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)][CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)].
      
       ![][12b]
   4. Kattintson a **OK** a tábla létrehozásához, és térjen vissza a **ADO.NET cél szerkesztő**.
8. Az a **ADO.NET cél szerkesztő**, jelölje be a **hozzárendelések** lapon, láthatja, hogyan oszlop szerepel a forrás oszlop szerepel a cél van leképezve.
   
    ![][13]
9. Kattintson a **OK** az adatforrás konfigurálásának befejezéséhez.

## <a name="step-6-run-the-package-to-load-the-data"></a>6. lépés: A csomag az adatok betöltéséhez futtassa
Futtassa a gombra kattintva a **Start** gomb az eszköztáron vagy egy a **futtatása** a lehetőségek a **Debug** menü.

A csomag kezdődik el, mivel látni sárga forgó kerekek tevékenység, valamint a feldolgozott eddig sorok számát jelzi.

![][14]

Ha a csomag futása befejeződött, megjelenik az zöld jelölését annak jelzésére, sikeres, valamint az adatok betöltése a forrásból történő sorainak száma.

![][15]

Gratulálunk! Adatok betöltése az Azure SQL Data Warehouse sikeresen használt SQL Server Integration Services.

## <a name="next-steps"></a>További lépések
* További tudnivalók az SSIS-adatfolyam. Kezdje itt: [adatfolyam][Data Flow].
* Útmutató a Hibakeresés és hibaelhárítás a csomagok jobbra a tervezési környezetben. Kezdje itt: [hibaelhárítási eszközök csomag fejlesztési][Troubleshooting Tools for Package Development].
* Megtudhatja, hogyan telepítheti a SSIS-projektek és csomagok Integration Services-kiszolgáló vagy egy másik tárolóhelyre. Kezdje itt: [telepítési a projekt és csomagok][Deployment of Projects and Packages].

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[PolyBase Guide]: https://msdn.microsoft.com/library/mt143171.aspx
[Download SQL Server Data Tools (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[Data Flow]: https://msdn.microsoft.com/library/ms140080.aspx
[Troubleshooting Tools for Package Development]: https://msdn.microsoft.com/library/ms137625.aspx
[Deployment of Projects and Packages]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]: http://go.microsoft.com/fwlink/?LinkID=626967
[SQL Server Evaluations]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Visual Studio Community]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[AdventureWorks 2014 Sample Databases]: https://msftdbprodsamples.codeplex.com/releases/view/125550
