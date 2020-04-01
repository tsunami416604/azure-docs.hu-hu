---
title: Az Azure Áttelepítési Kiszolgáló felmérésével fizikai kiszolgálók at vizsgálhat az Azure-ba való áttelepítéshez
description: Ez a témakör azt ismerteti, hogyan értékelheti a helyszíni fizikai kiszolgálókat az Azure-ba való áttelepítéshez az Azure Áttelepítési kiszolgáló értékelése használatával.
ms.topic: tutorial
ms.date: 11/18/2019
ms.openlocfilehash: c89c731712a625e5f3b7a1a7e9306f6a7480b96b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76990300"
---
# <a name="assess-physical-servers-with-azure-migrate-server-assessment"></a>Fizikai kiszolgálók felmérése az Azure Áttelepítése szolgáltatással: Kiszolgálófelmérés

Ez a cikk bemutatja, hogyan értékelheti a helyszíni fizikai kiszolgálók, az Azure Áttelepítés: Kiszolgálóértékelése eszközzel.

[Az Azure Migrate](migrate-services-overview.md) olyan eszközök központi eszköze, amelyek segítségével felderítheti, felmérheti és áttelepítheti az alkalmazásokat, az infrastruktúrát és a számítási feladatokat a Microsoft Azure-ba. A központ tartalmazza az Azure Migrate eszközöket és a külső független szoftverszállítói (ISV) ajánlatokat.

Ez az oktatóanyag a második olyan sorozat, amely bemutatja, hogyan lehet felmérni és áttelepíteni a fizikai kiszolgálók at Azure.This tutorial is the second in a series that demonstrates how to assess and migrate physical servers to Azure. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Hozzon létre egy Azure Migrate projektet.
> * Hozzon létre egy Azure Migrate-berendezést, amely a helyszínen fut a fizikai kiszolgálók felméréséhez.
> * A helyszíni fizikai kiszolgálók folyamatos felderítésének indítása. A készülék elküldi a felderített kiszolgálók konfigurációs és teljesítményadatait az Azure-ba.
> * Csoportosítsa a felderített kiszolgálókat, és értékelje a kiszolgálócsoportot.
> * Tekintse át az értékelést.

> [!NOTE]
> Az oktatóanyagok a forgatókönyv legegyszerűbb telepítési útvonalát mutatják be, így gyorsan beállíthat egy proof-of-concept-et. Az oktatóanyagok lehetőség szerint az alapértelmezett beállításokat használják, és nem jelenítik meg az összes lehetséges beállítást és elérési utat. Részletes útmutatásért tekintse át az útmutató cikkeket.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/pricing/free-trial/) mielőtt elkezdené.


## <a name="prerequisites"></a>Előfeltételek

