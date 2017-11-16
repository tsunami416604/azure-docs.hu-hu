---
title: "Resource Manager és klasszikus telepítési |} Microsoft Docs"
description: "A Resource Manager üzembe helyezési modellben és a klasszikus közötti különbségeket mutatja (vagy szolgáltatásfelügyelet) üzembe helyezési modellben."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: tomfitz
ms.openlocfilehash: 2144e3527b44e3cf508d23fedf7abb4cda595bbf
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>Az Azure Resource Manager és klasszikus üzembe helyezési: üzembe helyezési modellek és az erőforrások állapota
Ebből a cikkből megismerheti, Azure Resource Manager és klasszikus üzembe helyezési modellek. A Resource Manager és klasszikus üzembe helyezési modellek határoz meg két különböző módokat telepítése és kezelése az Azure megoldások. Különböző API kétféle módon működik velük, és a telepített erőforrások tartalmazhat fontos különbség. A két modell nem kompatibilisek egymással. Ez a cikk ismerteti azokat a különbségeket.

Egyszerűbbé teheti a telepítése és az erőforrások kezelése, a Microsoft javasolja, hogy Resource Manager az összes új erőforrás. Ha lehetséges a Microsoft azt javasolja, hogy a meglévő erőforrásokat Resource Manageren keresztül telepíteni.

Ha most ismerkedik az erőforrás-kezelő, érdemes lehet olvassa el a meghatározott terminológia a [Azure Resource Manager áttekintése](resource-group-overview.md).

## <a name="history-of-the-deployment-models"></a>Az üzembe helyezési modellekről előzményei
Azure eredetileg csak a klasszikus üzembe helyezési modell tartalmaz. Ebben a modellben minden erőforrás már létezett a egymástól függetlenül; nem lehetett kapcsolódó erőforrások csoportosítása. Ehelyett kellett manuálisan nyomon követése a megoldás vagy az alkalmazáshoz készült erőforrásokat, és ne felejtse el összehangolt kezelheti őket. A megoldás telepítéséhez kellett létrehozni az egyes erőforrások egyenként a portálon keresztül, vagy hozzon létre olyan parancsfájlt, amely a megfelelő sorrendben az erőforrások telepítése. Megoldás törlése, az egyes erőforrások törlése külön-külön kellett. Akkor lehetett könnyen nem vonatkoznak, és kapcsolódó erőforrások hozzáférés-vezérlési házirendek frissítése. Végül, akkor nem alkalmazható a következő címkék a erőforrásokhoz, hogy lássa el a feltételeket, amelyek segítenek az erőforrások figyelése és kezelése.

A 2014-re az Azure erőforrás-kezelő, amely hozzá, az erőforráscsoport fogalmát vezette be. Erőforráscsoport egy olyan tároló, amelyek egy közös életciklussal erőforrások. A Resource Manager üzembe helyezési modellel számos előnyt kínál:

* Ezt központi telepítése, kezelése, és figyelheti a megoldás a szolgáltatások csoportosan helyett külön-külön szolgáltatások ezek kezelése.
* Ismételten a teljes életciklus megoldás üzembe helyezése, és lehet abban, hogy az erőforrások telepítése konzisztens lesz.
* Az erőforráscsoportban összes erőforrásokhoz való hozzáférés-vezérlés is alkalmazhat, és ezek a házirendek automatikusan alkalmazza, amikor új erőforrásokat ad az erőforráscsoportot.
* Címkékkel láthatja el az erőforrásokat, így logikusan rendszerezhető az előfizetés összes erőforrása.
* JavaScript Object Notation (JSON) segítségével határozza meg az infrastruktúra megoldást. A JSON-fájlt egy Resource Manager-sablon néven ismert.
* Meghatározhatja az erőforrások közti függőségeket, hogy azok a megfelelő sorrendben legyenek telepítve.

Erőforrás-kezelő hozzáadásakor, alapértelmezett erőforráscsoportok visszamenőleges hozzáadott összes erőforrást. Most a klasszikus üzembe helyezési keresztül erőforrás hoz létre, ha az erőforrás automatikusan létrejön, hogy a szolgáltatás alapértelmezett erőforráscsoporton belül, annak ellenére, hogy nem adta meg a központi telepítés erőforráscsoporthoz. Azonban csak meglévő erőforráscsoporton belül nem jelenti azt, hogy az erőforrás a Resource Manager modellt lett konvertálva.

## <a name="understand-support-for-the-models"></a>A modellek támogatása ismertetése
Nincsenek tisztában lenni a három forgatókönyv:

