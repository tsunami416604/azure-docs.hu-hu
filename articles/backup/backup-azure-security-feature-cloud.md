---
title: Biztonsági funkciók a Felhőbeli munkaterhelések védelme érdekében
description: Megtudhatja, hogyan teheti biztonságosabbá a biztonsági mentéseket a Azure Backup biztonsági funkciói segítségével.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: bd7c86e18114513a264a0f9252589533fb7ff2d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80668741"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Biztonsági funkciók a Azure Backupt használó Felhőbeli munkaterhelések védelmének biztosításához

A biztonsági problémákkal, például a kártevővel, a ransomware és a behatolással kapcsolatos problémák egyre nagyobbak. Ezek a biztonsági problémák költségesek lehetnek a pénz és az adatmennyiség tekintetében. Az ilyen támadások elleni védelem érdekében Azure Backup mostantól biztonsági funkciókat biztosít a biztonsági mentési adatainak a törlés után még a védelme érdekében.

Az egyik ilyen funkció a Soft delete. A helyreállítható törléssel, még akkor is, ha egy rosszindulatú szereplő töröl egy virtuális gép biztonsági mentését (vagy véletlenül törli a biztonsági mentési adatmennyiséget), a biztonsági mentési adat 14 további napig marad, így a biztonsági mentési elem helyreállítása adatvesztés nélkül történik. A "Soft Delete" állapotban lévő biztonsági mentési adatok további 14 napos megőrzése nem jár költséggel az ügyfélnek. Az Azure az adatok védelmének biztosítása érdekében a [Storage Service encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) használatával is titkosítja az összes biztonsági másolatban tárolt adatait.

Az Azure Virtual Machines szolgáltatásban általánosan elérhető a Soft delete Protection szolgáltatás.

>[!NOTE]
>Az Azure-beli virtuális gépeken futó SQL Server-kiszolgáló és az Azure-beli virtuális gépekhez készült SAP HANA Soft delete már előzetes verzióban érhető el.<br>
>Az előzetes verzióra való feliratkozáshoz írjon nekünk a következőt:AskAzureBackupTeam@microsoft.com

## <a name="soft-delete"></a>Helyreállítható törlés

### <a name="soft-delete-for-vms"></a>Soft Delete a virtuális gépekhez

A virtuális gépek Soft törlésével megvédheti a virtuális gépek biztonsági másolatait a nem kívánt törléstől. Még a biztonsági másolatok törlése után is megmaradnak a további 14 napig.

> [!NOTE]
> A helyreállítható törlés csak a törölt biztonsági mentési adatvédelmet védi. Ha egy virtuális gépet biztonsági mentés nélkül töröl, a helyreállítható funkció nem fogja megőrizni az adatvédelmet. A teljes rugalmasság biztosítása érdekében minden erőforrást Azure Backup védelemmel kell ellátni.
>

### <a name="supported-regions"></a>Támogatott régiók

A Soft delete jelenleg az USA nyugati középső régiójában támogatott Kelet-Ázsia, Közép-Kanada, Kelet-Kanada, Közép-Franciaország, Dél-Brazília, Korea középső régiója, Dél-Korea, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Kelet-Ausztrália, Kelet-Ausztrália, Észak-Európa, USA nyugati régiója, Nyugat-RÉGIÓJA, USA középső régiója, Dél-Kelet-Ázsia, északi középső régió, USA 2. keleti régiója, Kelet-Japán, Nyugat-India , Észak-Svájc, Nyugat-Svájc, Norvégia nyugati régiója, Kelet-Norvégia és minden nemzeti régió.

### <a name="soft-delete-for-vms-using-azure-portal"></a>Soft Delete a virtuális gépekhez Azure Portal használatával

