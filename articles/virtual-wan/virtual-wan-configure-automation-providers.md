---
title: Az Azure virtuális WAN-partnerek |} A Microsoft Docs
description: Ez a cikk segít az Azure virtuális WAN az automation beállítása partnerek.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/23/2018
ms.author: cherylmc
Customer intent: As a Virtual WAN software-defined connectivity provider, I want to set up a provisioning environment.
ms.openlocfilehash: 7f70470880845fd4271ffdbb35af771ec433babc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961943"
---
# <a name="virtual-wan-partners-preview"></a>Virtuális WAN partnerek (előzetes verzió)

Ez a cikk segít megérteni az automation-környezetet csatlakozhat, és az Azure virtuális WAN ág eszközök (ügyfél helyi VPN-eszközt vagy SDWAN CPE) konfigurálása. Ha Ön egy szolgáltató, amely tudja fogadni a VPN-kapcsolat IPsec vagy IKEv2-vagy az IPsec vagy IKEv1 ág eszközöket biztosít, ez a cikk Önnek szól.

Egy fiókiroda eszközt (ügyfél helyi VPN-eszköz vagy SDWAN CPE) általában használ egy tartományvezérlő/eszköz irányítópultot ki kell építeni. SD-WAN megoldás a rendszergazdák a felügyeleti konzollal gyakran eszköz előzetes kiépítése előtt lekérdezi csatlakoztatva a hálózathoz. A kompatibilis VPN-eszköz beolvasása a vezérlési sík logika egy vezérlőt. A VPN-eszköz vagy az SD-WAN-vezérlő segítségével az Azure API-k automatizálása Azure virtuális WAN kapcsolódni. Ez a kapcsolattípus igényel a helyszíni eszköz a kifelé irányuló nyilvános IP-cím rendelve.

## <a name ="before"></a>A telepítés megkezdése előtt automatizálása

