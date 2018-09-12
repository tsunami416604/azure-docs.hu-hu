---
title: Bevezetés a biztonsági csoport nézet az Azure Network Watcher |} A Microsoft Docs
description: Ez az oldal áttekintést a Network Watcher biztonsági megtekintése funkció
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: ad27ab85-9d84-4759-b2b9-e861ef8ea8d8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: jdial
ms.openlocfilehash: 15f6bd0d7da63924e52db8ec7e2cbb0ee7483f82
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391538"
---
# <a name="introduction-to-network-security-group-view-in-azure-network-watcher"></a>Hálózati biztonsági csoport nézet az Azure Network Watcher bemutatása

Hálózati biztonsági csoportok társítva, egy alhálózatot vagy egy hálózati adapterek szintjén. Ha kapcsolódik egy alhálózat szintjén, az alhálózat összes Virtuálisgép-példány vonatkozik. Hálózati biztonsági csoport nézet adja vissza az összes beállított NSG-k és szabályokat, amelyek a konfiguráció betekintést nyújtó virtuális gép hálózati adapter és az alhálózat szintjén vannak társítva. Emellett az érvényben lévő biztonsági szabályokat a rendszer minden virtuális gép hálózati adapterei adja vissza. Használatával a hálózati biztonsági csoport nézet, felmérheti a hálózati biztonsági réseket, például a portok megnyitása virtuális Géphez. Ha a hálózati biztonsági csoport a várt módon működik alapján is ellenőrizheti a [a beállított és a jóváhagyott biztonsági szabályok összehasonlítása](network-watcher-nsg-auditing-powershell.md).

Több kiegészítő használati eset biztonsági, megfelelőségi és naplózási van. Megadhatja egy előíró biztonsági szabálykészletet biztonsági cégirányítási egy modellt, a szervezetben. Egy rendszeres megfelelőség ellenőrzése a következőket öleli szabályokat a hatékony szabályokat összehasonlítja minden, a hálózaton található virtuális gépek programozott módon implementálható.

Szabályok érvényesek, alhálózat, hálózati adapter és alapértelmezés szerint vannak csoportosítva, a portálon. Ez lehetővé teszi a egyszerű a virtuális gépek szabályokat. A Letöltés gombra, egyszerűen töltse le az összes biztonsági szabály függetlenül attól, hogy a lap egy CSV-fájlba van megadva.

![biztonsági csoport Nézet][1]

Szabályok választhatók ki, és a egy új panel megnyílik a hálózati biztonsági csoport és a forrás és cél-előtagok megjelenítése. Ezen a panelen is lépjen közvetlenül a hálózati biztonsági csoport-erőforráshoz.

![drilldown][2]

### <a name="next-steps"></a>További lépések

Ismerje meg, a hálózati biztonsági csoport beállításainak naplózása funkcionáló [hálózati biztonsági csoport naplózási beállítások a PowerShell-lel](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









