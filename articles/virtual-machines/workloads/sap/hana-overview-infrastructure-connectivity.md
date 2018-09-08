---
title: Infrastruktúra és kapcsolódás az Azure-ban (nagyméretű példányok) SAP Hana-hoz |} A Microsoft Docs
description: SAP HANA az Azure-ban (nagyméretű példányok) használandó kapcsolat szükséges infrastruktúra konfigurálása.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c61dffc6fd9d0c65f5e925daebdf2a02cfb5d6ba
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161356"
---
# <a name="sap-hana-large-instances-deployment"></a>SAP HANA (nagyméretű példányok) központi telepítés 

Miután megvásárlása esetén az SAP HANA az Azure-ban (nagyméretű példányok) le van zárva, és a Microsoft enterprise-fiókok ügyfélszolgálata között, a következő információkat a Microsoft telepítéséhez szükséges HANA nagyméretű példány egységek:

- Ügyfél neve
- Üzleti kapcsolattartási adatait (beleértve az e-mail cím és telefon száma)
- Technikai elérhetőségeit (beleértve az e-mail-cím és telefon száma)
- Technikai hálózati kapcsolattartási adatait (beleértve az e-mail-cím és telefon száma)
- Azure-beli régióban (USA nyugati RÉGIÓJA, USA keleti RÉGIÓJA, Kelet-Ausztrália, Délkelet-Ausztrália, Nyugat-Európa és 2017. július Észak-Európa)
- Erősítse meg az SAP HANA az Azure (nagyméretű példányok) Termékváltozat (konfiguráció)
- Mint már részletes áttekintése és architektúrája dokumentumban HANA nagyméretű példányokhoz, minden helyezésüket az Azure-régióhoz:
    - Egy/29 HANA nagyméretű példányok az Azure virtuális hálózatok csatlakoztatása ER-P2P-kapcsolatok IP-címtartomány
    - Egy/24 a HANA nagyméretű példányok kiszolgáló IP-készlet használt CIDR-blokkja
- Minden Azure virtuális hálózat, amely kapcsolódik a HANA nagyméretű példányok virtuális hálózati címtér attribútumában IP cím tartományértékeknek
- Adatok HANA nagyméretű példányok rendszer mindegyikéhez:
  - Kívánt gazdagépnévvel – ideális esetben a teljesen minősített tartománynevét.
  - A nagyméretű HANA-példány egység - kiszolgáló IP-készlet címtartománya nem kívánt IP-címe ne feledje, hogy a kiszolgáló IP-készlet címtartománya az első 30 IP-címek nagyméretű HANA-példányokhoz belüli belső használatra vannak fenntartva
  - Az SAP HANA biztonsági azonosító neve, az SAP HANA-példány (a szükséges SAP HANA-kapcsolódó kötetek létrehozásához szükséges). A HANA biztonsági AZONOSÍTÓRA szükség az NFS-kötetek, a nagyméretű HANA-példány egység első csatlakoztatott sidadm engedélyeinek létrehozni. Is használható egyik a neve, a kötet, amelyet csatlakozik. Ha azt szeretné, egynél több HANA-példányt futtathatnak az egység, fel kell több HANA biztonsági azonosítókkal való létrehozásához. Mindegyik kap egy hozzárendelt kötetek külön készlete.
  - A groupid, a Linux operációs rendszer a sidadm felhasználó rendelkezik a szükséges SAP HANA-kapcsolódó kötetek létrehozásához szükséges. Az SAP HANA telepítése egy csoport azonosítójú 1001, általában a sapsys csoportot hoz létre. A sidadm felhasználó, csoport tagja
  - A szükséges SAP HANA-kapcsolódó kötetek létrehozása a felhasználói azonosítóját a sidadm felhasználó rendelkezik-e a Linux operációs rendszer szükséges. Ha több HANA-példányt futtat az egységet, fel kell az összes a <sid>adm-felhasználók 
- Azure-előfizetési Azonosítóját az Azure-előfizetés melyik SAP Hana-hoz az Azure HANA nagyméretű példányok fog közvetlenül egymáshoz. Az előfizetés-azonosító az Azure-előfizetést, amely a nagyméretű HANA-példány egység megállapítva fog hivatkozik.

Miután megadta az adatokat, a Microsoft kiosztja az SAP HANA az Azure-ban (nagyméretű példányok), és adja vissza az adatokat az Azure virtuális hálózatok összekapcsolása a nagyméretű HANA-példányok és a nagyméretű HANA-példány egységek eléréséhez szükséges.

A cikk elolvasása előtt ismerkedjen meg [HANA nagyméretű példányok gyakori használati](hana-know-terms.md) és a [HANA nagyméretű példányok termékváltozatok](hana-available-skus.md).

A következő szakasz segítségével után terjesztve van a Microsoft által nagyméretű HANA-példányokhoz csatlakozhat:

1. [Az Azure virtuális gépek csatlakoztatásához a nagyméretű HANA-példányok](hana-connect-azure-vm-large-instances.md)
2. [Virtuális hálózat csatlakoztatása HANA nagyméretű példányok az ExpressRoute](hana-connect-vnet-express-route.md)
3. [További hálózati követelmények (nem kötelező)](hana-additional-network-requirements.md)

**Következő lépések**

- Tekintse meg [HANA nagyméretű példányok az Azure virtuális gépek csatlakoztatásához](hana-connect-azure-vm-large-instances.md).
- Tekintse meg [egy virtuális hálózathoz csatlakozik a HANA nagyméretű példányok ExpressRoute](hana-connect-vnet-express-route.md).