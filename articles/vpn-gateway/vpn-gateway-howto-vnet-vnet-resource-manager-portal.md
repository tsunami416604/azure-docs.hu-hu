---
title: 'VNet-VNet VPN Gateway-kapcsolatok konfigurálása: Azure Portal'
description: VPN Gateway-kapcsolatot hozhat létre virtuális hálózatok között a Resource Managerrel és az Azure Portallal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: cherylmc
ms.openlocfilehash: 3d91203253c08acdaa159fc70f7a34fa7fca20c8
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674160"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>A virtuális hálózatok közötti VPN gateway-kapcsolat konfigurálása az Azure portal használatával

Ez a cikk a VNet – VNet kapcsolat típusát a hogyan lehet csatlakoztatni virtuális hálózatok (Vnetek). Virtuális hálózatok lehetnek különböző régiókban, és a különböző előfizetésekből. Amikor különböző előfizetésekről csatlakoztat virtuális hálózatokat kapcsolódik, az előfizetéseket nem kell társítható ugyanahhoz az Active Directory-bérlőhöz. 

![v2v ábra](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

A jelen cikkben ismertetett lépések az Azure Resource Manager-alapú üzemi modellre vonatkoznak, és az Azure Portalon. Az alábbi cikkekben leírt beállítások használatával hozhat létre ezt a konfigurációt más üzembehelyezési eszközzel vagy modellt:

> [!div class="op_single_selector"]
> * [Azure Portalra](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Különböző üzemi modellek összekapcsolása – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Különböző üzemi modellek összekapcsolása – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>Tudnivalók a virtuális hálózatok csatlakoztatása

A következő szakaszok ismertetik a virtuális hálózatok összekapcsolása a különböző módjait.

### <a name="vnet-to-vnet"></a>Virtuális hálózatok közötti kapcsolat

Egy egyszerű módja, virtuális hálózatok összekapcsolása virtuális hálózatok közötti kapcsolat konfigurálása. Egy másik virtuális hálózat egy virtuális hálózat csatlakoztatása egy VNet – VNet kapcsolat típusú történő összekapcsolása (VNet2VNet), ha nagyon hasonlít egy helyek közötti IPsec-kapcsolat a helyszíni helyre. Két kapcsolattípus egy VPN-átjárót használ biztonságos alagút kialakításához IPsec/IKE-, és ugyanúgy működik a kommunikáció során. Azonban eltérnek a módon a helyi hálózati átjáró van konfigurálva. 

A VNet – VNet kapcsolat létrehozásakor a helyi hálózati átjáró címtér az automatikusan létrehozott és kitölti a rendszer. Ha egy virtuális hálózat a címtér frissíti, a másik virtuális hálózat automatikusan továbbítja a frissített címtér. Fontos általában gyorsabb és egyszerűbb, mint a helyek közötti kapcsolat a virtuális hálózatok közötti kapcsolat létrehozásához.

### <a name="site-to-site-ipsec"></a>Helyek közötti kapcsolat (IPsec)

Ha bonyolult hálózati konfigurációval dolgozik, érdemes inkább [helyek közötti kapcsolattal](vpn-gateway-howto-site-to-site-resource-manager-portal.md) csatlakoztatni a virtuális hálózatok. Ha a helyek közötti IPsec lépésekkel hozzon létre, és manuálisan konfigurálhatja a helyi hálózati átjárókat. Az egyes virtuális hálózatok helyi hálózati átjárója helyi helyként kezeli a többi virtuális hálózatot. Ezeket a lépéseket adja meg a helyi hálózati átjáróhoz irányítsa a forgalmat, további címtereket adhat. Ha egy virtuális hálózat címtere megváltozik, manuálisan frissítenie kell a megfelelő helyi hálózati átjárót.

### <a name="vnet-peering"></a>Társviszony létesítése virtuális hálózatok között

A virtuális hálózatokat is csatlakoztathat a virtuális hálózatok közötti társviszony-létesítés. Virtuális hálózatok közötti társviszony nem használ VPN-átjárót, és más korlátozásokkal rendelkezik. Emellett a [virtuális hálózatok közötti társviszony díjszabásának](https://azure.microsoft.com/pricing/details/virtual-network) kiszámítása máshogy történik, mint a [virtuális hálózatok közötti VPN-átjáró](https://azure.microsoft.com/pricing/details/vpn-gateway) esetén. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Miért érdemes létrehozni egy VNet – VNet kapcsolat?

Érdemes virtuális hálózatok összekapcsolása virtuális hálózatok közötti kapcsolat használatával a következő okok miatt:

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Georedundancia és földrajzi jelenlét több régióban

  * Beállíthat saját georeplikációját vagy szinkronizálási biztonságos kapcsolattal rendelkező kapcsolaton át, internetes végpontok nélkül.
  * Az Azure Traffic Manager és az Azure Load Balancer beállíthat magas rendelkezésre állású munkaterhelést georedundanciával több Azure-régiók között. Például akkor állíthat be az SQL Server Always On rendelkezésre állási csoportok több Azure-régiók között.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Regionális Többrétegű alkalmazások elkülönítéssel vagy felügyeleti határokat

  * Egy régión belül beállíthat többrétegű alkalmazásokat több, elkülönítéssel vagy felügyeleti követelményekkel miatt össze vannak kapcsolva virtuális hálózatokkal.

A virtuális hálózatok közötti kommunikáció kombinálható többhelyes konfigurációkkal. Ezen konfigurációk lehetővé teszi, hogy olyan hálózati topológiákat, amelyek létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal, az alábbi ábrán látható módon:

![Tudnivalók a kapcsolatokról](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Tudnivalók a kapcsolatokról")

Ez a cikk bemutatja, hogyan virtuális hálózatok összekapcsolása a VNet – VNet kapcsolat típus használatával. Ha gyakorlatként kövesse az alábbi lépéseket, használhatja a következő példa beállításértékeket. A példában a virtuális hálózatok ugyanabban az előfizetésben, de különböző erőforráscsoportokban vannak. Ha a virtuális hálózatok különböző előfizetésekben találhatóak, nem hozhatja létre a kapcsolatot a portálon. Ehelyett használja a [PowerShellt](vpn-gateway-vnet-vnet-rm-ps.md) vagy a [CLI](vpn-gateway-howto-vnet-vnet-cli.md) -t. További információ a VNet-VNet kapcsolatokról: [VNet – VNet gyakori kérdések](#vnet-to-vnet-faq).

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
       - **Megosztott kulcs**: saját maga is létrehozhatja a megosztott kulcsot. A virtuális hálózatok közötti kapcsolat létrehozásakor meg az értékeket meg kell egyeznie. Ehhez a gyakorlathoz használja a abc123.

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
       - **Megosztott kulcs**: saját maga is létrehozhatja a megosztott kulcsot. A virtuális hálózatok közötti kapcsolat létrehozásakor meg az értékeket meg kell egyeznie. Ehhez a gyakorlathoz használja a abc123.

## <a name="create-and-configure-vnet1"></a>VNet1 létrehozása és konfigurálása
Ha már rendelkezik egy virtuális hálózattal, győződjön meg arról, hogy a beállításai kompatibilisek a VPN-átjáró kialakításával. Különösen ügyeljen az esetleges olyan alhálózatokra, amelyek átfedésbe kerülhetnek más hálózatokkal. A kapcsolat nem fog megfelelően működni, ha egymást átfedő alhálózatok.

### <a name="to-create-a-virtual-network"></a>Virtuális hálózat létrehozása
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vnet1-gateway"></a>Az VNet1-átjáró létrehozása
Ebben a lépésben a virtuális hálózat virtuális hálózati átjáróját fogja létrehozni. Az átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró kiválasztott termékváltozatától függően. Ha gyakorlatként hozza létre ezt a konfigurációt, tekintse meg a [példa beállításait](#example-settings).

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-vnet4"></a>Vnet4 felé létrehozása és konfigurálása
A VNet1 konfigurálása után hozzon létre Vnet4 felé és a Vnet4 felé-átjárót az előző lépések megismétlésével, és cserélje le az értékeket a Vnet4 felé értékekre. A Vnet4 felé konfigurálása előtt nem kell megvárnia, amíg a VNet1 virtuális hálózati átjárója létre nem fejeződött. Ha a saját értékeit használja, ellenőrizze a címterek nincsenek átfedésben azokkal a virtuális hálózat, amelyhez szeretné csatlakoztatni.

## <a name="configure-the-vnet1-gateway-connection"></a>VNet1-átjárói kapcsolatok konfigurálása
Ha a VNet1 és a Vnet4 felé virtuális hálózati átjárói is befejeződtek, létrehozhatók a virtuális hálózati átjárók kapcsolatai. Ebben a szakaszban létrehozza a kapcsolatot a VNet1 felől a VNet4 felé. Ezek a lépések csak egyazon előfizetésben lévő virtuális hálózatokkal működnek. Ha a virtuális hálózatok különböző előfizetésekben vannak, a kapcsolódáshoz a [PowerShellt](vpn-gateway-vnet-vnet-rm-ps.md) kell használnia. Azonban ha a virtuális hálózatok eltérő erőforráscsoportokban ugyanabban az előfizetésben, csatlakoztathatja őket a portál használatával.

1. A Azure Portal válassza a **minden erőforrás**elemet, adja meg a *virtuális hálózati átjáró* kifejezést a keresőmezőbe, majd navigáljon a VNet tartozó virtuális hálózati átjáróhoz. Például: **VNet1GW**. Válassza ki az átjárót a **virtuális hálózati átjáró** lap megnyitásához. A **Beállítások**területen válassza a **kapcsolatok**lehetőséget.

   ![Kapcsolatok lap](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections.png "Kapcsolatok lap")
2. Válassza a **+ Hozzáadás** lehetőséget a **Kapcsolódás hozzáadása** lap megnyitásához.

   ![Kapcsolatok hozzáadása](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet1-vnet4-connection.png "kapcsolatok hozzáadása")
3. A **kapcsolatok hozzáadása** lapon adja meg a kapcsolatok értékeit:

   - **Név**: adja meg a kapcsolatok nevét. Például: *VNet1toVNet4*.

   - **Kapcsolattípus**: válassza a **VNet-to-VNet** elemet a legördülő menüből.

   - **Első virtuális hálózati átjáró**: a program automatikusan kitölti ezt a mezőt, mert ezt a hálózatot a megadott virtuális hálózati átjáróról hozza létre.

   - **Második virtuális hálózati átjáró**: Ez a mező annak a VNet a virtuális hálózati átjárója, amelyhez kapcsolódni szeretne. A **virtuális hálózati átjáró kiválasztása** lap megnyitásához válassza a **másik virtuális hálózati átjáró kiválasztása** lehetőséget.

     - Tekintse át az oldalon felsorolt virtuális hálózati átjárók listáját. Csak azok a virtuális hálózati átjárók jelennek meg, amelyek az előfizetésében szerepelnek. Ha olyan virtuális hálózati átjáróhoz szeretne csatlakozni, amely nem szerepel az előfizetésben, használja a [PowerShellt](vpn-gateway-vnet-vnet-rm-ps.md).

     - Válassza ki a virtuális hálózati átjárót, amelyhez szeretné csatlakoztatni.

     - **Megosztott kulcs (PSK)** : ebben a mezőben adjon meg egy megosztott kulcsot a kapcsolatban. A kulcsot generálhatja, vagy saját maga is létrehozhatja. Helyek közötti kapcsolaton keresztül a kulcs megegyezik a helyszíni eszközhöz és a virtuális hálózati átjáró kapcsolatának. A fogalom itt is hasonló, kivéve, hanem egy VPN-eszközre, a másik virtuális hálózati átjáró csatlakozik.
    
4. A módosítások mentéséhez kattintson **az OK gombra** .

## <a name="configure-the-vnet4-gateway-connection"></a>Vnet4 felé-átjárói kapcsolatok konfigurálása
Ezután hozzon létre egy, a Vnet4 felé és a VNet1 közötti kapcsolatokat. Keresse meg a Vnet4 felé társított virtuális hálózati átjárót a portálon. Kövesse az előző szakasz lépéseit, és cserélje le az értékeket a Vnet4 felé és a VNet1 közötti kapcsolatok létrehozásához. Ügyeljen arra, hogy ugyanazt a megosztott kulcsot használja.

## <a name="verify-your-connections"></a>Kapcsolatok ellenőrzése

1. Az Azure Portalon keresse meg a virtuális hálózati átjárót. 
2. A **virtuális hálózati átjáró** lapon válassza a **kapcsolatok** lehetőséget a virtuális hálózati átjáró **kapcsolatok** lapjának megtekintéséhez. A kapcsolat létrejötte után az **állapot** értékek a **Connected**értékre változnak.

   ![Kapcsolatok ellenőrzése](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/view-connections.png "Kapcsolatok ellenőrzése")
3. A **név** oszlopban válassza ki a kapcsolatok egyikét a további információk megtekintéséhez. Amikor az adatok áramlanak, az adatok és **az** **adatok ki**lesznek láthatók.

   ![Állapot](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/status.png "status")

## <a name="add-additional-connections"></a>További kapcsolatok hozzáadása

Ha további kapcsolatokat szeretne hozzáadni, keresse meg azt a virtuális hálózati átjárót, amelyről létre kívánja hozni a kapcsolatot, majd válassza a **kapcsolatok**lehetőséget. Létrehozhat egy másik virtuális hálózatok közötti kapcsolatot, vagy létrehozhat IPsec helyek közötti kapcsolatot egy helyszíni helyhez. Módosítsa a **Kapcsolat típusát**, hogy az megfeleljen a létrehozni kívánt kapcsolattípusnak. Mielőtt további kapcsolatokat hoz létre, győződjön meg arról, hogy a cím a virtuális hálózat nem átfedésben a címterekkel, amelyhez csatlakozni kíván. A helyek közötti kapcsolat létrehozásának lépéseiért lásd: [Helyek közötti kapcsolat létrehozása](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="vnet-to-vnet-faq"></a>Virtuális hálózatok közötti kapcsolat – gyakori kérdések
A virtuális hálózatok közötti kapcsolatokról további információt a gyakori kérdésekben talál.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>További lépések

További információ a virtuális hálózatban lévő erőforrásokra irányuló hálózati forgalom korlátozásáról: [hálózati biztonság](../virtual-network/security-overview.md).

Információ arról, hogyan irányítja az Azure az Azure, a helyszíni és az internetes erőforrások közötti forgalom útválasztását: [Virtuális hálózat forgalmának útválasztása](../virtual-network/virtual-networks-udr-overview.md).
