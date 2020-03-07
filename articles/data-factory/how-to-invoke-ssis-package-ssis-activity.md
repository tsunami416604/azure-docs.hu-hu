---
title: SSIS-csomag futtatása a SSIS-csomag végrehajtása tevékenységgel
description: Ez a cikk azt ismerteti, hogyan futtathat egy SQL Server Integration Services-(SSIS-) csomagot egy Azure Data Factory-folyamatban a SSIS-csomag végrehajtása tevékenység használatával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 11/14/2019
ms.openlocfilehash: 6027c2d94535ca2ef5c41e7027fe070c6ccb21a0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388562"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>SSIS-csomag futtatása a SSIS-csomag végrehajtása tevékenységgel Azure Data Factory
Ez a cikk azt ismerteti, hogyan futtathat egy SQL Server Integration Services-(SSIS-) csomagot egy Azure Data Factory-folyamatban a SSIS-csomag végrehajtása tevékenység használatával. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Hozzon létre egy Azure-SSIS integrációs modult (IR), ha még nem rendelkezik az oktatóanyag részletes utasításait követve [: kiépítés Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Csomag futtatása a Azure Portal
Ebben a szakaszban a Data Factory felhasználói felületén (UI) vagy az alkalmazáson keresztül hozhat létre egy Data Factory folyamatot a SSIS-csomagot futtató végrehajtási SSIS-csomag tevékenységgel.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Folyamat létrehozása SSIS-csomag végrehajtása tevékenységgel
Ebben a lépésben a Data Factory felhasználói felületét vagy az alkalmazást használja egy folyamat létrehozásához. Vegyen fel egy végrehajtási SSIS-csomag tevékenységet a folyamatba, és konfigurálja úgy, hogy futtassa a SSIS-csomagot. 

1. A Azure Portal Data Factory áttekintés vagy Kezdőlap lapján válassza a **szerző & figyelő** csempét, hogy elindítsa a Data Factory felhasználói felületét vagy alkalmazását egy külön lapon. 

   ![Data Factory Kezdőlap](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget. 

   ![Első lépések lap](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. A **tevékenységek** eszközkészletben bontsa ki az **általános**elemet. Ezután húzzon egy **SSIS-csomag végrehajtása** tevékenységet a folyamat tervező felületére. 

   ![SSIS-csomag végrehajtása tevékenység áthúzása a tervező felületére](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. A SSIS-csomag végrehajtása tevékenység **általános** lapján adja meg a tevékenység nevét és leírását. Adja meg a választható **időtúllépést** és az **újrapróbálkozási** értékeket.

   ![Tulajdonságok beállítása az Általános lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. A SSIS-csomag végrehajtása tevékenység **Beállítások** lapján válasszon egy Azure-SSIS IR, amelyen futtatni szeretné a csomagot. Ha a csomag Windows-hitelesítést használ az adattárakhoz való hozzáféréshez (például SQL-kiszolgálók vagy a helyszíni vagy Azure Files-alapú fájlmegosztás), jelölje be a **Windows-hitelesítés** jelölőnégyzetet. Adja meg a csomag végrehajtási hitelesítő adatainak értékét a **tartomány**, a **Felhasználónév**és a **jelszó** mezőkben. 

    Azt is megteheti, hogy az Azure Key vaultban tárolt titkos kulcsokat használja értékként. Ehhez jelölje be az **Azure Key Vault** jelölőnégyzetet a megfelelő hitelesítő adatok mellett. Válassza ki vagy szerkessze a meglévő Key Vault társított szolgáltatást, vagy hozzon létre egy újat. Ezután válassza ki a hitelesítő adat értékének titkos nevét vagy verzióját.

    A Key Vault társított szolgáltatás létrehozásakor vagy szerkesztésekor kiválaszthatja vagy szerkesztheti a meglévő kulcstartót, vagy létrehozhat egy újat is. Ha még nem tette meg, győződjön meg arról, hogy Data Factory felügyelt identitás-hozzáférést biztosít a kulcstartóhoz. A titkokat közvetlenül a következő formátumban is megadhatja: `<Key vault linked service name>/<secret name>/<secret version>`. Ha a csomag futtatásához 32 bites futtatókörnyezet szükséges, jelölje be a **32 bites futtatókörnyezet** jelölőnégyzetet.

   A **csomag helye**területen válassza a **SSISDB**, a fájlrendszer **(csomag)** , a **fájlrendszer (projekt)** vagy a **beágyazott csomag**elemet. Ha a **SSISDB** lehetőséget választja, amely automatikusan ki van választva, ha a Azure-SSIS IR a Azure SQL Database-kiszolgáló vagy a felügyelt példány által ÜZEMELTETett SSIS-katalógussal (SSISDB) lett kiépítve, adja meg azt a csomagot, amelyet a SSISDB üzembe helyezett. 

    Ha a Azure-SSIS IR fut, és a **manuális bejegyzések** jelölőnégyzet nincs bejelölve, tallózással keresse meg és válassza ki a meglévő mappákat, projekteket, csomagokat és környezeteket a SSISDB. Válassza a **frissítés** lehetőséget az újonnan hozzáadott mappák, projektek, csomagok vagy KÖRNYEZETek SSISDB való beolvasásához, hogy azok elérhetők legyenek a böngészéshez és a kiválasztáshoz. A csomagok végrehajtásához szükséges környezetek tallózásához vagy kiválasztásához előre be kell állítania a projekteket, hogy a SSISDB alatt található azonos mappák hivatkozásaiként adja hozzá ezeket a környezeteket. További információ: [SSIS-környezetek létrehozása és leképezése](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   A **naplózási szint**beállításnál válassza ki a csomag végrehajtásának előre meghatározott hatókörét. Ha ehelyett a testreszabott naplózási nevet szeretné megadni, jelölje be a **testreszabott** jelölőnégyzetet. 

   ![Tulajdonságok beállítása a beállítások lapon – automatikus](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Ha a Azure-SSIS IR nem fut, vagy a **manuális bejegyzések** jelölőnégyzet be van jelölve, adja meg a csomag-és környezeti elérési utakat a SSISDB közvetlenül a következő formátumokban: `<folder name>/<project name>/<package name>.dtsx` és `<folder name>/<environment name>`.

   ![Tulajdonságok beállítása a beállítások lapon – manuális](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Ha a csomag helyeként kiválasztja a **fájlrendszert (csomag)** , amely automatikusan ki van választva, ha a Azure-SSIS IR SSISDB nélkül lett kiépítve, adja meg a futtatni kívánt csomagot az univerzális elnevezési KONVENCIÓ (UNC) elérési útjának megadásával a csomagfájl (`.dtsx`) számára a **csomag elérési útja** mezőben. Ha például Azure Files tárolja a csomagot, a csomagjának elérési útja `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`. 
   
   Ha a csomagot külön fájlban konfigurálja, akkor a **konfigurációs elérési út** mezőben meg kell adnia a konfigurációs fájl (`.dtsConfig`) UNC elérési útját is. Ha például a konfigurációját a Azure Files tárolja, a konfigurációs elérési útja `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   ![Tulajdonságok beállítása a beállítások lapon – manuális](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Ha a **fájlrendszer (projekt)** lehetőséget választja a csomag helyeként, adja meg a futtatni kívánt csomagot a projekt **elérési útja** mezőben a PROJEKTFÁJL (`.ispac`) UNC elérési útjának megadásával, valamint egy csomagfájl (`.dtsx`) a projektben a **csomag neve** mezőben. Ha például a projektet a Azure Files tárolja, a projekt elérési útja `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   ![Tulajdonságok beállítása a beállítások lapon – manuális](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Ezután adja meg a projekthez, csomaghoz vagy konfigurációs fájlokhoz való hozzáféréshez szükséges hitelesítő adatokat. Ha korábban már megadta a csomag-végrehajtási hitelesítő adatok értékeit (lásd az előzőt), akkor újra felhasználhatja őket úgy, hogy kijelöli a **csomag-végrehajtás hitelesítő adataival megegyező** jelölőnégyzetet. Ellenkező esetben adja meg a csomag hozzáférési hitelesítő adatainak értékét a **tartomány**, a **Felhasználónév**és a **jelszó** mezőkben. Ha például a projektet, csomagot vagy konfigurációt Azure Filesban tárolja, a tartomány `Azure`, a Felhasználónév `<storage account name>`, és a jelszó `<storage account key>`. 

   Azt is megteheti, hogy a Key vaultban tárolt titkos kódokat értékként használja (lásd az előzőt). Ezekkel a hitelesítő adatokkal érheti el a csomagokat és a gyermek csomagokat a csomag végrehajtása tevékenységben, mindezt a saját elérési úttal vagy ugyanazon projekttel, valamint a csomagokban megadott konfigurációkkal. 

   Ha kijelöli a **beágyazott csomag** lehetőséget a csomag helyeként, húzza a csomagot a futtatásához, vagy **töltse fel** azt egy fájl mappájából a megadott mezőbe. A csomag automatikusan tömörítve és beágyazva lesz a tevékenység hasznos adataiba. A beágyazás után később is **letöltheti** a csomagot szerkesztésre. A beágyazott csomagot úgy is **parametrizálja** , hogy hozzárendeli azt egy olyan folyamat-paraméterhez, amely több tevékenységben is felhasználható, így optimalizálhatja a folyamat adattartalma méretét. Ha a beágyazott csomag nem minden titkosítva van, és a csomag végrehajtása feladatot érzékeljük, akkor a **csomag végrehajtása feladat** jelölőnégyzet automatikusan ki lesz választva, és a rendszer automatikusan hozzáadja a megfelelő, a fájlrendszerre vonatkozó hivatkozásokkal rendelkező gyermek csomagokat is. Ha nem tudjuk felderíteni a csomag végrehajtása feladatot, manuálisan kell kijelölnie a **csomag végrehajtása feladatot** jelölőnégyzetet, és hozzá kell adnia a megfelelő gyermek csomagokat a fájlrendszer hivatkozásával, ha azt is beágyazza. Ha a gyermek csomagok SQL Server referenciákat használnak, győződjön meg arról, hogy a Azure-SSIS IR a SQL Server elérhető.  Jelenleg nem támogatott a projektre vonatkozó hivatkozások használata a gyermek csomagokhoz.
   
   ![Tulajdonságok beállítása a beállítások lapon – manuális](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)
   
   Ha a **EncryptAllWithPassword** vagy a **EncryptSensitiveWithPassword** védelmi szintet használta a csomagnak a SQL Server Data Tools használatával történő létrehozásakor, írja be a jelszó értékét a **titkosítási jelszó** mezőbe. Azt is megteheti, hogy a Key vaultban tárolt titkos kulcsot használja értékként (lásd az előzőt). Ha a **EncryptSensitiveWithUserKey** védelmi szintet használta, adja meg a bizalmas értékeket a konfigurációs fájlokban vagy a **SSIS paraméterek**, a **Csatlakozáskezelő**vagy a **tulajdonság felülbírálásai** lapon (lásd: később). 

   Ha a **EncryptAllWithUserKey** védelmi szintet használta, az nem támogatott. A csomagot úgy kell konfigurálni, hogy SQL Server Data Tools vagy a `dtutil` parancssori segédprogram használatával más védelmi szintet használjon. 
   
   A **naplózási szint**beállításnál válassza ki a csomag végrehajtásának előre meghatározott hatókörét. Ha ehelyett a testreszabott naplózási nevet szeretné megadni, jelölje be a **testreszabott** jelölőnégyzetet. Ha a csomag végrehajtását a csomagban megadható szabványos naplózási szolgáltatók használatával szeretné naplózni, a **naplózási útvonal** mezőben adja meg az UNC elérési út megadásával a napló mappáját. Ha például Azure Files tárolja a naplókat, a naplózási útvonal `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. A rendszer létrehoz egy almappát ezen az elérési úton a SSIS-csomag végrehajtása tevékenység futtatási AZONOSÍTÓját követő minden egyes csomaghoz, amelyben a naplófájlok öt percenként jönnek létre. 
   
   Végül adja meg a log mappa eléréséhez szükséges hitelesítő adatokat. Ha korábban már megadta a csomag-hozzáférési hitelesítő adatok értékeit (lásd az előzőt), akkor újra felhasználhatja őket úgy, hogy kijelöli a **csomag hozzáférési hitelesítő adatai** jelölőnégyzetet. Ellenkező esetben adja meg a naplózási hozzáférési hitelesítő adatok értékeit a **tartomány**, a **Felhasználónév**és a **jelszó** mezőkben. Ha például Azure Files tárolja a naplókat, a tartomány `Azure`, a Felhasználónév `<storage account name>`, és a jelszó `<storage account key>`. 

    Azt is megteheti, hogy a Key vaultban tárolt titkos kódokat értékként használja (lásd az előzőt). A rendszer ezeket a hitelesítő adatokat használja a naplók tárolásához. 
   
   Az összes korábban említett UNC elérési út esetében a teljes fájlnévnek 260 karakternél rövidebbnek kell lennie. A könyvtár nevének 248 karakternél rövidebbnek kell lennie.

1. Ha a Azure-SSIS IR fut a **SSIS parameters (paraméterek** ) lapon a SSIS végrehajtásakor, a **SSISDB** a csomag helyeként van kiválasztva, és a **Beállítások** lapon a **manuális bejegyzések** jelölőnégyzet be van jelölve, a kiválasztott projektben vagy csomagban lévő meglévő SSIS-paraméterek az értékek hozzárendeléséhez lesznek megjelenítve. Ellenkező esetben megadhatja, hogy az egyes értékek manuálisan legyenek hozzárendelve. Győződjön meg arról, hogy léteznek, és helyesen vannak megadva a csomag végrehajtásának sikerességéhez. 
   
   Ha a **EncryptSensitiveWithUserKey** védelmi szintet használta SQL Server Data Tools és **fájlrendszer (csomag)** vagy **fájlrendszer (projekt)** használatával, akkor a csomag helyeként is be kell írnia a bizalmas paramétereket, hogy értékeket Rendeljen hozzájuk a konfigurációs fájlokban vagy ezen a lapon. 
   
   Ha értékeket rendel hozzá a paraméterekhez, hozzáadhat dinamikus tartalmat kifejezések, függvények, Data Factory rendszerváltozók, valamint Data Factory folyamat paramétereinek vagy változóinak használatával. Azt is megteheti, hogy a Key vaultban tárolt titkos kódokat értékként használja (lásd az előzőt).

   ![Tulajdonságok beállítása a SSIS Parameters lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. Ha a Azure-SSIS IR fut a SSIS-csomaghoz tartozó **kapcsolatkezelő** lapon, a **SSISDB** lehetőség van kiválasztva a csomag helyeként, és a **Beállítások** lapon a **manuális bejegyzések** jelölőnégyzet be van jelölve, a kiválasztott projektben vagy csomagban lévő, a SSISDB-ből származó meglévő kapcsolatok megjelennek az értékek a tulajdonságaihoz való hozzárendeléséhez. Ellenkező esetben megadhatja, hogy egy-egy értéket rendeljen hozzá a tulajdonságaihoz manuálisan. Győződjön meg arról, hogy léteznek, és helyesen vannak megadva a csomag végrehajtásának sikerességéhez. 
   
   Ha a csomag létrehozásakor a **EncryptSensitiveWithUserKey** védelmi szintet használta SQL Server Data Tools és **fájlrendszer (csomag)** vagy **fájlrendszer (projekt)** használatával, akkor is meg kell adnia a bizalmas kapcsolatkezelő tulajdonságait, hogy értékeket Rendeljen hozzájuk a konfigurációs fájlokban vagy ezen a lapon. 
   
   Ha értékeket rendel a Csatlakozáskezelő tulajdonságaihoz, a kifejezések, függvények, Data Factory rendszerváltozók és Data Factory folyamat paramétereinek vagy változóinak használatával adhat hozzá dinamikus tartalmat. Azt is megteheti, hogy a Key vaultban tárolt titkos kódokat értékként használja (lásd az előzőt).

   ![Tulajdonságok beállítása a kapcsolatkezelő lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. A SSIS-csomag végrehajtása művelet **Tulajdonságok felülbírálása** lapján adja meg a kiválasztott csomagban lévő meglévő tulajdonságok elérési útját, hogy az értékeket manuálisan rendelje hozzá. Győződjön meg arról, hogy léteznek, és helyesen vannak megadva a csomag végrehajtásának sikerességéhez. A felhasználói változó értékének felülbírálásához például adja meg az elérési útját a következő formátumban: `\Package.Variables[User::<variable name>].Value`. 
   
   Ha a csomag létrehozásakor a **EncryptSensitiveWithUserKey** védelmi szintet használta SQL Server Data Tools és **fájlrendszer (csomag)** vagy **fájlrendszer (projekt)** használatával, akkor a bizalmas tulajdonságokat is újra meg kell adnia, hogy értékeket Rendeljen hozzájuk a konfigurációs fájlokban vagy ezen a lapon. 
   
   Amikor értékeket rendel a tulajdonságokhoz, hozzáadhat dinamikus tartalmat kifejezések, függvények, Data Factory rendszerváltozók, valamint Data Factory folyamat paramétereinek vagy változóinak használatával.

   ![Tulajdonságok beállítása a tulajdonság-felülbírálások lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   A konfigurációs fájlokban és a **SSIS paraméterek** lapon hozzárendelt értékek felülbírálása a **Csatlakozáskezelő** vagy a **tulajdonság felülbírálási** lapjaival lehetséges. A **kapcsolatkezelő** lapon megadott értékek felülbírálása a **Tulajdonságok** felülbírálásai lapon is megadható.

1. A folyamat konfigurációjának ellenőrzéséhez kattintson az **Érvényesítés** elemre az eszköztáron. A folyamat- **ellenőrzési jelentés**bezárásához válassza a **>>** lehetőséget.

1. A folyamat Data Factory való közzétételéhez válassza az **összes közzététele**lehetőséget. 

### <a name="run-the-pipeline"></a>A folyamat futtatása
Ebben a lépésben elindítja a folyamat futtatását. 

1. A folyamat futtatásának elindításához válassza az **aktiválás** lehetőséget az eszköztáron, majd válassza az **aktiválás most**lehetőséget. 

   ![Aktiválás most](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. A **Folyamatfuttatás** ablakban kattintson a **Befejezés** gombra. 

### <a name="monitor-the-pipeline"></a>A folyamat figyelése

1. Váltson a bal oldali **Monitor** (Monitorozás) lapra. Megtekintheti a folyamat futását és állapotát, valamint egyéb információkat, például a **Futtatás kezdési** idejét. A nézet frissítéséhez válassza a **Frissítés** parancsot.

   ![Folyamatfuttatások](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Kattintson a **Műveletek** oszlopban található **Tevékenységfuttatások megtekintése** hivatkozásra. Csak egy tevékenység fut, mert a folyamat csak egy tevékenységet tartalmaz. Ez a SSIS-csomag végrehajtása tevékenység.

   ![Tevékenységfuttatások](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Futtassa a következő lekérdezést az SQL Server SSISDB-adatbázisán a csomag végrehajtásának ellenőrzéséhez. 

   ```sql
   select * from catalog.executions
   ```

   ![Csomagok végrehajtásának ellenőrzése](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. A SSISDB végrehajtási AZONOSÍTÓját a folyamat által futtatott tevékenység kimenetében is lekérheti, és az azonosító használatával további részletes végrehajtási naplókat és hibaüzeneteket is megtudhat a SQL Server Management Studio.

   ![A végrehajtás AZONOSÍTÓjának beolvasása.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>A folyamat beosztása triggerrel

Létrehozhat egy ütemezett triggert is a folyamathoz, hogy a folyamat ütemezésen, például óránként vagy naponta fusson. Példaként tekintse meg a következőt: [create a Factory-Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Csomag futtatása a PowerShell-lel
Ebben a szakaszban a Azure PowerShell használatával hozzon létre egy Data Factory folyamatot egy végrehajtási SSIS csomaggal, amely a SSIS-csomagot futtatja. 

Telepítse a legújabb Azure PowerShell modulokat a [Azure PowerShell telepítésével és konfigurálásával kapcsolatos](/powershell/azure/install-az-ps)részletes útmutató lépéseit követve.

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Hozzon létre egy adatgyárat Azure-SSIS IR
Használhat olyan meglévő adatelőállítót, amely már rendelkezik Azure-SSIS IR kiépítve, vagy létrehozhat egy új, Azure-SSIS IRkal rendelkező adatgyárat. Kövesse az oktatóanyag részletes utasításait [: SSIS-csomagok üzembe helyezése az Azure-](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)ban a PowerShell használatával.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Folyamat létrehozása SSIS-csomag végrehajtása tevékenységgel 
Ebben a lépésben létrehoz egy folyamatot a SSIS-csomag végrehajtása tevékenységgel. A tevékenység futtatja a SSIS-csomagot. 

1. Hozzon létre egy *RunSSISPackagePipeline. JSON* nevű JSON-fájlt a *C:\ADF\RunSSISPackage* mappában az alábbi példához hasonló tartalommal.

   > [!IMPORTANT]
   > A fájl mentése előtt cserélje le az Objektumnév, a leírások és az elérési utak, a tulajdonság vagy a paraméterek értékét, a jelszavakat és az egyéb változó értékeket. 
    
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

   A fájlrendszerben, fájlmegosztást vagy Azure Filesban tárolt csomagok végrehajtásához adja meg a csomag és a naplózási hely tulajdonságainak értékét a következőképpen:

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

   A fájlrendszerekben, fájlmegosztást vagy Azure Filesban tárolt projekteken belüli csomagok végrehajtásához adja meg a csomag Location tulajdonságának értékeit az alábbiak szerint:

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

   Beágyazott csomagok végrehajtásához adja meg a csomag Location tulajdonságának értékeit az alábbiak szerint:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

2. A Azure PowerShellban váltson a *C:\ADF\RunSSISPackage* mappára.

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

A folyamat futási állapotának folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez futtassa az alábbi PowerShell-szkriptet. Másolja vagy illessze be a következő szkriptet a PowerShell-ablakba, majd kattintson az ENTER gombra. 

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
Az előző lépésben igény szerint futtatta a folyamatot. Létrehozhat egy ütemezett triggert is, amely a folyamat ütemezett futtatására szolgál, például óránként vagy naponta.

1. Hozzon létre egy *MyTrigger. JSON* nevű JSON-fájlt a *C:\ADF\RunSSISPackage* mappában a következő tartalommal: 
        
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
    
1. A Azure PowerShellban váltson a *C:\ADF\RunSSISPackage* mappára.
1. Futtassa a **set-AzDataFactoryV2Trigger** parancsmagot, amely létrehozza az triggert. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Alapértelmezés szerint az trigger leállított állapotban van. Indítsa el a triggert a **Start-AzDataFactoryV2Trigger** parancsmag futtatásával. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. A **Get-AzDataFactoryV2Trigger** parancsmag futtatásával ellenőrizze, hogy elindult-e az trigger. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Futtassa a következő parancsot a következő óra után. Ha például az aktuális időpont 3:25 PM UTC, futtassa a parancsot 4 órakor UTC-kor. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Futtassa a következő lekérdezést az SQL Server SSISDB-adatbázisán a csomag végrehajtásának ellenőrzéséhez. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő blogbejegyzést:
- [Az ETL/ELT-munkafolyamatok modernizálása és kiterjesztése a SSIS-tevékenységekkel Azure Data Factory-folyamatokban](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
