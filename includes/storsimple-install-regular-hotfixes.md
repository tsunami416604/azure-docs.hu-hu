---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55888700"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Storsimple-höz készült Windows Powershellen keresztül rendszeres gyorsjavítások telepítése
1. Az eszköz soros konzoljához való csatlakozáshoz. További információkért lásd: [1. lépés: Csatlakozás soros konzolon](../articles/storsimple/storsimple-update-device.md#step1).
2. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes hozzáféréssel**. Írja be a jelszót. Az alapértelmezett jelszó az **jelszó1**.
3. A parancssorba írja be a következőt:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Ez a parancs csak a normál gyorsjavítások vonatkozik. Ez a parancs csak egy tartományvezérlőn futtatja, de mindkét vezérlő frissülni fog.
    > A frissítés során; előfordulhat, hogy figyelje meg a vezérlő feladatátvétele a feladatátvétel azonban rendszer rendelkezésre állását vagy művelet nincs hatással.

4. Amikor a rendszer kéri, adja meg a hálózati megosztott mappa, amely tartalmazza a gyorsjavítások fájljainak elérési útját.
5. Meg kell adnia megerősítést. Típus **Y** folytatásához a gyorsjavítás telepítését.

