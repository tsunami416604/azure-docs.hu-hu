---
title: fájl belefoglalása
description: fájl belefoglalása
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 04/10/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 711b662c35b5f8fec96f1edee765696bc1028bf8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127514"
---
### <a name="general-requirements"></a>Általános követelmények

* A virtuális hálózatnak a Batch-fiókkal megegyező előfizetésben és régióban kell lennie.

* A virtuális hálózatot használó készletnek legfeljebb 4096 csomópontja lehet.

* A készlethez meghatározott alhálózatnak elegendő hozzá nem rendelt IP-címmel kell rendelkeznie ahhoz, hogy helyet tudjon adni a készlethez kijelölt számú virtuális gépnek. Ez a szám a készlet `targetDedicatedNodes` és `targetLowPriorityNodes` tulajdonságának összege. Ha az alhálózaton nincs elegendő hozzá nem rendelt IP-cím, akkor a készlet részlegesen lefoglalja a számítási csomópontokat, és átméretezési hiba következik be. 

* Az Azure Storage-végpontot bármely, a virtuális hálózatot kiszolgáló egyéni DNS-kiszolgálónak kell feloldania. Az `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` és `<account>.blob.core.windows.net` űrlap URL-címeinek feloldhatónak kell lenniük. 

A virtuális hálózat további követelményei eltérhetnek attól függően, hogy a Batch-készlet a virtuálisgép- vagy a Cloud Services-konfigurációban van-e. Új készlet virtuális hálózatba történő üzembe helyezéséhez a virtuálisgép-konfiguráció javasolt.

### <a name="pools-in-the-virtual-machine-configuration"></a>A virtuálisgép-konfigurációban lévő készletek

**Támogatott virtuális hálózatok** – Csak az Azure Resource Manager-alapú virtuális hálózatok

