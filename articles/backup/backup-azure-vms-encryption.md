---
title: Biztonsági mentése és visszaállítása titkosított virtuális gépek az Azure Backup használatával
description: Ez a cikk ismerteti az Azure Disk Encryption használatával titkosított virtuális gépek biztonsági mentése és visszaállítása élményét.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 7/10/2018
ms.author: sogup
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a3a059e8edc286b2c1433c9b414dc275a433e2fd
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217679"
---
# <a name="back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Biztonsági mentése és visszaállítása titkosított virtuális gépek az Azure Backup szolgáltatással
Ez a cikk ismerteti a lépéseket, biztonsági mentése és visszaállítása a virtuális gépek (VM) az Azure Backup használatával. A következő többi hibaeseteket támogatott forgatókönyveket, előfeltételeket és hibaelhárítási lépéseket részletesen is ismerteti.

## <a name="supported-scenarios"></a>Támogatott esetek

 Biztonsági mentés és visszaállítás titkosított virtuális gépek csak az Azure Resource Manager-alapú üzemi modellt használó virtuális gépek esetében támogatott. A klasszikus üzemi modellt használó virtuális gépek esetében nem támogatott. Biztonsági mentés és visszaállítás titkosított virtuális gépek Windows és Linux rendszerű virtuális gépek, amelyek az Azure Disk Encryption használata támogatott. Lemeztitkosítás használ az iparági szabványos BitLocker funkcióját Windows és Linux dm-crypt funkcióját lemezek titkosítása. Az alábbi táblázat bemutatja a titkosítási típus és a virtuális gépek számára.

   |  | Rendelkeznek BEk-KEL és KEK-beli virtuális gépek | Csak rendelkeznek BEk-KEL virtuális gépek |
   | --- | --- | --- |
   | **Nem felügyelt virtuális gépek**  | Igen | Igen  |
   | **Felügyelt virtuális gépek**  | Igen | Igen  |

   > [!NOTE]
   > Az Azure Backup támogatja a különálló kulcsával használatával titkosított virtuális gépeket. Bármelyik billentyűt, amely része a virtuális gép titkosításához használt tanúsítványt jelenleg nem támogatott.
   >   

## <a name="prerequisites"></a>Előfeltételek
* A virtuális gép használatával titkosított [az Azure Disk Encryption](../security/azure-security-disk-encryption.md).

* Recovery Services-tároló lett létrehozva, és a storage-replikációt állított leírt lépések végrehajtásával [előkészítik a környezetét a biztonsági mentés](backup-azure-arm-vms-prepare.md).

