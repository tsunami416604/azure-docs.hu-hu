---
title: Számításiteljesítmény-mérési pontszámok COMPUTE az Azure Windows virtuális gépek |} A Microsoft Docs
description: Hasonlítsa össze a SPECint számítási számításiteljesítmény-mérési pontszámok Windows Server rendszert futtató Azure virtuális gépekhez.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 69ae72ec-e8be-4e46-a8f0-e744aebb5cc2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/09/2018
ms.author: cynthn;davberg
ms.openlocfilehash: 43d0ab6552847df7f1f2a8599dcc7cb9a8fcb57b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698829"
---
# <a name="compute-benchmark-scores-for-windows-vms"></a>Számításiteljesítmény-mérési pontszámok számítási a Windows virtuális gépek
A következő SPECInt számításiteljesítmény-mérési pontszámok megjelenítése az Azure nagy teljesítményű virtuális gép prototípusait Windows Server rendszert futtató számítási teljesítményt. Számításiteljesítmény-mérési pontszámok számítási is elérhetők az [Linux rendszerű virtuális gépek](../linux/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> A Linux-számokat nemrég frissítve lett-e, és virtuális gépek több átfogó készletét tartalmazzák.

## <a name="a-series---compute-intensive"></a>A-sorozat – nagy számítási igényű
| Méret | Virtuális magok | NUMA-csomópontok | CPU | Futtatások | Átlagos alapdíj | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |Intel Xeon CPU E5-2670 0 \@ 2,6 GHz-es |10 |236.1 |1.1 |
| Standard_A9 |16 |2 |Intel Xeon CPU E5-2670 0 \@ 2,6 GHz-es |10 |450.3 |7.0 |
| Standard_A10 |8 |1 |Intel Xeon CPU E5-2670 0 \@ 2,6 GHz-es |5 |235.6 |0.9 |
| Standard_A11 |16 |2 |Intel Xeon CPU E5-2670 0 \@ 2,6 GHz-es |7 |454.7 |4.8 |

## <a name="dv2-series"></a>Dv2-sorozat
| Méret | Virtuális magok | NUMA-csomópontok | CPU | Futtatások | Átlagos alapdíj | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |1 |Intel Xeon E5-2673 v3 \@ 2,4 GHz-es |83 |36.6 |2.6 |
| Standard_D2_v2 |2 |1 |Intel Xeon E5-2673 v3 \@ 2,4 GHz-es |27 |70.0 |3.7 |
| Standard_D3_v2 |4 |1 |Intel Xeon E5-2673 v3 \@ 2,4 GHz-es |19 |130.5 |4.4 |
| Standard_D4_v2 |8 |1 |Intel Xeon E5-2673 v3 \@ 2,4 GHz-es |19 |238.1 |5.2 |
| Standard_D5_v2 |16 |2 |Intel Xeon E5-2673 v3 \@ 2,4 GHz-es |14 |460.9 |15.4 |
| Standard_D11_v2 |2 |1 |Intel Xeon E5-2673 v3 \@ 2,4 GHz-es |19 |70.1 |3.7 |
| Standard_D12_v2 |4 |1 |Intel Xeon E5-2673 v3 \@ 2,4 GHz-es |2 |132.0 |1.4 |
| Standard_D13_v2 |8 |1 |Intel Xeon E5-2673 v3 \@ 2,4 GHz-es |17 |235.8 |3.8 |
| Standard_D14_v2 |16 |2 |Intel Xeon E5-2673 v3 \@ 2,4 GHz-es |15 |460.8 |6.5 |

## <a name="g-series-gs-series"></a>G-series, GS-series
| Méret | Virtuális magok | NUMA-csomópontok | CPU | Futtatások | Átlagos alapdíj | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard G1, Standard_GS1 |2 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz-es |31 |71.8 |6.5 |
| Standard_G2, Standard_GS2 |4 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz-es |5 |133.4 |13.0 |
| Standard G3, Standard_GS3 |8 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz-es |6 |242.3 |6.0 |
| Standard_G4, Standard_GS4 |16 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz-es |15 |398.9 |6.0 |
| Standard G5, például a Standard_GS5 |32 |2 |Intel Xeon E5-2698B v3 \@ 2 GHz-es |22 |762.8 |3.7 |

## <a name="h-series"></a>H-sorozat
| Méret | Virtuális magok | NUMA-csomópontok | CPU | Futtatások | Átlagos alapdíj  | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |1 |Intel Xeon E5-2667 v3 \@ 3,2 GHz-es |5 |297.4 |0.9 |
| Standard_H16 |16 |2 |Intel Xeon E5-2667 v3 \@ 3,2 GHz-es |5 |575.8 |6.8 |
| Standard_H8m |8 |1 |Intel Xeon E5-2667 v3 \@ 3,2 GHz-es |5 |297.0 |1.2 |
| Standard_H16m |16 |2 |Intel Xeon E5-2667 v3 \@ 3,2 GHz-es |5 |572.2 |3.9 |
| Standard h16r méretű |16 |2 |Intel Xeon E5-2667 v3 \@ 3,2 GHz-es |5 |573.2 |2.9 |
| Standard h16mr méretű |16 |2 |Intel Xeon E5-2667 v3 \@ 3,2 GHz-es |7 |569.6 |2.8 |

## <a name="about-specint"></a>SPECint kapcsolatban
Windows számítása futtatásával [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) Windows-kiszolgálón. SPECint futtatták lehetőséggel az alapdíj mellett (SPECint_rate2006), egy példánnyal vCPU /. SPECint áll 12 külön teszteket, minden egyes háromszor fut le, a középérték minden tesztből véve, és azokat az űrlap-pontszámot összetett súlyozási. Ezek között az átlagos pontszámok látható biztosít több virtuális gép is futtatja.

## <a name="next-steps"></a>További lépések
* Tekintse meg a tárolókapacitást, a háttértárakról és a Virtuálisgép-méretek között válogat akár további szempontokról, [virtuális gépek méretei](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

