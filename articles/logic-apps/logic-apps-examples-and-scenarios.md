---
title: "Példák & gyakori helyzetek - Azure Logic Apps |} Microsoft Docs"
description: "További információk a logic apps példák, forgatókönyvek, oktatóanyagok és forgatókönyvek"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 09/13/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: b88d0c1ccb7a729c95299bcdc3cba5fd73fcdeac
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Gyakori forgatókönyvek, példák, oktatóanyagok és az Azure Logic Apps forgatókönyvek

[Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) segít összehangolására és integrálását a különböző szolgáltatásokat nyújtó [100 + kész használható összekötők](../connectors/apis-list.md), tartománymeghatározási a helyszíni SQL Server vagy az SAP kognitív Microsoft-szolgáltatásokba. A Logic Apps szolgáltatás az "kiszolgáló nélküli", így nem kell foglalkoznia a skála vagy példányok. Ehhez mindössze határozza meg a munkafolyamat egy eseményindító és a munkafolyamat hajt végre műveleteket. Az alapul szolgáló platform kezeli a méretezés, a rendelkezésre állás és teljesítmény. A Logic Apps különösen fontos a használati esetek és forgatókönyvek kell több művelet koordinálják meg több rendszerben.

További tudnivalók a sok mintákat és képességeket nyújt segítséget, hogy [Azure Logic Apps](../logic-apps/logic-apps-overview.md) támogatja, az alábbiakban gyakori példák és forgatókönyvek.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Kezdőpont a logic app munkafolyamatok Népszerű elemek

