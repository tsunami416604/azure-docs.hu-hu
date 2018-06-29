## <a name="automatic-device-management"></a>Automatikus Eszközkezelés
Automatikus kezelés az Azure IoT Hub automatizálja az ismétlődő és az összetett feladatok nagy eszköz flották kezelését a teljes egészében a életciklusának keresztül. Automatikus felügyeleti (MDM) az eszközök tulajdonságaik alapján célozza, adja meg a kívánt konfiguráció, és lehetővé teszik az IoT-központ frissítése az eszközöket, amikor a hatókör származnak.  Áll [automatikus eszközkonfigurációk](/articles/iot-hub/iot-hub-auto-device-config.md) és [IoT peremhálózati automatikus telepítések](/articles/iot-edge/how-to-deploy-monitor.md).

## <a name="iot-edge"></a>IoT Edge
Az Azure IoT peremhálózati lehetővé teszi, hogy a felhő alapú telepítési Azure-szolgáltatások és a helyszíni eszközök Megoldásfüggő kódot. IoT peremeszközök képes összesíteni az adatokat más számítástechnikai végrehajtásához eszközök és az elemzés az adatok a felhőben való elküldése előtt. További információkért lásd: [Azure IoT peremhálózati](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>Az IoT-Edge ügynök
Az IoT peremhálózati runtime telepítését és megfigyelését modulok felelős részét.

## <a name="iot-edge-device"></a>IoT Edge-eszköz
Az IoT-peremeszközök az IoT peremhálózati runtime telepítve van és vannak megjelölve, mint a **IoT peremhálózati eszköz** az eszköz részleteit. Megtudhatja, hogyan [Azure IoT peremhálózati telepítése Linux a szimulált eszköz – előzetes](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-automatic-deployment"></a>Az IoT-Edge automatikus központi telepítési
Az IoT-Edge automatikus központi telepítési konfigurálja az IoT peremeszközök futtatása az IoT-Edge modulok cél készlete. Minden központi telepítési folyamatosan biztosítja, hogy minden olyan eszköz, a cél feltételének futtatja a megadott adapterkészlet olyan modulok, még akkor is, ha új eszközök jönnek létre, vagy módosultak, hogy a célként megadott feltételnek megfelelő. Minden egyes IoT peremhálózati eszköz csak megkapja a legmagasabb prioritású központi telepítést akiknek cél állapota megfelel-e. További információ [IoT peremhálózati automatikus központi telepítési](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>Az IoT-Edge üzembe helyezési jegyzék
Egy vagy több IoT peremhálózati eszköz modul twin(s) modulok készlete telepítendő másolni kívánt adatokat tartalmazó Json-dokumentum, útvonalak és társított modul szükséges tulajdonságai.

## <a name="iot-edge-gateway-device"></a>Az IoT-peremhálózati átjáró eszköz
Az IoT-peremhálózati eszköz alárendelt eszközzel. Az alsóbb rétegbeli eszköz IoT peremhálózati vagy a nem a IoT peremhálózati eszköz lehet.

## <a name="iot-edge-hub"></a>IoT Edge hub
A modul a modul-kommunikációhoz (felé az IoT-központ) felsőbb és alsóbb rétegbeli (elhagyja az IoT-központ) felelős IoT peremhálózati futásidejű részét kommunikáció. 

## <a name="iot-edge-leaf-device"></a>Az IoT-peremhálózati levél eszköz
Az IoT peremhálózati eszköz nincs az alárendelt eszközzel. 

## <a name="iot-edge-module"></a>Az IoT-Edge modul
Az IoT-Edge modul egy Docker-tároló, amely központilag telepíthető a IoT peremeszközök. Egy adott feladat, például választásával dolgozhat fel egy eszközről egy üzenetet, egy üzenet átalakítása vagy üzenetet küld az IoT-központ hajtja végre. Egyéb modulok kommunikál, és az IoT-Edge futásidejű adatokat küld. [Követelmények és eszközök IoT peremhálózati modulok fejlesztési](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>Az IoT-Edge modul identitás
Az IoT-központ modul identitásjegyzékhez, és részletesen leírja a létezését és a biztonsági hitelesítő adatokat, amelyek egy modul egy peremhálózati központ vagy az IoT-központ szolgáltatással való hitelesítésre rekord.

## <a name="iot-edge-module-image"></a>Az IoT-Edge modul kép
A docker lemezkép, amellyel az IoT-Edge-futtatókörnyezet modul példányok elindítását.

## <a name="iot-edge-module-twin"></a>Az IoT-Edge modul iker
A Json-dokumentum őrzi meg az IoT Hub, amely tárolja az állapotadatokat, a modul példányához.

## <a name="iot-edge-priority"></a>Az IoT-Edge prioritása
Ha két IoT peremhálózati telepítés cél ugyanarra az eszközre, a központi telepítést, a nagyobb prioritású alkalmazva. Ha két központi telepítések a azonos prioritással rendelkezik, a központi telepítés létrehozása dátumnál későbbi alkalmazva. További információ [prioritás](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>IoT Edge-futtatókörnyezet
Az IoT-Edge futásidejű tartalmaz mindent, ami Microsoft ellátó IoT peremhálózati eszköz kell telepíteni. Ez magában foglalja a peremhálózati ügynök Edge hub és az IoT-Edge biztonsági démon.

## <a name="iot-edge-set-modules-to-a-single-device"></a>IoT peremhálózati egyetlen eszközt modulok beállítása
Másolja a tartalmat egy IoT peremhálózati jegyzékfájl egy eszközön művelet "modul iker. Az alapul szolgáló API egy általános "alkalmazni a konfiguráció", amely egyszerűen veszi az IoT-Edge jegyzékfájl bemenetként.

## <a name="iot-edge-target-condition"></a>Az IoT-Edge cél feltétel
IoT Edge-telepítés, cél feltétele bármely logikai feltétel jelölje be például a központi telepítés, a Céleszközök eszköz twins címkékre **tag.environment = prod**. Cél feltétel folyamatosan értékelése követelményeknek megfelelő új eszköz vagy az eszközöket, amelyek többé nem távolíthatja el. További információ [céloz feltétel](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)