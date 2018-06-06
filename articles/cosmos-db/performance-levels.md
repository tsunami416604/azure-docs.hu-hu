---
title: Azure Cosmos DB teljesítményszintet kivonása |} Microsoft Docs
description: További tudnivalók a S1, S2 és S3 teljesítményszintet korábban elérhető az Azure Cosmos-Adatbázisba.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d1bb7551e6dfb6c42853ab95096f17f5285c69c1
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796648"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>A S1, S2 és S3 teljesítményszintet kivonása

> [!IMPORTANT] 
> A cikkben szereplő S1, S2 és S3 teljesítményszintet használatból van, és már nem érhetők el az új Azure Cosmos DB fiókokat.
>

Ez a cikk S1, S2 és S3 teljesítményszintet áttekintést nyújt, és ismerteti, hogyan a gyűjteményeket, a teljesítmény szinteket használó áttelepítése az egyetlen particionált gyűjtemények is lehet. A cikk elolvasása után képes lesz a következő kérdések megválaszolásához:

- [Miért van a S1, S2 és S3 teljesítményszintet használatból van?](#why-retired)
- [Hogyan hajtsa végre az egypartíciós gyűjtemények és a particionált gyűjtemények hasonlítsa össze a S1, S2, S3 teljesítményszintet?](#compare)
- [Mit kell tennie, az adatok folyamatos hozzáférés érdekében?](#uninterrupted-access)
- [Hogyan változik a saját gyűjteményembe az áttelepítést követően?](#collection-change)
- [Hogyan fogja módosítani a saját számlázási I vagyok történő áttelepítése után az egypartíciós gyűjtemények?](#billing-change)
- [Mi történik, ha több mint 10 GB tárhelyet kell?](#more-storage-needed)
- [Módosítható a S1, S2 és S3 között a tervezett áttelepítés előtt teljesítményszintet?](#change-before)
- [Hogyan tudom át az S1, S2, S3 teljesítményszintek az egypartíciós gyűjtemények önállóan?](#migrate-diy)
- [Hogyan vagyok feladatátvétele a Ha az ügyfél egy EA vagyok?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Miért van a S1, S2 és S3 teljesítmény szintek hatókörről?

A S1, S2 és S3 teljesítményszintet biztosít a szabványos Azure Cosmos DB ajánlat biztosít rugalmasságot. S1, S2, S3 teljesítményszintet, az átviteli sebesség és a tárolási kapacitás előre beállított és nem ajánlja fel a rugalmasság. Azure Cosmos DB kínál testreszabása az átviteli sebesség és tárterület, felkínálva sokkal nagyobb rugalmasságot biztosít arra, hogy a méretezés pedig az igényeinek módosítása.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Hogyan hajtsa végre az egypartíciós gyűjtemények és a particionált gyűjtemények hasonlítsa össze a S1, S2, S3 teljesítményszintet?

Az alábbi táblázat összehasonlítja az átviteli sebesség és tárterület beállításait az egypartíciós gyűjtemények, a particionált gyűjtemények és S1, S2, S3 teljesítményszintet. Íme egy példa a amerikai keleti régiója 2 régió:

|   |Particionált gyűjtemény|Az egypartíciós gyűjtemény|S1|S2|S3|
|---|---|---|---|---|---|
|Maximális átviteli sebesség|Korlátlan|10 KB-os RU/mp|250 RU/mp|1 K RU/mp|2.5-K RU/mp|
|Minimum átviteli kapacitás|2.5-K RU/mp|400 RU/mp|250 RU/mp|1 K RU/mp|2.5-K RU/mp|
|Maximális tárolóméret|Korlátlan|10 GB|10 GB|10 GB|10 GB|
|Árlista (havonta)|Átviteli sebesség: $6 / 100 RU/mp<br><br>Tárolás: $ 0,25/GB|Átviteli sebesség: $6 / 100 RU/mp<br><br>Tárolás: $ 0,25/GB|$25 USD|$50 USD|$100 USD|

Az ügyfél egy EA folytatja? Ha igen, tekintse meg a [hogyan vagyok I csökkentheti az EA felhasználóinál vagyok?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Mit kell tennie, az adatok folyamatos hozzáférés érdekében?

Ha egy S1, S2 vagy S3 gyűjteményt, át kell telepítenie a gyűjteményt egy egypartíciós gyűjtemény programozott módon [a .NET SDK használatával](#migrate-diy). 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Hogyan változik a saját gyűjteményembe az áttelepítést követően?

Ha egy S1 gyűjteményt, egypartíciós gyűjtemény 400 RU/mp-átviteli áttelepítheti őket. 400 RU/mp a legalacsonyabb átviteli sebesség érhető el az egypartíciós gyűjtemények. Azonban költsége 400 RU/mp egypartíciós gyűjtemény megközelítőleg azonos módon meg volt fizető S1 gyűjteményének és 250 RU/mp –, így nem kell fizet az extra 150 RU/mp elérhető.

Ha egy S2 gyűjteményt, azokat egy 1 KB-os RU/mp egypartíciós gyűjtemény telepítheti át. Nincs változás az átviteli szinten jelenik meg.

Ha egy S3 gyűjteményt, azokat egy egypartíciós gyűjtemény 2,5 K RU/mp telepítheti át. Nincs változás az átviteli szinten jelenik meg.

Minden ezekben az esetekben miután áttelepítette a gyűjtemény lesz az átviteli szintű testreszabásához, vagy azt felfelé és lefelé alacsony késésű hozzáférést biztosítani a felhasználók igény szerint méretezheti. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Hogyan fogja módosítani a saját számlázási I az egypartíciós gyűjtemények történő áttelepítése után?

Feltéve, hogy 10 S1 gyűjtemények, 1 GB tárhelyet minden, a US keleti terület rendelkezik, és ezek 10 S1 gyűjteményt telepít át, a 10 az egypartíciós gyűjtemények: 400 RU/mp (minimális). A számlázási következőképpen fog kinézni, ha a 10 az egypartíciós gyűjtemények esetében a teljes hónap:

![Hogyan S1 tarifacsomag 10 gyűjtemények összehasonlítja 10 gyűjtemények használatával az egypartíciós gyűjtemény díjszabása](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>Mi történik, ha több mint 10 GB tárhelyet kell?

E rendelkezik egy gyűjteménybe S1, S2 vagy S3 teljesítményszintet és, vagy ezek mindegyike rendelkezik, 10 GB-os kapacitású, a Azure Cosmos DB adatáttelepítés eszközzel az adatok áttelepítéséhez egy particionált gyűjtemény gyakorlatilag az egypartíciós gyűjtemény korlátlan tárterület. A particionált gyűjtemény előnyeivel kapcsolatos információk: [particionálás és az Azure Cosmos Adatbázisba skálázás](sql-api-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Módosítható a S1, S2 és S3 között a tervezett áttelepítés előtt teljesítményszintet?

S1, S2 és S3 teljesítménnyel csak meglévő fiókok módosíthatja, és módosítsa a teljesítmény szintű rétegek programozott módon [a .NET SDK használatával](#migrate-diy). Ha egy egypartíciós gyűjtemény S1, S3 vagy S3 módosítja, a S1, S2 vagy S3 teljesítményszintet nem lehet visszatérni.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Hogyan tudom át az S1, S2, S3 teljesítményszintek az egypartíciós gyűjtemények önállóan?

Akkor is áttelepítheti a S1, S2 és S3 teljesítményszintek az egypartíciós gyűjtemények programozott módon [a .NET SDK használatával](#migrate-diy). Ehhez a saját rugalmas átviteli lehetőségekről az egypartíciós gyűjtemények kihasználják a tervezett áttelepítés előtt.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>A .NET SDK használatával az egypartíciós gyűjtemények áttelepítése

Ez a szakasz csak hozzá van rendelve egy gyűjtési teljesítmény módosítása szinten használatával a [SQL .NET API](sql-api-sdk-dotnet.md), a folyamat hasonló, ha a Csomagjától, de.

Íme egy kódrészletet a a gyűjtemény átviteli sebességének módosítása a 5 000 kérelemegység / másodperc:
    
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

Látogasson el [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) további példákat és a további tudnivalók az ajánlat módszerek:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Hogyan vagyok feladatátvétele a Ha az ügyfél egy EA vagyok?

Nagyvállalati ügyfelek az aktuális szerződés végéig védett ár is.

## <a name="next-steps"></a>További lépések
Tarifa- és Azure Cosmos DB adatok kezelésével kapcsolatos további tudnivalókért ismerheti meg ezeket az erőforrásokat:

1.  [Particionálás adatokat az Adatbázisba az Cosmos](sql-api-partition-data.md). Az egypartíciós tároló particionált tárolók, valamint tippek az zökkenőmentesen méretezési jó particionálási stratégia megvalósítása közötti különbségek megértése.
2.  [Cosmos DB árképzési](https://azure.microsoft.com/pricing/details/cosmos-db/). További információk a telepítés átviteli sebesség és a tároló felhasználása költsége.
3.  [Egységek kérelem](request-units.md). Ismerje meg, a felhasználás átviteli különböző művelet-típusok, például a rekordhoz olvasási, írási, lekérdezés.
