---
title: Azure VMware-megoldás CloudSimple-alapú VPN-átjárók használatával
description: Tudnivalók a helyek közötti VPN-és pont – hely VPN-fogalmak CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8b7b02c164021ac084dccf5663122620b2af1b15
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972894"
---
# <a name="vpn-gateways-overview"></a>VPN-átjárók – áttekintés

A VPN-átjárók a CloudSimple-régió hálózata és a nyilvános interneten keresztüli számítógép közötti titkosított forgalom elküldésére szolgálnak.  Minden régió rendelkezhet egy VPN-átjáróval, amely több kapcsolatot is képes támogatni. Amikor többhelyes csatlakozást hoz létre egyetlen VPN-átjáróhoz, az összes VPN-alagút az elérhető sávszélességen osztozkodik.

A CloudSimple kétféle VPN-átjárót biztosít:

* Helyek közötti VPN-átjáró
* Pont – hely típusú VPN-átjáró

## <a name="site-to-site-vpn-gateway"></a>Helyek közötti VPN-átjáró

A helyek közötti VPN-átjárók a CloudSimple és a helyszíni adatközpontok közötti titkosított forgalom küldésére szolgálnak. Ezzel a kapcsolattal meghatározhatja az alhálózatok/CIDR tartományát a helyszíni hálózat és a CloudSimple régió hálózata közötti hálózati forgalom számára.

A VPN Gateway lehetővé teszi, hogy a helyszíni hálózatról származó szolgáltatásokat a saját felhőben és a saját felhőben lévő szolgáltatásokból is felhasználja.  A CloudSimple egy házirend-alapú VPN-kiszolgálót biztosít a helyszíni hálózattal létesített kapcsolat létrehozásához.

Helyek közötti VPN-re vonatkozó esetek használata:

* A saját felhőalapú vCenter a helyszíni hálózat bármely munkaállomásáról elérhetővé teheti.
* A helyszíni Active Directory használata vCenter-identitás forrásaként.
* A virtuálisgép-sablonok, az ISO-fájlok és a helyszíni erőforrások egyéb fájljainak kényelmes átvitele a saját Felhőbeli vCenter.
* A helyszíni hálózatról a saját felhőben futó munkaterhelések hozzáférhetősége.

![Helyek közötti VPN-kapcsolat topológiája](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Titkosítási paraméterek

A helyek közötti VPN-kapcsolat a következő alapértelmezett titkosítási paramétereket használja a biztonságos kapcsolat létrehozásához.  Ha a helyszíni VPN-eszköz kapcsolatát hozza létre, használja a helyszíni VPN-átjáró által támogatott alábbi paramétereket.

#### <a name="phase-1-proposals"></a>1\. fázis – javaslatok

| Paraméter | 1\. javaslat | 2\. javaslat | 3\. javaslat |
|-----------|------------|------------|------------|
| IKE verziószám | IKEv1 | IKEv1 | IKEv1 |
| Encryption | AES 128 | AES 256 | AES 256 |
| Kivonatoló algoritmus| SHA 256 | SHA 256 | SHA 1 |
| Diffie Hellman-csoport (DH-csoport) | 2 | 2 | 2 |
| Üzemidő | 28 800 másodperc | 28 800 másodperc | 28 800 másodperc |
| Adatok mérete | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>2\. fázis – javaslatok

| Paraméter | 1\. javaslat | 2\. javaslat | 3\. javaslat |
|-----------|------------|------------|------------|
| Encryption | AES 128 | AES 256 | AES 256 |
| Kivonatoló algoritmus| SHA 256 | SHA 256 | SHA 1 |
| Tökéletes továbbítási titoktartási csoport (PFS-csoport) | Nincsenek | Nincsenek | Nincsenek |
| Üzemidő | 1 800 másodperc | 1 800 másodperc | 1 800 másodperc |
| Adatok mérete | 4 GB | 4 GB | 4 GB |

## <a name="point-to-site-vpn-gateway"></a>Pont – hely típusú VPN-átjáró

A pont – hely típusú VPN a CloudSimple-régió hálózata és az ügyfélszámítógép közötti titkosított forgalom elküldésére szolgál.  A saját felhőalapú hálózatának, például a saját felhőalapú vCenter és a számítási feladatok virtuális gépei elérésének legegyszerűbb módja a pont – hely VPN.  Pont – hely típusú VPN-kapcsolat használata, ha távolról csatlakozik a privát felhőhöz.

## <a name="next-steps"></a>További lépések

* [VPN-átjáró beállítása](vpn-gateway.md)
