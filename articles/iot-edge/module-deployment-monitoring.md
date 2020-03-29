---
title: Automatikus üzembe helyezés eszközcsoportokhoz – Azure IoT Edge | Microsoft dokumentumok
description: Az Azure IoT Edge automatikus telepítéseinek használata az eszközök csoportjainak megosztott címkék alapján történő kezeléséhez
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8aaac6100ba980301ff3e85a3ac3959bfee89b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76895965"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Az IoT Edge automatikus üzembe helyezésének ismertetése egyetlen eszközre vagy nagy méretekben

Az automatikus központi telepítések és a réteges központi telepítés segítségével nagy számú IoT Edge-eszközön kezelheti és konfigurálhatja a modulokat.

Az Azure IoT Edge két lehetőséget biztosít a modulok IoT Edge-eszközökön való futtatására. Az első módszer a modulok eszközönkénti üzembe helyezése. Hozzon létre egy központi telepítési jegyzékfájlt, majd alkalmazza azt egy adott eszközre név szerint. A második módszer a modulok automatikus üzembe helyezése bármely regisztrált eszközre, amely megfelel a meghatározott feltételeknek. Hozzon létre egy központi telepítési jegyzékfájlt, majd határozza meg, hogy mely eszközökre vonatkozik az ikereszköz [címkéi](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) alapján.

Ez a cikk az eszközök flottáinak konfigurálására és figyelésére összpontosít, amelyeket együttesen *IoT Edge automatikus központi telepítésnek neveznek.*Az alapvető telepítési lépések a következők:

1. Az operátor határozza meg a központi telepítést, amely leírja a modulok és a céleszközök készletét.Minden központi telepítés rendelkezik egy központi telepítési jegyzékfájl, amely tükrözi ezt az információt.
2. Az IoT Hub szolgáltatás kommunikál az összes célzott eszközzel a deklarált modulokkal konfigurálásukhoz.
3. Az IoT Hub-szolgáltatás lekéri az IoT Edge-eszközök állapotát, és elérhetővé teszi azokat az üzemeltető számára.Például egy operátor láthatja, ha egy Edge-eszköz nincs sikeresen konfigurálva, vagy ha egy modul futásközben meghibásodik.
4. Bármikor új IoT Edge-eszközök, amelyek megfelelnek a célzási feltételek et konfigurálva vannak a központi telepítéshez.

Ez a cikk a központi telepítés konfigurálásában és figyelésében részt vevő minden egyes összetevőt ismerteti. A központi telepítés létrehozásának és frissítésének forgatókönyve: [IoT Edge-modulok üzembe helyezése és figyelése nagy méretekben.](how-to-deploy-monitor.md)

## <a name="deployment"></a>Környezet

Az IoT Edge automatikus üzembe helyezése hozzárendeli az IoT Edge-modullemezeit az IoT Edge-eszközök célzott készletén való futtatáshoz. Úgy működik, hogy konfigurálja az IoT Edge központi telepítési jegyzékfájl modulok listáját a megfelelő inicializálási paraméterekkel.A központi telepítés hozzárendelhető egyetlen eszközhöz (az eszközazonosító alapján) vagy eszközök egy csoportjához (címkék alapján).Miután egy IoT Edge-eszköz megkapja a központi telepítési jegyzékfájl, letölti és telepíti a tárolórendszerképeket a megfelelő tárolótárolókból, és ennek megfelelően konfigurálja őket.A központi telepítés létrehozása után az operátor figyelheti a központi telepítés állapotát, és ellenőrizheti, hogy a célzott eszközök megfelelően vannak-e konfigurálva.

Csak Az IoT Edge-eszközök konfigurálhatók egy központi telepítéssel. A következő előfeltételeknek kell lenniük az eszközön, mielőtt a központi telepítést kapnák:

* Az alap operációs rendszer
* Tárolókezelő rendszer, például a Moby vagy a Docker
* Az IoT Edge-futásidejű ek kiépítése

