---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67179065"
---
Ebben a lépésben létrehoz egy tűzfalszabályet, amely megnyitja a mintavételi portot a elosztott terhelésű végponthoz (59999), és egy másik szabályt a rendelkezésre állási csoport figyelő portjának megnyitásához. Mivel az elosztott terhelésű végpontot a rendelkezésre állási csoport replikáit tartalmazó virtuális gépeken hozta létre, meg kell nyitnia a mintavételi portot és a figyelő portot a megfelelő virtuális gépeken.

1. A replikákat futtató virtuális gépeken indítsa el a **fokozott biztonságú Windows tűzfalat**.

2. Kattintson a jobb gombbal a **Bejövő szabályok**elemre, majd kattintson az **új szabály**elemre.

3. A **szabály típusa** lapon válassza a **port**lehetőséget, majd kattintson a **tovább**gombra.

4. A **protokollok és portok** lapon válassza a **TCP**lehetőséget, írja be a **59999** értéket az **adott helyi portok** mezőbe, majd kattintson a **tovább**gombra.

5. A **művelet** lapon tartsa be **a kapcsolat lehetőséget** , majd kattintson a **tovább**gombra.

6. A **profil** lapon fogadja el az alapértelmezett beállításokat, majd kattintson a **tovább**gombra.

7. A **név** lapon, a **név** szövegmezőben adja meg a szabály nevét, például az **Always On Listener mintavételi portot**, majd kattintson a **Befejezés**gombra.

8. Ismételje meg az előző lépéseket a rendelkezésre állási csoport figyelő portjához (a parancsfájl $EndpointPort paraméterében megadott módon), majd adjon meg egy megfelelő nevet, például **a figyelő portját**.

