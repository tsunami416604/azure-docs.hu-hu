---
title: Azure-beli virtuális gépek hálózati teljesítménye | Microsoft Docs
description: Tudnivalók az Azure-beli virtuális gépek hálózati teljesítményéről.
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: steveesp
ms.reviewer: kumud, mareat
ms.openlocfilehash: 47f58b25b082784177910d14ab95d8d242fda71a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355818"
---
# <a name="virtual-machine-network-bandwidth"></a>Virtuális gép hálózati sávszélessége

Az Azure különféle virtuálisgép-méreteket és-típusokat kínál, amelyek mindegyike különböző teljesítménybeli képességekkel rendelkezik. Az egyik lehetőség a hálózati átviteli sebesség (vagy a sávszélesség), a megabit/másodperc (Mbps) alapján mérve. Mivel a virtuális gépek megosztott hardveren futnak, a hálózati kapacitást az azonos hardvert használó virtuális gépek között méltányosan kell megosztani. A nagyobb méretű virtuális gépek viszonylag nagyobb sávszélességet foglalnak le a kisebb virtuális gépeknél.
 
Az egyes virtuális gépek számára lefoglalt hálózati sávszélesség mérése a virtuális gépről érkező kimenő forgalomra vonatkozik. A virtuális gépet elhagyó összes hálózati forgalom beleszámít a lefoglalt korlát felé, a célhelytől függetlenül. Ha például egy virtuális gépen 1 000 MB/s korlát van, akkor ez a korlát azt határozza meg, hogy a kimenő forgalom az azonos virtuális hálózatban lévő másik virtuális géphez, vagy az Azure-on kívülre van-e szánva.
 
A bejövő forgalom nem mérhető vagy nem korlátozódik közvetlenül. Vannak azonban más tényezők, például a processzor-és tárolási korlátok, ami hatással lehet a virtuális gépeknek a bejövő adatok feldolgozására.

A gyorsított hálózatkezelés egy olyan szolgáltatás, amely a hálózati teljesítmény javítására szolgál, beleértve a késést, az átviteli sebességet és a CPU-kihasználtságot. Míg a gyorsított hálózatkezelés javíthatja a virtuális gépek átviteli sebességét, így csak a virtuális gép lefoglalt sávszélességére képes. A gyorsított hálózatkezeléssel kapcsolatos további tudnivalókért tekintse meg a Windows vagy [Linux](create-vm-accelerated-networking-cli.md) [rendszerű](create-vm-accelerated-networking-powershell.md) virtuális gépek gyorsított hálózatkezelését ismertető témakört.
 
Az Azure Virtual Machines szolgáltatásnak rendelkeznie kell egy, de több hálózati adapterrel is. A virtuális géphez lefoglalt sávszélesség az összes kimenő forgalom összege a virtuális géphez csatlakoztatott összes hálózati adapteren. Más szóval a lefoglalt sávszélesség virtuális gépenként történik, függetlenül attól, hogy hány hálózati adapter van csatlakoztatva a virtuális géphez. Ha szeretné megtudni, hogy a különböző Azure-beli virtuálisgép-méretek hány hálózati adaptert támogatnak, tekintse meg az Azure Windows-és [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) - [alapú](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

## <a name="expected-network-throughput"></a>Várt hálózati teljesítmény

A várt kimenő átviteli sebesség és az egyes virtuálisgép-méretek által támogatott hálózati adapterek száma az Azure Windows-és [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) - [alapú](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek méretei szerint van részletezve. Válasszon egy típust, például az általános célú elemet, majd válassza ki az eredményül kapott lapon lévő méret sorozatot (például a Dv2 sorozatot). Minden adatsorozat tartalmaz egy táblázatot, amely a hálózati specifikációkat tartalmazza az utolsó oszlopban, amely a hálózati **adapterek/várt hálózati teljesítmény (MB/s)** . 

Az átviteli sebesség korlátja a virtuális gépre vonatkozik. A következő tényezők nem érintik az átviteli sebességet:
- **Hálózati adapterek száma**: a sávszélesség korlátja a virtuális gépről érkező összes kimenő forgalom összesített értéke.
- **Gyorsított hálózatkezelés**: bár a szolgáltatás hasznos lehet a közzétett korlát elérésében, nem változtatja meg a korlátot.
- **Forgalmi cél**: minden célhely a kimenő korlát felé mutat.
- **Protokoll**: az összes protokollon keresztüli kimenő forgalom a korlát irányába számít.

## <a name="network-flow-limits"></a>Hálózati forgalom korlátai

A sávszélességen kívül a virtuális gépen lévő hálózati kapcsolatok száma az adott időpontban hatással lehet a hálózati teljesítményére. Az Azure hálózati verem a "flows" nevű adatstruktúrákban megőrzi a TCP/UDP-kapcsolat minden irányának állapotát. Egy tipikus TCP/UDP-kapcsolatok 2 folyamatot hoznak létre, egyet a bejövő és egy másikat a kimenő irányhoz. 

A végpontok közötti adatátvitel több folyamat létrehozását igényli az adatátvitelt végzők mellett. Néhány példa a DNS-feloldáshoz és a terheléselosztó Health-mintavételekhez létrehozott folyamatok számára létrehozott folyamatokra. Azt is vegye figyelembe, hogy a hálózati virtuális készülékek (NVA-EK), például az átjárók, a proxyk, a tűzfalak a készüléken megszakított kapcsolatok esetében jönnek létre, és a készülékből származnak. 

![TCP-beszélgetések forgalmának száma egy továbbító berendezésen keresztül](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>A flow korlátai és javaslatai

Napjainkban az Azure hálózati verem támogatja a 250K teljes hálózati folyamatait, és jó teljesítményt nyújt a több mint 8 CPU-magot tartalmazó virtuális gépekhez, és a teljes körű teljesítmény a 8 CPU-nál kevesebb processzorral rendelkező virtuális gépek esetében. Ez a korlátozás a hálózati teljesítményre vonatkozóan zökkenőmentesen csökkenti a további folyamatokat, amelyeknek a terhelése 500 000 000 teljes folyamat, a 250K bejövő és a 250K kimenő, a további folyamatok eldobása után.

||Virtuális gépek < 8 CPU-maggal|Virtuális gépek 8 és CPU maggal|
|---|---|---|
|<b>Jó teljesítmény</b>|100 000 folyamat |250K-folyamatok|
|<b>Csökkentett teljesítmény</b>|Több mint 100 000 folyamat|250K-folyamatok felett|
|<b>Folyamat korlátja</b>|500K-folyamatok|500K-folyamatok|

A metrikák a [Azure monitorban](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) érhetők el, hogy nyomon kövessék a hálózati folyamatok számát és a folyamat létrehozásának sebességét a virtuális GÉPEN vagy VMSS-példányokon.

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

A kapcsolat létesítése és a megszüntetési díjak is befolyásolhatják a hálózati teljesítményt, mivel a kapcsolat létesítési és megszüntetési megosztása a CPU-t a csomagok feldolgozására szolgáló rutinokkal. Azt javasoljuk, hogy a számítási feladatokat a várt forgalmi mintákkal és a teljesítménybeli igényeknek megfelelő méretezéssel bővítse. 

## <a name="next-steps"></a>Következő lépések

- [A hálózati teljesítmény optimalizálása egy virtuális gép operációs rendszere esetében](virtual-network-optimize-network-bandwidth.md)
- Egy virtuális gép [hálózati teljesítményének tesztelése](virtual-network-bandwidth-testing.md) .
