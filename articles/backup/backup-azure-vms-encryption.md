---
title: Titkosított Azure-beli virtuális gépek biztonsági mentése és visszaállítása
description: A titkosított Azure-beli virtuális gépek biztonsági mentését és visszaállítását ismerteti a Azure Backup szolgáltatással.
ms.topic: conceptual
ms.date: 07/29/2020
ms.openlocfilehash: a5c12f9f9177c4495a82ced2b3c7d0c5edcdd78e
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2020
ms.locfileid: "88262789"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Titkosított Azure-beli virtuális gép biztonsági mentése és visszaállítása

Ez a cikk azt ismerteti, hogyan lehet biztonsági mentést készíteni és visszaállítani a titkosított lemezekkel rendelkező Windows-vagy Linux-alapú virtuális gépeket a [Azure Backup](backup-overview.md) szolgáltatás használatával.

Ha többet szeretne megtudni arról, hogy a Azure Backup hogyan kommunikál az Azure-beli virtuális gépekkel az első lépések megkezdése előtt, tekintse át ezeket az erőforrásokat:

- [Tekintse át](backup-architecture.md#architecture-built-in-azure-vm-backup) az Azure virtuális gép biztonsági mentési architektúráját.
- [További](backup-azure-vms-introduction.md) információ Az Azure virtuális gép biztonsági mentése és a Azure Backup bővítmény.

## <a name="encryption-support"></a>Titkosítás támogatása

A Azure Backup támogatja az olyan Azure-beli virtuális gépek biztonsági mentését, amelyeknek az operációs rendszer/adatlemezei Azure Disk Encryption (ADE) titkosítással rendelkeznek. Az ADE a BitLockert használja a Windows rendszerű virtuális gépek titkosításához és a Linux rendszerű virtuális gépekhez készült dm-crypt szolgáltatáshoz. Az ADE integrálható Azure Key Vault a lemezes titkosítási kulcsok és titkos kódok kezelésére. Key Vault kulcs titkosítási kulcsa (KEK) egy további biztonsági réteg hozzáadására használható, amely titkosítja a titkosítási titkokat, mielőtt beírja őket a Key Vaultba.

Az Azure-beli virtuális gépek biztonsági mentését és visszaállítását Azure Backup az ADE használatával, az Azure AD-alkalmazás nélkül, az alábbi táblázatban összefoglalt módon végezheti el.

**Virtuális merevlemez típusa** | **ADE (BEK/dm-crypt)** | **ADE és KEK**
--- | --- | ---
**Nem felügyelt** | Igen | Igen
**Felügyelt**  | Igen | Igen

- További információ az [ade](../security/fundamentals/azure-disk-encryption-vms-vmss.md), a [Key Vault](../key-vault/general/overview.md)és a [KEK](../virtual-machine-scale-sets/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek).
- Olvassa el az Azure-beli virtuális gép lemezének titkosításával kapcsolatos [gyakori kérdéseket](../security/fundamentals/azure-disk-encryption-vms-vmss.md) .

### <a name="limitations"></a>Korlátozások

- A titkosított virtuális gépek biztonsági mentését és visszaállítását ugyanazon az előfizetésen és régión belül végezheti el.
- Azure Backup a különálló kulcsokkal titkosított virtuális gépeket támogatja. A virtuális gépek titkosításához használt tanúsítvány részét képező egyik kulcs jelenleg nem támogatott.
- A titkosított virtuális gépek biztonsági mentését és visszaállítását a Recovery Services backup-tárolóval megegyező előfizetésben és régióban végezheti el.
- A titkosított virtuális gépek nem állíthatók helyre a fájl/mappa szintjén. A fájlok és mappák visszaállításához helyre kell állítania a teljes virtuális gépet.
- Egy virtuális gép visszaállításakor nem használhatja a [meglévő virtuális gép cseréje](backup-azure-arm-restore-vms.md#restore-options) beállítást a titkosított virtuális gépekhez. Ez a beállítás csak titkosítatlan felügyelt lemezek esetén támogatott.

## <a name="before-you-start"></a>Előkészületek

Mielőtt elkezdené, tegye a következőket:

1. Győződjön meg arról, hogy van egy vagy több Windows vagy [Linux](../virtual-machines/linux/disk-encryption-overview.md) [rendszerű](../virtual-machines/linux/disk-encryption-overview.md) virtuális gép, amelyen az ade engedélyezve van.
2. [Tekintse át az](backup-support-matrix-iaas.md) Azure virtuális gép biztonsági mentésének támogatási mátrixát
3. Ha nem rendelkezik ilyennel, [hozzon létre](backup-create-rs-vault.md) egy Recovery Services backup-tárolót.
4. Ha engedélyezi a titkosítást olyan virtuális gépek számára, amelyek már engedélyezve vannak a biztonsági mentéshez, egyszerűen biztonsági mentést kell biztosítania a Key Vault eléréséhez, hogy a biztonsági mentések megszakítás nélkül is folytatódnak. [További](#provide-permissions) információ az engedélyek hozzárendeléséről.

Emellett van néhány dolog, amit bizonyos esetekben szükség lehet:

- **Telepítse a virtuálisgép-ügynököt a virtuális gépre**: Azure Backup biztonsági mentést készít az Azure-beli virtuális gépekről a számítógépen futó Azure VM-ügynök bővítményének telepítésével. Ha a virtuális gép Azure Piactéri rendszerképből lett létrehozva, akkor az ügynök telepítve van és fut. Ha egyéni virtuális gépet hoz létre, vagy egy helyszíni gépet telepít át, előfordulhat, hogy [manuálisan kell telepítenie az ügynököt](backup-azure-arm-vms-prepare.md#install-the-vm-agent).

## <a name="configure-a-backup-policy"></a>Biztonsági mentési szabályzat konfigurálása

1. Ha még nem hozott létre Recovery Services backup-tárolót, kövesse [az alábbi utasításokat](backup-create-rs-vault.md).
1. Nyissa meg a tárolót a portálon, és válassza a **+ biztonsági mentés** lehetőséget az **Áttekintés** szakaszban.

    ![Biztonsági mentés ablaktábla](./media/backup-azure-vms-encryption/select-backup.png)

1. A **biztonsági mentés célja**, hogy  >  **hol fut a munkaterhelés?** válassza az **Azure**lehetőséget.
1. A **Miről szeretne biztonsági másolatot készíteni?** válassza a **virtuális gép**lehetőséget. Ezután válassza a **biztonsági mentés**lehetőséget.

      ![Forgatókönyv panel](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

1. A **biztonsági mentési házirendben**  >  **válassza a biztonsági mentési házirend**elemet, és válassza ki a tárolóhoz társítandó szabályzatot. Ez után válassza az **OK** gombot.
    - A biztonsági mentési szabályzat meghatározza, hogy mikor készüljön biztonsági mentés, és mennyi ideig tárolja őket a rendszer.
    - Az alapértelmezett házirend részletei megtalálhatók a legördülő menüben.

    ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

1. Ha nem szeretné használni az alapértelmezett házirendet, válassza az **új létrehozása**lehetőséget, és [hozzon létre egy egyéni házirendet](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

1. A **Virtual Machines**területen válassza a **Hozzáadás**lehetőséget.

    ![Virtuális gépek hozzáadása](./media/backup-azure-vms-encryption/add-virtual-machines.png)

1. Válassza ki azokat a titkosított virtuális gépeket, amelyekről biztonsági másolatot szeretne készíteni a Select Policy használatával, majd kattintson az **OK gombra**.

      ![Titkosított virtuális gépek kiválasztása](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

1. Ha Azure Key Vault használ, a tároló lapon egy üzenet jelenik meg, amely szerint a Azure Backup csak olvasási hozzáféréssel kell rendelkeznie a Key Vault kulcsaihoz és titkaihoz.

    - Ha ezt az üzenetet kapja, nincs szükség beavatkozásra.

        ![Hozzáférés OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Ha ezt az üzenetet kapja, be kell állítania az engedélyeket az [alábbi eljárásban](#provide-permissions)leírtak szerint.

        ![Hozzáférési figyelmeztetés](./media/backup-azure-vms-encryption/access-warning.png)

1. A biztonsági mentés **engedélyezése** lehetőségre kattintva telepítheti a biztonsági mentési szabályzatot a tárolóban, és engedélyezheti a kiválasztott virtuális gépek biztonsági mentését.

## <a name="trigger-a-backup-job"></a>Biztonsági mentési feladatok elindítása

A kezdeti biztonsági mentés az ütemterv szerint fog futni, de az alábbiak szerint azonnal futtatható:

1. A tároló menüben válassza a **biztonsági másolati elemek elemet**.
2. A **biztonsági másolati elemek**területen válassza az Azure-beli **virtuális gép**lehetőséget.
3. A **biztonsági mentési elemek** listában válassza a három pontot (...).
4. Válassza a **biztonsági mentés**lehetőséget.
5. A **biztonsági mentés most**a Calendar (naptár) vezérlőelem használatával válassza ki azt az utolsó napot, ameddig a helyreállítási pontot meg kell őrizni. Ez után válassza az **OK** gombot.
6. A portál értesítéseinek figyelése. A feladat előrehaladását a tároló irányítópultján követheti nyomon > **biztonsági mentési feladatok**  >  **folyamatban**vannak. A virtuális gép méretétől függően a kezdeti biztonsági mentés létrehozása hosszabb időt vehet igénybe.

## <a name="provide-permissions"></a>Engedélyek megadása

Azure Backup csak olvasási hozzáféréssel kell rendelkeznie a kulcsok és titkok biztonsági mentéséhez, valamint a hozzájuk tartozó virtuális gépekhez.

- A Key Vault az Azure-előfizetéshez tartozó Azure AD-bérlőhöz van társítva. Ha Ön a **tag felhasználó**, a Azure Backup további művelet nélkül szerzi be a Key Vault elérését.
- Ha **vendég felhasználó**, meg kell adnia a Azure Backup számára a kulcstartó eléréséhez szükséges engedélyeket.

Engedélyek beállítása:

1. A Azure Portal válassza a **minden szolgáltatás**lehetőséget, és keresse meg a **kulcstárolókat**.
1. Válassza ki a titkosított virtuális géphez társított kulcstartót.
1. Válassza a **hozzáférési szabályzatok**  >  **hozzáférési házirend hozzáadása**lehetőséget.

    ![Hozzáférési szabályzat hozzáadása](./media/backup-azure-vms-encryption/add-access-policy.png)

1. Válassza a **résztvevő kiválasztása**lehetőséget, majd írja be a **biztonsági mentés kezelése**elemet.
1. Válassza a **biztonsági mentési felügyeleti szolgáltatás**  >  **elemet**.

    ![Backup szolgáltatás kiválasztása](./media/backup-azure-vms-encryption/select-backup-service.png)

1. A **hozzáférési szabályzat**  >  **beállítása sablonból (nem kötelező) beállításnál**válassza a **Azure Backup**lehetőséget.
    - A szükséges engedélyek a **legfontosabb engedélyek** és a **titkos engedélyek**előtt vannak feltöltve.
    - Ha a virtuális gép **csak BEK**használatával van titkosítva, távolítsa el a **kulcs engedélyeinek** kijelölését, mert csak a titkokra vonatkozó engedélyekre van szüksége.

    ![Az Azure Backup kiválasztása](./media/backup-azure-vms-encryption/select-backup-template.png)

1. Válassza a **Hozzáadás** elemet. A **biztonsági mentési felügyeleti szolgáltatás** hozzá van adva a **hozzáférési házirendekhez**.

    ![Hozzáférési szabályzatok](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

1. A **Save (Mentés** ) lehetőség kiválasztásával megadhatja Azure Backup az engedélyekkel.

## <a name="restore-an-encrypted-vm"></a>Titkosított virtuális gép visszaállítása

A titkosított virtuális gépek csak a virtuális gép lemezének visszaállításával állíthatók vissza az alább leírtak szerint. A meglévő és a **visszaállítási virtuális gép** **cseréje** nem támogatott.

A titkosított virtuális gépeket a következőképpen állíthatja vissza:

1. [Állítsa vissza a virtuális gép lemezét](backup-azure-arm-restore-vms.md#restore-disks).
2. Hozza létre újból a virtuálisgép-példányt a következők egyikével:
    1. A visszaállítási művelet során létrehozott sablon segítségével testre szabhatja a virtuális gép beállításait, és aktiválhatja a virtuális gépek telepítését. [További információk](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    2. Hozzon létre egy új virtuális gépet a helyreállított lemezekről a PowerShell használatával. [További információk](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
3. Linux rendszerű virtuális gépek esetén telepítse újra az ADE bővítményt, hogy az adatlemezek nyitva és csatlakoztatva legyenek.

## <a name="next-steps"></a>További lépések

Ha bármilyen problémába ütközik, tekintse át a következő cikkeket:

- [Gyakori hibák](backup-azure-vms-troubleshoot.md) a titkosított Azure-beli virtuális gépek biztonsági mentése és visszaállítása során.
- [Azure VM-ügynök/biztonsági mentési bővítményekkel](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) kapcsolatos problémák.
