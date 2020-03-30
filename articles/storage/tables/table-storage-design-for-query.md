---
title: Az Azure Table tároló tervezése lekérdezésekhez | Microsoft dokumentumok
description: Az Azure Table storage-ban lekérdezésekhez tervezzen táblákat.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 41a588ddc0c1be8014a84d8fe181013d8566f68d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457641"
---
# <a name="design-for-querying"></a>Tervezés lekérdezéshez
Table service megoldások lehetnek olvasási intenzív, írási intenzív, vagy a kettő keveréke. Ez a cikk azokra a dolgokra összpontosít, amelyeket szem előtt kell tartania, amikor a Table szolgáltatás tervezésekor hatékonyan támogatja az olvasási műveleteket. Általában egy olyan terv, amely hatékonyan támogatja az olvasási műveleteket, szintén hatékony az írási műveletekhez. Az írási műveletek támogatása során azonban további szempontokat kell szem előtt tartani, amelyeket a [Tervezés az adatok módosításához](table-storage-design-for-modification.md)című cikkben tárgyal.

Egy jó kiindulási pont a Table szolgáltatás megoldásának az adatok hatékony olvasása érdekében történő tervezéséhez, hogy feltegye a kérdést: "Milyen lekérdezéseket kell végrehajtania az alkalmazásnak a table szolgáltatásból szükséges adatok lekéréséhez?"  

> [!NOTE]
> A Table szolgáltatással fontos, hogy a design elöl legyen, mert később nehéz és költséges megváltoztatni. Például egy relációs adatbázisban gyakran lehetséges a teljesítményproblémák egyszerű kezelése egy meglévő adatbázishoz való indexek hozzáadásával: ez nem lehetséges a Table szolgáltatással.  
> 
> 

Ez a szakasz azokra a kulcsfontosságú kérdésekre összpontosít, amelyekkel a táblák lekérdezésre való tervezésekor foglalkoznia kell. Az ebben a részben tárgyalt témák a következők:

