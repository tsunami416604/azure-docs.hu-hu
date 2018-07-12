---
title: Az Azure Logic Apps szolgáltatással integrálható függvények létrehozása | Microsoft Docs
description: Létrehozhat egy függvényt, amely integrálható az Azure Logic Apps és az Azure Cognitive Services szolgáltatással a tweetek hangulatának kategorizálásához és értesítések küldéséhez, amennyiben azok kedvezőtlen véleményt tükröznek.
services: functions, logic-apps, cognitive-services
keywords: munkafolyamat, felhőalapú alkalmazások, felhőszolgáltatások, üzleti folyamatok, rendszerintegráció, vállalati alkalmazásintegráció, EAI
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 16a46b4c49687186e25c399dcc2c5c168e7c5004
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38586874"
---
# <a name="create-a-function-that-integrates-with-azure-logic-apps"></a>Az Azure Logic Apps szolgáltatással integrálható függvények létrehozása

Az Azure Functions integrálható az Azure Logic Apps szolgáltatással a Logic Apps Designerben. Ez az integráció lehetővé teszi a Functions számítási teljesítményének kihasználását, amikor egyéb Azure- és külső szolgáltatásokkal dolgozik. 

Az oktatóanyag bemutatja, hogyan használható a Functions, a Logic Apps és a Microsoft Cognitive Services az Azure-on a Twitter-bejegyzések hangulatának elemzéséhez. A HTTP által aktivált függvények zöld, sárga vagy piros színnel kategorizálják a tweeteket a véleménypontszám alapján. A rendszer e-mailt küld, amikor kedvezőtlen véleményt észlel. 

