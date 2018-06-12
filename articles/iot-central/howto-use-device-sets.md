---
title: Eszköz használata az Azure IoT központi alkalmazás beállítása |} Microsoft Docs
description: Kezelőként eszköz használata az Azure IoT központi alkalmazás beállítása.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: ef1fa64a276926a35dbf98646317bfe29200bb22
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261678"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Eszköz használata az Azure IoT központi alkalmazás beállítása

Ez a cikk ismerteti, hogyan, kezelőként eszköz használatára állítja be a Microsoft Azure IoT központi alkalmazásban.

Egy eszköz be kapcsolva, amelyek mert minden egyes megadott feltételeknek megfelelő eszközök listáját. Eszköz kezelése, megjelenítésének és kisebb, logikai csoportok eszközök csoportosításával léptékű eszközök elemzése súgó állítja be. Például minden légkondicionálók eszközöket létrehozhat Budapesten található minden olyan eszközre, amelynek ő feladata a budapesti technikus engedélyezéséhez. Ez a cikk bemutatja, hogyan hozza létre és konfigurálja az eszköz beállítása.

## <a name="create-a-device-set"></a>Eszköz létrehozása

Egy eszköz-készlet létrehozása:

1. Válasszon **eszköz beállítása** a bal oldali navigációs menü.

1. Kattintson az **+ Új** elemre.

    ![Új eszköz beállítása](media/howto-use-device-sets/image1.png)

1. Nevezze el az eszköz beállítása, amely egyedi a teljes alkalmazás között. Azt is megteheti leírását. Eszköz is csak tartalmazhatnak egyetlen eszközt sablonból eszközök. Válassza ki az ebben a készletben használt eszköz sablon.

