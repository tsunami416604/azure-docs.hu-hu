---
title: Hozzáférés Azure-beli virtuális hálózatokhoz Azure Logic Apps integrációs szolgáltatási környezetekkel (ISEs)
description: Ez az Áttekintés azt ismerteti, hogyan érhetők el az integrációs szolgáltatási környezetek (ISEs) az Azure Virtual Networks (virtuális hálózatok) segítségével
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 831a1457d865429fd53af1887a14c363b806300c
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516607"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Hozzáférés az Azure Virtual Network-erőforrásokhoz a Azure Logic Apps integrációs szolgáltatási környezetek (ISEs) használatával

Időnként a logikai alkalmazásoknak és az integrációs fiókoknak olyan biztonságos erőforrásokhoz, például virtuális gépekhez és más rendszerekhez vagy szolgáltatásokhoz kell hozzáférnie, amelyek egy Azure-beli [virtuális hálózaton](../virtual-network/virtual-networks-overview.md)belül vannak. A hozzáférés beállításához [létrehozhat egy *integrációs szolgáltatási környezetet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) , amelyen futtathatja a logikai alkalmazásokat, és létrehozhatja az integrációs fiókokat.

Ha ISE-t hoz létre, az Azure befecskendezi az ISE-t az Azure-beli virtuális hálózatba, amely ezután a Logic Apps szolgáltatás privát és elkülönített példányát telepíti az Azure-beli virtuális hálózatba. Ez a magánhálózati példány dedikált erőforrásokat használ, például a tárterületet, és külön fut a nyilvános "globális" Logic Apps szolgáltatástól. Ha elválasztja az elkülönített privát példányt és a nyilvános globális példányt, azzal csökkentheti annak hatását, hogy más Azure-bérlők milyen hatással lehetnek az alkalmazások teljesítményére, ami más néven ["zajos szomszédok"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

Az ISE létrehozása után a logikai alkalmazás vagy integrációs fiók létrehozása után kiválaszthatja az ISE-t a logikai alkalmazás vagy az integrációs fiók helyeként:

![Integrációs szolgáltatási környezet kiválasztása](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

A logikai alkalmazás mostantól közvetlenül hozzáférhet a virtuális hálózathoz tartozó vagy azokhoz csatlakozó rendszerekhez a következő elemek bármelyikének használatával:

* A rendszer **ISE**-címkével ellátott összekötője, például SQL Server
* Egyalapcímkével ellátott beépített trigger vagy művelet, például a http-trigger vagy a művelet
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

Az ISE logikai alkalmazásai ugyanazt a felhasználói élményt és hasonló képességeket nyújtják, mint a globális Logic Apps szolgáltatás. Nem csupán ugyanazokat a beépített eseményindítókat, beépített műveleteket és összekötőket használhatja a globális Logic Apps szolgáltatásból, de az ISE-specifikus összekötőket is használhatja. Íme például néhány szabványos összekötő, amely az ISE-ben futó verziókat kínál:

* Azure Blob Storage, File Storage és Table Storage
* Azure Queues, Azure Service Bus, Azure Event Hubs és IBM MQ
* FTP és SFTP – SSH
* SQL Server, SQL Data Warehouse, Azure Cosmos DB
* AS2, X12 és EDIFACT

Az ISE és a nem ISE összekötők közötti különbség az eseményindítók és műveletek futtatási helyein található:

* Az ISE-ben a beépített eseményindítók és műveletek (például a HTTP) mindig ugyanabban az ISE-ben futnak, mint a logikai alkalmazás, és megjeleníti az **alapvető** címkét.

  ![Alapszintű beépített eseményindítók és műveletek kiválasztása](./media/connect-virtual-network-vnet-isolated-environment-overview/select-core-built-in-actions-triggers.png)

* Az ISE-ben futó összekötők nyilvánosan üzemeltetett verzióit elérhetővé válnak a globális Logic Apps szolgáltatásban. Az olyan összekötők esetében, amelyek két verziót kínálnak, az **ISE** -címkével rendelkező összekötők mindig UGYANABBAN az ISE-ben futnak, mint a logikai alkalmazás. Az **ISE** címke nélküli összekötők a globális Logic apps szolgáltatásban futnak.

  ![ISE-összekötők kiválasztása](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

Az ISE nagyobb korlátokat biztosít a futtatási időtartam, a tárolás megőrzése, az átviteli sebesség, a HTTP-kérés és a válasz időtúllépése, az üzenetek mérete és az egyéni összekötői kérelmek esetében. További információ: [Azure Logic apps korlátai és konfigurálása](logic-apps-limits-and-config.md).

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKU-i

Az ISE létrehozásakor kiválaszthatja a fejlesztői SKU-t vagy prémium SKU-t. Az alábbi két SKU közötti különbségeket láthatja:

* **Fejlesztői**

  A olyan alacsonyabb szintű ISE, amely a kísérletezéshez, fejlesztéshez és teszteléshez használható, de éles és teljesítménybeli teszteléshez nem. A fejlesztői SKU beépített eseményindítókat és műveleteket, standard szintű összekötőket, vállalati összekötőket és egyetlen [ingyenes rétegbeli](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrációs fiókot tartalmaz a rögzített havi díjakhoz. Ez az SKU azonban nem tartalmaz semmilyen szolgáltatói szerződést (SLA), a kapacitás növelésének lehetőségeit vagy az újrahasznosítás során felmerülő redundanciát, ami azt jelenti, hogy késések vagy leállások merülhetnek fel.

* **Prémium**

  Egy olyan ISE-t biztosít, amely az éles környezethez használható, és tartalmaz SLA-támogatást, beépített eseményindítókat és műveleteket, standard összekötőket, vállalati összekötőket, egyetlen [standard szintű](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrációs fiókot, a kapacitás növelésének lehetőségeit és a redundanciát a rögzített havi díj újrahasznosítása.

> [!IMPORTANT]
> Az SKU beállítás csak az ISE létrehozásakor érhető el, és később nem módosítható.

A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/). A ISEs díjszabásának és számlázásának megismeréséhez tekintse meg a [Logic apps díjszabási modelljét](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE-végponti hozzáférés

Az ISE létrehozásakor dönthet úgy, hogy belső vagy külső hozzáférési végpontokat használ. Ezek a végpontok határozzák meg, hogy az ISE-beli logikai alkalmazásokban a kérelmek vagy a webhook-eseményindítók fogadhatnak-e hívásokat a virtuális hálózaton kívülről. Ezek a végpontok a logikai alkalmazások futtatási előzményeiben lévő bemenetekhez és kimenetekhez is hatással vannak.

* **Belső**: Privát végpontok, amelyek lehetővé teszik az ISE-beli Logic apps-hívásokat, valamint a futtatási előzményekben lévő bemenetekhez és kimenetekhez való hozzáférést csak a *virtuális hálózaton belülről*
* **Külső**: Nyilvános végpontok, amelyek lehetővé teszik az ISE-beli logikai alkalmazások meghívását, valamint a *virtuális hálózaton kívülről* a futtatási előzményekben lévő bemenetekhez és kimenetekhez való hozzáférést

> [!IMPORTANT]
> A hozzáférési végpont beállítás csak az ISE létrehozásakor érhető el, és később nem módosítható.

<a name="on-premises"></a>

## <a name="access-to-on-premises-data-sources"></a>Hozzáférés helyszíni adatforrásokhoz

Az Azure-beli virtuális hálózathoz csatlakozó helyszíni rendszerek esetében egy ISE-t kell beszúrnia a hálózatba, hogy a Logic apps közvetlenül hozzáférhessen ezekhez a rendszerekhez az alábbi elemek bármelyikének használatával:

* ISE – az adott rendszerhez tartozó verzió-összekötő, például SQL Server
* HTTP-művelet
* Egyéni összekötő

  * Ha olyan egyéni összekötővel rendelkezik, amely a helyszíni adatátjárót igényli, és az összekötőket az ISE-n kívül hozta létre, akkor az ISE-ben lévő Logic apps is használhatja ezeket az összekötőket.
  
  * Az ISE-ben létrehozott egyéni összekötők nem működnek a helyszíni adatátjáróval. Ezek az összekötők azonban közvetlenül képesek hozzáférni a helyszíni adatforrásokhoz, amelyek az ISE-t üzemeltető virtuális hálózathoz csatlakoznak. Ezért az ISE-ben a Logic apps valószínűleg nincs szüksége az adatátjáróra az ilyen erőforrásokkal való kommunikáció során.

Azon helyszíni rendszerekhez, amelyek nem csatlakoznak virtuális hálózathoz, vagy nem rendelkeznek ISE verziójú összekötővel, először be kell [állítania a helyszíni](../logic-apps/logic-apps-gateway-install.md) adatátjárót, mielőtt a logikai alkalmazások csatlakozni tudjanak a rendszerekhez.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrációs fiókok ISE-vel

Az integrációs fiókokat az integrációs szolgáltatási környezetben (ISE) belüli Logic apps-alkalmazásokkal is használhatja. Ezeknek az integrációs fiókoknak azonban *ugyanazt az ISE* -t kell használniuk, mint a társított logikai alkalmazások. Az ISE-beli Logic apps csak azokra az integrációs fiókokra hivatkozhat, amelyek ugyanabban az ISE-ban találhatók. Integrációs fiók létrehozásakor kiválaszthatja az ISE-t az integrációs fiókjának helyeként. Ha szeretné megtudni, hogyan működik a díjszabás és a számlázás az ISE-integrációs fiókok esetében, tekintse meg a [Logic apps díjszabási modelljét](../logic-apps/logic-apps-pricing.md#fixed-pricing). A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>További lépések

* [Kapcsolódás Azure-beli virtuális hálózatokhoz elkülönített logikai alkalmazásokból](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Összetevők hozzáadása az integrációs szolgáltatási környezetekhez](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Integrációs szolgáltatási környezetek kezelése](../logic-apps/ise-manage-integration-service-environment.md)
* További információ az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Ismerje meg az [Azure-szolgáltatások virtuális hálózati integrációját](../virtual-network/virtual-network-for-azure-services.md)
