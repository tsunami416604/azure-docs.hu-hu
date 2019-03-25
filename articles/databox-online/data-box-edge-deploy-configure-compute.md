---
title: Adatok átalakítása az Azure Data Box Edge segítségével | Microsoft Docs
description: Megismerheti, hogyan konfigurálhatja a számítási szerepköröket a Data Box Edge-ben, és hogyan alakíthatja át az adatokat, mielőtt beküldené őket az Azure-nak.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 31911c124aeafecb8ee37d14e58d3a0bdc0d4955
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400748"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Oktatóanyag: Adatátalakítás az Azure Data Box Edge szolgáltatással

Ez az oktatóanyag leírja a számítási szerepkör konfigurálása az Azure Data Box Edge-eszközön. Miután konfigurálta a számítási szerepkör, a Data Box Edge is mielőtt elküldené az Azure-bA adatok átalakítása.

Ez az eljárás befejezéséhez körülbelül 10 – 15 percet is eltarthat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Megosztások hozzáadása
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

 
## <a name="prerequisites"></a>Előfeltételek

A Data Box Edge-eszközön beállítása egy számítási szerepkört, előtt ellenőrizze, hogy:

- Miután aktiválta a Data Box peremhálózati eszköz, leírtak szerint [kapcsolódás beállítása, és aktiválja az Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Számítás konfigurálása

Számítás konfigurálása a Data Box Edge, hozzon létre egy IoT Hub-erőforrást.

1. Az Azure Portalon, a Data Box Edge erőforrás Ugrás az Áttekintéshez. A jobb oldali ablaktáblában a a **számítási** csempéről válassza **Ismerkedés**.

    ![Compute – első lépések](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. Az a **konfigurálása Edge számítási** csempéről válassza **számítás konfigurálása**.
3. Az a **konfigurálása Edge számítási** panelen adjon meg a következőket:

   
    |Mező  |Érték  |
    |---------|---------|
    |IoT Hub     | Válassza ki a **új** vagy **meglévő**. <br> Alapértelmezés szerint egy Standard szintű (S1) segítségével hozzon létre egy IoT-erőforrást. Egy ingyenes IoT erőforrás használatához hozzon létre egyet, és válassza ki a meglévő erőforrást. <br> Minden esetben az IoT Hub-erőforrást használ, ugyanazt az előfizetést és erőforráscsoportot, amelyet a Data Box Edge erőforrás.     |
    |Name (Név)     |Adja meg az IoT Hub-erőforrás nevét.         |

    ![Compute – első lépések](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Kattintson a **Létrehozás** gombra. Az IoT Hub erőforrás-létrehozás néhány percet vesz igénybe. Az IoT Hub-erőforrás létrehozása után a **számítás konfigurálása** csempe megjelenítése a számítási konfiguráció frissítéseit. Győződjön meg arról, hogy az Edge számítási szerepkör konfigurálva van-e, jelölje be **nézet számítási** a a **számítás konfigurálása** csempére.
    
    ![Compute – első lépések](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    Az Edge-eszközön a peremhálózati számítási szerepkör be van állítva, ha két eszközt hoz létre: egy IoT-eszköz és az IoT Edge-eszköz. Mindkét eszköz az IoT Hub erőforrás lehet megtekinteni. Az IoT Edge-futtatókörnyezet is fut az IoT Edge-eszközön. Ezen a ponton csak a Linux rendszeren érhető el az IoT Edge-eszköz.


## <a name="add-shares"></a>Megosztások hozzáadása

Ebben az oktatóanyagban egyszerűbb üzembe helyezéshez, szüksége lesz a két megosztások: egy Edge-megosztást, és a egy másik Edge helyi megosztás.

1. Az Edge-megosztás hozzáadása az eszközön a következő lépések végrehajtásával:

    1. A Data Box Edge erőforrás, lépjen a **Edge számítási > első lépések**.
    2. Az a **megosztás(ok) hozzáadása** csempéről válassza **Hozzáadás**.
    3. Az a **Hozzáadás megosztás** panelen adja meg a megosztás nevét, és válassza ki a megosztást.
    4. A peremhálózati megosztás csatlakoztatásához, válassza ki a jelölőnégyzetet **megosztás használatára az Edge számítási**.
    5. Válassza ki a **tárfiók**, **társzolgáltatás**, egy meglévő felhasználót, és válassza ki **létrehozás**.

        ![Az Edge-megosztás hozzáadása](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Ha létrehozott egy helyi NFS-megosztások, a következő távoli szinkronizálási (rsync) a kapcsoló használatával másolja a fájlokat a megosztás:

    `rsync <source file path> < destination file path>`

    A rsync paranccsal kapcsolatos további információkért lépjen [Rsync dokumentáció](https://www.computerhope.com/unix/rsync.htm).

    A peremhálózati megosztás létrehozása, és a egy sikeres létrehozás értesítést kap. A megosztási listán frissülhet, de meg kell várnia a fájlmegosztás létrehozásának hajtható végre.

2. Az Edge helyi megosztás hozzáadása az Edge-eszközön szerint ismételje meg az előző lépésben található összes lépést, és jelölje be a jelölőnégyzetet a **Edge helyi megosztásnak konfigurálása**. Az adatok a helyi megosztáson marad az eszközön.

    ![Az Edge helyi megosztás hozzáadása](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Válassza ki **megosztás(ok) hozzáadása** megosztások frissített listájának megtekintéséhez.

    ![Megosztások frissített listája](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Modul hozzáadása

Hozzáadhat egy egyéni vagy egy előre elkészített modul. Nincsenek nem egyéni modulok az Edge-eszközön. Ismerje meg, hogyan hozhat létre egy egyéni modult, lépjen a [Develop egy C# modul a Data Box Edge-eszköz](data-box-edge-create-iot-edge-module.md).

Ebben a szakaszban ad hozzá egy egyéni modult, amelyet az IoT Edge-eszköz [Develop egy C# készült a Data Box Edge-modul](data-box-edge-create-iot-edge-module.md). Ez a modul egyéni szükséges fájlokat-Edge helyi megosztásból az Edge-eszközön, és az eszközön az Edge (felhő) megosztások helyezi őket. A felhőbeli fájlmegosztás majd leküldi a fájlokat a felhőbeli fájlmegosztás társított Azure storage-fiókban.

1. Lépjen a **Edge számítási > első lépések**. Az a **modulok hozzáadása** csempére, válassza ki a forgatókönyv szerint **egyszerű**. Válassza a **Hozzáadás** lehetőséget.
2. Az a **konfigurálása, és adja hozzá a modul** panelen adjon meg a következő értékeket:

    
    |Mező  |Érték  |
    |---------|---------|
    |Name (Név)     | A modul egyedi neve. Ez a modul egy docker-tároló, amely központilag telepíthető az IoT Edge-eszköz, amely a Data Box Edge van társítva.        |
    |Lemezkép URI-ja     | A kép URI-t a megfelelő tárolórendszerképet a modul.        |
    |Szükséges hitelesítő adatok     | Ha be van jelölve, felhasználónév és jelszó segítségével modulok egy megfelelő URL-cím lekéréséhez.        |
    |A bemeneti megosztás     | Válasszon egy bemeneti megosztást. Az Edge helyi megosztás ebben az esetben a bemeneti megosztás. Az itt használt modul helyezi át fájlokat az Edge helyi megosztás egy Edge-megosztáshoz, azok a rendszer feltölti a felhőbe.        |
    |Kimeneti megosztás     | Válasszon egy kimeneti megosztást. Az Edge megosztás ebben az esetben a kimenő megosztás.        |
    |Trigger típusa     | Válasszon a **fájl** vagy **ütemezés**. Egy fájl eseményindítót, amikor egy fájl esemény bekövetkeztekor, mint például egy fájl a bemeneti megosztás íródik. Egy ütemezett eseményindítót fel az Ön által megadott ütemezés alapján.         |
    |Trigger neve     | Az eseményindító egyedi név.         |
    |Környezeti változók| Opcionális információk segítségével határozza meg, hogy a környezet, amelyben a modul futni fog.   |

    ![Felveheti és konfigurálhatja a modul](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Válassza a **Hozzáadás** lehetőséget. A modul hozzáadása. A **Hozzáadás modul** csempe frissül, jelezve, hogy a modul telepítve van. 

    ![A modul telepítése](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Adatok átalakításának ellenőrzése és adatok átvitele

Az utolsó lépés, hogy győződjön meg arról, hogy a modul csatlakoztatva és fut a várt módon. A modul futtatási állapotát az IoT Edge-eszköz az IoT Hub erőforrás kell futnia.

Győződjön meg arról, hogy a modul fut, tegye a következőket:

1. Válassza ki a **Hozzáadás modul** csempére. Ezzel továbblép a **modulok** panelen. A modulok listájához azonosítsa a telepített modul. A hozzáadott modul futási állapotát kell *futó*.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  A Fájlkezelőben kapcsolódni a helyi Edge és a korábban létrehozott Edge megosztások.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Adja hozzá az adatokat a helyi megosztáshoz.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    Az adatok áthelyeződnek a felhőalapú megosztásba.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    Az adatok a felhő megosztásból majd leküldve a storage-fiókhoz. Az adatok megtekintéséhez nyissa meg a Storage Explorerben.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
Az ellenőrzési folyamat befejezte.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Megosztások hozzáadása
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

Ismerje meg, hogyan felügyelheti a Data Box peremhálózati eszköz, tekintse meg:

> [!div class="nextstepaction"]
> [A Data Box Edge felügyelete a helyi webes felhasználói felületen](data-box-edge-manage-access-power-connectivity-mode.md)
