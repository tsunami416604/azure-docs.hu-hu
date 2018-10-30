---
title: Példák és a gyakori forgatókönyvek – Azure Logic Apps |} A Microsoft Docs
description: Példák, forgatókönyvek, oktatóanyagok és útmutatók az Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.date: 01/31/18
ms.openlocfilehash: 6249c45a1cd3a8acbb9770fc39da881d8da9057f
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230138"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Gyakori forgatókönyvek, példák, oktatóanyagok és útmutatók az Azure Logic Apps

[Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) összehangolására és azáltal, hogy más szolgáltatások integrálása segít [több mint 100 használatra kész összekötők](../connectors/apis-list.md), amely, a helyszíni SQL Server vagy az SAP a Microsoft Cognitive Servicesre. A Logic Apps szolgáltatás az "kiszolgáló nélküli", így nem kell aggódnia a méretezési csoport vagy a példányok. Ehhez csak határozhat meg munkafolyamatot egy eseményindítót és a munkafolyamatot hajt végre műveleteket. Az alapul szolgáló platform kezeli a méretezési csoport, rendelkezésre állását és teljesítményét. A Logic Apps különösen hasznos a használati esetekre és forgatókönyvekre, ahol kell működnie több művelet több rendszer között.

További információ a sok minták és képességeket nyújt segítséget, amelyek [Azure Logic Apps](../logic-apps/logic-apps-overview.md) támogatja, az alábbiakban gyakori példák és forgatókönyvek.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Kezdőpont a logikai alkalmazások munkafolyamataiba Népszerű elemek

