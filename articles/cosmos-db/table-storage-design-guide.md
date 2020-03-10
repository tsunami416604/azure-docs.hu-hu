---
title: A méretezéshez és a teljesítményhez Azure Cosmos DB táblázatok tervezése
description: 'Az Azure Table Storage tervezési útmutatója: skálázható és teljesített táblázatok a Azure Cosmos DB és az Azure Table Storage szolgáltatásban'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: sakash279
ms.author: akshanka
ms.custom: seodec18
ms.openlocfilehash: 166076d366cbbf7bef24648772beaba9b3a88253
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395649"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Az Azure Table Storage tábla tervezési útmutatója: skálázható és elvégezhető táblák

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

A méretezhető és nagy teljesítményű táblák tervezésénél rengeteg szempontot, köztük a költséget is figyelembe kell vennie. Ha korábban már tervezett sémákat relációs adatbázisokhoz, ismerősnek fogja találni ezeket a szempontokat. Bár az Azure Table Storage és a relációs modellek között vannak hasonlóságok, több fontos dologban is eltérnek egymástól. Ezek a különbségek általában olyan eltérő kialakításokhoz vezetnek, amelyek a relációs adatbázisokat ismerő személyek számára ellentmondásosnak vagy helytelennek tűnhetnek, azonban a NoSQL-alapú kulcs/érték tárolók, például a Table Storage számára való tervezésekor van értelmük.

A Table Storage úgy van kialakítva, hogy támogassa a felhőalapú alkalmazásokat, amelyek több milliárd entitást ("sorok") tartalmazhatnak a kapcsolódó adatbázis-terminológiában, illetve olyan adatkészleteket, amelyeknek támogatniuk kell a nagy tranzakciós köteteket. Ezért másképp kell gondolkodnia az adatai tárolásával kapcsolatban, és megismerheti a Table Storage működését. A jól megtervezett NoSQL-adattárak lehetővé teszik, hogy a megoldás sokkal tovább méretezhető legyen (és alacsonyabb költségeket is), mint a viszonyítási adatbázist használó megoldás. Az útmutató az alábbi témakörök segítségével.  

## <a name="about-azure-table-storage"></a>Tudnivalók az Azure Table Storage szolgáltatásról
Ez a szakasz a Table Storage egyes főbb funkcióit mutatja be, amelyek különösen a teljesítmény és a méretezhetőség tervezéséhez szükségesek. Ha még nem ismeri az Azure Storage-t és a Table Storage-t, tekintse meg a jelen cikk további részének beolvasása című témakör [Microsoft Azure Storage](../storage/common/storage-introduction.md) és az [Azure Table Storage használatának első lépéseit](table-storage-how-to-use-dotnet.md) ismertető cikket. Bár ez az útmutató a Table Storage-ra koncentrál, az Azure üzenetsor-tárolással és az Azure Blob Storage-mel kapcsolatos néhány vitát tartalmaz, valamint azt, hogy miként használhatja őket a megoldásban a Table Storage szolgáltatással együtt.  

A Table Storage táblázatos formátumban tárolja az adatokat. Ismertetésében, az a táblázat minden egyes sorához egy entitást jelent, és az oszlopok tárolja az adott entitás tulajdonságait. Minden entitás rendelkezik egy pár kulccsal az egyedi azonosításhoz, valamint egy időbélyeg-oszlophoz, amelyet a Table Storage használ az entitás utolsó frissítésének nyomon követésére. Az időbélyeg mező automatikusan hozzáadódik, és nem lehet manuálisan felülírni az időbélyeget tetszőleges értékkel. A Table Storage ezt a legutóbb módosított időbélyeget (LMT) használja az optimista Egyidejűség kezelésére.  

> [!NOTE]
> A Table Storage REST API műveletei az LMT-ból származtatott `ETag` értéket is visszaadnak. Ebben a dokumentumban a ETag és az LMT kifejezéseket szinonimaként használjuk, mert ugyanazok az alapul szolgáló adatokat használják.  
> 
> 

