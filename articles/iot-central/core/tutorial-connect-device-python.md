---
title: Oktatóanyag – általános Python-ügyfélalkalmazás összekötése az Azure IoT Central-vel | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan lehet egy eszköz fejlesztőként csatlakozni egy Python-ügyfélprogramot futtató eszközhöz az Azure IoT Central-alkalmazáshoz. Eszköz-sablon létrehozása eszköz-képességi modell importálásával és olyan nézetek hozzáadásával, amelyek lehetővé teszik a csatlakoztatott eszköz használatát
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: devx-track-python
ms.openlocfilehash: b79dce7755d6fab633a442839ae3ff8643d0b554
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876699"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>Oktatóanyag: ügyfélalkalmazás létrehozása és összekötése az Azure IoT Central-alkalmazással (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Ez a cikk a megoldás-építők és az eszközök fejlesztőire vonatkozik.*

Ebből az oktatóanyagból megtudhatja, hogyan csatlakozhat egy Python-ügyfélalkalmazás Azure IoT Central-alkalmazásához az eszköz fejlesztőinek. A Python-alkalmazás szimulálja a környezeti érzékelő eszköz viselkedését. A minta- _eszköz képesség modell_ használatával IoT Centralban hozhat létre egy _eszközt_ . A nézetek hozzáadásával lehetővé teheti, hogy az operátor kommunikáljon az eszközzel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Eszköz-képesség modell importálása eszköz sablonjának létrehozásához.
> * Adja hozzá az alapértelmezett és az egyéni nézeteket egy eszköz sablonhoz.
> * Tegye közzé az eszközt, és adjon hozzá egy valós eszközt a IoT Central alkalmazáshoz.
> * Hozza létre és futtassa a Python-eszköz kódját, és tekintse meg az IoT Central alkalmazáshoz való kapcsolódást.
> * Az eszközről eljuttatott szimulált telemetria megtekintése.
> * Az eszköz tulajdonságainak kezeléséhez használja a nézetet.
> * A szinkron és aszinkron parancsok meghívásával vezérelheti az eszközt.

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

* Egy Azure IoT Central-alkalmazás, amely az **egyéni alkalmazás** sablonnal lett létrehozva. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md). Az alkalmazást a 07/14/2020-es vagy későbbi, vagy azt követően kell létrehozni.
* Egy, a [Python](https://www.python.org/) 3,7-es vagy újabb verzióját futtató fejlesztési gép. A `python3 --version` parancssorban futtatva ellenőrizhető a verzió. A Python számos operációs rendszer számára elérhető. Az oktatóanyagban szereplő utasítások feltételezik, hogy a Windows-parancssorban futtatja a **python3** parancsot.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>Python-alkalmazás létrehozása

A következő lépések bemutatják, hogyan hozhat létre olyan Python-ügyfélalkalmazás, amely az alkalmazáshoz hozzáadott valódi eszközhöz csatlakozik. Ez a Python-alkalmazás szimulálja egy valós eszköz viselkedését.

1. A parancssori környezetben navigáljon a `environmental-sensor` korábban létrehozott mappára.

1. A szükséges kódtárak telepítéséhez futtassa a következő parancsokat:

    ```cmd
    pip install azure-iot-device
    ```

1. Hozzon létre egy **environmental_sensor.** -es nevű fájlt a `environmental-sensor` mappában.

1. Adja hozzá a következő `import` utasításokat a **environmental_sensor.** a fájl elejéhez:

    ```python
    import asyncio
    import os
    import json
    import datetime
    import random

    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import MethodResponse
    from azure.iot.device import Message
    ```

1. Adja hozzá a következő aszinkron `main` függvényt és változó deklarációkat a fájlhoz:

    ```python
    async def main():
      # In a production environment, don't store
      # connection information in the code.
      provisioning_host = 'global.azure-devices-provisioning.net'
      id_scope = '{your Scope ID}'
      registration_id = '{your Device ID}'
      symmetric_key = '{your Primary Key}'

      delay = 2

      # All the remaining code is nested within this main function

    if __name__ == '__main__':
    asyncio.run(main())
    ```

    Frissítse a helyőrzőket `{your Scope ID}` , `{your Device ID}` és a `{your Primary Key}` korábban megjegyzett értékekkel. Egy valós alkalmazásban ne jegyezze fel ezeket az információkat az alkalmazásban.

    A függvényen belül a következő függvények összes definíciója és kódja beágyazva van `main` .

1. Adja hozzá az alábbi két függvényt a `main` függvényben az eszköz regisztrálásához és a IoT Central alkalmazáshoz való csatlakoztatásához. A regisztráció az Azure Device kiépítési szolgáltatást használja:

    ```python
      async def register_device():
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
          provisioning_host=provisioning_host,
          registration_id=registration_id,
          id_scope=id_scope,
          symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print(f'Registration result: {registration_result.status}')

        return registration_result

      async def connect_device():
        device_client = None
        try:
          registration_result = await register_device()
          if registration_result.status == 'assigned':
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
              symmetric_key=symmetric_key,
              hostname=registration_result.registration_state.assigned_hub,
              device_id=registration_result.registration_state.device_id,
            )
            # Connect the client.
            await device_client.connect()
            print('Device connected successfully')
        finally:
          return device_client
    ```

1. Adja hozzá a következő függvényt a `main` függvényen belül, hogy telemetria küldjön a IoT Central alkalmazásnak:

    ```python
      async def send_telemetry():
        print(f'Sending telemetry from the provisioned device every {delay} seconds')
        while True:
          temp = random.randrange(1, 75)
          humid = random.randrange(30, 99)
          payload = json.dumps({'temp': temp, 'humid': humid})
          msg = Message(payload)
          await device_client.send_message(msg, )
          print(f'Sent message: {msg}')
          await asyncio.sleep(delay)
    ```

    A telemetria elemek nevének ( `temp` és `humid` ) meg kell egyeznie az eszköz sablonjában használt nevekkel.

1. Adja hozzá a következő függvényeket a `main` függvényen belül a IoT Central alkalmazás által hívott parancsok kezeléséhez:

    ```python
      async def blink_command(request):
        print('Received synchronous call to blink')
        response = MethodResponse.create_from_method_request(
          request, status = 200, payload = {'description': f'Blinking LED every {request.payload} seconds'}
        )
        await device_client.send_method_response(response)  # send response
        print(f'Blinking LED every {request.payload} seconds')

      async def diagnostics_command(request):
        print('Starting asynchronous diagnostics run...')
        response = MethodResponse.create_from_method_request(
          request, status = 202
        )
        await device_client.send_method_response(response)  # send response
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Generating diagnostics...')
        await asyncio.sleep(2)
        print('Sending property update to confirm command completion')
        await device_client.patch_twin_reported_properties({'rundiagnostics': {'value': f'Diagnostics run complete at {datetime.datetime.today()}.'}})

      async def turnon_command(request):
        print('Turning on the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      async def turnoff_command(request):
        print('Turning off the LED')
        response = MethodResponse.create_from_method_request(
          request, status = 200
        )
        await device_client.send_method_response(response)  # send response

      commands = {
        'blink': blink_command,
        'rundiagnostics': diagnostics_command,
        'turnon': turnon_command,
        'turnoff': turnoff_command,
      }

      # Define behavior for handling commands
      async def command_listener():
        while True:
          method_request = await device_client.receive_method_request()  # Wait for commands
          await commands[method_request.name](method_request)
    ```

    A parancsok nevének (,, `blink` , `turnon` `turnoff` és `rundiagnostics` ) meg kell egyeznie az eszköz sablonjában használt nevekkel.

    A IoT Central jelenleg nem használja az eszköz-képesség modellben definiált válasz sémát. A szinkron parancsok esetében a válasz hasznos JSON lehet. Egy aszinkron parancs esetében az eszköznek azonnal egy 202-es választ kell visszaadnia, amelyet a rendszer a munka befejeződése után jelentett a tulajdonságok frissítése után. A jelentett tulajdonság frissítésének formátuma:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Az operátor megtekintheti a válasz adattartalmát a parancs előzményeiben.

1. Adja hozzá a következő függvényeket a `main` függvényen belül a IoT Central alkalmazásból eljuttatott tulajdonságok kezeléséhez. Az az üzenet, amelyet az eszköz az [írható tulajdonság frissítésére](concepts-telemetry-properties-commands.md#writeable-property-types) válaszként küld, tartalmaznia kell a `av` és a `ac` mezőket. A `ad` mező megadása nem kötelező:

    ```python
      async def name_setting(value, version):
        await asyncio.sleep(1)
        print(f'Setting name value {value} - {version}')
        await device_client.patch_twin_reported_properties({'name' : {'value': value, 'ad': 'completed', 'ac': 200, 'av': version}})

      async def brightness_setting(value, version):
        await asyncio.sleep(5)
        print(f'Setting brightness value {value} - {version}')
        await device_client.patch_twin_reported_properties({'brightness' : {'value': value, 'ad': 'completed', 'ac': 200, 'av': version}})

      settings = {
        'name': name_setting,
        'brightness': brightness_setting
      }

      # define behavior for receiving a twin patch
      async def twin_patch_listener():
        while True:
          patch = await device_client.receive_twin_desired_properties_patch() # blocking
          to_update = patch.keys() & settings.keys()
          await asyncio.gather(
            *[settings[setting](patch[setting], patch['$version']) for setting in to_update]
          )
    ```

    Ha a kezelő egy írható tulajdonságot állít be a IoT Central alkalmazásban, az alkalmazás egy, a kívánt eszközhöz tartozó, a kívánt tulajdonságot használva küldi el az értéket az eszköznek. Az eszköz ezután válaszol a Device Twin jelentett tulajdonság használatával. Ha a IoT Central megkapja a jelentett tulajdonság értékét, akkor a rendszer **szinkronizált**állapottal frissíti a tulajdonság nézetét.

    A tulajdonságok nevének ( `name` és `brightness` ) meg kell egyeznie az eszköz sablonjában használt nevekkel.

1. Adja hozzá a következő függvényeket a `main` függvényen belül az alkalmazás vezérléséhez:

    ```python
      # Define behavior for halting the application
      def stdin_listener():
        while True:
          selection = input('Press Q to quit\n')
          if selection == 'Q' or selection == 'q':
            print('Quitting...')
            break

      device_client = await connect_device()

      if device_client is not None and device_client.connected:
        print('Send reported properties on startup')
        await device_client.patch_twin_reported_properties({'state': 'true', 'processorArchitecture': 'ARM', 'swVersion': '1.0.0'})
        tasks = asyncio.gather(
          send_telemetry(),
          command_listener(),
          twin_patch_listener(),
        )

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)

        # Wait for user to indicate they are done listening for method calls
        await user_finished

        # Cancel tasks
        tasks.add_done_callback(lambda r: r.exception())
        tasks.cancel()
        await device_client.disconnect()

      else:
        print('Device could not connect')
    ```

1. Mentse a **environmental_sensor.** a fájlt.

## <a name="run-your-python-application"></a>Python-alkalmazás futtatása

Az eszköz ügyfélalkalmazás elindításához futtassa a következő parancsot a parancssori környezetben:

```cmd
python3 environmental_sensor.py
```

Láthatja, hogy az eszköz csatlakozik az Azure IoT Central-alkalmazáshoz, és elkezdi elküldeni a telemetria:

![Az ügyfélalkalmazás futtatása](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Láthatja, hogy az eszköz hogyan válaszol a parancsokra és a tulajdonságokra:

![Az ügyfélalkalmazás megfigyelése](media/tutorial-connect-device-python/run-application-2.png)

## <a name="view-raw-data"></a>Nyers adattárolók megtekintése

[!INCLUDE [iot-central-monitor-environmental-sensor-raw-data](../../../includes/iot-central-monitor-environmental-sensor-raw-data.md)]

## <a name="next-steps"></a>Következő lépések

Most, hogy már megismerte, hogyan hozhat létre egy eszközt a Python használatával, néhány javasolt következő lépés:

* Olvassa el a [Mi az eszközök sablonjai?](./concepts-device-templates.md) további információ az eszközök sablonjainak szerepéről az eszköz kódjának megvalósításakor.
* Olvassa el az [Azure IoT Centralhoz való csatlakozást](./concepts-get-connected.md) ismertető témakört, amelyből megtudhatja, hogyan regisztrálhat eszközöket a IoT Central, és hogyan IoT Central biztonságossá teszi az eszközök kapcsolatait.

Ha továbbra is szeretne IoT Central oktatóanyagokat használni, és többet szeretne megtudni egy IoT Central megoldás létrehozásáról, tekintse meg a következő témakört:

> [!div class="nextstepaction"]
> [Átjáró-eszközsablon létrehozása](./tutorial-define-gateway-device-type.md)
