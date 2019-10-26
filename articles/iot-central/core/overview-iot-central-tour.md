---
title: Ismerkedés az Azure IoT Central kezelőfelületével | Microsoft Docs
description: Szerkesztőként érdemes megismerkedni az IoT-megoldások létrehozásához használt Azure IoT Central kezelőfelületének fő területeivel.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 6e375e809308da8be0723fb2fcbdf718e9fb5479
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957768"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Ismerkedés az Azure IoT Central kezelőfelületével

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

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

## <a name="use-the-left-pane"></a>A bal oldali ablaktábla használata

A bal oldali ablaktábla használatával érheti el az alkalmazás különböző területeit. A navigációs sávot kibonthatja vagy összecsukhatja **<** vagy **>** kiválasztásával:

:::row:::
  :::column span="":::
      ![bal oldali ablaktábla](media/overview-iot-central-tour/navigationbar.png)
  :::column-end:::
  :::column span="2":::
     Az **irányítópult** megjeleníti az alkalmazás irányítópultját. Építőként testreszabhatja az irányítópultot a kezelők számára. A felhasználók emellett saját irányítópultokat is létrehozhatnak.
    
     **Device Explorer** felsorolja az alkalmazásban az egyes eszközökhöz társított szimulált és valós eszközöket. Az operátorok az **Eszközkereső** használatával kezelhetik a csatlakoztatott eszközöket.
    
     Az **eszközök készletei** lehetővé teszik az eszközbeállítások megtekintését és létrehozását. Az operátorok létrehozhatnak eszközkészleteket, amelyek a lekérdezés által megadott eszközök logikai gyűjteményei.
    
     Az **elemzések** az eszközök és az eszközök telemetria származtatott elemzéseket jelenítik meg. Az operátorok létrehozhatnak egyéni nézeteket az eszközadatokhoz, így megjelenítve az alkalmazásból származó megállapításokat.
    
     A **feladatok** lehetővé teszik a tömeges eszközök felügyeletét azáltal, hogy feladatokkal hozza létre és futtatja a frissítéseket a méretezési feladatok végrehajtásához.
    
     Az **eszköz sablonjai** a Builder által az eszközök sablonjainak létrehozásához és kezeléséhez használt eszközöket jelenítik meg.
    
     A **folyamatos adatexportálás** lehetővé teszi a rendszergazda számára, hogy folyamatos exportálást konfiguráljon más Azure-szolgáltatásokba, például a Storage-ba és a várólistákba.
    
     Az **Adminisztráció** azokat az alkalmazás-felügyeleti lapokat jeleníti meg, ahol a rendszergazdák kezelhetik az Alkalmazásbeállítások, a felhasználók és a szerepkörök beállításait.
   :::column-end:::
:::row-end:::

## <a name="search-help-and-support"></a>Keresés, súgó és támogatás

Minden oldalon megjelenik a felső menü:

![Eszköztár](media/overview-iot-central-tour/toolbar.png)

- Az eszközök sablonjainak és eszközeinek kereséséhez adjon meg egy **keresési** értéket.
- A felhasználói felület nyelvének vagy témájának módosításához válassza a **Beállítások** ikont.
- Az alkalmazásból való kijelentkezéshez válassza a **fiók** ikont.
- Ha segítségre és támogatásra van szüksége, kattintson a **Súgó** elemre, amely megnyitja az erőforrások legördülő listáját. A próbaverziós alkalmazásokban a támogatási erőforrások közé tartozik az [élő csevegéshez](howto-show-hide-chat.md)való hozzáférés.

A kezelőfelületen egy világos és egy sötét téma közül választhat:

![A kezelőfelület témájának kiválasztása](media/overview-iot-central-tour/themes.png)

> [!NOTE]
> A világos és a sötét témák közötti választás nem érhető el, ha a rendszergazda egyéni témát konfigurált az alkalmazáshoz.

## <a name="dashboard"></a>Irányítópult

![Irányítópult](media/overview-iot-central-tour/homepage.png)

* Az irányítópult az első lap, amelyet az Azure IoT Central alkalmazásba való bejelentkezéskor láthat. Szerkesztőként csempék hozzáadásával testre szabhatja az alkalmazás irányítópultját más felhasználók számára. További részletekért tekintse át [az Azure IoT Central operátori nézeteinek testreszabására](tutorial-customize-operator.md) vonatkozó oktatóanyagot.

* Kezelőként személyre szabott irányítópultokat hozhat létre, és átválthat közöttük és az alapértelmezett irányítópulton is. További információt az [Azure IoT Central személyes irányítópultok létrehozása](howto-create-personal-dashboards.md) című cikkben talál.

## <a name="device-explorer"></a>Eszközkereső

![Kereső oldal](media/overview-iot-central-tour/explorer.png)

Az Explorer oldalon láthatók az Azure IoT Central alkalmazásban található _eszközök_ az _eszköz sablon_szerint csoportosítva.

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

A feladatok lap lehetővé teszi, hogy az eszközökön tömeges eszközkezelés műveleteket futtasson. A szerkesztő az oldalt az eszköz tulajdonságok, beállítások és parancsok frissítésére használja. További tudnivalókért tekintse meg a [Feladat futtatása](howto-run-a-job.md) cikket.

## <a name="device-templates"></a>Eszközök sablonjai

![Eszközök sablonjai lap](media/overview-iot-central-tour/templates.png)

Az eszközök sablonjai oldalon a Builder hozza létre és kezeli az alkalmazásban lévő eszközök sablonjait. Egy eszköz-sablon az eszköz jellemzőit határozza meg, például:

- Telemetria, állapot és események mérése.
- Beállítások és tulajdonságok.
- Parancsok.
- Események vagy telemetria értékek alapján történő szabályok.

További tudnivalókért lásd az [új eszköztípus az Azure IoT Central-alkalmazásokban történő definiálását](tutorial-define-device-type.md) ismertető szakaszt.

## <a name="continuous-data-export"></a>Folyamatos adatexportálás

![Folyamatos adatexportálási oldal](media/overview-iot-central-tour/export.png)

A folyamatos adatexportálás oldalon a rendszergazda határozza meg, hogyan továbbíthatja az adatok adatfolyamait, például a telemetria az alkalmazásból. Más szolgáltatások tárolhatják az exportált és az elemzéshez használható adattárakat. További információ: az [Azure-beli adatexportálás IoT Central](howto-export-data-blob-storage.md) cikk.

## <a name="administration"></a>Felügyelet

![Adminisztráció oldal](media/overview-iot-central-tour/administration.png)

Az adminisztráció lap a rendszergazda által használt eszközökre mutató hivatkozásokat tartalmaz, például a felhasználók és szerepkörök definiálása az alkalmazásban, valamint a felhasználói felület testreszabása. További tudnivalókért lásd az [Azure IoT Central-alkalmazások adminisztrációját](howto-administer.md) ismertető szakaszt.

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az Azure IoT Central jellemzőit, és megismerkedett a kezelőfelület elrendezésével, a javasolt következő lépés az [Azure IoT Central-alkalmazás létrehozását](quick-deploy-iot-central.md) ismertető rövid útmutató elvégzése.
