---
title: 'Klasszikus virtuális hálózatok összekapcsolása az Azure Resource Manager-alapú virtuális hálózatokhoz: Portál |} A Microsoft Docs'
description: Lépéseket a klasszikus virtuális hálózatok csatlakoztatása Resource Manager virtuális hálózatok VPN Gateway és a portál használatával
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: 9779885869666ffd1198afcda944823b99d52e5b
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417973"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>A portál használatával különböző üzemi modellekből származó virtuális hálózatok összekapcsolása

Ez a cikk bemutatja, hogyan klasszikus virtuális hálózatok csatlakoztatása Resource Manager-alapú virtuális hálózatokhoz kommunikálnak egymással a különböző üzembe helyezési modellekben található erőforrások. A jelen cikkben ismertetett lépések elsősorban az Azure-portálon, de ez a konfiguráció a listából a cikk a PowerShell használatával is létrehozhat.

> [!div class="op_single_selector"]
> * [Portál](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Egy klasszikus virtuális hálózat csatlakoztatása virtuális hálózathoz a Resource Manager nagyon hasonlít egy virtuális hálózathoz csatlakozik egy helyszíni helyhez. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához. Különböző előfizetésben található, és különböző régiókban található virtuális hálózatok közötti kapcsolatot hozhat létre. Már van kapcsolat a helyszíni hálózathoz, virtuális hálózatok mindaddig, amíg az átjárót, hogy fiókjuk konfigurálva lett a dinamikus vagy útválasztó-alapú is csatlakoztathat. A virtuális hálózatok közötti kapcsolatokról további információt a cikk végén, a [Virtuális hálózatok közötti kapcsolat – gyakori kérdések](#faq) című részben talál. 

Ha még nem rendelkezik a virtuális hálózati átjáró, és nem szeretne létrehozni egyet, érdemes inkább fontolja meg a virtuális hálózatok összekapcsolása a virtuális hálózatok közötti társviszony-létesítés. A virtuális hálózatok közötti társviszony nem használ VPN-átjárót. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

### <a name="before"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Ezek a lépések feltételezik, hogy mindkét virtuális hálózaton már korábban létrejött. Ha ez a cikk gyakorlatként használja, és nem rendelkezik a virtuális hálózatok, nincsenek kapcsolatok hozhat létre, azokat a lépéseket.
* Győződjön meg arról, hogy a virtuális hálózat címtartományát nem lehetnek egymással átfedésben, vagy átfedésben más kapcsolatokat, az átjárók csatlakoznak előfordulhat, hogy azokat a tartományokat.
* Telepítse a legújabb PowerShell-parancsmagok a Resource Manager és a szolgáltatásfelügyeleti (klasszikus). Ebben a cikkben használjuk az Azure portal és a PowerShell. A kapcsolat a Resource Manager virtuális hálózathoz a klasszikus virtuális hálózat létrehozása PowerShell szükséges. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál. 

### <a name="values"></a>Példabeállítások

Ezekkel az értékekkel létrehozhat egy tesztkörnyezetet, vagy a segítségükkel értelmezheti a cikkben szereplő példákat.

**Klasszikus virtuális hálózat**

Virtuális hálózat neve = ClassicVNet <br>
Címtér = 10.0.0.0/24 <br>
Alhálózat neve Subnet-1 = <br>
Alhálózati címtartomány = 10.0.0.0/27 <br>
Előfizetés = a használni kívánt előfizetés <br>
Erőforráscsoport = ClassicRG <br>
Hely = USA nyugati RÉGIÓJA <br>
Átjáró-alhálózat = 10.0.0.32/28 <br>
Helyi hely = RMVNetLocal <br>

**Resource Manager virtuális hálózathoz**

Virtuális hálózat neve = RMVNet <br>
Címtér = 192.168.0.0/16 <br>
Erőforráscsoport = RG1 <br>
Hely = USA keleti RÉGIÓJA <br>
Alhálózat neve Subnet-1 = <br>
Címtartomány = 192.168.1.0/24 <br>
Átjáró-alhálózat = 192.168.0.0/26 <br>
Virtuális hálózati átjáró neve = RMGateway <br>
Átjáró típusa = VPN <br>
VPN-típus = útvonalalapú <br>
Termékváltozat = VpnGw1 <br>
Hely = USA keleti RÉGIÓJA <br>
Virtuális hálózat = RMVNet <br> (társítása a VPN-átjárón a virtuális hálózat) Első IP-konfiguráció = rmgwpip <br> (átjáró nyilvános IP-címe) Helyi hálózati átjáró = ClassicVNetLocal <br>
Kapcsolat neve = RMtoClassic

### <a name="connectoverview"></a>Kapcsolat – áttekintés

Ebben a konfigurációban, hozzon létre VPN gateway-kapcsolat a virtuális hálózatok között egy IPsec/IKE VPN-alagúton keresztül. Győződjön meg arról, hogy a virtuális hálózatok közötti tartományok egyike átfedésben vannak egymással, vagy azokkal a helyi hálózatokat, amelyekhez csatlakoznak.

Az alábbi táblázatban látható egy példa a Példa virtuális hálózatok és helyek definiálásának módját:

| Virtual Network | Címtartomány | Régió | Csatlakozik a helyi hálózati telephely |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |USA nyugati régiója | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |USA keleti régiója |ClassicVNetLocal (10.0.0.0/24) |

## <a name="classicvnet"></a>1. szakasz – a klasszikus virtuális hálózat beállításainak konfigurálása

Ebben a szakaszban hoz létre a klasszikus virtuális hálózat, a helyi hálózati (helyi), és a virtuális hálózati átjárót. A képernyőképek csak példaként szolgálnak. Ügyeljen arra, hogy cserélje le az értékeket a saját, vagy használja a [példa](#values) értékeket.

### 1. <a name="classicvnet"></a>Egy klasszikus virtuális hálózat létrehozása

Ha gyakorlatként futtatja ezeket a lépéseket és egy klasszikus virtuális hálózat nem rendelkezik, egy virtuális hálózat használatával létrehozhat [Ez a cikk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) és a [példa](#values) beállítások értékei a fent.

Ha már rendelkezik egy virtuális hálózathoz egy VPN-átjáróval, ellenőrizze, hogy az átjáró dinamikus. Ha statikus, először törölnie kell a VPN-átjárót, mielőtt továbbhaladna az [a helyi hely konfigurálása](#local).

1. Nyissa meg az [Azure Portalt](https://ms.portal.azure.com), majd jelentkezzen be az Azure-fiókjával.
2. Kattintson a **+ erőforrás létrehozása** nyissa meg a "New".
3. A "Keresés a piactéren" mezőbe írja be a "Virtuális hálózat". Ha ehelyett hálózat -> virtuális hálózat, nem fog kapni egy klasszikus virtuális hálózat létrehozásának lehetősége.
4. A visszaadott listában keresse meg a "Virtuális hálózat", és kattintson rá a virtuális hálózat lap megnyitásához. 
5. A virtuális hálózat lapon válassza ki a "Klasszikus" klasszikus virtuális hálózat létrehozásához. Ha itt az alapértelmezett, meg fog lezárását Resource Manager virtuális hálózathoz helyette.

### 2. <a name="local"></a>A helyi hely konfigurálása

1. Navigáljon a **összes erőforrás** , és keresse meg a **ClassicVNet** a listában.
2. Az a **áttekintése** lap a **VPN-kapcsolatok** területén kattintson **átjáró** átjáró létrehozásához.
  ![VPN-átjáró konfigurálása](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "VPN-átjáró konfigurálása")
3. Az a **új VPN-kapcsolat** lapon a **kapcsolattípus**, jelölje be **Site-to-site**.
4. A **helyi hely**, kattintson a **kötelező beállítások konfigurálása**. Ekkor megnyílik a **helyi hely** lapot.
5. Az a **helyi hely** lapon, hozzon létre egy nevet a Resource Manager virtuális hálózathoz. Például "RMVNetLocal."
6. Ha a VPN-átjáró a Resource Manager virtuális hálózat már tartalmaz egy nyilvános IP-címet használja az értéket a **VPN-átjáró IP-cím** mező. Ha ezek a lépések végrehajtása gyakorlatként, vagy még nem rendelkezik a virtuális hálózati átjáró a Resource Manager virtuális hálózathoz, a helyőrző IP-címet is alkotják. Győződjön meg arról, hogy a helyőrző IP-címet egy érvényes formátumot használja. Később, cserélje le a helyőrző IP-címet a Resource Manager virtuális hálózati átjáró nyilvános IP-címét.
7. A **Ügyfélcímtér**, használja a [értékek](#connectoverview) a virtuális hálózati IP-címtereket a Resource Manager virtuális hálózathoz. Ez a beállítás adja meg a Resource Manager virtuális hálózatot a címterek szolgál. A példában 192.168.0.0/16, a RMVNet címtartománya használjuk.
8. Kattintson a **OK** mentse az értékeket, és térjen vissza a **új VPN-kapcsolat** lapot.

### <a name="classicgw"></a>3. Virtuális hálózati átjáró létrehozása

1. Az a **új VPN-kapcsolat** lapon válassza ki a **átjáró azonnali létrehozása** jelölőnégyzetet.
2. Kattintson a **Választható átjárókonfiguráció** elemre a **Átjáró konfigurációja** lap megnyitásához.

  ![Nyissa meg az átjáró konfigurációs lapján](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "nyissa meg az átjáró konfigurációs lapján")
3. Kattintson a **alhálózat – kötelező beállítások konfigurálása** megnyitásához a **alhálózat hozzáadása** lapot. A **neve** már konfigurálva van a szükséges érték: **Átjáró-alhálózat**.
4. A **címtartomány** az átjáró-alhálózat tartományának hivatkozik. Bár létrehozhat egy átjáró-alhálózat egy/29-címtartomány (3 címeket), azt javasoljuk, hogy a további IP-címet tartalmaz, amelyek átjáró-alhálózat létrehozását. Ez be tudja fogadni a jövőbeni konfigurációk, amely több elérhető IP-címeket lehet szükség. Ha lehetséges használjon, / 27-eset vagy/28. Ha gyakorlatként használja ezeket a lépéseket, olvassa el a [példaértékeket](#values). Ebben a példában "10.0.0.32/28" használ. Kattintson a **OK** az átjáró-alhálózat létrehozásához.
5. Az a **átjárókonfiguráció** lapon **mérete** hivatkozik az átjáró-Termékváltozatot. Válassza ki az átjáró-Termékváltozatot a VPN-átjáró számára.
6. Ellenőrizze a **útválasztási típusának** van **dinamikus**, majd kattintson **OK** térjen vissza a **új VPN-kapcsolat** lap.
7. Az a **új VPN-kapcsolat** kattintson **OK** a VPN-átjáró létrehozásához. Egy VPN-átjáró létrehozása akár 45 percet is igénybe vehet.

### <a name="ip"></a>4. A virtuális hálózati átjáró nyilvános IP-cím másolása

A virtuális hálózati átjáró létrehozása után megtekintheti az átjáró IP-cím. 

1. Keresse meg a klasszikus virtuális hálózat, és kattintson a **áttekintése**.
2. Kattintson a **VPN-kapcsolatok** a VPN-kapcsolatok lap megnyitásához. A VPN kapcsolatok lapon megtekintheti a nyilvános IP-címet. Ez a virtuális hálózati átjárójához rendelt nyilvános IP-címet. Jegyezze fel az IP-címet. Használhatja azt a későbbi lépések a Resource Manager helyi hálózati átjáró konfigurációs beállítások való munka során. 
3. Megtekintheti a gateway-kapcsolatok állapotát. Figyelje meg, hogy a létrehozott helyi hálózati hely "Csatlakozás" szerepel. Az állapot változik, miután létrehozta a kapcsolatot. Ezen a lapon bezárhatja, amikor elkészült, az állapota megtekintése.

## <a name="rmvnet"></a>2. szakasz – a Resource Manager virtuális hálózati beállítások konfigurálása

Ebben a szakaszban hoz létre a virtuális hálózati átjáró és a helyi hálózati átjárót a Resource Manager virtuális hálózathoz. A képernyőképek csak példaként szolgálnak. Ügyeljen arra, hogy cserélje le az értékeket a saját, vagy használja a [példa](#values) értékeket.

### <a name="1-create-a-virtual-network"></a>1. Virtuális hálózat létrehozása

**Példaértékek:**

* Virtuális hálózat neve = RMVNet <br>
* Címtér = 192.168.0.0/16 <br>
* Erőforráscsoport = RG1 <br>
* Hely = USA keleti RÉGIÓJA <br>
* Alhálózat neve Subnet-1 = <br>
* Címtartomány = 192.168.1.0/24 <br>


Ha nem rendelkezik a Resource Manager virtuális hálózathoz, és gyakorlatként futtatja ezeket a lépéseket, hozzon létre egy virtuális hálózat lépéseivel [hozzon létre egy virtuális hálózatot](../virtual-network/quick-create-portal.md), a példaértékek használatával.

### <a name="2-create-a-gateway-subnet"></a>2. Átjáróalhálózat létrehozása

**Példaérték:** Átjáró-alhálózat = 192.168.0.0/26

Virtuális hálózati átjáró létrehozása előtt először az átjáró-alhálózat létrehozásához. Átjáróalhálózat létrehozása/28-as vagy nagyobb CIDR-számot (/ 27, / 26-os stb.). Létrehozásakor gyakorlatként részeként, használhatja a példában szereplő értékeket.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="creategw"></a>3. Virtuális hálózati átjáró létrehozása

**Példaértékek:**

* Virtuális hálózati átjáró neve = RMGateway <br>
* Átjáró típusa = VPN <br>
* VPN-típus = útvonalalapú <br>
* Termékváltozat = VpnGw1 <br>
* Hely = USA keleti RÉGIÓJA <br>
* Virtuális hálózat = RMVNet <br>
* Első IP-konfiguráció = rmgwpip <br>

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

### <a name="createlng"></a>4. Helyi hálózati átjáró létrehozása

**Példaértékek:** Helyi hálózati átjáró = ClassicVNetLocal

| Virtual Network | Címtartomány | Régió | Csatlakozik a helyi hálózati telephely |Átjáró nyilvános IP-címe|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |USA nyugati régiója | RMVNetLocal (192.168.0.0/16) |ClassicVNet átjáróhoz rendelt nyilvános IP-cím|
| RMVNet | (192.168.0.0/16) |USA keleti régiója |ClassicVNetLocal (10.0.0.0/24) |RMVNet átjáróhoz rendelt nyilvános IP-címe.|

A helyi hálózati átjáró meghatározza a címtartományt és a klasszikus virtuális hálózat és a virtuális hálózati átjáró nyilvános IP-címet. Ha gyakorlatként végez ezeket a lépéseket, tekintse meg a példában szereplő értékeket.

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="modifylng"></a>3. szakasz – a klasszikus virtuális hálózatok helyi hely beállításainak módosítása

Ez a szakasz cserélje le a helyőrző IP-címet, amelyet a helyi webhely beállításainak megadásakor a Resource Manager VPN-átjáró IP-cím a használt. Ez a szakasz a klasszikus (SM) PowerShell-parancsmagokat használja.

1. Az Azure Portalon lépjen a klasszikus virtuális hálózat.
2. A virtuális hálózat lapján, kattintson a **áttekintése**.
3. Az a **VPN-kapcsolatok** területén kattintson az ábra a helyi hely neve.

  ![VPN-kapcsolatok](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "VPN-kapcsolatok")
4. Az a **Site-to-site VPN-kapcsolatok** lap, kattintson a webhely nevét.

  ![Site-name](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "helyi hely neve")
5. Nyissa meg a helyi hely neve a helyi helynek a kapcsolat lapon kattintson a **helyi hely** lapot.

  ![Open-local-hely](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "helyi webhely megnyitása")
6. Az a **helyi hely** lapon, cserélje le a **VPN-átjáró IP-cím** a Resource Manager-átjáró IP-címmel.

  ![Átjáró – ip-cím](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "átjáró IP-címe")
7. Kattintson a **OK** frissíteni az IP-címet.

## <a name="RMtoclassic"></a>4. szakasz – klasszikus kapcsolat létrehozása a Resource Managerben

Ezeket a lépéseket konfigurálja a kapcsolat a Resource Manager virtuális hálózat és a klasszikus virtuális hálózatot az Azure portal használatával.

1. A **összes erőforrás**, keresse meg a helyi hálózati átjárót. Ebben a példában a helyi hálózati átjáró a **ClassicVNetLocal**.
2. Kattintson a **konfigurációs** , és győződjön meg arról, hogy az IP-címének értékét a VPN-átjáró számára a klasszikus virtuális hálózatot. Szükség esetén frissítse, majd kattintson a **mentése**. Zárja be a lapot.
3. A **összes erőforrás**, kattintson a helyi hálózati átjárót.
4. Kattintson a **kapcsolatok** a kapcsolatok lap megnyitásához.
5. Az a **kapcsolatok** kattintson **+** kapcsolatot szeretne hozzáadni.
6. Az a **kapcsolat hozzáadása** lapon, a kapcsolat nevét. Például "RMtoClassic."
7. **Site-to-Site** már ki van jelölve ezen az oldalon.
8. Válassza ki a virtuális hálózati átjáró, amely a hellyel való társítani szeretné.
9. Hozzon létre egy **megosztott kulcs**. Ezt a kulcsot a Resource Manager virtuális hálózatot hoz létre a klasszikus virtuális hálózatok közötti kapcsolat is használatban van. A kulcs létrehozásához, vagy kitalálhat egyet. Ebben a példában az "abc123" kulcsot használjuk, de lehetséges (és javasolt) használhatja összetettebb.
10. Kattintson a **OK** a kapcsolat létrehozásához.

## <a name="classictoRM"></a>5. szakasz – klasszikusról Resource Manager-kapcsolat létrehozása

Ezeket a lépéseket a kapcsolat a klasszikus virtuális hálózat és a Resource Manager virtuális hálózatot fog konfigurálni. Ezeket a lépéseket a PowerShell szükséges. A portálon Ez a kapcsolat nem hozható létre. Győződjön meg arról, hogy letöltötte és telepítette a klasszikus (SM) és az erőforrás-kezelő (RM) PowerShell-parancsmagok is.

### <a name="1-connect-to-your-azure-account"></a>1. Csatlakozás az Azure-fiókhoz

Nyissa meg a PowerShell-konzol emelt szintű jogosultságokkal rendelkeznek, és jelentkezzen be az Azure-fiókjával. A bejelentkezés után, hogy elérhetők legyenek az Azure PowerShell-lel a rendszer letölti a fiók beállításait. A következő parancsmagot a bejelentkezési hitelesítő adatokat kér a Resource Manager üzemi modell az Azure-fiók:

```powershell
Connect-AzAccount
```

Olvassa be az Azure-előfizetések listáját.

```powershell
Get-AzSubscription
```

Ha több előfizetéssel rendelkezik, válassza ki a használni kívánt előfizetést.

```powershell
Select-AzSubscription -SubscriptionName "Name of subscription"
```

Ezután jelentkezzen be a klasszikus PowerShell-parancsmag (szolgáltatásfelügyelet) használatával. A következő parancs használatával adja hozzá a klasszikus üzemi modellben az Azure-fiókjával:

```powershell
Add-AzureAccount
```

Az előfizetések listájának beolvasása. Ez a lépés hozzáadása a Service Management-parancsmagok, attól függően, az Azure-modul telepítésekor lehet szükség.

```powershell
Get-AzureSubscription
```

Ha több előfizetéssel rendelkezik, válassza ki a használni kívánt előfizetést.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. A hálózati konfigurációs fájl értékeinek megtekintése

Virtuális hálózat létrehozásakor az Azure Portalon a teljes Azure használó név nem látható az Azure Portalon. Virtuális hálózat neve "ClassicVNet" az Azure Portalon megjelenő például rendelkezhet egy sokkal hosszabb nevet a hálózat konfigurációs fájljában. Neve a következőhöz hasonló: "Csoport ClassicRG ClassicVNet". Az alábbi lépéseket töltse le a hálózati konfigurációs fájlt, és tekintse meg az értékeket.

Hozzon létre egy könyvtárat a számítógépén, majd exportálja a hálózati konfigurációs fájlt a könyvtárba. Ebben a példában a hálózati konfigurációs fájlt a C:\AzureNet helyre exportálja.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Nyissa meg a fájlt egy szövegszerkesztőben, és tekintse meg a nevet a klasszikus virtuális hálózat számára. A hálózati konfigurációs fájlt a neveket használja, ha futtatja a PowerShell-parancsmagokat.

- Virtuális hálózat nevei jelennek meg **VirtualNetworkSite name =**
- Helynevek létrehoztak **LocalNetworkSite name =**

### <a name="3-create-the-connection"></a>3. A kapcsolat létrehozása

Állítsa be a megosztott kulcsot, és hozza létre a kapcsolatot a klasszikus virtuális hálózatot a Resource Manager virtuális hálózathoz. A portál használatával a megosztott kulcs nem állítható be. Ellenőrizze, hogy ezeket a lépéseket a klasszikus verzióra, a PowerShell-parancsmagok használatával bejelentkeztek futtatja. Ehhez használja **Add-AzureAccount**. Ellenkező esetben nem állíthatnak be a "-AzureVNetGatewayKey".

- Ebben a példában **- VNetName** neve a klasszikus virtuális hálózat található, a hálózat konfigurációs fájljában. 
- A **- LocalNetworkSiteName** határoz meg a helyi hely neve a hálózat konfigurációs fájljában található.
- A **- SharedKey** érték, amely az Ön hozza létre, és adja meg. Ebben a példában használt *abc123*, azonban összetettebb is létrehozhat. A lényeg az, hogy az itt megadott értéknek kell lennie, amely a Resource Manager, klasszikus kapcsolat létrehozásakor megadott ugyanazt az értéket.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="verify"></a>6. szakasz – a kapcsolatok ellenőrzése

Az Azure portal vagy a PowerShell használatával ellenőrizheti a kapcsolatokat. Ellenőrzésekor, szükség lehet egy percig várni, vagy két, a kapcsolat létrehozása folyamatban van. Ha a kapcsolat létrejött, a kapcsolat állapota "Csatlakozás" "Connected" értékűre.

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>A kapcsolat a klasszikus virtuális hálózat és a Resource Manager virtuális hálózat ellenőrzése

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>A kapcsolat a Resource Manager virtuális hálózat és a klasszikus virtuális hálózat ellenőrzése

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Virtuális hálózatok közötti kapcsolat – gyakori kérdések

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
