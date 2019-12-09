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
ms.openlocfilehash: 5f21623af9b89bbb020063dfb72f7b60e65a6ebe
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927711"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>SSIS-csomagok végrehajtása az Azure-ban a SSDT-ből
Ez a cikk a SQL Server Data Tools (SSDT) Azure-kompatibilis SQL Server Integration Services (SSIS) projektjeinek funkcióit ismerteti, amelyek lehetővé teszik csomagok futtatását Azure-SSIS Integration Runtime (IR) Azure Data Factoryban (ADF).  Ezzel a szolgáltatással tesztelheti a meglévő SSIS-csomagokat, mielőtt megemeli & a váltást, vagy áttelepíti azokat az Azure-ba, vagy új SSIS-csomagokat fejleszt ki az Azure-ban való futtatáshoz.

Ezzel a funkcióval létrehozhat egy új Azure-SSIS IR, vagy csatolhat egy meglévőt a SSIS-projektekhez, majd végrehajthatja a csomagokat.  Támogatjuk a SSIS-katalógusba (SSISDB) telepítendő csomagok futtatását a projekt-telepítési modellben, valamint azokat, amelyeket a csomag üzembe helyezési modelljében fájlrendszerekbe/fájlmegosztásba/Azure Files üzembe kell helyezni. 

## <a name="prerequisites"></a>Előfeltételek
A szolgáltatás használatához töltse le és telepítse a legújabb SSDT a Visual studióhoz készült SSIS projects bővítménnyel, [vagy innen](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) önálló telepítőként [.](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)

## <a name="azure-enable-ssis-projects"></a>Azure – SSIS-projektek engedélyezése
A SSDT-on új Azure-kompatibilis SSIS-projekteket hozhat létre az **Integration Services Project (Azure-enabled)** sablon használatával.

