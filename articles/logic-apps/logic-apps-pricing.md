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
ms.date: 05/22/2019
ms.openlocfilehash: 04b1d0eda85972517155f80488ad590fb56619ab
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190685"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Az Azure Logic Apps díjszabási modell

[Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) hoz létre, és automatizált munkafolyamatokat futtatni, amely segítséget nyújt a felhőben skálázhatja. Ez a cikk ismerteti az Azure Logic Apps számlázási és díjszabás működését. Díjszabási információkért tekintse meg a [Azure Logic Apps díjszabási](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Használatalapú díjszabási modellje

Új logic Apps, amely a nyilvános vagy "globális" az Azure Logic Apps szolgáltatásban akkor csak azért fizet, használja. Ezek a logic apps fogyasztásalapú terv és díjszabási modell használata. A logikai alkalmazás definíciójában minden lépése egy műveletet. Ha például műveletek a következők:

* Eseményindítók, amelyek speciális műveleteket. Minden logikai alkalmazás első lépéseként eseményindító szükséges.
* "Beépített" vagy a natív műveleteket, például HTTP-hívások Azure Functions és az API Management, és így tovább
* Például az Outlook 365, a Dropbox és más összekötők hívásainak
* A folyamat lépései hurkok, feltételes utasításokat, például a vezérlőelemet, és így tovább

Az Azure Logic Apps-mérőszámai összes műveletet, amely a logikai alkalmazás futtatását. További információ a számlázási működése [eseményindítók](#triggers) és [műveletek](#actions).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Rögzített díjszabási modell

Egy [ *integrációs szolgáltatás környezet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) módon biztosít a saját, elkülönített és dedikált hozhat létre és futtassa a logic apps egy Azure virtuális hálózatban lévő erőforrások eléréséhez. Az ISE-ben futtatható új logic Apps fizet egy [rögzített havidíj](https://azure.microsoft.com/pricing/details/logic-apps) a beépített műveletek, eseményindítók és a standard szintű összekötők.

Az ISE-ben is tartalmaz egy ingyenes vállalati összekötőt, amely tartalmazza a lehető legtöbb *kapcsolatok* ahogyan szeretné. További vállalati összekötők használati díját a [vállalati használati díj](https://azure.microsoft.com/pricing/details/logic-apps). Csak általánosan elérhető vállalati összekötők számoljuk el a vállalati használati díj. Vállalati összekötők nyilvános előzetes verzió díja a [Standard összekötő arány](https://azure.microsoft.com/pricing/details/logic-apps).

> [!NOTE]
> Az ISE-ben, a beépített eseményindítók és műveletek megjelenítése a **Core** címkét, és futtassa a logic Apps a azonos ISE-ben. Standard és vállalati összekötők, amelyek megjelenítik a **ISE** címke futtatása a logic apps, az azonos ISE-ben. Ne jelenjen meg az ISE címke összekötőket futtassa a globális Logic Apps szolgáltatásban.

Az ISE alapegység javította a kapacitást, ha a kapacitás növelése érdekében van szüksége, [több skálázási egység hozzáadása](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity), vagy a létrehozás során vagy később. A Logic apps egy ISE-ben futó nem költségekkel adatok megőrzése.

Díjszabási információkért tekintse meg a [Azure Logic Apps díjszabási](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="connectors"></a>

## <a name="connectors"></a>Összekötők

Az Azure Logic Apps-összekötők segítségével azáltal, hogy a logikai alkalmazás hozzáférés-alkalmazások, szolgáltatások és a felhőbeli és helyszíni rendszerek [eseményindítók](#triggers), [műveletek](#actions), vagy mindkettőt. Összekötők besorolt Standard vagy Enterprise. Ezeket az összekötőket kapcsolatos áttekintéséhez lásd: [Azure Logic Apps összekötői](../connectors/apis-list.md). Ha nincsenek előre elkészített összekötők a logic Apps használni kívánt REST API-k érhetők el, létrehozhat [egyéni összekötők](https://docs.microsoft.com/connectors/custom-connectors), amely körül, amelyek csak burkolókat REST API-k vannak. Egyéni összekötők, a standard szintű összekötők számítjuk fel. A következő szakaszok további információt a hogyan számlázzák a aktiválása és a műveletek.

<a name="triggers"></a>

## <a name="triggers"></a>Eseményindítók

Eseményindítók olyan speciális művelet, hozzon létre egy logikaialkalmazás-példányt egy meghatározott esemény. Eseményindítók különböző módokon, amelyek befolyásolják a logikai alkalmazás milyen forgalmi díjas jár el. Az alábbiakban a különböző típusú eseményindítókat, amelyek az Azure Logic Appsben létezik:

* **Lekérdezési eseményindító**: Ez az eseményindító folyamatosan ellenőrzi a végpont létrehozásához egy logikaialkalmazás-példányt, és elindítja a munkafolyamatot a feltételeknek megfelelő üzenetek. Logic Apps még ha nincs logikaialkalmazás-példányt jön létre, mert egy mérőszámai egyes lekérdezési kérelmek. Adja meg a lekérdezési időköz, állítsa be – a Logic App Designerben az eseményindító.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook-eseményindító**: Ez az eseményindító megvárja, amíg egy ügyfél kérelmet küld egy adott végpontnak. Minden egyes a webhook-végpontot küldött kérelem számít egy művelet végrehajtását. Például a kérés és a HTTP-Webhook eseményindító mindkét webhook eseményindítók.

* **Ismétlődési trigger**: Ez az eseményindító létrehoz egy logikaialkalmazás-példányt, amely beállította az eseményindító-ismétlődési időköze alapján. Például beállíthat egy ismétlődési eseményindítót, amely három naponta fut, vagy egy összetettebb ütemezés szerint.

<a name="actions"></a>

## <a name="actions"></a>Műveletek

Az Azure Logic Apps-mérőszámai "beépített" műveletek, például a HTTP-n natív műveletként használható. Beépített műveletek például HTTP-hívások, az Azure Functions vagy az API Management hívások közé tartozik, és feltételek, hurkok, mint például a folyamat lépései szabályozhatja, és switch-utasítások. Minden egyes műveletnek a saját művelet típusa. Ha például olyan műveletek, [összekötők](https://docs.microsoft.com/connectors) az "ApiConnection" típusa lehet. Ezeket az összekötőket besorolt Standard vagy a vállalati összekötők, amelyek mérése történik azoknak a alapján [díjszabás](https://azure.microsoft.com/pricing/details/logic-apps). Vállalati összekötők *előzetes* Standard összekötők díjkötelesek.

Az Azure Logic Apps-végrehajtások, az összes sikeres és sikertelen műveletek mérőszámai. A Logic Apps azonban ezek a műveletek nem mérni:

* Műveletek beolvasása teljesítetlen feltételek miatt kihagyva
* Műveleteket, amelyeket nem futtatható, mert a logikai alkalmazás a befejezés előtt leállt

Hurkok belül futó műveletek, az Azure Logic Apps száma minden műveletet a hurok minden ciklusban. Tegyük fel például, egy "mindegyikre" hurkot, amely a folyamatok listáját. Logic Apps-mérőszámok, hogy a szám és lista a ciklus a művelet elemei a hurok műveletek számát, és hozzáadja a művelet, amely elindítja a ciklus. Tehát a számítási 10-elemek listáját,-e (10 * 1) + 1, 11, műveletvégrehajtások eredményez.

## <a name="disabled-logic-apps"></a>Letiltva a logic apps

Le van tiltva a logic apps nem kell fizetnie, mert azok le van tiltva, nem hozható létre új példányokat.
Miután letiltja az egy logikai alkalmazást, a jelenleg futó példányok eltarthat egy ideig, mielőtt teljes mértékben meggátolni.

## <a name="integration-accounts"></a>Integration-fiókok

A rögzített díjszabási modell vonatkozik [integrációs fiókok](logic-apps-enterprise-integration-create-integration-account.md) , ismerje meg, fejlesztheti és tesztelheti a [B2B- és EDI](logic-apps-enterprise-integration-b2b.md) és [XML-feldolgozás](logic-apps-enterprise-integration-xml.md) szolgáltatások Azure Logic Apps nélkül További költség.
Egy integrációs fiókban rendelkezhet minden egyes Azure-régióban. Minden integrációs fiók adott is tárolhatja [összetevők számú](../logic-apps/logic-apps-limits-and-config.md), többek között a kereskedelmi partnerek, szerződések, térképeket, sémákat, szerelvényeket, tanúsítványok, kötegkonfigurációk és így tovább.

Az Azure Logic Apps ingyenes, alapszintű és standard szintű integrációs fiókok kínál. Az alapszintű és Standard szinten által támogatott a Logic Apps szolgáltatásiszint-szerződés (SLA), míg az ingyenes szint nem támogatja a szolgáltatásiszint-szerződés és a korlátozások vonatkoznak az átviteli sebesség és a használat.

Választhat egy ingyenes, alapszintű vagy standard szintű integrációs fiók:

* **Ingyenes**: Ha szeretné kipróbálni a felderítő forgatókönyvek esetén nem termelési forgatókönyvekhez tartozó.

* **Alapszintű**: Ha azt szeretné, hogy csak üzenetkezelés vagy kisvállalati partner-kiszolgálóként, amely rendelkezik a kereskedelmi partneri kapcsolatban van-e a nagyobb üzleti entitásban.

* **Standard szintű**: Ha összetettebb B2B-kapcsolatok és a megnövekedett számát, amely a következőket kell tennie entitások rendelkezik a kezeléséhez.

Díjszabási információkért tekintse meg a [Azure Logic Apps díjszabási](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="data-retention"></a>

## <a name="data-retention"></a>Adatmegőrzés

A logikai alkalmazás alapján, hogy futtassa az integráció service-környezet (ISE), az összes bemeneteit és kimeneteit tárolja a logikai alkalmazás futtatási előzmények díjat kell fizetnie kivételével a logic apps [futtassa a megőrzési időszak](logic-apps-limits-and-config.md#run-duration-retention-limits). A Logic apps egy ISE-ben futó nem költségekkel adatok megőrzése. Díjszabási információkért tekintse meg a [Azure Logic Apps díjszabási](https://azure.microsoft.com/pricing/details/logic-apps).

A logikai alkalmazás tárolófelhasználásának figyeléséhez segítségével a következőket teheti:

* Megtekintheti a tárolási egységek számát, amely a logikai alkalmazás havi GB-ban.
* A logikai alkalmazás futtatási előzményeinek megtekintése egy bizonyos művelet bemeneteit és kimeneteit méretei.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Logikai alkalmazás storage használatának megtekintése

1. Az Azure Portalon keresse meg és nyissa meg a logikai alkalmazást.

1. A logikai alkalmazás menüjében alatt **figyelés**válassza **metrikák**.

1. A jobb oldali ablaktáblán alatt **diagramcím**, az a **metrika** listáról válassza ki **Storage használati végrehajtások használatát számlázási**.

   Ez a metrika biztosítja a tárolási használatalapú egységek számát GB / hó első számlázása.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Művelet bemeneti megtekintése és a kimeneti méret

1. Az Azure Portalon keresse meg és nyissa meg a logikai alkalmazást.

1. A logikai alkalmazás menüjében kattintson a **áttekintése**.

1. A jobb oldali ablaktáblán a **futtatási előzmények**, válassza ki, amely rendelkezik a bemeneti adatok, és visszaadja a ellenőrizni kívánt futtató.

1. A **a logikai alkalmazás futtatásának**, válassza a **Futtatás részletei**.

1. Az a **logikai alkalmazás futtatásának részletei** ablaktáblán, a műveletek tábla, amely felsorolja az egyes műveletek állapotának és időtartama, válassza ki a megtekinteni kívánt műveletet.

1. Az a **Logikaialkalmazás-művelet** ablaktáblán kattintson a keresés rendre alatt jelennek meg, hogy a művelet bemeneteit és kimeneteit méretei **bemenetek hivatkozása** és **kimenetek hivatkozása**.

## <a name="next-steps"></a>További lépések

* [További információ az Azure Logic Apps](logic-apps-overview.md)
* [Az első logikai alkalmazás létrehozása](quickstart-create-first-logic-app-workflow.md)
