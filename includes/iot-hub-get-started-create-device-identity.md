## <a name="create-a-device-identity"></a>Eszközidentitás létrehozása

Ebben a szakaszban egy Node.js nevű eszközt használhat [IOT hubbal-explorer] [ iot-hub-explorer] egy eszközidentitás létrehozása ehhez az oktatóanyaghoz. Az eszközazonosítókban különbözőnek számítanak a kis- és nagybetűk.

1. Futtassa a következő parancssori környezetében:

    `npm install -g iothub-explorer@latest`

1. Ezután futtassa a következő parancs futtatásával jelentkezzen be a központ. Helyettesítő `{iot hub connection string}` és az IoT-központ korábban kimásolt kapcsolati karakterláncot:

    `iothub-explorer login "{iot hub connection string}"`

1. Végezetül hozza létre egy új eszközidentitás nevű `myDeviceId` paranccsal:

    `iothub-explorer create myDeviceId --connection-string`

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Jegyezze fel az eszköz kapcsolati karakterlánc az eredményből. Az eszköz kapcsolati karakterlánc az eszköz alkalmazás használják az IoT Hub eszközként való kapcsolódáshoz.

![][img-identity]

Tekintse meg [Ismerkedés az IoT-központ] [ lnk-getstarted] eszköz identitások programozott módon létrehozásához.

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md
