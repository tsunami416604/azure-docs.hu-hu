---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179151"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Rendszeres gyorsjavítások telepítése a StorSimple-hez szervizelt Windows PowerShell rendszeren keresztül
1. Csatlakozzon az eszköz soros konzoljához. További információt az [1.](../articles/storsimple/storsimple-update-device.md#step1)
2. A soros konzol menüjében **Log in with full access**válassza az 1. Írja be a jelszót. Az alapértelmezett jelszó a **Password1**.
3. A parancssorba írja be a következőt:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Ez a parancs csak a normál gyorsjavításokra vonatkozik. Ezt a parancsot csak egy vezérlőn futtatja, de mindkét vezérlő frissülni fog.
    > Előfordulhat, hogy a frissítési folyamat során vezérlőfeladat-átvételt észlel; a feladatátvétel azonban nem befolyásolja a rendszer rendelkezésre állását vagy működését.

4. Amikor a rendszer kéri, adja meg a gyorsjavításfájlokat tartalmazó megosztott hálózati mappa elérési útját.
5. A rendszer a művelet megerősítését kéri. Írja be az **Y** típust a gyorsjavítás telepítésének folytatásához.

