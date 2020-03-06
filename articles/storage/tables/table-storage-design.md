---
title: Méretezhető és teljesítményű táblák tervezése az Azure Table Storage-ban. | Microsoft Docs
description: Méretezhető és teljesítményű táblák tervezése az Azure Table Storage-ban.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 95272956da4567ec21e1c4603b88472e45373a39
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387133"
---
# <a name="design-scalable-and-performant-tables"></a>Méretezhető és nagy teljesítményű táblák tervezése

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

A skálázható és az elvégezhető táblázatok kialakításához olyan tényezőket kell figyelembe venni, mint a teljesítmény, a méretezhetőség és a költséghatékonyság. Ha korábban már tervezett sémákat a kapcsolati adatbázisokhoz, ezek a szempontok jól ismertek, de bár vannak hasonlóságok az Azure Table service Storage-modell és a kapcsolati modellek között, szintén fontos különbségek vannak. Ezek a különbségek jellemzően olyan különböző kialakításokhoz vezetnek, amelyek a viszonyítási adatbázisok megismerése érdekében hasznosak vagy helytelenek lehetnek, de érdemes lehet a NoSQL kulcs/érték tárolót, például az Azure Table service-t tervezni. A tervezési eltérések jelentős része annak a ténynek felel meg, hogy a Table service olyan felhőalapú alkalmazások támogatására lett tervezve, amelyek több milliárd entitást (vagy a kapcsolódó adatbázis-terminológia sorait) tartalmazhatnak, illetve olyan adatkészleteket, amelyeknek támogatniuk kell a nagy tranzakciót. kötetek. Ezért másképp kell meggondolni, hogyan tárolja az adatait, és hogyan tudja értelmezni a Table service működését. A jól megtervezett NoSQL-adattár lehetővé teszi, hogy a megoldás sokkal nagyobb mértékben és olcsóbban méretezhető legyen, mint a viszonyítási adatbázist használó megoldás. Az útmutató az alábbi témakörök segítségével.  

## <a name="about-the-azure-table-service"></a>Az Azure Table service-ről
Ez a szakasz kiemeli a Table service legfontosabb funkcióit, amely különösen a teljesítményének és méretezhetőségének tervezése. Ha még nem ismeri az Azure Storage-t és a Table servicet, először olvassa el a [Microsoft Azure Storage](../../storage/common/storage-introduction.md) és az [Azure Table Storage használatának első lépéseit](../../cosmos-db/table-storage-how-to-use-dotnet.md) , mielőtt beolvassa a cikk hátralévő részét. Bár az útmutató témája a Table service, az Azure üzenetsor és a blob Services megvitatását, valamint azt, hogy miként használhatja őket a Table service.  

Mi az a Table service? Ahogy a neve, a Table service rekordlisták táblázatos formátumú adatok tárolásához használja. Ismertetésében, az a táblázat minden egyes sorához egy entitást jelent, és az oszlopok tárolja az adott entitás tulajdonságait. Minden entitás rendelkezik egy pár kulccsal az egyedi azonosításhoz, valamint egy időbélyeg-oszlophoz, amelyet a Table service az entitás utolsó frissítésének nyomon követéséhez használ. A rendszer automatikusan alkalmazza az időbélyeget, és nem tudja manuálisan felülírni az időbélyeget tetszőleges értékkel. A Table service az utolsó módosítás időbélyegző (LMT) használatával kezelheti az optimista egyidejűséget.  

> [!NOTE]
> A Table service REST API műveletek egy **ETAG** értéket is visszaadnak, amelyet az LMT-ből származtatnak. Ez a dokumentum a ETag és az LMT által megváltoztathatatlan kifejezéseket használja, mivel ugyanazok az alapul szolgáló adatokat használják.  
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


