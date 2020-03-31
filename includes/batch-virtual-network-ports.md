---
title: fájl belefoglalása
description: fájl belefoglalása
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/04/2020
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: e9460108499ca76d1b149b61cebe3d3081bf6544
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086254"
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

**További hálózati erőforrások** – A Batch automatikusan további hálózati erőforrásokat foglal le a virtuális hálózatot tartalmazó erőforráscsoportban.

> [!IMPORTANT]
>Minden 50 dedikált csomópont (vagy minden 20 alacsony prioritású csomópont) batch lefoglalja: egy hálózati biztonsági csoport (NSG), egy nyilvános IP-cím, és egy terheléselosztó. Ezekre az erőforrásokra az előfizetésben meghatározott [erőforráskvóták](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) vonatkoznak. Nagy készletek esetén előfordulhat, hogy kvótanövelést kell kérnie egy vagy több erőforráshoz.

#### <a name="network-security-groups-batch-default"></a>Hálózati biztonsági csoportok: Alapértelmezés kötegelve

Az alhálózatnak lehetővé kell tennie a Batch szolgáltatás bejövő kommunikációját, hogy a számítási csomópontokon feladatokat ütemezhet, és kimenő kommunikációt az Azure Storage-szal vagy más erőforrásokkal való kommunikációhoz, ha a munkaterhelés szükséges. A virtuális gép konfigurációjában lévő készletek esetében a Batch nsg-ket ad hozzá a számítási csomópontokhoz csatlakoztatott hálózati adapterek (NIC) szinten. Ezek az NSG-k a következő további szabályokkal vannak konfigurálva:

* Bejövő TCP-forgalom a 29876-os és a 29877-es porton a Batch szolgáltatás IP-címének, amely megfelel a `BatchNodeManagement` szolgáltatáscímkének.
* A bejövő TCP-forgalom a 22-es porton (Linux-csomópontok) vagy a 3389-es porton (Windows-csomópontok) keresztül a távoli hozzáférés engedélyezéséhez. Bizonyos típusú többpéldányos feladatok Linuxon (például MPI), akkor is engedélyeznie kell az SSH-port 22 forgalmat az IP-k a Batch számítási csomópontokat tartalmazó alhálózatban. Ez alhálózati szintű NSG-szabályokszerint blokkolható (lásd alább).
* Kimenő forgalom bármilyen porton keresztül a virtuális hálózathoz. Ez alhálózati szintű NSG-szabályok szerint módosítható (lásd alább).
* Kimenő forgalom bármely porton az internetre. Ez alhálózati szintű NSG-szabályok szerint módosítható (lásd alább).

> [!IMPORTANT]
> Körültekintően járjon el a bejövő vagy kimenő szabályok módosításakor és hozzáadásakor a Batch által konfigurált NSG-kben. Ha a megadott alhálózaton a számítási csomópontok felé irányuló kommunikációt egy NSG letiltja, akkor a Batch szolgáltatás **nem használhatóra** állítja a számítási csomópontok állapotát. Ezenkívül a Batch által létrehozott erőforrásokra nem kell erőforrászárolást alkalmazni, ellenkező esetben megakadályozhatja az erőforrások karbantartását a felhasználó által kezdeményezett műveletek, például a készlet törlése miatt.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Hálózati biztonsági csoportok: Alhálózati szintű szabályok megadása

Nem szükséges az NSG-ket a virtuális hálózat alhálózati szintjén megadni, mert a Batch saját NSG-ket konfigurál (lásd fent). Ha nsg-t társít az alhálózathoz, ahol a Batch számítási csomópontok telepítve vannak, vagy egyéni NSG-szabályokat szeretne alkalmazni az alkalmazott alapértelmezett értékek felülbírálásához, akkor ezt az NSG-t legalább a bejövő és kimenő biztonsági szabályokkal kell konfigurálnia az alábbiak szerint Táblázatok.

Csak akkor konfigurálja a bejövő forgalmat a 3389-es (Windows) vagy a 22-es (Linux) porton, ha engedélyeznie kell a távoli hozzáférést a külső forrásokból származó számítási csomópontokhoz. Előfordulhat, hogy engedélyeznie kell a 22-es portszabályait Linuxon, ha bizonyos MPI-futtatási időszakokkal rendelkező többpéldányos feladatok támogatására van szüksége. A portok forgalmának engedélyezése nem feltétlenül szükséges ahhoz, hogy a készlet számítási csomópontjai használhatóak legyenek.

