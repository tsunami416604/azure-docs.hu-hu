---
title: Az Azure virtuális gépek hálózati átviteli sebessége |} A Microsoft Docs
description: További információ az Azure virtuális gépek hálózati átviteli sebessége.
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
ms.author: kumud,steveesp, mareat
ms.openlocfilehash: 9d74e53c754367ecfa63642514db93354fcadf25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153727"
---
# <a name="virtual-machine-network-bandwidth"></a>Virtuális gép hálózati sávszélesség

Az Azure számos olyan Virtuálisgép-méretek és-típusok, minden egyes teljesítménybeli különböző vegyesen. Egy szolgáltatás hálózati átviteli sebesség (vagy a sávszélesség) megadása megabit / másodperc (Mbps) érhető el. Virtuális gépek megosztott hardveren található, mert a hálózati kapacitás a virtuális gépek ugyanazt a hardvert megosztás között viszonylag kell osztani. Nagyobb virtuális gépek viszonylag további sávszélesség-nál kisebb méretű virtuális gépek vannak lefoglalva.
 
A hálózati sávszélességet, minden virtuális gép számára lefoglalt kimenő forgalom (kimenő) forgalmat a virtuális gépről a forgalmi díjas. Minden hálózati forgalmat a virtuális gép elhagyása után kell fizetnie a lefoglalt korlátot, függetlenül a cél felé. Ha például egy virtuális gépet egy 1000 MB/s-korlát, akkor ezt a korlátot attól függetlenül érvényes, a kimenő forgalom az azonos virtuális hálózatba, vagy Azure-on kívül egy másik virtuális gép szánt.
 
Bejövő forgalom nem díjköteles, vagy közvetlenül korlátozott. Vannak azonban egyéb tényezők, például a Processzor- és tárterületi korlátozásai, ami hatással lehet a virtuális gépek képesek-e feldolgozni a beérkező adatokat.

Gyorsított hálózatkezelés funkciója, amelyekkel javítható a hálózati teljesítmény, beleértve a késés, átviteli sebesség és CPU-kihasználtság. Gyorsított hálózatkezelés javítja a virtuális gépek átviteli sebesség, miközben azt is megteheti csak a virtuális gép legfeljebb kiosztott sávszélességet. Gyorsított hálózatkezelés kapcsolatos további információkért lásd a gyorsított hálózatkezelésével foglalkozó [Windows](create-vm-accelerated-networking-powershell.md) vagy [Linux](create-vm-accelerated-networking-cli.md) virtuális gépeket.
 
Az Azure virtual machines rendelkeznie kell egy, hanem több, a hálózati adapterek csatlakoztatva. Egy virtuális gépnek kiosztott sávszélesség egyezik meg az összes kimenő forgalmat a virtuális géphez csatlakoztatott összes hálózati adapter esetében. Más szóval virtuális gépenként, hány hálózati adaptereket a virtuális géphez csatolt függetlenül a lefoglalt a sávszélesség. Ha szeretné megtudni, hogy hány hálózati adapterek különböző Azure-beli Virtuálisgép-méretek támogatási, tekintse meg az Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Virtuálisgép-méretek. 

## <a name="expected-network-throughput"></a>Várt hálózati teljesítmény

Az Azure-ban részletes-várt kimenő adatátviteli és az egyes virtuális gép mérete által támogatott hálózati adapterek száma [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Virtuálisgép-méretek. Válassza ki a típusát, például az általános célú, majd válassza ki a méret-sorozat az eredményül kapott oldalon, mint a Dv2 sorozat. Minden sorozatot a hálózatkezelés című, az utolsó oszlopban specifikációk tartalmazó táblázat található **hálózati adapterek max. száma / várt hálózati teljesítmény (Mbps)** . 

Az átviteli sebesség korlát vonatkozik a virtuális géphez. Átviteli sebesség nem érinti a következő tényezőktől függ:
- **Hálózati adapterek száma**: A sávszélesség korlátja minden kimenő forgalom a virtuális gépből épül.
- **Gyorsított hálózatkezelés**: Bár a funkció akkor hasznosak, növelve a közzétett korlátot, nem változtatja meg a határértéket.
- **Forgalom cél**: Az összes destinations beleszámítanak a kimenő korlátot.
- **Protokoll**: Minden kimenő forgalmat az összes protokollhoz keresztül a határérték felé számolnak.

## <a name="network-flow-limits"></a>Hálózat a Flow korlátozásai

Sávszélesség mellett egy adott időpontban egy virtuális gépen található hálózati kapcsolatok száma befolyásolja a hálózati teljesítményt. Az Azure hálózati verem összes csatlakozás minden irányában egy TCP/UDP-kapcsolatot az "folyamat" nevű datové struktury állapotát kezeli. Egy tipikus TCP/UDP-kapcsolatot hozott létre, egyet a bejövő és kimenő irányban egy másik 2 folyamatok lesz. 

Végpontok közötti adatátvitel igényel, amelyek az adatátvitelhez mellett számos folyamatok létrehozását. Néhány példa a következők: a DNS-feloldás létrehozott folyamatok és a load balancer állapot-mintavételei létrehozott folyamatok. Is vegye figyelembe, hogy a hálózati virtuális berendezések (nva-k), például az átjárók, a proxyk, a tűzfalak, megjelenik a kapcsolatok számára a készülék bármikor, és adja meg a berendezés által létrehozott folyamatok. 

![A folyamat száma TCP beszélgetésnél továbbítási berendezésen keresztül](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>A flow korlátozásai és javaslatok

Még ma az Azure hálózati verem támogatja a 250e teljes hálózati adatfolyamaiba jó teljesítményű virtuális gépek nagyobb, mint 8 processzormag és 100 k teljes folyamatok jó teljesítményű virtuális gépek a kevesebb mint 8 processzormag. A korlát hálózat korábbi csökken a teljesítmény szabályosan for legfeljebb 1 millió rögzített korlátja további folyamatok teljes folyamatok, bejövő és 500 500 KB-os K kimenő, mely további folyamatokat a rendszer elveti után.

||A virtuális gépek < 8 processzormag|8 + CPU processzormaggal rendelkező virtuális gépek|
|---|---|---|
|<b>Good Performance</b>|100K folyamatok |250 ezer folyamatok|
|<b>A teljesítmény csökkenését</b>|100k feletti folyamatok|250 ezer felett folyamatok|
|<b>A folyamat korlát</b>|1 millió folyamatok|1 millió folyamatok|

Metrikák érhetők el a [Azure Monitor](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) nyomon követéséhez a hálózati forgalom és a folyamat létrehozásának sebessége számát a virtuális gép vagy VMSS-példányokon.

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

Kapcsolat létrehozása és a megszűnés díjait is hatással lehet a hálózati kapcsolat létrehozását és a megszűnés megosztások CPU-csomag feldolgozási rutinok teljesítménye. Azt javasoljuk, hogy számítási feladatokhoz várható forgalmi minták és horizontális felskálázása a számítási feladatok megfelelő teljesítménymérési a teljesítmény igényekhez. 

## <a name="next-steps"></a>További lépések

- [A hálózati teljesítmény optimalizálása egy virtuális gép operációs rendszere esetében](virtual-network-optimize-network-bandwidth.md)
- [Teszt hálózati átviteli sebesség](virtual-network-bandwidth-testing.md) egy virtuális géphez.
