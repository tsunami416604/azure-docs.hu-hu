---
title: Ismerkedés az Azure IoT Central kezelőfelületével | Microsoft Docs
description: Szerkesztőként érdemes megismerkedni az IoT-megoldások létrehozásához használt Azure IoT Central kezelőfelületének fő területeivel.
author: dominicbetts
ms.author: dobett
ms.date: 01/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: a5457299d0a0112685b194887802b4808a21d983
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666600"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui-new-ui-design"></a>Fedezze fel az Azure IoT Central felhasználói felület (új felhasználói felület tervezése)

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

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="use-the-left-navigation-menu"></a>A bal oldali navigációs menü használata

A bal oldali navigációs menüből érhetők el az alkalmazás különböző területei:

| Menü | Leírás |
| ---- | ----------- |
| ![Bal oldali navigációs menü](media/overview-iot-central-tour-experimental/navigationbar.png) | <ul><li>A **Kezdőlap** gomb megjeleníti az alkalmazás kezdőlapját. A szerkesztők testreszabhatják a kezdőlapot az operátorok számára.</li><li>A **Device Explorer** gombra a társított minden egyes eszköz sablon az alkalmazás szimulált és valós eszközöket sorolja fel. Az operátorok az **Eszközkereső** használatával kezelhetik a csatlakoztatott eszközöket.</li><li>Az **Eszközkészletek** gomb lehetővé teszi eszközkészletek megtekintését és létrehozását. Az operátorok létrehozhatnak eszközkészleteket, amelyek a lekérdezés által megadott eszközök logikai gyűjteményei.</li><li>Az **Elemzés** gomb az eszközök és eszközkészletek telemetriáiból származó elemzéseket jelenít meg. Az operátorok létrehozhatnak egyéni nézeteket az eszközadatokhoz, így megjelenítve az alkalmazásból származó megállapításokat.</li><li>A **Feladatok** gomb tömeges eszközkezelést tesz lehetővé a méretezhető frissítéseket végző feladatok létrehozásával és futtatásával.</li><li>A **eszközsablonok** gombon szerkesztő segítségével eszköz sablonokat létrehozni és felügyelni az eszközöket.</li><li>A **folyamatos adatexportálás** a rendszergazda konfigurálja a folyamatos exportálást, és más Azure-szolgáltatások például a storage és a várólisták gombra.</li><li>Az **Adminisztráció** gomb jeleníti meg azokat az alkalmazásadminisztrációs oldalakat, ahol a rendszergazda az alkalmazás beállításait, felhasználóit és szerepköreit kezelheti.</li></ul> |

## <a name="search-help-and-support"></a>Keresés, súgó és támogatás

Minden oldalon megjelenik a felső menü:

![Eszköztár](media/overview-iot-central-tour-experimental/toolbar.png)

- Eszközsablonok és eszközök kereséséhez kattintson a **Keresés** ikonra.
- Ha módosítani szeretné a felhasználói felületi nyelven, válassza ki a **nyelvi** ikonra.
- Ha segítségre és támogatásra van szüksége, kattintson a **Súgó** elemre, amely megnyitja az erőforrások legördülő listáját.
- A felhasználói felület téma módosítása, vagy jelentkezzen ki az alkalmazást, válassza a **fiók** ikonra.

A kezelőfelületen egy világos és egy sötét téma közül választhat:

![A kezelőfelület témájának kiválasztása](media/overview-iot-central-tour-experimental/themes.png)

## <a name="home-page"></a>Kezdőlap

![Kezdőlap](media/overview-iot-central-tour-experimental/homepage.png)

A kezdőlap az első lap, amely megjelenik az Azure IoT Central-alkalmazásba való bejelentkezést követően. Szerkesztőként csempék hozzáadásával testreszabhatja a kezdőlapot az alkalmazás további felhasználói számára. További részletekért tekintse át [az Azure IoT Central operátori nézeteinek testreszabására](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) vonatkozó oktatóanyagot.

## <a name="device-explorer"></a>Eszközkereső

![Kereső oldal](media/overview-iot-central-tour-experimental/explorer.png)

A kezelő lap megjeleníti a _eszközök_ szerint csoportosítva, az Azure IoT Central alkalmazásban _eszköz sablon_.

* Az eszközsablonok egy-egy olyan eszköztípust határoznak meg, amelyek csatlakozhatnak az alkalmazáshoz. További tudnivalókért lásd az [új eszköztípus az Azure IoT Central-alkalmazásokban történő definiálását](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) ismertető szakaszt.
* Egy eszköz egy valós vagy szimulált eszközt jelöl az alkalmazásban. További tudnivalókért lásd az [új eszköz az Azure IoT Central-alkalmazásokban történő hozzáadását](tutorial-add-device.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) ismertető szakaszt.

## <a name="device-sets"></a>Eszközkészletek

![Eszközkészletek oldal](media/overview-iot-central-tour-experimental/devicesets.png)

Az _Eszközkészletek_ oldalon jelennek meg a szerkesztő által létrehozott eszközkészletek. Az eszközkészletek egymáshoz kapcsolódó eszközök gyűjteményei. A szerkesztő meghatároz egy lekérdezést, amely azonosítja azokat az eszközöket, amelyek egy eszközkészlethez tartoznak. Az eszközkészletek az alkalmazáson belüli elemzések testreszabásakor használatosak. További tudnivalókért lásd az [eszközkészletek az Azure IoT Central-alkalmazásokban történő használatát](howto-use-device-sets.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) ismertető szakaszt.

## <a name="analytics"></a>Elemzés

![Elemzés oldal](media/overview-iot-central-tour-experimental/analytics.png)

Az Elemzés oldalon diagramok láthatók, amelyek segítenek áttekinteni az alkalmazáshoz csatlakozó eszközök viselkedését. Az operátorok ezen az oldalon monitorozhatják és vizsgálhatják ki a csatlakoztatott eszközökkel kapcsolatos problémákat. Az ezen az oldalon megjelenő diagramokat a szerkesztő határozza meg. További tudnivalókért lásd az [egyéni elemzések az Azure IoT Central-alkalmazásokban történő létrehozását](howto-create-analytics.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) ismertető szakaszt.

## <a name="jobs"></a>Feladatok

![Feladatok oldal](media/overview-iot-central-tour-experimental/jobs.png)

A feladatok oldal csoportos eszközfelügyeleti műveletek elvégzését teszi lehetővé az eszközökön. A szerkesztő az oldalt az eszköz tulajdonságok, beállítások és parancsok frissítésére használja. További tudnivalókért tekintse meg a [Feladat futtatása](howto-run-a-job.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) cikket.

## <a name="device-templates"></a>Eszközsablonok

![Eszköz sablonok lap](media/overview-iot-central-tour-experimental/templates.png)

Az eszköz sablonok lap, ahol egy jelentéskészítő létrehozza és kezeli az eszköz sablonok az alkalmazásban. További tudnivalókért lásd az [új eszköztípus az Azure IoT Central-alkalmazásokban történő definiálását](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) ismertető szakaszt.

## <a name="continuous-data-export"></a>Folyamatos adatexportálás

![Folyamatos adatexportálás lap](media/overview-iot-central-tour-experimental/export.png)

A folyamatos Exportálás lap, ahol a rendszergazda határozza meg az alkalmazásból származó adatok, telemetriai adatokat, például exportálása. Más szolgáltatások is az exportált adatok tárolására, vagy használhatja az elemzés. További tudnivalókért tekintse meg a [exportálhatja az adatokat az Azure IoT Central](howto-export-data.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) cikk.

## <a name="administration"></a>Adminisztráció

![Adminisztráció oldal](media/overview-iot-central-tour-experimental/administration.png)

Az Adminisztráció oldal hivatkozásokat tartalmaz a rendszergazda által például az alkalmazás felhasználóinak és szerepköreinek meghatározásához használt eszközökhöz. További tudnivalókért lásd az [Azure IoT Central-alkalmazások adminisztrációját](howto-administer.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) ismertető szakaszt.

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az Azure IoT Central jellemzőit, és megismerkedett a kezelőfelület elrendezésével, a javasolt következő lépés az [Azure IoT Central-alkalmazás létrehozását](quick-deploy-iot-central.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) ismertető rövid útmutató elvégzése.