---
title: Az oktatóanyag szűréséhez, speciális üzembe helyezés az Azure Data Box Edge számítási elemzéséhez |} A Microsoft Docs
description: Útmutató a számítási szerepkör konfigurálása a Data Box Edge, és speciális üzembe helyezési folyamat az adatok átalakítása az Azure-bA küldése előtt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b446a3ebf92f6240d3bc02a148fbb8296efec926
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65950724"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-for-advanced-deployment-flow"></a>Oktatóanyag: Az Azure Data Box Edge speciális üzembe helyezési folyamat segítségével az adatok átalakítása

Ez az oktatóanyag leírja a számítási szerepkör egy speciális üzembe helyezési folyamat konfigurálása az Azure Data Box Edge-eszközön. Miután konfigurálta a számítási szerepkör, a Data Box Edge is mielőtt elküldené az Azure-bA adatok átalakítása.

Számítási konfigurálhatók egyszerű vagy speciális üzembe helyezési folyamat az eszközön.

|                  | Egyszerű üzembe helyezés                                | A speciális telepítési                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Szánt     | A rendszergazdák                                | Fejlesztők                            |
| Típus             | Modulok telepítéséhez használja a Data Box Edge szolgáltatás      | IoT Hub szolgáltatás használatával hogyan helyezhet üzembe modulokat |
| Telepített modulok | Single                                           | Láncolt vagy több modul           |


Ez az eljárás befejezéséhez mintegy 20 – 30 percig is eltarthat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Megosztások hozzáadása
> * Adjon hozzá egy triggert
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

 
## <a name="prerequisites"></a>Előfeltételek

A Data Box Edge-eszközön beállítása egy számítási szerepkört, előtt ellenőrizze, hogy:

- Miután aktiválta a Data Box peremhálózati eszköz, leírtak szerint [kapcsolódás beállítása, és aktiválja az Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Számítás konfigurálása

Számítás konfigurálása a Data Box Edge, hozzon létre egy IoT Hub-erőforrást.

1. A Data Box Edge erőforrás, az Azure Portalon lépjen a **áttekintése**. A jobb oldali ablaktáblában a a **számítási** csempéről válassza **Ismerkedés**.

    ![Compute – első lépések](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. Az a **konfigurálása Edge számítási** csempéről válassza **számítás konfigurálása**.

    ![Compute – első lépések](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. Az a **konfigurálása Edge számítási** panelen adjon meg a következőket:

   
    |Mező  |Value  |
    |---------|---------|
    |IoT Hub     | Válassza ki a **új** vagy **meglévő**. <br> Alapértelmezés szerint egy Standard szintű (S1) segítségével hozzon létre egy IoT-erőforrást. Egy ingyenes IoT erőforrás használatához hozzon létre egyet, és válassza ki a meglévő erőforrást. <br> Minden esetben az IoT Hub-erőforrást használ, ugyanazt az előfizetést és erőforráscsoportot, amelyet a Data Box Edge erőforrás.     |
    |Name (Név)     |Adja meg az IoT Hub-erőforrás nevét.         |

    ![Compute – első lépések](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Kattintson a **Létrehozás** gombra. Az IoT Hub erőforrás-létrehozás néhány percet vesz igénybe. Az IoT Hub-erőforrás létrehozása után a **konfigurálása Edge számítási** csempe megjelenítése a számítási konfiguráció frissítéseit. Győződjön meg arról, hogy az Edge számítási szerepkör konfigurálva van-e, jelölje be **nézet config** a a **számítás konfigurálása** csempére.
    
    ![Compute – első lépések](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    Az Edge-eszközön a peremhálózati számítási szerepkör be van állítva, ha két eszközt hoz létre: egy IoT-eszköz és az IoT Edge-eszköz. Mindkét eszköz az IoT Hub erőforrás lehet megtekinteni. Az IoT Edge-futtatókörnyezet is fut az IoT Edge-eszközön.

    Ezen a ponton csak a Linux rendszeren érhető el az IoT Edge-eszköz.


## <a name="add-shares"></a>Megosztások hozzáadása

A Speciális központi telepítéshez ebben az oktatóanyagban két megosztások kell: egy Edge-megosztást, és a egy másik Edge helyi megosztás.

1. Az Edge-megosztás hozzáadása az eszközön a következő lépések végrehajtásával:

    1. A Data Box Edge erőforrás, lépjen a **Edge számítási > első lépések**.
    2. Az a **megosztás(ok) hozzáadása** csempéről válassza **Hozzáadás**.
    3. Az a **Hozzáadás megosztás** panelen adja meg a megosztás nevét, és válassza ki a megosztást.
    4. A peremhálózati megosztás csatlakoztatásához, válassza ki a jelölőnégyzetet **megosztás használatára az Edge számítási**.
    5. Válassza ki a **tárfiók**, **társzolgáltatás**, egy meglévő felhasználót, és válassza ki **létrehozás**.

        ![Az Edge-megosztás hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    Az Edge-megosztás létrehozása után egy sikeres létrehozás értesítést kap. A megosztás lista frissül, hogy tükrözzék az új megosztásban.

2. Az Edge helyi megosztás hozzáadása az Edge-eszközön szerint ismételje meg az előző lépésben található összes lépést, és jelölje be a jelölőnégyzetet a **Edge helyi megosztásnak konfigurálása**. Az adatok a helyi megosztáson marad az eszközön.

    ![Az Edge helyi megosztás hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. Az a **megosztások** panelen megosztások frissített listájának megtekintéséhez.

    ![Megosztások frissített listája](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Az újonnan létrehozott helyi megosztás tulajdonságainak megtekintéséhez válassza ki a megosztást a listából. Az a **helyi csatlakoztatási pont az Edge-hez számítási modulok** mezőbe másolja az ehhez a megosztáshoz tartozó értéket.

    A helyi csatlakoztatási pontot fogja használni, a modul telepítésekor.

    ![A "helyi csatlakoztatási pont az Edge-hez számítási modulok" mezőbe](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Az Ön által létrehozott Edge megosztott tulajdonságainak megtekintéséhez válassza ki a megosztást a listából. Az a **helyi csatlakoztatási pont az Edge-hez számítási modulok** mezőbe másolja az ehhez a megosztáshoz tartozó értéket.

    A helyi csatlakoztatási pontot fogja használni, a modul telepítésekor.

    ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Adjon hozzá egy triggert

1. Lépjen a **Edge számítási > Eseményindítók**. Válassza ki **+ Hozzáadás eseményindító**.

    ![Eseményindító hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. Az a **Hozzáadás eseményindító** panelen adjon meg a következő értékeket.

    |Mező  |Value  |
    |---------|---------|
    |Trigger neve     | Az eseményindító egyedi név.         |
    |Trigger típusa     | Válassza ki **fájl** eseményindító. Egy fájl eseményindítót, amikor egy fájl esemény bekövetkeztekor, mint például egy fájl a bemeneti megosztás íródik. Egy ütemezett, másrészt eseményindítót fel az Ön által megadott ütemezés alapján. Ebben a példában egy fájl eseményindító van szükségünk.    |
    |A bemeneti megosztás     | Válasszon egy bemeneti megosztást. Az Edge helyi megosztás ebben az esetben a bemeneti megosztás. Az itt használt modul helyezi át fájlokat az Edge helyi megosztás egy Edge-megosztáshoz, azok a rendszer feltölti a felhőbe.        |

    ![Eseményindító hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. Az eseményindító létrehozása után értesítést kap. Eseményindítók listájának megjelenítéséhez az újonnan létrehozott eseményindító frissül. Válassza ki az imént létrehozott eseményindító.

    ![Eseményindító hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Másolja és mentse a minta-útvonal. Fog ez az útvonal minta módosítja, és később az IoT Hub használhatja.

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Eseményindító hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Modul hozzáadása

Nincsenek nem egyéni modulok az Edge-eszközön. Hozzáadhat egy egyéni vagy egy előre elkészített modul. Ismerje meg, hogyan hozhat létre egy egyéni modult, lépjen a [Develop egy C# modul a Data Box Edge-eszköz](data-box-edge-create-iot-edge-module.md).

Ebben a szakaszban ad hozzá egy egyéni modult, amelyet az IoT Edge-eszköz [Develop egy C# készült a Data Box Edge-modul](data-box-edge-create-iot-edge-module.md). Ez a modul egyéni szükséges fájlokat-Edge helyi megosztásból az Edge-eszközön, és az eszközön az Edge (felhő) megosztások helyezi őket. A felhőbeli fájlmegosztás majd leküldi a fájlokat a felhőbeli fájlmegosztás társított Azure storage-fiókban.

1. Lépjen a **Edge számítási > első lépések**. Az a **modulok hozzáadása** csempére, válassza ki a forgatókönyv szerint **speciális**. Válassza ki **nyissa meg az IoT Hub**.

    ![Válassza ki a speciális telepítési](./media/data-box-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. Az IoT Hub-erőforrás, lépjen a **IoT Edge-eszköz** , és válassza ki az IoT Edge-eszköz.

    ![Nyissa meg az IoT Hub IoT Edge-eszköz](./media/data-box-edge-deploy-configure-compute-advanced/add-module-2.png)

3. A **eszközadatok**válassza **modulok beállítása**.

    ![A modulok beállítása hivatkozásra](./media/data-box-edge-deploy-configure-compute-advanced/add-module-3.png)

4. A **modulok hozzáadása**, tegye a következőket:

    1. A neve, címe, felhasználónév és jelszó megadása a tároló-beállításjegyzékek beállításai az egyéni modulhoz.
    A név, cím és a listán szereplő hitelesítő adatok használhatók megfelelő URL-modulok beolvasása. A modul üzembe helyezéséhez az **Üzemelő példány moduljai** területen válassza ki az **IoT Edge-modult**. Az IoT Edge-modul docker-tároló, amely központilag telepíthető a Data Box Edge-eszköz társított IoT Edge-eszköz.

        ![A modulok beállítása lap](./media/data-box-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Adja meg az egyéni IoT Edge-modul beállításait. Adjon meg a következő értékeket.
     
        |Mező  |Érték  |
        |---------|---------|
        |Name (Név)     | A modul egyedi neve. Ez a modul egy docker-tároló, amely központilag telepíthető a Data Box Edge társított IoT Edge-eszköz.        |
        |Lemezkép URI-ja     | A kép URI-t a megfelelő tárolórendszerképet a modul.        |
        |Szükséges hitelesítő adatok     | Ha be van jelölve, felhasználónév és jelszó segítségével modulok egy megfelelő URL-cím lekéréséhez.        |
    
        Az a **tároló létrehozása beállítások** mezőbe írja be a helyi csatlakoztatási pontokat az Edge-modulok, a peremhálózati megosztás és az Edge helyi megosztás az előző lépésben kimásolt.

        > [!IMPORTANT]
        > Az itt használt elérési utakat a tárolóba csatlakoztatva vannak, ezért meg kell egyeznie, a funkciót, a tárolóban vár. Ha [hozzon létre egy egyéni modult](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code), az adott modul megadott kód vár a másolt elérési útjait. Ne módosítsa az elérési utak.
    
        Az a **tároló létrehozása beállítások** mezőbe, beillesztheti a következő mintát:
    
        ```
        {
          "HostConfig": 
          {
           "Binds": 
            [
             "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
             "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
           }
        }
        ```

        Minden környezeti változó használt a modul adja meg. Környezeti változók nem kötelező információkkal, amelyek segítséget nyújtanak a környezet, amelyben a modul fut.

        ![A tároló létrehozása beállítások használata](./media/data-box-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Ha szükséges, a speciális Edge-futtatókörnyezet beállítások konfigurálása, és kattintson **tovább**.

        ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5.  A **útvonalak megadása**, állítsa be a modulok közötti útvonalak.  
    
    ![Útvonalak megadása](./media/data-box-edge-deploy-configure-compute-advanced/add-module-7.png)

    Lecserélheti *útvonal* az az alábbi útvonal-karakterlánc, amely korábban vágólapra másolt. Ebben a példában adja meg a helyi megosztás, amely adatokat küld le a felhőbeli fájlmegosztás neve. Cserélje le a `modulename` a modul nevével. Kattintson a **Tovább** gombra.
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![Az útvonalak megadása szakasz](./media/data-box-edge-deploy-configure-compute-advanced/add-module-8.png)

6.  A **tekintse át a központi telepítési**, tekintse át a a beállításokat, és válassza **küldés** elküldeni a modul a központi telepítés.

    ![A modulok beállítása lap](./media/data-box-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Ez a művelet elindítja az adatraktármodul központi telepítése. Az üzembe helyezés befejezése után a **futásidejű állapot** , de a modul **futó**.

    ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Ellenőrizze az adatok átalakításához, átvitele

Az utolsó lépés, hogy győződjön meg arról, hogy a modul csatlakoztatva és fut a várt módon. A modul futtatási állapotát az IoT Edge-eszköz az IoT Hub erőforrás kell futnia.

A következő lépésekkel ellenőrizze adatátalakítás és átvitele az Azure-bA.
 
1.  A Fájlkezelőben kapcsolódni a helyi Edge és a korábban létrehozott Edge megosztások.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1.  Adja hozzá az adatokat a helyi megosztáshoz.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    Az adatok áthelyeződnek a felhőalapú megosztásba.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Az adatok a felhő megosztásból majd leküldve a storage-fiókhoz. Az adatok megtekintéséhez lépjen a tárfiókhoz, majd **Tártallózó**. Megtekintheti a feltöltött adatok a storage-fiókban.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Az ellenőrzési folyamat befejezte.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Megosztások hozzáadása
> * Adjon hozzá egy triggert
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

Ismerje meg, hogyan felügyelheti a Data Box peremhálózati eszköz, tekintse meg:

> [!div class="nextstepaction"]
> [A Data Box Edge felügyelete a helyi webes felhasználói felületen](data-box-edge-manage-access-power-connectivity-mode.md)