1. Cloud Services nem támogatja a Resource Manager üzembe helyezési modellben.
2. Virtuális hálózatok, virtuális gépek és tárfiókok támogatja a Resource Manager és a klasszikus üzembe helyezési modellek.
3. Minden más Azure-szolgáltatások támogatják az erőforrás-kezelő.

A virtuális gépek, a storage-fiókok és a virtuális hálózatok Ha az erőforrás hozták létre, klasszikus telepítési kell továbbra is működik rajta, klasszikus műveletek révén. Ha a virtuális gép, a tárfiók, vagy a virtuális hálózati erőforrás-kezelő központi jött létre, továbbra is kell erőforrás-kezelő műveletekkel. Ezt a különbséget zavaró kaphat, ha az előfizetése tartalmazza majd a Resource Manager és klasszikus üzembe helyezési létrejött erőforrásokat kombinációját. Ez a kombináció erőforrások váratlan eredményeket hozhat létre, mert a források nem támogatják a ugyanazokat a műveleteket.

Bizonyos esetekben egy erőforrás-kezelő parancs kérheti le a klasszikus üzembe helyezési létre erőforrásra vonatkozó adatokat, vagy egy hagyományos erőforrás áthelyezése egy másik erőforráscsoportban például felügyeleti feladatot hajthat végre. Azonban ezekben az esetekben a benyomást, hogy a típus támogatja-e az erőforrás-kezelő műveletek számára. Tegyük fel például, a klasszikus üzembe helyezési hoztak létre virtuális gépet tartalmazó erőforráscsoport. A következő erőforrás-kezelő PowerShell-parancs futtatásával:

```powershell
Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines
```

Azt jelzi, hogy a virtuális gépet:

```powershell
Name              : ExampleClassicVM
ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
ResourceName      : ExampleClassicVM
ResourceType      : Microsoft.ClassicCompute/virtualMachines
ResourceGroupName : ExampleGroup
Location          : westus
SubscriptionId    : {guid}
```

Azonban az erőforrás-kezelő parancsmag **Get-AzureRmVM** csak a Resource Manager használatával telepített virtuális gépek adja vissza. A következő parancs nem ad vissza a klasszikus üzembe helyezési használatával létrehozott virtuális gépek.

```powershell
Get-AzureRmVM -ResourceGroupName ExampleGroup
```

Csak erőforrások erőforrás-kezelő támogatási címkék segítségével létrehozott. Hagyományos erőforrások nem címkékkel.

## <a name="changes-for-compute-network-and-storage"></a>A számítási, hálózati és tárolási változások
A következő diagram megjeleníti a számítási, hálózati és adattárolási erőforrásokat a Resource Manager használatával telepített.

![Erőforrás-kezelői architektúra](./media/resource-manager-deployment-model/arm_arch3.png)

Vegye figyelembe a következő, az erőforrások közötti kapcsolatokat:

* Az erőforrások erőforráscsoporton belül található.
* A virtuális gép egy adott tárfiókot a Storage erőforrás-szolgáltató a lemezek tárolni a blob storage (kötelező) definiált függ.
* A virtuális gép hivatkozik egy adott hálózati adapter a hálózati erőforrás-szolgáltató (kötelező) és a rendelkezésre állási készlet számítási erőforrás-szolgáltató az (nem kötelező).
* A hálózati Adaptert a virtuális gép hozzárendelt IP-cím (kötelező), az alhálózat a virtuális hálózat a virtuális gép (kötelező), és a hálózati biztonsági csoport (nem kötelező) hivatkozik.
* A virtuális hálózaton belül az alhálózat egy hálózati biztonsági csoportot (nem kötelező) hivatkozik.
* A betöltési terheléselosztó példány hivatkozik a háttérbeli IP-címkészletet, amely tartalmazza a hálózati Adaptert egy virtuális gép (nem kötelező), és hivatkozik arra a terhelés terheléselosztó nyilvános vagy privát IP-cím (nem kötelező).

Az alábbiakban az összetevőket, és azok a klasszikus üzembe helyezési:

![klasszikus architektúrája](./media/resource-manager-deployment-model/arm_arch1.png)

A klasszikus megoldást egy virtuális gépet szolgáltató tartalmazza:

* Egy szükséges felhőalapú szolgáltatás, amely a virtuális gépeket (számítást) tárolója. Virtuális gépek automatikusan kapnak egy hálózati kártya (NIC), és az Azure által kiosztott IP-címet. Ezenkívül a felhőalapú szolgáltatás tartalmazza-e a külső terheléselosztási terheléselosztó példány, egy nyilvános IP-cím és alapértelmezett végpontok, hogy a távoli asztali és a távoli PowerShell Windows-alapú virtuális gépek és a Secure Shell (SSH) forgalomnak a Linux-alapú virtuális gépek.
* Egy szükséges tárfiókot, amely a virtuális merevlemezek egy virtuális géphez, beleértve a rendszert, ideiglenes, és további adatlemezt (tárolás) tárolja.
* Egy nem kötelező, úgy működik, mint egy további tárolót, amelyben alhálózati struktúra létrehozása és kijelölése az alhálózaton, amelyre a virtuális gép is található virtuális hálózat (hálózati).

