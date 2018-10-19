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
ms.openlocfilehash: 1d8bbe2fc218004116177c4c9d95777d9ec57503
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426058"
---
# <a name="sap-hana-large-instances-deployment"></a>SAP HANA (nagyméretű példányok) központi telepítés 

Ez a cikk azt feltételezi, hogy befejezte a vásárlást az SAP HANA az Azure-ban (nagyméretű példányok) a Microsofttól. Ez a cikk általános háttér-elolvasása előtt tekintse meg [nagyméretű HANA példányok gyakori használati](hana-know-terms.md) és [nagyméretű HANA példányok termékváltozatok](hana-available-skus.md).


A Microsoft megköveteli a HANA nagyméretű példányok egységek telepítéséhez a következő információkat:

- Ügyfél neve.
- Vállalati kapcsolattartási adatokat (például e-mail címét és telefonszámát szám).
- Technikai kapcsolattartási adatait (például e-mail címét és telefonszámát szám).
- Technikai hálózatkezelés kapcsolattartási adatait (beleértve az e-mail-cím és telefon száma).
- Azure-beli régióban (például USA nyugati RÉGIÓJA, Kelet-Ausztrália, vagy Észak-Európa).
- Az Azure-ban (nagyméretű példányok) SAP HANA (konfiguráció) Termékváltozat.
- Minden Azure üzembe helyezési régióhoz:
    - Egy/29 HANA nagyméretű példányok az Azure virtuális hálózatokhoz csatlakozó ER-P2P-kapcsolatok IP-címtartományt.
    - Egy/24 a HANA nagyméretű példányok kiszolgáló IP-készlet használt CIDR-blokkja.
- Az IP cím tartományértékeknek minden Azure virtuális hálózat, amely kapcsolódik a HANA nagyméretű példányok virtuális hálózati cím terület attribútumában.
- Mindegyik HANA nagyméretű példányok rendszer adatok:
  - Kívánt gazdagépnévvel ideális esetben a teljesen minősített tartománynevét.
  - A HANA nagyméretű példányok egység a kiszolgáló IP-készlet címtartománya nem kívánt IP-címét. (A kiszolgáló IP-készlet címtartománya az első 30 IP-címek nagyméretű HANA-példányokhoz belüli belső használatra vannak fenntartva.)
  - Az SAP HANA biztonsági azonosító neve, az SAP HANA-példány (a szükséges SAP HANA-kapcsolódó kötetek létrehozásához szükséges). A Microsoft HANA SID kell létrehozni az NFS-kötetek sidadm engedélyeit. Ezeket a köteteket a HANA nagyméretű példányok egységet csatlakoztathat. A HANA biztonsági AZONOSÍTÓT az egyik a neve, amely csatlakozik a lemezkötetek is szolgál. Ha azt szeretné, egynél több HANA-példányt futtathatnak az egység, több HANA biztonsági azonosítóit sorolja fel. Mindegyik kap egy hozzárendelt kötetek külön készlete.
  - A Linux os-ben a sidadm felhasználónak van egy csoportban. Ezt az Azonosítót a szükséges SAP HANA-kapcsolódó kötetek létrehozásához szükséges. Az SAP HANA telepítése általában egy csoport 1001-es azonosítójú hoz létre a sapsys csoport. A sidadm felhasználó, csoport tagja.
  - A Linux os-ben a sidadm felhasználó rendelkezik a felhasználói azonosítóját. Ezt az Azonosítót a szükséges SAP HANA-kapcsolódó kötetek létrehozásához szükséges. Ha több HANA-példányt futtat az egységen, listázza az összes sidadm felhasználót. 
- Az Azure-előfizetés melyik SAP Hana-hoz az Azure HANA nagyméretű példányok fog közvetlenül csatlakoztatni kívánt Azure-előfizetés azonosítója. Az előfizetés-azonosító az Azure-előfizetéssel, amely számítunk fel a HANA nagyméretű példányok egység vagy egységek fog hivatkozik.

Miután megadta a fenti információ, Microsoft kiosztja az SAP HANA az Azure-ban (nagyméretű példányok). A Microsoft HANA nagyméretű példányok az Azure virtual networkök összekapcsolása információkat küld Önnek. A HANA nagyméretű példányok egységek is elérhető.

A HANA nagyméretű példányok csatlakozni, a Microsoft rendelkezik üzembe helyezése után kövesse az alábbi eljárást:

1. [Azure virtuális gépek csatlakoztatásához a nagyméretű HANA-példányok](hana-connect-azure-vm-large-instances.md)
2. [Virtuális hálózat csatlakoztatása HANA nagyméretű példányok az ExpressRoute](hana-connect-vnet-express-route.md)
3. [További hálózati követelmények (nem kötelező)](hana-additional-network-requirements.md)

