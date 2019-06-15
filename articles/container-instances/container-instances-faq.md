---
title: Az Azure Container Instances – gyakori kérdések
description: Az Azure Container Instances szolgáltatással kapcsolatos gyakori kérdésekre vonatkozó válaszokat
services: container-instances
author: dkkapur
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 4/25/2019
ms.author: dekapur
ms.openlocfilehash: 99882bd23d7b94afc550247172e5b70deb23bec9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65791401"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Azure Container Instances kapcsolatos gyakori kérdések

Ez a cikk foglalkozik az Azure Container Instances szolgáltatásban – gyakori kérdések.

## <a name="deployment"></a>Környezet

### <a name="how-large-can-my-container-image-be"></a>Milyen méretű saját tárolórendszerkép lehet?

Az Azure Container Instancesben üzembe helyezhető tárolórendszerképet maximális mérete 15 GB alá. Fogja tudni telepíteni a nagyobb lemezképek függően pontos jelenleg telepít, de ez nem garantált.

A tároló rendszerképének mérete befolyásolja, mennyi ideig tart telepítéséhez, ezért általában meg szeretné tartani a tárolólemezképek legyen a lehető legkisebb.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Hogyan gyorsíthatom fel saját tároló telepítését?

Mivel az üzembe helyezéshez szükséges idő a fő tényezők közül a rendszerkép méretétől, keresse meg a méretének csökkentésére. Távolítsa el a rétegek nem kell, vagy csökkentse a méretét, a kép rétegek (kiválasztásával a feladatorientált felhasználók által használt alap operációsrendszer-lemezkép). Például ha Linux-tárolók rendszert használ, érdemes Alpine egy teljes körű Ubuntu Server, hanem az alaplemezkép. Hasonlóképpen Windows-tárolókhoz, használatával alapszintű Nano Server-rendszerképet, ha lehetséges. 

Azt is ellenőrizze a lista előre összeállított rendszerképeit Azure Tárolórendszerképek keresztül elérhető a [gyorsítótárazott rendszerképek felsorolása](/rest/api/container-instances/listcachedimages) API-t. Előfordulhat, hogy ki az egyik előre összeállított képet egy kép réteg váltani. 

Még több [részletes útmutatót](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) a tároló indítási idejének csökkentését.

### <a name="what-windows-base-os-images-are-supported"></a>Milyen Windows alap operációsrendszer-lemezképek támogatottak?

#### <a name="windows-server-2016-base-images"></a>A Windows Server 2016 alaplemezképek

