---
title: "Az SQL Data Warehouse-adatok áttelepítése |} Microsoft Docs"
description: "Tippek az adatok áttelepítése az Azure SQL Data Warehouse adattárházzal történő, megoldások."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: d78f954a-f54c-4aa4-9040-919bc6414887
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 06/29/2017
ms.author: joeyong;barbkess
ms.openlocfilehash: 0d156bc2eecf8220bd5ff4eb811d91482f216837
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-data"></a>Adatok áttelepítése
Adatok az SQL Data Warehouse egy különböző eszközeivel mozgathatók különböző forrásokból származó.  ADF másolás, SSIS és bcp összes használható e cél eléréséhez. Azonban adatok növeli a összegként gondolja bontásához, az áttelepítési folyamat azokat a lépéseket. Ez számára biztosítja azt a lehetőséget, hogy az egyes lépések a teljesítmény- és a zökkenőmentes adatok áttelepítéshez hibatűrőnek optimalizálása.

A cikk először ADF másolása, SSIS és bcp egyszerű áttelepítési forgatókönyveit ismerteti. Ezután megjelenését kissé mélyebben hogyan az áttelepítés is lehet optimalizálni.

## <a name="azure-data-factory-adf-copy"></a>Az Azure Data Factory (ADF) másolása
[ADF másolási] [ ADF Copy] része [Azure Data Factory][Azure Data Factory]. ADF másolat segítségével exportálhatja az adatokat a helyi tároló távoli egybesimított fájlokba, az Azure blob Storage tárolóban, vagy közvetlenül az SQL Data Warehouse levő egybesimított fájlokba.

Ha az adatok indul egybesimított fájlokba, akkor először átvitele az Azure storage-blobba egy terheléselosztási kezdeményezése előtt azt az SQL Data Warehouse. Miután az adatok elküldése az Azure blob storage is használatát választja [ADF másolási] [ ADF Copy] újra történő az SQL Data Warehouse.

A PolyBase is biztosít egy nagy teljesítményű beállítása az adatok betöltése. Azonban, hogy: a két eszköz helyett egy. Ha a legjobb teljesítmény érdekében szükséges, akkor a polybase szolgáltatást akkor használja. Ha azt szeretné, hogy egy eszköz-élmény (és az adatok nem jelentős) ADF a válasz.


> 
> 

A következő cikk néhány nagy keresztül központi [ADF minták][ADF samples].

## <a name="integration-services"></a>Integrációs szolgáltatások
Integration Services (SSIS) egy hatékony és rugalmas bontsa ki az átalakítási és betöltési (ETL) eszközt, amely támogatja a bonyolult munkafolyamatok, adatok átalakítása és több adatok betöltését lehetőség. SSIS segítségével egyszerűen adatátvitelt az Azure-bA vagy egy szélesebb körű áttelepítés részeként.

> [!NOTE]
> SSIS UTF-8 exportálhatja a byte rendelés be van jelölve, a fájl nélkül. Konfigurálhatja ezt előbb segítségével a származtatott oszlop összetevő a karakteres adatokat az adatfolyam használata az UTF-8 65001 kódlapra. Az oszlopok konvertálás után az adatok írása a egybesimított fájl céladapter, győződjön meg arról, hogy 65001-es is van kiválasztva a kódlapot, a fájl.
> 
> 

SSIS csatlakozik az SQL Data Warehouse, ahogy azt egy SQL Server-példányt kíván csatlakozni. Azonban a kapcsolatokat. használja az ADO.NET Csatlakozáskezelő kell. Meg kell is igénybe vehet ügyeljen arra, hogy konfigurálja a "használata tömeges beszúrás eredményről" beállítás maximális átviteli sebesség. Tekintse meg a [ADO.NET céladapter] [ ADO.NET destination adapter] cikk további információt a tulajdonság

> [!NOTE]
> Csatlakozás az Azure SQL Data Warehouse szolgáltatáshoz OLEDB nem támogatott.
> 
> 

Ezenkívül mindig fennáll annak lehetősége, hogy egy csomag sávszélesség-szabályozás vagy hálózati problémák miatt lehetséges, hogy nem. Tervezési csomagok, így azok folytathatók helyén hiba nélkül megismétlése működik, amelyek a meghibásodás előtt végzi el.

További információkat a [SSIS-dokumentáció][SSIS documentation].

## <a name="bcp"></a>bcp
BCP parancssori segédprogram, amely a strukturálatlan fájl adatok importálásának és exportálásának szolgál. Néhány átalakítási adatok exportálásakor akkor kerül sor. Végrehajtásához egyszerű átalakítások lekérdezéssel jelölje ki, majd átalakíthatja az adatokat. Exportálása után a egybesimított fájlokba majd betölthető közvetlenül a cél az SQL Data Warehouse-adatbázis.

> [!NOTE]
> Általában célszerű a forrásrendszer nézetben adatok exportálása során használt átalakítások foglalják magukban. Ez biztosítja, hogy megőrzi a programot, és a folyamat nem ismételhető.
> 
> 

