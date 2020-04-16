---
title: SSIS-csomag futtatása ssis-csomag végrehajtásával tevékenységgel
description: Ez a cikk ismerteti, hogyan futtathat egy SQL Server Integration Services (SSIS) csomagot egy Azure Data Factory-folyamat az SSIS-csomag végrehajtása tevékenység használatával.
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
ms.openlocfilehash: f400463f6102d46d9da48bbb10466ad4ca04a69b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413243"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>SSIS-csomag futtatása az SSIS-csomag végrehajtása tevékenységgel az Azure Data Factoryben

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk ismerteti, hogyan futtathat egy SQL Server Integration Services (SSIS) csomagot egy Azure Data Factory-folyamat az SSIS-csomag végrehajtása tevékenység használatával. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Hozzon létre egy Azure-SSIS-integrációs futásidejűt (IR), ha még nem rendelkezik ilyenel az [oktatóanyag: Azure-SSIS IR kiépítés](tutorial-create-azure-ssis-runtime-portal.md)című lépésének lépésenkénti utasításait követve.

## <a name="run-a-package-in-the-azure-portal"></a>Csomag futtatása az Azure Portalon
Ebben a szakaszban a Data Factory felhasználói felület (UI) vagy az alkalmazás segítségével hozzon létre egy Data Factory folyamat ot az SSIS-csomag végrehajtására vonatkozó tevékenység végrehajtásával, amely az SSIS-csomagot futtatja.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Folyamat létrehozása SSIS-csomag végrehajtásával
Ebben a lépésben a Data Factory felhasználói felületvagy alkalmazás segítségével hozzon létre egy folyamatot. Hozzáad egy SSIS-csomag végrehajtása tevékenységet a folyamathoz, és konfigurálja az SSIS-csomag futtatásához. 

