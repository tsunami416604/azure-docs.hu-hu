---
title: SSIS-csomag futtatása a SSIS-csomag végrehajtása tevékenységgel – Azure | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan futtathat SQL Server Integration Services (SSIS) csomagot Azure Data Factory-folyamatban a SSIS-csomag végrehajtása tevékenység használatával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 09/13/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 9057cefa5108924c57dbc85bbb895b31e804a51c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000642"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>SSIS-csomag futtatása a SSIS-csomag végrehajtása tevékenységgel Azure Data Factory
Ez a cikk azt ismerteti, hogyan futtathat egy SQL Server Integration Services (SSIS) csomagot Azure Data Factory (ADF) folyamatban a SSIS-csomag végrehajtása tevékenység használatával. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha még nem rendelkezik az [oktatóanyag részletes utasításait követve, hozzon létre egy Azure-SSIS Integration Runtime (IR). Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md)kiépítés.

## <a name="run-a-package-in-the-azure-portal"></a>Csomag futtatása a Azure Portal
Ebben a szakaszban az ADF felhasználói felületének (UI)/App használatával hozzon létre egy ADF-folyamatot a SSIS-csomag végrehajtásával, amely futtatja a SSIS-csomagot.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Folyamat létrehozása SSIS-csomag végrehajtása tevékenységgel
Ebben a lépésben az ADF felhasználói felületet/alkalmazást használja egy folyamat létrehozásához. Vegyen fel egy végrehajtási SSIS-csomag tevékenységet a folyamatba, és konfigurálja úgy, hogy futtassa a SSIS-csomagot. 