Eddig ezek az adathalmazok a relációs adatbázisban lévő táblákhoz hasonlóan jelennek meg, amelyek a kötelező oszlopok fő különbségei, valamint a több entitás típusának egyazon táblában való tárolásának lehetősége. Emellett a felhasználó által definiált tulajdonságok (például a **FirstName** vagy a **Age** ) adattípusa (például Integer vagy string) egy, a viszonyítási adatbázisban lévő oszlophoz hasonlóan. Bár a ellentétben egy relációs adatbázisban, a Table service séma nélküli jellege azt jelenti, hogy egy tulajdonság nem kell minden entitáshoz ugyanolyan adattípusúak. Összetett adattípusok tárolni egyetlen tulajdonságát, például JSON vagy XML formátumú szerializált kell használnia. További információ a Table Service-ről, például a támogatott adattípusokról, a támogatott dátumtartomány, az elnevezési szabályok és a méretek megkötéséről: [a Table Service adatmodell ismertetése](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Az Ön által választott **PartitionKey** és **RowKey** a jó tábla kialakításához elengedhetetlen. A táblában tárolt összes entitásnak a **PartitionKey** és a **RowKey**egyedi kombinációjának kell lennie. Mint a **PartitionKey** és a **RowKey** értékek, mint a kulcsok egy kapcsolódó adatbázis-táblában, a rendszer indexeli az indexeket, és létrehoz egy fürtözött indexet a gyors keresések lehetővé tételéhez. A Table service azonban nem hoz létre másodlagos indexeket, így a **PartitionKey** és a **RowKey** az egyetlen indexelt tulajdonság. A [táblázatos tervezési mintákban](table-storage-design-patterns.md) leírt minták némelyike szemlélteti, hogy miként lehet megkerülni ennek a látszólagos korlátozásnak a körét.  

Egy tábla egy vagy több partícióból áll, és a tervezési döntések többsége egy megfelelő **PartitionKey** és **RowKey** kiválasztásával segíti a megoldás optimalizálását. Egy megoldás egy olyan táblából állhat, amely az összes olyan entitást tartalmazza, amely partíciókat rendez, de általában egy megoldás több táblával rendelkezik. Táblázatok segítséget logikailag rendszerezése az entitások, hozzáférés-vezérlési listák használatával az adatokhoz való hozzáférés kezeléséhez nyújt segítséget, és a egy teljes táblát egy egyetlen művelettel vethetők el.  

## <a name="table-partitions"></a>Táblapartíciók
A fiók neve, a tábla neve és a **PartitionKey** együtt azonosítja azt a tárolási szolgáltatáson belüli partíciót, amelyben a Table szolgáltatás tárolja az entitást. Az entitások címzési sémájának részeként a partíciók határozzák meg a tranzakciók hatókörét (lásd az alábbi [Entity Transactions-tranzakciókat](#entity-group-transactions) ), és a Table Service skálázásának alapjait alkotják. További információ a partíciókon: a [Table Storage teljesítmény-és méretezhetőségi ellenőrzőlistája](storage-performance-checklist.md).  

A Table service egy vagy több teljes partíciót, a szolgáltatás pedig a csomópontok közötti dinamikusan terheléselosztást végez. Ha egy csomópont terhelés alatt van, a Table szolgáltatás *feloszthatja* az adott csomópont által kiszolgált partíciók tartományát különböző csomópontokra. Ha a forgalom alá esik, a szolgáltatás *egyesítheti* a partíciók tartományait a csendes csomópontokból egyetlen csomópontra.  

További információ a Table service belső adatairól, valamint arról, hogy a szolgáltatás hogyan kezelje a partíciókat, a papír [Microsoft Azure Storage: magas rendelkezésre állású felhőalapú tárolási szolgáltatás erős konzisztencia mellett](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

## <a name="entity-group-transactions"></a>Tranzakciók
A Table service szolgáltatásban (EGTs) tranzakciók atomi frissítések végrehajtásához több entitásban a kizárólag beépített mechanizmus. A EGTs néha *Batch-tranzakcióknak*is nevezzük. A EGTs csak ugyanabban a partícióban tárolt entitásokban működhetnek (azaz ugyanazon a partíciós kulccsal osztoznak egy adott táblában). Így bármikor megkövetelheti, hogy az atomi tranzakciós viselkedés több entitásban is legyen, gondoskodnia kell arról, hogy ezek az entitások ugyanabban a partícióban legyenek. Ez a gyakran több entitástípusok tárolja az ugyanabban a táblában (és a partíció), és nem használja több tábla különböző entitástípusok okát. Egyetlen EGT működhet, a legfeljebb 100 entitást.  Ha több párhuzamos EGTs küld a feldolgozáshoz, akkor fontos, hogy a EGTs ne működjenek olyan entitásokon, amelyek a EGTs-ben közösek. Ellenkező esetben a feldolgozás késleltethető lehet.

A EGTs emellett potenciális kompromisszumot is bevezet, hogy kiértékelje a kialakítását. Ez a több partíció használata növeli az alkalmazás méretezhetőségét, mivel az Azure több lehetőséget kínál a különböző csomópontokon belüli terheléselosztási kérelmekre. A további partíciók használata azonban korlátozhatja, hogy az alkalmazás képes legyen atomi tranzakciókat végrehajtani, és erős konzisztenciát fenntartani az adatokhoz. Emellett vannak olyan méretezhetőségi célok is a partíció szintjén, amelyek korlátozhatják az egyetlen csomópontra várható tranzakciók átviteli sebességét. Az Azure standard Storage-fiókok méretezhetőségi céljaival kapcsolatos további információkért lásd [a standard szintű Storage-fiókok méretezhetőségi céljait](../common/scalability-targets-standard-account.md)ismertető témakört. A Table service skálázhatósági céljaival kapcsolatos további információkért lásd: a [táblázatos tárolás skálázhatósági és teljesítményi céljai](scalability-targets.md).

## <a name="capacity-considerations"></a>A kapacitás szempontok
Az alábbi táblázat néhány fontos értéket ismertet a Table service megoldás tervezésekor:  

| Az Azure storage-fiók teljes kapacitás | 500 TB |
| --- | --- |
| Az Azure storage-fiók táblák száma |Csak a tárfiók kapacitásának által korlátozott |
| Egy táblát a partíciók száma |Csak a tárfiók kapacitásának által korlátozott |
| Egy partíció entitások száma |Csak a tárfiók kapacitásának által korlátozott |
| Egy egyéni entitás méretét |Legfeljebb 1 MB, legfeljebb 255 tulajdonsággal (beleértve a **PartitionKey**, a **RowKey**és az **időbélyeget**) |
| A **PartitionKey** mérete |A karakterlánc-legfeljebb 1 KB méretű |
| A **RowKey** mérete |A karakterlánc-legfeljebb 1 KB méretű |
| Egy Entitáscsoportot tranzakció mérete |Egy tranzakció legfeljebb 100 entitást tartalmazhat, és a hasznos 4 MB-nál kisebbnek kell lennie. Miután egy EGT csak is frissítheti egy entitás. |

További információt a [Table Service adatmodelljét ismertető](https://msdn.microsoft.com/library/azure/dd179338.aspx) témakörben talál.  

## <a name="cost-considerations"></a>Költségekkel kapcsolatos szempontok
A Table Storage szolgáltatás viszonylag olcsó, azonban a kapacitás kihasználtsága és a tranzakciók mennyisége a Table service megoldások kiértékelésének részeként is szerepelnie kell. Számos esetben azonban a denormalizált vagy ismétlődő adatok tárolása a megoldás teljesítményének és méretezhetőségének javítása érdekében érvényes megközelítés. A díjszabással kapcsolatos további információkért lásd: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Következő lépések

- [Tábla kialakítási mintái](table-storage-design-patterns.md)
- [Modellezési kapcsolatok](table-storage-design-modeling.md)
- [Lekérdezés tervezése](table-storage-design-for-query.md)
- [A tábla adatai titkosítva](table-storage-design-encrypt-data.md)
- [Adatmódosítási terv](table-storage-design-for-modification.md)
