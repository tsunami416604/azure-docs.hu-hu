---
title: 'VNet-VNet VPN Gateway-kapcsolatok konfigurálása: Azure Portal'
description: VPN Gateway-kapcsolatot hozhat létre virtuális hálózatok között a Resource Managerrel és az Azure Portallal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: cherylmc
ms.openlocfilehash: c8ed01af9574ce10c95fb4af61f1da1c72c858ad
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75860503"
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

## <a name="about-connecting-vnets"></a>A virtuális hálózatok csatlakoztatása

A következő szakaszok a virtuális hálózatok összekapcsolásának különböző módjait ismertetik.

### <a name="vnet-to-vnet"></a>Virtuális hálózatok közötti kapcsolat

A VNet – VNet kapcsolat konfigurálása egyszerű módszer a virtuális hálózatok csatlakoztatására. Ha egy VNet-VNet kapcsolati típussal (VNet2VNet) csatlakoztat egy virtuális hálózatot egy másik virtuális hálózathoz, az hasonló a helyek közötti IPsec-kapcsolat helyi helyhez való létrehozásához. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE protokollal való biztosításához, és ugyanúgy működik a kommunikáció során. Azonban eltérnek a helyi hálózati átjáró konfigurálásának módjától. 

VNet-VNet-kapcsolatok létrehozásakor a rendszer automatikusan létrehozza és feltölti a helyi hálózati átjáró címterület területét. Ha egy VNet frissíti a címtartományt, a másik VNet automatikusan átirányítja a frissített címtartományt. Általában gyorsabban és könnyebben hozható létre VNet-VNet kapcsolat, mint a helyek közötti kapcsolat.

### <a name="site-to-site-ipsec"></a>Helyek közötti kapcsolat (IPsec)

Ha bonyolult hálózati konfigurációval dolgozik, érdemes inkább [helyek közötti kapcsolattal](vpn-gateway-howto-site-to-site-resource-manager-portal.md) csatlakoztatni a virtuális hálózatok. A helyek közötti IPsec-lépések követése után manuálisan hozza létre és konfigurálja a helyi hálózati átjárókat. Az egyes virtuális hálózatok helyi hálózati átjárója helyi helyként kezeli a többi virtuális hálózatot. Ezek a lépések lehetővé teszik további címtartomány megadását a helyi hálózati átjáró számára a forgalom irányításához. Ha a VNet megváltozik, manuálisan kell frissítenie a megfelelő helyi hálózati átjárót.

### <a name="vnet-peering"></a>Társviszony-létesítés virtuális hálózatok között