1. Az ADF áttekintése/kezdőlapján Azure Portalban kattintson a **szerző & monitorozás** csempére az ADF felhasználói felületének/alkalmazásának külön lapon való indításához. 

   ![Data factory kezdőlap](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Az **első lépések** lapon kattintson a **folyamat létrehozása**elemre: 

   ![Első lépések lap](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. A **tevékenységek** eszközkészletben bontsa ki az **általános**elemet, majd húzza a &AMP; eldobása **SSIS-csomag végrehajtása** tevékenységet a folyamat tervező felületére. 

   ![SSIS-csomag végrehajtása tevékenység áthúzása a tervező felületére](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Az SSIS-csomag végrehajtása tevékenység **általános** lapján adja meg a tevékenység nevét és leírását. Adja meg a választható időtúllépést és az újrapróbálkozási értékeket.

   ![Tulajdonságok beállítása az Általános lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. A SSIS-csomag végrehajtása tevékenység **Beállítások** lapján válasszon egy Azure-SSIS IR, ahol futtatni szeretné a csomagot. Ha a csomag Windows-hitelesítést használ az adattárakhoz való hozzáféréshez, például SQL-kiszolgálók/fájlmegosztás a helyszínen, Azure Files stb., jelölje be a **Windows-hitelesítés** jelölőnégyzetet, és adja meg a csomag-végrehajtási hitelesítő adatok (**tartomány** ) értékét. /Felhasználónévjelszava/). Azt is megteheti, hogy a Azure Key Vaultban (AKV) tárolt titkos kulcsokat használja értékként. Ehhez kattintson az **Azure Key Vault** jelölőnégyzetre a megfelelő hitelesítő adatok mellett, válassza ki vagy szerkessze a meglévő AKV társított szolgáltatást, vagy hozzon létre egy újat, majd válassza ki a hitelesítő adatokhoz tartozó titkos nevet vagy verziót.  A AKV társított szolgáltatás létrehozásakor vagy szerkesztésekor kiválaszthatja vagy szerkesztheti a meglévő AKV, vagy létrehozhat egy újat, de ha még nem tette meg, adja meg az ADF által felügyelt identitás-hozzáférést a AKV. A titkos kulcsokat a következő formátumban is megadhatja: `<AKV linked service name>/<secret name>/<secret version>`. Ha a csomag futtatásához 32 bites futtatókörnyezet szükséges, jelölje be a **32 bites futtatókörnyezet** jelölőnégyzetet. 

   A **csomag helye**területen válassza a **SSISDB**, a **fájlrendszer (csomag)** vagy a **fájlrendszer (projekt)** elemet. Ha a **SSISDB** lehetőséget választja, amely automatikusan ki van választva, ha a Azure-SSIS IR a Azure SQL Database kiszolgáló/felügyelt példány által üzemeltetett SSIS Catalog (SSISDB) használatával lett kiépítve, akkor meg kell adnia a futtatni kívánt csomagot üzembe helyezése a SSISDB-ben. Ha a Azure-SSIS IR fut, és a **manuális bejegyzések** jelölőnégyzet nincs bejelölve, a SSISDB megkeresheti és kiválaszthatja a meglévő mappák/projektek/csomagok/környezetek elemet. Kattintson a **frissítés** gombra az újonnan hozzáadott mappák/projektek/csomagok/KÖRNYEZETek SSISDB való beolvasásához, hogy elérhetők legyenek a böngészéshez és a kijelöléshez. A csomagok végrehajtásához használt környezetek tallózásához/kiválasztásához előre be kell állítania a projekteket, hogy a SSISDB alatt található azonos mappák hivatkozásaiként adja hozzá ezeket a környezeteket. További információ: [SSIS-környezetek létrehozása/leképezése](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   A **naplózási szint**beállításnál válassza ki a csomag végrehajtásának előre meghatározott hatókörét. Jelölje be a **testreszabott** jelölőnégyzetet, ha inkább a testreszabott naplózási nevet szeretné megadni. 

   ![Tulajdonságok beállítása a beállítások lapon – automatikus](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Ha a Azure-SSIS IR nem fut, vagy a **manuális bejegyzések** jelölőnégyzet be van jelölve, a csomag-és környezeti útvonalakat a SSISDB közvetlenül a következő formátumokban adhatja `<folder name>/<project name>/<package name>.dtsx` meg `<folder name>/<environment name>`: és.

   ![Tulajdonságok beállítása a beállítások lapon – manuális](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Ha a csomag helyeként kiválasztja a **fájlrendszert (csomag)** , amely automatikusan ki van választva, ha a Azure-SSIS IR SSISDB nélkül lett kiépítve, meg kell adnia a futtatni kívánt csomagot az univerzális elnevezési KONVENCIÓ (UNC) elérési útjának megadásával csomagfájl (`.dtsx`) a **csomag elérési útjában**. Ha például Azure Files tárolja a csomagot, a csomag elérési útja a következő lesz `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`:. 
   
   Ha a csomagot külön fájlban konfigurálja, akkor a`.dtsConfig` **konfigurációs elérési úton**is meg kell adnia a konfigurációs fájl () UNC elérési útját. Ha például Azure Files tárolja a konfigurációt, annak konfigurációs elérési útja a következő lesz `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`:.

   ![Tulajdonságok beállítása a beállítások lapon – manuális](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Ha a **fájlrendszer (projekt)** lehetőséget választja a csomag helyeként, meg kell adnia a futtatni kívánt csomagot úgy, hogy a projekt **elérési útján** és egy csomagfájl`.ispac`(`.dtsx`) segítségével megadja a Project- **fájl () elérési útját a projektben. Csomag neve**. Ha például a projektet a Azure Files tárolja, a projekt elérési útja a következő lesz `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`:.

   ![Tulajdonságok beállítása a beállítások lapon – manuális](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Ezután meg kell adnia a hitelesítő adatokat a projekt/csomag/konfigurációs fájlok eléréséhez. Ha korábban már megadta a csomag-végrehajtási hitelesítő adatok értékeit (lásd fentebb), a **csomag-végrehajtási hitelesítő adatok** jelölőnégyzetének megadásával újból felhasználhatja őket. Ellenkező esetben meg kell adnia a csomag-hozzáférési hitelesítő adatok (**tartományi**/**Felhasználónév**/**jelszava**) értékét. Ha például a projekt/csomag/konfiguráció a Azure Filesban van `Azure`tárolva, a **tartomány** : `<storage account name>`; a **Felhasználónév** ; és a **jelszó** `<storage account key>`. Azt is megteheti, hogy a AKV tárolt titkos kódokat értékként használja (lásd fent). A rendszer ezeket a hitelesítő adatokat fogja használni a csomag-és alárendelt csomagok eléréséhez a csomag feladatban, mindezt a saját elérési úttal/ugyanarról a projektből, valamint a konfigurációkból, beleértve a csomagokban megadott beállításokat is. 
   
   Ha SQL Server Data Tools (SSDT) használatával hozza létre a csomagot a **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** védelmi szintjén, meg kell adnia a jelszó értékét a **titkosításban. jelszó**. Azt is megteheti, hogy a AKV tárolt titkos kulcsot használja az értékként (lásd fent). Ha használta a **EncryptSensitiveWithUserKey** védelmi szintet, újra meg kell adnia a bizalmas értékeket a konfigurációs fájlokban vagy a **SSIS-paraméterek**/**Csatlakozáskezelő**/**tulajdonságában. Felülbírálások** lapjai (lásd alább). Ha használta a **EncryptAllWithUserKey** védelmi szintet, akkor nem támogatott, ezért a csomagot úgy kell konfigurálni, hogy a SSDT vagy `dtutil` a parancssori segédprogramon keresztül más védelmi szintet használjon. 
   
   A **naplózási szint**beállításnál válassza ki a csomag végrehajtásának előre meghatározott hatókörét. Jelölje be a **testreszabott** jelölőnégyzetet, ha inkább a testreszabott naplózási nevet szeretné megadni. Ha a csomag végrehajtását a csomagban megadható szabványos naplózási szolgáltatók használatával szeretné naplózni, a **naplózási elérési úton**adja meg az UNC elérési útját. Ha például Azure Files tárolja a naplókat, a naplózási útvonal a következő lesz `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`:. Ezen az elérési úton a rendszer létrehoz egy almappát a SSIS-csomag futtatása tevékenység futtatási AZONOSÍTÓjának végrehajtása után, amelyben a naplófájlok öt percenként jönnek létre. 
   
   Végül meg kell adnia a log mappa eléréséhez szükséges hitelesítő adatokat is. Ha korábban már megadta a csomag-hozzáférési hitelesítő adatok értékeit (lásd fentebb), akkor a **csomag-hozzáférési hitelesítő adatok** jelölőnégyzetének megadásával újból felhasználhatja őket. Ellenkező esetben meg kell adnia a naplózási hozzáférési hitelesítő adatok (**tartományi**/**Felhasználónév**/**jelszava**) értékét. Ha például a naplókat a Azure Files tárolja, a **tartomány** `Azure`:; a **Felhasználónév** `<storage account name>`; és a **jelszó** `<storage account key>`. Azt is megteheti, hogy a AKV tárolt titkos kódokat értékként használja (lásd fent). A rendszer ezeket a hitelesítő adatokat fogja használni a naplók tárolásához. 
   
   A fent említett összes UNC elérési út esetében a teljes fájlnévnek 260 karakternél rövidebbnek kell lennie, és a könyvtárnévnek 248 karakternél rövidebbnek kell lennie.

5. Ha a Azure-SSIS IR fut, a **SSIS parameters (paraméterek** ) lapon válassza a **SSISDB** lehetőséget a csomag helyeként, és a **Beállítások** lapon található **manuális bejegyzések** jelölőnégyzet nincs bejelölve, a meglévő SSIS a kiválasztott projektben/csomagban lévő paraméterek a SSISDB-ből lesznek megjelenítve, hogy értékeket Rendeljen hozzájuk. Ellenkező esetben megadhatja, hogy az egyes értékek manuálisan legyenek hozzárendelve – ellenőrizze, hogy léteznek-e, és helyesen vannak-e megadva a csomag végrehajtásának sikerességéhez. 
   
   Ha a **EncryptSensitiveWithUserKey** védelmi szintet használta a csomag SSDT és **fájlrendszerrel (csomag)** /való létrehozásakor, akkor azt is újra meg kell**adnia.** a konfigurációs fájlok vagy ezen a lapon lévő értékek hozzárendelésére szolgáló bizalmas paraméterek. 
   
   Ha értékeket rendel hozzá a paraméterekhez, hozzáadhat dinamikus tartalmat kifejezések, függvények, ADF rendszerváltozók, valamint az ADF-folyamat paramétereinek/változóinak használatával. Azt is megteheti, hogy a AKV tárolt titkos kódokat értékként használja (lásd fent).

   ![Tulajdonságok beállítása a SSIS Parameters lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. Ha a Azure-SSIS IR fut a **CSATLAKOZÁSKEZELŐ** SSIS-csomag végrehajtása lapján, a **SSISDB** a csomag helyeként van kiválasztva, és a **Beállítások** lapon a **manuális bejegyzések** jelölőnégyzet nincs bejelölve, a meglévő a kiválasztott projekthez/csomaghoz tartozó SSISDB megjelennek az értékek a tulajdonságaihoz való hozzárendeléséhez. Ellenkező esetben megadhatja, hogy egy-egy értéket rendeljen hozzá a tulajdonságaihoz manuálisan – győződjön meg arról, hogy léteznek, és helyesen vannak megadva a csomag végrehajtásának sikerességéhez. 
   
   Ha a **EncryptSensitiveWithUserKey** védelmi szintet használta a csomag SSDT és **fájlrendszerrel (csomag)** /való létrehozásakor, akkor azt is újra meg kell**adnia.** a bizalmas Csatlakozáskezelő tulajdonságaiban értékeket rendelhet hozzájuk a konfigurációs fájlokban vagy ezen a lapon. 
   
   Amikor értékeket rendel a Csatlakozáskezelő tulajdonságaihoz, hozzáadhat dinamikus tartalmat kifejezések, függvények, ADF rendszerváltozók és ADF-feldolgozási paraméterek/változók használatával. Azt is megteheti, hogy a AKV tárolt titkos kódokat értékként használja (lásd fent).

   ![Tulajdonságok beállítása a kapcsolatkezelő lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. A SSIS-csomag végrehajtásához tartozó tulajdonságok **felülbírálása** lapon megadhatja a kiválasztott csomagban lévő meglévő tulajdonságok elérési útját, ha az értékeket manuálisan szeretné hozzárendelni – ellenőrizze, hogy léteznek-e és helyesen vannak-e megadva a csomaghoz sikeres végrehajtás, például a felhasználói változó értékének felülbírálásához adja meg az elérési útját a következő formátumban: `\Package.Variables[User::<variable name>].Value`. 
   
   Ha a **EncryptSensitiveWithUserKey** védelmi szintet használta a csomag SSDT és **fájlrendszerrel (csomag)** /való létrehozásakor, akkor azt is újra meg kell**adnia.** az érzékeny tulajdonságok, amelyek értékeket rendelnek hozzájuk a konfigurációs fájlokban vagy ezen a lapon. 
   
   Ha értékeket rendel a tulajdonságokhoz, hozzáadhat dinamikus tartalmat kifejezések, függvények, ADF rendszerváltozók és az ADF-folyamat paramétereinek/változóinak használatával.

   ![Tulajdonságok beállítása a tulajdonság-felülbírálások lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   A konfigurációs fájlokban és a *SSIS paraméterek* lapon hozzárendelt értékek felülbírálása a/Csatlakozáskezelő-**Tulajdonságok felülbírálása** lapok használatával lehetséges, míg a **kapcsolatok kezelői** lapon hozzárendelt adatok felülbírálja a **tulajdonság-felülbírálások** lapot is.

8. A folyamat konfigurációjának ellenőrzéséhez kattintson az **Érvényesítés** elemre az eszköztáron. A **Folyamatérvényesítési jelentés** bezárásához kattintson a **>>** jelre.

9. Tegye közzé a folyamatot az ADF-ben az **összes közzététele** gomb megnyomásával. 

### <a name="run-the-pipeline"></a>A folyamat futtatása
Ebben a lépésben elindítja a folyamat futtatását. 

1. A folyamat futtatásának elindításához kattintson az **aktiválás** elemre az eszköztáron, majd kattintson az **aktiválás most**elemre. 

   ![Aktiválás most](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. A **Folyamatfuttatás** ablakban kattintson a **Befejezés** gombra. 

### <a name="monitor-the-pipeline"></a>A folyamat figyelése

1. Váltson a bal oldali **Monitorozás** lapra. Megtekintheti a folyamat futását és állapotát, valamint egyéb információkat (például a Futtatás kezdési idejét). A nézet frissítéséhez kattintson a **Frissítés** parancsra.

   ![Folyamatfuttatások](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Kattintson az **Actions** (Műveletek) oszlopban található **View Activity Runs** (Tevékenységfuttatások megtekintése) hivatkozásra. Csak egy tevékenység fut, mivel a folyamat csak egy tevékenységet tartalmaz (a SSIS-csomag végrehajtása tevékenység).

   ![Tevékenységfuttatások](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. A következő **lekérdezést** futtathatja az Azure SQL Server SSISDB-adatbázisán annak ellenőrzéséhez, hogy a csomag végre lett hajtva. 

   ```sql
   select * from catalog.executions
   ```

   ![Csomagok végrehajtásának ellenőrzése](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. A SSISDB végrehajtási AZONOSÍTÓját a folyamat futásának kimenetében is lekérheti, és az azonosító használatával a SQL Server Management Studio (SSMS) részletes végrehajtási naplókat és hibaüzeneteket is megtekintheti.

   ![A végrehajtás AZONOSÍTÓjának beolvasása.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>A folyamat beosztása triggerrel

Létrehozhat egy ütemezett triggert is a folyamathoz, hogy a folyamat ütemezésen (óránként, naponta stb.) fusson. Példaként tekintse meg a következőt: [create a Factory-Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Csomag futtatása a PowerShell-lel
Ebben a szakaszban a Azure PowerShell segítségével hozzon létre egy ADF-folyamatot a SSIS-csomagot futtató SSIS-csomag végrehajtása tevékenységgel. 

Telepítse a legújabb Azure PowerShell modulokat a [Azure PowerShell telepítésével és konfigurálásával kapcsolatos](/powershell/azure/install-az-ps)részletes útmutató lépéseit követve.

### <a name="create-an-adf-with-azure-ssis-ir"></a>ADF létrehozása Azure-SSIS IR
Használhat olyan meglévő ADF-t, amely már Azure-SSIS IR kiépítve, vagy létrehozhat egy új ADF-t Azure-SSIS IR az [oktatóanyag lépésenkénti utasításait követve: SSIS-csomagok üzembe helyezése az](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)Azure-ban a PowerShell használatával.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Folyamat létrehozása SSIS-csomag végrehajtása tevékenységgel 
Ebben a lépésben létrehoz egy folyamatot a SSIS-csomag végrehajtása tevékenységgel. A tevékenység futtatja a SSIS-csomagot. 

1. Hozzon létre egy **RunSSISPackagePipeline. JSON** nevű JSON-fájlt a **C:\ADF\RunSSISPackage** mappában az alábbi példához hasonló tartalommal:

   > [!IMPORTANT]
   > A fájl mentése előtt cserélje le az Objektumnév/Leírás/elérési utak, a tulajdonság/paraméterek értékét, a jelszavakat és az egyéb változó értékeket. 

   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx",
                       "type": "SSISDB"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
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
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
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

   A fájlrendszerekben/fájlmegosztás/Azure Filesban tárolt csomagok végrehajtásához a következő módon adhatja meg a csomag/napló hely tulajdonságainak értékeit.

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "type": "File",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   A fájlrendszerek/fájlmegosztás/Azure Filesban tárolt projekteken belüli csomagok végrehajtásához a következő módon adhatja meg a csomag Location tulajdonságának értékeit.

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. A Azure PowerShellban váltson a `C:\ADF\RunSSISPackage` mappára.

3. A folyamat **RunSSISPackagePipeline**létrehozásához futtassa a **set-AzDataFactoryV2Pipeline** parancsmagot.

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
A folyamat futtatásához használja a **meghívó-AzDataFactoryV2Pipeline** parancsmagot. A parancsmag visszaadja a folyamat futásának azonosítóját a későbbi monitorozás céljából.

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

A folyamatot a Azure Portal használatával is nyomon követheti. Részletes útmutatásért lásd: [a folyamat figyelése](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>A folyamat beosztása triggerrel
Az előző lépésben igény szerint futtatta a folyamatot. Létrehozhat egy ütemezett triggert is a folyamat ütemezett futtatásához (óránként, naponta stb.).

1. Hozzon létre egy **MyTrigger. JSON** nevű JSON-fájlt a **C:\ADF\RunSSISPackage** mappában a következő tartalommal: 

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
2. A **Azure PowerShellban**váltson a **C:\ADF\RunSSISPackage** mappára.
3. Futtassa a **set-AzDataFactoryV2Trigger** parancsmagot, amely létrehozza az triggert. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. Alapértelmezés szerint az trigger leállított állapotban van. Indítsa el a triggert a **Start-AzDataFactoryV2Trigger** parancsmag futtatásával. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. A **Get-AzDataFactoryV2Trigger** parancsmag futtatásával ellenőrizze, hogy elindult-e az trigger. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. Futtassa a következő parancsot a következő óra után. Ha például az aktuális időpont 3:25 PM UTC, futtassa a parancsot 4 órakor UTC-kor. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   A következő lekérdezést futtathatja az Azure SQL Server SSISDB-adatbázisán annak ellenőrzéséhez, hogy a csomag végre lett hajtva. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>További lépések
Tekintse meg a következő blogbejegyzést:
-   [ETL/ELT-munkafolyamatok modernizálása és kiterjesztése SSIS-tevékenységekkel az ADF-folyamatokban](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
