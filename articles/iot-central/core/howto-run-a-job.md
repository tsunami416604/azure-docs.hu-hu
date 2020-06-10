---
title: Feladatok létrehozása és futtatása az Azure IoT Central alkalmazásban | Microsoft Docs
description: Az Azure IoT Central-feladatok lehetővé teszik a csoportos eszközök felügyeleti funkcióit, például a tulajdonságok frissítését vagy a parancsok végrehajtását.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: dec9abc38bc0354ef3d22994a7988bfb006f5769
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84609742"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Feladatok létrehozása és futtatása az Azure IoT Central alkalmazásban

A csatlakoztatott eszközök méretezéséhez a feladatok használatával Microsoft Azure IoT Central is használhatja. A feladatok lehetővé teszik a tömeges frissítését az eszköz tulajdonságaira és a parancsok futtatására. Ez a cikk bemutatja, hogyan kezdheti el a feladatok használatát a saját alkalmazásban.

## <a name="create-and-run-a-job"></a>Feladatok létrehozása és futtatása

Ebből a szakaszból megtudhatja, hogyan hozhat létre és futtathat feladatokat. Bemutatja, hogyan állíthatja be a fény küszöbértékét a logisztikai átjáró eszközeinek csoportjához.

1. Navigáljon a **feladatok** elemre a bal oldali ablaktáblán.

2. Új feladatok létrehozásához válassza az **+ új** elemet:

    ![Új feladatok létrehozása](./media/howto-run-a-job/create-new-job.png)

3. Adja meg a létrehozandó feladatot azonosító nevet és leírást.

4. Válassza ki azt a céleszköz-csoportot, amelyre alkalmazni szeretné a feladatot. Megtekintheti, hogy hány eszközre vonatkozik a feladatok konfigurációja az **Összefoglalás** szakaszban.

5. Ezután válassza a **felhő tulajdonság**, **tulajdonság**vagy **parancs** lehetőséget a konfigurálandó feladattípusként. A **Tulajdonságok** konfigurációjának beállításához válasszon ki egy tulajdonságot, és állítsa be az új értékét. **Parancs**beállításához válassza ki a futtatandó parancsot. A tulajdonság-feladatok több tulajdonságot is beállíthat:

    ![Feladatok konfigurálása](./media/howto-run-a-job/configure-job.png)

6. A feladatot a létrehozás után válassza a **Futtatás** vagy a **Mentés**lehetőséget. A feladat most megjelenik a fő **feladatok** oldalon. Ezen az oldalon megtekintheti az aktuálisan futó feladatot és a korábban futtatott vagy mentett feladatok előzményeit. A mentett feladatok bármikor megnyithatók a Szerkesztés folytatásához vagy a futtatásához:

    ![Feladatok megtekintése](./media/howto-run-a-job/view-job.png)

    > [!NOTE]
    > A korábban futtatott feladatok esetében 30 nappal korábbi előzményeket tekinthet meg.

