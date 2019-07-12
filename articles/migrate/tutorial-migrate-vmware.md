---
title: Migrate a helyszíni VMware virtuális gépeken az ügynök nélküli kivételfigyelés Azure Migrate-kiszolgáló áttelepítése az Azure-bA |} A Microsoft Docs
description: Ismerteti, hogyan hajthat végre, és a helyszíni VMware virtuális gépek az Azure-ba, az Azure Migrate szolgáltatással ügynök nélküli migrálást.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 3510c0505a5a3c1353642baf5060a83d13fdd43a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809126"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>VMware virtuális gépek áttelepítése az Azure-ba (ügynök nélküli kivételfigyelés)

Ez a cikk bemutatja, hogyan a helyszíni VMware virtuális gépek áttelepítése az Azure-ba, ügynök nélküli migrálást használata az Azure Migrate Server áttelepítési eszköz.

[Az Azure Migrate](migrate-services-overview.md) kínál egy központi agyhoz felderítési, felmérési és a helyszíni alkalmazások és számítási feladatok és az AWS/GCP Virtuálisgép-példányok, az Azure-ba való migrálásának nyomon követéséhez. A hub felmérés és migrálás, valamint a külső független szoftverszállítók (ISV) szállító ajánlatok az Azure Migrate eszközöket biztosít.

Ez az oktatóanyag egy sorozat azt mutatja be, hogyan mérje fel, és a VMware virtuális gépek áttelepítése az Azure-ban az Azure Migrate kiszolgáló elemzésekhez és Migráláshoz a harmadik. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális gépek előkészítése migráláshoz.
> * Adja hozzá az Azure Migrálási Server Migrálási eszköz.
> * Migrálni kívánt virtuális gépek felderítéséhez.
> * Indítsa el a virtuális gépek replikálása.
> * Győződjön meg arról, hogy minden a várt módon működik a migrálási teszt futtatása.
> * Virtuális gép teljes áttelepítés futtatása.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="migration-methods"></a>Áttelepítési módszereinek

VMware virtuális gépek áttelepíthetők az Azure-bA az Azure Migrate Server áttelepítési eszköz használata. Ez az eszköz egy néhány további lehetőség a VMware virtuális gépek migrálása kínálja:

- A replikációs ügynök nélküli kivételfigyelés áttelepítés. Telepítse át a virtuális gépek anélkül, hogy telepít semmit a őket.
- Áttelepítés a replikációs ügynök. Az ügynök telepítése a virtuális gép.

Annak eldöntéséhez, hogy szeretné-e ügynök nélküli kivételfigyelés és az ügynök-alapú áttelepítést használ, tekintse át a következő cikkeket:

