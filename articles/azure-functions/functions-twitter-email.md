---
title: Az Azure Logic Apps szolgáltatással integrálható függvények létrehozása
description: Létrehozhat egy függvényt, amely integrálható az Azure Logic Apps és az Azure Cognitive Services szolgáltatással a tweetek hangulatának kategorizálásához és értesítések küldéséhez, amennyiben azok kedvezőtlen véleményt tükröznek.
services: functions, logic-apps, cognitive-services
keywords: munkafolyamat, felhőalapú alkalmazások, felhőszolgáltatások, üzleti folyamatok, rendszerintegráció, vállalati alkalmazásintegráció, EAI
author: craigshoemaker
manager: jeconnoc
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: azure-functions
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: cshoe
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 4c9f92f80275d04cd1bab408213fd02abf5c9139
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51279398"
---
# <a name="create-a-function-that-integrates-with-azure-logic-apps"></a>Az Azure Logic Apps szolgáltatással integrálható függvények létrehozása

Az Azure Functions integrálható az Azure Logic Apps szolgáltatással a Logic Apps Designerben. Ez az integráció lehetővé teszi a Functions számítási teljesítményének kihasználását, amikor egyéb Azure- és külső szolgáltatásokkal dolgozik. 

Az oktatóanyag bemutatja, hogyan használható a Functions, a Logic Apps és a Cognitive Services az Azure-on a Twitter-bejegyzések hangulatelemzésének futtatásához. A HTTP által aktivált függvények zöld, sárga vagy piros színnel kategorizálják a tweeteket a véleménypontszám alapján. A rendszer e-mailt küld, amikor kedvezőtlen véleményt észlel. 

![az alkalmazás első két lépésének képe a Logic App Designerben](media/functions-twitter-email/00-logic-app-overview.png)

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
+ A témakör [Az első függvény létrehozása az Azure Portalon](functions-create-first-azure-function.md) című cikkben létrehozott erőforrásokat használja kiindulópontként.  
Hajtsa végre az itt található lépéseket a függvényalkalmazás létrehozásához, ha eddig még nem tette meg.

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services-erőforrás létrehozása

A Cognitive Services API-k egyéni erőforrásként érhetők el az Azure-ban. A Text Analytics API-t használja a monitorozott tweetek hangulatának megállapításához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

3. Kattintson az **AI + Machine Learning** > **Text Analytics** elemre. Ezután használja a táblázatban megadott beállításokat az erőforrás létrehozásához.

    ![Cognitive-erőforrás létrehozása oldal](media/functions-twitter-email/01-create-text-analytics.png)

    | Beállítás      |  Ajánlott érték   | Leírás                                        |
    | --- | --- | --- |
    | **Name (Név)** | MyCognitiveServicesAccnt | Válasszon egy egyedi fióknevet. |
    | **Hely** | USA nyugati régiója | Az Önhöz legközelebbi helyet használja. |
    | **Tarifacsomag** | F0 | Kezdjen a legalacsonyabb szinttel. Ha kifogy a hívásokból, lépjen magasabb szintre.|
    | **Erőforráscsoport** | myResourceGroup | Ugyanazt az erőforráscsoportot használja minden olyan szolgáltatáshoz, amely az oktatóanyagban szóba kerül.|

4. Kattintson a **Létrehozás** gombra az erőforrás létrehozásához. 

5. Kattintson az **Áttekintés** elemre, majd másolja a **Végpont** mező értékét egy szövegszerkesztőbe. Erre az értékre akkor van szükség, amikor kapcsolatot létesít a Cognitive Services API-val.

    ![Cognitive Services-beállítások](media/functions-twitter-email/02-cognitive-services.png)

6. A bal oldali navigációs oszlopban kattintson a **Kulcsok** elemre, majd másolja az **1. kulcs** mező értékét, és illessze be egy szövegszerkesztőbe. Ezzel a kulccsal csatlakoztathatja a logikai alkalmazást a Cognitive Services API-hoz. 
 
    ![Cognitive Services-kulcsok](media/functions-twitter-email/03-cognitive-serviecs-keys.png)

