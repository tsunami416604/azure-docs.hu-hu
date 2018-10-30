---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226614"
---
Ebben a lépésben hoz létre egy tűzfalszabályt, nyissa meg a mintavételi portot az elosztott terhelésű végpont (korábban megadott 59999), és a egy másik szabály megnyitása a rendelkezésre állási csoport figyelőjének portszámára. Az elosztott terhelésű végpontot a virtuális gépeken, amelyek tartalmazzák a rendelkezésre állási csoport replikái hozta létre, mert meg kell nyitnia a mintavételi portot és a megfelelő virtuális gépeken a figyelőjének portszámára.

1. Indítsa el a virtuális gépeken, amely a replikákat, **fokozott biztonságú Windows tűzfal**.

2. Kattintson a jobb gombbal **bejövő szabályok**, és kattintson a **új szabály**.

3. Az a **szabálytípus** lapon jelölje be **Port**, és kattintson a **tovább**.

4. Az a **protokoll és portok** lapon jelölje be **TCP**, típus **59999** a a **adott helyi portok** mezőbe, majd kattintson a  **Tovább**.

5. Az a **művelet** lapon, hogy **engedélyezze a kapcsolatot** kiválasztva, és kattintson **tovább**.

6. Az a **profil** lapon fogadja el az alapértelmezett beállításokat, és kattintson a **tovább**.

7. Az a **neve** lap a **neve** szöveget adja meg a szabály nevét, például **mindig a figyelő mintavételi Port**, és kattintson a **Befejezés**.

8. Ismételje meg az előző lépést a rendelkezésre állási csoport figyelőjének portszámára (a korábban a parancsfájl $EndpointPort paraméterében megadott), majd adjon meg egy megfelelő szabály nevét, például **mindig a figyelőport**.

