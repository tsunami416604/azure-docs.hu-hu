---
title: Oktatóanyag a speciális üzembe helyezési adatok szűréséhez és elemzéséhez a Azure Data Box Edge számításával | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a számítási szerepkört Data Box Edge, és hogyan alakíthatja át az adatokat a speciális üzembe helyezési folyamathoz az Azure-ba való küldés előtt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b446a3ebf92f6240d3bc02a148fbb8296efec926
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384736"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-for-advanced-deployment-flow"></a>Oktatóanyag: az adatátalakítás Azure Data Box Edge a speciális üzembe helyezési folyamathoz

Ez az oktatóanyag azt ismerteti, hogyan lehet számítási szerepkört beállítani egy speciális telepítési folyamathoz a Azure Data Box Edge eszközön. A számítási szerepkör konfigurálása után Data Box Edge az adatokat az Azure-ba való küldés előtt átalakíthatja.

A számítás konfigurálható az eszköz egyszerű vagy speciális üzembe helyezési folyamatához.

|                  | Egyszerű üzembe helyezés                                | Speciális üzembe helyezés                   |
|------------------|--------------------------------------------------|---------------------------------------|
| A következőre szánt     | Rendszergazdák                                | Fejlesztők                            |
| Típus             | A Data Box Edge szolgáltatás használata modulok üzembe helyezéséhez      | A IoT Hub szolgáltatás használata modulok üzembe helyezéséhez |
| Üzembe helyezett modulok | Single                                           | Láncolt vagy több modul           |


Ez az eljárás körülbelül 20 – 30 percet vesz igénybe.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Megosztások hozzáadása
> * Eseményindító hozzáadása
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

 
## <a name="prerequisites"></a>Előfeltételek

Mielőtt beállít egy számítási szerepkört a Data Box Edge eszközön, győződjön meg a következőket:

- Aktiválta Data Box Edge eszközt a következő témakörben leírtak szerint: [Azure Data Box Edge csatlakoztatása, beállítása és aktiválása](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Számítás konfigurálása

A Data Box Edge számítási feladatainak konfigurálásához létre kell hoznia egy IoT Hub erőforrást.

1. Az Data Box Edge erőforrásának Azure Portal válassza az **Áttekintés**lehetőséget. A jobb oldali ablaktáblán, a **számítási** csempén válassza az első **lépések**lehetőséget.

    ![Ismerkedés a számítási feladatokkal](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. Az **Edge-számítás konfigurálása** csempén válassza a **számítás konfigurálása**lehetőséget.

    ![Ismerkedés a számítási feladatokkal](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. Az **Edge-számítás konfigurálása panelen adja** meg a következőket:

   
    |Mező  |Érték  |
    |---------|---------|
    |IoT Hub     | Válasszon az **új** vagy a **meglévő**listából. <br> Alapértelmezés szerint a rendszer egy standard szintű (S1) szintet használ IoT-erőforrás létrehozásához. Az ingyenes szintű IoT erőforrás használatához hozzon létre egyet, majd válassza ki a meglévő erőforrást. <br> A IoT Hub erőforrás minden esetben ugyanazt az előfizetést és erőforráscsoportot használja, amelyet a Data Box Edge erőforrás használ.     |
    |Name (Név)     |Adja meg a IoT Hub erőforrás nevét.         |

    ![Ismerkedés a számítási feladatokkal](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Kattintson a **Létrehozás** gombra. A IoT Hub erőforrás létrehozása néhány percet vesz igénybe. A IoT Hub erőforrás létrejötte után az **Edge számítási** csempék konfigurálása a számítási konfiguráció megjelenítéséhez. Annak ellenőrzéséhez, hogy a peremhálózati számítási szerepkör konfigurálva van-e, válassza a **konfiguráció megtekintése** lehetőséget a **számítás konfigurálása** csempén.
    
    ![Ismerkedés a számítási feladatokkal](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    Amikor a peremhálózati eszközön beállította a peremhálózat számítási szerepkört, két eszközt hoz létre: egy IoT eszközt és egy IoT Edge eszközt. Mindkét eszköz megtekinthető a IoT Hub erőforrásban. Ezen a IoT Edge eszközön a IoT Edge futtatókörnyezet is fut.

    Ezen a ponton csak a Linux platform érhető el a IoT Edge eszközhöz.


## <a name="add-shares"></a>Megosztások hozzáadása

Az oktatóanyag speciális üzembe helyezéséhez két megosztásra lesz szüksége: egy peremhálózati megosztásra és egy másik peremhálózati helyi megosztásra.

1. Vegyen fel egy peremhálózati megosztást az eszközön a következő lépések végrehajtásával:

    1. Az Data Box Edge-erőforrásban lépjen az **Edge számítás > első lépések**elemre.
    2. A **megosztás (ok) hozzáadása** csempén válassza a **Hozzáadás**lehetőséget.
    3. A **megosztás hozzáadása** panelen adja meg a megosztás nevét, és válassza ki a megosztás típusát.
    4. Az Edge-megosztás csatlakoztatásához jelölje be a **megosztás Edge-számítással való használatának**jelölőnégyzetét.
    5. Válassza ki a **Storage-fiókot**, a **Storage szolgáltatást**, egy meglévő felhasználót, majd válassza a **Létrehozás**lehetőséget.

        ![Peremhálózati megosztás hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    Az Edge-megosztás létrehozása után a rendszer sikeres létrehozási értesítést fog kapni. A rendszer frissíti a megosztási listát, hogy tükrözze az új megosztást.

2. Vegyen fel egy peremhálózati helyi megosztást a peremhálózati eszközön úgy, hogy megismétli az előző lépés összes lépését, és bejelöli a **beállítás helyi megosztásként**jelölőnégyzetet. A helyi megosztásban lévő adatmennyiség az eszközön marad.

    ![Peremhálózat helyi megosztásának hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. A **megosztások** panelen láthatja a megosztások frissített listáját.

    ![Megosztások frissített listája](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Az újonnan létrehozott helyi megosztás tulajdonságainak megtekintéséhez válassza ki a megosztást a listából. Az **Edge számítási modulok helyi csatlakoztatási pontja** mezőben másolja ki a megosztásnak megfelelő értéket.

    Ezt a helyi csatlakoztatási pontot fogja használni a modul telepítésekor.

    ![A "helyi csatlakoztatási pont az Edge számítási modulok számára" mező](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. A létrehozott peremhálózat-megosztás tulajdonságainak megtekintéséhez válassza ki a megosztást a listából. Az **Edge számítási modulok helyi csatlakoztatási pontja** mezőben másolja ki a megosztásnak megfelelő értéket.

    Ezt a helyi csatlakoztatási pontot fogja használni a modul telepítésekor.

    ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Eseményindító hozzáadása

1. Nyissa meg az **Edge számítási > eseményindítókat**. Válassza az **+ trigger hozzáadása**lehetőséget.

    ![Trigger hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. Az **trigger hozzáadása panelen adja** meg a következő értékeket.

    |Mező  |Érték  |
    |---------|---------|
    |Trigger neve     | Az trigger egyedi neve.         |
    |Trigger típusa     | Válassza a **fájl** trigger lehetőséget. Egy fájl aktiválódik, amikor egy fájl esemény következik be, például egy fájl íródik a bemeneti megosztásba. Egy másik oldalon ütemezett eseményindító, amely az Ön által meghatározott ütemezés alapján következik be. Ebben a példában egy fájl-triggerre van szükségünk.    |
    |Bemeneti megosztás     | Válasszon egy bemeneti megosztást. Ebben az esetben a peremhálózat helyi megosztása a bemeneti megosztás. Az itt használt modul áthelyezi a fájlokat a peremhálózati helyi megosztásból egy peremhálózati megosztásba, ahol azokat a rendszer feltölti a felhőbe.        |

    ![Trigger hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. A trigger létrehozása után értesítést kap. Az eseményindítók listája frissült az újonnan létrehozott eseményindító megjelenítéséhez. Válassza ki az imént létrehozott triggert.

    ![Trigger hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Másolja és mentse a minta útvonalát. Ezt a mintavételezési útvonalat módosítani fogja, majd később a IoT Hubben kell használni.

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Trigger hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Modul hozzáadása

Ezen a peremhálózati eszközön nincsenek egyéni modulok. Hozzáadhat egy egyéni vagy egy előre elkészített modult. Az egyéni modulok létrehozásával kapcsolatos információkért tekintse meg a [ C# modul fejlesztése a Data Box Edge eszközhöz](data-box-edge-create-iot-edge-module.md)című témakört.

Ebben a szakaszban egy egyéni modult ad hozzá a IoT Edge eszközhöz, amelyet a [ C# modul fejlesztése a Data Box Edge számára](data-box-edge-create-iot-edge-module.md)című témakörben hozott létre. Ez az egyéni modul a peremhálózati eszköz peremhálózati helyi megosztásának fájljait veszi át, és áthelyezi őket egy peremhálózati (Felhőbeli) megosztásra az eszközön. A felhőalapú megosztás ezután leküldi a fájlokat a Felhőbeli megosztáshoz társított Azure Storage-fiókba.

1. Ugrás az **Edge-számításra > első lépések**. A **modulok hozzáadása** csempén válassza ki a forgatókönyv típusát **speciálisként**. Válassza **a IoT hub ugrás**lehetőséget.

    ![Speciális központi telepítés kiválasztása](./media/data-box-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. A IoT Hub erőforrásban nyissa meg **IoT Edge eszköz** elemet, majd válassza ki IoT Edge eszközét.

    ![Ugrás a IoT Edge eszközre IoT Hub](./media/data-box-edge-deploy-configure-compute-advanced/add-module-2.png)

3. Az **eszköz részletei**lapon válassza a **modulok beállítása**lehetőséget.

    ![A modulok beállítása hivatkozás](./media/data-box-edge-deploy-configure-compute-advanced/add-module-3.png)

4. A **modulok hozzáadása**területen tegye a következőket:

    1. Adja meg az egyéni modulhoz tartozó tároló beállításjegyzék-beállításainak nevét, nevét, felhasználónevét és jelszavát.
    A név, a cím és a felsorolt hitelesítő adatok a modulok megfelelő URL-címmel való lekérésére használatosak. A modul üzembe helyezéséhez az **Üzemelő példány moduljai** területen válassza ki az **IoT Edge-modult**. Ez a IoT Edge-modul egy Docker-tároló, amelyet a Data Box Edge eszközhöz társított IoT Edge eszközön telepíthet.

        ![A modulok beállítása lap](./media/data-box-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Adja meg az egyéni IoT Edge-modul beállításait. Adja meg a következő értékeket.
     
        |Mező  |Érték  |
        |---------|---------|
        |Name (Név)     | A modul egyedi neve. Ez a modul egy Docker-tároló, amelyet a Data Box Edgehoz társított IoT Edge eszközre telepíthet.        |
        |Rendszerkép URI-ja     | A modulhoz tartozó tároló rendszerképéhez tartozó képuri.        |
        |Szükséges hitelesítő adatok     | Ha be van jelölve, a rendszer a felhasználónevet és a jelszót használja a modulok megfelelő URL-címmel való lekéréséhez.        |
    
        A **tároló létrehozása beállítások** mezőben adja meg a peremhálózati modulok helyi csatlakoztatási pontjait, amelyeket az előző lépésekben másolt a peremhálózat-megosztáshoz és a peremhálózati helyi megosztáshoz.

        > [!IMPORTANT]
        > Az itt használt elérési utak a tárolóba vannak csatlakoztatva, ezért meg kell egyezniük a tárolóban lévő funkciókkal. Ha [egy egyéni modult](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code)követ, a modulban megadott kód a másolt elérési utakat várja. Ne módosítsa ezeket az elérési utakat.
    
        A **tároló létrehozása beállítások** mezőben a következő mintát szúrhatja be:
    
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

        Adja meg a modulhoz használt környezeti változókat. A környezeti változók olyan opcionális információkat biztosítanak, amelyek segítenek meghatározni a környezetet, amelyben a modul fut.

        ![A tároló létrehozása beállítások mező](./media/data-box-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Ha szükséges, konfigurálja a speciális peremhálózati futtatókörnyezet beállításait, majd kattintson a **tovább**gombra.

        ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5.  Az **útvonalak megadása**területen állítsa be a modulok közötti útvonalakat.  
    
    ![Az útvonalak meghatározása](./media/data-box-edge-deploy-configure-compute-advanced/add-module-7.png)

    Az *útvonalat* lecserélheti a korábban átmásolt következő útvonal-karakterláncra. Ebben a példában adja meg annak a helyi megosztásnak a nevét, amely a Felhőbeli megosztásba küldi az adattovábbítást. Cserélje le a `modulename`t a modul nevére. Kattintson a **Tovább** gombra.
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![Az útvonalak meghatározása szakasz](./media/data-box-edge-deploy-configure-compute-advanced/add-module-8.png)

6.  Az **üzembe helyezés áttekintése**területen tekintse át az összes beállítást, majd kattintson a **Submit (elküldés** ) gombra a modul központi telepítéshez való küldéséhez.

    ![A modulok beállítása lap](./media/data-box-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Ez a művelet elindítja a modul üzembe helyezését. Az üzembe helyezés befejezése után a modul **futtatókörnyezeti állapota** **fut**.

    ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Adatok átalakításának ellenőrzése, átvitel

Az utolsó lépés az, hogy ellenőrizze, hogy a modul csatlakoztatva van-e, és hogy a várt módon fut-e. A modul futtatási ideje állapotának futnia kell a IoT Edge eszközön a IoT Hub erőforrásban.

A következő lépésekkel ellenőrizheti az adatok átalakítását és az Azure-ba való átvitelt.
 
1.  A Fájlkezelőben a korábban létrehozott Edge helyi és peremhálózati megosztásokhoz is csatlakozhat.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1.  Adja hozzá az adatokat a helyi megosztáshoz.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    Az adatok áthelyeződnek a felhőalapú megosztásba.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Az adatok ezután a Felhőbeli megosztásról a Storage-fiókba kerülnek. Az adatmegjelenítéshez nyissa meg a Storage-fiókját, majd válassza a **Storage Explorer**lehetőséget. A feltöltött adatait megtekintheti a Storage-fiókjában.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Végrehajtotta az érvényesítési folyamatot.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Megosztások hozzáadása
> * Eseményindító hozzáadása
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

A Data Box Edge eszköz felügyeletének megismeréséhez lásd:

> [!div class="nextstepaction"]
> [A Data Box Edge felügyelete a helyi webes felhasználói felületen](data-box-edge-manage-access-power-connectivity-mode.md)
