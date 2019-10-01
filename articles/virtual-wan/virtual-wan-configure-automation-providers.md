---
title: Azure-beli virtuális WAN-partnerek | Microsoft Docs
description: Ez a cikk segítséget nyújt a partnereknek az Azure Virtual WAN Automation üzembe állításában.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: cherylmc
ms.openlocfilehash: 72493f084b89d41c1e0d6ff60c35afa3491b0eda
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703458"
---
# <a name="virtual-wan-partners"></a>Virtuális WAN-partnerek

Ebből a cikkből megtudhatja, hogyan állíthatja be az automatizálási környezetet egy fiókirodai eszköz (helyszíni VPN-eszköz vagy SDWAN CPE) Azure Virtual WAN-hoz való csatlakoztatásához és konfigurálásához. Ha Ön olyan szolgáltató, amely az IPsec/IKEv2 vagy az IPsec/IKEv1 VPN-kapcsolaton keresztül képes fiókirodai eszközöket biztosít, akkor ez a cikk Önnek szól.

Az ág-eszköz (a helyszíni VPN-eszköz vagy a SDWAN CPE) általában egy vezérlő/eszköz irányítópultot használ. Az SD-WAN megoldás-rendszergazdák gyakran egy felügyeleti konzol használatával előre kiépíthetik az eszközöket a hálózatba való csatlakoztatás előtt. Ez a VPN-kompatibilis eszköz lekéri a vezérlő sík logikáját egy vezérlőből. A VPN-eszköz vagy az SD-WAN-vezérlő az Azure API-k segítségével automatizálhatja az Azure Virtual WAN-hoz való kapcsolódást. Az ilyen típusú kapcsolathoz a helyszíni eszköznek hozzá kell rendelnie egy kívülről álló nyilvános IP-címet.

## <a name ="before"></a>Az automatizálás megkezdése előtt

