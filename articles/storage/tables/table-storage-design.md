---
title: Méretezhető és teljesítményalapú táblákat tervezhet az Azure táblatárolójában. | Microsoft Docs
description: Méretezhető és teljesítményalapú táblákat tervezhet az Azure táblatárolójában.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 03/09/2020
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 8df639eea757c374554fa19e57c43cef79308e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255144"
---
# <a name="design-scalable-and-performant-tables"></a>Méretezhető és nagy teljesítményű táblák tervezése

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Méretezhető és teljesítménytáblák tervezéséhez figyelembe kell vennie olyan tényezőket, mint a teljesítmény, a méretezhetőség és a költségek. Ha korábban tervezett sémák relációs adatbázisok, ezek a szempontok ismerősek, de bár vannak hasonlóságok az Azure Table szolgáltatás tárolási modell és a relációs modellek között, vannak is fontos különbségek. Ezek a különbségek általában különböző tervekhez vezetnek, amelyek ellentétes intuitívnak vagy hibásnak tűnhetnek a relációs adatbázisokat ismerő személy számára, de van értelme, ha egy NoSQL kulcs/értéktároló, például az Azure Table szolgáltatás tervezésekor tervez. A tervezési különbségek közül sok azt tükrözi, hogy a Table szolgáltatás olyan felhőalapú alkalmazások at támogat, amelyek több milliárd entitást (vagy a relációs adatbázis terminológiájában sorokat) tartalmazhatnak, vagy olyan adatkészletek esetében, amelyeknek támogatniuk kell a magas tranzakciót Kötetek. Ezért másképp kell gondolkodnia az adatok tárolásáról, és meg kell értenie, hogyan működik a Table szolgáltatás. Egy jól megtervezett NoSQL adattár lehetővé teszi, hogy a megoldás méretezése sokkal tovább és alacsonyabb költséggel, mint egy megoldás, amely egy relációs adatbázis. Ez az útmutató segítséget nyújt ezekkel a témakörökkel kapcsolatban.  

## <a name="about-the-azure-table-service"></a>Az Azure Table szolgáltatásról
Ez a szakasz kiemeli a table szolgáltatás néhány kulcsfontosságú jellemzőjét, amelyek különösen fontosak a teljesítmény és a méretezhetőség tervezése szempontjából. Ha most ismerkedik az Azure Storage és a Table szolgáltatás, először olvassa el [a Bevezetés a Microsoft Azure Storage](../../storage/common/storage-introduction.md) és az Azure Table Storage [használatának megkezdése a .NET használata](../../cosmos-db/table-storage-how-to-use-dotnet.md) előtt a cikk többi részében. Bár ez az útmutató a table szolgáltatásra összpontosít, magában foglalja az Azure Queue és a Blob szolgáltatások megbeszélését, és hogyan használhatja őket a Table szolgáltatással.  

Mi az a Table szolgáltatás? Ahogy az a névtől elvárható, a Table szolgáltatás táblázatos formátumot használ az adatok tárolásához. A szabványos terminológiában a tábla minden sora egy entitást jelöl, és az oszlopok az entitás különböző tulajdonságait tárolják. Minden entitás rendelkezik egy kulcspárral, amely egyedileg azonosítja, és egy időbélyeg oszlop, amelyet a Table szolgáltatás használ az entitás utolsó frissítésének nyomon követéséhez. Az időbélyeg alkalmazása automatikusan történik, és az időbélyeg manuálisan nem írható felül tetszőleges értékkel. A Table szolgáltatás ezt az utolsó módosítású időbélyeget (LMT) használja az optimista egyidejűség kezelésére.  

> [!NOTE]
> A Table service REST API-műveletek is ad vissza egy **ETag** értéket, amely származik az LMT. Ez a dokumentum szinonimaként használja az ETag és az LMT kifejezéseket, mivel ugyanazokra az alapul szolgáló adatokra hivatkoznak.  
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


