---
title: Azure Data Factory folyamat létrehozása privát végpontok használatával
description: Az oktatóanyag részletes útmutatását követve létrehozhat egy adat-előállítót egy folyamat és az Azure Portal használatával. A folyamat a másolási tevékenységet használja az adatok Azure Blob Storage-ból egy Azure SQL Database-be való másolásához.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.author: jingwang
ms.openlocfilehash: 9458c89922fab8450b7cb8e202491d4c47e250e8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537638"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Adatok biztonságos másolása az Azure Blob Storage-ból egy SQL-adatbázisba privát végpontok használatával

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ebben az oktatóanyagban az Azure Data Factory felhasználói felületének használatával hoz létre egy adat-előállítót. **Az adatfeldolgozó folyamata biztonságos módon másolja az adatait az Azure Blob Storage-ból egy Azure SQL Database-adatbázisba (mindkettő engedélyezi a hozzáférést csak a kiválasztott hálózatokhoz) [Azure Data Factory felügyelt Virtual Networkban](managed-virtual-network-private-endpoint.md)lévő privát végpontok használatával.** Az oktatóanyagban szereplő konfigurációs minta fájlalapú adattárból relációs adattárba való másolásra vonatkozik. A forrásként és fogadóként támogatott adattárak listája a [támogatott adattárakat tartalmazó](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) táblázatban található.

