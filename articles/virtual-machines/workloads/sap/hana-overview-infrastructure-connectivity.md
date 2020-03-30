---
title: Infrastruktúra és kapcsolat az SAP HANA-val az Azure-ban (nagy példányok) | Microsoft dokumentumok
description: Konfigurálja a szükséges kapcsolati infrastruktúrát az SAP HANA azure-beli (nagy példányok) használatához.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cea89087742f1987f693b8bfb627bd71038a0c14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616922"
---
# <a name="sap-hana-large-instances-deployment"></a>SAP HANA (nagy példányok) telepítése 

Ez a cikk feltételezi, hogy befejezte az SAP HANA megvásárlását az Azure-ban (nagy példányok) a Microsofttól. A cikk elolvasása előtt az általános háttér, [lásd: HANA nagy példányok közös kifejezések](hana-know-terms.md) és [HANA nagy példányok SK.](hana-available-skus.md).


A Microsoftnak a hana-példányok központi telepítéséhez a következő adatokra van szüksége:

- Vevő neve.
- Üzleti kapcsolattartási adatok (beleértve az e-mail címet és a telefonszámot).
- Műszaki kapcsolattartási adatok (beleértve az e-mail címet és a telefonszámot).
- Műszaki hálózati kapcsolattartási adatok (beleértve az e-mail címet és a telefonszámot).
- Azure telepítési régió (például USA nyugati régiója, Kelet-Ausztrália vagy Észak-Európa).
- SAP HANA az Azure-ban (nagy példányok) Termékváltozat (konfiguráció).
- Minden Azure-telepítési régióesetében:
    - A /29 IP-címtartomány ER-P2P kapcsolatok, amelyek összekötik az Azure virtuális hálózatok HANA nagy példányok.
    - A HANA nagypéldányok kiszolgálóIP-készletéhez használt /24 CIDR blokk.
    - Ha nem kötelező [az ExpressRoute globális elérés](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) használatával a helyszíni hana nagy példány egységek közötti közvetlen útválasztás vagy a HANA nagy példány egységek közötti útválasztás különböző Azure-régiókban, le kell foglalnia egy másik /29 IP-címtartományt. Előfordulhat, hogy ez a tartomány nem fedi át a korábban megadott többi IP-címtartományt.
- A virtuális hálózati címtér attribútumában használt IP-címtartomány-értékek minden olyan Azure virtuális hálózathoz, amely a HANA nagypéldányokhoz csatlakozik.
- Adatok az egyes HANA nagy példányok rendszer:
  - A kívánt állomásnév, ideális esetben egy teljesen minősített domain névvel.
  - A HANA nagypéldány-egység kívánt IP-címe a kiszolgáló IP-készletcímtartományán kívül. (A kiszolgáló IP-készletcímtartományában szereplő első 30 IP-cím a HANA nagypéldányain belüli belső használatra van fenntartva.)
  - SAP HANA SID-név az SAP HANA-példány (szükséges a szükséges SAP HANA-val kapcsolatos lemezkötetek létrehozásához szükséges). A Microsoftnak szüksége van a HANA-biztonsági azonosítóra az NFS-köteteken lévő sidadm engedélyei létrehozásához. Ezek a kötetek a HANA nagypéldány-egységhez kapcsolódnak. A HANA SID is használják, mint az egyik név összetevői a lemez kötetek, hogy csatlakoztatva van. Ha azt szeretné, hogy egynél több HANA-példányt az egységen, több HANA-azonosítók at kell sorolnia. Mindegyik külön kötetkészletet kap hozzárendelve.
  - A Linux operációs rendszerben a sidadm felhasználó rendelkezik egy csoportazonosítóval. Ez az azonosító szükséges a szükséges SAP HANA-val kapcsolatos lemezkötetek létrehozásához. Az SAP HANA telepítése általában létrehozza a sapsys csoportot, 1001-es csoportazonosítóval. A sidadm felhasználó része, hogy a csoport.
  - A Linux operációs rendszerben a sidadm felhasználó rendelkezik felhasználói azonosítóval. Ez az azonosító szükséges a szükséges SAP HANA-val kapcsolatos lemezkötetek létrehozásához. Ha több HANA-példányt futtat az egységen, sorolja fel az összes sidadm-felhasználót. 
- Az Azure-előfizetési azonosító, amelyhez az Azure HANA az Azure HANA nagy példányai közvetlenül kapcsolódnak. Ez az előfizetési azonosító az Azure-előfizetésre hivatkozik, amely a HANA nagy példányegységével vagy egységeivel lesz felszámítva.

Miután az előző adatokat, a Microsoft rendelkezések SAP HANA az Azure-ban (nagy példányok). A Microsoft adatokat küld az Azure virtuális hálózatok hana nagy példányokkal való összekapcsolására. A HANA nagy példányegységek is hozzáférhet.

A következő szekvenciával csatlakozhat a HANA nagy példányaihoz, miután a Microsoft telepítette azt:

1. [Az Azure-beli virtuális gépek csatlakoztatása hana-példányokhoz](hana-connect-azure-vm-large-instances.md)
2. [Virtuális hálózat csatlakoztatása hana nagypéldányokhoz ExpressRoute](hana-connect-vnet-express-route.md)
3. [További hálózati követelmények (nem kötelező)](hana-additional-network-requirements.md)

