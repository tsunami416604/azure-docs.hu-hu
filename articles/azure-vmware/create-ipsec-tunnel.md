---
title: IPSec-alagút létrehozása az Azure VMware-megoldásba
description: Megtudhatja, hogyan hozhat létre virtuális WAN-hubot IPSec-alagút létrehozásához az Azure VMware-megoldásokban.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 74cc31abf432954008cbb20bf64825d199732dab
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951128"
---
# <a name="create-an-ipsec-tunnel-into-azure-vmware-solution"></a>IPSec-alagút létrehozása az Azure VMware-megoldásba

Ebben a cikkben elsajátítjuk a VPN (IPsec IKEv1 és IKEv2) helyek közötti alagút leállításának lépéseit a Microsoft Azure Virtual WAN hub-ban. Létrehozunk egy Azure-beli virtuális WAN-hubot és egy VPN-átjárót, amelyhez hozzá van rendelve egy nyilvános IP-cím. Ezután létrehozunk egy Azure ExpressRoute-átjárót, és létrehozunk egy Azure VMware-megoldási végpontot. A házirend-alapú VPN helyszíni telepítésének engedélyezésével kapcsolatban is tájékozódhat. 

## <a name="topology"></a>Topológia

![VPN-helyek közötti bújtatási architektúra.](media/create-ipsec-tunnel/vpn-s2s-tunnel-architecture.png)

Az Azure-beli virtuális központ tartalmazza az Azure VMware megoldás ExpressRoute-átjáróját és a két hálózat közötti pont-pont típusú VPN-átjárót. Egy helyszíni VPN-eszközt csatlakoztat egy Azure VMware-megoldási végponttal.

## <a name="before-you-begin"></a>Előkészületek

A helyek közötti VPN-alagút létrehozásához létre kell hoznia egy nyilvános IP-címet, amely egy helyszíni VPN-eszközön leáll.

## <a name="create-a-virtual-wan-hub"></a>Virtuális WAN-központ létrehozása

1. A Azure Portal keressen rá a **virtuális WAN**-ra. Válassza a **+Hozzáadás** lehetőséget. Megnyílik a WAN létrehozása lap.  

2. Adja meg a szükséges mezőket a **WAN létrehozása** lapon, majd válassza a **felülvizsgálat + létrehozás**elemet.
   
   | Mező | Érték |
   | --- | --- |
   | **Előfizetés** | Az érték előre fel van töltve az erőforráscsoporthoz tartozó előfizetéssel. |
   | **Erőforráscsoport** | A virtuális WAN egy globális erőforrás, és nem korlátozódik egy adott régióra.  |
   | **Erőforráscsoport helye** | A virtuális WAN-központ létrehozásához meg kell adnia az erőforráscsoport helyét.  |
   | **Név** |   |
   | **Típus** | Válassza a **Standard (normál**) lehetőséget, amely több, mint a VPN Gateway-forgalmat is lehetővé teszi.  |


    :::image type="content" source="media/create-ipsec-tunnel/create-wan.png" alt-text="Hozzon létre WAN-t.":::

3. A Azure Portal válassza ki az előző lépésben létrehozott virtuális WAN-t, válassza a **virtuális központ létrehozása**elemet, adja meg a szükséges mezőket, majd válassza a **Tovább: helyről a helyre**lehetőséget. 

   | Mező | Érték |
   | --- | --- |
   | **Régió** | A régió kiválasztásához felügyeleti szempontból szükséges.  |
   | **Név** |    |
   | **Központ titkos címtartománya** | Adja meg az alhálózatot a következő használatával: `/24` (minimum).  |

    :::image type="content" source="media/create-ipsec-tunnel/create-virtual-hub.png" alt-text="Hozzon létre WAN-t.":::

4. A **helyek közötti** lapon adja meg a helyek közötti átjárót úgy, hogy az összesített átviteli sebességet az **átjáró skálázási egységei** legördülő menüből állítja be. 

   >[!TIP]
   >Egy méretezési egység = 500 Mbps. A skálázási egységek a redundancia, a 500 Mbps-t támogató párok.
  