1. Egy virtuális gép biztonsági mentési adatmennyiségének törléséhez le kell állítani a biztonsági mentést. A Azure Portal lépjen a Recovery Services-tárolóba, kattintson a jobb gombbal a biztonsági mentési elemre, és válassza a **biztonsági mentés leállítása**lehetőséget.

   ![Azure Portal biztonsági másolati elemek képernyőképe](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. A következő ablakban lehetősége lesz törölni vagy megőrizni a biztonsági mentési adatait. Ha a biztonsági másolati elemek **törlése** és a **biztonsági mentés leállítása**lehetőséget választja, a virtuális gép biztonsági mentése nem lesz véglegesen törölve. Ehelyett a rendszer 14 napig őrzi meg a biztonsági mentési adatait a helyreállítható törölt állapotban. Ha a **biztonsági mentési adatok törlése** lehetőség van kiválasztva, a rendszer az e-mailek törlésére vonatkozó riasztást küldi el a felhasználó számára, hogy 14 nap maradjon a biztonsági mentési adatok megőrzéséhez. Emellett e-mailben értesítést küldünk a 12. napon arról, hogy a törölt adat feltámasztása még két nappal megmaradt. A törlés Elhalasztva marad a 15. napon, amikor végleges törlés történik, és a rendszer a végleges e-mail riasztást küldi el az adat végleges törléséről.

   ![Képernyőkép a Azure Portalről, a biztonsági mentési képernyő leállítása](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. A 14 nap során a Recovery Services-tárolóban a helyreállított virtuális gép megjelenik egy piros "Soft-Delete" ikon mellett.

   ![Képernyőkép a Azure Portalről, a virtuális gép a Soft delete állapotában](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Ha a tárolóban nem találhatók meg a helyreállított biztonsági mentési elemek, a tár ekkor nem törölhető. A biztonsági mentési elemek végleges törlését követően próbálja meg a tár törlését, és nincs olyan elem, amely a tárolóban marad.

4. A helyreállított virtuális gép visszaállításához először törölni kell a szolgáltatást. A törlés visszavonásához válassza a Soft-Deleted VM elemet, majd válassza a **Törlés**visszavonása lehetőséget.

   ![Azure Portal képernyőkép a virtuális gép törléséről](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Megjelenik egy ablak, amely figyelmeztetést jelenít meg, ha a törlés lehetőség van kiválasztva, a virtuális gép összes visszaállítási pontja törölve lesz, és a visszaállítási művelet elvégezhető. A virtuális gép "megőrzött adatmegőrzés" állapotban marad, a biztonsági mentések felfüggesztésével és a biztonsági mentési szabályzat hatályba lépésével megőrizve.

   ![Azure Portal képernyőképe – virtuális gép törlésének megerősítése](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   Ezen a ponton a virtuális GÉPET a kiválasztott visszaállítási pontról a **virtuális gép visszaállítása** lehetőség kiválasztásával is visszaállíthatja.  

   ![Képernyőkép a Azure Portalről, virtuális gép visszaállítása lehetőség](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > A rendszer csak akkor futtatja a szemét-gyűjtőt, amikor a felhasználó elvégezte a **biztonsági mentési művelet folytatását** .

5. A törlési folyamat befejezése után az állapot vissza fog térni a "biztonsági mentés leállítása az adatmegőrzéshez" értékre, majd a **biztonsági mentés folytatása**lehetőségre kattint. A **biztonsági mentés folytatása** művelet visszaadja az aktív állapotú biztonsági mentési elemet, amely a felhasználó által a biztonsági mentési és adatmegőrzési ütemtervet definiáló biztonsági mentési házirendhez van társítva.

   ![Képernyőkép a Azure Portalről, biztonsági mentés folytatása lehetőség](./media/backup-azure-security-feature-cloud/resume-backup.png)

Ez a folyamatábra a biztonsági mentési elemek különböző lépéseit és állapotát jeleníti meg, ha a Soft delete engedélyezve van:

![A nem törölt biztonságimásolat-elemek életciklusa](./media/backup-azure-security-feature-cloud/lifecycle.png)

További információkért lásd az alábbi [Gyakori kérdések](backup-azure-security-feature-cloud.md#frequently-asked-questions) szakaszt.

### <a name="soft-delete-for-vms-using-azure-powershell"></a>Soft Delete a virtuális gépekhez Azure PowerShell használatával

> [!IMPORTANT]
> Az az. Recoveryservices szolgáltatónál verzió, amely az Azure PS használatával történő Soft-delete használatát igényli, min 2.2.0. A ```Install-Module -Name Az.RecoveryServices -Force``` legújabb verziójának beszerzéséhez használja a következőt:.

A fentiekben leírtak szerint a lépések sorrendjének Azure Portal a Azure PowerShell is.

#### <a name="delete-the-backup-item-using-azure-powershell"></a>A biztonsági mentési tétel törlése Azure PowerShell használatával

Törölje a biztonsági mentési tételt a [disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS parancsmag használatával.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

A biztonsági mentési elem "DeleteState" változása "NotDeleted" értékről "törlendőként" értékre változik. A biztonsági mentési adat 14 napig őrzi meg a rendszer. Ha vissza szeretné állítani a törlési műveletet, hajtsa végre a visszavonás – törlés műveletet.

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>A törlési művelet visszavonása a Azure PowerShell használatával

Először olvassa be a megfelelő biztonsági mentést, amely a törlésre kész állapotú.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Ezután hajtsa végre a visszavonás-törlés műveletet a [Visszavonás-AzRecoveryServicesBackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0) PS parancsmag használatával.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

A biztonsági mentési elemek "DeleteState" állapota "NotDeleted" lesz. A védelem azonban továbbra is leáll. A védelem újbóli engedélyezéséhez [folytassa a biztonsági mentést](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) .

### <a name="soft-delete-for-vms-using-rest-api"></a>Soft Delete a virtuális gépekhez REST API használatával

- Törölje a biztonsági másolatokat a REST API az [itt](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)említett módon.
- Ha a felhasználó vissza kívánja vonni ezeket a törlési műveleteket, tekintse meg az [itt](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)említett lépéseket.

## <a name="disabling-soft-delete"></a>A Soft delete letiltása

A Soft delete alapértelmezés szerint engedélyezve van az újonnan létrehozott tárolókban a biztonsági mentési adatok véletlen vagy rosszindulatú törlésből való védelme érdekében.  A funkció letiltása nem ajánlott. Ha a védett elemek új tárolóba való áthelyezését tervezi, és a törlés és az ismételt védelem (például egy tesztkörnyezetben) esetében nem várhatja el a szükséges 14 napot, az egyetlen olyan körülmény, amelyben érdemes megfontolni a Soft delete letiltását. Csak a tár tulajdonosa tilthatja le ezt a funkciót. Ha letiltja ezt a funkciót, a védett elemek minden jövőbeli törlése azonnali eltávolítást eredményez, a visszaállítási lehetőség nélkül. A szolgáltatás letiltását megelőzően a törölt állapotban lévő biztonsági mentési adatmennyiséget a rendszer 14 napig változatlanul törli. Ha véglegesen törölni kívánja ezeket a fájlokat, törölnie kell a törlést, majd újra törölnie kell őket a végleges törléshez.

### <a name="disabling-soft-delete-using-azure-portal"></a>A Soft delete letiltása a Azure Portal használatával

A Soft delete letiltásához kövesse az alábbi lépéseket:

1. A Azure Portal nyissa meg a tárolót, majd lépjen a **Beállítások** -> **Tulajdonságok menüpontra**.
2. A Tulajdonságok ablaktáblán válassza a **biztonsági beállítások** -> **frissítés**lehetőséget.  
3. A biztonsági beállítások ablaktábla **Soft delete**területén válassza a **Letiltás**lehetőséget.

![Nem kötelező törlés letiltása](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>A Soft delete letiltása a Azure PowerShell használatával

> [!IMPORTANT]
> Az az. Recoveryservices szolgáltatónál verzió, amely az Azure PS használatával történő Soft-delete használatát igényli, min 2.2.0. A ```Install-Module -Name Az.RecoveryServices -Force``` legújabb verziójának beszerzéséhez használja a következőt:.

A letiltáshoz használja a [set-AzRecoveryServicesVaultBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) PS parancsmagot.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>A Soft delete letiltása a REST API használatával

Ha REST API használatával szeretné letiltani a helyreállítható törlési funkciót, tekintse át az [itt](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)említett lépéseket.

## <a name="permanently-deleting-soft-deleted-backup-items"></a>A helyreállított törölt biztonsági másolati elemek végleges törlése

A szolgáltatás letiltását megelőzően a törölt állapotban lévő biztonsági mentési állapotok helyreállított állapotban maradnak. Ha véglegesen törölni kívánja ezeket a fájlokat, törölje a törlést, majd törölje őket a végleges törléshez.

### <a name="using-azure-portal"></a>Az Azure Portal használata

Kövesse az alábbi lépéseket:

1. A [Soft delete letiltásához](#disabling-soft-delete)kövesse a következő lépéseket:.
2. A Azure Portal nyissa meg a tárolót, lépjen a **biztonsági másolatok elemre**, és válassza a Soft Deleted VM elemet.

   ![A törölt virtuális gép kiválasztása](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Válassza a **Törlés**visszavonása lehetőséget.

   ![Törlés visszavonása](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Ekkor megjelenik egy ablak. Válassza a **Törlés**visszavonása lehetőséget.

   ![Törlés visszavonása](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Válassza a **biztonsági mentési adattörlés** lehetőséget a biztonsági másolati adatbázis végleges törléséhez.

   ![Válassza a biztonsági másolatok törlése lehetőséget.](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Írja be a biztonsági mentési tétel nevét annak megerősítéséhez, hogy törölni kívánja a helyreállítási pontokat.

   ![Írja be a biztonsági másolati tétel nevét](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. Az elemhez tartozó biztonsági másolati elemek törléséhez válassza a **Törlés**lehetőséget. Egy értesítési üzenetből megtudhatja, hogy a biztonsági mentési információ törölve lett.

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

Ha törölve lettek az elemek, mielőtt a rendszer letiltotta a törlést, akkor a rendszer a törölt állapotot fogja törölni. Ha azonnal törölni szeretné őket, a törlési műveletnek sztornírozva kell lennie, majd újra el kell végrehajtania.

Azonosítsa a helyreállított állapotban lévő elemeket.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Ezután visszafordíthatja a törlési műveletet, amelyet a rendszer a Soft delete engedélyezésekor végzett.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Mivel a rendszer letiltotta a törlést, a törlési művelet a biztonsági másolatok azonnali eltávolítását fogja eredményezni.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>A REST API használata

Ha törölve lettek az elemek, mielőtt a rendszer letiltotta a törlést, akkor a rendszer a törölt állapotot fogja törölni. Ha azonnal törölni szeretné őket, a törlési műveletnek sztornírozva kell lennie, majd újra el kell végrehajtania.

1. Először vonja vissza a törlési műveleteket az [itt](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)leírt lépésekkel.
2. Ezután tiltsa le a Soft-delete funkciót a REST API használatával az [itt](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)említett lépések végrehajtásával.
3. Ezután törölje a biztonsági mentéseket a REST API az [itt](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)említett módon.

## <a name="encryption"></a>Titkosítás

Az Azure Storage encryption használatával a rendszer automatikusan titkosítja a biztonsági másolatok adatait a felhőben, ami segít a biztonsági és megfelelőségi kötelezettségek teljesítésében. Az inaktív adatok titkosítása 256 bites AES-titkosítással történik, amely az egyik legerősebb blokk titkosítási algoritmus, és az FIPS 140-2-kompatibilis.

Amellett, hogy a titkosítás a nyugalmi állapotban van, a rendszer az összes átviteli biztonsági mentési adatát HTTPS-kapcsolaton keresztül továbbítja. Mindig az Azure gerinc hálózatán marad.

További információ: az [Azure Storage titkosítása inaktív adatokhoz](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). A titkosítással kapcsolatban esetlegesen felmerülő kérdések megválaszolásához tekintse meg a [Azure Backup gyakori kérdések](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) című témakört.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Biztonsági másolatok titkosítása a platform által felügyelt kulcsokkal

Alapértelmezés szerint az összes adatait a platform által felügyelt kulcsok használatával titkosítja a rendszer. Semmilyen explicit műveletet nem kell tennie a végponttól a titkosítás engedélyezéséhez, és az összes olyan munkaterhelésre vonatkozik, amelyről biztonsági mentés készül a Recovery Services-tárba.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Biztonsági mentési adatai titkosítása az ügyfél által felügyelt kulcsokkal

Az Azure-Virtual Machines biztonsági mentésekor mostantól titkosíthatja az adatait az Ön által birtokolt és felügyelt kulcsokkal. Azure Backup lehetővé teszi, hogy a Azure Key Vault tárolt RSA-kulcsait a biztonsági mentések titkosításához használja. A biztonsági mentések titkosításához használt titkosítási kulcs különbözhet a forráshoz használttól. Az adatait egy AES 256-alapú adattitkosítási kulccsal (ADATTITKOSÍTÁSI kulcsot) védik, amely viszont a kulcsok használatával védett. Ez teljes körű irányítást biztosít az adat és a kulcsok felett. A titkosítás engedélyezéséhez szükség van arra, hogy a Recovery Services-tároló hozzáférhessen a Azure Key Vault található titkosítási kulcshoz. Ha szükséges, letilthatja a kulcsot, vagy visszavonhatja a hozzáférést. A titkosítást azonban a kulcsok használatával kell engedélyeznie, mielőtt bármilyen elemet a tárolóhoz próbál védelemmel ellátni.

>[!NOTE]
>Ez a szolgáltatás jelenleg korlátozott rendelkezésre állású. Kérjük, töltse ki [ezt a kérdőívet](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) , AskAzureBackupTeam@microsoft.com és küldjön nekünk e-mailt, ha az ügyfél által felügyelt kulcsok használatával szeretné titkosítani a biztonsági mentési adatait. Vegye figyelembe, hogy a funkció használatának lehetősége a Azure Backup szolgáltatás jóváhagyását képezi.

### <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Az ügyfél által felügyelt kulcsok használatával titkosított felügyelt lemezes virtuális gépek biztonsági mentése

A Azure Backup lehetővé teszi az Azure-beli virtuális gépek biztonsági mentését is, amelyek a kulcsot használják a kiszolgálóoldali titkosításhoz. A lemezek titkosításához használt kulcsot a Azure Key Vault tárolja és felügyeli. Az ügyfél által felügyelt kulcsokat használó kiszolgálóoldali titkosítás különbözik a Azure Disk Encryptiontól, mivel az ADE a BitLocker (for Windows) és a DM-Crypt (for Linux) használatával végzi a vendég titkosítást, az SSE titkosítja az adatait a Storage szolgáltatásban, lehetővé téve a virtuális gépekhez tartozó operációs rendszerek vagy rendszerképek használatát. További részletekért tekintse meg a [felügyelt lemezek titkosítása az ügyfél által felügyelt kulcsokkal](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) című témakört.

### <a name="backup-of-vms-encrypted-using-ade"></a>Az ADE használatával titkosított virtuális gépek biztonsági mentése

A Azure Backup segítségével biztonsági mentést készíthet az Azure-beli virtuális gépekről, amelyek az operációs rendszer vagy a Azure Disk Encryption használatával titkosított adatlemezekkel rendelkeznek. Az ADE a BitLockert használja a Windows rendszerű virtuális gépekhez és a Linux rendszerű virtuális gépekhez készült DM-Crypt titkosítást. Részletekért lásd: [a titkosított virtuális gépek biztonsági mentése és visszaállítása Azure Backupokkal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="private-endpoints"></a>Privát végpontok

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="other-security-features"></a>Egyéb biztonsági funkciók

### <a name="protection-of-azure-backup-recovery-points"></a>Azure Backup helyreállítási pontok védelme

A Recovery Services-tárolók által használt Storage-fiókok el vannak különítve, és a felhasználók nem férhetnek hozzá semmilyen kártékony célra. A hozzáférés csak Azure Backup felügyeleti műveleteken, például a visszaállításon keresztül engedélyezett. Ezeket a kezelési műveleteket szerepköralapú Access Controlon (RBAC) keresztül vezérli a rendszer.

További információ: [szerepköralapú Access Control használata Azure Backup helyreállítási pontok kezeléséhez](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="for-soft-delete"></a>A Soft delete

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Engedélyezni kell a Soft-delete szolgáltatást minden tárolón?

Nem, alapértelmezés szerint a Recovery Services-tárolók számára van kiépítve és engedélyezve.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Megadhatom, hogy a törlési művelet befejezése után a rendszer hány napig őrizze meg az adataimat a törölt állapotban?

Nem, a törlési művelet után a további 14 napos további megőrzési idő rögzített.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Fizetnem kell a további 14 napos adatmegőrzési díjat?

Nem, ez a 14 napos további megőrzés a Soft-delete funkció részeként díjmentes.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Végezhetek visszaállítási műveletet, ha az adatok helyreállítható törlési állapotban vannak?

Nem, vissza kell állítania a nem törölt erőforrás törlését a visszaállításhoz. A törlési művelet visszaállítja az erőforrást a **stop Protection szolgáltatásba, és megőrzi az adatállapotot,** ahol bármikor visszaállíthatja azokat. Ebben az állapotban a szemetet gyűjtő szüneteltetve marad.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>A pillanatképek ugyanazt az életciklust követik, mint a tároló helyreállítási pontjai?

Igen.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Hogyan indíthatom újra az ütemezett biztonsági mentéseket egy helyreállított erőforráshoz?

Törlés után a folytatási művelet ismét védi az erőforrást. A folytatási művelet társít egy biztonsági mentési szabályzatot az ütemezett biztonsági mentések a kiválasztott megőrzési időtartammal való elindításához. Emellett a szemetet gyűjtő is fut, amint a folytatási művelet befejeződik. Ha olyan helyreállítási pontról szeretne visszaállítást végezni, amelyik lejárt a lejárati dátumnál, azt javasoljuk, hogy a folytatási művelet elindítása előtt végezze el a helyreállítást.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Törölhetem a tárolót, ha a tárolóban nem találhatók a törölt elemek?

A Recovery Services-tároló nem törölhető, ha a tárolóban található helyreállítható állapotú biztonsági másolati elemek vannak. A törölt elemek a törlési művelet után 14 nappal véglegesen törlődnek. Ha nem tud megvárni 14 napig, tiltsa le a helyreállítható [törlést](#disabling-soft-delete), törölje a nem törölt elemek törlését, majd törölje újra a véglegesen a törléshez. Miután meggyőződött róla, hogy nincsenek védett elemek, és nem találhatók a törölt elemek, a tároló törölhető.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Törölhetem a 14 napnál régebbi adattörlési időszakot a törlés után?

Nem. Nem kényszerítheti a nem törölt elemek törlését, ezért a rendszer 14 nap elteltével automatikusan törli őket. Ez a biztonsági funkció lehetővé teszi az adatok véletlen vagy rosszindulatú törlésből való védelmét.  A virtuális gépen bármilyen más művelet végrehajtása előtt várjon 14 napot.  A rendszer felszámítja a nem kötelezően törölt elemek díját.  Ha az új tárolóra vonatkozó 14 napon belül a helyreállítható törlésre kijelölt virtuális gépeket újra kell védetté tenni, forduljon a Microsoft ügyfélszolgálatához.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Végrehajthatók a helyreállítható törlési műveletek a PowerShellben vagy a parancssori felületen?

A helyreállítható törlési műveletek a [PowerShell](#soft-delete-for-vms-using-azure-powershell)használatával végezhetők el. A CLI jelenleg nem támogatott.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>A Soft delete támogatott más felhőalapú számítási feladatokhoz, például az Azure-beli virtuális gépeken SQL Server és az Azure-beli virtuális gépeken SAP HANA?

Nem. Jelenleg az Azure Virtual Machines szolgáltatásban csak a Soft delete használata támogatott.

## <a name="next-steps"></a>További lépések

- További információ a [Azure Backup biztonsági vezérlőinek](backup-security-controls.md).
