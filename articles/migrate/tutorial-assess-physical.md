---
title: Fizikai kiszolgálók felmérése az Azure-ba való Migrálás Azure Migrate Server Assessment használatával
description: Ismerteti, hogyan értékelheti a helyszíni fizikai kiszolgálókat az Azure-ba való áttelepítéshez Azure Migrate Server Assessment használatával.
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 21ffc425ff7d04bbb1bc0c3a550133ae5374b1e9
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950102"
---
# <a name="assess-physical-servers-with-azure-migrateserver-assessment"></a>Fizikai kiszolgálók értékelése a Azure Migratekel: kiszolgáló értékelése

Ez a cikk bemutatja, hogyan értékelheti a helyszíni fizikai kiszolgálókat a Azure Migrate: Server Assessment Tool használatával.

[Azure Migrate](migrate-services-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és külső gyártótól származó független szoftvergyártó (ISV) ajánlatokat tartalmaz.

Ez az oktatóanyag egy sorozat második része, amely bemutatja, hogyan lehet felmérni és áttelepíteni a fizikai kiszolgálókat az Azure-ba. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Azure Migrate projekt beállítása.
> * Állítson be egy Azure Migrate berendezést, amely a fizikai kiszolgálók felmérésére a helyszínen fut.
> * Indítsa el a helyszíni fizikai kiszolgálók folyamatos felderítését. A készülék konfigurációs és teljesítményadatokat küld a felderített kiszolgálókhoz az Azure-ba.
> * Csoportosítsa a felderített kiszolgálókat, és mérje fel a kiszolgálói csoportot.
> * Tekintse át az értékelést.

> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Az oktatóanyagok az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út. Részletes utasításokért tekintse át a útmutató cikkeket.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/), mielőtt hozzákezd.


## <a name="prerequisites"></a>Előfeltételek

