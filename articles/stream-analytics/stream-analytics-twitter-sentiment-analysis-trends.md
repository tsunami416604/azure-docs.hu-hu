---
title: Twitter véleményeket valós idejű elemzés, az Azure Stream Analytics használ
description: A cikkből megtudhatja, hogyan használható a Stream Analytics Twitter-véleményeket valós idejű elemzés céljából. Az élő irányítópulton adatok az eseménygenerálás részletes útmutatást.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/29/2017
ms.openlocfilehash: 0b920d21486fc0003d8b11bef79bd44be4b28adf
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030614"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Azure Stream Analytics elemzés, valós idejű Twitter véleményeket

Megtudhatja, hogyan hozhat létre egy közösségi médiaelemzés véleményeket elemzési megoldás valós időben Twitterről események üzembe az Azure Event Hubs. Ezután használja az Azure Stream Analytics lekérdezési elemezheti az adatokat, majd az eredmények a később tárolására írási vagy egy irányítópultot és [Power BI](https://powerbi.com/) valós idejű elemzések biztosításához.

Közösségi elemzőeszközök segítségével a szervezetek trendekkel kapcsolatos témakörök ismertetése. Trendekkel témakörök tulajdonosok és a közösségi nagyszámú bejegyzéseket rendelkező szokások szolgálnak. Véleményeket elemzés, amely más néven *véleményével adatbányászati*, közösségi elemzőeszközök használja a termék, képet, és egyéb felé szokások megállapításához. 

Twitter-trend valós idejű elemzési nem kiváló példája egy elemzőeszköz, mert a hashtaggel történő előfizetés modell lehetővé teszi a megadott kulcsszavak (hashtageket) figyelésére és az adatcsatorna véleményeket elemzésének fejlesztése.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Forgatókönyv: Közösségi véleményeket elemzés, valós idejű

A vállalat, amely rendelkezik egy hírek media webhelyen olyan iránt érdeklődik előnyös használhatóságát a versenytársak való kiemeli a webhely tartalmát, amely közvetlenül kapcsolódik az olvasók által. A vállalat közösségi elemzési használ, amely kapcsolódik a olvasók Twitter-adatok elemzése a valós idejű véleményeket végrehajtásával kérdésekben.

Valós időben a Twitteren trendekkel témakörök azonosításához, a vállalat igényeinek tweetet mennyiségi és véleményeket valós idejű elemzési legfontosabb témakörök. Más szóval kell olyan céggel kapcsolatos véleményeket analysis elemzés, amely a közösségi média hírcsatorna alapul.

## <a name="prerequisites"></a>Előfeltételek
Ebben az oktatóanyagban egy ügyfélalkalmazást, amely Twitter kapcsolódik, és megkeresi a Twitter-üzeneteket, amelyek bizonyos hashtageket (amely állíthatja be) használja. Ahhoz, hogy az alkalmazás futtatásához, és a Twitter-üzeneteket Azure Streaming Analytics segítségével elemezheti, az alábbiakkal kell rendelkeznie:

* Azure-előfizetés
* Twitter-fiók 
* Egy Twitter-alkalmazást, és a [OAuth jogkivonat](https://dev.twitter.com/oauth/overview/application-owner-access-tokens) az adott alkalmazáshoz. Azt adja meg a magas szintű című cikkben talál útmutatást később egy Twitter-alkalmazás létrehozásához.
* A TwitterWPFClient alkalmazás, amely a Twitter hírcsatorna szól. Ahhoz, hogy az alkalmazás, töltse le a [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) a Githubról fájlt, és ezután bontsa ki a csomagot egy mappába a számítógépen. Ha meg szeretné tekinteni a forrás-kód, és futtassa az alkalmazást a hibakereső, a forráskódjának kaphat [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Létrehoz egy eseményközpontot Streaming Analytics bevitelhez

A mintaalkalmazás eseményeket hoz létre, és az Azure event hubs a leküldéses értesítések őket. Az Azure event hubs esemény adatfeldolgozást a Stream Analytics preferált. További információkért lásd: a [Azure Event Hubs dokumentáció](../event-hubs/event-hubs-what-is-event-hubs.md).


### <a name="create-an-event-hub-namespace-and-event-hub"></a>Event hub névtér és eseményközpont létrehozása
Ebben az eljárásban először létre kell hoznia egy event hub névtér, és ezután hozzáadhat új eseményközpont tartalmazni. Event hub névterek logikailag csoportosítják a kapcsolódó esemény bus példányok szolgálnak. 

1. Jelentkezzen be az Azure-portálon, majd kattintson a **hozzon létre egy erőforrást** > **az eszközök internetes hálózatát** > **Eseményközpont**. 

2. Az a **névtér létrehozása** panelen adja meg például a névtér nevét `<yourname>-socialtwitter-eh-ns`. A névtér egy tetszőleges nevet is használhat, de a neve nem érvényes URL- és Azure között egyedinek kell lennie. 
    
3. Válasszon egy előfizetést, és hozzon létre vagy válasszon egy erőforráscsoportot, majd kattintson **létrehozása**. 

    ![Az event hub névtér létrehozása](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. A névtér telepítése befejeződött, amikor az event hub névtér található az Azure-erőforrások listája. 

5. Kattintson az új névtéren, és a névtér paneljén kattintson  **+ &nbsp;Eseményközpont**. 

    ![Az Eseményközpont hozzáadása gombra egy új eseményközpont létrehozása ](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Az új eseményközpont neve `socialtwitter-eh`. Más nevet is használhat. Ha így tesz, jegyezze fel, mert később szüksége nevét. Nem kell minden egyéb beállításainak megadása az eseményközpontba.

    ![Egy új eseményközpont létrehozása panel](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Kattintson a **Create** (Létrehozás) gombra.


### <a name="grant-access-to-the-event-hub"></a>Hozzáférést biztosít az event hubs

Egy folyamat adatokat küldhet egy eseményközpontot, az event hubs egy házirendet, amely lehetővé teszi, hogy a megfelelő hozzáféréssel kell rendelkeznie. A hozzáférési szabályzat egy kapcsolati sztringet hoz létre, amelyben megtalálhatók az engedélyezési információk.

1.  Az esemény névtér paneljén kattintson **Event Hubs** és kattintson az új eseményközpont nevét.

2.  Az event hub paneljén kattintson **megosztott elérési házirendek** majd  **+ &nbsp;Hozzáadás**.

    >[!NOTE]
    >Ellenőrizze, hogy az event hubs, nem az event hub névtér dolgozik.

3.  Nevű házirend hozzáadása `socialtwitter-access` és **jogcím**, jelölje be **kezelése**.

    ![Az új esemény központi hozzáférési házirend létrehozása panel](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Kattintson a **Create** (Létrehozás) gombra.

5.  A házirend telepítése után kattintson rá a megosztott elérési házirendek listájában.

6.  A mezőben található **KAPCSOLATI karakterlánc elsődleges kulcs** , majd kattintson a Másolás gombra mellett a kapcsolati karakterláncot. 
    
    ![A hozzáférési házirendben az elsődleges kapcsolódási karakterlánc kulcs másolása](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Illessze be a kapcsolati sztringet egy szövegszerkesztőbe. Ez a kapcsolati karakterlánc szükséges ahhoz, hogy a következő szakaszban után néhány kisebb módosításokat végez.

    A kapcsolati karakterlánc így néz ki:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Figyelje meg, hogy a kapcsolati karakterlánc tartalmaz-e több kulcs-érték párok, pontosvesszővel elválasztva: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, és `EntityPath`.  

    > [!NOTE]
    > A biztonság a példában a kapcsolati karakterlánc részei el lettek távolítva.

8.  Szövegszerkesztőben, távolítsa el a `EntityPath` a kapcsolati karakterláncból pár (ne felejtse el eltávolítani az azt megelőző pontosvesszővel válassza el). Amikor elkészült, akkor a kapcsolati karakterlánc néz ki:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Konfigurálni és elindítani a Twitter ügyfélalkalmazás
Az ügyfélalkalmazás tweetet események lekérése Twitter-ről. Ehhez szükséges engedélyt a Twitter Streamelési API-k meghívásához. Adja meg az engedélyt, hogy a Twitter, amely állít elő, egyedi hitelesítő adataikat (például az OAuth jogkivonat) hoz létre egy alkalmazást. Az ügyfélalkalmazás használhatja a hitelesítő adatokat, ha az API-hívásokban ezután konfigurálhatja. 

### <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása
Ha még nem rendelkezik egy Twitter-alkalmazás, amely ehhez az oktatóanyaghoz használható, akkor hozzon létre egyet. Már rendelkeznie kell egy Twitter-fiók.

> [!NOTE]
> A pontos folyamatot, a Twitter-alkalmazás létrehozása és a kulcsokat, a titkos kulcsok és a token első változhatnak. Ha ezek az utasítások nem egyeznek meg, tekintse meg a Twitter hely, tekintse meg a Twitter fejlesztői dokumentációjában talál.

1. Lépjen a [Twitter alkalmazás kezelése lap](https://apps.twitter.com/). 

2. Hozzon létre egy új alkalmazást. 

    * A webhely URL-címe adja meg egy érvényes URL-címet. Nincs élő helyként. (Nem lehet csak megadni `localhost`.)
    * A visszahívási mezőt hagyja üresen. Ez az oktatóanyag használ az ügyfélalkalmazás visszahívások nem igényel.

    ![Az alkalmazás létrehozása a Twitteren](./media/stream-analytics-twitter-sentiment-analysis-trends/create-twitter-application.png)

3. Igény szerint megváltoztathatja a Alkalmazásengedélyek csak olvasható.

4. Az alkalmazás létrehozásakor Ugrás a **kulcsok és a hozzáférési jogkivonatok** lap.

5. Kattintson a gombra egy hozzáférési jogkivonat és a hozzáférési token titkos kulcs létrehozásához.

Tartani lesz szüksége, ez az információ, mivel a következő eljárásban szüksége lesz rá.

>[!NOTE]
>A kulcsok és titkos kulcsokat, a Twitter-alkalmazás a Twitter-fiók hozzáférést biztosítanak. Érzékeny, megegyezik a Twitter jelszavas kezelje ezt az információt. Például nincs beágyazás ezt az információt az alkalmazásban, amelyet átadhat másoknak. 


### <a name="configure-the-client-application"></a>Az ügyfélalkalmazás konfigurálása
Létrehoztunk Önnek egy Twitter-adatok használatával csatlakozó ügyfélalkalmazás [Twitter a Streamelési API-k](https://dev.twitter.com/streaming/overview) tweetet eseménygyűjtési kapcsolatos témakörök egy adott készletét. Az alkalmazás használja a [Sentiment140](http://help.sentiment140.com/) nyílt forráskódú eszközt, amely a következő véleményeket értéket rendel hozzá minden egyes tweetet:

* 0 = negatív.
* 2 = neutral
* 4 = pozitív

Miután a tweetet események véleményeket érték van rendelve, azok a korábban létrehozott eseményközpont vannak leküldve.

Mielőtt az alkalmazás fut, bizonyos információkat, például a Twitter és a event hub kapcsolati karakterlánc van szükség. Megadhatja, hogy a konfigurációs adatokat az alábbi módszerek:

* Futtassa az alkalmazást, és az alkalmazás felhasználói felület segítségével adja meg a kulcsokat, a titkos kulcsok és a kapcsolati karakterlánc. Ha ezt a konfigurációs adatok az aktuális munkamenetre, de nem menti a rendszer.
* Az alkalmazás .config fájl szerkesztésével, és állítsa be ott a értékeket. Ez a módszer továbbra is fennáll, a konfigurációs adatokat, de azt is jelenti, hogy a bizalmas adatokat a számítógép egyszerű szövegként vannak tárolva.

Az alábbi eljárás mindkét megközelítés dokumentumokat. 

1. Győződjön meg arról, hogy a letöltött és unzipped a [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) alkalmazás, az Előfeltételek a.

2. Beállítása a futási időben (és csak az aktuális munkamenet), majd futtassa a `TwitterWPFClient.exe` alkalmazás. Ha az alkalmazás kéri, adja meg a következő értékeket:

    * Twitter kulcsa (API-kulcsot).
    * A Twitter fogyasztói titkos kulcsot (API titok).
    * A Twitter hozzáférési jogkivonat.
    * A Twitter Access Token titkos kulcsot.
    * A korábban mentett kapcsolati karakterlánc adatokat. Győződjön meg arról, hogy a kapcsolati karakterlánc eltávolított használjon a `EntityPath` a kulcs-érték pár.
    * A Twitter kulcsszavakat, hogy meg szeretné határozni a céggel kapcsolatos véleményeket.

   ![TwitterWpfClient alkalmazás fut, melyen rejtett beállítások](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Az értékek tartósan beállításához szövegszerkesztővel TwitterWpfClient.exe.config fájl megnyitásához. Ezt a a `<appSettings>` elem, ehhez:

    * Állítsa be `oauth_consumer_key` a Twitter fogyasztói kulcsot (API-kulcsot). 
    * Állítsa be `oauth_consumer_secret` a Twitter fogyasztói titkos kulcsot (API titkos kulcs) számára.
    * Állítsa be `oauth_token` számára a Twitter hozzáférési jogkivonat.
    * Állítsa be `oauth_token_secret` számára a Twitter Access Token titkos kulcsot.

    A későbbi a `<appSettings>` elem, ezeket a módosításokat:

    * Állítsa be `EventHubName` az eseményközpont neveként az (Ez azt jelenti, hogy az entitás elérési út értékre).
    * Állítsa be `EventHubNameConnectionString` a kapcsolati karakterlánc módosításait. Győződjön meg arról, hogy a kapcsolati karakterlánc eltávolított használjon a `EntityPath` a kulcs-érték pár.

    A `<appSettings>` szakasz az alábbihoz hasonlóan néz ki. (Az érthetőség és a biztonsági, azt becsomagolt a sorokat és eltávolítani néhány karaktert.)

    ![TwitterWpfClient alkalmazás konfigurációs fájlt egy szövegszerkesztőben, és a Twitter kulcsok és titkos kulcsok és az esemény központ kapcsolati karakterlánc adatainak megjelenítése](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Ha már az alkalmazás nem indítható, TwitterWpfClient.exe most kell futtatni. 

5. A zöld start gombra kattintva gyűjthet közösségi céggel kapcsolatos véleményeket. A Tweetet eseményeket a **CreatedAt**, **témakör**, és **SentimentScore** küldi el az eseményközpont értékeket.

    ![TwitterWpfClient alkalmazás fut, a Twitter-üzenetek listáját ábrázoló](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Ha hibába ütközik, és a Twitter-üzeneteket a az ablak alsó részén megjelennek az adatfolyam nem jelenik meg, gondosan ellenőrizze a kulcsok és titkos kulcsok. Ellenőrizze a kapcsolati karakterlánc (Győződjön meg arról, hogy az ne tartalmazza a `EntityPath` kulcs-érték.)


## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

Most, hogy a rendszer valós időben Twitterről a adatfolyam-tweetet események, ezek az események valós idejű elemzése beállíthat egy Stream Analytics-feladat.

1. Az Azure portálon kattintson **hozzon létre egy erőforrást** > **az eszközök internetes hálózatát** > **Stream Analytics-feladat**.

2. A feladat neve `socialtwitter-sa-job` , és adja meg egy előfizetési, erőforráscsoportot és helyet.

    Jó ötlet helyezhető el a feladatot, és az event hubs a legjobb teljesítmény érdekében ugyanabban a régióban, és így nem kell fizetnie régiók közötti adattovábbításra.

    ![Egy új Stream Analytics-feladat létrehozása](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Kattintson a **Create** (Létrehozás) gombra.

    A feladat jön létre, és a portál megjeleníti a feladat részleteit.


## <a name="specify-the-job-input"></a>Adja meg a feladat bemeneti

1. A Stream Analytics-feladat, a alatt **feladat topológia** közepén feladat panelen kattintson a **bemenetek**. 

2. Az a **bemenetek** panelen kattintson  **+ &nbsp;hozzáadása** , és ezután adja meg a panel ezekkel az értékekkel:

    * **A bemeneti alias**: a nevet használja `TwitterStream`. Ha más nevet használjon, jegyezze fel a, mert később szüksége.
    * **Adatforrás típusa**: válasszon **adatfolyam**.
    * **Forrás**: válasszon **eseményközpont**.
    * **Beállítás importálása**: válasszon **eseményközpont használható a jelenlegi előfizetés**. 
    * **Service bus-névtér**: válassza ki a korábban létrehozott event hub névteret (`<yourname>-socialtwitter-eh-ns`).
    * **Az Event hubs**: válassza ki a korábban létrehozott eseményközpont (`socialtwitter-eh`).
    * **Eseményközpont házirend neve**: válassza ki a korábban létrehozott házirend (`socialtwitter-access`).

    ![Új bemeneti Streaming Analytics-feladat létrehozása](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Kattintson a **Create** (Létrehozás) gombra.


## <a name="specify-the-job-query"></a>A feladat-lekérdezés megadása

A Stream Analytics egy egyszerű, deklaratív lekérdezési modellel, átalakítások leíró támogatja. A nyelvi kapcsolatos további tudnivalókért tekintse meg a [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Ez az oktatóanyag segítséget nyújt a szerzői és több lekérdezések tesztelése Twitter-adatok.

Hasonlítsa össze a témakörök között megjegyzések számát, használhatja a [Átfedésmentes ablak](https://msdn.microsoft.com/library/azure/dn835055.aspx) számbavétele a megjegyzések által témakör öt másodpercenként.

1. Zárja be a **bemenetek** panelt, ha még nem tette meg.

2. A feladat panelen kattintson a **lekérdezés** mezőbe. Azure a be- és kimenetekkel, a feladathoz beállított sorolja fel, és lehetővé teszi, hogy hozzon létre egy lekérdezést, amely lehetővé teszi, hogy a bemeneti adatfolyam átalakítása a kimeneti keresztül küldött.

3. Győződjön meg arról, hogy fut-e a TwitterWpfClient alkalmazás. 

3. Az a **lekérdezés** panelen kattintson a Tovább gombra a pont a `TwitterStream` adja meg, és válassza ki **az adatokat a bemeneti**.

    ![A mintaadatokat használja a Streaming Analytics feladat bejegyzést, mintaadatokkal"bemeneti" kiválasztott menüpont](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Ekkor megnyílik egy panel, amely lehetővé teszi, hogy adja meg, mennyi mintaadatok beszerzéséhez definiált mennyi ideig a bemeneti adatfolyam olvasására.

4. Állítsa be **perc** 3 majd **OK**. 
    
    ![A bemeneti adatfolyam mintavételi a "3 perc" kiválasztott beállítások.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Azure-minták a bemeneti adatfolyam adatait 3 perc alatt érkezett, és értesíti, amikor készen áll a mintaadatok. (Ez időt vesz igénybe egy rövid ideig.) 

    A rendszer ideiglenesen tárolja a mintaadatokat, amelyek akkor érhetők el, amikor meg van nyitva a lekérdezési ablak. Ha bezárja a lekérdezési ablakban, a minta adatait a rendszer törli, és létre kell hoznia egy új minta adatkészletet. 

5. Módosítsa a lekérdezést a kód szerkesztése a következő:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Ha nem a `TwitterStream` a bemeneti aliasként, helyettesítse be az aliast, a `TwitterStream` a lekérdezésben.  

    Ez a lekérdezés használja a **TIMESTAMP BY** kulcsszó időbélyegmezővel adni a tartalom a historikus számítási használhatók. Ha ez a mező nincs megadva, a leképezési művelet történik, az event hubs érkező minden esemény ideje. További információ: a "Érkezésének ideje vs alkalmazási idő" szakaszában [Stream Analytics lekérdezési hivatkozás](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Ez a lekérdezés is segítségével éri el az egyes időszak végén az időbélyeg a **System.Timestamp** tulajdonság.

5. Kattintson a **teszt**. A lekérdezés, amely mintát venni, hogy az adatok alapján futtatja.
    
6. Kattintson a **Save** (Mentés) gombra. Ez a lekérdezés a Streaming Analytics-feladat részeként menti. (A mintaadatok nem menti azt.)


## <a name="experiment-using-different-fields-from-the-stream"></a>Az adatfolyamból különböző mezőkkel kísérlet 

A következő táblázat a mezők a streamelési adatok JSON részét képező. Nyugodtan kísérletezhet a lekérdezés-szerkesztőben.

|JSON-tulajdonság | Meghatározás|
|--- | ---|
|CreatedAt | Az a tweetet létrehozásának időpontja|
|Témakör | A témakör, amely megfelel a megadott kulcsszó|
|SentimentScore | A céggel kapcsolatos véleményeket pontszám a Sentiment140|
|Szerző | A tweetet küldött Twitter-leíró|
|Szöveg | A teljes választörzs a tweetet|


## <a name="create-an-output-sink"></a>Kimeneti fogadóként létrehozása

Most már definiált eseménystream egy eseményközpontot, a betöltési események és az adatfolyam keresztül átalakítás végrehajtásához egy lekérdezést adjon meg. Utolsó lépése, hogy a feladat kimeneti fogadóként meghatározása.  

Ebben az oktatóanyagban írt összesített tweetet események a feladat lekérdezésből Azure Blob Storage tárolóban.  Az eredményeket az Azure SQL Database, Azure Table storage, az Event Hubs is tolható, vagy a Power BI, attól függően, hogy az alkalmazás igényel.

## <a name="specify-the-job-output"></a>Adja meg a feladat kimenetére

1. Az a **feladat topológia** területen kattintson a **kimeneti** mezőbe. 

2. Az a **kimenetek** panelen kattintson  **+ &nbsp;Hozzáadás** , és ezután adja meg a panel ezekkel az értékekkel:

    * **A kimeneti alias**: a nevet használja `TwitterStream-Output`. 
    * **Gyűjtése**: válasszon **Blob-tároló**.
    * **Importálási lehetőségeit**: válasszon **használja a jelenlegi előfizetés blob-tároló**.
    * **A tárfiók**. Válassza ki **hozzon létre egy új tárfiókot.**
    * **A tárfiók** (második mezőben). Adja meg `YOURNAMEsa`, ahol `YOURNAME` a neve, vagy egy másik egyedi karakterlánc. A név csak kisbetűket és számokat használható, és Azure között egyedinek kell lennie. 
    * **Tároló**. Adja meg `socialtwitter`.
    A tárfiók nevét és a tároló neve együttesen arra használják, adjon meg egy URI-t a blob-tároló, ehhez hasonló: 

    `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
    ![Panel "Új kimeneti" Stream Analytics-feladat](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Kattintson a **Create** (Létrehozás) gombra. 

    Azure tárolási fiókot hoz létre, és automatikusan létrehoz egy kulcsot. 

5. Zárja be a **kimenetek** panelen. 


## <a name="start-the-job"></a>A feladat indítása

Egy feladat bemeneti, a lekérdezés és a kimeneti meg van adva. Készen áll a Stream Analytics-feladat indítása.

1. Győződjön meg arról, hogy fut-e a TwitterWpfClient alkalmazás. 

2. A feladat panelen kattintson **Start**.

    ![A Stream Analytics-feladat indítása](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. Az a **indítási feladat** panelen a **feladatkiemenetét kezdési időpont**, jelölje be **most** , majd **Start**. 

    ![A Stream Analytics-feladat "Feladat start" panel](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    Azure értesíti, amikor a feladat elindult, és a feladat panelen megjelenik az állapota **futtató**.

    ![Futó feladat](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Nézet kimeneti véleményeket elemzés

A feladat elindult, és a valós idejű Twitter-adatfolyam feldolgozása után tekintheti meg a kimeneti véleményeket elemzés céljából.

Egy eszköz, például használhatja [Azure Tártallózó](https://storageexplorer.com/) vagy [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) valós időben a feladat kimenetére megtekintéséhez. Itt használható [Power BI](https://powerbi.com/) kiterjesztése az alkalmazás közé tartoznak például az alábbi képernyőfelvételen látható módon egy személyre szabott irányítópultot:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Hozzon létre egy másik lekérdezést azonosításához trendekkel kapcsolatos témakörök

Egy másik lekérdezés segítségével Twitter sentiment ismertetése alapján egy [késleltetett ablak](https://msdn.microsoft.com/library/azure/dn835051.aspx). Trendekkel kapcsolatos témakörök azonosításához, akkor keresse meg az témakörök, amelyek a megadott időn belül megjegyzések vonatkozó küszöbérték cross.

Ez az oktatóanyag céljából akkor ellenőrizze, témakörök, amelyek szerepelnek a több mint 20 alkalommal az utolsó 5 másodperc.

1. A feladat panelen kattintson **leállítása** leállítja a feladatot. 

2. Az a **feladat topológia** területen kattintson a **lekérdezés** mezőbe. 

3. Módosítsa a lekérdezést a következőhöz:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Kattintson a **Save** (Mentés) gombra.

5. Győződjön meg arról, hogy fut-e a TwitterWpfClient alkalmazás. 

6. Kattintson a **Start** az új lekérdezés használata a feladat újraindításához.


## <a name="get-support"></a>Támogatás kérése
Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
