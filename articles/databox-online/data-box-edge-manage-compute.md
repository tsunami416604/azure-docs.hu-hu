---
title: Az Azure Data Box Edge számítási felügyeleti |} A Microsoft Docs
description: Ismerteti, hogyan lehet például az eseményindítót, a modulok, a számítási konfiguráció megtekintése az Edge számítási beállítások kezelése, távolítsa el az Azure Portalon az Azure Data Box Edge-konfigurációt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/24/2019
ms.author: alkohli
ms.openlocfilehash: de8ddd0d2886cd7798160e830094b295c62e17e9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400681"
---
# <a name="manage-compute-on-your-azure-data-box-edge"></a>Az Azure Data Box Edge számítások kezelése

Ez a cikk ismerteti az Azure Data Box Edge számítások kezelése. Az Azure Portalon a számítások kezelése, vagy keresztül a helyi webes felhasználói Felületét. Az Azure portal használata kezelheti a modulok, eseményindítók, és a számítási konfiguráció és a helyi webes felületén számítási beállítások kezelését.

> [!IMPORTANT]
> A Data Box Edge előzetes verzióban érhető el. A megoldás megrendelése és üzembe helyezése előtt tekintse át az [Azure előzetes verziókra vonatkozó szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Eseményindítók kezelése
> * A számítási konfiguráció kezelése


## <a name="manage-triggers"></a>Eseményindítók kezelése

Események akkor fordulhat elő, a felhőalapú környezetben, illetve az eszközön, előfordulhat, hogy a művelet végrehajtása a kívánt dolgot. Például amikor létrejön egy fájl olyan megosztáson található, egy eseményt. Triggerek olyan események választ. Eseményindítók segíthet végrehajthat egy függvényt, amikor az esemény lesz elindítva. A Data Box Edge eseményindítók fájl eseményekre vagy ütemezés szerint is lehet.

- **Fájl**: Nagyszerűek ezek fájl eseményeihez, például a létrehozott fájl, a fájl módosítása.
- **Ütemezett**: Nagyszerűek ezek az ütemezés szerint határozhatja meg a kezdő dátum, kezdési ideje és a gyakorisági adott válaszként.


### <a name="add-a-trigger"></a>Adjon hozzá egy triggert

A következő lépésekkel hozhat létre egy eseményindítót az Azure Portalon.

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrás, és folytassa a **Edge számítási > eseményindító**. Válassza ki **+ Hozzáadás eseményindító** a parancssávon.

    ![Válassza az eseményindító hozzáadása](media/data-box-edge-manage-compute/add-trigger-1.png)

2. A **Hozzáadás eseményindító** panelen adjon meg egy egyedi nevet az eseményindító.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Válassza ki a **típus** az eseményindító. Válasszon **fájl** Ha a trigger a fájl esemény bekövetkeztekor történik. Válassza ki **ütemezett** Ha azt szeretné, hogy az eseményindítót egy meghatározott időpontban kezdődik el, és ismételje meg a megadott időközönként futtatásához. A választástól függően különböző beállítási lehetőségek egyike jelenik meg.

    - **Fájl eseményindító** – a legördülő listából válasszon egy csatlakoztatott megosztás. Ha egy fájl esemény lesz elindítva, a megosztásban található, az eseményindító lenne az Azure-függvény.

        ![SMB-megosztás hozzáadása](media/data-box-edge-manage-compute/add-file-trigger.png)

    - **Ütemezett eseményindító** – adja meg a kezdő dátum és időpont, és az ismétlési időköz (óra), perc és másodperc. Emellett adja meg a témakör nevét. A témakör lesz a rugalmasságot biztosítanak az eseményindító útvonalat az eszközön telepített modul.

        Egy példa útvonal-karakterlánc: `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"`.

        ![NFS-megosztás hozzáadása](media/data-box-edge-manage-compute/add-scheduled-trigger.png)

4. Válassza ki **Hozzáadás** az eseményindító létrehozása. Egy értesítés jeleníti meg, hogy az eseményindító létrehozása folyamatban van. Az eseményindító létrehozása után a panel frissül az új eseményindítót.
 
    ![Frissített alkalmazásindító-listája](media/data-box-edge-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Trigger törlése

Az alábbi lépéseket egy trigger törlése az Azure Portalon.

1. Eseményindítók listájában jelölje ki az eseményindítót, amelyet törölni szeretne.

    ![Trigger kiválasztása](media/data-box-edge-manage-compute/add-trigger-1.png)

2. Kattintson a jobb gombbal, majd **törlése**.

    ![Válassza a Törlés](media/data-box-edge-manage-compute/add-trigger-1.png)

3. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.

    ![Törlés megerősítése](media/data-box-edge-manage-compute/add-trigger-1.png)

A törlés eseményindítók frissítések listája.

## <a name="manage-compute-configuration"></a>A számítási konfiguráció kezelése

Használja az Azure Portalon, a számítási konfiguráció megtekintéséhez, egy meglévő számítási konfiguráció eltávolításához, vagy a frissítés a számítási konfiguráció szinkronizálhatja az IoT eszköz és az IoT Edge-eszköz hozzáférési kulcsait, az a Data Box Edge-hez.

### <a name="view-compute-configuration"></a>Számítási konfiguráció megtekintése

Az alábbi lépéseket a számítási konfigurációt, az eszköz megtekintése az Azure Portalon.

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrás, és folytassa a **Edge számítási > modulok**. Válassza ki **számítási megtekintése** a parancssávon.

    ![Válassza ki a számítási megtekintése](media/data-box-edge-manage-compute/view-compute-1.png)

2. Jegyezze fel a számítási konfiguráció az eszközön. Ha konfigurálta a számítási, létrehozott egy IoT Hub-erőforrást. Egy IoT-eszköz és az IoT Edge-eszköz, hogy az IoT Hub erőforrás alatt vannak konfigurálva. Az IoT Edge-eszközön futtassa, csak a Linux-modulok támogatottak.

    ![Konfiguráció megtekintése](media/data-box-edge-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>Számítási konfiguráció eltávolítása

Az alábbi lépéseket a meglévő Edge számítási konfigurációt az eszköz eltávolítása az Azure Portalon.

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrás, és folytassa a **Edge számítási > első lépések**. Válassza ki **távolítsa el a számítási** a parancssávon.

    ![Válassza ki a számítási eltávolítása](media/data-box-edge-manage-compute/remove-compute-1.png)

2. Ha eltávolítja a számítási konfiguráció, szüksége lesz az eszköz újrakonfigurálása, abban az esetben, ha szeretné újra használni számítási. Amikor a rendszer megerősítést kér, válassza ki a **Igen**.

    ![Válassza ki a számítási eltávolítása](media/data-box-edge-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Hozzáférési kulcsok IoT-eszköz és az IoT Edge-eszköz szinkronizálása

A Data Box Edge számítási konfigurálásakor IoT-eszközökön és a egy IoT Edge-eszköz jönnek létre. Ezek az eszközök automatikusan hozzárendeli a szimmetrikus tárelérési kulcsok. Bevált biztonsági gyakorlat ezek a kulcsok rendszeresen vannak-e forgatni keresztül az IoT Hub szolgáltatást.

Ezek a kulcsok rotálására, keresse meg az Ön által létrehozott IoT Hub szolgáltatást, és válassza ki az IoT-eszköz vagy az IoT Edge-eszköz. Minden eszköz rendelkezik az elsődleges elérési kulcs és egy másodlagos hívóbetűit. Az elsődleges elérési kulcs hozzárendelése a másodlagos elérési kulcsot, és ezután újragenerálja az elsődleges elérési kulcsát.

Ha az IoT-eszköz és az IoT Edge-eszköz kulcsok elforgatta majd meg kell a Data Box Edge a legújabb hozzáférési kulcsainak lekérése beállításainak frissítése. A szinkronizálás segít az eszköz a legújabb kulcsok lekérése az IoT-eszköz és az IoT Edge-eszköz. Data Box Edge csak az elsődleges elérési kulcsokat használ.

Az alábbi lépéseket az eszköz számára a hozzáférési kulcsok szinkronizálása az Azure Portalon.

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrás, és folytassa a **Edge számítási > első lépések**. Válassza ki **frissítési konfiguráció** a parancssávon.

    ![Válassza ki a frissítési konfiguráció](media/data-box-edge-manage-compute/refresh-configuration-1.png)

2. Válassza ki **Igen** , amikor a rendszer megerősítést kér.

     ![Amikor a rendszer kéri válassza az Igen lehetőséget](media/data-box-edge-manage-compute/refresh-configuration-2.png)

3. A szinkronizálást követően zárja be a párbeszédpanelt.

## <a name="enable-a-network-interface-for-compute"></a>Egy hálózati adapter számítási engedélyezése

Szükség lehet a Data Box peremhálózati eszközön futó modul eléréséhez. A modul el kívülről, szüksége lesz egy IP-cím hozzárendelése egy hálózati adapter az eszközön. Ezek kezelheti számítási beállításokat a helyi webes felületén.

Az alábbi lépéseket a helyi webes felületén számítási beállítások konfigurálása.

1. A helyi webes felhasználói felületen váltson **Configuration > Számítási beállítások**.  

2. **Engedélyezése** a hálózati adapter, amelyet szeretne csatlakozni a számítási modulokat az eszközön. 

    - Ha statikus IP-címeket használ, adja meg a hálózati adapter IP-címet.
    - Ha a DHCP használatával, majd az IP-címek lesz automatikusan hozzárendelve.

3. Válassza ki **alkalmaz** a alkalmazni a beállításokat.

    ![Compute-beállítások engedélyezése](media/data-box-edge-manage-compute/compute-settings-1.png)


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [kezelheti a felhasználókat az Azure Portalon keresztül](data-box-edge-manage-users.md).
