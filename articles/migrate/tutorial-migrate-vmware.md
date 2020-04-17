---
title: VMware virtuális gépek áttelepítése ügynök nélküli Azure Áttelepítési kiszolgáló áttelepítése
description: Ismerje meg, hogyan futtathat ügynök nélküli áttelepítést a VMware virtuális gépek az Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 4612c9b0ea2ef8d53b0c04f47628f3789705d833
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535315"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>VMware virtuális gépek áttelepítése az Azure-ba (ügynök nélküli)

Ez a cikk bemutatja, hogyan telepítheti át a helyszíni VMware virtuális gépeket az Azure-ba ügynök nélküli áttelepítés használatával az Azure Áttelepítési kiszolgáló áttelepítése eszközzel.

[Az Azure Migrate](migrate-services-overview.md) központi központot biztosít a helyszíni alkalmazások és számítási feladatok, valamint az AWS/GCP virtuálisgép-példányok Azure-ba való felderítésének, értékelésének és áttelepítésének nyomon követéséhez. A központ Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint külső független szoftverszállítói (ISV) ajánlatokat.

Ez az oktatóanyag a harmadik egy sorozatban, amely bemutatja, hogyan értékelheti és telepítheti át a VMware virtuális gépeket az Azure-ba az Azure Áttelepítési kiszolgáló értékelése és áttelepítése használatával. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A virtuális gépek előkészítése az áttelepítéshez.
> * Adja hozzá az Azure Migration Server áttelepítési eszköz.
> * Fedezze fel az áttelepíteni kívánt virtuális gépeket.
> * Kezdje el replikálni a virtuális gépeket.
> * Futtasson egy tesztáttelepítést, hogy minden a várt módon működjön.
> * Futtasson egy teljes virtuális gép-áttelepítést.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/pricing/free-trial/) mielőtt elkezdené.

## <a name="migration-methods"></a>Áttelepítési módszerek

A VMware virtuális gépek az Azure-ba az Azure-áttelepítési kiszolgáló áttelepítése eszközzel. Ez az eszköz néhány lehetőséget kínál a VMware VM-áttelepítéshez:

- Áttelepítés ügynök nélküli replikációval. Telepítse a virtuális gépeket anélkül, hogy bármit is telepítenie kellene rájuk.
- Áttelepítés ügynökkel replikációra. Telepítsen egy ügynököt a virtuális gépre replikációra.

Annak eldöntéséhez, hogy ügynök nélküli vagy ügynökalapú áttelepítést szeretne-e használni, tekintse át az alábbi cikkeket:

- [Ismerje meg, hogyan](server-migrate-overview.md) működik az ügynök nélküli áttelepítés, és [hasonlítsa össze az áttelepítési módszereket.](server-migrate-overview.md#compare-migration-methods)
- Olvassa el ezt a [cikket,](tutorial-migrate-vmware-agent.md) ha az ügynök alapú módszert szeretné használni.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

1. Töltse ki a sorozat [első oktatóanyagát az](tutorial-prepare-vmware.md) Azure és a VMware áttelepítéshez való beállításához. Pontosabban, ez a bemutató meg kell:
    - [Készítse elő az Azure-t](tutorial-prepare-vmware.md#prepare-azure) az áttelepítésre.
    - [Készítse elő a helyszíni környezetet](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) az áttelepítéshez.
    
2. Azt javasoljuk, hogy az Azure Áttelepítési kiszolgáló értékelésével próbálja meg értékelni a VMware virtuális gépeket, mielőtt áttelepülne az Azure-ba. Az értékelés beállításához fejezze be a sorozat [második oktatóanyagát.](tutorial-assess-vmware.md) Ha nem szeretné felmérni a virtuális gépeket, kihagyhatja ezt az oktatóanyagot. Bár azt javasoljuk, hogy próbálja ki az értékelést, de nem kell elvégeznie egy értékelést, mielőtt megpróbálna egy áttelepítést.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Az Azure Áttelepítési kiszolgáló eszközének hozzáadása

Adja hozzá az Azure Áttelepítés:Kiszolgáló áttelepítése eszközt.

- Ha követte a második bemutató, hogy [értékelje VMware VMs](/tutorial-assess-vmware.md), akkor megy előre, és adja hozzá az eszközt.
- Ha nem követte a második oktatóanyag, [kövesse az alábbi utasításokat](how-to-add-tool-first-time.md) egy Azure Migrate projekt beállítása.  Az Azure Migrate:Server Áttelepítés eszköz hozzáadása a projekt létrehozásakor.

Ha van egy projekt beállítása, adja hozzá az eszközt az alábbiak szerint:

1. Az Azure Migrate projektben kattintson **az Áttekintés gombra.** 
2. A **Felderítés, a kiszolgálók felmérése és áttelepítése csoportban**kattintson **a Kiszolgálók felmérése és áttelepítése**elemre.

     ![Kiszolgálók felmérése és áttelepítése](./media/tutorial-migrate-vmware/assess-migrate.png)

3. Az **Áttelepítési eszközök csoportban**válassza **a Kattintson ide lehetőséget, ha áttelepítésre kész ként szeretne migrálni.**

    ![Eszköz kijelölése](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. Az eszközök listájában válassza az **Azure Áttelepítés: Kiszolgálóáttelepítés hozzáadása** > **eszközt.**

    ![Kiszolgálómigrálási eszköz](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Az Azure Migrate-berendezés beállítása

Az Azure Migrate Server Migration egy könnyű VMware vm-készüléket futtat. A készülék virtuális gép felderítése, és elküldi a virtuális gép metaadatait és teljesítményadatait az Azure Migrate:Server Áttelepítés. Ugyanezt a készüléket használja az Azure Migrate:Server Assessment eszköz is a VMware virtuális gépek ügynök nélküli áttelepítéséhez.

- Ha követte az [oktatóanyagot a VMware virtuális gépek értékeléséhez,](tutorial-assess-vmware.md)akkor már beállította a készüléket az oktatóanyag során.
- Ha nem követte az oktatóanyagot, most beállíthatja a készüléket az alábbi módszerek egyikével:
    - VMware virtuális gép en egy letöltött OVA-sablon [használatával.](how-to-set-up-appliance-vmware.md)
    - Állítsa be a VMware virtuális gép vagy fizikai gép egy PowerShell telepítő parancsfájlok. [Ezt a módszert](deploy-appliance-script.md) kell használni, ha nem tud beállítani egy virtuális gép egy OVA sablon használatával, vagy ha az Azure-kormányzat.

A készülék létrehozása után ellenőrizze, hogy csatlakozhat-e az Azure Migrate:Server Assessment alkalmazáshoz, konfigurálhatja-e először, és regisztrálja az Azure Migrate projekttel.


## <a name="prepare-vms-for-migration"></a>Virtuális gépek előkészítése áttelepítésre

Az Azure Migrate néhány virtuális gép-módosításra van szükség annak biztosításához, hogy a virtuális gépek áttelepíthetők legyenek az Azure-ba.

- Egyes operációs rendszerek esetén az Azure Migrate automatikusan legyártja ezeket a módosításokat. [További információ](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms)
- Ha olyan virtuális gép áttelepítése, amely nem rendelkezik ezen operációs rendszerek egyikével, kövesse az utasításokat a virtuális gép előkészítéséhez.
- Fontos, hogy ezeket a módosításokat az áttelepítés megkezdése előtt hajtsa végre. Ha a virtuális gép áttelepítése a módosítás előtt, a virtuális gép nem indul el az Azure-ban.
- A helyszíni virtuális gépeken végrehajtott konfigurációs módosítások replikálódnak az Azure-ba, miután a virtuális gép replikációja engedélyezve van. Győződjön meg arról, hogy a módosítások replikálása, győződjön meg arról, hogy a helyreállítási pont áttelepíti későbbi, mint az idő, amikor a konfigurációs módosítások történtek a helyszínen.


### <a name="prepare-windows-server-vms"></a>Windows Server virtuális gépek előkészítése

**Művelet** | **Részletek** | **Utasítások**
--- | --- | ---
Győződjön meg arról, hogy az Azure virtuális gép Windows-kötetek ugyanazokat a meghajtóbetűjel-hozzárendeléseket használják, mint a helyszíni virtuális gép. | Konfigurálja a SAN-házirendet online összesként. | 1. Jelentkezzen be a virtuális gépegy rendszergazdai fiókot, és nyisson meg egy parancsablakot.<br/> 2. Írja be a **diskpart** típust a Diskpart segédprogram futtatásához.<br/> 3. Írja be **a SAN POLICY=OnlineAll típust**<br/> 4. Írja be a Kilépés parancsot a Diskpart elhagyásához, és zárja be a parancssort.
Az Azure soros hozzáférési konzoljának engedélyezése az Azure virtuális géphez | Ez segít a hibaelhárításban. Nem kell újraindítania a virtuális gépet. Az Azure virtuális gép a lemezkép használatával indul, és ez egyenértékű az új virtuális gép újraindításával. | Az engedélyezéshez kövesse [az alábbi utasításokat.](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console)
Hyper-V vendégintegráció telepítése | Windows Server 2003 rendszert futtató gépek áttelepítése esetén telepítse a Hyper-V vendégintegrációs szolgáltatásokat a virtuális gép operációs rendszerére. | [További információ](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Távoli asztal | Engedélyezze a Távoli asztal szolgáltatást a virtuális gépen, és ellenőrizze, hogy a Windows tűzfal nem blokkolja-e a távoli asztali hozzáférést a hálózati profilokon. | [További információ](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Linuxos virtuális gépek előkészítése

**Művelet** | **Részletek** 
--- | --- | ---
Hyper-V Linux integrációs szolgáltatások telepítése | A Legtöbb új verzió a Linux disztribúciók ez alapértelmezés szerint tartalmazza.
Építse újra a Linux init lemezképet, hogy tartalmazza a szükséges Hyper-V illesztőprogramokat | Ez biztosítja, hogy a virtuális gép elindul az Azure-ban, és csak bizonyos disztribúciókon szükséges.
Az Azure soros konzolnaplózásának engedélyezése | Ez segít a hibaelhárításban. Nem kell újraindítania a virtuális gépet. Az Azure virtuális gép a lemezkép használatával indul, és ez egyenértékű az új virtuális gép újraindításával.<br/> Az engedélyezéshez kövesse [az alábbi utasításokat.](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console)
Eszköztérképfájl frissítése | Az eszköznévvel rendelkező eszközleképezési fájl frissítése kötettársításokra, állandó eszközazonosítók használatához
Fstab bejegyzések frissítése | A bejegyzések frissítése állandó kötetazonosítók használatához.
Udev-szabály eltávolítása | Távolítson el minden olyan udev szabályt, amely a mac-cím alapján fenntartja a kapcsolat neveket stb.
Hálózati adapterek frissítése | Frissítse a hálózati adaptereket, hogy megkapja az IP-címet a DHCP alapján.
Ssh engedélyezése | Győződjön meg arról, hogy az ssh engedélyezve van, és az sshd szolgáltatás automatikusan elindul újraindításkor.<br/> Győződjön meg arról, hogy a bejövő ssh-kapcsolatkérelmeket nem blokkolja az operációs rendszer tűzfala vagy parancsfájlokkal kezelhető szabályai.

[Kövesse ezt a cikket,](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) amely ismerteti ezeket a lépéseket egy Linux virtuális gép azure-beli futtatásához, és utasításokat tartalmaz néhány népszerű Linux-disztribúciók.  


## <a name="replicate-vms"></a>Virtuális gépek replikálása

A felderítés befejeztével elkezdheti a VMware virtuális gépek replikálását az Azure-ba. 

> [!NOTE]
> Legfeljebb 10 gépet replikálhat együtt. Ha több replikálnia kell, akkor azokat egyszerre 10-es kötegekben replikálja. Ügynök nélküli áttelepítés esetén legfeljebb 100 egyidejű replikáció futtatható.

1. Az Azure Migrate projekt > **az** **Azure Áttelepítés: Kiszolgálóáttelepítés**, kattintson a **Replikálás gombra.**

    ![Virtuális gépek replikálása](./media/tutorial-migrate-vmware/select-replicate.png)

2. A **Replikálás**, > **Forrásbeállítások** > **Virtualizáltak a gépek?** területen válassza az **Igen, a VMware vSphere szolgáltatással** lehetőséget.
3. A **Helyszíni berendezés** területen válassza ki a beállított Azure Migrate-berendezés nevét, majd kattintson az **OK** gombra. 

    ![Forrásbeállítások](./media/tutorial-migrate-vmware/source-settings.png)

    - Ez a lépés feltételezi, hogy már beállított egy készüléket, amikor befejezte az oktatóanyag.
    - Ha még nem állított be készüléket, kövesse a [cikkben](how-to-set-up-appliance-vmware.md)található utasításokat.

4. A **Virtuális gépek** területen jelölje ki a replikálni kívánt gépeket.
    - Ha futtatta a virtuális gépek értékelését, alkalmazhatja az eredményekben szereplő, a virtuális gépek méretezésére és a lemeztípusra (prémium/standard) vonatkozó javaslatokat. Ehhez a **Importálja a migrálási beállításokat az Azure Migrate-értékelésből?** területen válassza az **Igen** lehetőséget.
    - Ha nem futtatott értékelést, vagy nem szeretné használni az értékelés beállításait, válassza a **Nem** lehetőséget.
    - Ha az értékelés használatát választotta, válassza ki a virtuálisgép-csoportot és az értékelés nevét.

    ![Értékelés kiválasztása](./media/tutorial-migrate-vmware/select-assessment.png)

5. A **Virtuális gépek** között igény szerint keressen a virtuális gépekre, és válassza ki a gépeket, amelyeket migrálni szeretne. Ezután kattintson a **Tovább: Célbeállítások gombra.**

    ![Virtuális gépek kijelölése](./media/tutorial-migrate-vmware/select-vms.png)

6. A **Célbeállítások** között válassza ki az előfizetést és a célrégiót, ahova migrálni szeretne, majd adja meg az erőforráscsoportot, amelyben az Azure-beli virtuális gépek megtalálhatóak lesznek a migrálást követően. A **Virtuális hálózat** területen válassza ki az Azure-beli virtuális hálózatot/alhálózatot, amelyhez az Azure-beli virtuális gépek a migrálást követően csatlakoznak majd.
7. Az **Azure Hybrid Benefit** területen:

    - Válassza a **Nem** lehetőséget, ha nem szeretné alkalmazni az Azure Hybrid Benefit szolgáltatást. Kattintson a **Tovább** gombra.
    - Válassza az **Igen** lehetőséget, ha aktív frissítési garanciával vagy Windows Server-előfizetéssel rendelkező gépei vannak, és alkalmazni szeretné az előnyöket a migrált gépekre. Kattintson a **Tovább** gombra.

    ![Célbeállítások](./media/tutorial-migrate-vmware/target-settings.png)

8. A **Számítás** területen ellenőrizze a virtuális gép nevét, méretét, az operációs rendszer lemeztípusát és a rendelkezésreállási csoportot. A virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

    - **Virtuális gép mérete:** Ha értékelési javaslatokat használ, a virtuális gép mérete legördülő tartalmazza az ajánlott méretet. Egyéb esetben az Azure Migrate az Azure-előfizetésben található leginkább megfelelő érték alapján választja ki a méretet. Alternatív megoldásként az **Azure-beli virtuális gép mérete** területen manuálisan is kiválaszthatja a méretet. 
    - **OPERÁCIÓS RENDSZER lemeze**: Adja meg a virtuális gép operációs rendszerének (rendszerindító) lemezét. Az operációsrendszer-lemez az a lemez, amelyen az operációs rendszer rendszerbetöltője és telepítője található. 
    - **Rendelkezésre állási készlet:** Ha a virtuális gép kell egy Azure rendelkezésre állási csoport áttelepítés után, adja meg a készlet. A csoportnak a migrálás során megadott cél-erőforráscsoportban kell lennie.

    ![Virtuális gép számítási beállításai](./media/tutorial-migrate-vmware/compute-settings.png)

9. A **Lemezek** területen adja meg, hogy a virtuálisgép-lemezek replikálva legyenek-e az Azure-ba, majd válassza ki a lemez típusát (standard SSD/HDD vagy prémium felügyelt lemez) az Azure-ban. Kattintson a **Tovább** gombra.
    - Kizárhat lemezeket a replikációból.
    - Ha kizárja a lemezeket, azok nem lesznek jelen az Azure-beli virtuális gépen a migrációt követően. 

    ![Lemezek](./media/tutorial-migrate-vmware/disks.png)

10. Az **Áttekintés és a replikáció megkezdése** területen tekintse át a beállításokat, majd kattintson a **Replikálás** gombra a kiszolgálók kezdeti replikálásának elindításához.

> [!NOTE]
> A replikáció beállításait a replikáció megkezdése előtt bármikor frissítheti a**Replikálógépek** **kezelése** > párbeszédpanelen. A replikáció kezdete után a beállítások már nem módosíthatók.

### <a name="provisioning-for-the-first-time"></a>Első alkalommal történő kiépítés

Ha ez az első virtuális gép, amelyet az Azure Migrate projektben replikál, az Azure Migrate Server Migration automatikusan leálja ezeket az erőforrásokat ugyanabban az erőforráscsoportban, mint a projekt.

- **Szolgáltatásbusz:** Az Azure Áttelepítési kiszolgáló áttelepítése a szolgáltatásbusz segítségével küld replikációs vezénylési üzeneteket a készülékre.
- **Átjárótár-fiók**: A kiszolgálóáttelepítés az átjárótár-fiókot használja a replikált virtuális gépek állapotadatainak tárolására.
- **Naplótárfiók:** Az Azure Migrate appliance replikálási naplókat tölt fel a virtuális gépek egy naplótárfiókba. Az Azure Migrate a replikációs adatokat alkalmazza a replikáni kezelt lemezekre.
- **Kulcstároló:** Az Azure Migrate-készülék a key vault segítségével kezeli a szolgáltatásbusz kapcsolati karakterláncait, és a replikációban használt tárfiókok hozzáférési kulcsait. Be kell állítania az okat az engedélyeket, amelyeket a key vaultnak a tárfiók eléréséhez kell, amikor előkészítette. [Tekintse át ezeket az engedélyeket.](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault)   


## <a name="track-and-monitor"></a>Nyomon követése és monitorozása


- Amikor a **Replikáció replikálása feladat replikálása** parancsra kattint. 
- A replikáció indítása sikeres befejezése után a gépek megkezdik az Azure-ba történő kezdeti replikációt.
- A kezdeti replikáció során létrejön egy virtuális gép pillanatképe. A pillanatkép lemezadatait replikálja a replikáni kezelt lemezek az Azure-ban.
- A kezdeti replikáció befejezése után megkezdődik a különbözeti replikáció. A helyszíni lemezek növekményes módosításait rendszeres időközönként replikálja a replikalemezek az Azure-ban.

A feladat állapotát a portál értesítései ben követheti nyomon.

A replikáció állapotát az **Azure Áttelepítés: Kiszolgálóáttelepítés** **szolgáltatás replikálási kiszolgálóira** kattintva figyelheti.
![Replikáció monitorozása](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>Migrálási teszt futtatása


A különbözeti replikáció megkezdésekor futtathat egy tesztáttelepítést a virtuális gépekhez, mielőtt teljes áttelepítést futtatna az Azure-ba. Javasoljuk, hogy ezt minden géphez legalább egyszer tegye meg, mielőtt áttelepítene.

- Tesztáttelepítési ellenőrzések futtatása, amelyek áttelepítése a várt módon fog működni, anélkül, hogy befolyásolnák a helyszíni gépek, amelyek továbbra is működőképes, és továbbra is replikálása. 
- A tesztáttelepítés szimulálja az áttelepítést egy Azure virtuális gép replikált adatok használatával történő létrehozásával (általában nem éles virtuális hálózatra való áttelepítés az Azure-előfizetésben).
- Használhatja a replikált teszt Azure virtuális gép az áttelepítés érvényesítéséhez, az alkalmazástesztelésének végrehajtásához, és a problémák megoldása a teljes áttelepítés előtt.

Végezze el a tesztáttelepítést az alábbiak szerint:


1. Az **áttelepítési célok** > **kiszolgálóinak** > **Azure Migrate: Server Migration**területén kattintson az **Áttelepített kiszolgálók tesztelése**elemre.

     ![Migrált kiszolgálók tesztelése](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Kattintson a jobb gombbal a tesztelni kívánt virtuális gépre, majd válassza a **Migrálás tesztelése** lehetőséget.

    ![Migrálási teszt](./media/tutorial-migrate-vmware/test-migrate.png)

3. A **Migrálási teszt** területen válassza ki az Azure-beli virtuális hálózatot, amelyen az Azure-beli virtuális gép a migrálást követően megtalálható lesz. Javasoljuk, hogy ne éles virtuális hálózatot használjon.
4. A **Migrálási teszt** feladat elindul. A feladatot a portál értesítései között követheti nyomon.
5. A migrálás befejeztével az Azure Portal **Virtuális gépek** területén tekintheti meg a migrált Azure-beli virtuális gépet. A gép nevében a **-Test** utótag szerepel.
6. A teszt elvégzése után a **Gépek replikálása** területen kattintson a jobb gombbal az Azure-beli virtuális gépre, majd kattintson a **Migrálási teszt törlése** gombra.

    ![Migrálás törlése](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Virtuális gépek áttelepítése

Miután meggyőződött arról, hogy a tesztáttelepítés a várt módon működik, áttelepítheti a helyszíni gépeket.

1. Az Azure Migrate projektben > **A Kiszolgálók** > **Azure Áttelepítés: Kiszolgáló áttelepítése**, kattintson **a kiszolgálók replikálása**elemre.

    ![Kiszolgálók replikálása](./media/tutorial-migrate-vmware/replicate-servers.png)

2. A **Gépek replikálása** területen kattintson jobb gombbal a virtuális gépre, majd kattintson a **Migrálás** elemre.
3. Az **Áttelepítés** > leállítása a virtuális gépek leállítása**és a tervezett áttelepítés adatvesztés nélküli végrehajtása**csoportban válassza az **Igen** > **OK**lehetőséget.
    - Alapértelmezés szerint az Azure Migrate leállítja a helyszíni virtuális gépet, majd igény szerint replikációt végez a legutóbbi replikáció óta a virtuális gépen történt módosítások szinkronizálása érdekében. Így elkerülhető az adatvesztés.
    - Ha nem szeretné leállítani a virtuális gépet, válassza a **Nem** lehetőséget.
4. A virtuálisgép-migrálási feladat elindul. A feladatot az Azure-értesítések között követheti nyomon.
5. A feladat befejeztével a virtuális gépet a **Virtuális gépek** oldalon tekintheti meg és kezelheti.

## <a name="complete-the-migration"></a>Az áttelepítés befejezése

1. Az áttelepítés befejezése után kattintson a jobb gombbal a virtuális gép > **a replikáció leállítása**elemre. Ez leállítja a helyszíni gép replikációját, és törli a virtuális gép replikációs állapotadatait.
2. Telepítse az Azure VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) ügynök az áttelepített gépeken.
3. Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
4. Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
5. Az áttelepített Azure virtuálisgép-példány forgalmának csökkentése.
6. Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
7. Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
8. Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével. 

## <a name="post-migration-best-practices"></a>Az áttelepítés utáni gyakorlati tanácsok

- A nagyobb rugalmasság érdekében:
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [További információ](../backup/quick-backup-vm-portal.md).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [További információ](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- A biztonság fokozása érdekében:
    - Zárolja és korlátozza a bejövő forgalom elérését az [Azure Security Centerrel – Just in time administration](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Korlátozza a forgalmat felügyeleti végpontokra [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview) használatával.
    - Az [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) üzembe helyezésével biztonságba helyezheti a lemezeket, és megóvhatja az adatokat a lopási kísérletektől és a jogosulatlan hozzáféréstől.
    - Látogasson el a [az Azure Security Center](https://azure.microsoft.com/services/security-center/) webhelyére, és tudjon meg többet az [IaaS-erőforrások biztosításáról](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/).
- Figyelési és felügyeleti eszközök:
-  Fontolja meg az [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.


## <a name="next-steps"></a>További lépések

Vizsgálja meg a [felhőmigrálási utat](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) az Azure Cloud bevezetési keretrendszerben.
