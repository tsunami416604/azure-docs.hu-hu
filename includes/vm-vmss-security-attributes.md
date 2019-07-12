---
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2019
ms.author: mbaldwin
ms.openlocfilehash: df11493fa9663d3fcbf0a2f74a5acbead55a25fb
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800275"
---
## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például a kiszolgálóoldali titkosítás, a kiszolgálóoldali titkosítást az ügyfél által felügyelt kulcsokat és más titkosítási szolgáltatások) | Igen | Lásd: [az Azure-beli Linuxos virtuális gép titkosítása](/azure/virtual-machines/linux/encrypt-disks.md) és [a Windows virtuális gép virtuális lemezek titkosítása](/azure/virtual-machines/windows/encrypt-disks.md). |
| Titkosítás az átvitel során (például az ExpressRoute-titkosítás, a virtuális hálózatok közötti titkosítás és a VNet – VNet titkosítási)| Igen | Az Azure Virtual Machines támogatja [ExpressRoute](/azure/expressroute) és a virtuális hálózatok közötti titkosítást. Lásd: [az átvitel közbeni titkosítás, a virtuális gépek](/azure/security/security-azure-encryption-overview.md#in-transit-encryption-in-vms). |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| Igen | Felhasználó által kezelt kulcsokkal egy Azure-titkosítás támogatott forgatókönyv; Lásd: [Azure-titkosítás áttekintése](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms.md).|
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – | |
| Titkosított API-hívások| Igen | HTTPS és SSL. |

## <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| Igen | |
| VNet-injektálási támogatás| Igen | . |
| Hálózatelkülönítés és Firewalling támogatása| Igen |  |
| Kényszerített bújtatás támogatása| Igen | Lásd: [kényszerített bújtatás, az Azure Resource Manager-alapú üzemi modellel konfigurálása](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | Lásd: [figyelésére és frissítésére az Azure-beli Linuxos virtuális gép](/azure/virtual-machines/linux/tutorial-monitoring.md) és [figyelésére és frissítésére az Azure-beli Windows virtuális gép](/azure/virtual-machines/windows/tutorial-monitoring.md). |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen |  |
| Authorization| Igen |  |


## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen |  |
| Adatsík naplózása és naplózása | Nem |  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen |  | 