### <a name="deployment-manifest"></a>Üzembehelyezési jegyzék

A központi telepítési jegyzékfájl egy JSON-dokumentum, amely leírja a megcélzott IoT Edge-eszközökön konfigurálandó modulokat. Az összes modul konfigurációs metaadatait tartalmazza, beleértve a szükséges rendszermodulokat (különösen az IoT Edge-ügynököt és az IoT Edge hubot).  

Az egyes modulok konfigurációs metaadatai a következőket tartalmazzák:

* Verzió
* Típus
* Állapot (például futás vagy leállítva)
* Újraindítási szabályzat
* Lemezkép- és tárolóbeállítás-nyilvántartás
* Adatbeviteli és -kimeneti útvonalak

Ha a modullemezt egy privát tároló beállításjegyzékben tárolja, az IoT Edge-ügynök rendelkezik a rendszerleíró hitelesítő adatokat.

### <a name="target-condition"></a>Célfeltétel

A célfeltétel folyamatosan kiértékelése a központi telepítés teljes időtartama alatt. A rendszer tartalmazza a követelményeknek megfelelő új eszközöket, és eltávolítja azokat a meglévő eszközöket, amelyek már nem működnek. A központi telepítés újraaktiválódik, ha a szolgáltatás észleli a célfeltétel változását.

Például van egy központi telepítését a cél feltétel tags.environment = "prod". Amikor elindítja a központi telepítést, 10 éles eszköz van. A modulok telepítése sikeresen megtörtént ebben a 10 eszközben. Az IoT Edge-ügynök állapota 10 összes eszközt, 10 sikeres választ, 0 hibaválaszt és 0 függőben lévő választ jelenít meg. Most még öt eszközt ad hozzá a tags.environment = 'prod' eszközzel. A szolgáltatás észleli a változást, és az IoT Edge-ügynök állapota lesz 15 teljes eszközök, 10 sikeres válaszok, 0 hiba válaszok és 5 függőben lévő válaszok, amíg az öt új eszközökre telepíti.

Használja a logikai feltétel az eszköz ikercímkék, az eszköz iker jelentett tulajdonságai, vagy deviceId a céleszközök kiválasztásához. Ha a feltételeket címkékkel szeretné használni, hozzá{} kell adnia a "címkéket": az ikereszköz szakaszát a tulajdonságok szintjén. [További információ az ikereszköz-címkékről](../iot-hub/iot-hub-devguide-device-twins.md)

Példák a célfeltételekre:

* deviceId ='linuxprod1'
* címkék.környezet ='prod'
* tags.environment = 'prod' ÉS tags.location = 'westus'
* tags.environment = 'prod' OR tags.location = 'westus'
* tags.operator = 'John' AND tags.environment = 'prod' NOT deviceId = 'linuxprod1'
* properties.reported.devicemodel = "4000x"

A célfeltétel összeállításakor vegye figyelembe ezeket a kényszereket:

* Az ikereszközben csak címkék, jelentett tulajdonságok vagy deviceId használatával hozhat létre célfeltételt.
* A célfeltétel egyetlen részében sem lehet dupla idézőjeleket engedélyezni. Használjon egyszeres idézőjeleket.
* Az egyszeres idézőjelek a célfeltétel értékeit jelölik. Ezért ki kell kerülnie az egyetlen idézőjelből egy másik egyidézőjellel, ha az eszköz nevének része. Például a rendszer nevű `operator'sDevice`eszköz `deviceId='operator''sDevice'`célzásához írja be a írást.
* Számok, betűk és a következő karakterek megengedettek `-:.+%_#*?!(),=@;$`a célfeltétel-értékekben: .

### <a name="priority"></a>Prioritás

