---
title: Exportálja adatait az Azure Blob Storageba | Microsoft Docs
description: Adatok exportálása az Azure IoT Central alkalmazásból az Azure-ba Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 7ee9d2bf32fcec5f5f4435fe09916f437d6323ee
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71971667"
---
# <a name="export-your-data-to-azure-blob-storage"></a>Exportálja adatait az Azure Blob Storageba

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Ez a témakör a rendszergazdákra vonatkozik.*

Ez a cikk azt ismerteti, hogyan használható az Azure IoT Central folyamatos adatexportálás funkciója az **Azure Blob Storage-fiókba** vagy **Azure Data Lake Storage Gen2 Storage-fiókba**való rendszeres adatexportáláshoz. A fájlokat JSON vagy ApacheAvro formátumban **is exportálhatja** **a** fájlokra. Az exportált adatokat felhasználhatja a ritkán használt módszerekhez, például a Azure Machine Learning vagy a Microsoft Power BI hosszú távú trendek elemzéséhez.

> [!Note]
> Ha bekapcsolja a folyamatos adatexportálást, a rendszer csak az adott pillanattól kezdve kapja meg az adott adatot. Jelenleg nem lehet lekérni az adatgyűjtési időt, amikor a folyamatos adatexportálás ki lett kapcsolva. Több korábbi adat megtartásához kapcsolja be a folyamatos adatexportálást.


## <a name="prerequisites"></a>Előfeltételek

- A IoT Central-alkalmazásban rendszergazdának kell lennie


## <a name="set-up-export-destination"></a>Exportálás célhelyének beállítása

