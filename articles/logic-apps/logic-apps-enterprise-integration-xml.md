---
title: XML-üzenetek és egylapos fájlok
description: XML-üzenetek feldolgozása, érvényesítése és átalakítása az Azure Logic Apps alkalmazásban enterprise integration pack csomaggal
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 3cb3472572abbd891f8d36ea036b371c1224f38c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792151"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML-üzenetek és egyhelyen ként használható fájlok az Azure Logic Apps vállalati integrációs csomaggal

Az [Azure Logic Apps alkalmazásban](logic-apps-overview.md)feldolgozhatja az Sms-üzeneteket, amelyeket a vállalati integrációs csomaghasználatával küld el és fogad. Ha már használta a BizTalk Server szolgáltatást, az Enterprise Integration Pack hasonló lehetőségeket kínál az üzenetek átalakítására és érvényesítésére, az egyszerű fájlokkal való együttműködésre, sőt az XPath használatával is gazdagíthatja vagy kinyerheti az üzenetek bizonyos tulajdonságait. Ha még nem ismert ezen a helyen, ezek a funkciók kibővítik az üzenetek feldolgozásának módját a logikai alkalmazás munkafolyamatában. Ha például vállalatközi (B2B) forgatókönyvvel rendelkezik, és adott XML-sémákkal dolgozik, a vállalati integrációs csomaggal javíthatja, hogy a vállalat hogyan dolgozza fel ezeket az üzeneteket.

A vállalati integrációs csomag például a következő képességeket tartalmazza:

* [XML-érvényesítés:](logic-apps-enterprise-integration-xml-validation.md)Bejövő vagy kimenő XML-üzenet ellenőrzése egy adott sémán.

* [XML-átalakítás](logic-apps-enterprise-integration-transform.md): Xml-üzenet konvertálása vagy testreszabása a követelmények vagy a partner igényei alapján térképek használatával.

* [Egylapos fájlkódolás és egylapos fájldekódolás:](logic-apps-enterprise-integration-flatfile.md)Egysima fájl kódolása vagy dekódolása.

  Az SAP például sima fájlformátumban fogadja és küldi az IDOC fájlokat. Számos integrációs platform hoz létre XML-üzeneteket, beleértve a Logic Apps-et is. Így létrehozhat egy logikai alkalmazást, amely a síkfájl-kódolót használja az XML-fájlok "konvertálásához" egysima fájlokká.

* [XPath](workflow-definition-language-functions-reference.md#xpath): Üzenet gazdagítása és adott tulajdonságok kinyerése az üzenetből. Ezután a kinyert tulajdonságok segítségével irányíthatja az üzenetet egy cél, vagy egy köztes végpont.

## <a name="sample"></a>Sample

[Központi telepítés egy teljesen működőképes logikai alkalmazás](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (GitHub-minta) az Azure Logic Apps XML-szolgáltatások használatával.

## <a name="next-steps"></a>További lépések

További információ az [Enterprise Integration Pack csomagról](logic-apps-enterprise-integration-overview.md)
