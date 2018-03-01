---
title: "Adatok - az adatkezelési átjáró áthelyezése |} Microsoft Docs"
description: "Állítsa be a data gateway áthelyezni az adatokat a helyszíni és a felhő között. Az Azure Data Factoryben az adatkezelési átjáró segítségével az adatok áthelyezése."
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: monicar
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 49683da9f03871cbe5c27dd06d27111864eb489e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Adatok áthelyezése a helyszíni adatforrások és az adatkezelési átjáró a felhő között
> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [között a helyszíni adatok másolása, és a Data Factory 2-es verzióját használó felhőalapú](../tutorial-hybrid-copy-powershell.md).

Ez a cikk áttekintést adatok integrálása a helyszíni adattárolókhoz és a Data Factory használatával felhő adattárolók között. Az épít, a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk és az egyéb adatok gyári alapvető fogalmait: [adatkészletek](data-factory-create-datasets.md) és [folyamatok](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Adatkezelési átjáró
A helyszíni gépen belőle egy helyszíni adattároló áthelyezése adatok engedélyezéséhez telepítenie kell az adatkezelési átjáró. Az átjáró telepíthető adattárként ugyanazon a számítógépen vagy egy másik számítógépen, amíg az átjáró képes kapcsolódni az adattárolóhoz.

> [!IMPORTANT]
> Lásd: [az adatkezelési átjáró](data-factory-data-management-gateway.md) szóló cikkben olvashat az adatkezelési átjáró. 

A következő forgatókönyv bemutatja, hogyan hozzon létre egy folyamatot, amely egy helyszíni helyezi át az adatokat egy adat-előállító **SQL Server** egy Azure blob storage-adatbázist. A bemutató részeként telepíti és konfigurálja az adatkezelési átjárót a gépen.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Forgatókönyv: a helyszíni adatok felhőbe másolása
Ez a forgatókönyv a hajtsa végre az alábbi lépéseket: 

1. Adat-előállító létrehozása
2. Hozzon létre egy az adatkezelési átjáró. 
3. A forrás és a fogadó adattárolókhoz társított szolgáltatások létrehozásához.
4. Hozzon létre a bemeneti és kimeneti adatok adatkészletek.
5. Folyamat létrehozása másolási tevékenységgel az adatok áthelyezéséhez

## <a name="prerequisites-for-the-tutorial"></a>Az oktatóanyag előfeltételei
Ez a forgatókönyv megkezdése előtt rendelkeznie kell a következő előfeltételek teljesülését:

* **Azure-előfizetés**.  Ha nem rendelkezik előfizetéssel, mindössze néhány perc alatt létrehozhat egy ingyenes próbafiókot. Tekintse meg a [ingyenes](http://azure.microsoft.com/pricing/free-trial/) cikkben alább.
* **Azure Storage-fiók**. A blob Storage tárolót használja a **cél/fogadó** ebben az oktatóanyagban az adattároló. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket.
* **Egy SQL Server**. Ebben az oktatóanyagban egy helyszíni SQL Server-adatbázist használunk **forrásadattárként**. 

## <a name="create-data-factory"></a>Data factory létrehozása
Ebben a lépésben az Azure Data Factory nevű példány létrehozásához használhatja az Azure-portálon **ADFTutorialOnPremDF**.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Kattintson a **hozzon létre egy erőforrást**, kattintson a **Eszközintelligencia + analitika**, és kattintson **adat-előállító**.

   ![New (Új)->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. Az a **új adat-előállító** lapján adja meg **ADFTutorialOnPremDF** nevét.

    ![A kezdőpulton rögzít hozzáadása](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a hibaüzenetet kapja: **nem érhető el adat-előállító "ADFTutorialOnPremDF"**, változtassa meg az adat-előállítóban (például yournameADFTutorialOnPremDF) nevét, és próbálja meg újra létrehozni. Használja ezt a nevet ADFTutorialOnPremDF helyett fennmaradó lépéseit az oktatóanyag végrehajtása közben.
   >
   > A data factory neve később **DNS**-névként regisztrálható, így nyilvánosan láthatóvá válhat.
   >
   >
4. Jelölje ki azt az **Azure-előfizetést**, ahol létre szeretné hozni a data factoryt.
5. Jelöljön ki egy meglévő **erőforráscsoportot**, vagy hozzon létre egyet. Az oktatóanyag nevű erőforráscsoport létrehozása: **ADFTutorialResourceGroup**.
6. Kattintson a **létrehozása** a a **új adat-előállító** lap.

   > [!IMPORTANT]
   > Data Factory-példány létrehozásához a [Data Factory közreműködője](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) szerepkör tagjának kell lennie az előfizetés/erőforráscsoport szintjén.
   >
   >
7. Létrehozásának befejezése után megjelenik a **adat-előállító** lapon a következő ábrán látható módon:

   ![Data Factory kezdőlap](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Átjáró létrehozása
1. A a **adat-előállító** lapján kattintson **Szerző és központi telepítése** csempe elindíthatja a **szerkesztő** esetében az adat-előállítóban.

    ![Az Author and deploy (Fejlesztés és üzembe helyezés) csempe](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. Kattintson a Data Factory Editor **... További** az eszköztár majd **az új adatátjáró**. Másik lehetőségként kattintson a jobb egérgombbal **Data Gateways** a fanézetben, és kattintson a **az új adatátjáró**.

   ![Az új adatátjáró eszköztár](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. Az a **létrehozása** lapján adja meg **adftutorialgateway** a a **neve**, és kattintson a **OK**.     

    ![Átjáró lap létrehozása](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > Ebben a bemutatóban hoz létre a logikai átjáró csak egy csomópont (a helyi Windows-számítógépen). Az adatkezelési átjáró kiterjesztése több helyszíni gépet társít az átjárót. Méretezheti növelésével csomóponton egyidejűleg futtatható az adatátviteli feladatok száma legfeljebb. Ez a szolgáltatás egy logikai átjárójának egyetlen csomópont is érhető el. Lásd: [méretezés az adatkezelési átjáró az Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) cikkben alább.  
4. Az a **konfigurálása** kattintson **telepíthető közvetlenül a számítógépen**. Ez a művelet letölti az átjáró telepítési csomagját, telepíti, konfigurálja és regisztrálja az átjárót a számítógépen.  

   > [!NOTE]
   > Az Internet Explorer vagy a Microsoft ClickOnce kompatibilis böngésző használata.
   >
   > Amennyiben Chrome böngészőt használ, keresse fel a [Chrome webáruházát](https://chrome.google.com/webstore/), keressen rá a „ClickOnce” kulcsszóra, válassza ki az egyik ClickOnce-bővítményt, majd telepítse azt.
   >
   > Tegye meg ugyanezt a Firefox (install-bővítmény). Kattintson a **menü megnyitása** gomb az eszköztáron (**három vízszintes vonal** jobb felső sarkában), kattintson a **bővítmények**, keressen a "ClickOnce" kulcsszóval, válasszon egyet a ClickOnce-bővítmények, és telepítse.    
   >
   >

    ![Átjáró - lapjának konfigurálása](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Ez módja a legegyszerűbb (egykattintásos) letöltése, telepítése, konfigurálása, és regisztrálja az átjárót egy egyetlen lépésben. Megtekintheti a **Microsoft adatkezelési átjáró konfigurációkezelőjének** alkalmazás telepítve van a számítógépen. A végrehajtható fájl is található **ConfigManager.exe** a mappában: **C:\Program Files\Microsoft Data felügyeleti Gateway\2.0\Shared**.

    Is letöltheti és ezen a lapon található hivatkozások használatával manuálisan telepíteni az átjárót és látható-e a kulccsal regisztrálható a **új kulcs** szövegmezőben.

    Lásd: [az adatkezelési átjáró](data-factory-data-management-gateway.md) szóló cikkben az átjáró olvashat.

   > [!NOTE]
   > A helyi számítógépen történő telepítése és konfigurálása sikeresen megtörtént az adatkezelési átjáró rendszergazdának kell lennie. További felhasználók számára is hozzáadhat a **adatkezelési átjáró felhasználói** helyi Windows-csoport. A csoport tagjai az adatkezelési átjáró konfigurációkezelőjének eszköz segítségével konfigurálhatja az átjárót.
   >
   >
5. Várjon néhány percet, vagy várjon, amíg a következő értesítési üzenetet látja:

    ![Átjáró telepítése sikeres](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Indítsa el **az adatkezelési átjáró konfigurációkezelőjének** alkalmazás a számítógépre. Az a **keresési** ablakot, írja be **az adatkezelési átjáró** elérni ezt a segédprogramot. A végrehajtható fájl is található **ConfigManager.exe** a mappában: **C:\Program Files\Microsoft Data felügyeleti Gateway\2.0\Shared**

    ![Átjáró konfigurációkezelője](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Ellenőrizze, hogy látható `adftutorialgateway is connected to the cloud service` üzenet. Az alsó jeleníti meg az állapotsorban **a felhőalapú szolgáltatáshoz való kapcsolódás** együtt egy **zöld pipa**.

    Az a **Home** lapon is végrehajthatja a következő műveleteket:

   * **Regisztrálni** kulccsal átjáró regisztrálása gomb segítségével Azure-portálról.
   * **Állítsa le** az adatkezelési átjáró gazdaszolgáltatás az átjáró gépen futó.
   * **Frissítések ütemezése** telepíteni kell a nap adott időpontban.
   * Ha az átjáró megtekintése **utolsó frissítés**.
   * Adja meg, amelyen az átjáró frissítése telepíthető időpontját.
8. Váltson a **Settings** (Beállítások) lapra. A megadott tanúsítvány a **tanúsítvány** szakasz titkosítása/visszafejtése a hitelesítő adatokat a helyszíni adattároló meg a portál használatával. (választható) Kattintson a **módosítás** használhatja saját tanúsítvány is. Alapértelmezés szerint az átjáró a Data Factory szolgáltatásnak automatikusan létrehozza a tanúsítványt használja.

    ![Átjáró tanúsítvány konfigurálása](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Ön is végrehajthatja a következő műveleteket a **beállítások** lapon:

   * Tekinthetők meg és exportálja a tanúsítványt, az átjáró éppen használják.
   * Módosítsa az átjáró által használt HTTPS-végponton.    
   * Állítsa be az átjáró által használt HTTP-proxy.     
9. (választható) Váltás a **diagnosztika** lapon ellenőrizze a **részletes naplózás engedélyezése** lehetőséget, ha engedélyezi a részletes naplózás, amely segítségével hárítsa el a regisztrációt az átjárón. A naplózási adatok található **Eseménynapló** alatt **alkalmazási és Szolgáltatásnaplójában** -> **az adatkezelési átjáró** csomópont.

    ![Diagnosztika lap](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    A következő műveleteket is elvégezheti a **diagnosztika** lapon:

   * Használjon **kapcsolat tesztelése** szakasz az átjáró helyszíni adatforráshoz.
   * Kattintson a **naplók megtekintése** egy Eseménynapló-ablakban az adatkezelési átjáró napló megjelenítéséhez.
   * Kattintson a **naplók küldése** egy zip-fájlt az elmúlt hét napban rögzít feltöltése a Microsoftnak a problémák hibaelhárításához megkönnyítése érdekében.
10. Az a **diagnosztika** lap a **kapcsolat tesztelése** szakaszban jelölje be **SqlServer** az adatok tárolására, adja meg a nevét, az adatbázis-kiszolgáló, az adatbázis nevét, adja meg a hitelesítés típusát, adja meg a felhasználónevet és jelszót és kattintson a **tesztelése** annak megállapítására, hogy az átjáró képes kapcsolódni az adatbázishoz.
11. A webböngészőt, és a kapcsoló a **Azure-portálon**, kattintson a **OK** a a **konfigurálása** lap majd a a **az új adatátjáró** lap.
12. Megtekintheti az **adftutorialgateway** alatt **Data Gateways** a bal oldali fanézetben.  Kattintson rá, ha a kapcsolódó JSON kell megjelennie.

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Ebben a lépésben két társított szolgáltatások létrehozásához: **AzureStorageLinkedService** és **SqlServerLinkedService**. A **SqlServerLinkedService** hivatkozásokat tartalmaz a helyszíni SQL Server adatbázis és a **AzureStorageLinkedService** társított szolgáltatás hivatkozásokat tartalmaz egy Azure blob-tároló az adat-előállítóban. Egy folyamat később Ez a forgatókönyv az Azure blob-tárolóba másolja át adatokat a helyszíni SQL Server-adatbázis létrehozása.

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>A társított szolgáltatás hozzá egy helyi SQL Server-adatbázis
1. Az a **Data Factory Editor**, kattintson a **az új adattároló** az eszköztáron és a select **SQL Server**.

   ![Új kapcsolódó SQL Server szolgáltatás](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. Az a **JSON-szerkesztő** jobb, tegye a következőket:

   1. Az a **gatewayName**, adja meg **adftutorialgateway**.    
   2. Az a **connectionString**, hajtsa végre a következő lépéseket:    

      1. A **kiszolgálónév**, adja meg az SQL Server-adatbázist futtató kiszolgáló nevét.
      2. A **databasename**, adja meg az adatbázis nevét.
      3. Kattintson a **titkosítása** gomb az eszköztáron. A hitelesítőadat-kezelője alkalmazás láthatja.

         ![Hitelesítő adatok Manager alkalmazásban](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. Az a **hitelesítő adatok beállítása a** párbeszédpanelen adja meg a hitelesítés típusa, a felhasználónevet és jelszót, majd kattintson **OK**. Ha a kapcsolódás sikeres, a titkosított hitelesítő adatok kerülnek a JSON és a párbeszédpanel bezárása után.
      5. Zárja be, amely elindítja a párbeszédpanel, ha nem automatikusan lezárja üres böngészőlapon, és visszatérhet a lapon, az Azure portálon.

         Az átjáró számítógépén, ezek a hitelesítő adatok vannak **titkosított** a Data Factory szolgáltatásnak birtokló tanúsítvánnyal. Ha azt szeretné, hogy a tanúsítványt az adatkezelési átjáró társított helyette használandó [biztonságosan használandó hitelesítő adatok beállításához](#set-credentials-and-security).    
   3. Kattintson a **telepítés** a parancssávon az SQL Server telepítéséhez a társított szolgáltatás. Meg kell jelennie a fanézetben a társított szolgáltatás.

      ![Kapcsolódó SQL Server szolgáltatás a faszerkezetes nézetben](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>A társított szolgáltatás az Azure-tárfiók hozzáadása
1. Az a **Data Factory Editor**, kattintson a **az új adattároló** a parancssávon, majd kattintson a **az Azure storage**.
2. Adja meg az az Azure storage-fiók nevét a **fióknév**.
3. Adja meg a kulcsot az Azure storage-fiókjához tartozó a **fiókkulcs**.
4. Kattintson a **telepítés** központi telepítése a **AzureStorageLinkedService**.

## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben olyan bemeneti és kimeneti adatkészleteket hoz létre, amelyek a másolási művelet be- és kimeneti adatait képezik majd (Helyszíni SQL Server-adatbázis => Azure Blob Storage). Az adatkészletek létrehozása előtt végezze el a következő lépéseket (a részletes lépések a lista után olvashatóak):

* Hozzon létre egy **emp** nevű táblát az adat-előállítóhoz társított szolgáltatásként hozzáadott SQL Server-adatbázisban, és szúrjon be a táblába néhány mintabejegyzést.
* Hozzon létre egy **adftutorial** nevű Blob-tárolót az adat-előállítóhoz társított szolgáltatásként hozzáadott Azure Blob Storage-fiókban.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>A helyszíni SQL Server az oktatóanyag előkészítéséhez
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

1. A **Data Factory Editorban** kattintson ide: **... További**, kattintson a **új adatkészlet** a parancssávon, majd kattintson a **SQL Server tábla**.
2. A jobb oldali JSON cserélje le a következő szöveget:

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

   * **típus** értéke **SqlServerTable**.
   * **tableName** értéke **üres**.
   * **linkedServiceName** értéke **SqlServerLinkedService** (korábban létrehozott szolgáltatásnak a forgatókönyv során korábban küldje el.).
   * Egy bemeneti adatkészlet nem az Azure Data Factory egy másik folyamat által generált, be kell **külső** való **igaz**. Az azt jelzi, hogy a bemeneti adatokból keletkezik külső az Azure Data Factory szolgáltatásnak. Opcionálisan megadhat bármilyen külső adatok házirendekkel a **externalData** eleme a **házirend** szakasz.    

   Lásd: [helyezi át az adatokat az SQL Server](data-factory-sqlserver-connector.md) JSON-tulajdonságok vonatkozó további információért.
3. Kattintson a **telepítés** a parancssávon az adatkészlet telepítéséhez.  

### <a name="create-output-dataset"></a>Kimeneti adatkészlet létrehozása

1. Az a **Data Factory Editor**, kattintson a **új adatkészlet** a parancssávon, majd kattintson a **Azure Blob Storage tárolóban**.
2. A jobb oldali JSON cserélje le a következő szöveget:

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

   * **típus** értéke **AzureBlob**.
   * **linkedServiceName** értéke **AzureStorageLinkedService** (hozna létre szolgáltatásnak a 2. lépés).
   * **folderPath** értéke **adftutorial/outfromonpremdf** outfromonpremdf esetén a adftutorial tároló mappa. Hozzon létre a **adftutorial** tárolót, ha még nem létezik.
   * Az **availability** (rendelkezésre állás) paraméter **hourly** (óránként) értékre van állítva (a **frequency** (gyakoriság) paraméter **hour** (óra), az **interval** (időköz) paraméter pedig **1** értékre).  A Data Factory szolgáltatásnak hoz létre egy kimeneti az adatszelet az óránként a **üres** az Azure SQL adatbázis táblája.

   Ha nem ad meg egy **Fájlnév** a egy **eredménytábla**, a létrehozott fájlokat a **folderPath** elnevezése a következő formátumban: adatok.<Guid>. txt (például:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Beállítása **folderPath** és **Fájlnév** dinamikusan alapján a **SliceStart** idő, a "partitionedBy" tulajdonsággal. A következő példában a folderPath tulajdonság a SliceStart (a feldolgozás alatt álló szelet kezdő időpontja) változó Év, Hónap és Nap értékeit, a fileName tulajdonság pedig a SliceStart változó Óra értékét használja. Ha például a szelet előállítása a 2014-10-20T08:00:00 időpontban kezdődik, a folderName tulajdonság beállítása wikidatagateway/wikisampledataout/2014/10/20, a fileName beállítása pedig 08.csv lesz.

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

    Lásd: [helyezi át az adatokat az Azure Blob Storage](data-factory-azure-blob-connector.md) JSON-tulajdonságok vonatkozó további információért.
3. Kattintson a **telepítés** a parancssávon az adatkészlet telepítéséhez. Győződjön meg arról, hogy megjelenik-e a fanézetben mindkét adathalmazokat.  

## <a name="create-pipeline"></a>Folyamat létrehozása
Ebben a lépésben létrehoz egy **csővezeték** egy **másolási tevékenység** használó **EmpOnPremSQLTable** bemenetként és **OutputBlobTable** output típusúként.

1. Kattintson a Data Factory Editor **... Továbbiak**, majd az **Új adatcsatorna** elemre.
2. A jobb oldali JSON cserélje le a következő szöveget:    

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

   * A tevékenységek szakasz csak a tevékenység nincs amelynek **típus** értéke **másolási**.
   * **Bemeneti** az a tevékenység állítsa **EmpOnPremSQLTable** és **kimeneti** az a tevékenység állítsa **OutputBlobTable**.
   * Az a **typeProperties** szakaszban **SqlSource** van megadva a **adatforrástípust** és ** BlobSink ** van megadva a **típus gyűjtése**.
   * SQL-lekérdezés `select * from emp` van megadva a **sqlReaderQuery** tulajdonsága **SqlSource**.

   Mind a kezdő, mind a befejező dátum-időpont értéket [ISO formátumban](http://en.wikipedia.org/wiki/ISO_8601) kell megadni. Például: 2014-10-14T16:32:41Z. Az **end** (befejező) időpont megadása opcionális, a jelen oktatóanyagban azonban azt is használjuk.

   Ha nem adja meg az **end** (befejezés) tulajdonság értékét, akkor a rendszer a „**kezdő időpont + 48 óra**” számítással határozza meg azt. A folyamat határozatlan ideig történő futtatásához adja meg a **9/9/9999** értéket az **end** (befejezés) tulajdonsághoz.

   Megadja az időtartamot, amelyben az adatszeletek feldolgozása alapján a **rendelkezésre állási** minden Azure Data Factory adatkészlet definiált tulajdonságaihoz.

   A fenti példában 24 adatszelet jön létre, mivel a nap minden órájában készül egy adatszelet.        
3. Kattintson a **telepítés** a parancssávon az adatkészlet telepítéséhez (a tábla téglalap alakú dataset). Győződjön meg arról, hogy a fanézetben a megjelennek a feldolgozási sor **folyamatok** csomópont.  
4. Most kattintson **X** kétszer gombra kattintva visszatérhet a lap a **adat-előállító** lap a **ADFTutorialOnPremDF**.

**Gratulálunk!** Sikeresen létrehozott egy az Azure data factory, a társított szolgáltatások, a adatkészletek és a folyamat és a feldolgozási sor ütemezve.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>A data factory megtekintése diagramnézetben
1. Az a **Azure-portálon**, kattintson a **Diagram** csempére a kezdőlap a **ADFTutorialOnPremDF** adat-előállítóban. :

    ![Diagram-hivatkozás](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Az alábbi képhez hasonló diagramnak kell megjelennie:

    ![Diagramnézet](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Nagyítás, Kicsinyítés, 100 %-os, fér, automatikusan adatcsatornákat és adathalmazokat formázása és a Leszármaztatás adatok megjelenítése (kiemeli felsőbb és alsóbb rétegbeli elemeket a kijelölt elemek) nagyítás nagyítása.  Kattintson duplán egy objektum (bemeneti/kimeneti adatkészlet vagy csővezeték) tulajdonságok talál.

## <a name="monitor-pipeline"></a>Folyamat figyelése
Ebben a lépésben az Azure Portal használatával figyeli egy Azure data factory eseményeit. PowerShell-parancsmagokat is használhat az adatkészletek és folyamatok figyeléséhez. Figyelésével kapcsolatos részletekért lásd: [figyelő és folyamatok kezelése](data-factory-monitor-manage-pipelines.md).

1. A diagramban kattintson duplán a **EmpOnPremSQLTable**.  

    ![EmpOnPremSQLTable szeletek](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Figyelje meg, hogy minden be adatszelet van **készen** állapot, mert a folyamat időtartama (kezdete befejező időpontnál) múltbeli. Akkor is, mert az adatok beszúrása az SQL Server-adatbázisban, és nincs folyamatosan. Győződjön meg arról, hogy nincs szeletek megjelennek a **probléma szeletek** szakasz alján. A szeletek megtekintéséhez kattintson **lásd: több** szeletek a lista alján.
3. Most, az a **adatkészletek** kattintson **OutputBlobTable**.

    ![OputputBlobTable szeletek](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Kattintson a bármely adatszelet a listából, és kell megjelennie a **Adatszelet** lap. Megjelenik a tevékenység futtatása a szelet. Csak egy tevékenységfuttatási általában láthatja.  

    ![Adatok szelet panel](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Ha a szelet nem **Ready** (Kész) állapotú, az **Upstream slices that are not ready** (Nem kész állapotú fölérendelt szeletek) listában láthatja azokat a nem kész állapotú fölérendelt szeleteket, amelyek blokkolják az aktuális szelet végrehajtását.
5. Kattintson a **tevékenységfuttatási** tekintse meg az alsó listából **tevékenység fut részletek**.

   ![Tevékenység futtatása részleteit megjelenítő oldalra](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Átviteli sebesség, időtartama és az adatok átviteléhez használt átjáró mutatunk be.
6. Kattintson a **X** , amíg minden lap bezárásához
7. vissza a kezdőlapra, az a **ADFTutorialOnPremDF**.
8. (választható) Kattintson a **folyamatok**, kattintson a **ADFTutorialOnPremDF**, és a bemeneti tábla áthatoló részletezést (**felhasznált**) vagy a kimeneti adathalmazokat (**Produced**).
9. Használjon például az eszközök [Microsoft Tártallózó](http://storageexplorer.com/) ellenőrzése, hogy minden órában létrejön egy blob vagy fájl.

   ![Azure Storage Explorer](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>További lépések
* Lásd: [az adatkezelési átjáró](data-factory-data-management-gateway.md) szóló cikkben az adatkezelési átjáró olvashat.
* Lásd: [adatok másolása az Azure Blob az Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) további másolási tevékenység használatáról a forrás-tárolóban adatok áthelyezése a fogadó tárolóban.
