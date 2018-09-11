---
title: Kiszolgáló nélküli forgatókönyv – customer insights irányítópult létrehozása az Azure-szolgáltatásokkal |} A Microsoft Docs
description: Ügyfeleink visszajelzései, közösségi adatok és egyéb, az Azure Logic Apps és az Azure Functions ügyfél irányítópult létrehozásával kezelése
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: 6ba274bb3ff3679b4a44950db168215c54f3ade6
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299746"
---
# <a name="create-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Az Azure Logic Apps és az Azure Functions streamelési customer insights irányítópult létrehozása

Az Azure kiszolgáló nélküli eszközöket kínál, amelyek segítségével gyorsan hozhat létre és alkalmazásokat a felhőben, nem kell foglalkoznia infrastruktúra. Ebben az oktatóanyagban létrehozhat egy irányítópultot, amely elindítja az ügyfelek visszajelzései alapján elemzi a machine learning visszajelzés és elemzéseket tesz közzé egy forrás, például a Power bi-ban vagy az Azure Data Lake.

Ebben a megoldásban a legfontosabb Azure-összetevőket használata kiszolgáló nélküli alkalmazások: [Azure Functions](https://azure.microsoft.com/services/functions/) és [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).
Az Azure Logic Apps biztosít egy kiszolgáló nélküli munkafolyamat-motor a felhőben, így a vezénylések létrehozásához a kiszolgáló nélküli összetevői között, és több mint 200 services és API-hoz csatlakozhat. A biztosít az Azure Functions kiszolgáló nélküli számítási feladatokat a felhőben. Ez a megoldás az Azure Functions használ megjelölése ügyfél tweetek előre megadott kulcsszavak alapján.

Ebben a forgatókönyvben egy logikai alkalmazást fog létrehozni, amely visszajelzés keresi az ügyfelektől. Egyes összekötők, hogy az ügyfelek visszajelzései alapján válaszol súgó tartalmazzák Outlook.com-os, az Office 365-höz, a felmérés alfaja, Twitter, és a egy [HTTP-kérelem egy webes űrlap](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/). A munkafolyamat, amely létrehoz egy, a Twitteren hashtag figyeli.

Is [a Visual Studióban a teljes megoldás létrehozásához](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) és [a megoldás az Azure Resource Manager-sablon üzembe helyezéséhez](../logic-apps/logic-apps-create-deploy-template.md). A video-útmutatót, amely bemutatja, hogyan hozhat létre ebben a megoldásban a [tekintse meg a Channel 9 videót](http://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Eseményindítás a vásárlói adatok

1. Az Azure Portalon vagy a Visual Studióban hozzon létre egy üres logikai alkalmazás. 

   Ha most ismerkedik a logic apps, tekintse át a [az Azure Portalon a rövid útmutató](../logic-apps/quickstart-create-first-logic-app-workflow.md) vagy a [rövid útmutató a Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. Logic App Designerben keresse meg és adja hozzá a Twitter-eseményindítót, amely rendelkezik ezzel a művelettel: **egy új tweet közzétételekor**

3. Állítsa be az eseményindító figyelni a tweetek kulcsszó vagy hashtaggel alapján.

   A lekérdezés-alapú eseményindítók, például a Twitter-eseményindítót a recurrence tulajdonság határozza meg, hogy a logikai alkalmazás milyen gyakran keres új elemeket.

   ![Twitter-eseményindítót – példa][1]

Ez a logikai alkalmazás most már az összes új tweetekről következik be. Ezután igénybe vehet, és a tweet adatok elemzéséhez, így jobban megérthetők a hangulati kifejezve. 

## <a name="analyze-tweet-text"></a>Tweet szövege elemzése

A hangulat felismerése valamilyen szöveget mögött, használhatja [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

1. A Logic App Designerben az eseményindító területén válassza a **új lépés**.

2. Keresse meg a **Szövegelemzés** összekötő.

3. Válassza ki a **észlelése vélemények** művelet.

4. Ha a rendszer kéri, adja meg egy érvényes Cognitive Services-kulcsot a Text Analytics szolgáltatás.

5. A **kérelem törzse**, jelölje be a **Tweet szövege** mező, amely biztosítja a tweet szövege bemenetként elemzéshez.

Miután a tweet adatait információival és a Twitter-üzenetet kap, számos más releváns összekötők és a lépések most már használhatja:

* **Power BI - Streamelési adatkészletet sorok hozzáadása**: bejövő tweetek megtekintése a Power BI-irányítópulton.
* **Az Azure Data Lake - fájl hozzáfűzése**: hozzáadása az Azure Data Lake-adatkészlet az analytics-feladatok közé tartozik a vásárlói adatokat.
* **SQL - sorok hozzáadása**: data Store adatbázisban a későbbi beolvasásához.
* **Slack - üzenet küldése**: értesítés a negatív visszajelzés küldésére, beavatkozást igénylő Slack csatorna.

Is létrehozhat, és a egy Azure működik, hogy egyéni feldolgozási végezheti el az adatokat. 

## <a name="process-data-with-azure-functions"></a>Dolgozza fel az adatokat az Azure Functions használatával

Létrehozhat egy függvényt, mielőtt a függvényalkalmazás létrehozása az Azure-előfizetésében. Emellett a logikai alkalmazás közvetlenül egy függvény meghívásához, a függvény rendelkeznie kell egy HTTP-trigger kötést, például, használja a **HttpTrigger** sablont. Ismerje meg, [hogyan hozhat létre az első függvényalkalmazást és függvényt az Azure Portalon](../azure-functions/functions-create-first-azure-function-azure-portal.md).

Ebben a forgatókönyvben használni az Azure-függvény, a kérelem törzsében a tweet szövegét. A függvénykód adja meg a logikát, amely meghatározza, hogy a tweet szövegét egy kulcsszót vagy kifejezést tartalmaz-e. Tartsa meg a függvény egyszerű vagy összetett, mint a forgatókönyvhöz szükséges.
A függvény végén választ a logikai alkalmazás adatokkal, például, egy egyszerű logikai érték például `containsKeyword` vagy egy összetett objektumot.

> [!TIP]
> Összetett választ egy függvény a Logic Apps-alkalmazás elérésére, a **JSON elemzése** művelet.

Ha elkészült, mentse a függvényt, és hozzáadhatja a függvény létrehozásakor a logikai alkalmazásban műveletként.

## <a name="add-azure-function-to-logic-app"></a>Azure-függvény hozzáadása a logikai alkalmazáshoz

1. Logic App Designerben alatt a **észlelése vélemények** műveletet, válassza a **új lépés**.

2. Keresse meg a **Azure Functions** összekötő, és válassza ki a létrehozott függvényt.

3. A **kérelem törzse**válassza **Tweet szövege**.

![Konfigurált Azure-függvény. lépés][2]

## <a name="run-and-monitor-your-logic-app"></a>Futtathatja és figyelheti a logikai alkalmazás

Tekintse át a logikai alkalmazás jelenlegi vagy korábbi futtatásokat, a Hibakeresés és figyelési képességeket, amelyek az Azure Logic Apps biztosít az Azure Portalon, a Visual Studióban vagy az Azure REST API-k és SDK-k gazdag is használhatja.

A logikai alkalmazást, egyszerűen tesztelheti a Logic App Designerben, válassza a **Trigger futtatása**. Az eseményindító lekérdezi a tweeteket, amíg nem talál egy tweet, amely megfelel a feltételeknek a megadott ütemezés alapján. A futtatási különböző fázisokon halad, miközben a tervezőben a futó élő képet jeleníti meg.

Előző nézet futtatási előzmények a Visual Studióban vagy az Azure Portalon: 

* Nyissa meg a Visual Studio Cloud Explorer. Keresse meg a logikai alkalmazást, az alkalmazás helyi menü megnyitásához. Válassza ki **nyílt futtatási előzmények**.

* Az Azure Portalon keresse meg a logikai alkalmazást. A logikai alkalmazás menüjében válassza **áttekintése**. 

## <a name="create-automated-deployment-templates"></a>Automatizált üzembehelyezési sablonok létrehozása

Miután létrehozott egy logic app-megoldás, rögzítése, és telepítse az alkalmazást egy [Azure Resource Manager-sablon](../azure-resource-manager/resource-group-overview.md#template-deployment) a világ bármely Azure-régióba. Ez a funkció mindkét segítségével módosítsa a paramétereket az alkalmazás különböző verzióit létrehozása és a megoldás integrálása az Azure-folyamatok. A központi telepítési sablont az Azure Functions is tartalmazza, így a teljes megoldás az összes függőség egyetlen sablonként kezelheti. Ismerje meg, [hogyan hozhat létre a logikai alkalmazás központi telepítési sablonok](../logic-apps/logic-apps-create-deploy-template.md).

Egy példa központi telepítési sablont a egy Azure-függvényt, ellenőrizze a [Azure gyors üzembe helyezés sablontár](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>További lépések

* [Keresse meg az egyéb példák és forgatókönyvek az Azure Logic Apps](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png