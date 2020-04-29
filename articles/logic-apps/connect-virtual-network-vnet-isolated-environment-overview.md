---
title: Hozzáférés az Azure-beli virtuális hálózatokhoz
description: Az integrációs szolgáltatási környezetek (ISEs) súgójának áttekintése az Azure Virtual Networks (virtuális hálózatok) szolgáltatáshoz való hozzáféréshez
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 9d5e0c088fe773f16e1fc57f292ca812906aa09c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79127251"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Hozzáférés az Azure Virtual Network-erőforrásokhoz a Azure Logic Apps integrációs szolgáltatási környezetek (ISEs) használatával

Időnként a logikai alkalmazásoknak olyan biztonságos erőforrásokhoz, például virtuális gépekhez és más rendszerekhez vagy szolgáltatásokhoz kell hozzáférnie, amelyek egy Azure-beli [virtuális hálózaton](../virtual-network/virtual-networks-overview.md)belül vannak. A hozzáférés beállításához [létrehozhat egy *integrációs szolgáltatási környezetet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). Az ISE a Logic Apps szolgáltatás elkülönített példánya, amely dedikált erőforrásokat használ, és külön fut a "globális" több-bérlős Logic Apps szolgáltatástól.

A Logic apps önálló elkülönített példányban való futtatása segít csökkenteni a más Azure-bérlők által az alkalmazások teljesítményére gyakorolt hatást, más néven a ["zajos szomszédok" hatását](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). Az ISE a következő előnyöket is biztosítja:

* A saját statikus IP-címei, amelyek a több-bérlős szolgáltatásban a Logic apps által megosztott statikus IP-címektől eltérnek. Beállíthat egy nyilvános, statikus és kiszámítható kimenő IP-címet is a célszámítógépeken való kommunikációhoz. Így nem kell további tűzfal-megnyitásokat beállítania a célszámítógépeken az egyes ISE-rendszerek esetében.

* A futtatási időtartam, a tárterület megőrzése, az átviteli sebesség, a HTTP-kérés és a válasz időkorlátja, az üzenetek mérete és az egyéni összekötőre vonatkozó kérelmek száma nagyobb. További információ: [Azure Logic apps korlátai és konfigurálása](logic-apps-limits-and-config.md).

> [!NOTE]
> Néhány Azure-beli virtuális hálózat privát végpontokat ([Azure Private-hivatkozást](../private-link/private-link-overview.md)) használ az Azure-beli (Azure-beli) szolgáltatások, például az Azure Storage, a Azure Cosmos db vagy a Azure SQL Database, a partner Services vagy az Azure-ban üzemeltetett ügyfélszolgálatok számára. Ha a logikai alkalmazásoknak hozzá kell férniük a privát végpontokat használó virtuális hálózatokhoz, létre kell hoznia, telepítenie és futtatnia kell ezeket a logikai alkalmazásokat egy ISE-ben.

Ha ISE-t hoz létre, az Azure *befecskendez* vagy üzembe helyezi az ISE-t az Azure-beli virtuális hálózatában. Ezt az ISE-t használhatja azon logikai alkalmazások és integrációs fiókok helyeként, amelyeknek hozzáférésre van szükségük.

