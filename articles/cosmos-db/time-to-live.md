---
title: "Élettartam Azure Cosmos DB az adatok lejárnak |} Microsoft Docs"
description: "A TTL-t a Microsoft Azure Cosmos DB teszi lehetővé a dokumentumokat egy bizonyos idő eltelte után a rendszer automatikusan törlődnek."
services: cosmos-db
documentationcenter: 
keywords: "élettartam"
author: arramac
manager: jhubbard
editor: 
ms.assetid: 25fcbbda-71f7-414a-bf57-d8671358ca3f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: arramac
ms.openlocfilehash: 9b236ab8dd80b0c34501e0d60ba74dee3043d262
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="expire-data-in-azure-cosmos-db-collections-automatically-with-time-to-live"></a>Azure Cosmos DB gyűjtemények automatikusan élettartama adatok lejár
Alkalmazások létrehozására és hatalmas mennyiségű adat tárolására is használható. Az adatok egy részét, például generált gép esemény adatokat, a naplókat, és a felhasználói munkamenet az információ csak véges időn. Miután az adatok válnak az alkalmazás igényeinek felesleges ezek az adatok kiürítése és a tárolási igényeinek, az alkalmazások biztonságos.

"Élő idő" vagy a TTL-t a Microsoft Azure Cosmos DB nyújt a lehetővé teszi, hogy a dokumentumok automatikusan törlődnek az adatbázis egy bizonyos idő eltelte után. Az alapértelmezett élettartama a gyűjtemény szintjén állítsa be, és dokumentumra alapon felülbírálható. Élettartam beállítása, a gyűjtemény alapértelmezett vagy a dokumentum szinten után Cosmos DB automatikusan eltávolítja a dokumentumok létező adott időn belül, (másodpercben), mert utolsó módosítás.

A Cosmos DB élettartama a dokumentum utolsó módosításának elleni eltolás használja. Ehhez használja a `_ts` mező, amelyben megtalálható minden a dokumentumban. A _ts mezője dátumát és idejét jelző időbélyeg unix típusú epoch. A `_ts` mező frissül minden alkalommal, amikor a dokumentum módosításakor. 

## <a name="ttl-behavior"></a>Élettartam viselkedése
A TTL-szolgáltatás TTL tulajdonságok két szinten – a gyűjtemény és a dokumentum vezérli. Az értékek másodpercen belül vannak beállítva, és a különbözeti számít a `_ts` azon, hogy a dokumentum utolsó volt módosítani.

1. A gyűjtemény DefaultTTL
   
   * Ha hiányoznak (vagy null értékű), dokumentumok nem törlődnek automatikusan.
   * Ha jelen van, és az érték "-1" = végtelen – dokumentumok alapértelmezés szerint nem jár le
   * Ha jelen van, és az érték az egyes szám ("n") – a dokumentumok lejárnak "n" másodperc után utolsó módosítás
2. A dokumentumok élettartam: 
   
   * A tulajdonság csak akkor, ha a fölérendelt gyűjtemény DefaultTTL nincs alkalmazható.
   * Felülbírálja a fölérendelt gyűjtemény DefaultTTL értékét.

Amint a dokumentum lejárt (`ttl`  +  `_ts` < = aktuális server idő), a dokumentum jelölést "lejárt". Nincs művelet után most kapnak a dokumentumokon, és azok nem kerülnek bele az összes végrehajtott lekérdezések eredményeit. A dokumentumok fizikailag törlődnek a rendszerben, és törli a háttérben szerint egy későbbi időpontban. Ez nem foglal le a [kérelem egységek (RUs)](request-units.md) gyűjtemény költségvetés.

A fenti logika mutatja be a következő mátrix:

|  | DefaultTTL hiányzik vagy nem a gyűjtemény beállítása | DefaultTTL = -1-gyűjteménytől | DefaultTTL = "n" gyűjteménytől |
| --- |:--- |:--- |:--- |
| A dokumentum hiányzó élettartam |Nem végezhető el a dokumentum szinten bírálja felül, mivel a dokumentum és a gyűjtemény nem ismerik a TTL-t. |Ebben a gyűjteményben nem dokumentumok lejárnak. |A gyűjtemény dokumentumok amikor időköz n lejárta lejár. |
| Élettartam dokumentum -1 = |Nincs a szintjén bírálja felül, mert a gyűjtemény nem adja meg a dokumentum felülíró DefaultTTL tulajdonság. Egy dokumentumot a TTL-t a rendszer nem értelmezett. |Ebben a gyűjteményben nem dokumentumok lejárnak. |A dokumentum az élettartam =-1. a gyűjtemény nem jár. Minden más dokumentum "n" időszak után lejár. |
| Élettartam dokumentumon n = |Nem végezhető el a felülírja a dokumentum szinten. A dokumentumban TTL nem értelmezett, a rendszer. |A dokumentum TTL = n időköz n másodperc után lejár. Egyéb dokumentumokat fog örökli időintervalluma -1, és nem jár le. |A dokumentum TTL = n időköz n másodperc után lejár. Egyéb dokumentumokat "n" időköz örökli a gyűjteményben. |

