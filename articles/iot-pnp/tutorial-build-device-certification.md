---
title: IoT-Plug and Play előnézeti eszköz létrehozása, amely készen áll a minősítésre | Microsoft Docs
description: Az eszköz fejlesztőinek megtudhatja, hogyan hozhat létre olyan IoT-Plug and Play előnézeti eszközt, amely készen áll a minősítésre.
author: tbhagwat3
ms.author: tanmayb
ms.date: 06/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 151f6609375063a68a848f4c799af0b459124f23
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70047907"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>IoT Plug and Play előnézeti eszköz létrehozása, amely készen áll a minősítésre

Ebből az oktatóanyagból megtudhatja, hogyan fejlesztheti a IoT Plug and Play előnézeti eszközt, amely készen áll a minősítésre.

A minősítési tesztek a következőket ellenőrzi:

- A IoT Plug and Play eszköz kódja telepítve van az eszközön.
- A IoT Plug and Play eszköz kódját az Azure IoT SDK-val készítettük.
- Az eszköz kódja támogatja az [Azure-IoT hub Device Provisioning Service](../iot-dps/about-iot-dps.md).
- Az eszköz kódja megvalósítja az eszköz információs felületét.
- A képesség modell és az eszköz kódja IoT Centralekkel működik.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- [Visual Studio Code](https://code.visualstudio.com/download)
- [Azure IoT Workbench-bővítmény a VS Code-hoz](https://github.com/Azure/Azure-IoT-PnP-Preview/blob/master/VSCode/README.md#installation)

A rövid útmutatóban létrehozott [IoT Plug and Play eszközre is szüksége lesz: Eszköz-képesség modell használatával hozhat létre eszközöket](quickstart-create-pnp-device.md).

## <a name="store-a-capability-model-and-interfaces"></a>Képesség-modell és felületek tárolása

A IoT Plug and Play eszközökön olyan képességi modellt és interfészeket kell létrehoznia, amelyek az eszköznek a JSON-fájlként való használatát határozzák meg.

Ezeket a JSON-fájlokat három különböző helyen tárolhatja:

- A nyilvános modell tárháza.
- A vállalati modell tárháza.
- Az eszközön.

Jelenleg az eszköz hitelesítéséhez a fájlokat a vállalati modell adattárában vagy a nyilvános modell adattárában kell tárolni.

## <a name="include-the-required-interfaces"></a>A szükséges felületek belefoglalása

A minősítési folyamat átadásához be kell vonnia és végre kell hajtania az **eszköz adatai** felületet a képesség modellben. Ez az illesztőfelület a következő azonosítóval rendelkezik:

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> Ha befejezte a [gyors üzembe helyezést: Eszköz-képesség modell használatával létrehozhat egy eszközt](quickstart-create-pnp-device.md), már belefoglalta az **eszköz adatai** felületet a modellben.

Az **eszköz adatai** felületének az eszköz modelljébe való felvételéhez adja hozzá az interfész azonosítóját `implements` a képesség modell tulajdonságához:

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
}
```

Az **eszköz információs** felületének megtekintése a vs Code-ban:

1. A Command paletta megnyitásához használja a **CTRL + SHIFT + P** billentyűkombinációt.

1. Adja meg **Plug and Play** , majd válassza ki a **IoT Plug and Play nyissa meg a Model repository** parancsot. Válassza a **nyilvános modell-adattár megnyitása**lehetőséget. A nyilvános modell tárháza a VS Code-ban nyílik meg.

1. A nyilvános modell adattárában válassza a **felületek** fület, válassza a szűrő ikont, majd írja be az **eszköz adatait** a szűrő mezőbe.

1. Az **eszköz adatai** felület helyi másolatának létrehozásához jelölje ki azt a szűrt listában, majd válassza a **Letöltés**lehetőséget. A VS Code megjeleníti a csatoló fájlját.

## <a name="update-device-code"></a>Eszköz kódjának frissítése

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Eszköz kiépítés engedélyezése az Azure IoT Device kiépítési szolgáltatással (DPS)

Az eszköz hitelesítéséhez engedélyeznie kell az üzembe helyezést az [Azure IoT Device kiépítési szolgáltatás (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps)használatával. Ha hozzá szeretné adni a DPS használatát, a C Code-t is létrehozhatja a VS Code-ban. Kövesse az alábbi lépéseket:

1. Nyissa meg a fájlt a VS Code-ban a DCM-fájllal, a **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort, írja be a **IoT Plug and Play**, majd válassza az **eszköz kódjának**előállítása lehetőséget.

1. Válassza ki azt a DCM-fájlt, amelyet az eszköz kódjának létrehozásához használni kíván.

1. Adja meg a projekt nevét, amely az eszköz alkalmazásának neve.

1. Válassza az **ANSI C** nyelvet.

1. A projekt típusaként válassza a **CMAK-projekt** lehetőséget.

1. Válasszon a következőn **keresztül: DPS (Device kiépítési szolgáltatás) szimmetrikus kulcs** , mint a kapcsolatok módszere.

1. A VS Code egy új ablakot nyit meg a generált kódlap-fájlokkal.

1. `main.c`Nyissa meg a t, töltse ki a **dpsIdScope**, a **sasKey**és az előkészített **regisztrációban** . Ezt az információt a minősítési portálról kérheti le. További információ: [a IoT Plug and Play eszköz csatlakoztatása és tesztelése](tutorial-certification-test.md#connect-and-discover-interfaces).

    ```c
    // TODO: Specify DPS scope ID if you intend on using DPS / IoT Central.
    static const char *dpsIdScope = "[DPS Id Scope]";
    
    // TODO: Specify symmetric keys if you intend on using DPS / IoT Central and symmetric key based auth.
    static const char *sasKey = "[DPS symmetric key]";
    
    // TODO: specify your device registration ID
    static const char *registrationId = "[device registration Id]";
    ```

1. Mentse a fájlt.

### <a name="implement-standard-interfaces"></a>Standard felületek implementálása

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>A modell információinak és az SDK-információs felületek implementálása

Az Azure IoT eszközoldali SDK implementálja a modell információit és az SDK információs felületeit. Ha a Code generálási függvényt használja a VS Code-ban, az eszköz kódja a IoT Plug and Play Device SDK-t használja.

Ha úgy döntött, hogy nem használja az Azure IoT eszközoldali SDK-t, a saját megvalósításához használhatja az SDK forráskódját is.

#### <a name="implement-the-device-information-interface"></a>Az eszköz információs felületének implementálása

Implementálja az eszköz **információs** felületét az eszközön, és adja meg az eszközre vonatkozó információkat az eszközről a futási időben.

Példaként használhatja a [Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) rendszerhez készült **eszköz információi** felületének implementációját.

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>A modellben definiált összes képesség megvalósítása

A minősítés során az eszköz programozott módon van tesztelve, hogy biztosítsa a felületén definiált képességek megvalósítását. Az 501-as HTTP-állapotkód használatával válaszoljon az írási és olvasási tulajdonságra, és ha az eszköz nem valósítja meg az eszközt, a parancs kéri.

## <a name="next-steps"></a>További lépések

Most, hogy már létrehozott egy IoT Plug and Play eszközre készen áll a minősítésre, a javasolt következő lépés:

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan tanúsíthatja eszközét](tutorial-certification-test.md)
