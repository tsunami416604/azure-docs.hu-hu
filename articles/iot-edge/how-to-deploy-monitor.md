---
title: Modulok üzembe helyezése méretekben Azure Portal-Azure IoT Edge
description: A Azure Portal használatával automatikus központi telepítéseket hozhat létre IoT Edge-eszközök csoportjaihoz
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1e15f237bddd586f81c3b04483111f7e211bfb10
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563411"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>IoT Edge-modulok üzembe helyezése és figyelése a Azure Portal használatával

Hozzon létre egy **IoT Edge automatikus központi telepítést** a Azure Portalban, hogy egyszerre több eszközön is kezelhesse a folyamatban lévő központi telepítéseket. A IoT Edge automatikus központi telepítései a IoT Hub [automatikus Eszközkezelő](/azure/iot-hub/iot-hub-automatic-device-management) funkciójának részét képezik. A központi telepítések olyan dinamikus folyamatok, amelyek lehetővé teszik több modul üzembe helyezését több eszközön, nyomon követni a modulok állapotát és állapotát, és szükség esetén módosításokat hajthat végre.

További információ: [IoT Edge automatikus központi telepítésének ismertetése egyetlen eszközön vagy nagy méretekben](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Címkék használatával azonosíthatja az eszközöket

A központi telepítés létrehozása előtt meg kell határozni, hogy mely eszközöket kívánja érinteni. A Azure IoT Edge a **címkével** rendelkező eszközöket azonosítja az eszköz Twin-ben. Minden eszköz több címkével is rendelkezhet, amelyeket bármilyen módon meghatározhat a megoldásához. 

Ha például egy intelligens épületből álló campusot kezel, akkor a hely, a szobatípus és a környezeti címkék is hozzáadhatók egy eszközhöz:

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

További információ az eszközök ikrekről és címkékről: [az eszközök összevetése és használata az IoT Hubban](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Központi telepítés létrehozása

A IoT Edge két különböző típusú automatikus központi telepítést biztosít, amelyek segítségével testre szabhatja a forgatókönyvet. Létrehozhat egy szabványos *központi telepítést*, amely magában foglalja a rendszer futásidejű moduljait, valamint a további modulokat és útvonalakat is. Minden eszköz csak egy központi telepítést alkalmazhat. Vagy létrehozhat egy *rétegzett központi telepítést*, amely csak egyéni modulokat és útvonalakat tartalmaz, nem pedig a rendszer futtatókörnyezetét. Számos rétegzett üzemelő példány kombinálható egy eszközön, egy normál üzembe helyezési lehetőségen felül. További információ arról, hogyan működik együtt az automatikus központi telepítés két típusa: [IoT Edge automatikus központi telepítések megismerése egyetlen eszközhöz vagy nagy méretekben](module-deployment-monitoring.md).

Az üzembe helyezés és a rétegzett központi telepítés létrehozásának lépései nagyon hasonlóak. A különbségeket az alábbi lépésekben hívja meg.

1. A [Azure Portal](https://portal.azure.com)lépjen a IoT hub.
1. A bal oldali ablaktábla menüjében válassza a **IoT Edge** lehetőséget az **automatikus eszközkezelés**területen.
1. A felső sávon válassza a **központi telepítés létrehozása** vagy a **rétegzett központi telepítés létrehozása**lehetőséget.

Az üzemelő példány létrehozásának öt lépése van. A következő szakasz végigvezeti a műveletet.

### <a name="step-1-name-and-label"></a>1\. lépés: név és címke

1. Adja meg az üzembe helyezést egy egyedi névvel, amely akár 128 kisbetűt is tartalmazhat. Kerülje a szóközöket, és a következő érvénytelen karaktereket: `& ^ [ ] { } \ | " < > /`.
1. A központi telepítések nyomon követéséhez kulcs-érték párokkal adhat hozzá címkéket. Például: **HostPlatform** és **Linux**, vagy **Version** és **3.0.1**.
1. Válassza a **Next (tovább): modulok** elemet a második lépésre lépéshez.

### <a name="step-2-modules"></a>2\. lépés: modulok

Akár 20 modult is hozzáadhat egy központi telepítéshez. Ha modulokat nem tartalmazó központi telepítést hoz létre, az eltávolítja a megcélzott eszközökről származó aktuális modulokat.

A központi telepítések során a IoT Edge ügynök és IoT Edge hub-modulok beállításait kezelheti. Válassza a **Futásidejű beállítások** lehetőséget a két futásidejű modul konfigurálásához. A rétegzett központi telepítésben a futásidejű modulok nincsenek megadva, ezért nem konfigurálhatók. 

Három típusú modult is hozzáadhat:

* IoT Edge modul
* Marketplace-modul
* Azure Stream Analytics modul

#### <a name="add-an-iot-edge-module"></a>IoT Edge modul hozzáadása

Egyéni kód modulként való hozzáadásához, illetve Azure-szolgáltatási modul manuális hozzáadásához kövesse az alábbi lépéseket:

1. A lap **Container Registry hitelesítő adatok** szakaszában adja meg a központi telepítés moduljának lemezképeit tartalmazó privát tároló-beállításjegyzékek nevét és hitelesítő adatait. A IoT Edge ügynök a 500-es hibát fogja jelenteni, ha nem találja a tároló beállításjegyzékének hitelesítő adatait a Docker-rendszerképhez.
1. A lap **IoT Edge modulok** szakaszában kattintson a **Hozzáadás**gombra.
1. Válassza ki **IoT Edge modult** a legördülő menüből.
1. Adja a modulnak **IoT Edge modul nevét**.
1. A **RENDSZERKÉP URI** mezőjébe írja be a modulhoz tartozó tároló rendszerképét.
1. Az **Újraindítási szabályzat**kiválasztásához használja a legördülő menüt. Az alábbi lehetőségek közül választhat:
   * **mindig** – a modul mindig újraindul, ha valamilyen okból leáll.
   * **soha** – a modul soha nem indul újra, ha bármilyen okból leáll.
   * **hiba** esetén – a modul újraindul, ha összeomlik, de nem, ha nem áll le tisztán. 
   * **sérült** állapotban – a modul újraindul, ha összeomlik vagy visszaadja a nem kifogástalan állapotot. Az állapotfigyelő funkció megvalósításához az egyes modulok is felhasználhatók. 
1. A modul **kívánt állapotának** kiválasztásához használja a legördülő menüt. Az alábbi lehetőségek közül választhat:
   * a **futó** Futtatás az alapértelmezett beállítás. A modul a telepítése után azonnal elindul.
   * **Leállítva** – az üzembe helyezés után a modul tétlen marad mindaddig, amíg meg nem hívja az Ön vagy egy másik modul indítását.
1. Adja meg a tárolóhoz átadandó **tároló-létrehozási beállításokat** . További információ: [Docker Create](https://docs.docker.com/engine/reference/commandline/create/).
1. Válassza ki a **modul Twin beállításait** , ha címkéket vagy egyéb tulajdonságokat szeretne felvenni a Twin modulba.
1. Adja meg a modul **környezeti változóit** . A környezeti változók konfigurációs adatokat biztosítanak egy modulhoz.
1. A **Hozzáadás** gombra kattintva adja hozzá a modult a központi telepítéshez.

#### <a name="add-a-module-from-the-marketplace"></a>Modul hozzáadása a piactéren

Ha modult szeretne hozzáadni az Azure piactéren, kövesse az alábbi lépéseket:

1. A lap **IoT Edge modulok** szakaszában kattintson a **Hozzáadás**gombra.
1. Válassza ki a **piactér modult** a legördülő menüből.
1. Válasszon egy modult a **IoT Edge modul Marketplace** oldaláról. A kiválasztott modul automatikusan konfigurálva van az előfizetéshez, az erőforráscsoporthoz és az eszközhöz. Ezután megjelenik a IoT Edge-modulok listájában. Egyes modulok további konfigurálást igényelhetnek. További információ: [modulok üzembe helyezése az Azure Marketplace-](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace)en.

#### <a name="add-a-stream-analytics-module"></a>Stream Analytics modul hozzáadása

Ha Azure Stream Analytics-modult szeretne hozzáadni, kövesse az alábbi lépéseket:

1. A lap **IoT Edge modulok** szakaszában kattintson a **Hozzáadás**gombra.
1. Válassza ki **Azure stream Analytics modult** a legördülő menüből.
1. A jobb oldali ablaktáblán válassza ki az **előfizetését**.
1. Válassza ki a IoT **Edge-feladatot**.
1. Válassza a **Mentés** lehetőséget, hogy hozzáadja a modult a központi telepítéshez.

#### <a name="configure-module-settings"></a>A modul beállításainak konfigurálása

Miután hozzáadta a modult egy központi telepítéshez, kiválaszthatja a nevét, és megnyithatja a **IoT Edge modul frissítése** lapot. Ezen az oldalon szerkesztheti a modul beállításait, a környezeti változókat, a létrehozási beállításokat és a modult. Ha hozzáadta a modult a piactéren, előfordulhat, hogy már rendelkezik a paraméterekkel. 

Ha többrétegű központi telepítést hoz létre, akkor lehet, hogy olyan modult konfigurál, amely az ugyanazon eszközöket célzó más központi telepítések között található. Ha a modult a többi verzió felülírása nélkül szeretné frissíteni, nyissa meg a **modul Twin Settings** fület. hozzon létre egy új **modul Twin tulajdonságot** , amely egy egyedi névvel rendelkezik egy alszakaszhoz a modul két kívánt tulajdonságában, például `properties.desired.settings`. Ha csak a `properties.desired` mezőben adja meg a tulajdonságokat, a rendszer felülírja az alacsonyabb prioritású üzemelő példányokban definiált modul kívánt tulajdonságait. 

![A modul Twin tulajdonságának beállítása rétegzett központi telepítéshez](./media/how-to-deploy-monitor/module-twin-property.png)

A többrétegű központi telepítések moduljának kettős konfigurációjával kapcsolatos további információkért lásd: [rétegzett központi telepítés](module-deployment-monitoring.md#layered-deployment).

Miután konfigurálta az összes modult a központi telepítéshez, válassza a Next (tovább) gombot **: az útvonalakat** a harmadik lépéshez kell áthelyezni.

### <a name="step-3-routes"></a>3\. lépés: útvonalak

Az útvonalak határozzák meg, hogy a modulok hogyan kommunikáljanak egymással egy központi telepítésen belül. Alapértelmezés szerint a varázsló egy **felsőbb rétegbeli** útvonalat ad meg, amelyet a **rendszer a/messages/\* a $upstreamba**, ami azt jelenti, hogy minden modulból kimenetet küld az IoT hub-nak.  

Adja hozzá vagy frissítse az útvonalakat a [deklarált útvonalak](module-composition.md#declare-routes)információi alapján, majd kattintson a **tovább** gombra a felülvizsgálati szakasz folytatásához.

Válassza a **Next (tovább): metrikák**lehetőséget.

### <a name="step-4-metrics"></a>4\. lépés: mérőszámok

A metrikák a konfigurációs tartalom alkalmazásának eredményeképpen a különböző állapotok összegzési számát adják meg.

1. Adja meg a **metrika nevének**nevét.

1. Adja meg a **mérőszám feltételeinek**lekérdezését. A lekérdezés IoT Edge hub-modul Twin [jelentett tulajdonságain](module-edgeagent-edgehub.md#edgehub-reported-properties)alapul. A metrika a lekérdezés által visszaadott sorok számát jelöli.

   Példa:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Válassza a **Tovább gombot: eszközök megcélzása**.

### <a name="step-5-target-devices"></a>5\. lépés: eszközök megcélzása

Az eszközök címkék tulajdonságával megcélozhatja azokat az eszközöket, amelyeknek a központi telepítést meg kell kapniuk.

Mivel több üzemelő példány is megcélozhatja ugyanazt az eszközt, minden egyes központi telepítésnek prioritási számot kell megadnia. Ha ütközés lép fel, a legmagasabb prioritású üzemelő példány (a nagyobb érték magasabb prioritást jelez) a WINS. Ha két üzemelő példány azonos prioritási számmal rendelkezik, akkor a legutóbb létrehozott WINS.

Ha több üzemelő példány ugyanazt az eszközt célozza meg, akkor csak a magasabb prioritású rendszer lesz alkalmazva. Ha a többrétegű üzemelő példányok ugyanazt az eszközt célozzák meg, akkor azokat alkalmazza a rendszer. Ha azonban bármely tulajdonság duplikálva van, például ha két útvonal ugyanazzal a névvel, akkor a magasabb prioritású, rétegzett központi telepítésből származó adatok felülírják a többit. 

Az eszközre irányuló összes rétegzett telepítésnek magasabb prioritással kell rendelkeznie, mint az alapszintű üzemelő példány alkalmazása. 

1. Adjon meg egy pozitív egész számot a központi telepítési **prioritáshoz**.
1. Adja meg a **cél feltételt** annak meghatározásához, hogy mely eszközök lesznek megcélozva a központi telepítéssel. A feltétel a Device Twin-címkék vagy az eszközök Twin jelentett tulajdonságain alapul, és meg kell egyeznie a kifejezés formátumával. Például `tags.environment='test'` vagy `properties.reported.devicemodel='4000x'`.

Válassza a **Next (tovább): felülvizsgálat + létrehozás** elemet az utolsó lépésre való áttéréshez.

### <a name="step-6-review-and-create"></a>6\. lépés: Áttekintés és létrehozás

Tekintse át az üzembe helyezési adatokat, majd kattintson a **Létrehozás**gombra.

## <a name="monitor-a-deployment"></a>Központi telepítés figyelése

A központi telepítés részleteinek megtekintéséhez és az azt futtató eszközök figyeléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon a IoT hub.
1. Válassza a **IoT Edge**lehetőséget.
1. Válassza a **IoT Edge központi telepítések** fület.

   ![IoT Edge üzemelő példányok megtekintése](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Vizsgálja meg a központi telepítés listáját. Az egyes központi telepítések esetében az alábbi adatokat tekintheti meg:
   * **Azonosító** – az üzemelő példány neve.
   * **Típus** – a központi telepítés típusa, vagy a **telepítés vagy a** **rétegzett telepítés**. 
   * **Cél feltétel** – a megcélzott eszközök definiálásához használt címke.
   * **Prioritás** – az üzemelő példányhoz rendelt prioritási szám.
   * A - **megcélzott** **rendszermetrikák** határozzák meg a célcsoport-kezelési feltételnek megfelelő IoT hub az ikrek számát **, valamint azt** , hogy hány eszközön lett alkalmazva a központi telepítési tartalom a IoT hub.
   * **Eszköz metrikái** – a központi telepítésben szereplő IoT Edge eszközök száma a IoT Edge ügyfél futtatókörnyezetének sikerességéről vagy hibáiról.
   * **Egyéni metrikák** – a központi telepítéshez megadott összes metrika esetében az üzembe helyezési jelentéskészítési adatok IoT Edge eszközeinek száma.
   * **Létrehozás időpontja** – a központi telepítés létrehozásának időbélyegzője. Ez az időbélyeg a kapcsolatok megszakítására szolgál, ha két üzemelő példány azonos prioritással rendelkezik.
1. Válassza ki a figyelni kívánt központi telepítést.  
1. Vizsgálja meg a központi telepítés részleteit. A fülek segítségével áttekintheti a központi telepítés részleteit.

## <a name="modify-a-deployment"></a>Központi telepítés módosítása

Amikor módosít egy központi telepítést, a módosítások azonnal replikálódnak az összes megadott eszközre.

Ha frissíti a célként megadott feltételt, a következő frissítések történnek:

* Ha egy eszköz nem felelt meg a régi célként megadott feltételnek, de megfelel az új célként megadott feltételnek, és ez a központi telepítés az eszköz legmagasabb prioritása, akkor ez a központi telepítés az eszközre lesz alkalmazva.
* Ha a központi telepítést jelenleg futtató eszköz már nem felel meg a célként megadott feltételnek, akkor eltávolítja ezt a központi telepítést, és a következő legmagasabb prioritású üzemelő példányra kerül.
* Ha a központi telepítést jelenleg futtató eszköz már nem felel meg a célként megadott feltételnek, és nem felel meg a többi üzemelő példány céljának, akkor az eszközön nem történik változás. Az eszköz továbbra is a jelenlegi állapotukban futtatja az aktuális modulokat, de az üzembe helyezés részeként már nem felügyelhető. Ha teljesíti az egyéb üzemelő példányok célját, eltávolítja ezt az üzembe helyezést, és az újat veszi.

A központi telepítés módosításához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon a IoT hub.
1. Válassza a **IoT Edge**lehetőséget.
1. Válassza a **IoT Edge központi telepítések** fület.

   ![IoT Edge üzemelő példányok megtekintése](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Válassza ki a módosítani kívánt központi telepítést.
1. A következő lapokon végezze el a frissítéseket:
   * **Cél feltétel**
   * **Metrikák** – módosíthatja vagy törölheti a definiált mérőszámokat, illetve újakat adhat hozzá.
   * **Címkék**
   * **Modulok**
   * **Útvonalak**
   * **Üzembe helyezés**

1. Kattintson a **Mentés** gombra.
1. Kövesse az [üzembe helyezés figyelése](#monitor-a-deployment) című témakör lépéseit a változások megtekintéséhez.

## <a name="delete-a-deployment"></a>Központi telepítés törlése

Ha töröl egy központi telepítést, minden telepített eszköz a következő legmagasabb prioritású üzemelő példányra kerül. Ha az eszközök nem felelnek meg az egyéb üzemelő példányok céljának, akkor a rendszer nem távolítja el a modulokat a központi telepítés törlésekor.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon a IoT hub.
1. Válassza a **IoT Edge**lehetőséget.
1. Válassza a **IoT Edge központi telepítések** fület.

   ![IoT Edge üzemelő példányok megtekintése](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. A jelölőnégyzet használatával válassza ki a törölni kívánt központi telepítést.
1. Válassza a **Törlés** elemet.
1. A rendszer értesíti arról, hogy ez a művelet törli ezt a központi telepítést, és az összes eszköz előző állapotára vált vissza.  Ez azt jelenti, hogy alacsonyabb prioritású üzemelő példány lesz érvényben. Ha nincs más központi telepítés megcélozva, a rendszer nem távolítja el a modulokat. Ha el szeretné távolítani az összes modult az eszközről, hozzon létre egy üzembe helyezést nulla modulokkal, és telepítse azt ugyanarra az eszközre. A folytatáshoz válassza az **Igen** lehetőséget.

## <a name="next-steps"></a>Következő lépések

További információ a [modulok IoT Edge eszközökön való telepítéséről](module-deployment-monitoring.md).
