---
title: Automatikus eszközkezelés az Azure IoT Hub (CLI) használatával Microsoft Docs
description: Az Azure IoT Hub automatikus konfigurációinak használata több IoT-eszköz vagy-modul felügyeletéhez
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: chrisgre
ms.openlocfilehash: 381f550f6d64dee3c7649a040c1e24b7c9d42f2c
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78669433"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Az eszközök és modulok automatikus IoT az Azure CLI használatával

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Az Azure-IoT Hub automatikus eszközkezelés számos olyan ismétlődő és összetett feladatot automatizál, amely nagy méretű eszköz-flották felügyeletét végzi. Az automatikus eszközkezelés lehetővé teszi, hogy a tulajdonságok alapján csoportosítsa az eszközöket, Definiáljon egy kívánt konfigurációt, majd hagyja IoT Hub frissíteni az eszközöket, amikor azok hatókörbe kerülnek. Ezt a frissítést _automatikus eszköz-konfigurációval_ vagy _automatikus modul-konfigurációval_végezheti el, így összefoglalhatja a befejezést és a megfelelőséget, kezelheti az egyesítést és az ütközéseket, valamint a konfigurációkat egy szakaszos megközelítésben.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Az automatikus eszközkezelés úgy működik, hogy az ikrek vagy a modul-modulok egy készletét frissíti a kívánt tulajdonságokkal, és összefoglalja a két jelentett tulajdonságon alapuló összegzést.  Új osztályt és JSON-dokumentumot vezet be, *amelynek neve* három részből áll:

* A **célként megadott feltétel** az ikrek vagy a modul-modulok frissítésének hatókörét határozza meg. A célként megadott feltétel lekérdezésként van megadva az eszköz Twin-címkékkel és/vagy a jelentett tulajdonságokkal.

* A **célként megadott tartalom** határozza meg a kívánt tulajdonságokat, amelyeket hozzá kell adni, vagy frissíteni kell a megcélzott eszköz ikrek vagy moduljában. A tartalom a módosítani kívánt tulajdonságok szakaszának elérési útját tartalmazza.

* A **metrikák** határozzák meg a különböző konfigurációs állapotok, például a **sikeres**, **folyamatban lévő**és **hiba**összegzésének számát. Egyéni metrikák vannak megadva lekérdezésként a Twin jelentett tulajdonságainál.  A rendszermetrikák az alapértelmezett mérőszámok, amelyek a kettős frissítési állapotot mérik, például a megcélozott ikrek számát és a sikeresen frissített ikrek számát.

Az automatikus konfigurációk az első alkalommal futnak a konfiguráció létrehozása után, majd öt percenként. A metrikák lekérdezései minden alkalommal futnak, amikor az automatikus konfiguráció fut.

## <a name="cli-prerequisites"></a>A CLI előfeltételei

* Egy [IoT hub](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésében. 

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) a környezetben. Legalább az Azure CLI-verziójának 2.0.70 vagy újabbnak kell lennie. A verziószámot az `az –-version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer. 

* Az [Azure CLI-hez készült IoT-bővítmény](https://github.com/Azure/azure-cli).

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>Az ikrek megvalósítása

Az eszközök automatikus konfigurálásához az eszközökön az ikrek szinkronizálása szükséges a felhő és az eszközök között.  További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](iot-hub-devguide-device-twins.md).

Az automatikus modul-konfigurációkhoz az ikrek modul használatával kell szinkronizálni a felhő és a modulok közötti állapotot. További információ: az [ikrek megismerése és használata IoT Hubban](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>Címkék használata az ikrek célzásához

A konfiguráció létrehozása előtt meg kell adnia, hogy mely eszközöket vagy modulokat kívánja érinteni. Az Azure IoT Hub azonosítja az eszközöket, és címkék használatával azonosítja az eszközt a Twin-ben, és a modulban található címkék használatával azonosítja a modulokat. Minden eszköz vagy modul több címkével is rendelkezhet, és bármilyen módon meghatározhatja a megoldását. Ha például különböző helyen felügyeli az eszközöket, adja hozzá a következő címkéket egy eszközhöz a Twin:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>A célként megadott tartalom és mérőszámok definiálása

A célként megadott tartalom-és metrikai lekérdezések JSON-dokumentumokként vannak megadva, amelyek leírják az eszköz Twin vagy Module Twin kívánt tulajdonságait, hogy megállítsa és bejelentett tulajdonságokat mérjen.  Ha automatikus konfigurációt szeretne létrehozni az Azure CLI használatával, mentse a célként megadott tartalmat és mérőszámokat. txt fájlként. A fájl elérési útját egy későbbi szakaszban használhatja, amikor a parancsot futtatja a konfiguráció alkalmazásához az eszközön.

Íme egy alapszintű célként szolgáló tartalmi minta az automatikus eszköz konfigurálásához:

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

Az automatikus modul-konfigurációk nagyon hasonló módon viselkednek, de `deviceContent`helyett a `moduleContent` célozza meg.

```json
{
  "content": {
    "moduleContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Példa a metrikus lekérdezésekre:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

A modulok metrikai lekérdezései hasonlóak az eszközök lekérdezéséhez is, de a `moduleId` a `devices.modules`közül választhat. Például: 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>Konfiguráció létrehozása

A megcélzott eszközöket úgy konfigurálja, hogy létrehoz egy olyan konfigurációt, amely a célként megadott tartalmat és mérőszámokat tartalmazza. 

Konfiguráció létrehozásához használja a következő parancsot:

```cli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-ID** – annak a konfigurációnak a neve, amely az IoT központban lesz létrehozva. Adjon egy egyedi nevet a konfigurációnak, amely akár 128 kisbetűt is tartalmazhat. Kerülje a szóközöket, és a következő érvénytelen karaktereket: `& ^ [ ] { } \ | " < > /`.

* --**feliratok** – címkék hozzáadásával követheti nyomon a konfigurációt. Címkék olyan név, érték párok, melyek az üzemelő példány leírására. Például `HostPlatform, Linux` vagy `Version, 3.0.1`

* --**Content** -line JSON-vagy elérési út a célként megadott tartalomhoz, amely csak a kívánt tulajdonságokat adja meg. 

* --**hub –** a IoT hub neve, amelyben a rendszer létrehozza a konfigurációt. A központ az aktuális előfizetésben kell lennie. Váltson a kívánt előfizetésre a paranccsal `az account set -s [subscription name]`

* --**célalkalmazás** – adjon meg egy célként megadott feltételt annak meghatározásához, hogy mely eszközök vagy modulok lesznek megcélozva ezzel a konfigurációval. Az eszközök automatikus konfigurálásához a feltétel az eszköz Twin-címkék vagy az eszközök Twin kívánt tulajdonságain alapul, és meg kell egyeznie a kifejezés formátumával. Például `tags.environment='test'` vagy `properties.desired.devicemodel='4000x'`. A modul automatikus konfigurálásához a feltétel a modul Twin címkék vagy a modul Twin kívánt tulajdonságai alapján történik. Például `from devices.modules where tags.environment='test'` vagy `from devices.modules where properties.reported.chillerProperties.model='4000x'`.

* --**prioritás** – pozitív egész szám. Abban az esetben, ha két vagy több konfiguráció ugyanarra az eszközre vagy modulra irányul, a legmagasabb prioritású értékkel rendelkező konfiguráció lesz érvényes.

* --**mérőszámok** – filepath a metrikai lekérdezésekhez. A metrikák a konfigurációs tartalom alkalmazása után egy eszköz vagy modul által visszajelentett különböző állapotok összesítő számát tartalmazzák. Létrehozhat például egy mérőszámot a függőben lévő beállítások változásaihoz, egy mérőszámot a hibákhoz, valamint egy mérőszámot a sikeres beállítások módosításához. 

## <a name="monitor-a-configuration"></a>Konfiguráció figyelése

A következő parancs használatával jelenítheti meg egy konfiguráció tartalmát:

```cli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-ID** – az IoT hub-ban található konfiguráció neve.

* --**hub –** a IoT hub neve, amelyben a konfiguráció létezik. A központ az aktuális előfizetésben kell lennie. Váltson a kívánt előfizetésre a paranccsal `az account set -s [subscription name]`

Ellenőrizze a konfigurációt a parancsablakban. A **metrikák** tulajdonság felsorolja az egyes hubok által kiértékelt metrikák darabszámát:

* **targetedCount** – a rendszer mérőszáma, amely meghatározza, hogy az ikrek vagy a modulok hányan vannak a célzott feltételnek megfelelő IoT Hubban.

* **appliedCount** – a rendszer mérőszáma meghatározza, hogy hány eszközt vagy modult alkalmazott a célként megadott tartalom.

* **Egyéni metrika** – a definiált mérőszámok felhasználói mérőszámok.

Az alábbi paranccsal megjelenítheti az eszközök azonosítóit, modul-azonosítóit, illetve az egyes metrikák objektumainak listáját:

```cli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-ID** – az IoT hub-ban található központi telepítés neve.

* --**metrika – azonosító** – annak a metrikának a neve, amelynek az eszköz-azonosítóit vagy modul-azonosítóit meg szeretné jeleníteni, például `appliedCount`.

* --**hub –** a IoT hub neve, amelyben a központi telepítés létezik. A központ az aktuális előfizetésben kell lennie. Váltson a kívánt előfizetésre `az account set -s [subscription name]`paranccsal.

* --**metrika** típusú-metrikus típus lehet `system` vagy `user`.  A rendszermetrikák `targetedCount` és `appliedCount`. Minden más metrika felhasználói metrika.

## <a name="modify-a-configuration"></a>Konfiguráció módosítása

Konfiguráció módosításakor a módosítások azonnal replikálódnak az összes megadott eszközre. 

A célfeltétel frissít, ha elő a következő frissítéseket:

* Ha egy iker nem felelt meg a régi cél feltételnek, de megfelel az új célként megadott feltételnek, és ez a konfiguráció az adott Twin legmagasabb prioritása, akkor ezt a konfigurációt alkalmazza a rendszer. 

* Ha a jelenlegi konfigurációt jelenleg már nem teljesíti a megcélzott feltételnek, a rendszer eltávolítja a konfiguráció beállításait, és a Twin érték a következő legmagasabb prioritású konfigurációval lesz módosítva. 

* Ha a jelenlegi konfiguráció jelenleg nem felel meg a megcélzott feltételnek, és nem teljesíti a többi konfiguráció célját, akkor a konfiguráció beállításai el lesznek távolítva, és a különálló módosítások nem lesznek elérhetők. 

A konfiguráció frissítéséhez használja a következő parancsot:

```cli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-ID** – az IoT hub-ban található konfiguráció neve.

* --**hub –** a IoT hub neve, amelyben a konfiguráció létezik. A központ az aktuális előfizetésben kell lennie. Váltson a kívánt előfizetésre `az account set -s [subscription name]`paranccsal.

* --**beállítása** – egy tulajdonság frissítése a konfigurációban. Az alábbi tulajdonságok frissíthetők:

    * targetCondition – például `targetCondition=tags.location.state='Oregon'`

    * címkék 

    * priority

## <a name="delete-a-configuration"></a>Konfiguráció törlése

Ha töröl egy konfigurációt, minden eszköz ikrek vagy modul ikrek a következő legmagasabb prioritású konfigurációt használják. Ha az ikrek nem teljesíti a többi konfiguráció célját, akkor más beállítások nem lesznek alkalmazva. 

A konfiguráció törléséhez használja a következő parancsot:

```cli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```
* --**config-ID** – az IoT hub-ban található konfiguráció neve.

* --**hub –** a IoT hub neve, amelyben a konfiguráció létezik. A központ az aktuális előfizetésben kell lennie. Váltson a kívánt előfizetésre `az account set -s [subscription name]`paranccsal.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan konfigurálhatja és figyelheti a IoT-eszközök méretét. Az alábbi hivatkozásokat követve további információkat tudhat meg az Azure IoT Hub kezeléséről:

* [IoT Hub-eszközidentitások csoportos kezelése](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub metrikák](iot-hub-metrics.md)
* [Műveletek figyelése](iot-hub-operations-monitoring.md)

A IoT Hub képességeinek további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató](iot-hub-devguide.md)
* [AI üzembe helyezése az Edge-eszközökön Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Ha szeretné megtekinteni a IoT Hub Device Provisioning Service használatát a nulla érintéses, igény szerinti kiépítés engedélyezéséhez, olvassa el a következő témakört: 

* [Azure IoT Hub Device Provisioning Service](/azure/iot-dps)
