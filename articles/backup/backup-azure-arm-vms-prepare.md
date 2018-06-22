---
title: 'Az Azure Backup: Biztonsági másolatot készíteni a virtuális gépek előkészítése'
description: Győződjön meg arról, hogy a környezet elő kell készíteni az Azure virtuális gépek biztonsági mentését.
services: backup
author: markgalioto
manager: carmonm
keywords: biztonsági mentések; biztonsági mentése;
ms.service: backup
ms.topic: conceptual
ms.date: 6/21/2018
ms.author: markgal
ms.openlocfilehash: 5aa07d7861413fa0ddc0d5af7aefe828df412b4d
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309146"
---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>A környezet előkészítése a Resource Managerrel üzembe helyezett virtuális gépek biztonsági mentéséhez

Ez a cikk a biztonsági mentése az Azure Resource Manager telepített virtuális gép (VM) a környezet előkészítése a lépéseit ismerteti. A leírt eljárások lépés használja az Azure-portálon. A virtuális gépek biztonsági mentésekor, a biztonsági mentési adatok vagy a helyreállítási pontok vannak tárolva Recovery Services-tároló. Helyreállítási szolgáltatások tárolók klasszikus és Resource Manager telepített virtuális gépek biztonsági mentési adatok tárolására.

> [!NOTE]
> Azure az erőforrások létrehozására és kezelésére két üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md).

Mielőtt védelme (vagy biztonsági mentése) erőforrás-kezelő telepített virtuális gép, győződjön meg arról, az Előfeltételek:

