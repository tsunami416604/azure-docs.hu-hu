---
title: Analytics-lekérdezések futtatásához bérlői adatbázisok az Azure SQL Data Warehouse |} A Microsoft Docs
description: Azure SQL Database, az SQL Data Warehouse, Azure Data Factory vagy a Power BI kinyert adatok segítségével a több-bérlős elemzési lekérdezések.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: MightyPen, sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: a658e2fe32ec95dfabad54684a0c9095af7a341d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850292"
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>Az Azure SQL Database, az SQL Data Warehouse, a Data Factory és a Power BI SaaS-analytics megismerése

Ebben az oktatóanyagban vezeti végig egy teljes körű elemzési forgatókönyvet is. A forgatókönyv bemutatja, hogyan bérlői adatok elemzési teheti hatékonyabbá a szoftvergyártók intelligens döntéseket hozhat. Minden bérlői adatbázis kinyert adatok segítségével, használhatja analytics betekintést bérlő viselkedése, beleértve azok a Wingtip Tickets SaaS-mintaalkalmazás használatát. Ebben a forgatókönyvben három lépésből áll: 

1.  **Adatok kinyerése** egyes bérlőkhöz adatbázis-analytics tárolójába, ebben az esetben egy SQL Data Warehouse.
2.  **A kibontott adatok optimalizálása** analytics feldolgozás céljából.
3.  Használat **üzleti intelligencia** rajzolja meg a döntéshozatalhoz is útmutató hasznos insights eszközök. 

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> - Hozzon létre a bérlő analytics betöltése tárolni.
> - Az Azure Data Factory (ADF) használatával az adatok kinyerése az egyes bérlői adatbázisok az analytics data warehouse-bA.
> - Optimalizálhatja a kinyert adatok (reorganize csillagséma be).
> - Az analytics data warehouse lekérdezéséhez.
> - Használja a Power BI-adatvizualizáció javaslat javításai, és jelölje ki a bérlő adatainak trendeket.

