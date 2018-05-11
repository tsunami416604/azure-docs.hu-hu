---
title: Biztonsági mentése és visszaállítása a titkosított virtuális gépek Azure biztonsági mentéssel
description: Ez a cikk a virtuális gépek Azure Disk Encryption használatával titkosítja a biztonsági mentési és visszaállítási élmény beszél.
services: backup
documentationcenter: ''
author: JPallavi
manager: vijayts
editor: ''
ms.assetid: 8387f186-7d7b-400a-8fc3-88a85403ea63
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/13/2017
ms.author: pajosh;markgal;trinadhk; sogup
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c788720e046c2efef954ef77f7b52854439b7515
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Biztonsági mentése és visszaállítása az Azure Backup szolgáltatással titkosított virtuális gépek
Ez a cikk beszél biztonsági mentése és visszaállítása a virtuális gépek (VM) Azure biztonsági mentéssel lépéseit. Azt is hibák esetén támogatott forgatókönyveket, előfeltételeket és hibaelhárítási lépésekkel kapcsolatos részletes adatokat biztosít.

## <a name="supported-scenarios"></a>Támogatott esetek

 * Biztonsági mentés és visszaállítás titkosított virtuális gépek közül csak az Azure Resource Manager telepítési modellt használó gépekhez támogatott. A klasszikus üzembe helyezési modellt használó virtuális gépek esetén nem támogatott. <br>
 * Biztonsági mentés és visszaállítás titkosított virtuális gépek Windows és Linux Azure Disk Encryption használó virtuális gépek esetén támogatott. Lemeztitkosítás használja az iparági szabványos BitLocker a Windows és a dm-crypt Linux lemezek titkosítás biztosítása érdekében. <br>
 
 Az alábbi táblázat a BitLocker titkosítási kulcsot (BEK) – csak a és a kulcs titkosítási kulcs (KEK) - támogatott forgatókönyveit titkosított virtuális gépek:
 
 
   |  | BEK + KEK virtuális gépek | Csak BEK virtuális gépek |
   | --- | --- | --- |
   | **Nem felügyelt virtuális gépek**  | Igen | Igen  |
   | **Felügyelt virtuális gépek**  | Igen | Igen  |

## <a name="prerequisites"></a>Előfeltételek
* A virtuális gép által a használatával titkosított [Azure Disk Encryption](../security/azure-security-disk-encryption.md).

* Recovery Services-tároló lett létrehozva, ezért tárolási replikációs lett beállítva a lépések [előkészítse a környezetét a biztonsági mentés](backup-azure-arm-vms-prepare.md).

