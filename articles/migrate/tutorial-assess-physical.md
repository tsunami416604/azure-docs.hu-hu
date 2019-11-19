---
title: Fizikai kiszolgálók felmérése az Azure-ba való Migrálás Azure Migrate Server Assessment használatával
description: Ismerteti, hogyan értékelheti a helyszíni fizikai kiszolgálókat az Azure-ba való áttelepítéshez Azure Migrate Server Assessment használatával.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 7f840a99ab76548c16389955cf49791036cc63a2
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158362"
---
# <a name="assess-physical-servers-with-azure-migrate-server-assessment"></a>Fizikai kiszolgálók értékelése a Azure Migratekel: kiszolgáló értékelése

Ez a cikk bemutatja, hogyan értékelheti a helyszíni fizikai kiszolgálókat a Azure Migrate: Server Assessment Tool használatával.

[Azure Migrate](migrate-services-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és külső gyártótól származó független szoftvergyártó (ISV) ajánlatokat tartalmaz.

Ez az oktatóanyag egy sorozat második része, amely bemutatja, hogyan lehet felmérni és áttelepíteni a fizikai kiszolgálókat az Azure-ba. Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> * Azure Migrate projekt beállítása.
> * Állítson be egy Azure Migrate berendezést, amely a fizikai kiszolgálók felmérésére a helyszínen fut.
> * Indítsa el a helyszíni fizikai kiszolgálók folyamatos felderítését. A készülék konfigurációs és teljesítményadatokat küld a felderített kiszolgálókhoz az Azure-ba.
> * Csoportosítsa a felderített kiszolgálókat, és mérje fel a kiszolgálói csoportot.
> * Tekintse át az értékelést.

> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Az oktatóanyagok az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út. Részletes utasításokért tekintse át a útmutató cikkeket.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prerequisites"></a>Előfeltételek

- [Fejezze](tutorial-prepare-physical.md) be az első oktatóanyagot ebben a sorozatban. Ha nem, az oktatóanyagban szereplő utasítások nem fognak működni.
- Az első oktatóanyagban az alábbiakat kell elvégeznie:
    - [Azure-engedélyek beállítása](tutorial-prepare-physical.md#prepare-azure) Azure Migratehoz.
    - [Fizikai kiszolgálók előkészítése](tutorial-prepare-physical.md#prepare-for-physical-server-assessment) az értékeléshez. Ellenőrizni kell a készülék követelményeit. Emellett rendelkeznie kell egy, a fizikai kiszolgáló felderítéséhez beállított fiókkal is. A szükséges portok elérhetőnek kell lenniük, és tisztában kell lennie az Azure-hoz való hozzáféréshez szükséges URL-címekkel.


## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate projekt beállítása

Állítson be egy új Azure Migrate-projektet a következők szerint.

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés** területen a **Kiszolgálók felderítése, értékelése és migrálása** alatt kattintson a **Kiszolgálók értékelése és migrálása** lehetőségre.

    ![Kiszolgálók felderítése és értékelése](./media/tutorial-assess-physical/assess-migrate.png)

4. Az **Első lépések** területen kattintson az **Eszközök hozzáadása** elemre.
5. A **Projekt migrálása** területen válassza ki az Azure-előfizetését, majd hozzon létre egy erőforráscsoportot, ha még nem rendelkezik eggyel.     
6. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét, valamint azt a földrajzi nevet, amelyben létre kívánja hozni a projektet. Ázsia, Európa, Egyesült Királyság és a Egyesült Államok támogatottak.

    - A projekt földrajza csak a helyszíni kiszolgálókról összegyűjtött metaadatok tárolására szolgál.
    - Migrálás futtatása során bármilyen célrégiót választhat.

    ![Azure Migrate projekt létrehozása](./media/tutorial-assess-physical/migrate-project.png)


7. Kattintson a **Tovább**gombra.
8. Az **Assessment (kiértékelés) eszközben**válassza a **Azure Migrate: Server Assessment** > **Next (tovább**) lehetőséget.

    ![Azure Migrate projekt létrehozása](./media/tutorial-assess-physical/assessment-tool.png)

9. A **Migrálási eszköz kiválasztása** területen válassza **A migrálási eszköz hozzáadásának mellőzése egyelőre** > **Tovább** lehetőséget.
10. Az **Áttekintés + eszközök hozzáadása** területen ellenőrizze a beállításokat, majd kattintson az **Eszközök hozzáadása** lehetőségre.
11. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik. Megnyílik a projekt oldala. Ha nem látja a projektet, az Azure Migrate irányítópult **Kiszolgálók** területéről elérheti.


## <a name="set-up-the-appliance"></a>A készülék beállítása

Azure Migrate: a kiszolgáló értékelése egy könnyűsúlyú készüléket futtat.

- Ez a készülék fizikai kiszolgáló-felderítést végez, és kiszolgálói metaadatokat és teljesítményadatokat küld Azure Migrate kiszolgáló értékeléséhez.
- A készülék beállítása:
    - Töltse le a Azure Migrate telepítő parancsfájlt tartalmazó tömörített fájlt a Azure Portal.
    - Bontsa ki a tömörített fájl tartalmát. Indítsa el a PowerShell-konzolt rendszergazdai jogosultságokkal.
    - Futtassa a PowerShell-szkriptet a berendezés webalkalmazásának elindításához.
    - Konfigurálja a készüléket első alkalommal, és regisztrálja a Azure Migrate projekttel.
- Több berendezést is beállíthat egyetlen Azure Migrate projekthez. Minden készüléken megtalálhatja a fizikai kiszolgálók számát. Eszközönként legfeljebb 250 kiszolgálót lehet felderíteni.

### <a name="download-the-installer-script"></a>A telepítő parancsfájl letöltése

Töltse le a készülék tömörített fájlját.

1. Az **áttelepítési célok** > **kiszolgálók** > **Azure Migrate: kiszolgáló értékelése**területen kattintson a **felderítés**gombra.
2. A számítógépek **felderítése** > a **gépek virtualizáltak?** kattintson a **nem virtualizált/egyéb**elemre.
3. A tömörített fájl letöltéséhez kattintson a **Letöltés** gombra.

    ![Telepítő letöltése](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a tömörített fájl biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a tömörített fájl kivonatának létrehozásához.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller\AzureMigrateInstaller.ps1 SHA256```

3.  A készülék verziójának 1.19.05.10 a generált kivonatnak meg kell egyeznie ezekkel a beállításokkal.

  **Algoritmus** | **Kivonat értéke**
  --- | ---
  MD5 | 5b98cf68dad495696c789bdad8f0d91b
  SHA256 | c2364887738875a31634eb0cf5defd0398f813d41069875976ca076297071e6f

### <a name="run-the-azure-migrate-installer-script"></a>A Azure Migrate telepítő parancsfájl futtatása

A telepítő parancsfájl a következő műveleteket végzi el:

- Ügynököket és webalkalmazásokat telepít a fizikai kiszolgálók felderítéséhez és értékeléséhez.
- Telepítse a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Töltse le és telepítse az IIS újraírható modulját. [Részletek](https://www.microsoft.com/download/details.aspx?id=7435).
- Frissíti a (HKLM) beállításkulcsot a Azure Migrate állandó beállítási részleteivel.
- A következő fájlokat hozza létre az elérési út alatt:
    - **Konfigurációs fájlok**:%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok**:%ProgramData%\Microsoft Azure\Logs

Futtassa a szkriptet a következő módon:

1. Bontsa ki a tömörített fájlt egy olyan mappába a kiszolgálón, amely a készüléket fogja üzemeltetni.
2. Indítsa el a PowerShellt a fenti kiszolgálón rendszergazdai (emelt szintű) jogosultsággal.
3. Módosítsa a PowerShell könyvtárat arra a mappára, ahol a rendszer kibontotta a tartalmat a letöltött tömörített fájlból.
4. Futtassa a **AzureMigrateInstaller. ps1** nevű szkriptet a következő parancs futtatásával:
    ```
    PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
    ```
A szkript a készülék webalkalmazásának sikeres befejeződése után elindítja a készüléket.

Ha bármilyen probléma merül fel, a parancsfájl-naplókat a C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log naplófájlban érheti el a hibaelhárításhoz.

> [!NOTE]
> Ne hajtsa végre a Azure Migrate telepítési parancsfájlt egy meglévő Azure Migrate berendezésen.

### <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék tud csatlakozni az [Azure URL-címekhez](migrate-support-matrix-physical.md#assessment-appliance-url-access).


### <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

1. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a berendezéshez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást az asztalról az alkalmazás parancsikonra kattintva.
2. A webalkalmazás-> **Előfeltételek beállítása**lapon tegye a következőket:
    - **Licenc**: fogadja el a licencfeltételeket, és olvassa el a harmadik féltől származó információkat.
    - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a kiszolgáló rendelkezik-e internet-hozzáféréssel. Ha a kiszolgáló proxyt használ:
        - Kattintson a **Proxybeállítások**elemre, és határozza meg a proxy címe és a figyelő portját http://ProxyIPAddress vagy http://ProxyFQDNformában.
        - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
        - Csak a HTTP-proxyk használata támogatott.
    - **Idő szinkronizálása**: az idő ellenőrzése megtörtént. A készüléken a kiszolgáló felderítésének megfelelő működéséhez az idő szinkronizálása szükséges.
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


## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

Most kapcsolódjon a készülékről a felderíteni kívánt fizikai kiszolgálókhoz, és indítsa el a felderítést.

1. Kattintson a **hitelesítő adatok hozzáadása** elemre azon fiók hitelesítő adatainak megadásához, amelyet a berendezés a kiszolgálók felderítéséhez használ majd.  
2. Adja meg az **operációs rendszert**, a hitelesítő adatok, a **Felhasználónév** és a **jelszó** felhasználóbarát nevét, majd kattintson a **Hozzáadás**gombra.
A Windows-és Linux-kiszolgálókhoz is hozzáadhat egy hitelesítő adatot.
4. Kattintson a **kiszolgáló hozzáadása**elemre, és adja meg a kiszolgáló adatait – FQDN/IP-cím és a hitelesítő adatok rövid neve (egy sor, soronként egy bejegyzés) a kiszolgálóhoz való kapcsolódáshoz.
3. Kattintson az **Érvényesítés**elemre. Az ellenőrzés után megjelenik a felderíthető kiszolgálók listája.
    - Ha egy kiszolgáló érvényesítése meghiúsul, tekintse át a hibát az **állapot** oszlopban látható ikon fölé helyezve. Javítsa ki a hibákat, és ismételje meg az érvényesítést.
    - Kiszolgáló eltávolításához válassza a > **Törlés**lehetőséget.
4. Az ellenőrzés után kattintson a Mentés gombra, **és indítsa** el a felderítést a felderítési folyamat elindításához.

Ez elindítja a felderítést. Kiszolgálónként körülbelül 1,5 percet vesz igénybe, hogy a felderített kiszolgáló metaadatai megjelenjenek a Azure Portalban.

### <a name="verify-servers-in-the-portal"></a>Kiszolgálók ellenőrzése a portálon

A felderítést követően ellenőrizheti, hogy a kiszolgálók megjelennek-e a Azure Portal.

1. Nyissa meg a Azure Migrate irányítópultot.
2. A **Azure Migrate-** servers > **Azure Migrate: kiszolgáló értékelése** lapon kattintson arra az ikonra, amely megjeleníti a **felderített kiszolgálók**darabszámát.

## <a name="set-up-an-assessment"></a>Értékelés beállítása

A Azure Migrate: Server Assessment használatával kétféle értékelést hozhat létre.

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítmény-alapú** | Értékelések az összegyűjtött teljesítményadatok alapján | **Ajánlott**virtuálisgép-méret: a processzor-és memóriahasználat adatai alapján.<br/><br/> **Ajánlott lemez típusa (standard vagy prémium szintű felügyelt lemez)** : a helyszíni lemezek IOPS és átviteli sebessége alapján.
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

6. Az értékelés létrehozása után megtekintheti azt a **kiszolgálók** > **Azure Migrate: kiszolgálói felmérés** > **értékelések**.
7. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.



## <a name="review-an-assessment"></a>Értékelés áttekintése

Az értékelés a következőket írja le:

- **Azure-készültség**: az, hogy a kiszolgálók alkalmasak-e az Azure-ba való áttelepítésre.
- **Havi költségbecslés**: a kiszolgálók Azure-ban való futtatásának becsült havi számítási és tárolási költségei.
- **Havi tárolási költségek becslése**: a lemezes tárolás becsült költségei az áttelepítés után.

### <a name="view-an-assessment"></a>Értékelés megtekintése

1. Az **áttelepítési célok** >  - **kiszolgálók**területen kattintson az **értékelések** **Azure Migrate: kiszolgáló értékelése**elemre.
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

Ebben az oktatóanyagban az alábbiakat végezte el:

> [!div class="checklist"]
> * Azure Migrate berendezés beállítása
> * Értékelés létrehozva és felülvizsgálva

Folytassa a sorozat harmadik oktatóanyagával, amelyből megtudhatja, hogyan telepíthet át fizikai kiszolgálókat az Azure-ba a Azure Migrate: Server Migration használatával.

> [!div class="nextstepaction"]
> [Fizikai kiszolgálók migrálása](./tutorial-migrate-physical-virtual-machines.md)
