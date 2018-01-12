---
title: "Importálás és exportálás Azure IoT Hub eszköz identitások |} Microsoft Docs"
description: "Hogyan használható az Azure IoT szolgáltatás SDK szemben az identitásjegyzékhez történő importálására és exportálására eszköz identitások tömeges műveletek végrehajtásához. Importálási műveletek lehetővé teszik létrehozása, frissítése és törlése eszköz identitások egyszerre."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 2ade1494-45ea-46a7-ade7-cf6e11ce62da
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: dobett
ms.openlocfilehash: d2a6660b93fee1e1fc24269eb7075e5243ce88ed
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2018
---
# <a name="manage-your-iot-hub-device-identities-in-bulk"></a>Az IoT Hub eszköz identitásai tömeges kezelése

Minden egyes IoT-központ rendelkezik egy identitásjegyzékhez eszközönkénti erőforrásokat létrehozni a szolgáltatás segítségével. Az identitásjegyzékhez is lehetővé teszi az eszköz felé néző végpontok való hozzáférés vezérlése érdekében. Ez a cikk ismerteti, hogyan importálhat és exportálhat eszköz identitások tömeges irányuló és onnan az identitásjegyzékhez.

Importálás és exportálás zajlanak környezetében *feladatok* , amelyek lehetővé teszik az IoT-központ szolgáltatás tömeges műveleteket végrehajtani.

A **RegistryManager** osztály tartalmazza a **ExportDevicesAsync** és **ImportDevicesAsync** módszereket, amelyek a **feladat** keretrendszer. Ezek a módszerek lehetővé teszik a exportálása, importálása és az IoT hub identitásjegyzékhez a a teljes szinkronizálás.

Ez a témakör ismerteti, amelyek használatával a **RegistryManager** osztály és **feladat** rendszer tömeges és -eszközökhöz, illetve onnan az IoT-központ identitásjegyzékhez exportot végrehajtásához. Az Azure IoT Hub eszköz kiépítése szolgáltatás segítségével is nulla-érintés, közvetlenül az időponthoz kötött emberi beavatkozás nélkül egy vagy több IoT-központok kiépítés engedélyezése. További tudnivalókért tekintse meg a [szolgáltatás dokumentációja kiépítés][lnk-dps].

## <a name="what-are-jobs"></a>Mik azok a feladatok?

Identitás kapcsolatos műveletek használata a **feladat** rendszer Ha a művelet:

* Potenciálisan hosszú végrehajtási időt képest van szabványos futásidejű műveletek.
* A felhasználó egy nagy mennyiségű adatot adja vissza.

Egyetlen API-hívással Várakozás vagy blokkolja-e a művelet eredményét, helyett a művelet aszinkron módon létrehoz egy **feladat** , hogy az IoT hub. A műveletet, majd azonnal értéket ad vissza egy **JobProperties** objektum.

A következő C# kódrészletet exportálási feladat létrehozását szemlélteti:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Használatához a **RegistryManager** osztály a C#-kódban, adja hozzá a **Microsoft.Azure.Devices** NuGet-csomagot a projekthez. A **RegistryManager** osztály a **Microsoft.Azure.Devices** névtér.

Használhatja a **RegistryManager** osztály állapotának lekérdezése a **feladat** használatával a visszaadott **JobProperties** metaadatok.

A következő C# kódrészletet öt másodpercenként kérdezze le a megjelenítéséhez, ha a feladat végrehajtása befejeződött mutatja be:

```csharp
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="export-devices"></a>Eszközök exportálása

Használja a **ExportDevicesAsync** a a teljes az IoT hub identitásjegyzékhez történő exportálására egy [Azure Storage](../storage/index.yml) blob tároló használata egy [közös hozzáférésű Jogosultságkód](../storage/common/storage-security-guide.md#data-plane-security).

Ez a módszer lehetővé teszi a személyes eszköz adatok megbízható biztonsági mentések létrehozását egy blob a tárolóban, amelyek.

A **ExportDevicesAsync** módszernél két paramétert:

* A *karakterlánc* , amely tartalmazza a blob-tároló URI. Ezt az URI írási hozzáférést a tároló SAS-jogkivonatot kell tartalmaznia. A feladat a blokkblob ebben a tárolóban a szerializált export eszköz adatainak tárolására hoz létre. A SAS-jogkivonat tartalmaznia kell azokat az engedélyeket:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
   ```

* A *logikai* , amely azt jelzi, hogy szeretné-e a hitelesítési kulcsokat kizárása az adatok exportálása. Ha **hamis**, hitelesítési kulcsokat exportálás kimeneti szerepelnek. Ellenkező esetben kulcsok exportálása **null**.

A következő C# kódrészletet mutatja be, amely tartalmazza az eszköz hitelesítési kulcsokat az adatok exportálása az exportálási feladat kezdeményezése, és majd kérdezze le az Befejezés:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

A feladat tárolja a kimenetet a megadott blob-tárolóban egy blokkblob nevű **devices.txt**. A kimeneti adatok JSON szerializált eszközadatok, soronként egy eszközzel áll.

A következő példa bemutatja a kimeneti adatokat:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Ha egy eszköz iker adatokat, majd a kettős adatok is exportált együtt az eszközadatok. A következő példa bemutatja, ezt a formátumot. A "twinETag" sor végéig minden adatát iker adatok.

```json
{
   "id":"export-6d84f075-0",
   "eTag":"MQ==",
   "status":"enabled",
   "statusReason":"firstUpdate",
   "authentication":null,
   "twinETag":"AAAAAAAAAAI=",
   "tags":{
      "Location":"LivingRoom"
   },
   "properties":{
      "desired":{
         "Thermostat":{
            "Temperature":75.1,
            "Unit":"F"
         },
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
            "$lastUpdatedVersion":2,
            "Thermostat":{
               "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
               "$lastUpdatedVersion":2,
               "Temperature":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               },
               "Unit":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               }
            }
         },
         "$version":2
      },
      "reported":{
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:51.1309437Z"
         },
         "$version":1
      }
   }
}
```

Ha az adatokhoz, a kódban van szüksége, is könnyen deszerializálása ezen adatok segítségével a **ExportImportDevice** osztály. A következő C# kódrészletet olvassa el a korábban exportált blokkblobba eszközadatokat mutatja be:

```csharp
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), null, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [!NOTE]
> Használhatja a **GetDevicesAsync** metódusában a **RegistryManager** osztály az eszközök listájának beolvasása. Azonban ez a megközelítés azzal az 1000 merevlemez maximális visszaadott eszköz objektumok száma. A várható használati eset a **GetDevicesAsync** metódus hibakeresés elősegítésére fejlesztési forgatókönyvre vonatkozó, és nem ajánlott a termelési számítási feladatokhoz.

## <a name="import-devices"></a>Eszközök importálása

A **ImportDevicesAsync** metódust a **RegistryManager** osztály lehetővé teszi az IoT hub identitásjegyzékhez tömeges importálása és szinkronizálás műveleteinek elvégzéséhez. Például a **ExportDevicesAsync** metódus, a **ImportDevicesAsync** módszert használ a **feladat** keretrendszer.

Mi gondoskodunk használatával a **ImportDevicesAsync** metódus mert használt dinamikus kiosztásnál a identitásjegyzékhez új eszközök mellett is frissíteni és törölje a meglévő eszközökön.

> [!WARNING]
> Az importálási művelet nem vonható vissza. Mindig készítsen biztonsági másolatot a meglévő adatok a **ExportDevicesAsync** egy másik blob-tároló előtt tömeges módszert biztosít az identitásjegyzékhez változik.

A **ImportDevicesAsync** metódus két paramétereket fogadja:

* A *karakterlánc* , amely tartalmazza egy URI-azonosítója egy [Azure Storage](../storage/index.yml) kívánja használni, mint a blobtárolót *bemeneti* a feladathoz. Ezt az URI tartalmaznia kell egy SAS-jogkivonatot, amely olvasási hozzáférést biztosít a tárolóhoz. Ez a tároló tartalmaznia kell egy blob nevű **devices.txt** , amely tartalmazza a szerializált eszközadatok a identitásjegyzékhez rendszerbe való importálás érdekében. Az importálási adatok tartalmaznia kell az eszközinformáció ugyanazon JSON formátumban a **ExportImportDevice** feladatot használ, amikor létrehozza a **devices.txt** blob. A SAS-jogkivonat tartalmaznia kell azokat az engedélyeket:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```
* A *karakterlánc* , amely tartalmazza egy URI-azonosítója egy [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) kívánja használni, mint a blobtárolót *kimeneti* a feladatból. A feladat létrehoz egy blokkblob ebben a tárolóban, az importálás befejezése hiba adatok tárolására **feladat**. A SAS-jogkivonat tartalmaznia kell azokat az engedélyeket:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> A két paraméter blob tárolóhoz is mutat. A különböző paraméterek egyszerűen további szabályozásának engedélyezése az adatok a kimeneti tároló jogosultságokra van szüksége.

