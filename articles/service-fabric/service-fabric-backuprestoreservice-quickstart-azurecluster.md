---
title: Rendszeres biztonsági mentés és visszaállítás az Azure Service Fabricben
description: Használja a Service Fabric rendszeres biztonsági mentési és visszaállítási szolgáltatását az alkalmazásadatok rendszeres adatbiztonsági mentésének engedélyezéséhez.
author: hrushib
ms.topic: conceptual
ms.date: 5/24/2019
ms.author: hrushib
ms.openlocfilehash: f56fcb7d1dde700d954c3b55bcf8cd7759893521
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259005"
---
# <a name="periodic-backup-and-restore-in-an-azure-service-fabric-cluster"></a>Rendszeres biztonsági mentés és visszaállítás egy Azure Service Fabric-fürtben
> [!div class="op_single_selector"]
> * [Fürtök az Azure-on](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Önálló fürtök](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

A Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a megbízható, elosztott, mikroszolgáltatásokon alapuló felhőalkalmazások fejlesztését és kezelését. Lehetővé teszi mind az állapotmentes, mind az állapotalapú mikroszolgáltatások futtatását. Az állapotalapú szolgáltatások a kérésen és a válaszon vagy a teljes tranzakción túl is fenntarthatnak változékony, mérvadó állapotot. Ha egy állapotalapú szolgáltatás hosszú ideig leáll, vagy egy katasztrófa miatt elveszíti az adatokat, előfordulhat, hogy vissza kell állítani az állapotának egy újabb biztonsági mentésére annak érdekében, hogy a szolgáltatás nyújtása után is tovább szolgáljon, miután az biztonsági másolatot készít.

A Service Fabric replikálja az állapotot több csomópont között annak érdekében, hogy a szolgáltatás magas rendelkezésre állású legyen. Még akkor is, ha a fürt egyik csomópontja meghibásodik, a szolgáltatás továbbra is elérhető. Bizonyos esetekben azonban még mindig kívánatos, hogy a szolgáltatási adatok megbízhatóak legyenek a szélesebb körű hibákkal szemben.
 
Előfordulhat például, hogy a szolgáltatás biztonsági másolatot szeretne tenni az adatairól, hogy védelmet nyújtjon a következő esetekben:
- Abban az esetben, ha a teljes Service Fabric-fürt végleges elvesztése.
- A szolgáltatáspartíció kópiák többségének végleges elvesztése
- Felügyeleti hibák, amelyek alapján az állapot véletlenül törlődik vagy megsérül. Például egy megfelelő jogosultsággal rendelkező rendszergazda tévesen törli a szolgáltatást.
- Hibák a szolgáltatásban, amelyek adatsérülést okoznak. Ez például akkor fordulhat elő, ha egy szolgáltatáskód-frissítés hibás adatokat kezd írni egy megbízható gyűjteménybe. Ebben az esetben előfordulhat, hogy mind a kódot, mind az adatokat vissza kell fordítani egy korábbi állapotba.
- Offline adatfeldolgozás. Célszerű lehet az adatok offline feldolgozása az üzleti intelligencia számára, amely az adatokat generáló szolgáltatástól elkülönítve történik.

A Service Fabric beépített API-t biztosít az [időbiztonsági mentéshez és visszaállításhoz.](service-fabric-reliable-services-backup-restore.md) Az alkalmazásfejlesztők ezekkel az API-kkal rendszeres időközönként biztonsági másolatot kaphatnak a szolgáltatás állapotáról. Továbbá ha a szolgáltatás-rendszergazdák egy adott időpontban szeretnének biztonsági másolatot készíteni a szolgáltatáson kívülről, például az alkalmazás frissítése előtt, a fejlesztőknek a szolgáltatásból API-ként kell elérhetővé tennia a biztonsági mentést (és a visszaállítást). A biztonsági mentések karbantartása további költségeket magasabb, mint ez. Például érdemes lehet, hogy öt növekményes biztonsági mentések félóránként, majd egy teljes biztonsági mentést. A teljes biztonsági mentés után törölheti a korábbi növekményes biztonsági mentéseket. Ez a megközelítés további kódot igényel, amely az alkalmazás fejlesztéssorán további költségekhez vezet.

A Biztonsági mentés és visszaállítás szolgáltatás a Service Fabric lehetővé teszi az állapotalapú szolgáltatásokban tárolt információk egyszerű és automatikus biztonsági mentését. Az alkalmazásadatok rendszeres biztonsági mentése alapvető fontosságú az adatvesztés és a szolgáltatás elérhetetlensége elleni védelemhez. A Service Fabric egy opcionális biztonsági mentési és visszaállítási szolgáltatást biztosít, amely lehetővé teszi az állapotalapú megbízható szolgáltatások (beleértve az aktorszolgáltatásokat) időszakos biztonsági mentésének konfigurálását anélkül, hogy további kódot kellene írnia. Emellett megkönnyíti a korábban készített biztonsági mentések visszaállítását is. 


A Service Fabric api-k készletét biztosítja az időszakos biztonsági mentési és visszaállítási szolgáltatással kapcsolatos következő funkciók eléréséhez:

- Ütemezze a megbízható állapotalapú szolgáltatások és a Megbízható szereplők rendszeres biztonsági mentését a biztonsági mentés (külső) tárolási helyekre való feltöltésének támogatásával. Támogatott tárolási helyek
    - Azure Storage
    - Fájlmegosztás (helyszíni)
- Biztonsági másolatok felsorolása
- Partíció ad hoc biztonsági mentésének aktiválása
- Partíció visszaállítása előző biztonsági másolat tal
- Biztonsági mentések ideiglenes felfüggesztése
- Biztonsági mentések megőrzési kezelése (közelgő)

## <a name="prerequisites"></a>Előfeltételek
* Service Fabric-fürt a Fabric 6.4-es vagy újabb verziójával. Ebben a cikkben a Service Fabric-fürt Azure-erőforrássablon használatával történő létrehozásának lépéseit ebben a [cikkben](service-fabric-cluster-creation-via-arm.md) olvashatja.
* X.509 Tanúsítvány a tárolóhoz való csatlakozáshoz szükséges titkos kulcsok titkosításához a biztonsági mentések tárolásához. Tekintse meg [a cikket,](service-fabric-cluster-creation-via-arm.md) hogy megtudja, hogyan szerezhet be vagy hozhat létre X.509 tanúsítványt.
* Service Fabric megbízható állapotalapú alkalmazás a Service Fabric SDK 3.0-s vagy újabb verziójával készült. A .NET Core 2.0-t célzó alkalmazások esetében az alkalmazást a Service Fabric SDK 3.1-es vagy újabb verziójával kell felépíteni.
* Hozzon létre Azure Storage-fiókot az alkalmazások biztonsági másolatainak tárolásához.
* Telepítse a Microsoft.ServiceFabric.Powershell.Http modult [előzetes verzióban] konfigurációs hívások kezdeményezéséhez.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* A Microsoft.ServiceFabric.Powershell.Http module használatával ellenőrizze, hogy a fürt csatlakoztatva van-e a `Connect-SFCluster` paranccsal.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Biztonsági mentés és visszaállítás engedélyezése

### <a name="using-azure-portal"></a>Az Azure Portal használata

A `Include backup restore service` lap `+ Show optional settings` alatt `Cluster Configuration` található jelölőnégyzet engedélyezése.

![Biztonsági másolat-visszaállítási szolgáltatás engedélyezése a portállal][1]


### <a name="using-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon használata
Először engedélyeznie kell a _biztonsági mentési és visszaállítási szolgáltatást_ a fürtben. A telepíteni kívánt fürt sablonjának beszereznie. Használhatja a [mintasablonokat,](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) vagy létrehozhat egy Erőforrás-kezelő sablont. Engedélyezze a _biztonsági mentést és_ a visszaállítást a következő lépésekkel:

1. Ellenőrizze, `apiversion` hogy az **`2018-02-01`** `Microsoft.ServiceFabric/clusters` erőforrásra van-e beállítva, és ha nem, frissítse a következő kódrészletben látható módon:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Most engedélyezze a _biztonsági mentési és visszaállítási szolgáltatást_ a következő `addonFeatures` szakaszban, `properties` ahogy az a következő kódrészletben látható: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Az X.509 tanúsítvány konfigurálása a hitelesítő adatok titkosításához. Ez fontos annak biztosításához, hogy a tárolóhoz való csatlakozáshoz megadott hitelesítő adatok titkosítva legyenek a megőrzés előtt. A titkosítási tanúsítvány `BackupRestoreService` konfigurálása a következő szakasznak a szakaszban `fabricSettings` látható módon, akövetkező kódrészletben látható módon: 

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

4. Miután frissítette a fürtsablont az előző módosításokkal, alkalmazza őket, és hagyja, hogy a központi telepítés/frissítés befejeződjön. A biztonsági _mentési és visszaállítási szolgáltatás_ futása a fürtben elindul. A szolgáltatás Uri-ja, `fabric:/System/BackupRestoreService` és a szolgáltatás a Service Fabric-kezelő rendszerszolgáltatás-szakasza alatt található. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Rendszeres biztonsági mentés engedélyezése a megbízható állapotalapú szolgáltatáshoz és a megbízható szereplőkhez
Menjünk végig a lépéseket, hogy rendszeres biztonsági mentés megbízható állapotalapú szolgáltatás és megbízható szereplők. Ezek a lépések feltételezik, hogy
- Hogy a fürt beállítása az X.509 biztonsági _mentési és visszaállítási szolgáltatással_.
- A fürtön egy megbízható állapotalapú szolgáltatás van telepítve. Ennek a rövid útmutatónak a alkalmazásában az Uri alkalmazás `fabric:/SampleApp` és az Uri `fabric:/SampleApp/MyStatefulService`az alkalmazáshoz tartozó megbízható állapotalapú szolgáltatás . Ez a szolgáltatás egyetlen partícióval van telepítve, `974bd92a-b395-4631-8a7f-53bd4ae9cf22`és a partíció azonosítója .
- A rendszergazdai szerepkörrel rendelkező ügyféltanúsítvány a _CurrentUser_ tanúsítványtároló helyének _My_ (_Personal_) tárolójában van telepítve azon a számítógépen, ahonnan az alábbi parancsfájlok meghívásra kerülnek. Ez a `1b7ebe2174649c45474a4819dafae956712c31d3` példa a tanúsítvány ujjlenyomataként használatos. Az ügyféltanúsítványokról további információt a [Service Fabric-ügyfelek szerepköralapú hozzáférés-vezérlése című témakörben talál.](service-fabric-cluster-security-roles.md)

### <a name="create-backup-policy"></a>Biztonsági mentési házirend létrehozása

Az első lépés a biztonsági mentési házirend létrehozása, amely leírja a biztonsági mentés ütemezését, a biztonsági mentési adatok céltárolását, a házirend nevét, a maximális növekményes biztonsági mentéseket, mielőtt teljes biztonsági mentési és adatmegőrzési házirendet indítana el a biztonsági mentési tároláshoz. 

A biztonsági mentési tároláshoz használja a fent létrehozott Azure Storage-fiókot. A `backup-container` tároló biztonsági másolatok tárolására van konfigurálva. Ezzel a névvel rendelkező tároló jön létre, ha még nem létezik, a biztonsági mentés feltöltése során. Feltöltheti `ConnectionString` az Azure Storage-fiók érvényes kapcsolati `account-name` karakterláncát, lecserélheti a tárfiók nevét és `account-key` a tárfiók kulcsát.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell a Microsoft.ServiceFabric.Powershell.Http modul használatával

Hajtsa végre a következő PowerShell-parancsmagokat az új biztonsági mentési szabályzat létrehozásához. Cserélje `account-name` le a tárfiók `account-key` nevét, és a tárfiók kulcsát.

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container' -Basic -RetentionDuration '10.00:00:00'

```

#### <a name="rest-call-using-powershell"></a>Rest Call a PowerShell használatával

Hajtsa végre a következő PowerShell-parancsfájlt a szükséges REST API meghívásához az új szabályzat létrehozásához. Cserélje `account-name` le a tárfiók `account-key` nevét, és a tárfiók kulcsát.

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

#### <a name="using-service-fabric-explorer"></a>A Service Fabric Intéző használata

1. A Service Fabric Intézőben keresse meg a Biztonsági mentések lapot, és válassza a Műveletek > biztonsági mentési házirend létrehozása lehetőséget.

    ![Biztonsági másolat házirendjének létrehozása][6]

2. Töltse ki az adatokat. Az Azure-fürtök, AzureBlobStore ki kell jelölni.

    ![Azure Blob Storage biztonsági mentési szabályzat létrehozása][7]

### <a name="enable-periodic-backup"></a>Rendszeres biztonsági mentés engedélyezése
Az alkalmazás adatvédelmi követelményeinek teljesítéséhez szükséges biztonsági mentési szabályzat meghatározása után a biztonsági mentési szabályzatot az alkalmazáshoz kell társozni. A követelménytől függően a biztonsági mentési házirend alkalmazáshoz, szolgáltatáshoz vagy partícióhoz társítható.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell a Microsoft.ServiceFabric.Powershell.Http modul használatával

```powershell

Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'

```
#### <a name="rest-call-using-powershell"></a>Rest Call a PowerShell használatával

Hajtsa végre a következő PowerShell-parancsfájlt a `BackupPolicy1` szükséges REST API `SampleApp`meghívásához, hogy a biztonsági mentési házirendet a fenti lépésben létrehozott névvel társítsa az alkalmazással.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

#### <a name="using-service-fabric-explorer"></a>A Service Fabric Intéző használata

1. Jelöljön ki egy alkalmazást, és folytassa a műveletet. Kattintson az Alkalmazás biztonsági másolatának engedélyezése/frissítése gombra.

    ![Alkalmazásbiztonsági mentés engedélyezése][3]

2. Végül válassza ki a kívánt házirendet, és kattintson a Biztonsági mentés engedélyezése gombra.

    ![Házirend kiválasztása][4]


### <a name="verify-that-periodic-backups-are-working"></a>Annak ellenőrzése, hogy működnek-e az időszakos biztonsági mentések

Miután lehetővé tette a biztonsági mentést az alkalmazás szintjén, a megbízható állapotalapú szolgáltatásokhoz tartozó összes partíció és az alkalmazás megbízható szereplői rendszeres időközönként biztonsági mentést kezdenek a társított biztonsági mentési szabályzatnak megfelelően. 

![Partition BackedUp állapotesemény][0]

### <a name="list-backups"></a>Biztonsági másolatok listázása

A Megbízható állapotalapú szolgáltatásokhoz tartozó összes partícióhoz és az alkalmazás Reliable Actors-hez tartozó biztonsági mentések a _GetBackups_ API használatával számba sorolhatók. A biztonsági mentések egy alkalmazás, szolgáltatás vagy partíció számava.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell a Microsoft.ServiceFabric.Powershell.Http modul használatával

```powershell
    
Get-SFApplicationBackupList -ApplicationId WordCount
```

#### <a name="rest-call-using-powershell"></a>Rest Call a PowerShell használatával

Hajtsa végre a következő PowerShell-parancsfájlt a HTTP API meghívásához `SampleApp` az alkalmazáson belüli összes partícióhoz létrehozott biztonsági mentések számbavételéhez.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

#### <a name="using-service-fabric-explorer"></a>A Service Fabric Intéző használata

A Service Fabric Intézőben a biztonsági mentések megtekintéséhez keresse meg a partíciót, és válassza a Biztonsági mentések lapot.

![Biztonsági másolatok felsorolása][5]

## <a name="limitation-caveats"></a>Korlátozás/ kikötések
- A Service Fabric PowerShell-parancsmagok előnézeti módban vannak.
- Nem támogatja a Service Fabric-fürtök Linuxon.

## <a name="next-steps"></a>További lépések
- [Rendszeres biztonsági mentési konfiguráció ismertetése](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Rest API-kézikönyv biztonsági mentésének visszaállítása](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event-azure.png
[1]: ./media/service-fabric-backuprestoreservice/enable-backup-restore-service-with-portal.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/creation-bp.png