* Biztonsági mentés adott [kulcstároló hozzáférési engedélye a](#provide-permissions-to-backup) tartalmazó kulcsok és a titkos virtuális gépek titkosítva.

## <a name="backup-encrypted-vm"></a>Biztonsági mentés titkosított virtuális gép
Az alábbi lépések segítségével állítsa be a biztonsági mentés célja, adja meg a házirendet, elemek konfigurálása és a biztonsági mentés.

### <a name="configure-backup"></a>Biztonsági mentés konfigurálása
1. Ha már rendelkezik nyissa meg a Recovery Services-tároló, folytassa a következő lépéssel. Ha nem rendelkezik nyissa meg a Recovery Services-tároló, de van az Azure portálon, válassza a **minden szolgáltatás**.

   a. Az erőforrások listájába írja be a következőt: **Recovery Services**.

   b. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Amikor látja **Recovery Services-tárolók**, válassza ki azt.

      ![Recovery Services-tároló](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

    c. A Recovery Services-tárolók listája megjelenik. Válassza ki a listából egy tárolót.

     Megnyílik a kiválasztott tároló irányítópultja.
2. Elemek, hogy megjelenik-e a tárolóban, válassza ki a listáról **biztonsági mentés** a titkosított virtuális gép biztonsági másolatának indításához.

      ![Biztonsági mentési panel](./media/backup-azure-vms-encryption/select-backup.png)
3. Az a **biztonsági mentés** csempe, jelölje be **biztonsági mentési cél**.

      ![A forgatókönyv panel](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
4. A **a számítási feladatok futtató?**, jelölje be **Azure**. A **miről szeretne biztonsági másolatot készíteni?**, jelölje be **virtuális gép**. Ezután kattintson az **OK** gombra.

   ![Forgatókönyv panel megnyitása](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
5. A **válassza ki a biztonsági mentési házirend**, válassza ki a biztonsági mentési házirendet kíván alkalmazni a tárolóba. Ezután kattintson az **OK** gombra.

      ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    Az alapértelmezett házirend részleteinek vannak felsorolva. Ha azt szeretné, hozzon létre egy házirendet, válassza ki a **hozzon létre új** a legördülő listából. Miután kiválasztotta a **OK**, a biztonsági mentési házirend társítva a tárolóban.

6. Válassza ki a megadott házirend társítani, és válassza ki a titkosított virtuális gépek **OK**.

      ![Válassza ki a titkosított virtuális gépek](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. Ezen a lapon látható a kijelölt titkosított virtuális gépek társított kulcstárolójának üzenete. Biztonsági mentés a kulcsok és titkos kulcsokat a key vaultban olvasási hozzáférésre van szüksége. Ezeket az engedélyeket használ a kulcsok és titkos kulcsok, valamint a társított virtuális gépek biztonsági mentése.<br>
Ha Ön egy **felhasználói**, biztonsági mentés engedélyezése folyamat fog zökkenőmentesen hozzáférést szerezni a a key vault a biztonsági másolat virtuális gépek titkosítva, felhasználói beavatkozás nélkül.

   ![Titkosított virtuális gépek üzenet](./media/backup-azure-vms-encryption/member-user-encrypted-vm-warning-message.png)

   Az egy **Vendég felhasználó**, meg kell adnia az engedélyeket a biztonsági mentési szolgáltatás biztonsági másolatok működéséhez a kulcstároló eléréséhez. Megadhatja azokat az engedélyeket a következő a [a következő szakaszban leírt lépéseket](#provide-permissions-to-backup)

   ![Titkosított virtuális gépek üzenet](./media/backup-azure-vms-encryption/guest-user-encrypted-vm-warning-message.png)
 
    Most, hogy a tároló összes beállítást adta meg, válassza ki a **biztonsági mentés engedélyezése** az oldal alján. **Biztonsági mentés engedélyezése** telepíti a szabályzatot a tároló és a virtuális gépeket.
  
8. A következő fázis előkészítésekor a Virtuálisgép-ügynök telepítését végzi, és meggyőződött arról, hogy a Virtuálisgép-ügynök telepítve van. Ehhez, kövesse a lépéseket [előkészítse a környezetét a biztonsági mentés](backup-azure-arm-vms-prepare.md).

### <a name="trigger-a-backup-job"></a>A biztonsági mentési feladatot indít
Kövesse a [biztonsági mentési Azure virtuális gépek a Recovery Services-tároló](backup-azure-arm-vms.md) a biztonsági mentési feladatot indít.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Már biztonsági másolat virtuális gépek biztonsági másolatainak folytassa a titkosítás engedélyezése  
Ha már biztonsági mentés alatt a Recovery Services-tároló virtuális gépek, később titkosítás engedélyezett, biztonsági mentés biztonsági mentéshez továbbra is a kulcstároló elérésére engedélyt kell adnia. Megadhatja azokat az engedélyeket a következő a [a következő részben ismertetett lépések](#provide-permissions-to-azure-backup). Vagy a "Biztonságimásolat-készítés engedélyezése" című szakaszban a PowerShell eljárás követésével a [PowerShell dokumentációs](backup-azure-vms-automation.md). 

## <a name="provide-permissions-to-backup"></a>Engedélyek biztosítása a biztonsági mentés
Az alábbi lépések segítségével vonatkozó engedélyek biztosítása a kulcstároló eléréséhez, és biztonsági másolatot készít a titkosított virtuális gépek biztonsági mentése.
1. Válassza ki **minden szolgáltatás**, keresse meg a **tárolók kulcs**.

    ![Kulcstartók](./media/backup-azure-vms-encryption/search-key-vault.png)
    
2. A kulcstárolók listában jelölje ki a titkosított virtuális gép, amelyet a biztonsági mentésre társított kulcstároló.

     ![Kulcstároló kiválasztása](./media/backup-azure-vms-encryption/select-key-vault.png)
     
3. Válassza ki **hozzáférési házirendek**, majd válassza ki **új hozzáadása**.

    ![Új hozzáadása](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)
    
4. Válassza ki **válassza egyszerű**, majd írja be **biztonsági mentési szolgáltatás** be a keresőmezőbe. 

    ![A biztonsági mentési szolgáltatás keresése](./media/backup-azure-vms-encryption/search-backup-service.png)
    
5. Válassza ki **biztonsági másolat szolgáltatás**, majd válassza ki **válasszon**.

    ![A biztonsági mentési szolgáltatás kiválasztása](./media/backup-azure-vms-encryption/select-backup-service.png)
    
6. A **konfigurálása (nem kötelező) sablonból**, jelölje be **Azure biztonsági mentés**. A szükséges engedélyek vannak előre kitöltött a **engedélyek kulcs** és **titkos engedélyek**. Ha a virtuális gép használatával titkosított **BEK csak**, csak a titkos kulcsok engedélyek szükségesek, ezért el kell távolítania a telepítendő **engedélyek kulcs**.

    ![Az Azure biztonsági mentési kiválasztása](./media/backup-azure-vms-encryption/select-backup-template.png)
    
7. Kattintson az **OK** gombra. Figyelje meg, hogy **biztonsági másolat szolgáltatás** bekerül a **hozzáférési házirendek**. 

    ![Hozzáférési szabályzatok](./media/backup-azure-vms-encryption/backup-service-access-policy.png)
    
8. Válassza ki **mentése** ahhoz, hogy megkapja a szükséges engedélyekkel a biztonsági mentés.

    ![Biztonsági mentési házirend](./media/backup-azure-vms-encryption/save-access-policy.png)

Miután sikeresen biztosított engedélyeket, folytathatja a titkosított virtuális gépek biztonsági mentés engedélyezése.

## <a name="restore-an-encrypted-vm"></a>Állítsa vissza egy titkosított virtuális Gépre
Egy titkosított virtuális gép visszaállítására, a "Biztonsági másolat lemezek visszaállítása" című szakaszában ismertetett lépések szerint először állítsa vissza a lemezek [válasszon egy virtuális gép visszaállítási konfigurációt](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Ezután használhatja az alábbi lehetőségek közül:

* A PowerShell a lépések végrehajtásával [hozzon létre egy virtuális Gépet visszaállított lemezekből](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) visszaállított lemezekből teljes virtuális gép létrehozása.
* Vagy, [a sablonok segítségével testre szabhatja a visszaállított virtuális Géphez](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm) visszaállított lemezekből virtuális gépek létrehozásához. Sablonok csak 2017. április 26. után létrehozott helyreállítási pontok használható.

## <a name="troubleshooting-errors"></a>Hibaelhárítás
| Művelet | Hiba részletei | Megoldás: |
| --- | --- | --- |
|Backup | Biztonsági mentés nem rendelkezik megfelelő engedélyekkel a key vault a biztonsági mentéshez titkosított virtuális gépek. | Biztonsági mentés kell biztosítani az engedélyek a következő a [az előző szakaszban ismertetett visszaállítási lépésekkel](#provide-permissions-to-azure-backup). Vagy a PowerShell dokumentációjában, a "Engedélyezni a védelmet" szakaszában a PowerShell lépéseket követheti [használata AzureRM.RecoveryServices.Backup parancsmagok segítségével készítsen biztonsági másolatot a virtuális gépek](backup-azure-vms-automation.md#back-up-azure-vms). |  
| Visszaállítás |Titkosított virtuális gép nem állítható vissza, mert a virtuális gép társított kulcstároló nem létezik. |Hozzon létre egy kulcstartót használatával [Ismerkedés az Azure Key Vault](../key-vault/key-vault-get-started.md). Lásd: [egy kulcstartót kulcsot és titkos kulcs visszaállítása Azure Backup használatával](backup-azure-restore-key-secret.md) kulcsok és titkos kulcs visszaállítása, ha nem található. |
| Visszaállítás |Titkosított virtuális gép nem állítható vissza, mert a kulcs és a virtuális gép társított titkos kulcs nem létezik. |Lásd: [egy kulcstartót kulcsot és titkos kulcs visszaállítása Azure Backup használatával](backup-azure-restore-key-secret.md) kulcsok és titkos kulcs visszaállítása, ha nem található. |
| Visszaállítás |Biztonsági mentés nem rendelkezik az engedélyt az előfizetésében szereplő erőforrások eléréséhez. |Ahogy korábban említettük, visszaállíthatja az lemezek először a "Biztonsági másolat lemezek visszaállítása" című szakaszában ismertetett lépések [válasszon egy virtuális gép visszaállítási konfigurációt](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Ezt követően használja a PowerShell használatával [hozzon létre egy virtuális Gépet visszaállított lemezekből](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |
