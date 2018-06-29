---
title: Konfigurálhatja és figyelheti az IoT-eszközök Azure IoT Hub (CLI) léptékű |} Microsoft Docs
description: Konfiguráció hozzárendelése több eszköz automatikus eszközkonfigurációk Azure IoT-központ segítségével
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: c12a07aabdecb070cfa99f8851f907499599a1fc
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036228"
---
# <a name="configure-and-monitor-iot-devices-at-scale-using-the-azure-cli"></a>Konfigurálhatja és figyelheti az Azure parancssori felület használatával léptékű az IoT-eszközök

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatikus kezelés az Azure IoT Hub automatizálja az ismétlődő és az összetett feladatok nagy eszköz flották kezelését a teljes egészében a életciklusának keresztül. Automatikus felügyeleti (MDM) az eszközök tulajdonságaik alapján célozza, adja meg a kívánt konfiguráció, és lehetővé teszik az IoT-központ frissítése az eszközöket, amikor a hatókör származnak.  Ez egy automatikus eszközkonfiguráció, amely lehetővé teszi, hogy összesítse a befejezése és a megfelelőség, a leíró egyesítése és a ütközések, és fokozatosan konfigurációk szakaszos megközelítéssel használatával történik.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatikus eszköz konfigurációk munkahelyi eszköz twins csoportja frissítésével kívánt tulajdonságokkal és jelentéskészítési eszköz iker alapján összefoglaló jelentett tulajdonságait.  Egy új osztályt és nevű JSON-dokumentum okozna a _konfigurációs_ amelyek három részből áll:

* A **céloz feltétel** eszköz twins frissítendő körét határozza meg. A célként megadott feltétel van megadva eszköz iker címkékre lekérdezésként és/vagy jelentett tulajdonságok.

* A **céloz tartalom** kell hozzáadni, vagy a céleszköz twins frissített kívánt tulajdonságait határozza meg. A tartalom elérési útját a módosítani kívánt tulajdonságok szakasza tartalmazza.

* A **metrikák** határozza meg az összefoglaló számát is különböző konfigurációs állapotnak, mint **sikeres**, **folyamatban lévő**, és **hiba**. Egyéni metrikák eszközön lekérdezések vannak megadva a két jelentett tulajdonságok.  Rendszer-adatok gyűjtése le alapértelmezett metrikák mérő iker frissítési állapot, például az eszköz twins irányuló számát és a sikeresen frissített twins számát. 

## <a name="cli-prerequisites"></a>Parancssori felület Előfeltételek

* Egy [IoT-központ](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésben. 
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) a saját környezetében. Az Azure CLI 2.0 legalább 2.0.24-es verzióját kell használnia. A verziószámot az `az –-version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer. 
* A [IoT-bővítményt az Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="implement-device-twins-to-configure-devices"></a>Eszközök konfigurálása eszköz twins megvalósítása

Automatikus eszközkonfigurációk eszköz twins a felhő- és eszközök közötti szinkronizálását használata szükséges.  Tekintse meg [megértése és használja az IoT Hub eszköz twins] [ lnk-device-twin] útmutatót twins eszköz használatával.

## <a name="identify-devices-using-tags"></a>Címkék használatával eszközök azonosítása

Mielőtt létrehozna egy konfigurációs, mely eszközöket szeretné befolyásolni kell megadnia. Az Azure IoT Hub címkék használata az eszköz két eszközöket jeleníti meg. Minden eszköz lehet több címkét, és meghatározhatja azokat bármilyen módon legjobb megoldást. Például különböző helyeken lévő eszközök kezeléséhez, ha egy eszköz iker hozzáadhat előfordulhat, hogy a következő címkékkel:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```
## <a name="define-the-target-content-and-metrics"></a>Adja meg a célként megadott tartalom és a metrikák

A tartalom és a metrika lekérdezések megadott JSON-dokumentumok ismertetik az eszköz a két adatkészletet kívánt tulajdonságokat és mérendő jelentett tulajdonságok.  Az automatikus eszköz létrehozása az Azure CLI 2.0 verzióját használja, mentse a tartalom és a metrikák helyileg .txt fájlként. Szüksége lesz a Fájlelérési utak következő részeiben a konfiguráció alkalmazása az eszköz a parancs futtatásakor. 

Alapszintű cél tartalom minta a következő:

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

Az alábbiakban a metrika lekérdezések példák:

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

A tartalom és a metrikák konfiguráció létrehozása Céleszközök konfigurálásához. 

A következő paranccsal konfiguráció létrehozásához:

   ```cli
   az iot hub configuration create --config-id [configuration id] --labels [labels] --content [file path] --hub-name [hub name] --target-condition [target query] --priority [int] --metrics [metric queries]
   ```

* **--konfigurációs-azonosítója** -konfiguráció szerint létrejön az IoT hub nevét. Adjon meg legfeljebb 128 kisbetűk egy egyedi nevet a konfigurációt. Elkerülendő, a szóközöket és a következő érvénytelen karaktereket: `& ^ [ ] { } \ | " < > /`.
* **--címkék** -címkék nyomon követéséhez a konfiguráció hozzáadása. Címkék: név, érték párok, amelyek a központi telepítés ismertetik. Például `HostPlatform, Linux` vagy `Version, 3.0.1`
* **--tartalom** -beágyazott JSON vagy a fájl elérési útját a cél tartalmat állítható be a két szükséges tulajdonságai. 
* **---központnév** -az IoT hub, amely létrehozza a konfigurációs nevét. A központ az aktuális előfizetésben kell lennie. A parancs a kívánt előfizetés váltani `az account set -s [subscription name]`
* **--cél-feltétel** -adjon meg egy cél feltételt határozza meg, hogy mely eszközöket fogja ezt a konfigurációt. A feltétel alapján eszköz iker címkék vagy eszköz iker tulajdonságok szükséges, és meg kell felelnie a kifejezés formátumban. Például `tags.environment='test'` vagy `properties.desired.devicemodel='4000x'`. 
* **--prioritás** -pozitív egész szám. Abban az esetben, ha két vagy több konfigurációk célzott ugyanazon az eszközön, a konfiguráció a legnagyobb numerikus értéket prioritás érvényesek.
* **--metrikák** -fájl elérési útja a metrika lekérdezések. Metrikák eszköz előfordulhat, hogy jelentéseket küldhetnek vissza konfigurációs tartalom alkalmazása miatt a különböző állapotok összefoglaló számát adja meg. Például létrehozhat egy metrikát függőben lévő módosításait, egy metrika a hibákat és egy metrikát sikeres módosításait. 

## <a name="monitor-a-configuration"></a>A figyelő a konfigurációs

A következő paranccsal egy konfigurációs tartalmának megjelenítése:

   ```cli
az iot hub configuration show --config-id [configuration id] --hub-name [hub name]
   ```
* **--konfigurációs-azonosítója** -a konfiguráció már szerepel az IoT hub nevét.
* **---központnév** -neve az IoT hub, amelyben a konfigurációja létezik. A központ az aktuális előfizetésben kell lennie. A parancs a kívánt előfizetés váltani `az account set -s [subscription name]`

Ellenőrizze az konfigurációját a parancsablakban. A **metrikák** tulajdonság minden olyan metrikajelentés minden hubból kiértékelt számát sorolja fel:
* **targetedCount** -IoT-központ, a célcsoport-kezelési feltételnek megfelelő eszköz twins számát megadó rendszer metrikát.
* **appliedCount** -rendszer metrika meghatározza az eszközök rendelkezésére állt-e a célként megadott tartalom alkalmazott számát.
* **Az egyéni metrika** -bármely metrikák definiált felhasználói metrikák akkor minősül.

Megjelenítheti eszközazonosítók listája vagy objektumok az egyes a metrikák a következő paranccsal:

   ```cli
az iot hub configuration show-metric --config-id [configuration id] --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
   ```

