---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 28aab15dc67e051190e8d4e35e92240a56fe54a6
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262919"
---
Ezután, ha olyan kiszolgálót sem a fürtön futnak, a Windows Server 2008 R2 vagy Windows Server 2012, ellenőriznie kell, hogy a gyorsjavítási [KB2854082](https://support.microsoft.com/kb/2854082) telepítve van a helyszíni kiszolgálók és az Azure virtuális gépek a fürt részét képező. Bármely kiszolgáló vagy a virtuális Gépet, amely a fürt, de nem a rendelkezésre állási csoport is kell rendelkeznie a gyorsjavítás telepítve van.

Töltse le a távoli asztali munkamenetet az egyes fürtcsomópontok [KB2854082](https://support.microsoft.com/kb/2854082) egy helyi könyvtárba. Ezután telepítse a gyorsjavítást a fürt minden csomópontján egymás után. Ha a fürtcsomópont jelenleg fut a fürtszolgáltatás, a kiszolgáló újraindítása a gyorsjavítás telepítése végén.

> [!WARNING]
> A fürtszolgáltatás leállítása, vagy a kiszolgáló újraindítása hatással van a kvórum a fürt és a rendelkezésre állási csoport állapotát, és előfordulhat, hogy a fürt offline állapotba. A magas rendelkezésre állás, a fürt fenntartása a telepítés során, ellenőrizze, hogy:
> 
> * A fürt kvórum optimális egészségügyi szerepel. 
> * A gyorsjavítás telepítése bármely csomópontján, mielőtt a fürt összes csomópontján online üzemmódban.
> * A gyorsjavítás a fürt többi csomópontjára telepíti, mielőtt lehetővé teszik a gyorsjavítás telepítése műveleteinek végrehajtása egy csomóponton, beleértve a teljes mértékben a kiszolgáló újraindítása.
> 
> 

