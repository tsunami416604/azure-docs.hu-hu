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
>Minden 50 dedikált csomóponthoz (vagy minden 20 alacsony prioritású csomóponthoz) a Batch lefoglal 1 hálózati biztonsági csoportot (NSG-t), 1 nyilvános IP-címet és 1 terheléselosztót. Ezekre az erőforrásokra az előfizetésben meghatározott [erőforráskvóták](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) vonatkoznak. Nagy készletekhez szükség lehet a kvóta egy vagy több erőforrásra való megemelésének igénylésére.

#### <a name="network-security-groups-batch-default"></a>Hálózati biztonsági csoportok: Batch-alapértelmezés

Az alhálózatnak engedélyeznie kell a Batch szolgáltatástól kiinduló bejövő kommunikációt, hogy képes legyen feladatok ütemezésére a számítási csomópontokon, illetve a kimenő kommunikációt, hogy kommunikálhasson az Azure Storage szolgáltatással vagy más erőforrásokkal, a számítási feladat igényeinek megfelelően. A virtuálisgép-konfigurációban lévő készletekhez a Batch az NSG-ket a számítási csomópontokhoz kapcsolt hálózati adapterek (NIC-k) szintjén adja hozzá. Ezek az NSG-k a következő kiegészítő szabályokkal vannak konfigurálva:

* A Batch szolgáltatás a `BatchNodeManagement` szolgáltatáscímkének megfelelő IP-címeiről érkező bejövő TCP-forgalom a 29876-os és a 29877-es portokon keresztül.
* A bejövő TCP-forgalom a 22-es porton (Linux-csomópontok) vagy a 3389-es porton (Windows-csomópontok) keresztül a távoli hozzáférés engedélyezéséhez. A Linuxon futó többpéldányos feladatok bizonyos típusai (például MPI) esetében is engedélyezni kell a 22-es SSH-port forgalmát a Batch számítási csomópontokat tartalmazó alhálózat IP-címei számára. Ez az alhálózati szintű NSG-szabályok (lásd alább) alapján blokkolható.
* Kimenő forgalom bármilyen porton keresztül a virtuális hálózathoz. Ez az alhálózati szintű NSG-szabályok (lásd alább) alapján módosítható.
* Kimenő forgalom bármilyen porton keresztül az internetre. Ez az alhálózati szintű NSG-szabályok (lásd alább) alapján módosítható.

> [!IMPORTANT]
> Körültekintően járjon el a bejövő vagy kimenő szabályok módosításakor és hozzáadásakor a Batch által konfigurált NSG-kben. Ha a megadott alhálózaton a számítási csomópontok felé irányuló kommunikációt egy NSG letiltja, akkor a Batch szolgáltatás **nem használhatóra** állítja a számítási csomópontok állapotát. Emellett nem szabad erőforrás-zárolást alkalmazni a Batch által létrehozott semmilyen erőforrásra, mivel az a felhasználó által kezdeményezett műveletek, például egy készlet törlése eredményeképpen megakadályozhatja az erőforrások eltávolítását.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Hálózati biztonsági csoportok: Alhálózati szintű szabályok meghatározása

Nem szükséges megadnia NSG-t a virtuális hálózat alhálózati szintjén, mert a Batch konfigurálja a saját NSG-it (lásd fentebb). Ha rendelkezik olyan NSG-vel, amely ahhoz az alhálózathoz van társítva. amelyen a Batch számítási csomópontok üzembe lettek helyezve, vagy egyéni NSG-szabályokat szeretne használni az alkalmazott alapértelmezett beállítások felülbírálására, akkor legalább a következő táblázatokban látható bejövő és kimenő biztonsági szabályokkal kell konfigurálnia az adott NSG-t.

Csak akkor konfigurálja a bejövő forgalmat a 3389-es porton (Windows) vagy a 22-es porton (Linux) keresztül, ha engedélyeznie kell a számítási csomópontok külső forrásból való távoli elérését. Linux rendszeren előfordulhat, hogy engedélyezni kell a 22-es portra vonatkozó szabályokat, ha többpéldányos, bizonyos MPI-futtatókörnyezetekkel rendelkező feladatok támogatására van szüksége. E portok forgalmának engedélyezése nem feltétlenül szükséges a készletezett számítási csomópontok használhatóságához.

**Bejövő biztonsági szabályok**

| Forrás IP-címek | Forrás szolgáltatáscímke | Forrásportok | Cél | Célportok | Protokoll | Műveletek |
| --- | --- | --- | --- | --- | --- | --- |
| N/A | `BatchNodeManagement` [Szolgáltatáscímke](../articles/virtual-network/security-overview.md#service-tags) (regionális változat használata esetén, a Batch-fiókkal megegyező régióban) | * | Bármelyik | 29876-29877 | TCP | Engedélyezés |
| Felhasználók forrás IP-címei a számítási csomópontok és/vagy egy számítási csomópont alhálózatának távoli eléréséhez a Linux többpéldányos feladatai esetében, amennyiben szükséges. | N/A | * | Bármelyik | 3389 (Windows), 22 (Linux) | TCP | Engedélyezés |

> [!WARNING]
> A Batch szolgáltatás IP-címei idővel módosulhatnak. Ezért kifejezetten ajánlott az NSG-szabályokhoz a `BatchNodeManagement` szolgáltatáscímke (vagy helyi változatának) használata. Nem ajánlott az NSG-szabályok feltöltése közvetlenül a Batch szolgáltatás IP-címeivel.

**Kimenő biztonsági szabályok**

| Forrás | Forrásportok | Cél | Cél szolgáltatáscímkéje | Célportok | Protokoll | Műveletek |
| --- | --- | --- | --- | --- | --- | --- |
| Bármelyik | * | [Szolgáltatáscímke](../articles/virtual-network/security-overview.md#service-tags) | `Storage` (regionális változat használata esetén, a Batch-fiókkal megegyező régióban) | 443 | TCP | Engedélyezés |

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

Windows rendszer esetében csak akkor konfigurálja a bejövő forgalmat a 3389-es porton keresztül, ha engedélyeznie kell az RDP-hozzáférést a készletezett csomópontokhoz. Ez nem szükséges a készletezett csomópontok használhatóságához.

**Bejövő biztonsági szabályok**

| Forrás IP-címek | Forrásportok | Cél | Célportok | Protokoll | Műveletek |
| --- | --- | --- | --- | --- | --- |
Bármelyik <br /><br />Bár ehhez tulajdonképpen „az összes engedélyezése” engedély szükséges, a Batch szolgáltatás minden egyes csomópont szintjén alkalmaz egy ACL-szabályt, amely kiszűri az összes olyan IP-címet, amely nem a Batch szolgáltatáshoz tartozik. | * | Bármelyik | 10100, 20100, 30100 | TCP | Engedélyezés |
| Nem kötelező, a számítási csomópontokhoz való RDP-hozzáférés engedélyezésére szolgál. | * | Bármelyik | 3389 | TCP | Engedélyezés |

**Kimenő biztonsági szabályok**

| Forrás | Forrásportok | Cél | Célportok | Protokoll | Műveletek |
| --- | --- | --- | --- | --- | --- |
| Bármelyik | * | Bármelyik | 443  | Bármelyik | Engedélyezés |