## <a name="configuring-ttl"></a>Élettartam konfigurálása
Alapértelmezés szerint élettartama összes Cosmos DB gyűjtemények, valamint minden dokumentumok alapértelmezés szerint le van tiltva. Élettartam beállítható programozott módon, vagy az Azure portálon, a a **beállítások** szakasz a gyűjteményhez. 

## <a name="enabling-ttl"></a>Élettartam engedélyezése
Engedélyezze a TTL-t a gyűjteményt, vagy a dokumentumokat a gyűjteményen belül, a tulajdonsága DefaultTTL gyűjtemény vagy a -1, vagy a nullától eltérő pozitív számnak kell. Beállítása a DefaultTTL-1 érték azt jelenti, hogy a gyűjteményben lévő összes dokumentumot végtelen lesz élő alapértelmezett, de a Cosmos DB szolgáltatás által a gyűjteményhez, akik bírálták felül az alapértelmezett dokumentumok célszerű figyelemmel kísérni.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive =-1; //never expire by default

    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(databaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });

## <a name="configuring-default-ttl-on-a-collection"></a>A gyűjtemény alapértelmezett élettartam konfigurálása
Biztosan egy alapértelmezett élettartama a gyűjtemény szintjén konfigurálható. Egy gyűjtemény az élettartam beállításához meg kell adnia egy nullától eltérő pozitív szám, amely jelzi az időtartam (másodpercben), a gyűjteményben található összes dokumentum lejárati után utolsó módosításának időbélyeg a dokumentum (`_ts`). Vagy az alapértelmezett -1, ami azt jelenti, hogy beszúrni a gyűjtemény összes dokumentumot határozatlan ideig fog élő alapértelmezés szerint állíthatja be.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
    
    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        "/dbs/salesdb",
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });


## <a name="setting-ttl-on-a-document"></a>A dokumentum TTL beállítása
Egy gyűjtemény egy alapértelmezett élettartam beállítása mellett adott TTL dokumentum szinten állíthatja be. Ez a művelet felülírja a gyűjtemény az alapértelmezett.

* A dokumentum az élettartam beállításához meg kell adnia egy nullától eltérő pozitív szám, amely megadja, hogy az az időtartam (másodpercben), a dokumentum lejárati után utolsó módosításának időbélyeg a dokumentum (`_ts`).
* Ha egy dokumentum TTL mező nem rendelkezik, a gyűjtemény az alapértelmezett fog vonatkozni.
* Ha TTL le van tiltva, a gyűjtemény szintjén, a dokumentum TTL mezőjét figyelmen kívül TTL ismételt engedélyezéséig a gyűjteményen.

Íme egy részlet bemutatja, hogyan állítsa be a TTL lejárata egy dokumentumon:

    // Include a property that serializes to "ttl" in JSON
    public class SalesOrder
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        
        [JsonProperty(PropertyName="cid")]
        public string CustomerId { get; set; }
        
        // used to set expiration policy
        [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
        public int? TimeToLive { get; set; }
        
        //...
    }
    
    // Set the value to the expiration in seconds
    SalesOrder salesOrder = new SalesOrder
    {
        Id = "SO05",
        CustomerId = "CO18009186470",
        TimeToLive = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days 
    };


## <a name="extending-ttl-on-an-existing-document"></a>A létező dokumentum TTL kiterjesztése
A dokumentum bármely írási művelet végrehajtásával alaphelyzetbe állíthatja a TTL-t, a dokumentumon. Ezzel állítja a `_ts` a jelenlegi időpontnál, és a dokumentum lejárati ideje beállított visszaszámlálás a `ttl`, megkezdődik a újra. Ha úgy szeretné módosítani a `ttl` egy dokumentum is frissítheti a mező, mint bármely más beállítható mező teheti.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = 60 * 30 * 30; // update time to live
    
    response = await client.ReplaceDocumentAsync(salesOrder);

