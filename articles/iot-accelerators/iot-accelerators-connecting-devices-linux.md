---
title: Távoli figyelési C – az Azure Linux rendszerű eszközök kiépítése |} A Microsoft Docs
description: Ismerteti, hogyan lehet egy eszköz csatlakoztatása a távoli figyelési megoldásgyorsító a linuxon futó C nyelven írt alkalmazás használatával.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 5d7d6522dc663f13ce40cc638ba90ac4043d435c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611412"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Az eszköz csatlakoztatása a távoli figyelési megoldásgyorsító (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ez az oktatóanyag bemutatja, hogyan kell egy fizikai eszköz csatlakoztatása a távoli figyelési megoldásgyorsító.

## <a name="create-a-c-client-project-on-linux"></a>Linux rendszeren C ügyfél projekt létrehozása

Csakúgy, mint legnagyobb beágyazott korlátozott eszközökön futó alkalmazásokhoz, az Ügyfélkód az eszköz alkalmazás írt c-hez Ebben az oktatóanyagban hozza létre az alkalmazást egy Ubuntu (Linux) rendszert futtató gépen.

A lépések elvégzéséhez szüksége 15.04 vagy újabb verzió Ubuntu-es eszközök. A folytatás előtt telepítse az előfeltételként szolgáló csomagok a következő parancsot az Ubuntu eszközön:

```sh
sudo apt-get install cmake gcc g++
```

### <a name="install-the-client-libraries-on-your-device"></a>Az eszközön az ügyfél-kódtárak telepítése

Az Azure IoT Hub-ügyfélkönyvtárak-csomagként telepítheti az Ubuntu eszköz használatával érhetők el a **apt-get paranccsal** parancsot. A következő lépéseket követve telepítse a csomagot, amely tartalmazza az IoT Hub ügyfél erőforrástár és a fejléc fájlokat a számítógépre Ubuntu:

1. Vegyen fel egy rendszerhéjból, a számítógépre a AzureIoT tárház:

    ```sh
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

1. Az azure-iot-sdk-c-fejlesztői csomag telepítése

    ```sh
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

### <a name="install-the-parson-json-parser"></a>Telepítse a Parson JSON-elemző

Az IoT Hub-ügyfélkönyvtárak a Parson JSON-elemző használatával elemezni üzenetet is észleltünk adattartalmakat. A megfelelő mappát a számítógépen klónozza a Parson GitHub-tárházban, a következő paranccsal:

```sh
git clone https://github.com/kgabis/parson.git
```

### <a name="prepare-your-project"></a>-Projektek előkészítése

Az Ubuntu-gép, hozzon létre egy mappát nevű `remote_monitoring`. Az a `remote_monitoring` mappa:

- Hozza létre a négy fájlokat `main.c`, `remote_monitoring.c`, `remote_monitoring.h`, és `CMakeLists.txt`.
- Hozzon létre nevű mappát `parson`.

Másolja a fájlokat `parson.c` és `parson.h` származó be a Parson tárház helyi példányának a `remote_monitoring/parson` mappát.

Egy szövegszerkesztőben nyissa meg a `remote_monitoring.c` fájlt. Adja hozzá a következő `#include`-utasításokat:

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

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="add-code-to-run-the-app"></a>Adja hozzá az alkalmazás futtatásához szükséges kódot

Egy szövegszerkesztőben nyissa meg a `remote_monitoring.h` fájlt. Adja hozzá a következő kódot:

```c
void remote_monitoring_run(void);
```

Egy szövegszerkesztőben nyissa meg a `main.c` fájlt. Adja hozzá a következő kódot:

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása

Az alábbi lépések bemutatják, hogyan használható *CMake* ügyfélalkalmazás hozhat létre.

1. Egy szövegszerkesztőben nyissa meg a **CMakeLists.txt** fájlt a `remote_monitoring` mappát.

1. Adja hozzá az alábbi utasítások segítségével meghatározhatja, hogyan hozhat létre az ügyfélalkalmazás:

    ```cmake
    macro(compileAsC99)
      if (CMAKE_VERSION VERSION_LESS "3.1")
        if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
          set (CMAKE_C_FLAGS "--std=c99 ${CMAKE_C_FLAGS}")
          set (CMAKE_CXX_FLAGS "--std=c++11 ${CMAKE_CXX_FLAGS}")
        endif()
      else()
        set (CMAKE_C_STANDARD 99)
        set (CMAKE_CXX_STANDARD 11)
      endif()
    endmacro(compileAsC99)

    cmake_minimum_required(VERSION 2.8.11)
    compileAsC99()

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "${CMAKE_SOURCE_DIR}/parson" "/usr/include/azureiot" "/usr/include/azureiot/inc")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./parson/parson.c
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./parson/parson.h
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_mqtt_transport
        aziotsharedutil
        umqtt
        pthread
        curl
        ssl
        crypto
        m
    )
    ```

1. Az a `remote_monitoring` mappában hozzon létre egy mappát tárolásához a *győződjön meg arról,* CMake által létrehozott fájlokat. Ezután futtassa a **cmake** és **győződjön meg arról,** parancsok az alábbiak szerint:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Futtassa az ügyfélalkalmazást és telemetriát küldjön az IoT hubnak:

    ```sh
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