A következő C# kódrészletet jeleníti meg az importálási feladat indítása:

```csharp
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Ez a módszer az adatok importálása az eszköz iker is használható. A bemeneti adatok formátuma látható formátuma megegyezik a **ExportDevicesAsync** szakasz. Ezzel a módszerrel importálja újra az exportált adatokat. A **$metadata** nem kötelező megadni.

## <a name="import-behavior"></a>Importálás viselkedése

Használhatja a **ImportDevicesAsync** metódus a következő tömeges műveleteinek elvégzéséhez a identitásjegyzékhez:

* Az új eszközök tömeges regisztrálása
* Meglévő eszközök tömeges törlése
* Tömeges állapotmódosítások (engedélyezése vagy letiltása az eszközök)
* Új eszköz hitelesítési kulcsokat tömeges hozzárendelése
* Tömeges eszköz hitelesítési kulcsokat, automatikus-újragenerálása
* Tömeges frissítés iker adatok

Végezheti el az előző művelet egy bármely kombinációja **ImportDevicesAsync** hívható meg. Például új eszközöket regisztrálni és törölheti vagy frissítheti a meglévő eszközöket egy időben. Mentén használata esetén a **ExportDevicesAsync** módszer, teljesen át lehet az összes eszközt egy IoT-központot egy másikra.

Ha az importált fájl iker metaadatokat tartalmaz, a metaadatok felülírja a meglévő iker metaadatokat. Ha az importált fájl nem tartalmaz iker metaadatokat, majd csak a `lastUpdateTime` metaadatok frissül, az aktuális idő.

Használja az opcionális **amelyben a importMode** tulajdonság szerializációs adatok importálása az egyes eszközök az importálási folyamat eszközönkénti szabályozására. A **amelyben a importMode** tulajdonságnak a következő beállításokat:

| amelyben a importMode | Leírás |
| --- | --- |
| **createOrUpdate** |Ha egy eszköz nem létezik a megadott **azonosító**, újonnan regisztrálva van. <br/>Ha az eszköz már létezik, a megadott bemeneti adatok nélkül tekintettel felülírja a meglévő adatokat a **ETag** érték. <br> A felhasználó megadja a két adatok az eszköz adatokkal együtt. A kettős etag, ha meg van adva, akkor feldolgozott egymástól függetlenül az eszköz etag. Ha eltérést okoz a meglévő iker etag, egy hiba kerül a naplófájlba írást. |
| **létrehozás** |Ha egy eszköz nem létezik a megadott **azonosító**, újonnan regisztrálva van. <br/>Ha az eszköz már létezik, egy hiba kerül a naplófájlba írást. <br> A felhasználó megadja a két adatok az eszköz adatokkal együtt. A kettős etag, ha meg van adva, akkor feldolgozott egymástól függetlenül az eszköz etag. Ha eltérést okoz a meglévő iker etag, egy hiba kerül a naplófájlba írást. |
| **frissítés** |Ha egy eszköz már létezik a megadott **azonosító**, a megadott bemeneti adatok nélkül tekintettel felülírja a meglévő adatokat a **ETag** érték. <br/>Ha az eszköz nem létezik, egy hiba kerül a naplófájlba írást. |
| **updateIfMatchETag** |Ha egy eszköz már létezik a megadott **azonosító**, meglévő információt felülírja a megadott bemeneti adatok csak akkor, ha van egy **ETag** felel meg. <br/>Ha az eszköz nem létezik, egy hiba kerül a naplófájlba írást. <br/>Ha egy **ETag** eltérés, hiba ír a naplófájlba írást. |
| **createOrUpdateIfMatchETag** |Ha egy eszköz nem létezik a megadott **azonosító**, újonnan regisztrálva van. <br/>Ha az eszköz már létezik, meglévő információt felülírja a megadott bemeneti adatok csak akkor, ha van egy **ETag** felel meg. <br/>Ha egy **ETag** eltérés, hiba ír a naplófájlba írást. <br> A felhasználó megadja a két adatok az eszköz adatokkal együtt. A kettős etag, ha meg van adva, akkor feldolgozott egymástól függetlenül az eszköz etag. Ha eltérést okoz a meglévő iker etag, egy hiba kerül a naplófájlba írást. |
| **törlés** |Ha egy eszköz már létezik a megadott **azonosító**, nélkül tekintettel törlődik a **ETag** érték. <br/>Ha az eszköz nem létezik, egy hiba kerül a naplófájlba írást. |
| **deleteIfMatchETag** |Ha egy eszköz már létezik a megadott **azonosító**, törlése, csak akkor, ha van egy **ETag** felel meg. Ha az eszköz nem létezik, egy hiba kerül a naplófájlba írást. <br/>Ha az egy ETag nem egyezik, egy hiba kerül a naplófájlba írást. |

> [!NOTE]
> Ha a szerializációs adatok nem definiálhat explicit módon egy **amelyben a importMode** jelző egy eszközhöz, alapértelmezés szerint a **createOrUpdate** az importálási művelet során.

## <a name="import-devices-example--bulk-device-provisioning"></a>Eszközök például importálása – tömeges eszköz kiépítése

A következő C# kódminta bemutatja, hogyan hozható létre több eszközt identitás, amely:

* Például a hitelesítési kulcsokat.
* Adott eszköz kapcsolatos adatokat ír az blokkblobba.
* Az eszközök importálnia kell az identitásjegyzékhez.

```csharp
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
  // Create a new ExportImportDevice
  // CryptoKeyGenerator is in the Microsoft.Azure.Devices.Common namespace
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to the list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write the list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the blob to add new devices
// Log information related to the job is written to the same container
// This normally takes 1 minute per 100 devices
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Eszközök például – tömeges törlésének importálása

A következő példakód bemutatja, hogyan használja az előző példakód hozzáadott eszközök törlése:

```csharp
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="get-the-container-sas-uri"></a>A tároló SAS URI-JÁNAK beolvasása

A következő példakód bemutatja, hogyan létrehozni egy [SAS URI](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md) az olvasási, írási és törlési engedélyek egy blob-tároló:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan hajthat végre az identitásjegyzékhez tömeges műveleteket a az IoT-központ. Az alábbi hivatkozásokból tudhat meg többet az Azure IoT Hub kezelése:

* [Az IoT-központ metrikák][lnk-metrics]
* [Figyelési műveletek][lnk-monitor]

Az IoT-központ képességeit további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató][lnk-devguide]
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]

Az IoT Hub eszköz kiépítése szolgáltatás segítségével nulla-érintés engedélyezése megismeréséhez just-in-time kiépítés, lásd: 

* [Az Azure IoT Hub eszköz-üzembehelyezési szolgáltatás][lnk-dps]


[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
