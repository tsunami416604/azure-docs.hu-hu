---
title: Helyezze át a data - adatkezelési átjáró |} A Microsoft Docs
description: Állítsa be a data gateway adatok áthelyezése a helyszíni és a felhő között. Az Azure Data Factoryban az adatkezelési átjáró segítségével az adatok áthelyezése.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 3094075607f096200ce1fb7ad499776bdcd3e093
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233599"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Adatok áthelyezése egy helyszíni forrásra, és a felhő között az adatkezelési átjáróval
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [között a helyszíni adatok másolása és a Data Factory használatával a felhő](../tutorial-hybrid-copy-powershell.md).

Ez a cikk áttekintést Adatintegráció helyszíni adattárak és a Data Factory használatával felhőalapú adattárak között. Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) cikk és az egyéb data factory alapvető fogalmak: [adatkészletek](data-factory-create-datasets.md) és [folyamatok](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Adatkezelési átjáró
A helyszíni gépen helyez át adatokat, és a helyszíni adattárolókból engedélyezéséhez telepítenie kell az adatkezelési átjáró. Az átjáró telepíthető ugyanarra a gépre adattárként vagy egy másik gépen mindaddig, amíg az átjáró képes kapcsolódni az adattárhoz.

> [!IMPORTANT]
> Lásd: [adatkezelési átjáró](data-factory-data-management-gateway.md) adatkezelési átjáró részleteit ismertető cikket. 

A következő forgatókönyv bemutatja, hogyan hozhat létre egy adat-előállítót egy folyamatot, amely egy helyszíni helyez át adatokat **SQL Server** adatbázis egy Azure blob Storage-tárolóba. A bemutató részeként telepíti és konfigurálja az adatkezelési átjárót a gépen.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Forgatókönyv: a helyszíni adatok felhőbe másolása
Ez az útmutató hajtsa végre a következő lépéseket: 

1. Adat-előállító létrehozása
2. Hozzon létre egy adatkezelési átjárón. 
3. A forrás és fogadó társított szolgáltatások létrehozása.
4. Adatkészleteket hoz létre, bemeneti és kimeneti adatokat képviselik.
5. Folyamat létrehozása másolási tevékenységgel az adatok áthelyezéséhez

## <a name="prerequisites-for-the-tutorial"></a>Az oktatóanyag előfeltételei
Mielőtt elkezdené a forgatókönyv, a következő előfeltételeknek kell rendelkeznie:

* **Azure-előfizetés**.  Ha nem rendelkezik előfizetéssel, mindössze néhány perc alatt létrehozhat egy ingyenes próbafiókot. Tekintse meg a [ingyenes próbaverzió](http://azure.microsoft.com/pricing/free-trial/) részleteivel.
* **Az Azure Storage-fiók**. A blob storage-ot használ egy **cél/fogadó** ebben az oktatóanyagban. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../../storage/common/storage-quickstart-create-account.md) ismertető cikket.
* **Egy SQL Server**. Ebben az oktatóanyagban egy helyszíni SQL Server-adatbázist használunk **forrásadattárként**. 

## <a name="create-data-factory"></a>Data factory létrehozása
Ebben a lépésben, az Azure portal használatával hozzon létre egy Azure Data Factory-példányt nevű **ADFTutorialOnPremDF**.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **erőforrás létrehozása**, kattintson a **intelligencia és elemzés**, és kattintson a **adat-előállító**.

   ![New (Új)->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. Az a **új adat-előállító** lap, adja meg **ADFTutorialOnPremDF** neve.

    ![Hozzáadás a Kezdőpulthoz](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a hibaüzenetet kapja: **nem érhető el a Data factory name "ADFTutorialOnPremDF"**, módosítsa a nevet az adat-előállító (például yournameADFTutorialOnPremDF), és próbálkozzon újra a létrehozással. Ez a név helyett ADFTutorialOnPremDF használja ez az oktatóanyag fennmaradó lépéseinek végrehajtása során.
   >
   > Az adat-előállító nevét regisztrálható egy **DNS** nevét, a jövőben így válhat publicaly látható.
   >
   >
4. Jelölje ki azt az **Azure-előfizetést**, ahol létre szeretné hozni a data factoryt.
5. Jelöljön ki egy meglévő **erőforráscsoportot**, vagy hozzon létre egyet. Az oktatóanyag hozzon létre egy erőforráscsoportot: **ADFTutorialResourceGroup**.
6. Kattintson a **létrehozás** a a **új adat-előállító** lapot.

   > [!IMPORTANT]
   > Data Factory-példány létrehozásához a [Data Factory közreműködője](../../role-based-access-control/built-in-roles.md#data-factory-contributor) szerepkör tagjának kell lennie az előfizetés/erőforráscsoport szintjén.
   >
   >
7. Létrehozás után megjelenik a **adat-előállító** lapon az alábbi képen látható módon:

   ![Data Factory kezdőlapján](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Átjáró létrehozása
1. Az a **adat-előállító** kattintson **Szerző és üzembe helyezése** csempére kattintva indítsa el a **szerkesztő** a data Factory.

    ![Az Author and deploy (Fejlesztés és üzembe helyezés) csempe](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. A Data Factory Editorban kattintson **... További** az eszköztárban, majd kattintson a **új data gatewayre**. Azt is megteheti, hogy a jobb gombbal **Data Gateways** a fanézetben, és kattintson a **új data gatewayre**.

   ![Új data gatewayre eszköztár](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. Az a **létrehozása** lap, adja meg **adftutorialgateway** a a **neve**, és kattintson a **OK**.     

    ![Átjáró-lap létrehozása](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > Ebben az útmutatóban csak egy csomóponttal (a helyi Windows-számítógép) hoz létre a logikai átjáró. Adatkezelési átjáró méretezése több helyszíni gépek társítását az átjáró. Méretezhető beállítása növelje a csomóponton egyidejűleg futtatható adatok mozgását feladatok számát. Ez a funkció egy egyetlen csomóponttal logikai átjáró is érhető el. Lásd: [adatkezelési átjáró méretezés az Azure Data Factoryban](data-factory-data-management-gateway-high-availability-scalability.md) részleteivel.  
4. Az a **konfigurálása** kattintson **telepítheti közvetlenül a számítógépre**. Ez a művelet letölti az átjáró telepítési csomagját, telepíti, konfigurálja és regisztrálja az átjárót a számítógépen.  

   > [!NOTE]
   > Használja az Internet Explorer vagy egy Microsoft ClickOnce-kompatibilis böngészőt.
   >
   > Amennyiben Chrome böngészőt használ, keresse fel a [Chrome webáruházát](https://chrome.google.com/webstore/), keressen rá a „ClickOnce” kulcsszóra, válassza ki az egyik ClickOnce-bővítményt, majd telepítse azt.
   >
   > Tegye meg ugyanezt a Firefox (install-bővítmény). Kattintson a **menü megnyitása** gombra az eszköztáron (**három vízszintes vonal** jobb felső sarokban), kattintson a **bővítmények**, keressen a "ClickOnce" kulcsszóra, válassza ki az egyik a ClickOnce-bővítményt, telepítse azt.    
   >
   >

    ![Átjáró - lapjának konfigurálása](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Ezzel a módszerrel lehet a legkönnyebben (egykattintásos) letöltése, telepítése, konfigurálása és regisztrálnia kell az átjárót egy egyetlen lépésben. Megtekintheti a **Microsoft Data Management Gateway Configuration Manager** alkalmazás telepítve van a számítógépen. Is megtalálhatja a végrehajtható fájl **ConfigManager.exe** mappában: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**.

    Is letöltheti, és ezen a lapon található hivatkozások használatával manuálisan telepíteni az átjárót és látható a kulccsal regisztrálja a **új kulcs** szövegmezőben.

    Lásd: [adatkezelési átjáró](data-factory-data-management-gateway.md) a cikk az átjáró részleteit.

   > [!NOTE]
   > A helyi számítógépen, telepítse és konfigurálja az adatkezelési átjárót sikeresen rendszergazdának kell lennie. További felhasználók is hozzáadhat a **adatkezelési átjáró felhasználói** helyi Windows-csoport. Ez a csoport tagjai a Data Management Gateway Configuration Manager eszköz segítségével az átjáró konfigurálásához.
   >
   >
5. Várjon néhány percig, vagy várjon, amíg megjelenik a következő értesítést:

    ![Átjáró telepítése sikeres](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Indítsa el a **Data Management Gateway Configuration Manager** alkalmazást a számítógépre. Az a **keresési** ablakot, írja be **adatkezelési átjáró** elérni ezt a segédprogramot. Is megtalálhatja a végrehajtható fájl **ConfigManager.exe** mappában: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

    ![Átjáró konfigurációkezelője](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Győződjön meg arról, hogy `adftutorialgateway is connected to the cloud service` üzenet. Az alsó jeleníti meg az állapotsorban **csatlakozva a felhőszolgáltatáshoz** együtt egy **zöld pipa**.

    Az a **kezdőlap** lapon is elvégezheti a következő műveleteket:

   * **Regisztráljon** egy átjárót egy kulccsal a Regisztrálás gombra használatával az Azure Portalról.
   * **Állítsa le** a Data Management Gateway gazdagép szolgáltatás az átjárót tartalmazó számítógépen futó.
   * **Frissítések ütemezése** kell telepíteni a nap egy adott időpontban.
   * Megtekintheti, ha az átjáró volt a **utolsó frissítés**.
   * Adja meg, amelyen az átjáró frissítése telepíthető időpontját.
8. Váltson a **Settings** (Beállítások) lapra. A megadott tanúsítvány a **tanúsítvány** szakasz szolgál a helyszíni adattár, amely a portálon megadott hitelesítő adatok titkosítása/visszafejtése. (nem kötelező) Kattintson a **módosítása** inkább saját tanúsítvány használata. Alapértelmezés szerint az átjáró, amely a Data Factory szolgáltatás által automatikusan létrehozott tanúsítványt használja.

    ![Átjáró-tanúsítvány konfigurálása](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    A következő műveleteket is végrehajthatja, a **beállítások** lapon:

   * Megtekintheti, és exportálja az átjáró által használt tanúsítvány.
   * Módosítsa a HTTPS-végpontot, az átjáró által használt.    
   * Állítsa be az átjáró által használandó egy HTTP-proxyt.     
9. (nem kötelező) Váltson a **diagnosztikai** lapon jelölje a **részletes naplózás engedélyezése** beállítást, ha engedélyezi a részletes naplózást, amely az átjáró az esetleges problémák megoldásához használható. A naplózási adatok található **Eseménynapló** alatt **alkalmazások és szolgáltatásnaplók** -> **adatkezelési átjáró** csomópont.

    ![Diagnosztika lap](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Az a következő műveleteket is elvégezheti a **diagnosztikai** lapon:

   * Használat **kapcsolat tesztelése** szakasz egy helyszíni adatforráshoz az átjárót.
   * Kattintson a **naplók megtekintése** egy Eseménynapló-ablakban a Data Management Gateway napló megtekintéséhez.
   * Kattintson a **naplók küldése** a naplók az elmúlt hét nap egy zip-fájl feltöltése a Microsoftnak a problémák a hibaelhárítás megkönnyítése érdekében.
10. A a **diagnosztikai** lap a **kapcsolat tesztelése** szakaszban jelölje be **SqlServer** az adattár típusa, adja meg a nevét, az adatbázis-kiszolgáló, az adatbázis neve Adja meg a hitelesítési típust, adja meg a felhasználónevet és jelszót, majd kattintson **tesztelése** annak megállapítására, hogy az átjáró képes kapcsolódni az adatbázishoz.
11. Váltson a webböngészőt, majd a a **az Azure portal**, kattintson a **OK** a a **konfigurálása** lap majd a a **új data gatewayre** lapot.
12. Megtekintheti az **adftutorialgateway** alatt **Data Gateways** a bal oldali fanézetben.  Ha rákattint, a kapcsolódó JSON kell megjelennie.

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Ebben a lépésben két társított szolgáltatást hoz létre: **AzureStorageLinkedService** és **SqlServerLinkedService**. A **SqlServerLinkedService** kapcsolja össze egy helyszíni SQL Server-adatbázis és a **AzureStorageLinkedService** társított szolgáltatás egy Azure blob-tárolót az adat-előállító hivatkozásokat tartalmaz. Létrehoz egy folyamatot, amely adatokat másol a helyszíni SQL Server-adatbázist az Azure blob-tároló az útmutató későbbi részeiben.

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>A társított szolgáltatás egy helyszíni SQL Server-adatbázis hozzáadása
1. Az a **Data Factory Editor**, kattintson a **új adattároló** az eszköztáron, majd válassza **SQL Server**.

   ![Új SQL Server-alapú társított szolgáltatás](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. Az a **JSON-szerkesztő** a jobb oldalon tegye a következőket:

   1. Az a **átjárónév**, adja meg **adftutorialgateway**.    
   2. Az a **connectionString**, tegye a következőket:    

      1. A **servername**, adja meg az SQL Server-adatbázist üzemeltető kiszolgáló nevét.
      2. A **databasename**, adja meg az adatbázis nevét.
      3. Kattintson a **titkosítása** gombra az eszköztáron. Láthatja, hogy a hitelesítő adatok kezelőjének alkalmazást.

         ![Hitelesítő adatok Manager alkalmazás](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. Az a **hitelesítő adatok beállítása** párbeszédpanelen adja meg a hitelesítési típus, a felhasználónevet és jelszót, és kattintson a **OK**. Ha a kapcsolat létrejött, a titkosított hitelesítő adatokat a JSON-fájlban tárolódnak, és a párbeszédpanel bezárul.
      5. Zárja be a párbeszédpanel indul el, ha nem automatikusan lezárva üres a böngészőlapot, és az Azure Portalon tartalmazó lapra való visszatéréshez.

         Az átjárót tartalmazó számítógépen, ezek a hitelesítő adatok vannak **titkosított** tanúsítvánnyal, amely a Data Factory szolgáltatás tulajdonosa. Ha azt szeretné, hogy inkább az adatkezelési átjáró társítva van a tanúsítvány használatára, lásd: [hitelesítő adatok beállítása a biztonságos](#set-credentials-and-security).    
   3. Kattintson a **telepítés** a parancssávon az SQL Server telepítéséhez társított szolgáltatást. A társított szolgáltatást, a fanézetben megtekintheti.

      ![SQL Server társított szolgáltatásának a faszerkezetes nézetben](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Társított szolgáltatás egy Azure storage-fiók hozzáadása
1. Az a **Data Factory Editor**, kattintson a **új adattároló** a parancssávon, majd **az Azure storage**.
2. Adja meg az Azure storage-fiók nevét a **fióknév**.
3. Adja meg a kulcsot az Azure storage-fiók a **fiókkulcs**.
4. Kattintson a **telepítés** üzembe helyezéséhez a **AzureStorageLinkedService**.

## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben olyan bemeneti és kimeneti adatkészleteket hoz létre, amelyek a másolási művelet be- és kimeneti adatait képezik majd (Helyszíni SQL Server-adatbázis => Azure Blob Storage). Az adatkészletek létrehozása előtt végezze el a következő lépéseket (a részletes lépések a lista után olvashatóak):

* Hozzon létre egy **emp** nevű táblát az adat-előállítóhoz társított szolgáltatásként hozzáadott SQL Server-adatbázisban, és szúrjon be a táblába néhány mintabejegyzést.
* Hozzon létre egy **adftutorial** nevű Blob-tárolót az adat-előállítóhoz társított szolgáltatásként hozzáadott Azure Blob Storage-fiókban.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>A helyszíni SQL Server előkészítése az oktatóanyaghoz
1. A helyszíni SQL Server-alapú társított szolgáltatáshoz (**SqlServerLinkedService**) megadott adatbázisban a következő SQL-szkripttel hozza létre az **emp** táblát.

    ```SQL   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. Szúrjon be a táblába néhány mintabejegyzést:

    ```SQL
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="create-input-dataset"></a>Bemeneti adatkészlet létrehozása

1. A **Data Factory Editorban** kattintson ide: **... További**, kattintson a **új adatkészlet** a parancssávon, majd kattintson a **SQL Server-táblát**.
2. Cserélje le a JSON a jobb oldali panelen a következő szöveget:

    ```JSON   
    {        
        "name": "EmpOnPremSQLTable",
        "properties": {
            "type": "SqlServerTable",
            "linkedServiceName": "SqlServerLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }     
    ```     
   Vegye figyelembe a következő szempontokat:

   * **típus** értékre van állítva **SqlServerTable**.
   * **Táblanév** értékre van állítva **emp**.
   * **linkedServiceName** értékre van állítva **SqlServerLinkedService** (a hozta létre ezt a társított szolgáltatást korábban ebben a bemutatóban.).
   * A bemeneti adatkészlethez, amely nem jön létre az Azure Data Factory egy másik folyamat, meg kell **külső** való **igaz**. Ez azt jelzi, hogy a bemeneti adatok az Azure Data Factory szolgáltatásban külső jön létre. Opcionálisan megadhat bármilyen külső adatok házirendek segítségével a **externalData** eleme a **házirend** szakasz.    

   Lásd: [adatok áthelyezése SQLServer/](data-factory-sqlserver-connector.md) JSON-tulajdonságokkal kapcsolatos részletekért.
3. Kattintson a **telepítés** a parancssávon az adatkészlet üzembe helyezéséhez.  

### <a name="create-output-dataset"></a>Kimeneti adatkészlet létrehozása

1. Az a **Data Factory Editor**, kattintson a **új adatkészlet** a parancssávon, majd kattintson a **Azure Blob storage**.
2. Cserélje le a JSON a jobb oldali panelen a következő szöveget:

    ```JSON   
    {
        "name": "OutputBlobTable",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/outfromonpremdf",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```   
   Vegye figyelembe a következő szempontokat:

   * **típus** értékre van állítva **AzureBlob**.
   * **linkedServiceName** értékre van állítva **AzureStorageLinkedService** (Ön hozta létre ezt a társított szolgáltatást a 2. lépésben).
   * **folderPath** értékre van állítva **adftutorial/outfromonpremdf** ahol outfromonpremdf-e a mappát az adftutorial tárolóban. Hozzon létre a **adftutorial** tárolót, ha azt nem létezik.
   * Az **availability** (rendelkezésre állás) paraméter **hourly** (óránként) értékre van állítva (a **frequency** (gyakoriság) paraméter **hour** (óra), az **interval** (időköz) paraméter pedig **1** értékre).  A Data Factory szolgáltatás létrehoz egy kimeneti adatszeletet az óránként a **emp** táblát az Azure SQL Database-ben.

   Ha nem ad meg egy **fileName** a egy **kimeneti tábla**, az előállított fájlok a **folderPath** a következő formátumban lesznek elnevezve: Data.<Guid>. txt típusú (például:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Beállítása **folderPath** és **fileName** dinamikusan alapján a **SliceStart** idő, a "partitionedBy" tulajdonsággal. A következő példában a folderPath tulajdonság a SliceStart (a feldolgozás alatt álló szelet kezdő időpontja) változó Év, Hónap és Nap értékeit, a fileName tulajdonság pedig a SliceStart változó Óra értékét használja. Ha például a szelet előállítása a 2014-10-20T08:00:00 időpontban kezdődik, a folderName tulajdonság beállítása wikidatagateway/wikisampledataout/2014/10/20, a fileName beállítása pedig 08.csv lesz.

    ```JSON
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
    [

        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],
    ```

    Lásd: [adatok áthelyezése Azure Blob Storage-ból/](data-factory-azure-blob-connector.md) JSON-tulajdonságokkal kapcsolatos részletekért.
3. Kattintson a **telepítés** a parancssávon az adatkészlet üzembe helyezéséhez. Győződjön meg arról, hogy mindkét az adatkészletek a fanézetben.  

## <a name="create-pipeline"></a>Folyamat létrehozása
Ebben a lépésben hozzon létre egy **folyamat** egy **másolási tevékenység** használó **EmpOnPremSQLTable** bemenetként, és **OutputBlobTable** , kimenet.

1. A Data Factory Editorban kattintson **... Továbbiak**, majd az **Új adatcsatorna elemre.
2. Cserélje le a JSON a jobb oldali panelen a következő szöveget:    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on-prem SQL server to blob",
             "type": "Copy",
             "inputs": [
               {
                 "name": "EmpOnPremSQLTable"
               }
             ],
             "outputs": [
               {
                 "name": "OutputBlobTable"
               }
             ],
             "typeProperties": {
               "source": {
                 "type": "SqlSource",
                 "sqlReaderQuery": "select * from emp"
               },
               "sink": {
                 "type": "BlobSink"
               }
             },
             "Policy": {
               "concurrency": 1,
               "executionPriorityOrder": "NewestFirst",
               "style": "StartOfInterval",
               "retry": 0,
               "timeout": "01:00:00"
             }
           }
         ],
         "start": "2016-07-05T00:00:00Z",
         "end": "2016-07-06T00:00:00Z",
         "isPaused": false
       }
     }
    ```   
   > [!IMPORTANT]
   > A **start** (kezdés) tulajdonság értékét cserélje az aktuális, az **end** (befejezés) tulajdonság értékét pedig a következő napra.
   >
   >

   Vegye figyelembe a következő szempontokat:

   * A tevékenységek szakaszban csak tevékenység van amelynek **típus** értékre van állítva **másolási**.
   * **Bemeneti** a tevékenység bemenetének beállítása **EmpOnPremSQLTable** és **kimeneti** a tevékenység bemenetének beállítása **OutputBlobTable**.
   * Az a **typeProperties** szakaszban **SqlSource** a következőként van megadva a **adatforrástípust** és ** BlobSink ** a következőként van megadva a **fogadó típusa**.
   * SQL-lekérdezés `select * from emp` van megadva a **sqlReaderQuery** tulajdonsága **SqlSource**.

   Mind a kezdő, mind a befejező dátum-időpont értéket [ISO formátumban](http://en.wikipedia.org/wiki/ISO_8601) kell megadni. Például: 2014-10-14T16:32:41Z. Az **end** (befejező) időpont megadása opcionális, a jelen oktatóanyagban azonban azt is használjuk.

   Ha nem adja meg az **end** (befejezés) tulajdonság értékét, akkor a rendszer a „**kezdő időpont + 48 óra**” számítással határozza meg azt. A folyamat határozatlan ideig történő futtatásához adja meg a **9/9/9999** értéket az **end** (befejezés) tulajdonsághoz.

   Megadja az időtartamot, amelyben az adatszeletek feldolgozása alapján a **rendelkezésre állási** minden egyes Azure Data Factory-adatkészlet definiált tulajdonságaihoz.

   A fenti példában 24 adatszelet jön létre, mivel a nap minden órájában készül egy adatszelet.        
3. Kattintson a **telepítés** a parancssávon az adatkészlet üzembe helyezéséhez (a tábla egy téglalap alakú adatkészlet). Győződjön meg arról, hogy a folyamat megjelenik-e a faszerkezetes nézetében **folyamatok** csomópont.  
4. Most kattintson **X** való visszatéréshez az oldal gombra kétszer a **adat-előállító** lapján a **ADFTutorialOnPremDF**.

**Gratulálunk!** Sikeresen létrehozott egy Azure data factoryt, társított szolgáltatásokat, adatkészleteket és egy folyamatot, és ütemezte is a folyamatot.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>A data factory megtekintése diagramnézetben
1. Az a **az Azure portal**, kattintson a **Diagram** kezdőlapján csempét a **ADFTutorialOnPremDF** adat-előállító. :

    ![Diagram-hivatkozás](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Az alábbi képhez hasonló diagramnak kell megjelennie:

    ![Diagramnézet](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Nagyítás, Kicsinyítés, kicsinyíthet, 100 % nagyítás fér, automatikusan elhelyezheti a folyamatokat és az adatkészletek és leszármaztatási információkat (kiemeli a kijelölt elemek fölérendelt vagy alárendelt elemeinek).  Kattintson duplán egy objektumra (bemeneti/kimeneti adatkészlet vagy folyamatok) megtekintheti a tulajdonságait.

## <a name="monitor-pipeline"></a>Folyamat figyelése
Ebben a lépésben az Azure Portal használatával figyeli egy Azure data factory eseményeit. PowerShell-parancsmagokat is használhat az adatkészletek és folyamatok figyeléséhez. Figyelésével kapcsolatos részletekért lásd: [folyamatok figyelése és felügyelete](data-factory-monitor-manage-pipelines.md).

1. Kattintson duplán a diagram **EmpOnPremSQLTable**.  

    ![EmpOnPremSQLTable szeletek](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Figyelje meg, hogy az adatok a szeleteket be mind a **készen** állapot, mert ez a folyamat időtartama (kezdő időpontja és Befejezés időpontja). Ez azért is az adatok beszúrása az SQL Server-adatbázisban, és van folyamatosan. Győződjön meg arról, hogy nincs szeletek megjelennek a **problémás szeletek** alul található szakaszában. Az összes szelet megjelenítéséhez kattintson **Továbbiak** szeletek hosszabb listájának alján.
3. Most a **adatkészletek** kattintson **OutputBlobTable**.

    ![OputputBlobTable szeletek](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Kattintson a listában lévő bármelyik adatszeletre, és megjelenik a **Adatszelet** lapot. A szelet tevékenységfuttatásokat fog látni. Általában csak egy tevékenységfuttatás megjelenik.  

    ![Data Slice panel](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Ha a szelet nem **Ready** (Kész) állapotú, az **Upstream slices that are not ready** (Nem kész állapotú fölérendelt szeletek) listában láthatja azokat a nem kész állapotú fölérendelt szeleteket, amelyek blokkolják az aktuális szelet végrehajtását.
5. Kattintson a **tevékenységfuttatás** tekintse meg az alsó listából **az activity run details**.

   ![Tevékenység Futtatás részletei lap](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Átviteli sebesség, időtartama és az adatok átviteléhez használt átjáró jelennének meg.
6. Kattintson a **X** gombra kattintva zárja be az oldalak, amíg ki nem
7. a kezdőlapja való visszatéréshez a **ADFTutorialOnPremDF**.
8. (nem kötelező) Kattintson a **folyamatok**, kattintson a **ADFTutorialOnPremDF**, és lefúrással megjelenítheti a bemeneti táblák (**Consumed**) vagy a kimeneti adatkészleteket (**Produced**).
9. Például használja az eszközök [Microsoft Storage Explorer](http://storageexplorer.com/) , győződjön meg arról, hogy egy blob/fájl jön létre minden egyes órában.

   ![Azure Storage Explorer](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>További lépések
* Lásd: [adatkezelési átjáró](data-factory-data-management-gateway.md) az adatkezelési átjáró részleteit ismertető cikket.
* Lásd: [adatok másolása az Azure-Blobból Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) további információ a másolási tevékenység használatával helyezze át az adatokat egy forrásadattárból egy fogadó adattárba.
