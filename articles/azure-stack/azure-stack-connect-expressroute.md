---
title: Csatlakozás Azure verem Azure ExpressRoute segítségével
description: Azure-veremben lévő virtuális hálózatok az Azure-ban ExpressRoute virtuális hálózatokhoz való kapcsolódásának módja.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 544fc1bcc9212fd38938d58447f5050df2a08796
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
ms.locfileid: "29844921"
---
# <a name="connect-azure-stack-to-azure-using-expressroute"></a>Csatlakozás Azure verem Azure ExpressRoute segítségével

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Azure-veremben lévő virtuális hálózatok az Azure virtuális hálózatokhoz való kapcsolódásának támogatott két módszer áll rendelkezésre:
   * **Site-to-Site**

     VPN-kapcsolaton keresztül IPsec (IKE v1 és IKE v2). Ehhez a kapcsolattípushoz VPN-eszköz vagy RRAS szükséges. További információkért lásd: [Azure verem csatlakozzon az Azure VPN-kapcsolattal](azure-stack-connect-vpn.md).
   * **ExpressRoute**

     A közvetlen kapcsolat az Azure-bA az Azure-verem telepítéséből. Az ExpressRoute **nem** a nyilvános interneten keresztül VPN-kapcsolatot. Azure ExpressRoute kapcsolatos további információkért lásd: [ExpressRoute – áttekintés](../expressroute/expressroute-introduction.md).

Ez a cikk Azure verem csatlakozni az Azure ExpressRoute segítségével példáját mutatja be.
## <a name="requirements"></a>Követelmények
Csatlakozás Azure verem és az Azure ExpressRoute segítségével konkrét követelmények a következők:
* Azure-előfizetés ExpressRoute-kapcsolatcsoportot és Vnetek létrehozása az Azure-ban.
* A kiépített ExpressRoute-kapcsolatcsoportot keresztül egy [kapcsolat szolgáltatójánál](../expressroute/expressroute-locations.md).
* Olyan útválasztó, amely rendelkezik a WAN portokhoz kapcsolódó ExpressRoute-kapcsolatcsoportot.
* Az útválasztó helyi hálózati oldalán az Azure-verem több-Bérlős átjáró kapcsolódik.
* Az útválasztó támogatnia kell a helyi hálózatra Csatlakozó csatoló és Azure verem több-Bérlős átjáró közötti telephelyek közötti VPN-kapcsolatok.
* Ha egynél több bérlő fel van véve az Azure Alkalmazásveremben üzembe, az útválasztó létre tudja hozni több VRFs (virtuális útválasztási és továbbító) kell lennie.

Az alábbi ábrán egy hálózati áttekintése látható, a konfiguráció befejezése után:

![Fogalmi diagramja](media/azure-stack-connect-expressroute/Conceptual.png)

**1. ábra**

Az alábbi architektúra ábra bemutatja, hogyan több bérlő csatlakozni a veremben Azure infrastruktúra az ExpressRoute-útválasztón keresztül Azure Microsoft peremére:

![Architektúradiagram](media/azure-stack-connect-expressroute/Architecture.png)

**2. ábra**

Ebben a cikkben bemutatott példában a azonos architektúrát használ csatlakozhatnak az Azure ExpressRoute magánhálózati társviszony-létesítés használatával. Elkészült a virtuális hálózati átjáró telephelyek közötti VPN kapcsolaton keresztül egy ExpressRoute-útválasztó Azure-készletben. Ebben a cikkben a lépések bemutatják, hogyan között két Vnetek a két különböző bérlőkhöz Azure-készletben a megfelelő Vnetek az Azure-ban a végpontok közötti kapcsolat létrehozásához. Ha szeretné, számos Vnetek bérlői és replikálja a lépéseket az egyes bérlők számára, vagy használja ebben a példában csak egyetlen bérlői hálózatok központi telepítéséhez adja hozzá.

## <a name="configure-azure-stack"></a>Az Azure verem konfigurálása
Most hozzon létre az erőforrásokat, be kell állítani az Azure-verem környezet hez bérlőként. A következő lépések bemutatják, hogy mit kell tennie. Ezek az utasítások létrehozását mutatják be a verem Azure portál használatával erőforrásokat, de a PowerShell is használható.