A következő táblázat ismerteti, hogyan működnek együtt a számítási, hálózati és Tárolóerőforrás-szolgáltatók változásai:

| Elem | Klasszikus | Resource Manager |
| --- | --- | --- |
| Felhőszolgáltatás a virtuális gépekhez |A Cloud Service egy tároló volt a virtuális gépekhez, amely platform és a terheléselosztás Rendelkezésre állását is igényelte. |Az új modell használatával a Cloud Service már nem szükséges objektum egy virtuális gép létrehozásához. |
| Virtuális hálózatok |Virtuális hálózat nem kötelező megadni a virtuális gép. Ha tartalmazza, a virtuális hálózat nem állítható rendszerbe a Resource Manager. |A virtuális gépeknek a Resource Manager központilag telepített virtuális hálózat. |
| Tárfiókok |A virtuális géphez, amely tárolja a virtuális merevlemezeket az operációs rendszer, ideiglenes, és további adatlemezt tárfiók szükséges. |A virtuális géphez szükséges egy tárfiókot, a lemezek a blob Storage tárolóban tárolni. |
| Rendelkezésre állási csoportok |A platform felé való rendelkezésre állást azonos „AvailabilitySetName” konfigurálásával lehetett jelezni a virtuális gépeken. A tartalék tartományok maximális száma 2 volt. |A Rendelkezésre állási csoport egy Microsoft.Compute szolgáltató által közzétett erőforrás. A nagy rendelkezésre állást igénylő virtuális gépeket szerepeltetni kell a Rendelkezésre állási csoportban. A tartalék tartományok maximális száma mostantól 3. |
| Affinitáscsoportok |Virtuális hálózatok létrehozásához szükség volt Affinitáscsoportokra. A regionális virtuális hálózatok bevezetésével erre már nem volt szükség. |Egyszerűbben fogalmazva az Azure Resource Manageren keresztül közzétett API-kban nem létezik az Affinitáscsoportok koncepciója. |
| Terheléselosztás |Egy felhőszolgáltatás létrehozása egy implicit terheléselosztót biztosít a telepített virtuális gépekhez. |A Load Balancer egy Microsoft.Network szolgáltató által közzétett erőforrás. A terheléselosztást igénylő virtuális gépek elsődleges hálózati adapterének hivatkoznia kell a terheléselosztóra. Egy terheléselosztó lehet külső vagy belső. A betöltési terheléselosztó példánya hivatkozik a háttérbeli IP-címkészletet, amely tartalmazza a hálózati Adaptert egy virtuális gép (nem kötelező), és hivatkozik arra a terhelés terheléselosztó nyilvános vagy privát IP-cím (nem kötelező). [További információk.](../virtual-network/resource-groups-networking.md) |
| Virtuális IP-cím |Cloud Services csomag lekérdezi egy alapértelmezett VIP-t (virtuális IP-cím), ha egy virtuális Gépet hozzáadnak egy felhőalapú szolgáltatás. A Virtuális IP-cím az implicit terheléselosztóhoz társított cím. |A nyilvános IP-cím egy Microsoft.Network szolgáltató által közzétett erőforrás. Nyilvános IP-cím lehet statikus (fenntartott) vagy dinamikus. Dinamikus nyilvános IP-címek is hozzárendelhető a terheléselosztóhoz. A nyilvános IP-címek védelme biztonsági csoportok segítségével biztosítható. |
| Fenntartott IP-címek |Az Azure-ban fenntarthat egy IP-címet, és társíthatja egy felhőszolgáltatáshoz, hogy biztosítsa az IP-cím állandóságát. |Nyilvános IP-cím statikus módban is létrehozható, és ez lehetőséget nyújt a azonos egy fenntartott IP-címként. |
| Virtuális gépenként megadott nyilvános IP-cím (PIP) |Nyilvános IP-címeket is hozzárendelhet egy virtuális géphez közvetlenül. |A nyilvános IP-cím egy Microsoft.Network szolgáltató által közzétett erőforrás. Nyilvános IP-cím lehet statikus (fenntartott) vagy dinamikus. |
| Végpontok |A virtuális gépen konfigurálni kell a bemeneti végpontokat, hogy bizonyos portok csatlakoztathatóvá váljanak. A virtuális gépekhez való csatlakozás egyik legelterjedtebb módja a bemeneti végpontok beállítása. |A bejövő NAT-szabályok konfigurálhatók a terheléselosztókon, így azonos képességek érhetők el a végpontok engedélyezésére adott portokon a virtuális gépekhez való csatlakozás céljából. |
| DNS-név |Egy felhőszolgáltatás egy implicit globálisan egyedi DNS-nevet kap. Például: `mycoffeeshop.cloudapp.net`. |A DNS-nevek opcionális paraméterek, amelyek egy nyilvános IP-cím erőforráson adhatók meg. Az FQDN-je a következő formátumban - `<domainlabel>.<region>.cloudapp.azure.com`. |
| Hálózati illesztők |Az elsődleges és másodlagos hálózati adapter és tulajdonságai egy virtuális gép hálózati konfigurációjaként voltak megadva. |A hálózati adapter egy Microsoft.Network szolgáltató által közzétett erőforrás. A hálózati adapter életciklusa nincs a virtuális géphez kötve. A virtuális gép hozzárendelt IP-cím (kötelező), az alhálózat a virtuális hálózat a virtuális gép (kötelező), és a hálózati biztonsági csoport (nem kötelező) hivatkozik. |

