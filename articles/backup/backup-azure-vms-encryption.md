---
title: Titkosított Azure-beli virtuális gépek biztonsági mentése és visszaállítása
description: A titkosított Azure-beli virtuális gépek biztonsági mentését és visszaállítását ismerteti a Azure Backup szolgáltatással.
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: c4bc413e70d8e19f8006580c0631641651dcaf92
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172523"
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
**Unmanaged** | Igen | Igen
**Managed**  | Igen | Igen

- További információ az [ade](../security/azure-security-disk-encryption-overview.md), a [Key Vault](../key-vault/key-vault-overview.md)és a [KEK](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/).
- Olvassa el az Azure-beli virtuális gép lemezének titkosításával kapcsolatos [gyakori kérdéseket](../security/azure-security-disk-encryption-faq.md) .

### <a name="limitations"></a>Korlátozások

- A titkosított virtuális gépek biztonsági mentését és visszaállítását ugyanazon az előfizetésen és régión belül végezheti el.
- Azure Backup a különálló kulcsokkal titkosított virtuális gépeket támogatja. A virtuális gépek titkosításához használt tanúsítvány részét képező egyik kulcs jelenleg nem támogatott.
- A titkosított virtuális gépek biztonsági mentését és visszaállítását a Recovery Services backup-tárolóval megegyező előfizetésben és régióban végezheti el.
- A titkosított virtuális gépek nem állíthatók helyre a fájl/mappa szintjén. A fájlok és mappák visszaállításához helyre kell állítania a teljes virtuális gépet.
- Egy virtuális gép visszaállításakor nem használhatja a [meglévő virtuális gép cseréje](backup-azure-arm-restore-vms.md#restore-options) beállítást a titkosított virtuális gépekhez. Ez a beállítás csak titkosítatlan felügyelt lemezek esetén támogatott.

## <a name="before-you-start"></a>Előkészületek

Mielőtt elkezdené, tegye a következőket:

1. Győződjön meg arról, hogy van egy vagy több Windows vagy [Linux](../virtual-machines/linux/disk-encryption-overview.md) [rendszerű](../security/azure-security-disk-encryption-windows.md) virtuális gép, amelyen az ade engedélyezve van.
2. [Tekintse át az](backup-support-matrix-iaas.md) Azure virtuális gép biztonsági mentésének támogatási mátrixát
3. Ha nem rendelkezik ilyennel, [hozzon létre](backup-azure-arm-vms-prepare.md#create-a-vault) egy Recovery Services backup-tárolót.
4. Ha engedélyezi a titkosítást olyan virtuális gépek számára, amelyek már engedélyezve vannak a biztonsági mentéshez, egyszerűen biztonsági mentést kell biztosítania a Key Vault eléréséhez, hogy a biztonsági mentések megszakítás nélkül is folytatódnak. [További](#provide-permissions) információ az engedélyek hozzárendeléséről.

Emellett van néhány dolog, amit bizonyos esetekben szükség lehet:

- **Telepítse a virtuálisgép-ügynököt a virtuális gépre**: Azure Backup biztonsági mentést készít az Azure-beli virtuális gépekről a számítógépen futó Azure VM-ügynök bővítményének telepítésével. Ha a virtuális gép Azure Piactéri rendszerképből lett létrehozva, akkor az ügynök telepítve van és fut. Ha egyéni virtuális gépet hoz létre, vagy egy helyszíni gépet telepít át, előfordulhat, hogy [manuálisan kell telepítenie az ügynököt](backup-azure-arm-vms-prepare.md#install-the-vm-agent).
- A **kimenő hozzáférés explicit engedélyezése**: általában nem szükséges explicit módon engedélyezni az Azure-beli virtuális gépek kimenő hálózati hozzáférését ahhoz, hogy a Azure Backup kommunikáljon. Előfordulhat azonban, hogy egyes virtuális gépek kapcsolódási problémákba ütközik, és a kapcsolódási kísérlet során **ExtensionSnapshotFailedNoNetwork** hibaüzenetet jelenítenek meg. Ha ez történik, [explicit módon engedélyeznie kell a kimenő hozzáférést](backup-azure-arm-vms-prepare.md#explicitly-allow-outbound-access), így a Azure Backup bővítmény képes kommunikálni az Azure nyilvános IP-címeivel a biztonsági mentési forgalomhoz.

## <a name="configure-a-backup-policy"></a>Biztonsági mentési szabályzat konfigurálása

1. Ha még nem hozott létre Recovery Services backup-tárolót, kövesse [az alábbi utasításokat](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Nyissa meg a tárolót a portálon, majd a **első lépések** szakaszban válassza a **biztonsági mentés** lehetőséget.

    ![Biztonsági mentés panel](./media/backup-azure-vms-encryption/select-backup.png)

3. A **biztonsági mentési cél** > **hol fut a munkaterhelés?** válassza az **Azure**lehetőséget.
4. A **Miről szeretne biztonsági másolatot készíteni?** válassza a **virtuális gép** > **OK**elemet.

      ![Forgatókönyv panel](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. A **biztonsági mentési szabályzat** > **válassza a biztonsági mentési szabályzat**lehetőséget, majd válassza ki a tárolóhoz társítandó szabályzatot. Ezután kattintson az **OK** gombra.
    - A biztonsági mentési szabályzat meghatározza, hogy mikor készüljön biztonsági mentés, és mennyi ideig tárolja a rendszer.
    - Az alapértelmezett házirend részletei megtalálhatók a legördülő menüben.

    ![Forgatókönyv panel megnyitása](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Ha nem szeretné használni az alapértelmezett házirendet, válassza az **új létrehozása**lehetőséget, és [hozzon létre egy egyéni házirendet](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

7. Válassza ki azokat a titkosított virtuális gépeket, amelyekről biztonsági másolatot szeretne készíteni a Select Policy használatával, majd kattintson az **OK gombra**.

      ![Titkosított virtuális gépek kiválasztása](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Ha Azure Key Vault használ, a tároló lapon megjelenik egy üzenet arról, hogy Azure Backup csak olvasási hozzáféréssel kell rendelkeznie a Key Vault kulcsaihoz és titkaihoz.

    - Ha ezt az üzenetet kapja, nincs szükség beavatkozásra.

        ![Hozzáférés OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Ha ezt az üzenetet kapja, be kell állítania az engedélyeket az [alábbi eljárásban](#provide-permissions)leírtak szerint.

        ![Hozzáférési figyelmeztetés](./media/backup-azure-vms-encryption/access-warning.png)

9. A biztonsági mentés **engedélyezése** lehetőségre kattintva telepítheti a biztonsági mentési szabályzatot a tárolóban, és engedélyezheti a kiválasztott virtuális gépek biztonsági mentését.

## <a name="trigger-a-backup-job"></a>Biztonsági mentési feladatok elindítása

A kezdeti biztonsági mentés az ütemterv szerint fog futni, de az alábbiak szerint azonnal futtatható:

1. A tároló menüjében kattintson a **biztonsági másolati elemek elemre**.
2. A **biztonsági másolati elemek**területen kattintson az Azure-beli **virtuális gép**elemre.
3. A **biztonsági mentési elemek** listában kattintson a három pontra (...).
4. Kattintson a **biztonsági mentés**gombra.
5. A **biztonsági mentés most**a Calendar (naptár) vezérlőelem használatával válassza ki azt az utolsó napot, ameddig a helyreállítási pontot meg kell őrizni. Ezután kattintson az **OK** gombra.
6. A portál értesítéseinek figyelése. A feladat előrehaladását a tároló irányítópultján követheti nyomon > a **biztonsági mentési feladatok** > **folyamatban**van. A virtuális gép méretétől függően a kezdeti biztonsági mentés létrehozása hosszabb időt vehet igénybe.

## <a name="provide-permissions"></a>Engedélyek megadása

Az Azure-beli virtuális gépnek csak olvasási hozzáférésre van szüksége a kulcsok és titkok biztonsági mentéséhez, valamint a hozzájuk tartozó virtuális gépekhez.

- A Key Vault az Azure-előfizetéshez tartozó Azure AD-bérlőhöz van társítva. Ha Ön a **tag felhasználó**, a Azure Backup további művelet nélkül szerzi be a Key Vault elérését.
- Ha **vendég felhasználó**, meg kell adnia a Azure Backup számára a kulcstartó eléréséhez szükséges engedélyeket.

Engedélyek beállítása:

1. A Azure Portal válassza a **minden szolgáltatás**lehetőséget, és keresse meg a **kulcstárolókat**.
2. Válassza ki a titkosított virtuális géphez társított kulcstartót.
3. Válassza a **hozzáférési szabályzatok** > **új hozzáadása**elemet.
4. Válassza a **résztvevő kiválasztása**lehetőséget, majd írja be a **biztonsági mentés kezelése**elemet.
5. Válassza a **Backup Management szolgáltatás** > a **Kiválasztás lehetőséget**.

    ![Backup szolgáltatás kiválasztása](./media/backup-azure-vms-encryption/select-backup-service.png)

6. A **hozzáférési házirend hozzáadása** > **a sablonból (nem kötelező) beállításnál**válassza a **Azure Backup**lehetőséget.
    - A szükséges engedélyek a **legfontosabb engedélyek** és a **titkos engedélyek**előtt vannak feltöltve.
    - Ha a virtuális gép **csak BEK**használatával van titkosítva, távolítsa el a **kulcs engedélyeinek** kijelölését, mert csak a titkokra vonatkozó engedélyekre van szüksége.

    ![Az Azure Backup kiválasztása](./media/backup-azure-vms-encryption/select-backup-template.png)

7. Kattintson az **OK** gombra. A **biztonsági mentési felügyeleti szolgáltatás** hozzá van adva a **hozzáférési házirendekhez**.

    ![Hozzáférési szabályzatok](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

8. Kattintson a **Save (Mentés** ) gombra a Azure Backup engedélyekkel való megadásához.

## <a name="restore-an-encrypted-vm"></a>Titkosított virtuális gép visszaállítása

A titkosított virtuális gépeket a következőképpen állíthatja vissza:

1. [Állítsa vissza a virtuális gép lemezét](backup-azure-arm-restore-vms.md#restore-disks).
2. Ezután tegye a következők egyikét:
    - A visszaállítási művelet során létrehozott sablon segítségével testre szabhatja a virtuális gép beállításait, és aktiválhatja a virtuális gépek telepítését. [Részletek](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    - Hozzon létre egy új virtuális gépet a helyreállított lemezekről a PowerShell használatával. [Részletek](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
    - Linux rendszerű virtuális gépek esetén állítsa alaphelyzetbe az ADE-bővítményt, hogy az adatlemezek nyitva és csatlakoztatva legyenek.

## <a name="next-steps"></a>Következő lépések

Ha bármilyen problémába ütközik, tekintse át a következő cikkeket:

- [Gyakori hibák](backup-azure-vms-troubleshoot.md) a titkosított Azure-beli virtuális gépek biztonsági mentése és visszaállítása során.
- [Azure VM-ügynök/biztonsági mentési bővítményekkel](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) kapcsolatos problémák.
