---
title: Az Azure Service Fabric biztonsági másolat visszaállítása |} A Microsoft Docs
description: A rendszeres biztonsági mentését, és állítsa vissza a szolgáltatás a Service Fabric-adatok biztonsági másolatából az alkalmazásadatok visszaállításához.
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
ms.openlocfilehash: 66df926eaaf771fdde4d2eb59772b9d38a87edf0
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313228"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Az Azure Service Fabric biztonsági másolat visszaállítása

Az Azure Service Fabric Reliable Stateful services és Reliable Actors fenntarthat egy változtatható, mérvadó állapotot a kérések és válaszok tranzakció befejezése után. Az állapotalapú szolgáltatások előfordulhat, hogy hosszú ideig leáll vagy katasztrófa miatt adat elvész. Ha ez történik, a szolgáltatást kell kell biztonsági mentésből a legújabb elfogadható, hogy a munka folytatásához is.

Ha például egy szolgáltatást, hogy a következő esetekben elleni védelem érdekében az adatok biztonsági mentését is konfigurálhat:

- **Kis-és vész-helyreállítási**: Egy egész Service Fabric-fürt végleges adatvesztést.
- **Kis-és az adatvesztést**: A replikák szolgáltatás partíció többsége végleges adatvesztést.
- **Kis-és az adatvesztést**: Véletlen törlés és adatsérülések a szolgáltatás. A rendszergazda például megtekintővel törli a szolgáltatást.
- **Kis-és adatsérülés**: Hibák a szolgáltatás adatsérülést okozhat. Adatsérülés például csak akkor fordulhat elő, amikor a kód frissítése hibás adatokat ír egy megbízható gyűjteményben. Ebben az esetben előfordulhat, mind a kódot, és az adatok visszaállítása egy korábbi állapotba.

## <a name="prerequisites"></a>Előfeltételek

- A visszaállítás elindítása a _Hibaelemzési szolgáltatás (FAS)_ engedélyezni kell a fürt számára.
- A _biztonsági másolat visszaállítása szolgáltatás (BRS)_ létrehozott biztonsági másolat.
- A visszaállítás csak egy partíciót, aktiválható.

## <a name="triggered-restore"></a>Aktivált visszaállítása

A visszaállítás aktiválhatja az az alábbi esetekben:

- Az adatok visszaállítás _vész-helyreállítási_.
- Az adatok visszaállítás _sérülés vagy adatvesztés_.

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Vészhelyreállítás esetén adatok visszaállítása

Ha egy egész Service Fabric-fürt elveszett, helyreállíthatja az adatokat a partíciók a megbízható állapotalapú szolgáltatás és a Reliable Actors. A kívánt biztonsági mentés választható ki a listából, használatakor [biztonsági mentési tár adatokkal GetBackupAPI](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). A biztonsági mentési enumerálás egy alkalmazás, szolgáltatás vagy partíció is lehet.

A következő példában a tegyük fel, hogy elvesznek a fürt ugyanazon a fürtön, amely az említett [rendszeres biztonsági megbízható állapotalapú szolgáltatás és a Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). Ebben az esetben `SampleApp` üzemel, a biztonsági mentési szabályzat engedélyezve van, és a biztonsági másolatokat az Azure Storage vannak konfigurálva.

A REST API használatával az összes partíciók belül létrehozott biztonsági mentéseket listáját adja vissza egy PowerShell-parancsprogram végrehajtása a `SampleApp` alkalmazás. Az API-t a biztonsági mentési tár információkat listázhatja az elérhető biztonsági másolatok van szükség.

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

A visszaállítás elindítása, jelölje ki a biztonsági mentéseket. Az aktuális biztonsági vész-helyreállítási lehet például a következő biztonsági mentés:

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

A visszaállítási API-t, meg kell adnia a _biztonsági mentés azonosítója:_ és _BackupLocation_ részleteit.

