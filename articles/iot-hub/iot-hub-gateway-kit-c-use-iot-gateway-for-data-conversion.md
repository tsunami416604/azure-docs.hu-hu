---
title: "Adatok átalakítása az IoT-átjárón Azure IoT oldala |} Microsoft Docs"
description: "Az érzékelők adatstreamének keresztül egy testreszabott modul formátum át az Azure IoT peremhálózati IoT átjáró használata."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "IOT-átjáró adatok átalakítása, iot átjáró adatok átalakítása"
ms.assetid: 75f2573d-500b-4405-bff7-61021c4c3500
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2017
ms.author: xshi
ms.openlocfilehash: d5c735a4adbc59e9526ec4fd40720c5ec136d63d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-iot-gateway-for-sensor-data-transformation-with-azure-iot-edge"></a>Az IoT-átjáró használata az érzékelő adatokat átalakításhoz Azure IoT oldala

> [!NOTE]
> Ez az oktatóanyag megkezdése előtt győződjön meg arról befejezte a következő rendelkezésre sorrendben:
> * [Az Intel NUC beállítása IoT-átjáróként](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
> * [Az IoT-átjáró használata dolgot csatlakozni a felhő - SensorTag Azure IoT hubhoz](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)

Egy egy Iot-átjáró célja, mielőtt elküldené a felhőben gyűjtött adatok feldolgozásához. Az Azure IoT peremhálózati hozható létre és az adatok feldolgozása munkafolyamat kialakításához összeállított moduloknál vezet be. Egy modul üzenetet kap, néhány műveletet végez, és majd áthelyezéssel telepítheti más modulok feldolgozni.

## <a name="what-you-learn"></a>Ismertetett témák

Megismerheti, hogyan egy modult a SensorTag üzenetek konvertálása eltérő formátumú létrehozásához.

## <a name="what-you-do"></a>Mit

* Hozzon létre egy modult a .JSON kiterjesztésű formátumba alakítsa át a fogadott üzenethez.
* A modul össze.
* A modul felvétele a BLA mintaalkalmazás Azure IoT szélétől.
* Futtassa a mintaalkalmazást.

## <a name="what-you-need"></a>Mi szükséges

* Az alábbi oktatóanyagok sorrendben végrehajtani:
  * [Az Intel NUC beállítása IoT-átjáróként](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
  * [Az IoT-átjáró használata dolgot csatlakozni a felhő - SensorTag Azure IoT hubhoz](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)
* Egy SSH-ügyfél rendszert futtató számítógépen. A puTTY Windows ajánlott. Linux- és macOS egy SSH-ügyfél már rendelkeznek.
* Az IP-cím és a felhasználónév és jelszó megadásával érheti el az átjárót az SSH-ügyfél.
* Az internethez.

## <a name="create-a-module"></a>Modul létrehozása

1. Az állomáson az SSH-ügyfél, és az IoT-átjárón.
1. A forrásfájlok a kezdőkönyvtár az IoT-átjáró a Githubról átalakítás modul klónozása a következő parancsok futtatásával:

   ```bash
   cd ~
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-nuc-gateway-customized-module.git
   ```

   Ez az Azure biztonsági a C programozási nyelven írt natív modul. A modul a következő egy alakítja át a fogadott üzenetek formátuma:

   ```json
   {"deviceId": "Intel NUC Gateway", "messageId": 0, "temperature": 0.0}
   ```

## <a name="compile-the-module"></a>A modul összeállítása

A modul fordítása, a következő parancsokat:

```bash
cd iot-hub-c-intel-nuc-gateway-customized-module/my_module
# change the build script runnable
chmod 777 build.sh
# remove the invalid windows character
sed -i -e "s/\r$//" build.sh
# run the build shell script
./build.sh
```

Kapott egy `libmy_module.so` fájlt a fordítási befejeződése után. Jegyezze fel a fájl abszolút elérési út.

## <a name="add-the-module-to-the-ble-sample-application"></a>A modul felvétele a BLA mintaalkalmazás

1. Ugrás a minták mappa a következő parancs futtatásával:

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples
   ```

1. Nyissa meg a konfigurációs fájlt a következő parancs futtatásával:

   ```bash
   vi ble_gateway.json
   ```

1. A modul hozzá lesz adva a következő kódot a beszúrásával a `modules` szakasz.

   ```json
   {
       "name": "MyModule",
       "loader": {
           "name": "native",
           "entrypoint":{
               "module.path": "[Your libmy_module.so path]"
               }
            },
        "args": null
    },
    ```

1. Cserélje le `[Your libmy_module.so path]` a kódban az abszolút elérési útja a libmy_module.so "fájl.
1. Cserélje le a kód a `links` szakasz az alábbi kódra:

   ```json
   {
       "source": "SensorTag",
       "sink": "MyModule"
   },
   {
       "source": "MyModule",
       "sink": "mapping"
   }
   ```

1. Nyomja le az `ESC`, majd írja be `:wq` fájl mentéséhez.

## <a name="run-the-sample-application"></a>Futtassa a mintaalkalmazást

1. A SensorTag bekapcsolása.
1. A SSL_CERT_FILE környezeti változó beállítása a következő parancs futtatásával:

   ```bash
   export SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt
   ```

1. Futtassa a mintaalkalmazást a hozzáadott modul a következő parancs futtatásával:

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>Következő lépések

Az IoT-átjáró segítségével a .JSON kiterjesztésű formátumba alakítsa át az üzenetet a SensorTag sikeresen is.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
