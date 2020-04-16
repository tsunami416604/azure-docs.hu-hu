---
title: SSIS-csomagok végrehajtása az Azure SQL felügyelt példányügynök által
description: Ismerje meg, hogyan hajthat végre SSIS-csomagokat az Azure SQL felügyelt példányügynök.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: b3b7a25149a9d075c81b30307ade2beb71907637
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394721"
---
# <a name="execute-ssis-packages-by-azure-sql-managed-instance-agent"></a>SSIS-csomagok végrehajtása az Azure SQL felügyelt példányügynök által
Ez a cikk ismerteti, hogyan futtatható az SQL Server Integration Services (SSIS) csomag az Azure SQL felügyelt példányügynök használatával. Ez a szolgáltatás hasonló viselkedést biztosít, mint amikor az SQL Server Agent SSIS-csomagokat ütemezi a helyszíni környezetben.

Ezzel a funkcióval futtathatja az Azure SQL felügyelt példány ssisdb-ben vagy a fájlrendszerben, például az Azure Files-ban tárolt SSIS-csomagokat.

## <a name="prerequisites"></a>Előfeltételek
A szolgáltatás használatához töltse le és telepítse az SSMS legújabb verzióját, amely a 18.5-ös vagy újabb verzió. Töltse le [erről a honlapról](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

És ki kell építenie egy Azure-SSIS-integrációs futásidőt az Azure Data Factoryban, amely az Azure SQL felügyelt példányt használja végpontkiszolgálóként. Ha még nem rendelkezett, hogy már, kiépítés az [oktatóanyag](tutorial-create-azure-ssis-runtime-portal.md)utasításait követve . 

## <a name="run-ssis-packages-in-ssisdb-by-azure-sql-managed-instance-agent"></a>SSIS-csomagok futtatása az SSISDB-ben az Azure SQL felügyelt példányügynök által
Ebben a lépésben az Azure SQL felügyelt példányügynök használatával hívja meg az SSISDB-ben az Azure SQL felügyelt példányban tárolt SSISIS-csomagokmeghívását.
1. Az SSMS legújabb verziójában csatlakozzon az Azure SQL felügyelt példányhoz.
2. Hozzon létre egy új ügynöki feladatot és egy új feladatlépést.

![Új ügynök feladat](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. Az **Új feladatlépés** lapon válassza az **SQL Server Integrációs szolgáltatások csomag** típusát.

![Új SSIS feladat lépés](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. A **Csomag** lapon válassza az **SSIS-katalógust** csomagforrás-típusként.
5. Mivel az SSISDB ugyanabban az Azure SQL felügyelt példányban található, nem kell megadnia a hitelesítést.
6. Adjon meg egy SSIS-csomagot az SSISDB-ből.

![Csomagforrás típusa - SSIS katalógus](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

7. A **Konfigurációk** lapon **paraméterértékeket** adhat meg, felülírhatja az értékeket a **Kapcsolatkezelők**alkalmazásban, felülbírálhatja a **tulajdonságot,** és válassza a **Naplózási szint lehetőséget.**

![Csomagforrás típusa – SSIS-katalógus konfigurációja](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

8. Miután befejezte az összes fenti konfigurációt, kattintson az **OK** gombra az Ügynöki feladat konfigurációjának mentéséhez.
9. Indítsa el az Ügynök feladatot az SSIS-csomag végrehajtásához.


## <a name="run-ssis-packages-in-file-system-by-azure-sql-managed-instance-agent"></a>SSIS-csomagok futtatása a fájlrendszerben az Azure SQL felügyelt példányügynöke által
Ebben a lépésben az Azure SQL felügyelt példányügynök használatával hívja meg a fájlrendszerben futtatandó SSIS-csomagokat.
1. Az SSMS legújabb verziójában csatlakozzon az Azure SQL felügyelt példányhoz.
2. Hozzon létre egy új ügynöki feladatot és egy új feladatlépést.

   ![Új ügynök feladat](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. Az **Új feladatlépés** lapon válassza az **SQL Server Integrációs szolgáltatások csomag** típusát.

   ![Új SSIS feladat lépés](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. A **Csomag** lapon válassza a **Fájlrendszer** csomagforrás-típusként lehetőséget.

   ![Csomagforrás típusa - Fájlrendszer](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

   1. Ha a csomag feltöltve van az Azure File, válassza **az Azure-fájlmegosztás** fájlforrás-típusként.
      - A csomag elérési útja ** \\ <storage account name>\<.file.core.windows.net \<fájlmegosztás neve>csomag neve>.dtsx**
      - Írja be az Azure-fájlfiók nevét és a fiókkulcsot **a Package file access credential** az Azure-fájl eléréséhez. A tartomány **Azure-ként**van beállítva.
   2. Ha a csomagot hálózati megosztásra töltötte fel, válassza **a Hálózati megosztás** fájlforrás-típusként lehetőséget.
      - A csomag elérési útja a csomagfájl **UNC elérési útja** annak dtsx kiterjesztésével.
      - Írja be a megfelelő **tartományt**, **felhasználónevet**és **jelszót** a hálózati megosztási csomagfájl eléréséhez.
   3. Ha a csomagfájl jelszóval van titkosítva, válassza **a Titkosítási jelszót,** és írja be a jelszót.

 5. A **Konfigurációk** lapon írja be a **konfigurációs fájl elérési útját,** ha az SSIS-csomag végrehajtásához konfigurációs fájlra van szüksége.
 6. A **Végrehajtási beállítások** lapon eldöntheti, hogy **windows-hitelesítést** vagy **32 bites futásidejű** t használ-e az SSIS-csomag végrehajtásához.
 7. A Naplózás lapon **kiválaszthatja** a **naplózási elérési utat** és a megfelelő naplózási hozzáférési hitelesítő adatokat a naplófájlok tárolásához. Alapértelmezés szerint a naplózási elérési út megegyezik a csomagmappa elérési útvonalával, és a naplózási hozzáférési hitelesítő adat megegyezik a csomag-hozzáférési hitelesítő adatokkal.
 8. Az **Értékek beállítása** lapon a **Tulajdonság elérési útja** és az **Érték** mezőbe írhatja be a csomag tulajdonságainak felülbírálásához.
 Ha például felül szeretné írni a felhasználói változó értékét, adja meg elérési útját a következő formátumban: **<variable name>\Package.Variables[Felhasználó:: ] Érték**.
 9. Miután befejezte az összes fenti konfigurációt, kattintson az **OK** gombra az Ügynöki feladat konfigurációjának mentéséhez.
 10. Indítsa el az Ügynök feladatot az SSIS-csomag végrehajtásához.


 ## <a name="cancel-ssis-package-execution"></a>SSIS-csomag végrehajtásának megszakítása
 Ha megszakítja a csomag végrehajtását egy Azure SQL felügyelt ügynök feladatból, az alábbi lépéseket kell végrehajtania, ahelyett, hogy közvetlenül leállítana az ügynöki feladat.
 1. Keresse meg az SQL ügynök **jobId-tól** **msdb.dbo.sysjobs**.
 2. Keresse meg a megfelelő **SSIS-végrehajtási azonosítót** a feladatazonosító alapján az alábbi lekérdezés sel:
    ```sql
    select * from ssisdb.internal.execution_parameter_values_noncatalog where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
    ```
 3. Válassza **az Aktív műveletek lehetőséget** az SSIS-katalógusban.

    ![Csomagforrás típusa - Fájlrendszer](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

 4. A megfelelő művelet leállítása **a végrehajtási azonosító**alapján.

## <a name="next-steps"></a>További lépések
 SSIS-csomagok ütemezése az Azure Data Factory használatával is ütemezheti. Részletes útmutatást az Azure [Data Factory eseményindító című témakörben talál.](how-to-create-event-trigger.md) 