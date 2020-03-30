---
title: Azure VMware-megoldás a CloudSimple-től – VPN-átjáró beállítása
description: A pont–hely VPN-átjáró és a helyek közötti VPN-átjáró beállítása, valamint a helyszíni hálózat és a CloudSimple magánfelhő közötti kapcsolatok létrehozása
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8b7e238333196381524d189904871fe5933c906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279493"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>VPN-átjárók beállítása a CloudSimple hálózaton

A VPN-átjárók lehetővé teszik, hogy a helyszíni hálózatról és az ügyfélszámítógépről csatlakozzon a CloudSimple hálózathoz. A helyszíni hálózat és a CloudSimple-hálózat közötti VPN-kapcsolat hozzáférést biztosít a vCenterhez és a privát felhő munkaterheléseihez. A CloudSimple támogatja a helyek közötti VPN-t és a pont-hely VPN-átjárókat is.

## <a name="vpn-gateway-types"></a>VPN-átjárótípusok

* **A helyek közötti VPN-kapcsolat** lehetővé teszi a magánfelhőbeli számítási feladatok beállítását a helyszíni szolgáltatások eléréséhez. A helyszíni Active Directoryt is használhatja identitásforrásként a privát felhőbeli vCenter-kiszolgálón való hitelesítéshez.  Jelenleg csak **a házirend-alapú VPN-típus** támogatott.
* **A point-to-site VPN kapcsolat** a legegyszerűbb módja annak, hogy a számítógépről csatlakozzon a magánfelhőhöz. A point-to-site VPN-kapcsolat segítségével távolról csatlakozhat a magánfelhőhöz. A point-to-site VPN-kapcsolat ügyféltelepítéséről a [VPN-kapcsolat konfigurálása a magánfelhővel](set-up-vpn.md)című témakörben talál további információt.

Egy régióban létrehozhat egy pont-hely VPN átjárót és egy helyek közötti VPN-átjárót.

## <a name="automatic-addition-of-vlansubnets"></a>VLAN/alhálózatok automatikus hozzáadása

A CloudSimple VPN-átjárók biztosítják a VLAN/alhálózatok VPN-átjárókhoz való hozzáadásának házirendjeit.  A házirendek lehetővé teszik, hogy különböző szabályokat adjon meg a felügyeleti VLAN/alhálózatokés a felhasználó által definiált VLAN/alhálózatok.  A VLAN/alhálózatok kezelésére vonatkozó szabályok minden új privát felhőre vonatkoznak.  A felhasználó által definiált VLAN-okra/alhálózatokra vonatkozó szabályok lehetővé teszik, hogy automatikusan hozzáadjon minden új VLAN/alhálózatot a meglévő vagy új magánfelhőkhöz egy helyek közötti VPN-átjáróhoz, akkor az egyes kapcsolatokra vonatkozó házirendet kell megadni.

A VLAN-ok/alhálózatok VPN-átjárókhoz való hozzáadására vonatkozó házirendek a helyek közötti VPN-és a pont-hely VPN-átjárókra egyaránt vonatkoznak.

## <a name="automatic-addition-of-users"></a>A felhasználók automatikus hozzáadása

A pont-hely VPN átjáró lehetővé teszi, hogy automatikus összeadási házirendet határozzon meg az új felhasználók számára. Alapértelmezés szerint az előfizetés összes tulajdonosa és közreműködője hozzáférhet a CloudSimple portálhoz.  A felhasználók csak akkor jönnek létre, amikor a CloudSimple portál első alkalommal elindul.  A **Szabályok automatikus hozzáadása** lehetőség kiválasztásával minden új felhasználó hozzáférhet a CloudSimple hálózathoz a Point-to-Site VPN-kapcsolat használatával.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Helyek közötti VPN-átjáró beállítása

1. [Érje el a CloudSimple portált,](access-cloudsimple-portal.md) és válassza **a Hálózat**lehetőséget.
2. Válassza a **VPN-átjáró lehetőséget.**
3. Kattintson **az Új VPN-átjáró**elemre.

    ![VPN-átjáró létrehozása](media/create-vpn-gateway.png)

