---
title: Az Azure IoT Hub eszközidentitásainak importálása/exportálása | Microsoft dokumentumok
description: Az Azure IoT-szolgáltatás SDK-jának használata tömeges műveletek futtatásához az identitásjegyzéken az eszközidentitások importálásához és exportálásához. Az importálási műveletek lehetővé teszik az eszközidentitások tömeges létrehozását, frissítését és törlését.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: robinsh
ms.openlocfilehash: 2a0394e6e7c17e0a4954bbdddb1d5b2811959746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371579"
---
# <a name="import-and-export-iot-hub-device-identities-in-bulk"></a>IoT Hub-eszközidentitások tömeges importálása vagy exportálása

Minden IOt-központ rendelkezik egy identitás-beállításjegyzékkel, amely segítségével eszközre jutó erőforrásokat hozhat létre a szolgáltatásban. Az identitás-beállításjegyzék lehetővé teszi az eszköz felé néző végpontokhoz való hozzáférést is. Ez a cikk bemutatja, hogyan importálhatja és exportálhatja az eszközidentitásokat ömlesztve egy identitásjegyzékbe és arendszerből. Ha meg szeretne tekinteni egy működő mintát C# nyelven, és megtudhatja, hogyan használhatja ezt a képességet, amikor egy hubot egy másik régióba klónoz, olvassa [el az IoT Hub klónozása című témakört.](iot-hub-how-to-clone.md)

> [!NOTE]
> Az IoT Hub a közelmúltban virtuális hálózati támogatást adott hozzá korlátozott számú régióban. Ez a szolgáltatás biztosítja az importálási és exportálási műveleteket, és szükségtelenné teszi a hitelesítéshez szükséges kulcsok átadását.  Kezdetben a virtuális hálózati támogatás csak ezekben a régiókban érhető el: *WestUS2*, *EastUS*és *SouthCentralUS*. Ha többet szeretne megtudni a virtuális hálózati támogatásról és az API-hívások megvalósításáról, olvassa el [az IoT Hub virtuális hálózatokhoz nyújtott támogatása című témakört.](virtual-network-support.md)

Az importálási és exportálási műveletek a *feladatok* környezetében zajlanak, amelyek lehetővé teszik tömeges szolgáltatási műveletek ioT hubon való végrehajtását.

A **RegistryManager** osztály tartalmazza az **ExportDevicesAsync** és **ImportDevicesAsync** metódusok, amelyek a **feladat** keretrendszer. Ezek a módszerek lehetővé teszik az IoT hub identitásbeállítás-jegyzék teljes exportálását, importálását és szinkronizálását.

Ez a témakör a **RegistryManager** osztály és a **Feladatrendszer** használatával az Eszközök tömeges importálását és exportálását ismerteti az IoT-központ identitás-beállításjegyzékébe. Az Azure IoT Hub-eszközkiépítési szolgáltatás sal is engedélyezheti a nulla érintéses, just-in-time kiépítést egy vagy több IoT-központba emberi beavatkozás nélkül. További információ: [a kiépítési szolgáltatás dokumentációja.](/azure/iot-dps)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

## <a name="what-are-jobs"></a>Mi az a munka?

Az identitás-beállításjegyzék-műveletek a **feladatrendszert** használják a művelet során:

* Potenciálisan hosszú végrehajtási időa van a szabványos futásidejű műveletekhez képest.

* Nagy mennyiségű adatot ad vissza a felhasználónak.

Ahelyett, hogy egyetlen API-hívás várakozik, vagy blokkolja a művelet eredménye, a művelet aszinkron módon létrehoz egy **feladatot** az IoT hub. A művelet ezután azonnal visszaad egy **JobProperties** objektumot.

A következő C# kódrészlet bemutatja, hogyan hozhat létre exportálási feladatot:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Ha a **RegistryManager** osztályt szeretné használni a C# kódban, adja hozzá a **Microsoft.Azure.Devices** NuGet csomagot a projekthez. A **RegistryManager** osztály a **Microsoft.Azure.Devices** névtérben található.

A **RegistryManager** osztály segítségével lekérdezheti a **feladat** állapotát a visszaadott **JobProperties** metaadatok használatával. **A RegistryManager** osztály egy példányának létrehozásához használja a **CreateFromConnectionString** metódust.

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Az IoT-központ kapcsolati karakterláncának megkereséséhez az Azure Portalon:

- Keresse meg az IoT-központot.

- Válassza **a Megosztott hozzáférési házirendek lehetőséget.**

