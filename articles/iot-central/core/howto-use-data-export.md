---
title: Adatok exportálása a IoT Centralból | Microsoft Docs
description: Az új adatexportálás használata a IoT-adatainak az Azure-ba és az egyéni Felhőbeli célhelyekre való exportálásához.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/04/2020
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: f51630154b77233aeb2587ac3a2d603c1da6fa4f
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036555"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-preview"></a>IoT-alapú adatexportálás a Felhőbeli célhelyekre adatexportálással (előzetes verzió)

> [!Note]
> Az örökölt adatexportálást keresi? [Itt](./howto-export-data.md)megtalálhatja az adatexportálási dokumentumokat. Az új adatexportálás és az örökölt adatexportálás közötti különbségek megismeréséhez tekintse meg az [összehasonlító táblázatot](#comparison-of-legacy-data-export-and-new-data-export).

Ez a cikk az Azure IoT Central új adatexportálási funkcióinak használatát ismerteti. Ezzel a szolgáltatással folyamatosan exportálhatja a szűrt és a dúsított IoT-adatait a Cloud servicesbe. Az adatexportálás segítségével közel valós időben leküldheti a változtatásokat a felhőalapú megoldás más részeire a meleg elérésű elemzések, az elemzések és a tárolás céljából. 

 Megteheti például a következőt:
-   Telemetria-és tulajdonság-változások folyamatos exportálása JSON formátumban a közel valós időben
-   Az adatstreamek szűrése az egyéni feltételeknek megfelelő speciális képességek exportálásához
-   Az adatfolyamok gazdagítása egyéni értékekkel és tulajdonságértékek az eszközről
-   Az Azure Event Hubs, Azure Service Bus, Azure Blob Storage és webhook-végpontok számára küldje el ezeket az adatcímeket

> [!Note]
> Amikor bekapcsolja az adatexportálást, a rendszer csak az adott pillanattól kezdve kapja meg az adatait. Jelenleg nem lehet lekérni az adatexportálást olyan időpontra, amikor az adatexportálás ki lett kapcsolva. Több korábbi adat megtartásához kapcsolja be a korai adatexportálást.

## <a name="prerequisites"></a>Előfeltételek

Az adatexportálás (előzetes verzió) használatához v3 alkalmazással kell rendelkeznie, és az adatexportálási engedélyekkel kell rendelkeznie.

## <a name="set-up-export-destination"></a>Exportálás célhelyének beállítása

Az exportálási célhelynek az adatexportálás konfigurálása előtt léteznie kell. Ezek a célhelyek elérhető típusai:
  - Azure Event Hubs
  - Azure Service Bus-üzenetsor
  - Azure Service Bus-témakör
  - Azure Blob Storage
  - Webhook

### <a name="create-an-event-hubs-destination"></a>Event Hubs célhely létrehozása

Ha nem rendelkezik meglévő Event Hubs-névtérrel az exportáláshoz, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új Event Hubs névteret a Azure Portalban](https://ms.portal.azure.com/#create/Microsoft.EventHub). További információt az [Azure Event Hubs dokumentációjában](../../event-hubs/event-hubs-create.md)olvashat.

2. Hozzon létre egy Event hubot a Event Hubs névtérben. Nyissa meg a névteret, és a felül található **+ Event hub** elemet választva hozzon létre egy Event hub-példányt.

3. Hozzon létre egy kulcsot, amelyet a IoT Central fog használni az adatexportálás beállításához. 
    - Kattintson a létrehozott Event hub-példányra. 
    - Kattintson a **Beállítások/közös hozzáférési házirendek**elemre. 
    - Hozzon létre egy új kulcsot, vagy válasszon olyan meglévő kulcsot, amely rendelkezik **küldési** engedéllyel. 
    - Másolja az elsődleges vagy a másodlagos kapcsolatok sztringjét. Ennek segítségével új célhelyet állíthat be IoT Centralban.

### <a name="create-a-service-bus-queue-or-topic-destination"></a>Service Bus üzenetsor vagy témakör célhelyének létrehozása

Ha nem rendelkezik meglévő Service Bus-névtérrel az exportáláshoz, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új Service Bus névteret a Azure Portalban](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). [Azure Service Bus dokumentációban](../../service-bus-messaging/service-bus-create-namespace-portal.md)bővebben is olvashat.

2. Az exportáláshoz használandó üzenetsor vagy témakör létrehozásához nyissa meg a Service Bus névteret, és válassza a **+ üzenetsor** vagy a **+ témakör**lehetőséget.

