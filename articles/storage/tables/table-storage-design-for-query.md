---
title: Lekérdezések az Azure storage-táblák tervezése |} A Microsoft Docs
description: Lekérdezések az Azure table storage-táblák tervezése.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: 8b4ae066edc1c62c25762b5c6feebce1ecfff5a2
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521540"
---
# <a name="design-for-querying"></a>Tervezés lekérdezéshez
TABLE service megoldások nagy igényű, nagy írási vagy a kettő kombinációját olvashatók. Ez a cikk a Table szolgáltatás hatékonyan támogatja az olvasási műveletek tervezésekor figyelembe kell vennie a következő szempontokat összpontosít. Arról, hogy támogatja az olvasási műveletek hatékony terv általában is az írási műveletek hatékony. Előfordulhatnak azonban olyan szempontokat is figyelembe kell vennie, amikor támogatásához tervezése írási művelet, a cikkben tárgyalt [adatmódosítás kialakítása](table-storage-design-for-modification.md).

A Table service megoldás ahhoz, hogy hatékonyan adatolvasási tervezése az jó kiindulási pont, hogy kérje meg a "milyen lekérdezéseket az alkalmazásom kell hajtsa végre a szükséges adatokat lekérni a Table service?"  

> [!NOTE]
> A Table service, a fontos beolvasni a Tervező megfelelő meghozni bonyolult és költséges később módosítható, mert. Például egy relációs adatbázisban gyakran lehetőség cím teljesítményproblémák egyszerűen egy meglévő adatbázist indexek hozzáadásával: Ez a lehetőség a tábla szolgáltatással nem.  
> 
> 

Ez a szakasz a főbb problémák, meg kell oldania a táblák lekérdezése tervezésekor összpontosít. Az ebben a szakaszban ismertetett témaköröket tartalmazza:

