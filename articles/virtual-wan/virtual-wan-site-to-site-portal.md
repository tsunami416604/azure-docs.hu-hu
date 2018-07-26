---
title: Az Azure-ba irányuló helyek közötti kapcsolat létrehozása az Azure Virtual WAN használatával | Microsoft Docs
description: Az oktatóanyag az Azure-ba irányuló helyek közötti VPN-kapcsolat létrehozását ismerteti az Azure Virtual WAN használatával.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 07/13/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: ea36a3d4a2471cee6a18d70275aaf2e83ffc6f39
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159651"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan-preview"></a>Oktatóanyag: Helyek közötti kapcsolat létrehozása az Azure Virtual WAN (előzetes verzió) használatával

Az oktatóanyag bemutatja, hogyan kapcsolódhat a Virtual WAN használatával az Azure-ban lévő erőforrásaihoz IPsec/IKE (IKEv2) VPN-kapcsolaton keresztül. Az ilyen típusú kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy kifelé irányuló, nyilvános IP-cím. A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintését](virtual-wan-about.md).

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
> * Virtuális hálózat csatlakoztatása elosztóhoz
> * A VPN-eszköz konfigurációjának letöltése és alkalmazása
> * A virtuális WAN megtekintése
> * Erőforrás állapotának megtekintése
> * Kapcsolatok monitorozása

