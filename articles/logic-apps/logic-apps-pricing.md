---
title: Díjszabás & számlázás – Azure Logic Apps | Microsoft Docs
description: Ismerje meg, hogyan működik a díjszabás és a számlázás Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: e1285f6bc3bd081eae03cf1e5a124a86cac7a2ae
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335987"
---
# <a name="pricing-model-for-azure-logic-apps"></a>A Azure Logic Apps díjszabási modellje

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) segítségével olyan automatizált integrációs munkafolyamatokat hozhat létre és futtathat, amelyek méretezhetők a felhőben. Ez a cikk bemutatja, hogyan működik a számlázás és a díjszabás a Azure Logic Apps. A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Fogyasztási díjszabási modell

A nyilvános vagy "globális" Azure Logic Apps szolgáltatásban futó új logikai alkalmazások esetében csak a ténylegesen használt funkciókért kell fizetnie. Ezek a logikai alkalmazások egy fogyasztáson alapuló csomagot és díjszabási modellt használnak. A logikai alkalmazásban minden lépés egy művelet, és Azure Logic Apps méter a logikai alkalmazásban futó összes művelet.

A műveletek például a következők:

* A speciális műveleteket kiváltó eseményindítók. Minden logikai alkalmazásnak szüksége van egy triggerre az első lépésként.
* ["Beépített" vagy natív műveletek](../connectors/apis-list.md#built-in) , mint például a http, a Azure functions és a API Management hívások
* A [felügyelt összekötők](../connectors/apis-list.md##managed-connectors) , például az Outlook 365, a Dropbox stb. meghívása
* Vezérlési folyamat lépései, például hurkok, feltételes utasítások stb.

[](../connectors/apis-list.md#managed-connectors) A standard szintű összekötőket a [standard szintű összekötő](https://azure.microsoft.com/pricing/details/logic-apps)díja alapján számítjuk fel. Az általánosan [elérhető](../connectors/apis-list.md#managed-connectors) nagyvállalati összekötőket a [vállalati összekötők díjszabása](https://azure.microsoft.com/pricing/details/logic-apps)alapján számítjuk fel, míg a nyilvános előzetes verziójú vállalati összekötők díját a [standard szintű összekötő](https://azure.microsoft.com/pricing/details/logic-apps)díja alapján számítjuk fel

További információ a számlázás működéséről [Eseményindítók](#triggers) és [műveletek](#actions)esetén.

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Rögzített díjszabási modell

Az [ *integrációs szolgáltatási környezet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) olyan logikai alkalmazások létrehozását és futtatását teszi lehetővé, amelyek hozzáférhetnek az Azure-beli virtuális hálózatok erőforrásaihoz. Az ISE-on belül futó új Logic apps esetén a következő képességekhez [rögzített havi árat](https://azure.microsoft.com/pricing/details/logic-apps) kell fizetnie:

* [Beépített eseményindítók és műveletek](../connectors/apis-list.md#built-in)

* [Standard szintű összekötők](../connectors/apis-list.md#managed-connectors)

* [Vállalati összekötők](../connectors/apis-list.md#enterprise-connectors) tetszőleges számú kapcsolattal

* [Integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) használata további díjak nélkül, az [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)alapján:

  * **Prémium SKU**: Egyetlen [standard szintű](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrációs fiók

  * **Fejlesztői SKU**: Egyetlen [ingyenes szintű](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrációs fiók

  Minden ISE SKU 5 teljes integrációs fiókra korlátozódik. További díjakért több integrációs fiók is tartozhat az ISE SKU alapján:

  * **Prémium SKU**: Akár négy szabványos fiók. Nincsenek ingyenes vagy alapszintű fiókok.

  * **Fejlesztői SKU**: Akár 4 további standard fiók, vagy akár 5 teljes standard fiók. Nincsenek alapszintű fiókok.

További információ az integrációs fiók korlátairól: [Logic apps korlátok és konfiguráció](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Az [integrációs fiók szintjeiről és azok díjszabási modelljéről](#integration-accounts) a jelen témakör későbbi részében olvashat bővebben. 

A prémium ISE SKU esetében az alapegység rögzített kapacitással rendelkezik, így ha több átviteli sebességre van szüksége, akkor akár a létrehozáskor, akár a későbbiekben [több skálázási egységet is hozzáadhat](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity). A fejlesztői ISE SKU nem képes további skálázási egységek hozzáadására. Az ISE-ben futó logikai alkalmazások nem vállalnak adatmegőrzési költségeket.

> [!NOTE]
> Az ISE-ben a beépített eseményindítók és műveletek megjelenítik az **alapcímkét** , és UGYANABBAN az ISE-ben futnak, mint a Logic apps. A standard és a vállalati összekötők, amelyek megjelenítik az **ISE** -címkét, UGYANABBAN az ISE-ben futnak, mint a Logic apps. Az ISE-címkét nem megjelenítő összekötők a globális Logic Apps szolgáltatásban futnak.

A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="connectors"></a>

## <a name="connectors"></a>Összekötők

A Azure Logic Apps-összekötők a logikai alkalmazások számára megkönnyítik a felhőben vagy a helyszínen lévő alkalmazásokhoz, [](#triggers)szolgáltatásokhoz és rendszerekhez való hozzáférést, így eseményindítókat, [műveleteket](#actions)vagy mindkettőt biztosítanak. Az összekötők a standard vagy a vállalat kategóriába tartoznak. Az összekötők áttekintését lásd: [összekötők Azure Logic Appshoz](../connectors/apis-list.md). Ha nem érhetők el előre elkészített összekötők a logikai alkalmazásokban használni kívánt REST API-khoz, létrehozhat [Egyéni összekötőket](https://docs.microsoft.com/connectors/custom-connectors), amelyek csak a REST API-k körébe tartozó burkolók. Az egyéni összekötők szabványos összekötőként vannak kiszámlázva. A következő szakaszokban további információk találhatók az eseményindítók és műveletek számlázásával kapcsolatban.

<a name="triggers"></a>

## <a name="triggers"></a>Triggerek

Az eseményindítók olyan speciális műveletek, amelyek egy adott esemény bekövetkezésekor létrehoznak egy Logic app-példányt. Az eseményindítók különböző módokon működnek, amelyek befolyásolják a logikai alkalmazás mérési módját. Itt láthatók a Azure Logic Appsban található különféle triggerek:

* **Lekérdezési trigger**: Ez az aktiválás folyamatosan ellenőrzi a végpontot olyan üzenetek esetében, amelyek megfelelnek a logikai alkalmazás-példány létrehozásának és a munkafolyamat megkezdésének feltételeinek. Ha a rendszer még akkor sem hoz létre logikai alkalmazás-példányt, Logic Apps minden lekérdezési kérelem végrehajtásként. A lekérdezési időköz megadásához állítsa be a triggert a Logic app Designer használatával.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook-trigger**: Ez az indító arra vár, hogy az ügyfél egy kérést küldjön egy adott végpontnak. A webhook-végpontnak elküldett kérelmek művelet-végrehajtásnak számítanak. A kérelem és a HTTP webhook eseményindítója például webhook-eseményindítókat is tartalmaz.

* **Ismétlődési eseményindító**: Ez az eseményindító létrehoz egy Logic app-példányt az Eseményindítóban beállított Ismétlődési intervallum alapján. Beállíthat például egy ismétlődési eseményindítót, amely háromhavonta vagy összetettebb ütemezésen fut.

<a name="actions"></a>

## <a name="actions"></a>Műveletek

A "beépített" műveletek (például a HTTP) Azure Logic Apps mérőszámok natív műveletként. A beépített műveletek közé tartoznak például a HTTP-hívások, a Azure Functions vagy API Management hívásai, valamint a folyamat lépései, például a feltételek, a hurkok és a Switch utasítások. Mindegyik műveletnek saját Művelettípus van. Például az összekötőket meghívó [](https://docs.microsoft.com/connectors) műveletek "ApiConnection" típusúak. Ezek az összekötők standard vagy nagyvállalati összekötőként vannak besorolva, amelyek díjszabása a megfelelő [díjszabás](https://azure.microsoft.com/pricing/details/logic-apps)alapján történik. Az *előzetes* verzióban elérhető vállalati összekötők standard szintű összekötőként lesznek felszámítva.

A Azure Logic Apps az összes sikeres és sikertelen műveletet végrehajtja végrehajtásként. Logic Apps azonban nem méri ezeket a műveleteket:

* A nem kielégíthető feltételek miatt kihagyott műveletek
* Olyan műveletek, amelyek nem futnak, mert a logikai alkalmazás a befejezés előtt leállt

A hurokon belül futó műveletek esetében a Azure Logic Apps minden egyes ciklusnál megszámolja az egyes műveleteket a hurokban. Tegyük fel például, hogy van egy "for each" ciklus, amely egy listát dolgoz fel. A hurokban lévő műveletek számával rendelkező listaelemek számának és a hurok elindítását elindító művelet megadásával Logic Apps méteres műveletet. Így a 10 listaelem kiszámítása (10 * 1) + 1, amely 11 művelet végrehajtását eredményezi.

## <a name="disabled-logic-apps"></a>Letiltott logikai alkalmazások

A letiltott logikai alkalmazások nem számítanak fel díjat, mert nem tudnak új példányokat létrehozni, miközben le vannak tiltva. A logikai alkalmazások letiltása után a jelenleg futó példányok eltarthat egy ideig, mielőtt teljesen leállnak.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Integrációs fiókok

A [rögzített díjszabási modell](https://azure.microsoft.com/pricing/details/logic-apps) olyan [integrációs fiókokra](logic-apps-enterprise-integration-create-integration-account.md) vonatkozik, ahol a [B2B-és EDI](logic-apps-enterprise-integration-b2b.md) -és [XML-feldolgozási](logic-apps-enterprise-integration-xml.md) funkcióit a Azure Logic apps további költség nélkül vizsgálhatja, fejlesztheti és tesztelheti. Az egyes Azure-előfizetések esetében az [integrációs fiókok egy adott korlátja](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)is lehet. Az egyes integrációs fiókok akár a kereskedelmi [](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)partnereket, a szerződéseket, a térképeket, a sémákat, a szerelvényeket, a tanúsítványokat, a Batch-konfigurációkat és egyebeket is tartalmazhatnak.

A Azure Logic Apps ingyenes, alapszintű és standard integrációs fiókokat kínál. Az alapszintű és a standard szintű csomagokat a Logic Apps szolgáltatói szerződés (SLA) támogatja, az ingyenes szintet pedig SLA nem támogatja, és korlátozza az átviteli sebességet és a használatot. Az ingyenes szintű integrációs fiókok kivételével az egyes Azure-régiókban több integrációs fiók is lehet. A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/).

Ha a [Premium vagy a Developer](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) [ *integrációs szolgáltatási környezettel* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)rendelkezik, az ISE-ben 5 teljes integrációs fiók tartozhat. Ha szeretné megtudni, hogyan működik a rögzített díjszabási modell egy ISE esetében, tekintse meg a jelen témakör előző [rögzített díjszabási modell](#fixed-pricing) című szakaszát. A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps).

Az ingyenes, az alapszintű vagy a standard integrációs fiók közötti választáshoz tekintse át az alábbi használati esetek leírását:

* **Ingyenes**: Ha kipróbálhatja a feltáró forgatókönyveket, nem éles környezeteket

* Alapszintű: Ha csak az üzenetkezelést szeretné használni, vagy olyan kisméretű üzleti partnernek kell működnie, amely egy nagyobb üzleti egységgel rendelkező kereskedelmi partneri kapcsolattal rendelkezik

* **Standard**: Ha összetettebb VÁLLALATKÖZI kapcsolatokkal és nagyobb számú entitással rendelkezik, amelyeket kezelni kell

<a name="data-retention"></a>

## <a name="data-retention"></a>Adatmegőrzés

Az integrációs szolgáltatási környezetben (ISE) futó logikai alkalmazások kivételével a logikai alkalmazás futtatási előzményeiben tárolt összes bemenet és kimenet számlázása a logikai alkalmazás [futtatásának megőrzési időtartama](logic-apps-limits-and-config.md#run-duration-retention-limits)alapján történik. Az ISE-ben futó logikai alkalmazások nem vállalnak adatmegőrzési költségeket. A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps).

A logikai alkalmazás tárterület-felhasználásának figyeléséhez a következőket teheti:

* Itt megtekintheti a logikai alkalmazás által havonta használt tárolási egységek számát GB-ban.
* Megtekintheti egy adott művelet bemeneteit és kimeneteit a logikai alkalmazás futtatási előzményeiben.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Logikai alkalmazás tárterület-felhasználásának megtekintése

1. A Azure Portal keresse meg és nyissa meg a logikai alkalmazást.

1. A logikai alkalmazás menüjében, a **figyelés**területen válassza a **metrikák**lehetőséget.

1. A jobb oldali ablaktábla **diagram címe**területén a **metrika** listából válassza a **Számlázási használat lehetőséget a tárolási felhasználás végrehajtásához**.

   Ez a metrika adja meg a raktározási egységek számát GB/hó egységben.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>A művelet bemeneti és kimeneti méretének megtekintése

1. A Azure Portal keresse meg és nyissa meg a logikai alkalmazást.

1. A logikai alkalmazás menüjében válassza az **Áttekintés**lehetőséget.

1. A jobb oldali ablaktáblán, a **futtatási előzmények**területen válassza ki azt a futtatást, amelybe be szeretné jelölni a bemeneteket és kimeneteket.

1. A **logikai alkalmazás futtatása**területen válassza a **Futtatás részletek**lehetőséget.

1. Válassza ki a megtekinteni kívánt műveletet a **logikai alkalmazás futtatása részletek** panelen, a műveletek táblában, amely felsorolja az egyes műveletek állapotát és időtartamát.

1. A **logikai alkalmazás műveleti** ablaktábláján keresse meg a művelet bemeneteit és kimeneteit a bemenetek és kimenetek  **hivatkozás**alatt.

## <a name="next-steps"></a>További lépések

* [További információ a Azure Logic Apps](logic-apps-overview.md)
* [Az első logikai alkalmazás létrehozása](quickstart-create-first-logic-app-workflow.md)