A virtuális hálózatok a VNet-társítás használatával is összekapcsolhatók. A VNet-társítás nem használ VPN-átjárót, és eltérő korlátozásokkal rendelkezik. Emellett a [virtuális hálózatok közötti társviszony díjszabásának](https://azure.microsoft.com/pricing/details/virtual-network) kiszámítása máshogy történik, mint a [virtuális hálózatok közötti VPN-átjáró](https://azure.microsoft.com/pricing/details/vpn-gateway) esetén. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Miért érdemes VNet-VNet kapcsolatokat létrehozni?

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

**Értékek a TestVNet1-hez:**

- **Virtuális hálózati beállítások**
    - **Név**: adja meg a *TestVNet1*.
    - **Címterület**: írja be a *10.11.0.0/16*értéket.
    - **Előfizetés**: válassza ki a használni kívánt előfizetést.
    - **Erőforráscsoport**: adja meg a *TestRG1*.
    - Hely: válassza az **USA keleti** **régióját**.
    - **Alhálózat**
        - **Név**: írja be a *FrontEnd*nevet.
        - **Címtartomány**: írja be a *10.11.0.0/24*értéket.
    - **Átjáró-alhálózat**:
        - **Név**: a *GatewaySubnet* autofilled.
        - **Címtartomány**: írja be a *10.11.255.0/27*értéket.

- **Virtuális hálózati átjáró beállításai** 
    - **Név**: adja meg a *testvnet1gw elemre*.
    - **Átjáró típusa**: válassza ki a **VPN** elemet.
    - **VPN típusa**: válassza az **útvonal-alapú**lehetőséget.
    - **SKU**: válassza ki a használni kívánt ÁTJÁRÓ-SKU-t.
    - **Nyilvános IP-cím neve**: írja be a *TestVNet1GWIP*
    - **Kapcsolat** 
       - **Név**: adja meg a *TestVNet1toTestVNet4*.
       - **Megosztott kulcs**: adja meg a *abc123*. Saját maga is létrehozhatja a megosztott kulcsot. Amikor létrehozza a kapcsolatot a virtuális hálózatok között, az értékeknek egyezniük kell.

**Értékek a TestVNet4-hez:**

- **Virtuális hálózati beállítások**
   - **Név**: adja meg a *TestVNet4*.
   - **Címterület**: írja be a *10.41.0.0/16*értéket.
   - **Előfizetés**: válassza ki a használni kívánt előfizetést.
   - **Erőforráscsoport**: adja meg a *TestRG4*.
   - **Hely**: Válassza az **USA nyugati régiója** lehetőséget.
   - **Alhálózat** 
      - **Név**: írja be a *FrontEnd*nevet.
      - **Címtartomány**: írja be a *10.41.0.0/24*értéket.
   - **GatewaySubnet** 
      - **Név**: a *GatewaySubnet* autofilled.
      - **Címtartomány**: írja be a *10.41.255.0/27*értéket.

- **Virtuális hálózati átjáró beállításai** 
    - **Név**: adja meg a *TestVNet4GW*.
    - **Átjáró típusa**: válassza ki a **VPN** elemet.
    - **VPN típusa**: válassza az **útvonal-alapú**lehetőséget.
    - **SKU**: válassza ki a használni kívánt ÁTJÁRÓ-SKU-t.
    - **Nyilvános IP-cím neve**: adja meg a *TestVNet4GWIP*.
    - **Kapcsolat** 
       - **Név**: adja meg a *TestVNet4toTestVNet1*.
       - **Megosztott kulcs**: adja meg a *abc123*. Saját maga is létrehozhatja a megosztott kulcsot. Amikor létrehozza a kapcsolatot a virtuális hálózatok között, az értékeknek egyezniük kell.

## <a name="create-and-configure-testvnet1"></a>A TestVNet1 létrehozása és konfigurálása
Ha már rendelkezik egy virtuális hálózattal, győződjön meg arról, hogy a beállításai kompatibilisek a VPN-átjáró kialakításával. Különösen ügyeljen az esetleges olyan alhálózatokra, amelyek átfedésbe kerülhetnek más hálózatokkal. A kapcsolatok nem működnek megfelelően, ha átfedésben vannak az alhálózatok.

### <a name="to-create-a-virtual-network"></a>Virtuális hálózat létrehozása
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="add-additional-address-space-and-create-subnets"></a>További címterek hozzáadása és alhálózatok létrehozása
Miután létrehozta a virtuális hálózatot, további címtereket adhat hozzá és alhálózatokat hozhat létre.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="create-a-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása
Ebben a lépésben a virtuális hálózat virtuális hálózati átjáróját fogja létrehozni. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet a választott átjáró-termékváltozattól függően. Ha gyakorlatként hozza létre ezt a konfigurációt, tekintse meg a [példa beállításait](#example-settings).

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-testvnet4"></a>A TestVNet4 létrehozása és konfigurálása
A TestVNet1 konfigurálása után hozzon létre TestVNet4 az előző lépések megismétlésével, és cserélje le az értékeket a TestVNet4 értékekre. A TestVNet4 konfigurálása előtt nem kell megvárnia, amíg a TestVNet1 virtuális hálózati átjárója létre nem fejeződött. Ha a saját értékeit használja, ügyeljen arra, hogy a Címterület ne fedjék át azokat a virtuális hálózatok, amelyekhez csatlakozni szeretne.

## <a name="configure-the-testvnet1-gateway-connection"></a>A TestVNet1 átjárókapcsolat konfigurálása
Miután mind a TestVNet1, mind a TestVNet4 virtuális hálózati átjárójának létrehozása befejeződött, létrehozhatja a virtuális hálózati átjárókapcsolatokat. Ebben a szakaszban létrehozza a kapcsolatot a VNet1 felől a VNet4 felé. Ezek a lépések csak egyazon előfizetésben lévő virtuális hálózatokkal működnek. Ha a virtuális hálózatok különböző előfizetésekben vannak, a kapcsolódáshoz a [PowerShellt](vpn-gateway-vnet-vnet-rm-ps.md) kell használnia. Ha azonban a virtuális hálózatok ugyanabban az előfizetésben különböző erőforráscsoportok, akkor a portálon keresztül is csatlakozhat.

1. A Azure Portal válassza a **minden erőforrás**elemet, adja meg a *virtuális hálózati átjáró* kifejezést a keresőmezőbe, majd navigáljon a VNet tartozó virtuális hálózati átjáróhoz. Például: **TestVNet1GW**. Válassza ki a **virtuális hálózati átjáró** lap megnyitásához.

   ![Kapcsolatok lap](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "Kapcsolatok lap")
2. A **Beállítások**területen válassza a **kapcsolatok**lehetőséget, majd kattintson a **Hozzáadás** elemre a **kapcsolat hozzáadása** lap megnyitásához.

   ![Kapcsolatok hozzáadása](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "Kapcsolat hozzáadása")
3. A **kapcsolatok hozzáadása** lapon adja meg a kapcsolatok értékeit:

   - **Név**: adja meg a kapcsolatok nevét. Például: *TestVNet1toTestVNet4*.

   - **Kapcsolattípus**: válassza a **VNet-to-VNet** elemet a legördülő menüből.

   - **Első virtuális hálózati átjáró**: a program automatikusan kitölti ezt a mezőt, mert ezt a hálózatot a megadott virtuális hálózati átjáróról hozza létre.

   - **Második virtuális hálózati átjáró**: Ez a mező annak a VNet a virtuális hálózati átjárója, amelyhez kapcsolódni szeretne. A **virtuális hálózati átjáró kiválasztása** lap megnyitásához válassza a **másik virtuális hálózati átjáró kiválasztása** lehetőséget.

     - Tekintse át az oldalon felsorolt virtuális hálózati átjárók listáját. Csak azok a virtuális hálózati átjárók jelennek meg, amelyek az előfizetésében szerepelnek. Ha olyan virtuális hálózati átjáróhoz szeretne csatlakozni, amely nem szerepel az előfizetésben, használja a [PowerShellt](vpn-gateway-vnet-vnet-rm-ps.md).

     - Válassza ki azt a virtuális hálózati átjárót, amelyhez csatlakozni szeretne.

     - **Megosztott kulcs (PSK)** : ebben a mezőben adjon meg egy megosztott kulcsot a kapcsolatban. A kulcsot generálhatja, vagy saját maga is létrehozhatja. Helyek közötti kapcsolat esetén a használt kulcs ugyanaz, mint a helyszíni eszköz és a virtuális hálózati átjáró kapcsolata. A koncepció itt is hasonló, azzal a különbséggel, hogy a VPN-eszközhöz való csatlakozás helyett egy másik virtuális hálózati átjáróhoz csatlakozik.
    
4. A módosítások mentéséhez kattintson az **OK** gombra.

## <a name="configure-the-testvnet4-gateway-connection"></a>A TestVNet4 átjárókapcsolat konfigurálása
Ezután hozzon létre kapcsolatot a TestVNet4 felől a TestVNet1 felé. Keresse meg a TestVNet4 kapcsolattal társított virtuális hálózati átjárót a portálon. Kövesse az előző szakasz lépéseit, és cserélje le az értékeket a TestVNet4 felől a TestVNet1 felé mutató kapcsolat létrehozásához. Ügyeljen arra, hogy ugyanazt a megosztott kulcsot használja.

## <a name="verify-your-connections"></a>Kapcsolatok ellenőrzése

Keresse meg a virtuális hálózati átjárót a Azure Portal. A **virtuális hálózati átjáró** lapon válassza a **kapcsolatok** lehetőséget a virtuális hálózati átjáró **kapcsolatok** lapjának megtekintéséhez. A kapcsolat létrejötte után látni fogja, hogy az **állapot** értékei **sikeresek** és **kapcsolattal**változnak. Válasszon ki egy kapcsolódást az **Essentials** oldal megnyitásához, és tekintse meg a további információkat.

![Sikerült](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Sikeres")

Amikor az adatok áramlanak, az adatok és **az** **adatok ki**lesznek láthatók.

![Essentials](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Alapvető erőforrások")

## <a name="add-additional-connections"></a>További kapcsolatok hozzáadása

Ha további kapcsolatokat szeretne hozzáadni, keresse meg azt a virtuális hálózati átjárót, amelyről létre kívánja hozni a kapcsolatot, majd válassza a **kapcsolatok**lehetőséget. Létrehozhat egy másik virtuális hálózatok közötti kapcsolatot, vagy létrehozhat IPsec helyek közötti kapcsolatot egy helyszíni helyhez. Módosítsa a **Kapcsolat típusát**, hogy az megfeleljen a létrehozni kívánt kapcsolattípusnak. További kapcsolatok létrehozása előtt ellenőrizze, hogy a virtuális hálózat címterület nem fedi-e át a csatlakozáshoz használni kívánt címtartományt. A helyek közötti kapcsolat létrehozásának lépéseiért lásd: [Helyek közötti kapcsolat létrehozása](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="vnet-to-vnet-faq"></a>Virtuális hálózatok közötti kapcsolat – gyakori kérdések
A virtuális hálózatok közötti kapcsolatokról további információt a gyakori kérdésekben talál.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Következő lépések

További információ a virtuális hálózatban lévő erőforrásokra irányuló hálózati forgalom korlátozásáról: [hálózati biztonság](../virtual-network/security-overview.md).

Információ arról, hogyan irányítja az Azure az Azure, a helyszíni és az internetes erőforrások közötti forgalom útválasztását: [Virtuális hálózat forgalmának útválasztása](../virtual-network/virtual-networks-udr-overview.md).
