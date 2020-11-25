---
title: Az Azure-IoT Plug and Play eszköz telemetria tárolásához és elemzéséhez használja a Time Series Insightst | Microsoft Docs
description: Time Series Insights környezet beállítása és az IoT hub csatlakoztatása a IoT Plug and Play eszközök telemetria megtekintéséhez és elemzéséhez.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ca2319a78fb4c0c720a21e97944d5b75ada9d008
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014989"
---
# <a name="preview-tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>Oktatóanyag: Time Series Insights Gen2 létrehozása és csatlakoztatása a IoT Plug and Play eszköz telemetria tárolásához, megjelenítéséhez és elemzéséhez

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és konfigurálhat megfelelően egy [Azure Time Series Insights Gen2](https://docs.microsoft.com/azure/time-series-insights/overview-what-is-tsi) (ÁME) környezetet a IoT Plug and Play megoldással való integrációhoz. Használja az ÁME-t az idősoros adatok gyűjtésére, feldolgozására, tárolására, lekérdezésére és megjelenítésére eszközök internetes hálózata (IoT) skálán.

Először is üzembe kell helyeznie egy ÁME-környezetet, és csatlakoztatnia kell az IoT hub-t adatfolyam-eseményforrásként. Ezt követően a modell szinkronizálásával elkészítheti az [Idősorozat-modellt](../time-series-insights/concepts-model-overview.md) a hőmérséklet-vezérlő és a termosztátos eszközökhöz használt [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) minta alapján.

> [!NOTE]
> Ez az integráció előzetes verzióban érhető el. A DTDL-eszközök modelljeinek az idősorozat-modellhez való leképezése változhat.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Ezen a ponton a következőket teheti:

* Egy Azure IoT hub.
* Az IoT hub-hoz csatlakoztatott DPS-példány, amely a IoT Plug and Play eszközre vonatkozó egyéni eszközök regisztrálását.
* Egy vagy több összetevőből álló eszközről való kapcsolódás szimulált adatok továbbítása az IoT hubhoz.

Ha el szeretné kerülni az Azure CLI helyi telepítésének követelményét, használhatja a Azure Cloud Shell a Cloud Services beállításához.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>Az eseményforrás előkészítése

A korábban létrehozott IoT hub az ÁME-környezet [eseményének forrása](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingestion-event-sources)lesz.

> [!IMPORTANT]
> Tiltsa le a meglévő IoT Hub útvonalakat. Ismert probléma van, ha az IoT hub-t az ÁME-esemény forrásaként konfigurálta az [útválasztással](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints) . Ideiglenesen tiltsa le az útválasztási végpontokat, és ha az IoT hub az ÁME-hez van csatlakoztatva, akkor újra engedélyezheti őket.

Hozzon létre egy egyedi fogyasztói csoportot az IoT hub-ban az ÁME felhasználásához. Cserélje le a `my-pnp-hub` nevet a korábban használt IoT hub nevére:

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-your-time-series-id"></a>Idősorozat-azonosító kiválasztása

Az ÁME-környezet kiépítésekor ki kell választania egy *idősorozat-azonosítót*. Fontos, hogy kiválassza a megfelelő idősorozat-azonosítót, mert ez a tulajdonság nem módosítható, és a beállítása után nem módosítható. Az idősorozat-azonosító például egy adatbázis-partíciós kulcs. Az idősorozat-azonosító a Time Series-modell elsődleges kulcsaként működik. További információ: [ajánlott eljárások az Idősorozat-azonosító kiválasztásához](../time-series-insights/how-to-select-tsid.md).

IoT Plug and Play felhasználóként olyan _összetett kulcsot_ kell megadnia, amely a `iothub-connection-device-id` és az `dt-subject` idősorozat-azonosítóból áll. IoT Hub hozzáadja ezeket a rendszertulajdonságokat, amelyek tartalmazzák a IoT Plug and Play eszköz AZONOSÍTÓját, illetve az eszköz összetevőinek nevét.

Ha a IoT Plug and Play eszköz modelljei jelenleg nem használnak összetevőket, akkor `dt-subject` egy összetett kulcs részeként is szerepelnie kell, hogy később is használhassa őket. Mivel az idősorozat-azonosító nem módosítható, a Microsoft javasolja a beállítás engedélyezését abban az esetben, ha a későbbiekben szüksége van rá.

> [!NOTE]
> Az alábbi példák a multi-Component **TemperatureController** -eszközre vonatkoznak, de a nem összetevős **termosztátos** eszközhöz tartozó fogalmak is megegyeznek.

## <a name="provision-your-tsi-environment"></a>Az ÁME-környezet kiépítése

Ez a szakasz azt ismerteti, hogyan lehet kiépíteni a Azure Time Series Insights Gen2-környezetét.

A következő parancs:

* Létrehoz egy Azure Storage-fiókot a környezete [hűtőházi tárolójához](https://docs.microsoft.com/azure/time-series-insights/concepts-storage#cold-store), amely hosszú távú adatmegőrzésre és a múltbeli adatok elemzésére szolgál.
  * Cserélje le a változót `mytsicoldstore` egy egyedi névre a hűtőházi Storage-fiókhoz.
* Egy Azure Time Series Insights Gen2-környezetet hoz létre, amely tartalmazza a meleg tárolást a hét napos megőrzési időtartammal, valamint a hideg tárolást a végtelen megőrzés érdekében.
  * A helyére írja be az `my-tsi-env` ÁME-környezet egyedi nevét.
  * A helyére írja be annak az `my-pnp-resourcegroup` erőforráscsoportnak a nevét, amelyet a beállítás során használt.
  * `iothub-connection-device-id, dt-subject` az idősorozat-azonosító tulajdonsága.

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties iothub-connection-device-id, dt-subject --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

IoT Hub-eseményforrás összekapcsolásához. Cserélje `my-pnp-resourcegroup` le `my-pnp-hub` a, a és a `my-tsi-env` értéket a kiválasztott értékekre. A következő parancs a korábban létrehozott ÁME fogyasztói csoportjára hivatkozik:

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```

Navigáljon az erőforráscsoporthoz a [Azure Portalon](https://portal.azure.com) , és válassza ki az új Time Series Insights-környezetet. Látogasson el a példány áttekintésében látható *Time Series Insights Explorer URL-címére* :

![A portál áttekintő oldala](./media/tutorial-configure-tsi/view-environment.png)

Az Explorerben három példány jelenik meg:

* &lt;a PnP-eszköz azonosítója &gt; , thermostat1
* &lt;a PnP-eszköz azonosítója &gt; , thermostat2
* &lt;a PnP-eszköz azonosítója &gt; , `null`

> [!NOTE]
> A harmadik címke a saját **TemperatureController** származó telemetria jelöli, például az eszköz memóriájának munkakészletét. Mivel ez egy legfelső szintű tulajdonság, az összetevő nevének értéke null. Egy későbbi lépésben frissíti ezt egy további felhasználóbarát névre.

![Explorer – 1. nézet](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>Modell fordításának konfigurálása

Ezután lefordítja a DTDL az Azure Time Series Insights (ÁME) eszköz modelljére. Az ÁME idősorozat-modellje egy szemantikai modellezési eszköz az ÁME-n belüli contextualization. A Time Series-modell három alapvető összetevőből áll:

* [Idősorozat-modell példányai](../time-series-insights/concepts-model-overview.md#time-series-model-instances). A példányok magukban foglalhatják magukban az idősorozatok virtuális ábrázolását. A példányokat az idősorozat-azonosító egyedi módon azonosítja.
* [Idősorozat-modell hierarchiák](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies). A hierarchiák a tulajdonságok nevét és a kapcsolataik megadásával rendezik a példányokat.
* [Idősorozat-modell típusa](../time-series-insights/concepts-model-overview.md#time-series-model-types). A típusok segítségével [változók](../time-series-insights/concepts-variables.md) vagy képletek definiálhatók a számításokhoz. A típusok egy adott példánnyal vannak társítva.

### <a name="define-your-types"></a>A típusok meghatározása

Megkezdheti az adatbevitelt Azure Time Series Insights Gen2 anélkül, hogy előre definiált egy modellt. Amikor a telemetria megérkezik, az ÁME megkísérli az idősorozat-példányok újrafeloldását az idősorozat-azonosító tulajdonság értéke (i) alapján. Az összes példány az *alapértelmezett típushoz* van rendelve. A példányok megfelelő kategorizálásához manuálisan kell létrehoznia egy új típust. Az alábbi részletekből megtudhatja, hogy a legegyszerűbb módszer az eszköz DTDL-modelljeinek szinkronizálása az idősorozat-modellel:

* A digitális kettős modell azonosítója a típusazonosító lesz.
* A típus neve lehet a modell neve vagy a megjelenített név.
* A modell leírása lesz a típus leírása.
* Minden telemetria esetében legalább egy Type változó jön létre numerikus sémával.
  * Változókhoz csak numerikus adattípusok használhatók, de ha egy értéket egy másik típusként küld el, amely átalakítható, például `"0"` használhat [konverziós](/rest/api/time-series-insights/reference-time-series-expression-syntax.md#conversion-functions) függvényt is `toDouble` .
* A változó neve lehet a telemetria neve vagy a megjelenített név.
* A változó idősorozat kifejezésének meghatározásakor tekintse meg a telemetria nevét a dróton, és adja meg az adattípust.

| DTDL JSON | Time Series-modell JSON-típusa | Példaérték |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |  
|`contents` tömb| `variables` objektum  | Az alábbi példa megtekintése

![DTDL idősorozat-modell típusa](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> Ez a példa három változót mutat be, de mindegyik típus legfeljebb 100 lehet. A különböző változók hivatkozhatnak ugyanarra a telemetria-értékre, hogy szükség esetén különböző számításokat végezzenek. A szűrők, összesítések és skaláris függvények teljes listájáért tekintse meg [Time Series Insights Gen2 Time Series kifejezés szintaxisát](/rest/api/time-series-insights/reference-time-series-expression-syntax.md).

Nyisson meg egy szövegszerkesztőt, és mentse a következő JSON-t a helyi meghajtóra:

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:TemperatureController;1",
      "name": "Temperature Controller",
      "description": "Device with two thermostats and remote reboot.",
      "variables": {
        "workingSet": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.workingSet.Long, toLong($event.workingSet.Double))"
          }, 
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.temperature.Long, toLong($event.temperature.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "eventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        }
      }
    }
  ]
}
```

A Time Series Insights Explorerben navigáljon a **modell** lapra a bal oldali modell ikonra kattintva. Válassza a **típusok** lehetőséget, majd válassza a **JSON feltöltése** elemet:

![Feltöltés](./media/tutorial-configure-tsi/upload-type.png)

Válassza a **fájl kiválasztása** lehetőséget, válassza ki a korábban mentett JSON-t, majd válassza a **feltöltés** lehetőséget.

Megjelenik az újonnan definiált **hőmérséklet-vezérlő** típusa.

### <a name="create-a-hierarchy"></a>Hierarchia létrehozása

Hozzon létre egy hierarchiát a címkék a **TemperatureController** szülővel való rendszerezéséhez. A következő egyszerű példa egyetlen szinttel rendelkezik, de a IoT-megoldások gyakran több beágyazási szinttel rendelkeznek, hogy a contextualize a címkéket a szervezeten belüli fizikai és szemantikai helyükön belül.

Válassza a **hierarchiák** lehetőséget, majd válassza a **hierarchia hozzáadása** lehetőséget. A név mezőben adja meg az *eszköz-flotta* nevet, és hozzon létre egy, az *eszköz neve* nevű szintet. Kattintson a **Mentés** gombra.

![Hierarchia hozzáadása](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>A példányok helyes típushoz rendelése

Ezután módosítsa a példányok típusát, és helyezze őket a hierarchiába.

Válassza a **példányok** lapot, keresse meg az eszköz munkakészletét jelképező példányt, majd a jobb szélen kattintson a **Szerkesztés** ikonra:

![Példányok szerkesztése](./media/tutorial-configure-tsi/edit-instance.png)

Válassza a **típus** legördülő menüt, és válassza a **hőmérséklet-vezérlő** elemet. Adja meg a *defaultComponent, <your device name>* hogy frissítse a példány nevét, amely az eszközhöz társított összes legfelső szintű címkét jelképezi.

![Példány típusának módosítása](./media/tutorial-configure-tsi/change-type.png)

A Mentés gombra kattintva válassza ki a **példány mezői** lapot, és jelölje be az **eszköz flotta** mezőjét. Adja meg `<your device name> - Temp Controller` a telemetria csoportosítását, majd kattintson a **Mentés** gombra.

![Hozzárendelés hierarchia](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Az előző lépések megismétlésével rendelje hozzá a termosztát címkéit a megfelelő típushoz és hierarchiához.

## <a name="view-your-data"></a>Adatok megtekintése

Váltson vissza a diagram panelre, és bontsa ki az eszköz **flottáját > az eszközt**. Válassza a **thermostat1** lehetőséget, válassza ki a **hőmérséklet** változót, majd kattintson a **Hozzáadás** elemre az érték ábrázolásához. Végezze el ugyanezt a **thermostat2** és a **defaultComponent** **munkakészlet** értéknél.

![Thermostat2-példány típusának módosítása](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a különböző diagram-beállítási lehetőségekről, beleértve az intervallumok méretezését és az Y tengely vezérlőelemeket, tekintse meg a [Azure Time Series Insights Explorert](../time-series-insights/concepts-ux-panels.md).

* A környezet idősorozat-modelljének részletes áttekintését lásd: [Time Series Model in Azure Time Series Insights Gen2](../time-series-insights/concepts-model-overview.md) cikk.

* A lekérdezési API-k és az idősorozat-kifejezés szintaxisának betekintéséhez tekintse meg [Azure Time Series Insights Gen2 lekérdezési API-kat](/rest/api/time-series-insights/reference-query-apis.md).
