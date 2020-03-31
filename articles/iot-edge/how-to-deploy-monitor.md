---
title: Modulok üzembe helyezése az Azure Portalon – Azure IoT Edge
description: Az Azure Portal használatával automatikus üzembe helyezéseket hozhat létre az IoT Edge-eszközök csoportjai számára
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a20ea4236683e26c51bc75309435c65e24271d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271433"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>IoT Edge-modulok üzembe helyezése és figyelése az Azure Portalhasználatával

Hozzon létre egy **IoT Edge automatikus üzembe helyezést** az Azure Portalon, hogy egyszerre több eszközön is kezelhet folyamatban lévő központi telepítéseket. Az IoT Edge automatikus üzembe helyezései az IoT Hub [automatikus eszközfelügyeleti](/azure/iot-hub/iot-hub-automatic-device-management) szolgáltatásának részét képezik. A központi telepítések olyan dinamikus folyamatok, amelyek lehetővé teszik több modul több eszközre történő üzembe helyezését, a modulok állapotának és állapotának nyomon követését, és szükség esetén a módosításokat.

További információ: [Az IoT Edge automatikus központi telepítései egyetlen eszközökön vagy nagy méretekben](module-deployment-monitoring.md)című témakörben található.

## <a name="identify-devices-using-tags"></a>Eszközök azonosítása címkék használatával

A központi telepítés létrehozása előtt meg kell adnia, hogy mely eszközöket szeretné befolyásolni. Az Azure IoT Edge azonosítja az eszközöket az **ikereszköz-címkék** használatával. Minden eszköz több címkével is rendelkezhet, amelyeket bármilyen módon meghatároz, amely a megoldásnak van értelme.

Ha például intelligens épületek kampuszát kezeli, hely-, szobatípus- és környezetcímkéket adhat hozzá egy eszközhöz:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Az ikereszközök és -címkék ről az [Eszközök ikreinek megértése és használata az IoT Hubban](../iot-hub/iot-hub-devguide-device-twins.md)című témakörben talál további információt.

## <a name="create-a-deployment"></a>Központi telepítés létrehozása

Az IoT Edge két különböző típusú automatikus központi telepítést biztosít, amelyek segítségével testreszabhatja a forgatókönyvet. Létrehozhat egy szabványos *központi telepítést,* amely tartalmazza a rendszer futásidejű moduljait, valamint bármely további modult és útvonalat. Minden eszköz csak egy központi telepítést alkalmazhat. Vagy létrehozhat egy *réteges központi telepítést*is, amely csak egyéni modulokat és útvonalakat tartalmaz, a rendszer futási idejét nem. Számos réteges központi telepítés kombinálható egy eszközön, egy szabványos központi telepítés en. Az automatikus telepítések két típusának együttműködéséről az [IoT Edge automatikus központi telepítéseinek ismertetése egyetlen eszközökön vagy nagy méretekben](module-deployment-monitoring.md)című témakörben talál további információt.

A központi telepítés és a réteges központi telepítés létrehozásának lépései nagyon hasonlóak. A különbségeket a következő lépések ben nevezzük el.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az IoT Hub.
1. A bal oldali ablaktábla menüjében válassza az **IoT Edge** lehetőséget az **Automatikus eszközkezelés csoportban.**
1. A felső sávon válassza a **Központi telepítés létrehozása** vagy a **Réteges telepítés létrehozása**lehetőséget.

A központi telepítés létrehozásához öt lépésből áll. A következő szakaszok végigvezetik őket.

### <a name="step-1-name-and-label"></a>1. lépés: Név és címke

1. Adjon a központi telepítésegyedi nevet, amely legfeljebb 128 kisbetűs. Kerülje a szóközöket `& ^ [ ] { } \ | " < > /`és a következő érvénytelen karaktereket: .
1. Címkéket adhat hozzá kulcs-érték párokként, hogy segítsen nyomon követni a központi telepítések. Például **HostPlatform** és **Linux**, vagy **Version** és **3.0.1**.
1. Válassza a **Tovább: A második** lépésre való lépéshez tartozó modulok lehetőséget.

### <a name="step-2-modules"></a>2. lépés: Modulok

Legfeljebb 20 modult adhat hozzá egy központi telepítéshez. Ha modulok nélküli központi telepítést hoz létre, eltávolítja az aktuális modulokat a céleszközökről.

Az üzemelő példányokban kezelheti az IoT Edge-ügynök és az IoT Edge hub-modulok beállításait. A két futásidejű modul konfigurálásához válassza a **Futásidejű beállítások** lehetőséget. A réteges központi telepítésben a futásidejű modulok nem szerepelnek, így nem konfigurálhatók.

Háromféle modult adhat hozzá:

