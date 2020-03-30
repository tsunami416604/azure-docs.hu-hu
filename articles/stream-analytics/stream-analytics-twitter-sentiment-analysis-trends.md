---
title: Valós idejű Twitter-hangulatelemzés az Azure Stream Analytics segítségével
description: Ez a cikk bemutatja, hogyan használhatja a Stream Analytics valós idejű Twitter hangulatelemzés. Lépésről lépésre az eseménygenerálástól az élő irányítópulton lévő adatokig.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 4b265bb574895e4728ad93ee25c9dad0da226ea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240304"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Valós idejű Twitter-hangulatelemzés az Azure Stream Analytics szolgáltatásban

Ez a cikk bemutatja, hogyan hozhat létre közösségi média hangulatelemzési megoldást a valós idejű Twitter-események Azure Event Hubs-ba való bejuttatásával. Azure Stream Analytics-lekérdezést ír az adatok elemzéséhez és az eredmények későbbi felhasználáshoz való tárolásához, vagy hozzon létre egy Power BI-irányítópultot, hogy valós idejű elemzéseket nyújthasson. [Power BI](https://powerbi.com/)

A közösségi média elemzési eszközök segítenek a szervezeteknek megérteni a felkapott témákat. A felkapott témák olyan témák és attitűdök, amelyek nagy mennyiségű bejegyzést tesznek közzé a közösségi médiában. A *véleménybányászatnak*is nevezett hangulatelemzés közösségi média elemzési eszközöket használ a termékkel vagy ötlettel kapcsolatos attitűdök meghatározására. 

A valós idejű Twitter trendelemzés nagyszerű példa egy elemzési eszközre, mivel a hashtag-előfizetési modell lehetővé teszi bizonyos kulcsszavak (hashtagek) meghallgatását és a hírcsatorna hangulatelemzésének fejlesztését.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Forgatókönyv: Közösségi média hangulatelemzés valós időben

A cég, amely a hír média honlapján érdekelt abban, hogy előnyt szerezzen a versenytársak által felvázoló webhely tartalmát, amely azonnal releváns az olvasók. A vállalat a közösségi média elemzését olyan témákban használja, amelyek relevánsak az olvasók számára a Twitter adatok valós idejű hangulatelemzésével.

Ahhoz, hogy valós időben azonosítsa a felkapott témákat a Twitteren, a vállalatnak valós idejű elemzésre van szüksége a tweet mennyiségéről és a legfontosabb témák hangulatáról.

## <a name="prerequisites"></a>Előfeltételek

Ebben az útmutatóútmutatóban olyan ügyfélalkalmazást használ, amely csatlakozik a Twitterhez, és bizonyos hashtagekkel (amelyeket beállíthat) tartalmazó tweeteket keres. Az alkalmazás futtatásához és a tweetek Azure Streaming Analytics használatával történő elemzéséhez a következőkre van szüksége:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/)