## <a name="removing-ttl-from-a-document"></a>Egy dokumentum eltávolítása a TTL-t
Ha már nem szeretné, hogy a dokumentum lejárati TTL be van állítva egy dokumentumot, majd a dokumentum beolvasása, távolítsa el a TTL mező és cserélje le a dokumentumot a kiszolgálón. Az élettartam mező törlődik a dokumentumot, a gyűjtemény az alapértelmezett lépnek érvénybe. Egy dokumentum lejár, és nem örököl a gyűjtemény majd be kell az élettartam értéke -1 értékre.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = null; // inherit the default TTL of the collection
    
    response = await client.ReplaceDocumentAsync(salesOrder);

## <a name="disabling-ttl"></a>Élettartam letiltása
A TTL letiltása teljes egészében a gyűjtemény, majd állítsa le a háttérben folyamatot törölni kell a gyűjtemény DefaultTTL tulajdonsága lejárt dokumentumot keres. Ez a tulajdonság törlése eltér a -1 értékre állítaná. A beállítást, ha a-1 érték azt jelenti, hogy a gyűjteménybe felvett új dokumentumok végtelen lesz élő, de ez felülírható a gyűjtemény adott dokumentumokat. Ez a tulajdonság teljesen eltávolítása a gyűjtemény azt jelenti, hogy a dokumentumok lejár, akkor is, ha vannak, akik explicit módon bírálták felül az előző alapértelmezett dokumentumok.

    DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
    
    // Disable TTL
    collection.DefaultTimeToLive = null;
    
    await client.ReplaceDocumentCollectionAsync(collection);

## <a name="ttl-and-index-interaction"></a>Élettartam és Index interakció
Élettartam hozzáadása vagy módosítása: az alapul szolgáló index a módosítása. Ha nincs TTL-t, és akkor adjon meg érvényes élettartamértéket - az eredmény indexelik újra a műveletet. Egységes indexe - felhasználó nem jelenik meg a indexű állapotban változásait. Abban az esetben, ha lassú index - indexe először az összes van mindig elfogja fel, és a TTL-t az módosítását, a rendszer teljesen újra index. Az utóbbi esetben szempontjából, hogy az index rebuild során végzett lekérdezések nem ad vissza a teljes vagy a megfelelő eredményeket. Nem módosítsa TTL Lusta index Ha adatok száma stb kell pontos, mert maga az indexelő mód Lusta.  Ideális esetben konzisztens index kell mindig választani. 

## <a name="faq"></a>GYIK
**Mit fog költség TTL-t?**

Nincs a TTL beállítást a dokumentum további költség nélkül.

**Mennyi ideig tart a dokumentum törlése után az élettartam működik-e?**

A dokumentumok lejárt azonnal, amennyiben a TTL-t, működik-e, és nem CRUD vagy a lekérdezés API-k elérhetők lesznek. 

**Egy dokumentumon TTL semmilyen hatással lesz a RU díjak?**

Nem, nem lesznek nincs hatással az keresztül (élettartam) – Cosmos DB lejárt dokumentumok törlési RU költségek.

**A TTL-szolgáltatás csak vonatkozik a teljes dokumentumot, vagy is szeretnék egyes dokumentum tulajdonságértékek lejárnak?**

TTL-t a teljes dokumentum vonatkozik. Ha azt szeretné, csak egy dokumentumot egy részének lejár, majd javasoljuk, hogy az a része kinyerése a fő dokumentum külön "kapcsolódó" dokumentumhoz, és majd használni a TTL-t a kibontott dokumentumon.

**Rendelkezik a TTL-t a szolgáltatás indexelési különleges követelményeket?**

Igen. Rendelkeznie kell a gyűjtemény [szabályzatokkal indexelő](indexing-policies.md) Consistent vagy Lazy. Az indexelő beállítása None szervezendő DefaultTTL beállítása közben hiba, mint fognak, kapcsolja ki a gyűjteményt, amely már be van állítva egy DefaultTTL az indexelő próbál eredményez.

## <a name="next-steps"></a>Következő lépések
Azure Cosmos DB kapcsolatos további tudnivalókért tekintse meg a szolgáltatás [ *dokumentáció* ](https://azure.microsoft.com/documentation/services/cosmos-db/) lap.

