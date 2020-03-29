---
title: Bevezetés a biztonsági csoport nézetbe az Azure Network Watcher ben | Microsoft dokumentumok
description: Ez a lap áttekintést nyújt a Hálózatfigyelő biztonsági nézetének
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: damendo
ms.openlocfilehash: 18619cc0f7a21a39d0802575c484c68c5fa1a624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840757"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Bevezetés a hatékony biztonsági szabályok nézetbe az Azure Network Watcherben

A hálózati biztonsági csoportok alhálózati vagy hálózati adapterek szintjén vannak társítva. Ha egy alhálózati szinten van társítva, az alhálózat összes virtuálisgép-példányára vonatkozik. A hatékony biztonsági szabályok nézet visszaadja az összes konfigurált NSG-t és szabályt, amely hálózati adapteren és alhálózati szinten van társítva egy virtuális géphez, amely betekintést nyújt a konfigurációba. Emellett a hatékony biztonsági szabályok at a virtuális gép minden egyes hálózati adapterek. A Hatékony biztonsági szabályok nézet használatával felmérheti a virtuális gép hálózati biztonsági réseit, például a nyitott portokat. Azt is ellenőrizheti, hogy a hálózati biztonsági csoport a várt módon működik-e [a konfigurált és a jóváhagyott biztonsági szabályok összehasonlítása](network-watcher-nsg-auditing-powershell.md)alapján.

A kiterjesztett használati eset a biztonsági megfelelőség és a naplózás. A biztonsági szabályok előíró készletét a szervezet biztonsági cégirányítási modelljeként határozhatja meg. Az időszakos megfelelőségi naplózás programozott módon valósítható meg, ha összehasonlítja az előíró szabályokat a hálózat minden egyes virtuális gépének hatékony szabályaival.

A portálon szabályok jelennek meg minden hálózati adapter és csoportosítva bejövő vs kimenő. Ez egyszerű betekintést nyújt a virtuális gépekre alkalmazott szabályokba. A letöltés gomb áll rendelkezésre, hogy könnyen letölthető az összes biztonsági szabályok nem számít, a lap egy CSV fájlt.

![biztonsági csoport nézete][1]

Szabályok választhatók ki, és egy új panel nyílik meg a Hálózati biztonsági csoport, valamint a forrás- és célelőtagok megjelenítéséhez. Ebből a panelből közvetlenül a Hálózati biztonsági csoport erőforráshoz navigálhat.

![Lebontása][2]

### <a name="next-steps"></a>További lépések

Megtudhatja, hogy miként naplózhatja a hálózati biztonsági csoport beállításait a [Naplózási hálózat biztonsági csoportjának beállításainak a PowerShell használatával](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