Bcp előnyei a következők:

* Egyszerű. BCP parancsok is egyszerű felépítéséhez és végrehajtása
* Újra elindítható betöltési folyamatot. Egyszer exportált lehet a terhelés tetszőleges számú alkalommal végre

A bcp korlátozásai a következők:

* BCP egyszerű fájlok táblázatos működik. Nem működik az XML- vagy JSON például fájlok
* Adatok átalakítása képességek az Exportálás szakasza csak korlátozott, és könnyen jellegű
* BCP nem volt módosítani kell robusztus, amikor az adatok betöltése az interneten keresztül. Minden hálózati instabilitás okozhat a betöltési hiba történt.
* a séma megtalálhatók-e a céladatbázis, a betöltés előtt támaszkodik BCP

További információkért lásd: [adatok betöltése az SQL Data warehouse-bA bcp segítségével][Use bcp to load data into SQL Data Warehouse].

## <a name="optimizing-data-migration"></a>Adatok áttelepítése optimalizálása
Egy SQLDW adatok áttelepítési folyamat hatékonyan sorolhatók három különálló lépésből áll:

1. A forrásadatok exportálása
2. Adatok továbbítása az Azure-bA
3. A céladatbázis SQLDW betöltése

Minden lépés is lehet optimalizálni külön-külön, egy robusztus, újra elindítható és rugalmas áttelepítési folyamat, amely a lehető legnagyobbra növeli a teljesítményt lépésről lépésre létrehozásához.

## <a name="optimizing-data-load"></a>Optimalizálás adatok betöltése
A következő fordított sorrendben egy pillanatra; keresése a leggyorsabban adatok betöltése a polybase van. PolyBase-betöltési folyamat optimalizálása helyezi Előfeltételek az előző lépéseket, hogy ajánlott ennek megértéséhez társaságuk. Ezek a következők:

1. Fájlok kódolása
2. Az adatfájlok formátuma
3. Adatforrás adatfájljainak helyét

### <a name="encoding"></a>Encoding
PolyBase az adatfájlok UTF-8 vagy UTF-16FE igényel. 



### <a name="format-of-data-files"></a>Az adatfájlok formátuma
A PolyBase csak egy rögzített méretű sor lezárójele \n vagy új sor. Az adatfájlok meg kell felelnie az szabványnak. Nincs karakterlánc- vagy lezárói korlátozásait.

Minden egyes oszlophoz meghatározását a fájlban a PolyBase külső tábla részeként lesz. Győződjön meg arról, hogy minden exportált oszlopokra szükség, és hogy a típusok megfelelnek-e a szükséges szabványokat.

Tekintse meg ismét a [telepítse át a séma] cikk a támogatott adattípusokat részletek.

### <a name="location-of-data-files"></a>Adatforrás adatfájljainak helyét
Az SQL Data Warehouse PolyBase használ az adatok betöltése az Azure Blob Storage kizárólag. Ezért az adatok kell először átadott a blob-tárolóba.

## <a name="optimizing-data-transfer"></a>Optimalizálás adatátvitel
Adatok áttelepítése a leglassabb részeit egyik Azure az adatok átvitelét. Nem csak lehet hálózati sávszélesség problémát, de a hálózat megbízhatóságát is komolyan gátolja folyamatban van. Adatok áttelepítése az Azure-bA alapértelmezés szerint az interneten keresztül, az átviteli fellépett hibák esélyét ésszerűen valószínűleg. Ezek a hibák azonban megkövetelhetik küldendő adatok újbóli egészben vagy részben.

Szerencsére a sebesség és a folyamat rugalmasságot javításához több lehetőség közül választhat:

### <a name="expressrouteexpressroute"></a>[ExpressRoute][ExpressRoute]
Érdemes lehet érdemes [ExpressRoute] [ ExpressRoute] átvitelét felgyorsítása érdekében. [ExpressRoute] [ ExpressRoute] nyújt egy titkos kapcsolat az Azure-ba így a kapcsolat nem halad át a nyilvános internethez. Ez egy kötelező lépés semmiképpen sem. Azonban azt fogja javítják a teljesítményt kérdez le adatokat az Azure-bA egy helyszíni vagy a közös elhelyezés létesítmény.

A használatának előnyeit [ExpressRoute] [ ExpressRoute] vannak:

1. Nagyobb megbízhatóságot
2. Gyorsabb hálózati sebesség
3. Kisebb hálózati késés
4. magasabb szintű hálózati biztonság

[ExpressRoute] [ ExpressRoute] forgatókönyvek; számos hasznos nem csak az áttelepítés.

Felkeltettük az érdeklődését? És árképzési adjon további információért látogasson el a [ExpressRoute dokumentációja][ExpressRoute documentation].

### <a name="azure-import-and-export-service"></a>Az Azure Import és Export szolgáltatásról
Az Azure importálás és exportálása szolgáltatás rendkívül készült nagy (GB ++) történő tömeges (TB ++) adatok továbbítása az Azure data átviteli folyamat. Írás a a lemezek és a szállítási őket egy Azure adatközpontba magában foglalja. A lemez tartalma majd betölti Azure Storage blobs szolgáltatásában az Ön nevében.