![Hálózati erőforrás lépései](media/azure-stack-connect-expressroute/image2.png)
### <a name="before-you-begin"></a>Előkészületek
A konfigurációs megkezdése előtt szüksége:
* Egy Azure Alkalmazásveremben üzembe.

   Azure verem szoftverfejlesztői készlet telepítésével kapcsolatos információkért lásd: [Azure verem szoftverfejlesztői készlet telepítési gyors üzembe helyezés](azure-stack-deploy-overview.md).
* Az ajánlat, amely a felhasználó kérhet le Azure veremben.

  Útmutatásért lásd: [virtuális gépek Azure verem felhasználók számára történő elérhetővé](azure-stack-tutorial-tenant-vm.md).

### <a name="create-network-resources-in-azure-stack"></a>Hozzon létre a hálózati erőforrások Azure verem

A következő eljárásokkal hozhat létre a szükséges hálózati erőforrások Azure verem az egyes bérlők számára:

#### <a name="create-the-virtual-network-and-vm-subnet"></a>A virtuális hálózat és a virtuálisgép-alhálózat létrehozása
1. Jelentkezzen be a felhasználói portál (bérlői) felhasználói fiókkal.

2. Kattintson a portál **új**.

   ![](media/azure-stack-connect-expressroute/MAS-new.png)

3. A piactér menüjében válassza a **Hálózatkezelés** lehetőséget.

4. A menüben kattintson a **Virtuális hálózat** elemre.

5. Az alábbi táblázat segítségével a megfelelő mezőkbe írja be a értékeket:

   |Mező  |Érték  |
   |---------|---------|
   |Name (Név)     |Tenant1VNet1         |
   |Címtér     |10.1.0.0/16|
   |Alhálózat neve     |Tenant1-Sub1|
   |Alhálózati címtartomány     |10.1.1.0/24|

6. Most a korábban létrehozott Előfizetést kell látnia az **Előfizetés** mezőben.

    a. Az erőforráscsoport, hozzon létre egy erőforráscsoportot, vagy ha már rendelkezik egy, válassza ki a **meglévő**.

    b. Ellenőrizze az alapértelmezett helyet.

    c. Kattintson a **rögzítés az irányítópulton**.

    d. Kattintson a **Create** (Létrehozás) gombra.



#### <a name="create-the-gateway-subnet"></a>Az átjáróalhálózat létrehozása
1. Nyissa meg a virtuális hálózati erőforráshoz (Tenant1VNet1) létrehozott az irányítópultról.
2. Válassza a beállítások szakaszban **alhálózatok**.
3. Kattintson az **Átjáró alhálózata** lehetőségre egy átjáró-alhálózatnak a virtuális hálózathoz történő hozzáadásához.
   
    ![](media/azure-stack-connect-expressroute/gatewaysubnet.png)
4. Az alhálózat neve alapértelmezés szerint **GatewaySubnet**.
   Az átjáró-alhálózatok egyediek, és a megfelelő működéshez ezzel az adott névvel kell rendelkezniük.
5. Az a **-címtartományt** mezőbe a címe **10.1.0.0/24**.
6. Kattintson a **OK** az átjáró alhálózatának létrehozásához.

#### <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása
1. Az Azure-verem felhasználói portálon kattintson **új**.
   
2. A piactér menüjében válassza a **Hálózatkezelés** lehetőséget.
3. A hálózati erőforrások listájában válassza a **Virtuális hálózati átjáró** elemet.
4. A **Név** mezőbe írja be a következőt: **GW1**.
5. Virtuális hálózat kiválasztásához kattintson a **Virtuális hálózat** elemre.
   Válassza ki **Tenant1VNet1** a listából.
6. Kattintson a **Nyilvános IP-cím** menüpontra. Ha a **nyilvános IP-cím kiválasztása** szakasz megnyitja kattintson **hozzon létre új**.
7. A **Név** mezőbe írja be a **GW1-PiP** nevet, majd kattintson az **OK** gombra.
8. Az **VPN típusa** mezőben alapértelmezés szerint a **Útvonalalapú** lehetőség van kiválasztva.
    Ne módosítsa ezt a beállítást.