Ha nem rendelkezik meglévő tárolóval az exportáláshoz, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új Storage-fiókot a Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). További információ: új [Azure Blob Storage-fiókok](https://aka.ms/blobdocscreatestorageaccount) vagy [Azure Data Lake Storage v2 Storage-fiókok](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account)létrehozása.

    > [!Note] 
    > Ha **úgy dönt,** hogy ADLS v2 Storage-fiókba exportálja az adatexportálást, akkor a **BlobStorage**lehetőséget kell választania. 

    > [!Note] 
    > Az egyes előfizetésekhez tartozó Storage-fiókokba exportálhatja az adatait, mint az utólagos elszámolású IoT Central alkalmazás esetében. Ebben az esetben kapcsolati sztringet fog használni.

2. Hozzon létre egy tárolót a Storage-fiókban. Nyissa meg a Storage-fiókját. A **blob szolgáltatás**alatt válassza a **Tallózás Blobok**lehetőséget. Egy új tároló létrehozásához kattintson a felül található **+ tároló** elemre.

## <a name="set-up-continuous-data-export"></a>Folyamatos adatexportálás beállítása

Most, hogy van egy tárolási célhelye az adatexportáláshoz, kövesse az alábbi lépéseket a folyamatos adatexportálás beállításához. 

1. Jelentkezzen be IoT Central alkalmazásba.

2. A bal oldali menüben válassza az **adatexportálás**elemet.

    > [!Note]
    > Ha nem látja az adatexportálást a bal oldali menüben, nem Ön az alkalmazás rendszergazdája. Az adatexportálás beállításához forduljon a rendszergazdához.

3. Kattintson a jobb felső sarokban található **+ új** gombra. Válassza az **Azure Blob Storage** lehetőséget az Exportálás célhelye. 

    > [!NOTE] 
    > Az alkalmazások exportálásának maximális száma öt. 

    ![Új folyamatos adatexportálás létrehozása](media/howto-export-data/export-new2.png)

4. A legördülő listában válassza ki a **Storage-fiók névterét**. A lista utolsó elemét is kiválaszthatja, amely a **kapcsolatok karakterláncát adja meg**. 

    > [!NOTE] 
    > A Storage-fiókok névtereit a **IoT Central alkalmazással megegyező előfizetésben**fogja látni. Ha az előfizetésen kívüli célhelyre szeretne exportálni, válassza **az adja meg a kapcsolati karakterláncot** , és tekintse meg az 5. lépést.

    > [!NOTE] 
    > A 7 napos próbaverziós alkalmazások esetében az egyetlen módszer a folyamatos adatexportálás konfigurálására egy kapcsolódási karakterláncon keresztül. Ennek az az oka, hogy a 7 napos próbaverziós alkalmazások nem rendelkeznek társított Azure-előfizetéssel.

    ![Új Exportálás létrehozása a Blobba](media/howto-export-data/export-create-blob2.png)

5. Választható Ha a **kapcsolódási karakterlánc megadása**lehetőséget választotta, a rendszer egy új mezőt jelenít meg a kapcsolódási karakterlánc beillesztéséhez. A Storage-fiókhoz tartozó kapcsolati karakterlánc beszerzéséhez nyissa meg a Storage-fiókot a Azure Portal: – **Beállítások**területen, majd válassza a **hozzáférési kulcsok** lehetőséget – másolja a key1 kapcsolati karakterláncát vagy a key2 kapcsolati karakterláncát.
 
6. Válasszon egy tárolót a legördülő listából. Ha nem rendelkezik tárolóval, nyissa meg a Storage-fiókját a Azure Portalban:
    - A **blob Service**alatt válassza a **Blobok**elemet. Kattintson a **+ tároló** elemre, és adja meg a tároló nevét. Válasszon egy nyilvános hozzáférési szintet az adataihoz (a folyamatos adatexportálással együtt). További információ az [Azure Storage docs](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)-ról.

7. Válassza ki a kívánt **adatformátumot** : JSON-vagy [Apache Avro](https://avro.apache.org/docs/current/index.html) -formátum.

8. Az **exportálni kívánt adat**területen adja meg az exportálandó adattípusokat **, ha a**típust be értékre állítja.

9. A folyamatos adatexportálás bekapcsolásához győződjön meg arról, hogy az **adatexportálási** váltógomb be van **kapcsolva**. Kattintson a **Mentés** gombra.

   ![Folyamatos adatexportálás konfigurálása](media/howto-export-data/export-list-blob2.png)

10. Néhány perc elteltével az adatai megjelennek a Storage-fiókban.


## <a name="path-structure"></a>Elérési út szerkezete

A mérések, az eszközök és az eszközök sablonjainak adatai percenként egyszer lesznek exportálva a Storage-fiókba, és minden olyan fájl, amely tartalmazza a legutóbbi exportált fájl változási kötegét. Az exportált adatfájlok a JSON-vagy Avro-formátumú három mappában vannak elhelyezve. A Storage-fiók alapértelmezett elérési útjai a következők:
- Üzenetek: {Container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Eszközök: {Container}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Eszközök sablonjai: {Container}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}

Az exportált fájlok tallózásával tallózhat a Azure Portalban, ha a fájlra navigál, és a **blob szerkesztése** lapot választja.

## <a name="data-format"></a>Adatformátum 

### <a name="measurements"></a>Mérések

Az exportált mérések adatainak minden olyan új üzenete van, amelyet az összes eszköz IoT Central fogadott az adott idő alatt. Az exportált fájlok ugyanazt a formátumot használják, mint a blob Storage-ba [IoT hub üzenet-útválasztás](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) által exportált üzenet-fájlok.

> [!NOTE]
> Győződjön meg arról, hogy az eszközök olyan üzeneteket küldenek, amelyek `contentType: application/JSON` és `contentEncoding:utf-8` (vagy `utf-16`, `utf-32`) rendelkeznek. Példaként tekintse meg [IoT hub dokumentációját](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body) .

> [!NOTE]
> A méréseket küldő eszközöket az eszközök azonosítói jelölik (lásd a következő részeket). Az eszközök nevének lekéréséhez exportálja az eszköz pillanatképeit. Az egyes üzeneteket az **connectionDeviceId** megegyező, az eszközhöz **tartozó rekordra vonatkozó** adatokkal korrelálhatja.

A következő példa egy olyan rekordot mutat be a dekódolású Avro fájlban:

```json
{ 
  "EnqueuedTimeUtc":"2019-06-11T00:00:08.2250000Z",
  "Properties":{},
  "SystemProperties":{ 
    "connectionDeviceId":"<deviceId>",
    "connectionAuthMethod":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"<generationId>",
    "enqueuedTime":"2019-06-11T00:00:08.2250000Z"
  },
  "Body":"{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Eszközök

Ha a folyamatos adatexportálás be van kapcsolva, az összes eszközön egyetlen pillanatkép lesz exportálva. Minden eszköz a következőket tartalmazza:
- @no__t – az eszköz 0 IoT Central
- az eszköz @no__t – 0
- @no__t – 0 a [Device kiépítési szolgáltatásból](https://aka.ms/iotcentraldocsdps)
- Eszköz sablonjának adatai
- Tulajdonságok értékei
- Értékek beállítása

Az új Pillanatképek percenként egyszer írhatók. A pillanatkép a következőket tartalmazza:

- Új eszközök lettek hozzáadva a legutóbbi pillanatkép óta.
- A módosított tulajdonsággal rendelkező eszközök és a legutóbbi pillanatkép óta beállított értékek.

> [!NOTE]
> A legutóbbi pillanatkép óta törölt eszközök nem lesznek exportálva. A pillanatképek jelenleg nem rendelkeznek kijelzővel a törölt eszközökhöz.
>
> Az eszközök sablonja, amelyhez az egyes eszközök tartoznak, egy eszköz-sablon azonosítója jelöli. Az eszköz sablonjának lekéréséhez exportálja az eszköz sablonjának pillanatképeit.

Az exportált fájlok rekordokban egyetlen sort tartalmaznak. Az alábbi példa egy Avro formátumú rekordot mutat be, dekódolva:

```json
{ 
  "id":"<id>",
  "name":"Refrigerator 2",
  "simulated":true,
  "deviceId":"<deviceId>",
  "deviceTemplate":{ 
    "id":"<template id>",
    "version":"1.0.0"
  },
  "properties":{ 
    "cloud":{ 
      "location":"New York",
      "maintCon":true,
      "tempThresh":20
    },
    "device":{ 
      "lastReboot":"2018-02-09T22:22:47.156Z"
    }
  },
  "settings":{ 
    "device":{ 
      "fanSpeed":0
    }
  }
}
```

### <a name="device-templates"></a>Eszközsablonok

Ha a folyamatos adatexportálás be van kapcsolva, az összes eszköz sablonja egyetlen pillanatképet exportál. Minden eszköz sablonja a következőket tartalmazza:
- @no__t – 0 az eszköz sablonja
- @no__t – 0 az eszköz sablonja
- @no__t – 0 az eszköz sablonja
- Mérési adattípusok és minimális/maximális értékek.
- A tulajdonság adattípusai és az alapértelmezett értékek.
- Az adattípusok és az alapértelmezett értékek beállítása.

Az új Pillanatképek percenként egyszer írhatók. A pillanatkép a következőket tartalmazza:

- Új, a legutóbbi pillanatkép óta hozzáadott eszközök.
- A legutóbbi pillanatkép óta módosult mérési, tulajdonság-és definíciós eszközök.

> [!NOTE]
> A legutóbbi pillanatkép óta törölt eszközök sablonjai nem lesznek exportálva. Jelenleg a pillanatképek nem rendelkeznek kijelzővel a törölt eszközök sablonjaihoz.

Az exportált fájlok rekordokban egyetlen sort tartalmaznak. Az alábbi példa egy Avro formátumú rekordot mutat be, dekódolva:

```json
{ 
  "id":"<id>",
  "name":"Refrigerated Vending Machine",
  "version":"1.0.0",
  "measurements":{ 
    "telemetry":{ 
      "humidity":{ 
        "dataType":"double",
        "name":"Humidity"
      },
      "magnetometerX":{ 
        "dataType":"double",
        "name":"Magnetometer X"
      },
      "magnetometerY":{ 
        "dataType":"double",
        "name":"Magnetometer Y"
      },
      "magnetometerZ":{ 
        "dataType":"double",
        "name":"Magnetometer Z"
      }
    },
    "states":{ 
      "connectivity":{ 
        "dataType":"enum",
        "name":"Connectivity"
      }
    },
    "events":{ 
      "opened":{ 
        "name":"Door Opened",
        "category":"informational"
      }
    }
  },
  "settings":{ 
    "device":{ 
      "fanSpeed":{ 
        "dataType":"double",
        "name":"Fan Speed",
        "initialValue":0
      }
    }
  },
  "properties":{ 
    "cloud":{ 
      "location":{ 
        "dataType":"string",
        "name":"Location",
        "initialValue":"Seattle"
      },
      "maintCon":{ 
        "dataType":"boolean",
        "name":"Maintenance Contract",
        "initialValue":true
      },
      "tempThresh":{ 
        "dataType":"double",
        "name":"Temperature Alert Threshold",
        "initialValue":30
      }
    },
    "device":{ 
      "lastReboot":{ 
        "dataType":"dateTime",
        "name":"Last Reboot"
      }
    }
  }
}
```

## <a name="read-exported-avro-files"></a>Exportált Avro-fájlok olvasása

A Avro bináris formátum, így a fájlok nem olvashatók be a nyers állapotukban. A fájlokat JSON formátumba lehet dekódolni. Az alábbi példák bemutatják, hogyan elemezheti a méréseket, az eszközöket és az Avro fájlokat. A példák az előző szakaszban leírt példáknak felelnek meg.

### <a name="read-avro-files-by-using-python"></a>Avro-fájlok olvasása a Python használatával

#### <a name="install-pandas-and-the-pandavro-package"></a>A Panda és a pandavro csomag telepítése

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Mérések Avro-fájljának értelmezése

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(
        lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(
        lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)
```

#### <a name="parse-a-devices-avro-file"></a>Eszközök Avro-fájljának értelmezése

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["deviceId"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(
        lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(
        lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

#### <a name="parse-a-device-templates-avro-file"></a>Az eszközök sablonjainak Avro-fájljának elemzése

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
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
    transformed["fan_speed"] = templates["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>Avro-fájlok olvasása a következő használatával:C#

#### <a name="install-the-microsofthadoopavro-package"></a>A Microsoft. Hadoop. Avro csomag telepítése

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Mérések Avro-fájljának értelmezése

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
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
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

#### <a name="parse-a-devices-avro-file"></a>Eszközök Avro-fájljának értelmezése

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
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

#### <a name="parse-a-device-templates-avro-file"></a>Az eszközök sablonjainak Avro-fájljának elemzése

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
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

### <a name="read-avro-files-by-using-javascript"></a>Avro-fájlok olvasása JavaScript használatával

#### <a name="install-the-avsc-package"></a>A avsc-csomag telepítése

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>Mérések Avro-fájljának értelmezése

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device ID and humidity from each record.
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

#### <a name="parse-a-devices-avro-file"></a>Eszközök Avro-fájljának értelmezése

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
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

#### <a name="parse-a-device-templates-avro-file"></a>Az eszközök sablonjainak Avro-fájljának elemzése

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
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

Most, hogy már tudja, hogyan exportálhatja az adatait, folytassa a következő lépéssel:

> [!div class="nextstepaction"]
> [Az adataik megjelenítése Power BIban](howto-connect-powerbi.md)