* Hozzon létre vagy jelöljön Recovery Services-tároló *ugyanabban a régióban, mint a virtuális gép*.
* Válassza ki a forgatókönyvet, a biztonsági mentési házirend meghatározása és védelmére határozzák meg.
* Ellenőrizze a egy Virtuálisgép-ügynök telepítése (kiterjesztés) a virtuális gépen.
* Ellenőrizze a hálózati kapcsolatot.
* Linux virtuális gépekhez, ha szeretné testre szabni a biztonsági környezetet az alkalmazáskonzisztens biztonsági mentések, kövesse a [pillanatkép előtti és a pillanatkép utáni parancsfájlok konfigurálásának lépései](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Ha ezek a feltételek már szerepel a környezetben, lépjen a [készítsen biztonsági másolatot a virtuális gépek](backup-azure-arm-vms.md) cikk. Ha szeretné beállítani, vagy ellenőrizze az Előfeltételek bármelyike, ez a cikk végigvezeti Önt a lépéseket.

## <a name="supported-operating-systems-for-backup"></a>A biztonsági mentéshez támogatott operációs rendszerek

 * **Linux**: támogatja az Azure Backup [azokat a terjesztéseket, amelyek Azure hozzá támogatást listája](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), kivéve a CoreOS Linux. Fájlok visszaállítása támogató Linux operációs rendszerek listájáért lásd: [virtuális gép biztonsági mentésből fájlokat helyreállítani](backup-azure-restore-files-from-vm.md#for-linux).

    > [!NOTE] 
    > Más állapotba-a-saját-Linux terjesztésekről működnek, mindaddig, amíg a Virtuálisgép-ügynök érhető el a virtuális gépen, és támogatja a Python létezik-e. Azokat a terjesztéseket, azonban nem támogatott.
    >
 * **Windows Server**, **Windows ügyfél**: Windows Server 2008 R2 vagy Windows 7, régebbi verziói nem támogatottak.


## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Ha a biztonsági mentése és visszaállítása egy virtuális gép korlátozásai
A környezet előkészítése előtt ügyeljen arra, hogy ezek a korlátozások megértése:

* Több mint 16 adatlemezekkel rendelkező virtuális gépek biztonsági mentését nem támogatott.
* A fenntartott IP-cím és a nem definiált végpontot a virtuális gépek biztonsági mentését nem támogatott.
* Nem támogatott Linux egyesített kulcs beállítása (LUKS) titkosítással titkosított Linux virtuális gépek biztonsági mentéséről.
* Nem ajánlott, amelyek tartalmazzák a fürt megosztott kötetei (CSV) vagy kibővített fájlkiszolgáló virtuális gépek biztonsági mentéséről. Ha végzett, sikertelen volt-e a fürt megosztott kötetei szolgáltatás írók várt. A fürt konfigurálása során egy pillanatkép-feladat szereplő összes virtuális gépet is érintő van szükségük. Azure biztonsági mentés nem támogatja a virtuális Gépre kiterjedő konzisztencia. 
* Csatlakoztatott hálózati meghajtók egy virtuális Géphez csatlakozik, nem tartalmazza a biztonsági mentési adatokat.
* Egy meglévő virtuális gép cseréje a visszaállítás során nem támogatott. Ha úgy próbálja visszaállítani a virtuális gép, ha a virtuális gép létezik, a visszaállítási művelet sikertelen.
* Kereszt-régió biztonsági mentését, és a visszaállítás nem támogatottak.
* Miközben vissza konfigurálásának lépésein, győződjön meg arról, hogy a **tűzfalak és a virtuális hálózatok** tárolási fiók beállításait minden hálózati hozzáférés engedélyezése.
* A kijelölt hálózatokhoz, Miután konfigurálta a tűzfal és a virtuális hálózati beállításait a tárfiók, válassza **engedélyezése a Microsoft-szolgáltatások hozzáférésének ezt a tárfiókot megbízható** kivételként az Azure Backup szolgáltatás engedélyezése a korlátozott hálózati tárfiók eléréséhez. Elemszintű helyreállítás nem támogatott korlátozott hálózati storage-fiókok.
* Minden nyilvános régióiba Azure virtuális gépek biztonsági. (Lásd a [ellenőrzőlista](https://azure.microsoft.com/regions/#services) a támogatott régiók.) A régiót, amelyben keres jelenleg nem támogatott, ha már nem jelenik a legördülő listából válassza ki a tároló létrehozása során.
* A tartományvezérlők visszaállítását (DC) virtuális Gépet, amely része egy multi-tartományvezérlő-konfiguráció támogatott csak a PowerShell segítségével. További tudnivalókért lásd: [multi-DC tartományvezérlő visszaállítása](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
* Az engedélyezett írási gyorsító lemezen Snapshot utasítás nem támogatott. Ez a korlátozás blokkolja az Azure Backup szolgáltatás képes-e a virtuális gép összes lemeze egy alkalmazás konzisztens pillanatképe végrehajtásához.
* Az alábbi speciális beállításokkal rendelkező virtuális gépek visszaállításakor csak a PowerShell használatával támogatott. A visszaállítási munkafolyamat a felhasználói felület segítségével létrehozott virtuális gépek nem fognak rendelkezni a hálózati konfigurációt, a visszaállítási művelet befejezése után. További tudnivalókért lásd: [visszaállítását virtuális gépek speciális hálózati konfigurációkkal](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations).
  * Virtuális gépek a terheléselosztó-konfigurációja (belső és külső)
  * Virtuális gépek több foglalt IP-címmel
  * Virtuális gépek több hálózati adapterrel

## <a name="create-a-recovery-services-vault-for-a-vm"></a>A virtuális gépek Recovery Services-tároló létrehozása
Recovery Services-tároló olyan entitás, amely a biztonsági mentések és adott idő alatt létrehozott helyreállítási pontok tárolására. A Recovery Services-tárolónak a biztonsági mentési házirendek a védett virtuális gépek társított is tartalmaz.

Egy Recovery Services-tároló létrehozásához:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A a **Hub** menüjében válassza **Tallózás**, majd írja be **Recovery Services**. Írja be megkezdése előtt, a megadott erőforrások listájának szűrése. Válassza ki **Recovery Services-tárolók**.

    ![Írja be a jelölőnégyzetet, majd válassza a "Recovery Services-tárolók" az eredmények között](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    A Recovery Services-tárolók listája megjelenik.
3. Az a **Recovery Services-tárolók** menü **Hozzáadás**.

    ![Recovery Services-tároló létrehozása – 2. lépés](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    A **Recovery Services-tárolók** ablaktábla megnyitása. A rendszer kérni fogja, hogy információkat biztosítanak a **neve**, **előfizetés**, **erőforráscsoport**, és **hely**.

    !["Recovery Services-tárolók" ablak](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. A **Név** mezőben adjon meg egy egyszerű nevet a tároló azonosításához. A névnek egyedinek kell lennie az Azure-előfizetéshez. Adjon meg egy nevet, 2-50 karakternél. Betűvel kell kezdődnie, és csak betűket, számokat és kötőjeleket tartalmazhat.
5. Válassza ki **előfizetés** az elérhető előfizetések megtekintéséhez. Ha nem biztos abban, hogy melyik előfizetéssel használja, használja az alapértelmezett (vagy javasolt) előfizetés. Nincs több lehetősége csak akkor, ha a munkahelyi vagy iskolai fiókkal társítva a több Azure-előfizetéssel.
6. Válassza ki **erőforráscsoport** erőforráscsoportok elérhető listájának megtekintéséhez, vagy válasszon **új** egy új erőforráscsoport létrehozásához. Teljes információ az erőforráscsoportokkal: [Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md).
7. Válassza ki **hely** jelölje be a tároló földrajzi területét. A tárolónak ugyanabban a régióban *kell* lennie, mint a megvédeni kívánt virtuális gépeknek.

   > [!IMPORTANT]
   > Ha biztos benne, hogy a hely, amelyben a virtuális gép található, a tároló létrehozása párbeszédpanel bezárásához, és nyissa meg a portál virtuális gépek listáját. Ha több régióba olyan virtuális gépek, Recovery Services-tároló létrehozása minden régióban szeretné. Hozza létre a tárolót az első helyen, majd lépjen a következő helyre. Nincs szükség az adhatja meg a storage-fiókok a biztonsági mentési adatok tárolására. A Recovery Services-tároló és az Azure Backup szolgáltatás, amely automatikusan tud kezelni.
   >
   >

8. Kattintson a **Létrehozás** gombra. A Recovery Services-tároló létrehozása eltarthat egy ideig. A portál jobb felső területén állapot értesítések figyelése. A tároló létrehozása után a Recovery Services-tárolók listája jelenik meg. Ha nem látja a tárolóhoz, válassza ki a **frissítése**.

    ![A Backup-tárolók listája](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Most, hogy létrehozta a tárolóját, megismerkedhet a tárreplikáció beállításának módjával.

## <a name="set-storage-replication"></a>Tárolási replikációs
A tárolási replikációs beállítás lehetővé teszi a georedundáns tárolás és a helyileg redundáns tárolás közül választhat. Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik. Hagyja meg a beállítás, az elsődleges Backup georedundáns tárolást. Ha azt szeretné, hogy egy olcsóbb beállítása, amely nem a tartós, válassza ki azt a helyileg redundáns tárolás.

A tárreplikációs beállítás szerkesztése:

1. Az a **Recovery Services-tárolók** ablaktáblán válassza ki a tárolóban.
    A tároló kiválasztásakor a **beállítások** (melynek a neve, a tároló tetején) és a tároló részletei ablaktábla megnyitása.

   ![Válassza ki a tároló a mentési tárolók listájáról](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. Az a **beállítások** panelen görgessen le a függőleges csúszka segítségével a **kezelése** szakaszt, és válassza ki **biztonsági infrastruktúra**. Az a **általános** szakaszban jelölje be **biztonsági mentési konfigurációhoz**. Az a **biztonsági mentési konfigurációhoz** ablaktáblán válassza ki a tárolási replikációs beállítás a tároló számára. Alapértelmezés szerint a tárolója georedundáns tárolással rendelkezik.

   ![A Backup-tárolók listája](./media/backup-azure-arm-vms-prepare/full-blade.png)

   Azure biztonságimásolat-tároláshoz-végpontként használata, továbbra is georedundáns tárolás használata. Egy nem elsődleges biztonsági másolatok tárolásának végpontként használata Azure, válassza ki a helyileg redundáns tárolás. A tárolási lehetőségeket nyújt további információt a [Azure Storage replikáció – áttekintés](../storage/common/storage-redundancy.md).

3. Ha módosította a tárolási replikációs típusát, válassza ki a **mentése**.
    
Miután a tárolási lehetőséget választja a tároló számára, készen áll a virtuális gép társítása a tárolóban. A hozzárendelés megkezdéséhez fel kell fedezni és regisztrálni kell az Azure virtuális gépeket.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Biztonsági mentési cél házirendjének beállítása és határozzák meg védelméhez
Mielőtt a Recovery Services-tároló, a felderítési folyamat bármely az előfizetéshez történő hozzáadása új virtuális gépek azonosításához futtassa a virtuális gépek regisztrálása. A felderítési folyamat Azure lekérdezi a listán szereplő virtuális gépek az előfizetéshez. Ha új virtuális gépek találhatók, a portál megjeleníti a felhőszolgáltatás neve és a kapcsolódó régióban. Az Azure portálon a *forgatókönyv* kell beírnia a Recovery Services-tárolóban. *Házirend* rendszer milyen gyakran és mikor készít-e a helyreállítási pontok ütemezését. A házirend emellett tartalmazza a helyreállítási pontok megőrzési tartományát.

1. Ha már meg van nyitva egy Recovery Services-tároló, folytassa a 2. lépéssel. Ha még nem rendelkezik nyissa meg a Recovery Services-tároló, nyissa meg a [Azure-portálon](https://portal.azure.com/). Az a **Hub** menü **további szolgáltatások**.

   a. Az erőforrások listájába írja be a következőt: **Recovery Services**. Írja be megkezdése előtt, a bemeneti szűri. Amikor látja **Recovery Services-tárolók**, válassza ki azt.

      ![Írja be a jelölőnégyzetet, majd válassza a "Recovery Services-tárolók" az eredmények között](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

      A Recovery Services-tárolók listája megjelenik. Ha az előfizetésében nincsenek tárolók, a lista létrehozási üres.

      ![A Recovery Services-tároló listájának nézete](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   b. A Recovery Services-tárolók listájából válasszon ki egy tárolót.

      A **beállítások** ablaktábla és a tároló irányítópult a választott tároló megnyitása.

      ![Beállítások és a tároló irányítópult](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
2. Válassza ki a tároló irányítópult menü **biztonsági mentés**.

   ![Biztonsági mentés gombra](./media/backup-azure-arm-vms-prepare/backup-button.png)

   A **biztonsági mentés** és **biztonsági mentési cél** ablaktábla megnyitása.

3. Az a **biztonsági mentési cél** panelen állítsa **a számítási feladatok futtató?** , **Azure** és **miről szeretne biztonsági másolatot készíteni?** ,  **Virtuális gép**. Ezután kattintson az **OK** gombra.

   ![Biztonsági mentés és a biztonsági mentési cél ablaktábla](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Ez a lépés a Virtuálisgép-bővítmény regisztrálja a tárolóban. A **biztonsági mentési cél** ablaktáblát, és a **biztonsági mentési házirend** ablaktábla megnyitása.

   !["Mentés" és "Biztonsági mentés házirend" ablaktáblák](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
4. Az a **biztonsági mentési házirend** ablaktáblán válassza ki a biztonsági mentési házirendet, amelyet szeretne alkalmazni a tárolóba.

   ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

   Az alapértelmezett házirend részletei megtalálhatók a legördülő menüben. Ha új házirendet hozna létre, válassza az **Új létrehozása** elemet a legördülő menüből. A biztonsági mentési házirendek meghatározását segítő utasításokat itt találja: [Biztonsági mentési házirend meghatározása](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Válassza ki **OK** társítja a biztonsági mentési házirendet a tárolóban.

   A **biztonsági mentési házirend** ablaktáblát, és a **válassza ki a virtuális gépek** ablaktábla megnyitása.
5. Az a **válassza ki a virtuális gépek** ablaktáblán válassza ki a megadott házirend társítani, és jelölje ki a virtuális gépek **OK**.

   !["A virtuális gépek kiválasztása" ablak](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

   Megtörténik a kiválasztott virtuális gép ellenőrzése. Ha nem látja a kívánt virtuális gépek, ellenőrizze, hogy a virtuális gépek és a Recovery Services-tárolónak ugyanabban a Azure régióban. Ha még nem látja a virtuális gépek, ellenőrizze, hogy nem már védve legyenek egy másik tárolóban. A tároló irányítópult jeleníti meg a régió, amelyen a Recovery Services-tároló létezik.

6. Most, hogy meg van adva a tároló összes beállítás a **biztonsági mentési** ablaktáblán válassza előbb **biztonságimásolat-készítés engedélyezése**. Ez a lépés telepíti a szabályzatot a tároló és a virtuális gépeket. Ez a lépés nem hoz létre az első helyreállítási pont, a virtuális géphez.

   !["Biztonsági mentés engedélyezése" gombra](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Miután sikeresen engedélyezte a biztonsági mentés, a biztonsági mentési házirend ütemezés szerint fog futni. Ha szeretné létrehozni a virtuális gépek biztonsági mentése, ekkor megjelenik egy igény szerinti biztonságimásolat-készítő feladat [időt. a biztonsági mentési feladat](./backup-azure-vms-first-look-arm.md#initial-backup).

Ha problémába ütközik a virtuális gép rögzítése, tekintse meg a következő információkat a Virtuálisgép-ügynök telepítése és a hálózati kapcsolatot. Ha az Azure-ban létrehozott virtuális gépek védelmét valószínűleg nem kell a következő információkat. De ha áttelepítette a virtuális gépek Azure-ba, lehet, hogy a Virtuálisgép-ügynök megfelelően telepítve, és, hogy a virtuális gép kommunikálhatnak-e a virtuális hálózat.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>A Virtuálisgép-ügynök telepítése a virtuális gépen
A biztonsági mentés bővítmény használatához az Azure [Virtuálisgép-ügynök](../virtual-machines/extensions/agent-windows.md) telepíteni kell az Azure virtuális géphez. Ha a virtuális gép létrehozása az Azure piactérről, a Virtuálisgép-ügynök már telepítve a virtuális gép. 

Olyan esetekben, ahol a következő információ *nem* használatával egy virtuális gép létrehozása az Azure piactérről. **Például végezte az áttelepítést egy virtuális Gépet egy olyan helyszíni adatközpontban. Ebben az esetben a Virtuálisgép-ügynök telepítve kell lennie ahhoz, hogy a virtuális gép védelmét.**

**Megjegyzés:**: a Virtuálisgép-ügynök telepítése után is használnia kell az Azure PowerShell frissíteni a ProvisionGuestAgent tulajdonság, Azure tudja, hogy a virtuális Gépnek az ügynök telepítve legyen. 

Ha problémába ütközik az Azure virtuális gép biztonsági mentéséről, az alábbi táblázat segítségével ellenőrizze, hogy az Azure Virtuálisgép-ügynök megfelelően telepítve van a virtuális gépen. A táblázat további információt a Windows és Linux virtuális gépek a Virtuálisgép-ügynök.

| **Művelet** | **Windows** | **Linux** |
| --- | --- | --- |
| A virtuálisgép-ügynök telepítése |Töltse le és telepítse az [ügynök MSI-t](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek. |<li> Telepítse a legújabb [Linux-ügynök](../virtual-machines/extensions/agent-linux.md). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek. Azt javasoljuk, hogy a terjesztési tárházból ügynök telepítése. A Microsoft **nem javasoljuk** közvetlenül a githubból telepítése Linux Virtuálisgép-ügynök.  |
| A virtuálisgép-ügynök frissítése |A virtuálisgép-ügynök frissítése a [virtuálisgép-ügynök bináris fájljainak](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) újratelepítéséből áll. <br>Győződjön meg róla, hogy nem fut biztonsági mentési művelet a virtuálisgép-ügynök frissítése közben. |Kövesse a [linuxos virtuálisgép-ügynök frissítését](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ismertető szakasz utasításait. Azt javasoljuk, hogy a terjesztési tárházból ügynök frissítése. A Microsoft **nem javasoljuk** közvetlenül a githubból frissítése Linux Virtuálisgép-ügynök.<br>Győződjön meg róla, hogy nem fut biztonsági mentési művelet a virtuálisgép-ügynök frissítése közben. |
| A virtuálisgép-ügynök telepítésének érvényesítése |<li>Lépjen a *C:\WindowsAzure\Packages* mappába az Azure virtuális gépen. <li>Itt találja a WaAppAgent.exe fájlt.<li> Kattintson jobb gombbal a fájlra, válassza a **Tulajdonságok** parancsot, majd nyissa meg a **Részletek** lapot. A Termék verziószáma mezőben 2.6.1198.718 vagy újabb verziónak kell lennie. |– |

### <a name="backup-extension"></a>Backup bővítmény
A virtuális gépen a Virtuálisgép-ügynök telepítése után az Azure Backup szolgáltatás a Virtuálisgép-ügynök telepítése a tartalék mellék. A biztonsági mentési szolgáltatás zökkenőmentesen frissíti, és a tartalék mellék javításokkal.

A biztonsági mentési szolgáltatás a biztonsági mentési bővítmény telepítését, a virtuális gép fut-e. Egy futó virtuális gép adja a legnagyobb esélyt egy alkalmazással konzisztens helyreállítási pont létrehozásának. Azonban a biztonsági mentési szolgáltatás továbbra is készítsen biztonsági másolatot a virtuális gép még akkor is, ha ki van kapcsolva, és a bővítmény telepítése sikertelen volt. Ez más néven *offline állapotú virtuális gép*. Ebben az esetben a helyreállítási pont az *összeomláshoz igazodik* lesz.

## <a name="establish-network-connectivity"></a>Hálózati kapcsolatok létrehozása
A virtuális gép pillanatképek kezeléséhez, a tartalék mellék kapcsolódnia kell az Azure nyilvános IP-címeket. A jobb oldali internetkapcsolat nélkül a virtuális gép HTTP-kérelmek túllépi az időkorlátot, és a biztonsági mentés sikertelen lesz. Ha a központi telepítés rendelkezik-hozzáférés korlátozása – a hálózati biztonsági csoport (NSG) keresztül például--egyikén ezeket a beállításokat meg egyértelmű elérési útvonalát a biztonsági mentési forgalmat:

* [Engedélyezett az Azure datacenter IP-címtartományok](http://www.microsoft.com/en-us/download/details.aspx?id=41653).
* A forgalom útválasztási HTTP-proxy kiszolgáló telepítése.

Amikor eldönti, hogy melyik lehetőséget használja, a kompromisszumot kezelhetőség, a tanúsítványhasználat pontos szabályzása és költsége közötti vannak.

| Beállítás | Előnyei | Hátrányok |
| --- | --- | --- |
| Engedélyezett IP-címtartományok |Nincsenek további költségek.<br><br>A hozzáférést egy NSG, használja a **Set-AzureNetworkSecurityRule** parancsmag. |Az érintett rendszer kezelése bonyolult IP-címtartományok változnak az idők.<br><br>Azure és az nem csak a teljes hozzáférést biztosít. |
| HTTP proxyk használatára |A proxy a keresztül a tárolási a tanúsítványhasználat pontos szabályzása URL-címek használata engedélyezett.<br><br>Virtuális gépek egyetlen pont, internet-hozzáférést.<br><br>Nem vonatkozik a Azure IP-címet érintő módosításait. |További költségek a virtuális gépek futtatásához a proxy szoftverrel. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Engedélyezett Azure datacenter IP-címtartományok
Az Azure datacenter IP-címtartományok, tekintse meg az engedélyezett a [Azure-webhelyen](http://www.microsoft.com/en-us/download/details.aspx?id=41653) talál részletes információt az IP-címtartományok és az utasításokat.

Segítségével engedélyezheti az adott régió tárolási kapcsolatok [címkék szolgáltatás](../virtual-network/security-overview.md#service-tags). Győződjön meg arról, hogy a szabály, amely lehetővé teszi a hozzáférést a tárolási fiók rendelkezik-e a magasabb prioritású, mint a szabály, amely blokkolja az internet-hozzáféréssel. 

![NSG a régió tárolási címkékkel](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

A következő videó bemutatja, hogyan a lépéseit szolgáltatás címkék konfigurálása: 

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]

> [!WARNING]
> Tárolási szolgáltatás címkék csak a meghatározott régióiba elérhetők, és még csak előzetes verziójúak. Régiók listáját lásd: [címkék szolgáltatás tárolási](../virtual-network/security-overview.md#service-tags).

### <a name="use-an-http-proxy-for-vm-backups"></a>A virtuális gép biztonsági mentésekhez HTTP proxyk használatára
Ha biztonsági mentést készít a virtuális gép, a biztonsági mentési bővítményt a virtuális Gépen a pillanatkép felügyeleti parancsokat küld Azure Storage egy HTTPS API használatával. A tartalék mellék forgalmat a HTTP-proxyn keresztül történő irányításához, mivel a nyilvános internet-hozzáférés beállítása csak összetevő már.

> [!NOTE]
> Nem ajánlott a megadott proxy szoftvert kell használnia. Győződjön meg arról, hogy választ, amely kompatibilis a konfigurációs lépéseket a proxy olvashat.
>
>

A következő példa kép lépéseit mutatja be, a három kvórumbeállítási szükséges HTTP proxyk használatára:

* Az alkalmazás virtuális gép útvonalakat minden HTTP-forgalom a nyilvános interneten keresztül éri el a virtuális gép kapcsolódik.
* A proxy VM lehetővé teszi a bejövő forgalom virtuális gépek a virtuális hálózat.
* A hálózati biztonsági csoport nevű Elégtelen-zárolási kell a biztonsági szabály, amely lehetővé teszi a kimenő internetforgalom érkezett a virtuális gép.

A nyilvános internethez folytatott kommunikációhoz HTTP-proxy használatához kövesse az alábbi lépéseket.

> [!NOTE]
> Ezeket a lépéseket ehhez a példához adott neveit és értékeit használja. Ha most megadása (vagy beillesztése) részletei be a kódját, és az értékeket a telepítéshez használni.

#### <a name="step-1-configure-outgoing-network-connections"></a>1. lépés: A kimenő hálózati kapcsolatok konfigurálása
###### <a name="for-windows-machines"></a>A Windows-alapú gépek
Ez az eljárás beállítja a proxykiszolgálót a helyi rendszerfiók.

1. Töltse le [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Nyissa meg az Internet Explorer, a következő parancsot egy rendszergazda jogú parancssorból való futtatásával:

    ```
    psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
    ```

3. Az Internet Explorerben nyissa meg a **eszközök** > **Internetbeállítások** > **kapcsolatok** > **LAN-beállítások**.
4. Ellenőrizze a proxybeállítások helyességét a rendszerfiók. Állítsa be a proxy IP- és a port.
5. Zárja be az Internet Explorert.

A következő parancsfájl állít be egy érvényes proxybeállításait, és használja a minden kimenő HTTP vagy HTTPS-forgalmat. Ha az aktuális felhasználói fiók (nem a helyi rendszerfiók) proxy kiszolgáló beállítását követően ezen parancsfájl segítségével SYSTEMACCOUNT alkalmazhatja azokat.

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Ha a proxy server naplóban "(407) Proxyhitelesítés szükséges", ellenőrizze, hogy a hitelesítési megfelelően van-e beállítva.
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

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>2. lépés: Annak engedélyezése, hogy a bejövő kapcsolatok a proxykiszolgáló
1. Nyissa meg a proxykiszolgáló, a Windows tűzfal. A tűzfal eléréséhez legegyszerűbb módja a keresendő **fokozott biztonságú Windows tűzfal**.
2. Az a **fokozott biztonságú Windows tűzfal** párbeszédpanel, kattintson a jobb gombbal **bejövő szabályok** válassza **új szabály**.
3. Az új bejövő szabály varázsló a a **szabálytípus** lapon jelölje be a **egyéni** lehetőséget, majd válassza ki **következő**.
4. Az a **Program** lapon jelölje be **minden program** válassza **következő**.
5. Az a **protokoll és portok** lapon adja meg a következő adatokat, és válassza **következő**:
   * A **protokolltípus**, jelölje be **TCP**.
   * A **helyi port**, jelölje be **adott**. A következő mezőbe adja meg a konfigurált proxyport száma.
   * A **távoli port**, jelölje be **minden port**.

A varázsló többi fogadja el az alapértelmezett beállításokat, amíg elér vége. Ez a szabály majd adjon egy nevet. 

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>3. lépés: Vegyen fel egy kivételt a az NSG
Az alábbi parancs hozzáadja az NSG kivételt. Ez a kivétel lehetővé teszi, hogy a TCP-forgalom 10.0.0.5 a bármely portról bármely internetcím a 80-as (HTTP) vagy a 443-as (HTTPS) porton. Ha egy adott portot a nyilvános interneten van szüksége, ügyeljen arra, hogy, hogy a port hozzáadása ```-DestinationPortRange```.

Az Azure PowerShell-parancssort írja be a következő parancsot:

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

## <a name="questions"></a>Kérdései vannak?
Ha kérdése van, vagy ha az összes olyan szolgáltatás, amely meg szeretné tekinteni a része, [visszajelzést küldhet](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>További lépések
Most, hogy előkészítette a környezetet az biztonsági mentése a virtuális Gépet, a következő logikai lépésre biztonsági mentés létrehozásához. A tervezési cikk virtuális gépek biztonsági mentéséről további részletes információkat tartalmazza.

* [Virtuális gépek biztonsági mentése](backup-azure-arm-vms.md)
* [A virtuális gép biztonsági mentési infrastruktúra megtervezése](backup-azure-vms-introduction.md)
* [Virtuális gépek biztonsági mentéseinek kezelése](backup-azure-manage-vms.md)
