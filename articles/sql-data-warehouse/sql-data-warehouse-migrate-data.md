---
title: Migrálja adatait az SQL Data Warehouse |} A Microsoft Docs
description: Tippek az adatok migrálását az Azure SQL Data Warehouse használható megoldások fejlesztéséhez.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: 77e4500b20be844dfad31db284a9c77ad7ec6bb7
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118122"
---
# <a name="migrate-your-data"></a>Az adatok áttelepítése
Adatok különböző forrásokból származó áthelyezhetők a különböző eszközök, az SQL Data Warehouse szolgáltatásba.  ADF-példány, az SSIS és a bcp az összes használható e cél eléréséhez. Azonban adatokat nő a mennyisége, gondolja bontásához, az áttelepítési folyamat lépésekre. Ez végrehajtható optimalizálható a teljesítmény és rugalmasság zökkenőmentes áttelepítés biztosítása érdekében az egyes lépések lehetőséget.

Ez a cikk először az ADF-példány, az SSIS és a bcp-egyszerű áttelepítési forgatókönyvek ismerteti. Majd keresse meg egy kicsit részletesebben be, hogyan lehet optimalizálni a migrálás.

## <a name="azure-data-factory-adf-copy"></a>Az Azure Data Factory (ADF) másolása
[Az ADF másolási] [ ADF Copy] része [Azure Data Factory][Azure Data Factory]. ADF-példány segítségével exportálhatja az adatokat a helyi tárban, vagy közvetlenül az SQL Data Warehouse-bA az Azure blob storage-ban tárolt távoli egybesimított fájlokba levő egybesimított fájlokba.

Ha indítja el az adatokat egybesimított fájlokba, majd, először vigye át az Azure storage-blobból, mielőtt elindítaná a terhelés, az SQL Data Warehouse-bA. Miután az adatok továbbítása Azure blob storage-ba is használni kívánja [ADF másolási] [ ADF Copy] újra, hogy az adatok leküldése az SQL Data Warehouse-bA.

PolyBase az adatok betöltése a nagy teljesítményű lehetőséget is kínál. Azonban, amely: a két eszköz használata helyett. Ha a lehető legjobb teljesítményt kell, majd a polybase szolgáltatást akkor használja. Ha azt szeretné, hogy egy egyszerű eszközt élmény (és az adatok nem nagy) ADF a válasz.

Hajtsa végre a [ebben az oktatóanyagban](../data-factory/load-azure-sql-data-warehouse.md) az ADF használata az adatok betöltése az adattárházba.

## <a name="integration-services"></a>Integrációs szolgáltatások
Integration Services (SSIS) egy olyan hatékony és rugalmas kinyerése, átalakítás és betöltés (ETL) eszköz, amely támogatja a bonyolult munkafolyamatok, átalakítását és adatok betöltését számos. Az SSIS használata egyszerűen adatok átviteléhez az Azure-bA vagy egy szélesebb körű áttelepítés részeként.

> [!NOTE]
> SSIS exportálhatja az UTF-8 a bájtsorrendjelző a fájl nélkül. Konfigurálhatja a karakter adatok az adatfolyam az UTF-8 65001 kódlap használata a ez előbb használja az származtatott oszlop összetevőt. Az oszlopok konvertálását, miután az adatokat írni az egybesimított fájl céladapter, biztosítva, hogy 65001-es is van kiválasztva a kódlapot, a fájl.
> 
> 

SSIS csatlakozik az SQL Data Warehouse, ahogy azt kíván csatlakozni, egy SQL Server-telepítés. Azonban a kapcsolatokat kell használnia az ADO.NET kapcsolati manager. Meg kell is ügyeljen arra, hogy konfigurálja a "használat tömeges beszúrás Ha elérhető" beállítás átviteli sebesség maximalizálása érdekében. Tekintse meg a [ADO.NET-céladapter] [ ADO.NET destination adapter] cikk további információt Ez a tulajdonság

> [!NOTE]
> Csatlakozás az Azure SQL Data Warehouse OLEDB használatával nem támogatott.
> 
> 

Ezenkívül mindig fennáll a lehetősége, hogy a csomag szabályozás vagy hálózati problémák miatt meghiúsulhat. Tervezési csomagokat, így azok tudja folytatni a hibánál, nélkül megismétlése működik, amely a hiba előtt befejeződött.

További részletekért tekintse meg a [SSIS dokumentációjának][SSIS documentation].

## <a name="bcp"></a>bcp
BCP parancssori segédprogram az egybesimított fájl adatok importálása és exportálása tervezett. Bizonyos átalakítási adatok exportálásakor akkor kerül sor. Végrehajtásához egyszerű átalakítások egy lekérdezés segítségével válassza ki, és átalakíthatja az adatokat. Exportálása után az egybesimított fájlok majd betölthetők közvetlenül a cél az SQL Data Warehouse-adatbázist.

> [!NOTE]
> Ez a legtöbbször célszerű, hogy magába foglalja az adatok exportálásakor egy nézetben a forrásrendszerben használni átalakítások. Ez biztosítja, hogy a logikai maradnak, és a folyamat nem ismételhető.
> 
> 

