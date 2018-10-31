---
title: Adatok átalakítása az Azure Data Box Edge segítségével | Microsoft Docs
description: Megismerheti, hogyan konfigurálhatja a számítási szerepköröket a Data Box Edge-ben, és hogyan alakíthatja át az adatokat, mielőtt beküldené őket az Azure-nak.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: ba77fc4596d9bb245b3cea2538804b1816e9ad14
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466970"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>Oktatóanyag: Adatok átalakítása az Azure Data Box Edge segítségével (előzetes verzió)

Az oktatóanyag ismerteti, hogyan konfigurálhat számítási szerepköröket a Data Box Edge-ben. Miután konfigurált egy számítási szerepkört, a Data Box Edge képessé válik az adatok átalakítására, mielőtt beküldené őket az Azure-nak.

A folyamat elvégzése körülbelül 30-45 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * IoT Hub-erőforrás létrehozása
> * Számítási szerepkör beállítása
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

> [!IMPORTANT]
> A Data Box Edge előzetes verzióban érhető el. A megoldás megrendelése és üzembe helyezése előtt tekintse át az [Azure előzetes verziókra vonatkozó szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="prerequisites"></a>Előfeltételek

Mielőtt beállítaná a számítást a Data Box Edge-ben, ellenőrizze az alábbiakat:

* A Data Box Edge-eszköz aktiválva van az [Azure Data Box Edge csatlakoztatását és aktiválását](data-box-edge-deploy-connect-setup-activate.md) ismertető cikkben leírt módon.


## <a name="create-an-iot-hub-resource"></a>IoT Hub-erőforrás létrehozása

Mielőtt beállítaná a számítási szerepkört a Data Box Edge-ben, létre kell hoznia egy IoT Hub-erőforrást.

Részletes útmutatásért lásd az [új IoT Hub létrehozását](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub) ismertető témakört. Használja ugyanazt az előfizetést és erőforráscsoportot, amelyet a Data Box Edge-erőforráshoz is használt.

![IoT Hub-erőforrás létrehozása](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

Ha az Edge számítási szerepkör még nincs beállítva, vegye figyelembe az alábbiakat:

- Az IoT Hub-erőforrás nem rendelkezik IoT-eszközökkel vagy IoT Edge-eszközökkel.
- Nem hozhat létre helyi Edge-megosztásokat. Amikor megosztást ad hozzá, nincs engedélyezve az a lehetőség, amely engedélyezné a helyi megosztások létrehozását az Edge-számításokhoz.


## <a name="set-up-compute-role"></a>Számítási szerepkör beállítása

Amikor az Edge számítási szerepkört beállítja az Edge-eszközön, két eszköz jön létre - az egyik egy IoT-, a másik egy IoT Edge-eszköz. Mindkét eszköz megtekinthető az IoT Hub-erőforrásban.

Az alábbi lépésekkel állíthat be számítási szerepkört az eszközön.

1. Nyissa meg a Data Box Edge-erőforrást, nyissa meg az **áttekintési** oldalt, majd kattintson a **Számítási szerepkör beállítása** elemre. 

    ![Számítási szerepkör beállítása](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    Azt is megteheti, hogy a **Modulok** oldalra lép, és rákattint a **Számítás konfigurálása** gombra.

    ![Számítási szerepkör beállítása](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
2. A legördülő menüből válassza ki az előző lépésben létrehozott **IoT Hub-erőforrást**. Az IoT Edge-eszközön ezen a ponton csak a Linux platform érhető el. Kattintson a **Create** (Létrehozás) gombra.

    ![Számítási szerepkör beállítása](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
3. A számítási szerepkör létrehozása néhány percet vesz igénybe. A jelenlegi kiadás hibája miatt a képernyő akkor sem frissül, ha a számítási szerepkör már létrejött. Lépjen a **Modulok** oldalra, ahol ellenőrizheti, hogy az Edge-számítás konfigurálva van-e.  

    ![Számítási szerepkör beállítása](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

4. Lépjen újra az **Áttekintés** oldalra. A képernyő most már frissül, és jelzi, hogy a számítási szerepkör konfigurálva van.

    ![Számítási szerepkör beállítása](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
5. Lépjen az Edge számítási szerepkör létrehozásakor használt IoT Hubhoz. Lépjen az **IoT-eszközökre**. Láthatja, hogy egy IoT-eszköz már engedélyezve van. 

    ![Számítási szerepkör beállítása](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

6. Lépjen az **IoT Edge** részhez, ahol láthatja, hogy egy IoT Edge-eszköz is engedélyezve van.

    ![Számítási szerepkör beállítása](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
7. Válassza ki az IoT Edge-eszközt, és kattintson rá. Ezen az IoT Edge-eszközön egy Edge-ügynök fut. 

    ![Számítási szerepkör beállítása](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

Az Edge-eszköz egyelőre nem tartalmaz egyéni modulokat, azokat most tudja hozzáadni. Az egyéni modulok létrehozásának megismeréséért lépjen [a C#-modulok a Data Box Edge-hez való fejlesztését ismertető szakaszra](data-box-edge-create-iot-edge-module.md).


## <a name="add-a-custom-module"></a>Egyéni modul hozzáadása

Ebben a szakaszban egy egyéni modult fog hozzáadni [a C#-modulok a Data Box Edge-hez való fejlesztését ismertető szakaszban](data-box-edge-create-iot-edge-module.md) létrehozott IoT Edge-eszközhöz. 

Az eljárás egy olyan példát használ, amelyben az egyéni modul az Edge-eszköz helyi megosztásából fájlokat helyez át az eszköz felhőalapú megosztásába. A felhőalapú megosztás ezután a vele társított Azure-tárfiókba küldi tovább a fájlokat. 

1. Az első lépés a helyi megosztás hozzáadása az Edge-eszközhöz. A Data Box Edge-erőforrásban menjen a **Megosztások** oldalra. Kattintson a **+ Megosztás hozzáadása** gombra. Adja meg a megosztás nevét, és válassza ki a megosztás típusát. Helyi megosztás létrehozásához jelölje ki a **helyi Edge-megosztásként történő konfigurálás** jelölőnégyzetét. Válasszon ki egy **meglévő felhasználót**, vagy **hozzon létre egy újat**. Kattintson a **Create** (Létrehozás) gombra.

    ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    Ha egy helyi NFS-megosztást hozott létre, használja a következő rsync parancslehetőséget, hogy a fájlokat a megosztásra másolja:

    `rsync --inplace <source file path> < destination file path>`

     További információt az rsync parancsról az [Rsync dokumentációjában](https://www.computerhope.com/unix/rsync.htm) talál. 

 
2. Amint létrehozta a helyi megosztást, és értesítést kapott a sikeres létrehozásról (előfordulhat, hogy a megosztási lista azelőtt is frissíthető, de meg kell várni, amíg befejeződik a megosztás létrehozása), nyissa meg a megosztások listáját. 

    ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
3. Válassza ki az újonnan létrehozott helyi megosztást, kattintson rá, és tekintse át a megosztás tulajdonságait. Másolja ki és mentse el a megosztáshoz tartozó **Edge-modulok helyi csatlakozási pontját**.

    ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
    Lépjen egy meglévő felhőalapú megosztáshoz a Data Box Edge-eszközén. Itt is másolja ki és mentse el a felhőalapú megosztáshoz tartozó számítási Edge-modulok helyi csatlakozási pontját. A rendszer ezeket a helyi csatlakozási pontokat alkalmazza a modul üzembe helyezésekor.

    ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

4. Ha egyéni modult akar hozzáadni IoT Edge-eszközéhez, lépjen az IoT Hub-erőforráshoz, majd az **IoT Edge-eszközhöz**. Válassza ki az eszközt, és kattintson rá. Az **eszközadatoknál** kattintson a **Modulok beállítása** gombra a felső parancssorban. 

    ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

5. A **Modulok hozzáadása** területen tegye az alábbiakat:

    1. Adja meg a **nevet**, **címet**, **felhasználónevet** és **jelszót** az egyéni modul **Container Registry beállításaihoz**. A nevet, a címet és a hitelesítő adatokat a rendszer az egyező URL-címmel rendelkező modulok lekéréséhez használja. A modul üzembe helyezéséhez az **Üzemelő példány moduljai** területen válassza ki az **IoT Edge-modult**. Ez az IoT Edge-modul egy Docker-tároló, amit üzembe helyezhet a Data Box Edge-eszközhöz társított IoT Edge-eszközön.

        ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    2. Adja meg az egyéni IoT Edge-modul beállításait. Adja meg a modul **nevét** és a hozzá tartozó tárolórendszerkép **képi URI-ját**. 
    
        ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    3. A **Tároló-létrehozási beállításoknál** adja meg az Edge-modulok korábbi lépésekben kimásolt, felhőalapú és helyi megosztásokhoz tartozó helyi csatlakozási pontjait (fontos, hogy ezeket az elérési utakat használja újak létrehozása helyett). A helyi csatlakozási pontok a megfelelő **InputFolderPath** és **OutputFolderPath** útvonalakra vannak leképezve, amelyeket [a modul egyéni kóddal való frissítésekor](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code) határozott meg a modulban. 
    
        Az alább látható mintát bemásolhatja a **Tároló-létrehozási beállításokhoz**: 
        
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

        A modul minden környezeti változóját is meg kell adni.

        ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    4. Ha szükséges, **adja meg az Edge-futtatókörnyezet speciális beállításait**, majd kattintson a **Tovább** gombra.

        ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
6.  Az **Útvonalak megadása** területen állítsa be a modulok közötti útvonalakat. Jelen példában adja meg annak a helyi megosztásnak a nevét, ami a felhőalapú megosztásba küldi majd le az adatokat. Kattintson a **Tovább** gombra.

    Az útvonalat lecserélheti a következő útvonalsztringre:       "route": "FROM /* WHERE topic = 'mysmblocalshare' INTO BrokeredEndpoint(\"/modules/filemovemodule/inputs/input1\")"

    ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 
 
7.  Az **Üzembe helyezés áttekintése** területen tekintse át a beállításokat, és ha elégedett, **küldje el** a modult üzembe helyezésre.

    ![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
Ez elindítja a modul üzembe helyezésének folyamatát, ahogy azt az **egyéni IoT Edge-modul** is jelzi a **Modulok** területen.

![Egyéni modul hozzáadása](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>Adatok átalakításának ellenőrzése és adatok átvitele

Utolsó lépésként győződjön meg arról, hogy a modul csatlakoztatva van, és a várakozásoknak megfelelően fut. Az alábbi lépésekkel ellenőrizheti, hogy a modul fut-e.

1. A modul futtatókörnyezetbeli állapota „running” (futó) kell, hogy legyen az IoT Hub-erőforrásban lévő IoT Edge-eszközén.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
2. Válassza ki a modult, kattintson rá, és nézze meg a **Modul ikeridentitását**. Az Edge-eszköz és a modul ügyfélállapotának **Connected** (csatlakoztatott) értékűnek kell lennie.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
3.  Ha a modul már fut, a Data Box Edge-erőforrásban található Edge-modulok listáján is megjelenik. A hozzáadott modul **futtatókörnyezeti állapota** **futó**.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
4.  A Fájlkezelőben csatlakozzon a létrehozott helyi és felhőalapú megosztáshoz.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
5.  Adja hozzá az adatokat a helyi megosztáshoz.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
6.  Az adatok áthelyeződnek a felhőalapú megosztásba.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

7.  Az adatok ezután a felhőalapú megosztásból továbbítódnak a tárfiókba. Az adatok megtekintéséhez lépjen a Storage Explorerbe.

    ![Adatok átalakításának ellenőrzése](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
Ezzel az érvényesítés végére ért.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Data Box Edge-hez kötődő alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * IoT Hub-erőforrás létrehozása
> * Számítási szerepkör beállítása
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

A következő oktatóanyag azt mutatja be, hogyan felügyelhető a Data Box Edge.

> [!div class="nextstepaction"]
> [A Data Box Edge felügyelete a helyi webes felhasználói felületen](http://aka.ms/dbg-docs)


