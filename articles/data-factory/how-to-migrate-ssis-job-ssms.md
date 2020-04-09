---
title: Helyszíni SQL Server Integration Services (SSIS) feladatok áttelepítése az Azure Data Factoryba
description: Ez a cikk bemutatja, hogyan telepítheti át az SQL Server Integration Services (SSIS) feladatokat az Azure Data Factory folyamatok/tevékenységek/eseményindítók az SQL Server Management Studio használatával.
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
ms.openlocfilehash: c65fbd9bbd83db9c7c8ec0e9041d08372243b72f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887644"
---
# <a name="migrate-on-premises-ssis-jobs-to-azure-data-factory"></a>Helyszíni SSIS-feladatok áttelepítése az Azure Data Factoryba

A [helyszíni SQL Server Integration Services (SSIS) számítási feladatok Nak az ADF-ben lévő SSIS-be történő áttelepítésekor](scenario-ssis-migration-overview.md)az SSIS-csomagok áttelepítése után az SQL Server Agent-feladatok kötegelt áttelepítését teheti meg az SQL Server Integration Services Package feladatlépéstípusú feladattípusával az Azure Data Factory (ADF) folyamatokba/tevékenységekbe/ütemezési aktiválásokba az SQL Server Management Studio (SSMS) **SSIS Feladatáttelepítés varázslóján**keresztül.

Az **SSIS feladatáttelepítési varázsló** általában a megfelelő feladatlépés-típusokkal rendelkező kiválasztott SQL-ügynöki feladatok esetén a következőket teheti:

- a helyszíni SSIS-csomag helyét oda, ahová a csomagokat áttelepítik, és amelyek az SSIS által az ADF-ben elérhetők.
    > [!NOTE]
    > A fájlrendszer csomaghelye csak támogatott.
- a vonatkozó feladatok áttelepítése a megfelelő feladatlépésekkel a megfelelő ADF-erőforrásokra az alábbiak szerint:

|SQL Ügynök feladatobjektum  |ADF erőforrás  |Megjegyzések|
|---------|---------|---------|
|SQL Ügynök feladat|Csővezeték     |A folyamat neve *a feladatnévhez \<>jön létre. * <br> <br> A beépített ügyintézői feladatok nem alkalmazhatók: <li> SSIS-kiszolgáló karbantartási feladata <li> syspolicy_purge_history <li> collection_set_* <li> mdw_purge_data_* <li> sysutility_*|
|SSIS-feladat lépés|SSIS-csomagtevékenység végrehajtása|<li> A tevékenység neve \<a lépés neve lesz>. <li> A feladatlépésben használt proxyfiók átlesz telepítve a tevékenység Windows-hitelesítéseként. <li> *Végrehajtási beállítások,* kivéve *a 32 bites futásidejű* munka a feladat lépésben meghatározott figyelmen kívül hagyja az áttelepítés. <li> *A* feladatlépésben definiált ellenőrzés figyelmen kívül lesz hagyva az áttelepítés során.|
|schedule      |trigger ütemezése        |Az ütemezési eseményindító neve *>. \< * <br> <br> Az SQL Agent feladatütemezés beállításai figyelmen kívül lesznek hagyva az áttelepítés során: <li> Második szintű intervallum. <li> *Automatikus indítás az SQL Server Agent indításakor* <li> *Indítás, amikor a CPU-k tétlenné válnak* <li> *hétköznap* és *hétvégén*<time zone> <br> Az alábbiakban az SQL Agent feladatütemezés ADF-ütemezés-eseményindítóra való áttelepítése utáni különbségeket talál: <li> Az ADF-ütemezési eseményindító későbbi futtatása független az előidézett futtatás végrehajtási állapotától. <li> Az ADF-ütemezési eseményindító ismétlődési konfigurációja eltér az SQL-ügynökfeladat napi gyakoriságától.|