**Alhálózati azonosító** – Az alhálózat Batch API-kkal történő megadásakor használja az alhálózat *erőforrás-azonosítóját*. Az alhálózat azonosítója a következő formátumot követi:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}
  ```

**Engedélyek** – Ellenőrizze, hogy a virtuális hálózat előfizetésén vagy erőforráscsoportján lévő biztonsági szabályzatok vagy zárolások korlátozzák-e egy felhasználó virtuális hálózat kezelésére vonatkozó engedélyét.

**További hálózati erőforrások** – A Batch automatikusan további hálózati erőforrásokat foglal le a virtuális hálózatot tartalmazó erőforráscsoportban. Az egyes 50 dedikált csomópontok (vagy minden 20 alacsony prioritású csomópontok) kötegelt foglalja le: 1, 1 hálózati biztonsági csoport (NSG) és 1 nyilvános IP-címet a terheléselosztó. Ezekre az erőforrásokra az előfizetésben meghatározott [erőforráskvóták](../articles/azure-subscription-service-limits.md) vonatkoznak. Nagy készletekhez szükség lehet a kvóta egy vagy több erőforrásra való megemelésének igénylésére.

#### <a name="network-security-groups"></a>Hálózati biztonsági csoportok

Az alhálózatnak engedélyeznie kell a Batch szolgáltatástól kiinduló bejövő kommunikációt, hogy képes legyen feladatok ütemezésére a számítási csomópontokon, illetve a kimenő kommunikációt, hogy kommunikálhasson az Azure Storage szolgáltatással vagy más erőforrásokkal. A virtuálisgép-konfigurációban lévő készletekhez a Batch az NSG-ket a virtuális gépekhez kapcsolt hálózati adapterek (NIC-k) szintjén adja hozzá. Ezek az NSG-k automatikusan konfigurálnak bejövő és kimenő szabályokat a következő forgalom engedélyezéséhez:

* A Batch szolgáltatási szerepkör IP-címeiről érkező bejövő TCP-forgalom a 29876-os és a 29877-es portokon keresztül. 
* A bejövő TCP-forgalom a 22-es porton (Linux-csomópontok) vagy a 3389-es porton (Windows-csomópontok) keresztül a távoli hozzáférés engedélyezéséhez. Az egyes többpéldányos tevékenységek (például az MPI) Linux rendszeren típusú kell is módosuló IP-címek 22-es portot SSH-forgalmat a Batch számítási csomópontokon tartalmazó alhálózatról.
* Kimenő forgalom bármilyen porton keresztül a virtuális hálózathoz.
* Kimenő forgalom bármilyen porton keresztül az internetre.

> [!IMPORTANT]
> Körültekintően járjon el a bejövő vagy kimenő szabályok módosításakor és hozzáadásakor a Batch által konfigurált NSG-kben. Ha a megadott alhálózaton a számítási csomópontok felé irányuló kommunikációt egy NSG letiltja, akkor a Batch szolgáltatás **nem használhatóra** állítja a számítási csomópontok állapotát.

Nem kell megadnia NSG-t az alhálózat szintjén, mert a Batch konfigurálja a saját NSG-it. Ugyanakkor ha a megadott alhálózathoz hálózati biztonsági csoportok (NSG-k) és/vagy egy tűzfal van társítva, a következő táblázatokban látható módon konfigurálja a bejövő és kimenő biztonsági szabályokat. Konfigurálja a bejövő forgalmat a 3389-es porton (Windows) vagy a 22-es (Linux), csak akkor, ha a távoli hozzáférés engedélyezése a készlet virtuális gépekhez külső forrásokból kell. Ez nem szükséges a készletezett virtuális gépek használhatóságához. Vegye figyelembe, hogy szüksége lesz az engedélyezéséhez a virtuális hálózati alhálózat forgalmat a 22-es portot Linux, ha bizonyos típusú többpéldányos tevékenységek, például az MPI használatával.

**Bejövő biztonsági szabályok**

| Forrás IP-címek | Forrás-szolgáltatáscímke | Forrásportok | Célhely | Célportok | Protocol | Műveletek |
| --- | --- | --- | --- | --- | --- | --- |
| – | `BatchNodeManagement` [Szolgáltatáscímke](../articles/virtual-network/security-overview.md#service-tags) | * | Bármely | 29876-29877 | TCP | Engedélyezés |
| Felhasználói forrás IP-címek távolról fér hozzá a számítási csomópontok, illetve a számítási csomópont alhálózatot Linux többpéldányos tevékenységek esetén, ha szükséges. | – | * | Bármely | 3389 (Windows), 22 (Linux) | TCP | Engedélyezés |

**Kimenő biztonsági szabályok**

| Source | Forrásportok | Célhely | Cél-szolgáltatáscímke | Protocol | Műveletek |
| --- | --- | --- | --- | --- | --- |
| Bármely | 443 | [Szolgáltatáscímke](../articles/virtual-network/security-overview.md#service-tags) | `Storage` (az ugyanabban a régióban, a Batch-fiók és a virtuális hálózat)  | Bármely | Engedélyezés |

### <a name="pools-in-the-cloud-services-configuration"></a>Készletek a Cloud Services konfigurációjában

**Támogatott virtuális hálózatok** – Csak a klasszikus virtuális hálózatok

**Alhálózati azonosító** – Az alhálózat Batch API-kkal történő megadásakor használja az alhálózat *erőforrás-azonosítóját*. Az alhálózat azonosítója a következő formátumot követi:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicVirtualNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Engedélyek** – A `MicrosoftAzureBatch` szolgáltatásnévnek rendelkeznie kell a `Classic Virtual Machine Contributor` szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörrel az adott virtuális hálózaton.

#### <a name="network-security-groups"></a>Hálózati biztonsági csoportok

Az alhálózatnak engedélyeznie kell a Batch szolgáltatástól kiinduló bejövő kommunikációt, hogy képes legyen feladatok ütemezésére a számítási csomópontokon, illetve a kimenő kommunikációt, hogy kommunikálhasson az Azure Storage szolgáltatással vagy más erőforrásokkal.

Nem kell megadnia NSG-t, mert a Batch szolgáltatás csak a Batch IP-címeiről a készletezett csomópontokra érkező bejövő kommunikációt konfigurálja. Ugyanakkor ha a megadott alhálózathoz hálózati biztonsági csoportok (NSG-k) és/vagy egy tűzfal van társítva, a következő táblázatokban látható módon konfigurálja a bejövő és kimenő biztonsági szabályokat. Ha a megadott alhálózaton a számítási csomópontok felé irányuló kommunikációt egy NSG letiltja, akkor a Batch szolgáltatás **nem használhatóra** állítja a számítási csomópontok állapotát.

Konfigurálja a bejövő forgalmat a 3389-es portot Windows Ha RDP-hozzáférést a készlet csomópontjain van szüksége. Ez nem szükséges a készletezett csomópontok használhatóságához.

**Bejövő biztonsági szabályok**

| Forrás IP-címek | Forrásportok | Célhely | Célportok | Protocol | Műveletek |
| --- | --- | --- | --- | --- | --- |
Bármely <br /><br />Bár ehhez tulajdonképpen „az összes engedélyezése” engedély szükséges, a Batch szolgáltatás minden egyes csomópont szintjén alkalmaz egy ACL-szabályt, amely kiszűri az összes olyan IP-címet, amely nem a Batch szolgáltatáshoz tartozik. | * | Bármely | 10100, 20100, 30100 | TCP | Engedélyezés |
| Nem kötelező, a számítási csomópontok RDP-hozzáférést. | * | Bármely | 3389 | TCP | Engedélyezés |

**Kimenő biztonsági szabályok**

| Source | Forrásportok | Célhely | Célportok | Protocol | Műveletek |
| --- | --- | --- | --- | --- | --- |
| Bármely | * | Bármely | 443  | Bármely | Engedélyezés |