1. Hozzon létre a lekérdezés futtatásával azonosíthatja azokat az eszközöket, az eszköz konfigurálása egy tulajdonságot, egy összehasonlító operátor és egy érték megadásával. Több lekérdezések és teljesítő eszközöket úgy adhatja hozzá **összes** a feltételek kerülnek, az eszköz beállítása. Az eszköz készletet hoz létre a bárki, aki hozzáfér az alkalmazás így bárki, aki megtekintése, módosítása vagy az eszköz-készlet törlése érhető el.

    ![Lekérdezés beállítva](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > Az eszköz be kapcsolva a dinamikus lekérdezés. Minden alkalommal, amikor az eszközök listájának megtekintéséhez lehet különböző eszközök a listában. A lista attól függ, mely eszközök jelenleg felel meg a lekérdezés feltételeinek.

1. Válassza a **Mentés** elemet.

## <a name="configure-the-dashboard-for-your-device-set"></a>Az irányítópult az eszköz készlet konfigurálása

Miután létrehozta az eszköz-csoportosítóra, konfigurálhatja a **irányítópult**. A **irányítópult** helyezheti el képek és hivatkozások kezdőlapon van. Azt is megteheti, hogy az eszköz körben lévő eszközök listában rácsok.

1. Válasszon **eszköz beállítása** a bal oldali navigációs menü.

1. Válassza ki a **irányítópult** fülre.

1. Kapcsolja be a **Tervező mód**.

    ![A Tervező módban](media/howto-use-device-sets/image3.png)

1. A képfájl hozzáadásával kapcsolatos további információkért lásd: [előkészítése és feltöltése az Azure IoT központi alkalmazáshoz képek](howto-prepare-images.md).

1. Adja hozzá egy hivatkozást csempe:
    1. Válasszon **hivatkozás** a jobb oldali ablaktáblában.

        ![Válassza ki a kapcsolat](media/howto-use-device-sets/image6.png)

    1. Ad a hivatkozás egy **cím**.
    1. Válasszon egy URL-címet kell megnyitni, ha a hivatkozásra kattint.
    1. Ad a hivatkozás egy leírást, amely alatt a **cím**.
    1. Válassza a **Mentés** elemet.

        ![Hivatkozás mentése](media/howto-use-device-sets/image7.png)

    1. Helyezze át, és a hivatkozás csempe átméretezése a **irányítópult**.

1. Adja hozzá a rács. A rács az eszköz beállítása az oszlopokat, dönthet eszközöket tartalmazó táblázat.
    1. Válasszon **rács** a jobb oldali ablaktáblában.

        ![Válassza ki a rács](media/howto-use-device-sets/image8.png)

    1. A rács adjon egy **cím**.
    1. Válassza ki a beállítások gombra megjelenítendő oszlopok kiválasztása Ugrik fel a panelen válassza ki a megjelenítendő, és kattintson a jobbra mutató nyílra kattintva válassza ki azt az oszlop.
    1. Kattintson az **OK** gombra.
    1. Válassza a **Mentés** elemet.

        ![Mentse a rács](media/howto-use-device-sets/image9.png)

    1. A rácsban, hogy helyezze el áthúzása a **irányítópult**.

    > [!NOTE]
    > Több lemezképek, a hivatkozások és a rácsok is hozzáadhat.
  
    1. Kapcsolja ki a **tervezési módba**.

    ![Tervező mód kikapcsolása](media/howto-use-device-sets/image10.png)


### <a name="configuring-location-map-in-your-device-sets-dashboard"></a>Irányítópult helyének hozzárendelése konfigurálása az eszköz a állítja be. 
Egy hely leképezés jelenítheti meg az eszközök helyét a térképen beállítja adhat hozzá. 

Egy hely térképet, eszköz irányítópult rendelkeznie kell beállított hely tulajdonság beállítása az eszköz sablonban hozzáadásához lásd: [hozzon létre egy Azure Maps technológiával helyet jelölő tulajdonsághoz](howto-set-up-template.md).


1. Az eszköz beállítása irányítópultot válassza ki a térkép a könyvtárból. 

    ![Eszköz irányítópult Maps állítja be.](media/howto-use-device-sets/LocationMaps1.png)


2. Olyan címet adjon, és válassza ki a helyet jelölő tulajdonsághoz vannak konfigurálva a Eszköztulajdonságon részeként.

    ![Irányítópult-leképezések konfigurálása](media/howto-use-device-sets/LocationMaps2.png)

3. Mentés, és megjelenik a térkép csempén megjelenő helyét, az eszközök a beállítva.

    ![Irányítópult-leképezések mentése](media/howto-use-device-sets/LocationMaps3.png)


5. Most operátor megtekinti az eszköz beállítása irányítópultot, amikor áttekinthetők a csempék konfigurálta, beleértve a leképezés egy pillantással eszközök hely megjelenítése!

    ![A Maps operátor irányítópult-nézet](media/howto-use-device-sets/LocationMaps4.png)

    A kívánt méretet a térképen átméretezése lehet.




## <a name="configure-the-list-for-your-device-set"></a>Az eszköz készlet listájának konfigurálása

Miután létrehozta az eszköz-csoportosítóra, konfigurálhatja a **lista**. A **lista** jeleníti meg a megadott összes eszközt az eszközt a választott oszlopokat tartalmazó tábla.

1. Válasszon **eszköz beállítása** a bal oldali navigációs menü.

1. Válassza ki a **lista** fülre.

1. Válasszon **oszlop beállítások**.

    ![Oszlop beállítások](media/howto-use-device-sets/image11.png)

1. Jelölje ki a megjeleníteni kívánt oszlopot kiválasztásával, és válassza a jobbra mutató nyílra megjelenítendő oszlopok kiválasztása

    ![Oszlop kiválasztása](media/howto-use-device-sets/image12.png)

1. Kattintson az **OK** gombra.

## <a name="analytics"></a>Elemzés

Az eszköz beállítása az analytics megegyezik a fő analytics fülre a bal oldali navigációs menü. További elemzés a cikkben szereplő információk a [analytics létrehozása](howto-create-analytics.md).

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan használható az eszköz beállítása az Azure IoT központi alkalmazásban, ez a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Telemetria szabályok létrehozása](howto-create-telemetry-rules.md)
