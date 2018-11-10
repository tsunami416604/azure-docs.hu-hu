---
title: Beállítja a eszköz használata az Azure IoT Central alkalmazáshoz |} A Microsoft Docs
description: Kezelőként használata az eszköz beállítása az Azure IoT Central alkalmazáshoz.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: a5dd018197ff78ff0563349fd941308c3684a456
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51004119"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Eszköz használata az Azure IoT központi alkalmazás beállítása

Ez a cikk bemutatja, hogyan kezelőként eszközével állít be az Azure IoT Central alkalmazáshoz.

Egy eszköz csoportot az eszközöket, amelyek vannak csoportosítva, mivel bizonyos megadott feltételeknek megfelelő összes listája. Eszközök kezelése, megjelenítését és elemzését a nagy mennyiségű eszközt eszközök kisebb, logikai csoportokba csoportosításával súgó állítja be. Az összes légkondicionálóját eszközök listáját például Budapesten található összes eszközt, amelynek a technikus feladata a Seattle technikus engedélyezéséhez létrehozása. Ez a cikk bemutatja, hogyan hozhat létre, és az eszköz csoportjainak konfigurálása.

## <a name="create-a-device-set"></a>Hozzon létre egy eszköz csoportot

Eszköz létrehozásához állítsa be:

1. Válasszon **eszköz csoportok** a bal oldali navigációs menüben.

1. Kattintson az **+ Új** elemre.

    ![Új eszköz beállítása](media/howto-use-device-sets/image1.png)

1. Nevezze el az eszköz beállítása, amely egyedi a teljes alkalmazáson. Hozzáadhat egy leírást is. Egy eszköz csoportot csak egyetlen eszköz sablonból eszközök tartalmazhat. Válassza ki az eszközt a készlet használni kívánt sablont.

1. Az eszköz kiválasztásával, egy tulajdonságot egy összehasonlító operátor és egy értéket állítsa be az eszközök azonosítására a lekérdezés létrehozásához. Hozzáadhat több lekérdezések és eszközei megfeleljenek **minden** az eszköz beállítása a feltételek vannak elhelyezve. Az eszköz készletet hoz létre, bárki, aki hozzáfér az alkalmazáshoz, így bárki megtekintése, módosítása vagy törlése az eszköz beállítása érhető el.

    ![Eszköz beállítása lekérdezés](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > Az eszköz be kapcsolva egy dinamikus lekérdezést. Minden alkalommal, amikor az eszközök listájának megtekintéséhez lehet a különböző eszközök a listában. A lista attól függ, mely eszközök jelenleg felel meg a lekérdezés feltételeinek.

1. Válassza a **Mentés** elemet.

## <a name="configure-the-dashboard-for-your-device-set"></a>Az eszköz beállítása az irányítópulton konfigurálása

Miután létrehozta az eszköz beállítása, konfigurálhatja a **irányítópult**. A **irányítópult** van a kezdőlap helyezheti el képek és hivatkozások. Azt is megteheti, hogy az eszköz beállítása az eszközök rácsok.

1. Válasszon **eszköz csoportok** a bal oldali navigációs menüben.

1. Válassza ki a **irányítópult** fülre.

1. Kattintson a **sablon szerkesztése**.

    ![A Tervező módban](media/howto-use-device-sets/image3.png)

1. Kép adásával kapcsolatos információkért lásd: [előkészítése és a feltöltés rendszerképek az Azure IoT Central alkalmazásnak](howto-prepare-images.md).

1. Hivatkozás csempe hozzáadása:
    1. Válasszon **hivatkozás** a jobb oldali ablaktáblán.
    1. Adja meg a hivatkozás egy **cím**.
    1. Válasszon egy URL-címet kell megnyitni, ha a hivatkozásra kattint.
    1. Adjon a hivatkozás egy leírást, amely az alább látható a **cím**.
    1. Válassza a **Mentés** elemet.

        ![Hivatkozás mentése](media/howto-use-device-sets/image7.png)

    1. Helyezze át, és a hivatkozás csempe átméretezése a a **irányítópult**.

1. Adjon hozzá egy rácsot. Rács az eszköz beállítása az oszlopokat választja a eszközök tábláját.
    1. Válasszon **rács** a jobb oldali ablaktáblán.

        ![Válassza ki a rács](media/howto-use-device-sets/image8.png)

    1. A rács adjon egy **cím**.
    1. Válassza ki az oszlopok kiválasztásával megjelenítendő **hozzáadása/eltávolítása**. A felugró panelen válassza ki a jelenjenek, és válassza a jobbra mutató nyílra, válassza ki azt az oszlopban.
    1. Kattintson az **OK** gombra.
    1. Válassza a **Mentés** elemet.

        ![Mentse a rács](media/howto-use-device-sets/image9.png)

    1. Helyezze el a rácsban áthúzása a **irányítópult**.

    > [!NOTE]
    > Több lemezképek, a hivatkozások és a rácsok is hozzáadhat.
  
    1. Kattintson a **Done** (Kész) gombra.

    ![Tervezési mód kikapcsolása](media/howto-use-device-sets/image10.png)


### <a name="configuring-location-map-in-your-device-sets-dashboard"></a>Helyek térképe konfigurálása az eszköz beállítása az irányítópult 
Hozzáadhat egy helyek térképe jeleníthetik meg az eszközök helyét egy térképen állítja be.

Annak érdekében, hogy adjon hozzá egy eszközhöz, helyek térképe irányítópult rendelkeznie kell konfigurált helyre tulajdonságot állítja az eszköz sablonban, lásd: [hozzon létre egy hely tulajdonságot az Azure Maps segítségével](howto-set-up-template.md).


1. Az eszköz beállítása irányítópulton válassza ki a térkép a könyvtárból.

    ![Eszköz beállítása irányítópult térképek](media/howto-use-device-sets/LocationMaps1.png)

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

    ![Oszloptörlés beállításai](media/howto-use-device-sets/image11.png)

1. Jelölje ki a megjeleníteni kívánt oszlop kiválasztva, majd a jobbra mutató nyílra megjelenítendő oszlopok kiválasztása.

    ![Oszlop kiválasztása](media/howto-use-device-sets/image12.png)

1. Kattintson az **OK** gombra.

## <a name="analytics"></a>Elemzés

Az eszköz beállítása az analytics megegyezik a fő analytics lapra a bal oldali navigációs menüben. További kapcsolatos elemzések a cikkben a [analytics létrehozása](howto-create-analytics.md).

## <a name="next-steps"></a>További lépések

Most, hogy az eszköz csoportok használata az Azure IoT Central alkalmazásban megtanulhatta, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Telemetria szabályok létrehozása](howto-create-telemetry-rules.md)
