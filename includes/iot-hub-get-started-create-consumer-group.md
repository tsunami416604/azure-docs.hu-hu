---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "66248840"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Fogyasztói csoport hozzáadása az IoT hub-hoz

A [fogyasztói csoportok](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) független nézeteket biztosítanak az esemény-adatfolyamhoz, amely lehetővé teszi, hogy az alkalmazások és az Azure-szolgáltatások egymástól függetlenül használják az adott Event hub-végpont adatait. Ebben a szakaszban egy fogyasztói csoportot ad hozzá az IoT hub beépített végpontához, amelyet az oktatóanyag későbbi részében használ az adatok végpontból való lekéréséhez.

Az alábbi lépéseket követve adhat hozzá egy fogyasztói csoportot az IoT hub-hoz:

1. Az [Azure Portalon](https://portal.azure.com/) nyissa meg az IoT Hubot.

2. A bal oldali panelen válassza a **beépített végpontok**lehetőséget, válassza a jobb oldali ablaktábla **események** elemét, és adjon meg egy nevet a **fogyasztói csoportok**területen. Kattintson a **Mentés** gombra.

   ![Fogyasztói csoport létrehozása az IoT hub-ban](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
