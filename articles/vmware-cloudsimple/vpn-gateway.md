---
title: Azure VMware-megoldások (AVS) – VPN-átjáró beállítása
description: Útmutató pont – hely típusú VPN-átjáró és helyek közötti VPN-átjáró beállításához, valamint kapcsolatok létrehozása a helyszíni hálózat és az AVS Private Cloud között
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8731f7a9ff7f2cab7516e43c62ddc1aac2668168
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016765"
---
# <a name="set-up-vpn-gateways-on-avs-network"></a>VPN-átjárók beállítása az AVS-hálózaton

A VPN-átjárók lehetővé teszik az AVS-hálózathoz való csatlakozást a helyszíni hálózatról és az ügyfélszámítógépről távolról. A helyszíni hálózat és az AVS-hálózat közötti VPN-kapcsolat hozzáférést biztosít a vCenter és a számítási feladatokhoz az AVS Private-felhőben. Az AVS a helyek közötti VPN-t és a pont – hely VPN-átjárókat is támogatja.

## <a name="vpn-gateway-types"></a>VPN-átjárók típusai

* A **helyek közötti VPN-** kapcsolat lehetővé teszi, hogy az AVS saját felhőalapú számítási feladatait a helyszíni szolgáltatások eléréséhez hozza létre. A helyszíni Active Directory identitás forrásaként is használhatja az AVS Private Cloud vCenter való hitelesítéshez. Jelenleg csak a **házirend-alapú VPN-** típusok támogatottak.
* A **pont – hely VPN** -kapcsolat az a legegyszerűbb módszer, amellyel a számítógépről CSATLAKOZHAT az AVS saját felhőhöz. A pont – hely VPN-kapcsolat használatával távolról csatlakozhat az AVS privát felhőhöz. Az ügyfél pont – hely típusú VPN-kapcsolatra való telepítésével kapcsolatos információkért lásd: [VPN-kapcsolat beállítása az AVS Private Cloud](set-up-vpn.md)szolgáltatáshoz.

Egy régióban létrehozhat egy pont – hely típusú VPN-átjárót és egy helyek közötti VPN-átjárót.

## <a name="automatic-addition-of-vlansubnets"></a>VLAN/alhálózatok automatikus hozzáadása

Az AVS VPN-átjárók a VLAN/alhálózatok VPN-átjárók számára történő hozzáadására vonatkozó házirendeket biztosítanak. A szabályzatok lehetővé teszik különböző szabályok megadását a felügyeleti VLAN/alhálózatok és a felhasználó által megadott VLAN/alhálózatok számára. A felügyeleti VLAN/alhálózatok szabályai a létrehozott összes új egyéni AVS-felhőre érvényesek. A felhasználó által definiált VLAN-ok vagy alhálózatok szabályai lehetővé teszik, hogy a helyek közötti VPN-átjárók számára automatikusan hozzáadja az új VLAN-okat/alhálózatokat a meglévő vagy az új AVS privát Felhőkhöz, és meghatározza az egyes kapcsolatokra vonatkozó házirendet.

A VLAN-ok/alhálózatok VPN-átjárók számára való hozzáadására vonatkozó házirendek a helyek közötti VPN-és pont – hely típusú VPN-átjárók esetében is érvényesek.

## <a name="automatic-addition-of-users"></a>Felhasználók automatikus hozzáadása

Egy pont – hely típusú VPN-átjáró lehetővé teszi az új felhasználók automatikus kiegészítési szabályzatának definiálását. Alapértelmezés szerint az előfizetés összes tulajdonosa és közreműködője hozzáférhet az AVS-portálhoz. A felhasználók csak akkor jönnek létre, ha első alkalommal az AVS-portált indítják el. A szabályok **automatikus hozzáadása** lehetőség kiválasztásával bármely új felhasználó HOZZÁFÉRHET az AVS-hálózathoz pont – hely típusú VPN-kapcsolat használatával.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Helyek közötti VPN-átjáró beállítása

1. [Nyissa meg az AVS-portált](access-cloudsimple-portal.md) , és válassza a **hálózat**lehetőséget.
2. Válassza a **VPN Gateway**lehetőséget.
3. Kattintson az **új VPN Gateway**elemre.

    ![VPN-átjáró létrehozása](media/create-vpn-gateway.png)

