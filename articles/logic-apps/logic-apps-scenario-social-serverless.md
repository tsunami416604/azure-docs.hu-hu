---
title: Kiszolgáló nélküli forgatókönyv – a felhasználói irányítópult létrehozása az Azure-ral |} Microsoft Docs
description: Ismerje meg, hogyan kezelheti a ügyfelek visszajelzései alapján, közösségi adatok és sok más felépítése az Azure Logic Apps és az Azure Functions felhasználói irányítópult
keywords: ''
services: logic-apps
author: jeffhollan
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: jehollan; LADocs
ms.openlocfilehash: 3ee3ec3107cf8aad834e8201405c9aa833d838af
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299960"
---
# <a name="create-a-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Az Azure Logic Apps és az Azure Functions egy adatfolyam-továbbítási felhasználói irányítópult létrehozása

Azure kínál kiszolgáló nélküli eszközök, amelyek segítségével gyorsan build és a fogadó alkalmazások a felhőben, gondolja át infrastruktúra nélkül. Ebben az oktatóanyagban létrehozhat egy irányítópultot, amely elindítja az ügyfelek visszajelzései alapján, elemzi a machine Learning segítségével visszajelzés és elemzések közzéteszi a forrás, például a Power bi-ban vagy az Azure Data Lake.

Ebben a megoldásban a legfontosabb Azure összetevők használja kiszolgáló nélküli alkalmazások: [Azure Functions](https://azure.microsoft.com/services/functions/) és [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).
Az Azure Logic Apps biztosít egy kiszolgáló nélküli munkafolyamat-motor a felhőben, így álló üzenettípusok összehangolását létrehozása a kiszolgáló nélküli összetevői között, és csatlakoztassa 200 + szolgáltatások és API-k. Az Azure Functions biztosít a kiszolgáló nélküli számítógép-használatról a felhőben. A megoldás az Azure Functions az ügyfél Twitter-üzenetek alapján előre definiált kulcsszavak megjelölése.

Ebben a forgatókönyvben az ügyfél visszajelzés megkereséséről hoz létre, amely elindítja a logikai alkalmazás. Egyes összekötők, hogy az ügyfelek visszajelzései alapján válaszolhat súgó tartalmaz Outlook.com-os, az Office 365, a felmérést Monkey, Twitter, és egy [egy webes űrlap a HTTP-kérelem](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/). Az Ön által létrehozott munkafolyamat figyeli egy hashtaggel történő a Twitteren.

Is [a Visual Studio a teljes megoldás létrehozásához](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) és [üzembe helyezéséhez az Azure Resource Manager sablon](../logic-apps/logic-apps-create-deploy-template.md). A video-útmutatót, amely bemutatja, hogyan hozzon létre ebben a megoldásban a [a Channel 9 videót](http://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Indítás, a felhasználói adatok

1. Az Azure portálon vagy a Visual Studio létrehoz egy üres logikai alkalmazást. 

   Ha most ismerkedik a logic apps, tekintse át a [gyors üzembe helyezés az Azure portál](../logic-apps/quickstart-create-first-logic-app-workflow.md) vagy a [első lépések a Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. Logic App Designer található, és adja hozzá a Twitter eseményindító, amely rendelkezik ezzel a művelettel: **amikor egy új tweetet visszaküldi**

3. Állítsa be az eseményindító a Twitter-üzenetek alapján kulcsszó vagy hashtaggel történő figyelésére.

   A lekérdezési alapú eseményindítók, például a Twitter eseményindító az ismétlődési tulajdonság határozza meg, milyen gyakran kérdezze le a logikai alkalmazás új elemek.

   ![Twitter eseményindító – példa][1]

A logikai alkalmazás most már az összes új Twitter-üzeneteket következik be. Majd igénybe vehet, és a tweetet adatok elemzésére, így jobban megismerheti a kifejezett hangulati elemek. 

## <a name="analyze-tweet-text"></a>Tweetet szöveg elemzése

Észleli a céggel kapcsolatos véleményeket szöveget mögött, használhatja [Azure kognitív szolgáltatások](https://azure.microsoft.com/services/cognitive-services/).

1. A Logic App Designer alapján az eseményindító válassza **új lépés**.

2. Keresés a **Szövegelemzések** összekötő.

3. Válassza ki a **észlelése céggel kapcsolatos véleményeket** művelet.

4. Ha a rendszer kéri, adja meg a szöveg Analytics szolgáltatás érvényes kognitív szolgáltatások kulcs.

5. A **kérelem törzse**, jelölje be a **Tweetet szöveg** mező, amely az szövegét tweetet bemenetként elemzés céljából.

Miután beszerezte a tweetet adatok és a tweetet észrevételeket, számos egyéb kapcsolódó összekötők és a lépések most már használhatja:

* **Power BI - sorok hozzáadása a folyamatos átviteli adatkészletet**: bejövő Twitter-üzeneteket megtekintheti a Power BI-irányítópultot.
* **Azure Data Lake - fájl hozzáfűzése**: felhasználói adatok hozzáadása egy Azure Data Lake analytics-feladatok szerepeljenek a DataSet adatkészlethez.
* **SQL - sorok felvételének**: adatok tárolása a későbbi beolvasásához adatbázis.
* **Slack - üzenet küldése**: értesítés negatív visszajelzés beavatkozást igénylő Slack csatornát.

Is létrehozhat, és az Azure működnek, hogy az adatokról egyéni feldolgozási végezheti el. 

## <a name="process-data-with-azure-functions"></a>Az Azure Functions adatfeldolgozásra

Mielőtt létrehozna egy olyan függvényt, az függvény alkalmazás létrehozása az Azure-előfizetéshez. Emellett a logikai alkalmazásnak közvetlenül egy függvény meghívásához, a függvénynek rendelkeznie kell egy HTTP-kötés, például Indítás, használja a **HttpTrigger** sablont. Ismerje meg, [első függvény alkalmazása és függvény létrehozása az Azure portálon](../azure-functions/functions-create-first-azure-function-azure-portal.md).

Ebben az esetben használja a tweetet szöveget kérelemtörzsként való az az Azure-függvény. A függvény kódban adja meg a programot, amely meghatározza, hogy a tweetet szöveg tartalmazza-e a kívánt kulcsszót vagy kifejezést. A függvény megtartása egyszerűek vagy összetettek, mint a forgatókönyvhöz szükséges.
A függvény végén válaszol a logikai alkalmazás adatokkal, például, egy egyszerű logikai érték, mint `containsKeyword` vagy egy összetett objektumot.

> [!TIP]
> Összetett választ hozzáférést a logikai alkalmazás egy függvényt, használja a **elemzése JSON** művelet.

Amikor elkészült, mentse a függvény, és adja meg a függvény a logikai alkalmazásban, amelyet most felépítése műveletet.

## <a name="add-azure-function-to-logic-app"></a>A logikai alkalmazást az Azure függvény hozzáadása

1. Logic App Designer alatt a **észlelése céggel kapcsolatos véleményeket** művelet, válassza a **új lépés**.

2. Keresés a **Azure Functions** összekötő, és válassza ki a létrehozott függvény.

3. A **kérelem törzse**, jelölje be **Tweetet szöveg**.

![Konfigurált Azure-függvény lépés][2]

## <a name="run-and-monitor-your-logic-app"></a>Futtathatja és figyelheti a Logic Apps alkalmazást

Tekintse át a aktuális vagy korábbi futtatása a logikai alkalmazásnak, a Hibakeresés és figyelési képességeket kínál, amelyek az Azure Logic Apps biztosít az Azure portálon, a Visual Studio, vagy az Azure REST API-k és SDK-k segítségével gazdag is használhat.

Könnyen tesztelje a Logic Apps alkalmazást Logic App-tervezőben, válassza a **futtatása eseményindító**. Az eseményindító lekérdezi a megadott ütemezés alapján, amíg meg nem találja egy tweetet, a feltételeknek megfelelő Twitter-üzeneteket. A futtatási időtartamára, miközben a Tervező jeleníti meg, az élő a rendszert futtató.

Előző nézet a Visual Studio vagy az Azure-portál alábbi előzményeinek futtassa: 

* Nyissa meg a Visual Studio Cloud Explorer. A Logic Apps alkalmazást található, nyissa meg az alkalmazás helyi menü. Válassza ki **nyílt futtatási előzményei**.

* Az Azure-portálon keresse meg a Logic Apps alkalmazást. A Logic Apps alkalmazást menüben válasszon **áttekintése**. 

## <a name="create-automated-deployment-templates"></a>Automatizált üzembehelyezési sablonok létrehozása

Miután létrehozta a logic app megoldás, rögzítése, és az alkalmazást egy [Azure Resource Manager sablon](../azure-resource-manager/resource-group-overview.md#template-deployment) a világ bármely Azure régióban. Segítségével ezt a képességet is módosítsa az alkalmazás különböző verzióinak létrehozásának és a megoldás integrálása build paramétereket, és felszabadíthatja a folyamatot. A központi telepítési sablont az Azure Functions is tartalmazza, így a teljes megoldás összes függőségét, egyetlen sablonként kezelheti. Ismerje meg, [logic app központi telepítési sablonok létrehozása](../logic-apps/logic-apps-create-deploy-template.md).

Egy példa központi telepítési sablont a egy Azure-függvény, ellenőrizze a [Azure gyors üzembe helyezés sablon tárház](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>További lépések

* [Az Azure Logic Apps más példák és forgatókönyvek keresése](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png