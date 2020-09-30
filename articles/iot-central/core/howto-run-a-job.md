---
title: Feladatok létrehozása és futtatása az Azure IoT Central alkalmazásban | Microsoft Docs
description: Az Azure IoT Central-feladatok lehetővé teszik a csoportos eszközök felügyeleti funkcióinak használatát, például a tulajdonságok frissítését vagy egy parancs futtatását.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: 5b8aba74cb0914cf26382e0d17a8ce2ba6bd4063
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573960"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Feladatok létrehozása és futtatása az Azure IoT Central alkalmazásban

Az Azure IoT Central használatával a csatlakoztatott eszközöket nagy léptékű feladatok segítségével kezelheti. A feladatok lehetővé teszik az eszközök és a felhő tulajdonságainak tömeges frissítését, valamint parancsok futtatását. Ez a cikk bemutatja, hogyan kezdheti el a feladatok használatát a saját alkalmazásban.

## <a name="create-and-run-a-job"></a>Feladatok létrehozása és futtatása

Az alábbi példa bemutatja, hogyan hozhat létre és futtathat egy feladatot egy logisztikai átjáró eszközeinek egy csoportjának a fény küszöbértékének beállításához. Feladatok létrehozásához és futtatásához használja a feladat varázslót. A feladatokat később is futtathatja.

1. A bal oldali ablaktáblán válassza a **feladatok**lehetőséget.

1. Válassza az **+ új feladatok**lehetőséget.

1. A **saját feladatok konfigurálása** lapon adja meg a létrehozandó feladatot azonosító nevet és leírást.

1. Válassza ki azt a céleszköz-csoportot, amelyre alkalmazni szeretné a feladatot. Megtekintheti, hogy a feladatok hány eszközre vonatkoznak az **eszközcsoport** kiválasztása alatt.

1. A **Feladattípusként**válassza a **Cloud Property**, a **Property**vagy a **parancsot** :

    Egy **tulajdonság** konfigurálásához válasszon ki egy tulajdonságot, és állítsa be az új értékét. A **parancssori** feladatok konfigurálásához válassza ki a futtatandó parancsot. A tulajdonság-feladatok több tulajdonságot is beállíthat.

    :::image type="content" source="media/howto-run-a-job/configure-job.png" alt-text="Képernyőkép, amely a set Light küszöbérték nevű tulajdonság-feladatok létrehozására szolgáló beállításokat jeleníti meg":::

    Válassza a **Mentés és kilépés** lehetőséget a feladat a **feladatok** lapon lévő mentett feladatok listájához való hozzáadásához. Később visszatérhet egy feladathoz a mentett feladatok listájából.

    Kattintson a **tovább** gombra a **kézbesítési beállítások** lapra való áttéréshez. A **kézbesítési beállítások** lapon megadhatja a feladatok kézbesítési beállításait: **kötegek** és **lemondási küszöbérték**.

    A kötegek lehetővé teszik a nagy mennyiségű eszközre vonatkozó feladatok lépcsőzetes kiosztását. A feladatoknak több kötegbe vannak osztva, és mindegyik köteg az eszközök egy részhalmazát tartalmazza. A kötegek várólistára kerülnek, és sorban futnak.

    A lemondási küszöbérték lehetővé teszi, hogy automatikusan visszavonjon egy feladatot, ha a hibák száma meghaladja a beállított korlátot. A küszöbérték a feladatokban szereplő összes eszközre, illetve az egyes kötegekre is alkalmazható.

    :::image type="content" source="media/howto-run-a-job/job-wizard-delivery-options.png" alt-text="Képernyőkép, amely a set Light küszöbérték nevű tulajdonság-feladatok létrehozására szolgáló beállításokat jeleníti meg":::

    Kattintson a **tovább** gombra a **felülvizsgálati** lapra való áttéréshez. A **felülvizsgálati** oldal a feladatok konfigurációjának részleteit jeleníti meg. Válassza a **Futtatás** lehetőséget a feladatok elküldéséhez.

    :::image type="content" source="media/howto-run-a-job/job-wizard-review.png" alt-text="Képernyőkép, amely a set Light küszöbérték nevű tulajdonság-feladatok létrehozására szolgáló beállításokat jeleníti meg":::

1. A feladatok *függő*, *futó*és *befejezett* fázisokon haladnak át. A feladatok végrehajtásának részletei az eredmény mérőszámait, az időtartam részleteit és az eszközök listájának rácsát tartalmazzák.

    Ha a feladatok befejeződik, kiválaszthatja az **eredmények naplót** a feladatok részleteit tartalmazó CSV-fájl letöltéséhez, beleértve az eszközöket és az állapotukat is. Ez az információ hasznos lehet a hibaelhárításhoz.

    :::image type="content" source="media/howto-run-a-job/download-details.png" alt-text="Képernyőkép, amely a set Light küszöbérték nevű tulajdonság-feladatok létrehozására szolgáló beállításokat jeleníti meg":::

1. A feladat most a **feladatok** lap **utolsó 30 nap** listájában jelenik meg. Ez a lap a jelenleg futó feladatokat és a korábban futtatott vagy mentett feladatok előzményeit jeleníti meg.

    > [!NOTE]
    > A korábban futtatott feladatok esetében 30 nappal korábbi előzményeket tekinthet meg.

## <a name="manage-jobs"></a>Feladatok kezelése

