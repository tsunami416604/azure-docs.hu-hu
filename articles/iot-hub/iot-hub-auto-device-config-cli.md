---
title: Konfigurálhatja és figyelheti az Azure IoT Hub (CLI) nagy mennyiségű IoT-eszközök |} A Microsoft Docs
description: Azure IoT Hub automatikus konfigurációk használatával több eszközt konfiguráció hozzárendelése
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: f81ef3c231874f314d6fe023ba247a0bcff61e90
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2018
ms.locfileid: "42057231"
---
# <a name="configure-and-monitor-iot-devices-at-scale-using-the-azure-cli"></a>Konfigurálhatja és figyelheti az Azure CLI használatával nagy mennyiségű IoT-eszközök

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Az Azure IoT Hub automatikus kezelés automatizálja ismétlődő és összetett feladatok nagy eszköz flották kezelésének azok életciklusának teljes keresztül. Automatikus felügyeleti (MDM) cél az eszközök a hozzájuk tartozó tulajdonságok alapján, a kívánt konfiguráció definiálása és frissítheti az eszközeit, amikor hatókör lépnek az IoT Hub segítségével.  Ez egy automatikus konfigurációs, amely is lehetővé teszi az összegzés befejezését és a megfelelőség, a leíró egyesítése és a ütközések, és megkezdik a szakaszos megközelítéssel konfigurációk használatával történik.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatikus konfigurációk munkahelyi ikereszközök készletét frissítése a kívánt tulajdonságokkal és ikereszköz alapján összefoglaló jelentés által jelentett tulajdonságokként.  Azt mutatja be egy új osztályt és nevű JSON-dokumentumok egy *konfigurációs* amely három részből áll:

* A **feltétel cél** ikereszközök frissítendő hatókörének meghatározása. A célfeltétel device twin címkékre lekérdezésként van megadva és/vagy jelentett tulajdonságokként.

* A **tartalmat** hozzáadva vagy frissítve a célzott eszközök ikerállapotának a kívánt tulajdonságok meghatározása. A tartalom elérési útját a módosítani kívánt tulajdonságok szakasza tartalmaz.

* A **metrikák** határozza meg, például konfigurációs állapotait összesítő számát **sikeres**, **folyamatban lévő**, és **hiba**. Egyéni metrikák megadott eszközön lekérdezések ikereszköz jelentett tulajdonságait.  Rendszermérőszámokat alapértelmezett mérőszámok, amelyek a ikereszköz állapotát, például az ikereszközök szánt száma és a twins, amelyek sikeresen frissítve lett-e a rendszer. 

## <a name="cli-prerequisites"></a>Parancssori felület Előfeltételek

* Egy [az IoT hub](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésében. 
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) a saját környezetében. Az Azure CLI 2.0 legalább 2.0.24-es verzióját kell használnia. A verziószámot az `az –-version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer. 
* A [IoT-bővítmény az Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="implement-device-twins-to-configure-devices"></a>Eszközök konfigurálása ikereszközök megvalósítása

Automatikus eszközkonfigurációkat állapota a felhő és az eszközök közötti szinkronizálását ikereszközök használata szükséges.  Tekintse meg [ikereszközök megismerése és használata az IoT Hub](iot-hub-devguide-device-twins.md) útmutató ikereszközök használatával.

## <a name="identify-devices-using-tags"></a>Címkék használatával eszközök azonosítása

Mielőtt létrehozna egy konfigurációt, mely eszközöket szeretné befolyásolni meg kell adnia. Az Azure IoT Hub eszköz az ikereszköz címkék használatával azonosítja. Minden eszköz rendelkezhet több címkét, és meghatározhatja azokat bármilyen módon, amely logikus a megoldáshoz. Például ha különböző helyeken lévő eszközök kezeléséhez, előfordulhat, hogy hozzá a következő címkék az ikereszközök:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>A célként megadott tartalom és a metrikák megadása

A célként megadott tartalom és a metrika lekérdezések vannak megadva, az eszköz leíró JSON-dokumentumok ikereszköz kívánt tulajdonságot be beállítása és a jelentett tulajdonságok alapján mérhető.  Hozzon létre egy Azure CLI 2.0 használatával automatikus eszközkonfiguráció, mentse a célként megadott tartalom és a metrikák helyileg .txt-fájlként. A fájlelérési út egy újabb a következő szakaszban használnia, ha futtatja a parancsot a alkalmazni a konfigurációt az eszközre. 

Íme egy alapszintű cél tartalom példa:

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Az alábbiakban a metrikalekérdezések példái:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

## <a name="create-a-configuration"></a>Konfiguráció létrehozása

Céleszközök konfigurálásához hozza létre a konfigurációt, amely a célként megadott tartalom és a metrikák áll. 

A következő paranccsal hozzon létre egy konfigurációt:

```cli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** – a konfigurációt, amely létrehozza az IoT hub nevét. Adjon meg egy egyedi nevet, amely legfeljebb 128 kisbetűket a konfiguráció. Kerülje a tárolóhelyek és a következő érvénytelen karaktereket: `& ^ [ ] { } \ | " < > /`.

