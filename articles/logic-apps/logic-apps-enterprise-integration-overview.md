---
title: Vállalati B2B - Azure Logic Apps-integráció |} Microsoft Docs
description: B2B munkafolyamatok építhetők fel, és vállalati integrációs feladatokhoz a logic apps a vállalati integrációs csomaggal támogatás
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: jeconnoc
editor: cgronlun
ms.assetid: dd517c4d-1701-4247-b83c-183c4d8d8aae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 25349d48d88b64568fef0f5a6bac5950da15196b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298637"
---
# <a name="overview-b2b-scenarios-and-communication-with-the-enterprise-integration-pack"></a>Áttekintés: B2B forgatókönyvek és a vállalati integrációs csomag kommunikáció

Az üzleti vállalatközi (B2B) munkafolyamatok és az Azure Logic Apps zökkenőmentes kommunikációt engedélyezheti a vállalati integrációs feladatokhoz a Microsoft felhőalapú megoldással, a vállalati integrációs csomagot. A szervezetek továbbíthatja az üzenetek elektronikus úton, még akkor is, ha azok használnak a különböző protokollok és formázza az adathordozót. A csomag különböző formátumokban átalakítja az olyan formátumra, amely a szervezet rendszerek értelmezi, és feldolgozni. A szervezetek továbbíthatja az üzeneteket keresztül ipari szabványnak számító protokollokat, beleértve a [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md), és [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). Titkosítás és a digitális aláírás-üzenetek is biztosíthassa.

Ha ismeri a BizTalk Server vagy a Microsoft Azure BizTalk szolgáltatások, nincsenek a vállalati integrációs szolgáltatások, könnyen használható, mert a legtöbb fogalmak hasonlóak. Egy fő különbség az, hogy a vállalati integrációs integrációs fiókok használja a tárolása és kezelése az összetevők B2B kommunikációban használt leegyszerűsítése érdekében. 

A vállalati integrációs csomag architektúráját, "integrációs fiókok" alapul. Ezek a fiókok olyan felhőalapú tárolók tároló összes a összetevők, például a sémák, partnerek, tanúsítványok, a maps és megállapodások. Ezen összetevők is használhatja, tervezése, telepítése és karbantartása a B2B alkalmazások és a logic apps B2B munkafolyamatok építhetők fel. Azonban ezen összetevők használata előtt meg kell először az integráció fiók összekötése a Logic Apps alkalmazást. Ezt követően a Logic Apps alkalmazást integrációs fiókja összetevők férhetnek hozzá.

## <a name="why-should-you-use-enterprise-integration"></a>Miért kell használni a vállalati integrációs?

* A vállalati integrációs tárolhatja az összetevők egy helyen--integrációs fiókját.
* B2B munkafolyamatok építhetők fel, és a külső szolgáltatásként szoftver (SaaS) alkalmazások, a helyszíni alkalmazásokhoz és az egyéni alkalmazások integrálása az Azure Logic Apps és az összekötők használatával.
* A logic Apps alkalmazások az Azure functions létrehozhat egyéni kódot.

## <a name="how-to-get-started-with-enterprise-integration"></a>Ismerkedés a vállalati integrációs hogyan?

Létrehozhatja és a vállalati integrációs csomag révén a Logic App Designer B2B alkalmazások kezelése a **Azure-portálon**. Emellett kezelhetők a logic apps a [PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "a Logic apps PowerShell témaköreiben talál").

Alkalmazások létrehozása az Azure portálon előtt végre kell hajtania magas szintű lépései a következők:

![Kép – áttekintés](media/logic-apps-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>Mik azok a olyan gyakori forgatókönyveket tartalmaz?

Vállalati integrációs ezeket az ipari szabványok támogatja:

* EDI - elektronikus adatcsere
* EAI - vállalati alkalmazások integrálása

## <a name="heres-what-you-need-to-get-started"></a>Ez szükséges a kezdéshez

* Azure-előfizetés integrációs fiókkal
* A Visual Studio 2015-leképezések és sémák létrehozása
* [A Microsoft Azure Logic Apps Enterprise-integrációs eszközök a Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas)  

## <a name="try-it-now"></a>Kipróbálás

[Egy teljesen működőképes minta AS2 küldési telepítése, és a logikai alkalmazás fogadni](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) , amely a B2B funkciókat használ az Azure Logic Apps.

## <a name="learn-more"></a>Részletek
* [Szerződések](../logic-apps/logic-apps-enterprise-integration-agreements.md "vállalati integrációs megállapodások ismertetése")
* [A vállalatok (B2B) környezetek](../logic-apps/logic-apps-enterprise-integration-b2b.md "megtudhatja, hogyan hozzon létre a Logic apps B2B szolgáltatások ")  
* [Tanúsítványok](logic-apps-enterprise-integration-certificates.md "vállalati integrációs tanúsítványokkal kapcsolatos további információ")
* [Fájl kódolás/dekódolás lapos](logic-apps-enterprise-integration-flatfile.md "útmutató kódolni és dekódolási egybesimított fájl tartalma")  
* [Integrációs fiókok](../logic-apps/logic-apps-enterprise-integration-accounts.md "integrációs fiókok ismertetése")
* [Leképezhető](../logic-apps/logic-apps-enterprise-integration-maps.md "vállalati integrációs maps ismertetése")
* [Partnerek](logic-apps-enterprise-integration-partners.md "vállalati integrációs partnerek ismertetése")
* [Sémák](logic-apps-enterprise-integration-schemas.md "további információ a vállalati integrációs sémák")
* [XML-üzenet érvényesítés](logic-apps-enterprise-integration-xml.md "megtudhatja, hogyan XML-üzenetek a Logic apps ellenőrzése")
* [XML-átalakító](logic-apps-enterprise-integration-transform.md "vállalati integrációs maps ismertetése")
* [Vállalati integrációs összekötők](../connectors/apis-list.md "további információ a vállalati integrációs csomag összekötők")
* [Integráció fiók metaadat](../logic-apps/logic-apps-enterprise-integration-metadata.md "integrációs fiók metaadat ismertetése")
* [B2B üzeneteket figyelő](logic-apps-monitor-b2b-message.md "tudhat meg többet a B2B üzenetek figyelése")
* [Az OMS-portálon B2B üzenetek nyomon követése](logic-apps-track-b2b-messages-omsportal.md "tudhat meg többet az OMS-portálon B2B üzenetek nyomon követése")