- Válasszon ki egy házirendet, figyelembe véve a szükséges engedélyeket.

- Másolja a kapcsolati karakterláncot a képernyő jobb oldalán lévő panelről.

A következő C# kódrészlet bemutatja, hogyan lehet öt másodpercenként lehallgatni, hogy a feladat végrehajtása befejeződött-e:

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

> [!NOTE]
> Ha a tárfiók rendelkezik olyan tűzfal-konfigurációkkal, amelyek korlátozzák az IoT Hub kapcsolatát, fontolja meg [a Microsoft megbízható, első féltől származó kivétel](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) használatát (amely a felügyelt szolgáltatásidentitással rendelkező IoT-központok egyes régióiban érhető el).


## <a name="device-importexport-job-limits"></a>Eszközimportálási/-exportálási feladatkorlátok

Egyszerre csak 1 aktív eszközimportálási vagy -exportálási feladat engedélyezett az összes IoT Hub-szinthez. Az IoT Hub is korlátozza a feladatok műveletek száma. További információ: [Reference - IoT Hub kvóták és szabályozás.](iot-hub-devguide-quotas-throttling.md)

## <a name="export-devices"></a>Eszközök exportálása

Az **ExportDevicesAsync** metódus használatával exportálja az IoT hub identitásbeállítás-jegyzék teljes egészét egy Azure Storage blobtárolóba egy megosztott hozzáférésű aláírás (SAS) használatával. A megosztott hozzáférésű aláírásokról további információt az [Azure Storage-erőforrásokhoz való korlátozott hozzáférés megadása megosztott hozzáférésű aláírások (SAS) használatával](../storage/common/storage-sas-overview.md)című témakörben talál.

Ez a módszer lehetővé teszi, hogy megbízható biztonsági másolatot készítsen az eszközadatairól egy blobtárolóban, amelyet ön szabályoz.

Az **ExportDevicesAsync** metódushoz két paraméter szükséges:

* Blob-tároló URI-címét tartalmazó *karakterlánc.* Ez az URI-nak tartalmaznia kell egy SAS-jogkivonatot, amely írási hozzáférést biztosít a tárolóhoz. A feladat létrehoz egy blokkblobot ebben a tárolóban a szerializált exportálási eszköz adatok tárolására. A SAS-jogkivonatnak a következő engedélyeket kell tartalmaznia:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* Logikai *érték,* amely azt jelzi, hogy ki szeretné-e zárni a hitelesítési kulcsokat az exportálási adatokból. Ha **hamis**, hitelesítési kulcsok szerepelnek az exportálási kimenetben. Ellenkező esetben a kulcsok exportálása null értékben **lesz.**

A következő C# kódrészlet bemutatja, hogyan kezdeményezhet olyan exportálási feladatot, amely eszközhitelesítési kulcsokat tartalmaz az exportálási adatokban, majd a lekérdezést a befejezéshez:

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

A feladat tárolja a kimenetet a megadott blob tárolóban, mint egy blokk blob a neve **devices.txt.** A kimeneti adatok JSON szerializált eszközadatokból állnak, soronként egy eszközzel.

A következő példa a kimeneti adatokat mutatja be:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Ha egy eszköz két adattal rendelkezik, akkor az ikeradatok is exportálva lesznek az eszközadatokkal együtt. A következő példa ezt a formátumot mutatja be. A "twinETag" sor összes adata a végéig ikeradat.

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

Ha szüksége van ezekhez az adatokhoz a kódban, könnyen deszerializálhatja ezeket az adatokat az **ExportImportDevice** osztály használatával. A következő C# kódrészlet bemutatja, hogyan olvashatja el a korábban blokkblobba exportált eszközadatokat:

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

Az **ImportDevicesAsync** metódus a **RegistryManager** osztálylehetővé teszi, hogy tömeges importálási és szinkronizálási műveleteket hajtson végre egy IoT hub identitásjegyzékben. Az **ExportDevicesAsync** metódushoz hasonlóan az **ImportDevicesAsync** metódus is a **feladatkeretrendszert** használja.

Az **ImportDevicesAsync** metódus használatával is óvatosan használhatja, mert az új eszközök nek a személyazonossági jegyzékben való kiépítése mellett a meglévő eszközöket is frissítheti és törölheti.

> [!WARNING]
> Az importálási művelet nem állítható vissza. Mindig készítsen biztonsági másolatot a meglévő adatokról az **ExportDevicesAsync** metódussal egy másik blobtárolóba, mielőtt tömeges módosításokat hajtvégre az identitásjegyzékben.

