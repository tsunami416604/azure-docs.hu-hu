---
title: Csatlakoztatott gyári megoldás – Gyakori kérdések – Azure | Microsoft dokumentumok
description: Ez a cikk a Connected Factory megoldásgyorsítóval kapcsolatos gyakori kérdésekre ad választ. A GitHub-tárházra mutató hivatkozásokat tartalmaz.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: f2f8c08176f80436a339924adb4b2a09338a548a
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313588"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Gyakori kérdések a Csatlakoztatott gyári megoldásgyorsítóval kapcsolatban

Lásd még az IoT-megoldásgyorsítók általános [gyakori kérdéseket.](iot-accelerators-faq.md)

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Hol találom a megoldásgyorsító forráskódját?

A forráskód a következő GitHub-tárházban tárolódik:

* [Csatlakoztatott gyári megoldásgyorsító](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Mi az OPC UA?

A 2008-ban kiadott OPC Unified Architecture (UA) egy platformfüggetlen, szolgáltatásorientált interoperabilitási szabvány. Az OPC UA-t különböző ipari rendszerek és eszközök, például ipari számítógépek, PLC-k és érzékelők használják. Az OPC UA az OPC Classic specifikációk funkcionalitását egyetlen bővíthető keretrendszerbe integrálja, beépített biztonsággal. Ez egy olyan szabvány, amelyet az OPC Alapítvány vezet. Az [OPC Alapítvány](https://opcfoundation.org/) egy non-profit szervezet, több mint 440 tagja van. A szervezet célja, hogy opc specifikációk használatával megkönnyítse a többgyártós, többplatformos, biztonságos és megbízható interoperabilitást az:

* Infrastruktúra
* Specifikációk
* Technológia
* Folyamatok

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Miért választotta a Microsoft az OPC UA-t a Connected Factory megoldásgyorsítóhoz?

A Microsoft azért választotta az OPC UA-t, mert nyílt, nem védett, platformfüggetlen, iparágáltal elismert és bevált szabvány. Ez az Industrie 4.0 (RAMI4.0) referenciaarchitektúra-megoldások követelménye, amely biztosítja a gyártási folyamatok és berendezések széles körének interoperabilitását. A Microsoft úgy látja, hogy az ügyfelek keresletet látnak az Industrie 4.0-s megoldások létrehozásához. Az OPC UA támogatása segít csökkenteni az ügyfelek számára a céljaik elérésének akadályát, és azonnali üzleti értéket biztosít számukra.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Hogyan adhatok hozzá nyilvános IP-címet a szimulációs virtuális géphez?

Az IP-cím hozzáadásához két lehetőség van:

* Használja a PowerShell-parancsfájlt `Simulation/Factory/Add-SimulationPublicIp.ps1` a [tárházban.](https://github.com/Azure/azure-iot-connected-factory) Adja meg a központi telepítés nevét paraméterként. Helyi telepítéshez használja `<your username>ConnFactoryLocal`a használatát. A parancsfájl kinyomtatja a virtuális gép IP-címét.

* Az Azure Portalon keresse meg a központi telepítés erőforráscsoportját. A helyi központi telepítés kivételével az erőforráscsoport a megoldásként vagy központi telepítés ként megadott névvel rendelkezik. A buildparancsfájlt használó helyi központi telepítés esetén `<your username>ConnFactoryLocal`az erőforráscsoport neve . Most adjon hozzá egy új **nyilvános IP-cím** erőforrást az erőforráscsoporthoz.

> [!NOTE]
> Mindkét esetben győződjön meg róla, hogy telepíti a legújabb javításokat az Ubuntu honlapján található utasítások [követésével.](https://wiki.ubuntu.com/Security/Upgrades) Tartsa naprakészen a telepítést mindaddig, amíg a virtuális gép nyilvános IP-címen keresztül érhető el.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Hogyan távolíthatom el a nyilvános IP-címet a szimulációs virtuális géphez?

Az IP-cím eltávolításához két lehetősége van:

* Használja a [tárház](https://github.com/Azure/azure-iot-connected-factory)Simulation/Factory/Remove-SimulationPublicIp.ps1 PowerShell-parancsfájlt. Adja meg a központi telepítés nevét paraméterként. Helyi telepítéshez használja `<your username>ConnFactoryLocal`a használatát. A parancsfájl kinyomtatja a virtuális gép IP-címét.

* Az Azure Portalon keresse meg a központi telepítés erőforráscsoportját. A helyi központi telepítés kivételével az erőforráscsoport a megoldásként vagy központi telepítés ként megadott névvel rendelkezik. A buildparancsfájlt használó helyi központi telepítés esetén `<your username>ConnFactoryLocal`az erőforráscsoport neve . Most távolítsa el a **nyilvános IP-cím** erőforrást az erőforráscsoportból.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Hogyan tudok bejelentkezni a szimulációs virtuális gépbe?

A szimulációs virtuális gépbe való bejelentkezés csak akkor támogatott, ha `build.ps1` a megoldást a PowerShell-parancsfájl használatával telepítette a [tárházban.](https://github.com/Azure/azure-iot-connected-factory)

Ha üzembe helyezte a megoldást www.azureiotsolutions.com, nem tud bejelentkezni a virtuális gép. Nem tud bejelentkezni, mert a jelszó véletlenszerűen jön létre, és nem állíthatja alaphelyzetbe.

1. Adjon hozzá egy nyilvános IP-címet a virtuális géphez. Lásd: [Hogyan adhatok hozzá nyilvános IP-címet a szimulációs virtuális géphez?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Hozzon létre egy SSH-munkamenetet a virtuális gép hez a virtuális gép IP-címe használatával.
1. A használandó felhasználónév: `docker`.
1. A használandó jelszó a központi telepítéshez használt verziótól függ:
    * a build.ps1 parancsfájlhasználatával `Passw0rd`2017.
    * 2017. június 1-je után a build.ps1 parancsfájl használatával telepített `<name of your deployment>.config.user` megoldások esetében a jelszó megtalálható a fájlban. A jelszó a **VmAdminPassword** beállításban tárolódik. A jelszó véletlenszerűen jön létre a telepítés kor, kivéve, ha a `build.ps1` parancsfájl paraméter használatával adja meg`-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Hogyan állíthatom le és indíthatom el az összes docker-folyamatot a szimulációs virtuális gépben?

1. Jelentkezzen be a szimulációs virtuális gépbe. Lásd: [Hogyan jelentkezhetek be a szimulációs virtuális gépbe?](#how-do-i-sign-in-to-the-simulation-vm)
1. Annak ellenőrzéséhez, hogy mely `docker ps`tárolók aktívak, futtassa a következőket: .
1. Az összes szimulációs tároló `./stopsimulation`leállításához futtassa a következőket: .
1. Az összes szimulációs tároló indítása:
    * Exportálja a **IOTHUB_CONNECTIONSTRING**nevű rendszerhéj-változót. Használja az **IotHubOwnerConnectionString** beállítás értékét `<name of your deployment>.config.user` a fájlban. Például:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Futtassa az `./startsimulation` parancsot.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Hogyan frissíthetem a szimulációt a virtuális gépben?

Ha bármilyen módosítást végzett a szimuláción, használhatja `build.ps1` a PowerShell-parancsfájlt a [tárházban](https://github.com/Azure/azure-iot-connected-factory) a `updatedimulation` parancs használatával. Ez a parancsfájl létrehozza az összes szimulációs összetevőt, leállítja a szimulációt a virtuális gépben, feltölti, telepíti és elindítja azokat.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Hogyan találhatom meg a megoldásom által használt IoT hub kapcsolati karakterláncát?

Ha a megoldást a `build.ps1` [tárolóban](https://github.com/Azure/azure-iot-connected-factory)lévő parancsfájllal telepítette, a kapcsolati karakterlánc az `<name of your deployment>.config.user` **IotHubOwnerConnectionString** értéke a fájlban.

A kapcsolati karakterlánc az Azure Portalhasználatával is megkeresheti. Az IoT Hub-erőforrás a központi telepítés erőforráscsoportjában keresse meg a kapcsolati karakterlánc beállításait.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Mely IoT Hub-eszközöket használja a Csatlakoztatott gyár szimulációja?

A szimuláció sikamaa a következő eszközöket regisztrálja:

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

A [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) vagy [az Azure CLI-eszköz IoT-bővítmény](https://github.com/Azure/azure-iot-cli-extension) használatával ellenőrizheti, hogy mely eszközök vannak regisztrálva a megoldás által használt IoT-központban. Az eszközkezelő használatához az IOt hub kapcsolati karakterláncára van szükség a központi telepítésben. Az Azure CLI IoT-bővítményének használatához szüksége van az IoT Hub nevére.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Hogyan szerezhetek be naplóadatokat a szimulációs összetevőkből?

A szimuláció összes összetevője naplóba a naplófájlokba. Ezek a fájlok a mappában `home/docker/Logs`található virtuális gépben találhatók. A naplók beolvasásához használhatja a `Simulation/Factory/Get-SimulationLogs.ps1` PowerShell-parancsfájlt a [tárházban.](https://github.com/Azure/azure-iot-connected-factory)

Ezt a parancsfájlt be kell jelentkeznie a virtuális gépbe. Előfordulhat, hogy meg kell adnia a bejelentkezéshez szükséges hitelesítő adatokat. Lásd: [Hogyan jelentkezhetek be a szimulációs virtuális gépbe a](#how-do-i-sign-in-to-the-simulation-vm) hitelesítő adatok megkereséséhez.

A parancsfájl hozzáad/eltávolít egy nyilvános IP-címet a virtuális géphez, ha még nem rendelkezik ilyen, és eltávolítja azt. A parancsfájl az összes naplófájlt egy archívumba helyezi, és letölti az archívumot a fejlesztési munkaállomásra.

Másik lehetőségként jelentkezzen be a virtuális gépre SSH-n keresztül, és ellenőrizze a naplófájlokat futásidőben.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Hogyan ellenőrizhetem, hogy a szimuláció adatokat küld-e a felhőbe?

A [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) vagy az [Azure IoT CLI-bővítmény monitor-események](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub?view=azure-cli-latest#ext-azure-iot-az-iot-hub-monitor-events) parancs, megvizsgálhatja az adatokat küldött Az IoT Hub bizonyos eszközökről. Ezeknek az eszközöknek a használatához ismernie kell az IoT hub kapcsolati karakterláncát a központi telepítésben. Lásd: [Hogyan találhatom meg a megoldásom által használt IoT hub kapcsolati karakterláncát?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Vizsgálja meg az egyik közzétevő eszköz által küldött adatokat:

* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Ha nem lát adatokat az IoT Hubra, akkor probléma van a szimulációval. Első elemzési lépésként elemezze a szimulációs összetevők naplófájljait. Lásd: [Hogyan kaphatok naplóadatokat a szimulációs összetevőkből?](#how-can-i-get-log-data-from-the-simulation-components) Ezután próbálja meg leállítani és elindítani a szimulációt, és ha még mindig nincs elküldött adat, frissítse a szimulációt teljesen. Lásd: [Hogyan frissíthetem a szimulációt a virtuális gépben?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Hogyan engedélyezhetek interaktív térképet a Connected Factory-megoldásban?

Ahhoz, hogy engedélyezze az interaktív térképet a Csatlakoztatott gyári megoldásban, rendelkeznie kell egy Azure Maps-fiókkal.

[a www.azureiotsolutions.com](https://www.azureiotsolutions.com)üzembe helyezésekor a telepítési folyamat egy Azure Maps-fiókot ad hozzá a megoldásgyorsító szolgáltatásokat tartalmazó erőforráscsoporthoz.

Amikor a parancsfájl `build.ps1` használatával telepíti a Connected Factory GitHub tárházban állítsa be a környezeti változót `$env:MapApiQueryKey` a buildablakban az Azure [Maps-fiók kulcsára.](../azure-maps/how-to-manage-account-keys.md) Az interaktív térkép ezután automatikusan engedélyezve van.

Az Üzembe helyezés után hozzáadhat egy Azure Maps-fiókkulcsot is a megoldásgyorsítóhoz. Keresse meg az Azure Portalon, és az App Service-erőforrás a csatlakoztatott gyári üzembe helyezés. Nyissa meg az **Alkalmazás beállításai lehetőséget,** ahol megtalálja az Alkalmazás beállításai című **szakaszt.** Állítsa be a **MapApiQueryKey-t** az [Azure Maps-fiók kulcsára.](../azure-maps/how-to-manage-account-keys.md) Mentse a beállításokat, majd keresse meg **az Áttekintés lapot,** majd indítsa újra az App Service-t.

### <a name="how-do-i-create-an-azure-maps-account"></a>Hogyan hozhatok létre Azure Maps-fiókot?

Lásd: [Az Azure Maps-fiók és kulcsok kezelése.](../azure-maps/how-to-manage-account-keys.md)

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Az Azure Maps-fiókkulcs beszerzése

Lásd: [Az Azure Maps-fiók és kulcsok kezelése.](../azure-maps/how-to-manage-account-keys.md)

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Hogyan engedélyezheti az interaktív térképet helyi hibakeresés közben?

Ha helyi hibakeresés közben engedélyezni szeretné az interaktív térképet, `MapApiQueryKey` állítsa `local.user.config` a `<yourdeploymentname>.user.config` beállítás értékét a fájlokban és a központi telepítés gyökerében a korábban másolt **QueryKey** értékre.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Hogyan használhatok másik képet az irányítópult kezdőlapján?

Ha módosítani szeretné a statikus képet jelenik io `WebApp\Content\img\world.jpg`a honlapon az irányítópult, cserélje ki a képet . Ezután építse újra és telepítse újra a WebAppot.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Hogyan használhatom a nem OPC UA-eszközöket a Connected Factory segítségével?

Telemetriai adatok küldése nem OPC UA-eszközökről a Csatlakoztatott gyárba:

1. [Állítson be egy új állomást a](iot-accelerators-connected-factory-configure.md) `ContosoTopologyDescription.json` fájl Csatlakoztatott gyári topológiájában.

1. A telemetriai adatok betöltése connected factory kompatibilis JSON formátumban:

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

1. A formátum `<timestamp>` a következő:`2017-12-08T19:24:51.886753Z`

1. Indítsa újra a Csatlakoztatott gyári alkalmazásszolgáltatást.

### <a name="next-steps"></a>További lépések

Megismerheti az IoT-megoldásgyorsítók egyéb szolgáltatásait és funkcióit is:

* [A prediktív karbantartási megoldásgyorsító áttekintése](iot-accelerators-predictive-overview.md)
* [Csatlakoztatott gyári megoldásgyorsító telepítése](quickstart-connected-factory-deploy.md)
* [IoT-biztonság létrehozása az alapoktól](/azure/iot-fundamentals/iot-security-ground-up)
