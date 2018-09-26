---
title: C használata a Windows eszköz csatlakoztatása |} A Microsoft Docs
description: Ismerteti, hogyan lehet egy eszköz csatlakoztatása az Azure IoT Suite előre konfigurált távoli figyelési megoldásban a Windows rendszerű C nyelven írt alkalmazás használatával.
services: ''
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 56c58a31c6b20bd8da7d1442ae75425cb3e20f3d
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47093095"
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Az eszköz csatlakoztatása a távoli figyelési előre konfigurált megoldáshoz (Windows)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>A Windows c. minta megoldás létrehozása
A következő lépések bemutatják, hogyan hozhat létre egy ügyfélalkalmazás, amely kommunikál a távoli figyelési előre konfigurált megoldás. Ez az alkalmazás C nyelven írt és készített, és futtassa a Windows.

Hozzon létre egy alapszintű projektet a Visual Studio 2015 vagy Visual Studio 2017, és adja hozzá az IoT Hub eszköz ügyfél NuGet-csomagok:

1. A Visual Studióban hozzon létre egy C-konzolalkalmazást a Visual C++ használatával **Win32 Console Application** sablont. Adja a projektnek **RMDevice**.
2. Az a **Alkalmazásbeállítások** lapját a **Win32 alkalmazás varázslójának**, ügyeljen arra, hogy **Konzolalkalmazás** van jelölve, és törölje a jelet **Precompiled fejléc** és **biztonságos fejlesztési Életciklussal (SDL) ellenőrzi**.
3. A **Megoldáskezelőben**, törölje a fájlokat stdafx.h, targetver.h és stdafx.cpp.
4. A **Megoldáskezelőben**, nevezze át a fájlt RMDevice.cpp RMDevice.c.
5. A **Megoldáskezelőben**, kattintson a jobb gombbal a **RMDevice** projektre, majd kattintson **NuGet-csomagok kezelése**. Kattintson a **Tallózás**, majd keresse meg és telepítse a következő NuGet-csomagokat:
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.MqttTransport
6. A **Megoldáskezelőben**, kattintson a jobb gombbal a **RMDevice** projektre, majd kattintson **tulajdonságok** megnyitja a projektet **tulajdonságlapok** párbeszédpanel bezárásához. További információkért lásd: [beállításának Visual C++ projekt tulajdonságai][lnk-c-project-properties]. 
7. Kattintson a **Linker** mappát, majd kattintson a **bemeneti** tulajdonságlap.
8. Adjon hozzá **crypt32.lib** , a **további függőségek** tulajdonság. Kattintson a **OK** , majd **OK** újra a tulajdonságértékek menteni.

Adja hozzá a Parson JSON-könyvtárban a **RMDevice** projektre, és adja hozzá a szükséges `#include` utasításokat:

1. A megfelelő mappát a számítógépen klónozza a Parson GitHub-tárházban, a következő paranccsal:

    ```
    git clone https://github.com/kgabis/parson.git
    ```

1. Másolja a parson.h és parson.c fájlokat a Parson-adattár helyi példányának a **RMDevice** projektmappában.

1. A Visual Studióban kattintson a jobb gombbal a **RMDevice** projektre, kattintson a **Hozzáadás**, és kattintson a **meglévő elem**.

1. Az a **meglévő elem hozzáadása** párbeszédpanelen válassza ki a fájlokat a parson.h és parson.c a **RMDevice** projektmappában. Kattintson a **Hozzáadás** két fájlt hozzáadása a projekthez.

1. A Visual Studióban nyissa meg a RMDevice.c fájlt. Cserélje le a meglévő `#include` utasítások a következő kóddal:
   
    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"
    ```

    > [!NOTE]
    > Most ellenőrizheti, hogy a projekt létrehozásával, állítsa be a helyes függőség rendelkezik-e.

[!INCLUDE [iot-suite-v1-connecting-code](../../includes/iot-suite-v1-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

Adja hozzá a kódot kell elindítani a **távoli\_figyelési\_futtatása** működik, és ezután létrehozhatja és futtathatja az alkalmazást.

1. Cserélje le a **fő** függvény meghívása a következő kóddal a **távoli\_figyelési\_futtatása** függvény:
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Kattintson a **összeállítása** , majd **megoldás fordítása** hozhat létre az alkalmazást.

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **RMDevice** projektre, kattintson a **Debug**, és kattintson a **új példány indítása** a minta futtatásához. A konzol üzeneteket jelenít meg, az alkalmazás minta telemetriai adatokat küld az előre konfigurált megoldás, a megoldás irányítópultján beállított kívánt tulajdonságértékeket kap, és a megoldás irányítópultjáról indított metódusokra válaszol.

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx
