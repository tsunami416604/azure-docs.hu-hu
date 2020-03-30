---
title: Azure VMware-megoldás a CloudSimple által – VPN konfigurálása a helyszíni és a magánfelhő között
description: A helyszíni hálózat és a CloudSimple magánfelhő közötti hely–vagy pont-hely VPN-kapcsolat konfigurálása
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d000d8390375466232c7daac2a4a056ef424be79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087125"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>VPN-kapcsolat konfigurálása a CloudSimple private cloudszolgáltatással

A VPN-átjárók lehetővé teszik, hogy a helyszíni hálózatról és az ügyfélszámítógépről csatlakozzon a CloudSimple hálózathoz.  Ebben a cikkben a VPN-átjárók a CloudSimple portálról történő beállításával kapcsolatos információkat talál.  A helyszíni hálózat és a CloudSimple-hálózat közötti VPN-kapcsolat hozzáférést biztosít a vCenterhez és a privát felhő munkaterheléseihez. A CloudSimple támogatja mind a pont-hely VPN, mind a helyek közötti VPN-átjárókat.

## <a name="vpn-gateway-types"></a>VPN-átjárótípusok

* **A point-to-site VPN kapcsolat** a legegyszerűbb módja annak, hogy a számítógépről csatlakozzon a magánfelhőhöz. A helyek közötti VPN-kapcsolat segítségével távolról csatlakozhat a magánfelhőhöz.
* **A helyek közötti VPN-kapcsolat** lehetővé teszi a magánfelhőbeli számítási feladatok beállítását a helyszíni szolgáltatások eléréséhez. A helyszíni Active Directoryt is használhatja identitásforrásként a privát felhőbeli vCenter-kiszolgálón való hitelesítéshez.  Jelenleg **a házirendalapú VPN-típus** támogatott.

Egy régióban létrehozhat egy helyek közötti VPN-átjárót és egy pont-hely VPN átjárót.

## <a name="point-to-site-vpn"></a>Pont–hely VPN

