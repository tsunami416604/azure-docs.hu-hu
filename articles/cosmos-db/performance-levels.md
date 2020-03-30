---
title: Az Azure Cosmos DB nyugalmazott teljesítményszintjei
description: Ismerje meg az S1, S2 és S3 teljesítményszinteket, amelyek korábban elérhetők az Azure Cosmos DB-ben.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.openlocfilehash: 40735f91e2ca58cc42f723c7993686d92f0e5ff0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623333"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Az S1, S2 és S3 teljesítményszintek kivonása

> [!IMPORTANT] 
> Az s1, S2 és S3 teljesítményszintek ebben a cikkben tárgyalt kivonása, és már nem érhető el az új Azure Cosmos DB-fiókok.
>

Ez a cikk áttekintést nyújt az S1, S2 és S3 teljesítményszintekről, és bemutatja, hogy az ezeket a teljesítményszinteket használó gyűjtemények hogyan telepíthetők át egyetlen particionált gyűjteményekbe. A cikk elolvasása után a következő kérdésekre válaszolhat:

- [Miért vannak az S1, S2 és S3 teljesítményszintek kivonása?](#why-retired)
- [Hogyan egypartíciós gyűjtemények és particionált gyűjtemények összehasonlítani az S1, S2, S3 teljesítményszintek?](#compare)
- [Mit kell tennem az adataimhoz való folyamatos hozzáférés biztosítása érdekében?](#uninterrupted-access)
- [Hogyan változik a gyűjteményem az áttelepítés után?](#collection-change)
- [Hogyan változik a számlázás, miután áttelepítettek egypartíciós gyűjteményekbe?](#billing-change)
- [Mi a teendő, ha 20 GB-nál több tárhelyre van szükségem?](#more-storage-needed)
- [Módosíthatom az S1, S2 és S3 teljesítményszinteket a tervezett áttelepítés előtt?](#change-before)
- [Hogyan telepíthetek át az S1, S2, S3 teljesítményszintekről egypartíciós gyűjteményekbe saját?](#migrate-diy)
- [Milyen hatással van rám, ha Nagy- és Nagy-A ügyfél vagyok?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Miért vannak az S1, S2 és S3 teljesítményszintek kivonása?

Az S1, S2 és S3 teljesítményszintek nem biztosítják a szabványos Azure Cosmos DB-ajánlat által nyújtott rugalmasságot. Az S1, S2, S3 teljesítményszintek esetén mind az átviteli teljesítmény, mind a tárolási kapacitás előre be van állítva, és nem kínált rugalmasságot. Az Azure Cosmos DB mostantól lehetővé teszi az átviteli és tárolási eszközök testreszabását, így sokkal nagyobb rugalmasságot biztosít az igények változásával párhuzamosan a méretezésben.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Hogyan egypartíciós gyűjtemények és particionált gyűjtemények összehasonlítani az S1, S2, S3 teljesítményszintek?

Az alábbi táblázat összehasonlítja az egypartíciós gyűjtemények, particionált gyűjtemények és S1, S2, S3 teljesítményszinteken elérhető átviteli és tárolási lehetőségeket. Íme egy példa az USA Keleti Régió régiószámára:

|   |Particionált gyűjtemény|Egypartíciós gyűjtemény|S1|S2|S3|
|---|---|---|---|---|---|
|Maximális átviteli sebesség|Korlátlan|10 KRu/s|250 RU/s|1 K RU/s|2,5 K RU/s|
|Minimális átviteli|2,5 K RU/s|400 RU/s|250 RU/s|1 K RU/s|2,5 K RU/s|
|Maximális tárolás|Korlátlan|20 GB|20 GB|20 GB|20 GB|
|Ár (havi)|Átviteli: $6 / 100 RU/s<br><br>Tárhely: $0.25/GB|Átviteli: $6 / 100 RU/s<br><br>Tárhely: $0.25/GB|$25 USD|$50 USD|$100 USD|

Ön az EA ügyfele? Ha igen, lásd: [Hogyan érintett, ha Nagy- és Nagy- és Nagy- és Nagy- és Nagy- és Nagy- és Nagy- és Nagy- és Nagy- és Szerződési Vendég vagyok?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Mit kell tennem az adataimhoz való folyamatos hozzáférés biztosítása érdekében?

Ha S1, S2 vagy S3 gyűjteményt használ, a [.NET SDK használatával](#migrate-diy)programozott módon át kell telepítenie a gyűjteményt egyetlen partíciógyűjteménybe. 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Hogyan változik a gyűjteményem az áttelepítés után?

Ha rendelkezik egy S1-gyűjtemény, áttelepítheti őket egy partíciógyűjtemény 400 RU/s átviteli. 400 RU/s a legalacsonyabb átviteli egypartíciós gyűjtemények érhető el. Azonban a 400 RU/s költsége egy partíciógyűjteményben körülbelül megegyezik azzal, amit az S1-gyűjteményével és 250 RU/s-ával fizetett – így nem fizet az Ön rendelkezésére álló további 150 RU/s-ért.

Ha rendelkezik egy S2-gyűjtemény, áttelepítheti őket egy partíciógyűjtemény 1 K RU/s. Nem fog látni változást az átviteli szint.

Ha rendelkezik egy S3-gyűjtemény, áttelepítheti őket egy partíciógyűjtemény 2,5 K RU/s. Nem fog látni változást az átviteli szint.

Ezekben az esetekben a gyűjtemény áttelepítése után testre szabhatja az átviteli szintet, vagy szükség szerint fel- és leskálázhatja, hogy alacsony késleltetésű hozzáférést biztosítson a felhasználóknak. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Hogyan változik a számlázás, miután áttelepítettem az egypartíciós gyűjtemények?

Feltételezve, hogy 10 S1 gyűjtemények, 1 GB-os tárhely minden, az USA keleti régiójában, és áttelepíti ezeket a 10 S1 gyűjtemények 10 egypartíciós gyűjtemények 400 RU/s (a minimális szint). A számla a következőképpen fog kinézni, ha a 10 egypartíciós gyűjteményt egy teljes hónapig megtartja:

![Hogyan viszonyul az S1 10 gyűjteményhez egy egyetlen partíciógyűjtemény díjszabásával 10 gyűjteményhez?](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-20-gb-of-storage"></a>Mi a teendő, ha 20 GB-nál több tárhelyre van szükségem?

Akár S1, S2 vagy S3 teljesítményszinttel rendelkező gyűjteményt, akár egyetlen partíciógyűjteményt rendelkezik, amelyek mindegyike 20 GB tárhellyel rendelkezik, az Azure Cosmos DB adatáttelepítési eszközzel áttelepítheti az adatokat egy gyakorlatilag particionált gyűjteménybe korlátlan tárhely. A particionált gyűjtemény előnyeiről az [Azure Cosmos DB particionálása és méretezése című témakörben](sql-api-partition-data.md)talál. 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Módosíthatom az S1, S2 és S3 teljesítményszinteket a tervezett áttelepítés előtt?

A [.NET SDK használatával](#migrate-diy)csak az S1, S2 és S3 teljesítményű meglévő fiókok módosíthatók, és a teljesítményszint-szintek programozott módon módosíthatók. Ha S1, S3 vagy S3-ról egyetlen partíciógyűjteményre vált, nem térhet vissza az S1, S2 vagy S3 teljesítményszintekre.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Hogyan telepíthetek át az S1, S2, S3 teljesítményszintekről egypartíciós gyűjteményekbe saját?

Az S1, S2 és S3 teljesítményszintekről programozott módon áttelepíthető [egypartíciós gyűjteményekbe a .NET SDK használatával.](#migrate-diy) Ezt megteheti a tervezett áttelepítés előtt, hogy kihasználhassa az egypartíciós gyűjtemények rugalmas átviteli lehetőségeinek előnyeit.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Áttelepítés egypartíciós gyűjteményekbe a .NET SDK használatával

Ez a szakasz csak a gyűjtemény teljesítményszintjének az [SQL .NET API](sql-api-sdk-dotnet.md)használatával történő módosítására vonatkozik, de a folyamat hasonló a többi SDK-hoz.

Az alábbiakban egy kódrészletet olvashat a gyűjtemény átviteli fokának másodpercenként 5000 kérelemegységre történő módosításához:
    
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

Látogasson el az [MSDN oldalra,](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) ahol további példákat tekinthet meg, és többet szeretne megtudni ajánlati módszereinkről:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Milyen hatással van rám, ha Nagy- és Nagy-A ügyfél vagyok?

Az EA ügyfelei a jelenlegi szerződésük végéig árvédelem alatt részesülnek.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni az Azure Cosmos DB-vel történő díjszabásról és az adatok kezeléséről, tekintse meg az alábbi forrásokat:

1.  [Adatok particionálása a Cosmos DB-ben.](sql-api-partition-data.md) Ismerje meg az egypartíciós tároló és a particionált tárolók közötti különbséget, valamint a particionálási stratégia zökkenőmentes méretezéséhez való megvalósítására vonatkozó tippeket.
2.  [Cosmos DB árképzés](https://azure.microsoft.com/pricing/details/cosmos-db/). Ismerje meg az átviteli és a tárolási kapacitás kiépítési költségeit.
3.  [Kérésegységek](request-units.md). Ismerje meg a különböző művelettípusok, például olvasás, írás, lekérdezés átviteli közbeni felhasználását.
