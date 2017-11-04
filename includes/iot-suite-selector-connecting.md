> [!div class="op_single_selector"]
> * [C Windowson](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [C Linuxon](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [Node.js (általános)](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> * [Node.js Raspberry Pi-on](../articles/iot-suite/iot-suite-connecting-pi-node.md)
> * [C Raspberry Pi-on](../articles/iot-suite/iot-suite-connecting-pi-c.md)

Az oktatóanyag végrehajtása egy **hűtő** eszköz, amely a következő telemetriai adatokat küld a távoli megfigyelési [előre konfigurált megoldás](../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md):

* Hőmérséklet
* nyomás
* Páratartalom

Az egyszerűség, a kódot állít elő, a telemetriai adatok mintaértékek a **hűtő**. A minta lehetett bővíteni a valódi érzékelők csatlakozik az eszközt, és valós telemetriai adatok küldését.

A minta eszköz is:

* A megoldás platformképességei leírására elküldi a metaadatokat.
* Válaszol-e a műveletek állapotváltozás aktiválja a **eszközök** lap a megoldásban.
* A konfigurációs módosítások megválaszolja küldjön a **eszközök** lap a megoldásban.

Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](http://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Előkészületek

Mielőtt bármilyen kódot írna az eszközhöz, ki kell építenie az előre konfigurált távoli figyelési megoldást, és meg kell adnia egy új egyéni eszközt ebben a megoldásban.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Az előre konfigurált távoli figyelési megoldás kiépítése

A **hűtő** eszköz ebben az oktatóanyagban létrehozhat adatokat küld egy példányát a [távoli megfigyelési](../articles/iot-suite/iot-suite-remote-monitoring-explore.md) előre konfigurált megoldás. Ha a távoli felügyeleti előkonfigurált megoldás még nem már megtörtént, az Azure-fiókjába, lásd: [a távoli felügyeleti előkonfigurált megoldás üzembe helyezéséhez](../articles/iot-suite/iot-suite-remote-monitoring-deploy.md)

A távoli figyelési megoldás kiépítésének befejezte után kattintson az **Indítás** gombra a megoldás irányítópultjának a böngészőben történő megnyitásához.

![A megoldás irányítópultja](media/iot-suite-selector-connecting/dashboard.png)

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Az eszköz kiépítése a távoli figyelési megoldásban

> [!NOTE]
> Ha már kiépített egy eszközt a megoldásában, kihagyhatja ezt a lépést. Van szüksége a hitelesítő adatai az ügyfélalkalmazás létrehozásakor.

Ahhoz, hogy egy eszköz előre konfigurált megoldáshoz csatlakozhasson, érvényes hitelesítő adatokkal kell azonosítania magát az IoT Hubon. A megoldás kérhetnek le a hitelesítő adatai **eszközök** lap. Az oktatóanyagban később megadja az eszköz hitelesítő adatait az ügyfélalkalmazásban.

Hozzáad egy eszközt a távoli felügyeleti megoldás, végezze el az alábbi lépéseket a **eszközök** lap a megoldásban:

1. Válasszon **rendelkezés**, és válassza a **fizikai** , a **eszköztípus**:

    ![A fizikai eszköz kiépítése](media/iot-suite-selector-connecting/devicesprovision.png)

1. Adja meg **fizikai-hűtő** , az eszközazonosító. Válassza ki a **szimmetrikus kulcs** és **automatikus kulcsok létrehozása** beállítások:

    ![Válassza ki az eszköz beállítások](media/iot-suite-selector-connecting/devicesoptions.png)

Keresse meg az eszköz kell az előkonfigurált megoldás való csatlakozáskor használandó hitelesítő adatokat, navigáljon a böngészőben az Azure portálon. Jelentkezzen be az előfizetéshez.

1. Keresse meg az erőforráscsoport, amely tartalmazza a távoli felügyeleti megoldás az Azure-szolgáltatásokhoz. Az erőforráscsoport neve megegyezik a távoli figyelési megoldást igényelnek létesített rendelkezik.

1. Nyissa meg az IoT-központ az erőforráscsoportban. Válassza a **eszköz explorer**:

    ![Eszköz explorer](media/iot-suite-selector-connecting/deviceexplorer.png)

1. Válassza ki a **Eszközazonosító** a létrehozott a **eszközök** lap a távoli felügyeleti megoldás.

1. Jegyezze fel a **Eszközazonosító** és **elsődleges kulcs** értékeket. Csatlakoztassa az eszközt a megoldáshoz kód hozzáadásakor használja ezeket az értékeket.

Most már kiépített egy fizikai eszköz a távoli figyelési előre aktiválja megoldás. A következő szakaszokban, amelyek a hitelesítő adatai a megoldás való kapcsolódáshoz használ az ügyfélalkalmazás megvalósítása.

Az ügyfélalkalmazás valósítja meg a beépített **hűtő** eszközmodell. Egy előre konfigurált megoldás eszköz típusa határozza meg az alábbiakat az eszköz:

* Az eszköz jelentés készít a megoldás tulajdonságait. Például egy **hűtő** eszköz a belső vezérlőprogram és helyére vonatkozó jelent adatokat.
* A megoldás küld az eszköz a telemetriai adatok típusú. Például egy **hűtő** eszköz küld hőmérséklet, páratartalom és érték.
* A módszerek is ütemezheti a megoldásban való futtatható az eszközön. Például egy **hűtő** meg kell valósítania az eszköz **újraindítás**, **FirmwareUpdate**, **EmergencyValveRelease**, és  **IncreasePressuree** módszerek.