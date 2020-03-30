---
title: Az Azure-fájlok biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági,
description: Ebben a cikkben megtudhatja, hogyan készíthet biztonsági másolatot az Azure Files-ról az Azure Backup szolgáltatás és a PowerShell használatával.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f85451e0da6458de34aea936836b46781f4c4a21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273539"
---
# <a name="back-up-azure-files-with-powershell"></a>Az Azure-fájlok biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági,

Ez a cikk bemutatja, hogyan azure PowerShell segítségével biztonsági másolatot készíteni egy Azure Files fájlmegosztás egy [Azure Backup](backup-overview.md) Recovery Services-tároló használatával.

Ez a cikk a következőket ismerteti:

> [!div class="checklist"]
>
> * Állítsa be a PowerShellt, és regisztrálja az Azure Recovery Services Provider.Set up PowerShell and register the Azure Recovery Services Provider.
> * Recovery Services-tároló létrehozása.
> * Konfigurálja a biztonsági másolatot egy Azure-fájlmegosztáshoz.
> * Futtasson egy biztonsági mentési feladatot.

## <a name="before-you-start"></a>Előkészületek

* [További információ](backup-azure-recovery-services-vault-overview.md) a Recovery Services-tárolókról.
* Az [Azure-fájlmegosztások biztonsági mentésének](backup-afs.md)előzetes verziójáról olvashat.
* Tekintse át a PowerShell-objektumhierarchiát a Recovery Services számára.

## <a name="recovery-services-object-hierarchy"></a>Helyreállítási szolgáltatások objektumhierarchiája

Az objektumhierarchiát a következő ábra foglalja össze.

