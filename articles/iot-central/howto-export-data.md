---
title: Exportálhatja az adatokat az Azure IoT Central |} A Microsoft Docs
description: Adatok exportálása az Azure IoT Central alkalmazásból
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/3/2018
ms.topic: article
ms.prod: azure-iot-central
manager: peterpr
ms.openlocfilehash: 5b9564dfe40f292d289ee9ed680e816771d0b0ed
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282874"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportálhatja az adatokat az Azure IoT Central

Folyamatos adatexportálás használatával rendszeres időközönként exportálja az adatokat az Azure Blob Storage-fiókba. Exportálhatja **mérések**, **eszközök**, és **eszközsablonok** a Fájltípus [Apache AVRO](https://avro.apache.org/docs/current/index.html) formátumban. Használja az exportált adatok például az Azure Machine Learning betanítási modellek vagy hosszú távú trendek elemzése a Power bi-ban a ritka elérésű útvonal elemzéshez.

> [!Note]
> Folyamatos adatexportálás bekapcsolja, csak kap, amely ettől a pillanattól kezdve érhető el az adatokat. Jelenleg nincs mód, ha folyamatos adatexportálás ki lett kapcsolva adatokat lekérni. Kapcsolja be a folyamatos adatexportálás korai további korábbi adatok megőrzése!

## <a name="prerequisites"></a>Előfeltételek

- A kiterjesztett 30 napos próbaverziós alkalmazás vagy egy fizetős alkalmazás
- Azure-fiók Azure-előfizetéshez
- Ugyanazon Azure-fiók az IoT Central-alkalmazást a rendszergazda
- Ugyanazon Azure-fiók jogosult hozható létre egy storage-fiókot vagy egy meglévő tárfiókot az Azure-előfizetéshez

## <a name="types-of-data-to-export"></a>Az exportálandó adattípusokat

### <a name="measurements"></a>Mérések

A mértékek, amelyek az eszközök elküldik a tárfiókba történő exportálva. Mérési adatok exportálása után körülbelül egy percet, adott időtartományon belül az összes eszközről IoT-központ által fogadott összes új üzeneteket tartalmazó. Az exportált AVRO-fájlok, amelyben a üzenetek által exportált fájl ugyanebben a formátumban vannak [IoT Hub üzenet-útválasztása](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) a blob storage.

> [!NOTE]
> Az eszközöket, amelyek a mérések küldött eszközazonosítókat (lásd alább) képviseli. Az eszközök nevei lekéréséhez kell eszközök pillanatképek túl exportálni. Minden egyes üzenetrekordok, az eszköz azonosítója, amely a connectionDeviceId használatával kapcsolhatja össze.

Ez az a dekódolt AVRO-fájlt egy rekordot egy példát.

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "2383d8ba-c98c-403a-b4d5-8963859643bb",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "636614021491644195",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Eszközök

Amikor először bekapcsolja az adatok folyamatos exportálása, minden eszköz tartalmazó egyetlen pillanatkép exportálása. Az érintett műveletek közé tartoznak az alábbiak:
- Az eszközazonosítókat
- Eszköz neve
- Eszköz sablon azonosítók
- Tulajdonságok értékei
- Beállítási értékei

Egyszer körülbelül egy percet, új pillanatkép írt tartalmazó:

- Az új eszközökön, a legutolsó pillanatfelvétel óta hozzáadott
- Eszközök, akinek a legutolsó pillanatfelvétel óta megváltozott tulajdonságokat és beállításokat értékek

> [!NOTE]
> A legutolsó pillanatfelvétel óta törölt eszközöket a rendszer nem exportál. Nem áll semmilyen mutató jelenleg törölt eszközök pillanatképeit.

> [!NOTE]
> Az eszköz a sablon, amely minden eszközhöz tartozik egy eszközazonosítót sablon. által jelölt Eszköz-sablon nevének eszköz sablon pillanatképek túl exportálni kell.

A dekódolt AVRO fájlt minden rekordja a következőhöz hasonló:

```json
{
    "id": "2383d8ba-c98c-403a-b4d5-8963859643bb",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceTemplate": {
        "id": "c318d580-39fc-4aca-b995-843719821049",
        "version": "1.0.0"
    },
    "properties": {
        "cloud": {
            "location": "New York",
            "maintCon": true,
            "tempThresh": 20
        },
        "device": {
            "lastReboot": "2018-02-09T22:22:47.156Z"
        }
    },
    "settings": {
        "device": {
            "fanSpeed": 0
        }
    }
}
```

### <a name="device-templates"></a>Eszköz-sablonok

Amikor először bekapcsolja az adatok folyamatos exportálása, minden eszközsablonok tartalmazó egyetlen pillanatkép exportálása. Az érintett műveletek közé tartoznak az alábbiak: 
- Eszköz sablon azonosítók
- Mérési az adattípusok és a minimális/maximális értékei
- Tulajdonságok adattípusokat és az alapértelmezett értékek
- Beállítások az adattípusokat és az alapértelmezett értékek

Egyszer körülbelül egy percet, új pillanatkép írt tartalmazó:

- A legutolsó pillanatfelvétel óta hozzáadott új eszközt sablonból
- Eszközsablonok, akinek mérések, tulajdonságok és a legutolsó pillanatfelvétel óta módosított beállítások definíciók

> [!NOTE]
> A legutolsó pillanatfelvétel óta törölt eszközsablonok a rendszer nem exportál. Nem áll kijelző a törölt jelenleg eszközsablonok pillanatképeit.

A dekódolt AVRO fájlt minden rekordja a következőhöz hasonló:

```json
{
    "id": "c318d580-39fc-4aca-b995-843719821049",
    "name": "Refrigerated Vending Machine",
    "version": "1.0.0",
    "measurements": {
        "telemetry": {
            "humidity": {
                "dataType": "double",
                "name": "Humidity"
            },
            "magnetometerX": {
                "dataType": "double",
                "name": "Magnetometer X"
            },
            "magnetometerY": {
                "dataType": "double",
                "name": "Magnetometer Y"
            },
            "magnetometerZ": {
                "dataType": "double",
                "name": "Magnetometer Z"
            }
        },
        "states": {
            "connectivity": {
                "dataType": "enum",
                "name": "Connectivity"
            }
        },
        "events": {
            "opened": {
                "name": "Door Opened",
                "category": "informational"
            }
        }
    },
    "settings": {
        "device": {
            "fanSpeed": {
                "dataType": "double",
                "name": "Fan Speed",
                "initialValue": 0
            }
        }
    },
    "properties": {
        "cloud": {
            "location": {
                "dataType": "string",
                "name": "Location",
                "initialValue": "Seattle"
            },
            "maintCon": {
                "dataType": "boolean",
                "name": "Maintenance Contract",
                "initialValue": true
            },
            "tempThresh": {
                "dataType": "double",
                "name": "Temperature Alert Threshold",
                "initialValue": 30
            }
        },
        "device": {
            "lastReboot": {
                "dataType": "dateTime",
                "name": "Last Reboot"
            }
        }
    }
}
```

## <a name="how-to-set-up-data-export"></a>Adatok exportálása beállítása

1. Ha még nem rendelkezik egy, az Azure Storage-fiók létrehozása **az Azure-előfizetést, az alkalmazás**. [Kattintson ide a](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) való ugráshoz egy új Azure Storage-fiók létrehozása az Azure Portalon.

    - Válasszon *általános célú* vagy *a Blob storage-* típusú fiók
    - Válassza ki az előfizetést, az IoT-központ alkalmazás. Ha nem látja az előfizetés, szükség lehet bejelentkezni egy másik Azure-fiókkal, vagy kérjen hozzáférést az előfizetéshez.
    - Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Ismerje meg [új tárfiók létrehozása.](https://aka.ms/blobdocscreatestorageaccount)

2. Hozzon létre egy tárolót a Storage-fiókban az IoT-központ adatok exportálásához. Nyissa meg a tárfiók -> Blobok tallózása, és hozzon létre egy új tárolót. ![Tárolórendszerkép létrehozása](media/howto-export-data/createcontainer.png)

3. Jelentkezzen be az IoT Central alkalmazásnak ugyanazon Azure-fiók használatával.
1. Válassza a felügyeleti -> adatok folyamatos exportálása.
![IoT-központ CDE](media/howto-export-data/continuousdataexport.PNG)
1. A legördülő menük használatával, válassza ki a tárfiókot és tárolót. A Váltás használatával kapcsolhatja be és ki a különböző típusú adatok exportálásához.
1. Végül kapcsolja be a folyamatos adatexportálás a váltógomb segítségével, és kattintson a "Mentés".
1. Várjon néhány percet, és az adatok jelennek meg a Storage-fiókban kell megjelennie. Akkor is lépjen a tárfiókhoz, válassza a Tallózás blobok, a tárolót, és láthatja, hogy három mappát. Az AVRO-fájlok, amely tartalmazza a különböző típusú adatok alapértelmezett elérési útjait a következők:
- Üzenetek: **{container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- Eszközök: **{container}/devices/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- Eszközsablonok: **{container}/deviceTemplates/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**

## <a name="how-to-read-exported-avro-files"></a>Hogyan olvashatja be exportált AVRO-fájlok

Avro-hoz, a bináris formátum, így a fájlok nyers állapotban nem olvasható. Hogy vissza tudja fejteni JSON formátumba. Az alábbi példák bemutatják, hogyan elemezni a mérések, eszközök és eszközsablonok AVRO-fájlok a fenti példa használatával.

## <a name="python"></a>Python

### <a name="install-pandas-and-the-pandaavro-package"></a>Pandas és a PandaAvro csomag telepítése:

```python
pip install pandas
pip install pandavro
```
### <a name="parse-measurements-avro-file"></a>Mértékek AVRO-fájl elemzése

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    measurements = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary with the device id
    # located under the "connectionDeviceId" key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of utf-8 bytes that is stringified and parsed
    # as json. In this example, we pull the "humidity" property off of each one
    # to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, we print the new DataFrame with our device ids and humidities
    print(transformed)

```
### <a name="parse-devices-avro-file"></a>Eszközök az AVRO-fájl elemzése

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    devices = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device id is available directly in the "id" column
    transformed["device_id"] = devices["id"]

    # The template id and version are present in a dictionary under the
    # deviceTemplate column
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)

```

### <a name="parse-device-templates-avro-file"></a>Eszköz sablonok az AVRO-fájl elemzése

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    templates = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available directly in the "id" and "version"
    # columns, respectively
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)
```

## <a name="c"></a>C#

### <a name="install-microsofthadoopavro"></a>Microsoft.Hadoop.Avro telepítése

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

### <a name="parse-measurements-avro-file"></a>Mértékek AVRO-fájl elemzése

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;
using Newtonsoft.Json;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contain multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the fields
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    var systemProperties = record.GetField<IDictionary<string, object>>("SystemProperties");
                    var deviceId = systemProperties["connectionDeviceId"] as string;
                    Console.WriteLine("Device ID: {0}", deviceId);

                    using (var stream = new MemoryStream(record.GetField<byte[]>("Body")))
                    {
                        using (var streamReader = new StreamReader(stream, Encoding.UTF8))
                        {
                            var body = JsonSerializer.Create().Deserialize(streamReader, typeof(IDictionary<string, dynamic>)) as IDictionary<string, dynamic>;
                            var humidity = body["humidity"];
                            Console.WriteLine("Humidity: {0}", humidity);
                        }
                    }
                }
            }
        }
    }
}
```

### <a name="parse-devices-avro-file"></a>Eszközök az AVRO-fájl elemzése

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var deviceId = record.GetField<string>("id");

                    // The device template information is stored in a sub-record
                    // under the "deviceTemplate" field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
                        deviceId,
                        templateId,
                        templateVersion,
                        fanSpeed
                    );
                }
            }
        }
    }
}

```
### <a name="parse-device-templates-avro-file"></a>Eszköz sablonok az AVRO-fájl elemzése

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {1}, Version: {2}, Fan Speed: {3}",
                        id,
                        version,
                        fanSpeed
                    );
                }
            }
        }
    }
}
```

## <a name="javascript"></a>Javascript

### <a name="install-avsc"></a>Avsc telepítése

```javascript
npm install avsc
```

### <a name="parse-measurements-avro-file"></a>Mértékek AVRO-fájl elemzése

```javascript
const avro = require('avsc');

// Read the avro file and parse the device id and humidity from each record
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the system properties
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the Body from a Buffer to a string and parse it
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property off of the body
        const humidity = body.humidity;

        // Log the device id and humidity we found
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-devices-avro-file"></a>Eszközök az AVRO-fájl elemzése

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the id property
        const deviceId = record.id;

        // Fetch the tempalte id and version from the deviceTemplate property
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`ID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-device-templates-avro-file"></a>Eszköz sablonok az AVRO-fájl elemzése

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template id and version from the id and verison properties
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan exportálhatja az adatokat, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Hogyan jelenítheti meg az adatok a Power bi-ban](howto-connect-powerbi.md)