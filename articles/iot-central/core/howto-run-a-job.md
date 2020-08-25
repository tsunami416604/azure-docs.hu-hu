---
title: Feladatok létrehozása és futtatása az Azure IoT Central alkalmazásban | Microsoft Docs
description: Az Azure IoT Central-feladatok lehetővé teszik a csoportos eszközök felügyeleti funkcióinak használatát, például a tulajdonságok frissítését vagy egy parancs futtatását.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: 406881a9131aae35b91dcab248745bb426cecf0e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797836"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Feladatok létrehozása és futtatása az Azure IoT Central alkalmazásban

A csatlakoztatott eszközök méretezése a feladatok segítségével a Microsoft Azure IoT Central használatával végezhető el. A feladatok lehetővé teszik a tömeges frissítését az eszköz tulajdonságaira és a parancsok futtatására. Ez a cikk bemutatja, hogyan kezdheti el a feladatok használatát a saját alkalmazásban.

## <a name="create-and-run-a-job"></a>Feladatok létrehozása és futtatása

Ebből a szakaszból megtudhatja, hogyan hozhat létre és futtathat feladatokat a logisztikai átjáró eszközeinek egy csoportjának világos küszöbértékének beállítása formájában.

1. A bal oldali ablaktáblán válassza a **feladatok**lehetőséget.

2. Válassza a **+ Új** lehetőséget.

   ![A feladatok létrehozására szolgáló beállításokat bemutató képernyőkép.](./media/howto-run-a-job/create-new-job.png)

3. Adja meg a létrehozandó feladatot azonosító nevet és leírást.

4. Válassza ki azt a céleszköz-csoportot, amelyre alkalmazni szeretné a feladatot. Megtekintheti, hogy hány eszközre vonatkozik a feladatok konfigurációja az **Összefoglalás** szakaszban.

5. Válassza a **felhő tulajdonság**, **tulajdonság**vagy **parancs** lehetőséget a konfigurálandó feladattípusként. 

   A **Tulajdonságok** konfigurációjának beállításához válasszon ki egy tulajdonságot, és állítsa be az új értékét. A **parancs** -feladatok konfigurációjának beállításához válassza ki a futtatandó parancsot. A tulajdonság-feladatok több tulajdonságot is beállíthat.

   ![Képernyőkép, amely a set Light küszöbérték nevű tulajdonság-feladatok létrehozására szolgáló beállításokat jeleníti meg.](./media/howto-run-a-job/configure-job.png)

6. Válassza a **Futtatás** vagy a **Mentés**lehetőséget. A feladat most megjelenik a fő **feladatok** oldalon. Ezen az oldalon megtekintheti az aktuálisan futó feladatot és a korábban futtatott vagy mentett feladatok előzményeit. Bármikor újra megnyithatja a mentett feladatot a Szerkesztés folytatásához vagy a futtatásához.

   ![Képernyőkép, amely egy létrehozott feladatok nevét, állapotát és leírását jeleníti meg.](./media/howto-run-a-job/view-job.png)

   > [!NOTE]
   > A korábban futtatott feladatok esetében 30 nappal korábbi előzményeket tekinthet meg.

7. Válassza ki a mentett feladatot, és futtassa a **Futtatás** gombra kattintva. 

   Megjelenik a **feladatok futtatása?** párbeszédpanel. A művelet **futtatása** gombra kattintva erősítse meg a műveletet. 

   ![Képernyőfelvétel: a párbeszédpanel, amely megerősíti, hogy feladatot szeretne futtatni.](./media/howto-run-a-job/run-job.png)

