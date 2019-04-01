---
title: Az Azure IoT OPC UA tanúsítvány teljesen új modul üzembe helyezése |} A Microsoft Docs
description: Hogyan helyezheti üzembe az OPC-tároló sablon nélkül.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: a3a9d21b70f16482f05d27aa0df8d8865459aeb4
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759507"
---
# <a name="deploy-opc-vault-from-scratch"></a>Az OPC-tároló üzembe helyezése a előzmények

Az Azure IoT OPC UA tanúsítványkezelés, is tudja, és az OPC-tárolónak, mikroszolgáltatások, amelyek konfigurálni, regisztrálása, és az OPC UA-kiszolgáló és ügyfél alkalmazásokat a felhőben a tanúsítvány életciklusának kezelése. Ez a cikk bemutatja, hogyan telepítheti az OPC-tároló teljesen új.

## <a name="configuration-and-environment-variables"></a>Konfigurációs és környezeti változók

A szolgáltatás konfigurációját az ASP.NET Core konfigurációs adapterrel, amelyek segítségével appsettings.ini tárolja. Az INI-formátum lehetővé teszi, hogy olvasható formátumban, megjegyzésekkel tárolására.
A kérelem beszúrása környezeti változókat, például a hitelesítő adatok és a hálózati részleteket is támogatja. (Ez a szakasz eredetileg címe teendőlista-kezelő konfigurációs és környezeti változók).

A konfigurációs fájlt a tárház néhány környezeti változókat, amelyek legalább egyszer létrehozni hivatkozik. Az operációs rendszer és az IDE, függően többféleképpen is kezelheti a környezeti változókat:

- A Windows számára az env-változók-Setup.cmd fájlt parancsfájl kell készíteni, és csak egyszer végre. Végrehajtásakor a beállítások megmaradnak, terminál-munkamenetet, és az újraindítások.

- Linux- és os x-környezetek esetében az env-változók-beállítási szkript kell hajtható végre, minden alkalommal, amikor egy új konzol megnyitásakor. Az operációs rendszer és a Terminálszolgáltatások függően többféleképpen értékek globálisan, megőrizni ezeket az oldalakat webhelyünkre további információk:

  - https://stackoverflow.com/questions/13046624/how-to-permanently-export-a-variable-in-linux
  
  - https://stackoverflow.com/questions/135688/setting-environment-variables-in-os-x
  
  - https://help.ubuntu.com/community/EnvironmentVariables
  

- Visual Studio: A környezeti változók is beállítható a Visual Studióban a Project tulajdonságainál, a bal oldali panelen válassza a "Konfigurációs tulajdonságok" és "Környezet", ahol hozzáadhat több változó szakaszra.

- IntelliJ Rider: Minden egyes konfiguráció futtatása, hasonlóan az IntelliJ IDEA is megadhatók környezeti változók https://www.jetbrains.com/help/idea/run-debug-configuration-application.html

## <a name="run-and-debug-with-visual-studio"></a>Futtatását és hibakeresését a Visual Studióval

A Visual Studio gyorsan nyissa meg az alkalmazás használata a parancssort. Ehhez semmit az IDE-en kívül a konfigurálás nélkül nélkül teszi lehetővé.

A lépések a Visual Studio 2017 használatával:

1. Nyissa meg a megoldás használatával a `iot-opc-gds-service.sln` fájlt.

1. A megoldás betöltésekor, kattintson a jobb gombbal a `WebService` projektre, válassza ki, és nyissa meg a `Debug` szakaszban.

1. Ugyanabban a szegmensben adja meg a szükséges környezeti változókat.

1. Nyomja meg **F5**, vagy a **futtatása** ikonra. A Visual Studio kell megnyitnia a böngészőben, a szolgáltatás állapotát megjelenítő JSON formátumban.

## <a name="run-and-debug-with-intellij-rider"></a>Futtatás és hibakeresés az IntelliJ-vezető

1. Nyissa meg a megoldás használatával a `iot-opc-gds-service.sln` fájlt.

1. A megoldás betöltésekor Ugrás `Run > Edit Configurations` , és hozzon létre egy új `.NET Project` konfigurációja.

1. A konfigurációban válassza ki a webszolgáltatás-projektet.

1. A beállítások mentéséhez, és futtassa a konfigurációt, amely az IDE-eszköztár jön létre.

1. Megjelenik a szolgáltatás üzenetek IntelliJ Futtatás ablakba a részleteket, például az URL-címet a szolgáltatás emellett a webszolgáltatást futtató bootstrap naplókat.

## <a name="build-and-run-from-the-command-line"></a>Létrehozhatja és futtathatja a parancssorból

A parancsfájlok mappa tartalmaz néhány a gyakori feladatok parancsprogramokat:

- `build`: Fordítsa le az összes projektet, és a tesztek futtatásához.

- `compile`: Fordítsa le az összes projektet.

- `run`: Fordítsa le a projektek, és futtassa a szolgáltatást, amely kérni fogja a webszolgáltatások futtatásához emelt szintű jogosultságokkal a Windows.

A szkriptek keresése a környezeti változók beállítása. Globális beállítása a környezeti változókat az operációs rendszer, vagy "env-változók-telepítés" a parancsprogramot használja, a parancsfájlok mappában.

### <a name="sandbox"></a>Védőfal

A parancsfájlok azt feltételezik, hogy konfigurálta a fejlesztési környezetet a .NET Core és a Docker. Kerülje a .NET Core, telepítése, és csak a Docker telepítheti és parancssori paraméter `--in-sandbox` (vagy a rövid űrlapot `-s`), például:

- `build --in-sandbox`: Végrehajtja az összeállítási feladat belül egy Docker-tároló (krátká forma `build -s`).

- `compile --in-sandbox`: Docker-tárolókon belül a fordítási feladatot hajt végre (krátká forma `compile -s`).

- `run --in-sandbox`: Elindítja a szolgáltatást egy Docker-tároló belül (krátká forma `run -s`).

A védőfal használt Docker-rendszerképek a Docker hubon tárolt [Itt](https://hub.docker.com/r/azureiotpcs/code-builder-dotnet).

## <a name="package-the-application-to-a-docker-image"></a>Az alkalmazást egy Docker-rendszerkép becsomagolása

A `scripts` mappa tartalmazza a docker almappa azokkal a fájlokkal kell csomagolni a szolgáltatás egy Docker-rendszerképet:

- `Dockerfile`: Docker-rendszerképek előírásoknak.
- `build`: Hozhat létre egy Docker-tárolót, és tárolja a lemezképet a helyi beállításjegyzékben.
- `run`: Futtassa a Docker-tároló az a helyi beállításjegyzékben tárolt rendszerkép.
- `content`: Egy mappa a lemezképpel, beleértve a belépési pont szkriptje másolt fájlok.

## <a name="azure-iot-hub-setup"></a>Az Azure IoT Hub beállítása

A mikroszolgáltatások használatához állítsa be az Azure IoT Hub tesztek fejlesztése és integrációja.

A projekt tartalmaz néhány Bash-szkriptek, ezzel a telepítőprogrammal segítséget:

- Új IoT Hub létrehozása: `./scripts/iothub/create-hub.sh`

- Meglévő hubs listázza: `./scripts/iothub/list-hubs.sh`

- Az IoT Hub (például kulcsok) részletek megjelenítése: `./scripts/iothub/show-hub.sh`

És abban az esetben, ha több Azure-előfizetéssel kellett:

- Az előfizetések listájának megjelenítése: `./scripts/iothub/list-subscriptions.sh`

- Módosítsa az aktuális előfizetésben: `./scripts/iothub/select-subscription.sh`

## <a name="development-setup"></a>Fejlesztés a telepítő

### <a name="net-setup"></a>.NET-telepítő

A projekt munkafolyamat-n keresztül kezelt [.NET Core](https://dotnet.github.io) 1.x, így az összes parancsfájl futtatását, és győződjön meg arról, hogy a ide-je a várt módon működik-e a környezetben, telepítenie kell.

Is biztosítunk egy [Java-verzió](https://github.com/Azure/iot-opc-gds-service-dotnet) a projekthez, és a többi Azure IoT számítógépek összetevő.

### <a name="ide"></a>IDE

Íme néhány az Ide-k segítségével az Azure IoT számítógépeken működik:

- [Visual Studio](https://www.visualstudio.com)
- [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac)
- [IntelliJ Rider](https://www.jetbrains.com/rider)
- [Visual Studio Code](https://code.visualstudio.com)

### <a name="git-setup"></a>Git-telepítés

A projekt tartalmazza a Git hook automatizálása néhány ellenőrzést kódváltoztatást elfogadása előtt. A tesztek futtatása manuálisan, vagy lehetővé teszik a CI-platform a tesztek futtatásához. A következő Git-hook automatikusan GitHub kódmódosítás küldése előtt az összes vizsgálat futtatása, és felgyorsíthatja a fejlesztési munkafolyamat használjuk.

Ha bármikor el kívánja távolítani a hook, egyszerűen törölje a fájlt, a telepített `.git/hooks`. Emellett kikerülheti az üzem előtti véglegesítési hook használatával a `--no-verify` lehetőséget.

#### <a name="pre-commit-hook-with-sandbox"></a>A védőfal előtti véglegesítési hook

Állítsa be a csomagban foglalt hurkok, nyisson meg egy Windows/Linux/MacOS-konzolt, és hajtsa végre:

```
cd PROJECT-FOLDER
cd scripts/git
setup --with-sandbox
```

Ezzel a konfigurációval, a fájlok ellenőrzése során a Git ellenőrzi, hogy az alkalmazás minden tesztjén a, a build és a egy Docker-tárolót minden fejlesztési követelmények konfigurált belül a tesztek futtatásához.

#### <a name="pre-commit-hook-without-sandbox"></a>Üzem előtti véglegesítési hook védőfal nélkül

> [!NOTE] 
> A védőfal nélkül hook igényel [.NET Core](https://dotnet.github.io) a rendszer elérési ÚTJA.

Állítsa be a csomagban foglalt hurkok, nyisson meg egy Windows/Linux/MacOS-konzolt, és hajtsa végre:

```
cd PROJECT-FOLDER
cd scripts/git
setup --no-sandbox
```
Ezzel a konfigurációval, a fájlok ellenőrzése során a Git ellenőrzi, hogy az alkalmazás minden tesztjén a, a build és a tesztek futtatása a munkaállomáson, az operációs rendszer telepítve eszközök használatával.

A projekt kód stílus útmutatást:

- Ha elfogadható, sorok hossza korlátozódik kódvizsgálat és parancssori szerkesztők legfeljebb 80 karakter.

- Négy szóközt tartalmazó kód blokkok behúzás A lap karaktere el kell kerülni.

- Szövegfájlok használata Unix végén sor formátuma (Soremelés).

- Függőségi beszúrást felügyeletét [Autofac](https://autofac.org).

- Web service API-k mezők, amelyek CamelCased metaadatok kivételével.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az OPC-tároló üzembe helyezése a teljesen új, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Az OPC-Twin üzembe előzmények](howto-opc-twin-deploy-modules.md)