Futó feladatok leállításához nyissa meg, majd válassza a **Leállítás**lehetőséget. A feladatok állapota azt jelzi, hogy a feladatot leállították. Az **Összefoglalás** szakaszban látható, hogy mely eszközök lettek végrehajtva, sikertelenek, vagy még függőben vannak.

:::image type="content" source="media/howto-run-a-job/manage-job.png" alt-text="Képernyőkép, amely a set Light küszöbérték nevű tulajdonság-feladatok létrehozására szolgáló beállításokat jeleníti meg":::

Ha egy feladattípus leállított állapotban van, a **Folytatás** gombra kattintva folytathatja a feladatok futtatását. A feladatok állapota úgy változik, hogy a feladatainak futása újra megtörténjen. Az **Összefoglalás** szakasz a legújabb folyamattal folytatja a frissítést.

:::image type="content" source="media/howto-run-a-job/stopped-job.png" alt-text="Képernyőkép, amely a set Light küszöbérték nevű tulajdonság-feladatok létrehozására szolgáló beállításokat jeleníti meg":::

## <a name="copy-a-job"></a>Feladatok másolása

Meglévő feladatok másolásához válasszon ki egy végrehajtott feladatot. Válassza a **Másolás** elemet a feladat eredményei oldalon vagy a feladatok részletei lapon:

:::image type="content" source="media/howto-run-a-job/job-details-copy.png" alt-text="Képernyőkép, amely a set Light küszöbérték nevű tulajdonság-feladatok létrehozására szolgáló beállításokat jeleníti meg":::

Ekkor megnyílik a feladatok konfigurációjának egy másolata, amelyet **a rendszer a** feladatok nevéhez hozzáfűz.

## <a name="view-job-status"></a>Feladat állapotának megtekintése

A feladatok létrehozása után az **állapot** oszlop a legutóbbi feladatok állapota üzenettel frissül. A következő táblázat a lehetséges *feladatok állapotának* értékeit sorolja fel:

| Állapotüzenet       | Állapot jelentése                                          |
| -------------------- | ------------------------------------------------------- |
| Befejeződött            | Ez a feladatok minden eszközön futottak.              |
| Sikertelen               | A feladatot nem sikerült végrehajtani, és nem futott teljesen teljes mértékben az eszközökön.  |
| Függőben              | Ez a feladatok még nem kezdődött meg az eszközökön.         |
| Futó              | Ez a feladatok jelenleg az eszközökön futnak.             |
| Leállítva              | Egy felhasználó manuálisan leállította ezt a feladatot.           |
| Megszakítva             | A feladat meg lett szakítva, mert túllépte a **kézbesítési beállítások** lapon beállított küszöbértéket. |

Az állapotjelző üzenetet a feladatokban található eszközök áttekintése követi. A következő táblázat felsorolja a lehetséges *Eszközállapot* -értékeket:

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

Ha le szeretne tölteni egy CSV-fájlt, amely tartalmazza a feladatok részleteit, valamint az eszközök listáját és az állapotuk értékét, válassza az **eredmények napló**lehetőséget.

## <a name="filter-the-device-list"></a>Az eszközlista szűrése

A szűrő ikonra kattintva szűrheti az eszközök listáját a **feladatok részletei** oldalon. Az **eszköz azonosítóját** vagy **állapotát** az alábbiak szerint szűrheti:

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Képernyőkép, amely a set Light küszöbérték nevű tulajdonság-feladatok létrehozására szolgáló beállításokat jeleníti meg":::

## <a name="customize-columns-in-the-device-list"></a>Oszlopok testreszabása az eszközök listájában

Oszlopokat adhat hozzá az eszközök listájához az oszlop beállításai ikon kiválasztásával:

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Képernyőkép, amely a set Light küszöbérték nevű tulajdonság-feladatok létrehozására szolgáló beállításokat jeleníti meg":::

Az **oszlop beállításai** párbeszédpanel segítségével kiválaszthatja az eszközök listájának oszlopait. Jelölje ki a megjeleníteni kívánt oszlopokat, válassza ki a jobbra mutató nyilat, majd kattintson az **OK gombra**. Az összes elérhető oszlop kiválasztásához válassza az **összes kijelölése**lehetőséget. A kijelölt oszlopok megjelennek az eszközök listájában.

A kijelölt oszlopok megmaradnak egy felhasználói munkamenetben vagy az alkalmazáshoz hozzáféréssel rendelkező felhasználói munkamenetek között.

## <a name="rerun-jobs"></a>Feladatok újrafuttatása

Újrafuttathat egy hibás eszközöket tartalmazó feladatot. Válassza **az újrafuttatás sikertelen**:

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Képernyőkép, amely a set Light küszöbérték nevű tulajdonság-feladatok létrehozására szolgáló beállításokat jeleníti meg":::

Adja meg a feladatok nevét és leírását, majd válassza a **feladatok újrafuttatása**lehetőséget. A rendszer elküld egy új feladatot, hogy megismételje a műveletet a meghiúsult eszközökön.

> [!NOTE]
> Egy Azure IoT Central alkalmazásból egyszerre legfeljebb öt feladatot futtathat.
>
> Ha a feladatok befejeződik, és törli a feladathoz tartozó eszközök listáján szereplő eszközt, az eszköz neve törölve lesz az eszköz nevében. A részletek hivatkozás nem érhető el a törölt eszközhöz.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan hozhat létre feladatokat az Azure IoT Central alkalmazásban, néhány további lépés:

- [Eszközök kezelése](howto-manage-devices.md)
- [Az eszköz sablonjának verziója](howto-version-device-template.md)