Bcp előnyei a következők:

* Egyszerű. BCP parancsai hozhatnak létre és futtathatnak egyszerű
* Újból másik betöltési folyamat. Egyszer exportált lehet a terhelés végre tetszőleges számú alkalommal

BCP korlátozások a következők:

* egybesimított fájlok táblázatos BCP működik. A fájlok, például xml vagy JSON nem működik
* Adat-átalakítási képességek csak az export előkészítés korlátozódik, és egyszerűen jellegű
* BCP nem lett az interneten keresztül az adatok betöltése során robusztus kell igazítani. Minden olyan hálózati instabil egy betöltési hiba okozhatja.
* BCP támaszkodik a betöltés előtt a céladatbázis megtalálható-e a séma

További információkért lásd: [adatok betöltése az SQL Data Warehouse-bA bcp segítségével][Use bcp to load data into SQL Data Warehouse].

## <a name="optimizing-data-migration"></a>Adatok migrálása optimalizálása
Egy SQLDW adatmigrálás folyamata hatékonyan bonthatók három különálló lépésből áll:

1. Az adatforrás-adatok exportálása
2. Adatok átvitele az Azure-bA
3. A céladatbázis SQLDW betöltése

Minden egyes lépés külön-külön optimalizálható, amely maximalizálja a teljesítményt minden lépésnél robusztus, újbóli másik és rugalmas migrálási folyamat létrehozásához.

## <a name="optimizing-data-load"></a>Optimalizálás az adatok betöltése
Ezek között fordított sorrendben. egy pillanatra; Hibaoldal az adatok betöltésének leggyorsabb módja a polybase van. Betöltés a PolyBase a folyamat optimalizálása helyez Előfeltételek az előző lépések ennek megértéséhez a legjobb előzetes költségek. Ezek a következők:

1. Adatfájlok kódolás
2. Adatfájlok formátuma
3. Adatfájlok helye

### <a name="encoding"></a>Encoding
PolyBase az adatok fájlokat vagy UTF-8 a UTF-16FE van szükség. 



### <a name="format-of-data-files"></a>Adatfájlok formátuma
A PolyBase előírásoknak, egy rögzített sor lezárójele \n vagy új sor. Ez a szabvány meg kell felelnie az adatfájlokat. Nincsenek karakterlánc- vagy lezáró jelének egyeznie korlátozásait.

Minden oszlop meghatározásához a fájlban a PolyBase a külső tábla részeként kap. Győződjön meg arról, hogy minden exportált oszlopokra szükség, és hogy a típusok megfelelnek-e a szükséges szabványoknak.

Tekintse meg ismét a [séma áttelepítése] támogatott adattípusok részletes ismertető cikket.

### <a name="location-of-data-files"></a>Adatfájlok helye
Az SQL Data Warehouse a polybase kizárólag betölteni az adatokat az Azure Blob Storage-ból. Ennek következtében az adatok kell először átadott blob storage-bA.

## <a name="optimizing-data-transfer"></a>Az adatátvitel optimalizálása
Adatok áttelepítése a leglassabb részeit egyike az Azure-bA az adatok átvitelét. Nem csupán probléma lehet a sávszélesség, de is hálózati megbízhatóság komolyan gátolja folyamatban van. Adatok áttelepítése az Azure-ban alapértelmezés szerint a az interneten keresztül, így viszonylag várhatóan magas átviteli fellépett hibák esélyét. Ezek a hibák azonban adatok küldésének egészben vagy részben újra lehet szükség.

Szerencsére a sebességet és rugalmasságát, ez a folyamat javítása érdekében több lehetősége van:

### <a name="expressrouteexpressroute"></a>[ExpressRoute][ExpressRoute]
Érdemes fontolóra [ExpressRoute] [ ExpressRoute] az átvitel felgyorsítása érdekében. [Az ExpressRoute] [ ExpressRoute] nyújt egy létrehozott privát kapcsolat az Azure-ba így a kapcsolat nem a nyilvános interneten keresztül halad. Ez a lépése semmiképpen sem kötelező. Azonban azt fogja javítják a átviteli adatok leküldése az Azure egy helyszíni vagy közös elhelyezési létesítményben.

A használatának előnyeit [ExpressRoute] [ ExpressRoute] vannak:

1. Nagyobb megbízhatóság
2. Gyorsabb hálózati sebesség
3. Kisebb hálózati késés
4. magasabb szintű hálózati biztonság

[Az ExpressRoute] [ ExpressRoute] előnyös számos forgatókönyv esetében; a nem csak az áttelepítés.

Felkeltettük az érdeklődését? Díjszabásért és további információért látogasson el a [az ExpressRoute dokumentációja][ExpressRoute documentation].

### <a name="azure-import-and-export-service"></a>Az Azure importálási és exportálási szolgáltatás
Az Azure Import szolgáltatás exportálása az és adatok átvitel tervezett nagy méretű (GB -os ++) hatalmas (TB ++) adatok továbbítása az Azure-bA. Az adatok írása a lemezek és a szállítási őket egy Azure-adatközpontban való magában foglalja. A lemez tartalma lesz majd tölthetők be Azure Storage-Blobokból az Ön nevében.

