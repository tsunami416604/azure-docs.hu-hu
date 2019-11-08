---
title: Azure Cosmos DB táblázatok kialakítása a skálázás és a teljesítmény támogatásához
description: 'Az Azure Storage Table tervezési útmutatója: skálázható és teljesítményű táblázatok tervezése Azure Cosmos DB és az Azure Storage-táblában'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: wmengmsft
ms.author: wmeng
ms.custom: seodec18
ms.openlocfilehash: 499ac3a394339ebb07c36abeaaa761de22927941
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827773"
---
# <a name="azure-storage-table-design-guide-designing-scalable-and-performant-tables"></a>Azure Storage Table – tervezési útmutató: skálázható és elvégezhető táblák tervezése

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

A skálázható és a teljesítményű táblázatok megtervezéséhez számos tényezőt kell figyelembe vennie, például a teljesítményt, a méretezhetőséget és a költségeket. Ha korábban már tervezett sémákat a kapcsolati adatbázisokhoz, ezek a szempontok ismerősek lesznek, de bár vannak hasonlóságok az Azure Table service Storage-modell és a kapcsolati modellek között, számos fontos különbség is van. Ezek az eltérések általában olyan különböző kialakításokhoz vezetnek, amelyek a viszonyítási adatbázisokkal való ismerkedéshez intuitív vagy helytelenül hasonlítanak, de jó érzéket, ha olyan NoSQL kulcs/érték tárolót tervez, mint például az Azure Table service. A tervezési különbségek többsége tükrözi azt a tényt, hogy a Table service olyan felhőalapú alkalmazások támogatására lett tervezve, amelyek több milliárd entitást (a viszonyítási adatbázis-terminológiában lévő sorokat) és a magas tranzakciót támogató adatkészleteket is tartalmazhatnak. kötetek: ezért az adatai tárolása és a Table service működésének megismerése érdekében máshogy kell gondolkodnia. A jól megtervezett NoSQL-adattárak lehetővé teszik, hogy a megoldás sokkal tovább méretezhető legyen (és alacsonyabb költségeket is), mint a viszonyítási adatbázist használó megoldás. Ez az útmutató segítséget nyújt ezekről a témakörökről.  

## <a name="about-the-azure-table-service"></a>Tudnivalók az Azure Table service
Ez a szakasz a Table service főbb jellemzőit mutatja be, amelyek különösen a teljesítmény és a méretezhetőség tervezéséhez szükségesek. Ha még nem ismeri az Azure Storage-t és a Table servicet, először olvassa el a [Microsoft Azure Storage bevezetését](../storage/common/storage-introduction.md) , és [Ismerkedjen meg az Azure Table Storage .NET használatával](table-storage-how-to-use-dotnet.md) a jelen cikk hátralévő részének beolvasása előtt. Bár az útmutató célja a Table service, tartalmazni fog néhány vitát az Azure üzenetsor és a blob Services számára, valamint azt, hogy miként használhatja azokat a megoldás Table serviceével együtt.  

Mi a Table service? Ahogy a neve is várható, a Table service táblázatos formátumot használ az adatok tárolására. A standard terminológiában a tábla minden sora egy entitást jelöl, és az oszlopok az entitás különböző tulajdonságait tárolják. Minden entitás rendelkezik egy pár kulccsal, amely egyedi módon azonosítja azt, valamint egy időbélyeg-oszlopot, amelyet a Table service az entitás utolsó frissítésének nyomon követéséhez használ (az időbélyeg mező automatikusan hozzáadódik, és nem lehet manuálisan felülírni az időbélyeget egy tetszőleges értékkel). A Table service ezt az utolsó módosítás timestamp (LMT) használatával kezeli az optimista egyidejűséget.  

> [!NOTE]
> A Table service REST API műveletek egy **ETAG** értéket adnak vissza, amely az utolsó módosítás időbélyegből (LMT) származik. Ebben a dokumentumban láthatja, hogy a ETag és az LMT feltételeit szinonimaként használják, mivel ugyanazok a mögöttes adatokat említik.  
> 
> 

Az alábbi példa egy egyszerű tábla-kialakítást mutat be az alkalmazottak és részleg entitások tárolásához. Az útmutatóban később bemutatott példák közül sokat ezen az egyszerű kialakításon alapul.  

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
<th>firstName</th>
<th>lastName</th>
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
<th>firstName</th>
<th>lastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Június</td>
<td>Cao</td>
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
<th>firstName</th>
<th>lastName</th>
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


