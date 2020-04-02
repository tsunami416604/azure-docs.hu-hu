---
title: A VMware virtuális gépek felmérése az Azure-ba való migráláshoz
description: Bemutatja, hogyan értékelheti a helyszíni VMware virtuális gépeket az Azure-ba való áttelepítéshez az Azure Áttelepítési kiszolgáló értékelése használatával.
ms.topic: tutorial
ms.date: 03/23/2019
ms.openlocfilehash: 944b7c12a353a29a172576974261eece63ebf668
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548746"
---
# <a name="assess-vmware-vms-by-using-azure-migrate-server-assessment"></a>VMware virtuális gépek felmérése az Azure Migrate Server Assessment használatával

Ez a cikk bemutatja, hogyan értékelheti a helyszíni VMware virtuális gépek (VM-ek) az [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) eszközzel.


Ez az oktatóanyag a második olyan sorozatban, amely bemutatja a VMware virtuális gépek felmérését és áttelepítését az Azure-ba. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Hozzon létre egy Azure Migrate projektet.
> * Hozzon létre egy Azure Migrate-berendezést, amely a helyszínen fut a virtuális gépek értékeléséhez.
> * A helyszíni virtuális gépek folyamatos felderítésének indítása. A készülék elküldi a felderített virtuális gépek konfigurációs és teljesítményadatait az Azure-ba.
> * Csoport felderített virtuális gépek, és a virtuális gép csoport felmérése.
> * Tekintse át az értékelést.

> [!NOTE]
> Az oktatóanyagok a forgatókönyv legegyszerűbb telepítési útvonalát mutatják be, így gyorsan beállíthat egy proof-of-concept-et. Az oktatóanyagok lehetőség szerint az alapértelmezett beállításokat használják, és nem jelenítik meg az összes lehetséges beállítást és elérési utat. Részletes utasításokért tekintse át az útmutató cikkeket.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/pricing/free-trial/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

