## <a name="automatic-device-management"></a>Automatikus eszközkezelés
Az Azure IoT Hub automatikus kezelés automatizálja az ismétlődő és összetett feladatok nagy eszköz flották kezelésének azok életciklusának teljes keresztül. Automatikus felügyeleti (MDM) cél az eszközök a hozzájuk tartozó tulajdonságok alapján, a kívánt konfiguráció definiálása és frissítheti az eszközeit, amikor hatókör lépnek az IoT Hub segítségével.  Áll [automatikus eszközkonfigurációk](../articles/iot-hub/iot-hub-auto-device-config.md) és [automatikus IoT Edge-telepítések](../articles/iot-edge/how-to-deploy-monitor.md).

## <a name="iot-edge"></a>IoT Edge
Az Azure IoT Edge lehetővé teszi, hogy a felhőalapú fejlesztésben és üzembe helyezése, Azure-szolgáltatások megoldásspecifikus kódját a helyi eszközökre. IoT Edge-eszközök összesítheti azokat a más számítástechnikai végrehajtásához eszközök és az analytics az adatokat a felhőbe való elküldése előtt. További információkért lásd: [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>IoT Edge-ügynök
Az IoT Edge-futtatókörnyezet üzembe helyezése és figyelése a modulok felelős a része.

## <a name="iot-edge-device"></a>IoT Edge-eszköz
IoT Edge-eszközökön az IoT Edge-modul telepítve van és vannak megjelölt **IoT Edge-eszköz** az eszköz részletei között. Ismerje meg, hogyan [üzembe helyezése az Azure IoT Edge a Linux szimulált eszközön – előzetes verzió](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-automatic-deployment"></a>IoT Edge automatikus központi telepítési
Az IoT Edge automatikus központi telepítési konfigurálja az IoT Edge-eszközök egy készletét az IoT Edge-modulok futtatásához cél készletét. Minden egyes üzembe helyezési folyamatosan biztosítható, hogy a célként megadott feltételnek megfelelő összes eszköz modulok megadott készlete futnak, még akkor is, ha új eszközök jönnek létre, vagy módosultak, hogy a célként megadott feltételnek megfelelő. Minden IoT Edge-eszköz csak fogadja a legmagasabb prioritású üzembe helyezés amelynek célfeltétel megfelel-e. Tudjon meg többet [IoT Edge automatikus központi telepítési](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>IoT Edge-manifest nasazení
Egy vagy több IoT Edge-eszközök általi modul twin(s) való üzembe helyezésének-modulok a másolni kívánt adatokat tartalmazó Json-dokumentumok, útvonalak és a kapcsolódó modul kívánt tulajdonságot.

## <a name="iot-edge-gateway-device"></a>IoT Edge-átjáróeszköz
IoT Edge-eszköz az alsóbb rétegbeli eszközök. Lehet, hogy az alsóbb rétegbeli eszköz IoT Edge vagy a nem az IoT Edge-eszköz.

## <a name="iot-edge-hub"></a>IoT Edge hub
A modul a modul-kommunikációhoz (felé az IoT Hub) felsőbb és alsóbb rétegbeli (erről az IoT Hub) felelős az IoT Edge-futtatókörnyezet részét kommunikáció. 

## <a name="iot-edge-leaf-device"></a>Levél IoT Edge-eszköz
IoT Edge-eszköz nem alsóbb rétegbeli eszközzel. 

## <a name="iot-edge-module"></a>IoT Edge-modul
Az IoT Edge-modul Docker-tároló, amely IoT Edge-eszközökre telepíti központilag is. Egy adott feladat, például egy üzenetet az eszközről tölt, egy üzenet átalakítása vagy egy üzenetet küld az IoT hub hajtja végre. Ez más modulokkal kommunikál, és adatokat küld az IoT Edge-futtatókörnyezet. [A követelmények és az eszközök IoT Edge-modulok megismerése](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>IoT Edge-modul identitás
Az IoT Hub modul eszközidentitás-jegyzékben lévő részletező létezését és a biztonsági hitelesítő adatok a modul által az edge hub vagy az IoT Hub-hitelesítésre használható egy rekord.

## <a name="iot-edge-module-image"></a>IoT Edge-modul rendszerképének
A docker-rendszerképet, amelyet az IoT Edge-futtatókörnyezet modul példányok elindítását.

## <a name="iot-edge-module-twin"></a>IoT Edge ikermodul
Json-dokumentumok állandó az IoT hubban, amely tárolja az állapotadatokat, a modul példányhoz.

## <a name="iot-edge-priority"></a>IoT Edge prioritás
Ha két IoT Edge üzembe ugyanarra az eszközre, az üzembe helyezés, magasabb prioritású alkalmazva lesz. Ha két üzembe helyezés azonos prioritású rendelkezik, az üzembe helyezés az újabb létrehozási dátummal együtt alkalmazva lesz. Tudjon meg többet [prioritású](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>IoT Edge-futtatókörnyezet
IoT Edge-futtatókörnyezet tartalmaz mindent, ami egy IoT Edge-eszközön telepíteni kell a Microsoft osztja el. Az Edge agent, az Edge hub és az IoT Edge biztonsági démon tartalmazza.

## <a name="iot-edge-set-modules-to-a-single-device"></a>IoT Edge egy eszközre modulok beállítása
Másolja át az egyik eszközről egy IoT Edge-jegyzékfájl a tartalmat egy műveletet ' ikermodul. A mögöttes API egy általános "konfiguráció alkalmazása", amely egyszerűen fogadja bemenetként egy IoT Edge-jegyzékfájlt.

## <a name="iot-edge-target-condition"></a>IoT Edge célfeltétel
Célfeltétel IoT Edge-példányban, az eszközök ikerállapotának címkékre, például az üzembe helyezés céleszközeinek kiválasztásához bármely logikai feltétel **tag.environment éles =**. A célfeltétel a rendszer folyamatosan értékeli a követelményeknek megfelelő új eszközök, vagy távolítsa el az eszközöket, amelyek többé nem. Tudjon meg többet [feltétel cél](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)