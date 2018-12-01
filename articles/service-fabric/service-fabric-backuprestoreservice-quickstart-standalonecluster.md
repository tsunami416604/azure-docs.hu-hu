---
title: Rendszeres biztonsági mentése és visszaállítása az Azure Service Fabricben |} A Microsoft Docs
description: A Service Fabric rendszeres biztonsági mentését, és a szolgáltatás engedélyezéséhez az alkalmazásadatok periodikus adatok biztonsági másolatának visszaállítása.
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
ms.date: 10/29/2018
ms.author: hrushib
ms.openlocfilehash: 2ff7221a3742f59cdef2c5c7c220cc80148b94d0
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721561"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric"></a>Rendszeres biztonsági mentése és visszaállítása az Azure Service Fabricben
> [!div class="op_single_selector"]
> * [Fürtök az Azure-ban](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Önálló fürtök](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric egy elosztott rendszerplatform, amellyel egyszerűen hozhat létre és kezelhet, megbízható, elosztott, mikroszolgáltatás-alapú felhőalkalmazások a. Lehetővé teszi a is állapot nélküli és állapotalapú mikroszolgáltatásokhoz futtatását. Állapotalapú szolgáltatások megtarthatja a kérés és válasz vagy a teljes tranzakció túl változtatható, mérvadó állapotot. Ha az állapotalapú szolgáltatások hosszú ideig leáll, vagy elveszíti az információk katasztrófa miatt, azt kell ahhoz, hogy továbbra is a szolgáltatást biztosító követően betölt vissza néhány nemrég készült biztonsági másolatából állapotában visszaállíthatók.

A Service Fabric állapota győződjön meg arról, hogy a szolgáltatás magas rendelkezésre állású, több csomóponton replikálja. Akkor is, ha a fürtben egy csomópont meghibásodik, a szolgáltatás továbbra is elérhető. Bizonyos esetekben azonban kívánatos továbbra is a szolgáltatási adatok megbízható szélesebb körű meghibásodásokkal szemben.
 
Ha például egy szolgáltatás érdemes az adatok biztonsági mentését a következő esetekben a védelme érdekében:
- Egy egész Service Fabric-fürt végleges adatvesztést.
- A szolgáltatás partíció replikák többségi végleges adatvesztést
- Felügyeleti hibák, amellyel az állapot véletlenül lekérdezi törölték vagy sérült állapotba kerül. Például egy megfelelő jogosultsággal rendelkező rendszergazda hibásan törli a szolgáltatást.
- Hibák a szolgáltatásban, amely adatsérülést okozhat. Ez például akkor fordulhat elő, a kód frissítése az írás a hibás egy megbízható gyűjteményben indításakor. Ebben az esetben a kódot és az adatok is előfordulhat alakítható vissza egy korábbi állapotba.
- Offline adatok feldolgozása. Érdemes lehet kényelmes szeretné, hogy a kapcsolat nélküli adatfeldolgozást az üzleti intelligenciát, amely a szolgáltatás, amely létrehozza az adatok külön-külön történik.

A Service Fabric biztosít a beépített API-t időponthoz kötött [biztonsági mentését és visszaállítását](service-fabric-reliable-services-backup-restore.md). Az alkalmazásfejlesztők ezen API-k használatával lehet, hogy rendszeresen készítsen biztonsági másolatot a szolgáltatás állapotát. Emellett ha szolgáltatás-rendszergazdák szeretne biztonsági mentést a kívül a szolgáltatás aktiválása egy adott időpontban, például az alkalmazás frissítése előtt fejlesztők kell a szolgáltatás API-ként tegye elérhetővé a biztonsági mentés (és visszaállítása). A fenti további költségek fenntartása a biztonsági mentések. Például előfordulhat, hogy szeretné öt növekményes biztonsági mentések minden fél óra egy teljes biztonsági mentés követ. A teljes biztonsági mentést követően törölheti a korábbi növekményes biztonsági mentések. Ez a megközelítés szükséges alkalmazások fejlesztése során további költségek vezető további programkódokat kellene megtervezni.

Az alkalmazásadatok rendszeres biztonsági mentése egy elosztott alkalmazás kezelése és az adatvesztés és szolgáltatás rendelkezésre állása hosszan tartó adatvesztés elleni esetlegesen korán alapszintű szüksége. A Service Fabric biztosít egy nem kötelező biztonsági mentés és visszaállítás szolgáltatást, amely lehetővé teszi, hogy rendszeres biztonsági mentés (beleértve a Aktorszolgáltatások), állapotalapú Reliable Services konfigurálása további kód írása nélkül. Azt is elősegíti a korábban végrehajtott biztonsági mentések visszaállítása. 

A Service Fabric egy érhet el a következő funkciók kapcsolatos rendszeres biztonsági mentés és visszaállítás szolgáltatás API-készletet biztosít:

- Rendszeres biztonsági mentés ütemezése a Reliable Stateful services és Reliable Actors támogatásával (külső) történő biztonsági mentés tárolóhelyek feltölteni. Támogatott tárolási helyek
    - Azure Storage
    - Fájlmegosztás (helyszíni)
- Biztonsági másolatok számbavétele
- Indítson egy ad hoc biztonsági mentést egy partíció
- Visszaállítás egy korábbi biztonsági mentéssel partíció
- Átmenetileg felfüggeszti a biztonsági mentések
- A biztonsági mentések (későbbi) megőrzési kezelése

## <a name="prerequisites"></a>Előfeltételek
* Service Fabric-fürtön a Fabric verziója 6.2 vagy újabb. A Windows Server létre kell hozni a fürtöt. Ebben [cikk](service-fabric-cluster-creation-for-windows-server.md) lépésekhez szükséges csomag letöltéséhez.
* X.509-tanúsítvány a titkos kulcsok tárolási való csatlakozáshoz szükséges titkosítási biztonsági másolatok tárolására. Tekintse meg [cikk](service-fabric-windows-cluster-x509-security.md) tudnia kell, hogyan beszerezni, vagy hozzon létre egy önaláírt X.509-tanúsítványt.
* Service Fabric Reliable Stateful alkalmazás használatával a Service Fabric SDK 3.0-s verzió vagy újabb. Alkalmazások .net Core 2.0, az alkalmazás kell kialakítani, Service Fabric SDK verziója 3.1-es vagy újabb.

## <a name="enabling-backup-and-restore-service"></a>Biztonsági mentés és visszaállítás szolgáltatás engedélyezése
Először engedélyeznie kell a _biztonsági mentés és visszaállítás szolgáltatás_ a fürtben. Szerezze be a sablon a fürt, amely számára telepíteni kívánja. Használhatja a [mintasablon](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Engedélyezze a _biztonsági mentés és visszaállítás szolgáltatás_ az alábbi lépéseket követve:

1. Ellenőrizze, hogy a `apiversion` értékre van állítva `10-2017` a fürt konfigurációját a fájlt, és ha nem, frissítse azt az alábbi kódrészletben látható módon:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Most már engedélyezheti a _biztonsági mentés és visszaállítás szolgáltatás_ adja hozzá a következő `addonFeatures` szakaszba `properties` szakasz az alábbi kódrészletben látható módon: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. X.509 tanúsítvány konfigurálása a hitelesítő adatok titkosításához. Ez fontos annak érdekében, hogy a hitelesítő adatokat, feltéve, ha bármelyik tárhelyhez csatlakozhat vannak titkosítva, mielőtt megőrzése. Titkosítási tanúsítvány konfigurálása a következő `BackupRestoreService` szakaszba `fabricSettings` szakasz az alábbi kódrészletben látható módon: 

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

4. Miután frissítette a fürt konfigurációs fájlban a fenti módosítások, alkalmazhatja őket és lehetővé teszik a központi telepítés vagy frissítés befejezéséhez. A befejezést követően a _biztonsági mentés és visszaállítás szolgáltatás_ elindítja a fürtön belül. A szolgáltatás az Uri-ja `fabric:/System/BackupRestoreService` és a szolgáltatás is található a Service Fabric Explorert a szakaszban a rendszer szolgáltatás. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>A Reliable Stateful service és a Reliable Actors rendszeres biztonsági mentés engedélyezése
Nézzük meg a megbízható állapotalapú szolgáltatás és a Reliable Actors rendszeres biztonsági mentés engedélyezésének lépései. Ezek a lépések feltételezik
- Hogy a fürt be van állítva az _biztonsági mentés és visszaállítás szolgáltatás_.
- Egy megbízható állapotalapú szolgáltatás üzembe helyezése a fürtön. A rövid útmutató céljából alkalmazás URI azonosítója van `fabric:/SampleApp` és az ehhez az alkalmazáshoz tartozó megbízható állapotalapú szolgáltatás Uri-ja `fabric:/SampleApp/MyStatefulService`. Ez a szolgáltatás üzembe helyezése egyetlen partícióval, és a Partícióazonosító `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`.  

### <a name="create-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

Ismertető a biztonsági mentési ütemezés biztonsági mentési szabályzat létrehozása, a biztonsági mentési adatokat, a szabályzat nevét, a lehetővé kell tenni, mielőtt elindítaná a teljes biztonsági mentési és adatmegőrzési biztonságimásolat-tároláshoz a növekményes biztonsági mentések maximális adatméretét első lépéseként. 

Biztonságimásolat-tároláshoz hozzon létre fájlmegosztást, és az olvasási és írási hozzáférést biztosít a fájlmegosztás az összes Service Fabric-csomópont gépek. Ez a példa feltételezi, hogy a megosztás neve `BackupStore` -e a `StorageServer`.

Hajtsa végre a következő PowerShell-parancsfájl az új szabályzat létrehozásához szükséges REST API meghívása.

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
}

$RetentionPolicy = @{ 
    RetentionPolicyType = 'Basic'
    RetentionDuration =  'P10D'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
    RetentionPolicy = $RetentionPolicy
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

### <a name="enable-periodic-backup"></a>Rendszeres biztonsági mentés engedélyezése
Után az alkalmazás adatvédelmi követelmények teljesítéséhez házirend meghatározása, a biztonsági mentési szabályzat az alkalmazás társítva kell lennie. Követelmény, függően a biztonsági mentési szabályzathoz társított egy alkalmazást, a szolgáltatás és a partíció is lehet.

Hajtsa végre a következő PowerShell-szkript meghívása szükséges REST API-t a biztonsági mentési házirend társítása neve `BackupPolicy1` fenti alkalmazással létrehozott `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Rendszeres biztonsági mentések működésének ellenőrzése

Miután engedélyezte az alkalmazás biztonsági mentése, az alkalmazás a Reliable Stateful services és Reliable Actors tartozó összes partíció indul el első készül rendszeres időközönként a társított biztonsági mentési szabályzatának megfelelően. 

![Partíció BackedUp Állapotesemény][0]

### <a name="list-backups"></a>Lista biztonsági mentések

A Reliable Stateful services és Reliable Actors-alkalmazás tartozó összes partíció társított biztonsági másolatok használatával lehet enumerálni _GetBackups_ API-t. Követelmény, attól függően a biztonsági másolatok is enumerálni az alkalmazás, szolgáltatás vagy egy partíciót.

Hajtsa végre a következő PowerShell-parancsprogram enumerálni az összes partíción belül létrehozott biztonsági mentéseket a HTTP API meghívása a `SampleApp` alkalmazás.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
Futtassa a fenti kimeneti példa:

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

## <a name="limitation-caveats"></a>Korlátozás / kikötések
- Nem Service Fabric beépített PowerShell-parancsmagokat.
- Service Fabric parancssori felület nem támogatott.
- Nem támogatja a Service Fabric-fürtök Linux rendszeren.

## <a name="next-steps"></a>További lépések
- [Rendszeres biztonsági mentési konfiguráció ismertetése](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Biztonsági másolat visszaállítása – REST API-referencia](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent.png

