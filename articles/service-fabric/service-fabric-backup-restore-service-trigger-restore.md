---
title: Biztonsági mentés visszaállítása az Azure Service Fabricben
description: Használja a Service Fabric rendszeres biztonsági mentési és visszaállítási szolgáltatását az alkalmazásadatok biztonsági másolatából történő visszaállításhoz.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 1737102ee652cc2263bd0a908c1336bc93a6757b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377905"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Biztonsági mentés visszaállítása az Azure Service Fabricben

Az Azure Service Fabric, megbízható állapotalapú szolgáltatások és megbízható szereplők fenn egy mérhető, mérvadó állapot a kérelem és válasz tranzakció befejezése után. Egy állapotalapú szolgáltatás előfordulhat, hogy hosszú ideig leáll, vagy egy katasztrófa miatt adatokat veszít. Ha ez megtörténik, a szolgáltatást vissza kell állítani a legújabb elfogadható biztonsági mentésből, hogy tovább dolgozhasson.

Beállíthatja például, hogy egy szolgáltatás biztonsági másolatot készüljön az adatairól a következő esetek elleni védelem érdekében:

- **Vész-helyreállítási**: egy teljes Service Fabric-fürt végleges elvesztése.
- **Adatvesztés esetén:** a szolgáltatáspartíció replikáinak többségének végleges elvesztése.
- **Adatvesztés esetén**: A szolgáltatás véletlen törlése vagy sérülése. Például egy rendszergazda tévesen törli a szolgáltatást.
- **Adatsérülés esete**: A szolgáltatás hibái adatsérülést okoznak. Például adatsérülés akkor fordulhat elő, ha egy szolgáltatáskód-frissítés hibás adatokat ír egy megbízható gyűjteménybe. Ilyen esetben előfordulhat, hogy a kódot és az adatokat egy korábbi állapotba kell visszaállítania.

## <a name="prerequisites"></a>Előfeltételek

- A visszaállítás elindításához a _hibaelemzési szolgáltatást (FAS)_ engedélyezni kell a fürthöz.
- A _biztonsági mentés-visszaállítási szolgáltatás (BRS)_ hozta létre a biztonsági másolatot.
- A visszaállítás csak partíción indítható el.
- Telepítse a Microsoft.ServiceFabric.Powershell.Http modult [előzetes verzióban] konfigurációs hívások kezdeményezéséhez.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- A Microsoft.ServiceFabric.Powershell.Http module használatával ellenőrizze, hogy a fürt csatlakoztatva van-e a `Connect-SFCluster` paranccsal.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggered-restore"></a>Aktivált visszaállítás

A visszaállítás a következő esetekben indítható el:

- Adat-visszaállítás a _vészhelyreállításhoz._
- Adat-visszaállítás _adatsérülés/adatvesztés esetén._

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Adatok visszaállítása vészhelyreállítás esetén

Ha egy teljes Service Fabric-fürt elvész, helyreállíthatja a megbízható állapotalapú szolgáltatás és a megbízható szereplők partícióinak adatait. A kívánt biztonsági másolat akkor választható ki a listából, ha a [GetBackupAPI-t biztonsági mentési tárolórészletekkel](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation)használja. A biztonsági mentés enumerálása lehet egy alkalmazás, szolgáltatás vagy partíció.

A következő példában tegyük fel, hogy az elveszett fürt ugyanaz a fürt, amely a Megbízható állapotalapú szolgáltatás és a [Megbízható szereplők időszakos biztonsági mentésének engedélyezése](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)című részben található. Ebben az `SampleApp` esetben a biztonsági mentési szabályzat engedélyezve van, és a biztonsági mentések az Azure Storage-ra vannak konfigurálva.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell a Microsoft.ServiceFabric.Powershell.Http modul használatával

```powershell
Get-SFBackupsFromBackupLocation -Application -ApplicationName 'fabric:/SampleApp' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Rest Call a Powershell használatával

Hajtson végre egy PowerShell-parancsfájlt a REST API használatához az `SampleApp` alkalmazáson belüli összes partícióhoz létrehozott biztonsági mentések listájának visszaadásához. Az API-nak szüksége van a biztonsági mentési tárolási adatokra a rendelkezésre álló biztonsági mentések listázásához.

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

Mintakimenet a fenti futtatáshoz:

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

A visszaállítás elindításához válasszon egyet a biztonsági mentések közül. Például a vészhelyreállítás jelenlegi biztonsági mentése a következő biztonsági mentés lehet:

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

A visszaállítási API-t meg kell adnia a _BackupId_ és _a BackupLocation_ részleteket.

Ki kell választania egy célpartíciót is az alternatív fürtben a [partíciósémában](service-fabric-concepts-partitioning.md#get-started-with-partitioning)részletezett módon. Az alternatív fürt biztonsági mentése visszaáll az eredeti elveszett fürt partíciósémában megadott partícióra.

Ha az alternatív fürt partícióazonosítója a `1c42c47f-439e-4e09-98b9-88b8f60800c6`, leképezheti az `974bd92a-b395-4631-8a7f-53bd4ae9cf22` eredeti fürtpartíció-azonosítóra a _Ranged Partitioning (UniformInt64Partition)_ magas és alacsony kulcsának összehasonlításával.

Named _Partitioning_esetén a névérték összehasonlítása az alternatív fürt ben lévő célpartíció azonosításához lesz összehasonlítva.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell a Microsoft.ServiceFabric.Powershell.Http modul használatával

```powershell

Restore-SFPartition  -PartitionId '1c42c47f-439e-4e09-98b9-88b8f60800c6' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Rest Call a Powershell használatával

A visszaállítást a biztonsági másolat fürtpartícióján keresztül kéri a következő [Visszaállítási API használatával:](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)

```powershell

$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
    BackupStorage  = $StorageInfo
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

A TrackRestoreProgress segítségével nyomon követheti a visszaállítás előrehaladását.

### <a name="using-service-fabric-explorer"></a>A Service Fabric Intéző használata
A Service Fabric Explorer ből is elindíthategy visszaállítást. Győződjön meg arról, hogy a Speciális mód engedélyezve van a Service Fabric-kezelő beállításaiban.
1. Válassza ki a kívánt partíciót, és kattintson a műveletek. 
2. Válassza a Partíció-visszaállítás triggerének kiválasztását, és töltse ki az Azure adatait:

    ![Partíció visszaállításának aktiválása][2]

    vagy FileShare:

    ![Partíció-visszaállítás fájlmegosztásának aktiválása][3]

### <a name="data-restore-for-_data-corruption__data-loss_"></a>Adat-visszaállítás _adatsérülés_/_adatvesztés_

_Adatvesztés_ vagy _adatsérülés esetén_a megbízható állapotalapú szolgáltatás és a Reliable Actors partíciók biztonsági mentési partíciók visszaállíthatók a kiválasztott biztonsági mentések bármelyikére.

A következő példa a [reliable stateful szolgáltatás és a megbízható szereplők időszakos biztonsági mentésének engedélyezése folytatása.](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors) Ebben a példában egy biztonsági mentési szabályzat engedélyezve van a partíció, és a szolgáltatás biztonsági mentések a kívánt gyakorisággal az Azure Storage-ban.

Válasszon biztonsági másolatot a [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups)kimenetéből. Ebben az esetben a biztonsági mentés ugyanabból a fürtből jön létre, mint korábban.

A visszaállítás elindításához válasszon biztonsági másolatot a listából. Az aktuális _adatvesztési_/_adatsérülés_esetén válassza a következő biztonsági másolatot:

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

A visszaállítási API-t adja meg a _BackupId_ és _backuplocation_ részleteket. A fürt biztonsági mentése engedélyezve van, így a Service Fabric _biztonsági mentési visszaállítási szolgáltatás (BRS)_ azonosítja a megfelelő tárolási helyet a társított biztonsági mentési házirendből.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell a Microsoft.ServiceFabric.Powershell.Http modul használatával

```powershell
Restore-SFPartition  -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'

```

#### <a name="rest-call-using-powershell"></a>Rest Call a Powershell használatával

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

A Visszaállítás folyamata a TrackRestoreProgress segítségével követhető nyomon.

## <a name="track-restore-progress"></a>A visszaállítás folyamatának nyomon követése

Egy megbízható állapotalapú szolgáltatás vagy megbízható aktor partíciója egyszerre csak egy visszaállítási kérelmet fogad el. A partíció csak az aktuális visszaállítási kérelem befejezése után fogad el egy másik kérelmet. Egyszerre több visszaállítási kérelem is aktiválható különböző partíciókon.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell a Microsoft.ServiceFabric.Powershell.Http modul használatával

```powershell
    Get-SFPartitionRestoreProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'
```

#### <a name="rest-call-using-powershell"></a>Rest Call a Powershell használatával

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

A visszaállítási kérelem a következő sorrendben halad előre:

1. **Elfogadva:** _Az elfogadott_ visszaállítási állapot azt jelzi, hogy a kért partíció megfelelő kérésparaméterekkel lett aktiválva.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **InProgress**: _Az InProgress-visszaállítási_ állapot azt jelzi, hogy visszaállítás történik a partíción a kérelemben említett biztonsági másolattal. A partíció jelenti a _dataloss_ állapotát.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Sikeres,** **sikertelen**vagy **időbeli megmaradás:** A kért visszaállítás a következő állapotok bármelyikében elvégezhető. Minden állam nak a következő jelentősége és válaszrészletei vannak:
    - **Sikeres**: _A sikeres_ visszaállítási állapot a visszanyert partícióállapotot jelzi. A partíció jelentések _RestoredEpoch_ és _RestoredLSN_ államok utc-ben.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Hiba**: A _hiba_ visszaállítási állapota a visszaállítási kérelem sikertelenségét jelzi. A hiba okát a jelentés jelenti.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Időtúl- és**időtúltöltés: _Az időtúltöltés-visszaállítási_ állapot azt jelzi, hogy a kérelemnek időtúltöltése van. Hozzon létre egy új visszaállítási kérelmet nagyobb [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). Az alapértelmezett időhosszabbítás 10 perc. Győződjön meg arról, hogy a partíció nem adatvesztési állapotban van, mielőtt újra visszaszeretné állítani.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Automatikus visszaállítás

Konfigurálhatja a Megbízható állapotalapú szolgáltatás és a Reliable Actors partíciókat a Service Fabric-fürtben _az automatikus visszaállításhoz._ A biztonsági mentési `AutoRestore` _házirendben true_értékre van állítva. Az _automatikus visszaállítás_ automatikus engedélyezése automatikusan visszaállítja az adatokat a partíció legutóbbi biztonsági mentéséből, amikor adatvesztést jelentenek. További információkért lásd:

- [Automatikus visszaállítás engedélyezése a biztonsági mentési házirendben](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [RestorePartition API-referencia](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
- [GetPartitionRestoreProgress API-referencia](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>További lépések
- [Rendszeres biztonsági mentési konfiguráció ismertetése](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Rest API-kézikönyv biztonsági mentésének visszaállítása](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[2]: ./media/service-fabric-backuprestoreservice/restore-partition-backup.png
[3]: ./media/service-fabric-backuprestoreservice/restore-partition-fileshare.png