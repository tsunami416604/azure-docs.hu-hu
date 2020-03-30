---
title: Hozzáférés az Azure virtuális hálózataihoz
description: Áttekintés arról, hogy az integrációs szolgáltatáskörnyezetek (ISEs-ek) hogyan segítik a logikai alkalmazásokat az Azure virtuális hálózatok (VNET) elérésében
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 9d5e0c088fe773f16e1fc57f292ca812906aa09c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127251"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Hozzáférés az Azure virtuális hálózati erőforrásaihoz az Azure Logic Apps alkalmazásból integrációs szolgáltatáskörnyezetek (ISEs) használatával

Néha a logikai alkalmazások nak hozzá kell férniük a biztonságos erőforrásokhoz, például a virtuális gépekhez (VM-ek) és más rendszerekhez vagy szolgáltatásokhoz, amelyek egy [Azure virtuális hálózaton](../virtual-network/virtual-networks-overview.md)belül vannak. A hozzáférés beállításához [létrehozhat egy *integrációs szolgáltatási környezetet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). Az ISE a Logic Apps szolgáltatás elkülönített példánya, amely dedikált erőforrásokat használ, és a "globális" több-bérlős Logic Apps szolgáltatástól elkülönítve fut.

A logikai alkalmazások saját elkülönített példányban való futtatása csökkenti a többi Azure-bérlő nek az alkalmazások teljesítményére gyakorolt hatását, más néven a ["zajos szomszédok" hatást.](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) Az ISE a következő előnyöket is biztosítja:

* Saját statikus IP-címek, amelyek elkülönülnek a statikus IP-címeket, amelyek a több-bérlős szolgáltatás logikai alkalmazások által megosztott. Egyetlen nyilvános, statikus és kiszámítható kimenő IP-címet is beállíthat a célrendszerekkel való kommunikációhoz. Így nem kell további tűzfal-nyílásokat beállítania ezeken a célrendszereken az egyes ISE-khez.

* A futtatási időtartam, a tárhelymegtartás, az átviteli sebesség, a HTTP-kérelem- és válaszidő-túllépés, az üzenetméretek és az egyéni összekötőkérelmek megnövekedett korlátozása. További információ: [Korlátok és konfiguráció az Azure Logic Apps.](logic-apps-limits-and-config.md)

> [!NOTE]
> Egyes Azure virtuális hálózatok magánvégpontokat[(Azure Private Link)](../private-link/private-link-overview.md)használnak az Azure PaaS-szolgáltatásokhoz való hozzáférés biztosításához, például az Azure Storage, az Azure Cosmos DB vagy az Azure SQL Database, a partnerszolgáltatások vagy az Azure-ban üzemeltetett ügyfélszolgálatok. Ha a logikai alkalmazások nak hozzáférésre van szükségük a magánhálózati végpontokat használó virtuális hálózatokhoz, létre kell hoznia, telepítenie kell és futtatnia kell ezeket a logikai alkalmazásokat egy ISE-n belül.

Ha létrehoz egy ISE-t, az Azure *beadja* vagy üzembe helyezi az ISE-t az Azure virtuális hálózatába. Ezután használhatja ezt az ISE-t a hozzáférést igénylő logikai alkalmazások és integrációs fiókok helyeként.

