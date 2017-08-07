## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

1. Az [Azure Portalon](https://portal.azure.com/) kattintson az **Új** > **Eszközök internetes hálózata** > **IoT Hub** elemre.

   ![IoT Hub létrehozása az Azure Portalon](../articles/iot-hub/media/iot-hub-create-hub-and-device/1_create-azure-iot-hub-portal.png)
2. Az **IoT Hub** panelen adja meg a következő adatokat az IoT Hub számára:

     **Név**: Adja meg az IoT Hub nevét. Ha a megadott név érvényes, egy zöld pipa jelenik meg.

     **Tarifacsomag és méret**: Válassza az **F1 – Ingyenes** szintet. Ebben a demóban elegendő ezt a beállítást megadni. További információkat a [díjszabással](https://azure.microsoft.com/pricing/details/iot-hub/) kapcsolatos lapon olvashat.

     **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot az IoT Hub üzemeltetéséhez, vagy használjon egy meglévőt. További információkat az [Azure-erőforrások erőforráscsoportokkal való kezeléséről](../articles/azure-resource-manager/resource-group-portal.md) szóló cikkben olvashat.

     **Hely**: Válassza ki azt az Önhöz legközelebbi helyet, ahol az IoT Hubot létre kívánja hozni.

     **Rögzítés az irányítópulton**: Ezt a lehetőséget kiválasztva könnyen hozzáférhet az IoT Hubhoz az irányítópultról.

   ![Az IoT Hub létrehozási adatainak megadása](../articles/iot-hub/media/iot-hub-create-hub-and-device/2_fill-in-fields-for-azure-iot-hub-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

3. Kattintson a **Create** (Létrehozás) gombra. Az IoT Hub létrehozása eltarthat néhány percig. A létrehozás folyamatát az **Értesítések** panelen követheti nyomon.

   ![Az IoT Hub folyamatértesítéseinek megtekintése](../articles/iot-hub/media/iot-hub-create-hub-and-device/3_notification-azure-iot-hub-creation-progress-portal.png)

4. Miután létrehozta az IoT Hubot, kattintson rá az irányítópulton. Jegyezze fel az **állomásnevet**, majd kattintson a **Megosztott elérési házirendek** elemre.

   ![Az IoT Hub gazdanevének beszerzése](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

5. A **Megosztott elérési házirendek** panelen kattintson az **iothubowner** szabályzatra, majd másolja ki és jegyezze fel az IoT Hub **kapcsolati karakterláncát**. További információt [az IoT Hub-hozzáférés szabályozásával](../articles/iot-hub/iot-hub-devguide-security.md) kapcsolatos cikkben találhat.

> [!NOTE] 
Ennek a beállítási oktatóanyagnak az elvégzéséhez szüksége lesz erre az iothubowner kapcsolati karakterláncra. Lehetséges azonban, hogy a beállítás elvégzése után a különböző IoT-forgatókönyvek egyes oktatóanyagaihoz is szüksége lesz rá.

   ![Az IoT Hub kapcsolati karakterláncának beszerzése](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Eszköz regisztrálása az eszközéhez az IoT Hubon

1. Az [Azure Portalon](https://portal.azure.com/) nyissa meg az IoT Hubot.

2. Kattintson a **Device Explorer** elemre.
3. A Device Explorer panelen a **Hozzáadás** gombra kattintva vehet fel eszközt az IoT Hubba. Ezután tegye a következőket:

   **Eszközazonosító**: Adja meg az új eszköz azonosítóját. Az eszközazonosítókban különbözőnek számítanak a kis- és nagybetűk.

   **Hitelesítés típusa**: Válassza a **Szimmetrikus kulcs** lehetőséget.

   **Kulcsok automatikus létrehozása**: Jelölje be ezt a jelölőnégyzetet.

   **Eszköz csatlakoztatása az IoT Hubhoz**: Kattintson az **Engedélyezés** lehetőségre.

   ![Eszköz felvétele a Device Explorerben az IoT Hubon](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-device-explorer-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Kattintson a **Save** (Mentés) gombra.
5. Az eszköz létrehozása után nyissa meg azt a **Device Explorer** panelen.
6. Jegyezze fel a kapcsolati karakterlánc elsődleges kulcsának értékét.

   ![Az eszköz kapcsolati karakterláncának beszerzése](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_get-device-connection-string-in-device-explorer-portal.png)
