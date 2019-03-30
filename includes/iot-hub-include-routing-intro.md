---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 400f12237ae8b8cbaf6d66bda1663ecb680136f3
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630916"
---
[Üzenet-útválasztása](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) telemetriai adatok küldését az IoT-eszközökről származó beépített Event Hub-kompatibilis végpontokra vagy egyéni végpontok például lehetővé teszi, hogy a blob storage, Service Bus-üzenetsorok, Service Bus-témakörök és Eseményközpontok. Egyéni üzenet útválasztás konfigurálásához, hozzon létre [útválasztási lekérdezések](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) szabhatja testre az útvonalat, amely megfelel egy bizonyos feltételnek. A beállítás után az IoT Hub automatikusan a végpontokhoz irányítja át a bejövő adatokat. Ha az üzenet nem egyezik a megadott útválasztási lekérdezések bármelyikét, az alapértelmezett végpont lesz irányítva.

2. rész – ebben az oktatóanyagban megismerheti, hogyan állíthatja be, és ezek egyéni útválasztási lekérdezések használata az IoT hubbal. Egy IoT-eszköz egy több végpontot, beleértve a blob storage és a egy Service Bus-üzenetsor üzeneteit, irányíthatja. A Service Bus-üzenetsorba való üzenetet, dolgozza fel a logikai alkalmazás és az e-mailben küldött. Üzenetek, amelyek nem rendelkeznek, egyéni üzenet-útválasztása definiálva az alapértelmezett végpont küldött, majd dolgozza fel az Azure Stream Analytics és tekinthetők meg a Power BI-Vizualizációk.

 1. és 2. Ez az oktatóanyag teljes részére hajtsa végre a következő feladatokat:

**I. rész Erőforrások létrehozása, és beállíthatja az üzenet-útválasztása**
> [!div class="checklist"]
> * Az erőforrások – egy IoT hubot, tárfiók, Service Bus-üzenetsorba és szimulált eszköz létrehozása. Ezt megteheti a portálon, az Azure CLI, Azure PowerShell vagy az Azure Resource Manager-sablon használatával.
> * A végpontok és Üzenetútvonalak konfigurálása az IoT Hub a storage-fiók és a Service Bus-üzenetsorba.

**II. rész: Üzeneteket küldjön a hubnak, irányított eredmények megtekintése**
> [!div class="checklist"]
> * Egy logikai alkalmazás létrehozása, amely egy üzenet Service Bus-üzenetsorba való érkezésekor indul el és küld e-maileket.
> * Egy olyan alkalmazás letöltése és futtatása, amely a Hubhoz üzeneteket küldő IoT-eszközt szimulál a különböző útválasztási lehetőségekhez.
> * Power BI-vizualizáció létrehozása az alapértelmezett végpontra küldött adatokról.
> * Eredmények megtekintése...
> * ... a Service Bus-üzenetsorban és az e-mailekben.
> * ... a tárfiókban.
> * ... a Power BI-vizualizációban.

## <a name="prerequisites"></a>Előfeltételek

* Ez az oktatóanyag 1 részéhez:
  - Rendelkeznie kell egy Azure-előfizetéssel. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

* Ez az oktatóanyag 2 részéhez:
  - 1. rész befejeződött. a jelen oktatóanyag rendelkezik, és rendelkezik az erőforrások továbbra is elérhető.
  - A [Visual Studio](https://www.visualstudio.com/) telepítése.
  - Power BI-fiók az alapértelmezett végpont Stream Analytics-elemzéséhez. ([A Power BI ingyenes kipróbálása](https://app.powerbi.com/signupredirect?pbi_source=web).)
  - Office 365-fiók értesítési e-mailek küldéséhez.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
