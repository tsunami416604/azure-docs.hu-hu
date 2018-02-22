---
title: "Helyszíni VMware virtuális gépek felderítése és kiértékelése az Azure-ba való migráláshoz az Azure Migrate segítségével | Microsoft Docs"
description: "Ismerteti, hogyan derítheti fel és értékelheti ki a helyszíni VMware virtuális gépeket az Azure-ba való migráláshoz az Azure Migrate szolgáltatással."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 01/08/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: bbcfe95f5427681f8d55d647b102d35fc37f15ee
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2018
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Helyszíni VMware virtuális gépek felderítése és kiértékelése az Azure-ba való migráláshoz

Az [Azure Migrate](migrate-overview.md) szolgáltatás a helyszíni számítási feladatokat értékeli ki az Azure-ra történő migráláshoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Migrate-projekt létrehozása.
> * Helyszíni gyűjtő virtuális gép (VM) beállítása a helyszíni VMware virtuális gépek felderítéséhez kiértékelésre.
> * Virtuális gépek csoportosítása és értékelés készítése.


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prerequisites"></a>Előfeltételek

- **VMware**: A migrálni kívánt virtuális gépeket egy 5.5-ös, 6.0-s vagy 6.5-ös verziójú vCenter Servernek kell felügyelnie. Emellett egy 5.0-s vagy újabb verziójú ESXi gazdagépre is szükség van a gyűjtő virtuális gép üzembe helyezéséhez. 
 
> [!NOTE]
> A Hyper-V támogatása az ütemtervünk részét képezi, és hamarosan elérhető lesz. 

- **vCenter Server-fiók**: A vCenter Server eléréséhez egy csak olvasási jogokat biztosító fiók szükséges. Az Azure Migrate ezt a fiókot használja a helyszíni virtuális gépek felderítéséhez.
- **Engedélyek**: A vCenter Serveren megfelelő jogosultságra van szüksége ahhoz, hogy a virtuális gépeket .OVA-formátumú fájlok importálásával létrehozhassa. 
- **Statisztikai beállítások**: A vCenter Server statisztikai beállításait a 3. szintre kell konfigurálni a telepítés megkezdése előtt. A 3. szintnél alacsonyabb konfiguráció esetén a kiértékelés működni fog, de a tároló és a hálózat adatai nem lesznek gyűjtve. A méretezési javaslatok alapjául ebben az esetben a processzor és a memória teljesítményadatai, valamint a lemezek és a hálózati adapterek konfigurációs adatai szolgálnak majd. 

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra
Jelentkezzen be az [Azure portálra](https://portal.azure.com).

## <a name="create-a-project"></a>Projekt létrehozása

1. Az Azure Portalon kattintson az **Erőforrás létrehozása** gombra.
2. Keressen az **Azure Migrate** kifejezésre, és válassza ki az **Azure Migrate (előzetes verzió)** elemet a keresési eredmények közül. Ezt követően kattintson a **Create** (Létrehozás) gombra.
3. Adja meg a projekt nevét, majd a projekthez tartozó Azure-előfizetést.
4. Hozzon létre egy új erőforráscsoportot.
5. Adja meg a helyet a projekt létrehozásához, majd kattintson a **Létrehozás** gombra. Az előzetes verzióban Azure Migrate-projektet csak az USA középnyugati régiójában lehet létrehozni. Azonban továbbra is bármely Azure-beli célhelyre tervezheti a migrálást. A projekthez megadott hely csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál. 

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)
    


## <a name="download-the-collector-appliance"></a>A gyűjtőberendezés letöltése

Az Azure Migrate létrehoz egy gyűjtőberendezésnek nevezett helyszíni virtuális gépet. A virtuális gép felderíti a helyszíni VMware virtuális gépeket, és az azokkal kapcsolatos metaadatokat továbbítja az Azure Migrate szolgáltatásnak. A gyűjtőberendezés beállításához egy .OVA-fájlt kell letöltenie, majd importálnia a helyszíni vCenter-kiszolgálóra a virtuális gép létrehozásához.