![Helyreállítási szolgáltatások objektumhierarchiája](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Tekintse át az **Az.RecoveryServices** [parancsmag referenciahivatkozást](/powershell/module/az.recoveryservices) az Azure-kódtárban.

## <a name="set-up-and-install"></a>Beállítás és telepítés

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Állítsa be a PowerShellt az alábbiak szerint:

1. [Töltse le az Az PowerShell legújabb verzióját.](/powershell/azure/install-az-ps) A minimális verzió szükséges 1.0.0.

> [!WARNING]
> Az előzetes verzióhoz szükséges PS minimális verziója az "Az 1.0.0" volt. A GA közelgő módosításai miatt a szükséges minimális PS-verzió az "Az.RecoveryServices 2.6.0" lesz. Nagyon fontos, hogy frissítse az összes meglévő PS verziót erre a verzióra. Ellenkező esetben a meglévő parancsfájlok szünet után GA. A minimális verzió telepítése a következő PS-parancsokkal

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

2. Keresse meg az Azure Backup PowerShell-parancsmagjait ezzel a paranccsal:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Tekintse át az Azure Backup, az Azure Site Recovery és a Recovery Services-tároló aliasait és parancsmagjait. Íme egy példa arra, hogy mit láthat. Ez nem egy teljes lista a parancsmagokról.

    ![A helyreállítási szolgáltatások parancsmagjainak listája](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Jelentkezzen be Azure-fiókjába a **Connect-AzAccount segítségével.**
5. A megjelenő weblapon a rendszer kéri, hogy adja meg a fiók hitelesítő adatait.

    * A fiók hitelesítő adatait paraméterként is megadhatja a **Connect-AzAccount** parancsmag **-Credential paraméterei**közé.
    * Ha ön egy bérlő nevében dolgozó csp-partner, adja meg az ügyfelet bérlőként a bérlőazonosító vagy a bérlő elsődleges tartományneve használatával. Erre példa a **Connect-AzAccount -Tenant** fabrikam.com.

6. Társítsa a használni kívánt előfizetést a fiókhoz, mert egy fióknak több előfizetése is lehet.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Ha első alkalommal használja az Azure Backup szolgáltatást, a **Register-AzResourceProvider** parancsmaggal regisztrálja az Azure Recovery Services-szolgáltatót az előfizetéssel.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Ellenőrizze, hogy a szolgáltatók sikeresen regisztráltak-e:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. A parancs kimenetében ellenőrizze, hogy a **RegistrationState** regisztrált ra **változik-e.** Ha nem, futtassa újra a **Register-AzResourceProvider** parancsmagát.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services-tároló egy Erőforrás-kezelő erőforrás, ezért egy erőforráscsoporton belül kell elhelyezni. Használhat egy meglévő erőforráscsoportot, vagy létrehozhat egy erőforráscsoportot a **New-AzResourceGroup** parancsmaggal. Erőforráscsoport létrehozásakor adja meg az erőforráscsoport nevét és helyét.

A Helyreállítási szolgáltatások tárolójának létrehozásához kövesse az alábbi lépéseket.

1. A tároló egy erőforráscsoportba kerül. Ha nem rendelkezik meglévő erőforráscsoporttal, hozzon létre egy újat a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). Ebben a példában egy új erőforráscsoportot hozunk létre az USA nyugati régiójában.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. A Vault létrehozásához használja a [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) parancsmag. Adja meg a tároló nak ugyanazt a helyet, amelyet az erőforráscsoporthoz használt.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Adja meg a tároló tárolóhoz használandó redundancia típusát.

   * Helyileg [redundáns tárolást](../storage/common/storage-redundancy-lrs.md) vagy [georedundáns tárolást](../storage/common/storage-redundancy-grs.md)használhat.
   * A következő példa a **-BackupStorageRedundancy** beállítást állítja be a[Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd számára a **GeoRedundant**beállításhoz. **testvault**

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>A tárolók megtekintése egy előfizetésben

Az előfizetés összes tárolójának megtekintéséhez használja a [Get-AzRecoveryServicesVault .To](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)view all vaults in the subscription, use Get-AzRecoveryServicesVault .

```powershell
Get-AzRecoveryServicesVault
```

A kimenet hasonló a következőhöz. Vegye figyelembe, hogy a társított erőforráscsoport és hely meg van adva.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>A tároló környezetének beállítása

Tárolja a tárolóobjektumot egy változóban, és állítsa be a tároló környezetét.

* Számos Azure Backup-parancsmag bemenetként igényli a Recovery Services-tároló objektumát, ezért célszerű a tárolóobjektumot egy változóban tárolni.
* A tárolási környezet a tár által védett adatok típusa. Állítsa be a [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). A környezet beállítása után az összes további parancsmagra vonatkozik.

A következő példa a **testvault**tárolókörnyezetét állítja be.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>A tároló azonosítójának beolvasása

Azt tervezzük, hogy az Azure PowerShell irányelveinek megfelelően elavulttá tesszük a tárolókörnyezet-beállítást. Ehelyett tárolhatja vagy lehívhatja a tárolóazonosítót, és átadhatja a megfelelő parancsoknak. Így ha nem állította be a tároló környezetét, vagy meg szeretné adni a parancsot egy adott tárolószámára, adja át a tároló azonosítóját "-vaultID" azonosítóként az összes releváns parancsnak az alábbiak szerint:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Biztonságimentési házirend konfigurálása

A biztonsági mentési házirend meghatározza a biztonsági mentések ütemezését, valamint azt, hogy mennyi ideig kell megtartani a biztonsági mentéshelyreállítási pontokat:

* A biztonsági mentési szabályzat legalább egy adatmegőrzési házirendhez van társítva. Az adatmegőrzési szabály határozza meg, hogy mennyi ideig a helyreállítási pont megtartása előtt törlődik.
* Tekintse meg az alapértelmezett biztonsági mentési házirend-megőrzést a [Get-AzRecoveryServicesBackupRetentionPolicyObject használatával.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0)
* Tekintse meg az alapértelmezett biztonsági mentési házirend ütemezését a [Get-AzRecoveryServicesBackupPolicyObject objektummal.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)
* A [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) parancsmag használatával új biztonsági mentési házirendet hozhat létre. Adja meg az ütemezési és adatmegőrzési házirend objektumok.

Alapértelmezés szerint a kezdési időpont az Ütemezési házirendobjektumban van definiálva. A következő példában módosíthatja a kezdési időpontot a kívánt kezdési időpontra. A kívánt kezdési időpontnak UTC-ben is meg kell lennie. Az alábbi példa feltételezi, hogy a kívánt kezdési időpont 01:00 UTC a napi biztonsági mentések.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Meg kell adnia a kezdési időt csak 30 perces többszörösökben. A fenti példában csak "01:00:00" vagy "02:30:00" lehet. A kezdési időpont nem lehet "01:15:00"

A következő példa tárolja az ütemezési szabályzatot és az adatmegőrzési házirendet változókban. Ezután ezeket a változókat használja paraméterekként egy új házirendhez (**NewAFSPolicy**). **NewAFSPolicy** vesz egy napi biztonsági mentést, és megtartja azt 30 napig.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A kimenet hasonló a következőhöz.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Biztonsági mentés engedélyezése

Miután definiálja a biztonsági mentési szabályzatot, engedélyezheti az Azure-fájlmegosztás védelmét a szabályzat használatával.

### <a name="retrieve-a-backup-policy"></a>Biztonsági mentési házirend beolvasása

A megfelelő házirendobjektumot a [Get-AzRecoveryServicesBackupProtectionPolicy segítségével](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0)szerezheti be. Ezzel a parancsmaggal egy adott szabályzatot kaphat, vagy megtekintheti a számítási feladatok típusához társított házirendeket.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Munkaterhelés-típus házirendjének lekérése

A következő példa az **AzureFiles**munkaterhelés-típus házirendjeit olvassa be.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

A kimenet hasonló a következőhöz.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> A PowerShell **BackupTime** mezőjének időzónája az egyezményes világidő (UTC). Amikor a biztonsági mentési idő megjelenik az Azure Portalon, az idő a helyi időzónához igazodik.

### <a name="retrieve-a-specific-policy"></a>Adott házirend beolvasása

A következő házirend beolvassa a dailyafs nevű biztonsági **mentési házirendet.**

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Biztonsági mentés engedélyezése és házirend alkalmazása

Engedélyezze a védelmet az [Enable-AzRecoveryServicesBackupProtection segítségével.](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0) Miután a szabályzat a tárolóhoz van társítva, a biztonsági mentések a házirend-ütemezésnek megfelelően aktiválódnak.

A következő példa lehetővé teszi az Azure **fájlmegosztási testAzureFileShare** védelmét a **storageAcct**tárfiókban , a **szabályzat dailyafs-szal.**

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

A parancs megvárja, amíg a konfigurálásvédelmi feladat befejeződik, és hasonló kimenetet ad, ahogy az látható.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice---backup-item-identification-for-afs-backups"></a>Fontos megjegyzés - Biztonsági másolat elemének azonosítása az AFS biztonsági mentések esetén

Ez a szakasz az AFS biztonsági mentésének fontos változását ismerteti a GA előkészítése során.

Az AFS biztonsági mentésének engedélyezése közben a felhasználó entitásnévként adja meg az ügyfélbarát fájlmegosztás nevét, és létrejön egy biztonsági másolat. A biztonsági másolat elem "neve" egy egyedi azonosító az Azure Backup szolgáltatás által létrehozott. Az azonosító általában felhasználóbarát nevet tartalmaz. De a helyreállítható törlés fontos forgatókönyvének kezeléséhez, ahol egy fájlmegosztás törölhető, és egy másik fájlmegosztás is létrehozható ugyanazzal a névvel, az Azure-fájlmegosztás egyedi identitása mostantól azonosító lesz az ügyfélbarát név helyett. Annak érdekében, hogy megismerjék az egyes elemek ```Get-AzRecoveryServicesBackupItem``` egyedi identitását/nevét, csak futtassa a parancsot a backupManagementType és a WorkloadType megfelelő szűrőivel az összes releváns elem lekérni, majd figyelje meg a névmezőt a visszaadott PS-objektum/válasz mezőben. Mindig ajánlott az elemek et felsorolni, majd válaszként beolvasni az egyedi nevüket a "név" mezőből. Ezzel az értékkel szűrheti a cikkeket a "Név" paraméterrel. Ellenkező esetben a FriendlyName paraméterrel olvassa be a cikket a vevőbarát névvel/azonosítóval.

> [!WARNING]
> Győződjön meg arról, hogy a PS-verzió az AFS biztonsági mentések esetében az "Az.RecoveryServices 2.6.0" minimális verziójára van frissítve. Ezzel a verzióval a "friendlyName" ```Get-AzRecoveryServicesBackupItem``` szűrő parancsként érhető el. Adja át az Azure File Share nevet a friendlyName paraméternek. Ha átadja az Azure File Share nevet a "Név" paraméternek, ez a verzió figyelmeztetést ad át ennek a rövid névnek a rövid név paraméternek. A minimális verzió telepítésének elmaradása a meglévő parancsfájlok meghibásodását eredményezheti. Telepítse a PS minimális verzióját a következő paranccsal.

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

## <a name="trigger-an-on-demand-backup"></a>Igény szerinti biztonsági mentés aktiválása

A [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) használatával igény szerinti biztonsági mentést futtathat egy védett Azure-fájlmegosztáshoz.

1. A tárfiók lekérése a tárolóból, amely a biztonsági mentési adatokat a [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)tárolóban tárolja.
2. Biztonsági mentési feladat indításához információt kaphat az Azure fájlmegosztásról a [Get-AzRecoveryServicesBackupItem programmal.](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem)
3. Igény szerinti biztonsági mentés futtatása a[Backup-AzRecoveryServicesBackupItem segítségével.](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem)

Futtassa az igény szerinti biztonsági mentést az alábbiak szerint:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

A parancs egy nyomon követendő azonosítóval rendelkező feladatot ad vissza, ahogy az a következő példában látható.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Az Azure-fájlmegosztáspillanatképek a biztonsági mentések készítése közben használatosak, így a feladat általában befejeződik, mire a parancs visszaadja ezt a kimenetet.

### <a name="using-on-demand-backups-to-extend-retention"></a>Igény szerinti biztonsági mentések használata a megőrzés kiterjesztéséhez

Igény szerinti biztonsági mentések segítségével 10 évig őrizheti meg a pillanatképeket. Az ütemezők igény szerinti PowerShell-parancsfájlok futtatására használhatók a kiválasztott megőrzési móddal, és így minden héten, hónapban vagy évben rendszeres időközönként pillanatképeket készíthetnek. Rendszeres pillanatképek készítése közben tekintse meg az Azure [biztonsági mentés használatával az igény szerinti biztonsági mentések korlátait.](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share)

Ha mintaparancsfájlokat keres, a GitHubon (<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>) lévő mintaparancsfájlra hivatkozhat az Azure Automation runbook használatával, amely lehetővé teszi a biztonsági mentések rendszeres ütemezését és akár 10 évig való megtartását.

> [!WARNING]
> Győződjön meg arról, hogy a PS-verzió az "Az.RecoveryServices 2.6.0" minimális verziójára van frissítve az aFS-biztonsági mentések esetében az automation runbookokban. A régi "AzureRM" modult az "Az" modulra kell cserélnie. Ezzel a verzióval a "friendlyName" ```Get-AzRecoveryServicesBackupItem``` szűrő parancsként érhető el. Adja át az azure-fájlmegosztás nevét a friendlyName paraméternek. Ha átadja az azure file share nevet a "Név" paraméter, ez a verzió egy figyelmeztetést, hogy adja át ezt a rövid nevet a rövid név paraméter.

## <a name="next-steps"></a>További lépések

[Ismerje meg az](backup-afs.md) Azure Files biztonsági mentését az Azure Portalon.
