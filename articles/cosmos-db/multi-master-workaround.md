---
title: Hajtsa végre a többrégiós írások és olvasások válassza ki a partíciós kulcs jobb |} A Microsoft Docs
description: Ismerje meg a helyi olvasási és írási architektúrák tervezéséről több földrajzi régióban, az Azure Cosmos DB által olyan partíciókulcsot.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: conceptual
ms.date: 06/6/2018
ms.author: rimman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3d38b7cd7d1f28f706e94782602926abc8fc11e3
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "42060804"
---
# <a name="perform-multi-region-writes-and-reads-by-choosing-the-right-partitioning-key"></a>Válassza ki a partíciós kulcs jobb többrégiós írások és olvasások végrehajtása

Az Azure Cosmos DB támogatja a kulcsrakész [globális replikációt](distribute-data-globally.md), amely lehetővé teszi, hogy az adatok közel valós idejű hozzáféréssel bárhol az a számítási feladatok több régióban. Ez a modell közzétevő/fogyasztói számítási feladatok gyakran használják, ha egy írója földrajzi régióban, és a globálisan elosztott olvasók más (olvasás) régióban van. 

Azure Cosmos DB globális replikációt támogatási használatával, amelyben írók és olvasóknak vannak globálisan elosztott alkalmazásokat hozhat létre. Ez a dokumentum ismerteti egy mintát, amely lehetővé teszi a helyi írási és olvasási helyi hozzáférési elérése az Azure Cosmos DB használatával az elosztott-íróhoz.

## <a id="ExampleScenario"></a>Tartalom közzététele – egy példán keresztül
Nézzük meg, írja le, hogyan használhatja a globálisan elosztott több-region/több-főkulcs írható/olvashatóként minták az Azure Cosmos DB használatával valós forgatókönyvekben. Érdemes lehet egy Azure Cosmos DB beépített tartalom közzétételi-platform. Az alábbiakban néhány követelmények megszabásához ezen a platformon egy nagyszerű felhasználói élményt biztosít a kiadók és a fogyasztók számára.

* Szerzők és a előfizetők oszlik el a világ 
* Szerzők közzé kell tennie (írás) cikkeket a saját helyi (legközelebbi) régió
* Szerzők olvasók/előfizetők cikkeik, a világ különböző pontjain rendelkezik. 
* Előfizetők kell értesítést kaphat, amikor új cikkek.
* Előfizetők számára, olvassa el a cikkeket a helyi régióból képesnek kell lennie. Akkor is tudnia kell felülvizsgálatok hozzá ezeket a cikkeket. 
* Bárki, beleértve a cikkek szerzőjének kell lennie a helyi régióból cikkek csatolt összes felülvizsgálat képes megtekintése. 

Feltéve, hogy a fogyasztók és cikkeket, több milliárd közzétevőket hamarosan kell, hogy a skála helye való hozzáférés biztosítása mellett problémák leküzdésében. A legtöbb méretezhetőségi problémákat, és a megoldás egy jó particionálási stratégia rejlik. Ezután nézzük, hogyan cikkeket, tekintse át és értesítések-dokumentumok formájában modellezését, az Azure Cosmos DB-fiókok konfigurálása és egy data access implementálhat. 

Ha szeretne további információ a particionálási és partíciókulcsok, lásd: [particionálás és skálázás az Azure Cosmos DB](partition-data.md).

## <a id="ModelingNotifications"></a>Modellezési értesítések
Értesítések olyan adatok bekerülnek adott egy felhasználónak. Emiatt az adathozzáférési mintáknak megfelelően értesítések dokumentumok lehet mindig egyetlen felhasználó kontextusában. Például szeretné "értesítés küldése egy felhasználó számára" vagy "beolvasni az adott felhasználó számára minden értesítés". Így a kulcs Pro tento typ particionálás az optimális választás lenne `UserId`.

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
Előfizetések is létrehozható különféle feltételek, például a cikkeket, vagy egy adott közzétevő konkrét kategóriáját. Ezért a `SubscriptionFilter` jó választás az olyan partíciókulcsot.

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
Miután egy cikk értesítések alapján azonosítja, további lekérdezések általában alapul a `Article.Id`. Választás `Article.Id` partíciónak a kulcs így biztosít az ajánlott terjesztési cikkek belül egy Azure Cosmos DB-gyűjtemények tárolására. 

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

