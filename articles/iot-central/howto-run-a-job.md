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
ms.openlocfilehash: 6c18a244ceae2ccd9a536abeb6bc2d85760bb0a6
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875910"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Feladatok létrehozása és futtatása az Azure IoT Central alkalmazásban

A csatlakoztatott eszközök méretezéséhez a feladatok használatával Microsoft Azure IoT Central is használhatja. A feladatok lehetővé teszik az eszközök tulajdonságainak, beállításainak és parancsainak tömeges frissítését. Ez a cikk végigvezeti a feladatok saját alkalmazásban való használatának megkezdésének lépésein.

## <a name="create-and-run-a-job"></a>Feladatok létrehozása és futtatása

Ebből a szakaszból megtudhatja, hogyan hozhat létre és futtathat feladatokat. Bemutatja, hogyan növelheti a ventilátor sebességét több hűtött árusító gép esetén.

1. Navigáljon a feladatok elemre a navigációs ablaktáblán.

1. Új feladatok létrehozásához válassza az **+ új** lehetőséget.

    ![Új feladatok létrehozása](./media/howto-run-a-job/createnewjob.png)

1. Adja meg a létrehozandó feladatot azonosító nevet és leírást.

1. Válassza ki azt az eszközt, amelyre alkalmazni szeretné a feladatot. Miután kiválasztotta az eszközt, megjelenik a jobb oldali feltöltés az eszközbeállítások eszközeivel. Ha kijelöl egy hibás eszközt, az eszköz nem jelenik meg, és megjelenik egy üzenet, amely szerint az eszköz meg van törve.

1. Ezután válassza ki a definiálni kívánt feladattípust (beállítás, tulajdonság vagy parancs). Válassza **+** a tovább lehetőséget a kiválasztott feladatok típusához, és adja hozzá a műveleteit.

    ![Feladatok konfigurálása](./media/howto-run-a-job/configurejob.png)

1. A jobb oldalon válassza ki azokat az eszközöket, amelyeken futtatni szeretné a feladatot. A felső jelölőnégyzet bejelölésével az összes eszköz kiválasztásra kerül a teljes eszköz készletében. A **név**melletti jelölőnégyzet bejelölésével az aktuális oldalon lévő összes eszköz ki lesz választva.

1. Az eszközök kiválasztása után válassza a **Futtatás** vagy a **Mentés**lehetőséget. A feladat most megjelenik a fő **feladatok** oldalon. Ebben a nézetben láthatja a jelenleg futó feladatot és a korábban futtatott feladatok előzményeit. A futó feladatok mindig megjelennek a lista elejénél. A mentett feladatok bármikor megnyithatók a Szerkesztés vagy a Futtatás folytatásához.

    ![Feladat megtekintése](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > A korábban futtatott feladatok előzményeit akár 30 napig is megtekintheti.

1. A feladatok áttekintéséhez válassza ki a listából a megtekinteni kívánt feladatot. Ez az Áttekintés a feladatok részleteit, az eszközöket és az Eszközállapot-értékeket tartalmazza. Ebből az áttekintésből kiválaszthatja a **feladatok részleteinek letöltése** lehetőséget is, ha le szeretné tölteni a feladatok részleteit tartalmazó. csv fájlt, beleértve az eszközöket és az állapotukat is. Ez az információ hasznos lehet a hibaelhárításhoz.

    ![Eszközállapot megtekintése](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Futó feladatok leállítása

Futó feladatok leállításához jelölje ki azt, és válassza a **Leállítás** lehetőséget a panelen. A feladatok állapota úgy változik, hogy a rendszer leállítja a feladatot.

   ![Feladatok leállítása](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Leállított feladatok futtatása

Ha egy jelenleg leállított feladatot szeretne futtatni, válassza a leállított feladatot. Válassza a **Futtatás** lehetőséget a panelen. A feladatok állapota úgy változik, hogy az tükrözze a feladatot.

   ![Folytatási feladatok](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Feladatok másolása

Egy már létrehozott feladat másolásához válassza ki azt a fő feladatok lapon, és válassza a **Másolás**lehetőséget. Ekkor megnyílik a feladatok konfigurációjának új másolata. Az új feladatot mentheti vagy futtathatja. Ha a kiválasztott eszközön bármilyen módosítás történt, a rendszer ezeket a másolt feladatokban jeleníti meg a szerkesztéshez.

   ![Másolási feladatok](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>A feladatok állapotának megtekintése

A feladatok létrehozása után az **állapot** oszlop a feladatokhoz tartozó legújabb állapotüzenetek szerint frissül. A következő táblázat felsorolja a lehetséges állapotinformációkat:

| Állapotüzenet       | Állapot jelentése                                          |
| -------------------- | ------------------------------------------------------- |
| Befejeződött            | Ezt a feladatot minden eszközön végrehajtotta a rendszer.              |
| Meghiúsult               | A feladatot nem sikerült végrehajtani, és nem hajtották végre teljesen az eszközökön.  |
| Függőben lévő              | Ez a feladattípus még nem kezdte meg az eszközökön való végrehajtást.         |
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
| Befejeződött            | A feladatot végrehajtotta ezen az eszközön.                                     |
| Meghiúsult               | A feladatot nem sikerült végrehajtani az eszközön. A hibaüzenet további információkat jelenít meg.  |
| Függőben lévő              | A feladatot még nem hajtották végre ezen az eszközön.                                   |

> [!NOTE]
> Ha egy eszköz törölve lett, nem választhatja ki az eszközt, és törölve jelenik meg az eszköz azonosítójával.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan hozhat létre feladatokat az Azure IoT Central alkalmazásban, néhány további lépés:

- [Eszközkészletek használata](howto-use-device-sets.md)
- [Saját eszközök kezelése](howto-manage-devices.md)
- [Az eszköz sablonjának verziója](howto-version-device-template.md)
