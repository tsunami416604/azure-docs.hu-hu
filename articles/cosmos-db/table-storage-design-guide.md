---
title: Tervezzen Azure Cosmos DB-táblázatokat a méretezéshez és a teljesítményhez
description: 'Azure Table storage tervezési útmutató: méretezhető és teljesítménytáblák az Azure Cosmos DB és az Azure Table storage'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: sakash279
ms.author: akshanka
ms.custom: seodec18
ms.openlocfilehash: 166076d366cbbf7bef24648772beaba9b3a88253
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246473"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Azure Table storage-táblázat tervezési útmutató: Méretezhető és teljesítménytáblák

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

A méretezhető és nagy teljesítményű táblák tervezésénél rengeteg szempontot, köztük a költséget is figyelembe kell vennie. Ha korábban már tervezett sémákat relációs adatbázisokhoz, ismerősnek fogja találni ezeket a szempontokat. Bár az Azure Table Storage és a relációs modellek között vannak hasonlóságok, több fontos dologban is eltérnek egymástól. Ezek a különbségek általában olyan eltérő kialakításokhoz vezetnek, amelyek a relációs adatbázisokat ismerő személyek számára ellentmondásosnak vagy helytelennek tűnhetnek, azonban a NoSQL-alapú kulcs/érték tárolók, például a Table Storage számára való tervezésekor van értelmük.

A table storage olyan felhőalapú alkalmazásokat támogat, amelyek entitások milliárdjait ("sorok" a relációs adatbázis terminológiájában) tartalmaznak, vagy olyan adatkészletek esetében, amelyeknek támogatniuk kell a nagy tranzakciós köteteket. Ezért másképp kell gondolkodnia az adatok tárolásának módjáról, és meg kell értenie, hogyan működik a Table storage. Egy jól megtervezett NoSQL adattár lehetővé teszi a megoldás méretezése sokkal tovább (és alacsonyabb költséggel), mint egy megoldás, amely egy relációs adatbázist használ. Ez az útmutató segítséget nyújt ezekkel a témakörökkel kapcsolatban.  

## <a name="about-azure-table-storage"></a>Az Azure Table storage-ról
Ez a szakasz kiemeli a table storage néhány kulcsfontosságú jellemzőjét, amelyek különösen fontosak a teljesítmény és a méretezhetőség tervezése szempontjából. Ha most ismerkedik az Azure Storage and Table storage szolgáltatással, olvassa [el a Bevezetés a Microsoft Azure Storage használatába](../storage/common/storage-introduction.md) és az Azure Table storage [használatának megkezdéséhez a .NET használatával](table-storage-how-to-use-dotnet.md) című témakört a cikk további részének elolvasása előtt című témakörben olvashatja. Bár a jelen útmutató középpontjában a table storage, ez nem tartalmaz némi megbeszélést az Azure Queue storage és az Azure Blob storage, és hogyan lehet használni őket együtt table storage egy megoldásban.  

A táblatároló táblázatos formátumot használ az adatok tárolásához. A szabványos terminológiában a tábla minden sora egy entitást jelöl, és az oszlopok az entitás különböző tulajdonságait tárolják. Minden entitás rendelkezik egy pár kulcsot egyedileg azonosítani, és egy időbélyeg oszlop, amely table storage segítségével nyomon követni, amikor az entitás utolsó frissítése. Az időbélyeg mező automatikusan hozzáadódik, és nem lehet manuálisan felülírni az időbélyeget tetszőleges értékkel. A table storage ezt az utolsó módosítású időbélyeget (LMT) használja az optimista egyidejűség kezelésére.  

> [!NOTE]
> Table Storage REST API-műveletek `ETag` is ad vissza egy értéket, amely az LMT származik. Ebben a dokumentumban az ETag és az LMT kifejezések szinonimaként használatosak, mivel ugyanazokra az alapul szolgáló adatokra vonatkoznak.  
> 
> 

A következő példa egy egyszerű táblatervet mutat be az alkalmazottak és részlegentitások tárolására. Az útmutató későbbi részében bemutatott példák közül sok ezen az egyszerű kialakításon alapul.  

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
<td>Jún</td>
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
<th>Alkalmazottszáma</th>
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


