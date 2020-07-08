---
title: Csomagok kezelése Azure-SSIS Integration Runtime Package Store-val
description: Ismerje meg, hogyan kezelheti a csomagokat Azure-SSIS Integration Runtime Package Store-ban.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 05/25/2020
ms.openlocfilehash: fa1ab60ae5690bd3782e4c849ca7cb5c29d640c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84199055"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Csomagok kezelése Azure-SSIS Integration Runtime Package Store-val

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A helyszíni SQL Server Integration Services (SSIS) számítási feladatok felhőbe való átváltásához & a Azure-SSIS Integration Runtime (IR) Azure Data Factory (ADF) használatával. További információ: [Azure-SSIS IR létesítése](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure). Az Azure-SSIS IR a következőket támogatja:

- Azure SQL Database kiszolgáló/felügyelt példány által üzemeltetett SSIS-katalógusba (SSISDB) telepített csomagok futtatása (projekt-telepítési modell)
- Az Azure SQL felügyelt példányai által üzemeltetett fájlrendszerbe, Azure Filesba vagy SQL Server adatbázisba (MSDB) telepített csomagok futtatása (csomag-telepítési modell)

A csomag-telepítési modell használata esetén kiválaszthatja, hogy szeretné-e kiépíteni a Azure-SSIS IR olyan csomag-tárolókkal, amelyek csomag-felügyeleti réteget biztosítanak az Azure SQL felügyelt példánya által üzemeltetett fájlrendszer/Azure Files/MSDB felett. A Azure-SSIS IR Package Store lehetővé teszi a csomagok importálását/exportálását, törlését és futtatását, valamint a csomagok futtatásának figyelését/leállítását SQL Server Management Studio (SSMS) használatával, hasonlóan a [régi SSIS-csomag tárolójához](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017). 

## <a name="connect-to-azure-ssis-ir"></a>Kapcsolódás Azure-SSIS IRhoz

Ha a Azure-SSIS IR kiépítve, csatlakozhat hozzá, hogy megkeresse a csomagjait a SSMS.

