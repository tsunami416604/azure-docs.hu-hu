---
title: 'Az Azure Backup: Virtuális gépek biztonsági mentésének előkészítése'
description: Győződjön meg arról, hogy a környezet elő kell készíteni az Azure-beli virtuális gépek biztonsági mentését.
services: backup
author: markgalioto
manager: carmonm
keywords: biztonsági másolatok biztonsági mentése;
ms.service: backup
ms.topic: conceptual
ms.date: 9/10/2018
ms.author: markgal
ms.openlocfilehash: ebfd879fcd619dab48e4a08130f86afc68f91207
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2018
ms.locfileid: "48785426"
---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>A környezet előkészítése a Resource Managerrel üzembe helyezett virtuális gépek biztonsági mentéséhez

Ez a cikk lépéseit a biztonsági mentése az Azure Resource Manager által telepített virtuális gép (VM) a környezet előkészítése. A lépéseket, látható az eljárásokat használhatja az Azure Portalt. A virtuális gépek biztonsági mentésekor, a biztonsági mentési adatokat vagy a helyreállítási pontok vannak tárolva egy Recovery Services-tárolót. Recovery Services-tárolók a klasszikus és Resource Manager által telepített virtuális gépek biztonsági mentési adatok tárolására.

> [!NOTE]
> Az Azure az erőforrások létrehozásához és használatához két üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md).

Mielőtt védelme (vagy felhatolásra) a Resource Manager által telepített virtuális gép ellenőrizze, hogy az Előfeltételek létezik:

* Hozzon létre vagy Recovery Services-tároló azonosítása *ugyanabban a régióban, mint a virtuális gép*.
* Alkalmazási helyzet kiválasztása, a biztonsági mentési házirend meghatározása, és adja meg a megvédeni kívánt elemeket.
* Egy Virtuálisgép-ügynök (kiterjesztés) a virtuális gép telepítésének ellenőrzése.
* Ellenőrizze a hálózati kapcsolatot.
* Linux rendszerű virtuális gépekhez, ha szeretné testre szabhatja a biztonsági mentési környezetet az alkalmazáskonzisztens biztonsági mentést, kövesse a [pillanatkép előtti és utáni szkriptekkel konfigurálásának lépései](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Ha ezek a feltételek a környezetében már létezik, ugorjon a [a virtuális gépek biztonsági mentése](backup-azure-arm-vms.md) cikk. Állítsa be, vagy ellenőrizze az alábbi előfeltételek bármelyike van szüksége, ha az Ez a cikk végigvezeti a lépéseket.

## <a name="supported-operating-systems-for-backup"></a>Támogatott operációs rendszerek biztonsági mentés

 * **Linux**: az Azure Backup támogatja [disztribúciókat, amely Azure vannak jóváhagyva](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), a CoreOS Linux kivételével. Fájlok visszaállítása a támogató Linux operációs rendszerek listáját lásd: [fájlok helyreállítása a virtuális gépek biztonsági mentésének](backup-azure-restore-files-from-vm.md#for-linux-os).

    > [!NOTE]
    > Más bring-your-saját Linux-disztribúciók működnek, mindaddig, amíg a Virtuálisgép-ügynök nem érhető el a virtuális gépen, és támogatja a pythont. Azonban ezeket a disztribúció nem támogatottak.
    >
 * **A Windows Server**, **Windows ügyfél**: Windows Server 2008 R2 vagy Windows 7-nél régebbi verziók nem támogatottak.


## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Korlátozások a biztonsági mentése és visszaállítása egy virtuális gép esetén
Mielőtt a környezet előkészítése, ügyeljen arra, hogy ezek a korlátozások ismertetése:

* Több mint 32 adatlemezekkel rendelkező virtuális gépek biztonsági mentése nem támogatott.
* A fenntartott IP-cím és a nem meghatározott végpontot a virtuális gépek biztonsági mentése nem támogatott.
* Linux egyesített kulcs beállítása (LUKS) titkosítással titkosított Linux rendszerű virtuális gépek biztonsági mentésének nem támogatott.
* Nem ajánlott a fürt megosztott kötetei (CSV) vagy a Scale-Out File Server tartalmazó virtuális gépek biztonsági mentésének. Ha elkészült, a fürt megosztott kötetei szolgáltatás írók hiba várható. A fürt konfigurációját, a pillanatkép-feladat során szereplő összes virtuális gépet is érintő igényelnek. Az Azure Backup nem támogatja a több virtuális gépre kiterjedő konzisztencia.
* Biztonsági mentési adatokat egy virtuális géphez csatlakoztatott hálózati meghajtók nem tartalmazza.
* Egy meglévő virtuális gép cseréje a visszaállítás során nem támogatott. Ha megkísérli a virtuális gép visszaállítása, ha a virtuális gép létezik, a visszaállítási művelet sikertelen lesz.
* Régiók közötti biztonsági mentése és visszaállítása nem támogatottak.
* Közben konfigurálása biztonsági mentést, ellenőrizze, hogy a **tűzfalak és virtuális hálózatok** tárfiók-beállítások engedélyezze a hozzáférést minden hálózatból elérhető.
* A kiválasztott hálózatok után a tárfiók tűzfal- és a virtuális hálózati beállítások konfigurálása, válassza **engedélyezése megbízható Microsoft-szolgáltatások a tárfiók** engedélyezése az Azure Backup szolgáltatásnak a kivételként a korlátozott hálózati tárfiók eléréséhez. Az elemszintű helyreállítás nem támogatott, korlátozott hálózati tárfiókok esetében.
* Minden nyilvános Azure-régióban lévő virtuális gépek készíthető. (Lásd a [ellenőrzőlista](https://azure.microsoft.com/regions/#services) támogatott régiók.) A régiót, amelyben keres jelenleg nem támogatott, ha ez nem jelenik a legördülő listából válassza ki a tároló létrehozása során.
* (DC) virtuális gép egy több-DC konfiguráció részét képező támogatott tartományvezérlő visszaállítása csak a Powershellen keresztül. További tudnivalókért lásd: [multi-tartományvezérlő tartományvezérlő visszaállítása](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
* Az engedélyezett Írásgyorsító lemez-pillanatképek nem támogatott. Ez a korlátozás blokkolja az Azure Backup szolgáltatás képes-e a virtuális gép összes lemezének alkalmazáskonzisztens pillanatképet végrehajtásához.
* A következő speciális hálózati konfigurációval rendelkező virtuális gépek visszaállítása csak a Powershellen keresztül támogatott. A visszaállítási munkafolyamat a felhasználói felületen keresztül létrehozott virtuális gépek nem fog hálózati konfigurációkról, a visszaállítási művelet befejeződése után. További tudnivalókért lásd: [virtuális gépek visszaállítása speciális hálózati konfigurációval rendelkező](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations).
  * Virtuális gépek terheléselosztó-konfiguráció (belső és külső)
  * Több fenntartott IP-címmel rendelkező virtuális gépek
  * Virtuális gépek több hálózati adapter

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Létrehoz egy Recovery Services-tárolót egy virtuális Géphez
Recovery Services-tároló olyan entitás, amely a biztonsági mentések és idővel létrehozott helyreállítási pontokat tárolja. A Recovery Services-tárolót is tartalmaz, amely a védett virtuális gépek társítva vannak a biztonsági mentési szabályzatok.

Egy Recovery Services-tároló létrehozásához:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Az a **Hub** menüjében válassza **Tallózás**, majd írja be **Recovery Services**. Ahogy elkezd gépelni, a bemeneti szűri az erőforrások listájában. Válassza ki **Recovery Services-tárolók**.

    ![A mezőbe, majd válassza a "Helyreállítási tárak" a eredmények](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    A Recovery Services-tárolók listája megjelenik.
1. Az a **Recovery Services-tárolók** menüjében válassza **Hozzáadás**.

    ![Recovery Services-tároló létrehozása – 2. lépés](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    A **Recovery Services-tárolók** panel nyílik meg. Akkor arra kéri, hogy a **neve**, **előfizetés**, **erőforráscsoport**, és **hely**.

    !["A recovery Services-tárolók" panel](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
1. A **Név** mezőben adjon meg egy egyszerű nevet a tároló azonosításához. A névnek egyedinek kell lennie az Azure-előfizetéshez. Írjon be egy nevet, amely tartalmazza a 2 – 50 karakter lehet. Betűvel kell kezdődnie, és csak betűket, számokat és kötőjeleket tartalmazhat.
1. Válassza ki **előfizetés** az elérhető előfizetések listájának megtekintéséhez. Ha nem biztos abban, hogy melyik előfizetést szeretné használni, használja az alapértelmezett (vagy javasolt) előfizetést. Nincs több lehetőség csak akkor, ha a munkahelyi vagy iskolai fiók több Azure-előfizetéssel társítva.
1. Válassza ki **erőforráscsoport** az elérhető erőforráscsoportok listájának megtekintéséhez, vagy válasszon **új** egy új erőforráscsoport létrehozásához. Az erőforráscsoportokkal teljes körű információkért lásd: [Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md).
1. Válassza ki **hely** a tárolóhoz tartozó földrajzi régió kiválasztásához. A tárolónak ugyanabban a régióban *kell* lennie, mint a megvédeni kívánt virtuális gépeknek.

   > [!IMPORTANT]
   > Ha biztos benne, hogy a hely, amelyben a virtuális gép található, a tároló létrehozása párbeszédpanel bezárásához, és nyissa meg a portálon lévő virtuális gépek listáját. Ha több régióban rendelkezik virtuális gépekkel, minden régióban hozzon létre egy Recovery Services-tárolót szeretne. Hozza létre a tárolót az első helyen, majd lépjen a következő helyre. Hiba esetén nem kell megadnia a storage-fiókok a biztonsági mentési adatok tárolására. A Recovery Services-tároló és az Azure Backup szolgáltatás, amely automatikusan kezeli.
   >
   >

1. Kattintson a **Létrehozás** gombra. A Recovery Services-tároló létrehozása eltarthat egy ideig. Figyelje a jobb felső területén, a portál. Miután a tároló létrejött, megjelenik a Recovery Services-tárolók listája. Ha nem látja a tárolót, válassza ki a **frissítése**.

    ![A Backup-tárolók listája](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Most, hogy létrehozta a tárolóját, megismerkedhet a tárreplikáció beállításának módjával.

## <a name="set-storage-replication"></a>Tárreplikáció beállítása
A tárolóreplikációs beállítást georedundáns tárolás és helyileg redundáns tárolás között válasszon teszi lehetővé. Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik. A georedundáns tárolás az elsődleges biztonsági mentési beállítás beállítást hagyja. Ha egy olcsóbb, rövidebb élettartamú, válassza a helyileg redundáns tárolás.

A tárreplikációs beállítás szerkesztése:

1. Az a **Recovery Services-tárolók** ablaktáblán válassza ki a tárolót.
    Amikor kiválasztja a tárolót, a **beállítások** (amely a tároló nevére van felső) panelen, és nyissa meg a tároló részleteket tartalmazó ablaktáblán.

   ![Válassza ki a tárolót a backup-tárolók listájából](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

1. Az a **beállítások** panelen görgessen le a függőleges csúszkát használatával a **kezelés** szakaszt, és válassza ki **biztonsági mentési infrastruktúra**. Az a **általános** szakaszban jelölje be **biztonsági mentés konfigurációja**. Az a **biztonsági mentés konfigurációja** ablaktáblán válassza ki a tárolóreplikációs beállítást a tároló számára. Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik.

   ![A Backup-tárolók listája](./media/backup-azure-arm-vms-prepare/full-blade.png)

   Ha az Azure, elsődleges biztonsági mentési tároló végpontjaként használja, továbbra is a georedundáns tárolás használata. Ha Azure használja, egy nem elsődleges biztonsági mentési tároló végpontjaként, válassza a helyileg redundáns tárolás. További információ a tárolási lehetőségek a [Azure tárreplikáció áttekintése](../storage/common/storage-redundancy.md).

1. Ha módosította a tárolóreplikáció típusa, válassza ki a **mentése**.

Után a beállítást választja a tároló számára, készen áll a virtuális gép társítása a tárolóban. A hozzárendelés megkezdéséhez fel kell fedezni és regisztrálni kell az Azure virtuális gépeket.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Válassza ki a biztonsági mentés célját, állítsa be a házirendet és határozza meg a megvédeni kívánt elemeket
Mielőtt regisztrálja a virtuális gépek Recovery Services-tárolóval, futtassa a felderítési folyamat az előfizetéshez történő hozzáadása új virtuális gépek azonosításához. A felderítési folyamat lekéri az Azure-előfizetésben található virtuális gépek listáját. Ha új virtuális gépek találhatók, a portál megjeleníti a felhőszolgáltatás neve és a társított régió. Az Azure Portalon a *forgatókönyv* van, a Recovery Services-tároló megadása. *A házirend* adja meg a gyakoriságának és elhelyezési idejének a helyreállítási pontokat készít. A házirend emellett tartalmazza a helyreállítási pontok megőrzési tartományát.

1. Ha már meg van nyitva egy Recovery Services-tároló, folytassa a 2. lépéssel. Ha nem rendelkezik nyissa meg a Recovery Services-tárolót, nyissa meg a [az Azure portal](https://portal.azure.com/). Az a **Hub** menüjében válassza **további szolgáltatások**.

   a. Az erőforrások listájába írja be a következőt: **Recovery Services**. Ahogy elkezd gépelni, a bemeneti szűri a listában. Amikor látja **Recovery Services-tárolók**, jelölje ki azt.

      ![A mezőbe, majd válassza a "Helyreállítási tárak" a eredmények](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

      A Recovery Services-tárolók listája megjelenik. Ha az előfizetésében nincsenek tárolók, a lista je prázdná.

      ![A Recovery Services-tároló listájának nézete](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   b. A Recovery Services-tárolók listájából válasszon ki egy tárolót.

      A **beállítások** ablaktábla és a tároló irányítópultján a választott tároló megnyitása.

      ![Beállítások ablaktábla és a tár irányítópult](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
1. Válassza ki a tároló irányítópultos menüjében **Backup**.

   ![Biztonsági mentés gomb](./media/backup-azure-arm-vms-prepare/backup-button.png)

   A **Backup** és **biztonsági mentés célja** ablaktábla megnyitásához.

1. Az a **a biztonsági mentési célja** ablaktáblában állítsa **a számítási feladat futtató?** , **Azure** és **miről szeretne biztonsági másolatot készíteni?** ,  **Virtuális gép**. Ezután kattintson az **OK** gombra.

   ![Biztonsági mentés és a biztonsági mentés célja panel](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Ebben a lépésben a Virtuálisgép-bővítmény regisztrálja a tárolóban. A **biztonsági mentés célja** panel bezárul, és a **biztonsági mentési szabályzat** panel nyílik meg.

   !["Mentés" és "Biztonsági mentési szabályzat" panel](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
1. Az a **biztonsági mentési szabályzat** ablaktáblán válassza ki, hogy a tároló esetén alkalmazni kívánt biztonsági mentési szabályzat.

   ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

   Az alapértelmezett házirend részletei megtalálhatók a legördülő menüben. Ha új házirendet hozna létre, válassza az **Új létrehozása** elemet a legördülő menüből. A biztonsági mentési házirendek meghatározását segítő utasításokat itt találja: [Biztonsági mentési házirend meghatározása](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Válassza ki **OK** társítja a biztonsági mentési házirendet a tárolóval.

   A **biztonsági mentési szabályzat** panel bezárul, és a **válassza ki a virtuális gépek** panel nyílik meg.
1. Az a **válassza ki a virtuális gépek** ablaktáblán válassza ki a megadott házirendhez hozzárendelni, és válassza ki a virtuális gépeket **OK**.

   !["A virtuális gépek kiválasztása" panelen](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

   Megtörténik a kiválasztott virtuális gép ellenőrzése. Ha nem látja a várt virtuális gépeket, ellenőrizze, hogy a virtuális gépeket ugyanabban a régióban az Azure és a Recovery Services-tárolónak. Ha továbbra sem látja a virtuális gépeket, ellenőrizze, hogy azok még nem védett egy másik tárolóval. A tároló irányítópultjának mutatja a régió, ahol a Recovery Services-tároló létezik.

1. Most, hogy minden beállítást megadott a tárolóhoz, az a **biztonsági mentési** ablaktáblán válassza előbb **biztonsági mentés engedélyezése**. Ez a lépés a tároló és a virtuális gépeken telepítheti a szabályzatot. Ez a lépés nem hoz létre a kezdeti helyreállítási pontot a virtuális gép.

   !["A biztonsági mentés engedélyezése" gombra](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Miután sikeresen engedélyezte a biztonsági mentés, a biztonsági mentési szabályzat ütemezés szerint fog futni. Ha szeretné létrehozni a virtuális gépek biztonsági mentése, megjelenik egy igény szerinti biztonságimásolat-készítő feladat [a biztonsági mentési feladat aktiválása](./backup-azure-vms-first-look-arm.md#initial-backup).

Ha problémába ütközik a virtuális gép regisztrálása, tekintse meg a következő információkat a Virtuálisgép-ügynök telepítése és a hálózati kapcsolatot. Ha az Azure-ban létrehozott virtuális gépek védelmét valószínűleg nem kell a következő információkat. De ha a virtuális gépek az Azure-bA végzett áttelepítést, győződjön meg a Virtuálisgép-ügynök megfelelően telepítve, és, hogy a virtuális gépek kommunikálhatnak-e a virtuális hálózat.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Virtuálisgép-ügynök telepítése a virtuális gépen
A Backup bővítmény működjön, az Azure [Virtuálisgép-ügynök](../virtual-machines/extensions/agent-windows.md) az Azure virtuális gépen telepítve kell lennie. Ha a virtuális gép az Azure Marketplace-en lett létrehozva, a Virtuálisgép-ügynök már jelen a virtuális gépen.

A következő információ olyan esetekben, ahol Ön *nem* használatával egy virtuális gép létrehozása az Azure Marketplace-ről. **Például át a virtuális gép egy helyszíni adatközpontból. Ebben az esetben a virtuális gép ügynökét telepíteni kell a virtuális gép védelmét.**

**Megjegyzés:**: a Virtuálisgép-ügynök telepítése után is használnia kell az Azure PowerShell a ProvisionGuestAgent tulajdonság frissíteni, így az Azure tudja, hogy a virtuális gépen az ügynök telepítve van.

Ha rendelkezik az Azure virtuális gép biztonsági mentésével kapcsolatos problémák, a következő táblázat segítségével ellenőrizze, hogy az Azure-beli Virtuálisgép-ügynök megfelelően telepítve van a virtuális gépen. A táblázat a Virtuálisgép-ügynök további információt a Windows és Linux rendszerű virtuális gépekhez.

| **Művelet** | **Windows** | **Linux** |
| --- | --- | --- |
| A virtuálisgép-ügynök telepítése |Töltse le és telepítse az [ügynök MSI-t](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek. |<li> Telepítse a legújabb [Linux-ügynök](../virtual-machines/extensions/agent-linux.md). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek. Azt javasoljuk, hogy az ügynök telepítése a terjesztésipont-adattárból. Hogy **nem ajánlott** közvetlenül a githubból telepítése Linux rendszerű virtuális gépet.  |
| A virtuálisgép-ügynök frissítése |A virtuálisgép-ügynök frissítése a [virtuálisgép-ügynök bináris fájljainak](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) újratelepítéséből áll. <br>Győződjön meg róla, hogy nem fut biztonsági mentési művelet a virtuálisgép-ügynök frissítése közben. |Kövesse a [linuxos virtuálisgép-ügynök frissítését](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ismertető szakasz utasításait. Azt javasoljuk, hogy a terjesztési adattárból ügynök frissítését. Hogy **nem ajánlott** közvetlenül a githubból frissítése Linux rendszerű virtuális gépet.<br>Győződjön meg róla, hogy nem fut biztonsági mentési művelet a virtuálisgép-ügynök frissítése közben. |
| A virtuálisgép-ügynök telepítésének érvényesítése |<li>Lépjen a *C:\WindowsAzure\Packages* mappába az Azure virtuális gépen. <li>Itt találja a WaAppAgent.exe fájlt.<li> Kattintson jobb gombbal a fájlra, válassza a **Tulajdonságok** parancsot, majd nyissa meg a **Részletek** lapot. A Termék verziószáma mezőben 2.6.1198.718 vagy újabb verziónak kell lennie. |– |

### <a name="backup-extension"></a>Backup bővítmény
Miután a Virtuálisgép-ügynök telepítve van a virtuális gépen, az Azure Backup szolgáltatás a biztonsági mentési bővítményt a Virtuálisgép-ügynök telepítése. A Backup szolgáltatás zökkenőmentesen rendszerre frissül, és a biztonsági mentési bővítményt a javítások.

A Backup szolgáltatás telepíti a biztonsági mentési bővítményt, a virtuális gép fut-e. Egy futó virtuális gép adja a legnagyobb esélyt egy alkalmazással konzisztens helyreállítási pont létrehozásának. Azonban továbbra is a a Backup szolgáltatás biztonsági mentése a virtuális gép, még akkor is, ha ki van kapcsolva, és a bővítmény telepítése sikertelen volt. Ez az úgynevezett *offline virtuális gép*. Ebben az esetben a helyreállítási pont az *összeomláshoz igazodik* lesz.

## <a name="establish-network-connectivity"></a>Hálózati kapcsolatok létrehozása
A virtuális gép pillanatképek kezeléséhez, a biztonsági mentési bővítményt, kapcsolódnia kell az Azure nyilvános IP-címeket. A jobb oldali internetkapcsolat nélkül a virtuális gép HTTP-kérések időtúllépés, és a biztonsági mentés sikertelen lesz. Ha a központi telepítés rendelkezik hozzáférési korlátozásokat – keresztül egy hálózati biztonsági csoport (NSG), például – válasszon egyet az alábbi lehetőségek közül, világos és egyértelmű utat nyújt a biztonsági mentések forgalmától a következő címen:

* [Engedélyezett az Azure-adatközpont IP-címtartományok](http://www.microsoft.com/en-us/download/details.aspx?id=41653).
* A forgalom útválasztási HTTP-proxy kiszolgáló telepítése.

Amikor a mérlegeli, a használandó módszer, kezelhetőségi, részletes ellenőrzésére és költség között kell lennie a kompromisszummal.

| Beállítás | Előnyök | Hátrányai |
| --- | --- | --- |
| Engedélyezett IP-cím-tartományok |További költségek nélkül.<br><br>Nyissa meg a hozzáférést egy NSG-ben, használja a **Set-AzureNetworkSecurityRule** parancsmagot. |Összetett kezelhetők az érintett IP-címtartományok idővel változni.<br><br>Az Azure és a tároló nem csak a teljes hozzáférést biztosít. |
| HTTP proxyk használatára |Szabályozható a proxy a tároló URL-címek használata engedélyezett.<br><br>Virtuális gépek internet egyetlen pont hozzáférés.<br><br>Nem vonatkozik Azure IP-cím változik. |Virtuális gép futtatása a proxy szoftverhez további költségekkel. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Az Azure-adatközpont engedélyezett IP-címtartományok
Az engedélyezési listára az Azure adatközpont IP-címtartományait, tekintse meg a [Azure-webhelyen](http://www.microsoft.com/en-us/download/details.aspx?id=41653) az IP-címtartományok és az utasításokat.

Segítségével engedélyezheti az adott régió storage kapcsolatokat [szolgáltatáscímkéket](../virtual-network/security-overview.md#service-tags). Győződjön meg arról, hogy a szabály, amely lehetővé teszi a hozzáférést a tárfiókhoz magasabb prioritású, mint a szabályt, amely blokkolja az internet-hozzáféréssel rendelkezik-e.

![NSG-t egy régió címkék a storage](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

Az alábbi videó végigvezeti a részletes eljárást szolgáltatáscímkék konfigurálása:

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]

> [!NOTE]
> Tárolási szolgáltatáscímkék és régiók listáját lásd: [Szolgáltatáscímkéket Storage](../virtual-network/security-overview.md#service-tags).

### <a name="use-an-http-proxy-for-vm-backups"></a>A virtuális gép biztonsági mentéseinek HTTP proxyk használatára
Ha biztonsági másolatot egy virtuális Gépet, a biztonsági mentési bővítményt a virtuális gépen az Azure Storage a pillanatkép-kezelési parancsok küld egy HTTPS-API-val. A biztonsági mentési bővítményt a HTTP-proxy forgalmat útvonal, a konfigurált nyilvános internet-hozzáférés csak összetevőt, mert.

> [!NOTE]
> Nem ajánlott a speciális proxykat szoftver, amelyet használnia kell. Győződjön meg arról, hogy válasszon ki egy proxyt, amely kompatibilis a konfigurációs lépéseket olvashat.
>
>

A következő képen látható a három konfigurációs lépések szükségesek, hogy HTTP proxyk használatára:

* Az alkalmazás virtuális gép útvonalakat az összes HTTP-forgalom számára a nyilvános interneten keresztül éri el a virtuális gép kötve.
* A virtuális gép proxy lehetővé teszi, hogy a bejövő forgalom virtuális gépekről a virtuális hálózatban.
* Elégtelen-zárolási nevű hálózati biztonsági csoport van szüksége a biztonsági szabály, amely lehetővé teszi a kimenő internetes forgalmat a virtuális gép érkezett.

Egy HTTP-proxy használatával kommunikálnak a nyilvános interneten, kövesse az alábbi lépéseket.

> [!NOTE]
> Ezeket a lépéseket ebben a példában használja az adott neveit és értékeit. Amikor a rendszer megadása (vagy beillesztése) részletei elhelyezni a kódban, a neveket és értékeket a központi telepítéshez használni.

#### <a name="step-1-configure-outgoing-network-connections"></a>1. lépés: Konfigurálja a kimenő hálózati kapcsolatokat
###### <a name="for-windows-machines"></a>Windows-gépek
Ez az eljárás beállítja a proxykiszolgáló beállításait a helyi rendszer fiók számára.

1. Töltse le [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
1. Nyissa meg az Internet Explorer, a következő parancsot egy rendszergazda jogú parancssorból való futtatásával:

    ```
    psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
    ```

1. Az Internet Explorerben nyissa meg **eszközök** > **Internetbeállítások** > **kapcsolatok** > **LAN-beállítások**.
1. Ellenőrizze a proxykiszolgáló beállításait a system fiók. Állítsa be a proxykiszolgáló IP és port.
1. Zárja be az Internet Explorerben.

A következő szkript állítja be a gépre kiterjedő proxy konfigurációját, és használja a bármely kimenő HTTP- vagy HTTPS-forgalom számára. Ha a jelenlegi felhasználói fiók (nem a helyi rendszerfiók) proxykiszolgáló beállította a szkript használatával SYSTEMACCOUNT azokat alkalmazni.

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Ha a proxy server naplóban megfigyelte "(407) Proxyhitelesítés szükséges", ellenőrizze, hogy a hitelesítést megfelelően van-e beállítva.
>
>

###### <a name="for-linux-machines"></a>A Linux rendszerű gépek
Adja hozzá a következő sort a ```/etc/environment``` fájlt:

```
http_proxy=http://<proxy IP>:<proxy port>
```

Adja hozzá a következő sorokat a ```/etc/waagent.conf``` fájlt:

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>2. lépés: A proxykiszolgálót a bejövő kapcsolatok engedélyezése
1. Nyissa meg a proxykiszolgáló, a Windows tűzfal. A tűzfal eléréséhez legegyszerűbb módja a keresendő **fokozott biztonságú Windows tűzfal**.
1. Az a **fokozott biztonságú Windows tűzfal** párbeszédpanelen kattintson a jobb gombbal **bejövő szabályok** válassza **új szabály**.
1. Az új bejövő szabály varázsló a a **szabálytípus** lapon válassza ki a **egyéni** lehetőséget, majd válassza ki **tovább**.
1. Az a **Program** lapon jelölje be **minden program** válassza **tovább**.
1. Az a **protokoll és portok** lapon adja meg a következő adatokat, és válassza **tovább**:
   * A **protokoll típusa**válassza **TCP**.
   * A **helyi port**válassza **adott**. A következő párbeszédpanelen adja meg a konfigurált proxy-port száma.
   * A **távoli port**válassza **minden port**.

A varázsló további részeit, a fogadja el az alapértelmezett beállításokat, amíg meg nem jelenik a teljes körű. Ez a szabály majd adni egy nevet.

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>3. lépés: Vegyen fel egy kivételt az NSG-hez
Az alábbi parancs kivételt hozzáadja az NSG-hez. Ez a kivétel lehetővé teszi, hogy a TCP-forgalom a 80-as (HTTP) vagy a 443-as (HTTPS) porton bármely internetcím 10.0.0.5 a bármely portról. Ha egy adott portot a nyilvános interneten van szüksége, mindenképp vegye fel a portot ```-DestinationPortRange```.

Az Azure PowerShell-parancssort írja be a következő parancsot:

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

## <a name="questions"></a>Kérdései vannak?
Ha kérdése van, vagy ha van olyan szolgáltatás, és meg szeretné tekinteni a csomagban foglalt, [küldjön visszajelzést](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>További lépések
Most, hogy előkészítette a környezetet a virtuális gép biztonsági mentésével, a következő logikus lépés a biztonsági mentés létrehozásához. A tervezési cikk virtuális gépek biztonsági mentésének részletes információkkal szolgál.

* [Virtuális gépek biztonsági mentése](backup-azure-arm-vms.md)
* [Virtuális gép biztonsági infrastruktúrájának megtervezése](backup-azure-vms-introduction.md)
* [Virtuális gépek biztonsági mentéseinek kezelése](backup-azure-manage-vms.md)
