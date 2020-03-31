---
title: Az Azure-beli virtuális gépek biztonsági mentése a Recovery Services-tárolóban
description: Bemutatja, hogyan lehet biztonsági másolatot készíteni az Azure virtuális gépeiről egy Recovery Services-tárolóban az Azure Backup használatával
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: aeadd7bc798f690c67eef38c6dc645204ff39115
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273513"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Az Azure-beli virtuális gépek biztonsági mentése a Recovery Services-tárolóban

Ez a cikk ismerteti, hogyan készíthet biztonsági másolatot az Azure-beli virtuális gépekről egy Recovery Services-tárolóban az [Azure Backup](backup-overview.md) szolgáltatás használatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * Készítse elő az Azure virtuális gépeit.
> * Hozzon létre egy trezort.
> * Fedezze fel a virtuális gépeket, és konfiguráljon egy biztonsági mentési szabályzatot.
> * Engedélyezze a biztonsági mentést az Azure virtuális gépei számára.
> * Futtatja a kezdeti biztonsági mentést.

> [!NOTE]
> Ez a cikk ismerteti, hogyan kell beállítani egy tárolót, és válassza a virtuális gépek biztonsági mentéséhez. Ez akkor hasznos, ha több virtuális gépről szeretne biztonsági másolatot. Azt is megteheti, [hogy biztonsági másolatot egy azure-beli virtuális gép](backup-azure-vms-first-look-arm.md) közvetlenül a virtuális gép beállításait.

## <a name="before-you-start"></a>Előkészületek