1. A Data Factory áttekintése vagy kezdőlapja az Azure Portalon, válassza a **Szerzői & figyelés** csempe a Data Factory felhasználói felület vagy alkalmazás egy külön lapon. 

   ![A Data Factory kezdőlapja](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget. 

   ![Első lépések lap](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. A **Tevékenységek** eszközkészletben bontsa ki az **Általános**elemet. Ezután húzza az **SSIS-csomag végrehajtása** tevékenységet a folyamattervező felületére. 

   ![Végrehajtási SSIS-csomagtevékenység húzása a tervezőfelületre](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. Az SSIS-csomag végrehajtása tevékenység **Általános** lapján adja meg a tevékenység nevét és leírását. Állítsa be a választható **időmegtetési** és **újrapróbálkozási** értékeket.

   ![Tulajdonságok beállítása az Általános lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. Az SSIS-csomag végrehajtása tevékenység **Beállítások** lapján válasszon ki egy Azure-SSIS-ir-t, ahol futtatni szeretné a csomagot. Ha a csomag Windows-hitelesítést használ az adattárak eléréséhez (például SQL-kiszolgálók vagy fájlmegosztások a helyszínen vagy az Azure Files), jelölje be a **Windows-hitelesítés** jelölőnégyzetet. Adja meg a csomag végrehajtási hitelesítő adatainak értékeit a **Tartomány**, **Felhasználónév**és **Jelszó** mezőben. 

    Azt is megteheti, hogy az Azure key vaultban tárolt titkos kulcsokat is használhatja értékként. Ehhez jelölje be az **AZURE KEY VAULT** jelölőnégyzetet a megfelelő hitelesítő adatok mellett. Válassza ki vagy szerkessze a meglévő key vault csatolt szolgáltatást, vagy hozzon létre egy újat. Ezután válassza ki a hitelesítő adatok értékének titkos nevét vagy verzióját.

    A kulcstartóhoz csatolt szolgáltatás létrehozásakor vagy szerkesztésekor kijelölheti vagy szerkesztheti a meglévő kulcstartót, vagy létrehozhat egy újat. Győződjön meg arról, hogy a Data Factory felügyelt identitás hozzáférést a key vault, ha még nem tette meg. A titkos kulcsokat közvetlenül a `<Key vault linked service name>/<secret name>/<secret version>`következő formátumban is megadhatja: . Ha a csomag futtatásához 32 bites futásidejű időre van szüksége, jelölje be a **32 bites futásidejű** jelölőnégyzetet.

   A **Csomag helyéhez**válassza az **SSISDB**, **a File System (Package)**, **a File System (Project)** vagy **a Embedded package lehetőséget.** Ha az **SSISDB-t** választja a csomag helyeként, amely automatikusan kiválasztásra kerül, ha az Azure-SSIS ir-t egy Azure SQL Database-kiszolgáló vagy felügyelt példány által üzemeltetett SSIS-katalógussal (SSISDB) kiépítették, adja meg a ssisisdb-be üzembe helyezett csomagot. 

    Ha az Azure-SSIS ir fut, és a **Kézi bejegyzések** jelölőnégyzet nincs bejelölve, tallózással és a meglévő mappák, projektek, csomagok vagy környezetek kiválasztásával az SSISDB-ből. Válassza a **Frissítés** lehetőséget, ha az újonnan hozzáadott mappákat, projekteket, csomagokat vagy környezeteket szeretné lekérni az SSISDB-ből, hogy azok böngészhessenek és kiválaszthassanak. A csomagvégrehajtás-végrehajtások környezetének tallózásához vagy kiválasztásához előzetesen konfigurálnia kell a projekteket úgy, hogy ezeket a környezeteket az SSISDB csoport ban lévő mappákból származó hivatkozásokként adják hozzá. További információt az [SSIS-környezetek létrehozása és leképezése](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014)című témakörben talál.

   A **naplózási szint hez**válassza ki a naplózás előre meghatározott hatókörét a csomag végrehajtásához. Jelölje be a **Testreszabott jelölőnégyzetet,** ha helyette a testreszabott naplózási nevet szeretné megadni. 

   ![Tulajdonságok beállítása a Beállítások lapon – Automatikus](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Ha az Azure-SSIS IR nem fut, vagy a **Kézi bejegyzések** jelölőnégyzet be van jelölve, adja meg a `<folder name>/<project name>/<package name>.dtsx` `<folder name>/<environment name>`csomag és a környezet elérési útjait az SSISDB-ből közvetlenül a következő formátumokban: és .

   ![Tulajdonságok beállítása a Beállítások lapon - Kézi](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Ha a **Fájlrendszer (csomag)** lehetőséget választja a csomag helyeként, amely automatikusan kiválasztásra kerül, ha az Azure-SSIS ir-t SSISDB nélkül`.dtsx`lett kiépítve, adja meg a futtatni kívánt csomagot a csomagfájl ( UNC) elérési útjának megadásával a **Csomag elérési útja** mezőben. Ha például a csomagot az Azure Files ban `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`tárolja, a csomag elérési útja a . 
   
   Ha a csomagot külön fájlban konfigurálja, a konfigurációs fájlhoz`.dtsConfig`( ) unc elérési utat kell megadnia a **Konfigurációs útvonal** mezőben. Ha például a konfigurációt az Azure Files `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`ban tárolja, a konfigurációs elérési útja a .

   ![Tulajdonságok beállítása a Beállítások lapon - Kézi](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   Ha a csomag helyeként a **Fájlrendszer (Project)** lehetőséget választja, adja meg a`.ispac`futtatni kívánt csomagot úgy, hogy`.dtsx`a Projekt elérési útja ( ) UNC elérési útját adja meg a projekt elérési **útja** mezőben, és egy csomagfájlt ( ) a **projektből** a Csomag neve mezőben. Ha például a projektet az Azure Files `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`ban tárolja, a projekt elérési útja a .

   ![Tulajdonságok beállítása a Beállítások lapon - Kézi](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Ezután adja meg a projekt-, csomag- vagy konfigurációs fájlok eléréséhez szükséges hitelesítő adatokat. Ha korábban megadta a csomag végrehajtási hitelesítő adatainak értékeit (lásd az előzőt), újra felhasználhatja őket a **Csomag-végrehajtási hitelesítő adatok** ként jelölőnégyzetek bejelölésével. Ellenkező esetben adja meg a csomag hozzáférési hitelesítő adatainak értékeit a **Tartomány**, **Felhasználónév**és **Jelszó** mezőbe. Ha például a projektet, csomagot vagy konfigurációt az `Azure`Azure Files szolgáltatásban tárolja, a tartomány a , a felhasználónév `<storage account name>`a , a jelszó pedig `<storage account key>`. 

   Azt is megteheti, hogy a kulcstartóban tárolt titkos kulcsokat azok értékeiként használhatja (lásd az előzőt). Ezek a hitelesítő adatok a csomag és a gyermekcsomagok eléréséhez a Csomag végrehajtása feladat, mind a saját elérési útját, vagy ugyanazt a projektet, valamint konfigurációk, amelyek tartalmazzák a csomagokban megadott. 

   Ha a **Beágyazott csomag** lehetőséget választja a csomag helyeként, húzza a csomagot a futtatáshoz, vagy töltse **fel** egy fájlmappából a megadott mezőbe. A csomag automatikusan tömörítésre kerül, és beágyazódik a tevékenység hasznos adatába. Miután beágyazódott, **letöltheti** a csomagot később szerkesztésre. A beágyazott csomagot **paraméterezheti** úgy, hogy hozzárendeli egy csővezeték-paraméterhez, amely több tevékenységben is használható, így optimalizálhatja a folyamat hasznos adatának méretét. Ha a beágyazott csomag nem minden titkosított, és észleljük a Csomag végrehajtása feladat használatát, a **Csomag végrehajtása jelölőnégyzet** automatikusan be lesz jelölve, és a megfelelő gyermekcsomagok a fájlrendszerhivatkozásokkal automatikusan hozzáadva lesznek, hogy ön is beágyazza őket. Ha nem észleljük a Csomag végrehajtása feladat használatát, manuálisan kell bejelölnie a **Csomagfeladat végrehajtása** jelölőnégyzetet, és hozzá kell adnia a megfelelő gyermekcsomagokat a fájlrendszerükhivatkozásaival, hogy ön is beágyazza őket. Ha a gyermekcsomagok SQL Server-hivatkozásokat használnak, győződjön meg arról, hogy az SQL Server elérhető az Azure-SSIS IR szolgáltatása által.  A projekthivatkozások használata gyermekcsomagokhoz jelenleg nem támogatott.
   
   ![Tulajdonságok beállítása a Beállítások lapon - Kézi](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)
   
   Ha az **EncryptAllWithPassword** vagy **az EncryptSensitiveWithPassword** védelmi szintet használta, amikor a csomagot az SQL Server Data Tools segítségével hozta létre, írja be a jelszó értékét a **Titkosítási jelszó** mezőbe. Másik lehetőségként használhatja a kulcstartóban tárolt titkos kulcsot értékként (lásd az előzőt). Ha az **EncryptSensitiveWithUserKey** védelmi szintet használta, adja meg újra a bizalmas értékeket a konfigurációs fájlokban vagy az **SSIS-paraméterek**, **a Kapcsolatkezelők**vagy a **Tulajdonságfelülírás** lapon (lásd később). 

   Ha az **EncryptAllWithUserKey** védelmi szintet használta, az nem támogatott. Újra kell konfigurálnia a csomagot, hogy egy másik `dtutil` védelmi szintet használjon az SQL Server Data Tools vagy a parancssori segédprogram segítségével. 
   
   A **naplózási szint hez**válassza ki a naplózás előre meghatározott hatókörét a csomag végrehajtásához. Jelölje be a **Testreszabott jelölőnégyzetet,** ha helyette a testreszabott naplózási nevet szeretné megadni. Ha a csomagvégrehajtás-végrehajtásokat a csomagban megadható szabványos naplószolgáltatók használatán túl szeretné naplózni, adja meg a naplómappát úgy, hogy a naplózási elérési utat megadja a **Naplózási elérési út** mezőben. Ha például a naplókat az Azure Files ban `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`tárolja, a naplózási elérési út a . Ezen az elérési úton létrejön egy almappa minden egyes futtatott csomaghoz, amely az SSIS-csomag végrehajtása tevékenység futtatási azonosítójáról kapta a nevét, amelyben a naplófájlok ötpercenként jönnek létre. 
   
   Végül adja meg a naplómappa eléréséhez szükséges hitelesítő adatokat. Ha korábban megadta a csomag-hozzáférési hitelesítő adatok értékeit (lásd az előzőt), újra felhasználhatja őket, ha bejelöli a **Csomag-hozzáférési hitelesítő adatok** at. Ellenkező esetben adja meg a naplózási hozzáférési hitelesítő adatok értékeit a **Tartomány**, **Felhasználónév**és **Jelszó** mezőbe. Ha például a naplókat az Azure Files `Azure`szolgáltatásban tárolja, `<storage account name>`a tartomány `<storage account key>`a , a felhasználónév a , a jelszó pedig . 

    Azt is megteheti, hogy a kulcstartóban tárolt titkos kulcsokat azok értékeiként használhatja (lásd az előzőt). Ezek a hitelesítő adatok a naplók tárolására szolgálnak. 
   
   A korábban említett összes UNC elérési út esetében a teljesen minősített fájlnévnek 260 karakternél rövidebbnek kell lennie. A könyvtárnév nek 248 karakternél rövidebbnek kell lennie.

1. Az SSIS-csomag végrehajtása tevékenység **SSIS-paraméterek** lapján, ha az Azure-SSIS IR fut, az **SSISDB** lesz a csomag helye, és a **Beállítások** lap **Kézi bejegyzések** jelölőnégyzete törlődik, a kiválasztott projektben vagy az SSISDB-től származó csomagban meglévő SSIS-paraméterek jelennek meg, hogy értékeket rendeljen hozzájuk. Ellenkező esetben egyenként megadhatja őket, hogy manuálisan rendeljen hozzájuk értékeket. Győződjön meg arról, hogy léteznek, és helyesen vannak megadva a csomag végrehajtásának sikeres végrehajtásához. 
   
   Ha az **EncryptSensitiveWithUserKey** védelmi szintet használta, amikor a csomagot az SQL Server Data Tools és **a File System (Package)** segítségével hozta létre, vagy **a Fájlrendszer (Project)** van kiválasztva a csomag helyeként, akkor a bizalmas paramétereket is újra be kell írnia ahhoz, hogy értékeket rendeljen hozzájuk a konfigurációs fájlokban vagy ezen a lapon. 
   
   Amikor értékeket rendel a paraméterekhez, dinamikus tartalmat adhat hozzá kifejezések, függvények, Data Factory rendszerváltozók és Data Factory folyamatparaméterek vagy -változók használatával. Azt is megteheti, hogy a kulcstartóban tárolt titkos kulcsokat azok értékeiként használhatja (lásd az előzőt).

   ![Tulajdonságok beállítása az SSIS-paraméterek lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. Az SSIS-csomag végrehajtása tevékenység **kapcsolatkezelői** lapján, ha az Azure-SSIS IR fut, az **SSISDB** lesz a csomag helye, és a **Beállítások** lap **Kézi bejegyzések** jelölőnégyzete törlődik, a kiválasztott projekt vagy ssisisdb-csomag meglévő kapcsolatkezelői jelennek meg, hogy értékeket rendeljen a tulajdonságaikhoz. Ellenkező esetben egyenként megadhatja őket, hogy manuálisan rendeljenek értékeket a tulajdonságaikhoz. Győződjön meg arról, hogy léteznek, és helyesen vannak megadva a csomag végrehajtásának sikeres végrehajtásához. 
   
   Ha az **EncryptSensitiveWithUserKey** védelmi szintet használta, amikor a csomagot az SQL Server Data Tools és **a File System (Package)** segítségével hozta létre, vagy a **Fájlrendszer (Project)** van kiválasztva a csomag helyeként, akkor a bizalmas csatlakozáskezelő tulajdonságokat is újra be kell írnia ahhoz, hogy értékeket rendeljen hozzájuk a konfigurációs fájlokban vagy ezen a lapon. 
   
   Amikor értékeket rendel a kapcsolatkezelő tulajdonságaihoz, dinamikus tartalmat adhat hozzá kifejezések, függvények, Data Factory rendszerváltozók és Data Factory folyamatparaméterek vagy -változók használatával. Azt is megteheti, hogy a kulcstartóban tárolt titkos kulcsokat azok értékeiként használhatja (lásd az előzőt).

   ![Tulajdonságok beállítása a Kapcsolatkezelők lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. Az SSIS-csomag végrehajtása tevékenység **tulajdonságfelülbírálások** lapján egyenként adja meg a kijelölt csomag meglévő tulajdonságainak elérési útjait, hogy manuálisan rendeljen hozzájuk értékeket. Győződjön meg arról, hogy léteznek, és helyesen vannak megadva a csomag végrehajtásának sikeres végrehajtásához. Ha például felül szeretné írni a felhasználói változó értékét, adja `\Package.Variables[User::<variable name>].Value`meg az elérési útját a következő formátumban: . 
   
   Ha az **EncryptSensitiveWithUserKey** védelmi szintet használta, amikor a csomagot az SQL Server Data Tools és **a File System (Package)** segítségével hozta létre, vagy **a Fájlrendszer (Project)** van kiválasztva a csomag helyeként, akkor a bizalmas tulajdonságokat is újra be kell írnia, hogy értékeket rendeljen hozzájuk a konfigurációs fájlokban vagy ezen a lapon. 
   
   Amikor értékeket rendel a tulajdonságokhoz, dinamikus tartalmat adhat hozzá kifejezések, függvények, Data Factory rendszerváltozók és A Data Factory folyamatparaméterei vagy változói használatával.

   ![Tulajdonságok beállítása a Tulajdonságfelülírások lapon](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   A konfigurációs fájlokban és az **SSIS-paraméterek** lapon hozzárendelt értékek felülbírálhatók a **Kapcsolatkezelők** vagy a **Tulajdonságfelülírás okokkal.** A **Kapcsolatkezelők** lapon hozzárendelt értékek a **Tulajdonságfelülírások** lapon is felülbírálhatók.

1. A folyamatkonfiguráció érvényesítéséhez válassza az **Eszköztár Érvényesítés parancsát.** A **folyamatérvényesítési**jelentés **>>** bezárásához válassza a lehetőséget.

1. A folyamat adatgyárban való közzétételéhez válassza **az Összes közzététele**lehetőséget. 

### <a name="run-the-pipeline"></a>A folyamat futtatása
Ebben a lépésben egy folyamatfuttatást indít el. 

1. Folyamatfuttatás indításához válassza az **Eseményindító** lehetőséget az eszköztáron, majd az **Eseményindító most**lehetőséget. 

   ![Aktiválás most](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. A **Folyamatfuttatás** ablakban kattintson a **Befejezés** gombra. 

### <a name="monitor-the-pipeline"></a>A folyamat figyelése

1. Váltson a bal oldali **Monitorozás** lapra. Láthatja a folyamat futását és állapotát más információkkal együtt, például a **Futtatás kezdési** idejét. A nézet frissítéséhez válassza a **Frissítés** parancsot.

   ![Folyamatfuttatások](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Kattintson a **Műveletek** oszlopban található **Tevékenységfuttatások megtekintése** hivatkozásra. Csak egy tevékenységfuttatást lát, mert a folyamat csak egy tevékenységet rendelkezik. Ez az SSIS-csomag végrehajtása tevékenység.

   ![Tevékenységfuttatások](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Futtassa a következő lekérdezést az SQL-kiszolgáló SSISDB-adatbázisában, és ellenőrizze, hogy a csomag végrehajtása történt-e. 

   ```sql
   select * from catalog.executions
   ```

   ![Csomagvégrehajtás-végrehajtások ellenőrzése](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Az SSISDB végrehajtási azonosítót a folyamatfolyamat-futtatás kimenetéből is lekérdezheti, és az azonosító segítségével átfogóbb végrehajtási naplókat és hibaüzeneteket ellenőrizhet az SQL Server Management Studio-ban.

   ![Szerezd meg a kivégzés azonosítót.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>A folyamat ütemezése eseményindítóval

Ütemezett eseményindítót is létrehozhat a folyamathoz, hogy a folyamat ütemezés szerint, például óránként vagy naponta futhasson. Például lásd: [Adatgyár létrehozása - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Csomag futtatása a PowerShell használatával
Ebben a szakaszban az Azure PowerShell használatával hozzon létre egy Data Factory-folyamatot egy SSIS-csomag végrehajtására vonatkozó tevékenységvégrehajtásával, amely az SSIS-csomagot futtatja. 

Telepítse a legújabb Azure PowerShell-modulokat az Azure [PowerShell telepítése és konfigurálása](/powershell/azure/install-az-ps)című témakör részletes útmutatójának követésével.

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Adatgyár létrehozása az Azure-SSIS IR-vel
Használhatja a meglévő adatgyár, amely már rendelkezik az Azure-SSIS ir kiépített, vagy hozzon létre egy új adatgyár az Azure-SSIS IR. Kövesse az oktatóanyag részletes [utasításait: SSIS-csomagok telepítése az Azure-ba a PowerShellen keresztül.](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Folyamat létrehozása SSIS-csomag végrehajtásával 
Ebben a lépésben hozzon létre egy folyamatot egy SSIS-csomag végrehajtása tevékenységvégrehajtásával. A tevékenység futtatja az SSIS-csomagot. 

1. Hozzon létre egy *RunSSISPackagePipeline.json* nevű JSON-fájlt a *C:\ADF\RunSSISPackage* mappában, amelynek tartalma hasonló a következő példához.

   > [!IMPORTANT]
   > A fájl mentése előtt cserélje le az objektumneveket, leírásokat és elérési utakat, tulajdonság- vagy paraméterértékeket, jelszavakat és egyéb változóértékeket. 
    
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

   A fájlrendszerekben, fájlmegosztásokban vagy Az Azure Filesban tárolt csomagok végrehajtásához adja meg a csomag és a helytulajdonságok értékét az alábbiak szerint:

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

   A fájlrendszerekben, fájlmegosztásokban vagy Azure Filesban tárolt projekteken belüli csomagok végrehajtásához adja meg a csomag helytulajdonságának értékeit az alábbiak szerint:

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

   Beágyazott csomagok végrehajtásához írja be a csomag helytulajdonságának értékeit az alábbiak szerint:

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

2. Az Azure PowerShellben váltson a *C:\ADF\RunSSISPackage* mappára.

3. **A RunSSISPackagePipeline**folyamat létrehozásához futtassa a **Set-AzDataFactoryV2Pipeline** parancsmamot.

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Itt a minta kimenet:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>A folyamat futtatása
Használja az **Invoke-AzDataFactoryV2Pipeline** parancsmag a folyamat futtatásához. A parancsmag visszaadja a folyamat futásának azonosítóját a későbbi monitorozás céljából.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>A folyamat figyelése

A folyamat futási állapotának folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez futtassa az alábbi PowerShell-szkriptet. Másolja vagy illessze be a következő parancsfájlt a PowerShell-ablakban, és válassza az Enter lehetőséget. 

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

A folyamat az Azure Portal használatával is figyelheti. A részletes útmutatásról a [Folyamat figyelése](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)című témakörben talál.

### <a name="schedule-the-pipeline-with-a-trigger"></a>A folyamat ütemezése eseményindítóval
Az előző lépésben igény szerint futtatta a folyamatot. Ütemezési eseményindítót is létrehozhat a folyamat ütemezés szerinti futtatásához, például óránkénti vagy napi.

1. Hozzon létre egy *MyTrigger.json* nevű JSON-fájlt a *C:\ADF\RunSSISPackage* mappában a következő tartalommal: 
        
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
    
1. Az Azure PowerShellben váltson a *C:\ADF\RunSSISPackage* mappára.
1. Futtassa a **Set-AzDataFactoryV2Trigger** parancsmast, amely létrehozza az eseményindítót. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Alapértelmezés szerint az eseményindító leállított állapotban van. Indítsa el az eseményindítót a **Start-AzDataFactoryV2Trigger** parancsmag futtatásával. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Ellenőrizze, hogy az eseményindító **elindul-e a Get-AzDataFactoryV2Trigger** parancsmag futtatásával. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Futtassa a következő parancsot a következő óra után. Ha például az aktuális idő 15:25-kor (UTC) van, futtassa a parancsot 16:00-kor UTC-kor. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Futtassa a következő lekérdezést az SQL-kiszolgáló SSISDB-adatbázisában, és ellenőrizze, hogy a csomag végrehajtása történt-e. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>További lépések
Lásd a következő blogbejegyzést:
- [Az ETL/ELT-munkafolyamatok modernizálása és bővítése SSIS-tevékenységekkel az Azure Data Factory folyamataiban](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
