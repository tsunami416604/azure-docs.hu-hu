---
title: Oktatóanyag a szűréshez, az adatok elemzéséhez az Azure Data Box Edge számítási adataival | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhatja a számítási szerepkört a Data Box Edge-en, és hogyan alakíthatja át az adatokat a speciális üzembe helyezési folyamathoz az Azure-ba való küldés előtt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b446a3ebf92f6240d3bc02a148fbb8296efec926
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239041"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-for-advanced-deployment-flow"></a>Oktatóanyag: Adatok átalakítása az Azure Data Box Edge segítségével a speciális telepítési folyamathoz

Ez az oktatóanyag ismerteti, hogyan konfigurálhat számítási szerepkört egy speciális központi telepítési folyamat az Azure Data Box Edge-eszközön. A számítási szerepkör konfigurálása után a Data Box Edge átalakíthatja az adatokat, mielőtt elküldené az Azure-ba.

A számítási konfigurálható az egyszerű vagy speciális központi telepítési folyamathoz az eszközön.

|                  | Egyszerű telepítés                                | Speciális telepítés                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Célja, hogy     | Rendszergazdák                                | Fejlesztők                            |
| Típus             | Modulok üzembe helyezése a Data Box Edge szolgáltatással      | Modulok üzembe helyezése az IoT Hub szolgáltatással |
| Telepített modulok | Egyirányú                                           | Láncolt vagy több modul           |


Ez az eljárás körülbelül 20-30 percet is igénybe vehet.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Megosztások hozzáadása
> * Eseményindító hozzáadása
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

 
## <a name="prerequisites"></a>Előfeltételek

Mielőtt beállítegy számítási szerepkört a Data Box Edge eszközén, győződjön meg arról, hogy:

- Aktiválta a Data Box Edge-eszközt a [Csatlakozás, beállítás és az Azure Data Box Edge aktiválása szerint.](data-box-edge-deploy-connect-setup-activate.md)


## <a name="configure-compute"></a>Számítás konfigurálása

A Data Box Edge számítási konfigurálásához hozzon létre egy IoT Hub-erőforrást.