9. Ellenőrizze, hogy az **Előfizetés** és a **Hely** mező értéke helyes-e. Ha azt szeretné, az erőforrás az irányítópulton rögzíthető. Kattintson a **Create** (Létrehozás) gombra.

#### <a name="create-the-local-network-gateway"></a>A helyi hálózati átjáró létrehozása

A helyi hálózati átjáró erőforrás célja a távoli átjáró, a VPN-kapcsolat másik végén jelzi. Ebben a példában a távoli oldal esetén a LAN-alkapcsolat az ExpressRoute-útválasztó. Ebben a példában 1 bérlő a távoli cím 10.60.3.255 a 2. ábrán látható módon.

1. Jelentkezzen be az Azure Stack fizikai gépre.
2. Jelentkezzen be a felhasználói portálra, a felhasználói fiókjával, és kattintson a **új**.
3. A piactér menüjében válassza a **Hálózatkezelés** lehetőséget.
4. Az erőforrások listájában válassza a **Helyi hálózati átjáró** elemet.
5. Az a **neve** mező típusa **ER-útválasztó-GW**.
6. Az a **IP-cím** mezőben, tekintse meg a 2. ábra. Az IP-cím, az ExpressRoute-útválasztó helyi hálózati subinterface bérlői 1 10.60.3.255. A saját környezetben írja be a megfelelő az útválasztó-illesztő IP-címét.
7. Az a **Címterület** mezőbe írja be a használni kívánt Azure-ban való kapcsolódáshoz Vnetek címterében. Ebben a példában a Diagram 2 meg. A bérlői 1, figyelje meg, hogy vannak-e a szükséges alhálózatok **192.168.2.0/24** (Ez az a központ Vnetet az Azure-ban) és **10.100.0.0/16** (Ez az a küllő Vnetet az Azure-ban). Írja be a saját környezetének megfelelő alhálózatok.
   > [!IMPORTANT]
   > Ez a példa feltételezi, hogy a statikus útvonalak a telephelyek közötti VPN-kapcsolat az Azure-verem átjáró és az ExpressRoute-útválasztó közötti.

8. Ellenőrizze, hogy a **előfizetés**, **erőforráscsoport**, és **hely** helyességét, és kattintson a **létrehozása**.

#### <a name="create-the-connection"></a>A kapcsolat létrehozása
1. Az Azure-verem felhasználói portálon kattintson **új**.
2. A piactér menüjében válassza a **Hálózatkezelés** lehetőséget.
3. Az erőforrások listájában válassza a **Kapcsolat** elemet.
4. Az a **alapjai** beállítások területen válassza a **pont-pont (IPSec)** , a **kapcsolattípus**.
5. Válassza ki a **előfizetés**, **erőforráscsoport**, és **hely** kattintson **OK**.
6. Az a **beállítások** kattintson **virtuális hálózati átjáró** kattintson **GW1**.
7. Kattintson a **helyi hálózati átjáró**, és kattintson a **ER útválasztó GW**.
8. Az a **kapcsolatnév** mezőbe írja be **ConnectToAzure**.
9. Az a **megosztott kulcsot (PSK)** mezőbe írja be **abc123** kattintson **OK**.
10. Az a **összegzés** kattintson **OK**.

    A kapcsolat létrehozása után megtekintheti a nyilvános IP-cím, a virtuális hálózati átjáró által használt. A cím keresése a verem Azure-portálon, tallózással keresse meg a virtuális hálózati átjáró. A **áttekintése**, keresse a **nyilvános IP-cím**. Megjegyzés: Ez a cím; használhatja azt a *belső IP-cím* a következő szakaszban (adott esetben a telepítés számára).

    ![](media/azure-stack-connect-expressroute/GWPublicIP.png)

#### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
A VPN-kapcsolaton keresztül továbbított adatok ellenőrzéséhez szükséges adatokat küldeni és fogadni a verem Azure virtuális hálózatot a virtuális gépek. Most hozzon létre egy virtuális gépet, és tegye a Virtuálisgép-alhálózatot a virtuális hálózat.

