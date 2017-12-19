---
title: "Az Azure Backup: Biztonsági másolatot készíteni a virtuális gépek előkészítése |} Microsoft Docs"
description: "Győződjön meg arról, hogy a környezet elő kell készíteni az Azure virtuális gépek biztonsági mentését."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "biztonsági mentések; biztonsági mentése;"
ms.assetid: e87e8db2-b4d9-40e1-a481-1aa560c03395
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/3/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: 9b3584a93766be6052c822f40328169910de26c7
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2017
---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>A környezet előkészítése a Resource Managerrel üzembe helyezett virtuális gépek biztonsági mentéséhez

Ez a cikk a biztonsági mentése egy erőforrás-kezelő telepített virtuális gép (VM) a környezet előkészítése a lépéseit ismerteti. A leírt eljárások lépés használja az Azure-portálon.  

Az Azure Backup szolgáltatás kétféle tárolók (biztonságimásolat-tárolók és a recovery services-tárolók) a virtuális gépek védelmére. A mentési tároló klasszikus telepítési modellel telepített virtuális gépek védelmére. A recovery services-tároló védi **mindkét klasszikus telepített és erőforrás-kezelő telepített virtuális gépek**. A Resource Manager telepített virtuális gépek védelméhez a Recovery Services-tárolónak kell használnia.

> [!NOTE]
> Az Azure két üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). 

Mielőtt védeni, vagy készítsen biztonsági másolatot egy erőforrás-kezelő telepített virtuális gép (VM), ellenőrizze, az Előfeltételek létezik:

* A recovery services-tároló létrehozása (vagy egy meglévő recovery services-tároló azonosítása) *és a virtuális gép ugyanazon a helyen lévő*.
* Válassza ki a forgatókönyvet, a biztonsági mentési házirend meghatározása és védelmére határozzák meg.
* Ellenőrizze a virtuális gép Virtuálisgép-ügynök telepítését.
* Ellenőrizze a hálózati kapcsolatot
* Linux virtuális gépekhez, abban az esetben, ha szeretné testre szabni a biztonsági környezetet az alkalmazás konzisztens biztonsági másolatok kérjük, kövesse a [pillanatkép előtti és a pillanatkép utáni parancsfájlok konfigurálásának lépései](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)

Ha tudja, hogy ezek a feltételek már létezik a környezetben, majd folytassa a [készítsen biztonsági másolatot a virtuális gépek cikk](backup-azure-arm-vms.md). Ha szeretné beállítani, vagy ellenőrizze, az Előfeltételek bármelyike Ez a cikk végigvezeti Önt a készíti elő az, hogy az megfelel a lépéseket.

##<a name="supported-operating-system-for-backup"></a>Támogatott operációs rendszer biztonsági mentése
 * **Linux**: Az Azure Backup az [Azure által támogatott disztribúciókat](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) támogatja, a Core OS Linux kivételével. _Más kerüljön-a-saját-Linux terjesztéseket is előfordulhat, hogy működni, amíg a Virtuálisgép-ügynök érhető el a virtuális gépen, a Python létezik támogatása. Azonban azt hitelesíti ezeket terjesztéseket, a biztonsági mentéshez._
 * **Windows Server**: A Windows Server 2008 R2-nél régebbi verziók nem támogatottak.

## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Ha a biztonsági mentése és visszaállítása egy virtuális gép korlátozásai
Mielőtt a környezet előkészítése, tartsa szem előtt a korlátozások vonatkoznak.

* Több mint 16 adatlemezekkel rendelkező virtuális gépek biztonsági mentését nem támogatott.
* Virtuális gépek biztonsági mentését adatokkal 1023GB-nál nagyobb mérete nem támogatott.

> [!NOTE]
> Rendelkezünk egy privát előzetes verzióval olyan virtuális gépek biztonsági mentésének támogatásához, amelyeknél a nem felügyelt lemezek mérete meghaladja az 1 TB-ot. A részletekért tekintse meg [nagy virtuális gép biztonsági mentési támogatása a Private Preview verziójára](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a)
>
>

