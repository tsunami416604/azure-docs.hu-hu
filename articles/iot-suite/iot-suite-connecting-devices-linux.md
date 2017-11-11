---
title: "Távoli megfigyeléséhez, a C - Azure Linux rendszerű eszközre továbbítani |} Microsoft Docs"
description: "Eszköz csatlakoztatása az Azure IoT Suite előre konfigurált távoli figyelési megoldást igényelnek a futó Linux C alkalmazással ismerteti."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0c7c8039-0bbf-4bb5-9e79-ed8cff433629
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: 2121198482265e9cc0682e5099b0294ad3f2ab3e
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>Csatlakoztassa az eszközt a távoli felügyeleti előkonfigurált megoldás (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ez az oktatóanyag bemutatja, hogyan egy fizikai eszköz csatlakozni a távoli felügyeleti előkonfigurált megoldás.

## <a name="create-a-c-client-project-on-linux"></a>C ügyfél-projekt létrehozása Linux rendszeren

Csakúgy, mint a beágyazott korlátozott eszközökön futó alkalmazások, az Ügyfélkód az eszköz alkalmazás íródott a c kiszolgálóra. Ebben az oktatóanyagban az alkalmazás azon a számítógépen, amelyen Ubuntu (Linux) hoz létre.

Lépések elvégzéséhez szüksége van egy 15.04 vagy későbbi Ubuntu verziót futtató eszközre. A folytatás előtt telepítse a csomagokat az Ubuntu eszközre, a következő parancsot:

```sh
sudo apt-get install cmake gcc g++
```

### <a name="install-the-client-libraries-on-your-device"></a>A klienskódtárak segítségével telepítse az eszközre

Az Azure IoT Hub klienskódtárak segítségével telepítheti az Ubuntu eszköz használt csomag érhetők el a **apt get** parancsot. Az alábbi lépésekkel telepítse a csomagot, amely tartalmazza az IoT-központ ügyfél és a fejléc fájlokat a számítógépre Ubuntu:

1. A rendszerhéj a AzureIoT tárház hozzáadása a számítógéphez:

    ```sh
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

1. Az azure-iot-sdk-c-fejlesztői csomag telepítése

    ```sh
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

### <a name="install-the-parson-json-parser"></a>Telepítse a Parson JSON-elemző

Az IoT-központ klienskódtárak segítségével a Parson JSON-elemző használatával elemezni üzenet Payload van jelen. A megfelelő mappát a számítógépén klónozza a Parson GitHub-tárházban, a következő parancsot:

```sh
git clone https://github.com/kgabis/parson.git
```

### <a name="prepare-your-project"></a>Készítse elő a projekthez

A Ubuntu gépen, hozzon létre egy nevű `remote_monitoring`. Az a `remote_monitoring` mappába:

- Hozza létre a négy fájlokat `main.c`, `remote_monitoring.c`, `remote_monitoring.h`, és `CMakeLists.txt`.
- Hozzon létre nevű `parson`.

Másolja a fájlokat `parson.c` és `parson.h` Parson összetevőtárházat be helyi másolatát a `remote_monitoring/parson` mappát.

Egy szövegszerkesztőben nyissa meg a `remote_monitoring.c` fájlt. Adja hozzá a következő `#include`-utasításokat:

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

Az alábbi lépések bemutatják, hogyan használható *CMake* hozhat létre az ügyfélalkalmazást.

1. Egy szövegszerkesztőben nyissa meg a **CMakeLists.txt** fájlt a `remote_monitoring` mappát.

1. Az alábbi utasítások segítségével meghatározhatja, hogyan hozható létre az ügyfél-alkalmazás hozzáadása:

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

1. Az a `remote_monitoring` mappa, hozzon létre egy mappát tárolásához a *ellenőrizze* CMake előállított fájlokat. Ezután futtassa a **cmake** és **ellenőrizze** parancsok az alábbiak szerint:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Az ügyfélalkalmazás futtatása, és telemetriai adatokat küldhet az IoT hubhoz:

    ```sh
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
