---
title: "Azure-kiszolgáló nélküli áttekintése |} Microsoft Docs"
description: "Hatékony megoldások létrehozása a felhőben anélkül, hogy az infrastruktúra gondolniuk."
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
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 5cc6837ed0b0f4467e48c736f5d596a51a799fae
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="overview-of-azure-serverless-with-functions-and-logic-apps"></a>Azure-kiszolgáló nélküli funkciók és a Logic Apps áttekintése

Kiszolgáló nélküli alkalmazások fejlesztését sebesség növekedése, szükséges kódot, és amely egyszerűség csökkentése előnyei kínálnak.  Ez a cikk a különböző attribútumokat kiszolgáló nélküli megoldások, és az Azure kiszolgáló nélküli ajánlatok állapotba kerül.

## <a name="what-is-serverless"></a>Mi az a kiszolgáló nélküli?

Kiszolgáló nélküli nem jelenti azt, nincsenek kiszolgálók – utal, a fejlesztőknek nem kell aggódnia a kiszolgálók.  A hagyományos alkalmazásfejlesztés nagy része válaszol kérdések méretezés, a tároló és a figyelési megoldásoknak az alkalmazás követelményeinek.  A kiszolgáló nélküli ezeket a kérdéseket vannak végrehajtott kezeli a megoldás részeként.  Emellett kiszolgáló nélküli alkalmazások számlázása a fogyasztás alapján terv.  Ha az alkalmazás soha nem használ, egy ingyenesen elérhető soha nem van szükség.  Ezeket a szolgáltatásokat a fejlesztők olyan üzleti logikát a megoldás kizárólag összpontosítanak.

Az alapvető szolgáltatások az Azure-kiszolgáló nélküli körül [Azure Functions](https://azure.microsoft.com/services/functions/) és [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).  Ezek a megoldások kövesse a fenti elveket, mind a fejlesztők számára a hatékony felhő alkalmazások minimális kóddal.

## <a name="what-are-azure-functions"></a>Mik az Azure Functions?

Az Azure Functions megoldással egyszerűen futtathatók kisebb kódrészletek, más néven „függvények”, a felhőben. Elég, ha a szóban forgó problémára vonatkozó kódot megírja, nem kell egy egész alkalmazással vagy futtató infrastruktúrával bajlódnia. Funkciók is legyen még hatékonyabbá, és a fejlesztési nyelvi szerkesztőprogramban, például a C#, F #, Node.js, Python vagy a PHP is használhatja. Csak a futtatásakor a kódot kell fizetnie, és Azure méretezi igény szerint.

Ha szeretne rögtön az Azure Functions használatának első lépéseihez ugrani, kezdje [Az első Azure-függvény létrehozása](../azure-functions/functions-create-first-azure-function.md) résszel. Ha a Functions szolgáltatással kapcsolatos további műszaki információkat keres, lépjen a [fejlesztői segédanyagok](../azure-functions/functions-reference.md) részhez.

## <a name="what-are-azure-logic-apps"></a>Mik az Azure Logic Apps?

Az Azure Logic Apps segítségével egy egyszerűsítése és méretezhető integrációja és a munkafolyamatok megvalósításához a felhőben. A vizuális tervező használatára, a modell, és a folyamat automatizálására munkafolyamat nevű lépések sorozataként biztosít.  Nincsenek [sok összekötők](../connectors/apis-list.md) keresztül csatlakozni gyorsan egy kiszolgáló nélküli alkalmazást más API-k felhőalapú és helyszíni szolgáltatásokat.  Egy logikai alkalmazás egy eseményindítóval kezdődik (például „amikor egy fiókot hozzáadnak a Dynamics CRM-hez”), és az indítás után kezdődhet számos műveleti, konverziós és feltétellogikai kombináció.  A Logic Apps akkor kiváló választás, ha különböző Azure Functions - folyamatban futó, különösen akkor, ha a folyamat igényel egy külső rendszer vagy az API-val való interakció.

Első lépések a Logic Apps, indítsa el a [az első logikai alkalmazás létrehozása](quickstart-create-first-logic-app-workflow.md).  Ha a Logic Apps kapcsolatos további műszaki információkat keres, tekintse meg a [fejlesztői leírás](logic-apps-workflow-actions-triggers.md).

## <a name="how-can-i-build-and-deploy-serverless-applications-in-azure"></a>Hogyan felépítéséhez és az Azure-ban kiszolgáló nélküli alkalmazások telepítéséhez?

Azure fejlesztési, telepítési és felügyeleti kiszolgáló nélküli alkalmazások lehetővé teszi az eszközök széles skáláját.  Alkalmazások közvetlenül az Azure-portálon, vagy a építhetők [tooling eszköz, a Visual Studio eszközből](logic-apps-serverless-get-started-vs.md).  Ha egy alkalmazás lehet [azonnal telepített](logic-apps-create-deploy-template.md).  Azure is biztosít a kiszolgáló nélküli alkalmazások figyelését.  A figyelés érhetők el az Azure-portálon keresztül az API-t vagy az SDK-k, vagy integrált tooling OMS és az Application insights szolgáltatással.

## <a name="next-steps"></a>További lépések

* [Első lépések a Visual Studio egy kiszolgáló nélküli alkalmazást felépítése](logic-apps-serverless-get-started-vs.md)
* [Hozzon létre egy felhasználói irányítópult kiszolgáló nélküli](logic-apps-scenario-social-serverless.md)
* [A központi telepítési sablont a logikai alkalmazás létrehozása](logic-apps-create-deploy-template.md)