---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2f847cfe4e0097cdb6f70e8831e15e602b5e9ac6
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165395"
---
<!--author=alkohli last changed: 01/20/17-->


#### <a name="to-add-a-storage-account-credential-in-the-same-azure-subscription-as-the-storsimple-device-manager-service"></a>Tárfiók hitelesítő adatainak hozzáadása a StorSimple-eszközkezelővel meggyező Azure-előfizetésben

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. A **Konfigurálás** szakaszban kattintson a **Tárfiók hitelesítő adatai** elemre.

    ![Tárfiók hitelesítő adatai](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct1.png)

2. A **Tárfiók hitelesítő adatai** panelen kattintson a **+Hozzáadás** elemre.

    ![Tárfiók hitelesítő adatainak hozzáadása](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct2.png)

3. A **Tárfiók hitelesítő adatainak hozzáadása** panelen tegye a következőket:

    1. Ha ugyanahhoz az Azure-előfizetéshez szeretne tárfiók-hitelesítő adatokat hozzáadni, amelyhez a szolgáltatás is tartozik, győződjön meg arról, hogy az **Aktuális** elem van kiválasztva.

    2. Válasszon ki egy meglévő tárfiókot a **tárfiókok** legördülő listájából.

    3. A kiválasztott tárfiók alapján a rendszer megjeleníti a **helyet** (kiszürkítve jelenik meg, és itt nem módosítható).

    4. Válassza az **SSL-mód engedélyezése** lehetőséget, ha egy biztonságos csatornát szeretne létrehozni az eszköz és a felhő közötti hálózati kommunikációhoz. Csak akkor tiltsa le az **SSL engedélyezése** elemet, ha magánfelhőben tevékenykedik.

        ![Tárfiók-hitelesítő adatok hozzáadása panel](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct3.png)

    5. Kattintson a **Hozzáadás** elemre, amellyel elindítja a tárfiók-hitelesítő adatokhoz tartozó feladat létrehozását. Értesítést kap, ha a tárfiók-hitelesítő adatok sikeresen létrejöttek.

        ![Tárfiók-hitelesítő adatok – sikeres értesítés](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct5.png)

Az újonnan létrehozott tárfiók-hitelesítő adatok megjelennek a **Tárfiók hitelesítő adatai** listában.

![Tárfiók hitelesítő adatainak listája](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct6.png)