1. Az Azure-verem felhasználói portálon kattintson **új**.
2. A piactér menüjében válassza a **Virtuális gépek** lehetőséget.
3. A virtuálisgép-lemezképeket, jelölje ki a **Windows Server 2016 Datacenter próbaverzió** rendszerképet, kattintson a **létrehozása**.
4. Az a **alapjai** ebben a szakaszban a **neve** mező típusa **VM01**.
5. Írjon be érvényes felhasználónevet és jelszót. Ezt a fiókot fogja használni a virtuális gépre történő bejelentkezéshez a létrehozását követően.
6. Adjon meg egy **előfizetés**, **erőforráscsoport**, és **hely** majd **OK**.
7. Az a **mérete** a virtuális gép méretét, az adott példány szakaszban, majd kattintson a gombra **válasszon**.
8. Az a **beállítások** szakaszban elfogadhatja az alapértelmezett beállításokat. De a kiválasztott virtuális hálózat nem biztosítására **Tenant1VNet1** , és az alhálózati **10.1.1.0/24**. Kattintson az **OK** gombra.
9. Tekintse át a beállításokat a a **összegzés** szakaszt, és kattintson **OK**.

Az egyes bérlők virtuális hálózatot, amelyhez csatlakozni, ismételje meg az előző lépést a **hozza létre a virtuális hálózatot és Virtuálisgép-alhálózat** keresztül **hozzon létre egy virtuális gépet** szakaszok.

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>A NAT-virtuális gép átjáró egyesítésre alkalmas konfigurálása
> [!IMPORTANT]
> Ez a szakasz csak Azure verem szoftverfejlesztői készlet telepítésekhez van. A NAT nincs szükség több csomópontos rendszerekhez.

Az Azure verem szoftverfejlesztői készlet önálló és elkülönül a hálózaton, amelyek a fizikai gazdagépen van telepítve. Igen a "External" VIP-hálózati átjáró csatlakozó nincs külső, de ehelyett rejtett történt a hálózati címfordítás (NAT) útválasztó mögött.
 
Az útválasztó egy Windows Server virtuális gép (**AzS-BGPNAT01**) az Azure verem szoftverfejlesztői készlet infrastruktúrában működő az Útválasztás és távelérés szolgáltatás (RRAS) szerepkör. NAT kell konfigurálnia a AzS-BGPNAT01 virtuális gépen elhelyezve lehetővé teheti a a pont-pont VPN-kapcsolat mindkét végén csatlakozni.

#### <a name="configure-the-nat"></a>A NAT konfigurálása