Az alábbi példa bemutatja egy egyszerű táblázat megtervezése és az alkalmazottak és a részleg entitásokat. Az egyszerű kialakítás számos, az útmutató későbbi részében látható példák alapul.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Időbélyeg</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Don</td>
<td>Hall</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td>jún.</td>
<td>CaO</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>Részleg</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Értékesítés</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Eddig ez a kialakítás a kapcsolódó adatbázisban lévő táblákhoz hasonlóan néz ki. A legfontosabb különbségek a kötelező oszlopok, és több entitás típusának lehetősége ugyanabban a táblában. Emellett a felhasználó által definiált tulajdonságok (például a **FirstName** vagy a **Age**) adattípusa (például Integer vagy string) egy, a rokon adatbázisban lévő oszlophoz hasonlóan. A relációs adatbázistól eltérően azonban a Table Storage séma nélküli jellege azt jelenti, hogy egy tulajdonságnak nem kell ugyanazzal az adattípussal rendelkeznie az egyes entitásokon. Összetett adattípusok tárolni egyetlen tulajdonságát, például JSON vagy XML formátumú szerializált kell használnia. További információ: a [Table Storage adatmodell ismertetése](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Az Ön választása `PartitionKey` és `RowKey` a jó tábla kialakításához elengedhetetlen. A táblázatban tárolt összes entitásnak `PartitionKey` és `RowKey`egyedi kombinációjával kell rendelkeznie. A kapcsolati adatbázistábla kulcsaihoz hasonlóan a `PartitionKey` és a `RowKey` értékek indexelve vannak, így a gyors kereséseket lehetővé tevő fürtözött index hozható létre. A Table Storage azonban nem hoz létre másodlagos indexeket, így ezek az egyetlen két indexelt tulajdonság (a később bemutatott minták némelyike azt mutatja be, hogyan lehet megkerülni a látszólagos korlátozást).  

Egy tábla egy vagy több partícióból áll, és számos tervezési döntés közül választhat a megfelelő `PartitionKey` és `RowKey` a megoldás optimalizálásához. A megoldások csak egyetlen táblából állhatnak, amely tartalmazza az összes olyan entitást, amely partícióra van osztva, de általában egy megoldás több táblával is rendelkezik. A táblázatok segítségével logikailag rendszerezheti az entitásokat, és a hozzáférés-vezérlési listák segítségével kezelheti az adathozzáférést. A teljes táblázatot egyetlen tárolási művelettel lehet eldobni.  

### <a name="table-partitions"></a>Táblapartíciók
A fiók neve, a tábla neve és a `PartitionKey` együttesen azonosítják azt a tárolási szolgáltatáson belüli partíciót, ahol a Table Storage tárolja az entitást. Emellett az entitások címzési sémájának részeként a partíciók határozzák meg a tranzakciók hatókörét (lásd a cikk későbbi részében, az [Entity Transactions-tranzakciók](#entity-group-transactions)című szakaszt), és a tábla tárolási skálázásának alapjait alkotják. További információ a Table Partitions szolgáltatásról: a [Table Storage teljesítmény-és méretezhetőségi ellenőrzőlistája](../storage/tables/storage-performance-checklist.md).  

A Table Storage szolgáltatásban az egyes csomópontok egy vagy több teljes partíciót, a szolgáltatás pedig a csomópontok közötti dinamikusan terheléselosztást végez. Ha egy csomópont terhelés alatt van, a Table Storage feloszthatja az adott csomópont által kiszolgált partíciók tartományát különböző csomópontokra. Ha a forgalom alá esik, a Table Storage képes egyesíteni a partíciók tartományait a csendes csomópontokból egyetlen csomópontra.  

További információ a Table Storage belső részleteiről és különösen a partíciók kezeléséről: [Microsoft Azure Storage: magas rendelkezésre állású felhőalapú tárolási szolgáltatás erős konzisztencia](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)mellett.  

### <a name="entity-group-transactions"></a>Entitás-csoport tranzakciói
A Table Storage szolgáltatásban az Entity Transactions (EGTs) az egyetlen beépített mechanizmus, amellyel több entitáson végezheti el az Atomic-frissítéseket. A EGTs más néven batch- *tranzakciónak*is nevezzük. A EGTs csak ugyanabban a partícióban tárolt entitásokban működhet (egy adott tábla azonos partíciójának megosztásával), így bármikor szükség van az atomi tranzakciós viselkedésre több entitás között, hogy az entitások ugyanabban a partícióban legyenek. Ez gyakran indokolja, hogy több entitást is tartson ugyanabban a táblában (és partícióban), és ne használjon több táblát a különböző típusú entitásokhoz. Egyetlen EGT működhet, a legfeljebb 100 entitást.  Ha több párhuzamos EGTs küld a feldolgozáshoz, fontos, hogy ezek a EGTs ne működjenek olyan entitásokon, amelyek a EGTs-ben közösek. Ellenkező esetben a feldolgozás késleltetését kockáztatja.

A EGTs emellett potenciális kompromisszumot is bevezet, hogy kiértékelje a kialakítását. A több partíció használata növeli az alkalmazás méretezhetőségét, mivel az Azure több lehetőséget kínál a terheléselosztási kérelmek közötti terheléselosztásra. Ez azonban korlátozhatja, hogy az alkalmazás képes legyen atomi tranzakciókat végrehajtani, és erős konzisztenciát fenntartani az adatokhoz. Emellett vannak olyan méretezhetőségi célok is a partíció szintjén, amelyek korlátozhatják az egyetlen csomópontra várható tranzakciók átviteli sebességét.

Az Azure Storage-fiókok méretezhetőségi céljaival kapcsolatos további információkért lásd [a standard szintű Storage-fiókok méretezhetőségi céljait](../storage/common/scalability-targets-standard-account.md)ismertető témakört. A Table Storage skálázhatósági céljaival kapcsolatos további információkért lásd: [a táblázatos tárolás skálázhatósági és teljesítményi céljai](../storage/tables/scalability-targets.md). Ez az útmutató későbbi részekben bemutatjuk a különböző tervezési stratégiákat, amelyekkel kezelheti, például a kompromisszummal és hogyan érdemes beszélgetés kiválasztása a partíciókulcs alapján az ügyfélalkalmazás vonatkozó konkrét követelményekről.  

### <a name="capacity-considerations"></a>A kapacitás szempontok
A következő táblázat néhány kulcsfontosságú értéket tartalmaz, amelyek a Table Storage-megoldás tervezésekor szükségesek:  

| Az Azure storage-fiók teljes kapacitás | 500 TB |
| --- | --- |
| Az Azure storage-fiók táblák száma |Csak a Storage-fiók kapacitása korlátozza. |
| Egy táblát a partíciók száma |Csak a Storage-fiók kapacitása korlátozza. |
| Egy partíció entitások száma |Csak a Storage-fiók kapacitása korlátozza. |
| Egy egyéni entitás méretét |Legfeljebb 1 MB, legfeljebb 255 tulajdonsággal (beleértve a `PartitionKey`, `RowKey`és `Timestamp`). |
| A `PartitionKey` mérete |Legfeljebb 1 KB méretű sztring. |
| A `RowKey` mérete |Legfeljebb 1 KB méretű sztring. |
| Entitás-csoport tranzakciójának mérete |Egy tranzakció legfeljebb 100 entitást tartalmazhat, és a hasznos adatnak 4 MB-nál kisebbnek kell lennie. Miután egy EGT csak is frissítheti egy entitás. |

További információ: [a Table Service adatmodell ismertetése](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Költségekkel kapcsolatos szempontok
A Table Storage szolgáltatás viszonylag olcsó, azonban a kapacitás kihasználtsága és a tranzakciók mennyisége a Table Storage szolgáltatást használó bármely megoldás kiértékelésének részeként is szerepelnie kell. Számos forgatókönyv esetében azonban a denormalizált vagy ismétlődő adatok tárolása a megoldás teljesítményének és méretezhetőségének javítása érdekében érvényes megközelítés. A díjszabással kapcsolatos további információkért lásd: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Irányelvek táblatervezéshez
Ezek a lista összefoglalják a legfontosabb irányelveket, amelyeket érdemes szem előtt tartani a táblák tervezésekor. Ez az útmutató később részletesen ismerteti őket. Ezek az irányelvek különböznek azoktól az irányelvektől, amelyeket általában a kapcsolatok adatbázisának megtervezése követ.  

A Table Storage hatékony *olvasásának* megtervezése:

* **Az olvasási és a nagy teljesítményű alkalmazásokban való lekérdezés tervezése.** Amikor megtervezi a táblázatokat, gondolja át a lekérdezéseket (különösen a késésre érzékenyeket), mielőtt meggondolja, hogyan frissítheti az entitásokat. Ez általában egy hatékony és nagy teljesítményű megoldást eredményez.  
* **Adja meg a lekérdezésekben a `PartitionKey` és a `RowKey` is.** A *pontok lekérdezései* , például ezek a leghatékonyabb Table Storage-lekérdezések.  
* **Érdemes lehet az entitások duplikált példányait tárolni.** A Table Storage olcsó, ezért a hatékonyabb lekérdezések lehetővé tételéhez ugyanazt az entitást többször kell tárolni (különböző kulcsokkal).  
* **Érdemes lehet az adatai denormalizálása.** A Table Storage olcsó, ezért érdemes lehet az adatai denormalizálása. Tárolhatja például összefoglaló entitások, hogy az összesített adatok lekérdezések csak egyetlen entitás eléréséhez szükséges.  
* **Használjon összetett kulcsos értékeket.** Az egyetlen kulcsok `PartitionKey` és `RowKey`. Például összetett kulcs értékeit használatával engedélyezi a másodlagos kulcsalgoritmus elérési útvonalakat entitásokhoz.  
* **Lekérdezési leképezés használata.** Csökkentheti a lekérdezések, amelyek csak a szükséges mezők kiválasztása a hálózati átvitel adatok mennyisége.  

A tábla tárterületének megtervezése hatékony *írásra* :  

* **Ne hozzon létre gyors partíciót.** Válassza ki a kulcsokat, amelyek lehetővé teszik a kérelmek helyezkednek el, bármikor több partíciót.  
* **Kerülje a forgalomban lévő tüskéket.** Ossza el a forgalmat egy ésszerű időn belül, és kerülje a forgalomban lévő tüskéket.
* **Ne feltétlenül hozzon létre külön táblát az egyes entitások típusaihoz.** Ha elemi tranzakciókat entitástípusok között, tárolhatók ezek több entitás ugyanazon a partíción ugyanabban a táblában.
* **Gondolja át, hogy mekkora átviteli sebességet kell elérnie.** Tisztában kell lennie a Table Storage skálázhatósági céljaival, és biztosítania kell, hogy a kialakítás ne lépje túl a táblázatot.  

Az útmutató későbbi részében olyan példákat talál, amelyek az összes alapelvet bevezetik a gyakorlatban.  

## <a name="design-for-querying"></a>Tervezés lekérdezéshez
A Table Storage nagy mennyiségű, írható vagy a kettő kombinációját képes olvasni. Ez a szakasz az olvasási műveletek hatékony támogatásának kialakítását veszi figyelembe. Arról, hogy támogatja az olvasási műveletek hatékony terv általában is az írási műveletek hatékony. Az írási műveletek támogatásának tervezésekor azonban további szempontokat is figyelembe kell venni. Ezeket a következő, az [adatmódosításra szolgáló tervezési](#design-for-data-modification)szakaszban tárgyaljuk.

Egy jó kiindulási pont, amely lehetővé teszi, hogy az adatolvasások hatékonyak legyenek: "milyen lekérdezéseket kell futtatni az alkalmazásnak, hogy lekérje a szükséges adatgyűjtést?"  

> [!NOTE]
> A Table Storage szolgáltatással fontos, hogy a kialakítás elöl legyen, mivel nehéz és költséges lehet később módosítani. A kapcsolódó adatbázisban például gyakran lehetséges a teljesítménnyel kapcsolatos problémák megoldása, ha indexeket ad hozzá egy meglévő adatbázishoz. Ez a táblázatos tárolással nem lehetséges.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>`PartitionKey` és `RowKey` a lekérdezési teljesítményre gyakorolt hatása
A következő példák azt feltételezik, hogy a Table Storage a következő szerkezettel tárolja az alkalmazotti entitásokat (a legtöbb példa kihagyja a `Timestamp` tulajdonságot az érthetőség kedvéért):  

| Oszlop neve | Adattípus |
| --- | --- |
| `PartitionKey` (részleg neve) |Sztring |
| `RowKey` (alkalmazott azonosítója) |Sztring |
| `FirstName` |Sztring |
| `LastName` |Sztring |
| `Age` |Egész szám |
| `EmailAddress` |Sztring |

Íme néhány általános útmutató a Table Storage-lekérdezések tervezéséhez. A következő példákban használt szűrési szintaxis a Table Storage REST API. További információ: [lekérdezési entitások](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* Az adott *pont lekérdezése* a leghatékonyabb keresés, amelyet a legalacsonyabb késleltetést igénylő nagy mennyiségű keresésekhez vagy keresésekhez ajánlott használni. Egy ilyen lekérdezés az indexeket használva hatékonyan megkeresheti az egyes entitásokat a `PartitionKey` és az `RowKey` érték megadásával. Például: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* A második legjobb a *tartomány lekérdezése*. A `PartitionKey`és a szűrőket használja `RowKey` értékeken, így több entitást ad vissza. A `PartitionKey` érték egy adott partíciót azonosít, és a `RowKey` érték azonosítja a partícióban lévő entitások egy részhalmazát. Például: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* A harmadik legjobb a *partíciós vizsgálat*. A `PartitionKey`és a szűrőket használja egy másik nem kulcsfontosságú tulajdonsághoz, és több entitást is visszaadhat. A `PartitionKey` érték egy adott partíciót azonosít, és a tulajdonságértékek a partícióban lévő entitások egy részhalmaza számára választhatók ki. Például: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* A *tábla vizsgálata* nem tartalmazza a `PartitionKey`t, és nem hatékony, mert megkeresi az összes olyan partíciót, amelyik felkészíti a táblázatot a megfelelő entitásokra. Egy táblázatos vizsgálatot végez, függetlenül attól, hogy a szűrő használja-e a `RowKey`. Például: `$filter=LastName eq 'Jones'`.  
* A több entitást visszaadó Azure Table Storage-lekérdezések `PartitionKey` és `RowKey` sorrendbe rendezve. Ha nem szeretné, hogy az entitások ne legyenek az ügyfélben, válasszon egy `RowKey`, amely meghatározza a leggyakoribb rendezési sorrendet. A Azure Cosmos DB Azure Table API által visszaadott lekérdezési eredmények nem a partíciós kulcs vagy a sor kulcsa szerint vannak rendezve. A szolgáltatások közötti különbségek részletes listáját a [Azure Cosmos db és az Azure Table storage Table API közötti különbségek](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)című részben tekintheti meg.

A "**vagy**" használatával `RowKey` értékek alapján határozhat meg egy szűrőt, és a partíciók vizsgálatát eredményezi, és nem kezelhető tartomány-lekérdezésként. Ezért ne használjon olyan lekérdezéseket, amelyek a következő szűrőket használják: `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`.  

A Storage ügyféloldali kódtárat használó ügyféloldali kódokra vonatkozó példákat a hatékony lekérdezések futtatásához lásd:  

* [Pont lekérdezésének futtatása a Storage ügyféloldali kódtár használatával](#run-a-point-query-by-using-the-storage-client-library)
* [Több entitás beolvasása a LINQ használatával](#retrieve-multiple-entities-by-using-linq)
* [Kiszolgálóoldali kivetítés](#server-side-projection)  

Ügyféloldali kódot, amely képes kezelni ugyanabban a táblában tárolt több entitástípusok példákért lásd:  

* [Heterogén entitások típusának használata](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Válasszon ki egy megfelelő `PartitionKey`
Az Ön által választott `PartitionKey`nak egyensúlyt kell biztosítania a EGTs használatának lehetővé tételéhez (az egységesség biztosításához) az entitások több partíción való terjesztésének követelménye (a méretezhető megoldás biztosításához).  

Egyetlen szélsőséges esetben egyetlen partícióban tárolhatja az összes entitást. Ez azonban korlátozhatja a megoldás méretezhetőségét, és megakadályozhatja, hogy a tábla tárterülete ne tudja betölteni a kérelmeket. A másik végletben partícióként egy entitást is tárolhat. Ez a rugalmasan méretezhető, és lehetővé teszi a Table Storage számára, hogy terheléselosztási kérelmeket használjon, de megakadályozza az entitás-csoportok tranzakcióinak használatát.  

Az ideális `PartitionKey` lehetővé teszi a hatékony lekérdezések használatát, és elegendő partíciót biztosít a megoldás méretezhetőségének biztosításához. Általában úgy találja, hogy az entitások megfelelő tulajdonsággal rendelkeznek, amely az entitásokat a megfelelő partíciók között osztja szét.

> [!NOTE]
> Például egy olyan rendszeren, amely adatokat tárol a felhasználókról vagy alkalmazottakról, `UserID` lehet jó `PartitionKey`. Lehet, hogy több entitása van, amelyek egy adott `UserID` használják a partíciós kulcsként. Minden entitás, amely egy felhasználó adatait tárolja, egyetlen partícióba van csoportosítva. Ezek az entitások a EGTs-on keresztül érhetők el, miközben továbbra is rugalmasan méretezhetők.
> 
> 

Az entitások beszúrási, frissítési és törlési módjával kapcsolatban további szempontokat is figyelembe kell vennie `PartitionKey`. További információt a cikk későbbi, [adatmódosítási terve](#design-for-data-modification) című szakaszában talál.  

### <a name="optimize-queries-for-table-storage"></a>A Table Storage-lekérdezések optimalizálása
A Table Storage automatikusan indexeli az entitásokat egy fürtözött index `PartitionKey` és `RowKey` értékének használatával. Ennek az az oka, hogy a pontok lekérdezései a leghatékonyabbak. A `PartitionKey` és a `RowKey`fürtözött indexén azonban nincsenek indexek.

Számos tervek megfelel a követelményeknek, engedélyezéséhez keresési entitások több feltétel alapján. Például az alkalmazotti entitások az e-mailek, az alkalmazotti AZONOSÍTÓk vagy a vezetéknév alapján vannak megkeresve. A (z) szakasz [tábla kialakítási mintáinak](#table-design-patterns) következő mintái az ilyen típusú követelményeket kezelik. A minták azt is leírják, hogyan lehet megkerülni, hogy a Table Storage nem biztosít másodlagos indexeket.  

* [Partíción belüli másodlagos index minta](#intra-partition-secondary-index-pattern): az egyes entitások több példányának tárolása különböző `RowKey` értékek használatával (ugyanabban a partícióban). Ez lehetővé teszi a gyors és hatékony kereséseket, valamint a különböző `RowKey` értékek használatával történő más rendezési sorrendek használatát.  
* [Partíciók közötti másodlagos index minta](#inter-partition-secondary-index-pattern): az egyes entitások több példányának tárolása különböző `RowKey`i értékek külön partíciókban vagy külön táblákban való használatával. Ez lehetővé teszi a gyors és hatékony kereséseket, valamint a különböző `RowKey` értékek használatával történő más rendezési sorrendek használatát.  
* [Entitások indexelése minta](#index-entities-pattern): az index entitások kezelése az entitások listáját visszaadó hatékony keresések engedélyezéséhez.  

### <a name="sort-data-in-table-storage"></a>Az Adatrendezés a Table Storage-ban

A Table Storage a lekérdezés eredményeit növekvő sorrendbe rendezi, `PartitionKey` és `RowKey`alapján.

> [!NOTE]
> A Azure Cosmos DB Azure Table API által visszaadott lekérdezési eredmények nem a partíciós kulcs vagy a sor kulcsa szerint vannak rendezve. A szolgáltatások közötti különbségek részletes listáját a [Azure Cosmos db és az Azure Table storage Table API közötti különbségek](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)című részben tekintheti meg.

A Table Storage-ban található kulcsok karakterlánc-értékek. Annak érdekében, hogy a numerikus értékek megfelelően rendezve legyenek, konvertálja őket rögzített hosszba, és a nulla értékkel kell ellátni őket. Ha például a `RowKey`ként használt alkalmazott-azonosító érték egész érték, akkor a **123** -as alkalmazott azonosítóját a **00000123**-re kell konvertálnia. 

Számos alkalmazás követelményei eltérőek rendezett adatok használata: az alkalmazottak például rendezés, neve, vagy dátum csatlakoztatásával. A (z) szakasz [táblázatának kialakítási mintái](#table-design-patterns) a következő mintákat ismertetik az entitások más rendezési rendeléseivel kapcsolatban:  

* [Partíción belüli másodlagos index minta](#intra-partition-secondary-index-pattern): az egyes entitások több példányának tárolása különböző `RowKey` értékek használatával (ugyanabban a partícióban). Ez lehetővé teszi a gyors és hatékony kereséseket, valamint a különböző `RowKey` értékek használatával történő más rendezési sorrendek használatát.  
* [Partíciók közötti másodlagos index minta](#inter-partition-secondary-index-pattern): az egyes entitások több példányának tárolása különböző `RowKey`-értékekkel különálló partíciókban külön táblákban. Ez lehetővé teszi a gyors és hatékony kereséseket, valamint a különböző `RowKey` értékek használatával történő más rendezési sorrendek használatát.
* [Napló hátsó mintája](#log-tail-pattern): a partícióhoz legutóbb hozzáadott *n* entitások lekérése egy olyan `RowKey` értékkel, amely fordított dátum és idő sorrendbe rendezi.  

## <a name="design-for-data-modification"></a>Tervezés adatmódosításhoz
Ez a szakasz a Beszúrás, frissítés, optimalizálásával kapcsolatos kialakítási szempontok koncentrál, és törli. Bizonyos esetekben ki kell értékelnie a kiértékelést olyan kialakítások között, amelyek optimalizálják az adatmódosításra optimalizált formatervezési minták lekérdezését. Ez a kiértékelés hasonló a viszonyítási adatbázisok kialakításához (bár a tervezési kompromisszumok kezelésének módszerei eltérőek a kapcsolódó adatbázisokban). A [tábla kialakítási mintái](#table-design-patterns) a táblázatos tárolással kapcsolatos részletes tervezési mintákat ismertetik, és kiemelik a jelen kompromisszumok némelyikét. A gyakorlatban azt tapasztalja, hogy számos, az entitások lekérdezésére optimalizált terv is jól működik az entitások módosításához.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>A beszúrási, frissítési és törlési műveletek teljesítményének optimalizálása
Az entitások frissítéséhez vagy törléséhez a `PartitionKey` és `RowKey` értékekkel kell tudnia azonosítani azt. Ebben a tekintetben az entitások módosítására kiválasztott `PartitionKey` és `RowKey` a pontok lekérdezésének támogatásához hasonló feltételeket kell követnie. Az entitásokat a lehető leghatékonyabb módon szeretné azonosítani. Nem érdemes nem hatékony partíciót vagy táblázatot keresni, hogy megkeresse az entitást, hogy felderítse a `PartitionKey` és `RowKey` értékeket, amelyeket frissíteni vagy törölni kell.  

A következő minták szerepelnek a [tábla kialakítási mintái](#table-design-patterns) című szakaszban, amely optimalizálja az INSERT, Update és DELETE műveletek teljesítményét:  

* [Nagy mennyiségű törlési minta](#high-volume-delete-pattern): engedélyezze a nagy mennyiségű entitás törlését úgy, hogy az összes entitást egyidejű törlésre tárolja a saját külön táblájában. Az entitásokat a tábla törlésével törölheti.  
* [Adatsorozat-minta](#data-series-pattern): a teljes adatsorozatok tárolása egyetlen entitásban, az Ön által végzett kérelmek számának csökkentése érdekében.  
* [Széles körű entitások minta](#wide-entities-pattern): több fizikai entitás használata a több mint 252 tulajdonságú logikai entitások tárolására.  
* [Nagyméretű entitások minta](#large-entities-pattern): a blob Storage használata nagy tulajdonságértékek tárolására.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Konzisztencia biztosítása a tárolt entitásokban
A többi kulcsfontosságú tényező, amely befolyásolja a kiválasztott adatok módosítások optimalizálásához kulcsok, hogyan lehet biztosítani a konzisztenciát elemi tranzakciókat használatával. Egy EGT segítségével csak ugyanazon a partíción tárolt entitások a művelethez.  

A következő minták szerepelnek a [táblázat kialakítási mintái](#table-design-patterns) a konzisztencia kezelése című szakaszban:  

* [Partíción belüli másodlagos index minta](#intra-partition-secondary-index-pattern): az egyes entitások több példányának tárolása különböző `RowKey` értékek használatával (ugyanabban a partícióban). Ez lehetővé teszi a gyors és hatékony kereséseket, valamint a különböző `RowKey` értékek használatával történő más rendezési sorrendek használatát.  
* [Partíciók közötti másodlagos index minta](#inter-partition-secondary-index-pattern): az egyes entitások több példányának tárolása különböző `RowKey`i értékek külön partíciókban vagy külön táblákban való használatával. Ez lehetővé teszi a gyors és hatékony kereséseket, valamint a különböző `RowKey` értékek használatával történő más rendezési sorrendek használatát.  
* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern): az Azure Queues használatával a partíciós határok vagy a tárolási rendszerek határain belül végül konzisztens viselkedést engedélyezhet.
* [Entitások indexelése minta](#index-entities-pattern): az index entitások kezelése az entitások listáját visszaadó hatékony keresések engedélyezéséhez.  
* [Denormalizálás minta](#denormalization-pattern): a kapcsolódó adategységek összevonása egyetlen entitásban, hogy lehetővé váljon az összes szükséges adat lekérése egyetlen pont lekérdezéssel.  
* [Adatsorozat-minta](#data-series-pattern): a teljes adatsorozatok tárolása egyetlen entitásban, az Ön által végzett kérelmek számának csökkentése érdekében.  

További információ: entitás- [csoport tranzakciói](#entity-group-transactions) a cikk későbbi részében.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Győződjön meg arról, hogy a hatékony módosítások lehetővé teszik a tervezést a hatékony lekérdezésekhez
Sok esetben egy hatékony módosításokat, de hatékony lekérdezési eredmények kialakítása kell mindig mérlegelje, hogy ez a helyzet az adott forgatókönyvhöz. A szakasz [tábla kialakítási mintáinak](#table-design-patterns) néhány mintája explicit módon értékeli ki az entitások lekérdezése és módosítása közötti kompromisszumokat, és mindig vegye figyelembe az egyes típusú műveletek számát.  

A (z) szakasz [táblázatának kialakítási mintái](#table-design-patterns) a következő mintákat ismertetik a hatékony lekérdezések tervezése és a hatékony adatmódosítás kialakítása között:  

* [Összetett kulcs mintája](#compound-key-pattern): összetett `RowKey` értékek használatával lehetővé teheti, hogy az ügyfél egyetlen pont lekérdezéssel keressen össze kapcsolódó adatokkal.  
* [Napló hátsó mintája](#log-tail-pattern): a partícióhoz legutóbb hozzáadott *n* entitások lekérése egy olyan `RowKey` értékkel, amely fordított dátum és idő sorrendbe rendezi.  

## <a name="encrypt-table-data"></a>Tábla adatai titkosítása
A .NET Azure Storage ügyféloldali kódtára támogatja a karakterlánc-entitások tulajdonságainak az INSERT és a Replace műveletekhez való titkosítását. A titkosított karakterláncok a szolgáltatásban bináris tulajdonságokként tárolódnak, és a visszafejtés után vissza lesznek konvertálva karakterlánccá.    

Felhasználók táblákat, a titkosítási szabályzat mellett titkosítását a tulajdonságokat kell megadnia. Adjon meg egy `EncryptProperty` attribútumot (a `TableEntity`ból származtatott POCO-entitások esetében), vagy adjon meg egy titkosítási feloldót a kérelmek beállításai között. A titkosítási feloldó egy olyan delegált, amely egy partíciós kulcsot, egy sor kulcsot és egy tulajdonság nevét veszi át, és egy olyan logikai értéket ad vissza, amely jelzi, hogy a tulajdonságot titkosítani kell-e. A titkosítás során az ügyféloldali kódtár ezt az információt használja annak eldöntéséhez, hogy egy tulajdonságot titkosítani kell-e a drótba való írás során. A delegált körül hogyan tulajdonságok vannak titkosítva logikai lehetőségét is biztosít. (Ha például X, akkor titkosítsa az A tulajdonságot, máskülönben az A és B tulajdonságokat titkosítja.) Az entitások olvasása vagy lekérdezése során nem szükséges megadni ezeket az információkat.

Az egyesítés jelenleg nem támogatott. Mivel a tulajdonságok egy részhalmaza korábban egy másik kulccsal lett titkosítva, egyszerűen az új tulajdonságok egyesítése és a metaadatok frissítése adatvesztést eredményezhet. Az egyesítéshez szükség van további szolgáltatási hívásokra, hogy beolvassa a szolgáltatásból a már meglévő entitást, vagy egy tulajdonságot egy új kulcs használatával. Ezek egyike sem alkalmas a teljesítményre.     

További információ a táblák adatainak titkosításáról: [ügyféloldali titkosítás és Azure Key Vault Microsoft Azure Storagehoz](../storage/common/storage-client-side-encryption.md).  

## <a name="model-relationships"></a>Modell kapcsolatai
Tartomány modellek készítése Ez a komplex rendszerek kialakítását legfontosabb lépés. Általában a modellezési folyamattal azonosíthatók az entitások és a köztük fennálló kapcsolatok, így megismerhetik az üzleti tartományt, és tájékoztatják a rendszer kialakításáról. Ez a szakasz azt ismerteti, hogyan lehet lefordítani a tartományi modellekben található általános kapcsolattípus-típusokat a Table Storage kialakításához. A logikai adatmodellből fizikai NoSQL-alapú adatmodellre való hozzárendelési folyamat eltér a kapcsolatok adatbázisának tervezésekor használttól. A viszonyítási adatbázisok kialakítása általában feltételezi, hogy a redundancia minimalizálása érdekében optimalizált adatok normalizálása folyamatban van. Az ilyen kialakítás azt is feltételezi, hogy a deklaratív lekérdezési képesség az adatbázis működésének megvalósítását absztrakt módon mutatja be.  

### <a name="one-to-many-relationships"></a>-A-többhöz kapcsolatok
Üzleti tartományi objektumok közötti egy-a-többhöz kapcsolatok gyakran előfordulnak: például egy részleg sok alkalmazott. Több módon is megvalósítható egy-a-többhöz kapcsolat a Table Storage-ban, amelyek mindegyike az adott forgatókönyvhöz kapcsolódó előnyeit és hátrányait is tartalmazza.  

Vegyünk példaként egy nagyméretű multinacionális vállalatot, amely több tízezer szervezeti egységgel és alkalmazotti egységgel rendelkezik. Minden részlegnek számos alkalmazottja van, és mindegyik alkalmazott egy adott részleghez van társítva. Az egyik módszer a különálló részleg és az alkalmazott entitások tárolása, például a következők:  

![Részleg entitást és alkalmazotti entitást bemutató ábra][1]

Ez a példa egy implicit, egy-a-többhöz kapcsolatot mutat be a típusok között a `PartitionKey` érték alapján. Bármelyik részleg lehet sok alkalmazott.  

Ez a példa bemutatja egy szervezeti egység és a kapcsolódó alkalmazott entitásokat is ugyanazon a partíción. Dönthet úgy, hogy különböző partíciókat, táblákat vagy akár tárolási fiókokat is használhat a különböző entitások típusaihoz.  

Egy másik módszer az adatai denormalizálása, és csak a denormalizált részleg adataival rendelkező alkalmazotti entitások tárolása, ahogy az az alábbi példában is látható. Ebben a konkrét esetben ez a denormalizált megközelítés nem a legjobb, ha olyan követelmény, hogy módosítani tudja a Department Manager részleteit. Ehhez frissítenie kell a részleg összes alkalmazottját.  

![Alkalmazotti entitás ábrája][2]

További információ: a [denormalizálás mintája](#denormalization-pattern) az útmutató későbbi részében.  

Az alábbi táblázat összefoglalja az alkalmazotti és részlegi entitások egy-a-többhöz kapcsolattal rendelkező tárolási módszereinek előnyeit és hátrányait. Azt is érdemes figyelembe venni, hogy milyen gyakran várható a különböző műveletek végrehajtása. Lehetséges, hogy olyan kialakítással rendelkezik, amely költséges műveletet tartalmaz, ha a művelet csak ritkán fordul elő.  

<table>
<tr>
<th>A módszer</th>
<th>Szakemberek számára</th>
<th>Hátrányok</th>
</tr>
<tr>
<td>Külön entitástípusok, ugyanazon a partíción, ugyanazon a táblán</td>
<td>
<ul>
<li>A részleg entitás egyetlen művelettel frissítheti.</li>
<li>Egy EGT használatával fenntartani a konzisztenciát, ha van egy szervezeti egység módosítása követelmény, amikor Ön frissítési, beszúrási vagy törlési alkalmazott entitás. Ha például egy részlegszintű alkalmazottak száma az egyes részlegek megmaradjanak.</li>
</ul>
</td>
<td>
<ul>
<li>Előfordulhat, hogy egy alkalmazott és egy részleg entitást is le kell kérnie néhány ügyfél-tevékenységhez.</li>
<li>Tárolási műveletekre kerül sor, ugyanazon a partíción. A nagy tranzakciós kötetek esetében ez egy hotspotot eredményezhet.</li>
<li>Egy alkalmazott nem helyezhető át egy új részlegbe egy EGT használatával.</li>
</ul>
</td>
</tr>
<tr>
<td>Külön entitástípusok, a különböző partíciók vagy táblák vagy a storage-fiókok</td>
<td>
<ul>
<li>Egyetlen művelettel frissítheti egy részleg vagy alkalmazotti entitásokból.</li>
<li>A nagy tranzakciós kötetek esetében ez segíthet a terhelés több partíción való elosztásában.</li>
</ul>
</td>
<td>
<ul>
<li>Előfordulhat, hogy egy alkalmazott és egy részleg entitást is le kell kérnie néhány ügyfél-tevékenységhez.</li>
<li>A EGTs nem használható a konzisztencia fenntartására egy alkalmazott frissítésekor/behelyezése/törlése és a részleg frissítése során. Például frissítése folyamatban van egy szervezeti egységben az alkalmazottak száma.</li>
<li>Egy alkalmazott nem helyezhető át egy új részlegbe egy EGT használatával.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalizálja az egyetlen entitás típusa</td>
<td>
<ul>
<li>Egyetlen kérelem szükséges összes információt kérheti le.</li>
</ul>
</td>
<td>
<ul>
<li>Költséges lehet a konzisztencia fenntartása, ha frissítenie kell a részleg adatait (ez a részleg összes alkalmazottjának frissítését igényli).</li>
</ul>
</td>
</tr>
</table>

A lehetőségek közül választhat, és az előnyeit és hátrányait a legjelentősebb mértékben az adott alkalmazási helyzettől függ. Például milyen gyakran módosítja a részleg entitásait? Az alkalmazotti lekérdezéseknek szüksége van a további részlegi információkra? Hogyan közelíti meg a partíciók skálázhatósági korlátait vagy a Storage-fiókját?  

### <a name="one-to-one-relationships"></a>-Az-egyhez kapcsolat
A tartományi modellek tartalmazhatnak egy-az-egyhez kapcsolatot az entitások között. Ha egy-az-egyhez kapcsolatot kell létrehoznia a Table Storage-ban, akkor azt is ki kell választania, hogyan csatolja a két kapcsolódó entitást, ha mindkettőt le kell kérnie. Ez a hivatkozás implicit lehet a kulcs értékeinek konvenciója alapján, vagy explicit módon, ha a hivatkozást `PartitionKey` és `RowKey` értékekkel együtt tárolja az egyes entitások kapcsolódó entitásához. Ha meg szeretné tudni, hogy a kapcsolódó entitásokat ugyanabban a partícióban kell tárolnia, tekintse meg az [egy-a-többhöz kapcsolatok](#one-to-many-relationships)című szakaszt.  

Léteznek olyan implementációs megfontolások is, amelyek egy-az-egyhez kapcsolatok megvalósítására vezethetnek a Table Storage-ban:  

* Nagyméretű entitások kezeléséhez (További információ: [nagyméretű entitások mintája](#large-entities-pattern)).  
* Hozzáférés-vezérlések implementálása (További információ: [hozzáférés szabályozása közös hozzáférési aláírásokkal](#control-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Csatlakozás az ügyfél
Bár a Table Storage-ban több mód is van a kapcsolatok modellezésére, ne feledje, hogy a Table Storage használatának két fő oka a méretezhetőség és a teljesítmény. Ha úgy találja, hogy számos olyan kapcsolatot modellez, amely veszélyezteti a megoldás teljesítményét és méretezhetőségét, érdemes megkérdezni, hogy szükség van-e az összes adatkapcsolat létrehozására a tábla kialakításában. Lehet, hogy leegyszerűsíti a kialakítást, és javítja a megoldás méretezhetőségét és teljesítményét, ha lehetővé teszi, hogy az ügyfélalkalmazás bármilyen szükséges illesztést végezzen.  

Ha például olyan kis táblákat használ, amelyek nem változnak gyakran előforduló, akkor egyszer lekérheti ezeket az adatfájlokat, és gyorsítótárazhatja azt az ügyfélen. Ez elkerülhető, ismétlődő életű könyvtárgyorsítótárból ugyanazokat az adatokat lekérdezni. A jelen Útmutatóban bemutatott példákban a kisméretű szervezetek készlete valószínűleg kicsi, és ritkán változik. Ez jó választást tesz lehetővé, hogy az ügyfélalkalmazás egyszer letöltse a keresési adatként, és gyorsítótárazza azokat.  

### <a name="inheritance-relationships"></a>Öröklés kapcsolatok
Ha az ügyfélalkalmazás olyan osztályok készletét használja, amelyek egy öröklési kapcsolat részét képezik az üzleti entitások képviseletére, egyszerűen megtarthatja ezeket az entitásokat a Table Storage-ban. Előfordulhat például, hogy a következő osztályok vannak definiálva az ügyfélalkalmazás számára, ahol a `Person` absztrakt osztály.

![Öröklési kapcsolatok diagramja][3]

A Table Storage két konkrét osztályának példányait egyetlen `Person` tábla használatával is megtarthatja. Használjon az alábbihoz hasonló entitásokat:  

![Az ügyfél entitását és az alkalmazotti entitást bemutató ábra][4]

További információ az ügyfél kódjában található több entitás típusának használatáról: [heterogén entitások típusainak](#work-with-heterogeneous-entity-types) használata az útmutató későbbi részében. Ez példákat az Ügyfélkód entitástípus felismerése.  

## <a name="table-design-patterns"></a>Táblatervezési minták
Az előző szakaszban megtanulta, hogyan optimalizálhatja a tábla kialakítását az entitások adatainak lekérdezésekkel való beolvasásához, illetve az entitások adatainak beszúrásához, frissítéséhez és törléséhez. Ez a szakasz a Table Storage-hoz való használatra alkalmas mintákat ismerteti. Emellett azt is megtudhatja, hogyan lehet gyakorlatilag az útmutató korábbi részében ismertetett problémákat és kompromisszumokat kezelni. A következő ábra a különböző minták közötti kapcsolatokat összegzi:  

![A tábla kialakítási mintáinak ábrája][5]

A minta Térkép kiemeli az útmutatóban ismertetett minták (kék) és a (narancssárga) mintázatok közötti kapcsolatokat. Természetesen léteznek számos más mintákat, amelyek mellett szóló érvek. A Table Storage egyik fő forgatókönyve például az, hogy a [parancs lekérdezési felelősségének elkülönítési](https://msdn.microsoft.com/library/azure/jj554200.aspx) mintája alapján az [anyagilag megadott nézet mintát](https://msdn.microsoft.com/library/azure/dn589782.aspx) használja.  

### <a name="intra-partition-secondary-index-pattern"></a>Intra-partition másodlagos index minta
Az egyes entitások több példányának tárolása különböző `RowKey` értékek használatával (ugyanabban a partícióban). Ez lehetővé teszi a gyors és hatékony kereséseket, valamint a különböző `RowKey` értékek használatával történő más rendezési sorrendek használatát. A másolatok közötti frissítések konzisztensek lehetnek a EGTs használatával.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A Table Storage automatikusan indexeli az entitásokat a `PartitionKey` és `RowKey` értékekkel. Ez lehetővé teszi, hogy az ügyfélalkalmazás hatékonyan lekérje az entitásokat ezeknek az értékeknek a használatával. Például a következő táblázat struktúrájának használatával az ügyfélalkalmazás egy pont lekérdezéssel kérheti le az egyes alkalmazotti entitásokat a részleg neve és az alkalmazott azonosítója (a `PartitionKey` és a `RowKey` érték) használatával. Az ügyfelek az egyes részlegeken belül az alkalmazotti azonosító szerint rendezett entitásokat is lekérhetik.

![Alkalmazotti entitás ábrája][6]

Ha egy másik tulajdonság (például az e-mail-cím) alapján egy alkalmazotti entitást is szeretne találni, akkor az egyezés megtalálásához kevésbé hatékony partíciós vizsgálatot kell használnia. Ennek az az oka, hogy a Table Storage nem biztosít másodlagos indexeket. Emellett nincs lehetőség arra, hogy az alkalmazottak listáját a `RowKey` sorrend szerint eltérő sorrendbe sorolja.  

#### <a name="solution"></a>Megoldás
A másodlagos indexek hiányának megkerülése érdekében az egyes entitások több példányát is tárolhatja, az egyes másolatokat pedig egy másik `RowKey` érték használatával. Ha egy entitást a következő struktúrákkal tárol, akkor az e-mail-cím vagy az alkalmazott azonosítója alapján hatékonyan lekérheti az alkalmazotti entitásokat. A `RowKey`, `empid_`és `email_` előtag-értékei lehetővé teszik egy adott alkalmazott vagy egy adott tartomány egy csoportjának lekérdezését az e-mail-címek vagy az alkalmazottak azonosítóinak használatával.  

![Az alkalmazotti entitást különböző RowKey értékekkel ábrázoló ábra][7]

A következő két szűrési feltétel (az alkalmazotti azonosító alapján felkeresett és egy e-mail-cím alapján felkeresett) a pontok lekérdezéseit is megadja:  

* $filter = (PartitionKey eq "Értékesítés") és (RowKey eq "empid_000223")  
* $filter = (PartitionKey EQ "Sales") és (RowKey EQ "email_jonesj@contoso.com")  

Ha az alkalmazotti entitások egy tartományát kérdezi le, megadhatja az alkalmazotti azonosító sorrendjét, illetve az e-mail-címek sorrendje szerint rendezett tartományt. A megfelelő előtaggal rendelkező entitások lekérdezése a `RowKey`ban.  

* Az értékesítési részleg összes alkalmazottjának a 000100 – 000199 tartományba tartozó alkalmazotti AZONOSÍTÓjának megkereséséhez használja a következőt: $filter = (PartitionKey EQ ' Sales ') és (RowKey GE ' empid_000100 ') és (RowKey le ' empid_000199 ')  
* Az értékesítési részleg összes alkalmazottjának az "a" betűvel kezdődő e-mail-címmel történő megkereséséhez használja a következőt: $filter = (PartitionKey EQ "Sales") és (RowKey GE "email_a") és (RowKey lt "email_b")  
  
Az előző példákban használt szűrési szintaxis a Table Storage REST API. További információ: [lekérdezési entitások](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A Table Storage szolgáltatás viszonylag olcsó a használathoz, így a duplikált adatokat tároló költségek terhelése nem lehet jelentős. A várható tárolási követelmények alapján azonban mindig értékelnie kell a terv költségeit, és csak ismétlődő entitásokat kell hozzáadnia az ügyfélalkalmazás által futtatott lekérdezések támogatásához.  
* Mivel a másodlagos index entitások ugyanabban a partícióban vannak tárolva, mint az eredeti entitások, ügyeljen arra, hogy ne lépje túl az egyes partíciók méretezhetőségi célkitűzéseit.  
* Beállíthatja, hogy az ismétlődő entitások egymással konzisztenssé szolgáltatásfrissítést frissíteni az entitás két példányban EGTs használatával. Ez azt jelenti, hogy egy entitás összes példányát tárolja egyazon partícióra kerüljenek. További információ: [Entity Transactions-tranzakciók használata](#entity-group-transactions).  
* A `RowKey` használt értéknek egyedinek kell lennie az egyes entitásokhoz. Fontolja meg az összetett kulcs értékeinek használatával.  
* A `RowKey`ban szereplő numerikus értékek (például a 000223-es alkalmazott azonosítója) lehetővé teszik a megfelelő rendezést és szűrést a felső és alsó korlátok alapján.  
* Nem feltétlenül szükséges az entitás összes tulajdonságának duplikálása. Ha például az entitásokat a `RowKey` e-mail-címének használatával megkereső lekérdezések soha nem szükségesek az alkalmazott korához, akkor ezek az entitások a következő struktúrával rendelkezhetnek:

  ![Alkalmazotti entitás ábrája][8]

* Általában jobb az ismétlődő adattárolás, és gondoskodni arról, hogy egyetlen lekérdezéssel kérdezze le az összes szükséges adatkérést, mint ha egyetlen lekérdezés használatával keres egy entitást, és egy másikat a szükséges értékek megkereséséhez.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Használja ezt a mintát, ha:

- Az ügyfélalkalmazás számos különböző kulccsal kell lekérnie az entitásokat.
- Az ügyfélnek különböző rendezési sorrendbe kell beolvasnia az entitásokat.
- Az egyes entitásokat számos egyedi érték használatával azonosíthatja.

Azonban ügyeljen arra, hogy ne lépje túl a particionálási korlátokat, ha a különböző `RowKey` értékek használatával végzi az entitások keresését.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Partíciók közötti másodlagos index mintája](#inter-partition-secondary-index-pattern)
* [Összetett kulcs mintája](#compound-key-pattern)
* [Entitás-csoport tranzakciói](#entity-group-transactions)
* [Heterogén entitások típusának használata](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Közötti partíció másodlagos indexe mintája
Az egyes entitások több példányának tárolása különböző `RowKey`i értékek külön partíciókban vagy külön táblákban való használatával. Ez lehetővé teszi a gyors és hatékony kereséseket, valamint a különböző `RowKey` értékek használatával történő más rendezési sorrendek használatát.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A Table Storage automatikusan indexeli az entitásokat a `PartitionKey` és `RowKey` értékekkel. Ez lehetővé teszi, hogy az ügyfélalkalmazás hatékonyan lekérje az entitásokat ezeknek az értékeknek a használatával. Például a következő táblázat struktúrájának használatával az ügyfélalkalmazás egy pont lekérdezéssel kérheti le az egyes alkalmazotti entitásokat a részleg neve és az alkalmazott azonosítója (a `PartitionKey` és a `RowKey` érték) használatával. Az ügyfelek az egyes részlegeken belül az alkalmazotti azonosító szerint rendezett entitásokat is lekérhetik.  

![Alkalmazotti entitás ábrája][9]

Ha szeretné is tudja megtalálni egy alkalmazott entitás, például az e-mail-címét, egy másik tulajdonságának értéke alapján való kevésbé hatékony partíció vizsgálatot kell használnia. Ennek az az oka, hogy a Table Storage nem biztosít másodlagos indexeket. Emellett nincs lehetőség arra, hogy az alkalmazottak listáját a `RowKey` sorrend szerint eltérő sorrendbe sorolja.  

Nagy mennyiségű tranzakciót számít fel ezen entitások ellen, és csökkenteni szeretné az ügyfelet korlátozó Table Storage-díj kockázatát.  

#### <a name="solution"></a>Megoldás
A másodlagos indexek hiányának megkerülése érdekében az egyes entitások több példányát is tárolhatja, minden egyes példánnyal különböző `PartitionKey` és `RowKey` értékeket használhat. Ha egy entitást a következő struktúrákkal tárol, akkor az e-mail-cím vagy az alkalmazott azonosítója alapján hatékonyan lekérheti az alkalmazotti entitásokat. `PartitionKey`, `empid_`és `email_` előtag-értékei lehetővé teszik annak azonosítását, hogy melyik indexet szeretné használni a lekérdezésekhez.  

![Az elsődleges indextel és a másodlagos indexszel rendelkező alkalmazott entitással rendelkező alkalmazotti entitást ábrázoló ábra][10]

A következő két szűrési feltétel (az alkalmazotti azonosító alapján felkeresett és egy e-mail-cím alapján felkeresett) a pontok lekérdezéseit is megadja:  

* $filter = (PartitionKey eq ' empid_Sales") és (RowKey eq"000223")
* $filter = (PartitionKey EQ "email_Sales") és (RowKey EQ "jonesj@contoso.com")  

Ha az alkalmazotti entitások egy tartományát kérdezi le, megadhatja az alkalmazotti azonosító sorrendjét, illetve az e-mail-címek sorrendje szerint rendezett tartományt. A megfelelő előtaggal rendelkező entitások lekérdezése a `RowKey`ban.  

* Az értékesítési részleg összes alkalmazottjának a **000100** – **000199**tartományba tartozó alkalmazotti azonosítójának megkereséséhez, az alkalmazotti azonosító sorrendbe rendezéséhez használja a következőt: $Filter = (PartitionKey EQ "empid_Sales") és (RowKey GE "000100") és (RowKey le "000199")  
* Az értékesítési részleg összes alkalmazottja számára egy e-mail-címmel ellátott e-mail-cím megkeresése, amely az e-mail-címek sorrendjét használja: $filter = (PartitionKey EQ "email_Sales") és (RowKey GE "a") és (RowKey lt "b")  

Vegye figyelembe, hogy az előző példákban használt szűrési szintaxis a Table Storage REST API. További információ: [lekérdezési entitások](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Megtarthatja, hogy az ismétlődő entitások végül konzisztensek legyenek egymással, ha a [végül konzisztens tranzakciós mintát](#eventually-consistent-transactions-pattern) használják az elsődleges és a másodlagos index entitások karbantartásához.  
* A Table Storage szolgáltatás viszonylag olcsó a használathoz, így a duplikált adatokat tároló költségek terhelése nem lehet jelentős aggodalom. Azonban mindig értékelje a terv költségeit a várható tárolási követelmények alapján, és csak duplikált entitásokat adjon hozzá az ügyfélalkalmazás által futtatott lekérdezések támogatásához.  
* A `RowKey` használt értéknek egyedinek kell lennie az egyes entitásokhoz. Fontolja meg az összetett kulcs értékeinek használatával.  
* A `RowKey`ban szereplő numerikus értékek (például a 000223-es alkalmazott azonosítója) lehetővé teszik a megfelelő rendezést és szűrést a felső és alsó korlátok alapján.  
* Nem feltétlenül szükséges az entitás összes tulajdonságának duplikálása. Ha például az entitásokat a `RowKey` e-mail-címének használatával megkereső lekérdezések soha nem szükségesek az alkalmazott korához, akkor ezek az entitások a következő struktúrával rendelkezhetnek:
  
  ![A másodlagos indexszel rendelkező alkalmazotti entitást bemutató ábra][11]
* Általában jobb, ha ismétlődő adattárolást végez, és biztosítja, hogy egyetlen lekérdezéssel kérdezze le az összes szükséges adatfájlt, mint ha egy lekérdezést használ egy entitás megkereséséhez a másodlagos index használatával, egy másikat pedig az elsődleges indexben található szükséges értékek megkereséséhez.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Használja ezt a mintát, ha:

- Az ügyfélalkalmazás számos különböző kulccsal kell lekérnie az entitásokat.
- Az ügyfélnek különböző rendezési sorrendbe kell beolvasnia az entitásokat.
- Az egyes entitásokat számos egyedi érték használatával azonosíthatja.

Akkor használja ezt a mintát, ha el kívánja kerülni a partíciók méretezhetőségi korlátjának túllépését, ha a különböző `RowKey` értékekkel hajtja végre az entitások keresését.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern)  
* [Partíción belüli másodlagos index minta](#intra-partition-secondary-index-pattern)  
* [Összetett kulcs mintája](#compound-key-pattern)  
* [Entitás-csoport tranzakciói](#entity-group-transactions)  
* [Heterogén entitások típusának használata](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Végül konzisztens tranzakció mintája
Engedélyezze a végül konzisztens viselkedés partícióhatárok vagy a tárolási rendszer határok között az Azure-üzenetsorok használatával.  

#### <a name="context-and-problem"></a>Kontextus és probléma
EGTs elemi tranzakciókat engedélyezése több ugyanazzal a partíciókulccsal rendelkező entitások között. A teljesítményre és méretezhetőségre vonatkozó okok miatt dönthet úgy, hogy külön partíciókban vagy különálló tárolási rendszeren tárolja a konzisztencia-követelményekkel rendelkező entitásokat. Ilyen esetben a EGTs nem használható a konzisztencia fenntartásához. Előfordulhat például, hogy a követelmény, hogy a végleges konzisztencia között karbantartása:  

* Két különböző partíciók ugyanabban a táblában, a különböző táblák vagy a különböző storage-fiókban tárolt entitások.  
* A Table Storage-ban és a blob Storage-ban tárolt blobokban tárolt entitások.  
* A Table Storage szolgáltatásban és a fájlrendszerben található fájlokban tárolt entitások.  
* A Table Storage-ban tárolt entitás, amely még az Azure Cognitive Search használatával van indexelve.  

#### <a name="solution"></a>Megoldás
Az Azure-üzenetsorok használatával valósítható meg olyan megoldás, amely a végső konzisztenciát biztosít a két partíció és tárolórendszerek között.

Ennek a megközelítésnek a szemléltetéséhez feltételezhető, hogy a korábbi alkalmazotti entitások archiválására van szükség. A korábban alkalmazott entitásokat ritkán kérdezik le, és ki kell zárni azokat a tevékenységeket, amelyek az aktuális alkalmazottakkal foglalkoznak. Ennek a követelménynek a megvalósításához az **aktuális** táblában és a korábbi alkalmazottaknál az **archív** táblában tárolja az aktív alkalmazottakat. Az alkalmazottak archiválásához törölnie kell az entitást az **aktuális** táblából, és hozzá kell adnia az entitást az **archív** táblához.

Azonban nem használhat EGT a két művelet végrehajtásához. Annak elkerülése érdekében, hogy egy hiba miatt az entitások jelennek meg mindkét vagy sem táblában, az archiválási művelet végül konzisztens kell lennie. Az alábbi feladatütemezési ábrán ez a művelet lépéseit ismerteti.  

![Végleges konzisztencia megoldás diagramja][12]

Az ügyfél az archiválási műveletet úgy indítja el, hogy egy üzenetet helyez el egy Azure-várólistán (ebben a példában az Employee #456 archiválásához). Feldolgozói szerepkör az üzenetsorról új üzenetek; Ha talál egyet, kiolvassa az üzenetet, és egy rejtett másolási elhagyja az üzenetsorban. A következő feldolgozói szerepkör beolvassa az entitás egy másolatát az **aktuális** táblából, beszúr egy másolatot az **archív** táblába, majd törli az eredetit az **aktuális** táblából. Végül ha nincsenek hibák az előző lépésekből származó, a feldolgozói szerepkör törli a rejtett üzenetet az üzenetsorból.  

Ebben a példában a diagram 4. lépése beszúrja az alkalmazottat az **archív** táblába. Felveheti az alkalmazottat blob Storage-tárolóba vagy fájlrendszerbeli fájlba.  

#### <a name="recover-from-failures"></a>Helyreállítás hibákból
Fontos, hogy a diagram 4-5-es lépéseiben szereplő műveletek *idempotens* abban az esetben, ha a feldolgozói szerepkörnek újra kell indítania az archiválási műveletet. Ha Table Storage-t használ, a 4. lépésnél használjon "INSERT vagy replace" műveletet; az 5. lépésben a használt ügyfél-függvénytár "Törlés, ha létezik" műveletét kell használnia. Ha más tárolási rendszereket használ, a megfelelő idempotens műveletet kell használnia.  

Ha a feldolgozói szerepkör soha nem fejezi be a 6. lépést a diagramon, akkor időtúllépés után az üzenet újból megjelenik a feldolgozói szerepkörben az újrafeldolgozásra váró várólistán. A feldolgozói szerepkör megtekintheti, hogy a várólistán lévő üzenetek hányszor lettek elolvasva, és ha szükséges, akkor a vizsgálathoz egy külön várólistára küldje el a "méreg" üzenetet. A várólista-üzenetek olvasásával és a sorok számának ellenőrzésével kapcsolatos további információkért lásd: [üzenetek](https://msdn.microsoft.com/library/azure/dd179474.aspx)beolvasása.  

A Table Storage és a üzenetsor-tároló bizonyos hibái átmeneti hibák, és az ügyfélalkalmazás megfelelő újrapróbálkozási logikát is tartalmaz a kezeléséhez.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ez a megoldás nem biztosítja a tranzakciók elkülönítését. Előfordulhat például, hogy egy ügyfél beolvassa az **aktuális** és az **archív** táblát, amikor a feldolgozói szerepkör a diagram 4-5-es lépései között volt, és az adatok inkonzisztens nézete látható. Az adatok végül konzisztens lesz.  
* Győződjön meg arról, hogy az 4-5-es lépések idempotens a végleges konzisztencia biztosítása érdekében.  
* A megoldás több üzenetsort és feldolgozói szerepkörpéldányok segítségével méretezheti.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Használja ezt a mintát, ha meg szeretné garantálja a konzisztenciát entitások a különböző partíciók vagy a táblák között. Ezt a mintát kiterjesztheti, így biztosíthatja a tábla-és blob-tárolók, valamint más nem Azure Storage-adatforrások (például adatbázisok vagy fájlrendszer) közötti műveletek végleges egységességét.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Entitás-csoport tranzakciói](#entity-group-transactions)  
* [Egyesítés vagy csere](#merge-or-replace)  

> [!NOTE]
> Ha a tranzakció elkülönítése fontos a megoldás számára, érdemes lehet áttervezni a táblákat, hogy lehetővé váljon a EGTs használata.  
> 
> 

### <a name="index-entities-pattern"></a>Entitások indexelése minta
Karbantartása index entitások engedélyezése a hatékony keresést, hogy az entitások listáját adja vissza.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A Table Storage automatikusan indexeli az entitásokat a `PartitionKey` és `RowKey` értékekkel. Ez lehetővé teszi, hogy az ügyfélalkalmazás hatékonyan kérdezze le az entitásokat egy pont lekérdezés használatával. Például a következő táblázat struktúrájának használatával az ügyfélalkalmazás hatékonyan lekérheti az egyes alkalmazottak entitásokat a részleg neve és az alkalmazott azonosítója alapján (a `PartitionKey` és `RowKey`).  

![Alkalmazotti entitás ábrája][13]

Ha azt is szeretné, hogy az alkalmazott entitások listáját egy másik nem egyedi tulajdonság értéke alapján is le tudja kérni, például a vezetéknevet, akkor kevésbé hatékony partíciós vizsgálatot kell használnia. Ez a vizsgálat megkeresi a találatokat, és nem használja az indexet közvetlenül a kereséshez. Ennek az az oka, hogy a Table Storage nem biztosít másodlagos indexeket.  

#### <a name="solution"></a>Megoldás
Ha a vezetéknevet az előző entitás struktúrájával szeretné engedélyezni az utónévben, meg kell őriznie az alkalmazotti azonosítók listáját. Ha szeretné lekérni az alkalmazott entitásokat egy adott vezetéknevtel (például Jones), először meg kell keresnie a Jones-beli alkalmazottakhoz tartozó alkalmazotti azonosítók listáját a vezetéknevük alapján, majd le kell kérnie az alkalmazottak entitásait. Az alkalmazotti azonosítók listáját három fő lehetőséggel lehet tárolni:  

* BLOB Storage használata.  
* Hozzon létre index entitások az alkalmazottak entitásokként ugyanazon a partíción.  
* Index entitások létrehozása egy külön partíciót vagy tábla.  

1\. lehetőség: blob Storage használata  

Hozzon létre egy blobot minden egyedi vezetéknevhez, és mindegyik blobban tárolja a `PartitionKey` (részleg) és a `RowKey` (alkalmazotti azonosító) értékeit a vezetéknevet használó alkalmazottak számára. Ha hozzáad vagy töröl egy alkalmazottat, győződjön meg arról, hogy a megfelelő blob tartalma végül konzisztens az alkalmazott entitásokkal.  

2\. lehetőség: index entitások létrehozása ugyanabban a partícióban  

A következő adategységeket tároló indexelő entitások használata:  

![Az alkalmazott entitást bemutató ábra, amely tartalmazza az azonos vezetéknevű alkalmazottak azonosítóinak listáját.][14]

A `EmployeeIDs` tulajdonság a `RowKey`ban tárolt utolsó névvel rendelkező alkalmazottak alkalmazotti azonosítóinak listáját tartalmazza.  

Az alábbi lépések azt ismertetik, hogy milyen eljárást kell követnie, amikor új alkalmazottat ad hozzá. Ebben a példában egy 000152-es AZONOSÍTÓJÚ alkalmazottat és vezetéknevet adunk hozzá az értékesítési részleghez:  

1. Az index entitás beolvasása `PartitionKey` "Sales" értékkel és a "Jones" `RowKey` értékkel. Mentse a 2. lépésben használandó ETag címkéje ehhez az entitáshoz.  
2. Hozzon létre egy Entity Group-tranzakciót (azaz egy batch-műveletet), amely beszúrja az új alkalmazott entitást (`PartitionKey` "Sales" értéket és `RowKey` értéket "000152"), és frissíti az index entitást (`PartitionKey` "Sales" és `RowKey` "Jones" értéket). A EGT ezt úgy teszi meg, hogy hozzáadja az új alkalmazott azonosítót a listához a EmployeeIDs mezőben. További információ a EGTs: [Entity Transactions](#entity-group-transactions).  
3. Ha a EGT egy optimista egyidejűségi hiba miatt meghiúsul (azaz valaki más módosította az index entitást), akkor az 1. lépésben kell megkezdenie.  

A második lehetőség használata esetén hasonló módszert használhat az alkalmazottak törlésére. Egy alkalmazott vezetéknevének módosítása valamivel összetettebb, mert olyan EGT kell futtatnia, amely három entitást frissít: az alkalmazott entitást, a régi vezetéknevű index entitást, valamint az új vezetéknevet index entitást. A módosítások elvégzése előtt le kell kérnie az egyes entitásokat, hogy lekérje a ETag értékeit, amelyeket a frissítések optimista Egyidejűség használatával történő végrehajtásához használhat.  

A következő lépések azt a folyamatot ismertetik, amelyet követnie kell, ha az összes alkalmazottat meg kell keresnie egy adott vezetéknevű részlegben. Ebben a példában a Sales részleg összes alkalmazottját megkeresi a vezetékneve Jones néven:  

1. Az index entitás beolvasása `PartitionKey` "Sales" értékkel és a "Jones" `RowKey` értékkel.  
2. Elemezze az alkalmazotti azonosítók listáját a `EmployeeIDs` mezőben.  
3. Ha további információra van szüksége az alkalmazottakról (például az e-mail-címekről), az egyes alkalmazottak entitásokat az "értékesítés" `PartitionKey` értékkel kell lekérnie, és `RowKey` értékeket a 2. lépésben beszerzett alkalmazottak listájából.  

3\. lehetőség: index entitások létrehozása külön partícióban vagy táblázatban  

Ehhez a beállításhoz használja az indexelő entitásokat, amelyek a következő adategységeket tárolják:  

![Az alkalmazott entitást bemutató ábra, amely tartalmazza az azonos vezetéknevű alkalmazottak azonosítóinak listáját.][15]

A `EmployeeIDs` tulajdonság a `RowKey`ban tárolt utolsó névvel rendelkező alkalmazottak alkalmazotti azonosítóinak listáját tartalmazza.  

A EGTs nem használható a konzisztencia fenntartásához, mert az index entitások az alkalmazott entitások külön partíciójában vannak. Győződjön meg arról, hogy az indexelő entitások végül konzisztensek az alkalmazott entitásokkal.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ehhez a megoldáshoz legalább két lekérdezésre van szükség a megfelelő entitások lekéréséhez: az egyiket, hogy lekérdezze az index entitásokat a `RowKey` értékek listájának beolvasásához, majd lekérdezi az egyes entitásokat a listában.  
* Mivel az egyes entitások maximális mérete 1 MB, a megoldás 2. és 3. beállítása feltételezi, hogy az adott vezetéknevhez tartozó alkalmazotti azonosítók listája soha nem nagyobb, mint 1 MB. Ha az alkalmazottak azonosítóinak listája valószínűleg 1 MB-nál nagyobb méretű, használja az 1. lehetőséget, és tárolja az indexet a blob Storage-ban.  
* Ha a 2. lehetőséget használja (az EGTs használatával kezeli az alkalmazottak hozzáadását és törlését, valamint az alkalmazott vezetéknevének módosítását), akkor ki kell értékelnie, hogy a tranzakciók mennyisége megközelíti-e egy adott partíció skálázhatósági korlátait. Ebben az esetben érdemes megfontolni egy végül konzisztens megoldást (1. vagy 3. lehetőség). Ezek a várólisták segítségével kezelik a frissítési kérelmeket, és lehetővé teszik, hogy az indexelt entitásokat külön partícióban tárolja az alkalmazott entitásokból.  
* A megoldás 2. beállítása feltételezi, hogy a vezetéknevét egy részlegen belül szeretné megkeresni. Tegyük fel például, hogy az értékesítési részleg vezetékneve alapján szeretné lekérni az alkalmazottak listáját. Ha azt szeretné, hogy a teljes szervezeten belül az összes alkalmazott vezetékneve legyen Jones, használja az 1. vagy a 3. lehetőséget.
* Olyan üzenetsor-alapú megoldást is alkalmazhat, amely végleges konzisztenciát biztosít. További részletekért tekintse meg a [végül konzisztens tranzakciók mintáját](#eventually-consistent-transactions-pattern).  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha olyan entitásokat szeretne keresni, amelyek mindegyike közös tulajdonságot használ, például a vezetéknevét az utolsó nevű alkalmazottal.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcs mintája](#compound-key-pattern)  
* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern)  
* [Entitás-csoport tranzakciói](#entity-group-transactions)  
* [Heterogén entitások típusának használata](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Denormalizáció minta
Együtt ötvözze a kapcsolódó adatokat egyetlen entitás ahhoz, hogy minden hibaérzékeny pont lekérdezés szükséges adatok lekéréséhez.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A viszonyítási adatbázisban általában az adatok normalizálása történik, hogy eltávolítsa az ismétlődéseket, amikor a lekérdezések több táblából kérik le az adatok lekérdezését. Normalizálása az Azure-táblák adatait, ha gondoskodnia kell, több adatváltások az ügyfélről a kiszolgálónak a kapcsolódó adatokat. A következő táblázatos struktúra esetében például két oda-és visszaút szükséges a részleg részleteinek lekéréséhez. Az egyik út lekéri a kezelő entitást, amely tartalmazza a felettes AZONOSÍTÓját, a második pedig beolvassa a felettes adatait egy alkalmazott entitásban.  

![Részleg entitás és alkalmazott entitás ábrája][16]

#### <a name="solution"></a>Megoldás
Ahelyett, hogy az adatok tárolása a két különálló entitások, denormalizálja az adatokat, és a részleg entitás őrizze a kezelő részletek. Például:  

![A denormalizált és a kombinált részleg entitásának ábrája][17]

Az ezekkel a tulajdonságokkal rendelkező részleg entitásokkal mostantól lekérheti az adott részlegre vonatkozó összes adatot egy pont lekérdezés használatával.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Van néhány kétszer néhány adattárolás többletterhelést kapcsolódó költségek. A Table Storage-hoz kevesebb kérelemből eredő teljesítménybeli haszon általában a tárolási költségek marginális növekedését méri. Ezen túlmenően a költségeket részben ellensúlyozza a részleg részleteinek beolvasásához szükséges tranzakciók számának csökkentése.  
* A konzisztencia, a két entitás, amely a kezelők kapcsolatos adatokat kell fenntartani. A konzisztencia-problémát a EGTs használatával kezelheti egyetlen atomi tranzakcióban több entitás frissítéséhez. Ebben az esetben a részleg entitás és a Department Manager alkalmazott entitása ugyanabban a partícióban van tárolva.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha gyakran kell ellenőrizzék a kapcsolódó információkat. Ez a minta csökkenti az ügyfél biztosítania kell a szükséges adatok lekéréséhez lekérdezések száma.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcs mintája](#compound-key-pattern)  
* [Entitás-csoport tranzakciói](#entity-group-transactions)  
* [Heterogén entitások típusának használata](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Összetett kulcs minta
Az összetett `RowKey` értékek használatával lehetővé teheti, hogy az ügyfél egyetlen pont lekérdezéssel keressen kapcsolódó adatokkal.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A kapcsolati adatbázisban természetes, hogy a lekérdezésekben a kapcsolódó adatoknak az ügyfélhez való visszaküldéséhez egyetlen lekérdezésben használja a rendszer. Az alkalmazotti AZONOSÍTÓval például megkeresheti a kapcsolódó entitások listáját, amelyek az adott alkalmazott teljesítmény-és felülvizsgálati adatait tartalmazzák.  

Tegyük fel, hogy az Employee entitásokat a következő struktúra használatával tárolja a Table Storage-ban:  

![Alkalmazotti entitás ábrája][18]

Emellett a korábbi adatokat is tárolnia kell a felülvizsgálatokhoz és a teljesítményhez a munkahelye által a szervezete számára készített munkavégzéshez kapcsolódóan, és az adatokat évente kell elérni. Az egyik lehetőség, hogy hozzon létre egy másik táblát, amely tárolja az entitások az alábbi struktúra használatával:  

![Az alkalmazottak felülvizsgálati entitásának ábrája][19]

Ezzel a módszerrel az új entitásban dönthet úgy, hogy az adatokat (például utónév és vezetéknév) duplikálja, hogy lehetővé tegye az adatok egyetlen kérelemkel való lekérését. Azonban nem lehet erős konzisztenciát fenntartani, mert nem használhat EGT a két entitás atomi frissítésére.  

#### <a name="solution"></a>Megoldás
Egy új entitás típusának tárolása az eredeti táblában az alábbi struktúrával rendelkező entitások használatával:  

![Az összetett kulccsal rendelkező alkalmazott entitás ábrája][20]

Figyelje meg, hogy a `RowKey` mostantól egy összetett kulcs, amely az alkalmazotti AZONOSÍTÓból és a felülvizsgálati adatmennyiségből áll. Ez lehetővé teszi az alkalmazottak teljesítményének beolvasását és az adatellenőrzést egyetlen, egyetlen entitásra vonatkozó kéréssel.  

Az alábbi példa bemutatja, hogyan kérheti le az összes felülvizsgálati adatok (például az alkalmazottak 000123 a Sales nevű részleg) egy alkalmazott:  

$filter = (PartitionKey eq "Értékesítés") és (RowKey ge "empid_000123") és (RowKey lt "empid_000124") & $select = rowkey tulajdonságok esetén, Managerben minősítés, társ-minősítés, megjegyzések  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Használjon egy megfelelő elválasztó karaktert, amely megkönnyíti a `RowKey` érték elemzését: például **000123_2012**.  
* Ezt az entitást ugyanabban a partícióban tárolja, mint a többi olyan entitást, amely ugyanahhoz az alkalmazotthoz kapcsolódó adatforrásokat tartalmaz. Ez azt jelenti, hogy a EGTs-t használhatja az erős konzisztencia fenntartásához.
* Érdemes megfontolni, hogy milyen gyakran kérdezi le az adatlekérdezést, hogy a minta megfelelő-e. Ha például ritkábban éri el az áttekintő adatvizsgálatot, és a fő alkalmazotti adatai gyakran, külön entitásként kell megtartani őket.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha kell tárolnia, egy vagy több kapcsolódó entitások lekérdezett gyakran.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Entitás-csoport tranzakciói](#entity-group-transactions)  
* [Heterogén entitások típusának használata](#work-with-heterogeneous-entity-types)  
* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Log tail minta
A partícióhoz legutóbb hozzáadott *n* entitások beolvasása egy `RowKey` értékkel, amely fordított dátum és idő sorrendbe rendezi a sort.  

> [!NOTE]
> A Azure Cosmos DB Azure Table API által visszaadott lekérdezési eredmények nem a partíciós kulcs vagy a sor kulcsa szerint vannak rendezve. Így, míg ez a minta alkalmas a Table Storage-hoz, nem alkalmas Azure Cosmos DBra. A szolgáltatások közötti különbségek részletes listáját a [Azure Cosmos db és az Azure Table Storage Table API közötti különbségek](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)című részben tekintheti meg.

#### <a name="context-and-problem"></a>Kontextus és probléma
Általános követelmény, hogy tudja lekérni a legutóbb létrehozott entitások, például a 10 legújabb kiadás egy alkalmazott által küldött jogcímek. A táblázatos lekérdezések egy `$top` lekérdezési műveletet támogatnak, amely az első *n* entitást adja vissza egy készletből. Nincs egyenértékű lekérdezési művelet, amely egy készlet utolsó *n* entitását adja vissza.  

#### <a name="solution"></a>Megoldás
Az entitásokat egy olyan `RowKey` használatával tárolja, amely természetes módon rendezi a dátum-és idősorrendet, így a legújabb bejegyzés mindig az első a táblában.  

Például, hogy egy alkalmazott által küldött tíz legújabb kiadási jogcímek lekérni, használhatja fordított osztásjelek származik az aktuális dátum/idő érték. A következő C# mintakód az egyik módszert mutatja be egy olyan `RowKey` számára, amely a legutóbbiról a legrégebbi értékre rendezi a megfelelő "fordított kullancsok" értékét:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

A dátum/idő értékre a következő kóddal térhet vissza:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A lekérdezés a következőhöz hasonló:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A fordított osztásjelek értékét a bevezető nullákkal kell ellátni, hogy a karakterlánc-érték a várt módon legyen rendezve.  
* A skálázhatósági célokat az egy partíció szintjén tisztában kell lennie. Ügyeljen arra, hogy ne hozzon létre a gyors elérésű direktszínű partíciókat.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha fordított dátum/idő sorrendben szeretné elérni az entitásokat, vagy ha a legutóbb hozzáadott entitásokhoz kell hozzáférnie.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Előtag/Hozzáfűzés Anti-pattern](#prepend-append-anti-pattern)  
* [Entitások beolvasása](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Nagy mennyiségű delete minta
Engedélyezze a nagy mennyiségű entitás törlését úgy, hogy az összes entitást egyidejű törlésre tárolja a saját külön táblájában. Az entitásokat a tábla törlésével törölheti.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Számos alkalmazás, amely már nem kell lennie az ügyfélalkalmazások számára elérhető, vagy az alkalmazás egy másik adattárolóra rendelkezik archivált régi adatok törlése. Ezeket az adatok általában dátum alapján azonosíthatók. Tegyük fel például, hogy az összes olyan bejelentkezési kérelem rekordját törölni kell, amely több mint 60 napnál régebbi.  

Az egyik lehetséges kialakítás, hogy a bejelentkezési kérés dátumát és időpontját használja a `RowKey`ban:  

![Bejelentkezési kísérlet entitásának ábrája][21]

Ezzel a módszerrel elkerülhetők a partíciós hozzáférési pontok, mivel az alkalmazás egy külön partícióban beszúrhatja és törölheti a bejelentkezési entitásokat az egyes felhasználók számára. Ez a megközelítés azonban költséges és időigényes lehet, ha nagy számú entitással rendelkezik. Először be kell állítania egy táblázatos vizsgálatot a törölni kívánt entitások azonosításához, majd minden régi entitást törölnie kell. Adatváltások számát csökkentheti a kiszolgálóhoz, törölje a régi entitások kötegelés több delete kérelem be EGTs szükséges.  

#### <a name="solution"></a>Megoldás
Minden nap, a bejelentkezési kísérletek samostatné tabulce használja. Az előző entitás kialakításával elkerülheti, hogy az entitások beszúrásakor ne legyenek elérhetők a pontok. A régi entitások törlése mostantól csupán egy olyan kérdés, hogy naponta törölni kell egy táblázatot (egyetlen tárolási művelet), ahelyett, hogy naponta több száz és ezer egyéni bejelentkezési entitást kellene megkeresnie és törölni.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Az Ön által használt kialakítás más módon támogatja az adatokat, például bizonyos entitások keresését, más adatokkal való összekapcsolást vagy összesített információk létrehozását?  
* Nem a Tervező túlzott terhelés elkerülése új entitások beszúrásakor?  
* Késleltetés várható, ha azt szeretné, használja ugyanazt a táblanevet törlését követően. Célszerűbb mindig használja az egyedi táblák nevére.  
* Az új tábla első használatakor a Table Storage a hozzáférési mintákat is megtudhatja, és elosztja a partíciókat a csomópontok között. Érdemes megfontolni, hogy milyen gyakran kell új táblák létrehozása.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Használja ezt a mintát, ha a létrehozott entitásokat, törölnie kell egy időben nagy mennyiségű.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Entitás-csoport tranzakciói](#entity-group-transactions)
* [Entitások módosítása](#modify-entities)  

### <a name="data-series-pattern"></a>Adatsorozat adatmintát
Teljes körű sorozat Store egyetlen entitás minimalizálása érdekében, hogy kérések száma.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Gyakran előfordul az alkalmazás számára az adatok általában egyszerre lekéréséhez szükséges sorozatát tárolja a rendszer. Például az alkalmazás előfordulhat, hogy rögzítse az összes alkalmazott által minden órában hány Csevegési üzeneteket, majd ezt az információt megrajzolásához hány üzenetet minden felhasználóhoz az előző 24 óra során küldött. Lehet, hogy egy tervezési 24 entitások tárolására minden alkalmazott számára:  

![Az üzenet-stats entitás ábrája][22]

Ezzel a kialakítással egyszerűen keresse meg és frissítéséhez minden alkalmazott számára, amikor az alkalmazást az üzenetek száma érték frissíteni kell az entitás módosítására. Azonban és lekéri az információkat, a tevékenység-diagram megrajzolásához az előző 24 óra, le kell kérnie 24 entitásokat.  

#### <a name="solution"></a>Megoldás
A következő kialakítással külön tulajdonsággal tárolhatja az üzenetek darabszámát minden órában:  

![Az üzenet-stats entitást az elválasztott tulajdonságokkal ábrázoló ábra][23]

Ezzel a kialakítással az üzenetek száma egy alkalmazott frissíteni az adott órában használhatja egy merge művelet. Most lekérheti az összes szükséges információt, hogy a diagramot egyetlen entitásra vonatkozó kérelem használatával ábrázolja.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ha a teljes adatsorozat egyetlen entitáshoz sem fér hozzá (egy entitás legfeljebb 252 tulajdonsággal rendelkezhet), használjon másik adattárat, például egy blobot.  
* Ha több ügyfél is frissít egyszerre egy entitást, használja a **ETAG** az optimista Egyidejűség megvalósításához. Ha sok ügyfele van, érdemes lehet magas szintű elégedettséget tapasztalni.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Használja ezt a mintát, amikor szüksége van frissíteni, és a egy egyéni entitáshoz társított adatsor lekéréséhez.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Nagyméretű entitások mintája](#large-entities-pattern)  
* [Egyesítés vagy csere](#merge-or-replace)  
* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern) (ha az adatsorozatot egy blobban tárolja)  

### <a name="wide-entities-pattern"></a>Széles körű entitások minta
Több fizikai entitás legfeljebb 252 tulajdonságot tartalmazhat rendelkező logikai entitás használni.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Az egyes entitások legfeljebb 252 tulajdonsággal rendelkezhetnek (kivéve a kötelező rendszertulajdonságokat), és összesen legfeljebb 1 MB adatmennyiséget tárolhatnak. A relációs adatbázisokban általában a sorok méretének korlátozásai is megtalálhatók egy új tábla hozzáadásával, valamint egy 1 – 1 közötti kapcsolat kényszerítésével.  

#### <a name="solution"></a>Megoldás
A Table Storage használatával több entitást is tárolhat, amelyek több mint 252 tulajdonsággal rendelkező, egyetlen nagy üzleti objektumot képviselnek. Ha például az elmúlt 365 napban az egyes alkalmazottak által küldött IM-üzenetek számának számát szeretné tárolni, akkor a következő kialakítást használhatja, amely két, különböző sémákkal rendelkező entitást használ:  

![Az Rowkey 01 és az Rowkey 02 üzenet-stats entitást tartalmazó üzenet-stats entitás ábrázolása][24]

Ha olyan módosítást, amely mindkét entitások szinkronizálja egymással Újévi frissíteni kell az van szüksége, használhatja az EGT. Ellenkező esetben egy egyetlen merge művelet használatával frissítse az üzenetek száma egy adott napjára. Egy adott alkalmazott összes értékének lekéréséhez mindkét entitást le kell kérnie. Ezt két, `PartitionKey`t és egy `RowKey` értéket használó, hatékony kéréssel teheti meg.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósításának eldöntése során vegye figyelembe a következő pontot:  

* A kész logikai entitás beolvasása magában foglalja a legalább két tárelérési tranzakciók: egy minden fizikai entitás lekéréséhez.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha olyan entitásokat kell tárolnia, amelyek mérete vagy tulajdonságai meghaladják a Table Storage egyes entitásának korlátait.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Entitás-csoport tranzakciói](#entity-group-transactions)
* [Egyesítés vagy csere](#merge-or-replace)

### <a name="large-entities-pattern"></a>Nagy entitások minta
Nagyméretű tulajdonságértékek tárolására használjon blob Storage-t.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Az egyes entitások összesen legfeljebb 1 MB adatmennyiséget tárolhatnak. Ha a tulajdonságok közül egy vagy több olyan értéket tárol, amely az entitás teljes méretét eredményezi az adott érték túllépésekor, a teljes entitás nem tárolható a Table Storage-ban.  

#### <a name="solution"></a>Megoldás
Ha az entitás mérete meghaladja az 1 MB-ot, mert egy vagy több tulajdonság nagy mennyiségű adatmennyiséget tartalmaz, akkor a blob Storage-ban tárolhatja az adatait, majd az entitás egyik tulajdonságában tárolhatja a blob címeit. Tárolhat például egy alkalmazott fényképét a blob Storage-ban, és tárolhatja a fényképre mutató hivatkozást az alkalmazott entitás `Photo` tulajdonságában:  

![A blob Storage-ra mutató, az alkalmazotti entitást és a sztringet bemutató ábra][25]

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A táblázatos tárolóban lévő entitás és a blob Storage-beli adattárolók közötti végleges konzisztencia fenntartása érdekében a [végül konzisztens tranzakciós mintázattal](#eventually-consistent-transactions-pattern) kezelheti az entitásokat.
* Legalább két tárelérési tranzakciók teljes entitásnak beolvasása foglalja magában: az egyik beolvasni az entitást és a egy blob adatok lekéréséhez.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha olyan entitásokat kell tárolnia, amelyek mérete meghaladja a Table Storage egyes entitásának korlátait.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern)  
* [Széles entitások mintája](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Kizárási minta illesztenie hozzáfűzése
Ha nagy mennyiségű lapkákkal rendelkezik, növelje a méretezhetőséget a lapkák több partíción való elosztásával.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Előtag-beillesztés vagy entitások hozzáfűzi a tárolt entitások jellemzően az alkalmazás új entitásokat ad hozzá a partíciók egymást követő első vagy utolsó partíciót eredményez. Ebben az esetben a lapkák mindegyike adott időpontban zajlik ugyanabban a partícióban, és létrehoz egy hotspotot. Ez megakadályozza, hogy a tábla tárterülete több csomóponton helyezzen el terheléselosztási lapkákat, és lehetséges, hogy az alkalmazás megnyomja a partíció skálázhatósági céljait. Vegyünk például egy olyan alkalmazás esetét, amely naplózza az alkalmazottak hálózati és erőforrás-hozzáférését. Az entitások szerkezete, például az alábbiak miatt az aktuális óra partíciója válik elérhetővé, ha a tranzakciók mennyisége eléri az egyes partíciók skálázhatósági célját:  

![Alkalmazotti entitás ábrája][26]

#### <a name="solution"></a>Megoldás
A következő alternatív entitás-struktúra elkerüli a hotspotot egy adott partíción, mivel az alkalmazás naplózza az eseményeket:  

![Az alkalmazotti entitást bemutató ábra az év, hónap, nap, óra és eseményazonosító összetételével RowKey][27]

Figyelje meg, hogy a `PartitionKey` és az `RowKey` is összetett kulcsok. A `PartitionKey` a részleg és az alkalmazott AZONOSÍTÓját is használja a több partícióra kiterjedő naplózás elosztásához.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Támogatja az alternatív struktúra, amely hatékonyan létrehozása a Beszúrás a forró partíciók a lekérdezések, az ügyfél alkalmazás?  
* Az Ön várható tranzakciós mennyisége azt jelenti, hogy valószínűleg el fogja érni az egyes partíciók méretezhetőségi céljait, és a Table Storage szabályozza azt?  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ha a tranzakciós kötetek mennyisége valószínűleg a nagy mennyiségű partíció elérésekor a táblázatos tárterületet fogja eredményezni, kerülje a küldési/hozzáfűzési mintázatot.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcs mintája](#compound-key-pattern)  
* [Napló farok mintája](#log-tail-pattern)  
* [Entitások módosítása](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Napló adatok kizárási minta
A naplófájlok tárolásához általában blob Storage-t kell használnia a Table Storage helyett.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A naplózási adatok általános használati esete egy adott dátum-/időtartományhoz tartozó naplóbejegyzések beolvasása. Megkeresheti például az összes olyan hibát és kritikus üzenetet, amelyet az alkalmazás a 15:04 és a 15:06 között adott dátumra naplóz. Nem kívánja használni a naplófájl dátumát és időpontját, hogy meghatározza azt a partíciót, amelybe a napló entitásokat menteni kívánja. Ez a művelet egy gyors partíciót eredményez, mivel az összes napló entitás ugyanazt a `PartitionKey` értéket fogja megosztani (lásd a következőt: [fűzni/append Anti-pattern](#prepend-append-anti-pattern)). A naplófájl következő entitás-sémája például egy gyors partíciót eredményez, mivel az alkalmazás az összes naplózási üzenetet az aktuális dátumra és órára írja a partícióra:  

![A naplózási üzenet entitásának ábrája][28]

Ebben a példában a `RowKey` tartalmazza a napló üzenetének dátumát és időpontját, így biztosítva, hogy a naplóüzenetek dátum/idő sorrendben legyenek rendezve. A `RowKey` egy üzenet AZONOSÍTÓját is tartalmazza abban az esetben, ha több naplófájl is ugyanazt a dátumot és időpontot használja.  

Egy másik módszer egy olyan `PartitionKey` használata, amely gondoskodik arról, hogy az alkalmazás üzeneteket írjon a különböző partíciók között. Ha például a naplófájl forrása lehetővé teszi az üzenetek több partíción keresztüli terjesztését, használhatja a következő entitás-sémát:  

![A naplózási üzenet entitásának ábrája][29]

A séma problémája azonban az, hogy egy adott időtartomány összes naplófájljának lekéréséhez a tábla minden partícióján keresni kell.

#### <a name="solution"></a>Megoldás
Az előző szakasz rámutatott arra a problémára, hogy a Table Storage-t a naplóbejegyzések tárolására próbálja használni, és két nem megfelelő kialakítást javasolt. Az egyik megoldás egy olyan gyors partíciót eredményezett, amely gyenge teljesítményű írási napló üzeneteinek kockázatával jár. A másik megoldás rossz lekérdezési teljesítményt eredményezett, mert a táblázatban lévő összes partíció vizsgálatára vonatkozó követelmény egy adott időtartományhoz tartozó naplófájlok beolvasásához szükséges. A blob Storage jobb megoldást kínál az ilyen típusú forgatókönyvek esetében, így az Azure Storage Analytics az általa gyűjtött adatokat tárolja.  

Ez a szakasz azt ismerteti, hogyan tárolja a Storage Analytics a naplózási adattárakat a blob Storage-ban, ennek a megközelítésnek az szemlélteti, hogy az adattárolást általában a tartomány alapján kérdezi le.  

A Storage Analytics több blobban is tagolt formátumban tárolja a naplózási üzeneteket. A tagolt formátumú megkönnyíti az ügyfélalkalmazás elemzése a log üzenetben található adatokat.  

A Storage Analytics elnevezési konvenciója olyan blobokat használ, amelyekkel megkeresheti a keresett naplófájlokat tartalmazó blobot (vagy blobokat). Egy "üzenetsor/2014/07/31/1800/000001. log" nevű blob például a üzenetsor-szolgáltatáshoz kapcsolódó naplófájlokat tartalmaz, amelyek az óra 18:00-től kezdődően, 2014. július 31-ig érvényesek. A "000001" azt jelzi, hogy ez az első naplófájlja ebben az időszakban. A Storage Analytics a blob metaadatainak részeként rögzíti a fájlban tárolt első és utolsó naplózási üzenetek időbélyegét is. A blob Storage-hoz készült API lehetővé teszi, hogy megkeresse a tárolóban lévő blobokat egy név előtagja alapján. Ha meg szeretné keresni az összes olyan blobot, amely a várakozási sor naplózási adatsorát tartalmazza, a következő előtaggal 18:00: "üzenetsor/2014/07/31/1800".  

A Storage Analytics pufferek belső naplófájlba helyezi az üzeneteket, majd rendszeresen frissíti a megfelelő blobot, vagy létrehoz egy újat a naplóbejegyzések legújabb kötegével. Ez csökkenti a blob Storage-hoz szükséges írási műveletek számát.  

Ha a saját alkalmazásában hasonló megoldást valósít meg, gondolja át, hogyan kezelheti a megbízhatóság és a költséghatékonyság, valamint a méretezhetőség közötti kompromisszumot. Más szóval értékelje ki, hogy milyen hatással van az összes naplóbejegyzés a blob Storage-ba való írására az alkalmazásban lévő frissítések pufferelése és a blob Storage-ba kötegekben való írása során.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
Ha tervezi particionálni Teljesítménynapló-adatok tárolására, vegye figyelembe a következőket:  

* Ha olyan táblázatot hoz létre, amely elkerüli a lehetséges gyors partíciókat, akkor előfordulhat, hogy nem fér hozzá a napló adataihoz.  
* Az ügyfélnek Teljesítménynapló-adatok feldolgozásához, gyakran kell betölteni a sok rekordot.  
* Bár a naplózási adatként gyakran strukturált, a blob Storage lehet jobb megoldás.  

### <a name="implementation-considerations"></a>Implementálási szempontok
Ez a szakasz ismerteti a szempontot figyelembe kell vennie a a fentebbi szakaszokban leírt minták megvalósításának néhányat. Ez a szakasz a legtöbb C# nyelven írt példa, amely a Storage ügyféloldali kódtára (4.3.0 verzióban verzió idején összeállításakor) használja.  

### <a name="retrieve-entities"></a>Entitások beolvasása
Ahogy az a [lekérdezési](#design-for-querying)szakaszban is látható, a leghatékonyabb lekérdezés a pont lekérdezése. Bizonyos esetekben azonban előfordulhat, hogy több entitást kell lekérnie. Ez a szakasz néhány gyakori megközelítést ismertet az entitások a Storage ügyféloldali kódtár használatával történő beolvasásához.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Pont lekérdezésének futtatása a Storage ügyféloldali kódtár használatával
A pontok lekérdezésének legegyszerűbb módja a tábla **lekérése** művelet használata. Ahogy az a következő C# kódrészletben is látható, a művelet lekérdezi az "értékesítés" értékű `PartitionKey`t, és egy "212" értékű `RowKey`:  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Figyelje meg, hogy ez a példa azt várja, hogy a beolvasott entitás `EmployeeEntity`típusú legyen.  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Több entitás beolvasása a LINQ használatával
A LINQ és a Storage ügyféloldali kódtár használatával több entitást is lekérhet, és egy **Where** záradékkal rendelkező lekérdezést is megadhat. A táblák vizsgálatának elkerülése érdekében mindig adja meg a `PartitionKey` értéket a WHERE záradékban, és ha lehetséges, a `RowKey` értéket a tábla és a partíció vizsgálatainak elkerüléséhez. A Table Storage támogatja az összehasonlító operátorok korlátozott készletét (nagyobb, mint, nagyobb vagy egyenlő, kevesebb, mint, kisebb vagy egyenlő, egyenlő és nem egyenlő) a WHERE záradékban való használathoz. A következő C# kódrészlet megkeresi az összes olyan alkalmazottat, akinek vezetékneve "B" karakterrel kezdődik (feltéve, hogy a `RowKey` az utolsó nevet tárolja) az értékesítési részlegben (feltéve, hogy a `PartitionKey` tárolja a részleg nevét):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Figyelje meg, hogyan határozza meg a lekérdezés egy `RowKey` és egy `PartitionKey`t a jobb teljesítmény érdekében.  

Az alábbi mintakód egyenértékű funkciókat mutat be a fluent API használatával (az általános Fluent API-kkal kapcsolatos további információkért lásd: [ajánlott eljárások Fluent API tervezéséhez](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> A minta több `CombineFilters` metódust ágyaz be a három szűrési feltétel belefoglalásához.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Nagy számú entitás beolvasása egy lekérdezésből
Az optimális lekérdezés egy `PartitionKey` érték és egy `RowKey` érték alapján egyedi entitást ad vissza. Bizonyos esetekben azonban előfordulhat, hogy az azonos partícióból, vagy akár sok partícióból származó entitások visszaadására van szükség. Ilyen esetekben mindig teljes mértékben tesztelje az alkalmazás teljesítményét.  

A Table Storage-vel való lekérdezés egyszerre legfeljebb 1 000 entitást tud visszaadni, és legfeljebb öt másodpercig futhat. A Table Storage egy folytatási tokent ad vissza, amely lehetővé teszi, hogy az ügyfélalkalmazás a következő készletet kérje, ha a következők bármelyike igaz:

- Az eredményhalmaz több mint 1 000 entitást tartalmaz.
- A lekérdezés öt másodpercen belül nem fejeződött be.
- A lekérdezés átlépi a partíció határát. 

A folytatási tokenek működésével kapcsolatos további információkért lásd: [lekérdezési időkorlát és tördelés](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Ha a Storage ügyféloldali kódtárat használja, akkor automatikusan képes kezelni a folytatási tokeneket, mert az entitásokat a Table Storage-ból adja vissza. A következő C# mintakód például automatikusan kezeli a folytatási jogkivonatokat, ha a Table Storage visszaadja őket a válaszban:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

Az alábbi C#-kódot a folytatási token explicit módon kezeli:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Az explicit módon a folytatási token, szabályozhatja, ha az alkalmazás kéri le a következő szegmenst az adatok. Ha például az ügyfélalkalmazás lehetővé teszi a felhasználók számára, hogy a táblázatban tárolt entitásokon keresztül Lapozzák fel a felhasználókat, a felhasználók dönthetnek úgy, hogy a lekérdezés által lekért összes entitáson át nem teszik a lapot. Az alkalmazás csak a folytatási tokent használja a következő szegmens lekéréséhez, amikor a felhasználó befejezte a lapozást az aktuális szegmens összes entitásán. Ez a megközelítés több előnye van:  

* Korlátozhatja a tábla tárterületéről lekérdezhető adatok mennyiségét, és áthelyezheti azokat a hálózaton keresztül.  
* Aszinkron I/O-t is végrehajthat a .NET-ben.  
* A folytatási tokent az állandó tárterületre is szerializálhatja, így az alkalmazás összeomlása esetén is folytathatja.  

> [!NOTE]
> A folytatási token általában egy 1 000 entitást tartalmazó szegmenst ad vissza, bár kevesebbet tartalmazhat. Ez abban az esetben is igaz, ha a lekérdezés által visszaadott bejegyzések számát korlátozza a keresési feltételeknek megfelelő első n entitások visszaadásához. A Table Storage olyan szegmenst adhat vissza, amely kevesebb mint n entitást tartalmaz, valamint egy folytatási tokent, amely lehetővé teszi a fennmaradó entitások lekérését.  
> 
> 

Az alábbi C#-kód bemutatja, hogyan módosíthatja egy szegmens található entitások száma:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Kiszolgálóoldali leképezése
Egyetlen entitás legfeljebb 255 tulajdonságok és a legfeljebb 1 MB méretű lehet. Ha lekérdezi a táblázatot, és beolvassa az entitásokat, előfordulhat, hogy nincs szüksége az összes tulajdonságra, és szükségtelenül nem tudja átvinni az adatátvitelt (a késés és a Cost csökkentése érdekében). Kiszolgálóoldali leképezés használatával átvitele csak a szükséges tulajdonságokat. A következő példa csak a `Email` tulajdonságot kérdezi le (`PartitionKey`, `RowKey`, `Timestamp`és `ETag`) a lekérdezés által kiválasztott entitásokból.  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Figyelje meg, hogyan érhető el a `RowKey` értéke annak ellenére, hogy nem szerepel a lekérdezni kívánt Tulajdonságok listájában.  

### <a name="modify-entities"></a>Entitások módosítása
A Storage ügyféloldali kódtára lehetővé teszi, hogy az entitások beszúrásával, törlésével és frissítésével módosítsa a Table Storage-ban tárolt entitásokat. A EGTs használatával egyszerre több beszúrási, frissítési és törlési műveletet is használhat, így csökkentheti a szükséges lekerekítési utak számát, és javíthatja a megoldás teljesítményét.  

Kivételek, ha a Storage ügyféloldali kódtár egy EGT futtat, jellemzően a köteget okozó entitás indexét tartalmazza. Ez akkor hasznos, ha a hibakeresés EGTs használó kódot.  

Azt is figyelembe kell venni, hogyan a kialakítás befolyásolja, hogyan kezeli az ügyfélalkalmazás az egyidejűség és a frissítési művelet.  

#### <a name="managing-concurrency"></a>Az egyidejűség kezelése
Alapértelmezés szerint a Table Storage egy optimista egyidejűségi ellenőrzést valósít meg az egyes entitások szintjén a beszúrási, egyesítési és törlési műveletekhez, bár lehetséges, hogy az ügyfél kényszeríti a Table Storage-t, hogy megkerüljék ezeket az ellenőrzéseket. További információ: [a Egyidejűség kezelése Microsoft Azure Storageban](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Egyesítés vagy cseréje
A `TableOperation` osztály `Replace` metódusa mindig lecseréli a teljes entitást a Table Storage-ban. Ha nem tartalmaz tulajdonságot a kérelemben, ha az adott tulajdonság létezik a tárolt entitásban, a kérelem eltávolítja a tulajdonságot a tárolt entitásból. Kivéve, ha el kívánja távolítani egy tulajdonság explicit módon, egy tárolt entitásból, a kérésben meg kell adnia minden egyes tulajdonság.  

A `TableOperation` osztály `Merge` metódusával csökkentheti a Table Storage számára elküldött adatmennyiséget, ha egy entitást szeretne frissíteni. A `Merge` metódus a tárolt entitásban lévő összes tulajdonságot lecseréli a kérelemben szereplő entitásból származó tulajdonságértékek értékére. Ez a metódus érintetlenül hagy minden olyan tulajdonságot, amely a kérelemben nem szereplő tárolt entitásban található. Ez akkor hasznos, ha nagyméretű entitásokkal rendelkezik, és csak kis számú tulajdonságot kell frissítenie egy kérelemben.  

> [!NOTE]
> A `*Replace` és `Merge` metódus meghiúsul, ha az entitás nem létezik. Másik lehetőségként használhatja a `InsertOrReplace` és `InsertOrMerge` metódusokat, amelyek új entitást hoznak létre, ha az nem létezik.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Heterogén entitások típusának használata
A Table Storage *séma nélküli tábla-* tároló. Ez azt jelenti, hogy egyetlen tábla több típusú entitást is tárolhat, ami nagy rugalmasságot biztosít a kialakításban. Az alábbi példa azt mutatja be, egy tábla, alkalmazott és a részleg entitások tárolására:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Időbélyeg</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Minden entitásnak továbbra is `PartitionKey`, `RowKey`és `Timestamp` értékkel kell rendelkeznie, de a tulajdonságok bármilyen készlete lehet. Emellett nem kell megjelölnie egy entitás típusát, ha nem úgy dönt, hogy valahol tárolja ezt az információt. Kétféle entitás típusának azonosításához:  

* Adja meg az entitás típusát a `RowKey` (vagy esetleg a `PartitionKey`). Például `EMPLOYEE_000123` vagy `DEPARTMENT_SALES` `RowKey` értékként.  
* Az entitás típusát a következő táblázatban látható módon rögzítheti egy külön tulajdonsággal.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Időbélyeg</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>entityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Alkalmazott</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>entityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Alkalmazott</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>entityType</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Részleg</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>entityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Alkalmazott</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Az első lehetőség, amely az entitás típusától függően a `RowKey`ra van kiválasztva, akkor hasznos lehet, ha fennáll a lehetősége, hogy a különböző típusú entitások esetében ugyanaz a kulcs értéke. Csoportosítja a együtt, a partíció azonos típusú entitásokat is.  

Az ebben a szakaszban tárgyalt technikák különösen az[öröklési kapcsolatokkal](#inheritance-relationships)kapcsolatos vitára vonatkoznak.  

> [!NOTE]
> Vegye figyelembe a verziószámot az entitás típusa értékben, hogy lehetővé váljon az ügyfélalkalmazások számára a POCO-objektumok fejlesztése és a különböző verziók használata.  
> 
> 

Ez a szakasz további része a Storage ügyféloldali kódtár, amely ugyanabban a táblában több entitástípusok használatának megkönnyítése érdekében funkcióit ismerteti.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Heterogén entitások típusának beolvasása
Ha a Storage ügyféloldali kódtárat használja, három lehetősége van több entitás típusának használatára.  

Ha ismeri a megadott `RowKey` és `PartitionKey` értékekkel tárolt entitás típusát, akkor megadhatja az entitás típusát az entitás beolvasása során. Ezt az előző két példákban látta el, amelyek `EmployeeEntity`típusú entitásokat kérdeznek le: [a Storage ügyféloldali kódtár használatával](#run-a-point-query-by-using-the-storage-client-library) lekérik a pontok lekérdezését, és [több entitást is beolvasnak a LINQ használatával](#retrieve-multiple-entities-by-using-linq).  

A második lehetőség, hogy a `DynamicTableEntity` típust (a tulajdonság táska) használja a konkrét POCO típusú entitások típusának megadása helyett. Ez a beállítás javíthatja a teljesítményt is, mivel nem szükséges szerializálni és deszerializálni az entitást a .NET-típusokhoz. A következő C# kód lehetséges, hogy több, különböző típusú entitást kér le a táblából, de az összes entitást `DynamicTableEntity` példányként adja vissza. Ezután a `EntityType` tulajdonsággal határozza meg az egyes entitások típusát:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Más tulajdonságok beolvasásához a `DynamicTableEntity` osztály `Properties` tulajdonságának `TryGetValue` metódusát kell használnia.  

A harmadik lehetőség a `DynamicTableEntity` típus és egy `EntityResolver`-példány együttes használata. Ez lehetővé teszi, hogy ugyanabban a lekérdezésben többféle POCO feloldani. Ebben a példában a `EntityResolver` delegált a `EntityType` tulajdonságot használja a lekérdezés által visszaadott két típusú entitás megkülönböztetésére. A `Resolve` metódus a `resolver` delegált használatával oldja fel a `DynamicTableEntity` példányokat `TableEntity` példányokra.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

#### <a name="modify-heterogeneous-entity-types"></a>Heterogén entitások típusának módosítása
Nem kell tudnia, hogy milyen típusú entitást kell törölni, és mindig ismeri az entitás típusát az beszúrásakor. A `DynamicTableEntity` típus használatával azonban a típus ismerete nélkül frissítheti az entitásokat, és a POCO Entity osztály használata nélkül is. A következő mintakód egyetlen entitást kérdez le, és ellenőrzi, hogy a `EmployeeCount` tulajdonság létezik-e a frissítés előtt.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

### <a name="control-access-with-shared-access-signatures"></a>Hozzáférés vezérlése közös hozzáférésű aláírásokkal
A megosztott hozzáférés-aláírási (SAS-) tokenekkel engedélyezheti az ügyfélalkalmazások számára, hogy közvetlenül módosíthassák (és lekérdezési) a táblák entitásait anélkül, hogy közvetlenül a Table Storage szolgáltatással kellene hitelesíteni őket. Általában a SAS használatával az alkalmazás három fő előnyök:  

* A Storage-fiók kulcsát nem szükséges egy nem biztonságos platformra (például egy mobileszközön) terjeszteni ahhoz, hogy az eszköz hozzáférjen és módosíthassa az entitásokat a Table Storage-ban.  
* A webes és feldolgozói szerepkörök egy részét kiszervezheti az entitások kezelése során. Kiszervezheti az ügyfelek eszközeit, például a végfelhasználói számítógépeket és a mobil eszközöket.  
* Korlátozott és időkorlátos engedélyeket rendelhet egy ügyfélhez (például engedélyezheti a csak olvasási hozzáférést adott erőforrásokhoz).  

További információ az SAS-tokenek Table Storage használatával történő használatáról: [közös hozzáférésű aláírások (SAS) használata](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Azonban továbbra is olyan SAS-jogkivonatokat kell megadnia, amelyek az ügyfélalkalmazások számára engedélyezik a Table Storage-beli entitásokat. Ezt olyan környezetben tegye meg, amely biztonságos hozzáférést biztosít a Storage-fiók kulcsaihoz. Általában használatával egy webes vagy feldolgozói szerepkör a SAS-jogkivonatokat hoz létre, és továbbítsa őket az ügyfélalkalmazások, amelyek az entitásokhoz való hozzáférés szükséges. Mivel van még egy terhelés létrehozása, és jusson el SAS-tokeneket az ügyfeleknek, hogyan érdemes érdemes lehet csökkenteni a terhelést, különösen nagy mennyiségű forgatókönyvekben részt.  

Létre lehet hozni egy SAS-jogkivonatot, amely hozzáférést biztosít a táblákban lévő entitások egy részhalmazához. Alapértelmezés szerint egy teljes táblához hoz létre SAS-jogkivonatot. Azt is megadhatja, hogy az SAS-jogkivonat hozzáférést biztosítson `PartitionKey` értékekhez, illetve `PartitionKey`-és `RowKey`-értékek tartományához. Dönthet úgy is, hogy SAS-jogkivonatokat állít elő a rendszer egyes felhasználói számára, így az egyes felhasználók SAS-jogkivonata csak a saját entitásokhoz való hozzáférést teszi lehetővé a Table Storage szolgáltatásban.  

### <a name="asynchronous-and-parallel-operations"></a>Párhuzamos és aszinkron műveletek
Amennyiben a kérelmek több partíción is szét, aszinkron vagy a párhuzamos lekérdezések használatával is javítható az átviteli sebesség és az ügyfél válaszképességét.
Előfordulhat például, hogy két vagy több feldolgozói szerepkör példányai a táblák párhuzamos eléréséhez. Egyéni feldolgozói szerepköröket is használhat a partíciók bizonyos csoportjai számára, vagy egyszerűen több feldolgozói szerepkör-példánnyal rendelkezhet, amelyek mindegyike egy tábla összes partícióját elérheti.  

Egy ügyfél-példányon belül a tárolási műveletek aszinkron futtatásával javíthatja az átviteli sebességet. A Storage ügyféloldali kódtára megkönnyíti az írási aszinkron lekérdezések és a módosításokat. Előfordulhat például, hogy a szinkron metódussal indul, amely egy partíció összes entitását lekéri, ahogy az a következő C# kódban látható:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

A kód egyszerűen módosítható úgy, hogy a lekérdezés aszinkron módon fusson, a következőképpen:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

Aszinkron példában láthatja a szinkron verzió a következő módosításokat:  

* A metódus aláírása mostantól tartalmazza a `async` módosítót, és egy `Task` példányt ad vissza.  
* Ahelyett, hogy a `ExecuteSegmented` metódust az eredmények lekérésére hívja, a metódus most meghívja a `ExecuteSegmentedAsync` metódust. A metódus a `await` módosító használatával aszinkron módon kéri le az eredményeket.  

Az ügyfélalkalmazás többször is meghívhatja ezt a metódust a `department` paraméter különböző értékeivel. Mindegyik lekérdezés külön szálon fut.  

A `Execute` metódusnak nincs aszinkron verziója a `TableQuery` osztályban, mert a `IEnumerable` felület nem támogatja az aszinkron enumerálást.  

Beszúrása, frissítése, és aszinkron módon entitások törlése. Az alábbi C# példa bemutatja egy egyszerű, a szinkron módszer beszúrása vagy alkalmazotti entitás cseréje:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

A kód egyszerűen módosítható úgy, hogy a frissítés aszinkron módon fusson, a következőképpen:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Aszinkron példában láthatja a szinkron verzió a következő módosításokat:  

* A metódus aláírása mostantól tartalmazza a `async` módosítót, és egy `Task` példányt ad vissza.  
* Ahelyett, hogy meghívja a `Execute` metódust az entitás frissítéséhez, a metódus most meghívja a `ExecuteAsync` metódust. A metódus a `await` módosító használatával aszinkron módon kéri le az eredményeket.  

Az ügyfélalkalmazás több aszinkron metódust is meghívhat, így az egyes metódusok külön szálon futnak.  


[1]: ./media/storage-table-design-guide/storage-table-design-IMAGE01.png
[2]: ./media/storage-table-design-guide/storage-table-design-IMAGE02.png
[3]: ./media/storage-table-design-guide/storage-table-design-IMAGE03.png
[4]: ./media/storage-table-design-guide/storage-table-design-IMAGE04.png
[5]: ./media/storage-table-design-guide/storage-table-design-IMAGE05.png
[6]: ./media/storage-table-design-guide/storage-table-design-IMAGE06.png
[7]: ./media/storage-table-design-guide/storage-table-design-IMAGE07.png
[8]: ./media/storage-table-design-guide/storage-table-design-IMAGE08.png
[9]: ./media/storage-table-design-guide/storage-table-design-IMAGE09.png
[10]: ./media/storage-table-design-guide/storage-table-design-IMAGE10.png
[11]: ./media/storage-table-design-guide/storage-table-design-IMAGE11.png
[12]: ./media/storage-table-design-guide/storage-table-design-IMAGE12.png
[13]: ./media/storage-table-design-guide/storage-table-design-IMAGE13.png
[14]: ./media/storage-table-design-guide/storage-table-design-IMAGE14.png
[15]: ./media/storage-table-design-guide/storage-table-design-IMAGE15.png
[16]: ./media/storage-table-design-guide/storage-table-design-IMAGE16.png
[17]: ./media/storage-table-design-guide/storage-table-design-IMAGE17.png
[18]: ./media/storage-table-design-guide/storage-table-design-IMAGE18.png
[19]: ./media/storage-table-design-guide/storage-table-design-IMAGE19.png
[20]: ./media/storage-table-design-guide/storage-table-design-IMAGE20.png
[21]: ./media/storage-table-design-guide/storage-table-design-IMAGE21.png
[22]: ./media/storage-table-design-guide/storage-table-design-IMAGE22.png
[23]: ./media/storage-table-design-guide/storage-table-design-IMAGE23.png
[24]: ./media/storage-table-design-guide/storage-table-design-IMAGE24.png
[25]: ./media/storage-table-design-guide/storage-table-design-IMAGE25.png
[26]: ./media/storage-table-design-guide/storage-table-design-IMAGE26.png
[27]: ./media/storage-table-design-guide/storage-table-design-IMAGE27.png
[28]: ./media/storage-table-design-guide/storage-table-design-IMAGE28.png
[29]: ./media/storage-table-design-guide/storage-table-design-IMAGE29.png

