---
title: Azure VMware-megoldás a CloudSimple által – VPN-átjárók
description: Információ a Felhőalapú helyek közötti VPN- és pont-helyek VPN-fogalmakról
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 662fa342b3a18f726b418c496ff3fda937445301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024857"
---
# <a name="vpn-gateways-overview"></a>VPN-átjárók – áttekintés

A VPN-átjáró a cloudsimple régióhálózat a helyszíni helyen vagy a nyilvános interneten keresztül a számítógép közötti titkosított forgalom küldésére szolgál.  Minden régió rendelkezhet egy VPN-átjáróval, amely több kapcsolatot is támogathat. Amikor többhelyes csatlakozást hoz létre egyetlen VPN-átjáróhoz, az összes VPN-alagút az elérhető sávszélességen osztozkodik.

A CloudSimple kétféle VPN-átjárót kínál:

* Helyek közötti VPN-átjáró
* Pont-hely VPN átjáró

## <a name="site-to-site-vpn-gateway"></a>Helyek közötti VPN-átjáró

A helyek közötti VPN-átjáró a CloudSimple régióhálózat a helyszíni adatközpontok közötti titkosított forgalom küldésére szolgál. Ezzel a kapcsolattal határozhatja meg az alhálózatokat/CIDR-tartományt a helyszíni hálózat és a CloudSimple régióhálózat közötti hálózati forgalomhoz.

A VPN-átjáró lehetővé teszi, hogy a magánfelhő helyszíni szolgáltatásait, a magánfelhőszolgáltatásait pedig a helyszíni hálózatról használja fel.  A CloudSimple házirend-alapú VPN-kiszolgálót biztosít a helyszíni hálózatról való kapcsolat létrehozásához.

A helyek közötti VPN használati esetei:

* A privát felhőbeli vCenter kisegítő lehetőségei a helyszíni hálózat bármely munkaállomásáról.
* A helyszíni Active Directory használata vCenter-identitásforrásként.
* A virtuálisgép-sablonok, az ISO-k és más fájlok kényelmes átvitele a helyszíni erőforrásokból a privát felhőbeli vCenterbe.
* A magánfelhőn futó számítási feladatok hozzáférhetősége a helyszíni hálózatról.

![Helyek közötti VPN-kapcsolat topológiája](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Kriptográfiai paraméterek

A helyek közötti VPN-kapcsolatok a következő alapértelmezett titkosítási paramétereket használja a biztonságos kapcsolat létrehozásához.  Amikor kapcsolatot hoz létre a helyszíni VPN-eszközről, használja az alábbi paraméterek bármelyikét, amelyeket a helyszíni VPN-átjáró támogat.

#### <a name="phase-1-proposals"></a>Az 1.

| Paraméter | 1. javaslat | 2. | 3. |
|-----------|------------|------------|------------|
| IKE verziószám | IKEv1 | IKEv1 | IKEv1 |
| Titkosítás | AES 128 | AES 256 | AES 256 |
| Kivonatoló algoritmus| SHA 256 | SHA 256 | SHA 1 |
| Diffie Hellman Csoport (DH Csoport) | 2 | 2 | 2 |
| Az élet ideje | 28 800 másodperc | 28 800 másodperc | 28 800 másodperc |
| Adatméret | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>A második fázisra vonatkozó javaslatok

| Paraméter | 1. javaslat | 2. | 3. |
|-----------|------------|------------|------------|
| Titkosítás | AES 128 | AES 256 | AES 256 |
| Kivonatoló algoritmus| SHA 256 | SHA 256 | SHA 1 |
| Tökéletes határidős titoktartási csoport (PFS Group) | None | None | None |
| Az élet ideje | 1800 másodperc | 1800 másodperc | 1800 másodperc |
| Adatméret | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> Állítsa a TCP MSS-befogást 1200-ra a VPN-eszközön. Vagy ha a VPN-eszközök nem támogatják az MSS befogást, akkor az MTU-t az alagút-kapcsolaton 1240 bájtra állíthatja.

## <a name="point-to-site-vpn-gateway"></a>Pont-hely VPN átjáró

A Point-to-Site VPN a CloudSimple régióhálózat és az ügyfélszámítógép közötti titkosított forgalom küldésére szolgál.  A point-to-site VPN a legegyszerűbb módja a magánfelhő-hálózat elérésének, beleértve a privát felhőbeli vCenter-t és a számítási feladatok virtuális gépeit.  Használja a pont-hely VPN-kapcsolatot, ha távolról csatlakozik a magánfelhőhöz.

## <a name="next-steps"></a>További lépések

* [VPN-átjáró beállítása](vpn-gateway.md)