1. Jelentkezzen be az Azure-verem fizikai gép a rendszergazdai fiókjával.
2. Másolja, és szerkessze a következő PowerShell-parancsfájlt, és futtassa egy rendszergazda jogú Windows PowerShell ISE. Cserélje le a rendszergazdai jelszót. A cím érkezett a *külső BGPNAT cím*.

   ```
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```
4. A NAT konfigurálásához másolja, és szerkessze a következő PowerShell-parancsfájlt, és futtassa egy rendszergazda jogú Windows PowerShell ISE. Lecseréli a parancsfájl szerkesztése a *külső BGPNAT cím* és *belső IP-cím* (amely a korábban feljegyzett a **hozható létre a kapcsolat** szakaszban).

   Példa diagramok a *külső BGPNAT cím* 10.10.0.62 van, és a *belső IP-cím* 192.168.102.1 van.

   ```
   $ExtBgpNat = '<External BGPNAT address>'
   $IntBgpNat = '<Internal IP address>'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

## <a name="configure-azure"></a>Azure konfigurálása
Most, hogy az Azure-verem konfigurációja befejeződött, telepítheti az egyes Azure-erőforrások. Az alábbi ábrán látható egy minta bérlői virtuális hálózatot az Azure-ban. A virtuális hálózat az Azure-ban minden name és a címzési séma használható. Azonban az Azure-ban és Azure verem Vnetek címtartományát kell egyedinek lennie, és nem lehetnek átfedésben.

![Az Azure Vnetekhez](media/azure-stack-connect-expressroute/AzureArchitecture.png)

**3. ábra**

A központi telepítése az Azure-erőforrások Azure verem forrásokkal hasonlóak. Ehhez hasonlóan telepít:
* Virtuális hálózatok és alhálózatok
* Egy átjáró-alhálózatot
* A virtuális hálózati átjáró
* A kapcsolat
* Egy ExpressRoute-kapcsolatcsoportot

A példa Azure hálózati infrastruktúra úgy van beállítva, az alábbi módon:
* Egy szabványos (192.168.2.0/24) küllős (10.100.0.0./16) virtuális hálózatot modellhez szolgál.
* A munkaterhelések vannak telepítve a virtuális hálózat küllős, és az ExpressRoute-kapcsolatcsoport szeretne az elosztóhoz VNet van csatlakoztatva.
* A két Vnetek kapcsolódnak a Vnetben társviszony-létesítési funkció használata.

### <a name="configure-vnets"></a>Vnetek konfigurálása
1. Jelentkezzen be Azure hitelesítő adatait az Azure-portálon.
2. A virtuális hálózat használatával a 192.168.2.0/24 címtér hub létrehozása. Hozzon létre egy alhálózatot a 192.168.2.0/25 címtartomány használatával, és adja hozzá egy átjáró-alhálózatot a 192.168.2.128/27 címtartomány segítségével.
3. Hozzon létre a küllős VNet és alhálózat használatával a 10.100.0.0/16-címtartományt.


Az Azure-ban virtuális hálózatok létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy virtuális hálózatot](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

### <a name="configure-an-expressroute-circuit"></a>Egy ExpressRoute-kapcsolatcsoportot konfigurálása

1. Tekintse át a ExpressRoute Előfeltételek [ExpressRoute Előfeltételek & ellenőrzőlista](../expressroute/expressroute-prerequisites.md).
2. Kövesse a [létrehozása és módosítása az ExpressRoute-kapcsolatcsoportot](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) használata az Azure-előfizetéshez ExpressRoute-kapcsolatcsoportot létrehozni.
3. A szolgáltatás kulcs az előző lépésben megosztása a tárhelyszolgáltató vagy szolgáltatója kiépíteni az ExpressRoute-kapcsolatcsoportot a végén.
4. Kövesse a [létrehozása és módosítása az ExpressRoute-kör társviszony](../expressroute/expressroute-howto-routing-portal-resource-manager.md) konfigurálása a magánhálózati társviszony-létesítés az ExpressRoute körön.

### <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása

* Kövesse a [a virtuális hálózati átjáró konfigurálása a PowerShell használatával ExpressRoute](../expressroute/expressroute-howto-add-gateway-resource-manager.md) ExpressRoute a virtuális hálózati átjáró létrehozása a virtuális hálózat központban.

### <a name="create-the-connection"></a>A kapcsolat létrehozása

* Az ExpressRoute-kapcsolatcsoport csatolása a virtuális hálózat hub, kövesse a [csatlakozzon a virtuális hálózati ExpressRoute-kapcsolatcsoportot](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).

### <a name="peer-the-vnets"></a>A Vnetek partnert

* A lépések a Hub és küllő Vnetek partnert [hozzon létre egy virtuális hálózati társviszony-létesítést az Azure portál használatával](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md). Ha hálózatokból konfigurálja, győződjön meg arról, válassza a következő beállításokat:
   * A küllős hubot: **átjáró átvitel engedélyezése**
   * A küllős hubhoz: **távoli átjáró használata**

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

* A munkaterhelési virtuális gépek üzembe helyezés a küllős virtuális hálózat.

Ismételje meg ezeket a lépéseket minden további bérlői Vnetek, amelyhez csatlakozni az Azure-ban a megfelelő ExpressRoute-Kapcsolatcsoportok keresztül.

## <a name="configure-the-router"></a>Az útválasztó konfigurálása

A következő végpont infrastruktúra diagramja segítségével útmutató az ExpressRoute-útválasztó konfigurációját. Két bérlő (bérlői 1 és bérlői 2) és a megfelelő Expressroute-Kapcsolatcsoportok látható. Minden kapcsolódó saját VRF (virtuális útválasztási és továbbító) a LAN és WAN oldal az ExpressRoute-útválasztó a végpont elszigetelés érdekében a két bérlők között. Megjegyzés: az IP-címeit az útválasztó felületei a mintakonfiguráció végrehajtásával.

![Diagram befejezéséhez vége](media/azure-stack-connect-expressroute/EndToEnd.png)

**4. ábra**

Bármely útválasztó, amely támogatja az IKEv2 VPN és BGP a telephelyek közötti VPN-kapcsolat az Azure-verem használható. Az azonos útválasztó Azure ExpressRoute-kapcsolatcsoportot használatával való kapcsolódáshoz használatos. 

Íme egy minta-konfiguráció egy Cisco ASR 1000, amely támogatja a hálózati infrastruktúra, a 4. ábrán látható:

```
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2 
description IKEv2 proposal for Tenant 1 
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2 
description IKEv2 proposal for Tenant 2 
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2 
description IKEv2 Policy for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2 
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256 
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256 
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2 
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2 
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2 
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF 
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF 
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>A kapcsolat tesztelése

