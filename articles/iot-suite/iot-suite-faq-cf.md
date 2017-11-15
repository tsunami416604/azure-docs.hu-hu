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
ms.date: 11/10/2017
ms.author: corywink
ms.openlocfilehash: d4cb452b34ddefc70dc1adcff0e5fead072aa16a
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2017
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

Microsoft OPC EE választotta, mert a nyitott, a nem tulajdonosi platform független, iparági ismeri fel és bevált szabványnak. Ez feltétele Industrie 4.0-s verzióját (RAMI4.0) referencia architektúra megoldások gyártási eljárások széles körét és berendezések együttműködésével biztosítása. Microsoft igény szerinti, ügyfeleink Industrie 4.0 megoldások látja. OPC EE támogatása segítségével csökkenthető az ügyfelek számára a kitűzött célokat a korlát, és azonnali üzleti értéket ad a őket.

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

* proxy.Beijing.Corp.contoso
* proxy.capetown.Corp.contoso
* proxy.Mumbai.Corp.contoso
* proxy.munich0.Corp.contoso
* proxy.Rio.Corp.contoso
* proxy.Seattle.Corp.contoso
* Publisher.Beijing.Corp.contoso
* Publisher.capetown.Corp.contoso
* Publisher.Mumbai.Corp.contoso
* Publisher.munich0.Corp.contoso
* Publisher.Rio.Corp.contoso
* Publisher.Seattle.Corp.contoso

Használja a [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) vagy [IOT hubbal-explorer](https://github.com/azure/iothub-explorer) eszköz, ellenőrizheti, hogy mely eszközök vannak regisztrálva az IoT-központ, a megoldás használ. Ezek az eszközök használatához szüksége a kapcsolati karakterlánc az IoT-központ a környezetben.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Hogyan kaphatok naplóadatait a szimuláció összetevői?

A szimuláció lévő valamennyi összetevőnél információk jelentkezzen be a naplófájlokba. Ezek a fájlok megtalálhatók a virtuális Gépet a mappában `home/docker/Logs`. A naplók lekéréséhez használja a PowerShell parancsfájl `Simulation/Factory/Get-SimulationLogs.ps1` a a [tárház](https://github.com/Azure/azure-iot-connected-factory).

Ez a parancsfájl be kell jelentkeznie a virtuális géphez. Szükség lehet a a bejelentkezéshez adja meg a hitelesítő adatokat. Lásd: [hogyan be a virtuális gép szimuláció való?](#how-do-i-sign-in-to-the-simulation-vm) a hitelesítő adatok kereséséhez.

A parancsfájl hozzáadása/eltávolítása egy nyilvános IP-címet a virtuális gépre, ha az még nincs ilyen, és eltávolítja azt. A parancsfájl az összes napló fájlokat archiválhatja, és letölti a az archiválás a fejlesztő munkaállomás.

Másik megoldásként jelentkezzen be a virtuális gép SSH-kapcsolaton keresztül, és vizsgálja meg a naplófájlokat a futási időben.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Hogyan ellenőrizheti meg, hogy ha a szimuláció adatokat küld a felhőbe-e?

Az a [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) vagy a [IOT hubbal-explorer](https://github.com/azure/iothub-explorer) eszköz, vizsgálhatja az egyes eszközökről az IoT-központ küldött adatok. Ezek az eszközök használatához szüksége tudni, hogy a kapcsolati karakterláncot az IoT-központ a környezetben. Lásd: [hogyan állapíthatom meg a kapcsolati karakterlánc az IoT-központnak a megoldás által használt?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Vizsgálja meg a kiadó eszközök egyike által küldött adatokat:

* Publisher.Beijing.Corp.contoso
* Publisher.capetown.Corp.contoso
* Publisher.Mumbai.Corp.contoso
* Publisher.munich0.Corp.contoso
* Publisher.Rio.Corp.contoso
* Publisher.Seattle.Corp.contoso

Ha nem az IoT-központ küldött adatokat, majd nincs a szimuláció kapcsolatos problémát. Első lépésként elemzés elemezni kell a naplófájlok a szimuláció összetevőt. Lásd: [Hogyan juthatok naplóadatait a szimuláció összetevői?](#how-can-i-get-log-data-from-the-simulation-components) Ezt követően próbálja állítsa le és indítsa el a szimuláció, és még nincs adat küldése, ha frissíti a szimuláció teljesen. Lásd: [hogyan frissíthetők a szimuláció, a virtuális gép?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="next-steps"></a>Következő lépések

Megismerheti az IoT Suite előre konfigurált megoldásának egyéb szolgáltatásait és funkcióit is:

* [Előre konfigurált prediktív karbantartási megoldás áttekintése](iot-suite-predictive-overview.md)
* [Előre konfigurált csatlakoztatott gyári megoldási áttekintés](iot-suite-connected-factory-overview.md)
* [Az IoT biztonsági másolatot az alapoktól](securing-iot-ground-up.md)