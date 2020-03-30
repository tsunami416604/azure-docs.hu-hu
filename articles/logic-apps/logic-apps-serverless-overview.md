---
title: Áttekintés – Azure Serverless felhőalapú alkalmazásokhoz és megoldásokhoz
description: Ismerje meg, hogyan hozhat létre felhőalapú alkalmazásokat és megoldásokat anélkül, hogy aggódnia kellene az infrastruktúra miatt az Azure Logic Apps és az Azure Functions használatával
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 0f20bb5fb249ad6bac862afe2b0e8eee4b32e2a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666550"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure Serverless: Áttekintés felhőalapú alkalmazások és megoldások létrehozásához az Azure Logic Apps és az Azure Functions segítségével

[A kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) alkalmazások olyan előnyöket kínálnak, mint a nagyobb fejlesztési sebesség, a csökkentett kód, az egyszerűség és a méretezés. Ez a cikk a kiszolgáló nélküli megoldások és az Azure kiszolgáló nélküli ajánlatok különböző attribútumait ismerteti.

## <a name="what-is-serverless"></a>Mi az a kiszolgáló nélküli?

A Kiszolgáló nélküli nem jelenti azt, hogy nincsenek szerverek, hanem a fejlesztőknek nem kell aggódniuk a kiszolgálók miatt. A hagyományos alkalmazásfejlesztés nagy része a skálázási, üzemeltetési és figyelési megoldásokra vonatkozó kérdések megválaszolása az alkalmazás igényeinek megfelelően. A kiszolgáló nélküli, ezeket a kérdéseket gondoskodnak a megoldás részeként. Emellett a kiszolgáló nélküli alkalmazások számlázása a fogyasztásalapú csomag. Ha az alkalmazást soha nem használják, nem számítunk fel díjat. Ezek a funkciók segítik a fejlesztőket abban, hogy kizárólag a megoldás üzleti logikájára összpontosítsanak.

A kiszolgáló nélküli alapvető Azure-szolgáltatások az [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) és az Azure [Functions.](https://azure.microsoft.com/services/functions/) Mindkét megoldás a korábban leírt alapelveket követi, és segít a fejlesztőknek robusztus felhőalkalmazások at minimális kóddal építeni.

## <a name="what-is-azure-logic-apps"></a>Mi az Azure Logic Apps?

[Az Azure Logic Apps](logic-apps-overview.md) leegyszerűsítheti és megvalósíthatja a méretezhető integrációkat és munkafolyamatokat a felhőben. Ez a szolgáltatás vizuális tervezőt biztosít a folyamat modellezéséhez és automatizálásához munkafolyamatnak nevezett lépések sorozataként. A felhőszolgáltatások és a helyszíni rendszerek között számos [összekötő](../connectors/apis-list.md) található, amelyek gyorsan csatlakoztatnak egy kiszolgáló nélküli alkalmazást más API-khoz. Minden logikai alkalmazás egy eseményindítóval kezdődik, például "Amikor egy fiókot hozzáadnak a Dynamics CRM-hez". Az eseményindító tüzek után a munkafolyamat műveletek, konverziók és feltételes logika kombinációit futtathatja. A Logic Apps nagyszerű választás, ha különböző Azure-függvényeket vezényl egy folyamatban, különösen akkor, ha a folyamat külső rendszerrel vagy API-val való interakciót igényel.

A Logic Apps alkalmazásokkal való ismerkedéshez kezdje [az első logikai alkalmazás létrehozásával.](quickstart-create-first-logic-app-workflow.md) A Logic Apps alkalmazásokkal kapcsolatos további technikai információkért tekintse meg a [fejlesztői útmutatót.](logic-apps-workflow-definition-language.md)

## <a name="what-is-azure-functions"></a>Mi az az Azure Functions?

Az Azure Functions egy olyan szolgáltatás, amely könnyen futtatható kód- vagy "függvényeket" a felhőben. Csak az aktuális problémához szükséges kódot írhatja, anélkül, hogy aggódnia kellene egy teljes alkalmazás vagy a szükséges infrastruktúra miatt. A függvények még hatékonyabbá tehetik a fejlesztést, és használhatja a választott fejlesztési nyelvet, például a C#, F#, Node.js, Python vagy PHP nyelvet. Csak a kód futásának és az Azure-skálázásnak a kifizetését kell fizetnie.

Az Azure Functions első lépéseihez kezdje [az Első Azure-függvény létrehozása című](../azure-functions/functions-create-first-azure-function.md)dallal. A Funkciókkal kapcsolatos további technikai információkért tekintse meg a [fejlesztői útmutatót.](../azure-functions/functions-reference.md)

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Hogyan hozhatok létre és telepíthetek kiszolgáló nélküli alkalmazásokat az Azure-ban?

Az Azure gazdag eszközöket biztosít a kiszolgáló nélküli alkalmazások fejlesztéséhez, telepítéséhez és kezeléséhez. Közvetlenül az Azure Portalon hozhat létre alkalmazásokat a Visual Studio vagy a [Visual Studio-kód](quickstart-create-logic-apps-visual-studio-code.md) [eszközeivel.](logic-apps-serverless-get-started-vs.md) Az alkalmazás létrehozása után [gyorsan üzembe helyezheti az alkalmazást az Azure Resource Manager-sablonokkal.](logic-apps-deploy-azure-resource-manager-templates.md) Az Azure figyelést is biztosít, amely et az Azure Portalon, az API-n vagy az SDK-n keresztül, vagy az Azure Monitor-naplók és az Application Insights integrált eszközeivel érheti el.

## <a name="next-steps"></a>További lépések

* [Kiszolgáló nélküli alkalmazás létrehozása a Visual Studióban](logic-apps-serverless-get-started-vs.md)
* [Ügyfélelemzési irányítópult létrehozása kiszolgáló nélküli kiszolgálónélküli állapotban](logic-apps-scenario-social-serverless.md)
* [A logikai alkalmazás üzemelő példányának automatizálása](logic-apps-azure-resource-manager-templates-overview.md)
