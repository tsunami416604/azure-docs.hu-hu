---
title: SSIS-csomagok végrehajtása az SSDT-től
description: Ismerje meg, hogyan hajthat végre SSIS-csomagokat az Azure-ban az SSDT-ből.
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
ms.openlocfilehash: 11e76fea87c60ae2b56cc15d5827be6e1b2b5a01
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399431"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>SSIS-csomagok végrehajtása az Azure-ban az SSDT-től

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk az Azure-kompatibilis SQL Server Integration Services (SSIS) projektek szolgáltatását ismerteti az SQL Server Data Tools (SSDT) szolgáltatásban, amely lehetővé teszi a csomagok futtatását az Azure-SSIS-integrációs futásidőben (IR) az Azure Data Factoryban (ADF).  Ezzel a funkcióval tesztelheti meglévő SSIS-csomagjait, mielőtt felemelne & áthelyezi/áttelepítheti őket az Azure-ba, vagy új SSIS-csomagokat fejleszthet az Azure-ban való futtatásra.

Ezzel a funkcióval létrehozhat egy új Azure-SSIS-ir-t, vagy csatolhat egy meglévőt az SSIS-projektekhez, majd végrehajthatja a csomagokat.  Támogatjuk az SSIS-katalógusba (SSISDB) a Project Deployment Model ben telepítendő csomagok, valamint a fájlrendszerekbe/fájlmegosztásokba/Azure-fájlokba a csomagtelepítési modellben telepítendő csomagok futtatását. 

## <a name="prerequisites"></a>Előfeltételek
A funkció használatához töltse le és telepítse a Visual Studio legújabb SSDT ssis-projektbővítményét, [here](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) vagy [innen](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)önálló telepítőként.

## <a name="azure-enable-ssis-projects"></a>Az Azure-t engedélyező SSIS-projektek
Az SSDT-n új Azure-kompatibilis SSIS-projekteket hozhat létre az **Integrációs szolgáltatások projekt (Azure-kompatibilis)** sablon használatával.

