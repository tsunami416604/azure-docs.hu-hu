---
title: SSIS-csomag futtatása az SSIS-csomag végrehajtása tevékenység – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan futtathat egy SQL Server Integration Services (SSIS) csomag az Azure Data Factory-folyamatot az SSIS-csomag végrehajtása tevékenységek segítségével.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f33259fff17633cc4864a342609f747ebb9902ba
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484855"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Futtassa a Azure Data Factory SSIS-csomag végrehajtása tevékenysége egy SSIS-csomag
Ez a cikk ismerteti, hogyan futtathat egy SQL Server Integration Services (SSIS) csomag folyamatban, az Azure Data Factory (ADF) az SSIS-csomag végrehajtása tevékenységek segítségével. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Hozzon létre egy Azure-SSIS integrációs modul (IR), ha nem rendelkezik a részletes utasításait követve már a [oktatóanyag: Az Azure-SSIS integrációs modul kiépítés](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>A csomag futtatása az Azure Portalon
Ebben a szakaszban használhatja az ADF felhasználói felületének (UI) / alkalmazás létrehozása az ADF, amelyen az SSIS-csomag végrehajtása SSIS-csomag tevékenységgel rendelkező folyamat.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Az SSIS-csomag végrehajtása tevékenységgel rendelkező folyamat létrehozása
Ebben a lépésben a ADF felhasználói felületén vagy alkalmazás-folyamatok létrehozására használhatja. SSIS-csomag végrehajtása tevékenység hozzáadása a folyamathoz, és konfigurálja úgy, hogy az SSIS-csomag futtatása. 

1. A kezdőlapon ADF áttekintése és az Azure Portalon, kattintson a a **létrehozás és Monitorozás** csempére kattintva indítsa el az ADF felhasználói felületén vagy alkalmazás külön lapon. 

   ![Data factory kezdőlap](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Az a **első lépések** kattintson **folyamat létrehozása**: 

   ![Első lépések lap](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. Az a **tevékenységek** eszközkészletben bontsa ki a **általános**, majd áthúzással egy **SSIS-csomag végrehajtása** tevékenységet a folyamat tervezőfelületére. 

   ![A Tervező felületére húzza az SSIS-csomag végrehajtása tevékenység](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Az a **általános** SSIS-csomag végrehajtása tevékenység lapján, adjon meg egy nevet és leírást a tevékenység. Állítsa be a választható időkorlátja, és ismételje meg a értékeket.

   ![Az Általános lapon tulajdonságainak megadása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Az a **beállítások** SSIS-csomag végrehajtása tevékenység fülre, majd válasszon egy Azure-SSIS integrációs modul kívánja futtatni a csomag. Ha a csomag való hozzáférést, Windows-hitelesítést használ, például SQL-kiszolgálók/fájlt oszt meg a helyszínen, az Azure Files, stb., ellenőrizze a **Windows-hitelesítés** jelölőnégyzetet, és adja meg a csomag-végrehajtási hitelesítő adatait (**Tartomány**/**felhasználónév**/**jelszó**). Másik lehetőségként használhatja a titkos kulcsok az Azure Key Vault (AKV) tárolt és azok értékeit. Ehhez kattintson a a **AZURE KEY VAULT** jelölőnégyzetét a megfelelő hitelesítő adatokat, válassza ki és szerkeszteni a meglévő AKV társított szolgáltatás, vagy hozzon létre egy újat, és válassza a hitelesítő adat értékét a titkos kód neve/verziója.  Ön létrehozása/szerkesztése az AKV-beli társított szolgáltatás, is válassza ki és szerkeszteni a meglévő AKV vagy hozzon létre egy új, de kérjük ADF felügyelt identitás való hozzáférést biztosít az AKV Ha Ön még nem tette már. A titkos kulcsokat is megadhat közvetlenül a következő formátumban: `<AKV linked service name>/<secret name>/<secret version>`. Ha a csomag az 32 bites futtatókörnyezet futtatása van szüksége, ellenőrizze a **32 bites futtatókörnyezet** jelölőnégyzetet. 

   A **csomag helye**válassza **SSISDB**, **File System (csomag)** , vagy **File System (projekt)** . Ha **SSISDB** egy csomag helyet, amely automatikusan van kiválasztva, ha az Azure-SSIS integrációs modul lett üzembe helyezve, az SSIS-katalógus (SSISDB) az Azure SQL Database server/Managed Instance működteti, meg kell adnia a csomag futnak, amelyek az üzembe helyezésüket az SSISDB. Ha az Azure-SSIS integrációs modul fut, és a **manuális bejegyzések** jelölőnégyzet nincs bejelölve, keresse meg és válassza ki a meglévő mappákat és projektek/csomagok/környezetek SSISDB. Kattintson a **frissítése** beolvasni az SSISDB, az újonnan hozzáadott mappák/projektek/csomagok/környezetek, így elérhetők tallózása és kiválasztása gombra. 
   
   A **naplózási szint**, válassza ki a csomag végrehajtása naplózásának előre meghatározott köre. Ellenőrizze a **testre szabott** jelölőnégyzetet, ha szeretné inkább adja meg a testre szabott naplózási nevét. 

   ![A beállítások lapon - automatikus tulajdonságainak megadása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Ha az Azure-SSIS integrációs modul helyének nem fut, vagy a **manuális bejegyzések** jelölőnégyzet be van jelölve, megadhatja a csomag és a környezet útvonalak SSISDB közvetlenül a következő formátumban: `<folder name>/<project name>/<package name>.dtsx` és `<folder name>/<environment name>`.

   ![A beállítások lapon - manuális tulajdonságainak beállítása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Ha **File System (csomag)** egy csomag helyet, amely automatikusan van kiválasztva, ha az Azure-SSIS integrációs modul SSISDB nélkül lett üzembe helyezve, meg kell adnia a csomag futtatásához azáltal, hogy egy univerzális elnevezési konvenció () Az alkalmazáscsomag-fájl UNC) szerinti elérési útját (`.dtsx`) az a **alkalmazáscsomag elérési útja**. Például, ha a csomag az Azure Files tárolja, a csomag elérési útvonalát lesz `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`. 
   
   Ha egy külön fájlban adja meg a csomag, meg is kell adnia a konfigurációs fájl egy UNC elérési útja (`.dtsConfig`) az a **konfiguráció elérési útjának**. Például, ha a konfiguráció az Azure Files tárolja, a konfigurációs elérési úttal lesz `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   ![A beállítások lapon - manuális tulajdonságainak beállítása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Ha **File System (projekt)** a csomag helyével, meg kell adnia a csomag futtatásához azáltal, hogy a projekt fájl UNC formátumú elérési útját (`.ispac`) az a **projektútvonalra** és a egy csomagot () fájlt `.dtsx`) a projektjének a **csomagnév**. Például, ha az Azure Files szolgáltatásban tárolja a projektet, a projekt elérési út lesz `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   ![A beállítások lapon - manuális tulajdonságainak beállítása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Ezután meg a hitelesítő adatokat kell megadnia a project-package-konfiguráció fájlok eléréséhez. Az értékek korábban megadott hitelesítő adatait (lásd fent) csomag végrehajtását, ha újból felhasználhatja őket ellenőrzésével a **megegyeznek a csomag végrehajtása hitelesítő adatok** jelölőnégyzetet. Ellenkező esetben meg kell adnia az értékeket a csomag-hozzáférési hitelesítő adatok (**tartomány**/**felhasználónév**/**jelszó**). Például, ha a projekt/csomag/konfiguráció tárolása az Azure Files között a **tartomány** van `Azure`; a **felhasználónév** van `<storage account name>`; és a **jelszó**van `<storage account key>`. Titkos kódok tárolja az AKV értékeik (lásd fent) is használhatja. Ezeket a hitelesítő adatokat a csomag és a gyermek-csomagok végrehajtása csomag feladat, mindezt a saját elérési út/ugyanazon projekt, valamint konfigurációkkal, például a csomagok megadottakkal elérésére használható. 
   
   Ha használta a **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** védelmi szint az SQL Server Data Tools (SSDT) keresztül csomag létrehozásakor meg kell adnia az érték a jelszót a a **titkosítási jelszó**. Másik lehetőségként az AKV alelem értékként (lásd fent) tárolt titkos kulcs is használhatja. Ha használta a **EncryptSensitiveWithUserKey** védelmi szintet, akkor újra meg kell adnia a konfigurációs fájlok vagy a bizalmas értékeit a **SSIS paraméterek** /  **Kezelők**/**tulajdonság felülbírálja** lapok (lásd alább). Ha használta a **EncryptAllWithUserKey** védelmi szintet, azt nem támogatott, ezért újra kell konfigurálni a csomag használatára más védelmi szint az SSDT-n keresztül vagy `dtutil` parancssori segédprogramot. 
   
   A **naplózási szint**, válassza ki a csomag végrehajtása naplózásának előre meghatározott köre. Ellenőrizze a **testre szabott** jelölőnégyzetet, ha szeretné inkább adja meg a testre szabott naplózási nevét. Ha azt szeretné, a csomag végrehajtások túl a standard szintű szolgáltatók adható meg a csomag használatával bejelentkezni, adja meg a naplómappában azáltal, hogy az UNC elérési utat kell a **naplózás elérési út**. Például ha az Azure Files szolgáltatásban tárolja a naplókat, a naplózás elérési út lesz `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. Egy almappát minden egyes csomag futtatásakor ezen az elérési úton létrejön és az SSIS-csomag végrehajtása tevékenység futásának Azonosítóját, amelyben naplófájlokat hoz létre öt percenként elnevezett. 
   
   Végül, emellett a napló mappa eléréséhez hitelesítő adatok megadásához. Ha korábban már megadott értékek a csomag hozzáférési hitelesítő adatok (lásd fent), felhasználhatja őket úgy, a **megegyeznek a hozzáférési hitelesítő adatok csomag** jelölőnégyzetet. Ellenkező esetben meg kell adnia az értékeket a naplózás-hozzáférési hitelesítő adatok (**tartomány**/**felhasználónév**/**jelszó**). Például, ha a naplók tárolása az Azure Files között a **tartomány** van `Azure`; a **felhasználónév** van `<storage account name>`; és a **jelszó** van `<storage account key>`. Titkos kódok tárolja az AKV értékeik (lásd fent) is használhatja. Ezeket a hitelesítő adatokat a naplók tárolására használható. 
   
   Az összes UNC elérési utat a fent említett a teljesen minősített fájlnévnek 260 karakternél rövidebbnek kell lennie, és a könyvtár nevét kell kevesebb 248 karakternél.

5. A a **SSIS paraméterek** lapon SSIS-csomag végrehajtása tevékenységeit, ha az Azure-SSIS integrációs modul fut, **SSISDB** a csomaghely van kiválasztva, és a **manuális bejegyzések** a jelölőnégyzet **beállítások** lapon nincs bejelölve, a meglévő SSIS-paraméterek a kiválasztott projekt/csomagban az SSISDB jelennek meg, hogy az értékek rendelhet hozzájuk. Ellenkező esetben adhatja őket egyenként az értéket rendelni őket manuálisan – ellenőrizze, hogy azok léteznek, és pontosan írja be a csomag végrehajtás sikeres. 
   
   Ha használta a **EncryptSensitiveWithUserKey** védelmi szint az SSDT-csomag létrehozásakor és **File System (csomag)** /**File System (projekt)** van kiválasztva a csomag helyével, akkor is újra meg kell adnia a bizalmas paramétereit, és értéket rendelni azokat a konfigurációs fájlok vagy az ezen a lapon. 
   
   Ha értékeket rendel a paramétereket, a dinamikus tartalom kifejezések, functions, az ADF rendszerváltozók és ADF folyamat paraméterek és változók használatával is hozzáadhat. Titkos kódok tárolja az AKV értékeik (lásd fent) is használhatja.

   ![Az SSIS-paraméterek lapon tulajdonságainak megadása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. A a **kezelők** lapon SSIS-csomag végrehajtása tevékenységeit, ha az Azure-SSIS integrációs modul fut, **SSISDB** a csomaghely van kiválasztva, és a **manuális bejegyzések**jelölőnégyzetet **beállítások** lapon nincs bejelölve, a meglévő kezelők az SSISDB a kiválasztott projekt/csomag jelenik meg az értékeket rendel hozzájuk tartozó tulajdonságok. Ellenkező esetben adhatja őket egyenként, manuálisan értéket rendelni hozzájuk tartozó tulajdonságok – ellenőrizze, hogy azok léteznek, és pontosan írja be a csomag végrehajtása sikeres végrehajtásához az. 
   
   Ha használta a **EncryptSensitiveWithUserKey** védelmi szint az SSDT-csomag létrehozásakor és **File System (csomag)** /**File System (projekt)** van kiválasztva a csomag helyével, akkor is újra meg kell adnia a bizalmas kapcsolat manager – tulajdonságok értéket rendelni azokat a konfigurációs fájlok vagy az ezen a lapon. 
   
   Ha a kapcsolat manager – tulajdonságok értékeket rendel, a dinamikus tartalom kifejezések, functions, az ADF rendszerváltozók és ADF folyamat paraméterek és változók használatával is hozzáadhat. Titkos kódok tárolja az AKV értékeik (lásd fent) is használhatja.

   ![A kezelők lapon tulajdonságainak megadása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. Az a **tulajdonság felülbírálja** lapon SSIS-csomag végrehajtása tevékenységeit, megadhatja az elérési utak a meglévő tulajdonságok a kiválasztott csomag egyesével értéket rendelni őket manuálisan – ellenőrizze, hogy azok léteznek, és megfelelően a csomag végrehajtása sikeres legyen, például a megadott felülbírálás a felhasználói változó értékét, adja meg az elérési út a következő formátumban: `\Package.Variables[User::<variable name>].Value`. 
   
   Ha használta a **EncryptSensitiveWithUserKey** védelmi szint az SSDT-csomag létrehozásakor és **File System (csomag)** /**File System (projekt)** van kiválasztva a csomag helyével, akkor is újra meg kell adnia a bizalmas tulajdonságok értéket rendelni azokat a konfigurációs fájlok vagy az ezen a lapon. 
   
   Ha értékeket rendel a tulajdonságokat, a dinamikus tartalom kifejezések, functions, az ADF rendszerváltozók és ADF folyamat paraméterek és változók használatával is hozzáadhat.

   ![A tulajdonság felülbírálja lapon tulajdonságainak megadása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   A konfigurációs fájlokat és a hozzárendelt értékek a *SSIS paraméterek* lapon bírálható felül a **kezelők**/**tulajdonság felülbírálja** lapok, miközben azok a hozzárendelt a **kezelők** lapon is bírálható felül a **tulajdonság felülbírálja** fülre.

8. A folyamat konfiguráció érvényesítéséhez kattintson **ellenőrzése** az eszköztáron. A **Folyamatérvényesítési jelentés** bezárásához kattintson a **>>** jelre.

9. A folyamat közzétételére az ADF kattintva **összes közzététele** gombra. 

### <a name="run-the-pipeline"></a>A folyamat futtatása
Ebben a lépésben a folyamat futtatásának aktiválása. 

1. Folyamat futásának aktiválásához kattintson **eseményindító** az eszköztáron, majd kattintson a **Aktiválás most**. 

   ![Aktiválás most](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. A **Folyamatfuttatás** ablakban kattintson a **Befejezés** gombra. 

### <a name="monitor-the-pipeline"></a>A folyamat figyelése

1. Váltson a bal oldali **Monitorozás** lapra. Megjelenik a folyamat futtatása és egyéb információk (például a Futtatás kezdő időpont) és annak állapotát. A nézet frissítéséhez kattintson a **Frissítés** parancsra.

   ![Folyamatfuttatások](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Kattintson az **Actions** (Műveletek) oszlopban található **View Activity Runs** (Tevékenységfuttatások megtekintése) hivatkozásra. Láthatja, hogy csak egy tevékenységfuttatás, a folyamat egyetlen tevékenységet (SSIS-csomag végrehajtása tevékenység) tartalmaz.

   ![Tevékenységfuttatások](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Futtathatja a következő **lekérdezés** szemben az SSISDB adatbázis-az Azure SQL Serveren, ellenőrizze, hogy a csomag végrehajtása. 

   ```sql
   select * from catalog.executions
   ```

   ![Csomag végrehajtásának ellenőrzése](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Az SSISDB végrehajtási azonosító beszerzése a folyamatfuttatás tevékenység kimenetét, és az azonosító használatával ellenőrizze a átfogóbb feladatvégrehajtási naplók és hibaüzenetek az SQL Server Management Studio (SSMS) is.

   ![A végrehajtási azonosító lekérése](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Ütemezés a folyamat egy eseményindítóval

Is létrehozhat ütemezett eseményindítóként a folyamathoz, hogy a folyamat fut egy ütemezés szerint (óránként, naponta stb.). Egy vonatkozó példáért lásd: [- adat-előállító létrehozása a Data Factory felhasználói felülete](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>A csomag futtatása a PowerShell-lel
Ebben a szakaszban az Azure PowerShell használatával hozhat létre az ADF-folyamatot az SSIS-csomag végrehajtása tevékenység, amelyen az SSIS-csomag. 

A legújabb Azure PowerShell-modulok telepítése a részletes utasításait követve [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/install-az-ps).

### <a name="create-an-adf-with-azure-ssis-ir"></a>Azure-SSIS integrációs modul létrehozása az ADF használatával
Használhatja egy meglévő ADF, amely már rendelkezik Azure-SSIS integrációs modul üzembe helyezett vagy hozzon létre egy új ADF Azure-SSIS integrációs modul részletes utasításait a [oktatóanyag: SSIS csomagok üzembe helyezése az Azure Powershellen keresztül](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Az SSIS-csomag végrehajtása tevékenységgel rendelkező folyamat létrehozása 
Ebben a lépésben létrehoz egy folyamatot egy SSIS-csomag végrehajtása tevékenységgel. A tevékenység futtatása az SSIS-csomag. 

1. Hozzon létre egy JSON-fájlt **RunSSISPackagePipeline.json** a a **C:\ADF\RunSSISPackage** mappában az alábbi példához hasonló tartalommal:

   > [!IMPORTANT]
   > A fájl mentése előtt cserélje le a kijelölendő objektumok nevét, leírását és az elérési utak tulajdonság és a paraméterértékeket, jelszavakat és más változók értékeinek. 

   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "mySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "myAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyDomain",
                       "userName": "MyUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "**********"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "FolderName/ProjectName/PackageName.dtsx"
                   },
                   "environmentPath": "FolderName/EnvironmentName",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyPipelineParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MySecret"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "userName": {
                               "value": "sa"
                           },
                           "passWord": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "abc"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "userName": {
                               "value": {
                                   "value": "@pipeline().parameters.MyUsername",
                                   "type": "Expression"
                               }
                           },
                           "passWord": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyPassword",
                                   "secretVersion": "3a1b74e361bf4ef4a00e47053b872149"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

2. Az Azure PowerShellben váltson a `C:\ADF\RunSSISPackage` mappát.

3. A folyamat létrehozásához **RunSSISPackagePipeline**futtassa a **Set-AzDataFactoryV2Pipeline** parancsmagot.

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Itt látható a minta kimenete:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>A folyamat futtatása
Használja a **Invoke-AzDataFactoryV2Pipeline** parancsmagot futtathatja a folyamatot. A parancsmag visszaadja a folyamat futásának azonosítóját a későbbi monitorozás céljából.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>A folyamat figyelése

A folyamat futási állapotának folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez futtassa az alábbi PowerShell-szkriptet. Másolja/illessze be az alábbi szkriptet a PowerShell-ablakba, majd nyomja le az Enter billentyűt. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Is figyelemmel kísérheti a folyamat az Azure portal használatával. Lépésenkénti útmutatásért lásd: [a folyamat figyelése](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Ütemezés a folyamat egy eseményindítóval
Az előző lépésben a folyamat igény szerinti futtatta. A folyamatok futtatása ütemezés szerint (óránként, naponta stb.) az ütemezési eseményindító is létrehozhat.

1. Hozzon létre egy JSON-fájlt **MyTrigger.json** a **C:\ADF\RunSSISPackage** mappában az alábbi tartalommal: 

   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
2. A **Azure PowerShell-lel**, váltson át a **C:\ADF\RunSSISPackage** mappát.
3. Futtassa a **Set-AzDataFactoryV2Trigger** parancsmagot, amely az eseményindítót hoz létre. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. Alapértelmezés szerint az eseményindító leállított állapotban van. Az eseményindító elindításához futtassa a **Start-AzDataFactoryV2Trigger** parancsmagot. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. Győződjön meg arról, hogy az eseményindító futtatásával el van-e a **Get-AzDataFactoryV2Trigger** parancsmagot. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. Futtassa a következő parancsot a következő óra elteltével. Például ha az aktuális idő 3:25-kor (UTC), futtassa a parancsot, 4 Órakor (UTC). 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Futtathatja a következő lekérdezés az SSISDB-adatbázison annak ellenőrzéséhez, hogy az Azure SQL Serveren a csomag végrehajtása. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>További lépések
Tekintse meg a következő blogbejegyzésben található:
-   [Végezzen modernizálást és az ETL/ELT munkafolyamatok SSIS tevékenységek az ADF folyamatok kiterjesztése](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
