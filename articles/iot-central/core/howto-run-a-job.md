---
title: Feladatok létrehozása és futtatása az Azure IoT Central alkalmazásban | Microsoft Docs
description: Az Azure IoT Central-feladatok lehetővé teszik a csoportos eszközök felügyeleti funkcióinak használatát, például az eszköz tulajdonságainak frissítését, a beállítások beállítását vagy egy parancs végrehajtását.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/08/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 114946fa37ae161aeb2efd5b7cd50444c5df4c2b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906707"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Feladatok létrehozása és futtatása az Azure IoT Central alkalmazásban

A csatlakoztatott eszközök méretezéséhez a feladatok használatával Microsoft Azure IoT Central is használhatja. A feladatok lehetővé teszik az eszközök tulajdonságainak és parancsainak tömeges frissítését. Ez a cikk végigvezeti a feladatok saját alkalmazásban való használatának megkezdésének lépésein.


## <a name="create-and-run-a-job"></a>Feladatok létrehozása és futtatása

Ebből a szakaszból megtudhatja, hogyan hozhat létre és futtathat feladatokat. Bemutatja, hogyan növelheti a ventilátor sebességét több hűtött árusító gép esetén.

1. Navigáljon a feladatok elemre a navigációs ablaktáblán.

2. Új feladatok létrehozásához válassza az **+ új** lehetőséget.

    ![Új feladatok létrehozása](./media/howto-run-a-job/createnewjob.png)

3. Adja meg a létrehozandó feladatot azonosító nevet és leírást.

4. Válassza ki azt az eszközt, amelyre alkalmazni szeretné a feladatot. Megtekintheti, hogy a rendszer hány eszközt alkalmaz a feladatok konfigurációjában az összefoglalás szakaszban. 

5. Ezután válassza ki a definiálni kívánt feladattípus (tulajdonság vagy parancs) típusát. Állítsa be a feladatok konfigurációját úgy, hogy kiválasztja a tulajdonságot, és új értékeket állít be, vagy válasszon egy parancsot. Egyszerre több tulajdonság is felvehető.

    ![Feladatok konfigurálása](./media/howto-run-a-job/configurejob.png)

6. Az eszközök kiválasztása után válassza a **Futtatás** vagy a **Mentés**lehetőséget. A feladat most megjelenik a fő **feladatok** oldalon. Ebben a nézetben láthatja a jelenleg futó feladatot és a korábban futtatott feladatok előzményeit. A futó feladatok mindig megjelennek a lista elejénél. A mentett feladatok bármikor megnyithatók a Szerkesztés vagy a Futtatás folytatásához.

    ![Feladatok megtekintése](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > A korábban futtatott feladatok előzményeit akár 30 napig is megtekintheti.

7. A feladatok áttekintéséhez válassza ki a listából a megtekinteni kívánt feladatot. Ez az Áttekintés a feladatok részleteit, az eszközöket és az Eszközállapot-értékeket tartalmazza. Ebből az áttekintésből kiválaszthatja a **feladatok részleteinek letöltése** lehetőséget is, ha le szeretné tölteni a feladatok részleteit tartalmazó. csv fájlt, beleértve az eszközöket és az állapotukat is. Ez az információ hasznos lehet a hibaelhárításhoz.

    ![Eszközállapot megtekintése](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Futó feladatok leállítása

Futó feladatok leállításához jelölje ki, majd válassza a **Leállítás**lehetőséget. A feladatok állapota úgy változik, hogy a rendszer leállítja a feladatot.

   ![Feladatok leállítása](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Leállított feladatok futtatása

Ha egy jelenleg leállított feladatot szeretne futtatni, válassza a leállított feladatot. Válassza a **Futtatás** lehetőséget a panelen. A feladatok állapota úgy változik, hogy az tükrözze a feladatot.

   ![Folytatási feladatok](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Feladatok másolása

Ha egy meglévő feladatot szeretne másolni, nyisson meg egy létrehozott feladatot, és válassza a **Másolás**lehetőséget. Ekkor megnyílik a feladatok konfigurációjának új másolata. Az új feladatot mentheti vagy futtathatja. 

   ![Másolási feladatok](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>A feladatok állapotának megtekintése

A feladatok létrehozása után az **állapot** oszlop a feladatokhoz tartozó legújabb állapotüzenetek szerint frissül. A következő táblázat felsorolja a lehetséges állapotinformációkat:

| Állapotüzenet       | Állapot jelentése                                          |
| -------------------- | ------------------------------------------------------- |
| Befejezve            | Ezt a feladatot minden eszközön végrehajtotta a rendszer.              |
| Meghiúsult               | A feladatot nem sikerült végrehajtani, és nem hajtották végre teljesen az eszközökön.  |
| Függőben              | Ez a feladattípus még nem kezdte meg az eszközökön való végrehajtást.         |
| Fut              | Ez a feladatok jelenleg az eszközökön vannak végrehajtva.             |
| Leállítva              | Ezt a feladatot a felhasználó manuálisan leállította.           |

Az állapotjelző üzenetet a feladatokban található eszközök áttekintése követi. A következő táblázat felsorolja a lehetséges Eszközállapot-értékeket:

| Állapotüzenet       | Állapot jelentése                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Sikeres            | Azon eszközök száma, amelyeken a feladatot sikeresen végrehajtották.       |
| Meghiúsult               | Azon eszközök száma, amelyeken nem sikerült végrehajtani a feladatot.       |

### <a name="view-the-device-status"></a>Az eszköz állapotának megtekintése

A feladatok és az összes érintett eszköz állapotának megtekintéséhez válassza ki a feladatot. Ha le szeretne tölteni egy. csv-fájlt, amely tartalmazza a feladatok részleteit, beleértve az eszközök listáját és az állapotukat is, válassza a **feladatok adatainak letöltése**lehetőséget. Az egyes eszközök neve mellett a következő állapotüzenetek egyike jelenik meg:

| Állapotüzenet       | Állapot jelentése                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Befejezve            | A feladatot végrehajtotta ezen az eszközön.                                     |
| Meghiúsult               | A feladatot nem sikerült végrehajtani az eszközön. A hibaüzenet további információkat jelenít meg.  |
| Függőben              | A feladatot még nem hajtották végre ezen az eszközön.                                   |

> [!NOTE]
> Ha egy eszköz törölve lett, nem választhatja ki az eszközt, és törölve jelenik meg az eszköz azonosítójával.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan hozhat létre feladatokat az Azure IoT Central alkalmazásban, néhány további lépés:

- [Eszközkészletek használata](howto-use-device-sets.md)
- [Saját eszközök kezelése](howto-manage-devices.md)
- [Az eszköz sablonjának verziója](howto-version-device-template.md)
