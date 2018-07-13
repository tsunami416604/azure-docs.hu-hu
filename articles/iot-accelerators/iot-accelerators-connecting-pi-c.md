---
title: Raspberry Pi távoli figyelési C – az Azure használatával üzembe helyezése |} A Microsoft Docs
description: Ismerteti, hogyan lehet egy Raspberry Pi-eszköz csatlakoztatása a távoli figyelési megoldásgyorsító c nyelven írt alkalmazás használatával
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 23e84a8d577bb1c4950de3acd76b0f8528551ae0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611441"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>A Raspberry Pi-eszköz csatlakoztatása a távoli figyelési megoldásgyorsító (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ez az oktatóanyag bemutatja, hogyan kell egy fizikai eszköz csatlakoztatása a távoli figyelési megoldásgyorsító. Csakúgy, mint legnagyobb beágyazott korlátozott eszközökön futó alkalmazásokhoz, az ügyfélkód a Raspberry Pi eszköz alkalmazás betöltőprogramot c-hez Ebben az oktatóanyagban hozza létre a Raspbian operációs rendszert futtató az alkalmazást egy Raspberry Pi-on.

### <a name="required-hardware"></a>Szükséges hardver

Asztali számítógép ahhoz, hogy távolról csatlakozhat a parancssorban a Raspberry Pi-on.

[A Microsoft IoT-Kezdőcsomag a Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) vagy ezzel egyenértékű összetevőket. Ebben az oktatóanyagban a csomag a következő cikkeket:

- Raspberry pi 3 –
- (A NOOBS) MicroSD-kártyán
- Egy USB Mini-kábellel
- Egy Ethernet-kábelek

### <a name="required-desktop-software"></a>Szükséges asztali szoftverek

SSH-ügyfelet kell ahhoz, hogy a parancssor a Raspberry Pi-on érheti el távolról asztali gépén.

- Windows nem tartalmazza az SSH-ügyfelet. Azt javasoljuk, [PuTTY](http://www.putty.org/).
- A legtöbb Linux-disztribúciók és Mac OS tartalmazza az SSH parancssori segédprogramot. További információkért lásd: [SSH használatával Linux vagy Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Raspberry Pi szükséges szoftverek

Ez a cikk feltételezi, hogy telepítette a legújabb verzióját a [Raspbian operációs rendszer, a Raspberry Pi-on](https://www.raspberrypi.org/learning/software-guide/quickstart/).

A következő lépések bemutatják, hogyan készíti elő a Raspberry Pi a C alkalmazás, amely kapcsolódik a megoldásgyorsító létrehozásához:

1. Csatlakozzon a Raspberry Pi **ssh**. További információkért lásd: [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) a a [Raspberry Pi webhely](https://www.raspberrypi.org/).

1. A következő paranccsal a Raspberry Pi frissítése:

    ```sh
    sudo apt-get update
    ```

1. A következő parancs használatával adja hozzá a szükséges fejlesztői eszközök és kódtárak a Raspberry Pi:

    ```sh
    sudo apt-get install g++ make cmake gcc git libssl1.0-dev build-essential curl libcurl4-openssl-dev uuid-dev
    ```

1. A következő parancsok használatával töltse le, hozhat létre és telepítse az IoT Hub-ügyfélkönyvtárak a Raspberry Pi-on:

    ```sh
    cd ~
    git clone --recursive https://github.com/azure/azure-iot-sdk-c.git
    mkdir cmake
    cd cmake
    cmake ..
    make
    sudo make install
    ```

## <a name="create-a-project"></a>Projekt létrehozása

Hajtsa végre a következő lépések segítségével a **ssh** a Raspberry Pi kapcsolatot:

1. Hozzon létre egy nevű `remote_monitoring` a kezdőmappát a Raspberry Pi-on. Keresse meg a mappa az felületen:

    ```sh
    cd ~
    mkdir remote_monitoring
    cd remote_monitoring
    ```

1. Hozza létre a négy fájlokat **main.c**, **remote_monitoring.c**, **remote_monitoring.h**, és **CMakeLists.txt** a a `remote_monitoring` a mappa.

1. Egy szövegszerkesztőben nyissa meg a **remote_monitoring.c** fájlt. A Raspberry Pi-on is használhatja a **nano** vagy **vi** szövegszerkesztőben. Adja hozzá a következő `#include`-utasításokat:

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

Mentse a **remote_monitoring.c** fájlt, és zárja be a szerkesztőt.

## <a name="add-code-to-run-the-app"></a>Adja hozzá az alkalmazás futtatásához szükséges kódot

Egy szövegszerkesztőben nyissa meg a **remote_monitoring.h** fájlt. Adja hozzá a következő kódot:

```c
void remote_monitoring_run(void);
```

Mentse a **remote_monitoring.h** fájlt, és zárja be a szerkesztőt.

Egy szövegszerkesztőben nyissa meg a **main.c** fájlt. Adja hozzá a következő kódot:

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

Mentse a **main.c** fájlt, és zárja be a szerkesztőt.

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

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "/usr/local/include/azureiot")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
      serializer
      iothub_client_mqtt_transport
      umqtt
      iothub_client
      aziotsharedutil
      parson
      pthread
      curl
      ssl
      crypto
      m
    )
    ```

1. Mentse a **CMakeLists.txt** fájlt, és zárja be a szerkesztőt.

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