- Fejezze be a sorozat [első oktatóanyagát.](tutorial-prepare-vmware.md) Ha nem, az oktatóanyagutasításai nem fognak működni.
- Itt van, mit kellett volna tennie az első bemutató:
    - [Készítse elő](tutorial-prepare-vmware.md#prepare-azure) az Azure-t az Azure Migrate szolgáltatással való együttműködésre.
    - [Készítse elő a VMware-t értékelésre.](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) Ez magában foglalja a VMware-beállítások ellenőrzését, egy olyan fiók beállítását, amelyet az Azure Migrate használhat a vCenter Server eléréséhez.
    - [Ellenőrizze,](tutorial-prepare-vmware.md#verify-appliance-settings-for-assessment) hogy mire van szüksége az Azure Migrate-berendezés vmware-felméréshez való üzembe helyezéséhez.

## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate projekt beállítása

Hozzon létre egy új Azure Migrate projektet az alábbiak szerint:

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
1. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
1. Az **Áttekintés csoportban**a **Kiszolgálók felderítése, felmérése és áttelepítése**csoportban válassza **a Kiszolgálók felmassza és áttelepítése**lehetőséget.

   ![A kiszolgálók értékelésére és áttelepítésére szolgáló gomb](./media/tutorial-assess-vmware/assess-migrate.png)

1. Az **első lépések**csoportban válassza az Eszközök **hozzáadása**lehetőséget.
1. A **Projekt migrálása** területen válassza ki az Azure-előfizetését, majd hozzon létre egy erőforráscsoportot, ha még nem rendelkezik eggyel.     
1. A **Projekt részletei**területen adja meg a projekt nevét és azt a földrajzi elhelyezkedést, amelyben a projektet létre kívánja hozni. Ázsia, Európa, az Egyesült Királyság és az Egyesült Államok támogatott.

   A projekt helye csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál. Migrálás futtatása során bármilyen célrégiót választhat.

   ![A projekt nevének és régiójának mezői](./media/tutorial-assess-vmware/migrate-project.png)

1. Válassza a **Tovább lehetőséget.**
1. Az **Assessment (Felmérés kiválasztása) eszközben**válassza az **Azure Migrate: Server Assessment** > **Next**lehetőséget.

   ![A Kiszolgálóértékelés eszköz kiválasztása](./media/tutorial-assess-vmware/assessment-tool.png)

1. A **Migrálási eszköz kiválasztása** területen válassza **A migrálási eszköz hozzáadásának mellőzése egyelőre** > **Tovább** lehetőséget.
1. Az **Ellenőrzés + eszközök hozzáadása párbeszédpanelen**tekintse át a beállításokat, és válassza az Eszközök **hozzáadása**lehetőséget.
1. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik. Megnyílik a projekt oldala. Ha nem látja a projektet, az Azure Migrate irányítópult **Kiszolgálók** területéről elérheti.

## <a name="set-up-the-azure-migrate-appliance"></a>Az Azure Migrate-berendezés beállítása

Azure Migrate:Server Assessment egy könnyű Azure Migrate készüléket használ. A készülék virtuális gép felderítése, és elküldi a virtuális gép metaadatait és teljesítményadatait az Azure Migrate.
- A készülék egy VMware VM-en állítható be egy letöltött OVA sablon használatával. Azt is megteheti, hogy a készüléket egy virtuális gépen vagy fizikai gépen egy PowerShell telepítő parancsfájlt.
- Ez az oktatóanyag a OVA sablont használja. Tekintse át [ezt a cikket,](deploy-appliance-script.md) ha parancsfájl használatával szeretné beállítani a készüléket.

A készülék létrehozása után ellenőrizze, hogy csatlakozhat-e az Azure Migrate:Server Assessment alkalmazáshoz, konfigurálhatja-e először, és regisztrálja az Azure Migrate projekttel.



### <a name="download-the-ova-template"></a>Az OVA sablon letöltése

1. Az**Azure Migrate: Server Assessment (Áttelepítés: Kiszolgálófelmérés)** **áttelepítési** > **célkiszolgálókon** > válassza **a Discover (Felderítés)** lehetőséget.
1. A **Discover machines** > **Are Your machines virtualizált?**, válassza az **Igen, a VMWare vSphere hipervizor**.
1. A OVA sablonfájl letöltéséhez válassza a **Letöltés** lehetőséget.

   ![OVA fájl letöltésének kiválasztása](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>A biztonság ellenőrzése

A telepítés előtt ellenőrizze, hogy a OVA-fájl biztonságos-e:

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
1. Futtassa a következő parancsot az OVA fájl kivonatának létrehozásához:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

A 2.19.07.30-as verzió esetében a létrehozott kivonatnak meg kell egyeznie ezekkel az értékekkel:

**Algoritmus** | **Kivonat értéke**
--- | ---
MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c

### <a name="create-the-appliance-vm"></a>A készülék virtuális gépének létrehozása

Importálja a letöltött fájlt, és hozzon létre egy virtuális gép:

1. A vSphere ügyfélkonzolon válassza a File Deploy OVF Template **(Fájltelepítés** > **OVF-sablon) lehetőséget.**

   ![Menüparancs OVF-sablon telepítéséhez](./media/tutorial-assess-vmware/deploy-ovf.png)

1. Az OVF sablon telepítése varázslóban > **forrásból,** adja meg a OVA fájl helyét.
1. A **Név** és **a Hely területen**adja meg a virtuális gép rövid nevét. Válassza ki azt a készletobjektumot, amelyben a virtuális gép lesz tárolva.
1. Az **állomás/fürt**ben adja meg azt az állomást vagy fürtöt, amelyen a virtuális gép futni fog.
1. A **Storage alkalmazásban**adja meg a virtuális gép tárolási célját.
1. A **Disk Format** (Lemezformátum) mezőben adja meg a lemez típusát és méretét.
1. A **Hálózati leképezés**alkalmazásban adja meg azt a hálózatot, amelyhez a virtuális gép csatlakozni fog. A hálózatnak internetkapcsolatra van szüksége ahhoz, hogy metaadatokat küldjön az Azure Migrate Server Assessment szolgáltatásba.
1. Tekintse át és erősítse meg a beállításokat, majd válassza a **Befejezés gombot.**

### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-hoz való hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék virtuális gép [e-alapú URL-címekhez.](migrate-appliance.md#url-access)

### <a name="configure-the-appliance"></a>A készülék konfigurálása

Először állítsa be a készüléket.

> [!NOTE]
> Ha a letöltött OVA helyett [Egy PowerShell-parancsfájl](deploy-appliance-script.md) használatával állítja be a készüléket, az eljárás első két lépése nem releváns.

1. A vSphere-ügyfélkonzolon kattintson a jobb gombbal a virtuális gépre, majd válassza **a Konzol megnyitása parancsot.**
1. Adja meg a készülék nyelvét, időzónáját és jelszavát.
1. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozni tud a virtuális géphez, és nyissa meg a készülék webalkalmazásÁNAK URL-címét: **https:// készülék nevét vagy*IP-címét:* 44368**.

   Másik lehetőségként megnyithatja az alkalmazást a készülék asztaláról az alkalmazás parancsikonjának kiválasztásával.
1. A webalkalmazásban > **Az előfeltételek beállítása**beállításhoz tegye a következőket:
   - **Licenc**: Fogadja el a licencfeltételeket, és olvassa el a harmadik fél től származó információkat.
   - **Kapcsolat:** Az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
     - Válassza a **Proxy beállítások lehetőséget,** és adja http://ProxyIPAddress meg http://ProxyFQDNa proxycímet és a figyelőportot az űrlapon vagy a ban.
     - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
     - Ne feledje, hogy csak a HTTP-proxy támogatott.
   - **Időszinkronizálás**: A készüléken töltött időnek szinkronban kell lennie az internetidővel, hogy a felderítés megfelelően működjön.
   - **Frissítések telepítése**: A készülék biztosítja a legújabb frissítések telepítését.
   - **VDDK telepítése**: A készülék ellenőrzi, hogy a VMWare vSphere Virtual Disk Development Kit (VDDK) telepítve van-e. Ha nincs telepítve, töltse le a VDDK 6.7-et a VMware-ből, és bontsa ki a letöltött zip tartalmát a készülék megadott helyére.

     Az Azure Áttelepítési Kiszolgáló áttelepítése a VDDK-t használja a gépek replikálásához az Azure-ba való migrálás során.       

### <a name="register-the-appliance-with-azure-migrate"></a>Regisztrálja a készüléket az Azure Migrate szolgáltatással

1. Válassza **a Bejelentkezés**lehetőséget. Ha nem jelenik meg, győződjön meg arról, hogy letiltotta az előugró ablakok blokkolását a böngészőben.
1. Az új lapon jelentkezzen be az Azure-felhasználónevével és jelszavával.
   
   A PIN-kóddal való bejelentkezés nem támogatott.
1. Miután sikeresen bejelentkezett, lépjen vissza a webalkalmazásba.
1. Válassza ki azt az előfizetést, amelyben az Azure Migrate projekt et létrehozták, majd válassza ki a projektet.
1. Adja meg a készülék nevét. A névnek alfanumerikusnak kell lennie, legkevesebb karakterrel.
1. Kattintson a **Register** (Regisztrálás) elemre.


## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

A készüléknek csatlakoznia kell a vCenter Server szolgáltatáshoz a virtuális gépek konfigurációs és teljesítményadatainak felderítéséhez.

### <a name="specify-vcenter-server-details"></a>A vCenter Server adatainak megadása
1. A **VCenter-kiszolgáló részleteinek megadása**területen adja meg a vCenter Server-példány nevét (FQDN) vagy IP-címét. Meghagyhatja az alapértelmezett portot, vagy megadhat egy egyéni portot, amelyen a vCenter Server figyel.
2. A **Felhasználónév** és **jelszó**csoportban adja meg azokat a vCenter-kiszolgálói fiók hitelesítő adatait, amelyeket a készülék a vCenter-kiszolgáló példányon lévő virtuális gépek felderítéséhez használ. 

    - Létre kellett volna hoznia egy fiókot a szükséges engedélyekkel az [előző oktatóanyagban](tutorial-prepare-vmware.md#set-up-an-account-for-assessment).
    - Ha a felderítést adott VMware-objektumokra (vCenter Server-adatközpontokra, fürtökre, fürtök mappájára, állomásokra, állomások mappájára vagy egyedi virtuális gépekre) szeretné hatókörbe helyezni, tekintse át a [jelen cikkben](set-discovery-scope.md) található utasításokat az Azure Migrate által használt fiók korlátozásához.

3. Válassza **a Kapcsolat ellenőrzése lehetőséget,** hogy a készülék képes-e csatlakozni a vCenter Server kiszolgálóhoz.
4. Az **alkalmazások és a virtuális gépekfüggőségek felderítése**területen szükség szerint kattintson a Hitelesítő adatok **hozzáadása**gombra, és adja meg azt az operációs rendszert, amelyhez a hitelesítő adatok relevánsak, valamint a hitelesítő adatok felhasználónevét és jelszavát. Ezután kattintson **a Hozzáadás**gombra.

    - Ha itt adott meg hitelesítő adatokat, ha létrehozott egy fiókot az [alkalmazásfelderítési szolgáltatáshoz](how-to-discover-applications.md)vagy az [ügynök nélküli függőségelemzési szolgáltatáshoz.](how-to-create-group-machine-dependencies-agentless.md)
    - Ha nem használja ezeket a szolgáltatásokat, kihagyhatja ezt a beállítást.
    - Tekintse át az [alkalmazásfelderítéshez](migrate-support-matrix-vmware.md#application-discovery)vagy az ügynök nélküli elemzéshez szükséges hitelesítő [adatokat.](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)

5. **Mentse és indítsa el**a felderítést, hogy elinduljon a virtuális gép felderítése.

A Discovery a következőképpen működik:
- Körülbelül 15 percet vesz igénybe, amíg a felderített virtuális gép metaadatai megjelennek a portálon.
- A telepített alkalmazások, szerepkörök és szolgáltatások felderítése némi időt vesz igénybe. Az időtartam a felderített virtuális gépek számától függ. 500 virtuális gép esetén körülbelül egy órát vesz igénybe, amíg az alkalmazáskészlet megjelenik az Azure Migrate portalon.

### <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

A felderítés után ellenőrizheti, hogy a virtuális gépek megjelennek-e az Azure Portalon:

1. Nyissa meg az Azure Migrate irányítópultot.
1. Az **Azure Migrate - Servers** > Azure Migrate: Server Assessment (Azure**Migrate: Server Assessment)** alkalmazásban válassza ki a **felderített kiszolgálók**számát megjelenítő ikont.

## <a name="set-up-an-assessment"></a>Értékelés beállítása

Az Azure Migrate Server Assessment használatával kétféle felmérést hozhat létre:

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | Összegyűjtött teljesítményadatokon alapuló értékelések | **Ajánlott virtuális gép mérete:** A PROCESSZOR és a memória kihasználtsági adatai alapján.<br/><br/> **Ajánlott lemeztípus (standard vagy prémium díjas felügyelt lemez)**: Az IOPS és a helyszíni lemezek átviteli hatása alapján.
**A helyszíni** | Helyszíni méretezésen alapuló értékelések | **Ajánlott virtuális gép mérete:** a helyszíni virtuális gép mérete alapján.<br/><br> **Ajánlott lemeztípus:** Az értékeléshez kiválasztott tárolási típusbeállítás alapján.

## <a name="run-an-assessment"></a>Értékelés futtatása

Végezze el az értékelést az alábbiak szerint:

1. Tekintse át az értékelések létrehozásának [ajánlott gyakorlatait.](best-practices-assessment.md)
1. A **Kiszolgálók** lap **Azure Áttelepítés: Kiszolgálófelmérés** csempéjén válassza a **Felértékelés lehetőséget.**

   ![Az Értékelés gomb helye](./media/tutorial-assess-vmware/assess.png)

1. A **Kiszolgálók felmérése**területen adja meg az értékelés nevét.
1. Válassza **az Összes megtekintése**lehetőséget, majd tekintse át az értékelési tulajdonságokat.

   ![Értékelési tulajdonságok](./media/tutorial-assess-vmware/view-all.png)

1. A **Csoport kijelölése vagy létrehozása**csoportban válassza az Új **létrehozása**lehetőséget, és adja meg a csoport nevét. Egy csoport összegyűjti egy vagy több virtuális gépek együtt értékelésre.
1. A **Gépek hozzáadása a csoporthoz**csoportban válassza a csoporthoz hozzáadni kívánt virtuális gépek lehetőséget.
1. Válassza **az Értékelés létrehozása lehetőséget** a csoport létrehozásához és az értékelés futtatásához.

   ![Kiszolgálók felmérése](./media/tutorial-assess-vmware/assessment-create.png)

1. Az értékelés létrehozása után tekintse meg a **Kiszolgálók** > **Azure Áttelepítés: Kiszolgálóértékelési** > **értékelések**című részben.
1. Válassza **az Értékelés exportálása** lehetőséget, ha Excel-fájlként szeretné letölteni.

## <a name="review-an-assessment"></a>Értékelés áttekintése

Az értékelés a következőket írja le:

- **Azure-készenlét:** Függetlenül attól, hogy a virtuális gépek alkalmasak-e az Azure-ba való migráláshoz.
- **Havi költségbecslés:** A virtuális gépek Azure-beli futtatásának becsült havi számítási és tárolási költségei.
- **Havi tárolási költség becslés:** Az áttelepítés utáni lemeztárolás becsült költségei.

Az értékelés megtekintése:

1. Az **áttelepítési célok** > **kiszolgálói között**válassza az Értékelések **az** **Azure Áttelepítés: Kiszolgálói értékelés**lehetőséget.
1. Az **Értékelések alkalmazásban**válasszon ki egy értékelést a megnyitásához.

   ![Az értékelés összefoglalója](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Az Azure-ra való felkészültség áttekintése

1. Az **Azure-ban készenléti**ellenőrizze, hogy a virtuális gépek készen állnak-e az Azure-ba való migrálásra.
1. Tekintse át a virtuális gép állapotát:
    - **Készen áll az Azure-ra:** Akkor használatos, ha az Azure Migrate a virtuális gépek méretének és költségbecslésének a felmérésben ajánlott.
    - **Kész a feltételekkel:** Problémák és javasolt szervizelés megjelenítése.
    - **Nem áll készen az Azure-ra:** Problémák és javasolt szervizelés megjelenítése.
    - **Készenlét ismeretlen:** Akkor használatos, ha az Azure Migrate nem tudja felmérni a készenlétet az adatok rendelkezésre állásával kapcsolatos problémák miatt.

1. Válasszon egy **Azure-készenléti** állapotot. Megtekintheti a virtuális gép készenléti részleteit. A virtuális gépek részleteinek megtekintéséhez is leáshat, beleértve a számítási, tárolási és hálózati beállításokat.

### <a name="review-cost-details"></a>Költségrészletek áttekintése

Az értékelés összefoglalója az Azure-ban futó virtuális gépek becsült számítási és tárolási költségét mutatja be. A költségek összesítve vannak az értékelt csoport összes virtuális gépénél. Részletezheti az adott virtuális gépek költségrészleteinek megtekintéséhez.

> [!NOTE]
> A költségbecslések a gép méretre vonatkozó javaslatain, a lemezeken és annak tulajdonságain alapulnak. Becslések a helyszíni virtuális gépek iaaS virtuális gépekként való futtatásához. Az Azure Áttelepítési kiszolgáló értékelése nem veszi figyelembe a PaaS- vagy SaaS-költségeket.

Az értékelt csoport összesített tárolási költségei különböző típusú tárolólemezekre vannak felosztva. 

### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

Az Azure Áttelepítési kiszolgáló értékelése megbízhatósági minősítést rendel egy teljesítményalapú értékeléshez, 1 csillagtól (legalacsonyabb) 5 csillagig (legmagasabb).

![Megbízhatósági minősítés](./media/tutorial-assess-vmware/confidence-rating.png)

A megbízhatósági besorolás segít megbecsülni az értékelés méretre vonatkozó ajánlásainak megbízhatóságát. A minősítés az értékelés kiszámításához szükséges adatpontok rendelkezésre állásán alapul:

**Az adatpont elérhetősége** | **Megbízhatósági minősítés**
--- | ---
0%–20% | 1 csillag
21%–40% | 2 csillag
41%–60% | 3 csillag
61%–80% | 4 csillag
81%–100% | 5 csillag

[Ismerje meg a megbízhatósági minősítésekkel kapcsolatos gyakorlati tanácsokat.](best-practices-assessment.md#best-practices-for-confidence-ratings)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure Migrate-berendezést állít be. Ön is létrehozott és áttekintett egy értékelést.

Ha meg szeretné tudni, hogyan telepítheti át a VMware virtuális gépeket az Azure-ba az Azure Áttelepítési Kiszolgáló áttelepítése használatával, folytassa a sorozat harmadik oktatóanyagával:

> [!div class="nextstepaction"]
> [VMware virtuális gépek migrálása](./tutorial-migrate-vmware.md)
