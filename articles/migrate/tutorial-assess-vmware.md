---
title: VMware virtuális gépek felmérése Azure Migrate kiszolgáló értékelésével
description: Ismerteti, hogyan értékelheti a helyszíni VMware virtuális gépeket az Azure-ba való Migrálás Azure Migrate Server Assessment használatával történő áttelepítéséhez.
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: 7fcfa698b0a7e96a117b9d9663869bbec0e0079e
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/30/2020
ms.locfileid: "85550897"
---
# <a name="assess-vmware-vms-with-server-assessment"></a>VMware virtuális gépek értékelése a Server Assessmenttel

Ez a cikk bemutatja, hogyan értékelheti a helyszíni VMware virtuális gépeket (VM-EK) a [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) Tool használatával.


Ez az oktatóanyag egy sorozat második része, amely bemutatja, hogyan lehet felmérni és áttelepíteni a VMware virtuális gépeket az Azure-ba. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Azure Migrate projekt beállítása.
> * Hozzon létre egy Azure Migrate berendezést, amely a virtuális gépek felmérésére a helyszínen fut.
> * Indítsa el a helyszíni virtuális gépek folyamatos felderítését. A készülék konfigurációs és teljesítményadatokat küld a felderített virtuális gépeknek az Azure-ba.
> * Csoportosítsa a felderített virtuális gépeket, és mérje fel a virtuálisgép-csoportot.
> * Tekintse át az értékelést.

> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Az oktatóanyagok az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út. Részletes utasításokért tekintse át a útmutató cikkeket.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Előfeltételek