Is kell választania a célpartíción leírt módon a másodlagos fürtben a [partícióséma](service-fabric-concepts-partitioning.md#get-started-with-partitioning). A fürt másik biztonsági mentés a partícióra az eredeti elveszett fürthöz partícióséma megadott helyreáll.

Ha másik fürtön a Partícióazonosító `1c42c47f-439e-4e09-98b9-88b8f60800c6`, leképezheti a fürt eredeti Partícióazonosító `974bd92a-b395-4631-8a7f-53bd4ae9cf22` összehasonlítja a magas és alacsony kulcsot _előre particionálás (UniformInt64Partition)_.

A _nevű particionálás_, a név-érték a rendszer összehasonlítja a célpartíción alternatív fürt azonosításához.

A visszaállítás a biztonsági mentési fürt partíció szemben az alábbi kér [visszaállítási API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition):

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Egy visszaállítási TrackRestoreProgress az előrehaladását nyomon követheti.

### <a name="data-restore-for-data-corruptiondata-loss"></a>Az adatok visszaállítás _adatsérülés_/_adatvesztés_

A _adatvesztés_ vagy _adatsérülés_, megbízható állapotalapú szolgáltatás készül a partíciók és a Reliable actors – partíciók sem a választott biztonsági másolatok visszaállíthatók.

Az alábbi példa folytatása [rendszeres biztonsági megbízható állapotalapú szolgáltatás és a Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). Ebben a példában egy biztonsági mentési szabályzat engedélyezve van a partíció, és a szolgáltatás biztonsági mentések készítése az Azure Storage-ban a kívánt gyakorisággal.

Biztonsági másolat kiválasztása kimenetéből származó [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups). Ebben a forgatókönyvben a biztonsági mentés ugyanazon a fürtön, mielőtt alapján jön létre.

A visszaállítás elindítása, válassza ki a listából egy biztonsági mentés. Az aktuális _adatvesztés_/_adatsérülés_, válassza ki a következő biztonsági mentés:

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

A visszaállítási API-hoz, adja meg a _biztonsági mentés azonosítója:_ és _BackupLocation_ részleteit. A fürt rendelkezik a biztonsági mentés engedélyezve van, a Service Fabric _biztonsági másolat visszaállítása szolgáltatás (BRS)_ azonosítja a megfelelő tárolási helyét, a társított biztonsági mentési szabályzat.

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

A visszaállítási folyamat TrackRestoreProgress használatával követheti nyomon.

## <a name="track-restore-progress"></a>Visszaállítás folyamatban nyomon követése

Egy megbízható állapotalapú szolgáltatás vagy a Reliable Actor partíciójának egyszerre csak egy visszaállítási kérést fogad. Az aktuális visszaállítási kérés befejezése után a partíció csak egy újabb kérelmet fogad el. Több visszaállítási kérés is elindítható a különböző partíciók egy időben.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

A visszaállítási kérelem különböző fázisokon halad a következő sorrendben:

1. **Elfogadott**: Egy _elfogadva_ állapot visszaállítása, az azt jelzi, hogy a kért partíció elindult-e a megfelelő kérést paraméterekkel.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **InProgress**: Egy _InProgress_ visszaállítási állapot azt jelzi, hogy a visszaállítás történik a partíció a kérelemben szereplő említett biztonsági. A partíció-jelentéseket a _dataloss_ állapota.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Sikeres**, **hiba**, vagy **időtúllépési**: A kért visszaállítása a következő állapotok valamelyikében lévő elvégezhető. Van az egyes a következő jelentősége, és a válasz részletei:
    - **Sikeres**: A _sikeres_ állapot visszaállítása egy partíció helyreállt állapotát jelzi. A partíció jelentések _RestoredEpoch_ és _RestoredLSN_ állapotok a időpontja (UTC) együtt.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Hiba**: A _hiba_ visszaállítási állapot azt jelzi, hogy a visszaállítási kérelem sikertelen. A sikertelenség okát, a rendszer jelenti.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Időtúllépés**: A _időtúllépési_ állapot visszaállítása, az azt jelzi, hogy rendelkezik-e a kérés időtúllépés. Az új visszaállítási kérelem létrehozása nagyobb [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). Az alapértelmezett időtúllépési érték 10 perc. Győződjön meg arról, hogy a partíció nem adatok elvesztése állapotú visszaállítási újra kérelmezése előtt.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Automatikus visszaállítás

Konfigurálhatja a megbízható állapotalapú szolgáltatás és a fürthöz a Service Fabric Reliable Actors partíciók _automatikus visszaállítási_. Állítsa be a biztonsági mentési szabályzat `AutoRestore` való _igaz_. Engedélyezés _automatikus visszaállítási_ adatokat automatikusan visszaállítja a legutóbbi partíció biztonsági adatvesztés jelentésekor. További információkért lásd:

- [Automatikus visszaállítás engedélyezése a biztonsági mentési szabályzat](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [RestorePartition API-referencia](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
- [GetPartitionRestoreProgress API-referencia](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>További lépések
- [Rendszeres biztonsági mentési konfiguráció ismertetése](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Biztonsági másolat visszaállítása – REST API-referencia](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
