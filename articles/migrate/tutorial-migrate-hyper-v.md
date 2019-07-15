---
title: A helyszíni Hyper-V virtuális gépek áttelepítése az Azure-bA az Azure Migrate kiszolgáló áttelepítése |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan telepítheti át a helyszíni Hyper-V virtuális gépeket az Azure-bA az Azure Migrate-kiszolgáló áttelepítése
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4a88e9dddd492d5c24698bcde8c3a1fd942eaf66
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854197"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Hyper-V-alapú virtuális gépek migrálása az Azure-ba 

Ez a cikk bemutatja, hogyan a helyszíni Hyper-V virtuális gépek áttelepítése az Azure-ba, ügynök nélküli migrálást használata az Azure Migrate Server áttelepítési eszköz.

[Az Azure Migrate](migrate-services-overview.md) kínál egy központi agyhoz felderítési, felmérési és a helyszíni alkalmazások és a számítási feladatok és a privát és nyilvános felhőbeli VM-eken, az Azure-ba való migrálásának nyomon követéséhez. A hub felmérés és migrálás, valamint a külső független szoftverszállítók (ISV) szállító ajánlatok az Azure Migrate eszközöket biztosít.

Ez az oktatóanyag egy sorozat azt mutatja be, hogyan mérje fel, és a Hyper-V áttelepítése az Azure-ban az Azure Migrate kiszolgáló elemzésekhez és Migráláshoz a harmadik. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:


> [!div class="checklist"]
> * Az Azure és a helyszíni Hyper-V-környezet előkészítése
> * A forráskörnyezet beállításához, és a egy replikációs készülék üzembe helyezése.
> * A cél environmen beállítása...
> * Engedélyezze a replikációt.
> * Győződjön meg arról, hogy minden a várt módon működik a migrálási teszt futtatása.
> * Futtassa a teljes migrálás az Azure-bA.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

