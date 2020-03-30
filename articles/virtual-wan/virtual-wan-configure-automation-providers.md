---
title: Az Azure Virtual WAN-partnerek automatizálási irányelvei | Microsoft dokumentumok
description: Ez a cikk segít a partnereknek az Azure Virtual WAN-automatizálás beállításában.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: cherylmc
ms.openlocfilehash: 7848dda09b39f446dd218b7ce1eb2a07664bcaa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190420"
---
# <a name="automation-guidelines-for-virtual-wan-partners"></a>Automatizálási irányelvek a Virtual WAN-partnerek számára

Ez a cikk segít megérteni, hogyan állíthatja be az automatizálási környezetet egy elágazási eszköz (egy helyszíni VPN-eszköz vagy SDWAN CPE) csatlakoztatásához és konfigurálásához az Azure Virtual WAN számára. Ha Ön olyan szolgáltató, amely olyan ágeszközöket biztosít, amelyek képesek a VPN-kapcsolat befogadására Az IPsec/IKEv2 vagy az IPsec/IKEv1 felett, ez a cikk az Ön számára szól.

A fiókeszköz (egy helyszíni VPN-ügyfél vagy SDWAN CPE) általában egy vezérlő/eszköz irányítópultot használ a kiépítéshez. Az SD-WAN megoldásrendszergazdák gyakran használhatnak felügyeleti konzolt az eszköz előzetes kiépítésére, mielőtt az csatlakoztatva lenne a hálózathoz. Ez a VPN-képes eszköz a vezérlősík logikáját egy vezérlőtől kapja. A VPN-eszköz vagy az SD-WAN-vezérlő azure API-k használatával automatizálhatja az Azure Virtual WAN-hoz való kapcsolódást. Az ilyen típusú kapcsolat megköveteli, hogy a helyszíni eszköz külsőleg néző nyilvános IP-címet rendelve.

## <a name="before-you-begin-automating"></a><a name ="before"></a>Az automatizálás megkezdése előtt

