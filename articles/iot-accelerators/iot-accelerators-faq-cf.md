---
title: Csatlakoztatott gyár megoldás – gyakori kérdések – Azure |} A Microsoft Docs
description: Az Okosgyár-megoldásgyorsító kapcsolatos gyakori kérdések
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: d2f5ce97ee81deaf590771f4794af4adf0fdbd1b
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248587"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Gyakori kérdések az Okosgyár-megoldásgyorsító

Lásd még az általános [– gyakori kérdések](iot-accelerators-faq.md) az IoT-megoldásgyorsítók.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Hol található a forráskódban a megoldásgyorsító számára?

Az alábbi GitHub-adattárat a forráskód tárolja:

* [Csatlakoztatott Factory-megoldásgyorsítók](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Mi az OPC UA?

OPC egyesített architektúra (EE), 2008-ban, amely a standard szintű platformfüggetlen, szolgáltatásorientált együttműködési. Az OPC UA különféle ipari rendszerek és eszközök, például számítógépek iparági PLC és érzékelők használják. OPC UA egy bővíthető keretrendszer az a funkciók klasszikus OPC specifikációit integrálható a beépített biztonsági. Egy standard határozzák meg az OPC Foundation. A [OPC Foundation](http://opcfoundation.org/) egy nem nonprofit szervezet, több mint 440 tagjaival. A szervezet célja az, hogy használja az OPC-specifikációk keresztül több szállító, többplatformos, biztonságos és megbízható együttműködés megkönnyítése érdekében:

* Infrastruktúra
* Specifikációk
* Technológia
* Folyamatok

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Miért érdemes a Microsoft adta meg az OPC UA az Okosgyár-megoldásgyorsító a?

A Microsoft OPC UA úgy döntött, mert az egy nyílt és a nem tulajdonosi, platform független, iparági ismerhető fel és bevált szabvány. Követelmény Industrie 4.0-s verzióját (RAMI4.0) referencia architektúra megoldások biztosítása együttműködés gyártási folyamatok széles körét és a berendezések között. A Microsoft ügyfeleinek az Industrie 4.0 megoldásokat hozhat létre igény szerinti fog látni. Az OPC UA támogatása segítségével csökkentheti a korlát, az ügyfelek számára a kitűzött célokat, és azonnali üzleti értéket kell őket.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Hogyan nyilvános IP-cím hozzáadása a virtuális gép szimuláció?

Az IP-cím hozzáadása két lehetősége van:

* A PowerShell-szkripttel `Simulation/Factory/Add-SimulationPublicIp.ps1` a a [tárház](https://github.com/Azure/azure-iot-connected-factory). A központi telepítés neve paraméterként adja át. Egy helyi üzemelő példányának használata `<your username>ConnFactoryLocal`. A parancsfájl kiírja ezt a virtuális gép IP-címét.

* Az Azure Portalon keresse meg az erőforráscsoport, a központi telepítés. Egy helyi üzembe helyezés, kivéve az erőforráscsoporthoz a megoldás a megadott név vagy az üzemelő példány neve. A felépítési szkriptjének használatával helyi telepítés esetén az erőforráscsoport neve nem `<your username>ConnFactoryLocal`. Adjon hozzá egy új **nyilvános IP-cím** erőforrás ahhoz az erőforráscsoporthoz.

> [!NOTE]
> Mindkét esetben győződjön meg arról, utasításokat követve telepítheti a legújabb javításokat a [Ubuntu webhely](https://wiki.ubuntu.com/Security/Upgrades). A telepítés tarthatom naprakészen a mindaddig, amíg a virtuális gép nyilvános IP-cím keresztül érhető el.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Hogyan távolíthatom el a nyilvános IP-címet a virtuális gép szimuláció?

Az IP-cím eltávolítása két lehetősége van:

* A PowerShell-parancsfájlt Simulation/Factory/Remove-SimulationPublicIp.ps1, használja a [tárház](https://github.com/Azure/azure-iot-connected-factory). A központi telepítés neve paraméterként adja át. Egy helyi üzemelő példányának használata `<your username>ConnFactoryLocal`. A parancsfájl kiírja ezt a virtuális gép IP-címét.

* Az Azure Portalon keresse meg az erőforráscsoport, a központi telepítés. Egy helyi üzembe helyezés, kivéve az erőforráscsoporthoz a megoldás a megadott név vagy az üzemelő példány neve. A felépítési szkriptjének használatával helyi telepítés esetén az erőforráscsoport neve nem `<your username>ConnFactoryLocal`. Most eltávolítja az **nyilvános IP-cím** erőforrást az erőforráscsoportból.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Hogyan tudok bejelentkezni, a virtuális gép szimuláció?

Bejelentkezés a virtuális gép szimuláció csak támogatott, ha a megoldás a PowerShell-parancsfájllal központilag telepített `build.ps1` a a [tárház](https://github.com/Azure/azure-iot-connected-factory).

Ha üzembe helyezte a megoldást www.azureiotsolutions.com, nem jelentkezhet be a virtuális géphez. Nem tud bejelentkezni, mert a jelszó véletlenszerűen történik, és nem állítható alaphelyzetbe.

1. Nyilvános IP-cím hozzáadása a virtuális Géphez. Lásd: [Hogyan adhatok hozzá egy nyilvános IP-címet, a virtuális gép szimuláció?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Hozzon létre SSH-munkamenetből a virtuális gépre, IP-címet a virtuális gép használja.
1. A használandó felhasználónév: `docker`.
1. A jelszó használata a központi telepítéséhez használt verziójától függ:
    * A jelszó, 2017 június 1. előtt a build.ps1 parancsfájl használatával üzembe helyezett megoldások esetén: `Passw0rd`.
    * 2017 június 1. után a build.ps1 parancsfájl használatával üzembe helyezett megoldások, keresse meg a jelszót a `<name of your deployment>.config.user` fájlt. A jelszó tárolva van a **VmAdminPassword** beállítás. A jelszó létrehozásától véletlenszerűen üzembe helyezéskor, kivéve, ha megadja azt a használatával a `build.ps1` parancsfájl-paraméter `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Hogyan állítsa le és indítsa el az összes docker-folyamat és a virtuális gép szimuláció?

1. Jelentkezzen be a virtuális gép szimuláció. Lásd: [Hogyan tudok bejelentkezni, a virtuális gép szimuláció?](#how-do-i-sign-in-to-the-simulation-vm)
1. Annak ellenőrzéséhez, hogy mely tárolók aktívak, Futtatás: `docker ps`.
1. Minden szimulációs tároló leállításához futtassa: `./stopsimulation`.
1. Minden szimulációs tároló indítása:
    * Exportálja egy felületváltozóban nevű **IOTHUB_CONNECTIONSTRING**. Az értéket a **IotHubOwnerConnectionString** beállítását a `<name of your deployment>.config.user` fájlt. Példa:

        ```
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Futtassa az `./startsimulation` parancsot.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Hogyan frissíthetem a szimulációt, a virtuális gépen?

A szimuláció végzett módosításokat, ha a PowerShell-parancsfájlt használhatja `build.ps1` a a [tárház](https://github.com/Azure/azure-iot-connected-factory) használatával a `updatedimulation` parancsot. Ez a szkript minden szimulációs összetevő, a virtuális gépen a szimuláció leáll, feltölti, telepíti, létrehozásával és elindítja azokat.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Hogyan állapíthatom meg a kapcsolati karakterláncot a megoldás által használt IoT hub?

Ha üzembe helyezte a megoldás a `build.ps1` szkriptet a [tárház](https://github.com/Azure/azure-iot-connected-factory), a kapcsolati karakterlánc értékét az **IotHubOwnerConnectionString** a a `<name of your deployment>.config.user` fájlt.

A kapcsolati karakterláncot az Azure Portalon is megtalálhatja. Az IoT Hub erőforrás az üzembe helyezés az erőforráscsoportban keresse meg azt a kapcsolati karakterlánc beállításokat.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Mely IoT Hub-eszközöket használ a csatlakoztatott gyár szimuláció?

A szimuláció önkiszolgáló regisztrálja az alábbi eszközökhöz:

* proxy.beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.munich0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Használatával a [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) vagy [az IoT-bővítmény, az Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension) eszközzel ellenőrizheti, hogy mely eszközök regisztrációja az IoT hub, a megoldás által használt. Device explorer használatához szükség van a kapcsolati karakterláncot az IoT hub a központi telepítésben. Azure CLI 2.0, az IoT-bővítmény használatához szüksége az IoT Hub nevére.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Hogyan kaphatok naplóadatok szimuláció összetevők?

A szimuláció lévő valamennyi összetevőnél információk jelentkezzen be a naplófájlokat. Ezeket a fájlokat a virtuális gép megtalálható a mappa `home/docker/Logs`. A naplók begyűjtéséről, használhatja a PowerShell-parancsfájl `Simulation/Factory/Get-SimulationLogs.ps1` a a [tárház](https://github.com/Azure/azure-iot-connected-factory).

Jelentkezzen be a virtuális Gépet kell ezt a szkriptet. Szükség lehet a bejelentkezéshez adja meg a hitelesítő adatokat. Lásd: [Hogyan tudok bejelentkezni, a virtuális gép szimuláció?](#how-do-i-sign-in-to-the-simulation-vm) a hitelesítő adatok kereséséhez.

A szkript hozzáadása/eltávolítása egy nyilvános IP-címet a virtuális géphez, ha még nem rendelkezik egy, és eltávolítja azt. A parancsfájl minden naplófájlok helyezi az archívumot, és letölti az archívum a fejlesztő munkaállomás.

Alternatív megoldásként jelentkezzen be a virtuális Gépre SSH-n keresztül, és vizsgálja meg a naplófájlok futásidőben.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Hogyan tudom ellenőrizni Ha a szimulációs adatokat küld a felhőbe?

Az a [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) vagy a [Azure IoT CLI-bővítmény-események figyelése](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) paranccsal vizsgálhatja meg az egyes eszközök által az IoT hubnak küldött adatokat. Ezek az eszközök használatához, ismernie kell a központi telepítésben az IoT hub kapcsolati karakterláncára. Lásd: [hogyan állapíthatom meg a kapcsolati karakterláncot a megoldás által használt IoT hub?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Vizsgálja meg az egyik publisher eszköz által küldött adatokat:

* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Az IoT hubnak küldött adatok nem jelenik meg, majd van-e a szimuláció problémáját. Elemzés az első lépés a naplófájlokat a szimuláció összetevők elemezni kell. Lásd: [Hogyan olvashatom be a naplóadatok szimuláció összetevők?](#how-can-i-get-log-data-from-the-simulation-components) Ezt követően próbálja meg leállítása és a szimuláció elindításához, és ha még nem tartoznak adatok küldött, frissítse a szimuláció teljesen. Lásd: [hogyan frissíthetők a szimulációt, a virtuális gépen?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Hogyan engedélyezhetem interaktív térkép a csatlakoztatott gyár megoldás?

Ahhoz, hogy a csatlakoztatott gyár megoldás egy olyan interaktív térkép, egy meglévő Bing térképek API nagyvállalati csomagra kell rendelkeznie.

Amikor üzembe helyezése a [www.azureiotsolutions.com](http://www.azureiotsolutions.com), az üzembe helyezési folyamat ellenőrzi, hogy az előfizetése van egy engedélyezett Bing térképek API nagyvállalati csomagra, és interaktív térkép automatikusan telepíti a csatlakoztatott gyár be. Ha nem ez a helyzet, továbbra is engedélyezheti a interaktív térkép a központi telepítésben a következő:

Központi telepítése esetén használja a `build.ps1` parancsfájlt a csatlakoztatott gyári github a tárházat, és hogy ki rendelkezik Bing térképek API nagyvállalati csomagra, a környezeti változót `$env:MapApiQueryKey` a lekérdezési kulcsot a csomag a build-ablakban. Az interaktív térkép ezután automatikusan engedélyezve lesz.

Ha nem rendelkezik Bing térképek API nagyvállalati csomagra, üzembe helyezése a csatlakoztatott gyár megoldást [www.azureiotsolutions.com](http://www.azureiotsolutions.com) használatával vagy a `build.ps1` parancsfájlt. Majd adja hozzá a Bing térképek API nagyvállalati csomagra az előfizetés leírtak [Hogyan hozhatok létre vállalati fiók Bing térképek API?](#how-do-i-create-a-bing-maps-api-for-enterprise-account). Keressük meg ezt a fiókot a lekérdezési kulcs leírtak szerint [a Bing térképek API beszerzése a vállalati querykey értékét](#how-to-obtain-your-bing-maps-api-for-enterprise-querykey) és mentette a kulcsot. Keresse meg az Azure Portalon, és a csatlakoztatott gyár központi telepítésben az App Service erőforrás elérésére. Navigáljon a **Alkalmazásbeállítások**, hol található a szakasz **Alkalmazásbeállítások**. Állítsa be a **MapApiQueryKey** a beszerzett lekérdezési kulcsot. Mentse a beállításokat, majd lépjen **áttekintése** , és indítsa újra az App Service-ben.

### <a name="how-do-i-create-a-bing-maps-api-for-enterprise-account"></a>Hogyan hozhatok létre vállalati fiók Bing térképek API

Kap egy ingyenes *belső tranzakció szintjét 1 a Bing Maps Enterprise* tervet. Azonban csak adhat az alábbi díjcsomagok két Azure-előfizetéssel. Ha nem rendelkezik Bing térképek API vállalati fiók, hozzon létre egyet az Azure Portalon kattintva **+ erőforrás létrehozása**. Majd keresse meg az **vállalati Bing térképek API** , és kövesse az utasításokat követve hozza létre.

![A Bing-kulcs](./media/iot-accelerators-faq-cf/bing.png)

### <a name="how-to-obtain-your-bing-maps-api-for-enterprise-querykey"></a>A Bing térképek API vállalati querykey kulcsát szervizcsomagjának beszerzése

Miután létrehozta a Bing térképek API nagyvállalati csomagra, a Bing térképek vállalati erőforráshoz hozzá az erőforráscsoportot, a csatlakoztatott gyár megoldás az Azure Portalon.

1. Az Azure Portalon keresse meg az erőforráscsoportot, amely tartalmazza a Bing térképek API nagyvállalati csomagra.

1. Kattintson a **minden beállítás**, majd **kulcskezelés**.

1. Két kulcs létezi: **MasterKey** és **QueryKey**. Másolás a **QueryKey** értéket.

1. Dolgozza fel a kulcsot a `build.ps1` parancsfájl, a környezeti változót `$env:MapApiQueryKey` a PowerShell környezetében a **QueryKey** a csomaghoz. A felépítési szkriptjének ezután automatikusan hozzáadja az érték beállításait, az App Service-ben.

1. Futtasson helyi vagy felhőbeli üzembe helyezése a `build.ps1` parancsfájlt.

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Hogyan az interaktív térkép engedélyezése helyi hibakeresése során?

Az interaktív térkép engedélyezése a helyi hibakeresés során, a beállítás értékét állítsa `MapApiQueryKey` fájlok `local.user.config` és `<yourdeploymentname>.user.config` gyökere a központi telepítést az értékét a **QueryKey** másolt korábban.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Hogyan használhatom másik képet, az irányítópult kezdőlapján?

A statikus kép látható io módosításához az irányítópult kezdőlapján cserélje le a képet `WebApp\Content\img\world.jpg`. Majd újraépítése és ismételt üzembe helyezése a WebApp.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Hogyan használhatom nem OPC UA-eszközök a csatlakoztatott gyár?

A telemetriai adatokat küldeni a nem az OPC UA eszközök Okosgyár:

1. [A csatlakoztatott gyár topológia egy új állomás konfigurálása](iot-accelerators-connected-factory-configure.md) a a `ContosoTopologyDescription.json` fájlt.

1. Betöltési a telemetriai adatokat a csatlakoztatott gyár kompatibilis JSON formátumban:

    ```json
    [
      {
        "ApplicationUri": "<the_value_of_OpcUri_of_your_station",
        "DisplayName": "<name_of_the_datapoint>",
        "NodeId": "value_of_NodeId_of_your_datapoint_in_the_station",
        "Value": {
          "Value": <datapoint_value>,
          "SourceTimestamp": "<timestamp>"
        }
      }
    ]
    ```

1. Formátumát `<timestamp>` van: `2017-12-08T19:24:51.886753Z`

1. Indítsa újra a csatlakoztatott gyár App Service.

### <a name="next-steps"></a>További lépések

Megismerheti az IoT-megoldásgyorsítók egyéb szolgáltatásait és funkcióit is:

* [A prediktív karbantartási megoldásgyorsító áttekintése](iot-accelerators-predictive-overview.md)
* [Okosgyár-megoldásgyorsító üzembe helyezése](quickstart-connected-factory-deploy.md)
* [IoT-biztonság létrehozása az alapoktól](/azure/iot-fundamentals/iot-security-ground-up)
