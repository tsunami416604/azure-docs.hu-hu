---
title: 'Klasszikus virtuális hálózatok csatlakoztatása az Azure Resource Manager virtuális hálózatokhoz: Portál | Microsoft dokumentumok'
description: A klasszikus virtuális hálózatok és az erőforrás-kezelő virtuális hálózatai hoz való csatlakozás lépései a VPN-átjáró és a portál használatával
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: cherylmc
ms.openlocfilehash: 5e64cb2db2bd16a881334779a1c6f1ef19296da2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152023"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Virtuális hálózatok csatlakoztatása különböző telepítési modellekből a portál használatával

Ez a cikk bemutatja, hogyan csatlakoztathat klasszikus virtuális hálózatokat az Erőforrás-kezelő virtuális hálózataihoz, hogy a különálló központi telepítési modellekben található erőforrások kommunikálhassanak egymással. Ebben a cikkben a lépések elsősorban az Azure Portalon, de a PowerShell használatával is létrehozhatja ezt a konfigurációt a cikk kiválasztásával ebből a listából.

> [!div class="op_single_selector"]
> * [Portál](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Powershell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Klasszikus virtuális hálózat csatlakoztatása egy erőforrás-kezelő virtuális hálózathoz hasonló a virtuális hálózat helyszíni helyhez való csatlakoztatásához. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához. Létrehozhat egy kapcsolatot a különböző előfizetésekben és különböző régiókban lévő virtuális hálózatok között. A helyszíni hálózatokkal már rendelkező virtuális hálózatokat is csatlakoztathatja, feltéve, hogy az átjáró, amelyhez konfigurálva vannak, dinamikus vagy útvonalalapú. A virtuális hálózatok közötti kapcsolatokról további információt a cikk végén, a [Virtuális hálózatok közötti kapcsolat – gyakori kérdések](#faq) című részben talál. 

Ha még nem rendelkezik virtuális hálózati átjáróval, és nem szeretne létrehozni egyet, érdemes lehet inkább a virtuális hálózatok virtuális hálózatok használatával való csatlakoztatását. A virtuális hálózatok közötti társviszony nem használ VPN-átjárót. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

### <a name="before-you-begin"></a><a name="before"></a>Mielőtt elkezdené



* Ezek a lépések feltételezik, hogy mindkét virtuális hálózat már létrejött. Ha ezt a cikket gyakorlatként használja, és nem rendelkezik virtuális hálózatokkal, a lépésekben található hivatkozások segítenek azok létrehozásában.
* Ellenőrizze, hogy a virtuális hálózatok címtartományai nem fedik-e egymást, vagy átfedésben vannak-e más kapcsolatok bármely tartományával, amelyhez az átjárók csatlakoztathatók.
* Telepítse a legújabb PowerShell-parancsmagokat az Erőforrás-kezelőhöz és a Service Managementhez (klasszikus). Ebben a cikkben az Azure Portalt és a PowerShellt is használjuk. PowerShell van szükség a kapcsolatot a klasszikus virtuális hálózat és az erőforrás-kezelő virtuális hálózat létrehozása. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál. 

### <a name="example-settings"></a><a name="values"></a>Példabeállítások

Ezekkel az értékekkel létrehozhat egy tesztkörnyezetet, vagy a segítségükkel értelmezheti a cikkben szereplő példákat.

**Klasszikus virtuális hálózat**

Virtuális hálózat neve = ClassicVNet <br>
Címterület = 10.0.0.0/24 <br>
Alhálózat neve = Alhálózat-1 <br>
Alhálózati címtartomány = 10.0.0.0/27 <br>
Előfizetés = a használni kívánt előfizetés <br>
Erőforráscsoport = ClassicRG <br>
Helyszín = USA nyugati telephelye <br>
GatewaySubnet = 10.0.0.32/28 <br>
Helyi hely = RMVNetLocal <br>

**Erőforrás-kezelő virtuális hálózata**

VNet-név = RMVNet <br>
Címterület = 192.168.0.0/16 <br>
Erőforráscsoport = RG1 <br>
Helyszín = USA keleti része <br>
Alhálózat neve = Alhálózat-1 <br>
Címtartomány = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Virtuális hálózati átjáró neve = RMGateway <br>
Átjáró típusa = VPN <br>
VPN-típus = Útvonalalapú <br>
SKU = VpnGw1 <br>
Helyszín = USA keleti része <br>
Virtuális hálózat = RMVNet <br> (a VPN-átjáró társítása ehhez a virtuális hálózathoz) Első IP konfiguráció = rmgwpip <br> (átjáró nyilvános IP-címe) Helyi hálózati átjáró = ClassicVNetLocal <br>
Kapcsolat neve = RMtoClassic

### <a name="connection-overview"></a><a name="connectoverview"></a>Kapcsolat – áttekintés

Ehhez a konfigurációhoz vpn-átjárókapcsolatot hoz létre a virtuális hálózatok közötti IPsec/IKE VPN-alagúton keresztül. Győződjön meg arról, hogy a virtuális hálózat tartományok átfedésben vannak egymással, vagy a helyi hálózatok, amelyek hez csatlakoznak.

Az alábbi táblázat egy példát mutat be a példa virtuális hálózatok és a helyi helyek definiálására:

| Virtual Network | Címtartomány | Régió | Csatlakozás a helyi hálózati helyhez |
|:--- |:--- |:--- |:--- |
| KlasszikusVNet |(10.0.0.0/24) |USA nyugati régiója | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |USA keleti régiója |ClassicVNetLocal (10.0.0.0/24) |

## <a name="section-1---configure-the-classic-vnet-settings"></a><a name="classicvnet"></a>1. szakasz - A klasszikus virtuális hálózat beállításainak konfigurálása

Ebben a szakaszban hozza létre a klasszikus virtuális hálózat, a helyi hálózat (helyi hely) és a virtuális hálózati átjáró. A képernyőképek csak példaként szolgálnak. Ügyeljen arra, hogy cserélje le az értékeket a saját, vagy használja a [példa](#values) értékeket.

### <a name="1-create-a-classic-vnet"></a>1. <a name="classicvnet"> </a>Hozzon létre egy klasszikus virtuális hálózat

Ha nem rendelkezik klasszikus virtuális hálózattal, és ezeket a lépéseket edzésként futtatja, létrehozhat egy virtuális hálózatot [ezzel a cikkvel](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) és a [példa](#values) beállítási értékeivel felülről.

Ha már rendelkezik egy VPN-átjáróval rendelkező virtuális hálózattal, ellenőrizze, hogy az átjáró dinamikus-e. Ha statikus, először törölnie kell a VPN-átjárót, mielőtt továbblépne [a Helyi hely konfigurálása](#local)című területre.

1. Nyissa meg az [Azure Portalt](https://ms.portal.azure.com), majd jelentkezzen be az Azure-fiókjával.
2. Kattintson **a + Erőforrás létrehozása elemre** az "Új" oldal megnyitásához.
3. A "Keresés a piacon" mezőbe írja be a "Virtuális hálózat" kifejezést. Ha ehelyett válassza a Hálózat -> virtuális hálózat lehetőséget, nem kapja meg a klasszikus virtuális hálózat létrehozásának lehetőségét.
4. Keresse meg a "Virtuális hálózat" a visszaadott listát, és kattintson rá, hogy nyissa meg a Virtuális hálózat lapot. 
5. A virtuális hálózati lapon válassza a "Klasszikus" klasszikus virtuális hálózat létrehozásához. Ha az alapértelmezett itt, akkor a végén egy Erőforrás-kezelő virtuális hálózat helyett.

### <a name="2-configure-the-local-site"></a>2. <a name="local"> </a>A helyi hely konfigurálása

1. Keresse meg a **Minden erőforrást,** és keresse meg a **ClassicVNet-et** a listában.
2. Kattintson az **Átjáró** parancsra a menü **Beállítások** szakaszában, majd kattintson a szalagcímre az átjáró létrehozásához.
  ![VPN-átjáró konfigurálása](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "VPN-átjáró konfigurálása")
3. Az **Új VPN-kapcsolat** lap **Kapcsolat típusa csoportjában**válassza **a Helyek**közötti lehetőséget .
4. A **Helyi hely csoportban**kattintson **a Szükséges beállítások megadása gombra.** Ez megnyitja a **Helyi webhely** lapot.
5. A **Helyi hely** lapon hozzon létre egy nevet, amely az Erőforrás-kezelő virtuális hálózatára hivatkozik. Például "RMVNetLocal".
6. Ha az erőforrás-kezelő virtuális hálózatának VPN-átjárója már rendelkezik nyilvános IP-címmel, használja a **VPN-átjáró IP-cím** mezőjének értékét. Ha ezeket a lépéseket gyakorlatként végzi, vagy még nem rendelkezik virtuális hálózati átjáróval az erőforrás-kezelő virtuális hálózatához, létrehozhat egy helyőrző IP-címet. Győződjön meg arról, hogy a helyőrző IP-cím érvényes formátumot használ. Később lecseréli a helyőrző IP-címét az Erőforrás-kezelő virtuális hálózati átjáró nyilvános IP-címére.
7. Az **Ügyfélcímtér**hez használja az Erőforrás-kezelő virtuális hálózatvirtuális hálózatvirtuális hálózati IP-címtereinek [értékeit.](#connectoverview) Ezzel a beállítással adhatók meg az Erőforrás-kezelő virtuális hálózatára irányítandó címterek. A példában a 192.168.0.0/16, az RMVNet címtartományát használjuk.
8. Kattintson az **OK** gombra az értékek mentéséhez és az **Új VPN-kapcsolat** laphoz való visszatéréshez.

### <a name="3-create-the-virtual-network-gateway"></a><a name="classicgw"></a>3. A virtuális hálózati átjáró létrehozása

1. Az **Új VPN-kapcsolat** lapon jelölje be **az Átjáró létrehozása azonnal** jelölőnégyzetet.
2. Kattintson a **Választható átjárókonfiguráció** elemre a **Átjáró konfigurációja** lap megnyitásához.

   ![Átjáró konfigurációs lapjának megnyitása](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Átjáró konfigurációs lapjának megnyitása")
3. Kattintson **az Alhálózat – A szükséges beállítások konfigurálása** az **Alhálózat hozzáadása** lap megnyitásához. A **név** már konfigurálva van a szükséges értékkel: **GatewaySubnet**.
4. A **Cím tartomány** az átjáró alhálózatának tartományára utal. Bár létrehozhat egy /29 címtartományú átjáró-alhálózatot (3 cím), javasoljuk, hogy hozzon létre egy átjáró alhálózatot, amely több IP-címet tartalmaz. Ez lehetővé teszi a jövőbeli konfigurációk, amelyek több rendelkezésre álló IP-címeket igényelnek. Ha lehetséges, használja a /27 vagy a /28 kapcsolót. Ha ezeket a lépéseket gyakorolja, hivatkozhat a [Példa értékekre.](#values) Ebben a példában a "10.0.0.32/28" kapcsolót használjuk. Az átjáró alhálózatának létrehozásához kattintson az **OK** gombra.
5. Az **átjáró konfigurációs** lapján **a Méret** az átjáró termékváltozatára hivatkozik. Válassza ki az átjáró termékváltozatát a VPN-átjáróhoz.
6. Ellenőrizze, hogy az **Útválasztás típusa** **dinamikus-e,** majd kattintson az **OK** gombra az új **VPN-kapcsolat** lapra való visszatéréshez.
7. Az **Új VPN-kapcsolat** lapon kattintson az **OK** gombra a VPN-átjáró létrehozásának megkezdéséhez. Egy VPN-átjáró létrehozása akár 45 percet is igénybe vehet.

### <a name="4-copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>4. Másolja a virtuális hálózati átjáró nyilvános IP-cím

A virtuális hálózati átjáró létrehozása után megtekintheti az átjáró IP-címét. 

1. Nyissa meg a klasszikus virtuális hálózatot, és kattintson **az Áttekintés gombra.**
2. Kattintson a **VPN-kapcsolatok** elemre a VPN-kapcsolatok lap megnyitásához. A VPN-kapcsolatok lapon megtekintheti a nyilvános IP-címet. Ez a virtuális hálózati átjáróhoz rendelt nyilvános IP-cím. Jegyezze fel az IP-címet. Későbbi lépésekben használhatja, amikor az Erőforrás-kezelő helyi hálózati átjáró konfigurációs beállításaival dolgozik. 
3. Megtekintheti az átjárókapcsolatok állapotát. Figyelje meg, hogy a létrehozott helyi hálózati hely "Csatlakozás" néven szerepel. A kapcsolatok létrehozása után az állapot megváltozik. Ezt a lapot bezárhatja, ha befejezte az állapot megtekintését.

## <a name="section-2---configure-the-resource-manager-vnet-settings"></a><a name="rmvnet"></a>2. szakasz - Az Erőforrás-kezelő virtuális hálózatbeállításainak konfigurálása

Ebben a szakaszban hozza létre a virtuális hálózati átjárót és a helyi hálózati átjárót az erőforrás-kezelő virtuális hálózatához. A képernyőképek csak példaként szolgálnak. Ügyeljen arra, hogy cserélje le az értékeket a saját, vagy használja a [példa](#values) értékeket.

### <a name="1-create-a-virtual-network"></a>1. Hozzon létre egy virtuális hálózat

**Példa értékek:**

* VNet-név = RMVNet <br>
* Címterület = 192.168.0.0/16 <br>
* Erőforráscsoport = RG1 <br>
* Helyszín = USA keleti része <br>
* Alhálózat neve = Alhálózat-1 <br>
* Címtartomány = 192.168.1.0/24 <br>

Ha nem rendelkezik erőforrás-kezelő virtuális hálózattal, és ezeket a lépéseket gyakorlatként futtatja, hozzon létre egy virtuális hálózatot a [Virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md)című lépéseivel a példaértékek használatával.

### <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. Hozzon létre egy virtuális hálózati átjáró

Ebben a lépésben a virtuális hálózat virtuális hálózati átjáróját fogja létrehozni. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet a választott átjáró-termékváltozattól függően.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

**Példa értékek:**

* Virtuális hálózati átjáró neve = RMGateway <br>
* Átjáró típusa = VPN <br>
* VPN-típus = Útvonalalapú <br>
* SKU = VpnGw1 <br>
* Helyszín = USA keleti része <br>
* Virtuális hálózat = RMVNet <br>
* GatewaySubnet = 192.168.0.0/26 <br>
* Első IP konfiguráció = rmgwpip <br>

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="3-create-a-local-network-gateway"></a><a name="createlng"></a>3. Helyi hálózati átjáró létrehozása

**Példa értékek:** Helyi hálózati átjáró = ClassicVNetLocal

| Virtual Network | Címtartomány | Régió | Csatlakozás a helyi hálózati helyhez |Átjáró nyilvános IP-címe|
|:--- |:--- |:--- |:--- |:--- |
| KlasszikusVNet |(10.0.0.0/24) |USA nyugati régiója | RMVNetLocal (192.168.0.0/16) |A ClassicVNet átjáróhoz rendelt nyilvános IP-cím|
| RMVNet | (192.168.0.0/16) |USA keleti régiója |ClassicVNetLocal (10.0.0.0/24) |Az RMVNet-átjáróhoz rendelt nyilvános IP-cím.|

A helyi hálózati átjáró megadja a címtartományt és a klasszikus virtuális hálózathoz és a virtuális hálózati átjáróhoz társított nyilvános IP-címet. Ha ezeket a lépéseket gyakorlatként végzi, olvassa el a Példa értékeket.

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="modifylng"></a>3. szakasz - A klasszikus virtuális hálózat helyi helybeállításainak módosítása

Ebben a szakaszban lecseréli a helyi hely beállításainak megadásakor használt helyőrző IP-címet az Erőforrás-kezelő VPN-átjáró IP-címére. Ez a szakasz a klasszikus (SM) PowerShell-parancsmagokat használja.

1. Az Azure Portalon keresse meg a klasszikus virtuális hálózatot.
2. A virtuális hálózat lapján kattintson az **Áttekintés gombra.**
3. A **VPN-kapcsolatok** csoportban kattintson a helyi hely nevére a képen.

   ![VPN-kapcsolatok](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "VPN-kapcsolatok")
4. A **helyek közötti VPN-kapcsolatok** lapon kattintson a hely nevére.

   ![Webhely neve](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Helyi webhely neve")
5. A helyi webhely csatlakozási lapján kattintson a helyi webhely nevére a **Helyi webhely** lap megnyitásához.

   ![Nyílt helyi helyszín](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Helyi webhely megnyitása")
6. A **Helyi hely** lapon cserélje le a **VPN-átjáró IP-címét** az Erőforrás-kezelő átjáró IP-címére.

   ![Átjáró-ip-cím](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "Átjáró IP-címe")
7. Az IP-cím frissítéséhez kattintson az **OK** gombra.

## <a name="section-4---create-resource-manager-to-classic-connection"></a><a name="RMtoclassic"></a>4. szakasz - Erőforrás-kezelő létrehozása klasszikus kapcsolathoz

Ezekben a lépésekben konfigurálja a kapcsolatot az Erőforrás-kezelő virtuális hálózat a klasszikus virtuális hálózat az Azure Portalon keresztül.

1. A **Minden erőforrás területen**keresse meg a helyi hálózati átjárót. A példánkban a helyi hálózati átjáró a **ClassicVNetLocal**.
2. Kattintson a **Konfiguráció gombra,** és ellenőrizze, hogy az IP-cím értéke a klasszikus virtuális hálózat VPN-átjárója-e. Szükség esetén frissítsen, majd kattintson a **Mentés gombra.** Zárja be az oldalt.
3. A **Minden erőforrás területen**kattintson a helyi hálózati átjáróra.
4. Kattintson a **Kapcsolatok** gombra a Kapcsolatok lap megnyitásához.
5. A **Kapcsolatok** lapon **+** kattintson a kapcsolat hozzáadásához.
6. A **Kapcsolat hozzáadása** lapon nevezze el a kapcsolatot. Például "RMtoClassic".
7. **A webhely közötti beállítás** már ki van jelölve ezen a lapon.
8. Válassza ki azt a virtuális hálózati átjárót, amelyet ehhez a helyhez társítani szeretne.
9. Megosztott **kulcs**létrehozása . Ez a kulcs is használják a kapcsolatot, amely a klasszikus virtuális hálózat az erőforrás-kezelő virtuális hálózat hoz létre. Létrehozhatja a kulcsot, vagy létrehozhat egyet. A példánkban az "abc123"-ot használjuk, de használhatsz (és kell is) valami összetettebbet használni.
10. A kapcsolat létrehozásához kattintson az **OK** gombra.

## <a name="section-5---create-classic-to-resource-manager-connection"></a><a name="classictoRM"></a>5. szakasz - Klasszikus és Erőforrás-kezelő i kapcsolatot hozhat létre

Ezekben a lépésekben konfigurálja a kapcsolatot a klasszikus virtuális hálózat és az erőforrás-kezelő virtuális hálózat. Ezek a lépések powershellt igényelnek. Ez a kapcsolat nem hozható létre a portálon. Győződjön meg arról, hogy a klasszikus (SM) és az Erőforrás-kezelő (RM) PowerShell-parancsmagokat is letöltötte és telepítette.

### <a name="1-connect-to-your-azure-account"></a>1. Csatlakozás Azure-fiókjához

Nyissa meg a PowerShell-konzolt emelt szintű jogosultságokkal, és jelentkezzen be az Azure-fiókjába. A bejelentkezés után a fiók beállításai letöltődnek, hogy azok elérhetők az Azure PowerShell számára. A következő parancsmag az Azure-fiók bejelentkezési hitelesítő adatait kéri az Erőforrás-kezelő telepítési modelljéhez:

```powershell
Connect-AzAccount
```

Olvassa be az Azure-előfizetések listáját.

```powershell
Get-AzSubscription
```

Ha egynél több előfizetéssel rendelkezik, adja meg a használni kívánt előfizetést.

```powershell
Select-AzSubscription -SubscriptionName "Name of subscription"
```

Ezután jelentkezzen be a klasszikus PowerShell-parancsmagok (Service Management) használatához. A klasszikus üzembe helyezési modellhez az alábbi paranccsal hozzáadhatja azure-fiókját:

```powershell
Add-AzureAccount
```

Az előfizetések listájának beszerezése. Erre a lépésre akkor lehet szükség, ha hozzáadja a Service Management parancsmagokat, az Azure-modul telepítésétől függően.

```powershell
Get-AzureSubscription
```

Ha egynél több előfizetéssel rendelkezik, adja meg a használni kívánt előfizetést.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. A hálózati konfigurációs fájl értékeinek megtekintése

Amikor virtuális hálózatot hoz létre az Azure Portalon, az Azure által használt teljes név nem látható az Azure Portalon. Például egy virtuális hálózat, amely úgy tűnik, hogy "ClassicVNet" az Azure Portalon lehet, hogy sokkal hosszabb nevet a hálózati konfigurációs fájlban. A név a következőre hasonlíthat: "Group ClassicRG ClassicVNet". Ezekben a lépésekben letölti a hálózati konfigurációs fájlt, és megtekintheti az értékeket.

Hozzon létre egy könyvtárat a számítógépén, majd exportálja a hálózati konfigurációs fájlt a könyvtárba. Ebben a példában a hálózati konfigurációs fájlt a C:\AzureNet helyre exportálja.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Nyissa meg a fájlt egy szövegszerkesztővel, és tekintse meg a klasszikus virtuális hálózat nevét. Használja a neveket a hálózati konfigurációs fájlban a PowerShell-parancsmagok futtatásakor.

- A virtuális hálózatok nevei **VirtualNetworkSite névként** vannak felsorolva =
- A webhelynevek **LocalNetworkSite name=** néven jelennek meg.

### <a name="3-create-the-connection"></a>3. Hozza létre a kapcsolatot

Állítsa be a megosztott kulcsot, és hozza létre a kapcsolatot a klasszikus virtuális hálózatról az erőforrás-kezelő virtuális hálózatához. A portálhasználatával nem állítható be a megosztott kulcs. Győződjön meg arról, hogy futtassa ezeket a lépéseket, miközben bejelentkezett a PowerShell-parancsmagok klasszikus verziójával. Ehhez használja **az Add-AzureAccount .to**do do do it, use Add-AzureAccount . Ellenkező esetben nem fogja tudni beállítani az "-AzureVNetGatewayKey" .

- Ebben a példában a **-VNetName** a klasszikus virtuális hálózat neve, ahogy az a hálózati konfigurációs fájlban található. 
- A **-LocalNetworkSiteName** a helyi helyhez megadott név, ahálózati konfigurációs fájlban található módon.
- A **-SharedKey** egy létrehozott és megadott érték. Ebben a példában *az abc123-at*használtuk, de valami bonyolultabbat hozhat létre. A lényeg az, hogy az itt megadott értéknek meg kell egyeznie azzal az értékkel, amelyet az Erőforrás-kezelő klasszikus kapcsolathoz való létrehozásakor megadott.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="section-6---verify-your-connections"></a><a name="verify"></a>6. szakasz - A kapcsolatok ellenőrzése

A kapcsolatok az Azure Portalon vagy a PowerShell használatával ellenőrizheti a kapcsolatokat. Az ellenőrzés során előfordulhat, hogy a kapcsolat létrehozásakor várnia kell egy-két percet. Ha a kapcsolat sikeres, a kapcsolat állapota "Csatlakozás" állapotról "Csatlakoztatva" állapotra változik.

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>A klasszikus virtuális hálózat és az erőforrás-kezelő virtuális hálózatának kapcsolatának ellenőrzése

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Az erőforrás-kezelő virtuális hálózatának a klasszikus virtuális hálózathoz való csatlakozásának ellenőrzése

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Virtuális hálózatok közötti kapcsolat – gyakori kérdések

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