A prioritás határozza meg, hogy a központi telepítést kell-e alkalmazni a célzott eszközre más központi telepítésekhez képest. A telepítési prioritás pozitív egész szám, a nagyobb számok nagyobb prioritást jelentenek. Ha egy IoT Edge-eszköz több üzembe helyezés által célzott, a legmagasabb prioritású központi telepítés érvényes.Az alacsonyabb prioritású központi telepítések nem kerülnek alkalmazásra, és nem is egyesülnek.Ha egy eszköz két vagy több, azonos prioritású központi telepítéssel van megcélozva, a legutóbb létrehozott központi telepítés (a létrehozási időbélyeg határozza meg) a következőt alkalmazza.

### <a name="labels"></a>Címkék

A címkék karakterlánckulcs-/értékpárok, amelyek segítségével szűrheti és csoportosíthatja a központi telepítéseket.Egy központi telepítés több címkével is rendelkezhet. A címkék nem kötelezőek, és nincsenek hatással az IoT Edge-eszközök tényleges konfigurációjára.

### <a name="metrics"></a>Mérőszámok

Alapértelmezés szerint az összes központi telepítés négy mérőszámról számol be:

* **Célzott** jeleníti meg az IoT Edge-eszközök, amelyek megfelelnek a központi telepítési célzási feltétel.
* **Alkalmazott** jeleníti meg a célzott IoT Edge-eszközök, amelyek nem célzott egy másik központi telepítés magasabb prioritású.
* **A sikeres jelentéskészítési** sikeres az IoT Edge-eszközök, amelyek arról számoltak be, hogy a modulok sikeresen telepítve vannak.
* **Jelentési hiba** azt mutatja, az IoT Edge-eszközök, amelyek arról számoltak be, hogy egy vagy több modul nem lett sikeresen telepítve. A hiba további kivizsgálásához csatlakozzon távolról ezekhez az eszközökhöz, és tekintse meg a naplófájlokat.

Emellett megadhatja a saját egyéni metrikákat, hogy segítsen a központi telepítés figyelése és kezelése.

