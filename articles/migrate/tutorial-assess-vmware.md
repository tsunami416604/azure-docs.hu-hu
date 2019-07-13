---
title: VMware virtuális gépek értékelése az Azure-bA az Azure Migrate az áttelepítéshez
description: Ismerteti, hogyan értékelheti a helyszíni VMware virtuális gépek áttelepítése az Azure-bA az Azure Migrate szolgáltatással.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: hamusa
ms.openlocfilehash: 5f70037b1e6ce284b55ff5ff0ae38eb50c320122
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868658"
---
# <a name="assess-vmware-vms-with-azure-migrate-server-assessment"></a>Mérje fel az Azure-ral a VMware virtuális gépek áttelepítése: Server Assessment

Ez a cikk bemutatja, hogyan értékelheti a helyszíni VMware virtuális gépeket, az Azure Migrate szolgáltatással: Server Assessment eszközt.

[Az Azure Migrate](migrate-services-overview.md) hub eszközöket tartalmazza, amelyek segítséget nyújtanak a felderítése, értékelheti és migrálhatja az alkalmazásokat, az infrastruktúra és a számítási feladatok a Microsoft Azure biztosít. A hub tartalmaz, az Azure Migrate eszközökkel és külső független szoftverszállítók (ISV) szállító ajánlatok.



Ez az oktatóanyag egy sorozat, amely azt ismerteti, hogyan mérje fel, és a VMware virtuális gépek áttelepítése az Azure-bA a második. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Állítsa be az Azure Migrate-projektet.
> * Állítsa be, amely futtatja a helyszíni virtuális gépek értékelése az Azure Migrate telepíthetőek.
> * Indítsa el a helyszíni virtuális gépek folyamatos felderítését. A berendezés konfiguráció-és teljesítményadatokat küld a felderített virtuális gépek az Azure-bA.
> * Csoport felderített virtuális gépen, és felmérheti a VM-csoportazonosítóval.
> * Tekintse át az értékelést.



> [!NOTE]
> Az oktatóprogramok bemutatják, a legegyszerűbb telepítési út forgatókönyv esetén, hogy gyorsan beállíthat egy proof-of-concept. Az oktatóanyagok lehetőség szerint használja az alapértelmezett, és ne jelenjen meg az összes lehetséges beállítások és elérési út. Részletes útmutatásért tekintse át az útmutató cikkekre.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prerequisites"></a>Előfeltételek

