---
title: Oktatóanyag az Azure Data Box Edge számítási adatainak szűréséhez, elemzéséhez | Microsoft dokumentumok
description: Megismerheti, hogyan konfigurálhatja a számítási szerepköröket a Data Box Edge-ben, és hogyan alakíthatja át az adatokat, mielőtt beküldené őket az Azure-nak.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b641ae62ba6e0cdacaeb46b1ffee2f02c7544763
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239020"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Oktatóanyag: Adatok átalakítása az Azure Data Box Edge segítségével

Ez az oktatóanyag ismerteti, hogyan konfigurálhat számítási szerepkört az Azure Data Box Edge-eszközön. A számítási szerepkör konfigurálása után a Data Box Edge átalakíthatja az adatokat, mielőtt elküldené az Azure-ba.

Ez az eljárás körülbelül 10-15 percet is igénybe vehet.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Megosztások hozzáadása
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

 
## <a name="prerequisites"></a>Előfeltételek

Mielőtt beállítegy számítási szerepkört a Data Box Edge eszközén, győződjön meg arról, hogy:

- Aktiválta a Data Box Edge-eszközt a [Csatlakozás, beállítás és az Azure Data Box Edge aktiválása szerint.](data-box-edge-deploy-connect-setup-activate.md)


## <a name="configure-compute"></a>Számítás konfigurálása

A Data Box Edge számítási konfigurálásához hozzon létre egy IoT Hub-erőforrást.

