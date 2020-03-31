---
title: Az Azure virtuálisgép-hálózat átviteli fegyvere | Microsoft dokumentumok
description: Ismerje meg az Azure virtuálisgép-hálózati átviteli.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245433"
---
# <a name="virtual-machine-network-bandwidth"></a>Virtuális gép hálózati sávszélessége

Az Azure különböző virtuálisgép-méreteket és -típusokat kínál, amelyek mindegyike különböző teljesítményképességekkel rendelkezik. Az egyik képesség a hálózati átviteli sebesség (vagy sávszélesség), megabit per másodpercben (Mbps) mérve. Mivel a virtuális gépek megosztott hardveren vannak tárolva, a hálózati kapacitást igazságosan meg kell osztani az azonos hardveren osztozó virtuális gépek között. A nagyobb virtuális gépek viszonylag nagyobb sávszélességet foglalnak le, mint a kisebb virtuális gépek.
 
Az egyes virtuális gépek számára lefoglalt hálózati sávszélesség et a virtuális gépről kimenő (kimenő) forgalom alapján méri a kapcsolat. A virtuális gépet elhagyó összes hálózati forgalom a lefoglalt korlátba számít, a céltól függetlenül. Ha például egy virtuális gép 1000 Mb/s-os korláttal rendelkezik, ez a korlát akkor érvényes, ha a kimenő forgalom ugyanazon virtuális hálózaton belül vagy az Azure-on kívül egy másik virtuális gépre vonatkozik.
 
A be- és visszalépés nem mért vagy közvetlenül korlátozott. Vannak azonban más tényezők, például a PROCESSZOR és a tárolási korlátok, amelyek hatással lehetnek a virtuális gép azon képességét, hogy feldolgozza a bejövő adatokat.

A gyorsított hálózatkezelés olyan szolgáltatás, amelynek célja a hálózati teljesítmény javítása, beleértve a késést, az átviteli teljesítményt és a processzorkihasználtságot. Bár a gyorsított hálózatkezelés javíthatja a virtuális gép átviteli, ezt csak a virtuális gép lefoglalt sávszélesség. A gyorsított hálózati kapcsolatról a [Windows](create-vm-accelerated-networking-powershell.md) vagy [Linux](create-vm-accelerated-networking-cli.md) rendszerű virtuális gépek gyorsított hálózatkezelés e-jéről olvashat bővebben.
 
Az Azure virtuális gépeknek rendelkezniük kell egy, de több hálózati adapterek is rendelkezhetnek. A virtuális géphez rendelt sávszélesség a virtuális géphez csatlakoztatott összes hálózati csatoló összes kimenő forgalmának összege. Más szóval a lefoglalt sávszélesség virtuális gépenként történik, függetlenül attól, hogy hány hálózati adapter csatlakozik a virtuális géphez. Ha meg szeretné tudni, hogy hány hálózati felületkülönböző Azure virtuálisgép-méretek támogatása, tekintse meg az Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek mérete. 

## <a name="expected-network-throughput"></a>Várható hálózati átviteli érték

A várható kimenő átviteli és az egyes virtuális gépek mérete által támogatott hálózati adapterek száma az Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek méretében van részletezve. Válasszon ki egy típust, például az Általános célt, majd jelöljön ki egy méretsorozatot az eredményül kapott oldalon, például a Dv2-sorozatot. Minden adatsor rendelkezik egy táblázattal, amely az utolsó oszlopban rendelkezik hálózati specifikációkkal, melynek címe: **Maximális hálózati adapterek / Várható hálózati teljesítmény (Mbps)**. 

Az átviteli korlát a virtuális gépre vonatkozik. Az átviteli átatot nem befolyásolják a következő tényezők:
- **Hálózati csatolók száma**: A sávszélesség-korlát a virtuális gépről érkező összes kimenő forgalom összesítése.
- **Gyorsított hálózatkezelés**: Bár a szolgáltatás hasznos lehet a közzétett korlát elérésében, nem változtatja meg a korlátot.
- **Forgalmi cél:** Minden cél beleszámít a kimenő korlátba.
- **Protokoll**: Az összes protokollon keresztül immár összes kimenő forgalom beleszámít a korlátba.

## <a name="network-flow-limits"></a>Hálózati folyamatkorlátok

A sávszélesség mellett a virtuális gépeken egy adott időpontban található hálózati kapcsolatok száma is befolyásolhatja a hálózati teljesítményt. Az Azure hálózati verem fenntartja állapotát a TCP/UDP-kapcsolat egyes irányaihoz a "folyamatok" nevű adatstruktúrákban. Egy tipikus TCP/UDP-kapcsolathoz 2 folyamat jön létre, az egyik a bejövő, a másik pedig a kimenő irányhoz. 

A végpontok közötti adatátvitelhez az adatátvitelt végző folyamatokon kívül több folyamat létrehozása is szükséges. Néhány példa a DNS-feloldáshoz létrehozott folyamatok és a terheléselosztó állapotmintáihoz létrehozott folyamatok. Azt is vegye figyelembe, hogy a hálózati virtuális készülékek (NVA-k), például átjárók, proxyk, tűzfalak, látni fogja, hogy a rendszer a készüléken leszakított és a készülék által létrehozott kapcsolatokhoz létrehozott folyamatokat fog látni. 

![A TCP-beszélgetés folyamatszáma átirányító berendezésen keresztül](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Áramlási korlátok és javaslatok

Ma az Azure hálózati verem támogatja a 250K teljes hálózati folyamatok jó teljesítményt a virtuális gépek nagyobb, mint 8 CPU-magok és 100k teljes forgalom jó teljesítményt a virtuális gépek kevesebb, mint 8 CPU-magok. Ezen a korláton túl a hálózati teljesítmény kecsesen csökken a további folyamatok hoz a 500K teljes forgalom, a 250K bejövő és a 250 K kimenő, amely után további forgalom megszakad.

||<8 CPU-maggal rendelkező virtuális gépek|Több processzormaggal rendelkező virtuális gépek|
|---|---|---|
|<b>Jó teljesítmény</b>|100 ezer folyamat |250 ezer áramlás|
|<b>Leromlott teljesítmény</b>|100 ezer felett|250 ezer felett|
|<b>Folyamathatár</b>|500 ezer tranzakció|500 ezer tranzakció|

Metrikák érhetők el az [Azure Monitorban](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) a hálózati folyamatok számának és a virtuális gép vagy vMSS-példányok folyamatlétrehozási sebességének nyomon követéséhez.

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

A kapcsolat létesítési és végződési díjai a hálózati teljesítményre is hatással lehetnek, mivel a kapcsolat létrehozása és a megszüntetés megosztja a CPU-t a csomagfeldolgozási rutinokkal. Azt javasoljuk, hogy a számítási feladatok a várt forgalmi minták és a számítási feladatok horizontális felskálázása megfelelően megfeleljen a teljesítményigényeknek. 

## <a name="next-steps"></a>További lépések

- [A hálózati teljesítmény optimalizálása egy virtuális gép operációs rendszere esetében](virtual-network-optimize-network-bandwidth.md)
- [Tesztelje](virtual-network-bandwidth-testing.md) a virtuális gépek hálózati átviteli csatornáját.
