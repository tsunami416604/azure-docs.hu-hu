---
title: Helyszíni számítási feladatok Azure-ba való migrálásának értékelése a DMA és az Azure Migrate használatával | Microsoft Docs
description: Ismerje meg, hogyan készítheti elő az Azure-t a helyszíni gépek migrálására a Migration Assistant (DMA) és az Azure Migrate szolgáltatás használatával.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: fb102cc43c6e1d17afaa78a2833ae447600a96af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="scenario-1-assess-on-premises-workloads-for-migration-to-azure"></a>1. forgatókönyv: Helyszíni számítási feladatok Azure-ba való migrálásának értékelése

Az Azure-ba való migrálás lehetőségeinek feltérképezése során a Contoso vállalat műszaki és pénzügyi értékeléséket szeretne végezni annak felmérésére, hogy a helyszíni számítási feladataik alkalmasak-e a felhőbe való migrálásra. Különösképpen a gépek és adatbázisok kompatibilitását szeretnék felmérni, valamint felbecsülni erőforrásaik kapacitásait és költségeit az Azure-ban való futtatás esetén.

Hogy közelről is megismerhessék és jobban megérthessék az érintett technológiákat, először egy kisebb helyszíni utazási alkalmazást értékelnek ki és migrálnak. Ez egy kétszintű alkalmazás, amely egy virtuális gépen futó webalkalmazásból és egy másik virtuális gépen futó SQL Server-adatbázisból áll. Az alkalmazás a VMware-ben üzemel, a környezetet pedig a vCenter Server kezeli. A Data Migration Assistant (DMA) és az Azure Migrate szolgáltatás használatával végzik az értékelést. Ha ki szeretné próbálni a forgatókönyvet a szemléltetésre használt utazási alkalmazással, letöltheti a [GitHubról](https://github.com/Microsoft/SmartHotel360).

**Technológia** | **Leírás** | **Költségek**
--- | --- | ---
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | A DMA értékeli és észleli a kompatibilitási problémákat, amelyek befolyásolhatják az adatbázisokkal kapcsolatos funkciókat az Azure-ban. Emellett kiértékeli a szolgáltatásparitást a forrás és a cél SQL Server között, és teljesítmény- és megbízhatóságbeli fejlesztéseket javasol a célkörnyezethez. | Ez egy ingyenesen letölthető eszköz. 
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | A szolgáltatás segít kiértékelni a helyszíni gépeket az Azure-ba való migrálás előtt. Felméri a gépek migrálásra való alkalmasságát, és méretezési, illetve költségbecsléseket ad az Azure-ban való futtatásra vonatkozóan. Az Azure Migrate szolgáltatás jelenleg helyszíni VMware virtuális gépeket tud értékelni az Azure-ba való migrálás előtt. | A szolgáltatás jelenleg (2018. április) díjmentesen használható.
[Szolgáltatástérkép](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Az Azure Migrate a Service Map használatával jeleníti meg a függőségeket a migrálni kívánt gépek között. |  A Service Map az Azure Log Analytics részét képezi. Jelenleg 180 napig díjmentesen használható. 

Ebben a forgatókönyvben a DMA letöltésével és futtatásával értékeljük az utazási alkalmazás helyszíni SQL Server-adatbázisát. Az Azure Migrate és függőségi leképezés használatával értékeljük az alkalmazás virtuális gépeit, mielőtt migrálnánk őket az Azure-ba.

> [!NOTE]
> Ebben a forgatókönyvben az adatbázisra vonatkozó értékelési célunk „egy Azure-beli virtuális gépen futó SQL Server” lesz. Azonban a következő forgatókönyvet bemutató cikkben egy felügyelt Azure SQL-példányra való migrálást hajtunk végre. Azért alkalmazzuk ezt a módszert, mert a DMA jelenleg nem támogatja a felügyelt Azure SQL-példányra történő migrálás értékelését.

## <a name="architecture"></a>Architektúra


![Migrálásfelmérési architektúra](./media/migrate-scenarios-assessment/migration-assessment-architecture.png)

Ebben a forgatókönyvben:
- A Contoso egy kitalált név, amely egy tipikus nagyvállalatot jelöl. A Contoso értékelni és migrálni kívánja a kétszintű helyszíni utazási alkalmazását.
- A Contoso egy helyszíni tartományvezérlővel (**contosodc1**) ellátott helyszíni adatközponttal (**contoso-datacenter**) rendelkezik.
- A belső utazási alkalmazás két szintre van osztva két virtuális gépre (**WEBVM** és **SQLVM**), és a **contosohost1.contoso.com** VMware ESXi-gazdagépen található.
- A VMware-környezetet egy virtuális gépen futó vCenter Server (**vcenter.contoso.com**) felügyeli.




## <a name="prerequisites"></a>Előfeltételek

A forgatókönyv megvalósításához a következők szükségesek:

- Egy 5.5-ös, 6.0-s vagy 6.5-ös verziójú helyszíni vCenter-kiszolgáló.
- Egy csak olvasható fiók a vCenter-kiszolgálón, vagy a szükséges jogosultság egy ilyen fiók létrehozásához. 
- Jogosultság egy virtuális gép létrehozására a vCenter-kiszolgálón egy .OVA-sablon használatával.
- Legalább egy 5.0-s vagy újabb verziójú ESXi-gazdagép.
- Legalább két helyszíni VMware virtuális gép, amelyek közül az egyik egy SQL Server-adatbázist futtat.
- Rendelkeznie kell a szükséges jogosultsággal az Azure Migrate-ügynökök telepítéséhez minden virtuális gépen.
- A virtuális gépeknek közvetlen internetkapcsolattal kell rendelkezniük.
        - Az internetes hozzáférést korlátozhatja [a szükséges URL-címekre](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).
        - Ha olyan gépekkel rendelkezik, amelyeken nincs internetkapcsolat, le kell töltenie és telepítenie kell [az OMS-átjárót](../log-analytics/log-analytics-oms-gateway.md).
- Az SQL Server-példányt futtató virtuális gép teljes tartományneve az adatbázis-értékeléshez.
- Az SQL Server virtuális gépen futó Windows tűzfalnak engedélyeznie kell a külső kapcsolatokat a 1433-as (alapértelmezett) TCP-porton, hogy a DMA csatlakozni tudjon.


## <a name="scenario-overview"></a>Forgatókönyv áttekintése

A következő lépéseket hajtjuk végre:


> [!div class="checklist"]
> * **1. lépés: Az Azure előkészítése**. Csak egy Azure-előfizetésre van szükségünk.
> * **2. lépés: A DMA letöltése és telepítése**: A DMA előkészítése a helyszíni SQL Server-adatbázis értékelésére.
> * **3. lépés: Az adatbázis értékelése**: Az adatbázis-kiértékelés futtatása és elemzése.
> * **4. lépés: Felkészülés a virtuális gépek Azure Migrate-tel történő értékelésére**: A helyszíni fiókok telepítése és a VMware-beállítások optimalizálása.
> * **5. lépés: Helyszíni virtuális gépek felderítése**: Egy Azure Migrate gyűjtő virtuális gép létrehozása, majd a gyűjtő futtatása az értékelni kívánt virtuális gépek felderítésére.
> * **6. lépés: Felkészülés a függőségelemzésre**: Azure Migrate-ügynökök telepítése a virtuális gépeken a virtuális gépek közötti függőségi leképezés megjelenítéséhez.
> * **7. lépés: A virtuális gépek értékelése**: A függőségek ellenőrzése, a virtuális gépek csoportosítása és az értékelés futtatása. Az elkészült értékelés elemzése a migrálás előkészítése érdekében.


## <a name="step-1-prepare-azure"></a>1. lépés: Az Azure előkészítése

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

- Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.
- Ha meglévő előfizetést használ, és nem Ön a rendszergazda, kérjen a rendszergazdától tulajdonosi vagy közreműködői jogosultságot az előfizetéshez vagy a forgatókönyvben használt erőforráscsoporthoz.


## <a name="step-2-download-and-install-the-dma"></a>2. lépés: A DMA letöltése és telepítése

1. A DMA a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=53595) tölthető le.
    - A DMA bármely gépen telepíthető, amely csatlakozni tud az SQL-példányhoz. Nem szükséges az SQL Server-gépen futtatnia.
    - Ne futtassa az SQL Server-gazdagépen.
2. A telepítés elindításához kattintson duplán a letöltött telepítőfájlra (DownloadMigrationAssistant.msi).
3. A **Befejezés** lapon ellenőrizze, hogy a **Launch Microsoft Data Migration Assistant** (Microsoft Data Migration Assistant indítása) beállítás ki van-e választva, majd kattintson a **Finish** (Befejezés) elemre.

## <a name="step-3-run-and-analyze-the-database-assessment"></a>3. lépés: Az adatbázis-értékelés futtatása és elemzése

Futtassa a forrás SQL Server-példány adott célra vonatkozó értékelését.

1. Az **Új** területen válassza az **Értékelés** lehetőséget, és adjon egy projektnevet a kiértékelésnek.
2. A **Forráskiszolgáló típusa** mezőben válassza az **SQL Server** lehetőséget. A **Célkiszolgáló típusa** mezőben válassza az **SQL Server az Azure Virtual Machines szolgáltatásban** lehetőséget. 

    ![Forrás kiválasztása](./media/migrate-scenarios-assessment/dma-assessment-1.png)

    > [!NOTE]
      A DMA jelenleg nem támogatja a felügyelt SQL-példányokra való migrálások értékelését. Kerülő megoldásként az értékeléshez egy Azure-beli virtuális gépen futó SQL Servert használunk feltételezett célként.

1.  A **Cél verziójának kiválasztása** mezőben adja meg az Azure-ban futtatni kívánt SQL Server célverzióját, és hogy mit szeretne felderíteni az értékelésben:
    - A **Kompatibilitási problémák** az olyan változásokat mutatja meg, amelyek miatt meghiúsulhat a migrálás, illetve amelyek miatt kisebb módosítások szükségesek a migrálás előtt. Emellett bemutatja, ha valamelyik aktuálisan használt szolgáltatás már elavult. A problémák kompatibilitási szint szerint vannak rendezve. 
    - Az **Új szolgáltatásokra vonatkozó javaslat** az SQL Server célplatformon elérhető új szolgáltatásokról nyújt tájékoztatást, amelyeket a migrálás után használhat az adatbázissal. Ezek teljesítmény, biztonság és tárterület alapján vannak rendszerezve. 

    ![Cél kiválasztása](./media/migrate-scenarios-assessment/dma-assessment-2.png)

2. A **Kapcsolódás kiszolgálóhoz** panelen adja meg az SQL Server-példányt futtató gép nevét, a hitelesítés típusát és a kapcsolat részleteit. Ezután kattintson a **Csatlakozás** gombra.

    ![Cél kiválasztása](./media/migrate-scenarios-assessment/dma-assessment-3.png)
    
3. A **Forrás hozzáadása** panelen válassza ki az értékelni kívánt adatbázist, majd kattintson a **Hozzáadás** gombra.
4. A rendszer létrehoz egy értékelést a megadott néven.

    ![Értékelés létrehozása](./media/migrate-scenarios-assessment/dma-assessment-4.png)

5.  Kattintson a **Tovább** gombra az értékelés indításához.
6. Az **Eredmények áttekintése** panelen tekintheti meg az engedélyezett értékelési tesztek eredményeit.


### <a name="analyze-the-database-assessment"></a>Az adatbázis-értékelés elemzése

Az eredmények azonnal megjelennek az asszisztens felületén, amint elérhetők. 

1. A **Kompatibilitási problémák** jelentésben ellenőrizheti, hogy az adatbázisnak vannak-e problémái az egyes kompatibilitási szinteken, és ha igen, hogyan javíthatók. A kompatibilitási szintek a következőképpen feleltethetők meg az SQL Server-verzióknak:
    - 100: SQL Server 2008/Azure SQL Database
    - 110: SQL Server 2012/Azure SQL Database
    - 120: SQL Server 2014/Azure SQL Database
    - 130: SQL Server 2016/Azure SQL Database
    - 140: SQL Server 2017/Azure SQL Database

    ![Kompatibilitási problémák](./media/migrate-scenarios-assessment/dma-assessment-5.png)

2. A **Szolgáltatásjavaslatok** jelentésben megtekintheti azokat a teljesítménnyel, biztonsággal és tárolással kapcsolatos szolgáltatásokat, amelyeket az értékelés szerint konfigurálnia kell a migrálás után. Számos szolgáltatás, többek között a memóriabeli OLTP és oszloptár, a Stretch Database, az Always Encrypted, a dinamikus adatmaszkolás és a transzparens adattitkosítás használata ajánlott.

    ![Szolgáltatási javaslatok](./media/migrate-scenarios-assessment/dma-assessment-6.png)

3. Ha elhárított egyes problémákat, kattintson az **Értékelés újrakezdése** elemre az értékelés ismételt futtatásához. 
4. Kattintson a **Jelentés exportálása** elemre az értékelési jelentés JSON vagy CSV formátumban való lekéréséhez.

Ha nagyobb léptékű értékelést végez:

- Egyszerre több értékelést is futtathat, és az értékelések állapotát az **Összes értékelés** lapon tekintheti meg.
- Az [értékelések egyesíthetők egy SQL Server-adatbázisban](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
- Az [értékelések egyesíthetők egy PowerBI-jelentésben](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).


## <a name="step-4-prepare-for-vm-assessment-with-azure-migrate"></a>4. lépés: Felkészülés a virtuális gépek Azure Migrate-tel történő értékelésére

Hozzon létre egy VMware-fiókot, amellyel az Azure Migrate automatikusan felderíti az értékelendő virtuális gépeket. Ellenőrizze, hogy rendelkezik-e a virtuális gépek létrehozásához szükséges jogosultsággal. Jegyezze fel a portokat, amelyeknek nyitva kell lenniük, és állítsa be a statisztikai beállítások szintjét.

### <a name="set-up-a-vmware-account"></a>VMware-fiók beállítása

 Szüksége lesz egy csak olvasható fiókra a vCenterben. Ha még nem rendelkezik ilyennel, hozzon létre egy VMware-fiókot az alábbi tulajdonságokkal:

- Felhasználó típusa: Egy legalább olvasási jogosultsággal rendelkező felhasználó.
- Engedélyek: Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható.
- Részletek: A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.
- A hozzáférés korlátozásához rendelje a **Gyermekobjektumba propagálás** objektummal rendelkező **Nincs hozzáférés** szerepkört a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).

### <a name="verify-permissions-to-create-a-vm"></a>A virtuális gép létrehozásához szükséges engedélyek ellenőrzése

Ellenőrizze, hogy rendelkezik-e szükséges jogosultsággal a virtuális gépek .OVA formátumú fájl importálásával történő létrehozásához. [További információk](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Portok ellenőrzése

Ebben a forgatókönyvben függőségi leképezést fogunk konfigurálni. Ehhez a szolgáltatáshoz telepítenie kell egy ügynököt az értékelni kívánt virtuális gépre. Az ügynöknek minden virtuális gép 443-as TCP-portjáról tudnia kell csatlakozni az Azure-hoz. [További információ](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid) a kapcsolódási követelményekről.


### <a name="set-statistics-settings"></a>Statisztikai beállítások megadása

Az üzembe helyezés megkezdése előtt a vCenter Server statisztikai beállításait a 3. szintre kell konfigurálni. Vegye figyelembe:
- A szint beállítása után legalább egy napot várnia kell az értékelés futtatása előtt. Máskülönben előfordulhat, hogy az értékelés nem a vártnak megfelelően fog működni.
- Ha a szint nagyobb mint 3, az értékelés működik, de:
    - A rendszer nem gyűjti majd a lemezek és a hálózat teljesítményadatait.
    - A tárolás tekintetében az Azure Migrate egy standard lemez létrehozását javasolja az Azure-ban, amelynek a mérete megegyezik a helyszíni lemezével.
    - A hálózatkezelés tekintetében a rendszer azt javasolja, hogy minden helyszíni hálózati adapterhez hozzon létre egy Azure-beli hálózati adaptert.
    - A számítási kapacitás tekintetében az Azure Migrate áttekinti a VM-magokat és a memória méretét, és egy azonos konfigurációjú Azure-beli virtuális gép létrehozását javasolja. Ha több lehetséges Azure-beli virtuálisgép-méret létezik, a rendszer a legalacsonyabb költségűt ajánlja.
   
    
[További információ](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing) a 3. szintű méretezésről.

A szintet a következők szerint állítsa be:

1. A vSphere webes ügyfélben nyissa meg a vCenter-kiszolgálópéldányt.
2. Válassza a **Kezelés** lapot, majd a **Beállítások** területen kattintson az **Általános** elemre.
3. Kattintson a **Szerkesztés** elemre, majd a **Statisztika** területen állítsa a statisztikai szint beállítását a **3. szint** értékre.

    ![vCenter statisztikai szintje](./media/migrate-scenarios-assessment/vcenter-statistics-level.png)



## <a name="step-5-discover-vms"></a>5. lépés: Virtuális gépek felderítése

Hozzon létre egy Azure Migrate-projektet, majd töltse le és telepítse a gyűjtő virtuális gépet. Ezután a gyűjtő futtatásával derítse fel a virtuális gépeket.

### <a name="create-a-project"></a>Projekt létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és kattintson az **Erőforrás létrehozása** elemre.
2. Keresse meg az **Azure Migrate-et**. Válassza ki az **Azure Migrate-et** a keresési eredmények közül, és kattintson a **Létrehozás** elemre.
3. Adja meg a projekt nevét és az Azure-előfizetést.
4. Hozzon létre egy új erőforráscsoportot.
5. Adja meg a projekt helyét, majd kattintson a **Létrehozás** elemre.

    - Azure Migrate-projektet csak az USA középnyugati régiójában és keleti régiójában lehet létrehozni.
    - Bármilyen célhelyre tervezhet migrálást.
    - A projekt helye csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál.

    ![Azure Migrate](./media/migrate-scenarios-assessment/project-1.png)


    

### <a name="download-the-collector-appliance"></a>A gyűjtőberendezés letöltése

Az Azure Migrate létrehoz egy gyűjtőberendezésnek nevezett helyszíni virtuális gépet. A virtuális gép felderíti a helyszíni VMware virtuális gépeket, és az azokkal kapcsolatos metaadatokat továbbítja az Azure Migrate szolgáltatásnak. A gyűjtőberendezés beállításához egy .OVA-fájlt kell letöltenie, majd importálnia a helyszíni vCenter-kiszolgálóra a virtuális gép létrehozásához.

1. Az Azure Migrate projektben kattintson a **Bevezetés** > **Felderítés és értékelés** > **Gépek felderítése** elemre.
2. A **Gépek felderítése** területen kattintson a **Letöltés** gombra az .OVA-fájl letöltéséhez.
3. A **Projekt hitelesítő adatainak másolása** területen másolja ki a projekt azonosítóját és kulcsát. Ezekre a gyűjtő konfigurálásához lesz szüksége.

    ![Az .ova-fájl letöltése](./media/migrate-scenarios-assessment/download-ova.png) 

### <a name="verify-the-collector-appliance"></a>A gyűjtőberendezés ellenőrzése

A telepítése előtt ellenőrizze, hogy az .OVA-fájl biztonságos-e.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot az OVA kivonatának létrehozásához:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. A létrehozott kivonatnak egyeznie kell ezekkel a beállításokkal (1.0.9.7-es verzió)
    
    **Algoritmus** | **Kivonat értéke**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c
    

### <a name="create-the-collector-appliance"></a>A gyűjtőberendezés létrehozása

Importálja a letöltött fájlt a vCenter Serverre.

1. A vSphere Client-konzolon kattintson a **Fájl** > **OVF-sablon telepítése** elemre.

    ![Az OVF telepítése](./media/migrate-scenarios-assessment/vcenter-wizard.png) 

2. Az OVF-sablon telepítése varázsló **Source** (Forrás) lapján adja meg az .ova fájl helyét, majd kattintson a **Next** (Tovább) gombra.
3. Az **OVF Template Details** (OVF-sablon részletei) területen kattintson a **Next** (Tovább) gombra. Az **End User License Agreement** (Végfelhasználói licencszerződés) területen kattintson az **Accept** (Elfogadás) gombra a szerződés elfogadásához, majd kattintson a **Next** (Tovább) gombra.
4. A **Name and Location** (Név és hely) területen adjon meg egy rövid nevet a gyűjtő virtuális gépnek, valamint egy leltárhelyet, ahol a virtuális gép futni fog, majd kattintson a **Next** (Tovább) gombra. Adja meg a gazdagépet vagy fürtöt, amelyen a gyűjtő berendezés futni fog.
5. A **Storage** (Tárterület) mezőben adja meg, hogy hol szeretné tárolni a berendezés fájljait, majd kattintson a **Next** (Tovább) gombra.
6. A **Disk Format** (Lemezformátum) mezőben adja meg, hogyan kívánja kiosztani a tárhelyet.
7. A **Network Mapping** (Hálózatleképezés) mezőben adja meg a hálózatot, amelyhez a gyűjtő virtuális gép kapcsolódni fog. A hálózatnak internetkapcsolattal kell rendelkeznie a metaadatok az Azure-ba küldéséhez. 
8. A **Ready to Complete** (Befejezésre kész) területen tekintse át a beállításokat, jelölje be a **Power on after deployment** (Bekapcsolás az üzembe helyezés után) beállítást, majd kattintson a **Finish** (Befejezés) gombra.

A berendezés létrehozása után megjelenik egy üzenet, amely visszaigazolja a sikeres létrehozást.

### <a name="run-the-collector-to-discover-vms"></a>A gyűjtő futtatása a virtuális gépek felderítéséhez

Mielőtt hozzálátna, vegye figyelembe, hogy az adatgyűjtő jelenleg csak az „Angol (Egyesült Államok)” nyelvet támogatja az operációs rendszer és az adatgyűjtő felület nyelveként.

1. A vSphere Client-konzolon kattintson a jobb gombbal a virtuális gépre, majd kattintson az **Open Console** (Konzol megnyitása) elemre.
2. Adja meg a berendezés nyelv-, időzóna- és jelszóbeállításait.
3. Az asztalon kattintson a **Gyűjtő futtatása** parancsikonra.

    ![Gyűjtő parancsikonja](./media/migrate-scenarios-assessment/collector-shortcut.png) 
    
4. Az Azure Migrate Collectorban nyissa meg a **Set up prerequisites** (Előfeltételek megadása) felületet.
    - Fogadja el a licencfeltételeket, és olvassa el a külső szolgáltatóval kapcsolatos információkat.
    - A gyűjtő ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel, hogy az idő szinkronizálva van-e, valamint hogy fut-e a gyűjtőszolgáltatás (ez alapértelmezés szerint telepítve van a virtuális gépen). Továbbá a VMware PowerCLI-t is telepíti. 
    
    > [!NOTE]
    > Feltételezzük, hogy a virtuális gép közvetlen, proxy nélkül internet-hozzáféréssel rendelkezik.

    ![Előfeltételek ellenőrzése](./media/migrate-scenarios-assessment/collector-verify-prereqs.png)
    

5. A **Specify vCenter Server details** (vCenter Server adatainak megadása) területen tegye a következőket:
    - Adja meg a vCenter-kiszolgáló nevét (FQDN, teljes tartománynév) vagy IP-címét.
    - A **Username** (Felhasználónév) és a **Password** (Jelszó) mezőben adja meg a csak olvasási jogokkal rendelkező fiók hitelesítő adatait, amelyet a gyűjtő a virtuális gépek felderítéséhez használ majd a vCenter-kiszolgálón.
    - A **Select scope** (Hatókör kiválasztása) mezőben válassza ki a virtuális gépek felderítésének hatókörét. A gyűjtő csak a megadott hatókörön belül deríti fel a virtuális gépeket. A hatókör egy adott mappára, adatközpontra vagy fürtre állítható be. Nem tartalmazhat 1500-nál több virtuális gépet. 

    ![Csatlakozás a vCenterhez](./media/migrate-scenarios-assessment/collector-connect-vcenter.png)

6. A **Specify migration project** (Migrálási projekt megadása) területen adja meg az Azure Migrate projekt a portálról kimásolt azonosítóját és kulcsát. Ha nem másolta ki őket, nyissa meg az Azure Portalt a gyűjtő virtuális gépről. A projekt **Áttekintés** lapján kattintson a **Gépek felderítése** elemre, és másolja ki az értékeket.  

    ![Csatlakozás az Azure szolgáltatáshoz](./media/migrate-scenarios-assessment/collector-connect-azure.png)

7. A **View collection progress** (Gyűjtési folyamat megtekintése) területen monitorozhatja a felderítési folyamatot, és győződhet meg róla, hogy a virtuális gépekről gyűjtött metaadatok a hatókörbe tartoznak-e. Az adatgyűjtő mutatja a felderítés hozzávetőleges időtartamát.

    ![Gyűjtés folyamatban](./media/migrate-scenarios-assessment/collector-collection-process.png)
   


### <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

A gyűjtés befejezése után ellenőrizze, hogy a virtuális gépek megjelennek-e a portálon.

1. Az Azure Migrate-projektben kattintson a **Manage** > **Machines** (Felügyelet > Gépek) elemre.
2. Ellenőrizze, hogy a felderíteni kívánt virtuális gépek megjelennek-e a lapon.

    ![Felderített gépek](./media/migrate-scenarios-assessment/discovery-complete.png)

3. Vegye figyelembe, hogy az Azure Migrate-ügynök jelenleg nincs telepítve a gépeken. Ezért telepítenünk kell, ha meg szeretnénk tekinteni a függőségeket.
    
    ![Felderített gépek](./media/migrate-scenarios-assessment/machines-no-agent.png)



## <a name="step-6-prepare-for-dependency-analysis"></a>6. lépés: Felkészülés a függőségelemzésre

Az értékelni kívánt virtuális gépek közötti függőségek megtekintéséhez ügynököket töltünk le és telepítünk a webalkalmazás virtuális gépeire (WEBVM és SQLVM).

### <a name="take-a-snapshot"></a>Pillanatkép készítése

Ha a módosítás előtt szeretne másolatot készíteni a virtuális gépről, készítsen egy pillanatképet az ügynökök telepítése előtt.

![Gép pillanatképe](./media/migrate-scenarios-assessment/snapshot-vm.png) 


### <a name="download-and-install-the-vm-agents"></a>A virtuálisgép-ügynökök letöltése és telepítése

1.  Az Azure Migrate-projekt **Gépek** lapján válassza ki a gépet, és kattintson a **Telepítés szükséges** elemre a **Függőségek** oszlopban.
2.  A **Gépek felderítése** lapon mindegyik virtuális gépre töltse le és telepítse a Microsoft Monitoring Agentet (MMA), valamint a függőségi ügynököt.
3.  Másolja ki a munkaterület-azonosítót és -kulcsot. Ezekre az MMA telepítése során lesz szüksége.

    ![Ügynök letöltése](./media/migrate-scenarios-assessment/download-agents.png) 



#### <a name="install-the-mma"></a>Az MMA telepítése

1. Kattintson duplán a letöltött ügynökre.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra. A **Licencfeltételek** oldalon kattintson az **Elfogadom** gombra a feltételek elfogadásához.
3. A **Célmappa** mezőben tartsa meg az alapértelmezett telepítési mappát, és kattintson a **Tovább** gombra. 
4. **Az ügynök telepítésének beállításai** területen válassza **Az ügynök csatlakoztatása az Azure Log Analyticshez** > **Tovább** lehetőséget. 

    ![Az MMA telepítése](./media/migrate-scenarios-assessment/mma-install.png) 
5. Az **Azure Log Analyticsben** illessze be a munkaterület a portálról kimásolt azonosítóját és kulcsát. Kattintson a **Tovább** gombra.
    ![Az MMA telepítése](./media/migrate-scenarios-assessment/mma-install2.png) 

6. A **Telepítésre kész** területen telepítse az MMA ügynököt.



#### <a name="install-the-dependency-agent"></a>A függőségi ügynök telepítése

1.  Kattintson duplán a letöltött függőségi ügynökre.
2.  A **Licencfeltételek** oldalon kattintson az **Elfogadom** gombra a feltételek elfogadásához.
3.  A **Telepítés** oldalon várja meg, amíg a telepítés befejeződik. Ezután kattintson a **Next** (Tovább) gombra.

    ![Függőségi ügynök](./media/migrate-scenarios-assessment/dependency-agent.png) 


       
## <a name="step-7-run-and-analyze-the-vm-assessment"></a>7. lépés: A virtuálisgép-kiértékelés futtatása és elemzése

Ellenőrizze a gépek függőségeit, és hozzon létre egy csoportot. Ezután futtassa az értékelést.

### <a name="verify-dependencies-and-create-a-group"></a>Ellenőrizze a függőségeket, és hozzon létre egy csoportot.

1.  A **Gépek** oldalon az elemezni kívánt virtuális gépeknél kattintson a **Függőségek megtekintése** elemre.

    ![Gépek függőségeinek megtekintése](./media/migrate-scenarios-assessment/view-machine-dependencies.png) 

2. Az SQLVM esetében a függőségi térkép a következő részleteket tartalmazza:

    - Az SQLVM-en futó, aktív hálózati kapcsolatokkal rendelkező folyamatcsoportok/folyamatok a megadott időszakban (alapértelmezés szerint egy óra)
    - Az összes függő gép bejövő (ügyfél) és kimenő (kiszolgálói) TCP-kapcsolatai.
    - A telepített Azure Migrate-ügynökkel rendelkező függő gépek külön mezőkben jelennek meg
    - A telepített ügynökkel nem rendelkező gépek esetében a port- és IP-címadatok jelennek meg.
    
 3. A telepített ügynökkel (WEBVM) rendelkező gépek esetében kattintson a gép mezőjére a további információk megtekintéséhez, beleértve a teljes tartománynevet, az operációs rendszert és a MAC-címet. 

    ![Csoportos függőségek megtekintése](./media/migrate-scenarios-assessment/sqlvm-dependencies.png)

4. Most válassza ki a csoporthoz hozzáadni kívánt virtuális gépeket (SQLVM és WEBVM).  Ha a kattintás közben a Ctrl billentyűt lenyomva tartja, több virtuális gépet is kijelölhet.
5. Kattintson a **Csoport létrehozása** elemre, és adja meg a nevet (smarthotelapp).

> [!NOTE]
    > A függőségek részletesebb megtekintéséhez terjessze ki az időtartományt. Megadhat egy adott időtartamot vagy kezdő és befejező dátumokat is. 


### <a name="run-an-assessment"></a>Értékelés futtatása


1. A **Csoportok**  lapon nyissa meg a csoportot (smarthotelapp)
2. Kattintson az **Értékelés létrehozása** elemre.

    ![Értékelés létrehozása](./media/migrate-scenarios-assessment/run-vm-assessment.png)

3. Az értékelés a **Kezelés** > **Értékelések** lapon jelenik meg.


### <a name="modify-assessment-settings"></a>Értékelési beállítások módosítása

Ebben az oktatóanyagban az alapértelmezett értékelési beállításokat használtuk, de testre is szabhatók a következőképpen:

1. A migrálási projekt **Értékelések** lapján válassza ki az értékelést, és kattintson a **Tulajdonságok szerkesztése** elemre.
2. Módosítsa a tulajdonságokat az alábbi táblázat szerint:

    **Beállítás** | **Részletek** | **Alapértelmezett**
    --- | --- | ---
    **Célhely** | Az Azure-beli hely, ahová migrálni kíván | Nincs alapértelmezett értéke.
    **Tárhely-redundancia** | A tárhely-redundancia azon típusa, amelyet az Azure-beli virtuális gépek a migrálás után használni fognak. | Az alapértelmezett érték a [Helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy-lrs.md). Az Azure Migrate csak a felügyelt lemezeken alapuló értékeléseket támogatja, és a felügyelt lemezek csak az LRS-t támogatják, ezért az LRS a beállítás értéke. 
    **Méretezési feltétel** | Az Azure Migrate által használt feltétel a virtuális gépek Azure-nak megfelelő méretezéséhez. Végezhet *teljesítményalapú* méretezést, vagy méretezheti a virtuális gépeket *helyszíniként* is, a teljesítményelőzmények figyelembe vétele nélkül. | Az alapértelmezett beállítás a teljesítményalapú méretezés.
    **Teljesítményelőzmények** | A virtuális gépek teljesítményének értékelésekor figyelembe veendő időtartam. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel a *teljesítményalapú méretezés*. | Az alapértelmezett érték egy nap.
    **Százalékos kihasználtság** | A teljesítményminta-halmaz megfelelő méretezéshez figyelembe veendő százalékos értéke. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel a *teljesítményalapú méretezés*.  | Az alapértelmezett érték a 95. percentilis.
    **Tarifacsomag** | Megadhatja a cél Azure-beli virtuális gépek [tarifacsomagját (alapszintű/standard)](../virtual-machines/windows/sizes-general.md). Például ha azt tervezi, hogy éles környezetet migrál, érdemes a Standard csomagot választani, amely kis késleltetésű virtuális gépeket biztosít, de többe kerülhet. Másrészről ha egy fejlesztői/tesztkörnyezetet használ, érdemes lehet az Alapszintű csomag mellett dönteni, amely nagyobb késleltetésű virtuális gépeket biztosít, alacsonyabb költségek mellett. | Alapértelmezés szerint a rendszer a [Standard](../virtual-machines/windows/sizes-general.md) csomagot használja.
    **Kényelmi faktor** | Az Azure Migrate az értékelés során figyelembe veszi a puffert (kényelmi faktor). Ezt a puffert a rendszer a virtuális gépek gépkihasználtsági adatai (CPU, memória, lemez és hálózat) mellett alkalmazza. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép. | Alapértelmezett beállítás 1.3x.
    **Ajánlat** | Az [Azure-ajánlat](https://azure.microsoft.com/support/legal/offer-details/), amelyre regisztrált. | Az alapértelmezett érték a [használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Pénznem** | A számlázás pénzneme. | Az alapértelmezett érték az amerikai dollár.
    **Kedvezmény (%)** | Az Azure-ajánlaton felül kapott, az előfizetéshez tartozó kedvezmények. | Az alapértelmezett beállítás 0%.
    **Azure Hybrid Benefit** | Adja meg, hogy rendelkezik-e Frissítési Garanciával, és jogosult-e az [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/) igénybevételére. Ha az Igen értéket állítja be, a nem Microsoft Azure-alapú árazás érvényes a windowsos virtuális gépekre. | Az alapértelmezett érték az Igen.

3. Az értékelési beállítások frissítéséhez kattintson a **Mentés** elemre.


### <a name="analyze-the-vm-assessment"></a>A virtuálisgép-kiértékelés elemzése

Az Azure Migrate-értékelések információt nyújtanak a helyszíni virtuális gépek Azure-kompatibilitásáról, az Azure-beli virtuális gépek javasolt méretezéséről, valamint a becsült havi Azure-költségekről. 

![Értékelési jelentés](./media/migrate-scenarios-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

![Értékelés megjelenítése](./media/migrate-scenarios-assessment/assessment-display.png)

Az értékelés kap egy megbízhatósági minősítést, amely 1 csillagtól 5 csillagig terjed (1 csillag a legalacsonyabb, 5 csillag a legmagasabb).
- A megbízhatósági minősítés az értékelések kiszámításához szükséges adatpontok rendelkezésre állása alapján vannak az értékelésekhez rendelve.
- A minősítés segít megbecsülni az Azure Migrate által nyújtott méretjavaslatok megbízhatóságát.
- A megbízhatósági minősítés akkor hasznos, ha *teljesítményalapú méretezést* végez, mert lehet, hogy az Azure Migrate nem rendelkezik elegendő adatponttal a használatalapú méretezéshez. A *helyszíni méretezésnél* a megbízhatósági minősítés mindig 5 csillagos, mert az Azure Migrate rendelkezik a virtuális gép méretezéséhez szükséges összes adatponttal.
- Az elérhető adatpontok százalékától függően van megadva a megbízhatósági minősítés:

   **Az adatpontok rendelkezésre állása** | **Megbízhatósági minősítés**
   --- | ---
   0%–20% | 1 csillag
   21%–40% | 2 csillag
   41%–60% | 3 csillag
   61%–80% | 4 csillag
   81%–100% | 5 csillag

#### <a name="verify-readiness"></a>Felkészültség ellenőrzése

![Készenléti állapot értékelése](./media/migrate-scenarios-assessment/azure-readiness.png)  

Az értékelési jelentés egy táblában összefoglalva jeleníti meg az információkat. Vegye figyelembe, hogy a teljesítményalapú méretezés megjelenítéséhez az Azure Migrate-nek szüksége van a következő adatokra. Ha ezt az információt nem lehet összegyűjteni, az értékelés pontatlan lehet.

- A processzor és a memória kihasználtsági adatai.
- A virtuális géphez csatlakoztatott összes lemez olvasási/írási IOPS-értéke és adatátviteli teljesítménye.
- A virtuális géphez csatlakoztatott összes hálózati adapter bejövő és kimenő hálózati forgalmának adatai.


**Beállítás** | **Jelzés** | **Részletek**
--- | --- | ---
**Azure-beli virtuális gép felkészültsége** | Azt jelzi, hogy a virtuális gép készen áll-e a migrálásra | Lehetséges állapotok:</br><br/>- Készen áll az Azure-beli migrálásra<br/><br/>- Feltételekkel kész <br/><br/>- Nem áll készen az Azure-beli migrálásra<br/><br/>- A felkészültség ismeretlen<br/><br/> Ha a virtuális gép nem áll készen, bemutatunk néhány lehetséges javítási intézkedést.
**Azure-beli virtuális gép mérete** | A készen álló virtuális gépek esetében javaslatot teszünk egy Azure-beli virtuálisgép-méretre. | A méretezési javaslat az értékelési tulajdonságoktól függ:<br/><br/>- Ha teljesítményalapú méretezést használt, a méretezés a virtuális gépek teljesítményelőzményeit veszi figyelembe.<br/><br/>- Ha „helyszíni méretezést” használt, a méretezés a helyszíni virtuális gép méretétől függ, és a kihasználtsági adatokat nem használja fel a rendszer.
**Ajánlott eszköz** | Mivel az ügynökök a mi gépeinken futnak, az Azure Migrate megvizsgálja a gépen futó folyamatokat, és meghatározza, hogy a gép adatbázisgép-e.
**Virtuális gép adatai** | A jelentés a helyszíni virtuális gép beállításait mutatja, beleértve az operációs rendszer, a rendszerindítási típus, a lemez és a tárterület adatait.




#### <a name="review-monthly-cost-estimates"></a>Havi költségbecslések áttekintése

Ez a nézet a virtuális gépeknek az Azure-ban való futtatásával kapcsolatos összes számítási és tárolási költséget mutatja az egyes gépek adataival együtt. 

![Készenléti állapot értékelése](./media/migrate-scenarios-assessment/azure-costs.png) 

- A költségbecslések az egyes gépekre vonatkozó méretjavaslatokon alapulnak.
- A becsült havi számítási és tárolási költségek a csoportban lévő virtuális gépekre összesítve szerepelnek. 


## <a name="conclusion"></a>Összegzés

Ebben a forgatókönyvben:

> [!div class="checklist"]
> * Kiértékeltük a helyszíni adatbázist a DMA eszközzel.
> * Az Azure Migrate szolgáltatással kiértékeltük a helyszíni virtuális gépeket, függőségi leképezés használatával.
> * Áttekintettük az értékeléseket, hogy a helyszíni erőforrásaink biztosan készen álljanak az Azure-ba való migrálásra.

## <a name="next-steps"></a>További lépések

Folytassuk a következő forgatókönyvvel, amelyben a helyszíni virtuális gépeket és az adatbázist [migráljuk átemeléses módszerrel](migrate-scenarios-lift-and-shift.md) az Azure-ba.



