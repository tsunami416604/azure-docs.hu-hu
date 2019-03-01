---
title: Az Azure kiszolgáló nélküli áttekintése |} A Microsoft Docs
description: Hatékony megoldások létrehozása a felhőben infrastruktúrával bajlódnia
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 9cd1946d8fa670764bcc95b82298d07cc70417bd
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57191323"
---
# <a name="overview-azure-serverless-with-azure-logic-apps-and-azure-functions"></a>Áttekintés: Az Azure Logic Apps és az Azure Functions kiszolgáló nélküli Azure

[Kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) alkalmazások fejlesztési sebességének növekedése, a szükséges kódot, és egyszerűen méretezhető csökkenése előnyöket kínálják.  Ez a cikk a különböző attribútumokat, kiszolgáló nélküli megoldások és az Azure kiszolgáló nélküli ajánlatai hiányzóra változik.

## <a name="what-is-serverless"></a>Mi a kiszolgáló nélküli?

Kiszolgáló nélküli nem jelenti azt, nem találhatók kiszolgálók – csupán annyit, a fejlesztő nem kell aggódnia a kiszolgálók.  A hagyományos alkalmazásfejlesztés nagy része skálázási, üzemeltetési és figyelési megoldások, hogy a videólejátszást az alkalmazás körül kérdéseket válaszol.  A kiszolgáló nélküli ezeket a kérdéseket is elvégzi, a megoldás részeként.  Ezenkívül kiszolgáló nélküli alkalmazások fogyasztásalapú csomag számítjuk fel.  Soha ne használja az alkalmazást, ha soha nem merül fel díjat.  Ezek a funkciók lehetővé teszik a fejlesztők számára, hogy az üzleti logika, a megoldás kizárólag a.

Az alapvető szolgáltatások az Azure-beli kiszolgáló nélküli körül [Azure Functions](https://azure.microsoft.com/services/functions/) és [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).  Mindkét ezeket a megoldásokat, kövesse a fenti elvek, és lehetővé teszi a fejlesztők számára csak minimális kódírásra robusztus felhőalkalmazások létrehozását.

## <a name="what-are-azure-functions"></a>Mik az Azure Functions?

Az Azure Functions megoldással egyszerűen futtathatók kisebb kódrészletek, más néven „függvények”, a felhőben. Elég, ha a szóban forgó problémára vonatkozó kódot megírja, nem kell egy egész alkalmazással vagy futtató infrastruktúrával bajlódnia. Függvények legyen még hatékonyabb, és használhatja a fejlesztési nyelvet választott, mint például C#, F#, Node.js, Python vagy PHP. Csak a kód futtatásának idejéért kell fizetnie, és az Azure méretezhető, igény szerint.

Ha szeretne rögtön az Azure Functions használatának első lépéseihez ugrani, kezdje [Az első Azure-függvény létrehozása](../azure-functions/functions-create-first-azure-function.md) résszel. Ha a Functions szolgáltatással kapcsolatos további műszaki információkat keres, lépjen a [fejlesztői segédanyagok](../azure-functions/functions-reference.md) részhez.

## <a name="what-are-azure-logic-apps"></a>Mik az Azure Logic Apps?

Az Azure Logic Apps lehetővé teszi egyszerűsíthetők és valósíthatók meg skálázható munkafolyamatok és integrációk a felhőben. Biztosít egy vizuális tervezővel és automatizálhatók a folyamat egy munkafolyamat nevű lépések sorozataként.  Nincsenek [számos összekötő](../connectors/apis-list.md) felhőbeli és helyszíni szolgáltatásokat a kiszolgáló nélküli alkalmazás egyszerűen csatlakozhat más API-k között.  Egy logikai alkalmazás egy eseményindítóval kezdődik (például „amikor egy fiókot hozzáadnak a Dynamics CRM-hez”), és az indítás után kezdődhet számos műveleti, konverziós és feltétellogikai kombináció.  A Logic Apps remek választás a következőkhöz esetén replikálásával segít a vállalatnak különböző Azure Functions egy folyamatban – különösen akkor, ha a folyamat igényel egy külső rendszer vagy az API használata.

A Logic Apps használatának első lépései, kezdje [az első logikai alkalmazás létrehozása](quickstart-create-first-logic-app-workflow.md).  Ha a Logic Apps szolgáltatással kapcsolatos további műszaki információkat keres, tekintse meg a [– fejlesztői referencia](logic-apps-workflow-actions-triggers.md).

## <a name="how-can-i-build-and-deploy-serverless-applications-in-azure"></a>Hogyan hozhat létre és üzembe helyezése az Azure-beli kiszolgáló nélküli alkalmazásokat?

Az Azure egy eszközök széles skáláját biztosítja a fejlesztési, üzembe helyezési és felügyeleti kiszolgáló nélküli alkalmazások között.  Alkalmazások építhetők fel, közvetlenül az Azure Portalon, vagy a [hibakeresését a Visual Studióból](logic-apps-serverless-get-started-vs.md).  Ha egy alkalmazás fejlesztettek ki lehet [azonnal üzembe helyezett](logic-apps-create-deploy-template.md).  Az Azure is kiszolgáló nélküli alkalmazások figyelését teszi lehetővé.  A monitorozás elérhető lesz az Azure portal, az API vagy SDK-k, vagy az integrált eszközök, az Azure Monitor naplóira és az Application Insights.

## <a name="next-steps"></a>További lépések

* [A Visual Studióban egy kiszolgáló nélküli alkalmazás létrehozásának első lépései](logic-apps-serverless-get-started-vs.md)
* [Kiszolgáló nélküli a customer insights irányítópult létrehozása](logic-apps-scenario-social-serverless.md)
* [A központi telepítési sablont a logikai alkalmazás létrehozása](logic-apps-create-deploy-template.md)