> [!NOTE]
>
> - Ha még csak ismerkedik a Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](https://docs.microsoft.com/azure/data-factory/introduction) ismertető cikket.

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> * Adat-előállító létrehozása
> * Másolási tevékenységgel rendelkező folyamat létrehozása


## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* **Azure Storage-fiók**. A Blob Storage lesz használatban *forrás*adattárként. Ha még nem rendelkezik tárfiókkal, tekintse meg az [Azure Storage-fiók létrehozásának](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal) lépéseit ismertető cikket. **Győződjön meg arról, hogy a Storage-fiók csak a "kiválasztott hálózatokból" engedélyezi a hozzáférést.** 
* **Azure SQL Database**. Ezt az adatbázist használjuk *fogadó*adattárként. Ha nem rendelkezik Azure SQL Database-adatbázissal, tekintse meg az [SQL-adatbázis létrehozása](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) című témakört. **Győződjön meg arról, hogy a Azure SQL Database fiók csak a "kiválasztott hálózatokból" engedélyezi a hozzáférést.** 

### <a name="create-a-blob-and-a-sql-table"></a>Blob és SQL-tábla létrehozása

Készítse elő a Blob Storage-et és az SQL Database-t az oktatóanyaghoz a következő lépésekkel.

#### <a name="create-a-source-blob"></a>Forrás blob létrehozása

1. Indítsa el a Jegyzettömböt. Másolja be a következő szöveget, és mentse **emp.txt** néven egy fájlba a lemezen.

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Hozzon létre egy **adftutorial** nevű tárolót a Blob Storage-ban. Ebben a tárolóban hozzon létre egy **input** nevű mappát. Ezután töltse fel az **emp.txt** fájlt az **input** mappába. Ezekhez a feladatokhoz használja az Azure Portalt vagy olyan eszközöket, mint az [Azure Storage Explorer](https://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Fogadó SQL-tábla létrehozása

1. A következő SQL-szkripttel hozza létre a **dbo.emp** táblát az SQL Database-ben.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

1. Engedélyezze az SQL Server elérését az Azure-szolgáltatások számára. Győződjön meg arról, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése****BE** van kapcsolva az SQL Serverhez, hogy a Data Factory tudjon adatokat írni az SQL Serverre. A beállítás ellenőrzéséhez és bekapcsolásához nyissa meg az Azure SQL Server > áttekintés > **a**kiszolgáló tűzfalának beállítása> az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** lehetőséget.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása
Ebben a lépésben létrehoz egy adat-előállítót, és elindítja a Data Factory felhasználói felületét, hogy létrehozzon egy folyamatot az adat-előállítóban.

1. Nyissa meg a **Microsoft Edge** vagy a **Google Chrome böngészőt**. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.


2. A bal oldali menüben válassza az **erőforrás létrehozása**  >  **elemzési**  >  **Data Factory**elemet.

3. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

   Az Azure-beli adatgyár nevének *globálisan egyedinek*kell lennie. Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. (például Sajátneveadftutorialdatafactory). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](https://docs.microsoft.com/azure/data-factory/naming-rules) ismertető cikkben találja.

4. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni.

5. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    a. Válassza a **meglévő használata**lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából.

    b. Válassza az **új létrehozása**lehetőséget, és adja meg az erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) ismertető cikket. 

6. A **Verzió** résznél válassza a **V2** értéket.

7. A **Hely** területen válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (például az Azure Storage és az SQL Database) és számítási erőforrások (például az Azure HDInsight) más régiókban is lehetnek.


8. Kattintson a **Létrehozás** gombra.


9. A létrehozás befejezése után megjelenik az értesítési központban megjelenő értesítés. Válassza az **Ugrás az erőforráshoz** lehetőséget, hogy megnyissa az adatfeldolgozó lapot.

10. A Data Factory felhasználói felületének külön lapon történő elindításához válassza a **Létrehozás és figyelés** csempét.

## <a name="create-an-azure-integration-runtime-in-adf-managed-virtual-network"></a>Azure Integration Runtime létrehozása az ADF által felügyelt Virtual Network
Ebben a lépésben létrehoz egy Azure Integration Runtime, és engedélyezi a felügyelt Virtual Network.

1. Az ADF-portálon nyissa meg a **felügyelet hubot** , és kattintson az **új** elemre új Azure Integration Runtime létrehozásához.
   ![Új Azure Integration Runtime létrehozása](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
2. Válassza az Azure * * Integration Runtime létrehozása lehetőséget.
   ![Új Azure Integration Runtime](./media/tutorial-copy-data-portal-private/azure-ir.png)
3. **Virtual Network**engedélyezése.
   ![Új Azure Integration Runtime](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
4. Kattintson a **Létrehozás** gombra.

## <a name="create-a-pipeline"></a>Folyamat létrehozása
Ebben a lépésben létrehoz egy másolási tevékenységgel rendelkező folyamatot az adat-előállítóban. A másolási tevékenység adatokat másol egy Blob Storage-ból egy SQL Database-be. A [rövid útmutatóban](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) létrehozott egy folyamatot az alábbi lépéseket követve:

1. Hozza létre a társított szolgáltatást.
1. Hozzon létre bemeneti és kimeneti adatkészleteket.
1. Folyamat létrehozása.

Ebben az oktatóanyagban először létrehozzuk a folyamatot. Ezután társított szolgáltatásokat és adatkészleteket hozunk létre, amikor szükség van rájuk a folyamat konfigurálásához.

1. Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget.

   ![Folyamat létrehozása](./media/doc-common-process/get-started-page.png)
1. A folyamat **Tulajdonságok** ablaktábláján adja meg a **CopyPipeline** nevet a folyamat **neveként** .

1. A **tevékenységek** eszközben bontsa ki az **áthelyezés és átalakítás** kategóriát, majd húzza a **adatok másolása** tevékenységet az eszközkészletből a folyamat tervező felületére. Adja meg a **CopyFromBlobToSql** értéket a **Név** mezőben.

    ![Másolási tevékenység](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Forrás konfigurálása

>[!TIP]
>Ebben az oktatóanyagban a *fiók kulcsát* használja a forrás adattároló hitelesítési típusaként, de más támogatott hitelesítési módszereket is választhat: *sas URI*,*egyszerű szolgáltatásnév* és *felügyelt identitás* , ha szükséges. A részletekért tekintse meg a [cikk](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#linked-service-properties) megfelelő részeit.
>Az adattárak titkos kulcsainak biztonságos tárolásához ajánlott egy Azure Key Vault is használni. A részletes illusztrációkat [ebben a cikkben](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) találja.

#### <a name="create-source-dataset-and-linked-service"></a>Forrás-adatkészlet és társított szolgáltatás létrehozása

1. Lépjen a **forrás** lapra. Válassza az **+ új** lehetőséget a forrás adatkészlet létrehozásához.

1. Az **új adatkészlet** párbeszédpanelen válassza az **Azure Blob Storage**lehetőséget, majd kattintson a **Folytatás**gombra. A forrásadatok egy Blob Storage-ban vannak, tehát forrásadatkészletként az **Azure Blob Storage**-ot válassza.

1. A **formátum kiválasztása** párbeszédpanelen válassza ki az adatformátum típusát, majd kattintson a **Continue (folytatás**) elemre.

1. A **Tulajdonságok megadása** párbeszédpanelen adja meg a **sourceblobdataset lehetőség** nevet. Jelölje be az **első sor jelölőnégyzetét fejlécként**. A **társított szolgáltatás** szövegmezőben válassza az **+ új**lehetőséget.

1. Az **új társított szolgáltatás (Azure Blob Storage)** párbeszédpanelen írja be a **AzureStorageLinkedService** nevet, válassza ki a Storage-fiók nevét a **Storage-fiók neve** listából. 

1. Győződjön meg arról, hogy az **interaktív szerzői műveletek**engedélyezve vannak. Előfordulhat, hogy az engedélyezése körülbelül 1 percet vesz igénybe.

    ![Interaktív szerzői műveletek](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Válassza a **kapcsolat tesztelése**lehetőséget, ha a Storage-fiók csak a kiválasztott hálózatról engedélyezi a hozzáférést, és azt igényli, hogy Azure Data Factory hozzon létre egy privát végpontot, amelyet a használata előtt jóvá kell hagyni. A hibaüzenetben egy olyan hivatkozást kell látnia, amely létrehoz egy **privát végpontot** , amelyet követve felügyelt privát végpontokat hozhat létre. *Egy másik lehetőség, hogy közvetlenül a kezelés lapra lép, és a [következő szakaszban](#create-a-managed-private-endpoint) szereplő utasításokat követve felügyelt privát végpontot hoz létre*
> [!NOTE]
> Előfordulhat, hogy a kezelés lap nem érhető el az összes adatfeldolgozó példányhoz. Ha nem látja, továbbra is elérheti a privát végpontokat a "**Szerző**" lapon – > "**Connections**"--> "**Private Endpoint**"
1. Tartsa nyitva a párbeszédpanelt, majd lépjen a fent kiválasztott Storage-fiókhoz.

1. A privát hivatkozás jóváhagyásához kövesse az [ebben a szakaszban](#approval-of-a-private-link-in-storage-account) található utasításokat.

1. Lépjen vissza a párbeszédpanelre. Próbálja megismételni a **kapcsolódást** , és válassza a **Létrehozás** lehetőséget a társított szolgáltatás telepítéséhez.

1. A társított szolgáltatás létrehozása után a rendszer a **tulajdonságok beállítása** lapra navigál. A **Fájl elérési útja** mellett válassza a **Tallózás** lehetőséget.

1. Keresse meg a **adftutorial/input** mappát, válassza ki a **emp.txt** fájlt, majd kattintson az **OK gombra**.

1. Kattintson az **OK** gombra. Automatikusan megnyitja a folyamat lapot. A **forrás** lapon ellenőrizze, hogy a **sourceblobdataset lehetőség** van-e kiválasztva. A lapon lévő adatok előnézetének megtekintéséhez válassza az **Adatok előnézete** elemet.

    ![Forrásadatkészlet](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Felügyelt privát végpont létrehozása

Ha a fenti kapcsolat tesztelésekor nem kattintott a hiperhivatkozásra, kövesse az alábbi elérési utat. Most létre kell hoznia egy felügyelt magánhálózati végpontot, amelyhez csatlakozni fog a fent létrehozott társított szolgáltatáshoz.

1. Lépjen a kezelés lapra.
> [!NOTE]
> Előfordulhat, hogy a kezelés lap nem érhető el az összes adatfeldolgozó példányhoz. Ha nem látja, továbbra is elérheti a privát végpontokat a "**Szerző**" lapon – > "**Connections**"--> "**Private Endpoint**"

1. Lépjen a felügyelt privát végpontok szakaszra.

1. Válassza az **+ új** lehetőséget a felügyelt privát végpontok alatt.

    ![Új felügyelt magánhálózati végpont](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Válassza ki az Azure Blob Storage csempét a listából, majd válassza a **Folytatás**lehetőséget.

1. Adja meg a fent létrehozott Storage-fiók nevét.

1. Kattintson a **Létrehozás** gombra.

1. Néhány másodperc várakozás után kell megjelennie, hogy a privát kapcsolat létrehozása jóváhagyást igényel.

1. Válassza ki a fent létrehozott privát végpontot. Megtekintheti a hivatkozásokat, amelyekkel jóváhagyhatja a magánhálózati végpontot a Storage-fiók szintjén.

    ![Privát végpont kezelése](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-storage-account"></a>Privát hivatkozás jóváhagyása a Storage-fiókban
1. A Storage-fiókban válassza a **privát végponti kapcsolatok** lehetőséget a beállítások szakaszban.

1. Jelölje be a fent létrehozott privát végpontot, és válassza a **jóváhagyás**lehetőséget.

    ![Privát végpont jóváhagyása](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Adja meg a leírást, és kattintson az **Igen** gombra.
1. Lépjen vissza a Azure Data Factory **kezelés** lapján található **felügyelt privát végpontok** szakaszra.
1. Körülbelül 1-2 percet vesz igénybe a privát végpont jóváhagyásának beolvasása Azure Data Factory felhasználói felületen.


### <a name="configure-sink"></a>Fogadó konfigurálása
>[!TIP]
>Ebben az oktatóanyagban az *SQL-hitelesítést* használja a fogadó adattár hitelesítési típusaként, de más támogatott hitelesítési módszereket is választhat: az *egyszerű szolgáltatásnév* és a *felügyelt identitás* , ha szükséges. A részletekért tekintse meg a [cikk](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties) megfelelő részeit.
>Az adattárak titkos kulcsainak biztonságos tárolásához ajánlott egy Azure Key Vault is használni. A részletes illusztrációkat [ebben a cikkben](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) találja.

#### <a name="create-sink-dataset-and-linked-service"></a>Fogadó adatkészlet és társított szolgáltatás létrehozása
1. Váltson a **Fogadó** lapra, és válassza az **+ Új** elemet egy fogadó-adatkészlet létrehozásához.

1. Az **új adatkészlet** párbeszédpanelen írja be az "SQL" kifejezést a keresőmezőbe az összekötők szűréséhez, válassza a **Azure SQL Database**lehetőséget, majd kattintson a **Folytatás**gombra. Ebben az oktatóanyagban adatokat másol egy SQL Database-be.

1. A **Tulajdonságok megadása** párbeszédpanelen adja meg a **outputsqldataset érték** nevet. A **társított szolgáltatás** legördülő listából válassza az **+ új**lehetőséget. Az adatkészleteket mindig társítani kell egy társított szolgáltatáshoz. A társított szolgáltatás rendelkezik azzal a kapcsolati sztringgel, amely használatával a Data Factory a futtatás során kapcsolódik az SQL Database-hez. Az adatkészlet meghatározza azt a tárolót, mappát és fájlt (az utóbbi nem kötelező), ahova a rendszer az adatokat másolja.

1. Az **új társított szolgáltatás (Azure SQL Database)** párbeszédpanelen hajtsa végre a következő lépéseket:

    1. A **Név** mezőbe írja az **AzureSqlDatabaseLinkedService** nevet.
    1. A **Kiszolgáló neve** mezőben válassza ki az SQL Server-példányát.
    1. Győződjön meg arról, hogy az **interaktív szerzői műveletek**engedélyezve vannak.
    1. Az **Adatbázis neve** mezőben válassza ki az SQL Database-adatbázisát.
    1. A **Felhasználónév** mezőben adja meg a felhasználó nevét.
    1. A **Jelszó** mezőben adja meg a felhasználó jelszavát.
    1. Válassza a **Kapcsolat tesztelése** elemet. A művelet végrehajtása sikertelen, mert az SQL Server csak a "kiválasztott hálózatokból" engedélyezi a hozzáférést, és Azure Data Factory kell létrehoznia egy privát végpontot, amelyet a használata előtt jóvá kell hagyni. A hibaüzenetben egy olyan hivatkozást kell látnia, amely létrehoz egy **privát végpontot** , amelyet követve felügyelt privát végpontokat hozhat létre. *Egy másik lehetőség, hogy közvetlenül a kezelés lapra lép, és a következő szakaszban szereplő utasításokat követve felügyelt privát végpontot hoz létre*
    1. Hagyja nyitva a párbeszédpanelt, majd lépjen a fent kiválasztott SQL Serverre.    
    1. A privát hivatkozás jóváhagyásához kövesse az [ebben a szakaszban](#approval-of-a-private-link-in-sql-server) található utasításokat.
    1. Lépjen vissza a párbeszédpanelre. Próbálja megismételni a **kapcsolódást** , és válassza a **Létrehozás** lehetőséget a társított szolgáltatás telepítéséhez.

1. Automatikusan megnyitja a **tulajdonságok beállítása** párbeszédpanelt. A **Tábla** területen válassza a **[dbo].[emp]** elemet. Ez után válassza az **OK** gombot.

1. Lépjen a folyamatot tartalmazó lapra, és győződjön meg arról, hogy a **Fogadóadattár** mezőben az **OutputSqlDataset** érték van kiválasztva.

    ![Folyamat lap](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)       

A forrás sémáját leképezheti a cél megfelelő sémájára a [másolási tevékenységben lévő séma-hozzárendelés](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping)után.

#### <a name="create-a-managed-private-endpoint"></a>Felügyelt privát végpont létrehozása

Ha a fenti kapcsolat tesztelésekor nem kattintott a hiperhivatkozásra, kövesse az alábbi elérési utat. Most létre kell hoznia egy felügyelt magánhálózati végpontot, amelyhez csatlakozni fog a fent létrehozott társított szolgáltatáshoz.

1. Lépjen a kezelés lapra.
1. Lépjen a felügyelt privát végpontok szakaszra.
1. Válassza az **+ új** lehetőséget a felügyelt privát végpontok alatt.

    ![Új felügyelt magánhálózati végpont](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Válassza ki a listából a Azure SQL Database csempét, és válassza a **Folytatás**lehetőséget.
1. Adja meg a fent kiválasztott SQL Server-kiszolgáló nevét.
1. Kattintson a **Létrehozás** gombra.
1. Néhány másodperc várakozás után kell megjelennie, hogy a privát kapcsolat létrehozása jóváhagyást igényel.
1. Válassza ki a fent létrehozott privát végpontot. Megtekintheti a hiperhivatkozást, amellyel jóváhagyhatja a privát végpontot az SQL Server szintjén.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Privát hivatkozás jóváhagyása az SQL Serverben
1. Az SQL Serverben válassza a **privát végponti kapcsolatok** lehetőséget a beállítások szakaszban.
1. Jelölje be a fent létrehozott privát végpontot, és válassza a **jóváhagyás**lehetőséget.
1. Adjon meg egy leírást, és kattintson az **Igen**gombra.
1. Lépjen vissza a Azure Data Factory **kezelés** lapján található **felügyelt privát végpontok** szakaszra.
1. Körülbelül 1-2 percet vesz igénybe, hogy a jóváhagyás megjelenjen a saját végpontján.

#### <a name="debug-and-publish-the-pipeline"></a>A folyamat hibakeresése és közzététele

Elvégezheti a folyamat hibakeresését, mielőtt összetevőket (társított szolgáltatások, adatkészletek és folyamat) tenne közzé a Data Factoryben vagy a saját Azure Repos Git-adattárában.

1. A folyamat hibakereséséhez válassza a **Hibakeresés** elemet az eszköztáron. A folyamat futtatási állapotát az ablak alján található **Kimenet** lapon tekintheti meg.
2. Ha a folyamat sikeresen futtatható, a felső eszköztáron válassza az **összes közzététele**lehetőséget. Ez a művelet közzéteszi a létrehozott entitásokat (adatkészleteket és folyamatokat) a Data Factoryben.
3. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet. Az értesítési üzenetek megjelenítéséhez kattintson az **értesítések megjelenítése** lehetőségre a jobb felső sarokban (harang gomb).


#### <a name="summary"></a>Összegzés
Az ebben a példában szereplő folyamat a blob Storage-ból az Azure SQL DB-be másolja az adatait a felügyelt Virtual Network privát végpontjának használatával. Megtanulta végrehajtani az alábbi műveleteket:

> * Adat-előállító létrehozása
> * Másolási tevékenységgel rendelkező folyamat létrehozása

