---
title: Igény szerinti biztonsági mentést az Azure Service Fabricben |} A Microsoft Docs
description: A biztonsági másolat, és állítsa vissza a szolgáltatás a Service Fabric biztonsági mentése az alkalmazásadatok kell alapon.
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 02DA262A-EEF6-4F90-842E-FFC4A09003E5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 8a276f26367e66f55b8fc10dbcba2429dc2e5450
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062691"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Igény szerinti biztonsági mentést az Azure Service Fabricben

A Reliable Stateful services és Reliable actors – adatok katasztrófa vagy adatok elvesztése forgatókönyvek készíthető.

Az Azure Service Fabric a funkcióval rendelkezik a [adatok rendszeres biztonsági mentés](service-fabric-backuprestoreservice-quickstart-azurecluster.md) és a biztonsági mentés az adatok szükség alapon. Igény szerinti biztonsági mentés akkor hasznos, mert azt ellen védelmet nyújt _adatvesztés_/_adatsérülés_ a mögöttes szolgáltatás vagy a környezetben a tervezett módosítások miatt.

Az igény szerinti biztonsági másolat szolgáltatásai hasznosak a szolgáltatások állapotának rögzítésére, manuálisan fogja aktiválni egy szolgáltatás vagy szolgáltatás környezet művelet előtt. Például, ha módosítja a szolgáltatás bináris fájljainak frissítéskor vagy alacsonyabb verziójúra változtatása a szolgáltatást. Ebben az esetben igény szerinti biztonsági mentést is hardvermeghibásodásokkal szemben az adatok application kódhibák által.

## <a name="triggering-on-demand-backup"></a>Igény szerinti biztonsági mentésének elindítása

Igény szerinti biztonsági mentést biztonságimásolat-fájlok feltöltése szükséges tárhelyre vonatkozó információkat. Az igény szerinti biztonsági mentési helyre, adja meg, vagy a rendszeres biztonsági mentési szabályzatban, vagy egy igény szerinti biztonsági mentési kérelmet.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Rendszeres biztonsági mentési házirend által megadott tárolási igény szerinti biztonsági mentés

Konfigurálhatja a rendszeres biztonsági mentési szabályzat nagyon igény szerinti biztonsági mentést a storage egy partíciót, egy megbízható állapotalapú szolgáltatás vagy a Reliable Actor használandó.

A következő esetben a forgatókönyv folytatása [rendszeres biztonsági megbízható állapotalapú szolgáltatás és a Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). Ebben az esetben engedélyezi a biztonsági mentési házirend egy partíció használata, és a egy biztonsági mentés akkor fordul elő, az Azure Storage-ban a set gyakorisággal.

Használja a [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API-t állítsa be a partícióazonosító az igény szerinti biztonsági mentés elindítása `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Használja a [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) nyomon követése az API-t a [igény szerinti biztonsági mentési folyamat](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="on-demand-backup-to-specified-storage"></a>Igény szerinti biztonsági mentést az adott tárolóba

Igény szerinti biztonsági mentést egy partíció egy megbízható állapotalapú szolgáltatás vagy a Reliable Actor kérhetnek. Adja meg a storage-adatokat, az igény szerinti biztonsági mentési kérelem részeként.

Használja a [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API-t állítsa be a partícióazonosító az igény szerinti biztonsági mentés elindítása `974bd92a-b395-4631-8a7f-53bd4ae9cf22`. A következő Azure Storage-információkat tartalmazza:

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Használhatja a [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API-t állítsa be a nyomon követése a [igény szerinti biztonsági mentési folyamat](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

## <a name="tracking-on-demand-backup-progress"></a>Igény szerinti biztonsági mentési folyamat nyomon követése

Egy megbízható állapotalapú szolgáltatás vagy a Reliable Actor partíciójának egyszerre csak egy igény szerinti biztonsági mentési kérelmet fogad. Egy másik kérelem elfogadható csak az aktuális igény szerinti biztonsági mentési kérés befejezése után.

A különböző partíciók egy egyszerre is indíthat igény szerinti biztonsági mentési kérelmeket.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Igény szerinti biztonsági mentési kérelmek állapota a következő lehet:

- **Elfogadott**: A biztonsági mentés elkezdődött a partíció, és folyamatban van.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Sikeres**, **hiba**, vagy **időtúllépési**: A kért igény szerinti biztonsági mentést a következő állapotok valamelyikében lévő is elvégezhető:
  - **Sikeres**: A _sikeres_ biztonsági mentési állapot azt jelzi, hogy a partíció állapota rendelkezik biztonsági mentése sikeresen megtörtént. A válasz biztosít _BackupEpoch_ és _BackupLSN_ a partíció együtt a időpontja (UTC).
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Hiba**: A _hiba_ biztonsági mentési állapot azt jelzi, hogy hiba történt a partíció állapota biztonsági mentése során. A hiba oka van, mint a választ.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Időtúllépés**: A _időtúllépési_ biztonsági mentési állapot azt jelzi, hogy a partíció rendszerállapot biztonsági mentése nem sikerült létrehozni egy adott időn belül. Az alapértelmezett időtúllépési értéke 10 perc. Az új igény szerinti biztonsági mentési kérelem kezdeményezéséhez nagyobb [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) ebben a forgatókönyvben.
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>További lépések

- [Rendszeres biztonsági mentési konfiguráció ismertetése](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [BackupRestore REST API-referencia](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
