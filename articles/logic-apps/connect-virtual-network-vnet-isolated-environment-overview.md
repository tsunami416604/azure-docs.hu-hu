---
title: Hozzáférés az Azure-beli virtuális hálózatokhoz
description: Az integrációs szolgáltatási környezetek (ISEs) súgójának áttekintése az Azure Virtual Networks (virtuális hálózatok) szolgáltatáshoz való hozzáféréshez
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 1f743384f467e4559412fa1a46d48011b568d249
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191583"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Hozzáférés az Azure Virtual Network-erőforrásokhoz a Azure Logic Apps integrációs szolgáltatási környezetek (ISEs) használatával

Időnként a logikai alkalmazásoknak és az integrációs fiókoknak olyan biztonságos erőforrásokhoz, például virtuális gépekhez és más rendszerekhez vagy szolgáltatásokhoz kell hozzáférnie, amelyek egy Azure-beli [virtuális hálózaton](../virtual-network/virtual-networks-overview.md)belül vannak. A hozzáférés beállításához [létrehozhat egy *integrációs szolgáltatási környezetet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) , amelyen futtathatja a logikai alkalmazásokat, és létrehozhatja az integrációs fiókokat.

Ha ISE-t hoz létre, az Azure *befecskendezi* az ISE-t az Azure-beli virtuális hálózatba, amely ezután a Logic Apps szolgáltatás privát és elkülönített példányát telepíti az Azure-beli virtuális hálózatba. Ez a magánhálózati példány dedikált erőforrásokat használ, például a tárterületet, és a nyilvános, "globális", több-bérlős Logic Apps szolgáltatástól függetlenül fut. Ha elválasztja az elkülönített privát példányt és a nyilvános globális példányt, azzal csökkentheti annak hatását, hogy más Azure-bérlők milyen hatással lehetnek az alkalmazások teljesítményére, ami más néven ["zajos szomszédok"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). Az ISE a saját statikus IP-címeivel is rendelkezik. Ezek az IP-címek a nyilvános, több-bérlős szolgáltatásban a logikai alkalmazások által megosztott statikus IP-címektől eltérnek.

Az ISE létrehozása után a logikai alkalmazás vagy integrációs fiók létrehozása után kiválaszthatja az ISE-t a logikai alkalmazás vagy az integrációs fiók helyeként:

