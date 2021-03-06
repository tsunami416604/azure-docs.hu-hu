---
title: Díjszabás & számlázási modell
description: A Azure Logic Apps díjszabásának és számlázásának áttekintése
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 9243d089b4a000066ec03dbeeccd046db374f558
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673110"
---
# <a name="pricing-model-for-azure-logic-apps"></a>A Azure Logic Apps díjszabási modellje

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) segítségével olyan automatizált integrációs munkafolyamatokat hozhat létre és futtathat, amelyek méretezhetők a felhőben. Ez a cikk bemutatja, hogyan működik a számlázás és a díjszabás a Azure Logic Apps. A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Fogyasztási díjszabási modell

A nyilvános, "globális", több-bérlős Azure Logic Apps szolgáltatásban futó új logikai alkalmazások esetében csak a ténylegesen használt funkciókért kell fizetnie. Ezek a logikai alkalmazások egy fogyasztáson alapuló csomagot és díjszabási modellt használnak. A logikai alkalmazásban minden lépés egy művelet, és Azure Logic Apps méter a logikai alkalmazásban futó összes művelet.

A műveletek például a következők:

* A speciális műveleteket [kiváltó eseményindítók](#triggers). Minden logikai alkalmazásnak szüksége van egy triggerre az első lépésként.

* ["Beépített" vagy natív műveletek](../connectors/apis-list.md#built-in) , mint például a http, a Azure functions és a API Management hívások

* A [felügyelt összekötők](../connectors/apis-list.md#managed-connectors) , például az Outlook 365, a Dropbox stb. meghívása

* A munkafolyamat-műveletek, például a hurkok, a feltételes utasítások és egyebek [szabályozása](../connectors/apis-list.md#control-workflow)

A standard [szintű összekötőket](../connectors/apis-list.md#managed-connectors) a [standard szintű összekötő](https://azure.microsoft.com/pricing/details/logic-apps)díja alapján számítjuk fel. Az általánosan [elérhető nagyvállalati](../connectors/apis-list.md#managed-connectors) összekötőket a [vállalati összekötők díjszabása](https://azure.microsoft.com/pricing/details/logic-apps)alapján számítjuk fel, míg a nyilvános előzetes verziójú vállalati összekötők [díját a standard szintű összekötő](https://azure.microsoft.com/pricing/details/logic-apps)díja alapján számítjuk fel

További információ a számlázás működéséről az [Eseményindítók](#triggers) és [műveletek](#actions) szintjén. A korlátokkal kapcsolatos további információkért lásd: [a Azure Logic apps korlátai és konfigurálása](logic-apps-limits-and-config.md).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Rögzített díjszabási modell

Az [ *integrációs szolgáltatási környezet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) elkülönített módszert biztosít olyan logikai alkalmazások létrehozására és futtatására, amelyek hozzáférhetnek egy Azure-beli virtuális hálózat erőforrásaihoz. Az ISE-ben futó logikai alkalmazások nem vállalnak adatmegőrzési költségeket. Ha egy ISE-t hoz létre, és csak a létrehozás során, akkor választhat egy [ISE-szintet vagy "SKU"](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)-t, amely különböző [díjszabási díjszabással](https://azure.microsoft.com/pricing/details/logic-apps)rendelkezik:

* **Prémium** szintű ISE: ez az SKU alapegysége rögzített kapacitású, de ha nagyobb átviteli sebességre van szüksége, [további méretezési egységeket adhat hozzá](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) az ISE létrehozásakor vagy azután. Az ISE-korlátokat lásd: [a Azure Logic apps korlátai és konfigurálása](logic-apps-limits-and-config.md#integration-service-environment-ise).

* **Fejlesztői** ISE: ez az SKU nem képes felskálázásra, nincs szolgáltatói szerződés (SLA), és nincs közzétett korlát. Ezt az SKU-t csak kísérletezéshez, fejlesztéshez és teszteléshez használja, nem éles környezetben, sem pedig a teljesítmény teszteléséhez.

Az ISE-ben létrehozott és futtatott Logic apps esetén a következő képességekhez [rögzített árat](https://azure.microsoft.com/pricing/details/logic-apps) kell fizetnie (a használat fizetése ellenében):

* [Beépített](../connectors/apis-list.md#built-in) eseményindítók és műveletek

  Az ISE-ben a beépített eseményindítók és műveletek megjelenítik az **alapcímkét** , és UGYANABBAN az ISE-ben futnak, mint a Logic apps.

* [Standard szintű](../connectors/apis-list.md#managed-connectors) összekötők és [nagyvállalati](../connectors/apis-list.md#enterprise-connectors) összekötők, amelyekkel tetszőleges számú vállalati kapcsolattal rendelkezhet

   A standard és a vállalati összekötők, amelyek megjelenítik az **ISE** -címkét, UGYANABBAN az ISE-ben futnak, mint a Logic apps. Azok az összekötők, amelyek nem jelenítik meg az ISE-címkét a nyilvános, a "globális", a több-bérlős Logic Apps szolgáltatásban. A rögzített díjszabás a több-bérlős szolgáltatásban futó összekötők esetében is érvényes, ha az ISE-ben futó Logic Apps használatával használja őket.

* [Integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) használata további díjak nélkül, az [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)alapján:

  * **Prémium** szintű ISE SKU: egyetlen [standard szintű](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrációs fiók

  * **Fejlesztői** ISE SKU: egyetlen [ingyenes rétegbeli](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrációs fiók

  További díjakért több integrációs fiókot is létrehozhat az ISE számára [a teljes korlátig](logic-apps-limits-and-config.md#integration-account-limits). 

  * **Prémium** szintű ISE SKU: legfeljebb 19 további szabványos fiók. Ingyenes vagy alapszintű fiókok használata nem engedélyezett.

  * **Fejlesztői** ISE SKU: akár 19 további szabványos fiók, ha már rendelkezik ingyenes fiókkal vagy 20 teljes standard fiókkal, ha nem rendelkezik ingyenes fiókkal. Nem engedélyezett alapszintű fiók.

  További információ az integrációs fiók korlátairól: [Azure Logic apps korlátai és konfigurálása](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Az [integrációs fiók szintjeiről és azok díjszabási modelljéről](#integration-accounts) a jelen témakör későbbi részében olvashat bővebben.

<a name="connectors"></a>

## <a name="connectors"></a>Összekötők

A Azure Logic Apps-összekötők a logikai alkalmazások számára megkönnyítik a felhőben vagy a helyszínen lévő alkalmazásokhoz, szolgáltatásokhoz és rendszerekhez való hozzáférést, így [eseményindítókat](#triggers), [műveleteket](#actions)vagy mindkettőt biztosítanak. Az összekötők a standard vagy a vállalat kategóriába tartoznak. Az összekötők áttekintését lásd: [összekötők Azure Logic Appshoz](../connectors/apis-list.md). Ha nem érhetők el előre elkészített összekötők a logikai alkalmazásokban használni kívánt REST API-khoz, létrehozhat [Egyéni összekötőket](/connectors/custom-connectors), amelyek csak a REST API-k körébe tartozó burkolók. Az egyéni összekötők szabványos összekötőként vannak kiszámlázva. A következő szakaszokban további információk találhatók az eseményindítók és műveletek számlázásával kapcsolatban.

<a name="triggers"></a>

## <a name="triggers"></a>Triggerek

Az eseményindító mindig a logikai alkalmazás munkafolyamatának első lépése, és egy speciális művelet, amely egy Logic App-példány létrehozására és futtatására szolgál, ha meghatározott feltételek teljesülnek, vagy egy adott esemény történik. Az eseményindítók különböző módokon működnek, amelyek befolyásolják a logikai alkalmazás mérési módját. Itt láthatók a Azure Logic Appsban található különféle triggerek:

* **Ismétlődési eseményindító**: ezt az általános eseményindítót használhatja, amely nem kifejezetten semmilyen szolgáltatásra vagy rendszerre vonatkozik, a logikai alkalmazások munkafolyamatainak elindításához és az Eseményindítóban beállított ismétlődési időköz alapján létrehozott logikai alkalmazás-példány létrehozásához. Beállíthat például egy ismétlődési eseményindítót, amely háromhavonta vagy összetettebb ütemezésen fut.

* **Lekérdezési eseményindító**: ezt a speciális ismétlődési eseményindítót használhatja, amely általában egy adott szolgáltatáshoz vagy rendszerhez tartozó felügyelt összekötőhöz van társítva, hogy olyan eseményeket vagy üzeneteket keressen, amelyek megfelelnek a Logic App-példány létrehozásának és futtatásának feltételeinek az Eseményindítóban beállított Ismétlődési intervallum alapján. Akkor is, ha a rendszer nem hoz létre logikai alkalmazás-példányt, például ha kihagyja az eseményindítókat, a Logic Apps szolgáltatás minden lekérdezési kérelem végrehajtásként szolgál. A lekérdezési időköz megadásához állítsa be a triggert a Logic app Designer használatával.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook-trigger**: a lekérdezési trigger használata helyett egy webhook-trigger használatával megvárhatja, hogy az ügyfél kérést küldjön a logikai alkalmazásnak egy adott VÉGPONTI URL-címen. A webhook-végpontnak elküldett minden kérelem művelet-végrehajtásnak számít. A kérelem és a HTTP webhook eseményindító például általános webhook-eseményindítókat is tartalmaz. A szolgáltatások vagy rendszerek egyes összekötői is rendelkeznek webhook-eseményindítókkal.

<a name="actions"></a>

## <a name="actions"></a>Műveletek

A "beépített" műveletek (például a HTTP) Azure Logic Apps mérőszámok natív műveletként. A beépített műveletek közé tartoznak például a HTTP-hívások, a Azure Functions vagy API Management hívásai, valamint a folyamat lépései, például a feltételek, a hurkok és a Switch utasítások. Mindegyik műveletnek saját Művelettípus van. Például az [összekötőket](/connectors) meghívó műveletek "ApiConnection" típusúak. Ezek az összekötők standard vagy nagyvállalati összekötőként vannak besorolva, amelyek [díjszabása](https://azure.microsoft.com/pricing/details/logic-apps)a megfelelő díjszabás alapján történik. Az *előzetes* verzióban elérhető vállalati összekötők standard szintű összekötőként lesznek felszámítva.

A Azure Logic Apps az összes sikeres és sikertelen műveletet végrehajtja végrehajtásként. Logic Apps azonban nem méri ezeket a műveleteket:

* A nem kielégíthető feltételek miatt kihagyott műveletek
* Olyan műveletek, amelyek nem futnak, mert a logikai alkalmazás a befejezés előtt leállt

A hurokon belül futó műveletek esetében a Azure Logic Apps minden egyes ciklusnál megszámolja az egyes műveleteket a hurokban. Tegyük fel például, hogy van egy "for each" ciklus, amely egy listát dolgoz fel. A hurokban lévő műveletek számával rendelkező listaelemek számának és a hurok elindítását elindító művelet megadásával Logic Apps méteres műveletet. Így a 10 listaelem kiszámítása (10 * 1) + 1, amely 11 művelet végrehajtását eredményezi.

## <a name="disabled-logic-apps"></a>Letiltott logikai alkalmazások

A letiltott logikai alkalmazások nem számítanak fel díjat, mert nem tudnak új példányokat létrehozni, miközben le vannak tiltva. A logikai alkalmazások letiltása után a jelenleg futó példányok eltarthat egy ideig, mielőtt teljesen leállnak.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Integrációs fiókok

A [rögzített díjszabási modell](https://azure.microsoft.com/pricing/details/logic-apps) olyan [integrációs fiókokra](logic-apps-enterprise-integration-create-integration-account.md) vonatkozik, ahol a [B2B-és EDI](logic-apps-enterprise-integration-b2b.md) -és [XML-feldolgozási](logic-apps-enterprise-integration-xml.md) funkcióit a Azure Logic apps további költség nélkül vizsgálhatja, fejlesztheti és tesztelheti. Az egyes Azure-előfizetések esetében az [integrációs fiókok egy adott korlátja](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)is lehet. Mindegyik integrációs fiók legfeljebb egy meghatározott [korlátot](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)képes tárolni, amelyek közé tartoznak a kereskedelmi partnerek, a szerződések, a térképek, a sémák, a szerelvények, a tanúsítványok, a Batch-konfigurációk stb.

A Azure Logic Apps ingyenes, alapszintű és standard integrációs fiókokat kínál. Az alapszintű és a standard szintű csomagokat a Logic Apps szolgáltatói szerződés (SLA) támogatja, az ingyenes szintet pedig SLA nem támogatja, és korlátozza a régió rendelkezésre állását, az átviteli sebességet és a használatot. Az ingyenes szintű integrációs fiókok kivételével az egyes Azure-régiókban több integrációs fiók is lehet. A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/).

Ha [ *integrációs szolgáltatási környezettel* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)rendelkezik, az ISE külön díj nélkül használhat egyetlen integrációs fiókot, bár a tartalmazott fióktípus az [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)-tól függ. [További díjakért](#fixed-pricing)több integrációs fiókot is LÉTREHOZHAT az ISE számára az [integrációs fiókok teljes korlátozásával](logic-apps-limits-and-config.md#integration-account-limits). Ha szeretné megtudni, hogyan működik a rögzített díjszabási modell egy ISE esetében, tekintse meg a jelen témakör előző [rögzített díjszabási modell](#fixed-pricing) című szakaszát. A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps).

Az ingyenes, az alapszintű vagy a standard integrációs fiók közötti választáshoz tekintse át az alábbi használati esetek leírását:

* **Ingyenes**: Ha szeretné kipróbálni a feltáró forgatókönyveket, nem pedig éles környezetben. Ez a lehetőség csak az Azure nyilvános régiói esetében érhető el, például az USA nyugati régiójában vagy Délkelet-Ázsiában, de nem az [Azure China 21Vianet](/azure/china/overview-operations) vagy a [Azure Government](../azure-government/documentation-government-welcome.md).

* **Alapszintű**: Ha csak az üzenetkezelést szeretné használni, vagy olyan kisméretű üzleti partnerként tevékenykedik, amely egy nagyobb üzleti egységgel rendelkező kereskedelmi partneri kapcsolattal rendelkezik

* **Standard**: ha összetettebb vállalatközi kapcsolatokkal és nagyobb számú entitással rendelkezik, amelyeket kezelni kell

<a name="data-retention"></a>

## <a name="data-retention"></a>Adatmegőrzés

Az integrációs szolgáltatási környezetben (ISE) futó logikai alkalmazások kivételével a logikai alkalmazás futtatási előzményeiben tárolt összes bemenet és kimenet számlázása a logikai alkalmazás [futtatásának megőrzési időtartama](logic-apps-limits-and-config.md#run-duration-retention-limits)alapján történik. Az ISE-ben futó logikai alkalmazások nem vállalnak adatmegőrzési költségeket. A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps).

A logikai alkalmazás tárterület-felhasználásának figyeléséhez a következőket teheti:

* Itt megtekintheti a logikai alkalmazás által havonta használt tárolási egységek számát GB-ban.

* Megtekintheti egy adott művelet bemeneteit és kimeneteit a logikai alkalmazás futtatási előzményeiben.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Logikai alkalmazás tárterület-felhasználásának megtekintése

1. A Azure Portal keresse meg és nyissa meg a logikai alkalmazást.

1. A logikai alkalmazás menüjében, a **figyelés** területen válassza a **metrikák** lehetőséget.

1. A jobb oldali ablaktábla **diagram címe** területén a **metrika** listából válassza a **Számlázási használat lehetőséget a tárolási felhasználás végrehajtásához**.

   Ez a metrika adja meg a raktározási egységek számát GB/hó egységben.

   > [!NOTE]
   > Előfordulhat, hogy az 500 MB-nál kevesebb memóriát használó futtatások nem jelennek meg a figyelés nézetben, de továbbra is számlázás alatt állnak.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>A művelet bemeneti és kimeneti méretének megtekintése

1. A Azure Portal keresse meg és nyissa meg a logikai alkalmazást.

1. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget.

1. A jobb oldali ablaktáblán, a **futtatási előzmények** területen válassza ki azt a futtatást, amelybe be szeretné jelölni a bemeneteket és kimeneteket.

1. A **logikai alkalmazás futtatása** területen válassza a **Futtatás részletek** lehetőséget.

1. Válassza ki a megtekinteni kívánt műveletet a **logikai alkalmazás futtatása részletek** panelen, a műveletek táblában, amely felsorolja az egyes műveletek állapotát és időtartamát.

1. A **logikai alkalmazás műveleti** ablaktábláján keresse meg a művelet bemeneteit és kimeneteit. A **bemenetek** és **kimenetek hivatkozás** alatt keresse meg a bemenetekre és kimenetekre mutató hivatkozásokat.

   > [!NOTE]
   > A hurkok esetében csak a legfelső szintű műveletek jelennek meg a bemenetek és kimenetek méretében. Beágyazott hurkok esetén a bemenetek és a kimenetek nulla méretet és hivatkozások nélkül jelennek meg.

## <a name="next-steps"></a>Következő lépések

* [További információ a Azure Logic Apps](logic-apps-overview.md)
* [Az első logikai alkalmazás létrehozása](quickstart-create-first-logic-app-workflow.md)