![architectureOverView](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Elemzési adatok kinyert bérlő

SaaS-alkalmazásokat a felhőben egy potenciálisan nagy mennyiségű bérlői tartsa. Ezeket az adatokat biztosít a művelet insights gazdag forrását és az alkalmazás használatának és a bérlők viselkedését. Ezen elemzési funkcióinak fejlesztését, használhatóságuk javítását és más befektetéseket az alkalmazások és a platform képes útmutató.

Az adatokhoz hozzáférő összes bérlőre vonatkozó akkor egyszerű, ha minden adat egyetlen több bérlős adatbázisban. Hozzáférés azonban összetettebb, ha méretezve akár több ezer adatbázis között elosztva. Egy összetettsége eláraszt módja elemzési adatbázis vagy egy lekérdezés data warehouse az adatok kinyerése érdekében.

Ez az oktatóanyag a Wingtip Tickets alkalmazás egy teljes körű elemzési forgatókönyvet mutat be. Először [Azure Data Factory (ADF)](../data-factory/introduction.md) jegyek értékesítési és a kapcsolódó adatok kinyerése az egyes bérlői adatbázisok a vezénylési eszközt használja. Ezeket az adatokat analytics tárolóban előkészítési táblába tölti. Az analytics-tároló vagy egy SQL Database vagy az SQL Data Warehouse lehet. Ebben az oktatóanyagban [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) azokat az elemzéseket, tárolásához.

Ezután a kibontott adatok átalakíthatók és egy betöltött [csillagséma-](https://www.wikipedia.org/wiki/Star_schema) táblákat. A táblák egy központi ténytáblát és a kapcsolódó dimenziótábla áll:

- A központi ténytáblájából a csillagséma-jegy adatokat tartalmaz.
- A dimenziótáblák adatainak helyszínek, események, a vásárlók tartalmaz, és a dátumok vásárolhat.

Együtt a közép- és táblák engedélyezése hatékony elemzési dimenziófeldolgozás. A jelen oktatóanyagban használt csillagséma jelenik meg a következő képen:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/starschematables.JPG)

A csillagséma-táblákat, a rendszer megkérdezi. Lekérdezési eredmények jelennek meg vizuálisan jelölje ki a bérlő viselkedése és használata során az alkalmazás elemzések a Power BI használatával. A csillagséma az elérhetővé lekérdezések futtatása:

- Akik van vásárlása jegyek és melyik helyszín.
- Minták és trendek felismerése, a jegyek értékesítésére.
- Minden egyes helyszín relatív népszerűsége.

Ebben az oktatóanyagban példákat alapszintű, amelyek segítségével adatokat a Wingtip Tickets adatokból. Ismertetése, hogyan minden egyes helyszín szolgáltatását használja, előfordulhat, hogy a Wingtip Tickets szállító kell vennie, például több vagy kevesebb aktív helyszínek megcélzó különböző szolgáltatáscsomaggal. 

## <a name="setup"></a>Beállítás

### <a name="prerequisites"></a>Előfeltételek

> [!NOTE]
> Ez az oktatóanyag az Azure Data Factory jelenleg korlátozott előzetes verzió (társított szolgáltatás paraméterezés) funkcióit használja. Ha szeretné elvégezni ezt az oktatóanyagot, adja meg az előfizetés-azonosító [Itt](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu). Küldünk egy megerősítő, amint az előfizetés engedélyezve van.

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:
- A Wingtip Tickets SaaS adatbázis Per bérlői alkalmazás van telepítve. Kevesebb mint öt perc alatt üzembe helyezéséhez lásd: [üzembe helyezése és megismerése a Wingtip SaaS-alkalmazás](saas-dbpertenant-get-started-deploy.md).
- A Wingtip Tickets SaaS adatbázis Per bérlői parancsfájlok és az alkalmazás [forráskódját](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) letöltődnek a Githubról. Lásd: Töltse le az utasításokat. Ügyeljen arra, hogy *feloldása a zip-fájl* tartalmának beolvasása előtt.
- A Power BI Desktop telepítve van. [A Power BI Desktop letöltése](https://powerbi.microsoft.com/downloads/).
- További bérlők kötegét kiépítését, tekintse meg a [ **kiépítése katalogizálása oktatóanyag**](saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>A bemutató adatok létrehozása

Ez az oktatóanyag bemutatja a analytics jegy értékesítési adatok. Ebben a lépésben a bérlőknek hozhat létre jegyet adatokat. Egy későbbi lépésben az adatok elemzéshez ki kell olvasni. _Győződjön meg arról, a bérlők kötegelt kiépítése_ (leírt módon korábban), hogy elég adat elérhetővé különböző számos jegyet vásárlási mintákat.

1. A PowerShell ISE-ben nyissa meg a *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*, és állítsa be a következő értéket:
    - **$DemoScenario** = **1** jegyek beszerzése minden helyszínen
2. Nyomja meg **F5** futtassa a szkriptet, és hozzon létre a jegyvásárlások naplóját a beszerzése minden helyszínen. Minden 20-bérlőre, az a parancsfájl tickets tízezer állít elő, és akár 10 percet is igénybe vehet.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>Az SQL Data Warehouse, adat-előállító üzembe helyezése és a Blob Storage-ban 
A Wingtip Tickets alkalmazás tranzakciós adatokat a bérlők számára sok adatbázis van elosztva. Az Azure Data Factory (ADF) segítségével előkészíthető a kinyerési, betöltési és átalakítási (ELT), ezeket az adatokat a data warehouse-bA. Adatok betöltése az SQL Data Warehouse leghatékonyabban, az ADF köztes blob fájlokba kinyeri az adatokat, majd [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) az adatok betöltésére az adattárházba.   

Ebben a lépésben üzembe helyezi a további erőforrások, a jelen oktatóanyagban használt: egy SQL Data Warehouse nevű _tenantanalytics_, egy Azure Data Factory nevű _dbtodwload -\<felhasználói\>_  , és a egy Azure storage-fiók _wingtipstaging\<felhasználói\>_. A storage-fiók segítségével kinyert adatok fájlok átmeneti tárolására blobként előtt be töltve a data warehouse-bA. Ez a lépés is üzembe helyezi az adatraktár sémájának és koordinálhatja az ELT folyamatok ADF folyamatok meghatározása.
1. A PowerShell ISE-ben nyissa meg a *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* és állítsa be:
    - **$DemoScenario** = **2** bérlői analitikai adattárház, a blob storage és az adat-előállító üzembe helyezése 
1. Nyomja meg **F5** a bemutató-parancsfájl futtatásához, és az Azure-erőforrások üzembe helyezése. 

Most, tekintse át a telepített Azure-erőforrások:
#### <a name="tenant-databases-and-analytics-store"></a>Bérlői adatbázisok és az analytics-tároló
Használat [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) való csatlakozáshoz **tenants1-dpt -&lt;felhasználói&gt;**  és **katalógus-dpt -&lt;felhasználói&gt;**  kiszolgálók. Cserélje le &lt;felhasználói&gt; az alkalmazás üzembe helyezésekor használt értékkel. -Bejelentkezés használatához = *fejlesztői* és a jelszó = *P\@ssword1*. Tekintse meg a [bevezető oktatóanyag](saas-dbpertenant-wingtip-app-overview.md) további útmutatást.

![Csatlakozás SQL Database-kiszolgáló a ssms használatával](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

Az Object Explorer:

1. Bontsa ki a *tenants1-dpt -&lt;felhasználói&gt;*  kiszolgáló.
1. Bontsa ki az adatbázisok csomópontot, és a bérlői adatbázisok listájának megtekintéséhez.
1. Bontsa ki a *katalógus-dpt -&lt;felhasználói&gt;*  kiszolgáló.
1. Győződjön meg arról, hogy megjelenik-e a elemzési tárolja, amely tartalmazza a következő objektumok:
    1. Táblák **raw_Tickets**, **raw_Customers**, **raw_Events** és **raw_Venues** a bérlői adatbázisok származó nyers kinyert adatok tárolásához.
    1. A csillagséma-táblák **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, és **dim_Dates** .
    1. A tárolt eljárás **sp_transformExtractedData** átalakítja az adatokat, és töltse be a csillagséma-táblákat.

![DWtables](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage
1. Az a [az Azure Portal](https://ms.portal.azure.com), keresse meg az erőforráscsoportot, amelyet az alkalmazás üzembe helyezéséhez használt. Győződjön meg arról, hogy a tárfiók neve **wingtipstaging\<felhasználói\>**  hozzá lett adva.

   ![DWtables](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. Kattintson a **wingtipstaging\<felhasználói\>**  tárfiókban található objektumok megismerése.
1. Kattintson a **Blobok** csempe
1. Kattintson a tároló **configfile**
1. Ellenőrizze, hogy **configfile** nevű JSON-fájlt tartalmaz **TableConfig.json**. Ez a fájl tartalmazza a forrás és cél táblanevek, az oszlopnevek és tracker oszlop neve.

#### <a name="azure-data-factory-adf"></a>Az Azure Data Factory (ADF)
Az a [az Azure Portal](https://ms.portal.azure.com) az erőforráscsoportban, győződjön meg arról, hogy az Azure Data Factory neve _dbtodwload -\<felhasználói\>_  hozzá lett adva. 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

Ez a szakasz ismerteti az adat-előállító létrehozása. Indítsa el a data factory az alábbi lépésekkel:
1. A portálon kattintson a data factory nevű **dbtodwload -\<felhasználói\>**.
2. Kattintson a **létrehozás és Monitorozás** csempére kattintva indítsa el a Data Factory-Tervező egy új lapon. 

## <a name="extract-load-and-transform-data"></a>Kinyerési, betöltési, és az adatok átalakítása
Az Azure Data Factory a kinyerés, betöltés és adatok átalakítása vezénylésre szolgál. Ebben az oktatóanyagban az adatok kinyerése négy különböző SQL-nézetek az egyes bérlői adatbázisok: **rawTickets**, **rawCustomers**, **rawEvents**, és  **rawVenues**. Ezek a nézetek helyszín azonosítója, tartalmazza, így a, is minden egyes helyszín az adatraktárban adatokat különbséget. Az adatok betöltése az adatraktárban lévő adatok megfelelő átmeneti tárolási táblákba: **raw_Tickets**, **raw_customers**, **raw_Events** és **raw_Venue**. Tárolt eljárás majd alakítja át a nyers adatokat, és feltölti a csillagséma-táblák: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** , és **dim_Dates**.

Az előző szakaszban üzembe helyezett, és a szükséges Azure-erőforrást, beleértve az adat-előállító inicializálni. Az üzembe helyezett adat-előállító folyamatok, a társított szolgáltatások, adatkészletek stb kinyerése, betöltése és a bérlői adatok átalakításához szükséges tartalmazza. Vizsgáljuk meg ezeket további objektumokat, és a bérlői adatbázisok adatok áthelyezése a data warehouse-bA a folyamatot az eseményindító.

### <a name="data-factory-pipeline-overview"></a>Data factory-folyamat áttekintése
Ez a szakasz ismerteti az adat-előállítóban létrehozott objektumok. Az alábbi ábra a teljes munkafolyamatba, a jelen oktatóanyagban használt ADF-folyamatot ismerteti. Ha inkább a folyamat későbbi vizsgálata, és először megjelenítjük az eredményeket, ugorjon a következő szakaszra **a folyamat futásának aktiválásához**.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

Az Áttekintés lapon váltson **Szerző** lapra a bal oldali panelen, és figyelje meg, hogy háromféle [folyamatok](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) és három [adatkészletek](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) létrehozott.
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

A három beágyazott folyamatok a következők: SQLDBToDW DBCopy és TableCopy.

**1 – SQLDBToDW folyamat** kikeresi a bérlői adatbázisokat, a katalógus-adatbázisban tárolt nevét (táblanév: [__ShardManagement]. [ ShardsGlobal]), és minden bérlői adatbázis végrehajtja a **DBCopy** folyamat. A művelet befejezését követően a megadott **sp_TransformExtractedData** tárolt eljárás séma, hajtja végre. Ez a tárolt eljárás alakítja át a betöltött adatokra az átmeneti tárolási táblázatokban, és feltölti a csillagséma-táblákat.

**2 – DBCopy folyamat** megkeresi a forrás-táblák és oszlopok nevei a blob storage-ban tárolt konfigurációs fájlból.  A **TableCopy** mind a négy tábla majd futtassa a folyamatot: TicketFacts, CustomerFacts, EventFacts és VenueFacts. A **[Foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** tevékenység végrehajtása párhuzamosan történik mind a 20 adatbázisra vonatkozó. Az ADF lehetővé teszi, hogy egy legfeljebb 20 ciklus ismétléseinek párhuzamosan futtatni. Érdemes lehet további adatbázisok több folyamatot létrehozni.    

**3 – TableCopy folyamat** használ sorszintű verziószámok SQL Database-ben (_rowversion_) megváltozott, vagy frissített sorainak azonosításához. A kezdő és a teljes sor verziójú a sorok beolvasása a forrástáblákból a megkeresi ezt a tevékenységet. A **CopyTracker** tábla összes bérlői adatbázisban tárolt nyomon követi az egyes forrás tábla minden egyes Futtatás kinyert utolsó sora. Új vagy módosított sorok másolja az adatraktárban lévő adatok megfelelő előkészítési táblák: **raw_Tickets**, **raw_Customers**, **raw_Venues**, és **raw_ Események**. Végül az utolsó sort verzióját a rendszer menti a **CopyTracker** tábla a következő kivonás kezdeti sor verzióval használható. 

Hivatkozás az adat-előállító, a forrás SQL-adatbázisok, a cél SQL Data Warehouse és a köztes Blob storage, nincsenek is három paraméteres társított szolgáltatást. Az a **Szerző** fülre, kattintson a **kapcsolatok** fedezheti fel a társított szolgáltatások, az alábbi képen látható módon:

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

A három társított szolgáltatások megfelelő, tekintse meg az adatokat, használja a folyamat tevékenységek bemeneti vagy kimeneti adatkészletek három vannak. Fedezze fel az egyes kapcsolatok és a használt paraméterek megfigyelése az adatkészletek. _Azure BLOB_ a forrás- és táblákat és oszlopokat, valamint a tracker oszlopban adatvédelmiszint tartalmazó konfigurációs fájlt mutat.
  
### <a name="data-warehouse-pattern-overview"></a>Az adatraktár-minta áttekintése
Az SQL Data Warehouse segítségével az analytics tárolóként végez összesítést a bérlői adatok. Ebben a példában a PolyBase segítségével adatok betöltése az SQL Data warehouse-bA. Nyers adatok betöltése, amelyek egy identitásoszlop sorokat, amelyek a csillagséma-táblákba átalakítása nyomon követheti, hogy átmeneti tárolási táblákba. Az alábbi képen látható a betöltés minta: ![loadingpattern](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

Ebben a példában lassan módosítása dimenzió (. SCD) típusú 1 dimenziótáblák használhatók. Minden egyes dimenziónak van egy identitásoszlop segítségével meghatározott helyettes kulcs. Ajánlott eljárásként a dátum dimenzió tábla előre összeállított időt takaríthat meg. A többi dimenziótábla, a CREATE TABLE a SELECT... (CTAS) utasítás segítségével hozzon létre egy ideiglenes táblát tartalmazó a meglévő módosított, és nem módosított sorok együtt a helyettes kulcs. Ez a lépés IDENTITY_INSERT = ON. Új sorok majd beszúrja a táblázat az IDENTITY_INSERT = off BEÁLLÍTÁST. A meglévő dimenzió-tábla neve egyszerűen alkalmazásfrissítéseket, és az ideiglenes tábla neve lesz az új táblát. Mindegyik futtatás előtt a régi dimenziótábla törlődik.

A ténytábla előtt dimenziótáblák töltődnek be. Ez az alkalmazás-előkészítés biztosítja, hogy az egyes beérkező egyedkapcsolat minden hivatkozott dimenziók már létezik. Ahogy a tények töltődnek be, a kapcsolódó dimenzióknál üzleti kulcs egyezik, és az egyes (tény) hozzáadja a hozzá tartozó helyettes kulcs.

Az utolsó lépés az átalakítás készen áll a folyamat a következő végrehajtásra az átmeneti adatok törlése.
   
### <a name="trigger-the-pipeline-run"></a>A folyamatfuttatás aktiválása
Kövesse a teljes futtassa az alábbi lépések csomagolja ki, betöltési és átalakítási folyamat az összes bérlői adatbázist:
1. Az a **Szerző** az ADF felhasználói felületén kattintson a lap **SQLDBToDW** folyamatot a bal oldali ablaktáblán.
1. Kattintson a **eseményindító** és onnan a lekért kattintson a menüre **Aktiválás most**. Ez a művelet azonnal futtatja a folyamatot. Egy éles forgatókönyvet szeretné határoz meg egy ütemterv futtatja a folyamatot az adatok ütemezett frissítésére.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. A **Folyamatfuttatás** kattintson **Befejezés**.
 
### <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása
1. ADF-felhasználói felületén váltson a **figyelő** fülre a bal oldali menüben.
1. Kattintson a **frissítése** mindaddig, amíg SQLDBToDW folyamat állapota **sikeres**.
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. Csatlakozás az adattárházhoz az ssms-ben, és győződjön meg arról, hogy ezek a táblák adatait lett betöltve a csillagséma-táblák lekérdezése.

A folyamat befejezése után a ténytábla jegyek beszerzése minden helyszínen értékesítési adatait tartalmazza, és a dimenziótáblák fel van töltve a megfelelő helyszínek, események és ügyfelek.

## <a name="data-exploration"></a>Az adatok feltárása

### <a name="visualize-tenant-data"></a>Bérlői adatok megjelenítése

A csillagséma-lévő adatokat biztosít minden a jegy értékesítési adatokat az elemzéshez szükséges. Vizualizációja grafikusan megkönnyíti a nagy méretű adatkészleteket trendeket megtekintéséhez. Ebben a szakaszban használhatja **Power BI** feldolgozására, és a bérlői adatok az adattárház megjelenítése.

Csatlakozás a Power bi-ba, illetve importálhatja a korábban létrehozott nézetek, kövesse az alábbi lépéseket:

1. Indítsa el a Power BI desktopban.
2. A kezdőlap menüszalagon válassza **adatok lekérése**, és válassza ki **több...** a menüből.
3. Az a **adatok lekérése** ablakban válassza **Azure SQL Database**.
4. Az adatbázis bejelentkezési ablakban írja be a kiszolgáló nevét (**katalógus-dpt -&lt;felhasználói&gt;. database.windows.net**). Válassza ki **importálás** a **adatkapcsolati mód**, és kattintson a **OK**. 

    ![sign-in-to-power-bi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Válassza ki **adatbázis** a bal oldali panelen, majd adjon meg felhasználónevet = *fejlesztői*, és adja meg a jelszó = *P\@ssword1*. Kattintson a **Connect** (Csatlakozás) gombra.  

    ![database-sign-in](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. Az a **kezelő** az analitikai adatbázis oldali ablaktábla, válassza ki a csillagséma-táblákat: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** és **dim_Dates**. Válassza ki **terhelés**. 

Gratulálunk! Sikeresen betöltötte az adatokat a Power BI-bA. Most vizsgálja meg az érdekes vizualizációkat követésével fontos információkhoz juthat a bérlők számára. Nézzük végig, hogyan analytics a Wingtip Tickets üzleti csapatnak adatvezérelt ajánlásokat tud biztosítani. A javaslatok segíthetnek az üzleti modell és a felhasználói élmény optimalizálása.

Indítsa el a használati változat megtekintéséhez a helyszínek között a jegy értékesítési adatok elemzése. Válassza a Power BI-ban minden egyes helyszín által értékesített jegyek száma a sáv diagram megrajzolásához jelenik meg. (A jegy generátor véletlenszerű változat, mert a találatok eltérő lehet.)
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

A fenti diagram megerősíti, hogy minden egyes helyszín által értékesített jegyek száma változhat. Helyszínek, amely további jegyek értékesítésére, amelyek kevesebb jegyek értékesítésére helyszínek részletesebben használja a szolgáltatást. Előfordulhat, hogy az erőforrás-elosztás másik bérlőben igényeinek megfelelően testre szabni itt lehetőséget.

Elemezheti az adatait, hogy lássa, hogyan jegyeladásokkal változnak idővel további. Válassza ki a beállításokat, az alábbi képen a Power bi-ban nyomtatandó 60 nappal minden nap értékesített jegyek száma látható.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

A fenti diagram mutatja, hogy az egyes helyszínek jegy értékesítési kiugrás. Kedvez a cél, hogy egyes helyszínek lehet, hogy lehet felhasználása rendszererőforrások aránytalanul megerősítése. Amennyiben nem áll sem nyilvánvaló minta esetén fordulhat elő, az adatforgalmi csúcsokhoz.

Tovább most vizsgálja meg ezek csúcsnapok értékesítés jelentőségét. Amikor hajtsa végre ezeket a csúcsok azt követően merülhet fel jegyek árusítása? Jeleníti meg napi értékesített jegyeket, jelölje be a Power BI-ban az alábbi képen látható beállításokat.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

Ez a diagram bemutatja, hogy egyes helyszínek értékesítési első napján nagyszámú jegyek értékesítésére. Amint a jegyek árusítása, ezek helyszínek, úgy tűnik, hogy mad sürgős. Ez a tevékenység által néhány helyszínek adatlöketek hatással lehet a szolgáltatás más bérlők számára.

Az adatok ismételt használatával ellenőrizheti a mad sürgős teljesül-e az összes esemény ezek helyszínek által üzemeltetett részletesen is. Az előző grafikon láthatta, hogy a Contoso Concert Hall értékesít számos jegyeket, és hogy Contoso is ugrásszerű jegyeladásokkal az egyes napokon. A Contoso Concert Hall, az események mindegyike értékesítési trendek összpontosító összegző jegyeladásokkal nyomtatandó Power BI beállítások módosításával. Hajtsa végre az összes esemény hajtsa végre az értékesítési minta? Próbálja ki az alábbihoz hasonlóan egy diagram előállításához.

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Ez minden esemény esetén a Contoso Concert Hall idővel összegző jegyeladásokkal ábrázolása a jeleníti meg, hogy a mad sürgős sem történik az összes esemény. A szűrőbeállítások, a többi helyszínek értékesítés trendekkel módosításával.

Minták jegyárusító betekintést az üzleti modell optimalizálása érdekében a Wingtip Tickets vezethet. Helyett az összes bérlőre vonatkozóan egyaránt díjszabási, például a Wingtip be kell vezetnie a szolgáltatási szintek a különböző számítási méretekre. Nagyobb helyszínek, amely naponta több jegyek értékesítésére kell egy magasabb szintű szolgáltatói szerződést (SLA) a magasabb szintű sikerült kínáljuk. Ezek a helyszínek lehet adatbázisaikat, adatbázis-erőforrás magasabb korlátokkal rendelkező készlet helyezi el. Minden szolgáltatási szint sikerült óránkénti értékesítési adatkeret, további díjakat számítanak fel díjat a meghaladja a lefoglalt rendelkezik. Nagyobb helyszínek, amelyek rendszeres adatlöketekkel értékesítési előnyös a magasabb szintű, és a Wingtip Tickets hatékonyabb szolgáltatást is értékesítheti.

Ugyanakkor az egyes Wingtip Tickets ügyfeleink panaszok, hogy azok kihívást jelent, hogy adja meg a szolgáltatás költsége elegendő jegyek értékesítésére. Például ilyen elemzést van lehetőség az alulteljesítő helyszínek jegyeladásokkal növelése érdekében. Magasabb értékesítési lenne növelje mintavételezéskor a szolgáltatás. Kattintson a jobb gombbal a fact_Tickets, és válassza ki **új mérték**. Adja meg az új mérték neve a következő kifejezést **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Válassza ki az alábbi Vizualizáció lehetőségek a relatív sikerük meghatározásához minden egyes helyszín által értékesített százalékos jegyek ábrázolásához.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

A fenti diagram bemutatja, hogy annak ellenére, hogy a legtöbb helyszínek több mint 80 %-a jegyek értékesítésére, néhány elterjedtek már több mint fele a munkaállomások adja meg. Játsszon egy kicsit az értékeket is minden egyes helyszín értékesített jegyek maximális és minimális aránya kiválasztásához.

## <a name="embedding-analytics-in-your-apps"></a>Szeretne elemzéseket beágyazni az alkalmazások 
Ebben az oktatóanyagban a több-bérlős analytics növelhető a bérlők a szoftver gyártójához ismeretekkel rendelkezik összpontosít. Analytics elemzéseket is megadhatja a _bérlők_, amelyekkel hatékonyabban kezelheti üzleti magukat. 

A Wingtip Tickets példában, korábban felderített, hogy jegyeladásokkal általában a kiszámítható mintázatú kövesse. Az ilyen elemzések érdekében alulteljesítő helyszínek boost jegyeladásokkal használhatók. Talán nincs ilyen módon támaszkodhatnak a machine learning-módszerekkel előre jelezni az események jegyeladásokkal lehetőséget. Az ár módosítások hatásainak sikerült is modellezni, hogy az ajánlat kedvezményesen kell elvégezni, ha a hatását. Power BI Embedded sikerült integrálni kell egy esemény felügyeleti alkalmazás megjelenítése az előre jelzett értékesítési, beleértve a teljes munkaállomások értékesített díjára és a bevétel alacsony értékesítése események hatását. A Power BI Embedded még akkor is is integrálhatók a ténylegesen alkalmazza a kedvezményt a jegy árak, jobb gombbal a Vizualizáció felületen.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A bérlői analitikák csillagsémával feltöltve SQL Data Warehouse üzembe helyezése.
> * Azure Data Factory használatával az adatok kinyerése az egyes bérlői adatbázisok az analytics data warehouse-bA.
> * Optimalizálhatja a kinyert adatok (reorganize csillagséma be).
> * Az analytics data warehouse lekérdezéséhez. 
> * A Power BI használatával az adatok trendek megjelenítése az összes bérlőre kiterjedő.

Gratulálunk!

## <a name="additional-resources"></a>További források

- További [oktatóanyagok, amely a Wingtip SaaS-alkalmazás útmutatóra](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
