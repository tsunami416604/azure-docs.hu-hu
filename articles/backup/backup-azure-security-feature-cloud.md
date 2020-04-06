---
title: Biztonsági funkciók a felhőbeli számítási feladatok védelméhez
description: Ismerje meg, hogyan használhatja a biztonsági funkciókat az Azure Backup ban a biztonsági mentések biztonságosabbá tétele érdekében.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: bd7c86e18114513a264a0f9252589533fb7ff2d3
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668741"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Az Azure Backup ot használó felhőalapú számítási feladatok védelmére szolgáló biztonsági funkciók

A biztonsági problémákkal kapcsolatos aggodalmak, például a rosszindulatú programok, a zsarolóprogramok és a behatolás oka egyre nő. Ezek a biztonsági kérdések költségesek lehetnek mind a pénz, mind az adatok tekintetében. Az ilyen támadások elleni védelem érdekében az Azure Backup most antól biztonsági funkciókat biztosít a biztonsági mentési adatok védelme érdekében még a törlés után is.

Az egyik ilyen funkció a lágy törlés. A helyreállítható törlés, még akkor is, ha egy rosszindulatú szereplő törli a virtuális gép biztonsági másolatát (vagy a biztonsági mentési adatok véletlenül törlődnek), a biztonsági mentési adatok további 14 napig megmaradnak, így a biztonsági mentési elem helyreállítása adatvesztés nélkül. A biztonsági mentési adatok "ideiglenes törlés" állapotban való további 14 napos megőrzése nem jár semmilyen költséggel az ügyfél számára. Az Azure is titkosítja az összes up-up adatok at in-tároló segítségével Storage Service Encryption az adatok további védelme [érdekében.](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

Az Azure virtuális gépek ideiglenes törléselleni védelme általánosan elérhető.

>[!NOTE]
>Az Azure Virtuális gépben az SQL-kiszolgáló előzetes törlése már előzetes verzióban érhető el az SAP HANA ideiglenes törlése az Azure virtuális gép számítási feladatában.<br>
>Az előnézetre való feliratkozáshoz írjon nekünk aAskAzureBackupTeam@microsoft.com

## <a name="soft-delete"></a>Helyreállítható törlés

### <a name="soft-delete-for-vms"></a>Helyreállítható törlés a virtuális gépekhez

A virtuális gépek helyreállítható törlése megvédi a virtuális gépek biztonsági másolatait a nem kívánt törléstől. A biztonsági mentések törlése után is további 14 napig helyreállítható törlési állapotban maradnak.

> [!NOTE]
> A helyreállítható törlés csak a törölt biztonsági mentési adatokat védi. Ha egy virtuális gép biztonsági mentés nélkül törlődik, a helyreállítható törlés funkció nem őrzi meg az adatokat. A teljes rugalmasság biztosítása érdekében az Azure Backup minden erőforrást védeni kell.
>

### <a name="supported-regions"></a>Támogatott régiók

Soft törlés jelenleg támogatott a Nyugat-Közép-USA, Kelet-Ázsia, Kanada Közép-, Kanada Kelet-, Franciaország Közép-, Franciaország Déli, Korea Közép-, Korea Dél- és Egyesült Királyság Déli, Egyesült Királyság Nyugati, Ausztrália Kelet- , Ausztrália Délkelet, Észak-Európa, USA nyugati, USA nyugati régiója, Usa középső régiója, Délkelet-Ázsia, Észak-Közép-USA, Usa déli középső régiója, Japán-kelet, Japán-kelet, Japán Nyugati, India Déli, India Közép-, India Nyugat-, USA keleti régiója 2. , Svájc Észak-, Svájc Nyugati, Norvégia Nyugati, Norvégia Kelet és az összes nemzeti régió.

### <a name="soft-delete-for-vms-using-azure-portal"></a>Az Azure Portal használatával a virtuális gépek ideiglenes törlése

1. A virtuális gép biztonsági mentési adatainak törléséhez a biztonsági mentést le kell állítani. Az Azure Portalon nyissa meg a helyreállítási szolgáltatások tárolóját, kattintson a jobb gombbal a biztonsági mentési elemre, és válassza a **Biztonsági mentés leállítása parancsot.**

   ![Képernyőkép az Azure Portal biztonsági mentési elemeiről](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. A következő ablakban választhat, hogy törli vagy megőrzi a biztonsági mentési adatokat. Ha a **Biztonsági másolat törlése,** majd a Biztonsági mentés leállítása lehetőséget **választja,** a virtuális gép biztonsági mentése nem törlődik véglegesen. Ehelyett a biztonsági mentési adatok 14 napig törlődnek a helyreállíthatóan törölt állapotban. Ha **a Biztonsági másolat adatainak törlése** lehetőséget választja, a rendszer törlési e-mail értesítést küld a konfigurált e-mail-azonosítónak, amely tájékoztatja a felhasználót arról, hogy 14 nap van hátra a biztonsági mentési adatok kiterjesztett megőrzéséből. Emellett a 12. napon e-mailben értesítést küldünk arról, hogy még két nap van hátra a törölt adatok feltámasztására. A törlést elhalasztják a 15.

   ![Képernyőkép az Azure Portalról, a Biztonsági másolat leállítása képernyőről](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Ez alatt a 14 nap alatt a Recovery Services Vaultban a helyreállíthatóan törölt virtuális gép piros "helyreállítható törlés" ikonnal jelenik meg.

   ![Képernyőkép az Azure Portalról, a virtuális gép helyreállítható törlési állapotban](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Ha a tárolóban vannak helyreállíthatóbiztonsági mentési elemek, a tároló nem törölhető abban az időben. Próbálkozzon a tároló törlésével, miután a biztonsági mentési elemek véglegesen törlődnek, és nincs elem a tárolóban maradt helyreállíthatóan törölt állapotban.

4. A helyreállítható virtuális gép visszaállításához először törölni kell. A törlés megszüntetéséhez válassza a helyreállítható virtuális gép kiválasztását, majd a **Törlés megszüntetése**lehetőséget.

   ![Képernyőkép az Azure Portalról, a Virtuális gép törlésének törlése](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Egy ablak jelenik meg, amely figyelmezteti, hogy ha a törlés visszatörlése van kiválasztva, a virtuális gép összes visszaállítási pontja törlődik, és elérhető a visszaállítási művelet végrehajtásához. A virtuális gép "stop protection with retain data" állapotban marad, a biztonsági mentések szüneteltetve és a biztonsági mentési adatok örökre megmaradnak, és nincs biztonsági mentési házirend hatékony.

   ![Képernyőkép az Azure Portalról, a Virtuális gép törlésének megerősítése](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   Ezen a ponton is visszaállíthatja a virtuális gép kiválasztásával **virtuális gép visszaállítása** a kiválasztott visszaállítási pont.  

   ![Képernyőkép az Azure Portalról, a Virtuálisgép visszaállítása beállításról](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > A szemétgyűjtő csak akkor fut és tisztítja meg a lejárt helyreállítási pontokat, ha a felhasználó végrehajtja a **Tartalék mentés folytatása** műveletet.

5. A törlési folyamat törlése után az állapot visszatér a "Biztonsági mentés leállítása az adatok megőrzésével" üzenetbe, majd **választhatja a Biztonsági mentés folytatása**lehetőséget. A **Biztonsági mentés folytatása** művelet a biztonsági mentési elemet aktív állapotban hozza vissza, amely a biztonsági mentési és megőrzési ütemezéseket meghatározó felhasználó által kiválasztott biztonsági mentési házirendhez van társítva.

   ![Képernyőkép az Azure Portalról, a Biztonsági mentés folytatása lehetőségről](./media/backup-azure-security-feature-cloud/resume-backup.png)

Ez a folyamatábra a biztonsági másolat elemének különböző lépéseit és állapotait mutatja, ha a biztonsági másolat engedélyezése engedélyezve van:

![A helyreállítható biztonsági másolat eleméletciklusa](./media/backup-azure-security-feature-cloud/lifecycle.png)

További információt az alábbi [Gyakori kérdések](backup-azure-security-feature-cloud.md#frequently-asked-questions) című részben talál.

### <a name="soft-delete-for-vms-using-azure-powershell"></a>Az Azure PowerShell használatával a virtuális gépek ideiglenes törlése

> [!IMPORTANT]
> Az Azure PS használatával a helyreállítható törléshez szükséges Az.RecoveryServices verzióm 2.2.0. Használja ```Install-Module -Name Az.RecoveryServices -Force``` a legújabb verzió beszerezhető.

Az Azure Portalhoz a fentiekben ismertetett módon a lépések sorrendje is megegyezik az Azure PowerShell használata közben.

#### <a name="delete-the-backup-item-using-azure-powershell"></a>A biztonsági másolat elemének törlése az Azure PowerShell használatával

Törölje a biztonsági mentési elemet az [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS parancsmag használatával.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

A biztonsági másolat elemének "DeleteState" fájlja "NotDeleted" -ről "ToBeDeleted" -re változik. A biztonsági mentési adatok 14 napig maradnak meg. Ha vissza szeretné állítani a törlési műveletet, akkor a visszavonás-törlést végre kell hajtani.

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>A törlési művelet visszavonása az Azure PowerShell használatával

Először olvassa be a megfelelő biztonsági másolat elemet, amely helyreállítható törlési állapotban van (azaz hamarosan törölni fog).

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Ezután hajtsa végre a visszavonás-törlésműveletet az [Undo-AzRecoveryServicesBackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0) PS parancsmag használatával.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

A biztonsági másolat elemének "DeleteState" visszaáll "NotDeleted" állapotra. De a védelem még mindig levan állítva. [Folytassa a biztonsági mentést](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) a védelem újbóli engedélyezéséhez.

### <a name="soft-delete-for-vms-using-rest-api"></a>Helyreállítható törlés a REST API-t használó virtuális gépekhez

- Törölje a biztonsági másolatokat a REST API használatával, ahogy [itt](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)említettük.
- Ha a felhasználó vissza kívánja vonni ezeket a törlési műveleteket, olvassa el [az itt](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)említett lépéseket.

## <a name="disabling-soft-delete"></a>A helyreállítható törlés letiltása

A helyreállítható törlés alapértelmezés szerint engedélyezve van az újonnan létrehozott tárolókban, hogy megvédje a biztonsági mentési adatokat a véletlen vagy rosszindulatú törlésekkel szemben.  A szolgáltatás letiltása nem ajánlott. Az egyetlen olyan körülmény, amikor érdemes megfontolni a helyreállítható törlés letiltását, ha a védett elemeket egy új tárolóba szeretné átállítani, és nem tudja megvárni a törlés és az újbóli védelem előtti 14 napot (például tesztkörnyezetben).) Csak a tároló tulajdonosa letilthatja ezt a funkciót. Ha letiltja ezt a funkciót, a védett elemek minden jövőbeli törlése azonnali eltávolítást eredményez, anélkül, hogy vissza tudná állítani. A szolgáltatás letiltása előtt helyreállíthatóan törölt állapotban lévő biztonsági mentési adatok 14 napig helyreállíthatóan törölt állapotban maradnak. Ha szeretné véglegesen törölni ezeket azonnal, akkor meg kell törölni, és törölje őket újra, hogy véglegesen törlődik.

### <a name="disabling-soft-delete-using-azure-portal"></a>A helyreállítható törlés letiltása az Azure Portal használatával

A helyreállítható törlés letiltásához hajtsa végre az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a trezort, majd nyissa meg a **Beállítások** -> **tulajdonságai lehetőséget.**
2. A tulajdonságok ablaktáblán válassza a **Biztonsági beállítások** -> **frissítése lehetőséget.**  
3. A Biztonsági beállítások ablaktáblán a **Helyreállítható törlés**csoportban válassza **a Letiltás**lehetőséget.

![A helyreállítható törlés letiltása](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>A helyreállítható törlés letiltása az Azure PowerShell használatával

> [!IMPORTANT]
> Az Azure PS használatával a helyreállítható törléshez szükséges Az.RecoveryServices verzióm 2.2.0. Használja ```Install-Module -Name Az.RecoveryServices -Force``` a legújabb verzió beszerezhető.

A letiltáshoz használja a [Set-AzRecoveryServicesVaultBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) PS parancsmamot.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>A helyreállítható törlés letiltása a REST API használatával

A REST API-val a helyreállítható törlésfunkció letiltásához olvassa el az [itt](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)említett lépéseket.

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Helyreállíthatóan törölt biztonsági másolat elemeinek végleges törlése

A helyreállíthatóan törölt állapotban lévő biztonsági mentési adatok a funkció letiltása előtt helyreállíthatóan törölt állapotban maradnak. Ha szeretné véglegesen törölni ezeket azonnal, majd törölje, és törölje őket újra, hogy véglegesen törlődik.

### <a name="using-azure-portal"></a>Az Azure Portal használata

Kövesse az alábbi lépéseket:

1. A [helyreállítható törlés letiltásához](#disabling-soft-delete)kövesse a lépéseket.
2. Az Azure Portalon nyissa meg a tárolót, nyissa meg a **Biztonsági mentési elemek ,** és válassza ki a helyreállíthatóan törölt virtuális gép.

   ![Helyreállíthatóan törölt virtuális gép kiválasztása](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Válassza a **Törlés megszüntetése**lehetőséget.

   ![Törlés törlése](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Megjelenik egy ablak. Válassza **a Törlés megszüntetése**lehetőséget.

   ![Törlés megszüntetése kijelölése](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. A biztonsági másolat adatainak végleges törléséhez válassza a **Biztonsági másolat adatainak törlése** lehetőséget.

   ![Biztonsági másolat adatainak törlése](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Írja be a biztonsági másolat elemének nevét, és erősítse meg, hogy törölni szeretné a helyreállítási pontokat.

   ![Írja be a biztonsági másolat elemének nevét](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. Az elem biztonsági mentési adatainak törléséhez válassza a **Törlés**lehetőséget. Egy értesítési üzenet jelzi, hogy a biztonsági mentési adatok at törölték.

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

Ha az elemeket a helyreállítható törlés letiltása előtt törölték, akkor azok helyreállíthatóan törölt állapotban lesznek. A törlésük azonnali törléséhez a törlési műveletet meg kell fordítani, majd újra végre kell hajtani.

Azonosítsa a helyreállíthatóan törölt elemeket.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Ezután fordítsa meg a törlési műveletet, amelyet a helyreállítható törlés engedélyezésekor hajtott végre.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Mivel a helyreállítható törlés most le van tiltva, a törlési művelet a biztonsági mentési adatok azonnali eltávolítását eredményezi.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>A REST API használata

Ha az elemeket a helyreállítható törlés letiltása előtt törölték, akkor azok helyreállíthatóan törölt állapotban lesznek. A törlésük azonnali törléséhez a törlési műveletet meg kell fordítani, majd újra végre kell hajtani.

1. Először vonja vissza a törlési műveleteket az [itt](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)említett lépésekkel.
2. Ezután tiltsa le a soft-delete funkciót a REST API használatával az [itt](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)említett lépések segítségével.
3. Ezután törölje a biztonsági másolatokat a REST API használatával, ahogy [itt](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)említettük .

## <a name="encryption"></a>Titkosítás

Az összes biztonsági másolatot készít, ha a felhőben tárolva tárolja az Azure Storage titkosítást, amely segít a biztonsági és megfelelőségi kötelezettségek teljesítésében. Ezek az inaktív adatok 256 bites AES titkosítással vannak titkosítva, amely az egyik legerősebb blokktitkosítás, és fips 140-2 szabványnak megfelelő.

Az inaktív titkosítás mellett az átvitel alatt álló összes biztonsági mentési adat ÁTVITELE HTTPS-en keresztül történik. Mindig az Azure gerinchálózatán marad.

További információ: [Azure Storage titkosítás a nyugalmi adatok.](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) Tekintse meg az [Azure Backup gyakori kérdések](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) a titkosítással kapcsolatos esetleges kérdések megválaszolása.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>A biztonsági mentési adatok titkosítása platformáltal kezelt kulcsok használatával

Alapértelmezés szerint az összes adat platformáltal kezelt kulccsal van titkosítva. A titkosítás engedélyezéséhez nem kell semmilyen explicit műveletet végrehajtania a célból, és ez minden olyan számítási feladatra vonatkozik, amelyről a Recovery Services-tárolóba készül biztonsági másolatot.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Biztonsági másolat adatainak titkosítása ügyfél által kezelt kulcsokkal

Az Azure virtuális gépek biztonsági mentésekor most már titkosíthatja az adatokat az Ön tulajdonában lévő és felügyelt kulcsok használatával. Az Azure Backup lehetővé teszi, hogy az Azure Key Vaultban tárolt RSA-kulcsokat használja a biztonsági mentések titkosításához. A biztonsági mentések titkosításához használt titkosítási kulcs eltérhet a forráshoz használttól. Az adatok egy AES 256 alapú adattitkosítási kulccsal (DEK) védettek, amely viszont a kulcsokkal védett. Ez teljes ellenőrzést biztosít az adatok és a kulcsok felett. A titkosítás engedélyezéséhez szükséges, hogy a Recovery Services-tároló hozzáférést kapjon az Azure Key Vault titkosítási kulcsához. Szükség esetén letilthatja a kulcsot, vagy visszavonhatja a hozzáférést. Azonban engedélyeznie kell a titkosítást a kulcsok használatával, mielőtt megkísérli a tároló elemeinek védelmét.

>[!NOTE]
>Ez a funkció jelenleg korlátozott annektál. Kérjük, töltse ki ezt AskAzureBackupTeam@microsoft.com a [felmérést,](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) és írjon nekünk, ha szeretné titkosítani a biztonsági mentési adatokat az ügyfél által kezelt kulcsok. Vegye figyelembe, hogy a funkció használatának lehetőségét az Azure Backup szolgáltatás jóváhagyása szükséges.

### <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Ügyfél által kezelt kulcsokkal titkosított felügyelt lemezes virtuális gépek biztonsági mentése

Az Azure Backup lehetővé teszi az Azure-beli virtuális gépek biztonsági mentését is, amelyek a kulcsot a kiszolgálóoldali titkosításhoz használják. A lemezek titkosításához használt kulcs az Azure Key Vault ban tárolódik, és az Ön által kezelt. Az ügyfél által felügyelt kulcsokat használó kiszolgálóoldali titkosítás eltér az Azure lemeztitkosítástól, mivel az ADE a BitLocker (Windows) és a DM-Crypt (Linux) használatával történik a vendégen lévő titkosítás végrehajtásához, az SSE titkosítja az adatokat a tárolószolgáltatásban, lehetővé téve a virtuális gépek operációs rendszerének vagy lemezképeinek használatát. További részletekért olvassa el [a felügyelt lemezek titkosítását az ügyfél által felügyelt kulcsokkal.](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys)

### <a name="backup-of-vms-encrypted-using-ade"></a>Az ADE használatával titkosított virtuális gépek biztonsági mentése

Az Azure Backup segítségével is biztonsági másolatot készíthet az Azure virtuális gépeiről, amelyek operációs rendszerüket vagy adatlemezeiket az Azure Disk Encryption használatával titkosítva használják. Az ADE a BitLocker for Windows virtuális gépek és a DM-Crypt for Linux virtuális gépek segítségével végzi a vendégtitkosítást. További információt a [Titkosított virtuális gépek biztonsági mentése és visszaállítása az Azure Backup segítségével.](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)

## <a name="private-endpoints"></a>Privát végpontok

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="other-security-features"></a>Egyéb biztonsági funkciók

### <a name="protection-of-azure-backup-recovery-points"></a>Az Azure Biztonsági mentés helyreállítási pontjainak védelme

A helyreállítási szolgáltatások tárolói által használt tárfiókok elkülönítve vannak, és a felhasználók rosszindulatú célokra nem férhetnek hozzá. A hozzáférés csak az Azure Backup felügyeleti műveletek, például a visszaállítás keresztül engedélyezett. Ezeket a felügyeleti műveleteket a szerepköralapú hozzáférés-vezérlés (RBAC) vezérli.

További információt a [Szerepköralapú hozzáférés-vezérlés használata az Azure Backup helyreállítási pontok kezeléséhez](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)című témakörben talál.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="for-soft-delete"></a>Ideiglenes törlés esetén

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Minden trezorban engedélyezni kell a helyreállítható törlés funkciót?

Nem, alapértelmezés szerint az összes helyreállítási szolgáltatás tárolóihoz beépített és engedélyezett.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Konfigurálhatom, hogy az adataim hány napig lesznek helyreállíthatóan törölt állapotban a törlési művelet befejezése után?

Nem, a törlési művelet után 14 napos további megőrzési szintre van rögzítve.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Meg kell fizetnem a további 14 napos visszatartás költségeit?

Nem, ez a 14 napos további megőrzés ingyenes, a soft-delete funkció részeként.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Végrehajthatok egy visszaállítási műveletet, ha az adataim helyreállítható törlési állapotban vannak?

Nem, a visszaállításhoz vissza kell törölnie a helyreállíthatóan törölt erőforrást. A törlés törlése művelet visszaállítja az erőforrást a **Stop védelembe, és megőrzi az adatok állapotát,** ahol bármikor visszaállítható. A szemétgyűjtő ebben az állapotban szünetel.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>A pillanatképek ugyanazt az életciklust követik, mint a helyreállítási pontok a tárolóban?

Igen.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Hogyan aktiválhatom újra az ütemezett biztonsági mentéseket egy helyreállíthatóan törölt erőforráshoz?

A törlés, majd a folytatási művelet ismételt védelme tévő elemével ismét megvédheti az erőforrást. Folytatásművelet társít egy biztonsági mentési szabályzatot az ütemezett biztonsági mentések aktiválásához a kiválasztott megőrzési időszakkal. Is, a szemétgyűjtő fut, amint a folytatási művelet befejeződik. Ha olyan helyreállítási pontról szeretne visszaállítani, amely már túl van a lejárati dátumon, azt javasoljuk, hogy tegye meg a folytatási művelet végrehajtása előtt.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Törölhetem a trezoromat, ha a tárolóban vannak helyreállíthatóan törölt elemek?

A Recovery Services-tároló nem törölhető, ha a biztonsági mentési elemek helyreállítható állapotban vannak a tárolóban. A helyreállíthatóan törölt elemek véglegesen törlődnek 14 nappal a törlési művelet után. Ha nem tud várni 14 napot, akkor tiltsa le a [helyreállítható törlést,](#disabling-soft-delete)törölje a helyreállíthatóan törölt elemeket, és törölje őket újra a végleges törléshez. Miután meggyőződött arról, hogy nincsenek védett elemek és helyreállíthatóan törölt elemek, a tároló törölhető.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Törölhetem az adatokat a törlés utáni 14 napos törlési időszaknál korábban?

Nem. Nem kényszerítheti a helyreállíthatóan törölt elemek törlését, a rendszer 14 nap elteltével automatikusan törli őket. Ez a biztonsági funkció lehetővé teszi a biztonsági másolatok adatainak védelmét a véletlen vagy rosszindulatú törlésekkel szemben.  Várjon 14 napot, mielőtt bármilyen más műveletet hajt végre a virtuális számítógépen.  A helyreállíthatóan törölt elemek et felszámítják.  Ha 14 napon belül újra kell védenie a helyreállítható törlésre jelölt virtuális gépeket egy új tárolóba, forduljon a Microsoft támogatási szolgálatához.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>A powershellben vagy a CLI-ben elvégezhető a helyreállítható törlési műveletek?

A soft delete műveletek a [PowerShell](#soft-delete-for-vms-using-azure-powershell)használatával hajthatók végre. Jelenleg a CLI nem támogatott.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>A helyreállítható törlés más felhőbeli számítási feladatok, például az Azure-beli virtuális gépeksql Server és az Azure-beli SAP HANA esetében támogatott?

Nem. Jelenleg a helyreállítható törlés csak az Azure virtuális gépek támogatott.

## <a name="next-steps"></a>További lépések

- Olvassa el [az Azure Backup biztonsági vezérlőinek olvasását.](backup-security-controls.md)