* A fenntartott IP-cím és a nem definiált végpontot a virtuális gépek biztonsági mentését nem támogatott.
* Csak BEK használatával titkosított virtuális gépek biztonsági mentése nem támogatott. Linux virtuális gépek LUKS titkosítással titkosított biztonsági mentése nem támogatott.
* A fürt megosztott Volumes(CSV) vagy skálája tartalmazó fájl kiszolgálókonfiguráció kimenő virtuális gépek biztonsági mentése nem ajánlott, mivel a fürt konfigurálása során a pillanatkép-feladat szereplő összes virtuális gépet is érintő van szükségük. Azure biztonsági mentés nem támogatja a virtuális Gépre kiterjedő konzisztencia. 
* Biztonsági mentési adatok nem tartalmazza a virtuális Géphez csatlakozik, a csatlakoztatott hálózati meghajtókat.
* Egy meglévő virtuális gép cseréje a visszaállítás során nem támogatott. Ha úgy próbálja visszaállítani a virtuális gép, ha a virtuális gép létezik, a visszaállítási művelet sikertelen.
* Kereszt-régió biztonsági mentése és visszaállítása nem támogatottak.
* Készíthet biztonsági másolatot az összes nyilvános régióiba Azure virtuális gépek (lásd a [ellenőrzőlista](https://azure.microsoft.com/regions/#services) a támogatott régiók). A régiót, amelyben keres jelenleg nem támogatott, ha már nem jelenik a legördülő lista tároló létrehozása során.
* A tartományvezérlők visszaállítását (DC) virtuális Gépet, amely része egy multi-tartományvezérlő-konfiguráció támogatott csak a PowerShell segítségével. Tudjon meg többet az [multi-DC tartományvezérlő visszaállítása](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
* Az alábbi speciális beállításokkal rendelkező virtuális gépek visszaállításakor csak a PowerShell használatával támogatott. A visszaállítási munkafolyamat a felhasználói felület használatával létrehozott virtuális gépek mindaddig nem lesz a hálózati konfigurációt, a visszaállítási művelet befejeződése után. További tudnivalókért lásd: [visszaállítását virtuális gépek speciális hálózati konfigurációkkal](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations).

  * Virtuális gépek a terheléselosztó-konfigurációja (belső és külső)
  * Virtuális gépek több foglalt IP-címmel
  * Virtuális gépek több hálózati adapterrel

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Létrehoz egy Recovery Services-tárolót egy virtuális géphez.
A recovery services-tároló olyan entitás, amely a biztonsági mentések és adott idő alatt létrehozott helyreállítási pontok tárolására. A recovery services-tároló tartalmazza a védett virtuális gépeihez tartozó biztonsági mentési házirendek is.

Egy Recovery Services-tároló létrehozásához:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A központi menüben kattintson a **Tallózás** elemre, majd az erőforrások listájába írja be a következőt: **Recovery Services**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Kattintson a **Recovery Services-tároló** elemre.

    ![Kattintson a Tallózás gombra, és írja be a Recovery Services. Amikor megjelenik a Recovery Services-tároló lehetőséget, kattintson rá a Recovery Services-tároló panel megnyitásához.](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    A Recovery Services-tárolók listája jelenik meg.
3. A **Recovery Services-tárolók** menüben kattintson a **Hozzáadás** elemre.

    ![Recovery Services-tároló létrehozása – 2. lépés](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    Megnyílik a Recovery Services-tároló panelje, a rendszer pedig egy **Név**, **Előfizetés**, **Erőforráscsoport** és **Hely** megadását kéri.

    ![Recovery Services-tároló létrehozása – 5. lépés](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. A **Név** mezőben adjon meg egy egyszerű nevet a tároló azonosításához. A névnek egyedinek kell lennie az Azure-előfizetéshez. Írjon be egy 2–50 karakter hosszúságú nevet. Ennek egy betűvel kell kezdődnie, és csak betűket, számokat és kötőjeleket tartalmazhat.
5. Kattintson az **Előfizetés** elemre az elérhető előfizetések listájának megtekintéséhez. Ha nem biztos benne, hogy melyik előfizetést szeretné használni, használja az alapértelmezett (vagy javasolt) előfizetést. Csak akkor lesz több választási lehetőség, ha a szervezetéhez tartozó fiók több Azure-előfizetéssel van összekötve.
6. Kattintson az **Erőforráscsoport** elemre az elérhető erőforráscsoportok listájának megtekintéséhez, vagy kattintson az **Új** elemre egy új erőforráscsoport létrehozásához. Átfogó információk az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md).
7. Kattintson a **Hely** elemre a tárolóhoz tartozó földrajzi régió kiválasztásához. A tárolónak ugyanabban a régióban **kell** lennie, mint a megvédeni kívánt virtuális gépeknek.

   > [!IMPORTANT]
   > Ha nem biztos a virtuális gép helyében, lépjen ki a tároló-létrehozási párbeszédpanelből, és lépjen a virtuális gépek listájához a portálon. Ha több régióban rendelkezik virtuális gépekkel, minden régióban létre kell hoznia egy Recovery Services-tárolót. Hozza létre a tárolót az első helyen, majd lépjen a következő helyre. Az adatok biztonsági másolatának tárolásához nincs szükség tárfiókok megadására – a Recovery Services-tároló és az Azure Backup szolgáltatás ezt automatikusan kezeli.
   >
   >

8. Kattintson a  **Create** (Létrehozás) gombra. A Recovery Services-tároló létrehozása eltarthat egy ideig. Figyelje az állapotértesítéseket a portál jobb felső területén. Miután a tároló létrejött, megjelenik a Recovery Services-tárolók listájában. Ha a tároló nem látható, kattintson a **frissítése** számára

    ![A Backup-tárolók listája](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

    Most, hogy létrehozta a tárolóját, megismerkedhet a tárreplikáció beállításának módjával.

## <a name="set-storage-replication"></a>Tárreplikáció beállítása
A tárreplikáció lehetősége lehetővé teszi, hogy georedundáns tárolás és helyileg redundáns tárolás között válasszon. Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik. Ha ez az elsődleges biztonsági mentési tároló, hagyja a beállítást georedundáns tároláson. Ha egy olcsóbb, rövidebb élettartamú megoldást szeretne, válassza a helyileg redundáns tárolást.

A tárreplikációs beállítás szerkesztése:

1. Az a **Recovery Services-tárolók** panelen válassza ki a tárolóban.
    A tároló, a beállítások panelről elemre (*a tároló neve tartalmaz felső*) és a tároló Részletek panel megnyitása.

    ![Válassza ki a tároló a mentési tárolók listájáról](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. Az a **beállítások** panelen görgessen le a függőleges csúszka segítségével a **kezelése** szakasz. Kattintson a **biztonsági infrastruktúra** a panel megnyitásához. Az a **általános** szakaszban kattintson **biztonsági mentési konfigurációhoz** a panel megnyitásához. A **Biztonsági mentés konfigurációja** panelen válassza ki a tárreplikációs beállítást a tároló számára. Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik. Ha módosítja a replikációs tárolótípus, kattintson a **mentése**.

    ![A Backup-tárolók listája](./media/backup-azure-arm-vms-prepare/full-blade.png)

     Ha az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, folytassa a georedundáns tárolás használatát. Ha egy nem elsődleges biztonsági másolatok tárolásának végpontként Azure használ, majd válassza a helyileg redundáns tárolás. A [georedundáns](../storage/common/storage-redundancy.md#geo-redundant-storage) és a [helyileg redundáns](../storage/common/storage-redundancy.md#locally-redundant-storage) tárolási lehetőségekről többet olvashat az [Azure tárreplikáció áttekintése](../storage/common/storage-redundancy.md) című cikkben.
    Miután kiválasztotta a tárolási beállítást a tároló számára, készen áll, hogy hozzárendelje a virtuális gépet a tárolóhoz. A hozzárendelés megkezdéséhez fel kell fedezni és regisztrálni kell az Azure virtuális gépeket.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Válassza ki a biztonsági mentés célját, állítsa be a házirendet, és határozza meg a megvédeni kívánt elemeket
Mielőtt regisztrálna egy virtuális gépet a tárolóval, futtassa a felfedezési folyamatot, hogy meggyőződjön arról, hogy az előfizetéshez hozzáadott minden új virtuális gép azonosítva lett. A folyamat lekéri az Azure-ból az előfizetésben található virtuális gépek listáját, olyan kiegészítő információkkal, mint a felhőszolgáltatás neve és a régió. Az Azure portálon a forgatókönyv arra utal, amit bele fog tenni a Recovery Services-tárolóba. A házirend adja meg a helyreállítási pontok gyakoriságának és elhelyezési idejének a menetrendjét. A házirend emellett tartalmazza a helyreállítási pontok megőrzési tartományát.

1. Ha már meg van nyitva egy Recovery Services-tároló, folytassa a 2. lépéssel. Ha nem rendelkezik nyissa meg a Recovery Services-tároló, majd nyissa meg a [Azure-portálon](https://portal.azure.com/) és a központ menüben kattintson a **további szolgáltatások**.

   * Az erőforrások listájába írja be a következőt: **Recovery Services**.
   * Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Amikor meglátja a **Recovery Services-tárolót**, kattintson rá.

     ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

     A Recovery Services-tárolók listája megjelenik. Ha az előfizetésében nincsenek tárolók, ez a lista üres lesz.

    ![A Recovery Services-tároló listájának nézete](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   * A Recovery Services-tárolók listában jelölje ki a tároló az irányítópult megnyitásához.

     A beállítások panelről és a tároló irányítópult a kiválasztott tároló megnyitása.

     ![Tároló panelének megnyitása](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
2. A tároló irányítópultos menüjében kattintson a **Biztonsági mentés** elemre a Biztonsági mentés panel megnyitásához.

    ![Biztonsági mentés panel megnyitása](./media/backup-azure-arm-vms-prepare/backup-button.png)

    Megnyílik a Biztonsági mentés és a Biztonsági mentés célja panel.

    ![Forgatókönyv panel megnyitása](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. A biztonsági mentési cél panelen állítsa **a számítási feladatok futtató** az Azure-bA és **miről szeretne biztonsági másolatot készíteni** virtuális géphez, majd kattintson a **OK**.

    Ez regisztrálja a virtuálisgép-bővítményt a tárolóban. A Biztonsági mentés célja panel bezárul, a **Biztonsági mentési házirend** panel pedig megnyílik.

    ![Forgatókönyv panel megnyitása](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
4. A Biztonsági mentési házirend panelen válassza ki a tároló esetén alkalmazni kívánt biztonsági mentési házirendet.

    ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

    Az alapértelmezett házirend részletei megtalálhatók a legördülő menüben. Ha új házirendet hozna létre, válassza az **Új létrehozása** elemet a legördülő menüből. A biztonsági mentési házirendek meghatározását segítő utasításokat itt találja: [Biztonsági mentési házirend meghatározása](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Kattintson az **OK** gombra a biztonsági mentési házirend a tárolóhoz való hozzárendeléséhez.

    A Biztonsági mentés házirend panel bezárul, a **Virtuális gépek kijelölése** panel pedig megnyílik.
5. A **Virtuális gépek kijelölése** panelen válassza ki a megadott házirendhez hozzárendelni kívánt virtuális gépeket, majd kattintson az **OK** gombra.

    ![Számítási feladat kiválasztása](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    Megtörténik a kiválasztott virtuális gép ellenőrzése. Ha nem látja azt a kívánt virtuális gépeket, ellenőrizze, hogy azok és a Recovery Services-tároló Azure ugyanazon a helyen szerepel, és nem védettek már egy másik tárolóban. A Recovery Services-tároló helye a tároló irányítópultján jelenik meg.

6. Most, hogy minden beállítást megadott a tárolóhoz, a Biztonsági mentés panelen kattintson a **Biztonsági mentés engedélyezése** elemre. Ez üzembe helyezi a tároló és a virtuális gépek házirendjét. Ez nem hozza létre a virtuális géphez tartozó első helyreállítási pontot.

    ![Biztonsági mentés engedélyezése](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

A biztonsági mentés sikeres engedélyezése után a biztonsági mentési házirend az ütemezés szerint fog futni. Ha szeretné létrehozni a virtuális gépek biztonsági mentése, ekkor megjelenik egy igény szerinti biztonságimásolat-készítő feladat [időt. a biztonságimásolat-készítő feladat](./backup-azure-arm-vms.md#triggering-the-backup-job).

Ha problémába ütközik a virtuális gép rögzítése, tekintse meg a következő információkat a Virtuálisgép-ügynök telepítése és a hálózati kapcsolatot. Ha az Azure-ban létrehozott virtuális gépek védelmét valószínűleg nem kell a következő információkat. Azonban ha az Azure a virtuális gépeket telepített át, akkor ne feledje megfelelően telepítette az ügynököt, és, hogy a virtuális gép kommunikálhatnak-e a virtuális hálózat.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Telepítse a virtuális gép ügynökét a virtuális gépre.
Az Azure virtuálisgép-ügynököt telepíteni kell az Azure virtuális gépre, hogy a Backup bővítmény működjön. Ha a virtuális Gépet az Azure katalógusában alapján készült, majd a Virtuálisgép-ügynök már telepítve a virtuális gép. Ezen információ a helyzetekben, ahol *nem* használatával egy virtuális Gépet az Azure katalógusában létre – például végezte az áttelepítést egy virtuális Gépet egy olyan helyszíni adatközpontban. Ebben az esetben a Virtuálisgép-ügynök telepítve kell lennie ahhoz, hogy a virtuális gép védelmét. További tudnivalók a [Virtuálisgép-ügynök](../virtual-machines/windows/classic/agents-and-extensions.md#azure-vm-agents-for-windows-and-linux).

Ha problémákba ütközik az Azure virtuális gép telepítése közben, ellenőrizze, hogy az Azure virtuálisgép-ügynök megfelelően telepítve van-e a virtuális gépen (lásd az alábbi táblázatot). A következő táblázat további információkat tartalmaz a Windows és Linux virtuális gépekhez tartozó virtuálisgép-ügynökről.

| **Művelet** | **Windows** | **Linux** |
| --- | --- | --- |
| A virtuálisgép-ügynök telepítése |Töltse le és telepítse az [ügynök MSI-t](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek. |<li> Telepítse a legújabb [Linux-ügynök](../virtual-machines/linux/agent-user-guide.md). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek. Azt javasoljuk, hogy a terjesztési tárházból ügynök telepítése. A Microsoft **nem javasoljuk** közvetlenül a githubból telepítése Linux Virtuálisgép-ügynök.  |
| A virtuálisgép-ügynök frissítése |A virtuálisgép-ügynök frissítése a [virtuálisgép-ügynök bináris fájljainak](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) újratelepítéséből áll. <br>Győződjön meg róla, hogy nem fut biztonsági mentési művelet a virtuálisgép-ügynök frissítése közben. |Kövesse a [linuxos virtuálisgép-ügynök frissítését](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ismertető szakasz utasításait. Azt javasoljuk, hogy a terjesztési tárházból ügynök frissítése. A Microsoft **nem javasoljuk** közvetlenül a githubból frissítése Linux Virtuálisgép-ügynök.<br>Győződjön meg róla, hogy nem fut biztonsági mentési művelet a virtuálisgép-ügynök frissítése közben. |
| A virtuálisgép-ügynök telepítésének érvényesítése |<li>Lépjen a *C:\WindowsAzure\Packages* mappába az Azure virtuális gépen. <li>Itt találja a WaAppAgent.exe fájlt.<li> Kattintson jobb gombbal a fájlra, válassza a **Tulajdonságok** parancsot, majd nyissa meg a **Részletek** lapot. A Termék verziószáma mezőben 2.6.1198.718 vagy újabb verziónak kell lennie. |N/A |

### <a name="backup-extension"></a>Backup bővítmény
Miután a virtuálisgép-ügynök telepítve lett a virtuális gépen, az Azure Backup szolgáltatás telepíti a biztonsági mentési bővítményt a virtuálisgép-ügynökhöz. Az Azure Backup szolgáltatás zökkenőmentesen frissíti, és a tartalék mellék javításokkal.

A biztonsági mentési bővítményt a Backup szolgáltatás a virtuális gép futásától függetlenül telepíti. Egy futó virtuális gép adja a legnagyobb esélyt egy alkalmazással konzisztens helyreállítási pont létrehozásának. Ugyanakkor az Azure Backup szolgáltatás folytatja a virtuális gép biztonsági másolatának elkészítését akkor is, ha az ki van kapcsolva és a bővítményt nem lehet telepíteni. Ennek neve offline virtuális gép. Ebben az esetben a helyreállítási pont az *összeomláshoz igazodik* lesz.

## <a name="network-connectivity"></a>Hálózati kapcsolat
A virtuális gép pillanatképek kezeléséhez, a tartalék mellék kapcsolódnia kell az Azure nyilvános IP-címeket. A jobb oldali internetkapcsolat nélkül a virtuális gép HTTP-kérelmek túllépi az időkorlátot, és a biztonsági mentés sikertelen lesz. Ha a központi telepítés rendelkezik korlátozza a hozzáférést (a hálózati biztonsági csoport (NSG), például), majd válassza ki biztosítani azt egy tiszta elérési utat a biztonsági mentési forgalom ezen beállítások valamelyikét:

* [Engedélyezett az Azure datacenter IP-címtartományok](http://www.microsoft.com/en-us/download/details.aspx?id=41653) -talál utasításokat a hogyan számára engedélyezett az IP-címek.
* A forgalom útválasztási HTTP-proxy kiszolgáló telepítése.

Amikor arról dönt, hogy melyik lehetőséget használja, a kompromisszumot kezelhetőség, a tanúsítványhasználat pontos szabályzása és költsége közötti vannak.

| Beállítás | Előnyei | Hátrányok |
| --- | --- | --- |
| Engedélyezett IP-címtartományok |Nincsenek további költségek.<br><br>A hozzáférést egy NSG, használja a <i>Set-AzureNetworkSecurityRule</i> parancsmag. |A befolyásolt kezelése bonyolult IP-címtartományok változnak az idők.<br><br>Azure és az nem csak a teljes hozzáférést biztosít. |
| HTTP-proxy |Részletes szabályozását a proxyval, a tárolási engedélyezett URL-címeket.<br>Virtuális gépek egyetlen pont, Internet-hozzáférést.<br>Nem vonatkozik a Azure IP-címet érintő módosításait. |További költségek a virtuális gépek futtatásához a proxy szoftverrel. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Engedélyezett Azure datacenter IP-címtartományok
* Az engedélyezési lista az Azure datacenter IP-címtartományok, tekintse meg a [Azure-webhelyen](http://www.microsoft.com/en-us/download/details.aspx?id=41653) talál részletes információt az IP-címtartományok, és az utasításokat.
* Szolgáltatás címkék használata az adott régió használatának tárolási kapcsolatok engedélyezése a [szolgáltatás címkék](../virtual-network/security-overview.md#service-tags). Győződjön meg arról, hogy a szabályt, amely lehetővé teszi, hogy a tárfiók eléréséhez nehézségekkel magasabb prioritású, mint a szabály blokkolja-e internet-hozzáféréssel. 

  ![NSG a régió tárolási címkékkel](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

> [!WARNING]
> Tárolási címkék csak a meghatározott régióiba elérhetők, és még csak előzetes verziójúak. A régiók listáját, tekintse meg [címkék szolgáltatás tárolás](../virtual-network/security-overview.md#service-tags)

### <a name="using-an-http-proxy-for-vm-backups"></a>A virtuális gép biztonsági mentésekhez egy HTTP-proxy használatával
Ha a virtuális gépek biztonsági mentéséről, a biztonsági mentési bővítményt a virtuális Gépen a pillanatkép felügyeleti parancsokat küld Azure Storage egy HTTPS API használatával. A tartalék mellék forgalmat a HTTP-proxyn keresztül történő irányításához, mivel ez a nyilvános Internet-hozzáférés beállítása csak összetevő.

> [!NOTE]
> Nincs a proxy szoftver használandó nem ajánlott. Győződjön meg arról, hogy a proxy, amely kompatibilis az alábbi konfigurációs lépéseket a választ.
>
>

Az alábbi példa képen lépéseit mutatja be, a három kvórumbeállítási szükséges HTTP proxyk használatára:

* App virtuális gép továbbítja a HTTP-forgalom a nyilvános interneten keresztül Proxy Virtuálisgép kapcsolódik.
* Proxy VM lehetővé teszi a bejövő forgalom virtuális gépek a virtuális hálózat.
* A hálózati biztonsági csoport (NSG) nevű Elégtelen-zárolási kell egy biztonsági szabály engedélyezése kimenő internetforgalom Proxy virtuális gépről.

A nyilvános internethez való kommunikációhoz HTTP proxyk használatára, kövesse az alábbi lépéseket:

#### <a name="step-1-configure-outgoing-network-connections"></a>1. lépés A kimenő hálózati kapcsolatok konfigurálása
###### <a name="for-windows-machines"></a>A Windows-alapú gépek
Ez a helyi rendszerfiókhoz proxykiszolgálót állítják be.

1. Töltse le [PsExec](https://technet.microsoft.com/sysinternals/bb897553)
2. Futtassa a következő parancs rendszergazda jogú parancssorból

     ```
     psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
     ```
     Megnyílik az internet explorer ablakot.
3. Ugrás a eszközök -> Internet-beállítások -> kapcsolatok LAN-beállítások ->.
4. Ellenőrizze a proxybeállítások helyességét a rendszerfiók. Állítsa be a Proxy IP-cím és port.
5. Zárja be az Internet Explorert.

Beállításához nyújt útmutatást a gépre kiterjedő proxy konfigurációját, és egyetlen kimenő HTTP/HTTPS-forgalmat fog történni.

Ha a telepítő proxykiszolgálót a jelenlegi felhasználói fiók (nem a helyi rendszerfiók), használja a következő parancsfájl SYSTEMACCOUNT alkalmazni:

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Ha "(407) Proxyhitelesítés szükséges" proxy server naplóban, ellenőrizze a hitelesítési helyesen beállítva.
>
>

###### <a name="for-linux-machines"></a>Linux-gépekhez
Adja hozzá a következő sort a ```/etc/environment``` fájlt:

```
http_proxy=http://<proxy IP>:<proxy port>
```

Adja hozzá a következő sorokat a ```/etc/waagent.conf``` fájlt:

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>2. lépés A proxykiszolgáló bejövő kapcsolatok engedélyezése:
1. Nyissa meg a proxykiszolgáló, a Windows tűzfal. A legegyszerűbben úgy lehet hozzáférni a tűzfal fokozott biztonságú Windows tűzfal kereséséhez.
2. A Windows tűzfal párbeszédpanelen kattintson a jobb gombbal **bejövő szabályok** kattintson **új szabály létrehozása...** .
3. Az a **új bejövő szabály varázsló**, válassza ki a **egyéni** választás, a **szabálytípus** kattintson **következő**.
4. A lapon válassza ki a **Program**, válassza a **minden program** kattintson **következő**.
5. Az a **protokoll és portok** lapon adja meg a következő adatokat, majd kattintson **következő**:

   * a *protokolltípus* válasszon *TCP*
   * a *helyi port* válasszon *adott*, az alábbi mezőben adja meg a ```<Proxy Port>``` be van állítva.
   * a *távoli port* válasszon *minden port*

     A varázsló többi egészen a érdekében kattintson, és nevezze el ez a szabály.

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>3. lépés Vegyen fel egy kivételt a az NSG:
Az Azure PowerShell-parancssort írja be a következő parancsot:

Az alábbi parancs hozzáadja az NSG kivételt. Ez a kivétel lehetővé teszi, hogy a TCP-forgalom 10.0.0.5 a bármely portról bármely internetcím a 80-as (HTTP) vagy a 443-as (HTTPS) porton. Ha a nyilvános internethez az adott portra van szüksége, ügyeljen arra, hogy, hogy a port hozzáadása az ```-DestinationPortRange``` is.

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```


*Ezeket a lépéseket ehhez a példához adott neveit és értékeit használja. Használja a neveit és értékeit a központi telepítés megadásakor, vagy Kivágás és részletek beillesztése a kódot.*

Most, hogy ismeri a hálózati kapcsolattal rendelkezik, készen áll készítsen biztonsági másolatot a virtuális Gépet. Lásd: [erőforrás-kezelő telepített virtuális gépek biztonsági mentése](backup-azure-arm-vms.md).

## <a name="questions"></a>Kérdései vannak?
Ha kérdései vannak, vagy van olyan szolgáltatás, amelyről hallani szeretne, [küldjön visszajelzést](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Következő lépések
Most, hogy előkészítette a környezetet az biztonsági mentése a virtuális Gépet, a következő logikai lépésre biztonsági mentés létrehozásához. A tervezési cikk virtuális gépek biztonsági mentéséről további részletes információkat tartalmazza.

* [Készítsen biztonsági másolatot a virtuális gépek](backup-azure-arm-vms.md)
* [A virtuális gép biztonsági mentési infrastruktúra megtervezése](backup-azure-vms-introduction.md)
* [Virtuális gépek biztonsági mentéseinek kezelése](backup-azure-manage-vms.md)
