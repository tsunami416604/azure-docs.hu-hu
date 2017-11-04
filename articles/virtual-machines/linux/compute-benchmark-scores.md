---
title: "Teljesítményteszt pontszámok Linux virtuális gépek számítási |} Microsoft Docs"
description: "Hasonlítsa össze a CoreMark számítási teljesítményteszt pontszámok Linux operációs rendszert futtató Azure virtuális gépek"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 93e812c1-79dd-40c5-b97b-aa79f5cd7d76
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 3c7b34652ea4c9340b9fe7f6ada3f9992642aeac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="compute-benchmark-scores-for-linux-vms"></a>Teljesítményteszt pontszámok számítási Linux virtuális gépekhez
A következő CoreMark teljesítményteszt eredmények megjelenítése Azure nagy teljesítményű virtuális gép következőhöz Ubuntu rendszert futtató számítási teljesítményt. Számítási teljesítményteszt pontszámok is elérhető [Windows virtuális gépek](../windows/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="a-series---compute-intensive"></a>A-sorozatú-számítási igényű
| Méret | Vcpu | NUMA-csomópontok | CPU | Fut. | Az ismétlés/mp | Szórás |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |Intel Xeon CPU E5-2670 0 @ 2.6-os GHz |179 |110,294 |554 |
| Standard_A9 |16 |2 |Intel Xeon CPU E5-2670 0 @ 2.6-os GHz |189 |210,816 |2,126 |
| Standard_A10 |8 |1 |Intel Xeon CPU E5-2670 0 @ 2.6-os GHz |188 |110,025 |1,045 |
| Standard_A11 |16 |2 |Intel Xeon CPU E5-2670 0 @ 2.6-os GHz |188 |210,727 |2,073 |

## <a name="dv2-series"></a>Dv2-sorozat
| Méret | Vcpu | NUMA-csomópontok | CPU | Fut. | Az ismétlés/mp | Szórás |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |1 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |140 |14,852 |780 |
| Standard_D2_v2 |2 |1 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |133 |29,467 |1,863 |
| Standard_D3_v2 |4 |1 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |139 |56,205 |1,167 |
| Standard_D4_v2 |8 |1 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |126 |108,543 |3,446 |
| Standard_D5_v2 |16 |2 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |126 |205,332 |9,998 |
| Standard_D11_v2 |2 |1 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |125 |28,598 |1,510 |
| Standard_D12_v2 |4 |1 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |131 |55,673 |1,418 |
| Standard_D13_v2 |8 |1 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |140 |107,986 |3,089 |
| Standard_D14_v2 |16 |2 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |140 |208,186 |8,839 |
| Standard_D15_v2 |20 |2 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |28 |268,560 |4,667 |

## <a name="f-series"></a>F-sorozat
| Méret | Vcpu | NUMA-csomópontok | CPU | Fut. | Az ismétlés/mp | Szórás |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_F1 |1 |1 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |154 |15,602 |787 |
| Standard_F2 |2 |1 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |126 |29,519 |1,233 |
| Standard_F4 |4 |1 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |147 |58,709 |1,227 |
| Standard_F8 |8 |1 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |224 |112,772 |3,006 |
| Standard_F16 |16 |2 |Intel Xeon E5-2673 v3 2,4 GHz-es @ |42 |218,571 |5,113 |

## <a name="g-series"></a>G-sorozat
| Méret | Vcpu | NUMA-csomópontok | CPU | Fut. | Az ismétlés/mp | Szórás |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1 |2 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-es |83 |31,310 |2,891 |
| Standard_G2 |4 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-es |84 |60,112 |3,537 |
| Standard_G3 |8 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-es |84 |107,522 |4,537 |
| Standard_G4 |16 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-es |83 |195,116 |5,024 |
| Standard szintű, G5 |32 |2 |Intel Xeon E5-2698B v3 @ 2 GHz-es |84 |360,329 |14,212 |

## <a name="gs-series"></a>GS sorozat
| Méret | Vcpu | NUMA-csomópontok | CPU | Fut. | Az ismétlés/mp | Szórás |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 |2 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-es |84 |28,613 |1,884 |
| Standard_GS2 |4 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-es |83 |54,348 |3,474 |
| Standard_GS3 |8 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-es |83 |104,564 |1,834 |
| Standard_GS4 |16 |1 |Intel Xeon E5-2698B v3 @ 2 GHz-es |84 |194,111 |4,735 |
| Standard_GS5 |32 |2 |Intel Xeon E5-2698B v3 @ 2 GHz-es |84 |357,396 |16,228 |

## <a name="h-series"></a>H-sorozat
| Méret | Vcpu | NUMA-csomópontok | CPU | Fut. | Az ismétlés/mp | Szórás |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |1 |Intel Xeon E5-2667 v3 3,2 GHz-es @ |28 |140,782 |2,512 |
| Standard_H16 |16 |2 |Intel Xeon E5-2667 v3 3,2 GHz-es @ |35 |275,289 |7,110 |
| Standard_H18m |8 |1 |Intel Xeon E5-2667 v3 3,2 GHz-es @ |28 |139,071 |3,988 |
| Standard_H16m |16 |2 |Intel Xeon E5-2667 v3 3,2 GHz-es @ |28 |275,988 |6,963 |
| Standard h16r méretű |16 |2 |Intel Xeon E5-2667 v3 3,2 GHz-es @ |28 |273,982 |6,069 |
| Standard h16mr méretű |16 |2 |Intel Xeon E5-2667 v3 3,2 GHz-es @ |28 |274,523 |5,698 |

## <a name="about-coremark"></a>CoreMark kapcsolatos
Linux-számok futtatásával volt számított [CoreMark](http://www.eembc.org/coremark/faq.php) az Ubuntu. CoreMark lett konfigurálva a virtuális processzorok számának beállítása szálak számát, és feldolgozási PThreads értékre. A cél az ismétlések száma alapján várt teljesítményét, és adja meg a legyen legalább 20 másodperc (általában sokkal hosszabb) futtatókörnyezettel lett igazítva. A végleges pontszám elosztja a másodpercig tartott a a teszt futtatása befejeződött ismétlések számát jelöli. Minden teszt virtuális gépek legalább hét alkalommal volt futtatva. Teszteli (kivéve a H – series_ minden nyilvános Azure-régiót több virtuális a 2015 október futtassa a virtuális gép támogatta a Futtatás időpontjától.

## <a name="next-steps"></a>Következő lépések
* Tárolási kapacitás, a lemez adatai és Virtuálisgép-méretek között további szempontokról, lásd: [virtuális gépek méretei](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* A CoreMark-parancsfájlok futtathatók a Linux virtuális gépek, le kell töltenie a [parancsfájl pack CoreMark](http://download.microsoft.com/download/3/0/5/305A3707-4D3A-4599-9670-AAEB423B4663/AzureCoreMarkScriptPack.zip).