1. A Data Box Edge-erőforrás Azure-portálján nyissa **meg az Áttekintés című témakört.** A jobb oldali **ablaktáblában,** a Számítási csempe, válassza a **Kezdési lehetőséget**.

    ![A számítás – első lépések](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. Az **Edge számításkonfigurálása csempén** válassza **a Számítás konfigurálása**lehetőséget.

    ![A számítás – első lépések](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. Az **Edge konfigurálása számítási** panelen adja meg a következőket:

   
    |Mező  |Érték  |
    |---------|---------|
    |IoT Hub     | Válasszon az **Új** vagy **a Meglévő közül.** <br> Alapértelmezés szerint egy standard szint (S1) egy IoT-erőforrás létrehozásához használható. Ingyenes szintű IoT-erőforrás használatához hozzon létre egyet, majd válassza ki a meglévő erőforrást. <br> Minden esetben az IoT Hub-erőforrás ugyanazt az előfizetést és erőforráscsoportot használja, amelyet a Data Box Edge erőforrás használ.     |
    |Név     |Adja meg az IoT Hub-erőforrás nevét.         |

    ![A számítás – első lépések](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Kattintson a **Létrehozás** gombra. Az IoT Hub-erőforrás létrehozása néhány percet vesz igénybe. Az IoT Hub-erőforrás létrehozása után az Edge számítási csempe **konfigurálása** csempe frissítések et a számítási konfiguráció megjelenítéséhez. Annak ellenőrzéséhez, hogy az Edge számítási szerepkör konfigurálva van-e, válassza a **Konfiguráció megtekintése lehetőséget** a Számítási csempe **konfigurálása** csempén.
    
    ![A számítás – első lépések](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    Amikor az Edge számítási szerepkör be van állítva az Edge-eszközön, két eszközt hoz létre: egy IoT-eszközt és egy IoT Edge-eszközt. Mindkét eszköz megtekinthető az IoT Hub-erőforrásban. Egy IoT Edge-futtató, amely ezen az IoT Edge-eszközön is fut.

    Ezen a ponton csak a Linux platform érhető el az IoT Edge-eszköz.


## <a name="add-shares"></a>Megosztások hozzáadása

Az oktatóanyag speciális üzembe helyezéséhez két megosztásra van szükség: egy Edge-megosztásra és egy másik Edge helyi megosztásra.

1. Az alábbi lépésekkel adjon hozzá egy Edge-megosztást az eszközön:

    1. A Data Box Edge erőforrásban nyissa meg **az Edge számítási > az Első lépések**.
    2. A **Megosztás hozzáadása (k)** csempén válassza a **Hozzáadás**lehetőséget.
    3. A **Megosztás hozzáadása** panelen adja meg a megosztás nevét, és válassza ki a megosztás típusát.
    4. Az Edge megosztás csatlakoztatásához jelölje be **a Megosztás használata az Edge compute segítségével jelölőnégyzetet.**
    5. Válassza a **Storage fiók**, **Storage service**, egy meglévő felhasználó, majd válassza a **Create**lehetőséget.

        ![Szegélymegosztás hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    Az Edge-megosztás létrehozása után sikeres létrehozási értesítést kap. A megosztási lista frissül, hogy tükrözze az új megosztást.

2. Adjon hozzá egy Edge helyi megosztást az Edge-eszközön az előző lépés összes lépésének megismétlésével, és jelölje be a **Beállítás edge helyi megosztásként jelölőnégyzetet.** A helyi megosztásban lévő adatok az eszközön maradnak.

    ![Edge helyi megosztás hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. A **Megosztások** panelen megjelenik a megosztások frissített listája.

    ![Megosztások frissített listája](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Az újonnan létrehozott helyi megosztás tulajdonságainak megtekintéséhez válassza ki a megosztást a listából. Az **Edge számítási modulok helyi csatlakoztatási pontjában** másolja az ennek a megosztásnak megfelelő értéket.

    Ezt a helyi csatlakoztatási pontot fogja használni a modul telepítésekor.

    ![A "Helyi csatlakoztatási pont az Edge számítási modulokhoz" mező](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. A létrehozott Szegélymegosztás tulajdonságainak megtekintéséhez jelölje ki a megosztást a listából. Az **Edge számítási modulok helyi csatlakoztatási pontjában** másolja az ennek a megosztásnak megfelelő értéket.

    Ezt a helyi csatlakoztatási pontot fogja használni a modul telepítésekor.

    ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Eseményindító hozzáadása

1. Nyissa meg az **Edge számítási > eseményindítók .** Válassza **a + Trigger hozzáadása**lehetőséget.

    ![Trigger hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. Az **Eseményindító hozzáadása** panelen adja meg a következő értékeket.

    |Mező  |Érték  |
    |---------|---------|
    |Eseményindító neve     | Az eseményindító egyedi neve.         |
    |Trigger típusa     | Válassza **a Fájleseményindító** lehetőséget. A fájl eseményindító akkor aktiválódik, amikor fájlesemény történik, például egy fájl írása a bemeneti megosztásba. Az ütemezett eseményindító ezzel szemben az Ön által meghatározott ütemezés alapján aktiválódik. Ebben a példában szükségünk van egy fájl eseményindító.    |
    |Bemeneti megosztás     | Jelöljön ki egy bemeneti megosztást. Az Edge helyi megosztás a bemeneti részesedés ebben az esetben. Az itt használt modul áthelyezi a fájlokat az Edge helyi megosztásáról egy Edge-megosztásra, ahol feltöltik őket a felhőbe.        |

    ![Trigger hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. Az eseményindító létrehozása után értesítést kap. Az eseményindítók listája frissül az újonnan létrehozott eseményindító megjelenítéséhez. Válassza ki az imént létrehozott eseményindítót.

    ![Trigger hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Másolja és mentse a mintaútvonalat. Ezt a mintaútvonalat módosítani fogja, és később az IoT Hubban fogja használni.

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Trigger hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Modul hozzáadása

Ezen az Edge-eszközön nincsenek egyéni modulok. Hozzáadhat egy egyéni vagy egy előre elkészített modult. Ha meg szeretné tudni, hogyan hozhat létre egyéni modult, olvassa el [a C# modul fejlesztése a Data Box Edge eszközhöz](data-box-edge-create-iot-edge-module.md)című lehetőséget.

Ebben a szakaszban egy egyéni modult ad hozzá az IoT Edge-eszközhöz, amelyet a C# modul fejlesztése a [Data Box Edge.In](data-box-edge-create-iot-edge-module.md)this section, you add a custom module to the IoT Edge device that you created in Develop a C# module for your Data Box Edge . Ez az egyéni modul az Edge-eszköz edge-i helyi megosztásáról fájlokat vesz fel, és áthelyezi őket egy Edge (felhőbeli) megosztásra az eszközön. A felhőmegosztás ezután lelöki a fájlokat az Azure storage-fiókba, amely a felhőmegosztáshoz van társítva.

1. Nyissa meg az **Edge számítási > Az első lépések**. A **Modulok hozzáadása** csempén válassza ki a forgatókönyv típusát **speciálisként.** Válassza **az Ugrás az IoT Hubra**lehetőséget.

    ![Speciális telepítés kiválasztása](./media/data-box-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. Az IoT Hub-erőforrás, az **IoT Edge-eszköz,** majd válassza ki az IoT Edge-eszközt.

    ![Ugrás az IoT Edge-eszközre az IoT Hubban](./media/data-box-edge-deploy-configure-compute-advanced/add-module-2.png)

3. Az **Eszköz részletei képernyőn**válassza a Modulok **beállítása**lehetőséget.

    ![A Modulok beállítása hivatkozás](./media/data-box-edge-deploy-configure-compute-advanced/add-module-3.png)

4. A **Modulok hozzáadása csoportban**tegye a következőket:

    1. Adja meg az egyéni modul tárolóbeállítási beállításainak nevét, címét, felhasználónevét és jelszavát.
    A név, a cím és a felsorolt hitelesítő adatok a megfelelő URL-címmel rendelkező modulok lekéréséhez használatosak. A modul üzembe helyezéséhez az **Üzemelő példány moduljai** területen válassza ki az **IoT Edge-modult**. Ez az IoT Edge-modul egy docker-tároló, amely telepítheti az IoT Edge-eszköz, amely a Data Box Edge-eszköz höz társított.

        ![A Modulok beállítása lap](./media/data-box-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Adja meg az egyéni IoT Edge-modul beállításait. Adja meg a következő értékeket.
     
        |Mező  |Érték  |
        |---------|---------|
        |Név     | A modul egyedi neve. Ez a modul egy docker-tároló, amely a Data Box Edge-hez társított IoT Edge-eszközre telepíthető.        |
        |Kép URI-ja     | A modul megfelelő tárolórendszerképének lemezképe.        |
        |Hitelesítő adatok szükségesek     | Ha be van jelölve, a felhasználónév és a jelszó a megfelelő URL-címmel rendelkező modulok beolvasására szolgál.        |
    
        A **Tároló létrehozása beállítások** mezőbe írja be az Edge-modulok helyi csatlakoztatási pontjait, amelyeket az Edge megosztás és az Edge helyi megosztás előző lépéseiben másolt.

        > [!IMPORTANT]
        > Az itt használt elérési utak a tárolóba vannak csatlakoztatva, így meg kell egyezniük a tárolóban elvárt funkciókkal. Ha [az Egyéni modul létrehozása](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code)lehetőséget követi, a modulban megadott kód a másolt elérési utakra számít. Ne módosítsa ezeket az elérési utakat.
    
        A **Tároló létrehozása beállításai** párbeszédpanelen a következő mintát illesztheti be:
    
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

        Adja meg a modulhoz használt környezeti változókat. A környezeti változók opcionális információkat nyújtanak, amelyek segítenek meghatározni azt a környezetet, amelyben a modul fut.

        ![A Tároló létrehozása beállításai párbeszédpanel](./media/data-box-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Ha szükséges, adja meg a speciális Edge futásidejű beállításokat, majd kattintson a **Tovább**gombra.

        ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5.  Az **Útvonalak megadása**csoportban adja meg a modulok közötti útvonalakat.  
    
    ![A megadott útvonalak](./media/data-box-edge-deploy-configure-compute-advanced/add-module-7.png)

    Az *útvonalat* lecserélheti a korábban másolt következő útvonalkarakterláncra. Ebben a példában adja meg a nevét a helyi megosztás, amely leküldéses adatokat a felhőmegosztásra. Cserélje `modulename` le a modul nevére. Válassza a **Tovább lehetőséget.**
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![Az Útvonalak megadása szakasz](./media/data-box-edge-deploy-configure-compute-advanced/add-module-8.png)

6.  A **Központi telepítés áttekintése**csoportban tekintse át az összes beállítást, majd válassza a **Küldés** lehetőséget a modul központi telepítésre való elküldéséhez.

    ![A Modulok beállítása lap](./media/data-box-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Ez a művelet elindítja a modul központi telepítését. A telepítés befejezése után a modul **Futásidejű állapota** **fut.**

    ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Adatátalakítás ellenőrzése, átvitel

Az utolsó lépés annak biztosítása, hogy a modul csatlakoztatva van, és fut a várt módon. A modul futásidejű állapotának futnia kell az IoT Edge-eszköz az IoT Hub-erőforrásban.

Az alábbi lépésekkel ellenőrizheti az adatok átalakítását és az Azure-ba való átvitelt.
 
1.  A Fájlkezelőben csatlakozzon a korábban létrehozott Edge helyi és edge megosztásokhoz is.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1.  Adja hozzá az adatokat a helyi megosztáshoz.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    Az adatok áthelyeződnek a felhőalapú megosztásba.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Az adatok ezután leküldéses a felhőmegosztásról a tárfiókba. Az adatok megtekintéséhez nyissa meg a tárfiókot, és válassza a **Tárház**lehetőséget. Megtekintheti a feltöltött adatokat a tárfiókban.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Befejezte az ellenőrzési folyamatot.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Megosztások hozzáadása
> * Eseményindító hozzáadása
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

A Data Box Edge-eszköz felügyeletéről a következő témakörben olvashat:

> [!div class="nextstepaction"]
> [A Data Box Edge felügyelete a helyi webes felhasználói felületen](data-box-edge-manage-access-power-connectivity-mode.md)
