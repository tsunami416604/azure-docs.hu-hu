---
title: Automatikus eszközkezelés nagy méretekben az Azure IoT Hub (CLI) használatával | Microsoft dokumentumok
description: Több IoT-eszköz vagy -modul kezeléséhez használja az Azure IoT Hub automatikus konfigurációit
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: chrisgre
ms.openlocfilehash: 748f3e09fd03a6f37954c8dfaf4b6ae9144384bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235606"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Automatikus IoT-eszköz- és modulkezelés az Azure CLI használatával

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Az Azure IoT Hub automatikus eszközkezelése automatizálja a nagy eszközflották kezelésével kapcsolatos ismétlődő és összetett feladatok at. Az automatikus eszközfelügyelettel megcélozhatja az eszközök készletét a tulajdonságaik alapján, meghatározhatja a kívánt konfigurációt, majd lehetővé teheti az IoT Hub számára, hogy frissítse az eszközöket, amikor azok hatókörbe kerülnek. Ez a frissítés _automatikus eszközkonfigurációval_ vagy _automatikus modulkonfigurációval_történik, amely lehetővé teszi a befejezés és a megfelelőség összegzését, az egyesítés és az ütközések kezelését, valamint a konfigurációk szakaszos megközelítésben történő bevezetését.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Az automatikus eszközkezelés úgy működik, hogy frissíti az ikereszközök vagy a modulikrek készletét a kívánt tulajdonságokkal, és jelentést tesz egy, két jelentett tulajdonságon alapuló összegzést.  Bevezet egy új osztály és JSON dokumentum nevű *konfiguráció,* amely három részből áll:

* A **célfeltétel** határozza meg az eszköz ikrek vagy a modul ikrek frissíteni kell. A célfeltétel az eszköz ikercímkéinek és/vagy jelentett tulajdonságainak lekérdezéseként van megadva.

* A **céltartalom** határozza meg a kívánt tulajdonságokat kell hozzáadni vagy frissíteni a megcélzott eszköz twins vagy modul twins. A tartalom tartalmazza a módosítani kívánt tulajdonságok szakaszának elérési útját.

* A **mérőszámok határozzák** meg a különböző konfigurációs állapotok , például a **Sikeres**, **a Folyamatban**és a **Hiba**összegzését . Az egyéni metrikák két jelentett tulajdonság lekérdezéseként vannak megadva.  A rendszermetrikák az alapértelmezett metrikák, amelyek az ikerfrissítés állapotát mérik, például a megcélzott ikerikrek számát és a sikeresen frissített ikrek számát.

Az automatikus konfigurációk először röviddel a konfiguráció létrehozása után, majd ötperces időközönként futnak. Metrikák lekérdezések fut minden alkalommal, amikor az automatikus konfiguráció fut.

## <a name="cli-prerequisites"></a>CLI előfeltételek

* Egy [IoT-központ](../iot-hub/iot-hub-create-using-cli.md) az Azure-előfizetésben. 

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) a környezetben. Az Azure CLI-verziónak legalább 2.0.70-nek kell lennie. A verziószámot az `az –-version` paranccsal ellenőrizheti. Ez a verzió támogatja az „az” bővítményparancsokat, és ebben a verzióban került bevezetésre a Knack parancskeretrendszer. 

* Az [Azure CLI IoT-bővítménye.](https://github.com/Azure/azure-cli)

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>Ikrek megvalósítása

Az automatikus eszközkonfigurációk hoz használ-as eszközök ikerállapot a felhő és az eszközök közötti szinkronizáláshoz.  További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](iot-hub-devguide-device-twins.md).

Az automatikus modulkonfigurációk hoz a modul twins a felhő és a modulok közötti állapot szinkronizálásához. További információ: [A modultwins az IoT Hubban.](iot-hub-devguide-module-twins.md)

## <a name="use-tags-to-target-twins"></a>Címkék használata ikrek célzásához

A konfiguráció létrehozása előtt meg kell adnia, hogy mely eszközöket vagy modulokat kívánja befolyásolni. Az Azure IoT Hub azonosítja az eszközöket, és címkéket használ az ikereszközben, és azonosítja a modulokat a modul ikercímkék használatával. Minden eszköz vagy modul több címkével is rendelkezhet, és bármilyen módon meghatározhatja őket, amely a megoldásnak van értelme. Ha például különböző helyeken kezeli az eszközöket, adja hozzá a következő címkéket egy ikereszközhöz:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>A céltartalom és a mérőszámok meghatározása

A céltartalom és a metrika lekérdezések vannak megadva JSON-dokumentumok, amelyek leírják az eszköz iker vagy modul iker kívánt tulajdonságokat beállítani és jelenteni tulajdonságok mérésére.  Ha automatikus konfigurációt szeretne létrehozni az Azure CLI használatával, mentse a céltartalmat és a metrikákat helyileg .txt fájlokként. A fájlelérési utakat egy későbbi szakaszban használja, amikor a parancs futtatásával alkalmazza a konfigurációt az eszközre.

Az alábbiakban egy alapvető céltartalom-minta áll az automatikus eszközkonfigurációhoz:

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

Az automatikus modulkonfigurációk nagyon hasonlóan viselkednek, `deviceContent`de a helyett a célt használja. `moduleContent`

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

Íme néhány példa a metrikalekérdezésekre:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

A modulok metrikalekérdezései is hasonlóak `moduleId` `devices.modules`az eszközök lekérdezéseihez, de a közül választhat. Példa: 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>Konfiguráció létrehozása

A céleszközöket a céltartalomból és a metrikákból álló konfiguráció létrehozásával konfigurálhatja. 

Konfiguráció létrehozásához használja a következő parancsot:

```azurecli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** - Az IoT hubban létrehozandó konfiguráció neve. Adjon a konfigurációnak egy egyedi nevet, amely legfeljebb 128 kisbetűs. Kerülje a szóközöket `& ^ [ ] { } \ | " < > /`és a következő érvénytelen karaktereket: .

* --**címkék** – Címkék hozzáadása a konfiguráció nyomon követéséhez. A címkék a név, értékpárok, amelyek leírják a központi telepítést. Példa: `HostPlatform, Linux` vagy `Version, 3.0.1`

* --**content** - Inline JSON vagy fájl elérési útját a céltartalom hoz be két kívánt tulajdonságokat. 

* --**hub-name** - Annak az IoT hubnak a neve, amelyben a konfiguráció létre jön. A hubnak az aktuális előfizetésben kell lennie. Váltás a kívánt előfizetésre a paranccsal`az account set -s [subscription name]`

* --**célfeltétel** – Adja meg a célfeltételt annak meghatározásához, hogy mely eszközök vagy modulok legyenek megcélozva ezzel a konfigurációval.Az automatikus eszközkonfiguráció esetén a feltétel az ikercímkék vagy az ikereszköz kívánt tulajdonságain alapul, és meg kell egyeznie a kifejezés formátumának.Például `tags.environment='test'` vagy `properties.desired.devicemodel='4000x'`.Az automatikus modulkonfiguráció esetében a feltétel a modul ikercímkéken vagy a modul ikerkívánt tulajdonságain alapul.. Például `from devices.modules where tags.environment='test'` vagy `from devices.modules where properties.reported.chillerProperties.model='4000x'`.

* --**prioritás** - Pozitív egész szám. Abban az esetben, ha két vagy több konfiguráció ugyanarra az eszközre vagy modulra irányul, a legmagasabb numerikus prioritással rendelkező konfiguráció lesz alkalmazva.

* --**metrikák** - Filepath a metrika lekérdezések. Metrikák összegzése a különböző állapotok, amelyek egy eszköz vagy modul jelentheti vissza a konfigurációs tartalom alkalmazása után. Létrehozhat például egy mérőszámot a függőben lévő beállítások változásaihoz, egy hibát, és egy mérőszámot a sikeres beállítások változásaihoz. 

## <a name="monitor-a-configuration"></a>Konfiguráció figyelése

A konfiguráció tartalmának megjelenítéséhez használja a következő parancsot:

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** - Az IoT hubban található konfiguráció neve.

* --**hub-name** - Annak az IoT hubnak a neve, amelyben a konfiguráció létezik. A hubnak az aktuális előfizetésben kell lennie. Váltás a kívánt előfizetésre a paranccsal`az account set -s [subscription name]`

Vizsgálja meg a konfigurációt a parancsablakban.A **metrikák** tulajdonság felsorolja az egyes csomópontok által kiértékelt metrikák számát:

