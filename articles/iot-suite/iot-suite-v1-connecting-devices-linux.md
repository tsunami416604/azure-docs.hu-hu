---
title: "Csatlakoztassa a C használatával Linux |} Microsoft Docs"
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
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: a5768041a13d5ddc355c054dc85ba651b0752aba
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>Csatlakoztassa az eszközt a távoli felügyeleti előkonfigurált megoldás (Linux)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="build-and-run-a-sample-c-client-linux"></a>Hozza létre, és egy minta C-ügyfél Linux
A következő lépések bemutatják a hozzon létre egy ügyfélalkalmazást, amely kommunikál a távoli felügyeleti előkonfigurált megoldás. Ez az alkalmazás a C és beépített és Ubuntu Linux rendszeren futtassa.

A lépések elvégzésével Ubuntu 15.04 vagy 15.10 verzióját futtató eszközök kell. A folytatás előtt telepítse a csomagokat az Ubuntu eszközre, a következő parancsot:

```
sudo apt-get install cmake gcc g++
```

## <a name="install-the-client-libraries-on-your-device"></a>A klienskódtárak segítségével telepítse az eszközre
Az Azure IoT Hub klienskódtárak segítségével telepítheti az Ubuntu eszköz használt csomag érhetők el a **apt get** parancsot. Az alábbi lépésekkel telepítse a csomagot, amely tartalmazza az IoT-központ ügyfél és a fejléc fájlokat a számítógépre Ubuntu:

1. A rendszerhéj a AzureIoT tárház hozzáadása a számítógéphez:
   
    ```
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```
2. Az azure-iot-sdk-c-fejlesztői csomag telepítése
   
    ```
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

## <a name="install-the-parson-json-parser"></a>Telepítse a Parson JSON-elemző
Az IoT-központ klienskódtárak segítségével a Parson JSON-elemző használatával elemezni üzenet Payload van jelen. A megfelelő mappát a számítógépén klónozza a Parson GitHub-tárházban, a következő parancsot:

```
git clone https://github.com/kgabis/parson.git
```

## <a name="prepare-your-project"></a>Készítse elő a projekthez
A Ubuntu gépen, hozzon létre egy nevű **távoli\_figyelési**. Az a **távoli\_figyelési** mappába:

- Hozza létre a négy fájlokat **main.c**, **távoli\_monitoring.c**, **távoli\_monitoring.h**, és **CMakeLists.txt**.
- Hozzon létre nevű **parson**.

Másolja a fájlokat **parson.c** és **parson.h** Parson összetevőtárházat be helyi másolatát a **távoli\_figyelési/parson** mappa.

Egy szövegszerkesztőben nyissa meg a **távoli\_monitoring.c** fájlt. Adja hozzá a következő `#include`-utasításokat:
   
```
#include "iothubtransportmqtt.h"
#include "schemalib.h"
#include "iothub_client.h"
#include "serializer_devicetwin.h"
#include "schemaserializer.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"
#include "parson.h"
```

[!INCLUDE [iot-suite-v1-connecting-code](../../includes/iot-suite-v1-connecting-code.md)]

## <a name="call-the-remotemonitoringrun-function"></a>A távoli eljáráshívás\_figyelési\_függvény futtatása
Egy szövegszerkesztőben nyissa meg a **remote_monitoring.h** fájlt. Adja hozzá a következő kódot:

```
void remote_monitoring_run(void);
```

Egy szövegszerkesztőben nyissa meg a **main.c** fájlt. Adja hozzá a következő kódot:

```
#include "remote_monitoring.h"

int main(void)
{
    remote_monitoring_run();

    return 0;
}
```

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása
Az alábbi lépések bemutatják, hogyan használható *CMake* hozhat létre az ügyfélalkalmazást.

1. Egy szövegszerkesztőben nyissa meg a **CMakeLists.txt** fájlt a **remote_monitoring** mappa.

1. Az alábbi utasítások segítségével meghatározhatja, hogyan hozható létre az ügyfél-alkalmazás hozzáadása:
   
    ```
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
1. Az a **remote_monitoring** mappa, hozzon létre egy mappát tárolásához a *ellenőrizze* CMake hoz létre fájlokat, majd futtassa a **cmake** és **győződjön** parancsok az alábbiak szerint:
   
    ```
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Az ügyfélalkalmazás futtatása, és telemetriai adatokat küldhet az IoT hubhoz:
   
    ```
    ./sample_app
    ```

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

