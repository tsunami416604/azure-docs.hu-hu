---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 28aab15dc67e051190e8d4e35e92240a56fe54a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179067"
---
Ezután, ha a fürt bármelyik kiszolgálóján Windows Server 2008 R2 vagy Windows Server 2012 fut, ellenőriznie kell, hogy a [KB2854082](https://support.microsoft.com/kb/2854082) gyorsjavítás telepítve van-e a fürt höz tartozó minden helyszíni kiszolgálóra vagy Azure-szolgáltatásra. A fürtben található, de a rendelkezésre állási csoportban nem szereplő kiszolgálóknak vagy virtuális gépeknek is telepítve kell lennie ennek a gyorsjavításnak.

Az egyes fürtcsomópontok távoli asztali munkamenetében töltse le a [KB2854082 kb.](https://support.microsoft.com/kb/2854082) Ezután telepítse a gyorsjavítást minden fürtcsomópontra egymás után. Ha a fürtszolgáltatás jelenleg fut a fürtcsomóponton, a kiszolgáló a gyorsjavítás telepítésének végén újraindul.

> [!WARNING]
> A fürtszolgáltatás leállítása vagy a kiszolgáló újraindítása hatással van a fürt és a rendelkezésre állási csoport kvórumállapotára, és a fürt offline állapotba hozó állapotát okozhatja. A fürt magas rendelkezésre állásának fenntartása érdekében a telepítés során győződjön meg arról, hogy:
> 
> * A fürt optimális kvórumállapotban van. 
> * Mielőtt bármelyik csomópontra telepítené a gyorsjavítást, az összes fürtcsomópont online állapotban van.
> * Mielőtt a gyorsjavítást a fürt bármely más csomópontjára telepítené, engedélyezze, hogy a gyorsjavítás telepítése egy csomóponton befejeződjen, beleértve a kiszolgáló teljes újraindítását is.
> 
> 

