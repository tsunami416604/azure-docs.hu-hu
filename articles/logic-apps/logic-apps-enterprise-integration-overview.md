---
title: B2B vállalati integráció áttekintése – Azure Logic Apps |} A Microsoft Docs
description: Vállalati integrációs megoldásokat az Azure Logic Apps és az Enterprise Integration Pack csomag B2B automatizált munkafolyamatokat hozhat létre
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: dd517c4d-1701-4247-b83c-183c4d8d8aae
ms.date: 09/08/2016
ms.openlocfilehash: c9733346d091a6829bd5b42c80201df3a1357fe3
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243110"
---
# <a name="overview-b2b-enterprise-integration-scenarios-in-azure-logic-apps-with-enterprise-integration-pack"></a>Áttekintés: B2B vállalati integrációs forgatókönyvek az Azure Logic Appsben és Enterprise Integration Pack

Vállalatközi (B2B) munkafolyamatok és az Azure Logic Apps zökkenőmentes kommunikációt engedélyezheti a vállalati integrációs kérdéseket a Microsoft felhőalapú megoldása, az Enterprise Integration Pack. Szervezetek is alkalmazásközi elektronikus úton, még akkor is, ha eltérő protokollok és formátumok használata. A csomag különböző formátumokat alakítja át az olyan formátumra, amely a szervezet rendszerek és feldolgozható formátumokba. Szervezetek üzenetváltás is beleértve iparági szabványnak megfelelő protokollokon keresztül [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md), és [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). A titkosítás és digitális aláírások üzeneteket is gondoskodhat.

Ha ismeri a BizTalk Server vagy a Microsoft Azure BizTalk Services, nincsenek az Enterprise Integration funkciói, könnyen használható, mert a legtöbb fogalmak hasonlóak. Egy fő különbség az, hogy a vállalati integrációs integrációs fiókok használja egyszerűsítése érdekében a tároló és a B2B-kommunikációban használt összetevők kezelését. 

Tekintve az Enterprise Integration Pack "integrációs fiók" alapján történik. Ezek a fiókok olyan felhőalapú tárolók tároló összes az összetevőkhöz, például a sémákat, partnerek, tanúsítványok, térképek és megállapodások. Ezek az összetevők megtervezésére, üzembe helyezése és karbantartása a B2B-alkalmazások, és a logic apps B2B-munkafolyamatok létrehozásához is használhatja. De ezek az összetevők használata előtt először az integrációs fiók hozzá kell rendelnie a logikai alkalmazást. Ezt követően a logikai alkalmazás hozzáférhet az integrációs fiók összetevői.

## <a name="why-should-you-use-enterprise-integration"></a>Miért használjam a vállalati integrációs?

* Vállalati integráció, az összes összetevőt egy helyen – az integrációs fiók tárolhatja.
* B2B-munkafolyamatok és a külső szolgáltatottszoftver (SaaS) alkalmazások, a helyszíni alkalmazások és egyéni alkalmazások integrálása az Azure Logic Apps-motor és az összes összekötő használatával.
* A logic Apps az Azure functions egyéni kódot is létrehozhat.

## <a name="how-to-get-started-with-enterprise-integration"></a>Ismerkedés a vállalati integrációs hogyan?

Build és a B2B-alkalmazások és az Enterprise Integration Pack a Logikaialkalmazás-tervező segítségével kezelheti a **az Azure portal**. Emellett kezelheti a logic apps- [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp).

Az alábbiakban a magas szintű lépéseket kell tennie, mielőtt alkalmazásokat hozhat létre az Azure Portalon:

![áttekintő kép](media/logic-apps-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>Mik azok a néhány gyakori helyzet?

Vállalati integráció a következő iparági szabványok támogatja:

* EDI - elektronikus adatcsere
* EAI - Enterprise Application Integration

## <a name="heres-what-you-need-to-get-started"></a>Mire lesz szüksége a kezdéshez

* Integrációs fiók az Azure-előfizetés
* A Visual Studio 2015-leképezések és sémák létrehozása
* [A Microsoft Azure Logic Apps Enterprise Integration Tools for Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas)  

## <a name="try-it-now"></a>Kipróbálás

[Üzembe helyezése egy teljesen működőképes minta AS2 küldése és fogadása a logic app](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) a B2B-funkciói, amely használja az Azure Logic Apps.

## <a name="learn-more"></a>Részletek
* [Szerződések](../logic-apps/logic-apps-enterprise-integration-agreements.md "megismerheti a vállalati integrációs szerződések")
* [Vállalatközi (B2B) környezetek](../logic-apps/logic-apps-enterprise-integration-b2b.md "megtudhatja, hogyan hozhat létre a Logic apps B2B-funkciókkal ")  
* [Tanúsítványok](logic-apps-enterprise-integration-certificates.md "megismerheti a vállalati integrációs tanúsítványokat")
* [Egybesimított fájl kódolása és dekódolása](logic-apps-enterprise-integration-flatfile.md "megtudhatja, hogyan kódolhat és dekódolhat egybesimított fájl tartalma")  
* [Integrációs fiókok](../logic-apps/logic-apps-enterprise-integration-accounts.md "megismerheti a integrációs fiókok")
* [Térképek](../logic-apps/logic-apps-enterprise-integration-maps.md "megismerheti a vállalati integrációs térképek")
* [Partnerek](logic-apps-enterprise-integration-partners.md "megismerheti a vállalati integrációs partnerek")
* [Sémák](logic-apps-enterprise-integration-schemas.md "megismerheti a vállalati integrációs sémák")
* [Üzenet XML-érvényesítés](logic-apps-enterprise-integration-xml.md "megtudhatja, hogyan érvényesítheti az XML-üzenetek a Logic apps használatával")
* [XML-átalakításhoz](logic-apps-enterprise-integration-transform.md "megismerheti a vállalati integrációs térképek")
* [Vállalati integrációs összekötők](../connectors/apis-list.md "további tudnivalók az enterprise integration pack-összekötők")
* [Integrációs fiók metaadat](../logic-apps/logic-apps-enterprise-integration-metadata.md "további információ az integrációs fiók metaadatok")
* [B2B üzenetek megfigyelése](logic-apps-monitor-b2b-message.md "B2B-üzenetek monitorozásával kapcsolatos további információkért")
* [Az Azure Log Analyticsben B2B üzenetek nyomon követése](logic-apps-track-b2b-messages-omsportal.md "tudjon meg többet az Azure Log Analyticsben B2B üzenetek nyomon követése")