* Ellenőrizze, hogy az eszköz támogatja-e az IPsec IKEv1/IKEv2. Lásd: [alapértelmezett házirendek](#default).
* Tekintse meg azokat a [REST API-kat](https://docs.microsoft.com/rest/api/azure/) , amelyeket az Azure Virtual WAN-hoz való kapcsolódás automatizálásához fog használni.
* Próbálja ki az Azure Virtual WAN portáljának tapasztalatait.
* Ezután döntse el, hogy a kapcsolódási lépések melyik részét szeretné automatizálni. Legalább az automatizálást javasoljuk:

  * Hozzáférés-vezérlés
  * Ág-eszköz adatainak feltöltése az Azure Virtual WAN-ba
  * Az Azure-konfiguráció letöltése és az ág-eszköz kapcsolatának beállítása az Azure Virtual WAN-ba

* Ismerje meg a várt felhasználói élményt az Azure Virtual WAN szolgáltatással együtt.

  1. Egy virtuális WAN-felhasználó általában egy virtuális WAN-erőforrás létrehozásával indítja el a folyamatot.
  2. A felhasználó létrehoz egy egyszerű szolgáltatáshoz tartozó erőforráscsoport-hozzáférést a helyszíni rendszerhez (az Ön fiókirodai vagy VPN-eszközének kiépítési szoftveréhez), amely a fiókirodák adatait az Azure Virtual WAN-ba írja.
  3. A felhasználó ebben az időben dönthet úgy, hogy bejelentkezik a felhasználói felületen, és beállítja a szolgáltatás egyszerű hitelesítő adatait. Ha ez befejeződik, a vezérlőnek képesnek kell lennie az ág adatainak feltöltésére az Ön által biztosított automatizálással. Az Azure-oldalon ennek manuális megfelelője a "hely létrehozása".
  4. Ha a hely (ág eszköz) adatai elérhetők az Azure-ban, a felhasználó csatlakoztatni fogja a helyet egy központhoz. A virtuális központ egy Microsoft által felügyelt virtuális hálózat. Az elosztó különféle szolgáltatásvégpontokat tartalmaz a helyszíni hálózatból (vpnsite) induló kapcsolatok biztosításához. Az elosztó a hálózat központja egy adott régióban. Az Azure-régiókban és a benne található VPN-végponton (átjáróban) belül csak egy központ hozható létre a folyamat során. A VPN Gateway egy méretezhető átjáró, amely a sávszélesség és a kapcsolati igények alapján megfelelő méreteket biztosít. Dönthet úgy, hogy automatizálja a virtuális központot és a átjáróban-létrehozást az ág-eszköz vezérlő irányítópultján.
  5. Ha a virtuális központ hozzá van rendelve a helyhez, a rendszer létrehoz egy konfigurációs fájlt a felhasználó számára a manuális letöltéshez. Ebben az esetben az automatizálás bekerül a szolgáltatásba, és zökkenőmentesen teszi a felhasználói élményt. Ahelyett, hogy a felhasználó manuálisan le kell töltenie és konfigurálnia kell a fiókirodai eszközt, beállíthatja az automatizálást, és minimális kattintási élményt biztosíthat a felhasználói felületen, így csökkentve a tipikus kapcsolódási problémákat, például a megosztott kulcsokat, az IPSec paramétert eltérés, konfigurációs fájl olvashatósága stb.
  6. Ennek a lépésnek a végén a felhasználó zökkenőmentes helyek közötti kapcsolattal fog rendelkezni a fiókiroda és a virtuális központ között. További kapcsolatokat is beállíthat más hubokon. Az egyes kapcsolatok aktív-aktív alagutak. Az ügyfél dönthet úgy, hogy egy másik INTERNETSZOLGÁLTATÓt használ az alagúthoz tartozó összes hivatkozáshoz.
  7. Érdemes lehet hibaelhárítási és figyelési funkciókat biztosítani a CPE felügyeleti felületén. A tipikus forgatókönyvek közé tartozik például az "ügyfél nem tud hozzáférni az Azure-erőforrásokhoz egy CPE-probléma miatt", "az IPsec-paraméterek megjelenítése a CPE oldalán" stb.

## <a name ="understand"></a>Az Automation részleteinek megismerése


###  <a name="access"></a>Hozzáférés-vezérlés

Az ügyfeleknek képesnek kell lenniük a virtuális WAN megfelelő hozzáférés-vezérlésének beállítására az eszköz felhasználói felületén. Ezt az Azure egyszerű szolgáltatásnév használatával ajánlott használni. Az egyszerű szolgáltatás-alapú hozzáférés biztosítja az eszköz megfelelő hitelesítését a fiókirodai adatok feltöltéséhez. További információ: [egyszerű szolgáltatásnév létrehozása](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Habár ez a funkció az Azure-beli virtuális WAN-ajánlaton kívül esik, a hozzáférés az Azure-ban való beállításának szokásos lépésein látható, amely után a vonatkozó részletek bekerülnek az Eszközkezelő irányítópultba

* Hozzon létre egy Azure Active Directory alkalmazást a helyszíni eszköz-vezérlőhöz.
* Alkalmazás-azonosító és hitelesítési kulcs beszerzése
* A bérlőazonosító beszerzése
* Alkalmazás társítása a (z) közreműködő szerepkörhöz

###  <a name="branch"></a>Ág-eszköz adatainak feltöltése

Tervezze meg a felhasználói élményt az ág (helyszíni hely) adatainak az Azure-ba való feltöltéséhez. A VPNSite [REST API-jai](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) a hely adatainak létrehozásához használhatók a virtuális WAN-ban. Megadhatja az összes ág SDWAN/VPN-eszközt, vagy kiválaszthatja a megfelelő eszközök testreszabását.


### <a name="device"></a>Eszközök konfigurációjának letöltése és kapcsolata

Ez a lépés az Azure-konfiguráció letöltését és a fiókirodai eszköz és az Azure Virtual WAN közötti kapcsolat beállítását foglalja magában. Ebben a lépésben a szolgáltatót nem használó ügyfél manuálisan letölti az Azure-konfigurációt, és alkalmazza azt a helyszíni SDWAN/VPN-eszközre. Szolgáltatóként automatizálja ezt a lépést. Az Eszközkezelő a "GetVpnConfiguration" REST API hívhatja az Azure-konfiguráció letöltéséhez, amely általában a következő fájlhoz hasonlóan fog kinézni.

**Konfigurációs megjegyzések**

  * Ha az Azure-virtuális hálózatok a virtuális hubhoz vannak csatlakoztatva, a ConnectedSubnets-ként fognak megjelenni.
  * A VPN-kapcsolat Route-alapú konfigurációt használ, és támogatja a IKEv1 és a IKEv2 protokollt is.

#### <a name="understanding-the-device-configuration-file"></a>Az eszköz konfigurációs fájljának ismertetése

Az eszközkonfigurációs fájl a helyszíni VPN-eszköz konfigurálása során használandó beállításokat tartalmazza. A fájl áttekintésekor a következő információkat láthatja:

* **vpnSiteConfiguration** – Ez a szakasz az eszköz a virtuális WAN-ra csatlakozó helyként való telepítésére vonatkozó adatait tartalmazza. Az ágeszköz nevét és nyilvános IP-címét tartalmazza.
* **vpnSiteConnections** – Ez a szakasz a következőkről nyújt információkat:

    * A virtuális központ (ok) VNet **címe** .<br>Példa:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **A hubhoz** csatlakozó virtuális hálózatok.<br>Példa:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * A virtuális hálózat VPN-átjárójának **IP-címei** Mivel a VPN-átjáró mindegyik kapcsolata 2 alagutat tartalmaz aktív-aktív konfigurációban, a fájlban mindkét IP-cím szerepel. Ebben a példában mindegyik helyhez az „Instance0” és az „Instance1” van feltüntetve.<br>Példa:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * A **VPN-átjáró kapcsolati konfigurációjának részletei**, például BGP, előre megosztott kulcs stb. A rendszer által automatikusan létrehozott előre megosztott kulcs a PSK. A kapcsolatot az egyéni PSK Áttekintés lapján bármikor szerkesztheti.
  
#### <a name="example-device-configuration-file"></a>Eszközkonfigurációs példafájl

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

## <a name="default"></a>IPsec-kapcsolat alapértelmezett házirendjei

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-include.md)]

### <a name="does-everything-need-to-match-between-the-virtual-hub-vpngateway-policy-and-my-on-premises-sdwanvpn-device-or-sd-wan-configuration"></a>Mindent meg kell egyeznie a virtuális központ átjáróban házirendje és a helyszíni SDWAN/VPN-eszköz vagy az SD-WAN konfiguráció között?

A helyszíni SDWAN/VPN-eszköznek vagy az SD-WAN konfigurációnak meg kell egyeznie a következő algoritmusokkal és paraméterekkel, amelyeket az Azure IPsec/IKE-házirendben adhat meg.

* IKE titkosítási algoritmus
* IKE integritási algoritmus
* DH-csoport
* IPsec titkosítási algoritmus
* IPsec integritási algoritmus
* PFS-csoport

## <a name="next-steps"></a>További lépések

További információ a virtuális WAN-ról: az [Azure Virtual WAN](virtual-wan-about.md) és az [Azure Virtual WAN gyakori kérdései](virtual-wan-faq.md).

További információkért kérjük, küldjön e-mailt a <azurevirtualwan@microsoft.com> címre. Adja meg a vállalat nevét szögletes zárójelek ([]) között az e-mail tárgymezőjében.