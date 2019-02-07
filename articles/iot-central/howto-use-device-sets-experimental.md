---
title: Beállítja a eszköz használata az Azure IoT Central alkalmazáshoz |} A Microsoft Docs
description: Kezelőként használata az eszköz beállítása az Azure IoT Central alkalmazáshoz.
author: ellenfosborne
ms.author: elfarber
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: dd8fa36acaf3f4871d63200a4863778180e9be1a
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55773479"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Eszköz használata az Azure IoT központi alkalmazás beállítása

Ez a cikk bemutatja, hogyan kezelőként eszközével állít be az Azure IoT Central alkalmazáshoz.

Egy eszköz csoportot az eszközöket, amelyek vannak csoportosítva, mivel bizonyos megadott feltételeknek megfelelő összes listája. Eszközök kezelése, megjelenítését és elemzését a nagy mennyiségű eszközt eszközök kisebb, logikai csoportokba csoportosításával súgó állítja be. Az összes légkondicionálóját eszközök listáját például Budapesten található összes eszközt, amelynek a technikus feladata a Seattle technikus engedélyezéséhez létrehozása. Ez a cikk bemutatja, hogyan hozhat létre, és az eszköz csoportjainak konfigurálása.

## <a name="create-a-device-set"></a>Hozzon létre egy eszköz csoportot

Eszköz létrehozásához állítsa be:

1. Válasszon **eszköz csoportok** a bal oldali navigációs menüben.

1. Kattintson az **+ Új** elemre.

    ![Új eszköz beállítása](media/howto-use-device-sets-experimental/image1.png)

1. Nevezze el az eszköz beállítása, amely egyedi a teljes alkalmazáson. Hozzáadhat egy leírást is. Egy eszköz csoportot csak egyetlen eszköz sablonból eszközök tartalmazhat. Válassza ki az eszközt a készlet használni kívánt sablont.

1. Az eszköz kiválasztásával, egy tulajdonságot egy összehasonlító operátor és egy értéket állítsa be az eszközök azonosítására a lekérdezés létrehozásához. Hozzáadhat több lekérdezések és eszközei megfeleljenek **minden** az eszköz beállítása a feltételek vannak elhelyezve. Az eszköz készletet hoz létre, bárki, aki hozzáfér az alkalmazáshoz, így bárki megtekintése, módosítása vagy törlése az eszköz beállítása érhető el.

    ![Eszköz beállítása lekérdezés](media/howto-use-device-sets-experimental/image2.png)

    > [!NOTE]
    > Az eszköz be kapcsolva egy dinamikus lekérdezést. Minden alkalommal, amikor az eszközök listájának megtekintéséhez lehet a különböző eszközök a listában. A lista attól függ, mely eszközök jelenleg felel meg a lekérdezés feltételeinek.

1. Válassza a **Mentés** elemet.

## <a name="configure-the-dashboard-for-your-device-set"></a>Az eszköz beállítása az irányítópulton konfigurálása

Miután létrehozta az eszköz beállítása, konfigurálhatja a **irányítópult**. A **irányítópult** van a kezdőlap helyezheti el képek és hivatkozások. Azt is megteheti, hogy az eszköz beállítása az eszközök rácsok.

1. Válasszon **eszköz csoportok** a bal oldali navigációs menüben.

1. Válassza ki az eszköz beállítása.

1. Kattintson az **Irányítópult** fülre.

1. Kattintson a **Szerkesztés** gombra.

    ![A Tervező módban](media/howto-use-device-sets-experimental/image3.png)

1. Kép adásával kapcsolatos információkért lásd: [előkészítése és a feltöltés rendszerképek az Azure IoT Central alkalmazásnak](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

1. Hivatkozás csempe hozzáadása:
    1. Válasszon **hivatkozás** a jobb oldali ablaktáblán.
    1. Adja meg a hivatkozás egy **cím**.
    1. Válasszon egy URL-címet kell megnyitni, ha a hivatkozásra kattint.
    1. Adjon a hivatkozás egy leírást, amely az alább látható a **cím**.
    1. Válassza a **Mentés** elemet.

        ![Hivatkozás mentése](media/howto-use-device-sets-experimental/image7.png)

    1. Helyezze át, és a hivatkozás csempe átméretezése a a **irányítópult**.

1. Adjon hozzá egy rácsot. Rács az eszköz beállítása az oszlopokat választja a eszközök tábláját.
    1. Válasszon **rács** a jobb oldali ablaktáblán.
    1. A rács adjon egy **cím**.
    1. Válassza ki az oszlopok kiválasztásával megjelenítendő **hozzáadása/eltávolítása**. A felugró panelen válassza ki a jelenjenek, és válassza a jobbra mutató nyílra, válassza ki azt az oszlopban.
    1. Kattintson az **OK** gombra.
    1. Válassza a **Mentés** elemet.

        ![Mentse a rács](media/howto-use-device-sets-experimental/image9.png)

    1. Helyezze el a rácsban áthúzása a **irányítópult**.

        > [!NOTE]
        > Több lemezképek, a hivatkozások és a rácsok is hozzáadhat.
  
    1. Kattintson a **Done** (Kész) gombra.

### <a name="configuring-location-map-in-your-device-sets-dashboard"></a>Helyek térképe konfigurálása az eszköz beállítása az irányítópult

Hozzáadhat egy helyek térképe jeleníthetik meg az eszközök helyét egy térképen állítja be.

Annak érdekében, hogy adjon hozzá egy eszközhöz, helyek térképe irányítópult rendelkeznie kell konfigurált helyre tulajdonságot állítja az eszköz sablonban, lásd: [hozzon létre egy hely tulajdonságot az Azure Maps segítségével](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

1. Az eszköz beállítása irányítópulton válassza ki a térkép a könyvtárból.
2. Adjon címet, és válassza ki a helyet jelölő tulajdonsághoz korábban konfigurálta a Eszköztulajdonság részeként.
3. Mentés, és megjelenik a csempén, az eszköz beállítása az eszközök helyét megjelenítése térképen.
4. Most egy operátor nézetek, az eszköz állítja be az irányítópultot, az üzemeltető konfigurálta az összes csempe látható, ha például a helye térkép megjelenítése egyetlen pillantással eszközök helyét! 
    
> [!NOTE] 
> Méretezze át a térkép a megfelelő méretre lehet. Kattintson a PIN-kódot a térképen az eszközinformációkat, nevét és helyét jeleníti meg. Az eszköz tulajdonságok oldalán lépjen az előugró kattinthat.  

## <a name="configure-the-list-for-your-device-set"></a>A lista az eszköz készlet konfigurálása

Miután létrehozta az eszköz beállítása, konfigurálhatja a **lista**. A **lista** megjeleníti az összes az eszközök az eszközön állítsa be a választott oszlopokat tartalmazó tábla.

1. Válasszon **eszköz csoportok** a bal oldali navigációs menüben.

1. Válassza ki a **lista** fülre.

1. Válasszon **Oszloptörlés beállításai**.

    ![Oszloptörlés beállításai](media/howto-use-device-sets-experimental/image11.png)

1. Jelölje ki a megjeleníteni kívánt oszlop kiválasztva, majd a jobbra mutató nyílra megjelenítendő oszlopok kiválasztása.

    ![Oszlop kiválasztása](media/howto-use-device-sets-experimental/image12.png)

1. Kattintson az **OK** gombra.

## <a name="analytics"></a>Elemzés

Az eszköz beállítása az analytics megegyezik a fő analytics lapra a bal oldali navigációs menüben. További kapcsolatos elemzések a cikkben a [analytics létrehozása](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="next-steps"></a>További lépések

Most, hogy az eszköz csoportok használata az Azure IoT Central alkalmazásban megtanulhatta, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Telemetria szabályok létrehozása](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
