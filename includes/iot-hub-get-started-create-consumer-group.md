---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66248840"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Fogyasztói csoport hozzáadása az IoT-központhoz

[A fogyasztói csoportok](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) független nézeteket biztosítanak az eseményfolyamba, amelyek lehetővé teszik, hogy az alkalmazások és az Azure-szolgáltatások egymástól függetlenül használják fel az adatokat ugyanabból az Event Hub-végpontból. Ebben a szakaszban egy fogyasztói csoportot ad hozzá az IoT hub beépített végponthoz, amely et az oktatóanyag később használja az adatok lekérése a végpontról.

Ha hozzá szeretne adni egy fogyasztói csoportot az IoT-központhoz, kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/) nyissa meg az IoT Hubot.

2. A bal oldali ablaktáblában válassza a **Beépített végpontok**lehetőséget, válassza az **Események** lehetőséget a jobb oldali ablaktáblán, és írjon be egy nevet a Fogyasztói **csoportok mezőbe.** Kattintson a **Mentés** gombra.

   ![Fogyasztói csoport létrehozása az IoT-központban](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
