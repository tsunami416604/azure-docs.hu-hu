---
title: Bérlői analytics lekérdezéseinek futtatásához adatbázisokat az Azure SQL Data Warehouse |} Microsoft Docs
description: Több-bérlős elemzési lekérdezések kibontani a több Azure SQL Database adatbázist adatokkal.
keywords: sql database-oktatóanyag
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/08/2017
ms.author: anjangsh; billgib; genemi
ms.openlocfilehash: 487dcd75113b92c1e50caca2ae9df34b4fb2548f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>Fedezze fel az Azure SQL Database, az SQL Data Warehouse, adat-előállító és a Power BI Szolgáltatottszoftver-elemzés

Ebben az oktatóanyagban akkor végezze el egy végpontok közötti analytics forgatókönyvet. A forgatókönyv bemutatja, hogyan analytics bérlői adatok is ellenőrizniük a szoftvergyártók intelligens vonatkozó döntések meghozatalában. Minden bérlő adatbázisból beolvasott adatok felhasználásával, segítségével analytics betekintést nyerhet bérlői viselkedése, beleértve a minta Wingtip jegyek SaaS-alkalmazás használatát. Ebben a forgatókönyvben a három lépést foglal magában: 

1.  **Adatok kinyerése** tőlük adatbázis analytics tárolóba, ebben az esetben egy SQL Data Warehouse.
2.  **A kibontott adatok optimalizálása** analytics feldolgozásra.
3.  Használjon **üzleti intelligencia** kimenő hasznos insights, amelyek révén is ismerteti. megrajzolásához eszközök. 

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> - Hozza létre a bérlőt, elemzés betöltése tárolja.
> - Ha adatokat szeretne kinyerni az egyes bérlői adatbázisok az analytics adatraktárba használja az Azure Data Factory (ADF).
> - A kibontott adatok (csillag sémába átszervez) optimalizálásához.
> - Az elemzés adatraktár lekérdezésére.
> - Használja a Power BI az adatok vizuális jelölje ki a bérlői adatforgalom trendeket, és lehetővé teszi a javításai javaslat.