* Győződjön meg arról, hogy az eszköz támogatja-e az IPsec IKEv1 vagy IKEv2. Lásd: [alapértelmezett házirendek](#default).
* Tekintse meg a [REST API-k](https://docs.microsoft.com/rest/api/azure/) , hogy segítségével automatizálhatja az Azure virtuális WAN kapcsolódni.
* Az Azure virtuális WAN-portál lehetőségek kipróbálásához.
* Ezután döntse el, melyik részére a kapcsolat lépéseket szeretne automatizálni. Minimális ajánlott automatizálása:

  * Access Control
  * Az Azure virtuális WAN ág eszközadatokat feltöltése
  * Letöltése az Azure konfigurálása és a kapcsolat beállítása az Azure virtuális WAN ág eszközről

* Ismerje meg a várt vásárlói élményt, az Azure virtuális WAN együtt.

  1. Általában egy virtuális WAN felhasználó elindítja egy virtuális WAN erőforrás létrehozásával.
  2. A felhasználó egy szolgáltatás egyszerűszolgáltatás-alapú erőforrás-csoport hozzáférés a helyszíni rendszer (a fiókirodai vezérlő vagy a VPN-eszköz kiépítési szoftver) írása a munkaág adatai az Azure virtuális WAN beállítja.
  3. A felhasználó jelentkezzen be a felhasználói felület és a szolgáltatásnév hitelesítő adatainak beállítása most határozhat meg. Ha végzett, a vezérlő képesnek kell lennie az Ön biztosítja az automation-ág adatok feltöltéséhez. Az Azure részéről az e manuális megfelelője a hely létrehozása.
  4. A helyadatok (ág eszköz) az Azure-ban érhető el, ha a felhasználó a helyet, hogy a hub társítja. Egy virtuális központtal egy Microsoft által felügyelt virtuális hálózatot. Az elosztó különféle szolgáltatásvégpontokat tartalmaz a helyszíni hálózatból (vpnsite) induló kapcsolatok biztosításához. Az elosztó a hálózat központja egy adott régióban. Csak lehet Azure-régiónként egy eseményközpont, és a vpn-végpont (VPN-átjáró), a folyamat során jön létre. A VPN-átjáró mely méretek megfelelően alapján sávszélesség- és igényeinek megfelelően méretezhető átjáró. Dönthet úgy automatizálható virtuális központ és az ág eszköz vezérlő irányítópultról VPN-átjáró létrehozása.
  5. Miután a virtuális központ, a helyhez társított, való manuális letöltéséhez a felhasználó egy konfigurációs fájl jön létre. Ez az, ahol az automation érhető el, és így zökkenőmentes felhasználói élményt. Helyett a felhasználó nem kell manuálisan töltse le és a fiókiroda-eszköz konfigurálásához beállíthatja az automatizálás és minimális átkattintásos élményt biztosítsanak a felhasználói felület, ezáltal enyhítése jellemző csatlakozási problémák, például a megosztott kulcsa eltérő, az IPSec-paraméter eltérés, a konfigurációs fájl olvashatóság stb.
  6. A megoldásban ez a lépés végén a felhasználónak kell egy zökkenőmentes helyek közötti kapcsolat az ág eszköz és a virtuális központ között. Is állíthat be további kapcsolatok más hubok között. Minden kapcsolat egy aktív-aktív alagutat. Az ügyfél dönthet az egyes az alagutat a hivatkozások különböző Internetszolgáltató használata.

## <a name ="understand"></a>Automation részletes ismertetése


###  <a name="access"></a>Hozzáférés-vezérlés

Ügyfelek való megfelelő hozzáférés-vezérlés virtuális WAN az eszköz felhasználói Felületéhez a képesnek kell lennie. Ez egy egyszerű Azure-szolgáltatás használata javasolt. Szolgáltatás egyszerű-alapú hozzáférés biztosít a tartományvezérlő megfelelő eszközhitelesítés ág adatok feltöltéséhez. További információkért lásd: [egyszerű szolgáltatás létrehozása](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Bár ez a funkció az Azure virtuális WAN ajánlat kívül, azt alább a jellemzően előforduló lépéseket tett az Azure-ban utána a releváns adatokat képkockák az eszköz felügyeleti irányítópulton való hozzáférés beállítása listázása

* Hozzon létre egy Azure Active Directory-alkalmazás a helyi eszközön vezérlő.
* Alkalmazásazonosító és hitelesítési kulcs beszerzése
* A bérlőazonosító beszerzése
* Alkalmazás hozzárendelése szerepkörhöz "Közreműködő"

###  <a name="branch"></a>Töltse fel a fiókiroda eszköz adatai

Tervezze meg a felhasználói élmény ág (a helyszíni hely) adatok feltöltése az Azure-bA. [REST API-k](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) számára a helyadatok létrehozása a virtuális WAN VPNSite használható. Adja meg az összes ág SDWAN/VPN-eszközök, vagy válassza ki az eszközt a testreszabások szükség szerint.


### <a name="device"></a>Eszköz konfigurációjának letöltéséhez és a kapcsolat

Ennek a lépésnek része a letöltése az Azure konfigurálása és a kapcsolat beállítása az Azure virtuális WAN ág eszközről. Ebben a lépésben, amelyet nem használ egy szolgáltatót manuálisan töltse le az Azure konfigurálása és alkalmazza azt a helyszíni SDWAN/VPN-eszköz. Szolgáltatóként automatizálni kell ezt a lépést. Az eszközvezérlő meghívhat "GetVpnConfiguration" REST API-t az Azure konfigurációs, és általában hasonlóan néz ki a következő fájl letöltéséhez.

**Konfigurációs megjegyzések**

  * Azure virtuális hálózatokhoz is csatlakozik a virtuális központ, ha azok ConnectedSubnets fog megjelenni.
  * VPN-kapcsolat az útválasztó-alapú konfigurációs és az IKEv2 vagy IKEv1 használja.

#### <a name="understanding-the-device-configuration-file"></a>Az eszköz konfigurációs fájl ismertetése

Az eszközkonfigurációs fájl a helyszíni VPN-eszköz konfigurálása során használandó beállításokat tartalmazza. A fájl áttekintésekor a következő információkat láthatja:

* **vpnSiteConfiguration** – Ez a szakasz az eszköz a virtuális WAN-ra csatlakozó helyként való telepítésére vonatkozó adatait tartalmazza. Az ágeszköz nevét és nyilvános IP-címét tartalmazza.
* **vpnSiteConnections** – Ez a szakasz a következőkről nyújt információkat:

    * **Címtér** , a virtuális hálózat virtuális példáink.<br>Példa:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Címtér** a virtuális hálózatokkal, csatlakozik a hubhoz.<br>Példa:

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

## <a name="default"></a>Alapértelmezett szabályzatok az IPsec-kapcsolat

### <a name="initiator"></a>Kezdeményező

Az alábbi szakaszok tartalmazzák a szabályzat támogatott kombinációk, Azure esetén a kezdeményező az alagút számára.

**1. fázis**

* AES_256-TAL, SHA1, DH_GROUP_2
* AES_256-TAL, SHA_256, DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* A 3DES, SHA1, DH_GROUP_2
* A 3DES, SHA_256, DH_GROUP_2

**2. fázis**

* GCM_AES_256, GCM_AES_256, PFS_NONE
* AES_256-TAL, SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256-TAL, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE

### <a name="responder"></a>Válaszadó

Az alábbi szakaszok tartalmazzák a szabályzat támogatott kombinációk, Azure esetén az válaszadó az alagút számára.

**1. fázis**

* AES_256-TAL, SHA1, DH_GROUP_2
* AES_256-TAL, SHA_256, DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* A 3DES, SHA1, DH_GROUP_2
* A 3DES, SHA_256, DH_GROUP_2

**2. fázis**

* GCM_AES_256, GCM_AES_256, PFS_NONE
* AES_256-TAL, SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256-TAL, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE
* CBC_DES, SHA_1, PFS_NONE 
* AES_256-TAL, SHA_1, PFS_1
* AES_256-TAL, SHA_1, PFS_2
* AES_256-TAL, SHA_1, PFS_14
* AES_128, SHA_1, PFS_1
* AES_128, SHA_1, PFS_2
* AES_128, SHA_1, PFS_14
* CBC_3DES, SHA_1, PFS_1
* CBC_3DES, SHA_1, PFS_2
* CBC_3DES, SHA_256, PFS_2
* AES_256-TAL, SHA_256, PFS_1
* AES_256-TAL, SHA_256, PFS_2
* AES_256-TAL, SHA_256, PFS_14
* AES_256-TAL, SHA_1, PFS_24
* AES_256-TAL, SHA_256, PFS_24
* AES_128, SHA_256, PFS_NONE
* AES_128, SHA_256, PFS_1
* AES_128, SHA_256, PFS_2
* AES_128, SHA_256, PFS_14
* CBC_3DES, SHA_1, PFS_14

### <a name="does-everything-need-to-match-between-the-virtual-hub-vpngateway-policy-and-my-on-premises-sdwanvpn-device-or-sd-wan-configuration"></a>Nem minden meg kell egyeznie a VPN-átjáró virtuális központ házirend és a saját helyszíni SDWAN/VPN-eszköz vagy SD-WAN konfigurációs között?

A helyszíni SDWAN/VPN-eszközt vagy az SD-WAN konfiguráció felel meg, vagy tartalmaznak, az alábbi algoritmusokkal és paraméterekkel, amelyet az Azure IPsec/IKE-szabályzat.

* IKE titkosítási algoritmus
* IKE integritási algoritmus
* DH-csoport
* IPsec titkosítási algoritmus
* IPsec integritási algoritmus
* PFS-csoport

## <a name="next-steps"></a>További lépések

Virtuális WAN kapcsolatos további információkért lásd: [kapcsolatos Azure virtuális WAN](virtual-wan-about.md) és a [Azure virtuális WAN GYIK](virtual-wan-faq.md).

További információkért, küldjön egy e-mailek <azurevirtualwan@microsoft.com>. Adja meg a vállalat nevét szögletes zárójelek ([]) között az e-mail tárgymezőjében.
