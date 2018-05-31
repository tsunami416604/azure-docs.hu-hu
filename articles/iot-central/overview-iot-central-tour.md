---
title: Ismerkedés az Azure IoT Central kezelőfelületével | Microsoft Docs
description: Szerkesztőként érdemes megismerkedni az IoT-megoldások létrehozásához használt Azure IoT Central kezelőfelületének fő területeivel.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 4/13/2018
ms.topic: overview
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: aa0d496b073600bb976a639ea0039d2a5bbdbbcf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201475"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Ismerkedés az Azure IoT Central kezelőfelületével

Ez a cikk a Microsoft Azure IoT Central kezelőfelületét mutatja be. A kezelőfelületet használhatja Azure IoT Central-megoldások és az azokhoz csatlakozó eszközök létrehozásához, felügyeletéhez és használatához.

A _szerkesztők_ az Azure IoT Central kezelőfelületén határozhatják meg az Azure IoT Central-megoldásokat. A kezelőfelületen a következő műveletek végezhetők el:

- A megoldáshoz csatlakozó eszközök típusainak meghatározása.
- Az eszközökre vonatkozó szabályok és műveletek konfigurálása.
- A felhasználói felület testreszabása a megoldást használó _operátorok_ számára.

Az _operátorok_ az Azure IoT Central kezelőfelületén felügyelhetik az Azure IoT Central-megoldást. A kezelőfelületen a következő műveletek végezhetők el:

- Eszközök monitorozása.
- Eszközök konfigurálása.
- Az eszközök hibáinak keresése és elhárítása.
- Új eszközök kiépítése.

## <a name="use-the-left-navigation-menu"></a>A bal oldali navigációs menü használata

A bal oldali navigációs menüből érhetők el az alkalmazás különböző területei:

| Menü | Leírás |
| ---- | ----------- |
| ![Bal oldali navigációs menü](media/overview-iot-central-tour/navigationbar.png) | <ul><li>A **Kezdőlap** gomb megjeleníti az alkalmazás kezdőlapját. A szerkesztők testreszabhatják a kezdőlapot az operátorok számára.</li><li>Az **Eszközkereső** gomb felsorolja az alkalmazásban meghatározott eszközsablonokat, valamint az egyes eszközsablonokhoz tartozó valós eszközöket. Az operátorok az **Eszközkereső** használatával kezelhetik a csatlakoztatott eszközöket.</li><li>Az **Eszközkészletek** gomb lehetővé teszi eszközkészletek megtekintését és létrehozását. Az operátorok létrehozhatnak eszközkészleteket, amelyek a lekérdezés által megadott eszközök logikai gyűjteményei.</li><li>Az **Elemzés** gomb az eszközök és eszközkészletek telemetriáiból származó elemzéseket jelenít meg. Az operátorok létrehozhatnak egyéni nézeteket az eszközadatokhoz, így megjelenítve az alkalmazásból származó megállapításokat.</li><li>Az **Alkalmazásszerkesztő** gomb megjeleníti a szerkesztők által használt eszközöket, például az **Eszközsablon-készítő** eszközt.</li><li>Az **Adminisztráció** gomb jeleníti meg azokat az alkalmazásadminisztrációs oldalakat, ahol a rendszergazda az alkalmazás beállításait, felhasználóit és szerepköreit kezelheti.</li></ul> |

## <a name="search-help-and-support"></a>Keresés, súgó és támogatás

Minden oldalon megjelenik a felső menü:

![Eszköztár](media/overview-iot-central-tour/toolbar.png)

- Eszközsablonok és eszközök kereséséhez kattintson a **Keresés** ikonra.
- Ha segítségre és támogatásra van szüksége, kattintson a **Súgó** elemre, amely megnyitja az erőforrások legördülő listáját.
- Az oktatóanyagok vezérléséhez, a kezelőfelületi téma módosításához vagy az alkalmazásból való kijelentkezéshez kattintson a **Fiók** ikonra.

A kezelőfelületen egy világos és egy sötét téma közül választhat:

![A kezelőfelület témájának kiválasztása](media/overview-iot-central-tour/themes.png)

## <a name="home-page"></a>Kezdőlap

![Kezdőlap](media/overview-iot-central-tour/homepage.png)

A kezdőlap az első lap, amely megjelenik az Azure IoT Central-alkalmazásba való bejelentkezést követően. Szerkesztőként csempék hozzáadásával testreszabhatja a kezdőlapot az alkalmazás további felhasználói számára. További részletekért tekintse át [az Azure IoT Central operátori nézeteinek testreszabására](tutorial-customize-operator.md) vonatkozó oktatóanyagot.

## <a name="device-explorer"></a>Eszközkereső

![Kereső oldal](media/overview-iot-central-tour/explorer.png)

A Kereső oldalon jelennek meg az Azure IoT Central-alkalmazáson belüli _eszközsablonok_ és _eszközök_.

* Az eszközsablonok egy-egy olyan eszköztípust határoznak meg, amelyek csatlakozhatnak az alkalmazáshoz. További tudnivalókért lásd az [új eszköztípus az Azure IoT Central-alkalmazásokban történő definiálását](tutorial-define-device-type.md) ismertető szakaszt.
* Egy eszköz egy valós vagy szimulált eszközt jelöl az alkalmazásban. További tudnivalókért lásd az [új eszköz az Azure IoT Central-alkalmazásokban történő hozzáadását](tutorial-add-device.md) ismertető szakaszt.

## <a name="device-sets"></a>Eszközkészletek

![Eszközkészletek oldal](media/overview-iot-central-tour/devicesets.png)

Az _Eszközkészletek_ oldalon jelennek meg a szerkesztő által létrehozott eszközkészletek. Az eszközkészletek egymáshoz kapcsolódó eszközök gyűjteményei. A szerkesztő meghatároz egy lekérdezést, amely azonosítja azokat az eszközöket, amelyek egy eszközkészlethez tartoznak. Az eszközkészletek az alkalmazáson belüli elemzések testreszabásakor használatosak. További tudnivalókért lásd az [eszközkészletek az Azure IoT Central-alkalmazásokban történő használatát](howto-use-device-sets.md) ismertető szakaszt.

## <a name="analytics"></a>Elemzés

![Elemzés oldal](media/overview-iot-central-tour/analytics.png)

Az Elemzés oldalon diagramok láthatók, amelyek segítenek áttekinteni az alkalmazáshoz csatlakozó eszközök viselkedését. Az operátorok ezen az oldalon monitorozhatják és vizsgálhatják ki a csatlakoztatott eszközökkel kapcsolatos problémákat. Az ezen az oldalon megjelenő diagramokat a szerkesztő határozza meg. További tudnivalókért lásd az [egyéni elemzések az Azure IoT Central-alkalmazásokban történő létrehozását](howto-create-analytics.md) ismertető szakaszt.

## <a name="application-builder"></a>Alkalmazásszerkesztő

![Alkalmazásszerkesztő oldal](media/overview-iot-central-tour/applicationbuilder.png)

Az Alkalmazásszerkesztő oldal hivatkozásokat tartalmaz azokra az eszközökre, amelyeket a szerkesztők használnak az Azure IoT Central-alkalmazások létrehozására, például az eszközsablonokhoz és a kezdőlap konfigurálásához. További tudnivalókért lásd az [új eszköztípus az Azure IoT Central-alkalmazásokban történő definiálását](tutorial-define-device-type.md) ismertető szakaszt.

## <a name="administration"></a>Adminisztráció

![Adminisztráció oldal](media/overview-iot-central-tour/administration.png)

Az Adminisztráció oldal hivatkozásokat tartalmaz a rendszergazda által például az alkalmazás felhasználóinak és szerepköreinek meghatározásához használt eszközökhöz. További tudnivalókért lásd az [Azure IoT Central-alkalmazások adminisztrációját](howto-administer.md) ismertető szakaszt.

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az Azure IoT Central jellemzőit, és megismerkedett a kezelőfelület elrendezésével, a javasolt következő lépés az [Azure IoT Central-alkalmazás létrehozását](quick-deploy-iot-central.md) ismertető rövid útmutató elvégzése.