## <a name="create-the-function-app"></a>A függvényalkalmazás létrehozása

A függvények kiváló módot nyújtanak egy logikai alkalmazás munkafolyamatában található feldolgozási feladatok kiszervezésére. Ez az oktatóanyag HTTP által aktivált függvényt használ a tweetek Cognitive Servicesből származó véleménypontszámának feldolgozására és egy kategóriaérték visszaadására.  

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-an-http-triggered-function"></a>HTTP által aktivált függvény létrehozása  

1. Bontsa ki a függvényalkalmazást, és kattintson a **Függvények** elem melletti **+** gombra. Ha ez az első függvény a függvényalkalmazásban, válassza a **Portálba épített** elemet.

    ![Függvények gyors létrehozásának oldala az Azure Portalon](media/functions-twitter-email/05-function-app-create-portal.png)

2. Ezután válassza a **Webhook + API** elemet, majd kattintson a **Létrehozás** gombra. 

    ![HTTP-eseményindító kiválasztása](./media/functions-twitter-email/06-function-webhook.png)

3. Cserélje le a `run.csx` fájl tartalmát a következő kódra, majd kattintson a **Mentés** gombra:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        string category = "GREEN";
    
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        log.LogInformation(string.Format("The sentiment score received is '{0}'.", requestBody));
    
        double score = Convert.ToDouble(requestBody);
    
        if(score < .3)
        {
            category = "RED";
        }
        else if (score < .6) 
        {
            category = "YELLOW";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(category)
            : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    }
    ```
    Ez a függvénykód visszaad egy színkategóriát a kérésben kapott véleménypontszám alapján. 

4. A függvény teszteléséhez kattintson a jobb szélen a **Tesztelés** gombra, amely kibontja a tesztelési lapot. A **Kérelem törzse** mezőben adja meg a `0.2` értéket, majd kattintson a **Futtatás** parancsra. A válasz törzse a **RED** (Vörös) értéket adja vissza. 

    ![A függvény tesztelése az Azure Portalon](./media/functions-twitter-email/07-function-test.png)

Ezzel létrehozott egy olyan függvényt, amely kategorizálja a véleménypontszámokat. Most hozzon létre egy logikai alkalmazást, amely a függvényt integrálja a Twitterrel és a Cognitive Services API-val. 

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása   

1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.

2. Kattintson a **Web** > **Logikai alkalmazás** elemre.
 
3. Ezután írjon be egy értéket a **Név** mezőbe (például: `TweetSentiment`), és használja a táblázatban megadott beállításokat.

    ![A logikai alkalmazás létrehozása az Azure Portalon](./media/functions-twitter-email/08-logic-app-create.png)

    | Beállítás      |  Ajánlott érték   | Leírás                                        |
    | ----------------- | ------------ | ------------- |
    | **Name (Név)** | TweetSentiment | Válasszon egy megfelelő nevet az alkalmazáshoz. |
    | **Erőforráscsoport** | myResourceGroup | Válassza ki a korábban is használt meglévő erőforráscsoportot. |
    | **Hely** | USA keleti régiója | Válassza ki az Önhöz legközelebb eső helyet. |    

4. Miután megadta a megfelelő beállításértékeket, kattintson a **Létrehozás** gombra a logikai alkalmazás létrehozásához. 

5. Az alkalmazás létrehozása után kattintson az irányítópultra rögzített új logikai alkalmazásra. Ezután a Logic Apps Designerben görgessen lefelé, és kattintson az **Üres logikai alkalmazás** sablonra. 

    ![Üres logikai alkalmazás sablon](media/functions-twitter-email/09-logic-app-create-blank.png)

A Logic Apps Designerben szolgáltatásokat és triggereket adhat az alkalmazáshoz.

## <a name="connect-to-twitter"></a>Csatlakozás a Twitterhez

Először kapcsolatot kell létesíteni a Twitter-fiókjával. A logikai alkalmazás lekérdezi a tweeteket, amelyek kiváltják az alkalmazás futtatását.

1. A tervezőben kattintson a **Twitter** szolgáltatásra, és kattintson az **Új tweet közzétételekor** eseményindítóra. Jelentkezzen be a Twitter-fiókjába, és engedélyezze, hogy a Logic Apps használhassa a fiókját.

2. A Twitter-triggerekhez a táblázatban megadott beállításokat használja. 

    ![A Twitter-összekötő beállításai](media/functions-twitter-email/10-tweet-settings.png)

    | Beállítás      |  Ajánlott érték   | Leírás                                        |
    | ----------------- | ------------ | ------------- |
    | **Keresett szöveg** | #Azure | Olyan hashtaget használjon, amely elég népszerű ahhoz, hogy a választott időszakban új Twitter-üzenetek vonatkozzanak rá. Amikor az ingyenes szintet használja és a hashtag túl népszerű, gyorsan felhasználhatja a Cognitive Services API tranzakciókvótáját. |
    | **Intervallum** | 15 | A Twitter-kérelmek között eltelt idő, a gyakoriság mértékegységében megadva. |
    | **Gyakoriság** | Perc | A tweetek lekérdezési gyakoriságának mértékegysége.  |

3.  Kattintson a **Mentés** gombra, hogy csatlakozzon a Twitter-fiókjához. 

Ezzel csatlakoztatta az alkalmazást és a Twittert. Most a szövegelemzést kell csatlakoztatnia a begyűjtött tweetek hangulatának megállapítása érdekében.

## <a name="add-sentiment-detection"></a>Hangulatfelismerés hozzáadása

1. Kattintson az **Új lépés**, majd a **Művelet hozzáadása** elemre.

2. A **műveletek választására szolgáló** területen írja be a **Text Analytics** kifejezést, majd kattintson a **Hangulat felismerése** műveletre.
    
    ![Új lépés, majd Művelet hozzáadása](media/functions-twitter-email/11-detect-sentiment.png)

3. Írjon be egy kapcsolatnevet (például: `MyCognitiveServicesConnection`), illessze be a Cognitive Services API kulcsát és a Cognitive Services végpontját a szövegszerkesztőből, majd kattintson a **Létrehozás** gombra.

    ![Új lépés, majd Művelet hozzáadása](media/functions-twitter-email/12-connection-settings.png)

4. Írja be a **Tweet szövege** értéket a szövegmezőbe, majd kattintson az **Új lépés** elemre.

    ![Az elemezni kívánt szöveg meghatározása](media/functions-twitter-email/13-analyze-tweet-text.png)

Most, hogy konfigurálta a hangulatfelismerést, kapcsolatot adhat a véleménypontszám kimenetét feldolgozó függvényhez.

## <a name="connect-sentiment-output-to-your-function"></a>Hangulatkimenet csatlakoztatása a függvényhez

1. A Logic Apps Designerben kattintson az **Új lépés** > **Művelet hozzáadása** elemre, szűrjön rá az **Azure Functions** lehetőségre, majd kattintson a **Válasszon ki egy Azure-függvényt** elemre.

    ![Hangulat felismerése](media/functions-twitter-email/14-azure-functions.png)
  
4. Válassza ki a korábban létrehozott függvényalkalmazást.

    ![Függvény kiválasztása](media/functions-twitter-email/15-select-function.png)

5. Válassza ki az oktatóanyaghoz létrehozott függvényt.

    ![Függvény kiválasztása](media/functions-twitter-email/16-select-function.png)

4. A **Kérelem törzsében** kattintson a **Pontszám**, majd a **Mentés** lehetőségre.

    ![Pontszám](media/functions-twitter-email/17-function-input-score.png)

Mostantól aktiválódik a függvény, amikor a logikai alkalmazás átküld egy véleménypontszámot. A függvény egy színkódolt kategóriát küld vissza a logikai alkalmazásnak. A következő lépés egy e-mail-értesítés megadása, amelyet a rendszer akkor küld el, ha a függvény **RED** értékű véleményt küld vissza. 

## <a name="add-email-notifications"></a>E-mail-értesítések hozzáadása

A munkafolyamat utolsó része egy e-mail-értesítés küldésének kiváltása, ha a vélemény a _RED_ kategóriába sorolódik. Ez a témakör egy Outlook.com-összekötőt használ. Gmail- vagy Office 365 Outlook-összekötők esetén hasonló lépéseket kell elvégezni.   

1. A Logic Apps Designerben kattintson az **Új lépés** > **Feltétel hozzáadása** lehetőségre. 

    ![Feltétel hozzáadása a logikai alkalmazáshoz.](media/functions-twitter-email/18-add-condition.png)

2. Kattintson a **Válasszon egy értéket**, majd a **Törzs** elemre. Válassza ki az **egyenlő** lehetőséget, kattintson a **Válasszon egy értéket** elemre, írja be a `RED` értéket, és kattintson a **Mentés** gombra. 

    ![Művelet választása a feltételhez.](media/functions-twitter-email/19-condition-settings.png)    

3. A **HA IGAZ** elemnél kattintson a **Művelet hozzáadása** lehetőségre, keresse meg az `outlook.com` elemet, kattintson az **E-mail küldése** műveletre, majd jelentkezzen be Outlook.com-fiókjába.

    ![E-mail konfigurálása az e-mailt elküldő művelethez.](media/functions-twitter-email/20-add-outlook.png)

    > [!NOTE]
    > Ha nincs Outlook.com-fiókja, választhat egy másik, például Gmail- vagy Office 365 Outlook-összekötőt.

4. Az **E-mail küldése** művelethez használja a táblázatban megadott e-mail-beállításokat. 

    ![E-mail konfigurálása az e-mailt elküldő művelethez.](media/functions-twitter-email/21-configure-email.png)
    
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
 
    ![A logikai alkalmazás végrehajtási állapota](media/functions-twitter-email/22-execution-history.png)

3. (Opcionális) Kattintson valamelyik futtatásra a végrehajtás részleteinek megtekintéséhez.

4. Lépjen a függvényhez, tekintse át a naplókat, és győződjön meg arról, hogy a véleményértékeket a rendszer fogadta és feldolgozta.
 
    ![A függvény naplóinak megtekintése](media/functions-twitter-email/sent.png)

5. Amikor a rendszer feltehetően kedvezőtlen véleményt észlel, e-mailt küld Önnek. Ha nem kapott e-mailt, módosíthatja a függvénykódot, hogy mindig RED (piros) értéket adjon vissza:

    ```csharp
    return (ActionResult)new OkObjectResult("RED");
    ```

    Miután ellenőrizte, hogy az e-mail-értesítések működnek-e, állítsa vissza az eredeti kódot:

    ```csharp
    return requestBody != null
        ? (ActionResult)new OkObjectResult(category)
        : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    ```

    > [!IMPORTANT]
    > Amikor végzett az oktatóanyaggal, érdemes letiltani a logikai alkalmazást. Ha letiltja az alkalmazást, elkerülheti, hogy díjat kelljen fizetnie a végrehajtásokért, és hogy felhasználja az összes Cognitive Services API-tranzakciót.

Láthatta, milyen egyszerűen integrálható a Functions a Logic Apps-munkafolyamatokba.

## <a name="disable-the-logic-app"></a>A logikai alkalmazás letiltása

A logikai alkalmazás letiltásához kattintson az **Áttekintés**, majd a képernyő tetején lévő **Letiltás** gombra. Az alkalmazás letiltása leállítja a futtatását és a vele járó költségeket anélkül, hogy törölni kellene az alkalmazást.

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

