---
title: Infrastruktúra és kapcsolódás az Azure-SAP HANAhoz (nagyméretű példányok) | Microsoft Docs
description: Konfigurálja a szükséges kapcsolati infrastruktúrát az Azure-beli SAP HANA használatához (nagyméretű példányok esetén).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4a1df7406ab4f4d7137d12dd7131a4c26b617cb2
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869221"
---
# <a name="sap-hana-large-instances-deployment"></a>SAP HANA (nagyméretű példányok) üzembe helyezése 

Ez a cikk azt feltételezi, hogy elvégezte a Microsoft Azure-beli (nagyméretű példányok) SAP HANAának megvásárlását. A cikk elolvasása előtt általános háttérként tekintse meg a [Hana nagyméretű példányainak](hana-know-terms.md) általános kifejezéseit és a [Hana Large instances SKU](hana-available-skus.md)-t.


A Microsoft a következő információkat igényli a HANA nagyméretű példányok üzembe helyezéséhez:

- Az ügyfél neve.
- Üzleti kapcsolattartási adatok (beleértve az e-mail-címet és a telefonszámot).
- Technikai kapcsolattartási adatok (beleértve az e-mail-címet és a telefonszámot).
- Technikai hálózati kapcsolattartási adatok (beleértve az e-mail-címet és a telefonszámot).
- Azure üzembe helyezési régió (például az USA nyugati régiója, Kelet-Ausztrália vagy Észak-Európa).
- SAP HANA az Azure-ban (nagyméretű példányok) SKU (konfiguráció).
- Minden Azure-beli üzembe helyezési régióban:
    - A/29 IP-címtartomány az olyan ER-P2P kapcsolatok esetében, amelyek az Azure-beli virtuális hálózatokat a HANA nagyméretű példányaihoz kötik.
    - A HANA nagyméretű példányok kiszolgálói IP-készletéhez használt a/24 CIDR-blokk.
    - Nem kötelező, ha a [ExpressRoute-Global REACH](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) használata lehetővé teszi, hogy a helyszíni közvetlen útválasztást Hana nagyméretű példányokra vagy a különböző Azure-régiókban lévő Hana nagyméretű példány-egységek közötti útválasztásra használja, egy másik/29 IP-címtartományt kell fenntartania. Ez az adott tartomány nem fedi át a korábban definiált többi IP-címtartományt.
- A HANA nagyméretű példányokhoz csatlakozó összes Azure-beli virtuális hálózat virtuális hálózati címtartomány attribútumában használt IP-címtartomány értékei.
- Az egyes HANA Large instances rendszerekhez tartozó adatmennyiség:
  - A kívánt állomásnév, ideális esetben egy teljesen minősített tartománynév.
  - A HANA nagyméretű példányának kívánt IP-címe a kiszolgáló IP-készletének címtartományból. (A kiszolgáló IP-címkészlet címének első 30 IP-címe a HANA nagyméretű példányain belüli belső használatra van fenntartva.)
  - SAP HANA SID neve a SAP HANA példányhoz (a szükséges SAP HANA kapcsolódó kötetek létrehozásához szükséges). A Microsoftnak szüksége van a HANA SID-re az NFS-köteteken található sidadm engedélyeinek létrehozásához. Ezek a kötetek a HANA nagyméretű példány-egységhez csatlakoznak. A HANA SID a csatlakoztatott lemez kötetek egyikének a nevét is használja. Ha egynél több HANA-példányt szeretne futtatni az egységen, több HANA-SID-t is fel kell sorolnia. Mindegyikhez egy különálló kötet lesz hozzárendelve.
  - A Linux operációs rendszerekben a sidadm felhasználónak van egy csoport azonosítója. Ez az azonosító szükséges a szükséges SAP HANA kapcsolódó kötetek létrehozásához. A SAP HANA telepítés általában létrehozza a sapsys csoportot, amelynek csoportazonosító 1001. A sidadm felhasználó tagja ennek a csoportnak.
  - A Linux operációs rendszerekben a sidadm felhasználója felhasználói AZONOSÍTÓval rendelkezik. Ez az azonosító szükséges a szükséges SAP HANA kapcsolódó kötetek létrehozásához. Ha az egységen több HANA-példányt futtat, sorolja fel a sidadm összes felhasználóját. 
- Az Azure-előfizetéshez tartozó Azure-előfizetés azonosítója, amelyhez az Azure HANA nagyméretű példányain SAP HANA közvetlenül csatlakoztatva lesznek. Ez az előfizetés-azonosító az Azure-előfizetésre hivatkozik, amelyet a HANA nagyméretű példány egységével vagy egységével kell fizetni.

Az előző információk megadása után a Microsoft SAP HANA az Azure-ban (nagyméretű példányok). A Microsoft információkat küld Önnek az Azure-beli virtuális hálózatok a HANA nagyméretű példányokhoz való összekapcsolásához. A HANA nagyméretű példányok egységeit is elérheti.

A következő műveletsorral csatlakozhat a HANA nagyméretű példányaihoz a Microsoft általi üzembe helyezése után:

1. [Azure-beli virtuális gépek csatlakoztatása nagyméretű HANA-példányokhoz](hana-connect-azure-vm-large-instances.md)
2. [VNet csatlakoztatása a HANA Large instances ExpressRoute](hana-connect-vnet-express-route.md)
3. [További hálózati követelmények (nem kötelező)](hana-additional-network-requirements.md)

