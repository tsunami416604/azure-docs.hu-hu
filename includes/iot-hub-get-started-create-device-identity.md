## <a name="create-a-device-identity"></a>Eszközidentitás létrehozása

Ebben a szakaszban egy Node.js nevű eszközt használhat [iothub-explorer] [ iot-hub-explorer] eszközidentitás létrehozása ehhez az oktatóanyaghoz. Az eszközazonosítókban különbözőnek számítanak a kis- és nagybetűk.

1. A parancssori környezetben futtassa a következőt:

    `npm install -g iothub-explorer@latest`

1. Ezután futtassa a következő parancsot a hubhoz való bejelentkezéshez. A helyettesítő `{iot hub connection string}` a korábban kimásolt az IoT Hub kapcsolati karakterláncra:

    `iothub-explorer login "{iot hub connection string}"`

1. Végül hozzon létre egy új eszközidentitást nevű `myDeviceId` a paranccsal:

    `iothub-explorer create myDeviceId --connection-string`

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Jegyezze fel az eszköz kapcsolati karakterláncát az eredményből. Az eszköz kapcsolati karakterláncát használják az eszközalkalmazás kapcsolódni az IoT hubhoz eszközként.

![][img-identity]

Tekintse meg [Ismerkedés az IoT Hub] [ lnk-getstarted] hozhat létre programozott módon eszközidentitások.

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md

[lnk-getstarted]: ../articles/iot-hub/quickstart-send-telemetry-dotnet.md