![Integrációs szolgáltatási környezet kiválasztása](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

A logikai alkalmazás mostantól közvetlenül hozzáférhet a virtuális hálózathoz tartozó vagy azokhoz csatlakozó rendszerekhez ezen elemek bármelyikének használatával, amelyek a logikai alkalmazással megegyező ISE-ben futnak:

* A rendszer **ISE**-címkével ellátott összekötője
* Egy alapcímkével **ellátott beépített**trigger vagy művelet, például a http-trigger vagy a művelet
* Egyéni összekötő

Ez az Áttekintés további részleteket tartalmaz arról, hogy az ISE Hogyan biztosítja a logikai alkalmazások és az integrációs fiókok számára a közvetlen hozzáférést az Azure-beli virtuális hálózathoz, és összehasonlítja az ISE és a globális Logic Apps szolgáltatás közötti különbségeket.

> [!IMPORTANT]
> A Logic apps, a beépített eseményindítók, a beépített műveletek és az ISE-ben futó összekötők olyan díjszabási csomagot használnak, amely eltér a fogyasztási díjszabástól. A ISEs díjszabásának és számlázásának megismeréséhez tekintse meg a [Logic apps díjszabási modelljét](../logic-apps/logic-apps-pricing.md#fixed-pricing). A díjszabással kapcsolatban lásd: [Logic apps díjszabása](../logic-apps/logic-apps-pricing.md).
>
> Az ISE a futtatási időtartamot, a tárterület megőrzését, az átviteli sebességet, a HTTP-kérést és a válasz időtúllépését, az üzenetek méretét és az egyéni összekötői kérelmeket is megnövelte. 
> További információ: [Azure Logic apps korlátai és konfigurálása](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Elszigetelt és globális

Ha az Azure-ban létrehoz egy integrált szolgáltatási környezetet (ISE), akkor kiválaszthatja azt az Azure-beli virtuális hálózatot, ahová az ISE-t be szeretné *szúrni* . Az Azure ezután beadja vagy telepíti a Logic Apps szolgáltatás privát példányát a virtuális hálózatba. Ez a művelet egy elkülönített környezetet hoz létre, ahol a logikai alkalmazásokat dedikált erőforrásokon lehet létrehozni és futtatni. A logikai alkalmazás létrehozásakor kiválaszthatja az ISE-t az alkalmazás helyeként, így a logikai alkalmazás közvetlen hozzáférést biztosít a virtuális hálózathoz és a hálózatban lévő erőforrásokhoz.

Az ISE logikai alkalmazásai ugyanazt a felhasználói élményt és hasonló képességeket nyújtják, mint a nyilvános globális Logic Apps szolgáltatás. Az összes olyan beépített eseményindítót, műveletet és felügyelt összekötőt használhatja, amelyek elérhetők a globális Logic Apps szolgáltatásban. Egyes felügyelt összekötők további ISE-verziókat is biztosítanak. A különbség abban rejlik, hogy hol futnak, és a Logic app Designerben megjelenített címkék, amikor az ISE-n belül dolgozik.

![Olyan összekötők, amelyeken címkék találhatók az ISE-ben](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* A beépített eseményindítók és műveletek megjelenítik az alapcímkét, és mindig ugyanabban az ISE-ben futnak, **mint a logikai** alkalmazás. Az **ISE** -címkét megjelenítő felügyelt összekötők a logikai alkalmazással megegyező ISE-ben is futnak.

  Íme például néhány összekötő, amely az ISE-verziókat kínálja:

  * Azure Blob Storage, File Storage és Table Storage
  * Azure Queues, Azure Service Bus, Azure Event Hubs és IBM MQ
  * FTP és SFTP – SSH
  * SQL Server, Azure SQL Data Warehouse, Azure Cosmos DB
  * AS2, X12 és EDIFACT

* Azok a felügyelt összekötők, amelyek nem jelenítenek meg további címkéket, mindig a nyilvános globális Logic Apps szolgáltatásban futnak, de ezek az összekötők továbbra is használhatók ISE-alapú logikai alkalmazásokban.

Az ISE nagyobb korlátokat biztosít a futtatási időtartam, a tárolás megőrzése, az átviteli sebesség, a HTTP-kérés és a válasz időtúllépése, az üzenetek mérete és az egyéni összekötői kérelmek esetében. További információ: [Azure Logic apps korlátai és konfigurálása](logic-apps-limits-and-config.md).

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKU-i

Az ISE létrehozásakor kiválaszthatja a fejlesztői SKU-t vagy prémium SKU-t. Az alábbi két SKU közötti különbségeket láthatja:

* **Fejlesztői**

  A olyan alacsonyabb szintű ISE, amely a kísérletezéshez, fejlesztéshez és teszteléshez használható, de éles és teljesítménybeli teszteléshez nem. A fejlesztői SKU beépített eseményindítókat és műveleteket, standard szintű összekötőket, vállalati összekötőket és egyetlen [ingyenes rétegbeli](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrációs fiókot tartalmaz a rögzített havi díjakhoz. Ez az SKU azonban nem tartalmaz semmilyen szolgáltatói szerződést (SLA), a kapacitás növelésének lehetőségeit vagy az újrahasznosítás során felmerülő redundanciát, ami azt jelenti, hogy késések vagy leállások merülhetnek fel.

* **Prémium**

  Egy olyan ISE-t biztosít, amely az éles környezethez használható, és tartalmaz SLA-támogatást, beépített eseményindítókat és műveleteket, standard összekötőket, vállalati összekötőket, egyetlen [standard szintű](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrációs fiókot, a kapacitás növelésének lehetőségeit, valamint a rögzített havi díj miatti redundanciát.

> [!IMPORTANT]
> Az SKU beállítás csak az ISE létrehozásakor érhető el, és később nem módosítható.

A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/). A ISEs díjszabásának és számlázásának megismeréséhez tekintse meg a [Logic apps díjszabási modelljét](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE-végponti hozzáférés

Az ISE létrehozásakor dönthet úgy, hogy belső vagy külső hozzáférési végpontokat használ. A kiválasztott beállítás határozza meg, hogy az ISE-beli Logic apps-beli kérelem vagy webhook-eseményindítók fogadhatnak-e hívásokat a virtuális hálózaton kívülről is.

Ezek a végpontok a logikai alkalmazások futtatási előzményeiben lévő bemenetekhez és kimenetekhez is hatással lehetnek.

* **Belső**: olyan privát végpontok, amelyek lehetővé teszik az ISE-beli Logic Apps-alkalmazások meghívását, ahol a Logic apps bemeneteit és kimeneteit *csak a virtuális hálózatán belül* lehet megtekinteni és elérni

* **Külső**: nyilvános végpontok, amelyek lehetővé teszik az ISE-beli Logic Apps-alkalmazások meghívását, ahol a logikai alkalmazások bemeneteit és kimeneteit a *virtuális hálózatán kívülről*is megtekintheti és elérheti. Ha hálózati biztonsági csoportokat (NSG) használ, győződjön meg róla, hogy be van állítva a bejövő szabályokkal, hogy engedélyezze a futtatási előzmények bemeneteit és kimeneteit. További információ: az [ISE hozzáférésének engedélyezése](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

> [!IMPORTANT]
> A hozzáférési végpont beállítás csak az ISE létrehozásakor érhető el, és később nem módosítható.

<a name="on-premises"></a>

## <a name="access-to-on-premises-data-sources"></a>Hozzáférés helyszíni adatforrásokhoz

Az Azure-beli virtuális hálózathoz csatlakozó helyszíni rendszerek esetében egy ISE-t kell beszúrnia a hálózatba, hogy a Logic apps közvetlenül hozzáférhessen ezekhez a rendszerekhez az alábbi elemek bármelyikének használatával:

* HTTP-művelet

* ISE – címkézett összekötő az adott rendszerhez

  > [!NOTE]
  > Ha Windows-hitelesítést kíván használni az SQL Server-összekötővel egy [integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), használja az összekötő nem ISE verzióját a helyszíni [adatátjáróval](../logic-apps/logic-apps-gateway-install.md). Az ISE-címkével ellátott verzió nem támogatja a Windows-hitelesítést.

* Egyéni összekötő

  * Ha olyan egyéni összekötővel rendelkezik, amely a helyszíni adatátjárót igényli, és az összekötőket az ISE-n kívül hozta létre, akkor az ISE-ben lévő Logic apps is használhatja ezeket az összekötőket.

  * Az ISE-ben létrehozott egyéni összekötők nem működnek a helyszíni adatátjáróval. Ezek az összekötők azonban közvetlenül képesek hozzáférni a helyszíni adatforrásokhoz, amelyek az ISE-t üzemeltető virtuális hálózathoz csatlakoznak. Ezért az ISE-ben a Logic apps valószínűleg nincs szüksége az adatátjáróra az ilyen erőforrásokkal való kommunikáció során.

Azon helyszíni rendszerekhez, amelyek nem csatlakoznak virtuális hálózathoz, vagy nem rendelkeznek ISE-címkézett összekötővel, először [be kell állítania a helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md) , mielőtt a logikai alkalmazások csatlakozni tudjanak a rendszerekhez.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrációs fiókok ISE-vel

Az integrációs fiókokat az integrációs szolgáltatási környezetben (ISE) belüli Logic apps-alkalmazásokkal is használhatja. Ezeknek az integrációs fiókoknak azonban *ugyanazt az ISE* -t kell használniuk, mint a társított logikai alkalmazások. Az ISE-beli Logic apps csak azokra az integrációs fiókokra hivatkozhat, amelyek ugyanabban az ISE-ban találhatók. Integrációs fiók létrehozásakor kiválaszthatja az ISE-t az integrációs fiókjának helyeként. Ha szeretné megtudni, hogyan működik a díjszabás és a számlázás az ISE-integrációs fiókok esetében, tekintse meg a [Logic apps díjszabási modelljét](../logic-apps/logic-apps-pricing.md#fixed-pricing). A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Következő lépések

* [Kapcsolódás Azure-beli virtuális hálózatokhoz elkülönített logikai alkalmazásokból](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Összetevők hozzáadása az integrációs szolgáltatási környezetekhez](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Integrációs szolgáltatási környezetek kezelése](../logic-apps/ise-manage-integration-service-environment.md)
* További információ az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Ismerje meg az [Azure-szolgáltatások virtuális hálózati integrációját](../virtual-network/virtual-network-for-azure-services.md)
