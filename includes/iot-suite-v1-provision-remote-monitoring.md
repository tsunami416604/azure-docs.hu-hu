---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 05/30/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 4224575644e754724d2129f101aed6b9e4b54b65
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34670166"
---
## <a name="provision-the-solution"></a>A megoldás megvalósítása

Ha még nem építette ki az előre konfigurált távoli figyelési megoldást a fiókban:

1. Jelentkezzen be az [azureiotsuite.com][lnk-azureiotsuite] címre az Azure-fiókja hitelesítő adataival, majd kattintson a **+** elemre egy megoldás létrehozásához.
2. Kattintson a **Kiválasztás** elemre a **Távoli figyelés** csempén.
3. Adja meg a **Megoldásnevet** az előre konfigurált távoli figyelési megoldáshoz.
4. Válassza ki a megoldás kiépítéséhez használni kívánt **Régiót** és **Előfizetést**.
5. Kattintson a **Megoldás létrehozása** gombra a kiépítés elkezdéséhez. Ez a folyamat általában több percig tart.

### <a name="wait-for-the-provisioning-process-to-complete"></a>Várjon, amíg a kiépítési folyamat befejeződik
1. Kattintson a megoldás **Kiépítési** állapotát jelző csempére.
2. Megtekintheti a **Kiépítési állapotokat**, miközben az Azure-szolgáltatások telepítése megtörténik az Azure-előfizetésben.
3. A kiépítés befejezése után az állapot **Kész** értékre változik.
4. Kattintson a csempére, és a jobb oldali panelen láthatja a megoldás részleteit.

> [!NOTE]
> Ha problémái vannak az előre konfigurált megoldás telepítésekor, tekintse meg az [Engedélyek az azureiotsuite.com webhelyen][lnk-permissions] és a [GYIK][lnk-faq] fejezetet. Ha a problémák továbbra is fennállnak, hozzon létre egy szolgáltatásjegyet a [portálon][lnk-portal].
> 
> 

Hiányol bizonyos részleteket a megoldásával kapcsolatban? A [felhasználói visszajelzési webhelyen](https://feedback.azure.com/forums/321918-azure-iot) elküldheti a szolgáltatásokkal kapcsolatos javaslatait.

[lnk-azureiotsuite]: https://www.azureiotsolutions.com
[lnk-permissions]: ../articles/iot-suite/iot-suite-v1-permissions.md
[lnk-portal]: http://portal.azure.com/
[lnk-faq]: ../articles/iot-suite/iot-suite-v1-faq.md