* [A nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `10.0.14393.x`, `sac2016`
* [A Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`,  `10.0.14393.x`

> [!NOTE]
> Féléves csatorna kiadási 1709-es vagy 1803-alapú Windows-rendszerképeket nem támogatottak.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>A Windows Server 2019 és az ügyfél alaplemezképek (előzetes verzió)

* [A nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809`, `10.0.17763.x`
* [A Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019`, `1809`, `10.0.17763.x`
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809`, `10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Milyen .NET vagy a .NET Core kép réteget kell használni a saját tároló? 

Használja a legkisebb lemezképet, amely megfelel a követelményeknek. Linux esetén használhat egy *futásidejű alpine* .NET Core-rendszerképhez, amelyet a .NET Core 2.1-es kiadás óta támogatott. A Windows, ha a teljes .NET-keretrendszert használ majd kell a Windows Server Core-rendszerkép használata (csak futásidejű kép, mint például *4.7.2-windowsservercore-ltsc2016*). Csak futásidejű rendszerképek mérete kisebb, de nem támogatja a .NET SDK-t megkövetelő számítási feladatokhoz.

## <a name="availability-and-quotas"></a>Rendelkezésre állási és kvóták

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Hány magok és a memória kell I lefoglalni a tárolók vagy a tárolócsoport?

Ez valójában a munkaterheléstől függ. Kezdhetik, és tesztelje a teljesítményt, tekintse meg, hogyan hajtsa végre a tárolókat. [Processzor- és erőforrás-használat figyelése](container-instances-monitor.md), majd adja hozzá a magok vagy a memória, a folyamatokat, amelyek végzi az üzembe helyezést a tároló típusa alapján. 

Győződjön meg arról is, hogy ellenőrizze a [erőforrás rendelkezésre állási](container-instances-region-availability.md#availability---general) a régió, a CPU-magok és a egy tárolócsoport rendelkezésre álló memória felső korlátja a telepít. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Milyen alapul szolgáló infrastruktúra fut az ACI?

Az Azure Container Instances az a célja, hogy egy kiszolgáló nélküli tárolók igény szerinti szolgáltatás lehet, hogy azt szeretné, hogy, amely kell a tárolókat, és nem kell foglalkoznia infrastruktúra! Azok számára, amely kíváncsi rá, vagy wanting összehasonlításokhoz a teljesítményét, ACI futtatja meg az Azure-beli virtuális gépek különböző SKU-k, az elsősorban az F és a D sorozat. Ezt később módosíthatja, mivel folyamatosan fejlesztik, és optimalizálhatja a szolgáltatás várhatóan. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Ezer mag az aci-ban telepíteni kívánt - szerezhető be saját kvótájának növeléséért?
 
Igen (néha). Tekintse meg a [kvótái és korlátai](container-instances-quotas.md) cikk aktuális kvótái és a kérés mely korlátot lehet növelni.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Telepíthetek több mint 4 magos, 16 GB RAM rendelkező?

még nem. Jelenleg ezek a egy tárolócsoport megtervezésekor. Különleges követelmények vagy kérelmek forduljon az Azure ügyfélszolgálatához. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Mikor lesz ACI egy adott régióban?

Aktuális régiók rendelkezésre állása közzé van téve [Itt](container-instances-region-availability.md#availability---general). Ha egy adott régióhoz követelmény, forduljon az Azure ügyfélszolgálatához.

## <a name="features-and-scenarios"></a>Funkciók és alkalmazási helyzetek

### <a name="how-do-i-scale-a-container-group"></a>Hogyan méretezhetek egy tárolócsoportot?

Skálázás jelenleg nem érhető el a tárolók vagy tárolócsoportok. További példányok futtatásához szükséges, ha az API segítségével automatizálhatja és a tároló csoport létrehozásakor a szolgáltatás további kérések. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Milyen funkciók érhetők el, egy egyéni virtuális hálózaton futó példányok?

Tárolócsoportok az Azure-beli virtuális hálózathoz a választott központi telepítése, és delegálja a tárolócsoportok irányíthatja a forgalmat a Vneten belül az Azure-erőforrások közötti privát IP-címek. Egy virtuális hálózatban egy tárolócsoport üzembe helyezési szolgáltatás jelenleg előzetes verzióban, és ez a szolgáltatás néhány szempontja változhat általánosan (elérhetővé tétel GA) előtt. Lásd: [előzetes verzió korlátozásai](container-instances-vnet.md#preview-limitations) a frissített adatokat.

## <a name="pricing"></a>Díjszabás

### <a name="when-does-the-meter-start-running"></a>Ha nem az fogyasztásmérő kezdjen programokat futtatni?

Tárolócsoportok működési időtartama kezdődik meg, kérje le az első tároló rendszerképét (az új üzembe helyezési modell) vagy a tárolócsoport újraindítása (Ha már telepítve vannak), a tárolócsoport leállításáig idő kiszámítása. Részletek: [Container Instances díjszabását](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Állítsam le a díját számítjuk fel, ha a tárolók le vannak állítva?

Az érték a teljes tárolócsoport leállítását követően állni. Mindaddig, amíg egy szereplő a tároló fut-e, hogy tartsa az erőforrásokat szeretne újból elindítani a tárolók. 

## <a name="next-steps"></a>További lépések

* [További](container-instances-overview.md) Azure Container Instances.
* [Gyakori problémák megoldása](container-instances-troubleshooting.md) az Azure Container Instances szolgáltatásban.