Eddig ez a kialakítás hasonlít a relációs adatbázis egyik táblájához, amely a kötelező oszlopok fő különbségeit tartalmazza, és lehetővé teszi, hogy több típusú entitást is tároljon ugyanabban a táblában. Emellett a felhasználó által definiált tulajdonságok (például a **FirstName** vagy a **Age** ) adattípussal rendelkeznek, például Integer vagy String típusú adatokkal, ugyanúgy, mint a viszonyítási adatbázisban lévő oszlopokhoz. A relációs adatbázistól eltérően azonban a Table service séma nélküli jellege azt jelenti, hogy egy tulajdonságnak nem kell ugyanazzal az adattípussal rendelkeznie minden entitáson. Az összetett adattípusok egyetlen tulajdonságba való tárolásához szerializált formátumot kell használni, például JSON vagy XML. További információ a Table Service-ről, például a támogatott adattípusokról, a támogatott dátumtartomány, az elnevezési szabályok és a méretek megkötéséről: [a Table Service adatmodell ismertetése](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Ahogy látni fogja, a **PartitionKey** és a **RowKey** is alapvető fontosságú a jó tábla kialakításához. A táblában tárolt összes entitásnak a **PartitionKey** és a **RowKey**egyedi kombinációjának kell lennie. A kapcsolati adatbázistábla kulcsaihoz hasonlóan a **PartitionKey** és a **RowKey** értékek indexelve lettek, és egy fürtözött indexet hoznak létre, amely lehetővé teszi a gyors kereséseket. a Table service azonban nem hoz létre másodlagos indexeket, így ezek az egyetlen két indexelt tulajdonság (a később bemutatott minták némelyike azt mutatja be, hogyan lehet megkerülni a látszólagos korlátozást).  

Egy tábla egy vagy több partícióból áll, és ahogy látni fogja, a tervezési döntések közül sok a megfelelő **PartitionKey** és **RowKey** kiválasztása a megoldás optimalizálásához. Egy megoldás csak egyetlen táblából állhat, amely az összes olyan entitást tartalmazza, amely partícióra van osztva, de általában egy megoldás több táblával fog rendelkezni. A táblázatok segítségével logikailag rendszerezheti az entitásokat, és a hozzáférés-vezérlési lista segítségével kezelheti az adathozzáférést, és egyetlen tárolási művelettel elvégezheti a teljes tábla eldobását.  

### <a name="table-partitions"></a>Táblapartíciók
A fiók neve, a tábla neve és a **PartitionKey** együtt azonosítja azt a tárolási szolgáltatáson belüli partíciót, amelyben a Table szolgáltatás tárolja az entitást. Az entitások címzési sémájának részeként a partíciók határozzák meg a tranzakciók hatókörét (lásd az alábbi [Entity Transactions-tranzakciókat](#entity-group-transactions) ), és a Table Service skálázásának alapjait alkotják. További információ a partíciókkal kapcsolatban: az [Azure Storage skálázhatósági és teljesítménybeli céljai](../storage/common/storage-scalability-targets.md).  

A Table service az egyes csomópontok egy vagy több teljes partíciót és a szolgáltatást a csomópontok dinamikusan terheléselosztási partíciói között méretezi. Ha egy csomópont terhelés alatt van, a Table szolgáltatás *feloszthatja* az adott csomópont által kiszolgált partíciók tartományát különböző csomópontokra. Ha a forgalom alá esik, a szolgáltatás *egyesítheti* a partíciók tartományait a csendes csomópontokból egyetlen csomópontra.  

További információ a Table service belső adatairól, valamint arról, hogy a szolgáltatás hogyan kezelje a partíciókat, a papír [Microsoft Azure Storage: magas rendelkezésre állású felhőalapú tárolási szolgáltatás erős konzisztencia mellett](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Entitás-csoport tranzakciói
Az Table serviceban az Entity Transactions (EGTs) az egyetlen beépített mechanizmus, amellyel több entitáson végezheti el az Atomic-frissítéseket. Egyes dokumentációkban a EGTs is nevezik *Batch-tranzakcióknak* . A EGTs csak ugyanabban a partícióban tárolt entitásokban működhetnek (egy adott tábla ugyanazon partíciójának megosztásával), így bármikor, ha több entitás esetében is szüksége van az atomi tranzakciós viselkedésre, biztosítania kell, hogy ezek az entitások ugyanabban a partícióban legyenek. Ez gyakran indokolja, hogy több entitást is tartson ugyanabban a táblában (és partícióban), és ne használjon több táblát a különböző típusú entitásokhoz. Egyetlen EGT legfeljebb 100 entitáson működhet.  Ha több párhuzamos EGTs is elküld a feldolgozáshoz, akkor fontos, hogy a EGTs ne működjenek olyan entitásokon, amelyek a EGTs esetében közösek, mert az egyéb feldolgozás késleltethető.

A EGTs emellett potenciális kompromisszumot is bevezet a tervbe való kiértékeléshez: a további partíciók használata növeli az alkalmazás méretezhetőségét, mivel az Azure több lehetőséget kínál a terheléselosztási kérelmek különböző csomópontjain való elosztására, de ez korlátozhatja a az alkalmazás, amely atomi tranzakciókat hajt végre, és erős konzisztenciát tart fenn az adatokhoz. Ezen kívül bizonyos skálázhatósági célok vannak egy olyan partíció szintjén is, amely korlátozhatja egy adott csomópont számára várható tranzakciók átviteli sebességét: az Azure Storage-fiókok és a Table Service skálázhatósági céljaival kapcsolatos további információkért lásd: [Az Azure Storage skálázhatósági és teljesítménybeli céljai](../storage/common/storage-scalability-targets.md). A jelen útmutató későbbi szakaszai különböző tervezési stratégiákat tárgyalnak, amelyek segítségével kezelheti az ilyen jellegű kompromisszumokat, és megtudhatja, hogyan választhatja ki legjobban a partíciós kulcsot az ügyfélalkalmazás konkrét követelményei alapján.  

### <a name="capacity-considerations"></a>Kapacitással kapcsolatos megfontolások
Az alábbi táblázat néhány fontos értéket tartalmaz, amelyeket érdemes figyelembe venni a Table service megoldás tervezésekor:  

| Azure Storage-fiók teljes kapacitása | 500 TB |
| --- | --- |
| Táblák száma egy Azure Storage-fiókban |Csak a Storage-fiók kapacitása korlátozza |
| Egy tábla partícióinak száma |Csak a Storage-fiók kapacitása korlátozza |
| Egy partícióban lévő entitások száma |Csak a Storage-fiók kapacitása korlátozza |
| Egyéni entitás mérete |Legfeljebb 1 MB, legfeljebb 255 tulajdonsággal (beleértve a **PartitionKey**, a **RowKey**és az **időbélyeget**) |
| A **PartitionKey** mérete |Legfeljebb 1 KB méretű sztring |
| A **RowKey** mérete |Legfeljebb 1 KB méretű sztring |
| Entitás-csoport tranzakciójának mérete |Egy tranzakció legfeljebb 100 entitást tartalmazhat, és a hasznos adatnak 4 MB-nál kisebbnek kell lennie. Egy EGT csak egyszer tud frissíteni egy entitást. |

További információt a [Table Service adatmodelljét ismertető](https://msdn.microsoft.com/library/azure/dd179338.aspx) témakörben talál.  

### <a name="cost-considerations"></a>Költségekkel kapcsolatos szempontok
A Table Storage szolgáltatás viszonylag olcsó, de érdemes belefoglalni a kapacitás-használat és a tranzakciók mennyiségét a Table service használó bármely megoldás kiértékelésének részeként. Sok esetben azonban a denormalizált vagy duplikált adatok tárolása a megoldás teljesítményének és méretezhetőségének javítása érdekében érvényes megközelítés. A díjszabással kapcsolatos további információkért lásd: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Irányelvek táblatervezéshez
Ezek a jegyzékek összefoglalják a főbb irányelveket, amelyeket érdemes figyelembe venni a táblázatok tervezésekor, és ez az útmutató a későbbiekben további részleteket tartalmaz. Ezek az irányelvek különböznek azoktól az irányelvektől, amelyeket általában a kapcsolatok adatbázisának kialakításához követnek.  

A Table service-megoldás hatékony *olvasásának* megtervezése:

* ***Az olvasási és a nagy teljesítményű alkalmazásokban való lekérdezés tervezése.*** A táblázatok tervezésekor gondolja át a lekérdezéseket (különösen a késési különbségeket), amelyeket a rendszer az entitások frissítésének megkezdése előtt végrehajt. Ez általában egy hatékony és nagy teljesítményű megoldás eredményét eredményezi.  
* ***Adja meg a PartitionKey és a RowKey is a lekérdezésekben.*** A *pontok lekérdezései* , például ezek a leghatékonyabb Table Service-lekérdezések.  
* ***Érdemes lehet az entitások duplikált példányait tárolni.*** A Table Storage olcsó, ezért a hatékonyabb lekérdezések lehetővé tételéhez ugyanazt az entitást többször kell tárolni (különböző kulcsokkal).  
* ***Érdemes lehet az adatai denormalizálása.*** A Table Storage olcsó, ezért érdemes lehet az adatai denormalizálása. Tárolhatja például az összegző entitásokat, hogy az összesített adatlekérdezések csak egyetlen entitáshoz férhessenek hozzá.  
* ***Használjon összetett kulcsos értékeket.*** Csak a **PartitionKey** és a **RowKey**kulcsok vannak. Használjon például összetett kulcsos értékeket az entitásokhoz való helyettesítő hozzáférési útvonalak engedélyezéséhez.  
* ***Lekérdezési leképezés használata.*** Csökkentheti a hálózaton keresztül átvitt adatok mennyiségét olyan lekérdezések használatával, amelyek csak a szükséges mezőket választják ki.  

A Table service-megoldás hatékony *írásának* megtervezése:  

* ***Ne hozzon létre forró partíciót.*** Válassza ki azokat a kulcsokat, amelyek lehetővé teszik a kérések több partíción keresztüli terjesztését egy adott időpontban.  
* ***Kerülje a forgalomban lévő tüskéket.*** A forgalmat egy ésszerű időn belül zökkenőmentesen, és elkerülje a forgalomban fellépő tüskéket.
* ***Ne feltétlenül hozzon létre külön táblát az egyes entitások típusaihoz.*** Ha az entitások típusai között atomi tranzakciókat igényel, akkor a több entitás típusát ugyanabban a partícióban tárolhatja ugyanabban a táblában.
* ***Gondolja át, hogy mekkora átviteli sebességet kell elérnie.*** Tisztában kell lennie a Table service skálázhatósági céljaival, és gondoskodnia kell arról, hogy a kialakítás ne lépje túl őket.  

Az útmutató elolvasása során olyan példákat talál, amelyek az összes alapelvet bemutatják a gyakorlatban.  

## <a name="design-for-querying"></a>Tervezés lekérdezéshez
Table service megoldások intenzív, írásos vagy a kettő kombinációját is beolvashatja. Ez a szakasz azokat a dolgokat ismerteti, amelyeket figyelembe kell vennie, amikor megtervezi a Table service az olvasási műveletek hatékony támogatásához. Az olvasási műveleteket hatékonyan támogató kialakítás általában írási műveletek esetén is hatékony. Azonban további szempontokat is figyelembe kell venni az írási műveletek támogatásának tervezésekor, a következő szakaszban tárgyalva az [adatok módosításának megtervezését](#design-for-data-modification).

Egy jó kiindulási pont a Table service megoldás megtervezéséhez, amely lehetővé teszi az adatok hatékony olvasását, ha megkérdezi, hogy milyen lekérdezéseket kell végrehajtani az alkalmazásnak, hogy lekérje a szükséges adatok lekérését a Table service? "  

> [!NOTE]
> A Table service esetében fontos, hogy a kialakítás megtörténjen, mert nehéz és költséges lehet később módosítani. Például egy rokon adatbázisban gyakran lehetséges a teljesítménnyel kapcsolatos problémák megoldása, ha indexeket ad hozzá egy meglévő adatbázishoz: ez nem egy lehetőség a Table service.  
> 
> 

Ez a szakasz azokat a kulcsfontosságú problémákat tárgyalja, amelyeket a táblák lekérdezéshez való tervezésekor kell megadnia. Az ebben a szakaszban szereplő témakörök a következők:

* [A PartitionKey és a RowKey hatása a lekérdezési teljesítményre](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Megfelelő PartitionKey kiválasztása](#choosing-an-appropriate-partitionkey)
* [A Table service lekérdezések optimalizálása](#optimizing-queries-for-the-table-service)
* [Adatrendezés a Table serviceban](#sorting-data-in-the-table-service)

### <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>A PartitionKey és a RowKey hatása a lekérdezési teljesítményre
A következő példák azt feltételezik, hogy a Table Service az alábbi struktúrával tárolja az alkalmazotti entitásokat (a legtöbb példa kihagyja az Érthetőségi **timestamp** tulajdonságot):  

| *Oszlop neve* | *Adattípus* |
| --- | --- |
| **PartitionKey** (részleg neve) |Sztring |
| **RowKey** (alkalmazott azonosítója) |Sztring |
| **FirstName** |Sztring |
| **LastName** |Sztring |
| **Kor** |Egész szám |
| **EmailAddress** |Sztring |

Az Azure Table service-áttekintés korábbi szakasza az Azure Table service főbb funkcióit ismerteti, amelyek közvetlen hatással vannak a lekérdezés tervezésére. Ezek a következő általános irányelveket eredményezik Table service lekérdezések tervezéséhez. Az alábbi példákban használt szűrési szintaxis a Table service REST API, további információ: [lekérdezési entitások](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* Az adott ***pont lekérdezése*** a leghatékonyabb keresési módszer, amelyet a legalacsonyabb késleltetést igénylő nagy mennyiségű keresésekhez vagy keresésekhez ajánlott használni. Egy ilyen lekérdezés az indexeket használva hatékonyan megkeresheti az egyes entitásokat a **PartitionKey** és a **RowKey** értékek megadásával. Például: $filter = (PartitionKey EQ ' Sales ') és (RowKey EQ ' 2 ')  
* A második legjobb egy ***tartományos lekérdezés*** , amely a **PartitionKey** és a szűrőket használja számos **RowKey** -értékkel, így több entitást ad vissza. A **PartitionKey** érték egy adott partíciót azonosít, és a **RowKey** értékek a partícióban lévő entitások egy részhalmazát azonosítják. Például: $filter = PartitionKey EQ ' Sales ' and RowKey GE ' and RowKey lt 'T '  
* A harmadik legjobb egy olyan ***partíciós vizsgálat*** , amely a **PartitionKey** és a szűrőket használja egy másik nem kulcsfontosságú tulajdonságnál, amely több entitást is visszaadhat. A **PartitionKey** érték egy adott partíciót azonosít, és a tulajdonságértékek a partícióban lévő entitások egy részhalmaza számára kiválasztva. Például: $filter = PartitionKey EQ "Sales" és LastName EQ "Smith"  
* A ***táblázatos vizsgálat*** nem tartalmazza a **PartitionKey** , és nem hatékony, mert megkeresi az összes olyan partíciót, amely a táblázatot felkészíti a megfelelő entitásokra. Táblázatos vizsgálatot végez, függetlenül attól, hogy a szűrő a **RowKey**használja-e. Például: $filter = LastName EQ ' Jones '  
* Az Azure Table Storage olyan lekérdezéseket, amelyek több entitást adnak vissza, **PartitionKey** és **RowKey** sorrendben rendezik őket. Ha nem szeretné, hogy az entitások ne legyenek az ügyfélben, válasszon egy **RowKey** , amely meghatározza a leggyakoribb rendezési sorrendet. A Azure Cosmos DB Azure-Table API által visszaadott lekérdezési eredményeket nem a partíciós kulcs vagy a sor kulcsa szerint rendezi a rendszer. A szolgáltatások közötti különbségek részletes listáját a [Azure Cosmos db és az Azure Table storage Table API közötti különbségek](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)című részben tekintheti meg.

A "**vagy**" használatával **RowKey** -értékek alapján határozhat meg egy partíciós vizsgálat eredményét, és nem kezelhető tartomány-lekérdezésként. Ezért érdemes elkerülni a szűrőket használó lekérdezéseket, például a következőket: $filter = PartitionKey EQ ' Sales ' és (RowKey EQ ' 121 ' vagy RowKey EQ ' 322 ')  

A Storage ügyféloldali kódtárat használó ügyféloldali kódokra vonatkozó példákat a hatékony lekérdezések végrehajtásához lásd:  

* [Pont lekérdezés végrehajtása a Storage ügyféloldali kódtár használatával](#executing-a-point-query-using-the-storage-client-library)
* [Több entitás beolvasása a LINQ használatával](#retrieving-multiple-entities-using-linq)
* [Kiszolgálóoldali kivetítés](#server-side-projection)  

Az ugyanabban a táblában tárolt több entitást kezelő ügyféloldali kódokra vonatkozó Példákért lásd:  

* [Heterogén entitások típusának használata](#working-with-heterogeneous-entity-types)  

### <a name="choosing-an-appropriate-partitionkey"></a>Megfelelő PartitionKey kiválasztása
Az Ön által választott **PartitionKey** egyensúlyt kell biztosítania ahhoz, hogy lehetővé váljon a EGTs használata (az egységesség biztosítása érdekében) az entitások több partíción való terjesztésének követelménye (a méretezhető megoldás biztosításához).  

Egyetlen szélsőséges esetben egyetlen partícióban tárolhatja az összes entitást, ez azonban korlátozhatja a megoldás méretezhetőségét, és megakadályozhatja, hogy a Table Service terheléselosztási kérelmeket tudjon betölteni. A másik véglet, ha egy entitást tárol egy partíción, ami nagy mértékben méretezhető, és lehetővé teszi a Table szolgáltatás számára, hogy terheléselosztási kérelmeket használjon, de ez megakadályozhatja az Entity Group-tranzakciók használatát.  

Az ideális **PartitionKey** , amely lehetővé teszi, hogy hatékony lekérdezéseket használjon, és elegendő partíciót biztosítson a megoldás méretezhetőségének biztosításához. Általában úgy látja, hogy az entitások megfelelő tulajdonsággal rendelkeznek, amely az entitásokat a megfelelő partíciók között osztja szét.

> [!NOTE]
> A felhasználókkal vagy alkalmazottakkal kapcsolatos adatokat tároló rendszerekben például a UserID jó PartitionKey lehet. Előfordulhat, hogy több olyan entitása van, amely egy adott felhasználóazonosító-t használ partíciós kulcsként. Minden olyan entitás, amely egy felhasználó adatait tárolja, egyetlen partícióba van csoportosítva, így ezek az entitások az Entity Group Transactions használatával érhetők el, miközben továbbra is jól méretezhetők.
> 
> 

Az entitások beszúrási, frissítési és törlési módjával kapcsolatban további szempontokat is figyelembe kell vennie: a **PartitionKey** az alábbi, [adatmódosítási terv](#design-for-data-modification) című szakaszban talál.  

### <a name="optimizing-queries-for-the-table-service"></a>A Table service lekérdezések optimalizálása
A Table service automatikusan indexeli az entitásokat egy fürtözött indexben lévő **PartitionKey** és **RowKey** értékekkel, ezért az az oka, hogy a pontok lekérdezései a leghatékonyabbak. A **PartitionKey** és a **RowKey**fürtözött indexén azonban nincsenek indexek.

Számos tervnek meg kell felelnie az entitások több feltételen alapuló keresésének engedélyezéséhez szükséges követelményeknek. Például az alkalmazotti entitások az e-mailek, az alkalmazotti azonosítók vagy a vezetéknév alapján vannak megkeresve. Az alábbi, a [tábla kialakítási mintái](#table-design-patterns) című szakasza ezeket a követelményeket ismerteti, és leírja, hogy a Table Service milyen módszereket biztosít a másodlagos indexek eléréséhez:  

* [Partíción belüli másodlagos index minta](#intra-partition-secondary-index-pattern) – az egyes entitások több példányának tárolása különböző **RowKey** -értékekkel (ugyanabban a partícióban) a gyors és hatékony keresési és alternatív rendezési sorrendek eltérő **RowKey** -értékek használatával történő engedélyezéséhez.  
* [Partíciók közötti másodlagos index mintája](#inter-partition-secondary-index-pattern) – az egyes entitások több példányának tárolása különböző **RowKey** -értékekkel külön partíciókban vagy külön táblákban, amelyek lehetővé teszik a gyors és hatékony keresési és alternatív rendezési sorrendek használatát  **RowKey** -értékek.  
* [Entitások indexelése](#index-entities-pattern) – az indexelési entitások megtartása az entitások listáját visszaadó hatékony keresések engedélyezéséhez.  

### <a name="sorting-data-in-the-table-service"></a>Adatrendezés a Table serviceban

A Table service által visszaadott lekérdezési eredményeket növekvő sorrendbe rendezi a rendszer a **PartitionKey** , majd a **RowKey**alapján.

> [!NOTE]
> A Azure Cosmos DB Azure-Table API által visszaadott lekérdezési eredményeket nem a partíciós kulcs vagy a sor kulcsa szerint rendezi a rendszer. A szolgáltatások közötti különbségek részletes listáját a [Azure Cosmos db és az Azure Table storage Table API közötti különbségek](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)című részben tekintheti meg.

Az Azure Storage táblában található kulcsok karakterlánc-értékek, és a numerikus értékek megfelelő rendezése érdekében érdemes a rögzített hosszúságú értékre váltani, és nullával elválasztani őket. Ha például a **RowKey** használt alkalmazotti azonosító érték egész érték, akkor a **123** -as alkalmazott azonosítóját a **00000123**-re kell konvertálnia. 

Számos alkalmazás rendelkezik a különböző megrendelésekben tárolt adatok használatára vonatkozó követelményekkel: például az alkalmazottak név szerinti rendezése vagy a csatlakozás dátuma. A (z) szakasz [táblázatának kialakítási mintái](#table-design-patterns) a következő mintákat ismertetik az entitások más rendezési rendeléseivel kapcsolatban:  

* [Partíción belüli másodlagos index minta](#intra-partition-secondary-index-pattern) – az egyes entitások több példányának tárolása különböző RowKey-értékekkel (ugyanabban a partícióban) a gyors és hatékony keresési és alternatív rendezési sorrendek eltérő RowKey-értékek használatával történő engedélyezéséhez.  
* [Partíciók közötti másodlagos index minta](#inter-partition-secondary-index-pattern) – az egyes entitások több példányának tárolása különböző RowKey-értékekkel különálló partíciókban külön táblákban, amelyek lehetővé teszik a gyors és hatékony keresési és alternatív rendezési sorrendek használatát a különböző RowKey értékek használatával.
* [Naplóbeli farok minta](#log-tail-pattern) – a partícióhoz legutóbb hozzáadott *n* entitások beolvasása egy **RowKey** értékkel, amely fordított dátum és idő sorrendbe rendezi.  

## <a name="design-for-data-modification"></a>Tervezés adatmódosításhoz
Ez a szakasz a beszúrások, a frissítések és a törlések optimalizálására vonatkozó tervezési szempontokra összpontosít. Bizonyos esetekben ki kell értékelnie a kiértékelést olyan formatervezési minták között, amelyek optimalizálják az adatmódosításra optimalizált kialakításokat, ugyanúgy, mint a viszonyítási adatbázisok tervezésekor (bár a tervezési kompromisszumok kezelésének módszerei eltérő a kapcsolódó adatbázisban). A [tábla kialakítási mintái](#table-design-patterns) a Table Service néhány részletes kialakítási mintáját ismertetik, és kiemelik a kompromisszumok némelyikét. A gyakorlatban azt tapasztalja, hogy számos, az entitások lekérdezésére optimalizált terv is jól működik az entitások módosításához.  

### <a name="optimizing-the-performance-of-insert-update-and-delete-operations"></a>Az INSERT, Update és DELETE műveletek teljesítményének optimalizálása
Entitások frissítéséhez vagy törléséhez a **PartitionKey** és a **RowKey** értékeket kell tudnia azonosítani. Ebben a tekintetben az entitások módosítására választott **PartitionKey** és **RowKey** a hasonló feltételeket kell követnie, hogy támogassa a pontok lekérdezéseit, mert az entitásokat a lehető leghatékonyabb módon szeretné azonosítani. Nem érdemes nem hatékony partíciót vagy táblázatot keresni, hogy megkeresse az entitást, hogy felderítse a frissíteni vagy törölni kívánt **PartitionKey** és **RowKey** értékeket.  

A következő minták a [táblázat tervezési mintái](#table-design-patterns) című szakasza a teljesítmény vagy az INSERT, Update és DELETE műveletek optimalizálása érdekében:  

* [Nagy mennyiségű törlési minta](#high-volume-delete-pattern) – engedélyezze a nagy mennyiségű entitás törlését úgy, hogy az összes entitást egyidejű törlésre tárolja a saját külön táblában. az entitásokat a tábla törlésével törölheti.  
* [Adatsorozat-minta](#data-series-pattern) – a teljes adatsorozatok tárolása egyetlen entitásban, az Ön által végzett kérelmek számának csökkentése érdekében.  
* [Széles entitások mintája](#wide-entities-pattern) – több fizikai entitást használhat a több mint 252 tulajdonságú logikai entitások tárolására.  
* [Nagyméretű entitások mintája](#large-entities-pattern) – a blob Storage használata nagy tulajdonságértékek tárolására.  

### <a name="ensuring-consistency-in-your-stored-entities"></a>Az egységesség biztosítása a tárolt entitásokban
A másik kulcsfontosságú tényező, amely hatással van az adatmódosítások optimalizálására kiválasztott kulcsokra, az Atomic Transactions használatával biztosítható a konzisztencia. A EGT csak akkor használhatók, ha ugyanazon a partíción tárolt entitásokon működnek.  

A következő minták szerepelnek a [táblázat kialakítási mintái](#table-design-patterns) a konzisztencia kezelése című szakaszban:  

* [Partíción belüli másodlagos index minta](#intra-partition-secondary-index-pattern) – az egyes entitások több példányának tárolása különböző **RowKey** -értékekkel (ugyanabban a partícióban) a gyors és hatékony keresési és alternatív rendezési sorrendek eltérő **RowKey** -értékek használatával történő engedélyezéséhez.  
* [Partíciók közötti másodlagos index mintája](#inter-partition-secondary-index-pattern) – az egyes entitások több példányának tárolása különböző RowKey-értékekkel külön partíciókban vagy külön táblákban, amelyek lehetővé teszik a gyors és hatékony keresési és alternatív rendezési sorrendek használatát különböző **RowKey** értékek.  
* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern) – az Azure Queues használatával lehetővé válik a partíciós határok vagy a tárolási rendszerek határainak végül konzisztens viselkedésének engedélyezése.
* [Entitások indexelése](#index-entities-pattern) – az indexelési entitások megtartása az entitások listáját visszaadó hatékony keresések engedélyezéséhez.  
* [Denormalizálás mintája](#denormalization-pattern) – a kapcsolódó adategységeket egyetlen entitásban kombinálva lehetővé teszi az összes szükséges adat lekérését egyetlen pont lekérdezéssel.  
* [Adatsorozat-minta](#data-series-pattern) – a teljes adatsorozatok tárolása egyetlen entitásban, az Ön által végzett kérelmek számának csökkentése érdekében.  

További információ az Entity Transactions szolgáltatással kapcsolatban: [Entity Transactions (entitások csoportosítása](#entity-group-transactions)) szakasz.  

### <a name="ensuring-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>A hatékony módosítások kialakításának biztosítása a hatékony lekérdezések érdekében
Sok esetben a hatékony lekérdezések kialakítása a hatékony módosítások eredményét eredményezi, azonban mindig ki kell értékelnie, hogy ez a helyzet az adott forgatókönyv esetén. A szakasz [tábla kialakítási mintáinak](#table-design-patterns) néhány mintája explicit módon értékeli ki az entitások lekérdezése és módosítása közötti kompromisszumokat, és mindig vegye figyelembe az egyes típusú műveletek számát.  

A (z) szakasz [táblázatának kialakítási mintái](#table-design-patterns) a következő mintákat ismertetik a hatékony lekérdezések tervezése és a hatékony adatmódosítás kialakítása között:  

* [Összetett kulcs mintázata](#compound-key-pattern) – összetett **RowKey** -értékek használatával lehetővé teheti, hogy az ügyfél egyetlen pont lekérdezéssel keressen kapcsolódó adatokkal.  
* [Naplóbeli farok minta](#log-tail-pattern) – a partícióhoz legutóbb hozzáadott *n* entitások beolvasása egy **RowKey** értékkel, amely fordított dátum és idő sorrendbe rendezi.  

## <a name="encrypting-table-data"></a>A tábla adatai titkosítva
A .NET Azure Storage ügyféloldali kódtára támogatja a karakterlánc-entitások tulajdonságainak az INSERT és a Replace műveletekhez való titkosítását. A titkosított karakterláncok a szolgáltatásban bináris tulajdonságokként tárolódnak, és a visszafejtés után vissza lesznek konvertálva karakterlánccá.    

A táblák esetében a titkosítási házirenden kívül a felhasználóknak meg kell adniuk a titkosítani kívánt tulajdonságokat. Ezt megteheti egy [EncryptProperty] attribútum megadásával (a TableEntity-ből származó POCO-entitások esetén) vagy a kérési beállításokban található titkosítási feloldóval. A titkosítási feloldó egy olyan delegált, amely egy partíciós kulcsot, egy sor kulcsot és egy tulajdonság nevét veszi át, és egy logikai értéket ad vissza, amely jelzi, hogy a tulajdonságot titkosítani kell-e. A titkosítás során az ügyféloldali kódtár ezeket az információkat fogja használni annak eldöntéséhez, hogy a tulajdonságot titkosítani kell-e a drótba való írás során. A delegált emellett a tulajdonságok titkosítását is lehetővé teszi. (Ha például X, akkor titkosítsa az A tulajdonságot, máskülönben az A és B tulajdonságokat titkosítja.) Az entitások olvasása vagy lekérdezése során nem szükséges megadni ezeket az információkat.

Az egyesítés jelenleg nem támogatott. Mivel a tulajdonságok egy részhalmaza korábban egy másik kulccsal lett titkosítva, egyszerűen az új tulajdonságok egyesítése és a metaadatok frissítése adatvesztést eredményezhet. Az egyesítéshez szükség van további szolgáltatási hívásokra, hogy beolvassa a szolgáltatásból a már meglévő entitást, vagy egy új kulcsot használjon egy tulajdonságban, amelyek közül mindkettő nem alkalmas a teljesítményre.     

További információ a táblák adatainak titkosításáról: [ügyféloldali titkosítás és Azure Key Vault Microsoft Azure Storagehoz](../storage/common/storage-client-side-encryption.md).  

## <a name="modeling-relationships"></a>Kapcsolatok modellezése
A tartományi modellek kiépítése kulcsfontosságú lépés a komplex rendszerek kialakításában. Általában a modellezési folyamattal azonosíthatja az entitásokat és a köztük lévő kapcsolatokat az üzleti tartomány megismerése érdekében, és tájékoztatja a rendszer kialakításáról. Ez a szakasz azt ismerteti, hogyan lehet lefordítani a tartományi modellekben található általános kapcsolattípus-típusokat a Table service kialakításához. A logikai adatmodellből fizikai NoSQL-alapú adatmodellre való hozzárendelési folyamat különbözik a kapcsolatok adatbázisának tervezésekor használt adatoktól. A rokon adatbázisok kialakítása általában feltételezi a redundancia minimalizálása érdekében optimalizált adatnormalizálás folyamatát, valamint egy deklaratív lekérdezési képességet, amely absztrakt módon ismerteti az adatbázis működésének megvalósítását.  

### <a name="one-to-many-relationships"></a>Egy-a-többhöz kapcsolat
Gyakran fordulnak elő egy-a-többhöz kapcsolat az üzleti tartomány objektumai között: például az egyik részleg számos alkalmazottal rendelkezik. Több módon is megvalósítható egy-a-többhöz kapcsolat a Table serviceban, amelyek az adott forgatókönyvhöz kapcsolódó előnyökkel és hátrányokkal rendelkeznek.  

Vegyünk példaként egy nagyméretű, többnemzetiségű vállalatot, amely több tízezer részleget és alkalmazotti entitást tartalmaz, ahol minden részlegnek számos alkalmazottja van, és mindegyik alkalmazott egy adott részleghez társítva van. Az egyik módszer a különálló részleg és az alkalmazotti entitások (például a következők) tárolása:  

![Részleg és alkalmazotti entitás][1]

Ez a példa egy implicit, egy-a-többhöz kapcsolatot mutat be a típusok között a **PartitionKey** érték alapján. Minden részlegnek számos alkalmazottja lehet.  

Ez a példa egy részleg entitást és a hozzá tartozó alkalmazott entitásokat is megjeleníti ugyanabban a partícióban. Dönthet úgy, hogy különböző partíciókat, táblákat vagy akár tárolási fiókokat is használhat a különböző entitások típusaihoz.  

Egy másik megoldás az, hogy denormalizálja az adatait, és csak az alkalmazott entitásokat tárolja a denormalizált részleg adataival az alábbi példában látható módon. Ebben az adott esetben ez a normalizált megközelítés nem a legjobb megoldás, ha a Department Manager részletes adatainak módosítására van szükség, mivel ehhez frissítenie kell a részleg összes alkalmazottját.  

![Alkalmazotti entitás][2]

További információ: a [denormalizálás mintája](#denormalization-pattern) az útmutató későbbi részében.  

Az alábbi táblázat összefoglalja a fentiekben ismertetett megközelítések előnyeit és hátrányait, amelyek egy-a-többhöz kapcsolattal rendelkező alkalmazotti és részlegi entitások tárolására szolgálnak. Azt is érdemes figyelembe venni, hogy milyen gyakran várható a különböző műveletek végrehajtása: elfogadható, ha olyan kialakítással rendelkezik, amely költséges műveletet tartalmaz, ha a művelet csak ritkán fordul elő.  

<table>
<tr>
<th>Megközelítés</th>
<th>Szakemberek</th>
<th>Hátrányok</th>
</tr>
<tr>
<td>Különálló entitások típusai, azonos partíció, azonos tábla</td>
<td>
<ul>
<li>Egyetlen művelettel frissítheti a részleg entitásait.</li>
<li>Az EGT segítségével megtarthatja az egységességet, ha az alkalmazotti entitás frissítésekor/beszúrásakor/törlésekor követelmény, hogy módosítania kell egy részleg entitását. Ha például az egyes részlegek esetében fenntart egy részleg alkalmazottainak a darabszámát.</li>
</ul>
</td>
<td>
<ul>
<li>Előfordulhat, hogy egy alkalmazott és egy részleg entitást is le kell kérnie néhány ügyfél-tevékenységhez.</li>
<li>A tárolási műveletek ugyanabban a partícióban történnek. A nagy tranzakciós kötetek esetében ez egy hotspotot eredményezhet.</li>
<li>EGT használatával nem helyezhető át egy alkalmazott egy új részlegbe.</li>
</ul>
</td>
</tr>
<tr>
<td>Különálló entitások típusai, különböző partíciók vagy táblák vagy Storage-fiókok</td>
<td>
<ul>
<li>Egyetlen művelettel frissítheti a részleg entitásait vagy az alkalmazott entitásokat.</li>
<li>A nagy tranzakciós kötetek esetében ez segíthet a terhelés több partíción való elosztásában.</li>
</ul>
</td>
<td>
<ul>
<li>Előfordulhat, hogy egy alkalmazott és egy részleg entitást is le kell kérnie néhány ügyfél-tevékenységhez.</li>
<li>A EGTs nem használható a konzisztencia fenntartására egy alkalmazott frissítésekor/behelyezése/törlése és a részleg frissítése során. Például egy részleg entitásban lévő alkalmazottak számának frissítése.</li>
<li>EGT használatával nem helyezhető át egy alkalmazott egy új részlegbe.</li>
</ul>
</td>
</tr>
<tr>
<td>Normalizálás egyetlen entitás típusúra</td>
<td>
<ul>
<li>Az összes szükséges információt egyetlen kérelemmel kérheti le.</li>
</ul>
</td>
<td>
<ul>
<li>Költséges lehet a konzisztencia fenntartása, ha frissítenie kell a részleg adatait (ez a részleg összes alkalmazottjának frissítését igényli).</li>
</ul>
</td>
</tr>
</table>

A lehetőségek közül választhat, és az előnyeit és hátrányait a legjelentősebb mértékben az adott alkalmazási helyzettől függ. Például azt, hogy milyen gyakran módosítja a részleg entitásait; az összes alkalmazotti lekérdezésnek szüksége van a további tanszéki információkra; Hogyan közelíti meg a partíciók skálázhatósági korlátait vagy a Storage-fiókját?  

### <a name="one-to-one-relationships"></a>Egy-az-egyhez kapcsolat
A tartományi modellek tartalmazhatnak egy-az-egyhez kapcsolatot az entitások között. Ha egy-az-egyhez kapcsolatot kell létrehoznia a Table serviceban, azt is ki kell választania, hogyan csatolja a két kapcsolódó entitást, ha mindkettőt le kell kérnie. Ez a hivatkozás implicit lehet, a kulcs értékeinek konvenciója alapján, vagy explicit módon, ha egy hivatkozást a **PartitionKey** és a **RowKey** értékek formájában tárol az egyes entitásokban a kapcsolódó entitáshoz. Ha meg szeretné tudni, hogy a kapcsolódó entitásokat ugyanabban a partícióban kell tárolnia, tekintse meg az [egy-a-többhöz kapcsolatok](#one-to-many-relationships)című szakaszt.  

Olyan implementációs megfontolások is megtalálhatók, amelyek egy-az-egyhez kapcsolatok megvalósítására vezethetnek a Table serviceban:  

* Nagyméretű entitások kezeléséhez (További információ: [nagyméretű entitások mintája](#large-entities-pattern)).  
* Hozzáférés-vezérlések implementálása (További információ: [hozzáférés szabályozása közös hozzáférési aláírásokkal](#controlling-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Csatlakozás az ügyfélen
Bár a Table serviceban a kapcsolatok modellezése is lehetséges, ne feledje, hogy a Table service használatának két fő oka a méretezhetőség és a teljesítmény. Ha úgy találja, hogy számos olyan kapcsolatot modellez, amely veszélyezteti a megoldás teljesítményét és méretezhetőségét, meg kell kérdezni, hogy szükség van-e az összes adatkapcsolat létrehozására a tábla kialakításában. Lehet, hogy leegyszerűsíti a kialakítást, és javítja a megoldás méretezhetőségét és teljesítményét, ha lehetővé teszi, hogy az ügyfélalkalmazás bármilyen szükséges illesztést végezzen.  

Ha például olyan kis táblákat használ, amelyek gyakran nem változnak, akkor ezeket az adatfájlokat egyszer kell lekérnie, és az ügyfélen kell gyorsítótárazni. Ezzel elkerülhető, hogy az ismétlődő adatpontok ugyanazt az adatlekérdezést tudják lekérni. A jelen Útmutatóban bemutatott példákban a kisméretű szervezet részlegei valószínűleg kicsik lesznek, és ritkán változnak, hogy az ügyfélalkalmazás csak egyszer tölthető le, és keresési adatként gyorsítótárazza azokat.  

### <a name="inheritance-relationships"></a>Öröklési kapcsolatok
Ha az ügyfélalkalmazás olyan osztályok készletét használja, amelyek egy öröklési kapcsolat részét képezik az üzleti entitások képviseletére, egyszerűen megtarthatja ezeket az entitásokat a Table service. Előfordulhat például, hogy a következő osztályok vannak meghatározva az ügyfélalkalmazásban, ahol a **személy** absztrakt osztály.

![Az öröklési kapcsolatok diagramja][3]

A két konkrét osztály példányait megtarthatja a Table service egyetlen személy tábla használatával a következőhöz hasonló entitások használatával:  

![Az ügyfél entitás és az alkalmazott entitás ábrája][4]

Ha többet szeretne megtudni arról, hogyan használható több entitás típusa ugyanabban a táblában az ügyfél kódjában, tekintse meg az útmutató későbbi, [heterogén entitások típusának használata](#working-with-heterogeneous-entity-types) című szakaszát. Ez példákat tartalmaz arra, hogyan ismerhető fel az entitás típusa az ügyfél kódjában.  

## <a name="table-design-patterns"></a>Tábla kialakítási mintái
Az előző részekben néhány részletes vitát látott arról, hogyan optimalizálhatja a tábla kialakítását az entitások adatainak lekérdezésekkel való beolvasásához, illetve az entitások adatainak beszúrásához, frissítéséhez és törléséhez. Ez a szakasz a Table service-megoldásokkal való használatra alkalmas mintákat ismerteti. Emellett azt is látni fogja, hogy miként lehet gyakorlatilag az útmutató korábbi részében ismertetett problémákat és kompromisszumokat kezelni. A következő ábra összefoglalja a különböző minták közötti kapcsolatokat:  

![A tábla kialakítási mintáinak képe][5]

A fenti minta-hozzárendelés kiemeli az útmutatóban ismertetett minták (kék) és a (narancssárga) mintázatok közötti kapcsolatokat. Természetesen számos más mintát érdemes figyelembe venni. A Table Service egyik fő forgatókönyve például az, ha a [(z) parancs lekérdezési felelősségének elkülönítése (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) mintából származó, az [anyagon alapuló megtekintési mintát](https://msdn.microsoft.com/library/azure/dn589782.aspx) használja.  

### <a name="intra-partition-secondary-index-pattern"></a>Partíción belüli másodlagos index minta
Az egyes entitások több példányát is tárolhatja különböző **RowKey** -értékekkel (ugyanabban a partícióban) a gyors és hatékony keresési és alternatív rendezési sorrendek különböző **RowKey** -értékek használatával történő engedélyezéséhez. A másolatok közötti frissítések konzisztensek lehetnek a EGTs használatával.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A Table service automatikusan indexeli az entitásokat a **PartitionKey** és a **RowKey** értékkel. Ez lehetővé teszi, hogy az ügyfélalkalmazás hatékonyan lekérje az entitásokat ezen értékek használatával. Például az alább látható táblázat struktúrájának használatával az ügyfélalkalmazás egy pont lekérdezéssel kérheti le az egyes alkalmazotti entitásokat a részleg neve és az alkalmazott azonosítója (a **PartitionKey** és a **RowKey** értékek) használatával. Az ügyfelek az egyes részlegeken belül az alkalmazotti azonosító szerint rendezett entitásokat is lekérhetik.

![Alkalmazotti entitás][6]

Ha egy másik tulajdonság (például az e-mail-cím) alapján szeretné megkeresni az alkalmazotti entitást, akkor kevésbé hatékony partíciós vizsgálatot kell használnia, hogy megtalálja a megfelelőt. Ennek az az oka, hogy a Table szolgáltatás nem biztosít másodlagos indexeket. Emellett nincs lehetőség arra, hogy az alkalmazottak listáját a **RowKey** sorrendjében eltérő sorrendbe rendezze.  

#### <a name="solution"></a>Megoldás
A másodlagos indexek hiányának megkerülése érdekében az egyes entitások több példányát is tárolhatja az egyes másolatokkal egy másik **RowKey** érték használatával. Ha az alább látható struktúrákkal rendelkező entitást tárol, akkor az e-mail-cím vagy az alkalmazott azonosítója alapján hatékonyan lekérheti az alkalmazotti entitásokat. A **RowKey**, a "empid_" és a "email_" előtag-értékei lehetővé teszik egy adott alkalmazott vagy egy tartomány egy adott csoportjának lekérdezését az e-mail-címek vagy az alkalmazottak azonosítóinak használatával.  

![Alkalmazott entitás eltérő RowKey-értékekkel][7]

A következő két szűrési feltétel (az alkalmazotti azonosító és egy e-mail-cím alapján megtekintett) a pontok lekérdezéseit is megadja:  

* $filter = (PartitionKey EQ "Sales") és (RowKey EQ "empid_000223")  
* $filter = (PartitionKey EQ "Sales") és (RowKey EQ "email_jonesj@contoso.com")  

Ha az alkalmazotti entitások egy tartományát kérdezi le, megadhatja az alkalmazotti azonosító sorrendbe rendezett tartományt, vagy az e-mail-címek sorrendjét az **RowKey**megfelelő előtaggal rendelkező entitások lekérdezésével.  

* Az értékesítési részleg összes alkalmazottjának megkeresése az 000100 – 000199 tartományba tartozó alkalmazotti azonosítóval: $filter = (PartitionKey EQ "Sales") és (RowKey GE "empid_000100") és (RowKey le "empid_000199")  
* Az értékesítési részleg összes alkalmazottjának megkeresése az "a" betűvel kezdődő e-mail-címmel: $filter = (PartitionKey EQ "Sales") és (RowKey GE "email_a") és (RowKey lt "email_b")  
  
  A fenti példákban használt szűrési szintaxis a Table service REST API, további információ: [lekérdezési entitások](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A Table Storage szolgáltatás viszonylag olcsó ahhoz, hogy az ismétlődő adatokat tároló költségek terhelése ne legyen jelentős aggodalom. A várható tárolási követelmények alapján azonban mindig értékelnie kell a terv költségeit, és csak ismétlődő entitásokat kell hozzáadnia az ügyfélalkalmazás által végrehajtandó lekérdezések támogatásához.  
* Mivel a másodlagos index entitások ugyanabban a partícióban vannak tárolva, mint az eredeti entitások, gondoskodjon arról, hogy ne lépje túl az egyes partíciók méretezhetőségi céljait.  
* A EGTs használatával megtarthatja a duplikált entitások egymással való egységességét, ha az entitás két példányát atomian frissíti. Ez azt jelenti, hogy az entitások összes példányát ugyanabban a partícióban kell tárolnia. További információ: az [Entity Transactions használatával](#entity-group-transactions)foglalkozó szakasz.  
* A **RowKey** használt értéknek egyedinek kell lennie az egyes entitásokhoz. Használjon összetett kulcsos értékeket.  
* A **RowKey** lévő numerikus értékek (például a 000223 alkalmazott azonosítója) kitöltése lehetővé teszi a megfelelő rendezést és szűrést a felső és alsó határok alapján.  
* Nem feltétlenül kell duplikálnia az entitás összes tulajdonságát. Ha például a **RowKey** található e-mail-címmel rendelkező entitásokat megkereső lekérdezésekhez soha nem szükséges az alkalmazott kora, akkor ezek az entitások a következő szerkezettel rendelkezhetnek:

![Alkalmazotti entitás][8]

* Általában jobb az ismétlődő adattárolás, és gondoskodni arról, hogy egyetlen lekérdezéssel kérdezze le az összes szükséges adatkérést, mint ha egyetlen lekérdezés használatával keres egy entitást, és egy másikat a szükséges értékek megkereséséhez.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha az ügyfélalkalmazás számos különböző kulccsal kell lekérnie az entitásokat, amikor az ügyfélnek különböző rendezési sorrendbe kell beolvasnia az entitásokat, és az egyes entitásokat különféle egyedi értékek alapján kell azonosítania. Azonban ügyeljen arra, hogy ne lépje túl a particionálási korlátokat, ha a különböző **RowKey** értékeket használó entitás-keresési műveleteket végez.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Partíciók közötti másodlagos index mintája](#inter-partition-secondary-index-pattern)
* [Összetett kulcs mintája](#compound-key-pattern)
* [Entitás-csoport tranzakciói](#entity-group-transactions)
* [Heterogén entitások típusának használata](#working-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Partíciók közötti másodlagos index mintája
Több példányban tárolhatja az egyes entitásokat különböző **RowKey** -értékekkel külön partíciókban vagy külön táblákban, így a gyors és hatékony keresési és alternatív rendezési sorrendeket különböző **RowKey** -értékek használatával engedélyezheti.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A Table service automatikusan indexeli az entitásokat a **PartitionKey** és a **RowKey** értékkel. Ez lehetővé teszi, hogy az ügyfélalkalmazás hatékonyan lekérje az entitásokat ezen értékek használatával. Például az alább látható táblázat struktúrájának használatával az ügyfélalkalmazás egy pont lekérdezéssel kérheti le az egyes alkalmazotti entitásokat a részleg neve és az alkalmazott azonosítója (a **PartitionKey** és a **RowKey** értékek) használatával. Az ügyfelek az egyes részlegeken belül az alkalmazotti azonosító szerint rendezett entitásokat is lekérhetik.  

![Alkalmazotti entitás][9]

Ha egy másik tulajdonság (például az e-mail-cím) alapján szeretné megkeresni az alkalmazotti entitást, akkor kevésbé hatékony partíciós vizsgálatot kell használnia, hogy megtalálja a megfelelőt. Ennek az az oka, hogy a Table szolgáltatás nem biztosít másodlagos indexeket. Emellett nincs lehetőség arra, hogy az alkalmazottak listáját a **RowKey** sorrendjében eltérő sorrendbe rendezze.  

Nagy mennyiségű tranzakciót számít fel ezen entitások ellen, és csökkenteni szeretné az ügyfelet korlátozó Table servicei arány kockázatát.  

#### <a name="solution"></a>Megoldás
A másodlagos indexek hiányának megkerülése érdekében az egyes entitások több példányát is tárolhatja az egyes példányok különböző **PartitionKey** és **RowKey** értékek használatával. Ha az alább látható struktúrákkal rendelkező entitást tárol, akkor az e-mail-cím vagy az alkalmazott azonosítója alapján hatékonyan lekérheti az alkalmazotti entitásokat. A **PartitionKey**, a "empid_" és a "email_" előtag értékei lehetővé teszik annak azonosítását, hogy melyik indexet szeretné használni a lekérdezésekhez.  

![Alkalmazott entitás elsődleges indextel és alkalmazott entitással másodlagos indexszel][10]

A következő két szűrési feltétel (az alkalmazotti azonosító és egy e-mail-cím alapján megtekintett) a pontok lekérdezéseit is megadja:  

* $filter = (PartitionKey EQ "empid_Sales") és (RowKey EQ "000223")
* $filter = (PartitionKey EQ "email_Sales") és (RowKey EQ "jonesj@contoso.com")  

Ha az alkalmazotti entitások egy tartományát kérdezi le, megadhatja az alkalmazotti azonosító sorrendbe rendezett tartományt, vagy az e-mail-címek sorrendjét az **RowKey**megfelelő előtaggal rendelkező entitások lekérdezésével.  

* Az értékesítési részleg összes alkalmazottjának a **000100** – **000199** tartományba tartozó alkalmazotti azonosítóval történő megkereséséhez használja az alkalmazotti azonosító sorrendjét: $Filter = (PartitionKey EQ "empid_Sales") és (RowKey GE "000100") és (RowKey le "000199")  
* Az értékesítési részleg összes alkalmazottjának megkeresése egy e-mail-címmel, amely az e-mail-címek sorrendjét használja: $filter = (PartitionKey EQ "email_Sales") és (RowKey GE "a") és (RowKey lt "b")  

Vegye figyelembe, hogy a fenti példákban használt szűrési szintaxis a Table service REST API, további információ: [lekérdezési entitások](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Megtarthatja, hogy az ismétlődő entitások végül konzisztensek legyenek egymással, ha a [végül konzisztens tranzakciós mintát](#eventually-consistent-transactions-pattern) használják az elsődleges és a másodlagos index entitások karbantartásához.  
* A Table Storage szolgáltatás viszonylag olcsó ahhoz, hogy az ismétlődő adatokat tároló költségek terhelése ne legyen jelentős aggodalom. A várható tárolási követelmények alapján azonban mindig értékelnie kell a terv költségeit, és csak ismétlődő entitásokat kell hozzáadnia az ügyfélalkalmazás által végrehajtandó lekérdezések támogatásához.  
* A **RowKey** használt értéknek egyedinek kell lennie az egyes entitásokhoz. Használjon összetett kulcsos értékeket.  
* A **RowKey** lévő numerikus értékek (például a 000223 alkalmazott azonosítója) kitöltése lehetővé teszi a megfelelő rendezést és szűrést a felső és alsó határok alapján.  
* Nem feltétlenül kell duplikálnia az entitás összes tulajdonságát. Ha például a **RowKey** e-mail-címét használó entitásokat kereső lekérdezések soha nem szükségesek az alkalmazott korához, akkor ezek az entitások a következő szerkezettel rendelkezhetnek:
  
  ![Alkalmazott entitás másodlagos indexszel][11]
* Általában jobb, ha ismétlődő adattárolást végez, és biztosítja, hogy az összes szükséges, egyetlen lekérdezéssel rendelkező felhasználó lekérdezzen egy olyan entitást, amely a másodlagos index használatával, a másikkal pedig az elsődleges indexben lévő szükséges értékek keresésére használható.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha az ügyfélalkalmazás számos különböző kulccsal kell lekérnie az entitásokat, amikor az ügyfélnek különböző rendezési sorrendbe kell beolvasnia az entitásokat, és az egyes entitásokat különféle egyedi értékek alapján kell azonosítania. Akkor használja ezt a mintát, ha el szeretné kerülni, hogy a partíciók skálázhatósági korlátai meghaladják a különböző **RowKey** -értékekkel rendelkező entitás-keresési értékeket.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern)  
* [Partíción belüli másodlagos index minta](#intra-partition-secondary-index-pattern)  
* [Összetett kulcs mintája](#compound-key-pattern)  
* [Entitás-csoport tranzakciói](#entity-group-transactions)  
* [Heterogén entitások típusának használata](#working-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Végül konzisztens tranzakciós minta
Az Azure Queues használatával engedélyezheti a partíciós határok vagy a tárolási rendszerek határain belüli, végül konzisztens viselkedést.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A EGTs lehetővé teszi az atomi tranzakciók több entitás között, amelyek ugyanazt a partíciós kulcsot használják. A teljesítményre és méretezhetőségre vonatkozó okok miatt dönthet úgy, hogy különálló partíciókban vagy különálló tárolási rendszeren található konzisztencia-követelményeknek megfelelő entitásokat tárol: ilyen esetben nem használhatja a EGTs-t a konzisztencia fenntartásához. Előfordulhat például, hogy meg kell őriznie a végleges konzisztencia fenntartásának követelményét:  

* Ugyanazon tábla két különböző partíciójában, különböző táblákban vagy különböző tárolási fiókokban tárolt entitások.  
* A Table serviceban tárolt entitás és a Blob serviceban tárolt blob.  
* A Table serviceban tárolt entitás és a fájlrendszerben található fájl.  
* Az Table service még indexelt az Azure Cognitive Search szolgáltatással.  

#### <a name="solution"></a>Megoldás
Az Azure Queues használatával olyan megoldást valósíthat meg, amely két vagy több partícióra vagy tárolási rendszerre kiterjedő végleges konzisztenciát biztosít.
Ennek a megközelítésnek a szemléltetéséhez feltételezhető, hogy a régi alkalmazotti entitások archiválására van szükség. A régi alkalmazotti entitásokat ritkán kérdezik le, és ki kell zárni azokat a tevékenységeket, amelyek az aktuális alkalmazottakkal foglalkoznak. Ennek a követelménynek a megvalósításához az **aktuális** táblában és a régi alkalmazottakban tárolt aktív alkalmazottakat kell tárolni az **archív** táblában. Az alkalmazottak archiválásához törölnie kell az entitást az **aktuális** táblából, és fel kell vennie az entitást az **archív** táblába, de nem használhat EGT a két művelet végrehajtásához. Annak elkerülése érdekében, hogy a hiba miatt egy entitás mindkét vagy egyik táblában megjelenjen, az archiválási műveletnek végül konzisztensnek kell lennie. A következő Sequence diagram a művelet lépéseit ismerteti. További részleteket a következő szövegben található kivételi elérési utakhoz biztosítunk.  

![A végleges konzisztencia megoldási diagramja][12]

Az ügyfél az archiválási műveletet úgy indítja el, hogy egy Azure-várólistára helyez egy üzenetet, amely ebben a példában az Employee #456 archiválására vonatkozik. Egy feldolgozói szerepkör lekérdezi az új üzenetek várólistáját; Ha megtalál egyet, beolvassa az üzenetet, és elhagy egy rejtett másolatot a várólistán. A következő feldolgozói szerepkör beolvassa az entitás egy másolatát az **aktuális** táblából, beszúr egy másolatot az **archív** táblába, majd törli az eredetit az **aktuális** táblából. Végül, ha az előző lépések nem voltak hibák, a feldolgozói szerepkör törli a rejtett üzenetet a várólistából.  

Ebben a példában a 4. lépés beszúrja az alkalmazottat az **archív** táblába. Hozzáadhatja az alkalmazottat egy blobhoz a Blob service vagy egy fájlrendszerben.  

#### <a name="recovering-from-failures"></a>Helyreállítás a hibákból
Fontos, hogy a **4** . és az **5** . lépésben szereplő műveleteknek *idempotens* kell lenniük abban az esetben, ha a feldolgozói szerepkörnek újra kell indítania az archiválási műveletet. Ha a Table service használja, a **4** . lépésnél használjon "INSERT vagy replace" műveletet; az **5** . lépésben a használt ügyfél-függvénytár "Törlés, ha létezik" műveletét kell használnia. Ha más tárolási rendszereket használ, megfelelő idempotens műveletet kell használnia.  

Ha a feldolgozói szerepkör soha nem fejezi be a **6**. lépést, akkor időtúllépés után az üzenet ismét megjelenik az üzenetsor számára, hogy a feldolgozói szerepkör megpróbáljon újradolgozni. A feldolgozói szerepkör megtekintheti, hogy a várólistán lévő üzenetek hányszor lettek elolvasva, és ha szükséges, a "méreg" üzenet a vizsgálathoz, hogy elküldje azt egy külön várólistára. A várólista-üzenetek olvasásával és a sorok számának ellenőrzésével kapcsolatos további információkért lásd: [üzenetek](https://msdn.microsoft.com/library/azure/dd179474.aspx)beolvasása.  

A tábla és a várólista-szolgáltatások bizonyos hibái átmeneti hibák, és az ügyfélalkalmazás megfelelő újrapróbálkozási logikát kell tartalmaznia a kezeléséhez.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ez a megoldás nem biztosítja a tranzakciók elkülönítését. Előfordulhat például, hogy egy ügyfél beolvassa az **aktuális** és az **archív** táblát, amikor a feldolgozói szerepkör a **4** . és az **5**. lépés között volt, és az adatok inkonzisztens nézetét látja. Az adategységek végül konzisztensek lesznek.  
* Győződjön meg arról, hogy a 4. és az 5. lépés idempotens a végleges konzisztencia biztosítása érdekében.  
* A megoldás több várólista és feldolgozói szerepkör-példány használatával is méretezhető.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha a különböző partíciókban vagy táblákban található entitások közötti végleges konzisztenciát szeretné garantálni. Ezt a mintát kiterjesztve biztosíthatja a Table service és a Blob service és egyéb nem Azure Storage-adatforrások, például az adatbázis vagy a fájlrendszer közötti műveletek végleges egységességét.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Entitás-csoport tranzakciói](#entity-group-transactions)  
* [Egyesítés vagy csere](#merge-or-replace)  

> [!NOTE]
> Ha a tranzakció elkülönítése fontos a megoldás számára, érdemes megfontolnia a táblák újratervezését, hogy lehetővé váljon a EGTs használata.  
> 
> 

### <a name="index-entities-pattern"></a>Entitások indexelése minta
Az entitások listáját visszaadó hatékony keresések engedélyezéséhez indexelő entitásokat kell fenntartani.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A Table service automatikusan indexeli az entitásokat a **PartitionKey** és a **RowKey** értékkel. Ez lehetővé teszi, hogy az ügyfélalkalmazás hatékonyan kérdezze le az entitásokat egy pont lekérdezéssel. Például az alább látható táblázat struktúrájának használatával az ügyfélalkalmazás hatékonyan lekérheti az egyes alkalmazottak entitásokat a részleg neve és az alkalmazott azonosítója (a **PartitionKey** és a **RowKey**) használatával.  

![Alkalmazotti entitás][13]

Ha azt is szeretné, hogy az alkalmazott entitások listájának lekérése egy másik nem egyedi tulajdonság értéke alapján történjen, például a vezetékneve, akkor kevésbé hatékony partíciós vizsgálatot kell használnia a egyezések kereséséhez, ahelyett, hogy az index használatával közvetlenül megkeresse őket. Ennek az az oka, hogy a Table szolgáltatás nem biztosít másodlagos indexeket.  

#### <a name="solution"></a>Megoldás
Ha a vezetéknevet a fent látható entitás-struktúra szerint szeretné engedélyezni, az alkalmazotti azonosítók listáját kell fenntartania. Ha szeretné lekérni az alkalmazott entitásokat egy adott vezetéknevtel (például Jones), először meg kell keresnie a Jones-beli alkalmazottakhoz tartozó alkalmazotti azonosítók listáját a vezetéknevük alapján, majd le kell kérnie az alkalmazottak entitásait. Az alkalmazotti azonosítók listáját három fő lehetőséggel lehet tárolni:  

* BLOB Storage használata.  
* Hozzon létre index entitásokat az alkalmazott entitásokkal megegyező partícióban.  
* Hozzon létre index entitásokat egy külön partícióban vagy táblában.  

<u>#1 lehetőség: blob Storage használata</u>  

Az első lehetőségnél hozzon létre egy blobot minden egyedi vezetéknevhez, és mindegyik blob tárolja a **PartitionKey** (részleg) és a **RowKey** (alkalmazotti azonosító) értékét a vezetéknevet használó alkalmazottak számára. Egy alkalmazott hozzáadásakor vagy törlésekor gondoskodnia kell arról, hogy az érintett blob tartalma végül konzisztens legyen az alkalmazott entitásokkal.  

<u>#2 lehetőség:</u> Index entitások létrehozása ugyanabban a partícióban  

A második lehetőség esetében használja az indexelő entitásokat, amelyek a következő adategységeket tárolják:  

![Alkalmazotti entitás, amelynek a neve tartalmazza az azonos vezetéknevű alkalmazottak azonosítóit tartalmazó listát][14]

A **EmployeeIDs** tulajdonság a **RowKey**tárolt utolsó névvel rendelkező alkalmazottak alkalmazotti azonosítóinak listáját tartalmazza.  

A következő lépések azt a folyamatot ismertetik, amelyet követnie kell, amikor új alkalmazottat ad hozzá, ha a második lehetőséget használja. Ebben a példában egy 000152 azonosítójú alkalmazottat és egy vezetéknevet adunk hozzá az értékesítési részleghez:  

1. Az index entitás beolvasása az "értékesítés" **PartitionKey** értékkel és a "Jones" **RowKey** értékkel. Mentse az entitás ETag a 2. lépésben való használatra.  
2. Hozzon létre egy Entity Group-tranzakciót (azaz egy batch-műveletet), amely beszúrja az új alkalmazott entitást (**PartitionKey** "Sales" és **RowKey** Value "000152"), és frissíti az index entitást (**PartitionKey** érték "Sales" és **RowKey** a "Jones" érték az új alkalmazott azonosítójának a EmployeeIDs mezőben szereplő listához való hozzáadásával. További információ az Entity Transactions szolgáltatással kapcsolatban: [Entity Transactions](#entity-group-transactions).  
3. Ha az entitás-csoport tranzakciója optimista egyidejűségi hiba miatt meghiúsul (valaki más módosította az index entitást), akkor újra kell kezdenie az 1. lépésben.  

A második lehetőség használata esetén hasonló megközelítést használhat az alkalmazottak törléséhez. Az alkalmazott vezetéknevének módosítása valamivel összetettebb, mert egy olyan Entity Group-tranzakciót kell végrehajtania, amely három entitást frissít: az alkalmazott entitást, a régi vezetéknev index entitását, valamint az új vezetéknevet index entitást. Az egyes entitásokat le kell kérnie a módosítások megkezdése előtt, hogy lekérje a ETag értékeit, amelyek a frissítések optimista párhuzamosságtal történő végrehajtásához használhatók.  

A következő lépések ismertetik azt a folyamatot, amelyet követnie kell, ha a második lehetőség használatakor az összes alkalmazottra vonatkozóan meg kell keresnie egy adott vezetéknevet a részlegen. Ebben a példában a Sales részleg összes alkalmazottját megkeresi a vezetékneve Jones néven:  

1. Az index entitás beolvasása az "értékesítés" **PartitionKey** értékkel és a "Jones" **RowKey** értékkel.  
2. Elemezze az alkalmazotti azonosítók listáját a EmployeeIDs mezőben.  
3. Ha további információra van szüksége az alkalmazottakról (például az e-mail-címekről), a 2. lépésben beszerzett alkalmazottak listájából kérje le az egyes alkalmazottak entitásokat az "értékesítés" **PartitionKey** értékkel, illetve a **RowKey** értékeit.  

<u>#3 lehetőség:</u> Index entitások létrehozása külön partícióban vagy táblázatban  

A harmadik lehetőség esetében használja az indexelő entitásokat, amelyek a következő adategységeket tárolják:  

![Alkalmazotti entitás, amelynek a neve tartalmazza az azonos vezetéknevű alkalmazottak azonosítóit tartalmazó listát][15]

A **EmployeeIDs** tulajdonság a **RowKey**tárolt utolsó névvel rendelkező alkalmazottak alkalmazotti azonosítóinak listáját tartalmazza.  

A harmadik lehetőség esetében a EGTs nem használható a konzisztencia fenntartásához, mert az index entitások az alkalmazott entitások külön partíciójában vannak. Győződjön meg arról, hogy az indexelő entitások végül konzisztensek az alkalmazott entitásokkal.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ehhez a megoldáshoz legalább két lekérdezésre van szükség a megfelelő entitások lekéréséhez: az egyiket, hogy lekérdezze az index entitásokat a **RowKey** -értékek listájának beolvasásához, majd lekérdezi az egyes entitásokat a listában.  
* Mivel az egyes entitások maximális mérete 1 MB, a lehetőség #2 és a megoldás #3 a megoldás feltételezi, hogy az adott vezetéknevhez tartozó alkalmazotti azonosítók listája soha nem nagyobb, mint 1 MB. Ha az alkalmazottak azonosítóinak listája valószínűleg nagyobb, mint 1 MB, használja a #1 lehetőséget, és tárolja az indexet a blob Storage-ban.  
* Ha a (z) #2 lehetőséget használja (az EGTs használatával kezeli az alkalmazottak hozzáadását és törlését, valamint az alkalmazott vezetéknevének módosítását), akkor ki kell értékelnie, hogy a tranzakciók mennyisége megközelíti-e az adott partíció skálázhatósági korlátait. Ebben az esetben érdemes megfontolni egy végül konzisztens megoldást (#1 vagy Option #3), amely várólistákat használ a frissítési kérelmek kezeléséhez, és lehetővé teszi, hogy az index entitásait külön partícióban tárolja az alkalmazott entitásokból.  
* Az ebben a megoldásban #2 beállítás azt feltételezi, hogy a vezetéknevét egy részlegen belül szeretné megkeresni: például szeretné lekérdezni az alkalmazottak listáját az értékesítési részleg vezetékneve Jones névvel. Ha szeretné megkeresni az összes olyan alkalmazottat, aki vezetékneve Jones a teljes szervezeten belül, használja a #1 vagy a Option #3 lehetőséget.
* Megvalósíthat egy üzenetsor-alapú megoldást, amely végleges konzisztenciát biztosít (további részletekért tekintse meg a [végül konzisztens tranzakciók mintáját](#eventually-consistent-transactions-pattern) ).  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha olyan entitások egy készletét szeretné keresni, amelyek mindegyike közös tulajdonságot használ, például az összes olyan alkalmazottat, aki a vezetékneve Jones nevet adja.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcs mintája](#compound-key-pattern)  
* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern)  
* [Entitás-csoport tranzakciói](#entity-group-transactions)  
* [Heterogén entitások típusának használata](#working-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Denormalizálás mintája
A kapcsolódó adategységeket egyetlen entitásban egyesítheti, így egyetlen pont lekérdezéssel kérheti le az összes szükséges adat lekérését.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A kapcsolódó adatbázisokban általában az adatok normalizálása a több táblából származó adatok lekérdezését eredményező lekérdezések ismétlődésének eltávolításához. Ha az adatait az Azure-táblákban normalizálja, akkor a kapcsolódó adatok lekérése érdekében az ügyféltől több oda-vissza kell, hogy beolvassa a kiszolgálót. Az alábbi táblázat szerkezete például két oda-vissza váltásra van szüksége egy részleg adatainak lekéréséhez: az egyiket a felettes azonosítóját tartalmazó részleg entitás beolvasásához, majd egy másik kérést, amely a felettes adatait egy alkalmazott entitásban kéri le.  

![Részleg entitása és alkalmazotti entitása][16]

#### <a name="solution"></a>Megoldás
Ahelyett, hogy két különálló entitásban tárolja az adatokat, denormalizálja az adatokat, és megtartja a felettes adatainak másolatát a részleg entitásban. Például:  

![A denormalizált és a kombinált részleg entitása][17]

Az ezekkel a tulajdonságokkal rendelkező részleg entitásokkal mostantól lekérheti az adott részlegre vonatkozó összes szükséges adatot egy pont lekérdezés használatával.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Az adattárolással kapcsolatos költségek kétszer vannak tárolva. A teljesítmény (amely a tárolási szolgáltatásnak kevesebb kérésből származik) általában meghaladja a tárolási költségek marginális növekedését (és ezt a költséget részben ellensúlyozza a részleg részleteinek beolvasásához szükséges tranzakciók számának csökkenése. ).  
* Meg kell őriznie a két entitás konzisztenciáját, amelyek adatokat tárolnak a kezelők számára. A konzisztencia problémát a EGTs használatával kezelheti egyetlen atomi tranzakcióban: ebben az esetben a részleg entitást és a Department Manager alkalmazott entitását ugyanazon a partíción tárolja a rendszer.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha gyakran kell kapcsolódó információkat keresnie. Ez a minta csökkenti azoknak a lekérdezéseknek a számát, amelyeket az ügyfélnek el kell végeznie a szükséges adatforrások lekéréséhez.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcs mintája](#compound-key-pattern)  
* [Entitás-csoport tranzakciói](#entity-group-transactions)  
* [Heterogén entitások típusának használata](#working-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Összetett kulcs mintája
Az összetett **RowKey** értékek használatával lehetővé teheti, hogy az ügyfél a kapcsolódó adatokra egyetlen pont lekérdezéssel keres.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A kapcsolati adatbázisban természetes, hogy a lekérdezésekben a kapcsolódó adatmennyiségeket egy lekérdezésben a-ügyfélhez való visszatéréshez használja. Az alkalmazotti azonosítóval például megkeresheti a kapcsolódó entitások listáját, amelyek az adott alkalmazott teljesítmény-és felülvizsgálati adatait tartalmazzák.  

Tegyük fel, hogy az alábbi struktúra használatával tárolja az alkalmazott entitásokat a Table serviceban:  

![Alkalmazotti entitás][18]

Emellett a korábbi adatokat is tárolnia kell a felülvizsgálatokhoz és a teljesítményhez az alkalmazottak által a szervezet számára dolgozott munkavégzéshez, és az adatoknak évente kell hozzáférnie. Az egyik lehetőség egy másik tábla létrehozása, amely az entitásokat az alábbi struktúrával tárolja:  

![Alkalmazotti felülvizsgálati entitás][19]

Figyelje meg, hogy ezzel a megközelítéssel eldöntheti, hogy egy bizonyos információt (például utónév és vezetéknév) duplikál az új entitásban, hogy lehetővé tegye az adatok lekérését egyetlen kérelemmel. Azonban nem lehet erős konzisztenciát fenntartani, mert nem használhat EGT a két entitás atomi frissítésére.  

#### <a name="solution"></a>Megoldás
Egy új entitás típusának tárolása az eredeti táblában az alábbi struktúrával rendelkező entitások használatával:  

![Alkalmazott entitás összetett kulccsal][20]

Figyelje meg, hogy a **RowKey** mostantól egy összetett kulcs, amely az alkalmazotti azonosítóból és a felülvizsgálati adat évének számított részét képezi, amely lehetővé teszi az alkalmazott teljesítményének beolvasását és az adatellenőrzést egyetlen entitásra vonatkozó egyetlen kéréssel.  

Az alábbi példa azt ismerteti, hogyan kérhető le egy adott alkalmazott összes felülvizsgálati adata (például 000123 alkalmazott az értékesítési részlegben):  

$filter = (PartitionKey EQ "Sales") és (RowKey GE "empid_000123") és (RowKey lt "empid_000124") & $select = RowKey, felettes minősítés, társ-minősítés, megjegyzések  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Használjon egy megfelelő elválasztó karaktert, amely megkönnyíti a **RowKey** érték elemzését: például **000123_2012**.  
* Ezt az entitást ugyanabban a partícióban tárolja, mint a többi olyan entitást, amely ugyanahhoz az alkalmazotthoz kapcsolódó adatait tartalmazza, ami azt jelenti, hogy a EGTs használatával erős konzisztencia tartható fenn.
* Érdemes megfontolni, hogy milyen gyakran fogja lekérdezni az adatlekérdezést, hogy a minta megfelelő-e.  Ha például az áttekintő adatelemzést ritkán szeretné elérni, és a fő alkalmazottak adatai gyakran külön entitásként lesznek tárolva.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha egy vagy több, gyakran lekérdezett kapcsolódó entitást kell tárolnia.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Entitás-csoport tranzakciói](#entity-group-transactions)  
* [Heterogén entitások típusának használata](#working-with-heterogeneous-entity-types)  
* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Napló farok mintája
A partícióhoz legutóbb hozzáadott *n* entitások lekérése egy olyan **RowKey** -értékkel, amely fordított dátum és idő sorrendbe rendez.  

> [!NOTE]
> A Azure Cosmos DB Azure Table API által visszaadott lekérdezési eredmények nem a partíciós kulcs vagy a sor kulcsa szerint vannak rendezve. Ez a minta tehát az Azure Table Storage számára megfelelő, és nem Azure Cosmos DB. A szolgáltatások közötti különbségek részletes listáját a [Azure Cosmos db és az Azure Table Storage Table API közötti különbségek](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)című részben tekintheti meg.

#### <a name="context-and-problem"></a>Kontextus és probléma
Gyakori követelmény, hogy a legutóbb létrehozott entitásokat, például az alkalmazott által küldött tíz legutóbbi költséget kéri le. A táblázatos lekérdezések egy **$Top** lekérdezési műveletet támogatnak a készlet első *n* entitásának visszaadásához: nincs megfelelő lekérdezési művelet, amely egy készlet utolsó n entitását adja vissza.  

#### <a name="solution"></a>Megoldás
Az entitásokat egy olyan **RowKey** használatával tárolhatja, amely természetes módon rendezi a fordított dátum és idő sorrendjét, így a legújabb bejegyzés mindig az első a táblában.  

Ha például le szeretné kérni az alkalmazott által küldött tíz legutóbbi költséget, az aktuális dátum/idő értékből származtatott fordított osztásjelek értékét is használhatja. A következő C# mintakód azt mutatja be, hogyan hozható létre megfelelő "fordított osztásjelek" érték egy olyan **RowKey** , amely a legutóbbitől a legrégebbiig rendezhető:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

A dátum/idő értékhez a következő kódot használhatja:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A tábla lekérdezése a következőképpen néz ki:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A fordított osztásjelek értékét a bevezető nullákkal kell ellátni, hogy a karakterlánc-érték a várt módon legyen rendezve.  
* A méretezhetőségi célokat a partíció szintjén kell figyelembe venni. Ügyeljen arra, hogy ne hozzon létre gyors elérésű helyet.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha az entitásokat fordított dátum/idő sorrendben kell elérnie, vagy ha a legutóbb hozzáadott entitásokhoz kell hozzáférnie.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Előtag/Hozzáfűzés Anti-pattern](#prepend-append-anti-pattern)  
* [Entitások beolvasása](#retrieving-entities)  

### <a name="high-volume-delete-pattern"></a>Nagy mennyiségű törlési minta
Engedélyezze a nagy mennyiségű entitás törlését úgy, hogy az összes entitást egyidejű törlésre tárolja a saját külön táblában. az entitásokat a tábla törlésével törölheti.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Számos alkalmazás törli azokat a régi adatmennyiségeket, amelyek már nem szükségesek az ügyfélalkalmazás számára, vagy hogy az alkalmazás archiválva lett egy másik tárolóeszközre. Ezeket az adatokat általában dátum alapján azonosítjuk: például a 60 napnál régebbi bejelentkezési kérelmek rekordjainak törlésére van szükség.  

Az egyik lehetséges megoldás a bejelentkezési kérés dátumának és időpontjának használata a **RowKey**:  

![Bejelentkezési kísérlet entitása][21]

Ezzel a módszerrel elkerülhetők a partíciós pontok, mert az alkalmazás beillesztheti és törölheti a bejelentkezési entitásokat az egyes felhasználók számára egy külön partíción. Ez a megközelítés azonban költséges és időigényes lehet, ha nagyszámú entitással rendelkezik, mert először egy táblázatos vizsgálatot kell végeznie a törölni kívánt entitások azonosításához, majd minden régi entitást törölnie kell. A régi entitások törléséhez a EGTs-ba történő több törlési kérelem kötegelt feldolgozásával csökkentheti a lekerekítési utak számát a kiszolgálóra.  

#### <a name="solution"></a>Megoldás
A bejelentkezési kísérletek minden napján külön táblázatot használjon. A fenti entitás kialakításával elkerülheti, hogy az entitások ne legyenek beszúrva, és a régi entitások törlése mostantól csupán egy tábla törlésének kérdése (egyetlen tárolási művelet) a több száz vagy több ezer személy megkeresése és törlése helyett minden nap aláírja az entitásokat.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Az Ön által használt kialakítás más módon támogatja az adatokat, például adott entitások keresését, más adatokkal való összekapcsolást vagy összesített információk létrehozását?  
* Az új entitások beszúrásakor a terv elkerüli a gyors helyeket?  
* Késleltetés, ha a törlés után ugyanazt a táblanév-nevet szeretné használni. Jobb, ha mindig egyedi táblanév-nevet használ.  
* A rendszer az új tábla első használatakor korlátozza az egyes mértékeket, miközben a Table service megtanulja a hozzáférési mintákat, és elosztja a partíciókat a csomópontok között. Érdemes megfontolni, hogy milyen gyakran kell új táblákat létrehoznia.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha nagy mennyiségű entitást kell törölni egyszerre.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Entitás-csoport tranzakciói](#entity-group-transactions)
* [Entitások módosítása](#modifying-entities)  

### <a name="data-series-pattern"></a>Adatsorozat mintája
Egyetlen entitásban tárolhatja a teljes adatsorozatokat, így csökkentve a kérelmek számát.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Gyakori forgatókönyv, hogy egy alkalmazás egy adatsorozatot tárol, amelyet általában egyszerre kell lekérnie. Előfordulhat például, hogy az alkalmazás rögzíti, hogy az egyes alkalmazottak hány ÜZENETKÜLDÉSi üzenetet küldenek óránként, majd ezt az információt felhasználva ábrázolják, hogy az egyes felhasználók hány üzenetet küldenek az előző 24 órában. Az egyik kialakítás lehet 24 entitás tárolása az egyes alkalmazottak számára:  

![Üzenet stats entitása][22]

Ezzel a kialakítással könnyedén megkeresheti és frissítheti az entitásokat, amelyeket frissíteni kell az egyes alkalmazottak számára, amikor az alkalmazásnak frissítenie kell az üzenetek számának értékét. Ahhoz azonban, hogy lekérje a tevékenység diagramjának az előző 24 órában való ábrázolásához szükséges információkat, 24 entitást kell lekérnie.  

#### <a name="solution"></a>Megoldás
Az alábbi kialakítással külön tulajdonsággal tárolhatja az üzenetek darabszámát minden órában:  

![Üzenet-stats entitás elkülönített tulajdonságokkal][23]

Ezzel a kialakítással egy adott órában egy alkalmazotthoz tartozó üzenetek számának frissítése egyesítési művelettel végezhető el. Most lekérheti az összes szükséges információt, hogy a diagramot egyetlen entitásra vonatkozó kérelem használatával ábrázolja.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ha a teljes adatsorozat nem fér el egyetlen entitásba (egy entitás legfeljebb 252 tulajdonsággal rendelkezhet), használjon másik adattárat, például egy blobot.  
* Ha több ügyfél is frissít egyszerre egy entitást, akkor a **ETAG** kell használnia az optimista Egyidejűség megvalósításához. Ha sok ügyfele van, nagy mértékben megtapasztalhatja a tartalmat.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha egy adott entitáshoz társított adatsorozatot kell frissítenie és beolvasnia.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Nagyméretű entitások mintája](#large-entities-pattern)  
* [Egyesítés vagy csere](#merge-or-replace)  
* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern) (ha az adatsorozatot egy blobban tárolja)  

### <a name="wide-entities-pattern"></a>Széles entitások mintája
Több mint 252 tulajdonságú logikai entitások tárolására több fizikai entitást használjon.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Az egyes entitások legfeljebb 252 tulajdonsággal rendelkezhetnek (kivéve a kötelező rendszertulajdonságokat), és összesen legfeljebb 1 MB adatmennyiséget tárolhatnak. A relációs adatbázisokban általában a sorok méretére vonatkozó korlátokat kell felvennie egy új tábla hozzáadásával, és egy-az-1 közötti kapcsolat kényszerítésével.  

#### <a name="solution"></a>Megoldás
A Table service használatával több entitást is tárolhat, hogy egyetlen nagy üzleti objektumot, több mint 252 tulajdonságot képvisel. Ha például az elmúlt 365 napban az egyes alkalmazottak által küldött IM-üzenetek számának számát szeretné tárolni, akkor a következő kialakítást használhatja, amely két, különböző sémákkal rendelkező entitást használ:  

![Üzenet-stats entitás Rowkey 01-es és Rowkey 02-es üzenet-állapottal rendelkező entitással][24]

Ha olyan módosítást kell végeznie, amelyhez mindkét entitást frissíteni kell, hogy azok szinkronizálva maradjanak egymással, használhat egy EGT. Ellenkező esetben egyetlen egyesítési művelettel frissítheti az üzenetek darabszámát egy adott napra vonatkozóan. Egy adott alkalmazott összes értékének lekéréséhez mindkét entitást le kell kérnie, amely két, **PartitionKey** és **RowKey** értéket használó hatékony kéréssel végezhető el.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A teljes logikai entitások beolvasása legalább két tárolási tranzakciót foglal magában: egyet az egyes fizikai entitások lekéréséhez.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha olyan entitásokat kell tárolnia, amelyek mérete vagy tulajdonságai meghaladják a Table service egyes entitásának korlátait.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Entitás-csoport tranzakciói](#entity-group-transactions)
* [Egyesítés vagy csere](#merge-or-replace)

### <a name="large-entities-pattern"></a>Nagyméretű entitások mintája
Nagyméretű tulajdonságértékek tárolására használjon blob Storage-t.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Az egyes entitások összesen legfeljebb 1 MB adatmennyiséget tárolhatnak. Ha a tulajdonságok közül egy vagy több olyan értéket tárol, amely az entitás teljes méretét eredményezi, akkor a Table serviceban nem lehet a teljes entitást tárolni.  

#### <a name="solution"></a>Megoldás
Ha az entitás mérete meghaladja az 1 MB-ot, mert egy vagy több tulajdonság nagy mennyiségű adatmennyiséget tartalmaz, akkor a Blob service tárolhatja az adatait, majd a blob címeit az entitás egyik tulajdonságában tárolhatja. Tárolhat például egy alkalmazott fényképét a blob Storage-ban, és a fényképre mutató hivatkozást tárolhat az alkalmazotti entitás **Photo (fénykép** ) tulajdonságában:  

![Alkalmazotti entitás a blob Storage-ra mutató fényképhez tartozó karakterlánccal][25]

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A Table service és az Blob serviceban lévő entitások közötti állandó konzisztencia fenntartása érdekében a [végül konzisztens tranzakciós mintát](#eventually-consistent-transactions-pattern) használja az entitások karbantartásához.
* A teljes entitások beolvasása legalább két tárolási tranzakciót foglal magában: egyet az entitás lekéréséhez, egyet pedig a blob adatainak lekéréséhez.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha olyan entitásokat kell tárolnia, amelyek mérete meghaladja a Table service egyes entitásának korlátait.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Végül konzisztens tranzakciós minta](#eventually-consistent-transactions-pattern)  
* [Széles entitások mintája](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Előtag/Hozzáfűzés Anti-pattern
Növelje a méretezhetőséget, ha nagy mennyiségű lapkákkal rendelkezik a lapkák több partíción való elosztásával.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Az entitások a tárolt entitások számára történő előállítása vagy hozzáfűzése általában azt eredményezi, hogy az alkalmazás új entitásokat ad hozzá a partíciók egy sorozatából lévő első vagy utolsó partícióhoz. Ebben az esetben az összes beillesztett adat ugyanabban a partícióban zajlik, és egy olyan hotspotot hoz létre, amely megakadályozza, hogy a Table szolgáltatás terheléselosztási lapkákat helyezzen el több csomóponton, és hogy az alkalmazás elérje a méretezhetőségi célokat partíció. Ha például olyan alkalmazással rendelkezik, amely az alkalmazottak hálózati és erőforrás-hozzáférését naplózza, akkor az alábbi ábrán látható entitás-struktúra azt eredményezheti, hogy az aktuális óra partíciója válik elérhetővé, ha a tranzakciók mennyisége eléri a méretezhetőségi célt Egyéni partíció:  

![Alkalmazotti entitás][26]

#### <a name="solution"></a>Megoldás
A következő alternatív entitás-struktúra elkerüli a hotspotot egy adott partíción, amikor az alkalmazás naplózza az eseményeket:  

![Alkalmazotti entitás az év, hónap, nap, óra és eseményazonosító összetételével RowKey][27]

Figyelje meg ezt a példát, hogy a **PartitionKey** és a **RowKey** is összetett kulcsok legyenek. A **PartitionKey** mind a részleg, mind az alkalmazott azonosítója használatával osztja szét a naplózást több partíció között.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Az alternatív kulcs szerkezete, amely elkerüli a gyakori partíciók gyors létrehozását a lapkákon, hatékonyan támogatja az ügyfélalkalmazás által nyújtott lekérdezéseket?  
* Az Ön várható tranzakciós mennyisége azt jelenti, hogy valószínűleg el fogja érni az egyes partíciók méretezhetőségi céljait, és a tárolási szolgáltatás szabályozza azt?  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Kerülje a küldési/hozzáfűzési mintázatot, ha a tranzakciós mennyiség valószínűleg a tárolási szolgáltatás által a forró partícióhoz való hozzáférés miatti korlátozást eredményez.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcs mintája](#compound-key-pattern)  
* [Napló farok mintája](#log-tail-pattern)  
* [Entitások módosítása](#modifying-entities)  

### <a name="log-data-anti-pattern"></a>Adatnapló adattípusa
A naplófájlok tárolásához általában a Table service helyett a Blob service kell használnia.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A naplózási adatok általános használati esete egy adott dátum-és időtartományhoz tartozó naplóbejegyzések beolvasása. például szeretné megkeresni az alkalmazás által a 15:04 és a 15:06 között az adott napon naplózott összes hibát és kritikus üzenetet. Nem kívánja használni a naplófájl dátumát és időpontját, hogy meghatározza azt a partíciót, amelybe a napló entitásokat menteni kívánja: Ez a művelet egy gyors partíciót eredményez, mivel az összes napló entitás ugyanazt a **PartitionKey** -értéket fogja megosztani (lásd a következő szakaszt: előtag [/ fűzze hozzá](#prepend-append-anti-pattern)a következőt: Anti-pattern). A naplófájl következő entitás-sémája például egy gyors partíciót eredményez, mivel az alkalmazás az összes naplózási üzenetet az aktuális dátumra és órára írja a partícióra:  

![Üzenet naplózása entitás][28]

Ebben a példában a **RowKey** tartalmazza a napló üzenetének dátumát és időpontját, így biztosítva, hogy a rendszer a naplófájlokat dátum/idő sorrendbe rendezi, és tartalmazza az üzenet azonosítóját abban az esetben, ha több naplófájl ugyanazt a dátumot és időpontot használja.  

Egy másik módszer egy olyan **PartitionKey** használata, amely biztosítja, hogy az alkalmazás üzeneteket írjon a különböző partíciók között. Ha például a naplófájl forrása lehetővé teszi az üzenetek több partíción keresztüli terjesztését, a következő entitás-sémát használhatja:  

![Üzenet naplózása entitás][29]

A séma problémája azonban az, hogy egy adott időtartomány összes naplófájljának lekéréséhez a táblában lévő összes partíción keresnie kell.

#### <a name="solution"></a>Megoldás
Az előző szakasz rámutatott arra a problémára, amellyel a Table service a naplóbejegyzések tárolására, és javasolt két, nem kielégítő, formatervezési minták használatára. Az egyik megoldás egy olyan gyors partíciót eredményezett, amely gyenge teljesítményű írási naplót jelent. a másik megoldás rossz lekérdezési teljesítményt eredményezett, mert a táblázatban lévő összes partíció vizsgálatára vonatkozó követelmény egy adott időtartományhoz tartozó naplófájlok beolvasásához szükséges. A blob Storage jobb megoldást kínál az ilyen típusú forgatókönyvekhez, és így Azure Storage Analytics tárolja az általa gyűjtött napló adatait.  

Ez a szakasz azt ismerteti, hogyan tárolja a Storage Analytics a blob Storage-ban tárolt naplófájlokat az olyan adattárolási módszer szemléltetésére, amelyet általában a tartomány alapján kérdez le.  

Storage Analytics a naplófájlokat több blobban tagolt formátumban tárolja. A tagolt formátum megkönnyíti az ügyfélalkalmazás számára a naplófájlban lévő adatelemzést.  

Storage Analytics a Blobok elnevezési konvenciójának használatával megkeresheti azokat a blobokat (vagy blobokat), amelyek a keresett naplófájlokat tartalmazzák. Egy "üzenetsor/2014/07/31/1800/000001. log" nevű blob például a üzenetsor-szolgáltatáshoz kapcsolódó naplófájlokat tartalmaz, amelyek az óra 18:00. július 31-ig, 2014-kor kezdődnek. A "000001" azt jelzi, hogy ez az időszak első naplófájlja. A Storage Analytics a fájlban tárolt első és utolsó naplózási üzenetek időbélyegét is rögzíti a blob metaadatainak részeként. A blob Storage-hoz készült API lehetővé teszi, hogy megkeresse a tárolóban lévő blobokat egy név előtag alapján: Ha meg szeretné keresni az összes olyan blobot, amely üzenetsor-naplózási adathalmazt tartalmaz, az óra 18:00-től kezdődően a "üzenetsor/2014/07/31/1800" előtagot használhatja.  

Storage Analytics pufferek belső naplófájlokat küldenek, majd rendszeresen frissítik a megfelelő blobot, vagy létrehoznak egy újat a naplóbejegyzések legújabb kötegével. Ez csökkenti a blob szolgáltatásnak elvégezhető írások számát.  

Ha a saját alkalmazásában hasonló megoldást valósít meg, meg kell fontolnia, hogyan kezelheti a megbízhatóságot (az összes naplóbejegyzést a blob Storage-ba, ahogy ez történik), valamint a költséghatékonyságot és méretezhetőséget (az alkalmazásban és az írásban lévő bejegyzések pufferelése). a kötegekben lévő blob Storage-ba.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A naplófájlok tárolási módjának meghatározásakor vegye figyelembe a következő szempontokat:  

* Ha olyan tábla-kialakítást hoz létre, amely elkerüli a lehetséges gyors partíciókat, előfordulhat, hogy a rendszer nem fér hozzá a napló adataihoz.  
* A naplófájlok feldolgozásához az ügyfeleknek gyakran sok rekordot kell betölteni.  
* Bár a naplózási adatként gyakran strukturált, a blob Storage lehet jobb megoldás.  

### <a name="implementation-considerations"></a>Implementálási szempontok
Ez a szakasz azokat a szempontokat ismerteti, amelyeket figyelembe kell venni az előző szakaszokban leírt mintázatok megvalósításakor. Ennek a szakasznak a nagy része a C# Storage ügyféloldali kódtárat (az írás időpontjában 4.3.0 verziót) használó példákat használja.  

### <a name="retrieving-entities"></a>Entitások beolvasása
Ahogy az a [lekérdezési](#design-for-querying)szakaszban is látható, a leghatékonyabb lekérdezés a pont lekérdezése. Bizonyos esetekben azonban előfordulhat, hogy több entitást kell lekérnie. Ez a szakasz néhány gyakori megközelítést ismertet az entitások tárolási ügyféloldali kódtár használatával történő beolvasásához.  

#### <a name="executing-a-point-query-using-the-storage-client-library"></a>Pont lekérdezés végrehajtása a Storage ügyféloldali kódtár használatával
A pontok lekérdezésének legegyszerűbb módja, ha a táblázat **lekérése** műveletet használja, ahogy az a következő C# kódrészletben látható, amely egy "Sales" értékű **PartitionKey** és egy "212" értékű **RowKey** -értéket kér le:  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Figyelje meg, hogy ez a példa arra vár, hogy a beolvasott entitás **EmployeeEntity**típusú legyen.  

#### <a name="retrieving-multiple-entities-using-linq"></a>Több entitás beolvasása a LINQ használatával
A LINQ és a Storage ügyféloldali kódtár használatával több entitást is lekérhet, és egy **Where** záradékkal rendelkező lekérdezést is megadhat. A táblák vizsgálatának elkerüléséhez mindig adja meg a **PartitionKey** értékét a WHERE záradékban, és ha lehetséges, akkor a **RowKey** értéket a tábla és a partíció ellenőrzésének elkerülése érdekében. A Table szolgáltatás támogatja az összehasonlító operátorok korlátozott készletét (nagyobb, mint, nagyobb vagy egyenlő, kisebb, mint, kisebb vagy egyenlő, egyenlő és nem egyenlő) a WHERE záradékban való használathoz. A következő C# kódrészlet megkeresi az összes olyan alkalmazottat, akinek vezetékneve "B" karakterrel kezdődik (feltéve, hogy a **RowKey** az utolsó nevet tárolja) az értékesítési részlegben (feltéve, hogy a **PartitionKey** a részleg nevét tárolja):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Figyelje meg, hogyan határozza meg a lekérdezés a **RowKey** és a **PartitionKey** is a jobb teljesítmény érdekében.  

Az alábbi mintakód egyenértékű funkciókat mutat be a fluent API használatával (az általános Fluent API-kkal kapcsolatos további információkért lásd: [ajánlott eljárások a FLUENT API kialakításához](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

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
> A minta több **CombineFilters** metódust ágyaz be, hogy tartalmazza a három szűrési feltételt.  
> 
> 

#### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Nagy számú entitás beolvasása egy lekérdezésből
Az optimális lekérdezés egy egyedi entitást ad vissza egy **PartitionKey** érték és egy **RowKey** érték alapján. Bizonyos esetekben azonban előfordulhat, hogy számos entitást kell visszaadnia ugyanabból a partícióból, vagy akár sok partícióból is.  

Az alkalmazás teljesítményét mindig teljes mértékben tesztelheti ilyen esetekben.  

A Table szolgáltatásra irányuló lekérdezés egyszerre legfeljebb 1 000 entitást adhat vissza, és legfeljebb öt másodpercig futhat. Ha az eredményhalmaz több mint 1 000 entitást tartalmaz, ha a lekérdezés nem fejeződött be öt másodpercen belül, vagy ha a lekérdezés átlépi a partíció határát, a Table service egy folytatási tokent ad vissza, amely lehetővé teszi, hogy az ügyfélalkalmazás a következő készletet igényelje. A folytatási tokenek működésével kapcsolatos további információkért lásd: [lekérdezési időkorlát és tördelés](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Ha a Storage ügyféloldali kódtárat használja, akkor automatikusan képes kezelni a folytatási jogkivonatokat, mert az entitásokat visszaadja a Table serviceból. A Storage C# ügyféloldali kódtárat használó következő mintakód automatikusan kezeli a folytatási jogkivonatokat, ha a Table szolgáltatás válaszként visszaadja őket:  

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

A következő C# kód explicit módon kezeli a folytatási jogkivonatokat:  

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

A folytatási tokenek explicit módon történő használatával szabályozhatja, hogy az alkalmazás Mikor kéri le a következő adatszegmenst. Ha például az ügyfélalkalmazás lehetővé teszi a felhasználók számára, hogy a táblázatban tárolt entitásokon keresztül Lapozzák fel a felhasználókat, a felhasználók dönthetnek úgy, hogy a lekérdezés által lekért összes entitáson át nem teszik a lapot, így az alkalmazás csak a folytatási tokent használja a következő szegmens lekéréséhez, amikor a felhasználó az aktuális szegmens összes entitásán befejezte a lapozást. Ez a megközelítés számos előnnyel jár:  

* Ez lehetővé teszi, hogy korlátozza a lekérdezni kívánt adatok mennyiségét a Table serviceból, és a hálózaton halad át.  
* Lehetővé teszi, hogy aszinkron IO-t végezzen a .NET-ben.  
* Lehetővé teszi a folytatási jogkivonat szerializálását állandó tárterületre, hogy az alkalmazás összeomlása esetén is folytatódjon.  

> [!NOTE]
> A folytatási token általában egy 1 000 entitást tartalmazó szegmenst ad vissza, bár kevesebb lehet. Ez abban az esetben is igaz, ha a lekérdezés által visszaadott bejegyzések számát korlátozza a keresési feltételeknek megfelelő első n entitások visszaadásához: a Table szolgáltatás olyan szegmenst adhat vissza, amely kevesebb mint n entitást tartalmaz, és a folytatási tokent tartalmazza lehetővé teszi a fennmaradó entitások beolvasását.  
> 
> 

A következő C# kód bemutatja, hogyan módosítható a szegmensen belül visszaadott entitások száma:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Kiszolgálóoldali kivetítés
Egyetlen entitás legfeljebb 255 tulajdonsággal rendelkezhet, és legfeljebb 1 MB méretű lehet. Ha lekérdezi a táblázatot, és beolvassa az entitásokat, előfordulhat, hogy nincs szüksége az összes tulajdonságra, és szükségtelenül nem tudja átvinni az adatátvitelt (a késés és a Cost csökkentése érdekében). Kiszolgálóoldali leképezés használatával csak a szükséges tulajdonságokat viheti át. A következő példa lekéri az **e-mail** -tulajdonságot (valamint a **PartitionKey**, a **RowKey**, a **timestamp**és a **ETAG**) a lekérdezés által kiválasztott entitásokból.  

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

Figyelje meg, hogyan érhető el a **RowKey** értéke annak ellenére, hogy nem szerepel a lekérdezni kívánt tulajdonságok listáján.  

### <a name="modifying-entities"></a>Entitások módosítása
A Storage ügyféloldali kódtára lehetővé teszi, hogy az entitások beszúrásával, törlésével és frissítésével módosítsa a Table szolgáltatásban tárolt entitásokat. A EGTs használatával több beszúrási, frissítési és törlési műveletet is felhasználhat, hogy csökkentse a szükséges lekerekítési utak számát, és javítsa a megoldás teljesítményét.  

Kivételek történtek, ha a Storage ügyféloldali kódtár egy EGT hajt végre, jellemzően tartalmazza a köteget okozó entitás indexét. Ez akkor hasznos, ha az EGTs-t használó kódot hibakeresést végez.  

Azt is gondolja át, hogy a terv hogyan befolyásolja az ügyfélalkalmazás a párhuzamossági és frissítési műveleteket.  

#### <a name="managing-concurrency"></a>Az egyidejűség kezelése
Alapértelmezés szerint a Table szolgáltatás a **beszúrási**, **egyesítési**és **törlési** műveletek szintjén hajtja végre az optimista egyidejűségi ellenőrzéseket, bár lehetséges, hogy az ügyfél kényszeríti a Table szolgáltatást, hogy megkerülje ezeket ellenőrzi. További információ arról, hogyan kezeli a Table szolgáltatás a párhuzamosságot: az [Egyidejűség kezelése Microsoft Azure Storageban](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Egyesítés vagy csere
A **TableOperation** osztály **replace** metódusa mindig a Table Service teljes entitását váltja fel. Ha nem tartalmaz tulajdonságot a kérelemben, ha az adott tulajdonság létezik a tárolt entitásban, a kérelem eltávolítja a tulajdonságot a tárolt entitásból. Hacsak nem szeretne explicit módon eltávolítani egy tulajdonságot egy tárolt entitásból, a kérelemben szereplő összes tulajdonságot fel kell vennie.  

A **TableOperation** osztály **egyesítési** metódusával csökkentheti a Table Service küldött adatmennyiséget, ha egy entitást szeretne frissíteni. Az **egyesítési** módszer lecseréli a tárolt entitás bármely tulajdonságát a kérelemben szereplő entitás tulajdonságértékek értékével, de érintetlenül hagy minden olyan tulajdonságot, amely nem szerepel a kérelemben. Ez akkor hasznos, ha nagyméretű entitásokkal rendelkezik, és csak kis számú tulajdonságot kell frissítenie egy kérelemben.  

> [!NOTE]
> A **replace** és az **merge** metódus meghiúsul, ha az entitás nem létezik. Másik lehetőségként használhatja a **InsertOrReplace** és a **InsertOrMerge** metódust, amely új entitást hoz létre, ha az nem létezik.  
> 
> 

### <a name="working-with-heterogeneous-entity-types"></a>Heterogén entitások típusának használata
Az Table service egy *séma nélküli tábla-* áruház, amely azt jelenti, hogy egyetlen tábla képes több típusú entitás tárolására, amelyek nagy rugalmasságot biztosítanak a kialakításban. Az alábbi példa egy olyan táblázatot mutat be, amely az alkalmazottak és a részleg entitásait tárolja:  

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
<th>firstName</th>
<th>lastName</th>
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
<th>firstName</th>
<th>lastName</th>
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
<th>firstName</th>
<th>lastName</th>
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

Minden entitásnak továbbra is **PartitionKey**, **RowKey**és **timestamp** értékekkel kell rendelkeznie, de bármilyen tulajdonsága lehet. Ezen kívül nincs lehetőség arra, hogy az entitás típusát jelezze, ha nem úgy dönt, hogy valahol tárolja ezt az információt. Az entitás típusának azonosítására két lehetőség áll rendelkezésre:  

* Adja meg az entitás típusát a **RowKey** (vagy esetleg a **PartitionKey**). Például **EMPLOYEE_000123** vagy **DEPARTMENT_SALES** **RowKey** értékként.  
* Egy külön tulajdonsággal rögzítheti az entitás típusát az alábbi táblázatban látható módon.  

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
<th>EntityType</th>
<th>firstName</th>
<th>lastName</th>
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
<th>EntityType</th>
<th>firstName</th>
<th>lastName</th>
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
<th>EntityType</th>
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
<th>EntityType</th>
<th>firstName</th>
<th>lastName</th>
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

Az első lehetőség, amely az entitás típusától függően a **RowKey**, akkor hasznos, ha fennáll a lehetősége, hogy a különböző típusú entitások esetében azonos a kulcs értéke. A partícióban azonos típusú entitásokat is csoportosít.  

Az ebben a szakaszban tárgyalt módszerek különösen az útmutató korábbi, a modellezési kapcsolatok szakasza című szakaszban ismertetett vita [öröklési kapcsolataira](#inheritance-relationships) vonatkoznak.  

> [!NOTE]
> Érdemes megfontolnia, hogy az entitás típusa érték tartalmazza-e a verziószámot, hogy az ügyfélalkalmazások a POCO objektumokat fejlődjön, és különböző verziókkal működjenek.  
> 
> 

A szakasz további része a Storage ügyféloldali kódtár néhány olyan szolgáltatását ismerteti, amely megkönnyíti a több entitás típusának használatát ugyanabban a táblában.  

#### <a name="retrieving-heterogeneous-entity-types"></a>Heterogén entitások típusának beolvasása
Ha a Storage ügyféloldali kódtárat használja, három lehetősége van több entitás típusának használatára.  

Ha ismeri a megadott **RowKey** és **PartitionKey** -értékekkel tárolt entitás típusát, akkor megadhatja az entitás típusát az entitás lekérése során az előző két példa szerint, amely az EmployeeEntity típusú entitások beolvasását mutatja be.: [Pont lekérdezés végrehajtása a Storage ügyféloldali kódtár használatával](#executing-a-point-query-using-the-storage-client-library) és [több entitás beolvasása a LINQ használatával](#retrieving-multiple-entities-using-linq).  

A második lehetőség, hogy a **DynamicTableEntity** típust (a tulajdonság táska) használja a konkrét poco típusú entitások típusa helyett (ez a beállítás javíthatja a teljesítményt, mert nem szükséges szerializálni és deszerializálni az entitást a .net-típusokra). A következő C# kód lehetséges, hogy több, különböző típusú entitást kér le a táblából, de az összes entitást **DynamicTableEntity** -példányként adja vissza. Ezután a **EntityType** tulajdonsággal határozza meg az egyes entitások típusát:  

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

Más tulajdonságok lekéréséhez a **TryGetValue** metódust kell használnia a **DynamicTableEntity** osztály **Properties (Tulajdonságok** ) tulajdonságában.  

Egy harmadik lehetőség a **DynamicTableEntity** típus és egy **EntityResolver** -példány használatával történő egyesítés. Ez lehetővé teszi több POCO típus feloldását ugyanabban a lekérdezésben. Ebben a példában a **EntityResolver** delegált a **EntityType** tulajdonság használatával különbözteti meg a lekérdezés által visszaadott két típusú entitást. A **megoldási** metódus a **feloldó** delegált használatával oldja fel a **DynamicTableEntity** -példányokat **TableEntity** -példányokra.  

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

#### <a name="modifying-heterogeneous-entity-types"></a>Heterogén entitások típusának módosítása
Nem kell tudnia, hogy milyen típusú entitást kell törölni, és mindig ismeri az entitás típusát az beszúrásakor. A **DynamicTableEntity** -típus használatával azonban a típusának ismerete nélkül frissítheti az entitásokat, és nem használhatja a Poco Entity osztályt. A következő mintakód egyetlen entitást kérdez le, és a frissítés előtt ellenőrzi, hogy a **EmployeeCount** tulajdonság létezik-e.  

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

### <a name="controlling-access-with-shared-access-signatures"></a>Hozzáférés szabályozása közös hozzáférésű aláírásokkal
A megosztott hozzáférés-aláírási (SAS-) tokenekkel engedélyezheti az ügyfélalkalmazások számára, hogy közvetlenül a Table Service-sel való hitelesítés nélkül módosíthassák (és lekérdezni) a táblák entitásait. Az SAS használatának három fő előnye van az alkalmazásban:  

* A Storage-fiók kulcsát nem szükséges egy nem biztonságos platformra (például egy mobileszközön) terjeszteni ahhoz, hogy az eszköz hozzáférjen és módosíthassa a Table service entitásait.  
* A webes és feldolgozói szerepkörök egy részét kiszervezheti az entitások az ügyfélszámítógépekre, például a végfelhasználói számítógépekre és a mobileszközök kezelésére.  
* Az ügyfelek számára korlátozott és időkorlátos engedélyeket rendelhet hozzá (például a csak olvasási hozzáférés engedélyezése adott erőforrásokhoz).  

Az SAS-tokenek Table service használatával történő használatáról további információt a [közös hozzáférésű aláírások (SAS) használata](../storage/common/storage-dotnet-shared-access-signature-part-1.md)című témakörben talál.  

Azonban továbbra is olyan SAS-jogkivonatokat kell generálnia, amelyek a Table szolgáltatásban lévő entitásoknak biztosítanak ügyfélalkalmazás: ezt olyan környezetben végezze el, amely biztonságos hozzáférést biztosít a Storage-fiók kulcsaihoz. Általában webes vagy feldolgozói szerepkört használ az SAS-jogkivonatok létrehozásához, és azokat olyan ügyfélalkalmazások számára kézbesíteni, amelyeknek hozzá kell férniük az entitásokhoz. Mivel továbbra is az SAS-tokenek ügyfeleknek való létrehozásával és megvalósításával kapcsolatos költségek merülnek fel, érdemes megfontolni, hogy a lehető leghatékonyabban csökkentse a terhelést, különösen nagy mennyiségű forgatókönyv esetén.  

Olyan SAS-tokent is létre lehet hozni, amely hozzáférést biztosít egy tábla entitásai egy részhalmazához. Alapértelmezés szerint egy teljes táblázathoz hoz létre SAS-jogkivonatot, de azt is megadhatja, hogy az SAS-jogkivonat hozzáférést biztosítson egy **PartitionKey** -értékhez, vagy egy **PartitionKey** -és **RowKey** -érték tartományához. Úgy is dönthet, hogy SAS-jogkivonatokat állít elő a rendszer egyes felhasználói számára, hogy az egyes felhasználók SAS-jogkivonata csak a Table szolgáltatásban lévő saját entitásokhoz férhessenek hozzá.  

### <a name="asynchronous-and-parallel-operations"></a>Aszinkron és párhuzamos műveletek
Ha több partíción terjeszti át a kéréseket, az átviteli sebességet és az ügyfelek közötti rugalmasságot aszinkron vagy párhuzamos lekérdezések használatával növelheti.
Előfordulhat például, hogy két vagy több feldolgozói szerepkörrel rendelkező példánya párhuzamosan fér hozzá a táblákhoz. Lehet, hogy egyes feldolgozói szerepkörök a partíciók meghatározott készleteit terhelik, vagy egyszerűen több feldolgozói szerepkör-példánnyal rendelkeznek, amelyek mindegyike egy tábla összes partíciójának elérésére képes.  

Egy ügyfél-példányon belül a tárolási műveletek aszinkron végrehajtásával javíthatja az átviteli sebességet. A Storage ügyféloldali kódtára megkönnyíti az aszinkron lekérdezések és módosítások írását. Előfordulhat például, hogy a szinkron metódussal indul, amely egy partíció összes entitását lekéri a következő C# kódban látható módon:  

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

A kód egyszerűen módosítható úgy, hogy a lekérdezés aszinkron módon fusson a következőképpen:  

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

Ebben az aszinkron példában a szinkron verzió következő változásai láthatók:  

* A metódus aláírása mostantól tartalmazza az **aszinkron** módosítót, és visszaadja a **feladat** példányát.  
* Ahelyett, hogy a **ExecuteSegmented** metódust az eredmények beolvasására szólítja fel, a metódus most meghívja a **ExecuteSegmentedAsync** metódust, és a **várakozási** módosító használatával aszinkron módon kéri le az eredményeket.  

Az ügyfélalkalmazás többször is meghívhatja ezt a metódust (a **Department** paraméter különböző értékeivel), és mindegyik lekérdezés külön szálon fog futni.  

A **végrehajtási** metódusnak nincs aszinkron verziója a **TableQuery** osztályban, mert az **IEnumerable** felület nem támogatja az aszinkron enumerálást.  

Az entitásokat aszinkron módon is beszúrhatja, frissítheti és törölheti. Az alábbi C# példa egy egyszerű, szinkron metódust mutat be, amely egy alkalmazotti entitást szúr be vagy cserél le:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

A kód egyszerűen módosítható úgy, hogy a frissítés aszinkron módon fusson a következőképpen:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Ebben az aszinkron példában a szinkron verzió következő változásai láthatók:  

* A metódus aláírása mostantól tartalmazza az **aszinkron** módosítót, és visszaadja a **feladat** példányát.  
* Ahelyett, hogy meghívja a **Execute** metódust az entitás frissítéséhez, a metódus most meghívja a **ExecuteAsync** metódust, és a **várakozási** módosító használatával aszinkron módon kéri le az eredményeket.  

Az ügyfélalkalmazás több aszinkron metódust is meghívhat, mint például ez, és minden metódus meghívása külön szálon fut.  

### <a name="credits"></a>Kreditek
Köszönetet mondunk az Azure-csapat következő tagjainak a hozzájárulásaikkal kapcsolatban: Dominic Betts, Jason domborított, Jean Ghanem, jai Vina, Jeff Irwin, Dávid Kommineni, Vinay Shah és Gál Vali Ozler, valamint Tom holland a Microsoft DX-ból. 

A következő Microsoft-MVP is köszönjük értékes visszajelzéseit a felülvizsgálati ciklusok során: Igor Papirov és Edward Bakker.

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

