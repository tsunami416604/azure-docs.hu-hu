---
title: A Hyper-V virtuális gépek felmérése az Azure-ba való Migrálás Azure Migrate használatával | Microsoft Docs
description: Ismerteti, hogyan lehet kiértékelni a helyszíni Hyper-V virtuális gépeket az Azure-ba való Migrálás Azure Migrate használatával.
ms.topic: tutorial
ms.date: 01/01/2020
ms.custom: mvc
ms.openlocfilehash: f2a7caad13ad845d5b2aeb3240b7d77fa89faf12
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720259"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>A Hyper-V virtuális gépek felmérése Azure Migrate kiszolgáló értékelésével

Ez a cikk bemutatja, hogyan értékelheti a helyszíni Hyper-V virtuális gépeket a Azure Migrate: Server Assessment Tool használatával.

[Azure Migrate](migrate-services-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és külső gyártótól származó független szoftvergyártó (ISV) ajánlatokat tartalmaz.



Ez az oktatóanyag egy sorozat második része, amely bemutatja, hogyan lehet felmérni és áttelepíteni a Hyper-V virtuális gépeket az Azure-ba. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Migrate projekt beállítása.
> * Azure Migrate berendezés beállítása és regisztrálása.
> * Indítsa el a helyszíni virtuális gépek folyamatos felderítését.
> * Csoportosítsa a felderített virtuális gépeket, és mérje fel a csoportot.
> * Tekintse át az értékelést.

> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Az oktatóanyagok az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út. Részletes utasításokért tekintse át a útmutató cikkeket.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prerequisites"></a>Előfeltételek

- [Fejezze](tutorial-prepare-hyper-v.md) be az első oktatóanyagot ebben a sorozatban. Ha nem, az oktatóanyagban szereplő utasítások nem fognak működni.
- Az első oktatóanyagban az alábbiakat kell elvégeznie:
    - [Azure-engedélyek beállítása](tutorial-prepare-hyper-v.md#prepare-azure) Azure Migratehoz.
    - [Hyper-V](tutorial-prepare-hyper-v.md#prepare-hyper-v-for-assessment) fürtök, gazdagépek és virtuális gépek előkészítése az értékeléshez.
    - Felkészülés a Hyper-V virtuális gépek felderítéséhez és értékeléséhez használt Azure Migrate berendezés [üzembe helyezésére](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment) .

## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate projekt beállítása

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A keresési eredmények között válassza a **Azure Migrate**lehetőséget.
3. Az **Áttekintés** területen a **Kiszolgálók felderítése, értékelése és migrálása** alatt kattintson a **Kiszolgálók értékelése és migrálása** lehetőségre.

    ![Kiszolgálók felderítése és értékelése](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. Az **Első lépések** területen kattintson az **Eszközök hozzáadása** elemre.
5. A **projekt Átmigrálása** lapon válassza ki az Azure-előfizetését, és hozzon létre egy erőforráscsoportot, ha még nem rendelkezik ilyennel.
6. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét, valamint azt a régiót, amelyben létre kívánja hozni a projektet.


    ![Azure Migrate projekt létrehozása](./media/tutorial-assess-hyper-v/migrate-project.png)

    Ezekben a régiókban létrehozhat egy Azure Migrate projektet is.

    **Régiócsoport** | **Régió**
    --- | ---
    Ázsia  | Délkelet-Ázsia
    Európa | Észak-Európa vagy Nyugat-Európa
    Egyesült Királyság |  Egyesült Királyság déli régiója vagy Egyesült Királyság nyugati régiója
    Egyesült Államok | USA keleti régiója, USA 2. nyugati régiója vagy az USA nyugati középső régiója

    - A projekt régió csak a helyszíni virtuális gépekről összegyűjtött metaadatok tárolására szolgál.
    - A virtuális gépek áttelepítésekor egy másik Azure Target-régiót is kijelölhet. Az összes Azure-régió áttelepítési célként támogatott.

7. Kattintson a **Tovább** gombra.
8. Az **Assessment (kiértékelés) eszközben**válassza a **Azure Migrate: Server Assessment** > **Next (tovább**) lehetőséget.

    ![Azure Migrate projekt létrehozása](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. A **Migrálási eszköz kiválasztása** területen válassza **A migrálási eszköz hozzáadásának mellőzése egyelőre** > **Tovább** lehetőséget.
10. Az **Áttekintés + eszközök hozzáadása** területen ellenőrizze a beállításokat, majd kattintson az **Eszközök hozzáadása** lehetőségre.
11. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik. Megnyílik a projekt oldala. Ha nem látja a projektet, az Azure Migrate irányítópult **Kiszolgálók** területéről elérheti.




## <a name="set-up-the-appliance-vm"></a>A berendezés virtuális gép beállítása

Azure Migrate Server Assessment egy egyszerűsített Hyper-V VM-készüléket futtat.

- Ez a készülék a virtuális gépek felderítését végzi, és a virtuális gépek metaadatait és teljesítményadatait a kiszolgáló értékelésének Azure Migrate.
- A készülék beállítása:
    - Töltsön le egy tömörített Hyper-V virtuális merevlemezt a Azure Portal.
    - Hozza létre a készüléket, és győződjön meg róla, hogy tud kapcsolódni Azure Migrate Server Assessmenthez.
    - Konfigurálja a készüléket első alkalommal, és regisztrálja a Azure Migrate projekttel.

### <a name="download-the-vhd"></a>A VHD letöltése

Töltse le a készülék tömörített VHD-sablonját.

1. Az **áttelepítési célok** > **kiszolgálók** > **Azure Migrate: kiszolgáló értékelése**területen kattintson a **felderítés**gombra.
2. A számítógépek **felderítése** > a **gépek virtualizáltak?** , kattintson **az igen, a Hyper-V**elemre.
3. A VHD-fájl letöltéséhez kattintson a **Letöltés** gombra.

    ![Virtuális gép letöltése](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a tömörített fájl biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.

2. Futtassa a következő PowerShell-parancsot a ZIP-fájl kivonatának létrehozásához
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  A készülék verziójának 2.19.07.30 a generált kivonatnak meg kell egyeznie ezekkel a beállításokkal.

  **Algoritmus** | **Kivonat értéke**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31

### <a name="create-the-appliance-vm"></a>A berendezés virtuális gép létrehozása

Importálja a letöltött fájlt, és hozza létre a virtuális gépet.

1. Miután letöltötte a tömörített VHD-fájlt arra a Hyper-V-gazdagépre, amelyre a készülék virtuális gépe kerül, bontsa ki a tömörített fájlt.
    - A kinyert helyen a fájl kibontja **AzureMigrateAppliance_VersionNumber**nevű mappába.
    - Ez a mappa almappát tartalmaz, más néven **AzureMigrateAppliance_VersionNumber**.
    - Ez az almappa három további almappát tartalmaz: **Pillanatképek**, **virtuális merevlemezek**és **Virtual Machines**.

2. Nyissa meg a Hyper-V kezelőjét. A **műveletek**területen kattintson a **virtuális gép importálása**elemre.

    ![VHD üzembe helyezése](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. A virtuális gép importálása varázslóban > a **Kezdés előtt**kattintson a **tovább**gombra.
3. A **mappa keresése**területen válassza a **Virtual Machines** mappát. Ezután kattintson a **Next** (Tovább) gombra.
1. A **virtuális gép kiválasztása lapon**kattintson a **tovább**gombra.
2. Az **importálási típus kiválasztása**területen kattintson **a virtuális gép másolása (új egyedi azonosító létrehozása)** elemre. Ezután kattintson a **Next** (Tovább) gombra.
3. A **cél kiválasztása**területen hagyja meg az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
4. A **tárolási mappák**területen hagyja meg az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
5. A **hálózat kiválasztása**területen adja meg azt a virtuális kapcsolót, amelyet a virtuális gép használni fog. A kapcsolónak internetkapcsolattal kell rendelkeznie az Azure-ba való adatküldéshez.
6. Az **Összefoglalás**területen tekintse át a beállításokat. Ezután kattintson a **Befejezés** gombra.
7. A Hyper-V kezelőjében > **Virtual Machines**indítsa el a virtuális gépet.


### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék virtuális gépe tud csatlakozni az [Azure URL-címekhez](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

### <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

1. A Hyper-V kezelőjében > **Virtual Machines**kattintson a jobb gombbal a virtuális gépre > a **kapcsolat**elemre.
2. Adja meg a berendezés nyelvét, időzónáját és jelszavát.
3. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a virtuális géphez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást a készülék asztaláról az alkalmazás parancsikonra kattintva.
1. A webalkalmazás-> **Előfeltételek beállítása**lapon tegye a következőket:
    - **Licenc**: fogadja el a licencfeltételeket, és olvassa el a harmadik féltől származó információkat.
    - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
      - Kattintson a **Proxybeállítások**elemre, és határozza meg a proxy címe és a figyelő portját http://ProxyIPAddress vagy http://ProxyFQDN formában.
      - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
      - Csak a HTTP-proxyk használata támogatott.
    - **Idő szinkronizálása**: az idő ellenőrzése megtörtént. A készüléken az idő a virtuális gép felderítésének megfelelő működéséhez szinkronban kell lennie.
    - **Frissítések telepítése**: Azure Migrate Server Assessment ellenőrzi, hogy a készüléken telepítve vannak-e a legújabb frissítések.

### <a name="register-the-appliance-with-azure-migrate"></a>A készülék regisztrálása a Azure Migrate

1. Kattintson **a bejelentkezés**elemre. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.
2. Az új lapon jelentkezzen be az Azure-beli hitelesítő adataival.
    - Jelentkezzen be a felhasználónevével és jelszavával.
    - A PIN-kóddal való bejelentkezés nem támogatott.
3. A sikeres bejelentkezés után térjen vissza a webalkalmazáshoz.
4. Válassza ki azt az előfizetést, amelyben a Azure Migrate projektet létrehozták. Ezután válassza ki a projektet.
5. Adja meg a berendezés nevét. A névnek legalább 14 karakterből kell állnia.
6. Kattintson a **regisztrálás**gombra.


### <a name="delegate-credentials-for-smb-vhds"></a>Az SMB virtuális merevlemezek hitelesítő adatainak delegálása

Ha virtuális merevlemezeket futtat az SMB-n, engedélyeznie kell a hitelesítő adatok delegálását a készülékről a Hyper-V gazdagépekre. Ehhez a következőkre van szükség:

- Engedélyezheti, hogy mindegyik gazdagép meghatalmazottként működjön a berendezésben. Ha követte az oktatóanyagokat, ezt az előző oktatóanyagban végezte el, amikor felkészítette a Hyper-V-t az értékeléshez és az áttelepítéshez. [Manuálisan](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts)kell beállítania a CredSSP a gazdagépekhez, vagy [egy olyan parancsfájl futtatásával](tutorial-prepare-hyper-v.md#prepare-with-a-script) , amely ezt teszi.
- Engedélyezze az CredSSP delegálást, hogy az Azure Migrate készülék ügyfélként működhet, hitelesítő adatokat delegáljon egy gazdagépre.

A következő módon engedélyezheti a készüléket:

#### <a name="option-1"></a>1\. lehetőség

Futtassa ezt a parancsot a készülék virtuális gépén. A HyperVHost1/HyperVHost2 például állomásnevek.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
```

Például: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>2\. lehetőség

Ezt is megteheti a berendezés Helyicsoportházirend-szerkesztőján:

1. A **helyi számítógép-házirend** > **Számítógép konfigurációja**területen kattintson **Felügyeleti sablonok** > **rendszer** > **hitelesítő adatok delegálása**elemre.
2. Kattintson duplán a **új hitelesítő adatok delegálásának engedélyezése**lehetőségre, és válassza az **engedélyezve**lehetőséget.
3. A **Beállítások**területen kattintson a **Megjelenítés**elemre, és adja hozzá a listában felderíteni kívánt Hyper-V-gazdagépeket a **wsman/** előtagként.
4. Ezután a **hitelesítő adatok delegálása**lehetőségre duplán kattintva **engedélyezze a friss hitelesítő adatok delegálását csak NTLM kiszolgálói hitelesítéssel**. Ismét adja hozzá a **wsman/** előtagként használni kívánt Hyper-V-gazdagépeket a listához.

## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

Kapcsolódjon a készülékről a Hyper-V-gazdagépekhez vagy-fürtökhöz, és indítsa el a virtuális gépek felderítését.

1. A **Felhasználónév** és a **jelszó**mezőben adja meg a fiók hitelesítő adatait, amelyet a berendezés a virtuális gépek felderítéséhez használ majd. Adja meg a hitelesítő adatok rövid nevét, majd kattintson a **részletek mentése**gombra.
2. Kattintson a **gazdagép hozzáadása**lehetőségre, és adja meg a Hyper-V-gazdagép/-fürt részleteit.
3. Kattintson az **Érvényesítés**elemre. Az ellenőrzés után az egyes gazdagépeken vagy fürtökön felderíthető virtuális gépek száma látható.
    - Ha az érvényesítés sikertelen egy gazdagép esetében, tekintse át a hibát az **állapot** oszlopban látható ikon fölé helyezve. Javítsa ki a hibákat, és ismételje meg az érvényesítést.
    - Gazdagépek vagy fürtök eltávolításához válassza a > **Törlés**lehetőséget.
    - Egy adott gazdagép nem távolítható el fürtből. Csak a teljes fürtöt távolíthatja el.
    - Hozzáadhat egy fürtöt, még akkor is, ha a fürt adott gazdagépével problémák léptek fel.
4. Az ellenőrzés után kattintson a Mentés gombra, **és indítsa** el a felderítést a felderítési folyamat elindításához.

Ez elindítja a felderítést. Gazdagépen körülbelül 1,5 percet vesz igénybe, hogy a felderített kiszolgálók metaadatai megjelenjenek a Azure Portalban.

### <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

A felderítés befejezését követően ellenőrizheti, hogy a virtuális gépek megjelennek-e a portálon.

1. Nyissa meg a Azure Migrate irányítópultot.
2. A **Azure Migrate-** servers > **Azure Migrate: kiszolgáló értékelése** lapon kattintson arra az ikonra, amely megjeleníti a **felderített kiszolgálók**darabszámát.

## <a name="set-up-an-assessment"></a>Értékelés beállítása

Az értékeléseknek két típusa lehet a Azure Migrate Server Assessment használatával.

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítmény-alapú** | Értékelések az összegyűjtött teljesítményadatok alapján | **Ajánlott**virtuálisgép-méret: a processzor-és memóriahasználat adatai alapján.<br/><br/> **Ajánlott lemez típusa (standard vagy prémium szintű felügyelt lemez)** : a helyszíni lemezek IOPS és átviteli sebessége alapján.
**Helyszíni** | Helyszíni méretezésen alapuló értékelések. | **Ajánlott**virtuálisgép-méret: a helyszíni virtuális gép méretétől függően<br/><br> **Ajánlott lemez típusa**: az értékeléshez kiválasztott tárolási típus alapján.



### <a name="run-an-assessment"></a>Értékelés futtatása

Az értékelést a következőképpen futtathatja:

1. Tekintse át az értékelések létrehozásával kapcsolatos [ajánlott eljárásokat](best-practices-assessment.md) .
2. A **kiszolgálók** > **Azure Migrate: kiszolgáló értékelése**területen kattintson az **értékelés**elemre.

    ![Értékelés](./media/tutorial-assess-hyper-v/assess.png)

3. A **kiszolgálók értékelése**lapon adja meg az értékelés nevét.
4. Kattintson az **Összes megtekintése** elemre az értékelési tulajdonságok áttekintéséhez.

    ![Értékelés tulajdonságai](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. A **csoport kijelölése vagy létrehozása**területen válassza az **új létrehozása** elemet, és adja meg a csoportnév nevet. Egy csoport egy vagy több virtuális gépet gyűjt össze az értékeléshez.
4. A **számítógépek hozzáadása a csoporthoz**területen válassza ki a csoportba felvenni kívánt virtuális gépeket.
5. A csoport létrehozásához kattintson az **Értékelés létrehozása** elemre, majd futtassa az értékelést.

    ![Értékelés létrehozása](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Az értékelés létrehozása után megtekintheti azt a **kiszolgálók** > **Azure Migrate: Server Assessment**.
7. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.


## <a name="review-an-assessment"></a>Értékelés áttekintése

Az értékelés a következőket írja le:

- **Azure-készültség**: azt, hogy a virtuális gépek alkalmasak-e az Azure-ba való áttelepítésre.
- **Havi költségbecslés**: a virtuális gépek Azure-ban való futtatásának becsült havi számítási és tárolási költségei.
- **Havi tárolási költségek becslése**: a lemezes tárolás becsült költségei az áttelepítés után.


### <a name="view-an-assessment"></a>Értékelés megtekintése

1. Az **áttelepítési célok** >  **kiszolgálók** > **Azure Migrate: kiszolgáló értékelése**területen kattintson az **értékelések**elemre.
2. Az **értékelésekben**kattintson egy értékelésre a megnyitásához.

    ![Értékelés összegzése](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Az Azure készültségének áttekintése

1. Az **Azure készültségi**területén ellenőrizze, hogy a virtuális gépek készen állnak-e az Azure-ba való áttelepítésre.
2. A virtuális gép állapotának áttekintése:
    - **Készen áll az Azure-ra**: Azure Migrate javasolja a virtuális gépek méretének és becsült értékének becslését az értékelés során.
    - **Feltételekkel kész**: megjeleníti a problémákat és a javasolt szervizelést.
    - **Nem áll készen az Azure-ra: a**problémákat és a javasolt szervizelést jeleníti meg.
    - **Felkészültségi ismeretlen**: akkor használatos, ha az adatelérhetőségi problémák miatt Azure Migrate nem tudja felmérni a készültséget.

2. Kattintson egy **Azure-készültségi** állapotra. Megtekintheti a VM-készültség részleteit, és részletesen megtekintheti a virtuális gép részleteit, beleértve a számítási, tárolási és hálózati beállításokat.

### <a name="review-cost-details"></a>A Cost részleteinek áttekintése

Ez a nézet az Azure-ban futó virtuális gépek becsült számítási és tárolási költségeit jeleníti meg.

1. Tekintse át a havi számítási és tárolási költségeket. A költségek összesítése az összes virtuális gép számára történik a vizsgált csoportban.

    - A költségbecslés a gép méretére vonatkozó javaslatokon, valamint a lemezek és a tulajdonságok alapján történik.
    - A számítási és tárolási költségek becsült havi költségei láthatók.
    - A költségbecslés a helyszíni virtuális gépek IaaS virtuális gépekként való futtatására szolgál. Azure Migrate Server Assessment nem veszi figyelembe a Pásti vagy az SaaS költségeit.

2. Megtekintheti a havi tárolási díjak becsült összegét. Ez a nézet az értékelt csoport összesített tárolási költségeit jeleníti meg, a különböző típusú tárolóeszközök felosztásával.
3. A részletezéssel megtekintheti az adott virtuális gépek részleteit.


### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

Ha teljesítmény-alapú értékeléseket futtat, a rendszer megbízhatósági minősítést rendel az értékeléshez.

![Megbízhatósági minősítés](./media/tutorial-assess-hyper-v/confidence-rating.png)

- A rendszer kinyeri az 1 csillagos (legalacsonyabb) és az 5 csillagos (legmagasabb) minősítést.
- A megbízhatósági minősítés segít megbecsülni az értékelés által biztosított méretre vonatkozó ajánlások megbízhatóságát.
- A megbízhatósági minősítés az értékelés kiszámításához szükséges adatpontok rendelkezésre állásán alapul.

Az értékelés megbízhatósági minősítése a következő.

**Adatpont rendelkezésre állása** | **Megbízhatósági minősítés**
--- | ---
0%–20% | 1 csillag
21%–40% | 2 csillag
41%–60% | 3 csillag
61%–80% | 4 csillag
81%–100% | 5 csillag

[További](best-practices-assessment.md#best-practices-for-confidence-ratings) információ a megbízhatósági minősítéssel kapcsolatos ajánlott eljárásokról.





## <a name="next-steps"></a>Következő lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Azure Migrate berendezés beállítása
> * Értékelés létrehozva és felülvizsgálva

Folytassa a sorozat harmadik oktatóanyagával, amelyből megtudhatja, hogyan telepítheti át a Hyper-V virtuális gépeket az Azure-ba Azure Migrate Server áttelepítéssel.

> [!div class="nextstepaction"]
> [Hyper-V virtuális gépek áttelepítése](./tutorial-migrate-hyper-v.md)
