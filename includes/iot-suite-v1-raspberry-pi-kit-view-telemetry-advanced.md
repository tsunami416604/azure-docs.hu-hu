## <a name="view-the-telemetry"></a>A telemetriai adatok megtekintése

A málna Pi telemetriai most küld a távoli figyelési megoldást igényelnek. A telemetriai adatokat a megoldás irányítópultján tekintheti meg. A málna Pi a megoldás irányítópultról is küldhet üzeneteket.

- Nyissa meg a megoldás irányítópultja.
- Válassza ki az eszköz a **nézet eszköz** legördülő menüből.
- A málna Pi a telemetriai adatok megjelenítése az irányítópulton.

![A málna Pi a telemetriai adatok megjelenítéséhez][img-telemetry-display]

## <a name="initiate-the-firmware-update"></a>A belső vezérlőprogram frissítésének kezdeményezése

A belső vezérlőprogram frissítési folyamat tölti le, és frissítse az eszköz ügyfélalkalmazás a málna Pi telepíti. A belső vezérlőprogram frissítési folyamattal kapcsolatos további információkért lásd a belső vezérlőprogram frissítési mintát leírását [IoT-központ az eszközkezelés áttekintése][lnk-update-pattern].

A belső vezérlőprogram frissítési folyamat kezdeményezése a metódus az eszközön. Ez a metódus aszinkron, és adja vissza, amint a frissítési folyamat megkezdése. Az eszköz a megoldás a frissítési folyamat kapcsolatban értesíteni jelentett tulajdonságokat használja.

Módszerek a málna Pi a megoldás irányítópultról a hívja meg. A málna Pi először csatlakozik a távoli felügyeleti megoldás, ha az támogatja-e a módszerekkel kapcsolatos információk küld. 

[img-telemetry-display]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced/telemetry.png
[lnk-update-pattern]: ../articles/iot-hub/iot-hub-device-management-overview.md
