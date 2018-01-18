---
title: "Gyári megoldás gyakori kérdések – Azure csatlakoztatott |} Microsoft Docs"
description: "Az IoT Suite csatlakoztatott factory gyakran ismételt kérdések"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: ab72152fc937e3c4552147fce29c95ea0efcadf4
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="frequently-asked-questions-for-iot-suite-connected-factory-preconfigured-solution"></a>Gyakori kérdések az IoT Suite csatlakoztatott beépített, előre konfigurált megoldás

Lásd még az általános [gyakran ismételt kérdések](iot-suite-faq.md) IoT Suite.

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solution"></a>Hol található az előkonfigurált megoldás forráskódját?

A forráskód a következő GitHub-tárházban tárolja:

* [Előre konfigurált csatlakoztatott gyári megoldás](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Mi az a OPC EE?

OPC egyesített architektúra révén, 2008, amely a szabványos platformfüggetlen, szolgáltatásorientált együttműködési. OPC EE különféle ipari rendszerek és egyéb eszközök például számítógépek iparági PLC vagy érzékelők használja. OPC EE OPC klasszikus specifikációit funkcióit integrálja a beépített biztonsági a egy bővíthető keretrendszer. Olyan szabvány, amelyek célja a OPC alapját. A [OPC Foundation](http://opcfoundation.org/) egy nem nonprofit szervezet több mint 440 tagjaival. A szervezet célja, hogy több szállító, többplatformos, biztonságos és megbízható együttműködést elősegítő OPC specifikációk használja:

* Infrastruktúra
* Specifikációk
* Technológia
* Folyamatok

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-preconfigured-solution"></a>Miért Microsoft adta meg OPC EE a csatlakoztatott gyári előre konfigurált megoldáshoz?

Microsoft OPC EE választotta, mert a nyitott, a nem tulajdonosi platform független, iparági ismeri fel és bevált szabványnak. Ez feltétele Industrie 4.0-s verzióját (RAMI4.0) referencia architektúra megoldások gyártási eljárások széles körét és berendezések együttműködésével biztosítása. Microsoft látja az ügyfelek igény Industrie 4.0 megoldások létrehozásához. OPC EE támogatása segítségével csökkenthető az ügyfelek számára a kitűzött célokat a korlát, és azonnali üzleti értéket ad a őket.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Hogyan egy nyilvános IP-cím hozzáadása a virtuális gép szimuláció?

Az IP-cím hozzáadása két lehetőség közül választhat:

* A PowerShell-parancsfájl `Simulation/Factory/Add-SimulationPublicIp.ps1` a a [tárház](https://github.com/Azure/azure-iot-connected-factory). A telepítés neve paraméterként adja át. Egy helyi központi telepítésének használata `<your username>ConnFactoryLocal`. A parancsfájl jelenít meg a virtuális gép IP-címét.

* Az Azure portálon keresse meg az erőforráscsoportot, a telepítés. A helyi központi telepítés, kivéve az erőforráscsoport rendelkezik, a megoldáshoz megadott vagy telepítés nevét. A build script használó helyi telepítés, az erőforráscsoport neve nem `<your username>ConnFactoryLocal`. Most adjon hozzá egy új **nyilvános IP-cím** erőforrás az erőforráscsoporthoz.

> [!NOTE]
> Mindkét esetben ellenőrizze a megjelenő utasításokat követve telepítse a legújabb javítások a [Ubuntu webhely](https://wiki.ubuntu.com/Security/Upgrades). A telepítés naprakészen tartása a mindaddig, amíg a virtuális Gépet egy nyilvános IP-cím keresztül érhető el.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Hogyan távolítsa el a szimuláció VM a nyilvános IP-címet?

Az IP-cím eltávolítása két lehetőség közül választhat:

* A PowerShell-parancsfájlt a Simulation/Factory/Remove-SimulationPublicIp.ps1 használja a [tárház](https://github.com/Azure/azure-iot-connected-factory). A telepítés neve paraméterként adja át. Egy helyi központi telepítésének használata `<your username>ConnFactoryLocal`. A parancsfájl jelenít meg a virtuális gép IP-címét.

* Az Azure portálon keresse meg az erőforráscsoportot, a telepítés. A helyi központi telepítés, kivéve az erőforráscsoport rendelkezik, a megoldáshoz megadott vagy telepítés nevét. A build script használó helyi telepítés, az erőforráscsoport neve nem `<your username>ConnFactoryLocal`. Most eltávolítja a **nyilvános IP-cím** az erőforráscsoportból erőforrás.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Hogyan be a szimuláció virtuális gép számára?

A szimuláció VM bejelentkezés csak támogatott, ha a megoldás a PowerShell-parancsfájl használatával telepített `build.ps1` a a [tárház](https://github.com/Azure/azure-iot-connected-factory).

Ha telepítette a www.azureiotsuite.com megoldást, nem tud bejelentkezni virtuális gép. Nem tud bejelentkezni, mert a jelszó véletlenszerűen történik, és nem állítható alaphelyzetbe.

1. A nyilvános IP-cím hozzáadása a virtuális Gépet. Lásd: [hogyan adni a nyilvános IP-címnek a szimuláció VM?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. A virtuális géphez az IP-címmel, a virtuális gép SSH-munkamenetet létrehozni.
1. A felhasználónév használata: `docker`.
1. A jelszó használata a központi telepítéséhez használt verziójától függ:
    * A megoldások a build.ps1 parancsfájl használatával előtt 2017. június 1. telepítve, a jelszó nem: `Passw0rd`.
    * A megoldások a build.ps1 parancsfájl használatával után 2017. június 1. telepítve, a jelszót a megtalálhatja a `<name of your deployment>.config.user` fájlt. A jelszó tárolódik a **VmAdminPassword** beállítást. A jelszó jön létre véletlenszerűen központi telepítéskor kivéve ha megadja azt a `build.ps1` parancsfájl-paraméter`-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Hogyan állítsa le és indítsa el az összes docker-folyamat a szimuláció VM?

1. Jelentkezzen be a szimuláció virtuális gép. Lásd: [hogyan be a szimuláció virtuális gép számára?](#how-do-i-sign-in-to-the-simulation-vm)
1. Ellenőrizze, hogy mely tárolók aktívak, futtassa: `docker ps`.
1. Futtassa az összes szimuláció tároló leállításához: `./stopsimulation`.
1. Az összes szimuláció tároló indítása:
    * Exportálja a nevű rendszerhéj változó **IOTHUB_CONNECTIONSTRING**. Az értéket a **IotHubOwnerConnectionString** beállítást azokban a `<name of your deployment>.config.user` fájlt. Példa:

        ```
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Futtassa az `./startsimulation` parancsot.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Hogyan frissíthetők a szimuláció, a virtuális gép?

A szimuláció végzett módosításokat, ha használható a PowerShell parancsfájl `build.ps1` a a [tárház](https://github.com/Azure/azure-iot-connected-factory) használatával a `updatedimulation` parancsot. Ezt a parancsfájlt hoz létre a szimuláció összetevők, leállítja a virtuális gép a szimuláció, feltölti, telepíti, és elindítja azokat.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Hogyan állapítható meg a kapcsolati karakterlánc az IoT-központnak a megoldás által használt?

Ha telepítette a megoldást a `build.ps1` a parancsfájl a [tárház](https://github.com/Azure/azure-iot-connected-factory), a kapcsolati karakterlánc értékét: **IotHubOwnerConnectionString** a a `<name of your deployment>.config.user` fájlt.

A kapcsolati karakterláncot az Azure portál használatával is tájékozódhat. Az IoT-központ erőforrás erőforráscsoporthoz tartozik, a telepítés keresse meg a kapcsolódási karakterlánc beállításainak.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Mely IoT Hub eszközöket használ a csatlakoztatott gyári szimuláció?

A szimuláció önkiszolgáló regisztrál, a következő eszközöket:

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

Használja a [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) vagy [az IoT-bővítmény az Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension) eszköz, ellenőrizheti, hogy mely eszközök vannak regisztrálva az IoT-központ, a megoldás használ. Eszköz-kezelővel, szüksége a kapcsolati karakterlánc az IoT-központ a környezetben. Az Azure CLI 2.0 az IoT-bővítmény használatához szüksége van az IoT Hub nevét.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Hogyan kaphatok naplóadatait a szimuláció összetevői?

A szimuláció lévő valamennyi összetevőnél információk jelentkezzen be a naplófájlokba. Ezek a fájlok megtalálhatók a virtuális Gépet a mappában `home/docker/Logs`. A naplók lekéréséhez használja a PowerShell parancsfájl `Simulation/Factory/Get-SimulationLogs.ps1` a a [tárház](https://github.com/Azure/azure-iot-connected-factory).

Ez a parancsfájl be kell jelentkeznie a virtuális géphez. Szükség lehet a a bejelentkezéshez adja meg a hitelesítő adatokat. Lásd: [hogyan be a virtuális gép szimuláció való?](#how-do-i-sign-in-to-the-simulation-vm) a hitelesítő adatok kereséséhez.

A parancsfájl hozzáadása/eltávolítása egy nyilvános IP-címet a virtuális gépre, ha az még nincs ilyen, és eltávolítja azt. A parancsfájl az összes napló fájlokat archiválhatja, és letölti a az archiválás a fejlesztő munkaállomás.

Másik megoldásként jelentkezzen be a virtuális gép SSH-kapcsolaton keresztül, és vizsgálja meg a naplófájlokat a futási időben.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Hogyan ellenőrizheti meg, hogy ha a szimuláció adatokat küld a felhőbe-e?

Az a [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) vagy a [IOT hubbal-explorer](https://github.com/azure/iothub-explorer) eszköz, vizsgálhatja az egyes eszközökről az IoT-központ küldött adatok. Ezek az eszközök használatához szüksége tudni, hogy a kapcsolati karakterláncot az IoT-központ a környezetben. Lásd: [hogyan állapíthatom meg a kapcsolati karakterlánc az IoT-központnak a megoldás által használt?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Vizsgálja meg a kiadó eszközök egyike által küldött adatokat:

* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Ha nem az IoT-központ küldött adatokat, majd nincs a szimuláció kapcsolatos problémát. Első lépésként elemzés elemezni kell a naplófájlok a szimuláció összetevőt. Lásd: [Hogyan juthatok naplóadatait a szimuláció összetevői?](#how-can-i-get-log-data-from-the-simulation-components) Ezt követően próbálja állítsa le és indítsa el a szimuláció, és még nincs adat küldése, ha frissíti a szimuláció teljesen. Lásd: [hogyan frissíthetők a szimuláció, a virtuális gép?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Hogyan engedélyezhető az interaktív térkép a csatlakoztatott gyári-megoldásban?

Ahhoz, hogy a csatlakoztatott gyári megoldásban interaktív térkép, rendelkeznie kell egy meglévő Bing térképek API vállalati terv.

A központi telepítésekor [www.azureiotsuite.com](http://www.azureiotsuite.com), a telepítési folyamat ellenőrzi, hogy az előfizetés egy engedélyezett Bing térképek API vállalati terv rendelkezik, és automatikusan telepíti az interaktív térkép csatlakoztatott gyári be. Ha nem ez a helyzet, továbbra is engedélyezheti egy interaktív leképezés a központi telepítésben az alábbiak szerint:

Központi telepítésekor használ a `build.ps1` parancsfájl csatlakoztatott gyári GitHub-tárházban, és a Bing térképek API vállalati terv rendelkezik, a környezeti változót `$env:MapApiQueryKey` a build ablakban, a lekérdezési kulcsot a csomag számára. Az interaktív térkép majd automatikusan engedélyezve lesz.

Ha nem rendelkezik a Bing térképek API vállalati terv, telepítheti a csatlakoztatott gyári megoldást [www.azureiotsuite.com](http://www.azureiotsuite.com) használatával vagy a `build.ps1` parancsfájl. Majd adja hozzá a Bing térképek API vállalati terv az előfizetés a [hogyan hozható létre a Bing térképek API vállalati fiók?](#how-do-i-create-a-bing-maps-api-for-enterprise-account). A fiókhoz tartozó lekérdezési kulcs kereshető [a Bing térképek API beszerzése a vállalati QueryKey](#how-to-obtain-your-bing-maps-api-for-enterprise-querykey) , és mentse ezt a kulcsot. Navigáljon az Azure portálra, és a csatlakoztatott gyári környezetben App Service erőforrás elérésére. Navigáljon a **Alkalmazásbeállítások**, hol található a szakasz **Alkalmazásbeállítások**. Állítsa be a **MapApiQueryKey** a lekérdezési kulcsot kapott. A beállítások mentéséhez, majd lépjen **áttekintése** , és indítsa újra az App Service.

### <a name="how-do-i-create-a-bing-maps-api-for-enterprise-account"></a>Hogyan hozható létre a Bing térképek API vállalati fiók

Kérhet egy ingyenes *belső tranzakciók szintjét 1 a Bing Maps vállalati* terv. Azonban csak hozzáadhat ezen sémák két Azure-előfizetéshez. Ha még nem rendelkezik vállalati fiók a Bing térképek API, hozzon létre egyet az Azure portálon kattintva **+ hozzon létre egy erőforrást**. Majd keresse meg a **Bing Maps API for Enterprise** és kövesse az utasításokat annak létrehozásához.

![Bing kulcs](media/iot-suite-faq-cf/bing.png)

### <a name="how-to-obtain-your-bing-maps-api-for-enterprise-querykey"></a>A Bing térképek API beszerzése a vállalati QueryKey

Miután létrehozta a Bing térképek API-t a vállalati terv, a Bing Maps vállalati erőforrás hozzáadása az Azure-portálon a csatlakoztatott gyári megoldás erőforráscsoportját.

1. Az Azure-portálon lépjen a Bing térképek API-t a vállalati terv tartalmazó erőforráscsoportot.

1. Kattintson a **összes beállítás**, majd **kulcskezelés**.

1. Két kulcs van: **főkulcsos** és **QueryKey**. Másolás a **QueryKey** érték.

1. A kulcsot észlelnie a `build.ps1` parancsfájl, a környezeti változót `$env:MapApiQueryKey` a PowerShell-környezetében a **QueryKey** a csomag. A build script majd automatikusan értéket adja meg az App Service beállításait.

1. Futtassa a helyi vagy felhőalapú használó telepítés a `build.ps1` parancsfájl.

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Hogyan engedélyezhető az interaktív térkép helyileg hibakeresés során?

Az interaktív térkép közben helyileg hibakeresést engedélyezéséhez állítsa be a beállítás értékének `MapApiQueryKey` fájlok `local.user.config` és `<yourdeploymentname>.user.config` gyökérkönyvtárában található a központi telepítés értékének a **QueryKey** másolt korábban.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Hogyan használható egy másik lemezképet, az irányítópult kezdőlapján?

Módosítsa a statikus kép io látható az irányítópult kezdőlapján cserélje le a lemezképet `WebApp\Content\img\world.jpg`. Majd újraépítése, és telepítse újra a webalkalmazást.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Miként használható az eszközök nem OPC EE csatlakoztatott Factory?

A telemetriai adatokat küldeni a nem OPC EE eszközök csatlakoztatott gyári:

1. [Egy új állomás konfigurálása a csatlakoztatott gyári topológia](iot-suite-connected-factory-configure.md) a a `ContosoTopologyDescription.json` fájlt.

1. A telemetriai adatok csatlakoztatott gyári kompatibilis JSON formátumban betöltési:

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

1. Formátuma `<timestamp>` van:`2017-12-08T19:24:51.886753Z`

1. Indítsa újra a csatlakoztatott gyári App Service.

### <a name="next-steps"></a>További lépések

Megismerheti az IoT Suite előre konfigurált megoldásának egyéb szolgáltatásait és funkcióit is:

* [Előre konfigurált prediktív karbantartási megoldás áttekintése](iot-suite-predictive-overview.md)
* [Előre konfigurált csatlakoztatott gyári megoldási áttekintés](iot-suite-connected-factory-overview.md)
* [Az IoT biztonsági másolatot az alapoktól](securing-iot-ground-up.md)