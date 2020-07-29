---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 07/14/2020
ms.openlocfilehash: b47580ad3aa14cee3ea3658ecd3b921e8e738882
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352758"
---
## <a name="download-the-model"></a>A modell letöltése

Később az Azure IoT Explorer használatával megtekintheti az eszközt, amikor az IoT hubhoz csatlakozik. Az Azure IoT Explorernek szüksége van az eszköz által küldött **modell-azonosítóval** megegyező nevű modell helyi példányára. A Model fájl lehetővé teszi az Azure IoT Explorer számára az eszköz által megvalósított telemetria, tulajdonságok és parancsok megjelenítését.

Ha még nem töltötte le a minta-modell fájljait:

1. Hozzon létre egy *modell* nevű mappát a helyi gépen.

1. Kattintson a jobb gombbal [aTemperatureController.js](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) elemre, és mentse a JSON-fájlt a *modellek* mappába.

1. Kattintson a jobb gombbal [aThermostat.js](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json) elemre, és mentse a JSON-fájlt a *modellek* mappába.
