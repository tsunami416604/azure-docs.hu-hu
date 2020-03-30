---
title: Ismerkedés az Azure IoT Central kezelőfelületével | Microsoft Docs
description: Ismerkedjen meg az Azure IoT központi felhasználói felületének kulcsfontosságú területeivel, amelyek segítségével létrehozza, kezeli és használja az IoT-megoldást.
author: lmasieri
ms.author: lmasieri
ms.date: 12/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: b905b1e86810b25c4c94072d6cd414b993e2a883
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77426198"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Ismerkedés az Azure IoT Central kezelőfelületével



Ez a cikk a Microsoft Azure IoT Central kezelőfelületét mutatja be. A kezelőfelületet használhatja Azure IoT Central-megoldások és az azokhoz csatlakozó eszközök létrehozásához, felügyeletéhez és használatához.

_Megoldásszerkesztőként_az Azure IoT központi felhasználói felületét használhatja az Azure IoT Central-megoldás meghatározásához. A kezelőfelületen a következő műveletek végezhetők el:

* A megoldáshoz csatlakozó eszközök típusainak meghatározása.
* Az eszközökre vonatkozó szabályok és műveletek konfigurálása. 
* A felhasználói felület testreszabása a megoldást használó _operátorok_ számára.

Az _operátorok_ az Azure IoT Central kezelőfelületén felügyelhetik az Azure IoT Central-megoldást. A kezelőfelületen a következő műveletek végezhetők el:

* Eszközök monitorozása.
* Eszközök konfigurálása.
* Az eszközök hibáinak keresése és elhárítása.
* Új eszközök kiépítése.

## <a name="iot-central-homepage"></a>Az IoT Central honlapja

Az [IoT Central kezdőlapja](https://aka.ms/iotcentral-get-started) az a hely, ahol többet megtudhat az IoT Central ban elérhető legfrissebb hírekről és funkciókról, új alkalmazásokat hozhat létre, és megtekintheti és elindíthatja meglévő alkalmazását.

> [!div class="mx-imgBorder"]
> ![Az IoT Central honlapja](media/overview-iot-central-tour/iot-central-homepage-pnp.png)

### <a name="create-an-application"></a>Alkalmazás létrehozása

A Build szakaszban tallózhat az iparágszempontjából releváns IoT Central-sablonok listájában, hogy gyorsan elkezdhesse, vagy a nulláról kezdheti az egyéni alkalmazássablonhasználatával.  
> [!div class="mx-imgBorder"]
> ![IoT Central buildlap](media/overview-iot-central-tour/iot-central-build-pnp.png)

További információkért tekintse meg az [Azure IoT Central alkalmazás létrehozása](quick-deploy-iot-central.md) rövid útmutató.

### <a name="launch-your-application"></a>Az alkalmazás elindítása

Az IoT Central alkalmazást az Ön vagy a megoldáskészítő által az alkalmazás létrehozása során kiválasztott URL-címhez megkeresve indíthatja el. Az [IoT Central alkalmazáskezelőben](https://aka.ms/iotcentral-apps)is megtekintheti az összes olyan alkalmazás listáját, amelyhez hozzáférése van.

> [!div class="mx-imgBorder"]
> ![IoT Központi alkalmazáskezelő](media/overview-iot-central-tour/app-manager-pnp.png)

## <a name="navigate-your-application"></a>Navigálás az alkalmazásban

Miután az IoT-alkalmazásban, használja a bal oldali ablaktáblán a különböző területek eléréséhez. A bal oldali ablaktáblát az ablaktábla tetején található háromvonalas ikonkiválasztásával bonthatja ki vagy csukhatja össze:

> [!NOTE]
> A bal oldali ablaktáblában látható elemek a felhasználói szerepkörtől függenek. További információ [a felhasználók és szerepkörök kezeléséről.](howto-manage-users-roles.md) 

:::row:::
  :::column span="":::
      > [!div class="mx-imgBorder"]
      > ![bal oldali ablaktábla](media/overview-iot-central-tour/navigationbar-pnp.png)
  :::column-end:::
  :::column span="2":::
     **Az irányítópult** megjeleníti az alkalmazás irányítópultját. *Megoldásszerkesztőként*testreszabhatja az operátorok globális irányítópultját. Felhasználói szerepkörüktől függően az operátorok saját személyes irányítópultokat is létrehozhatnak.
     
     **Eszközök** lehetővé teszi, hogy kezelje a csatlakoztatott eszközök - valós és szimulált.

     **Az eszközcsoportok** lehetővé teszik a lekérdezés által megadott eszközök logikai gyűjteményeinek megtekintését és létrehozását. Ezt a lekérdezést mentheti, és az alkalmazáson keresztül eszközcsoportokat használhat tömeges műveletek végrehajtásához.

     **A szabályok** lehetővé teszik az eszközök figyelésére vonatkozó szabályok létrehozását és szerkesztését. A szabályok kiértékelése eszköztelemetria i és az aktiválható műveletek alapján történik.

     **Az Analytics** lehetővé teszi, hogy egyéni nézeteket hozzon létre az eszközadatok on-val, hogy elemzéseket nyerjen az alkalmazásból.

     **A feladatok** lehetővé teszik az eszközök nagyméretű kezelését tömeges műveletek futtatásával.

     **Az eszközsablonokban** hozhatja létre és kezelheti az alkalmazáshoz csatlakozó eszközök jellemzőit.

     **Az adatexportálás** lehetővé teszi a külső szolgáltatásokba – például a tárolókba és a várólistákba – történő folyamatos exportálás konfigurálását.

     **A felügyelet** az a hely, ahol kezelheti az alkalmazás beállításait, testreszabását, számlázását, felhasználóit és szerepköreit.

     **Az IoT Central** lehetővé teszi *a rendszergazdák számára,* hogy visszaugorjanak az IoT Central alkalmazáskezelőjéhez.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Keresés, súgó, téma és támogatás

Minden oldalon megjelenik a felső menü:

> [!div class="mx-imgBorder"]
> ![Eszköztár](media/overview-iot-central-tour/toolbar-pnp.png)

* Eszközsablonok és -eszközök kereséséhez adjon meg egy **Keresési** értéket.
* A felhasználói felület nyelvének vagy beállításainak módosításához kattintson a **Beállítások** ikonra. További információ [az alkalmazásbeállítások kezeléséről](howto-manage-preferences.md)
* Az alkalmazásból való kijelentkezéshez válassza a **Fiók** ikont.
* Ha segítségre és támogatásra van szüksége, kattintson a **Súgó** elemre, amely megnyitja az erőforrások legördülő listáját. Az ingyenes díjcsomag egyik alkalmazásában a támogatási források közé tartozik az [élő csevegéshez](howto-show-hide-chat.md)való hozzáférés.

A kezelőfelületen egy világos és egy sötét téma közül választhat:

> [!NOTE]
> A világos és a sötét témák közötti választás nem érhető el, ha a rendszergazda egyéni témát konfigurált az alkalmazáshoz.

> [!div class="mx-imgBorder"]
> ![A kezelőfelület témájának kiválasztása](media/overview-iot-central-tour/themes-pnp.png)

### <a name="dashboard"></a>Irányítópult
> [!div class="mx-imgBorder"]
> ![Irányítópult](media/overview-iot-central-tour/dashboard-pnp.png)

* Az irányítópult az első oldal, amelyet az Azure IoT Central alkalmazásba való bejelentkezéskor láthat. *Megoldásszerkesztőként*több globális alkalmazás-irányítópultot hozhat létre és szabhat testre más felhasználók számára. További információ a [csempék irányítópulthoz való hozzáadásáról](howto-add-tiles-to-your-dashboard.md)

* Operátorként *operator*, ha a felhasználói szerepkör lehetővé teszi, személyes irányítópultokat hozhat létre, hogy figyelemmel kísérhesse, mi érdekli. További információkért tekintse meg az [Azure IoT Central személyes irányítópultok](howto-create-personal-dashboards.md) létrehozása útmutató cikket.

### <a name="devices"></a>Eszközök

> [!div class="mx-imgBorder"]
> ![Eszközök lap](media/overview-iot-central-tour/devices-pnp.png)

A felfedező lapon az _eszközök_ az Azure IoT Central alkalmazás _eszközsablon_szerint csoportosítva. 

* Az eszközsablonok egy-egy olyan eszköztípust határoznak meg, amelyek csatlakozhatnak az alkalmazáshoz.
* Egy eszköz egy valós vagy szimulált eszközt jelöl az alkalmazásban.

További információ: [Az eszközök figyelése](./quick-monitor-devices.md) rövid útmutató. 

### <a name="device-groups"></a>Device groups

> [!div class="mx-imgBorder"]
> ![Eszközcsoportok lap](media/overview-iot-central-tour/device-groups-pnp.png)

Az eszközcsoport kapcsolódó eszközök gyűjteménye. A *megoldásszerkesztő* egy lekérdezést határoz meg az eszközcsoportban szereplő eszközök azonosítására. Az eszközcsoportok segítségével tömeges műveleteket hajthat végre az alkalmazásban. További információkért tekintse meg az [Eszközcsoportok használata az Azure IoT Central alkalmazás cikkében.](tutorial-use-device-groups.md)

### <a name="rules"></a>Szabályok
> [!div class="mx-imgBorder"]
> ![Szabályok lap](media/overview-iot-central-tour/rules-pnp.png)

A szabályok lap lehetővé teszi, hogy az eszközök telemetriai adatai, állapota vagy eseményei alapján határozza meg a szabályokat. Amikor egy szabály kigyullad, egy vagy több műveletet indíthat el – például e-mailt küldhet, értesíthet egy külső rendszert webhook-riasztásokon keresztül stb. A tanulásról a [Szabályok konfigurálása](tutorial-create-telemetry-rules.md) oktatóanyag című témakörben olvashat. 

### <a name="analytics"></a>Elemzés

> [!div class="mx-imgBorder"]
> ![Elemzés oldal](media/overview-iot-central-tour/analytics-pnp.png)

Az elemzés lehetővé teszi, hogy egyéni nézeteket hozzon létre az eszközadatok on-ból, hogy elemzéseket nyerjen az alkalmazásból. További információkért tekintse meg az [Azure IoT Central alkalmazáshoz](howto-create-analytics.md) készült elemzés létrehozása című cikket.

### <a name="jobs"></a>Feladatok

> [!div class="mx-imgBorder"]
> ![Feladatok oldal](media/overview-iot-central-tour/jobs-pnp.png)

A feladatok lap lehetővé teszi a tömeges eszközkezelési műveletek futtatását az eszközökön. Frissítheti az eszköz tulajdonságait, beállításait, és parancsokat hajthat végre az eszközcsoportokon. További tudnivalókért tekintse meg a [Feladat futtatása](howto-run-a-job.md) cikket.

### <a name="device-templates"></a>Eszközsablonok

> [!div class="mx-imgBorder"]
> ![Eszközsablonok lapja](media/overview-iot-central-tour/templates-pnp.png)

Az eszközsablonok lap az, ahol a szerkesztő létrehozza és kezeli az alkalmazásban lévő eszközsablonokat. Az eszközsablon az eszközök jellemzőit határozza meg, például:

* Telemetriai, állapot- és eseménymérések
* Tulajdonságok
* Parancsok
* Nézetek

A *megoldáskészítő* űrlapokat és irányítópultokat is létrehozhat az operátorok számára az eszközök kezeléséhez.

További tudnivalókért lásd az [új eszköztípus az Azure IoT Central-alkalmazásokban történő definiálását](howto-set-up-template.md) ismertető szakaszt. 

### <a name="data-export"></a>Adatexportálás
> [!div class="mx-imgBorder"]
> ![Adatexportálási lap](media/overview-iot-central-tour/export-pnp.png)

Az adatexportálás lehetővé teszi az adatfolyamok, például a telemetriai adatok beállítását az alkalmazásból a külső rendszerekbe. További információkért tekintse meg az [Adatok exportálása az Azure IoT Central cikkben.](./howto-export-data.md)

### <a name="administration"></a>Adminisztráció
> [!div class="mx-imgBorder"]
> ![Adminisztráció oldal](media/overview-iot-central-tour/administration-pnp.png)

A felügyeleti lap lehetővé teszi az IoT Central alkalmazás konfigurálását és testreszabását. Itt módosíthatja az alkalmazás nevét, URL-címét, a téma, kezelheti a felhasználókat és a szerepköröket, API-jogkivonatokat hozhat létre, és exportálhatja az alkalmazást. További tudnivalókért lásd az [Azure IoT Central-alkalmazások adminisztrációját](howto-administer.md) ismertető szakaszt.

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az Azure IoT Central jellemzőit, és megismerkedett a kezelőfelület elrendezésével, a javasolt következő lépés az [Azure IoT Central-alkalmazás létrehozását](quick-deploy-iot-central.md) ismertető rövid útmutató elvégzése.
