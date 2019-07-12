---
title: A Hyper-V virtuális gépek áttelepítése az Azure-bA az Azure Migrate értékeléséhez |} A Microsoft Docs
description: Ismerteti, hogyan értékelheti a helyszíni Hyper-V virtuális gépek Azure-ban az Azure Migrate használatával való migráláshoz.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 83567a45980b29931f9b68bd6d60df0d427b09de
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813023"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Mérje fel az Azure-ral a Hyper-V virtuális gépek áttelepítése a Server Assessment

Ez a cikk bemutatja, hogyan értékelheti a helyszíni Hyper-V virtuális gépeket, az Azure Migrate szolgáltatással: Server Assessment eszközt.

[Az Azure Migrate](migrate-services-overview.md) hub eszközöket tartalmazza, amelyek segítséget nyújtanak a felderítése, értékelheti és migrálhatja az alkalmazásokat, az infrastruktúra és a számítási feladatok a Microsoft Azure biztosít. A hub tartalmaz, az Azure Migrate eszközökkel és külső független szoftverszállítók (ISV) szállító ajánlatok.



Ez az oktatóanyag egy sorozat azt mutatja be, hogyan mérje fel, és Hyper-V virtuális gépek áttelepítése az Azure-bA a második. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be az Azure Migrate-projektet.
> * Állítsa be, és regisztrálja az Azure Migrate telepíthetőek.
> * Indítsa el a helyszíni virtuális gépek folyamatos felderítését.
> * Csoport felderített virtuális gépen, és felmérheti a csoport.
> * Tekintse át az értékelést.

> [!NOTE]
> Az oktatóprogramok bemutatják, a legegyszerűbb telepítési út forgatókönyv esetén, hogy gyorsan beállíthat egy proof-of-concept. Az oktatóanyagok lehetőség szerint használja az alapértelmezett, és ne jelenjen meg az összes lehetséges beállítások és elérési út. Részletes útmutatásért tekintse át az útmutató cikkekre.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prerequisites"></a>Előfeltételek

