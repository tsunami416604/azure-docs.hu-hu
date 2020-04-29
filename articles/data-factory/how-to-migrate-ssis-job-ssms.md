---
title: Helyszíni SQL Server Integration Services-(SSIS-) feladatok migrálása Azure Data Factory
description: Ez a cikk azt ismerteti, hogyan lehet áttelepíteni SQL Server Integration Services (SSIS) feladatokat Azure Data Factory folyamatokat/tevékenységeket/eseményindítókat SQL Server Management Studio használatával.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 4/7/2020
ms.openlocfilehash: 6e357e98d6c5190c6dfef675dc1ab9cf30a717c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81455087"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>SQL Server Agent feladatok migrálása az ADF-be a SSMS-mel

Helyszíni [SQL Server Integration Services (SSIS) számítási feladatok ADF-ben történő áttelepítésekor](scenario-ssis-migration-overview.md)a SSIS-csomagok áttelepítése után a SQL Server Agent feladatok kötegelt áttelepítését SQL Server Integration Services csomag feladat-lépési típusával Azure Data Factory (ADF) folyamatok/tevékenységek/ütemezett eseményindítók SQL Server Management Studio (SSMS) **SSIS feladat-áttelepítési varázsló**használatával végezheti el.

Általánosságban elmondható, hogy a kiválasztott SQL-ügynök feladatainak esetében a **SSIS feladatok áttelepítése varázsló** a következőket teheti:

- a helyszíni SSIS-csomag helyének leképezése arra a helyre, ahová a csomagok át lesznek telepítve, amelyek az ADF-SSIS által elérhetők.
    > [!NOTE]
    > A fájlrendszer csomagjának helye csak támogatott.
- Telepítse át az alkalmazható feladatokat a megfelelő feladat lépéseivel a kapcsolódó ADF-erőforrásokra az alábbi módon:

|SQL Agent-feladatok objektuma  |ADF-erőforrás  |Megjegyzések|
|---------|---------|---------|
|SQL-ügynök feladata|folyamat     |A folyamat neve a *>\<feladatokhoz lesz létrehozva *. <br> <br> A beépített ügynöki feladatok nem alkalmazhatók: <li> SSIS-kiszolgáló karbantartási feladata <li> syspolicy_purge_history <li> collection_set_ * <li> mdw_purge_data_ * <li> sysutility_ *|
|SSIS-feladatok lépései|SSIS-csomag végrehajtása tevékenység|<li> A tevékenység neve> lesz \<. <li> A feladatütemezés során használt proxy fiók a tevékenység Windows-hitelesítésként lesz áttelepítve. <li> Az áttelepítés során a rendszer figyelmen kívül hagyja a *végrehajtási beállításokat* , kivéve a feladatok lépésben definiált *32-bites futtatókörnyezetet* . <li> Az áttelepítés során a rendszer figyelmen kívül hagyja a feladatok lépésben megadott *ellenőrzést* .|
|schedule      |trigger ütemezése        |Az ütemezett trigger neve lesz *létrehozva az \<ütemterv neve>*. <br> <br> Az SQL Agent feladatütemezés alábbi beállításai figyelmen kívül lesznek hagyva az áttelepítés során: <li> Második szintű intervallum <li> *Automatikus indítás SQL Server Agent indításakor* <li> *Indítás, amikor a processzorok tétlenek lesznek* <li> *hét* *napja és hétvége*<time zone> <br> Az alábbi különbségek az SQL Agent feladatütemezés áttelepítését idézik elő az ADF-ütemező triggerre: <li> Az ADF-ütemterv elindításának későbbi futtatása független az előzmény által aktivált Futtatás végrehajtásának állapotával. <li> Az ADF ütemezése eseményindítójának ismétlődési konfigurációja eltér a napi gyakoriságtól az SQL Agent-feladatokban.|

