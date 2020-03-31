---
title: Igény szerinti biztonsági mentés az Azure Service Fabricben
description: Használja a biztonsági mentési és visszaállítási szolgáltatás a Service Fabric szükség esetén biztonsági másolatot készíteni az alkalmazás adatait.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: d5eada62bec49fe771373671e9438d2786d6b165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458416"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Igény szerinti biztonsági mentés az Azure Service Fabricben

A megbízható állapotalapú szolgáltatások és a reliable actors adatait a katasztrófa- vagy adatvesztési forgatókönyvek kezelésére.

Az Azure Service Fabric rendelkezik az adatok rendszeres biztonsági és biztonsági [mentési](service-fabric-backuprestoreservice-quickstart-azurecluster.md) funkcióival. Az igény szerinti biztonsági mentés azért hasznos, mert az alapul szolgáló szolgáltatás vagy környezete tervezett változásai miatt védelmet nyújt az _adatvesztési_/_adatok sérülése_ ellen.

Az igény szerinti biztonsági mentési funkciók hasznosak a szolgáltatások állapotának rögzítéséhez, mielőtt manuálisan elindítana egy szolgáltatás- vagy szolgáltatáskörnyezet-műveletet. Ha például a szolgáltatás bináris fájljait módosítja a szolgáltatás frissítésekor vagy a szolgáltatás visszaminősítésekor. Ebben az esetben az igény szerinti biztonsági mentés segíthet megvédeni az adatokat a korrupciótól az alkalmazáskód-hibák miatt.
## <a name="prerequisites"></a>Előfeltételek

- Telepítse a Microsoft.ServiceFabric.Powershell.Http modult [előzetes verzióban] konfigurációs hívások kezdeményezéséhez.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- A Microsoft.ServiceFabric.Powershell.Http module használatával ellenőrizze, hogy a fürt csatlakoztatva van-e a `Connect-SFCluster` paranccsal.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggering-on-demand-backup"></a>Igény szerinti biztonsági mentés aktiválása

Az igény szerinti biztonsági mentéshez tárolási adatokra van szükség a biztonsági másolat fájlok feltöltéséhez. Megadhatja az igény szerinti biztonsági mentés helyét, akár az időszakos biztonsági mentési házirendben, akár egy igény szerinti biztonsági mentési kérelemben.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Igény szerinti biztonsági mentés az időszakos biztonsági mentési házirend által meghatározott tárolóba

Konfigurálhatja a rendszeres biztonsági mentési szabályzatot egy megbízható állapotalapú szolgáltatás vagy megbízható aktor partíciójának használatára a tárolóba való extra igény szerinti biztonsági mentéshez.

A következő eset a forgatókönyv folytatása [a Megbízható állapotalapú szolgáltatás és a Megbízható szereplők időszakos biztonsági mentésének engedélyezése című](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)forgatókönyvben. Ebben az esetben engedélyezi a biztonsági mentési szabályzat ot egy partíció használatára, és egy biztonsági mentés történik az Azure Storage-ban egy meghatározott gyakorisággal.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell a Microsoft.ServiceFabric.Powershell.Http modul használatával

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>Rest Call a Powershell használatával

A [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API segítségével állítsa be a partícióazonosító igény `974bd92a-b395-4631-8a7f-53bd4ae9cf22`szerinti biztonsági mentésének indítását.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

A [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API segítségével engedélyezheti az igény szerinti biztonsági mentés folyamatának nyomon [követését.](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)

### <a name="on-demand-backup-to-specified-storage"></a>Igény szerinti biztonsági mentés a megadott tárolóba

Igény szerinti biztonsági mentést kérhet egy megbízható állapotalapú szolgáltatás vagy megbízható akta partíciójára. Adja meg a tárolási adatokat az igény szerinti biztonsági mentési kérelem részeként.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell a Microsoft.ServiceFabric.Powershell.Http modul használatával

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Rest Call a Powershell használatával

A [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API segítségével állítsa be a partícióazonosító igény `974bd92a-b395-4631-8a7f-53bd4ae9cf22`szerinti biztonsági mentésének indítását. Adja meg a következő Azure Storage-adatokat:

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

A [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API segítségével beállíthatja az igény szerinti biztonsági mentés folyamatának nyomon [követését.](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)

### <a name="using-service-fabric-explorer"></a>A Service Fabric Intéző használata
Győződjön meg arról, hogy a Speciális mód engedélyezve van a Service Fabric-kezelő beállításaiban.
1. Válassza ki a kívánt partíciót, és kattintson a műveletek. 
2. Válassza a Partícióbiztonsági rendszer aktiválása lehetőséget, és töltse ki az Azure adatait:

    ![Partíció biztonsági másolatának aktiválása][0]

    vagy FileShare:

    ![Partíció biztonsági másolatának aktiválása fájlmegosztás][1]

## <a name="tracking-on-demand-backup-progress"></a>Az igény szerinti biztonsági mentés folyamatának nyomon követése

Egy megbízható állapotalapú szolgáltatás vagy megbízható aktor partíciója egyszerre csak egy igény szerinti biztonsági mentési kérelmet fogad el. Egy másik kérelem csak akkor fogadható el, ha az aktuális igény szerinti biztonsági mentési kérelem befejeződött.

A különböző partíciók egyszerre aktiválhatnak igény szerinti biztonsági mentési kérelmeket.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell a Microsoft.ServiceFabric.Powershell.Http modul használatával

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>Rest Call a Powershell használatával

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Az igény szerinti biztonsági mentési kérelmek a következő állapotokban lehetnek:

- **Elfogadva**: A biztonsági mentés elindult a partíción, és folyamatban van.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Sikeres,** **sikertelen**vagy **időbeli meghosszabbítás:** A kért igény szerinti biztonsági mentés a következő állapotok bármelyikében elvégezhető:
  - **Sikeres:** _A sikeres_ biztonsági mentési állapot azt jelzi, hogy a partíció állapota sikeresen biztonsági mentést kapott. A válasz _biztonsági mentést_ és _backuplsn-t_ biztosít a partícióhoz az UTC-ben töltött idővel együtt.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Hiba**: _A hiba_ biztonsági mentési állapota azt jelzi, hogy hiba történt a partíció állapotának biztonsági mentése során. A hiba oka válaszként szerepel.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Időtúltöltés:** _Az időtúltöltési_ biztonsági mentési állapot azt jelzi, hogy a partícióállapot biztonsági mentése nem hozható létre egy adott idő alatt. Az alapértelmezett időtúlérték 10 perc. Ebben az esetben kezdeményezzen egy új, igény szerinti biztonsági mentési kérelmet nagyobb [BackupTimeout-kapcsolattal.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout)
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

- [A biztonsági mentés rendszeres konfigurálásának ismertetése](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [BackupRestore REST API-hivatkozás](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/trigger-partition-backup.png
[1]: ./media/service-fabric-backuprestoreservice/trigger-backup-fileshare.png