---
title: Az Azure Advisor költség javaslatok |} Microsoft Docs
description: Azure Advisor segítségével optimalizálhatja az Azure-környezetekhez költségét.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 7a8807a580f1a7f1fe67e026a8fbd4cc0e96c41c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="advisor-cost-recommendations"></a>Költség javaslatokat biztosít

Az Advisor segítségével optimalizálhatja, és csökkentheti a teljes Azure töltött túl magas és üresjárati erőforrások azonosításával. Akkor is beolvasása költség javaslatokat a **költség** az Advisor irányítópult fület.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimalizálása virtuális gép töltött átméretezésével vagy a túl példány leállítása 
Bár egyes alkalmazás-forgatókönyvekre eredményezhet alacsony kihasználtságú úgy lett kialakítva, gyakran mentheti pénz való kezelésekor a a virtuális gépek száma és mérete. Az Advisor figyeli a virtuális gépek használatának 14 napja, és majd azonosítja a kis-kihasználtságának virtuális gépek. Virtuális gépek, amelyek CPU-felhasználás csak 5 % vagy kevesebb és hálózati aktivitás a 7 MB vagy kisebb a négy vagy több napot minősülnek alacsony használt virtuális gépek.

Az Advisor jeleníti meg, hogy ha szeretné leállítani, vagy méretezze át a virtuális gép futtatásához Folytatás becsült költségét.

Ha szigorúbb túl virtuális gépek azonosításához, lehet, módosíthatja a átlagos CPU kihasználtsága szabály egy előfizetés alapon.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Csökkentheti a költségeket kiépített ExpressRoute-Kapcsolatcsoportok kiküszöbölése révén
Az Advisor azonosítja a szolgáltató állapota már ExpressRoute-Kapcsolatcsoportok *nincs kiépítve* több mint egy hónap, és javasolja a kapcsolatcsoport törlése, ha nem tervezi a kapcsolatcsoport adja meg a kapcsolatot szolgáltató.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Költség javaslatok az Azure Advisor elérése

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2.  Az Advisor irányítópultján kattintson a **költség** fülre.

## <a name="next-steps"></a>További lépések

Az Advisor-javaslatokra kapcsolatos további információkért lásd:
* [Az Advisor bemutatása](advisor-overview.md)
* [Első lépések](advisor-get-started.md)
* [Teljesítmény javaslatokat biztosít](advisor-cost-recommendations.md)
* [Magas rendelkezésre állású javaslatokat biztosít](advisor-cost-recommendations.md)
* [Biztonsági javaslatokat biztosít](advisor-cost-recommendations.md)