- [Fejezze](tutorial-prepare-physical.md) be az első oktatóanyagot ebben a sorozatban. Ha nem, az oktatóanyagban szereplő utasítások nem fognak működni.
- Az első oktatóanyagban az alábbiakat kell elvégeznie:
    - [Azure-engedélyek beállítása](tutorial-prepare-physical.md) Azure Migratehoz.
    - [Fizikai kiszolgálók előkészítése](tutorial-prepare-physical.md#prepare-for-physical-server-assessment) az értékeléshez. Ellenőrizni kell a készülék követelményeit. Emellett rendelkeznie kell egy, a fizikai kiszolgáló felderítéséhez beállított fiókkal is. A szükséges portok elérhetőnek kell lenniük, és tisztában kell lennie az Azure-hoz való hozzáféréshez szükséges URL-címekkel.




## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate projekt beállítása

Állítson be egy új Azure Migrate-projektet a következők szerint.

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés** területen a **Kiszolgálók felderítése, értékelése és migrálása** alatt kattintson a **Kiszolgálók értékelése és migrálása** lehetőségre.

    ![Kiszolgálók felderítése és értékelése](./media/tutorial-assess-physical/assess-migrate.png)

4. Az **Első lépések** területen kattintson az **Eszközök hozzáadása** elemre.
5. A **Projekt migrálása** területen válassza ki az Azure-előfizetését, majd hozzon létre egy erőforráscsoportot, ha még nem rendelkezik eggyel.  
6. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét, valamint azt a földrajzi nevet, amelyben létre kívánja hozni a projektet. Tekintse át a nyilvános és a [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)támogatott földrajzi [területeit](migrate-support-matrix.md#supported-geographies-public-cloud) .

    - A projekt földrajza csak a helyszíni kiszolgálókról összegyűjtött metaadatok tárolására szolgál.
    - Migrálás futtatása során bármilyen célrégiót választhat.

    ![Azure Migrate projekt létrehozása](./media/tutorial-assess-physical/migrate-project.png)


7. Kattintson a **Tovább** gombra.
8. Az **Assessment (kiértékelés) eszközben**válassza a **Azure Migrate: Server Assessment Next (kiszolgáló értékelése**  >  **Next**) elemet.

    ![Azure Migrate projekt létrehozása](./media/tutorial-assess-physical/assessment-tool.png)

9. A **Migrálási eszköz kiválasztása** területen válassza **A migrálási eszköz hozzáadásának mellőzése egyelőre** > **Tovább** lehetőséget.
10. A **felülvizsgálat + eszközök hozzáadása**lapon tekintse át a beállításokat, majd kattintson az **eszközök hozzáadása**elemre.
11. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik. Megnyílik a projekt oldala. Ha nem látja a projektet, az Azure Migrate irányítópult **Kiszolgálók** területéről elérheti.


## <a name="set-up-the-azure-migrate-appliance"></a>A Azure Migrate berendezés beállítása

Azure Migrate: a kiszolgáló értékelése egy könnyűsúlyú készüléket futtat.

- Ez a készülék fizikai kiszolgáló-felderítést végez, és kiszolgálói metaadatokat és teljesítményadatokat küld Azure Migrate kiszolgáló értékeléséhez.
- A készülék beállítása:
    - Töltse le a Azure Migrate telepítő parancsfájlt tartalmazó tömörített fájlt a Azure Portal.
    - Bontsa ki a tömörített fájl tartalmát. Indítsa el a PowerShell-konzolt rendszergazdai jogosultságokkal.
    - Futtassa a PowerShell-szkriptet a berendezés webalkalmazásának elindításához.
    - Konfigurálja a készüléket első alkalommal, és regisztrálja a Azure Migrate projekttel.
- Több berendezést is beállíthat egyetlen Azure Migrate projekthez. Minden készüléken megtalálhatja a fizikai kiszolgálók számát. Eszközönként legfeljebb 1000 kiszolgálót lehet felderíteni.

### <a name="generate-the-azure-migrate-project-key"></a>A Azure Migrate projekt kulcsának előállítása

1. Az **áttelepítési célok**  >  **kiszolgálói**  >  **Azure Migrate: kiszolgáló értékelése**területen válassza a **felderítés**lehetőséget.
2. A **Discover Machines**szolgáltatásban  >  **a gépek virtualizáltak?**, válassza a **fizikai vagy egyéb (AWS, GCP, Xen stb.)** lehetőséget.
3. **1.: Azure Migrate Project-kulcs létrehozásakor**adja meg a fizikai vagy virtuális kiszolgálók felderítéséhez beállítani kívánt Azure Migrate berendezés nevét. A névnek legfeljebb 14 karakterből kell állnia.
1. Kattintson a **kulcs létrehozása** lehetőségre a szükséges Azure-erőforrások létrehozásának elindításához. Az erőforrások létrehozásakor ne zárja be a gépek felderítése lapot.
1. Az Azure-erőforrások sikeres létrehozása után létrejön egy **Azure Migrate projekt kulcsa** .
1. Másolja a kulcsot, mert szüksége lesz rá, hogy elvégezze a berendezés regisztrációját a konfiguráció során.

### <a name="download-the-installer-script"></a>A telepítő parancsfájl letöltése

**2.: töltse le Azure Migrate készüléket**, és kattintson a **Letöltés**gombra.

   ![A felderítési gépek kiválasztása](./media/tutorial-assess-physical/servers-discover.png)


   ![A kulcs létrehozásának kiválasztása](./media/tutorial-assess-physical/generate-key-physical.png)


### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a tömörített fájl biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a tömörített fájl kivonatának létrehozásához:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Példa a nyilvános felhő használatára: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Példa kormányzati felhő használatára: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  Ellenőrizze a készülék legújabb verzióit és a kivonatoló értékeket:
    - Nyilvános felhő esetén:

        **Forgatókönyv** | **Letöltés*** | **Kivonat értéke**
        --- | --- | ---
        Fizikai (85 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140334) | 6de88e229c2b4836d16ce03fdfac93b5c27274945577bd8a359d598cf3777b24

    - Azure Government esetén:

        **Forgatókönyv** | **Letöltés*** | **Kivonat értéke**
        --- | --- | ---
        Fizikai (85 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140338) | 5a4715a9d10e81a5f38192b7d1c6ac0919ae3998afbf2e933c99bafae6bef80e

### <a name="run-the-azure-migrate-installer-script"></a>A Azure Migrate telepítő parancsfájl futtatása

A telepítő parancsfájl a következő műveleteket végzi el:

- Ügynököket és webalkalmazásokat telepít a fizikai kiszolgálók felderítéséhez és értékeléséhez.
- Telepítse a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Töltse le és telepítse az IIS újraírható modulját. [További információ](https://www.microsoft.com/download/details.aspx?id=7435).
- Frissíti a (HKLM) beállításkulcsot a Azure Migrate állandó beállítási részleteivel.
- A következő fájlokat hozza létre az elérési út alatt:
    - **Konfigurációs fájlok**:%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok**:%ProgramData%\Microsoft Azure\Logs

Futtassa a szkriptet a következő módon:

1. Bontsa ki a. zip fájlt egy olyan mappába a kiszolgálón, amely a készüléket fogja üzemeltetni.  Győződjön meg arról, hogy nem futtatja a parancsfájlt egy meglévő Azure Migrate berendezésen lévő gépen.
2. Indítsa el a PowerShellt a fenti kiszolgálón rendszergazdai (emelt szintű) jogosultsággal.
3. Módosítsa a PowerShell könyvtárat arra a mappára, ahol a rendszer kibontotta a tartalmat a letöltött tömörített fájlból.
4. Futtassa a **AzureMigrateInstaller.ps1** nevű szkriptet a következő parancs futtatásával:

    - Nyilvános felhő esetén: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - Azure Government esetén: 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    A szkript a készülék webalkalmazásának sikeres befejeződése után elindítja a készüléket.

Ha bármilyen probléma merül fel, a parancsfájl-naplókat a C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log naplófájlban érheti el a hibaelhárításhoz.

### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék tud csatlakozni az Azure URL-címekhez a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati](migrate-appliance.md#government-cloud-urls) felhőkhöz.


### <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

1. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a berendezéshez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást az asztalról az alkalmazás parancsikonra kattintva.
2. Fogadja el a **licencfeltételeket**, és olvassa el a harmadik féltől származó információkat.
1. A webalkalmazás-> **Előfeltételek beállítása**lapon tegye a következőket:
    - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a kiszolgáló rendelkezik-e internet-hozzáféréssel. Ha a kiszolgáló proxyt használ:
        - Kattintson a **proxy beállítása** elemre, és adja meg a proxy címe (az űrlapon http://ProxyIPAddress vagy a http://ProxyFQDN) figyelési porton.
        - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
        - Csak a HTTP-proxyk használata támogatott.
        - Ha hozzáadta a proxy részleteit, vagy letiltotta a proxyt és/vagy a hitelesítést, kattintson a **Save (Mentés** ) gombra a kapcsolat ismételt elindításához.
    - **Idő szinkronizálása**: az idő ellenőrzése megtörtént. A készüléken a kiszolgáló felderítésének megfelelő működéséhez az idő szinkronizálása szükséges.
    - **Frissítések telepítése**: Azure Migrate Server Assessment ellenőrzi, hogy a készüléken telepítve vannak-e a legújabb frissítések. Az ellenőrzések befejezése után a berendezés **megtekintése** lehetőségre kattintva megtekintheti a készüléken futó összetevők állapotát és verzióit.

### <a name="register-the-appliance-with-azure-migrate"></a>A készülék regisztrálása a Azure Migrate

1. Illessze be a portálról másolt **Azure Migrate Project kulcsot** . Ha nem rendelkezik a kulccsal, lépjen a **kiszolgáló értékelése> felderítés> a meglévő berendezések kezelése**lehetőségre, válassza ki a készüléknek a kulcs létrehozásakor megadott nevét, és másolja a megfelelő kulcsot.
1. Kattintson a **Bejelentkezés**elemre. Egy új böngésző lapon nyit meg egy Azure-beli bejelentkezési kérést. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.
1. Az új lapon jelentkezzen be az Azure-beli felhasználónevével és jelszavával.
   
   A PIN-kóddal való bejelentkezés nem támogatott.
3. A sikeres bejelentkezést követően térjen vissza a webalkalmazáshoz. 
4. Ha a naplózáshoz használt Azure-beli felhasználói fiók rendelkezik a megfelelő [engedélyekkel](tutorial-prepare-physical.md) a kulcs létrehozása során létrehozott Azure-erőforrásokhoz, a készülék regisztrációja kezdeményezve lesz.
1. A készülék sikeres regisztrálása után a **részletek megtekintése**lehetőségre kattintva megtekintheti a regisztráció részleteit.


## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

Most kapcsolódjon a készülékről a felderíteni kívánt fizikai kiszolgálókhoz, és indítsa el a felderítést.

1. Az **1. lépés: hitelesítő adatok megadása a Windows-és Linux-alapú fizikai vagy virtuális kiszolgálók felderítéséhez**kattintson a **hitelesítő adatok hozzáadása** lehetőségre a hitelesítő adatok rövid nevének megadásához, adja hozzá a **felhasználónevet** és a **jelszót** egy Windows-vagy Linux-kiszolgálóhoz. Kattintson a **Save (Mentés**) gombra.
1. Ha egyszerre több hitelesítő adatot szeretne felvenni, kattintson a **továbbiak hozzáadása** elemre, és adjon hozzá további hitelesítő adatokat. A fizikai kiszolgálók felderítéséhez több hitelesítő adat is támogatott.
1. A **2. lépés: fizikai vagy virtuális kiszolgáló adatainak**megadása elemnél kattintson a **felderítési forrás hozzáadása** lehetőségre a kiszolgáló **IP-címének/teljes tartománynevének** és a kiszolgálóhoz való kapcsolódáshoz szükséges hitelesítő adatok rövid nevének megadásához.
1. Egyszerre **egyetlen elemet is hozzáadhat** , vagy egy menetben **több elemet is hozzáadhat** . Lehetőség van arra is, hogy a kiszolgáló adatait a **CSV importálásával**adja meg.

    ![A felderítési forrás hozzáadásának kijelölése](./media/tutorial-assess-physical/add-discovery-source-physical.png)

    - Ha az **egyetlen elem hozzáadása**lehetőséget választja, kiválaszthatja az operációs rendszer típusát, megadhatja a hitelesítő adatok rövid nevét, a kiszolgáló **IP-címét vagy teljes tartománynevét** , majd kattintson a **Mentés**gombra.
    - Ha úgy dönt, hogy **több elemet ad hozzá**, egyszerre több rekordot is hozzáadhat, ha a szövegmezőben a hitelesítő adatok rövid nevét adja meg a kiszolgáló **IP-címe/teljes tartományneve** mezőben. **Ellenőrizze** a hozzáadott rekordokat, és kattintson a **Save (Mentés**) gombra.
    - Ha a **CSV importálása** _(alapértelmezés szerint kiválasztva)_ lehetőséget választja, letöltheti a CSV-sablonfájl fájlját, feltöltheti a fájlt a kiszolgáló **IP-címével/teljes tartománynevével** , valamint a hitelesítő adatok rövid nevét. Ezután importálja a fájlt a készülékbe, **ellenőrizze** a fájlban szereplő rekordokat, és kattintson a **Mentés**gombra.

1. A Save (Mentés) gombra kattintva a készülék megpróbálhatja érvényesíteni a hozzáadott kiszolgálókhoz való kapcsolódást, és megjeleníti az **ellenőrzési állapotot** a táblában az egyes kiszolgálókon.
    - Ha egy kiszolgáló érvényesítése meghiúsul, tekintse át a hibát, ha a tábla állapot oszlopában a **sikertelen érvényesítés** gombra kattint. Javítsa ki a problémát, és ismételje meg az érvényesítést.
    - Kiszolgáló eltávolításához kattintson a **Törlés**gombra.
1. A felderítés megkezdése előtt bármikor **újraérvényesítheti** a kiszolgálókkal való kapcsolatot.
1. Kattintson a **felderítés indítása**gombra a sikeresen érvényesített kiszolgálók felderítésének elindításához. A felderítés sikeres elindítása után megtekintheti a felderítési állapotot a tábla minden egyes kiszolgálóján.


Ez elindítja a felderítést. Kiszolgálónként körülbelül 2 percet vesz igénybe, hogy a felderített kiszolgáló metaadatai megjelenjenek a Azure Portalban.

### <a name="verify-servers-in-the-portal"></a>Kiszolgálók ellenőrzése a portálon

A felderítést követően ellenőrizheti, hogy a kiszolgálók megjelennek-e a Azure Portal.

1. Nyissa meg a Azure Migrate irányítópultot.
2. A **Azure Migrate-Servers**  >  **Azure Migrate: kiszolgáló értékelése** lapon kattintson arra az ikonra, amely megjeleníti a **felderített kiszolgálók**darabszámát.

## <a name="set-up-an-assessment"></a>Értékelés beállítása

A Azure Migrate: Server Assessment használatával kétféle értékelést hozhat létre.

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítmény-alapú** | Értékelések az összegyűjtött teljesítményadatok alapján | **Ajánlott**virtuálisgép-méret: a processzor-és memóriahasználat adatai alapján.<br/><br/> **Ajánlott lemez típusa (standard vagy prémium szintű felügyelt lemez)**: a helyszíni lemezek IOPS és átviteli sebessége alapján.
**Helyszíni** | Helyszíni méretezésen alapuló értékelések. | **Ajánlott**virtuálisgép-méret: a helyszíni kiszolgáló méretétől függően<br/><br> **Ajánlott lemez típusa**: az értékeléshez kiválasztott tárolási típus alapján.


### <a name="run-an-assessment"></a>Értékelés futtatása

Az értékelést a következőképpen futtathatja:

1. Tekintse át az értékelések létrehozásával kapcsolatos [ajánlott eljárásokat](best-practices-assessment.md) .
2. A **kiszolgálók** lap **Azure Migrate: kiszolgáló értékelése** csempén kattintson az **értékelés**elemre.

    ![Kiértékelés](./media/tutorial-assess-physical/assess.png)

2. A **kiszolgálók értékelése**lapon adja meg az értékelés nevét.
3. Kattintson az **Összes megtekintése** elemre az értékelési tulajdonságok áttekintéséhez.

    ![Értékelés tulajdonságai](./media/tutorial-assess-physical/view-all.png)

3. A **válasszon ki vagy hozzon létre egy csoportot**, válassza az **új létrehozása**lehetőséget, és adjon meg egy csoportnevet. Egy csoport egy vagy több kiszolgálót gyűjt össze az értékeléshez.
4. A **számítógépek hozzáadása a csoporthoz**területen válassza ki a csoportba felvenni kívánt kiszolgálókat.
5. A csoport létrehozásához kattintson az **Értékelés létrehozása** elemre, majd futtassa az értékelést.

    ![Értékelés létrehozása](./media/tutorial-assess-physical/assessment-create.png)

6. Az értékelés létrehozása után tekintse meg a **kiszolgálók**  >  **Azure Migrate: kiszolgáló-értékelési**  >  **értékelések**.
7. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.



## <a name="review-an-assessment"></a>Értékelés áttekintése

Az értékelés a következőket írja le:

- **Azure-készültség**: az, hogy a kiszolgálók alkalmasak-e az Azure-ba való áttelepítésre.
- **Havi költségbecslés**: a kiszolgálók Azure-ban való futtatásának becsült havi számítási és tárolási költségei.
- **Havi tárolási költségek becslése**: a lemezes tárolás becsült költségei az áttelepítés után.

### <a name="view-an-assessment"></a>Értékelés megtekintése

1. Az **áttelepítési célok**  >   **kiszolgálóin**kattintson az **értékelések** **Azure Migrate: kiszolgáló értékelése**elemre.
2. Az **értékelésekben**kattintson egy értékelésre a megnyitásához.

    ![Értékelés összegzése](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Az Azure készültségének áttekintése

1. Az **Azure készültségi**területén ellenőrizze, hogy a kiszolgálók készen állnak-e az Azure-ba való áttelepítésre.
2. Tekintse át az állapotot:
    - **Készen áll az Azure-ra**: Azure Migrate javasolja a virtuális gépek méretének és becsült értékének becslését az értékelés során.
    - **Feltételekkel kész**: megjeleníti a problémákat és a javasolt szervizelést.
    - **Nem áll készen az Azure-ra: a**problémákat és a javasolt szervizelést jeleníti meg.
    - **Felkészültségi ismeretlen**: akkor használatos, ha az adatelérhetőségi problémák miatt Azure Migrate nem tudja felmérni a készültséget.

2. Kattintson egy **Azure-készültségi** állapotra. Megtekintheti a kiszolgáló készültségi adatait, és részletesen megtekintheti a kiszolgáló adatait, beleértve a számítási, tárolási és hálózati beállításokat.



### <a name="review-cost-details"></a>A Cost részleteinek áttekintése

Ez a nézet az Azure-ban futó virtuális gépek becsült számítási és tárolási költségeit jeleníti meg.

1. Tekintse át a havi számítási és tárolási költségeket. A költségek összesítése az összes kiszolgáló számára a vizsgált csoportban történik.

    - A költségbecslés a gép méretére vonatkozó javaslatokon, valamint a lemezek és a tulajdonságok alapján történik.
    - A számítási és tárolási költségek becsült havi költségei láthatók.
    - A költségbecslés a helyszíni kiszolgálók IaaS virtuális gépekként való futtatására szolgál. Azure Migrate Server Assessment nem veszi figyelembe a Pásti vagy az SaaS költségeit.

2. Megtekintheti a havi tárolási díjak becsült összegét. Ez a nézet az értékelt csoport összesített tárolási költségeit jeleníti meg, a különböző típusú tárolóeszközök felosztásával.
3. Megtekintheti az egyes kiszolgálók részleteit.


### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

Ha teljesítmény-alapú értékeléseket futtat, a rendszer megbízhatósági minősítést rendel az értékeléshez.

![Megbízhatósági minősítés](./media/tutorial-assess-physical/confidence-rating.png)

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

Folytassa a sorozat harmadik oktatóanyagával, amelyből megtudhatja, hogyan telepíthet át fizikai kiszolgálókat az Azure-ba a Azure Migrate: Server Migration használatával.

> [!div class="nextstepaction"]
> [Fizikai kiszolgálók migrálása](./tutorial-migrate-physical-virtual-machines.md)