* --**címkék** -címkézés segít nyomon követni a konfigurációt. Címkék olyan név, érték párok, melyek az üzemelő példány leírására. Ha például `HostPlatform, Linux` vagy `Version, 3.0.1`

* --**tartalom** – beágyazott JSON vagy fájl elérési útja a célként megadott tartalom értékű legyen ikereszköz kívánt tulajdonságait. 

* --**eseményközpont-neve** – az IoT hub, amely létrehozza a konfiguráció nevét. A központ az aktuális előfizetésben kell lennie. Váltson át a kívánt előfizetés azonosítóértékét paranccsal `az account set -s [subscription name]`

* --**célfeltétel** – adja meg a célfeltétel meghatározni, hogy mely eszközök érinteni fog ebben a konfigurációban. A feltétel device twin címkék alapján vagy az ikereszköz kívánt tulajdonságot, és meg kell egyeznie a kifejezés formátuma. Ha például `tags.environment='test'` vagy `properties.desired.devicemodel='4000x'`. 

* --**prioritás** -pozitív egész szám. Abban az esetben, ha két vagy több konfigurációk célzott ugyanazon az eszközön, a konfiguráció a legnagyobb numerikus értékkel prioritás érvényes lesz.

* --**metrikák** -fájl elérési útja a metrikalekérdezések. Metrikák adja meg az eszköz lehet, hogy jelentéseket küldhetnek vissza alkalmazása konfigurációjának tartalma eredményeként állapotait összesítő számát. Például előfordulhat, hogy hozzon létre egy metrikát a függőben lévő beállításokat érintő változások, a egy metrikát a hibákat és a egy metrikát a sikeres beállítások módosításait. 

## <a name="monitor-a-configuration"></a>A figyelő a konfigurációs

A következő paranccsal egy konfigurációs tartalmának megjelenítése:

```cli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** – a konfigurációt, amely az IoT hub nevét.

* --**eseményközpont-neve** –, amelyben a konfigurációja létezik IoT hub nevét. A központ az aktuális előfizetésben kell lennie. Váltson át a kívánt előfizetés azonosítóértékét paranccsal `az account set -s [subscription name]`

Vizsgálja meg a konfiguráció a parancssori ablakba. A **metrikák** tulajdonság mindegyik metrikát, amely kiértékeli a valamennyi elosztóhoz számát sorolja fel:

* **targetedCount** -egy rendszer mérőszám, amely a célcsoport-kezelési feltételnek megfelelő IoT hub device twins számát adja meg.

* **appliedCount** -rendszer metrika kellett volna a alkalmazni tartalom cél eszközök számát adja meg.

* **Az egyéni metrika** – bármely metrikák definiált felhasználói mérőszámok minősülnek.

Megjelenítheti az eszközazonosítókat listáját vagy objektumok az egyes metrikák a következő paranccsal:

```cli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** – a központi telepítés, amely az IoT hub nevét.

