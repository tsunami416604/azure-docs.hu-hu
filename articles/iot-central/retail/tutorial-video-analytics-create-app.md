---
title: Oktatóanyag – video Analytics-objektum-és mozgásészlelési alkalmazás létrehozása az Azure-ban IoT Central
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egy video Analytics-alkalmazást a IoT Centralban. Létrehozhatja, testreszabhatja és kapcsolódhat más Azure-szolgáltatásokhoz.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 07/31/2020
ms.openlocfilehash: b98406984f2c9f2adfca030369a6ea3d47a786f3
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2020
ms.locfileid: "88762777"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central"></a>Oktatóanyag: video Analytics-objektum-és mozgásészlelési alkalmazás létrehozása az Azure-ban IoT Central

Megoldás-szerkesztőként megtudhatja, hogyan hozhat létre video Analytics-alkalmazást a IoT Central *video Analytics – Object és Motion Detection* alkalmazás sablonnal, Azure IoT Edge eszközökkel és Azure Media Servicesekkel. A megoldás egy kiskereskedelmi áruházban mutatja be, hogyan teljesíti a közös üzleti igényeket a biztonsági kamerák figyelésére. A megoldás automatikus objektum-észlelést használ egy videó-hírcsatornában érdekes események gyors azonosításához és megkereséséhez.

A minta alkalmazás két szimulált eszközt és egy IoT Edge átjárót tartalmaz. Az alábbi oktatóanyagok két módszert mutatnak be a kísérlethez és az átjáró képességeinek megismeréséhez:

* Hozza létre az IoT Edge átjárót egy Azure-beli virtuális gépen, és csatlakoztasson egy szimulált kamerát.
* Hozza létre az IoT Edge-átjárót egy valódi eszközön, például egy Intel-NUC, és csatlakoztasson egy valódi kamerát.

