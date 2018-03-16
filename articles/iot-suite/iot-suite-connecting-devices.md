---
title: "Windows-eszközök a távoli figyelése c. - Azure kiépítése |} Microsoft Docs"
description: "Eszköz csatlakoztatása az Azure IoT Suite előre konfigurált távoli figyelési megoldást a Windows rendszeren futó C alkalmazással ismerteti."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 83d0427a3ba8c634699608c38ab22efb1f275e52
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Csatlakoztassa az eszközt a távoli felügyeleti előkonfigurált megoldás (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ez az oktatóanyag bemutatja, hogyan egy fizikai eszköz csatlakozni a távoli felügyeleti előkonfigurált megoldás.

## <a name="create-a-c-client-solution-on-windows"></a>C ügyfél megoldás létrehozása a Windows rendszeren

Csakúgy, mint a beágyazott korlátozott eszközökön futó alkalmazások, az Ügyfélkód az eszköz alkalmazás íródott a c kiszolgálóra. Ebben az oktatóanyagban hoz létre az alkalmazás a Windows rendszerű gépen.

### <a name="create-the-starter-project"></a>Az alapszintű projekt létrehozása

Hozzon létre egy alapszintű projektet a Visual Studio 2017, és az IoT Hub eszköz ügyfél NuGet-csomagok hozzáadása:

1. A Visual Studio, hozzon létre egy C-konzolalkalmazást a Visual C++ használatával **Windows-Konzolalkalmazás** sablont. Nevet a projektnek **RMDevice**.

    ![Visual C++ Windows-Konzolalkalmazás létrehozása](media/iot-suite-connecting-devices/visualstudio01.png)

1. A **Megoldáskezelőben**, törölje a fájlokat `stdafx.h`, `targetver.h`, és `stdafx.cpp`.

1. A **Megoldáskezelőben**, nevezze át a fájlt `RMDevice.cpp` való `RMDevice.c`.

    ![Solution Explorer ábrázoló RMDevice.c fájl átnevezése](media/iot-suite-connecting-devices/visualstudio02.png)

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **RMDevice** projektre, majd kattintson **kezelése NuGet-csomagok**. Válasszon **Tallózás**, majd keresse meg, és telepítse a következő NuGet-csomagok:

    * Microsoft.Azure.IoTHub.Serializer
    * Microsoft.Azure.IoTHub.IoTHubClient
    * Microsoft.Azure.IoTHub.MqttTransport

    ![NuGet-Csomagkezelő telepített Microsoft.Azure.IoTHub csomagok jeleníti meg.](media/iot-suite-connecting-devices/visualstudio03.png)

1. A **Solution Explorer**, kattintson a jobb gombbal a **RMDevice** projektre, majd válassza ki **tulajdonságok** nyissa meg a projektet a **tulajdonságlapjain** a párbeszédpanel. További információkért lásd: [beállításának Visual C++ projekt tulajdonságai](https://docs.microsoft.com/cpp/ide/working-with-project-properties).

1. Válassza ki a **C/C++** mappát, majd válassza ki a **előre le fordítva fejlécek** tulajdonságlapján.

1. Állítsa be **előre le fordítva fejléc** való **előre le nem használ fordítva fejlécek**. Válassza a **alkalmaz**.

    ![Projekt tulajdonságok nem használja az előfordított fejlécek projekt megjelenítése](media/iot-suite-connecting-devices/visualstudio04.png)

1. Válassza ki a **Linker** mappát, majd válassza ki a **bemeneti** tulajdonságlapján.

1. Adja hozzá `crypt32.lib` számára a **további függőségek** tulajdonság. A projekt tulajdonságértékek mentéséhez válasszon **OK** , majd **OK** újra.

    ![Projekt tulajdonságok crypt32.lib beleértve Linker megjelenítése](media/iot-suite-connecting-devices/visualstudio05.png)

### <a name="add-the-parson-json-library"></a>A Parson JSON könyvtár hozzáadása

A Parson JSON könyvtár hozzáadása a **RMDevice** projektre, és adja hozzá a szükséges `#include` utasításokat:

1. A megfelelő mappát a számítógépén klónozza a Parson GitHub-tárházban, a következő parancsot:

    ```cmd
    git clone https://github.com/kgabis/parson.git
    ```

1. Másolás a `parson.h` és `parson.c` fájlok a Parson tárház helyi másolatát a **RMDevice** projekt mappájából.

1. A Visual Studióban, kattintson a jobb gombbal a **RMDevice** projektre, válassza a **Hozzáadás**, és válassza a **meglévő cikk**.

1. Az a **meglévő elem hozzáadása** párbeszédpanelen válassza a `parson.h` és `parson.c` -fájlok a **RMDevice** projekt mappájából. Ezeket a fájlokat két hozzáadása a projekthez, válassza a **Hozzáadás**.

    ![Megoldáskezelő parson.h és parson.c fájlokat jeleníti meg.](media/iot-suite-connecting-devices/visualstudio06.png)

1. A Visual Studióban nyissa meg a `RMDevice.c` fájlt. Cserélje le a meglévő `#include` utasítások a következő kóddal:

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include <string.h>
    ```

    > [!NOTE]
    > Most már ellenőrizheti, hogy rendelkezik-e a helyes függőség beállítja azt a megoldás létrehozása a projekthez.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

Adja hozzá a meghívni kívánt kódot a **távoli\_figyelési\_futtatása** funkciót, majd létre, és futtassa az alkalmazást:

1. A meghívni kívánt a **távoli\_figyelési\_futtatása** működik, cserélje le a **fő** függvényt a következő kódot:

    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Válasszon **Build** , majd **megoldás fordítása** hozható létre az eszköz alkalmazás.

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **RMDevice** projektre, válassza a **Debug**, és válassza a **Start új példányt** a minta futtatásához . A konzol üzenetet jeleníti meg:

    * Az alkalmazás minta telemetriai adatokat küld az előkonfigurált megoldás.
    * A megoldás irányítópultjának beállítani kívánt tulajdonságértékek kap.
    * A megoldás irányítópultja metódusokra válaszol.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
