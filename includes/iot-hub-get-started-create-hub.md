## <a name="create-an-iot-hub"></a>IoT Hub létrehozása
Hozzon létre egy IoT Hubot, amelyhez csatlakozhat a szimulált eszközalkalmazás. Az alábbi lépések bemutatják, hogyan hajthatja végre ezt a feladatot az Azure Portal segítségével.

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Most, hogy létrehozta az IoT-központ, keresse meg az eszközök és alkalmazások számára az IoT hub kapcsolódni a fontos információkról. 

1. Az IoT Hub sikeres létrehozása után kattintson az Azure Portalon az IoT Hub új csempéjére a hozzá tartozó Tulajdonságok ablak megnyitásához. Jegyezze fel az **állomásnevet**, majd kattintson a **Megosztott elérési házirendek** elemre.
   
    ![Új IoT Hub ablak][4]
1. A **Megosztott elérési házirendek** panelen kattintson az **iothubowner** házirendre, majd másolja ki és jegyezze fel az **iothubowner** ablakban látható IoT Hub kapcsolati karakterláncot. További információkért tekintse meg az IoT Hub fejlesztői útmutató [hozzáférés-vezérléssel][lnk-access-control] foglalkozó részét.
   
    ![Megosztott elérési házirendek][5]

<!-- Images. -->
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
