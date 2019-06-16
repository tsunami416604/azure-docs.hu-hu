---
title: VMware megoldást, CloudSimple – az Azure VPN-átjárókkal
description: Ismerje meg CloudSimple site-to-site VPN és a pont – hely VPN-fogalmak
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c9689a468e8784eb4ec3590011e02a37d92d6b9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083402"
---
# <a name="vpn-gateways-overview"></a>VPN-átjárók áttekintése

VPN gateway titkosított adatforgalmat továbbíthat egy helyszíni helyhez, vagy egy számítógép, a nyilvános interneten keresztül CloudSimple régió hálózatának között szolgál.  Minden egyes régióban kizárólag egy VPN-átjáróval rendelkezhet. Egy VPN-átjáróhoz azonban létrehozhat több kapcsolatot is. Amikor többhelyes csatlakozást hoz létre egyetlen VPN-átjáróhoz, az összes VPN-alagút az elérhető sávszélességen osztozkodik.

CloudSimple kétféle VPN-átjárók biztosítja:

* Site-to-site VPN Gateway
* Pont – hely VPN-átjáró

## <a name="site-to-site-vpn-gateway"></a>Site-to-site VPN gateway

Site-to-site VPN gateway titkosított adatforgalmat továbbíthat CloudSimple régió hálózaton és a egy a helyszíni adatközpont között szolgál. Alhálózatok vagy CIDR-tartományát, a helyszíni hálózat és a CloudSimple régió közötti hálózati forgalom meghatározására használja ezt a kapcsolatot.

A VPN gateway lehetővé teszi, hogy a magánfelhőben helyszíni szolgáltatásait, valamint a magánfelhőben, a helyszíni hálózatról szolgáltatásokhoz.  CloudSimple egy házirendalapú VPN-kiszolgálót biztosít a helyszíni hálózatról való kapcsolat létrehozásakor.

Site-to-site VPN használati esetek a következők:

* A helyszíni hálózat minden munkaállomásról a magánfelhő vCenter elérhetőségét.
* Használja a helyszíni Active Directory-identitás vCenter forrásként.
* Kényelmes virtuális Gépet sablonok, ISO és egyéb fájlok való átvitele a helyszíni erőforrások a saját magánfelhő-alapú.
* Kisegítő lehetőségek, a helyszíni hálózaton a magánfelhőben futó számítási feladatokat.

![Site-to-Site VPN kapcsolati topológia](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Titkosítási paraméterek

Site-to-site VPN-kapcsolat biztonságos kapcsolatot létesíteni a következő alapértelmezett titkosítási paramétereket használja.  Ha a helyszíni VPN-eszköz kapcsolatot hoz létre, a paraméterek meg kell egyeznie.

Site-to-site VPN-kapcsolat biztonságos kapcsolatot létesíteni a következő alapértelmezett titkosítási paramétereket használja.  Ha a helyszíni VPN-eszköz kapcsolatot hoz létre, az alábbi paramétereket a helyszíni VPN-átjáró által támogatott bármelyikét használhatja.

#### <a name="phase-1-proposals"></a>1\. fázis javaslatok

| Paraméter | Javaslat: 1 | Javaslat 2 | Javaslat 3 |
|-----------|------------|------------|------------|
| IKE verziószám | IKEv1 | IKEv1 | IKEv1 |
| Encryption | AES 128 | AES 256 | AES 256 |
| Kivonatoló algoritmus| SHA 256 | SHA 256 | SHA 1 |
| Diffie-Hellman csoport (DH-csoport) | 2 | 2 | 2 |
| Élettartam | 28 800 másodperc | 28 800 másodperc | 28 800 másodperc |
| Adatok mérete | 4 GB | 4 GB | 4 GB |


#### <a name="phase-2-proposals"></a>2\. fázis javaslatok 

| Paraméter | Javaslat: 1 | Javaslat 2 | Javaslat 3 |
|-----------|------------|------------|------------|
| Encryption | AES 128 | AES 256 | AES 256 |
| Kivonatoló algoritmus| SHA 256 | SHA 256 | SHA 1 |
| Tökéletes előre csoport-sérülés utáni Titkosságvédelmi (PFS-csoport) | None | Nincsenek | None |
| Élettartam | 1,800 másodperc | 1,800 másodperc | 1,800 másodperc |
| Adatok mérete | 4 GB | 4 GB | 4 GB |

## <a name="point-to-site-vpn-gateway"></a>Pont – hely VPN-átjáró

Pont – hely VPN segítségével titkosított adatforgalmat továbbíthat egy CloudSimple terület hálózat és egy ügyfélszámítógép között.  Pont – hely VPN emellett akkor a legegyszerűbben úgy férhet hozzá a magánfelhő hálózatához, például a magánfelhő vCenter és a munkaterhelési virtuális gépekhez.  Pont – hely VPN-kapcsolat akkor használja, ha csatlakozik a magánfelhő távolról.

## <a name="next-steps"></a>További lépések

* [VPN-átjáró beállítása](https://docs.azure.cloudsimple.com/vpn-gateway/)