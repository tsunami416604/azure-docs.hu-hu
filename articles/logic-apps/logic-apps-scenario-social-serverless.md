---
title: Ügyfélelemzési irányítópult létrehozása
description: Az Azure Logic Apps és az Azure Functions segítségével ügyfél-irányítópult létrehozásával kezelheti az ügyfelek visszajelzéseit, a közösségi média adatait és még sok mást
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: e300bf9c9aa0acf0bed6426eb73f690f9a38bd74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980429"
---
# <a name="create-a-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Az Azure Logic Apps és az Azure Functions segítségével streamelt ügyfélelemzési irányítópult létrehozása

Az Azure [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) eszközöket kínál, amelyek segítségével gyorsan hozhat létre és üzemeltetheti az alkalmazásokat a felhőben anélkül, hogy az infrastruktúrára kellene gondolnia. Ebben az oktatóanyagban létrehozhat egy irányítópultot, amely elindítja az ügyfelek visszajelzéseit, elemzi a visszajelzéseket a gépi tanulással, és elemzéseket tesz közzé egy forrásban, például a Power BI-ban vagy az Azure Data Lake-ben.

Ehhez a megoldáshoz ezeket a kulcsfontosságú Azure-összetevőket használja a kiszolgáló nélküli alkalmazásokhoz: [az Azure Functionshez](https://azure.microsoft.com/services/functions/) és [az Azure Logic Apps alkalmazásokhoz.](https://azure.microsoft.com/services/logic-apps/)
Az Azure Logic Apps egy kiszolgáló nélküli munkafolyamat-motort biztosít a felhőben, így vezényléseket hozhat létre kiszolgáló nélküli összetevők között, és több mint 200 szolgáltatáshoz és API-hoz csatlakozhat. Az Azure Functions kiszolgáló nélküli számítástechnikát biztosít a felhőben. Ez a megoldás az Azure Functions függvényt használja az ügyféltweetek megjelöléséhez előre definiált kulcsszavak alapján.

Ebben a forgatókönyvben hozzon létre egy logikai alkalmazást, amely elindítja az ügyfelek visszajelzéseinek keresését. Egyes összekötők, amelyek segítenek az ügyfelek visszajelzéseinek megválaszolásában, közétartozik a Outlook.com, az Office 365, a Survey Monkey, a Twitter és [egy webes űrlaphttp-kérése.](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/) A létrehozott munkafolyamat egy hashtaget figyel a Twitteren.

A [teljes megoldást a Visual Studióban hozhatja létre,](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) és [az Azure Resource Manager-sablonnal telepítheti](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)a megoldást. A megoldás létrehozásának módját bemutató videóbemutatóhoz [tekintse meg ezt a Channel 9 videót.](https://aka.ms/logicappsdemo) 

## <a name="trigger-on-customer-data"></a>Eseményindító az ügyféladatokon

1. Az Azure Portalon vagy a Visual Studio hozzon létre egy üres logikai alkalmazást. 

   Ha most kezdi meg a logikai alkalmazásokat, tekintse át [az Azure Portal rövid útmutatóját](../logic-apps/quickstart-create-first-logic-app-workflow.md) vagy a Visual Studio rövid [útmutatóját.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

2. A Logic App Designer, keresse meg, és adja hozzá a Twitter eseményindító, amely ezt a műveletet: **Ha egy új csipog van kifüggesztett**

3. Állítsa be az eseményindítót, hogy egy kulcsszó vagy hashtag alapján figyelje a tweeteket.

   A lekérdezési alapú eseményindítók, például a Twitter eseményindító, az ismétlődési tulajdonság határozza meg, hogy milyen gyakran a logikai alkalmazás ellenőrzi az új elemeket.

   ![Példa a Twitter trigger][1]

Ez a logikai alkalmazás most antól minden új tweetet meggyújt. Ezután vegye és elemezze a tweet adatokat, hogy jobban megértse a kifejezett érzelmeket. 

## <a name="analyze-tweet-text"></a>Tweet szövegének elemzése

Ha észlelni szeretné a szöveg mögötti érzelmeket, használhatja az [Azure Cognitive Services szolgáltatást.](https://azure.microsoft.com/services/cognitive-services/)

1. A Logic App Designer programban az eseményindító alatt válassza az **Új lépés lehetőséget.**

2. Keresse meg a Text Analytics-összekötőt. **Text Analytics**

3. Válassza a **Hangulat észlelése** műveletet.

4. Ha a rendszer kéri, adjon meg egy érvényes Cognitive Services-kulcsot a Text Analytics szolgáltatáshoz.

5. A **Kérelem törzse**csoportban jelölje ki a **Tweet szövege mezőt,** amely elemzéshez a tweet szövegét adja meg bevitelként.

Miután megkapja a tweet adatait és a tweetekkel kapcsolatos elemzéseket, most már számos más releváns összekötőt és azok tetteit is használhatja:

* **Power BI – Sorok hozzáadása a streamelési adatkészlethez:** A bejövő tweetek megtekintése a Power BI irányítópultján.
* **Azure Data Lake – Hozzáfűzésfájl:** Ügyféladatok hozzáadása egy Azure Data Lake-adatkészlethez, hogy az elemzési feladatokba is bekerülj.
* **SQL - Sorok hozzáadása**: Adatok tárolása adatbázisban későbbi lekéréshez.
* **Tartalékidő – Üzenet küldése**: Értesítés a Laza csatornáról a negatív visszajelzésekről, amelyek beavatkozást igényelhetnek.

Létrehozhat és létrehozhat egy Azure-függvényt is, hogy egyéni feldolgozást hajthass végre az adatokon. 

## <a name="process-data-with-azure-functions"></a>Adatok feldolgozása az Azure Functions segítségével

Mielőtt létrehozna egy függvényt, hozzon létre egy függvényalkalmazást az Azure-előfizetésében. Ahhoz is, hogy a logikai alkalmazás közvetlenül meghívja a függvényt, http-eseményindító-kötéssel kell **rendelkeznie,** például a HttpTrigger sablon használatával. Ismerje [meg, hogyan hozhatja létre az első függvényalkalmazást és -függvényt az Azure Portalon.](../azure-functions/functions-create-first-azure-function-azure-portal.md)

Ebben a forgatókönyvben használja a tweet szövegét az Azure-függvény kéréstörzseként. A függvénykódban határozza meg azt a logikát, amely meghatározza, hogy a tweet szövege tartalmaz-e kulcsszót vagy kifejezést. Tartsa a függvényt olyan egyszerű vagy összetett, mint a forgatókönyvhöz szükséges.
A függvény végén adjon vissza választ a logikai alkalmazásra néhány adattal, például `containsKeyword` egy egyszerű logikai értékkel, például egy összetett objektummal.

> [!TIP]
> Összetett válasz eléréséhez egy függvény egy logikai alkalmazásban, használja a **Parse JSON** művelet.

Ha elkészült, mentse a függvényt, majd adja hozzá a függvényt műveletként az éppen építkező logikai alkalmazásban.

## <a name="add-azure-function-to-logic-app"></a>Azure-függvény hozzáadása a logikai alkalmazáshoz

1. A Logic App Designer ben a **Hangulat észlelése** művelet csoportban válassza az Új **lépés lehetőséget.**

2. Keresse meg az Azure Functions-összekötőt, majd válassza ki a létrehozott függvényt. **Azure Functions**

3. A **Kérelem törzse csoportban**válassza a **Tweet szövege lehetőséget.**

![Konfigurált Azure-függvény lépés][2]

## <a name="run-and-monitor-your-logic-app"></a>A logikai alkalmazás futtatása és figyelése

A logikai alkalmazás jelenlegi vagy korábbi futtatások áttekintéséhez használhatja az Azure Logic Apps által az Azure Portalon, a Visual Studio- ban vagy az Azure REST API-kon és SDK-kban biztosított részletes hibakeresési és figyelési lehetőségeket.

A logikai alkalmazás egyszerű teszteléséhez a Logic App Designer alkalmazásban válassza az **Eseményindító futtatása lehetőséget.** A tweetek eseményindítószavazása a megadott ütemezés alapján, amíg nem talál egy olyan tweetet, amely megfelel a feltételeknek. A futtatás előrehaladtával a tervező egy élő nézetet jelenít meg az adott futtatáshoz.

Korábbi futtatási előzmények megtekintése a Visual Studio-ban vagy az Azure Portalon: 

* Nyissa meg a Visual Studio Cloud Explorer t. Keresse meg a logikai alkalmazást, nyissa meg az alkalmazás helyi menüjét. Válassza **a Futtatási előzmények megnyitása**lehetőséget.

  > [!TIP]
  > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a Visual Studio legújabb frissítéseivel.

* Az Azure Portalon keresse meg a logikai alkalmazást. A logikai alkalmazás menüjében válassza **az Áttekintés**lehetőséget. 

## <a name="create-automated-deployment-templates"></a>Automatizált üzembehelyezési sablonok létrehozása

Miután létrehozott egy logikai alkalmazásmegoldást, rögzítheti és üzembe helyezheti az alkalmazást [Azure Resource Manager-sablonként](../azure-resource-manager/templates/overview.md) a világ bármely Azure-régiójában. Ezt a funkciót használhatja egyaránt az alkalmazás különböző verzióinak létrehozásához szükséges paraméterek módosításához, valamint a megoldás Azure-folyamatokba való integrálásához. Az Azure Functions a központi telepítési sablonban is szerepelhet, így a teljes megoldást egyetlen sablonként kezelheti az összes függőséggel. Ismerje meg, hogyan [automatizálható a logikai alkalmazások központi telepítése.](logic-apps-azure-resource-manager-templates-overview.md)

Egy Azure-függvénnyel rendelkező központi telepítési sablonpéldául tekintse meg az [Azure gyorsindítási sablontárját.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic)

## <a name="next-steps"></a>További lépések

* [További példák és forgatókönyvek keresése az Azure Logic Apps alkalmazáshoz](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png
