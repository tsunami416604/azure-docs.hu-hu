---
title: SSIS-csomagok végrehajtása a SSDT-ből
description: Ismerje meg, hogyan hajthat végre SSIS-csomagokat az Azure-ban a SSDT-ból.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/31/2019
ms.openlocfilehash: c3163d414e940d843489a34f319996b1b8ed6f4a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86497412"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>SSIS-csomagok végrehajtása az Azure-ban a SSDT-ből

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk a SQL Server Data Tools (SSDT) Azure-kompatibilis SQL Server Integration Services (SSIS) projektjeinek funkcióit ismerteti, amelyek lehetővé teszik csomagok futtatását Azure-SSIS Integration Runtime (IR) Azure Data Factoryban (ADF).  Ezzel a szolgáltatással tesztelheti a meglévő SSIS-csomagokat, mielőtt megemeli & a váltást, vagy áttelepíti azokat az Azure-ba, vagy új SSIS-csomagokat fejleszt ki az Azure-ban való futtatáshoz.

Ezzel a funkcióval létrehozhat egy új Azure-SSIS IR, vagy csatolhat egy meglévőt a SSIS-projektekhez, majd végrehajthatja a csomagokat.  Támogatjuk a SSIS-katalógusba (SSISDB) telepítendő csomagok futtatását a projekt-telepítési modellben, valamint azokat, amelyeket a csomag üzembe helyezési modelljében fájlrendszerekbe/fájlmegosztásba/Azure Files üzembe kell helyezni. 

## <a name="prerequisites"></a>Előfeltételek
A szolgáltatás használatához töltse le és telepítse a legújabb SSDT a Visual studióhoz készült SSIS projects bővítménnyel, [vagy innen](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) önálló telepítőként [.](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)

