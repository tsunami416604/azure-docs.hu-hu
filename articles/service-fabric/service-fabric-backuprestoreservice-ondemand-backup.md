---
title: Igény szerinti biztonsági mentést az Azure Service Fabricben |} A Microsoft Docs
description: A Service Fabric biztonsági mentését, és állítsa vissza kell alapon az alkalmazásadatok biztonsági mentése a funkciót.
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
ms.openlocfilehash: 2e83dcb4ce42a0c08ce482e9ffbbf1ac18634fd5
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730202"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Igény szerinti biztonsági mentést az Azure Service Fabricben

A Reliable Stateful services és Reliable actors – az adatok biztonsági másolat készíthető a vészhelyreállítási vagy adatok elvesztése forgatókönyvek.

A Service Fabric van ellátva a szolgáltatások [adatok rendszeres biztonsági mentés](service-fabric-backuprestoreservice-quickstart-azurecluster.md) és kell elszámolással, az adatok biztonsági mentését. Igény szerinti biztonsági mentés akkor hasznos, ahogy azt ellen védelmet nyújt _adatvesztés_/_adatsérülés_ oka a tervezett módosításokat a mögöttes szolgáltatás vagy a környezetben.

Az igény szerinti biztonsági másolat szolgáltatásai hasznos rögzítése a szolgáltatásokat, mielőtt bármilyen manuálisan aktivált műveletet, a szolgáltatás-vagy service-környezet állapotát. A szolgáltatás bináris fájljai, azaz módosítása, frissítése vagy a szolgáltatás; alacsonyabb verziójúra változtatása mert az adatsérülés elleni az alkalmazás kódjában szereplő hibák által védett adatokat.

## <a name="triggering-on-demand-backup"></a>Igény szerinti biztonsági mentésének elindítása

Az igény szerinti biztonsági mentést biztonságimásolat-fájlok feltöltése szükséges tárhelyre vonatkozó információkat. Az igény szerinti biztonsági mentést a rendszeres biztonsági mentési szabályzat vagy a megadott tárolási igény szerinti biztonsági mentési kérelmet a megadott tárolót fog történni.

### <a name="on-demand-backup-to-storage-specified-by-periodic-backup-policy"></a>Rendszeres biztonsági mentési szabályzat által megadott tárolási igény szerinti biztonsági mentés

A partíció egy megbízható állapotalapú szolgáltatás vagy a Reliable Actor-extra Storage rendszeres biztonsági mentési szabályzatban megadott kell alapján biztonsági mentésével kapcsolatos lehet igényelni. 

A következő esetben a minta fenntartása említetteknek megfelelően [rendszeres biztonsági megbízható állapotalapú szolgáltatás és a Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), amelyben a partíció egy biztonsági mentési szabályzattal rendelkezik és a kívánt gyakorisággal biztonsági mentési tart az Azure-Tárfiókba.

Az igény szerinti biztonsági mentést partícióazonosító `974bd92a-b395-4631-8a7f-53bd4ae9cf22` is elindítható a [BackupPartition] (https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API-t. 

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

A [igény szerinti biztonsági mentési folyamat](service-fabric-backuprestoreservice-ondemand-backup.md#tracking-on-demand-backup-progress) által nyomon követett [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API-t.

### <a name="on-demand-backup-to-specified-storage"></a>Igény szerinti biztonsági mentést az adott tárolóba

Az igény szerinti biztonsági mentést és a tároló adatait egy megbízható állapotalapú szolgáltatás vagy a Reliable Actor partíciója lehet igényelni. A storage információkat kell megadni a igény szerinti biztonsági mentési kérés részeként.

Az igény szerinti biztonsági mentést partícióazonosító `974bd92a-b395-4631-8a7f-53bd4ae9cf22` is elindítható a [BackupPartition] (https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API-hoz az Azure storage-információkat alább látható módon.

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

A [igény szerinti biztonsági mentési folyamat](service-fabric-backuprestoreservice-ondemand-backup.md#tracking-on-demand-backup-progress) által nyomon követett [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API-t.


## <a name="tracking-on-demand-backup-progress"></a>Igény szerinti biztonsági mentési folyamat nyomon követése

Egy megbízható állapotalapú szolgáltatás vagy a Reliable Actor partíciójának egyszerre csak egy igény szerinti biztonsági mentési kérelmet fogad. Egy másik kérelem elfogadható csak az aktuális igény szerinti biztonsági mentési kérés befejezése után. 

Több igény szerinti biztonsági mentési kérelmet is elindítható a különböző partíciók egy egy időben.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Az igény szerinti biztonsági mentési kérelmet folyamatban van a következő állapotok egyike lehet

* **Elfogadott** – a biztonsági mentés a partíción kezdeményeztek, és folyamatban van.
    ```
    BackupState             : Accepted
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          : 
    EpochOfLastBackupRecord : 
    LsnOfLastBackupRecord   : 0
    FailureError            : 
    ```
    
* **Sikeres / sikertelen / időtúllépés** – A kért a következő állapotok valamelyikében lévő igény szerinti biztonsági mentés elvégezhető. Van az egyes alábbi jelentősége, és a válaszok részleteit.

    * **Sikeres** – a biztonsági mentés állapota _sikeres_ azt jelzi, hogy a partíció állapota készüljön biztonsági mentés sikeresen megtörtént. A válasz biztosít __BackupEpoch__ és __BackupLSN__ a partíció együtt a időpontja (UTC).
        ```
        BackupState             : Success
        TimeStampUtc            : 2018-11-21T20:00:01Z
        BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
        BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
        EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
        LsnOfLastBackupRecord   : 36
        FailureError            : 
        ```
        
    * **Hiba** – a biztonsági mentés állapota _hiba_ jelzi a hiba történt a partíció állapota biztonsági mentése során. A sikertelenség okát, válasz ismerteti.
        ```
        BackupState             : Failure
        TimeStampUtc            : 0001-01-01T00:00:00Z
        BackupId                : 00000000-0000-0000-0000-000000000000
        BackupLocation          : 
        EpochOfLastBackupRecord : 
        LsnOfLastBackupRecord   : 0
        FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
        ```
       
    * **Időtúllépés** -a biztonsági mentés állapota _időtúllépési_ azt jelzi, hogy a partíció rendszerállapot biztonsági mentése nem sikerült létrehozni a megadott időkereten; alapértelmezett időtúllépési értéke 10 perc. Az új biztonsági mentési kérelem indítása nagyobb [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) ebben a forgatókönyvben javasoljuk a igény szerinti biztonsági mentési kérelmet.

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
- [Biztonsági másolat visszaállítása – REST API-referencia](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