Tesztelje a kapcsolatot a pont-pont kapcsolat és az ExpressRoute-kapcsolatcsoport létrehozása után. Ez a feladat felettébb egyszerű.  Jelentkezzen be az Azure virtuális hálózat létrehozott virtuális gépek közül, és Pingelje meg a virtuális gép létrehozta az Azure-verem környezetben, vagy fordítva. 

Győződjön meg arról, amelyről üzenetet küld a forgalmat a pont-pont és az ExpressRoute-kapcsolatot, a dedikált IP-címet (DIP) címet a virtuális gép is végénél és nem a virtuális gép virtuális IP-cím kell pingelést. Igen keresse meg és jegyezze fel a cím, a kapcsolat másik végén.

### <a name="allow-icmp-in-through-the-firewall"></a>Az ICMP engedélyezése a tűzfalon keresztül
Alapértelmezés szerint a Windows Server 2016 nem engedélyezi a tűzfalon az ICMP-csomagokat. Igen minden virtuális gép használata a teszt esetén futtassa egy emelt szintű PowerShell-ablakban a következő parancsmagot:


   ```
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="ping-the-azure-stack-virtual-machine"></a>Pingelni a veremben Azure virtuális gép

1. Jelentkezzen be a Azure verem felhasználói portál egy bérlői fiók használatával.
2. Kattintson a bal oldali navigációs sávban lévő **Virtuális gépek** elemre.
3. Keresse meg a korábban létrehozott virtuális gépet, és kattintson rá.
4. A szakasz a virtuális gépet, kattintson a **Connect**.
5. Nyisson meg egy rendszergazda jogú PowerShell ablakot, és írja be **ipconfig/all**.
6. A kimenet az IPv4-cím található, és jegyezze fel azt. Pingelje meg ezt a címet a virtuális gép mentése az Azure virtuális hálózatot. A példa környezetben a cím a 10.1.1.x/24 alhálózati származik. A környezetben a cím eltérő lehet. Azonban a bérlői hálózatok alhálózat létrehozott alhálózatban kell tenni.


### <a name="view-data-transfer-statistics"></a>Nézet adatok zónaátviteli statisztikák

Ha meg szeretné ismerni a forgalom, hogy a kapcsolaton keresztül, az Azure-verem felhasználói portálon ezt az információt a kapcsolat szakaszban találja. Ez az információ is győződjön meg arról, hogy az imént a telefonjára küldött ping ténylegesen a végrehajtás során a VPN- és a ExpressRoute-kapcsolatot egy másik módja.

1. Jelentkezzen be a Microsoft Azure verem felhasználói portálra a bérlői fiók használatával.
2. Nyissa meg az erőforráscsoportot, amelyen létrehozták a VPN-átjárót, és válassza ki a **kapcsolatok** objektumtípusra.
3. Kattintson a **ConnectToAzure** kapcsolatot a listájában.
4. Az a **kapcsolat** területen látható statisztikája **adatok** és **kimenő adatforgalmat**. Néhány nem nulla értéket kell látnia itt.

   ![A kimenő adatforgalmat adatok](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>További lépések
[Alkalmazások telepítése Azure és az Azure verem](azure-stack-solution-pipeline.md)