![architectureOverView](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Kibontott bérlői adatok elemzés

SaaS-alkalmazásokhoz potenciálisan hatalmas mennyiségű bérlői adatok tárolásához a felhőben. Ezeket az adatokat a művelet észrevételeket gazdag forrását és az alkalmazás használatát és viselkedését a bérlők számára biztosít. Ezek insights szolgáltatás fejlesztési használhatóság fejlesztések és egyéb befektetések az alkalmazások és a platform is ismerteti.

Az adatok elérése az összes bérlőre vonatkozó használata egyszerű, amikor az adatok csak egy több-bérlős adatbázisban. De hozzáférés összetettebb, ha akár több ezer adatbázis léptékű pontjain. Egy összetettségét tame módja az analytics adatbázisba vagy egy lekérdezés adatraktár adatok kinyerése érdekében.

Ez az oktatóanyag a Wingtip jegyek alkalmazás egy végpont analytics forgatókönyvet mutat. Első, [Azure Data Factory (ADF)](../data-factory/introduction.md) jegyek értékesítési és kapcsolódó Ha adatokat szeretne kinyerni az egyes bérlői adatbázisok a vezénylési eszköz szolgál. Ezek az adatok átmeneti tárolási táblákba analytics tárolóban be van töltve. Az analytics-tároló egy SQL-adatbázis vagy egy SQL Data Warehouse sikerült lennie. Ez az oktatóanyag használja [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) , az analytics tárolja.

Ezután a kibontott adatok át legyenek-e, és egy betöltött [csillag-séma](https://www.wikipedia.org/wiki/Star_schema) táblákat. A táblák egy központi ténytábla és a kapcsolódó dimenziótáblák foglalják magukban:

- A csillag-séma a központi ténytábla jegy adatokat tartalmaz.
- A dimenziótáblák helyszínek, események, az ügyfelek adatait tartalmazza, és dátumokat vásároljon.

Együtt a központi és táblák engedélyezése hatékony analitikai dimenziófeldolgozás. Ebben az oktatóanyagban használt csillag-séma a következő kép jelenik meg:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/starschematables.JPG)

Végezetül a csillag-séma táblák megkérdezi a. Lekérdezés eredményei jelennek meg vizuálisan jelölje ki a bérlő viselkedést és az alkalmazás használatát a Power BI használatával. A csillag-sémát teszi közzé lekérdezések futtatása:

- Ki van vásárlás jegyek és mely helyszínére.
- Mintákat és trendeket a jegyektől értékesítése.
- Az egyes helyszínekkel relatív népszerűségét.

Ez az oktatóanyag példákat alapvető insights, amely a Wingtip jegyek adatokból adatokat is. Az ismertetése, hogyan használja az egyes helyszínekkel a szolgáltatást, előfordulhat, hogy a Wingtip jegyek szállító gondolniuk irányuló több vagy kevesebb aktív helyszínek, például különböző service-csomagokról. 

## <a name="setup"></a>Beállítás

### <a name="prerequisites"></a>Előfeltételek

> [!NOTE]
> Ez az oktatóanyag az Azure Data Factory, amelyek jelenleg egy korlátozott előzetes (társított szolgáltatás (egyszerű) paraméterezéssel) funkcióit használja. Ha ez az oktatóanyag elvégzéséhez, adja meg az előfizetés-Azonosítóval [Itt](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu). Kapni fog egy megerősítő üzenet, amint az előfizetés engedélyezve van.

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:
- A Wingtip jegyek SaaS adatbázis / bérlői alkalmazás központi telepítése. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip SaaS-alkalmazás](saas-dbpertenant-get-started-deploy.md).
- A Wingtip jegyek SaaS adatbázis / bérlői parancsfájlok és az alkalmazás [forráskód](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) letöltődnek a Githubról. Lásd: Töltse le az utasításokat. Ügyeljen arra, hogy *feloldása a zip-fájl* tartalmának kibontása előtt.
- A Power BI Desktop telepítve van. [A Power BI Desktop letöltése](https://powerbi.microsoft.com/downloads/).
- A köteg további bérlő van kiépítve, tekintse meg a [ **rendelkezés bérlők oktatóanyag**](saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>A bemutató-adatok létrehozása

Ez az oktatóanyag analytics felderíti a jegy értékesítési adatok. Ebben a lépésben a bérlőknek hozhat létre jegy adatokat. Ezek az adatok elemzéséhez egy későbbi lépésben ki kell olvasni. _Győződjön meg arról, a bérlő kötegelt létesített_ (leírtak korábban), hogy kevés az adat teszi közzé a különböző számos jegyet beszerzési kombinációját.

1. Nyissa meg a PowerShell ISE *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*, és a következő értéket:
    - **$DemoScenario** = **1** beszerzési jegyek összes helyszínek, események
2. Nyomja le az **F5** futtassa a parancsfájlt, és hozzon létre jegy megvásárlásáról a helyszínek előzményeit. A 20 bérlőkkel a parancsfájl hoz létre a jegyektől tízezreit, és 10 percig vagy tovább eltarthat.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>Központi telepítése az SQL Data Warehouse, adat-előállítót, és a Blob-tároló 
A Wingtip jegyek alkalmazásban a bérlők tranzakciós adatok sok adatbázis van elosztva. Az Azure Data Factory (ADF) segítségével levezényelni a kinyerési, betöltés és átalakítás (ELT) az adatok az adatraktárba. Adatok betöltése az SQL Data Warehouse leghatékonyabban, ADF adatok kibontása köztes blob-fájlok, és ezután [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) az adatok betöltése az adatraktárba.   

Ebben a lépésben az oktatóanyagban használt további erőforrások telepítése: egy SQL Data Warehouse nevű _tenantanalytics_, az Azure Data Factory nevű _dbtodwload -\<felhasználói\>_  , és az Azure-tárfiók hívása _wingtipstaging\<felhasználói\>_. A storage-fiók segítségével átmeneti tárolására kibontott adatfájlok blobként, mielőtt betöltve az adatraktárba. Ez a lépés is telepíti az adatraktár sémájának és az ADF folyamatok, amelyek levezényelni a ELT folyamat meghatározása.
1. Nyissa meg a PowerShell ISE *...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* és állítsa be:
    - **$DemoScenario** = **2** bérlői analytics data warehouse-ba, a blob storage és adat-előállító központi telepítése 
1. Nyomja le az **F5** a bemutató-parancsfájl futtatása, és az Azure-erőforrások telepítése. 

Most, tekintse át a telepített Azure-erőforrások:
#### <a name="tenant-databases-and-analytics-store"></a>Bérlői adatbázisok és az elemzések tároló
Használjon [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) való kapcsolódáshoz **tenants1-dpt -&lt;felhasználói&gt;**  és **katalógus-dpt -&lt;felhasználói&gt;**  kiszolgálók. Cserélje le &lt;felhasználói&gt; az alkalmazás telepítésekor használt értékkel. Használja a bejelentkezési = *fejlesztői* és a jelszó = *P@ssword1*. Tekintse meg a [bevezető oktatóanyag](saas-dbpertenant-wingtip-app-overview.md) további útmutatást.

![SQL Database-kiszolgálóhoz csatlakozni a szolgáltatáshoz az SSMS](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

Az Object Explorer:

1. Bontsa ki a *tenants1-dpt -&lt;felhasználói&gt;*  kiszolgáló.
1. Bontsa ki az adatbázisok csomópontot, és a bérlői adatbázisok listájának megtekintéséhez.
1. Bontsa ki a *katalógus-dpt -&lt;felhasználói&gt;*  kiszolgáló.
1. Győződjön meg arról, hogy megjelenik-e a analytics tárolja, amely tartalmazza a következő objektumok:
    1. Táblák **raw_Tickets**, **raw_Customers**, **raw_Events** és **raw_Venues** a tenant adatbázisból származó nyers kinyert adat tárolására.
    1. A csillag-séma táblák **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, és **dim_Dates** .
    1. A tárolt eljárás **sp_transformExtractedData** átalakíthatja az adatokat, és töltse be a csillag-séma táblákba szolgál.

![DWtables](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage
1. Az a [Azure Portal](https://ms.portal.azure.com), keresse meg az erőforráscsoport, amelyet az alkalmazás telepítéséhez használt. Győződjön meg arról, hogy a tárfiók neve **wingtipstaging\<felhasználói\>**  hozzá lett adva.

  ![DWtables](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. Kattintson a **wingtipstaging\<felhasználói\>**  található objektumok megismerkedhet a tárfiókot.
1. Kattintson a **Blobok** csempéje
1. Kattintson a tároló **configfile**
1. Ellenőrizze, hogy **configfile** nevezett JSON-fájlt tartalmaz **TableConfig.json**. Ez a fájl tartalmazza a forrás és cél táblanevek, az oszlopneveket és a követő oszlop neve.

#### <a name="azure-data-factory-adf"></a>Az Azure Data Factory (ADF)
Az a [Azure Portal](https://ms.portal.azure.com) az erőforráscsoportban, győződjön meg arról, hogy az Azure Data Factory neve _dbtodwload -\<felhasználói\>_  hozzá lett adva. 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

Ez a szakasz ismerteti az adat-előállító létrehozása. Hajtsa végre az adat-előállítóban indítsa el az alábbi lépéseket:
1. A portálon kattintson a data factory nevű **dbtodwload -\<felhasználói\>**.
2. Kattintson a **Szerző & figyelő** csempére indítsa el a Data Factory-Tervező egy külön lapján. 

## <a name="extract-load-and-transform-data"></a>Extract, Load, és az adatok átalakítása
Az Azure Data Factory koordinálása a kinyerés, betöltés és adatok átalakítása szolgál. Ebben az oktatóanyagban adatok kinyerése négy különböző SQL nézeteket a bérlő-adatbázisok mindegyike esetében: **rawTickets**, **rawCustomers**, **rawEvents**, és  **rawVenues**. Ezek a nézetek helyszínére azonosítója, tartalmazza, így a is különbséget tenni az adatraktár egyes helyszínekkel adatait. Az adatok betöltése az adatraktár megfelelő átmeneti tárolási táblákba: **raw_Tickets**, **raw_customers**, **raw_Events** és **raw_Venue**. A tárolt eljárás majd átalakítja a nyers adatok tölti fel a csillag-séma táblák: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** , és **dim_Dates**.

Az előző szakaszban telepíti, és a szükséges Azure-erőforrások, például a data factory inicializálva. Telepített adat-előállító tartalmazza, a folyamatok, a adatkészleteket, a társított szolgáltatások, a stb., bontsa ki, betölteni, és a bérlői adatok átalakítása szükséges. Megismeréséhez vizsgálja a további objektumokat, és a eseményindító a tenant adatbázisból származó adatok áthelyezése az adatraktár-feldolgozási folyamat.

### <a name="data-factory-pipeline-overview"></a>Az adatok gyári folyamat áttekintése
Ez a szakasz ismerteti az adat-előállító létrehozott objektumokat. Az alábbi ábra az ADF-feldolgozási folyamat ebben az oktatóanyagban használt a teljes munkafolyamatot ismerteti. Ha a folyamat későbbi vizsgálatát, és az eredmények megtekintéséhez először szeretne használni, ugorjon a következő szakaszban **indul el, futtassa a csővezeték**.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

Az Áttekintés lap váltani **Szerző** fülre, a bal oldali panelen, és figyelje meg, hogy nincsenek három [folyamatok](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) és három [adatkészletek](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) létrehozott.
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

A három beágyazott folyamatok vannak: SQLDBToDW DBCopy és TableCopy.

**Adatcsatorna 1 - SQLDBToDW** keres a katalógus adatbázisban tárolt bérlői adatbázisok neveit (táblanév: [__ShardManagement]. [ ShardsGlobal]) és az egyes bérlői adatbázisok végrehajtja a **DBCopy** folyamat. Létrehozása után a megadott **sp_TransformExtractedData** tárolt eljárás séma, végrehajtja a rendszer. Ez a tárolt eljárás átalakítja az átmeneti tárolási táblázatokban betöltött adatokról, és feltölti a csillag-séma táblák.

**Feldolgozási sor 2 - DBCopy** keresi a táblák és az oszlopok nevét a blob storage-ban tárolt konfigurációs fájlból.  A **TableCopy** folyamat futtatja az négy a táblákat: TicketFacts, CustomerFacts, EventFacts és VenueFacts. A **[Foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** tevékenység 20 adatbázisok párhuzamos végrehajtása. ADF lehetővé teszi, hogy legfeljebb 20 hurok a közelítés a párhuzamosan futtatható. Érdemes lehet további adatbázisok több folyamatok létrehozni.    

**Feldolgozási sor 3 - TableCopy** egy sor verziószámok által használt SQL-adatbázis (_rowversion_) megváltozott, vagy frissített sorok azonosításához. Ez a tevékenység a kezdő és záró sor verzióját sorok beolvasása a forrástáblákból a keres. A **CopyTracker** minden bérlő adatbázisban tárolt tábla minden egyes futtatása forrástábla kinyert utolsó sorának követi nyomon. Új vagy módosított sorokat a rendszer a megfelelő átmeneti tárolási táblák az adatraktár másolja: **raw_Tickets**, **raw_Customers**, **raw_Venues**, és **raw_ Események**. Végül az utolsó sort verzió menti a **CopyTracker** tábla a következő kiolvasásához kezdeti sor verzióval használható. 

A forrás-SQL-adatbázisok adat-előállító hivatkozást, a cél az SQL Data Warehouse és a köztes Blob-tároló, nincsenek is három paraméteres társított szolgáltatások. Az a **Szerző** lapra, majd a **kapcsolatok** alapján megismerheti a társított szolgáltatások, a következő ábrán látható módon:

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

A három társított szolgáltatások megfelelő, az adatokat, használja a feldolgozási sor tevékenység bemeneti vagy kimeneti hivatkozó három adatkészletek vannak. Fedezze fel az adatkészletek és figyelje meg a kapcsolatokat és a használt paraméterek mindegyikét. _AzureBlob_ a forrás és cél táblák és oszlopok, valamint az egyes követő oszlop tartalmazó konfigurációs fájlt mutat.
  
### <a name="data-warehouse-pattern-overview"></a>Az adatraktár mintát áttekintése
Az SQL Data Warehouse összesítési végre a bérlői adatforgalom az analytics tárolóként szolgál. Ez a példa a PolyBase segítségével adatok betöltése az SQL Data warehouse-bA. Nyers adatok azon sorait, amelyek a csillag-séma táblákba átalakítása nyomon követéséhez azonosító oszlopot átmeneti tárolási táblákba be van töltve. A következő kép bemutatja a betöltés mintát: ![loadingpattern](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

Ebben a példában lassan dimenzió módosításával (SCD) típusú 1 dimenziótáblák használhatók. Minden dimenzió egy helyettesítő kulcs van definiálva az azonosító oszlop használatával. Ajánlott eljárásként a dátum dimenzió tábla előre megadott időmegtakarítás. A másik dimenzió táblák a létrehozás, válassza ki táblázat... (CTAS) utasítást használja a meglévő módosított, és nem módosított sorok, a helyettesítő kulcsokkal együtt tartalmazó ideiglenes tábla létrehozásához. Ez a lépés IDENTITY_INSERT = ON. Új sorok majd szúrja be a táblába való IDENTITY_INSERT = off BEÁLLÍTÁST. Az egyszerű visszaállítási a meglévő dimenzió tábla neve, és az ideiglenes tábla neve legyen, az új dimenziótáblában. Minden egyes futtatása előtt a rendszer törli a régi dimenziótáblában.

A ténytábla előtt dimenziótáblák töltődnek be. Ez az alkalmazás-előkészítés biztosítja, hogy minden érkező tény minden hivatkozott dimenziók már létezik. A tények be vannak töltve, a rendszer megkeres minden megfelelő dimenzió üzleti kulcsa és a megfelelő helyettesítő kulcsok minden egyes tény hozzáadódnak.

Az utolsó lépést az átalakítás készen áll a feldolgozási folyamat következő végrehajtásának átmeneti adatokat törli.
   
### <a name="trigger-the-pipeline-run"></a>A Futtatás futószalag indítás
Kövesse az alábbi lépéseket a futtassa az összes kibontása, betöltés és átalakítás a következő feldolgozási sorban az összes bérlői adatbázisról:
1. Az a **Szerző** az ADF felhasználói felületén kattintson a lap **SQLDBToDW** folyamat a bal oldali ablaktáblán.
1. Kattintson a **eseményindító** pedig a lekért menüre kattintson **eseményindító most**. Ez a művelet azonnal futtatja a folyamatot. Éles forgatókönyv esetében a folyamat az adatfrissítési ütemezés szerint fut ütemtervét határozzák meg.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. A **folyamat futtatása** kattintson **Befejezés**.
 
### <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása
1. Váltás az ADF felhasználói felületén, a **figyelő** lapon a bal oldali menüből.
1. Kattintson a **frissítése** amíg SQLDBToDW folyamat állapota **sikeres**.
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. A data warehouse szolgáltatáshoz az SSMS csatlakozhat, és győződjön meg arról, hogy ezek a táblázatok adatok lett betöltve a csillag-séma táblázatokat lekérdezése.

A folyamat befejezése után a ténytábla összes helyszínek jegy értékesítési adatokat tároló és a dimenziótáblák a rendszer feltölti a megfelelő helyszínek, eseményeket és ügyfelek.

## <a name="data-exploration"></a>Az adatok feltárása

### <a name="visualize-tenant-data"></a>Bérlői adatok megjelenítése

A csillag-sémában található adatok összes a jegy értékesítési adatokat biztosít a elemzése szükséges. Megjeleníteni az adatokat grafikusan megkönnyíti nagy adatkészletek trendeket megjelenítéséhez. Ebben a szakaszban használhatja **Power BI** módosíthatók, és az adatraktár bérlői adatok megjelenítéséhez.

A Power bi-ba, és importálhatja a korábban létrehozott nézeteket, tegye a következőket:

1. Indítsa el a Power BI desktopban.
2. Válassza ki az otthoni szalagon **adatok beolvasása**, és válassza ki **több...** a menüből.
3. Az a **adatok beolvasása** ablakban válassza ki **Azure SQL Database**.
4. Az adatbázis-bejelentkezési ablakban adja meg a kiszolgálónév (**katalógus-dpt -&lt;felhasználói&gt;. database.windows.net**). Válassza ki **importálási** a **adatok csatlakozási mód**, és kattintson a **OK**. 

    ![bejelentkezési-az-a-power-bi-ban](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Válassza ki **adatbázis** a bal oldali ablaktáblán, majd adjon meg felhasználónevet = *fejlesztői*, és írja be a jelszó = *P@ssword1*. Kattintson a **Connect** (Csatlakozás) gombra.  

    ![adatbázis-bejelentkezés](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. Az a **Navigator** analytics adatbázis ablaktábla, válassza ki a csillag-séma táblákat: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** és **dim_Dates**. Válassza ki **terhelés**. 

Gratulálunk! Sikeresen betöltötte az adatokat a Power bi-bA. Most megismerkedhet az érdekes képi megjelenítéseket betekintést nyerhet a bérlők számára. Bemutatjuk, hogyan analytics biztosíthat a Wingtip jegyek üzleti csoport adatvezérelt ajánlásokat tartalmaz keresztül. A javaslatok segítségével az üzleti modell és a felhasználói élmény optimalizálása érdekében.

Első lépésként jegy értékesítési adatok megtekintéséhez használatának változása a helyszínek között elemzése. Válassza ki a Power BI-ban a sávdiagram az egyes helyszínekkel által jegyek teljes száma ábrázolásához látható lehetőségeket. (A jegy generátor véletlenszerű variációjának, mert az eredmények lehetnek.)
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

A fenti ábra megerősíti, hogy a jegyektől által az egyes helyszínekkel száma függ. A jegyektől több értékesítő helyszínek, amely kevesebb a jegyektől eladásra helyszínek-nál több fokozottan használ a szolgáltatás. Előfordulhat, hogy az erőforrás-elosztás különböző bérlői igények szerint testre szabni itt lehetőséget.

További elemezheti pontozásával megállapíthatjuk, hogyan jegy értékesítési időbeli eltérők lehetnek. Válassza a minden nap 60 napon eladott jegyek száma ábrázolásához Power BI-ban az alábbi ábrán látható.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

A fenti diagram bemutatja, hogy az egyes helyszínek jegy értékesítési csúcs. Ezek a teljesítményt a képet, hogy egyes helyszínek előfordulhat, hogy lehet fel rendszererőforrásokat aránytalanul megerősítése. Eddig nincs nincs nyilvánvaló minta esetén fordulhat elő, a teljesítményt.

Következő most vizsgálja meg a maximális pénztári napjainkban jelentőségét. Ha tegye ezeket csúcsait után kerül sor jegyek keresse fel a pénztári? A jegyektől naponta értékesített megrajzolásához, válassza a Power BI-ban az alábbi ábrán látható.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

A rajzolási jeleníti meg, hogy egyes helyszínek pénztári első napján nagyszámú jegyek el. Amint jegyek keresse fel a pénztári, ezek helyszínek, úgy tűnik, hogy egy mad sürgős. A tevékenység által néhány helyszínek kapacitásnövelés hatással lehet a szolgáltatás más bérlők számára.

Tovább részletezhető az adatok ismételt használatával ellenőrizheti a mad sürgős teljesül-e az összes esemény ezek helyszínek üzemelteti. Az előző előkészítésére, látta, hogy a Contoso energiaoptimalizálást egyszerre Hall sok jegyek értékesít, és hogy a Contoso is van egy csúcs jegy Sales egyes napokon. A Power BI beállításokkal a Contoso energiaoptimalizálást egyszerre Hall, értékesítési trendeket a eseményeinek minden egyes előtérbe összegző jegy értékesítési megrajzolásához lejátszása. Hajtsa végre az összes esemény ugyanezt a pénztári mintát követik? Próbálja például alább rajzot létrehozásához.

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Ez az összegző jegy értékesítési minden esemény Contoso energiaoptimalizálást egyszerre Hall időbeli rajzot jeleníti meg, hogy a mad sürgős történik meg az összes esemény. A szűrő beállításokkal pénztári trendekről a licencekkel kapcsolatos egyéb helyszínek felfedezése lejátszása.

Minták értékesítési jegy betekintést vezethet Wingtip jegyek üzleti modelljüknek optimalizálása érdekében. Helyett egyaránt díjszabási egyetlen bérlő számára, lehet, hogy a Wingtip kell vezetnie más-más teljesítménybeli szintű szolgáltatási szinteket. Nagyobb helyszínek, amely naponta több jegyek el kell egy magasabb szolgáltatásiszint-szerződéssel (SLA) és magasabb szintű használható sikerült választhatják. E helyszínek rendelkezhetnek a hozzájuk tartozó adatbázisok címkészlet, amely nagyobb adatbázis-erőforrás korlátok helyezve. Egyes szolgáltatásszinteken sikerült óránkénti értékesítési kiosztását, rendelkező haladja meg a lemezfoglalási kiszabott további díjakat. Nagyobb helyszínek, amelyek az értékesítési rendszeres felszakadásáig előnyös az magasabb szinteket, és Wingtip jegyek is hatékonyabbá pénzt a szolgáltatás.

Egyes Wingtip jegyek ügyfelek panaszkodnak mert ugyanakkor, hogy kihívást jelent eladásra elég a jegyektől szolgáltatás költségeinek igazolására. Lehet, hogy az ezen insights van a helyszínek underperforming jegy értékesítés növelése lehetőséget. Magasabb értékesítési volna növelje észlelt a szolgáltatást. Fact_Tickets kattintson jobb gombbal, majd válassza ki **új mérték**. Adja meg az alábbi kifejezés a következő új néven mértékhez **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Válassza ki a következő képi megjelenítések beállításai az egyes helyszínekkel azok relatív sikerességének meghatározásához által százalékos jegyek megrajzolásához.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

A fenti ábra bemutatja, hogy annak ellenére, hogy a legtöbb helyszínek több mint 80 %-a jegyek eladásra, néhány vannak tud lépést töltse ki a több mint fele a munkaállomásokat. Az értékek is jelölje be a jegyektől egyes helyszínekkel eladott maximális és minimális aránya az lejátszása.

## <a name="embedding-analytics-in-your-apps"></a>Az alkalmazások analytics beágyazása 
Ez az oktatóanyag fordította a több-bérlős analytics segítségével a bérlők a szoftver gyártója által biztosított megértése. Elemzés is összefüggések, megadhatja a _bérlők_, üzleti hatékonyabban kezelését megkönnyítő magukat. 

A Wingtip jegyek példában meg korábban felderített, hogy a jegy értékesítési általában a kiszámítható minták kövesse. Ez insight helyszínek program jegy értékesítési underperforming segítségével használhatók. Lehet, hogy nincs tervezni a machine learning technikák előre jelezni jegy értékesítési események lehetőséget. A hatásairól sikerült is modellezni, engedmények meghatározható az ajánlat hatását engedélyezéséhez. A Power BI Embedded sikerült integrálni kell egy esemény felügyeleti alkalmazás számsort, beleértve engedmények értékesített teljes munkaállomásokat és események alacsony értékesítési bevétel hatásának megjelenítéséhez. Power BI Embedded még akkor is is integrálhatja a kedvezményeket ténylegesen alkalmazása a jegy árak, jobb gombbal a képi megjelenítés élményt nyújt.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Telepítse a bérlői analytics csillagséma töltődik SQL Data Warehouse.
> * Azure Data Factory használatával kinyeri az adatokat az egyes bérlői adatbázisok az analytics adatraktárba.
> * A kibontott adatok (csillag sémába átszervez) optimalizálásához.
> * Az elemzés adatraktár lekérdezésére. 
> * A bérlők között adatok a trendek megjelenítése a Power BI használatával.

Gratulálunk!

## <a name="additional-resources"></a>További források

- További [oktatóprogramot kínál, amelyek a Wingtip SaaS-alkalmazás épül](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
