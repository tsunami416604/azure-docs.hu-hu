---
title: "Teljesítményteszt pontszámok Windows virtuális gépek számítási |} Microsoft Docs"
description: "Windows Server rendszert futtató Azure virtuális gépek SPECint számítási teljesítményteszt pontszámok összehasonlítása"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 69ae72ec-e8be-4e46-a8f0-e744aebb5cc2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cynthn
ms.openlocfilehash: dc715a9bf4e5366e1719de7c649feb06b0c4bee7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="compute-benchmark-scores-for-windows-vms"></a>Teljesítményteszt pontszámok Windows virtuális gépek számítási
A következő SPECInt teljesítményteszt eredmények megjelenítése Azure nagy teljesítményű virtuális gép következőhöz Windows Server rendszert futtató számítási teljesítményt. Számítási teljesítményteszt pontszámok is elérhető [Linux virtuális gépek](../linux/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="a-series---compute-intensive"></a>A-sorozatú-számítási igényű
| Méret | Vcpu | NUMA-csomópontok | CPU | Fut. | Átlagos alap arány | Szórás |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |Intel Xeon CPU E5-2670 0 @ 2.6-os GHz |10 |236.1 |1.1 |
| Standard_A9 |16 |2 |Intel Xeon CPU E5-2670 0 @ 2.6-os GHz |10 |450.3 |7.0 |
| Standard_A10 |8 |1 |Intel Xeon CPU E5-2670 0 @ 2.6-os GHz |5 |235.6 |0.9 |
| Standard_A11 |16 |2 |Intel Xeon CPU E5-2670 0 @ 2.6-os GHz |7 |454.7 |4.8 |

## <a name="dv2-series"></a>Dv2-sorozat
| Méret | Vcpu | NUMA-csomópontok | CPU | Fut. | Átlagos alap arány | Szórás |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |1 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |83 |36.6 |2.6 |
| Standard_D2_v2 |2 |1 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |27 |70.0 |3.7 |
| Standard_D3_v2 |4 |1 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |19 |130.5 |4.4 |
| Standard_D4_v2 |8 |1 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |19 |238.1 |5.2 |
| Standard_D5_v2 |16 |2 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |14 |460.9 |15.4 |
| Standard_D11_v2 |2 |1 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |19 |70.1 |3.7 |
| Standard_D12_v2 |4 |1 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |2 |132.0 |1.4 |
| Standard_D13_v2 |8 |1 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |17 |235.8 |3.8 |
| Standard_D14_v2 |16 |2 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |15 |460.8 |6.5 |

## <a name="g-series-gs-series"></a>G-sorozat, GS sorozatnak
| Méret | Vcpu | NUMA-csomópontok | CPU | Fut. | Átlagos alap arány | Szórás |
| --- | --- | --- | --- | --- | --- | --- |
| Standard G1, Standard_GS1 |2 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-es |31 |71.8 |6.5 |
| Standard szintű, G2, Standard_GS2 |4 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-es |5 |133.4 |13.0 |
| Standard szintű, G3, Standard_GS3 |8 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-es |6 |242.3 |6.0 |
| Standard szintű, G4, Standard_GS4 |16 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-es |15 |398.9 |6.0 |
| Standard G5, Standard_GS5 |32 |2 |Intel Xeon E5-2698B v3 @ 2 GHz-es |22 |762.8 |3.7 |

## <a name="h-series"></a>H-sorozat
| Méret | Vcpu | NUMA-csomópontok | CPU | Fut. | Átlagos alap arány  | Szórás |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |1 |Intel Xeon E5-2667 v3 3,2 GHz-es @ |5 |297.4 |0.9 |
| Standard_H16 |16 |2 |Intel Xeon E5-2667 v3 3,2 GHz-es @ |5 |575.8 |6.8 |
| Standard_H8m |8 |1 |Intel Xeon E5-2667 v3 3,2 GHz-es @ |5 |297.0 |1.2 |
| Standard_H16m |16 |2 |Intel Xeon E5-2667 v3 3,2 GHz-es @ |5 |572.2 |3.9 |
| Standard h16r méretű |16 |2 |Intel Xeon E5-2667 v3 3,2 GHz-es @ |5 |573.2 |2.9 |
| Standard h16mr méretű |16 |2 |Intel Xeon E5-2667 v3 3,2 GHz-es @ |7 |569.6 |2.8 |

## <a name="about-specint"></a>SPECint kapcsolatos
Windows számok futtatásával volt számított [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) Windows Server rendszeren. SPECint volt futtatva az Alap (SPECint_rate2006) beállítás használata core egy példányt. SPECint áll 12 külön teszteket, minden egyes háromszor fut le, minden tesztből középértékét véve és súlyozási őket egy összetett pontszám kialakításához. Ezek a tesztek között az átlagos pontszámok jelenik meg több virtuális gép is futtatja.

## <a name="next-steps"></a>Következő lépések
* Tárolási kapacitás, a lemez adatai és Virtuálisgép-méretek között további szempontokról, lásd: [virtuális gépek méretei](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

