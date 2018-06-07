---
title: Az Azure storage-táblákat lekérdezések kialakítása |} Microsoft Docs
description: Tervezze meg a táblák lekérdezések Azure table storage-ban.
services: storage
documentationcenter: na
author: MarkMcGeeAtAquent
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: b8d2033b0b29caddf165f4b582c7d0578109480c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660918"
---
# <a name="design-for-querying"></a>Tervezési lekérdezése
TABLE szolgáltatási megoldások intenzív, írási intenzív vagy a két vegyesen olvashatók. Ez a cikk a Table szolgáltatás az olvasási műveletek hatékonyan támogatásához tervezése során figyelembe kell vennie a következőket összpontosít. A Tervező, támogatja a hatékony olvassa el az operations általában is hatékony az írási műveletek. Van azonban szempontokat is figyelembe kell vennie, amikor támogatásához tervezése írási műveleteket, a cikkben ismertetett [adatmódosítás kialakítása](table-storage-design-for-modification.md).

Ahhoz, hogy hatékonyan-adatok olvasása a Table szolgáltatás megoldás tervezése az jó kiindulási pont, kérje meg a "milyen lekérdezések alkalmazás kell végrehajtani a szükséges adatokat lekérdezni a Table szolgáltatás?"  

> [!NOTE]
> A Table szolgáltatás fontos beolvasni a Tervező megfelelő előre mert bonyolult és költséges később módosítható. Például egy relációs adatbázisban is gyakran lehet cím teljesítményproblémák egyszerűen, amelyet indexek hozzáadása egy meglévő adatbázist: Ez a lehetőség nem érhető a Table szolgáltatással.  
> 
> 

Ez a szakasz meg kell oldania a táblák lekérdezése tervezésekor kulcs témákra összpontosít. Ebben a szakaszban szereplő témakörök az alábbiak:

