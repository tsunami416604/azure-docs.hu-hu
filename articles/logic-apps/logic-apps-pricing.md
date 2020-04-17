---
title: Díjszabási & számlázási modell
description: A díjszabási és számlázási modell működésének áttekintése az Azure Logic Apps alkalmazásokban
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: f47c7412bdd5ada1e50d1005b8e740e3f46ffd8d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536233"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Az Azure Logic Apps díjszabási modellje

[Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) segítségével hozhat létre és futtathat automatizált integrációs munkafolyamatokat, amelyek a felhőben skálázhatók. Ez a cikk ismerteti, hogyan működik a számlázásés a díjszabás i. Azure Logic Apps. Az árakról a [Logic Apps díjszabása (Logic Apps Pricing) (Logikai alkalmazások díjszabása) témakörben](https://azure.microsoft.com/pricing/details/logic-apps)található.

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Fogyasztási árképzési modell

A nyilvánosan futó új logikai alkalmazások, a "globális", a több-bérlős Azure Logic Apps szolgáltatás, csak akkor kell fizetnie, amit használ. Ezek a logikai alkalmazások egy fogyasztásalapú csomag és díjszabási modell használata. A logikai alkalmazásban minden lépés egy művelet, és az Azure Logic Apps a logikai alkalmazásban futó összes műveletet méri.

A műveletek például a következők:

* [Eseményindítók](#triggers), amelyek speciális műveletek. Minden logikai alkalmazás hoz létre egy eseményindító, mint az első lépés.

* ["Beépített" vagy natív műveletek,](../connectors/apis-list.md#built-in) például HTTP, hívások az Azure Functions és az API Management, és így tovább

* Felügyelt [összekötők,](../connectors/apis-list.md#managed-connectors) például Outlook 365, Dropbox és így tovább hívások

* [Munkafolyamat-műveletek,](../connectors/apis-list.md#control-workflow) például hurkok, feltételes utasítások és így tovább vezérlése

[A standard összekötők](../connectors/apis-list.md#managed-connectors) díja a [Standard összekötőáron kerül felszámításra.](https://azure.microsoft.com/pricing/details/logic-apps) Az általánosan elérhető [vállalati összekötők](../connectors/apis-list.md#managed-connectors) díja a [Vállalati összekötők árán,](https://azure.microsoft.com/pricing/details/logic-apps)míg a nyilvános előzetes verziójú vállalati összekötők a Standard [összekötők árán](https://azure.microsoft.com/pricing/details/logic-apps)kerülnek felszámításra .

További információ aszámlázás működéséről az [eseményindítók](#triggers) és [műveletek](#actions) szintjén. A korlátokkal kapcsolatos további információkért tekintse meg [az Azure Logic Apps korlátaiés konfigurációja című témakört.](logic-apps-limits-and-config.md)

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Rögzített árképzési modell

Az [ *integrációs szolgáltatási környezet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) egy elszigetelt módja annak, hogy hozzon létre és futtasson logikai alkalmazásokat, amelyek hozzáférhetnek az Azure virtuális hálózat erőforrásaihoz. Az ISE-ben futó logikai alkalmazások nem viselnek adatmegőrzési költségeket. Az ISE létrehozásakor és csak a létrehozás során választhat egy [ISE-szintet vagy "Termékváltozatot",](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)amely eltérő [árazási rátákkal rendelkezik:](https://azure.microsoft.com/pricing/details/logic-apps)

* **Prémium** ISE: A termékváltozat alapegysége rögzített kapacitással rendelkezik, de ha nagyobb átviteli kapacitásra van szüksége, [további méretezési egységeket adhat hozzá](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) az ISE létrehozása során vagy azt követően. Az ISE-korlátozásokról az [Azure Logic Apps korlátai és konfigurációja.](logic-apps-limits-and-config.md#integration-service-environment-ise)

* **Fejlesztő** ISE: Ez a termékváltozat nem képes a felskálázás, nincs szolgáltatásiszint-szerződés (SLA), és nem közzétett korlátok. Ezt a termékváltozatot csak kísérletezéshez, fejlesztéshez és teszteléshez használja, éles vagy teljesítménytesztelésre.

Az ISE-ben létrehozott és futtatott logikai alkalmazások ért [rögzített havi díjat](https://azure.microsoft.com/pricing/details/logic-apps) kell fizetnie a következő képességekért:

* [Beépített eseményindítók](../connectors/apis-list.md#built-in) és műveletek

  Az ISE-n belül a beépített eseményindítók és műveletek megjelenítik a **Core** címkét, és ugyanabban az ISE-ben futnak, mint a logikai alkalmazások.

* [Szabványos](../connectors/apis-list.md#managed-connectors) összekötők és [nagyvállalati](../connectors/apis-list.md#enterprise-connectors) összekötők, amelyek segítségével annyi nagyvállalati kapcsolattal rendelkezik, amennyit csak szeretne

   Az **ISE-címkét** megjelenítő szabványos és vállalati összekötők ugyanabban az ISE-ben futnak, mint a logikai alkalmazások. Összekötők, amelyek nem jelenítik meg az ISE címke fut a nyilvános, "globális", több-bérlős Logic Apps szolgáltatás. A rögzített havi díjszabás a több-bérlős szolgáltatásban futó összekötőkre is vonatkozik, ha azokat az ISE-ben futó logikai alkalmazásokkal használja.

* [Integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) használata további költségek nélkül, az [ISE Termékváltozat](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)alapján:

  * **Prémium** ISE Termékváltozat: Egyetlen [standardszintű](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrációs fiók

  * **Fejlesztő** ISE Termékváltozat: Egyetlen [ingyenes szintű](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrációs fiók

  Minden ISE termékváltozat legfeljebb 5 teljes integrációs fiók. További költségek esetén több integrációs fiókkal is rendelkezhet az ISE termékváltozat alapján:

  * **Prémium** ISE Termékváltozat: Legfeljebb négy további standard fiók. Nincs ingyenes vagy alapszintű fiók.

  * **Fejlesztő** ISE Termékváltozat: Legfeljebb 4 további Standard számla, vagy legfeljebb 5 standard számla. Nincs enek alapfiók.

  Az integrációs fiókkorlátokról az [Azure Logic Apps korlátai és konfigurációja](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)című témakörben talál további információt. Ebben a témakörben további információaz [integrációs fiókszintekről és azok díjszabási modelljéről.](#integration-accounts)

<a name="connectors"></a>

## <a name="connectors"></a>Összekötők

Az Azure Logic Apps-összekötők segítségével a logikai alkalmazás hozzáférést alkalmazások, szolgáltatások és rendszerek a felhőben vagy a helyszínen [azáltal, hogy eseményindítók,](#triggers) [műveletek](#actions), vagy mindkettő. Az összekötők standard vagy vállalati besorolásúak. Az összekötők áttekintését az Azure Logic Apps összekötői című [témakörben találja.](../connectors/apis-list.md) Ha a rest api-k, amelyek a logikai alkalmazásokban használni kívánt rest API-khoz nem érhetők el előre összeállított összekötők, [létrehozhat egyéni összekötőket,](https://docs.microsoft.com/connectors/custom-connectors)amelyek csak burkolók a REST API-k körül. Az egyéni összekötők díja standard összekötőkként van számlázva. A következő szakaszok további információt nyújtanak az eseményindítók és műveletek számlázásáról.

<a name="triggers"></a>

## <a name="triggers"></a>Eseményindítók

Az eseményindítók olyan speciális műveletek, amelyek egy adott esemény bekövetkezésekekkor logikai alkalmazáspéldányt hoznak létre. Az eseményindítók különböző módokon működnek, amelyek befolyásolják a logikai alkalmazás mérésének módját. Az Azure Logic Apps-alkalmazásokban található különféle eseményindítók:

* **Lekérdezési eseményindító**: Ez az eseményindító folyamatosan ellenőrzi a végpont ot az üzenetek, amelyek megfelelnek a feltételeknek egy logikai alkalmazáspéldány létrehozása és a munkafolyamat indítása. Még akkor is, ha nem logikai alkalmazáspéldány jön létre, logic apps méter minden lekérdezési kérelmet, mint egy végrehajtás. A lekérdezési időköz megadásához állítsa be az eseményindítót a Logic App Designer en keresztül.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook-eseményindító:** Ez az eseményindító arra vár, hogy egy ügyfél kérést küldjön egy adott végpontra. A webhook-végpontnak küldött minden egyes kérelem műveletvégrehajtásnak számít. Például a kérelem és a HTTP Webhook eseményindító egyaránt webhook eseményindítók.

* **Ismétlődési eseményindító**: Ez az eseményindító logikai alkalmazáspéldányt hoz létre az eseményindítóban beállított ismétlődési időköz alapján. Beállíthatja például a háromnaponta vagy összetettebb ütemezés szerint futó Ismétlődés eseményindítót.

<a name="actions"></a>

## <a name="actions"></a>Műveletek

Az Azure Logic Apps natív műveletekként méri a "beépített" műveleteket, például a HTTP-t. A beépített műveletek közé tartoznak például a HTTP-hívások, az Azure Functions vagy az API Management hívásai, valamint a folyamatlépések, például a feltételek, a hurkok és a kapcsolóutasítások. Minden műveletnek saját művelettípusa van. Például az összekötők hívását tartalmazó műveletek "ApiConnection" [típusúak.](https://docs.microsoft.com/connectors) Ezek az összekötők standard vagy nagyvállalati összekötőkként vannak besorolva, amelyek a megfelelő [árképzésalapján](https://azure.microsoft.com/pricing/details/logic-apps)vannak felmérve. Az *előzetes* verzióban a vállalati összekötők standard összekötőként vannak feltöltve.

Az Azure Logic Apps minden sikeres és sikertelen műveletet végrehajtásiként mér. A Logic Apps azonban nem méri ezeket a műveleteket:

* A teljesítetlen körülmények miatt kihagyott műveletek
* Nem futtatott műveletek, mert a logikai alkalmazás a befejezés előtt leállt

A hurkokon belül futó műveletek hez az Azure Logic Apps minden egyes ciklusban a ciklusban számít. Tegyük fel például, hogy van egy "minden" ciklus, amely feldolgozza a listát. A Logic Apps egy műveletet mér ebben a ciklusban a listaelemek számának és a ciklusban lévő műveletek számának szorzatával, és hozzáadja a ciklust indító műveletet. Tehát a 10-elemből álló lista számítása (10 * 1) + 1, ami 11 műveletvégrehajtást eredményez.

## <a name="disabled-logic-apps"></a>Letiltott logikai alkalmazások

A letiltott logikai alkalmazások nem kerülek felszámolásra, mert nem hozhatnak létre új példányokat, amíg le vannak tiltva. A logikai alkalmazás letiltása után a jelenleg futó példányok leállhatnak egy ideig, mielőtt teljesen leállnak.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Integrációs fiókok

A [rögzített díjszabási modell](https://azure.microsoft.com/pricing/details/logic-apps) az [integrációs fiókokra](logic-apps-enterprise-integration-create-integration-account.md) vonatkozik, ahol további költségek nélkül felfedezheti, fejlesztheti és tesztelheti az Azure Logic Apps [B2B és EDI](logic-apps-enterprise-integration-b2b.md) és [XML-feldolgozási](logic-apps-enterprise-integration-xml.md) funkcióit. Minden Azure-előfizetés rendelkezhet az [integrációs fiókok egy meghatározott korlátjáig.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) Minden integrációs fiók tárolhatja a összetevők meghatározott [korlátjáig,](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)amelyek magukban foglalják a kereskedelmi partnerek, megállapodások, térképek, sémák, szerelvények, tanúsítványok, kötegkonfigurációk, és így tovább.

Az Azure Logic Apps ingyenes, alapszintű és standard integrációs fiókokat kínál. Az alapszintű és standard szintű csomagokat a Logic Apps szolgáltatásszintű szerződés (SLA) támogatja, míg az ingyenes szint nem támogatja az SLA, és korlátozza az átviteli és használati korlátokat. Az ingyenes rétegintegrációs fiókok kivételével minden Azure-régióban több integrációs fiókkal is rendelkezhet. Az árak, lásd: [Logic Apps árképzés.](https://azure.microsoft.com/pricing/details/logic-apps/)

Ha [ *integrációs szolgáltatási környezettel* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)rendelkezik, [prémium vagy fejlesztői,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)az ISE-nek 5 teljes integrációs fiókja lehet. Ha meg szeretné tudni, hogyan működik a rögzített díjszabási modell egy ISE-hez, tekintse meg a témakör korábbi [rögzített díjszabási modell](#fixed-pricing) szakaszát. Az árak, lásd: [Logic Apps árképzés.](https://azure.microsoft.com/pricing/details/logic-apps)

Ha ingyenes, alapszintű vagy standard integrációs fiók közül szeretne választani, tekintse át a használati eset leírásokat:

* **Ingyenes**: Ha feltáró forgatókönyveket szeretne kipróbálni, nem pedig éles forgatókönyveket

* **Alapszintű**: Ha csak üzenetkezelést szeretne, vagy olyan kisüzleti partnerként szeretne viselkedni, amely kereskedelmi partneri kapcsolatban áll egy nagyobb üzleti szervezettel

* **Normál**: Ha összetettebb B2B kapcsolatokkal és megnövelt számú entitást kell kezelnie

<a name="data-retention"></a>

## <a name="data-retention"></a>Adatmegőrzés

Az integrációs szolgáltatási környezetben (ISE) futó logikai alkalmazások kivételével a logikai alkalmazás futtatási előzményeiben tárolt összes bemenet és kimenet számlázásra kerül egy logikai alkalmazás [futtatási megőrzési időszaka](logic-apps-limits-and-config.md#run-duration-retention-limits)alapján. Az ISE-ben futó logikai alkalmazások nem viselnek adatmegőrzési költségeket. Az árak, lásd: [Logic Apps árképzés.](https://azure.microsoft.com/pricing/details/logic-apps)

A logikai alkalmazás tárolási felhasználásának figyelése érdekében a következőket teheti:

* Tekintse meg a tárolóegységek számát GB-ban, amelyet a logikai alkalmazás havonta használ.
* Tekintse meg egy adott művelet bemenetek és kimenetek a logikai alkalmazás futtatási előzmények méretei.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Logikai alkalmazás tárolási felhasználásának megtekintése

1. Az Azure Portalon keresse meg és nyissa meg a logikai alkalmazást.

1. A logikai alkalmazás menüjében válassza **a Képmutatók** **lehetőséget.**

1. A jobb oldali ablaktáblában a **Diagram címe**csoport ban a **Metrika** listában válassza a **Tárolási felhasználás végrehajtásai számlázási felhasználásának lehetőséget.**

   Ez a metrika a számlázott, GB-ban tárolt tárolási felhasználási egységek számát adja meg.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Műveletbemeneti és kimeneti méretek megtekintése

1. Az Azure Portalon keresse meg és nyissa meg a logikai alkalmazást.

1. A logikai alkalmazás menüjében válassza **az Áttekintés**lehetőséget.

1. A jobb oldali ablaktáblában az **Előzmények futtatása**csoportban válassza ki azt a futtatást, amely rendelkezik az ellenőrizni kívánt bemenetekkel és kimenetekkel.

1. A **Logikai alkalmazás futtatása**csoportban válassza **a Részletek futtatása lehetőséget.**

1. A **Logikai alkalmazás részletes** ablaktábláján, a műveletek táblázatában, amely felsorolja az egyes műveletek állapotát és időtartamát, jelölje ki a megtekinteni kívánt műveletet.

1. A **Logikai alkalmazás műveletpanelén** keresse meg, hogy a művelet bemenetei és kimenetei méretei a **Bemenetek hivatkozás** és a **Kimenetek hivatkozás**alatt jelenjenek meg.

## <a name="next-steps"></a>További lépések

* [További információ az Azure Logic-alkalmazásokról](logic-apps-overview.md)
* [Az első logikai alkalmazás létrehozása](quickstart-create-first-logic-app-workflow.md)