* IoT peremhálózati modul
* Piactér modul
* Azure Stream Analytics modul

#### <a name="add-an-iot-edge-module"></a>IoT Edge modul hozzáadása

Egyéni kód modulként való hozzáadásához vagy egy Azure-szolgáltatásmodul manuális hozzáadásához kövesse az alábbi lépéseket:

1. A **lap Tárolóbeállítás-hitelesítő adatok** szakaszában adja meg a központi telepítés modullemezt tartalmazó magántároló-beállításjegyzékek nevét és hitelesítő adatait. Az IoT Edge-ügynök 500-as hibát jelent, ha nem találja a Tároló beállításjegyzék-hitelesítő adatait egy Docker-rendszerképhez.
1. A lap **IoT Edge Modulok** szakaszában kattintson a **Hozzáadás**gombra.
1. Válassza ki az **IoT Edge module** elemet a legördülő menüből.
1. Adja meg a modulnak az **IoT Edge modul nevét.**
1. A **lemezkép** URI-mezőhöz adja meg a modul tárolólemezképét.
1. Az Újraindítás házirend kiválasztásához használja a legördülő **menüt.** Az alábbi lehetőségek közül választhat:
   * **mindig** - A modul mindig újraindul, ha bármilyen okból leáll.
   * **soha** - A modul soha nem indul újra, ha bármilyen okból leáll.
   * **on-failure** - A modul újraindul, ha összeomlik, de nem, ha leáll tisztán.
   * **on-unhealthy** – A modul újraindul, ha összeomlik, vagy nem megfelelő állapotot ad vissza. Az állapotfunkció megvalósításához minden modulon múlik.
1. A legördülő menüben válassza ki a modul **kívánt állapotát.** Az alábbi lehetőségek közül választhat:
   * **futás** - A futás az alapértelmezett beállítás. A modul a telepítés után azonnal elindul.
   * **leállítva** - A telepítés után a modul tétlen marad, amíg fel nem kérik, hogy Ön vagy egy másik modul kezdje.