Az **ImportDevicesAsync** metódus két paramétert vesz igénybe:

* Egy *karakterlánc,* amely egy [Azure Storage blobtároló URI-ját](../storage/index.yml) tartalmazza a feladat *bemeneteként.* Ez az URI-nak tartalmaznia kell egy SAS-jogkivonatot, amely olvasási hozzáférést biztosít a tárolóhoz. Ennek a tárolónak tartalmaznia kell egy blobot az **eszközök.txt** névvel, amely tartalmazza a szerializált eszközadatokat az identitásjegyzékbe való importáláshoz. Az importálási adatoknak ugyanabban a JSON formátumban kell tartalmazniuk az eszközadatokat, mint az **ExportImportDevice** feladat, amelyet az **devices.txt** blob létrehozásakor használ. A SAS-jogkivonatnak a következő engedélyeket kell tartalmaznia:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* Egy *karakterlánc,* amely egy [Azure Storage blobtároló URI-ját](https://azure.microsoft.com/documentation/services/storage/) tartalmazza a feladat *kimeneteként* való használatra. A feladat létrehoz egy blokkblobot ebben a tárolóban a befejezett importálási feladatból származó hibainformációk **tárolására.** A SAS-jogkivonatnak a következő engedélyeket kell tartalmaznia:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> A két paraméter ugyanarra a blobtárolóra mutathat. A különálló paraméterek egyszerűen lehetővé teszik az adatok további vezérlését, mivel a kimeneti tároló további engedélyeket igényel.

A következő C# kódrészlet bemutatja, hogyan kezdeményezhet importálási feladatot:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Ez a módszer az ikereszköz adatainak importálására is használható. Az adatbevitel formátuma megegyezik az **ExportDevicesAsync** szakaszban látható formátummal. Ily módon újraimportálhatja az exportált adatokat. A **$metadata** nem kötelező.

## <a name="import-behavior"></a>Importálási viselkedés

Az **ImportDevicesAsync** metódussal a következő tömeges műveleteket hajthatja végre az identitásjegyzékben:

* Új eszközök tömeges regisztrációja
* Meglévő eszközök tömeges törlése
* Tömeges állapotváltozások (eszközök engedélyezése vagy letiltása)
* Új eszközhitelesítési kulcsok tömeges hozzárendelése
* Az eszközhitelesítési kulcsok tömeges automatikus regenerálása
* Ikeradatok tömeges frissítése

Az előző műveletek tetszőleges kombinációját egyetlen **ImportDevicesAsync-híváson** belül hajthatja végre. Például regisztrálhat új eszközöket, és egyszerre törölheti vagy frissítheti a meglévő eszközöket. Ha az **ExportDevicesAsync** metódussal együtt használja, teljesen áttelepítheti az összes eszközt az egyik IoT hubról a másikra.

Ha az importálási fájl két metaadatot tartalmaz, akkor ez a metaadat felülírja a meglévő kettős metaadatokat. Ha az importálási fájl nem tartalmaz iker `lastUpdateTime` metaadatokat, akkor csak a metaadatok frissülnek az aktuális idő használatával.

Használja a választható **importMode** tulajdonságot az egyes eszközök importálási szerializálási adataiban az eszközönkénti importálási folyamat szabályozásához. Az **importMode** tulajdonság beállításai a következők:

| importMode | Leírás |
| --- | --- |
| **createOrUpdate** |Ha egy eszköz nem létezik a megadott **azonosítóval,** akkor az újonnan regisztrált. <br/>Ha az eszköz már létezik, a meglévő információkat felülírja a megadott bemeneti adatokkal, tekintet nélkül az **ETag** értékre. <br> A felhasználó az eszközadatokkal együtt két adatot is megadhat. Az iker etag, ha meg van adva, a feldolgozás függetlenül az eszköz etag. Ha eltérés van a meglévő iker etagjával, a rendszer hibát ír a naplófájlba. |
| **Létrehozása** |Ha egy eszköz nem létezik a megadott **azonosítóval,** akkor az újonnan regisztrált. <br/>Ha az eszköz már létezik, a rendszer hibát ír a naplófájlba. <br> A felhasználó az eszközadatokkal együtt két adatot is megadhat. Az iker etag, ha meg van adva, a feldolgozás függetlenül az eszköz etag. Ha eltérés van a meglévő iker etagjával, a rendszer hibát ír a naplófájlba. |
| **Frissítés** |Ha egy eszköz már létezik a megadott **azonosítóval,** a meglévő adatok felülíródnak a megadott bemeneti adatokkal, az **ETag** értéktől függetlenül. <br/>Ha az eszköz nem létezik, a rendszer hibát ír a naplófájlba. |
| **updateIfMatchETag** |Ha egy eszköz már létezik a megadott **azonosítóval,** a meglévő adatok csak akkor lesznek felülírva a megadott bemeneti adatokkal, ha **eTag** egyezés van. <br/>Ha az eszköz nem létezik, a rendszer hibát ír a naplófájlba. <br/>Ha az **ETag** nem egyezik, a rendszer hibát ír a naplófájlba. |
| **createOrUpdateIfMatchETag** |Ha egy eszköz nem létezik a megadott **azonosítóval,** akkor az újonnan regisztrált. <br/>Ha az eszköz már létezik, a meglévő adatok felülíródnak a megadott bemeneti adatokkal, ha **eTag** egyezés van. <br/>Ha az **ETag** nem egyezik, a rendszer hibát ír a naplófájlba. <br> A felhasználó az eszközadatokkal együtt két adatot is megadhat. Az iker etag, ha meg van adva, a feldolgozás függetlenül az eszköz etag. Ha eltérés van a meglévő iker etagjával, a rendszer hibát ír a naplófájlba. |
| **Töröl** |Ha egy eszköz már létezik a megadott **azonosítóval,** akkor az **etag** értéktől függetlenül törlődik. <br/>Ha az eszköz nem létezik, a rendszer hibát ír a naplófájlba. |
| **deleteIfMatchETag** |Ha egy eszköz már létezik a megadott **azonosítóval,** akkor csak akkor törlődik, ha **eTag** egyezés van. Ha az eszköz nem létezik, a rendszer hibát ír a naplófájlba. <br/>Ha az ETag nem egyezik, a rendszer hibát ír a naplófájlba. |

> [!NOTE]
> Ha a szerializálási adatok nem határoznak meg kifejezetten **egy eszköz importMode** jelzőjét, akkor alapértelmezés szerint az importálási művelet során **létrekell hozniazOrUpdate** szolgáltatást.

## <a name="import-devices-example--bulk-device-provisioning"></a>Eszközök importálása példa – ömlesztett eszköz kiépítése

A következő C# kódminta bemutatja, hogyan hozhat létre több eszközidentitást, amelyek:

* Adja meg a hitelesítési kulcsokat.
* Írja be az eszközadatokat egy blokkblobba.
* Importálja az eszközöket az identitásjegyzékbe.

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

## <a name="import-devices-example--bulk-deletion"></a>Például eszközök importálása – tömeges törlés

A következő kódminta bemutatja, hogyan törölheti az előző kódmintával hozzáadott eszközöket:

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

## <a name="get-the-container-sas-uri"></a>A tároló SAS URI-jának beszereznie

A következő kódminta bemutatja, hogyan hozhat létre [SAS-URI-t](../storage/common/storage-dotnet-shared-access-signature-part-1.md) olvasási, írási és törlési engedélyekkel egy blobtárolóhoz:

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

Ebben a cikkben megtanulta, hogyan hajthatja végre a tömeges műveleteket az IoT hub identitás-beállításjegyzéke ellen. Ezen műveletek közül sok, beleértve az eszközök egyik hubról a másikra való áthelyezését, az [IoT hub klónozása című IoT hub szakaszában](iot-hub-how-to-clone.md#managing-the-devices-registered-to-the-iot-hub)található eszközök kezelése. 

A klónozási cikk hez tartozik egy működő minta társítva, amely az IoT C# minták ezen az oldalon található: [Azure IoT-minták C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/), a projekt importExportDevicesSample. Letöltheti a mintát, és próbálja ki; az [IoT](iot-hub-how-to-clone.md) Hub-cikkben található utasítások találhatók.

Ha többet szeretne megtudni az Azure IoT Hub kezeléséről, olvassa el az alábbi cikkeket:

* [IoT Hub-metrikák](iot-hub-metrics.md)
* [IoT Hub-naplók](iot-hub-monitor-resource-health.md)

Az IoT Hub képességeinek további megismeréséhez lásd:

* [Az IoT Hub fejlesztői útmutatója](iot-hub-devguide.md)
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)

Az IoT Hub-eszközkiépítési szolgáltatás használatával a nulla érintéses, just-in-time kiépítés engedélyezéséről a következő témakört kell ismertennie: 

* [Azure IoT Hub Device Provisioning Service](/azure/iot-dps)
