---
title: Díjszabás és számlázás – Azure Logic Apps |} A Microsoft Docs
description: Megtudhatja, hogyan árak és számlázás használható az Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
manager: carmonm
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 02/26/2019
ms.openlocfilehash: 9b5452f112c6325dafd5edbe693b90ec2a94abc0
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990237"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Az Azure Logic Apps díjszabási modell

Hozzon létre, és automatizált munkafolyamatokat, amely az Azure Logic Apps használata esetén skálázhatja a felhőben futtatni. Az alábbiakban a Logic Apps számlázási és díjszabás működését részleteit. 

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Használatalapú díjszabási modellje

Új logic Apps, amely a nyilvános vagy "globális" Logic Apps szolgáltatásban akkor csak azért fizet, használja. Ezek a logic apps fogyasztásalapú terv és díjszabási modell használata. A logikai alkalmazás definíciójában minden lépése egy műveletet. Műveletek közé tartoznak az eseményindító, minden vezérlő a folyamat lépései, beépített műveleteket és összekötő hívások. A Logic Apps-mérőszámai minden művelet, amely a logikai alkalmazás futtatását.  
További információkért lásd: [Logic Apps díjszabási](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Rögzített díjszabási modell

Új logikai alkalmazásokhoz, amelyek futtatható egy [ *integrációs szolgáltatás környezet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), fix havi díja a beépített műveletek, a standard szintű összekötők kell fizetnie. Az ISE-ben is biztosítja, hogy az Azure virtuális hálózatban lévő erőforrások eléréséhez elszigetelt logikai alkalmazások létrehozása és futtatása. 

Az ISE alapegység javította a kapacitást, ha a kapacitás növelése érdekében van szüksége, [több skálázási egység hozzáadása](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity), vagy a létrehozás során vagy később. Az ISE-ben tartalmaz egy ingyenes vállalati összekötőt, amely a kívánt számú kapcsolatot foglal magában. További vállalati összekötők díjkötelesek használata a vállalati használati díj alapján. 

> [!NOTE]
> Az ISE-ben van [ *nyilvános előzetes verzióban*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). További információkért lásd: [Logic Apps díjszabási](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="triggers"></a>

## <a name="triggers"></a>Eseményindítók

Eseményindítók olyan speciális művelet, hozzon létre egy logikaialkalmazás-példányt egy meghatározott esemény. Eseményindítók különböző módokon, amelyek befolyásolják a logikai alkalmazás milyen forgalmi díjas jár el.

* **Lekérdezési eseményindító** – Ez az eseményindító folyamatosan ellenőrzi a végpont létrehozásához egy logikaialkalmazás-példányt, és elindítja a munkafolyamatot a feltételeknek megfelelő üzenetek. Logic Apps még ha nincs logikaialkalmazás-példányt jön létre, mert egy mérőszámai egyes lekérdezési kérelmek. Adja meg a lekérdezési időköz, állítsa be – a Logic App Designerben az eseményindító.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook-eseményindító** – Ez az eseményindító megvárja, amíg egy ügyfél kérelmet küld egy adott végpontnak. Minden egyes a webhook-végpontot küldött kérelem számít egy művelet végrehajtását. Például a kérés és a HTTP-Webhook eseményindító mindkét webhook eseményindítók.

* **Ismétlődési trigger** – Ez az eseményindító létrehoz egy logikaialkalmazás-példányt, amely beállította az eseményindító-ismétlődési időköze alapján. Például beállíthat egy ismétlődési eseményindítót, amely három naponta fut, vagy egy összetettebb ütemezés szerint.

## <a name="actions"></a>Műveletek

A Logic Apps beépített műveleteket mérőszámai natív műveletként használható. Például beépített műveletek függvényhívásokat tartalmazzák, az Azure Functions vagy az API Management és az ellenőrzési folyamat lépései például ciklusok és feltételek hívásait HTTP-n keresztül 
- mindegyik a saját művelet típusa. Műveletek, [összekötők](https://docs.microsoft.com/connectors) az "ApiConnection" típusa lehet. Ezeket az összekötőket besorolt standard vagy enterprise összekötők, amelyek mérjük, valamint saját alapján [díjszabás][pricing]. Vállalati összekötők *előzetes* standard összekötők díjkötelesek.

Logic Apps-mérőszámok az összes sikeres és sikertelen műveletek futtatására, műveletvégrehajtások. A Logic Apps nem mérni ezeket a műveleteket: 

* Műveletek beolvasása teljesítetlen feltételek miatt kihagyva
* Műveleteket, amelyeket nem futtatható, mert a logikai alkalmazás a befejezés előtt leállt

Letiltva a logic apps nem kell fizetnie során le van tiltva, mert nem hozhatnak létre új példányokat.

> [!NOTE]
> Miután letiltja az egy logikai alkalmazást, a jelenleg futó példányok eltarthat egy ideig, mielőtt teljes mértékben meggátolni.

Hurkok belül futó művelettel a Logic Apps minden műveletet a hurok ciklusonként számít. Tegyük fel például, egy "mindegyikre" hurkot, amely a folyamatok listáját. Logic Apps-mérőszámok, hogy a szám és lista a ciklus a művelet elemei a hurok műveletek számát, és hozzáadja a művelet, amely elindítja a ciklus. 10-elemek listáját, a számítás: (10 * 1) + 1, 11, műveletvégrehajtások eredményez.

## <a name="integration-account-usage"></a>Integrációs fiók használata

Érvényes használati fogyasztásalapú [integrációs fiókok](logic-apps-enterprise-integration-create-integration-account.md) , ismerje meg, fejlesztheti és tesztelheti a [B2B/EDI](logic-apps-enterprise-integration-b2b.md) és [XML-feldolgozás](logic-apps-enterprise-integration-xml.md) nélkül a Logic Apps funkciói További költség. Egy integrációs fiók lehet régiónként. Minden integrációs fiók adott is tárolhatja [összetevők számú](../logic-apps/logic-apps-limits-and-config.md), többek között a kereskedelmi partnerek, szerződések, térképeket, sémákat, szerelvényeket, tanúsítványok, kötegkonfigurációk és így tovább.

A Logic Apps is kínál az alapszintű és standard szintű integrációs fiók támogatott Logic Apps SLA-val. Alapszintű integrációs fiók segítségével használhatja, ha csak szeretné, üzenetkezelés vagy olyan kisméretű vállalkozások partner, amely a kereskedelmi partneri kapcsolatban áll azzal a nagyobb üzleti entitásban szerepét. Standard szintű integrációs fiókok összetettebb B2B kapcsolatokat támogatja, és kezelheti entitások számának növelése. További információkért lásd: [az Azure díjszabása](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>További lépések

* [További tudnivalók a Logic Apps][whatis]
* [Az első logikai alkalmazás létrehozása][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