Minden logikai alkalmazást egy [ *eseményindító*](../logic-apps/logic-apps-overview.md#logic-app-concepts), és egyetlen eseményindító, amely a logikai alkalmazás munkafolyamatának elindul, és átadja az adatokat az eseményindító részeként. Egyes összekötők eseményindítókat, amelyre ezek a típusok következő biztosítanak:

* *Lekérdezési eseményindítók*: rendszeresen ellenőrzi az új adatok szolgáltatásvégpontot. Új adatok állnak rendelkezésre, ha az eseményindító hoz létre, és a egy új munkafolyamat-példány fut, a bemeneti adatok.

* *Leküldéses eseményindítók*: figyeli az adatok egy végpontot, és megvárja, amíg egy meghatározott esemény. Ha az esemény történik, az eseményindítót, azonnal létrehozásának és futtatásának új munkafolyamat-példány elérhető adatokat használja bemeneti adatként.

Az alábbiakban néhány népszerű triggert példák:

* Lekérdezés: 

  * [**Ütemezés – ismétlődés** eseményindító](../connectors/connectors-native-recurrence.md) lapon beállíthatja a kezdő dátum és idő plusz a logikai alkalmazás aktiválja az ismétlődés. 
  Kiválaszthatja például a hét azon napjai, és a logikai alkalmazás indítására nap.

  * Az "E-mail érkezésekor" eseményindító lehetővé teszi, hogy a logikai alkalmazás ellenőrzi a bármely mail szolgáltató, például a Logic Apps által támogatott új e-mailek [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [ Outlook.com-os](https://docs.microsoft.com/connectors/outlook/), és így tovább.

  * A [ **HTTP** eseményindító](../connectors/connectors-native-http.md) lehetővé teszi, hogy a logikai alkalmazás egy adott szolgáltatásvégpont ellenőrzéséhez HTTP protokollon keresztül kommunikál.
  
* Küldje le:

  * A [ **kérelem / válasz – kérelem** eseményindító](../connectors/connectors-native-reqres.md) lehetővé teszi, hogy a logikai alkalmazás HTTP-kérelmekre, és válaszolhatnak valós idejű események valamilyen módon.

  * A [ **HTTP-Webhook** eseményindító](../connectors/connectors-native-webhook.md) regisztrálásával feliratkozik egy végpontot egy *visszahívási URL-Címének* az adott szolgáltatással. 
  Ezzel a módszerrel a szolgáltatás csak értesítheti az eseményindító a meghatározott esemény történik, ha úgy, hogy az eseményindító nem kell lekérdeznie, a szolgáltatás.

Új adatokat és a egy esemény szóló értesítés megjelenését követően az eseményindító aktiválódik, hoz létre egy új logikai alkalmazás munkafolyamat-példány és futtatja a műveleteket a munkafolyamatban. Az eseményindító a munkafolyamaton keresztül a keresztül elérhető adatokat. Például az "On"új tweet eseményindító be a logikai alkalmazás futtatásának továbbítja a tweet tartalmát. 

## <a name="respond-to-triggers-and-extend-actions"></a>Válaszolni a eseményindítók és műveletek kiterjesztése

Rendszerek és szolgáltatások, előfordulhat, hogy nem közzétett összekötők is kiterjesztheti a logic apps.

* [Hozzon létre egyéni eseményindítók és műveletek](../logic-apps/logic-apps-create-api-app.md)
* [A munkafolyamat-futtatások hosszú ideig futó műveletek beállítása](../logic-apps/logic-apps-create-api-app.md)
* [Külső események és műveletek webhookokkal válaszolni](../logic-apps/logic-apps-create-api-app.md)
* [Hívása, eseményindító, vagy a HTTP-kérelmekre szinkron válaszok munkafolyamatok beágyazása](../logic-apps/logic-apps-http-endpoint.md)
* [Oktatóanyag: Egy mesterséges Intelligencia által működtetett közösségi irányítópult létrehozása percek alatt, a Logic Apps és a Power bi-ban](https://aka.ms/logicappsdemo)
* [Videó: Twilio SMS webhookok válaszolni, és a egy SMS-válasz küldése](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Átvitelvezérlés hibakezelést és naplózási képességek

Logikai alkalmazások közé tartoznak a speciális ellenőrzési folyamat, például a feltételeket, a kapcsolók, a hurkokat és a hatókörök széles körű lehetőségekkel. Ahhoz, hogy a rugalmas megoldások, hiba- és kivételkezelés munkafolyamatait is alkalmazhat. Értesítési és diagnosztikai naplókat munkafolyamat-futtatási állapota, az Azure Logic Apps azt is biztosít, figyelésig és figyelmeztetésekig.

* Különféle különböző műveletek végezhetők el [feltételes utasításokat](../logic-apps/logic-apps-control-flow-conditional-statement.md) és [switch-utasítások](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Ismételje meg a lépéseket, vagy a tömbök és gyűjtemények hoz léte hurkokkal folyamat elemek](../logic-apps/logic-apps-control-flow-loops.md)
* [Hatókörök együtt műveleteit](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Szerző hiba- és kivételkezelés a munkafolyamat](../logic-apps/logic-apps-exception-handling.md)
* [Használati eset: hogyan egy támogatott egészségügyi cég használja a logikai alkalmazás kivételkezelési HL7 FHIR-munkafolyamatok](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Figyelés, naplózás és a meglévő logic apps riasztások bekapcsolása](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Logikai alkalmazások létrehozásának figyelési és diagnosztikai naplózás bekapcsolása](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>Logikai alkalmazások telepítése és felügyelete

Teljes körűen fejlesztéséhez és üzembe helyezése a Visual Studio, az Azure DevOps, vagy bármely más forráskezelés és az automatizált összeállítási eszközök rendelkező logikai alkalmazások. Azure-erőforrások központi telepítési sablonok használatával támogatja az üzembe helyezési munkafolyamatok és a egy erőforrás-sablonban függő kapcsolatok, a logic apps. Visual Studio-eszközök automatikusan létrehozni ezeket a sablonokat és ellenőrizheti verziókövetési verziószámozása.

* [Létrehozása és üzembe helyezése a logikai alkalmazások Visual studióval](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Figyelés, naplózás és a meglévő logic apps riasztások bekapcsolása](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Az automatikus központi telepítési sablon létrehozása](../logic-apps/logic-apps-create-deploy-template.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Tartalomtípusok, átalakítás és átalakítások belüli Futtatás

Elérését, átalakítás és a számos függvényt az Azure Logic Apps használatával több tartalomtípus átalakítása [munkafolyamat-definíciós nyelv](https://aka.ms/logicappsdocs). Például átválthat egy karakterlánc, a JSON és az XML-között a `@json()` és `@xml()` munkafolyamat a kifejezéseket. A Logic Apps-motor megőrzi a támogatásához a tartalomátvitel-szolgáltatások közötti veszteségmentes módon tartalomtípusokat.

* [A logic apps munkafolyamat-kifejezések működése](../logic-apps/logic-apps-author-definitions.md)
* [Tartalomtípusok kezelése nem JSON](../logic-apps/logic-apps-content-type.md), pl. `application/xml`, `application/octet-stream`, és `multipart/formdata`
* [Az Azure Logic Apps munkafolyamat Definition Language séma](https://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Más Integrációk és képességek

Logikai alkalmazások is rendelkeznek számos szolgáltatás, mint az Azure Functions, Azure API Management, Azure App Services és az egyéni HTTP-végpontokat, például a REST-en és a SOAP-integráció.

* [Valós idejű közösségi irányítópult létrehozása az Azure kiszolgáló nélküli megoldással](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Az Azure Functions hívása a logic apps](../logic-apps/logic-apps-azure-functions.md)
* [Oktatóanyag: Az eseményindító logikai alkalmazások az Azure Functions használatával](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Oktatóanyag: Az Azure Event Grid és a Logic Apps a virtuális gépek módosításainak monitorozása](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Oktatóanyag: Hozzon létre egy függvényt, amely integrálható az Azure Logic Apps és a Microsoft Cognitive Services post Twitter-vélemények elemzése](../azure-functions/functions-twitter-email.md)
* [Oktatóanyag: IoT távoli figyelés és értesítések az Azure Logic Apps csatlakoztatása az IoT hub és a postaláda](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: Hívja a logic apps a SOAP-végpontok](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Végpontok közötti forgatókönyvek

* [Tanulmány: Teljes körű megkülönbözteti a kis kezelésének integrációja az Azure-szolgáltatásokkal, például a Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Ügyfelek történetei

Ismerje meg, hogyan Azure Logic Apps más Azure-szolgáltatások és a Microsoft-termékek, valamint segített [ezeknek a vállalatoknak](https://aka.ms/logic-apps-customer-stories) növelheti a rugalmasságot elérni és összpontosítson fő üzleti tevékenységeikre egyszerűsítése, rendezése, automatizálása és replikálásával segít a vállalatnak szerint összetett folyamatokat.

## <a name="next-steps"></a>További lépések

* [A JSON-fájllal logikaialkalmazás-definíciók készítése](../logic-apps/logic-apps-author-definitions.md)
* [Hibák és kivételek, a logic apps kezelése](../logic-apps/logic-apps-exception-handling.md)
* [Küldje el a megjegyzéseket, kérdéseket, visszajelzés vagy Azure Logic Apps javítására vonatkozó javaslatok](https://feedback.azure.com/forums/287593-logic-apps)