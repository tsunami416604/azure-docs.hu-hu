---
title: A munkafolyamatok - Azure Logic Apps XML-üzenetek használata |} Microsoft Docs
description: Feldolgozni, ellenőrizze, átalakítás és funkciógazdagabbá teheti a logic Apps alkalmazásokat és üzleti XML-üzenetek-forgatókönyvekre, a vállalati integrációs csomag segítségével
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: jeconnoc
editor: ''
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 02/27/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 85bdaff5cb88bbadaed778458b66c0cce7bce9de
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298974"
---
# <a name="validate-and-transform-xml-encode-and-decode-flat-files-and-enrich-messages-features-in-logic-apps"></a>Ellenőrizze és átalakítható XML, kódolása és egybesimított fájlok és funkciógazdagabbá teheti a logic apps üzenetek szolgáltatásai

A logic apps segítségével, lehetősége nyílik XML üzenetek küldésére és fogadására. Ez a szolgáltatás része a vállalati integrációs csomagot. Azoknak a felhasználóknak a BizTalk Serveren háttérrel a vállalati integrációs csomag lehetővé teszi az átalakítási és üzenetek ellenőrzése, egyszerű fájlok, és még használatához XPath kiegészítése, illetve konkrét tulajdonságok üzenetből hasonló képességek. 

Azoknak a felhasználóknak, akik még nem használta a hely a ezek a funkciók bontsa ki, hogyan üzenetek feldolgozásához a munkafolyamaton belül. Például ha a vállalatok forgatókönyvek, és együttműködik a megadott XML-sémák, használhatja a vállalati integrációs csomag javítása érdekében hogyan vállalata dolgozza fel ezeket az üzeneteket. 

A vállalati integrációs csomag tartalmazza: 

* [XML-érvényesítés](logic-apps-enterprise-integration-xml-validation.md "további információ a XML-üzenet érvényesítés") -ellenőrzése egy bejövő vagy kimenő XML üzenet egy adott sémának.
* [XML-átalakító](../logic-apps/logic-apps-enterprise-integration-transform.md "XML üzenet átalakítások és a maps ismertetése") - alakítható, vagy testre szabhatja a követelmények, vagy egy partner követelményei alapján XML-üzenetben.
* [Fájl kódolási és dekódolási egybesimított fájl lapos](logic-apps-enterprise-integration-flatfile.md "további információ a egybesimított fájl kódolás/dekódolás") - kódolni vagy egybesimított fájl dekódolására. Például SAP fogad el, és egybesimított fájl formátumban megadva IDOC-fájlok küld. Integráció számos platformon XML-üzenetek, beleértve a Logic Apps létrehozása. Igen a egybesimított fájl kódoló "átalakítani" XML-fájlok egybesimított fájlokba használó logikai alkalmazás is létrehozhat. 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) - funkciógazdagabbá teheti az üzenetet, és konkrét tulajdonságok kinyerése az üzenetet. A kibontott tulajdonságok segítségével majd átirányíthatja az üzenet egy cél, vagy köztes végpont.

## <a name="try-it-out"></a>Próbálja ki!
[Egy teljesen működőképes logikai alkalmazás telepítése ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (GitHub minta) az XML-szolgáltatások az Azure Logic Apps segítségével.

## <a name="learn-more"></a>Részletek
[További tudnivalók a vállalati integrációs csomag](../logic-apps/logic-apps-enterprise-integration-overview.md "további információ a vállalati integrációs csomag")