1. Adja meg a **tároló létrehozási beállításait,** amelyeket át kell adni a tárolónak. További információ: [docker create](https://docs.docker.com/engine/reference/commandline/create/).
1. Válassza **a Modul ikerbeállítások lehetőséget,** ha címkéket vagy más tulajdonságokat szeretne hozzáadni az ikermodulhoz.
1. Adja meg a modul **környezeti változóit.** A környezeti változók konfigurációs információkat szolgáltatnak egy modulnak.
1. Válassza a **Hozzáadás** lehetőséget, ha hozzá szeretné adni a modult a központi telepítéshez.

#### <a name="add-a-module-from-the-marketplace"></a>Modul hozzáadása a Piactérről

Ha hozzá szeretne adni egy modult az Azure Piactérről, kövesse az alábbi lépéseket:

1. A lap **IoT Edge Modulok** szakaszában kattintson a **Hozzáadás**gombra.
1. Válassza a **Piactérmodul lehetőséget** a legördülő menüből.
1. Válasszon egy **modult az IoT Edge module piactér** oldalon. A kiválasztott modul automatikusan konfigurálva van az előfizetéshez, az erőforráscsoporthoz és az eszközhöz. Ezután megjelenik az IoT Edge-modulok listájában. Egyes modulok további konfigurációt igényelhetnek. További információ: [Modulok telepítése az Azure piactérről.](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace)

#### <a name="add-a-stream-analytics-module"></a>Stream Analytics modul hozzáadása

Ha hozzá szeretne adni egy modult az Azure Stream Analytics szolgáltatásból, kövesse az alábbi lépéseket:

1. A lap **IoT Edge Modulok** szakaszában kattintson a **Hozzáadás**gombra.
1. Válassza ki az **Azure Stream Analytics modult** a legördülő menüből.
1. A jobb oldali ablaktáblában válassza ki az **Előfizetés t.**
1. Válassza ki az IoT **Edge-feladatot.**
1. Válassza a **Mentés** lehetőséget, ha hozzá szeretné adni a modult a központi telepítéshez.

#### <a name="configure-module-settings"></a>Modulbeállítások konfigurálása

Miután hozzáadott egy modult egy központi telepítéshez, kiválaszthatja a nevét az **IoT Edge Module frissítése lap** megnyitásához. Ezen az oldalon szerkesztheti a modul beállításokat, a környezeti változókat, a beállításokat és az ikermodult. Ha hozzáadott egy modult a piactérről, előfordulhat, hogy már van néhány ilyen paraméter kitöltve.

Ha egy réteges központi telepítést hoz létre, előfordulhat, hogy konfigurálja a modult, amely létezik más központi telepítések célzó ugyanazon eszközök. Ha más verziók felülírása nélkül szeretné frissíteni a **Module Twin Property** `properties.desired.settings`modul ikertestvérét, nyissa meg a Modul **ikerbeállítások** lapját. Ha csak a mezőben adja meg a `properties.desired` tulajdonságokat, felülírja az alacsonyabb prioritású központi telepítésekben definiált modul kívánt tulajdonságait.

![Modul ikertulajdonság beállítása réteges telepítéshez](./media/how-to-deploy-monitor/module-twin-property.png)

A többrétegű központi telepítések moduliker-konfigurációjáról a [Réteges telepítés című](module-deployment-monitoring.md#layered-deployment)témakörben talál további információt.

Miután konfigurálta a központi telepítés összes modulját, válassza a **Tovább: Útvonalak** a harmadik lépésre való áttérést.

### <a name="step-3-routes"></a>3. lépés: Útvonalak

Az útvonalak határozzák meg, hogy a modulok hogyan kommunikálnak egymással a központi telepítésen belül. Alapértelmezés szerint a varázsló ad egy útvonalat hívott **upstream** és a meghatározás: **FROM\* /messages/ INTO $upstream,** ami azt jelenti, hogy minden üzenet kimeneti bármely modul által küldött az IoT hub.  

Adja hozzá vagy frissítse az útvonalakat az [Útvonalak deklarálása](module-composition.md#declare-routes)című adatokkal, majd válassza a **Tovább** gombot a véleményezési szakasz folytatásához.

Válassza a **Tovább lehetőséget: Mérőszámok**.

### <a name="step-4-metrics"></a>4. lépés: Mérőszámok

A metrikák a konfigurációs tartalom alkalmazása miatt jelenthető különböző állapotok összesített számát biztosítják.

1. Adja meg a **metrikus név nevét.**

1. Adjon meg egy lekérdezést a **Metrikus feltételekhez.** A lekérdezés az IoT Edge hub modul iker [jelentett tulajdonságai](module-edgeagent-edgehub.md#edgehub-reported-properties)alapul. A metrika a lekérdezés által visszaadott sorok számát jelöli.

   Példa:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Válassza a **Tovább lehetőséget: Eszközök célzása**.

### <a name="step-5-target-devices"></a>5. lépés: Céleszközök

Használja a címkék tulajdonságot az eszközökről, hogy megcélozhatja az adott eszközöket, amelyeknek meg kell kapniuk ezt a központi telepítést.

Mivel több központi telepítés is megcélozhatja ugyanazt az eszközt, minden egyes központi telepítésnek prioritási számot kell adnia. Ha bármikor ütközik, a legmagasabb prioritású központi telepítés (a nagyobb értékek magasabb prioritást jeleznek) nyer. Ha két központi telepítés azonos prioritási számmal rendelkezik, a legutóbb létrehozott a nyer.

Ha több központi telepítés ek cél ugyanazt az eszközt, majd csak a magasabb prioritású a rendszer alkalmazza. Ha több réteges központi telepítések cél ugyanazt az eszközt, akkor mindegyik alkalmazva. Ha azonban bármely tulajdonság duplikált, például ha két azonos nevű útvonal van, akkor a magasabb prioritású rétegzett központi telepítésből származó rész felülírja a többit.

Az eszköz reklált központi telepítésének magasabb prioritással kell rendelkeznie, mint az alap központi telepítésnek ahhoz, hogy alkalmazni lehessen.

1. Adjon meg egy pozitív egész szám a központi telepítési **prioritás**.
1. Adja meg a **cél feltételt** annak meghatározásához, hogy mely eszközök lesznek megcélozva ezzel a központi telepítéssel.A feltétel az ikercímkéken vagy az ikereszköz jelentett tulajdonságain alapul, és meg kell egyeznie a kifejezés formátumának.Például `tags.environment='test'` vagy `properties.reported.devicemodel='4000x'`.

Válassza a **Tovább lehetőséget: Véleményezés + Létrehozás** lehetőséget az utolsó lépésre való továbblépéshez.

### <a name="step-6-review-and-create"></a>6. lépés: Véleményezés és létrehozás

Tekintse át a központi telepítési adatokat, majd válassza a **Létrehozás lehetőséget.**

## <a name="monitor-a-deployment"></a>Központi telepítés figyelése

A központi telepítés részleteinek megtekintéséhez és az azt futtató eszközök figyeléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg az IoT Hub.
1. Válassza az **IoT Edge lehetőséget.**
1. Válassza ki az **IoT Edge-telepítések** lapot.

   ![IoT Edge-telepítések megtekintése](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Vizsgálja meg a telepítési listát.Az egyes telepítésekhez az alábbi részletekben tekintheti meg a következőket:
   * **Azonosító** – a központi telepítés neve.
   * **Típus** - a központi telepítés típusa, vagy **központi telepítés** vagy **réteges telepítés**.
   * **Célfeltétel** – a célzott eszközök definiálásához használt címke.
   * **Prioritás** – a központi telepítéshez rendelt prioritási szám.
   * **A rendszermetrikák** - **megcélozva** adja meg az eszközök twins az IoT Hub, amelyek megfelelnek a célzási feltételnek, és az **Alkalmazott** adja meg, hogy hány eszközök, amelyek a központi telepítési tartalom az IoT Hubban a modul twins alkalmazott.
   * **Device Metrikák** – az IoT Edge-eszközök száma a központi telepítésben az IoT Edge-ügyfél futásidejű hibák jelentési száma.
   * **Egyéni metrikák** – az IoT Edge-eszközök száma a központi telepítés jelentési adatok at a központi telepítéshez megadott metrikák.
   * **Létrehozási idő** – az időbélyeg a központi telepítés létrehozásának időpontjától. Ez az időbélyeg a kapcsolatok megszakítására szolgál, ha két központi telepítés azonos prioritással rendelkezik.
1. Válassza ki a figyelni kívánt központi telepítést.  
1. Vizsgálja meg a központi telepítés részleteit. A lapok segítségével áttekintheti a központi telepítés részleteit.

## <a name="modify-a-deployment"></a>Központi telepítés módosítása

Központi telepítés módosításakor a módosítások azonnal replikálódnak az összes célzott eszközre.

Ha frissíti a célfeltételt, a következő frissítések jelennek meg:

* Ha egy eszköz nem felel meg a régi célfeltételnek, de megfelel az új célfeltételnek, és ez a központi telepítés az adott eszköz legmagasabb prioritása, akkor ez a központi telepítés az eszközre lesz alkalmazva.
* Ha egy olyan eszköz, amely jelenleg futtatja ezt a központi telepítést, már nem felel meg a célfeltételnek, eltávolítja ezt a központi telepítést, és a következő legmagasabb prioritású központi telepítést veszi igénybe.
* Ha egy olyan eszköz, amely jelenleg futtatja ezt a központi telepítést, már nem felel meg a célfeltételnek, és nem felel meg más központi telepítések célfeltételének, akkor nincs változás az eszközön. Az eszköz továbbra is fut a jelenlegi modulok a jelenlegi állapotban, de nem kezeli kebben a központi telepítés ben már nem kezelik. Miután megfelel a célfeltétel bármely más központi telepítés, eltávolítja ezt a központi telepítést, és veszi át az újat.

Központi telepítés módosításához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg az IoT Hub.
1. Válassza az **IoT Edge lehetőséget.**
1. Válassza az **IoT Edge-telepítések** lapot.

   ![IoT Edge-telepítések megtekintése](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Válassza ki a módosítani kívánt központi telepítést.
1. Frissítések et a következő lapokon:
   * **Célfeltétel**
   * **Metrikák** – módosíthatja vagy törölheti a megadott mutatókat, vagy újakat adhat hozzá.
   * **Címkék**
   * **Modulok**
   * **Útvonalak**
   * **Környezet**

1. Kattintson a **Mentés** gombra.
1. Kövesse a [Központi telepítés figyelése](#monitor-a-deployment) című részben leírt lépéseket a változások bevezetésének megtekintéséhez.

## <a name="delete-a-deployment"></a>Központi telepítés törlése

Központi telepítés törlésekor minden telepített eszköz a következő legmagasabb prioritású központi telepítést veszi igénybe. Ha az eszközök nem felelnek meg a célfeltétel bármely más központi telepítés, majd a modulok nem törlődnek, amikor a központi telepítés törlődik.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg az IoT Hub.
1. Válassza az **IoT Edge lehetőséget.**
1. Válassza az **IoT Edge-telepítések** lapot.

   ![IoT Edge-telepítések megtekintése](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. A jelölőnégyzet segítségével jelölje ki a törölni kívánt központi telepítést.
1. Válassza a **Törlés** elemet.
1. Egy kérdés tájékoztatja, hogy ez a művelet törli ezt a központi telepítést, és visszaáll az előző állapotba az összes eszközre.Ez azt jelenti, hogy egy alacsonyabb prioritású központi telepítés lesz érvényben.Ha nincs más központi telepítés célzott, nem modulok lesznek eltávolítva. Ha el szeretné távolítani az összes modult az eszközről, hozzon létre egy központi telepítést nulla modulokkal, és telepítse ugyanazokra az eszközökre.A folytatáshoz válassza az **Igen** lehetőséget.

## <a name="next-steps"></a>További lépések

További információ a [modulok IoT Edge-eszközökre való üzembe helyezéséről.](module-deployment-monitoring.md)
