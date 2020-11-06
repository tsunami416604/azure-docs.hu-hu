---
title: Az Azure-IoT Plug and Play eszköz telemetria tárolásához és elemzéséhez használja a Time Series Insightst | Microsoft Docs
description: Time Series Insights környezet beállítása és a IoT Hub csatlakoztatása a telemetria megtekintéséhez és elemzéséhez a IoT Plug and Play eszközökről.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: aa99b9059fe8e3cd5b0dfe6f7e62bd02012fd144
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422263"
---
# <a name="tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>Oktatóanyag: Time Series Insights Gen2 létrehozása és csatlakoztatása a IoT Plug and Play eszköz telemetria tárolásához, megjelenítéséhez és elemzéséhez

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és konfigurálhat megfelelően egy [Azure Time Series Insights Gen2](https://docs.microsoft.com/azure/time-series-insights/overview-what-is-tsi) (ÁME) környezetet a IoT Plug and Play megoldással való integrációhoz. Az ÁME használatával az idősoros adatok gyűjtése, feldolgozása, tárolása, lekérdezése és megjelenítése eszközök internetes hálózata (IoT) skálán végezhető el.

Először is üzembe kell helyeznie egy ÁME-környezetet, és össze kell kötnie a IoT Hub adatfolyam-eseményforrásként. Ezt követően a modell szinkronizálásával is elkészítheti az ÁME-környezet idősorozat-modelljét a hőmérséklet-vezérlő és a termosztátos eszközökhöz használt [digitális Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) modellből származó fájlok alapján.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Ha el szeretné kerülni az Azure CLI helyi telepítésének követelményét, használhatja a Azure Cloud Shell a Cloud Services beállításához.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="time-series-id-selection"></a>Idősorozat-azonosító kiválasztása

Az ÁME-környezet kiépítés alatt ki kell választania egy idősorozat-azonosítót. A megfelelő idősorozat-azonosító kiválasztása kritikus fontosságú, mivel a tulajdonság nem változtatható meg, és a beállítása után nem módosítható. Az idősorozat-azonosító kiválasztása olyan, mint egy adatbázis partíciós kulcsának kiválasztása. Általában a terminálszolgáltatási AZONOSÍTÓnak az eszköz-modell levél csomópontjának kell lennie. Más szóval általában ki kell választania a telemetria kibocsátó legrészletesebb eszköz vagy érzékelő azonosító tulajdonságát.

IoT Plug and Play felhasználóként a terminálszolgáltatási azonosító kiválasztására [vonatkozó kérdés](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#component) az eszköz modelljeinek jelenléte. 

![TS-azonosító kiválasztása](./media/tutorial-configure-tsi/ts-id-selection-pnp.png)

* Ha elvégezte a gyors üzembe helyezést, és a IoT Hub eszköz a [termosztátot](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json)jelöli, használja `iot-hub-connection-device-id` TS-azonosítóként.

* Ha a multi-Component [TemperatureController](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json)kapcsolatos oktatóanyagok valamelyikét végrehajtotta, használjon egy összetett kulcsot az alábbi szakaszban  `iot-hub-connection-device-id, dt-subject`

## <a name="provision-your-azure-time-series-insights-gen2-environment"></a>A Azure Time Series Insights Gen2-környezet kiépítése

Az alábbi parancs a következő műveleteket végzi el:

* Létrehoz egy Azure Storage-fiókot a környezete [hűtőházi tárolójához](https://docs.microsoft.com/azure/time-series-insights/concepts-storage#cold-store), amely hosszú távú adatmegőrzésre és a múltbeli adatok elemzésére szolgál.
  * Cserélje le a `mytsicoldstore` nevet a fiókjához tartozó egyedi névre.
* Egy Azure Time Series Insights Gen2-környezetet hoz létre, beleértve a 7 napos megőrzési időtartamú meleg tárolást és a végtelen megőrzéshez szükséges hideg tárolót. 
  * Cserélje le `my-tsi-env` az-t az ÁME-környezet egyedi nevére 
  * Cserélje le a helyére a `my-pnp-resourcegroup` beállítás során használt erőforráscsoport nevét.
  * Cserélje le a `my-ts-id-property` elemet a terminálszolgáltatási azonosító tulajdonság értékére a fenti kiválasztási feltételek alapján

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties my-ts-id-property --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

Most konfigurálja a korábban létrehozott IoT Hub a környezete [eseményforrásként](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingestion-event-sources). Ha az eseményforrás csatlakoztatva van, az ÁME megkezdi az események indexelését a központból, a legkorábbi eseménytől kezdve a várólistában.

Először hozzon létre egy egyedi fogyasztói csoportot a IoT Hub az ÁME-környezethez. Cserélje le a helyére a `my-pnp-hub` korábban használt IoT hub nevét.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group 
```

A IoT Hub összekapcsolásához. Cserélje `my-pnp-resourcegroup` le `my-pnp-hub` a, a és a `my-tsi-env` értéket a megfelelő értékekre.

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```
Navigáljon az erőforráscsoporthoz a [Azure Portalban](https://portal.azure.com) , és válassza ki az újonnan létrehozott Time Series Insights-környezetét. Látogasson el a példány áttekintésében látható *Time Series Insights Explorer URL-címére* .

![A portál áttekintő oldala](./media/tutorial-configure-tsi/view-environment.png)

Az Explorerben a két termosztátot kell látnia a "minden hierarchia" alatt. Ezután az Ön eszköz-modelljén alapuló idősorozat-modellt is kiállíthatja.

![Explorer – 1. nézet](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="model-synchronization-between-digital-twins-definition-language-and-time-series-insights-gen2"></a>Modell szinkronizálása a digitális ikrek definíciós nyelve és a Time Series Insights Gen2 között

Ezután lefordítja a DTDL az Azure Time Series Insights (ÁME) eszköz modelljére. Az ÁME idősorozat-modellje egy szemantikai modellezési eszköz az ÁME-n belüli contextualization. A Time Series-modell három alapvető összetevőből áll:

* [Idősorozat-modell példányai](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-instances). A példányok magukban foglalhatják magukban az idősorozatok virtuális ábrázolását. A példányokat a terminálszolgáltatási azonosító egyedileg azonosítja.
* [Idősorozat-modell hierarchiák](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-hierarchies). A hierarchiák a tulajdonságok nevét és a kapcsolataik megadásával rendezik a példányokat.
* [Idősorozat-modell típusa](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-types). A típusok segítségével [változók](https://docs.microsoft.com/azure/time-series-insights/concepts-variables) vagy képletek definiálhatók a számítások végrehajtásához. A típusok egy adott példánnyal vannak társítva.

> [!NOTE]
> Az alábbi példák a multi-Component TemperatureController vonatkoznak.

### <a name="define-your-types"></a>A típusok meghatározása

Megkezdheti az adatbevitelt Azure Time Series Insights Gen2 anélkül, hogy előre definiált egy modellt. Amikor a telemetria megérkezik, az ÁME megkísérli automatikusan feloldani a Time Series-példányokat a terminálszolgáltatási azonosító tulajdonság értéke alapján. Az összes példány az *alapértelmezett típushoz* lesz rendelve. A modellek megjelenítéséhez manuálisan létre kell hoznia egy új típust. Az alábbi képen egy egyszerű módszer látható egy DTDL-modell és egy TSM-típus szinkronizálásához:

![DTDL TSM típusa](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

* A digitális Twin Model-azonosító (DTMI) lesz a típusazonosító
* A típus neve lehet a modell neve vagy a megjelenített név.
* A modell leírása lesz a típus leírása
* Minden olyan telemetria-összetevőhöz, amely numerikus sémával rendelkezik, legalább egy típus változót létre kell hozni. 
  * Változókhoz csak numerikus adattípusok használhatók, de ha egy értéket elemező sztringként küld el, például `"0"` használhat [konverziós](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) függvényt, például: `toDouble`
* A változó neve lehet a telemetria neve vagy a megjelenítendő név.
* A változó idősorozat kifejezésének (TSX) meghatározásakor tekintse meg a telemetria nevét a dróton, és az adattípust.

> [!NOTE]
> Ez a példa csak két változót mutat be – egy összesítést és egy számot, de mindegyik típus legfeljebb 100 lehet. A különböző változók hivatkozhatnak ugyanarra a telemetria értékre, hogy szükség szerint különböző calucaultions végezzenek. A szűrők, összesítések és skaláris függvények teljes listájáért tekintse meg a [Time Series Insights Gen2 Time Series kifejezés szintaxisának](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) dokumentációját.

Nyissa meg a választott szövegszerkesztőt, és mentse az alábbi JSON-t a helyi meghajtóra:

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:Thermostat;1",
      "name": "Thermostat",
      "description": "Reports current temperature and provides desired temperature control.",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.temperature.Double"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

A Time Series Insights Explorerben navigáljon a modell lapra a bal oldali modell ikonra kattintva. Kattintson a **types (típusok** ) elemre, és kattintson a **JSON feltöltés** :

![Feltöltés](./media/tutorial-configure-tsi/upload-type.png)

Válassza a **fájl kiválasztása** lehetőséget, válassza ki a korábban mentett JSON-t, majd kattintson a **feltöltés** gombra.

Ekkor meg kell jelennie az újonnan definiált termosztát típusának.

### <a name="optional---create-a-hierarchy"></a>Nem kötelező – hierarchia létrehozása

Szükség esetén létrehozhat egy hierarchiát, amely a két termosztát-összetevőt a TemeraptureController szülője alapján rendezi.

Kattintson a *hierarchiák* elemre, majd válassza *a hierarchia hozzáadása* elemet. Adja meg `Device Fleet` a nevet, és hozzon létre egy nevű szintet, `Device Name` majd kattintson a *Mentés* gombra.

![Hierarchia hozzáadása](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>A példányok helyes típushoz rendelése

A következő lépésben módosítania kell a példányok típusát, és opcionálisan fekvő azokat a hierarchián belül.

Válassza a *példányok* lapot, és kattintson a jobb szélen a *Szerkesztés* ikonra.

![Példányok szerkesztése](./media/tutorial-configure-tsi/edit-instance.png)

Kattintson a típus legördülő listára, és válassza a elemet `Thermostat` . 

![Példány típusának módosítása](./media/tutorial-configure-tsi/change-type.png)

Ha létrehozott egy hierarchiát, válassza a *példány mezői* lehetőséget, és jelölje be a `Device Fleet` jelölőnégyzetet. Adja meg `Temperature Controller` a fölérendelt eszköz értékét, majd kattintson a Save ( *Mentés* ) gombra.

![Hozzárendelés hierarchia](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Ismételje meg a fenti lépéseket a második Termosztátnál.

### <a name="view-your-data"></a>Adatok megtekintése

Térjen vissza a diagram panelre, és bontsa ki az eszköz flottáját és a TemperatureController. Kattintson a thermostat1 elemre, válassza ki a `Temperature` változót, majd kattintson a *Hozzáadás* gombra az érték ábrázolásához. Tegye ugyanezt a thermostat2.

![Thermostat2-példány típusának módosítása](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a különböző diagram-beállítási lehetőségekről, beleértve az intervallumok méretezését és az Y tengely vezérlőelemeket, tekintse meg a [Azure Time Series Insights Explorer ](https://docs.microsoft.com/azure/time-series-insights/concepts-ux-panels) dokumentációját.

* A környezet idősorozat-modelljének részletes áttekintését [ebben](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview) a cikkben találja.

* A lekérdezési API-k és az idősorozat-kifejezés szintaxisának [kiválasztásához válassza a elemet](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis).