* [Milyen hatással van a PartitionKey és rowkey tulajdonságok esetén választott a lekérdezési teljesítmény](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Egy megfelelő PartitionKey kiválasztása](#choosing-an-appropriate-partitionkey)
* [A Table service a lekérdezések optimalizálását](#optimizing-queries-for-the-table-service)
* [A Table service szolgáltatásban az adatok rendezése](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Milyen hatással van a PartitionKey és rowkey tulajdonságok esetén választott a lekérdezési teljesítmény
Az alábbi példák azt feltételezik, hogy a table service az alábbi struktúra használatával alkalmazott entitások tárolja (a példákban a legtöbb hagyja el a **időbélyeg** ajánlattartalomnak tulajdonság):  

| *Oszlop neve* | *Adattípus* |
| --- | --- |
| **PartitionKey** (részleg neve) |Sztring |
| **RowKey** (alkalmazott azonosítója) |Sztring |
| **Keresztnév** |Sztring |
| **Vezetéknév** |Sztring |
| **Kor** |Egész szám |
| **E-mail cím** |Sztring |

A cikk [Azure Table storage áttekintése](table-storage-overview.md) néhány, az Azure Table service legfontosabb funkcióit, hogy közvetlenül befolyásolják a lekérdezés tervezéséhez. Ezek a következő általános irányelveket a Table service Lekérdezéstervezés eredményez. Vegye figyelembe, hogy a szűrési szintaxist használja az alábbi példák a Table service REST API-t a további tudnivalókat lásd a [Entitáslekérdezés](https://docs.microsoft.com/rest/api/storageservices/Query-Entities).  

* A ***pont lekérdezés*** használandó leghatékonyabb keresési és javasoljuk, hogy nagy mennyiségű kereséseket és legkisebb késés igénylő keresések használható. Ilyen lekérdezések használhatja az indexeket egy egyéni entitás nagyon hatékonyan mindkét megadásával keresse meg a **PartitionKey** és **RowKey** értékeket. Például: $filter = (PartitionKey eq "Értékesítés") és (RowKey eq '2')  
* A második legjobb van egy ***Sortartomány-lekérdezés*** , amely használja a **PartitionKey** és a szűrők számos **rowkey tulajdonságok esetén** több entitást visszaadandó értékek. A **PartitionKey** érték azonosítja az adott partíciók és a **RowKey** az értékek azonosítják az adott partíció az entitásokat egy részét. Például: $filter = PartitionKey eq "Értékesítések és a rowkey tulajdonságok esetén a ge" és a rowkey tulajdonságok esetén lt sikerült "  
* Harmadik legjobb van egy ***partíció beolvasása*** , amely használja a **PartitionKey** és a szűrők nem kulcs egy másik tulajdonságot, és hogy a több entitást adhatnak vissza. A **PartitionKey** érték azonosít egy adott partícióra, és a tulajdonság értékei válassza ki az entitásokat az adott partíció részéhez. Például: $filter PartitionKey eq "Értékesítés" és a Vezetéknév eq 'Smith' =  
* A ***tábla beolvasása*** nem tartalmazza a **PartitionKey** és nem nagyon hatékony, mivel a partíciók, viszont a táblázatot a megfelelő entitások alkotó összes keres. Függetlenül attól a szűrőt használ a táblázatbeolvasás hajtja végre a **RowKey**. Például: $filter = LastName eq "János"  
* Több entitás visszaadó lekérdezések vissza rendezve **PartitionKey** és **RowKey** sorrendben. Válassza ki az entitásokat az ügyfél hibahivatkozások elkerüléséhez egy **RowKey** , amely meghatározza, hogy a leggyakrabban használt rendezés.  

Vegye figyelembe, hogy az egy "**vagy**" megadása alapján történő szűrés **RowKey** értéket partíció vizsgálat eredményeket, majd egy sortartomány-lekérdezés nem számít. Ezért kerülje például szűrőket használó lekérdezéseket: $filter = PartitionKey eq "Értékesítés" és a (RowKey eq "121" vagy RowKey eq "322")  

Ügyféloldali kódot, amely hatékony lekérdezések végrehajtására a Storage ügyféloldali kódtár használatával, talál példákat:  

* [A Storage ügyféloldali kódtár használatával pont lekérdezés végrehajtása](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [LINQ használatával több entitás beolvasása](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Kiszolgálóoldali leképezése](table-storage-design-patterns.md#server-side-projection)  

Ügyféloldali kódot, amely képes kezelni ugyanabban a táblában tárolt több entitástípusok példákért lásd:  

* [Heterogén entitástípusok használata](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Egy megfelelő PartitionKey kiválasztása
A választott **PartitionKey** kell terheléselosztást végeznie az EGTs (annak biztosítása érdekében konzisztencia) engedélyezni kell a követelménnyel szemben az entitások szét több partíció (annak biztosítása érdekében egy méretezhető megoldás).  

Egy rendkívüli összes entitás ugyanazon a partíción tárolhatja, de ez korlátozhatja a megoldás méretezhetőségét, és a table service megakadályozná, hogy el a kérelmek terhelése. A rendkívüli, a partíciónként, amely nagy mértékben skálázható lenne és amely lehetővé teszi a table service, a terheléselosztás kérésekre, de amely megakadályozná, a tranzakciók használatával az entitásokban tárolhatja.  

Az ideális **PartitionKey** , amely lehetővé teszi a hatékony lekérdezések használatát, és annak biztosítása érdekében, a megoldás méretezhető elegendő partícióval rendelkezik, amelyek egyike. Általában tapasztalni fogja, hogy az entitások rendelkeznek-e a megfelelő partíciók között az entitások ellátó megfelelő tulajdonság.

> [!NOTE]
> Például egy rendszer, amely a felhasználók vagy az alkalmazottak adatait tárolja, UserID lehet egy jó PartitionKey. Előfordulhat, hogy több entitások, amelyek egy adott felhasználói azonosítót használja partíciókulcsként. Minden entitás, amely tárolja az adatokat a felhasználó egyetlen partícióra vannak csoportosítva, és így ezek az entitások tranzakciók, keresztül érhető el ugyanakkor továbbra is nagy mértékben skálázható.
> 
> 

Nincsenek további szempontok az Ön által választott **PartitionKey** kapcsolódó hogyan meg fog beszúrása, frissítése és törlése entitásokat. További információkért lásd: [adatmódosítás táblák tervezése](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>A Table service a lekérdezések optimalizálását
A Table service automatikusan indexeli az entitásoknak a **PartitionKey** és **RowKey** egyetlen fürtözött index, ezért az okot, amely a lekérdezések pont értékei a leghatékonyabb használatához. Vannak azonban nincs indexei, hogy a fürtözött index a a **PartitionKey** és **RowKey**.

Számos tervek megfelel a követelményeknek, engedélyezéséhez keresési entitások több feltétel alapján. Például megkeresése az e-mailben alapján alkalmazott entitások alkalmazott azonosítója, vagy a vezetéknevét. A minták ismertetett [Table tervezési minták](table-storage-design-patterns.md) követelmény az ilyen típusú címet, valamint az a tény, hogy a Table service nem biztosít a másodlagos indexek megkerülő módjait ismertetik:  

* [Intra-partition másodlagos index minta](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -Store több példányban minden entitás használatával különböző **RowKey** értékeket (az ugyanazon a partíción) a gyors és hatékony kereséseket, valamint alternatív a rendezési sorrend különböző használatával **RowKey** értékeket.  
* [Közötti partíció másodlagos indexe mintája](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) -Store több példányban minden entitás használatával különböző **RowKey** értékek a különböző partíciók vagy a különböző táblák gyors és hatékony keresések és a másodlagos rendezés engedélyezése rendelések használatával különböző **RowKey** értékeket.  
* [Index entitások minta](table-storage-design-patterns.md#index-entities-pattern) -karbantartása index entitások engedélyezése a hatékony keresést, hogy az entitások listáját adja vissza.  

## <a name="sorting-data-in-the-table-service"></a>A Table service szolgáltatásban az adatok rendezése
A Table service adja vissza növekvő sorrendben alapján entitások **PartitionKey** , majd az **RowKey**. Ezek a kulcsok karakterlánc-értékek és annak érdekében, hogy numerikus értékek megfelelően rendezni, meg kell alakíthatja át őket egy rögzített hosszúságú és nullákkal kitölti őket. Például, ha az alkalmazott azonosító értéket használja a **rowkey tulajdonságok esetén** egy egész érték alkalmazott azonosítója alakítsuk **123** való **00000123**.  

Számos alkalmazás követelményei eltérőek rendezett adatok használata: az alkalmazottak például rendezés, neve, vagy dátum csatlakoztatásával. A következő minták cím az az eredetitől eltérő rendezési sorrend az entitások hogyan:  

* [Másodlagos indexet a minta Intra-partition](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Store segítségével különböző RowKey értékeket (az ugyanazon a partíción) lehetővé teszik a gyors minden entitás több példányát, és hatékony keresések és a másodlagos rendezés orders különböző RowKey értékek alapján.  
* [Közötti partíció másodlagos indexe mintája](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Store értékeivel különböző rowkey tulajdonságok esetén külön partícióban külön táblázatban gyorsan minden entitás több példányát, és hatékony keresések és a másodlagos rendezés orders különböző RowKey értékek alapján .
* [Log tail minta](table-storage-design-patterns.md#log-tail-pattern) -lekérni a *n* használatával a partíció legutóbb hozzáadott entitásokat egy **RowKey** érték, amely fordított dátum és idő sorrendben rendezi.  

## <a name="next-steps"></a>További lépések

- [Táblázat kialakítási minták](table-storage-design-patterns.md)
- [Kapcsolatok modellezését](table-storage-design-modeling.md)
- [Táblák adatainak titkosítása](table-storage-design-encrypt-data.md)
- [Adatmódosítás tervezése](table-storage-design-for-modification.md)
