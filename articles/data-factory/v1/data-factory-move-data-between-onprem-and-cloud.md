---
title: Adatok áthelyezése - Adatkezelési átjáró
description: Állítson be egy adatátjárót az adatok helyszíni és a felhő közötti áthelyezéséhez. Az adatok áthelyezéséhez használja az Azure Data Factory adatkezelési átjáróját.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: be797f76988c924503e11b6f66cce899b515e3a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982197"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Adatok áthelyezése a helyszíni források és a felhő között az Adatkezelési átjáróval
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg az [adatok másolása a helyszíni és a felhőbeli data factory használatával.](../tutorial-hybrid-copy-powershell.md)

Ez a cikk áttekintést nyújt a helyszíni adattárak és a Data Factory használatával a felhőbeli adattárak közötti adatintegrációról. Az [Adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre és más adatfeldolgozó alapfogalmakra épül: [adatkészletek](data-factory-create-datasets.md) és [csővezetékek](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Adatkezelési átjáró
Az adatkezelési átjárót telepítenie kell a helyszíni gépen, hogy lehetővé tegye az adatok áthelyezését a helyszíni adattárba/ onnan. Az átjáró telepíthető ugyanarra a számítógépre, mint az adattár, vagy egy másik gépen, amíg az átjáró csatlakozhat az adattárhoz.

> [!IMPORTANT]
> Az Adatkezelési átjáróval kapcsolatos részleteket az [Adatkezelési átjáróról](data-factory-data-management-gateway.md) szóló cikkben találja.

Az alábbi forgatókönyv bemutatja, hogyan hozhat létre egy adat-előállító egy folyamat, amely áthelyezi az adatokat egy helyszíni **SQL Server-adatbázis** egy Azure blob storage. A bemutató részeként telepíti és konfigurálja az adatkezelési átjárót a gépen.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Forgatókönyv: a helyszíni adatok másolása a felhőbe
Ebben a forgatókönyvben hajtsa végre a következő lépéseket:

1. Adat-előállító létrehozása
2. Hozzon létre egy adatkezelési átjárót.
3. Csatolt szolgáltatások létrehozása forrás- és fogadóadattárakhoz.
4. Hozzon létre adatkészleteket a bemeneti és kimeneti adatok ábrázolására.
5. Folyamat létrehozása másolási tevékenységgel az adatok áthelyezéséhez

## <a name="prerequisites-for-the-tutorial"></a>Az oktatóanyag előfeltételei
A forgatókönyv megkezdése előtt a következő előfeltételekkel kell rendelkeznie:

* **Azure-előfizetés**.  Ha nem rendelkezik előfizetéssel, mindössze néhány perc alatt létrehozhat egy ingyenes próbafiókot. A részleteket lásd az [Ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/) című cikkben.
* **Az Azure Storage-fiók.** A blob storage **célként/fogadó** adattárként használja ebben az oktatóanyagban. ha nem rendelkezik Azure-tárfiókkal, tekintse meg a [tárfiók létrehozása](../../storage/common/storage-account-create.md) cikket a létrehozási lépéseket.
* **SQL Server**kiszolgáló . Ebben az oktatóanyagban egy helyszíni SQL Server-adatbázist használunk **forrásadattárként**.

## <a name="create-data-factory"></a>Data factory létrehozása
Ebben a lépésben az Azure Portal használatával hozzon létre egy **ADFTutorialOnPremDF**nevű Azure Data Factory-példányt.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Kattintson **az Erőforrás létrehozása**, Intelligencia + elemzés menügombra, majd a Data **Factory**parancsra. **Intelligence + analytics**

   ![New (Új)->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. Az **Új adatgyár** lapon adja meg az **ADFTutorialOnPremDF** nevet.

    ![Hozzáadás a starttáblához](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Az Azure data factory nevének globálisan egyedinek kell lennie. Ha megjelenik a hiba: **Data factory name "ADFTutorialOnPremDF" nem áll rendelkezésre**, változtassa meg a nevét, az adatgyár (például yournameADFTutorialOnPremDF), és próbálja meg újra létrehozni. Használja ezt a nevet az ADFTutorialOnPremDF helyett, miközben az oktatóanyag ban további lépéseket hajt végre.
   >
   > Az adatfeldolgozó neve a jövőben **DNS-névként** regisztrálható, és így nyilvánosan láthatóvá válhat.
   >
   >
4. Jelölje ki azt az **Azure-előfizetést**, ahol létre szeretné hozni a data factoryt.
5. Jelöljön ki egy meglévő **erőforráscsoportot**, vagy hozzon létre egyet. Az oktatóanyaghoz hozzon létre egy **ADFTutorialResourceGroup**nevű erőforráscsoportot.
6. Kattintson a **Létrehozás gombra** az **Új adatgyár** lapon.

   > [!IMPORTANT]
   > Data Factory-példány létrehozásához a [Data Factory közreműködője](../../role-based-access-control/built-in-roles.md#data-factory-contributor) szerepkör tagjának kell lennie az előfizetés/erőforráscsoport szintjén.
   >
   >
7. A létrehozás befejezése után megjelenik a **Data Factory** oldal, ahogy az a következő képen látható:

   ![Adatgyár kezdőlapja](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Átjáró létrehozása
1. A **Data Factory** lapon kattintson a **Szerző és a csempe üzembe helyezése** elemre az adat-előállító **szerkesztő** elindításához.

    ![Az Author and deploy (Fejlesztés és üzembe helyezés) csempe](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. A Data Factory Editor, kattintson **... További a** menürendszeraz eszköztáron, majd kattintson az **Új adatátjáró gombra.** Másik lehetőségként kattintson a jobb gombbal az **Adatátjárók** elemre a fanézetben, majd kattintson az **Új adatátjáró parancsra.**

   ![Új adatátjáró az eszköztáron](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. A **Létrehozás** lapon adja meg a **fájlbemutató átjárót** a **névhez,** majd kattintson az **OK**gombra.     

    ![Átjáró létrehozása lap](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > Ebben a forgatókönyvben a logikai átjárót csak egy csomón (helyszíni Windows-gép) hozza létre. Az adatkezelési átjárót úgy skálázhatja ki, hogy több helyszíni gépet társít az átjáróval. A növekvő skálázhatja az adatmozgatási feladatok számának növelésével, amelyek egyidejűleg futtathatók egy csomóponton. Ez a szolgáltatás egy egyetlen csomópontos logikai átjáróesetén is elérhető. A részletekért tekintse meg [az Adatkezelési átjáró méretezése az Azure Data Factory cikkében.](data-factory-data-management-gateway-high-availability-scalability.md)  
4. A **Konfigurálás** lapon kattintson a **Telepítés közvetlenül a számítógépre gombra.** Ez a művelet letölti az átjáró telepítőcsomagját, telepíti, konfigurálja és regisztrálja az átjárót a számítógépen.  

   > [!NOTE]
   > Használja az Internet Explorert vagy a Microsoft ClickOnce kompatibilis webböngészőt.
   >
   > Amennyiben Chrome böngészőt használ, keresse fel a [Chrome webáruházát](https://chrome.google.com/webstore/), keressen rá a „ClickOnce” kulcsszóra, válassza ki az egyik ClickOnce-bővítményt, majd telepítse azt.
   >
   > Tegye ugyanezt a Firefox (install add-in). Kattintson a **Menü megnyitása** gombra az eszköztáron **(három vízszintes vonal** a jobb felső sarokban), kattintson a Bővítmények **gombra,** keressen a "ClickOnce" kulcsszóra, válasszon egyet a ClickOnce kiterjesztések közül, és telepítse.    
   >
   >

    ![Átjáró – Konfigurálás lap](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Így a legegyszerűbb módja (egy kattintással) az átjáró egyetlen lépésben történő letöltéséhez, telepítéséhez, konfigurálásához és regisztrálásához. Láthatja, hogy a **Microsoft Data Management Gateway Configuration Manager** alkalmazás telepítve van a számítógépen. A végrehajtható **ConfigManager.exe** fájl a következő mappában található: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**.

    Az átjárót manuálisan is letöltheti és telepítheti az ezen az oldalon található hivatkozások segítségével, és regisztrálhatja az **ÚJ KULCS** szövegmezőben látható gombbal.

    Az [adatkezelési átjáróról](data-factory-data-management-gateway.md) szóló cikkben az átjáróval kapcsolatos összes részletet tartalmazza.

   > [!NOTE]
   > Az adatkezelési átjáró sikeres telepítéséhez és konfigurálásához rendszergazdának kell lennie a helyi számítógépen. További felhasználókat adhat hozzá az **Adatkezelési átjáró felhasználói** helyi Windows-csoporthoz. A csoport tagjai az Adatkezelési átjáró konfigurációkezelő eszközével konfigurálhatják az átjárót.
   >
   >
5. Várjon néhány percet, vagy várjon, amíg megjelenik a következő értesítési üzenet:

    ![Az átjáró telepítése sikeres](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Indítsa el **az Adatkezelési átjáró konfigurációkezelő** alkalmazását a számítógépen. A **Keresés** ablakban írja be az **Adatkezelési átjáró** kifejezést a segédprogram eléréséhez. A végrehajtható **ConfigManager.exe** fájl a következő mappában található: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

    ![Átjárókonfiguráció-kezelő](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Erősítse meg, `adftutorialgateway is connected to the cloud service` hogy megjelenik az üzenet. Az alsó állapotsor a **Csatlakozás a felhőszolgáltatáshoz** zöld pipával együtt jelenik **meg.**

    A **Kezdőlap** lapon a következő műveleteket is elvégezheti:

   * **Regisztráljon** egy átjárót egy kulccsal az Azure Portalról a Regisztráció gombbal.
   * **Állítsa le** az átjárógépen futó Data Management Gateway Host Service szolgáltatást.
   * **Ütemezze** a frissítéseket a nap egy adott időpontjában telepítendőre.
   * Az átjáró **utolsó frissítésének**megtekintése.
   * Adja meg, hogy mikor rakható az átjáró frissítése.
8. Váltson a **Beállítások** lapra. A **tanúsítvány** ban megadott tanúsítvány a portálon megadott helyszíni adattár hitelesítő adatainak titkosítására/visszafejtésére szolgál. (nem kötelező) Kattintson a **Módosítás** gombra a saját tanúsítvány használatához. Alapértelmezés szerint az átjáró a Data Factory szolgáltatás által automatikusan létrehozott tanúsítványt használja.

    ![Átjárótanúsítvány-konfiguráció](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    A **Beállítások** lapon a következő műveleteket is elvégezheti:

   * Az átjáró által használt tanúsítvány megtekintése vagy exportálása.
   * Módosítsa az átjáró által használt HTTPS-végpontot.    
   * Állítson be egy HTTP-proxyt, amelyet az átjáró használ.     
9. (nem kötelező) Váltson át a **Diagnosztika** lapra, és jelölje be a **Részletes naplózás engedélyezése jelölőnégyzetet,** ha engedélyezni szeretné az átjáróval kapcsolatos problémák elhárításához használható részletes naplózást. A naplózási adatok az **Eseménynapló** **Alkalmazások és szolgáltatások adatkezelési** -> **átjárócsomópontja** csoportban találhatók.

    ![Diagnosztika lap](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    A **Diagnosztika** lapon a következő műveleteket is végrehajthatja:

   * Használja **a Kapcsolat tesztelése** szakaszt egy helyszíni adatforráshoz az átjáró használatával.
   * Kattintson a **Naplók megtekintése gombra** az Adatkezelési átjáró naplójának eseménynaplójának megtekintéséhez az Eseménynapló ablakban.
   * Kattintson **a Naplók küldése gombra,** ha az elmúlt hét nap naplóit tartalmazó zip-fájlt szeretne feltölteni a Microsoftnak a problémák elhárításának megkönnyítése érdekében.
10. A **Diagnosztika** lap **Kapcsolat tesztelése** csoportjában válassza az **SqlServer** elemet az adattár típusához, írja be az adatbázis-kiszolgáló nevét, az adatbázis nevét, adja meg a hitelesítés típusát, írja be a felhasználónevet és a jelszót, majd kattintson a **Tesztelés** gombra annak ellenőrzéséhez, hogy az átjáró csatlakozhat-e az adatbázishoz.
11. Váltson át a webböngészőre, és az **Azure Portalon**kattintson az **OK** gombra a **Konfigurálás** lapon, majd az **Új adatátjáró** lapon.
12. A bal oldali fanézetben az adatátjárók területen kell látnia az **adftutorialgateway** **átjárót.**  Ha rákattint, látnia kell a társított JSON-t.

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Ebben a lépésben két összekapcsolt szolgáltatást hoz létre: **az AzureStorageLinkedService-t** és **az SqlServerLinkedService szolgáltatást.** Az **SqlServerLinkedService** egy helyszíni SQL Server-adatbázist kapcsol össze, és az **AzureStorageLinkedService** kapcsolt szolgáltatás egy Azure blob-tárolót kapcsol össze az adat-előállítóval. A forgatókönyv későbbi részében hozzon létre egy folyamatot, amely adatokat másol a helyszíni SQL Server-adatbázisból az Azure blob store-ba.

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Csatolt szolgáltatás hozzáadása helyszíni SQL Server-adatbázishoz
1. A **Data Factory Editor**programban kattintson az eszköztár **Új adattár gombjára,** és válassza az **SQL Server**lehetőséget.

   ![Új SQL Server csatolt szolgáltatás](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. A jobb oldali **JSON-szerkesztőben** tegye a következő lépéseket:

   1. Az **átjárónév esetén**adja meg az **adftutorialgateway értéket.**    
   2. A **connectionString**alkalmazásban tegye a következő lépéseket:    

      1. A **kiszolgálónév**mezőbe írja be az SQL Server adatbázist tároló kiszolgáló nevét.
      2. Az **adatbázisnév**mezőbe írja be az adatbázis nevét.
      3. Kattintson a **Titkosítás** gombra az eszköztáron. Megjelenik a Hitelesítő adatok kezelője alkalmazás.

         ![Hitelesítő adatok kezelője alkalmazás](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. A **Hitelesítő adatok megadása** párbeszédpanelen adja meg a hitelesítés típusát, a felhasználónevét és a jelszavát, majd kattintson **az OK**gombra. Ha a kapcsolat sikeres, a titkosított hitelesítő adatok a JSON-ban tárolódnak, és a párbeszédpanel bezárul.
      5. Zárja be az üres böngészőlapot, amely elindította a párbeszédpanelt, ha nem zárja be automatikusan, és lépjen vissza a lapra az Azure Portalon.

         Az átjárógépen ezek a hitelesítő adatok **titkosítva** vannak a Data Factory szolgáltatás tulajdonában lévő tanúsítvány használatával. Ha ehelyett az adatkezelési átjáróhoz társított tanúsítványt szeretné használni, olvassa el a Hitelesítő adatok biztonságos beállítása című témakört.    
   3. Kattintson a **központi telepítés gombra** a parancssávon az SQL Server csatolt szolgáltatás ának központi telepítéséhez. A csatolt szolgáltatásnak a fa nézetben kell lennie.

      ![SQL Server csatolt szolgáltatás a fa nézetben](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Csatolt szolgáltatás hozzáadása Azure-tárfiókhoz
1. A **Data Factory Editor**alkalmazásban kattintson az **Új adattár** elemre a parancssávon, majd kattintson az **Azure storage parancsra.**
2. Adja meg az Azure storage-fiók nevét a **fiók nevéhez.**
3. Adja meg az Azure storage-fiókjának **kulcsát**a Fiók kulcshoz.
4. Kattintson a **Telepítés** gombra az **AzureStorageLinkedService**üzembe helyezéséhez.

## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben olyan bemeneti és kimeneti adatkészleteket hoz létre, amelyek a másolási művelet be- és kimeneti adatait képezik majd (Helyszíni SQL Server-adatbázis => Azure Blob Storage). Az adatkészletek létrehozása előtt végezze el a következő lépéseket (a részletes lépések a lista után olvashatóak):

* Hozzon létre egy **emp** nevű táblát az adat-előállítóhoz társított szolgáltatásként hozzáadott SQL Server-adatbázisban, és szúrjon be a táblába néhány mintabejegyzést.
* Hozzon létre egy **adftutorial** nevű Blob-tárolót az adat-előállítóhoz társított szolgáltatásként hozzáadott Azure Blob Storage-fiókban.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Helyszíni SQL Server előkészítése az oktatóanyaghoz
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

1. A **Data Factory Editor**, kattintson **... További**, kattintson a parancssáv **Új adatkészlet e parancsára,** majd az **SQL Server tábla parancsra.**
2. Cserélje le a jobb oldali ablaktáblában lévő JSON-t a következő szövegre:

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

   * **a type** beállítása **SqlServerTable**.
   * **tableName** van beállítva, hogy **emp**.
   * **a linkedServiceName** beállítása **SqlServerLinkedService** (ezt a csatolt szolgáltatást a forgatókönyv korábbi pontjában hozta létre.).
   * Az Azure Data Factory egy másik folyamata által nem létrehozott bemeneti adatkészlet esetén be kell állítania **a külső** **értéket.** Azt jelzi, hogy a bemeneti adatok az Azure Data Factory szolgáltatáson kívül jönnek létre. A **házirend** szakasz **externalData** elemével megadhatja a külső adatházirendeket.    

   A JSON-tulajdonságokkal kapcsolatos részletek az [Adatok áthelyezése/az SQL Server kiszolgálóról](data-factory-sqlserver-connector.md) című témakörben találhatóak.
3. Az adatkészlet központi **telepítéséhez** kattintson a központi telepítés gombra a parancssávon.  

### <a name="create-output-dataset"></a>Kimeneti adatkészlet létrehozása

1. A **Data Factory Editor**ban kattintson az **Új adatkészlet** elemre a parancssávon, majd kattintson az Azure Blob **storage parancsra.**
2. Cserélje le a jobb oldali ablaktáblában lévő JSON-t a következő szövegre:

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

   * **a típus** **azureblob**lesz.
   * **a linkedServiceName** beállítása **AzureStorageLinkedService** (ezt a csatolt szolgáltatást a 2. lépésben hozta létre).
   * **folderPath** van beállítva **adftutorial/outfromonpremdf** ahol outfromonpremdf a mappát a adftutorial tárolóban. Hozza létre az **adftutorial** tárolót, ha még nem létezik.
   * Az **availability** (rendelkezésre állás) paraméter **hourly** (óránként) értékre van állítva (a **frequency** (gyakoriság) paraméter **hour** (óra), az **interval** (időköz) paraméter pedig **1** értékre).  A Data Factory szolgáltatás óránként létrehoz egy kimeneti adatszeletet az **Azure** SQL Database emp-táblájában.

   Ha nem ad meg **fájlnevet** egy **kimeneti táblához,** a mappa létrehozott fájljaia **következő** formátumban lesznek elnevezve: `Data.<Guid>.txt` (például: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Ha a **folderPath** és a **fileName** tulajdonságokat dinamikusan szeretné beállítani a **SliceStart** változó időpontja alapján, használja a partitionedBy tulajdonságot. A következő példában a folderPath tulajdonság a SliceStart (a feldolgozás alatt álló szelet kezdő időpontja) változó Év, Hónap és Nap értékeit, a fileName tulajdonság pedig a SliceStart változó Óra értékét használja. Ha például a szelet előállítása a 2014-10-20T08:00:00 időpontban kezdődik, a folderName tulajdonság beállítása wikidatagateway/wikisampledataout/2014/10/20, a fileName beállítása pedig 08.csv lesz.

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

    A JSON-tulajdonságokkal kapcsolatos részletekért tekintse meg az [Adatok áthelyezése az Azure Blob Storage-ba/az Azure Blob Storage-ból](data-factory-azure-blob-connector.md) című témakört.
3. Az adatkészlet központi **telepítéséhez** kattintson a központi telepítés gombra a parancssávon. Ellenőrizze, hogy mindkét adatkészlet megjelenik-e a fanézetben.  

## <a name="create-pipeline"></a>Folyamat létrehozása
Ebben a lépésben hozzon létre egy **folyamatot** egy **másolási tevékenység,** amely **az EmpOnPremSQLTable** bemeneti és **OutputBlobTable** kimenetként.

1. A Data Factory Editor, kattintson **... Egy**e után kattintson **az Új folyamat gombra.**
2. Cserélje le a jobb oldali ablaktáblában lévő JSON-t a következő szövegre:    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on premises SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on premises SQL server to blob",
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

   * A tevékenységek szakaszban csak olyan tevékenység van, amelynek **típusa** **Másolás**.
   * A tevékenység **bemenete** **EmpOnPremSQLTable,** a tevékenység **kimenete** pedig **OutputBlobTable**lesz állítva.
   * A **typeProperties** szakaszban az **SqlSource** a **forrástípus,** **a BlobSink** pedig **a fogadó típusa.**
   * Sql `select * from emp` lekérdezés van megadva az **SqlSource** **sqlReaderQuery** tulajdonságához.

   Mind a kezdő, mind a befejező dátum-időpont értéket [ISO formátumban](https://en.wikipedia.org/wiki/ISO_8601) kell megadni. Például: 2014-10-14T16:32:41Z. Az **end** (befejező) időpont megadása opcionális, a jelen oktatóanyagban azonban azt is használjuk.

   Ha nem adja meg a **záró** tulajdonság értékét, akkor a program a "**start + 48 óra**" értékként számítja ki. A folyamat határozatlan ideig történő futtatásához adja meg a **9/9/9999** értéket az **end** (befejezés) tulajdonsághoz.

   Az adatszeletek feldolgozásának időtartamát az egyes Azure Data Factory-adatkészlethez definiált **rendelkezésre állási** tulajdonságok alapján határozza meg.

   A fenti példában 24 adatszelet jön létre, mivel a nap minden órájában készül egy adatszelet.        
3. Kattintson a **Központi telepítés gombra** a parancssávon az adatkészlet központi telepítéséhez (a tábla egy téglalap alakú adatkészlet). Ellenőrizze, hogy a folyamat megjelenik-e a fanézetben **a Folyamatok** csomópont alatt.  
4. Most kattintson **az X** kétszer, hogy zárja be az oldalt, hogy újra a **Data Factory** oldalon a **ADFTutorialOnPremDF**.

**Gratulálok!** Sikeresen létrehozott egy Azure-adat-előállító, csatolt szolgáltatások, adatkészletek és egy folyamat, és a folyamat ütemezése.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>A data factory megtekintése diagramnézetben
1. Az **Azure Portalon**kattintson a **Diagram** csempe a kezdőlapon az **ADFTutorialOnPremDF** adatgyár. :

    ![Diagram hivatkozása](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Az alábbi képhez hasonló diagramnak kell megjelennie:

    ![Diagramnézet](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Nagyíthatja, kicsinyítheti, 100%-ra nagyíthatja, nagyíthatja a beállításokat, automatikusan elhelyezheti a folyamatokat és adatkészleteket, és megjelenítheti a leszármazási adatokat (kiemeli a kijelölt elemek felfelé és lefelé mutató elemeit).  Dupla kattintással egy objektum (bemeneti/kimeneti adatkészlet vagy csővezeték) annak tulajdonságait.

## <a name="monitor-pipeline"></a>Folyamat figyelése
Ebben a lépésben az Azure Portal használatával figyeli egy Azure data factory eseményeit. PowerShell-parancsmagokat is használhat az adatkészletek és folyamatok figyeléséhez. A figyelésről a [Folyamatok figyelése és kezelése témakörben](data-factory-monitor-manage-pipelines.md)talál.

1. Az ábrán kattintson duplán az **EmpOnPremSQLTable elemre.**  

    ![EmpOnPremSQLTable szeletek](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Figyelje meg, hogy az összes felfelé lévő adatszelet kész állapotban **van,** mert a folyamat időtartama (kezdési időpont a befejezési időpontig) a múltban van. Ez azért is van, mert beszúrta az adatokat az SQL Server adatbázisba, és mindig ott van. Ellenőrizze, hogy nem jelennek-e meg szeletek a **Problémaszeletek** szakaszalján. Az összes szelet megtekintéséhez kattintson a szeletek listájának alján található **Lásd** tovább gombra.
3. Most az **Adatkészletek** lapon kattintson a **OutputBlobTable gombra.**

    ![OputputBlobTable szeletek](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Kattintson bármelyik adatszeletre a listából, és megjelenik az **Adatszelet** lap. Láthatja, hogy a tevékenység fut a szelethez. Általában csak egy tevékenységfuttatást lát.  

    ![Adatszelet panelje](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Ha a szelet nem **Ready** (Kész) állapotú, az **Upstream slices that are not ready** (Nem kész állapotú fölérendelt szeletek) listában láthatja azokat a nem kész állapotú fölérendelt szeleteket, amelyek blokkolják az aktuális szelet végrehajtását.
5. Kattintson a **tevékenység futtatása** a listából az alján, hogy **tevékenység futtatási részleteket**.

   ![Tevékenység futtatása részletei lap](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Az adatok átviteléhez használt adatok, például az átviteli, az időtartam és az átjáró.
6. Az Összes oldal bezárásához kattintson az **X** gombra
7. vissza a honlapon az **ADFTutorialOnPremDF**.
8. (nem kötelező) Kattintson **a Folyamatok**menüRe, majd az **ADFTutorialOnPremDF**elemre, és részletezze a bemeneti táblákat (**Felhasznált**) vagy a kimeneti adatkészleteket (**Előállított**).
9. Az eszközök, például a [Microsoft Storage Explorer](https://storageexplorer.com/) segítségével ellenőrizze, hogy minden órában létrejön-e blob/fájl.

   ![Azure Storage Explorer](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>További lépések
* Az [Adatkezelési átjáróval](data-factory-data-management-gateway.md) kapcsolatos részleteket lásd: Data Management Gateway cikk.
* Az [Azure Blobból az Azure SQL-be című témakörben](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) megtudhatja, hogy miként helyezhetát át az adatokat a forrásadattárból a fogadóadattárba.
