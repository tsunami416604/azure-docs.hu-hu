---
title: Ismerkedés az Azure IoT Central kezelőfelületével | Microsoft Docs
description: Ismerkedjen meg az Azure IoT Central felhasználói felületének azon főbb területeivel, amelyeket a IoT-megoldás létrehozásához, kezeléséhez és használatához használ.
author: lmasieri
ms.author: lmasieri
ms.date: 10/21/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 0bd078e8c920ca1ebe01767915036ca84650aadd
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958195"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui-preview-features"></a>Ismerkedjen meg az Azure IoT Central felhasználói felületével (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ez a cikk a Microsoft Azure IoT Central kezelőfelületét mutatja be. A kezelőfelületet használhatja Azure IoT Central-megoldások és az azokhoz csatlakozó eszközök létrehozásához, felügyeletéhez és használatához.

Megoldás- _szerkesztőként_az Azure IoT Central felhasználói felületén megadhatja az Azure IoT Central-megoldást. A kezelőfelületen a következő műveletek végezhetők el:

* A megoldáshoz csatlakozó eszközök típusainak meghatározása.
* Az eszközökre vonatkozó szabályok és műveletek konfigurálása. 
* A felhasználói felület testreszabása a megoldást használó _operátorok_ számára.

Az _operátorok_ az Azure IoT Central kezelőfelületén felügyelhetik az Azure IoT Central-megoldást. A kezelőfelületen a következő műveletek végezhetők el:

* Eszközök monitorozása.
* Eszközök konfigurálása.
* Az eszközök hibáinak keresése és elhárítása.
* Új eszközök kiépítése.

## <a name="iot-central-homepage"></a>IoT Central kezdőlapja

A [IoT Central kezdőlapja](https://aka.ms/iotcentral-get-started) az a hely, ahol további információt talál a IoT Central elérhető legfrissebb hírekről és szolgáltatásokról, új alkalmazások létrehozásáról, valamint a meglévő alkalmazás megnyitásáról.

> [!div class="mx-imgBorder"]
> ![IoT Central kezdőlapja](media/overview-iot-central-tour-pnp/iot-central-homepage-pnp.png)

### <a name="create-an-application"></a>Alkalmazás létrehozása

A build (létrehozás) szakaszban böngészheti az iparágban releváns IoT Central sablonok listáját, amelyekkel gyorsan elsajátíthatja az első lépéseket, vagy megkezdheti az előzményeket egy egyéni alkalmazás sablonjának használatával.  
> [!div class="mx-imgBorder"]
> ![IoT Central Build oldal](media/overview-iot-central-tour-pnp/iot-central-build-pnp.png)

További információ: [Azure IoT Central-alkalmazás létrehozása](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

### <a name="launch-your-application"></a>Az alkalmazás elindítása

A IoT Central alkalmazás elindításához nyissa meg az URL-címet, amelyet Ön vagy a megoldás-szerkesztője az alkalmazás létrehozása során választ. Megtekintheti a [IoT Central app Managerben](https://aka.ms/iotcentral-apps)elérhető összes alkalmazás listáját is.

> [!div class="mx-imgBorder"]
> ![IoT Central app Manager](media/overview-iot-central-tour-pnp/app-manager-pnp.png)

## <a name="navigate-your-application"></a>Navigáljon az alkalmazáshoz

A IoT alkalmazásban a bal oldali ablaktábla használatával férhet hozzá a különböző területekhez. A navigációs sáv kibontásához vagy összecsukásához válassza a navigációs sáv tetején látható három soros ikont:

> [!NOTE]
> A navigációs sávon megjelenő elemek a felhasználói szerepkörtől függenek. További információ a [felhasználók és a szerepkörök kezeléséről](howto-manage-users-roles-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). 

:::row:::
  :::column span="":::
      > [!div class="mx-imgBorder"]
      > ![bal oldali ablaktábla](media/overview-iot-central-tour-pnp/navigationbar-pnp.png)
  :::column-end:::
  :::column span="2":::
     Az **irányítópult** megjeleníti az alkalmazás irányítópultját. Megoldás- *szerkesztőként*testreszabhatja az operátorok globális irányítópultját. A kezelők saját felhasználói szerepkörtől függően saját irányítópultokat is létrehozhatnak.
     
     Az **eszközök** lehetővé teszik a csatlakoztatott eszközök felügyeletét – valós és szimulált.

     Az **eszközcsoport segítségével** megtekintheti és létrehozhatja a lekérdezésben megadott eszközök logikai gyűjteményeit. A lekérdezés menthető, és az alkalmazás használatával csoportos műveletek végrehajtására is használhatja az eszközöket.

     A **szabályok** lehetővé teszik az eszközök figyelésére vonatkozó szabályok létrehozását és szerkesztését. A szabályok kiértékelése az eszközök telemetria és a testre szabható műveletek elindításán alapul.

     Az **Analytics** lehetővé teszi, hogy egyéni nézeteket hozzon létre az eszköz adatain az alkalmazásból származó elemzések származtatása érdekében.

     A **feladatok** lehetővé teszik az eszközök méretezését nagy mennyiségű művelet futtatásával.

     Az eszközök **sablonjaival** létrehozhatja és kezelheti az alkalmazáshoz kapcsolódó eszközök jellemzőit.

     Az **adatexportálás** lehetővé teszi folyamatos exportálás konfigurálását külső szolgáltatásokra – például a tárterületre és a várólistákra.

     Az **adminisztrációval** kezelheti az alkalmazás beállításait, testreszabását, számlázását, felhasználóit és szerepköreit.

     **IoT Central** lehetővé teszi, hogy a *rendszergazdák* visszaugorjanak a IoT Central app Managerbe.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Keresés, Súgó, téma és támogatás

Minden oldalon megjelenik a felső menü:

> [!div class="mx-imgBorder"]
> ![eszköztár](media/overview-iot-central-tour-pnp/toolbar-pnp.png)

* Az eszközök sablonjainak és eszközeinek kereséséhez adjon meg egy **keresési** értéket.
* A felhasználói felület nyelvének vagy témájának módosításához válassza a **Beállítások** ikont. További információ [az alkalmazások beállításainak kezeléséről](howto-manage-preferences.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* Az alkalmazásból való kijelentkezéshez válassza a **fiók** ikont.
* Ha segítségre és támogatásra van szüksége, kattintson a **Súgó** elemre, amely megnyitja az erőforrások legördülő listáját. A próbaverziós alkalmazásokban a támogatási erőforrások közé tartozik az [élő csevegéshez](howto-show-hide-chat.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)való hozzáférés.

A kezelőfelületen egy világos és egy sötét téma közül választhat:

> [!NOTE]
> A világos és a sötét témák közötti választás nem érhető el, ha a rendszergazda egyéni témát konfigurált az alkalmazáshoz.

> [!div class="mx-imgBorder"]
> ![téma kiválasztása a felhasználói felületen](media/overview-iot-central-tour-pnp/themes-pnp.png)

### <a name="dashboard"></a>Irányítópult
> [!div class="mx-imgBorder"]
> ![Irányítópult](media/overview-iot-central-tour-pnp/dashboard-pnp.png)

* Az irányítópult az első lap, amelyet az Azure IoT Central alkalmazásba való bejelentkezéskor láthat. Megoldás- *szerkesztőként*több globális alkalmazás-irányítópultot hozhat létre és szabhat testre más felhasználók számára. További információ a [csempék irányítópulthoz való hozzáadásáról](howto-add-tiles-to-your-dashboard.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

* Ha a felhasználói szerepkör lehetővé *teszi, hogy*a felhasználó, személyes irányítópultokat is létrehozhat, amelyekkel figyelheti, hogy mit szeretne. További információt az [Azure IoT Central személyes irányítópultok létrehozása](howto-create-personal-dashboards.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) című cikkben talál.

### <a name="devices"></a>Eszközök

> [!div class="mx-imgBorder"]
> ![eszközök lap](media/overview-iot-central-tour-pnp/devices-pnp.png)

Az Explorer oldalon láthatók az Azure IoT Central alkalmazásban található _eszközök_ az _eszköz sablon_szerint csoportosítva. 

* Az eszközsablonok egy-egy olyan eszköztípust határoznak meg, amelyek csatlakozhatnak az alkalmazáshoz.
* Egy eszköz egy valós vagy szimulált eszközt jelöl az alkalmazásban.

További információt az [eszközök figyelése](tutorial-monitor-devices.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) oktatóanyagban talál. 

### <a name="device-groups"></a>Eszközök csoportjai

> [!div class="mx-imgBorder"]
> ![eszközcsoport lap](media/overview-iot-central-tour-pnp/device-groups-pnp.png)

Az eszközcsoport kapcsolódó eszközök gyűjteménye. A *megoldás-szerkesztő* egy lekérdezést határoz meg az eszközcsoport részét képező eszközök azonosításához. Az erőforráscsoportok használatával tömeges műveleteket hajthat végre az alkalmazásban. További tudnivalókat az [eszközök használata az Azure IoT Central alkalmazásban](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) című cikkben talál.

### <a name="rules"></a>Szabályok
> [!div class="mx-imgBorder"]
> ![szabályok lap](media/overview-iot-central-tour-pnp/rules-pnp.png)

A szabályok lapon megadhatja az eszközök telemetria, állapotának vagy eseményeinek alapjául szolgáló szabályokat. Egy szabály kiváltásakor egy vagy több műveletet indíthat el – például e-mailt küldhet, külső rendszer értesítése webhook-riasztásokon keresztül stb. További tudnivalókért tekintse meg a [szabályok konfigurálása](tutorial-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) oktatóanyagot. 

### <a name="analytics"></a>Elemzés

> [!div class="mx-imgBorder"]
> ![Analytics-lap](media/overview-iot-central-tour-pnp/analytics-pnp.png)

Az Analytics lehetővé teszi, hogy egyéni nézeteket hozzon létre az eszköz adatain az alkalmazásból származó elemzések kinyeréséhez. További tudnivalókért lásd az [egyéni elemzések az Azure IoT Central-alkalmazásokban történő létrehozását](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) ismertető szakaszt.

### <a name="jobs"></a>Feladatok

> [!div class="mx-imgBorder"]
> ![feladatok lap](media/overview-iot-central-tour-pnp/jobs-pnp.png)

A feladatok lap lehetővé teszi, hogy az eszközökön tömeges eszközkezelés műveleteket futtasson. Az eszközbeállítások, a beállítások és a parancsok végrehajtása az eszközök csoportjaira is frissíthető. További tudnivalókért tekintse meg a [Feladat futtatása](howto-run-a-job.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) cikket.

### <a name="device-templates"></a>Eszközök sablonjai

> [!div class="mx-imgBorder"]
> ![az eszközök sablonjai lapot](media/overview-iot-central-tour-pnp/templates-pnp.png)

Az eszközök sablonjai oldalon a Builder hozza létre és kezeli az alkalmazásban lévő eszközök sablonjait. Az eszköz sablonja az eszközök jellemzőit adja meg, például:

* Telemetria, állapot és események mérése
* Tulajdonságok
* Parancsok
* Nézetek

A *megoldás-szerkesztő* létrehozhat űrlapokat és irányítópultokat is az eszközök kezelésére szolgáló operátorok számára.

További tudnivalókért lásd az [új eszköztípus az Azure IoT Central-alkalmazásokban történő definiálását](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) ismertető szakaszt. 

### <a name="data-export"></a>Adatexportálás
> [!div class="mx-imgBorder"]
> ![adatexportálási lap](media/overview-iot-central-tour-pnp/export-pnp.png)

Az adatok exportálása lehetővé teszi az adatstreamek (például telemetria) beállítását az alkalmazásból a külső rendszerekre. További információ: az [Azure-beli adatexportálás IoT Central](howto-export-data-blob-storage.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) cikk.

### <a name="administration"></a>Felügyelet
> [!div class="mx-imgBorder"]
> ![adminisztrációs lap](media/overview-iot-central-tour-pnp/administration-pnp.png)

Az adminisztráció oldalon konfigurálhatja és testre szabhatja a IoT Central alkalmazást. Itt megváltoztathatja az alkalmazás nevét, URL-címét, a felhasználók és szerepkörök kezelését, API-jogkivonatok létrehozását és az alkalmazás exportálását. További tudnivalókért lásd az [Azure IoT Central-alkalmazások adminisztrációját](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) ismertető szakaszt.

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az Azure IoT Central jellemzőit, és megismerkedett a kezelőfelület elrendezésével, a javasolt következő lépés az [Azure IoT Central-alkalmazás létrehozását](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) ismertető rövid útmutató elvégzése.
