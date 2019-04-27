---
title: Az Azure virtuális gépek hálózati átviteli sebessége |} A Microsoft Docs
description: További információ az Azure virtuális gépek hálózati átviteli sebessége.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: kumud
ms.openlocfilehash: 29c4926f56070874fe17622170e697986df0fbc3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743080"
---
# <a name="virtual-machine-network-bandwidth"></a>Virtuális gép hálózati sávszélesség

Az Azure számos olyan Virtuálisgép-méretek és-típusok, minden egyes teljesítménybeli különböző vegyesen. Egy szolgáltatás hálózati átviteli sebesség (vagy a sávszélesség) megadása megabit / másodperc (Mbps) érhető el. Virtuális gépek megosztott hardveren található, mert a hálózati kapacitás a virtuális gépek ugyanazt a hardvert megosztás között viszonylag kell osztani. Nagyobb virtuális gépek viszonylag további sávszélesség-nál kisebb méretű virtuális gépek vannak lefoglalva.
 
A hálózati sávszélességet, minden virtuális gép számára lefoglalt kimenő forgalom (kimenő) forgalmat a virtuális gépről a forgalmi díjas. Minden hálózati forgalmat a virtuális gép elhagyása után kell fizetnie a lefoglalt korlátot, függetlenül a cél felé. Ha például egy virtuális gépet egy 1000 MB/s-korlát, akkor ezt a korlátot attól függetlenül érvényes, a kimenő forgalom az azonos virtuális hálózatba, vagy Azure-on kívül egy másik virtuális gép szánt.
 
Bejövő forgalom nem díjköteles, vagy közvetlenül korlátozott. Vannak azonban egyéb tényezők, például a Processzor- és tárterületi korlátozásai, ami hatással lehet a virtuális gépek képesek-e feldolgozni a beérkező adatokat.

Gyorsított hálózatkezelés funkciója, amelyekkel javítható a hálózati teljesítmény, beleértve a késés, átviteli sebesség és CPU-kihasználtság. Gyorsított hálózatkezelés javítja a virtuális gépek átviteli sebesség, miközben azt is megteheti csak a virtuális gép legfeljebb kiosztott sávszélességet. Gyorsított hálózatkezelés kapcsolatos további információkért lásd a gyorsított hálózatkezelésével foglalkozó [Windows](create-vm-accelerated-networking-powershell.md) vagy [Linux](create-vm-accelerated-networking-cli.md) virtuális gépeket.
 
Az Azure virtual machines rendelkeznie kell egy, hanem több, a hálózati adapterek csatlakoztatva. Egy virtuális gépnek kiosztott sávszélesség egyezik meg az összes kimenő forgalmat a virtuális géphez csatlakoztatott összes hálózati adapter esetében. Más szóval virtuális gépenként, hány hálózati adaptereket a virtuális géphez csatolt függetlenül a lefoglalt a sávszélesség. Ha szeretné megtudni, hogy hány hálózati adapterek különböző Azure-beli Virtuálisgép-méretek támogatási, tekintse meg az Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Virtuálisgép-méretek. 

## <a name="expected-network-throughput"></a>Várt hálózati teljesítmény

Az Azure-ban részletes-várt kimenő adatátviteli és az egyes virtuális gép mérete által támogatott hálózati adapterek száma [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Virtuálisgép-méretek. Válassza ki a típusát, például az általános célú, majd válassza ki a méret-sorozat az eredményül kapott oldalon, mint a Dv2 sorozat. Minden sorozatot a hálózatkezelés című, az utolsó oszlopban specifikációk tartalmazó táblázat található **hálózati adapterek max. száma / várt hálózati teljesítmény (Mbps)**. 

Az átviteli sebesség korlát vonatkozik a virtuális géphez. Átviteli sebesség nem érinti a következő tényezőktől függ:
- **Hálózati adapterek száma**: A sávszélesség korlátja minden kimenő forgalom a virtuális gépből épül.
- **Gyorsított hálózatkezelés**: Bár a funkció akkor hasznosak, növelve a közzétett korlátot, nem változtatja meg a határértéket.
- **Forgalom cél**: Az összes destinations beleszámítanak a kimenő korlátot.
- **Protokoll**: Minden kimenő forgalmat az összes protokollhoz keresztül a határérték felé számolnak.

## <a name="next-steps"></a>További lépések

- [A hálózati teljesítmény optimalizálása egy virtuális gép operációs rendszere esetében](virtual-network-optimize-network-bandwidth.md)
- [Teszt hálózati átviteli sebesség](virtual-network-bandwidth-testing.md) egy virtuális géphez.
