---
title: Hyper-V virtuális gépek értékelése az Azure Áttelepítése segítségével az Azure-ba való áttelepítéshez | Microsoft dokumentumok
description: Bemutatja, hogyan értékelheti a helyszíni Hyper-V virtuális gépek az Azure-ba való áttelepítés az Azure-ba az Azure Áttelepítési kiszolgáló értékelése.
ms.topic: tutorial
ms.date: 03/23/2020
ms.custom: mvc
ms.openlocfilehash: cb3c29e01b7917a6d639b6b2a53fc2842efc2172
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80336774"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Hyper-V virtuális gépek felmérése az Azure Áttelepítési kiszolgáló felmérésével

Ez a cikk bemutatja, hogyan értékelheti a helyszíni Hyper-V virtuális gépek, az [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) eszközzel.


Ez az oktatóanyag a második egy sorozatban, amely bemutatja, hogyan lehet felmérni és áttelepíteni a Hyper-V virtuális gépek et az Azure-ba. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy Azure Migrate projektet.
> * Állítsa be és regisztrálja az Azure Migrate-berendezést.
> * A helyszíni virtuális gépek folyamatos felderítésének indítása.
> * Csoport felderített virtuális gépek, és értékelje a csoportot.
> * Tekintse át az értékelést.

> [!NOTE]
> Az oktatóanyagok a forgatókönyv legegyszerűbb telepítési útvonalát mutatják be, így gyorsan beállíthat egy proof-of-concept-et. Az oktatóanyagok lehetőség szerint az alapértelmezett beállításokat használják, és nem jelenítik meg az összes lehetséges beállítást és elérési utat. Részletes útmutatásért tekintse át az útmutató cikkeket.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/pricing/free-trial/) mielőtt elkezdené.


## <a name="prerequisites"></a>Előfeltételek