4. Az **Átjáró konfigurációja**párbeszédpanelen adja meg a következő beállításokat, majd kattintson a **Tovább**gombra.

    * Válassza a **helyek közötti VPN-t** átjárótípusként.
    * Adjon meg egy nevet az átjáró azonosításához.
    * Válassza ki azt az Azure-helyet, ahol a CloudSimple szolgáltatás telepítve van.
    * Opcionálisan engedélyezze a magas rendelkezésre állást.

    ![Helyek közötti VPN-átjáró létrehozása](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > A magas rendelkezésre állás engedélyezéséhez a helyszíni VPN-eszköznek két IP-címhez való csatlakozást kell támogatnia. Ez a beállítás nem tiltható le a VPN-átjáró telepítése után.

5. Hozza létre az első kapcsolatot a helyszíni hálózatról, és kattintson a **Tovább**gombra.

    * Adjon meg egy nevet a kapcsolat azonosításához.
    * A társ IP-cím, adja meg a helyszíni VPN-átjáró nyilvános IP-címét.
    * Adja meg a helyszíni VPN-átjáró társazonosítóját.  A társazonosító általában a helyszíni VPN-átjáró nyilvános IP-címe.  Ha beállított egy adott azonosítót az átjárón, adja meg az azonosítót.
    * Másolja a helyszíni VPN-átjáróból a kapcsolathoz használható megosztott kulcsot.  Az alapértelmezett megosztott kulcs módosításához és új kulcs megadásához kattintson a szerkesztés ikonra.
    * Helyszíni **előtagok**esetén adja meg a helyszíni CIDR-előtagokat, amelyek hozzáférnek a CloudSimple-hálózathoz.  A kapcsolat létrehozásakor több CIDR-előtagot is hozzáadhat.

    ![Helyek közötti VPN-átjárókapcsolat létrehozása](media/create-vpn-gateway-s2s-connection.png)

6. Engedélyezze a vlan-t/alhálózatokat a magánfelhő-hálózaton, amelyek a helyszíni hálózatról lesznek elérhetők, majd kattintson a **Tovább**gombra.

    * Felügyeleti VLAN/alhálózat hozzáadásához engedélyezze **a Felügyeleti VLAN-ok/privát felhők alhálózatainak hozzáadását.**  Felügyeleti alhálózat szükséges vMotion és vSAN alhálózatok.
    * VMotion-alhálózatok hozzáadásához engedélyezze **a Private Clouds vMotion hálózatának hozzáadása.**
    * VSAN-alhálózatok hozzáadásához engedélyezze a **Private Clouds vSAN-alhálózatának hozzáadását.**
    * Adott VLAN-ok kijelölése vagy kijelölésének megszüntetése.

    ![Kapcsolat létrehozása](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Tekintse át a beállításokat, és kattintson a **Küldés gombra.**

    ![Helyek közötti VPN-átjáró felülvizsgálata és létrehozása](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Pont-hely VPN-átjáró létrehozása

1. [Érje el a CloudSimple portált,](access-cloudsimple-portal.md) és válassza **a Hálózat**lehetőséget.
2. Válassza a **VPN-átjáró lehetőséget.**
3. Kattintson **az Új VPN-átjáró**elemre.

    ![VPN-átjáró létrehozása](media/create-vpn-gateway.png)

4. Az **Átjáró konfigurációja**párbeszédpanelen adja meg a következő beállításokat, majd kattintson a **Tovább**gombra.

    * Válassza **a pont-hely VPN** átjárótípust.
    * Adjon meg egy nevet az átjáró azonosításához.
    * Válassza ki azt az Azure-helyet, ahol a CloudSimple szolgáltatás telepítve van.
    * Adja meg a pont-hely átjáró ügyfél-alhálózatát.  A DHCP-címek a csatlakozáskor az ügyfélalhálózatból származnak.

5. A **Kapcsolat/felhasználó**csoportban adja meg a következő beállításokat, majd kattintson a **Tovább**gombra.

    * Ha azt szeretné, hogy az összes jelenlegi és jövőbeli felhasználó automatikusan hozzáférhessen a magánfelhőhöz a pont-webhely átjárón keresztül, válassza az **Összes felhasználó automatikus hozzáadása**lehetőséget. Ha ezt a beállítást választja, a felhasználói lista összes felhasználója automatikusan ki lesz jelölve. Az automatikus beállítást felülírhatja, ha törli a lista egyes felhasználóinak jelölőnégyzetjelölését.
    * Az egyes felhasználók kijelöléséhez kattintson a felhasználói lista jelölőnégyzetére.

6. A VLAN-ok/Alhálózatok szakasz lehetővé teszi az átjáró és a kapcsolatok felügyeleti és felhasználói VLAN-ok/alhálózatok megadását.

    * Az **Automatikus hozzáadási** beállítások az átjáró globális házirendjének beállítását határozzák meg. A beállítások az aktuális átjáróra vonatkoznak. A beállítások felülbírálhatók a **Kijelölés** területen.
    * Válassza **a Felügyeleti VLAN-ok hozzáadása/magánfelhők alhálózatainak hozzáadása**lehetőséget. 
    * Az összes felhasználó által definiált VLAN/alhálózat hozzáadásához kattintson **a Felhasználó által definiált VLAN-ok/alhálózatok hozzáadása**elemre.
    * A **Beállítások kiválasztása** felülírja a globális beállításokat az Automatikus **hozzáadás csoportban.**

7. A beállítások áttekintéséhez kattintson a **Tovább** gombra. A módosítások módosításához kattintson a Szerkesztés ikonokra.
8. A VPN-átjáró létrehozásához kattintson a **Létrehozás** gombra.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Ügyfélalhálózat és protokollok a pont-hely VPN átjáróhoz

A pont-hely VPN átjáró lehetővé teszi a TCP és UDP kapcsolatokat.  Válassza ki azt a protokollt, amelyet a számítógépről való csatlakozáskor használni szeretne a TCP- vagy UDP-konfiguráció kiválasztásával.

A konfigurált ügyfél-alhálózat tcp- és UDP-ügyfelekhez is használatos.  A CIDR előtag két alhálózatra oszlik, az egyik a TCP és egy UDP-ügyfelek számára. Válassza ki az előtag maszkot az egyidejűleg csatlakozó VPN-felhasználók száma alapján.  

Az alábbi táblázat az előtagmaszk egyidejű ügyfélkapcsolatainak számát sorolja fel.

| Előtag maszkja | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| Egyidejű TCP-kapcsolatok száma | 124 | 60 | 28 | 12 | 4 |
| Egyidejű UDP-kapcsolatok száma | 124 | 60 | 28 | 12 | 4 |

Ha pontról webhelyre vpn-nel szeretne csatlakozni, olvassa el [a Csatlakozás a FelhőhözEgyszerű szolgáltatáshoz pontról webhelyre VPN használatával című témakört.](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn)
