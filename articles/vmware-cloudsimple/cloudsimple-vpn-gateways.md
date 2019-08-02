---
title: VPN-átjárók VMware-megoldásban a CloudSimple által – Azure
description: Tudnivalók a helyek közötti VPN-és pont – hely VPN-fogalmak CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 47d61f80cae926965dd71342980302c2b3045c52
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689645"
---
# <a name="vpn-gateways-overview"></a>VPN-átjárók – áttekintés

A VPN-átjárók a CloudSimple-régió hálózata és a nyilvános interneten keresztüli számítógép közötti titkosított forgalom elküldésére szolgálnak.  Minden régiónak csak egy VPN-átjárója lehet. Egy VPN-átjáróhoz azonban létrehozhat több kapcsolatot is. Amikor többhelyes csatlakozást hoz létre egyetlen VPN-átjáróhoz, az összes VPN-alagút az elérhető sávszélességen osztozkodik.

A CloudSimple kétféle VPN-átjárót biztosít:

* Helyek közötti VPN Gateway
* Pont – hely VPN Gateway

## <a name="site-to-site-vpn-gateway"></a>Helyek közötti VPN-átjáró

A helyek közötti VPN-átjárók a CloudSimple és a helyszíni adatközpontok közötti titkosított forgalom küldésére szolgálnak. Ezzel a kapcsolattal meghatározhatja az alhálózatok/CIDR tartományát a helyszíni hálózat és a CloudSimple régió hálózata közötti kommunikációhoz.

A VPN Gateway lehetővé teszi, hogy a helyszíni hálózatról a saját felhőben és a saját felhőben lévő szolgáltatásokat használja a helyszíni hálózaton.  A CloudSimple egy házirend-alapú VPN-kiszolgálót biztosít a helyszíni hálózattal létesített kapcsolat létesítéséhez.

Helyek közötti VPN-hez használható esetek:

* A saját felhőalapú vCenter a helyszíni hálózat bármely munkaállomásáról elérhetővé teheti.
* A helyszíni Active Directory használata vCenter-identitás forrásaként.
* A virtuálisgép-sablonok, az ISO-fájlok és a helyszíni erőforrások egyéb fájljainak kényelmes átvitele a saját Felhőbeli vCenter.
* A helyszíni hálózatról a saját felhőben futó munkaterhelések hozzáférhetősége.

![Helyek közötti VPN-kapcsolat topológiája](media/cloudsimple-site-to-site-vpn-connection.png)

> [!IMPORTANT]
> A TCP MSS-t 1078 bájt vagy alacsonyabb értékre kell befogni. Ha a VPN-eszközök nem támogatják a MSS-befogást, akkor a bújtatási felületen lévő MTU-t 1118 bájtra is állíthatja. 

### <a name="cryptographic-parameters"></a>Titkosítási paraméterek

A helyek közötti VPN-kapcsolat a következő alapértelmezett titkosítási paramétereket használja a biztonságos kapcsolat létrehozásához.  Ha a helyszíni VPN-eszközről hoz létre kapcsolatokat, használja a helyszíni VPN-átjáró által támogatott alábbi paramétereket.

#### <a name="phase-1-proposals"></a>1\. fázis – javaslatok

| Paraméter                       | 1\. javaslat     | 2\. javaslat     | 3\. javaslat     |
|---------------------------------|----------------|----------------|----------------|
| IKE verziószám                     | IKEv1          | IKEv1          | IKEv1          |
| Encryption                      | AES 128        | AES 256        | AES 256        |
| Kivonatoló algoritmus                  | SHA 256        | SHA 256        | SHA 1          |
| Diffie Hellman-csoport (DH-csoport) | 2              | 2              | 2              |
| Üzemidő                       | 28 800 másodperc | 28 800 másodperc | 28 800 másodperc |
| Adatok mérete                       | 4 GB           | 4 GB           | 4 GB           |
| Kapcsolat megszakadásának észlelése (DPD)       | Letiltva/kikapcsolva   | Letiltva/kikapcsolva   | Letiltva/kikapcsolva   |


#### <a name="phase-2-proposals"></a>2\. fázis – javaslatok 

| Paraméter                                 | 1\. javaslat    | 2\. javaslat    | 3\. javaslat    |
|-------------------------------------------|---------------|---------------|---------------|
| Encryption                                | AES 128       | AES 256       | AES 256       |
| Kivonatoló algoritmus                            | SHA 256       | SHA 256       | SHA 1         |
| Tökéletes továbbítási titoktartási csoport (PFS-csoport) | None          | Nincsenek          | Nincsenek          |
| Üzemidő                                 | 1 800 másodperc | 1 800 másodperc | 1 800 másodperc |
| Adatok mérete                                 | 4 GB          | 4 GB          | 4 GB          |

## <a name="point-to-site-vpn-gateway"></a>Pont – hely típusú VPN-átjáró

A pont – hely típusú VPN a CloudSimple-régió hálózata és az ügyfélszámítógép közötti titkosított forgalom elküldésére szolgál.  A saját felhőalapú hálózatának, például a saját felhőalapú vCenter és a számítási feladatok virtuális gépei elérésének legegyszerűbb módja a pont – hely VPN.  Pont – hely típusú VPN-kapcsolat használata, ha távolról csatlakozik a privát felhőhöz.

## <a name="next-steps"></a>További lépések

* [VPN-átjáró beállítása](https://docs.azure.cloudsimple.com/vpn-gateway/)