- [Fejezze be az első oktatóanyagot](tutorial-prepare-vmware.md) ebben a sorozatban. Ha nem, az oktatóanyagban szereplő utasítások nem fognak működni.
- Az első oktatóanyagban az alábbiakat kell elvégeznie:
    - [Készítse elő az Azure](tutorial-prepare-vmware.md#prepare-azure) -t a Azure Migrate való együttműködéshez.
    - A [VMware előkészítése](tutorial-prepare-vmware.md#prepare-for-assessment) az értékeléshez. Ide tartozik a VMware-beállítások ellenőrzése, egy olyan fiók beállítása, amelyet Azure Migrate használhat a vCenter Server eléréséhez.
    - [Ellenőrizze](tutorial-prepare-vmware.md#verify-appliance-settings-for-assessment) , hogy mire van szüksége a Azure Migrate készülék VMware-értékeléshez való telepítéséhez.

## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate projekt beállítása

Hozzon létre egy új Azure Migrate projektet a következőképpen:

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés**területen a **kiszolgálók felderítése, felmérése és migrálása**szakaszban válassza a **kiszolgálók felmérése és áttelepíteni**lehetőséget.

   ![A kiszolgálók értékelésére és átmigrálára szolgáló gomb](./media/tutorial-assess-vmware/assess-migrate.png)

4. Az **első lépések**területen válassza az **eszközök hozzáadása**lehetőséget.
5. A **Projekt migrálása** területen válassza ki az Azure-előfizetését, majd hozzon létre egy erőforráscsoportot, ha még nem rendelkezik eggyel.     
6. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét és a földrajzot, amelyben létre kívánja hozni a projektet. Tekintse át a nyilvános és a [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)támogatott földrajzi [területeit](migrate-support-matrix.md#supported-geographies-public-cloud) .

   ![A projekt neve és a régió mezői](./media/tutorial-assess-vmware/migrate-project.png)

7. Válassza a **Tovább** lehetőséget.
8. Az **Assessment (kiértékelés) eszközben**válassza a **Azure Migrate: Server Assessment Next (kiszolgáló értékelése**  >  **Next**) elemet.

   ![A kiszolgáló-Assessment eszköz kiválasztása](./media/tutorial-assess-vmware/assessment-tool.png)

9. A **Migrálási eszköz kiválasztása** területen válassza **A migrálási eszköz hozzáadásának mellőzése egyelőre** > **Tovább** lehetőséget.
10. A **felülvizsgálat + eszközök hozzáadása**lapon tekintse át a beállításokat, majd válassza az **eszközök hozzáadása**lehetőséget.
11. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik. Megnyílik a projekt oldala. Ha nem látja a projektet, az Azure Migrate irányítópult **Kiszolgálók** területéről elérheti.

## <a name="set-up-the-azure-migrate-appliance"></a>A Azure Migrate berendezés beállítása

Azure Migrate: a kiszolgáló értékelése egy könnyű Azure Migrate berendezést használ. A készülék virtuálisgép-felderítést végez, és a virtuális gépek metaadatait és teljesítményadatait Azure Migrateba küldi. A készülék több módon is beállítható.

- A VMware virtuális gépen egy letöltött PETESEJT-sablonnal állíthatja be a szolgáltatást. Ez az oktatóanyagban használt módszer.
- Állítsa be a VMware virtuális gépen vagy fizikai gépen egy PowerShell-telepítő parancsfájl használatával. [Ezt a módszert](deploy-appliance-script.md) akkor kell használni, ha nem állít be egy virtuális GÉPET egy petesejt-sablon használatával, vagy ha Azure Government.

A berendezés létrehozása után győződjön meg róla, hogy tud csatlakozni Azure Migrate: kiszolgáló-értékeléshez, először konfigurálja, majd regisztrálja azt a Azure Migrate projektben.


### <a name="download-the-ova-template"></a>A petesejtek sablon letöltése

1. Az **áttelepítési célok**  >  **kiszolgálói**  >  **Azure Migrate: kiszolgáló értékelése**területen válassza a **felderítés**lehetőséget.
2. A **felderítési gépek**a  >  **gépek virtualizáltak?** területen válassza **az igen, a VMware vSphere hypervisor**lehetőséget.
3. Válassza a **Letöltés** lehetőséget a petesejtek sablon fájljának letöltéséhez.

   ![A PETESEJT-fájlok letöltésének kiválasztása](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a petesejtek fájlja biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a PETESEJT-fájl kivonatának létrehozásához:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Ellenőrizze a készülék legújabb verzióit és a kivonatoló értékeket:

    - Az Azure nyilvános felhőben:
    
        **Algoritmus** | **Letöltés** | **SHA256**
        --- | --- | ---
        VMware (10,9 GB) | [Legújabb verzió](https://aka.ms/migrate/appliance/vmware) | cacbdaef927fe5477fa4e1f494fcb7203cbd6b6ce7402b79f234bc0fe69663dd

    - Azure Government esetén:
    
        **Algoritmus** | **Letöltés** | **SHA256**
        --- | --- | ---
        VMware (63,1 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2120300&clcid=0x409 ) | 3d5822038646b81f458d89d706832c0a2c0e827bfa9b0a55cc478eaf2757a4de


### <a name="create-the-appliance-vm"></a>A berendezés virtuális gép létrehozása

Importálja a letöltött fájlt, és hozzon létre egy virtuális gépet:

1. A vSphere-ügyfél konzolon válassza a **fájl**  >  **telepítése OVF-sablon**lehetőséget.

   ![OVF-sablon üzembe helyezéséhez használható menüparancsok](./media/tutorial-assess-vmware/deploy-ovf.png)

2. A OVF-sablon központi telepítése varázslóban > **forrás**mezőben határozza meg a petesejt-fájl helyét.
3. A **név** és **hely**mezőben adjon meg egy rövid nevet a virtuális gép számára. Válassza ki azt a leltár objektumot, amelyben a virtuális gép üzemeltetve lesz.
4. A **gazdagép/fürt**területen adja meg azt a gazdagépet vagy fürtöt, amelyen a virtuális gép futni fog.
5. A **tárterület**területen határozza meg a virtuális gép tárolási célját.
6. A **Disk Format** (Lemezformátum) mezőben adja meg a lemez típusát és méretét.
7. A **hálózati megfeleltetés**területen válassza ki azt a hálózatot, amelyhez a virtuális gép csatlakozni fog. A hálózat internetkapcsolatra van szüksége, hogy metaadatokat küldjön Azure Migrate kiszolgáló értékelésére.
8. Tekintse át és erősítse meg a beállításokat, majd kattintson a **Befejezés gombra**.

## <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék virtuális gépe tud csatlakozni az Azure URL-címekhez a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati](migrate-appliance.md#government-cloud-urls) felhők számára.

### <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

> [!NOTE]
> Ha a letöltött petesejtek helyett [PowerShell-parancsfájl](deploy-appliance-script.md) használatával állítja be a készüléket, az ebben az eljárásban szereplő első két lépés nem releváns.

1. A vSphere-ügyfél konzolján kattintson a jobb gombbal a virtuális gépre, majd válassza a **konzol megnyitása**lehetőséget.
2. Adja meg a berendezés nyelvét, időzónáját és jelszavát.
3. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a virtuális géphez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást a készülék asztaláról az alkalmazás parancsikonjának kiválasztásával.
1. A webalkalmazás-> **Előfeltételek beállítása**lapon tegye a következőket:
   - **Licenc**: fogadja el a licencfeltételeket, és olvassa el a harmadik féltől származó információkat.
   - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
     - Válassza a **Proxybeállítások**lehetőséget, majd adja meg a proxy címe és a figyelő portot az űrlapon vagy a-ben http://ProxyIPAddress http://ProxyFQDN .
     - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
     - Csak a HTTP-proxyk használata támogatott.
   - **Időszinkronizálás**: a készüléken az idő a megfelelő működés érdekében szinkronban kell lennie az internettel.
   - **Frissítések telepítése**: a készülék gondoskodik a legújabb frissítések telepítéséről.
   - A **VDDK telepítése**: a készülék ellenőrzi, hogy telepítve van-e a VMware vSphere Virtual Disk Development Kit (VDDK). Ha nincs telepítve, töltse le a VDDK 6,7-et a VMware-ből, és bontsa ki a letöltött zip-tartalmat a készülék megadott helyére.

     Azure Migrate kiszolgáló áttelepítése a VDDK használatával replikálja a gépeket az Azure-ba való áttelepítés során.       

### <a name="register-the-appliance-with-azure-migrate"></a>A készülék regisztrálása a Azure Migrate

1. Válassza **a bejelentkezés**lehetőséget. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.
2. Az új lapon jelentkezzen be az Azure-beli felhasználónevével és jelszavával.
   
   A PIN-kóddal való bejelentkezés nem támogatott.
3. A sikeres bejelentkezést követően térjen vissza a webalkalmazáshoz.
4. Válassza ki azt az előfizetést, amelyben a Azure Migrate projektet létrehozták, majd válassza ki a projektet.
5. Adja meg a berendezés nevét. A névnek legfeljebb 14 karakterből kell állnia.
6. Kattintson a **Register** (Regisztrálás) elemre.


## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

A készüléknek csatlakoznia kell a vCenter Serverhoz a virtuális gépek konfigurációjának és teljesítményének felderítéséhez.

### <a name="specify-vcenter-server-details"></a>A vCenter Server adatainak megadása
1. A **vCenter Server részletek megadása lapon**adja meg a vCenter Server példány nevét (FQDN) vagy IP-címét. Meghagyhatja az alapértelmezett portot, vagy megadhat egy egyéni portot, amelyen vCenter Server figyeli.
2. A **Felhasználónév** és a **jelszó**mezőben adja meg azokat a vCenter Server fiók hitelesítő adatait, amelyeket a berendezés a vCenter Server példányon lévő virtuális gépek felderítéséhez használni fog. 

    - Be kell állítania egy fiókot a szükséges engedélyekkel az [előző oktatóanyagban](tutorial-prepare-vmware.md#set-up-permissions-for-assessment).
    - Ha a hatókör-felderítést meghatározott VMware-objektumokra (vCenter Server adatközpontokra, fürtökre, fürtökre, gazdagépekre, gazdagépekre vagy egyéni virtuális gépekre) szeretné alkalmazni, tekintse át az [ebben a cikkben](set-discovery-scope.md) szereplő utasításokat a Azure Migrate által használt fiók korlátozásához.

3. Válassza a **kapcsolat ellenőrzése** lehetőséget, és győződjön meg arról, hogy a készülék csatlakozhat vCenter Serverhoz.
4. Az **alkalmazások és a virtuális gépek függőségeinek felderítése**területen kattintson a **hitelesítő adatok hozzáadása**lehetőségre, és adja meg azt az operációs rendszert, amelyhez a hitelesítő adatok érvényesek, valamint a hitelesítő adatok felhasználónevét és jelszavát. Ezután kattintson az **Add** (Hozzáadás) gombra.

    - Itt opcionálisan adhat meg hitelesítő adatokat, ha létrehozott egy fiókot az [alkalmazás-felderítési szolgáltatáshoz](how-to-discover-applications.md), vagy az [ügynök nélküli függőségek elemzése funkciót](how-to-create-group-machine-dependencies-agentless.md).
    - Ha nem használja ezeket a funkciókat, akkor kihagyhatja ezt a beállítást.
    - Tekintse át az alkalmazás- [felderítéshez](migrate-support-matrix-vmware.md#application-discovery-requirements)szükséges hitelesítő adatokat, vagy az [ügynök nélküli elemzéshez](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. A virtuális gép felderítésének **elindításához mentse és indítsa el a felderítést**.

A felderítés a következőképpen működik:
- Körülbelül 15 percet vesz igénybe, hogy a felderített virtuális gépek metaadatai megjelenjenek a portálon.
- A telepített alkalmazások, szerepkörök és szolgáltatások felderítése hosszabb időt vesz igénybe. Az időtartam a felderített virtuális gépek számától függ. Az 500-es virtuális gépek esetében körülbelül egy óra telik, amíg az alkalmazás leltára megjelenik a Azure Migrate-portálon.

### <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

A felderítést követően ellenőrizheti, hogy a virtuális gépek megjelennek-e a Azure Portalban:

1. Nyissa meg a Azure Migrate irányítópultot.
2. A **Azure Migrate-** Servers  >  **Azure Migrate: kiszolgáló értékelése**lapon válassza ki a **felderített kiszolgálók**darabszámát megjelenítő ikont.

## <a name="set-up-an-assessment"></a>Értékelés beállítása

Azure Migrate Server Assessment használatával kétféle értékelést hozhat létre:

**Értékelés típusa** | **Részletek**
--- | --- 
**Azure VM** | Értékelések a helyszíni kiszolgálók Azure-beli virtuális gépekre való átköltöztetéséhez. <br/><br/> A helyszíni [VMWare virtuális gépeket](how-to-set-up-appliance-vmware.md), a [Hyper-V virtuális gépeket](how-to-set-up-appliance-hyper-v.md)és a [fizikai kiszolgálókat](how-to-set-up-appliance-physical.md) felhasználhatja az Azure-ba való áttelepítéshez ezzel az értékelési típussal. [További információ](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | A helyszíni kiszolgálók [Azure VMware-megoldásba (AVS)](https://docs.microsoft.com/azure/azure-vmware/introduction)való átköltöztetésének felmérése. <br/><br/> A helyszíni [VMWare virtuális gépeket](how-to-set-up-appliance-vmware.md) az értékelés típusának használatával értékelheti az Azure VMware-megoldásba (AVS) való áttelepítésre. [További információ](concepts-azure-vmware-solution-assessment-calculation.md)

A kiszolgáló értékelése két méretezési feltétel beállítását biztosítja:

**Méretezési feltételek** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítmény-alapú** | Az összegyűjtött teljesítményadatok alapján ajánlásokat tevő értékelések | **Azure VM Assessment**: a virtuálisgép-méretre vonatkozó javaslat a CPU-és memória-kihasználtsági adatain alapul.<br/><br/> A lemez típusára vonatkozó javaslat (standard HDD/SSD vagy prémium szintű Managed Disks) a helyszíni lemezek IOPS és átviteli sebességén alapul.<br/><br/> **Azure VMware-megoldás (AVS) értékelése**: az AVS-csomópontok ajánlásai a processzor-és memóriahasználat adatain alapulnak.
**Helyszíni** | Az olyan értékelések, amelyek nem használnak teljesítményadatokat, ajánlásokat tesznek. | **Azure VM Assessment**: a virtuális gépek méretére vonatkozó javaslat a helyszíni virtuális gép méretétől függ.<br/><br> Az ajánlott lemez típusa az értékeléshez megadott tárolási típus beállításán alapul.<br/><br/> **Azure VMware-megoldás (AVS) értékelése**: az AVS-csomópontok ajánlásai a helyszíni virtuális gép méretétől függenek.

## <a name="run-an-assessment"></a>Értékelés futtatása

Futtasson egy Azure-beli *virtuális gép értékelését* az alábbiak szerint:

1. Tekintse át az értékelések létrehozásával kapcsolatos [ajánlott eljárásokat](best-practices-assessment.md) .
2. A **kiszolgálók** lap **Azure Migrate: kiszolgáló értékelése** csempén válassza az **értékelés**lehetőséget.

   ![Az értékelés gomb helye](./media/tutorial-assess-vmware/assess.png)

3. A **kiszolgálók értékelése**területen válassza ki az értékelés típusát "Azure VM" néven, válassza ki a felderítés forrását, és adja meg az értékelés nevét.

    ![Értékelés alapjai](./media/tutorial-assess-vmware/assess-servers-azurevm.png)
 
4. Válassza az **összes megtekintése**lehetőséget, majd tekintse át az értékelés tulajdonságait.

   ![Értékelés tulajdonságai](./media/tutorial-assess-vmware/view-all.png)

5. A **tovább** gombra kattintva **kiválaszthatja a gépeket az értékeléshez**. A **válasszon ki vagy hozzon létre egy csoportot**, válassza az **új létrehozása**lehetőséget, és adjon meg egy csoportnevet. Egy csoport egy vagy több virtuális gépet gyűjt össze az értékeléshez.
6. A **számítógépek hozzáadása a csoporthoz**területen válassza ki a csoportba felvenni kívánt virtuális gépeket.
7. Az értékelés részleteinek áttekintéséhez kattintson a **tovább** gombra a **+ Értékelés létrehozása** elemre.
8. Az **Értékelés létrehozása** elemre kattintva hozza létre a csoportot, és futtassa az értékelést.

   ![Kiszolgálók értékelése](./media/tutorial-assess-vmware/assessment-create.png)

8. Az értékelés létrehozása után tekintse meg a **kiszolgálók**  >  **Azure Migrate: kiszolgáló-értékelési**  >  **értékelések**.
9. Válassza az **értékelés exportálása** lehetőséget, ha Excel-fájlként szeretné letölteni.

Ha az **Azure VMware-megoldás (AVS) értékelését**szeretné futtatni, kövesse az [itt](how-to-create-azure-vmware-solution-assessment.md)leírt lépéseket.


## <a name="review-an-assessment"></a>Értékelés áttekintése

Az értékelés a következőket írja le:

- **Azure-készültség**: azt, hogy a virtuális gépek alkalmasak-e az Azure-ba való áttelepítésre.
- **Havi költségbecslés**: a virtuális gépek Azure-ban való futtatásának becsült havi számítási és tárolási költségei.
- **Havi tárolási költségek becslése**: a lemezes tárolás becsült költségei az áttelepítés után.

Értékelés megtekintése:

1. Az **áttelepítési célok**  >  **kiszolgálóin**válassza az **értékelések** **Azure Migrate: kiszolgáló értékelése**elemet.
2. Az **értékelések**területen válasszon ki egy értékelést a megnyitásához.

   ![Értékelés összegzése](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Az Azure készültségének áttekintése

1. Az **Azure készültségi**területén ellenőrizze, hogy a virtuális gépek készen állnak-e az Azure-ba való áttelepítésre.
2. A virtuális gép állapotának áttekintése:
    - **Készen áll az Azure-ra**: használatban van, amikor a Azure Migrate javasolja a virtuális gépek méretének és becsült értékének becslését az értékelés során.
    - **Feltételekkel kész**: megjeleníti a problémákat és a javasolt szervizelést.
    - **Nem áll készen az Azure-ra: a**problémákat és a javasolt szervizelést jeleníti meg.
    - **Felkészültségi ismeretlen**: akkor használatos, ha a Azure Migrate nem tudja felmérni a készültséget az adatelérhetőségi problémák miatt.

3. Válasszon ki egy **Azure-készültségi** állapotot. Megtekintheti a VM-készültség részleteit. A virtuális gép részleteit is megtekintheti, beleértve a számítási, tárolási és hálózati beállításokat.

### <a name="review-cost-details"></a>A Cost részleteinek áttekintése

Az értékelés összegzése az Azure-ban futó virtuális gépek becsült számítási és tárolási költségeit mutatja. A költségek összesítése az összes virtuális gép számára történik a vizsgált csoportban. A részletezést lenyomva megtekintheti az adott virtuális gépek részletes költségeit.

> [!NOTE]
> A költségbecslés a gép, a lemezek és a hozzá tartozó tulajdonságok méretére vonatkozó javaslatok alapján történik. A becslések a helyszíni virtuális gépek IaaS virtuális gépekként való futtatására szolgálnak. Azure Migrate Server Assessment nem veszi figyelembe a Pásti vagy az SaaS költségeit.

Az értékelt csoport összesített tárolási költségei különböző típusú tárolóeszközökön vannak felosztva. 

### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

Azure Migrate Server Assessment megbízhatósági minősítést rendel a teljesítmény-alapú értékeléshez, egy csillag (legalacsonyabb) és öt csillag között (a legmagasabb).

![Megbízhatósági minősítés](./media/tutorial-assess-vmware/confidence-rating.png)

A megbízhatósági minősítés segít megbecsülni az értékelés méretére vonatkozó javaslatok megbízhatóságát. A minősítés az értékelés kiszámításához szükséges adatpontok rendelkezésre állásán alapul:

**Adatpont rendelkezésre állása** | **Megbízhatósági minősítés**
--- | ---
0%–20% | 1 csillag
21%–40% | 2 csillag
41%–60% | 3 csillag
61%–80% | 4 csillag
81%–100% | 5 csillag

[Ismerje meg](best-practices-assessment.md#best-practices-for-confidence-ratings) a megbízhatósági minősítéssel kapcsolatos ajánlott eljárásokat.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure Migrate berendezést állít be. Az értékelést is létrehozta és felülvizsgálta.

A VMware virtuális gépek Azure-ba történő áttelepítésének megismeréséhez Azure Migrate kiszolgáló áttelepítésének használatával folytassa a sorozat harmadik oktatóanyagával:

> [!div class="nextstepaction"]
> [VMware virtuális gépek migrálása](./tutorial-migrate-vmware.md)