- [Teljes](tutorial-prepare-hyper-v.md) az első oktatóanyag-sorozat. Ha nem, akkor ez az oktatóanyag utasításait nem fog működni.
- Mit kell még meg az első oktatóanyagban itt látható:
    - [Állítsa be az Azure-engedélyeket](tutorial-prepare-hyper-v.md#prepare-azure) az Azure Migrate.
    - [A Hyper-V előkészítése](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-assessment) fürtökkel, a gazdagépek és virtuális gépeket az értékeléshez.

## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate-projekt beállítása

1. Az Azure Portal > **minden szolgáltatás**, keressen **Azure Migrate**.
2. A keresési eredmények között, válassza ki a **Azure Migrate**.
3. A **áttekintése**alatt **felfedezheti, értékelni és migrálni a kiszolgálók**, kattintson a **felmérési és a kiszolgálók áttelepítése**.

    ![Felderítés és értékelés kiszolgálók](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. A **bevezetés**, kattintson a **eszközök hozzáadása**.
5. Az a **Migrate projekt** lapra, válassza ki az Azure-előfizetés, és ha nem rendelkezik ilyennel, hozzon létre egy erőforráscsoportot.
6. A **Project Details**, adja meg a projekt nevét és a régiót, amelyben meg szeretné a projekt létrehozásához.


    ![Az Azure Migrate-projekt létrehozása](./media/tutorial-assess-hyper-v/migrate-project.png)

    Azure Migrate-projekt ezekben a régiókban hozhat létre.

    **Régiócsoport** | **Régió**
    --- | ---
    Ázsia  | Délkelet-Ázsia
    Európa | Észak-Európában és Nyugat-Európa
    Egyesült Királyság |  Egyesült Királyság déli régiója vagy az Egyesült Királyság nyugati régiója
    Egyesült Államok | USA keleti RÉGIÓJA, USA 2. nyugati vagy USA nyugati középső RÉGIÓJA

    - A projekt régiót csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál.
    - A virtuális gépek áttelepítésekor választhat egy másik Azure célrégióban. Migrálási cél Azure-régiók mindegyikében támogatottak.

7. Kattintson a **Tovább** gombra.
8. A **válassza frissítésfelmérő eszköz**válassza **Azure Migrate: Server Assessment** > **tovább**.

    ![Az Azure Migrate-projekt létrehozása](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. A **válassza áttelepítési eszköz**válassza **egy migrációs eszköz felvétele most kihagyása** > **tovább**.
10. A **tekintse át + eszközök hozzáadása**, tekintse át a beállításokat, majd kattintson **eszközök hozzáadása**.
11. Várjon néhány percet, amíg az Azure Migrate-projekt üzembe helyezéséhez. Megnyílik a projekt lapra. Ha nem látja a projektet, hozzá tud férni a **kiszolgálók** az Azure Migrate-irányítópulton.




## <a name="set-up-the-appliance-vm"></a>A berendezés virtuális gép beállítása

Az Azure Migrate Server Assessment fut egy könnyen használható Hyper-V virtuális berendezésre.

- A berendezés hajtja végre a virtuális gépek felderítésének, és a virtuális gép metaadatainak és a teljesítmény adatokat küld az Azure Migrate: Server Assessment.
- Beállítása a készülék meg:
    - Töltse le a tömörített Hyper-V virtuális merevlemez az Azure Portalról.
    - A berendezés létrehozásához, és ellenőrizze, hogy képes csatlakozni az Azure Migrate Server Assessment.
    - Konfigurálja a berendezést először, és regisztrálja az Azure Migrate-projektben.

### <a name="download-the-vhd"></a>Töltse le a VHD-t

Töltse le a Tömörített VHD-sablon a berendezéshez.

1. A **áttelepítési célok** > **kiszolgálók** > **Azure Migrate: Server Assessment**, kattintson a **felderítési**.
2. A **gépek felderítése** > **virtualizáltak a gépek?** , kattintson a **Igen, a Hyper-V**.
3. Kattintson a **letöltése** a VHD-fájl letöltéséhez.

    ![Töltse le a virtuális gép](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Biztonsági ellenőrzése

Ellenőrizze, hogy a zip-fájl biztonságos, az üzembe helyezés előtt.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a kivonat létrehozásához a virtuális merevlemez
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  A berendezés 1.19.06.27 esetén a létrehozott kivonatnak egyeznie kell az ezeket a beállításokat.

  **Algoritmus** | **Kivonat értéke**
  --- | ---
  MD5 | 3681f745fa2b0a0a6910707d85161ec5
  SHA256 | e6ca109afab9657bdcfb291c343b3e3abced9a273d25273059171f9954d25832



### <a name="create-the-appliance-vm"></a>A berendezés virtuális gép létrehozása

Importálja a letöltött fájlt, és a virtuális gép létrehozásához.

1. Bontsa ki a Tömörített VHD-fájlt egy mappába a berendezés virtuális Gépet futtató Hyper-V gazdagépen. Három mappa ki kell olvasni.
2. Nyissa meg a Hyper-V kezelőjét. A **műveletek**, kattintson a **virtuális gép importálása**.

    ![Virtuális merevlemez központi telepítése](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. A virtuális gép varázslóban > **megkezdése előtt**, kattintson a **tovább**.
3. A **mappa keresése**, adja meg a kibontott VHD-t tartalmazó mappát. Ezután kattintson a **Next** (Tovább) gombra.
1. A **válassza ki a virtuális gép**, kattintson a **tovább**.
2. A **importálási típus kiválasztása**, kattintson a **virtuális gép másolása (új egyedi azonosító létrehozása)** . Ezután kattintson a **Next** (Tovább) gombra.
3. A **válasszon cél**, ne módosítsa az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
4. A **tárolási mappák**, ne módosítsa az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
5. A **hálózat kiválasztása**, adja meg a virtuális kapcsolót, amelyet a virtuális gép használni fog. A kapcsoló adatok küldése az Azure-bA internetkapcsolattal kell rendelkeznie.
6. A **összefoglalás**, tekintse át a beállításokat. Kattintson a **Befejezés**.
7. A Hyper-V kezelőjében > **virtuális gépek**, indítsa el a virtuális Gépet.


### <a name="verify-appliance-access-to-azure"></a>Az Azure-bA készülék-hozzáférés ellenőrzése

Győződjön meg arról, hogy a berendezés virtuális gép kapcsolódik- [Azure URL-címeinek](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

### <a name="configure-the-appliance"></a>Konfigurálja a berendezést

A berendezés beállítása az első alkalommal.

1. A Hyper-V kezelőjében > **virtuális gépek**, kattintson a jobb gombbal a virtuális gép > **Connect**.
2. Adja meg a berendezés a nyelv, az időzóna és a jelszót.
3. Nyisson meg egy böngészőt, minden olyan gépen, amely a virtuális gép csatlakozhat, és nyissa meg a webalkalmazás URL-címét a készülék: **https://*berendezés neve vagy IP-cím*: 44368**.

   Azt is megteheti megnyithatja az alkalmazás a készülék asztalról az alkalmazás parancsikonjára kattintva.
1. A webalkalmazásban > **Előfeltételek beállítása**, tegye a következőket:
    - **Licenc**: Fogadja el a licencfeltételeket, és olvassa el a külső szolgáltatóval kapcsolatos információkat.
    - **Kapcsolat**: Az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép egy proxyt használja:
        - Kattintson a **proxybeállítások**, és adja meg a proxykiszolgáló címét és figyelőportját, a képernyőn http://ProxyIPAddress vagy http://ProxyFQDN.
        - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
        - Csak a HTTP-proxyk használata támogatott.
    - **Szinkronizálási idő**: Idő ellenőrizve. Lehet, hogy az idő, a készüléken szinkronban tartani az internetes idővel a virtuális gép felderítése megfelelően működjön.
    - **Telepítse a frissítéseket**: Az Azure Migrate Server Assessment ellenőrzi, hogy a berendezés rendelkezik-e a legújabb frissítések.

### <a name="register-the-appliance-with-azure-migrate"></a>A berendezés regisztráljon az Azure Migrate

1. Kattintson a **bejelentkezés**. Ha nem jelenik meg, győződjön meg arról, akkor le lett tiltva az előugró ablakok a böngészőben.
2. Az új lapon jelentkezzen be Azure hitelesítő adatait.
    - Jelentkezzen be a felhasználónevét és jelszavát.
    - Jelentkezzen be a PIN-kód nem támogatott.
3. Miután sikeresen bejelentkezett, lépjen vissza a webalkalmazás.
4. Válassza ki az előfizetést, amelyben az Azure Migrate-projekt létrehozása. Ezután válassza ki a projektet.
5. Adjon meg egy nevet a berendezéshez. A névnek alfanumerikus karakterek és 14 karakter vagy kevesebb kell lennie.
6. Kattintson a **regisztrálása**.


### <a name="delegate-credentials-for-smb-vhds"></a>Hitelesítő adatok delegálása SMB virtuális merevlemezek

Ha virtuális merevlemezek futtatja az SMB-kben, engedélyeznie kell a Hyper-V-gazdagépek a készülék hitelesítő adatok delegálását. Ellenkező esetben ez az összes állomáson a [előző oktatóanyagban](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts), ez most a készülék do:

1. A virtuális berendezésen a következő parancs futtatásával. HyperVHost1/HyperVHost2 példa állomásnevek.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Azt is megteheti ehhez a a Helyicsoportházirend-szerkesztő a készüléken:
    - A **helyi számítógép-házirend** > **számítógép konfigurációja**, kattintson a **felügyeleti sablonok** > **rendszer**  >  **Hitelesítő adatok delegálása**.
    - Kattintson duplán a **friss hitelesítő adatok delegálásának engedélyezése**, és válassza ki **engedélyezve**.
    - A **beállítások**, kattintson a **megjelenítése**, és adja hozzá a listához, a felderíteni kívánt minden egyes Hyper-V-gazdagép **wsman /** előtagjaként.
    - A **hitelesítő adatok delegálása**, kattintson duplán a **csak NTLM-server-hitelesítéssel friss hitelesítő adatok delegálásának engedélyezése**. Újra, adja hozzá a listához, a felderíteni kívánt minden egyes Hyper-V-gazdagép **wsman /** előtagjaként.

## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

A készülék Hyper-V-gazdagépekhez vagy fürtökhöz csatlakozzon, és indítsa el a virtuális gépek felderítésének.

1. A **felhasználónév** és **jelszó**, adja meg a fiók hitelesítő adatait, hogy a berendezés virtuális gépek felderítéséhez használ majd. Adjon meg egy rövid nevet a hitelesítő adatokat, és kattintson a **részletek mentése**.
2. Kattintson a **Hozzáadás gazdagép**, és adja meg a Hyper-V gazdagép vagy fürt részletes adatai.
3. Kattintson a **ellenőrzése**. Ellenőrzést követően könnyen megtalálhatók legyenek az egyes gazdagépfürtön található virtuális gépek száma látható.
    - Ha az érvényesítés meghiúsul, a gazdagép, tekintse át a hiba az egérmutatót a ikonra a **állapot** oszlop. Hárítsa el a problémákat, és újra ellenőrzi.
    - Gazdagépek és fürtök eltávolításához jelölje ki > **törlése**.
    - Egy adott gazdagép fürtből nem távolítható el. Csak eltávolíthatja a teljes fürtöt.
    - Hozzáadhat egy fürthöz, még akkor is, ha a fürt adott gazdagépeire probléma adódik.
4. Kattintson az ellenőrzést követően **mentéséhez és a felderítés megkezdése** a felderítési folyamat elindításához.

Ez elindítja a felderítést. Az Azure Portalon megjelenő felderített virtuális gépek metaadatait hozzávetőlegesen 15 percet vesz igénybe.

### <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

Felderítés befejeződése után ellenőrizheti, hogy a virtuális gépek megjelennek-e a portálon.

1. Nyissa meg az Azure Migrate irányítópultot.
2. A **Azure Migrate - kiszolgálók** > **Azure Migrate: Server Assessment** lap, kattintson az ikont, amely megjeleníti a számát, a **felderített kiszolgálók**.

## <a name="set-up-an-assessment"></a>Értékelés beállítása

Értékelések futtatása az Azure Migrate Server Assessment használatával két típusa van.

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | Értékelések alapján összegyűjtött teljesítményadatok | **Virtuálisgép-méretet ajánlott**: A Processzor és a memória kihasználtsági adatok alapján.<br/><br/> **Lemez típusa (standard vagy prémium szintű felügyelt lemez) ajánlott**: Az IOPS és átviteli sebessége, a helyi lemezek alapján.
**Helyszíni** | Értékelések alapján helyszíni méretezést. | **Virtuálisgép-méretet ajánlott**: A helyszíni virtuális gép mérete alapján<br/><br> **Ajánlott a lemez típusát**: A tárolási típus beállítását választja, az értékelés alapján.



### <a name="run-an-assessment"></a>Értékelés futtatása

Értékelés futtatása a következőképpen:

1. Tekintse át a [ajánlott eljárások](best-practices-assessment.md) értékelés létrehozásához.
2. A **kiszolgálók** > **Azure Migrate: Server Assessment**, kattintson a **felmérési**.

    ![Értékelés](./media/tutorial-assess-hyper-v/assess.png)

3. A **kiszolgálók értékeléséhez**, adja meg az értékelés nevét.
4. Kattintson az **Összes megtekintése** elemre az értékelési tulajdonságok áttekintéséhez.

    ![Értékelés tulajdonságai](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. A **válassza ki vagy hozzon létre egy**válassza **hozzon létre új** , és adja meg a csoport nevét. Egy csoport egy vagy több virtuális gépeket az értékeléshez együtt gyűjt.
4. A **gépek felvétele a csoportba**, válassza ki a virtuális gépek hozzáadása a csoporthoz.
5. Kattintson a **értékelés létrehozása** hozza létre a csoportot, és az értékelés futtatása.

    ![Értékelés létrehozása](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Az értékelés létrehozása után megtekintheti a **kiszolgálók** > **Azure Migrate: Server Assessment**.
7. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.


## <a name="review-an-assessment"></a>Tekintse át az értékeléseket

Az értékelés ismerteti:

- **Azure-kompatibilitás**: Virtuális gépek alkalmasak-e az Azure-ba való migrálásra.
- **Havi költségbecslés**: A becsült havi számítási és tárolási költségek a virtuális gépek futtatásához az Azure-ban.
- **Havi tárolási költségbecsléshez**: Becsült költségek a lemezes tárolás az áttelepítés után.


### <a name="view-an-assessment"></a>Értékelés megjelenítése

1. A **áttelepítési célok** >  **kiszolgálók** > **Azure Migrate: Server Assessment**, kattintson a **értékelések**.
2. A **értékelések**, kattintson az értékelés való megnyitásához.

    ![Értékelés összegzése](./media/tutorial-assess-hyper-v/assessment-summary.png)


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

![Megbízhatósági minősítés](./media/tutorial-assess-hyper-v/confidence-rating.png)

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

Továbbra is a harmadik oktatóanyag a sorozat további információt a Hyper-V virtuális gépek migrálása az Azure Migrate-kiszolgáló áttelepítése Azure-bA.

> [!div class="nextstepaction"]
> [A Hyper-V virtuális gépek áttelepítése](./tutorial-migrate-hyper-v.md)