## <a id="ModelingReviews"></a>Modellezési értékelések
Értékelések cikkeket, például többnyire írt és olvassa el a cikk a környezetben. Választás `ArticleId` partícióként kulcs ajánlott terjesztési és biztosít hatékony hozzáférési felülvizsgálatok cikk társított. 

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

## <a id="DataAccessMethods"></a>Hozzáférési réteg metody dat
Most nézzük meg a fő adatok hozzáférési metódusokat kell megvalósítani. Íme a módszerek listája, amelyek a `ContentPublishDatabase` van szüksége:

    class ContentPublishDatabase 
    { 
        public async Task CreateSubscriptionAsync(string userId, string category);
    
        public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId);
    
        public async Task<Article> ReadArticleAsync(string articleId);
    
        public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating);
    
        public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId); 
    }

## <a id="Architecture"></a>Az Azure Cosmos DB-fiók konfigurációja
Helyi olvas és ír, hogy kell particionálni adatok nem csupán partíción kulcs, de is alapján elérni földrajzi területekre. Egy georeplikált Azure Cosmos DB adatbázis-fiókot minden régióhoz, hogy a modell támaszkodik. Például két régióban, az itt látható a telepítés pedig többrégiós írási műveletek számára:

| Fiók neve | Írási régió | Olvasási régió |
| --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |

Az alábbi ábrán látható, hogy olvasási és írási végzett ezzel a beállítással egy tipikus alkalmazásban:

![Az Azure Cosmos DB több főkiszolgálós architektúrája](./media/multi-master-workaround/multi-master.png)

Itt láthat egy kódrészletet, bemutató inicializálni egy futó DAL az ügyfelekhez a `West US` régióban.
    
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

Az előző telepítés az adatelérési réteg továbbíthatja az írásokat a helyi fiókot, ahol központilag telepítették alapján. Olvasási fiókot is az adatok globális felületen való olvasásakor hajtja végre. Ezzel a módszerrel tetszőleges számú régiót szükség szerint kiterjeszthető. Például a következő három földrajzi régióban egy telepítése:

| Fiók neve | Írási régió | Olvasási régió 1 | Olvasási régió 2 |
| --- | --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |`Southeast Asia` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |`Southeast Asia` |
| `contentpubdatabase-asia.documents.azure.com` | `Southeast Asia` |`North Europe` |`West US` |

## <a id="DataAccessImplementation"></a>Adatok hozzáférési réteg végrehajtása
Most nézzük meg az adatelérési réteg (DAL) két írható régiót az alkalmazás végrehajtását. A DAL meg kell valósítani az alábbi lépéseket:

* Több példányának létrehozása `DocumentClient` az egyes fiókok számára. Két új régió a DAL a példányonként van egy `writeClient` és a egy `readClient`. 
* Az alkalmazás az üzembe helyezett régió alapján, a végpontok konfigurálása `writeclient` és `readClient`. Ha például a DAL-ban üzembe helyezett `West US` használ `contentpubdatabase-usa.documents.azure.com` írási műveletek végrehajtásához. A DAL-ban üzembe helyezett `NorthEurope` használ `contentpubdatabase-europ.documents.azure.com` való írásra.

Az előző telepítés adatelérési valósítható meg. Írási műveletek továbbítja az írás a megfelelő `writeClient`.

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

Értesítések és értékelések olvasásához, el kell olvasnia a régiók és a union az eredmények az alábbi kódrészletben látható módon:

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

Így egy jó particionálási kulcsot és a statikus fiók-alapú particionálás kiválasztásával érheti el többrégiós helyi írási és olvasási Azure Cosmos DB használatával.

## <a id="NextSteps"></a>Következő lépések
Ez a cikk azt ismerteti hogyan használhatja a globálisan elosztott többrégiós írható/olvashatóként minták az Azure Cosmos DB használatával tartalom közzététele egy mintaforgatókönyv.

* Ismerje meg hogyan támogatja az Azure Cosmos DB [globális terjesztés](distribute-data-globally.md)
* Ismerje meg [automatikus és manuális feladatátvételt az Azure Cosmos DB-ben](regional-failover.md)
* Ismerje meg [az Azure Cosmos DB globális összhangot](consistency-levels.md)
* Fejlesztés használatával több régióban a [Azure Cosmos DB használatával – az SQL API](tutorial-global-distribution-sql-api.md)
* Fejlesztés használatával több régióban a [Azure Cosmos DB - MongoDB API-val](tutorial-global-distribution-MongoDB.md)
* Fejlesztés használatával több régióban a [Azure Cosmos DB - Table API](tutorial-global-distribution-table.md)
