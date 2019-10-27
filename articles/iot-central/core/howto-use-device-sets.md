---
title: Eszköz-készletek használata az Azure IoT Central alkalmazásban | Microsoft Docs
description: Kezelőként, hogyan használhatók az eszközök az Azure IoT Central alkalmazásban.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 87bbab041405a085d405707ff419fbad55fdcd09
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952757"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Az eszközök készletének használata az Azure IoT Central alkalmazásban

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Ez a cikk azt ismerteti, hogyan használhatja az eszközök készleteit az Azure IoT Central alkalmazásban.

Az eszközbeállítások azon eszközök listája, amelyek egy csoportba vannak csoportosítva, mert megfelelnek néhány megadott feltételnek. Az eszközök a méretezési eszközök segítségével kisebb, logikai csoportokba csoportosítva kezelhetik, megjeleníthetik és elemezhetik az eszközöket. Létrehozhat például egy olyan eszközt, amely a Seattle-ben lévő összes légkondicionáló eszköz listáját tartalmazza, és lehetővé teszi, hogy a technikus megkeresse azokat az eszközöket, amelyekhez felelősek. Ebből a cikkből megtudhatja, hogyan hozhat létre és konfigurálhat eszközbeállításokat.

## <a name="create-a-device-set"></a>Eszköz készletének létrehozása

Eszköz készletének létrehozása:

1. Válassza az **eszközbeállítások** elemet a bal oldali ablaktáblán.

1. Válassza az **+ új**lehetőséget.

    ![Új eszköz készlete](media/howto-use-device-sets/image1.png)

1. Adja meg az eszköznek a teljes alkalmazáson belül egyedi nevet. Leírást is hozzáadhat a szolgáltatáshoz. Egy eszköz csak egyetlen eszköz sablonból tartalmazhat eszközöket. Válassza ki a készlethez használni kívánt sablont.

1. A lekérdezés létrehozásával azonosíthatja az eszközhöz tartozó eszközöket egy tulajdonság, egy összehasonlító operátor és egy érték kiválasztásával. Több olyan lekérdezést és eszközt is hozzáadhat, amelyek megfelelnek az **összes** feltételnek az eszköz készletében. Az Ön által létrehozott eszköz mindenki számára elérhető, aki hozzáfér az alkalmazáshoz, így bárki megtekintheti, módosíthatja vagy törölheti az eszköz készletét.

    ![Eszköz beállított lekérdezése](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > Az eszköz készlete dinamikus lekérdezés. Minden alkalommal, amikor megtekinti az eszközök listáját, lehet, hogy a listában különböző eszközök szerepelnek. A lista attól függ, hogy mely eszközök felelnek meg a lekérdezés feltételeinek.

1. Válassza a **Mentés** elemet.

## <a name="configure-the-dashboard-for-your-device-set"></a>Az eszköz irányítópultjának beállítása

Miután létrehozta az eszközt, beállíthatja az **irányítópultját**. Az **irányítópult** a Kezdőlap, ahol képeket és hivatkozásokat helyez el. Hozzáadhat olyan rácsokat is, amelyek az eszközön lévő eszközöket listázzák.

1. Válassza az **eszközbeállítások** elemet a bal oldali ablaktáblán.

1. Válassza ki az eszköz készletét.

1. Kattintson az **Irányítópult** fülre.

1. Válassza a **Szerkesztés** elemet.

    ![Tervezési mód bekapcsolva](media/howto-use-device-sets/image3.png)

1. A rendszerkép hozzáadásával kapcsolatos információkért lásd: [lemezképek előkészítése és feltöltése az Azure IoT Central alkalmazásba](howto-prepare-images.md).

1. Hivatkozás csempe hozzáadása:
    1. A jobb oldali ablaktáblán kattintson a **hivatkozás** elemre.
    1. Adja meg a hivatkozás **címét**.
    1. Válassza ki a hivatkozás kiválasztásakor megnyitni kívánt URL-címet.
    1. Adja meg a hivatkozáshoz a **cím**alatt látható leírást.
    1. Válassza a **Mentés** elemet.

        ![Hivatkozás mentése](media/howto-use-device-sets/image7.png)

    1. Áthelyezheti és átméretezheti a hivatkozás csempéjét az **irányítópulton**.

1. Adjon hozzá egy rácsot. A rács a kiválasztott oszlopokkal beállított eszközön lévő eszközök táblázata.
    1. A jobb oldali ablaktáblán válassza a **rács** lehetőséget.
    1. Adja meg a Grid a **címet**.
    1. Válassza ki a megjelenítendő oszlopokat a **Hozzáadás/Eltávolítás**lehetőség választásával. A felugró panelen válassza ki a megjeleníteni kívánt oszlopot, és válassza ki a jobbra mutató nyilat.
    1. Kattintson az **OK** gombra.
    1. Válassza a **Mentés** elemet.

        ![Rács mentése](media/howto-use-device-sets/image9.png)

    1. Húzza a rácsot úgy, hogy az **irányítópulton**helyezze el.

        > [!NOTE]
        > Több képet, hivatkozást és rácsot is hozzáadhat.
  
    1. Válassza a **Done** (Kész) lehetőséget.

Ha többet szeretne megtudni a csempék használatáról az Azure IoT Centralban, tekintse meg az [irányítópult-csempék használatát](howto-use-tiles.md)ismertető témakört.

### <a name="configure-a-location-map-in-your-device-sets-dashboard"></a>Helyadatok konfigurálása az eszközbeállítások irányítópultján

Hozzáadhat egy térképet az eszközök helyének megjelenítéséhez az eszköz készletében.

Ha hozzá szeretne adni egy térképet az eszközbeállítások irányítópulthoz, konfigurálnia kell egy Location vagy Location tulajdonságot az eszköz sablonjában. További információt a [hely mérésének létrehozása](howto-set-up-template.md) vagy [a Location tulajdonság létrehozása](howto-set-up-template.md)című témakörben talál.

1. Az eszköz beállítása **irányítópulton**válassza a **Térkép** lehetőséget a könyvtárból.
2. Adjon hozzá egy címet, és válassza ki a korábban konfigurált hely mértékét vagy tulajdonságát.
3. Válassza a **Save (Mentés** ) lehetőséget, és a Térkép csempén az eszközök utolsó ismert helye látható az eszköz készletében.
4. Amikor egy operátor megtekinti az eszköz irányítópultját, az operátor látja az összes konfigurált csempét, beleértve a hely térképét is.

A Térkép csempét átméretezheti az irányítópulton. A térképen kiválasztott PIN-kód az eszköz információit, nevét és helyét jeleníti meg. Válassza ki az előugró ablakot az eszköz tulajdonságai lapra.

## <a name="configure-the-list-for-your-device-set"></a>Az eszközbeállítások listájának konfigurálása

Miután létrehozta az eszközt, beállíthatja a **listát**. A **lista** az eszközön lévő összes eszközt megjeleníti a kiválasztott oszlopokkal rendelkező táblában.

1. Válassza az **eszközbeállítások** elemet a bal oldali ablaktáblán.

1. Válassza a **lista** fület.

1. Válassza a **oszlopbeállítások**lehetőséget.

    ![Oszlop beállításai](media/howto-use-device-sets/image11.png)

1. Válassza ki a megjelenítendő oszlopokat a megjeleníteni kívánt oszlop kiválasztásával, majd válassza ki a jobbra mutató nyilat.

    ![Oszlop kiválasztása](media/howto-use-device-sets/image12.png)

1. Kattintson az **OK** gombra.

## <a name="analytics"></a>Elemzés

Az eszközökön található elemzések ugyanazok, mint a bal oldali ablaktábla fő elemzési lapja. Az elemzéssel kapcsolatos további információkért tekintse meg az [elemzések létrehozásáról](howto-use-device-sets.md)szóló cikket.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan használhatja az eszközök készleteit az Azure IoT Central alkalmazásban, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Telemetria-szabályok létrehozása](howto-create-telemetry-rules.md)
