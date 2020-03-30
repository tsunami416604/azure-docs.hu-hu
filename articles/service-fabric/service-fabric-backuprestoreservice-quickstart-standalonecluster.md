---
title: Időszakos biztonsági mentés/visszaállítás az önálló Azure Service Fabricben
description: Használja a Service Fabric rendszeres biztonsági mentési és visszaállítási szolgáltatását az alkalmazásadatok rendszeres adatbiztonsági mentésének engedélyezéséhez.
author: hrushib
ms.topic: conceptual
ms.date: 5/24/2019
ms.author: hrushib
ms.openlocfilehash: 938cbbde9f53c52350ef64715f6c61c4aa961057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75526243"
---
# <a name="periodic-backup-and-restore-in-a-standalone-service-fabric"></a>Időszakos biztonsági mentés és visszaállítás önálló szolgáltatáshálóban
> [!div class="op_single_selector"]
> * [Fürtök az Azure-on](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Önálló fürtök](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

A Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a megbízható, elosztott, mikroszolgáltatásokon alapuló felhőalkalmazások fejlesztését és kezelését. Lehetővé teszi mind az állapotmentes, mind az állapotalapú mikroszolgáltatások futtatását. Az állapotalapú szolgáltatások a kérésen és a válaszon vagy a teljes tranzakción túl is fenntarthatnak változékony, mérvadó állapotot. Ha egy állapotalapú szolgáltatás hosszú ideig leáll, vagy egy katasztrófa miatt elveszíti az adatokat, előfordulhat, hogy vissza kell állítani az állapotának egy újabb biztonsági mentésére annak érdekében, hogy a szolgáltatás nyújtása után is tovább szolgáljon, miután az biztonsági másolatot készít.

A Service Fabric replikálja az állapotot több csomópont között annak érdekében, hogy a szolgáltatás magas rendelkezésre állású legyen. Még akkor is, ha a fürt egyik csomópontja meghibásodik, a szolgáltatás továbbra is elérhető. Bizonyos esetekben azonban még mindig kívánatos, hogy a szolgáltatási adatok megbízhatóak legyenek a szélesebb körű hibákkal szemben.
 
Előfordulhat például, hogy egy szolgáltatás biztonsági másolatot szeretne tenni az adatairól, hogy védelmet nyújtjon a következő esetekben:
- Egy teljes Service Fabric-fürt végleges elvesztése.
- A szolgáltatáspartíció kópiák többségének végleges elvesztése
- Felügyeleti hibák, amelyek alapján az állapot véletlenül törlődik vagy megsérül. Például egy megfelelő jogosultsággal rendelkező rendszergazda tévesen törli a szolgáltatást.
- Hibák a szolgáltatásban, amelyek adatsérülést okoznak. Ez például akkor fordulhat elő, ha egy szolgáltatáskód-frissítés hibás adatokat kezd írni egy megbízható gyűjteménybe. Ebben az esetben előfordulhat, hogy mind a kódot, mind az adatokat vissza kell fordítani egy korábbi állapotba.
- Offline adatfeldolgozás. Célszerű lehet az adatok offline feldolgozása az üzleti intelligencia számára, amely az adatokat generáló szolgáltatástól elkülönítve történik.

A Service Fabric beépített API-t biztosít az [időbiztonsági mentéshez és visszaállításhoz.](service-fabric-reliable-services-backup-restore.md) Az alkalmazásfejlesztők ezekkel az API-kkal rendszeres időközönként biztonsági másolatot kaphatnak a szolgáltatás állapotáról. Továbbá ha a szolgáltatás-rendszergazdák egy adott időpontban szeretnének biztonsági másolatot készíteni a szolgáltatáson kívülről, például az alkalmazás frissítése előtt, a fejlesztőknek a szolgáltatásból API-ként kell elérhetővé tennia a biztonsági mentést (és a visszaállítást). A biztonsági mentések karbantartása további költségeket magasabb, mint ez. Például érdemes lehet, hogy öt növekményes biztonsági mentések félóránként, majd egy teljes biztonsági mentést. A teljes biztonsági mentés után törölheti a korábbi növekményes biztonsági mentéseket. Ez a megközelítés további kódot igényel, amely az alkalmazás fejlesztéssorán további költségekhez vezet.

Az alkalmazásadatok rendszeres biztonsági mentése rendszeres időközönként alapvető fontosságú egy elosztott alkalmazás kezeléséhez és az adatvesztés vagy a szolgáltatás rendelkezésre állásának tartós elvesztésének elleni védelemhez. A Service Fabric egy opcionális biztonsági mentési és visszaállítási szolgáltatást biztosít, amely lehetővé teszi az állapotalapú megbízható szolgáltatások (beleértve az aktorszolgáltatásokat) időszakos biztonsági mentésének konfigurálását anélkül, hogy további kódot kellene írnia. Emellett megkönnyíti a korábban készített biztonsági mentések visszaállítását is. 

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
* Service Fabric-fürt a Fabric 6.4-es vagy újabb verziójával. A szükséges csomagok letöltésének lépéseit ebben a [cikkben](service-fabric-cluster-creation-for-windows-server.md) olvashatja.
* X.509 Tanúsítvány a tárolóhoz való csatlakozáshoz szükséges titkos kulcsok titkosításához a biztonsági mentések tárolásához. Tekintse meg [a cikket,](service-fabric-windows-cluster-x509-security.md) hogy tudja, hogyan lehet beszerezni vagy létrehozni egy önaláírt X.509 tanúsítványt.

* Service Fabric megbízható állapotalapú alkalmazás a Service Fabric SDK 3.0-s vagy újabb verziójával készült. A .Net Core 2.0-t célzó alkalmazások esetében az alkalmazást a Service Fabric SDK 3.1-es vagy újabb verziójával kell felépíteni.
* Telepítse a Microsoft.ServiceFabric.Powershell.Http modult [előzetes verzióban] konfigurációs hívások kezdeményezéséhez.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* A Microsoft.ServiceFabric.Powershell.Http module használatával ellenőrizze, hogy a fürt csatlakoztatva van-e a `Connect-SFCluster` paranccsal.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Biztonsági mentés és visszaállítás engedélyezése
Először engedélyeznie kell a _biztonsági mentési és visszaállítási szolgáltatást_ a fürtben. A telepíteni kívánt fürt sablonjának beszereznie. Használhatja a [mintasablonokat](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Engedélyezze a _biztonsági mentést és_ a visszaállítást a következő lépésekkel:

1. Ellenőrizze, `apiversion` hogy a `10-2017` fürt konfigurációs fájljában van-e beállítva, és ha nem, frissítse a következő kódrészletben látható módon:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Most engedélyezze a _biztonsági mentési és visszaállítási szolgáltatást_ a következő `addonFeatures` szakaszban, `properties` ahogy az a következő kódrészletben látható: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
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

4. Miután frissítette a fürt konfigurációs fájlját az előző módosításokkal, alkalmazza azokat, és hagyja, hogy a központi telepítés/frissítés befejeződjön. A biztonsági _mentési és visszaállítási szolgáltatás_ futása a fürtben elindul. A szolgáltatás Uri-ja, `fabric:/System/BackupRestoreService` és a szolgáltatás a Service Fabric-kezelő rendszerszolgáltatás-szakasza alatt található. 



## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Rendszeres biztonsági mentés engedélyezése a megbízható állapotalapú szolgáltatáshoz és a megbízható szereplőkhez
Menjünk végig a lépéseket, hogy rendszeres biztonsági mentés megbízható állapotalapú szolgáltatás és megbízható szereplők. Ezek a lépések feltételezik, hogy
- A fürt _biztonsági mentési és visszaállítási szolgáltatással_van beállítva.
- A fürtön egy megbízható állapotalapú szolgáltatás van telepítve. Ennek a rövid útmutatónak a alkalmazásában az Uri alkalmazás `fabric:/SampleApp` és az Uri `fabric:/SampleApp/MyStatefulService`az alkalmazáshoz tartozó megbízható állapotalapú szolgáltatás . Ez a szolgáltatás egyetlen partícióval van telepítve, `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`és a partíció azonosítója .  

### <a name="create-backup-policy"></a>Biztonsági mentési házirend létrehozása

Az első lépés a biztonsági mentési házirend létrehozása, amely leírja a biztonsági mentés ütemezését, a biztonsági mentési adatok céltárolását, a házirend nevét, a maximális növekményes biztonsági mentéseket, mielőtt teljes biztonsági mentési és adatmegőrzési házirendet indítana el a biztonsági mentési tároláshoz. 

Biztonsági mentési tároláshoz hozzon létre fájlmegosztást, és adjon readwrite-hozzáférést ehhez a fájlmegosztáshoz az összes Service Fabric-csomópont-gépen. Ez a példa feltételezi, `BackupStore` hogy `StorageServer`a névvel rendelkező megosztás jelen van a on.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell a Microsoft.ServiceFabric.Powershell.Http modul használatával

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -FileShare -Path '\\StorageServer\BackupStore' -Basic -RetentionDuration '10.00:00:00'

```
#### <a name="rest-call-using-powershell"></a>Rest Call a Powershell használatával

Hajtsa végre a következő PowerShell-parancsfájlt a szükséges REST API meghívásához az új szabályzat létrehozásához.

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

#### <a name="using-service-fabric-explorer"></a>A Service Fabric Intéző használata

1. A Service Fabric Intézőben keresse meg a Biztonsági mentések lapot, és válassza a Műveletek > biztonsági mentési házirend létrehozása lehetőséget.

    ![Biztonsági másolat házirendjének létrehozása][6]

2. Töltse ki az adatokat. Önálló fürtök esetén a FileShare-t kell kijelölni.

    ![Biztonságimásolat-készítési házirend fájlmegosztásának létrehozása][7]

### <a name="enable-periodic-backup"></a>Rendszeres biztonsági mentés engedélyezése
Az alkalmazás adatvédelmi követelményeinek teljesítésére vonatkozó házirend meghatározása után a biztonsági mentési szabályzatot az alkalmazáshoz kell társozni. A követelménytől függően a biztonsági mentési házirend alkalmazáshoz, szolgáltatáshoz vagy partícióhoz társítható.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell a Microsoft.ServiceFabric.Powershell.Http modul használatával

```powershell
Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'
```

#### <a name="rest-call-using-powershell"></a>Rest Call a Powershell használatával
Hajtsa végre a következő PowerShell-parancsfájlt a `BackupPolicy1` szükséges REST API `SampleApp`meghívásához, hogy a biztonsági mentési házirendet a fenti lépésben létrehozott névvel társítsa az alkalmazással.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

#### <a name="using-service-fabric-explorer"></a>A Service Fabric Intéző használata

1. Jelöljön ki egy alkalmazást, és folytassa a műveletet. Kattintson az Alkalmazás biztonsági másolatának engedélyezése/frissítése gombra.

    ![Alkalmazásbiztonsági mentés engedélyezése][3] 

2. Végül válassza ki a kívánt házirendet, és kattintson a Biztonsági mentés engedélyezése gombra.

    ![Házirend kiválasztása][4]

### <a name="verify-that-periodic-backups-are-working"></a>Annak ellenőrzése, hogy működnek-e az időszakos biztonsági mentések

Miután lehetővé tette a biztonsági mentést az alkalmazás, a megbízható állapotalapú szolgáltatások és az alkalmazás megbízható szereplői tartozó partíciók rendszeres időközönként megkezdi a biztonsági mentést, mint a társított biztonsági mentési szabályzat.

![Partition BackedUp állapotesemény][0]

### <a name="list-backups"></a>Biztonsági másolatok listázása

A Megbízható állapotalapú szolgáltatásokhoz tartozó összes partícióhoz és az alkalmazás Reliable Actors-hez tartozó biztonsági mentések a _GetBackups_ API használatával számba sorolhatók. A követelménytől függően a biztonsági mentések felsorolhatók az alkalmazáshoz, a szolgáltatáshoz vagy a partícióhoz.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>Powershell a Microsoft.ServiceFabric.Powershell.Http modul használatával

```powershell
    Get-SFApplicationBackupList -ApplicationId WordCount     
```

#### <a name="rest-call-using-powershell"></a>Rest Call a Powershell használatával

Hajtsa végre a következő PowerShell-parancsfájlt a HTTP API meghívásához `SampleApp` az alkalmazáson belüli összes partícióhoz létrehozott biztonsági mentések számbavételéhez.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Mintakimenet a fenti futtatáshoz:

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

#### <a name="using-service-fabric-explorer"></a>A Service Fabric Intéző használata

A Service Fabric Intézőben a biztonsági mentések megtekintéséhez keresse meg a partíciót, és válassza a Biztonsági mentések lapot.

![Biztonsági másolatok felsorolása][5]

## <a name="limitation-caveats"></a>Korlátozás/ kikötések
- A Service Fabric PowerShell-parancsmagok előnézeti módban vannak.
- Nem támogatja a Service Fabric-fürtök Linuxon.

## <a name="next-steps"></a>További lépések
- [Rendszeres biztonsági mentési konfiguráció ismertetése](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Rest API-kézikönyv biztonsági mentésének visszaállítása](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/create-bp-fileshare.png