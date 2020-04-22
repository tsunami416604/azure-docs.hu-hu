---
title: Eszközkapcsolat figyelése az Azure IoT Központi Explorer használatával
description: Az IOt Central Explorer CLI-n keresztül figyelheti az eszközüzeneteket, és figyelheti az ikereszköz-módosításokat.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 1a6106a45f5062850ceb12205528a05ed1d494be
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756664"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Eszközkapcsolatok monitorozása az Azure CLI-vel

*Ez a témakör az eszközfejlesztőkre és a megoldáskészítőkre vonatkozik.*

Az Azure CLI IoT-bővítmény segítségével megtekintheti az eszközök által az IoT Centralba küldött üzeneteket, és megfigyelheti az ikereszköz változásait. Ezzel az eszközzel debug és megfigyelheti az eszköz kapcsolatát, és diagnosztizálhatja a felhőbe vagy az ikermódosításokra nem reagáló eszközökhöz nem elérő eszközüzenetek kelendő problémáit.

[További részletekért látogasson el az Azure CLI-bővítmények hivatkozási adataira](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/central?view=azure-cli-latest)

## <a name="prerequisites"></a>Előfeltételek

+ Az Azure CLI telepítve van, és 2.0.7-es vagy újabb verziójú. Ellenőrizze az Azure CLI verzióját `az --version`a futtatásával. További információ az Azure [CLI-dokumentumok](https://docs.microsoft.com/cli/azure/install-azure-cli) telepítéséről és frissítéséről
+ Munkahelyi vagy iskolai fiók az Azure-ban, egy IoT Central alkalmazás felhasználójaként hozzáadva.

## <a name="install-the-iot-central-extension"></a>Az IoT Central bővítmény telepítése

A telepítéshez futtassa a következő parancsot a parancssorból:

```azurecli
az extension add --name azure-iot
```

Ellenőrizze a bővítmény verzióját a következő futtatásával:

```azurecli
az --version
```

Látnia kell az azure-iot kiterjesztés 0.8.1 vagy magasabb. Ha nem, futtassa:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>A bővítmény használata

Az alábbi szakaszok a futtatássorán `az iot central`használható gyakori parancsokat és beállításokat ismertetik. A parancsok és beállítások teljes készletének megtekintéséhez adja át `--help` bármelyik `az iot central` alparancsát.

### <a name="login"></a>Bejelentkezés

Kezdje azzal, hogy bejelentkezik az Azure CLI-be. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Az IoT Central alkalmazás alkalmazásazonosítójának beszereznie
A **Felügyeleti/alkalmazás beállításai**lapon másolja az **alkalmazásazonosítót.** Ezt az értéket a későbbi lépésekben használhatja.

### <a name="monitor-messages"></a>Üzenetek figyelése
Figyelje az IoT Central alkalmazásba az eszközökről küldött üzeneteket. A kimenet tartalmazza az összes fejlécet és jegyzetet.

```azurecli
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Eszköztulajdonságok megtekintése
Egy adott eszköz aktuális olvasási és olvasási/írási eszköztulajdonságainak megtekintése.

```azurecli
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>További lépések

Ha Ön eszközfejlesztő, a javasolt következő lépés az [Azure IoT Central eszközkapcsolatának](./concepts-get-connected.md)elolvasása.