## <a name="azure-enable-ssis-projects"></a>Azure – SSIS-projektek engedélyezése
### <a name="create-new-azure-enabled-ssis-projects"></a>Új Azure-kompatibilis SSIS-projektek létrehozása
A SSDT-on új Azure-kompatibilis SSIS-projekteket hozhat létre az **Integration Services Project (Azure-enabled)** sablon használatával.

   ![Új Azure-kompatibilis SSIS-projekt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Az Azure-kompatibilis projekt létrehozása után a rendszer felszólítja, hogy Azure Data Factory-ben kapcsolódjon a SSIS.

   ![Azure-SSIS IR-üzenet összekötése](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

Ha azonnal szeretne csatlakozni a Azure-SSIS IRhoz, a részletekért tekintse meg a [Kapcsolódás a Azure-SSIS IRhoz](#irconnect) című témakört. Később is csatlakozhat, ha a jobb gombbal rákattint a SSDT Megoldáskezelő paneljének projekt csomópontjára, és bejelöli a menüt, majd kiválasztja a **Kapcsolódás a SSIS Azure Data Factory** menüpontot a Azure Data Factory almenüben a **SSIS** alatt.

### <a name="azure-enable-existing-ssis-projects"></a>Azure – meglévő SSIS-projektek engedélyezése
A meglévő SSIS-projektek esetében az alábbi lépéseket követve engedélyezheti az Azure-t:

1. Kattintson a jobb gombbal a SSDT Megoldáskezelő paneljének projekt csomópontjára egy menü megnyitásához, majd válassza ki az **Azure-t engedélyező projekt** menüelemet **Azure Data Factory** almenüben az **Azure-kompatibilis projekt varázsló**elindításához.

   ![Azure – meglévő SSIS-projekt engedélyezése](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. A **Visual Studio Configuration (konfiguráció kiválasztása** ) lapon válassza ki a Visual Studio-konfigurációt a csomag-végrehajtási beállítások az Azure-ban való alkalmazásához. Bevált gyakorlat egy új Visual Studio-konfiguráció létrehozása a felhőhöz és az Azure-hoz – a projekt a felhő-konfigurációval való használatának engedélyezése. Több konfiguráció esetén különböző értékeket rendelhet hozzá a paraméterekhez a különböző környezetek (akár helyileg, akár az Azure) alapján. További részletekért tekintse meg [ezt a példát](#example) .

   ![Visual Studio-konfiguráció kiválasztása](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. Azure – a meglévő SSIS-projektek engedélyezése megköveteli, hogy a célkiszolgáló verziószámát a Azure-SSIS IR által támogatott legújabb verzióra állítsa be, amely jelenleg **SQL Server 2017**. Tehát ha még nem tette meg, ellenőrizze, hogy a csomag tartalmaz-e olyan további összetevőket, amelyeket nem támogat a SQL Server 2017, és kattintson a Tovább gombra a folytatáshoz, ha nincs probléma.

   ![A célkiszolgáló verziójának váltása](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. A Azure-SSIS IRhoz való kapcsolódás befejezéséhez tekintse meg a [kapcsolódás Azure-SSIS IRhoz](#irconnect) című témakört.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="irconnect"></a>Azure-kompatibilis projektek összekapcsolása a Azure Data Factory SSIS

Az Azure-kompatibilis projektek ADF-SSIS való csatlakoztatásával feltöltheti a csomagokat Azure Filesba, és futtathatja azokat Azure-SSIS IR. Ehhez kövesse az alábbi lépéseket:

1. Az **ADF bevezetésének SSIS** lapon tekintse át a bevezetőt, és kattintson a **Tovább gombra a** folytatáshoz.

   ![SSIS az ADF-ben – bevezetés](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. Az **SSIS IR kiválasztása az ADF-ben** lapon válassza ki a meglévő ADF-t és Azure-SSIS IR a csomagok futtatásához, vagy hozzon létre újakat, ha nincs.
   - A meglévő Azure-SSIS IR kiválasztásához először válassza ki a megfelelő Azure-előfizetést és az ADF-t.
   - Ha kijelöli a meglévő ADF-t, amelyhez nincs Azure-SSIS IR, kattintson az **SSIS létrehozása** elemre, és hozzon létre egy újat az ADF-portálon/alkalmazáson.
   - Ha olyan meglévő Azure-előfizetést választ ki, amely nem rendelkezik ADF-vel, kattintson az **SSIS létrehozása** gombra a **Integration Runtime létrehozási varázsló**elindításához, ahol megadhatja a helyet és az előtagot úgy, hogy automatikusan létrehozzon egy új Azure-ERŐFORRÁSCSOPORTOT, Data Factory és SSIS az Ön nevében, a következő mintában: **YourPrefix-RG/DF/IR-YourCreationTime**.

   ![Válassza ki a SSIS IR-t az ADF-ben](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. Az **Azure Storage kiválasztása** lapon válassza ki a meglévő Azure Storage-fiókot a csomagok Azure Filesba való feltöltéséhez, vagy hozzon létre egy újat, ha nem rendelkezik ilyennel.
   - A meglévő Azure Storage-fiók kiválasztásához először válassza ki a megfelelő Azure-előfizetést.
   - Ha ugyanazt az Azure-előfizetést választja, mint a Azure-SSIS IR, amely nem rendelkezik Azure Storage-fiókkal, kattintson az **Azure Storage létrehozása** gombra, hogy automatikusan hozzon létre egy újat az Ön nevében ugyanazon a helyen, mint a Azure-SSIS IR Azure-SSIS IR.
   - Ha olyan Azure-előfizetést választ ki, amely nem rendelkezik Azure Storage-fiókkal, kattintson az **Azure Storage létrehozása** gombra, és hozzon létre egy újat a Azure Portal.

   ![Az Azure Storage kiválasztása](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. A kapcsolat befejezéséhez kattintson a **Kapcsolódás** gombra.  A kiválasztott Azure-SSIS IR és az Azure Storage-fiókot a SSDT Megoldáskezelő paneljén található **társított Azure-erőforrások** csomópont alatt jelenítjük meg.  A Azure-SSIS IR állapotát is frissítjük, miközben a jobb gombbal a csomópontra kattintva megnyithatja a menüt, majd kiválaszthatja a **Start\Stop\Manage** menüelemet, amely az ADF-portál vagy-alkalmazás megadására kerül.

## <a name="assess-ssis-projectpackages-for-executions-in-azure"></a>SSIS-project\packages kiértékelése az Azure-beli végrehajtásokhoz
### <a name="assess-ssis-project-or-package"></a>SSIS projekt vagy csomag értékelése
A csomagok Azure-ban való végrehajtása előtt értékelheti a csomagot annak kiértékeléséhez, hogy van-e lehetséges áttelepítési blokkoló vagy információ, amelyről tisztában kell lennie. 
-  A projektben vagy egy csomagban lévő összes csomagot fel lehet mérni.

   ![Projekt- ](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project.png)
    ![ értékelési csomag értékelése](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-package.png)

-  Az értékelési jelentést az egyes értékelési problémák ellenőrzéséhez, valamint az egyes problémák részletes leírását és ajánlását is megtekintheti. Az értékelési jelentést CSV-fájlként is exportálhatja. 

   ![Projekt eredményének felmérése](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project-result.png)

### <a name="suppress-assessment-rule"></a>Értékelési szabály mellőzése
Ha biztos abban, hogy a csomaghoz nem alkalmaztak valamilyen értékelési szabályt, dönthet úgy, hogy letiltja azt. 
-  Közvetlenül rákattinthat az **értékelési szabály letiltásának beállítása** az értékelési jelentésben lehetőségre.

   ![Értékelési szabály letiltási beállításai](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings.png)

-  Az **Azure-kompatibilis beállítások**használatával is konfigurálhatja.

   ![Azure-kompatibilis beállítások](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

   ![Az értékelési szabály letiltásának beállításai az Azure-kompatibilis beállításokon keresztül](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings-via-azure-enabled-settings.png)

## <a name="execute-ssis-packages-in-azure"></a>SSIS-csomagok végrehajtása az Azure-ban
### <a name="azure-enabled-setting"></a>Azure-kompatibilis beállítás
A csomagok Azure-ban való végrehajtása előtt dönthet úgy, hogy a végrehajtási beállításokat konfigurálja. Ha engedélyezni szeretné a Windows-hitelesítést a SSIS-csomagokhoz, kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a SSDT Megoldáskezelő paneljének projekt csomópontjára egy menü felugró ablakára, majd válassza ki az **Azure-kompatibilis beállítások** menüelemet Azure Data Factory almenüben a **SSIS** elemnél.

   ![Azure-kompatibilis beállítások](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. Kattintson a **Windows-hitelesítés engedélyezése** legördülő listára, és válassza az **igaz**lehetőséget. Ezután kattintson a Szerkesztés gombra a **Windows-hitelesítés hitelesítő adatai** beállításnál a hitelesítő adatok megadásához.

   ![Windows-hitelesítés engedélyezése](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. Adja meg a hitelesítő adatokat a **Windows-hitelesítési hitelesítő adatok** szerkesztőjében.

   ![Windows-hitelesítés hitelesítő adatai](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>Csomagok végrehajtásának indítása
Miután csatlakoztatta a projekteket a SSIS-hez az ADF-ben, Azure-SSIS IR csomagokat is végrehajthat.  A csomagok végrehajtásának elindításához két lehetőség közül választhat:
-  Kattintson a **Start** gombra a SSDT eszköztárán a menü legördülő menüjében, majd válassza a **végrehajtás az Azure-ban** menüpontot. 

   ![Végrehajtás az Azure-ban](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Kattintson a jobb gombbal a SSDT Megoldáskezelő paneljének csomag csomópontjára, és válassza ki a kívánt menüt, és válassza ki a **csomag végrehajtása az Azure** menüpontban.

   ![Csomag végrehajtása az Azure-ban](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> A csomagok Azure-ban való végrehajtásához futó Azure-SSIS IR szükséges, így ha a Azure-SSIS IR leáll, megnyílik egy párbeszédpanel, amely elindítja.  Az egyéni telepítési idő kizárása után a folyamatnak 5 percen belül el kell végeznie, de körülbelül 20-30 percet is igénybe vehet, Azure-SSIS IR a virtuális hálózathoz való csatlakozáshoz.  Miután futtatta a csomagokat az Azure-ban, leállíthatja a Azure-SSIS IR a futó költségeket úgy, hogy a jobb gombbal a csomópontra kattint a SSDT Megoldáskezelő paneljén, majd kijelöli a menüt, majd kiválasztja a **Start\Stop\Manage** menüelemet, amely az ADF-portál vagy-alkalmazás megadására kerül.

### <a name="checking-package-execution-logs"></a>Csomag-végrehajtási naplók ellenőrzése
A csomag végrehajtásának megkezdése után formázza és megjeleníti a naplót a SSDT folyamat ablakában.  Hosszú ideig futó csomag esetén a naplót a percek alatt fogjuk frissíteni.  A csomagok végrehajtásának leállításához kattintson a **Leállítás** gombra a SSDT eszköztárán, amely azonnal megszakítja a szolgáltatást.  Az univerzális elnevezési konvenció (UNC) elérési útjában átmenetileg megkeresheti a napló nyers adatait is: `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` , de egy nap elteltével megtisztítjuk.

### <a name="switching-package-protection-level"></a>A csomag védelmi szintjének váltás
A SSIS-csomagok Azure-ban való végrehajtása nem támogatja a **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** -védelmi szinteket.  Ennek következtében, ha a csomagjait ezekkel a beállításokkal konfigurálta, a rendszer átmenetileg a **EncryptSensitiveWithPassword** / **EncryptAllWithPassword**, véletlenszerűen generált jelszavakkal kapcsolja őket, amikor feltölti a csomagokat Azure Filesba a Azure-SSIS IR való végrehajtáshoz.

> [!NOTE]
> Ha a csomagok végrehajtják a **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** védelmi szinttel konfigurált egyéb csomagokat, akkor a csomagok végrehajtása előtt manuálisan kell konfigurálnia ezeket a többi csomagot a **EncryptSensitiveWithPassword**- / **EncryptAllWithPassword**használatára.

Ha a csomagok már konfigurálva vannak a **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** védelmi szintjeivel, változatlanul tartjuk őket, de továbbra is véletlenszerűen generált jelszavakat fognak használni, amikor feltölti a csomagokat Azure Filesba a Azure-SSIS IR végrehajtásához.

### <a name="using-package-configuration-file"></a>Csomag konfigurációs fájljának használata
Ha a csomag konfigurációs fájljait a csomagok központi telepítési modelljében használja a változók értékeinek futtatáskor történő megváltoztatásához, a rendszer automatikusan feltölti ezeket a fájlokat a csomagjaiba Azure Files a Azure-SSIS IR végrehajtásához.

### <a name="using-execute-package-task"></a>Csomag végrehajtása feladat használata
Ha a csomagjai a helyi fájlrendszereken tárolt egyéb csomagokra hivatkozó csomagokat tartalmaznak, a következő további telepítéseket kell végrehajtania:

1. Töltse fel a többi csomagot Azure Files a projektekhez kapcsolódó Azure Storage-fiókba, és szerezzen be új UNC-útvonalat, például:`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Cserélje le a más csomagok elérési útját a Fájlkezelőben a csomag feladatainak új UNC elérési úttal való végrehajtásához.
   - Ha a SSDT-t futtató számítógép nem fér hozzá az új UNC elérési úthoz, módosíthatja a fájl elérési útját a file kapcsolatkezelő tulajdonságok paneljén.
   - Azt is megteheti, hogy egy változót használ a fájl elérési útjához, hogy a megfelelő értéket a futtatáskor rendelje hozzá

Ha a csomagok olyan csomagokat hajtanak végre, amelyek ugyanabban a projektben más csomagokra hivatkoznak, nincs szükség további beállításra.

## <a name="switching-package-execution-environments-with-azure-enabled-projects"></a><a name="example"></a>Csomagok végrehajtási környezetének váltása Azure-kompatibilis projektekkel

Ha az Azure-t támogató projektekkel szeretné váltani a csomag-végrehajtási környezeteket, több Visual Studio-konfigurációt is létrehozhat, amelyekkel különböző értékeket alkalmazhat a környezeti paraméterekhez. Egy egyszerű SSIS-csomag például egy **fájlrendszer-feladattal** rendelkezik, amely beállítja a megadott fájl attribútumait, egyszerűen áttelepítheti a felhőbe a következő lépések végrehajtásával:

1. Definiáljon egy karakterlánc típusú **filepath** , amely a célfájl elérési útja.

   ![Csomag paraméterének meghatározása](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. Társítsa a **forrás-kapcsolatokat** ezzel a paraméterrel. 

   ![A forrásoldali kapcsolatok frissítése](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. Új Visual Studio-konfiguráció létrehozása a felhőhöz a Visual Studio Configuration Managerban.

4. Adja meg a paraméter értékeit minden Visual Studio-konfigurációnál.

   ![Paraméter értékeinek felülbírálása](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Azure – ezt a SSIS-projektet a Visual Studio a felhőhöz való konfigurálásával engedélyezte.

6. A csomag végrehajtása az Azure-ban. A Visual Studio aktuális konfigurációjának váltásával egyszerűen átválthatja a környezetet a helyi környezetre.

   ![A Visual Studio konfigurációjának váltása](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

## <a name="current-limitations"></a>Aktuális korlátozások
-  Ez a SSDT szolgáltatás jelenleg nem támogatja az országos felhőt.

## <a name="next-steps"></a>Következő lépések
Ha elégedett az Azure-beli csomagok az SSDT-ból való futtatásával, telepítheti és futtathatja őket az ADF-folyamatokban végrehajtható SSIS-csomag tevékenységként, lásd: [SSIS-csomagok futtatása az ADF-folyamatokban végrehajtási SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