![Integrációs szolgáltatáskörnyezet kiválasztása](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

A logikai alkalmazások a virtuális hálózaton belüli vagy a virtuális hálózathoz kapcsolódó erőforrásokhoz férhetnek hozzá ezek az elemek használatával, amelyek ugyanabban az ISE-ben futnak, mint a logikai alkalmazások:

* **Core**címkével ellátott beépített eseményindító vagy művelet, például a HTTP-eseményindító vagy -művelet
* AZ ADOTT rendszer hez vagy szolgáltatáshoz egy **ISE**címkével ellátott csatlakozó
* Egyéni összekötő

Továbbra is használhatja **azokat** az összekötőket is, amelyek nem rendelkeznek core vagy **ISE** címkével az ISE logikai alkalmazásaival. Ezek az összekötők a több-bérlős Logic Apps szolgáltatás ban futnak helyette. További információt az alábbi szakaszokban talál:

* [Elszigetelt és több-bérlős](#difference)
* [Csatlakozás integrációs szolgáltatási környezetből](../connectors/apis-list.md#integration-service-environment)
* [ISE csatlakozók](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> A logikai alkalmazások, a beépített eseményindítók, a beépített műveletek és az ISE-ben futó összekötők olyan díjszabási tervet használnak, amely eltér a fogyasztásalapú díjszabási csomagtól. További információ: [Logic Apps pricing model](../logic-apps/logic-apps-pricing.md#fixed-pricing). Az árképzési részletekről a [Logic Apps díjszabása](../logic-apps/logic-apps-pricing.md).

Ez az áttekintés további információt ad arról, hogy az ISE hogyan biztosít a logikai alkalmazások közvetlen hozzáférést az Azure virtuális hálózatához, és összehasonlítja az ISE és a több-bérlős Logic Apps szolgáltatás közötti különbségeket.

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>Elszigetelt és több-bérlős

Amikor logikai alkalmazásokat hoz létre és futtat egy ISE-ben, ugyanazokat a felhasználói élményeket és hasonló képességeket kap, mint a több-bérlős Logic Apps szolgáltatás. Használhatja ugyanazokat a beépített eseményindítók, műveletek és felügyelt összekötők, amelyek a több-bérlős Logic Apps szolgáltatás érhető el. Egyes felügyelt összekötők további ISE-verziókat kínálnak. Az ISE-összekötők és a nem ISE-összekötők közötti különbség abban a helyen van, ahol futnak, és a Logic App Designerben lévő címkék között, amikor egy ISE-n belül dolgozik.

![Csatlakozók címkékkel vagy anélkül az ISE-ben](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* A beépített eseményindítók és műveletek a **CORE** címkét jelenítik meg. Mindig ugyanabban az ISE-ben futnak, mint a logikai alkalmazás. Az **ISE-címkét** megjelenítő felügyelt összekötők is ugyanabban az ISE-ben futnak, mint a logikai alkalmazás.

  Íme például néhány csatlakozó, amely ISE-verziókat kínál:

  * Azure Blob Storage, Fájltároló és Táblatároló
  * Az Azure-várólisták, az Azure Service Bus, az Azure Event Hubs és az IBM MQ
  * FTP és SFTP-SSH
  * SQL Server, Azure SQL Data Warehouse, Azure Cosmos DB
  * AS2, X12 és EDIFACT

* Felügyelt összekötők, amelyek nem jelenítenek meg további címkéket mindig futnak a több-bérlős Logic Apps szolgáltatás, de továbbra is használhatja ezeket az összekötőket egy ISE által üzemeltetett logikai alkalmazás.

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>Hozzáférés a helyszíni rendszerekhez

A helyszíni rendszerek vagy az Azure virtuális hálózathoz kapcsolódó adatforrások eléréséhez az ISE-ben lévő logikai alkalmazások használhatják ezeket az elemeket:

* HTTP-művelet

* ISE címkével ellátott csatlakozó az adott rendszerhez

  > [!NOTE]
  > Ha windowsos hitelesítést szeretne használni az SQL Server-összekötővel [integrációs szolgáltatási környezetben (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)használja az összekötő nem ISE-verzióját a [helyszíni adatátjáróval.](../logic-apps/logic-apps-gateway-install.md) Az ISE-címkével ellátott verzió nem támogatja a Windows-hitelesítést.

* Egyéni összekötők

  * Ha egyéni összekötők, amelyek megkövetelik a helyszíni adatátjáró, és ezeket az összekötőket egy ISE-n kívül, logikai alkalmazások egy ISE is használhatja ezeket az összekötőket.

  * Az ISE-ben létrehozott egyéni összekötők nem működnek a helyszíni adatátjáróval. Ezek az összekötők azonban közvetlenül hozzáférhetnek a helyszíni adatforrásokhoz, amelyek az ISE-t üzemeltető virtuális hálózathoz csatlakoznak. Így az ISE-ben lévő logikai alkalmazásoknak valószínűleg nincs szükségük az adatátjáróra, amikor ezekkel az erőforrásokkal kommunikálnak.

Olyan helyszíni rendszerek esetében, amelyek nem csatlakoznak virtuális hálózathoz, vagy nem rendelkeznek ISE-címkével ellátott összekötőkkel, először be kell [állítania a helyszíni adatátjárót,](../logic-apps/logic-apps-gateway-install.md) mielőtt a logikai alkalmazások csatlakozhatnának ezekhez a rendszerekhez.

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKUs

Az ISE létrehozásakor kiválaszthatja a fejlesztői termékváltozatot vagy a prémium termékváltozatot. A sulc-ok közötti különbségek a következők:

* **Fejlesztői**

  Alacsonyabb költségű ISE-t biztosít, amelyet kísérletezéshez, fejlesztéshez és teszteléshez használhat, de éles vagy teljesítményteszteléshez nem. A fejlesztői termékváltozat beépített eseményindítókat és műveleteket, standard összekötőket, vállalati összekötőket és egy [ingyenes csomagintegrációs](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) fiókot tartalmaz rögzített havi díjért. Ez a termékváltozat azonban nem tartalmaz szolgáltatásiszint-szerződést (SLA), kapacitás-növelése lehetőségeket vagy redundanciát az újrahasznosítás során, ami azt jelenti, hogy késéseket vagy állásidőt tapasztalhat.

* **Prémium**

  Olyan ISE-t biztosít, amely et éles környezetben is használhat, és tartalmazza az SLA-támogatást, a beépített eseményindítókat és műveleteket, a standard összekötőket, a vállalati összekötőket, az egyetlen [standard szintű](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrációs fiókot, a kapacitás bővítésének lehetőségeit és a redundanciát az újrahasznosítás során rögzített havi áron.

> [!IMPORTANT]
> A Termékváltozat beállítás csak az ISE létrehozásakor érhető el, és később nem módosítható.

Az árak, lásd: [Logic Apps árképzés.](https://azure.microsoft.com/pricing/details/logic-apps/) Ha meg szeretné tudni, hogyan működik az ise-k díjszabása és számlázása, olvassa el a [Logic Apps díjszabási modelljét.](../logic-apps/logic-apps-pricing.md#fixed-pricing)

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE-végpont-hozzáférés

Az ISE létrehozásakor belső vagy külső hozzáférési végpontokat is használhat. A kijelölés határozza meg, hogy a kérés vagy a webhook-eseményindítók az ISE logikai alkalmazásaiban fogadhatnak-e hívásokat a virtuális hálózaton kívülről.

Ezek a végpontok azt is befolyásolják, hogy a logikai alkalmazások futtatási előzményei hogyan férhetnek hozzá a bemenetekhez és kimenetekhez.

* **Belső**: Privát végpontok, amelyek lehetővé teszik az ISE logikai alkalmazásainak hívását, ahol a logikai alkalmazások bemeneteit és kimeneteit *csak a virtuális hálózaton belülről* tekintheti meg és érheti el.

* **Külső**: Nyilvános végpontok, amelyek lehetővé teszik az ISE logikai alkalmazásainak hívását, ahol megtekintheti és elérheti a logikai alkalmazások bemeneteit és kimeneteit *a virtuális hálózaton kívülről.* Ha hálózati biztonsági csoportokat (NSG-ket) használ, győződjön meg arról, hogy bejövő szabályokkal vannak beállítva, hogy lehetővé tegyék a hozzáférést a futtatási előzmények bemeneteihez és kimeneteihez. További információt az [ISE hozzáférés engedélyezése](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)című témakörben talál.

> [!IMPORTANT]
> A hozzáférési végpont beállítás csak az ISE létrehozásakor érhető el, és később nem módosítható.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrációs fiókok az ISE-vel

Az integrációs fiókok logikai alkalmazásokkal egy integrációs szolgáltatási környezetben (ISE) belül is használhatók. Ezeknek az integrációs fiókoknak azonban ugyanazt az *ISE-t* kell használniuk, mint a csatolt logikai alkalmazásoknak. Az ISE-ben lévő logikai alkalmazások csak azokra az integrációs fiókokra hivatkozhatnak, amelyek ugyanabban az ISE-ben találhatók. Integrációs fiók létrehozásakor kiválaszthatja az ISE-t az integrációs fiók helyeként. Ha meg szeretné tudni, hogyan működik az ISE-vel rendelkező integrációs fiókok díjszabása és számlázása, olvassa el a [Logic Apps díjszabási modelljét.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Az árak, lásd: [Logic Apps árképzés.](https://azure.microsoft.com/pricing/details/logic-apps/)

## <a name="next-steps"></a>További lépések

* [Csatlakozás az Azure-alapú virtuális hálózatokhoz az Azure Logic Apps alkalmazásból](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* További információ az [Azure virtuális hálózatról](../virtual-network/virtual-networks-overview.md)
* További információ [az Azure-szolgáltatások virtuális hálózati integrációjáról](../virtual-network/virtual-network-for-azure-services.md)