Az importálás exportálási folyamat áttekintése a következőképpen történik:

1. Egy Azure Blob Storage tárolót a adatok fogadására konfigurálása
2. Az adatok exportálása helyi tárterülethez
3. 3,5 hüvelykes SATA II/III merevlemez-meghajtók [Azure Import/Export eszköz] segítségével az adatok másolása
4. Az Azure importálás és exportálása, az adatbázisnapló-fájlok az [Azure Import/Export eszköz] által visszaadott biztosító szolgáltatás segítségével importálási feladat létrehozása
5. Küldje el a lemezt a kijelölt Azure-adatközpont
6. Az adatátvitel történik az Azure Blob Storage tárolóban
7. Az adatok betöltése a PolyBase használatával SQLDW

### <a name="azcopyazcopy-utility"></a>[AZCopy][AZCopy] segédprogram
A [AZCopy][AZCopy] segédprogram egy remek eszköz az első Azure Storage Blobs átvihetők az adatokat. A kis (MB ++) túl nagy (GB ++) adatátvitelek számára tervezték. [AZCopy] jó rugalmas átviteli Azure, ezért történő adatátvitel során az adatok átvitel lépés kiváló választás arra is használható. Egyszer átvitt betöltheti az adatokat az SQL Data Warehouse PolyBase használatával. AZCopy is beépítheti a SSIS-csomagok használata egy "Hajtható végre folyamat" feladatot.

AZCopy használatához először töltse le és telepíti. Van egy [éles verziójával] [ production version] és egy [előzetes verzióval] [ preview version] érhető el.

A fájlrendszerből-fájl feltöltése szüksége lesz az alábbihoz hasonló parancsot:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Egy magas szintű folyamat összegzése lehet:

1. Egy Azure blob tároló a adatok fogadására konfigurálása
2. Az adatok exportálása helyi tárterülethez
3. AZCopy az adatokat az Azure Blob Storage tárolóban
4. Az adatok betöltése az SQL Data Warehouse PolyBase használatával

Teljes rendelkezésre álló dokumentáció: [AZCopy][AZCopy].

## <a name="optimizing-data-export"></a>Optimalizálás adatok exportálása
Győződjön meg arról, hogy az Exportálás megfelel-e a követelményeknek, a PolyBase által leírva mellett akkor is kérhet az a folyamat további javítása érdekében az adatok optimalizálása érdekében.



### <a name="data-compression"></a>Adattömörítés
A PolyBase gzip tömörített adatok olvashatók. Ha az adatokat, hogy a gzip fájl tömörítése majd fog minimálisra csökkenthető a hálózaton keresztül leküldött alatt álló adatok mennyisége.

### <a name="multiple-files"></a>Több fájl
Több fájlokba nagy táblák összeállításának nem csak exportálási sebesség növelése érdekében, az átviteli re-startability, és egyszer az adatokat az Azure blob storage általános kezelhetőségével emellett segít. A PolyBase sok töltött jellemzői egyike, hogy emellett egy mappában található összes fájl olvasása és kezelje azt egy olyan táblát. Éppen ezért érdemes elkülöníteni a fájlok minden táblához saját mappába.

PolyBase az úgynevezett "rekurzív mappa átjárás" is támogatja. Ez a funkció használatával tovább javíthatja a szervezet az exportált adatok az adatok felügyelet továbbfejlesztése érdekében.

A polybase-zel adatok betöltése kapcsolatos további információkért lásd: [használja a PolyBase az adatok betöltése az SQL Data Warehouse][Use PolyBase to load data into SQL Data Warehouse].

## <a name="next-steps"></a>Következő lépések
Áttelepítési kapcsolatban bővebben lásd: [megoldás Migrálása az SQL Data Warehouse][Migrate your solution to SQL Data Warehouse].
További fejlesztési tippek, lásd: [fejlesztői áttekintés][development overview].

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/common/storage-use-azcopy.md
[ADF Copy]: ../data-factory/v1/data-factory-data-movement-activities.md 
[ADF samples]: ../data-factory/v1/data-factory-samples.md
[ADF Copy examples]: ../data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio.md
[development overview]: sql-data-warehouse-overview-develop.md
[Migrate your solution to SQL Data Warehouse]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Use bcp to load data into SQL Data Warehouse]: sql-data-warehouse-load-with-bcp.md
[Use PolyBase to load data into SQL Data Warehouse]: sql-data-warehouse-get-started-load-with-polybase.md


<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory]: http://azure.microsoft.com/services/data-factory/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[ExpressRoute documentation]: http://azure.microsoft.com/documentation/services/expressroute/

[production version]: http://aka.ms/downloadazcopy/
[preview version]: http://aka.ms/downloadazcopypr/
[ADO.NET destination adapter]: https://msdn.microsoft.com/library/bb934041.aspx
[SSIS documentation]: https://msdn.microsoft.com/library/ms141026.aspx