Az importálási exportálási folyamat áttekintése a következőképpen történik:

1. Egy Azure Blob Storage tárolóba, az adatok fogadására konfigurálása
2. Helyi tárhely az adatok exportálása
3. 3,5 hüvelykes II. és III SATA merevlemez-meghajtók használata az [Azure Import/Export eszköz] az adatok másolása
4. Az Azure importálás és exportálás szolgáltatást, amely az [Azure Import/Export eszköz] által előállított Adatbázisnapló-fájlok importálási feladat létrehozása
5. A kijelölt Azure-adatközpontban szállítsa
6. Az adatok átkerülnek az Azure Blob Storage-tárolóba
7. Az adatok betöltése a PolyBase SQLDW az

### <a name="azcopyazcopy-utility"></a>[Az AZCopy][AZCopy] segédprogram
A [AZCopy][AZCopy] segédprogram egy remek eszköz az Azure Storage-Blobok már átvitt adatok beolvasása. A nagyon nagy (GB -os ++) adatforgalom kis (MB -os ++) tervezték. [AzCopy] is úgy lett kialakítva, adja meg a helyes rugalmas átviteli sebesség Azure, így történő adatátvitel során egy remek választás az adatok átvitel lépés. Egyszer átvitt betöltheti az adatokat, a PolyBase az SQL Data Warehouse-bA. Az AZCopy is beépítse az SSIS-csomagok használatával egy "Végrehajtása folyamat" feladatot.

Az AZCopy használata, először töltse le és telepítse azt. Van egy [változatát] [ production version] és a egy [előzetes verzió] [ preview version] érhető el.

Töltsön fel egy fájlt a fájlrendszer szüksége lesz az alábbihoz hasonló parancsot:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Lehet magas szintű folyamata összegzése:

1. Fogadni az adatokat egy Azure storage blob-tároló konfigurálása
2. Helyi tárhely az adatok exportálása
3. Az AZCopy az adatok az Azure Blob Storage-tárolóba
4. Az adatok betöltése az SQL Data Warehouse a PolyBase használatával

Teljes dokumentáció elérhető: [Az AZCopy][AZCopy].

## <a name="optimizing-data-export"></a>Adatok optimalizálása exportálása
Arról, hogy az Exportálás megfelel a követelményeknek, a PolyBase által biztosítása mellett azt is kérhet az Exportálás további a folyamatot az adatok optimalizálása érdekében.



### <a name="data-compression"></a>Adattömörítés
A PolyBase olvashatja a gzip formátumban tömörített adatokként. Ha tömörítése a gzip-fájlokat az adatok tudja majd minimalizálhatja a hálózaton keresztüli adatküldés adatok mennyisége.

### <a name="multiple-files"></a>Több fájl
Szétválasztja a nagyméretű táblák több fájlt, nem csak az exportálási sebességének növelése, is átviteli re-startability és az általános kezelhetőséggel egyszer az adatok az Azure blob storage segítségével. A PolyBase számos nagyszerű funkcióját egyik, amellyel fog egy mappában található összes fájl olvasása és egy táblázatként kezeli. Éppen ezért célszerű elkülöníteni a fájlok minden táblához a saját mappájába.

A PolyBase támogatja a "rekurzív Mappa bejárása" néven ismert szolgáltatás is. Ez a funkció használatával tovább javíthatja a szervezet az exportált adatok az adatkezelés javítása érdekében.

A polybase lehetővé teszi az adatok betöltése kapcsolatos további információkért lásd: [bA a PolyBase használatával az adatok betöltése az SQL Data Warehouse][Use PolyBase to load data into SQL Data Warehouse].

## <a name="next-steps"></a>További lépések
Az áttelepítéssel kapcsolatos további információkért lásd: [megoldás áttelepítése az SQL Data Warehouse][Migrate your solution to SQL Data Warehouse].
További fejlesztési tippek: [fejlesztői áttekintés][development overview].

<!--Image references-->

<!--Article references-->
[AzCopy]: ../storage/common/storage-use-azcopy.md
[ADF Copy]: ../data-factory/load-azure-sql-data-warehouse.md 
[ADF Copy examples]: ../data-factory/quickstart-create-data-factory-dot-net.md
[development overview]: sql-data-warehouse-overview-develop.md
[séma áttelepítése]: sql-data-warehouse-migrate-schema.md
[Migrate your solution to SQL Data Warehouse]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Use bcp to load data into SQL Data Warehouse]: /sql/tools/bcp-utility
[Use PolyBase to load data into SQL Data Warehouse]: load-data-wideworldimportersdw.md


<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory]: http://azure.microsoft.com/services/data-factory/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[ExpressRoute documentation]: http://azure.microsoft.com/documentation/services/expressroute/

[production version]: http://aka.ms/downloadazcopy/
[preview version]: http://aka.ms/downloadazcopypr/
[ADO.NET destination adapter]: https://msdn.microsoft.com/library/bb934041.aspx
[SSIS documentation]: https://msdn.microsoft.com/library/ms141026.aspx
