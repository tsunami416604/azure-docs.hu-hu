## <a name="view-device-telemetry-in-the-dashboard"></a>Eszköztelemetria megtekintése az irányítópulton
A távoli figyelési megoldás irányítópultján megtekintheti az eszközök által az IoT Hubra küldött telemetriát.

1. A böngészőben térjen vissza a távoli figyelési megoldás irányítópultjára, és kattintson a bal oldali panelen az **Eszközök** elemre az **Eszközlista** megnyitásához.
2. Az **Eszközlistában** az eszköznek **Fut** állapotúnak kell lennie. Ha ez nem így lenne, kattintson az **Eszköz engedélyezése** parancsra az **Eszköz részletei** panelen.
   
    ![Eszközállapot megtekintése][18]
3. Kattintson az **Irányítópult** elemre, hogy visszatérjen az irányítópulthoz, és válassza ki az eszközt a **Megtekintendő eszköz** legördülő listában a telemetriájának megtekintéséhez. A mintaalkalmazás telemetriája 50 egység belső hőmérséklet esetén, 55 egység külső hőmérséklet esetén és 50 egység pára esetén.
   
    ![Eszköztelemetria megtekintése][img-telemetry]

## <a name="invoke-a-method-on-your-device"></a>Metódus meghívása az eszközön
A távoli figyelési megoldás irányítópultján metódusokat hívhat meg az eszközein az IoT Hubon használatával. A távoli figyelési megoldásban például egy metódust hívhat meg egy eszköz újraindításához.

1. A távoli figyelési megoldás irányítópultján kattintson a bal oldali panelen az **Eszközök** elemre az **Eszközlista** megnyitásához.
2. Kattintson az **Eszközlistában** lévő eszköz **Eszközazonosító** elemére.
3. Az **Eszköz részletei** panelen kattintson a **Metódusok** elemre.
   
    ![Eszközmetódusok][13]
4. A **Metódus** legördülő listában válassza az **InitiateFirmwareUpdate** elemet, majd a **FWPACKAGEURI** területen írjon be egy nem létező URL-címet. Kattintson a **Metódus meghívása** gombra a metódus az eszközön történő elindításához.
   
    ![Eszközmetódus meghívása][14]
   

5. Az eszközkódot futtató konzolon egy üzenetet lát, amikor az eszköz kezeli a metódust. A metódus eredményei az előzményekhez lesznek adva a megoldásportálon:

    ![Metódus előzményeinek megtekintése][img-method-history]

## <a name="next-steps"></a>Következő lépések
Az [előre konfigurált megoldások testreszabását][lnk-customize] ismertető cikkben további módokat találhat a minta kibővítésére. A lehetséges bővítmények közé tartozik a valódi érzékelők használata és további parancsok implementálása.

Az [engedélyekről többet is megtudhat az azureiotsuite.com webhelyen][lnk-permissions].

[13]: ./media/iot-suite-v1-visualize-connecting/suite4.png
[14]: ./media/iot-suite-v1-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-v1-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-v1-visualize-connecting/telemetry.png
[img-method-history]: ./media/iot-suite-v1-visualize-connecting/history.png
[lnk-customize]: ../articles/iot-suite/iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-v1-permissions.md