- [Teljes](tutorial-prepare-vmware.md) az első oktatóanyag-sorozat. Ha nem, akkor ez az oktatóanyag utasításait nem fog működni.
- Mit kell még meg az első oktatóanyagban itt látható:
    - [Állítsa be az Azure-engedélyeket](tutorial-prepare-vmware.md#prepare-azure) az Azure Migrate.
    - [VMware előkészítése](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) értékeléshez. VMware-beállítások ellenőrizni kell, és a VMware virtuális gép létrehozása az OVA-sablonnal kell. A virtuális gépek felderítésének fiókkal is rendelkezhet. Szükséges portok elérhetőnek kell lennie, és érdemes figyelembe vennie, az Azure-bA eléréséhez szükséges URL-címeket.


## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate-projekt beállítása

Egy új Azure Migrate-projektet az alábbiak szerint állíthatja.

1. Az Azure Portal > **minden szolgáltatás**, keressen **Azure Migrate**.
2. A **szolgáltatások**válassza **Azure Migrate**.
3. A **áttekintése**alatt **felfedezheti, értékelni és migrálni a kiszolgálók**, kattintson a **felmérési és a kiszolgálók áttelepítése**.

    ![Felderítés és értékelés kiszolgálók](./media/tutorial-assess-vmware/assess-migrate.png)

4. A **bevezetés**, kattintson a **eszközök hozzáadása**.
5. A **Migrate projekt**, válassza ki az Azure-előfizetés, és ha nem rendelkezik ilyennel, hozzon létre egy erőforráscsoportot.     
6. A **Project Details**, adja meg a projekt nevét és a földrajzi hely, amelyben meg szeretné a projekt létrehozásához. Ázsia, Európa, Egyesült Királyság és az Egyesült Államokban támogatottak.

    - A projekt földrajzi hely csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál.
    - Minden célként megadott régióban is válassza, ha az áttelepítés futtatása.

    ![Az Azure Migrate-projekt létrehozása](./media/tutorial-assess-vmware/migrate-project.png)


7. Kattintson a **Tovább** gombra.
8. A **válassza frissítésfelmérő eszköz**válassza **Azure Migrate: Server Assessment** > **tovább**.

    ![Az Azure Migrate-projekt létrehozása](./media/tutorial-assess-vmware/assessment-tool.png)

9. A **válassza áttelepítési eszköz**válassza **egy migrációs eszköz felvétele most kihagyása** > **tovább**.
10. A **tekintse át + eszközök hozzáadása**, tekintse át a beállításokat, majd kattintson **eszközök hozzáadása**.
11. Várjon néhány percet, amíg az Azure Migrate-projekt üzembe helyezéséhez. Megnyílik a projekt lapra. Ha nem látja a projektet, hozzá tud férni a **kiszolgálók** az Azure Migrate-irányítópulton.


## <a name="set-up-the-appliance-vm"></a>A berendezés virtuális gép beállítása

Az Azure Migrate: Server Assessment egy egyszerűsített VMware virtuális gép készülék futtatja.

- A készülék elvégzi a virtuális gépek felderítésének, és a virtuális gép metaadatainak és a teljesítmény adatokat küld az Azure Migrate Server Assessment.
- Beállítása a készülék meg:
    - Töltse le az OVA sablon fájlt, és importálja a vCenter Serverhez.
    - A berendezés létrehozásához, és ellenőrizze, hogy képes csatlakozni az Azure Migrate Server Assessment.
    - Konfigurálja a berendezést először, és regisztrálja az Azure Migrate-projektben.
- Beállíthat több készülékek egyetlen Azure Migrate-projekt. Az összes berendezések között legfeljebb 35,000 virtuális gépek felderítését használata támogatott. Legfeljebb 10 000 kiszolgálók száma berendezés könnyen megtalálhatók legyenek.

### <a name="download-the-ova-template"></a>Az OVA-sablon letöltése

1. A **áttelepítési célok** > **kiszolgálók** > **Azure Migrate: Server Assessment**, kattintson a **felderítési**.
2. A **gépek felderítése** > **virtualizáltak a gépek?** , kattintson a **Igen, a VMWare vSphere hipervizor**.
3. Kattintson a **letöltése** letöltéséhez a. Sablon OVA-fájl.

    ![Az .ova-fájl letöltése](./media/tutorial-assess-vmware/download-ova.png)


### <a name="verify-security"></a>Biztonsági ellenőrzése

Ellenőrizze, hogy az OVA-fájl biztonságos, az üzembe helyezés előtt.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot az OVA kivonatának létrehozásához:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. A 1.19.06.27 esetén a létrehozott kivonatnak egyeznie kell az ezeket az értékeket. 

  **Algoritmus** | **Kivonat értéke**
  --- | ---
  MD5 | 605d208ac5f4173383f616913441144e
  SHA256 | 447d16bd55f20f945164a1189381ef6e98475b573d6d1c694f3e5c172cfc30d4


### <a name="create-the-appliance-vm"></a>A berendezés virtuális gép létrehozása

Importálja a letöltött fájlt, és hozzon létre egy virtuális Gépet.

1. A vSphere Client-konzolon kattintson a **Fájl** > **OVF-sablon telepítése** elemre.

    ![Az OVF telepítése](./media/tutorial-assess-vmware/deploy-ovf.png)

2. Az OVF-sablon üzembe helyezése varázsló > **forrás**, adja meg az OVA-fájl helyét.
3. A **neve** és **hely**, adjon meg egy rövid nevet a virtuális gép számára. Válassza ki a készlet objektum, amelyben a virtuális gép fog üzemeltetni.
5. A **gazdagéphez/fürthöz**, adja meg a gazdagépet, vagy a fürtöt, amely a virtuális gép fog futni.
6. A **tárolási**, adja meg a célhelyet a virtuális gép számára.
7. A **Disk Format** (Lemezformátum) mezőben adja meg a lemez típusát és méretét.
8. A **Hálózatleképezés**, adja meg a hálózatot, amelyhez a virtuális gépek csatlakozni fognak. Metaadatokat küldhet az Azure Migrate Server Assessment internetkapcsolattal kell rendelkeznie a a hálózaton.
9. Tekintse át és hagyja jóvá a beállításokat, majd kattintson a **Finish** (Befejezés) gombra.


### <a name="verify-appliance-access-to-azure"></a>Az Azure-bA készülék-hozzáférés ellenőrzése

Győződjön meg arról, hogy a berendezés virtuális gép kapcsolódik- [Azure URL-címeinek](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


### <a name="configure-the-appliance"></a>Konfigurálja a berendezést

Állítsa be a készüléket, az alábbi lépéseket követve.

1. A vSphere Client-konzolon kattintson a jobb gombbal a virtuális gépre, majd kattintson az **Open Console** (Konzol megnyitása) elemre.
2. Adja meg a berendezés a nyelv, az időzóna és a jelszót.
3. Nyisson meg egy böngészőt, minden olyan gépen, amely a virtuális gép csatlakozhat, és nyissa meg a webalkalmazás URL-címét a készülék: **https://*berendezés neve vagy IP-cím*: 44368**.

   Azt is megteheti megnyithatja az alkalmazás a készülék asztalról az alkalmazás parancsikonjára kattintva.
4. A webalkalmazásban > **Előfeltételek beállítása**, tegye a következőket:
    - **Licenc**: Fogadja el a licencfeltételeket, és olvassa el a külső szolgáltatóval kapcsolatos információkat.
    - **Kapcsolat**: Az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép egy proxyt használja:
        - Kattintson a **proxybeállítások**, és adja meg a proxykiszolgáló címét és figyelőportját, a képernyőn http://ProxyIPAddress vagy http://ProxyFQDN.
        - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
        - Csak a HTTP-proxyk használata támogatott.
    - **Szinkronizálási idő**: lehet, hogy az idő, a készüléken felderítés működéséhez internet időt szinkronizálva.
    - **Telepítse a frissítéseket**: A berendezés biztosítja, hogy telepítve vannak-e a legújabb frissítéseket.
    - **Telepítse a VDDK**: A berendezés ellenőrzi, hogy a VMWare vSphere virtuális lemez Development Kit (VDDK) telepítve van.
        - Az Azure Migrate: Kiszolgáló áttelepítése a VDDK használja a gépek replikálása az Azure-ba való migráláskor.
        - Töltse le a VDDK 6.7 a VMware-ből, és csomagolja ki a letöltött zip tartalmát a megadott helyen, a készüléken.

### <a name="register-the-appliance-with-azure-migrate"></a>A berendezés regisztráljon az Azure Migrate

1. Kattintson a **bejelentkezés**. Ha nem jelenik meg, győződjön meg arról, akkor le lett tiltva az előugró ablakok a böngészőben.
2. Az új lapon jelentkezzen be Azure hitelesítő adatait.
    - Jelentkezzen be a felhasználónevét és jelszavát.
    - Jelentkezzen be a PIN-kód nem támogatott.
3. Miután sikeresen bejelentkezett, lépjen vissza a webalkalmazás.
2. Válassza ki az előfizetést, amelyben az Azure Migrate-projekt létrejött, majd válassza ki a projektet.
3. Adjon meg egy nevet a berendezéshez. A névnek alfanumerikus karakterek és 14 karakter vagy kevesebb kell lennie.
4. Kattintson a **regisztrálása**.


## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

Most a készülék vCenter-kiszolgálóhoz csatlakozhat, és indítsa el a virtuális gépek felderítésének.

1. A **adja meg a vCenter Server adatait**, adja meg a nevét (FQDN) vagy a vCenter-kiszolgáló IP-címét. Hagyja bejelölve az alapértelmezett port, vagy adjon meg egy egyéni portot, amelyen a vCenter-kiszolgáló figyeli.
2. A **felhasználónév** és **jelszó**, adja meg a csak olvasható fiók hitelesítő adatait, amelynek használatával a berendezést a vCenter-kiszolgáló a virtuális gépek felderítéséhez. Győződjön meg arról, hogy a fiók rendelkezik-e a [szükséges engedélyek a felderítéshez](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). A felderítés gazdagépcsoportjaira, a vCenter-fióknak hozzáférést korlátozza. További információ a felderítési hatókör [Itt](tutorial-assess-vmware.md#scoping-discovery).
3. Kattintson a **-kapcsolat ellenőrzése** , győződjön meg arról, hogy a berendezés vCenter-kiszolgálóhoz csatlakozhat.
4. A kapcsolat létrejötte után kattintson a **mentéséhez és a felderítés megkezdése**.

Ez elindítja a felderítést. Megjelenik a portálon a felderített virtuális gépek metaadatait hozzávetőlegesen 15 percet vesz igénybe.

### <a name="scoping-discovery"></a>Felderítési hatókör

Felderítés hatóköre beállítható korlátozza a hozzáférést a detektáláshoz használt vCenter-fiók. A hatókör és a vCenter Server adatközpontok, a fürtök, fürtök, a gazdagépek, a gazdagépek, illetve az egyes virtuális gépekhez mappa megadható. 

> [!NOTE]
> Még ma az Server Assessment nem sikerül, ha a vCenter-fióknak vCenter VM mappa szintjén a hozzáférést a virtuális gépek felderítéséhez. Ha a virtuális gép mappák által a felderítés hatókörének, ezért úgy, hogy a vcenter-kiszolgáló csak olvasási hozzáféréssel rendelkezik egy VM-szinten hozzárendelt megteheti.  Az alábbiakban útmutatást találhat hogyan ehhez:
>
> 1. Rendelje hozzá a virtuális gép mappákat, amelyre a felderítés hatókörét szeretné az összes virtuális gép csak olvasható engedélyek. 
> 2. A virtuális gépeket üzemeltető összes szülőobjektum olvasási hozzáférési jogot. Minden szülőobjektumok - gazdagép, a gazdagépek, a fürtben, fürtök mappa – a hierarchiában, akár az adatközpontban is használni fog. Nem kell az engedélyek az összes gyermekobjektum propagálása.
> 3. A hitelesítő adatok használata az adatközpont, kiválasztása felderítéshez *gyűjtés hatóköre*. Állítsa be az RBAC biztosítja, hogy csak a bérlőspecifikus virtuális gépek a megfelelő vCenter-felhasználó hozzáférhet.
>
> Jegyezze fel a gazdagépek mappájából, és a fürtök támogatottak.

### <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

Felderítés, után ellenőrizheti, hogy a virtuális gépek megjelennek-e az Azure Portalon.

1. Nyissa meg az Azure Migrate irányítópultot.
2. A **Azure Migrate - kiszolgálók** > **Azure Migrate: Server Assessment** lap, kattintson az ikont, amely megjeleníti a számát, a **felderített kiszolgálók**.

## <a name="set-up-an-assessment"></a>Értékelés beállítása

Az Azure Migrate használatával hozhat létre értékeléseket két típusa van: Server Assessment.

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | Értékelések alapján összegyűjtött teljesítményadatok | **Virtuálisgép-méretet ajánlott**: A Processzor és a memória kihasználtsági adatok alapján.<br/><br/> **Lemez típusa (standard vagy prémium szintű felügyelt lemez) ajánlott**: Az IOPS és átviteli sebessége, a helyi lemezek alapján.
**Helyszíni** | Értékelések alapján helyszíni méretezést. | **Virtuálisgép-méretet ajánlott**: A helyszíni virtuális gép mérete alapján<br/><br> **Ajánlott a lemez típusát**: A tárolási típus beállítását választja, az értékelés alapján.


### <a name="run-an-assessment"></a>Értékelés futtatása

Értékelés futtatása a következőképpen:

1. Tekintse át a [ajánlott eljárások](best-practices-assessment.md) értékelés létrehozásához.
2. Az a **kiszolgálók** lap **Azure Migrate: Server Assessment** csempére, **felmérési**.

    ![Értékelés](./media/tutorial-assess-vmware/assess.png)

2. A **mérje fel a kiszolgálók**, adja meg az értékelés nevét.
3. Kattintson az **Összes megtekintése** elemre az értékelési tulajdonságok áttekintéséhez.

    ![Értékelés tulajdonságai](./media/tutorial-assess-vmware/view-all.png)

3. A **válassza ki vagy hozzon létre egy**válassza **hozzon létre új**, és adja meg a csoport nevét. Egy csoport egy vagy több virtuális gépeket az értékeléshez együtt gyűjt.
4. A **gépek felvétele a csoportba**, válassza ki a virtuális gépek hozzáadása a csoporthoz.
5. Kattintson a **értékelés létrehozása** hozza létre a csoportot, és az értékelés futtatása.

    ![Értékelés létrehozása](./media/tutorial-assess-vmware/assessment-create.png)

6. Az értékelés létrehozása után megtekintheti a **kiszolgálók** > **Azure Migrate: Server Assessment** > **értékelések**.
7. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.



## <a name="review-an-assessment"></a>Tekintse át az értékeléseket

Az értékelés ismerteti:

- **Azure-kompatibilitás**: Virtuális gépek alkalmasak-e az Azure-ba való migrálásra.
- **Havi költségbecslés**: A becsült havi számítási és tárolási költségek a virtuális gépek futtatásához az Azure-ban.
- **Havi tárolási költségbecsléshez**: Becsült költségek a lemezes tárolás az áttelepítés után.

### <a name="view-an-assessment"></a>Értékelés megjelenítése

1. A **áttelepítési célok** >  **kiszolgálók**, kattintson a **értékelések** a **Azure Migrate: Server Assessment**.
2. A **értékelések**, kattintson az értékelés való megnyitásához.

    ![Értékelés összegzése](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Tekintse át az Azure-kompatibilitás

1. A **Azure-kompatibilitás**, ellenőrizze, hogy e virtuális gépek készen áll az Azure-ba való migrálásra.
2. Tekintse át a virtuális gép állapota:
    - **Azure-beli használatra kész**: Az Azure Migrate javasol egy Virtuálisgép-méret és a költségek becslése a virtuális gépek értékelése.
    - **Feltételekkel kész**: Problémák és a megoldáshoz javasolt jeleníti meg.
    - **Nem áll készen az Azure-ban**: Problémák és a megoldáshoz javasolt jeleníti meg.
    - **A kompatibilitás ismeretlen**: Használja, amikor az Azure Migrate nem tudja felmérni készültségi adatok rendelkezésre állási problémák miatt.

2. Kattintson a egy **Azure-kompatibilitás** állapotát. Virtuális gépek készültségi részleteinek megtekintéséhez, és részletes elemzését tekintse meg a virtuális gép részleteit, köztük számítási, tárolási és hálózati beállításokat.



### <a name="review-cost-details"></a>Költség felülvizsgálat részletei

Ez a nézet megjeleníti az Azure-ban futó virtuális gépek becsült számítási és tárolási költségeit.

1. Tekintse át a havi számítási és tárolási költségeit. Költségek a értékelt csoportban lévő összes virtuális gép vannak összesítve.

    - A költségbecslések a javaslatok a méretekkel kapcsolatban egy gép és a lemezek és a Tulajdonságok alapulnak.
    - Számítási és tárolási becsült havi költségek jelenik meg.
    - A helyszíni virtuális gépek IaaS virtuális gépként futó költségbecslési szól. Az Azure Migrate Server Assessment nem tekinti a PaaS és SaaS-költségeket.

2. Át is havi tárolási költségbecslés. Ez a nézet jeleníti meg a értékelt csoport összesített tárolási költségek felosztás tárolólemezek különböző típusú.
3. Részletes elemzését is tekintse meg a részleteit az adott virtuális gépek.


### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

Teljesítmény alapuló értékeléseket futtatásakor olyan megbízhatósági minősítéssel van rendelve az értékelés.

![Megbízhatósági minősítés](./media/tutorial-assess-vmware/confidence-rating.png)

- A minősítés 1 csillag a (legalacsonyabb), 5 csillagos (legmagasabb) oda.
- A megbízhatósági minősítés segít megbecsülni az értékelés által nyújtott méretjavaslatok megbízhatóságát.
- A megbízhatósági minősítés alapján történik az értékelés kiszámításához szükséges adatpontok rendelkezésre állását.

Az értékelés megbízhatósági minősítése az alábbiak szerint.

**Adatpont rendelkezésre állása** | **Megbízhatósági minősítés**
--- | ---
0%–20% | 1 csillag
21%-40% | 2 csillag
41%-60% | 3 csillag
61%-80% | 4 csillag
81%-100% | 5 csillag

[További](best-practices-assessment.md#best-practices-for-confidence-ratings) kapcsolatos ajánlott eljárások a megbízhatósági minősítés.


## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Az Azure Migrate berendezés beállítása
> * Létrehozott, és tekintse át az értékeléseket

Továbbra is a harmadik oktatóanyag-sorozathoz, megtudhatja, hogyan lehet VMware virtuális gépek migrálása az Azure Migrate-kiszolgáló áttelepítése Azure-bA.

> [!div class="nextstepaction"]
> [VMware virtuális gépek áttelepítése](./tutorial-migrate-vmware.md)