Virtuális hálózatok csatlakoztatása a különböző üzembe helyezési modellel kapcsolatos további tudnivalókért lásd: [csatlakoztatja a virtuális hálózatok a különböző üzembe helyezési modellel, a portál](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="migrate-from-classic-to-resource-manager"></a>Az erőforrás-kezelő a klasszikus áttelepítése
Ha készen áll az erőforrások erőforrás-kezelő telepítési klasszikus üzembe helyezési telepíthetők át, lásd:

1. [Műszaki részletes bemutatója a platform által támogatott áttelepítési a klasszikus Azure Resource Managerbe](../virtual-machines/windows/migration-classic-resource-manager-deep-dive.md)
2. [Támogatott platformon IaaS-erőforrásokra a klasszikus Azure Resource Manager áttelepítése](../virtual-machines/windows/migration-classic-resource-manager-overview.md)
3. [Telepítse át IaaS-erőforrásokra a klasszikus Azure Resource Manager Azure PowerShell használatával](../virtual-machines/windows/migration-classic-resource-manager-ps.md)
4. [Telepítse át IaaS-erőforrásokra a klasszikus Azure Resource Manager Azure parancssori felület használatával](../virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## <a name="frequently-asked-questions"></a>Gyakori kérdések
**Létrehozhat egy virtuális gép erőforrás-kezelő használatával történő telepítéséhez a klasszikus üzembe helyezési használatával létrehozott virtuális hálózatban?**

Ez a konfiguráció nem támogatott. Erőforrás-kezelő nem használható virtuális gép telepítése a klasszikus üzembe helyezési használatával létrehozott virtuális hálózatba.

**Hozhat létre a virtuális gépek erőforrás-kezelő egy klasszikus telepítési modellel létrehozott felhasználói rendszerképből?**

Ez a konfiguráció nem támogatott. Azonban a VHD-fájlok másolása egy tárfiókot a klasszikus üzembe helyezési modellel létrehozott, és adja hozzá egy új fiók létrehozása a Resource Manageren keresztül.

**Mi az az előfizetéshez tartozó kvóta gyakorolt hatás?**

A virtuális gépek, virtuális hálózatok és az Azure Resource Manager használatával létrehozott storage-fiókok kvótái nem azonosak a más kvótákat. Minden előfizetés lekérdezi a kvótákat létrehozni az új API-kkal erőforrásokat. A további kvótákról [itt](../azure-subscription-service-limits.md) talál részletes információkat.

**Használhatom továbbra is az automatizált parancsfájljaimat virtuális gépek, a virtuális hálózatok és a storage-fiókok a Resource Manager API-k segítségével történő üzembe helyezéséhez?**

Az automatizálás és a már létrehozott parancsfájlokat is a meglévő virtuális gépek, az Azure szolgáltatásfelügyelet módban létrehozott virtuális hálózatokat működik. Azonban a parancsfájlok létrehozásához a Resource Manager módra ugyanazon erőforrásoknak az új séma használatához frissítenie kell rendelkeznie.

**Hol találhatok példákat az Azure Resource Manager-sablonok?**

Széles választékát kezdősablont található [Azure Resource Manager gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/).

## <a name="next-steps"></a>Következő lépések
* A sablont, amely meghatározza egy virtuális gépet, a tárfiók és a virtuális hálózat létrehozása bízná, lásd: [Resource Manager sablonokhoz](resource-manager-template-walkthrough.md).
* A parancsok a sablonok telepítésével kapcsolatos információk: [Azure Resource Manager-sablon az alkalmazás központi telepítését](resource-group-template-deploy.md).

