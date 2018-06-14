---
title: Azure virtuális gép hálózati teljesítményt |} Microsoft Docs
description: További tudnivalók az Azure virtuális gép hálózati teljesítményt.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: f22b6f361f0c5bea547721309bb0f75b62f18d92
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
ms.locfileid: "27778955"
---
# <a name="virtual-machine-network-bandwidth"></a>Virtuális gép hálózati sávszélesség

Azure Virtuálisgép-méretek és típusok, mindegyik különböző vegyesen teljesítménybeli képességek választékát kínálja. Egy képesség, a hálózati átviteli sebesség (vagy sávszélesség), megabit / másodperc (Mbps). Virtuális gépek megosztott hardver üzemelteti, mert a hálózati kapacitás viszonylag kell osztani a virtuális gépek ugyanazt a hardvert megosztása között. Nagyobb virtuális gépek viszonylag nagyobb sávszélesség-nál kisebb virtuális gépek foglal le.
 
A hálózati sávszélesség, minden virtuális gép számára lefoglalt kimenő forgalom (kimenő) forgalmat a virtuális gépről a forgalmi díjas. Az összes hálózati forgalom, így a virtuális gép számít a lefoglalt határt, függetlenül a cél felé. Például ha egy virtuális gépet egy 1000 MB/s korlátot, ezt a határértéket érvényes a kimenő forgalom egy másik virtuális gép ugyanazon a virtuális hálózaton, vagy Azure-on kívüli szánt.
 
Nem díjköteles vagy korlátozott közvetlenül érkező. Vannak azonban más tényezőktől, például a Processzor- és tárterületi korlátozásai, ami kedvezőtlen hatással lehet a virtuális gép képes-e feldolgozni a bejövő adatok.

Gyorsított hálózatkezelés egy olyan szolgáltatás, amelyekkel javítható a hálózati teljesítményt, beleértve a késés, az átviteli sebesség és a CPU felhasználását. Gyorsított hálózatkezelés javíthatja a virtuális gép átviteli, amíg azt is megteheti csak a virtuális gép létrehozása foglalt sávszélesség. Gyorsított hálózatokkal kapcsolatos további tudnivalókért tekintse meg a hálózat gyorsított [Windows](create-vm-accelerated-networking-powershell.md) vagy [Linux](create-vm-accelerated-networking-cli.md) virtuális gépek.
 
Az Azure virtuális gépek rendelkeznie kell egy, de előfordulhat, hogy több, a hálózati illesztőt kapcsolódik. A virtuális gép számára lefoglalt sávszélessége az összegük minden kimenő forgalom egy virtuális géphez csatolt hálózati adaptereken keresztül. Más szóval a hozzárendelt sávszélesség virtuális gépenként, függetlenül attól, hogy hány hálózati adapterek vannak csatolva a virtuális gép van. Hány hálózati adapterek különböző Azure virtuális gép mérete támogatási kapcsolatban: Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Virtuálisgép-méretek. 

## <a name="expected-network-throughput"></a>Várt hálózati átviteli sebesség

Az Azure-ban részletes-várt kimenő átviteli sebesség és a hálózati adapterek minden virtuális gép mérete által támogatott száma [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Virtuálisgép-méretek. Jelöljön ki egy típus, például az általános célú, majd az eredményül kapott oldalon, például a Dv2-sorozat méret – több. Valamennyi adatsorozatnál van ilyen nevű tábla című, az utolsó oszlopban specifikációk hálózati **maximális hálózati adapterek / (Mbps sebességű) hálózati teljesítmény várható**. 

Az átviteli sebesség korlát vonatkozik a virtuális géphez. Átviteli sebesség nem érinti a következő tényezőket:
- **Hálózati adapterek száma**: A sávszélesség-korlátozás összegző minden kimenő forgalom a virtuális gépről.
- **Hálózatkezelés az elérését gyorsítja fel**: a szolgáltatás elérése érdekében a közzétett korlátot hasznos lehet, ha nem változtatja meg a határértéket.
- **Forgalom cél**: minden rendeltetési hely felé a kimenő korlátot száma.
- **Protokoll**: minden protokollokon keresztül minden kimenő forgalom módszert a határérték felé számolnak.

## <a name="next-steps"></a>További lépések

- [A virtuális gép operációs rendszert a hálózat átviteli sebességét optimalizálása](virtual-network-optimize-network-bandwidth.md)
- [Teszt hálózati teljesítményt](virtual-network-bandwidth-testing.md) egy virtuális géphez.
