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
ms.date: 04/03/2020
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: dc08dcded6418208751edbffcb5d263db059ec01
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80657481"
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
>Minden 50 dedikált csomóponthoz (vagy 20 alacsony prioritású csomóponthoz) a Batch kiosztása: egy hálózati biztonsági csoport (NSG), egy nyilvános IP-cím és egy terheléselosztó. Ezekre az erőforrásokra az előfizetésben meghatározott [erőforráskvóták](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) vonatkoznak. Nagyméretű készletek esetén előfordulhat, hogy egy vagy több ilyen erőforráshoz kvótát kell emelni.

#### <a name="network-security-groups-batch-default"></a>Hálózati biztonsági csoportok: batch alapértelmezett

Az alhálózatnak engedélyeznie kell a Batch szolgáltatás bejövő kommunikációját, hogy képes legyen a feladatok ütemezhetnek a számítási csomópontokon, és a kimenő kommunikáció az Azure Storage szolgáltatással vagy más erőforrásokkal való kommunikációhoz szükséges. A virtuális gép konfigurációjában lévő készletek esetében a Batch a számítási csomópontokhoz csatolt hálózati adapterek (NIC) szintjén adja hozzá a NSG. Ezek a NSG a következő további szabályokkal vannak konfigurálva:

* Bejövő TCP-forgalom a 29876-es és a `BatchNodeManagement` 29877-es portokon a szolgáltatás címkéjének megfelelő batch szolgáltatás IP-címeitől.
* A bejövő TCP-forgalom a 22-es porton (Linux-csomópontok) vagy a 3389-es porton (Windows-csomópontok) keresztül a távoli hozzáférés engedélyezéséhez. A Linuxon futó, több példányos feladatokhoz (például MPI) is engedélyezni kell az SSH-port 22-es forgalmát a Batch számítási csomópontokat tartalmazó alhálózat IP-címei számára. Ez az alhálózati szintű NSG-szabályok (lásd alább) esetében blokkolva lehet.
* Kimenő forgalom bármilyen porton keresztül a virtuális hálózathoz. Ez az alhálózati szintű NSG-szabályok alapján módosítható (lásd alább).
* Kimenő forgalom bármely porton az interneten. Ez az alhálózati szintű NSG-szabályok alapján módosítható (lásd alább).

> [!IMPORTANT]
> Körültekintően járjon el a bejövő vagy kimenő szabályok módosításakor és hozzáadásakor a Batch által konfigurált NSG-kben. Ha a megadott alhálózaton a számítási csomópontok felé irányuló kommunikációt egy NSG letiltja, akkor a Batch szolgáltatás **nem használhatóra** állítja a számítási csomópontok állapotát. Emellett nem kell erőforrás-zárolást alkalmazni a Batch által létrehozott összes erőforrásra, ellenkező esetben a felhasználó által kezdeményezett műveletek, például a készlet törlése miatt megakadályozhatja az erőforrások törlését.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Hálózati biztonsági csoportok: alhálózat szintű szabályok megadására

A virtuális hálózati alhálózat szintjén nem szükséges megadni a NSG, mert a Batch konfigurálja a saját NSG (lásd fent). Ha van olyan NSG társítva az alhálózathoz, ahol a Batch számítási csomópontok telepítve vannak, vagy ha egyéni NSG szabályokat szeretne alkalmazni az alapértelmezett beállítások felülbírálására, akkor a NSG-t legalább a bejövő és kimenő biztonsági szabályokkal kell konfigurálnia, ahogy az a következő táblázatban látható.

Konfigurálja a bejövő forgalmat a 3389 (Windows) vagy a 22 (Linux) porton, ha engedélyezni szeretné a külső forrásokból származó számítási csomópontok távoli elérését. Előfordulhat, hogy engedélyezni kell a 22-es portra vonatkozó szabályokat a Linuxon, ha több példányra vonatkozó, bizonyos MPI-futtatókörnyezetekkel kapcsolatos feladatokra van szüksége. Ezen portok forgalmának engedélyezése nem feltétlenül szükséges ahhoz, hogy a készlet számítási csomópontjai felhasználhatók legyenek.

**Bejövő biztonsági szabályok**

| Forrás IP-címek | Forrásoldali szolgáltatás címkéje | Forrásportok | Cél | Célportok | Protocol (Protokoll) | Műveletek |
| --- | --- | --- | --- | --- | --- | --- |
| N/A | `BatchNodeManagement`[Szolgáltatás címkéje](../articles/virtual-network/security-overview.md#service-tags) (ha regionális változatot használ, a Batch-fiókkal megegyező régióban) | * | Bármelyik | 29876-29877 | TCP | Engedélyezés |
| Felhasználói forrás IP-címei a számítási csomópontok és/vagy a számítási csomópont alhálózatának távoli eléréséhez a Linux többpéldányos feladataihoz, ha szükséges. | N/A | * | Bármelyik | 3389 (Windows), 22 (Linux) | TCP | Engedélyezés |

> [!WARNING]
> A Batch szolgáltatás IP-címei idővel változhatnak. Ezért erősen ajánlott a NSG-szabályokhoz használni `BatchNodeManagement` a szolgáltatási címkét (vagy a regionális változatot). Nem ajánlott a NSG-szabályokat közvetlenül a Batch szolgáltatás IP-címeivel feltölteni.

**Kimenő biztonsági szabályok**

| Forrás | Forrásportok | Cél | Cél szolgáltatáscímkéje | Célportok | Protocol (Protokoll) | Műveletek |
| --- | --- | --- | --- | --- | --- | --- |
| Bármelyik | * | [Szolgáltatáscímke](../articles/virtual-network/security-overview.md#service-tags) | `Storage`(regionális változat használata esetén a Batch-fiókkal megegyező régióban) | 443 | TCP | Engedélyezés |

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

Konfigurálja a bejövő forgalmat a Windows 3389-es portján, ha engedélyeznie kell az RDP-hozzáférést a készlet csomópontjaihoz. Ez nem szükséges a készletezett csomópontok használhatóságához.

**Bejövő biztonsági szabályok**

| Forrás IP-címek | Forrásportok | Cél | Célportok | Protocol (Protokoll) | Műveletek |
| --- | --- | --- | --- | --- | --- |
Bármelyik <br /><br />Bár ehhez tulajdonképpen „az összes engedélyezése” engedély szükséges, a Batch szolgáltatás minden egyes csomópont szintjén alkalmaz egy ACL-szabályt, amely kiszűri az összes olyan IP-címet, amely nem a Batch szolgáltatáshoz tartozik. | * | Bármelyik | 10100, 20100, 30100 | TCP | Engedélyezés |
| Nem kötelező, ha engedélyezni szeretné a számítási csomópontok RDP-hozzáférését. | * | Bármelyik | 3389 | TCP | Engedélyezés |

**Kimenő biztonsági szabályok**

| Forrás | Forrásportok | Cél | Célportok | Protocol (Protokoll) | Műveletek |
| --- | --- | --- | --- | --- | --- |
| Bármelyik | * | Bármelyik | 443  | Bármelyik | Engedélyezés |
