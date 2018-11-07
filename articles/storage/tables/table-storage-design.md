---
title: Tervezési méretezhető és nagytejesítményű táblákat az Azure table storage-ban. | Microsoft Docs
description: Tervezési méretezhető és nagytejesítményű táblákat az Azure table storage-ban.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: c5b18bce9d0cf78569d0c2fa02ad14c96ad09bd1
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51237774"
---
# <a name="design-scalable-and-performant-tables"></a>Méretezhető és nagy teljesítményű táblák tervezése

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Méretezhető és nagytejesítményű táblákat például a teljesítményt, méretezhetőséget és tényező figyelembe kell venni. Ha korábban létrehozott sémák a relációs adatbázisok, ezeket a szempontokat ismeri, de áll némileg hasonlít az Azure Table storage modell és a relációs modellek között, amelyek szintén fontos funkciókülönbségek vannak. Ezek a különbségek általában vezethet a különböző formában használhatók, amely counter-intuitive vagy valaki jól ismert, relációs adatbázisok nem megfelelő hely, még ha tervez, egy NoSQL kulcs-érték tárolót, például az Azure Table service számára jelentéssel bírnak. Számos, a Tervező különbségek megfelelően a tény, hogy a Table service célja, hogy a felhőléptékű alkalmazások támogatása több milliárd entitások (vagy a relációs adatbázis-terminológiában sorok) is tartalmazhat az adatok vagy támogatnia kell a nagy tranzakciós adatkészletek esetében kötetek. Ezért kell másképp gondolja hogyan tárolja az adatait, és a Table service működésének megismerése. Egy jól megtervezett NoSQL-adattár engedélyezheti a megoldás méretezése sokkal tovább és alacsonyabb költségek, mint egy megoldás, amely relációs adatbázist használ. Az útmutató az alábbi témakörök segítségével.  

## <a name="about-the-azure-table-service"></a>Az Azure Table service-ről
Ez a szakasz kiemeli a Table service legfontosabb funkcióit, amely különösen a teljesítményének és méretezhetőségének tervezése. Ha most ismerkedik az Azure Storage és a Table service, először olvassa el [a Microsoft Azure Storage bemutatása](../../storage/common/storage-introduction.md) és [Ismerkedés az Azure Table Storage .NET-tel](../../cosmos-db/table-storage-how-to-use-dotnet.md) Ez a cikk további részében elolvasása előtt . Bár a lépéseknek az ismertetése, ez az útmutató a Table Service, az Azure üzenetsor és a Blob szolgáltatás, és hogyan használhatja azokat a Table service-tartalmazza.  

Mi az a Table service? Ahogy a neve, a Table service rekordlisták táblázatos formátumú adatok tárolásához használja. Ismertetésében, az a táblázat minden egyes sorához egy entitást jelent, és az oszlopok tárolja az adott entitás tulajdonságait. Minden entitás rendelkezik egy kulcspárra és egy időbélyegző-oszlopot, amely a Table service használatával nyomon követheti az utolsó frissítés az entitás egyedi azonosításához. Az időbélyeg automatikusan alkalmazza-e, és manuálisan nem lehet felülírni történő küldés időbélyegzője legyen egy tetszőleges érték. A Table service az utolsó módosítás időbélyegző (LMT) használatával kezelheti az optimista egyidejűséget.  

> [!NOTE]
> A Table service REST API műveletek is visszaad egy **ETag** érték, amely azt a LMT származik. Ez a dokumentum felcserélhető használja a feltételeket, az ETag és LMT, mivel ugyanazokat az alapul szolgáló adatokat hivatkoznak.  
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
<th>Részleg neve</th>
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


Ezeket az adatokat az eddigi megjelenik egy relációs adatbázisban lévő táblából hasonló a fő különbségeket, a kötelező oszlopokat, és nem tárolhat több entitástípusok ugyanabban a táblában. Minden, a felhasználó által definiált tulajdonságok, mint például az **FirstName** vagy **életkor** adattípusú, például az egész számnak vagy karakterláncnak, egyszerűen, például egy oszlop egy relációs adatbázisban. Bár a ellentétben egy relációs adatbázisban, a Table service séma nélküli jellege azt jelenti, hogy egy tulajdonság nem kell minden entitáshoz ugyanolyan adattípusúak. Összetett adattípusok tárolni egyetlen tulajdonságát, például JSON vagy XML formátumú szerializált kell használnia. A table service, például a támogatott adattípusok, támogatott dátumtartományokat, elnevezési szabályok és méret megkötések kapcsolatos további információkért lásd: [a Table szolgáltatás adatmodelljének ismertetése](https://msdn.microsoft.com/library/azure/dd179338.aspx).

A választott **PartitionKey** és **RowKey** jó Táblatervezés alapvető fontosságú. A táblákban tárolt minden entitás egyedi kombinációját kell **PartitionKey** és **RowKey**. Csakúgy, mint a kulcsokat egy relációs adatbázis-táblában a **PartitionKey** és **RowKey** gyors look-ups engedélyezése egy fürtözött index indexelt értékek. Azonban a Table service nem hoz létre minden olyan másodlagos indexeket, így **PartitionKey** és **RowKey** azokat a csak indexelt tulajdonságokat. Az ismertetett mintáit [Table tervezési minták](table-storage-design-patterns.md) bemutatják, hogyan használhatja a nyilvánvaló korlátozás.  

Egy tábla egy vagy több partíciót kell megadni, és számos, a tervezési döntések itt maradunk a megfelelő választás **PartitionKey** és **RowKey** a megoldás optimalizálja. A megoldás állhat egyetlen tábla, amely minden, a részletezés partíciók entitásokat tartalmaz, de általában a megoldás több tábla van. Táblázatok segítséget logikailag rendszerezése az entitások, hozzáférés-vezérlési listák használatával az adatokhoz való hozzáférés kezeléséhez nyújt segítséget, és a egy teljes táblát egy egyetlen művelettel vethetők el.  

## <a name="table-partitions"></a>Táblapartíciók
A fiók neve, a táblázat neve, és **PartitionKey** együttesen azonosítja a partíción belül a storage szolgáltatás, amelyben a table service tárolja-e az entitás. Amellett, hogy az entitások a címzési séma része, a partíciók tranzakciók hatókör meghatározása (lásd: [tranzakciók](#entity-group-transactions) alább), és hogyan a table service méretezhető alapját. A partíciók további információkért lásd: [Azure Storage méretezhetőségi és Teljesítménycéljai](../../storage/common/storage-scalability-targets.md).  

A Table service szolgáltatásban az egyes csomópontok services egy vagy több fejezze be a partíciók és a szolgáltatás skálázható dinamikus terheléselosztás partíciók csomópontok között. Ha egy csomópont terhelés alatt van, a table service is *felosztása* partíciók számos különböző csomópontokon alakzatot a csomópont által kiszolgált; enyhül a forgalmat, ha a szolgáltatás is *egyesítési* csendes csomópontjáról partíció tartományok vissza az alakzatot egyetlen csomópont.  

További információ a belső részleteket a Table Service, és különösen a szolgáltatás kezeli a partíciók, hogyan: a tanulmány [a Microsoft Azure Storage: A magas rendelkezésre álló felhős Társzolgáltatás erős konzisztencia](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

## <a name="entity-group-transactions"></a>Tranzakciók
A Table service szolgáltatásban (EGTs) tranzakciók atomi frissítések végrehajtásához több entitásban a kizárólag beépített mechanizmus. EGTs néha is nevezzük *batch-tranzakciók*. EGTs csak működhet ugyanazon a partíción tárolt entitások (azaz megosztása ugyanazt a partíciókulcsot az adott táblában). Így több entitásban atomi tranzakciós viselkedés van szüksége, bármikor biztosítania kell, hogy ezeket az entitásokat is ugyanazon a partíción találhatók. Ez a gyakran több entitástípusok tárolja az ugyanabban a táblában (és a partíció), és nem használja több tábla különböző entitástípusok okát. Egyetlen EGT működhet, a legfeljebb 100 entitást.  Több egyidejű EGTs feldolgozásra elküldött, esetén fontos, hogy ezek EGTs nem fog működni az entitásokat, által közösen EGTs; Ellenkező esetben a feldolgozási késleltethető.

EGTs is, a tervezés kiértékelheti, hogy egy potenciális kompromisszum vezetnek be. Azt jelenti további partíciók használatával növeli a méretezhetőség, az alkalmazás, mivel az Azure rendelkezik a terheléselosztási kérések a csomópontok között további lehetőségek. Azonban további partíciók használatával lehet, hogy korlátozhatja, hogy a alkalmazásainak elemi tranzakciókat végrehajtania, és az adatok erős konzisztenciája. Emellett nincsenek adott teljesítménycélokat olyan partíció, amely előfordulhat, hogy korlátozza a tranzakciók egy csomópont esetén várható az átviteli szinten. Az Azure storage-fiókok és a table service skálázási célértékei kapcsolatos további információkért lásd: [Azure Storage méretezhetőségi és Teljesítménycéljai](../../storage/common/storage-scalability-targets.md).   

## <a name="capacity-considerations"></a>A kapacitás szempontok
A következő táblázat ismerteti az egyes érdemes figyelembe vennie, amikor tervezésekor tábla szolgáltatottszoftver-megoldás fő értékek:  

| Az Azure storage-fiók teljes kapacitás | 500 TB |
| --- | --- |
| Az Azure storage-fiók táblák száma |Csak a tárfiók kapacitásának által korlátozott |
| Egy táblát a partíciók száma |Csak a tárfiók kapacitásának által korlátozott |
| Egy partíció entitások száma |Csak a tárfiók kapacitásának által korlátozott |
| Egy egyéni entitás méretét |Legfeljebb 1 MB, amely legfeljebb 255 tulajdonságok (beleértve a **PartitionKey**, **rowkey tulajdonságok esetén**, és **időbélyeg**) |
| Méretét a **PartitionKey** |A karakterlánc-legfeljebb 1 KB méretű |
| Méretét a **rowkey tulajdonságok esetén** |A karakterlánc-legfeljebb 1 KB méretű |
| Egy Entitáscsoportot tranzakció mérete |Egy tranzakció legfeljebb 100 entitást tartalmazhat, és a hasznos 4 MB-nál kisebbnek kell lennie. Miután egy EGT csak is frissítheti egy entitás. |

További információt a [Table Service adatmodelljét ismertető](https://msdn.microsoft.com/library/azure/dd179338.aspx) témakörben talál.  

## <a name="cost-considerations"></a>Költségekkel kapcsolatos szempontok
A TABLE storage viszonylag alacsonyak, de a kiértékelés a Table service megoldásoktól részeként kell vennie a költségbecslések a kapacitás használati és a tranzakciók mennyiségét. Azonban sok esetben ahhoz, hogy a teljesítmény vagy a megoldás méretezhetőségét denormalizált vagy duplikált adatok tárolására egy érvényes megközelítés. Díjszabással kapcsolatos további információkért lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>További lépések

- [Táblázat kialakítási minták](table-storage-design-patterns.md)
- [Kapcsolatok modellezését](table-storage-design-modeling.md)
- [Lekérdezés tervezése](table-storage-design-for-query.md)
- [Táblák adatainak titkosítása](table-storage-design-encrypt-data.md)
- [Adatmódosítás tervezése](table-storage-design-for-modification.md)