![Integrációs szolgáltatási környezet kiválasztása](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

A logikai alkalmazások a virtuális hálózathoz tartozó vagy ahhoz csatlakozó erőforrásokat a következő elemek használatával érhetik el, amelyek ugyanabban az ISE-ben futnak, mint a Logic apps:

* Egy alapcímkével **ellátott beépített**trigger vagy művelet, például a http-trigger vagy a művelet
* Az adott rendszer vagy szolgáltatás **ISE**-címkével ellátott összekötője
* Egyéni összekötő

Továbbra is használhat olyan összekötőket, amelyek nem rendelkeznek a **Core** vagy **ISE** címkével az ISE logikai alkalmazásaival. Ezek az összekötők Ehelyett a több-bérlős Logic Apps szolgáltatásban futnak. További információkat a következő részekben talál:

* [Elkülönített és több-bérlős](#difference)
* [Csatlakozás integrációs szolgáltatási környezetből](../connectors/apis-list.md#integration-service-environment)
* [ISE-összekötők](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> A Logic apps, a beépített eseményindítók, a beépített műveletek és az ISE-ben futó összekötők olyan díjszabási csomagot használnak, amely eltér a fogyasztási díjszabástól. További információ: [Logic apps díjszabási modell](../logic-apps/logic-apps-pricing.md#fixed-pricing). A díjszabással kapcsolatos részletekért tekintse meg a [Logic apps díjszabását](../logic-apps/logic-apps-pricing.md).

Ez az Áttekintés további információkat tartalmaz arról, hogy az ISE Hogyan biztosítja a logikai alkalmazások számára az Azure-beli virtuális hálózat közvetlen elérését, és összehasonlítja az ISE és a több-bérlős Logic Apps szolgáltatás közötti különbségeket.

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>Elkülönített és több-bérlős

Ha egy ISE-ben hoz létre és futtat logikai alkalmazásokat, ugyanazokat a felhasználói élményeket és hasonló képességeket kapja, mint a több-bérlős Logic Apps szolgáltatás. Az összes olyan beépített eseményindítót, műveletet és felügyelt összekötőt használhatja, amelyek elérhetők a több-bérlős Logic Apps szolgáltatásban. Egyes felügyelt összekötők további ISE-verziókat is biztosítanak. Az ISE-összekötők és a nem ISE összekötők közötti különbség a futtatásuk helyétől, valamint a Logic app Designerben lévő címkéktől, amikor egy ISE-n belül dolgozik.

![Olyan összekötők, amelyeken címkék találhatók az ISE-ben](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* A beépített eseményindítók és **műveletek az** alapcímkét jelenítik meg. Mindig ugyanabban az ISE-ben futnak, mint a logikai alkalmazás. Az **ISE** -címkét megjelenítő felügyelt összekötők a logikai alkalmazással megegyező ISE-ben is futnak.

  Íme például néhány összekötő, amely az ISE-verziókat kínálja:

  * Azure Blob Storage, File Storage és Table Storage
  * Azure Queues, Azure Service Bus, Azure Event Hubs és IBM MQ
  * FTP és SFTP – SSH
  * SQL Server, Azure SQL Data Warehouse, Azure Cosmos DB
  * AS2, X12 és EDIFACT

* Azok a felügyelt összekötők, amelyek nem jelenítenek meg további címkéket, mindig a több-bérlős Logic Apps szolgáltatásban futnak, de továbbra is használhatják ezeket az összekötőket egy ISE által üzemeltetett logikai alkalmazásban.

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>Hozzáférés a helyszíni rendszerekhez

Az Azure-beli virtuális hálózathoz csatlakoztatott helyszíni rendszerek vagy adatforrások eléréséhez az ISE-ben a Logic apps ezeket az elemeket használhatja:

* HTTP-művelet

* ISE – címkézett összekötő az adott rendszerhez

  > [!NOTE]
  > Ha Windows-hitelesítést kíván használni az SQL Server-összekötővel egy [integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), használja az összekötő nem ISE verzióját a helyszíni [adatátjáróval](../logic-apps/logic-apps-gateway-install.md). Az ISE-címkével ellátott verzió nem támogatja a Windows-hitelesítést.

* Egyéni összekötők

  * Ha olyan egyéni összekötővel rendelkezik, amely a helyszíni adatátjárót igényli, és az összekötőket az ISE-n kívül hozta létre, akkor az ISE-ben lévő Logic apps is használhatja ezeket az összekötőket.

  * Az ISE-ben létrehozott egyéni összekötők nem működnek a helyszíni adatátjáróval. Ezek az összekötők azonban közvetlenül képesek hozzáférni a helyszíni adatforrásokhoz, amelyek az ISE-t üzemeltető virtuális hálózathoz csatlakoznak. Ezért az ISE-ben a Logic apps valószínűleg nincs szüksége az adatátjáróra az ilyen erőforrásokkal való kommunikáció során.

Azon helyszíni rendszerekhez, amelyek nem csatlakoznak virtuális hálózathoz, vagy nem rendelkeznek ISE-címkézett összekötővel, először [be kell állítania a helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md) , mielőtt a logikai alkalmazások csatlakozni tudjanak a rendszerekhez.

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKU-i

Az ISE létrehozásakor kiválaszthatja a fejlesztői SKU-t vagy prémium SKU-t. Az alábbi két SKU közötti különbségeket láthatja:

* **Fejlesztő**

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

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrációs fiókok ISE-vel

Az integrációs fiókokat az integrációs szolgáltatási környezetben (ISE) belüli Logic apps-alkalmazásokkal is használhatja. Ezeknek az integrációs fiókoknak azonban *ugyanazt az ISE* -t kell használniuk, mint a társított logikai alkalmazások. Az ISE-beli Logic apps csak azokra az integrációs fiókokra hivatkozhat, amelyek ugyanabban az ISE-ban találhatók. Integrációs fiók létrehozásakor kiválaszthatja az ISE-t az integrációs fiókjának helyeként. Ha szeretné megtudni, hogyan működik a díjszabás és a számlázás az ISE-integrációs fiókok esetében, tekintse meg a [Logic apps díjszabási modelljét](../logic-apps/logic-apps-pricing.md#fixed-pricing). A díjszabással kapcsolatban lásd: [Logic apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>További lépések

* [Kapcsolódás Azure-beli virtuális hálózatokhoz Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* További információ az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Ismerje meg az [Azure-szolgáltatások virtuális hálózati integrációját](../virtual-network/virtual-network-for-azure-services.md)