Ezek az adatok eddig hasonlónak tűnnek egy relációs adatbázis táblájához, ahol a legfontosabb különbségek a kötelező oszlopok, és több entitástípus tárolására is lehetővé teszik ugyanabban a táblában. Emellett a felhasználó által definiált tulajdonságok,például a **FirstName** vagy az **Age** is rendelkezik adattípussal, például egész számmal vagy karakterlánccal, ugyanúgy, mint egy relációs adatbázis oszlopa. Bár a relációs adatbázissal ellentétben a Table szolgáltatás séma nélküli jellege azt jelenti, hogy egy tulajdonságnak nem kell minden entitáson azonos adattípussal rendelkeznie. Ha összetett adattípusokat szeretne egyetlen tulajdonságban tárolni, szerializált formátumot kell használnia, például JSON vagy XML formátumot. A táblaszolgáltatásról, például a támogatott adattípusokról, a támogatott dátumtartományokról, az elnevezési szabályokról és a méretkorlátozásokról a [Table Service Data Model ismertetése című](https://msdn.microsoft.com/library/azure/dd179338.aspx)témakörben talál további információt.

A **partitionkey** és **rowkey** választása alapvető fontosságú a jó táblatervezéshez. A táblában tárolt minden entitásnak a PartitionKey és a **RowKey** egyedi kombinációjával kell **rendelkeznie.** A relációs adatbázistáblában lévő kulcsokhoz is a **Partíciókulcs** és a **RowKey** értékek indexelésével fürtözött indexet hoznak létre a gyors keresgések lehetővé tétele érdekében. A Table szolgáltatás azonban nem hoz létre másodlagos indexeket, így **a PartitionKey** és a **RowKey** az egyetlen indexelt tulajdonság. A Táblázat tervezési [mintáiban](table-storage-design-patterns.md) ismertetett minták némelyike azt szemlélteti, hogyan kerülheti meg ezt a látszólagos korlátozást.  

Egy tábla egy vagy több partíciót tartalmaz, és sok tervezési döntést hoz, amelyek a megoldás optimalizálásához megfelelő **PartitionKey** és **RowKey** kiválasztása körül lesznek. A megoldás állhat egyetlen tábla, amely tartalmazza az összes entitást partíciókba rendezve, de általában egy megoldás több tábla. A táblázatok segítségével logikusan rendszerezheti az entitásokat, kezelheti az adatokhoz való hozzáférést hozzáféréssel a hozzáférés-vezérlési listák használatával, és egyetlen tárolási művelettel eldobhat egy teljes táblát.  

## <a name="table-partitions"></a>Táblapartíciók
A fiók név, a táblanév és **a PartitionKey** együtt azonosítja a partíciót a storage szolgáltatáson belül, ahol a table service tárolja az entitást. Amellett, hogy az entitások címzési sémájának részét képezik, a partíciók meghatározzák a tranzakciók hatókörét (lásd alább [az Entitáscsoport-tranzakciókat),](#entity-group-transactions) és a table service méretezésének alapját képezik. A partíciókról a [Táblatárolás teljesítmény- és méretezhetőségi ellenőrzőlistája](storage-performance-checklist.md)című témakörben talál további információt.  

A Table szolgáltatásban egy egyes csomópont egy vagy több teljes partíciót, és a szolgáltatás skálázásdinamikus terheléselosztási partíciók csomópontok között dinamikusan terheléselosztási partíciók. Ha egy csomópont terhelés alatt van, a table service *feloszthatja* az adott csomópont által kiszolgált partíciók tartományát különböző csomópontokra; amikor a forgalom csökken, a szolgáltatás *egyesítheti* a partíció tartományok csendes csomópontok vissza egyetlen csomópontra.  

A Table szolgáltatás belső részleteiről és különösen arról, hogy a szolgáltatás hogyan kezeli a partíciókat, tekintse meg a [Microsoft Azure Storage: A highly available Cloud Storage Service with Strong Consistency](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)című tanulmányt.  

## <a name="entity-group-transactions"></a>Entitáscsoport tranzakciói
A Table szolgáltatásban az entitáscsoport-tranzakciók (EGTs) az egyetlen beépített mechanizmus több entitás közötti atomi frissítések végrehajtásához. Az EGT-ket néha *kötegelt tranzakcióknak*is nevezik . EGT-k csak akkor működhet az azonos partíción tárolt entitások (azaz ugyanazt a partíciókulcsot egy adott táblában). Így bármikor, amikor több entitás között szükséges atomi tranzakciós viselkedés, győződjön meg arról, hogy ezek az entitások ugyanabban a partícióban vannak. Ez gyakran oka annak, hogy több entitástípust kell ugyanabban a táblában (és partícióban) tartani, és nem több táblát használni különböző entitástípusokhoz. Egyetlen EGT legbénult 100 entitáson működhet.  Ha több egyidejű EGT-t küld be feldolgozásra, fontos biztosítani, hogy ezek az EGT-k ne működjenek olyan entitásokon, amelyek közösek az EGT-k között; ellenkező esetben a feldolgozás késleltethető.

Az EGT-k egy lehetséges kompromisszumot is bevezetnek, amelyet a tervezésben értékelhet. Ez azt, hogy több partíció használata növeli az alkalmazás méretezhetőségét, mivel az Azure-nak több lehetősége van a terheléselosztási kérelmek csomóponton keresztül. De több partíció használata korlátozhatja az alkalmazás képességét az atomi tranzakciók végrehajtására és az adatok erős konzisztenciájának fenntartására. Továbbá vannak olyan speciális méretezhetőségi célok egy partíció szintjén, amelyek korlátozhatják az egyetlen csomópontra várható tranzakciók átviteli képességét. Az Azure standard szintű tárfiókok méretezhetőségi céljairól további információt a [szabványos tárfiókok méretezhetőségi céljai című témakörben](../common/scalability-targets-standard-account.md)talál. A Table szolgáltatás méretezhetőségi céljairól a [Táblázattárolás méretezhetőségi és teljesítménycéljai](scalability-targets.md)című témakörben talál további információt.

## <a name="capacity-considerations"></a>Kapacitással kapcsolatos szempontok

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="cost-considerations"></a>Költségekkel kapcsolatos szempontok
A table storage viszonylag olcsó, de a Table service-megoldások kiértékelésének részeként költségbecsléseket kell megadnia mind a kapacitáskihasználtságra, mind a tranzakciók mennyiségére vonatkozóan. Azonban sok esetben a denormalizált vagy duplikált adatok tárolása a megoldás teljesítményének vagy méretezhetőségének javítása érdekében érvényes megközelítés. A díjszabásról az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/)című témakörben talál további információt.  

## <a name="next-steps"></a>További lépések

- [Táblázat tervezési mintái](table-storage-design-patterns.md)
- [Kapcsolatok modellezése](table-storage-design-modeling.md)
- [Tervezés lekérdezéshez](table-storage-design-for-query.md)
- [Táblaadatok titkosítása](table-storage-design-encrypt-data.md)
- [Tervezés adatmódosításhoz](table-storage-design-for-modification.md)
