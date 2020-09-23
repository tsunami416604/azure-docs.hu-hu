---
title: 'VNet-VNet VPN Gateway-kapcsolatok konfigurálása: Azure Portal'
description: VPN Gateway-kapcsolatot hozhat létre virtuális hálózatok között a Resource Managerrel és az Azure Portallal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 12ab54e51b26c4b3d77f5518d4e4f44efe34affd
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983304"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>VNet-VNet VPN Gateway-kapcsolat konfigurálása a Azure Portal használatával

Ebből a cikkből megtudhatja, hogyan csatlakoztathatók a virtuális hálózatok (virtuális hálózatok) a VNet – VNet kapcsolattípus használatával. A virtuális hálózatok lehetnek különböző régiókban és különböző előfizetésekben. Ha különböző előfizetésekhez kapcsolódik a virtuális hálózatok, az előfizetéseket nem kell ugyanahhoz a Active Directory bérlőhöz társítania. 

![v2v ábra](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

A cikkben ismertetett lépések a Azure Resource Manager üzemi modellre vonatkoznak, és a Azure Portal használják. Ezt a konfigurációt egy másik központi telepítési eszközzel vagy modellel is létrehozhatja az alábbi cikkekben ismertetett beállítások használatával:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Különböző üzemi modellek összekapcsolása – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Különböző üzemi modellek összekapcsolása – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>Tudnivalók a virtuális hálózatok csatlakoztatásáról

A következő szakaszok a virtuális hálózatok összekapcsolásának különböző módjait ismertetik.

### <a name="vnet-to-vnet"></a>Virtuális hálózatok közötti kapcsolat

A VNet – VNet kapcsolat konfigurálása egyszerű módszer a virtuális hálózatok csatlakoztatására. Ha egy VNet-VNet kapcsolati típussal (VNet2VNet) csatlakoztat egy virtuális hálózatot egy másik virtuális hálózathoz, az hasonló a helyek közötti IPsec-kapcsolat helyi helyhez való létrehozásához. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE protokollal való biztosításához, és ugyanúgy működik a kommunikáció során. Azonban eltérnek a helyi hálózati átjáró konfigurálásának módjától. 

VNet-VNet-kapcsolatok létrehozásakor a rendszer automatikusan létrehozza és feltölti a helyi hálózati átjáró címterület területét. Ha egy VNet frissíti a címtartományt, a másik VNet automatikusan átirányítja a frissített címtartományt. Általában gyorsabban és könnyebben hozható létre VNet-VNet kapcsolat, mint a helyek közötti kapcsolat.

### <a name="site-to-site-ipsec"></a>Helyek közötti kapcsolat (IPsec)

Ha bonyolult hálózati konfigurációval dolgozik, érdemes inkább [helyek közötti kapcsolattal](vpn-gateway-howto-site-to-site-resource-manager-portal.md) csatlakoztatni a virtuális hálózatok. A helyek közötti IPsec-lépések követése után manuálisan hozza létre és konfigurálja a helyi hálózati átjárókat. Az egyes virtuális hálózatok helyi hálózati átjárója helyi helyként kezeli a többi virtuális hálózatot. Ezek a lépések lehetővé teszik további címtartomány megadását a helyi hálózati átjáró számára a forgalom irányításához. Ha a VNet megváltozik, manuálisan kell frissítenie a megfelelő helyi hálózati átjárót.

### <a name="vnet-peering"></a>Virtuális hálózatok közötti társviszony

A virtuális hálózatok a VNet-társítás használatával is összekapcsolhatók. A VNet-társítás nem használ VPN-átjárót, és eltérő korlátozásokkal rendelkezik. Emellett a [virtuális hálózatok közötti társviszony díjszabásának](https://azure.microsoft.com/pricing/details/virtual-network) kiszámítása máshogy történik, mint a [virtuális hálózatok közötti VPN-átjáró](https://azure.microsoft.com/pricing/details/vpn-gateway) esetén. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Mikor érdemes virtuális hálózatok közötti kapcsolatot létrehozni?

A következő okok miatt érdemes lehet virtuális hálózatokat csatlakoztatni egy VNet-VNet kapcsolat használatával:

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Georedundancia és földrajzi jelenlét több régióban

  * Biztonságos kapcsolaton keresztül is beállíthatja saját geo-replikálását vagy szinkronizálását az internetre irányuló végpontok használata nélkül.
  * Az Azure Traffic Manager és Azure Load Balancer segítségével több Azure-régióban is beállíthatja a kiválóan elérhető számítási feladatokat. Beállíthat például SQL Server always on rendelkezésre állási csoportokat több Azure-régióban is.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Regionális többrétegű alkalmazások elkülönítéssel vagy felügyeleti határokkal

  * Ugyanazon a régión belül beállíthatja a többrétegű alkalmazásokat több virtuális hálózattal, amelyek elkülönítési vagy felügyeleti követelmények miatt kapcsolódnak egymáshoz.

A virtuális hálózatok közötti kommunikáció kombinálható többhelyes konfigurációkkal. Ezekkel a konfigurációkkal olyan hálózati topológiákat hozhat létre, amelyek a létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolattal ötvözik, ahogy az alábbi ábrán is látható:

![Tudnivalók a kapcsolatokról](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Tudnivalók a kapcsolatokról")

Ez a cikk bemutatja, hogyan csatlakoztathatja a virtuális hálózatok a VNet – VNet kapcsolattípus használatával. Ha gyakorlatként követi ezeket a lépéseket, használhatja a következő példában szereplő beállításokat. A példában a virtuális hálózatok ugyanabban az előfizetésben, de különböző erőforráscsoportokban vannak. Ha a virtuális hálózatok különböző előfizetésekben találhatóak, nem hozhatja létre a kapcsolatot a portálon. Ehelyett használja a [PowerShellt](vpn-gateway-vnet-vnet-rm-ps.md) vagy a [CLI](vpn-gateway-howto-vnet-vnet-cli.md) -t. További információ a VNet-VNet kapcsolatokról: [VNet – VNet gyakori kérdések](#vnet-to-vnet-faq).

### <a name="example-settings"></a>Példabeállítások

**VNet1 értékei:**

- **Virtuális hálózati beállítások**
    - **Név**: VNet1
    - **Címterület**: 10.1.0.0/16
    - **Előfizetés**: válassza ki a használni kívánt előfizetést.
    - **Erőforráscsoport**: TestRG1
    - Hely: USA keleti **régiója**
    - **Alhálózat**
        - **Név**: előtér
        - **Címtartomány**: 10.1.0.0/24
    - **Átjáró-alhálózat**:
        - **Név**: a *GatewaySubnet* autofilled
        - **Címtartomány**: 10.1.255.0/27

- **Virtuális hálózati átjáró beállításai**
    - **Név**: VNet1GW
    - **Átjáró típusa**: válassza ki a **VPN** elemet.
    - **VPN típusa**: válassza az **útvonal-alapú**lehetőséget.
    - **SKU**: válassza ki a használni kívánt ÁTJÁRÓ-SKU-t.
    - **Nyilvános IP-cím neve**: VNet1GWpip
    - **Kapcsolat**
       - **Név**: VNet1toVNet4
       - **Megosztott kulcs**: saját maga is létrehozhatja a megosztott kulcsot. Amikor létrehozza a kapcsolatot a virtuális hálózatok között, az értékeknek egyezniük kell. Ehhez a gyakorlathoz használja a abc123.

**Vnet4 felé értékei:**

- **Virtuális hálózati beállítások**
   - **Név**: vnet4 felé
   - **Címterület**: 10.41.0.0/16
   - **Előfizetés**: válassza ki a használni kívánt előfizetést.
   - **Erőforráscsoport**: TestRG4
   - Hely: USA nyugati **régiója**
   - **Alhálózat** 
      - **Név**: előtér
      - **Címtartomány**: 10.41.0.0/24
   - **GatewaySubnet** 
      - **Név**: a *GatewaySubnet* autofilled
      - **Címtartomány**: 10.41.255.0/27

- **Virtuális hálózati átjáró beállításai** 
    - **Név**: VNet4GW
    - **Átjáró típusa**: válassza ki a **VPN** elemet.
    - **VPN típusa**: válassza az **útvonal-alapú**lehetőséget.
    - **SKU**: válassza ki a használni kívánt ÁTJÁRÓ-SKU-t.
    - **Nyilvános IP-cím neve**: VNet4GWpip
    - **Kapcsolat** 
       - **Név**: VNet4toVNet1
       - **Megosztott kulcs**: saját maga is létrehozhatja a megosztott kulcsot. Amikor létrehozza a kapcsolatot a virtuális hálózatok között, az értékeknek egyezniük kell. Ehhez a gyakorlathoz használja a abc123.

## <a name="create-and-configure-vnet1"></a>VNet1 létrehozása és konfigurálása
Ha már rendelkezik egy virtuális hálózattal, győződjön meg arról, hogy a beállításai kompatibilisek a VPN-átjáró kialakításával. Különösen ügyeljen az esetleges olyan alhálózatokra, amelyek átfedésbe kerülhetnek más hálózatokkal. A kapcsolatok nem működnek megfelelően, ha átfedésben vannak az alhálózatok.

### <a name="to-create-a-virtual-network"></a>Virtuális hálózat létrehozása
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vnet1-gateway"></a>Az VNet1-átjáró létrehozása
Ebben a lépésben a virtuális hálózat virtuális hálózati átjáróját fogja létrehozni. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet a választott átjáró-termékváltozattól függően. Ha gyakorlatként hozza létre ezt a konfigurációt, tekintse meg a [példa beállításait](#example-settings).

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-vnet4"></a>Vnet4 felé létrehozása és konfigurálása
A VNet1 konfigurálása után hozzon létre Vnet4 felé és a Vnet4 felé-átjárót az előző lépések megismétlésével, és cserélje le az értékeket a Vnet4 felé értékekre. A Vnet4 felé konfigurálása előtt nem kell megvárnia, amíg a VNet1 virtuális hálózati átjárója létre nem fejeződött. Ha a saját értékeit használja, ügyeljen arra, hogy a Címterület ne fedjék át azokat a virtuális hálózatok, amelyekhez csatlakozni szeretne.

## <a name="configure-the-vnet1-gateway-connection"></a>VNet1-átjárói kapcsolatok konfigurálása
Ha a VNet1 és a Vnet4 felé virtuális hálózati átjárói is befejeződtek, létrehozhatók a virtuális hálózati átjárók kapcsolatai. Ebben a szakaszban létrehozza a kapcsolatot a VNet1 felől a VNet4 felé. Ezek a lépések csak egyazon előfizetésben lévő virtuális hálózatokkal működnek. Ha a virtuális hálózatok különböző előfizetésekben vannak, a kapcsolódáshoz a [PowerShellt](vpn-gateway-vnet-vnet-rm-ps.md) kell használnia. Ha azonban a virtuális hálózatok ugyanabban az előfizetésben különböző erőforráscsoportok, akkor a portálon keresztül is csatlakozhat.

1. A Azure Portal válassza a **minden erőforrás**elemet, adja meg a *virtuális hálózati átjáró* kifejezést a keresőmezőbe, majd navigáljon a VNet tartozó virtuális hálózati átjáróhoz. Például: **VNet1GW**. Válassza ki az átjárót a **virtuális hálózati átjáró** lap megnyitásához. A **Beállítások**területen válassza a **kapcsolatok**lehetőséget.

   ![Kapcsolatok lap](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections.png "Kapcsolatok lap")
2. Válassza a **+ Hozzáadás** lehetőséget a **Kapcsolódás hozzáadása** lap megnyitásához.

   ![Kapcsolat hozzáadása](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet1-vnet4-connection.png "Kapcsolat hozzáadása")
3. A **kapcsolatok hozzáadása** lapon adja meg a kapcsolatok értékeit:

   - **Név**: adja meg a kapcsolatok nevét. Például: *VNet1toVNet4*.

   - **Kapcsolattípus**: válassza a **VNet-to-VNet** elemet a legördülő menüből.

   - **Első virtuális hálózati átjáró**: a program automatikusan kitölti ezt a mezőt, mert ezt a hálózatot a megadott virtuális hálózati átjáróról hozza létre.

   - **Második virtuális hálózati átjáró**: Ez a mező annak a VNet a virtuális hálózati átjárója, amelyhez kapcsolódni szeretne. A **virtuális hálózati átjáró kiválasztása** lap megnyitásához válassza a **másik virtuális hálózati átjáró kiválasztása** lehetőséget.

     - Tekintse át az oldalon felsorolt virtuális hálózati átjárók listáját. Csak azok a virtuális hálózati átjárók jelennek meg, amelyek az előfizetésében szerepelnek. Ha olyan virtuális hálózati átjáróhoz szeretne csatlakozni, amely nem szerepel az előfizetésben, használja a [PowerShellt](vpn-gateway-vnet-vnet-rm-ps.md).

     - Válassza ki azt a virtuális hálózati átjárót, amelyhez csatlakozni szeretne.

     - **Megosztott kulcs (PSK)**: ebben a mezőben adjon meg egy megosztott kulcsot a kapcsolatban. A kulcsot generálhatja, vagy saját maga is létrehozhatja. Helyek közötti kapcsolat esetén a használt kulcs ugyanaz, mint a helyszíni eszköz és a virtuális hálózati átjáró kapcsolata. A koncepció itt is hasonló, azzal a különbséggel, hogy a VPN-eszközhöz való csatlakozás helyett egy másik virtuális hálózati átjáróhoz csatlakozik.
    
4. A módosítások mentéséhez kattintson az **OK** gombra.

## <a name="configure-the-vnet4-gateway-connection"></a>Vnet4 felé-átjárói kapcsolatok konfigurálása
Ezután hozzon létre egy, a Vnet4 felé és a VNet1 közötti kapcsolatokat. Keresse meg a Vnet4 felé társított virtuális hálózati átjárót a portálon. Kövesse az előző szakasz lépéseit, és cserélje le az értékeket a Vnet4 felé és a VNet1 közötti kapcsolatok létrehozásához. Ügyeljen arra, hogy ugyanazt a megosztott kulcsot használja.

## <a name="verify-your-connections"></a>Kapcsolatok ellenőrzése

1. Keresse meg a virtuális hálózati átjárót a Azure Portal. 
2. A **virtuális hálózati átjáró** lapon válassza a **kapcsolatok** lehetőséget a virtuális hálózati átjáró **kapcsolatok** lapjának megtekintéséhez. A kapcsolat létrejötte után az **állapot** értékek a **Connected**értékre változnak.

   ![Kapcsolatok ellenőrzése](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/view-connections.png "Kapcsolatok ellenőrzése")
3. A **név** oszlopban válassza ki a kapcsolatok egyikét a további információk megtekintéséhez. Amikor az adatok áramlanak, az adatok és **az** **adatok ki**lesznek láthatók.

   ![A képernyőképen egy erőforráscsoport látható, amely az adatok és az adatok kimenő értékeit tartalmazza.](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/status.png "Állapot")

## <a name="add-additional-connections"></a>További kapcsolatok hozzáadása

Ha további kapcsolatokat szeretne hozzáadni, keresse meg azt a virtuális hálózati átjárót, amelyről létre kívánja hozni a kapcsolatot, majd válassza a **kapcsolatok**lehetőséget. Létrehozhat egy másik virtuális hálózatok közötti kapcsolatot, vagy létrehozhat IPsec helyek közötti kapcsolatot egy helyszíni helyhez. Módosítsa a **Kapcsolat típusát**, hogy az megfeleljen a létrehozni kívánt kapcsolattípusnak. További kapcsolatok létrehozása előtt ellenőrizze, hogy a virtuális hálózat címterület nem fedi-e át a csatlakozáshoz használni kívánt címtartományt. A helyek közötti kapcsolat létrehozásának lépéseiért lásd: [Helyek közötti kapcsolat létrehozása](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="vnet-to-vnet-faq"></a>Virtuális hálózatok közötti kapcsolat – gyakori kérdések
A virtuális hálózatok közötti kapcsolatokról további információt a gyakori kérdésekben talál.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Következő lépések

További információ a virtuális hálózatban lévő erőforrásokra irányuló hálózati forgalom korlátozásáról: [hálózati biztonság](../virtual-network/security-overview.md).

Információ arról, hogyan irányítja az Azure az Azure, a helyszíni és az internetes erőforrások közötti forgalom útválasztását: [Virtuális hálózat forgalmának útválasztása](../virtual-network/virtual-networks-udr-overview.md).
