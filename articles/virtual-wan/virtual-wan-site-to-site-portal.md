---
title: 'Azure Virtual WAN: Helyek közötti kapcsolatok létrehozása'
description: Az oktatóanyag az Azure-ba irányuló helyek közötti VPN-kapcsolat létrehozását ismerteti az Azure Virtual WAN használatával.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: b4278cb2e8c5152f522258a37c37acda5efbacf8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239685"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Oktatóanyag: Helyek közötti kapcsolat létrehozása az Azure Virtual WAN használatával

Az oktatóanyag bemutatja, hogyan kapcsolódhat a Virtual WAN használatával az Azure-ban lévő erőforrásaihoz IPsec/IKE (IKEv1 és IKEv2) VPN-kapcsolaton keresztül. Az ilyen típusú kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy kifelé irányuló, nyilvános IP-cím. A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintését](virtual-wan-about.md).

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Virtuális WAN létrehozása
> * Elosztó létrehozása
> * Hely létrehozása
> * Webhely csatlakoztatása elosztóhoz
> * VPN-hely csatlakoztatása hubhoz
> * Virtuális hálózat csatlakoztatása elosztóhoz
> * Konfigurációs fájl letöltése
> * A virtuális WAN megtekintése

> [!NOTE]
> Ha több hellyel rendelkezik, általában egy [Virtual WAN-partner](https://aka.ms/virtualwan) segítségével hozza létre ezt a konfigurációt. A konfigurációt azonban maga is létrehozhatja, ha elboldogul a hálózatkezeléssel, és tudja, hogyan kell konfigurálnia saját VPN-eszközeit.
>

![Virtuális WAN ábrája](./media/virtual-wan-about/virtualwan.png)

## <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Van egy virtuális hálózat, amelyhez csatlakozni szeretne. Ellenőrizze, hogy a helyszíni hálózatok egyik alhálózata sem fedi-e át azokat a virtuális hálózatokat, amelyekhez csatlakozni szeretne. Virtuális hálózat létrehozásához az Azure Portalon tekintse meg a [gyorsútmutatót.](../virtual-network/quick-create-portal.md)

* A virtuális hálózat nem rendelkezik virtuális hálózati átjárókkal. Ha a virtuális hálózat rendelkezik átjáróval (VPN vagy ExpressRoute), el kell távolítania az összes átjárót. Ez a konfiguráció megköveteli, hogy a virtuális hálózatok csatlakoznak helyett, a Virtual WAN hub átjáró.

* Igényeljen egy IP-címtartományt az elosztó régiójában. A hub egy virtuális hálózat, amelyet a Virtual WAN hoz létre és használ. A hubhoz megadott címtartomány nem fedheti át a meglévő virtuális hálózatokat, amelyekhez csatlakozik. Emellett nem lehet átfedésben azokkal a címtartományokkal sem, amelyekhez a helyszínen csatlakozik. Ha nem ismeri a helyszíni hálózati konfigurációban található IP-címtartományokat, egyeztessen valakivel, aki meg tudja adni ezeket az adatokat.

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Virtuális WAN létrehozása

Egy böngészőből lépjen az Azure Portalra, majd jelentkezzen be az Azure-fiókjával.

1. Nyissa meg a Virtual WAN lapot. A portálon kattintson az **+Erőforrás létrehozása** gombra. Írja be a **Virtual WAN** parancsot a keresőmezőbe, és válassza az Enter lehetőséget.
2. Válassza ki a **Virtual WAN** az eredmények közül. A Virtual WAN lapon kattintson a **Létrehozás** gombra a WAN létrehozása lap megnyitásához.
3. A **WAN létrehozása** lapon az **Alapok** lapon töltse ki a következő mezőket:

   ![Virtuális WAN](./media/virtual-wan-site-to-site-portal/vwan.png)

   * **Előfizetés** – Válassza ki a használni kívánt előfizetést.
   * **Erőforráscsoport** – Új létrehozása vagy meglévő használata.
   * **Erőforráscsoport helye** – Erőforrás helyének kiválasztása a legördülő menüből. A WAN egy globális erőforrás, és nem egy adott régióhoz tartozik. Mindazonáltal mégis ki kell választania egy régiót, hogy könnyebben kezelhesse és megtalálhassa a létrehozott WAN-erőforrást.
   * **Név** – Írja be a WAN-nak hívni kívánt nevet.
   * **Típus:** Alapszintű vagy standard. Ha alapwanst hoz létre, csak alapszintű elosztót hozhat létre. Az alapvető elosztók csak vpn-helyek közötti kapcsolatra képesek.
4. Miután befejezte a mezők kitöltését, válassza a **Véleményezés +Létrehozás**gombot.
5. Az érvényesítés sikeressítése után válassza a **Létrehozás** lehetőséget a virtuális WAN létrehozásához.

## <a name="create-a-hub"></a><a name="hub"></a>Elosztó létrehozása

A hub olyan virtuális hálózat, amely a helyek közötti, az ExpressRoute vagy a pont-hely funkciók átjáróit is tartalmazhatja. A központ létrehozását követően a szolgáltatás akkor is díjköteles, ha nem csatol hozzá egyetlen webhelyet sem. A helyek közötti VPN-átjáró létrehozása 30 percet vesz igénybe a virtuális központban.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-site"></a><a name="site"></a>Hely létrehozása

Most már készen áll a fizikai helyeknek megfelelő webhelyek létrehozására. Hozzon létre annyi helyet, amennyit csak szükséges a fizikai helyeknek megfelelően. Például ha New Yorkban, Londonban és Los Angelesben van egy-egy helyi irodája, három külön helyet hozzon létre. Ezek a helyek tartalmazzák a helyszíni VPN-eszközök végpontjait. Virtuális wan-hálózatonként legfeljebb 1000 helyet hozhat létre. Ha több hubja volt, 1000-et hozhat létre mindegyik hubonként. Ha rendelkezik Virtual WAN-partner (hivatkozásbeszúrási) CPE-eszközzel, kérdezze meg őket az Azure-ba való automatizálásukról. Az automatizálás általában egyszerű kattintási élményt jelent a nagyméretű elágazási adatok azure-ba történő exportálásához és a CPE-ről az Azure Virtual WAN VPN-átjáróra való kapcsolat beállításához. További információ: [Automation guidance from Azure from CPE partners.](virtual-wan-configure-automation-providers.md)

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connect-the-vpn-site-to-the-hub"></a><a name="connectsites"></a>A VPN-hely csatlakoztatása a hubhoz

Ebben a lépésben csatlakoztathatja a VPN-webhelyet a hubhoz.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>A virtuális hálózat csatlakoztatása a hubhoz

Ebben a lépésben hozza létre a hub és a virtuális hálózat közötti kapcsolatot. Ismételje meg a fenti lépéseket minden csatlakoztatni kívánt virtuális hálózat esetében.

1. A virtuális WAN lapján kattintson a **Virtuális hálózati kapcsolatok** elemre.
2. A virtuális hálózati kapcsolat lapján kattintson a **+Kapcsolat hozzáadása** elemre.
3. A **Kapcsolat hozzáadása** lapon töltse ki a következő mezőket:

    * **Kapcsolat neve** – Nevezze el a kapcsolatot.
    * **Elosztók** – Válassza ki azt az elosztót, amelyet a kapcsolattal társítani kíván.
    * **Előfizetés** – Ellenőrizze az előfizetést.
    * **Virtuális hálózat** – Válassza ki azt a virtuális hálózatot, amelyet az elosztóhoz csatlakoztatni kíván. A virtuális hálózat nem rendelkezhet már meglévő virtuális hálózati átjáróval.
4. A virtuális hálózati kapcsolat létrehozásához kattintson az **OK** gombra.

## <a name="download-vpn-configuration"></a><a name="device"></a>VPN-konfiguráció letöltése

A VPN-eszköz konfigurációjának használatával konfigurálhatja a helyszíni VPN-eszközöket.

1. A virtuális WAN lapján kattintson az **Áttekintés** elemre.
2. A **Hub ->VPN-webhely** lap tetején kattintson a **VPN-konfiguráció letöltése gombra.** Az Azure létrehoz egy tárfiókot a "microsoft-network-[location]" erőforráscsoportban, ahol a hely a WAN helye. Miután a konfigurációt alkalmazta a VPN-eszközökre, törölheti ezt a tárfiókot.
3. Miután befejeződött a fájl létrehozása, a hivatkozásra kattintva letöltheti.
4. Alkalmazza a konfigurációt a helyszíni VPN-eszközre.

### <a name="understanding-the-vpn-device-configuration-file"></a>VPN-eszköz konfigurációs fájljának ismertetése

Az eszközkonfigurációs fájl a helyszíni VPN-eszköz konfigurálása során használandó beállításokat tartalmazza. A fájl áttekintésekor a következő információkat láthatja:

* **vpnSiteConfiguration** – Ez a szakasz az eszköz a virtuális WAN-ra csatlakozó helyként való telepítésére vonatkozó adatait tartalmazza. Az ágeszköz nevét és nyilvános IP-címét tartalmazza.
* **vpnSiteConnections -** Ez a rész a következő beállításokkal kapcsolatos információkat tartalmazza:

    * A virtuális elosztó(k) virtuális hálózatának **címtere**<br>Példa:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * Az elosztóra csatlakozó virtuális hálózatok **címtere**<br>Példa:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * A virtuális hálózat VPN-átjárójának **IP-címei** Mivel a vpngateway minden kapcsolata két, aktív és aktív konfigurációban lévő alagútból áll, a fájlban mindkét IP-cím megjelenik. Ebben a példában mindegyik helyhez az „Instance0” és az „Instance1” van feltüntetve.<br>Példa:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Vpngateway kapcsolat konfigurációs részleteket,** mint a BGP, előre megosztott kulcs, stb A PSK az előre megosztott kulcs, amely automatikusan létrejön az Ön számára. A kapcsolatot az egyéni PSK Áttekintés lapján bármikor szerkesztheti.
  
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
                  "10.3.0.0/16"
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
* Az új virtuális WAN támogatja az IKEv1 és az IKEv2-t is.
* A Virtual WAN házirend-alapú és útvonalalapú VPN-eszközöket és eszközutasításokat is használhat.

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>A virtuális WAN megtekintése

1. Lépjen a virtuális WAN-ra.
2. Az **Áttekintés** lapon a térkép minden pontja egy elosztót jelöl. Mutasson bármely pontra a központi állapot összegzésének, a kapcsolat állapotának és a be- és ki- és be- és ki- és be- és ki- és be- és ki- és be- és ki- és be- és ki- és be- és ki- és betekintési aránymegtekintéséhez.
3. A Hubs and Connections (Hubs and Connections) szakaszban megtekintheti a hub állapotát, a VPN-helyeket stb. Kattintson egy adott hub nevére, és keresse meg a VPN-webhelyet további részletekért.

## <a name="next-steps"></a>További lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.
