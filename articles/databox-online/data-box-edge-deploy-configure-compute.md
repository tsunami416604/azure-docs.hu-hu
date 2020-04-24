---
title: Oktatóanyag a szűréshez, az adatok elemzéséhez a Azure Data Box Edge számításával | Microsoft Docs
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
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Oktatóanyag: az adatátalakítás Azure Data Box Edge

Ez az oktatóanyag azt ismerteti, hogyan lehet számítási szerepkört konfigurálni a Azure Data Box Edge eszközön. A számítási szerepkör konfigurálása után Data Box Edge az adatokat az Azure-ba való küldés előtt átalakíthatja.

Ez az eljárás körülbelül 10 – 15 percet is igénybe vehet.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Megosztások hozzáadása
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

 
## <a name="prerequisites"></a>Előfeltételek

Mielőtt beállít egy számítási szerepkört a Data Box Edge eszközön, győződjön meg a következőket:

- Aktiválta Data Box Edge eszközt a következő témakörben leírtak szerint: [Azure Data Box Edge csatlakoztatása, beállítása és aktiválása](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Számítás konfigurálása

A Data Box Edge számítási feladatainak konfigurálásához létre kell hoznia egy IoT Hub erőforrást.

1. Az Data Box Edge erőforrásának Azure Portal válassza az Áttekintés lehetőséget. A jobb oldali ablaktáblán, a **számítási** csempén válassza az első **lépések**lehetőséget.

    ![Ismerkedés a számítási feladatokkal](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. Az **Edge-számítás konfigurálása** csempén válassza a **számítás konfigurálása**lehetőséget.
3. Az **Edge-számítás konfigurálása panelen adja** meg a következőket:

   
    |Mező  |Érték  |
    |---------|---------|
    |IoT Hub     | Válasszon az **új** vagy a **meglévő**listából. <br> Alapértelmezés szerint a rendszer egy standard szintű (S1) szintet használ IoT-erőforrás létrehozásához. Az ingyenes szintű IoT erőforrás használatához hozzon létre egyet, majd válassza ki a meglévő erőforrást. <br> A IoT Hub erőforrás minden esetben ugyanazt az előfizetést és erőforráscsoportot használja, amelyet a Data Box Edge erőforrás használ.     |
    |Name (Név)     |Adja meg a IoT Hub erőforrás nevét.         |

    ![Ismerkedés a számítási feladatokkal](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Kattintson a **Létrehozás** gombra. A IoT Hub erőforrás létrehozása néhány percet vesz igénybe. A IoT Hub erőforrás létrehozása után a számítási csempék **konfigurálása** a számítási konfiguráció megjelenítéséhez. Annak ellenőrzéséhez, hogy a peremhálózati számítási szerepkör konfigurálva van-e, válassza a számítás **megjelenítése** lehetőséget a **számítás konfigurálása** csempén.
    
    ![Ismerkedés a számítási feladatokkal](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    > [!NOTE]
    > Ha a **számítási párbeszédpanel konfigurálása párbeszédpanelen be** van zárva a IoT Hub a Data Box Edge eszközhöz való társítása előtt, akkor a IoT hub jön létre, de nem jelenik meg a számítási konfigurációban. 
    
    Amikor a peremhálózati eszközön beállította a peremhálózat számítási szerepkört, két eszközt hoz létre: egy IoT eszközt és egy IoT Edge eszközt. Mindkét eszköz megtekinthető a IoT Hub erőforrásban. Ezen a IoT Edge eszközön a IoT Edge futtatókörnyezet is fut. Ezen a ponton csak a Linux platform érhető el a IoT Edge eszközhöz.


## <a name="add-shares"></a>Megosztások hozzáadása

Az oktatóanyag egyszerű üzembe helyezéséhez két megosztásra lesz szüksége: egy peremhálózati megosztásra és egy másik peremhálózati helyi megosztásra.

1. Vegyen fel egy peremhálózati megosztást az eszközön a következő lépések végrehajtásával:

    1. Az Data Box Edge-erőforrásban lépjen az **Edge számítás > első lépések**elemre.
    2. A **megosztás (ok) hozzáadása** csempén válassza a **Hozzáadás**lehetőséget.
    3. A **megosztás hozzáadása** panelen adja meg a megosztás nevét, és válassza ki a megosztás típusát.
    4. Az Edge-megosztás csatlakoztatásához jelölje be a **megosztás Edge-számítással való használatának**jelölőnégyzetét.
    5. Válassza ki a **Storage-fiókot**, a **Storage szolgáltatást**, egy meglévő felhasználót, majd válassza a **Létrehozás**lehetőséget.

        ![Peremhálózati megosztás hozzáadása](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Ha létrehozott egy helyi NFS-megosztást, a következő parancssori kapcsoló használatával másolja a fájlokat a megosztásba:

    `rsync <source file path> < destination file path>`

    Az rsync-paranccsal kapcsolatos további információkért lépjen az [rsync dokumentációra](https://www.computerhope.com/unix/rsync.htm).

    A rendszer létrehozza az Edge-megosztást, és sikeres létrehozási értesítést fog kapni. Előfordulhat, hogy a megosztási lista frissül, de meg kell várnia, amíg a megosztás létrehozása be nem fejeződik.

2. Vegyen fel egy peremhálózati helyi megosztást a peremhálózati eszközön úgy, hogy megismétli az előző lépés összes lépését, és bejelöli a **beállítás helyi megosztásként**jelölőnégyzetet. A helyi megosztásban lévő adatmennyiség az eszközön marad.

    ![Peremhálózat helyi megosztásának hozzáadása](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Válassza a **megosztások hozzáadása** lehetőséget a megosztások frissített listájának megtekintéséhez.

    ![Megosztások frissített listája](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Modul hozzáadása

Hozzáadhat egy egyéni vagy egy előre elkészített modult. Ezen a peremhálózati eszközön nincsenek egyéni modulok. Ha szeretné megismerni, hogyan hozhat létre egyéni modult, ugorjon a [C# modul fejlesztése a Data Box Edge eszközhöz](data-box-edge-create-iot-edge-module.md)című témakörre.

Ebben a szakaszban egy egyéni modult ad hozzá a IoT Edge eszközhöz, amelyet a [C# modul fejlesztése a Data Box Edge számára](data-box-edge-create-iot-edge-module.md)létrehozott. Ez az egyéni modul a peremhálózati eszköz peremhálózati helyi megosztásának fájljait veszi át, és áthelyezi őket egy peremhálózati (Felhőbeli) megosztásra az eszközön. A felhőalapú megosztás ezután leküldi a fájlokat a Felhőbeli megosztáshoz társított Azure Storage-fiókba.

1. Ugrás az **Edge-számításra > első lépések**. A **modulok hozzáadása** csempén válassza ki a forgatókönyv típusát **egyszerűként**. Válassza a **Hozzáadás** lehetőséget.
2. A **modul konfigurálása és hozzáadása panelen adja** meg a következő értékeket:

    
    |Mező  |Érték  |
    |---------|---------|
    |Name (Név)     | A modul egyedi neve. Ez a modul egy Docker-tároló, amelyet a Data Box Edgehoz társított IoT Edge eszközre telepíthet.        |
    |Rendszerkép URI-ja     | A modulhoz tartozó tároló rendszerképéhez tartozó képuri.        |
    |Szükséges hitelesítő adatok     | Ha be van jelölve, a rendszer a felhasználónevet és a jelszót használja a modulok megfelelő URL-címmel való lekéréséhez.        |
    |Bemeneti megosztás     | Válasszon egy bemeneti megosztást. Ebben az esetben a peremhálózat helyi megosztása a bemeneti megosztás. Az itt használt modul áthelyezi a fájlokat a peremhálózati helyi megosztásból egy peremhálózati megosztásba, ahol azokat a rendszer feltölti a felhőbe.        |
    |Kimeneti megosztás     | Válasszon kimeneti megosztást. Ebben az esetben az Edge-megosztás a kimeneti megosztás.        |
    |Trigger típusa     | Válasszon a **fájlból** vagy az **ütemtervből**. Egy fájl aktiválódik, amikor egy fájl esemény következik be, például egy fájl íródik a bemeneti megosztásba. Az ütemezett eseményindítók az Ön által meghatározott ütemezés alapján jelennek meg.         |
    |Trigger neve     | Az trigger egyedi neve.         |
    |Környezeti változók| Nem kötelezően megadandó információk, amelyek segítenek meghatározni azt a környezetet, amelyben a modul futni fog.   |

    ![Modul hozzáadása és konfigurálása](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Válassza a **Hozzáadás** lehetőséget. A modul hozzá lesz adva. A **modul hozzáadása** csempére vonatkozó frissítések jelzik, hogy a modul telepítve van. 

    ![Telepített modul](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Adatok átalakításának ellenőrzése és adatok átvitele

Az utolsó lépés az, hogy ellenőrizze, hogy a modul csatlakoztatva van-e, és hogy a várt módon fut-e. A modul futtatási ideje állapotának futnia kell a IoT Edge eszközön a IoT Hub erőforrásban.

Annak ellenőrzéséhez, hogy a modul fut-e, tegye a következőket:

1. Válassza a **modul hozzáadása** csempét. Ekkor a **modulok** panelre kerül. A modulok listájában azonosítsa a telepített modult. A hozzáadott modul futásidejű állapotának *futnia*kell.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  A Fájlkezelőben a korábban létrehozott Edge helyi és peremhálózati megosztásokhoz is csatlakozhat.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Adja hozzá az adatokat a helyi megosztáshoz.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    Az adatok áthelyeződnek a felhőalapú megosztásba.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    Az adatok ezután a Felhőbeli megosztásról a Storage-fiókba kerülnek. Az adatmegjelenítéshez lépjen a Storage Explorer.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
Végrehajtotta az érvényesítési folyamatot.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Megosztások hozzáadása
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

A Data Box Edge eszköz felügyeletének megismeréséhez lásd:

> [!div class="nextstepaction"]
> [A Data Box Edge felügyelete a helyi webes felhasználói felületen](data-box-edge-manage-access-power-connectivity-mode.md)