![az alkalmazás első két lépésének képe a Logic App Designerben](media/functions-twitter-email/designer1.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egy Cognitive Services API-erőforrás létrehozása.
> * Olyan függvény létrehozása, amely kategorizálja a tweetek hangulatát.
> * A Twitterhez csatlakozó logikai alkalmazás létrehozása.
> * A hangulatfelismerés hozzáadása a logikai alkalmazáshoz. 
> * A logikai alkalmazás csatlakoztatása a függvényhez.
> * E-mail küldése a függvénytől érkező válasz alapján.

## <a name="prerequisites"></a>Előfeltételek

+ Egy aktív [Twitter](https://twitter.com/)-fiók. 
+ Egy [Outlook.com](https://outlook.com/)-fiók (az értesítések küldéséhez).
+ A témakör kiindulópontjául [Az első függvény létrehozása az Azure Portalon](functions-create-first-azure-function.md) című cikkben létrehozott erőforrások szolgálnak.  
Hajtsa végre az itt található lépéseket a függvényalkalmazás létrehozásához, ha eddig még nem tette meg.

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services-erőforrás létrehozása

A Cognitive Services API-k egyéni erőforrásként érhetők el az Azure-ban. A Text Analytics API-t használja a monitorozott tweetek hangulatának megállapításához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

3. Kattintson az **AI + Cognitive Services** > **Text Analytics API** lehetőségre. Használja a táblában megadott beállításokat, fogadja el a feltételeket, és jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet.

    ![Cognitive-erőforrás létrehozása oldal](media/functions-twitter-email/cog_svcs_resource.png)

    | Beállítás      |  Ajánlott érték   | Leírás                                        |
    | --- | --- | --- |
    | **Name (Név)** | MyCognitiveServicesAccnt | Válasszon egy egyedi fióknevet. |
    | **Hely** | USA nyugati régiója | Az Önhöz legközelebbi helyet használja. |
    | **Tarifacsomag** | F0 | Kezdjen a legalacsonyabb szinttel. Ha kifogy a hívásokból, lépjen magasabb szintre.|
    | **Erőforráscsoport** | myResourceGroup | Ugyanazt az erőforráscsoportot használja minden olyan szolgáltatáshoz, amely az oktatóanyagban szóba kerül.|

4. Kattintson a **Létrehozás** gombra az erőforrás létrehozásához. Miután létrejött, válassza ki az irányítópultra rögzített új Cognitive Services-erőforrást. 

5. A bal oldali navigációs oszlopban kattintson a **Kulcsok** elemre, majd másolja ki az **1. kulcs** értékét, és mentse el. Ezzel a kulccsal tudja csatlakoztatni a logikai alkalmazást a Cognitive Services API-hoz. 
 
    ![Kulcsok](media/functions-twitter-email/keys.png)

## <a name="create-the-function-app"></a>A függvényalkalmazás létrehozása

A függvények kiváló módot nyújtanak egy logikai alkalmazás munkafolyamatában található feldolgozási feladatok kiszervezésére. Ez az oktatóanyag HTTP által aktivált függvényt használ a tweetek Cognitive Servicesből származó véleménypontszámának feldolgozására és egy kategóriaérték visszaadására.  

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-an-http-triggered-function"></a>HTTP által aktivált függvény létrehozása  

1. Bontsa ki a függvényalkalmazást, és kattintson a **Függvények** elem melletti **+** gombra. Ha ez az első függvény a függvényalkalmazásban, jelölje ki az **Egyéni függvény** lehetőséget. Ez megjeleníti a függvénysablonok teljes készletét.

    ![Függvények gyors létrehozásának oldala az Azure Portalon](media/functions-twitter-email/add-first-function.png)

2. A keresés mezőbe írja be a `http` kifejezést, majd válassza ki a **C#** nyelvet a HTTP-eseményindító sablonjához. 

    ![HTTP-eseményindító kiválasztása](./media/functions-twitter-email/select-http-trigger-portal.png)

3. Adja meg a függvény **nevét**, válassza ki a `Function` elemet **[hitelesítési szintként](functions-bindings-http-webhook.md#http-auth)**, majd válassza a **Létrehozás** gombot. 

    ![A HTTP által aktivált függvény létrehozása](./media/functions-twitter-email/select-http-trigger-portal-2.png)

    Ez a művelet egy C#-szkriptfüggvényt hoz létre a HTTP trigger sablonjával. A kód egy új ablakban jelenik meg, mint `run.csx`.

4. Cserélje le a `run.csx` fájl tartalmát a következő kódra, majd kattintson a **Mentés** gombra:

    ```csharp
    using System.Net;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // The sentiment category defaults to 'GREEN'. 
        string category = "GREEN";
    
        // Get the sentiment score from the request body.
        double score = await req.Content.ReadAsAsync<double>();
        log.Info(string.Format("The sentiment score received is '{0}'.",
                    score.ToString()));
    
        // Set the category based on the sentiment score.
        if (score < .3)
        {
            category = "RED";
        }
        else if (score < .6)
        {
            category = "YELLOW";
        }
        return req.CreateResponse(HttpStatusCode.OK, category);
    }
    ```
    Ez a függvénykód visszaad egy színkategóriát a kérésben kapott véleménypontszám alapján. 

4. A függvény teszteléséhez kattintson a jobb szélen a **Tesztelés** gombra, amely kibontja a tesztelési lapot. A **Kérelem törzse** mezőben adja meg a `0.2` értéket, majd kattintson a **Futtatás** parancsra. A válasz törzse a **RED** (Vörös) értéket adja vissza. 

    ![A függvény tesztelése az Azure Portalon](./media/functions-twitter-email/test.png)

Ezzel létrehozott egy olyan függvényt, amely kategorizálja a véleménypontszámokat. Most hozzon létre egy logikai alkalmazást, amely a függvényt integrálja a Twitterrel és a Cognitive Services API-val. 

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása   

1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.

2. Kattintson az **Enterprise Integration** > **Logic App** elemre. Használja a táblázatban megadott beállításokat, jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, és kattintson a **Létrehozás** gombra.
 
4. Ezután írjon be egy **nevet** (például `TweetSentiment`), használja a táblázatban megadott beállításokat, fogadja el a feltételeket, és jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet.

    ![A logikai alkalmazás létrehozása az Azure Portalon](./media/functions-twitter-email/new_logic_app.png)

    | Beállítás      |  Ajánlott érték   | Leírás                                        |
    | ----------------- | ------------ | ------------- |
    | **Name (Név)** | TweetSentiment | Válasszon egy megfelelő nevet az alkalmazáshoz. |
    | **Erőforráscsoport** | myResourceGroup | Válassza ki a korábban is használt meglévő erőforráscsoportot. |
    | **Hely** | USA keleti régiója | Válassza ki az Önhöz legközelebb eső helyet. |    

4. Válassza a **Rögzítés az irányítópulton** lehetőséget, majd kattintson a **Létrehozás** gombra a logikai alkalmazás létrehozásához. 

5. Az alkalmazás létrehozása után kattintson az irányítópultra rögzített új logikai alkalmazásra. Ezután a Logic Apps Designerben görgessen lefelé, és kattintson az **Üres logikai alkalmazás** sablonra. 

    ![Üres logikai alkalmazás sablon](media/functions-twitter-email/blank.png)

A Logic Apps Designerben szolgáltatásokat és triggereket adhat az alkalmazáshoz.

## <a name="connect-to-twitter"></a>Csatlakozás a Twitterhez

Először kapcsolatot kell létesíteni a Twitter-fiókjával. A logikai alkalmazás lekérdezi a tweeteket, amelyek kiváltják az alkalmazás futtatását.

1. A tervezőben kattintson a **Twitter** szolgáltatásra, és kattintson az **Új tweet közzétételekor** eseményindítóra. Jelentkezzen be a Twitter-fiókjába, és engedélyezze, hogy a Logic Apps használhassa a fiókját.

2. A Twitter-triggerekhez a táblázatban megadott beállításokat használja. 

    ![A Twitter-összekötő beállításai](media/functions-twitter-email/azure_tweet.png)

    | Beállítás      |  Ajánlott érték   | Leírás                                        |
    | ----------------- | ------------ | ------------- |
    | **Keresett szöveg** | #Azure | Olyan hashtaget használjon, amely elég népszerű ahhoz, hogy a választott időszakban új Twitter-üzenetek vonatkozzanak rá. Amikor az ingyenes szintet használja és a hashtag túl népszerű, gyorsan felhasználhatja a Cognitive Services API tranzakciókvótáját. |
    | **Gyakoriság** | Perc | A tweetek lekérdezési gyakoriságának mértékegysége.  |
    | **Intervallum** | 15 | A Twitter-kérelmek között eltelt idő, a gyakoriság mértékegységében megadva. |

3.  Kattintson a **Mentés** gombra, hogy csatlakozzon a Twitter-fiókjához. 

Ezzel csatlakoztatta az alkalmazást és a Twittert. Most a szövegelemzést kell csatlakoztatnia a begyűjtött tweetek hangulatának megállapítása érdekében.

## <a name="add-sentiment-detection"></a>Hangulatfelismerés hozzáadása

1. Kattintson az **Új lépés**, majd a **Művelet hozzáadása** elemre.

    ![Új lépés, majd Művelet hozzáadása](media/functions-twitter-email/new_step.png)

2. A **műveletek választására szolgáló** területen kattintson a **Text Analytics** lehetőségre, majd a **Hangulat felismerése** műveletre.

    ![Hangulat felismerése](media/functions-twitter-email/detect_sent.png)

3. Írjon be egy kapcsolatnevet (például `MyCognitiveServicesConnection`), illessze be a Cognitive Services API mentett kulcsát, és kattintson a **Létrehozás** gombra.  

4. Kattintson az **Elemzendő szöveg** > **Tweet szövege** lehetőségre, majd kattintson a **Mentés** gombra.  

    ![Hangulat felismerése](media/functions-twitter-email/ds_tta.png)

Most, hogy konfigurálta a hangulatfelismerést, kapcsolatot adhat a véleménypontszám kimenetét feldolgozó függvényhez.

## <a name="connect-sentiment-output-to-your-function"></a>Hangulatkimenet csatlakoztatása a függvényhez

1. A Logic Apps Designerben kattintson az **Új lépés** > **Művelet hozzáadása** elemre, majd az **Azure Functions** lehetőségre. 

2. Kattintson a **Válasszon ki egy Azure-függvényt** lehetőségre, és válassza a korábban létrehozott **CategorizeSentiment** függvényt.  

    ![Azure Function-mező a Válasszon ki egy Azure-függvényt lehetőséggel](media/functions-twitter-email/choose_fun.png)

3. A **Kérelem törzsében** kattintson a **Pontszám**, majd a **Mentés** lehetőségre.

    ![Pontszám](media/functions-twitter-email/trigger_score.png)

Mostantól aktiválódik a függvény, amikor a logikai alkalmazás átküld egy véleménypontszámot. A függvény egy színkódolt kategóriát küld vissza a logikai alkalmazásnak. A következő lépés egy e-mail-értesítés megadása, amelyet a rendszer akkor küld el, ha a függvény **Piros** értékű véleményt küld vissza. 

## <a name="add-email-notifications"></a>E-mail-értesítések hozzáadása

A munkafolyamat utolsó része egy e-mail-értesítés küldésének kiváltása, ha a vélemény a _Piros_ kategóriába sorolódik. Ez a témakör egy Outlook.com-összekötőt használ. Gmail- vagy Office 365 Outlook-összekötők esetén hasonló lépéseket kell elvégezni.   

1. A Logic Apps Designerben kattintson az **Új lépés** > **Feltétel hozzáadása** lehetőségre. 

2. Kattintson a **Válasszon egy értéket**, majd a **Törzs** elemre. Válassza ki az **egyenlő** lehetőséget, kattintson a **Válasszon egy értéket** elemre, írja be a `RED` értéket, és kattintson a **Mentés** gombra. 

    ![Feltétel hozzáadása a logikai alkalmazáshoz.](media/functions-twitter-email/condition.png)

3. A **HA IGAZ** elemnél kattintson a **Művelet hozzáadása** lehetőségre, keresse meg az `outlook.com` elemet, kattintson az **E-mail küldése** műveletre, majd jelentkezzen be Outlook.com-fiókjába.
    
    ![Művelet választása a feltételhez.](media/functions-twitter-email/outlook.png)

    > [!NOTE]
    > Ha nincs Outlook.com-fiókja, választhat egy másik, például Gmail- vagy Office 365 Outlook-összekötőt.

4. Az **E-mail küldése** művelethez használja a táblázatban megadott e-mail-beállításokat. 

    ![E-mail konfigurálása az e-mailt elküldő művelethez.](media/functions-twitter-email/send_email.png)

    | Beállítás      |  Ajánlott érték   | Leírás  |
    | ----------------- | ------------ | ------------- |
    | **Címzett** | Írja be az e-mail-címét | Az e-mail-cím, amelyre megérkezik az értesítés. |
    | **Tárgy** | A tweetek által tükrözött vélemény kedvezőtlen  | Az e-mail-értesítés tárgysora.  |
    | **Törzs** | Tweet szövege, Hely | Kattintson a **Tweet szövege** és a **Hely** paraméterre. |

5.  Kattintson a **Save** (Mentés) gombra.

Most, hogy a munkafolyamat befejeződött, engedélyezheti a logikai alkalmazást, és megtekintheti a függvényt működés közben.

## <a name="test-the-workflow"></a>A munkafolyamat tesztelése

1. A Logic App Designerben kattintson a **Futtatás** gombra az alkalmazás elindításához.

2. A bal oldali oszlopban kattintson az **Áttekintés** elemre a logikai alkalmazás állapotának megtekintéséhez. 
 
    ![A logikai alkalmazás végrehajtási állapota](media/functions-twitter-email/over1.png)

3. (Opcionális) Kattintson valamelyik futtatásra a végrehajtás részleteinek megtekintéséhez.

4. Lépjen a függvényhez, tekintse át a naplókat, és győződjön meg arról, hogy a véleményértékeket a rendszer fogadta és feldolgozta.
 
    ![A függvény naplóinak megtekintése](media/functions-twitter-email/sent.png)

5. Amikor a rendszer feltehetően kedvezőtlen véleményt észlel, e-mailt küld Önnek. Ha nem kapott e-mailt, módosíthatja a függvénykódot, hogy mindig RED (piros) értéket adjon vissza:

        return req.CreateResponse(HttpStatusCode.OK, "RED");

    Miután ellenőrizte, hogy az e-mail-értesítések működnek-e, állítsa vissza az eredeti kódot:

        return req.CreateResponse(HttpStatusCode.OK, category);

    > [!IMPORTANT]
    > Amikor végzett az oktatóanyaggal, érdemes letiltani a logikai alkalmazást. Ha letiltja az alkalmazást, elkerülheti, hogy díjat kelljen fizetnie a végrehajtásokért, és hogy felhasználja az összes Cognitive Services API-tranzakciót.

Láthatta, milyen egyszerűen integrálható a Functions a Logic Apps-munkafolyamatokba.

## <a name="disable-the-logic-app"></a>A logikai alkalmazás letiltása

A logikai alkalmazás letiltásához kattintson az **Áttekintés**, majd a képernyő tetején lévő **Letiltás** gombra. Ez leállítja a logikai alkalmazás futtatását és a vele járó költségeket anélkül, hogy törölni kéne az alkalmazást. 

![Függvénynaplók](media/functions-twitter-email/disable-logic-app.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Egy Cognitive Services API-erőforrás létrehozása.
> * Olyan függvény létrehozása, amely kategorizálja a tweetek hangulatát.
> * A Twitterhez csatlakozó logikai alkalmazás létrehozása.
> * A hangulatfelismerés hozzáadása a logikai alkalmazáshoz. 
> * A logikai alkalmazás csatlakoztatása a függvényhez.
> * E-mail küldése a függvénytől érkező válasz alapján.

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre kiszolgáló nélküli API-t a függvényeihez.

> [!div class="nextstepaction"] 
> [Kiszolgáló nélküli API létrehozása az Azure Functions használatával](functions-create-serverless-api.md)

A Logic Apps szolgáltatással kapcsolatos további információkért lásd: [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