7. A feladatok áttekintéséhez válassza ki a listából a megtekinteni kívánt feladatot. Ez az Áttekintés a feladatok részleteit, az eszközöket és az Eszközállapot-értékeket tartalmazza. Ebből az áttekintésből kiválaszthatja a feladatok **részleteinek letöltése** lehetőséget is, ha le szeretné tölteni a feladatok részleteit tartalmazó CSV-fájlt, beleértve az eszközöket és az állapotukat is. Ezek az információk a hibaelhárításhoz hasznosak lehetnek:

    ![Eszközállapot megtekintése](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>Feladatok kezelése

Az egyik futó feladat leállításához nyissa meg, majd válassza a **Leállítás**lehetőséget. A feladatok állapota úgy változik, hogy a rendszer leállítja a feladatot. Az **Összefoglalás** szakaszban látható, hogy mely eszközök lettek végrehajtva, sikertelenek, vagy még függőben vannak.

A jelenleg leállított feladatok futtatásához jelölje ki azt, majd válassza a **Futtatás**lehetőséget. A feladatok állapota úgy változik, hogy az tükrözze a feladatot. Az **Összefoglalás** szakasz a legújabb folyamattal folytatja a frissítést.

![Feladatok kezelése](./media/howto-run-a-job/manage-job.png)

## <a name="copy-a-job"></a>Feladatok másolása

Az egyik meglévő feladat másolásához jelölje ki azt a **feladatok** lapon, majd válassza a **Másolás**lehetőséget. Ekkor megnyílik a feladatok konfigurációjának egy másolata, amelyet **a rendszer a** feladatok nevéhez hozzáfűz. Az új feladatot az alábbiak szerint mentheti vagy futtathatja:

![Másolási feladatok](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>Feladat állapotának megtekintése

A feladatok létrehozása után az **állapot** oszlop a feladatokhoz tartozó legújabb állapotüzenetek szerint frissül. A következő táblázat felsorolja a lehetséges állapotinformációkat:

| Állapotüzenet       | Állapot jelentése                                          |
| -------------------- | ------------------------------------------------------- |
| Befejeződött            | Ezt a feladatot minden eszközön végrehajtotta a rendszer.              |
| Sikertelen               | A feladatot nem sikerült végrehajtani, és nem hajtották végre teljesen az eszközökön.  |
| Függőben              | Ez a feladattípus még nem kezdte meg az eszközökön való végrehajtást.         |
| Fut              | Ez a feladatok jelenleg az eszközökön vannak végrehajtva.             |
| Leállítva              | Ezt a feladatot a felhasználó manuálisan leállította.           |

Az állapotjelző üzenetet a feladatokban található eszközök áttekintése követi. A következő táblázat felsorolja a lehetséges Eszközállapot-értékeket:

| Állapotüzenet       | Állapot jelentése                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Sikeres            | Azon eszközök száma, amelyeken a feladatot sikeresen végrehajtották.       |
| Sikertelen               | Azon eszközök száma, amelyeken nem sikerült végrehajtani a feladatot.       |

### <a name="view-the-device-status-values"></a>Eszközállapot-értékek megtekintése

A feladatok és az összes érintett eszköz állapotának megtekintéséhez nyissa meg a feladatot. Az egyes eszközök neve mellett a következő állapotüzenetek egyike jelenik meg:

| Állapotüzenet       | Állapot jelentése                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Befejeződött            | Az eszközön végrehajtott feladatok.                                     |
| Sikertelen               | A feladatot nem sikerült végrehajtani az eszközön. A hibaüzenet további információkat jelenít meg.  |
| Függőben              | A feladatot még nem hajtották végre ezen az eszközön.                                   |

Ha le szeretne tölteni egy CSV-fájlt, amely tartalmazza a feladatok részleteit, valamint az eszközök listáját és azok állapotát, válassza a **Letöltés**lehetőséget.

### <a name="filter-the-list-of-devices"></a>Az eszközök listájának szűrése

A szűrő ikonra kattintva szűrheti az eszközök listáját a feladatok részletei oldalon. Szűrheti az **eszköz azonosítóját** vagy az **állapot** mezőit:

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Az eszközlista szűrése":::

### <a name="customize-columns-in-the-device-list"></a>Oszlopok testreszabása az eszközök listájában

Az oszlop beállításai ikon kiválasztásával további oszlopokat is megadhat az eszközök listájában:

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Oszlop beállításai":::

Ekkor megjelenik egy párbeszédpanel, amely lehetővé teszi az eszközök listájában megjelenítendő oszlopok kiválasztását. Jelölje ki a megjeleníteni kívánt oszlopokat, válassza a jobbra mutató nyilat, majd kattintson az **OK gombra**. Az összes elérhető oszlop kiválasztásához jelölje be az **összes kijelölése**:

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="Oszlop kiválasztása párbeszédpanel":::

A kijelölt oszlopok megjelennek az eszközök listáján:

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="Oszlopok kiválasztása":::

A kijelölt oszlopok egy felhasználói munkamenetben vagy az alkalmazáshoz hozzáférő felhasználói munkamenetekben maradnak.

## <a name="rerun-jobs"></a>Feladatok újrafuttatása

Újrafuttathat egy hibás eszközöket tartalmazó feladatot. Válassza az **újrafuttatás**lehetőséget:

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Feladatok újrafuttatása":::

Adja meg a feladatok nevét és leírását, majd válassza a **feladatok újrafuttatása**lehetőséget. A rendszer elküld egy új feladatot a sikertelen eszközökön a művelet megismétléséhez:

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="Sikertelen eszközök újrafuttatása":::

> [!NOTE]
> Egy IoT Central alkalmazásból egyszerre legfeljebb öt feladatot lehet végrehajtani.

> [!NOTE]
> Ha a feladatok befejeződik, és törli a feladathoz tartozó eszközök listáján szereplő eszközt, az eszköz neve és az eszköz részletei hivatkozása nem érhető el a törölt eszközhöz.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan hozhat létre feladatokat az Azure IoT Central alkalmazásban, néhány további lépés:

- [Eszközök kezelése](howto-manage-devices.md)
- [Az eszköz sablonjának verziója](howto-version-device-template.md)
