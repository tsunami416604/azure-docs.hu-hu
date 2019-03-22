---
title: Hozzon létre és feladatok futtatása az Azure IoT Central alkalmazásban |} A Microsoft Docs
description: Az Azure IoT Central-feladatok lehetővé teszik a tömeges eszköz felügyeleti képességei, például egy adott eszköztulajdonság frissítése, a beállítás vagy egy parancs végrehajtása.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/18/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: ec7033719316bb186408ea78f6dabac43c383491
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199851"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Hozzon létre és futtathat feladatokat az Azure IoT központi alkalmazás

A Microsoft Azure IoT Central segítségével a csatlakoztatott eszközök nagy mennyiségű-feladatok használata kezelheti. Feladatok lehetővé teszik a tömeges eszköz tulajdonságait, beállításait és parancsok frissítéseit. Ez a cikk végigvezeti hogyan kezdheti el a feladatok használata a saját alkalmazásokban.

## <a name="create-and-run-a-job"></a>Hozzon létre, és a egy feladat futtatása

Ez a szakasz bemutatja, hogyan hozhat létre és futtathat feladatokat. Ez bemutatja, hogyan a több hűtött eladóautomaták ventilátor sebességének növelése érdekében.

1. Lépjen a feladatok a navigációs ablaktáblán.

1. Válassza ki **+ új** létrehoz egy új feladatot.

    ![Új feladat létrehozása](./media/howto-run-a-job/createnewjob.png)

1. Adjon meg egy nevet és leírást a hoz létre feladat azonosításához.

1. Válassza ki az eszköz beállítása a feladatot a alkalmazni szeretné. Miután az eszköz kiválasztásával, adja meg az eszköz beállítása az eszközök jobb oldalán láthatja. Ha egy hibás eszköz set, nincsenek eszközök megjelenítéséhez, és megjelenik egy üzenet, hogy az eszköz beállítása megszakad.

1. Ezután válassza ki a feladat (a beállítás, tulajdonság vagy parancs) meghatározására. Válassza ki **+** mellett milyen típusú feladatot kiválasztva, és adja hozzá a műveletek.

    ![Feladat konfigurálása](./media/howto-run-a-job/configurejob.png)

1. A jobb oldalon válassza ki az eszközök, amelyet szeretne futtatni a feladatot. A felső jelölőnégyzet bejelölésével, minden eszköz ki van jelölve, a teljes eszköz csoportba. A jelölőnégyzet bejelölésével közel **neve**, az aktuális oldalon lévő összes eszköz ki van jelölve.

1. Miután az eszközök, válassza ki a **futtatása** vagy **mentése**. A feladat most már megjelenik a fő **feladatok** lapot. Ebben a nézetben megtekintheti az éppen futó feladat és a korábban a feladatok futtatása tetszőleges előzmények. A futó feladat mindig megjelenik-e a lista tetején. A mentett feladat segítségével lehet megnyitni, bármikor újból folytathatja a szerkesztést vagy futtatásához.

    ![Feladat megtekintése](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Megtekintheti a korábban futtatott feladatok előzményeit legfeljebb 30 napig.

1. A feladat áttekintésére, válassza ki a feladat megtekintéséhez a listából. Ez az Áttekintés a feladat részleteit, eszközök és eszköz állapota értékeket tartalmazza. Ez az Áttekintés is kiválaszthatja **töltse le a feladat részletei** , a feladat részletei, beleértve az eszközök és az állapot értékei .csv-fájl letöltésére. Ez az információ akkor lehet hasznos, a hibaelhárításhoz.

    ![Eszközállapot megtekintése](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Egy futó feladat leállítása

Egy futó feladat leállításához jelölje ki azt, és válassza a **leállítása** panelen. A feladat állapota módosul, hogy a feladat le van állítva.

   ![Feladat leállítása](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>A leállt feladat futtatása

Szeretne futtatni egy feladatot, amely jelenleg le van állítva, válassza ki a leállt feladatot. Válasszon **futtatása** panelen. A feladat állapota módosul, hogy a feladat most ismét futtatni.

   ![Feladat folytatása](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Egy feladat másolása

Létrehozott egy meglévő feladat másolásához válassza ki a legfontosabb feladatok lapján, és válassza **másolási**. A feladat konfigurálása egy új példányát nyitja meg a szerkesztését. Mentse, vagy az új feladat futtatása. Ha a kiválasztott eszköz csoport bármely módosultak, azokat a szerkesztését a másolt feladat van tükrözi.

   ![Másolási feladat](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>A feladat állapotának megtekintéséhez

Egy feladat létrehozása után a **állapot** oszlop frissítések a feladat legfrissebb állapotának üzenettel. Az alábbi táblázat a lehetséges állapot értékeit sorolja fel:

| Állapotüzenet       | Állapot jelentése                                          |
| -------------------- | ------------------------------------------------------- |
| Befejezve            | Ez a feladat végrehajtását követően az összes eszközön.              |
| Meghiúsult               | Ez a feladat nem sikerült és az eszközökön nincs teljes egészében végrehajtani.  |
| Függőben              | Ez a feladat még nem kezdődött végrehajtása az eszközökön.         |
| Fut              | Ez a feladat végrehajtása folyamatban van az eszközökön.             |
| Leállítva              | Ez a feladat a felhasználó manuálisan leállították.           |

Az állapotüzenet az eszközök a feladat áttekintése követ. Az alábbi táblázat a lehetséges állapot értékeit sorolja fel:

| Állapotüzenet       | Állapot jelentése                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Sikeres            | A feladat sikeresen végrehajtva az eszközök száma.       |
| Meghiúsult               | A feladat nem tudta végrehajtani a eszközök száma.       |

### <a name="view-the-device-status"></a>Az eszköz állapotának megtekintése

A feladat és az érintett eszközök állapotának megtekintéséhez válassza ki a feladatot. Válassza ki a feladat adatait, például az állapot értékekre, és eszközök listáját tartalmazó .csv-fájl letöltésére **feladat részleteinek letöltése**. Minden egyes eszköz neve mellett jelenik meg az alábbi állapot üzenetek:

| Állapotüzenet       | Állapot jelentése                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Befejezve            | A feladat végrehajtva az eszközön.                                     |
| Meghiúsult               | A feladat nem sikerült végrehajtani ezen az eszközön. A hibaüzenet további információkat jelenít meg.  |
| Függőben              | A feladat még az eszköz még nem futottak.                                   |

> [!NOTE]
> Ha egy eszköz törölve lett, nem választhat ki az eszközt, és azt jeleníti meg, mert törölték az eszköz azonosítójával. a

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a feladatok létrehozása az Azure IoT Central alkalmazásban, az alábbiakban további lépések:

- [Eszközkészletek használata](howto-use-device-sets.md)
- [Saját eszközök kezelése](howto-manage-devices.md)
- [Verzióját az eszköz sablon](howto-version-devicetemplate.md)
