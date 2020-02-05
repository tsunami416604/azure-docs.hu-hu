---
title: Azure VMware-megoldások (AVS) – VPN-átjárók
description: Tudnivalók az AVS-helyek közötti VPN-és pont – hely VPN-fogalmakról
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 73171e2c46bdf6c934db5777efe36ba51153a686
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024857"
---
# <a name="vpn-gateways-overview"></a>VPN-átjárók – áttekintés

A VPN-átjárók titkosított adatforgalom küldésére szolgálnak egy helyi helyen vagy egy számítógépen a nyilvános interneten keresztül. Minden régió rendelkezhet egy VPN-átjáróval, amely több kapcsolatot is képes támogatni. Amikor többhelyes csatlakozást hoz létre egyetlen VPN-átjáróhoz, az összes VPN-alagút az elérhető sávszélességen osztozkodik.

Az AVS két típusú VPN-átjárót biztosít:

* Helyek közötti VPN-átjáró
* Pont – hely típusú VPN-átjáró

## <a name="site-to-site-vpn-gateway"></a>Helyek közötti VPN-átjáró

A helyek közötti VPN-átjárók titkosított adatforgalom küldésére szolgálnak egy AVS-régió hálózata és egy helyszíni adatközpont között. Ezzel a kapcsolattal meghatározhatja az alhálózatok/CIDR tartományát a helyszíni hálózat és az AVS-régió hálózata közötti hálózati forgalomhoz.

A VPN-átjáró lehetővé teszi, hogy a helyszíni hálózatról az AVS Private-felhőben, illetve az AVS Private Cloud szolgáltatásban lévő szolgáltatásokat használja. Az AVS házirend-alapú VPN-kiszolgálót biztosít a helyszíni hálózattal létesített kapcsolat létrehozásához.

Helyek közötti VPN-re vonatkozó esetek használata:

* Az AVS Private Cloud vCenter elérhetősége a helyszíni hálózat bármely munkaállomásáról.
* A helyszíni Active Directory használata vCenter-identitás forrásaként.
* A virtuálisgép-sablonok, az ISO-fájlok és a helyszíni erőforrások egyéb fájljainak kényelmes átvitele az AVS Private Cloud vCenter.
* A helyszíni hálózatról származó AVS Private-felhőben futó munkaterhelések hozzáférhetősége.

![Helyek közötti VPN-kapcsolat topológiája](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Titkosítási paraméterek

A helyek közötti VPN-kapcsolat a következő alapértelmezett titkosítási paramétereket használja a biztonságos kapcsolat létrehozásához. Ha a helyszíni VPN-eszköz kapcsolatát hozza létre, használja a helyszíni VPN-átjáró által támogatott alábbi paramétereket.

#### <a name="phase-1-proposals"></a>1\. fázis – javaslatok

| Paraméter | 1\. javaslat | 2\. javaslat | 3\. javaslat |
|-----------|------------|------------|------------|
| IKE verziószám | IKEv1 | IKEv1 | IKEv1 |
| Titkosítás | AES 128 | AES 256 | AES 256 |
| Kivonatoló algoritmus| SHA 256 | SHA 256 | SHA 1 |
| Diffie Hellman-csoport (DH-csoport) | 2 | 2 | 2 |
| Üzemidő | 28 800 másodperc | 28 800 másodperc | 28 800 másodperc |
| Adatméret | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>2\. fázis – javaslatok

| Paraméter | 1\. javaslat | 2\. javaslat | 3\. javaslat |
|-----------|------------|------------|------------|
| Titkosítás | AES 128 | AES 256 | AES 256 |
| Kivonatoló algoritmus| SHA 256 | SHA 256 | SHA 1 |
| Tökéletes továbbítási titoktartási csoport (PFS-csoport) | None | None | None |
| Üzemidő | 1 800 másodperc | 1 800 másodperc | 1 800 másodperc |
| Adatméret | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> Állítsa be a TCP MSS-befogást a 1200-es méretre a VPN-eszközön. Ha a VPN-eszközök nem támogatják a MSS-befogást, akkor a bújtatási felületen lévő MTU-t 1240 bájtra is állíthatja.

## <a name="point-to-site-vpn-gateway"></a>Pont – hely típusú VPN-átjáró

A pont – hely VPN használatával titkosított adatforgalmat küldhet az AVS-régió hálózata és az ügyfélszámítógép között. A pont – hely VPN az AVS Private Cloud Network elérésének legegyszerűbb módja, beleértve az AVS Private Cloud vCenter és a számítási feladatok virtuális gépeket. Pont – hely típusú VPN-kapcsolat használata, ha távolról csatlakozik az AVS Private Cloud szolgáltatáshoz.

## <a name="next-steps"></a>Következő lépések

* [VPN-átjáró beállítása](vpn-gateway.md)
