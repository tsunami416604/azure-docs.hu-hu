---
title: Helyek közötti VPN-kapcsolat létrehozása a ExpressRoute-en keresztül az Azure Virtual WAN-ban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure Virtual WAN-t helyek közötti VPN-kapcsolat létrehozására a ExpressRoute privát társításával.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 10/11/2019
ms.author: cherylmc
Customer intent: I want to connect my on-premises networks to my VNets using S2S VPN connection over my ExpressRoute private peering using Azure Virtual WAN.
ms.openlocfilehash: 6272d6fe6f8c35c06a8121e10be2dd5a2e5512a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515030"
---
# <a name="create-a-site-to-site-vpn-connection-over-expressroute-private-peering-using-azure-virtual-wan"></a>Helyek közötti VPN-kapcsolat létrehozása ExpressRoute-alapú privát kapcsolatok használatával az Azure Virtual WAN használatával

Ez a cikk bemutatja, hogyan használható a Virtual WAN egy IPsec/IKE VPN-kapcsolat létrehozására a helyszíni hálózatról az Azure-ba egy ExpressRoute-áramkör privát összevonása során. Ez titkosított átvitelt tesz lehetővé a helyszíni hálózatok és az Azure-beli virtuális hálózatok között a ExpressRoute keresztül, a nyilvános interneten keresztül, vagy nyilvános IP-címek használata nélkül.

## <a name="topology-and-routing"></a>Topológia és Útválasztás

Az alábbi ábrán a ExpressRoute magánhálózati kapcsolaton keresztüli VPN-en keresztül látható egy példa:

![VPN over ExpressRoute](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

Az ábrán az Azure hub VPN Gateway-hez csatlakoztatott helyszíni hálózaton belüli hálózat látható a ExpressRoute-alapú privát kapcsolaton keresztül. A kapcsolat létrehozása egyszerű:

1. ExpressRoute-kapcsolat létrehozása ExpressRoute-áramkörrel és privát partnerekkel
2. A VPN-kapcsolat létrehozása a jelen dokumentumban leírtak szerint

Ennek a konfigurációnak a fontos aspektusa a helyszíni hálózatok és az Azure közötti útválasztás a ExpressRoute és a VPN elérési útjain keresztül.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Helyszíni hálózatokból az Azure-ba irányuló forgalom

A helyszíni hálózatokból az Azure-ba irányuló forgalom esetén az Azure-előtagok (beleértve a virtuális központot és a központhoz csatlakozó összes küllős virtuális hálózat) a ExpressRoute privát és a VPN BGP használatával is meghirdetve lesznek. Ennek eredményeképpen a helyszíni hálózatokból két hálózati útvonal (elérési út) lesz az Azure felé. egyet az IPsec által védett elérési úton, és közvetlenül az IPSec-védelem **nélküli** ExpressRoute. Annak érdekében, hogy a titkosítás a kommunikációra legyen alkalmazva, meg kell győződnie arról, hogy a diagramon a VPN-kapcsolattal rendelkező hálózat esetében az Azure-útvonalak a helyszíni VPN-átjárón keresztül, előnyben részesítettek a közvetlen ExpressRoute útvonalon.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Az Azure-ból a helyszíni hálózatokra irányuló forgalom

Ugyanez a követelmény vonatkozik az Azure-ból a helyszíni hálózatokra érkező forgalomra is. Ha biztosítani szeretné, hogy az IPsec elérési útja előnyben legyen a közvetlen ExpressRoute útvonalon (IPsec nélkül), két lehetőség közül választhat:

- A VPN-kapcsolattal rendelkező hálózathoz tartozó VPN BGP-munkamenetben konkrétabb előtagokat Hirdessen. A "VPN-kapcsolattal rendelkező hálózat" ExpressRoute többek között többek között a VPN BGP-munkamenetben megjelenő nagyobb tartományokban is meghirdethető. Például Hirdessen 10.0.0.0/16 over ExpressRoute, és 10.0.1.0/24 VPN-en keresztül.

- A VPN-és ExpressRoute különálló előtagjainak reklámozása. Ha a VPN-kapcsolattal rendelkező hálózati tartományok eltérő ExpressRoute csatlakoztatott hálózatról vannak kiválasztva, akkor a VPN-és ExpressRoute BGP-munkamenetekben is meghirdetheti az előtagokat. Például Hirdessen 10.0.0.0/24 over ExpressRoute, és 10.0.1.0/24 VPN-en keresztül.

Mindkét példában az Azure a VPN-kapcsolaton keresztül küldi el a forgalmat a 10.0.1.0/24-re, nem pedig közvetlenül a VPN-védelem nélküli ExpressRoute.

> [!WARNING]
> Ha **ugyanazokat** az előtagokat hirdeti meg mind a ExpressRoute, mind a VPN-kapcsolaton keresztül, **Az Azure közvetlenül a VPN-védelem nélkül fogja használni a ExpressRoute útvonalat**.
>

## <a name="before-you-begin"></a>Előzetes teendők

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1. virtuális WAN és hub létrehozása átjárókkal

A továbblépés előtt a következő Azure-erőforrásokat és a megfelelő helyszíni konfigurációkat kell megadnia:

1. Azure-beli virtuális WAN
2. Egy [ExpressRoute-átjáróval](virtual-wan-expressroute-portal.md) és egy [VPN-átjáróval](virtual-wan-site-to-site-portal.md) rendelkező virtuális WAN-központ

Tekintse meg a [ExpressRoute-társítás Azure Virtual WAN használatával](virtual-wan-expressroute-portal.md) történő létrehozását ismertető témakört, amely egy Azure-beli virtuális WAN és egy ExpressRoute-társítással rendelkező központ létrehozásához nyújt útmutatást, valamint [helyek közötti kapcsolat létrehozása az Azure Virtual WAN használatával](virtual-wan-site-to-site-portal.md) a VPN létrehozásának lépéseihez átjáró a virtuális WAN-ban.

## <a name="site"></a>2. hely létrehozása a helyszíni hálózathoz

A hely erőforrása megegyezik a virtuális WAN-hoz nem ExpressRoute VPN-helyekkel. Fontos megjegyezni, hogy a helyszíni VPN-eszköz IP-címe mostantól privát IP-cím lehet, vagy a helyszíni hálózaton elérhető nyilvános IP-cím az 1. lépésben létrehozott ExpressRoute-alapú privát társon keresztül érhető el.

> [!NOTE]
> A helyszíni VPN-eszköz IP-címének részét kell képeznie a virtuális WAN-hubhoz az Azure ExpressRoute privát kapcsolaton keresztül meghirdetett cím előtagjainak.
>

1. Navigáljon a Azure Portal a böngészőben. Kattintson a korábban létrehozott WAN-ra. A WAN lap **kapcsolat**területén kattintson a **VPN-helyek** elemre a VPN-helyek lap megnyitásához.

2. A **VPN-helyek** lapon kattintson a **+Hely létrehozása** elemre.

3. A **Hely létrehozása** lapon töltse ki a következő mezőket:

   * **Előfizetés** – Ellenőrizze az előfizetést.
   * **Erőforráscsoport** – válassza ki vagy hozza létre a használni kívánt erőforráscsoportot.
   * **Régió** – a VPN-hely erőforrásához tartozó Azure-régió.
   * **Név** – a név, amellyel a helyszíni helyre hivatkozni kíván.
   * **Eszköz szállítója** – a helyszíni VPN-eszköz gyártója.
   * **Border Gateway Protocol** – ha a helyszíni hálózat BGP-t használ, válassza az "engedélyezés" lehetőséget.
   * **Privát címtér** – Ez a helyszíni helyen található IP-címtér. A Címterület felé irányuló forgalom a VPN-átjárón keresztül lesz átirányítva a helyszíni hálózatra.
   * **Hubok** – válasszon ki egy vagy több hubokat a VPN-hely összekapcsolásához. A kiválasztott huboknak már létre kell hozniuk a VPN-átjárókat.

4. Kattintson a **Tovább gombra: hivatkozások >** a VPN-kapcsolat beállításaihoz:

   * **Hivatkozás neve** – a név, amellyel hivatkozni kíván erre a kapcsolatra.
   * **Szolgáltató neve** – a hely internetszolgáltatójának neve. ExpressRoute helyszíni hálózat esetén a ExpressRoute-szolgáltató neve.
   * **Speed** – az Internet Service-hivatkozás sebessége vagy a ExpressRoute áramkör.
   * **IP-cím** – a helyszíni helyen található VPN-eszköz nyilvános IP-címe. Vagy a helyszíni ExpressRoute esetében a VPN-eszköz magánhálózati IP-címe ExpressRoute-n keresztül.

   Ha a BGP engedélyezve van, akkor az az Azure-ban a helyhez létrehozott összes kapcsolatra érvényes lesz. A BGP virtuális WAN-on való konfigurálása egyenértékű a BGP Azure-beli VPN-átjárón való konfigurálásával. A helyszíni BGP-társ címe *nem* egyezhet meg az eszköz VPN-címével vagy a VPN-hely VNet. Használjon egy másik IP-címet a VPN-eszközön a BGP-társ IP-címéhez. Ez lehet egy olyan cím is, amely az eszköz visszacsatolási hálózatához van rendelve. Azonban *nem* lehet APIPA (169,254). *x*. *x*) címe. Ezt a címet a helyet jelölő megfelelő Helyi hálózati átjáróban kell megadni. A BGP-előfeltételeket lásd: [a BGP és az Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

5. Kattintson a **Tovább gombra: Tekintse át a + create >** elemet a beállítási értékek ellenőrzéséhez és a VPN-hely létrehozásához. Ha a csatlakozáshoz a **hubok** lehetőséget választotta, a kapcsolat létrejön a helyszíni hálózat és a hub VPN-átjáró között.

## <a name="hub"></a>3. frissítse a VPN-kapcsolat beállítását a ExpressRoute használatára

A VPN-hely létrehozása és az elosztóhoz való csatlakozás után a következő lépésekkel konfigurálhatja a kapcsolatot a ExpressRoute privát társítás használatára:

1. Lépjen vissza a virtuális WAN-erőforrás lapra, és kattintson a hub-erőforrásra. Vagy navigáljon a VPN-helyről a csatlakoztatott hubhoz.

2. A **kapcsolat**területen kattintson a **VPN (helyek közötti)** elemre.

3. Kattintson a "..." elemre. a VPN-helyen a ExpressRoute-en keresztül, és válassza a "**VPN-kapcsolat szerkesztése ehhez a**központhoz" lehetőséget.

4. Válassza az "igen" lehetőséget az "**Azure Private IP-cím használata**" beállításnál. A beállítás úgy konfigurálja a hub VPN-átjárót, hogy a nyilvános IP-címek helyett magánhálózati IP-címeket használjon a hub-címtartomány ezen a kapcsolaton belül. Ezzel biztosíthatja, hogy a helyszíni hálózatról érkező forgalom a nyilvános Internet helyett a VPN-kapcsolaton keresztül áthaladjon a ExpressRoute privát elágazási útjain. A következő képernyőfelvételen a beállítási ablak látható.

   ![VPN-kapcsolat beállítása](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
5. Kattintson a **Save** (Mentés) gombra.

A mentés után a hub VPN Gateway a VPN-átjáró magánhálózati IP-címeivel hozza létre az IPsec/IKE-kapcsolatokat a helyszíni VPN-eszközzel a ExpressRoute-en keresztül.

## <a name="associate"></a>4. a hub VPN Gateway magánhálózati IP-címeinek lekérése

Töltse le a VPN-eszköz konfigurációját a hub VPN-átjáró magánhálózati IP-címeinek beszerzéséhez. Ezek a helyszíni VPN-eszköz konfigurálásához szükségesek.

1. A központ lapján kattintson a **VPN (helyek közötti)** elemre a **kapcsolat** alatt.

2. Az Áttekintés lap tetején kattintson a **VPN-konfiguráció letöltése**elemre. Az Azure létrehoz egy Storage-fiókot a "Microsoft-Network-[Location]" erőforráscsoporthoz, ahol a hely a WAN helye. Miután a konfigurációt alkalmazta a VPN-eszközökre, törölheti ezt a tárfiókot.

3. Miután befejeződött a fájl létrehozása, a hivatkozásra kattintva letöltheti.

4. Alkalmazza a konfigurációt a VPN-eszközre.

### <a name="understanding-the-vpn-device-configuration-file"></a>VPN-eszköz konfigurációs fájljának ismertetése

Az eszközkonfigurációs fájl a helyszíni VPN-eszköz konfigurálása során használandó beállításokat tartalmazza. A fájl áttekintésekor a következő információkat láthatja:

* **vpnSiteConfiguration** – Ez a szakasz az eszköz a virtuális WAN-ra csatlakozó helyként való telepítésére vonatkozó adatait tartalmazza. Az ágeszköz nevét és nyilvános IP-címét tartalmazza.
* **vpnSiteConnections –** Ez a szakasz a következő beállításokkal kapcsolatos információkat tartalmazza:

    * A virtuális elosztó(k) virtuális hálózatának **címtere**<br/>Példa:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * Az elosztóra csatlakozó virtuális hálózatok **címtere**<br>Példa:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * A virtuális hálózat VPN-átjárójának **IP-címei** Mivel a átjáróban minden egyes csatlakoztatása két alagútból áll az aktív-aktív konfigurációban, a fájlban felsorolt IP-címeket is látni fogja. Ebben a példában az egyes helyekhez a "Instance0" és a "Peldany1" elemet "látható, és a nyilvános IP-címek helyett magánhálózati IP-címek.<br>Példa:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * **Átjáróban-kapcsolat konfigurációs részletei** , például BGP, előmegosztott kulcs stb. A PSK a automatikusan létrehozott előmegosztott kulcs. A kapcsolatot az egyéni PSK Áttekintés lapján bármikor szerkesztheti.
  
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

* A VPN-eszközök lapon található utasítások nem a Virtual WAN-hoz íródtak, a Virtual WAN a konfigurációs fájlban lévő értékeivel manuálisan konfigurálhatja a VPN-eszközt. 
* A VPN Gatewayre vonatkozó letölthető eszközkonfigurációs szkriptek a Virtual WAN esetében nem működnek, mivel a konfiguráció eltér.
* Egy új virtuális WAN az IKEv1 és az IKEv2 támogatására is képes.
* A Virtual WAN kizárólag az útvonalalapú VPN-eszközöket és eszközutasításokat képes használni.

## <a name="viewwan"></a>5. a virtuális WAN megtekintése

1. Lépjen a virtuális WAN-ra.

2. Az Áttekintés lapon a térképen látható pontok mindegyike egy elosztót jelöl. Az elosztók állapotösszegzéséért vigye a mutatót az egyes pontok fölé.

3. Az elosztók és kapcsolatok szakaszában láthatja az elosztók állapotát, helyét, régióját, VPN-kapcsolati állapotát, valamint a bájtban kifejezett be- és kimenő forgalmát.

## <a name="viewhealth"></a>6. az erőforrás állapotának megtekintése

1. Lépjen a WAN-ra.

2. A WAN lapjának **TÁMOGATÁS + hibaelhárítás** szakaszában kattintson az **Állapot** lehetőségre, és tekintse meg az erőforrást.

## <a name="connectmon"></a>7. a kapcsolatok figyelése

Létrehozhat egy kapcsolatot az Azure-beli virtuális gépek és a távoli helyek közötti kommunikáció monitorozására. A kapcsolatmonitor beállításával kapcsolatos információkért lásd a [hálózati kommunikáció monitorozását](~/articles/network-watcher/connection-monitor.md) ismertető szakaszt. A forrásmező a virtuális gép IP-címe az Azure-ban, a cél IP-cím pedig a hely IP-címe.

## <a name="cleanup"></a>8. erőforrások törlése

Ha már nincs szüksége ezekre az erőforrásokra, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) használatával távolítsa el az erőforráscsoportot és a benne található összes erőforrást. A „myResourceGroup” helyére írja be az erőforráscsoport nevét, és futtassa a következő PowerShell-parancsot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatja, hogyan hozhat létre VPN-kapcsolatot a ExpressRoute magánhálózati kapcsolaton keresztül a virtuális WAN használatával. Ha többet szeretne megtudni a virtuális WAN-ról és az egyéb kapcsolódó szolgáltatásokról, tekintse meg a [virtuális WAN – áttekintés](virtual-wan-about.md) lapot.