- [Ismerje meg, hogyan](server-migrate-overview.md) működik ügynök nélküli migrálást, és [vonatkozó korlátozások áttekintése](server-migrate-overview.md#agentless-migration-limitations).
- [Ez a cikk](tutorial-migrate-vmware-agent.md) Ha az ügynök-alapú módszert használni szeretne.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

1. [Megismerheti](migrate-architecture.md) a VMware-migrálási architektúra.
2. [Az első oktatóanyag](tutorial-prepare-vmware.md) beállításához az áttelepítés az Azure és VMware-sorozat. Pontosabban ebben az oktatóanyagban kell tennie:
    - [Az Azure előkészítése](tutorial-prepare-vmware.md#prepare-azure) az áttelepítéshez.
    - [A környezet előkészítése a helyszíni](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) az áttelepítéshez.
    
3. Azt javasoljuk, hogy a VMware virtuális gépekhez az Azure Migrate Server Assessment felmérése való migrálás előtt próbálja őket az Azure-bA. Értékelés beállítása [végezze el a második oktatóanyagban](tutorial-assess-vmware.md) az oktatóanyag-sorozatban. Ha nem szeretne virtuális gépek értékelése ebben az oktatóanyagban kihagyhatja. Habár javasoljuk, hogy, próbálja ki az értékelés, de nem kell az áttelepítés előtt értékelés futtatása.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Az Azure Migrate Server áttelepítési eszköz hozzáadása

Ha nem követi a felmérheti a VMware virtuális gépeket az második oktatóanyaga, kell [az alábbi lépéseket követve](how-to-add-tool-first-time.md) beállítása az Azure Migrate-projektet, és válassza ki az Azure Migrate Server áttelepítési eszköz. 

Ha a második oktatóanyagban követni, és már rendelkezik az Azure Migrate-projektet állíthatja be, a következő adja hozzá az Azure Migrate Server áttelepítési eszköz:

1. Az Azure Migrate-projektben kattintson **áttekintése**. 
2. A **felfedezheti, mérje fel, és a migrálás kiszolgálók**, kattintson a **felmérési és a kiszolgálók áttelepítése**.

     ![Mérje fel, és a kiszolgálók áttelepítése](./media/tutorial-migrate-vmware/assess-migrate.png)

3. A **áttelepítési eszközök**válassza **Ide kattintva hozzáadhat egyet egy migrációs eszköz, amikor készen áll a áttelepítése**.

    ![Eszköz kiválasztása](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. Az eszközök listájában válassza ki a **Azure Migrate: Kiszolgáló-áttelepítési** > **eszköz hozzáadása**

    ![Server Migrálási eszköz](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Az Azure Migrate berendezés beállítása

Az Azure Migrate kiszolgáló áttelepítése egy egyszerűsített VMware virtuális gép készülék futtatja. A berendezés hajtja végre a virtuális gépek felderítésének, és a virtuális gép metaadatait és a teljesítmény adatokat küld az Azure Migrate-kiszolgáló áttelepítése. Az azonos készülék is használják az Azure Migrate Server Assessment eszköz.

Ha követte a felmérheti a VMware virtuális gépeket az második oktatóanyaga, akkor már beállított a készülék az oktatóanyag során. Ha az oktatóanyag követéséhez nem kell most már a berendezés beállítása. Ehhez meg: 

- Töltse le az OVA sablon fájlt, és importálja a vCenter Serverhez.
- A berendezés létrehozásához, és ellenőrizze, hogy képes csatlakozni az Azure Migrate Server Assessment. 
- Konfigurálja a berendezést először, és regisztrálja az Azure Migrate-projektben.

Kövesse a [Ez a cikk](how-to-set-up-appliance-vmware.md) a berendezés beállítása.


## <a name="prepare-vms-for-migration"></a>Virtuális gépek előkészítése migráláshoz

Az Azure Migrate győződjön meg arról, hogy áttelepíthető-e a virtuális gépek az Azure-bA néhány virtuális gép módosítását igényli.

- Néhány [operációs rendszerek](server-migrate-overview.md#agentless-migration-limitations), az Azure Migrate automatikusan végrehajtja ezeket a módosításokat.
- Ha egy virtuális Gépet, amely nem rendelkezik a következő operációs rendszerek migráláshoz, kövesse az utasításokat a virtuális gép előkészítése.
- Fontos, hogy ezek a változások, az áttelepítés megkezdése előtt. Ha a virtuális Gépet telepít át, a módosítás előtt, a virtuális gép esetleg nem indul el az Azure-ban.
- Konfigurációs módosításokat a helyszíni virtuális gépeket az Azure-bA lesznek replikálva, a virtuális gép replikálásának engedélyezése után. Annak érdekében, hogy történt változások lesznek replikálva, ellenőrizze, hogy a helyreállítási pontot telepít át későbbi, mint az idő, amelyen a konfigurációs módosítások történtek a helyszínen.


### <a name="prepare-windows-server-vms"></a>Windows Server rendszerű virtuális gépek előkészítése

**Művelet** | **Részletek** | **Utasítások**
--- | --- | ---
Győződjön meg arról, az Azure virtuális Gépen Windows kötetek azonos meghajtóbetűjelek a helyszíni virtuális gépként. | Az összes Online a TÁROLÓHÁLÓZATI szabályzatát állítsa be. | 1. Jelentkezzen be a virtuális gép egy rendszergazdai fiókkal, és nyisson meg egy parancsablakot.<br/> 2. Típus **diskpart** a Diskpart segédprogram futtatásához.<br/> 3. Típus **TÁROLÓHÁLÓZATI szabályzata = OnlineAll**<br/> 4. Írja be az Exit Diskpart hagyja, és zárja be a parancssort.
Soros hozzáférés az Azure-konzolon engedélyezi az Azure virtuális gép | Ez segít a hibák elhárításában. Nem kell a virtuális gépet. Az Azure virtuális gép a lemezkép használatával fog elindulni, és ez egyenértékű, az új virtuális gép újraindítását. | Hajtsa végre a [ezek az utasítások](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console#enable-serial-console-in-custom-or-older-images) engedélyezéséhez.
Telepítse a Hyper-V Vendég-integráció | Ha a Windows Server 2003-at futtató gépek migráláshoz, telepítse a Hyper-V Vendég integrációs szolgáltatásokat a virtuális gép operációs rendszeren. | [További információk](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Távoli asztal | Távoli asztal engedélyezése a virtuális gép, és ellenőrizze, hogy a Windows tűzfal nem blokkolja-e távoli asztali hozzáférés bármely hálózati profilban. | [További információk](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Linux rendszerű virtuális gépek előkészítése

**Művelet** | **Részletek** 
--- | --- | ---
A Hyper-V Linux integrációs szolgáltatások telepítése | Linux-disztribúciók a legtöbb új verziók esetében ez alapértelmezés szerint benne.
A Linux init rendszerkép tartalmazza a szükséges Hyper-V illesztőprogramok újraépítése | Ez biztosítja, hogy a virtuális gép fog elindulni, az Azure-ban, és csak bizonyos disztribúciókon szükséges.
Soros konzol az Azure-naplózás engedélyezése | Ez segít a hibák elhárításában. Nem kell a virtuális gépet. Az Azure virtuális gép a lemezkép használatával fog elindulni, és ez egyenértékű, az új virtuális gép újraindítását.<br/> Hajtsa végre a [ezek az utasítások](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) engedélyezéséhez.
Eszköz leképezési fájl frissítése | A térkép eszközfájl a társítást, állandó készülékazonosítók használata az eszköz nevét tartalmazó frissítése
Fstab bejegyzések frissítése | Tartós kötet azonosítókat használjon bejegyzések frissítése.
Udev szabály eltávolítása | Távolítsa el, amely alapján a mac-cím stb illesztőneveket fenntartja az udev szabályokat.
Hálózati adapter frissítése | Frissítse a hálózati adaptert a DHCP-alapú IP-címet kapnak.
Ssh engedélyezése | Győződjön meg, hogy ssh engedélyezve van, és a sshd szolgáltatás újraindításakor automatikus indításra van beállítva.<br/> Győződjön meg arról, hogy a bejövő ssh csatlakozási kérelmek nem blokkolja az operációs rendszer tűzfala vagy parancsfájlok segítségével szabályokat.

[Kövesse az ebben a cikkben](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) , amely ismerteti ezeket a lépéseket az Azure-on futó Linux rendszerű virtuális gép, és tartalmaz néhány, a népszerű Linux-disztribúciók arra vonatkozó útmutatást.  


## <a name="replicate-vms"></a>Virtuális gépek replikálása

A felderítés befejeződött elkezdheti a VMware virtuális gépek az Azure-bA replikálását.

1. Az Azure Migrate-projektben > **kiszolgálók**, **Azure Migrate: Kiszolgáló-áttelepítési**, kattintson a **replikálása**.

    ![Virtuális gépek replikálása](./media/tutorial-migrate-vmware/select-replicate.png)

2. A **replikálása**, > **adatforrás beállításaiban** > **virtualizáltak a gépek?** válassza **Igen, a VMware vSphere**.
3. A **helyszíni berendezés**, válassza ki a nevét, amelynek beállítása az Azure Migrate készülék > **OK**. 

    ![Az adatforrás-beállítások](./media/tutorial-migrate-vmware/source-settings.png)

    - Ez a lépés azt feltételezi, hogy Ön már beállított egy készülék az oktatóanyag befejezésekor.
    - Ha nem állított be egy készülék, majd kövesse a [Ez a cikk](how-to-set-up-appliance-vmware.md).

4. A **virtuális gépek**, válassza ki a replikálni kívánt gépeken.
    - Ha egy értékelést a virtuális gépeken futtatott, virtuális gép méretezése és a lemez típusát (prémium vagy standard) javaslatokat az értékelés eredményeinek is alkalmazhat. Ehhez a **áttelepítési beállításokat importálhat egy Azure Migrate értékelést?** , jelölje be a **Igen** lehetőséget.
    - Ha nem futtatta az értékelés, vagy nem kívánja az értékelési beállításokat használja, jelölje be a **nem** beállítások.
    - Ha az értékelés használatára, válassza ki a VM-csoportazonosítóval, és az értékelés nevét.

    ![Válassza ki az értékelés](./media/tutorial-migrate-vmware/select-assessment.png)

5. A **virtuális gépek**, keresse meg a virtuális gépek, igény szerint, és minden egyes áttelepíteni kívánt virtuális gép ellenőrzése. Kattintson a **tovább: Cél beállításai**.

    ![Virtuális gépek kiválasztása](./media/tutorial-migrate-vmware/select-vms.png)

6. A **cél beállításai**, válassza ki az előfizetést, és a célrégió, amelyhez fog át, és adja meg az erőforráscsoportot, amelyben az Azure virtuális gépek helyezkednek el az áttelepítés után. A **virtuális hálózat**, válassza ki az Azure virtuális hálózat/alhálózat, amelyhez az Azure virtuális gépek tartományhoz fog csatlakozni az áttelepítés után.
7. A **Azure Hybrid Benefit értékelem**:

    - Válassza ki **nem** Ha nem szeretné az Azure Hybrid Benefit alkalmazására. Ezután kattintson a **Next** (Tovább) gombra.
    - Válassza ki **Igen** Ha Windows Serveres gépek, aktív frissítési garancia vagy a Windows Server előfizetések amelyekre rendelkezik, és alkalmazza a juttatás a migráláshoz gépekhez. Ezután kattintson a **Next** (Tovább) gombra.

    ![Tárolóbeállítások](./media/tutorial-migrate-vmware/target-settings.png)

8. A **számítási**, tekintse át a virtuális gép nevét, méretét, operációsrendszer-lemez típusa és a rendelkezésre állási csoport. Virtuális gépek meg kell felelniük az [Azure-követelmények](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Virtuálisgép-méret**: Kapcsolódó javaslatokat használja, ha a virtuális gép mérete legördülő listában az ajánlott mérete fogja tartalmazni. Ellenkező esetben az Azure Migrate választja ki a mérete alapján a leginkább egyezik az Azure-előfizetésében. Azt is megteheti, válassza ki a manuális méretét **Azure Virtuálisgép-méret**. 
    - **Operációsrendszer-lemez**: Adja meg az (Indítás) operációsrendszer-lemez a virtuális gép számára. Az operációsrendszer-lemezének mérete a lemezt, amely az operációs rendszer rendszerbetöltőt, és a telepítő rendelkezik. 
    - **A rendelkezésre állási csoport**: Ha a virtuális gép az Azure rendelkezésre állási csoportba az áttelepítés után kell lennie, adja meg a készletet. A készlet adja meg, ha az áttelepítés a célerőforrás-csoportban kell lennie.

    ![Virtuális gép számítási beállítások](./media/tutorial-migrate-vmware/compute-settings.png)

9. A **lemezek**, adja meg, hogy a Virtuálisgép-lemezek replikálni kell az Azure-ba, válassza ki a lemez típusát (standard szintű SSD vagy HDD vagy prémium szintű managed disks) az Azure-ban. Ezután kattintson a **Next** (Tovább) gombra.
    - Lemezeket zárhat ki a replikációból.
    - Lemezek kizárása nem elérhetőnek lennie az Azure virtuális gépen az áttelepítés után. 

    ![Lemezek](./media/tutorial-migrate-vmware/disks.png)

10. A **tekintse át, és indítsa el a replikációt**, tekintse át a beállításokat, majd kattintson **replikálása** a kezdeti replikáció a kiszolgálókhoz.

> [!NOTE]
> Frissítheti replikációs beállításait, a replikáció megkezdése előtt bármikor **kezelés** > **replikáló**. Replikációs elindulása után nem lehet módosítani a beállításokat.

### <a name="provisioning-for-the-first-time"></a>Az első alkalommal kiépítése

Ha ez az első virtuális gép replikál az Azure Migrate-projektben, az Azure Migrate-kiszolgáló áttelepítése automatikusan látja el ezeket az erőforrásokat a projekt ugyanabban az erőforráscsoportban.

- **Service bus**: Az Azure Migrate kiszolgáló áttelepítése a service bus replikációs vezénylési üzeneteket küldeni a berendezést használ.
- **Átjáró tárfiók**: Kiszolgáló áttelepítése az átjáró storage-fiókot használ a replikált virtuális gépek állapot adatainak tárolásához.
- **Log storage-fiók**: Az Azure Migrate berendezés virtuális gépek replikálási naplók tölt fel egy naplózási tárfiók. Az Azure Migrate vonatkozik a replika felügyelt lemezeket a replikációs adatokat.
- **A Key vault**: Az Azure Migrate készülék a key vault használatával kezelheti a service bus kapcsolati karakterláncok és a replikáció használt tárfiókok hozzáférési kulcsait. Kell állítania az engedélyeket, a key vault el kell érnie a tárfiók előkészítése során. [Tekintse át ezeket az engedélyeket](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>Nyomon követése és figyelése


- Amikor rákattint **replikálása** replikáció indítása feladat kezdődik. 
- A replikáció indítása feladat sikeres befejezését követően a gépek kezdje a kezdeti replikáció az Azure-bA.
- Kezdeti replikálás során egy VM-pillanatkép jön létre. Lemez adatai a pillanatképből replikálódik replika felügyelt lemezeket az Azure-ban.
- Kezdeti replikálás befejezése után kezdődik a változásreplikációhoz. A replika lemezeket az Azure-ban rendszeres időközönként replikálja a helyi lemezek növekményes módosításokat.

A portál értesítési a feladat állapotának nyomon követéséhez.

Kattintson a replikációs állapot figyelheti **kiszolgálók replikálásához** a **Azure Migrate: Kiszolgáló-áttelepítési**.
![A figyelő replikáció](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>Migrálási teszt futtatása


Amikor elindul a változások replikálása az Azure-ban a teljes migrálás futtatása előtt egy áttelepítési teszt futtathatja a virtuális gépek esetén. Kifejezetten ajánljuk, hogy ehhez legalább egyszer minden egyes számítógépen, akkor az áttelepítés előtt.

- Futó migrálási teszt ellenőrzi, hogy a migrálás megfelelően működik, a helyszíni gépek befolyásolása nélkül fog működni. amely működőképes marad, és továbbra is replikálásához. 
- Migrálási teszt hoz létre a replikált adatok (az Azure-előfizetés nem éles hálózatba általában áttelepítése) használata Azure virtuális Gépen az áttelepítés szimulálja.
- Az Azure virtuális gép replikált teszt segítségével az áttelepítés ellenőrzése, hajtsa végre az alkalmazás tesztelése, és a teljes áttelepítés előtt problémák elhárítása.

Migrálási teszt hajtsa végre a következő:


1. A **áttelepítési célok** > **kiszolgálók** > **Azure Migrate: Kiszolgáló-áttelepítési**, kattintson a **teszt át kiszolgálók**.

     ![Az áttelepített kiszolgálók teszt](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Kattintson a jobb gombbal a virtuális gép teszteléséhez, és kattintson a **teszt áttelepítése**.

    ![Migrálási teszt](./media/tutorial-migrate-vmware/test-migrate.png)

3. A **Migrálási teszt**, válassza ki az Azure virtuális hálózatot, amelyben az Azure virtuális gép található a migrálás után. Azt javasoljuk, hogy egy nem éles virtuális hálózathoz használhat.
4. A **migrálási teszt** feladat elindul. A portál értesítési a feladat figyeléséhez.
5. Az áttelepítés befejezése után megtekintheti a migrált Azure a virtuális gép **virtuális gépek** az Azure Portalon. A számítógépnek a neve tartalmaz egy utótagot **-teszt**.
6. A vizsgálat után kattintson a jobb gombbal az Azure VM **replikáló**, és kattintson a **migrálási teszt karbantartása**.

    ![Migrálás törlése](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Virtuális gépek áttelepítése

Miután meggyőződött arról, hogy a migrálási teszt a várt módon működik-e, a helyszíni gépeket telepíthet át.

1. Az Azure Migrate-projektben > **kiszolgálók** > **Azure Migrate: Kiszolgáló-áttelepítési**, kattintson a **kiszolgálók replikálásához**.

    ![Kiszolgálók replikálása](./media/tutorial-migrate-vmware/replicate-servers.png)

2. A **replikáló**, kattintson a jobb gombbal a virtuális gép > **áttelepítése**.
3. A **áttelepítése** > **virtuális gépek leállítása és az adatvesztés a tervezett áttelepítés**válassza **Igen** > **OK** .
    - Alapértelmezés szerint az Azure Migrate a helyszíni virtuális gép leáll, és futtat egy igény szerinti replikációs történt a legutóbbi replikáció óta történt virtuális gépek változásainak szinkronizálását. Ez biztosítja, hogy nincs adatvesztés.
    - Ha nem szeretné a virtuális gép leállítására, válassza ki a **nem**
4. Áttelepítési feladat elindul a virtuális gép számára. Az Azure-értesítések a feladatok nyomon követése.
5. A feladat befejezése után megtekintheti és kezelheti a virtuális gépről a **virtuális gépek** lapot.

## <a name="complete-the-migration"></a>A migrálás befejezése

1. Miután az áttelepítés elkészült, kattintson a jobb gombbal a virtuális gép > **migrálás leállítása**. A helyi gép replikálása leáll, és törli a replikációs állapot információt a virtuális gép számára.
2. Az Azure virtuális gép telepítése [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) az áttelepített gépeken az ügynök.
3. Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
4. Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
5. Kivágás felett a migrált Azure-beli Virtuálisgép-példányhoz.
6. Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
7. Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
8. Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével. 

## <a name="post-migration-best-practices"></a>Áttelepítés utáni eljárások

- A nagyobb rugalmasság érdekében:
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [További információk](../backup/quick-backup-vm-portal.md).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [További információk](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- A biztonság fokozása érdekében:
    - Zárolását, és a bejövő forgalmat korlátozni [Azure Security Center – csak az adminisztrációs alkalommal](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Korlátozza a forgalmat felügyeleti végpontokra [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview) használatával.
    - Az [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) üzembe helyezésével biztonságba helyezheti a lemezeket, és megóvhatja az adatokat a lopási kísérletektől és a jogosulatlan hozzáféréstől.
    - Látogasson el a [az Azure Security Center](https://azure.microsoft.com/services/security-center/) webhelyére, és tudjon meg többet az [IaaS-erőforrások biztosításáról](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/).
- Figyelési és felügyeleti eszközök:
-  Fontolja meg az [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.


## <a name="next-steps"></a>További lépések

Vizsgálja meg a [felhőbe történő migrálás folyamatának](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) a az Azure Cloud bevezetési keretrendszert.
