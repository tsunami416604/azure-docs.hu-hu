---
title: 'Oktatóanyag: Az Azure Tűzfal telepítése és konfigurálása hibrid hálózatban az Azure Portal használatával'
description: Ebben az oktatóanyagban megtudhatja, hogyan telepítheti és konfigurálhatja az Azure Firewall-t az Azure Portal használatával.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 03/24/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 208a7a677bdf0b76ffed83e679c6f1ff3041d50d
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239688"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-the-azure-portal"></a>Oktatóanyag: Az Azure Tűzfal telepítése és konfigurálása hibrid hálózatban az Azure Portal használatával

Amikor csatlakoztatja a helyszíni hálózatot egy Azure virtuális hálózathoz egy hibrid hálózat létrehozásához, az Azure hálózati erőforrásokhoz való hozzáférés szabályozásának képessége fontos része egy átfogó biztonsági tervnek.

Az Azure Firewall segítségével szabályozhatja a hálózati hozzáférést egy hibrid hálózatban az engedélyezett és elutasított hálózati forgalmat meghatározó szabályok használatával.

Ebben az oktatóanyagban három virtuális hálózatot hoz létre:

- **VNet-Hub** – a tűzfal ebben a virtuális hálózatban található.
- **VNet-Küllős** – a küllővirtuális hálózat az Azure-ban található számítási feladatokat jelöli.
- **VNet-Onprem** – A helyszíni virtuális hálózat egy helyszíni hálózatot képvisel. Egy tényleges központi telepítésben VPN- vagy ExpressRoute-kapcsolattal is csatlakoztatható. Az egyszerűség kedvéért ez az oktatóanyag VPN-átjárókapcsolatot használ, és egy Azure-ban található virtuális hálózatot használ a helyszíni hálózat ábrázolására.

