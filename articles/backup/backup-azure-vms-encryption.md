---
title: Titkosított Azure-virtuális gépek biztonsági és visszaállítása
description: Bemutatja, hogyan lehet biztonsági másolatot készíteni és visszaállítani a titkosított Azure-beli virtuális gépeket az Azure Backup szolgáltatással.
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 98febe9f91cb4b71d546300d4e65ade073d19e67
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461769"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Titkosított Azure-virtuális gép biztonsági és visszaállítása

Ez a cikk ismerteti, hogyan biztonsági másolatot készíthet és állíthat vissza Windows vagy Linux Azure virtuális gépek (VM-ek) titkosított lemezekkel az [Azure Backup](backup-overview.md) szolgáltatás használatával.

Ha többet szeretne megtudni arról, hogy az Azure Backup hogyan lép kapcsolatba az Azure virtuális gépekkel a kezdés előtt, tekintse át az alábbi erőforrásokat:

- [Tekintse át](backup-architecture.md#architecture-built-in-azure-vm-backup) az Azure virtuális gép biztonsági mentési architektúráját.
- [További információ a](backup-azure-vms-introduction.md) Az Azure Virtuálisgép biztonsági mentése és az Azure Backup bővítmény.

## <a name="encryption-support"></a>Titkosítás támogatása

Az Azure Backup támogatja az Azure virtuális gépek biztonsági mentését, amelyek operációsrendszer-/adatlemezeit Azure Disk Encryption (ADE) titkosítással titkosítják. Az ADE a BitLocker-t használja a Windows virtuális gépek titkosításához, és a Linux-virtuális gépek dm-crypt szolgáltatását. Az ADE integrálható az Azure Key Vaultszolgáltatással a lemeztitkosítási kulcsok és titkos kulcsok kezeléséhez. Key Vault key encryption keys (KEKs) segítségével egy további biztonsági réteget, titkosítja a titkosítási titkos kulcsokat, mielőtt a Key Vault ba írnák őket.

Az Azure Backup az Alábbi táblázatban összefoglalva biztonsági másolatot készíthet és visszaállíthat Az Azure-beli virtuális gépeket az ADE használatával az Azure AD alkalmazással és anélkül.

**Virtuális merevlemez típusa** | **ADE (BEK/dm-crypt)** | **ADE és KEK**
--- | --- | ---
**Nem felügyelt** | Igen | Igen
**Kezelt**  | Igen | Igen

- További információ az [ADE,](../security/azure-security-disk-encryption-overview.md) [a Key Vault](../key-vault/general/overview.md)és a [KEK-k](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-key-vault#set-up-a-key-encryption-key-kek)ről.
- Olvassa el az Azure VM lemeztitkosítással [kapcsolatos gyakori kérdéseket.](../security/azure-security-disk-encryption-faq.md)

### <a name="limitations"></a>Korlátozások

- Biztonsági másolatot és visszaállítást a titkosított virtuális gépek ugyanabban az előfizetésben és régióban.
- Az Azure Backup támogatja az önálló kulcsok használatával titkosított virtuális gépeket. A virtuális gép titkosításához használt tanúsítvány részét képezi bármely kulcs jelenleg nem támogatott.
- Biztonsági másolatot készíthet és visszaállíthat titkosított virtuális gépeket ugyanazon az előfizetésen és régión belül, mint a Recovery Services biztonsági másolattárolója.
- A titkosított virtuális gépek nem helyreállnak a fájl/mappa szintjén. A fájlok és mappák visszaállításához vissza kell állítania a teljes virtuális gépét.
- Virtuális gép visszaállításakor nem [használhatja](backup-azure-arm-restore-vms.md#restore-options) a meglévő virtuális gép cseréje beállítást a titkosított virtuális gépekhez. Ez a beállítás csak titkosítatlan felügyelt lemezek esetén támogatott.

## <a name="before-you-start"></a>Előkészületek

Mielőtt elkezdené, tegye a következőket:

1. Győződjön meg arról, hogy egy vagy több [Windows](../security/azure-security-disk-encryption-windows.md) vagy [Linux](../virtual-machines/linux/disk-encryption-overview.md) rendszerű, ADE-kompatibilis virtuális gépe i.
2. Tekintse át az Azure VM biztonsági [mentésének támogatási mátrixát](backup-support-matrix-iaas.md)
3. [Hozzon létre](backup-azure-arm-vms-prepare.md#create-a-vault) egy helyreállítási szolgáltatások biztonsági mentési tárolóját, ha nem rendelkezik ilyensel.
4. Ha engedélyezi a titkosítást a biztonsági mentéshez már engedélyezett virtuális gépek számára, egyszerűen meg kell adnia a biztonsági mentést a Key Vault eléréséhez szükséges engedélyekkel, hogy a biztonsági mentések megszakítás nélkül folytatódhassanak. [További információ](#provide-permissions) az engedélyek hozzárendeléséről.

Ezen kívül van néhány dolog, amit szükség lehet tennie bizonyos körülmények között:

- **Telepítse a virtuálisgép-ügynököt a virtuális gépre:** Az Azure Backup biztonsági másolatot készít az Azure virtuális gépekről azáltal, hogy egy bővítményt telepít a gépen futó Azure virtuálisgép-ügynökre. Ha a virtuális gép egy Azure piactéri lemezképből lett létrehozva, az ügynök telepítve van és fut. Ha egyéni virtuális gépet hoz létre, vagy áttelepít egy helyszíni gépet, előfordulhat, hogy manuálisan kell [telepítenie az ügynököt.](backup-azure-arm-vms-prepare.md#install-the-vm-agent)

## <a name="configure-a-backup-policy"></a>Biztonságimentési házirend konfigurálása

1. Ha még nem hozott létre helyreállítási szolgáltatások biztonsági tárolóját, kövesse [az alábbi utasításokat](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Nyissa meg a tárolót a portálon, és válassza a **Biztonsági mentés** lehetőséget az **Első lépések** szakaszban.

    ![Biztonsági másolat panelje](./media/backup-azure-vms-encryption/select-backup.png)

3. A **Biztonsági mentés i célja** > **Hol fut a számítási feladatok?** válassza az **Azure**lehetőséget.
4. A **Mit szeretne biztonsági másolatot tenni?** válassza a Virtuális **gép** > **OK**lehetőséget.

      ![Forgatókönyv panel](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. A **Biztonsági másolat házirend** > válassza ki a**biztonsági mentési házirendet,** válassza ki a tárolóhoz társítani kívánt házirendet. Ezt követően kattintson az **OK** gombra.
    - A biztonsági mentési házirend határozza meg, hogy mikor és mennyi ideig tárolja őket a biztonsági mentések.
    - Az alapértelmezett házirend részletei megtalálhatók a legördülő menüben.

    ![Forgatókönyv panel megnyitása](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Ha nem szeretné használni az alapértelmezett házirendet, válassza az **Új létrehozása**lehetőséget, és [hozzon létre egy egyéni házirendet.](backup-azure-arm-vms-prepare.md#create-a-custom-policy)

7. Válassza ki azokat a titkosított virtuális gépeket, amelyekről biztonsági másolatot szeretne tartani a kiválasztott házirend használatával, és válassza az **OK gombot.**

      ![Titkosított virtuális gépek kijelölése](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Ha az Azure Key Vault, a vault lapon megjelenik egy üzenet, amely szerint az Azure Backup szüksége van csak olvasási hozzáférést a kulcsokhoz és a kulcsok a Key Vaultban.

    - Ha ez az üzenet jelenik meg, nincs szükség műveletre.

        ![Hozzáférés – OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Ha ez az üzenet jelenik meg, az [alábbi eljárásban](#provide-permissions)leírtak szerint kell beállítania az engedélyeket.

        ![Hozzáférési figyelmeztetés](./media/backup-azure-vms-encryption/access-warning.png)

9. Kattintson a **Biztonsági mentés engedélyezése** gombra a biztonsági mentési házirend telepítéséhez a tárolóban, és engedélyezze a biztonsági mentést a kijelölt virtuális gépekhez.

## <a name="trigger-a-backup-job"></a>Biztonsági mentési feladat aktiválása

A kezdeti biztonsági mentés az ütemezésnek megfelelően fog futni, de azonnal futtathatja az alábbiak szerint:

1. A tároló menüjében kattintson a **Biztonsági másolat parancsra.**
2. A **Biztonsági másolat elemei csoportban**kattintson az Azure virtuális **gép**elemre.
3. A **Biztonsági másolat elemei listában** kattintson a három pontra (...).
4. Kattintson **a Biztonsági mentés gombra.**
5. A **Biztonsági másolat most**eszközben a naptár vezérlővel jelölje ki azt az utolsó napot, amikor a helyreállítási pontot meg kell őrizni. Ezt követően kattintson az **OK** gombra.
6. A portál értesítéseinek figyelése. A feladat előrehaladását a tároló irányítópultján figyelheti > folyamatban lévő **biztonsági mentési feladatok** > **.** A virtuális gép méretétől függően a kezdeti biztonsági mentés létrehozása hosszabb időt vehet igénybe.

## <a name="provide-permissions"></a>Engedélyek megadása

Az Azure virtuális gépnek csak olvasható hozzáférésre van szüksége a kulcsok és a titkos kulcsok, valamint a társított virtuális gépek biztonsági mentén.

- A Key Vault az Azure-előfizetés Azure AD-bérlőhöz van társítva. Ha **Ön tagfelhasználó,** az Azure Backup további művelet nélkül szerez hozzáférést a Key Vaulthoz.
- Ha **Vendég felhasználó,** engedélyt kell adnia az Azure Backup számára a key vault eléréséhez.

Engedélyek beállítása:

1. Az Azure Portalon válassza a **Minden szolgáltatás**lehetőséget, és keresse meg a **kulcstartókat.**
2. Válassza ki a titkosított virtuális géphez társított kulcstartót, amelyről biztonsági másolatot készít.
3. Válassza **az Access-házirendek** > **Új hozzáadása lehetőséget.**
4. Válassza **az Egyszerű kijelölés lehetőséget,** majd írja be a **Biztonságimásolat-kezelés parancsot.**
5. Válassza a **Biztonságimásolat-kezelési szolgáltatás** > **kiválasztása lehetőséget.**

    ![A biztonsági mentési szolgáltatás kiválasztása](./media/backup-azure-vms-encryption/select-backup-service.png)

6. A **Hozzáférési házirend** > **konfigurálása sablonból (nem kötelező)** csoportban válassza az **Azure Backup lehetőséget.**
    - A **kulcsengedélyekhez** és titkos **engedélyekhez**szükséges engedélyek et előre kitöltik.
    - Ha a virtuális gép csak **BEK**használatával van titkosítva, távolítsa el a **kulcsengedélyek** kiválasztását, mivel csak a titkos kulcsokhoz szükséges engedélyekre van szüksége.

    ![Az Azure biztonsági másolatának kiválasztása](./media/backup-azure-vms-encryption/select-backup-template.png)

7. Kattintson az **OK** gombra. **A Biztonságimásolat-kezelési szolgáltatás** hozzáadódik az **Access-házirendekhez.**

    ![Hozzáférési szabályzatok](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

8. Kattintson a **Mentés** gombra, ha az Azure Backup számára meg szeretné adni az engedélyeket.

## <a name="restore-an-encrypted-vm"></a>Titkosított virtuális gép visszaállítása

A titkosított virtuális gépeket az alábbiak szerint állítja vissza:

1. [Állítsa vissza a virtuális gép lemezét](backup-azure-arm-restore-vms.md#restore-disks).
2. Hozza létre újra a virtuálisgép-példányt az alábbi módon:
    1. Használja a sablont, amely a visszaállítási művelet során létrehozott a virtuális gép beállításainak testreszabásához, és a virtuális gép központi telepítésének aktiválásához. [További információ](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    2. Hozzon létre egy új virtuális gép a visszaállított lemezek powershell használatával. [További információ](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
3. Linuxos virtuális gépek esetén telepítse újra az ADE-bővítményt, hogy az adatlemezek megnyílnak és csatlakoztatva vannak.

## <a name="next-steps"></a>További lépések

Ha bármilyen probléma merül fel, tekintse át az alábbi cikkeket:

- [Gyakori hibák](backup-azure-vms-troubleshoot.md) a titkosított Azure-beli virtuális gépek biztonsági mentésekor és visszaállításakor.
- [Az Azure Virtuálisgép-ügynök/biztonsági mentési bővítmény](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) problémái.
