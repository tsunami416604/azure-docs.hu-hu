---
title: A Tervező méretezhető és performant táblák Azure table storage-ban. | Microsoft Docs
description: A Tervező méretezhető és performant táblák Azure table storage-ban.
services: storage
documentationcenter: na
author: SnehaGunda
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: ba48283371ac099b162aeb3cbffd6127ccce1676
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660899"
---
# <a name="design-scalable-and-performant-tables"></a>Méretezhető és nagyteljesítményű táblák tervezése

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Tervezési méretezhető és performant táblák amelyeket érdemes figyelembe venni tényezőktől, például a teljesítmény, méretezhetőség és költség. Ha korábban létrehozott sémák a relációs adatbázisok, ezeket a szempontokat ismerős, de amíg vannak bizonyos az az Azure Table storage modell és a relációs modellek közötti Hasonlóságok, is fontos különbségek vannak. Ezek a különbségek általában a különböző kialakításokról, keresse meg a counter-intuitive vagy valaki ismeri a relációs adatbázisok nem megfelelő, de célszerű, például az Azure Table szolgáltatás egy NoSQL kulcs-érték tároló tervezésekor vezethet. A Tervező különbségek számos érdekében, hogy a Table szolgáltatás célja, hogy felhőméretű alkalmazások támogatását, amely tartalmazhat egy az entitások (vagy az relációs adatbázis-terminológiában sorok) az adatok, vagy olyan adatkészletekhez, amelyek magas tranzakció támogatnia kell kötetek. Ezért kell vennie eltérően, hogyan tárolja az adatait, és a Table szolgáltatás működésének megismerése. Tetszetős NoSQL-adattár engedélyezheti a megoldás jóval további és alacsonyabb költségekkel mint egy relációs adatbázist használó megoldás méretezése. Az útmutató az alábbi témakörök segítségével.  

## <a name="about-the-azure-table-service"></a>Az Azure Table szolgáltatással kapcsolatos
Ez a szakasz azt mutatja be néhány fő funkciója a Table szolgáltatás szempontjából különösen teljesítményének és méretezhetőségének tervezése. Ha most ismerkedik az Azure Storage és a Table szolgáltatás, elolvashatja [Microsoft Azure Storage bemutatása](../../storage/common/storage-introduction.md) és [Ismerkedés az Azure Table Storage használata a .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) további része a cikk elolvasása előtt . Bár a jelen útmutató elsősorban a Table szolgáltatás, az Azure üzenetsor és a Blob szolgáltatás, és hogyan használhatja őket a táblázat szolgáltatással is tartalmaz.  

Mi az a Table szolgáltatás? Előfordulhat, hogy a neve alapján várt, a Table szolgáltatás táblázatos formátumú adatok használ. A ismertetésében a tábla minden egyes sorára jelöli egy entitás, és az oszlopok tárolja, hogy az entitás tulajdonságait. Minden entitás rendelkezik egy kulcspárra van szüksége, és egy Timestamp típusú oszlop, amely a Table szolgáltatás segítségével nyomon követheti az az entitás utolsó frissítésének egyedi azonosításához. Az időbélyeg automatikusan alkalmazzák, és adjon meg egy tetszőleges értéket nem lehet felülírni a Timestamp típusú manuálisan. A Table szolgáltatás az utolsó módosításának időbélyegző (LMT) egyidejű hozzáférések optimista kezelésére használ.  

> [!NOTE]
> A Table szolgáltatás REST API-műveleteket is vissza egy **ETag** érték, amely azt a LMT származik. Ez a dokumentum azonos értelemben használja a feltételeket, ETag és LMT, mivel ugyanazokat az alapul szolgáló adatokat hivatkoznak.  
> 
> 

Az alábbi példában egy egyszerű Táblatervezés alkalmazott és részleg entitások tárolásához. Ez a kialakítás egyszerű számos az útmutató későbbi részében látható példája alapul.  

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
<th>Utónév</th>
<th>Vezetéknév</th>
<th>Kor</th>
<th>E-mail</th>
</tr>
<tr>
<td>Nincs</td>
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
<th>Utónév</th>
<th>Vezetéknév</th>
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
<th>Utónév</th>
<th>Vezetéknév</th>
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


Amennyiben ezek az adatok hasonlóan jelenik meg a egy relációs adatbázisban a legfőbb különbségek a kötelező oszlopok, és ugyanabban a táblában több entitástípusok tárolásának képessége. Minden, a felhasználó által definiált tulajdonságok, mint az **Keresztnév** vagy **kora** adattípusú, például az egész szám vagy karakterlánc, csak, például egy relációs adatbázisban oszlop. Bár eltérően egy relációs adatbázisban, a Table szolgáltatás séma nélküli jellege azt jelenti, hogy a tulajdonság nem szükséges minden entitáshoz ugyanolyan adattípusúak. Összetett adattípusú egy adott tulajdonságra vannak tárolva, például a JSON- vagy XML-szerializált formátum kell használnia. A table szolgáltatás például a támogatott adattípusok, támogatott dátumtartományok, elnevezési szabályok és mérete megkötések kapcsolatos további információkért lásd: [ismertetése a Table szolgáltatás adatmodell](http://msdn.microsoft.com/library/azure/dd179338.aspx).

A választott **PartitionKey** és **RowKey** jó Táblatervezés alapvető. Minden entitás egy táblázatban tárolja rendelkeznie kell egy egyedi kombinációja **PartitionKey** és **RowKey**. Csakúgy, mint a kulcsokat egy relációs adatbázis tábláinak a **PartitionKey** és **RowKey** értékek indexelt egy fürtözött index gyors look-ups engedélyezéséhez. Azonban a Table szolgáltatás nem hoz létre bármely másodlagos indexek, így **PartitionKey** és **RowKey** a csak az indexelt tulajdonságok vannak. A minták ismertetett némelyike [táblázat kialakítási minta](table-storage-design-patterns.md) bemutatják, hogyan oldható meg a nyilvánvaló korlátozás.  

Egy tábla egy vagy több partíció tartalmazza, és a tervezési döntéseit számos kell körül kiválasztása megfelelő **PartitionKey** és **RowKey** a megoldás optimalizálása érdekében. A megoldás, amely minden, a partíciók szervezve entitásokat tartalmaz egyetlen tábla állhat, de jellemzően a megoldás több táblát is rendelkezik. Táblázatok segítséget logikailag rendezheti az entitások, a hozzáférés-vezérlési listák segítségével adataihoz való hozzáférés kezeléséhez nyújt segítséget, és egyetlen tárolási művelettel teljes táblázat elvetné.  

## <a name="table-partitions"></a>Táblapartíciók
A fiók neve, a táblázat nevét, és **PartitionKey** együtt azonosíthatja a partíción belül a társzolgáltatás, ahol a table szolgáltatás tárolja az entitás. Amellett, hogy az entitások címzési séma része, a partíciók az egyes tranzakciókra vonatkozóan hatókör meghatározása (lásd: [entitás csoport tranzakciók](#entity-group-transactions) alább), és hogyan méretezze át a table szolgáltatás alapját. A partíciókon további információkért lásd: [Azure Storage méretezhetőségi és teljesítménycéloknak](../../storage/common/storage-scalability-targets.md).  

A Table szolgáltatásban, az egyes csomópontok szolgáltatások egy vagy több befejezéseként partíciók, és a szolgáltatás méretezik dinamikus terheléselosztás partíciók csomópontjai között. Egy csomópont terhelésnek van kitéve, ha a table szolgáltatás is *vágási* a partíciók száma a tartományon, más csomópontok csomópont által kiszolgált; enyhül forgalmat, ha a szolgáltatás képes *egyesítési* csendes csomópontjáról partíció tartományok biztonsági alakzatot egyetlen csomópont.  

További információ a belső részleteit a Table szolgáltatás, és ebben az esetben a szolgáltatás kezeli a partíciók, hogyan talál a [Microsoft Azure Storage: A magas rendelkezésre álló felhőalapú tárolási szolgáltatásba az erős konzisztencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

## <a name="entity-group-transactions"></a>Entitás csoport tranzakciók
A Table szolgáltatásban entitás csoport tranzakciók (EGTs) atomi frissítések végrehajtásához a több egység közötti csak beépített mechanizmus. EGTs néha is nevezzük *kötegelt tranzakciókat*. EGTs csak működhessenek partícióra tárolt entitásokat (Ez azt jelenti, hogy megoszthatja ugyanazzal a partíciókulccsal egy adott táblában). Így a több egység közötti atomi tranzakciós viselkedés van szüksége, bármikor győződjön meg arról, hogy ezeket az entitásokat tartalmazó partícióra vannak. Ez gyakran az több entitástípusok megőrzi a ugyanahhoz a táblához (és a partíció) található, és több tábla nem használ másik entitástípusok okát. Egyetlen EGT legfeljebb 100 entitást is működik.  Több egyidejű EGTs feldolgozásra elküldéséhez esetén fontos, hogy ezek EGTs működés nem lehetséges EGTs; által közösen használt entitások Ellenkező esetben a feldolgozási késleltethető.

EGTs is vezethet, hogy kipróbálhassa a Tervező egy potenciális kompromisszum. Ez azt jelenti, hogy további partíciók használata növeli a méretezhetőség, az alkalmazás, mert Azure további lehetőségek a terheléselosztás kérelmek csomópontjai között. De használatával több partíciót előfordulhat, hogy korlátozza az alkalmazás atomi tranzakciók elvégzéséhez, és erős konzisztenciát biztosít az adatok karbantartása. Emellett nincsenek egyedi méretezhetőségi célok javasoljuk, hogy előfordulhat, hogy korlátozza az egyetlen csomópont számíthat tranzakciók átviteli szintjén. Az Azure storage-fiókok és a table szolgáltatás vonatkozó méretezhetőségi célok kapcsolatos további információkért lásd: [Azure Storage méretezhetőségi és teljesítménycéloknak](../../storage/common/storage-scalability-targets.md).   

## <a name="capacity-considerations"></a>A kapacitás kapcsolatos szempontok
A következő táblázat ismerteti az egyes értékek érdemes figyelembe vennie, amikor tervezésekor a Table szolgáltatás megoldás:  

| Az Azure storage-fiók teljes kapacitás | 500 TB |
| --- | --- |
| Egy Azure storage-fiókot a táblák száma |Csak a tárfiók kapacitásának korlátozva |
| Egy tábla partíciók száma |Csak a tárfiók kapacitásának korlátozva |
| Partíció entitástartományának száma |Csak a tárfiók kapacitásának korlátozva |
| Az egyes entitás mérete |Legfeljebb 255 tulajdonságok legfeljebb 1 MB (beleértve a **PartitionKey**, **RowKey**, és **időbélyeg**) |
| A méret a **PartitionKey** |A karakterlánc legfeljebb 1 KB méretű |
| A méret a **RowKey** |A karakterlánc legfeljebb 1 KB méretű |
| Egy entitás csoport tranzakció mérete |Egy tranzakció legfeljebb 100 entitást tartalmazhat, és a tartalom 4 MB-nál kevesebb kell lennie. Egy EGT csak frissíthető entitás egyszer. |

További információkért lásd: [ismertetése a Table szolgáltatás adatmodell](http://msdn.microsoft.com/library/azure/dd179338.aspx).  

## <a name="cost-considerations"></a>Költség kapcsolatos szempontok
A TABLE storage költségei viszonylag alacsonyak, de a Table szolgáltatás megoldások próbaverziójának részeként tartalmaznia kell a kapacitás és a tranzakciók mennyisége költség becslése. Azonban számos forgatókönyvben denormalizált vagy ismétlődő adatok tárolása a teljesítmény vagy a megoldás méretezhetőség javítása érdekében nem egy érvényes megközelítés. Az árazással kapcsolatos további információkért lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>További lépések

- [Táblázat kialakítási minták](table-storage-design-patterns.md)
- [Modellezési kapcsolatok](table-storage-design-modeling.md)
- [Tervezési lekérdezése](table-storage-design-for-query.md)
- [Tábla adatok titkosítása](table-storage-design-encrypt-data.md)
- [Adatmódosítás kialakítása](table-storage-design-for-modification.md)