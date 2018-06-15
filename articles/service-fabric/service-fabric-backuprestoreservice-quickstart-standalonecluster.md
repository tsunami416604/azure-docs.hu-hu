---
title: Rendszeres biztonsági mentése és visszaállítása az Azure Service Fabric (előzetes verzió) |} Microsoft Docs
description: A Service Fabric rendszeres biztonsági mentése és visszaállítása a szolgáltatást ahhoz, hogy az alkalmazások adatvesztés elleni védelme.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAADBCAB-F0CF-4CBC-B663-4A6DCCB4DEE1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2018
ms.author: hrushib
ms.openlocfilehash: cfbc0e6ca255bd005bb6e4cc381a9121347fe227
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206045"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric-preview"></a>Rendszeres biztonsági mentése és visszaállítása az Azure Service Fabric (előzetes verzió)
> [!div class="op_single_selector"]
> * [Azure-fürtök](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Önálló fürtök](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

A Service Fabric egy elosztottrendszer-platform, amely megkönnyíti a egyszerűen fejleszthető és megbízható, elosztott, mikroszolgáltatások alapú felhőalapú alkalmazások kezelése. Lehetővé teszi mind az állapotmentes és állapotalapú micro szolgáltatások futtatásához. Állapotalapú szolgáltatások fenntarthatja a kérelem és válasz, vagy egy teljes tranzakció túl változtatható, mérvadó állapotát. Ha egy állapotalapú szolgáltatás hosszú ideig nem működik, vagy elveszíti az információk katasztrófa miatt, kell visszaállítani, néhány nemrég készült biztonsági másolatából állapotában szolgáltatást biztosító az ismét biztonsági után a folytatáshoz.

A Service Fabric állapotát annak biztosításához, hogy a szolgáltatás magas rendelkezésre állású több csomópont között replikálja. Akkor is, ha a fürtben egy csomópont meghibásodik, a szolgáltatás továbbra is elérhető. Bizonyos esetekben azonban kívánatos továbbra is a szolgáltatás adatok megbízható szélesebb körű-hibákkal szemben.
 
Szolgáltatás például érdemes biztonsági másolatot a ahhoz, hogy megvédje a következő esetekben:
- A teljes Service Fabric-fürt állandó adatvesztés.
- A szolgáltatás partíció replikák többsége végleges adatvesztést
- Felügyeleti hibák, amelyek állapotát véletlenül lekérdezi törölt vagy sérült. Egy megfelelő jogosultsággal rendelkező rendszergazda például tévesen törli a szolgáltatást.
- Hibák az szolgáltatásban adatvesztést okozhat. Ez például akkor fordulhat elő, a kód frissítése az írás a hibás egy megbízható gyűjtemény indításakor. Ebben az esetben a kód és az adatok is lehet, hogy egy korábbi állapotára visszaállítani.
- Kapcsolat nélküli adatok feldolgozása. Előfordulhat, hogy az adatok az üzleti intelligencia, külön-külön történik, a szolgáltatás, amely az adatokat hoz létre a kapcsolat nélküli feldolgozása kényelmes.

A Service Fabric biztosít a beépített API időpontra történő [biztonsági mentése és visszaállítása](service-fabric-reliable-services-backup-restore.md). Alkalmazásfejlesztők ezen API-k használatával lehet, hogy rendszeresen készítsen biztonsági másolatot a szolgáltatás állapotát. Emellett ha a szolgáltatás-rendszergazdák aktiválják a biztonsági mentés, a szolgáltatás kívül egy adott időpontban, például az alkalmazást, a frissítés előtt fejlesztők kell teszi közzé a biztonsági mentés (és visszaállítása), az API-k a szolgáltatásból. A biztonsági mentések karbantartása újabb ez további költségek. Érdemes lehet például 5 növekményes biztonsági mentések érvénybe félig óránként egy teljes biztonsági mentés követ. A teljes biztonsági mentés után törölheti a korábbi növekményes biztonsági mentést. Ez a módszer alkalmazásfejlesztés során további költség nélkül vezető kód igényli.

A rendszeres időközönként alkalmazásadatok biztonsági mentése egy elosztott alkalmazás kezelése és adatvesztés vagy a szolgáltatás rendelkezésre állásának elvesztése üzemszünetei ellen esetlegesen korán alapvető szükség. A Service Fabric biztosít egy nem kötelező biztonsági mentési és visszaállítási szolgáltatást, amely lehetővé teszi (többek között a Aktorszolgáltatások) állapotalapú Reliable Services rendszeres biztonsági mentését konfigurálását további kód írása nélkül. Azt is elősegíti a korábban végrehajtott biztonsági másolatok visszaállítása. 

> [!NOTE]
> Rendszeres biztonsági mentési és visszaállítási funkció jelenleg **előzetes** és a termelési számítási feladatokhoz nem támogatott. 
>

A Service Fabric API-k érhetők el a következő funkciók rendszeres kapcsolódó biztonsági mentés és visszaállítás szolgáltatás biztosít:

- Rendszeres biztonsági másolatot készíteni megbízható állapotalapú alkalmazások és szolgáltatások services és Reliable Actors-támogatással rendelkező töltse fel a biztonsági mentés (külső) tárolóhelyek ütemezni. Támogatott tárolóhelyek
    - Azure Storage
    - A fájlmegosztás (helyi)
- Biztonsági másolatok felsorolása
- Ad hoc biztonsági mentés partíció
- Visszaállítás egy korábbi biztonsági mentésből partíció
- Ideiglenesen felfüggesztheti a biztonsági másolatok
- A biztonsági mentések (jövőbeli) megőrzési kezelése

## <a name="prerequisites"></a>Előfeltételek
* Service Fabric-fürt hálóval verzió 6.2 vagy újabb. A fürt beállítása a Windows Server kell lennie. Tekintse meg a [cikk](service-fabric-cluster-creation-for-windows-server.md) szükséges csomag lépéseit.
* X.509 tanúsítvány a titkos kulcsok tárolási való csatlakozáshoz szükséges titkosítási biztonsági másolatok tárolására. Tekintse meg a [cikk](service-fabric-windows-cluster-x509-security.md) tudnia kell, hogyan szerezni, vagy egy önaláírt X.509-tanúsítvány létrehozásához.
* Service Fabric megbízható állapotfüggő alkalmazás használatával a Service Fabric SDK 3.0-s verziójával készített vagy újabb. A célcsoport-kezelési .net alkalmazások alapvető 2.0, alkalmazás kell kialakítani, Service Fabric SDK 3.1-es verzióját vagy újabb.

## <a name="enabling-backup-and-restore-service"></a>Biztonsági mentés és visszaállítás szolgáltatás engedélyezése
Először engedélyeznie kell a _biztonsági mentése és visszaállítása szolgáltatás_ a fürtön. Szerezze be a sablon a fürt, amely számára telepíteni kívánja. Használhatja a [mintasablon használatával](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Engedélyezze a _biztonsági mentése és visszaállítása szolgáltatás_ a következő lépéseket:

1. Ellenőrizze, hogy a `apiversion` értéke `10-2017` a fürt konfigurációjába fájlt, és ha nem, frissítheti a következő kódrészletben látható:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Most már engedélyezheti a _biztonsági mentése és visszaállítása szolgáltatás_ a következő `addonFeatures` szakaszában `properties` szakaszban, ahogy az a következő kódrészletet: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Konfigurálja az X.509 tanúsítvány a hitelesítő adatok titkosításához. Ez fontos annak érdekében, hogy a hitelesítő adatokat ad meg, ha bármelyik csatlakozhat titkosított előtt megőrzése. Titkosítási tanúsítvány konfigurálása a következő `BackupRestoreService` szakaszában `fabricSettings` szakaszban, ahogy az a következő kódrészletet: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. Miután a fenti változtatásokat a fürt konfigurációs fájl frissítése befejeződött, alkalmazza őket, és lehetővé teszik a központi telepítés/frissítés befejezéséhez. A befejezést követően a _biztonsági mentése és visszaállítása szolgáltatás_ elindul a fürtön. A megadott URI azonosító, a szolgáltatás `fabric:/System/BackupRestoreService` és a szolgáltatás a Service Fabric explorer rendszer szolgáltatás szakaszban található. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>A megbízható állapotalapú alkalmazások és szolgáltatások szolgáltatás és a Reliable Actors rendszeres biztonsági mentés engedélyezése
Bemutatjuk, keresztül történő engedélyezéséről a megbízható állapotalapú alkalmazások és szolgáltatások szolgáltatás és a Reliable Actors rendszeres biztonsági mentést. Ezek a lépések feltételezik
- Hogy a fürt működik a _biztonsági mentése és visszaállítása szolgáltatás_.
- A fürt egy megbízható állapotalapú alkalmazások és szolgáltatások szolgáltatás van telepítve. A gyors üzembe helyezési útmutató céljából alkalmazás Uri van `fabric:/SampleApp` , és az alkalmazáshoz tartozó megbízható állapotalapú alkalmazások és szolgáltatások szolgáltatás URI-jának `fabric:/SampleApp/MyStatefulService`. Ez a szolgáltatás telepítve van az egypartíciós, és a Partícióazonosító van `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`.  

### <a name="create-backup-policy"></a>A biztonsági mentési házirend létrehozása

Első lépés, ha a biztonsági mentési házirend ütemezett biztonsági mentés, a célként megadott biztonsági mentési adatokat, a házirend nevét és a maximális növekményes biztonsági mentések teljes biztonsági mentés elindítása előtt engedélyezett leíró. 

Biztonsági másolatok tárolására létrehozni a fájlmegosztást, és hozzáférést ReadWrite ezt a fájlmegosztást a Service Fabric-csomópont az összes számítógépen. Ez a példa azt feltételezi, hogy a megosztás neve `BackupStore` -e a `StorageServer`.

Hajtsa végre a következő PowerShell-parancsfájl az új házirend létrehozásához szükséges REST API meghívása.

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

### <a name="enable-periodic-backup"></a>Rendszeres biztonsági mentés engedélyezése
Meghatározza az alkalmazás adatvédelmi követelményeinek teljesítéséhez házirendet, miután a biztonsági mentési házirendet az alkalmazással társított kell lennie. Követelmény, attól függően, hogy a biztonsági mentési házirend társítva van egy alkalmazás, szolgáltatás vagy egy partíció lehet.

Hajtsa végre a következő PowerShell-parancsfájl az rendelje hozzá a biztonsági mentési házirend nevű szükséges REST API meghívása `BackupPolicy1` a fenti lépés alkalmazással létrehozott `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Ellenőrizze, hogy működik-e a rendszeres biztonsági mentést

Miután engedélyezte a biztonsági mentés az alkalmazáshoz, az alkalmazás a megbízható állapotalapú alkalmazások és szolgáltatások services és Reliable Actors tartozó összes partíció első biztonsági másolat rendszeres időközönként a tartozó biztonsági mentési házirend szerint elindul. 

![Partíció BackedUp Állapotesemény][0]

### <a name="list-backups"></a>Lista biztonsági mentések

Megbízható állapotalapú alkalmazások és szolgáltatások services és Reliable Actors az alkalmazás tartozó összes partíció társított biztonsági mentések használatával vehető számba _GetBackups_ API. Követelmény, attól függően, hogy a biztonsági mentések alkalmazás, szolgáltatás vagy egy partíció számba.

Hajtsa végre a következő PowerShell parancsfájlt a HTTP API számba venni az összes partíció belül létrehozott biztonsági mentéseket a `SampleApp` alkalmazás.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.2-preview"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
Futtassa a fenti példa kimenete:

```
BackupId                : d7e4038e-2c46-47c6-9549-10698766e714
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.39.40.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2058
CreationTimeUtc         : 2018-04-01T19:39:40Z
FailureError            : 

BackupId                : 8c21398a-2141-4133-b4d7-e1a35f0d7aac
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.54.38.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2237
CreationTimeUtc         : 2018-04-01T19:54:38Z
FailureError            : 

BackupId                : fc75bd4c-798c-4c9a-beee-e725321f73b2
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 20.09.44.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2437
CreationTimeUtc         : 2018-04-01T20:09:44Z
FailureError            : 
```

## <a name="preview-limitation-caveats"></a>Tekintse meg a korlátozás / figyelmeztetések
- Nem a Service Fabric PowerShell-parancsmagok beépített.
- Service Fabric CLI nem támogatott.
- Az automatikus biztonsági mentési végleges törlése nem támogatott. Kézi megtisztítsa azokat a biztonsági mentések igényel.
- Nem támogatja a Service Fabric-fürtök Linux rendszeren.

## <a name="next-steps"></a>További lépések
- [Biztonságimásolat-visszaállítással REST API-referencia](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent.png