A pont-hely VPN-átjáró létrehozásáról a [Pont-hely VPN átjáró létrehozása](vpn-gateway.md#create-point-to-site-vpn-gateway)című témakörben található.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Csatlakozás a CloudSimple szolgáltatáshoz a point-to-site VPN használatával

VPN-ügyfél szükséges a CloudSimple-hez való csatlakozáshoz a számítógépről.  Töltse le az [OpenVPN klienst](https://openvpn.net/community-downloads/) Windows rendszerre, vagy [a Viscosity-t](https://www.sparklabs.com/viscosity/download/) macOS-re és OS X-re.

1. Indítsa el a CloudSimple portált, és válassza a **Hálózat**lehetőséget.
2. Válassza a **VPN-átjáró lehetőséget.**
3. A VPN-átjárók listájában kattintson a pont-hely VPN átjáróra.
4. Válassza a **Felhasználók** lehetőséget.
5. Kattintson a **VPN-konfiguráció letöltése lehetőségre**

    ![VPN-konfiguráció letöltése](media/download-p2s-vpn-configuration.png)

6. A konfiguráció importálása a VPN-ügyfélen

    * A [Windows-ügyfél konfigurációjának importálására](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program) vonatkozó utasítások
    * A [konfiguráció macOS vagy OS X rendszeren történő importálásának](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection) utasításai

7. Csatlakozzon a CloudSimple VPN-átjáróhoz.

Az alábbi példa a **viskozitási ügyféllel**történő kapcsolat importálása látható.

#### <a name="import-connection-on-viscosity-client"></a>Kapcsolat importálása viskozitási ügyfélen

1. Bontsa ki a VPN-konfiguráció tartalmát a letöltött .zip fájlból.

2. Nyissa meg a Viscosity-t a számítógépen.

3. Kattintson **+** az ikonra, és válassza **a Kapcsolat** > importálása**fájlból**lehetőséget.

    ![VPN-konfiguráció importálása fájlból](media/import-p2s-vpn-config.png)

4. Jelölje ki a használni kívánt protokoll OpenVPN konfigurációs fájlját (.ovpn), és kattintson a **Megnyitás gombra.**

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

A kapcsolat most megjelenik a Viszkozitás menüben.

#### <a name="connect-to-the-vpn"></a>Kapcsolódás a VPN-hez

Ha a Viscosity OpenVPN-ügyféllel szeretne csatlakozni a VPN-hez, válassza ki a kapcsolatot a menüből. A menüikon frissül, jelezve, hogy a kapcsolat létrejött.

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>Csatlakozás több privát felhőhöz

A pont-hely VPN-kapcsolat feloldja az első létrehozott magánfelhő DNS-nevét. Ha más privát felhőket szeretne elérni, frissítenie kell a VPN-ügyfél DNS-kiszolgálóját.

1. Indítsa el [a CloudSimple portált.](access-cloudsimple-portal.md)

2. Nyissa meg az **Erőforrások** > **magánfelhőket,** és válassza ki azt a magánfelhőt, amelyhez csatlakozni szeretne.

3. A magánfelhő **Összefoglaló** lapján másolja a Magánfelhő DNS-kiszolgáló IP-címét az **Alapszintű adatok csoportba.**

    ![Magánfelhő DNS-kiszolgálói](media/private-cloud-dns-server.png)

4. Kattintson a jobb gombbal a számítógép tálcáján lévő Viszkozitás ikonra, és válassza a **Beállítások parancsot.**

    ![VPN](media/vis00.png)

5. Válassza ki a CloudSimple VPN-kapcsolatot.

    ![VPN-kapcsolat](media/viscosity-client.png)

6. A kapcsolat tulajdonságainak módosításához kattintson a **Szerkesztés** gombra.

    ![VPN-kapcsolat szerkesztése](media/viscosity-edit-connection.png)

7. Kattintson a **Hálózat** fülre, és adja meg a magánfelhő DNS-kiszolgáló IP-címét vesszővel vagy térközrel elválasztva, és a tartományt a . ```cloudsimple.io```  Válassza **a VPN-kiszolgáló által küldött DNS-beállítások figyelmen kívül hagyása**lehetőséget.

    ![VPN-hálózat](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Az első magánfelhőhöz való csatlakozáshoz távolítsa el ezeket a beállításokat, és csatlakozzon a VPN-kiszolgálóhoz.

## <a name="site-to-site-vpn"></a>Helyek közötti VPN

A helyek közötti VPN-átjáró létrehozásáról a [Helyek közötti VPN-átjáró létrehozása](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)című témakörben található.  A helyszíni hálózatról a magánfelhőbe irányuló helyek közötti VPN-kapcsolat biztosítja ezeket az előnyöket.  

* A privát felhőbeli vCenter kisegítő lehetőségei a helyszíni hálózat bármely munkaállomásáról
* A helyszíni Active Directory használata vCenter-identitásforrásként
* Virtuálisgép-sablonok, ISO-k és egyéb fájlok kényelmes átvitele a helyszíni erőforrásokból a privát felhőbeli vCenterbe
* A magánfelhőn futó számítási feladatok hozzáférhetősége a helyszíni hálózatról

A helyszíni VPN-átjáró magas rendelkezésre állású módban való beállításához [olvassa el A magas rendelkezésre állású VPN-kapcsolat konfigurálása](high-availability-vpn-connection.md)című témakört.

> [!IMPORTANT]
>    1. Állítsa a TCP MSS-befogást 1200-ra a VPN-eszközön. Vagy ha a VPN-eszközök nem támogatják az MSS befogást, akkor az MTU-t az alagút-kapcsolaton 1240 bájtra állíthatja.
> 2. A helyek közötti VPN beállítása után továbbítsa a *.cloudsimple.io DNS-kéréseit a magánfelhő DNS-kiszolgálóinak.  Kövesse a [helyszíni DNS-telepítő utasításait.](on-premises-dns-setup.md)
