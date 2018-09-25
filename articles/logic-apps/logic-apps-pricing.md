---
title: Díjszabás és számlázás – Azure Logic Apps |} A Microsoft Docs
description: Megtudhatja, hogyan árak és számlázás használható az Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: b75fba2ba0e9fa922b1252378e0bab326cada7d2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974306"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Az Azure Logic Apps díjszabási modell

Hozzon létre, és automatizált méretezhető integrációs a munkafolyamatok futtatásához a felhőben az Azure Logic Apps. Az alábbiakban a Logic Apps számlázási és díjszabás működését részleteit. 

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Használatalapú díjszabási modellje

Új logic Apps, amely a nyilvános vagy "globális" Logic Apps szolgáltatás használatával hoz létre akkor csak azért fizet, használja. Ezek a logic apps használata egy fogyasztásalapú tervet és díjszabási modell, ami azt jelenti, hogy a logikai alkalmazás által végzett minden művelet-végrehajtások díjkötelesek. A logic app-definíciójának minden lépése a műveletet, például a triggereket, vezérlőelem a folyamat lépései, beépített műveletek hívásainak és összekötők hívásainak. További információkért lásd: [Logic Apps díjszabási](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Rögzített díjszabási modell

> [!NOTE]
> Az integrációs service-környezet *privát előzetes verzió*. Hozzáférés igényléséhez [itt csatlakozni a kérelem létrehozása](https://aka.ms/iseprivatepreview).

Létrehozott egy új logikai alkalmazásokhoz egy [ *integrációs szolgáltatás környezet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), amely egy privát elkülönül a Logic Apps-példány dedikált erőforrásokat használ, fizet a fix havi díja beépített műveletek és a standard szintű ISE címkével összekötők. Az ISE-ben egy vállalati összekötő díjmentes, míg további vállalati összekötők vannak a vállalati használati díj alapján tartalmaz. További információkért lásd: [Logic Apps díjszabási](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="triggers"></a>

## <a name="triggers"></a>Eseményindítók

Eseményindítók olyan speciális művelet, hozzon létre egy logikaialkalmazás-példányt egy meghatározott esemény. Eseményindítók különböző módokon, amelyek befolyásolják a logikai alkalmazás milyen forgalmi díjas jár el.

* **Lekérdezési eseményindító** – Ez az eseményindító folyamatosan ellenőrzi a végpont létrehozásához egy logikaialkalmazás-példányt, és elindítja a munkafolyamatot a feltételeknek megfelelő üzenetek. Minden lekérdezési kérelem-végrehajtási számít, és forgalmi díjas, még akkor is, ha nincs logikaialkalmazás-példányt jön létre. Adja meg a lekérdezési időköz, állítsa be – a Logic App Designerben az eseményindító.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook-eseményindító** – Ez az eseményindító megvárja, amíg egy ügyfél kérelmet küld egy adott végpontnak. Minden egyes a webhook-végpontot küldött kérelem számít egy művelet végrehajtását. Például a kérés és a HTTP-Webhook eseményindító mindkét webhook eseményindítók.

* **Ismétlődési trigger** – Ez az eseményindító létrehoz egy logikaialkalmazás-példányt, amely beállította az eseményindító-ismétlődési időköze alapján. Például beállíthat egy ismétlődési eseményindítót, amely három naponta fut, vagy egy összetettebb ütemezés szerint.

Eseményindító-végrehajtások a logikai alkalmazás áttekintése panelen a Triggerelőzmények szakaszában találhatja meg.

## <a name="actions"></a>Műveletek

Beépített műveletek, például a műveleteket, amelyeket hívja fel a HTTP, az Azure Functions vagy az API Management, és azt is szabályozzák, a folyamat lépései natív műveleteket, amelyek rendelkeznek a megfelelő típusú mérjük. Műveletek, [összekötők](https://docs.microsoft.com/connectors) az "ApiConnection" típusa lehet. Ezeket az összekötőket besorolt standard vagy enterprise összekötők, amelyek mérjük, valamint saját alapján [díjszabás][pricing]. 

Az összes sikeres és sikertelen feldolgozásokat is beleértve futtatási műveletek számoljuk és forgalmi díjas, műveletvégrehajtások. Műveleteket, amelyeket a rendszer kihagyja, miatt teljesítetlen feltételeket vagy műveleteket, amelyeket nem futnak, mert a logikai alkalmazás befejezését, mielőtt befejeződött, műveletvégrehajtások nem számít. Letiltva a logic apps nem hozható létre új példányokat, így azokat nem kell fizetnie, amikor le van tiltva.

> [!NOTE]
> Miután letiltja az egy logikai alkalmazást, a jelenleg futó példányok eltarthat egy ideig, mielőtt teljes mértékben meggátolni.

Hurkok belül futó műveleteket a hurok minden ciklusonként bájtjai számítanak. Ha például egyetlen művelettel egy "mindegyikre" hurkot, amely 10-elemek listáját dolgozza fel a listaelemek (10) számát megszorozva a hurok (1) a műveletek számát plusz egy, az iteráció megkezdése a akkor számít. Tehát ebben a példában a számítás (10 * 1) + 1, 11, műveletvégrehajtások eredményez.

## <a name="integration-account-usage"></a>Integrációs fiók használata

Használati fogyasztásalapú tartalmaz egy [integrációs fiók](logic-apps-enterprise-integration-create-integration-account.md) , ismerje meg, fejlesztheti és tesztelheti a [B2B/EDI](logic-apps-enterprise-integration-b2b.md) és [XML-feldolgozás](logic-apps-enterprise-integration-xml.md) nélkül a Logic Apps funkciói További költség. Használhat egy integrációs fiók / régió és áruházbeli specifikus legfeljebb [összetevők számú](../logic-apps/logic-apps-limits-and-config.md), például a kereskedelmi EDI-partnerek és szerződések, térképek, sémák, szerelvényeket, tanúsítványok és kötegkonfigurációk.

A Logic Apps is kínál az alapszintű és standard szintű integrációs fiók támogatott Logic Apps SLA-val. Alapszintű integrációs fiók is használhatja, ha szeretné használni, csak a kezelési üzenetet, vagy olyan kisméretű vállalkozások partner, amely a kereskedelmi partneri kapcsolatban áll azzal a nagyobb üzleti entitásban szerepét. Standard szintű integrációs fiókok összetettebb B2B-kapcsolatokat támogatja, és növelje a felügyelt entitások számát. További információkért lásd: [az Azure díjszabása](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>További lépések

* [További tudnivalók a Logic Apps][whatis]
* [Az első logikai alkalmazás létrehozása][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

