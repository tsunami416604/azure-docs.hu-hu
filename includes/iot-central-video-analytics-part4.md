---
title: fájlbefoglalás
description: fájlbefoglalás
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 164f5803b6e9e62447423735e98f6e4c36c73f13
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877189"
---
### <a name="add-relationships"></a>Kapcsolatok hozzáadása

Az **LVA Edge Gateway** -eszköz sablonjában, a **modulok/LVA Edge-átjáró modul**alatt válassza a **kapcsolatok**lehetőséget. Válassza a **+ kapcsolat hozzáadása** lehetőséget, és adja hozzá a következő két kapcsolatot:

|Megjelenítendő név               |Name (Név)          |Cél |
|-------------------------- |------------- |------ |
|LVA Edge mozgásérzékelő   |Alapértelmezett használata   |LVA Edge mozgásérzékelő eszköz |
|LVA Edge-objektum detektora   |Alapértelmezett használata   |LVA Edge Object detektor eszköz |

Kattintson a **Mentés** gombra.

:::image type="content" source="media/iot-central-video-analytics-part4/relationships.png" alt-text="Kapcsolatok hozzáadása":::

### <a name="add-views"></a>Nézetek hozzáadása

Az **LVA Edge Gateway** eszköz sablonja nem tartalmaz nézet-definíciókat.

Nézet hozzáadása az eszköz sablonhoz:

1. Az **LVA Edge Gateway** -eszköz sablonjában navigáljon a **nézetek** elemre, és válassza ki az **eszköz megjelenítését** tartalmazó csempét.

1. A nézet neveként adja meg az *LVA Edge-átjáró eszközét* .

1. Adja hozzá a következő csempéket a nézethez:

    * Az **eszköz adatai** tulajdonságokkal rendelkező csempék: **eszköz modell**, **gyártó**, **operációs rendszer**, **processzor architektúrája**, **szoftver verziója**, **teljes memória**és **teljes tárterület**.
    * Egy vonalas diagram a **szabad memóriával** és a **rendszer szívverési** telemetria értékeivel.
    * Egy esemény-előzmény csempe a következő eseményekkel: **kamera létrehozása**, **kamera törlése**, **modul újraindítása**, modul **elindítva**, **modul leállítva**.
    * Egy 2x1 utolsó ismert érték csempe, amely a **IoT Central ügyfél állapotának** telemetria mutatja.
    * Egy 2x1 utolsó ismert érték csempe, amely a **modul állapotának** telemetria mutatja.
    * Egy 1x1 utolsó ismert érték csempe, amely a **rendszer szívverésének** telemetria jeleníti meg.
    * Egy 1x1 utolsó ismert érték csempe, amely a **csatlakoztatott kamerák** telemetria jeleníti meg.

    :::image type="content" source="media/iot-central-video-analytics-part4/gateway-dashboard.png" alt-text="Kapcsolatok hozzáadása":::

1. Kattintson a **Mentés** gombra.

### <a name="publish-the-device-template"></a>Az eszközsablon közzététele

Ahhoz, hogy hozzá lehessen adni egy eszközt az alkalmazáshoz, közzé kell tennie az eszköz sablonját:

1. Az **LVA Edge Gateway** -eszköz sablonjában válassza a **Közzététel**lehetőséget.

1. Az **eszköz sablonjának közzététele az alkalmazás** lapon válassza a **Közzététel**lehetőséget.

Az **LVA Edge-átjáró** mostantól eszköz típusaként használható az alkalmazás **eszközök** lapján.

## <a name="add-a-gateway-device"></a>Átjáró-eszköz hozzáadása

**LVA Edge Gateway** -eszköz hozzáadása az alkalmazáshoz:

1. Navigáljon az **eszközök** lapra, és válassza ki az **LVA Edge Gateway** -eszköz sablonját.

1. Válassza a **+ Új** lehetőséget.

1. Az **új eszköz létrehozása** párbeszédpanelen módosítsa az eszköz nevét a *LVA Gateway 001*értékre, és módosítsa az eszköz azonosítóját a *LVA-Gateway-001*értékre.

    > [!NOTE]
    > Az eszköz AZONOSÍTÓjának egyedinek kell lennie az alkalmazásban.

1. Kattintson a **Létrehozás** gombra.

Az eszköz állapota **regisztrálva**van.

### <a name="get-the-device-credentials"></a>Az eszköz hitelesítő adatainak beolvasása

Szüksége lesz a hitelesítő adatokra, amelyek lehetővé teszik az eszköz számára a IoT Central alkalmazáshoz való kapcsolódást. Az eszköz hitelesítő adatainak lekérése:

1. Az **eszközök** lapon válassza ki a létrehozott **LVA-Gateway-001** eszközt.

1. Kattintson a **Csatlakozás** gombra.

1. Az **eszköz csatlakoztatása** lapon jegyezze fel az **azonosító hatókörének** *scratchpad.txt* fájlját, az **eszköz azonosítóját**és az eszköz **elsődleges kulcsát**. Ezeket az értékeket később használhatja.

1. Győződjön meg arról, hogy a kapcsolati módszer a **közös hozzáférési aláírásra**van beállítva.

1. Válassza a **Bezárás** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ezzel létrehozott egy IoT Central alkalmazást a **video Analytics-Object és a mozgásérzékelő** alkalmazás sablonnal, létrehozott egy eszköz sablont az átjáró eszközhöz, és hozzáadta az átjáró-eszközt az alkalmazáshoz.

Ha olyan IoT Edge-modulokkal szeretné kipróbálni a video Analytics – Object és Motion Detection alkalmazást, amelyeken szimulált videó streamekkel rendelkező felhőalapú virtuális gépek futnak:

> [!div class="nextstepaction"]
> [IoT Edge-példány létrehozása a video analyticshez (Linux rendszerű virtuális gép)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

Ha olyan IoT Edge-modulokkal szeretné kipróbálni a video Analytics – Object és Motion Detection alkalmazást, amelyek valódi **ONVIF** kamera használatával futtatnak valós eszközt:

> [!div class="nextstepaction"]
> [IoT Edge-példány létrehozása a video analyticshez (Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)
