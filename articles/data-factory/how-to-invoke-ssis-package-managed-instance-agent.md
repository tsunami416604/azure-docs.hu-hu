---
title: SSIS-csomagok végrehajtásának ütemezett végrehajtása Azure SQL Database felügyelt példány-ügynök használatával
description: Ismerje meg, hogyan ütemezhet SSIS-csomagok végrehajtását Azure SQL Database felügyelt példány-ügynök használatával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: f230e4d33686b006b20e856d5e8033847e3f3d67
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628486"
---
# <a name="schedule-ssis-package-executions-by-using-azure-sql-database-managed-instance-agent"></a>SSIS-csomagok végrehajtásának ütemezett végrehajtása Azure SQL Database felügyelt példány-ügynök használatával

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk azt ismerteti, hogyan futtathat SQL Server Integration Services (SSIS) csomagot Azure SQL Database felügyelt példány-ügynök használatával. Ez a funkció olyan viselkedést biztosít, amely hasonló ahhoz, hogy a SSIS-csomagokat a helyszíni környezetben SQL Server Agent használatával ütemezze.

Ezzel a szolgáltatással futtathatja a SSISDB-ben tárolt SSIS-csomagokat egy Azure SQL Database felügyelt példányban vagy egy olyan fájlrendszerben, mint például a Azure Files.

## <a name="prerequisites"></a>Előfeltételek
A szolgáltatás használatához [töltse le](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) és telepítse a SQL Server Management Studio (SSMS) legújabb verzióját, amely a 18,5-es verzió.

[Egy Azure-SSIS integrációs](tutorial-create-azure-ssis-runtime-portal.md) modult is létre kell hoznia Azure Data Factory-ben. Egy Azure SQL Database felügyelt példányt használ végpont-kiszolgálóként. 

## <a name="run-an-ssis-package-in-ssisdb"></a>SSIS-csomag futtatása a SSISDB-ben
Ebben az eljárásban Azure SQL Database felügyelt példány-ügynököt használ egy SSISDB-ben tárolt SSIS-csomag meghívásához.

1. A SSMS legújabb verziójában kapcsolódjon Azure SQL Database felügyelt példányhoz.
1. Hozzon létre egy új Agent-feladatot és egy új feladatot. A **SQL Server Agent**alatt kattintson a jobb gombbal a **feladatok** mappára, majd válassza az **új feladat**elemet.

   ![Új ügynök feladatainak létrehozásához kiválasztott beállítások](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Az **új feladatütemezés** lapon válassza ki **SQL Server Integration Services csomagot** típusként.

   ![Új SSIS-feladatok létrehozásának lépései](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. A **csomag** lapon válassza a **SSIS katalógus** lehetőséget a csomag forrása típusaként.
1. Mivel a SSISDB Azure SQL Database felügyelt példányban van, nem kell megadnia a hitelesítést.
1. SSIS-csomagot kell megadnia a SSISDB.

   ![Csomag lap a csomag forrásának kiválasztásával](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

1. A **konfiguráció** lapon a következőket teheti:
  
   - A paraméterek területen állítsa be a **paramétereket**.
   - Felülbírálja az értékeket a **kapcsolatkezelő**alatt.
   - Bírálja felül a tulajdonságot, és válassza a naplózási szintet a **speciális**területen.

   ![Konfiguráció lap a csomag forrás típusának kiválasztásával](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

1. Kattintson az **OK** gombra az ügynök-feladatok konfigurációjának mentéséhez.
1. Indítsa el az ügynök feladatot a SSIS-csomag futtatásához.


## <a name="run-an-ssis-package-in-the-file-system"></a>SSIS-csomag futtatása a fájlrendszerben
Ebben az eljárásban Azure SQL Database felügyelt példány-ügynököt használ a fájlrendszerben tárolt SSIS-csomag futtatásához.

1. A SSMS legújabb verziójában kapcsolódjon Azure SQL Database felügyelt példányhoz.
1. Hozzon létre egy új Agent-feladatot és egy új feladatot. A **SQL Server Agent**alatt kattintson a jobb gombbal a **feladatok** mappára, majd válassza az **új feladat**elemet.

   ![Új ügynök feladatainak létrehozásához kiválasztott beállítások](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Az **új feladatütemezés** lapon válassza ki **SQL Server Integration Services csomagot** típusként.

   ![Új SSIS-feladatok létrehozásának lépései](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. A **csomag** lapon:

   1. A **csomag forrása**területen válassza a **fájlrendszer**lehetőséget.
   
   1. A **forrásfájl típusa**:   

      - Ha a csomag fel van töltve Azure Filesre, válassza az **Azure-fájlmegosztás**lehetőséget.

        ![A forrásfájl típusának beállításai](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)
      
        A csomag elérési **`\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`** útja:.
      
        Az Azure-fájl eléréséhez adja meg az Azure-fiók nevét és a fiók kulcsát a **fájl-hozzáférési hitelesítő adatok**megadása alatt. A tartomány az **Azure**-ban van beállítva.

      - Ha a csomag egy hálózati megosztásra van feltöltve, válassza a **hálózati megosztás**lehetőséget.
      
        A csomag elérési útja a csomagfájl UNC elérési útja a. dtsx kiterjesztéssel.
      
        A hálózati megosztási csomag fájljának eléréséhez adja meg a megfelelő tartományt, felhasználónevet és jelszót.
   1. Ha a csomagfájl jelszóval van titkosítva, válassza a **titkosítási jelszó** lehetőséget, és adja meg a jelszót.
1. Ha a SSIS-csomag futtatásához konfigurációs fájlra van szüksége, a **konfigurációk** lapon adja meg a konfigurációs fájl elérési útját.
   Ha Azure Files tárolja a konfigurációt, annak konfigurációs elérési útja a **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** lesz.
1. A **végrehajtási beállítások** lapon kiválaszthatja, hogy a SSIS-csomag futtatásához **Windows-hitelesítést** vagy **32-bites futtatókörnyezetet** szeretne használni.
1. A **naplózás** lapon kiválaszthatja a naplózási útvonalat és a naplózási hozzáférési hitelesítő adatokat a naplófájlok tárolásához. 
   Alapértelmezés szerint a naplózási útvonal megegyezik a csomag mappájának elérési útjával, és a naplózási hozzáférési hitelesítő adatok megegyeznek a csomag hozzáférési hitelesítő adataival.
   Ha Azure Files tárolja a naplókat, a naplózási útvonala lesz **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`**.
1. Az **értékek beállítása** lapon megadhatja a tulajdonság elérési útját és értékét a csomag tulajdonságainak felülbírálásához.
 
   A felhasználói változó értékének felülbírálásához például adja meg az elérési útját a következő formátumban: **`\Package.Variables[User::<variable name>].Value`**.
1. Kattintson az **OK** gombra az ügynök-feladatok konfigurációjának mentéséhez.
1. Indítsa el az ügynök feladatot a SSIS-csomag futtatásához.


## <a name="cancel-ssis-package-execution"></a>SSIS-csomag végrehajtásának megszakítása
Ha meg szeretné szüntetni a csomagok végrehajtását egy Azure SQL Database felügyelt példány-ügynök feladataiból, hajtsa végre az alábbi lépéseket az ügynök feladat közvetlen leállítása helyett:

1. Keresse meg az SQL Agent **jobId** az **msdb. dbo. sysjobs**címről.
1. Keresse meg a megfelelő SSIS- **executionId** a feladatsor alapján a következő lekérdezés használatával:
   ```sql
   select * from ssisdb.internal.execution_parameter_values_noncatalog where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
   ```
1. Kattintson a jobb gombbal a SSISDB-katalógusra, majd válassza az **aktív műveletek**elemet.

   !["Aktív műveletek" a SSISDB-katalógus helyi menüjében](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

1. Állítsa le a megfelelő műveletet a **executionId**alapján.

## <a name="next-steps"></a>További lépések
Azure Data Factory használatával is ütemezhet SSIS-csomagokat. Részletes útmutatásért lásd: [Azure Data Factory eseményvezérelt eseményindító](how-to-create-event-trigger.md). 
