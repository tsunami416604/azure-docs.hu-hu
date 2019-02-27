---
title: XML-üzenetek B2B vállalati integráció – Azure Logic Apps |} A Microsoft Docs
description: Feldolgozásához, ellenőrzése, átalakítás és bővítés az Azure Logic Appsben az Enterprise Integration Pack csomag B2B-megoldásoktól az XML-üzenetek
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.date: 02/27/2017
ms.openlocfilehash: a75ac9773072423c13eef85ecad29c632c13d024
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873253"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML-üzenetek, és az Azure Logic Appsben és Enterprise Integration Pack egybesimított fájlok

Logic apps használata esetén, hogy a számítógép XML üzenetek küldése és fogadása. Ez a funkció az Enterprise Integration Pack részét képezi. Azoknak a felhasználóknak a BizTalk Server háttérrel az Enterprise Integration Pack biztosít hasonló funkciókat vehetnek igénybe, átalakítása és üzenetek érvényesíti, egybesimított fájlok használata, és akár az XPath bővítését, vagy konkrét tulajdonságok kinyerése egy üzenetet. 

Azoknak a felhasználóknak nem ismerő felhasználók érdekében ez a terület ezeket a funkciókat bontsa ki, hogyan-üzenetek feldolgozása a munkafolyamaton belül. Például ha vannak olyan vállalatok forgatókönyvben, és dolgozhat adott az XML-sémák, majd az Enterprise Integration Pack használatával javíthatja a miként a vállalat dolgozza fel ezeket az üzeneteket. 

Az Enterprise Integration Pack tartalmazza: 

* [XML-érvényesítés](logic-apps-enterprise-integration-xml-validation.md "további információ üzenet XML-érvényesítés") – egy bejövő vagy kimenő XML-üzenet egy adott séma érvényesítése.
* [XML-átalakításhoz](../logic-apps/logic-apps-enterprise-integration-transform.md "megismerheti a XML-üzenet átalakítások vagy a térképek") – konvertálni, vagy testre szabhatja egy XML-üzenet, a követelmények, vagy egy partneri követelményei alapján.
* [Egybesimított fájl kódolása és egybesimított fájl dekódolása](logic-apps-enterprise-integration-flatfile.md "megismerheti a egybesimított fájl kódolása és dekódolása") – kódolása, vagy egy egybesimított fájl dekódolása. Például az SAP fogad el, és egybesimított fájl formátumú IDOC-fájlok küldése. Számos integrációs platform XML-üzenetek, beleértve a logikai alkalmazások létrehozása. Így hozhat létre egy logikai alkalmazást, amely a flat file encoder "átalakítása" XML-fájlok egybesimított fájlok. 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) - üzenet bővítését, és konkrét tulajdonságok kinyerni az üzenetet. Ezután használhatja a kinyert tulajdonságait az üzenet célba, vagy egy köztes végpontot irányíthatja.

## <a name="try-it-out"></a>Próbálja ki!
[Egy teljesen működőképes logikai alkalmazás üzembe helyezése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (GitHub-minta) az Azure Logic Appsben az XML-funkciók használatával.

## <a name="learn-more"></a>Részletek
[További információ az Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "megismerheti a vállalati integrációs csomag")
