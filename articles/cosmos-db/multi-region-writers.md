---
title: "Az Azure Cosmos DB több főkiszolgálós adatbázis architektúrák |} Microsoft Docs"
description: "További tudnivalók a helyi olvasási és írási alkalmazási architektúrákban tervezéséről Azure Cosmos DB több földrajzi régiók között."
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 706ced74-ea67-45dd-a7de-666c3c893687
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cf1482ae7b1070023703f5dbe861d151f5d64fd8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="multi-master-globally-replicated-database-architectures-with-azure-cosmos-db"></a>Több főkiszolgálós globálisan replikált Azure Cosmos DB az adatbázis-architektúra
Azure Cosmos DB támogatja kulcsrakész [globális replikációs](distribute-data-globally.md), amely lehetővé teszi, hogy több területre kis késleltetésű hozzáféréssel bárhol a munkaterhelési adatok terjesztése. Ebben a modellben van általánosan használt publisher/fogyasztói ahol egy-egy földrajzi régiót íróhoz és egyéb (olvasás) régiókban globálisan elosztott olvasók van. 

Azure Cosmos DB globális replikációs támogatási segítségével is, amelyben írók és olvasók globálisan fel vannak osztva alkalmazások létrehozását. Ez a dokumentum egy mintát, amely lehetővé teszi a helyi írási és olvasási helyi hozzáférési elérése az Azure Cosmos DB használatával elosztott íróhoz ismertet.

## <a id="ExampleScenario"></a>Tartalom közzététele - bemutató példa
Nézzük leírására, hogyan használható globálisan elosztott több-region/több-főkulcs olvasási írási minták Azure Cosmos DB valós forgatókönyvekben. Fontolja meg a közzétételi tartalomplatform Azure Cosmos DB épül. Az alábbiakban olyan követelményekkel, amelyeket ezen a platformon meg kell felelnie a kiváló felhasználói élmény a közzétevők és a fogyasztók.

* Szerzők és a előfizetők oszlik el a világ 
* Szerzők közzé kell tennie (írás) cikkeket a helyi (legközelebbi) régió
* Szerzők olvasók/előfizetők a cikkek felvételéhez világ különböző pontjain lehet. 
* Előfizetők kell értesítést kap, amikor új cikkek.
* Előfizetők cikkek olvasni a helyi régió képesnek kell lennie. Is kell adhat hozzá az alábbi cikkek értékelést. 
* Bárki, beleértve a szerző, kattintson az alábbi cikkek cikkek csatlakozó összes értékelést egy helyi területet képes nézet kell lennie. 

Feltéve, hogy a fogyasztók és a közzétevők milliárd cikkeket, több millió hamarosan tudunk skála helység hozzáférés biztosítása mellett a problémák alkalmazásra. A legtöbb méretezhetőség problémát, a megoldás egy jó particionálási stratégia rejlik. A következő modellhez tartozó cikkek, tekintse át és értesítések-dokumentumokként, Azure Cosmos DB fiókok konfigurálása és valósítja meg az adatelérési réteg vizsgáljuk meg. 

Ha azt szeretné, további információt a particionálás és partíciós kulcsok, lásd: [particionálás és az Azure Cosmos Adatbázisba skálázás](partition-data.md).

## <a id="ModelingNotifications"></a>Modellezési értesítések
A rendszer értesítések egy felhasználó számára az adatok adott hírcsatornák. Emiatt a hozzáférési minták értesítések dokumentumok lehet mindig egyetlen felhasználó környezetében. Például akkor "értesítést közzétett egy felhasználó számára" vagy "beolvasni a megadott felhasználó összes értesítésben". Igen, az optimális választás az ilyen típusú kulcs particionálás lenne `UserId`.

    class Notification 
    { 
        // Unique ID for Notification. 
        public string Id { get; set; }

        // The user Id for which notification is addressed to. 
        public string UserId { get; set; }

        // The partition Key for the resource. 
        public string PartitionKey 
        { 
            get 
            { 
                return this.UserId; 
            }
        }

        // Subscription for which this notification is raised. 
        public string SubscriptionFilter { get; set; }

        // Subject of the notification. 
        public string ArticleId { get; set; } 
    }