* **targetedCount** – A rendszer metrika, amely meghatározza az eszközök iker- vagy modultwins az IoT Hub, amelyek megfelelnek a célzási feltételnek.

* **appliedCount** – A rendszermetrika a céltartalommal alkalmazott eszközök vagy modulok számát adja meg.

* **Az egyéni metrika** – A megadott mérőszámok felhasználói mérőszámok.

Az egyes mérőszámok eszközazonosítóinak, modulazonosítóinak vagy objektumainak listáját a következő parancs segítségével jelenítheti meg:

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** – Az IoT hubban található központi telepítés neve.

* --**metric-id** - Annak a metrikának a neve, amelynek az eszközazonosítóinak `appliedCount`vagy modulazonosítóinak listáját meg szeretné tekinteni, például .

* --**hub-name** - Annak az IoT hubnak a neve, amelyben a központi telepítés létezik. A hubnak az aktuális előfizetésben kell lennie. Váltson a kívánt `az account set -s [subscription name]`előfizetésre a paranccsal.

* --**metrikus típusú** - `system` A `user`metrikus típus lehet vagy .  A rendszermetrikák és `targetedCount` `appliedCount`a. Minden más mérőszám felhasználói mutató.

## <a name="modify-a-configuration"></a>Konfiguráció módosítása

Konfiguráció módosításakor a módosítások azonnal replikálódnak az összes megcélzott eszközre. 

Ha frissíti a célfeltételt, a következő frissítések jelennek meg:

* Ha egy iker nem felel meg a régi célfeltételnek, de megfelel az új célfeltételnek, és ez a konfiguráció az iker számára a legmagasabb prioritás, akkor ez a konfiguráció lesz alkalmazva. 

* Ha egy iker, amely jelenleg ezt a konfigurációt futtatja, már nem felel meg a célfeltételnek, a konfiguráció beállításai törlődnek, és az ikertestvérét a következő legmagasabb prioritású konfiguráció módosítja. 

* Ha egy iker, amely jelenleg ezt a konfigurációt futtatja, már nem felel meg a célfeltételnek, és nem felel meg más konfigurációk célfeltételének, akkor a konfiguráció beállításai törlődnek, és az ikerkapcsolaton nem történik más módosítás. 

A konfiguráció frissítéséhez használja a következő parancsot:

```azurecli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** - Az IoT hubban található konfiguráció neve.

* --**hub-name** - Annak az IoT hubnak a neve, amelyben a konfiguráció létezik. A hubnak az aktuális előfizetésben kell lennie. Váltson a kívánt `az account set -s [subscription name]`előfizetésre a paranccsal.

* --**set** - Tulajdonság frissítése a konfigurációban. A következő tulajdonságokat frissítheti:

    * targetCondition - például`targetCondition=tags.location.state='Oregon'`

    * Címkék 

    * Prioritás

## <a name="delete-a-configuration"></a>Konfiguráció törlése

Amikor töröl egy konfigurációt, minden eszköz twins vagy modul ikrek veszi a következő legmagasabb prioritású konfiguráció. Ha az ikrek nem felelnek meg más konfiguráció célfeltételének, akkor más beállítások nem lesznek alkalmazva. 

A konfiguráció törléséhez használja a következő parancsot:

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-id** - Az IoT hubban található konfiguráció neve.

* --**hub-name** - Annak az IoT hubnak a neve, amelyben a konfiguráció létezik. A hubnak az aktuális előfizetésben kell lennie. Váltson a kívánt `az account set -s [subscription name]`előfizetésre a paranccsal.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan konfigurálhatja és figyelheti az IoT-eszközöket nagy méretekben. Az Azure IoT Hub kezeléséről az alábbi hivatkozásokra kattintva olvashat bővebben:

* [IoT Hub-eszközidentitások csoportos kezelése](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub-metrikák](iot-hub-metrics.md)
* [Műveletek figyelése](iot-hub-operations-monitoring.md)

Az IoT Hub képességeinek további megismeréséhez lásd:

* [Az IoT Hub fejlesztői útmutatója](iot-hub-devguide.md)
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)

Az IoT Hub-eszközkiépítési szolgáltatás használatával a nulla érintéses, just-in-time kiépítés engedélyezéséről a következő témakört kell ismertennie: 

* [Azure IoT Hub Device Provisioning Service](/azure/iot-dps)