**Bejövő biztonsági szabályok**

| Forrás IP-címek | Forrásszolgáltatás címkéje | Forrásportok | Cél | Célportok | Protocol (Protokoll) | Műveletek |
| --- | --- | --- | --- | --- | --- | --- |
| N/A | `BatchNodeManagement`[Szolgáltatáscímke](../articles/virtual-network/security-overview.md#service-tags) (ha regionális változatot használ, ugyanabban a régióban, mint a Batch-fiók) | * | Bármelyik | 29876-29877 | TCP | Engedélyezés |
| Felhasználói forrás IP-címeket a számítási csomópontok és/vagy a számítási csomópont alhálózat a Linux többpéldányos feladatok, ha szükséges. | N/A | * | Bármelyik | 3389 (Windows), 22 (Linux) | TCP | Engedélyezés |

**Kimenő biztonsági szabályok**

| Forrás | Forrásportok | Cél | Cél szolgáltatáscímkéje | Célportok | Protocol (Protokoll) | Műveletek |
| --- | --- | --- | --- | --- | --- | --- |
| Bármelyik | * | [Szolgáltatáscímke](../articles/virtual-network/security-overview.md#service-tags) | `Storage`(ha regionális változatot használ, ugyanabban a régióban, mint a Batch-fiók) | 443 | TCP | Engedélyezés |

### <a name="pools-in-the-cloud-services-configuration"></a>Készletek a Cloud Services konfigurációjában

**Támogatott virtuális hálózatok** – Csak a klasszikus virtuális hálózatok

**Alhálózati azonosító** – Az alhálózat Batch API-kkal történő megadásakor használja az alhálózat *erőforrás-azonosítóját*. Az alhálózat azonosítója a következő formátumot követi:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Engedélyek** – A `Microsoft Azure Batch` szolgáltatásnévnek rendelkeznie kell a `Classic Virtual Machine Contributor` szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörrel az adott virtuális hálózaton.

#### <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)

Az alhálózatnak engedélyeznie kell a Batch szolgáltatástól kiinduló bejövő kommunikációt, hogy képes legyen feladatok ütemezésére a számítási csomópontokon, illetve a kimenő kommunikációt, hogy kommunikálhasson az Azure Storage szolgáltatással vagy más erőforrásokkal.

Nem kell megadnia NSG-t, mert a Batch szolgáltatás csak a Batch IP-címeiről a készletezett csomópontokra érkező bejövő kommunikációt konfigurálja. Ugyanakkor ha a megadott alhálózathoz hálózati biztonsági csoportok (NSG-k) és/vagy egy tűzfal van társítva, a következő táblázatokban látható módon konfigurálja a bejövő és kimenő biztonsági szabályokat. Ha a megadott alhálózaton a számítási csomópontok felé irányuló kommunikációt egy NSG letiltja, akkor a Batch szolgáltatás **nem használhatóra** állítja a számítási csomópontok állapotát.

Konfigurálja a bejövő forgalmat a Windows 3389-es portján, ha engedélyeznie kell az RDP-hozzáférést a készletcsomópontokhoz. Ez nem szükséges a készletezett csomópontok használhatóságához.

**Bejövő biztonsági szabályok**

| Forrás IP-címek | Forrásportok | Cél | Célportok | Protocol (Protokoll) | Műveletek |
| --- | --- | --- | --- | --- | --- |
Bármelyik <br /><br />Bár ehhez tulajdonképpen „az összes engedélyezése” engedély szükséges, a Batch szolgáltatás minden egyes csomópont szintjén alkalmaz egy ACL-szabályt, amely kiszűri az összes olyan IP-címet, amely nem a Batch szolgáltatáshoz tartozik. | * | Bármelyik | 10100, 20100, 30100 | TCP | Engedélyezés |
| Nem kötelező, ha lehetővé teszi az RDP hozzáférést a számítási csomópontokhoz. | * | Bármelyik | 3389 | TCP | Engedélyezés |

**Kimenő biztonsági szabályok**

| Forrás | Forrásportok | Cél | Célportok | Protocol (Protokoll) | Műveletek |
| --- | --- | --- | --- | --- | --- |
| Bármelyik | * | Bármelyik | 443  | Bármelyik | Engedélyezés |