1. Az Azure Migrate projektben kattintson a **Bevezetés** > **Felderítés és értékelés** > **Gépek felderítése** elemre.
2. A **Gépek felderítése** területen kattintson a **Letöltés** gombra az .OVA-fájl letöltéséhez.
3. A **Projekt hitelesítő adatainak másolása** területen másolja ki a projekt azonosítóját és kulcsát. Ezekre a gyűjtő konfigurálásához lesz szüksége.

    ![Az .ova-fájl letöltése](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>A gyűjtőberendezés ellenőrzése

A telepítése előtt ellenőrizze, hogy az .OVA-fájl biztonságos-e.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot az OVA kivonatának létrehozásához:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. A létrehozott kivonatnak egyeznie kell ezekkel a beállításokkal.
    
    Az OVA 1.0.8.49-es verziója esetében
    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | cefd96394198b92870d650c975dbf3b8 
    SHA1 | 4367a1801cf79104b8cd801e4d17b70596481d6f
    SHA256 | fda59f076f1d7bd3ebf53c53d1691cc140c7ed54261d0dc4ed0b14d7efef0ed9

    Az OVA 1.0.8.40-es verziója esetében:

    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>A gyűjtő virtuális gép létrehozása

Importálja a letöltött fájlt a vCenter Serverre.

1. A vSphere Client-konzolon kattintson a **Fájl** > **OVF-sablon telepítése** elemre.

    ![Az OVF telepítése](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. Az OVF-sablon telepítése varázsló **Source** (Forrás) lapján adja meg az .ova-fájl helyét.
3. A **Name** (Név) és a **Location** (Hely) mezőben adjon meg egy rövid nevet a gyűjtő virtuális gépnek, valamint az azt futtató leltárobjektumnak.
5. A **Host/Cluster** (Gazdagép/fürt) mezőben adja meg a gazdagépet vagy fürtöt, amelyen a gyűjtő virtuális gép futni fog.
7. A tárolóban adja meg a célhelyet a gyűjtő virtuális gép tárolásához.
8. A **Disk Format** (Lemezformátum) mezőben adja meg a lemez típusát és méretét.
9. A **Network Mapping** (Hálózatleképezés) mezőben adja meg a hálózatot, amelyhez a gyűjtő virtuális gép kapcsolódni fog. A hálózatnak internetkapcsolattal kell rendelkeznie a metaadatok az Azure-ba küldéséhez. 
10. Tekintse át és hagyja jóvá a beállításokat, majd kattintson a **Finish** (Befejezés) gombra.

## <a name="run-the-collector-to-discover-vms"></a>A gyűjtő futtatása a virtuális gépek felderítéséhez

1. A vSphere Client-konzolon kattintson a jobb gombbal a virtuális gépre, majd kattintson az **Open Console** (Konzol megnyitása) elemre.
2. Adja meg a berendezés nyelv-, időzóna- és jelszóbeállításait.
3. Az asztalon kattintson a **Gyűjtő futtatása** parancsikonra.
4. Az Azure Migrate Collectorban nyissa meg a **Set up prerequisites** (Előfeltételek megadása) felületet.
    - Fogadja el a licencfeltételeket, és olvassa el a külső szolgáltatóval kapcsolatos információkat.
    - A gyűjtő ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel.
    - Ha a virtuális gép proxykiszolgálón keresztül éri el az internetet, kattintson a **Proxy settings** (Proxybeállítások) gombra, és adja meg a proxykiszolgáló címét és a figyelőportot. Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.

    > [!NOTE]
    > A proxykiszolgáló címét a http://ProxyIPcíme vagy a http://Proxyteljestartományneve formátumban kell megadni. Csak a HTTP-proxyk használata támogatott.

    - A gyűjtő ellenőrzi, hogy fut-e a gyűjtőszolgáltatás. A szolgáltatás alapértelmezés szerint telepítve van a gyűjtő virtuális gépen.
    - Töltse le és telepítse a VMware PowerCLI-t.

5. A **Specify vCenter Server details** (vCenter Server adatainak megadása) területen tegye a következőket:
    - Adja meg a vCenter-kiszolgáló nevét (FQDN, teljes tartománynév) vagy IP-címét.
    - A **User name** (Felhasználónév) és a **Password** (Jelszó) mezőben adja meg a csak olvasási jogokkal rendelkező fiók hitelesítő adatait, amelyet a gyűjtő a virtuális gépek felderítéséhez használ majd a vCenter-kiszolgálón.
    - A **Collection scope** (Gyűjtés hatóköre) mezőben válassza ki a virtuális gépek felderítésének hatókörét. A gyűjtő csak a megadott hatókörön belül deríti fel a virtuális gépeket. A hatókör egy adott mappára, adatközpontra vagy fürtre állítható be. Nem tartalmazhat 1000-nél több virtuális gépet. 

6. A **Specify migration project** (Migrálási projekt megadása) területen adja meg az Azure Migrate projekt a portálról kimásolt azonosítóját és kulcsát. Ha nem másolta ki őket, nyissa meg az Azure Portalt a gyűjtő virtuális gépről. A projekt **Áttekintés** lapján kattintson a **Gépek felderítése** elemre, és másolja ki az értékeket.  
7. A **View collection progress** (Gyűjtési folyamat megtekintése) területen monitorozhatja a felderítési folyamatot, és győződhet meg róla, hogy a virtuális gépekről gyűjtött metaadatok a hatókörbe tartoznak-e. Az adatgyűjtő mutatja a felderítés hozzávetőleges időtartamát.

> [!NOTE]
> Az adatgyűjtő csak az „Angol (Egyesült Államok)” nyelvet támogatja az operációs rendszer és az adatgyűjtő felület nyelveként. A támogatás további nyelveken is hamarosan elérhetővé válik.


### <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

A felderítési idő a felderített virtuális gépek számától függ. 100 virtuális gép esetében tipikusan egy órát vesz igénybe a felderítés lefutása a gyűjtő futtatását követően. 

1. A Migration Planner-projektben kattintson a **Manage** > **Machines** (Felügyelet > Gépek) elemre.
2. Ellenőrizze, hogy a felderíteni kívánt virtuális gépek megjelennek-e a portálon.


## <a name="create-and-view-an-assessment"></a>Értékelés készítése és megtekintése

A virtuális gépeket a felderítésüket követően csoportosíthatja, és értékelést hozhat létre. 

1. A projekt **Áttekintés** lapján kattintson az **+Értékelés létrehozása** elemre.
2. Kattintson az **Összes megtekintése** elemre az értékelési tulajdonságok áttekintéséhez.
3. Hozza létre a csoportot, és adja meg a nevét.
4. Válassza ki a csoporthoz hozzáadni kívánt gépeket.
5. A csoport és az értékelés létrehozásához kattintson az **Értékelés létrehozása** elemre.
6. Az értékelést a létrehozását követően az **Áttekintés** > **Irányítópult** menüpontban tekintheti meg.
7. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.

### <a name="sample-assessment"></a>Minta értékelés

Íme egy példa az értékelési jelentésre. A virtuális gépek az Azure-ral való kompatibilitására és a becsült havi költségekre vonatkozó információkat tartalmaz. 

![Értékelési jelentés](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Az Azure használatához szükséges állapot

Ez a nézet az egyes gépek készenléti állapotát jeleníti meg.

- A kész virtuális gépekhez az Azure Migrate javasol egy virtuálisgép-méretet az Azure-ban.
- A nem kész virtuális gépek esetében az Azure Migrate ismerteti az okokat, valamint a javításhoz szükséges lépéseket.
- Az Azure Migrate javaslatokat tesz a migráláshoz használható eszközökre. Ha a gép alkalmas az átemeléses migrálásra, a rendszer az Azure Site Recovery szolgáltatást javasolja. Ha a gép egy adatbázisgép, a rendszer az Azure Database Migration Service szolgáltatást javasolja.

  ![Készenléti állapot értékelése](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Havi költségbecslés

Ez a nézet a virtuális gépek az Azure-ban való futtatásával kapcsolatos összes számítási és tárolási költséget mutatja az egyes gépek adataival együtt. A költségbecslések a gépre és annak lemezeire vonatkozó teljesítményalapú méretajánlásokon, valamint az értékelési tulajdonságokon alapulnak. 

> [!NOTE]
> Az Azure Migrate által adott költségbecslések a helyszíni virtuális gépek az Azure szolgáltatott infrastruktúrában (IaaS) üzemelő virtuális gépekként való futtatására vonatkoznak. Az Azure Migrate a szolgáltatásként nyújtott platformokra (PaaS) vagy a szolgáltatott szoftverekre (SaaS) vonatkozó költségeket nem vesz figyelembe. 

A becsült havi számítási és tárolási költségek a csoportban lévő virtuális gépekre összesítve szerepelnek. 

![Értékelés – VM-költségek](./media/tutorial-assessment-vmware/assessment-vm-cost.png) 

Az egyes gépek részletes elemzését is megtekintheti.

![Értékelés – VM-költségek](./media/tutorial-assessment-vmware/assessment-vm-drill.png) 

## <a name="next-steps"></a>További lépések

- [Ismerje meg](how-to-scale-assessment.md), hogyan deríthetők fel és értékelhetők a nagyobb VMware-környezetek.
- Ismerje meg, hogyan hozhat létre megbízható értékelési csoportokat [gépfüggőségi leképezések](how-to-create-group-machine-dependencies.md) használatával
- [További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
