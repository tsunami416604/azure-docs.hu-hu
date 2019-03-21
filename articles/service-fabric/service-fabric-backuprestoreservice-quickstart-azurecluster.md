---
title: Rendszeres biztonsági mentése és visszaállítása az Azure Service Fabricben |} A Microsoft Docs
description: A Service Fabric rendszeres biztonsági mentését, és a szolgáltatás engedélyezéséhez az alkalmazásadatok periodikus adatok biztonsági másolatának visszaállítása.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAA58600-897E-4CEE-9D1C-93FACF98AD1C
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: hrushib
ms.openlocfilehash: 4cb73ea08060336710207e026bb5bcc5a251efc8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877921"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric"></a>Rendszeres biztonsági mentése és visszaállítása az Azure Service Fabricben 
> [!div class="op_single_selector"]
> * [Fürtök az Azure-ban](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Önálló fürtök](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric egy elosztott rendszerplatform, amellyel egyszerűen hozhat létre és kezelhet, megbízható, elosztott, mikroszolgáltatás-alapú felhőalkalmazások a. Lehetővé teszi a is állapot nélküli és állapotalapú mikroszolgáltatásokhoz futtatását. Állapotalapú szolgáltatások megtarthatja a kérés és válasz vagy a teljes tranzakció túl változtatható, mérvadó állapotot. Ha az állapotalapú szolgáltatások hosszú ideig leáll, vagy elveszíti az információk katasztrófa miatt, azt kell ahhoz, hogy továbbra is a szolgáltatást biztosító követően betölt vissza néhány nemrég készült biztonsági másolatából állapotában visszaállíthatók.

A Service Fabric állapota győződjön meg arról, hogy a szolgáltatás magas rendelkezésre állású, több csomóponton replikálja. Akkor is, ha a fürtben egy csomópont meghibásodik, a szolgáltatás továbbra is elérhető. Bizonyos esetekben azonban kívánatos továbbra is a szolgáltatási adatok megbízható szélesebb körű meghibásodásokkal szemben.
 
Ha például szolgáltatást érdemes az adatok biztonsági mentését a következő esetekben a védelme érdekében:
- Ha egy egész Service Fabric-fürt állandó elvesztését.
- A szolgáltatás partíció replikák többségi végleges adatvesztést
- Felügyeleti hibák, amellyel az állapot véletlenül lekérdezi törölték vagy sérült állapotba kerül. Például egy megfelelő jogosultsággal rendelkező rendszergazda hibásan törli a szolgáltatást.
- Hibák a szolgáltatásban, amely adatsérülést okozhat. Ez például akkor fordulhat elő, a kód frissítése az írás a hibás egy megbízható gyűjteményben indításakor. Ebben az esetben a kódot és az adatok is előfordulhat alakítható vissza egy korábbi állapotba.
- Offline adatok feldolgozása. Érdemes lehet kényelmes szeretné, hogy a kapcsolat nélküli adatfeldolgozást az üzleti intelligenciát, amely a szolgáltatás, amely létrehozza az adatok külön-külön történik.

A Service Fabric biztosít a beépített API-t időponthoz kötött [biztonsági mentését és visszaállítását](service-fabric-reliable-services-backup-restore.md). Az alkalmazásfejlesztők ezen API-k használatával lehet, hogy rendszeresen készítsen biztonsági másolatot a szolgáltatás állapotát. Emellett ha szolgáltatás-rendszergazdák szeretne biztonsági mentést a kívül a szolgáltatás aktiválása egy adott időpontban, például az alkalmazás frissítése előtt fejlesztők kell a szolgáltatás API-ként tegye elérhetővé a biztonsági mentés (és visszaállítása). A fenti további költségek fenntartása a biztonsági mentések. Például előfordulhat, hogy szeretné öt növekményes biztonsági mentések minden fél óra egy teljes biztonsági mentés követ. A teljes biztonsági mentést követően törölheti a korábbi növekményes biztonsági mentések. Ez a megközelítés szükséges alkalmazások fejlesztése során további költségek vezető további programkódokat kellene megtervezni.

A biztonsági mentés és visszaállítás szolgáltatás a Service Fabric lehetővé teszi az állapotalapú szolgáltatások tárolt adatok könnyen és az automatikus biztonsági mentés. Alkalmazásadatok biztonsági mentésekor rendszeres időközönként alapvető fontosságú adatok szolgáltatáskimaradást és -szolgáltatás rendelkezésre állásának hiánya elleni esetlegesen korán. A Service Fabric biztosít egy nem kötelező biztonsági mentés és visszaállítás szolgáltatást, amely lehetővé teszi, hogy rendszeres biztonsági mentés (beleértve a Aktorszolgáltatások), állapotalapú Reliable Services konfigurálása további kód írása nélkül. Azt is elősegíti a korábban végrehajtott biztonsági mentések visszaállítása. 


A Service Fabric egy érhet el a következő funkciók kapcsolatos rendszeres biztonsági mentés és visszaállítás szolgáltatás API-készletet biztosít:

- Rendszeres biztonsági mentés ütemezése a Reliable Stateful services és Reliable Actors támogatásával (külső) történő biztonsági mentés tárolóhelyek feltölteni. Támogatott tárolási helyek
    - Azure Storage
    - Fájlmegosztás (helyszíni)
- Biztonsági másolatok számbavétele
- Indítson egy ad-hoc biztonsági mentést egy partíció
- Visszaállítás egy korábbi biztonsági mentéssel partíció
- Átmenetileg felfüggeszti a biztonsági mentések
- A biztonsági mentések (későbbi) megőrzési kezelése

## <a name="prerequisites"></a>Előfeltételek
* Service Fabric-fürtön a Fabric verziója 6.2 vagy újabb. A Windows Server létre kell hozni a fürtöt. Ebben [cikk](service-fabric-cluster-creation-via-arm.md) létrehozás a Service Fabric-fürt Azure-erőforrás-sablon használatával.
* X.509-tanúsítvány a titkos kulcsok tárolási való csatlakozáshoz szükséges titkosítási biztonsági másolatok tárolására. Tekintse meg [cikk](service-fabric-cluster-creation-via-arm.md) szeretné elsajátítani, vagy hozzon létre egy X.509 tanúsítvány.
* Service Fabric Reliable Stateful alkalmazás használatával a Service Fabric SDK 3.0-s verzió vagy újabb. Alkalmazások .NET Core 2.0, kérelmet kell kialakítani, Service Fabric SDK verziója 3.1-es vagy újabb.
* Hozzon létre az Azure Storage-fiók alkalmazás biztonsági mentések tárolására.

## <a name="enabling-backup-and-restore-service"></a>Biztonsági mentés és visszaállítás szolgáltatás engedélyezése
Először engedélyeznie kell a _biztonsági mentés és visszaállítás szolgáltatás_ a fürtben. Szerezze be a sablon a fürt, amely számára telepíteni kívánja. Használhatja a [mintasablon](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) , vagy hozzon létre egy Resource Manager-sablon. Engedélyezze a _biztonsági mentés és visszaállítás szolgáltatás_ az alábbi lépéseket követve:

1. Ellenőrizze, hogy a `apiversion` értékre van állítva **`2018-02-01`** a a `Microsoft.ServiceFabric/clusters` erőforrás, és ha nem, frissítse azt az alábbi kódrészletben látható módon:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Most már engedélyezheti a _biztonsági mentés és visszaállítás szolgáltatás_ adja hozzá a következő `addonFeatures` szakaszba `properties` szakasz az alábbi kódrészletben látható módon: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. X.509 tanúsítvány konfigurálása a hitelesítő adatok titkosításához. Ez fontos, hogy a tárhelyhez csatlakozhat a megadott hitelesítő adatok titkosítottak-e előtt megőrzése érdekében. Titkosítási tanúsítvány konfigurálása a következő `BackupRestoreService` szakaszba `fabricSettings` szakasz az alábbi kódrészletben látható módon: 

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

4. Miután a fenti módosítások a fürtsablonhoz frissítése, alkalmazza őket, és lehetővé teszik a központi telepítés vagy frissítés befejezéséhez. A befejezést követően a _biztonsági mentés és visszaállítás szolgáltatás_ elindítja a fürtön belül. A szolgáltatás az Uri-ja `fabric:/System/BackupRestoreService` és a szolgáltatás is található a Service Fabric Explorert a szakaszban a rendszer szolgáltatás. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>A Reliable Stateful service és a Reliable Actors rendszeres biztonsági mentés engedélyezése
Nézzük meg a megbízható állapotalapú szolgáltatás és a Reliable Actors rendszeres biztonsági mentés engedélyezésének lépései. Ezek a lépések feltételezik
- Hogy a fürt be állítva, az X.509-rendszerbiztonság használatával _biztonsági mentés és visszaállítás szolgáltatás_.
- Egy megbízható állapotalapú szolgáltatás üzembe helyezése a fürtön. A rövid útmutató céljából alkalmazás URI azonosítója van `fabric:/SampleApp` és az ehhez az alkalmazáshoz tartozó megbízható állapotalapú szolgáltatás Uri-ja `fabric:/SampleApp/MyStatefulService`. Ez a szolgáltatás üzembe helyezése egyetlen partícióval, és a Partícióazonosító `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.
- A rendszergazdai szerepkörrel rendelkező ügyfél-tanúsítványt telepít a _saját_ (_személyes_) nevét tárolja _CurrentUser_ tanúsítvány a gépen, ahonnan a alább tároló helye parancsprogramok akkor kell meghívni. Ez a példa `1b7ebe2174649c45474a4819dafae956712c31d3` , a tanúsítvány ujjlenyomatát. Az ügyfél-tanúsítványokról további információ: [szerepköralapú hozzáférés-vezérlés a Service Fabric-ügyfelek](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

Ismertető a biztonsági mentési ütemezés biztonsági mentési szabályzat létrehozása, a biztonsági mentési adatokat, a szabályzat nevét, a lehetővé kell tenni, mielőtt elindítaná a teljes biztonsági mentési és adatmegőrzési biztonságimásolat-tároláshoz a növekményes biztonsági mentések maximális adatméretét első lépéseként. 

Biztonsági mentési tár használja az Azure Storage a fent létrehozott fiók. Tároló `backup-container` biztonsági mentések tárolására van beállítva. Egy ilyen nevű tároló jön létre, ha ezt még nem létezik, biztonsági mentési feltöltésekor. Töltse fel `ConnectionString` egy érvényes kapcsolati karakterlánccal, az Azure Storage-fiókok cseréje `account-name` az a tárfiók nevét, és `account-key` az a tárfiók kulcsára.

Hajtsa végre a következő PowerShell-parancsfájl az új szabályzat létrehozásához szükséges REST API meghívása. Cserélje le `account-name` az a tárfiók nevét, és `account-key` az a tárfiók kulcsára.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
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
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

> [!IMPORTANT]
> A futtatókörnyezet hibája miatt győződjön meg arról, hogy a megőrzési időtartamát a megtartási házirend van beállítva 24 napnál kisebb különbségnek kell, különben szolgáltatás biztonsági mentése és helyreállítása kvórum elvesztése replika feladatátvétel után lép eredményez.

### <a name="enable-periodic-backup"></a>Rendszeres biztonsági mentés engedélyezése
Után az alkalmazás adatvédelmi követelmények teljesítéséhez biztonsági mentési házirend meghatározása, a biztonsági mentési szabályzat az alkalmazás társítva kell lennie. Követelmény, függően a biztonsági mentési szabályzathoz társított egy alkalmazást, a szolgáltatás és a partíció is lehet.

Hajtsa végre a következő PowerShell-szkript meghívása szükséges REST API-t a biztonsági mentési házirend társítása neve `BackupPolicy1` fenti alkalmazással létrehozott `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Rendszeres biztonsági mentések működésének ellenőrzése

Miután engedélyezte a biztonsági mentés az alkalmazás szintjén, az alkalmazás a Reliable Stateful services és Reliable Actors tartozó összes partíció indul el első készül rendszeres időközönként a társított biztonsági mentési szabályzatának megfelelően. 

![Partíció BackedUp Állapotesemény][0]

### <a name="list-backups"></a>Lista biztonsági mentések

A Reliable Stateful services és Reliable Actors-alkalmazás tartozó összes partíció társított biztonsági másolatok használatával lehet enumerálni _GetBackups_ API-t. Biztonsági másolatok számba vehetők egy alkalmazás, szolgáltatás vagy egy partíciót.

Hajtsa végre a következő PowerShell-parancsprogram enumerálni az összes partíción belül létrehozott biztonsági mentéseket a HTTP API meghívása a `SampleApp` alkalmazás.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

## <a name="limitation-caveats"></a>Korlátozás / kikötések
- Nem Service Fabric beépített PowerShell-parancsmagokat.
- Nem támogatja a Service Fabric-fürtök Linux rendszeren.

## <a name="known-issues"></a>Ismert problémák
- Győződjön meg arról, hogy a megőrzési időtartam 24 napnál kisebb különbségnek kell van konfigurálva. 

## <a name="next-steps"></a>További lépések
- [Rendszeres biztonsági mentési konfiguráció ismertetése](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Biztonsági másolat visszaállítása – REST API-referencia](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent_Azure.png

