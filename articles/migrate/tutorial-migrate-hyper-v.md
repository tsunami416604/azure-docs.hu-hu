---
title: Hyper-V virtuális gépek áttelepítése az Azure-ba az Azure Áttelepítési Kiszolgáló áttelepítésével
description: Megtudhatja, hogyan telepíthető át a helyszíni Hyper-V virtuális gépek az Azure-ba az Azure Áttelepítési Kiszolgáló áttelepítésével
ms.topic: tutorial
ms.date: 11/18/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2b9ed56186649b7644adbd1237ad74af50474cc5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80279708"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>A Hyper-V virtuális gépek migrálása az Azure-ba 

Ez a cikk bemutatja, hogyan telepítheti át a helyszíni Hyper-V virtuális gépeket az Azure-ba az Azure Áttelepítés: Kiszolgálóáttelepítés eszköz használatával.

[Az Azure Migrate](migrate-services-overview.md) központi központot biztosít a helyszíni alkalmazások és számítási feladatok, valamint a magán-/nyilvános felhőbeli virtuális gépek Azure-ba való felderítésének, értékelésének és áttelepítésének nyomon követéséhez. A központ Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint külső független szoftverszállítói (ISV) ajánlatokat.

Ez az oktatóanyag a harmadik egy sorozatban, amely bemutatja, hogyan értékelheti és telepítheti át a Hyper-V-t az Azure-ba az Azure Áttelepítési kiszolgáló értékelése és áttelepítése használatával. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:


> [!div class="checklist"]
> * Készítse elő az Azure-t és a helyszíni Hyper-V környezetet
> * Állítsa be a forráskörnyezetet, és telepítsen egy replikációs berendezést.
> * Állítsa be a célkörnyezetet.
> * Engedélyezze a replikációt.
> * Futtasson egy tesztáttelepítést, hogy minden a várt módon működjön.
> * Futtasson teljes áttelepítést az Azure-ba.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/pricing/free-trial/) mielőtt elkezdené.


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

