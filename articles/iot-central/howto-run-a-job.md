---
title: Hozzon létre és feladatok futtatása az Azure IoT Central alkalmazásban |} A Microsoft Docs
description: Az Azure IoT Central-feladatok lehetővé teszik a tömeges eszköz felügyeleti képességei, például egy adott eszköztulajdonság frissítése, a beállítás vagy egy parancs végrehajtása.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 09/15/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: f08167b4f37193c9a04da02fdd31e066a97aec66
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317662"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Hozzon létre és futtathat feladatokat az Azure IoT központi alkalmazás

A Microsoft Azure IoT Central segítségével a csatlakoztatott eszközök nagy mennyiségű-feladatok használata kezelheti. A feladatok funkció lehetővé teszi, hogy az eszköz tulajdonságait, beállításait és parancsok tömeges frissítése. Ez a cikk végigvezeti hogyan kezdheti el a feladatok használata a saját alkalmazásokban.

## <a name="create-and-run-a-job"></a>Hozzon létre, és a egy feladat futtatása

Ez a szakasz bemutatja, hogyan hozhat létre és futtathat feladatokat. Az egyes lépések halad végig egy példa, amely bemutatja, hogyan futtathat feladatokat kell rendelkeznie a nagyobb ventilátor sebességét hűtött Eladóautomata eszközök esetében.

1. Lépjen a feladatok a navigációs ablaktáblán.

1. Válassza ki **+ új** új feladatok létrehozásának megkezdéséhez.

    ![Új feladat létrehozása](./media/howto-run-a-job/createnewjob.png)

1. Adjon meg egy nevet és leírást, amely segít azonosítani a feladat létrehozásakor.

1. Válassza ki az eszköz beállítása azt szeretné, hogy a feladat a alkalmazni lehessen. Miután az eszköz kiválasztásával, látni fogja a jobb oldalán adja meg az eszközök a kiválasztott eszköz csoporton belül. Ha egy hibás eszköz set, eszközöket nem jelenik meg, és megjelenik egy üzenet tájékoztatja, hogy az eszköz beállítása megszakad.

1. Következő lépésként válassza ki, milyen típusú feladatot, amely lesz meghatározva (a beállítás, tulajdonság vagy parancsot). Válassza ki **+** mellett milyen típusú feladatot kiválasztva, és adja hozzá a kívánt műveleteket.

    ![Feladat konfigurálása](./media/howto-run-a-job/configurejob.png)

1. A jobb oldalon kiválasztani, az eszközök, amelyet szeretne futtatni a feladatot. A felső jelölőnégyzet bejelölésével, minden eszköz ki van jelölve, a teljes eszköz csoportba. Közel nevét a jelölőnégyzet bejelölésével, az aktuális oldalon lévő összes eszköz ki van jelölve.

1. A kívánt eszközök kiválasztása után válassza ki a **futtatása**. A feladat most már megjelenik a fő **feladatok** lapot. Az ebben a nézetben megtekintheti az éppen futó feladat és a korábban a feladatok futtatása tetszőleges előzmények áll. A futó feladat mindig a lista tetején fog megjelenni.

    ![Feladat futtatása](./media/howto-run-a-job/runjob.png)

    ![Feladat megtekintése](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Megtekintheti a korábban futtatott feladatok előzményeit legfeljebb 30 napig fogja.

1. A feladat áttekintésére, válassza a listában megtekinteni kívánt feladat nevét. Ez az Áttekintés a feladat részletei, az eszközök és az eszközök állapotának tartalmazza.

    ![Eszközállapot megtekintése](./media/howto-run-a-job/viewdevicestatus.png)

### <a name="stop-a-running-job"></a>Egy futó feladat leállítása

Szeretné a jelenleg futó feladat leállítása, ha válassza ki, amelyeket szeretne, állítsa le a futó feladat nevét. Válassza ki a **leállítása** gombra a panel. Láthatja, hogy a feladat állapota megváltozott megfelelően, hogy a feladat le lett állítva.

   ![Feladat leállítása](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>A leállt feladat futtatása

Ha szeretne futtatni egy feladatot, amely jelenleg le van állítva, válassza ki a leállt feladatot, amely a futtatni kívánt nevét. Válassza ki a **futtatása** gombra a panel. Látni fogja, hogy a feladat állapota módosult-e, hogy fut a feladat most ismét megfelelően.

   ![Feladat folytatása](./media/howto-run-a-job/resumejob.png)

## <a name="view-the-job-status"></a>A feladat állapotának megtekintéséhez

Egy feladat létrehozása után a **állapot** oszlop a rendszer ekkor frissíti a feladat legfrissebb állapotüzenet. Az állapotüzenetek a következőket jelentik:

| Állapotüzenet       | Állapot jelentése                                          |
| -------------------- | ------------------------------------------------------- |
| Befejezve            | Ez a feladat végrehajtását követően az összes eszközön.              |
| Meghiúsult               | Ez a feladat nem sikerült és az eszközökön nincs teljes egészében végrehajtani.  |
| Függőben              | Ez a feladat még nem kezdődött végrehajtása az eszközökön.        |
| Fut              | Ez a feladat végrehajtása folyamatban van az eszközökön.             |
| Leállítva              | Ez a feladat a felhasználó manuálisan leállították.           |

Az állapotüzenet az eszközök a feladaton belül áttekintést követ. Ezek az eszközök állapotának a következőket jelentik:

| Állapotüzenet       | Állapot jelentése                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Sikeres            | A feladat sikeresen kvótaerőforráshoz az eszközök száma.  |
| Meghiúsult               | Eszközök, amelyek a feladat nem tudta végrehajtani a száma.      |

### <a name="view-the-device-status"></a>Az eszköz állapotának megtekintése

Annak érdekében, hogy a feladat minden egyes eszköz állapotának megtekintéséhez válassza ki a feladat nevét. Itt megjelenik a feladat és az eszközök, az adott feladatot egy részét képező összes részleteit. Minden egyes eszköz neve mellett a következő állapotüzeneteket egyik láthatja:

| Állapotüzenet       | Állapot jelentése                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Befejezve            | A feladat végrehajtva az eszközön.                                     |
| Meghiúsult               | A feladat nem sikerült végrehajtani ezen az eszközön. További információt a hibaüzenetben jelennek meg.  |
| Függőben              | A feladat nem rendelkezik még végrehajtani ezen az eszközön.                                  |

> [!NOTE]
> Eszköz törölve lett, akkor az eszközt, és jeleníti meg, mert törölték az eszköz azonosítójával. a

## <a name="next-steps"></a>További lépések

Most, hogy, hogyan feladatok létrehozása az Azure IoT Central alkalmazásban, az alábbiakban további lépések:

- [Eszközkészletek használata](howto-use-device-sets.md)
- [Saját eszközök kezelése](howto-manage-devices.md)
- [Verzióját az eszköz sablon](howto-version-devicetemplate.md)