## <a id="ModelingSubscriptions"></a>Modellezési előfizetések
Az előfizetések különböző feltételek, például egy adott konkrét kategóriával a cikkek, vagy egy adott publisher hozható létre. Ezért a `SubscriptionFilter` a partíciós kulcs jó választás.

    class Subscriptions 
    { 
        // Unique ID for Subscription 
        public string Id { get; set; }

        // Subscription source. Could be Author | Category etc. 
        public string SubscriptionFilter { get; set; }

        // subscribing User. 
        public string UserId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.SubscriptionFilter; 
            } 
        } 
    }

## <a id="ModelingArticles"></a>Modellezési cikkek
Egy cikk, amelynél értesítések keresztül, amennyiben a általában alapuló lekérdezések a `Article.Id`. Kiválasztása `Article.Id` partíció a kulcs így biztosítja a legjobb terjesztési cikkek az Azure Cosmos DB gyűjteményt belül tárolásához. 

    class Article 
    { 
        // Unique ID for Article 
        public string Id { get; set; }
        
        public string PartitionKey 
        { 
            get 
            { 
                return this.Id; 
            } 
        }
        
        // Author of the article
        public string Author { get; set; }

        // Category/genre of the article
        public string Category { get; set; }

        // Tags associated with the article
        public string[] Tags { get; set; }

        // Title of the article
        public string Title { get; set; }
        
        //... 
    }

## <a id="ModelingReviews"></a>Ellenőrzi, hogy modellezési
Ellenőrzések hasonló cikkek, főleg írása és cikk olvassa. Kiválasztása `ArticleId` egy partíció kulcs biztosít az ajánlott terjesztési és hatékony hozzáférés cikk társított felülvizsgálat. 

    class Review 
    { 
        // Unique ID for Review 
        public string Id { get; set; }

        // Article Id of the review 
        public string ArticleId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.ArticleId; 
            } 
        }
        
        //Reviewer Id 
        public string UserId { get; set; }
        public string ReviewText { get; set; }
        
        public int Rating { get; set; } }
    }

## <a id="DataAccessMethods"></a>Adatok hozzáférési réteg módszerek
Most vizsgáljuk meg a fő adatok hozzáférési metódust kell megvalósítani. A módszerek listája itt található, amely a `ContentPublishDatabase` kell:

    class ContentPublishDatabase 
    { 
        public async Task CreateSubscriptionAsync(string userId, string category);
    
        public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId);
    
        public async Task<Article> ReadArticleAsync(string articleId);
    
        public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating);
    
        public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId); 
    }

## <a id="Architecture"></a>Azure Cosmos DB fiók konfigurálása
Helyi olvas és ír, igazolnia kell particionálni adatok nem csupán a partíción kulcs, de is alapján földrajzi hozzáférési mintázatát területekre. A modell támaszkodik, amely egy georeplikált Azure Cosmos DB adatbázisfiók mindegyik régióhoz. Például két régiókban, itt van a telepítés több területi írási műveletek esetében:

| Fióknév | Írási régió | Olvasási régió |
| --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |

Az alábbi ábrán látható, hogyan olvasási és írási történik a telepítés egy tipikus alkalmazásban:

![Azure Cosmos DB több főkiszolgálós architektúra](./media/multi-region-writers/multi-master.png)

Íme egy kódrészletet, amely a DAL futtatja az ügyfelekhez inicializálásával a `West US` régióban.
    
    ConnectionPolicy writeClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    writeClientPolicy.PreferredLocations.Add(LocationNames.WestUS);
    writeClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

    DocumentClient writeClient = new DocumentClient(
        new Uri("https://contentpubdatabase-usa.documents.azure.com"), 
        writeRegionAuthKey,
        writeClientPolicy);

    ConnectionPolicy readClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    readClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);
    readClientPolicy.PreferredLocations.Add(LocationNames.WestUS);

    DocumentClient readClient = new DocumentClient(
        new Uri("https://contentpubdatabase-europe.documents.azure.com"),
        readRegionAuthKey,
        readClientPolicy);

Az előző beállítás az adatelérési réteg továbbíthatja az összes írási műveleteket ad ki a helyi fiók, ahol központilag telepítették alapján. Olvasás a számára a globális adatokat megtekintheti a fiókot is olvasásakor hajtja végre. Ez a megközelítés kiterjeszthető annyi régiók szükség szerint. Íme például egy három földrajzi régiókhoz telepítése:

| Fióknév | Írási régió | Olvassa el az 1 régió | Olvassa el a régió 2 |
| --- | --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |`Southeast Asia` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |`Southeast Asia` |
| `contentpubdatabase-asia.documents.azure.com` | `Southeast Asia` |`North Europe` |`West US` |

