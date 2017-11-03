---
title: "Resource Manager és klasszikus telepítési |} Microsoft Docs"
description: "A Resource Manager üzembe helyezési modellben és a klasszikus közötti különbségeket mutatja (vagy szolgáltatásfelügyelet) üzembe helyezési modellben."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 7ae0ffa3-c8da-4151-bdcc-8f4f69290fb4
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/09/2017
ms.author: tomfitz
ms.openlocfilehash: 060680fd4a7ce6e0cde406cc4a8f6f3a21d3c588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>Az Azure Resource Manager és klasszikus üzembe helyezési: üzembe helyezési modellek és az erőforrások állapota
Ebben a témakörben megismerkedhet a Azure Resource Manager és klasszikus üzembe helyezési modellel, az erőforrások állapotát, és ezért az erőforrások közül egy vagy egyéb üzembe helyezése. A Resource Manager és klasszikus üzembe helyezési modellek határoz meg két különböző módokat telepítése és kezelése az Azure megoldások. Különböző API kétféle módon működik velük, és a telepített erőforrások tartalmazhat fontos különbség. A két modell nem teljesen kompatibilis egymással. Ez a témakör ismerteti azokat a különbségeket.

Egyszerűbbé teheti a telepítése és az erőforrások kezelése, a Microsoft javasolja, hogy Resource Manager az összes új erőforrás. Ha lehetséges a Microsoft azt javasolja, hogy a meglévő erőforrásokat Resource Manageren keresztül telepíteni.

Ha most ismerkedik az erőforrás-kezelő, érdemes lehet olvassa el a meghatározott terminológia a [Azure Resource Manager áttekintése](resource-group-overview.md).

## <a name="history-of-the-deployment-models"></a>Az üzembe helyezési modellekről előzményei
Azure eredetileg csak a klasszikus üzembe helyezési modell tartalmaz. Ebben a modellben minden erőforrás már létezett a egymástól függetlenül; nem lehetett kapcsolódó erőforrások csoportosítása. Ehelyett kellett manuálisan nyomon követése a megoldás vagy az alkalmazáshoz készült erőforrásokat, és ne felejtse el összehangolt kezelheti őket. A megoldás telepítéséhez kellett létrehozni az egyes erőforrások egyenként a klasszikus portálon keresztül, vagy hozzon létre olyan parancsfájlt, amely a megfelelő sorrendben az erőforrások telepítése. Megoldás törlése, az egyes erőforrások törlése külön-külön kellett. Akkor lehetett könnyen nem vonatkoznak, és kapcsolódó erőforrások hozzáférés-vezérlési házirendek frissítése. Végül, akkor nem alkalmazható a következő címkék a erőforrásokhoz, hogy lássa el a feltételeket, amelyek segítenek az erőforrások figyelése és kezelése.

A 2014-re az Azure erőforrás-kezelő, amely hozzá, az erőforráscsoport fogalmát vezette be. Erőforráscsoport egy olyan tároló, amelyek egy közös életciklussal erőforrások. A Resource Manager üzembe helyezési modellel számos előnyt kínál:

* Ezt központi telepítése, kezelése, és figyelheti a megoldás a szolgáltatások csoportosan helyett külön-külön szolgáltatások ezek kezelése.
* Ismételten a teljes életciklus megoldás üzembe helyezése, és lehet abban, hogy az erőforrások telepítése konzisztens lesz.
* Az erőforráscsoportban összes erőforrásokhoz való hozzáférés-vezérlés is alkalmazhat, és ezek a házirendek automatikusan alkalmazza, amikor új erőforrásokat ad az erőforráscsoportot.
* Címkékkel láthatja el az erőforrásokat, így logikusan rendszerezhető az előfizetés összes erőforrása.
* JavaScript Object Notation (JSON) segítségével határozza meg az infrastruktúra megoldást. A JSON-fájlt egy Resource Manager-sablon néven ismert.
* Meghatározhatja az erőforrások közti függőségeket, hogy azok a megfelelő sorrendben legyenek telepítve.

