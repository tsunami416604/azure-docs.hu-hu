1. Jelentkezzen be az [Azure Portalra][lnk-portal].
1. Válassza ki **hozzon létre egy erőforrást** > **az eszközök internetes hálózatát** > **IoT-központ**.
   
    ![Képernyőfelvétel az Azure portál navigációs az IoT hubhoz][1]

1. Az **IoT Hub** panelen adja meg a következő adatokat az IoT Hub számára:

   * **Név**: Adja meg az IoT Hub nevét. Ha a megadott név érvényes, egy zöld pipa jelenik meg.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   * **Tarifacsomag és méret**: Az oktatóanyaghoz válassza ki az **F1 – Ingyenes** szintet. További információkat a [díjszabással][lnk-pricing] kapcsolatos lapon olvashat.

   * **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot az IoT Hub üzemeltetéséhez, vagy használjon egy meglévőt. További információkat az [Azure-erőforrások erőforráscsoportokkal való kezeléséről][lnk-resource-groups] szóló cikkben olvashat

   * **Hely**: Válassza ki azt az Önhöz legközelebbi helyet.

   * **Rögzítés az irányítópulton**: Ezt a lehetőséget kiválasztva könnyen hozzáférhet az IoT Hubhoz az irányítópultról.

    ![IoT Hub ablak][2]

1. Kattintson a **Create** (Létrehozás) gombra. Az IoT Hub létrehozása eltarthat néhány percig. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.
<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md