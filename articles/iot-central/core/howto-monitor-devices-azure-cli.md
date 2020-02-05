---
title: Az eszközök kapcsolatának figyelése az Azure IoT Central Explorer használatával
description: Az eszközök üzeneteinek figyelése és az eszköz két módosításának megfigyelése az IoT Central Explorer parancssori felületén keresztül.
author: viv-liu
ms.author: viviali
ms.date: 12/18/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 3f18537b4b038844c9aa824593e354c23c792370
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026782"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Eszközkapcsolatok monitorozása az Azure CLI-vel

*Ez a témakör az építők és a rendszergazdákra vonatkozik.*

Az Azure CLI IoT bővítmény használatával megtekintheti, hogy az eszközök milyen üzeneteket küldenek IoT Centralnek, és megfigyelik a Twin eszközök változásait. Ezt az eszközt használhatja az eszközök kapcsolatának hibakeresésére és figyelésére, valamint a felhőbe nem lépő eszközök üzeneteinek diagnosztizálására, illetve a kettős módosításokra nem válaszoló eszközökre.

[További részletekért tekintse meg az Azure CLI-bővítmények referenciáját](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/central)

## <a name="prerequisites"></a>Előfeltételek

+ Az Azure CLI telepítve van, és a verziója 2.0.7 vagy újabb. Az `az --version`futtatásával keresse meg az Azure CLI verzióját. Ismerje meg, hogyan telepítheti és frissítheti az [Azure CLI-docs](https://docs.microsoft.com/cli/azure/install-azure-cli)
+ Munkahelyi vagy iskolai fiók az Azure-ban, amely felhasználóként van hozzáadva egy IoT Central alkalmazásban.

## <a name="install-the-iot-central-extension"></a>Az IoT Central-bővítmény telepítése

Futtassa a következő parancsot a parancssorból a telepítéshez:

```cmd/sh
az extension add --name azure-cli-iot-ext
```

A bővítmény verziójának ellenőrzéséhez futtassa a 
```cmd/sh
az --version
```
Az Azure-CLI-IOT-ext kiterjesztésnek 0.8.1 vagy magasabbnak kell lennie. Ha nem, futtassa a parancsot.
```cmd/sh
az extension update --name azure-cli-iot-ext
```

## <a name="using-the-extension"></a>A bővítmény használata

A következő szakaszok a `az iot central`futtatásakor használható általános parancsokat és beállításokat ismertetik. A parancsok és beállítások teljes készletének megtekintéséhez adja át `--help` `az iot central` vagy annak alparancsainak bármelyikét.

### <a name="login"></a>Bejelentkezés

Először jelentkezzen be az Azure CLI-be. 

```cmd/sh
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>A IoT Central-alkalmazás alkalmazás-AZONOSÍTÓjának beolvasása
Az **Adminisztráció/alkalmazás beállításaiban**másolja az **alkalmazás azonosítóját**. Ezt a későbbi lépésekben fogja használni.

### <a name="monitor-messages"></a>Üzenetek figyelése
A IoT Central alkalmazásnak az eszközökről küldött üzeneteinek figyelése. Ebbe beletartozik az összes fejléc és Megjegyzés is.

```cmd/sh
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Eszköz tulajdonságainak megtekintése
Megtekintheti egy adott eszköz aktuális olvasási és olvasási/írási/olvasási eszközének tulajdonságait.

```cmd/sh
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a IoT Central Explorer használatát, a javasolt következő lépés az [eszközök kezelésének](howto-manage-devices.md)megismerése IoT Central.