1. [Felülvizsgálat](migrate-architecture.md) a Hyper-V áttelepítési architektúra.
2. [Az első oktatóanyag](tutorial-prepare-hyper-v.md) az oktatóanyag-sorozatban az áttelepítéshez az Azure és Hyper-V beállításához. Az első oktatóanyagban meg:
    - [Az Azure előkészítése](tutorial-prepare-hyper-v.md#prepare-azure) az áttelepítéshez.
    - [A környezet előkészítése a helyszíni](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration) az áttelepítéshez.
3. Azt javasoljuk, hogy a Hyper-V virtuális gépek Azure Migrate Server Assessment használatával való migrálás előtt felmérése próbálja őket az Azure-bA. Ehhez [végezze el a második oktatóanyagban](tutorial-assess-hyper-v.md) az oktatóanyag-sorozatban. Habár javasoljuk, hogy próbálja ki az értékelés, nem kell értékelés futtatása a virtuális gépek áttelepítése előtt.
4. Győződjön meg arról, hogy az Azure-fiókjával a virtuális gépek Közreműködője szerepkör van hozzárendelve, hogy az engedélyekkel rendelkezik:

    - Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
    - Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
    - Írni az Azure-beli felügyelt lemezt.   
5. [Azure-hálózat beállítása](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Ha telepít át, az Azure-ba, a létrehozott Azure virtuális gépek csatlakoznak áttelepítési beállításakor megadott Azure-hálózat.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Az Azure Migrate Server áttelepítési eszköz hozzáadása

Ha nem követi a második oktatóanyagban Hyper-V virtuális gépek értékeléséhez, akkor [az alábbi lépéseket követve](how-to-add-tool-first-time.md) beállítása az Azure Migrate-projektet, és az Azure Migrate Server áttelepítési eszköz hozzáadása a projekthez.

Ha a második oktatóanyagban követni, és már rendelkezik egy Azure Migrate projekt beállításról, adja hozzá a következő az Azure Migrate Server áttelepítési eszköz:

1. Az Azure Migrate-projektben kattintson **áttekintése**. 
2. A **felfedezheti, mérje fel, és a migrálás kiszolgálók**, kattintson a **felmérési és a kiszolgálók áttelepítése**.
3. A **áttelepítési eszközök**válassza **Ide kattintva hozzáadhat egyet egy migrációs eszköz, amikor készen áll a áttelepítése**.

    ![Eszköz kiválasztása](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. Az eszközök listájában válassza ki a **Azure Migrate: Kiszolgáló-áttelepítési** > **eszköz hozzáadása**

    ![Server Migrálási eszköz](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>Az Azure Migrate berendezés beállítása

Az Azure Migrate kiszolgáló áttelepítése egy könnyen használható Hyper-V virtuális készülék futtatja.

- A berendezés hajtja végre a virtuális gépek felderítésének, és a virtuális gép metaadatait és a teljesítmény adatokat küld az Azure Migrate-kiszolgáló áttelepítése.
- Az azonos készülék is használják az Azure Migrate Server Assessment eszköz.

A berendezés beállítása:
- Ha követte a lépéseket annak ellenőrzéséhez, a Hyper-V virtuális gépeket az második oktatóanyagban, akkor már beállított a készülék az oktatóanyag során.
- Ha az oktatóanyag követéséhez nem kell most már a berendezés beállítása. Ehhez meg: 

    - Töltse le a tömörített Hyper-V virtuális merevlemez az Azure Portalról.
    - A berendezés létrehozásához, és ellenőrizze, hogy képes csatlakozni az Azure Migrate Server Assessment. 
    - Konfigurálja a berendezést először, és regisztrálja az Azure Migrate-projektben.

    Kövesse a [Ez a cikk](how-to-set-up-appliance-hyper-v.md) a berendezés beállítása.

## <a name="prepare-hyper-v-hosts"></a>A Hyper-V-gazdagépek előkészítése

1. Az Azure Migrate-projektben > **kiszolgálók**, a **Azure Migrate: Kiszolgáló-áttelepítési**, kattintson a **felderítési**.
2. A **gépek felderítése** > **virtualizáltak a gépek?** válassza **Igen, a Hyper-V**.
3. A **célrégió**, válassza ki, amelyhez hozzá szeretné a gépek áttelepítése az Azure-régióban.
6. Válassza ki **erősítse meg, hogy az áttelepítéshez a célrégióban régiónévhez**.
7. Kattintson a **létre erőforrásokat**. Ez létrehoz egy Azure Site Recovery-tároló a háttérben.
    - Ha már beállította az Azure Migrate-kiszolgáló áttelepítése a migrálást, ezt a lehetőséget, mivel az erőforrások korábban voltak beállítása nem jelenik meg.
    - Erre a gombra kattintás után nem módosíthatja a célrégióban a projekthez.
    - Minden ezt követő áttelepítés ebben a régióban vannak.
    
8. A **előkészítése Hyper-V-kiszolgálóknak**, töltse le a Hyper-V replikációs szolgáltató, és a regisztrációs kulcs fájlját.
    - A Hyper-V-gazdagép regisztrálása az Azure Migrate-kiszolgáló áttelepítése a Szolgáltatásregisztrációs kulcs szükséges.
    - A kulcs a generálásától számított öt napig érvényes.

    ![Szolgáltató és a kulcs letöltése](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Másolja a szolgáltató telepítőfájlját és a regisztrációs kulcs fájlját minden egyes Hyper-V-gazdagép (vagy a fürtcsomópont) replikálni kívánt virtuális gépeket.
5. Futtassa a szolgáltató telepítőfájlját minden gazdagépen a következő eljárásban leírtak szerint.
6. A gazdagépeken, a szolgáltató telepítése után **gépek felderítése**, kattintson a **regisztráció véglegesítése**.

    ![Regisztráció véglegesítése](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Regisztráció véglegesítése mindaddig, amíg a felderített virtuális gépek megjelennek-e az Azure Migrate-kiszolgáló áttelepítése után akár 15 percet is igénybe vehet. Mivel a virtuális gépeket a felderítésüket, a **felderített kiszolgálók** megnövekszik száma.

![Észlelt kiszolgálók](./media/tutorial-migrate-hyper-v/discovered-servers.png)

### <a name="register-hyper-v-hosts"></a>Regisztrálja a Hyper-V-gazdagépek

Telepítse a letöltött telepítőfájlját (AzureSiteRecoveryProvider.exe) minden releváns Hyper-V-gazdagépre.

1. Futtassa a szolgáltató minden egyes gazdagéphez vagy fürthöz csomóponton telepítőfájlt.
2. A szolgáltató telepítése varázslóban > **Microsoft Update**, szolgáltató frissítései a Microsoft Update használata.
3. A **telepítési**, fogadja el az alapértelmezett telepítési hely a Provider és az ügynök számára, és válassza ki **telepítése**.
4. A regisztrációs varázslóban a telepítést követően > **tároló beállításai**, jelölje be **Tallózás**, és a **titkosításikulcs-fájl**, válassza ki a tároló kulcsfájlját letöltött.
5. A **proxybeállítások**, adja meg, hogy a gazdagépen futó provider hogyan csatlakozzon az internethez.
    - Ha a készülék egy proxykiszolgáló mögött található, adja meg a proxybeállítások szeretné.
    - Adja meg a proxykiszolgáló nevét **http://ip-address** , vagy **http://FQDN** . HTTPS-proxykiszolgálók nem támogatottak.
   

6. Győződjön meg arról, hogy a szolgáltató elérje a [szükséges URL-címekre](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access).
7. A **regisztrációs**, a gazdagép regisztrálása után kattintson a **Befejezés**.

## <a name="replicate-hyper-v-vms"></a>A Hyper-V virtuális gépek replikálása

A felderítés befejeződött elkezdheti a Hyper-V virtuális gépek az Azure-bA replikálását.

1. Az Azure Migrate-projektben > **kiszolgálók**, **Azure Migrate: Kiszolgáló-áttelepítési**, kattintson a **replikálása**.
2. A **replikálása**, > **adatforrás beállításaiban** > **virtualizáltak a gépek?** válassza **Igen, a Hyper-V**. Kattintson a **tovább: Virtuális gépek**.
3. A **virtuális gépek**, válassza ki a replikálni kívánt gépeken.
    - Ha egy értékelést a virtuális gépeken futtatott, virtuális gép méretezése és a lemez típusát (prémium vagy standard) javaslatokat az értékelés eredményeinek is alkalmazhat. Ehhez a **áttelepítési beállításokat importálhat egy Azure Migrate értékelést?** , jelölje be a **Igen** lehetőséget.
    - Ha nem futtatta az értékelés, vagy nem kívánja az értékelési beállításokat használja, jelölje be a **nem** beállítások.
    - Ha az értékelés használatára, válassza ki a VM-csoportazonosítóval, és az értékelés nevét.

        ![Válassza ki az értékelés](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. A **virtuális gépek**, keresse meg a virtuális gépek, igény szerint, és minden egyes áttelepíteni kívánt virtuális gép ellenőrzése. Kattintson a **tovább: Cél beállításai**.

    ![Virtuális gépek kiválasztása](./media/tutorial-migrate-hyper-v/select-vms.png)

5. A **cél beállításai**, válassza ki a célrégióban, amelyhez fog át, az előfizetés és az erőforráscsoport, amelyben az Azure virtuális gépek helyezkednek el az áttelepítés után.
7. A **replikálás Tárfiókja**, válassza a replikált adatokat az Azure storage-fiók, amelyben tárolni az Azure-ban.
8. **Virtuális hálózat**, válassza ki az Azure virtuális hálózat/alhálózat, amelyhez az Azure virtuális gépek tartományhoz fog csatlakozni az áttelepítés után.
9. A **Azure Hybrid Benefit értékelem**:

    - Válassza ki **nem** Ha nem szeretné az Azure Hybrid Benefit alkalmazására. Ezután kattintson a **Tovább** gombra.
    - Válassza ki **Igen** Ha Windows Serveres gépek, aktív frissítési garancia vagy a Windows Server előfizetések amelyekre rendelkezik, és alkalmazza a juttatás a migráláshoz gépekhez. Ezután kattintson a **Next** (Tovább) gombra.

    ![Tárolóbeállítások](./media/tutorial-migrate-hyper-v/target-settings.png)

10. A **számítási**, tekintse át a virtuális gép nevét, méretét, operációsrendszer-lemez típusa és a rendelkezésre állási csoport. Virtuális gépek meg kell felelniük az [Azure-követelmények](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Virtuálisgép-méret**: Kapcsolódó javaslatokat használja, ha a virtuális gép mérete legördülő listában az ajánlott mérete fogja tartalmazni. Ellenkező esetben az Azure Migrate választja ki a mérete alapján a leginkább egyezik az Azure-előfizetésében. Azt is megteheti, válassza ki a manuális méretét **Azure Virtuálisgép-méret**. 
    - **Operációsrendszer-lemez**: Adja meg az (Indítás) operációsrendszer-lemez a virtuális gép számára. Az operációsrendszer-lemezének mérete a lemezt, amely az operációs rendszer rendszerbetöltőt, és a telepítő rendelkezik. 
    - **A rendelkezésre állási csoport**: Ha a virtuális gép az Azure rendelkezésre állási csoportba az áttelepítés után kell lennie, adja meg a készletet. A készlet adja meg, ha az áttelepítés a célerőforrás-csoportban kell lennie.

    ![Virtuális gép számítási beállítások](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. A **lemezek**, adja meg, hogy a Virtuálisgép-lemezek replikálni kell az Azure-ba, válassza ki a lemez típusát (standard szintű SSD vagy HDD vagy prémium szintű managed disks) az Azure-ban. Ezután kattintson a **Next** (Tovább) gombra.
    - Lemezeket zárhat ki a replikációból.
    - Lemezek kizárása nem elérhetőnek lennie az Azure virtuális gépen az áttelepítés után. 

    ![Lemezek](./media/tutorial-migrate-hyper-v/disks.png)

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
- Kezdeti replikálás befejezése után kezdődik a változásreplikációhoz. A helyi lemezek növekményes változásait a rendszer rendszeres időközönként replikálja az Azure-bA.

A portál értesítési a feladat állapotának nyomon követéséhez.

Kattintson a replikációs állapot figyelheti **kiszolgálók replikálásához** a **Azure Migrate: Kiszolgáló-áttelepítési**.
![A figyelő replikáció](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Migrálási teszt futtatása


Amikor elindul a változások replikálása az Azure-ban a teljes migrálás futtatása előtt egy áttelepítési teszt futtathatja a virtuális gépek esetén. Kifejezetten ajánljuk, hogy ehhez legalább egyszer minden egyes számítógépen, akkor az áttelepítés előtt.

- Futó migrálási teszt ellenőrzi, hogy a migrálás megfelelően működik, a helyszíni gépek befolyásolása nélkül fog működni. amely működőképes marad, és továbbra is replikálásához. 
- Migrálási teszt hoz létre a replikált adatok (az Azure-előfizetés nem éles hálózatba általában áttelepítése) használata Azure virtuális Gépen az áttelepítés szimulálja.
- Az Azure virtuális gép replikált teszt segítségével az áttelepítés ellenőrzése, hajtsa végre az alkalmazás tesztelése, és a teljes áttelepítés előtt problémák elhárítása.

Migrálási teszt hajtsa végre a következő:


1. A **áttelepítési célok** > **kiszolgálók** > **Azure Migrate: Kiszolgáló-áttelepítési**, kattintson a **teszt át kiszolgálók**.

     ![Az áttelepített kiszolgálók teszt](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Kattintson a jobb gombbal a virtuális gép teszteléséhez, és kattintson a **teszt áttelepítése**.

    ![Migrálási teszt](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. A **Migrálási teszt**, válassza ki az Azure virtuális hálózatot, amelyben az Azure virtuális gép található a migrálás után. Azt javasoljuk, hogy egy nem éles virtuális hálózathoz használhat.
4. A **migrálási teszt** feladat elindul. A portál értesítési a feladat figyeléséhez.
5. Az áttelepítés befejezése után megtekintheti a migrált Azure a virtuális gép **virtuális gépek** az Azure Portalon. A számítógépnek a neve tartalmaz egy utótagot **-teszt**.
6. A vizsgálat után kattintson a jobb gombbal az Azure VM **replikáló**, és kattintson a **migrálási teszt karbantartása**.

    ![Migrálás törlése](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Virtuális gépek áttelepítése

Miután meggyőződött arról, hogy a migrálási teszt a várt módon működik-e, a helyszíni gépeket telepíthet át.

1. Az Azure Migrate-projektben > **kiszolgálók** > **Azure Migrate: Kiszolgáló-áttelepítési**, kattintson a **kiszolgálók replikálásához**.

    ![Kiszolgálók replikálása](./media/tutorial-migrate-hyper-v/replicate-servers.png)

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
