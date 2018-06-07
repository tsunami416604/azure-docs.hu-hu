Az Azure IoT Edge egyik legfontosabb képessége a modulok felhőből való üzembe helyezése az IoT Edge-eszközökön. Az IoT Edge-modul egy tárolóként megvalósított végrehajtható csomag. Ebben a szakaszban üzembe helyez egy modult, amely telemetriát hoz létre a szimulált eszköz számára. 

1. Az Azure Portalon keresse meg az IoT-központot.
1. Lépjen az **IoT Edge (előzetes verzió)** részhez, és válassza ki az IoT Edge-eszközt.
1. Válassza a **Modulok beállítása** lehetőséget.
1. Válassza az **IoT Edge-modul hozzáadása** lehetőséget.
1. A **Név** mezőbe írja a következőt: `tempSensor`. 
1. A **Rendszerkép URI** mezőbe írja be a következőt: `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`. 
1. Hagyja változatlanul a többi beállítást, és válassza a **Mentés** lehetőséget.

   ![IoT Edge-modul mentése a név és a rendszerkép URI megadása után](./media/iot-edge-deploy-module/name-image.png)

1. A **Modulok hozzáadása** lépésben kattintson a **Tovább** gombra.
1. Az **Útvonalak megadása** lépésben kattintson a **Tovább** gombra.
1. A **Sablon áttekintése** lépésben kattintson a **Küldés** elemre.
1. Térjen vissza az eszköz részleteit tartalmazó oldalra, és kattintson a **Frissítés** elemre. Látható, hogy az új tempSensor modul fut az IoT Edge-futtatókörnyezettel együtt. 

   ![A tempSensor megtekintése az üzembe helyezett modulok listájában][1]

<!-- Images -->
[1]: ../articles/iot-edge/media/tutorial-simulate-device-windows/view-module.png