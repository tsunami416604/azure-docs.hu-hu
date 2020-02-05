---
title: Azure VMware-megoldások (AVS) – VPN konfigurálása a helyszíni és az AVS Private Cloud között
description: Útmutató helyek közötti vagy pont – hely típusú VPN-kapcsolat konfigurálásához a helyszíni hálózat és az AVS Private Cloud között
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fbd2b227c9292593a7652044ef4c013bf0cfaf8e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77017003"
---
# <a name="configure-a-vpn-connection-to-your-avs-private-cloud"></a>VPN-kapcsolat konfigurálása az AVS Private Cloud-hoz

A VPN-átjárók lehetővé teszik az AVS-hálózathoz való csatlakozást a helyszíni hálózatról és az ügyfélszámítógépről távolról. Ebből a cikkből megtudhatja, hogyan állíthatja be a VPN-átjárókat az AVS-portálról. A helyszíni hálózat és az AVS-hálózat közötti VPN-kapcsolat hozzáférést biztosít a vCenter és a számítási feladatokhoz az AVS Private-felhőben. Az AVS a pont – hely VPN-t és a helyek közötti VPN-átjárókat is támogatja.

## <a name="vpn-gateway-types"></a>VPN-átjárók típusai

* A **pont – hely VPN** -kapcsolat az a legegyszerűbb módszer, amellyel a számítógépről CSATLAKOZHAT az AVS saját felhőhöz. A pont – hely VPN-kapcsolat használatával távolról csatlakozhat az AVS Private Cloud-hoz.
* A **helyek közötti VPN-** kapcsolat lehetővé teszi, hogy az AVS saját felhőalapú számítási feladatait a helyszíni szolgáltatások eléréséhez hozza létre. A helyszíni Active Directory identitás forrásaként is használhatja az AVS Private Cloud vCenter való hitelesítéshez. Jelenleg a **házirend-alapú VPN-** típus támogatott.

Egy régióban létrehozhat egy helyek közötti VPN-átjárót és egy pont – hely típusú VPN-átjárót.

## <a name="point-to-site-vpn"></a>Pont – hely típusú VPN