* [Tekintse át](backup-architecture.md#architecture-built-in-azure-vm-backup) az Azure virtuális gép biztonsági mentési architektúráját.
* [További információ a](backup-azure-vms-introduction.md) Az Azure virtuális gép biztonsági mentése és a biztonsági mentésbővítmény.
* A biztonsági mentés konfigurálása előtt [tekintse át a támogatási mátrixot.](backup-support-matrix-iaas.md)

Ezen kívül van néhány dolog, amit szükség lehet tennie bizonyos körülmények között:

* **Telepítse a virtuálisgép-ügynököt a virtuális gépre:** Az Azure Backup biztonsági másolatot készít az Azure virtuális gépekről azáltal, hogy egy bővítményt telepít a gépen futó Azure virtuálisgép-ügynökre. Ha a virtuális gép egy Azure piactéri lemezképből lett létrehozva, az ügynök telepítve van és fut. Ha egyéni virtuális gépet hoz létre, vagy áttelepít egy helyszíni gépet, előfordulhat, hogy manuálisan kell [telepítenie az ügynököt.](#install-the-vm-agent)

## <a name="create-a-vault"></a>Tároló létrehozása

 A tároló tárolja a biztonsági mentések és helyreállítási pontok idővel létrehozott, és tárolja a biztonsági mentési szabályzatok biztonsági mentési gépekhez társított. Hozzon létre egy tárolót az alábbiak szerint:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. A keresésben írja be a **Helyreállítási szolgáltatások kifejezést.** A **Szolgáltatások csoportban**kattintson a **Helyreállítási szolgáltatások tárolói**elemre.

     ![Helyreállítási szolgáltatások tárolóinak keresése](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png)

3. A **Recovery Services-tárolók** menüben kattintson a **+Hozzáadás**gombra.

     ![Recovery Services-tároló létrehozása – 2. lépés](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. A **Recovery Services-tárolóban**írja be a tároló azonosítására egy rövid nevet.
    * A névnek egyedinek kell lennie az Azure-előfizetéshez.
    * 2–50 karaktert tartalmazhat.
    * Betűvel kell kezdődnie, és csak betűket, számokat és kötőjeleket tartalmazhat.
5. Válassza ki az Azure-előfizetést, erőforráscsoportot és földrajzi régiót, amelyben a tárolót létre kell hozni. Ezután kattintson a **Létrehozás gombra.**
    * Beletelhet egy kis időbe, amíg létrejön a trezor.
    * A portál jobb felső részén lévő állapotértesítések figyelése.

A tároló létrehozása után megjelenik a Recovery Services-tárolók listájában. Ha nem látja a trezort, válassza a **Frissítés**lehetőséget.

![A Backup-tárolók listája](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> Az Azure Backup most már lehetővé teszi az Azure Backup szolgáltatás által létrehozott erőforráscsoport nevének testreszabását. További információ: [Azure Backup erőforráscsoport virtuális gépekhez.](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines)

### <a name="modify-storage-replication"></a>Tárolóreplikáció módosítása

Alapértelmezés szerint a tárolók [georedundáns tárolást (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)használnak.

* Ha a tároló az elsődleges biztonsági mentési mechanizmus, javasoljuk, hogy grs-t használjon.
* Használhatja [a helyileg redundáns tárolás (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) egy olcsóbb lehetőség.

A tárolóreplikáció típusának módosítása a következőképpen:

1. Az új tárolóban kattintson a **Beállítások** szakasz **Tulajdonságok gombjára.**
2. A **Tulajdonságok csoportBan**a **Biztonsági másolat beállítása csoportban**kattintson a **Frissítés gombra.**
3. Jelölje ki a tárolóreplikáció típusát, majd kattintson a **Mentés gombra.**

      ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > A tároló beállítása után nem módosíthatja a tároló replikációs típusát, és biztonsági mentési elemeket tartalmaz. Ha ezt szeretné megtenni, újra létre kell hoznia a tárolót.

## <a name="apply-a-backup-policy"></a>Biztonsági mentési házirend alkalmazása

Konfigurálja a tároló biztonsági mentési házirendjét.

1. A tárolóban kattintson a **+Biztonsági mentés** **gombra** az Áttekintés szakaszban.

   ![Biztonsági másolat gomb](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. A **Biztonsági mentési cél hol** > **fut a számítási feladatok?** válassza az **Azure**lehetőséget. A **Mit szeretne biztonsági másolatot tenni?** válassza a Virtuális **gép** >  **OK**lehetőséget. Ez regisztrálja a virtuális gép bővítményét a tárolóban.

   ![Biztonsági másolat és biztonsági mentés it.](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. A **Biztonsági másolat házirendben**válassza ki a tárolóhoz társítani kívánt házirendet.
    * Az alapértelmezett házirend naponta egyszer biztonsági másolatot ad a virtuális gépről. A napi biztonsági mentések 30 napig megmaradnak. Az azonnali helyreállítási pillanatképek két napig megmaradnak.
    * Ha nem szeretné használni az alapértelmezett házirendet, válassza az **Új létrehozása**lehetőséget, és hozzon létre egy egyéni házirendet a következő eljárásban leírtak szerint.

      ![Alapértelmezett biztonsági mentési házirend](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. A Válassza ki a **virtuális gépek,** válassza ki a virtuális gépeket szeretne biztonsági másolatot a házirend használatával. Ezt követően kattintson az **OK** gombra.

   * A kijelölt virtuális gépek érvényesítése.
   * Csak a tárolóval azonos régióban lévő virtuális gépeket választhatja ki.
   * Virtuális gépek csak egy tárolóban lehet biztonsági másolatot.

     !["Virtuális gépek kiválasztása" ablaktábla](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. A **Biztonsági másolat segédprogramban**kattintson a **Biztonsági másolat engedélyezése gombra.** Ez telepíti a szabályzatot a tárolóba és a virtuális gépekre, és telepíti a biztonsági mentési bővítményt az Azure virtuális gépen futó virtuális gépen.

     !["Biztonsági mentés engedélyezése" gomb](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

A biztonsági mentés engedélyezése után:

* A biztonsági mentési szolgáltatás telepíti a biztonsági mentési bővítményt, függetlenül attól, hogy a virtuális gép fut-e.
* A kezdeti biztonsági mentés a biztonsági mentés ütemezésének megfelelően fog futni.
* Amikor a biztonsági mentések futnak, vegye figyelembe, hogy:
  * A virtuális gép, amely fut a legnagyobb esélye egy alkalmazás-konzisztens helyreállítási pont rögzítésére.
  * Azonban még akkor is, ha a virtuális gép ki van kapcsolva, hogy biztonsági másolatot kell. Az ilyen virtuális gép offline virtuális gépként ismert. Ebben az esetben a helyreállítási pont összeomlás-konzisztens lesz.
* Explicit kimenő kapcsolat nem szükséges az Azure virtuális gépek biztonsági mentésének engedélyezéséhez.

### <a name="create-a-custom-policy"></a>Egyéni szabályzat létrehozása

Ha új biztonságimentési házirend létrehozását választotta, töltse ki a házirend-beállításokat.

1. A **Házirend neve**területen adjon meg egy értelmes nevet.
2. A **Biztonsági másolat ütemezése területen**adja meg, hogy mikor kell biztonsági másolatokat készíteni. Napi vagy heti biztonsági mentéseket készíthet az Azure virtuális gépekhez.
3. Az **Azonnali visszaállítás modulban**adja meg, hogy mennyi ideig szeretné megtartani a pillanatképeket helyileg az azonnali visszaállításhoz.
    * Visszaállításkor a virtuális gép lemezeiről a rendszer a tárolóból a hálózaton keresztül a helyreállítási tároló helyére másolja a biztonsági mentést. Az azonnali visszaállítással kihasználhatja a biztonsági mentési feladat során készített, helyileg tárolt pillanatképeket anélkül, hogy meg kellene várnia a biztonsági mentési adatok átvitelét a tárolóba.
    * Az azonnali visszaállításhoz egy-öt napig megőrizheti a pillanatképeket. Az alapértelmezett beállítás a két nap.
4. Az **Adatmegőrzési tartományban**adja meg, hogy mennyi ideig szeretné megtartani a napi vagy heti biztonsági mentési pontokat.
5. A **havi biztonsági mentési pont megőrzése területen**adja meg, hogy meg szeretné-e tartani a napi vagy heti biztonsági mentések havi biztonsági mentését.
6. A házirend mentéséhez kattintson az **OK** gombra.

    ![Új biztonsági mentési házirend](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Az Azure Backup nem támogatja az automatikus órabeállítás az Azure virtuális gépek biztonsági mentések nyári időtakarékos módosításai esetén. Az időváltozások bekövetkezésekor szükség szerint manuálisan módosítsa a biztonsági mentési házirendeket.

## <a name="trigger-the-initial-backup"></a>A kezdeti biztonsági mentés aktiválása

A kezdeti biztonsági mentés az ütemezésnek megfelelően fog futni, de azonnal futtathatja az alábbiak szerint:

1. A tároló menüjében kattintson a **Biztonsági másolat parancsra.**
2. A **Biztonsági másolat elemei csoportban**kattintson az Azure virtuális **gép**elemre.
3. A **Biztonsági másolat elemei listában** kattintson a három pontra (...).
4. Kattintson **a Biztonsági mentés gombra.**
5. A **Biztonsági másolat most**eszközben a naptár vezérlővel jelölje ki azt az utolsó napot, amikor a helyreállítási pontot meg kell őrizni. Ezt követően kattintson az **OK** gombra.
6. A portál értesítéseinek figyelése. A feladat előrehaladását a tároló irányítópultján figyelheti > folyamatban lévő **biztonsági mentési feladatok** > **.** A virtuális gép méretétől függően a kezdeti biztonsági mentés létrehozása hosszabb időt vehet igénybe.

## <a name="verify-backup-job-status"></a>Biztonsági másolat készítése feladat állapotának ellenőrzése

A biztonsági mentési feladat részletei minden virtuális gép biztonsági mentése két fázisból áll, a **Pillanatkép** fázis, majd az **adatok átvitele** a tároló fázisba.<br/>
A pillanatkép fázis garantálja a rendelkezésre álló helyreállítási pont tárolt lemezek mellett az **azonnali visszaállítások,** és a felhasználó által konfigurált pillanatkép-megőrzési legfeljebb öt napig állnak rendelkezésre. Adatok átvitele a tárolóba létrehoz egy helyreállítási pontot a tárolóban a hosszú távú megőrzéshez. Az adatok átvitele a tárolóba csak a pillanatkép-fázis befejezése után kezdődik.

  ![Biztonsági másolat készítése: feladat állapota](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Két **alfeladat** fut a háttérrendszerben, az egyik az előtér-biztonsági mentési feladathoz, amely az alábbi módon ellenőrizhető a **Biztonsági másolat készítés részletei** panelen:

  ![Biztonsági másolat készítése: feladat állapota](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

Az **adatok átvitele a tárolóba** több napig is eltarthat a lemezek méretétől, a lemezenkénti lemorzsolódástól és számos más tényezőtől függően.

A feladat állapota a következő forgatókönyvektől függően változhat:

**Pillanatkép** | **Adatok átvitele a tárolóba** | **Feladat állapota**
--- | --- | ---
Befejezve | Folyamatban | Folyamatban
Befejezve | Kimarad | Befejezve
Befejezve | Befejezve | Befejezve
Befejezve | Sikertelen | Figyelmeztetéssel kiegészítve
Sikertelen | Sikertelen | Sikertelen

Ezzel a képességgel, ugyanahhoz a virtuális géphez két biztonsági mentés párhuzamosan futhat, de mindkét fázisban (pillanatkép, adatátvitel a tárolóba) csak egy alfeladat futtatható. Így a forgatókönyvek voltak a biztonsági mentési feladat folyamatban eredményezett a következő napon a biztonsági mentés sikertelen lesz elkerülni ezzel a leválasztás funkcióval. A következő napi biztonsági mentések pillanatképet is elvégezhetnek, miközben az **adatok átvitele a tárolóba** kimarad, ha egy korábbi nap biztonsági mentési feladata folyamatban van.
A tárolóban létrehozott növekményes helyreállítási pont rögzíti a tárolóban létrehozott utolsó helyreállítási pont összes lemorzsolódását. Nincs költség hatása a felhasználóra.

## <a name="optional-steps"></a>Nem kötelező lépések

### <a name="install-the-vm-agent"></a>A virtuálisgép-ügynök telepítése

Az Azure Backup biztonsági másolatot készít az Azure virtuális gépekről azáltal, hogy egy bővítményt telepít a gépen futó Azure virtuálisgép-ügynökre. Ha a virtuális gép egy Azure Marketplace-lemezképből lett létrehozva, az ügynök telepítve van és fut. Ha egyéni virtuális gépet hoz létre, vagy áttelepít egy helyszíni gépet, előfordulhat, hogy manuálisan kell telepítenie az ügynököt, a hogy a táblázatban összegezve.

**Vm** | **Részletek**
--- | ---
**Windows** | 1. [Töltse le és telepítse](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) az ügynök MSI fájlt.<br/><br/> 2. Telepítse rendszergazdai engedélyekkel a számítógépen.<br/><br/> 3. Ellenőrizze a telepítést. A *VM C:\WindowsAzure\Packages mappájában* kattintson a jobb gombbal a **WaAppAgent.exe** > **tulajdonságai elemre.** A **Részletek** lapon a **termékverziónak** 2.6.1198.718-nak vagy magasabbnak kell lennie.<br/><br/> Ha frissíti az ügynököt, győződjön meg arról, hogy nem futnak biztonsági mentési műveletek, majd [telepítse újra az ügynököt](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Telepítse rpm vagy DEB csomag használatával a disztribúció csomagtárából. Ez az előnyben részesített módszer az Azure Linux-ügynök telepítéséhez és frissítéséhez. Az összes [jóváhagyott terjesztési szolgáltatók](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrálja az Azure Linux-ügynök csomagot a rendszerképek és adattárak. Az ügynök elérhető a [GitHubon,](https://github.com/Azure/WALinuxAgent)de nem javasoljuk a telepítést.<br/><br/> Ha frissíti az ügynököt, győződjön meg arról, hogy nem futnak biztonsági mentési műveletek, és frissítse a bináris fájlokat.

>[!NOTE]
> **Az Azure Backup mostantól támogatja a szelektív lemezbiztonsági mentést és visszaállítást az Azure virtuális gép biztonsági mentési megoldásával.**
>
>Az Azure Backup ma támogatja az összes lemez (operációs rendszer és adatok) biztonsági mentését a virtuális gépben a virtuális gép biztonsági mentési megoldásának használatával. A exclude-disk funkcióval lehetőséget kap arra, hogy egy vagy néhány biztonsági másolatot készítsen a virtuális gép számos adatlemezéről. Ez hatékony és költséghatékony megoldást nyújt a biztonsági mentési és visszaállítási igényekhez. Minden helyreállítási pont a biztonsági mentési műveletben szereplő lemezek adatait tartalmazza, ami lehetővé teszi, hogy a lemezek egy részhalmaza visszaálljon az adott helyreállítási pontról a visszaállítási művelet során. Ez a pillanatképből és a tárolóból történő visszaállításra vonatkozik.
>
>**Az előnézetre való feliratkozáshoz írjon nekünk aAskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>További lépések

* Az Azure [VM-ügynökökkel](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) vagy az [Azure VM biztonsági mentésével](backup-azure-vms-troubleshoot.md)kapcsolatos problémák elhárítása.
* [Visszaállítás](backup-azure-arm-restore-vms.md) Az Azure virtuális gépek.
