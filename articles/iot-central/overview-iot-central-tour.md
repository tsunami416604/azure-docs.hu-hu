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
ms.openlocfilehash: 4908130cc0d59e42adb4f7ab2239e5b57dc08b89
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57774302"
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


## <a name="use-the-left-navigation-menu"></a>A bal oldali navigációs menü használata

A bal oldali navigációs menü használata a különböző területeket, az alkalmazás eléréséhez. Bontsa ki, vagy válassza a navigációs sáv összecsukása **<** vagy **>**:

| Menü | Leírás |
| ---- | ----------- |
| ![Bal oldali navigációs menü](media/overview-iot-central-tour/navigationbar.png) | <ul><li>A **irányítópult** gombra az alkalmazás irányítópulton jeleníti meg. Mint szerkesztő testre szabhatja az irányítópultot az operátorok számára. Felhasználók is létrehozhat saját irányítópultokat.</li><li>A **Device Explorer** gombra a társított minden egyes eszköz sablon az alkalmazás szimulált és valós eszközöket sorolja fel. Az operátorok az **Eszközkereső** használatával kezelhetik a csatlakoztatott eszközöket.</li><li>Az **Eszközkészletek** gomb lehetővé teszi eszközkészletek megtekintését és létrehozását. Az operátorok létrehozhatnak eszközkészleteket, amelyek a lekérdezés által megadott eszközök logikai gyűjteményei.</li><li>Az **Elemzés** gomb az eszközök és eszközkészletek telemetriáiból származó elemzéseket jelenít meg. Az operátorok létrehozhatnak egyéni nézeteket az eszközadatokhoz, így megjelenítve az alkalmazásból származó megállapításokat.</li><li>A **Feladatok** gomb tömeges eszközkezelést tesz lehetővé a méretezhető frissítéseket végző feladatok létrehozásával és futtatásával.</li><li>A **eszközsablonok** gombon szerkesztő segítségével eszköz sablonokat létrehozni és felügyelni az eszközöket.</li><li>A **folyamatos adatexportálás** a rendszergazda konfigurálja a folyamatos exportálást, és más Azure-szolgáltatások például a storage és a várólisták gombra.</li><li>Az **Adminisztráció** gomb jeleníti meg azokat az alkalmazásadminisztrációs oldalakat, ahol a rendszergazda az alkalmazás beállításait, felhasználóit és szerepköreit kezelheti.</li></ul> |

## <a name="search-help-and-support"></a>Keresés, súgó és támogatás

Minden oldalon megjelenik a felső menü:

![Eszköztár](media/overview-iot-central-tour/toolbar.png)

- Eszközsablonok és eszközök keresése, adjon meg egy **keresési** értéket.
- A felhasználói felületi nyelven vagy a téma módosításához válassza a **beállítások** ikonra.
- Jelentkezzen ki az alkalmazást, válassza a **fiók** ikonra.
- Ha segítségre és támogatásra van szüksége, kattintson a **Súgó** elemre, amely megnyitja az erőforrások legördülő listáját.

A kezelőfelületen egy világos és egy sötét téma közül választhat:

![A kezelőfelület témájának kiválasztása](media/overview-iot-central-tour/themes.png)

## <a name="dashboard"></a>Irányítópult

![Irányítópult](media/overview-iot-central-tour/homepage.png)

Az irányítópult Ez az első lap jelenik meg, jelentkezzen be az Azure IoT Central alkalmazáshoz. Szerkesztő, mint az irányítópult más felhasználók számára testreszabható hozzáadhat csempéket. További részletekért tekintse át [az Azure IoT Central operátori nézeteinek testreszabására](tutorial-customize-operator.md) vonatkozó oktatóanyagot. A felhasználók is [saját személyes irányítópultok kialakítása](howto-personalize-dashboard.md).

## <a name="device-explorer"></a>Eszközkereső

![Kereső oldal](media/overview-iot-central-tour/explorer.png)

A kezelő lap megjeleníti a _eszközök_ szerint csoportosítva, az Azure IoT Central alkalmazásban _eszköz sablon_.

