---
title: 'Virtuális hálózatés virtuális hálózat VPN-átjárókapcsolat konfigurálása: Azure Portal'
description: VPN Gateway-kapcsolatot hozhat létre virtuális hálózatok között a Resource Managerrel és az Azure Portallal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: cherylmc
ms.openlocfilehash: 3d91203253c08acdaa159fc70f7a34fa7fca20c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78674160"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Virtuális hálózat és virtuális hálózat vpn-átjárókapcsolat konfigurálása az Azure Portal használatával

Ez a cikk segít a virtuális hálózatok (VNets) csatlakoztatásában a virtuális hálózat és a virtuális hálózat kapcsolattípus használatával. A virtuális hálózatok különböző régiókban és különböző előfizetésekben lehetnek. Ha különböző előfizetésekvirtuális hálózatait csatlakoztatja, az előfizetéseket nem kell ugyanahhoz az Active Directory-bérlőhöz társítható. 

![v2v ábra](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

Ebben a cikkben a lépések az Azure Resource Manager telepítési modellre vonatkoznak, és az Azure Portal használatával. Ezt a konfigurációt egy másik központi telepítési eszközzel vagy modellel hozhatja létre az alábbi cikkekben ismertetett beállításokkal:

> [!div class="op_single_selector"]
> * [Azure-portál](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Powershell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Különböző üzemi modellek összekapcsolása – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Különböző üzemi modellek összekapcsolása – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>Tudnivalók a virtuális hálózatok csatlakoztatásáról

A következő szakaszok a virtuális hálózatok csatlakoztatásának különböző módjait ismertetik.

### <a name="vnet-to-vnet"></a>Virtuális hálózatok közötti kapcsolat

Virtuális hálózat és virtuális hálózat között kapcsolat konfigurálása egyszerű módja a virtuális hálózatok csatlakoztatásának. Ha virtuális hálózatot csatlakoztat egy virtuális hálózathoz virtuális hálózat-vnet kapcsolattípussal (VNet2VNet), az hasonló a helyek közötti IPsec-kapcsolat hoz létre egy helyszíni helyhez. Mindkét kapcsolattípus VPN-átjárót használ az IPsec/IKE biztonságos alagút biztosításához, és ugyanúgy működik a kommunikáció során. A helyi hálózati átjáró konfigurálásának módjában azonban különböznek. 

Virtuális hálózat és virtuális hálózat között lévő kapcsolat létrehozásakor a helyi hálózati átjáró címterülete automatikusan létrejön és kitöltődik. Ha frissíti az egyik virtuális hálózat címterületét, a másik virtuális hálózat automatikusan a frissített címtérbe irányítja. Általában gyorsabb és egyszerűbb virtuális hálózatés virtuális hálózat közötti kapcsolatot létrehozni, mint a helyek közötti kapcsolat.

### <a name="site-to-site-ipsec"></a>Helyek közötti kapcsolat (IPsec)

Ha bonyolult hálózati konfigurációval dolgozik, célszerű lehet a virtuális hálózatokat helyek közötti [kapcsolat](vpn-gateway-howto-site-to-site-resource-manager-portal.md) használatával csatlakoztatni. Ha követi a helyek közötti IPsec lépéseket, manuálisan hozza létre és konfigurálja a helyi hálózati átjárókat. Az egyes virtuális hálózatok helyi hálózati átjárója helyi helyként kezeli a többi virtuális hálózatot. Ezek a lépések lehetővé teszik, hogy további címtereket adjon meg a helyi hálózati átjáró számára a forgalom irányításához. Ha a virtuális hálózat címterülete megváltozik, manuálisan kell frissítenie a megfelelő helyi hálózati átjárót.

### <a name="vnet-peering"></a>Virtuális hálózatok közötti társviszony

A virtuális hálózatok virtuális hálózatok társtársviszony-létesítés használatával is csatlakoztatható. A virtuális hálózatok társviszony-létesítése nem használ VPN-átjárót, és különböző korlátozásokkal rendelkezik. Emellett a [virtuális hálózatok közötti társviszony díjszabásának](https://azure.microsoft.com/pricing/details/virtual-network) kiszámítása máshogy történik, mint a [virtuális hálózatok közötti VPN-átjáró](https://azure.microsoft.com/pricing/details/vpn-gateway) esetén. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Mikor érdemes virtuális hálózatok közötti kapcsolatot létrehozni?

A virtuális hálózatokat virtuális hálózatról virtuális hálózatra való kapcsolaton keresztül érdemes csatlakoztatni a következő okok miatt:

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Georedundancia és földrajzi jelenlét több régióban

  * Beállíthatja a saját georeplikáció vagy szinkronizálás biztonságos kapcsolat nélkül megy át az internetfelé néző végpontok.
  * Az Azure Traffic Manager és az Azure Load Balancer segítségével magas rendelkezésre állású számítási feladatokat állíthat be, amelyek több Azure-régióban is beállíthatók a georedundanciával. Beállíthatja például az SQL Server Always On rendelkezésre állási csoportokat több Azure-régióban.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Regionális többszintű alkalmazások elkülönítéssel vagy közigazgatási határokkal

  * Ugyanabban a régióban többrétegű alkalmazásokat állíthat be több virtuális hálózattal, amelyek az elkülönítés vagy a felügyeleti követelmények miatt kapcsolódnak egymáshoz.

A virtuális hálózatok közötti kommunikáció kombinálható többhelyes konfigurációkkal. Ezek a konfigurációk lehetővé teszik olyan hálózati topológiák létrehozását, amelyek a létesítmények közötti kapcsolatot összekapcsolják a virtuális hálózatok közötti kapcsolattal, ahogy az az alábbi ábrán látható:

![Tudnivalók a kapcsolatokról](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Tudnivalók a kapcsolatokról")

Ez a cikk bemutatja, hogyan lehet virtuális hálózatok at a virtuális hálózat és a virtuális hálózat kapcsolat típusával. Ha ezeket a lépéseket gyakorlatként követi, a következő példabeállítási értékeket használhatja. A példában a virtuális hálózatok ugyanabban az előfizetésben, de különböző erőforráscsoportokban vannak. Ha a virtuális hálózatok különböző előfizetésekben találhatóak, nem hozhatja létre a kapcsolatot a portálon. Használja a [PowerShellvagy](vpn-gateway-vnet-vnet-rm-ps.md) [a CLI](vpn-gateway-howto-vnet-vnet-cli.md) helyett. A virtuális hálózatok és a virtuális hálózatok között létesített kapcsolatokról a [VNet-vNet](#vnet-to-vnet-faq)– gyakori kérdések című témakörben talál további információt.

### <a name="example-settings"></a>Példabeállítások

**A VNet1 értékei:**

- **Virtuális hálózati beállítások**
    - **Név**: VNet1
    - **Címtér**: 10.1.0.0/16
    - **Előfizetés**: Válassza ki a használni kívánt előfizetést.
    - **Erőforrás-csoport**: TestRG1
    - **Helyszín**: USA keleti része
    - **Alhálózat**
        - **Név**: Előtér
        - **Címtartomány**: 10.1.0.0/24
    - **Átjáró alhálózata**:
        - **Név**: *A GatewaySubnet* automatikusan kitöltődik
        - **Címtartomány**: 10.1.255.0/27

- **Virtuális hálózati átjáró beállításai**
    - **Név**: VNet1GW
    - **Átjáró típusa**: válassza ki a **VPN** elemet.
    - **VPN-típus**: Válassza **az Útvonal alapú**lehetőséget.
    - **Termékváltozat:** Válassza ki a használni kívánt átjáró termékváltozatot.
    - **Nyilvános IP-cím neve**: VNet1GWpip
    - **Kapcsolat**
       - **Név**: VNet1toVNet4
       - **Megosztott kulcs:** A megosztott kulcsot saját kezűleg is létrehozhatja. Amikor létrehozza a kapcsolatot a virtuális hálózatok között, az értékeknek meg kell egyezniük. Ehhez a gyakorlathoz használja az abc123-at.

**A VNet4 értékei:**

- **Virtuális hálózati beállítások**
   - **Név**: VNet4
   - **Címterület**: 10.41.0.0/16
   - **Előfizetés**: Válassza ki a használni kívánt előfizetést.
   - **Erőforrás-csoport**: TestRG4
   - **Helyszín**: USA nyugati
   - **Alhálózat** 
      - **Név**: Előtér
      - **Címtartomány**: 10.41.0.0/24
   - **GatewaySubnet** 
      - **Név**: *A GatewaySubnet* automatikusan kitöltődik
      - **Címtartomány**: 10.41.255.0/27

- **Virtuális hálózati átjáró beállításai** 
    - **Név**: VNet4GW
    - **Átjáró típusa**: válassza ki a **VPN** elemet.
    - **VPN-típus**: Válassza **az Útvonal alapú**lehetőséget.
    - **Termékváltozat:** Válassza ki a használni kívánt átjáró termékváltozatot.
    - **Nyilvános IP-cím neve**: VNet4GWpip
    - **Kapcsolat** 
       - **Név**: VNet4toVNet1
       - **Megosztott kulcs:** A megosztott kulcsot saját kezűleg is létrehozhatja. Amikor létrehozza a kapcsolatot a virtuális hálózatok között, az értékeknek meg kell egyezniük. Ehhez a gyakorlathoz használja az abc123-at.

## <a name="create-and-configure-vnet1"></a>Virtuális hálózat létrehozása és konfigurálása1
Ha már rendelkezik egy virtuális hálózattal, győződjön meg arról, hogy a beállításai kompatibilisek a VPN-átjáró kialakításával. Különösen ügyeljen az esetleges olyan alhálózatokra, amelyek átfedésbe kerülhetnek más hálózatokkal. A kapcsolat nem fog megfelelően működni, ha egymást átfedő alhálózatokkal rendelkezik.

### <a name="to-create-a-virtual-network"></a>Virtuális hálózat létrehozása
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vnet1-gateway"></a>A VNet1 átjáró létrehozása
Ebben a lépésben a virtuális hálózat virtuális hálózati átjáróját fogja létrehozni. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet a választott átjáró-termékváltozattól függően. Ha ezt a konfigurációt gyakorlatként hozza létre, olvassa el a [Példa beállítások című témakört.](#example-settings)

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-vnet4"></a>Virtuális hálózat létrehozása és konfigurálása4
A Virtuálishálózat1 konfigurálása után hozza létre a VNet4 és a VNet4 átjárót az előző lépések megismétlésével és az értékek Virtuálishálózat4-értékekre való lecserélésével. A Virtuálishálózat4 konfigurálása előtt nem kell megvárnia, amíg a Virtuálishálózat1 virtuális hálózati átjárója befejeződik. Ha saját értékeket használ, győződjön meg arról, hogy a címterek nem fedik át azokat a virtuális hálózatokat, amelyekhez csatlakozni szeretne.

## <a name="configure-the-vnet1-gateway-connection"></a>A VNet1 átjárókapcsolat konfigurálása
Ha a virtuális hálózati átjárók mind a VNet1, mind a VNet4 befejeződött, létrehozhatja a virtuális hálózati átjáró kapcsolatokat. Ebben a szakaszban létrehozza a kapcsolatot a VNet1 felől a VNet4 felé. Ezek a lépések csak egyazon előfizetésben lévő virtuális hálózatokkal működnek. Ha a virtuális hálózatok különböző előfizetések, a Kapcsolat hoz létre a [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) t. Azonban ha a virtuális hálózatok különböző erőforráscsoportok ugyanabban az előfizetésben, csatlakoztathatja őket a portál használatával.

1. Az Azure Portalon válassza a **Minden erőforrás**lehetőséget, írja be a virtuális *hálózati átjárót* a keresőmezőbe, majd keresse meg a virtuális hálózat virtuális hálózati átjáróját. Például **VNet1GW**. Válassza ki az átjárót a **Virtuális hálózati átjáró** lap megnyitásához. A **Beállítások csoportban**válassza a **Kapcsolatok**lehetőséget.

   ![Kapcsolatok lap](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections.png "Kapcsolatok lap")
2. A **+Hozzáadás** gombot a **Kapcsolat hozzáadása** lap megnyitásához válassza.

   ![Kapcsolat hozzáadása](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet1-vnet4-connection.png "Kapcsolat hozzáadása")
3. A **Kapcsolat hozzáadása** lapon adja meg a kapcsolat értékeit:

   - **Név**: Adja meg a kapcsolat nevét. Például *vnet1toVNet4*.

   - **Kapcsolat típusa**: Válassza **a virtuális hálózat-virtuális hálózat** lehetőséget a legördülő menüből.

   - **Első virtuális hálózati átjáró**: Ez a mezőérték automatikusan kitöltődik, mert ezt a kapcsolatot a megadott virtuális hálózati átjáróból hozza létre.

   - **Második virtuális hálózati átjáró**: Ez a mező annak a virtuális hálózatnak a virtuális hálózati átjárója, amelyhez kapcsolatot kíván létrehozni. Válassza **a Másik virtuális hálózati átjáró kiválasztása lehetőséget** a Virtuális hálózati átjáró **kiválasztása** lap megnyitásához.

     - Tekintse át az oldalon felsorolt virtuális hálózati átjárók listáját. Csak azok a virtuális hálózati átjárók jelennek meg, amelyek az előfizetésében szerepelnek. Ha olyan virtuális hálózati átjáróhoz szeretne csatlakozni, amely nem az előfizetésében található, használja a [PowerShellt.](vpn-gateway-vnet-vnet-rm-ps.md)

     - Válassza ki azt a virtuális hálózati átjárót, amelyhez csatlakozni szeretne.

     - **Megosztott kulcs (PSK):** Ebben a mezőben adja meg a kapcsolat megosztott kulcsát. A kulcsot generálhatja, vagy saját maga is létrehozhatja. A helyek közötti kapcsolatokban a használt kulcs megegyezik a helyszíni eszközés a virtuális hálózati átjáró kapcsolat. A koncepció itt hasonló, kivéve, hogy ahelyett, hogy egy VPN-eszközhöz csatlakozna, egy másik virtuális hálózati átjáróhoz csatlakozik.
    
4. A módosítások mentéséhez kattintson az **OK** gombra.

## <a name="configure-the-vnet4-gateway-connection"></a>A VNet4 átjárókapcsolat konfigurálása
Ezután hozzon létre egy kapcsolatot a VNet4 és a VNet1 között. A portálon keresse meg a virtuális hálózati átjáró a Virtuálishálózat4 társított. Kövesse az előző szakasz lépéseit, és cserélje le az értékeket a virtuális hálózatról a virtuális hálózatra1 kapcsolat létrehozásához. Ügyeljen arra, hogy ugyanazt a megosztott kulcsot használja.

## <a name="verify-your-connections"></a>Kapcsolatok ellenőrzése

1. Keresse meg a virtuális hálózati átjáró az Azure Portalon. 
2. A **Virtuális hálózati átjáró** lapon válassza a **Kapcsolatok** lehetőséget a virtuális hálózati átjáró **Kapcsolatok** lapjának megtekintéséhez. A kapcsolat létrejötte után az **Állapotértékek** Kapcsolat értékére **változnak.**

   ![Kapcsolatok ellenőrzése](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/view-connections.png "Kapcsolatok ellenőrzése")
3. A **Név** oszlopban válassza ki az egyik kapcsolatot a további információk megtekintéséhez. Amikor az adatok elkezdik folyni, látni fogja az **adatok be-** és **adatkitöltésének értékeit.**

   ![Állapot](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/status.png "status")

## <a name="add-additional-connections"></a>További kapcsolatok hozzáadása

Ha további kapcsolatokat szeretne hozzáadni, keresse meg azt a virtuális hálózati átjárót, amelyről létre szeretné hozni a kapcsolatot, válassza a **Kapcsolatok lehetőséget.** Létrehozhat egy másik virtuális hálózatok közötti kapcsolatot, vagy létrehozhat IPsec helyek közötti kapcsolatot egy helyszíni helyhez. Módosítsa a **Kapcsolat típusát**, hogy az megfeleljen a létrehozni kívánt kapcsolattípusnak. Mielőtt további kapcsolatokat hozna létre, ellenőrizze, hogy a virtuális hálózat címterülete nem fedi-e át azokat a címtereket, amelyekhez csatlakozni szeretne. A helyek közötti kapcsolat létrehozásának lépéseiért lásd: [Helyek közötti kapcsolat létrehozása](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="vnet-to-vnet-faq"></a>Virtuális hálózatok közötti kapcsolat – gyakori kérdések
A virtuális hálózatok közötti kapcsolatokról további információt a gyakori kérdésekben talál.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>További lépések

Arról, hogy miként korlátozhatja a hálózati forgalmat a virtuális hálózat erőforrásaira, olvassa el a Hálózatbiztonság című [témakört.](../virtual-network/security-overview.md)

Információ arról, hogyan irányítja az Azure az Azure, a helyszíni és az internetes erőforrások közötti forgalom útválasztását: [Virtuális hálózat forgalmának útválasztása](../virtual-network/virtual-networks-udr-overview.md).