- [Fejezze be](tutorial-prepare-hyper-v.md) a sorozat első oktatóanyagát. Ha nem, az oktatóanyagutasításai nem fognak működni.
- Itt van, mit kellett volna tennie az első bemutató:
    - [Készítse elő](tutorial-prepare-hyper-v.md#prepare-azure) az Azure-t az Azure Migrate szolgáltatással való együttműködésre.
    - [Hyper-V gazdagépek](tutorial-prepare-hyper-v.md#prepare-hyper-v-for-assessment) és virtuális gépek felmérésének előkészítése.
    - [Ellenőrizze,](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment) hogy mire van szüksége az Azure Migrate-készülék hyper-V-felméréshez való üzembe helyezéséhez.

## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate projekt beállítása

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A keresési eredmények között válassza az **Azure Áttelepítése**lehetőséget.
3. Az **Áttekintés** területen a **Kiszolgálók felderítése, értékelése és migrálása** alatt kattintson a **Kiszolgálók értékelése és migrálása** lehetőségre.

    ![Kiszolgálók felfedezése és értékelése](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. Az **Első lépések** területen kattintson az **Eszközök hozzáadása** elemre.
5. A **Projekt áttelepítése** lapon válassza ki az Azure-előfizetést, és hozzon létre egy erőforráscsoportot, ha nem rendelkezik ilyentel.
6. A **Projekt részletei**területen adja meg a projekt nevét és azt a régiót, amelyben a projektet létre kívánja hozni. [Tekintse át](migrate-support-matrix.md#supported-geographies) azazure migrate projektet létrehozó régiókat.

    - A projektrégió csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál.
    - A virtuális gépek áttelepítésekénél másik Azure-célrégiót választhat. Az összes Azure-régió támogatja az áttelepítési cél.

    ![Azure Migrate projekt létrehozása](./media/tutorial-assess-hyper-v/migrate-project.png)

7. Kattintson a **Tovább** gombra.
8. Az **Assessment (Felmérés kiválasztása) eszközben**válassza az **Azure Migrate: Server Assessment** > **Next**lehetőséget.

    ![Azure Migrate projekt létrehozása](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. A **Migrálási eszköz kiválasztása** területen válassza **A migrálási eszköz hozzáadásának mellőzése egyelőre** > **Tovább** lehetőséget.
10. Az **Ellenőrzés + eszközök hozzáadása párbeszédpanelen**tekintse át a beállításokat, és kattintson az Eszközök **hozzáadása**gombra.
11. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik. Megnyílik a projekt oldala. Ha nem látja a projektet, az Azure Migrate irányítópult **Kiszolgálók** területéről elérheti.

## <a name="set-up-the-azure-migrate-appliance"></a>Az Azure Migrate-berendezés beállítása

Azure Migrate:server Assessment egy könnyű Azure Migrate készüléket használ. A készülék virtuális gép felderítése, és elküldi a virtuális gép metaadatait és teljesítményadatait az Azure Migrate.
- A készülék egy Hyper-V VM-en állítható be egy letöltött Hyper-V VHD használatával. Azt is megteheti, hogy a készüléket egy virtuális gépen vagy fizikai gépen egy PowerShell telepítő parancsfájlt.
- Ez az oktatóanyag a virtuális merevlemezt használja. Tekintse át [ezt a cikket,](deploy-appliance-script.md) ha parancsfájl használatával szeretné beállítani a készüléket.

A készülék létrehozása után ellenőrizze, hogy csatlakozhat-e az Azure Migrate:Server Assessment alkalmazáshoz, konfigurálhatja-e először, és regisztrálja az Azure Migrate projekttel.

### <a name="download-the-vhd"></a>A virtuális merevlemez letöltése

Töltse le a készülék tömörített VHD sablonját.

1. Az Azure Migrate: Server Assessment **(Áttelepítés:** > **Kiszolgálófelmérés)****alkalmazásban** > kattintson a **Felderítés**gombra.
2. A **Discover machines** > **Are your machines virtualizált?**, kattintson **igen, a Hyper-V**.
3. A **Virtuális merevlemez-fájl** letöltéséhez kattintson a Letöltés gombra.

    ![Virtuális gép letöltése](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>A biztonság ellenőrzése

A tömörített fájl telepítése előtt ellenőrizze, hogy a tömörített fájl biztonságos-e.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.

2. Futtassa a következő PowerShell parancsot a ZIP-fájl kivonatának létrehozásához
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  A 2.19.07.30-as verziójú készülék esetében a létrehozott kivonatnak meg kell egyeznie ezekkel a beállításokkal.

  **Algoritmus** | **Kivonat értéke**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc44f475872e35f04fcb8f338606c84534c117d1609f2d12444569b31

### <a name="create-the-appliance-vm"></a>A készülék virtuális gépének létrehozása

Importálja a letöltött fájlt, és hozza létre a virtuális gép.

1. Miután letöltötte a tömörített VHD fájlt a Hyper-V állomásra, amelyre a készülék virtuális gépe kerül, bontsa ki a tömörített fájlt.
    - A kibontott helyen a fájl kicsomagolja a **AzureMigrateAppliance_VersionNumber**.
    - Ez a mappa egy almappát tartalmaz, más néven **AzureMigrateAppliance_VersionNumber.**
    - Ez az almappa három további almappát tartalmaz : **Pillanatképek**, **virtuális merevlemezek**és **virtuális gépek**.

2. Nyissa meg a Hyper-V kezelőjét. A **Műveletek csoportban**kattintson **a Virtuális gép importálása gombra.**

    ![Virtuális merevlemez telepítése](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. A Virtuális gép importálása varázsló> **Mielőtt elkezdené,** kattintson a **Tovább**gombra.
3. A **Mappa megkeresése területen**jelölje ki a Virtuális **gépek** mappát. Kattintson a **Tovább** gombra.
1. A **Virtuális gép kiválasztása csoportban**kattintson a **Tovább**gombra.
2. A **Típus kiválasztása csoportban**kattintson **a Virtuális gép másolása gombra (hozzon létre egy új egyedi azonosítót).** Kattintson a **Tovább** gombra.
3. A **Cél kiválasztása területen**hagyja meg az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
4. A **Tárolómappák mezőben**hagyja meg az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
5. A **Hálózat kiválasztása területen**adja meg a virtuális gép által használni kívánt virtuális kapcsolót. A kapcsolónak internetkapcsolatra van szüksége az Azure-ba való adatküldéshez. [További információ](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) a virtuális kapcsolók létrehozásáról.
6. Az **Összegzés alkalmazásban**tekintse át a beállításokat. Ezután kattintson a **Befejezés** gombra.
7. A Hyper-V Manager > **virtuális gépek,** indítsa el a virtuális gépet.


### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-hoz való hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék virtuális gép [e-alapú URL-címekhez.](migrate-appliance.md#url-access)

### <a name="configure-the-appliance"></a>A készülék konfigurálása

Először állítsa be a készüléket.

> [!NOTE]
> Ha a letöltött virtuális merevlemez helyett [egy PowerShell-parancsfájl](deploy-appliance-script.md) használatával állítja be az akészüléket, az eljárás első két lépése nem releváns.

1. A Hyper-V Manager > **a virtuális gépek**ben kattintson a jobb gombbal a Virtuálisgép > Csatlakozás **parancsra.**
2. Adja meg a készülék nyelvét, időzónáját és jelszavát.
3. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozni tud a virtuális géphez, és nyissa meg a készülék webalkalmazásÁNAK URL-címét: **https:// készülék nevét vagy*IP-címét:* 44368**.

   Másik lehetőségként az alkalmazás parancsikonjára kattintva is megnyithatja az alkalmazást a készülék asztaláról.
1. A webalkalmazásban > **Az előfeltételek beállítása**beállításhoz tegye a következőket:
    - **Licenc**: Fogadja el a licencfeltételeket, és olvassa el a harmadik fél től származó információkat.
    - **Kapcsolat:** Az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
      - Kattintson **a Proxy beállításai gombra,** és adja http://ProxyIPAddress http://ProxyFQDNmeg a proxycímet és a figyelőportot az űrlapon vagy a ban.
      - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
      - Csak a HTTP-proxyk használata támogatott.
    - **Időszinkronizálás**: Az idő ellenőrizve van. A készüléken töltött időnek szinkronban kell lennie az internetidővel, hogy a virtuális gép felderítése megfelelően működjön.
    - **Frissítések telepítése**: Az Azure Migrate Server Assessment ellenőrzi, hogy a készüléken telepítve vannak-e a legújabb frissítések.

### <a name="register-the-appliance-with-azure-migrate"></a>Regisztrálja a készüléket az Azure Migrate szolgáltatással

1. Kattintson **a Bejelentkezés gombra.** Ha nem jelenik meg, győződjön meg arról, hogy letiltotta az előugró ablakok blokkolását a böngészőben.
2. Az új lapon jelentkezzen be az Azure-hitelesítő adatokkal.
    - Jelentkezzen be felhasználónevével és jelszavával.
    - A PIN-kóddal való bejelentkezés nem támogatott.
3. Sikeres bejelentkezés után lépjen vissza a webalkalmazásba.
4. Válassza ki azt az előfizetést, amelyben az Azure Migrate projekt jött létre. Ezután válassza ki a projektet.
5. Adja meg a készülék nevét. A névnek alfanumerikusnak kell lennie, legkevesebb karakterrel.
6. Kattintson a **Regisztráció gombra.**


### <a name="delegate-credentials-for-smb-vhds"></a>SMB VHD-k hitelesítő adatainak delegálása

Ha virtuális gépeken fut, engedélyeznie kell a hitelesítő adatok delegálását a készülékről a Hyper-V gazdagépekre. Ehhez a következőkre van szükség:

- Lehetővé teszi, hogy minden állomás meghatalmazottként működjön a készüléken. Ha követte az oktató érdekében, akkor ezt az előző bemutató, amikor készített Hyper-V értékelése és a migráció. A CredSSP-t [manuálisan](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts)kell beállítania az állomásokhoz, vagy egy ezt elvezető [parancsfájl futtatásával.](tutorial-prepare-hyper-v.md#prepare-with-a-script)
- CredSSP-delegálás engedélyezése, hogy az Azure Migrate készülék működhet az ügyfél, delegálása hitelesítő adatokat egy gazdagép.

Engedélyezze a készüléken az alábbiak szerint:

#### <a name="option-1"></a>1. lehetőség

A virtuális gép a készüléken futtassa ezt a parancsot. HyperVHost1/HyperVHost2 példa állomásnevek.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
```

Például: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>2. lehetőség

Ezt a készülék helyi csoportházirend-szerkesztőjében is megteheti:

1. A **Helyi számítógépházirend-konfiguráció** > **párbeszédpanelen**kattintson **a Felügyeleti sablonok** > **rendszerhitelesítő** > **adatok delegálása**elemre.
2. Kattintson duplán **a Friss hitelesítő adatok delegálásának engedélyezése**elemre, és válassza az **Engedélyezve**lehetőséget.
3. A **Beállítások párbeszédpanelen**kattintson a **Megjelenítés**gombra, és adja hozzá a felderíteni kívánt Hyper-V-állomásokat a listához, **a wsman/** előtaggal.
4. Ezután a **Hitelesítő adatok delegálása**területen kattintson duplán a **Friss hitelesítő adatok delegálásának engedélyezése csak NTLM kiszolgálóhitelesítéssel**elemre. Ismét adja hozzá az egyes Hyper-V gazdagépeket, amelyeket fel szeretne deríteni a listához, **a wsman/** előtaggal.

## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

Csatlakozzon a készülékről a Hyper-V gazdagépekhez vagy fürtökhöz, és indítsa el a virtuális gép felderítését.

1. A **Felhasználónév** és **jelszó**csoportban adja meg a fiók hitelesítő adatait, amelyeket a készülék a virtuális gépek felderítéséhez használ. Adja meg a hitelesítő adatok rövid nevét, majd kattintson a **Részletek mentése gombra.**
2. Kattintson **az Állomás hozzáadása**gombra, és adja meg a Hyper-V állomás/fürt adatait.
3. Kattintson a **Validate** (Érvényesítés) elemre. Az ellenőrzés után az egyes gazdagépeken/fürtökön felderíthető virtuális gépek száma látható.
    - Ha egy állomás érvényesítése sikertelen, tekintse át a hibát úgy, hogy az Állapot oszlopban az ikonra viszi az **egérmutatót.** Javítsa ki a problémákat, és ellenőrizze újra.
    - Az állomások vagy fürtök eltávolításához válassza a > **Törlés**lehetőséget.
    - Egy adott állomás nem távolítható el a fürtből. Csak a teljes fürtet távolíthatja el.
    - Fürt hozzáadása akkor is, ha problémák vannak a fürt adott állomásaival.
4. Az ellenőrzés után kattintson a Mentés gombra, és indítsa el a **felderítést** a felderítési folyamat elindításához.

Ez elindítja a felderítést. Hostonként körülbelül 1,5 percet vesz igénybe, hogy a felderített kiszolgálók metaadatai megjelenjenek az Azure Portalon.

### <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

A felderítés befejezése után ellenőrizheti, hogy a virtuális gépek megjelennek-e a portálon.

1. Nyissa meg az Azure Migrate irányítópultot.
2. Az **Azure Migrate – Servers** > **Azure Migrate: Server Assessment (Kiszolgálói értékelés)** lapon kattintson a **felderített kiszolgálók**számát megjelenítő ikonra.

## <a name="set-up-an-assessment"></a>Értékelés beállítása

Kétféle értékelések futtatható az Azure Áttelepítési kiszolgáló értékelése használatával.

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | Összegyűjtött teljesítményadatokon alapuló értékelések | **Ajánlott virtuális gép mérete:** A PROCESSZOR és a memória kihasználtsági adatai alapján.<br/><br/> **Ajánlott lemeztípus (standard vagy prémium díjas felügyelt lemez)**: Az IOPS és a helyszíni lemezek átviteli hatása alapján.
**A helyszíni** | A helyszíni méretezésen alapuló értékelések. | **Ajánlott virtuális gép mérete:** A helyszíni virtuális gép mérete alapján<br/><br> **Ajánlott lemeztípus:** Az értékeléshez kiválasztott tárolási típusbeállítás alapján.



### <a name="run-an-assessment"></a>Értékelés futtatása

Végezze el az értékelést az alábbiak szerint:

1. Tekintse át az értékelések létrehozásának [ajánlott gyakorlatait.](best-practices-assessment.md)
2. A **Kiszolgálók** > **Azure Migrate: Server Assessment**területén kattintson a **Felértékelés gombra.**

    ![Kiértékelés](./media/tutorial-assess-hyper-v/assess.png)

3. A **Kiszolgálók értékelése csoportban**adja meg az értékelés nevét.
4. Kattintson az **Összes megtekintése** elemre az értékelési tulajdonságok áttekintéséhez.

    ![Értékelési tulajdonságok](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. A **Csoport kijelölése vagy létrehozása**csoportban válassza az Új **létrehozása**lehetőséget, és adja meg a csoport nevét. Egy csoport összegyűjti egy vagy több virtuális gépek együtt értékelésre.
4. A **Gépek hozzáadása a csoporthoz**csoportban válassza a csoporthoz hozzáadni kívánt virtuális gépek lehetőséget.
5. Kattintson **az Értékelés létrehozása** a csoport létrehozásához, és futtassa az értékelést.

    ![Értékelés létrehozása](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Az értékelés létrehozása után tekintse meg a **Kiszolgálók** > **Azure Áttelepítés: Kiszolgálóértékelése**.
7. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.


## <a name="review-an-assessment"></a>Értékelés áttekintése

Az értékelés a következőket írja le:

- **Azure-készenlét:** Függetlenül attól, hogy a virtuális gépek alkalmasak-e az Azure-ba való migráláshoz.
- **Havi költségbecslés:** A virtuális gépek Azure-beli futtatásának becsült havi számítási és tárolási költségei.
- **Havi tárolási költség becslés:** Az áttelepítés utáni lemeztárolás becsült költségei.


### <a name="view-an-assessment"></a>Értékelés megtekintése

1. Az **áttelepítési célok** >  **kiszolgálóinak** > **Azure Migrate: Server Assessment (Áttelepítési kiszolgáló értékelése)** területén kattintson az **Értékelések**elemre.
2. Az **Értékelések menüben**kattintson egy értékelésre a megnyitásához.

    ![Az értékelés összefoglalója](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Az Azure-ra való felkészültség áttekintése

1. Az **Azure-ban készenléti**ellenőrizze, hogy a virtuális gépek készen állnak-e az Azure-ba való migrálásra.
2. Tekintse át a virtuális gép állapotát:
    - **Készen áll az Azure-ra:** Az Azure Migrate a virtuális gépek méretének és költségbecslésének ajánlott a felmérésben.
    - **Kész a feltételekkel:** Problémák és javasolt szervizelés megjelenítése.
    - **Nem áll készen az Azure-ra:** Problémák és javasolt szervizelés megjelenítése.
    - **Készenlét ismeretlen:** Akkor használatos, ha az Azure Migrate nem tudja felmérni a készenlétet az adatok rendelkezésre állásával kapcsolatos problémák miatt.

2. Kattintson egy **Azure-készenléti** állapotra. Megtekintheti a virtuális gépek készenléti részleteit, és leáshat a virtuális gép részleteinek megtekintéséhez, beleértve a számítási, tárolási és hálózati beállításokat.

### <a name="review-cost-details"></a>Költségrészletek áttekintése

Ez a nézet az Azure-ban futó virtuális gépek becsült számítási és tárolási költségét jeleníti meg.

1. Tekintse át a havi számítási és tárolási költségeket. A költségek összesítve vannak az értékelt csoport összes virtuális gépénél.

    - A költségbecslések a gép méretre vonatkozó javaslatain, valamint lemezein és tulajdonságain alapulnak.
    - A számítási és tárolási becsült havi költségek láthatók.
    - A költségbecslés a helyszíni virtuális gépek iaaS virtuális gépekként való futtatásához. Az Azure Áttelepítési kiszolgáló értékelése nem veszi figyelembe a PaaS- vagy SaaS-költségeket.

2. Megtekintheti a havi tárolási költségbecsléseket. Ez a nézet az értékelt csoport összesített tárolási költségeit mutatja, különböző típusú tárolólemezekre osztva.
3. Részletezheti az adott virtuális gépek részleteit.


### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

Teljesítményalapú értékelések futtatásakor megbízhatósági minősítés van rendelve az értékeléshez.

![Megbízhatósági minősítés](./media/tutorial-assess-hyper-v/confidence-rating.png)

- Az 1 csillagos (legalacsonyabb) és az 5 csillagos (legmagasabb) minősítést kapja.
- A megbízhatósági besorolás segít megbecsülni az értékelés által biztosított méretjavaslatok megbízhatóságát.
- A megbízhatósági minősítés az értékelés kiszámításához szükséges adatpontok rendelkezésre állásán alapul.

Az értékelés megbízhatósági minősítései a következők.

**Az adatpont elérhetősége** | **Megbízhatósági minősítés**
--- | ---
0%–20% | 1 csillag
21%–40% | 2 csillag
41%–60% | 3 csillag
61%–80% | 4 csillag
81%–100% | 5 csillag

[További információ](best-practices-assessment.md#best-practices-for-confidence-ratings) a megbízhatósági minősítésekkel kapcsolatos gyakorlati tanácsokról.





## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Azure Migrate-berendezés beállítása
> * Értékelés létrehozása és felülvizsgálata

Folytassa a sorozat harmadik oktatóanyagával, amelyből megtudhatja, hogyan telepítheti át a Hyper-V virtuális gépeket az Azure-ba az Azure Áttelepítési kiszolgáló áttelepítésével.

> [!div class="nextstepaction"]
> [Hyper-V virtuális gépek migrálása](./tutorial-migrate-hyper-v.md)
