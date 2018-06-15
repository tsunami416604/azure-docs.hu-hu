---
title: Biztonsági csoport megtekintése az Azure hálózati figyelőt bemutatása |} Microsoft Docs
description: Ezen a lapon a hálózati figyelőt biztonsági nézet képességek áttekintése
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
ms.openlocfilehash: f4175875b68c52e68588b8d0debd003ab73427ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23864209"
---
# <a name="introduction-to-network-security-group-view-in-azure-network-watcher"></a>Hálózati biztonsági csoport megtekintése az Azure hálózati figyelőt bemutatása

Hálózati biztonsági csoportok társított alhálózati szinten, vagy egy hálózati adapter szintjén. Hozzárendelt alhálózat szinten, ha az alhálózat összes Virtuálisgép-példányára vonatkozik. Hálózati biztonsági csoport nézet összes beállított NSG-ket és egy virtuális gépet, a konfiguráció betekintést biztosít a hálózati és alhálózati szinten társított szabályokat adja vissza. Emellett a hatékony biztonsági szabályok adja vissza minden virtuális gép hálózati adapterei. Hálózati biztonsági csoport használatával nézet felmérheti a virtuális gépek, a hálózati biztonsági réseket, például a megnyitott portok. Ha a hálózati biztonsági csoport a várt módon működik alapján is ellenőrzéséhez egy [a beállított és a hatékony biztonsági szabályok összehasonlítása](network-watcher-nsg-auditing-powershell.md).

Több kiterjesztett használati eset biztonsági, megfelelőségi és naplózási van. Biztonsági irányításhoz modellként előíró meghatározott biztonsági szabályok adhat meg a szervezetében. Egy rendszeres megfelelőségi naplózási programozott módon valósítható a hatékony szabályok az előírásoknak megfelelő szabályokkal összehasonlítva az egyes virtuális gépek a hálózaton.

A portál hatályos, a alhálózat, a hálózati illesztő és az alapértelmezett szabályok vannak osztva. Ennek révén a virtuális gép szabálya egyszerű képet. Letöltés gomb megadott könnyen töltse le az összes biztonsági szabály függetlenül a lap egy CSV-fájlba.

![Biztonsági csoport megtekintése][1]

Szabályok választhatók ki, és jelenítse meg a hálózati biztonsági csoport és a forrás és cél-előtagok megnyílik egy új panelen. Ezen a panelen lépjen közvetlenül a hálózati biztonsági csoport erőforrás.

![Részletezési][2]

### <a name="next-steps"></a>Következő lépések

Ismerje meg, látogasson el a hálózati biztonsági csoport beállításait naplózása [hálózati biztonsági csoport naplózási beállítások a PowerShell használatával](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









