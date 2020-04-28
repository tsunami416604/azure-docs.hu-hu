---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 28aab15dc67e051190e8d4e35e92240a56fe54a6
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "67179067"
---
Ha a fürt bármely kiszolgálója Windows Server 2008 R2 vagy Windows Server 2012 rendszert futtat, ellenőriznie kell, hogy a gyorsjavítás [KB2854082](https://support.microsoft.com/kb/2854082) telepítve van-e a fürt részét képező helyszíni kiszolgálókon vagy Azure-beli virtuális gépeken. A fürtben lévő minden olyan kiszolgálót vagy virtuális gépet, amely a rendelkezésre állási csoportban nem található, a gyorsjavítást is telepíteni kell.

Az egyes fürtcsomópontok távoli asztali munkamenetében töltse le a [KB2854082](https://support.microsoft.com/kb/2854082) egy helyi könyvtárba. Ezután telepítse a gyorsjavítást mindegyik fürtcsomóponton. Ha a fürtszolgáltatás jelenleg a fürtcsomóponton fut, a kiszolgáló a gyorsjavítások telepítésének végén újraindul.

> [!WARNING]
> A fürtszolgáltatás leállítása vagy a kiszolgáló újraindítása hatással van a fürt Kvórumának állapotára és a rendelkezésre állási csoportra, és előfordulhat, hogy a fürt offline állapotba kerül. A fürt magas rendelkezésre állásának fenntartása a telepítés során győződjön meg arról, hogy:
> 
> * A fürt optimális kvórum állapotú. 
> * Mielőtt telepítené a gyorsjavítást bármely csomópontra, a fürt összes csomópontja online állapotú.
> * Mielőtt telepítené a gyorsjavítást a fürt bármely más csomópontjára, engedélyezze, hogy a gyorsjavítás telepítése az egyik csomóponton fusson, beleértve a kiszolgáló teljes újraindítását is.
> 
> 