* A megadott biztonsági mentési [engedélyeket az eléréséhez a key vault](#provide-permissions-to-backup) tartalmazó kulcsokat és titkos kulcsokat, a titkosított virtuális gépek.

## <a name="backup-encrypted-vm"></a>Biztonsági mentés titkosított virtuális gépek
Az alábbi lépések segítségével állítsa be a biztonsági mentés célját, meghatározhat egy olyan szabályzatot, elemek konfigurálása és indítson egy biztonsági mentést.

### <a name="configure-backup"></a>Biztonsági mentés konfigurálása
1. Ha már rendelkezik nyissa meg a Recovery Services-tároló, folytassa a következő lépéssel. Ha nem rendelkezik nyissa meg a Recovery Services-tároló, de az Azure Portalon, válassza a **minden szolgáltatás**.

   a. Az erőforrások listájába írja be a következőt: **Recovery Services**.

   b. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Amikor látja **Recovery Services-tárolók**, jelölje ki azt.

      ![Recovery Services-tároló](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

    c. A Recovery Services-tárolók listája megjelenik. Válasszon egy tárolót a listában.

     Megnyílik a kiválasztott tároló irányítópultja.
1. Elemek listájában, amely megjelenik a tárolóban, válassza ki a **Backup** , a titkosított virtuális gép biztonsági mentésének megkezdéséhez.

      ![Biztonsági mentés panel](./media/backup-azure-vms-encryption/select-backup.png)
1. Az a **Backup** csempéről válassza **biztonsági mentés célja**.

      ![Forgatókönyv panel](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
1. A **a számítási feladat futtató?** válassza **Azure**. A **miről szeretne biztonsági másolatot készíteni?** válassza **virtuális gép**. Ezután kattintson az **OK** gombra.

   ![Forgatókönyv panel megnyitása](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
1. A **biztonsági mentési házirend kiválasztása**, válassza ki a tároló esetén alkalmazni szeretné a biztonsági mentési szabályzat. Ezután kattintson az **OK** gombra.

      ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    Az alapértelmezett házirend részletei jelennek meg. Ha azt szeretné, hozzon létre egy házirendet, válassza ki a **hozzon létre új** a legördülő listából. Miután kiválasztotta **OK**, a biztonsági mentési szabályzat rendelve a tárolóhoz.

1. Válassza ki a megadott házirendhez hozzárendelni, és válassza ki a titkosított virtuális gépek **OK**.

      ![Válassza ki a titkosított virtuális gépekhez](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
1. Ezen a lapon látható a kulcstartókhoz a titkosított virtuális gépek kiválasztott társított szóló üzenetet. Biztonsági mentés a kulcsok és titkos kulcsokat a key vault a csak olvasási hozzáférésre van szüksége. Ezeket az engedélyeket használ a kulcsok és titkos kulcsokat, valamint a kapcsolódó virtuális gépek biztonsági mentése.<br>
Ha Ön egy **tag felhasználói**, biztonsági mentés engedélyezése folyamat zökkenőmentesen fogják beszerezni a key vaulthoz való hozzáférés biztonsági mentés titkosított virtuális gépek felhasználói beavatkozás nélkül.

   ![Titkosított virtuális gépek üzenet](./media/backup-azure-vms-encryption/member-user-encrypted-vm-warning-message.png)

   Az egy **vendégfelhasználó**, meg kell adnia az engedélyeket a biztonsági mentési szolgáltatás hozzáférjen a kulcstartóhoz működéséhez a biztonsági mentésekhez. Megadhatja azokat az engedélyeket az alábbi a [a következő szakaszban leírt lépéseket](#provide-permissions-to-backup)

   ![Titkosított virtuális gépek üzenet](./media/backup-azure-vms-encryption/guest-user-encrypted-vm-warning-message.png)

    Most, hogy a tároló minden beállítást megadott, válassza ki a **biztonsági mentés engedélyezése** az oldal alján. **Biztonsági mentés engedélyezése** telepítheti a szabályzatot a tároló és a virtuális gépek számára.

1. A következő fázis előkészítése a Virtuálisgép-ügynököt telepít, és gondoskodik róla, hogy a Virtuálisgép-ügynök telepítve van. Ugyanez a teendője, kövesse a lépéseket a [előkészítik a környezetét a biztonsági mentés](backup-azure-arm-vms-prepare.md).

### <a name="trigger-a-backup-job"></a>Biztonsági mentési feladat aktiválása
Kövesse a [biztonsági mentési Azure virtuális gépek Recovery Services-tároló](backup-azure-arm-vms.md) egy biztonsági mentési feladat aktiválásához.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Már készíteni virtuális gépek biztonsági mentései folytatásához titkosítás engedélyezve  
Ha már folyamatban van biztonsági mentése Recovery Services-tároló virtuális gépek, később titkosítás engedélyezett, biztonsági mentés, biztonsági mentések továbbra is a key vault eléréséhez engedélyeket kell megadnia. Megadhatja azokat az engedélyeket az alábbi a [a következő szakaszban található lépések](#provide-permissions-to-azure-backup). Vagy a PowerShell a "Biztonsági mentés engedélyezése" című szakaszának lépéseit kövesse a [PowerShell-dokumentáció](backup-azure-vms-automation.md).

## <a name="provide-permissions-to-azure-backup"></a>Adja meg az engedélyeket a biztonsági mentés
Az alábbi lépések segítségével adja meg a megfelelő engedélyekkel a kulcstartó elérését, és végezze el a titkosított virtuális gépek biztonsági mentését a biztonsági mentés.
1. Válassza ki **minden szolgáltatás**, és keressen rá a **tárolók kulcs**.

    ![Kulcstartók](./media/backup-azure-vms-encryption/search-key-vault.png)

1. Jelölje ki a titkosított virtuális Gépet, amely lehet készíteni társított kulcstartó kulcstartók listája.

     ![A Key vault kiválasztása](./media/backup-azure-vms-encryption/select-key-vault.png)

1. Válassza ki **hozzáférési házirendek**, majd válassza ki **új hozzáadása**.

    ![Új hozzáadása](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)

1. Válassza ki **válassza egyszerű**, majd írja be **biztonsági felügyeleti szolgáltatás** kifejezést a keresőmezőbe.

    ![Keresés a biztonsági mentési szolgáltatás](./media/backup-azure-vms-encryption/search-backup-service.png)

1. Válassza ki **biztonsági felügyeleti szolgáltatás**, majd válassza ki **kiválasztása**.

    ![A biztonsági mentési szolgáltatás kiválasztása](./media/backup-azure-vms-encryption/select-backup-service.png)

1. Alatt **konfigurálása (nem kötelező) sablonból**válassza **Azure Backup**. A szükséges engedélyek vannak előre kitöltött a **Kulcsengedélyek** és **titkos kód engedélyei**. Ha a virtuális gép használatával titkosítja a **rendelkeznek BEk-KEL csak**, csak a titkos kulcsokhoz szükség, ezért el kell távolítania az adatgyűjtésre vonatkozó felhasználói **Kulcsengedélyek**.

    ![Az Azure biztonsági mentés kiválasztása](./media/backup-azure-vms-encryption/select-backup-template.png)

1. Kattintson az **OK** gombra. Figyelje meg, hogy **biztonsági felügyeleti szolgáltatás** bekerül a **hozzáférési házirendek**.

    ![Hozzáférési szabályzatok](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

1. Válassza ki **mentése** a szükséges engedélyeket adhat a biztonsági mentés.

    ![Biztonsági mentési házirend](./media/backup-azure-vms-encryption/save-access-policy.png)

Miután sikeresen biztosított engedélyeket, folytathatja a biztonsági mentés engedélyezése a titkosított virtuális gépek számára.

## <a name="restore-an-encrypted-vm"></a>Egy titkosított virtuális gép visszaállítása
Az Azure Backup mostantól támogatja a visszaállítás [Azure titkosított virtuális gép az Azure AD nélkül](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-prerequisites-aad) mellett az előző visszaállítási elérhető támogatás az Azure-bA titkosított virtuális Gépet az Azure ad-ben.<br>

Egy titkosított virtuális gép visszaállításához a "Biztonsági másolat lemezek visszaállítása" című szakaszának lépéseit követve először állítsa vissza a lemezeket [válasszon egy Virtuálisgép-konfiguráció visszaállítása](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Ezt követően az alábbi lehetőségek egyikét használhatja:

* Kövesse a lépéseket a PowerShell [hozzon létre egy virtuális gép helyreállított lemezekből](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) teljes virtuális gép létrehozása a helyreállított lemezek alapján.
* Másik lehetőségként [a visszaállított virtuális gép testreszabása sablonok használatával](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm) hozhat létre virtuális gépeket a helyreállított lemezek alapján. Sablonok csak 2017. április 26. után létrehozott helyreállítási pontokat is használható.

## <a name="troubleshooting-errors"></a>Hibaelhárítás
| Művelet | A hiba részletei | Megoldás: |
| --- | --- | --- |
|Backup | Hibakód: UserErrorKeyVaultPermissionsNotConfigured<br><br>Hibaüzenet: Az Azure Backup szolgáltatás nem rendelkezik megfelelő engedélyekkel a Key Vault a biztonsági mentés a titkosított virtuális gépek. | Biztonsági mentés meg kell adni ezeket az engedélyeket az alábbi a [az előző szakaszban szereplő lépések](#provide-permissions-to-azure-backup). Vagy a PowerShell a cikk a "Védelem engedélyezése" című szakaszának lépéseit kövesse [PowerShell-lel történő biztonsági mentése és visszaállítása a virtuális gépek](backup-azure-vms-automation.md#enable-protection). |  
| Visszaállítás | A titkosított virtuális gép nem állítható vissza, mert a virtuális Géphez társított kulcstartó nem létezik. |Hozzon létre egy kulcstartót használatával [első lépései az Azure Key Vault](../key-vault/key-vault-get-started.md). Lásd: [a key vault-kulcs és titkos kulcs visszaállítása az Azure Backup használatával](backup-azure-restore-key-secret.md) egy kulcsot és titkos kulcs visszaállítása, ha azok nincsenek jelen. |
| Visszaállítás | Hibakód: UserErrorKeyVaultKeyDoesNotExist<br><br> Hibaüzenet: A titkosított virtuális gép nem állítható vissza, mert a virtuális Géphez társított kulcs nem létezik. |Lásd: [a key vault-kulcs és titkos kulcs visszaállítása az Azure Backup használatával](backup-azure-restore-key-secret.md) egy kulcsot és titkos kulcs visszaállítása, ha azok nincsenek jelen. |
| Visszaállítás | Hibakód: ProviderAuthorizationFailed/UserErrorProviderAuthorizationFailed<br><br>Hibaüzenet: A Backup szolgáltatás nem jogosult az Ön előfizetésében biztosított erőforrások elérésére. |Ahogy korábban említettük a lemezek visszaállítása először a "Biztonsági másolat lemezek visszaállítása" című szakaszának lépéseit követve [válasszon egy Virtuálisgép-konfiguráció visszaállítása](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Ezt követően használja a PowerShell-lel [hozzon létre egy virtuális gép helyreállított lemezekből](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |
