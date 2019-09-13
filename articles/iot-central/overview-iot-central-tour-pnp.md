---
title: Ismerkedés az Azure IoT Central kezelőfelületével | Microsoft Docs
description: Szerkesztőként érdemes megismerkedni az IoT-megoldások létrehozásához használt Azure IoT Central kezelőfelületének fő területeivel.
author: dominicbetts
ms.author: dobett
ms.date: 07/05/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 82996db232fde0424ccc8e3e478a70a5892231e6
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884830"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui-preview-features"></a>Ismerkedjen meg az Azure IoT Central felhasználói felületével (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Ez a cikk a Microsoft Azure IoT Central kezelőfelületét mutatja be. A kezelőfelületet használhatja Azure IoT Central-megoldások és az azokhoz csatlakozó eszközök létrehozásához, felügyeletéhez és használatához.

A _szerkesztők_ az Azure IoT Central kezelőfelületén határozhatják meg az Azure IoT Central-megoldásokat. A kezelőfelületen a következő műveletek végezhetők el:

* A megoldáshoz csatlakozó eszközök típusainak meghatározása.
* Az eszközökre vonatkozó szabályok és műveletek konfigurálása.
* A felhasználói felület testreszabása a megoldást használó _operátorok_ számára.

Az _operátorok_ az Azure IoT Central kezelőfelületén felügyelhetik az Azure IoT Central-megoldást. A kezelőfelületen a következő műveletek végezhetők el:

* Eszközök monitorozása.
* Eszközök konfigurálása.
* Az eszközök hibáinak keresése és elhárítása.
* Új eszközök kiépítése.

## <a name="use-the-left-navigation-menu"></a>A bal oldali navigációs menü használata

A bal oldali navigációs menü használatával érheti el az alkalmazás különböző területeit. A navigációs sáv kibontásához vagy összecsukásához válassza **<** a **>** vagy a:

:::row:::
  :::column span="":::
      ![Left navigation menu](media/overview-iot-central-tour-pnp/navigationbar.png)
  :::column-end:::
  :::column span="2":::
     Az **irányítópult** megjeleníti az alkalmazás irányítópultját. Építőként testreszabhatja az irányítópultot a kezelők számára. A felhasználók emellett saját irányítópultokat is létrehozhatnak.
     
     Az **eszközök** felsorolja az alkalmazásban az egyes eszközökhöz társított szimulált és valós eszközöket. Az operátorok az **Eszközkereső** használatával kezelhetik a csatlakoztatott eszközöket.

     Az **eszközcsoport lehetővé teszi az erőforráscsoportok** megtekintését és létrehozását. Kezelőként létrehozhat eszközcsoport-csoportokat a lekérdezésben megadott eszközök logikai gyűjteményében.

     A **szabályok** lehetővé teszi az eszköz telemetria alapuló szabályok szerkesztését, valamint a testreszabható műveletek elindítását.

     Az **elemzések** az eszköz-és telemetria származó elemzéseket jelenítik meg. Az operátorok létrehozhatnak egyéni nézeteket az eszközadatokhoz, így megjelenítve az alkalmazásból származó megállapításokat.

     A **feladatok** lehetővé teszik a tömeges eszközök felügyeletét azáltal, hogy a feladatokat az eszközök nagy léptékű frissítéséhez hozza létre és futtatja.

     Az **eszköz sablonjai** a Builder által az eszközök sablonjainak létrehozásához és kezeléséhez használt eszközöket jelenítik meg.

     Az **adatexportálás** lehetővé teszi a rendszergazda számára, hogy a folyamatos exportálást más Azure-szolgáltatásokhoz, például a tárolóhoz és a várólistákhoz konfigurálja.

     Az **Adminisztráció** azokat az alkalmazás-felügyeleti lapokat jeleníti meg, ahol a rendszergazdák kezelhetik az Alkalmazásbeállítások, a felhasználók és a szerepkörök beállításait.
   :::column-end:::
:::row-end:::

## <a name="search-help-and-support"></a>Keresés, súgó és támogatás

Minden oldalon megjelenik a felső menü:

![Eszköztár](media/overview-iot-central-tour-pnp/toolbar.png)

* Az eszközök sablonjainak és eszközeinek kereséséhez adjon meg egy **keresési** értéket.
* A felhasználói felület nyelvének vagy témájának módosításához válassza a **Beállítások** ikont.
* Az alkalmazásból való kijelentkezéshez válassza a **fiók** ikont.
* Ha segítségre és támogatásra van szüksége, kattintson a **Súgó** elemre, amely megnyitja az erőforrások legördülő listáját. A próbaverziós alkalmazásokban a támogatási erőforrások közé tartozik az [élő csevegéshez](howto-show-hide-chat.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)való hozzáférés.

A kezelőfelületen egy világos és egy sötét téma közül választhat:

![A kezelőfelület témájának kiválasztása](media/overview-iot-central-tour-pnp/themes.png)

> [!NOTE]
> A világos és a sötét témák közötti választás nem érhető el, ha a rendszergazda egyéni témát konfigurált az alkalmazáshoz.

## <a name="dashboard"></a>Irányítópult

![Irányítópult](media/overview-iot-central-tour-pnp/homepage.png)

* Az irányítópult az első lap, amelyet az Azure IoT Central alkalmazásba való bejelentkezéskor láthat. Szerkesztőként csempék hozzáadásával testre szabhatja az alkalmazás irányítópultját más felhasználók számára. További tudnivalókat az [eszköz sablonjának beállítása](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) oktatóanyagban talál.

* Kezelőként személyre szabott irányítópultokat hozhat létre, és átválthat közöttük és az alapértelmezett irányítópulton is. További információért lásd a [személyes irányítópultok létrehozása és kezelése](howto-personalize-dashboard.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) című cikket.

## <a name="devices"></a>Eszközök

![Eszközök lap](media/overview-iot-central-tour-pnp/explorer.png)

Az Explorer oldalon láthatók az Azure IoT Central alkalmazásban található _eszközök_ az _eszköz sablon_szerint csoportosítva.

* Az eszközsablonok egy-egy olyan eszköztípust határoznak meg, amelyek csatlakozhatnak az alkalmazáshoz. További tudnivalókért lásd az [új eszköztípus az Azure IoT Central-alkalmazásokban történő definiálását](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) ismertető szakaszt.
* Egy eszköz egy valós vagy szimulált eszközt jelöl az alkalmazásban. További tudnivalókért lásd az [új eszköz az Azure IoT Central-alkalmazásokban történő hozzáadását](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) ismertető szakaszt.

## <a name="device-groups"></a>Eszközök csoportjai

![Eszközbeállítások lap](media/overview-iot-central-tour-pnp/devicesets.png)

Az _eszközcsoport oldalon láthatók a_ Builder által létrehozott eszközcsoport-csoportok. Az eszközcsoport kapcsolódó eszközök gyűjteménye. A szerkesztő definiál egy lekérdezést az eszközcsoport részét képező eszközök azonosításához. Az erőforráscsoportok az alkalmazásban található elemzések testreszabásakor használhatók. További tudnivalókat az [eszközök használata az Azure IoT Central alkalmazásban](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) című cikkben talál.

## <a name="rules"></a>Szabályok

![Szabályok lap](media/overview-iot-central-tour-pnp/rules.png)

A szabályok lapon megadhatja a telemetria, az eszköz állapotát vagy az eszköz eseményein alapuló szabályokat. Amikor egy szabály elindít egy műveletet, például egy e-mailt küldhet az operátornak. A Builder ezt a lapot használja a szabályok létrehozásához és kezeléséhez. További információ: az [eszközökre vonatkozó szabályok és műveletek konfigurálása az Azure IoT Central](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) oktatóanyagban.

## <a name="analytics"></a>Elemzés

![Elemzés oldal](media/overview-iot-central-tour-pnp/analytics.png)

Az Elemzés oldalon diagramok láthatók, amelyek segítenek áttekinteni az alkalmazáshoz csatlakozó eszközök viselkedését. Az operátorok ezen az oldalon monitorozhatják és vizsgálhatják ki a csatlakoztatott eszközökkel kapcsolatos problémákat. Az ezen az oldalon megjelenő diagramokat a szerkesztő határozza meg. További tudnivalókért lásd az [egyéni elemzések az Azure IoT Central-alkalmazásokban történő létrehozását](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) ismertető szakaszt.

## <a name="jobs"></a>Feladatok

![Feladatok oldal](media/overview-iot-central-tour-pnp/jobs.png)

A feladatok lap lehetővé teszi, hogy az eszközökön tömeges eszközkezelés műveleteket futtasson. A szerkesztő az oldalt az eszköz tulajdonságok, beállítások és parancsok frissítésére használja. További tudnivalókért tekintse meg a [Feladat futtatása](howto-run-a-job.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) cikket.

## <a name="device-templates"></a>Eszközök sablonjai

![Eszközök sablonjai lap](media/overview-iot-central-tour-pnp/templates.png)

Az eszközök sablonjai oldalon a Builder hozza létre és kezeli az alkalmazásban lévő eszközök sablonjait. Egy eszköz-sablon az eszköz jellemzőit határozza meg, például:

* Telemetria, állapot és események mérése.
* Tulajdonságok.
* Parancsok.

A szerkesztő létrehozhat űrlapokat és irányítópultokat is az eszközök kezelésére szolgáló operátorok számára.

További tudnivalókért lásd az [új eszköztípus az Azure IoT Central-alkalmazásokban történő definiálását](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) ismertető szakaszt.

## <a name="data-export"></a>Adatexportálás

![Adatexportálási lap](media/overview-iot-central-tour-pnp/export.png)

Az adatexportálás oldalon a rendszergazda határozza meg, hogyan továbbíthatja az adatok adatfolyamait, például a telemetria az alkalmazásból. Más szolgáltatások tárolhatják az exportált és az elemzéshez használható adattárakat. További információ: az [Azure-beli adatexportálás IoT Central](howto-export-data.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) cikk.

## <a name="administration"></a>Felügyelet

![Adminisztráció oldal](media/overview-iot-central-tour-pnp/administration.png)

Az adminisztráció lap a rendszergazda által használt eszközökre mutató hivatkozásokat tartalmaz, például a felhasználók és szerepkörök definiálása az alkalmazásban, valamint a felhasználói felület testreszabása. További tudnivalókért lásd az [Azure IoT Central-alkalmazások adminisztrációját](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) ismertető szakaszt.

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az Azure IoT Central jellemzőit, és megismerkedett a kezelőfelület elrendezésével, a javasolt következő lépés az [Azure IoT Central-alkalmazás létrehozását](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) ismertető rövid útmutató elvégzése.
