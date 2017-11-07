## <a name="view-the-solution-dashboard"></a>A megoldás irányítópultjának megtekintése

A megoldás irányítópultján kezelheti az üzembe helyezett megoldást. Például telemetriai adatainak megtekintése, eszközöket, és lehetőség módszerek meghívására.

1. Amikor a kiépítés befejeződött, és az előre konfigurált megoldás csempéje **Kész** állapotot jelez, válassza az **Indítás** gombot a távoli figyelési portál új lapon való megnyitásához.

    ![Az előre konfigurált megoldás indítása][img-launch-solution]

1. Alapértelmezés szerint a megoldás portálja az *irányítópultot* jeleníti meg. Nyissa meg egyéb területein a megoldás portálra a lap bal oldalán a menü segítségével.

    ![Az előre konfigurált távoli figyelési megoldás irányítópultja][img-menu]

## <a name="add-a-device"></a>Eszköz hozzáadása

Ahhoz, hogy egy eszköz előre konfigurált megoldáshoz csatlakozhasson, érvényes hitelesítő adatokkal kell azonosítania magát az IoT Hubon. A megoldás irányítópultjáról kérheti le az eszköz hitelesítő adatait. Az oktatóanyagban később megadja az eszköz hitelesítő adatait az ügyfélalkalmazásban.

Ha eszközt szeretne hozzáadni a távoli figyelési megoldáshoz, végezze el a következő lépéseket a megoldás irányítópultján:

1. Az irányítópult bal alsó részén kattintson az **Eszköz hozzáadása** parancsra.

   ![Eszköz hozzáadása][1]

1. Az **Egyéni eszköz** panelen kattintson az **Új hozzáadása** parancsra.

   ![Egyéni eszköz hozzáadása][2]

1. Válassza a **Meghatározom a saját eszközazonosítómat** elemet. Adja meg például az Eszközazonosítót **device01**, kattintson a **ellenőrizze azonosító** ellenőrzése a név már nem a megoldásban használt, és kattintson **létrehozása** az eszköz kiépítése.

   ![Eszközazonosító hozzáadása][3]

1. Jegyezze fel a hitelesítő adatai (**Eszközazonosító**, **IoT Hub állomásnév**, és **eszközkulcs**). Az ügyfélalkalmazás az Intel NUC kell kapcsolódni a távoli felügyeleti megoldás ezeket az értékeket. Ezután kattintson a **Done** (Kész) gombra.

    ![Eszköz hitelesítő adatainak megtekintése][4]

1. Válassza ki az eszközt a megoldás irányítópultján lévő eszközlistából. Ezután az **Eszköz részletei** panelen kattintson az **Eszköz engedélyezése** parancsra. Az eszköz mostantól **Fut** állapotú. A távoli figyelési megoldás ettől kezdve telemetriát fogadhat az eszközről és metódusokat hívhat meg az eszközön.

[img-launch-solution]: media/iot-suite-v1-gateway-kit-view-solution/launch.png
[img-menu]: media/iot-suite-v1-gateway-kit-view-solution/menu.png
[1]: media/iot-suite-v1-gateway-kit-view-solution/suite0.png
[2]: media/iot-suite-v1-gateway-kit-view-solution/suite1.png
[3]: media/iot-suite-v1-gateway-kit-view-solution/suite2.png
[4]: media/iot-suite-v1-gateway-kit-view-solution/suite3.png