- azure resource manager (ARM) sablonokat a helyi kimeneti mappában, és telepítse az adatgyárba közvetlenül vagy manuálisan. Az ADF Resource Manager-sablonokról a [Microsoft.DataFactory erőforrástípusok](https://docs.microsoft.com/azure/templates/microso.ft.datafactory/allversions)című témakörben olvashat bővebben.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett szolgáltatáshoz az SQL Server Management Studio 18.5-ös vagy újabb verziója szükséges. Az SSMS legújabb verziójának beletöltéséhez olvassa el [az SQL Server Management Studio (SSMS) letöltése című témakört.](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)

## <a name="migrate-ssis-jobs-to-adf"></a>SSIS-feladatok áttelepítése ADF-be

1. Az SSMS Objektumkezelőben válassza az SQL Server Agent elemet, válassza a Feladatok parancsot, majd kattintson a jobb gombbal, és válassza **az SSIS-feladatok áttelepítése ADF-be parancsot.**
![Menü](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Jelentkezzen be az Azure-ba, válassza az Azure-előfizetést, a Data Factory-t és az integrációs futásidőt. Az Azure Storage nem kötelező, amely a csomag helyleképezési lépésében használatos, ha az áttelepítendő SSIS-feladatok SSIS fájlrendszer-csomagokkal rendelkeznek.
![Menü](media/how-to-migrate-ssis-job-ssms/step1.png)

1. Rendelje hozzá az SSIS-csomagok és konfigurációs fájlok elérési útjait azokra a célútvonalakhoz, amelyekhez az áttelepített folyamatok hozzáférhetnek. Ebben a leképezési lépésben a következőket teheti:

    1. Jelöljön ki egy forrásmappát, majd **adja hozzá a leképezést**.
    1. A forrásmappa elérési útjának frissítése. Az érvényes elérési utak a csomagok mappa- vagy szülőmappa-elérési útjai.
    1. A célmappa elérési útjának frissítése. Az alapértelmezett érték az alapértelmezett tárfiók relatív elérési útja, amely az 1.
    1. A kijelölt leképezés törlése a **Leképezés törlése**segítségével.
![2.](media/how-to-migrate-ssis-job-ssms/step2.png)
![lépés2-1](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. Válassza ki az áttelepítendő feladatokat, és adja meg a megfelelő *végrehajtott SSIS-csomagtevékenység beállításait.*

    - *Alapértelmezés szerint*az alapértelmezett beállítás az összes kijelölt lépésre vonatkozik. Az egyes tulajdonokról további információt az [SSIS-csomag végrehajtása tevékenység](how-to-invoke-ssis-package-ssis-activity.md) *Beállítások lapján* talál, ha a csomag helye *Fájlrendszer (Csomag)*.
    ![3-1.](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *Lépésbeállítás*, a kijelölt lépés beállításának konfigurálása.
        
        **Alapértelmezett beállítás alkalmazása:** az alapértelmezett beállítás van kiválasztva. Törölje a jelet, ha csak a kijelölt lépésbeállítását szeretné beállítani.  
        Az egyéb tulajdonságokról további információt az [SSIS-csomag végrehajtása tevékenység](how-to-invoke-ssis-package-ssis-activity.md) Beállítások *lapján* talál, ha a csomag helye *Fájlrendszer (Csomag)*.
    ![3-2. lépés](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. Arm-sablon létrehozása és üzembe helyezése.
    1. Jelölje ki vagy adja meg az áttelepített ADF-folyamatok ARM-sablonjainak kimeneti útvonalát. A mappa automatikusan létrejön, ha nem létezik.
    2. Válassza ki az **ARM-sablonok üzembe helyezése az adatgyárba**lehetőséget:
        - Az alapértelmezett beállítás nincs kijelölve. A létrehozott ARM-sablonokat később manuálisan is telepítheti.
        - Jelölje be, ha a létrehozott ARM-sablonokat közvetlenül az adatgyárba szeretné telepíteni.
    ![4. lépés](media/how-to-migrate-ssis-job-ssms/step4.png)

1. Migráljon, majd ellenőrizze az eredményeket.
![5. lépés](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>További lépések

[Folyamat futtatása és figyelése](how-to-invoke-ssis-package-ssis-activity.md)