## <a id="DataAccessImplementation"></a>Adatok hozzáférési réteg végrehajtása
Most már az adatelérési réteg (DAL) az alkalmazás a két írható régió végrehajtásának vizsgáljuk meg. A DAL meg kell valósítania az alábbi lépéseket:

* Hozzon létre több példánya `DocumentClient` az egyes fiókok számára. Két régiókban, a DAL feltünteti van egy `writeClient` és egy `readClient`. 
* Az alkalmazás telepített régió alapján, konfigurálja a végpontokat a `writeclient` és `readClient`. Például a DAL telepített `West US` használ `contentpubdatabase-usa.documents.azure.com` írási műveletek végrehajtásához. A DAL telepített `NorthEurope` használ `contentpubdatabase-europ.documents.azure.com` az írásokhoz.

Az előző beállítás az adatok hozzáférési metódusokat valósítható meg. Írási műveletek továbbítsa a megfelelő írási `writeClient`.

    public async Task CreateSubscriptionAsync(string userId, string category)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Subscriptions
        {
            UserId = userId,
            SubscriptionFilter = category
        });
    }

    public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Review
        {
            UserId = userId,
            ArticleId = articleId,
            ReviewText = reviewText,
            Rating = rating
        });
    }

Értesítések és az ellenőrzések olvasásához, kell olvasási régiók és Unió az eredmények látható módon a következő kódrészletet:

    public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId)
    {
        IDocumentQuery<Notification> writeAccountNotification = (
            from notification in this.writeClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();
        
        IDocumentQuery<Notification> readAccountNotification = (
            from notification in this.readClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();

        List<Notification> notifications = new List<Notification>();

        while (writeAccountNotification.HasMoreResults || readAccountNotification.HasMoreResults)
        {
            IList<Task<FeedResponse<Notification>>> results = new List<Task<FeedResponse<Notification>>>();

            if (writeAccountNotification.HasMoreResults)
            {
                results.Add(writeAccountNotification.ExecuteNextAsync<Notification>());
            }

            if (readAccountNotification.HasMoreResults)
            {
                results.Add(readAccountNotification.ExecuteNextAsync<Notification>());
            }

            IList<FeedResponse<Notification>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Notification> feed in notificationFeedResult)
            {
                notifications.AddRange(feed);
            }
        }
        return notifications;
    }

    public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId)
    {
        IDocumentQuery<Review> writeAccountReviews = (
            from review in this.writeClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();
        
        IDocumentQuery<Review> readAccountReviews = (
            from review in this.readClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();

        List<Review> reviews = new List<Review>();
        
        while (writeAccountReviews.HasMoreResults || readAccountReviews.HasMoreResults)
        {
            IList<Task<FeedResponse<Review>>> results = new List<Task<FeedResponse<Review>>>();

            if (writeAccountReviews.HasMoreResults)
            {
                results.Add(writeAccountReviews.ExecuteNextAsync<Review>());
            }

            if (readAccountReviews.HasMoreResults)
            {
                results.Add(readAccountReviews.ExecuteNextAsync<Review>());
            }

            IList<FeedResponse<Review>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Review> feed in notificationFeedResult)
            {
                reviews.AddRange(feed);
            }
        }

        return reviews;
    }

Így egy jó particionálási kulcs és a statikus ügyfélalapú particionálás kiválasztásával érhető el több területi helyi írások és olvasások Azure Cosmos DB használatával.

## <a id="NextSteps"></a>Következő lépések
Ez a cikk azt leírt használatát globálisan elosztott több területi olvasási-írási minták rendelkező Azure Cosmos DB használatával tartalom közzététele egy mintaforgatókönyv.

* További tudnivalók arról, hogyan támogatja a Azure Cosmos DB [globális terjesztési](distribute-data-globally.md)
* További tudnivalók [automatikus és manuális feladatátvételt az Azure Cosmos-Adatbázisba](regional-failover.md)
* További tudnivalók [az Azure Cosmos DB globális egységesítése](consistency-levels.md)
* Több régiókat fejlesztést a [Azure Cosmos DB - DocumentDB API](tutorial-global-distribution-documentdb.md)
* Több régiókat fejlesztést a [Azure Cosmos DB - MongoDB API](tutorial-global-distribution-MongoDB.md)
* Több régiókat fejlesztést a [Azure Cosmos DB - tábla API](tutorial-global-distribution-table.md)
