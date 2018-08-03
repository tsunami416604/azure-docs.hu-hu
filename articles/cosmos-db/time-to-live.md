---
title: Adatok az Azure Cosmos DB az élettartam elévülése |} A Microsoft Docs
description: Élettartam Microsoft Azure Cosmos DB üríti ki automatikusan a rendszer bizonyos idő után dokumentumok lehetőséget biztosít.
services: cosmos-db
keywords: élettartam
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: sngun
ms.openlocfilehash: 49f6d6ee65ffae71cba8c73301355bfe2bdcd1d6
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480556"
---
# <a name="expire-data-in-azure-cosmos-db-collections-automatically-with-time-to-live"></a>Az Azure Cosmos DB-gyűjtemények automatikusan az élettartam adatok elévülése
Az alkalmazások létrehozásához és hatalmas mennyiségű adat tárolásához. Ezen adatok némelyike, például létrehozott machine esemény adatok, naplók és a felhasználói munkamenet az információ hasznos csak egy véges ideig. Miután az adatok válik az alkalmazás számára szükséges többlet, is biztonságos törléséhez ezeket az adatokat, és csökkentse egy alkalmazás tárolási igényeinek megfelelően.

"Élő idő" vagy a TTL a Microsoft Azure Cosmos DB lehetővé teszi a dokumentumok automatikusan törlődnek az adatbázisból egy idő után kell. Az alapértelmezett élettartama a gyűjtemény szintjén beállított, és felülbírálva a dokumentumra vonatkozó alapul. Miután TTL be van állítva, a gyűjtemény alapértelmezett vagy dokumentum szinten Cosmos DB automatikusan eltávolítja dokumentumok létező időt (másodpercben), az időszak elteltével, mivel utoljára módosítva.

Az Azure Cosmos DB élettartam a dokumentum utolsó módosításának elleni eltolási értéket használja. Ehhez használja a `_ts` mező, amely minden dokumentumnak létezik. A _ts mező indexe unix-stílusú alapidőpont dátumot és időt jelölő időbélyegző. A `_ts` minden alkalommal, amikor módosul egy dokumentum frissítendő mezőket. 

## <a name="ttl-behavior"></a>Élettartam viselkedés
Az élettartam szolgáltatást két szinten – a gyűjtemény szintjén és az elvárt dokumentum TTL tulajdonságok vezérli. Az értékek másodpercen belül vannak beállítva, és a különbözeti számít a `_ts` , hogy a dokumentum utolsó lett módosítva.

1. A gyűjtemény DefaultTTL
   
   * Ha hiányzik (vagy null értékű) a dokumentumok nem törlődnek automatikusan.
   * Ha jelen van, az értéke "-1" értékre van állítva = végtelen – dokumentumok alapértelmezés szerint nem jár le
   * Ha jelen van, és az érték egy szám ("n") értékre van állítva – a dokumentumok lejárnak "n" másodperc utolsó módosítás után
2. A dokumentumok Élettartama: 
   
   * A tulajdonság csak akkor, ha DefaultTTL megtalálható a fölérendelt gyűjtemény esetében alkalmazható.
   * Felülbírálja a fölérendelt gyűjtemény DefaultTTL értékét.

Amint a dokumentum lejárt (`ttl`  +  `_ts` < = server aktuális idő), a dokumentum "lejárt" van megjelölve. Nincs művelet engedélyezni a dokumentumok ezt az időpontot követően, és azok ki lesznek zárva minden végrehajtott lekérdezések eredményeit. A dokumentumok fizikailag törlődnek a rendszerben, és törlődnek a háttérben kulcsmodulonként egy későbbi időpontban. Ez nem igényel minden [kérelemegység (RU)](request-units.md) gyűjtemény költségvetés.

A fenti logikai mutatja be a következő mátrix:

|  | DefaultTTL hiányzik vagy nem a gyűjtemény beállítása | DefaultTTL =-1 értéket a gyűjteményben | DefaultTTL = "n" a gyűjteményben |
| --- |:--- |:--- |:--- |
| Élettartam hiányzik a dokumentum |Semmi óta a dokumentum és a gyűjtemény amelyek TTL nem ismerik a dokumentum szintjén felülbírálására. |Ebben a gyűjteményben nem dokumentumok le fog járni. |Ebben a gyűjteményben a dokumentumok időköz n eltelt amikor lejár. |
| Élettartam dokumentum -1 = |Semmit nem kell a gyűjtemény óta a dokumentum szintjén felülbírálás nem adja meg a DefaultTTL tulajdonság, amely dokumentumot felül lehet bírálni. Egy dokumentum TTL a nem értelmezett, a rendszer. |Ebben a gyűjteményben nem dokumentumok le fog járni. |A dokumentum TTL =-1 ebben a gyűjteményben lévő soha nem jár le. Minden egyéb dokumentumokat "n" idő után jár le. |
| Élettartam = n dokumentum |Semmi nem bírálja felül a szintjén. Egy dokumentum TTL a nem értelmezett, a rendszer. |A dokumentum TTL = n időköz n másodperc után lejár. Egyéb dokumentumokat fog időköz 1 öröklik, és soha ne járjon le. |A dokumentum TTL = n időköz n másodperc után lejár. Egyéb dokumentumokat "n" időköz öröklik a gyűjteményből. |