3. Hozzon létre egy kulcsot, amelyet a IoT Central fog használni az adatexportálás beállításához. 
    - Kattintson a létrehozott várólistára vagy témakörre. 
    - Kattintson a **Beállítások/közös hozzáférési házirendek**elemre. 
    - Hozzon létre egy új kulcsot, vagy válasszon olyan meglévő kulcsot, amely rendelkezik **küldési** engedéllyel. 
    - Másolja az elsődleges vagy a másodlagos kapcsolatok sztringjét. Ennek segítségével új célhelyet állíthat be IoT Centralban.

### <a name="create-an-azure-blob-storage-destination"></a>Azure Blob Storage célhely létrehozása

Ha nem rendelkezik meglévő Azure Storage-fiókkal az exportáláshoz, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új Storage-fiókot a Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). További információ: új [Azure Blob Storage-fiókok](https://aka.ms/blobdocscreatestorageaccount) vagy [Azure Data Lake Storage v2 Storage-fiókok](../../storage/blobs/data-lake-storage-quickstart-create-account.md)létrehozása. Az adatexportálás csak a blokk blobokat támogató Storage-fiókokba tud írni. A következő lista az ismert kompatibilis Storage-fiókok típusait mutatja be:

    |Teljesítményszint|Fiók típusa|
    |-|-|
    |Standard|általános célú v2|
    |Standard|általános célú v1|
    |Standard|Blob Storage|
    |Prémium|BLOB Storage letiltása|

2. Hozzon létre egy tárolót a Storage-fiókban. Nyissa meg a Storage-fiókját. A **blob szolgáltatás**alatt válassza a **Tallózás Blobok**lehetőséget. Egy új tároló létrehozásához kattintson a felül található **+ tároló** elemre.

3. Létrehoz egy kapcsolati karakterláncot a Storage-fiókhoz a **Beállítások/hozzáférési kulcsok menüpontban**. Másolja az egyiket a két összekapcsolási karakterláncból.

### <a name="create-a-webhook-endpoint"></a>Webhook-végpont létrehozása
Megadhat egy nyilvánosan elérhető HTTP-végpontot az exportálandó adatexportáláshoz. A RequestBin használatával létrehozhat egy teszt webhook-végpontot. Ne feledje, hogy a kérések leszabályozása előtt a RequestBin rendelkezik egy beállított kérelem korláttal.

1. Nyissa meg a [RequestBin](https://requestbin.net/).
2. Hozzon létre egy új RequestBin, és másolja a bin URL-címét.

## <a name="set-up-data-export"></a>Az adatexportálás beállítása

Most, hogy van egy célhelye az adatexportáláshoz, az alábbi lépéseket követve állíthatja be az adatexportálást.

1. Jelentkezzen be IoT Central alkalmazásba.

2. A bal oldali panelen válassza az **adatexportálás**elemet.

    > [!Tip]
    > Ha nem látja az **adatexportálást** a bal oldali ablaktáblán, akkor nincs engedélye az adatexportálás konfigurálására az alkalmazásban. Az adatexportálás beállításához forduljon a rendszergazdához.

3. Kattintson az **+ új Exportálás** gombra. 

4. Adja meg az új Exportálás megjelenítendő nevét, és győződjön meg arról, hogy az **engedélyezett** váltógomb be van kapcsolva az adatforgalomhoz.

## <a name="1-choose-the-type-of-data-to-export"></a>1. Válassza ki az exportálandó adattípust
A különböző típusú adattípusok folyamatos exportálását is választhatja. A támogatott adattípusok a következők:

| Adattípus | Leírás | Adatformátum |
| :------------- | :---------- | :----------- |
|  Telemetria | Telemetria-üzenetek exportálása az eszközökről közel valós időben. Minden exportált üzenet tartalmazni fogja az eredeti üzenet teljes tartalmát, normalizálva.   |  [Telemetria-üzenet formátuma](#telemetry-format)   |
| Tulajdonságok módosítása | A módosításokat az eszköz és a felhő tulajdonságai között közel valós időben exportálhatja. A csak olvasható eszköz tulajdonságainál a jelentett értékek módosításai lesznek exportálva. Az írási és olvasási tulajdonságok esetében a rendszer a jelentett és a kívánt értékeket is exportálja. | [Tulajdonság-módosítási üzenet formátuma](#property-changes-format) |

## <a name="2-optional-add-filters"></a>2. (nem kötelező) szűrők hozzáadása
Szűrők hozzáadásával csökkentheti a szűrési feltételek alapján exportált adatmennyiséget. Az exportáláshoz különböző típusú szűrők érhetők el az egyes adattípusokhoz.

### <a name="telemetry-filters"></a>Telemetria szűrők
  - **Képesség szűrő**: Ha egy telemetria elemet választ a legördülő menüben, az exportált adatfolyam csak olyan telemetria fog tartalmazni, amelyek megfelelnek a szűrési feltételnek. Ha egy eszköz vagy egy Felhőbeli tulajdonság elemet választ a legördülő menüben, az exportált adatfolyam csak olyan eszközök telemetria fogja tartalmazni, amelyek a szűrési feltételnek megfelelő tulajdonságokkal rendelkeznek.
  - **Üzenet tulajdonságának szűrője**: az eszköz SDK-kat használó eszközök lehetővé teszik az *üzenet tulajdonságainak* vagy az *alkalmazás tulajdonságainak* az egyes telemetria üzenetek küldését, azaz a kulcs-érték párok egy zacskóját, amely általában az üzenet egyéni azonosítókkal való címkézéséhez használatos. Létrehozhat egy üzenet-tulajdonság szűrőt úgy, hogy beírja a keresett üzenet tulajdonság kulcsát, és megadta a feltételt. Csak azokat a telemetria-üzeneteket exportálja a rendszer, amelyeknek a megadott szűrési feltételnek megfelelő üzenet-tulajdonságokkal rendelkeznek. Csak a karakterlánc-összehasonlító operátorok támogatottak (egyenlő, nem egyenlő, nem tartalmaz, nem tartalmaz, nem létezik). [További információ az alkalmazás tulajdonságairól IoT hub docs-ból](../../iot-hub/iot-hub-devguide-messages-construct.md).

### <a name="property-changes-filters"></a>Tulajdonságok módosítása szűrők
**Tulajdonság szűrője**: válasszon egy tulajdonságot a legördülő listából, és az exportált adatfolyam csak a szűrési feltételnek megfelelő kijelölt tulajdonság módosításait fogja tartalmazni.

## <a name="3-optional-add-enrichments"></a>3. (nem kötelező) bővítések hozzáadása
Bővítheti az exportált üzeneteket a kulcs-érték párokban található további metaadatokkal gazdagítva. A telemetria és a tulajdonsághoz elérhető alkoholtartalom-NÖVELÉSEK az alábbi módokon változnak:
  - **Egyéni karakterlánc**: egyéni statikus karakterláncot hoz létre minden üzenethez. Írjon be egy tetszőleges kulcsot, és adjon meg egy karakterlánc-értéket.
  - **Tulajdonság**: hozzáadja az aktuális eszköz jelentett tulajdonságát vagy a Felhőbeli tulajdonság értékét az egyes üzenetekhez. Adjon meg egy kulcsot, és válasszon ki egy eszközt vagy egy Felhőbeli tulajdonságot. Ha az exportált üzenet olyan eszközről származik, amely nem rendelkezik a megadott eszköz-vagy felhő-tulajdonsággal, az exportált üzenet nem fog megjelenni.

## <a name="4-add-destinations"></a>4. célhelyek hozzáadása
Hozzon létre egy új célhelyet, vagy adjon hozzá egy már létrehozott célhelyet. 
  
1. Kattintson az **új cél létrehozása** hivatkozásra. Adja meg a következő információkat:
    - **Cél neve**: a célhely megjelenítendő neve IoT Central
    - **Cél típusa**: válassza ki a célhely típusát. Ha még nem tette meg, [állítsa be az Exportálás célhelyét](#set-up-export-destination)
    - Az Azure Event Hubs Azure Service Bus üzenetsor vagy témakör esetében illessze be az erőforráshoz tartozó kapcsolatok karakterláncát. 
    - Az Azure Blob Storage esetében illessze be az erőforráshoz tartozó kapcsolatok karakterláncát, és adja meg a tároló nevét (megkülönbözteti a kis-és nagybetűket).
    - Webhook esetén illessze be a webhook-végpont visszahívási URL-címét. 
    - Kattintson a **Létrehozás** gombra

2. Kattintson a **+ cél** lehetőségre, és válasszon egy célhelyet a legördülő listából. Legfeljebb 5 célhelyet adhat hozzá egyetlen exportáláshoz.

3. Miután végzett az Exportálás beállításával, kattintson a **Mentés**gombra. Néhány perc elteltével az adatai megjelennek a célhelyen.

## <a name="export-contents-and-format"></a>Tartalom és formátum exportálása

### <a name="azure-blob-storage-destination"></a>Azure Blob Storage célhely

Az adatexportálás percenként egyszer történik, és minden olyan fájl, amely a legutóbbi exportált fájl óta megjelenő változási köteget tartalmazza. Az exportált adatfájlok JSON formátumú három mappába kerülnek. A Storage-fiók alapértelmezett elérési útjai a következők:

- Telemetria: _{Container}/{app-id}/{partition_id}/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Tulajdonságok módosítása: _{Container}/{app-id}/{partition_id}/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_

Az exportált fájlok tallózásához a Azure Portalban navigáljon a fájlhoz, és válassza a **blob szerkesztése** lapot.

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Azure Event Hubs és Azure Service Bus célhelyek

Az adatexportálás közel valós idejű. Az adattartalom az üzenet törzsében van, és JSON formátumú, UTF-8 kódolású. 

Az üzenethez tartozó jegyzetek vagy Rendszertulajdonságok táskájában megkeresheti,, és megadhatja az üzenet `iotcentral-device-id` `iotcentral-application-id` `iotcentral-message-source` `iotcentral-message-type` törzsének megfelelő mezőivel megegyező értékeket.

### <a name="webhook-destination"></a>Webhook célhelye
A webhookok rendeltetési helyein a rendszer közel valós időben is exportálja az adategységeket. Az üzenet törzsében lévő adatértékek formátuma megegyezik a Event Hubs és az Service Bus.


## <a name="telemetry-format"></a>Telemetria formátuma
Minden exportált üzenet a teljes üzenet normalizált formáját tartalmazza, amely az üzenet törzsében a JSON formátumban elküldött eszköz UTF-8-ként van kódolva. Az egyes üzenetekben található további információk a következők:

- `applicationId`IoT Central alkalmazás
- `messageSource`a telemetria-adatexportálás *telemetria*
- `deviceId`a telemetria üzenetet küldő eszköz
- `schema`a hasznos adatok sémájának neve és verziója
- `templateId`az eszközhöz rendelt eszköz-sablon azonosítója
- `enrichments`az exportáláskor beállított alkoholtartalom-növelés
- `messageProperties`a további adatok, amelyeket az eszköz az üzenet mellett küldött. Ez más néven alkalmazás- *Tulajdonságok*, további [információ IoT hub docs-ról](../../iot-hub/iot-hub-devguide-messages-construct.md).

Event Hubs és Service Bus esetén a IoT Central gyorsan exportál egy új üzenetet, miután megkapta az üzenetet az eszközről.

A blob Storage esetében az üzenetek kötegbe kerülnek, és percenként egyszer lesznek exportálva.

Az alábbi példa egy exportált telemetria üzenetet mutat be:

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

## <a name="property-changes-format"></a>Tulajdonság módosításainak formátuma

Minden üzenet vagy rekord egy eszköz vagy egy Felhőbeli tulajdonság egyik módosítását jelöli. Az eszköz tulajdonságainál csak a jelentett érték változásai lesznek exportálva külön üzenetként. Az exportált üzenetben szereplő további információk a következők:

- `applicationId`IoT Central alkalmazás
- `messageSource`az exportálási tulajdonságok adatváltozásának *tulajdonságai*
- `messageType`amely vagy *cloudPropertyChange* vagy *devicePropertyDesiredChange*, *devicePropertyReportedChange*
- `deviceId`azon eszköz, amelynek tulajdonságai megváltoztak
- `schema`a hasznos adatok sémájának neve és verziója
- `templateId`az eszközhöz rendelt eszköz-sablon azonosítója
- `enrichments`az exportáláskor beállított alkoholtartalom-növelés

Event Hubs és Service Bus esetén IoT Central az új üzenetek adatait az Event hub-ba vagy a Service Bus üzenetsor vagy témakörbe exportálja közel valós időben.

A blob Storage esetében az üzenetek kötegbe kerülnek, és percenként egyszer lesznek exportálva.

Az alábbi példa egy exportált tulajdonság-módosítási üzenetet mutat be az Azure Blob Storageban.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "fieldName": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-new-data-export"></a>A korábbi adatexportálás és az új adatexportálás összehasonlítása
Ez egy táblázat, amely kiemeli az örökölt adatexportálás és az új adatexportálás közötti különbségeket. [Itt](howto-export-data.md)megismerheti az örökölt adatexportálást.

| Képességek  | Örökölt adatexportálás | Új adatexportálás |
| :------------- | :---------- | :----------- |
| Elérhető adattípusok | Telemetria, eszközök, eszközök sablonjai | Telemetria, tulajdonságok módosításai |
| Szűrés | None | Az exportált adattípustól függ. Telemetria, szűrés telemetria, üzenet tulajdonságai, tulajdonságértékek alapján |
| Modellbővítések | None | Gazdagítsa az eszköz egyéni sztringjét vagy tulajdonságának értékét |
| Célhelyek | Azure Event Hubs, Azure Service Bus várólisták és témakörök, Azure Blob Storage | Ugyanaz, mint a régebbi adatexportáláshoz és webhookokhoz| 
| Támogatott alkalmazások | V2, V3 | Csak v3 |
| Jelentős korlátok | 5 export/alkalmazás, 1 cél/exportálás | 10 exportálás – cél kapcsolatok száma alkalmazás szerint | 

## <a name="next-steps"></a>További lépések

Most, hogy már tudja, hogyan használhatja az új adatexportálást, folytassa a következő lépéssel:

> [!div class="nextstepaction"]
> [Az Analytics használata a IoT Centralban](./howto-create-analytics.md)
