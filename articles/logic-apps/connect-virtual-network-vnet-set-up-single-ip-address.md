---
title: Nyilvános kimenő IP-cím beállítása az ise-k számára
description: Megtudhatja, hogy miként állíthat be egyetlen nyilvános kimenő IP-címet az integrációs szolgáltatáskörnyezethez (ISEs) az Azure Logic Apps alkalmazásban
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 619c68b84291bc35b8216194ac4534393fde454c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191501"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Egyetlen IP-cím beállítása egy vagy több integrációs szolgáltatási környezethez az Azure Logic Apps alkalmazásban

Amikor az Azure Logic Apps-szel dolgozik, beállíthat egy [ *integrációs szolgáltatási környezetet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) olyan logikai alkalmazások üzemeltetéséhez, amelyeknek hozzáférést kell biztosítani az [Azure virtuális hálózat](../virtual-network/virtual-networks-overview.md)erőforrásaihoz. Ha több ISE-példányt, amelyek ip-korlátozásokkal rendelkező más végpontokhoz való hozzáférést igényelnek, telepítsen egy [Azure tűzfalat](../firewall/overview.md) vagy egy [hálózati virtuális berendezést](../virtual-network/virtual-networks-overview.md#filter-network-traffic) a virtuális hálózatba, és irányítsa a kimenő forgalmat a tűzfalon vagy a hálózati virtuális eszközön keresztül. Ezután a virtuális hálózat összes ISE-példánya egyetlen, nyilvános, statikus és kiszámítható IP-címet használhat a célrendszerekkel való kommunikációhoz. Így nem kell további tűzfal-nyílásokat beállítania ezeken a célrendszereken az egyes ISE-khez.

Ez a témakör bemutatja, hogyan irányíthatja a kimenő forgalmat egy Azure tűzfalon keresztül, de hasonló fogalmakat alkalmazhat egy hálózati virtuális berendezésre, például egy külső tűzfalra az Azure Marketplace-ről. Bár ez a témakör több ISE-példány beállítására összpontosít, ezt a megközelítést egyetlen ISE-hez is használhatja, ha a forgatókönyv ben korlátozni kell a hozzáférést igénylő IP-címek számát. Fontolja meg, hogy a tűzfal vagy a virtuális hálózati berendezés többletköltségei nek van-e értelme a forgatókönyvhöz. További információ az [Azure Firewall díjszabásáról.](https://azure.microsoft.com/pricing/details/azure-firewall/)

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-tűzfal, amely ugyanabban a virtuális hálózatban fut, mint az ISE. Ha nem rendelkezik tűzfallal, először adjon hozzá `AzureFirewallSubnet` egy [alhálózatot,](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) amely a virtuális hálózathoz van elnevezve. Ezután [létrehozhat és telepíthet tűzfalat](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) a virtuális hálózatban.

* Egy [Azure-útvonaltábla.](../virtual-network/manage-route-table.md) Ha még nem rendelkezik ilyensel, először [hozzon létre egy útvonaltáblát.](../virtual-network/manage-route-table.md#create-a-route-table) Az útválasztásról további információt a [Virtuális hálózati forgalom útválasztása](../virtual-network/virtual-networks-udr-overview.md)című témakörben talál.

## <a name="set-up-route-table"></a>Útvonaltábla beállítása

1. Az [Azure Portalon](https://portal.azure.com)válassza ki az útvonaltáblát, például:

   ![Válassza ki az útvonaltáblát a kimenő forgalom irányítására vonatkozó szabállyal](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Új [útvonal hozzáadásához](../virtual-network/manage-route-table.md#create-a-route)válassza az Útvonalak tábla menüjének **Útvonalak** > **hozzáadása parancsát.**

   ![Útvonal hozzáadása a kimenő forgalom irányításához](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. Az **Útvonal hozzáadása** ablaktáblán [állítsa be az új útvonalat](../virtual-network/manage-route-table.md#create-a-route) egy olyan szabállyal, amely meghatározza, hogy a célrendszerbe irányuló kimenő forgalom az alábbi viselkedést követi:

   * A [**virtuális berendezést**](../virtual-network/virtual-networks-udr-overview.md#user-defined) használja következő ugrástípusként.

   * A tűzfalpéldány privát IP-címére lép a következő ugrási címként.

     Az IP-cím megkereséséhez válassza a tűzfal **menüjének Áttekintés parancsát,** keresse meg a címet a **Privát IP-cím**csoportban, például:

     ![Tűzfal saját IP-címének keresése](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Íme egy példa, amely bemutatja, hogyan nézhet ki egy ilyen szabály:

   ![Szabály beállítása a kimenő forgalom irányításához](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Útvonal neve** | <*egyedi útvonal-név*> | Az útvonal egyedi neve az útvonaltáblában |
   | **Címelőtag** | <*cél-cím*> | A célrendszer címe, ahová a forgalmat el szeretné látni. Győződjön meg arról, hogy [osztály nélküli tartományok közötti útválasztás (CIDR) jelöléssel](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) használja ezt a címet. |
   | **Következő ugrási típus** | **Virtuális berendezés** | A kimenő forgalom által használt [ugrástípus](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) |
   | **A következő ugrás címe** | <*tűzfal-magán-IP-cím*> | A tűzfal privát IP-címe |
   |||

## <a name="set-up-network-rule"></a>Hálózati szabály beállítása

1. Az Azure Portalon keresse meg és válassza ki a tűzfalat. A tűzfal menü **Beállítások**területén válassza a **Szabályok**lehetőséget. A szabályok ablaktáblán válassza a **Hálózati szabálygyűjtemény** > **Hálózati szabálygyűjtemény hozzáadása**lehetőséget.

   ![Hálózati szabálygyűjtemény hozzáadása a tűzfalhoz](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. A gyűjteményben adjon hozzá egy szabályt, amely lehetővé teszi a forgalmat a célrendszerbe.

   Tegyük fel például, hogy van egy logikai alkalmazás, amely fut egy ISE, és meg kell kommunikálni egy SFTP-rendszerrel. A neve se nevű `LogicApp_ISE_SFTP_Outbound`hálózatiszabály-gyűjteményt hoz `ISE_SFTP_Outbound`létre, amely a . Ez a szabály lehetővé teszi a forgalmat az IP-cím minden alhálózat, ahol az ISE fut a virtuális hálózaton a cél SFTP-rendszer segítségével a tűzfal privát IP-címét.

   ![Hálózati szabály beállítása tűzfalhoz](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Hálózati szabály gyűjteményének tulajdonságai**

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Név** | <*hálózat-szabály-gyűjtemény-név*> | A hálózati szabálygyűjtemény neve |
   | **Prioritás** | <*prioritási szintű*> | A szabálygyűjtemény futtatásához használandó prioritási sorrend. További információ: [Mik az Azure Tűzfal fogalmak?](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts) |
   | **Művelet** | **Lehetővé teszi** | A szabályhoz végrehajtandó művelettípus |
   |||

   **Hálózati szabály tulajdonságai**

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Név** | <*hálózati szabály neve*> | A hálózati szabály neve |
   | **Protocol (Protokoll)** | <*kapcsolat-protokollok*> | A használandó csatlakozási protokollok. Ha például NSG-szabályokat használ, ne csak a **TCP-t,** mind az **UDP-t**válassza. **TCP** |
   | **Forráscímek** | <*ISE-alhálózati címek*> | Az alhálózati IP-címek, ahol az ISE fut, és ahonnan a logikai alkalmazásból származó forgalom származik |
   | **Célcímek** | <*cél-IP-cím*> | A célrendszer IP-címe, ahová a forgalmat szeretné |
   | **Célportok** | <*célportok*> | A célrendszer által a bejövő kommunikációhoz használt portok |
   |||

   A hálózati szabályokról az alábbi cikkekben talál további információt:

   * [Hálózatszabály konfigurálása](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Az Azure Firewall szabályfeldolgozási logikája](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Gyakori kérdések az Azure tűzfalról](../firewall/firewall-faq.md)
   * [Azure PowerShell: Új-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: hálózati tűzfal hálózati szabály](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>További lépések

* [Csatlakozás az Azure-alapú virtuális hálózatokhoz az Azure Logic Apps alkalmazásból](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)