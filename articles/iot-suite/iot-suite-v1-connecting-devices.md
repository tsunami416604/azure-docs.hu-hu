---
title: "Csatlakozás egy eszközt a Windows C |} Microsoft Docs"
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
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 56c58a31c6b20bd8da7d1442ae75425cb3e20f3d
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Csatlakoztassa az eszközt a távoli felügyeleti előkonfigurált megoldás (Windows)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>C minta megoldás létrehozása a Windows rendszeren
A következő lépések bemutatják a hozzon létre egy ügyfélalkalmazást, amely kommunikál a távoli felügyeleti előkonfigurált megoldás. Ez az alkalmazás a C és beépített, és futtassa a Windows.

Hozzon létre egy alapszintű projektet a Visual Studio 2015-öt vagy a Visual Studio 2017, és az IoT Hub eszköz ügyfél NuGet-csomagok hozzáadása:

1. A Visual Studio, hozzon létre egy C-konzolalkalmazást a Visual C++ használatával **Win32 Konzolalkalmazás** sablont. Nevet a projektnek **RMDevice**.
2. Az a **Alkalmazásbeállítások** lapját a **Win32 alkalmazás varázsló**, ügyeljen arra, hogy **Konzolalkalmazás** van kiválasztva, és törölje a jelet **Precompiled fejléc** és **biztonságos fejlesztési Életciklussal (SDL) ellenőrzi**.
3. A **Megoldáskezelőben**, a fájlok stdafx.h, targetver.h és stdafx.cpp törléséhez.
4. A **Megoldáskezelőben**, nevezze át a fájlt RMDevice.cpp RMDevice.c.
5. A **Megoldáskezelőben**, kattintson a jobb gombbal a **RMDevice** projektre, majd kattintson **kezelése NuGet-csomagok**. Kattintson a **Tallózás**, majd keresse meg, és telepítse a következő NuGet-csomagok:
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.MqttTransport
6. A **Solution Explorer**, kattintson a jobb gombbal a **RMDevice** projektre, majd kattintson **tulajdonságok** nyissa meg a projektet a **tulajdonságlapjain** a párbeszédpanel. További információkért lásd: [beállításának Visual C++ projekt tulajdonságai][lnk-c-project-properties]. 
7. Kattintson a **Linker** mappát, majd kattintson a **bemeneti** tulajdonságlapján.
8. Adja hozzá **crypt32.lib** számára a **további függőségek** tulajdonság. Kattintson a **OK** , majd **OK** újra mentésére a projekt tulajdonságok értékeit.

A Parson JSON könyvtár hozzáadása a **RMDevice** projektre, és adja hozzá a szükséges `#include` utasításokat:

1. A megfelelő mappát a számítógépén klónozza a Parson GitHub-tárházban, a következő parancsot:

    ```
    git clone https://github.com/kgabis/parson.git
    ```

1. A parson.h és parson.c fájlok másolását a Parson tárház helyi példányát a **RMDevice** projekt mappájából.

1. A Visual Studióban, kattintson a jobb gombbal a **RMDevice** projektre, kattintson **Hozzáadás**, és kattintson a **meglévő cikk**.

1. Az a **meglévő elem hozzáadása** párbeszédpanelen válassza a parson.h és parson.c-fájlok a **RMDevice** projekt mappájából. Kattintson a **Hozzáadás** két fájlt hozzáadása a projekthez.

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
    > Most már ellenőrizheti, hogy a projekt rendelkezik-e a helyes függőség az épület beállítása.

[!INCLUDE [iot-suite-v1-connecting-code](../../includes/iot-suite-v1-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Hozza létre, és futtathatja a

Adja hozzá a meghívni kívánt kódot a **távoli\_figyelési\_futtatása** működéséhez majd összeállítása, és futtassa az alkalmazást.

1. Cserélje le a **fő** függvény hívása a következő kóddal a **távoli\_figyelési\_futtatása** függvény:
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Kattintson a **Build** , majd **megoldás fordítása** hozható létre az eszköz alkalmazás.

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **RMDevice** projektre, kattintson **Debug**, és kattintson a **Start új példány** a minta futtatásához. A konzol üzeneteket jelenít meg, az alkalmazás minta telemetriai adatokat küld az előkonfigurált megoldás, a megoldás irányítópultjának beállítani kívánt tulajdonságértékek kap, és válaszol-e a megoldás irányítópultja metódusokra.

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx
