---
title: Adatok exportálása az Azure IoT Central (előzetes verzió) | Microsoft Docs
description: Az új adatexportálás használata a IoT-adatainak az Azure-ba és az egyéni Felhőbeli célhelyekre való exportálásához.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/02/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: contperfq1
ms.openlocfilehash: 0a07d7e57ced5e2cd9457dc51ebcd355306fc48e
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89461935"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-preview"></a>IoT-alapú adatexportálás a Felhőbeli célhelyekre adatexportálással (előzetes verzió)

> [!Note]
> Ez a cikk a IoT Central előnézeti adatexportálási funkcióit ismerteti.
>
> - Az örökölt adatexportálási funkciókkal kapcsolatos információkért lásd: [IoT-adatok exportálása a Felhőbeli célhelyekre adatexportálás (örökölt) használatával](./howto-export-data-legacy.md).
> - Az előzetes verziójú adatexportálás és az örökölt adatexportálási funkciók közötti különbségekről az alábbi [összehasonlító táblázatban](#comparison-of-legacy-data-export-and-preview-data-export) talál további információt.

Ez a cikk azt ismerteti, hogyan használható az új adatexportálási Előnézet funkció az Azure IoT Centralban. Ezzel a szolgáltatással folyamatosan exportálhatja a szűrt és a dúsított IoT-adatait a IoT Central alkalmazásból. Az adatexportálás leküldi a közel valós idejű változásokat a felhőalapú megoldás más részeire a meleg elérésű elemzések, az elemzések és a tárolás érdekében.

Megteheti például a következőt:

- A telemetria-és a tulajdonság-módosítások folyamatos exportálása JSON formátumban, közel valós időben.
- Az adatstreamek szűrésével exportálhatja az egyéni feltételekkel egyező adatforgalmat.
- Az adatstreamek az eszköz egyéni értékeivel és tulajdonságértékek gazdagítása.
- Az Azure Event Hubs, a Azure Service Bus, az Azure Blob Storage és a webhook-végpontok számára küldje el az összes olyan célhelyet.

> [!Tip]
> Amikor bekapcsolja az adatexportálást, a rendszer csak az adott pillanattól kezdve kapja meg az adatait. Jelenleg nem lehet lekérni az adatexportálást olyan időpontra, amikor az adatexportálás ki lett kapcsolva. Több korábbi adat megtartásához kapcsolja be a korai adatexportálást.

## <a name="prerequisites"></a>Előfeltételek

Az előzetes verziójú adatexportálási funkciók használatához [v3 alkalmazás](howto-get-app-info.md)szükséges, és rendelkeznie kell az [adatexportálási](howto-manage-users-roles.md) engedéllyel.

## <a name="set-up-export-destination"></a>Exportálás célhelyének beállítása

Az exportálási célhelynek az adatexportálás konfigurálása előtt léteznie kell. Jelenleg a következő típusú célhelyek érhetők el:

- Azure Event Hubs
- Azure Service Bus-üzenetsor
- Azure Service Bus-témakör
- Azure Blob Storage
- Webhook

### <a name="create-an-event-hubs-destination"></a>Event Hubs célhely létrehozása

Ha nem rendelkezik meglévő Event Hubs-névtérrel az exportáláshoz, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új Event Hubs névteret a Azure Portalban](https://ms.portal.azure.com/#create/Microsoft.EventHub). További információt az [Azure Event Hubs dokumentációjában](../../event-hubs/event-hubs-create.md)olvashat.

1. Hozzon létre egy Event hubot a Event Hubs névtérben. Nyissa meg a névteret, és a felül található **+ Event hub** elemet választva hozzon létre egy Event hub-példányt.

1. Hozzon létre egy kulcsot, amelyet akkor kell használni, ha az adatexportálást IoT Centralban állítja be:

    - Válassza ki a létrehozott Event hub-példányt.
    - Válassza a **beállítások > a közös hozzáférési szabályzatok**lehetőséget.
    - Hozzon létre egy új kulcsot, vagy válasszon olyan meglévő kulcsot, amely rendelkezik **küldési** engedéllyel.
    - Másolja az elsődleges vagy a másodlagos kapcsolatok sztringjét. Ezt a kapcsolódási karakterláncot használva új célhelyet állíthat be IoT Centralban.

### <a name="create-a-service-bus-queue-or-topic-destination"></a>Service Bus üzenetsor vagy témakör célhelyének létrehozása

Ha nem rendelkezik meglévő Service Bus-névtérrel az exportáláshoz, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új Service Bus névteret a Azure Portalban](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). [Azure Service Bus dokumentációban](../../service-bus-messaging/service-bus-create-namespace-portal.md)bővebben is olvashat.

1. Az exportáláshoz használandó üzenetsor vagy témakör létrehozásához nyissa meg a Service Bus névteret, és válassza a **+ üzenetsor** vagy a **+ témakör**lehetőséget.

1. Hozzon létre egy kulcsot, amelyet akkor kell használni, ha az adatexportálást IoT Centralban állítja be:

    - Válassza ki a létrehozott várólistát vagy témakört.
    - Válassza a **Beállítások/közös hozzáférési szabályzatok**lehetőséget.
    - Hozzon létre egy új kulcsot, vagy válasszon olyan meglévő kulcsot, amely rendelkezik **küldési** engedéllyel.
    - Másolja az elsődleges vagy a másodlagos kapcsolatok sztringjét. Ezt a kapcsolódási karakterláncot használva új célhelyet állíthat be IoT Centralban.

### <a name="create-an-azure-blob-storage-destination"></a>Azure Blob Storage célhely létrehozása

Ha nem rendelkezik meglévő Azure Storage-fiókkal az exportáláshoz, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új Storage-fiókot a Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). További információ: új [Azure Blob Storage-fiókok](https://aka.ms/blobdocscreatestorageaccount) vagy [Azure Data Lake Storage v2 Storage-fiókok](../../storage/blobs/data-lake-storage-quickstart-create-account.md)létrehozása. Az adatexportálás csak a blokk blobokat támogató Storage-fiókokba tud írni. A következő lista az ismert kompatibilis Storage-fiókok típusait mutatja be:

    |Teljesítményszint|Fiók típusa|
    |-|-|
    |Standard|általános célú v2|
    |Standard|általános célú v1|
    |Standard|Blob Storage|
    |Prémium|BLOB Storage letiltása|

1. A Storage-fiókban lévő tároló létrehozásához nyissa meg a Storage-fiókját. A **blob szolgáltatás**alatt válassza a **Tallózás Blobok**lehetőséget. Egy új tároló létrehozásához kattintson a felül található **+ tároló** elemre.

1. A Storage-fiókhoz tartozó kapcsolati karakterlánc létrehozásához nyissa meg a **beállítások > hozzáférési kulcsok lehetőséget**. Másolja az egyiket a két összekapcsolási karakterláncból.

### <a name="create-a-webhook-endpoint"></a>Webhook-végpont létrehozása

Az adatexportálás nyilvánosan elérhető HTTP webhook-végpontba is elvégezhető. A [RequestBin](https://requestbin.net/)használatával létrehozhat egy teszt webhook-végpontot. RequestBin-szabályozás a kérelem korlátjának elérésekor:

1. Nyissa meg a [RequestBin](https://requestbin.net/).
2. Hozzon létre egy új RequestBin, és másolja a **bin URL-címét**. Ezt az URL-címet használja az adatexportálás tesztelésekor.

## <a name="set-up-data-export"></a>Az adatexportálás beállítása

Most, hogy van egy célhelye az adatai exportálásához, állítsa be az adatexportálást a IoT Central alkalmazásban:

1. Jelentkezzen be IoT Central alkalmazásba.

1. A bal oldali panelen válassza az **adatexportálás (előzetes verzió)** lehetőséget.

    > [!Tip]
    > Ha nem látja az **adatexportálás (előzetes verzió)** lehetőséget a bal oldali ablaktáblán, akkor nincs engedélye az adatexportálás konfigurálására az alkalmazásban. Az adatexportálás beállításához forduljon a rendszergazdához.

1. Válassza az **+ új Exportálás**lehetőséget.

1. Adja meg az új Exportálás megjelenítendő nevét, és győződjön meg arról, hogy az adatexportálás **engedélyezve**van.

1. Válassza ki az exportálandó adattípust. A következő táblázat a támogatott adatexportálási típusokat sorolja fel:

    | Adattípus | Leírás | Adatformátum |
    | :------------- | :---------- | :----------- |
    |  Telemetria | Telemetria-üzenetek exportálása az eszközökről közel valós időben. Minden exportált üzenet tartalmazza az eredeti üzenet teljes tartalmát, normalizálva.   |  [Telemetria-üzenet formátuma](#telemetry-format)   |
    | Tulajdonságok módosítása | A módosításokat az eszköz és a felhő tulajdonságai között közel valós időben exportálhatja. A csak olvasható eszköz tulajdonságainál a jelentett értékek módosításai lesznek exportálva. Az írási és olvasási tulajdonságok esetében a jelentett és a kívánt értékeket is exportálja a rendszer. | [Tulajdonság-módosítási üzenet formátuma](#property-changes-format) |

1. Szükség esetén szűrők hozzáadásával csökkentheti az exportált adatmennyiséget. Az egyes adatexportálási típusokhoz különböző típusú szűrők érhetők el:

    A telemetria szűréséhez használja a következőt:

    - **Képesség szűrő**: Ha egy telemetria elemet választ a **név** legördülő menüben, az exportált adatfolyam csak olyan telemetria tartalmaz, amelyek megfelelnek a szűrési feltételnek. Ha a **név** legördülő menüben kiválasztja az eszköz vagy a felhő tulajdonság elemét, az exportált adatfolyam csak a telemetria megfelelő tulajdonságokkal rendelkező eszközökről származó eszközöket tartalmaz.
    - **Üzenet tulajdonságai szűrő**: az eszköz SDK-kat használó eszközök az egyes telemetria üzenetekben küldhetnek *üzenet-tulajdonságokat* vagy *alkalmazás-tulajdonságokat* . A tulajdonságok olyan kulcs-érték párok táska, amelyek egyéni azonosítókkal címkézik az üzenetet. Az üzenet tulajdonságai szűrő létrehozásához adja meg a keresett üzenet tulajdonság kulcsát, és adjon meg egy feltételt. Csak a megadott szűrési feltételnek megfelelő tulajdonságokkal rendelkező telemetria exportálja a rendszer. A következő karakterlánc-összehasonlító operátorok támogatottak: egyenlő, nem egyenlő, nem tartalmaz, nem tartalmaz, létezik, nem létezik. [További információ az alkalmazás tulajdonságairól IoT hub docs-ból](../../iot-hub/iot-hub-devguide-messages-construct.md).

    A tulajdonságok változásainak szűréséhez használjon egy **képesség szűrőt**. Válasszon egy tulajdonságot a legördülő menüben. Az exportált adatfolyam csak a szűrési feltételnek megfelelő kijelölt tulajdonság módosításait tartalmazza.

1. Igény szerint az exportált üzeneteket további kulcs-érték párokkal gazdagíthatja. A következő dúsítások érhetők el a telemetria és a tulajdonsághoz az adatexportálási típusok módosításához:

    - **Egyéni karakterlánc**: egyéni statikus karakterláncot hoz létre minden üzenethez. Írjon be egy tetszőleges kulcsot, és adjon meg egy karakterlánc-értéket.
    - **Tulajdonság**: hozzáadja az aktuális eszköz jelentett tulajdonságát vagy a Felhőbeli tulajdonság értékét az egyes üzenetekhez. Adjon meg egy kulcsot, és válasszon ki egy eszközt vagy egy Felhőbeli tulajdonságot. Ha az exportált üzenet olyan eszközről származik, amely nem rendelkezik a megadott tulajdonsággal, az exportált üzenet nem kapja meg a dúsítást.

1. Adjon hozzá egy új célhelyet, vagy adjon hozzá egy már létrehozott célhelyet. Válassza az **új létrehozása** hivatkozást, és adja hozzá a következő információkat:

    - **Cél neve**: a célhely megjelenítendő neve IoT Centralban.
    - **Cél típusa**: válassza ki a célhely típusát. Ha még nem állította be a célhelyet, olvassa el az [Exportálás célhelyének beállítása](#set-up-export-destination)című témakört.
    - Az Azure Event Hubs Azure Service Bus üzenetsor vagy témakör esetében illessze be az erőforráshoz tartozó kapcsolatok karakterláncát.
    - Az Azure Blob Storage esetében illessze be az erőforráshoz tartozó kapcsolatok karakterláncát, és adja meg a kis-és nagybetűket megkülönböztető tároló nevét.
    - Webhook esetén illessze be a webhook-végpont visszahívási URL-címét.
    - Kattintson a **Létrehozás** gombra.

1. Válassza a **+ cél** lehetőséget, és válasszon egy célhelyet a legördülő listából. Legfeljebb öt célhelyet adhat hozzá egyetlen exportáláshoz.

1. Ha végzett az Exportálás beállításával, válassza a **Mentés**lehetőséget. Néhány perc elteltével az adatai megjelennek a célhelyen.

## <a name="export-contents-and-format"></a>Tartalom és formátum exportálása

### <a name="azure-blob-storage-destination"></a>Azure Blob Storage célhely

Az adatexportálás percenként egyszer történik, és minden olyan fájl, amely az előző exportálás óta változást tartalmaz. Az exportált adatfájlok JSON formátumban lesznek mentve. A Storage-fiókban található exportált adatelérési utak a következők:

- Telemetria: _{Container}/{app-id}/{partition_id}/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Tulajdonságok módosítása: _{Container}/{app-id}/{partition_id}/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_

Az exportált fájlok tallózásához a Azure Portal navigáljon a fájlhoz, és válassza a **blob szerkesztése**lehetőséget.

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Azure Event Hubs és Azure Service Bus célhelyek

Az adatexportálás közel valós időben történik. Az adattartalom az üzenet törzsében van, és JSON formátumú, UTF-8 kódolású.

Az üzenet jegyzetek vagy Rendszertulajdonságok táska tartalmazza a,, `iotcentral-device-id` `iotcentral-application-id` `iotcentral-message-source` és `iotcentral-message-type` mezőket, amelyek az üzenettörzs megfelelő mezőivel megegyező értékekkel rendelkeznek.

### <a name="webhook-destination"></a>Webhook célhelye

Webhookok célhelye esetén a rendszer a közel valós időben is exportálja az adategységeket. Az üzenet törzsében lévő adatformátum megegyezik a Event Hubs és Service Bus.

### <a name="telemetry-format"></a>Telemetria formátuma

Minden exportált üzenet a teljes üzenet normalizált formáját tartalmazza, amelyet az eszköz küld az üzenet törzsében. Az üzenet JSON formátumú, és UTF-8-ként van kódolva. Az egyes üzenetekben található információk a következők:

- `applicationId`: A IoT Central alkalmazás azonosítója.
- `messageSource`: Az üzenet forrása – `telemetry` .
- `deviceId`: Annak az eszköznek az azonosítója, amely elküldte a telemetria üzenetet.
- `schema`: A hasznos adatok sémájának neve és verziószáma.
- `templateId`: Az eszközhöz társított eszköz sablonjának azonosítója.
- `enrichments`: Az exportáláskor beállított alkoholtartalom-NÖVELÉSEK.
- `messageProperties`: Az eszköz által az üzenettel küldött további tulajdonságok. Ezeket a tulajdonságokat más néven az *alkalmazás tulajdonságai*is nevezik. [További információ: IoT hub docs](../../iot-hub/iot-hub-devguide-messages-construct.md).

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

Minden üzenet vagy rekord egy eszköz vagy egy Felhőbeli tulajdonság egyik módosítását jelöli. Az eszköz tulajdonságainál csak a jelentett érték változásai lesznek exportálva külön üzenetként. Az exportált üzenetben szereplő információk a következők:

- `applicationId`: A IoT Central alkalmazás azonosítója.
- `messageSource`: Az üzenet forrása – `properties` .
- `messageType`: Vagy `cloudPropertyChange` , `devicePropertyDesiredChange` , vagy `devicePropertyReportedChange` .
- `deviceId`: Annak az eszköznek az azonosítója, amely elküldte a telemetria üzenetet.
- `schema`: A hasznos adatok sémájának neve és verziószáma.
- `templateId`: Az eszközhöz társított eszköz sablonjának azonosítója.
- `enrichments`: Az exportáláskor beállított alkoholtartalom-NÖVELÉSEK.

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

## <a name="comparison-of-legacy-data-export-and-preview-data-export"></a>A régi adatexportálás és az előzetes verziójú adatexportálás összehasonlítása

Az alábbi táblázat a [korábbi adatexportálási](howto-export-data-legacy.md) és-előnézeti adatexportálási funkciók közötti különbségeket mutatja be:

| Képességek  | Örökölt adatexportálás | Új adatexportálás |
| :------------- | :---------- | :----------- |
| Elérhető adattípusok | Telemetria, eszközök, eszközök sablonjai | Telemetria, tulajdonságok módosításai |
| Szűrés | Nincs | Az exportált adattípustól függ. Telemetria, szűrés telemetria, üzenet tulajdonságai, tulajdonságértékek alapján |
| Modellbővítések | Nincs | Gazdagítsa az eszköz egyéni sztringjét vagy tulajdonságának értékét |
| Célhelyek | Azure Event Hubs, Azure Service Bus várólisták és témakörök, Azure Blob Storage | Ugyanaz, mint a korábbi adatexportáláshoz és webhookokhoz|
| Támogatott alkalmazások verziói | V2, V3 | Csak v3 |
| Jelentős korlátok | 5 export/alkalmazás, 1 cél/exportálás | 10 exportálás – cél kapcsolatok száma alkalmazás szerint |

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan használhatja az új adatexportálást, egy javasolt következő lépés az [elemzések használatának](./howto-create-analytics.md) megismerése IoT Central
