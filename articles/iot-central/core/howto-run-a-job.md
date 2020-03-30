---
title: Feladatok létrehozása és futtatása az Azure IoT Central alkalmazásban | Microsoft dokumentumok
description: Az Azure IoT Central-feladatok lehetővé teszik a tömeges eszközkezelési képességek, például a tulajdonságok frissítése vagy egy parancs végrehajtása.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/03/2020
ms.topic: how-to
manager: peterpr
ms.openlocfilehash: c9d5a0daa364b09e45699e898511c28d4b4d92ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157755"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Feladat létrehozása és futtatása az Azure IoT Central alkalmazásban

A Microsoft Azure IoT Central segítségével a csatlakoztatott eszközöket nagy méretekben kezelheti a feladatok használatával. A feladatok lehetővé teszik az eszköz tulajdonságainak tömeges frissítését és a parancsok futtatását. Ez a cikk bemutatja, hogyan kezdheti el használni a feladatokat a saját alkalmazásában.

## <a name="create-and-run-a-job"></a>Feladat létrehozása és futtatása

Ez a szakasz bemutatja, hogyan hozhat létre és futtathatja a feladatot. Bemutatja, hogyan állíthatja be a fényküszöbértéket a logisztikai átjáróeszközök egy csoportjához.

1. Keresse meg a **Feladatok** elemet a bal oldali ablaktáblából.

2. Új feladat létrehozásához válassza a **+ Új** lehetőséget:

    ![Új feladat létrehozása](./media/howto-run-a-job/createnewjob.png)

3. Adja meg a létrehozott feladatot.

4. Válassza ki azt a céleszközcsoportot, amelyre alkalmazni szeretné a feladatot. Az Összegzés szakaszban **láthatja,** hogy a feladat konfigurációja hány eszközre vonatkozik.

5. Ezután válassza a **Konfigurálandó**feladat típusaként a Cloud tulajdonság , **a Tulajdonság** vagy a **Command** lehetőséget. **Tulajdonságfeladat-konfiguráció** beállításához jelöljön ki egy tulajdonságot, és állítsa be annak új értékét. **A Parancs**beállításához válassza a futtatni kívánt parancsot. Egy tulajdonságfeladat több tulajdonságot is beállíthat:

    ![Feladat konfigurálása](./media/howto-run-a-job/configurejob.png)

6. A feladat létrehozása után válassza a **Futtatás** vagy **a Mentés**lehetőséget. A feladat most megjelenik a fő **Feladatok** oldalon. Ezen az oldalon láthatja az éppen futó feladatot, valamint a korábban futtatott vagy mentett feladatok előzményeit. A mentett feladat bármikor újra megnyitható a szerkesztés folytatásához vagy futtatásához:

    ![Feladat megtekintése](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > A korábban futtatott feladatok 30 napos előzményeket tekinthet meg.

7. A feladat áttekintéséhez válassza ki a listából megtekinteni kívánt feladatot. Ez az áttekintés tartalmazza a feladat részleteit, az eszközöket és az eszköz állapotértékeit. Ebből az áttekintésből a **Feladat részleteinek letöltése** lehetőséget is választva letöltheti a feladat részleteinek CSV-fájlját, beleértve az eszközöket és azok állapotértékeit. Ez az információ hibaelhárításhoz hasznos lehet:

    ![Eszközállapot megtekintése](./media/howto-run-a-job/downloaddetails.png)

### <a name="manage-a-job"></a>Feladat kezelése

Az egyik futó feladat leállításához nyissa meg, és válassza a **Leállítás**gombot. A feladat állapota a feladatnak megfelelően megváltozik. Az **Összegzés** szakasz megmutatja, hogy mely eszközök fejezték be, nem sikerült vagy még függőben vannak.

Az éppen leállított feladat futtatásához jelölje ki, majd válassza a **Futtatás**lehetőséget. A feladat állapota megváltozik, hogy tükrözze a feladat most fut újra. Az **Összegzés** szakasz továbbra is frissül a legújabb folyamattal.

![Feladat kezelése](./media/howto-run-a-job/managejob.png)

## <a name="copy-a-job"></a>Feladat másolása

Ha az egyik meglévő feladatot szeretné másolni, jelölje ki azt a **Feladatok** lapon, és válassza a **Másolás lehetőséget.** Megnyílik a feladat konfigurációjának egy példánya, amelyet szerkesztheti, és a **másolás** hozzáfűzi a feladat nevét. Az új feladatot mentheti vagy futtathatja:

![Feladat másolása](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>A feladat állapotának megtekintése

A feladat létrehozása után az **Állapot** oszlop frissül a feladat legújabb állapotüzenetével. Az alábbi táblázat a lehetséges állapotértékeket sorolja fel:

| Állapotüzenet       | Állapot jelentése                                          |
| -------------------- | ------------------------------------------------------- |
| Befejezve            | Ez a feladat minden eszközön végrehajtásra került.              |
| Sikertelen               | Ez a feladat nem sikerült, és nem teljesen hajtották végre az eszközökön.  |
| Függőben              | Ez a feladat még nem kezdte meg az eszközökön történő végrehajtást.         |
| Fut              | Ez a feladat jelenleg az eszközökön hajt végre.             |
| Leállítva              | Ezt a feladatot egy felhasználó manuálisan leállította.           |

Az állapotüzenetet a feladatban lévő eszközök áttekintése követi. Az alábbi táblázat az eszköz lehetséges állapotértékeit sorolja fel:

| Állapotüzenet       | Állapot jelentése                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Sikeres            | A feladat sikeresen végrehajtott eszközeinek száma.       |
| Sikertelen               | Azon eszközök száma, amelyeken a feladat végrehajtása sikertelen volt.       |

### <a name="view-the-device-status"></a>Az eszköz állapotának megtekintése

A feladat és az összes érintett eszköz állapotának megtekintéséhez nyissa meg a feladatot. Ha olyan CSV-fájlt szeretne letölteni, amely tartalmazza a feladat részleteit, beleértve az eszközök listáját és azok állapotértékeit, válassza a **Feladat részleteinek letöltése**lehetőséget. Az egyes eszközök neve mellett az alábbi állapotüzenetek egyike látható:

| Állapotüzenet       | Állapot jelentése                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Befejezve            | A feladat végrehajtása ezen az eszközön történt.                                     |
| Sikertelen               | A feladat végrehajtása nem sikerült ezen az eszközön. A hibaüzenet további információkat jelenít meg.  |
| Függőben              | A feladat még nem hajtottvégre ezen az eszközön.                                   |

> [!NOTE]
> Ha egy eszközt töröltek, nem választhatja ki az eszközt. Az eszközazonosítóval töröltként jelenik meg.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan hozhat létre munkahelyeket az Azure IoT Central alkalmazásban, íme néhány következő lépés:

- [Eszközök kezelése](howto-manage-devices.md)
- [Az eszközsablon verziója](howto-version-device-template.md)
