---
title: Az Azure Service Fabric biztonsági másolat visszaállítása |} A Microsoft Docs
description: A Service Fabric rendszeres biztonsági mentését, és a szolgáltatás az adatok visszaállítása biztonsági másolatból az alkalmazásadatok visszaállításához.
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 802F55B6-6575-4AE1-8A8E-C9B03512FF88
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 69604decab354368f336b85bfa1497671f0c3101
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730197"
---
#  <a name="restoring-backup-in-azure-service-fabric"></a>Az Azure Service Fabric biztonsági másolat visszaállítása


A Reliable Stateful services és Reliable Actors a Service Fabric változtatható, mérvadó állapotot a kérés és válasz vagy a teljes tranzakció túl fenntartására. Az állapotalapú szolgáltatások hosszú ideig leáll, vagy elveszíti az információk katasztrófa miatt, ha azt kell ahhoz, hogy továbbra is a szolgáltatást biztosító után azt fog érkezni legújabb elfogadható biztonsági-másolat állapota lehet visszaállítani.

Például szolgáltatást érdemes biztonsági mentési adatokat a következő esetekben a védelme érdekében:

- Ha egy egész Service Fabric-fürt állandó elvesztését. **(A kis vész-helyreállítási - DR)**
- A replikák szolgáltatás partíció többsége végleges adatvesztést. **(A kis adatvesztés)**
- Felügyeleti hibák, amellyel az állapot véletlenül lekérdezi törölték vagy sérült állapotba kerül. Például egy megfelelő jogosultsággal rendelkező rendszergazda hibásan törli a szolgáltatást. **(Adatvesztés eset)**
- Hibák a szolgáltatásban, amely adatsérülést okozhat. Adatsérülés például akkor is jelentkezhet, ha egy szolgáltatás kód frissítése elindítja az írás a hibás egy megbízható gyűjteményben. Ebben az esetben a kódot és az adatok is előfordulhat alakítható vissza egy korábbi állapotba. **(Adatsérülés eset)**


## <a name="prerequisites"></a>Előfeltételek
* Aktiválásához állítsa vissza a _Hibaelemzési szolgáltatás (FAS)_ engedélyezni kell a fürt számára
* A biztonsági mentés, visszaállítás lennie kell tett _biztonsági másolat visszaállítása szolgáltatás (BRS)_
* A visszaállítás csak egy partíciót, aktiválható.

## <a name="triggering-restore"></a>Visszaállításának

A visszaállítás a következő esetekben lehet 
* Adatok visszaállítása, _vész-helyreállítási_ (DR)
* Adatok visszaállítása, _adatsérülés / adatvesztés_



### <a name="data-restore-in-the-event-of-disaster-recovery-dr"></a>Adatok visszaállítása, _vész-helyreállítási_ (DR)
Teljes Service Fabric-fürt elvesznek, ha az adatok a partíciók a megbízható állapotalapú szolgáltatás és a Reliable Actors visszaállíthatók egy másik fürtön. A kívánt biztonsági mentés kiválaszthatók felsorolása [biztonsági mentési tár adatokkal GetBackupAPI](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). A biztonsági mentés enumerálás egy alkalmazás, szolgáltatás vagy partíció is lehet.

Lehetővé teszi, hogy tegyük fel, az elveszett fürthöz a fürt említett volt [rendszeres biztonsági megbízható állapotalapú szolgáltatás és a Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), amely kellett `SampleApp` üzembe helyezve, ahol a partíció lett kellene biztonsági mentési szabályzattal és biztonsági másolatok az Azure Storage-ban is történik. 


Hajtsa végre a következő PowerShell-szkript meghívása a REST API-t enumerálni az összes partíción belül létrehozott biztonsági mentéseket a `SampleApp` alkalmazás elveszett Service Fabric-fürtben. Az enumerálás API tárolóinformációk egy alkalmazás a biztonsági másolatok tárolására, az elérhető biztonsági másolatok számbavételekor van szükség. 

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
Futtassa a fenti kimeneti példa:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            : 
```



A visszaállítás indítására, válassza ki a kívánt biztonsági mentés kell. Lehetővé teszik a kívánt biztonsági mentés, a jelenlegi vészhelyreállítás (DR) lehet a következő biztonsági mentés

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 
```