* Egy [Twitter](https://twitter.com) fiók.

* A TwitterClientCore alkalmazás, amely beolvassa a Twitter feed. Ahhoz, hogy ezt az alkalmazást, letöltés [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore).

* Telepítse a [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x) 2.1.0-s verzióját.

## <a name="create-an-event-hub-for-streaming-input"></a>Eseményközpont létrehozása a streamelési bemenethez

A mintaalkalmazás eseményeket hoz létre, és leküldéses egy Azure-eseményközpontba. Az Azure Event Hubs a Stream Analytics eseménybetöltésének előnyben részesített módja. További információt az [Azure Event Hubs dokumentációjában](../event-hubs/event-hubs-what-is-event-hubs.md)talál.

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Eseményközpont névtere és eseményközpont létrehozása
Ebben a szakaszban hozzon létre egy eseményközpont névtafőt, és adjon hozzá egy eseményközpontot a névtérhez. Az eseményközpont-névterek a kapcsolódó eseménybusz-példányok logikai csoportosítására szolgálnak. 

1. Jelentkezzen be az Azure Portalra, és válassza **az Erőforrás létrehozása lehetőséget.** Akkor. keresse meg az **Eseményközpontokat,** és válassza a **Létrehozás lehetőséget.**

2. A **Névtér létrehozása** lapon adjon meg egy névtérnevet. A névtérhez bármilyen nevet használhat, de a névnek érvényesnek kell lennie egy URL-címhez, és egyedinek kell lennie az Azure-ban. 
    
3. Válasszon ki egy tarifacsomagot és előfizetést, és hozzon létre vagy válasszon ki egy erőforráscsoportot. Ezután válasszon egy helyet, és válassza a **Létrehozás gombot.** 
 
4. Amikor a névtér telepítése befejeződött, keresse meg az erőforráscsoportot, és keresse meg az eseményközpont névterét az Azure-erőforrások listájában. 

5. Az új névtérben válassza az ** + &nbsp;Event Hub**lehetőséget. 

6. Nevezze el az új esemény központ *socialtwitter-eh*. Más nevet is használhat. Ha igen, jegyezze fel, mert később szüksége lesz a névre. Az eseményközponthoz nem kell más beállításokat beállítania.
 
7. Kattintson a **Létrehozás** gombra.

### <a name="grant-access-to-the-event-hub"></a>Hozzáférés megadása az eseményközponthoz

Mielőtt egy folyamat adatokat küldhetne egy eseményközpontba, az eseményközpontnak olyan házirendre van szüksége, amely lehetővé teszi a hozzáférést. A hozzáférési szabályzat egy kapcsolati sztringet hoz létre, amelyben megtalálhatók az engedélyezési információk.

1.  Az eseményközpontok névterének bal oldalán található navigációs sávon válassza az **Eseményközpontok**lehetőséget, amely az **Entitások** szakaszban található. Ezután válassza ki az imént létrehozott eseményközpontot.

2.  A bal oldali navigációs sávon válassza a Beállítások csoportban található **Megosztott hozzáférési házirendek** **lehetőséget.**

    >[!NOTE]
    >Van egy megosztott hozzáférési szabályzatok beállítás alatt az eseményközpont névtér és az eseményközpont. Győződjön meg arról, hogy az eseményközpont környezetében dolgozik, nem a teljes eseményközpont névterében.

3.  A hozzáférési házirend lapon válassza a **+ Hozzáadás lehetőséget.** Ezután adja meg *socialtwitter-hozzáférés* a **politika nevét,** és jelölje be a **Kezelés** jelölőnégyzetet.
 
4.  Kattintson a **Létrehozás** gombra.

5.  A házirend üzembe helyezése után válassza ki a házirendet a megosztott hozzáférési házirendek listájából.

6.  Keresse meg a **Kapcsolatkarakterlánc elsődleges kulcsfeliratú** mezőt, és válassza a kapcsolati karakterlánc melletti másolásgombot.
 
7.  Illessze be a kapcsolati sztringet egy szövegszerkesztőbe. Szüksége van erre a kapcsolati karakterláncra a következő szakaszhoz, miután néhány kisebb szerkesztést végzett.

   A kapcsolati karakterlánc így néz ki:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   Figyelje meg, hogy a kapcsolati karakterlánc több kulcs-érték `Endpoint` `SharedAccessKeyName`párt `SharedAccessKey`tartalmaz, pontosvesszővel elválasztva: , , , és `EntityPath`.  

   > [!NOTE]
   > A biztonság érdekében a példában lévő kapcsolati karakterlánc egyes részeit eltávolították.

## <a name="configure-and-start-the-twitter-client-application"></a>A Twitter-ügyfélalkalmazás konfigurálása és indítása

Az ügyfélalkalmazás kap csipog események közvetlenül a Twitter. Ehhez engedélyt kell kérnie a Twitter streaming API-k hívásához. Az engedély konfigurálásához hozzon létre egy alkalmazást a Twitteren, amely egyedi hitelesítő adatokat (például OAuth-jogkivonatot) hoz létre. Ezután konfigurálhatja az ügyfélalkalmazást, hogy használja ezeket a hitelesítő adatokat, amikor API-hívásokat kezdeményez. 

### <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása
Ha még nem rendelkezik Twitter alkalmazással, amelyet használhat ehhez az útmutatóhoz, létrehozhat egyet. Már rendelkezik Twitter-fiókkal.

> [!NOTE]
> A pontos folyamat a Twitter egy alkalmazás létrehozásához, és a kulcsok, titkos kulcsok és token lehet változtatni. Ha ezek az utasítások nem egyeznek meg a Twitter-webhelyen látottakkal, olvassa el a Twitter fejlesztői dokumentációját.

1. Webböngészőből nyissa meg a [Twitter for Developers (Fejlesztőknek)](https://developer.twitter.com/en/apps)webhelyet, hozzon létre fejlesztői fiókot, és válassza **az Alkalmazás létrehozása**lehetőséget. Előfordulhat, hogy megjelenik egy üzenet, amely szerint egy Twitter-fejlesztői fiókra kell jelentkeznie. Nyugodtan tegye meg, és miután a kérelmet jóváhagyták, látnia kell egy visszaigazoló e-mailt. A fejlesztői fiók jóváhagyása több napig is eltarthat.

   ![Twitter alkalmazás részletei](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Twitter alkalmazás részletei")

2. Az **Alkalmazás létrehozása** oldalon adja meg az új alkalmazás adatait, majd válassza a **Twitter-alkalmazás létrehozása** parancsot.

   ![Twitter alkalmazás részletei](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Twitter alkalmazás részletei")

3. Az alkalmazáslapon válassza a **Kulcsok és jogkivonatok** lapot, és másolja a **Fogyasztói API-kulcs** és a **fogyasztói API titkos kulcs értékeit.** A hozzáférési jogkivonat és a **hozzáférési jogkivonat titkos** kulcsa csoportban válassza a **Create(Create)** lehetőséget is a hozzáférési jogkivonatok létrehozásához. Másolja a **Hozzáférési token** és a **Hozzáférési token titkos kulcsa** mező értékeit.

   Mentse a Twitter-alkalmazáshoz lekért értékeket. Később szükséged van az értékekre.

> [!NOTE]
> A kulcsok és titkok a Twitter alkalmazás hozzáférést biztosít a Twitter számla. Kezelje ezeket az információkat bizalmasan, ugyanúgy, mint a Twitter jelszavát. Például ne ágyazza be ezt az információt egy másoknak megadott alkalmazásba. 

### <a name="configure-the-client-application"></a>Az ügyfélalkalmazás konfigurálása

Létrehoztunk egy kliensalkalmazást, amely a [Twitter streaming API-jainak](https://dev.twitter.com/streaming/overview) használatával csatlakozik a Twitter adataihoz, hogy tweeteseményeket gyűjtsön egy adott témakörkészletről.

Az alkalmazás futtatása előtt bizonyos információkat igényel Öntől, például a Twitter-kulcsokat és az eseményközpont-kapcsolati karakterláncot.

1. Győződjön meg arról, hogy letöltötte a [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) alkalmazást, az előfeltételekben felsoroltak szerint.

2. Az *App.config* fájl megnyitásához használjon szövegszerkesztőt. Hajtsa végre `<appSettings>` a következő módosításokat az elemen:

   * Állítsa `oauth_consumer_key` a Twitter fogyasztói kulcs (API-kulcs). 
   * Állítsa `oauth_consumer_secret` be a Twitter Consumer Secret (API titkos kulcs).
   * Állítsa `oauth_token` a Twitter Access token.
   * Állítsa `oauth_token_secret` be a Twitter Access token titkos.
   * Állítsa `EventHubNameConnectionString` a kapcsolati karakterláncra.
   * Állítsa `EventHubName` be az eseményközpont nevét (ez az entitás elérési útjának értéke).

3. Nyissa meg a parancssort, és keresse meg azt a könyvtárat, ahol a TwitterClientCore alkalmazás található. A projekt `dotnet build` létrehozásához használja a parancsot. Ezután a `dotnet run` paranccsal futtassa az alkalmazást. Az alkalmazás tweeteket küld az Eseményközpontba.

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

Most, hogy a tweetesemények valós időben streamelhetők a Twitterről, beállíthat egy Stream Analytics-feladatot, hogy valós időben elemezze ezeket az eseményeket.

1. Az Azure Portalon keresse meg az erőforráscsoportot, és válassza a **+ Hozzáadás lehetőséget.** Ezután keressen rá a **Stream Analytics feladatra,** és válassza a **Létrehozás lehetőséget.**

2. Nevezze el `socialtwitter-sa-job` a feladatot, és adjon meg egy előfizetést, erőforráscsoportot és helyet.

    Érdemes a feladatot és az eseményközpontot ugyanabban a régióban elhelyezni a legjobb teljesítmény érdekében, hogy ne fizessen az adatok régiók közötti átviteléért.

3. Kattintson a **Létrehozás** gombra. Ezután keresse meg a feladatot, amikor a központi telepítés befejeződött.

## <a name="specify-the-job-input"></a>A feladat bemenetének megadása

1. A Stream Analytics-feladatban válassza a Bal oldali menü **Bemenetek parancsát** a **Feladattopológia**csoportban.

2. Válassza ** + &nbsp;az Adatfolyam-beviteli eseményközpont** > hozzáadása**lehetőséget.** Töltse ki az **Új beviteli** űrlapot a következő információkkal:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Bemeneti alias| *TwitterStream* | Adja meg a bemenet aliasát. |
   |Előfizetés  | \<Az Ön előfizetése\> |  Válassza ki a használni kívánt Azure-előfizetést. |
   |Event Hubs-névtér | *asa-twitter-eventhub* |
   |Eseményközpont neve | *socialtwitter-eh* | Válassza *a Meglévő használata*lehetőséget. Ezután válassza ki a létrehozott Eseményközpontot.|
   |Eseménytömörítés típusa| Gzip | Az adattömörítés típusa.|

   Hagyja meg a fennmaradó alapértelmezett értékeket, és válassza a **Mentés lehetőséget.**

## <a name="specify-the-job-query"></a>A feladatlekérdezés megadása

A Stream Analytics egy egyszerű, deklaratív lekérdezési modellt támogat, amely az átalakításokat írja le. Ha többet szeretne megtudni a nyelvről, olvassa el az [Azure Stream Analytics lekérdezési nyelvének útmutatója című témakört.](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) Ez az útmutató segít több lekérdezés tanusítja és teszteli a Twitter-adatokat.

Az említések számának összehasonlításához a témakörök között egy [Tumbling ablaksegítségével](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) öt másodpercenként megkaphatja az említések számát.

1. A feladat **áttekintése**párbeszédpanelen válassza a **Lekérdezés szerkesztése** lehetőséget a Lekérdezés mező jobb felső sarokja mellett. Az Azure felsorolja a bemenetek és kimenetek, amelyek a feladathoz konfigurálva vannak, és lehetővé teszi, hogy hozzon létre egy lekérdezést a bemeneti adatfolyam átalakításához, ahogy az a kimenetre.

2. Módosítsa a lekérdezésszerkesztőlekérdezését a következőre:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. Az üzenetekből származó eseményadatoknak meg kell jelenniük a lekérdezés alatti **Input előnézeti** ablakban. Győződjön meg **arról,** hogy a nézet **JSON-ra**van állítva. Ha nem lát adatokat, győződjön meg arról, hogy az adatgenerátor eseményeket küld az eseményközpontba, és hogy a **GZip-et** választotta a bemenet tömörítési típusaként.

4. Válassza **a Lekérdezés tesztelése** lehetőséget, és figyelje meg az eredményeket a Lekérdezés alatti **Teszteredmények** ablakban.

5. Módosítsa a lekérdezést a kódszerkesztőben a következőre, és válassza a **Lekérdezés tesztelése**lehetőséget:

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. Ez a lekérdezés az Azure kulcsszót tartalmazó összes *tweetet*visszaadja.

## <a name="create-an-output-sink"></a>Kimeneti fogadó létrehozása

Most már definiált egy eseményfolyamot, egy eseményközpont-bemenetet az események betöltéséhez, és egy lekérdezést az adatfolyamon keresztültörténő átalakításhoz. Az utolsó lépés egy kimeneti fogadó definiálása a feladathoz.  

Ebben az útmutatóútmutatóban az összesített tweeteseményeket a feladatlekérdezésből az Azure Blob storage-ba írja.  Az eredményeket az Azure SQL Database, az Azure Table storage, az Event Hubs vagy a Power BI is leküldéses, az alkalmazás igényeitől függően.

## <a name="specify-the-job-output"></a>A feladat kimenetének megadása

1. A bal oldali navigációs menü **Feladattopológia** szakaszában válassza a **Kimenetek**lehetőséget. 

2. A **Kimenetek** lapon kattintson a ** + &nbsp;Hozzáadás** és **blobtároló/Data Lake Storage Gen2**elemre:

   * **Kimeneti alias**: `TwitterStream-Output`Használja a nevet. 
   * **Importálási beállítások:** Válassza **ki a Tárhely kiválasztása lehetőséget az előfizetések közül**.
   * **Tárfiók**. Válassza ki a tárfiókot.
   * **Konténer .** Válassza az Új `socialtwitter`létrehozása **és** a belépés lehetőséget.
   
4. Kattintson a **Mentés** gombra.   

## <a name="start-the-job"></a>A feladat indítása

A feladat bemenet, lekérdezés és kimenet van megadva. Készen áll a Stream Analytics feladat elindítására.

1. Ellenőrizze, hogy fut-e a TwitterClientCore alkalmazás. 

2. A feladat áttekintésében válassza a **Start**lehetőséget.

3. A **Feladat indítása** lapon a **Feladat kimeneti kezdési időpontja**lapon válassza **a Now** ( Most , majd a Start ) **lehetőséget.**

## <a name="get-support"></a>Támogatás kérése
További segítségért próbálja ki [az Azure Stream Analytics fórumunkat.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
