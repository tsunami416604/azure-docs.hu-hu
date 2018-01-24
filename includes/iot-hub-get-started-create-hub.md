## <a name="create-an-iot-hub"></a>IoT Hub létrehozása
Hozzon létre egy IoT Hubot, amelyhez csatlakozhat a szimulált eszközalkalmazás. Az alábbi lépések bemutatják, hogyan hajthatja végre ezt a feladatot az Azure Portal segítségével.

1. Jelentkezzen be az [Azure Portalra][lnk-portal].

1. Válassza az **Új** > **Eszközök internetes hálózata** > **IoT Hub** elemet.
   
    ![Azure Portal – ugrósáv][1]

1. Az **IoT Hub** panelen adja meg a következő adatokat az IoT Hub számára:

   * **Név**: Adja meg az IoT Hub nevét. Ha a megadott név érvényes, egy zöld pipa jelenik meg.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   * **Tarifacsomag és méret**: Az oktatóanyaghoz válassza ki az **F1 – Ingyenes** szintet. További információkat a [díjszabással][lnk-pricing] kapcsolatos lapon olvashat.

   * **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot az IoT Hub üzemeltetéséhez, vagy használjon egy meglévőt. További információkat az [Azure-erőforrások erőforráscsoportokkal való kezeléséről][lnk-resource-groups] szóló cikkben olvashat

   * **Hely**: Válassza ki azt az Önhöz legközelebbi helyet.

   * **Rögzítés az irányítópulton**: Ezt a lehetőséget kiválasztva könnyen hozzáférhet az IoT Hubhoz az irányítópultról.

    ![IoT Hub ablak][2]

1. Kattintson a **Create** (Létrehozás) gombra. Az IoT Hub létrehozása eltarthat néhány percig. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.

1. Az IoT Hub sikeres létrehozása után kattintson az Azure Portalon az IoT Hub új csempéjére a hozzá tartozó Tulajdonságok ablak megnyitásához. Most, hogy létrehozta az IoT-központ, keresse meg az eszközök és alkalmazások számára az IoT hub kapcsolódni a fontos információkról. Jegyezze fel az **állomásnevet**, majd kattintson a **Megosztott elérési házirendek** elemre.
   
    ![Új IoT Hub ablak][4]

1. A **Megosztott elérési házirendek** panelen kattintson az **iothubowner** házirendre, majd másolja ki és jegyezze fel az **iothubowner** ablakban látható IoT Hub kapcsolati karakterláncot. További információkért tekintse meg az IoT Hub fejlesztői útmutató [hozzáférés-vezérléssel][lnk-access-control] foglalkozó részét.
   
    ![Megosztott elérési házirendek][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
