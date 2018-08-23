---
title: Importálás és exportálás az Azure IoT Hub-eszközidentitásokat |} A Microsoft Docs
description: Hogyan használható az Azure IoT service SDK szemben az eszközidentitás-jegyzék, importálása és exportálása az eszközidentitások tömeges műveletek végrehajtásához. Importálási műveletek létrehozása, frissítése és törlése az eszközidentitások tömeges lehetővé teszik.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: dobett
ms.openlocfilehash: aedf2d0012f5af8ea2eb8e944f06b20c7f1a6bb8
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2018
ms.locfileid: "42055297"
---
# <a name="manage-your-iot-hub-device-identities-in-bulk"></a>Az IoT Hub eszközidentitások tömeges kezelése

Minden IoT hub tartalmaz egy identitásjegyzéket, eszköz erőforrások létrehozása a szolgáltatás segítségével. Az eszközidentitás-jegyzék azt is lehetővé teszi, hogy ki férhet hozzá az eszközök felé néző végpontok. Ez a cikk bemutatja, hogyan importálhat és exportálhat az eszközidentitások tömeges, és a egy identitásjegyzéket.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Importálás és exportálás műveletek egy kontextusában *feladatok* , amely lehetővé teszi az IoT hub elleni tömeges szolgáltatás műveletek végrehajtásához.

A **RegistryManager** osztály tartalmazza a **ExportDevicesAsync** és **ImportDevicesAsync** használó metódusok a **feladat** keretrendszer. Ezek a metódusok lehetővé teszi exportálása, importálása és a egy IoT hub-identitásjegyzék teljes szinkronizálása.

Ez a témakör ismerteti a használatával a **RegistryManager** osztály és **feladat** rendszer végrehajtsa bulk behozatali és kiviteli az eszközök és a egy IoT hub identitásjegyzékében. Az Azure IoT Hub Device Provisioning Service használatával engedélyezze a beavatkozás nélküli, just-in-time egy vagy több IoT hubon való üzembe helyezést, emberi beavatkozás nélkül. További tudnivalókért tekintse meg a [provisioning service dokumentációja](/azure/iot-dps).


## <a name="what-are-jobs"></a>Mik azok a feladatok?

Identitásjegyzék műveletei használja a **feladat** rendszer Ha a művelet:

* Rendelkezik egy potenciálisan hosszú végrehajtási időt képest standard szintű futásidőt műveletek.

* A felhasználó egy nagy mennyiségű adatot adja vissza.

Egyetlen API hívással Várakozás vagy blokkolja-e a művelet eredménye helyett a művelet aszinkron módon létrehoz egy **feladat** , hogy az IoT hub számára. A műveletet, majd azonnal értéket ad vissza egy **JobProperties** objektum.

Az alábbi C# kódrészlet bemutatja, hogyan exportálási feladat létrehozása:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Használatához a **RegistryManager** a C#-kódot az osztályhoz, adja hozzá a **Microsoft.Azure.Devices** NuGet-csomagot a projekthez. A **RegistryManager** osztály szerepel a **Microsoft.Azure.Devices** névtér.

Használhatja a **RegistryManager** osztály állapotának lekérdezése a **feladat** használatával a visszaadott **JobProperties** metaadatait. Hozzon létre egy példányt, a **RegistryManager** osztály, használja a **CreateFromConnectionString** metódust.

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

A kapcsolati karakterlánc megkeresése az IoT hub, az Azure Portalon:

- Keresse meg az IoT-központot.

- Válassza ki **megosztott elérési házirendek**.

- Válasszon ki egy szabályzatot, figyelembe véve a szükséges engedélyekkel.

- Másolja a connectionstring a panel a képernyő jobb oldalán.

Az alábbi C# kódrészlet bemutatja, hogyan öt másodpercenként lekérdezi a megtekintéséhez, ha a feladat végrehajtása befejeződött:

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

