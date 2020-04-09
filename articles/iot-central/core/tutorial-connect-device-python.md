---
title: Oktatóanyag – Általános Python-ügyfélalkalmazás csatlakoztatása az Azure IoT Centralhoz | Microsoft dokumentumok
description: Ez az oktatóanyag bemutatja, hogyan, eszközfejlesztőként egy Python-ügyfélalkalmazást futtató eszköz csatlakoztatása az Azure IoT Central alkalmazáshoz. Eszközsablont úgy hoz létre, hogy importál egy eszközképességi modellt, és olyan nézeteket ad hozzá, amelyek lehetővé teszik a csatlakoztatott eszközzel való interakciót
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 493dcd71905970434dd2ab2997cfebd17b8e47ff
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891594"
---
# <a name="tutorial-create-and-connect-a-python-client-application-to-your-azure-iot-central-application-python"></a>Oktatóanyag: Python-ügyfélalkalmazás létrehozása és csatlakoztatása az Azure IoT Central alkalmazáshoz (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

Ez az oktatóanyag bemutatja, hogyan, eszközfejlesztőként egy Python-ügyfélalkalmazás csatlakoztatása az Azure IoT Central alkalmazáshoz. A Python-alkalmazás szimulálja egy környezeti érzékelő eszköz viselkedését. Egy _mintaeszköz képességmodell_ segítségével hozzon létre egy _eszközsablont_ az IoT Centralban. Az eszközsablonhoz nézeteket adhat, hogy az operátor kapcsolatba léphesse az eszközzel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Eszközképességi modell importálása eszközsablon létrehozásához.
> * Alapértelmezett és egyéni nézetek hozzáadása eszközsablonhoz.
> * Közzétehet egy eszközsablont, és hozzáadhat egy valódi eszközt az IoT Central-alkalmazáshoz.
> * Hozza létre és futtassa a Python-eszközkódot, és tekintse meg, hogy csatlakozzon az IoT Central alkalmazáshoz.
> * Tekintse meg az eszközről küldött szimulált telemetriai adatokat.
> * Az eszköz tulajdonságainak kezeléséhez használjon nézetet.
> * Az eszköz vezérléséhez hívja meg a szinkron és aszinkron parancsokat.

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

* Az **Egyéni alkalmazássablon** használatával létrehozott Azure IoT Central alkalmazás. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
* Egy [fejlesztőgép, amelyen telepítve van](https://www.python.org/) a Python 3.7-es vagy újabb verziója. Futtathatja `python3 --version` a parancssorból, hogy ellenőrizze a verziót. A Python az operációs rendszerek széles skálájával érhető el. Az oktatóanyag utasításai feltételezik, hogy a **Python3** parancsot futtatja a Windows parancssorában.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>Python-alkalmazás létrehozása

A következő lépések bemutatják, hogyan hozhat létre egy Python-ügyfélalkalmazást, amely csatlakozik az alkalmazáshoz hozzáadott valódi eszközhöz. Ez a Python-alkalmazás egy valódi eszköz viselkedését szimulálja.

1. A parancssori környezetben keresse `environmental-sensor` meg a korábban létrehozott mappát.

1. A szükséges könyvtárak telepítéséhez futtassa a következő parancsokat:

    ```cmd
    pip install azure-iot-device
    ```

1. Hozzon létre egy **environmental_sensor.py** nevű fájlt a `environmental-sensor` mappában.

1. Adja hozzá `import` a következő állításokat a **environmental_sensor.py** fájl elején:

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

1. Adja hozzá a következő `main` aszinkron függvény- és változódeklarációkat a fájlhoz:

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

    Frissítse a `{your Scope ID}` `{your Device ID}`helyőrzőket `{your Primary Key}` és a korábban feljegyzett értékekkel. Egy valós alkalmazás, ne kemény kód ezt az információt az alkalmazásban.

    Az alábbi függvénydefiníciók és -kódok `main` a függvénybe vannak ágyazva.

1. Adja hozzá a következő `main` két funkciót a funkción belül az eszköz regisztrálásához és az IoT Central alkalmazáshoz való csatlakoztatásához. A regisztráció az Azure eszközkiépítési szolgáltatást használja:

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

1. Adja hozzá a `main` következő függvényt a függvényen belül telemetriai adatok küldéséhez az IoT Central alkalmazáshoz:

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

    A telemetriai elemek`temp` ( `humid`és ) nevének meg kell egyeznie az eszközsablonban használt nevekkel.

1. Adja hozzá a `main` következő függvényeket a függvényen belül az IoT Central alkalmazásból meghívott parancsok kezeléséhez:

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

    A parancsok`blink`( , `turnon` `turnoff`, `rundiagnostics`, és ) nevének meg kell egyeznie az eszközsablonban használt nevekkel.

    Jelenleg az IoT Central nem használja az eszközképességi modellben definiált válaszsémát. Szinkron parancs esetén a válasz hasznos adata bármely érvényes JSON lehet. Aszinkron parancs esetén az eszköznek azonnal 202-es választ kell adnia, amelyet a munka befejezésekor jelentett tulajdonságfrissítés követ. A jelentett tulajdonságfrissítés formátuma:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Az operátor megtekintheti a válasz hasznos adatát a parancselőzményekben.

1. Adja hozzá a `main` következő függvényeket a függvényen belül az IoT Central alkalmazásból küldött tulajdonságfrissítések kezeléséhez:

    ```python
        async def name_setting(value, version):
          await asyncio.sleep(1)
          print(f'Setting name value {value} - {version}')
          await device_client.patch_twin_reported_properties({'name' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

        async def brightness_setting(value, version):
          await asyncio.sleep(5)
          print(f'Setting brightness value {value} - {version}')
          await device_client.patch_twin_reported_properties({'brightness' : {'value': value['value'], 'status': 'completed', 'desiredVersion': version}})

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

    Amikor az operátor beállít egy írható tulajdonságot az IoT Central alkalmazásban, az alkalmazás egy ikereszköz kívánt tulajdonságot használ az érték elküldéséhez az eszköznek. Az eszköz ezután válaszol egy eszköz iker jelentett tulajdonság használatával. Amikor az IoT Central megkapja a jelentett tulajdonság értéket, **szinkronizálva**frissíti a tulajdonságnézetet.

    A tulajdonságok (`name` és `brightness`) nevének meg kell egyeznie az eszközsablonban használt nevekkel.

1. Adja hozzá a `main` következő funkciókat a funkción belül az alkalmazás vezérléséhez:

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
        await device_client.patch_twin_reported_properties({'state': 'true'})
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

1. Mentse a **environmental_sensor.py** fájlt.

## <a name="run-your-python-application"></a>A Python-alkalmazás futtatása

Az eszközügyfél-alkalmazás elindításához futtassa a következő parancsot a parancssori környezetben:

```cmd
python3 environmental_sensor.py
```

Láthatja, hogy az eszköz csatlakozik az Azure IoT Central alkalmazáshoz, és elkezdi a telemetriai adatok küldését:

![Az ügyfélalkalmazás futtatása](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Láthatja, hogy az eszköz hogyan reagál a parancsokra és a tulajdonságfrissítésekre:

![Figyelje meg az ügyfélalkalmazást](media/tutorial-connect-device-python/run-application-2.png)

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az eszközképességi modellekről és a saját eszközsablonok létrehozásáról, folytassa az útmutatóval:

> [!div class="nextstepaction"]
> [Új IoT-eszköztípus definiálása](./howto-set-up-template.md)
