## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Most, hogy létrehozta az IoT-központ, keresse meg az eszközök és alkalmazások számára az IoT hub kapcsolódni a fontos információkról. 

1. Miután létrehozta az IoT Hubot, kattintson rá az irányítópulton. Jegyezze fel az **állomásnevet**, majd kattintson a **Megosztott elérési házirendek** elemre.

   ![Az IoT Hub gazdanevének beszerzése](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

1. A **Megosztott elérési házirendek** panelen kattintson az **iothubowner** szabályzatra, majd másolja ki és jegyezze fel az IoT Hub **kapcsolati karakterláncát**. További információt [az IoT Hub-hozzáférés szabályozásával](../articles/iot-hub/iot-hub-devguide-security.md) kapcsolatos cikkben találhat.

> [!NOTE] 
Ennek a beállítási oktatóanyagnak az elvégzéséhez szüksége lesz erre az iothubowner kapcsolati karakterláncra. Lehetséges azonban, hogy a beállítás elvégzése után a különböző IoT-forgatókönyvek egyes oktatóanyagaihoz is szüksége lesz rá.

   ![Az IoT Hub kapcsolati karakterláncának beszerzése](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Eszköz regisztrálása az eszközéhez az IoT Hubon

1. Az [Azure Portalon](https://portal.azure.com/) nyissa meg az IoT Hubot.

2. Kattintson a **az IoT-eszközök**.
3. Az IoT-eszközök ablaktábláján kattintson **Hozzáadás** hozzáad egy eszközt az IoT-központ számára. Ezután tegye a következőket:

   **Eszközazonosító**: Adja meg az új eszköz azonosítóját. Az eszközazonosítókban különbözőnek számítanak a kis- és nagybetűk.

   **Hitelesítés típusa**: Válassza a **Szimmetrikus kulcs** lehetőséget.

   **Kulcsok automatikus létrehozása**: Jelölje be ezt a jelölőnégyzetet.

   **Eszköz csatlakoztatása az IoT Hubhoz**: Kattintson az **Engedélyezés** lehetőségre.

   ![Az IoT-eszközök az IoT hub, az eszköz hozzáadásához](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-iot-devices-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Kattintson a **Save** (Mentés) gombra.
5. Az eszköz létrehozását követően nyissa meg az eszközt a **az IoT-eszközök** ablaktáblán.

   ![IoT-eszközök listája az IoT hubon](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_device-list-in-iot-devices-portal.png)

6. Jegyezze fel a kapcsolati karakterlánc elsődleges kulcsának értékét.

   ![Az eszköz kapcsolati karakterláncának beszerzése](../articles/iot-hub/media/iot-hub-create-hub-and-device/8_get-device-connection-string-in-iot-devices-portal.png)
