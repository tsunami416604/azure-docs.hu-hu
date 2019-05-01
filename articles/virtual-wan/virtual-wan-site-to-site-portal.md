---
title: Az Azure-ba irányuló helyek közötti kapcsolat létrehozása az Azure Virtual WAN használatával | Microsoft Docs
description: Az oktatóanyag az Azure-ba irányuló helyek közötti VPN-kapcsolat létrehozását ismerteti az Azure Virtual WAN használatával.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: e8e251aa5031a8eadd2d567bff2830449c7decc3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689506"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Oktatóanyag: Azure virtuális WAN használatával helyek közötti kapcsolat létrehozása

Az oktatóanyag bemutatja, hogyan kapcsolódhat a Virtual WAN használatával az Azure-ban lévő erőforrásaihoz IPsec/IKE (IKEv1 és IKEv2) VPN-kapcsolaton keresztül. Az ilyen típusú kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy kifelé irányuló, nyilvános IP-cím. A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintését](virtual-wan-about.md).

> [!NOTE]
> Ha több hellyel rendelkezik, általában egy [Virtual WAN-partner](https://aka.ms/virtualwan) segítségével hozza létre ezt a konfigurációt. A konfigurációt azonban maga is létrehozhatja, ha elboldogul a hálózatkezeléssel, és tudja, hogyan kell konfigurálnia saját VPN-eszközeit.
>

![A Virtual WAN ábrája](./media/virtual-wan-about/virtualwan.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * WAN létrehozása
> * Hely létrehozása
> * Elosztó létrehozása
> * Elosztó csatlakoztatása helyhez
> * Kompatibilis a virtuális hálózat létrehozása (Ha még nem rendelkezik egy)
> * Virtuális hálózat csatlakoztatása elosztóhoz
> * A VPN-eszköz konfigurációjának letöltése és alkalmazása
> * A virtuális WAN megtekintése
> * Erőforrás állapotának megtekintése
> * Kapcsolatok monitorozása

## <a name="before-you-begin"></a>Előzetes teendők

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1. Virtuális WAN létrehozása

Egy böngészőből lépjen az [Azure Portalra](https://aka.ms/azurevirtualwanpreviewfeatures), majd jelentkezzen be az Azure-fiókjával.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="site"></a>2. Hely létrehozása

Hozzon létre annyi helyet, amennyit csak szükséges a fizikai helyeknek megfelelően. Például ha New Yorkban, Londonban és Los Angelesben van egy-egy helyi irodája, három külön helyet hozzon létre. Ezek a helyek tartalmazzák a helyszíni VPN-eszközök végpontjait. Jelenleg csak egyetlen privát címteret adhat meg a helyekhez.

1. Kattintson a korábban létrehozott WAN-ra. A WAN lap **WAN-architektúra** területén kattintson a **VPN-helyek** elemre a VPN-helyek lap megnyitásához.
2. A **VPN-helyek** lapon kattintson a **+Hely létrehozása** elemre.
3. A **Hely létrehozása** lapon töltse ki a következő mezőket:

   * **Név** – tekintse meg a helyszíni hely kívánt nevét.
   * **Nyilvános IP-cím** – a nyilvános IP-cím a VPN-eszköz, amely a helyszíni helyen található.
   * **Privát címtér** – Ez a helyszíni helyen található IP-címtér. Az erre a címtérre címzett forgalom át lesz irányítva a helyszíni helyre.
   * **Előfizetés** – Ellenőrizze az előfizetést.
   * **Erőforráscsoport** – A használni kívánt erőforráscsoport.
   * **Hely**
4. A további beállítások megtekintéséhez kattintson a **Speciális megjelenítése** gombra. Választhat **BGP** BGP, amely lehetővé teszi a BGP-funkciót ehhez a helyhez az Azure-ban létrehozott összes kapcsolatot a engedélyezéséhez. Megadhatja az **eszközökkel kapcsolatos információkat** is (opcionális mezők). Ez segíthet a környezet további optimalizálási lehetőségeit a jövőben hozzáadása és háríthatja el jobban megismerheti az Azure-csapat.
5. Kattintson a **Megerősítés** gombra.
6. Miután rákattintott a **Megerősítés** gombra, nézze meg a VPN-hely lapon a hely állapotát. A hely a **Kiépítés alatt** állapotból a**Kiépített** állapotba lép át.

## <a name="hub"></a>3. Elosztó létrehozása

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="associate"></a>4. Helyek társítása a hubhoz

Általános esetben a hubokat olyan helyekhez érdemes társítani, amelyek a virtuális hálózattal egy régióban vannak.

1. A **VPN-helyek** lapon válassza ki azokat a helyeket, amelyeket a hubhoz kíván társítani, majd kattintson az **+Új hub társítása** elemre.
2. A **Helyek társítása egy vagy több hubhoz** lapon válasszon egy hubot a legördülő listából. A helyhez a **+Társítás hozzáadása** elemre kattintva adhat további hubokat.
3. Itt megadhatja a kívánt**PSK**-t is, de használhatja az alapértelmezettet is.
4. Kattintson a **Megerősítés** gombra.
5. A kapcsolat állapotát a **VPN-helyek** lapon tekintheti meg.

## <a name="vnet"></a>5. Virtuális hálózat létrehozása

Ha Ön még nem rendelkezik egy virtuális hálózathoz, gyorsan létrehozhat egy a PowerShell vagy az Azure Portalon. Ha már rendelkezik virtuális hálózattal, győződjön meg arról, hogy megfelel a követelményeknek, és nem rendelkezik virtuális hálózati átjáróval.

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="vnet"></a>6. A virtuális hálózat csatlakoztatása egy elosztóhoz

Ebben a lépésben a társviszony-kapcsolatot hozzuk létre az elosztó és egy virtuális hálózat között. Ismételje meg a fenti lépéseket minden csatlakoztatni kívánt virtuális hálózat esetében.

1. A virtuális WAN lapján kattintson a **Virtuális hálózati kapcsolatok** elemre.
2. A virtuális hálózati kapcsolat lapján kattintson a **+Kapcsolat hozzáadása** elemre.
3. A **Kapcsolat hozzáadása** lapon töltse ki a következő mezőket:

    * **Kapcsolat neve** – Nevezze el a kapcsolatot.
    * **Elosztók** – Válassza ki azt az elosztót, amelyet a kapcsolattal társítani kíván.
    * **Előfizetés** – Ellenőrizze az előfizetést.
    * **Virtuális hálózat** – Válassza ki azt a virtuális hálózatot, amelyet az elosztóhoz csatlakoztatni kíván. A virtuális hálózat nem rendelkezhet már meglévő virtuális hálózati átjáróval.
4. A társviszony-kapcsolat létrehozásához kattintson az **OK** gombra.

## <a name="device"></a>7. VPN-konfiguráció letöltése

A VPN-eszköz konfigurációjának használatával konfigurálhatja a helyszíni VPN-eszközöket.

1. A virtuális WAN lapján kattintson az **Áttekintés** elemre.
2. Az Áttekintés oldal tetején kattintson a **VPN-konfiguráció letöltése** lehetőségre. Az Azure létrehoz egy tárfiókot a „microsoft-network-[hely]” erőforráscsoportban, ahol a hely a WAN helye. Miután a konfigurációt alkalmazta a VPN-eszközökre, törölheti ezt a tárfiókot.
3. Miután befejeződött a fájl létrehozása, a hivatkozásra kattintva letöltheti.
4. Alkalmazza a konfigurációt a VPN-eszközre.

### <a name="understanding-the-vpn-device-configuration-file"></a>VPN-eszköz konfigurációs fájljának ismertetése

Az eszközkonfigurációs fájl a helyszíni VPN-eszköz konfigurálása során használandó beállításokat tartalmazza. A fájl áttekintésekor a következő információkat láthatja:

* **vpnSiteConfiguration** – Ez a szakasz az eszköz a virtuális WAN-ra csatlakozó helyként való telepítésére vonatkozó adatait tartalmazza. Az ágeszköz nevét és nyilvános IP-címét tartalmazza.
* **vpnSiteConnections -** Ez a szakasz ismerteti a következő beállításokat:

    * A virtuális elosztó(k) virtuális hálózatának **címtere**<br>Példa:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * Az elosztóra csatlakozó virtuális hálózatok **címtere**<br>Példa:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * A virtuális hálózat VPN-átjárójának **IP-címei** Minden kapcsolat, a VPN-átjáró két alagút aktív-aktív konfigurációban épül fel, mert a fájlban szereplő IP-címe megjelenik. Ebben a példában mindegyik helyhez az „Instance0” és az „Instance1” van feltüntetve.<br>Példa:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * A **VPN-átjáró kapcsolati konfigurációjának részletei**, például BGP, előre megosztott kulcs stb. A rendszer által automatikusan létrehozott előre megosztott kulcs a PSK. A kapcsolatot az egyéni PSK Áttekintés lapján bármikor szerkesztheti.
  
### <a name="example-device-configuration-file"></a>Eszközkonfigurációs példafájl

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.30.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a>VPN-eszköz konfigurálása

>[!NOTE]
> Amennyiben Virtual WAN-partnermegoldást használ, a VPN-eszköz konfigurációja automatikusan végbemegy. Az eszközvezérlő megkapja a konfigurációs fájlt az Azure-ból, és alkalmazza azt az eszközre az Azure-kapcsolat beállításához. Ez azt jelenti, hogy a VPN-eszköz manuális konfigurálásának menetét nem kell ismernie.
>

Amennyiben útmutatásra van szüksége az eszköz konfigurálásához, használhatja a [VPN-eszközkonfigurációs szkriptek lapon](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) található utasításokat az alábbi kikötésekkel:

* A VPN-eszközök lapon található utasítások nem a Virtual WAN-hoz íródtak, a Virtual WAN a konfigurációs fájlban lévő értékeivel manuálisan konfigurálhatja a VPN-eszközt. 
* A VPN Gatewayre vonatkozó letölthető eszközkonfigurációs szkriptek a Virtual WAN esetében nem működnek, mivel a konfiguráció eltér.
* Egy új virtuális WAN az IKEv1 és az IKEv2 támogatására is képes.
* A Virtual WAN kizárólag az útvonalalapú VPN-eszközöket és eszközutasításokat képes használni.

## <a name="viewwan"></a>8. A virtuális WAN megtekintése

1. Lépjen a virtuális WAN-ra.
2. Az Áttekintés lapon a térképen látható pontok mindegyike egy elosztót jelöl. Az elosztók állapotösszegzéséért vigye a mutatót az egyes pontok fölé.
3. Az elosztók és kapcsolatok szakaszában láthatja az elosztók állapotát, helyét, régióját, VPN-kapcsolati állapotát, valamint a bájtban kifejezett be- és kimenő forgalmát.

## <a name="viewhealth"></a>9. Az erőforrás állapotának megtekintése

1. Lépjen a WAN-ra.
2. A WAN lapjának **TÁMOGATÁS + hibaelhárítás** szakaszában kattintson az **Állapot** lehetőségre, és tekintse meg az erőforrást.

## <a name="connectmon"></a>10. Kapcsolatok monitorozása

Létrehozhat egy kapcsolatot az Azure-beli virtuális gépek és a távoli helyek közötti kommunikáció monitorozására. A kapcsolatmonitor beállításával kapcsolatos információkért lásd a [hálózati kommunikáció monitorozását](~/articles/network-watcher/connection-monitor.md) ismertető szakaszt. A forrásmező a virtuális gép IP-címe az Azure-ban, a cél IP-cím pedig a hely IP-címe.

## <a name="cleanup"></a>11. Az erőforrások eltávolítása

Ha ezekre az erőforrásokra már nincs szüksége, használhatja [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) , távolítsa el az erőforráscsoportot és az összes benne található erőforrást. A „myResourceGroup” helyére írja be az erőforráscsoport nevét, és futtassa a következő PowerShell-parancsot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * WAN létrehozása
> * Hely létrehozása
> * Elosztó létrehozása
> * Elosztó csatlakoztatása helyhez
> * Virtuális hálózat csatlakoztatása elosztóhoz
> * A VPN-eszköz konfigurációjának letöltése és alkalmazása
> * A virtuális WAN megtekintése
> * Erőforrás állapotának megtekintése
> * Kapcsolatok monitorozása

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.