Erőforrás-kezelő hozzáadásakor, alapértelmezett erőforráscsoportok visszamenőleges hozzáadott összes erőforrást. Most a klasszikus üzembe helyezési keresztül erőforrás hoz létre, ha az erőforrás automatikusan létrejön, hogy a szolgáltatás alapértelmezett erőforráscsoporton belül, annak ellenére, hogy nem adta meg a központi telepítés erőforráscsoporthoz. Azonban csak meglévő erőforráscsoporton belül nem jelenti azt, hogy az erőforrás a Resource Manager modellt lett konvertálva. Megnézzük, hogyan minden egyes szolgáltatás kezeli a két üzembe helyezési modellel, a következő szakaszban. 

## <a name="understand-support-for-the-models"></a>A modellek támogatása ismertetése
Amikor arról dönt, hogy mely erőforrások használandó telepítési modell, nincsenek tisztában lenni a három forgatókönyv:

1. A szolgáltatás támogatja az erőforrás-kezelő, és csak egyetlen típusra biztosítja.
2. A szolgáltatás támogatja az erőforrás-kezelő, de két típus - biztosít egy Resource Manager és klasszikus. Ez a forgatókönyv csak a virtuális hálózatok, virtuális gépek és tárfiókok vonatkozik.
3. A szolgáltatás nem támogatja az erőforrás-kezelő.

Annak megállapításához, hogy a szolgáltatás támogatja-e az erőforrás-kezelő, lásd: [erőforrás-szolgáltatók és típusok](resource-manager-supported-services.md).

Ha szeretné használni a szolgáltatás nem támogatja az erőforrás-kezelő, a klasszikus üzembe helyezési használatával kell folytatja.

Ha a szolgáltatás támogatja az erőforrás-kezelő és **nem** egy virtuális gépet, a tárfiókhoz vagy a virtuális hálózati erőforrás-kezelő bármely komplikációk nélkül is használható.

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

## <a name="resource-manager-characteristics"></a>Erőforrás-kezelő jellemzői
Megismerheti a két modell, tekintsük át, az erőforrás-kezelő típusok jellemzői:

* Létre a [Azure-portálon](https://portal.azure.com/).
  
     ![Azure Portal](./media/resource-manager-deployment-model/portal.png)
  
     A számítási, tárolási és hálózati erőforrások lehetősége van az erőforrás-kezelő vagy a klasszikus telepítési használatával. Válassza ki **erőforrás-kezelő**.
  
     ![Erőforrás-kezelő telepítése](./media/resource-manager-deployment-model/select-resource-manager.png)
* Az Azure PowerShell-parancsmagok a Resource Manager verziójával létrehozott. Ezek a parancsok a formátumuk *ige-AzureRmNoun*.

  ```powershell
  New-AzureRmResourceGroupDeployment
  ```

* Létre a [Azure Resource Manager REST API](https://docs.microsoft.com/rest/api/resources/) REST-műveletek.
* Futtassa Azure parancssori felület parancsait létre a **arm** mód.
  
  ```azurecli
  azure config mode arm
  azure group deployment create
  ```

* Az erőforrástípus nem tartalmaz **(klasszikus)** nevében. A következő kép bemutatja a típus **tárfiók**.
  
    ![webalkalmazásra](./media/resource-manager-deployment-model/resource-manager-type.png)

## <a name="classic-deployment-characteristics"></a>Klasszikus üzembe helyezési jellemzői
A klasszikus üzembe helyezési modellel is előfordulhat, hogy tudja, a szolgáltatás felügyeleti modell.

A klasszikus üzembe helyezési modellel létrehozott erőforrások rendelkezik a következő jellemzőkkel:

* Létre a [klasszikus portál](https://manage.windowsazure.com)
  
     ![klasszikus portál](./media/resource-manager-deployment-model/classic-portal.png)
  
     Az Azure-portálon, és megadhat **klasszikus** központi telepítését (számítási, tárolási és hálózati).
  
     ![Klasszikus üzembe helyezési](./media/resource-manager-deployment-model/select-classic.png)
* A Service Management verzióját az Azure PowerShell-parancsmagok használatával létre. A parancs nevek formátumuk *ige-AzureNoun*.

  ```powershell
  New-AzureVM
  ```

* Létre a [szolgáltatásfelügyelet REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx) REST-műveletek.
* Futtassa Azure parancssori felület parancsait létre **asm** mód.

  ```azurecli
  azure config mode asm
  azure vm create
  ```
   
* Az erőforrástípus tartalmaz **(klasszikus)** nevében. A következő kép bemutatja a típus **(klasszikus) tárfiókot**.
  
    ![klasszikus típusa](./media/resource-manager-deployment-model/classic-type.png)

Az Azure portál segítségével kezelheti az erőforrásokat, amelyek klasszikus üzembe helyezési keresztül lettek létrehozva.

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
| Virtuális IP-cím |Cloud Services egy alapértelmezett VIP-t (virtuális IP-cím) jelenik meg, ha egy virtuális Gépet hozzáadnak egy felhőalapú szolgáltatás. A Virtuális IP-cím az implicit terheléselosztóhoz társított cím. |A nyilvános IP-cím egy Microsoft.Network szolgáltató által közzétett erőforrás. Egy nyilvános IP-cím lehet Statikus (Fenntartott) vagy Dinamikus. A dinamikus nyilvános IP-címek hozzárendelhetők egy terheléselosztóhoz. A nyilvános IP-címek védelme biztonsági csoportok segítségével biztosítható. |
| Fenntartott IP-címek |Az Azure-ban fenntarthat egy IP-címet, és társíthatja egy felhőszolgáltatáshoz, hogy biztosítsa az IP-cím állandóságát. |A nyilvános IP-címek létrehozhatók „Statikus” módban, amely ugyanazokat a képességeket biztosítja, mint a „Fenntartott IP-cím”. A statikus nyilvános IP-címek jelenleg csak terheléselosztóhoz rendelhetők hozzá. |
| Virtuális gépenként megadott nyilvános IP-cím (PIP) |Nyilvános IP-címeket is hozzárendelhet egy virtuális géphez közvetlenül. |A nyilvános IP-cím egy Microsoft.Network szolgáltató által közzétett erőforrás. Egy nyilvános IP-cím lehet Statikus (Fenntartott) vagy Dinamikus. Jelenleg azonban csak dinamikus nyilvános IP-címek rendelhetők hozzá hálózati adapterekhez, hogy virtuális gépenként legyen meghatározva egy nyilvános IP-cím. |
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
**Létrehozhat egy virtuális gép Azure Resource Manager használatával történő telepítéséhez a klasszikus üzembe helyezési használatával létrehozott virtuális hálózatban?**

Ez nem támogatott. Azure Resource Manager használatával nem virtuális gép telepítése a klasszikus üzembe helyezési használatával létrehozott virtuális hálózatba.

**Létrehozhat egy virtuális gép az Azure Resource Manager az Azure szolgáltatásfelügyeleti API használatával létrehozott felhasználói rendszerképből?**

Ez nem támogatott. Azonban a VHD-fájlok másolása a szolgáltatásfelügyeleti API használatával létrehozott tárfiók, és adja hozzá egy új fiókot, Azure Resource Manageren keresztül.

**Mi az az előfizetéshez tartozó kvóta gyakorolt hatás?**

A virtuális gépek, virtuális hálózatok és az Azure Resource Manager használatával létrehozott storage-fiókok kvótái nem azonosak a más kvótákat. Minden előfizetés lekérdezi a kvótákat létrehozni az új API-kkal erőforrásokat. A további kvótákról [itt](../azure-subscription-service-limits.md) talál részletes információkat.

**Használhatom továbbra is az automatizált parancsfájljaimat virtuális gépek, a virtuális hálózatok és a storage-fiókok a Resource Manager API-k segítségével történő üzembe helyezéséhez?**

Az automatizálás és a már létrehozott parancsfájlokat is a meglévő virtuális gépek, az Azure szolgáltatásfelügyelet módban létrehozott virtuális hálózatokat működik. Azonban a parancsfájlok létrehozásához a Resource Manager módra ugyanazon erőforrásoknak az új séma használatához frissítenie kell rendelkeznie.

**Hol találhatok példákat az Azure Resource Manager-sablonok?**

Széles választékát kezdősablont található [Azure Resource Manager gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/).

## <a name="next-steps"></a>Következő lépések
* A sablont, amely meghatározza egy virtuális gépet, a tárfiók és a virtuális hálózat létrehozása bízná, lásd: [Resource Manager sablonokhoz](resource-manager-template-walkthrough.md).
* A parancsok a sablonok telepítésével kapcsolatos információk: [Azure Resource Manager-sablon az alkalmazás központi telepítését](resource-group-template-deploy.md).