![Új Azure-kompatibilis SSIS-projekt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Azt is megteheti, hogy az Azure-Megoldáskezelő ban engedélyezheti meglévő SSIS-projektjeit úgy, hogy a SSDT paneljének projekt csomópontjára kattint a jobb gombbal, majd kijelöli a menüt, majd kiválasztja az **Azure-t támogató** menüelemet.

![Azure – meglévő SSIS-projekt engedélyezése](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

Azure – a meglévő SSIS-projektek engedélyezése megköveteli, hogy a célkiszolgáló verziószámát a legkésőbbi Azure-SSIS IR által támogatottak legyenek, ami **SQL Server**jelenleg a 2017-as számú, így ha még nem tette meg, akkor egy párbeszédpanel jelenik meg.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Azure-kompatibilis projektek összekapcsolása a Azure Data Factory SSIS
Az Azure-kompatibilis projektek ADF-SSIS való csatlakoztatásával feltöltheti a csomagokat Azure Filesba, és futtathatja azokat Azure-SSIS IR.  Ehhez kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a projektre vagy a **kapcsolódó Azure-erőforrások** csomópontra a SSDT megoldáskezelő paneljén egy menü megnyitásához, és válassza a **Kapcsolódás a következőhöz: Azure Data Factory** menüpontban a **SSIS az ADF-kapcsolat varázslóban**való elindításához.

   ![Kapcsolódás a SSIS az ADF-ben](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. Az **ADF bevezetésének SSIS** lapon tekintse át a bevezetőt, és kattintson a **Tovább gombra a** folytatáshoz.

   ![SSIS az ADF-ben – bevezetés](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. Az **SSIS IR kiválasztása az ADF-ben** lapon válassza ki a meglévő ADF-t és Azure-SSIS IR a csomagok futtatásához, vagy hozzon létre újakat, ha nincs.
   - A meglévő Azure-SSIS IR kiválasztásához először válassza ki a megfelelő Azure-előfizetést és az ADF-t.
   - Ha kijelöli a meglévő ADF-t, amelyhez nincs Azure-SSIS IR, kattintson az **SSIS létrehozása** elemre, és hozzon létre egy újat az ADF-portálon/alkalmazáson.
   - Ha olyan meglévő Azure-előfizetést választ ki, amely nem rendelkezik ADF-vel, kattintson az **SSIS létrehozása** gombra a **Integration Runtime létrehozási varázsló**elindításához, ahol megadhatja a helyet és az előtagot úgy, hogy automatikusan létrehozzon egy új Azure-ERŐFORRÁSCSOPORTOT, Data Factory és SSIS az Ön nevében, a következő mintában: **YourPrefix-RG/DF/IR-YourCreationTime**.
   
   ![Válassza ki a SSIS IR-t az ADF-ben](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. Az **Azure Storage kiválasztása** lapon válassza ki a meglévő Azure Storage-fiókot a csomagok Azure Filesba való feltöltéséhez, vagy hozzon létre egy újat, ha nem rendelkezik ilyennel.
   - A meglévő Azure Storage-fiók kiválasztásához először válassza ki a megfelelő Azure-előfizetést.
   - Ha ugyanazt az Azure-előfizetést választja, mint a Azure-SSIS IR, amely nem rendelkezik Azure Storage-fiókkal, kattintson az **Azure Storage létrehozása** gombra, hogy automatikusan hozzon létre egy újat az Ön nevében ugyanazon a helyen, mint a Azure-SSIS IR Azure-SSIS IR.
   - Ha olyan Azure-előfizetést választ ki, amely nem rendelkezik Azure Storage-fiókkal, kattintson az **Azure Storage létrehozása** gombra, és hozzon létre egy újat a Azure Portal.
   
   ![Az Azure Storage kiválasztása](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. A kapcsolat befejezéséhez kattintson a **Kapcsolódás** gombra.  A kiválasztott Azure-SSIS IR és az Azure Storage-fiókot a SSDT Megoldáskezelő paneljén található **társított Azure-erőforrások** csomópont alatt jelenítjük meg.  A Azure-SSIS IR állapotát is frissítjük, miközben a jobb gombbal a csomópontra kattintva megnyithatja a menüt, majd kiválaszthatja a **Start\Stop\Manage** menüelemet, amely az ADF-portál vagy-alkalmazás megadására kerül.

## <a name="execute-ssis-packages-in-azure"></a>SSIS-csomagok végrehajtása az Azure-ban
### <a name="starting-package-executions"></a>Csomagok végrehajtásának indítása
Miután csatlakoztatta a projekteket a SSIS-hez az ADF-ben, Azure-SSIS IR csomagokat is végrehajthat.  A csomagok végrehajtásának elindításához két lehetőség közül választhat:
-  Kattintson a **Start** gombra a SSDT eszköztárán a menü legördülő menüjében, majd válassza a **végrehajtás az Azure-ban** menüpontot. 

   ![Végrehajtás az Azure-ban](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Kattintson a jobb gombbal a SSDT Megoldáskezelő paneljének csomag csomópontjára, és válassza ki a kívánt menüt, és válassza ki a **csomag végrehajtása az Azure** menüpontban.

   ![Csomag végrehajtása az Azure-ban](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> A csomagok Azure-ban való végrehajtásához futó Azure-SSIS IR szükséges, így ha a Azure-SSIS IR leáll, megnyílik egy párbeszédpanel, amely elindítja.  Az egyéni telepítési idő kizárása után a folyamatnak 5 percen belül el kell végeznie, de körülbelül 20-30 percet is igénybe vehet, Azure-SSIS IR a virtuális hálózathoz való csatlakozáshoz.  Miután futtatta a csomagokat az Azure-ban, leállíthatja a Azure-SSIS IR a futó költségeket úgy, hogy a jobb gombbal a csomópontra kattint a SSDT Megoldáskezelő paneljén, majd kijelöli a menüt, majd kiválasztja a **Start\Stop\Manage** menüelemet, amely az ADF-portál vagy-alkalmazás megadására kerül.

### <a name="checking-package-execution-logs"></a>Csomag-végrehajtási naplók ellenőrzése
A csomag végrehajtásának megkezdése után formázza és megjeleníti a naplót a SSDT folyamat ablakában.  Hosszú ideig futó csomag esetén a naplót a percek alatt fogjuk frissíteni.  A csomagok végrehajtásának leállításához kattintson a **Leállítás** gombra a SSDT eszköztárán, amely azonnal megszakítja a szolgáltatást.  Az univerzális elnevezési konvenció (UNC) elérési útjában átmenetileg megkeresheti a napló nyers adatait is: `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`, de egy nap elteltével megtisztítjuk.

### <a name="switching-package-protection-level"></a>A csomag védelmi szintjének váltás
A SSIS-csomagok Azure-ban való végrehajtása nem támogatja a **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** védelmi szintjét.  Ennek következtében, ha a csomagjait ezekkel a beállításokkal konfigurálta, akkor átmenetileg a rendszer a **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**helyezi át azokat a véletlenszerűen generált jelszavakkal, amikor a csomagokat Azure Filesba tölti fel a Azure-SSIS IRn való végrehajtás céljából.

> [!NOTE]
> Ha a csomagok végrehajtják a **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** -védelmi szinttel konfigurált egyéb csomagokra vonatkozó csomagokat, akkor a csomagok végrehajtása előtt manuálisan kell konfigurálnia ezeket a többi csomagot a **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**használatára.

Ha a csomagok már konfigurálva vannak a **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** védelmi szintjeivel, változatlanul tartjuk őket, de továbbra is véletlenszerűen generált jelszavakat fognak használni, amikor a csomagokat Azure Filesba töltik fel a Azure-SSIS IR.

### <a name="using-package-configuration-file"></a>Csomag konfigurációs fájljának használata
Ha a csomag konfigurációs fájljait a csomagok központi telepítési modelljében használja a változók értékeinek futtatáskor történő megváltoztatásához, a rendszer automatikusan feltölti ezeket a fájlokat a csomagjaiba Azure Files a Azure-SSIS IR végrehajtásához.

### <a name="using-execute-package-task"></a>Csomag végrehajtása feladat használata
Ha a csomagjai a helyi fájlrendszereken tárolt egyéb csomagokra hivatkozó csomagokat tartalmaznak, a következő további telepítéseket kell végrehajtania:

1. Töltse fel a többi csomagot Azure Files a projektekhez kapcsolódó Azure Storage-fiókba, és szerezzen be új UNC-útvonalat, például `\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Cserélje le a más csomagok elérési útját a Fájlkezelőben a csomag feladatainak új UNC elérési úttal való végrehajtásához.
   - Ha a SSDT-t futtató számítógép nem fér hozzá az új UNC elérési úthoz, módosíthatja a fájl elérési útját a file kapcsolatkezelő tulajdonságok paneljén.
   - Azt is megteheti, hogy egy változót használ a fájl elérési útjához, hogy a megfelelő értéket a futtatáskor rendelje hozzá

Ha a csomagok olyan csomagokat hajtanak végre, amelyek ugyanabban a projektben más csomagokra hivatkoznak, nincs szükség további beállításra.

## <a name="next-steps"></a>Következő lépések
Ha elégedett az Azure-beli csomagok az SSDT-ból való futtatásával, telepítheti és futtathatja őket az ADF-folyamatokban végrehajtható SSIS-csomag tevékenységként, lásd: [SSIS-csomagok futtatása az ADF-folyamatokban végrehajtási SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
