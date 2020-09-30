---
title: IoT Plug and Play híd | Microsoft Docs
description: Ismerje meg a IoT Plug and Play hidat, és hogyan használható a Windows-vagy Linux-átjáróhoz csatlakoztatott meglévő eszközök csatlakoztatására IoT Plug and Play-eszközökként.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 224f86a40fa812003463301f97bcae07de907f3c
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580471"
---
# <a name="iot-plug-and-play-bridge"></a>IoT Plug and Play híd

A IoT Plug and Play Bridge egy nyílt forráskódú alkalmazás, amellyel a Windows vagy Linux rendszerű átjáróhoz csatlakoztatott meglévő eszközök IoT Plug and Play-eszközökként csatlakoztathatók. Miután telepítette és konfigurálta az alkalmazást a Windows vagy Linux rendszerű gépen, használhatja a csatlakoztatott eszközök csatlakoztatását egy IoT hubhoz. A Bridge használatával leképezheti a IoT Plug and Play-adaptereket a csatlakoztatott eszközök küldésére, az telemetria és a parancsok meghívására szolgáló kapcsolatokra.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="A bal oldalon van néhány meglévő érzékelő (vezetékes és vezeték nélküli) egy olyan Windows vagy Linux rendszerű számítógéphez, amely IoT Plug and Play hidat tartalmaz. A IoT Plug and Play híd ezután csatlakozik egy IoT hubhoz a jobb oldalon":::

A IoT Plug and Play Bridge önálló végrehajtható fájlként is telepíthető bármely IoT-eszközön, ipari számítógépen, kiszolgálón vagy Windows 10-es vagy Linux rendszerű átjárón. Az alkalmazás kódjába is beállítható. Egy egyszerű konfigurációs JSON-fájl közli a IoT Plug and Play-híddal, hogy mely csatlakoztatott eszközöket/perifériákat kell feltenni az Azure-ba.

## <a name="supported-protocols-and-sensors"></a>Támogatott protokollok és érzékelők

A IoT Plug and Play Bridge alapértelmezés szerint a következő típusú perifériákat támogatja, az adapter dokumentációjának hivatkozásait követve:

|Perifériás|Windows|Linux|
|---------|---------|---------|
|[Bluetooth LE](https://aka.ms/iot-pnp-bridge-bluetooth)       |Igen|Nem|
|[Kamerák](https://aka.ms/iot-pnp-bridge-camera)               |Igen|Nem|
|[Modbus](https://aka.ms/iot-pnp-bridge-modbus)                |Igen|Igen|
|[MQTT](https://aka.ms/iot-pnp-bridge-mqtt)                    |Igen|Igen|
|[Sorozatszám](https://aka.ms/iot-pnp-bridge-serial)                |Igen|Igen|
|[Windows USB-perifériák](https://aka.ms/iot-pnp-bridge-usb)  |Igen|Nem alkalmazható|

>[!Important]
>A fejlesztők kiterjeszthetik a IoT Plug and Play hidat a további eszközök protokolljainak támogatásához a **[IoT Plug and Play Bridge fejlesztői dokumentációjának](https://aka.ms/iot-pnp-bridge-dev-doc)** útmutatásai alapján.

## <a name="prerequisites"></a>Előfeltételek

### <a name="os-platform"></a>Operációs rendszer platformja

A következő operációsrendszer-platformok és-verziók támogatottak:

|Platform  |Támogatott verziók  |
|---------|---------|
|Windows 10 |     Minden Windows SKU támogatott. Például: IoT Enterprise, Server, Desktop, IoT Core. *A kamera állapotának figyelése funkció esetén a 20H1 vagy a későbbi Build használata javasolt. Minden egyéb funkció elérhető minden Windows 10-es builden.*  |
|Linux     |Az Ubuntu 18,04-on tesztelt és támogatott, más disztribúciók funkciói nem lettek tesztelve.         |
||

### <a name="hardware"></a>Hardver

- Bármilyen hardveres platform, amely támogatja a fenti operációs rendszerbeli SKU-ket és verziókat.
- A soros, az USB-, a Bluetooth-és a kamera-perifériák és-érzékelők natív módon támogatottak. A IoT Plug and Play híd kiterjeszthető bármely egyéni perifériára vagy érzékelőre ([lásd a fenti perifériák szakaszt](#iot-plug-and-play-bridge)).

### <a name="development-environment"></a>Fejlesztési környezet

A IoT Plug and Play-híd létrehozásához, bővítéséhez és fejlesztéséhez a következőkre lesz szüksége:  

- Olyan fejlesztési környezet, amely támogatja a C++ fordítását, például: [Visual Studio (közösségi, Professional vagy Enterprise)](https://visualstudio.microsoft.com/downloads/)– ügyeljen arra, hogy a Visual Studio telepítésekor a c++ számítási feladatait is tartalmazza az asztali fejlesztés.
- [CMAK](https://cmake.org/download/) – a CMAK telepítésekor válassza a lehetőséget `Add CMake to the system PATH` .
- Ha Windows rendszerre épít, a Windows 17763 SDK-t is le kell töltenie: [https://developer.microsoft.com/windows/downloads/windows-10-sdk](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
- [Azure IoT hub Device C SDK](https://github.com/Azure/azure-iot-sdk-c). Az ebben a tárházban foglalt Build szkriptek automatikusan klónozott, a szükséges Azure IoT C SDK-t.

### <a name="azure-iot-products-and-tools"></a>Azure IoT-termékek és-eszközök

- **Azure IoT hub** – az eszköz csatlakoztatásához az Azure-előfizetéshez szüksége lesz egy [Azure IoT hub](https://docs.microsoft.com/azure/iot-hub/) -ra. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt. Ha nem rendelkezik IoT-hubhoz, [kövesse az alábbi utasításokat, és hozzon létre egyet](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-using-cli).

> [!Note]
> A IoT Plug and Play jelenleg az USA középső régiójában, Észak-Európában és Kelet-japán régióban létrehozott IoT-hubokon érhető el. Az alapszintű IoT-hubok nem tartalmazzák a IoT Plug and Play támogatását. Ha a IoT Plug and Play eszközzel szeretne kommunikálni, használhatja az Azure IoT Explorer eszközét. [Töltse le és telepítse az Azure IoT Explorer legújabb kiadását](./howto-use-iot-explorer.md) az operációs rendszeréhez.

## <a name="iot-plug-and-play-bridge-architecture"></a>IoT Plug and Play híd architektúrája

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="A bal oldalon van néhány meglévő érzékelő (vezetékes és vezeték nélküli) egy olyan Windows vagy Linux rendszerű számítógéphez, amely IoT Plug and Play hidat tartalmaz. A IoT Plug and Play híd ezután csatlakozik egy IoT hubhoz a jobb oldalon":::

## <a name="download-iot-plug-and-play-bridge"></a>IoT Plug and Play-híd letöltése

Letöltheti a híd előre összeállított verzióját a támogatott adapterekkel a [IoT Plug and Play Bridge kiadásokban](https://aka.ms/iot-pnp-bridge-releases) , és kiterjesztheti a legújabb kiadáshoz tartozó eszközök listáját. Töltse le az alkalmazás legújabb verzióját az operációs rendszeréhez.

Letöltheti és megtekintheti a [githubon Plug and Play Bridge IoT](https://aka.ms/bridge)forráskódját is.

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a IoT Plug and Play Bridge architektúráját, a következő lépésekkel többet is megtudhat a következőkről:

- [A IoT Plug and Play-híd használata](./howto-use-iot-pnp-bridge.md)
- [Lásd a GitHub fejlesztői referenciáját a IoT Plug and Play Bridge-hez](https://aka.ms/iot-pnp-bridge-dev-doc)
- [IoT Plug and Play híd a GitHubon](https://aka.ms/iotplugandplaybridge)
