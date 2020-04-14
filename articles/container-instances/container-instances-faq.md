---
title: Gyakori kérdések
description: Válaszok az Azure Container Instances szolgáltatással kapcsolatos gyakori kérdésekre
author: dkkapur
ms.topic: article
ms.date: 04/10/2020
ms.openlocfilehash: 8730dcb24af61730d7f93ea37a53cf87435eb9f9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261618"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Gyakori kérdések az Azure Container-példányokkal kapcsolatban

Ez a cikk az Azure Container Instances használatával kapcsolatos gyakori kérdéseket ismerteti.

## <a name="deployment"></a>Üzembe helyezés

### <a name="how-large-can-my-container-image-be"></a>Mekkora lehet a tárolólemezképem?

Az Azure Container Instances üzembe helyezhető tárolórendszerképének maximális mérete 15 GB. Előfordulhat, hogy a telepítés pillanatában rendelkezésre álló pontos rendelkezésre állástól függően nagyobb lemezképeket is üzembe helyezhet, de ez nem garantált.

A tárolórendszerkép mérete hatással van a telepítéshez szükséges ideig, ezért általában a tárolórendszerképeket a lehető legkisebb rekedésben szeretné tartani.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Hogyan gyorsíthatom fel a tárolóüzembe helyezését?

Mivel az üzembe helyezési idők egyik fő meghatározótényezője a képméret, keresse meg a méret csökkentésének módjait. Távolítsa el azolyan rétegeket, amelyekre nincs szüksége, vagy csökkentse a rétegméretét a képen (egy világosabb alap operációsrendszer-lemezkép kiválasztásával). Ha például Linux-tárolókat futtat, fontolja meg az Alpine alaplemezképként való használatát, nem pedig egy teljes Ubuntu Servert. Hasonlóképpen, a Windows-tárolók, használja a Nano Server alaplemezképet, ha lehetséges. 

Tekintse meg az előre gyorsítótárazott lemezképek listáját az Azure Container Images, a [lista gyorsítótárazott képek](/rest/api/container-instances/listcachedimages) API-n keresztül érhető el. Előfordulhat, hogy az egyik előre gyorsítótárazott képhez át tud váltani egy képréteget. 