* Ellenőrizze, hogy az eszköz támogatja-e az IPsec IKEv1/IKEv2-t. Lásd: [alapértelmezett házirendek](#default).
* Tekintse meg az Azure Virtual WAN-nal való kapcsolat automatizálásához használt [REST API-kat.](#additional)
* Tesztelje az Azure Virtual WAN portálélményét.
* Ezután döntse el, hogy a kapcsolódási lépések melyik részét szeretné automatizálni. Legalább azt javasoljuk, hogy automatizálja:

  * Hozzáférés-vezérlés
  * Ágeszköz-információk feltöltése az Azure Virtual WAN-ba
  * Az Azure-konfiguráció letöltése és a kapcsolat beállítása a fiókeszközről az Azure Virtual WAN-ra

### <a name="additional-information"></a><a name ="additional"></a>További információk

* [REST API](https://docs.microsoft.com/rest/api/virtualwan/virtualhubs) a virtuális központ létrehozásának automatizálásához
* [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpngateways) az Azure VPN-átjáró automatizálásához a Virtual WAN-hoz
* [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpnconnections) a VPN-webhely csatlakoztatásához egy Azure VPN-központhoz
* [Alapértelmezett IPsec-házirendek](#default)

## <a name="customer-experience"></a><a name ="ae"></a>Felhasználói élmény

Ismerje meg a várt felhasználói élményt az Azure Virtual WAN-nal együtt.

  1. A virtuális WAN-felhasználók általában egy virtuális WAN-erőforrás létrehozásával indítják el a folyamatot.
  2. A felhasználó beállítja a szolgáltatás egyszerű erőforráscsoport-hozzáférést a helyszíni rendszerhez (a fiókvezérlőhöz vagy a VPN-eszköz kiépítési szoftverhez) az Azure Virtual WAN-ba való ágadatok írásához.
  3. A felhasználó dönthet úgy, hogy jelenleg bejelentkezik a felhasználói felületre, és beállítja a szolgáltatásnév hitelesítő adatait. Ha ez befejeződött, a vezérlő képesnek kell lennie arra, hogy feltölti ág információkat az automatizálás lesz biztosítva. Ennek manuális megfelelője az Azure oldalán a "Webhely létrehozása".
  4. Miután a webhely (ágeszköz) információ elérhetővé válik az Azure-ban, a felhasználó csatlakoztatja a helyet egy hubhoz. A virtuális központ a Microsoft által felügyelt virtuális hálózat. Az elosztó különféle szolgáltatásvégpontokat tartalmaz a helyszíni hálózatból (vpnsite) induló kapcsolatok biztosításához. Az elosztó a hálózat központja egy adott régióban. Azure-régiónként csak egy hub lehet, és a benne lévő vpn-végpont (vpngateway) a folyamat során jön létre. A VPN-átjáró egy méretezhető átjáró, amely a sávszélesség és a kapcsolat igényei alapján megfelelőméretű. Dönthet úgy, hogy automatizálja a virtuális hub és a VPNgateway létrehozása a fiókeszköz vezérlő irányítópultján.
  5. Miután a virtuális hub van társítva a helyhez, egy konfigurációs fájl jön létre a felhasználó számára, hogy manuálisan töltse le. Itt jön a nap a célja az automatizálás, és teszi zökkenőmentessé a felhasználói élményt. Ahelyett, hogy a felhasználónak manuálisan kellene letöltenie és konfigurálnia az ágeszközt, beállíthatja az automatizálást, és minimális átkattintási élményt biztosíthat a felhasználói felületen, ezáltal enyhítve a tipikus kapcsolódási problémákat, például a megosztott kulcs eltérését, az IPSec paraméter paraméterét eltérés, konfigurációs fájl olvashatósága stb.
  6. A megoldás ezen lépésének végén a felhasználó zökkenőmentes, helyek közötti kapcsolattal fog rendelkezni az elágazási eszköz és a virtuális központ között. Más csomópontok között további kapcsolatokat is beállíthat. Minden kapcsolat aktív-aktív alagút. Az ügyfél dönthet úgy, hogy az alagút minden egyes kapcsolatához más-más rendszerszolgáltatót használ.
  7. Fontolja meg a hibaelhárítási és figyelési képességek biztosítását a CPE felügyeleti felületen. A tipikus forgatókönyvek közé tartozik az "Ügyfél nem tud hozzáférni az Azure-erőforrásokhoz cpe-probléma miatt", "IPsec-paraméterek megjelenítése a CPE-oldalon" stb.

## <a name="automation-details"></a><a name ="understand"></a>Automatizálási részletek

###  <a name="access-control"></a><a name="access"></a>Hozzáférés-vezérlés

Az ügyfeleknek képesnek kell lenniük a virtual WAN megfelelő hozzáférés-vezérlésének beállítására az eszköz felhasználói felületén. Ez egy Azure egyszerű Azure-szolgáltatás használata használata ajánlott. Egyszerű szolgáltatásalapú hozzáférés biztosítja az eszközvezérlő megfelelő hitelesítést fiókadatok feltöltéséhez. További információ: [Egyszerű szolgáltatás létrehozása.](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) Bár ez a funkció kívül esik az Azure Virtual WAN-ajánlaton, az alábbiakban felsoroljuk azokat a tipikus lépéseket, amelyek az Azure-hozzáférés beállításához szükséges lépéseket, amelyek után a vonatkozó adatokat beviszi a rendszer az eszközfelügyeleti irányítópultra.

* Hozzon létre egy Azure Active Directory-alkalmazást a helyszíni eszközvezérlőhöz.
* Alkalmazásazonosító és hitelesítési kulcs beszerezni
* A bérlőazonosító beszerzése
* Alkalmazás hozzárendelése a "Közreműködő" szerepkörhöz

###  <a name="upload-branch-device-information"></a><a name="branch"></a>Ágeszköz-adatok feltöltése

A felhasználói élményt úgy kell megterveznie, hogy az azure-ba fiókinformációkat töltsön fel. A REST [VPNSite API-k](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) segítségével létrehozhatja a helyadatokat a Virtual WAN-ban. Megadhatja az összes ág SDWAN/VPN-eszközt, vagy szükség szerint kiválaszthatja az eszköz testreszabását.

### <a name="device-configuration-download-and-connectivity"></a><a name="device"></a>Eszközkonfiguráció letöltése és csatlakoztatása

Ez a lépés magában foglalja az Azure-konfiguráció letöltését és a kapcsolat beállítását a fiókeszközről az Azure Virtual WAN-ra. Ebben a lépésben egy ügyfél, amely nem használ szolgáltatót manuálisan töltse le az Azure-konfigurációt, és alkalmazza azt a helyszíni SDWAN/VPN-eszközre. Szolgáltatóként automatizálnia kell ezt a lépést. További információkért tekintse meg a [letöltési REST API-kat.](https://docs.microsoft.com/rest/api/virtualwan/vpnsitesconfiguration/download) Az eszközvezérlő meghívhat "GetVpnConfiguration" REST API-t az Azure-konfiguráció letöltéséhez.

**Konfigurációs megjegyzések**

  * Ha az Azure virtuális hálózatok a virtuális hubhoz vannak csatolva, connectedsubnets néven jelennek meg.
  * A VPN-kapcsolat útvonalalapú konfigurációt használ, és támogatja mind az IKEv1, mind az IKEv2 protokollt.

## <a name="device-configuration-file"></a><a name="devicefile"></a>Eszköz konfigurációs fájlja

Az eszközkonfigurációs fájl a helyszíni VPN-eszköz konfigurálása során használandó beállításokat tartalmazza. A fájl áttekintésekor a következő információkat láthatja:

* **vpnSiteConfiguration** – Ez a szakasz az eszköz a virtuális WAN-ra csatlakozó helyként való telepítésére vonatkozó adatait tartalmazza. Az ágeszköz nevét és nyilvános IP-címét tartalmazza.
* **vpnSiteConnections** – Ez a szakasz a következőkről nyújt információkat:

    * A virtuális elosztó virtuális **hálózatcímterülete.**<br>Példa:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * A hubhoz csatlakoztatott virtuális hálózatok **címtere.**<br>Példa:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * A virtuális hálózat VPN-átjárójának **IP-címei** Mivel a VPN-átjáró mindegyik kapcsolata 2 alagutat tartalmaz aktív-aktív konfigurációban, a fájlban mindkét IP-cím szerepel. Ebben a példában mindegyik helyhez az „Instance0” és az „Instance1” van feltüntetve.<br>Példa:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Vpngateway kapcsolat konfigurációs részleteket,** mint a BGP, előre megosztott kulcs, stb A PSK az előre megosztott kulcs, amely automatikusan létrejön az Ön számára. A kapcsolatot az egyéni PSK Áttekintés lapján bármikor szerkesztheti.
  
**Eszközkonfigurációs példafájl**

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

## <a name="connectivity-details"></a><a name="default"></a>Kapcsolódási részletek

A helyszíni SDWAN/VPN-eszköz vagy SD-WAN-konfigurációnak meg kell egyeznie, vagy tartalmaznia kell a következő algoritmusokat és paramétereket, amelyeket az Azure IPsec/IKE-szabályzatban megadott.

* IKE titkosítási algoritmus
* IKE integritási algoritmus
* DH-csoport
* IPsec titkosítási algoritmus
* IPsec integritási algoritmus
* PFS-csoport

### <a name="default-policies-for-ipsec-connectivity"></a><a name="default"></a>Alapértelmezett szabályzatok IPsec-kapcsolatokhoz

[!INCLUDE [IPsec Default](../../includes/virtual-wan-ipsec-include.md)]

### <a name="custom-policies-for-ipsec-connectivity"></a><a name="custom"></a>Egyéni házirendek az IPsec-kapcsolathoz

[!INCLUDE [IPsec Custom](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="next-steps"></a>További lépések

A Virtual WAN szolgáltatásról további információt az [Azure Virtual WAN és](virtual-wan-about.md) az Azure Virtual WAN – gyakori kérdések című [témakörben talál.](virtual-wan-faq.md)

További információkért kérjük, küldjön <azurevirtualwan@microsoft.com>egy e-mailt a . Adja meg a vállalat nevét szögletes zárójelek ([]) között az e-mail tárgymezőjében.
