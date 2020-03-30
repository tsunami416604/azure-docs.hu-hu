---
title: További hálózati követelmények az SAP HANA az Azure-ban (nagy példányok) | Microsoft dokumentumok
description: Az SAP HANA további hálózati követelményei az Azure-ban (nagy példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 66bbd8b462ac35756be0fae6eba940ba0aba6c4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614578"
---
# <a name="additional-network-requirements-for-large-instances"></a>További hálózati követelmények nagy példányokra

Előfordulhat, hogy további hálózati követelmények részeként az SAP HANA nagy példányainak az Azure-ban központi telepítése.

## <a name="add-more-ip-addresses-or-subnets"></a>További IP-címek vagy alhálózatok hozzáadása

Használja az Azure Portalon, a PowerShellben vagy az Azure CLI-ben, ha további IP-címeket vagy alhálózatokat ad hozzá.

Adja hozzá az új IP-címtartományt új tartományként a virtuális hálózati címterülethez, ahelyett, hogy új összesített tartományt hozna létre. Küldje el ezt a módosítást a Microsoftnak. Ez lehetővé teszi, hogy az új IP-címtartományból csatlakozzon az ügyfél HANA nagy példányegységeihez. Megnyithat egy Azure-támogatási kérelmet az új virtuális hálózati címterület hozzáadásához. Miután megkapta a megerősítést, hajtsa végre a következő lépéseket.

Ha további alhálózatot szeretne létrehozni az Azure Portalról, olvassa el a Virtuális hálózat létrehozása az Azure Portal használatával című [témakört.](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network) Ha a PowerShellből szeretne létrehozni egyet, olvassa el a Virtuális hálózat létrehozása a PowerShell használatával című [témakört.](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)

## <a name="add-virtual-networks"></a>Virtuális hálózatok hozzáadása

Miután először csatlakozott egy vagy több Azure virtuális hálózathoz, érdemes lehet csatlakozni a további, amelyek az SAP HANA azure-beli (nagy példányok) eléréséhez. Először küldjön el egy Azure-támogatási kérelmet. Ebben a kérelemben adja meg az adott Azure-telepítés azonosítására szolgáló konkrét információkat. Az IP-címterület-tartományt vagy az Azure virtuális hálózati címterület tartományait is tartalmazza. Az SAP HANA a Microsoft Service Management szolgáltatáskezelő ezután biztosítja a szükséges információkat a további virtuális hálózatok és az Azure ExpressRoute csatlakoztatásához. Minden virtuális hálózathoz egyedi engedélyezési kulcsra van szükség az ExpressRoute-kapcsolat hana nagy példányokhoz való csatlakoztatásához.

## <a name="increase-expressroute-circuit-bandwidth"></a>Az ExpressRoute-áramkör sávszélességének növelése

Forduljon az SAP HANA-hoz a Microsoft Service Management szolgáltatáskezelés. Ha azt tanácsolják, hogy növelje az SAP HANA sávszélességét az Azure-beli (nagy példányok) ExpressRoute-kapcsolaton, hozzon létre egy Azure-támogatási kérelmet. (Egyetlen áramköri sávszélesség növelését kérheti legfeljebb 10 Gb/s-ig.) Ezután értesítést kap a művelet befejezése után; nem kell mást tennie ahhoz, hogy ezt a nagyobb sebességet engedélyezze az Azure-ban.

## <a name="add-an-additional-expressroute-circuit"></a>További ExpressRoute-áramkör hozzáadása

Forduljon az SAP HANA-hoz a Microsoft Service Management szolgáltatáskezelés. Ha azt tanácsolják, hogy adjon hozzá egy további ExpressRoute-csoportot, hozzon létre egy Azure-támogatási kérelmet (beleértve az engedélyezési adatok lekérését az új kapcsolatcsoporthoz való csatlakozáshoz). A kérelem benyújtása előtt meg kell határoznia a virtuális hálózatokon használt címterületet. Az SAP HANA a Microsoft Service Management ezután engedélyt adhat.

Amikor az új kapcsolatcsoport jön létre, és az SAP HANA a Microsoft Service Management konfiguráció befejeződött, értesítést kap a folytatáshoz szükséges információkat. Nem tud azure-beli virtuális hálózatokat csatlakoztatni ehhez a további kapcsolatcsoporthoz, ha azok már csatlakoznak egy másik SAP HANA-hoz az Azure -ban (nagypéldány) ExpressRoute-kapcsolaton ugyanabban az Azure-régióban.

## <a name="delete-a-subnet"></a>Alhálózat törlése

Virtuális hálózati alhálózat eltávolításához használhatja az Azure Portal, a PowerShell vagy az Azure CLI. Ha az Azure virtuális hálózati IP-címtartománya vagy címterülete összesített tartomány volt, a Microsoft nem követi nyomon. (Ne feledje azonban, hogy a virtuális hálózat továbbra is propagálja a BGP-útvonal címterületét, amely tartalmazza a törölt alhálózatot.) Előfordulhat, hogy az Azure virtuális hálózati címtartományt vagy címterületet több IP-címtartományként definiálta, amelyek közül az egyik a törölt alhálózathoz volt rendelve. Ügyeljen arra, hogy törölje a virtuális hálózati címterületről. Ezután tájékoztassa az SAP HANA-t a Microsoft Service Management szolgáltatáskezelésről, hogy távolítsa el azokat a tartományokat, amelyekkel az SAP HANA az Azure-ban (nagy példányok) kommunikálhat.

További információt az [Alhálózat törlése](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet)című témakörben talál.

## <a name="delete-a-virtual-network"></a>Virtuális hálózat törlése

További információt a [Virtuális hálózat törlése](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network)című témakörben talál.

SAP HANA a Microsoft Service Management eltávolítja a meglévő engedélyeket az SAP HANA az Azure-ban (nagy példányok) ExpressRoute-kapcsolat. Emellett eltávolítja az Azure virtuális hálózati IP-címtartományt vagy címterületet a HANA nagy példányokkal való kommunikációhoz.

A virtuális hálózat eltávolítása után nyisson meg egy Azure-támogatási kérelmet az ELTÁVOLÍTandó IP-címterület-tartomány vagy -tartományok megadásához.

Annak érdekében, hogy mindent eltávolítson, törölje az ExpressRoute-kapcsolatot, a virtuális hálózati átjárót, a nyilvános virtuális hálózati átjáró t és a virtuális hálózatot.

## <a name="delete-an-expressroute-circuit"></a>ExpressRoute-kapcsolat törlése

Ha el szeretne távolítani egy további SAP HANA az Azure-ban (nagy példányok) ExpressRoute-kapcsolat, nyisson meg egy Azure-támogatási kérelmet az SAP HANA a Microsoft Service Management. Kérje az áramkör törlését. Az Azure-előfizetésen belül szükség esetén törölheti vagy megtarthatja a virtuális hálózatot. Azonban törölnie kell a hana nagy példányok ExpressRoute-kapcsolat és a csatolt virtuális hálózati átjáró közötti kapcsolatot.

## <a name="next-steps"></a>További lépések

- [Az SAP HANA (nagy méretű példányok) telepítése és konfigurálása az Azure-ban](hana-installation.md)