* [Hogy a választott PartitionKey és RowKey milyen hatással van a teljesítmény-küszöbérték](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Egy megfelelő PartitionKey kiválasztása](#choosing-an-appropriate-partitionkey)
* [A Table szolgáltatás lekérdezések optimalizálása](#optimizing-queries-for-the-table-service)
* [A Table szolgáltatás az adatok rendezése](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Hogy a választott PartitionKey és RowKey milyen hatással van a teljesítmény-küszöbérték
Az alábbi példák azt feltételezik, hogy a table szolgáltatás a következő struktúrával alkalmazott entitások tárolja (a példák a legtöbb hagyja ki ezt a **időbélyeg** jobb érthetőség kedvéért bizonyos tulajdonság):  

| *Oszlop neve* | *Adattípus* |
| --- | --- |
| **PartitionKey** (részleg neve) |Karakterlánc |
| **RowKey** (alkalmazott azonosítója) |Karakterlánc |
| **Utónév** |Karakterlánc |
| **Vezetéknév** |Karakterlánc |
| **kora** |Egész szám |
| **E-mail cím** |Karakterlánc |

A cikk [Azure Table storage áttekintése](table-storage-overview.md) néhány szolgáltatását az Azure Table szolgáltatás, amely közvetlenül befolyásolják a lekérdezéshez tervezéséről foglalja össze. Ezek a Table szolgáltatás Lekérdezéstervezés vonatkozó általános irányelveket eredményez. Vegye figyelembe, hogy a Table szolgáltatásból REST API-t a további tudnivalókat lásd az alábbi példákban szereplő szűrőszintaxisának [lekérdezés entitások](https://docs.microsoft.com/rest/api/storageservices/Query-Entities).  

* A ***pont lekérdezés*** a leghatékonyabb keresési használatára, és nagy mennyiségű keresések vagy a legkisebb mértékű késleltetést igénylő keresések használandó ajánlott. Ilyen lekérdezést az indexek segítségével egyedi entitás nagyon hatékonyan mindkét megadásával keresse meg a **PartitionKey** és **RowKey** értékeket. Például: $filter = (PartitionKey eq 'Értékesítés') és (RowKey eq '2')  
* Második legjobb van egy ***értéktartomány lekérdezésének*** , amely használja a **PartitionKey** és a szűrők számos **RowKey** egynél több entitáskészlet visszaadandó értékek száma. A **PartitionKey** érték azonosít egy adott partícióra, és a **RowKey** értékek azonosítsa az adott partíció entitástartományának részhalmazát. Például: $filter = "Értékesítési és RowKey ge" PartitionKey eq és RowKey lt jelzést "  
* Harmadik legjobb van egy ***partíció vizsgálata*** , amely használja a **PartitionKey** és olyan nem kulcs tulajdonsága, és hogy a szűrők térhetnek vissza egy entitást. A **PartitionKey** érték egy adott partícióra azonosítja, és a tulajdonság értékek válassza ki az adott partíció entitást egy részéhez. Például: $filter PartitionKey eq "Értékesítési" és a Vezetéknév eq 'Smith' =  
* A ***tábla vizsgálata*** nem tartalmazza a **PartitionKey** és nem nagyon hatékony, mivel az összes pedig a hozzá tartozó entitások az a táblázat partíciókat keresi. A táblázatbeolvasás, függetlenül a szűrő használja-e hajtja végre a **RowKey**. Például: $filter Vezetéknév eq "János" =  
* Több entitás visszaadó lekérdezések Alapértelmezések rendezve **PartitionKey** és **RowKey** sorrendje. Az ügyfél entitást keresésére átrendezésével elkerüléséhez válassza ki a **RowKey** , amely meghatározza, hogy a leggyakrabban használt rendezési sorrend.  

Vegye figyelembe, hogy használja az "**vagy**" alapján egy szűrő meghatározására **RowKey** értéket egy partíció vizsgálati eredményeket, majd egy értéktartomány lekérdezésének nem számít. Ezért kerülje el a lekérdezéseket, szűrők, mint amelyekkel: $filter = PartitionKey eq 'Értékesítés' és (RowKey eq "121" vagy "322" RowKey eq)  

Az ügyféloldali kódot, amely hatékony-lekérdezéseket hajt végre a Storage ügyféloldali kódtár használatával című részben talál példákat:  

* [A Storage ügyféloldali kódtár segítségével pont lekérdezés végrehajtása](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Több entitás LINQ használatával beolvasása](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Kiszolgálóoldali leképezése](table-storage-design-patterns.md#server-side-projection)  

Ügyféloldali kódot, amelyet kezelni ugyanabban a táblában tárolt több entitás típusokat tud példákért lásd:  

* [Heterogén entitástípusok használata](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Egy megfelelő PartitionKey kiválasztása
A választott **PartitionKey** kell terheléselosztást kell EGTs (konzisztencia biztosításához) használatának engedélyezése a követelménnyel szemben az entitások szét több partíciót (méretezhető megoldás biztosításához).  

Egy rendkívüli az entitások sikerült egyetlen partícióra vannak tárolva, de ez korlátozottá teheti méretezhetőségét a megoldás, és akadályozzák a table szolgáltatás terheléselosztásához kérelmek igényt. A rendkívüli egy entitás partíció található, amely magas szinten méretezhető lenne és amely lehetővé teszi a table szolgáltatás terheléselosztási kérésekre, de amelyek volna újrafelhasználásának megtiltása entitás csoport tranzakciók száma tárolhatja.  

Az ideális **PartitionKey** , amely lehetővé teszi, hogy használni lehessen a hatékony lekérdezések és annak biztosítása érdekében, a megoldás méretezhető elegendő partíciókkal rendelkezik, amelyek egyike. Általában találja, hogy az entitások lesz-e az entitások elosztja elegendő partíciók megfelelő tulajdonság.

> [!NOTE]
> A rendszer, hogy a felhasználók vagy az alkalmazottak kapcsolatos információkat tárolja, például UserID lehet egy jó PartitionKey. Előfordulhat, hogy több entitás, amely egy adott felhasználói azonosítóját használják a partíciós kulcs. Minden entitás, amely a felhasználó adatait tárolja egyetlen partícióra vannak csoportosítva, és ezért ezeket az entitásokat elérhetők entitás csoport tranzakciók, miközben továbbra is magas szinten méretezhető.
> 
> 

A választott további szempontot **PartitionKey** hogyan meg fog beszúrási, frissítési és törlési entitások kapcsolódó. További információkért lásd: [adatok módosítása a táblák tervezése](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>A Table szolgáltatás lekérdezések optimalizálása
A Table szolgáltatás automatikusan indexeli az entitások használata a **PartitionKey** és **RowKey** egy fürtözött index, ezért az oka, hogy a lekérdezések pont értékei a leghatékonyabb használatára. Van azonban nem indexei eltérő a fürtözött index a a **PartitionKey** és **RowKey**.

Sok ahhoz, hogy az entitások több feltétel alapján keresési kell megfelelnie. Például az e-mailek alapján alkalmazott entitások keresése Alkalmazottazonosító vagy vezetéknevét. A minták ismertetett [táblázat kialakítási minta](table-storage-design-patterns.md) követelmény az ilyen típusú cím, és azt a tényt, hogy a Table szolgáltatás nem nyújt másodlagos indexek körül módon ismertetik:  

* [Intra-partíció másodlagos index mintát](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -tárolja a több másolatot minden entitás használatával különböző **RowKey** engedélyezése gyors és hatékony keresések és másodlagos rendezési sorrend különböző használatával (a partícióra) értékek **RowKey** értékeket.  
* [Másodlagos index közötti particionálásához mintát](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) -tárolja a több másolatot minden entitás használatával különböző **RowKey** értékek a különböző partíciók, vagy a külön a táblák gyors és hatékony keresést és a másodlagos rendezési sorrend engedélyezése a különböző rendelések **RowKey** értékeket.  
* [Index entitások mintát](table-storage-design-patterns.md#index-entities-pattern) -index entitások entitások listájának visszaadó hatékony keresések engedélyezése karbantartása.  

## <a name="sorting-data-in-the-table-service"></a>A Table szolgáltatás az adatok rendezése
A Table szolgáltatás adja vissza növekvő sorrendben rendezve entitások **PartitionKey** és a majd **RowKey**. Ezek a kulcsok karakterlánc-értékek és numerikus értékek megfelelően rendezése érdekében kell alakíthatja át őket egy rögzített hosszúságú és nulla kitölti őket. Például, ha az alkalmazott azonosítóérték használja a **RowKey** megadott egész szám, alkalmazottazonosító átalakíthatja **123** való **00000123**.  

Számos alkalmazás más-más sorrendben rendezett adatok használata követelményekkel rendelkezik: az alkalmazottak például rendezést a neve vagy való csatlakozás dátumát. A következő minták számára, hogy a rendezési sorrend a entitások hogyan cím:  

* [Helyen belüli-partíció másodlagos index mintát](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - különböző RowKey értékei alapján (a partícióra) gyors engedélyezése minden entitás több példányát tárolja, és hatékony keresést és a másodlagos rendezési sorrend rendelések különböző RowKey értékek használatával.  
* [Másodlagos helyek közötti partíció index mintát](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – minden külön táblázatban külön partíciók különböző RowKey értékek segítségével engedélyezi a gyors entitás több példányát tárolja, és hatékony keresést és a másodlagos rendezési sorrend rendelések különböző RowKey értékek használatával .
* [Napló végéről mintát](table-storage-design-patterns.md#log-tail-pattern) -beolvasni a *n* partíció legutóbb hozzáadott entitások egy **RowKey** érték, amely fordított dátum és idő sorrendben rendezi.  

## <a name="next-steps"></a>További lépések

- [Táblázat kialakítási minták](table-storage-design-patterns.md)
- [Modellezési kapcsolatok](table-storage-design-modeling.md)
- [Tábla adatok titkosítása](table-storage-design-encrypt-data.md)
- [Adatmódosítás kialakítása](table-storage-design-for-modification.md)