A metrikák a központi telepítési konfiguráció alkalmazása során jelenthető különböző állapotok összesített számát biztosítják. A metrikák lekérdezhetik [az edgeHub modul ikerjelentett tulajdonságait,](module-edgeagent-edgehub.md#edgehub-reported-properties)például *a lastDesiredStatus* vagy *a lastConnectTime tulajdonságot.* Példa:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

A saját metrikák hozzáadása nem kötelező, és nem befolyásolja az IoT Edge-eszközök tényleges konfigurációját.

## <a name="layered-deployment"></a>Réteges telepítés

A réteges központi telepítések automatikus központi telepítések, amelyek kombinálhatók a létrehozandó egyedi telepítések számának csökkentése érdekében. A réteges központi telepítések olyan esetekben hasznosak, ahol ugyanazokat a modulokat számos automatikus központi telepítésben különböző kombinációkban használják fel újra.

A réteges központi telepítések ugyanazokaz alapvető összetevőkkel rendelkeznek, mint bármely automatikus központi telepítés. Az eszközök az ikereszközök ben lévő címkék alapján célozzák meg az eszközöket, és ugyanazokat a funkciókat biztosítják a címkék, a metrikák és az állapotjelentések körül. A réteges központi telepítések prioritásokkal is rendelkeznek, de ahelyett, hogy a prioritást használnáannak meghatározására, hogy melyik központi telepítés van alkalmazva egy eszközre, a prioritás határozza meg, hogy több központi telepítés hogyan van rangsorolva egy eszközön. Ha például két réteges központi telepítés rendelkezik egy modullal vagy egy azonos nevű útvonallal, a magasabb prioritású réteges központi telepítés lesz alkalmazva, miközben az alacsonyabb prioritás felülíródik.

A rendszer futásidejű modulok, edgeAgent és edgeHub, nincsenek konfigurálva egy réteges központi telepítés részeként. Bármely, réteges központi telepítés által megcélzott IoT Edge-eszköznek először egy szabványos automatikus üzembe helyezést kell alkalmaznia. Az automatikus központi telepítés biztosítja az alapot, amelyen réteges központi telepítések adhatók hozzá.

Egy IoT Edge-eszköz egy és csak egy szabványos automatikus üzembe helyezést alkalmazhat, de több réteges automatikus központi telepítést is alkalmazhat. Az eszközt célzó réteges központi telepítések prioritásának magasabb prioritással kell rendelkezniük, mint az adott eszköz automatikus központi telepítésének.

Vegyük például a következő forgatókönyvet egy olyan vállalatnál, amely épületeket kezel. IoT Edge modulokat fejlesztettek ki a biztonsági kamerák, mozgásérzékelők és felvonók adatainak gyűjtésére. Azonban nem minden épület használhatja mindhárom modult. A szabványos automatikus üzembe helyezésekkel a vállalatnak egyedi üzembe helyezéseket kell létrehoznia az összes olyan modulkombinációhoz, amelyre az épületeknek szüksége van.

![A szabványos automatikus üzembe helyezésekhez minden modulkombinációhoz be kell fogadni](./media/module-deployment-monitoring/standard-deployment.png)

Azonban, ha a vállalat átvált a réteges automatikus központi telepítések azt találják, hogy létrehozhatnak ugyanazokat a modulkombinációkat az épületek kevesebb központi telepítések kezelésére. Minden modul saját réteges központi telepítéssel rendelkezik, és az eszközcímkék azonosítják, hogy mely modulok kerülnek hozzáadásra az egyes épületekhez.

![A réteges automatikus központi telepítések leegyszerűsítik azokat a forgatókönyveket, amelyekben ugyanazokat a modulokat különböző módokon kombinálják](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Ikermodul konfigurációja

Ha réteges központi telepítésekkel dolgozik, előfordulhat, hogy szándékosan vagy más módon két központi telepítést is elvégezhet ugyanazzal a modullal, amelyek egy eszközt céloznak meg. Ezekben az esetekben eldöntheti, hogy a magasabb prioritású központi telepítés felülírja-e a modul iker- vagy hozzáfűzi azt. Előfordulhat például, hogy egy központi telepítés, amely ugyanazt a modult alkalmazza 100 különböző eszközökre. Ezen eszközök közül azonban 10 biztonságos létesítményben van, és további konfigurációra van szükség a proxykiszolgálókon keresztüli kommunikációhoz. A réteges központi telepítés segítségével hozzáadhat ikermodul-tulajdonságokat, amelyek lehetővé teszik, hogy a 10 eszköz biztonságosan kommunikáljon anélkül, hogy felülírná a meglévő modul ikeradatait az alap központi telepítésből.

Hozzáfűzheti a modul iker kívánt tulajdonságait a központi telepítési jegyzékben. Ahol egy szabványos központi telepítésben tulajdonságokat ad hozzá a **properties.desired** szakasza a modul iker, egy réteges központi telepítés deklarálhatja a kívánt tulajdonságok új részhalmazát.

Egy szabványos központi telepítésben például hozzáadhatja a szimulált hőmérséklet-érzékelő modult a következő kívánt tulajdonságokkal, amelyek azt mondják, hogy 5 másodperces időközönként küldjön adatokat:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Egy réteges központi telepítés, amely célja néhány vagy az összes azonos eszközök, hozzáadhat egy tulajdonságot, amely megmondja a szimulált érzékelő 1000 üzeneteket küldeni, majd állítsa le. Nem szeretné felülírni a meglévő tulajdonságokat, ezért hozzon létre egy `layeredProperties`új szakaszt a kívánt tulajdonságokon belül, amely az új tulajdonságot tartalmazza:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

A szimulált hőmérséklet-érzékelő ikermodulának következő tulajdonságait fogja tükrözni a két üzembe helyezést alkalmazó eszköz:

```json
"properties": {
  "desired": {
    "SendData": true,
    "SendInterval": 5,
    "layeredProperties": {
      "StopAfterCount": 1000
    }
  }
}
```

Ha a `properties.desired` modul ikermezőjét egy réteges központi telepítésben állítja be, felülírja az adott modul kívánt tulajdonságait bármely alacsonyabb prioritású központi telepítésben.

## <a name="phased-rollout"></a>Fokozatos bevezetés

A fokozatos bevezetés egy olyan átfogó folyamat, amelynek során egy operátor telepíti a módosításokat az IoT Edge-eszközök kiszélesítése. A cél az, hogy fokozatosan változtatásokat, hogy csökkentsék a kockázatát, hogy széles körű törés változásokat. Az automatikus üzembe helyezések segítségével kezelheti a szakaszos bevezetéseket az IoT Edge-eszközök flottájában.

A fokozatos bevezetés a következő fázisokban és lépésekben hajtható végre:

1. Hozzon létre egy tesztkörnyezetet az IoT Edge-eszközök `tag.environment='test'`kiépítése, és az eszköz ikercímke, mint például.A tesztkörnyezetnek tükröznie kell az éles környezetet, amelyet a központi telepítés végül megcéloz.
2. Hozzon létre egy központi telepítést, beleértve a kívánt modulokat és konfigurációkat. A célzási feltételnek meg kell céloznia a teszt IoT Edge-eszköz környezetben.
3. Ellenőrizze az új modulkonfigurációt a tesztkörnyezetben.
4. Frissítse a központi telepítést, hogy tartalmazza az éles IoT Edge-eszközök egy részhalmazát egy új címke hozzáadásával a célzási feltételhez. Győződjön meg arról is, hogy a központi telepítés prioritása magasabb, mint az ezen eszközökre jelenleg célzott többi központi telepítés.
5. Ellenőrizze, hogy a központi telepítés sikeres volt-e a megcélzott IoT-eszközökön a központi telepítési állapot megtekintésével.
6. Frissítse a központi telepítést az összes fennmaradó éles IoT Edge-eszközök célzásához.

## <a name="rollback"></a>Visszagurítás

A központi telepítések visszaállíthatók, ha hibákat vagy helytelen konfigurációkat kap.Mivel egy központi telepítés határozza meg az IoT Edge-eszköz abszolút modulkonfigurációját, egy további üzembe helyezést is alacsonyabb prioritású eszközre kell megcélozni, még akkor is, ha a cél az összes modul eltávolítása.  

A központi telepítés törlése nem távolítja el a modulokat a célzott eszközökről. Kell lennie egy másik központi telepítésnek, amely meghatározza az eszközök új konfigurációját, még akkor is, ha üres központi telepítésről van szó.

Visszaállításokat hajtson végre a következő sorrendben:

1. Győződjön meg arról, hogy egy második központi telepítés is ugyanarra az eszközkészletre irányul. Ha a visszaállítás célja az összes modul eltávolítása, a második központi telepítés nem tartalmazhat modulokat.
2. Módosítsa vagy távolítsa el a visszagörgetni kívánt központi telepítés célfeltétel-kifejezését, hogy az eszközök már nem felelnek meg a célzási feltételnek.
3. Ellenőrizze, hogy a visszaállítás sikeres volt-e a központi telepítés állapotának megtekintésével.
   * A visszaállított központi telepítés már nem jelenik meg a visszaállított eszközök állapota.
   * A második központi telepítésnek tartalmaznia kell a visszaállított eszközök telepítési állapotát.

## <a name="next-steps"></a>További lépések

* Az [IoT Edge-modulok nagy méretekben történő](how-to-deploy-monitor.md)telepítésével és figyelésével végigvezeti a központi telepítés lépéseit.
* További információ az IoT Edge-fogalmakról, például az [IoT Edge futásidejű](iot-edge-runtime.md) és az [IoT Edge-modulokról.](iot-edge-modules.md)