![Tűzfal a hibrid hálózatban](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Változók deklarálása
> * A tűzfalközpont virtuális hálózatának létrehozása
> * A küllővirtuális hálózat létrehozása
> * A helyszíni virtuális hálózat létrehozása
> * A tűzfal konfigurálása és üzembe helyezése
> * A VPN-átjárók létrehozása és csatlakoztatása
> * Társa a hub és a küllővirtuális hálózatok
> * Az útvonalak létrehozása
> * A virtuális gépek létrehozása
> * A tűzfal tesztelése

Ha ehelyett az Azure PowerShellt szeretné használni az eljárás végrehajtásához, [olvassa el az Azure Firewall üzembe helyezése és konfigurálása hibrid hálózatban az Azure PowerShell használatával című témakört.](tutorial-hybrid-ps.md)

## <a name="prerequisites"></a>Előfeltételek

A hibrid hálózat a hub-and-küllős architektúra modell segítségével irányítja a forgalmat az Azure virtuális hálózatok és a helyszíni hálózatok között. A küllős architektúra a következő követelményekkel rendelkezik:

- Állítsa **be az AllowGatewayTransit-ot** a Virtuálishálózati-hub és a Virtuálishálózat-küllő koncett koncettkapcsolatakor. A küllős hálózati architektúrában az átjáró-átvitel lehetővé teszi, hogy a küllővirtuális hálózatok megosszák a VPN-átjárót a hubon, ahelyett, hogy vpn-átjárókat helyezne üzembe minden küllővirtuális hálózatban. 

   Ezenkívül az átjáróhoz csatlakoztatott virtuális hálózatokhoz vagy helyszíni hálózatokhoz vezető útvonalak automatikusan továbbítódnak a társviszonyba helyezett virtuális hálózatok útválasztási tábláihoz az átjáró-átvitel használatával. További információ: [VpN-átjáró átvitelkonfigurálása a virtuális hálózati társviszony-létesítéshez.](../vpn-gateway/vpn-gateway-peering-gateway-transit.md)

- Állítsa be **a UseRemoteGateways,** ha társ-virtuális hálózat küllős a Virtuálishálózat-hub. Ha **a UseRemoteGateways** be van állítva, és az **AllowGatewayTransit** a távoli társviszony-létesítésen is be van állítva, a küllős virtuális hálózat a távoli virtuális hálózat átjáróit használja az átvitelhez.
- A küllős alhálózati forgalom központi tűzfalon keresztültörténő irányításához olyan felhasználó által definiált útvonalra (UDR) van szükség, amely a **virtuális hálózati átjáró útvonalának propagálási** beállításával rendelkező tűzfalra mutat. A **virtuális hálózati átjáró útvonalpropagálásletiltva** beállítás megakadályozza az útvonal-terjesztést a küllőalhálózatokhoz. Ez megakadályozza, hogy a megtanult útvonalak ütközzenek az UDR-rel.
- Konfiguráljon egy UDR-t a központi átjáró alhálózaton, amely a tűzfal IP-címére mutat a küllős hálózatok következő ugrásaként. Nincs szükség UDR-re az Azure Tűzfal alhálózaton, mivel a BGP-től tanul útvonalakat.

Az útvonalak létrehozásával kapcsolatos információkért lásd az oktatóanyag [Útvonalak létrehozása](#create-the-routes) című szakaszát.

>[!NOTE]
>Az Azure tűzfalnak közvetlen internetkapcsolattal kell rendelkeznie. Ha az AzureFirewallSubnet megtanulja az alapértelmezett útvonalat a helyszíni hálózatra a BGP-n keresztül, felül kell bírnia ezt egy 0.0.0.0/0 UDR-rel, amelyen a **NextHopType** érték **internetként** van beállítva a közvetlen internetkapcsolat fenntartásához.
>
>Az Azure Firewall konfigurálható a kényszerített bújtatás támogatására. További információ: [Azure Firewall kényszerített bújtatás.](forced-tunneling.md)

>[!NOTE]
>A közvetlenül társviszonyba vett virtuális hálózatok közötti forgalom közvetlenül akkor is közvetlenül továbbítható, ha az UDR az Azure Tűzfalra mutat alapértelmezett átjáróként. Ebben az esetben az alhálózat nak a tűzfalnak való elküldéséhez az UDR-nek mindkét alhálózaton kifejezetten tartalmaznia kell a cél alhálózati hálózati előtagot.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-the-firewall-hub-virtual-network"></a>A tűzfalközpont virtuális hálózatának létrehozása

Először hozza létre az erőforráscsoportot az oktatóanyag erőforrásainak tárolására:

1. Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .
2. Az Azure Portal kezdőlapján válassza az **Erőforráscsoportok** > **hozzáadása lehetőséget.**
3. Az **Erőforráscsoport nevéhez**írja be az **FW-Hybrid-Test nevet.**
4. Az **Előfizetés** beállításnál válassza ki az előfizetését.
5. A **Régió**területen válassza **az USA keleti régiója**lehetőséget. A később létrehozott összes erőforrásnak ugyanazon a helyen kell lennie.
6. Válassza **a Véleményezés + Létrehozás lehetőséget.**
7. Kattintson a **Létrehozás** gombra.

Most hozza létre a virtuális hálózatot:

> [!NOTE]
> Az AzureFirewallSubnet alhálózat mérete /26. Az alhálózat méretéről az [Azure Firewall gyIK](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size)című témakörben talál további információt.

1. Az Azure Portal kezdőlapján válassza az **Erőforrás létrehozása lehetőséget.**
2. A **Hálózat csoportban**válassza a **Virtuális hálózat**lehetőséget.
4. A **Name mezőbe**írja be a **VNet-hub**nevet.
5. A **Címmező**mezőbe írja be a **10.5.0.0/16 mezőt.**
6. Az **Előfizetés** beállításnál válassza ki az előfizetését.
7. Az **Erőforráscsoport csoportban**válassza **az FW-Hybrid-Test lehetőséget.**
8. A **Hely területen**válassza az USA keleti **része**lehetőséget.
9. Az **Alhálózat** területen a **Név** mezőbe írja be a következőt: **AzureFirewallSubnet**. Ezen az alhálózaton lesz a tűzfal. Az alhálózat neve **kizárólag** AzureFirewallSubnet lehet.
10. A **Címtartomány**mezőbe írja be a **10.5.0.0/26 mezőt.** 
11. Fogadja el a többi alapértelmezett beállítást, és válassza a **Létrehozás gombot.**

## <a name="create-the-spoke-virtual-network"></a>A küllővirtuális hálózat létrehozása

1. Az Azure Portal kezdőlapján válassza az **Erőforrás létrehozása lehetőséget.**
2. A **Hálózat csoportban**válassza a **Virtuális hálózat**lehetőséget.
4. A **Name**mezőbe írja be a **VNet-küllős**nevet.
5. A **Címmező**mezőbe írja be a **10.6.0.0/16 mezőt.**
6. Az **Előfizetés** beállításnál válassza ki az előfizetését.
7. Az **Erőforráscsoport csoportban**válassza **az FW-Hybrid-Test lehetőséget.**
8. A **Hely** elemnél válassza a korábban használt helyet.
9. Az **Alhálózat** területen a **Név** mezőbe írja be a következőt: **SN-Workload**.
10. A **Címtartomány**mezőbe írja be a **10.6.0.0/24 mezőt.**
11. Fogadja el a többi alapértelmezett beállítást, és válassza a **Létrehozás gombot.**

## <a name="create-the-on-premises-virtual-network"></a>A helyszíni virtuális hálózat létrehozása

1. Az Azure Portal kezdőlapján válassza az **Erőforrás létrehozása lehetőséget.**
2. A **Hálózat csoportban**válassza a **Virtuális hálózat**lehetőséget.
4. A **Név mezőbe**írja be a **VNet-OnPrem**nevet.
5. A **Címtér** mezőbe írja be a következőt: **192.168.0.0/16**.
6. Az **Előfizetés** beállításnál válassza ki az előfizetését.
7. Az **Erőforráscsoport csoportban**válassza **az FW-Hybrid-Test lehetőséget.**
8. A **Hely** elemnél válassza a korábban használt helyet.
9. Az **Alhálózat**csoportban az **SN-Corp** **névtípusban.**
10. A **Címtartomány** mezőbe írja be a következőt: **192.168.1.0/24**.
11. Fogadja el a többi alapértelmezett beállítást, és válassza a **Létrehozás gombot.**

Most hozzon létre egy második alhálózatot az átjáróhoz.

1. A **VNet-Onprem** lapon válassza **az Alhálózatok**lehetőséget.
2. Válassza a **+Alhálózat**lehetőséget.
3. A **Name mezőbe**írja be a **GatewaySubnet (Átjáróalnet ) mezőbe.**
4. **A Címtartomány (CIDR blokk)** **192.168.2.0/24**típushoz.
5. Válassza **az OK gombot.**

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Ez a helyszíni átjáró nyilvános IP-címe.

1. Az Azure Portal kezdőlapján válassza az **Erőforrás létrehozása lehetőséget.**
2. A keresőmezőbe írja be a **nyilvános IP-címet,** és nyomja **le az Enter billentyűt.**
3. Válassza **a Nyilvános IP-cím,** majd a **Létrehozás**lehetőséget.
4. A névhez írja be a **VNet-Onprem-GW-pip**nevet.
5. Az erőforráscsoporthoz írja be az **FW-Hybrid-Test ( FW-Hybrid-Test ) című szöveget.**
6. A **Hely** elemnél válassza a korábban használt helyet.
7. Fogadja el a többi alapértelmezést, majd válassza a **Létrehozás gombot.**

## <a name="configure-and-deploy-the-firewall"></a>A tűzfal konfigurálása és üzembe helyezése

Most telepítse a tűzfalat a tűzfal hub virtuális hálózatra.

1. Az Azure Portal kezdőlapján válassza az **Erőforrás létrehozása lehetőséget.**
2. A bal oldali oszlopban válassza a **Hálózat**lehetőséget, majd a **Tűzfal**lehetőséget.
4. A **Tűzfal létrehozása** oldalon konfigurálja a tűzfalat a következő táblázatban található értékekkel:

   |Beállítás  |Érték  |
   |---------|---------|
   |Előfizetés     |\<az Ön előfizetése\>|
   |Erőforráscsoport     |**FW-hibrid-teszt** |
   |Név     |**AzFW01 között**|
   |Hely     |Válassza a korábban használt helyet|
   |Válasszon egy virtuális hálózatot     |**Meglévő használata**:<br> **Virtuális hálózat-központ**|
   |Nyilvános IP-cím     |Create new (Új létrehozása): <br>**Név** - **fw-pip**. |

5. Válassza az **Áttekintés + létrehozás** lehetőséget.
6. Tekintse át az összegzést, majd a **Létrehozás gombra** a tűzfal létrehozásához.

   Ez néhány percet vesz igénybe.
7. A telepítés befejezése után nyissa meg az **FW-Hybrid-Test** erőforráscsoportot, és válassza az **AzFW01** tűzfalat.
8. Jegyezze fel a magánhálózati IP-címet. Később, az alapértelmezett útvonal létrehozásakor szükség lesz rá.

### <a name="configure-network-rules"></a>Hálózati szabályok konfigurálása

Először adjon hozzá egy hálózati szabályt a webes forgalom engedélyezéséhez.

1. Az **AzFW01** lapon válassza ki a **Szabályok lehetőséget.**
2. Válassza a **Hálózati szabály gyűjtemény** lapot.
3. Válassza **a Hálózati szabálygyűjtemény hozzáadása**lehetőséget.
4. A **Name**mezőbe írja be az **RCNet01 nevet.**
5. Az **Elsőbbség**mezőbe írja be a **100-as mezőt.**
6. A **Művelet** beállításnál válassza az **Engedélyezés** lehetőséget.
6. A **Szabályok csoportban**a **Name (Név)** mezőbe írja be az **AllowWeb (AllowWeb ) (AllowWeb**) mezőbe.
7. A **Protokoll** beállításnál válassza a **TCP** lehetőséget.
8. A **Forrástípus mezőben**válassza az **IP-cím**lehetőséget.
9. A **Forrás**mezőbe írja be a **192.168.1.0/24 mezőt.**
10. A **Célcím**mezőbe írja be a **10.6.0.0/16**
11. A **célportok**esetén írja be a **80-as t.**

Most adjon hozzá egy szabályt az RDP-forgalom engedélyezéséhez.

A második szabálysorra írja be a következő adatokat:

1. **Name**( Name ) mezőbe írja be **az AllowRDP**nevet.
2. A **Protokoll** beállításnál válassza a **TCP** lehetőséget.
3. A **Forrástípus mezőben**válassza az **IP-cím**lehetőséget.
4. A **Forrás**mezőbe írja be a **192.168.1.0/24 mezőt.**
5. A **Célcím**mezőbe írja be a **10.6.0.0/16**
6. Célportok esetén írja be a 3389.for destination **ports (Célportok)** mezőbe a **3389.**
7. Válassza a **Hozzáadás** lehetőséget.

## <a name="create-and-connect-the-vpn-gateways"></a>A VPN-átjárók létrehozása és csatlakoztatása

A hub és a helyszíni virtuális hálózatok VPN-átjárókon keresztül csatlakoznak.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>VPN-átjáró létrehozása a központi virtuális hálózathoz

Most hozza létre a VPN-átjárót a központi virtuális hálózathoz. A hálózat-hálózat konfigurációkhoz RouteBased VpnType szükséges. A VPN-átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró kiválasztott termékváltozatától függően.

1. Az Azure Portal kezdőlapján válassza az **Erőforrás létrehozása lehetőséget.**
2. A keresőmezőbe írja be a **virtuális hálózati átjárót,** és nyomja **le az Enter billentyűt.**
3. Válassza **a Virtuális hálózati átjáró**lehetőséget, majd a Create **(Létrehozás) lehetőséget.**
4. A **Név mezőbe**írja be a **GW-hub**nevet.
5. A **Régió**területen válassza ki ugyanazt a régiót, amelyet korábban használt.
6. Az **Átjárótípus mezőben**válassza a **VPN**lehetőséget.
7. A **VPN-típushoz**válassza **az Útvonal alapú**lehetőséget.
8. A **Termékváltozatban**válassza az **Alapszintű**lehetőséget.
9. **Virtuális hálózat esetén**válassza a **VNet-hub**lehetőséget.
10. Nyilvános **IP-cím**esetén válassza **az Új létrehozása**lehetőséget, és írja be a **vnet-hub-GW-pip** nevet a névhez.
11. Fogadja el a fennmaradó alapértelmezett értékeket, majd válassza **a Véleményezés + create**lehetőséget.
12. Tekintse át a konfigurációt, majd válassza a **Létrehozás gombot.**

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>VPN-átjáró létrehozása a helyszíni virtuális hálózathoz

Most hozza létre a VPN-átjárót a helyszíni virtuális hálózathoz. A hálózat-hálózat konfigurációkhoz RouteBased VpnType szükséges. A VPN-átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró kiválasztott termékváltozatától függően.

1. Az Azure Portal kezdőlapján válassza az **Erőforrás létrehozása lehetőséget.**
2. A keresőmezőbe írja be a **virtuális hálózati átjárót,** és nyomja **le az Enter billentyűt.**
3. Válassza **a Virtuális hálózati átjáró**lehetőséget, majd a Create **(Létrehozás) lehetőséget.**
4. A **Név mezőbe**írja be a **GW-Onprem**nevet.
5. A **Régió**területen válassza ki ugyanazt a régiót, amelyet korábban használt.
6. Az **Átjárótípus mezőben**válassza a **VPN**lehetőséget.
7. A **VPN-típushoz**válassza **az Útvonal alapú**lehetőséget.
8. A **Termékváltozatban**válassza az **Alapszintű**lehetőséget.
9. **Virtuális hálózat esetén**válassza a **VNet-Onprem**lehetőséget.
10. Nyilvános **IP-cím**esetén válassza **az Új létrehozása**lehetőséget, és írja be a **VNet-Onprem-GW-pip** nevet a névhez.
11. Fogadja el a fennmaradó alapértelmezett értékeket, majd válassza **a Véleményezés + create**lehetőséget.
12. Tekintse át a konfigurációt, majd válassza a **Létrehozás gombot.**

### <a name="create-the-vpn-connections"></a>A VPN-kapcsolatok létrehozása

Most már létrehozhatja a VPN-kapcsolatokat a hub és a helyszíni átjárók között.

Ebben a lépésben hozza létre a kapcsolatot a központi virtuális hálózatról a helyszíni virtuális hálózathoz. A példák egy megosztott kulcsra is hivatkoznak. A megosztott kulcshoz használhatja a saját értékeit. Fontos, hogy a megosztott kulcs azonos legyen mindkét kapcsolathoz. A kapcsolat létrehozása egy kis időt vehet igénybe.

1. Nyissa meg az **FW-Hybrid-Test** erőforráscsoportot, és válassza ki a **GW-hub** átjárót.
2. A bal oldali oszlopban válassza a **Kapcsolatok** lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget.
4. A kapcsolat neve, írja be **a Hub-to-Onprem**nevet.
5. Válassza **a Virtuálishálózat és virtuális hálózat** lehetőséget a Kapcsolat **típusához.**
6. A **második virtuális hálózati átjáróhoz**válassza a **GW-Onprem**lehetőséget.
7. Megosztott **kulcs (PSK)** esetén írja be az **AzureA1b2C3 (AzureA1b2C3) típust.**
8. Válassza **az OK gombot.**

Hozza létre a helyszíni virtuális hálózati kapcsolat központi. Ez a lépés hasonló az előzőhez, kivéve, hogy a kapcsolatot a VNet-Onprem és a VNet-hub között hozza létre. Ügyeljen arra, hogy a megosztott kulcsok megegyezzenek. A kapcsolat néhány perc alatt létrejön.

1. Nyissa meg az **FW-Hybrid-Test** erőforráscsoportot, és válassza ki a **GW-Onprem** átjárót.
2. A bal oldali oszlopban válassza a **Kapcsolatok** lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget.
4. A kapcsolat neve, írja **Onprem-hub**.
5. Válassza **a Virtuálishálózat és virtuális hálózat** lehetőséget a Kapcsolat **típusához.**
6. A **második virtuális hálózati átjáróhoz**válassza a **GW-hub**lehetőséget.
7. Megosztott **kulcs (PSK)** esetén írja be az **AzureA1b2C3 (AzureA1b2C3) típust.**
8. Válassza **az OK gombot.**


#### <a name="verify-the-connection"></a>A kapcsolat ellenőrzése

Körülbelül öt perc múlva mindkét kapcsolat állapota **Csatlakoztatva**legyen.

![Átjárókapcsolatok](media/tutorial-hybrid-portal/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Társa a hub és a küllővirtuális hálózatok

Most peer a hub és a küllővirtuális hálózatok.

1. Nyissa meg az **FW-Hybrid-Test** erőforráscsoportot, és válassza ki a **Virtuális virtuális virtuális virtuális** hálózatot.
2. A bal oldali oszlopban válassza a **Társviszony-létesítések**lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget.
4. A **Name mezőbe**írja be a HubtoSpoke ( **HubtoSpoke**.
5. A **virtuális hálózathoz**válassza a **VNet-küllős**
6. A vNetSpoke és a VNet-hub társviszony-létesítésének nevéhez írja be a **SpoketoHub**nevet.
7. Válassza **az Átjáró átvitelének engedélyezése**lehetőséget.
8. Válassza **az OK gombot.**

### <a name="configure-additional-settings-for-the-spoketohub-peering"></a>A SpoketoHub-társviszony-létesítés további beállításainak konfigurálása

Engedélyeznie kell a **továbbított forgalom engedélyezése** a SpoketoHub-társviszony-létesítés.

1. Nyissa meg az **FW-Hybrid-Test** erőforráscsoportot, és válassza ki a **virtuális** virtuális virtuális hálózatot.
2. A bal oldali oszlopban válassza a **Társviszony-létesítések**lehetőséget.
3. Válassza ki a SpoketoHub-társviszony-létesítést. **SpoketoHub**
4. A **Továbbítás isztifikált forgalom engedélyezése a virtuális hálózatról a VNet-küllőre**csoportban válassza **az Engedélyezve**lehetőséget.
5. Kattintson a **Mentés** gombra.

## <a name="create-the-routes"></a>Az útvonalak létrehozása

Ezután hozzon létre néhány útvonalat:

- Egy útvonalat a központi átjáró alhálózatától a küllő alhálózatához a tűzfal IP-címén keresztül
- Egy alapértelmezett útvonalat a küllő alhálózattól a tűzfal IP-címén keresztül

1. Az Azure Portal kezdőlapján válassza az **Erőforrás létrehozása lehetőséget.**
2. A keresőmezőbe írja be az **útvonaltáblázatot,** és nyomja **le az Enter billentyűt.**
3. Válassza **az Útvonal tábla lehetőséget**.
4. Kattintson a **Létrehozás** gombra.
5. A névhez írja be az **UDR-Hub-Spoke**nevet.
6. Válassza ki az **FW-hibrid-teszt** az erőforráscsoport.
8. A **Hely** elemnél válassza a korábban használt helyet.
9. Kattintson a **Létrehozás** gombra.
10. Az útvonaltábla létrehozása után jelölje ki az útvonaltábla-lap megnyitásához.
11. Válassza az **Útvonalak** lehetőséget a bal oldali oszlopban.
12. Válassza a **Hozzáadás** lehetőséget.
13. Az útvonal nevéhez írja be a **ToSpoke**nevet.
14. A címelőtaghoz írja be a **10.6.0.0/16**-ot.
15. A következő ugrástípushoz válassza a **Virtuális berendezés lehetőséget.**
16. A következő ugrási címhez írja be a tűzfal korábban megadott privát IP-címét.
17. Válassza **az OK gombot.**

Most társítsa az útvonalat az alhálózathoz.

1. Az **UDR-Hub-Spoke - Routes (Útvonalak)** lapon válassza **az Alhálózatok**lehetőséget.
2. Válassza **a Társítás**lehetőséget.
3. Válassza **a Virtuális hálózat kiválasztása**lehetőséget.
4. Válassza **a VNet-hub**lehetőséget.
5. Válassza **a GatewaySubnet**lehetőséget.
6. Válassza **az OK gombot.**

Most hozza létre az alapértelmezett útvonalat a küllős alhálózatból.

1. Az Azure Portal kezdőlapján válassza az **Erőforrás létrehozása lehetőséget.**
2. A keresőmezőbe írja be az **útvonaltáblázatot,** és nyomja **le az Enter billentyűt.**
3. Válassza **az Útvonal tábla lehetőséget**.
5. Kattintson a **Létrehozás** gombra.
6. A névhez írja be az **UDR-DG nevet.**
7. Válassza ki az **FW-hibrid-teszt** az erőforráscsoport.
8. A **Hely** elemnél válassza a korábban használt helyet.
4. **A Virtuális hálózati átjáró útvonalának propagálása**esetén válassza a **Letiltva**lehetőséget.
1. Kattintson a **Létrehozás** gombra.
2. Az útvonaltábla létrehozása után jelölje ki az útvonaltábla-lap megnyitásához.
3. Válassza az **Útvonalak** lehetőséget a bal oldali oszlopban.
4. Válassza a **Hozzáadás** lehetőséget.
5. Az útvonal nevéhez írja be a **ToHub**nevet.
6. A címelőtaghoz írja be a **0.0.0.0/0-t.**
7. A következő ugrástípushoz válassza a **Virtuális berendezés lehetőséget.**
8. A következő ugrási címhez írja be a tűzfal korábban megadott privát IP-címét.
9. Válassza **az OK gombot.**

Most társítsa az útvonalat az alhálózathoz.

1. Az **UDR-DG - Routes (Útvonalak)** lapon válassza **az Alhálózatok**lehetőséget.
2. Válassza **a Társítás**lehetőséget.
3. Válassza **a Virtuális hálózat kiválasztása**lehetőséget.
4. Válassza **a VNet-küllőlehetőséget.**
5. Válassza az **SN-Workload lehetőséget.**
6. Válassza **az OK gombot.**

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Most hozza létre a küllős számítási feladatok és a helyszíni virtuális gépek, és helyezze őket a megfelelő alhálózatok.

### <a name="create-the-workload-virtual-machine"></a>A számítási feladatot futtató virtuális gép létrehozása

Hozzon létre egy virtuális gépet a küllős virtuális hálózatban, amely iis-t futtat, nyilvános IP-cím nélkül.

1. Az Azure Portal kezdőlapján válassza az **Erőforrás létrehozása lehetőséget.**
2. A **Népszerű**csoportban válassza a **Windows Server 2016 Datacenter**lehetőséget.
3. Adja meg a következő értékeket a virtuális gép számára:
    - **Erőforráscsoport** – Válassza **az FW-Hybrid-Test lehetőséget.**
    - **Virtuális gép neve**: *VM-Spoke-01*.
    - **Régió** – ugyanaz a régió, amelyet korábban használt.
    - **Felhasználónév**: *azureuser*.
    - **Jelszó**: *Azure123456!*
4. Válassza a **Tovább:Lemezek**lehetőséget.
5. Fogadja el az alapértelmezett beállításokat, és válassza **a Tovább: Hálózat lehetőséget.**
6. Válassza a virtuális hálózat **virtuális küllős ét,** és az alhálózat **SN-Workload**.
7. **Nyilvános IP esetén**válassza a **Nincs**lehetőséget.
8. **Nyilvános bejövő portok**esetén jelölje be **a Kijelölt portok engedélyezése választógombot,** majd válassza a **HTTP (80)** és **rdp (3389) lehetőséget.**
9. Válassza a **Tovább:Management lehetőséget.**
10. A **Rendszerindítási diagnosztika**esetén válassza a **Ki**lehetőséget.
11. Válassza **a Véleményezés+Létrehozás**lehetőséget, tekintse át az összesítő lap beállításait, majd válassza a **Létrehozás lehetőséget.**

### <a name="install-iis"></a>Az IIS telepítése

1. Az Azure Portalon nyissa meg a Cloud Shellt, és győződjön meg arról, hogy a **PowerShell**van beállítva.
2. Futtassa a következő parancsot az IIS virtuális gépen történő telepítéséhez, és szükség esetén módosítsa a helyet:

   ```azurepowershell-interactive
   Set-AzVMExtension `
           -ResourceGroupName FW-Hybrid-Test `
           -ExtensionName IIS `
           -VMName VM-Spoke-01 `
           -Publisher Microsoft.Compute `
           -ExtensionType CustomScriptExtension `
           -TypeHandlerVersion 1.4 `
           -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell      Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
           -Location EastUS
   ```

### <a name="create-the-on-premises-virtual-machine"></a>A helyszíni virtuális gép létrehozása

Ez egy olyan virtuális gép, amellyel a Távoli asztal használatával csatlakozhat a nyilvános IP-címhez. Innen a tűzfalon keresztül csatlakozhat a helyszíni kiszolgálóhoz.

1. Az Azure Portal kezdőlapján válassza az **Erőforrás létrehozása lehetőséget.**
2. A **Népszerű**csoportban válassza a **Windows Server 2016 Datacenter**lehetőséget.
3. Adja meg a következő értékeket a virtuális gép számára:
    - **Erőforráscsoport** – Válassza ki a meglévőt, majd válassza **az FW-Hybrid-Test lehetőséget.**
    - **Virtuális gép neve** - *VM-Onprem*.
    - **Régió** – ugyanaz a régió, amelyet korábban használt.
    - **Felhasználónév**: *azureuser*.
    - **Jelszó**: *Azure123456!*.
4. Válassza a **Tovább:Lemezek**lehetőséget.
5. Fogadja el az alapértelmezett beállításokat, és válassza **a Tovább:Hálózat lehetőséget.**
6. Válassza a Virtuális hálózat hoz a **VNet-Onprem lehetőséget,** az alhálózat pedig az **SN-Corp.**
7. **Nyilvános bejövő portok**esetén jelölje be **a Kijelölt portok engedélyezése**jelölőnégyzetet, majd válassza az **RDP (3389) lehetőséget.**
8. Válassza a **Tovább:Management lehetőséget.**
9. A **Rendszerindítási diagnosztika**esetén válassza a **Ki**lehetőséget.
10. Válassza **a Véleményezés+Létrehozás**lehetőséget, tekintse át az összesítő lap beállításait, majd válassza a **Létrehozás lehetőséget.**

## <a name="test-the-firewall"></a>A tűzfal tesztelése

1. Először vegye figyelembe a **virtuális gép küllő-01** virtuális gép privát IP-címét.

2. Az Azure Portalról csatlakozzon a **VM-Onprem** virtuális géphez.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Nyisson meg egy webböngészőt a **VM-Onprem-en,** és keresse meg http://\<VM-küllős-01 privát IP-cím.\>

   Meg kell jelennie a **VM-spoke-01** weboldal: ![VM-Spoke-01 weboldal](media/tutorial-hybrid-portal/VM-Spoke-01-web.png)

4. A **Virtuálisgép-Onprem** virtuális gépről nyisson meg egy távoli asztalt a **VM-küllő-01-re** a privát IP-címen.

   A kapcsolatnak sikeresnek kell lennie, és be kell tudnia jelentkezni.

Így most már ellenőrizte, hogy a tűzfalszabályok működnek:

<!---- You can ping the server on the spoke VNet.--->
- A küllővirtuális hálózaton böngészhet a webkiszolgáló között.
- A küllővirtuális hálózat kiszolgálójához rdp használatával csatlakozhat.

Ezután módosítsa a tűzfal hálózati szabálygyűjteményének műveletét **Deny** (Megtagadás) értékre annak ellenőrzéséhez, hogy a tűzfalszabályok a vártnak megfelelően működnek-e.

1. Válassza ki az **AzFW01** tűzfalat.
2. Válassza a **Szabályok**lehetőséget.
3. Válassza a **Hálózati szabály gyűjtemény** lapot, és válassza az **RCNet01** szabálygyűjteményt.
4. A **Művelet csoportban**válassza **a Megtagadás**lehetőséget.
5. Kattintson a **Mentés** gombra.

A módosított szabályok ellenőrzése előtt zárja be a meglévő távoli asztalokat. Most futtassa újra az ellenőrzéseket. Ezúttal mindegyiknek sikertelennek kell lennie.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tűzfalhoz kapcsolódó erőforrásokat a következő oktatóanyagban is használhatja, vagy ha már nincs rájuk szükség, törölje az **FW-Hybrid-Test** erőforráscsoportot, és vele együtt a tűzfalhoz kapcsolódó összes erőforrást.

## <a name="next-steps"></a>További lépések

A következő lépésben monitorozhatja az Azure Firewall naplóit.

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Firewall naplóinak monitorozása](./tutorial-diagnostics.md)
