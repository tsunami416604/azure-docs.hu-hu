---
title: SAP HANA az Azure-ban (nagyméretű példányok) további hálózati követelményei |} A Microsoft Docs
description: További hálózati követelmények az SAP Hana az Azure-ban (nagyméretű példányok).
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
ms.openlocfilehash: 2f4c74076f2763addc1f2b971d8ea0ca29ce6fd0
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49392998"
---
# <a name="additional-network-requirements-for-large-instances"></a>Nagyméretű példányok további hálózati követelményei

Lehetséges, hogy további hálózati követelmények az Azure-beli SAP HANA nagyméretű példányok egy központi telepítésének részeként.

## <a name="add-more-ip-addresses-or-subnets"></a>Több IP-címekről vagy alhálózatokról hozzáadása

Több IP-címekről vagy alhálózatokról hozzáadásakor, használja az Azure portal, PowerShell vagy az Azure parancssori felület.

Az új IP-címtartományt egy új tartomány hozzáadása a virtuális hálózat címterét, egy új összevont tartományt generálása helyett. Ez a változás a Microsoftnak elküldeni. Ez lehetővé teszi, hogy a HANA nagyméretű példányok egységeket az ügyfél új IP-címtartományba tartozó kapcsolódjon. Megnyithat egy Azure-támogatáskérést beolvasni a hozzáadott új virtuális hálózat címterét. Miután megkapta a megerősítést, hajtsa végre a következő lépéseket.

Egy további alhálózat létrehozása az Azure Portalról: [hozzon létre egy virtuális hálózatot az Azure portal használatával](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). Egy, a Powershellből létrehozásához lásd: [hozzon létre egy virtuális hálózathoz a PowerShell használatával](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Virtuális hálózatok hozzáadása

Egy vagy több Azure virtuális hálózatok kezdetben csatlakozás után előfordulhat, hogy szeretné csatlakoztatni az SAP HANA az Azure-ban (nagyméretű példányok) hozzáférő újak. Először is nyújt az Azure-támogatási kérést. A kérelemben szereplő közé tartozik az adott Azure üzembe helyezési azonosító információkat. Az IP-címtartomány terület vagy tartományok az Azure virtuális hálózat címtere is tartalmazhatnak. Microsoft Service Management az SAP HANA majd biztosítja a szükséges információkat, a további virtuális hálózatok és az Azure ExpressRoute csatlakoznia kell. Minden virtuális hálózathoz kell egy egyedi hitelesítési kulcsot az ExpressRoute-kapcsolatcsoport nagyméretű HANA-példányokhoz való csatlakozáshoz.

## <a name="increase-expressroute-circuit-bandwidth"></a>ExpressRoute-kapcsolatcsoport sávszélességét növelése

Tekintse meg az SAP HANA-szolgáltatás a Microsoft Management. Ha azok javasolja, hogy növelje az SAP HANA az Azure-ban (nagyméretű példányok) ExpressRoute-kapcsolatcsoport sávszélességét, hozzon létre egy Azure-támogatáskérést. (Akkor is növelni egy egyetlen kapcsolatcsoport sávszélességét, legfeljebb 10 GB/s.) Ezt követően kap értesítést a művelet befejezése; után bármi más, a nagyobb sebesség, az Azure-beli engedélyezéséhez nem kell.

## <a name="add-an-additional-expressroute-circuit"></a>További ExpressRoute-kapcsolatcsoport hozzáadása

Tekintse meg az SAP HANA-szolgáltatás a Microsoft Management. Ha azok javasolja, hogy egy további ExpressRoute-kapcsolatcsoportot, hozzon létre egy Azure-támogatási kérelmet (beleértve az engedélyezési adatok csatlakozni az új kapcsolatcsoport beolvasására irányuló kérelem). A kérés előtt meg kell határoznia a címtér a virtuális hálózatokon használt. Microsoft Service Management az SAP HANA engedélyezési majd biztosít.

Ha az új kapcsolatcsoport jön létre, és az SAP HANA az Microsoft Service Management configuration befejeződött, információkkal kell folytatnia az eljárást értesítést kap. Ön nem tud Azure virtuális hálózatok csatlakozni a további Kapcsolatcsoportok, ha azok már kapcsolódik egy másik SAP HANA (nagyméretű példányok) az Azure ExpressRoute-kapcsolatcsoport a ugyanazon Azure-régióban.

## <a name="delete-a-subnet"></a>Alhálózat törlése

Távolítsa el a virtuális hálózat alhálózatán, használhatja az Azure portal, PowerShell vagy az Azure parancssori felület. Ha az Azure virtuális hálózat IP-tartomány vagy cím címtér lett egy összesített címtartományt, nem Microsoft-szolgáltatásra, nincs kövesse. (Ne feledje, hogy a virtuális hálózat továbbra is propagálása a BGP útválasztási címtér, amely magában foglalja a törölt alhálózati.) Előfordulhat, hogy meghatározta a az Azure virtual network-tartomány vagy cím terület, több IP-címtartományok, amely egyik hozzá volt rendelve a törlése alhálózatnak. Győződjön meg arról, törölni, amely a virtuális hálózat címterét. Microsoft Service Management eltávolíthatja azokat a tartományokat, hogy az SAP HANA az Azure-ban (nagyméretű példányok) kommunikálni az SAP HANA majd tájékoztatja.

További információkért lásd: [alhálózat törlése](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Virtuális hálózat törlése

További információ: [virtuális hálózat törlése](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network).

Microsoft Service Management az SAP HANA eltávolítja az SAP HANA az Azure-ban (nagyméretű példányok) ExpressRoute-kapcsolatcsoport a meglévő engedélyeket. Az Azure virtuális hálózat IP-tartomány vagy cím címtér a nagyméretű HANA-példányokhoz való kommunikációhoz is eltávolítja.

Miután eltávolítja a virtuális hálózatot, nyissa meg egy Azure-támogatási kérést adja meg az IP-cím terület tartomány vagy tartományok el kell távolítani.

Annak érdekében, távolítson el minden, az ExpressRoute-kapcsolatra, a virtuális hálózati átjáró, a virtuális hálózati átjáró nyilvános IP-cím és a virtuális hálózat törlése.

## <a name="delete-an-expressroute-circuit"></a>Egy ExpressRoute-kapcsolatcsoport törlése

Egy további SAP HANA (nagyméretű példányok) az Azure ExpressRoute-kapcsolatcsoport eltávolításához nyisson meg egy Azure-támogatáskérést SAP HANA-Microsoft Service Management. A kérés, hogy a kapcsolatcsoport törölni kell. Az Azure-előfizetés keretében, törölheti, és a virtuális hálózat, szükség szerint. Azonban törölnie kell a kapcsolat a HANA nagyméretű példányok ExpressRoute-kapcsolatcsoport és a társított virtuális hálózati átjáró között.

## <a name="next-steps"></a>További lépések

- [Az SAP HANA (nagy méretű példányok) telepítése és konfigurálása az Azure-ban](hana-installation.md)