* **--konfigurációs-azonosítója** -a központi telepítés már szerepel az IoT hub nevét.
* **--azonosító a metrika** - a legyen megtekintéséhez a eszközazonosítók, például a mérték neve `appliedCount`
* **---központnév** -neve az IoT hub, amelyben az üzemelő példány található. A központ az aktuális előfizetésben kell lennie. A parancs a kívánt előfizetés váltani `az account set -s [subscription name]`
* **– a metrika-típus** -típust lehet `system` vagy `user`.  Rendszer-adatok gyűjtése le van `targetedCount` és `appliedCount`. Minden egyéb adatok gyűjtése le felhasználói metrikákat.

## <a name="modify-a-configuration"></a>A konfiguráció módosítása

A konfiguráció módosításakor a azonnal replikálja a módosításokat az összes megcélzott eszköz. 

Ha frissíti a cél feltétel, a következő frissítéseket fordulhat elő:
* Ha egy eszköz iker nem felelt meg a régi cél feltétel, de az új cél feltétel és a beállítások az adott eszköz iker a legmagasabb prioritású, ez a konfiguráció az eszköz a két van alkalmazva. 
* Ha egy eszköz iker már nem felel meg a célként megadott feltétel, a beállítások a konfigurációs eltávolításra kerül, és az eszköz iker módosítani fogja a következő legmagasabb prioritású virtuális gép konfigurációja. 
* Ha egy eszköz iker már nem ez a konfiguráció jelenleg fut a célként megadott feltétel, és minden egyéb konfigurációt cél állapota nem felel meg, a beállítások a konfigurációs eltávolításra kerül, és nem módosít a kettős kerül sor. 

A következő paranccsal egy konfiguráció frissítése:

   ```cli
az iot hub configuration update --config-id [configuration id] --hub-name [hub name] --set [property1.property2='value']
   ```
* **--konfigurációs-azonosítója** -a konfiguráció már szerepel az IoT hub nevét.
* **---központnév** -neve az IoT hub, amelyben a konfigurációja létezik. A központ az aktuális előfizetésben kell lennie. A parancs a kívánt előfizetés váltani `az account set -s [subscription name]`
* **– Állítsa** -frissítés a konfigurációs tulajdonság. Frissítheti a következő tulajdonságokkal:
    * targetCondition – példa `targetCondition=tags.location.state='Oregon'`
    * címkék 
    * prioritás

## <a name="delete-a-configuration"></a>Konfiguráció törlése

Ha töröl egy konfigurációt, bármely eszköz twins a következő legmagasabb prioritású konfigurációs igénybe. Eszköz twins nem felelnek meg cél más beállításokat, ha nincs más beállítások érvényesek. 

A következő parancs segítségével törölheti a konfiguráció:

   ```cli
az iot hub configuration delete --config-id [configuration id] --hub-name [hub name] 
   ```
* **--konfigurációs-azonosítója** -a konfiguráció már szerepel az IoT hub nevét.
* **---központnév** -neve az IoT hub, amelyben a konfigurációja létezik. A központ az aktuális előfizetésben kell lennie. A parancs a kívánt előfizetés váltani `az account set -s [subscription name]`

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan konfigurálhatja és figyelheti a léptékű az IoT-eszközök. Az alábbi hivatkozásokból tudhat meg többet az Azure IoT Hub kezelése:

* [Az IoT Hub eszköz identitásai tömeges kezelése][lnk-bulkIDs]
* [Az IoT-központ metrikák][lnk-metrics]
* [Figyelési műveletek][lnk-monitor]

Az IoT-központ képességeit további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató][lnk-devguide]
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]

Az IoT Hub eszköz kiépítése szolgáltatás segítségével nulla-érintés engedélyezése megismeréséhez just-in-time kiépítés, lásd: 

* [Az Azure IoT Hub eszköz-üzembehelyezési szolgáltatás][lnk-dps]

[lnk-device-twin]: iot-hub-devguide-device-twins.md
[lnk-bulkIDs]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
[lnk-portal]: https://portal.azure.com