![Kapcsolódás Azure-SSIS IRhoz](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

A SSMS **Object Explorer** ablakában válassza a **Azure-SSIS Integration Runtime** lehetőséget a **kapcsolat** legördülő menüben. Ezután jelentkezzen be az Azure-ba, és válassza ki a megfelelő előfizetést, az ADF-t és a Azure-SSIS IR, amelyhez a csomagok tárolói vannak csatolva. A Azure-SSIS IR a **futó csomagok** és a **tárolt csomagok** csomópont alatt jelenik meg. Bontsa ki a **tárolt csomagok** csomópontot, és tekintse meg a csomagok tárolóit az alatt. Bontsa ki a csomagok tárolóit, hogy az alatta lévő mappák és csomagok megjelenjenek. Előfordulhat, hogy meg kell adnia a csomagok tárolóinak hozzáférési hitelesítő adatait, ha a SSMS nem tud automatikusan csatlakozni hozzájuk. Ha például kibővít egy MSDB a felső részén, akkor előfordulhat, hogy először csatlakoznia kell a felügyelt Azure SQL-példányhoz.

![Kapcsolódás az Azure SQL felügyelt példányához](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>Mappák és csomagok kezelése

Amikor a Azure-SSIS IR a SSMS-on böngészik, a jobb gombbal bármelyik csomagbeli tárolóra/mappára/csomagra kattintva megnyithatja a menüt, majd kiválaszthatja az **új mappa**, a **csomag importálása**, a **csomag exportálása**, a **Törlés**vagy a **frissítés**lehetőséget.

   ![Mappák és csomagok kezelése](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  Válassza az **új mappa** lehetőséget az importált csomagok új mappájának létrehozásához.

   *  Válassza a **csomag importálása** lehetőséget, hogy csomagokat importáljon a **fájlrendszerből**, **SQL Server** (MSDB) vagy a régi **SSIS-Package áruházból** a csomag tárolójába.

      ![Csomag importálása](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      Attól függően, hogy melyik **csomagot** szeretné importálni, válassza ki a megfelelő **kiszolgálói** / **hitelesítési típust**, adja meg a hozzáférési hitelesítő adatokat, ha szükséges, válassza ki a **csomag elérési útját**, és adja meg az új **csomag nevét**. Csomagok importálásakor a védelmi szintje nem módosítható. A módosításhoz használja a SQL Server Data Tools (SSDT) vagy a `dtutil` parancssori segédprogramot.

   *  Válassza a csomag **exportálása** lehetőséget a csomagok tárolóból való exportálásához a **fájlrendszerbe**, **SQL Server** (MSDB) vagy az örökölt **SSIS-csomag tárolóba**.

      ![Csomag exportálása](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      Az exportálandó **csomag helyétől** függően válassza ki a megfelelő **kiszolgálói** / **hitelesítési típust**, adja meg a hozzáférési hitelesítő adatokat, ha szükséges, majd válassza ki a **csomag elérési útját**. A csomagok exportálásakor, ha titkosítva vannak, adja meg a jelszavakat, hogy először visszafejtse őket, majd módosítsa a védelmi szintet, például hogy ne mentsen bizalmas adatokat, vagy az összes/bizalmas adatokat felhasználói kulccsal/jelszóval titkosítsa.

   *  A **Törlés** lehetőség kiválasztásával törölheti a meglévő mappákat/csomagokat a csomag tárolójából.

   *  Válassza a **frissítés** lehetőséget, hogy megjelenjenek az újonnan hozzáadott mappák/csomagok a Package Store-ban.

## <a name="execute-packages"></a>Csomagok végrehajtása

Ha a Azure-SSIS IR a SSMS böngészi, a jobb gombbal bármelyik tárolt csomagra kattintva megnyithatja a menüt, és kiválaszthatja a **csomag futtatása**lehetőséget.  Ekkor megnyílik a **Csomagfuttató** párbeszédpanel, amelyen beállíthatja, hogy a csomagok végrehajtása a Azure-SSIS IR SSIS-csomag tevékenységének végrehajtásához az ADF-folyamatokban.

![Csomagfuttató lapok 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![Csomagfuttató Pages 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

**Csomagfuttató** párbeszédpanel **általános**, **konfigurációk**, **végrehajtási beállításai**és **naplózási** lapjai megfelelnek a SSIS-csomag végrehajtása tevékenység **Beállítások** lapjának, ahol megadhatja a csomag titkosítási jelszavát, a csomag konfigurációs fájljának hozzáférési adatait, a csomag végrehajtási hitelesítő adatait/tulajdonságait, valamint a naplófájl elérési adatait.  Az **Csomagfuttató** párbeszédablak **set Values (értékek beállítása** ) lapján a SSIS-csomag végrehajtása tevékenységben szereplő **Tulajdonságok felülbírálása lapra kerül** , ahol megadhatja a meglévő csomag tulajdonságait a felülbíráláshoz. További információ: [SSIS-csomagok futtatása az ADF-folyamatokban végrehajtható SSIS-csomag tevékenységként](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

**Csomagfuttató** párbeszédpanel **végrehajtás** gombjának kiválasztásakor a rendszer automatikusan generálja és elindítja az új ADF-folyamatot az SSIS-csomag végrehajtása tevékenységgel. Ha már létezik azonos csomag-végrehajtási beállításokkal rendelkező ADF-folyamat, a rendszer Újrafuttatja, és egy új folyamat nem jön létre. Az ADF-folyamat és a SSIS-csomag végrehajtása tevékenység a neve `Pipeline_SSMS_YourPackageName_HashString` `Activity_SSMS_YourPackageName` , illetve a lesz.

![Csomagfuttató gomb](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![SSIS-csomag végrehajtása tevékenység](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>A csomagok futtatásának figyelése és leállítása

A Azure-SSIS IR SSMS való tallózása során kibonthatja a **futó csomagok** csomópontot, hogy a jelenleg futó csomagok megjelenjenek.  Kattintson a jobb gombbal bármelyikre, és válassza a **Leállítás** vagy a **frissítés**lehetőséget.

   ![A csomagok futtatásának figyelése és leállítása](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  A **Leállítás** gombra kattintva szakítsa meg a csomagot futtató aktuálisan futó ADF-folyamatot a SSIS-csomag végrehajtása tevékenységként.

   *  Válassza a **frissítés** lehetőséget, ha az újonnan futó csomagokat szeretné megjeleníteni a csomagok tárolójában.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>A csomagok Azure-SSIS IRának és szerkesztésének figyelése

Ha a Azure-SSIS IR a SSMS böngészi, kattintson rá a jobb gombbal, és válassza ki a kívánt menüt, és válassza az **ugrás Azure Data Factory portálra vagy a** **frissítés**lehetőségre.

   ![Ugrás az ADF-portálra](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  Válassza az **Ugrás a Azure Data Factory portálra** lehetőséget az ADF-figyelési központ **Integration Runtimes (integrációs** modulok) oldalának megnyitásához, ahol nyomon követheti a Azure-SSIS IR. A **Package Stores (csomagok tárolása** ) csempén láthatja a Azure-SSIS IRhoz csatolt csomagok tárolóinak számát.  Ha kiválasztja ezt a számot, megjelenik egy ablak, amelyen szerkesztheti az ADF-hez társított szolgáltatásokat, amelyek tárolják a csomagokhoz tartozó tárolók hozzáférési információit.

      ![Csomagok tárolóinak szerkesztése](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  Válassza a **frissítés** lehetőséget, hogy megjelenjenek az újonnan hozzáadott mappák/csomagok a csomagban, és csomagokat futtassanak a csomagok tárolói között.

## <a name="next-steps"></a>További lépések

Az automatikus létrehozott ADF-folyamatokat újrafuttathatja vagy szerkesztheti a SSIS-csomag tevékenységeivel, vagy újakat hozhat létre az ADF-portálon. További információ: [SSIS-csomagok futtatása az ADF-folyamatokban végrehajtható SSIS-csomag tevékenységként](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).