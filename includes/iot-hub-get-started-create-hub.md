## IoT Hub létrehozása

Hozzon létre egy IoT Hubot, amelyhez csatlakozhat a szimulált eszköz. Az alábbi lépések bemutatják, hogyan hajthatja végre ezt a feladatot az Azure Portal segítségével.

1. Jelentkezzen be az [Azure Portalra][lnk-portal].

2. Az ugrósávon kattintson az **Új** > **Eszközök internetes hálózata** > **Azure IoT Hub** elemre.

    ![Azure Portal – ugrósáv][1]

3. Az **IoT Hub** panelen válassza ki az IoT Hub konfigurációját.

    ![IoT Hub panel][2]

    * A **Név** mezőben adja meg az IoT Hub nevét. Ha a **Név** érvényes és elérhető, egy zöld pipa jelenik meg a **Név** mezőben.
    * Válasszon egy [tarifacsomagot és méretet][lnk-pricing]. Az oktatóanyag teljesítéséhez nem kell egy konkrét csomagot kiválasztani. Az oktatóanyaghoz használja az ingyenes F1 csomagot.
    * Az **Erőforráscsoport** mezőben hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévőt. További információk: [Using Resource groups to manage your Azure resources][lnk-resource-groups] (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez).
    * A **Hely** mezőben válasszon ki egy helyet, ahol az IoT Hubot tárolni szeretné. A jelen oktatóanyag esetében válassza az Önhöz legközelebb eső helyet.

4. Az IoT Hub konfigurációs beállításainak kiválasztása után kattintson a **Létrehozás** gombra.  Az IoT Hub létrehozása eltarthat néhány percig. A folyamat állapotát a kezdőpulton vagy az értesítési panelen ellenőrizheti.

    ![Az új IoT Hub állapota][3]

5. Az IoT Hub sikeres létrehozása után kattintson a portálon az IoT Hub új csempéjére a hozzá tartozó panel megnyitásához. Jegyezze fel az **állomásnevet**, majd kattintson a **Megosztott elérési házirendek** elemre.

    ![Új IoT Hub panel][4]

6. A **Megosztott hozzáférési házirendek** panelen kattintson az **iothubowner** házirendre, majd másolja ki és jegyezze fel az **iothubowner** panelen látható kapcsolati karakterláncot. További információkért tekintse meg az „Azure IoT Hub developer guide” (Azure IoT Hub fejlesztői útmutató) [Access control][lnk-access-control] (Hozzáférés-vezérlés) című részét.

    ![Megosztott hozzáférési házirend panel][5]


<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-portal/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide.md#accesscontrol


<!--HONumber=Sep16_HO4-->