- [Fejezze be](tutorial-prepare-physical.md) a sorozat első oktatóanyagát. Ha nem, az oktatóanyagutasításai nem fognak működni.
- Itt van, mit kellett volna tennie az első bemutató:
    - [Állítsa be az Azure-engedélyeket](tutorial-prepare-physical.md#prepare-azure) az Azure Migrate számára.
    - [Készítse elő a fizikai kiszolgálókat](tutorial-prepare-physical.md#prepare-for-physical-server-assessment) az értékelésre. A készülékre vonatkozó követelményeket ellenőrizni kell. A fizikai kiszolgálófelderítéshez be kell állítani egy fiókot is. A szükséges portok nak elérhetőnek kell lenniük, és tisztában kell lennie az Azure-hoz való hozzáféréshez szükséges URL-címekkel.


## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate projekt beállítása

Állítson be egy új Azure Migrate-projektet a következők szerint.

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés** területen a **Kiszolgálók felderítése, értékelése és migrálása** alatt kattintson a **Kiszolgálók értékelése és migrálása** lehetőségre.

    ![Kiszolgálók felfedezése és értékelése](./media/tutorial-assess-physical/assess-migrate.png)

4. Az **Első lépések** területen kattintson az **Eszközök hozzáadása** elemre.
5. A **Projekt migrálása** területen válassza ki az Azure-előfizetését, majd hozzon létre egy erőforráscsoportot, ha még nem rendelkezik eggyel.     
6. A **Projekt részletei**területen adja meg a projekt nevét és azt a földrajzi elhelyezkedést, amelyben a projektet létre szeretné hozni. Ázsia, Európa, az Egyesült Királyság és az Egyesült Államok támogatott.

    - A projekt földrajzi csak a helyszíni kiszolgálókról gyűjtött metaadatok tárolására szolgál.
    - Migrálás futtatása során bármilyen célrégiót választhat.

    ![Azure Migrate projekt létrehozása](./media/tutorial-assess-physical/migrate-project.png)


7. Kattintson a **Tovább** gombra.
8. Az **Assessment (Felmérés kiválasztása) eszközben**válassza az **Azure Migrate: Server Assessment** > **Next**lehetőséget.

    ![Azure Migrate projekt létrehozása](./media/tutorial-assess-physical/assessment-tool.png)

9. A **Migrálási eszköz kiválasztása** területen válassza **A migrálási eszköz hozzáadásának mellőzése egyelőre** > **Tovább** lehetőséget.
10. Az **Ellenőrzés + eszközök hozzáadása párbeszédpanelen**tekintse át a beállításokat, és kattintson az Eszközök **hozzáadása**gombra.
11. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik. Megnyílik a projekt oldala. Ha nem látja a projektet, az Azure Migrate irányítópult **Kiszolgálók** területéről elérheti.


## <a name="set-up-the-appliance"></a>A készülék beállítása

Azure Migrate: Server Assessment fut egy könnyű berendezés.

- Ez a készülék fizikai kiszolgálófelderítést hajt végre, és elküldi a kiszolgáló metaadatait és teljesítményadatait az Azure Migrate Server Assessment szolgáltatásnak.
- A készülék beállításához:
    - Töltsön le egy tömörített fájlt az Azure Migrate telepítőparancsfájljával az Azure Portalról.
    - Bontsa ki a tartalmát a tömörített fájlból. Indítsa el a PowerShell-konzolrendszergazdai jogosultságokkal.
    - Hajtsa végre a PowerShell-parancsfájlt a készülék webalkalmazás elindításához.
    - Konfigurálja a készüléket az első alkalommal, és regisztrálja az Azure Migrate projekt.
- Egyetlen Azure Migrate-projekthez több készüléket is beállíthat. Az összes készülék, akkor fedezze fel tetszőleges számú fizikai szerverek. Készülékenként legfeljebb 250 kiszolgáló fedezhető fel.

### <a name="download-the-installer-script"></a>A telepítőparancsfájl letöltése

Töltse le a készülék tömörített fájlját.

1. Az Azure Migrate: Server Assessment **(Áttelepítés:** > **Kiszolgálófelmérés)****alkalmazásban** > kattintson a **Felderítés**gombra.
2. A **Discover machines** > **Are your machines virtualizált?**, kattintson a Nem **virtualizált/ Egyéb gombra.**
3. A **tömörített** fájl letöltéséhez kattintson a Letöltés gombra.

    ![Telepítő letöltése](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>A biztonság ellenőrzése

A tömörített fájl telepítése előtt ellenőrizze, hogy a tömörített fájl biztonságos-e.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. A következő parancs futtatása a tömörített fájl kivonatának létrehozásához
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3.  A legújabb készülék verzió, a generált kivonat olt kell egyeznie ezekkel a beállításokkal.

  **Algoritmus** | **Kivonat értéke**
  --- | ---
  MD5 | 1e92ede3e87c03bd148e56a708cdd33f
  SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

### <a name="run-the-azure-migrate-installer-script"></a>Az Azure Migrate telepítőparancsfájljának futtatása

A telepítő parancsfájl jaa következő:

- Ügynökök és egy webalkalmazás telepítése a fizikai kiszolgáló felderítéséhez és értékeléséhez.
- Telepítse a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Az IIS újraírható moduljának letöltése és telepítése. [További információ](https://www.microsoft.com/download/details.aspx?id=7435).
- Frissíti a beállításkulcsot (HKLM) az Azure Migrate állandó beállításrészleteivel.
- A következő fájlokat hozza létre az elérési út alatt:
    - **Konfigurációs fájlok**: %ProgramData%\Microsoft Azure\Config
    - **Naplófájlok**: %ProgramData%\Microsoft Azure\Logs

Futtassa a parancsfájlt az alábbiak szerint:

1. Bontsa ki a tömörített fájlt a kiszolgálón lévő, a készüléket fogadó mappába.
2. Indítsa el a PowerShellt a fenti kiszolgálón rendszergazdai (emelt szintű) jogosultsággal.
3. Módosítsa a PowerShell-könyvtárat arra a mappára, ahol a tartalom kilett nyerve a letöltött tömörített fájlból.
4. Futtassa az **AzureMigrateInstaller.ps1** nevű parancsfájlt a következő parancs futtatásával:
    ```
    PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
    ```
A parancsfájl elindítja a készülék webalkalmazását, amint az sikeresen befejeződik.

Bármilyen probléma esetén a parancsfájlnaplókat a C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log számú időbélyegű naplóban érheti el.

> [!NOTE]
> Kérjük, ne hajtsa végre az Azure Migrate telepítő parancsfájlt egy meglévő Azure Migrate-berendezésen.

### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-hoz való hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék képes csatlakozni az [Azure URL-címekhez.](migrate-appliance.md#url-access)


### <a name="configure-the-appliance"></a>A készülék konfigurálása

Először állítsa be a készüléket.

1. Nyisson meg egy böngészőt minden olyan gépen, amely csatlakozni tud a készülékhez, és nyissa meg a készülék webalkalmazás URL-címét: **https:// készülék nevét vagy*IP-címét:* 44368**.

   Az alkalmazás parancsikonjára kattintva az alkalmazás parancsikonjára kattintva is megnyithatja az alkalmazást.
2. A webalkalmazásban > **Az előfeltételek beállítása**beállításhoz tegye a következőket:
    - **Licenc**: Fogadja el a licencfeltételeket, és olvassa el a harmadik fél től származó információkat.
    - **Kapcsolat**: Az alkalmazás ellenőrzi, hogy a kiszolgáló rendelkezik-e internet-hozzáféréssel. Ha a kiszolgáló proxyt használ:
        - Kattintson **a Proxy beállításai gombra,** és adja http://ProxyIPAddress http://ProxyFQDNmeg a proxycímet és a figyelőportot az űrlapon vagy a ban.
        - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
        - Csak a HTTP-proxyk használata támogatott.
    - **Időszinkronizálás**: Az idő ellenőrizve van. A készüléken töltött időnek szinkronban kell lennie az internetidővel, hogy a kiszolgálófelderítés megfelelően működjön.
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


## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

Most csatlakoztassa a készülékről a fizikai kiszolgálókat, hogy felfedezzék, és indítsa el a felderítést.

1. Kattintson a **Hitelesítő adatok hozzáadása gombra** a kiszolgálók felderítéséhez használt fiókhitelesítő adatok megadásához.  
2. Adja meg az **operációs rendszert,** a hitelesítő adatok rövid nevét, a **felhasználónevet** és a **jelszót,** majd kattintson a **Hozzáadás gombra.**
Windows- és Linux-kiszolgálókhoz egy-egy hitelesítő adatot adhat hozzá.
4. Kattintson **a Kiszolgáló hozzáadása**gombra, és adja meg a kiszolgáló adatait – FQDN/IP-cím és a hitelesítő adatok rövid neve (soronként egy bejegyzés) a kiszolgálóhoz való csatlakozáshoz.
3. Kattintson a **Validate** (Érvényesítés) elemre. Az ellenőrzés után megjelenik a felderíthető kiszolgálók listája.
    - Ha egy kiszolgáló érvényesítése sikertelen, tekintse át a hibát úgy, hogy az **Állapot** oszlopban az ikonra viszi az egérmutatót. Javítsa ki a problémákat, és ellenőrizze újra.
    - Kiszolgáló eltávolításához válassza a > **törlés**lehetőséget.
4. Az ellenőrzés után kattintson a Mentés gombra, és indítsa el a **felderítést** a felderítési folyamat elindításához.

Ez elindítja a felderítést. Kiszolgálónként körülbelül 1,5 percet vesz igénybe, hogy a felderített kiszolgáló metaadatai megjelenjenek az Azure Portalon.

### <a name="verify-servers-in-the-portal"></a>Kiszolgálók ellenőrzése a portálon

A felderítés után ellenőrizheti, hogy a kiszolgálók megjelennek-e az Azure Portalon.

1. Nyissa meg az Azure Migrate irányítópultot.
2. Az **Azure Migrate – Servers** > **Azure Migrate: Server Assessment (Kiszolgálói értékelés)** lapon kattintson a **felderített kiszolgálók**számát megjelenítő ikonra.

## <a name="set-up-an-assessment"></a>Értékelés beállítása

Az Azure Migrate használatával kétféle értékelést hozhat létre: Kiszolgálói értékelés.

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | Összegyűjtött teljesítményadatokon alapuló értékelések | **Ajánlott virtuális gép mérete:** A PROCESSZOR és a memória kihasználtsági adatai alapján.<br/><br/> **Ajánlott lemeztípus (standard vagy prémium díjas felügyelt lemez)**: Az IOPS és a helyszíni lemezek átviteli hatása alapján.
**A helyszíni** | A helyszíni méretezésen alapuló értékelések. | **Ajánlott virtuális gép méret:** A helyszíni kiszolgáló mérete alapján<br/><br> **Ajánlott lemeztípus:** Az értékeléshez kiválasztott tárolási típusbeállítás alapján.


### <a name="run-an-assessment"></a>Értékelés futtatása

Végezze el az értékelést az alábbiak szerint:

1. Tekintse át az értékelések létrehozásának [ajánlott gyakorlatait.](best-practices-assessment.md)
2. A **Kiszolgálók** lap **Azure Migrate: Server Assessment (Kiszolgálói felmérés)** csempéjén kattintson a **Felértékelés gombra.**

    ![Kiértékelés](./media/tutorial-assess-physical/assess.png)

2. A **Kiszolgálók felmérése**területen adja meg az értékelés nevét.
3. Kattintson az **Összes megtekintése** elemre az értékelési tulajdonságok áttekintéséhez.

    ![Értékelési tulajdonságok](./media/tutorial-assess-physical/view-all.png)

3. A **Csoport kijelölése vagy létrehozása**csoportban válassza az Új **létrehozása**lehetőséget, és adja meg a csoport nevét. Egy csoport összegyűjt egy vagy több kiszolgálót értékelésre.
4. A **Gépek hozzáadása a csoporthoz**csoportban válassza ki a csoporthoz hozzáadni kívánt kiszolgálókat.
5. Kattintson **az Értékelés létrehozása** a csoport létrehozásához, és futtassa az értékelést.

    ![Értékelés létrehozása](./media/tutorial-assess-physical/assessment-create.png)

6. Az értékelés létrehozása után tekintse meg a **Kiszolgálók** > **Azure Áttelepítés: Kiszolgálóértékelési** > **értékelések**című részben.
7. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.



## <a name="review-an-assessment"></a>Értékelés áttekintése

Az értékelés a következőket írja le:

- **Azure-készenlét:** A kiszolgálók alkalmasak-e az Azure-ba való áttelepítésre.
- **Havi költségbecslés:** A kiszolgálók Azure-beli futtatásának becsült havi számítási és tárolási költségei.
- **Havi tárolási költség becslés:** Az áttelepítés utáni lemeztárolás becsült költségei.

### <a name="view-an-assessment"></a>Értékelés megtekintése

1. Az **áttelepítési célok** >  **kiszolgálói között**kattintson az Értékelések **az** **Azure Áttelepítés: Kiszolgálói értékelés**elemre.
2. Az **Értékelések menüben**kattintson egy értékelésre a megnyitásához.

    ![Az értékelés összefoglalója](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Az Azure-ra való felkészültség áttekintése

1. Az **Azure-ban készenléti**győződjön meg arról, hogy a kiszolgálók készen állnak az Azure-ba való áttelepítésre.
2. Tekintse át az állapotot:
    - **Készen áll az Azure-ra:** Az Azure Migrate a virtuális gépek méretének és költségbecslésének ajánlott a felmérésben.
    - **Kész a feltételekkel:** Problémák és javasolt szervizelés megjelenítése.
    - **Nem áll készen az Azure-ra:** Problémák és javasolt szervizelés megjelenítése.
    - **Készenlét ismeretlen:** Akkor használatos, ha az Azure Migrate nem tudja felmérni a készenlétet az adatok rendelkezésre állásával kapcsolatos problémák miatt.

2. Kattintson egy **Azure-készenléti** állapotra. Megtekintheti a kiszolgáló készenléti részleteit, és leáshat a kiszolgáló részleteinek megtekintéséhez, beleértve a számítási, tárolási és hálózati beállításokat.



### <a name="review-cost-details"></a>Költségrészletek áttekintése

Ez a nézet az Azure-ban futó virtuális gépek becsült számítási és tárolási költségét jeleníti meg.

1. Tekintse át a havi számítási és tárolási költségeket. A költségek összesítése az értékelt csoport összes kiszolgálójára vonatkozóan.

    - A költségbecslések a gép méretre vonatkozó javaslatain, valamint lemezein és tulajdonságain alapulnak.
    - A számítási és tárolási becsült havi költségek láthatók.
    - A költségbecslés a helyszíni kiszolgálók IaaS virtuális gépekként való futtatásához. Az Azure Áttelepítési kiszolgáló értékelése nem veszi figyelembe a PaaS- vagy SaaS-költségeket.

2. Megtekintheti a havi tárolási költségbecsléseket. Ez a nézet az értékelt csoport összesített tárolási költségeit mutatja, különböző típusú tárolólemezekre osztva.
3. Leáshat az adott kiszolgálók részleteinek megtekintéséhez.


### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

Teljesítményalapú értékelések futtatásakor megbízhatósági minősítés van rendelve az értékeléshez.

![Megbízhatósági minősítés](./media/tutorial-assess-physical/confidence-rating.png)

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

Folytassa a sorozat harmadik oktatóanyagával, és ismerje meg, hogyan telepítheti át a fizikai kiszolgálókat az Azure-ba az Azure Áttelepítés: Kiszolgálóáttelepítés szolgáltatással.

> [!div class="nextstepaction"]
> [Fizikai kiszolgálók migrálása](./tutorial-migrate-physical-virtual-machines.md)
