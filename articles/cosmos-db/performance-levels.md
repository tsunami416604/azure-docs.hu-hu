---
title: Kivont Azure Cosmos DB teljesítményszintek
description: Ismerje meg az S1, S2 és S3 teljesítményszintek korábban elérhető az Azure Cosmos DB-ben.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.openlocfilehash: 06fa98ae4acc2252d8866858ed0e2194ed84ff79
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034386"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Az S1, S2 és S3 teljesítményszintek kivonása

> [!IMPORTANT] 
> A cikkben tárgyalt S1, S2 és S3 teljesítményszintek van vezetve, és már nem érhetők el új Azure Cosmos DB-fiókok esetében.
>

Ez a cikk az S1, S2 és S3 teljesítményszintek nyújt áttekintést, és bemutatja, hogyan használja ezeket a teljesítményszinteket gyűjtemények lehet egyetlen áttelepített particionált gyűjtemények. Ez a cikk elolvasása után is elérheti az alábbi kérdések megválaszolásához:

- [Miért vannak az S1, S2 és S3 teljesítményszintek vannak vezetve?](#why-retired)
- [Hogyan tegye egypartíciós gyűjtemények és a particionált gyűjtemények hasonlítsa össze az S1, S2 és S3 teljesítményszintek?](#compare)
- [Mit kell tennem saját adatokhoz való zavartalan hozzáférés biztosítására?](#uninterrupted-access)
- [Hogyan változik a saját gyűjteményembe az áttelepítés után?](#collection-change)
- [Hogyan változik a történik a számlázás a szeretnék az egypartíciós gyűjteményeket használok áttelepítése után?](#billing-change)
- [Mi történik, ha több mint 10 GB tárhelyet kell?](#more-storage-needed)
- [Módosítható között az S1, S2 és S3 teljesítményszintek megelőzően a tervezett áttelepítés?](#change-before)
- [Hogyan migrálhatom az S1, S2 és S3 teljesítményszintű önálló egypartíciós gyűjtemények?](#migrate-diy)
- [Hogyan vagyok érintett a Ha nagyvállalati szerződéssel rendelkező ügyfelek vagyok?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Miért vannak az S1, S2 és S3 szintű teljesítmény szintjeit kivezetjük?

Az S1, S2 és S3 teljesítményszintek nem kínál a szabványos Azure Cosmos DB-ajánlat biztosít rugalmasságot. Az átviteli sebesség és a tárolási kapacitást az S1, S2 és S3 teljesítményszintek, előre beállított, és nem ajánlja fel rugalmasság. Az Azure Cosmos DB most kínál a testreszabása az átviteli sebesség és a tárolás, felkínálva sokkal nagyobb rugalmasságot biztosít arra, hogy az igényeinek megfelelően méretezhető.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Hogyan tegye egypartíciós gyűjtemények és a particionált gyűjtemények hasonlítsa össze az S1, S2 és S3 teljesítményszintek?

Az alábbi táblázat összehasonlítja az adatátviteli és tárolási lehetőségek egypartíciós gyűjteményeket, particionált gyűjteményeket, és az S1, S2 és S3 teljesítményszintek. Íme egy példa az USA keleti régiója 2 régióban:

|   |A particionált gyűjtemény|Különálló partíciós gyűjteménybe|S1|S2|S3|
|---|---|---|---|---|---|
|Maximális átviteli kapacitás|Korlátlan|10 ezer Kérelemegység/s|250 RU/s|1 ezer Kérelemegység/s|2.5 ezer Kérelemegység/s|
|Minimum átviteli kapacitás|2.5 ezer Kérelemegység/s|400 Kérelemegység/s|250 RU/s|1 ezer Kérelemegység/s|2.5 ezer Kérelemegység/s|
|Maximális tárhely|Korlátlan|10 GB|10 GB|10 GB|10 GB|
|Ár (havonta)|Átviteli sebesség: $6 / 100 RU/s<br><br>Storage: $ 0,25/GB|Átviteli sebesség: $6 / 100 RU/s<br><br>Storage: $ 0,25/GB|25 USD|50 USD|100 USD|

Ön nagyvállalati szerződéssel rendelkező ügyfelek? Ha igen, tekintse meg a [hogyan vagyok I érint, ha nagyvállalati szerződéssel rendelkező ügyfelek vagyok?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Mit kell tennem saját adatokhoz való zavartalan hozzáférés biztosítására?

Ha egy S1, S2 vagy S3 szintű gyűjtemény, át kell telepítenie a gyűjtemény egyetlen különálló partíciós gyűjteménybe való programozott módon [a .NET SDK-val](#migrate-diy). 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Hogyan változik a saját gyűjteményembe az áttelepítés után?

Ha egy S1 szintű gyűjteményt, áttelepítheti azokat egyetlen különálló partíciós gyűjteménybe 400 RU/s átviteli sebességgel. 400 Kérelemegység/s a legalacsonyabb átviteli sebesség érhető el az egypartíciós gyűjteményeket. Azonban költsége 400 RU/s az egyetlen különálló partíciós gyűjteménybe körülbelül azonos módon, az S1 szintű gyűjteményt és 250 RU/s – volt és ezért nem fizetnie az extra 150 RU/s elérhető.

Ha az S2-gyűjteményt, áttelepítheti azokat egy különálló partíciós gyűjteménybe 1 ezer Kérelemegység/s. Az átviteli sebesség szinten jelenik meg nem változik.

Ha egy S3 szintű gyűjtemény, áttelepítheti azokat egy különálló partíciós gyűjteménybe 2,5 ezer Kérelemegység/s. Az átviteli sebesség szinten jelenik meg nem változik.

Az egyes ezekben az esetekben miután áttelepítette a gyűjtemény lesz az átviteli sebesség szinten testreszabásához, vagy felfelé és lefelé közel valós idejű elérést biztosíthat a felhasználók igény szerint skálázza fel. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Hogyan változik a történik a számlázás a szeretnék az egypartíciós gyűjtemények áttelepítése után?

Feltételezve, hogy 10 S1 szintű gyűjtemények, 1 GB-nyi mindegyik esetében az USA keleti régiójában lévő tároló rendelkezik, és ezek 10 S1 szintű gyűjteményt telepít át 10 egypartíciós gyűjtemények 400 RU/s (a minimális szint). A számla módon fog kinézni, ha egy teljes hónapig tárolja a 10 egypartíciós gyűjtemények:

![Hogyan viszonyul a 10 gyűjtemények díjszabása S1 10 gyűjtemények használatával egyetlen különálló partíciós gyűjteménybe díjszabása](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>Mi történik, ha több mint 10 GB tárhelyet kell?

E rendelkezik az S1, S2 vagy S3 teljesítményszintű gyűjtemény, vagy egyetlen különálló partíciós gyűjteménybe, amelyek mindegyike rendelkezik, 10 GB tárhelyet érhető el, az Azure Cosmos DB adatáttelepítés eszközzel áttelepíteni az adatokat egy particionált gyűjteménybe, az gyakorlatilag korlátlan tárolási kapacitás Egy particionált gyűjteménybe előnyeivel kapcsolatos információk: [particionálás és skálázás az Azure Cosmos DB](sql-api-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Módosítható között az S1, S2 és S3 teljesítményszintek megelőzően a tervezett áttelepítés?

S1, S2 és S3 teljesítményű csak meglévő fiókok is módosítható, és módosítsa a szolgáltatói teljesítményszintek programozott módon [a .NET SDK-val](#migrate-diy). Ha egyetlen különálló partíciós gyűjteménybe módosítja az S1, az S3 vagy az S3, az S1, S2 vagy S3 teljesítményszintek nem lehet visszatérni.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Hogyan migrálhatom az S1, S2 és S3 teljesítményszintű önálló egypartíciós gyűjtemények?

Áttelepítheti az S1, S2 és S3 teljesítményszintek az egypartíciós gyűjtemények programozott módon [a .NET SDK-val](#migrate-diy). Ezt megteheti a saját maga számára, hogy a rugalmas átviteli sebesség érhető el az egypartíciós gyűjtemények beállításokat a tervezett áttelepítés előtt.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>A .NET SDK-val egypartíciós gyűjtemények áttelepítése

Ez a szakasz csak ismertet a naplógyűjtési teljesítmény módosítása szintű használatával a [SQL .NET API](sql-api-sdk-dotnet.md), azonban a folyamat hasonlít a más SDK-Ink.

Itt láthat egy kódrészletet, az a gyűjtemény kapacitásának módosítása a következőre összesen 5 000 kérelemegység / s:
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

Látogasson el [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) további példákat és további információ az ajánlat módszerek:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Hogyan vagyok érintett a Ha nagyvállalati szerződéssel rendelkező ügyfelek vagyok?

Nagyvállalati szerződéssel rendelkező ügyfelek aktuális szerződésük lejáratáig védett díja lesz.

## <a name="next-steps"></a>További lépések
Díjszabás és adatkezelési Azure Cosmos DB-vel kapcsolatos további tudnivalókért ezekben a forrásanyagokban:

1.  [Adatparticionálás a Cosmos DB](sql-api-partition-data.md). Egypartíciós tárolók és a particionált tárolók, valamint tippekkel szolgál az zökkenőmentesen skálázható particionálási stratégia megvalósításához közötti különbségek megértése.
2.  [A cosmos DB díjszabása](https://azure.microsoft.com/pricing/details/cosmos-db/). Ismerje meg az átviteli sebesség kiépítésének és felhasználásának tárolási költsége.
3.  [Kérelemegységek](request-units.md). Ismerje meg, az átviteli sebesség eltérő művelet típusok, például olvasási, írási, lekérdezés felhasználását.