Használja a **ExportDevicesAsync** metódus az IoT hub eszközidentitás-jegyzék, a teljes exportálása egy [Azure Storage](../storage/index.yml) blob-tároló használatával egy [közös hozzáférésű Jogosultságkód](../storage/common/storage-security-guide.md#data-plane-security).

Ez a módszer lehetővé teszi, hogy az eszköz adatai megbízható biztonsági másolatokat készíthet egy Ön által megadott blob-tárolóban.

A **ExportDevicesAsync** metódus két paraméter szükséges:

* A *karakterlánc* , amely tartalmaz egy URI-ját egy blob-tárolóba. Ez az URI tartalmaznia kell egy SAS-token, amely írási hozzáférést biztosít a tárolóhoz. A feladat egy blokkblobot szerializált export eszköz adatokat tárolni ebben a tárolóban hoz létre. A SAS-jogkivonatát ezekkel az engedélyekkel kell tartalmaznia:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* A *logikai* , amely azt jelzi, hogy szeretné-e az adatok exportálása hitelesítési kulcsok kizárása. Ha **hamis**, hitelesítési kulcsok szerepelnek exportálás kimenete. Ellenkező esetben kulcsok vannak exportálva mint **null**.

Az alábbi C# kódrészlet azt mutatja, amely tartalmazza az eszköz hitelesítési kulcsokat az adatok exportálása az exportálási feladat indítása, és ezután lekérdezi a befejezési:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
  await registryManager.ExportDevicesAsync(containerSasUri, false);

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

A feladat kimenetét tárolja a megadott blob-tároló nevű blokkblobként **devices.txt**. A kimeneti adatokat soronként egy eszközzel rendelkező JSON-szerializált eszközadatok, áll.

Az alábbi példa bemutatja a kimeneti adatokat:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Ha egy eszköz az ikereszköz adatok rendelkezik, majd az ikereszköz is exportálás és az eszközön lévő adatokat. Az alábbi példa bemutatja ezt a formátumot. A "twinETag" sor, amíg a teljes ikereszköz adatok összes adata.

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

Ha ezeket az adatokat a code-ban való hozzáférésre van szüksége, is könnyen deszerializálása ezen adatok segítségével a **ExportImportDevice** osztály. Az alábbi C# kódrészlet bemutatja, hogyan olvassa el a korábban exportált blokkblobba eszköz adatai:

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

## <a name="import-devices"></a>Eszközök importálása

A **ImportDevicesAsync** metódus az a **RegistryManager** osztály lehetővé teszi az IoT hub-identitásjegyzék tömeges importálás és a szinkronizálási műveletek végrehajtása. Például a **ExportDevicesAsync** metódus, a **ImportDevicesAsync** metódus az **feladat** keretrendszer.

Legyen óvatos használatával a **ImportDevicesAsync** metódus mert mellett az eszközidentitás-jegyzékben lévő új eszközök kiépítése, is frissíteni és törölni a meglévő eszközök.

> [!WARNING]
> Az importálási művelet nem vonható vissza. Mindig készítsen biztonsági másolatot a meglévő adatok használatával a **ExportDevicesAsync** metódus egy másik blob-tárolóba előtt tömeges megváltozik biztosít az identitásjegyzékhez.

A **ImportDevicesAsync** metódus két paraméter szükséges:

* A *karakterlánc* , amely tartalmaz egy URI-ját egy [Azure Storage](../storage/index.yml) blobtároló adatokként *bemeneti* a feladathoz. Ez az URI tartalmaznia kell egy SAS-token, amely olvasási hozzáférést biztosít a tárolóhoz. Ez a tároló tartalmaznia kell egy blob nevű **devices.txt** , amely tartalmazza a szerializált eszközön lévő adatokat importálja az eszközidentitás-jegyzékében. Az adatok importálása tartalmaznia kell az eszköz adatai ugyanazon JSON formátumban, amely a **ExportImportDevice** feladat használ, amikor létrehozza a **devices.txt** blob. A SAS-jogkivonatát ezekkel az engedélyekkel kell tartalmaznia:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* A *karakterlánc* , amely tartalmaz egy URI-ját egy [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) blobtároló adatokként *kimeneti* a feladatból. A feladat létrehoz egy blokkblobot ebben a tárolóban, az importálás befejezése bármilyen hiba adatainak tárolására **feladat**. A SAS-jogkivonatát ezekkel az engedélyekkel kell tartalmaznia:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> A két paraméter blob-tárolóban is mutat. A különböző paraméterek egyszerűen engedélyezze az adatok felett több szabályozási, a kimeneti tárolóhoz a további engedélyekkel kell rendelkeznie.

Az alábbi C# kódrészlet bemutatja az importálási feladat indítása:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Ez a módszer is használható az adatok importálása az ikereszköz. A bemeneti adatok formátuma megegyezik a formátumot, ahogyan a **ExportDevicesAsync** szakaszban. Ezzel a módszerrel importálja újra az exportált adatok. A **$metadata** nem kötelező.

## <a name="import-behavior"></a>Importálás viselkedés

Használhatja a **ImportDevicesAsync** metódus az eszközidentitás-jegyzékben lévő tömeges műveletek végrehajtásához:

* Új eszközök tömeges regisztrálása
* A meglévő eszközök tömeges törlés
* Tömeges állapotmódosítások (engedélyezése vagy letiltása az eszköz)
* Új eszköz hitelesítési kulcsok tömeges hozzárendelését
* Tömeges automatikus – eszköz hitelesítési kulcsok újragenerálása
* Az ikereszköz adatok tömeges frissítés

A fenti műveletek egy tetszőleges kombinációját is végezhet **ImportDevicesAsync** hívja. Ha például új eszközök regisztrálása és törölheti vagy frissítheti a meglévő eszközök egyszerre. A használatakor a **ExportDevicesAsync** metódus, telepíthet teljesen át eszközeit egy IoT hub egy másikba.

Ha a fájl ikereszköz metaadatokat tartalmaz, a metaadatok felülírja a meglévő ikereszköz metaadatokat. Ha a fájl nem tartalmaz ikereszköz metaadatokat, majd csak a `lastUpdateTime` metaadatok frissül az aktuális időt használja.

A választható **amelyben a importMode** tulajdonság a szerializálási adatok importálása az importálási folyamat eszközönkénti szabályozásához minden egyes eszközhöz. A **amelyben a importMode** tulajdonsága a következő beállításokat:

| amelyben a importMode | Leírás |
| --- | --- |
| **createOrUpdate** |Ha egy eszköz nem létezik a megadott **azonosító**, újonnan regisztrálva van. <br/>Ha az eszköz már létezik, a megadott bemeneti adatok nélkül tekintettel a következőkre felülírja a meglévő adatokat a **ETag** értéket. <br> A felhasználó igény szerint megadhatja az ikereszköz adatokat és az eszközön lévő adatokat. Az ikereszköz ETag címke, ha meg van adva, a feldolgozása egymástól függetlenül, az eszköz etag. A meglévő ikereszköz etag-eltérés van, ha hiba történt a naplófájlba írt. |
| **létrehozás** |Ha egy eszköz nem létezik a megadott **azonosító**, újonnan regisztrálva van. <br/>Ha az eszköz már létezik, hibát a naplófájlba írt. <br> A felhasználó igény szerint megadhatja az ikereszköz adatokat és az eszközön lévő adatokat. Az ikereszköz ETag címke, ha meg van adva, a feldolgozása egymástól függetlenül, az eszköz etag. A meglévő ikereszköz etag-eltérés van, ha hiba történt a naplófájlba írt. |
| **update** |Ha egy eszköz már létezik a megadott **azonosító**, felülírja a meglévő adatokat a megadott bemeneti adatok nélkül tekintettel a következőkre a **ETag** értéket. <br/>Ha az eszköz nem létezik, hibát a naplófájlba írt. |
| **updateIfMatchETag** |Ha egy eszköz már létezik a megadott **azonosító**, meglévő információt felülírja a megadott bemeneti adatok csak akkor, ha van egy **ETag** felel meg. <br/>Ha az eszköz nem létezik, hibát a naplófájlba írt. <br/>Ha egy **ETag** eltérés, hiba van a naplófájlba írt. |
| **createOrUpdateIfMatchETag** |Ha egy eszköz nem létezik a megadott **azonosító**, újonnan regisztrálva van. <br/>Ha az eszköz már létezik, meglévő információt felülírja a megadott bemeneti adatok csak akkor, ha van egy **ETag** felel meg. <br/>Ha egy **ETag** eltérés, hiba van a naplófájlba írt. <br> A felhasználó igény szerint megadhatja az ikereszköz adatokat és az eszközön lévő adatokat. Az ikereszköz ETag címke, ha meg van adva, a feldolgozása egymástól függetlenül, az eszköz etag. A meglévő ikereszköz etag-eltérés van, ha hiba történt a naplófájlba írt. |
| **törlés** |Ha egy eszköz már létezik a megadott **azonosító**, nélkül tekintettel a következőkre törölné a rendszer a **ETag** értéket. <br/>Ha az eszköz nem létezik, hibát a naplófájlba írt. |
| **deleteIfMatchETag** |Ha egy eszköz már létezik a megadott **azonosító**, törölné a rendszer csak akkor, ha van egy **ETag** felel meg. Ha az eszköz nem létezik, hibát a naplófájlba írt. <br/>Ha az ETag nem egyezik, a naplófájlba írt hiba. |

> [!NOTE]
> Ha a szerializálási adatok nem explicit módon definiál egy **amelyben a importMode** jelző eszköz, a rendszer alapértelmezés szerint **createOrUpdate** az importálási művelet során.

## <a name="import-devices-example--bulk-device-provisioning"></a>Példa eszközök importálása – tömeges eszköz kiépítése

Az alábbi C# kódminta azt ábrázolja, hogyan hozzon létre több eszközidentitások, amely:

* Például a hitelesítési kulcsok.
* A blokkblobok, eszköz adatai írni.
* Az eszközök importálása az eszközidentitás-jegyzék.

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
JobProperties importJob =
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

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

## <a name="import-devices-example--bulk-deletion"></a>Importálási eszköz példa – tömeges törlés

Az alábbi példakód bemutatja, hogyan használja az előző példakód hozzáadott eszközök törlése:

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

Az alábbi példakód bemutatja, hogyan hozhat létre egy [SAS URI-t](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md) az olvasási, írási és törlési engedélyek blob tárolóhoz:

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

Ebben a cikkben megtanulta, hogyan az IoT hub szemben az eszközidentitás-jegyzék tömeges műveletek végrehajtásához. Az alábbi hivatkozásokból tudhat meg többet az Azure IoT Hub kezelése:

* [Az IoT Hub-metrikák](iot-hub-metrics.md)
* [Műveletek figyelése](iot-hub-operations-monitoring.md)

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Az IoT Hub fejlesztői útmutató](iot-hub-devguide.md)
* [Edge-eszközök mesterséges Intelligencia telepítése az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)

Böngészhet a beavatkozás nélküli, just-in-time kiépítését lehetővé tevő, olvassa el az IoT Hub Device Provisioning Service használatával: 

* [Azure IoT Hub Device Provisioning Service](/azure/iot-dps)
