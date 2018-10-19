---
title: 'Oktatóanyag: ETL-műveletek végrehajtása az Azure Databricks használatával'
description: Ismerje meg hogyan nyerhet ki adatokat a Data Lake Store-ból az Azure Databricksbe, hogyan alakíthatja át az adatokat, és hogyan töltheti be őket az Azure SQL Data Warehouse-ba.
services: azure-databricks
author: nitinme
ms.author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.workload: Active
ms.date: 07/26/2018
ms.openlocfilehash: c416937f98f6bcb49f86fce18213ca4ed349c513
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902059"
---
# <a name="tutorial-extract-transform-and-load-data-using-azure-databricks"></a>Oktatóanyag: Adatok kinyerése, átalakítása és betöltése az Azure Databricks használatával

Ebben az oktatóanyagban egy ETL-műveletet (kinyerés, átalakítás és adatbetöltés) hajt végre az Azure Databricks használatával. Kinyeri az adatokat az Azure Data Lake Store-ból az Azure Databricksbe, átalakításokat futtat az Azure Databricksben található adatokon, majd betölti az átalakított adatokat az Azure SQL Data Warehouse-ba. 

A jelen oktatóanyagban szereplő lépések az Azure Databricks SQL Data Warehouse-összekötőjét használják az adatok Azure Databricksbe való átviteléhez. Az összekötő ezután az Azure Blob Storage-ot használja ideiglenes tárolóként az Azure Databricks-fürt és az Azure SQL Data Warehouse között átvitt adatokhoz.

Az alábbi ábrán az alkalmazásfolyam látható:

![Az Azure Databricks a Data Lake Store-ral és az SQL Data Warehouse-szal](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Az Azure Databricks a Data Lake Store-ral és az SQL Data Warehouse-szal")

Ez az oktatóanyag a következő feladatokat mutatja be: 

> [!div class="checklist"]
> * Azure Databricks-munkaterület létrehozása
> * Spark-fürt létrehozása az Azure Databricksben
> * Azure Data Lake Store-fiók létrehozása
> * Adatok feltöltése az Azure Data Lake Store-ba
> * Jegyzetfüzet létrehozása az Azure Databricksben
> * Adatok kinyerése a Data Lake Store-ból
> * Adatok átalakítása az Azure Databricksben
> * Adatok betöltése az Azure SQL Data Warehouse-ba

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt nekilát az oktatóanyagnak, ellenőrizze, hogy megfelel-e a következő feltételeknek:
- Hozzon létre egy Azure SQL Data Warehouse-t, hozzon létre egy kiszolgálószintű tűzfalszabályt, majd csatlakozzon a kiszolgálóhoz kiszolgáló-rendszergazdaként. Kövesse az [Azure SQL Data Warehouse létrehozásáról szóló rövid útmutató](../sql-data-warehouse/create-data-warehouse-portal.md) utasításait
- Hozzon létre egy fő adatbáziskulcsot az Azure SQL Data Warehouse számára. Kövesse a [Fő adatbázisfőkulcs létrehozását](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key) ismertető dokumentum utasításait.
- Hozzon létre egy Azure Blob Storage-fiókot, benne egy tárolóval. A hozzáférési kulcsot is kérje le a tárfiók eléréséhez. A kapcsolódó utasításokért lásd az [Azure Blob Storage-fiók létrehozását ismertető rövid útmutatót](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

Ebben a szakaszban egy Azure Databricks-munkaterületet fog létrehozni az Azure Portal használatával. 

1. Az Azure Portalon válassza az **Erőforrás létrehozása** > **Adatok + analitika** > **Azure Databricks** elemet.

    ![Databricks az Azure Portalon](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png "Databricks az Azure Portalon")

3. Az **Azure Databricks szolgáltatás** pontban adja meg az értékeket Databricks-munkaterület létrehozásához.

    ![Azure Databricks-munkaterület létrehozása](./media/databricks-extract-load-sql-data-warehouse/create-databricks-workspace.png "Azure Databricks-munkaterület létrehozása")

    Adja meg a következő értékeket: 
     
    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.        |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../azure-resource-manager/resource-group-overview.md). |
    |**Hely**     | Válassza az **USA 2. keleti régiója** lehetőséget. A további elérhető régiókért tekintse meg az [elérhető Azure-szolgáltatások régiók szerinti bontását](https://azure.microsoft.com/regions/services/).        |
    |**Tarifacsomag**     |  Válassza a **Standard** vagy a **Prémium** előfizetést. További információkért a csomagokkal kapcsolatban tekintse meg a [Databricks díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/databricks/).       |

    Válassza a **Rögzítés az irányítópulton**, majd a **Létrehozás** lehetőséget.

4. A fiók létrehozása eltarthat néhány percig. A fiók létrehozása során a portál jobb oldalán megjelenik az **Üzembe helyezés beküldése a következőhöz: Azure Databricks** csempe. Lehetséges, hogy jobbra kell görgetnie az irányítópulton, hogy megjelenjen a csempe. Megjelenik egy folyamatjelző is a képernyő tetejéhez közel. Mindkét területen nyomon követheti a folyamat előrehaladását.

    ![Databricks üzembe helyezési csempe](./media/databricks-extract-load-sql-data-warehouse/databricks-deployment-tile.png "Databricks üzembe helyezési csempe")

## <a name="create-a-spark-cluster-in-databricks"></a>Spark-fürt létrehozása a Databricks használatával

1. Az Azure Portalon lépjen a létrehozott Databricks-munkaterülethez, majd válassza a **Munkaterület indítása** elemet.

2. A rendszer átirányítja az Azure Databricks portáljára. A portálon válassza a **Fürt** elemet.

    ![Databricks az Azure-on](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Databricks az Azure-on")

3. Az **Új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-on](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-on")

    Fogadja el az összes alapértelmezett értéket, kivéve a következőket:

    * Adjon egy nevet a fürtnek.
    * Ehhez a cikkhez a **4.0** futtatókörnyezetben hozzon létre fürtöt. 
    * Mindenképpen jelölje be a **Leállítás ___ percnyi tétlenség után** jelölőnégyzetet. Adja meg az időtartamot (percben), amelynek elteltével le kell állítani a fürtöt, amennyiben az használaton kívül van.
    
    Válassza a **Fürt létrehozása** lehetőséget. Ha a fürt már fut, notebookokat csatlakoztathat hozzá, illetve Spark-feladatokat futtathat.

## <a name="create-an-azure-data-lake-store-account"></a>Azure Data Lake Store-fiók létrehozása

Ebben a szakaszban létrehoz egy Azure Data Lake Store-fiókot, és társít hozzá egy Azure Active Directory-szolgáltatásnevet. Az oktatóanyag későbbi részében ezt a szolgáltatásnevet fogja használni az Azure Databricksben az Azure Data Lake Store eléréséhez. 

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Storage** > **Data Lake Store** lehetőséget.
3. Az **Új Data Lake Store** panelen adja meg az értékeket az alábbi képernyőfelvételen látható módon:
   
    ![Új Azure Data Lake Store-fiók létrehozása](./media/databricks-extract-load-sql-data-warehouse/create-new-datalake-store.png "Új Azure Data Lake Store-fiók létrehozása")

    Adja meg a következő értékeket: 
     
    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Name (Név)**     | Adja meg a Data Lake Store-fiók egyedi nevét.        |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Ehhez az oktatóanyaghoz válassza ki ugyanazt az erőforráscsoportot, mint amelyet az Azure Databricks-munkaterület létrehozásakor használt.  |
    |**Hely**     | Válassza az **USA 2. keleti régiója** lehetőséget.  |
    |**Díjszabási csomag**     |  Válassza a **Használatalapú fizetés** lehetőséget. |
    | **Titkosítási beállítások** | Tartsa meg az alapértelmezett beállításokat. |

    Válassza a **Rögzítés az irányítópulton**, majd a **Létrehozás** lehetőséget.

A következőkben létrehoz egy Azure Active Directory-szolgáltatásnevet, és társítja hozzá a létrehozott Data Lake Store-fiókot.

### <a name="create-an-azure-active-directory-service-principal"></a>Azure Active Directory-szolgáltatásnév létrehozása
   
1. Az [Azure Portalon](https://portal.azure.com) válassza a **Minden szolgáltatás** elemet, majd keressen rá az **Azure Active Directory** kifejezésre.

2. Válassza az **Alkalmazásregisztrációk** elemet.

   ![alkalmazásregisztrációk kiválasztása](./media/databricks-extract-load-sql-data-warehouse/select-app-registrations.png)

3. Válassza az **Új alkalmazás regisztrálása** elemet.

   ![alkalmazás hozzáadása](./media/databricks-extract-load-sql-data-warehouse/select-add-app.png)

4. Adja meg az alkalmazás nevét és URL-címét. Válassza a **Webalkalmazás/API** lehetőséget a létrehozni kívánt alkalmazás típusaként. Adjon meg egy bejelentkezési URL-címet, majd kattintson a **Létrehozás** elemre.

   ![alkalmazás elnevezése](./media/databricks-extract-load-sql-data-warehouse/create-app.png)

A Data Lake Store-fiók Azure Databricksből való eléréséhez rendelkeznie kell az alábbi értékekkel a létrehozott Azure Active Directory-szolgáltatásnévhez:
- Alkalmazásazonosító
- Hitelesítési kulcs
- Bérlőazonosító

A következő szakaszokban le fogja kérni ezeket a korábban létrehozott Azure Active Directory-szolgáltatásnévhez tartozó értékeket.

### <a name="get-application-id-and-authentication-key-for-the-service-principal"></a>A szolgáltatásnévhez tartozó alkalmazásazonosító és hitelesítési kulcs beszerzése

Ha programozott módon jelentkezik be, szüksége lesz az alkalmazásazonosítóra és egy hitelesítési kulcsra. Az értékek beszerzéséhez kövesse az alábbi lépéseket:

1. Válassza ki az alkalmazást az Azure Active Directory **Alkalmazásregisztrációk** területén.

   ![alkalmazás kiválasztása](./media/databricks-extract-load-sql-data-warehouse/select-app.png)

2. Másolja ki az **Alkalmazásazonosítót**, és tárolja az alkalmazás kódjában. Egyes [mintaalkalmazások](#log-in-as-the-application) ügyfél-azonosítóként hivatkoznak erre az értékre.

   ![ügyfél-azonosító](./media/databricks-extract-load-sql-data-warehouse/copy-app-id.png)

3. A hitelesítési kulcs létrehozásához válassza a **Beállítások** elemet.

   ![beállítások kiválasztása](./media/databricks-extract-load-sql-data-warehouse/select-settings.png)

4. A hitelesítési kulcs létrehozásához válassza a **Kulcsok** elemet.

   ![kulcsok kiválasztása](./media/databricks-extract-load-sql-data-warehouse/select-keys.png)

5. Adjon meg egy leírást és egy időtartamot a kulcshoz. Ha elkészült, kattintson a **Mentés** elemre.

   ![kulcs mentése](./media/databricks-extract-load-sql-data-warehouse/save-key.png)

   A kulcs mentése után megjelenik a kulcs értéke. Másolja ezt az értéket, mivel később nem lesz lehetősége lekérni a kulcsot. A kulcsértéket és az alkalmazásazonosítót az alkalmazásként való bejelentkezéshez kell megadnia. A kulcsértéket olyan helyen tárolja, ahonnan az alkalmazás le tudja kérni.

   ![mentett kulcs](./media/databricks-extract-load-sql-data-warehouse/copy-key.png)

### <a name="get-tenant-id"></a>A bérlőazonosító beszerzése

Ha programozott módon jelentkezik be, át kell adnia a bérlőazonosítót a hitelesítési kérelemmel együtt.

1. Válassza az **Azure Active Directory** elemet.

   ![Azure Active Directory kiválasztása](./media/databricks-extract-load-sql-data-warehouse/select-active-directory.png)

1. A bérlőazonosító lekéréséhez válassza ki az Azure AD-bérlőjéhez tartozó **Tulajdonságok** elemet.

   ![Azure AD tulajdonságok kiválasztása](./media/databricks-extract-load-sql-data-warehouse/select-ad-properties.png)

1. Másolja ki a **Címtár-azonosítót**. Ez az érték a bérlőazonosítója.

   ![bérlőazonosító](./media/databricks-extract-load-sql-data-warehouse/copy-directory-id.png) 

## <a name="upload-data-to-data-lake-store"></a>Adatok feltöltése a Data Lake Store-ba

Ebben a szakaszban feltölt egy mintaadatfájlt a Data Lake Store-ba. Ezt a fájlt később az Azure Databricksben fogja használni átalakítások futtatására. Az oktatóanyagban használt mintaadatok (**small_radio_json.json**) ebben a [GitHub-adattárban](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) érhetők el.

1. Az [Azure Portalon](https://portal.azure.com) válassza ki a létrehozott Data Lake Store-fiókot.

2. Az **Áttekintés** lapon kattintson az **Adatkezelő** elemre.

    ![Adatkezelő megnyitása](./media/databricks-extract-load-sql-data-warehouse/open-data-explorer.png "Adatkezelő megnyitása")

3. Az Adatkezelőben kattintson a **Feltöltés** elemre.

    ![Feltöltés lehetőség](./media/databricks-extract-load-sql-data-warehouse/upload-to-data-lake-store.png "Feltöltés lehetőség")

4. A **Fájlok feltöltése** területen tallózással keresse meg a mintaadatfájl helyét, majd válassza a **Kiválasztott fájlok hozzáadása** lehetőséget.

    ![Feltöltés lehetőség](./media/databricks-extract-load-sql-data-warehouse/upload-data.png "Feltöltés lehetőség")

5. Ebben az oktatóanyagban feltöltötte az adatfájlt a Data Lake Store gyökerébe. A fájl így már elérhető a következő helyen: `adl://<YOUR_DATA_LAKE_STORE_ACCOUNT_NAME>.azuredatalakestore.net/small_radio_json.json`.

## <a name="associate-service-principal-with-azure-data-lake-store"></a>Szolgáltatásnév társítása az Azure Data Lake Store-ral

Ebben a szakaszban társítja az Azure Data Lake Storage-fiókban található adatokat a létrehozott Azure Active Directory-szolgáltatásnévvel. Ezzel biztosítja, hogy el lehessen érni a Data Lake Store-fiókot az Azure Databricksből. A cikkben vázolt forgatókönyv szerint az adatokat a Data Lake Storage-ból olvassuk ki, és egy SQL Data Warehouse-beli táblába töltjük be. Ahogy a [Data Lake Storage-beli hozzáférés-vezérlést](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions) áttekintő cikk is írja, a Data Lake Storage-ban tárolt fájlok olvasási hozzáféréséhez a következők szükségesek:

- **Végrehajtási** jogosultság a fájlt tartalmazó mappastruktúra összes mappájához.
- **Olvasási** jogosultság magára a fájlra vonatkozóan.

A fenti engedélyek megadásához kövesse az alábbi lépéseket.

1. Az [Azure Portalon](https://portal.azure.com) válassza ki a létrehozott Data Lake Storage-fiókot, majd válassza az **Adatkezelő** lehetőséget.

    ![Adatkezelő indítása](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-data-explorer.png "Adatkezelő indítása")

2. Mivel ebben a forgatókönyvben a mintaadatfájl a mappaszerkezet gyökerében található, csak a mappa gyökeréhez kell kiosztani a **végrehajtási** engedélyt. Ehhez az adatkezelő gyökérkönyvtárában válassza ki a **Hozzáférés** elemet.

    ![Mappa hozzáférés-vezérlési listáinak megadása](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-1.png "Mappa hozzáférés-vezérlési listáinak megadása")

3. A **Hozzáférés** területen válassza a **Hozzáadás** lehetőséget.

    ![Mappa hozzáférés-vezérlési listáinak megadása](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-2.png "Mappa hozzáférés-vezérlési listáinak megadása")

4. Az **Engedélyek hozzárendelése** területen kattintson a **Felhasználó vagy csoport kiválasztása** lehetőségre, és keressen rá a korábban létrehozott Azure Active Directory-szolgáltatásnévre.

    ![Data Lake Store-hozzáférés hozzáadása](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-3.png "Data Lake Store-hozzáférés hozzáadása")

    Válassza ki a hozzárendelni kívánt AAD-szolgáltatásnevet, és kattintson a **Kiválasztás** gombra.

5. Az **Engedélyek hozzárendelése** területen kattintson az **Engedélyek kiválasztása** > **Végrehajtás** elemre. Tartsa meg a többi alapértelmezett értéket, és kattintson az **OK** gombra az **Engedélyek kiválasztása**, majd az **Engedélyek hozzárendelése** területen.

    ![Data Lake Store-hozzáférés hozzáadása](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-4.png "Data Lake Store-hozzáférés hozzáadása")

6. Lépjen vissza az Adatkezelőhöz, és kattintson arra a fájlra, amelyhez hozzá szeretné rendelni az olvasási engedélyt. A **Fájlelőnézet** területen válassza a **Hozzáférés** elemet.

    ![Data Lake Store-hozzáférés hozzáadása](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-file-1.png "Data Lake Store-hozzáférés hozzáadása")

7. A **Hozzáférés** területen válassza a **Hozzáadás** lehetőséget. Az **Engedélyek hozzárendelése** területen kattintson a **Felhasználó vagy csoport kiválasztása** lehetőségre, és keressen rá a korábban létrehozott Azure Active Directory-szolgáltatásnévre.

    ![Data Lake Store-hozzáférés hozzáadása](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-3.png "Data Lake Store-hozzáférés hozzáadása")

    Válassza ki a hozzárendelni kívánt AAD-szolgáltatásnevet, és kattintson a **Kiválasztás** gombra.

8. Az **Engedélyek hozzárendelése** területen kattintson az **Engedélyek kiválasztása** > **Olvasás** elemre. Kattintson az **OK** gombra az **Engedélyek kiválasztása**, majd az **Engedélyek hozzárendelése** területen.

    ![Data Lake Store-hozzáférés hozzáadása](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-file-2.png "Data Lake Store-hozzáférés hozzáadása")

    A szolgáltatásnév most már rendelkezik a szükséges engedélyekkel ahhoz, hogy olvassa a mintaadatfájlt az Azure Data Lake Storage-ból.

## <a name="extract-data-from-data-lake-store"></a>Adatok kinyerése a Data Lake Store-ból

Ebben a szakaszban létrehoz egy jegyzetfüzetet az Azure Databricks-munkaterületen, majd kódrészleteket futtat az adatok Data Lake Store-ból az Azure Databricksbe való kinyeréséhez.

1. Az [Azure Portalon](https://portal.azure.com) lépjen a létrehozott Azure Databricks-munkaterülethez, majd válassza a **Munkaterület indítása** elemet.

2. A bal oldali panelen válassza a **Munkaterület** elemet. A **Munkaterület** legördülő menüből válassza a **Létrehozás** > **Jegyzetfüzet** lehetőséget.

    ![Notebook létrehozása a Databricksben](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Notebook létrehozása a Databricksben")

2. A **Jegyzetfüzet létrehozása** párbeszédpanelen adja meg a jegyzetfüzet nevét. Válassza a **Scala** nyelvet, majd válassza ki a korábban létrehozott Spark-fürtöt.

    ![Notebook létrehozása a Databricksben](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Notebook létrehozása a Databricksben")

    Kattintson a **Létrehozás** gombra.

3. Illessze be az alábbi kódrészletet egy üres kódcellába, és cserélje le a helyőrző értékeket az Azure Active Directory-szolgáltatásnévhez tartozó, korábban mentett értékekkel.

        spark.conf.set("dfs.adls.oauth2.access.token.provider.type", "ClientCredential")
        spark.conf.set("dfs.adls.oauth2.client.id", "<APPLICATION-ID>")
        spark.conf.set("dfs.adls.oauth2.credential", "<AUTHENTICATION-KEY>")
        spark.conf.set("dfs.adls.oauth2.refresh.url", "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token")

    A kódcella futtatásához nyomja le a **SHIFT + ENTER** billentyűparancsot.

4. Most már betöltheti a Data Lake Store-ban lévő JSON-mintafájlt az Azure Databricks adathalmazaként. Illessze be az alábbi kódrészletet egy új kódcellába, cserélje le a helyőrző értéket, majd nyomja le a **SHIFT + ENTER** billentyűparancsot.

        val df = spark.read.json("adl://<DATA LAKE STORE NAME>.azuredatalakestore.net/small_radio_json.json")

5. Futtassa az alábbi kódrészletet az adathalmaz tartalmának megtekintéséhez.

        df.show()

    Az alábbi kódrészlethez hasonló kimenet jelenik meg:

        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
        | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
        | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
        ...
        ...

Ezzel kinyerte az adatokat az Azure Data Lake Store-ból az Azure Databricksbe.

## <a name="transform-data-in-azure-databricks"></a>Adatok átalakítása az Azure Databricksben

A nyers **small_radio_json.json** mintaadat egy rádióállomás hallgatóit rögzíti, és számos oszloppal rendelkezik. Ebben a szakaszban átalakítja az adatokat, hogy a rendszer csak bizonyos oszlopokat kérjen le az adatkészletből. 

1. Első lépésként kérje le csak a *firstName*, *lastName*, *gender*, *location*, és *level* oszlopokat a már létrehozott adathalmazból.

        val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
        specificColumnsDf.show()

    Az alábbi kódrészletben látható kimenetet fogja kapni:

        +---------+----------+------+--------------------+-----+
        |firstname|  lastname|gender|            location|level|
        +---------+----------+------+--------------------+-----+
        | Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
        |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
        |     Liam|     Watts|     M|New York-Newark-J...| paid|
        |     Tess|  Townsend|     F|Nashville-Davidso...| free|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
        |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        |     Tess|  Townsend|     F|Nashville-Davidso...| free|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |     Liam|     Watts|     M|New York-Newark-J...| paid|
        |     Liam|     Watts|     M|New York-Newark-J...| paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        +---------+----------+------+--------------------+-----+

2.  Az adatok további átalakításához nevezze át a **level** oszlopot a következőre: **subscription_type**.

        val renamedColumnsDf = specificColumnsDf.withColumnRenamed("level", "subscription_type")
        renamedColumnsDf.show()

    Az alábbi kódrészletben látható kimenetet fogja kapni.

        +---------+----------+------+--------------------+-----------------+
        |firstname|  lastname|gender|            location|subscription_type|
        +---------+----------+------+--------------------+-----------------+
        | Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
        |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
        |     Liam|     Watts|     M|New York-Newark-J...|             paid|
        |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
        |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |     Liam|     Watts|     M|New York-Newark-J...|             paid|
        |     Liam|     Watts|     M|New York-Newark-J...|             paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        +---------+----------+------+--------------------+-----------------+

## <a name="load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése az Azure SQL Data Warehouse-ba

Ebben a szakaszban feltölti az átalakított adatokat az Azure SQL Data Warehouse-ba. Az Azure Databricks Azure SQL Data Warehouse-összekötőjével közvetlenül feltölthet egy adathalmazt SQL Data Warehouse-táblaként.

Amint korábban említettük, az SQL Data Warehouse-összekötő az Azure Blob Storage-ot használja ideiglenes tárhelyként, amelybe feltölti az Azure Databricks és az Azure SQL Data Warehouse között áthelyezett adatokat. Ezért első lépésként adja meg a tárfiókhoz való csatlakozáshoz szükséges konfigurációt. A cikk előfeltételeinek részeként korábban már létre kellett hoznia a fiókot.

1. Adja meg az Azure Storage-fiók Azure Databricksből való eléréséhez szükséges konfigurációt.

        val blobStorage = "<STORAGE ACCOUNT NAME>.blob.core.windows.net"
        val blobContainer = "<CONTAINER NAME>"
        val blobAccessKey =  "<ACCESS KEY>"

2. Adjon meg egy ideiglenes mappát, amelyet a rendszer az adatok Azure Databricks és Azure SQL Data Warehouse közötti áthelyezésekor fog használni.

        val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"

3. Futtassa az alábbi kódrészletet az Azure Blob Storage hozzáférési kulcsainak a konfigurációban való tárolásához. Így nem kell egyszerű szövegként tárolnia a hozzáférési kulcsot a jegyzetfüzetben.

        val acntInfo = "fs.azure.account.key."+ blobStorage
        sc.hadoopConfiguration.set(acntInfo, blobAccessKey)

4. Adja meg az Azure SQL Data Warehouse-példányhoz való csatlakozáshoz szükséges értékeket. Az előfeltételek részeként már létre kellett hoznia egy SQL Data Warehouse-t.

        //SQL Data Warehouse related settings
        val dwDatabase = "<DATABASE NAME>"
        val dwServer = "<DATABASE SERVER NAME>" 
        val dwUser = "<USER NAME>"
        val dwPass = "<PASSWORD>"
        val dwJdbcPort =  "1433"
        val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
        val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
        val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass

5. Futtassa az alábbi kódrészletet a **renamedColumnsDF** nevű átalakított adathalmaz SQL Data Warehouse-táblaként való betöltéséhez. Ez a kódrészlet létrehoz egy **SampleTable** nevű táblát az SQL-adatbázisban. Vegye figyelembe, hogy az Azure SQL DW használatához egy főkulcs szükséges.  A főkulcsot az SQL Server Management Studióban, a „CREATE MASTER KEY;” parancs végrehajtásával hozhatja létre.

        spark.conf.set(
          "spark.sql.parquet.writeLegacyFormat",
          "true")
        
        renamedColumnsDf.write
            .format("com.databricks.spark.sqldw")
            .option("url", sqlDwUrlSmall) 
            .option("dbtable", "SampleTable")
            .option( "forward_spark_azure_storage_credentials","True")
            .option("tempdir", tempDir)
            .mode("overwrite")
            .save()

6. Csatlakozzon az SQL-adatbázishoz, és ellenőrizze, hogy látja-e a **SampleTable** nevű táblát.

    ![Mintatábla ellenőrzése](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Mintatábla ellenőrzése")

7. Futtasson egy választó lekérdezést a tábla tartalmának ellenőrzéséhez. Ugyanazokat az adatokat kell tartalmaznia, mint a **renamedColumnsDF** adathalmaznak.

    ![Mintatábla tartalmának ellenőrzése](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Mintatábla tartalmának ellenőrzése")

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag befejezése után leállíthatja a fürtöt. Ehhez az Azure Databricks-munkaterület bal oldali panelén kattintson a **Fürtök** elemre. A leállítani kívánt fürtnél vigye az egérmutatót a **Műveletek** oszlopban található három pont fölé, majd kattintson a **Leállítás** ikonra.

![Databricks-fürt leállítása](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Databricks-fürt leállítása")

Ha nem állítja le manuálisan a fürtöt, az automatikusan le fog állni, amennyiben a fürt létrehozásakor bejelölte a **Leállítás ___ percnyi tétlenség után** jelölőnégyzetet. Ebben az esetben a fürt automatikusan leáll, ha a megadott ideig inaktív volt.

## <a name="next-steps"></a>További lépések 
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure Databricks-munkaterület létrehozása
> * Spark-fürt létrehozása az Azure Databricksben
> * Azure Data Lake Store-fiók létrehozása
> * Adatok feltöltése az Azure Data Lake Store-ba
> * Jegyzetfüzet létrehozása az Azure Databricksben
> * Adatok kinyerése a Data Lake Store-ból
> * Adatok átalakítása az Azure Databricksben
> * Adatok betöltése az Azure SQL Data Warehouse-ba

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan streamelhetők valós időben az adatok az Azure Databricksbe az Azure Event Hubs használatával.

> [!div class="nextstepaction"]
>[Adatok streamelése az Azure Databricksbe az Event Hubs használatával](databricks-stream-from-eventhubs.md)
