---
title: Exportálhatja az adatokat az Azure IoT Central |} A Microsoft Docs
description: Adatok exportálása az Azure IoT Central alkalmazásból
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 3231a956648b80d88059b7b0fc8f790e0e58be99
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50962792"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportálhatja az adatokat az Azure IoT Central

*Ez a témakör a rendszergazdák vonatkozik.*

Ez a cikk ismerteti a folyamatos exportálási szolgáltatás segítségével az Azure IoT Central rendszeres időközönként exportál adatokat az Azure Blob storage-fiókot. Exportálhatja **mérések**, **eszközök**, és **eszközsablonok** a fájlok a [Apache AVRO](https://avro.apache.org/docs/current/index.html) formátumban. Az exportált adatok például az Azure Machine Learning betanítási modellek vagy hosszú távú tendenciája, a Microsoft Power bi-ban a ritka elérésű útvonal elemzéshez használható.

> [!Note]
> Folyamatos adatexportálás bekapcsolásakor kap csak az adatok ettől a pillanattól kezdve. Jelenleg adatokat nem lehet beolvasni egy alkalommal, amikor folyamatos adatexportálás ki volt kapcsolva. További korábbi adatok megőrzése, kapcsolja be a folyamatos exportálás korai.

## <a name="prerequisites"></a>Előfeltételek

- A használatalapú fizetés alkalmazás.
- A rendszergazda az IoT-központ alkalmazásában rendelkezik:
    - az Azure-előfizetést, az IoT-központ alkalmazás szerepel az Azure-fiók
    - egy tároló létrehozásához szükséges engedélyek fiókkal vagy egy meglévő tárfiókot az Azure-előfizetés eléréséhez

## <a name="types-of-data-to-export"></a>Az exportálandó adattípusokat

### <a name="measurements"></a>Mérések

A mértékeket, az eszközök elküldik percenként egyszer a tárfiókba exportálja. Az adatok az új üzenetek IoT-központ által fogadott összes eszközről idő alatt van. Az exportált AVRO-fájlok ugyanazt a formátumot használja az exportált fájlokat [IoT Hub üzenet-útválasztása](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) a Blob storage.

> [!NOTE]
> Az eszközöket, amelyek a mérések küldése (lásd a következő szakaszok) eszköz azonosítóját képviseli. Az eszközök nevei lekéréséhez exportálja az eszköz pillanatképeket. Vesse össze az egyes üzenetrekordok használatával a **connectionDeviceId** , amely megfelel a **deviceId** eszköz rekord.

Az alábbi példa bemutatja egy rekordot egy dekódolt AVRO fájlban:

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "<connectionDeviceId>",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "<generationId>",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Eszközök

Amikor először folyamatos adatexportálás van kapcsolva, az összes eszköz egyetlen pillanatkép exportálása. Egyes eszközök a következőket tartalmazza:
- `id` az eszköz az IoT-központ
- `name` az eszköz
- `deviceId` a [Device Provisioning Service](https://aka.ms/iotcentraldocsdps)
- Eszköz sablon adatai
- Tulajdonságok értékei
- Beállításértékek

Új pillanatkép percenkénti van megírva. A pillanatkép tartalmazza:

- A legutolsó pillanatfelvétel óta hozzáadott új eszközök.
- Az eszközök módosított tulajdonsággal, és állítsa az értékeket a legutolsó pillanatfelvétel óta.

> [!NOTE]
> Az eszközök történt, a legutolsó pillanatfelvétel nem exportálható. A pillanatképek jelenleg nem rendelkezik a törölt eszközök mutatók.
>
> Az eszköz a sablon, amely minden eszközhöz tartozik egy eszközazonosítót sablon. által jelölt A kiszolgáló nevének az eszköz sablon exportálása az eszköz sablon pillanatképeket.

Egy rekordot a dekódolt AVRO-fájl is látható:

```json
{
    "id": "<id>",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
        "id": "<template id>",
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

Amikor először folyamatos adatexportálás van kapcsolva, az összes eszköz-sablonokkal egyetlen pillanatkép exportálása. Minden eszköz sablon tartalmazza:
- `id` az eszköz sablon
- `name` az eszköz sablon
- `version` az eszköz sablon
- Mérési az adattípusok és a minimális/maximális értékei.
- Vlastnost adattípusokat és az alapértelmezett értékeket.
- A beállítás az adattípusok és az alapértelmezett értékeket.

Új pillanatkép percenkénti van megírva. A pillanatkép tartalmazza:

- A legutolsó pillanatfelvétel óta hozzáadott új eszközsablonok.
- Eszköz-sablonok módosított mérések, tulajdonságot és definíciókat beállítása a legutolsó pillanatfelvétel óta.

> [!NOTE]
> A legutolsó pillanatfelvétel óta törölve eszközsablonok nem exportálható. A pillanatképek jelenleg nem rendelkezik a törölt sablonok mutatók.

A dekódolt AVRO-fájlt egy rekordot fog kinézni:

```json
{
    "id": "<id>",
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

## <a name="set-up-continuous-data-export"></a>Állítsa be a folyamatos exportálás

1. Ha nem rendelkezik Azure storage-fiók [hozzon létre egy új tárfiókot](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) az Azure Portalon. A storage-fiók létrehozása **az Azure-előfizetésben, amely rendelkezik az IoT-központ alkalmazás**.
    - Adja meg a fiók típusú tárfiók, **általános célú** vagy **a Blob storage-**.
    - Válassza ki az előfizetést, amely rendelkezik az IoT Central alkalmazáshoz. Ha nem látja az előfizetés, szüksége lehet bejelentkezni egy másik Azure-fiók vagy a kérelem hozzáférést az előfizetéshez.
    - Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Ismerje meg [új tárfiók létrehozása](https://aka.ms/blobdocscreatestorageaccount).

2. Hozzon létre egy tárolót a storage-fiókban exportálhatja az IoT Central-adatokat. Lépjen a tárfiókhoz. A **Blob Service**válassza **Blobok tallózása**. Válassza ki **tároló** hozhat létre egy új tárolót.

   ![Tároló létrehozása](media/howto-export-data/createcontainer.png)

3. Jelentkezzen be az IoT Central alkalmazásnak ugyanazon Azure-fiók használatával.

4. A **felügyeleti**válassza **adatexportálás**.

5. Az a **tárfiók** legördülő listában jelölje ki a tárfiók. Az a **tároló** legördülő listában jelölje ki a tárolót. A **exportálható adatot**, adja meg az egyes adattípusok úgy, hogy a típus exportálása **a**.

6. Folyamatos adatexportálás bekapcsolásához állítsa **adatexportálás** való **a**. Kattintson a **Mentés** gombra.

  ![Folyamatos adatexportálás konfigurálása](media/howto-export-data/continuousdataexport.PNG)

7. Néhány perc elteltével az adatok a tárfiókban található meg. Keresse meg a tárfiók. Válassza ki **blobok tallózása** > a tárolót. Láthatja, hogy az adatok exportálása a három mappát. Az AVRO-fájlok, az adatok exportálása az alapértelmezett elérési útjait a következők:
    - Üzenet: {container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
    - Eszközök: {container}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
    - Eszközsablonok: {container}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro

## <a name="read-exported-avro-files"></a>Olvassa el a AVRO-fájlok exportálása

Avro-hoz, a bináris formátum, így a fájlok nyers állapotban nem olvasható. A fájlok vissza tudja fejteni JSON formátumba. Az alábbi példák bemutatják, hogyan elemezhető a mérést, eszközök és eszközsablonok AVRO-fájlok. A példák megegyeznek az előző szakaszban leírt példák.

### <a name="read-avro-files-by-using-python"></a>AVRO-fájlok olvasása a Python használatával

#### <a name="install-pandas-and-the-pandavro-package"></a>Pandas és a pandavro csomag telepítése

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>A mértékek az AVRO-fájl elemzése

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)

```

#### <a name="parse-a-devices-avro-file"></a>Egy eszköz AVRO-fájl elemzése

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["deviceId"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)

```

#### <a name="parse-a-device-templates-avro-file"></a>Egy eszköz sablonok az AVRO-fájl elemzése

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    templates = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available in the id and version columns.
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>Olvassa el az AVRO-fájlok C# használatával

#### <a name="install-the-microsofthadoopavro-package"></a>A Microsoft.Hadoop.Avro csomag telepítése

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>A mértékek az AVRO-fájl elemzése

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
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
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

#### <a name="parse-a-devices-avro-file"></a>Egy eszköz AVRO-fájl elemzése

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var deviceId = record.GetField<string>("deviceId");

                    // The device template information is stored in a sub-record
                    // under the deviceTemplate field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "Device ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
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

#### <a name="parse-a-device-templates-avro-file"></a>Egy eszköz sablonok az AVRO-fájl elemzése

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
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

### <a name="read-avro-files-by-using-javascript"></a>Olvassa el az AVRO-fájlok a Javascript használatával

#### <a name="install-the-avsc-package"></a>A avsc csomag telepítése

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>A mértékek az AVRO-fájl elemzése

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device ID and humidity from each record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the system properties.
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the body from a buffer to a string and parse it.
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property from the body.
        const humidity = body.humidity;

        // Log the retrieved device ID and humidity.
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-devices-avro-file"></a>Egy eszköz AVRO-fájl elemzése

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the deviceId property.
        const deviceId = record.deviceId;

        // Fetch the template ID and version from the deviceTemplate property.
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device ID and humidity.
        console.log(`deviceID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-device-templates-avro-file"></a>Egy eszköz sablonok az AVRO-fájl elemzése

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template ID and version from the id and verison properties.
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device id and humidity.
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>További lépések

Most, hogy tudja, hogyan exportálhatja az adatokat, folytassa a következő lépéssel:

> [!div class="nextstepaction"]
> [Hogyan jelenítheti meg az adatok a Power bi-ban](howto-connect-powerbi.md)