> [!IMPORTANT]
> Az Azure Virtual WAN jelenleg felügyelt nyilvános előzetes verzióként érhető el. A Virtual WAN használatához [regisztrálnia kell az előzetes verzióra](#enroll).
>
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Győződjön meg arról, hogy rendelkezésre áll egy kompatibilis, útvonalalapú, az IKEv2 használatára alkalmas VPN-eszköz és egy, azt konfigurálni képes személy. Amennyiben egy Virtual WAN-partner segítségét veszi igénybe, a konfigurációs beállítások létrehozása automatikusan történik, és nem kell ismernie az eszközök manuális konfigurálásának folyamatát.
* Győződjön meg arról, hogy rendelkezik egy kifelé irányuló, nyilvános IPv4-címmel a VPN-eszköz számára. Ez az IP-cím nem lehet NAT mögötti.
* Ha már rendelkezik virtuális hálózattal, amelyhez csatlakozni szeretne, győződjön meg arról, hogy a helyszíni hálózat egyik alhálózata sincs átfedésben azon virtuális alhálózatokkal, amelyekhez csatlakozni szeretne. A virtuális hálózathoz nem szükséges átjáró-alhálózat, és nem rendelkezhet virtuális hálózati átjárókkal. Ha nem rendelkezik virtuális hálózattal, létrehozhat egyet a cikkben ismertetett lépések végrehajtásával.
* Igényeljen egy IP-címtartományt az elosztó régiójában. Az elosztó egy virtuális hálózat, és az elosztó régiójához megadott címtartomány nem lehet átfedésben a meglévő azon virtuális hálózatokkal, amelyekhez csatlakozni szeretne. Emellett nem lehet átfedésben azokkal a címtartományokkal sem, amelyekhez a helyszínen csatlakozik. Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek.
* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="enroll"></a>1. Regisztráció az előzetes verzióra

A Virtual WAN konfigurálása előtt regisztrálnia kell az előfizetését az előzetes verzióban. Máskülönben nem használhatja a Virtual WAN-t a portálon. Ehhez küldjön egy e-mailt a következő címre az előfizetés azonosítójával: **azurevirtualwan@microsoft.com**. Az előfizetés regisztrációja után egy e-mailt fog kapni.

## <a name="vnet"></a>2. Virtuális hálózat létrehozása

Ha még nem rendelkezik virtuális hálózattal, gyorsan létrehozhat egyet a PowerShell használatával. Vagy az Azure Portalon is létrehozhat egy virtuális hálózatot.

* Mindenképp ellenőrizze, hogy a létrehozott virtuális hálózat címtere nincs-e átfedésben a többi virtuális hálózat címterével, amelyekhez csatlakozni szeretne, vagy a helyszíni hálózat címterével. 
* Ha már rendelkezik virtuális hálózattal, győződjön meg arról, hogy megfelel a követelményeknek, és nem rendelkezik virtuális hálózati átjáróval.

A cikkben lévő „Kipróbálás” lehetőségre kattintva megnyíló PowerShell-konzollal könnyen létrehozhat egy virtuális hálózatot. Állítsa be az értékeket, majd másolja és illessze be a parancsokat a konzolablakba.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Módosítsa a PowerShell-parancsokat, majd hozzon létre egy erőforráscsoportot.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Virtuális hálózat létrehozása

Módosítsa a PowerShell-parancsokat, hogy egy olyan virtuális hálózatot hozzon létre, amely kompatibilis a környezettel.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzureRmVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```

## <a name="wan"></a>3. Virtuális WAN létrehozása

1. Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.
2. Most a Virtual WAN eléréséhez lépjen a **Minden szolgáltatás** felületre, és keressen rá a Virtual WAN kifejezésre. Vagy az Azure Portal tetején lévő keresőmezőben is rákereshet a Virtual WAN kifejezésre. Kattintson a **Virtual WAN** elemre a lap megnyitásához.
3. A **WAN létrehozása** lap megnyitásához kattintson a **Létrehozás** gombra. Ha a lap nem érhető el, az előzetes verzióhoz való hozzáférési jogosultsága nem lett még engedélyezve.

  ![WAN létrehozása](./media/virtual-wan-site-to-site-portal/createwan.png)
4. A WAN létrehozása lapon töltse ki a következő mezőket.

  * **Név** – Válasszon egy tetszés szerinti nevet a WAN számára.
  * **Előfizetés** – Válassza ki a használni kívánt előfizetést.
  * **Erőforráscsoport** – Hozzon létre egy új erőforráscsoportot, vagy használjon egy meglévőt.
  * **Erőforrás helye** – Válassza ki az erőforrás helyét a legördülő listából. A WAN egy globális erőforrás, és nem egy adott régióhoz tartozik. Mindazonáltal mégis ki kell választania egy régiót, hogy könnyebben kezelhesse és megtalálhassa a létrehozott WAN-erőforrást.
5. A WAN létrehozásához kattintson a **Létrehozás** elemre.

## <a name="site"></a>4. Hely létrehozása

Hozzon létre annyi helyet, amennyit csak szükséges a fizikai helyeknek megfelelően. Például ha New Yorkban, Londonban és Los Angelesben van egy-egy helyi irodája, három külön helyet hozzon létre. Ezek a helyek tartalmazzák a helyszíni VPN-eszközök végpontjait. Jelenleg csak egyetlen privát címteret adhat meg a helyekhez.

1. Lépjen a **Minden erőforrás** menüpontra.
2. Kattintson a létrehozott virtuális WAN-ra.
3. Kattintson az oldal tetején a **+Hely létrehozása** lehetőségre a **Hely létrehozása** lap megnyitásához.

  ![új hely](media/virtual-wan-site-to-site-portal/createsite.png)
4. A **Hely létrehozása** lapon töltse ki a következő mezőket:

  *  **Név** – Ez az a név, amellyel hivatkozni szeretne a helyszíni helyre.
  *  **Nyilvános IP-cím** – Ez a helyszíni helyen található VPN-eszköz nyilvános IP-címe.
  *  **Privát címtér** – Ez a helyszíni helyen található IP-címtér. Az erre a címtérre címzett forgalom át lesz irányítva a helyszíni helyre.
  *  **Előfizetés** – Ellenőrizze az előfizetést.
  *  **Erőforráscsoport** – A használni kívánt erőforráscsoport.
5. A további beállítások megtekintéséhez kattintson a **Speciális megjelenítése** gombra. **Engedélyezheti a BGP-t** (opcionális mező), amely az Azure-ban az ehhez a helyhez létrehozott összes kapcsolatra engedélyezi ezt a funkciót. Megadhatja az **eszközökkel kapcsolatos információkat** is (opcionális mező). Az Azure Team így jobban megismerheti a környezetet, és a jövőben így további optimalizálási lehetőségeket adhat a szolgáltatáshoz, valamint ezzel elősegítheti a hibaelhárítást.

  ![BGP](media/virtual-wan-site-to-site-portal/sitebgp.png)
6. Kattintson a **Megerősítés** gombra a hely létrehozásához.
7. Ismételje meg a fenti lépéseket minden létrehozni kívánt hely esetében.

## <a name="hub"></a>5. Elosztó létrehozása és helyek csatlakoztatása

1. A virtuális WAN lapján kattintson a **Helyek** elemre.
2. A **Társítatlan webhelyek** területen láthatja azoknak a helyeknek a listáját, amelyek még nem lettek elosztóhoz csatlakoztatva.
3. Jelölje ki azokat a helyeket, amelyeket társítani kíván.
4. A legördülő menüből válassza ki azt a régiót, amelyhez az elosztó társítva lesz. Az elosztót azzal a régióval kell társítania, ahol a csatlakoztatni kívánt virtuális hálózatok találhatók.
5. Kattintson a **Megerősítés** gombra. Ha a régióban nincs elosztó, a rendszer automatikusan létrehoz egy virtuális elosztói virtuális hálózatot. Ebben az esetben a **Regionális központok létrehozása** lap jelenik meg.
6. A **Regionális központok létrehozása** lapon adja meg a virtuális hálózat címtartományát. Ez az a virtuális hálózat, amely az elosztószolgáltatásokat tartalmazni fogja. Az itt megadott tartománynak privát IP-címtartománynak kell lennie, és nem lehet átfedésben egyik helyszíni címtérrel vagy a virtuális hálózat egyik címterével sem. Ezután a rendszer létrehoz egy VPN-végpontot az elosztó virtuális hálózatán. (Az elosztók és az átjárók automatikus létrehozása csak a portálon érhető el.)
7. Kattintson a **Create** (Létrehozás) gombra.

## <a name="vnet"></a>6. A virtuális hálózat csatlakoztatása egy elosztóhoz

Ebben a lépésben a társviszony-kapcsolatot hozzuk létre az elosztó és egy virtuális hálózat között. Ismételje meg a fenti lépéseket minden csatlakoztatni kívánt virtuális hálózat esetében.

1. A virtuális WAN lapján kattintson a **Virtuális hálózati kapcsolat** elemre.
2. A virtuális hálózati kapcsolat lapján kattintson a **+Kapcsolat hozzáadása** elemre.
3. A **Kapcsolat hozzáadása** lapon töltse ki a következő mezőket:

    * **Kapcsolat neve** – Nevezze el a kapcsolatot.
    * **Elosztók** – Válassza ki azt az elosztót, amelyet a kapcsolattal társítani kíván.
    * **Előfizetés** – Ellenőrizze az előfizetést.
    * **Virtuális hálózat** – Válassza ki azt a virtuális hálózatot, amelyet az elosztóhoz csatlakoztatni kíván. A virtuális hálózat nem rendelkezhet már meglévő virtuális hálózati átjáróval.

## <a name="device"></a>7. VPN-konfiguráció letöltése

A VPN-eszköz konfigurációjának használatával konfigurálhatja a helyszíni VPN-eszközöket.

1. A virtuális WAN lapján kattintson az **Áttekintés** elemre.
2. Az Áttekintés oldal tetején kattintson a **VPN-konfiguráció letöltése** lehetőségre. Az Azure létrehoz egy tárfiókot a „microsoft-network-[hely]” erőforráscsoportban, ahol a hely a WAN helye. Miután a konfigurációt alkalmazta a VPN-eszközökre, törölheti ezt a tárfiókot.
3. Miután befejeződött a fájl létrehozása, a hivatkozásra kattintva letöltheti.
4. Alkalmazza a konfigurációt a VPN-eszközre.

### <a name="understanding-the-vpn-device-configuration-file"></a>VPN-eszköz konfigurációs fájljának ismertetése

Az eszközkonfigurációs fájl a helyszíni VPN-eszköz konfigurálása során használandó beállításokat tartalmazza. A fájl áttekintésekor a következő információkat láthatja:

* **vpnSiteConfiguration** – Ez a szakasz az eszköz a virtuális WAN-ra csatlakozó helyként való telepítésére vonatkozó adatait tartalmazza. Az ágeszköz nevét és nyilvános IP-címét tartalmazza.
* **vpnSiteConnections** – Ez a szakasz a következőkről nyújt információkat:

    * A virtuális elosztó(k) virtuális hálózatának **címtere**<br>Példa:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * Az elosztóra csatlakozó virtuális hálózatok **címtere**<br>Példa:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * A virtuális hálózat VPN-átjárójának **IP-címei** Mivel a VPN-átjáró mindegyik kapcsolata 2 alagutat tartalmaz aktív-aktív konfigurációban, a fájlban mindkét IP-cím szerepel. Ebben a példában mindegyik helyhez az „Instance0” és az „Instance1” van feltüntetve.<br>Példa:

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
> Amennyiben Virtual WAN-partnermegoldást használ, a VPN-eszköz konfigurációja automatikusan ott megy végbe, ahol az eszközvezérlő megkapja a konfigurációs fájlt az Azure-ból, és alkalmazza azt az eszközre az Azure-kapcsolat beállításához. Ez azt jelenti, hogy a VPN-eszköz manuális konfigurálásának menetét nem kell ismernie.
>

Amennyiben útmutatásra van szüksége az eszköz konfigurálásához, használhatja a [VPN-eszközkonfigurációs szkriptek lapon](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) található utasításokat az alábbi kikötésekkel:

* A VPN-eszközök lapon található utasítások nem a Virtual WAN-hoz íródtak, a Virtual WAN a konfigurációs fájlban lévő értékeivel manuálisan konfigurálhatja a VPN-eszközt. 
* A VPN Gatewayre vonatkozó letölthető eszközkonfigurációs szkriptek a Virtual WAN esetében nem működnek, mivel a konfiguráció eltér.
* A Virtual WAN kizárólag az IKEv2-t képes használni, az IKEv1-et nem.
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

Ha már nincs szükség ezekre az erőforrásokra, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást. A „myResourceGroup” helyére írja be az erőforráscsoport nevét, és futtassa a következő PowerShell-parancsot:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="feedback"></a>Az előzetes verzióval kapcsolatos visszajelzés

Nagya értékeljük a visszajelzését. Amennyiben a Virtual WAN-nal kapcsolatos problémát szeretne bejelenteni, vagy (pozitív vagy negatív) visszajelzést küldene, küldjön egy e-mailt az <azurevirtualwan@microsoft.com> címre. Adja meg a vállalat nevét szögletes zárójelek ([]) között az e-mail tárgymezőjében. Emellett, amennyiben hibát jelent be, adja meg az előfizetése azonosítóját is.

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
