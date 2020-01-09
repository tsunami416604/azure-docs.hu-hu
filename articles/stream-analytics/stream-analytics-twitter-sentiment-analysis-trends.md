---
title: A Twitter hangulatának valós idejű elemzése Azure Stream Analytics
description: Ez a cikk azt ismerteti, hogyan használható a Stream Analytics a valós idejű Twitter-hangulat elemzéséhez. Lépésenkénti útmutató az események generálásához az élő irányítópulton lévő adatokhoz.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: f3ab21d55b7d59bb58760bfba452b4ea2d103496
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369898"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>A Twitter hangulatának valós idejű elemzése Azure Stream Analytics

Megtudhatja, hogyan hozhat létre a közösségi média elemzésére szolgáló hangulat-elemzési megoldást azáltal, hogy valós idejű Twitter-eseményeket helyez el az Azure Event Hubsba. Ezután írjon egy Azure Stream Analytics lekérdezést, hogy elemezze az adatelemzést, és tárolja az eredményeket későbbi használatra, vagy hozzon létre egy [Power bi](https://powerbi.com/) irányítópultot, hogy valós időben szolgáltasson betekintést.

A közösségi média elemzési eszközei segítenek a szervezeteknek a trendek megismerésében. A Kiemelt témakörök olyan témák és hozzáállások, amelyek nagy mennyiségű bejegyzéssel rendelkeznek a közösségi médiában. A vélemény-és *adatbányászati*elemzések, valamint a közösségi média-elemzési eszközök segítségével határozzák meg a termékre vagy ötletre vonatkozó hozzáállást. 

A valós idejű Twitter trend elemzése nagyszerű példa egy elemzési eszközre, mert a hashtag-előfizetési modell lehetővé teszi, hogy bizonyos kulcsszavakat (hashtageket) Hallgasson meg, és fejlessze a hírcsatornát.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Forgatókönyv: a közösségi média hangulatának elemzése valós időben

A legfrissebb hírekkel rendelkező vállalatok számára hasznos, hogy az olvasók számára azonnal releváns webhely-tartalmakat szerezzenek a versenytársakkal szemben. A vállalat a közösségi média-elemzést olyan témakörökre használja, amelyek az olvasóknak a Twitter-adatokat valós időben elemezve használják.

A Twitteren valós időben azonosított trendek felismeréséhez a vállalatnak valós idejű elemzésre van szüksége a tweetek mennyiségéről és a legfontosabb témakörökről.

## <a name="prerequisites"></a>Előfeltételek
Ebben a útmutatóban egy, a Twitterhez csatlakozó ügyfélalkalmazás és olyan tweetek megkeresi, amelyek bizonyos hashtagekkel rendelkeznek (amelyet beállíthat). Az alkalmazás futtatásához és a tweetek Azure streaming Analytics használatával történő elemzéséhez a következőkre van szükség:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Egy [Twitter](https://twitter.com) -fiók.
* A TwitterWPFClient alkalmazás, amely beolvassa a Twitter-hírcsatornát. Az alkalmazás beszerzéséhez töltse le a [TwitterWPFClient. zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) fájlt a githubról, majd csomagolja ki a csomagot a számítógép egyik mappájába. Ha szeretné megtekinteni a forráskódot, és futtatni az alkalmazást egy hibakeresőben, a forráskódot a [githubról](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TwitterClient)szerezheti be. 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Event hub létrehozása a streaming Analytics bemenetéhez

A minta alkalmazás eseményeket hoz létre, és leküldi őket egy Azure Event hub-ba. Az Azure Event hubok előnyben részesített módja az események betöltésének a Stream Analytics. További információkért tekintse meg az [Azure Event Hubs dokumentációját](../event-hubs/event-hubs-what-is-event-hubs.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Event hub-névtér és Event hub létrehozása
Hozzon létre egy Event hub-névteret, majd adjon hozzá egy Event hub-t a névtérhez. Az Event hub-névterek a kapcsolódó Event Bus-példányok logikai csoportosítására szolgálnak. 

1. Jelentkezzen be a Azure Portalba, és kattintson az **erőforrás létrehozása** > **eszközök internetes hálózata** > **Event hub**elemre. 

2. A **névtér létrehozása** panelen adja meg a névtér nevét (például `<yourname>-socialtwitter-eh-ns`). Bármilyen nevet használhat a névtérhez, de a névnek érvényesnek kell lennie az URL-címhez, és egyedinek kell lennie az Azure-ban. 
    
3. Válasszon ki egy előfizetést, hozzon létre vagy válasszon ki egy erőforráscsoportot, majd kattintson a **Létrehozás**gombra. 

    ![Eseményközpont-névtér létrehozása](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. Ha a névtér befejezte a telepítést, keresse meg az Event hub-névteret az Azure-erőforrások listájában. 

5. Kattintson az új névtérre, majd a névtér panelen kattintson az **+&nbsp;Event hub**elemre. 

    ![Az Event hub hozzáadása gomb új Event hub létrehozásához](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Nevezze el az új Event hub `socialtwitter-eh`. Más nevet is használhat. Ha így tesz, jegyezze fel, mert később szüksége lesz erre a névre. Az Event hub egyéb beállításait nem kell beállítania.

    ![Új Event hub létrehozásának panelje](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Kattintson a **Create** (Létrehozás) gombra.


### <a name="grant-access-to-the-event-hub"></a>Hozzáférés biztosítása az Event hub számára

Ahhoz, hogy egy folyamat hozzáférhessen az adatközponthoz, az Event hub-nak rendelkeznie kell egy olyan házirenddel, amely lehetővé teszi a megfelelő hozzáférést. A hozzáférési szabályzat egy kapcsolati sztringet hoz létre, amelyben megtalálhatók az engedélyezési információk.

1.  Az esemény-névtér panelen kattintson a **Event Hubs** elemre, majd kattintson az új Event hub nevére.

2.  Az Event hub panelen kattintson a **megosztott elérési házirendek** elemre, majd kattintson a **+&nbsp;Hozzáadás**elemre.

    >[!NOTE]
    >Győződjön meg arról, hogy az Event hub-t használja, nem az Event hub-névteret.

3.  Vegyen fel egy `socialtwitter-access` nevű szabályzatot, és a **jogcím**beállításnál válassza a **kezelés**lehetőséget.

    ![Új Event hub hozzáférési szabályzat létrehozásának panelje](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Kattintson a **Create** (Létrehozás) gombra.

5.  Miután telepítette a házirendet, kattintson rá a megosztott hozzáférési házirendek listájában.

6.  Keresse meg a **kapcsolódási karakterlánc – elsődleges kulcs** jelölőnégyzetet, és kattintson a kapcsolódási karakterlánc melletti Másolás gombra. 
    
    ![Az elsődleges kapcsolati sztring kulcsának másolása a hozzáférési házirendből](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Illessze be a kapcsolati sztringet egy szövegszerkesztőbe. A következő szakaszhoz szükség van erre a kapcsolódási sztringre, miután elvégezte néhány kis módosítást.

    A következőhöz hasonló a kapcsolatok karakterlánca:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Figyelje meg, hogy a kapcsolatok karakterlánca több kulcs-érték párokat tartalmaz, pontosvesszővel elválasztva: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`és `EntityPath`.  

    > [!NOTE]
    > A biztonság érdekében a példában szereplő, a kapcsolatok karakterláncának egyes részei el lettek távolítva.

8.  A szövegszerkesztőben távolítsa el a `EntityPath` pár elemet a kapcsolódási karakterláncból (ne felejtse el eltávolítani a pontosvesszőt, amely megelőzi azt). Ha elkészült, a kapcsolódási sztring a következőképpen néz ki:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>A Twitter-ügyfélalkalmazás konfigurálása és elindítása
Az ügyfélalkalmazás közvetlenül a Twitterről szerzi be a tweetek eseményeit. Ennek érdekében engedélyre van szüksége a Twitter streaming API-k meghívásához. Az engedély konfigurálásához létre kell hoznia egy alkalmazást a Twitteren, amely egyedi hitelesítő adatokat (például OAuth tokent) generál. Ezután beállíthatja, hogy az ügyfélalkalmazás ezeket a hitelesítő adatokat használja, amikor API-hívásokat végez. 

### <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása
Ha még nem rendelkezik olyan Twitter-alkalmazással, amelyet ehhez a útmutatóhoz használhat, létrehozhat egyet. Már rendelkeznie kell egy Twitter-fiókkal.

> [!NOTE]
> Az alkalmazások létrehozásához és a kulcsok, titkok és tokenek beszerzéséhez szükséges pontos folyamat a Twitteren változhat. Ha ezek az utasítások nem egyeznek meg a Twitter webhelyén láthatókkal, tekintse meg a Twitter fejlesztői dokumentációját.

1. A böngészőben nyissa meg a [Twitter fejlesztőket](https://developer.twitter.com/en/apps), és válassza **az alkalmazás létrehozása**lehetőséget. Előfordulhat, hogy megjelenik egy üzenet, amely azt jelzi, hogy egy Twitter fejlesztői fiókra van szüksége. Nyugodtan megteheti, és az alkalmazás jóváhagyását követően meg kell jelennie egy megerősítő e-mailben. Több napot is igénybe vehet egy fejlesztői fiók jóváhagyása.

   ![Twitter fejlesztői fiók megerősítése](./media/stream-analytics-twitter-sentiment-analysis-trends/twitter-dev-confirmation.png "Twitter fejlesztői fiók megerősítése")

   ![Twitter-alkalmazás részletei](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Twitter-alkalmazás részletei")

2. Az **Alkalmazás létrehozása** oldalon adja meg az új alkalmazás adatait, majd válassza a **Twitter-alkalmazás létrehozása** parancsot.

   ![Twitter-alkalmazás részletei](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Twitter-alkalmazás részletei")

3. Az alkalmazás lapon válassza a **kulcsok és tokenek** fület, és másolja a **fogyasztói API-kulcs** és a **fogyasztói API titkos kulcsa**értékeit. A hozzáférési tokenek létrehozásához a hozzáférési jogkivonat **és a hozzáférési jogkivonat titka** területen válassza a **Létrehozás** lehetőséget. Másolja a **Hozzáférési token** és a **Hozzáférési token titkos kulcsa** mező értékeit.

    ![Twitter-alkalmazás részletei](./media/stream-analytics-twitter-sentiment-analysis-trends/twitter-app-key-secret.png "Twitter-alkalmazás részletei")

Mentse a Twitter-alkalmazáshoz lekért értékeket. Az értékeket a útmutatóban később kell megadnia.

>[!NOTE]
>A Twitter-alkalmazás kulcsai és titkai biztosítják a Twitter-fiók elérését. Ezeket az adatokat bizalmasként kell kezelni, ugyanúgy, mint a Twitter-jelszót. Például ne ágyazza be ezeket az adatokat egy olyan alkalmazásba, amelyet másoknak adnak meg. 


### <a name="configure-the-client-application"></a>Az ügyfélalkalmazás konfigurálása
Létrehoztunk egy olyan ügyfélalkalmazás-alkalmazást, amely Twitter-adatokhoz csatlakozik a [Twitter streaming API](https://dev.twitter.com/streaming/overview) -kkal a témakörök adott készletével kapcsolatos Tweet-események összegyűjtéséhez. Az alkalmazás a [Sentiment140](http://help.sentiment140.com/) nyílt forráskódú eszközt használja, amely a következő érzelmi értéket rendeli hozzá az egyes tweetekhez:

* 0 = negatív
* 2 = semleges
* 4 = pozitív

Miután a tweet-események hozzá lettek rendelve egy hangulati értékhez, a rendszer leküldi azokat a korábban létrehozott Event hubhoz.

Az alkalmazás futtatása előtt szükség van bizonyos információkra, például a Twitter-kulcsokra és az Event hub kapcsolati karakterláncára. A konfigurációs adatokat az alábbi módokon adhatja meg:

* Futtassa az alkalmazást, majd az alkalmazás felhasználói felületén adja meg a kulcsokat, a titkokat és a kapcsolódási karakterláncot. Ha ezt teszi, a rendszer a konfigurációs adatokat használja az aktuális munkamenethez, de nem menti azt.
* Szerkessze az alkalmazás. config fájlját, és adja meg az értékeket. Ez a megközelítés megőrzi a konfigurációs információkat, de ez azt is jelenti, hogy ez a potenciálisan bizalmas információ tárolása egyszerű szövegként történik a számítógépen.

Az alábbi eljárás mindkét megközelítést dokumentálja. 

1. Győződjön meg róla, hogy letöltötte és kicsomagolta a [TwitterWPFClient. zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) alkalmazást az előfeltételek szakaszban felsoroltak szerint.

2. Ha a futási időben szeretné beállítani az értékeket (és csak az aktuális munkamenet esetében), futtassa a `TwitterWPFClient.exe` alkalmazást. Amikor az alkalmazás kéri, adja meg a következő értékeket:

    * A Twitter fogyasztói kulcs (API-kulcs).
    * A Twitter Consumer Secret (API Secret).
    * A Twitter hozzáférési jogkivonata.
    * A Twitter hozzáférési token titka.
    * A korábban mentett kapcsolatok karakterláncának adatai. Győződjön meg arról, hogy a `EntityPath` kulcs-érték párokat eltávolító kapcsolódási karakterláncot használja.
    * A Twitter-kulcsszavak, amelyeknek a véleményét meg kívánja határozni.

   ![TwitterWpfClient-alkalmazás fut, amely rejtett beállításokat mutat be](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Az értékek állandó beállításához egy szövegszerkesztővel nyissa meg a TwitterWpfClient. exe. config fájlt. Ezután a `<appSettings>` elemben tegye a következőket:

   * `oauth_consumer_key` beállítása a Twitter fogyasztói kulcsához (API-kulcs). 
   * `oauth_consumer_secret` beállítása a Twitter Consumer Secret (API Secret) értékre.
   * `oauth_token` beállítása a Twitter hozzáférési jogkivonatra.
   * `oauth_token_secret` beállítása a Twitter hozzáférési token titkára.

     A `<appSettings>` elem későbbi részében hajtsa végre a következő módosításokat:

   * Állítsa `EventHubName` az Event hub nevére (vagyis az entitás elérési útjának értékére).
   * `EventHubNameConnectionString` beállítása a kapcsolódási karakterláncra. Győződjön meg arról, hogy a `EntityPath` kulcs-érték párokat eltávolító kapcsolódási karakterláncot használja.

     A `<appSettings>` szakasz a következő példához hasonlít. (Az egyértelműség és a biztonság érdekében néhány sort becsomagoltunk, és eltávolítunk néhány karaktert.)

     ![TwitterWpfClient az alkalmazás konfigurációs fájlját egy szövegszerkesztőben, amely megjeleníti a Twitter-kulcsokat és titkos kódokat, valamint az Event hub kapcsolati karakterláncának adatait](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Ha még nem indította el az alkalmazást, futtassa most a TwitterWpfClient. exe fájlt. 

5. A közösségi hangulat összegyűjtéséhez kattintson a zöld Start gombra. A tweet-események a **CreatedAt**, a **témakörben**és a **SentimentScore** megadott értékekkel jelennek meg az Event hub-ban.

    ![TwitterWpfClient-alkalmazás fut, amely a tweetek listáját jeleníti meg](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Ha hibákat lát, és nem látja az ablak alsó részén megjelenő tweeteket, ellenőrizze a kulcsokat és a titkos kulcsokat. Ellenőrizze a kapcsolatok karakterláncát is (ellenőrizze, hogy nem tartalmazza-e a `EntityPath` kulcsot és értéket.)


## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

Most, hogy a tweet-események valós időben áramlanak a Twitterről, beállíthat egy Stream Analytics feladatot, amellyel valós időben elemezheti ezeket az eseményeket.

1. A Azure Portal kattintson az **erőforrás létrehozása** > **eszközök internetes hálózata** > **stream Analytics feladatokra**.

2. Nevezze el a feladatot `socialtwitter-sa-job`, és adjon meg egy előfizetést, erőforráscsoportot és helyet.

    Érdemes a feladatot és az Event hub-t ugyanabban a régióban elhelyezni a legjobb teljesítmény érdekében, és így nem kell fizetnie a régiók közötti adatátvitel során.

    ![Új Stream Analytics-feladatok létrehozása](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Kattintson a **Create** (Létrehozás) gombra.

    A rendszer létrehozza a feladatot, és a portál megjeleníti a feladatok részleteit.


## <a name="specify-the-job-input"></a>Adja meg a feladatok bemenetét

1. A Stream Analytics-feladatban a feladatok panel közepén található **feladatok topológiája** alatt kattintson a **bemenetek**elemre. 

2. A **bemenetek** panelen kattintson a **+&nbsp;Hozzáadás** elemre, majd töltse ki a panelt a következő értékekkel:

   * **Bemeneti alias**: használja a `TwitterStream`nevet. Ha más nevet használ, jegyezze fel, mert később szüksége lesz rá.
   * **Forrás típusa**: válassza **az adatfolyam**lehetőséget.
   * **Forrás**: válassza az **Event hub**elemet.
   * **Importálási lehetőség**: válassza az **Event hub használata a jelenlegi előfizetésből**lehetőséget. 
   * **Service Bus-névtér**: válassza ki a korábban létrehozott Event hub-névteret (`<yourname>-socialtwitter-eh-ns`).
   * **Event hub**: válassza ki a korábban létrehozott Event hub-t (`socialtwitter-eh`).
   * **Event hub-házirend neve**: válassza ki a korábban létrehozott hozzáférési szabályzatot (`socialtwitter-access`).

     ![Új bemenet létrehozása a streaming Analytics-feladathoz](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Kattintson a **Create** (Létrehozás) gombra.


## <a name="specify-the-job-query"></a>A feladatok lekérdezésének meghatározása

A Stream Analytics egy egyszerű, deklaratív lekérdezési modellt támogat, amely leírja a transzformációkat. Ha többet szeretne megtudni a nyelvről, tekintse meg a [Azure stream Analytics lekérdezés nyelvi referenciáját](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).  Ez a útmutató segítséget nyújt a Twitter-alapú adatlekérdezések létrehozásához és teszteléséhez.

Ha össze szeretné hasonlítani a témakörök közötti említések számát, egy leválasztó ablak használatával lekérheti a témakörben szereplő, öt másodpercenként [megjelenő](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) megjegyzések számát.

1. Ha még nem tette meg, zárjuk be a **bemenetek** panelt.

2. Az **Áttekintés** panelen kattintson a lekérdezés **szerkesztése** lehetőségre a lekérdezési mező jobb felső sarkában. Az Azure felsorolja a feladathoz konfigurált bemeneteket és kimeneteket, és lehetővé teszi egy olyan lekérdezés létrehozását, amely lehetővé teszi a bemeneti adatfolyam átalakítását, ahogy az a kimenetre lesz küldve.

3. Győződjön meg arról, hogy a TwitterWpfClient alkalmazás fut. 

3. A **lekérdezés** panelen kattintson a `TwitterStream` bemenet melletti pontokra, majd válassza a **mintaadatok a bemenetből**lehetőséget.

    ![A streaming Analytics-feladathoz tartozó mintaadatok használatára szolgáló menüpontok, a "mintaadatok a bemenetből" lehetőséggel](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Ekkor megnyílik egy panel, amely lehetővé teszi a beolvasott mintaadatok mennyiségének megadását, meghatározva, hogy mennyi ideig kell beolvasni a bemeneti adatfolyamot.

4. Állítsa be a **percet** 3 értékre, majd kattintson **az OK**gombra. 
    
    ![A bemeneti adatfolyam mintavételezésének lehetőségei, 3 perc kiválasztásával.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Az Azure 3 perces adatokat ad a bemeneti adatfolyamból, és értesítést küld, ha a mintaadatok készen állnak. (Ez rövid ideig tart.) 

    A rendszer ideiglenesen tárolja a mintaadatokat, amelyek akkor érhetők el, amikor meg van nyitva a lekérdezési ablak. Ha bezárta a lekérdezési ablakot, a mintaadatok elvesznek, és létre kell hoznia egy új mintaadatok-készletet. 

5. Módosítsa a lekérdezést a Kódszerkesztő alkalmazásban a következőre:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Ha nem a bemeneti aliasként használja a `TwitterStream`, a lekérdezésben helyettesítse be a `TwitterStream` aliasnevét.  

    Ez a lekérdezés a **timestamp by** kulcsszó használatával határozza meg az időbeli számításban használni kívánt adattartalomhoz tartozó timestamp mezőt. Ha ez a mező nincs megadva, a rendszer az ablakkezelő műveletet a következő időpontban hajtja végre, amikor az egyes események megérkeztek az Event hub-ba. További információ: [stream Analytics lekérdezési útmutató](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)"érkezési idő vs alkalmazás ideje" szakaszában.

    A lekérdezés a **System. timestamp** tulajdonság használatával is hozzáfér az egyes ablakok végének időbélyegéhez.

5. Kattintson a **teszt**gombra. A lekérdezés a mintavételen átadott adathalmazon fut.
    
6. Kattintson a **Mentés** gombra. Ezzel menti a lekérdezést a streaming Analytics-feladatok részeként. (Nem menti a mintaadatok mentését.)


## <a name="experiment-using-different-fields-from-the-stream"></a>Kísérlet a stream különböző mezőinek használatával 

A következő táblázat felsorolja a JSON-adatfolyamban tárolt adat részét képező mezőket. A lekérdezés-szerkesztőben nyugodtan kísérletezhet.

|JSON-tulajdonság | Meghatározás|
|--- | ---|
|CreatedAt (Létrehozás ideje) | A tweet létrehozásának időpontja|
|Témakör | A megadott kulcsszóval egyező témakör|
|SentimentScore | Az Sentiment140 származó hangulati pontszám|
|Szerző | A tweetet küldő Twitter-leíró|
|Szöveg | A tweet teljes törzse|


## <a name="create-an-output-sink"></a>Kimeneti fogadó létrehozása

Már definiált egy esemény-adatfolyamot, egy Event hub-bemenetet az események betöltéséhez, valamint egy lekérdezést, amely az adatfolyamon végzett átalakítást végzi. Az utolsó lépés a feladatokhoz tartozó kimeneti fogadó definiálása.  

Ebben a útmutatóban az összesített Tweet-eseményeket a feladatsorból az Azure Blob Storage-ba írhatja.  Az eredményeket az alkalmazás igényeitől függően az Azure SQL Database, az Azure Table Storage, a Event Hubs vagy a Power BI használatával is leküldheti.

## <a name="specify-the-job-output"></a>Adja meg a feladatok kimenetét

1. A **feladatok topológiája** szakaszban kattintson a **kimenet** mezőre. 

2. A **kimenetek** panelen kattintson a **+&nbsp;Hozzáadás** elemre, majd töltse ki a panelt a következő értékekkel:

   * **Kimeneti alias**: használja a `TwitterStream-Output`nevet. 
   * Fogadó **: válassza**a **blob Storage**elemet.
   * **Importálási beállítások**: válassza **a blob Storage használata a jelenlegi előfizetésből**lehetőséget.
   * **Storage-fiók**. Válassza **az új Storage-fiók létrehozása lehetőséget.**
   * **Storage-fiók** (második mező). Adja meg `YOURNAMEsa`, ahol `YOURNAME` a neve vagy egy másik egyedi karakterlánc. A név csak kisbetűket és számokat használhat, és egyedinek kell lennie az Azure-ban. 
   * **Tároló**. Írja be a `socialtwitter` (igen) kifejezést.
     A Storage-fiók neve és a tároló neve együtt használható a blob Storage URI-azonosítójának megadásához, például: 

     `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
     !["Új kimenet" panel Stream Analytics feladatokhoz](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Kattintson a **Create** (Létrehozás) gombra. 

    Az Azure létrehozza a Storage-fiókot, és automatikusan generálja a kulcsot. 

5. Zárjuk be a **kimenetek** panelt. 


## <a name="start-the-job"></a>A feladat indítása

Meg van adva a feladatok bemenete, lekérdezése és kimenete. Készen áll arra, hogy elindítsa a Stream Analytics feladatot.

1. Győződjön meg arról, hogy a TwitterWpfClient alkalmazás fut. 

2. A feladatok panelen kattintson a **Start**gombra.

    ![A Stream Analytics-feladatok elindítása](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. A **kezdési feladatok** panelen a **feladatok kimenetének kezdési idejéhez**válassza a **most** lehetőséget, majd kattintson az **Indítás**gombra. 

    ![A Stream Analyticsi feladatokhoz tartozó "Start Job" panel](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    Az Azure értesíti Önt, ha elindította a feladatot, és a feladatok paneljén **fut**állapot jelenik meg.

    ![Futó művelet](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Az érzelmi elemzés kimenetének megtekintése

Miután elindította a feladatot, és feldolgozza a valós idejű Twitter streamet, megtekintheti az érzelmek elemzésének eredményét.

A feladatok kimenetét valós időben tekintheti meg, például [Azure Storage Explorer](https://storageexplorer.com/) vagy az [Azure Explorer](https://www.cerebrata.com/products/azure-explorer/introduction) használatával. Innen [Power bi](https://powerbi.com/) használatával kiterjesztheti az alkalmazást, hogy az tartalmazza a testreszabott irányítópultot, például a következő képernyőképen láthatót:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Egy másik lekérdezés létrehozása a felnövekvő témakörök azonosításához

Egy másik lekérdezés, amellyel megismerheti a Twitter hangulatát, egy [csúszó ablakon](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics)alapul. A kapcsolódó témakörök azonosításához olyan témaköröket kell megkeresnie, amelyek egy adott időtartamon belül megemlítik a küszöbértéket.

Ebben a útmutatóban az elmúlt 5 másodpercben több mint 20 alkalommal említett témaköröket keres.

1. A feladatok panelen kattintson a **Leállítás** gombra a művelet leállításához. 

2. A **feladatok topológiája** szakaszban kattintson a **lekérdezés** mezőre. 

3. Módosítsa a lekérdezést a következőre:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Kattintson a **Mentés** gombra.

5. Győződjön meg arról, hogy a TwitterWpfClient alkalmazás fut. 

6. A **Start** gombra kattintva indítsa újra a feladatot az új lekérdezés használatával.


## <a name="get-support"></a>Támogatás kérése
További segítségért próbálja ki a [Azure stream Analytics fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