## <a name="configuring-ttl"></a>Élettartam konfigurálása
Élettartam alapértelmezés szerint le van tiltva az összes Cosmos DB-gyűjtemények és az összes dokumentum alapértelmezés szerint. Élettartam beállítható programozott módon, vagy az Azure portal használatával. A következő lépések segítségével élettartam konfigurálása az Azure Portalról:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) , és keresse meg az Azure Cosmos DB-fiókot.  

2. Keresse meg a következőt kívánja beállítani az élettartam értéke gyűjtemény, nyissa meg a **méretezés és beállítások** ablaktáblán. Láthatja, hogy az idő élettartama alapértelmezett értéke **ki**. Módosíthatja, hogy **bekapcsolva (alapértelmezett) nincs** vagy **a**.

   **ki** -dokumentumok nem törlődnek automatikusan.  
   **(nincs alapértelmezett érték) a** – Ez a beállítás az élettartam értéke "1" (végtelen), ami azt jelenti, hogy a dokumentumok nem jár le, alapértelmezés szerint állítja be.  
   **a** -dokumentumok lejárnak "n" másodperc utolsó módosítás után.  

   ![Az élő idő beállítása](./media/time-to-live/set-ttl-in-portal.png)

## <a name="enabling-ttl"></a>Élettartam engedélyezése
Engedélyezi az élettartam a gyűjteményt, vagy a dokumentumokat a gyűjteményen belül, állítsa a gyűjtemény DefaultTTL tulajdonság a -1 vagy nullától eltérő pozitív számnak kell. Beállítás a DefaultTTL-1 azt jelenti, hogy az alapértelmezett örökre kapnak helyet a gyűjteményben lévő összes dokumentumot, de a Cosmos DB szolgáltatás célszerű figyelemmel kísérni a gyűjtemény, akik bírálták felül az alapértelmezett dokumentumok.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive =-1; //never expire by default

    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(databaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });

## <a name="configuring-default-ttl-on-a-collection"></a>A gyűjtemény alapértelmezett élettartam konfigurálása
Ön egy alapértelmezett élettartama a gyűjtemény szintjén konfigurálható. Az élettartam beállítása egy gyűjteményen, meg kell adnia egy nullától eltérő pozitív szám, amely azt jelzi, hogy az az időtartam másodpercben, amely után az utolsó módosításának a dokumentum időbélyeg érvényessége lejár a gyűjteményben lévő összes dokumentumot (`_ts`). Másik lehetőségként beállíthatja az alapértelmezett 1 értéket ad, ami azt jelenti, hogy minden dokumentumot a gyűjteményhez beszúrt határozatlan időre lesz élő alapértelmezés szerint.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
    
    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        "/dbs/salesdb",
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });


## <a name="setting-ttl-on-a-document"></a>A dokumentum TTL-beállítást
Mellett egy gyűjteményt egy alapértelmezett TTL beállításnál beállíthatja adott TTL dokumentum szinten. Ez felülírja az alapértelmezett a gyűjtemény.

* Az élettartam beállítani egy dokumentumot, meg kell adnia egy nullától eltérő pozitív szám, amely azt jelzi, hogy az időszak lejár a dokumentum utolsó módosításának a dokumentum időbélyeg után másodpercben (`_ts`).
* Ha a dokumentum nem rendelkezik TTL mezővel, a gyűjtemény az alapértelmezett lesz érvényes.
* Ha TTL le van tiltva, a gyűjtemény szintjén, a dokumentum TTL-mező figyelmen kívül mindaddig, amíg a TTL újra engedélyezve van a gyűjteményen.

Itt látható egy részlet megjelenítése az élettartam lejárta megadása egy dokumentum:

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


## <a name="extending-ttl-on-an-existing-document"></a>A meglévő dokumentum TTL kiterjesztése
A dokumentum minden írási művelet végrehajtásával alaphelyzetbe állíthatja a dokumentum az Élettartamot. Ennek során állítja a `_ts` az aktuális idő és a dokumentum lejárati ideje beállított visszaszámlálás az `ttl`, elkezdi az újra. Ha módosítani szeretné a `ttl` egy dokumentum is frissítheti a mező, ahogy bármely más beállítható mező is végezhet.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = 60 * 30 * 30; // update time to live
    
    response = await client.ReplaceDocumentAsync(readDocument);

