---
title: Rendszeres biztonsági mentése és visszaállítása az Azure Service Fabric (előzetes verzió) |} Microsoft Docs
description: A Service Fabric rendszeres biztonsági mentése és visszaállítása a szolgáltatást ahhoz, hogy az alkalmazások adatvesztés elleni védelme.
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
ms.date: 04/04/2018
ms.author: hrushib
ms.openlocfilehash: 9fbf3a6c965bb2f52b71fbac32b289555b922772
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113989"
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
    - A fájlmegosztás (helyszíni)
- Biztonsági másolatok felsorolása
- Ad hoc biztonsági mentés partíció
- Visszaállítás egy korábbi biztonsági mentésből partíció
- Ideiglenesen felfüggesztheti a biztonsági másolatok
- A biztonsági mentések (jövőbeli) megőrzési kezelése

## <a name="prerequisites"></a>Előfeltételek
* Service Fabric-fürt hálóval verzió 6.2 vagy újabb. A fürt beállítása a Windows Server kell lennie. Tekintse meg a [cikk](service-fabric-cluster-creation-via-arm.md) lépések végrehajtásával hozza létre a Service Fabric fürt Azure erőforráscsoport-sablonnal.
* X.509 tanúsítvány a titkos kulcsok tárolási való csatlakozáshoz szükséges titkosítási biztonsági másolatok tárolására. Tekintse meg a [cikk](service-fabric-cluster-creation-via-arm.md) kívánja beolvasni, vagy hozzon létre egy X.509 tanúsítvány.
* Service Fabric megbízható állapotfüggő alkalmazás használatával a Service Fabric SDK 3.0-s verziójával készített vagy újabb. A célcsoport-kezelési .net alkalmazások alapvető 2.0, alkalmazás kell kialakítani, Service Fabric SDK 3.1-es verzióját vagy újabb.
* Hozzon létre az Azure Storage-fiók alkalmazás biztonsági mentések tárolására.

## <a name="enabling-backup-and-restore-service"></a>Biztonsági mentés és visszaállítás szolgáltatás engedélyezése
Először engedélyeznie kell a _biztonsági mentése és visszaállítása szolgáltatás_ a fürtön. Szerezze be a sablon a fürt, amely számára telepíteni kívánja. Használhatja a [mintasablon használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) vagy Resource Manager-sablon létrehozása. Engedélyezze a _biztonsági mentése és visszaállítása szolgáltatás_ a következő lépéseket:

1. Ellenőrizze, hogy a `apiversion` értékre van állítva **`2018-02-01`** a a `Microsoft.ServiceFabric/clusters` erőforrás, és ha nem, frissítheti a következő kódrészletben látható:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Most már engedélyezheti a _biztonsági mentése és visszaállítása szolgáltatás_ a következő `addonFeatures` szakaszában `properties` szakaszban, ahogy az a következő kódrészletet: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Konfigurálja az X.509 tanúsítvány a hitelesítő adatok titkosításához. Ez fontos győződjön meg arról, hogy a tároló való kapcsolódáshoz megadott hitelesítő adatok megőrzése előtt titkosítva legyenek. Titkosítási tanúsítvány konfigurálása a következő `BackupRestoreService` szakaszában `fabricSettings` szakaszban, ahogy az a következő kódrészletet: 

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

4. Miután frissítette a fürt sablon előző módosításainak, alkalmazza őket és lehetővé teszik a központi telepítés/frissítés befejezéséhez. A befejezést követően a _biztonsági mentése és visszaállítása szolgáltatás_ elindul a fürtön. A megadott URI azonosító, a szolgáltatás `fabric:/System/BackupRestoreService` és a szolgáltatás a Service Fabric explorer rendszer szolgáltatás szakaszban található. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>A megbízható állapotalapú alkalmazások és szolgáltatások szolgáltatás és a Reliable Actors rendszeres biztonsági mentés engedélyezése
Bemutatjuk, keresztül történő engedélyezéséről a megbízható állapotalapú alkalmazások és szolgáltatások szolgáltatás és a Reliable Actors rendszeres biztonsági mentést. Ezek a lépések feltételezik
- Hogy a fürt működik X.509 biztonsági funkcióinak használatával _biztonsági mentése és visszaállítása szolgáltatás_.
- A fürt egy megbízható állapotalapú alkalmazások és szolgáltatások szolgáltatás van telepítve. A gyors üzembe helyezési útmutató céljából alkalmazás Uri van `fabric:/SampleApp` , és az alkalmazáshoz tartozó megbízható állapotalapú alkalmazások és szolgáltatások szolgáltatás URI-jának `fabric:/SampleApp/MyStatefulService`. Ez a szolgáltatás telepítve van az egypartíciós, és a Partícióazonosító van `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.
- A rendszergazdai szerepkörrel rendelkező ügyfél tanúsítvány telepítve van a _a_ (_személyes_) nevét tárolja _CurrentUser_ tárolási helye a számítógépen az alábbi where tanúsítvány parancsfájlok fogja meghívni. Ez a példa `1b7ebe2174649c45474a4819dafae956712c31d3` , a tanúsítvány ujjlenyomatát. Az ügyféltanúsítványok további információkért lásd: [szerepköralapú hozzáférés-vezérlés a Service Fabric ügyfelek](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>A biztonsági mentési házirend létrehozása

Első lépés, ha a biztonsági mentési házirend ütemezett biztonsági mentés, a célként megadott biztonsági mentési adatokat, a házirend nevét és a maximális növekményes biztonsági mentések teljes biztonsági mentés elindítása előtt engedélyezett leíró. 

Biztonságimásolat-tároláshoz az Azure Storage a fenti létrehozott fiókot használja. Tároló `backup-container` biztonsági mentések tárolására van beállítva. Egy tároló ezen a néven jön létre, ha még nem létezik, biztonsági mentési feltöltés közben. Feltöltése `ConnectionString` az Azure Storage-fiók érvényes kapcsolati karakterláncot a cseréje `account-name` rendelkező a tárfiók nevére, és `account-key` rendelkező a tárfiók kulcsára.

Hajtsa végre a következő PowerShell-parancsfájl az új házirend létrehozásához szükséges REST API meghívása. Cserélje le `account-name` rendelkező a tárfiók nevére, és `account-key` rendelkező a tárfiók kulcsára.

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

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

### <a name="enable-periodic-backup"></a>Rendszeres biztonsági mentés engedélyezése
Az alkalmazás adatvédelmi követelményeinek teljesítéséhez a biztonsági mentési házirend meghatározása, után a biztonsági mentési házirendet az alkalmazással társított kell lennie. Követelmény, attól függően, hogy a biztonsági mentési házirend társítva van egy alkalmazás, szolgáltatás vagy egy partíció lehet.

Hajtsa végre a következő PowerShell-parancsfájl az rendelje hozzá a biztonsági mentési házirend nevű szükséges REST API meghívása `BackupPolicy1` a fenti lépés alkalmazással létrehozott `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Ellenőrizze, hogy működik-e a rendszeres biztonsági mentést

Miután engedélyezte a biztonsági mentés az alkalmazás szintjén, a megbízható állapotalapú alkalmazások és szolgáltatások services és Reliable Actors alatt az alkalmazáshoz tartozó összes partíció első biztonsági másolat rendszeres időközönként a tartozó biztonsági mentési házirend szerint fog elindulni. 

![Partíció BackedUp Állapotesemény][0]

### <a name="list-backups"></a>Lista biztonsági mentések

Megbízható állapotalapú alkalmazások és szolgáltatások services és Reliable Actors az alkalmazás tartozó összes partíció társított biztonsági mentések használatával vehető számba _GetBackups_ API. Biztonsági mentések számba vehetők egy alkalmazás, szolgáltatás vagy partíció.

Hajtsa végre a következő PowerShell parancsfájlt a HTTP API számba venni az összes partíció belül létrehozott biztonsági mentéseket a `SampleApp` alkalmazás.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.2-preview"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
Futtassa a fenti példa kimenete:

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

## <a name="preview-limitation-caveats"></a>Tekintse meg a korlátozás / figyelmeztetések
- Nem a Service Fabric PowerShell-parancsmagok beépített.
- Service Fabric CLI nem támogatott.
- Az automatikus biztonsági mentési végleges törlése nem támogatott. Kézi megtisztítsa azokat a biztonsági mentések igényel.
- Nem támogatja a Service Fabric-fürtök Linux rendszeren.

## <a name="next-steps"></a>További lépések
- [Biztonságimásolat-visszaállítással REST API-referencia](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent_Azure.png

