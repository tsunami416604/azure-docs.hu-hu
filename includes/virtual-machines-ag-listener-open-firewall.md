---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179065"
---
Ebben a lépésben hozzon létre egy tűzfalszabályt a kiegyenlítő végpont mintavételi portjának megnyitásához (59999, a hogy korábban megadott) és egy másik szabályt a rendelkezésre állási csoport figyelőportjának megnyitásához. Mivel létrehozta a terhelés-kiegyensúlyozott végpontot a virtuális gépek, amelyek rendelkezésre állási csoport replikák, meg kell nyitnia a mintavételi port és a figyelő port a megfelelő virtuális gépeken.

1. A replikákat tartalmazó virtuális gépeken indítsa el **a fokozott biztonságú Windows tűzfalat.**

2. Kattintson a jobb gombbal **a Bejövő szabályok**elemre, majd kattintson az Új szabály **parancsra.**

3. A **Szabály típusa** lapon válassza a **Port**lehetőséget, majd kattintson a **Tovább**gombra.

4. A **Protokoll és portok** lapon válassza a **TCP**lehetőséget, írja be az **59999-es** négyzetet a **Helyi portok** mezőbe, majd kattintson a **Tovább**gombra.

5. A **Művelet** lapon tartsa a **Kapcsolat engedélyezése** lehetőséget, majd kattintson a **Tovább**gombra.

6. A **Profil** lapon fogadja el az alapértelmezett beállításokat, majd kattintson a **Tovább**gombra.

7. A **Név** lap **Név** mezőjében adja meg a szabály nevét, például **Mindig a figyelő mintavételi portján,** majd kattintson a **Befejezés gombra.**

8. Ismételje meg az előző lépéseket a rendelkezésre állási csoport figyelőportjához (a parancsfájl $EndpointPort paraméterének korábbi meghatározása szerint), majd adja meg a megfelelő szabálynevet, például **mindig a figyelőporton.**