Tekintse meg a tárolóindítási idő csökkentésére [vonatkozó részletesebb útmutatást.](container-instances-troubleshooting.md#container-takes-a-long-time-to-start)

### <a name="what-windows-base-os-images-are-supported"></a>Milyen Windows-alapoperációs rendszerű rendszerképek támogatottak?

#### <a name="windows-server-2016-base-images"></a>Windows Server 2016 alaplemezképek

* [Nano](https://hub.docker.com/_/microsoft-windows-nanoserver)szerver `10.0.14393.x`: ,`sac2016`
* [Windows Server](https://hub.docker.com/_/microsoft-windows-servercore) `ltsc2016`Core : ,`10.0.14393.x`

> [!NOTE]
> Az 1709-es vagy 1803-as féléves csatornakiadáson alapuló Windows-lemezképek nem támogatottak.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 és ügyfélalap-lemezképek (előzetes verzió)

* [Nano](https://hub.docker.com/_/microsoft-windows-nanoserver)Server `1809` `10.0.17763.914` : , vagy korábban
* [Windows Server](https://hub.docker.com/_/microsoft-windows-servercore) `ltsc2019`Core `1809` `10.0.17763.914` : , vagy korábbi
* [Windows](https://hub.docker.com/_/microsoft-windows) `1809`: `10.0.17763.914` , vagy korábbi

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Milyen .NET vagy .NET Core lemezképréteget használjak a tárolóban? 

Használja a legkisebb képet, amely megfelel az Ön igényeinek. Linux esetén használhat egy *runtime-alpine* .NET Core lemezképet, amelyet a .NET Core 2.1 megjelenése óta támogatott. Windows esetén, ha a teljes . *4.7.2-windowsservercore-ltsc2016* A csak futásidejű lemezképek kisebbek, de nem támogatják a .NET SDK-t igénylő számítási feladatokat.

## <a name="availability-and-quotas"></a>Elérhetőség és kvóták

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Hány magot és memóriát kell lefoglalni a tárolók vagy a tárolócsoport számára?

Ez valóban a munkaterheléstől függ. Indítsa el a kis-és tesztelje a teljesítményt, hogy lássa, hogyan csinálják a tárolók. [Figyelje a PROCESSZOR- és memóriaerőforrás-használatot,](container-instances-monitor.md)majd adja hozzá a magokat vagy a memóriát a tárolóban üzembe helyezett folyamatok tól függően. 

Győződjön meg arról is, hogy ellenőrizze az [erőforrás-elérhetőséget](container-instances-region-availability.md#availability---general) a régióban üzembe helyezett a processzormagok és a tárolócsoportonként rendelkezésre álló memória felső határait. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Milyen mögöttes infrastruktúrán fut az ACI?

Az Azure Container Instances célja, hogy egy kiszolgáló nélküli tárolók igény szerinti szolgáltatás, ezért azt szeretnénk, hogy összpontosítson a tárolók fejlesztésére, és ne aggódjon az infrastruktúra miatt! Azok számára, akik kíváncsiak, vagy szeretnének összehasonlítást végezni a teljesítmény, Az ACI fut azure-os virtuális gépek készletein különböző SK-k, elsősorban az F és a D sorozat. Arra számítunk, hogy ez a változás a jövőben, mint mi továbbra is fejleszteni és optimalizálni a szolgáltatást. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Azt akarom, hogy telepíteni ezer magok ACI - kaphatok a kvótát nőtt?
 
Igen (néha). Tekintse meg a [kvóták és korlátok](container-instances-quotas.md) cikket az aktuális kvóták, és amely korlátok at lehet növelni kérésre.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Telepíthetek több mint 4 maggal és 16 GB RAM-mal?

Jelenleg nem. Jelenleg ezek a tárolócsoport-csoportok maximális értékei. Lépjen kapcsolatba az Azure-támogatási szolgálattal meghatározott követelményekkel vagy kérésekkel. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Mikor lesz az ACI egy adott régióban?

A régió jelenlegi elérhetősége [itt](container-instances-region-availability.md#availability---general)jelenik meg. Ha egy adott régióra vonatkozó követelménye van, forduljon az Azure-támogatáshoz.

## <a name="features-and-scenarios"></a>Jellemzők és forgatókönyvek

### <a name="how-do-i-scale-a-container-group"></a>Hogyan méretezhetek egy tárolócsoportot?

Jelenleg a méretezés nem érhető el a tárolók vagy tárolócsoportok. Ha több példányt kell futtatnia, az API-val automatizálhatja és további kérelmeket hozhat létre a tárolócsoport-létrehozáshoz a szolgáltatáshoz. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Milyen szolgáltatások érhetők el az egyéni virtuális hálózaton futó példányok számára?

A tárolócsoportokat egy Ön által kiválasztott [Azure virtuális hálózatban helyezheti üzembe,](container-instances-vnet.md) és delegálhatja a tárolócsoportok nak a tárolócsoportoknak a virtuális hálózaton belüli forgalmat az Azure-erőforrások között. Egy tárolócsoport virtuális hálózatba való üzembe helyezése jelenleg elérhető az Azure-régiók egy részhalmazában lévő éles számítási feladatokhoz.

## <a name="pricing"></a>Díjszabás

### <a name="when-does-the-meter-start-running"></a>Mikor kezd el futni a mérő?

A tárolócsoport időtartama attól az időponttól számítja ki, amikor elkezdjük lekérni az első tároló rendszerképét (egy új központi telepítéshez), vagy a tárolócsoport újraindul (ha már üzembe helyezett), amíg a tárolócsoport le nem áll. A [container instances díjszabása](https://azure.microsoft.com/pricing/details/container-instances/)részletesen ismerteti a részleteket.

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Abbahagyom a töltést, amikor a tárolók leállnak?

A mérőórák leállnak, ha a teljes tárolócsoport leállt. Mindaddig, amíg egy tároló a tárolócsoportban fut, az erőforrásokat abban az esetben tároljuk, ha újra el szeretné indítani a tárolókat. 

## <a name="next-steps"></a>További lépések

* [További információ](container-instances-overview.md) az Azure Container Instances.
* Az Azure Container Instances [gyakori problémáinak elhárítása.](container-instances-troubleshooting.md)