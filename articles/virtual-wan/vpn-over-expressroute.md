---
title: 'ExpressRoute-titkosítás konfigurálása: IPsec az ExpressRoute-on keresztül az Azure Virtual WAN-hoz'
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre helyek közötti VPN-kapcsolatot az Azure Virtual WAN használatával az ExpressRoute privát társviszony-létesítésén keresztül.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: b1e6305d142530ab19849f61f12a122d0c6434aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059302"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>ExpressRoute-titkosítás: IPsec az ExpressRoute-on keresztül a Virtual WAN-hoz

Ez a cikk bemutatja, hogyan azure Virtual WAN-nal létesíthet IPsec/IKE VPN-kapcsolatot a helyszíni hálózatról az Azure-ba egy Azure ExpressRoute-kapcsolat létesítése egy Azure ExpressRoute-kapcsolat létesítése során. Ez a módszer titkosított átvitelt biztosíthat a helyszíni hálózatok és az Azure virtuális hálózatok között az ExpressRoute-on keresztül anélkül, hogy a nyilvános interneten vagy nyilvános IP-címeket használna.

## <a name="topology-and-routing"></a>Topológia és útválasztás

Az alábbi ábra egy példát mutat be az ExpressRoute privát társviszony-létesítésén keresztüli VPN-kapcsolatra:

![VPN expressroute-on keresztül](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

Az ábrán egy hálózat a helyszíni hálózaton belül csatlakozik az Azure hub VPN-átjáró ExpressRoute privát társviszony-létesítési keresztül. A konnektivitási létesítmény egyszerű:

1. ExpressRoute-kapcsolat létrehozása ExpressRoute-kapcsolattal és privát társviszony-létesítéssel.
2. Hozza létre a VPN-kapcsolatot az ebben a cikkben leírtak szerint.

A konfiguráció fontos szempontja a helyszíni hálózatok és az Azure közötti útválasztás az ExpressRoute és a VPN-útvonalakon keresztül.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Forgalom a helyszíni hálózatokról az Azure-ba

A helyszíni hálózatokból az Azure-ba irányuló forgalom esetén az Azure-előtagok (beleértve a virtuális központot és a hubhoz csatlakoztatott összes küllővirtuális hálózatot) az ExpressRoute privát társviszony-létesítési BGP-n és a VPN-bGP-n keresztül kerülnek meghirdetésre. Ennek eredményeképpen két hálózati útvonal (elérési út) az Azure felé a helyszíni hálózatokról:

- Az egyik az IPsec-védelemmel ellátott elérési úton
- Egy közvetlenül az ExpressRoute-on *iPsec* védelem nélkül 

A kommunikáció titkosításának alkalmazásához győződjön meg arról, hogy a VPN-hez csatlakoztatott hálózat a diagramon, az Azure-útvonalak helyszíni VPN-átjárón keresztül előnyben részesítik a közvetlen ExpressRoute elérési út.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Forgalom az Azure-ból a helyszíni hálózatokba

Ugyanez a követelmény vonatkozik az Azure-ból a helyszíni hálózatokba irányuló forgalomra is. Annak érdekében, hogy az IPsec elérési út előnyben részesüljön a közvetlen ExpressRoute elérési úton (IPsec nélkül), két lehetőség közül választhat:

- A VPN-kapcsolathoz csatlakoztatott hálózat VPN BGP-munkamenetének konkrétabb előtagok hirdetése. A VPN-hez csatlakoztatott hálózatot az ExpressRoute privát társviszony-létesítésén keresztül, majd a VPN BGP-munkamenet specifikusabb tartományait is meghirdetheti. Például hirdessen 10.0.0.0/16-ot az ExpressRoute-on, és 10.0.1.0/24-et VPN-en keresztül.

- A VPN és az ExpressRoute különálló előtagok hirdetése. Ha a VPN-kapcsolattal rendelkező hálózati tartományok nem tartoznak más ExpressRoute-csatlakoztatott hálózatokból, akkor a VPN- és ExpressRoute BGP-munkamenetekben hirdetheti az előtagokat. Például hirdessen 10.0.0.0/24-et az ExpressRoute-on, és 10.0.1.0/24-et VPN-en keresztül.

Mindkét példában az Azure a 10.0.1.0/24-es számú forgalmat küldavpn-kapcsolaton keresztül, nem pedig közvetlenül az ExpressRoute-on vpn-védelem nélkül.

> [!WARNING]
> Ha *ugyanazokat* az előtagokat hirdeti az ExpressRoute- és A VPN-kapcsolatokon is, az Azure az ExpressRoute elérési útját közvetlenül vpn-védelem nélkül fogja használni.
>

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. Hozzon létre egy virtuális WAN és hub átjárók

A folytatás előtt a következő Azure-erőforrásoknak és a megfelelő helyszíni konfigurációknak kell érvényben lennie:

- Egy Azure virtuális WAN
- Virtuális WAN-központ [ExpressRoute-átjáróval](virtual-wan-expressroute-portal.md) és [VPN-átjáróval](virtual-wan-site-to-site-portal.md)

Az Azure virtuális WAN és egy ExpressRoute-társítással rendelkező hub létrehozásának lépései az [ExpressRoute-társítás létrehozása az Azure Virtual WAN használatával című](virtual-wan-expressroute-portal.md)témakörben található. A virtuális WAN-ban [vpn-átjáró](virtual-wan-site-to-site-portal.md)létrehozásának lépéseit a Helyek közötti kapcsolat létrehozása az Azure Virtual WAN használatával című témakörben található.

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. Hely létrehozása a helyszíni hálózat számára

A helyerőforrás megegyezik a virtuális WAN nem ExpressRoute VPN-helyekkel. A helyszíni VPN-eszköz IP-címe most már lehet egy privát IP-cím, vagy egy nyilvános IP-cím a helyszíni hálózatban érhető el ExpressRoute privát társviszony-létesítés i lépésben létrehozott.

> [!NOTE]
> A helyszíni VPN-eszköz IP-címének az Azure ExpressRoute privát társviszony-létesítése révén a virtuális WAN hubon hirdetett címelőtagok részét *kell,* hogy legyen.
>

1. Nyissa meg az Azure Portalt a böngészőben. 
1. Válassza ki a létrehozott WAN-t. A WAN lap **Kapcsolat**csoportban válassza a **VPN-helyek**lehetőséget.
1. A **VPN-webhelyek** lapon válassza a **+Create site (Webhely létrehozása)** lehetőséget.
1. A **Hely létrehozása** lapon töltse ki a következő mezőket:
   * **Előfizetés**: Ellenőrizze az előfizetést.
   * **Erőforráscsoport**: Jelölje ki vagy hozza létre a használni kívánt erőforráscsoportot.
   * **Régió**: Adja meg a VPN-hely erőforrásának Azure-régióját.
   * **Név**: Adja meg azt a nevet, amely alapján a helyszíni webhelyre szeretne hivatkozni.
   * **Eszközszállító:** Adja meg a helyszíni VPN-eszköz szállítóját.
   * **Border Gateway Protocol**: Válassza az "Engedélyezés" lehetőséget, ha a helyszíni hálózat BGP-t használ.
   * **Privát címterület**: Adja meg a helyszíni webhelyen található IP-címterületet. Az erre a címtérre szánt forgalmat a VPN-átjáró n keresztül irányítja a helyszíni hálózatra.
   * **Hubok**: Jelöljön ki egy vagy több elosztót a VPN-hely csatlakoztatásához. A kijelölt elosztóknak már létre kell hozniuk a VPN-átjárókat.
1. Válassza a Tovább: A VPN-kapcsolat beállításaihoz **>hivatkozások:**
   * **Hivatkozás neve**: Az a név, amellyel erre a kapcsolatra hivatkozni szeretne.
   * **Szolgáltató neve**: A webhely internetszolgáltatójának neve. A helyszíni ExpressRoute-hálózat esetén ez az ExpressRoute-szolgáltató neve.
   * **Sebesség**: Az internetszolgáltatás-kapcsolat vagy az ExpressRoute-kapcsolat sebessége.
   * **IP-cím:** A helyszíni webhelyen található VPN-eszköz nyilvános IP-címe. Vagy a helyszíni ExpressRoute esetén a VPN-eszköz privát IP-címe expressroute-on keresztül.

   Ha a BGP engedélyezve van, akkor az azure-ban a helyhez létrehozott összes kapcsolatra vonatkozik. A BGP konfigurálása virtuális WAN-on egyenértékű a BGP konfigurálásával egy Azure VPN-átjárón. 
   
   A helyszíni BGP-társcím *nem* egyezhet meg az eszköz VPN-címének IP-címével vagy a VPN-hely virtuális hálózati címterével. Használjon másik IP-címet a VPN-eszközön a BGP-társ IP-címéhez. Ez lehet egy olyan cím is, amely az eszköz visszacsatolási hálózatához van rendelve. Azonban *ez nem lehet* APIPA (169.254.* x*. *x*) cím. Adja meg ezt a címet a helyet jelölő megfelelő helyi hálózati átjáróban. A BGP előfeltételeiről a [BGP és az Azure VPN-átjáró című témakörben](../vpn-gateway/vpn-gateway-bgp-overview.md)látható.

1. Válassza a **Tovább lehetőséget: A >áttekintése >** a beállítási értékek ellenőrzéséhez és a VPN-hely létrehozásához. Ha a **Hubs** lehetőséget választotta a csatlakozáshoz, a kapcsolat létrejön a helyszíni hálózat és a központi VPN-átjáró között.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. Frissítse a VPN-kapcsolat beállítását az ExpressRoute használatához

Miután létrehozta a VPN-helyet, és csatlakozott a hubhoz, az alábbi lépésekkel konfigurálhatja a kapcsolatot az ExpressRoute privát társviszony-létesítéséhez:

1. Lépjen vissza a virtuális WAN-erőforráslapra, és válassza ki a központi erőforrást. Vagy navigáljon a VPN-helyről a csatlakoztatott hubra.
1. A **Kapcsolat csoportban**válassza a **VPN (helyek közötti)** lehetőséget.
1. Jelölje ki a három pontot (**...**) az ExpressRoute-on lévő VPN-helyen, és válassza **a VPN-kapcsolat szerkesztése a hubhoz**lehetőséget.
1. **Az Azure privát IP-cím használata**esetén válassza az **Igen**lehetőséget. A beállítás úgy konfigurálja a központi VPN-átjárót, hogy a nyilvános IP-címek helyett a nyilvános IP-címek helyett a hub címtartományán belüli privát IP-címeket használjon az átjárón belül. Ez biztosítja, hogy a helyszíni hálózatról érkező forgalom az ExpressRoute privát társviszony-létesítési útvonalain halad át ahelyett, hogy a nyilvános internetet használná ehhez a VPN-kapcsolathoz. A következő képernyőképen látható a beállítás.

   ![A VPN-kapcsolat privát IP-címének beállítása](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
1. Kattintson a **Mentés** gombra.

A módosítások mentése után a központi VPN-átjáró a VPN-átjáró magánhálózati IP-címeit fogja használni az IPsec/IKE-kapcsolatok létrehozásához a helyszíni VPN-eszközzel az ExpressRoute-on keresztül.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. A központi VPN-átjáró privát IP-címeinek beolvasása

Töltse le a VPN-eszköz konfigurációját a központi VPN-átjáró privát IP-címeinek leolvasásához. Ezekre a címekre van szükség a helyszíni VPN-eszköz konfigurálásához.

1. A hub lapján válassza a **VPN (helyek közötti)** lehetőséget a **Kapcsolat csoportban.**
1. Az **Áttekintés** lap tetején válassza a **VPN-konfiguráció letöltése**lehetőséget. 

   Az Azure létrehoz egy tárfiókot a "microsoft-network-[location]" erőforráscsoportban, ahol a WAN *helye* található. Miután alkalmazta a konfigurációt a VPN-eszközökre, törölheti ezt a tárfiókot.
1. A fájl létrehozása után válassza ki a letöltéshez szükséges hivatkozást.
1. Alkalmazza a konfigurációt a VPN-eszközre.

### <a name="vpn-device-configuration-file"></a>VPN-eszköz konfigurációs fájlja

Az eszközkonfigurációs fájl tartalmazza a helyszíni VPN-eszköz konfigurálásakor használandó beállításokat. A fájl áttekintésekor a következő információkat láthatja:

* **vpnSiteConfiguration**: Ez a szakasz a virtuális WAN-hoz csatlakozó helyként beállított eszközrészleteket jelöli. Ez magában foglalja a nevét és nyilvános IP-címét a fiókeszköz.
* **vpnSiteConnections**: Ez a szakasz a következő beállításokkal kapcsolatos információkat tartalmazza:

    * A virtuális központ virtuális hálózatának címterülete.<br/>Példa:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * A hubhoz csatlakoztatott virtuális hálózatok címterülete.<br>Példa:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * A virtuális központ VPN-átjárójának IP-címei. Mivel a VPN-átjáró minden egyes kapcsolata két, aktív és aktív konfigurációban lévő alagútból áll, a fájlban mindkét IP-cím megjelenik. Ebben a példában, `Instance1` akkor és `Instance0` az egyes helyek, és ők privát IP-címek helyett nyilvános IP-címek.<br>Példa:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * A VPN-átjárókapcsolat konfigurációs adatai, például a BGP és az előmegosztott kulcs. Az előmegosztott kulcs automatikusan létrejön az Ön számára. A kapcsolatot bármikor szerkesztheti az Egyéni előre megosztott kulcs **Áttekintő** lapján.
  
### <a name="example-device-configuration-file"></a>Eszközkonfigurációs példafájl

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>VPN-eszköz konfigurálása

Amennyiben útmutatásra van szüksége az eszköz konfigurálásához, használhatja a [VPN-eszközkonfigurációs szkriptek lapon](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) található utasításokat az alábbi kikötésekkel:

* A VPN-eszköz oldalán található utasítások nem virtuális WAN-ra vannak írva. A konfigurációs fájl virtuális WAN-értékei segítségével azonban manuálisan konfigurálhatja a VPN-eszközt. 
* A VPN-átjáróhoz használható letölthető eszközkonfigurációs parancsfájlok nem működnek a virtuális WAN-hoz, mivel a konfiguráció eltérő.
* Egy új virtuális WAN támogatja az IKEv1 és az IKEv2-t is.
* A virtuális WAN csak útvonalalapú VPN-eszközöket és eszközutasításokat használhat.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. Tekintse meg virtuális WAN

1. Nyissa meg a virtuális WAN-t.
1. Az **Áttekintés** lapon a térkép minden pontja egy elosztót jelöl.
1. A **Hubs and Connections (Hubs and Connections)** szakaszban megtekintheti a hub, a hely, a régió és a VPN-kapcsolat állapotát. A bájtokat be- és kiis tekintheti.

## <a name="7-monitor-a-connection"></a><a name="connectmon"></a>7. Kapcsolat figyelése

Hozzon létre egy kapcsolatot az Azure virtuális gép (VM) és egy távoli hely közötti kommunikáció figyeléséhez. A kapcsolatmonitor beállításával kapcsolatos információkért lásd a [hálózati kommunikáció monitorozását](~/articles/network-watcher/connection-monitor.md) ismertető szakaszt. The source field is the VM IP in Azure, and the destination IP is the site IP.

## <a name="8-clean-up-resources"></a><a name="cleanup"></a>8. Az erőforrások megtisztítása

Ha már nincs szüksége ezekre az erőforrásokra, az [Eltávolító-AzResourceGroup segítségével](/powershell/module/az.resources/remove-azresourcegroup) eltávolíthatja az erőforráscsoportot és az összes benne lévő erőforrást. Futtassa a következő PowerShell parancsot, és cserélje le `myResourceGroup` az erőforráscsoport nevére:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ez a cikk segít a VPN-kapcsolat létrehozásában ExpressRoute privát társviszony-létesítés a Virtual WAN használatával. Ha többet szeretne megtudni a Virtual WAN-ról és a kapcsolódó funkciókról, olvassa el a [Virtual WAN áttekintését.](virtual-wan-about.md)