4. Az **átjáró konfigurálása**lapon írja be a következő beállításokat, majd kattintson a **tovább**gombra.

    * Válassza a **helyek közötti VPN** lehetőséget átjáró típusaként.
    * Adjon meg egy nevet az átjáró azonosításához.
    * Válassza ki azt az Azure-helyet, ahol az AVS szolgáltatás telepítve van.
    * Opcionálisan engedélyezze a magas rendelkezésre állást.

    ![Helyek közötti VPN-átjáró létrehozása](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > A magas rendelkezésre állás engedélyezéséhez a helyszíni VPN-eszköznek két IP-címhez való csatlakozást kell támogatnia. Ez a beállítás nem tiltható le, ha a VPN-átjáró telepítve van.

5. Hozza létre az első csatlakozni a helyszíni hálózatból, és kattintson a **tovább**gombra.

    * Adjon meg egy nevet a kapcsolódás azonosításához.
    * A társ IP esetében adja meg a helyszíni VPN-átjáró nyilvános IP-címét.
    * Adja meg a helyszíni VPN-átjáró társ-azonosítóját. A társ-azonosító általában a helyszíni VPN-átjáró nyilvános IP-címe. Ha konfigurált egy adott azonosítót az átjárón, adja meg az azonosítót.
    * Másolja a helyi VPN-átjáróval létesített kapcsolathoz használni kívánt megosztott kulcsot. Ha módosítani szeretné az alapértelmezett megosztott kulcsot, és megad egy újat, kattintson a Szerkesztés ikonra.
    * Helyszíni **előtagok**esetén adja meg az AVS-hálózatot elérő helyszíni CIDR előtagokat. A kapcsolat létrehozásakor több CIDR-előtagot is hozzáadhat.

    ![Helyek közötti VPN Gateway-kapcsolat létrehozása](media/create-vpn-gateway-s2s-connection.png)

6. Engedélyezze a helyszíni hálózatról elérhető, az AVS saját felhőalapú hálózatán lévő VLAN-t/alhálózatokat, majd kattintson a **tovább**gombra.

    * Felügyeleti VLAN/alhálózat hozzáadásához engedélyezze a **felügyeleti VLAN-ok/alhálózatok hozzáadása az AVS privát felhőkhöz**lehetőséget. A vMotion és vSAN alhálózatok esetében felügyeleti alhálózatra van szükség.
    * VMotion alhálózatok hozzáadásához engedélyezze az **AVS Private-felhők vMotion-hálózatának hozzáadása**lehetőséget.
    * VSAN-alhálózatok hozzáadásához engedélyezze **a privát felhők hozzáadására vonatkozó vSAN-alhálózatot**.
    * Válassza ki vagy törölje a megadott VLAN-okat.

    ![Kapcsolat létrehozása](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Tekintse át a beállításokat, majd kattintson a **Küldés**gombra.

    ![Helyek közötti VPN-átjáró áttekintése és létrehozása](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Pont – hely típusú VPN-átjáró létrehozása

1. [Nyissa meg az AVS-portált](access-cloudsimple-portal.md) , és válassza a **hálózat**lehetőséget.
2. Válassza a **VPN Gateway**lehetőséget.
3. Kattintson az **új VPN Gateway**elemre.

    ![VPN-átjáró létrehozása](media/create-vpn-gateway.png)

4. Az **átjáró konfigurálása**lapon írja be a következő beállításokat, majd kattintson a **tovább**gombra.

    * Válassza a **pont – hely típusú VPN** lehetőséget átjáró típusaként.
    * Adjon meg egy nevet az átjáró azonosításához.
    * Válassza ki azt az Azure-helyet, ahol az AVS szolgáltatás telepítve van.
    * A pont – hely átjáróhoz tartozó ügyféloldali alhálózat megadása. A DHCP-címeket a rendszer a csatlakozáskor az ügyfél alhálózatában adja meg.

5. A **kapcsolatok/felhasználók**területen válassza a következő beállításokat, majd kattintson a **tovább**gombra.

    * Ha automatikusan engedélyezni szeretné az összes jelenlegi és jövőbeli felhasználó számára az AVS Private Cloud elérését a pont – hely átjárón keresztül, válassza az **összes felhasználó automatikus hozzáadása**lehetőséget. Ha bejelöli a beállítást, a rendszer a felhasználói listán szereplő összes felhasználót automatikusan kijelöli. Az automatikus beállítást felülbírálhatja az egyes felhasználók törlésével a listában.
    * Az egyes felhasználók kiválasztásához kattintson a felhasználók listájában található jelölőnégyzetekre.

6. A VLAN-ok/alhálózatok szakaszban megadhatja az átjáróhoz és a kapcsolatokhoz tartozó felügyeleti és felhasználói VLAN-okat/alhálózatokat.

    * Az **automatikus hozzáadási** beállítások az átjáróhoz tartozó globális házirendet határozzák meg. A beállítások az aktuális átjáróra érvényesek. A beállítások felülbírálása a **Select** területen lehetséges.
    * Válassza **a felügyeleti VLAN-ok vagy az AVS privát felhők alhálózatok hozzáadása**lehetőséget. 
    * A felhasználó által definiált VLAN-ok/alhálózatok hozzáadásához kattintson a **felhasználó által definiált VLAN-ok/alhálózatok hozzáadása**lehetőségre.
    * A beállítások **kiválasztása** felülbírálja a globális beállításokat az **automatikus Hozzáadás**területen.

7. A beállítások áttekintéséhez kattintson a **tovább** gombra. A módosítások elvégzéséhez kattintson a Szerkesztés ikonra.
8. A VPN-átjáró létrehozásához kattintson a **Létrehozás** gombra.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Az ügyfél alhálózata és a pont – hely típusú VPN-átjáró protokolljai

A pont – hely VPN-átjáró lehetővé teszi a TCP-és UDP-kapcsolatokat. A TCP-vagy UDP-konfiguráció kiválasztásával válassza ki a számítógépről való csatlakozáskor használandó protokollt.

A konfigurált ügyfél-alhálózat TCP-és UDP-ügyfelek esetén is használatos. A CIDR előtag két alhálózatra van osztva, egyet a TCP és egy UDP-ügyfél számára. Válassza ki az előtag maszkját azon VPN-felhasználók száma alapján, akik egyidejűleg csatlakoznak. 

A következő táblázat az előtag-maszk egyidejű ügyfélkapcsolatok számát sorolja fel.

| Előtag maszkja | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| Egyidejű TCP-kapcsolatok száma | 124 | 60 | 28 | 12 | 4 |
| Egyidejű UDP-kapcsolatok száma | 124 | 60 | 28 | 12 | 4 |

Ha pont – hely típusú VPN-kapcsolattal szeretne csatlakozni, tekintse meg [a csatlakozás az AVS-hez pont – hely típusú VPN használatával](set-up-vpn.md#connect-to-avs-using-point-to-site-vpn)című témakört.