* Az eszközsablonok egy-egy olyan eszköztípust határoznak meg, amelyek csatlakozhatnak az alkalmazáshoz. További tudnivalókért lásd az [új eszköztípus az Azure IoT Central-alkalmazásokban történő definiálását](tutorial-define-device-type.md) ismertető szakaszt.
* Egy eszköz egy valós vagy szimulált eszközt jelöl az alkalmazásban. További tudnivalókért lásd az [új eszköz az Azure IoT Central-alkalmazásokban történő hozzáadását](tutorial-add-device.md) ismertető szakaszt.

## <a name="device-sets"></a>Eszközkészletek

![Eszközkészletek oldal](media/overview-iot-central-tour/devicesets.png)

Az _Eszközkészletek_ oldalon jelennek meg a szerkesztő által létrehozott eszközkészletek. Az eszközkészletek egymáshoz kapcsolódó eszközök gyűjteményei. A szerkesztő meghatároz egy lekérdezést, amely azonosítja azokat az eszközöket, amelyek egy eszközkészlethez tartoznak. Az eszközkészletek az alkalmazáson belüli elemzések testreszabásakor használatosak. További tudnivalókért lásd az [eszközkészletek az Azure IoT Central-alkalmazásokban történő használatát](howto-use-device-sets.md) ismertető szakaszt.

## <a name="analytics"></a>Elemzés

![Elemzés oldal](media/overview-iot-central-tour/analytics.png)

Az Elemzés oldalon diagramok láthatók, amelyek segítenek áttekinteni az alkalmazáshoz csatlakozó eszközök viselkedését. Az operátorok ezen az oldalon monitorozhatják és vizsgálhatják ki a csatlakoztatott eszközökkel kapcsolatos problémákat. Az ezen az oldalon megjelenő diagramokat a szerkesztő határozza meg. További tudnivalókért lásd az [egyéni elemzések az Azure IoT Central-alkalmazásokban történő létrehozását](howto-use-device-sets.md) ismertető szakaszt.

## <a name="jobs"></a>Feladatok

![Feladatok oldal](media/overview-iot-central-tour/jobs.png)

A feladatok oldal csoportos eszközfelügyeleti műveletek elvégzését teszi lehetővé az eszközökön. A szerkesztő az oldalt az eszköz tulajdonságok, beállítások és parancsok frissítésére használja. További tudnivalókért tekintse meg a [Feladat futtatása](howto-run-a-job.md) cikket.

## <a name="device-templates"></a>Eszközsablonok

![Eszköz sablonok lap](media/overview-iot-central-tour/templates.png)

Az eszköz sablonok lap, ahol egy jelentéskészítő létrehozza és kezeli az eszköz sablonok az alkalmazásban. További tudnivalókért lásd az [új eszköztípus az Azure IoT Central-alkalmazásokban történő definiálását](tutorial-define-device-type.md) ismertető szakaszt.

## <a name="continuous-data-export"></a>Folyamatos adatexportálás

![Folyamatos adatexportálás lap](media/overview-iot-central-tour/export.png)

A folyamatos Exportálás lap, ahol a rendszergazda határozza meg az alkalmazásból származó adatok, telemetriai adatokat, például exportálása. Más szolgáltatások is az exportált adatok tárolására, vagy használhatja az elemzés. További tudnivalókért tekintse meg a [exportálhatja az adatokat az Azure IoT Central](howto-export-data.md) cikk.

## <a name="administration"></a>Adminisztráció

![Adminisztráció oldal](media/overview-iot-central-tour/administration.png)

Az Adminisztráció oldal hivatkozásokat tartalmaz a rendszergazda által például az alkalmazás felhasználóinak és szerepköreinek meghatározásához használt eszközökhöz. További tudnivalókért lásd az [Azure IoT Central-alkalmazások adminisztrációját](howto-administer.md) ismertető szakaszt.

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az Azure IoT Central jellemzőit, és megismerkedett a kezelőfelület elrendezésével, a javasolt következő lépés az [Azure IoT Central-alkalmazás létrehozását](quick-deploy-iot-central.md) ismertető rövid útmutató elvégzése.