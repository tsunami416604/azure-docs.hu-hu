---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 77fc95c53ba89c3905ebd1aec785e22f42339369
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165884"
---
<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Storsimple-höz készült Windows Powershellen keresztül rendszeres gyorsjavítások telepítése
1. Az eszköz soros konzoljához való csatlakozáshoz. További információkért lásd: [1. lépés: csatlakozás soros konzolon](../articles/storsimple/storsimple-update-device.md#step1).
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

