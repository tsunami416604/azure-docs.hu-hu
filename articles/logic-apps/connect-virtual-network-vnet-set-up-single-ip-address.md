---
title: Több ISEs hozzáférésének beállítása – Azure Logic Apps
description: Több integrációs szolgáltatási környezet (ISEs) esetén egyetlen nyilvános kimenő IP-címet állíthat be a külső rendszerek eléréséhez Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: f35b6836012df7ac3879070f7a85fbfb21b456c0
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74549244"
---
# <a name="set-up-access-for-multiple-integration-service-environments-in-azure-logic-apps"></a>Több integrációs szolgáltatási környezethez való hozzáférés beállítása Azure Logic Apps

Ha Azure Logic Apps-nal dolgozik, beállíthat egy [ *integrációs szolgáltatási környezetet* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) olyan logikai alkalmazások üzemeltetéséhez, amelyek egy Azure-beli [virtuális hálózat](../virtual-network/virtual-networks-overview.md)erőforrásaihoz férnek hozzá. Ha több ISE-példánnyal rendelkezik, amelyek IP-korlátozásokkal rendelkező más végpontokhoz férnek hozzá, helyezzen üzembe egy [Azure Firewall](../firewall/overview.md) vagy egy [hálózati virtuális berendezést](../virtual-network/virtual-networks-overview.md#filter-network-traffic) a virtuális hálózatban, és irányítsa át a kimenő forgalmat a tűzfalon vagy a hálózati virtuális berendezésen keresztül. Ezután a virtuális hálózat összes ISE-példánya egyetlen, kiszámítható és nyilvános IP-címet használ a célszámítógépeken való kommunikációhoz. Így nem kell további tűzfal-megnyitásokat beállítania a célszámítógépeken az egyes ISE-rendszerek esetében. Ez a témakör bemutatja, hogyan irányíthatja a kimenő forgalmat egy Azure Firewallon keresztül, de hasonló fogalmakat alkalmazhat egy virtuális hálózati virtuális berendezésre, például egy külső gyártótól származó tűzfalra az Azure piactéren.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-tűzfal, amely ugyanabban a virtuális hálózaton fut, mint az ISE. Ha nem rendelkezik tűzfallal, először [adjon hozzá egy `AzureFirewallSubnet` nevű alhálózatot](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) a virtuális hálózathoz. Ezután [létrehozhatja és üzembe helyezheti a tűzfalat](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) a virtuális hálózaton.

* Egy Azure- [útválasztási táblázat](../virtual-network/manage-route-table.md). Ha még nem rendelkezik ilyennel, először [hozzon létre egy útválasztási táblázatot](../virtual-network/manage-route-table.md#create-a-route-table). További információ az útválasztásról: [virtuális hálózati forgalom útválasztása](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Útválasztási táblázat beállítása

1. A [Azure Portal](https://portal.azure.com)válassza ki az útválasztási táblázatot, például:

   ![Válassza ki az útválasztási tábla szabályt a kimenő forgalom irányításához](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. [Új útvonal hozzáadásához](../virtual-network/manage-route-table.md#create-a-route)az útválasztási táblázat menüben válassza az **útvonalak** > **Hozzáadás**lehetőséget.

   ![Útvonal hozzáadása a kimenő forgalom irányításához](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. Az **útvonal hozzáadása** panelen [állítsa be az új útvonalat](../virtual-network/manage-route-table.md#create-a-route) egy szabállyal, amely megadja, hogy a célként megadott rendszerre irányuló összes kimenő forgalom a következő viselkedést követi:

   * A következő ugrási típusként használja a [**virtuális készüléket**](../virtual-network/virtual-networks-udr-overview.md#user-defined) .

   * Ugrás a tűzfal magánhálózati IP-címére a következő ugrási címként.

     Az IP-cím megkereséséhez a tűzfal menüjében válassza az **Áttekintés**lehetőséget, keresse meg a címet a **magánhálózati IP-cím**területen, például:

     ![Tűzfal magánhálózati IP-címének keresése](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Az alábbi példa bemutatja, hogyan nézhet ki egy ilyen szabály:

   ![Szabály beállítása a kimenő forgalom irányításához](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Tulajdonság | Value (Díj) | Leírás |
   |----------|-------|-------------|
   | **Útvonal neve** | <*egyedi-útvonal-név*> | Az útvonal egyedi neve az útválasztási táblában |
   | **Címelőtag** | <*cél – címek*> | A célrendszer címe, ahová a forgalmat el szeretné hajtani. Ügyeljen arra, hogy ehhez a címnek az [osztály nélküli tartományok közötti útválasztási (CIDR) jelölést](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) használja. |
   | **Következő ugrási típus** | **Virtuális készülék** | A kimenő forgalom által használt [Ugrás típusa](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) |
   | **Következő ugrás címe** | <*tűzfal – magánhálózati-IP-cím*> | A tűzfal magánhálózati IP-címe |
   |||

## <a name="set-up-network-rule"></a>Hálózati szabály beállítása

1. A Azure Portal keresse meg és válassza ki a tűzfalat. A tűzfal menü **Beállítások**területén válassza a **szabályok**elemet. A szabályok ablaktáblán válassza a **hálózati szabálygyűjtemény** > **hálózati szabálygyűjtemény hozzáadása**elemet.

   ![Hálózati szabálygyűjtemény hozzáadása a tűzfalhoz](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. A gyűjteményben adjon hozzá egy szabályt, amely engedélyezi a forgalmat a célként megadott rendszer felé.

   Tegyük fel például, hogy rendelkezik egy olyan logikai alkalmazással, amely egy ISE-ben fut, és egy SFTP-rendszerrel kell kommunikálnia. Létre kell hoznia egy `LogicApp_ISE_SFTP_Outbound`nevű hálózati szabály-gyűjteményt, amely egy `ISE_SFTP_Outbound`nevű hálózati szabályt tartalmaz. Ez a szabály minden olyan alhálózat IP-címéről engedélyezi a forgalmat, ahol az ISE a virtuális hálózaton a cél SFTP rendszerre fut a tűzfal magánhálózati IP-címének használatával.

   ![Hálózati szabály beállítása a tűzfalhoz](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Hálózati szabályok gyűjteményének tulajdonságai**

   | Tulajdonság | Value (Díj) | Leírás |
   |----------|-------|-------------|
   | **Name (Név)** | <*Network-Rule-Collection-name*> | A hálózati szabály gyűjteményének neve |
   | **Priority (Prioritás)** | <*prioritási szintű*> | A szabály gyűjteményének futtatásához használandó prioritási sorrend. További információ: [Mi a Azure Firewall fogalmak](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)? |
   | **Művelet** | **Engedélyezés** | A szabályhoz végrehajtandó Művelettípus |
   |||

   **Hálózati szabály tulajdonságai**

   | Tulajdonság | Value (Díj) | Leírás |
   |----------|-------|-------------|
   | **Name (Név)** | <*Network-rule-name*> | A hálózati szabály neve |
   | **Protocol (Protokoll)** | <*kapcsolatok – protokollok*> | A használandó kapcsolódási protokollok. Ha például NSG-szabályokat használ, válassza a **TCP** és az **UDP**beállítást, nem csak a **TCP protokollt**. |
   | **Forrásoldali címek** | <*ISE – alhálózati címek*> | Az alhálózati IP-címek, amelyeken az ISE fut, és a logikai alkalmazásból származó forgalom |
   | **Cél címei** | <*cél-IP-cím*> | Annak a célhelynek az IP-címe, amelyen a forgalmat el szeretné jutni |
   | **Célport** | <*cél – portok*> | A célként megadott rendszer által a bejövő kommunikációhoz használt portok |
   |||

   A hálózati szabályokkal kapcsolatos további információkért tekintse meg a következő cikkeket:

   * [Hálózati szabály konfigurálása](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Azure Firewall szabály-feldolgozási logika](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Azure Firewall GYIK](../firewall/firewall-faq.md)
   * [Azure PowerShell: New-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: az Network Firewall Network-Rule](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Következő lépések

* [Kapcsolódás Azure-beli virtuális hálózatokhoz Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)