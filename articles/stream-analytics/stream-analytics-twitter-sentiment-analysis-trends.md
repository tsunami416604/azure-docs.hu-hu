---
title: Valós idejű Twitter-vélemények elemzése az Azure Stream Analytics
description: Ez a cikk ismerteti a Stream Analytics használata valós idejű Twitter-vélemények elemzése. Adatok az irányítópulton az élő esemény létrehozása a lépésekre bontott útmutatót.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/29/2017
ms.openlocfilehash: c34b3fa1597866e4f722fccee84f83f4caab6bfb
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56876296"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Valós idejű Twitter-vélemények elemzése az Azure Stream Analytics szolgáltatásban

Ismerje meg, hogyan hozhat létre egy közösségi médiaelemzés sentiment analysis megoldást, és valós idejű Twitter-események Azure Event hubsba. Ezután elemezheti az adatokat, és egy Azure Stream Analytics lekérdezési később tárolja az eredményeket az írási vagy használhatja egy irányítópultot és [Power BI](https://powerbi.com/) valós idejű elemzéseket biztosít.

Közösségi média elemzőeszközök megkönnyíti a szervezetek számára a népszerű témakörök ismertetése. Népszerű témakörök olyan témákat és szokások, amelyek nagy mennyiségű bejegyzések, a közösségi médiában. Hangulatelemzés, más néven *vélemény adatbányászati*, közösségi média analitikai eszközök segítségével határozza meg, szokások felé egy termék, idea és így tovább. 

Twitter-trendek valós idejű elemzése egy remek példa egy elemzőeszköz azért a hashtag előfizetési modell lehetővé teszi meghatározott kulcsszavakat (hashtageket) figyelésére és a hírcsatorna hangulatelemzés fejlesztése.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Forgatókönyv: Közösségi média vélemények elemzése valós időben

A vállalat, amely rendelkezik egy sajtóbeli híradásoknál webhely olyan így versenytársai használhatóságát teljes tartalmát, amely közvetlenül kapcsolódik az olvasók által iránt. A vállalati közösségimédia-elemzés, amely a olvasók, Twitter-adatok valós idejű hangulatelemzés végrehajtásával témakörök használja.

Népszerű valós idejű Twitter-témakörök azonosításához, a vállalati tweet kötet és vélemények valós idejű elemzések a legfontosabb témakörök van szüksége. Más szóval szükség egy sentiment analysis elemzési motorjára a közösségimédia-hírcsatorna alapján.

## <a name="prerequisites"></a>Előfeltételek
Ebben az oktatóanyagban, amely kapcsolódik a Twitteren, és a tweeteket, amelyek bizonyos (amely beállíthatja) hashtageket keres egy ügyfélalkalmazás használhat. Annak érdekében, hogy az alkalmazás futtatásához, és az Azure Stream Analytics használatával tweetek elemzése, az alábbiakkal kell rendelkeznie:

* Azure-előfizetés
* Twitter-fiók 
* Twitter-alkalmazás és a [OAuth-jogkivonat](https://dev.twitter.com/oauth/overview/application-owner-access-tokens) az adott alkalmazáshoz. Később egy Twitter-alkalmazás létrehozása magas szintű útmutatást biztosítunk.
* A TwitterWPFClient alkalmazás, amely beolvassa a Twitter-hírcsatorna. Ez az alkalmazás első, töltse le a [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) GitHub-fájlt, majd csomagolja ki a csomagot egy mappába a számítógépen. Ha meg szeretné tekinteni a kódot, és futtassa az alkalmazást a hibakeresőt a forrás, beszerezheti a forráskódot a [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TwitterClient). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>A Stream Analytics beviteli eseményközpont létrehozása

A mintaalkalmazás eseményeket hoz létre, és leküldi azokat egy Azure-eseményközpont. Az Azure event hubs a Stream Analytics eseményfogadás előnyben részesített módja. További információkért lásd: a [Azure Event Hubs dokumentációja](../event-hubs/event-hubs-what-is-event-hubs.md).


### <a name="create-an-event-hub-namespace-and-event-hub"></a>Eseményközpont-névtér és eseményközpont létrehozása
Ebben az eljárásban először hozzon létre egy eseményközpont-névteret, és ezután hozzáadhat új eseményközpont adott névtérhez. Eseményközpont-névtér logikailag csoportosítják a kapcsolódó esemény bus példányok szolgálnak. 

1. Jelentkezzen be az Azure Portalon, és kattintson a **erőforrás létrehozása** > **IOT-** > **Eseményközpont**. 

2. Az a **névtér létrehozása** panelen adja meg például a névtér nevét `<yourname>-socialtwitter-eh-ns`. A névtér bármilyen nevet használhat, de a név érvényes URL-cím lehet, és Azure-ban egyedinek kell lennie. 
    
3. Válasszon ki egy előfizetést, és hozzon létre vagy válasszon ki egy erőforráscsoportot, majd kattintson **létrehozás**. 

    ![Eseményközpont-névtér létrehozása](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. Ha a névtér telepítése befejeződött, az eseményközpont-névtér található az Azure-erőforrások listája. 

5. Kattintson az új névtéren, és a névtér panelen kattintson a  **+ &nbsp;Eseményközpont**. 

    ![Az Eseményközpont felvétele gombra egy új eseményközpont létrehozása](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Nevezze el az új event hubs `socialtwitter-eh`. Más nevet is használhat. Ha így tesz, jegyezze fel, mert később szüksége lesz a neve. Nem kell minden olyan egyéb beállítások megadása az event hubs.

    ![Egy új eseményközpont létrehozása panel](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Kattintson a **Create** (Létrehozás) gombra.


### <a name="grant-access-to-the-event-hub"></a>Az event hubs hozzáférés engedélyezése

Egy folyamat küldhet adatokat egy eseményközpontba, mielőtt az event hubs egy szabályzatot, amely lehetővé teszi, hogy a megfelelő hozzáféréssel kell rendelkeznie. A hozzáférési szabályzat egy kapcsolati sztringet hoz létre, amelyben megtalálhatók az engedélyezési információk.

1.  Az esemény névtér panelen kattintson a **az Event Hubs** és kattintson az új eseményközpont neve.

2.  Az event hub panelen kattintson a **megosztott elérési házirendek** majd  **+ &nbsp;Hozzáadás**.

    >[!NOTE]
    >Ellenőrizze, hogy az event hubs, nem az eseményközpont-névtér dolgozik.

3.  Nevű szabályzat hozzáadása `socialtwitter-access` és **jogcím**válassza **kezelés**.

    ![Event hub új hozzáférési szabályzat létrehozására szolgáló panelt](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Kattintson a **Create** (Létrehozás) gombra.

5.  A házirend telepítése után kattintson rá a megosztott elérési szabályzatok listájában.

6.  Keresse meg a be az **KAPCSOLATI karakterlánc – elsődleges kulcs** , és kattintson a Másolás gombra a kapcsolati karakterlánc mellett. 
    
    ![A hozzáférési szabályzatot az elsődleges kapcsolati karakterlánc kulcs másolása](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Illessze be a kapcsolati sztringet egy szövegszerkesztőbe. Szükség van a kapcsolati karakterlánc a következő szakaszban rá néhány kisebb módosítások végrehajtása után.

    A kapcsolati karakterláncot a következőhöz hasonló:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Figyelje meg, hogy a kapcsolati karakterláncot tartalmazza-e több kulcs-érték párok, pontosvesszővel elválasztva: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, és `EntityPath`.  

    > [!NOTE]
    > A biztonság érdekében a rendszer eltávolítja a példában a kapcsolati karakterlánc részei.

8.  A szöveges szerkesztő, távolítsa el a `EntityPath` párt a kapcsolati karakterláncból (ne felejtse el eltávolítani a pontosvesszőt is). Ha elkészült, a kapcsolati karakterláncot a következőhöz hasonló:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Konfigurálja és indítsa el a Twitter-ügyfélalkalmazás
Az ügyfélalkalmazás lekéri a tweet események közvetlenül a Twitteren. Ehhez szükséges engedélyt a Twitter-Streamelési API-jainak hívására. Adja meg az engedélyt, létrehozhat egy alkalmazást a Twitteren, amely állít elő, egyedi hitelesítő adatokat (például az OAuth-jogkivonatának). Konfigurálhatja az ügyfélalkalmazás ezeket a hitelesítő adatokat használja, ha azt az API-hívást hajt végre. 

### <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása
Ha Ön még nem rendelkezik egy Twitter-alkalmazás, amely ebben az oktatóanyagban használhatja, létrehozhat egyet. Twitter-fiókja már rendelkeznie kell.

> [!NOTE]
> A Twitter-alkalmazás létrehozása és lekérdezése, a kulcsok, titkos kódok és jogkivonat-pontos folyamat módosulhat. Ha ezek az utasítások nem egyezik meg a Twitter webhelyen megjelenő, tekintse meg a Twitter-fejlesztői dokumentáció.

1. Nyissa meg a [Twitter-alkalmazás felügyeleti oldala](https://apps.twitter.com/). 

2. Hozzon létre egy új alkalmazást. 

    * A webhely URL-CÍMÉT adja meg egy érvényes URL-címet. Nincs élő helyként. (Nem adható meg csak `localhost`.)
    * A visszahívási mezőt hagyja üresen. Az ebben az oktatóanyagban használt ügyfélalkalmazás visszahívások nem igényel.

    ![Twitter-alkalmazás létrehozása](./media/stream-analytics-twitter-sentiment-analysis-trends/create-twitter-application.png)

3. Igény szerint módosítsa az alkalmazás engedélyeit a csak olvasható.

4. Az alkalmazás létrehozását követően nyissa meg a **kulcsok és hozzáférési tokenek** lapot.

5. Kattintson a gombra kattintva hozzon létre egy hozzáférési jogkivonatot, és a hozzáférési jogkivonat titkos kulcsa.

Tartsa praktikus, ezt az információt, mert szüksége lesz rá a következő eljárásban.

>[!NOTE]
>A kulcsok és titkos kulcsokat, a Twitter-alkalmazás számára adja meg a Twitter-fiókja elérését. Kezelje ezt az információt érzékeny, ugyanaz, mint a Twitter-jelszavát. Például ne ágyazza be ezt az információt, amelyet mások átadhat egy alkalmazásban. 


### <a name="configure-the-client-application"></a>Az ügyfélalkalmazás konfigurálása
Létrehoztunk egy Twitter-adatok a csatlakozó ügyfélalkalmazás [Twitter a Streamelési API-k](https://dev.twitter.com/streaming/overview) tweet események kapcsolatos témaköröket egy meghatározott készletének gyűjtéséhez. Az alkalmazás használja a [Sentiment140](http://help.sentiment140.com/) nyílt forráskódú eszköz, amely a következő vélemények értéket rendel minden tweetnek:

* 0 = negatív
* 2 = neutral
* 4 = pozitív

Miután a tweet események értékű véleményt rendeltek, azok a korábban létrehozott event hubs kerüljenek.

Mielőtt az alkalmazás fut, bizonyos adatait, például a Twitter-kulcsokat és az eseményközpont kapcsolati karakterláncával van szükség. Megadhatja, hogy a konfigurációs adatokat a következő lehetőségeket biztosítva:

* Futtassa az alkalmazást, és az alkalmazás felhasználói felület használatával a kulcsokat, titkokat és kapcsolati karakterláncot adja meg. Ha így tesz, a rendszer a konfigurációs adatokat használja az aktuális munkamenet, de nem menti a rendszer.
* Az alkalmazás .config fájl szerkesztésével, és ott állítsa az értékeket. Ez a módszer továbbra is fennáll, a konfigurációs adatokat, de azt is jelenti, hogy ez feltehetően bizalmas adatok szövegként a számítógépen tárolja.

Az alábbi eljárás mindkét módszerénél dokumentumok. 

1. Győződjön meg arról, hogy letöltötte és kicsomagolt a [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) alkalmazás, az az előfeltételek.

2. A futási időben (és csak az aktuális munkamenethez tartozó) értékek Futtatás beállítása a `TwitterWPFClient.exe` alkalmazás. Ha az alkalmazás kéri, adja meg a következő értékeket:

    * A Twitter fogyasztói kulcs (API-kulcs).
    * A Twitter fogyasztói titkos kulcs (API titkos kódot).
    * A Twitter-hozzáférési jogkivonatot.
    * A Twitter-hozzáférési Token titkos.
    * A korábban mentett kapcsolati sztring adatait. Győződjön meg arról, hogy eltávolította a kapcsolati karakterláncot használja-e a `EntityPath` a kulcs-érték pár.
    * A Twitter kulcsszavak, amelyekre meg szeretné határozni a róluk szóló véleményeket.

   ![TwitterWpfClient alkalmazás fut, rejtett beállításainak megjelenítése:](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Tartósan állítsa az értékeket, egy szövegszerkesztő használatával nyissa meg a TwitterWpfClient.exe.config fájlt. Ezt a a `<appSettings>` elem, ehhez:

    * Állítsa be `oauth_consumer_key` a Twitter fogyasztói kulcs (API-kulcs). 
    * Állítsa be `oauth_consumer_secret` , a Twitter fogyasztói titkos kulcs (API titkos kódot).
    * Állítsa be `oauth_token` , a Twitter-hozzáférési jogkivonatot.
    * Állítsa be `oauth_token_secret` , a Twitter hozzáférési Token titkos kulcsot.

    Későbbi a `<appSettings>` elem, ezeket a módosításokat:

    * Állítsa be `EventHubName` event hub nevét (azt jelenti, az a entitás elérési útja értékre).
    * Állítsa be `EventHubNameConnectionString` kapcsolati karakterláncot. Győződjön meg arról, hogy eltávolította a kapcsolati karakterláncot használja-e a `EntityPath` a kulcs-érték pár.

    A `<appSettings>` szakaszban a következő példához hasonlóan jelenik meg. (És biztonsági, hogy burkolt be néhány sor és néhány karaktert eltávolítva.)

    ![TwitterWpfClient alkalmazás konfigurációs fájlt egy szövegszerkesztőben, és a Twitter-kulcsok és titkok és az event hub-kapcsolati sztring adatait](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Ha az alkalmazás már nem indította el, futtassa a TwitterWpfClient.exe most. 

5. A zöld indítása gombra kattintva közösségi vélemények gyűjtése. A Tweet eseményeket a **CreatedAt**, **témakör**, és **SentimentScore** értékek az event hubs felé.

    ![TwitterWpfClient alkalmazást futtató, tweetek listáját megjelenítő](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Ha hibába ütközik, és nem jelenik meg az ablak alsó részén megjelenő tweetstream, ellenőrizze a kulcsok és titkos kulcsok. Emellett ellenőrizze a kapcsolati karakterláncot (Győződjön meg arról, hogy ne tartalmazza a `EntityPath` kulcs-érték.)


## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

Most, hogy a tweet események vannak twitteren valós idejű streamelési, állíthat be egy Stream Analytics-feladat ezek az események valós idejű elemzését.

1. Az Azure Portalon kattintson a **erőforrás létrehozása** > **IOT-** > **Stream Analytics-feladat**.

2. Nevezze el a feladat `socialtwitter-sa-job` és a egy előfizetést, erőforráscsoportot és helyet adja meg.

    Helyezze el a feladat és az eseményközpont ugyanabban a régióban, a legjobb teljesítmény érdekében érdemes, és hogy a régiók közötti adatátvitel után ne fizessen.

    ![Egy új Stream Analytics-feladat létrehozása](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Kattintson a **Create** (Létrehozás) gombra.

    A feladat jön létre, és a portál megjeleníti a feladat részleteit.


## <a name="specify-the-job-input"></a>Adja meg a feladat bemenete

1. A Stream Analytics-feladatban alatt **Feladattopológia** közepén feladat panelen kattintson a **bemenetek**. 

2. Az a **bemenetek** panelen kattintson a  **+ &nbsp;Hozzáadás** , majd töltse ki a panel ezekkel az értékekkel:

    * **Bemeneti áljel**: A nevet használja `TwitterStream`. Ha más nevet használ, jegyezze fel a, mert később szüksége.
    * **Forrás típusa**: Válassza ki **adatfolyam**.
    * **forrás**: Válassza ki **eseményközpont**.
    * **Importálási beállítás**: Válassza ki **használható event hubs aktuális előfizetésből**. 
    * **Service bus-névtér**: Válassza ki a korábban létrehozott eseményközpont-névtér (`<yourname>-socialtwitter-eh-ns`).
    * **Event hubs**: Válassza ki a korábban létrehozott event hubs (`socialtwitter-eh`).
    * **Eseményközpont szabályzatának neve**: Válassza ki a korábban létrehozott hozzáférési házirend (`socialtwitter-access`).

    ![Hozzon létre új bemeneti Stream Analytics-feladat](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Kattintson a **Create** (Létrehozás) gombra.


## <a name="specify-the-job-query"></a>Adja meg a feladat-lekérdezés

Stream Analytics egy egyszerű, deklaratív lekérdezési modellel, átalakítások leíró támogatja. A nyelv kapcsolatos további információkért tekintse meg a [Azure Stream Analytics lekérdezési nyelv leírása](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Ez az oktatóanyag segítséget nyújt a hozhat létre, és a Twitter-adatok felett több lekérdezés teszteléséhez.

Hasonlítsa össze a témakörök közötti említései számát, használhatja a [Átfedésmentes ablak](https://msdn.microsoft.com/library/azure/dn835055.aspx) említései száma beszerezni a témakör öt másodpercenként.

1. Zárja be a **bemenetek** panelen, ha még nem tette.

2. Az a **áttekintése** panelen kattintson a **lekérdezés szerkesztése** a közelében lekérdezésmező, jobb. Azure bemeneteit és kimeneteit a feladat konfigurált sorolja fel, és a egy lekérdezést, amely a bemeneti stream alakíthatja a kimeneti keresztül küldött létrehozását teszi lehetővé.

3. Győződjön meg arról, hogy a TwitterWpfClient alkalmazása fut-e. 

3. Az a **lekérdezés** panelen kattintson a Tovább gombra a pontra a `TwitterStream` adja meg, és válassza ki **mintaadatok bemenetből**.

    ![A Stream Analytics feladat "Mintaadatok a bemenetből" kijelölt bejegyzés mintaadatok használandó menü parancsai](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Ekkor megnyílik egy panel, amelyen megadhatja a mintaadatok mennyiségét tekintetében, hogy mennyi ideig beolvasása, megadva, hogy a bemeneti adatfolyam olvasása.

4. Állítsa be **perc** 3 majd **OK**. 
    
    ![A bemeneti stream mintavételezi a kiválasztott "3 percig" lehetőségei.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Az Azure-minták 3 perc alatt az adatokat a bemeneti streamből, és értesíti, amikor készen áll a mintaadatok. (Ez tart egy rövid ideig.) 

    A rendszer ideiglenesen tárolja a mintaadatokat, amelyek akkor érhetők el, amikor meg van nyitva a lekérdezési ablak. Ha bezárja a lekérdezési ablakban, a mintaadatok elvesznek, és létre kell hoznia egy új mintaadatkészletet. 

5. A kódszerkesztőben lekérdezés módosítása a következőhöz:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Ha nem a `TwitterStream` a bemeneti alias, helyettesítse be az aliast a `TwitterStream` a lekérdezésben.  

    Ez a lekérdezés használ a **TIMESTAMP BY** kulcsszó használatával adjon meg egy időbélyegmezőt a hasznos adatok használhatók a historikus számítás. Ha ez a mező nincs megadva, akkor a leképezési művelet az eseményközpontból érkező minden esemény ideje szerint történik. További információ a "Érkezési ideje és kérelem ideje" szakaszában [Stream Analytics lekérdezési leírása](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Ez a lekérdezés is hozzáfér minden időszak végén az időbélyeg használatával a **System.Timestamp** tulajdonság.

5. Kattintson a **teszt**. A lekérdezés fut, a mintavételezett adatok.
    
6. Kattintson a **Save** (Mentés) gombra. Ez menti a lekérdezés a Streaming Analytics-feladat részeként. (A mintaadatok nem mentse azt.)


## <a name="experiment-using-different-fields-from-the-stream"></a>Kísérletezzen a különböző mezővel a streamből 

Az alábbi táblázat a mezők, amelyek részei a streamelési adatok JSON. Nyugodtan kísérletezhet a Lekérdezésszerkesztő.

|JSON-tulajdonság | Meghatározás|
|--- | ---|
|CreatedAt | A tweet létrehozásának időpontja|
|Témakör | A témakör, amely megfelel a megadott kulcsszóval.|
|SentimentScore | A véleménypontszám a Sentiment140|
|Szerző | A Twitter-leírót a Twitter-üzenetet küldött|
|Szöveg | A tweet a teljes szervezet|


## <a name="create-an-output-sink"></a>Hozzon létre egy kimeneti fogadóba

Most már definiált az eseménystream egy eseményközpontba, adjon meg, hogy eseményeket és a egy lekérdezést a stream keresztül átalakítás végrehajtásához. Az utolsó lépés, hogy a feladat egy kimeneti fogadó.  

Ebben az oktatóanyagban írása az összesített tweet eseményeket a feladat lekérdezésből a az Azure Blob storage.  Az eredmények küldje le az Azure SQL Database, Azure Table storage, az Event Hubs, vagy a Power bi-ban az alkalmazás igényeinek.

## <a name="specify-the-job-output"></a>Adja meg a feladat kimenete

1. Az a **Feladattopológia** területén kattintson a **kimeneti** mezőbe. 

2. Az a **kimenetek** panelen kattintson a  **+ &nbsp;Hozzáadás** , majd töltse ki a panel ezekkel az értékekkel:

    * **Kimeneti alias**: A nevet használja `TwitterStream-Output`. 
    * **Fogadó**: Válassza a **Blob Storage** lehetőséget.
    * **Importálási beállítások**: Válassza ki **a blob storage aktuális előfizetésből**.
    * **Storage-fiók**. Válassza ki **hozzon létre egy új tárfiókot.**
    * **Storage-fiók** (második mezőben). Adja meg `YOURNAMEsa`, ahol `YOURNAME` a nevét, vagy egy másik egyedi karakterlánc. Használhatja, hogy a név csak kisbetűket és számokat, és az Azure-ban egyedinek kell lennie. 
    * **Tároló**. Írja be a `socialtwitter` (igen) kifejezést.
    A tárfiók nevét és a tároló nevének együttesen arra használják egy URI-t biztosítanak a blob Storage, ehhez hasonló: 

    `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
    ![Stream Analytics-feladat "Új kimenet" panel](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Kattintson a **Create** (Létrehozás) gombra. 

    Az Azure storage-fiókot hoz létre, és automatikusan létrehoz egy kulcsot. 

5. Zárja be a **kimenetek** panelen. 


## <a name="start-the-job"></a>A feladat indítása

Egy feladat bemenete, a lekérdezés és a kimeneti vannak megadva. Készen áll a Stream Analytics-feladat elindításához.

1. Győződjön meg arról, hogy a TwitterWpfClient alkalmazása fut-e. 

2. A feladat panelen kattintson a **Start**.

    ![A Stream Analytics-feladat indítása](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. Az a **indítási feladat** panelen a **feladatkimenet kezdési idő**, jelölje be **most** és kattintson a **Start**. 

    ![A Stream Analytics-feladat "Feladat indítása" panel](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    Azure értesíti, ha a feladat elindult, és a feladatok panelen az állapot elemnél **futó**.

    ![Feladat futtatása](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Hangulatelemzés kimenetének megtekintése

Miután a feladat elindult, és feldolgozása folyamatban van a valós idejű Twitter-streamet, megtekintheti a hangulatelemzés kimenetét.

Egy hasonló eszközzel [Azure Storage Explorer](https://storageexplorer.com/) vagy [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) valós időben a feladat kimenetének megtekintéséhez. Itt használható [Power BI](https://powerbi.com/) bővítése az alkalmazás egy testreszabott irányítópulttal, az alábbi képernyőn láthatóhoz hasonló:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Hozzon létre egy másik lekérdezést Népszerű-témakörök azonosításához

Egy másik lekérdezés segítségével megismerheti a Twitter-vélemények alapján egy [késleltetett ablak](https://msdn.microsoft.com/library/azure/dn835051.aspx). Népszerű-témakörök azonosításához, akkor tekintse meg az témakörök, amelyek egy küszöbértéket egy adott időn belül említései adatbázisközi.

Ez az oktatóanyag az alkalmazásában akkor ellenőrizze témakörök, amelyek az elmúlt 5 másodperc alatt több mint 20 alkalommal szerepelnek.

1. A feladat panelen kattintson a **leállítása** leállítani a feladatot. 

2. Az a **Feladattopológia** területén kattintson a **lekérdezés** mezőbe. 

3. Módosítsa a lekérdezést a következőhöz:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Kattintson a **Save** (Mentés) gombra.

5. Győződjön meg arról, hogy a TwitterWpfClient alkalmazása fut-e. 

6. Kattintson a **Start** , indítsa újra a feladatot, az új lekérdezés használatával.


## <a name="get-support"></a>Támogatás kérése
További segítségre van szüksége, próbálja meg [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
