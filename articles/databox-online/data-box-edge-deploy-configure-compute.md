---
title: Adatok átalakítása az Azure Data Box Edge segítségével | Microsoft Docs
description: Megismerheti, hogyan konfigurálhatja a számítási szerepköröket a Data Box Edge-ben, és hogyan alakíthatja át az adatokat, mielőtt beküldené őket az Azure-nak.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: c52c311f1e1cd1335ea5797eadacd0bc89e1b36c
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402115"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>Oktatóanyag: Adatátalakítás az Azure Data Box Edge (előzetes verzió)

Ez az oktatóanyag leírja a számítási szerepkör konfigurálása az Azure Data Box Edge-eszközön. Miután konfigurálta a számítási szerepkör, a Data Box Edge is mielőtt elküldené az Azure-bA adatok átalakítása.

Ez az eljárás befejezéséhez körülbelül 30-45 percig is eltarthat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure IoT Hub-erőforrás létrehozásához
> * Számítási szerepkör beállítása
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

> [!IMPORTANT]
> A Data Box Edge előzetes verzióban érhető el. Order, és a megoldás üzembe helyezése előtt tekintse át a [Azure villámnézethez szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="prerequisites"></a>Előfeltételek

A Data Box Edge-eszközön beállítása egy számítási szerepkört, előtt ellenőrizze, hogy:

* Miután aktiválta a Data Box peremhálózati eszköz, leírtak szerint [kapcsolódás beállítása, és aktiválja az Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="create-an-iot-hub-resource"></a>IoT Hub-erőforrás létrehozása

A számítási szerepkör Data Box Edge beállítása előtt létre kell hoznia egy IoT Hub-erőforrást.

Részletes útmutatásért lásd az [új IoT Hub létrehozását](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub) ismertető témakört. A Data Box Edge erőforrás használt azonos előfizetésben és erőforráscsoportban csoportot használja.

![IoT Hub-erőforrás létrehozása](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

Ha egy Edge számítási szerepkör még nincs beállítva, az alábbi korlátozásokkal érvényesek:

- Az IoT Hub-erőforrás nincs bármely Azure-IoT-eszközök vagy az Azure IoT Edge-eszközökön.
- Nem hozhat létre helyi Edge-megosztásokat. Amikor megosztást ad hozzá, nincs engedélyezve az a lehetőség, amely engedélyezné a helyi megosztások létrehozását az Edge-számításokhoz.


## <a name="set-up-compute-role"></a>Számítási szerepkör beállítása

Az Edge-eszközön a peremhálózati számítási szerepkör be van állítva, ha két eszközt hoz létre: egy IoT-eszköz és az IoT Edge-eszköz. Mindkét eszköz az IoT Hub erőforrás lehet megtekinteni.

Az eszközön a számítási szerepkör beállításához, tegye a következőket:

1. Lépjen a Data Box Edge erőforrás, válassza ki a **áttekintése**, majd válassza ki **állítsa be a számítási szerepkör**. 

    ![A bal oldali ablaktáblán a áttekintése hivatkozás](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    Másik lehetőségként megnyithatja **modulok** válassza **számítás konfigurálása**.

    ![A "Moduloknak" és a "Számítás konfigurálása" hivatkozások](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
1. A legördülő listában válassza ki a **az IoT Hub erőforrás** az előző lépésben létrehozott.  
    Ezen a ponton csak a Linux rendszeren érhető el az IoT Edge-eszköz. 
    
1. Kattintson a **Create** (Létrehozás) gombra.

    ![A Létrehozás gombra](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
    A számítási szerepkör létrehozása néhány percet vesz igénybe. A jelenlegi kiadás hibája miatt a képernyő akkor sem frissül, ha a számítási szerepkör már létrejött. Győződjön meg arról, hogy az Edge számítási szerepkör konfigurálva van-e, lépjen a **modulok**.  

    ![A "Konfigurálás Edge számítási" eszközök listája](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

1. Lépjen a **áttekintése** újra.  
    A képernyő frissül, annak jelzésére, hogy a számítási szerepkör van-e konfigurálva.

    ![A számítási szerepkör beállítása](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
1. Az IoT Hub, az Edge számítási szerepkör létrehozásakor használt, lépjen a **IoT-eszközök**.  
    Egy IoT-eszköz most már engedélyezve van. 

    ![Az "IoT-eszközök" oldal](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

1. A bal oldali panelen válassza ki a **IoT Edge**.  
    IoT Edge-eszköz is engedélyezve van.

    ![A számítási szerepkör beállítása](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
1. Válassza ki az IoT Edge-eszközt, és kattintson rá.  
    Ezen az IoT Edge-eszközön egy Edge-ügynök fut. 

    ![Az eszköz részleteit megjelenítő oldalon](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

    Nincsenek nem egyéni modulok az Edge-eszközön, de most már hozzáadhat egy egyéni modult. Ismerje meg, hogyan hozhat létre egy egyéni modult, lépjen a [Develop egy C# modul a Data Box Edge-eszköz](data-box-edge-create-iot-edge-module.md).


## <a name="add-a-custom-module"></a>Egyéni modul hozzáadása

Ebben a szakaszban ad hozzá egy egyéni modult, amelyet az IoT Edge-eszköz [Develop egy C# készült a Data Box Edge-modul](data-box-edge-create-iot-edge-module.md). 

Az alábbi eljárás egy példa, ahol a egyéni modul szükséges fájlok egy helyi meghajtóról az Edge-eszközön, és az eszközön helyezi őket egy felhőbeli fájlmegosztás használja. A felhőbeli fájlmegosztás majd leküldi a fájlokat a felhőbeli fájlmegosztás társított Azure storage-fiókban. 

1. Az Edge-eszközön a helyi megosztás hozzáadása az alábbiak szerint:

    a. A Data Box Edge-erőforrásban menjen a **Megosztások** oldalra. 
    
    b. Válassza ki **Hozzáadás megosztás**, majd adja meg a megosztás nevét, és válassza ki a megosztást. 
    
    c. Egy helyi megosztás létrehozásához válassza a **Edge helyi megosztásnak konfigurálása** jelölőnégyzetet. 
    
    d. Válassza ki **új létrehozása** vagy **meglévő**, majd válassza ki **létrehozás**.

    ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    Ha létrehozott egy helyi NFS-megosztások, a következő távoli szinkronizálási (rsync) a kapcsoló használatával másolja a fájlokat a megosztás:

    `rsync --inplace <source file path> < destination file path>`

    A rsync paranccsal kapcsolatos további információkért lépjen [Rsync dokumentáció](https://www.computerhope.com/unix/rsync.htm). 

    A helyi megosztás létrehozása, és a egy sikeres létrehozás értesítést kap. A megosztási listán frissülhet, de meg kell várnia a fájlmegosztás létrehozásának hajtható végre.
    
1. Nyissa meg a megosztások. 

    ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
1. Az újonnan létrehozott helyi megosztás tulajdonságainak megtekintéséhez kattintson rá. 

1. Az a **helyi csatlakoztatási pont az Edge-hez számítási modulok** mezőbe másolja az ehhez a megosztáshoz tartozó értéket.  
    A helyi csatlakoztatási pontot fogja használni, a modul telepítésekor.

    ![A "helyi csatlakoztatási pont az Edge-hez számítási modulok" mezőbe](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
1. A Data Box Edge-eszközön, a létrehozott meglévő felhő-megosztáson a **helyi csatlakoztatási pont az Edge-hez számítási modulok** mezőbe másolja a helyi csatlakoztatási pont az Edge compute-modulok a felhő-megosztás.  
    A helyi csatlakoztatási pontot fogja használni, a modul telepítésekor.

    ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

1. Az IoT Edge-eszközt ad hozzá egy egyéni modult, nyissa meg az IoT Hub-erőforrásra, és folytassa a **IoT Edge-eszköz**. 

1. Válassza ki az eszközt, majd a **eszközadatok**válassza **modulok beállítása**. 

    ![A modulok beállítása hivatkozásra](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

1. A **modulok hozzáadása**, tegye a következőket:

    a. A neve, címe, felhasználónév és jelszó megadása a tároló-beállításjegyzékek beállításai az egyéni modulhoz.  
    A név, cím és a listán szereplő hitelesítő adatok használhatók megfelelő URL-modulok beolvasása. A modul üzembe helyezéséhez az **Üzemelő példány moduljai** területen válassza ki az **IoT Edge-modult**. Az IoT Edge-modul docker-tároló, amely központilag telepíthető a Data Box Edge-eszköz társított IoT Edge-eszköz.

    ![A modulok beállítása lap](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    b. A név, a modul és a megfelelő tároló rendszerképéhez tartozó kép URI-t adja meg az egyéni IoT Edge-modul beállításait. 
    
    ![Az IoT Edge-egyéni modulok oldal](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    c. Az a **tároló létrehozása beállítások** mezőbe írja be a helyi csatlakoztatási pontokat az Edge-modulok, a felhő és helyi megosztás az előző lépésben kimásolt.
    > [!IMPORTANT]
    > A másolt elérési utakat; Ne hozzon létre új elérési utakat. A helyi csatlakoztatási pontok vannak leképezve a megfelelő **InputFolderPath** és a **OutputFolderPath** a modulban megadott amikor Ön [frissíteni a modul egyéni kóddal](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code). 
    
    Az a **tároló létrehozása beállítások** mezőbe, beillesztheti a következő mintát: 
    
    ```
    {
        "HostConfig": {
        "Binds": [
        "/home/hcsshares/mysmblocalshare:/home/LocalShare",
        "/home/hcsshares/mysmbshare1:/home/CloudShare"
        ]
        }
    }
    ```

    Minden környezeti változó is adja meg a modulban.

    ![A tároló létrehozása beállítások használata](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    d. Ha szükséges, a speciális Edge-futtatókörnyezet beállítások konfigurálása, és kattintson **tovább**.

    ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
1.  A **útvonalak megadása**, állítsa be a modulok közötti útvonalak.  
    Ebben a példában adja meg a helyi megosztás, amely adatokat küld le a felhőbeli fájlmegosztás neve.

    Lecserélheti *útvonal* az az alábbi útvonal-karakterlánc:       `"route": "FROM /* WHERE topic = 'mysmblocalshare' INTO BrokeredEndpoint(\"/modules/filemovemodule/inputs/input1\")"`

    ![Az útvonalak megadása szakasz](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 

1. Kattintson a **Tovább** gombra. 

1.  A **tekintse át a központi telepítési**, tekintse át a a beállításokat, és válassza **küldés** elküldeni a modul a központi telepítés.

    ![A modulok beállítása lap](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
    Ez a művelet elindítja az adatraktármodul központi telepítése, az alábbi képen látható módon:

    ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>Adatok átalakításának ellenőrzése és adatok átvitele

Az utolsó lépés, hogy győződjön meg arról, hogy a modul csatlakoztatva és fut a várt módon. A modul futtatási állapotát az IoT Edge-eszköz az IoT Hub erőforrás kell futnia.

![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
Győződjön meg arról, hogy a modul fut, tegye a következőket:

1. Válassza ki a modult, és nézze meg, hogy az identitás Ikermodulja.  
    Az ügyfél állapota az Edge-eszköz és a modul jelenjenek meg *csatlakoztatva*.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
    A modul fut, miután is megjelenik a Data Box Edge erőforrás az Edge-modulok listáját. A rendszer hozzáadta modul futási állapotát *futó*.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
1.  A Fájlkezelőben, csatlakozzon a mind a helyi és felhőbeli fájlmegosztások, korábban létrehozott.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
1.  Adja hozzá az adatokat a helyi megosztáshoz.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
    Az adatok áthelyeződnek a felhőalapú megosztásba.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

    Az adatok a felhő megosztásból majd leküldve a storage-fiókhoz. Az adatok megtekintéséhez nyissa meg a Storage Explorerben.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
Az ellenőrzési folyamat befejezte.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * IoT Hub-erőforrás létrehozása
> * Számítási szerepkör beállítása
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

Ismerje meg, hogyan felügyelheti a Data Box peremhálózati eszköz, tekintse meg:

> [!div class="nextstepaction"]
> [A Data Box Edge felügyelete a helyi webes felhasználói felületen](https://aka.ms/dbg-docs)