1. A Data Box Edge-erőforrás Azure portalon nyissa meg az Áttekintés című témakört. A jobb oldali **ablaktáblában,** a Számítási csempe, válassza a **Kezdési lehetőséget**.

    ![A számítás – első lépések](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. Az **Edge számításkonfigurálása csempén** válassza **a Számítás konfigurálása**lehetőséget.
3. Az **Edge konfigurálása számítási** panelen adja meg a következőket:

   
    |Mező  |Érték  |
    |---------|---------|
    |IoT Hub     | Válasszon az **Új** vagy **a Meglévő közül.** <br> Alapértelmezés szerint egy standard szint (S1) egy IoT-erőforrás létrehozásához használható. Ingyenes szintű IoT-erőforrás használatához hozzon létre egyet, majd válassza ki a meglévő erőforrást. <br> Minden esetben az IoT Hub-erőforrás ugyanazt az előfizetést és erőforráscsoportot használja, amelyet a Data Box Edge erőforrás használ.     |
    |Név     |Adja meg az IoT Hub-erőforrás nevét.         |

    ![A számítás – első lépések](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Kattintson a **Létrehozás** gombra. Az IoT Hub-erőforrás létrehozása néhány percet vesz igénybe. Az IoT Hub-erőforrás létrehozása után a Számítási csempe **frissítéseinek konfigurálása** a számítási konfiguráció megjelenítéséhez. Annak ellenőrzéséhez, hogy az Edge számítási szerepkör konfigurálva van-e, válassza a **Számítási nézet lehetőséget** a Számítási **konfigurálása** csempén.
    
    ![A számítás – első lépések](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    > [!NOTE]
    > Ha a **Számítási konfigurálás** párbeszédpanel be van zárva, mielőtt az IoT Hub társítva lenne a Data Box Edge eszközhöz, az IoT Hub létrejön, de nem jelenik meg a számítási konfigurációban. 
    
    Amikor az Edge számítási szerepkör be van állítva az Edge-eszközön, két eszközt hoz létre: egy IoT-eszközt és egy IoT Edge-eszközt. Mindkét eszköz megtekinthető az IoT Hub-erőforrásban. Egy IoT Edge-futtató, amely ezen az IoT Edge-eszközön is fut. Ezen a ponton csak a Linux platform érhető el az IoT Edge-eszköz.


## <a name="add-shares"></a>Megosztások hozzáadása

Az oktatóanyag egyszerű üzembe helyezéséhez két megosztásra van szükség: egy Edge-megosztásra és egy másik Edge helyi megosztásra.

1. Az alábbi lépésekkel adjon hozzá egy Edge-megosztást az eszközön:

    1. A Data Box Edge erőforrásban nyissa meg **az Edge számítási > az Első lépések**.
    2. A **Megosztás hozzáadása (k)** csempén válassza a **Hozzáadás**lehetőséget.
    3. A **Megosztás hozzáadása** panelen adja meg a megosztás nevét, és válassza ki a megosztás típusát.
    4. Az Edge megosztás csatlakoztatásához jelölje be **a Megosztás használata az Edge compute segítségével jelölőnégyzetet.**
    5. Válassza a **Storage fiók**, **Storage service**, egy meglévő felhasználó, majd válassza a **Create**lehetőséget.

        ![Szegélymegosztás hozzáadása](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Ha helyi NFS-megosztást hozott létre, a következő távoli szinkronizálási (rsync) parancssegítségével másolja a fájlokat a megosztásra:

    `rsync <source file path> < destination file path>`

    Az rsync paranccsal kapcsolatos további tudnivalókért tanulmányozza az [Rsync dokumentációját.](https://www.computerhope.com/unix/rsync.htm)

    Az Edge-megosztás létrejön, és sikeres létrehozási értesítést kap. Lehet, hogy a megosztási lista frissült, de meg kell várnia a megosztás létrehozásának befejezését.

2. Adjon hozzá egy Edge helyi megosztást az Edge-eszközön az előző lépés összes lépésének megismétlésével, és jelölje be a **Beállítás edge helyi megosztásként jelölőnégyzetet.** A helyi megosztásban lévő adatok az eszközön maradnak.

    ![Edge helyi megosztás hozzáadása](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Válassza **a Megosztások hozzáadása** lehetőséget a frissített megosztáslista megtekintéséhez.

    ![Megosztások frissített listája](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Modul hozzáadása

Hozzáadhat egy egyéni vagy egy előre elkészített modult. Ezen az Edge-eszközön nincsenek egyéni modulok. Ha meg szeretné tudni, hogyan hozhat létre egyéni modult, olvassa el [a C# modul fejlesztése a Data Box Edge eszközhöz](data-box-edge-create-iot-edge-module.md)című lehetőséget.

Ebben a szakaszban egy egyéni modult ad hozzá az IoT Edge-eszközhöz, amelyet a C# modul fejlesztése a [Data Box Edge.In](data-box-edge-create-iot-edge-module.md)this section, you add a custom module to the IoT Edge device that you created in Develop a C# module for your Data Box Edge . Ez az egyéni modul az Edge-eszköz edge-i helyi megosztásáról fájlokat vesz fel, és áthelyezi őket egy Edge (felhőbeli) megosztásra az eszközön. A felhőmegosztás ezután lelöki a fájlokat az Azure storage-fiókba, amely a felhőmegosztáshoz van társítva.

1. Nyissa meg az **Edge számítási > Az első lépések**. A **Modulok hozzáadása** csempén válassza ki a forgatókönyv típusát **egyszerűként.** Válassza a **Hozzáadás** lehetőséget.
2. A **Modul konfigurálása és hozzáadása** panelen adja meg a következő értékeket:

    
    |Mező  |Érték  |
    |---------|---------|
    |Név     | A modul egyedi neve. Ez a modul egy docker-tároló, amely telepítheti az IoT Edge-eszköz, amely a Data Box Edge társított.        |
    |Kép URI-ja     | A modul megfelelő tárolórendszerképének lemezképe.        |
    |Hitelesítő adatok szükségesek     | Ha be van jelölve, a felhasználónév és a jelszó a megfelelő URL-címmel rendelkező modulok beolvasására szolgál.        |
    |Bemeneti megosztás     | Jelöljön ki egy bemeneti megosztást. Az Edge helyi megosztás a bemeneti részesedés ebben az esetben. Az itt használt modul áthelyezi a fájlokat az Edge helyi megosztásáról egy Edge-megosztásra, ahol feltöltik őket a felhőbe.        |
    |Kimeneti részesedés     | Jelöljön ki egy kimeneti megosztást. Az Edge-megosztás ebben az esetben a kimeneti részesedés.        |
    |Trigger típusa     | Válasszon a **Fájl** vagy **az Ütemezés lehetőséget.** A fájl eseményindító akkor aktiválódik, amikor fájlesemény történik, például egy fájl írása a bemeneti megosztásba. Az ütemezett eseményindító az Ön által meghatározott ütemezés alapján aktiválódik.         |
    |Eseményindító neve     | Az eseményindító egyedi neve.         |
    |Környezeti változók| Választható információk, amelyek segítenek meghatározni azt a környezetet, amelyben a modul futni fog.   |

    ![Modul hozzáadása és konfigurálása](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Válassza a **Hozzáadás** lehetőséget. A modul hozzáadódik. A **Modul hozzáadása** csempe frissíti azt jelzi, hogy a modul telepítve van. 

    ![Modul telepítve](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Adatok átalakításának ellenőrzése és adatok átvitele

Az utolsó lépés annak biztosítása, hogy a modul csatlakoztatva van, és fut a várt módon. A modul futásidejű állapotának futnia kell az IoT Edge-eszköz az IoT Hub-erőforrásban.

A modul futásának ellenőrzéséhez tegye a következőket:

1. Válassza a **Modul hozzáadása** csempét. Ez **elviszi** a modulok penge. A modulok listájában azonosítsa a telepített modult. A hozzáadott modul futásidejű állapotának *futnia*kell.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  A Fájlkezelőben csatlakozzon a korábban létrehozott Edge helyi és edge megosztásokhoz is.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Adja hozzá az adatokat a helyi megosztáshoz.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    Az adatok áthelyeződnek a felhőalapú megosztásba.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    Az adatok ezután leküldéses a felhőmegosztásról a tárfiókba. Az adatok megtekintéséhez nyissa meg a Tárolókezelőt.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
Befejezte az ellenőrzési folyamatot.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Megosztások hozzáadása
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

A Data Box Edge-eszköz felügyeletéről a következő témakörben olvashat:

> [!div class="nextstepaction"]
> [A Data Box Edge felügyelete a helyi webes felhasználói felületen](data-box-edge-manage-access-power-connectivity-mode.md)