Ez a terv eddig hasonlít egy relációs adatbázis táblájához. A legfontosabb különbségek a kötelező oszlopok és az a képesség, hogy több entitástípust tároljon ugyanabban a táblában. Ezenkívül a felhasználó által definiált tulajdonságok mindegyikének , például a **Keresztnév** vagy **az Életkor**, adattípusa (például egész szám vagy karakterlánc), ugyanúgy, mint egy relációs adatbázis oszlopa. A relációs adatbázisokkal ellentétben azonban a Table storage séma nélküli jellege azt jelenti, hogy egy tulajdonságnak nem kell minden entitáson azonos adattípussal rendelkeznie. Ha összetett adattípusokat szeretne egyetlen tulajdonságban tárolni, szerializált formátumot kell használnia, például JSON vagy XML formátumot. További információt a [Táblatárolási adatmodell ismertetése című témakörben](https://msdn.microsoft.com/library/azure/dd179338.aspx)talál.

Az Ön `PartitionKey` `RowKey` választása, és alapvető fontosságú a jó asztal tervezés. A táblában tárolt minden entitásnak egyedi `PartitionKey` `RowKey`kombinációjával kell rendelkeznie a és a alkalmazásból. A relációs adatbázistáblában lévő `PartitionKey` kulcsokhoz ugyanúgy a rendszer indexeli a kulcsokat, `RowKey` hogy olyan fürtözött indexet hozzon létre, amely lehetővé teszi a gyors kivizsgálást. A table storage azonban nem hoz létre másodlagos indexeket, így csak ezek a két indexelt tulajdonság (a később ismertetett minták némelyike megmutatja, hogyan kerülheti meg ezt a látszólagos korlátozást).  

A táblázat egy vagy több partícióból áll, és sok tervezési döntést hoz, `PartitionKey` `RowKey` amelyek a megfelelő és a megoldás optimalizálása körül lesznek. A megoldás állhat csak egy tábla, amely tartalmazza az összes entitást partíciókba rendezve, de általában egy megoldás több tábla. A táblázatok segítségével logikusan rendszerezheti az entitásokat, és hozzáférés-vezérlési listák használatával kezelheti az adatokhoz való hozzáférést. Egyetlen tárolási művelettel eldobhat egy teljes táblát.  

### <a name="table-partitions"></a>Táblapartíciók
A fiók név, táblanév és `PartitionKey` együtt azonosítja a partíciót a tárolási szolgáltatáson belül, ahol a Table storage tárolja az entitást. Amellett, hogy az entitások címzési sémájának részét képezik, a partíciók meghatározzák a tranzakciók hatókörét (lásd a cikk későbbi részét, [az Entitáscsoport-tranzakciókat),](#entity-group-transactions)és a Table storage méretezésének alapját képezik. A táblapartíciókról a [Táblatárolás teljesítmény- és méretezhetőségi ellenőrzőlistája](../storage/tables/storage-performance-checklist.md)című témakörben talál további információt.  

A Table Storage-ban egy adott csomópont egy vagy több teljes partíciót, és a szolgáltatás skálázásdinamikusterhelés-elosztási partíciók csomópontok között dinamikusan terheléselosztási partíciók. Ha egy csomópont terhelés alatt van, a Table storage feloszthatja az adott csomópont által kiszolgált partíciók tartományát különböző csomópontokra. Amikor a forgalom csökken, a Table storage egyesítheti a partíciótartományokat a csendes csomópontoktól egyetlen csomópontra.  

A Table storage belső részleteiről és különösen a partíciók kezeléséről a [Microsoft Azure Storage: Egy magas rendelkezésre állású felhőalapú tárolási szolgáltatás, amely erős konzisztenciával rendelkezik.](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)  

### <a name="entity-group-transactions"></a>Entitáscsoport-tranzakciók
A Table storage-ban az entitáscsoport-tranzakciók (EGT-k) az egyetlen beépített mechanizmus több entitás közötti atomi frissítések végrehajtásához. Az EGT-ket *kötegelt tranzakcióknak*is nevezik . EGT-k csak akkor működhet nek az azonos partíción tárolt entitások (megosztása ugyanazt a partíciókulcsot egy adott táblában), így bármikor, amikor több entitások közötti atomi tranzakciós viselkedésre van szüksége, győződjön meg arról, hogy ezek az entitások ugyanabban a partícióban vannak. Ez gyakran okarra, hogy több entitástípus maradjon ugyanabban a táblában (és partícióban), és ne használjon több táblát különböző entitástípusokhoz. Egyetlen EGT legbénult 100 entitáson működhet.  Ha több egyidejű EGT-t küld be feldolgozásra, fontos annak biztosítása, hogy ezek az EGT-k ne működjenek az EGT-k közös entitásain. Ellenkező esetben a feldolgozás késleltetésének veszélye.

Az EGT-k egy lehetséges kompromisszumot is bevezetnek, amelyet a tervezésben értékelhet. Több partíció használata növeli az alkalmazás méretezhetőségét, mivel az Azure több lehetőséget kínál a terheléselosztási kérelmek csomópontok közötti. De ez korlátozhatja az alkalmazás képességét az atomi tranzakciók végrehajtására és az adatok erős konzisztenciájának fenntartására. Továbbá vannak olyan speciális méretezhetőségi célok egy partíció szintjén, amelyek korlátozhatják az egyetlen csomópontra várható tranzakciók átviteli képességét.

Az Azure storage-fiókok méretezhetőségi céljairól további információt a [szabványos tárfiókok méretezhetőségi céljai című témakörben](../storage/common/scalability-targets-standard-account.md)talál. A table storage méretezhetőségi céljairól a [Táblázattárolás méretezhetőségi és teljesítménycéljai](../storage/tables/scalability-targets.md)című témakörben talál további információt. Az útmutató későbbi szakaszai ismertetik a különböző tervezési stratégiákat, amelyek segítenek az ehhez hasonló kompromisszumok kezelésében, és bemutatják, hogyan lehet a legjobban kiválasztani a partíciókulcsot az ügyfélalkalmazás speciális követelményei alapján.  

### <a name="capacity-considerations"></a>Kapacitással kapcsolatos szempontok
Az alábbi táblázat néhány olyan kulcsfontosságú értéket tartalmaz, amelyet a Table tárolási megoldás tervezésekor figyelembe kell venni:  

| Egy Azure-tárfiók teljes kapacitása | 500 TB |
| --- | --- |
| Táblák száma egy Azure-tárfiókban |Csak a tárfiók kapacitása korlátozza. |
| A táblában lévő partíciók száma |Csak a tárfiók kapacitása korlátozza. |
| A partíción lévő entitások száma |Csak a tárfiók kapacitása korlátozza. |
| Egy adott entitás mérete |Legfeljebb 1 MB, legfeljebb 255 tulajdonsággal (beleértve a `PartitionKey`, `RowKey`és a) értéket. `Timestamp` |
| A`PartitionKey` |Legfeljebb 1 KB-os karakterlánc. |
| A`RowKey` |Legfeljebb 1 KB-os karakterlánc. |
| Egy entitáscsoport-tranzakció mérete |Egy tranzakció legfeljebb 100 entitást tartalmazhat, és a hasznos adatnak 4 MB-nál kisebb nek kell lennie. Az EGT csak egyszer frissíthet egy entitást. |

További információt [a Table szolgáltatás adatmodelljének ismertetése című](https://msdn.microsoft.com/library/azure/dd179338.aspx)témakörben talál.  

### <a name="cost-considerations"></a>Költségekkel kapcsolatos szempontok
A táblatárolás viszonylag olcsó, de a Table storage-t használó megoldások kiértékelésének részeként költségbecsléseket kell megadnia mind a kapacitáskihasználtságra, mind a tranzakciók mennyiségére vonatkozóan. Sok esetben azonban a denormalizált vagy duplikált adatok tárolása a megoldás teljesítményének vagy méretezhetőségének javítása érdekében érvényes megközelítés. A díjszabásról az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/)című témakörben talál további információt.  

## <a name="guidelines-for-table-design"></a>Irányelvek táblatervezéshez
Ezek a listák összefoglalnak néhány kulcsfontosságú irányelvet, amelyeket a táblák tervezésekor szem előtt kell tartania. Ez az útmutató foglalkozik velük részletesebben később. Ezek az irányelvek eltérnek a relációs adatbázis-tervezéshez általában követett irányelvektől.  

A táblázattároló tervezése *úgy,* hogy hatékonylegyen:

* **Tervezze meg a lekérdezést a nehéz alkalmazásokban.** A táblák tervezésekor gondolja át a lekérdezéseket (különösen a késés-érzékenyeket), amelyeket futtatni fog, mielőtt átgondolná, hogyan fogja frissíteni az entitásokat. Ez általában hatékony és hatékony megoldást eredményez.  
* **Adja `PartitionKey` meg `RowKey` mind és a lekérdezések.** Az ilyen *pontlekérdezések* a leghatékonyabb táblatárolási lekérdezések.  
* **Fontolja meg az entitások ismétlődő másolatainak tárolását.** A táblatárolás olcsó, ezért fontolja meg ugyanazon entitás többszöri (különböző kulcsokkal) tárolását a hatékonyabb lekérdezések lehetővé tétele érdekében.  
* **Fontolja meg az adatok denormalizálását.** Az asztaltárolás olcsó, ezért fontolja meg az adatok denormalizálását. Például tárolja az összesítő entitásokat, hogy az összesített adatok lekérdezéseinek csak egyetlen entitáshoz kell hozzáférnie.  
* **Összetett kulcsértékek használata.** Az egyetlen kulcs `PartitionKey` van, és `RowKey`. Például használja az összetett kulcs értékeket az entitások másodlagos kulcsos elérési utak engedélyezéséhez.  
* **Használja a lekérdezés-vetületet.** A hálózaton keresztül átvihető adatok mennyiségét csökkentheti, ha olyan lekérdezéseket használ, amelyek csak a szükséges mezőket választják ki.  

A táblázattároló megtervezése *írási* hatékonysággal:  

* **Ne hozzon létre gyorspartíciókat.** Válassza ki azokat a kulcsokat, amelyek lehetővé teszik a kérelmek több partíció közötti terjesztését bármikor.  
* **Kerülje a forgalmi csúcsokat.** A forgalom elosztása ésszerű időn belül, és ne kiugró forgalom.
* **Nem feltétlenül hozzon létre külön táblát az entitások minden típusához.** Ha entitástípusok közötti atomi tranzakciókra van szüksége, ezeket a több entitástípust ugyanabban a partícióban, ugyanabban a táblában tárolhatja.
* **Vegye figyelembe a maximális átviteli teljesítmény, amit el kell érnie.** Tisztában kell lennie a table storage méretezhetőségi céljaival, és győződjön meg arról, hogy a terv nem fogja túllépni azokat.  

Az útmutató későbbi részében olyan példákat láthatsz, amelyek mindezeket az elveket átülik a gyakorlatba.  

## <a name="design-for-querying"></a>Tervezés lekérdezéshez
Az asztaltárolás lehet olvasásigényes, írásigényes vagy a kettő keveréke. Ez a szakasz úgy tervezi, hogy támogassa az olvasási műveletek hatékony támogatását. Általában egy olyan terv, amely hatékonyan támogatja az olvasási műveleteket, szintén hatékony az írási műveletekhez. Az írási műveletek támogatása érdekében történő tervezés során azonban további szempontok is felmerülnek. Ezeket a következő, [Az adatok módosításának megtervezése](#design-for-data-modification)című szakasz ismerteti.

Egy jó kiindulási pont, amely lehetővé teszi az adatok hatékony olvasását, hogy feltegye a kérdést: "Milyen lekérdezéseket kell futtatnia az alkalmazásnak a szükséges adatok lekéréséhez?"  

> [!NOTE]
> A Table storage-nak köszönhetően fontos, hogy a design elöl legyen, mert később nehéz és költséges megváltoztatni. Például egy relációs adatbázisban gyakran lehetséges a teljesítményproblémák megoldása egyszerűen egy meglévő adatbázishoz való indexek hozzáadásával. Ez nem lehetséges a Table storage használata kor.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>A választás `PartitionKey` és `RowKey` a lekérdezési teljesítmény befolyásolása
A következő példák feltételezik, hogy a Table Storage a következő struktúrával `Timestamp` rendelkező alkalmazotti entitásokat tárolja (a legtöbb példa kihagyja a tulajdonságot az egyértelműség kedvéért):  

| Oszlop neve | Adattípus |
| --- | --- |
| `PartitionKey`(Osztály neve) |Sztring |
| `RowKey`(Alkalmazottazonosító) |Sztring |
| `FirstName` |Sztring |
| `LastName` |Sztring |
| `Age` |Egész szám |
| `EmailAddress` |Sztring |

Íme néhány általános irányelv a táblatárolási lekérdezések tervezéséhez. Az alábbi példákban használt szűrőszintaxis a Table Storage REST API-ból származik. További információt az [Entitások lekérdezése](https://msdn.microsoft.com/library/azure/dd179421.aspx)című témakörben talál.  

* A *pontlekérdezés* a leghatékonyabb anamnézis, és a nagy mennyiségű, a legalacsonyabb késést igénylő nagy mennyiségű keresések esetén ajánlott. Az ilyen lekérdezés az indexek segítségével megkeresheti az egyes `PartitionKey` `RowKey` entitásokat hatékonyan, mind a, mind az értékek megadásával. Például: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* A második legjobb egy *tartomány lekérdezés*. A és `PartitionKey`a szűrőket használja `RowKey` egy értéktartományra, hogy egynél több entitást ad vissza. Az `PartitionKey` érték egy adott partíciót `RowKey` azonosít, és az értékek azonosítják az adott partíció entitásainak egy részét. Például: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* A harmadik legjobb egy *partíció scan*. A `PartitionKey`, és szűrők et használ egy másik nem kulcsú tulajdonságra, és egynél több entitást adhat vissza. Az `PartitionKey` érték egy adott partíciót azonosít, és a tulajdonságértékek az adott partíció entitásainak egy részhalmazához választanak ki. Például: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* A *táblavizsgálat* nem tartalmazza `PartitionKey`a , és nem hatékony, mert megkeresi az összes partíciót, amely a táblát alkotja az egyező entitások. Táblázatvizsgálat, függetlenül attól, hogy a szűrő `RowKey`a. Például: `$filter=LastName eq 'Jones'`.  
* Az Azure Table storage-lekérdezések, `PartitionKey` amelyek `RowKey` több entitást adnak vissza, rendezze őket és rendezze őket. Az ügyfél entitásai alkalmazásának elkerülése `RowKey` érdekében válasszon egy olyan entitást, amely meghatározza a leggyakoribb rendezési sorrendet. Az Azure Cosmos DB-ben az Azure Table API által visszaadott lekérdezési eredmények nincsenek partíciókulcs vagy sorkulcs szerint rendezve. A szolgáltatáskülönbségek részletes listáját lásd: [különbségek table API az Azure Cosmos DB és az Azure Table storage.](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)

A "**vagy**" értéken `RowKey` alapuló szűrő megadásával partícióvizsgálatot eredményez, és nem minősül tartománylekérdezésnek. Ezért kerülje a szűrőket használó `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`lekérdezéseket, például: .  

Példák olyan ügyféloldali kódra, amely a storage-ügyfélkódtár hatékony lekérdezések futtatását használja, lásd:  

* [Pontlekérdezés futtatása a Tárolóügyféltár használatával](#run-a-point-query-by-using-the-storage-client-library)
* [Több entitás beolvasása a LINQ használatával](#retrieve-multiple-entities-by-using-linq)
* [Kiszolgálóoldali vetítés](#server-side-projection)  

Példák olyan ügyféloldali kódra, amely több, ugyanabban a táblában tárolt entitástípust képes kezelni, lásd:  

* [Heterogén entitástípusokkal végzett munka](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Válassza ki a megfelelő`PartitionKey`
A választás `PartitionKey` egyensúlyban kell lennie az EGT-k használatának engedélyezésének szükségességével (a konzisztencia biztosítása érdekében) az entitások több partíció közötti elosztásának követelményével (skálázható megoldás biztosítása érdekében).  

Egy szélsőséges, az összes entitást egyetlen partíción tárolhatja. Ez azonban korlátozhatja a megoldás méretezhetőségét, és megakadályozhatja, hogy a Table storage képes-hoz teherelosztási kérelmek. A másik véglet, partíciónként egy entitás tárolható. Ez nagymértékben méretezhető, és lehetővé teszi a Table storage számára a terheléselosztási kérelmeket, de megakadályozza az entitáscsoport-tranzakciók használatát.  

Az `PartitionKey` ideális lehetővé teszi a hatékony lekérdezések használatát, és elegendő partícióval rendelkezik ahhoz, hogy a megoldás méretezhető legyen. Általában azt fogja találni, hogy az entitások lesz egy megfelelő tulajdonság, amely elosztja az entitások között elegendő partíciókat.

> [!NOTE]
> Egy olyan rendszerben például, amely a `UserID` felhasználókra vagy `PartitionKey`az alkalmazottakra vonatkozó információkat tárolja, lehet jó. Előfordulhat, hogy több entitás, `UserID` amely egy adott partíciókulcsként használja. Minden entitás, amely egy felhasználó adatait tárolja, egyetlen partícióba van csoportosítva. Ezek az entitások elérhetőek EGT-ken keresztül, miközben továbbra is jól méretezhetők.
> 
> 

Az entitások beszúrásával, `PartitionKey` frissítésével és törlésével kapcsolatban további szempontok at is figyelembe vesz. További információt a cikk későbbi [Adatmódosítási terv](#design-for-data-modification) című részében talál.  

### <a name="optimize-queries-for-table-storage"></a>Lekérdezések optimalizálása táblatároláshoz
A táblatárolás automatikusan indexeli `PartitionKey` `RowKey` az entitásokat a és az értékek et egyetlen fürtözött indexben használva. Ez az oka annak, hogy a pontlekérdezések a leghatékonyabban használható. A `PartitionKey` fürtözött indexen kívül azonban nincs más index `RowKey`a és a csoportban lévő indexen kívül.

Számos tervnek meg kell felelnie a követelményeknek ahhoz, hogy több feltétel alapján engedélyezhesse az entitások megkeresését. Például az alkalmazotti entitások megkeresése e-mail, alkalmazottazonosító vagy vezetéknév alapján. A táblázat tervezési [mintái](#table-design-patterns) szakaszban az alábbi minták az ilyen típusú követelményekkel foglalkoznak. A minták azt is leírják, hogyan lehet megkerülni azt a tényt, hogy a table storage nem biztosít másodlagos indexeket.  

* [Partíción belüli másodlagos indexminta:](#intra-partition-secondary-index-pattern)Tárolja az egyes `RowKey` entitások több példányát különböző értékek használatával (ugyanabban a partícióban). Ez lehetővé teszi a gyors és hatékony keres, és alternatív rendezési sorrendek segítségével különböző `RowKey` értékeket.  
* [Partíciók közötti másodlagos indexminta:](#inter-partition-secondary-index-pattern)Tárolja az egyes `RowKey` entitások több példányát különböző értékek használatával külön partíciókban vagy külön táblákban. Ez lehetővé teszi a gyors és hatékony keres, és alternatív rendezési sorrendek segítségével különböző `RowKey` értékeket.  
* [Entitások indexmintája](#index-entities-pattern): Indexentitások karbantartása az entitások listáját visszaadó hatékony keresések engedélyezéséhez.  

### <a name="sort-data-in-table-storage"></a>Adatok rendezése a Táblázat tárolóban

A táblatároló növekvő sorrendben rendezi a `PartitionKey` lekérdezés eredményeit, majd a `RowKey`alapján.

> [!NOTE]
> Az Azure Cosmos DB-ben az Azure Table API által visszaadott lekérdezési eredmények nincsenek partíciókulcs vagy sorkulcs szerint rendezve. A szolgáltatáskülönbségek részletes listáját lásd: [különbségek table API az Azure Cosmos DB és az Azure Table storage.](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)

A Table storage kulcsai karakterlánc-értékek. A numerikus értékek helyes rendezésének biztosításához konvertálja őket rögzített hosszúságúra, és nullákkal kell kipárnáznia őket. Ha például a használt alkalmazottazonosító értéke `RowKey` egész szám, akkor a **123-as** alkalmazotti azonosítót **00000123-as**értékre kell konvertálni. 

Számos alkalmazás nak vannak követelményei a különböző sorrendben rendezett adatok használatára: például az alkalmazottak név szerint vagy csatlakozás dátuma szerint történő rendezése. A Táblázat tervezési [mintái](#table-design-patterns) című szakaszban a következő minták az entitások rendezési sorrendjeinek váltakozását ismertetik:  

* [Partíción belüli másodlagos indexminta:](#intra-partition-secondary-index-pattern)Tárolja az egyes `RowKey` entitások több példányát különböző értékek használatával (ugyanabban a partícióban). Ez lehetővé teszi a gyors és hatékony keres, és alternatív rendezési sorrendek segítségével különböző `RowKey` értékeket.  
* [Partíciók közötti másodlagos indexminta](#inter-partition-secondary-index-pattern): Tárolja az `RowKey` egyes entitások több példányát különböző értékek használatával külön partíciókban, külön táblákban. Ez lehetővé teszi a gyors és hatékony keres, és alternatív rendezési sorrendek segítségével különböző `RowKey` értékeket.
* [Log tail minta:](#log-tail-pattern)A partícióhoz legutóbb hozzáadott *n* entitások lekérése fordított `RowKey` dátum- és idősorrendben rendezési érték használatával.  

## <a name="design-for-data-modification"></a>Tervezés adatmódosításhoz
Ez a szakasz a beszúrások, frissítések és törlések optimalizálásával kapcsolatos tervezési szempontokra összpontosít. Bizonyos esetekben ki kell értékelnie a minták közötti kompromisszumot, amely az adatmódosításra optimalizált tervek lekérdezésére optimalizált. Ez az értékelés hasonló ahhoz, amit a relációs adatbázisok tervezésénél végez (bár a tervezési kompromisszumok kezelésének technikái eltérnek a relációs adatbázisokban). A [táblázat tervezési mintái](#table-design-patterns) című szakasz a táblázattárolás néhány részletes tervezési mintázatát ismerteti, és kiemeli a kompromisszumok némelyikét. A gyakorlatban azt fogja találni, hogy sok entitások lekérdezésére optimalizált tervek is jól működik az entitások módosítása.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>A beszúrási, frissítési és törlési műveletek teljesítményének optimalizálása
Az entitás frissítéséhez vagy törléséhez képesnek kell `PartitionKey` lennie `RowKey` arra, hogy azonosítsa azt a és az értékek használatával. Ebben a tekintetben `PartitionKey` az `RowKey` entitások választásának és módosításának az Ön által választott feltételekhez hasonló feltételeket kell követnie a pontlekérdezések támogatásához. Az entitásokat a lehető leghatékonyabban szeretné azonosítani. Nem szeretné, hogy egy nem hatékony partíció vagy tábla vizsgálat megkeresni `PartitionKey` egy `RowKey` entitást annak érdekében, hogy felfedezzék a és értékeket kell frissíteni vagy törölni.  

A táblázat tervezési [mintái](#table-design-patterns) című szakaszban a következő minták a beszúrási, frissítési és törlési műveletek teljesítményének optimalizálásával foglalkoznak:  

* [Nagy mennyiségű törlési minta](#high-volume-delete-pattern): Nagy mennyiségű entitás törlésének engedélyezése az összes entitás egyidejű törléséhez a saját külön táblájukban. Az entitásokat a tábla törlésével törölheti.  
* [Adatsorok mintája:](#data-series-pattern)A teljes adatsorokat egyetlen entitásban tárolja a kérések számának minimalizálása érdekében.  
* [Széles entitások mintája:](#wide-entities-pattern)Több fizikai entitás használatával 252-nél több tulajdonsággal rendelkező logikai entitásokat tároljon.  
* [Nagy entitások minta:](#large-entities-pattern)Blob storage használatával nagy tulajdonságértékek tárolására.  

### <a name="ensure-consistency-in-your-stored-entities"></a>A tárolt entitások konzisztenciájának biztosítása
A másik kulcsfontosságú tényező, amely befolyásolja a választott kulcsok optimalizálása adatmódosítások, hogyan biztosíthatja a konzisztenciát atomi tranzakciók használatával. Egt-t csak az ugyanazon partíción tárolt entitásokon használhat.  

A Táblázat tervezési [mintái](#table-design-patterns) című szakaszban a következő minták a konzisztencia kezelésével foglalkoznak:  

* [Partíción belüli másodlagos indexminta:](#intra-partition-secondary-index-pattern)Tárolja az egyes `RowKey` entitások több példányát különböző értékek használatával (ugyanabban a partícióban). Ez lehetővé teszi a gyors és hatékony keres, és alternatív rendezési sorrendek segítségével különböző `RowKey` értékeket.  
* [Partíciók közötti másodlagos indexminta:](#inter-partition-secondary-index-pattern)Tárolja az egyes `RowKey` entitások több példányát különböző értékek használatával külön partíciókban vagy külön táblákban. Ez lehetővé teszi a gyors és hatékony keres, és alternatív rendezési sorrendek segítségével különböző `RowKey` értékeket.  
* [Végül konzisztens tranzakciók minta:](#eventually-consistent-transactions-pattern)Engedélyezése végül konzisztens viselkedés partícióhatárokon vagy a tárolási rendszer határait az Azure-várólisták használatával.
* [Entitások indexmintája](#index-entities-pattern): Indexentitások karbantartása az entitások listáját visszaadó hatékony keresések engedélyezéséhez.  
* [Denormalizációs minta:](#denormalization-pattern)A kapcsolódó adatok egyesítése egyetlen entitásban, hogy egyetlen pontlekérdezéssel lehívhassa az összes szükséges adatot.  
* [Adatsorok mintája:](#data-series-pattern)A teljes adatsorokat egyetlen entitásban tárolja a kérések számának minimalizálása érdekében.  

További információt a cikk [későbbi, Entitáscsoport-tranzakciói](#entity-group-transactions) című témakörben talál.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Győződjön meg róla, hogy a hatékony módosítások érdekében a tervezés megkönnyíti a hatékony lekérdezéseket
Sok esetben a hatékony lekérdezési terv hatékony módosításokat eredményez, de mindig ki kell értékelnie, hogy ez a helyzet az adott forgatókönyv esetében. A [táblázat tervezési mintái](#table-design-patterns) szakaszban szereplő minták közül néhány kifejezetten kiértékeli az entitások lekérdezése és módosítása közötti kompromisszumokat, és mindig figyelembe kell venni az egyes művelettípusok számát.  

A Táblázat tervezési [mintái](#table-design-patterns) című szakaszban szereplő alábbi minták a hatékony lekérdezések tervezése és a hatékony adatmódosítás közötti kompromisszumokkal foglalkoznak:  

* [Összetett kulcsminta:](#compound-key-pattern)Összetett `RowKey` értékek használatával engedélyezheti, hogy az ügyfél egypontos lekérdezéssel keressen kapcsolódó adatokat.  
* [Log tail minta:](#log-tail-pattern)A partícióhoz legutóbb hozzáadott *n* entitások lekérése fordított `RowKey` dátum- és idősorrendben rendezési érték használatával.  

## <a name="encrypt-table-data"></a>Táblaadatok titkosítása
A .NET Azure Storage ügyfélkódtár támogatja a karakterláncentitás-tulajdonságok titkosítását a beszúrási és csereműveletekhez. A titkosított karakterláncok bináris tulajdonságokként tárolódnak a szolgáltatásban, és visszafejtik karakterláncokká a visszafejtés után.    

Táblák esetén a titkosítási házirendmellett a felhasználóknak meg kell adniuk a titkosítandó tulajdonságokat. Adjon meg `EncryptProperty` egy attribútumot (a poco-entitásokhoz, amelyek származnak), `TableEntity`vagy adjon meg egy titkosítási feloldót a kérelembeállításokban. A titkosítási feloldó olyan delegált, amely egy partíciókulcsot, sorkulcsot és tulajdonságnevet vesz vissza, és logikai értéket ad vissza, amely jelzi, hogy a tulajdonságot titkosítani kell-e. A titkosítás során az ügyféltár ezt az információt használja annak eldöntésére, hogy egy tulajdonságot titkosítani kell-e a vezetékbe írás közben. A delegált is lehetővé teszi a logika körül, hogyan tulajdonságok titkosítását. (Ha például X, akkor titkosítsa az A tulajdonságot; egyébként titkosítsa az A és B tulajdonságokat.) Nem szükséges megadni ezeket az adatokat entitások olvasása vagy lekérdezése közben.

Az egyesítés jelenleg nem támogatott. Mivel előfordulhat, hogy a tulajdonságok egy részhalmaza korábban egy másik kulccsal lett titkosítva, az új tulajdonságok egyesítése és a metaadatok frissítése adatvesztést eredményez. Az egyesítéshez további szolgáltatáshívásokat kell kezdeményezni a már meglévő entitás szolgáltatásból való olvasásához, vagy egy új kulcsot használ tulajdonságonként. Ezek egyike sem alkalmas a teljesítmény miatt.     

A táblaadatok titkosításáról az [ügyféloldali titkosítás és az Azure Key Vault for Microsoft Azure Storage című témakörben talál](../storage/common/storage-client-side-encryption.md)további információt.  

## <a name="model-relationships"></a>Modellkapcsolatok
A tartománymodellek létrehozása kulcsfontosságú lépés az összetett rendszerek tervezésében. A modellezési folyamat segítségével általában az entitások és a köztük lévő kapcsolatok azonosítására használja az üzleti tartomány megértését és a rendszer tervezésének tájékoztatását. Ez a szakasz arra összpontosít, hogyan fordíthatja le a tartománymodellekben található egyes gyakori kapcsolattípusokat a Table storage terveire. A logikai adatmodellből a fizikai NoSQL-alapú adatmodellre való leképezés folyamata eltér a relációs adatbázis tervezésekor használt tól. A relációs adatbázisok tervezése általában a redundancia minimalizálására optimalizált adatnormalizálási folyamatot feltételezi. Az ilyen kialakítás deklaratív lekérdezési képességet is feltételez, amely absztraktmódon használja ki az adatbázis működésének megvalósítását.  

### <a name="one-to-many-relationships"></a>Egy-a-többhöz kapcsolatok
Az üzleti tartomány objektumok közötti egy-a-többhöz kapcsolatok gyakran előfordulnak: például egy részlegnek sok alkalmazottja van. A Table storage-ban többféleképpen is megvalósíthat egy-a-többhöz kapcsolatokat, amelyek mindegyike az adott forgatókönyv szempontjából releváns előnyökkel és hátrányokkal rendelkezik.  

Tekintsük a példát egy nagy multinacionális vállalat több tízezer szervezeti egységek és munkavállalói szervezetek. Minden részlegnek sok alkalmazottja van, és minden alkalmazott egy adott részleghez van társítva. Az egyik megközelítés a különálló részleg- és alkalmazotti entitások tárolása, például a következők:  

![Szervezeti egységet és alkalmazotti entitást ábrázoló ábra][1]

Ez a példa implicit egy-a-többhöz kapcsolatot mutat `PartitionKey` be a típusok között az érték alapján. Minden részlegnek sok alkalmazottja lehet.  

Ebben a példában egy részlegentitást és annak kapcsolódó alkalmazotti entitásait is megjeleníti ugyanazon a partíción. Választhat, hogy különböző partíciókat, táblákat vagy akár tárfiókokat a különböző entitástípusok.  

Egy másik módszer az adatok denormalizálása, és csak a denormalizált részlegadatokkal rendelkező alkalmazottentitások tárolása, ahogy az a következő példában látható. Ebben a konkrét esetben ez a denormalizált megközelítés nem feltétlenül a legjobb, ha követelmény, hogy képes legyen módosítani a részleteket a részlegvezető. Ehhez frissítenie kell az osztály minden alkalmazottját.  

![Alkalmazotti entitás ábrája][2]

További információt az útmutató [későbbi, denormalizációs mintájában](#denormalization-pattern) talál.  

Az alábbi táblázat összefoglalja az egy-a-többhöz kapcsolattal rendelkező alkalmazotti és részlegentitások tárolására szolgáló egyes megközelítések előnyeit és hátrányait. Azt is figyelembe kell vennie, hogy milyen gyakran vár különböző műveleteket. Elfogadható lehet, hogy egy olyan tervvel rendelkezik, amely drága műveletet tartalmaz, ha az csak ritkán történik meg.  

<table>
<tr>
<th>Módszer</th>
<th>Előnyök</th>
<th>Hátrányok</th>
</tr>
<tr>
<td>Különálló entitástípusok, ugyanaz a partíció, ugyanaz a tábla</td>
<td>
<ul>
<li>A részleg entitást egyetlen művelettel frissítheti.</li>
<li>Az EGT-vel a konzisztencia fenntartásához akkor tarthat fenn konzisztenciát, ha egy alkalmazott entitás frissítésekelésekekén/beszúrásakor/törlésekor módosítania kell egy részlegentitást. Ha például az egyes részlegek osztályának számát tartja karban.</li>
</ul>
</td>
<td>
<ul>
<li>Előfordulhat, hogy bizonyos ügyféltevékenységekhez egy alkalmazottat és egy részlegentitást is be kell olvasnia.</li>
<li>A tárolási műveletek ugyanabban a partíción történnek. Nagy tranzakciós volumen mellett ez hotspotot eredményezhet.</li>
<li>Egt használatával nem helyezhet át alkalmazottat új részlegre.</li>
</ul>
</td>
</tr>
<tr>
<td>Különálló entitástípusok, különböző partíciók, táblák vagy tárfiókok</td>
<td>
<ul>
<li>A részleg entitást vagy alkalmazottentitást egyetlen művelettel frissítheti.</li>
<li>Nagy tranzakciós kötetek, ez segíthet a terhelés tágítható több partíciót.</li>
</ul>
</td>
<td>
<ul>
<li>Előfordulhat, hogy bizonyos ügyféltevékenységekhez egy alkalmazottat és egy részlegentitást is be kell olvasnia.</li>
<li>Az EGT-k nem használhatók a konzisztencia fenntartásához, amikor frissít/beszúr/töröl egy alkalmazottat, és frissít egy részleget. Például egy alkalmazott számának frissítése egy részleg entitásban.</li>
<li>Egt használatával nem helyezhet át alkalmazottat új részlegre.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalizálás egyetlen entitástípussá</td>
<td>
<ul>
<li>Egyetlen kéréssel minden szükséges információt lekérhet.</li>
</ul>
</td>
<td>
<ul>
<li>A konzisztencia fenntartása költséges lehet, ha frissítenie kell a részleg adatait (ehhez a részleg összes alkalmazottjának frissítését kell igényelnie).</li>
</ul>
</td>
</tr>
</table>

Az, hogy hogyan választhatja ki ezeket a lehetőségeket, és mely előnyei és hátrányai a legjelentősebbek, az adott alkalmazási forgatókönyvektől függ. Például milyen gyakran módosítja a részlegentitásokat? Minden alkalmazotti lekérdezéshez szükség van a további részlegadatokra? Milyen közel van a méretezhetőségi korlátok a partíciók vagy a tárfiók?  

### <a name="one-to-one-relationships"></a>Egy-az-egyhez kapcsolatok
A tartományi modellek tartalmazhatnak entitások közötti egy-az-egyhez kapcsolatokat. Ha egy-az-egyhez kapcsolatot kell megvalósítania a Table storage-ban, azt is meg kell választania, hogy miként kapcsolja össze a két kapcsolódó entitást, amikor mindkettőt be kell olvasnia. Ez a kapcsolat lehet implicit, a kulcsértékekben lévő konvención alapuló, vagy explicit, `PartitionKey` `RowKey` ha az egyes entitásokban lévő hivatkozásokat és értékeket tárol a kapcsolódó entitáshoz. A kapcsolódó entitások ugyanabban a partícióban való tárolásáról az [Egy-a-többhöz kapcsolatok](#one-to-many-relationships)című szakaszban található.  

Vannak olyan megvalósítási szempontok is, amelyek arra késztethetik, hogy egy-az-egyhez kapcsolatokat valósítson meg a Table storage-ban:  

* Nagy entitások kezelése (további információkért lásd: [Nagy entitások minta](#large-entities-pattern)).  
* Hozzáférés-vezérlés megvalósítása (további információ: [Hozzáférés vezérlése megosztott hozzáférésű aláírásokkal](#control-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Csatlakozás az ügyfélhez
Bár a Table storage-ban a kapcsolatok modellezésének számos módja van, ne felejtse el, hogy a Table storage használatának két fő oka a méretezhetőség és a teljesítmény. Ha úgy találja, hogy sok olyan kapcsolatot modellez, amely veszélyezteti a megoldás teljesítményét és méretezhetőségét, kérdezze meg magától, hogy szükség van-e az összes adatkapcsolat nak a táblatervbe való felépítésére. Előfordulhat, hogy egyszerűsítheti a tervezést, és javíthatja a megoldás méretezhetőségét és teljesítményét, ha hagyja, hogy az ügyfélalkalmazás végrehajtsa a szükséges illesztéseket.  

Ha például olyan kis táblákkal rendelkezik, amelyek nem gyakran változnak, az adatokat egyszer bekeresheti, és gyorsítótáraszthatja az ügyfélen. Ezzel elkerülhetők az ismétlődő körbeutak, hogy ugyanazokat az adatokat beolvashassa. Az ebben az útmutatóban megvizsgált példákban egy kis szervezet részlegei valószínűleg kicsik és ritkán változnak. Ez jó jelöltet jelent az olyan adatokra, amelyeket egy ügyfélalkalmazás egyszer tölthet le, és gyorsítótárazhatja a keresett adatokat.  

### <a name="inheritance-relationships"></a>Öröklési kapcsolatok
Ha az ügyfélalkalmazás egy öröklési kapcsolat részét képező osztályok at használ az üzleti entitások képviseletére, könnyen megőrizheti ezeket az entitásokat a Table storage-ban. Előfordulhat például, hogy az ügyfélalkalmazásban a következő osztályok vannak `Person` definiálva, ahol egy absztrakt osztály található.

![Öröklési kapcsolatok diagramja][3]

A táblázattároló két konkrét osztályának példányait egyetlen `Person` tábla használatával is megőrizheti. A következőhez hasonló entitásokat használjon:  

![Vevő entitást és alkalmazotti entitást megjelenítő ábra][4]

Az ügyfélkódban lévő több entitástípussal való munkáról az útmutató későbbi részében a [Heterogén entitástípusok munkája](#work-with-heterogeneous-entity-types) című témakörben talál további információt. Ez példákat tartalmaz arra, hogyan ismerheti fel az entitástípust az ügyfélkódban.  

## <a name="table-design-patterns"></a>Táblatervezési minták
Az előző szakaszokban megismerkedhet azzal, hogyan optimalizálhatja a táblatervet mind az entitásadatok lekérdezések, mind az entitásadatok beszúrása, frissítése és törlése érdekében. Ez a szakasz a Table storage-hoz megfelelő mintákat ismerteti. Ezen kívül látni fogja, hogyan lehet gyakorlatilag kezelni néhány kérdést és kompromisszumot felvetett korábban ebben az útmutatóban. Az alábbi ábra összefoglalja a különböző minták közötti kapcsolatokat:  

![Táblázattervezési minták diagramja][5]

A mintatérkép kiemel néhány kapcsolatot a minták (kék) és az anti-minták (narancssárga) között, amelyeket ez az útmutató dokumentál. Vannak persze sok más mintákat, amelyeket érdemes megfontolni. A Table storage egyik legfontosabb forgatókönyve például a [parancslekérdezési felelősség elkülönítési](https://msdn.microsoft.com/library/azure/jj554200.aspx) minta [materializált nézetminta](https://msdn.microsoft.com/library/azure/dn589782.aspx) használata.  

### <a name="intra-partition-secondary-index-pattern"></a>Partíción belüli másodlagos indexminta
Az egyes entitások több példányát különböző `RowKey` értékek használatával tárolja (ugyanabban a partícióban). Ez lehetővé teszi a gyors és hatékony keres, és alternatív rendezési sorrendek segítségével különböző `RowKey` értékeket. A másolatok közötti frissítések az EGT-k használatával konzisztensek maradhatnak.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A táblatárolás automatikusan indexeli `RowKey` az entitásokat a és az `PartitionKey` értékek használatával. Ez lehetővé teszi, hogy egy ügyfélalkalmazás hatékonyan lekérje az entitást ezeknek az értékeknek a használatával. A következő táblastruktúra használatával például egy ügyfélalkalmazás pontlekérdezéssel lekérheti az egyes alkalmazottentitásokat a részleg `PartitionKey` `RowKey` név és az alkalmazottazonosító (a és az értékek) használatával. Az ügyfél az egyes részlegeken belül alkalmazotti azonosító szerint rendezett entitásokat is lekérhet.

![Alkalmazotti entitás ábrája][6]

Ha egy másik tulajdonság, például az e-mail-cím értéke alapján is meg szeretne találni egy alkalmazottentitást, kevésbé hatékony partícióvizsgálatot kell használnia az egyezés megtalálásához. Ennek az az oka, hogy a Table storage nem biztosít másodlagos indexeket. Ezenkívül nincs lehetőség az alkalmazottak listájának más sorrendbe rendezésére, mint `RowKey` a sorrend.  

#### <a name="solution"></a>Megoldás
A másodlagos indexek hiányának megkerüléséhez tárolhatja az egyes entitások több `RowKey` példányát, és minden egyes példány más értéket használ. Ha a következő struktúrákkal rendelkező entitást tárol, hatékonyan lekérheti az alkalmazottentitásokat az e-mail cím vagy az alkalmazottazonosító alapján. A alkalmazás előtagértékei, `RowKey` `empid_`és `email_` lehetővé teszik egyetlen alkalmazott vagy alkalmazotti tartomány lekérdezését e-mail címek vagy alkalmazottazonosítók tartományának használatával.  

![Eltérő RowKey értékkel rendelkező alkalmazotti entitást ábrázoló ábra][7]

A következő két szűrőfeltétel (az egyik az alkalmazottazonosító, a másik az e-mail-cím alapján keresfel) pontlekérdezéseket határoz meg:  

* $filter=(PartitionKey eq 'Sales') és (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Sales') és (RowKey eq 'email_jonesj@contoso.com')  

Ha egy csoport alkalmazotti entitást kérdez le, megadhatja az alkalmazotti azonosítósorrendben rendezett tartományt, vagy az e-mail címsorrendben rendezett tartományt. A megfelelő előtaggal rendelkező entitások lekérdezése a ban. `RowKey`  

* Ha a 000100 és 000199 közötti tartományban lévő alkalmazottazonosítóval rendelkező értékesítési osztály összes alkalmazottját meg szeretné találni, használja a következőt: $filter=(PartitionKey eq 'Sales') és (RowKey ge 'empid_000100') és (RowKey le 'empid_000199')  
* Ha az értékesítési osztály összes alkalmazottját "a" betűvel kezdődő e-mail címmel szeretné megtalálni, használja a következőt: $filter=(PartitionKey eq 'Sales') és (RowKey ge 'email_a') és (RowKey lt 'email_b')  
  
Az előző példákban használt szűrőszintaxis a Table Storage REST API-ból származik. További információt az [Entitások lekérdezése](https://msdn.microsoft.com/library/azure/dd179421.aspx)című témakörben talál.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A table storage használata viszonylag olcsó, így az ismétlődő adatok tárolásának költsége nem lehet komoly probléma. Azonban mindig ki kell értékelnie a tervezési költsége alapján a várható tárolási követelmények, és csak duplikált entitások, hogy támogassa a lekérdezéseket az ügyfélalkalmazás fog futni.  
* Mivel a másodlagos index entitások ugyanabban a partícióban vannak tárolva, mint az eredeti entitások, győződjön meg arról, hogy nem lépi túl az egyes partíciók méretezhetőségi céljait.  
* Az ismétlődő entitások konzisztensek maradhatnak egymással az EGT-k használatával az entitás két példányának atomi frissítéséhez. Ez azt jelenti, hogy egy entitás összes példányát ugyanabban a partícióban kell tárolnia. További információ: [Entitáscsoport-tranzakciók használata.](#entity-group-transactions)  
* A használt értéknek minden entitáshoz egyedinek `RowKey` kell lennie. Fontolja meg az összetett kulcsértékek használatát.  
* A kitöltési numerikus értékek `RowKey` (például a 000223-as alkalmazotti azonosító) lehetővé teszi a helyes rendezést és szűrést a felső és alsó határok alapján.  
* Nem feltétlenül kell duplikálnia az entitás összes tulajdonságát. Ha például azok a lekérdezések, amelyek az entitásokat `RowKey` az alkalmazott életkorának soha nem szükséges e-mail címével keresik, ezek az entitások a következő struktúrával rendelkezhetnek:

  ![Alkalmazotti entitás ábrája][8]

* Általában jobb, ha ismétlődő adatokat tárol, és biztosítja, hogy egyetlen lekérdezéssel letudja olvasni az összes szükséges adatot, mint hogy egy entitást, egy másikat pedig a szükséges adatok kereséséhez használjon.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Használja ezt a mintát, ha:

- Az ügyfélalkalmazásnak különböző kulcsok használatával kell lekérnie az entitásokat.
- Az ügyfélnek különböző rendezési sorrendben kell lekérnie az entitásokat.
- Az egyes entitásokat különböző egyedi értékek használatával azonosíthatja.

Győződjön meg azonban arról, hogy nem lépi túl a partíció méretezhetőségi `RowKey` korlátait, amikor entitás-keres a különböző értékek használatával hajt végre entitáskeresőket.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Partíciók közötti másodlagos indexminta](#inter-partition-secondary-index-pattern)
* [Összetett kulcsminta](#compound-key-pattern)
* [Entitáscsoport-tranzakciók](#entity-group-transactions)
* [Heterogén entitástípusokkal végzett munka](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Partíciók közötti másodlagos indexminta
Az egyes entitások több példányát különböző értékek használatával, külön `RowKey` partíciókban vagy különálló táblákban tárolja. Ez lehetővé teszi a gyors és hatékony keres, és alternatív rendezési sorrendek segítségével különböző `RowKey` értékeket.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A táblatárolás automatikusan indexeli `RowKey` az entitásokat a és az `PartitionKey` értékek használatával. Ez lehetővé teszi, hogy egy ügyfélalkalmazás hatékonyan lekérje az entitást ezeknek az értékeknek a használatával. A következő táblastruktúra használatával például egy ügyfélalkalmazás pontlekérdezéssel lekérheti az egyes alkalmazottentitásokat a részleg `PartitionKey` `RowKey` név és az alkalmazottazonosító (a és az értékek) használatával. Az ügyfél az egyes részlegeken belül alkalmazotti azonosító szerint rendezett entitásokat is lekérhet.  

![Alkalmazotti entitás ábrája][9]

Ha azt is szeretné, hogy egy alkalmazott entitás értéke alapján egy másik tulajdonság, például az e-mail-cím, meg kell használni a kevésbé hatékony partíciós vizsgálat találni egyezést. Ennek az az oka, hogy a Table storage nem biztosít másodlagos indexeket. Ezenkívül nincs lehetőség az alkalmazottak listájának más sorrendbe rendezésére, mint `RowKey` a sorrend.  

Nagy mennyiségű tranzakcióra számít ezekkel az entitásokkal szemben, és minimálisra szeretné csökkenteni az ügyfél korlátozásának kockázatát.  

#### <a name="solution"></a>Megoldás
A másodlagos indexek hiányának megkerüléséhez tárolhatja az egyes entitások `PartitionKey` több `RowKey` példányát, és minden egyes példányt különböző és értékek használatával. Ha a következő struktúrákkal rendelkező entitást tárol, hatékonyan lekérheti az alkalmazottentitásokat az e-mail cím vagy az alkalmazottazonosító alapján. A alkalmazáselőtag `PartitionKey` `empid_`értékei, és `email_` lehetővé teszik a lekérdezéshez használni kívánt index azonosítását.  

![Az elsődleges indexet és a másodlagos indexet mutató alkalmazotti entitást megjelenítő ábra][10]

A következő két szűrőfeltétel (az egyik az alkalmazottazonosító, a másik az e-mail-cím alapján keresfel) pontlekérdezéseket határoz meg:  

* $filter=(PartitionKey eq 'empid_Sales') és (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') és (RowKey eq 'jonesj@contoso.com')  

Ha egy csoport alkalmazotti entitást kérdez le, megadhatja az alkalmazotti azonosítósorrendben rendezett tartományt, vagy az e-mail címsorrendben rendezett tartományt. A megfelelő előtaggal rendelkező entitások lekérdezése a ban. `RowKey`  

* A **000100 és 000199** közötti alkalmazottazonosítóval **000199**rendelkező értékesítési osztály összes alkalmazottjának megkereséséhez alkalmazottazonosító-sorrendben, használja a következőt: $filter=(PartitionKey eq 'empid_Sales') és (RowKey ge '000100') és (RowKey le '000199')  
* Ha az értékesítési osztály összes alkalmazottját "a" betűvel kezdődő e-mail címmel szeretné megtalálni, e-mail ben kell rendezni, használja a következőt: $filter=(PartitionKey eq 'email_Sales') és (RowKey ge 'a') és (RowKey lt 'b')  

Vegye figyelembe, hogy az előző példákban használt szűrőszintaxis a Table Storage REST API-ból származik. További információt az [Entitások lekérdezése](https://msdn.microsoft.com/library/azure/dd179421.aspx)című témakörben talál.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Az ismétlődő entitások végül konzisztensek maradhatnak egymással az elsődleges és másodlagos indexentitások karbantartásához a [Végül konzisztens tranzakciók minta](#eventually-consistent-transactions-pattern) használatával.  
* A táblatárolás használata viszonylag olcsó, ezért az ismétlődő adatok tárolásának költsége nem lehet komoly probléma. Azonban mindig értékelje ki a tervezési költséget a várható tárolási követelmények alapján, és csak ismétlődő entitásokat adjon hozzá az ügyfélalkalmazás által futtatott lekérdezések támogatásához.  
* A használt értéknek minden entitáshoz egyedinek `RowKey` kell lennie. Fontolja meg az összetett kulcsértékek használatát.  
* A kitöltési numerikus értékek `RowKey` (például a 000223-as alkalmazotti azonosító) lehetővé teszi a helyes rendezést és szűrést a felső és alsó határok alapján.  
* Nem feltétlenül kell duplikálnia az entitás összes tulajdonságát. Ha például azok a lekérdezések, amelyek az entitásokat `RowKey` az alkalmazott életkorának soha nem szükséges e-mail címével keresik, ezek az entitások a következő struktúrával rendelkezhetnek:
  
  ![Másodlagos indexszel rendelkező alkalmazotti entitást megjelenítő ábra][11]
* Általában jobb, ha ismétlődő adatokat tárol, és biztosítja, hogy egyetlen lekérdezéssel letudja olvasni az összes szükséges adatot, mint hogy egy lekérdezést használjon egy entitás megkereséséhez a másodlagos index használatával, és egy másikat a szükséges adatok lekérdezéséhez az elsődleges indexben.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Használja ezt a mintát, ha:

- Az ügyfélalkalmazásnak különböző kulcsok használatával kell lekérnie az entitásokat.
- Az ügyfélnek különböző rendezési sorrendben kell lekérnie az entitásokat.
- Az egyes entitásokat különböző egyedi értékek használatával azonosíthatja.

Akkor használja ezt a mintát, ha el szeretné kerülni a partíció méretezhetőségi korlátait, amikor entitás-keresőket hajt végre a különböző `RowKey` értékek használatával.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Végül konzisztens tranzakciók minta](#eventually-consistent-transactions-pattern)  
* [Partíción belüli másodlagos indexminta](#intra-partition-secondary-index-pattern)  
* [Összetett kulcsminta](#compound-key-pattern)  
* [Entitáscsoport-tranzakciók](#entity-group-transactions)  
* [Heterogén entitástípusokkal végzett munka](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Végül konzisztens tranzakciók minta
Engedélyezze a partícióhatárokon vagy a tárolórendszer-határokon keresztül imfeonos viselkedést az Azure-várólisták használatával.  

#### <a name="context-and-problem"></a>Kontextus és probléma
EGT-k lehetővé teszik az atomi tranzakciók több entitások, amelyek ugyanazt a partíciókulcsot. A teljesítmény és a méretezhetőség miatt dönthet úgy, hogy a konzisztenciakövetelményekkel rendelkező entitásokat külön partíciókon vagy külön tárolórendszerben tárolja. Ilyen esetben nem használhatja az EGT-ket a konzisztencia fenntartásához. Előfordulhat például, hogy a következők között fenn kell tartania a végleges konzisztenciát:  

* Entitások két különböző partíciók ugyanabban a táblában, különböző táblákban vagy különböző tárfiókokban tárolt.  
* A Table storage és a Blob storage-ban tárolt blob.  
* A Table storage-ban tárolt entitás és egy fájl egy fájlrendszerben.  
* Egy entitás tárolt table storage, de indexelt az Azure Cognitive Search használatával.  

#### <a name="solution"></a>Megoldás
Az Azure-várólisták használatával olyan megoldást valósíthat meg, amely két vagy több partíció vagy tárolórendszer végső konzisztenciáját biztosítja.

Ennek a megközelítésnek a szemléltetéséhez tegyük fel, hogy a korábbi alkalmazotti entitások archiválásához szükséges követelmény. A korábbi munkavállalói entitásokat ritkán kérdezik le, és ki kell zárni őket minden olyan tevékenységből, amely a jelenlegi alkalmazottakkal foglalkozik. Ennek a követelménynek a megvalósításához az aktív alkalmazottakat az **Aktuális** táblában, a korábbi alkalmazottakat pedig az **Archiválás** táblában tárolja. Az alkalmazott archiválásához törölnie kell az entitást az **aktuális** táblából, és hozzá kell adnia az entitást az **Archiválás** táblához.

De nem használhat EGT-t a két művelet végrehajtásához. Annak elkerülése érdekében, hogy egy hiba miatt egy entitás mindkét vagy egyik táblában is megjelenjen, az archiválási műveletnek végül konzisztensnek kell lennie. A következő szekvenciadiagram a művelet lépéseit ismerteti.  

![Megoldásdiagram a végleges konzisztenciához][12]

Az ügyfél úgy indítja el az archiválási műveletet, hogy egy üzenetet helyez el egy Azure-várólistán (ebben a példában az alkalmazottak #456 archiválására). A dolgozói szerepkör lekérdezi az új üzenetek várólistáját; ha talál egyet, beolvassa az üzenetet, és egy rejtett másolatot hagy a várólistán. A munkavégző szerepkör ezután lekéri az entitás egy példányát az **Aktuális** táblából, beszúr egy másolatot az **Archiválás** táblába, majd törli az eredetit az **Aktuális** táblából. Végül, ha nem voltak hibák az előző lépésekből, a feldolgozói szerepkör törli a rejtett üzenetet a várólistából.  

Ebben a példában a diagram 4. **Archive** Hozzáadhat az alkalmazottat egy blobhoz a Blob storage-ban vagy egy fájlhoz egy fájlrendszerben.  

#### <a name="recover-from-failures"></a>Helyreállítás a hibákból
Fontos, hogy a 4-5 lépésben a diagram *idempotent* 4-5. Táblázattároló használata esetén a 4. az 5. Ha egy másik tárolórendszert használ, megfelelő idempotens műveletet kell használnia.  

Ha a dolgozói szerepkör soha nem fejezi be a 6. A feldolgozói szerepkör ellenőrizheti, hogy a várólistán lévő üzenetek et hányszor olvasták el, és szükség esetén megjelölheti azt "méreg" üzenetként vizsgálatra egy külön várólistába küldésével. A várólista-üzenetek olvasásáról és a várólista-szám ellenőrzéséről az [Üzenetek beolvasása](https://msdn.microsoft.com/library/azure/dd179474.aspx)című témakörben talál további információt.  

A table storage és a Queue storage néhány hiba átmeneti hiba, és az ügyfélalkalmazásnak megfelelő újrapróbálkozási logikát kell tartalmaznia azok kezeléséhez.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ez a megoldás nem biztosítja a tranzakciók elkülönítését. Előfordulhat például, hogy egy ügyfél olvassa az **Aktuális** és **az Archiválás** táblákat, amikor a feldolgozói szerepkör a diagram 4-5. Az adatok végül konzisztensek lesznek.  
* Meg kell győződnie arról, hogy a 4-5 lépések idempotens annak érdekében, hogy a végleges konzisztencia.  
* A megoldás több várólisták és feldolgozói szerepkör-példányok használatával méretezhető.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ezt a mintát akkor használja, ha garantálni szeretné a különböző partíciókban vagy táblákban lévő entitások közötti végleges konzisztenciát. Ezt a mintát kiterjesztheti a table storage és blob storage és más nem Azure Storage-adatforrások, például egy adatbázis vagy a fájlrendszer közötti műveletek végleges konzisztenciájának biztosítása érdekében.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Entitáscsoport-tranzakciók](#entity-group-transactions)  
* [Egyesítés vagy csere](#merge-or-replace)  

> [!NOTE]
> Ha a tranzakcióelkülönítés fontos a megoldás számára, fontolja meg a táblák újratervezését az EGT-k használatához.  
> 
> 

### <a name="index-entities-pattern"></a>Entitások indexelési mintája
Indexentitások karbantartása az entitások listájának visszaadását tartalmazó hatékony keresések engedélyezéséhez.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A táblatárolás automatikusan indexeli `RowKey` az entitásokat a és az `PartitionKey` értékek használatával. Ez lehetővé teszi, hogy egy ügyfélalkalmazás hatékonyan lekérje az entitást egy pontlekérdezés használatával. A következő táblastruktúra használatával például egy ügyfélalkalmazás hatékonyan lekérheti az egyes alkalmazotti entitásokat `PartitionKey` `RowKey`a részleg név és az alkalmazottazonosító (az és) használatával.  

![Alkalmazotti entitás ábrája][13]

Ha azt is szeretné, hogy egy másik, nem egyedi tulajdonság, például a vezetéknév értéke alapján letudja olvasni az alkalmazottentitások listáját, kevésbé hatékony partícióvizsgálatot kell használnia. Ez a vizsgálat találatokat talál, ahelyett, hogy indexet használna a közvetlen megkeresésükhez. Ennek az az oka, hogy a Table storage nem biztosít másodlagos indexeket.  

#### <a name="solution"></a>Megoldás
Ha engedélyezni szeretné a vezetéknév alapján történő keresmel az előző entitásstruktúrával, az alkalmazottazonosítók listáját kell karbantartania. Ha egy adott vezetéknévvel rendelkező alkalmazotti entitásokat (például Jones) szeretne beolvasni, először meg kell keresnie az alkalmazottak alkalmazotti azonosítóinak listáját, amelynek vezetékneve Jones, majd beolvasnia ezeket az alkalmazottentitásokat. Az alkalmazotti azonosítók listájának tárolására három fő lehetőség van:  

* Használja a Blob storage-ot.  
* Indexentitások létrehozása ugyanabban a partícióban, mint az alkalmazott entitások.  
* Indexentitások létrehozása külön partíción vagy táblában.  

1. lehetőség: Blob-tároló használata  

Hozzon létre egy blobot minden egyedi vezetéknévhez, `PartitionKey` és minden `RowKey` blobtárolóban hozzon létre egy listát a (részleg) és (alkalmazotti azonosító) értékekről az ilyen vezetéknévvel rendelkező alkalmazottak számára. Amikor hozzáad vagy töröl egy alkalmazottat, győződjön meg arról, hogy a megfelelő blob tartalma végül konzisztens az alkalmazottentitásokkal.  

2. lehetőség: Indexentitások létrehozása ugyanabban a partícióban  

A következő adatokat tároló indexentitásokat használja:  

![Alkalmazotti entitást ábrázoló ábra azonos vezetéknevű alkalmazottazonosítók listáját tartalmazó karakterlánccal][14]

A `EmployeeIDs` tulajdonság tartalmazza az alkalmazottak alkalmazotti azonosítóinak listáját, `RowKey`a vezetéknevét a ban.  

A következő lépések ismertetik azt a folyamatot, amelyet új alkalmazott hozzáadásakor kell követnie. Ebben a példában egy 000152-es azonosítóval és Jones vezetéknevű alkalmazottal adunk hozzá egy alkalmazottat az értékesítési osztályhoz:  

1. Az "Értékesítés" `PartitionKey` és a `RowKey` "Jones" értékkel rendelkező indexentitás lekérése. Mentse az entitás ETag-jét a 2.  
2. Hozzon létre egy entitáscsoport-tranzakciót (azaz kötegműveletet),`PartitionKey` amely beszúrja `RowKey` az új alkalmazottentitást ( "Értékesítés"`PartitionKey` és "000152 érték"), és frissíti az index entitást ( "Értékesítés" és `RowKey` "Jones érték"). Az EGT ezt úgy éri el, hogy hozzáadja az új alkalmazottazonosítót az EmployeeIDs mező listájához. Az EGT-kről további információt az [Entitáscsoport tranzakciói című témakörben talál.](#entity-group-transactions)  
3. Ha az EGT egy optimista egyidejűségi hiba miatt sikertelen (azaz valaki más módosította az index entitást), akkor az 1.  

Hasonló megközelítést használhat egy alkalmazott törléséhez, ha a második lehetőséget használja. Az alkalmazott vezetéknevének módosítása valamivel összetettebb, mivel olyan EGT-t kell futtatnia, amely három entitást frissít: az alkalmazott entitást, a régi vezetéknév indexentitását és az új vezetéknév indexentitását. A módosítások végrehajtása előtt be kell olvasnia az egyes entitásokat, hogy lekérhesse azokat az ETag értékeket, amelyek segítségével optimista egyidejűség használatával hajthatja végre a frissítéseket.  

A következő lépések felvázolják azt a folyamatot, amelyet akkor kell követnie, ha egy adott vezetéknévvel rendelkező összes alkalmazottat meg kell keresnie egy részlegen. Ebben a példában az értékesítési osztályban a Jones vezetéknevű alkalmazottakat keressük:  

1. Az "Értékesítés" `PartitionKey` és a `RowKey` "Jones" értékkel rendelkező indexentitás lekérése.  
2. Elemezd a `EmployeeIDs` mezőben szereplő alkalmazottazonosítók listáját.  
3. Ha további információra van szüksége az egyes alkalmazottakról (például az e-mail címükről), `RowKey` az "Értékesítés" érték és `PartitionKey` a 2.  

3. lehetőség: Indexentitások létrehozása külön partíción vagy táblában  

Ehhez a beállításhoz használjon indexentitásokat, amelyek a következő adatokat tárolják:  

![Alkalmazotti entitást ábrázoló ábra azonos vezetéknevű alkalmazottazonosítók listáját tartalmazó karakterlánccal][15]

A `EmployeeIDs` tulajdonság tartalmazza az alkalmazottak alkalmazotti azonosítóinak listáját, `RowKey`a vezetéknevét a ban.  

Az EGT-k nem használhatók a konzisztencia fenntartásához, mert az index entitások az alkalmazott entitások tól különálló partíción vannak. Győződjön meg arról, hogy az index entitások végül összhangban vannak az alkalmazottentitásokkal.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ehhez a megoldáshoz legalább két lekérdezésre van szükség az egyező entitások lekéréséhez: az egyik az indexentitások lekérdezéséhez az értékek listájának beolvasásához, `RowKey` majd a lekérdezések a lista minden egyes entitásának lekéréséhez.  
* Mivel egy adott entitás maximális mérete 1 MB, a megoldás 2. Ha az alkalmazotti azonosítók listája valószínűleg több mint 1 MB méretű, használja az 1-es lehetőséget, és tárolja az indexadatokat a Blob storage-ban.  
* Ha a 2-es lehetőséget használja (EGTs használatával kezeli az alkalmazottak hozzáadását és törlését, valamint módosítja az alkalmazott vezetéknevét), ki kell értékelnie, hogy a tranzakciók mennyisége megközelíti-e egy adott partíció méretezhetőségi korlátait. Ebben az esetben érdemes egy végül konzisztens megoldást (1. vagy 3. lehetőség). Ezek a várólisták segítségével kezeli a frissítési kérelmeket, és lehetővé teszi, hogy tárolja az index entitások egy külön partíciót az alkalmazott entitások.  
* A megoldás 2. Például be szeretné olvasni a Jones vezetéknevű alkalmazottak listáját az értékesítési részlegben. Ha azt szeretné, hogy az összes Jones vezetéknevű alkalmazott at a teljes szervezetben megkeresse, használja az 1.
* Létrehozhat egy várólista-alapú megoldást, amely biztosítja a végleges konzisztenciát. További részletekért olvassa el a [Végül konzisztens tranzakciók minta című témakört.](#eventually-consistent-transactions-pattern)  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ezt a mintát akkor használja, ha olyan entitásokat szeretne keresni, amelyek közös tulajdonságértékkel rendelkeznek, például a Jones vezetéknevű összes alkalmazottat.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcsminta](#compound-key-pattern)  
* [Végül konzisztens tranzakciók minta](#eventually-consistent-transactions-pattern)  
* [Entitáscsoport-tranzakciók](#entity-group-transactions)  
* [Heterogén entitástípusokkal végzett munka](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Denormalizációs minta
A kapcsolódó adatokat egyetlen entitásban kombinálva lehetővé teszi az összes szükséges adat egyetlen pontos lekérdezéssel történő beolvasását.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Relációs adatbázisban általában normalizálhatja az adatokat, hogy eltávolítsa a párhuzamosságokat, amelyek akkor következnek be, amikor a lekérdezések több táblából kérnek adatokat. Ha normalizálja az adatokat az Azure-táblákban, több körutat kell tennie az ügyfélről a kiszolgálóra a kapcsolódó adatok lekéréséhez. A következő táblázatszerkezettel például két oda-vissza útra van szükség egy részleg részleteinek beolvasásához. Az egyik út lekéri a részleg entitást, amely tartalmazza a vezető azonosítóját, a második pedig a vezető adatait egy alkalmazotti entitásban.  

![A részleg és az alkalmazotti entitás ábrája][16]

#### <a name="solution"></a>Megoldás
Ahelyett, hogy az adatokat két különálló entitásban tárolnák, denormalizálják az adatokat, és a részlegentitásban megőrzik a vezető adatainak másolatát. Példa:  

![Denormalizált és egyesített osztályú entitás ábrája][17]

Az ezekkel a tulajdonságokkal tárolt részlegentitások esetén most egy pontlekérdezés használatával lekérheti a részleggel kapcsolatos összes szükséges adatot.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Bizonyos adatok kétszeri tárolása bizonyos költségköltség-többlettel jár. A table storage-ba irányuló kevesebb kérelemből származó teljesítményelőny általában meghaladja a tárolási költségek marginális növekedését. Továbbá ezt a költséget részben ellensúlyozza a részleg adatainak lekéréséhez szükséges tranzakciók számának csökkenése.  
* Fenn kell tartania a vezetők adatait tároló két entitás konzisztenciáját. A konzisztencia-problémát úgy oldhatja meg, hogy egts használatával több entitást frissít egyetlen atomtranzakcióban. Ebben az esetben a részlegentitás és az osztályvezető alkalmazotti entitása ugyanabban a partícióban van tárolva.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha gyakran kell kapcsolódó információkat keresnie. Ez a minta csökkenti az ügyfél által a szükséges adatok lekéréséhez szükséges lekérdezések számát.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcsminta](#compound-key-pattern)  
* [Entitáscsoport-tranzakciók](#entity-group-transactions)  
* [Heterogén entitástípusokkal végzett munka](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Összetett kulcsminta
Összetett `RowKey` értékek használatával engedélyezheti, hogy az ügyfél egypontos lekérdezéssel keressen kapcsolódó adatokat.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Relációs adatbázisban természetes, hogy a lekérdezésekben illesztések használatával egyetlen lekérdezésben adja vissza a kapcsolódó adatokat az ügyfélnek. Az alkalmazottazonosító segítségével például megkeresheti azon kapcsolódó entitások listáját, amelyek az adott alkalmazott teljesítményadatait tartalmazzák, és áttekintheti az adatokat.  

Tegyük fel, hogy alkalmazottentitásokat tárol a Table storage-ban a következő struktúra használatával:  

![Alkalmazotti entitás ábrája][18]

Az értékelésekkel és a teljesítménysel kapcsolatos előzményadatokat is tárolnia kell minden olyan évre vonatkozóan, amikor az alkalmazott a szervezetnél dolgozott, és ezeket az információkat évszerint hozzá kell tudnia férni. Az egyik lehetőség egy másik tábla létrehozása, amely a következő struktúrával rendelkező entitásokat tárolja:  

![Az alkalmazotti felülvizsgálati entitás ábrája][19]

Ezzel a módszerrel dönthet úgy, hogy egyes adatokat (például a keresztnevet és a vezetéknevet) duplikál az új entitásban, hogy egyetlen kéréssel lekérje az adatokat. Azonban nem tarthatja fenn az erős konzisztenciát, mert nem használhatja az EGT-t a két entitás atomi frissítéséhez.  

#### <a name="solution"></a>Megoldás
Új entitástípus tárolása az eredeti táblában a következő struktúrával rendelkező entitások használatával:  

![Alkalmazotti entitás ábrája összetett kulccsal][20]

Figyelje `RowKey` meg, hogy a most egy összetett kulcs, amely az alkalmazottazonosítóból és a felülvizsgálati adatok évéből áll. Ez lehetővé teszi az alkalmazott teljesítményének beolvasását és az adatok áttekintését egyetlen entitásra vonatkozó egyetlen kérelemmel.  

A következő példa bemutatja, hogyan lehet beolvasni egy adott alkalmazott összes ellenőrzési adatát (például a 000123 alkalmazottat az Értékesítési részlegben):  

$filter=(PartitionKey eq 'Sales') és (RowKey ge 'empid_000123') és (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Olyan megfelelő elválasztó karaktert kell használnia, amely megkönnyíti `RowKey` az érték elemzését: például **000123_2012**.  
* Ezt az entitást ugyanabban a partíción is tárolja, mint más entitásokat, amelyek ugyanahhoz az alkalmazotthoz kapcsolódó adatokat tartalmaznak. Ez azt jelenti, hogy az EGT-k segítségével erős konzisztencia fenntartásához.
* Érdemes megfontolni, hogy milyen gyakran fogja lekérdezni az adatokat annak megállapításához, hogy ez a minta megfelelő-e. Ha például ritkán fér hozzá az ellenőrzési adatokhoz, és gyakran a fő alkalmazotti adatokhoz, akkor azokat külön entitásként kell tartani.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ezt a mintát akkor használja, ha egy vagy több olyan kapcsolódó entitást kell tárolnia, amelyet gyakran lekérdez.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Entitáscsoport-tranzakciók](#entity-group-transactions)  
* [Heterogén entitástípusokkal végzett munka](#work-with-heterogeneous-entity-types)  
* [Végül konzisztens tranzakciók minta](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Log tail minta
A partícióhoz legutóbb hozzáadott *n* entitások lekérése fordított `RowKey` dátum- és idősorrendben rendezett érték használatával.  

> [!NOTE]
> Az Azure Cosmos DB-ben az Azure Table API által visszaadott lekérdezési eredmények nincsenek partíciókulcs vagy sorkulcs szerint rendezve. Így, bár ez a minta table storage, nem alkalmas az Azure Cosmos DB. A szolgáltatáskülönbségek részletes listáját lásd: [különbségek table API az Azure Cosmos DB és az Azure Table Storage.](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)

#### <a name="context-and-problem"></a>Kontextus és probléma
Gyakori követelmény, hogy le lehessen kérni a legutóbb létrehozott entitásokat, például az alkalmazott által benyújtott tíz legutóbbi költségigényt. A táblalekérdezések támogatják a `$top` lekérdezési műveletet, hogy az első *n* entitásokat egy készletből adják vissza. Nincs egyenértékű lekérdezési művelet a készlet utolsó *n* entitásának visszaadására.  

#### <a name="solution"></a>Megoldás
Az entitásokat olyan `RowKey` módon tárolja, amely természetesen fordított dátum/idő sorrendben rendezi, így a legutóbbi tétel mindig az első a táblában.  

Például ahhoz, hogy lelehessen kérni az alkalmazott által benyújtott tíz legutóbbi költségigénylést, használhat az aktuális dátumból/időpontból származó fordított osztásértéket. A következő C# kódminta egy módot mutat a megfelelő "fordított `RowKey` ticks" érték létrehozására, amely a legutóbbitól a legrégebbiig rendez:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

A dátum/idő értékhez a következő kód dal juthat vissza:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A táblalekérdezés így néz ki:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A fordított osztásértéket a kezdő nullákkal kell bejelölni, hogy a karakterlánc értéke a várt módon legyen.  
* Tisztában kell lennie a méretezhetőségi célok szintjén egy partíciót. Ügyeljen arra, hogy ne hozzon létre hot spot partíciókat.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ezt a mintát akkor használja, ha fordított dátum/idő sorrendben kell hozzáférnie az entitásokhoz, vagy amikor a legutóbb hozzáadott entitásokhoz kell hozzáférnie.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Előkészület / hozzáfűzés anti-minta](#prepend-append-anti-pattern)  
* [Entitások lekérése](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Nagy térfogatú törlési minta
Engedélyezze az entitások nagy mennyiségű törlését úgy, hogy az összes entitást a saját külön táblájukban egyidejűtörlésre tárolja. Az entitásokat a tábla törlésével törölheti.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Számos alkalmazás törli azokat a régi adatokat, amelyeknek már nem kell elérhetők az ügyfélalkalmazások számára, vagy amelyeket az alkalmazás egy másik adathordozóra archivált. Az ilyen adatokat általában dátum szerint azonosítja. Például van egy követelmény, hogy törölje a rekordokat az összes bejelentkezési kérelmek, amelyek több mint 60 napos.  

Az egyik lehetséges kialakítás a bejelentkezési kérelem dátumának `RowKey`és időpontjának használata a következőben:  

![Bejelentkezési kísérlet entitás ábrája][21]

Ez a megközelítés elkerüli a partíciós elérési pontokat, mert az alkalmazás beszúrhatja és törölheti a bejelentkezési entitásokat minden egyes felhasználóhoz egy külön partíción. Ez a megközelítés azonban költséges és időigényes lehet, ha nagy számú entitással rendelkezik. Először el kell végeznie egy táblavizsgálatot, hogy azonosíthassa az összes törlödő entitást, majd törölnie kell minden régi entitást. Csökkentheti a régi entitások törléséhez szükséges oda-vissza utak számát, ha több törlési kérelmet kötegel az EGT-kbe.  

#### <a name="solution"></a>Megoldás
A bejelentkezési kísérletek minden nap külön táblája. Az előző entitásterv segítségével elkerülheti az elérési pontokat, amikor entitásokat szúr be. A régi entitások törlése most már egyszerűen csak egy tábla törlése minden nap (egyetlen tárolási művelet), ahelyett, hogy több száz és ezer egyéni bejelentkezési entitások naponta keresése és törlése.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Támogatja-e a terv más módokon is, amelyekkel az alkalmazás az adatokat használja, például adott entitások keresését, más adatokhoz való csatolást vagy összesített adatok generálását?  
* Elkerüli a tervezési terv a forró pontokat, amikor új entitásokat szúr be?  
* Késésre számíthat, ha a törlés után újra fel szeretné használni ugyanazt a táblanevet. Jobb, ha mindig egyedi táblaneveket használ.  
* Bizonyos sebességkorlátozásvárható, amikor először használ egy új táblát, míg a Table Storage megtanulja a hozzáférési mintákat, és elosztja a partíciókat a csomópontok között. Érdemes figyelembe venni, hogy milyen gyakran kell új táblákat létrehoznia.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Akkor használja ezt a mintát, ha nagy mennyiségű entitást kell egyszerre törölnie.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Entitáscsoport-tranzakciók](#entity-group-transactions)
* [Entitások módosítása](#modify-entities)  

### <a name="data-series-pattern"></a>Adatsorok mintája
A teljes adatsorokat egyetlen entitásban tárolhatja, hogy minimalizálja a kérések számát.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Gyakori forgatókönyv, hogy egy alkalmazás tárolja az adatok sorozatát, amelyekáltalában egyszerre kell beolvasnia. Az alkalmazás például rögzítheti, hogy az egyes alkalmazottak óránként hány üzenetet küldenek, majd ezt az információt felhasználva megtudja, hogy az egyes felhasználók hány üzenetet küldtek az előző 24 órában. Egy terv lehet, hogy 24 entitást tárol minden alkalmazotthoz:  

![Az üzenetstatisztika entitás ábrája][22]

Ezzel a kialakítással könnyedén megkeresheti és frissítheti az entitást, hogy minden alkalmazotthoz frissítsen, amikor az alkalmazásnak frissítenie kell az üzenetszám értékét. Ahhoz azonban, hogy az információt a tevékenység diagramjának ábrázolásához az előző 24 órára vonatkozóan szeretné lekérni, 24 entitást kell beolvasnia.  

#### <a name="solution"></a>Megoldás
Használja a következő kialakítást, külön tulajdonsággal az üzenetek számának tárolására minden órára:  

![Külön tulajdonságokkal rendelkező üzenetstatisztika-entitást megjelenítő ábra][23]

Ezzel a kialakítással egyesítési művelettel frissítheti egy alkalmazott üzenetszámát egy adott órára. Most már lekérheti az összes szükséges információt a diagram ábrázolásához egyetlen entitásra vonatkozó kérelem használatával.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Ha a teljes adatsor nem fér el egyetlen entitás (egy entitás legfeljebb 252 tulajdonságok, használjon egy alternatív adattár, például egy blob.  
* Ha egyszerre több ügyfél is frissít egy entitást, az **ETag** segítségével valósíthatja meg az optimista egyidejűséget. Ha sok ügyfele van, előfordulhat, hogy magas a viszálykodás.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ezt a mintát akkor használja, ha egy adott entitáshoz társított adatsort kell frissítenie és beolvasnia.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Nagy entitások mintája](#large-entities-pattern)  
* [Egyesítés vagy csere](#merge-or-replace)  
* [Végül konzisztens tranzakciók minta](#eventually-consistent-transactions-pattern) (ha az adatsorokat egy blobban tárolja)  

### <a name="wide-entities-pattern"></a>Széles entitások mintája
Több fizikai entitás használatával 252-nél több tulajdonsággal rendelkező logikai entitásokat tároljon.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Egy adott entitás legfeljebb 252 tulajdonsággal rendelkezhet (kivéve a kötelező rendszertulajdonságokat), és összesen legfeljebb 1 MB adatot tárolhat. Relációs adatbázisban általában egy sor méretének minden korlátozását megkell kerülni egy új tábla hozzáadásával, és a kettő közötti 1 az egyhez kapcsolat érvényesítésével.  

#### <a name="solution"></a>Megoldás
A Table storage használatával több entitást is tárolhat, hogy egyetlen, 252-nél több tulajdonsággal rendelkező nagy üzleti objektumot képviseljen. Ha például az egyes alkalmazottak által az elmúlt 365 napban küldött üzenetek számát szeretné tárolni, a következő tervet használhatja, amely két különböző sémával rendelkező entitást használ:  

![Grafikus bemutató üzenet statisztika entitás Rowkey 01 és üzenet statisztika entitás Rowkey 02][24]

Ha olyan módosítást kell eszközre tennie, amelymindkét entitás frissítését igényli, hogy szinkronizálva legyenek egymással, használhat EGY EGT-t. Ellenkező esetben egyetlen egyesítési művelettel frissítheti az üzenetek számát egy adott napra vonatkozóan. Az egyes alkalmazottak összes adatának beolvasásához mindkét entitást be kell olvasnia. Ezt két hatékony kérelemmel teheti meg, amelyek a `PartitionKey` és egy `RowKey` értéket is használnak.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
Vegye figyelembe a következő pontot, amikor eldönti, hogyan valósítja meg ezt a mintát:  

* A teljes logikai entitás beolvasása legalább két tárolási tranzakciót foglal magában: egyet az egyes fizikai entitások lekéréséhez.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ezt a mintát akkor használja, ha olyan entitásokat kell tárolnia, amelyek mérete vagy tulajdonságainak száma meghaladja a Table storage egy adott entitásra vonatkozó korlátot.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Entitáscsoport-tranzakciók](#entity-group-transactions)
* [Egyesítés vagy csere](#merge-or-replace)

### <a name="large-entities-pattern"></a>Nagy entitások mintája
A Blob storage használatával nagy tulajdonságértékeket tárol.  

#### <a name="context-and-problem"></a>Kontextus és probléma
Egy adott entitás összesen legfeljebb 1 MB adatot tárolhat. Ha egy vagy több tulajdonság olyan értékeket tárol, amelyek hatására az entitás teljes mérete meghaladja ezt az értéket, nem tárolhatja a teljes entitást a Table storage-ban.  

#### <a name="solution"></a>Megoldás
Ha az entitás mérete meghaladja az 1 MB-ot, mert egy vagy több tulajdonság nagy mennyiségű adatot tartalmaz, tárolhatja az adatokat a Blob storage-ban, majd tárolhatja a blob címét az entitás egy tulajdonságában. Például tárolhatja egy alkalmazott fényképét a Blob storage-ban, és `Photo` a fényképre mutató hivatkozást az alkalmazott entitásának tulajdonában tárolhatja:  

![A Blob-tárolóra mutató fényképes karakterláncú alkalmazotti entitást megjelenítő ábra][25]

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* A table storage entitás és a Blob storage-beli adatok közötti végleges konzisztencia fenntartásához használja a [Végül konzisztens tranzakciók minta](#eventually-consistent-transactions-pattern) az entitások karbantartásához.
* Egy teljes entitás beolvasása legalább két tárolási tranzakciót foglal magában: az egyik az entitás lekéréséhez, a másik pedig a blobadatok beolvasásához.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Ezt a mintát akkor használja, ha olyan entitásokat kell tárolnia, amelyek mérete meghaladja az egyes entitások korlátját a Table storage-ban.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Végül konzisztens tranzakciók minta](#eventually-consistent-transactions-pattern)  
* [Széles entitások mintája](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Előleg/hozzáfűzés anti-minta
Ha nagy mennyiségű lapkával rendelkezik, növelje a méretezhetőséget azáltal, hogy a lapkákat több partícióra osztja.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A tárolt entitásokhoz való elő- vagy hozzáfűzés általában azt eredményezi, hogy az alkalmazás új entitásokat ad hozzá a partíciók sorozatának első vagy utolsó partíciójához. Ebben az esetben az összes lapka egy adott időpontban zajlik ugyanabban a partíción, ami egy hotspot. Ez megakadályozza, hogy a table storage terheléselosztási beszúrások több csomópontközött, és esetleg okozza az alkalmazás eléri a méretezhetőségi célok partíció. Vegyük például egy olyan alkalmazás esetét, amely naplózza az alkalmazottak hálózati és erőforrás-hozzáférését. Egy entitásstruktúra, például a következő, azt eredményezheti, hogy az aktuális óra partíciója hotspottá válik, ha a tranzakciók mennyisége eléri az egyes partíciók méretezhetőségi célját:  

![Alkalmazotti entitás ábrája][26]

#### <a name="solution"></a>Megoldás
A következő alternatív entitásstruktúra elkerüli a hotspotot egy adott partíción, mivel az alkalmazás naplózza az eseményeket:  

![Az alkalmazottentitást ábrázoló ábra, amelynek RowKey-azonosítója az év, a hónap, a nap, az óra és az eseményazonosítót összekapcsolja][27]

Figyelje meg ezt `PartitionKey` a `RowKey` példát, hogy mind a mind a, mind az összetett kulcsok. A `PartitionKey` részleg és az alkalmazottazonosító segítségével több partíció között osztja el a naplózást.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A minta megvalósítása során az alábbi pontokat vegye figyelembe:  

* Az alternatív kulcsstruktúra, amely elkerüli a gyakori elérésű partíciók létrehozása a beszúrások hatékonyan támogatja a lekérdezéseket az ügyfélalkalmazás teszi?  
* A tranzakciók várható mennyisége azt jelenti, hogy valószínűleg eléri az egyes partíciók méretezhetőségi céljait, és a Table storage szabályozza?  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?
Kerülje a prepend/hozzáfűzési anti-minta, ha a tranzakciók mennyisége valószínűleg eredményez sebességkorlátozást table storage egy gyakori elérésű partíció elérésekor.  

#### <a name="related-patterns-and-guidance"></a>Kapcsolódó minták és útmutatók
Az alábbi minták és útmutatók szintén hasznosak lehetnek a minta megvalósításakor:  

* [Összetett kulcsminta](#compound-key-pattern)  
* [Log tail minta](#log-tail-pattern)  
* [Entitások módosítása](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Adatanti-minta naplózása
Általában a Table storage helyett blobtárolót kell használnia a naplóadatok tárolásához.  

#### <a name="context-and-problem"></a>Kontextus és probléma
A naplóadatok gyakori használati oka egy adott dátum-idő tartományra vonatkozó naplóbejegyzések beolvasása. Például meg szeretné találni az összes hibát és kritikus üzenetet, amelyet az alkalmazás 15:04 és 15:06 között naplózott egy adott napon. Nem szeretné a naplóüzenet dátumát és időpontját használni a naplóentitások mentéséhez használt partíció meghatározásához. Ez egy gyorspartíciót eredményez, mert egy adott időpontban az `PartitionKey` összes naplóentitás ugyanazt az értéket fogja megosztani (lásd a [Prepend/Hozzáfűzés anti-minta).](#prepend-append-anti-pattern) Például a következő entitásséma egy naplóüzenet hez egy gyorspartíciót eredményez, mert az alkalmazás az összes naplóüzenetet az aktuális dátumra és órára írja a partícióra:  

![A naplóüzenet entitásának képe][28]

Ebben a példában a `RowKey` naplóüzenet dátumát és időpontját tartalmazza annak érdekében, hogy a naplóüzenetek dátum/idő sorrendben legyenek rendezve. Az `RowKey` üzenetazonosítót is tartalmaz, ha több naplóüzenet ugyanazt a dátumot és időt osztja meg.  

Egy másik megközelítés `PartitionKey` az, hogy egy, amely biztosítja, hogy az alkalmazás üzeneteket ír a partíciók tartományában. Ha például a naplóüzenet forrása lehetővé teszi az üzenetek több partíció közötti elosztását, a következő entitássémát használhatja:  

![A naplóüzenet entitásának képe][29]

A sémával azonban az a probléma, hogy az összes naplóüzenet egy adott időtartamra vonatkozó lekéréséhez a tábla minden partíciójában meg kell keresnie.

#### <a name="solution"></a>Megoldás
Az előző szakasz rávilágított arra a problémára, hogy a Table storage-t a naplóbejegyzések tárolására próbálja használni, és két nem megfelelő tervet javasolt. Az egyik megoldás egy gyorspartícióhoz vezetett, amely nek gyenge a teljesítménye a naplóüzenetek írásakor. A másik megoldás a lekérdezés idomának csökkenését eredményezte, mivel a tábla minden partíciójának be olvasására van szükség a naplóüzenetek adott időtartamú lekéréséhez. A Blob Storage jobb megoldást kínál az ilyen típusú forgatókönyvekhez, és így tárolja az Azure Storage analytics az általa gyűjtött naplóadatokat.  

Ez a szakasz ismerteti, hogy a Storage analytics hogyan tárolja a naplóadatokat a Blob storage-ban, amely szemlélteti ezt a megközelítést a általában lekérdező adatok tartomány szerint történő tárolásához.  

A Storage analytics a naplóüzeneteket több blobban, tagolt formátumban tárolja. A tagolt formátum megkönnyíti az ügyfélalkalmazások számára a naplóüzenetben szereplő adatok elemzését.  

A Storage Analytics elnevezési konvenciót használ a blobokhoz, amely lehetővé teszi a blob (vagy blobok) megkeresését, amelyek tartalmazzák azokat a naplóüzeneteket, amelyekről keres. Például egy blob nevű "queue/2014/07/31/1800/000001.log" naplóüzeneteket tartalmaz, amelyek kapcsolódnak a várólista-szolgáltatás az óra kezdődő 18:00 július 31, 2014. A "000001" azt jelzi, hogy ez az első naplófájl ebben az időszakban. Storage analytics is rögzíti az időbélyegek az első és az utolsó napló üzenetek a fájlban tárolt, a blob metaadatok részeként. A Blob storage API-ja lehetővé teszi a blobok névelőtag alapján való megkeresését egy tárolóban. A 18:00 órától kezdődő en a "queue/2014/07/31/1800" előtag megkereséséhez keresse meg a 18:00 órától kezdődő enalistanapló-adatokat tartalmazó összes blobot.  

A storage-elemzési pufferek belső naplózza az üzeneteket, majd rendszeresen frissíti a megfelelő blobot, vagy létrehoz egy újat a naplóbejegyzések legújabb kötegével. Ez csökkenti a Blob storage-ban végrehajtandó írások számát.  

Ha egy hasonló megoldást valósít meg a saját alkalmazásában, fontolja meg, hogyan kezelheti a megbízhatóság és a költség és a méretezhetőség közötti kompromisszumot. Más szóval értékelje ki, hogy milyen hatással van minden naplóbejegyzés a Blob storage-ba, ahogy az történik, összehasonlítva az alkalmazás frissítéseinek pufferelésével és a Blob storage-tárolóba kötegekben való írásával.  

#### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok
A naplóadatok tárolásának eldöntésekor vegye figyelembe a következő pontokat:  

* Ha olyan táblatervet hoz létre, amely elkerüli a potenciális gyakori elérésű partíciókat, előfordulhat, hogy nem tud hatékonyan hozzáférni a naplóadatokhoz.  
* A naplóadatok feldolgozásához az ügyfélnek gyakran sok rekordot kell betöltenie.  
* Bár a naplóadatok gyakran strukturált, blob storage lehet, hogy jobb megoldás.  

### <a name="implementation-considerations"></a>Implementálási szempontok
Ez a szakasz az előző szakaszokban ismertetett minták megvalósításakor figyelembe vesszen néhány szempontot. A szakasz nagy része c# nyelven írt példákat használ, amelyek a Storage Client Library-t (az írás időpontjában a 4.3.0-s verziót) használják.  

### <a name="retrieve-entities"></a>Entitások lekérése
Ahogy azt a [Tervezés lekérdezéshez](#design-for-querying)című szakaszban is tárgyaltuk, a leghatékonyabb lekérdezés pontlekérdezés. Bizonyos esetekben azonban előfordulhat, hogy több entitást kell beolvasnia. Ez a szakasz néhány gyakori módszert ismertet az entitások lekéréséhez a Storage Client Library használatával.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Pontlekérdezés futtatása a Tárolóügyféltár használatával
A pontlekérdezés futtatásának legegyszerűbb módja a **Tábla beolvasása** művelet használata. Ahogy az a következő C# kódrészletben látható, ez `PartitionKey` a művelet lekéri `RowKey` az "Értékesítés" és "212" értékű entitást:  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Figyelje meg, hogy ez a példa hogyan `EmployeeEntity`várja el, hogy a beolvasott entitás típusa legyen.  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Több entitás beolvasása a LINQ használatával
Több entitást is lekérhet a LINQ és a Storage Client Library használatával, és megadhat egy lekérdezést egy **where** záradékkal. A táblavizsgálat elkerülése érdekében mindig `PartitionKey` a Where záradékban kell szerepelnie az értékkel, és ha lehetséges, a `RowKey` tábla- és partíciós vizsgálatok elkerüléséhez. A táblázattárolás korlátozott számú összehasonlító operátort támogat (nagyobb, nagyobb vagy egyenlő, kisebb vagy egyenlő, egyenlő és nem egyenlő) a where záradékban való felhasználásra. A következő C# kódrészlet megkeresi az összes olyan alkalmazottat, akinek a vezetékneve "B" -vel kezdődik (feltételezve, hogy a `RowKey` vezetéknév tárolja) az értékesítési részlegben (feltéve, hogy az `PartitionKey` áruházak a részleg nevét tárolják):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Figyelje meg, hogy `RowKey` a `PartitionKey` lekérdezés határozza meg mind a és a jobb teljesítmény biztosítása érdekében.  

A következő kódminta egyenértékű funkciókat mutat be a folyékony API használatával (a folyékony API-król általában bővebben a [folyékony API tervezésének gyakorlati tanácsai](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)című témakörben talál:  

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
> A minta `CombineFilters` több módszert ágyaz be, hogy tartalmazza a három szűrőfeltételt.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Nagy számú entitás beolvasása lekérdezésből
Az optimális lekérdezés egy adott `PartitionKey` entitást `RowKey` ad vissza egy érték és egy érték alapján. Bizonyos esetekben azonban előfordulhat, hogy szükség van sok entitás tért vissza ugyanabból a partícióról, vagy akár sok partícióról. Mindig teljes mértékben tesztelje az alkalmazás teljesítményét az ilyen esetekben.  

A Table storage lekérdezése egyszerre legfeljebb 1000 entitást adhat vissza, és legfeljebb öt másodpercig futtatható. A table storage egy folytatási tokent ad vissza, amely lehetővé teszi, hogy az ügyfélalkalmazás kérje az entitások következő készletét, ha az alábbiak bármelyike igaz:

- Az eredményhalmaz több mint 1000 entitást tartalmaz.
- A lekérdezés nem fejeződött be öt másodpercen belül.
- A lekérdezés átlépi a partíció határát. 

A folytatási jogkivonatok működéséről a [Lekérdezés időmegszavazása és tördelés című témakörben talál](https://msdn.microsoft.com/library/azure/dd135718.aspx)további információt.  

Ha a storage-ügyfélkönyvtárat használja, automatikusan képes kezelni a folytatási jogkivonatokat, mivel entitásokat ad vissza a Table storage-ból. Például a következő C# kódminta automatikusan kezeli a folytatási jogkivonatokat, ha a Table storage válaszként adja vissza őket:  

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

A folytatási tokenek explicit használatával szabályozhatja, hogy az alkalmazás mikor olvassa be az adatok következő szegmensét. Ha például az ügyfélalkalmazás lehetővé teszi a felhasználók számára, hogy egy táblában tárolt entitások között lapozgatjanak, a felhasználó dönthet úgy, hogy nem lapoz a lekérdezés által beolvasott összes entitásközött. Az alkalmazás csak akkor használja a folytatási jogkivonatot a következő szegmens lekéréséhez, amikor a felhasználó befejezte a lapozást az aktuális szegmens összes entitásán keresztül. Ennek a megközelítésnek számos előnye van:  

* Korlátozhatja a Table tárolóból beolvasni kívánt adatok mennyiségét, és áthelyezheti a hálózaton.  
* A .NET-ben aszinkron I/O-t is elvégezhet.  
* Szerializálhatja a folytatási jogkivonatot az állandó tárolóba, így folytathatja az alkalmazás összeomlását.  

> [!NOTE]
> A folytatási jogkivonat általában 1000 entitást tartalmazó szegmenst ad vissza, bár kevesebb et tartalmazhat. Ez akkor is így van, ha korlátozza a lekérdezés által visszaadott bejegyzések számát a **Take** használatával a keresési feltételeknek megfelelő első n entitások visszaadásához. Table storage előfordulhat, hogy visszaad egy szegmenst, amely kevesebb, mint n entitások, valamint egy folytatási jogkivonatot, amely lehetővé teszi a fennmaradó entitások beolvasása.  
> 
> 

A következő C# kód bemutatja, hogyan lehet módosítani a szegmensen belül visszaadott entitások számát:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Kiszolgálóoldali vetítés
Egy entitás legfeljebb 255 tulajdonsággal rendelkezhet, és legfeljebb 1 MB méretű lehet. Amikor lekérdezi a táblát, és lekéri az entitásokat, előfordulhat, hogy nem kell az összes tulajdonságot, és elkerülheti az adatok felesleges átvitelét (a késés és a költség csökkentése érdekében). A kiszolgálóoldali vetület segítségével csak a szükséges tulajdonságokat viheti át. A következő példa csak `Email` a tulajdonságot `PartitionKey`(a , `RowKey` `Timestamp`, és `ETag`) olvassa be a lekérdezés által kiválasztott entitásokból.  

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

Figyelje `RowKey` meg, hogy az érték elérhető, még akkor is, ha nem szerepel a beolvasandó tulajdonságok listájában.  

### <a name="modify-entities"></a>Entitások módosítása
A Storage Client Library lehetővé teszi, hogy módosítsa a table storage-ban tárolt entitásokat entitások beszúrásával, törlésével és frissítésével. Az EGT-k segítségével több beszúrást kötegelhet, frissítheti és törölheti a műveleteket, így csökkentheti a szükséges adatváltások számát, és javíthatja a megoldás teljesítményét.  

Az EGT-t futtatáskor a storage-ügyfélkönyvtár futtatásakor gyakran okozott kivételek közé tartozik annak az entitásnak az indexe, amely a köteg sikertelensét okozta. Ez akkor hasznos, ha EGT-ket használó kódot használ.  

Azt is meg kell fontolnia, hogy a terv hogyan befolyásolja, hogy az ügyfélalkalmazás hogyan kezeli az egyidejűségi és frissítési műveleteket.  

#### <a name="managing-concurrency"></a>Az egyidejűség kezelése
Alapértelmezés szerint a Table Storage optimista egyidejűségi ellenőrzéseket valósít meg az egyes entitások szintjén beszúrási, egyesítési és törlési műveletekhez, bár az ügyfél kényszerítheti a Table storage-t az ellenőrzések megkerülésére. További információt az [Egyidejűség kezelése a Microsoft Azure Storage szolgáltatásban című témakörben talál.](../storage/common/storage-concurrency.md)  

#### <a name="merge-or-replace"></a>Egyesítés vagy csere
Az `Replace` `TableOperation` osztály metódusa mindig helyettesíti a table storage teljes entitását. Ha nem vesz fel egy tulajdonságot a kérelembe, ha az adott tulajdonság létezik a tárolt entitásban, a kérelem eltávolítja a tulajdonságot a tárolt entitásból. Hacsak nem szeretne egy tulajdonságot kifejezetten eltávolítani egy tárolt entitásból, minden tulajdonságot bele kell foglalnia a kérelembe.  

Az `TableOperation` osztály `Merge` metódusával csökkentheti a Table storage-ba küldött adatok mennyiségét, ha egy entitást frissíteni szeretne. A `Merge` metódus a tárolt entitás bármely tulajdonságát lecseréli a kérelemben szereplő entitás tulajdonságértékeire. Ez a módszer érintetlenül hagyja a tárolt entitás azon tulajdonságait, amelyek nem szerepelnek a kérelemben. Ez akkor hasznos, ha nagy entitásokkal rendelkezik, és csak néhány tulajdonságot kell frissítenie egy kérelemben.  

> [!NOTE]
> A `*Replace` `Merge` és a metódusok sikertelenek, ha az entitás nem létezik. Alternatív megoldásként használhatja `InsertOrReplace` a `InsertOrMerge` és a módszereket, amelyek új entitást hoznak létre, ha nem létezik.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Heterogén entitástípusokkal végzett munka
A táblatároló *séma nélküli* táblatároló. Ez azt jelenti, hogy egyetlen tábla többféle típusú entitásokat képes tárolni, így nagy rugalmasságot biztosít a tervezésben. A következő példa egy alkalmazotti és részlegentitásokat egyaránt tároló táblát mutat be:  

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
<th>Alkalmazottszáma</th>
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

Minden entitásnak `PartitionKey`rendelkeznie kell a , `RowKey`és `Timestamp` értékekkel, de bármilyen tulajdonságkészletet tartalmazhat. Továbbá, nincs semmi, amely jelzi, hogy milyen típusú entitás, kivéve, ha úgy dönt, hogy tárolja az információt valahol. Az entitástípus azonosítására két lehetőség van:  

* Készítse elő az `RowKey` entitástípust a `PartitionKey`(vagy esetleg a) Például, `EMPLOYEE_000123` `DEPARTMENT_SALES` vagy `RowKey` értékekként.  
* Használjon külön tulajdonságot az entitástípus rögzítéséhez, ahogy az az alábbi táblázatban látható.  

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
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Employee (Alkalmazott)</td>
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
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Employee (Alkalmazott)</td>
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
<th>Alkalmazottszáma</th>
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
<th>FirstName</th>
<th>LastName</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Employee (Alkalmazott)</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Az első lehetőség, amely az `RowKey`entitástípust a számára előírja, akkor hasznos, ha fennáll annak a lehetősége, hogy két különböző típusú entitás nak ugyanaz a kulcsértéke. Az azonos típusú entitásokat is csoportosítja a partícióban.  

Az ebben a szakaszban tárgyalt technikák különösen fontosak az[öröklési kapcsolatokról](#inheritance-relationships)szóló vita során.  

> [!NOTE]
> Fontolja meg egy verziószám megírása az entitástípus értékében, hogy az ügyfélalkalmazások POCO-objektumokat fejleszthessenek, és különböző verziókkal dolgozhassanak.  
> 
> 

A szakasz további része a Storage Client Library néhány olyan szolgáltatását ismerteti, amelyek megkönnyítik az ugyanabban a táblában lévő több entitástípussal való munkát.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Heterogén entitástípusok beolvasása
Ha a Storage Client Library-t használja, három lehetősége van több entitástípus használatára.  

Ha ismeri az entitás típusát, amely `RowKey` `PartitionKey` et adott és értékekkel tárolják, akkor megadhatja az entitás típusát az entitás beolvasásakor. Ezt az előző két példában látta, `EmployeeEntity`amelyek típusú entitásokat kérnek be: [Pontlekérdezés futtatása a Storage Client Library használatával,](#run-a-point-query-by-using-the-storage-client-library) és [több entitás lekérése a LINQ használatával.](#retrieve-multiple-entities-by-using-linq)  

A második lehetőség a `DynamicTableEntity` típus (tulajdonságtáska) használata a konkrét POCO entitástípus helyett. Ez a beállítás javíthatja a teljesítményt is, mivel nincs szükség az entitás .NET típusokba történő szerializálására és deszerializálására. A következő C# kód potenciálisan lekéri a különböző típusú entitások a `DynamicTableEntity` táblából, de az összes entitáspéldányként adja vissza. Ezután a `EntityType` tulajdonság segítségével határozza meg az egyes entitások típusát:  

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

Más tulajdonságok lekéréséhez az `TryGetValue` `DynamicTableEntity` osztály `Properties` tulajdonságán lévő metódust kell használnia.  

A harmadik lehetőség a `DynamicTableEntity` típus és `EntityResolver` a példány használatával történő kombinálás. Ez lehetővé teszi, hogy ugyanazon lekérdezésben több POCO-típusra is feloldódjon. Ebben a példában a `EntityResolver` `EntityType` delegált a tulajdonság segítségével különbséget tesz a lekérdezés által visszaadott két entitástípus között. A `Resolve` metódus `resolver` a delegált segítségével oldja fel `DynamicTableEntity` a példányokat példányok. `TableEntity`  

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

#### <a name="modify-heterogeneous-entity-types"></a>Heterogén entitástípusok módosítása
Nem kell tudnia, hogy milyen típusú entitás törölni, és mindig tudja, hogy milyen típusú egy entitás, amikor beszúrja. A típus sal `DynamicTableEntity` azonban frissítheti az entitást a típus ismerete nélkül, és POCO entitásosztály használata nélkül. A következő kódminta egyetlen entitást kér `EmployeeCount` be, és a frissítés előtt ellenőrzi, hogy a tulajdonság létezik-e.  

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

### <a name="control-access-with-shared-access-signatures"></a>Hozzáférés szabályozása megosztott hozzáférésű aláírásokkal
A megosztott hozzáférésű aláírás (SAS) jogkivonatokkal lehetővé teheti, hogy az ügyfélalkalmazások közvetlenül módosíthassák (és lekérdezhetjék) a táblaentitásokat anélkül, hogy közvetlenül kellene hitelesíteniük magukat a Table storage szolgáltatással. A SAS alkalmazásban való használatának általában három fő előnye van:  

* Nem kell terjesztenie a tárfiók kulcsát egy nem biztonságos platformra (például egy mobileszközre) annak érdekében, hogy az eszköz hozzáférhessen és módosíthassa az entitásokat a Table storage-ban.  
* Kiszervezheti a webes és a feldolgozói szerepkörök által az entitások kezelésében végzett munka egy részét. Az ügyféleszközökre, például végfelhasználói számítógépekre és mobileszközökre is kiszervezheti.  
* Az ügyfelekhez korlátozott és korlátozott számú engedélykészletet rendelhet (például írásvédett hozzáférést biztosít bizonyos erőforrásokhoz).  

A SAS-jogkivonatok table storage-szal való használatáról a [Megosztott hozzáférésű aláírások (SAS) használata című](../storage/common/storage-dotnet-shared-access-signature-part-1.md)témakörben talál további információt.  

Azonban továbbra is létre kell hoznia a SAS-jogkivonatokat, amelyek ügyfélalkalmazást adnak a Table storage entitásainak. Ezt olyan környezetben, amely biztonságos hozzáférést biztosít a tárfiók kulcsaihoz. Általában egy webes vagy feldolgozói szerepkört használ a SAS-jogkivonatok létrehozásához, és azokat az entitásokhoz hozzáférést igénylő ügyfélalkalmazásokhoz. Mivel továbbra is többletterhelés merül fel a SAS-jogkivonatok létrehozása és az ügyfelek számára történő kézbesítése során, érdemes megfontolnia, hogyan lehet a legjobban csökkenteni ezt a többletterhelést, különösen a nagy volumenű forgatókönyvekben.  

Lehetőség van egy SAS-jogkivonat létrehozásához, amely hozzáférést biztosít a tábla entitásainak egy részhalmazához. Alapértelmezés szerint egy sas-jogkivonatot hoz létre egy teljes táblához. De azt is megadhatja, hogy a SAS-jogkivonat `PartitionKey` hozzáférést biztosít-e `PartitionKey` egy `RowKey` értéktartományhoz, vagy egy és értékek tartományához. Előfordulhat, hogy a rendszer egyes felhasználói számára SAS-jogkivonatokat hoz létre, például hogy minden felhasználó SAS-jogkivonata csak a saját entitásaikhoz való hozzáférést engedélyezi a Table Storage-ban.  

### <a name="asynchronous-and-parallel-operations"></a>Aszinkron és párhuzamos műveletek
Feltéve, hogy a kérelmeket több partícióközött osztja el, aszinkron vagy párhuzamos lekérdezések használatával javíthatja az átviteli és az ügyfél válaszképességét.
Előfordulhat például, hogy két vagy több feldolgozói szerepkörpéldány párhuzamosan fér hozzá a táblákhoz. Rendelkezhet a partíciók adott készleteiért felelős egyéni feldolgozói szerepkörök, vagy egyszerűen csak több feldolgozói szerepkör-példányok, mindegyik képes elérni a partíciókat egy táblában.  

Egy ügyfélpéldányon belül javíthatja az átviteli kapacitást a tárolási műveletek aszinkron futtatásával. A tárolóügyfél-kódtár megkönnyíti az aszinkron lekérdezések és módosítások írását. Előfordulhat például, hogy a szinkron metódussal kezdi, amely lekéri a partíció összes entitását, ahogy az a következő C# kódban látható:  

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

Ezt a kódot egyszerűen módosíthatja úgy, hogy a lekérdezés aszinkron módon futjon, az alábbiak szerint:  

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

Ebben az aszinkron példában a következő módosításokat láthatja a szinkron verzióból:  

* A metódus aláírása `async` most már tartalmazza `Task` a módosítót, és egy példányt ad vissza.  
* Ahelyett, hogy `ExecuteSegmented` meghívja a metódust az `ExecuteSegmentedAsync` eredmények beolvasásához, a metódus most meghívja a metódust. A metódus `await` a módosító segítségével aszinkron módon olvassa be az eredményeket.  

Az ügyfélalkalmazás ezt a metódust többször is `department` meghívhatja, a paraméter különböző értékeivel. Minden lekérdezés külön szálon fut.  

Az `Execute` `TableQuery` osztályban nincs aszinkron verzió, mert a felület `IEnumerable` nem támogatja az aszinkron enumerálást.  

Az entitásokat aszinkron módon is beszúrhatja, frissítheti és törölheti. A következő C# példa egy egyszerű, szinkron módszert mutat be egy alkalmazottentitás beszúrására vagy cseréjére:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Ezt a kódot egyszerűen módosíthatja úgy, hogy a frissítés aszinkron módon futjon, az alábbiak szerint:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Ebben az aszinkron példában a következő módosításokat láthatja a szinkron verzióból:  

* A metódus aláírása `async` most már tartalmazza `Task` a módosítót, és egy példányt ad vissza.  
* Ahelyett, hogy `Execute` meghívja az entitás frissítésére `ExecuteAsync` irányuló metódust, a metódus most meghívja a metódust. A metódus `await` a módosító segítségével aszinkron módon olvassa be az eredményeket.  

Az ügyfélalkalmazás több aszinkron metódust is meghívhat, mint ez, és minden metódus meghívása külön szálon fut.  


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