8. A feladatok a függőben lévő, a futó és a befejezett fázisokon haladnak át. A feladatok végrehajtásának részletei az eredmény mérőszámait, az időtartam részleteit és az eszközök listájának rácsát tartalmazzák. 

   Ebből az áttekintésből kiválaszthatja az **eredmények naplóját** is, hogy letöltse a feladatok részleteit tartalmazó CSV-fájlt, beleértve az eszközöket és az állapotukat is. Ez az információ hasznos lehet a hibaelhárításhoz.

   ![Az eszköz állapotát megjelenítő képernyőkép.](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>Feladatok kezelése

Futó feladatok leállításához nyissa meg, majd válassza a **Leállítás**lehetőséget. A feladatok állapota azt jelzi, hogy a feladatot leállították. Az **Összefoglalás** szakaszban látható, hogy mely eszközök lettek végrehajtva, sikertelenek, vagy még függőben vannak.

![Képernyőkép, amely egy futó feladatot és a feladatok leállítására szolgáló gombot mutatja.](./media/howto-run-a-job/manage-job.png)

Miután a feladatok leállított állapotban vannak, a **Folytatás** gombra kattintva folytathatja a feladatok futtatását. A feladatok állapota úgy változik, hogy a feladatainak futása újra megtörténjen. Az **Összefoglalás** szakasz továbbra is frissül a legújabb folyamattal.

![A leállított feladatot megjelenítő képernyőkép, valamint a feladatok folytatására szolgáló gomb.](./media/howto-run-a-job/stopped-job.png)

## <a name="copy-a-job"></a>Feladatok másolása

Az egyik meglévő feladat másolásához válassza ki azt a **feladatok** lapon, és válassza a **feladat részletei**lehetőséget. Megjelenik a **feladathoz tartozó részletek** lap. 

![Képernyőfelvétel: a feladatok részleteit megjelenítő oldal.](./media/howto-run-a-job/job-details.png)

Válassza a **Másolás** lehetőséget.

![A másolás gombot megjelenítő képernyőkép.](./media/howto-run-a-job/job-details-copy.png)

Ekkor megnyílik a feladatok konfigurációjának egy másolata, amelyet **a rendszer a** feladatok nevéhez hozzáfűz. Az új feladatot mentheti vagy futtathatja.

![A feladatok konfigurációjának másolatát megjelenítő képernyőkép.](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>Feladat állapotának megtekintése

A feladatok létrehozása után az **állapot** oszlopot a rendszer a feladatokhoz tartozó legújabb állapotüzenetek szerint frissíti. A következő táblázat a lehetséges feladatok állapotának értékeit sorolja fel:

| Állapotüzenet       | Állapot jelentése                                          |
| -------------------- | ------------------------------------------------------- |
| Befejeződött            | Ez a feladatok minden eszközön futottak.              |
| Sikertelen               | A művelet sikertelen volt, és nem futott le teljesen az eszközökön.  |
| Függőben              | Ez a feladatok még nem kezdődött meg az eszközökön.         |
| Futó              | Ez a feladatok jelenleg az eszközökön futnak.             |
| Leállítva              | Egy felhasználó manuálisan leállította ezt a feladatot.           |

Az állapotjelző üzenetet a feladatokban található eszközök áttekintése követi. A következő táblázat felsorolja a lehetséges Eszközállapot-értékeket:

| Állapotüzenet       | Állapot jelentése                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Sikeres            | Azon eszközök száma, amelyeken a feladatok sikeresen futottak.       |
| Sikertelen               | Azon eszközök száma, amelyeken a feladatoknak nem sikerült futniuk.       |

A feladatok és az összes érintett eszköz állapotának megtekintéséhez nyissa meg a feladatot. Az egyes eszközök neve mellett a következő állapotüzenetek egyike jelenik meg:

| Állapotüzenet       | Állapot jelentése                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Befejeződött            | A feladatot futtatták ezen az eszközön.                                     |
| Sikertelen               | A feladatot nem sikerült futtatni ezen az eszközön. A hibaüzenet további információkat jelenít meg.  |
| Függőben              | A feladatot még nem futtatták ezen az eszközön.                                   |

Ha le szeretne tölteni egy CSV-fájlt, amely tartalmazza a feladatok részleteit, valamint az eszközök listáját és azok állapotát, válassza a **Letöltés**lehetőséget.

## <a name="filter-the-device-list"></a>Az eszközlista szűrése

A szűrő ikonra kattintva szűrheti az eszközök listáját a **feladatok részletei** oldalon. Szűrheti az **eszköz azonosítóját** vagy **állapotát** .

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Az eszközök listájának szűrésére szolgáló beállításokat megjelenítő képernyőkép.":::

## <a name="customize-columns-in-the-device-list"></a>Oszlopok testreszabása az eszközök listájában

Az oszlop beállításai ikon kiválasztásával további oszlopokat is megadhat az eszközök listájában.

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Az oszlop beállításainak ikonját megjelenítő képernyőkép":::

A párbeszédpanel segítségével kiválaszthatja az eszközök listájában megjelenítendő oszlopokat. Jelölje ki a megjeleníteni kívánt oszlopokat, válassza ki a jobbra mutató nyilat, majd kattintson az **OK gombra**. Az összes elérhető oszlop kiválasztásához jelölje be az **összes kijelölése**jelölőnégyzetet.

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="Képernyőkép, amely megjeleníti a megjelenítendő oszlopok kiválasztására szolgáló párbeszédpanelt.":::

A kijelölt oszlopok megjelennek az eszközök listájában.

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="Képernyőkép, amely megjeleníti a kijelölt oszlopokat az eszközök listájában.":::

A kijelölt oszlopok egy felhasználói munkamenetben vagy az alkalmazáshoz hozzáférő felhasználói munkamenetekben maradnak.

## <a name="rerun-jobs"></a>Feladatok újrafuttatása

Újrafuttathat egy hibás eszközöket tartalmazó feladatot. Válassza **az újrafuttatás sikertelen**lehetőséget.

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Képernyőfelvétel: a feladatoknak a hibás eszközökön való újbóli futtatására szolgáló gomb.":::

Adja meg a feladatok nevét és leírását, majd válassza a **feladatok újrafuttatása**lehetőséget. A rendszer elküld egy új feladatot, hogy megismételje a műveletet a meghiúsult eszközökön.

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="A sikertelen eszközök újrafuttatására szolgáló párbeszédpanelt megjelenítő képernyőkép.":::

> [!NOTE]
> Egy Azure IoT Central alkalmazásból egyszerre legfeljebb öt feladatot futtathat.
>
> Ha a feladatok befejeződik, és törli a feladathoz tartozó eszközök listáján szereplő eszközt, az eszköz neve törölve lesz az eszköz nevében. A részletek hivatkozás nem érhető el a törölt eszközhöz.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan hozhat létre feladatokat az Azure IoT Central alkalmazásban, néhány további lépés:

- [Eszközök kezelése](howto-manage-devices.md)
- [Az eszköz sablonjának verziója](howto-version-device-template.md)
