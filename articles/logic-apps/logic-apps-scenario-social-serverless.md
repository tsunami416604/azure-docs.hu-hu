---
title: "Forgatókönyv – a felhasználói irányítópult létrehozása az Azure kiszolgáló nélküli |} Microsoft Docs"
description: "Hogyan kezelheti az ügyfelek visszajelzései alapján, közösségi adatok és további Azure Logic Apps és az Azure Functions egy irányítópultot hozhat létre egy példát."
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: jehollan
ms.openlocfilehash: d3e07b8d7194d83e3ba3986177170edff21e1d7a
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="create-a-real-time-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Az Azure Logic Apps és az Azure Functions egy valós idejű felhasználói irányítópult létrehozása

Azure-kiszolgáló nélküli eszközök biztosítanak a hatékony funkciókat gyorsan készíthet, és nem kell gondolniuk infrastruktúra alkalmazásaikat a felhőben tárolni.  Ebben a forgatókönyvben létrehozunk indul el, az ügyfelek visszajelzései, visszajelzés machine Learning segítségével elemezheti és elemzések közzététele irányítópulton Power bi-ban vagy az Azure Data Lake ki forrást.

## <a name="overview-of-the-scenario-and-tools-used"></a>A forgatókönyv és a használt eszközök áttekintése

Ez a megoldás megvalósításához azt fogja használni, a két legfontosabb összetevők, a kiszolgáló nélküli alkalmazások az Azure-ban: [Azure Functions](https://azure.microsoft.com/services/functions/) és [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).

A Logic Apps egy kiszolgáló nélküli munkafolyamat-motor a felhőben.  Vezénylési biztosít a kiszolgáló nélküli összetevői között, és több mint 100 szolgáltatások és API-kat is csatlakozik.  A jelen esetben létre fogunk hozni egy logikai alkalmazást az ügyfelek visszajelzései indításához.  Az összekötők, amelyek segítik az ügyfelek visszajelzései alapján reagál a többek között Outlook.com-os, az Office 365, a felmérést Monkey, a Twitter és a HTTP-kérelem [egy webes űrlap](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/).  Az alábbi munkafolyamat azt figyeli egy hashtaggel történő a Twitteren.

Funkciók adja meg a kiszolgáló nélküli számítási a felhőben.  Ebben a forgatókönyvben az Azure Functions segítségével jelzőt Twitter-üzeneteket az ügyfelektől, előre definiált kulcsszavakat sorozata alapján.

A teljes megoldás lehet [a Visual Studio build](logic-apps-deploy-from-vs.md) és [erőforrás sablon részeként](logic-apps-create-deploy-template.md).  Szerepel továbbá forgatókönyv bemutató videó [a Channel 9](http://aka.ms/logicappsdemo).

## <a name="build-the-logic-app-to-trigger-on-customer-data"></a>Az ügyféladatok elindítani a logikai alkalmazás létrehozása

Miután [logikai alkalmazás létrehozása](quickstart-create-first-logic-app-workflow.md) a Visual Studio vagy az Azure-portálon:

1. Adja hozzá az eseményindító **az új Twitter-üzeneteket** a Twitteren
2. Az eseményindító Twitter-üzeneteket kulcsszó vagy hashtaggel történő figyelésére konfigurálni.

   > [!NOTE]
   > Az eseményindító ismétlődési tulajdonsága határozza meg, hogy milyen gyakran a logikai alkalmazás ellenőrzi a lekérdezési alapú eseményindítók új elemek

   ![Twitter eseményindító – példa][1]

Ez az alkalmazás most már az összes új Twitter-üzeneteket fog érvényesítést.  Azt majd tweetet adatok igénybe vehet, és részletesebb a céggel kapcsolatos véleményeket kifejezve.  A használjuk a [Azure kognitív szolgáltatás](https://azure.microsoft.com/services/cognitive-services/) szöveg véleményeket észleléséhez.

1. Kattintson a **új lépés**
1. Válassza ki, vagy keresse meg a **Szövegelemzések** összekötő
1. Válassza ki a **észlelése céggel kapcsolatos véleményeket** művelet
1. Ha a rendszer kéri, adja meg egy érvényes kognitív szolgáltatások kulcsát a Szövegelemzések szolgáltatás
1. Adja hozzá a **Tweetet szöveg** , mint a szöveg elemzésére.

Most, hogy az tweetet adatok, és az elemzések a a tweetet, más összekötőket vonatkozó lehet:
* Power BI - sorok hozzáadása a folyamatos átviteli adatkészletet: valós idejű egy Power BI-irányítópult nézet Twitter-üzeneteket.
* Azure Data Lake - fájl hozzáfűzése: felhasználói adatok hozzáadása egy Azure Data Lake analytics-feladatok szerepeljenek a DataSet adatkészlethez.
* SQL - sorok felvételének: adatok tárolása a későbbi beolvasásához adatbázis.
* Slack - üzenetet küldeni: egy közzététele a slack-csatornát negatív visszajelzés műveletek igénylő riasztást.

Egy Azure-függvény is használható felett az adatokat több egyéni számítás elvégzéséhez.

## <a name="enriching-the-data-with-an-azure-function"></a>Az adatokat egy Azure-függvényt a bővítése

Azt is hozzon létre egy függvényt, igazolnia kell egy függvény alkalmazást az Azure-előfizetésben rendelkezik.  Egy Azure-függvény létrehozása a portálon részleteket is [itt található](../azure-functions/functions-create-first-azure-function-azure-portal.md)

Egy függvény hívása közvetlenül a logikai alkalmazás, HTTP rendelkeznie kell elindítani a kötés.  Azt javasoljuk, a **HttpTrigger** sablont.

Ebben a forgatókönyvben a kérés törzsében az Azure-függvény lenne a tweetet szöveget.  A függvény kódban egyszerűen határozza meg programot, ha a tweetet szöveg tartalmazza-e egy kulcsszót vagy kifejezést.  A függvény önmagára sikerült megmarad, ennek egyszerűek vagy összetettek, a forgatókönyv konfigurálásához szükséges.

A függvény végén egyszerűen válaszol a logikai alkalmazás adatokkal.  Ennek oka lehet egy egyszerű logikai értéket (pl. `containsKeyword`), vagy egy összetett objektumot.

![Konfigurált Azure-függvény lépés][2]

> [!TIP]
> Egy logikai alkalmazás függvényt az összetett választ elérésekor művelettel a JSON elemezni.

A funkció megmarad, ha azokat az előbb létrehozott logikai alkalmazás vehető fel.  A logikai alkalmazásban:

1. Hozzáadásához kattintson ide a **új lépés**
1. Válassza ki a **Azure Functions** összekötő
1. Válasszon egy meglévő függvényben, és keresse meg a létrehozott függvény kiválasztása
1. Küldjön a **Tweetet szöveg** a a **kérelem törzse**

## <a name="running-and-monitoring-the-solution"></a>Futtatnia vagy felügyelnie a megoldás

A jelentéskészítő kiszolgáló nélküli álló üzenettípusok összehangolását a Logic Apps előnyeit egyik a hatékony hibakeresési és a figyelési lehetőségek körét.  A Futtatás (jelenlegi vagy történelmi) tekintheti meg a Visual studióban, az Azure-portálon, vagy a REST API-t és az SDK-k segítségével.

Egyik legegyszerűbb módja logikai alkalmazás teszteléséhez használja a **futtatása** gomb a tervezőben.  Kattintson a **futtatása** továbbra is kérdezze le az eseményindító minden 5 másodperc, amíg a rendszer észlelt egy eseményt, és a futási előrehaladtával egy élő betekintést.

Előző futtatási alábbi előzményeinek az Áttekintés panel az Azure-portálon, vagy a Visual Studio Cloud Explorer használatával tekintheti meg.

## <a name="creating-a-deployment-template-for-automated-deployments"></a>Az automatikus központi telepítés központi telepítési sablon létrehozása

Ha olyan megoldást fejlesztett ki, rögzített, és egy Azure központi telepítési sablont a világ bármely Azure régióban keresztül telepíthetők.  Ez akkor hasznos, mindkét módosítása paraméter különböző a munkafolyamat-verziót, hanem is használható az ebben a megoldásban egy build és kiadás folyamat.  A központi telepítési sablonok létrehozásának részletes ismertetése [ebben a cikkben](logic-apps-create-deploy-template.md).

Az Azure Functions is be lehessen építeni a központi telepítési sablont -, a teljes megoldás az összes függősége kezelhető egyetlen sablont.  Példa függvény a központi telepítési sablont itt található: a [Azure gyors üzembe helyezés sablon tárház](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>További lépések

* [Tekintse meg az egyéb példák és forgatókönyvek az Azure Logic Apps](logic-apps-examples-and-scenarios.md)
* [Tekintse meg a video-útmutatót a megoldás-végpontok létrehozásáról](http://aka.ms/logicappsdemo)
* [Megtudhatja, hogyan dolgozza fel a logikai alkalmazások kivételeket és kezelésére](logic-apps-exception-handling.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png