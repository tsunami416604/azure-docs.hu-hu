## <a name="view-the-telemetry"></a>A telemetriai adatok megtekintése

A málna Pi telemetriai most küld a távoli figyelési megoldást igényelnek. A telemetriai adatokat a megoldás irányítópultján tekintheti meg. A málna Pi a megoldás irányítópultról is küldhet üzeneteket.

- Nyissa meg a megoldás irányítópultja.
- Válassza ki az eszköz a **nézet eszköz** legördülő menüből.
- A málna Pi a telemetriai adatok megjelenítése az irányítópulton.

![A málna Pi a telemetriai adatok megjelenítéséhez][img-telemetry-display]

## <a name="act-on-the-device"></a>Az eszközön működésre

A megoldás irányítópultról módszerek hívhatók meg a málna Pi. A málna Pi csatlakozik a távoli felügyeleti megoldás, ha az támogatja-e a módszerekkel kapcsolatos információk küld.

- A megoldás irányítópulton kattintson **eszközök** és látogasson el a **eszközök** lap. Válassza ki a Raspberry Pi a a **eszközlista**. Válassza a **módszerek**:

    ![Az irányítópult eszközök][img-list-devices]

- Az a **metódus meghívása** lapon, válassza ki **LightBlink** a a **metódus** legördülő menüből.

- Válasszon **InvokeMethod**. A Pi málna villanás több alkalommal csatlakozik a LED-jét. Az alkalmazás a málna Pi nyugtázás visszaküldi a megoldás irányítópultja:

    ![Módszer előzmények megjelenítése][img-method-history]

- Megváltoztathatja a LED be és ki használja a **ChangeLightStatus** metódust egy **LightStatusValue** beállítása **1** a a vagy **0** a ki.

> [!WARNING]
> Ha nem adja meg a távoli figyelési megoldást igényelnek fut az Azure-fiókjával, a futtatásakor a kell fizetni. További információ a felhasználás csökkentése a távoli felügyeleti megoldás futtatása közben: [konfigurálása Azure IoT Suite megoldások bemutató céljára előre konfigurált][lnk-demo-config]. Ha befejezte, használja az előkonfigurált megoldás törlése az Azure-fiókjával.


[img-telemetry-display]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry/telemetry.png
[img-list-devices]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry/listdevices.png
[img-method-history]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md