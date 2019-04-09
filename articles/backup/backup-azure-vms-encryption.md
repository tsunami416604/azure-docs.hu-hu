---
title: Biztonsági mentése és visszaállítása titkosított Azure virtuális gépek az Azure Backup szolgáltatással
description: Ismerteti, hogyan lehet biztonsági mentése és visszaállítása titkosított Azure virtuális gépek az Azure Backup szolgáltatással.
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 4/3/2019
ms.author: geetha
ms.openlocfilehash: 99117c96f79dd7d0da388a0e793908f6ffb8ed27
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266443"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Biztonsági mentése és visszaállítása titkosított Azure virtuális Gépen

Ez a cikk bemutatja, hogyan biztonsági mentése és visszaállítása Windows vagy Linux rendszerű Azure virtuális gépeken (VM) használatával titkosított lemezek a [Azure Backup](backup-overview.md) szolgáltatás.

Ha azt szeretné, ha többet szeretne megtudni, hogyan Azure Backup együttműködik az Azure virtuális gépek megkezdése előtt tekintse át az erőforrásokat:

- [Felülvizsgálat](backup-architecture.md#architecture-direct-backup-of-azure-vms) az Azure virtuális gép biztonsági mentési architektúra.
- [Ismerje meg](backup-azure-vms-introduction.md) Azure virtuális gépek biztonsági mentését, és az Azure Backup bővítmény.

## <a name="encryption-support"></a>Titkosítás támogatása

Az Azure Backup támogatja az operációs rendszer/adatlemezek titkosítja az Azure Disk Encryption (ADE) rendelkező Azure virtuális gépek biztonsági mentését. ADE BitLocker titkosítási Windows virtuális gépek és a dm-crypt szolgáltatás Linux rendszerű virtuális gépekhez használja. ADE integrálható az Azure Key Vault lemeztitkosítási kulcsokat és titkos kulcsok kezeléséhez. Key Vault kulcs titkosítási kulcsok (KEk) segítségével adjon hozzá egy további rétegét biztonsági, titkosító titkosítási titkos kódok őket a Key Vault írása előtt.

Azure biztonsági mentés készíthető, illetve Azure virtuális gépek ADE használatával és anélkül az Azure AD-alkalmazás visszaállítása, az alábbi táblázat foglalja össze.

**Virtuális gép lemeztípusa** | **ADE (BEK/dm-crypt)** | **ADE és kek-KEL**
--- | --- | --- 
**Nem felügyelt** | Igen | Igen
**Managed**  | Igen | Igen

- Tudjon meg többet [ADE](../security/azure-security-disk-encryption-overview.md), [Key Vault](../key-vault/key-vault-overview.md), és [kek](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/).
- Olvassa el a [– gyakori kérdések](../security/azure-security-disk-encryption-faq.md) lemeztitkosítás Azure virtuális Gépen.



### <a name="limitations"></a>Korlátozások

- Készítsen biztonsági másolatot, és állítsa vissza a titkosított virtuális gépek belül az azonos előfizetésben és régióban.
- Az Azure Backup támogatja a különálló kulcsok használatával titkosított virtuális gépeket. Bármelyik billentyűt, amely része a virtuális gép titkosításához használt tanúsítványt jelenleg nem támogatott.
- Biztonsági mentését, és állítsa vissza a titkosított virtuális gépek ugyanazt az előfizetést és a Recovery Services Backup-tárolóban és a régióban.
- Titkosított virtuális gépek nem állítható helyre, a fájl vagy mappa szintjén. A teljes virtuális gép visszaállításához, fájlok és mappák helyreállítása kell.
- Virtuális gépek visszaállításakor nem használható a [cserélje le a meglévő virtuális gép](backup-azure-arm-restore-vms.md#restore-options) titkosított virtuális gépek lehetőséget. Ezt a beállítást csak nem titkosított felügyelt lemezek esetében támogatott.




## <a name="before-you-start"></a>Előkészületek

Mielőtt elkezdené, tegye a következőket:

1. Ellenőrizze, hogy egy vagy több [Windows](../security/azure-security-disk-encryption-windows.md) vagy [Linux](../security/azure-security-disk-encryption-linux.md) ADE rendelkező virtuális gépek engedélyezve van.
2. [Tekintse át a támogatási mátrix](backup-support-matrix-iaas.md) Azure VM backup esetében
3. [Hozzon létre](backup-azure-arm-vms-prepare.md#create-a-vault) egy helyreállítási szolgáltatások biztonsági mentési tárolót, ha nem rendelkezik ilyennel.
4. Ha engedélyezi a titkosítást a virtuális gépek, amelyek már engedélyezve van a biztonsági mentéshez, egyszerűen adja meg a biztonsági mentés az engedélyeket az eléréséhez a Key Vault, hogy a biztonsági mentések továbbra is megszakítás nélkül kell. [További](#provide-permissions) hozzárendeléséről ezeket az engedélyeket.

Ezenkívül van néhány dolgot, amely a bizonyos körülmények között szüksége lehet:

- **A Virtuálisgép-ügynök telepítése a virtuális gépen**: Az Azure Backup biztonsági másolatot készít az Azure virtuális gépek a gépen futó Azure-beli Virtuálisgép-ügynök bővítmény telepítésével. Ha a virtuális gép az Azure marketplace-lemezképből lett létrehozva, az ügynök telepítve és fut. Ha egy egyéni virtuális Gépet hoz létre, vagy egy helyszíni gép áttelepítése, szüksége lehet [telepítse kézzel az ügynököt](backup-azure-arm-vms-prepare.md#install-the-vm-agent).
- **Explicit módon engedélyezi a kimenő hozzáférést**: Általában nem kell explicit módon engedélyezi a kimenő hálózati hozzáférés egy Azure virtuális gép ahhoz, hogy azt az Azure Backup folytatott kommunikációhoz. Azonban néhány virtuális gépet is szembesülhet kapcsolódási problémák, megjelenítése a **ExtensionSnapshotFailedNoNetwork** hiba csatlakozásra tett kísérlet közben. Ha ez történik, akkor [explicit módon engedélyezi a kimenő hozzáférést](backup-azure-arm-vms-prepare.md#explicitly-allow-outbound-access), így az Azure Backup bővítmény képes kommunikálni az Azure nyilvános IP-címeket, a biztonsági mentések forgalmától.



## <a name="configure-a-backup-policy"></a>Biztonsági mentési szabályzat konfigurálása

1. Ha Ön még nem hozta létre biztonsági mentési Recovery Services-tárolót, hajtsa végre a [ezeket az utasításokat](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Nyissa meg a tárolót a portálon, és válassza ki **Backup** a a **bevezetés** szakaszban.

    ![Biztonsági mentés panel](./media/backup-azure-vms-encryption/select-backup.png)

3. A **biztonsági mentés célja** > **a számítási feladat futtató?** kiválasztása **Azure**.
4. A **választható biztonsági mentéséhez?** kiválasztása **virtuális gép** > **OK**.

      ![Forgatókönyv panel](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. A **biztonsági mentési szabályzat** > **biztonsági mentési házirend kiválasztása**, válassza ki a tárolóval társítani kívánt házirendet. Ezután kattintson az **OK** gombra.
    - Biztonsági mentési szabályzat adja meg, amikor a biztonsági másolatokat készít, és hogy mennyi ideig tárolódnak.
    - Az alapértelmezett házirend részletei megtalálhatók a legördülő menüben.

    ![Forgatókönyv panel megnyitása](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Ha nem szeretné használni az alapértelmezett házirendet, válassza ki a **hozzon létre új**, és [hozzon létre egy egyéni házirendet](backup-azure-arm-vms-prepare.md#create-a-custom-policy).


7. Válassza a titkosított virtuális gépek biztonsági mentése a select-szabályzattal szeretné, majd válasszon **OK**.

      ![Válassza ki a titkosított virtuális gépekhez](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Azure Key Vault használatakor a tároló oldalon megjelenik egy üzenet, hogy az Azure Backup kell a kulcsok és titkos kulcsok a Key vaultban a csak olvasási hozzáférést.

    - Ha ezt az üzenetet kap, akkor semmit nem kell.
    
        ![Hozzáférés OK](./media/backup-azure-vms-encryption/access-ok.png)
        
    - Ha ez az üzenet-engedélyek beállítására leírtaknak megfelelően kell a [kövesse az alábbi eljárást](#provide-permissions).
    
        ![Hozzáférés figyelmeztetés](./media/backup-azure-vms-encryption/access-warning.png)

9. Kattintson a **biztonsági mentés engedélyezése** a biztonsági mentési házirend a tároló üzembe helyezését és a kiválasztott virtuális gépek biztonsági mentésének engedélyezése. 


## <a name="trigger-a-backup-job"></a>Biztonsági mentési feladat aktiválása

A kezdeti biztonsági mentés a ütemterv szerint fog futni, de is futtatható legyen azonnal az alábbiak szerint:

1. A tároló menüjében kattintson **biztonsági mentési elemek**.
2. A **biztonsági másolati elemek** kattintson **Azure virtuális gép**.
3. Az a **biztonsági másolati elemek** listában, kattintson a három pontra (...).
4. Kattintson a **biztonsági mentés**.
5. A **biztonsági mentés**, használja a naptárvezérlőt annak, hogy megőrződjön-e a helyreállítási pont kiválasztására. Ezután kattintson az **OK** gombra.
6. A portál feladatértesítések figyelésére. Figyelheti a feladat előrehaladását a tároló irányítópultján > **biztonsági mentési feladatok** > **folyamatban**. A virtuális gép méretétől függően a kezdeti biztonsági mentés létrehozása hosszabb időt vehet igénybe.


## <a name="provide-permissions"></a>Engedélyek megadása

Az Azure virtuális gép a kulcsok és titkos kulcsokat, valamint a kapcsolódó virtuális gépek biztonsági mentése csak olvasható hozzá kell férnie.

- A Key Vault az Azure AD-bérlő az Azure-előfizetés társítva. Ha Ön egy **tag felhasználói**, az Azure Backup beszerzi a Key Vault további teendők nélkül való hozzáférést.
- Ha Ön egy **vendégfelhasználó**, meg kell adnia a kulcstartó elérését az Azure Backup engedélyek.

Engedélyek beállításához:

1. Az Azure Portalon válassza ki a **minden szolgáltatás**, és keressen rá a **tárolók kulcs**.
2. Válassza ki a titkosított virtuális gép biztonsági mentését végzi a társított kulcstartót.
3. Válassza ki **hozzáférési házirendek** > **új hozzáadása**.
4. Válassza ki **válassza egyszerű**, majd írja be **Backup felügyeleti**. 
5. Válassza ki **biztonsági felügyeleti szolgáltatás** > **kiválasztása**.

    ![A biztonsági mentési szolgáltatás kiválasztása](./media/backup-azure-vms-encryption/select-backup-service.png)

6. A **hozzáférési szabályzat hozzáadása** > **konfigurálása (nem kötelező) sablonból**válassza **Azure Backup**.
    - A szükséges engedélyek vannak előre kitöltött a **Kulcsengedélyek** és **titkos kód engedélyei**.
    - Ha a virtuális gép titkosítása **rendelkeznek BEk-KEL csak**, távolítsa el az adatgyűjtésre vonatkozó felhasználói **Kulcsengedélyek** óta csak engedélyek szükségesek a titkos kulcsok. 

    ![Az Azure biztonsági mentés kiválasztása](./media/backup-azure-vms-encryption/select-backup-template.png)

6. Kattintson az **OK** gombra. **Biztonsági mentés a felügyeleti szolgáltatás** adnak hozzá **hozzáférési házirendek**. 

    ![Hozzáférési szabályzatok](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

7. Kattintson a **mentése** biztosít az Azure Backup az engedélyeket.

## <a name="restore-an-encrypted-vm"></a>Egy titkosított virtuális gép visszaállítása

A következő visszaállítása titkosított virtuális gépek:

1. [A Virtuálisgép-lemez visszaállítása](backup-azure-arm-restore-vms.md#restore-disks).
2. Ezután tegye a következők egyikét:
    - A sablon használata, amely akkor jön létre, a visszaállítási művelet testre szabhatja a virtuális gép beállításait, és aktiválja a virtuális gép üzembe helyezése során. [További információk](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    - Hozzon létre egy új virtuális Gépet a Powershell használatával a helyreállított lemezek alapján. [További információk](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="next-steps"></a>További lépések

Ha problémákat tapasztal, tekintse át a következőket:

- [Gyakori hibák](backup-azure-vms-troubleshoot.md#troubleshoot-backup-of-encrypted-vms) amikor biztonsági mentése és visszaállítása titkosított Azure virtuális gépek.
- [Általános](backup-azure-vms-troubleshoot.md) kiállított Azure virtuális Gépen.
- [Az Azure Virtuálisgép-ügynök, illetve biztonsági mentési bővítményt](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) problémákat.
