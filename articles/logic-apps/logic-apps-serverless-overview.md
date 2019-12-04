---
title: Áttekintés – Azure kiszolgáló nélküli
description: Hatékony megoldásokat hozhat létre a felhőben anélkül, hogy az infrastruktúrával kellene foglalkoznia
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: d658efd278425358a2a931fe976827c3bae4247d
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792826"
---
# <a name="overview-azure-serverless-with-azure-logic-apps-and-azure-functions"></a>Áttekintés: Azure-kiszolgáló nélküli Azure Logic Apps és Azure Functions

A [kiszolgáló](https://azure.microsoft.com/solutions/serverless/) nélküli alkalmazások olyan előnyöket kínálnak, mint például a fejlesztési sebesség, a kevesebb kód, az egyszerűség és a skála. Ez a cikk a kiszolgáló nélküli megoldások és az Azure kiszolgáló nélküli ajánlatok különböző attribútumait ismerteti.

## <a name="what-is-serverless"></a>Mi az a kiszolgáló nélküli?

A kiszolgáló nélküli nem jelenti azt, hogy nincsenek kiszolgálók, hanem a fejlesztőknek nem kell aggódniuk a kiszolgálókon. A hagyományos alkalmazásfejlesztés nagy része az alkalmazás igényeinek megfelelő méretezési, üzemeltetési és figyelési megoldásokkal kapcsolatos kérdéseket válaszol. A kiszolgáló nélküli megoldások a megoldás részeként gondoskodnak a kérdésekről. Továbbá a kiszolgáló nélküli alkalmazások számlázása egy fogyasztáson alapuló terv alapján történik. Ha az alkalmazást soha nem használják, a rendszer díjmentesen felmerül. Ezek a funkciók segítséget nyújtanak a fejlesztőknek kizárólag a megoldás üzleti logikájában való fókuszban.

A kiszolgáló nélküli alapszintű Azure-szolgáltatások [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/) és [Azure functions](https://azure.microsoft.com/services/functions/). Mindkét megoldás a korábban leírt alapelveket követi, és segít a fejlesztőknek, hogy robusztus felhőalapú alkalmazásokat hozzanak létre minimális kóddal.

## <a name="what-is-azure-logic-apps"></a>Mi az Azure Logic Apps?

A [Azure Logic apps](logic-apps-overview.md) a felhőben méretezhető integrációk és munkafolyamatok egyszerűsítését és megvalósítását teszi lehetővé. Ez a szolgáltatás egy vizuális tervezőt biztosít a folyamat modellezéséhez és automatizálásához, mint egy munkafolyamatnak nevezett lépések sorozata. Számos [összekötő](../connectors/apis-list.md) található a Cloud Services és a helyszíni rendszerek között, amelyek gyorsan összekapcsolják a kiszolgáló nélküli alkalmazást más API-kkal. Minden logikai alkalmazás egy triggerrel kezdődik, például "Ha egy fiókot hozzáadnak a Dynamics CRM-hez". Az eseményindító elindítása után a munkafolyamat műveletek, konverziók és feltételes logika kombinációit futtathatja. A Logic Apps nagyszerű választás, ha különböző Azure Functionsokat dolgoz fel egy folyamat során, különösen akkor, ha a folyamat külső rendszerrel vagy API-val való interakciót igényel.

Az Logic Apps első lépéseinek megkezdéséhez [először hozzon létre egy logikai alkalmazást](quickstart-create-first-logic-app-workflow.md). A Logic Appsával kapcsolatos további technikai információkért tekintse meg a [fejlesztői referenciát](logic-apps-workflow-definition-language.md).

## <a name="what-is-azure-functions"></a>Mi az az Azure Functions?

Azure Functions egy szolgáltatás a felhőben található kódok vagy függvények egyszerű futtatásához. Csak az aktuális problémához szükséges kódot írhat, és nem kell aggódnia a teljes alkalmazásról vagy a szükséges infrastruktúráról. A függvények még hatékonyabbá tehetik a fejlesztést, és használhatja a választott fejlesztési nyelvet, például: C#, F#Node. js, Python vagy php. Csak a kód futásának idejére kell fizetnie, és szükség szerint az Azure-ra is szüksége van.

A Azure Functions első lépéseinek megkezdéséhez [először hozzon létre egy Azure-függvényt](../azure-functions/functions-create-first-azure-function.md). A functions szolgáltatással kapcsolatos további technikai információkért tekintse meg a [fejlesztői referenciát](../azure-functions/functions-reference.md).

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Hogyan lehet kiszolgáló nélküli alkalmazásokat felépíteni és üzembe helyezni az Azure-ban?

Az Azure sokoldalú eszközöket biztosít a kiszolgáló nélküli alkalmazások fejlesztéséhez, üzembe helyezéséhez és kezeléséhez. Az alkalmazásokat közvetlenül a Azure Portal, a [Visual Studióban](logic-apps-serverless-get-started-vs.md)vagy a [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)-ban található eszközökkel hozhatja létre. Az alkalmazás létrehozása után az [alkalmazást gyorsan üzembe helyezheti Azure Resource Manager-sablonokkal](logic-apps-deploy-azure-resource-manager-templates.md). Az Azure emellett figyelést is biztosít, amely a Azure Portalon, az API-n vagy SDK-n keresztül, vagy a Azure Monitor naplók és Application Insights integrált eszközeivel érhető el.

## <a name="next-steps"></a>Következő lépések

* [Kiszolgáló nélküli alkalmazás létrehozása a Visual Studióban](logic-apps-serverless-get-started-vs.md)
* [Ügyfél-áttekintési irányítópult létrehozása kiszolgáló nélküli](logic-apps-scenario-social-serverless.md)
* [A logikai alkalmazások telepítésének automatizálása](logic-apps-azure-resource-manager-templates-overview.md)