![Új Azure-kompatibilis SSIS-projekt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Azt is megteheti, hogy az Azure-ban engedélyezheti meglévő SSIS-projekteket, ha a jobb gombbal a project csomópontra kattint az SSDT Megoldáskezelő paneljén egy menüben, majd kiválasztja az **Azure-kompatibilis** menüelemet.

![Azure- Meglévő SSIS-projekt engedélyezése](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

A meglévő SSIS-projektek azure-engedélyezése megköveteli, hogy a célkiszolgáló-verziójukat az Azure-SSIS IR által támogatott legújabbverzióra állítsa be, amely jelenleg **az SQL Server 2017,** így ha még nem tette meg, egy párbeszédpanel ablak jelenik meg.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Azure-kompatibilis projektek csatlakoztatása az SSIS-hez az Azure Data Factoryban
Az Azure-kompatibilis projektek ADF-ben ssis-hez való csatlakoztatásával feltöltheti csomagjait az Azure Files szolgáltatásba, és futtathatja őket az Azure-SSIS IR-n.  Ehhez kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a projektre vagy a **Csatolt Azure-erőforrások** csomópontra az SSDT Megoldáskezelő paneljén egy menü megnyitásához, és válassza a Csatlakozás az **SSIS-hez lehetőséget** az Azure Data Factory menüben az SSIS elindításához az **ADF-kapcsolat varázslóban**.

   ![Csatlakozás az SSIS-hez az ADF-ben](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. Az **SSIS in ADF Bevezetés** oldalán tekintse át a bevezetést, és a folytatáshoz kattintson a **Tovább** gombra.

   ![SSIS az ADF-ben Bevezetés](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. Az **SSIS-kapcsolat kiválasztása az ADF-ben** lapon válassza ki a meglévő ADF- és Azure-SSIS-ir-t csomagok futtatásához, vagy hozzon létre újakat, ha nem rendelkezik ilyensel.
   - A meglévő Azure-SSIS-ir kiválasztásához először válassza ki a megfelelő Azure-előfizetést és az ADF-et.
   - Ha olyan meglévő ADF-et választ, amely nem rendelkezik Azure-SSIS IR-vel, kattintson az **SSIS IR létrehozása** gombra, és hozzon létre egy újat az ADF portálon/alkalmazáson.
   - Ha olyan meglévő Azure-előfizetését választja, amely nem rendelkezik ADF-fel, kattintson az **SSIS infravörös létrehozása** gombra az **Integrációs futásidejű létrehozás varázsló**elindításához, ahol megadhatja a helyet és az előtagot, hogy automatikusan hozzunk létre egy új Azure Resource Groupot, Data Factoryt és SSIS IR-t az Ön nevében, amelyet a következő minta nevez meg: **YourPrefix-DF/RG/IR-YourCreationTime**.
   
   ![SSIS-kapcsolat kiválasztása az ADF-ben](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. Az **Azure Storage kiválasztása** lapon válassza ki a meglévő Azure Storage-fiókot csomagok azure files-ba való feltöltéséhez, vagy hozzon létre egy újat, ha nem rendelkezik ilyensel.
   - Meglévő Azure Storage-fiók kiválasztásához először válassza ki a megfelelő Azure-előfizetést.
   - Ha ugyanazt az Azure-előfizetést választja, mint az Azure-SSIS-ir, amely nem rendelkezik Azure Storage-fiókkal, kattintson az **Azure Storage létrehozása** gombra, hogy automatikusan hozzon létre egy újat az Ön nevében ugyanazon a helyen, mint az Azure-SSIS IR, amelyet az Azure-SSIS infravörös nevének előtagésa és a létrehozási dátum kombinálásával nevezhetünk el.
   - Ha olyan másik Azure-előfizetést választ, amely nem rendelkezik Azure Storage-fiókkal, kattintson az **Azure Storage létrehozása** gombra, és hozzon létre egy újat az Azure Portalon.
   
   ![Az Azure Storage kiválasztása](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. A kapcsolat befejezéséhez kattintson a **Csatlakozás** gombra.  A kiválasztott Azure-SSIS IR- és Azure Storage-fiókot az SSDT Megoldáskezelő paneljének **Csatolt Azure-erőforrások** csomópontja alatt jelenítjük meg.  Az Azure-SSIS IR állapotát is frissítjük, miközben a jobb gombbal a csomópontjára kattintva megjelenik egy menü, majd kiválasztja a **Start\Stop\Manage** menüelemet, amely az ADF portálra/alkalmazásra irányítja.

## <a name="execute-ssis-packages-in-azure"></a>SSIS-csomagok végrehajtása az Azure-ban
### <a name="starting-package-executions"></a>Csomagvégrehajtás indítása
Miután csatlakoztatta a projekteket az SSIS-hez az ADF-ben, csomagokat hajthat végre az Azure-SSIS IR-n.  A csomagok végrehajtásának megkezdéséhez két lehetősége van:
-  Kattintson az SSDT eszköztár **Start** gombjára egy menü legördülő menüjéhez, és válassza a Végrehajtás az **Azure-ban** menüelemet 

   ![Végrehajtás az Azure-ban](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Kattintson a jobb gombbal a csomagcsomópontra az SSDT Megoldáskezelő paneljén egy menü megnyitásához, és válassza a Csomag végrehajtása az **Azure-ban** menüelemet.

   ![Csomag végrehajtása az Azure-ban](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> A csomagok Azure-ban történő végrehajtásához egy futó Azure-SSIS IR-re van szükség, így ha az Azure-SSIS IR le áll, egy párbeszédablak jelenik meg az indításhoz.  Az egyéni beállítási idő kizárásával ezt a folyamatot 5 percen belül el kell végezni, de körülbelül 20–30 percet is igénybe vehet, ha az Azure-SSIS infravörös kapcsolata virtuális hálózathoz csatlakozik.  Miután az Azure-ban végrehajtotta a csomagokat, leállíthatja az Azure-SSIS IR-t a működési költség kezeléséhez, ha a jobb gombbal az SSDT Megoldáskezelő paneljében a csomópontjára kattintva megjelenik egy menü, majd kiválasztja a **Start\Stop\Manage** menüelemet, amely az ADF portálra/alkalmazásra irányítja.

### <a name="checking-package-execution-logs"></a>Csomagvégrehajtási naplók ellenőrzése
Amikor elindítja a csomag végrehajtását, formázzuk és megjelenítjük a naplóját az SSDT Progress ablakában.  Egy hosszú ideig futó csomag esetén rendszeresen frissítjük a naplóját a percek szerint.  A csomag végrehajtását az SSDT eszköztár **Stop** gombjára kattintva állíthatja le, amely azonnal megszakítja azt.  Ideiglenesen is megtalálhatja a napló nyers adatait az univerzális `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`elnevezési konvenció (UNC) elérési útján: , de egy nap után megtisztítjuk.

### <a name="switching-package-protection-level"></a>A csomagvédelmi szint váltása
Az SSIS-csomagok Azure-ban történő végrehajtása nem támogatja **az EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** védelmi szinteket.  Következésképpen, ha a csomagok konfigurálva vannak ezekkel, akkor ideiglenesen váltani őket **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**, illetve véletlenszerűen generált jelszavakat, amikor feltöltjük a csomagokat az Azure-Files az Azure-SSIS IR végrehajtására.

> [!NOTE]
> Ha a csomagok olyan csomagműveletek végrehajtása műveleteket tartalmaznak, amelyek az **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** védelmi szintekkel konfigurált más csomagokra hivatkoznak, a csomagok végrehajtása előtt manuálisan újra kell konfigurálnia azokat a többi csomagot az **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**használatához.

Ha a csomagok már konfigurálva **encryptSensitiveWithPassword**/**EncryptAllWithPassword** védelmi szintek, akkor azokat változatlanul, de továbbra is véletlenszerűen generált jelszavakat, amikor feltölti a csomagokat az Azure-Files az Azure-SSIS IR végrehajtásra.

### <a name="using-package-configuration-file"></a>Csomagkonfigurációs fájl használata
Ha a csomagtelepítési modellben a csomagkonfigurációs fájlok at használja a változóértékek futásidőben történő módosításához, akkor automatikusan feltöltjük ezeket a fájlokat a csomagokkal az Azure-Files-ba az Azure-SSIS IR-en való végrehajtáshoz.

### <a name="using-execute-package-task"></a>Csomagvégrehajtás végrehajtása
Ha a csomagok olyan Csomagműveletek végrehajtása műveletet tartalmaznak, amelyek a helyi fájlrendszereken tárolt más csomagokra vonatkoznak, a következő további beállításokat kell végrehajtania:

1. Töltse fel a többi csomagot az Azure Files-ba a projektekhez kapcsolódó Azure Storage-fiókban, és töltse le az új UNC-útvonalat, például.`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. A csomagvégrehajtás végrehajtására vonatkozó feladatok fájlkezelőjében szereplő többi csomag fájlelérési útjának lecserélése az új UNC elérési útjukra
   - Ha az SSDT-t futtató gép nem tudja elérni az új UNC elérési utat, módosíthatja a fájl elérési útját a Fájlcsatlakozáskezelő Tulajdonságok paneljén
   - Másik lehetőségként a fájl elérési útjának változójával a megfelelő értéket rendelheti hozzá futási időben.

Ha a csomagok olyan Csomagműveletek végrehajtása műveleteket tartalmaznak, amelyek ugyanannak a projektnek más csomagjaira vonatkoznak, nincs szükség további beállításokra.

## <a name="next-steps"></a>További lépések
Miután elégedett az SSDT-ről az Azure-ban futó csomagokkal, üzembe helyezheti és futtathatja őket SSIS-csomagtevékenységek végrehajtásaként az ADF-folyamatokban, [lásd: SSIS-csomagok futtatása SSIS-csomagtevékenységek végrehajtása az ADF-folyamatokban.](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