- Azure Resource Manager-(ARM-) Sablonok előállítása a helyi kimeneti mappában, és közvetlenül vagy később, manuálisan is üzembe helyezhető. További információ az ADF Resource Manager-sablonokról: [Microsoft. DataFactory-erőforrástípusok](https://docs.microsoft.com/azure/templates/microsoft.datafactory/allversions).

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett szolgáltatáshoz SQL Server Management Studio 18,5-es vagy újabb verziójúnak kell lennie. A SSMS legújabb verziójának beszerzéséhez tekintse meg a [SQL Server Management Studio letöltése (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)című témakört.

## <a name="migrate-ssis-jobs-to-adf"></a>SSIS-feladatok migrálása az ADF-be

1. A SSMS Object Explorer válassza a SQL Server Agent lehetőséget, válassza a feladatok lehetőséget, majd kattintson a jobb gombbal, majd válassza **a SSIS-feladatok áttelepíteni az ADF**-be lehetőséget.
![menüben](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Jelentkezzen be az Azure-ba, válassza az Azure-előfizetés, a Data Factory és a Integration Runtime lehetőséget. Az Azure Storage nem kötelező, amely a csomag helyének leképezési lépéseként használatos, ha az áttelepíteni kívánt SSIS feladatok SSIS rendelkeznek.
![menüben](media/how-to-migrate-ssis-job-ssms/step1.png)

1. A SSIS-feladatokban található SSIS-csomagok és konfigurációs fájlok elérési útját képezi le a célhelyre, ahol az áttelepített folyamatok hozzáférhetnek. Ebben a leképezési lépésben a következőket teheti:

    1. Válasszon ki egy forrás mappát, majd **adja hozzá a leképezést**.
    1. A forrás mappa elérési útjának frissítése Az érvényes elérési utak a csomagok mappa elérési útjai vagy a szülőmappa elérési útjai.
    1. A célmappa elérési útjának frissítése Az alapértelmezett érték az 1. lépésben kiválasztott alapértelmezett Storage-fiók relatív elérési útja.
    1. Kijelölt leképezés törlése **törlési leképezéssel**.
![2. lépés](media/how-to-migrate-ssis-job-ssms/step2.png)
![2. lépés-1](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. Válassza ki az áttelepíteni kívánt feladatokat, és konfigurálja a megfelelő *végrehajtott SSIS-csomag tevékenység*beállításait.

    - Alapértelmezett *beállítás*, alapértelmezés szerint az összes kijelölt lépésre vonatkozik. Az egyes tulajdonságokról további információt talál a [SSIS-csomag végrehajtása tevékenység](how-to-invoke-ssis-package-ssis-activity.md) *Beállítások lapján* , ha a csomag helye *fájlrendszer (csomag)*.
    ![3. lépés – 1](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *Lépés beállítása*, beállítás beállítása a kiválasztott lépéshez.
        
        **Alapértelmezett beállítás alkalmazása**: alapértelmezett érték van kiválasztva. Törölje a kijelölést a kiválasztott lépéshez tartozó beállítás megadásához.  
        További információ a többi tulajdonságról: a [SSIS-csomag végrehajtása tevékenység](how-to-invoke-ssis-package-ssis-activity.md) *Beállítások lapja* , ha a csomag helye *fájlrendszer (csomag)*.
    ![3. lépés – 2](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. ARM-sablon előállítása és üzembe helyezése.
    1. Válassza ki vagy adja meg az áttelepített ADF-folyamatok ARM-sablonjainak kimeneti elérési útját. Ha a mappa nem létezik, a rendszer automatikusan létrehozza a mappát.
    2. Válassza ki az **ARM-sablonok üzembe helyezésének lehetőségét az adatai-gyárban**:
        - Az alapértelmezett beállítás nincs kiválasztva. A létrehozott ARM-sablonokat később manuálisan is üzembe helyezheti.
        - Válassza a létrehozott ARM-sablonok közvetlen üzembe helyezése az adatgyárban lehetőséget.
    ![step4](media/how-to-migrate-ssis-job-ssms/step4.png)

1. Telepítse át, majd vizsgálja meg az eredményeket.
![step5](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>További lépések

[Folyamat futtatása és figyelése](how-to-invoke-ssis-package-ssis-activity.md)