Pont – hely típusú VPN-átjáró létrehozásával kapcsolatban lásd: [pont – hely típusú VPN-átjáró létrehozása](vpn-gateway.md#create-point-to-site-vpn-gateway).

### <a name="connect-to-avs-using-point-to-site-vpn"></a>Csatlakozás az AVS-hez pont – hely típusú VPN használatával

A VPN-ügyfél szükséges az AVS-hez való csatlakozáshoz a számítógépről. Töltse le a Windowshoz készült [OpenVPN-ügyfelet](https://openvpn.net/community-downloads/) , illetve a MacOS és az OS X [viszkozitását](https://www.sparklabs.com/viscosity/download/) .

1. Indítsa el az AVS Portalt, és válassza a **hálózat**lehetőséget.
2. Válassza a **VPN Gateway**lehetőséget.
3. A VPN-átjárók listájában kattintson a pont – hely VPN-átjáróra.
4. Válassza a **felhasználók**lehetőséget.
5. Kattintson a **saját VPN-konfiguráció letöltése** elemre.

    ![VPN-konfiguráció letöltése](media/download-p2s-vpn-configuration.png)

6. A konfiguráció importálása a VPN-ügyfélen

    * Útmutató a [konfiguráció importálásához a Windows-ügyfélen](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Útmutató a [konfiguráció importálásához MacOS vagy OS X rendszeren](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Csatlakozás az AVS VPN Gateway-hez.

Az alábbi példa azt mutatja be, hogyan importálhatók a kapcsolatok a **viszkozitási ügyféllel**.

#### <a name="import-connection-on-viscosity-client"></a>Importálási kapcsolatok a viszkozitási ügyfélen

1. Bontsa ki a VPN-konfiguráció tartalmát a letöltött. zip fájlból.

2. Nyissa meg a viszkozitást a számítógépén.

3. Kattintson a **+** ikonra, és válassza a **fájlból**a **kapcsolatok > importálás** lehetőséget.

    ![VPN-konfiguráció importálása fájlból](media/import-p2s-vpn-config.png)

4. Válassza ki a használni kívánt protokollhoz tartozó OpenVPN konfigurációs fájlt (. ovpn), majd kattintson a **Megnyitás**gombra.

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

A kapcsolatok most a viszkozitás menüben jelennek meg.

#### <a name="connect-to-the-vpn"></a>Kapcsolódás a VPN-hez

Ha a VPN-kapcsolathoz a viszkozitási OpenVPN-ügyféllel szeretne csatlakozni, válassza ki a kapcsolatot a menüből. A menü ikonja azt jelzi, hogy a kapcsolódás létrejött.

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-avs-private-clouds"></a>Csatlakozás több AVS privát Felhőkhöz

A pont – hely típusú VPN-kapcsolat feloldja a létrehozott első AVS saját felhő DNS-nevét. Ha más AVS privát Felhőkhöz szeretne hozzáférni, frissítenie kell a DNS-kiszolgálót a VPN-ügyfélen.

1. Indítsa el az [AVS-portált](access-cloudsimple-portal.md).

2. Navigáljon az **erőforrásokhoz** > az **AVS privát felhők** elemre, és válassza ki azt az AVS privát felhőt, amelyhez csatlakozni szeretne.

3. Az AVS Private Cloud – **Összefoglalás** lapon másolja az AVS Private Cloud DNS-kiszolgáló IP-címét az **alapszintű adatok**területen.

    ![AVS Private Cloud DNS-kiszolgálók](media/private-cloud-dns-server.png)

4. Kattintson a jobb gombbal a számítógép rendszertálcán található viszkozitás ikonra, és válassza a **Beállítások**lehetőséget.

    ![VPN](media/vis00.png)

5. Válassza ki az AVS VPN-kapcsolat lehetőséget.

    ![VPN-kapcsolat](media/viscosity-client.png)

6. A kapcsolódási tulajdonságok módosításához kattintson a **Szerkesztés** gombra.

    ![VPN-kapcsolat szerkesztése](media/viscosity-edit-connection.png)

7. Kattintson a **hálózatkezelés** lapra, és adja meg az AVS Private Cloud DNS-kiszolgáló IP-címeit vesszővel vagy szóközzel elválasztva, a tartományt pedig ```AVS.io```ként. Válassza **a VPN-kiszolgáló által eljuttatott DNS-beállítások mellőzése**lehetőséget.

    ![VPN-hálózatkezelés](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Ha az első AVS Private-felhőhöz szeretne csatlakozni, távolítsa el ezeket a beállításokat, és kapcsolódjon a VPN-kiszolgálóhoz.

## <a name="site-to-site-vpn"></a>Helyek közötti VPN

Helyek közötti VPN-átjáró létrehozásához tekintse meg a [helyek közötti VPN-átjáró létrehozása](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)című témakört. A helyszíni hálózatról az AVS Private Cloud felé irányuló helyek közötti VPN-kapcsolat biztosítja ezeket az előnyöket. 

* Az AVS Private Cloud vCenter elérhetősége a helyszíni hálózat bármely munkaállomásáról
* Helyszíni Active Directory használata vCenter-identitás forrásaként
* A virtuálisgép-sablonok, az ISO-fájlok és a helyszíni erőforrások egyéb fájljainak kényelmes átvitele az AVS Private Cloud vCenter
* A helyszíni hálózatról származó AVS Private-felhőben futó munkaterhelések hozzáférhetősége

Ha magas rendelkezésre állású módban szeretné beállítani a helyszíni VPN-átjárót, tekintse meg a [magas rendelkezésre állású VPN-kapcsolat konfigurálása](high-availability-vpn-connection.md)című témakört.

> [!IMPORTANT]
>    1. Állítsa be a TCP MSS-befogást a 1200-es méretre a VPN-eszközön. Ha a VPN-eszközök nem támogatják a MSS-befogást, akkor a bújtatási felületen lévő MTU-t 1240 bájtra is állíthatja.
> 2. A helyek közötti VPN beállítása után továbbítja a (*) DNS-kéréseit. AVS.io az AVS Private Cloud DNS-kiszolgálókra. Kövesse a helyszíni [DNS-telepítő](on-premises-dns-setup.md)utasításait.
