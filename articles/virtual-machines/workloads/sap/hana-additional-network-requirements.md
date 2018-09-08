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
ms.openlocfilehash: 8ee89553846309bd96f3cad3648e337488cf07d3
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164716"
---
# <a name="additional-network-requirements"></a>További hálózati követelmények

Néha szükség lehet további hálózati követelmények HANA nagyméretű példányok telepítésének részeként. Ez a cikk azt mutatja be az alábbi hálózati követelmények:
- [Több IP-cím vagy alhálózat hozzáadása ](#adding-more-ip-addresses-or-subnets)
- [Virtuális vetworks hozzáadása](#adding-vnets)
- [Az expressroute-kapcsolatcsoport sávszélességét növelése](#increasing-expressroute-circuit-bandwidth)
- [Egy további expressroute-kapcsolatcsoportot beszereznem hozzáadása](#adding-an-additional-expressroute-circuit)
- [Egy alhálózat törlése folyamatban van](#deleting-a-subnet)
- [Egy virtuális hálózat törlése](#deleting-a-vnet)
- [Egy expressz útválasztó-kapcsolatcsoport törlése](#deleting-an-expressroute-circuit)


## <a name="adding-more-ip-addresses-or-subnets"></a>Több IP-címekről vagy alhálózatokról hozzáadása

Használja a az Azure portal, PowerShell, vagy a parancssori felület, amikor hozzáadása több IP-címek vagy alhálózatok.

Ebben az esetben a javaslatot, hogy az új IP-címtartományt új tartomány hozzáadása a virtuális hálózat Címtere új összevont tartományt generálása helyett. Mindkét esetben ez a változás, amely engedélyezi a csatlakozást ki, hogy új IP-címtartományt, a nagyméretű HANA-példány egységeket az ügyfél a Microsoft elküldése kell. Megnyithat egy Azure-támogatási kérést a új virtuális hálózat címterét hozzáadott beolvasása. Miután megkapta a megerősítést, hajtsa végre a következő lépéseket.

Egy további alhálózat létrehozásához az Azure Portalról, tekintse meg ezt a cikket [hozzon létre egy virtuális hálózatot az Azure portal használatával](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network), és a powershellből való létrehozására, tekintse meg a [hozzon létre egy virtuális hálózathoz a PowerShell használatával](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="adding-vnets"></a>Virtuális hálózatok hozzáadása

Egy vagy több Azure virtuális hálózatok kezdetben csatlakozás után érdemes SAP HANA az Azure-ban (nagyméretű példányok) hozzáférő újak hozzáadásához. Először is egy Azure-támogatási kérelmet küldeni, alkotó tartalmazzák mind az adott Azure üzembe helyezési és az Azure virtuális hálózati címtér az IP cím terület tranzakciónaplók azonosító információkat. SAP HANA az Azure Service Management majd biztosítja a szükséges információkat, a további virtuális hálózatok és az ExpressRoute csatlakoznia kell. Minden olyan virtuális hálózathoz kell egy egyedi hitelesítési kulcsot az ExpressRoute-kapcsolatcsoport nagyméretű HANA-példányokhoz való csatlakozáshoz.

Adjon hozzá egy új Azure VNet lépései:

1. Befejeződött a bevezetési folyamat első lépéseként tekintse meg a _létrehozása Azure virtuális hálózat_ szakaszban.
2. Befejeződött a bevezetési folyamat második lépése tekintse meg a _átjáró-alhálózat létrehozását_ szakaszban.
3. A további virtuális hálózatok összekapcsolása a HANA nagyméretű példány ExpressRoute-kapcsolatcsoporthoz, nyisson meg egy Azure-támogatási kérést az új VNet információkat, és a egy új hitelesítési kulcs kérése.
4. Ha értesítést kap, hogy helyesek-e az engedélyt, tekintse meg a használatát a Microsoft által biztosított engedélyezési információkat befejezéséhez a harmadik lépése a bevezetési folyamat a _összekapcsolása virtuális hálózatok_ szakaszban.

## <a name="increasing-expressroute-circuit-bandwidth"></a>ExpressRoute-kapcsolatcsoport sávszélességét növelése

Tekintse meg az SAP HANA az Azure Service Management. Ha azt javasoljuk, növelje az SAP HANA az Azure-ban (nagyméretű példányok) ExpressRoute-kapcsolatcsoport sávszélességét, hozzon létre egy Azure-támogatáskérést. (Akkor is növelni egy egyetlen kapcsolatcsoport sávszélességét, legfeljebb 10 GB/s.) Ezt követően kap értesítést a művelet befejezése; után az Azure-ban a nagyobb sebesség engedélyezéséhez szükséges további művelet.

## <a name="adding-an-additional-expressroute-circuit"></a>További ExpressRoute-kapcsolatcsoport hozzáadása

Tekintse meg az SAP HANA az Azure Service Management, ha azt javasoljuk, hogy további ExpressRoute-kapcsolatcsoport van szükség, győződjön meg arról, az Azure-támogatási kérelem egy új ExpressRoute-kapcsolatcsoport létrehozása (és a csatlakozáshoz engedélyezési adatok beolvasása). A címtér fogja használni a virtuális hálózatok engedélyezési adja meg az SAP HANA az Azure Service Management ahhoz a kérelem végrehajtása előtt definiálni kell.

Az új kapcsolatcsoport létrehozása és az SAP HANA az Azure Service Management configuration befejeződése után fog értesítés információkkal kell folytatnia az eljárást. Kövesse a fenti lépéseket követve létrehozása és csatlakozás az új VNet a további kapcsolatcsoport számára. Ön nem tud Azure virtuális hálózatok összekapcsolása a további kapcsolatcsoporthoz, ha azok már csatlakozik egy másik SAP HANA (nagyméretű példányok) az Azure ExpressRoute-kapcsolatcsoport Azure ugyanabban a régióban az.

## <a name="deleting-a-subnet"></a>Egy alhálózat törlése folyamatban van

Egy virtuális hálózat alhálózatához eltávolításához az Azure portal, PowerShell vagy parancssori felület is használható. Abban az esetben az Azure virtuális hálózat IP cím tartományt vagy az Azure virtuális hálózati címtér lett egy összesített címtartományt, van Microsoft-szolgáltatásra, nincs kövesse. Azzal a különbséggel, hogy a virtuális hálózaton van továbbra is propagálása a BGP-útvonal címtér, amely tartalmazza a törölt alhálózat. Ha több IP-címtartományt, amely egyik hozzá lett rendelve a törölt alhálózatot meghatározva, az Azure virtuális hálózat IP cím tartományt vagy az Azure virtuális hálózat Címtere, törölje, amely ki a virtuális hálózati címtér, és ezt követően tájékoztatja az SAP HANA az Azure Service Management Távolítsa el a tartományból, hogy az SAP HANA az Azure-ban (nagyméretű példányok) való kommunikációhoz.

Alhálózat törlése, lásd: [alhálózat törlése](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet) alhálózatok létrehozásával kapcsolatos további információt.

## <a name="deleting-a-vnet"></a>Virtuális hálózat törlése

Tekintse meg a virtuális hálózat törlése [virtuális hálózat törlése](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network). SAP HANA az Azure Service Management eltávolítja a meglévő engedélyeket az SAP HANA (nagyméretű példányok) az Azure ExpressRoute-kapcsolatcsoport az a, és távolítsa el az Azure virtuális hálózat IP cím tartományt vagy az Azure virtuális hálózati címtér a kommunikáció a nagyméretű HANA-példányokhoz.

A virtuális hálózat az Eltávolítás után, nyissa meg egy Azure-támogatási kérést adja meg az IP cím terület tranzakciónaplók el kell távolítani.

Annak érdekében, hogy mindent, a rendszer eltávolítja, törölje a következő elemek:

- Az ExpressRoute-kapcsolatokban a virtuális hálózat átjárója, virtuális hálózati átjáró nyilvános IP-Címek és virtuális hálózat

## <a name="deleting-an-expressroute-circuit"></a>Egy ExpressRoute-kapcsolatcsoport törlése

Egy további SAP HANA (nagyméretű példányok) az Azure ExpressRoute-kapcsolatcsoport eltávolításához nyisson meg egy Azure-támogatási kérelmet az SAP HANA az Azure Service Management és a kérés, hogy a kapcsolatcsoport törölni kell. Az Azure-előfizetés keretében, törölheti, és szükség esetén a virtuális hálózatok közötti megtartása. Azonban törölnie kell a kapcsolat a HANA nagyméretű példányok ExpressRoute-kapcsolatcsoport és a társított virtuális hálózati átjáró között.

Ha is el kívánja távolítani a virtuális hálózaton, kövesse az útmutatást törléséről című fenti szakaszban egy virtuális hálózathoz.

**Következő lépések**

- Tekintse meg [telepítése és konfigurálása az SAP HANA (nagyméretű példányok) az Azure-ban](hana-installation.md).
