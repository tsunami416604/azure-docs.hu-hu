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
ms.openlocfilehash: bb5eb2904ae969c3ffcd4ee8e365a51853add2fd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182234"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Hozzon létre és futtathat feladatokat az Azure IoT központi alkalmazás

A Microsoft Azure IoT Central segítségével a csatlakoztatott eszközök nagy mennyiségű-feladatok használata kezelheti. A feladatok funkció lehetővé teszi, hogy az eszköz tulajdonságait, beállításait és parancsok tömeges frissítése. Ez a cikk végigvezeti hogyan kezdheti el a feladatok használata a saját alkalmazásokban.

## <a name="create-and-run-a-job"></a>Hozzon létre, és a egy feladat futtatása

Ez a szakasz bemutatja, hogyan hozhat létre és futtathat feladatokat. Az egyes lépések halad végig egy példa, amely bemutatja, hogyan futtathat feladatokat kell rendelkeznie a nagyobb ventilátor sebességét hűtött Eladóautomata eszközök esetében.

1. Lépjen a feladatok a navigációs ablaktáblán.

1. Válassza ki **+ új** új feladatok létrehozásának megkezdéséhez.

    ![Új feladat létrehozása](./media/howto-run-a-job/createnewjob.png)

1. Adjon meg egy nevet és leírást, amely segít azonosítani a feladat létrehozásakor.

1. Válassza ki az eszköz beállítása azt szeretné, hogy a feladat a alkalmazni lehessen. Miután az eszköz kiválasztásával, lásd: jobb oldalán adja meg az eszközök a kiválasztott eszköz csoporton belül. Ha egy hibás eszköz set, nincsenek eszközök megjelenítéséhez, és megjelenik egy üzenet tájékoztatja, hogy az eszköz beállítása megszakad.

1. Ezután válassza ki a feladat (a beállítás, tulajdonság vagy parancs) meghatározására. Válassza ki **+** mellett milyen típusú feladatot kiválasztva, és adja hozzá a kívánt műveleteket.

    ![Feladat konfigurálása](./media/howto-run-a-job/configurejob.png)

1. A jobb oldalon kiválasztani, az eszközök, amelyet szeretne futtatni a feladatot. A felső jelölőnégyzet bejelölésével, minden eszköz ki van jelölve, a teljes eszköz csoportba. A jelölőnégyzet bejelölésével közel **neve**, az aktuális oldalon lévő összes eszköz ki van jelölve.

1. Miután az eszközök, válassza ki a **futtatása** vagy **mentése**. A feladat most már megjelenik a fő **feladatok** lapot. Ebben a nézetben megtekintheti az éppen futó feladat és a korábban a feladatok futtatása tetszőleges előzmények. A futó feladat mindig megjelenik-e a lista tetején. A mentett feladat segítségével lehet megnyitni, bármikor újból folytathatja a szerkesztést vagy futtatásához.

    ![Feladat megtekintése](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Megtekintheti a korábban futtatott feladatok előzményeit legfeljebb 30 napig.

1. A feladat áttekintésére, válassza a listában megtekinteni kívánt feladat nevét. Ez az Áttekintés a feladat részleteit, eszközök és eszköz állapota értékeket tartalmazza. Ez az Áttekintés is kiválaszthatja **töltse le a feladat részletei** , a feladat részletei, beleértve az eszközök és az állapot értékei .csv-fájl letöltésére. Ez az információ akkor lehet hasznos, a hibaelhárításhoz.

    ![Eszközállapot megtekintése](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Egy futó feladat leállítása

Szeretné a jelenleg futó feladat leállítása, ha válassza ki, amelyeket szeretne, állítsa le a futó feladat nevét. Válassza ki a **leállítása** gombra a panel. Láthatja, hogy a feladat állapota megváltozott megfelelően, hogy a feladat le lett állítva.

   ![Feladat leállítása](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>A leállt feladat futtatása

Szeretne futtatni egy feladatot, amely jelenleg le van állítva, válassza ki a leállt feladatot, amely a futtatni kívánt nevét. Válassza ki a **futtatása** gombra a panel. A feladat állapotát, hogy fut a feladat most ismét megfelelően módosul.

   ![Feladat folytatása](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Egy feladat másolása

Létrehozott egy meglévő feladat másolásához válassza ki a legfontosabb feladatok lapján, és válassza **másolási**. Ekkor megnyílik egy új példányát szerkesztését a feladat konfigurációját. Mentse, vagy az új feladat futtatása. Ha a kiválasztott eszköz csoport bármely módosultak, azokat a szerkesztését a másolt feladat is megjelennek.

   ![Másolási feladat](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>A feladat állapotának megtekintéséhez

Egy feladat létrehozását követően a **állapot** oszlop frissítések a feladat legfrissebb állapotának üzenettel. Az állapotüzenetek a következőket jelentik:

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

A feladat minden egyes eszköz állapotának megtekintéséhez válassza ki a feladat nevét. A feladat és összes egy részét képező eszköz részleteit láthatja. Választhat **feladat részleteinek letöltése** a feladat részleteit, az eszközöket és azok állapot értékeit tartalmazó .csv-fájl letöltésére. Minden egyes eszköz neve mellett jelenik meg az alábbi állapot üzenetek:

| Állapotüzenet       | Állapot jelentése                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Befejezve            | A feladat végrehajtva az eszközön.                                     |
| Meghiúsult               | A feladat nem sikerült végrehajtani ezen az eszközön. A hibaüzenetben további információkat jelenít meg.  |
| Függőben              | A feladat nem rendelkezik még végrehajtani ezen az eszközön.                                  |

> [!NOTE]
> Ha egy eszköz törölve lett, nem választhat ki az eszközt, és azt jeleníti meg, mert törölték az eszköz azonosítójával. a

## <a name="next-steps"></a>További lépések

Most, hogy, hogyan feladatok létrehozása az Azure IoT Central alkalmazásban, az alábbiakban további lépések:

- [Eszközkészletek használata](howto-use-device-sets.md)
- [Saját eszközök kezelése](howto-manage-devices.md)
- [Verzióját az eszköz sablon](howto-version-devicetemplate.md)