5. A **ExpressRoute** lapon hozzon létre egy ExpressRoute-átjárót. 

   >[!TIP]
   >A méretezési egység értéke 2 GB/s. 

    Az egyes hubok létrehozása körülbelül 30 percet vesz igénybe. 

## <a name="create-a-vpn-site"></a>VPN-hely létrehozása 

1. A Azure Portal **legutóbbi erőforrásaiban** válassza ki az előző szakaszban létrehozott virtuális WAN-t.

2. A virtuális központ **áttekintésében** válassza a **kapcsolati**  >  **VPN (helyek közötti)** lehetőséget, majd válassza az **új VPN-hely létrehozása**lehetőséget.


    :::image type="content" source="media/create-ipsec-tunnel/create-vpn-site-basics.png" alt-text="Hozzon létre WAN-t.":::  
 
3. Az **alapvető beállítások** lapon adja meg a kötelező mezőket, majd válassza a **Tovább: hivatkozások**elemet. 

   | Mező | Érték |
   | --- | --- |
   | **Régió** | Az előző szakaszban megadott régió.  |
   | **Név** |  |
   | **Eszköz szállítója** |  |
   | **Border Gateway Protocol** | Az **Engedélyezés** beállítás megadásával biztosíthatja, hogy az Azure VMware-megoldás és a helyszíni kiszolgálók is meghirdessek az útvonalakat az alagúton keresztül. Ha le van tiltva, a meghirdetni kívánt alhálózatokat manuálisan kell karbantartani. Ha az alhálózatok kimaradnak, a HCX nem fogja tudni kiképezni a szolgáltatás hálóját. További információ:  [a BGP és az Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md). |
   | **Magánhálózati címterület**  | Adja meg a helyszíni CIDR blokkot.  A rendszer az összes, a helyszíni alagúton keresztüli forgalom irányítására használatos.  A CIDR blokkra csak akkor van szükség, ha nem engedélyezi a BGP-t. |
   | **Csatlakozás a következőhöz:** |   |

4. A **hivatkozások** lapon töltse ki a kötelező mezőket, és válassza a **felülvizsgálat + létrehozás**elemet. A hivatkozások és a szolgáltatók nevének megadásával megkülönböztetni a csomópontok részeként esetlegesen létrehozott átjárók számát. A BGP és az autonóm System number (ASN) egyedinek kell lennie a szervezeten belül.
 
## <a name="optional-defining-a-vpn-site-for-policy-based-vpn-site-to-site-tunnels"></a>Választható VPN-hely meghatározása házirend-alapú VPN-helyek közötti alagutakhoz

Ez a szakasz csak a házirend-alapú VPN-ek esetében érvényes. A házirend-alapú (vagy statikus, útválasztás-alapú) VPN-beállításokat a legtöbb esetben a helyszíni VPN-eszköz képességei vezérlik. Szükségük van a helyszíni és az Azure VMware megoldási hálózatokra. Azure-beli virtuális WAN-központtal rendelkező Azure VMware-megoldás esetén *egyetlen* hálózat sem választható ki. Ehelyett meg kell adnia az összes releváns helyi és Azure VMware-megoldás virtuális WAN hub-tartományát. Ezek a hub-tartományok a házirend alap VPN-alagút helyszíni végpontjának titkosítási tartományának megadására szolgálnak. Az Azure VMware megoldás oldalának csak a házirend-alapú forgalom kiválasztó jelzőjének engedélyezésére van szükség. 

1. A Azure Portal lépjen a virtuális WAN-központ webhelyére; a **kapcsolat**területen válassza a **VPN (hely helye)** lehetőséget.

2. Válassza ki a VPN-hely nevét, majd a jobb szélen lévő három pontot (...). Ezután válassza **a VPN módosítása szakaszt erre a hubhoz**.
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png" alt-text="Hozzon létre WAN-t." lightbox="media/create-ipsec-tunnel/edit-vpn-section-to-this-hub.png":::

3. Szerkessze a VPN-hely és a központ közötti kapcsolatot, majd kattintson a **Mentés**gombra.
   - Internet Protocol biztonság (IPSec), válassza az **Egyéni**lehetőséget.
   - Házirend alapú forgalom-választó használata, válassza az **Engedélyezés** lehetőséget.
   - Az **1** . és az **IKE 2. fázis (IPSec)** adatainak megadása. 
 
    :::image type="content" source="media/create-ipsec-tunnel/edit-vpn-connection.png" alt-text="Hozzon létre WAN-t."::: 
 
    A házirend-alapú titkosítási tartomány részét képező forgalmi választók vagy alhálózatok a következők:
    
    - A virtuális WAN hub/24
    - Azure VMware-megoldás saját felhő/22
    - A csatlakoztatott Azure-beli virtuális hálózat (ha van)

## <a name="connect-your-vpn-site-to-the-hub"></a>A VPN-hely összekötése a hubhoz

1. Jelölje be a VPN-hely neve melletti jelölőnégyzetet (lásd: előző **VPN-hely – hely** képernyőképe), majd válassza a **VPN-helyek összekapcsolását**. A **előmegosztott kulcs** mezőben adja meg a helyi végponthoz korábban definiált kulcsot. Ha még nem rendelkezik korábban definiált kulccsal, ezt a mezőt üresen hagyhatja, és a rendszer automatikusan létrehozza a kulcsot. 
 
    Csak akkor engedélyezze az **alapértelmezett útválasztást** , ha tűzfalat telepít a központba, és ez a következő ugrás az adott alagúton keresztüli kapcsolatokhoz.

    Kattintson a **Csatlakozás** gombra. A kapcsolat állapota képernyő megjeleníti az alagút létrehozási állapotát.

2. Nyissa meg a virtuális WAN áttekintést. Nyissa meg a VPN-hely lapot, és töltse le a VPN-konfigurációs fájlt a helyszíni végpontra való alkalmazásához.  

3. Most az Azure VMware-megoldás ExpressRoute a virtuális WAN-hubhoz fogjuk feljavítani. (Ehhez a lépéshez először létre kell hozza a saját felhőjét.)

    Nyissa meg az Azure VMware Solution Private Cloud **kapcsolati** szakaszát. A **ExpressRoute** lapon válassza a **+ engedélyezési kulcs kérése**lehetőséget. Nevezze el, majd válassza a **Létrehozás**lehetőséget. (A kulcs létrehozásához körülbelül 30 másodperc is eltelhet.) Másolja a ExpressRoute-azonosítót és az engedélyezési kulcsot. 

    :::image type="content" source="media/create-ipsec-tunnel/express-route-connectivity.png" alt-text="Hozzon létre WAN-t.":::

    > [!NOTE]
    > Az engedélyezési kulcs némi idő elteltével eltűnik, ezért másolja azt, amint megjelenik.

4. Ezután összekapcsoljuk az Azure VMware-megoldást és a VPN-átjárót a virtuális WAN-központban. A Azure Portal nyissa meg a korábban létrehozott virtuális WAN-t. Válassza ki a létrehozott virtuális WAN-hubot, majd a bal oldali panelen válassza a **ExpressRoute** lehetőséget. Válassza a **+ engedélyezési kulcs beváltása**elemet.

    :::image type="content" source="media/create-ipsec-tunnel/redeem-authorization-key.png" alt-text="Hozzon létre WAN-t.":::

    Illessze be az engedélyezési kulcsot az engedélyezési kulcs mezőbe és a ExpressRoute AZONOSÍTÓját a **társ-áramkör URI** mezőjébe. Győződjön meg arról, hogy **a ExpressRoute áramkör automatikus hozzárendelése a központhoz beállítás van** kiválasztva. A hivatkozás létrehozásához válassza a **Hozzáadás** lehetőséget. 

5. A kapcsolódás teszteléséhez [hozzon létre egy NSX-T szegmenst](./tutorial-nsx-t-network-segment.md) , és helyezzen üzembe egy virtuális gépet a hálózaton. Tesztelje a helyszíni és az Azure VMware megoldás végpontjának pingelését.