* [A PartitionKey és a RowKey által választott lehetőségek hatása a lekérdezésteljesítményre](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [A megfelelő PartitionKey kiválasztása](#choosing-an-appropriate-partitionkey)
* [Lekérdezések optimalizálása a Table szolgáltatáshoz](#optimizing-queries-for-the-table-service)
* [Adatok rendezése a Table szolgáltatásban](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>A PartitionKey és a RowKey által választott lehetőségek hatása a lekérdezésteljesítményre
A következő példák feltételezik, hogy a table service az alkalmazotti entitásokat a következő struktúrával tárolja (a legtöbb példa kihagyja az **Időbélyeg** tulajdonságot az egyértelműség kedvéért):  

| *Oszlop neve* | *Adattípus* |
| --- | --- |
| **Partíciókulcs** (részleg neve) |Sztring |
| **RowKey** (alkalmazottazonosító) |Sztring |
| **Utónév** |Sztring |
| **Vezetéknév** |Sztring |
| **Kor** |Egész szám |
| **E-mail cím** |Sztring |

Az [Azure Table storage áttekintése](table-storage-overview.md) az Azure Table szolgáltatás néhány kulcsfontosságú szolgáltatását ismerteti, amelyek közvetlen hatással vannak a lekérdezés tervezésére. Ezek a table service-lekérdezések tervezésére vonatkozó alábbi általános irányelvek nek adják le. Vegye figyelembe, hogy az alábbi példákban használt szűrőszintaxis a Table service REST API-ból származik, további információt a [Lekérdezésentitások](https://docs.microsoft.com/rest/api/storageservices/Query-Entities)című témakörben talál.  

* A ***Pontlekérdezés*** a leghatékonyabb keresettség, amelyet nagy mennyiségű vagy legkisebb késést igénylő nagy mennyiségű kereséshez kell használni. Egy ilyen lekérdezés az indexek segítségével keresse meg az egyes entitások nagyon hatékonyan megadásával a **PartitionKey** és **a RowKey** értékeket. Például: $filter=(PartitionKey eq 'Sales') és (RowKey eq '2')  
* A második legjobb egy ***tartománylekérdezés,*** amely a **PartitionKey-t** használja, és egy **sorkulcs-értéktartományt** szűr egynél több entitás visszaadására. A **PartitionKey** érték egy adott partíciót azonosít, és a **RowKey** értékek azonosítják az adott partíció entitásainak egy részét. Például: $filter=PartitionKey eq 'Sales' és RowKey ge 'S' és RowKey lt 'T'  
* A harmadik legjobb egy ***partíciós vizsgálat,*** amely a **PartitionKey-t** használja, és egy másik nem kulcstulajdonságszűrőit használja, és amely egynél több entitást adhat vissza. A **PartitionKey** érték egy adott partíciót azonosít, és a tulajdonságértékek az adott partíció entitásainak egy részhalmazához választanak ki. Például: $filter=PartitionKey eq 'Sales' és LastName eq 'Smith'  
* A ***table scan*** nem tartalmazza a **PartitionKey,** és nagyon nem hatékony, mert megkeresi az összes partíciót, amelyek alkotják a táblát viszont a megfelelő entitások. Táblázat-vizsgálat hajt végre, függetlenül attól, hogy a szűrő a **RowKey-t**használja-e. Például: $filter=Vezetéknév eq 'Jones'  
* A több entitást visszaadó lekérdezések **partitionkey** és **rowkey** sorrendben rendezve adják vissza őket. Az ügyfél entitásai alkalmazásának elkerülése érdekében válasszon egy **RowKey-t,** amely meghatározza a leggyakoribb rendezési sorrendet.  

Ne feledje, hogy a **RowKey** értékeken alapuló szűrő megadásához "**vagy**" használatával partícióvizsgálat történik, és nem lesz tartománylekérdezés. Ezért kerülje az olyan lekérdezéseket, amelyek olyan szűrőket használnak, mint a következők: $filter=PartitionKey eq 'Sales' és (RowKey eq '121' vagy RowKey eq '322')  

Példák olyan ügyféloldali kódra, amely a storage-ügyfélkódtár hatékony lekérdezések végrehajtására használja, lásd:  

* [Pontlekérdezés végrehajtása a Tárolóügyféltár használatával](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Több entitás beolvasása a LINQ használatával](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Kiszolgálóoldali vetítés](table-storage-design-patterns.md#server-side-projection)  

Példák olyan ügyféloldali kódra, amely több, ugyanabban a táblában tárolt entitástípust képes kezelni, lásd:  

* [Heterogén entitástípusokkal végzett munka](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>A megfelelő PartitionKey kiválasztása
A **választott PartitionKey** egyensúlyt kell teremtenie az EGT-k használatának engedélyezéséhez (a konzisztencia biztosítása érdekében) az entitások több partíció között történő elosztásának követelményével szemben (méretezhető megoldás biztosítása érdekében).  

Egy szélsőséges, az entitások egyetlen partíción tárolhatja, de ez korlátozhatja a megoldás méretezhetőségét, és megakadályozhatja, hogy a table service-t, hogy képes-hoz terheléselosztási kérelmek. A másik véglet, partíciónként egy entitástárolhatja, amely nagymértékben méretezhető, és amely lehetővé teszi a table service terheléselosztási kérelmek, de amely megakadályozza az entitáscsoport-tranzakciók használatát.  

Az ideális **PartitionKey** az egyik, amely lehetővé teszi a hatékony lekérdezések használatát, és amely elegendő partíciót biztosít a megoldás méretezhető. Általában azt fogja találni, hogy az entitások lesz egy megfelelő tulajdonság, amely elosztja az entitások között elegendő partíciókat.

> [!NOTE]
> Egy olyan rendszerben például, amely a felhasználókvagy alkalmazottak adatait tárolja, a UserID jó partitionkey lehet. Előfordulhat, hogy több entitás, amely egy adott UserID partíciókulcsként. Minden entitás, amely tárolja a felhasználó adatait egyetlen partícióra van csoportosítva, és így ezek az entitások entitáscsoport-tranzakciókon keresztül érhetők el, miközben továbbra is nagymértékben méretezhető.
> 
> 

A **PartitionKey** által választott további szempontok további szempontokat is figyelembe vesz, amelyek az entitások beszúrásával, frissítésével és törlésével kapcsolatosak. További információt a [Táblák tervezése adatmódosításhoz](table-storage-design-for-modification.md)című témakörben talál.  

## <a name="optimizing-queries-for-the-table-service"></a>Lekérdezések optimalizálása a Table szolgáltatáshoz
A Table szolgáltatás automatikusan indexeli az entitásokat a **PartitionKey** és **a RowKey** értékek használatával egyetlen fürtözött indexben, ezért a pontlekérdezések a leghatékonyabbak. A **PartitionKey** és a **RowKey**fürtözött indexén kívül azonban nincs más index.

Számos tervnek meg kell felelnie a követelményeknek ahhoz, hogy több feltétel alapján engedélyezhesse az entitások megkeresését. Például az alkalmazotti entitások megkeresése e-mail, alkalmazottazonosító vagy vezetéknév alapján. A [táblatervezési mintákban](table-storage-design-patterns.md) leírt minták az ilyen típusú követelményekre vonatkoznak, és leírják a munka módját, hogy a Table szolgáltatás nem biztosít másodlagos indexeket:  

* [Partíción belüli másodlagos indexminta](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – Tárolja az egyes entitások több példányát különböző **RowKey-értékek** használatával (ugyanabban a partícióban) a gyors és hatékony keresések és a különböző **RowKey-értékek** használatával történő rendezési sorrendek engedélyezéséhez.  
* [Partíciók közötti másodlagos indexminta](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – Tárolja az egyes entitások több példányát különböző **RowKey-értékek** használatával külön partíciókon vagy külön táblákban, hogy gyors és hatékony keresések és alternatív rendezési sorrendek különböző **RowKey-értékek** használatával.  
* [Index entitások mintája](table-storage-design-patterns.md#index-entities-pattern) – Index entitások karbantartása az entitások listájának visszaadását tartalmazó hatékony keresések engedélyezéséhez.  

## <a name="sorting-data-in-the-table-service"></a>Adatok rendezése a Table szolgáltatásban
A Table szolgáltatás a **PartitionKey,** majd a **RowKey**alapján növekvő sorrendbe rendezett entitásokat adja vissza. Ezek a kulcsok karakterlánc-értékek, és annak érdekében, hogy a numerikus értékek megfelelően rendeződjenek, konvertálja őket rögzített hosszúságúra, és nullákkal kell kipárnáznia őket. Ha például a **RowKey-ként** használt alkalmazotti azonosító érték egész szám, akkor a **123-as** alkalmazottat **00000123-ra**kell konvertálni.  

Számos alkalmazás nak vannak követelményei a különböző sorrendben rendezett adatok használatára: például az alkalmazottak név szerint vagy csatlakozás dátuma szerint történő rendezése. A következő minták az entitások rendezési sorrendjének váltakozásával foglalkoznak:  

* [Partíción belüli másodlagos indexminta](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – Tárolja az egyes entitások több példányát különböző RowKey-értékek használatával (ugyanabban a partícióban) a gyors és hatékony keresések és a különböző RowKey-értékek használatával történő rendezési sorrendek engedélyezéséhez.  
* [Partíciók közötti másodlagos indexminta](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – Tárolja az egyes entitások több példányát különböző RowKey-értékek használatával külön partíciókban, külön táblákban, hogy gyors és hatékony keresések és alternatív rendezési sorrendek különböző RowKey-értékek használatával.
* [Log tail minta](table-storage-design-patterns.md#log-tail-pattern) – A partícióhoz legutóbb hozzáadott *n* entitások lekérése **egy RowKey** érték használatával, amely fordított dátum- és idősorrendben rendezi.  

## <a name="next-steps"></a>További lépések

- [Táblatervezési minták](table-storage-design-patterns.md)
- [Kapcsolatok modellezése](table-storage-design-modeling.md)
- [Táblaadatok titkosítása](table-storage-design-encrypt-data.md)
- [Tervezés adatmódosításhoz](table-storage-design-for-modification.md)
