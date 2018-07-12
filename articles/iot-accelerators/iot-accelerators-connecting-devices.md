---
title: Távoli figyelési C – az Azure Windows-eszköz kiépítése |} A Microsoft Docs
description: Ismerteti, hogyan lehet egy eszköz csatlakoztatása a távoli figyelési megoldásgyorsító futó Windows C nyelven írt alkalmazás használatával.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 139daea3e885636b352d4c9a1ba2651a24195b21
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38309870"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Az eszköz csatlakoztatása a távoli figyelési megoldásgyorsító (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ez az oktatóanyag bemutatja, hogyan kell egy fizikai eszköz csatlakoztatása a távoli figyelési megoldásgyorsító.

## <a name="create-a-c-client-solution-on-windows"></a>A Windows C ügyfél megoldás létrehozása

Csakúgy, mint legnagyobb beágyazott korlátozott eszközökön futó alkalmazásokhoz, az Ügyfélkód az eszköz alkalmazás írt c-hez Ebben az oktatóanyagban hozza létre az alkalmazást egy Windows rendszert futtató gépen.

### <a name="create-the-starter-project"></a>A kezdő-projekt létrehozása

Hozzon létre egy alapszintű projektet a Visual Studio 2017-ben, és adja hozzá az IoT Hub eszköz ügyfél NuGet-csomagok:

1. A Visual Studióban hozzon létre egy C-konzolalkalmazást a Visual C++ használatával **Windows-Konzolalkalmazást** sablont. Adja a projektnek **RMDevice**.

    ![Visual C++ Windows-Konzolalkalmazás létrehozása](./media/iot-accelerators-connecting-devices/visualstudio01.png)

1. A **Megoldáskezelőben**, törölje a fájlokat `stdafx.h`, `targetver.h`, és `stdafx.cpp`.

1. A **Megoldáskezelőben**, nevezze át a fájlt `RMDevice.cpp` való `RMDevice.c`.

    ![Megoldás Explorer ábrázoló RMDevice.c fájl átnevezése](./media/iot-accelerators-connecting-devices/visualstudio02.png)

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **RMDevice** projektre, majd kattintson **NuGet-csomagok kezelése**. Válasszon **Tallózás**, majd keresse meg és telepítse a következő NuGet-csomagokat:

    * Microsoft.Azure.IoTHub.Serializer
    * Microsoft.Azure.IoTHub.IoTHubClient
    * Microsoft.Azure.IoTHub.MqttTransport

    ![NuGet-Csomagkezelő telepített Microsoft.Azure.IoTHub csomagokat jeleníti meg.](./media/iot-accelerators-connecting-devices/visualstudio03.png)

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **RMDevice** projektre, majd válassza ki **tulajdonságok** megnyitja a projektet **tulajdonságait tartalmazó oldalakon** párbeszédpanel bezárásához. További információkért lásd: [beállításának Visual C++ projekt tulajdonságai](https://docs.microsoft.com/cpp/ide/working-with-project-properties).

1. Válassza ki a **C/C++** mappát, majd válassza ki a **előre lefordított fejlécek** tulajdonságlap.

1. Állítsa be **előre lefordított fejléc** való **fejlécek nem használja az előre lefordított**. Válassza a **alkalmaz**.

    ![Projekt tulajdonságai nem használ lefordított fejlécek projekt megjelenítése](./media/iot-accelerators-connecting-devices/visualstudio04.png)

1. Válassza ki a **Linker** mappát, majd válassza ki a **bemeneti** tulajdonságlap.

1. Adjon hozzá `crypt32.lib` , a **további függőségek** tulajdonság. Válassza ki a projekt mentése tulajdonságértékek, **OK** , majd **OK** újra.

    ![Projekt tulajdonságai Linker, beleértve a crypt32.lib megjelenítése](./media/iot-accelerators-connecting-devices/visualstudio05.png)

### <a name="add-the-parson-json-library"></a>A Parson JSON könyvtár hozzáadása

Adja hozzá a Parson JSON-könyvtárban a **RMDevice** projektre, és adja hozzá a szükséges `#include` utasításokat:

1. A megfelelő mappát a számítógépen klónozza a Parson GitHub-tárházban, a következő paranccsal:

    ```cmd
    git clone https://github.com/kgabis/parson.git
    ```

1. Másolás a `parson.h` és `parson.c` a Parson-adattár helyi példányának fájlokat a **RMDevice** projektmappában.

1. A Visual Studióban kattintson a jobb gombbal a **RMDevice** projektre, válassza a **Hozzáadás**, és válassza a **meglévő elem**.

1. Az a **meglévő elem hozzáadása** párbeszédpanelen válassza ki a `parson.h` és `parson.c` található fájlokat a **RMDevice** projektmappában. Adja hozzá a projekthez két fájlt, válassza a **Hozzáadás**.

    ![Megoldáskezelő parson.h és parson.c fájlokat jeleníti meg.](./media/iot-accelerators-connecting-devices/visualstudio06.png)

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
    > Most ellenőrizheti, hogy rendelkezik-e a helyes függőség beállítja azt a megoldás létrehozása a projekthez.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

Adja hozzá a kódot kell elindítani a **távoli\_figyelési\_futtatása** függvényt, majd alkalmazás összeállítása és futtatása az eszköz:

1. Meghívni a **távoli\_figyelési\_futtatása** működik, cserélje le a **fő** függvény a következő kóddal:

    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Válassza a **összeállítása** , majd **megoldás fordítása** hozhat létre az alkalmazást.

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **RMDevice** projektre, válassza a **Debug**, és válassza a **új példány indítása** a minta futtatásához . A konzolon, üzeneteket jelenít meg:

    * Az alkalmazás minta telemetriai adatokat küld a megoldásgyorsító.
    * A megoldás irányítópultján beállított kívánt tulajdonságértékeket kap.
    * A megoldás irányítópultjáról indított metódusokra válaszol.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
