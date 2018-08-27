---
title: Konfigurálja az Azure virtuális WAN automation – virtuális WAN-partnerek számára |} A Microsoft Docs
description: Ez a cikk segít az Azure virtuális WAN az automation beállítása szoftveresen definiált kapcsolat partnerei.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: cherylmc
Customer intent: As a Virtual WAN software-defined connectivity provider, I want to set up a provisioning environment.
ms.openlocfilehash: bac728f286c90550107b27da76a070623577ed82
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918900"
---
# <a name="configure-virtual-wan-automation---for-virtual-wan-partners-preview"></a>Virtuális WAN automation – a virtuális WAN-partnerek számára (előzetes verzió) konfigurálása

Ez a cikk segít megérteni az automation-környezetet csatlakozhat, és az Azure virtuális WAN ág eszközök (ügyfél helyi VPN-eszközt vagy SDWAN) konfigurálása. Ha Ön egy szolgáltató által biztosított ág eszközöket képes kezelni a VPN-kapcsolat IPsec vagy IKEv2 keresztül, ez a cikk Önnek szól.

Szoftver-meghatározott csatlakozási megoldásokat általában egy vezérlőt, vagy egy eszköz-üzembehelyezési center kezelésére használja ág eszközeiket. A vezérlő Azure API-k segítségével automatizálhatja az Azure virtuális WAN kapcsolódni. Ez a kapcsolattípus SDWAN vagy VPN-eszközre helyszíni, amely rendelkezik egy kifelé irányuló nyilvános IP-cím rendelve van szükség.

##  <a name="access"></a>Hozzáférés-vezérlés

Ügyfelek való megfelelő hozzáférés-vezérlés virtuális WAN az eszköz felhasználói Felületéhez a képesnek kell lennie. Ez egy egyszerű Azure-szolgáltatás használata javasolt. Szolgáltatás egyszerű-alapú hozzáférés biztosít a tartományvezérlő megfelelő eszközhitelesítés ág adatok feltöltéséhez. További információkért lásd: [egyszerű szolgáltatás létrehozása](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).

##  <a name="site"></a>Fiókiroda-adatok feltöltése

Tervezze meg a felhasználói élmény ág (a helyszíni hely) adatok feltöltése az Azure-bA. [REST API-k](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) a **VPNSite** virtuális WAN létrehozása a helyadatok segítségével. Adja meg az összes ág SDWAN/VPN-eszközök, vagy válassza ki az eszközt a testreszabások szükség szerint.

##  <a name="hub"></a>Eseményközpont és a szolgáltatások

Az Azure-bA az ág eszköz feltöltését követően ügyfél központ régiója és/vagy szolgáltatások beállításokat fogja elérhetővé tenni általában az Azure Portalon, amely meghív egy műveletkészletet hozhat létre a központi virtuális hálózaton és a VPN-végpontot a hub belül. A VPN-átjáró mely méretek megfelelően alapján sávszélesség- és igényeinek megfelelően méretezhető átjáró.

## <a name="device"></a>Eszközök konfigurálása

Ebben a lépésben, amelyet nem használ egy szolgáltatót manuálisan töltse le az Azure konfigurálása és alkalmazza azt a helyszíni SDWAN/VPN-eszköz. Szolgáltatóként automatizálni kell ezt a lépést. Az adatkezelő segítségével meghívhatja **GetVpnConfiguration** REST API-t az Azure konfigurációs, és általában hasonlóan néz ki a következő fájl letöltéséhez.

**Konfigurációs megjegyzések**

  * Azure virtuális hálózatokhoz is csatlakozik a virtuális központ, ha azok ConnectedSubnets fog megjelenni.
  * VPN-kapcsolat konfigurációs útvonalalapú és az IKEv2 használ.

### <a name="understanding-the-device-configuration-file"></a>Az eszköz konfigurációs fájl ismertetése

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

## <a name="default"></a>Az alapértelmezett házirendek

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

A helyszíni SDWAN/VPN-eszközt vagy az SD-WAN konfiguráció felel meg, vagy tartalmaznak, az alábbi algoritmusokkal és paraméterekkel, amelyet az Azure IPsec/IKE-szabályzat. Az SA-élettartam helyi, és nem kell egyeznie.

* IKE titkosítási algoritmus
* IKE integritási algoritmus
* DH-csoport
* IPsec titkosítási algoritmus
* IPsec integritási algoritmus
* PFS-csoport

## <a name="feedback"></a>Az előzetes verzióval kapcsolatos visszajelzés

Nagya értékeljük a visszajelzését. Amennyiben a Virtual WAN-nal kapcsolatos problémát szeretne bejelenteni, vagy (pozitív vagy negatív) visszajelzést küldene, küldjön egy e-mailt az <azurevirtualwan@microsoft.com> címre. Adja meg a vállalat nevét szögletes zárójelek ([]) között az e-mail tárgymezőjében. Emellett, amennyiben hibát jelent be, adja meg az előfizetése azonosítóját is.

## <a name="next-steps"></a>További lépések

Virtuális WAN kapcsolatos további információkért lásd: [kapcsolatos Azure virtuális WAN](virtual-wan-about.md) és a [Azure virtuális WAN GYIK](virtual-wan-faq.md).