Az oktatóanyag a következőket ismerteti:
> [!div class="checklist"]
> * Hozzon létre egy kiskereskedelmi áruházbeli alkalmazást az Azure IoT Central video Analytics alkalmazás sablon használatával
> * Az Alkalmazásbeállítások testreszabása
> * IoT Edge Gateway-eszközhöz tartozó eszköz sablonjának létrehozása
> * Átjáró-eszköz hozzáadása a IoT Central alkalmazáshoz

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag-Sorozat elvégzéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs oldalán](https://aka.ms/createazuresubscription).
* Ha valódi kamerát használ, kapcsolatra van szüksége a IoT Edge eszköz és a kamera között, és szüksége van a **valós idejű Streaming Protocol** -csatornára.

## <a name="initial-setup"></a>Kezdeti beállítás

Ezekben az oktatóanyagokban számos konfigurációs fájl frissítésére és használatára van lehetőség. Ezeknek a fájloknak a kezdeti verziói a [LVA-Gateway GitHub-](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) tárházban érhetők el. A tárház tartalmaz egy firkatömb szövegfájlt is, amellyel letöltheti és felhasználhatja a konfigurációs értékek rögzítését a telepített szolgáltatásokból.

Hozzon létre egy *LVA-Configuration* nevű mappát a helyi gépen a fájlok másolatainak mentéséhez. Ezután kattintson a jobb gombbal az alábbi hivatkozásokra, és válassza a **Mentés másként** lehetőséget a fájl mentéséhez a *LVA* mappába:

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.amd64.jsbekapcsolva](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.jsbekapcsolva](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.jsbekapcsolva](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

> [!NOTE]
> A GitHub-tárház tartalmazza a **LvaEdgeGatewayModule** és a **lvaYolov3** IoT Edge moduljainak forráskódját is. További információ a forráskód használatáról: a [LVA-átjáró moduljainak összeállítása](tutorial-video-analytics-build-module.md).

## <a name="deploy-and-configure-azure-media-services"></a>Azure Media Services üzembe helyezése és konfigurálása

A megoldás egy Azure Media Services fiókot használ az IoT Edge Gateway-eszköz által készített objektum-észlelési videoklipek tárolásához.

A Media Services fiók létrehozásakor:

- Meg kell adnia egy fióknevet, egy Azure-előfizetést, egy helyet, egy erőforráscsoportot és egy Storage-fiókot. Hozzon létre egy új Storage-fiókot az alapértelmezett beállításokkal a Media Services fiók létrehozásakor.

- Válassza ki az **USA keleti** régióját a helyhez.

- Hozzon létre egy *LVA-RG* nevű új ERŐFORRÁSCSOPORTOT az **USA keleti** régiójában a Media Services és a Storage-fiókokhoz. Az oktatóanyagok befejezése után egyszerűen eltávolíthatja az összes erőforrást a *LVA-RG* erőforráscsoport törlésével.

Hozza létre a [Media Services fiókot a Azure Portalban](https://portal.azure.com/?r=1#create/Microsoft.MediaService).

> [!TIP]
> Ezek az oktatóanyagok az **USA keleti** régióját használják a példákban. Ha szeretné, a legközelebbi régiót használhatja.

Jegyezze fel **Media Services** fiókjának nevét a *scratchpad.txt* fájlban.

Az üzembe helyezés befejezésekor navigáljon a **Media Services** fiók **tulajdonságlapján** . Jegyezze fel az erőforrás- **azonosítót** a *scratchpad.txt* fájlban, ezt az értéket később a IoT Edge modul konfigurálásakor kell használni.

Ezután konfiguráljon egy Azure Active Directory egyszerű szolgáltatásnevet a Media Services erőforráshoz. Válassza az **API-hozzáférés** , majd az **egyszerű szolgáltatás hitelesítés**lehetőséget. Hozzon létre egy új Azure Active Directory alkalmazást a Media Services-erőforrás nevével megegyező névvel, és hozzon létre egy titkos kulcsot a Leírás *IoT Edge a hozzáférés*.

:::image type="content" source="./media/tutorial-video-analytics-create-app/media-service-authentication.png" alt-text="Az AMS HRE alkalmazásának konfigurálása":::

A titkos kód létrehozásakor görgessen le a **hitelesítő adatok másolása az egyszerű szolgáltatásnév alkalmazásához** szakaszhoz. Ezután válassza a **JSON**elemet. A hitelesítő adatokat innen másolhatja egy menetben. Jegyezze fel ezt az információt a *scratchpad.txt* fájlban, amelyet később a IoT Edge eszköz konfigurálásakor fog használni.

> [!WARNING]
> Ez az egyetlen lehetőség a titok megtekintésére és mentésére. Ha elveszíti, egy újabb titkot kell kiállítania.

## <a name="create-the-azure-iot-central-application"></a>Az Azure IoT Central-alkalmazás létrehozása

Ebben a szakaszban új Azure IoT Central alkalmazást hoz létre egy sablonból. Ezt az alkalmazást az oktatóanyag-sorozaton belül egy teljes megoldás létrehozásához használhatja.

Új Azure IoT Central-alkalmazás létrehozása:

1. Navigáljon az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyére.

1. Jelentkezzen be az Azure-előfizetés eléréséhez használt hitelesítő adatokkal.

1. Új Azure IoT Central-alkalmazás létrehozásának megkezdéséhez válassza az **új alkalmazás** lehetőséget a **Build** lapon.

1. Válassza a **kereskedelmi**lehetőséget. A kiskereskedelmi oldalon számos kereskedelmi alkalmazás-sablon látható.

Új video Analytics-alkalmazás létrehozása:

1. Válassza ki a **videó-elemzés-objektum és a mozgásérzékelő** alkalmazás sablonját. Ez a sablon tartalmazza az oktatóanyagban használt eszközökhöz tartozó eszközök sablonjait. A sablon olyan minta-irányítópultokat tartalmaz, amelyeket az operátorok olyan feladatok végrehajtására használhatnak, mint például a kamerák figyelése és kezelése.

1. Igény szerint válasszon egy felhasználóbarát **nevet**. Ez az alkalmazás a Northwind Traders nevű kitalált kereskedelmi áruházon alapul. Az oktatóanyag a *Northwind Traders video Analytics* **alkalmazás nevét** használja.

    > [!NOTE]
    > Ha felhasználóbarát **alkalmazást**használ, továbbra is egyedi értéket kell használnia az alkalmazás **URL-címéhez**.

1. Ha rendelkezik Azure-előfizetéssel, válassza ki a **címtárat**, az **Azure-előfizetést**és a **Egyesült Államok** **helyét**. Ha nem rendelkezik előfizetéssel, engedélyezheti a **7 napos ingyenes próbaidőszakot** , és elvégezheti a szükséges kapcsolattartási adatokat. Ez az oktatóanyag három eszközt használ – két kamerát és egy IoT Edge eszközt – ha nem használja az ingyenes próbaverziót, a használatért kell fizetnie.

    További információ a címtárakról, előfizetésekről és helyszínekről: [alkalmazás létrehozása](../core/quick-deploy-iot-central.md)– rövid útmutató.

1. Kattintson a **Létrehozás** gombra.

    :::image type="content" source="./media/tutorial-video-analytics-create-app/new-application.png" alt-text="Azure IoT Central Alkalmazás létrehozása oldal":::

### <a name="retrieve-the-configuration-data"></a>Konfigurációs adatlekérdezés

Az oktatóanyag későbbi részében a IoT Edge-átjáró konfigurálásakor szükség van néhány konfigurációs adatokra a IoT Central alkalmazásból. A IoT Edge eszköznek szüksége van ezekre az információkra a regisztrálásához és az alkalmazáshoz való kapcsolódáshoz.

Az **Adminisztráció** szakaszban válassza ki az **alkalmazást** , és jegyezze fel az **alkalmazás URL-címét** és az **alkalmazás azonosítóját** a *scratchpad.txt* fájlban:

:::image type="content" source="./media/tutorial-video-analytics-create-app/administration.png" alt-text="Felügyelet":::

Válassza ki az **API-jogkivonatokat** , és állítson elő egy **LVAEdgeToken** nevű új jogkivonatot az **operátori** szerepkörhöz:

:::image type="content" source="./media/tutorial-video-analytics-create-app/token.png" alt-text="Jogkivonat előállítása":::

Jegyezze fel a tokent a *scratchpad.txt* fájlban később. A párbeszédpanel bezárása után a tokent nem lehet újból megtekinteni.

Az **Adminisztráció** szakaszban válassza az **eszköz csatlakoztatása**lehetőséget, majd válassza az **sas-IoT-Devices**elemet.

Jegyezze fel az eszközök **elsődleges kulcsát** a *scratchpad.txt* fájlban. Ezt az *elsődleges csoport megosztott hozzáférési aláírási tokenjét* később, az IoT Edge eszköz konfigurálásakor kell használni.

## <a name="edit-the-deployment-manifest"></a>Az üzembe helyezési jegyzék szerkesztése

Üzembe helyez egy IoT Edge modult egy üzembe helyezési jegyzékfájl használatával. IoT Central importálhatja a jegyzékfájlt sablonként, majd engedélyezheti IoT Central a modul központi telepítésének kezelését.

Az üzembe helyezési jegyzék előkészítése:

1. Nyissa meg a *deployment.amd64.js* fájlt, amelyet a *LVA-konfigurációs* mappában mentett a szövegszerkesztő használatával.

1. Keresse meg a `LvaEdgeGatewayModule` beállításokat, és módosítsa a rendszerkép nevét az alábbi kódrészletben látható módon:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Adja hozzá a Media Services fiók nevét a `env` szakasz csomópontjában `LvaEdgeGatewayModule` . A fiók nevét a *scratchpad.txt* fájlban jegyezze fel:

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_ACCOUNT_NAME>"
        }
    }
    ```

1. A sablon nem teszi elérhetővé ezeket a tulajdonságokat IoT Centralban, ezért hozzá kell adnia a Media Services konfigurációs értékeit az üzembe helyezési jegyzékhez. Keresse meg a `lvaEdge` modult, és cserélje le a helyőrzőket azokra az értékekre, amelyeket a Media Services-fiók létrehozásakor a *scratchpad.txt* fájlban jegyzett adott meg.

    Az az `azureMediaServicesArmId` **erőforrás-azonosító** , amelyet a Media Services fiók létrehozásakor a *scratchpad.txt* fájlban jegyzett készített.

    A `aadTenantId` `aadServicePrincipalAppId` (z),, és ascratchpad.txtfájlban jegyezze fel a Media Services- `aadServicePrincipalSecret` fiókhoz tartozó egyszerű szolgáltatásnév létrehozásakor: *scratchpad.txt*

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Mentse a módosításokat.

Igény szerint lecserélheti a Yolov3 modult az Intel hardveres optimalizált [OpenVINO &trade; Model Server – Edge AI bővítménnyel](https://github.com/openvinotoolkit/model_server/tree/ams/extras/ams_wrapper) . Letölthető egy minta üzembe helyezési jegyzékfájl [deployment.openvino.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json). Ez a jegyzékfájl a modul konfigurációs beállításait váltja `lvaYolov3` fel a következő konfigurációval:

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

## <a name="create-the-azure-iot-edge-gateway-device"></a>Az Azure IoT Edge Gateway-eszköz létrehozása

A video Analytics – Object és Motion észlelési alkalmazás tartalmaz egy **LVA Edge Object detektor** -eszközt és egy **LVA Edge mozgásérzékelő-észlelési** eszköz sablonját. Ebben a szakaszban egy átjáró-eszköz sablont hoz létre a központi telepítési jegyzékfájl használatával, és hozzáadja az átjáró eszközt a IoT Central alkalmazáshoz.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>Eszköz sablonjának létrehozása a LVA Edge-átjáróhoz

Az üzembe helyezési jegyzék importálása és az **LVA Edge Gateway** -sablon létrehozása:

1. A IoT Central alkalmazásban navigáljon az **eszközök sablonjaihoz**, és válassza az **+ új**lehetőséget.

1. A **sablon típusának kiválasztása** lapon válassza a **Azure IoT Edge** csempét. Ezután válassza a **Tovább: testreszabás**lehetőséget.

1. Az **Azure IoT Edge üzembe helyezési jegyzék feltöltése** lapon adja meg a *LVA Edge Gateway* nevet a sablon neveként, és tekintse meg az **átjáró eszközét az alsóbb rétegbeli eszközökkel**.

    Az üzembe helyezési jegyzékfájlt még ne keresse meg. Ha így tesz, a központi telepítési varázsló minden modulhoz egy felületet vár, de csak a **LvaEdgeGatewayModule**felületét kell kitennie. A jegyzékfájlt egy későbbi lépésben töltheti fel.

    :::image type="content" source="./media/tutorial-video-analytics-create-app/upload-deployment-manifest.png" alt-text="Ne töltse fel az üzembe helyezési jegyzéket":::

    Válassza a **Next: Review** (Tovább: Áttekintés) lehetőséget.

1. Az **Áttekintés** lapon válassza a **Létrehozás**lehetőséget.

### <a name="import-the-device-capability-model"></a>Az eszköz képességi modell importálása

Az eszköz sablonjának tartalmaznia kell egy eszköz-képesség modellt. Az **LVA Edge-átjáró** lapon válassza az **importálási képesség modellje** csempét. Navigáljon a korábban létrehozott *LVA-konfigurációs* mappához, és válassza ki a *LvaEdgeGatewayDcm.js* fájlt.

Az **LVA Edge Gateway** -eszköz most már tartalmazza a **LVA Edge-átjáró modulját** , valamint három felületet: az **eszköz információi**, a **LVA Edge-átjáró beállításai**és az **LVA Edge Gateway felülete**.

### <a name="replace-the-manifest"></a>A jegyzékfájl cseréje

Az **LVA Edge-átjáró** lapon válassza a **+ jegyzékfájl cseréje**lehetőséget.

:::image type="content" source="./media/tutorial-video-analytics-create-app/replace-manifest.png" alt-text="Jegyzékfájl cseréje":::

Navigáljon a *LVA* mappára, és válassza ki a korábban szerkesztett *deployment.amd64.jsa* jegyzékfájlban. Válassza a **Feltöltés** lehetőséget. Az ellenőrzés befejezésekor válassza a **replace (csere**) lehetőséget.

### <a name="add-relationships"></a>Kapcsolatok hozzáadása

Az **LVA Edge Gateway** -eszköz sablonjában, a **modulok/LVA Edge-átjáró modul**alatt válassza a **kapcsolatok**lehetőséget. Válassza a **+ kapcsolat hozzáadása** lehetőséget, és adja hozzá a következő két kapcsolatot:

|Megjelenítendő név               |Név          |Cél |
|-------------------------- |------------- |------ |
|LVA Edge mozgásérzékelő   |Alapértelmezett használata   |LVA Edge mozgásérzékelő eszköz |
|LVA Edge-objektum detektora   |Alapértelmezett használata   |LVA Edge Object detektor eszköz |

Kattintson a **Mentés** gombra.

:::image type="content" source="media/tutorial-video-analytics-create-app/relationships.png" alt-text="Kapcsolatok hozzáadása":::

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

    :::image type="content" source="media/tutorial-video-analytics-create-app/gateway-dashboard.png" alt-text="Irányítópult":::

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

1. Válassza a **Kapcsolódás** lehetőséget.

1. Az **eszköz csatlakoztatása** lapon jegyezze fel az **azonosító hatókörének** *scratchpad.txt* fájlját, az **eszköz azonosítóját**és az eszköz **elsődleges kulcsát**. Ezeket az értékeket később használhatja.

1. Győződjön meg arról, hogy a kapcsolati módszer a **közös hozzáférési aláírásra**van beállítva.

1. Válassza a **Bezárás** lehetőséget.

## <a name="next-steps"></a>További lépések

Ezzel létrehozott egy IoT Central alkalmazást a **video Analytics-Object és a mozgásérzékelő** alkalmazás sablonnal, létrehozott egy eszköz sablont az átjáró eszközhöz, és hozzáadta az átjáró-eszközt az alkalmazáshoz.

Ha olyan IoT Edge-modulokkal szeretné kipróbálni a video Analytics – Object és Motion Detection alkalmazást, amelyeken szimulált videó streamekkel rendelkező felhőalapú virtuális gépek futnak:

> [!div class="nextstepaction"]
> [IoT Edge-példány létrehozása a video analyticshez (Linux rendszerű virtuális gép)](./tutorial-video-analytics-iot-edge-vm.md)

Ha olyan IoT Edge-modulokkal szeretné kipróbálni a video Analytics – Object és Motion Detection alkalmazást, amelyek valódi **ONVIF** kamera használatával futtatnak valós eszközt:

> [!div class="nextstepaction"]
> [IoT Edge-példány létrehozása a video analyticshez (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md)