* --**a metrika-id** – a neve, amelynek meg szeretné tekinteni a eszközazonosítókat, listája például a metrika `appliedCount`.

* --**eseményközpont-neve** -neve az IoT hub, amelyben az üzemelő példány található. A központ az aktuális előfizetésben kell lennie. A kívánt előfizetés azonosítóértékét paranccsal váltson `az account set -s [subscription name]`.

* --**a metrika-típusú** -metrikus típus lehet `system` vagy `user`.  Rendszer-metrikák `targetedCount` és `appliedCount`. Minden egyéb metrika a felhasználói mérőszámok.

## <a name="modify-a-configuration"></a>A konfiguráció módosítása

Ha módosítja a konfigurációt, a módosítások azonnal replikálja az összes megcélzott eszközre. 

A célfeltétel frissít, ha elő a következő frissítéseket:

* Az ikereszközök nem felelt meg a régi célfeltétel, de az új célfeltétel megfelel, és ez a konfiguráció a legmagasabb prioritású az adott ikereszköz, ha ez a konfiguráció az ikereszköz van alkalmazva. 

* Az ikereszközök már nem felel meg a célként megadott feltétel, ha a konfigurációs beállításai a rendszer eltávolítja, és az ikereszköz fog módosította a következő legmagasabb prioritású konfigurációs. 

* Ha már nem ez a konfiguráció jelenleg fut az ikereszközök a célfeltétel megfelel, és minden egyéb konfigurációt cél állapota nem felel meg, a konfigurációs beállításai a eltávolításra kerül, és minden egyéb módosítás kerül sor az ikereszköz. 

Az alábbi parancs segítségével frissítse a konfigurációt:

```cli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** – a konfigurációt, amely az IoT hub nevét.

* --**eseményközpont-neve** –, amelyben a konfigurációja létezik IoT hub nevét. A központ az aktuális előfizetésben kell lennie. A kívánt előfizetés azonosítóértékét paranccsal váltson `az account set -s [subscription name]`.

* --**Állítsa be** – egy tulajdonság frissítése az a konfiguráció. Az alábbi tulajdonságok frissíthetők:

    * targetCondition – példa `targetCondition=tags.location.state='Oregon'`

    * címkék 

    * prioritás

## <a name="delete-a-configuration"></a>Konfiguráció törlése

Amikor egy konfiguráció törléséhez a következő legmagasabb prioritású konfigurációjuk bármely ikereszközök igénybe vehet. Ikereszközök nem felelnek meg a célfeltétel bármely más beállítás, ha nincs más beállítások lesznek alkalmazva. 

A következő parancsot használja egy konfiguráció törléséhez:

```cli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```
* --**config-id** – a konfigurációt, amely az IoT hub nevét.

* --**eseményközpont-neve** –, amelyben a konfigurációja létezik IoT hub nevét. A központ az aktuális előfizetésben kell lennie. A kívánt előfizetés azonosítóértékét paranccsal váltson `az account set -s [subscription name]`.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan konfigurálhatja és figyelheti a nagy mennyiségű IoT-eszközök. Az alábbi hivatkozásokból tudhat meg többet az Azure IoT Hub kezelése:

* [Az IoT Hub eszközidentitások tömeges kezelése](iot-hub-bulk-identity-mgmt.md)
* [Az IoT Hub-metrikák](iot-hub-metrics.md)
* [Műveletek figyelése](iot-hub-operations-monitoring.md)

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Az IoT Hub fejlesztői útmutató](iot-hub-devguide.md)
* [Edge-eszközök mesterséges Intelligencia telepítése az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)

Böngészhet a beavatkozás nélküli, just-in-time kiépítését lehetővé tevő, olvassa el az IoT Hub Device Provisioning Service használatával: 

* [Azure IoT Hub Device Provisioning Service](/azure/iot-dps)