1. [Tekintse át](hyper-v-migration-architecture.md) a Hyper-V áttelepítési architektúrát.
2. Töltse ki a sorozat [első oktatóanyagát az](tutorial-prepare-hyper-v.md) Azure és a Hyper-V áttelepítéshez való beállításához. Az első bemutató, akkor:
    - [Készítse elő az Azure-t](tutorial-prepare-hyper-v.md#prepare-azure) az áttelepítésre.
    - [Készítse elő a helyszíni környezetet](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration) az áttelepítéshez.
3. Azt javasoljuk, hogy az Azure Áttelepítés: Kiszolgálófelmérés használatával próbálja meg értékelni a Hyper-V virtuális gépeket, mielőtt áttelepülne az Azure-ba. Ehhez fejezze be a sorozat [második oktatóanyagát.](tutorial-assess-hyper-v.md) Bár azt javasoljuk, hogy próbálja ki egy értékelést, nem kell futtatni a felmérést a virtuális gépek áttelepítése előtt.
4. Győződjön meg arról, hogy az Azure-fiók rendelkezik a Virtuálisgép közreműködő szerepkör, így rendelkezik engedélyekkel:

    - Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
    - Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
    - Írjon egy Azure felügyelt lemezre.
5. [Hozzon létre egy Azure-hálózatot.](../virtual-network/manage-virtual-network.md#create-a-virtual-network) Amikor áttelepül az Azure-ba, a létrehozott Azure-beli virtuális gépek csatlakoznak egy Azure-hálózathoz, amelyet az áttelepítés beállításakor ad meg.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Az Azure Áttelepítési kiszolgáló eszközének hozzáadása

Ha nem követte a második oktatóanyag ot a Hyper-V virtuális gépek értékeléséhez, [kövesse ezeket az utasításokat](how-to-add-tool-first-time.md) egy Azure Migrate projekt beállításához, és adja hozzá az Azure Migrate Server Assessment eszközt a projekthez.

Ha követte a második oktatóanyagot, és már rendelkezik egy Azure Migrate projekttel, adja hozzá az Azure Áttelepítés: Kiszolgálóáttelepítés eszközt az alábbiak szerint:

1. Az Azure Migrate projektben kattintson **az Áttekintés gombra.** 
2. A **Felderítés, a kiszolgálók felmérése és áttelepítése csoportban**kattintson **a Kiszolgálók felmérése és áttelepítése**elemre.
3. Az **Áttelepítési eszközök csoportban**válassza **a Kattintson ide lehetőséget, ha áttelepítésre kész ként szeretne migrálni.**

    ![Eszköz kijelölése](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. Az eszközök listájában válassza az **Azure Áttelepítés: Kiszolgálóáttelepítés hozzáadása** > **eszközt.**

    ![Kiszolgálóáttelepítés eszköz](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>Az Azure Migrate-berendezés beállítása

Az Azure Migrate Server Migration egy szoftverügynököt futtat a Hyper-V gazdagépeken vagy fürtcsomópontokon az azure-áttelepítési adatok koordinálásához és replikálásához, és nincs szükség dedikált készülékre az áttelepítéshez.

- Az Azure Migrate: Server Assessment appliance virtuális gép felderítése, és elküldi a virtuális gép metaadatok és teljesítményadatok at Azure Áttelepítési kiszolgáló áttelepítése.
- Az áttelepítési vezénylést és az adatreplikációt a Microsoft Azure Site Recovery szolgáltató és a Microsoft Azure Recovery Service ügynök kezeli.

A készülék beállítása:
- Ha követte a második oktatóanyag ot a Hyper-V virtuális gépek értékeléséhez, már beállította a készüléket az oktatóanyag során, és nem kell újra megtennie.
- Ha nem követte az oktatóanyagot, akkor most be kell állítania a készüléket. Ehhez: 

    - Töltsön le egy tömörített Hyper-V HD-t az Azure Portalról.
    - Hozza létre a készüléket, és ellenőrizze, hogy tud-e csatlakozni az Azure Áttelepítési kiszolgáló értékelése. 
    - Konfigurálja a készüléket az első alkalommal, és regisztrálja az Azure Migrate projekt.

    A készülék beállításához kövesse a [cikkben](how-to-set-up-appliance-hyper-v.md) található részletes utasításokat.

## <a name="prepare-hyper-v-hosts"></a>Hyper-V állomások előkészítése

1. Az Azure Migrate projekt **>-kiszolgálók ban**kattintson a Discover **(Azure Migrate: Server Migration)** **projektben.**
2. A **Discover machines** > **Are your machines virtualizált?** lehetőséget **Igen, a Hyper-V**.
3. A **Célrégióban**válassza ki azt az Azure-régiót, amelyre át szeretné telepíteni a gépeket.
6. Válassza **a Megerősítés lehetőséget, hogy az áttelepítés célrégiója régiónév.Select Confirm that the target region for migration is region-name**.
7. Kattintson **az Erőforrások létrehozása gombra.** Ez létrehoz egy Azure Site Recovery-tárolóak a háttérben.
    - Ha már beállította az áttelepítést az Azure Áttelepítési kiszolgáló áttelepítésével, ez a beállítás nem jelenik meg, mivel az erőforrások korábban be vannak állítva.
    - A gombra kattintás után nem módosíthatja a projekt célrégióját.
    - Minden ezt követő áttelepítés erre a régióra van.
    
8. A **Hyper-V gazdakiszolgálók előkészítése területen**töltse le a Hyper-V replikációs szolgáltatót és a regisztrációs kulcsfájlt.
    - A regisztrációs kulcs szükséges a Hyper-V állomás regisztrálásához az Azure Áttelepítési kiszolgáló áttelepítésével.
    - A kulcs a generálásától számított öt napig érvényes.

    ![Letöltésszolgáltató és kulcs](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Másolja a szolgáltató telepítőfájlját és a regisztrációs kulcsfájlt minden replikálni kívánt virtuális gépet futtató Hyper-V állomásra (vagy fürtcsomópontra).
5. Futtassa a szolgáltató beállítási fájlját az egyes gazdagépen a következő eljárásnak.
6. Miután telepítette a szolgáltatót a gazdagépekre, a **Discover gépek**területén kattintson a **Regisztráció véglegesítése gombra.**

    ![Regisztráció véglegesítése](./media/tutorial-migrate-hyper-v/finalize-registration.png)

A regisztráció véglegesítése után akár 15 percet is igénybe vehet, amíg a felderített virtuális gépek megjelennek az Azure Áttelepítési kiszolgáló áttelepítése során. A virtuális gépek felderítése, a **felderített kiszolgálók** száma emelkedik.

![Felderített kiszolgálók](./media/tutorial-migrate-hyper-v/discovered-servers.png)

### <a name="register-hyper-v-hosts"></a>Hyper-V állomások regisztrálása

Telepítse a letöltött telepítőfájlt (AzureSiteRecoveryProvider.exe) minden egyes megfelelő Hyper-V állomásra.

1. Futtassa a szolgáltató telepítőfájlját az egyes állomás- vagy fürtcsomópontokon.
2. A Szolgáltató beállítása varázslóban > **Microsoft Update**alkalmazásban engedélyezze, hogy a Microsoft Update segítségével keresse meg a szolgáltató frissítéseit.
3. A **telepítés ben**fogadja el a szolgáltató és az ügynök alapértelmezett telepítési helyét, és válassza a **Telepítés**lehetőséget.
4. A telepítés után a Regisztráció varázsló > **a Tároló beállításai párbeszédpanelen**válassza a **Tallózás**gombot, és a **Kulcsfájl**ban jelölje ki a letöltött tárolókulcsfájlt.
5. A **Proxybeállítások párbeszédpanelen**adja meg, hogy az állomáson futó szolgáltató hogyan csatlakozzon az internethez.
    - Ha a készülék proxykiszolgáló mögött található, meg kell adnia a proxybeállításokat.
    - Adja meg a **http://ip-address**proxynevet a vagy **http://FQDN**a . A HTTPS-proxykiszolgálók nem támogatottak.
   

6. Győződjön meg arról, hogy a szolgáltató el tudja érni a [szükséges URL-eket.](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts)
7. A **Regisztráció ban**a gazdagép regisztrálása után kattintson a **Befejezés**gombra.

## <a name="replicate-hyper-v-vms"></a>Hyper-V virtuális gépek replikálása

A felderítés befejeződött, megkezdheti a hyper-V virtuális gépek azure-ba replikációját.

> [!NOTE]
> Legfeljebb 10 gépet replikálhat együtt. Ha több replikálnia kell, akkor azokat egyszerre 10-es kötegekben replikálja.

1. Az Azure Migrate projekt > **az** **Azure Áttelepítés: Kiszolgálóáttelepítés**, kattintson a **Replikálás gombra.**
2. A **Replikálás**párbeszédpanelen > **A** > **gépei virtualizáltak?** lehetőséget választja az **Igen, a Hyper-V beállítással**lehetőséget. Ezután kattintson a **Tovább: Virtuális gépek gombra.**
3. A **Virtuális gépek** területen jelölje ki a replikálni kívánt gépeket.
    - Ha futtatta a virtuális gépek értékelését, alkalmazhatja az eredményekben szereplő, a virtuális gépek méretezésére és a lemeztípusra (prémium/standard) vonatkozó javaslatokat. Ehhez a **Importálja a migrálási beállításokat az Azure Migrate-értékelésből?** területen válassza az **Igen** lehetőséget.
    - Ha nem futtatott értékelést, vagy nem szeretné használni az értékelés beállításait, válassza a **Nem** lehetőséget.
    - Ha az értékelés használatát választotta, válassza ki a virtuálisgép-csoportot és az értékelés nevét.

        ![Értékelés kiválasztása](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. A **Virtuális gépek** között igény szerint keressen a virtuális gépekre, és válassza ki a gépeket, amelyeket migrálni szeretne. Ezután kattintson a **Tovább: Célbeállítások gombra.**

    ![Virtuális gépek kijelölése](./media/tutorial-migrate-hyper-v/select-vms.png)

5. A **Célbeállítások területen**válassza ki azt a célrégiót, amelyre áttelepíti, az előfizetést és azt az erőforráscsoportot, amelyben az Azure-beli virtuális gépek az áttelepítés után tartózkodnak.
7. A **Replikációs tárfiókban**válassza ki azt az Azure Storage-fiókot, amelyben a replikált adatok at az Azure-ban tárolja.
8. **Virtuális hálózat**, válassza ki az Azure virtuális hálózat/alhálózat, amelyhez az Azure virtuális gépek lesznek csatlakoztatva az áttelepítés után.
9. Az **Azure Hybrid Benefit** területen:

    - Válassza a **Nem** lehetőséget, ha nem szeretné alkalmazni az Azure Hybrid Benefit szolgáltatást. Ezután kattintson a **Tovább gombra.**
    - Válassza az **Igen** lehetőséget, ha aktív frissítési garanciával vagy Windows Server-előfizetéssel rendelkező gépei vannak, és alkalmazni szeretné az előnyöket a migrált gépekre. Kattintson a **Tovább** gombra.

    ![Célbeállítások](./media/tutorial-migrate-hyper-v/target-settings.png)

10. A **Számítás** területen ellenőrizze a virtuális gép nevét, méretét, az operációs rendszer lemeztípusát és a rendelkezésreállási csoportot. A virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).

    - **Virtuális gép mérete:** Ha értékelési javaslatokat használ, a virtuális gép mérete legördülő tartalmazza az ajánlott méretet. Egyéb esetben az Azure Migrate az Azure-előfizetésben található leginkább megfelelő érték alapján választja ki a méretet. Alternatív megoldásként az **Azure-beli virtuális gép mérete** területen manuálisan is kiválaszthatja a méretet. 
    - **OPERÁCIÓS RENDSZER lemeze**: Adja meg a virtuális gép operációs rendszerének (rendszerindító) lemezét. Az operációsrendszer-lemez az a lemez, amelyen az operációs rendszer rendszerbetöltője és telepítője található. 
    - **Rendelkezésre állási készlet:** Ha a virtuális gép kell egy Azure rendelkezésre állási csoport áttelepítés után, adja meg a készlet. A csoportnak a migrálás során megadott cél-erőforráscsoportban kell lennie.

    ![Virtuális gép számítási beállításai](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. A **Lemezek** területen adja meg, hogy a virtuálisgép-lemezek replikálva legyenek-e az Azure-ba, majd válassza ki a lemez típusát (standard SSD/HDD vagy prémium felügyelt lemez) az Azure-ban. Kattintson a **Tovább** gombra.
    - Kizárhat lemezeket a replikációból.
    - Ha kizárja a lemezeket, azok nem lesznek jelen az Azure-beli virtuális gépen a migrációt követően. 

    ![Lemezek](./media/tutorial-migrate-hyper-v/disks.png)

10. Az **Áttekintés és a replikáció megkezdése** területen tekintse át a beállításokat, majd kattintson a **Replikálás** gombra a kiszolgálók kezdeti replikálásának elindításához.

> [!NOTE]
> A replikáció beállításait a replikáció megkezdése előtt bármikor frissítheti a**Replikálógépek** **kezelése** > párbeszédpanelen. A replikáció kezdete után a beállítások már nem módosíthatók.

## <a name="provisioning-for-the-first-time"></a>Első alkalommal történő kiépítés

Ha ez az első virtuális gép, amelyet az Azure Áttelepítés projektben replikál, az Azure Migrate: Server Migration automatikusan leálja ezeket az erőforrásokat ugyanabban az erőforráscsoportban, mint a projekt.

- **Service bus**: Azure Áttelepítés: A kiszolgálóáttelepítés a Service Bus használatával küld replikációs vezénylési üzeneteket a készülékre.
- **Átjárótár-fiók**: Azure Áttelepítés: A kiszolgálóáttelepítés az átjárótár-fiókot használja a replikált virtuális gépek állapotadatainak tárolására.
- **Naplótárfiók:** Az Azure Migrate appliance replikálási naplókat tölt fel a virtuális gépek egy naplótárfiókba. Az Azure Migrate a replikációs adatokat alkalmazza a replikáni kezelt lemezekre.
- **Kulcstároló:** Az Azure Migrate-készülék a key vault segítségével kezeli a szolgáltatásbusz kapcsolati karakterláncait, és a replikációban használt tárfiókok hozzáférési kulcsait. Be kell állítania az engedélyeket, amelyeka key vault eléréséhez a tárfiók eléréséhez, amikor [az Azure](tutorial-prepare-hyper-v.md#prepare-azure) hyper-V virtuális gép értékelése és áttelepítése. 


## <a name="track-and-monitor"></a>Nyomon követése és monitorozása


- Amikor a **Replikáció replikálása feladat replikálása** parancsra kattint. 
- A replikáció indítása sikeres befejezése után a gépek megkezdik az Azure-ba történő kezdeti replikációt.
- A kezdeti replikáció befejezése után megkezdődik a különbözeti replikáció. A helyszíni lemezek növekményes módosításait a rendszer rendszeresen replikálja az Azure-ba.

A feladat állapotát a portál értesítései ben követheti nyomon.

A replikáció állapotát az **Azure Áttelepítés: Kiszolgálóáttelepítés** **szolgáltatás replikálási kiszolgálóira** kattintva figyelheti.
![Replikáció monitorozása](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Migrálási teszt futtatása


A különbözeti replikáció megkezdésekor futtathat egy tesztáttelepítést a virtuális gépekhez, mielőtt teljes áttelepítést futtatna az Azure-ba. Javasoljuk, hogy ezt minden géphez legalább egyszer tegye meg, mielőtt áttelepítene.

- Tesztáttelepítési ellenőrzések futtatása, amelyek áttelepítése a várt módon fog működni, anélkül, hogy befolyásolnák a helyszíni gépek, amelyek továbbra is működőképes, és továbbra is replikálása. 
- A tesztáttelepítés szimulálja az áttelepítést egy Azure virtuális gép replikált adatok használatával történő létrehozásával (általában nem éles Azure virtuális hálózatra való áttelepítés az Azure-előfizetésben).
- Használhatja a replikált teszt Azure virtuális gép az áttelepítés érvényesítéséhez, az alkalmazástesztelésének végrehajtásához, és a problémák megoldása a teljes áttelepítés előtt.

Végezze el a tesztáttelepítést az alábbiak szerint:


1. Az **áttelepítési célok** > **kiszolgálóinak** > **Azure Migrate: Server Migration**területén kattintson az **Áttelepített kiszolgálók tesztelése**elemre.

     ![Migrált kiszolgálók tesztelése](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Kattintson a jobb gombbal a tesztelni kívánt virtuális gépre, majd válassza a **Migrálás tesztelése** lehetőséget.

    ![Migrálási teszt](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. A **Test Migration**alkalmazásban válassza ki azt az Azure virtuális hálózatot, amelyben az Azure virtuális gép az áttelepítés után található lesz. Nem éles virtuális hálózat használatát javasoljuk.
4. A **Migrálási teszt** feladat elindul. A feladatot a portál értesítései között követheti nyomon.
5. A migrálás befejeztével az Azure Portal **Virtuális gépek** területén tekintheti meg a migrált Azure-beli virtuális gépet. A gép nevében a **-Test** utótag szerepel.
6. A teszt elvégzése után a **Gépek replikálása** területen kattintson a jobb gombbal az Azure-beli virtuális gépre, majd kattintson a **Migrálási teszt törlése** gombra.

    ![Migrálás törlése](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Virtuális gépek áttelepítése

Miután meggyőződött arról, hogy a tesztáttelepítés a várt módon működik, áttelepítheti a helyszíni gépeket.

1. Az Azure Migrate projektben > **A Kiszolgálók** > **Azure Áttelepítés: Kiszolgáló áttelepítése**, kattintson **a kiszolgálók replikálása**elemre.

    ![Kiszolgálók replikálása](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. A **Gépek replikálása** területen kattintson jobb gombbal a virtuális gépre, majd kattintson a **Migrálás** elemre.
3. Az **Áttelepítés** > leállítása a virtuális gépek leállítása**és a tervezett áttelepítés adatvesztés nélküli végrehajtása**csoportban válassza az **Igen** > **OK**lehetőséget.
    - Alapértelmezés szerint az Azure Migrate leállítja a helyszíni virtuális gépet, majd igény szerint replikációt végez a legutóbbi replikáció óta a virtuális gépen történt módosítások szinkronizálása érdekében. Így elkerülhető az adatvesztés.
    - Ha nem szeretné leállítani a virtuális gépet, válassza a **Nem** lehetőséget.
4. A virtuálisgép-migrálási feladat elindul. A feladatot az Azure-értesítések között követheti nyomon.
5. A feladat befejeztével a virtuális gépet a **Virtuális gépek** oldalon tekintheti meg és kezelheti.

## <a name="complete-the-migration"></a>Az áttelepítés befejezése

1. Az áttelepítés befejezése után kattintson a jobb gombbal a virtuális gép > **az áttelepítés leállítása**elemre. Ez a következőket teszi:
    - Leállítja a helyszíni gép replikációját.
    - Eltávolítja a gépet a **replikáló kiszolgálók** száma az Azure Áttelepítés: Kiszolgáló áttelepítése.
    - Törli a virtuális gép replikációs állapotadatait.
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
