---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d656d756759c997972eb034e194355185be93e1a
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226915"
---
Ezután, ha olyan kiszolgálót sem a fürtön futnak, a Windows Server 2008 R2 vagy Windows Server 2012, ellenőriznie kell, hogy a gyorsjavítási [KB2854082](http://support.microsoft.com/kb/2854082) telepítve van a helyszíni kiszolgálók és az Azure virtuális gépek a fürt részét képező. Bármely kiszolgáló vagy a virtuális Gépet, amely a fürt, de nem a rendelkezésre állási csoport is kell rendelkeznie a gyorsjavítás telepítve van.

Töltse le a távoli asztali munkamenetet az egyes fürtcsomópontok [KB2854082](http://support.microsoft.com/kb/2854082) egy helyi könyvtárba. Ezután telepítse a gyorsjavítást a fürt minden csomópontján egymás után. Ha a fürtcsomópont jelenleg fut a fürtszolgáltatás, a kiszolgáló újraindítása a gyorsjavítás telepítése végén.

> [!WARNING]
> A fürtszolgáltatás leállítása, vagy a kiszolgáló újraindítása hatással van a kvórum a fürt és a rendelkezésre állási csoport állapotát, és előfordulhat, hogy a fürt offline állapotba. A magas rendelkezésre állás, a fürt fenntartása a telepítés során, ellenőrizze, hogy:
> 
> * A fürt kvórum optimális egészségügyi szerepel. 
> * A gyorsjavítás telepítése bármely csomópontján, mielőtt a fürt összes csomópontján online üzemmódban.
> * A gyorsjavítás a fürt többi csomópontjára telepíti, mielőtt lehetővé teszik a gyorsjavítás telepítése műveleteinek végrehajtása egy csomóponton, beleértve a teljes mértékben a kiszolgáló újraindítása.
> 
> 