A visszaállítási API-t, hogy kell adnia a __biztonsági mentés azonosítója:__ és __BackupLocation__ részleteit. A választott megfelelően kell a fürt másik partíció a [partícióséma](service-fabric-concepts-partitioning.md#get-started-with-partitioning). A felhasználó felelőssége, hogy a biztonsági mentés visszaállításához a másodlagos fürtből az eredeti elveszett fürthöz partíciós sémája alapján célpartíció választott.

Tegyük fel, hogy a Partícióazonosító másik fürtön `1c42c47f-439e-4e09-98b9-88b8f60800c6`, amely leképezi a fürt eredeti Partícióazonosító `974bd92a-b395-4631-8a7f-53bd4ae9cf22` összehasonlítja a magas és alacsony kulcsot _előre particionálás (UniformInt64Partition)_.

A _nevű particionálás_, a név-érték a rendszer összehasonlítja a célpartíción alternatív fürt azonosításához.

A visszaállítás elleni biztonsági mentési fürt partíció kért a következő [API visszaállítása](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)

```powershell 
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 
A visszaállítás állapotát lehet [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress)

### <a name="data-restore-in-the-event-of-data-corruption--data-loss"></a>Adatok visszaállítása, _adatsérülés / adatvesztés_

A kis-és _adatvesztés_ vagy _adatsérülés_ az adatok a partíciók a megbízható állapotalapú szolgáltatás és a Reliable Actors vissza tudja állítani a kiválasztott biztonsági mentések bármelyik. A következő esetben a minta fenntartása említetteknek megfelelően [rendszeres biztonsági megbízható állapotalapú szolgáltatás és a Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors), amelyben a partíció egy biztonsági mentési szabályzattal rendelkezik és a kívánt gyakorisággal biztonsági mentési tart egy Azure Storage. 

A kívánt biztonsági mentés van kiválasztva, a kimenetből [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). Ebben a forgatókönyvben a biztonsági mentés létrehozása ugyanazon a fürtön a múltbeli.
A visszaállítás indítására, válassza ki a kívánt biztonsági mentés a listából kell. Lehetővé teszik az aktuális meg a kívánt biztonsági _adatvesztés_ / _adatsérülés_ kell a következő biztonsági mentés

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 
```

A visszaállítási API-t, hogy kell adnia a __biztonsági mentés azonosítója:__ és __BackupLocation__ részleteit. Mivel a fürt biztonsági mentés engedélyezve van a Service Fabric _biztonsági másolat visszaállítása szolgáltatás (BRS)_ azonosítja a megfelelő tárolási helyét, a társított biztonsági mentési szabályzat alól.

```powershell
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4', 
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

A visszaállítás állapotát lehet [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress)


## <a name="tracking-restore-progress"></a>Követési visszaállítás folyamatban

Egy megbízható állapotalapú szolgáltatás vagy a Reliable Actor partíciójának egyszerre csak egy visszaállítási kérést fogad. Egy másik kérelem elfogadható csak az aktuális visszaállítási kérés befejezése után. Több visszaállítási kérés is elindítható a különböző partíciók egy egy időben.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
 
$restoreResponse = (ConvertFrom-Json $response.Content) 
$restoreResponse | Format-List
```

a visszaállítási kérelem különböző fázisokon halad, a következő sorrendben

1. __Elfogadott__ – a visszaállítás állapota _elfogadva_ azt jelzi, hogy a kért aktiválódott-e a megfelelő kérést paraméterekkel.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
2. __InProgress__ – a visszaállítás állapota _InProgress_ azt jelzi, hogy a partíció halad át a visszaállítás a kérelemben szereplő említett biztonsági mentést. A partíció jelenteni _dataloss_ állapota.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. __Sikeres__/ __hiba__/ __időtúllépési__ – A kért visszaállítási hajtható végre a következő állapotok valamelyikében lévő. Van az egyes alábbi jelentősége, és a válaszok részleteit.
       
    * __Sikeres__ – a visszaállítás állapota _sikeres_ azt jelzi, hogy a partíció állapota helyreállt. A válasz a időpontja (UTC) együtt a partíció RestoreEpoch és RestordLSN biztosít. 
    
        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```
        
    *. __Hiba__ – a visszaállítás állapota _hiba_ azt jelzi, hogy a visszaállítási kérelem sikertelen. A sikertelenség okát, kérelem ismerteti.
        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    *. __Időtúllépés__ – a visszaállítás állapota _időtúllépés_ azt jelzi, a kérelem időkorlátja. Az új visszaállítási kérelmében nagyobb [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) ajánlott; alapértelmezett időtúllépési érték 10 perc. Ügyeljen arra, hogy a partíció adatok elvesztése állapotból, újból a restore kérelmezése előtt javasolt.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="auto-restore"></a>Automatikus visszaállítás

A partíciók a Reliable Stateful service és a Reliable Actors a Service Fabric-fürt konfigurálható _automatikus visszaállítási_. A biztonsági mentési szabályzat létrehozásakor a szabályzat rendelkezhet `AutoRestore` beállítása _igaz_.  Engedélyezés _automatikus visszaállítási_ egy partíció visszaállítja az adatokat a legújabb biztonsági adatvesztés készként.
 
 [Automatikus visszaállítás engedélyezése a biztonsági mentési szabályzat](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)


[RestorePartition API-referencia](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
[GetPartitionRestoreProgress API-referencia](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>További lépések
- [Rendszeres biztonsági mentési konfiguráció ismertetése](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Biztonsági másolat visszaállítása – REST API-referencia](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