Minden logikai alkalmazás kezdődik-e egy [ *eseményindító*](../logic-apps/logic-apps-overview.md#logic-app-concepts), és csak egy eseményindító, amely elindítja a logic app munkafolyamatot, és átadja az adatokat, hogy az eseményindító részeként. Néhány összekötő is biztosít eseményindítókat, amely a következő típusok:

* *Lekérdezési eseményindítók*: rendszeresen ellenőrzi az új adatokat egy végpontot. Új adatok létezik-e, amikor az eseményindítót hoz létre, és új munkafolyamat-példány fut, a bemeneti adatok.

* *Leküldéses eseményindítók*: az adatokat a szolgáltatásvégpont figyeli, és megvárja, amíg egy adott esemény történik. Az esemény akkor fordul elő, amikor az eseményindító következik be, létrehozása és a rendelkezésre álló adatokat használja bemeneti adatként új munkafolyamat-példány fut.

Az alábbiakban néhány népszerű eseményindító példák:

* Lekérdezési: 

  * [**Ütemezés - ismétlődési** eseményindító](../connectors/connectors-native-recurrence.md) lehetővé teszi, hogy állítsa be a kezdő dátum és idő, valamint az ismétlődés kiváltó a Logic Apps alkalmazást. 
  Kiválaszthatja például a hét napjainak és napszakokban indítására, a Logic Apps alkalmazást.

  * A "Ha e-mailben kapott" eseményindító lehetővé teszi, hogy a Logic Apps alkalmazást, ellenőrizze az új e-mailek bármely mail szolgáltatótól, például a Logic Apps által támogatott [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [ Outlook.com-os](https://docs.microsoft.com/connectors/outlook/), és így tovább.

  * A [ **HTTP** eseményindító](../connectors/connectors-native-http.md) lehetővé teszi, hogy a logikai alkalmazás egy megadott szolgáltatás végpontjának ellenőrzése HTTP Protokollon keresztül kommunikál.
  
* Leküldéses:

  * A [ **kérelem / válasz - kérelem** eseményindító](../connectors/connectors-native-reqres.md) lehetővé teszi, hogy a logikai alkalmazás HTTP-kérelmek fogadásához, valamilyen módon események valós időben válaszolni.

  * A [ **HTTP Webhook** eseményindító](../connectors/connectors-native-webhook.md) regisztrálásával szolgáltatásvégpont előfizet egy *visszahívási URL-cím* szolgáltatáshoz. 
  Így a szolgáltatás csak értesítheti az eseményindító a megadott esemény történik, amikor, hogy az eseményindító nem szükséges, és kérdezze le a szolgáltatást.

Új adatok vagy az esemény értesítést kap, miután az eseményindító következik be, létrehoz egy új logic app munkafolyamat-példányt és futtatja a műveleteket a munkafolyamatban. Adatok a eseményindítóval a munkafolyamaton keresztül érheti el. Például az "On egy új tweetet" eseményindító a logikai alkalmazásban, futtassa a tweetet tartalmat adja át. 

## <a name="respond-to-triggers-and-extend-actions"></a>Válaszolni az eseményindítók és műveletek kiterjesztése

Rendszerekhez és a szolgáltatások, előfordulhat, hogy nem közzétett összekötők logic Apps alkalmazásokat is kiterjeszthető.

* [Hozzon létre egyéni eseményindítók és műveletek](../logic-apps/logic-apps-create-api-app.md)
* [A munkafolyamat futtatása hosszú ideig futó műveletek](../logic-apps/logic-apps-create-api-app.md)
* [Külső eseményeket és műveleteket a webhookok válaszolni](../logic-apps/logic-apps-create-api-app.md)
* [Hívja, eseményindító, vagy a HTTP-kérelmek szinkron választ munkafolyamatok egymásba](../logic-apps/logic-apps-http-endpoint.md)
* [Oktatóanyag: AI táplált közösségi irányítópult létrehozása a Logic Apps és a Power BI percben](http://aka.ms/logicappsdemo)
* [Oktatóanyag: Twilio SMS webhookok válaszolni, és a szöveg visszajelzés küldése](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Vezérlési folyamatában, hibakezelés és naplózási képességek

A Logic apps speciális vezérlési folyamatában, például a feltételeket, kapcsolók, hurkokat és hatókörök gazdag képességeit tartalmazza. Ahhoz, hogy a refs megoldások, hiba és a munkafolyamatok kivételkezelés is alkalmazhat. Értesítés és a munkafolyamat futtatási állapot diagnosztikai naplók Azure Logic Apps is biztosít figyelése és a riasztásokat.

* [A tömbök és gyűjteményeket, amelyek a hurok- és a logic apps kötegek folyamat elemek](../logic-apps/logic-apps-loops-and-scopes.md)
* [E kapcsoló utasításokat különböző műveleteket](../logic-apps/logic-apps-switch-case.md)
* [Szerző hiba és a kivételkezelő munkafolyamat](../logic-apps/logic-apps-exception-handling.md)
* [Használati eset: hogyan használja az egészségügyi vállalati a logic app kivételkezelő HL7 FHIR munkafolyamatokhoz](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [A figyelés, a naplózás vagy a meglévő logic Apps alkalmazások riasztások bekapcsolása](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [A logic apps létrehozásakor figyelési és diagnosztikai naplózás bekapcsolása](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>Logikai alkalmazások telepítését és kezelését

Teljes körű fejlesztése és logikai alkalmazások telepítése a Visual Studio, a Visual Studio Team Services, vagy bármely más verziókezelő és automatizált összeállítási eszközök. A logic apps munkafolyamatok és a függő kapcsolatok egy erőforrás-sablon a központi telepítés támogatása, használja az Azure-erőforrás a központi telepítési sablonok. Visual Studio eszközök automatikusan létre ezeket a sablonokat, amelynek a verziókövetési verziószámozásának ellenőrizheti.

* [Hozza létre és logikai alkalmazások Visual Studio telepítése](../logic-apps/logic-apps-deploy-from-vs.md)
* [A figyelés, a naplózás vagy a meglévő logic Apps alkalmazások riasztások bekapcsolása](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Az automatikus központi telepítési sablon létrehozása](../logic-apps/logic-apps-create-deploy-template.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Tartalomtípusokat, átalakítás és átalakítások futtató belül

Elérheti, alakítsa át, és az Azure Logic Apps sok függvénnyel több tartalomtípus átalakítási [munkafolyamatdefiníciós nyelve](http://aka.ms/logicappsdocs). Alakíthatja, például egy karakterlánc, a JSON és az XML-re között a `@json()` és `@xml()` munkafolyamat-kifejezések. A Logic Apps motor őrzi meg a tartalom átvitele támogatja a szolgáltatások közötti veszteségmentes módon tartalomtípusokat.

* [A logic apps a munkafolyamat-kifejezések működése](../logic-apps/logic-apps-author-definitions.md)
* [Nem JSON tartalom kezelésére](../logic-apps/logic-apps-content-type.md), például `application/xml`, `application/octet-stream`, és`multipart/formdata`
* [Útmutató: Az Azure Logic Apps munkafolyamatdefiníciós nyelve](http://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Más integrációja és képességek

A Logic apps is kínál számos szolgáltatás, például az Azure Functions, az Azure API Management, az Azure App Service szolgáltatások és az egyéni HTTP-végpontokról, például a REST és a SOAP való integráció.

* [Hozzon létre egy valós idejű közösségimédia irányítópultot Azure kiszolgáló nélküli](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Az Azure Functions hívható meg a logic Apps alkalmazásokból](../logic-apps/logic-apps-azure-functions.md)
* [Forgatókönyv: Eseményindító logic Apps alkalmazások az Azure Functions](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Blog: Hívja a logic Apps alkalmazásokból SOAP-végpontok](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Végpontok közötti forgatókönyvek

* [Tanulmány: Vállalati integrációs végpont nagybetűk kezelése az Azure-szolgáltatásokkal, például a Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="next-steps"></a>További lépések

* [Szerző munkafolyamat definíciója a munkafolyamatdefiníciós nyelve](../logic-apps/logic-apps-author-definitions.md)
* [Hibákat és kivételeket a logic Apps alkalmazásokat kezeléséhez](../logic-apps/logic-apps-exception-handling.md)
* [Küldje el a megjegyzéseket, a kérdéseket, a visszajelzések és a javaslatok, milyen azt látna Azure Logic Apps alkalmazások számára](https://feedback.azure.com/forums/287593-logic-apps)