## <a name="removing-ttl-from-a-document"></a>Élettartam eltávolítása a dokumentumból
Ha egy dokumentum TTL lett beállítva, és már nem szeretné, hogy a dokumentum hamarosan lejár, ezután a dokumentum lekéréséhez, távolítsa el az élettartam mezőt, és cserélje le a dokumentumot a kiszolgálón. A TTL mező eltávolítják a dokumentumot, az a gyűjtemény alapértelmezett lépnek érvénybe. Egy dokumentum lejárjanak leállítása, és nem örököl a gyűjtemény majd be kell az élettartam értéke-1.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = null; // inherit the default TTL of the collection
    
    response = await client.ReplaceDocumentAsync(readDocument);

## <a name="disabling-ttl"></a>Élettartam letiltása
Tiltsa le a TTL teljes egészében a gyűjtemény, és törölni kell a gyűjtemény DefaultTTL tulajdonsága lejárt dokumentumot keres a háttérben futó folyamatot leállítja. Ez a tulajdonság törlése nem azonos a -1 értékre állítaná. Örökre kapnak helyet-1 azt jelenti, hogy a gyűjtemény hozzáadott új dokumentumot a beállítást, de ez felülírható az adott gyűjteményben lévő dokumentumot. Ez a tulajdonság eltávolítása a gyűjteményből teljesen azt jelenti, hogy a nem dokumentum lejár, akkor is, ha vannak, akik kifejezetten bírálták felül az előző alapértelmezett dokumentumok.

    DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
    
    // Disable TTL
    collection.DefaultTimeToLive = null;
    
    await client.ReplaceDocumentCollectionAsync(collection);

<a id="ttl-and-index-interaction"></a> 
## <a name="ttl-and-index-interaction"></a>Élettartam és index interakció
Hozzáadása vagy módosítása egy gyűjteményen a TTL-értéket módosítja az alapul szolgáló index. Amikor az élettartam értéke a be-vagy kikapcsolása módosul, a gyűjtemény újraindexelése van. Amikor változtatásokat végez az indexelési házirendet konzisztens az indexelési mód esetén, a felhasználók nem figyelje meg az index módosítása. Az indexelő mód értéke Lusta, ha az index mindig kölcsönhatásai, és az élettartam értéke módosul, ha az index ismételt létrehozását sablon nélkül. Az élettartam értéke módosul, és az index mód értéke Lusta, amikor az indexkészítés során végzett lekérdezések nem adnak vissza teljes vagy megfelelő eredményeket.

Ha pontosan az adott vissza adatokat, ne módosítsa az élettartam értéke beállításakor az indexelési mód Lusta. Ideális esetben konzisztens index lekérdezés konzisztens eredmények biztosítása kell kiválasztani. 

## <a name="faq"></a>GYIK
**Milyen fogja terhelni TTL?**

Nincs egy dokumentum TTL beállítás további költség nélkül.

**Mennyi ideig tart a dokumentum törlése után az élettartam (TTL) fel?**

A dokumentumok az élettartam működik, és nem lesz elérhető a CRUD-MŰVELETEKKEL, vagy a lekérdezési API-k után azonnal lejárt. 

**Egy dokumentum TTL semmilyen hatással lesz a Kérelemegység díjai?**

Nem, lesz nincs hatással a Cosmos DB-ben Élettartama lejárt dokumentumoknak törléseket RU kell fizetni.

**A TTL szolgáltatás csak vonatkozik a teljes dokumentumot, vagy is I egyes dokumentum tulajdonságértékek lejárnak?**

A teljes dokumentum TTL vonatkozik. Ha azt szeretné, hogy a dokumentum egy része, majd javasoljuk, hogy a rész kinyerése a fő dokumentum egy külön "társított" dokumentumba, és kinyert dokumentum TTL majd használja.

**A TTL szolgáltatás rendelkezik az indexelő különleges követelményeket?**

Igen. Rendelkeznie kell a gyűjtemény [indexelési szabályzat](indexing-policies.md) Consistent vagy Lazy. Az indexelő None értékre van állítva egy gyűjteményen DefaultTTL beállítása közben hiba, ahogyan kapcsolja ki egy gyűjteményt, amely rendelkezik egy DefaultTTL már be van állítva az indexelő próbál eredményez.

## <a name="next-steps"></a>További lépések
Azure Cosmos DB kapcsolatos további információkért tekintse meg a szolgáltatás [ *dokumentáció* ](https://azure.microsoft.com/documentation/services/cosmos-db/) lapot.

