---
title: Elemzési lekérdezések futtatása bérlői adatbázisokon
description: A bérlők közötti elemzési lekérdezések az Azure SQL Database, az SQL Data Warehouse, az Azure Data Factory vagy a Power BI által kinyert adatok használatával.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: MightyPen, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 4791cd3a6b6f72c5d9ee4ca828d66b0d361f356c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73816767"
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>SaaS-elemzés felfedezése az Azure SQL Database, az SQL Data Warehouse, a Data Factory és a Power BI segítségével

Ebben az oktatóanyagban végigvezeti a végpontok között elemzési forgatókönyv. A forgatókönyv bemutatja, hogy a bérlői adatokon keresztüli elemzés hogyan képessé teheti a szoftvergyártókat az intelligens döntések meghozatalára. Az egyes bérlői adatbázisokból kinyert adatok használatával elemzéssel betekintést nyerhet a bérlői viselkedésbe, beleértve a wingtip jegyek SaaS-alkalmazás minta használatát. Ez a forgatókönyv három lépésből áll: 

1.  **Adatok kinyerése** minden bérlői adatbázisból egy elemzési tárolóba, ebben az esetben egy SQL Data Warehouse.
2.  **Optimalizálja a kinyert adatokat** elemzési feldolgozásra.
3.  **Az Üzletiintelligencia-eszközök** segítségével hasznos elemzéseket készíthet, amelyek irányíthatják a döntéshozatalt. 

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> - Hozza létre a bérlői elemzési tárolóbe töltéshez.
> - Az Azure Data Factory (ADF) használatával adatokat nyerhet ki az egyes bérlői adatbázisokból az elemzési adattárházba.
> - Optimalizálja a kinyert adatokat (átszervezi egy csillag-séma).
> - Az elemzési adattárház lekérdezése.
> - A Power BI adatmegjelenítéshez használja a bérlői adatok trendjeinek kiemelését és a fejlesztésekre vonatkozó javaslatok at.

![architectureOverView](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Akinyert bérlői adatok elemzése

Az SaaS-alkalmazások potenciálisan hatalmas mennyiségű bérlői adatot tárolnak a felhőben. Ezek az adatok az alkalmazás működésével és használatával, valamint a bérlők viselkedésével kapcsolatos információk gazdag forrását biztosíthatják. Ezek az elemzések irányíthatják a funkciók fejlesztését, a használhatóság javítását és az alkalmazásokba és a platformba történő egyéb beruházásokat.

Az adatok elérése az összes bérlő egyszerű, ha az összes adat csak egy több-bérlős adatbázisban található. A hozzáférés azonban összetettebb, ha több ezer adatbázis között osztja el a nagy méreteket. Az összetettség megszelídítésének egyik módja az adatok kinyerése egy elemzési adatbázisba vagy egy adattárházba lekérdezéshez.

Ez az oktatóanyag egy végpontok között lévő elemzési forgatókönyvet mutat be a Wingtip Tickets alkalmazáshoz. Először is, [az Azure Data Factory (ADF)](../data-factory/introduction.md) vezénylési eszközként használják a jegyek értékesítésének és a kapcsolódó adatok kinyerése az egyes bérlői adatbázisokból. Ezek az adatok egy elemzési tároló átmeneti tábláiba töltődnek be. Az elemzési tároló lehet SQL-adatbázis vagy SQL Data Warehouse. Ez az oktatóanyag [az SQL Data Warehouse-t](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) használja elemzési tárolóként.

Ezután a kinyert adatok átalakulnak, és [csillagsématáblákba](https://www.wikipedia.org/wiki/Star_schema) töltődnek be. A táblák egy központi ténytáblából és a kapcsolódó dimenziótáblákból állnak:

- A csillagséma központi ténytáblája jegyadatokat tartalmaz.
- A dimenziótáblák a helyszínekre, eseményekre, vevőkre és vásárlási dátumokra vonatkozó adatokat tartalmaznak.

A központi és a dimenziótáblák együttesen hatékony analitikai feldolgozást tesznek lehetővé. Az oktatóanyagban használt csillagséma az alábbi képen látható:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/starschematables.JPG)

Végül a rendszer lekérdezi a csillagséma-táblákat. A lekérdezési eredmények vizuálisan jelennek meg a Power BI segítségével, hogy kiemeljék a bérlői viselkedéssel és az alkalmazás használatával kapcsolatos elemzéseket. Ezzel a csillag-sémával olyan lekérdezéseket futtat, amelyek a következőket teszik ki:

- Ki vásárol jegyeket és melyik helyszínről.
- Minták és trendek a jegyek értékesítésében.
- A relatív népszerűsége minden helyszínen.

Ez az oktatóanyag alapvető példákat tartalmaz a Wingtip jegyek adataiból kinyerhető elemzési adatokra. Annak megértése, hogy az egyes helyszínek hogyan használja a szolgáltatást, előfordulhat, hogy a Wingtip Jegyek forgalmazója különböző szolgáltatási csomagokat gondol át, amelyek többé-kevésbé aktív helyszínekre irányulnak, például. 

## <a name="setup"></a>Telepítés

### <a name="prerequisites"></a>Előfeltételek

> [!NOTE]
> Ez az oktatóanyag az Azure Data Factory olyan funkcióit használja, amelyek jelenleg korlátozott előzetes verzióban vannak (összekapcsolt szolgáltatás paraméterezése). Ha szeretné ezt a bemutatót, adja meg az előfizetésazonosítóját [itt](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu). Amint az előfizetése engedélyezve lett, visszaigazolást küldünk Önnek.

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:
- A Wingtip jegyek SaaS-adatbázis bérlőnként alkalmazás onként van telepítve. Ha kevesebb, mint öt perc alatt üzembe szeretne helyezni, [olvassa el a Wingtip SaaS-alkalmazás telepítése és feltárása című témakört.](saas-dbpertenant-get-started-deploy.md)
- A Wingtip jegyek SaaS-adatbázis bérlőnként parancsfájlok és az alkalmazás [forráskód](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) a GitHubról töltődnek le. Lásd a letöltési utasításokat. Ügyeljen arra, hogy *feloldja a zip fájl blokkolását,* mielőtt kibontana annak tartalmát.
- A Power BI Desktop telepítve van. [A Power BI Desktop letöltése](https://powerbi.microsoft.com/downloads/).
- A további bérlők kötege ki van építve, lásd: A [**bérlők kiépítése oktatóanyag.**](saas-dbpertenant-provision-and-catalog.md)

### <a name="create-data-for-the-demo"></a>Adatok létrehozása a bemutatóhoz

Ez az oktatóanyag a jegyértékesítési adatok elemzését vizsgálja. Ebben a lépésben az összes bérlő jegyadatait hozza létre. Egy későbbi lépésben ezeket az adatokat elemzésre bontja ki. _Győződjön meg arról, hogy kiépített a bérlők kötegét_ (a korábbiaknak megfelelően), hogy elegendő adatkal rendelkezzen a különböző jegyvásárlási minták széles választékának elérhetővé tételéhez.

1. A PowerShell ISE-ben nyissa meg *a ...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1 webhelyet,* és állítsa be a következő értéket:
    - **$DemoScenario** = **1** Jegyek minden helyszínen történő megvásárlása
2. Nyomja meg **az F5** billentyűt a parancsfájl futtatásához, és hozzon létre jegyvásárlási előzményeket az összes helyszínhez. 20 bérlővel a parancsfájl több tízezer jegyet hoz létre, és 10 percet vagy többet is igénybe vehet.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>SQL Data Warehouse, Data Factory és Blob Storage telepítése 
A Wingtip Jegyek alkalmazásban a bérlők tranzakciós adatai számos adatbázison keresztül oszlanak meg. Az Azure Data Factory (ADF) ezen adatok kinyerésének, betöltésének és átalakításának (ELT) az adattárházba történő vezénylésére szolgál. Az ADATOK SQL Data Warehouse-ba való leghatékonyabb betöltéséhez az ADF kinyeri az adatokat köztes blobfájlokba, majd a [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) segítségével betölti az adatokat az adattárházba.   

Ebben a lépésben üzembe helyezi az oktatóanyagban használt további erőforrásokat: egy SQL Data Warehouse nevű _tenantanalytics_, egy Azure Data Factory nevű _dbtodwload-felhasználó,\<\>_ és egy Azure storage-fiók nevű _wingtipstaging\<felhasználó.\>_ A tárfiók ideiglenesen tárolják a kibontott adatfájlokblobok, mielőtt azok betöltődnek az adattárházba. Ez a lépés az adattárház sémáját is telepíti, és meghatározza az ELT-folyamatot vezénylő ADF-folyamatokat.
1. A PowerShell ISE-ben nyissa meg *a ...\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1 webhelyet,* és állítsa be a következőket:
    - **$DemoScenario** = **2** Bérlői elemzési adattárház, blobstorage és adat-előállító üzembe helyezése 
1. Nyomja le **az F5** billentyűt a bemutató parancsfájl futtatásához és az Azure-erőforrások központi telepítéséhez. 

Most tekintse át a telepített Azure-erőforrásokat:
#### <a name="tenant-databases-and-analytics-store"></a>Bérlői adatbázisok és elemzési tároló
Az [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) segítségével csatlakozzon a **bérlők1-dpt-&lt;felhasználó&gt; ** és **katalógus-dpt-&lt;felhasználói&gt; ** kiszolgálókhoz. Cserélje &lt;&gt; le a felhasználót az alkalmazás telepítésekor használt értékre. Használja Login = *fejlesztő* és jelszó = *P\@ssword1*. További útmutatásért tekintse meg a [bevezető oktatóanyagot.](saas-dbpertenant-wingtip-app-overview.md)

![Csatlakozás az SQL Database-kiszolgálóhoz az SSMS-ből](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

Az Objektumkezelőben:

1. Bontsa ki a *bérlők1-dpt-&lt;felhasználói&gt; * kiszolgáló.
1. Bontsa ki az Adatbázisok csomópontot, és tekintse meg a bérlői adatbázisok listáját.
1. Bontsa ki a *&lt;katalógus-dpt- felhasználói&gt; * kiszolgálót.
1. Ellenőrizze, hogy a következő objektumokat tartalmazó elemzési tároló jelenik-e meg:
    1. A **raw_Tickets**, **raw_Customers**, **raw_Events** és **raw_Venues** a bérlői adatbázisokból kinyert nyers kinyert adatokat tárolni.
    1. A csillagsématáblák **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**és **dim_Dates**.
    1. A tárolt eljárás, **sp_transformExtractedData** az adatok átalakítására és a csillagsématáblákba való betöltésére szolgál.

![DWtables között](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage
1. Az [Azure Portalon](https://ms.portal.azure.com)keresse meg az alkalmazás üzembe helyezéséhez használt erőforráscsoportot. Ellenőrizze, hogy egy **\<wingtipstaging\> nevű felhasználó** nevű tárfiók lett-e hozzáadva.

   ![DWtables között](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. Kattintson **a\<\> wingtipstaging felhasználói** tárfiókra a jelen lévő objektumok felfedezéséhez.
1. Kattintson **a Blobok csempére**
1. Kattintson a tároló **konfigurációs fájljára**
1. Ellenőrizze, hogy **a konfigurációs fájl** tartalmaz-e **TableConfig.json**nevű JSON-fájlt. Ez a fájl tartalmazza a forrás- és céltábla neveket, az oszlopneveket és a követő oszlopnevét.

#### <a name="azure-data-factory-adf"></a>Azure Data Factory (ADF)
Az [Erőforráscsoport Azure Portalján](https://ms.portal.azure.com) ellenőrizze, hogy egy _dbtodwload\<\> nevű_ Azure Data Factory-felhasználó lett-e hozzáadva. 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

Ez a szakasz a létrehozott adat-előállítót vizsgálja. Az adatgyár elindításához kövesse az alábbi lépéseket:
1. A portálon kattintson a **dbtodwload\<\>nevű adatgyárra.**
2. Kattintson **a & figyelő** csempe szerkesztése parancsra a Data Factory tervező külön lapon való elindításához. 

## <a name="extract-load-and-transform-data"></a>Adatok kinyerése, betöltése és átalakítása
Az Azure Data Factory az adatok kinyerésének, betöltésének és átalakításának vezénylésére szolgál. Ebben az oktatóanyagban négy különböző SQL-nézetből nyerhet ki adatokat az egyes bérlői adatbázisokból: **rawTickets**, **rawCustomers**, **rawEvents**és **rawVenues**. Ezek a nézetek tartalmazzák a helyszínazonosítót, így az adatraktár minden helyszínéről különbséget tehet. Az adatok betöltődnek az adatraktár megfelelő átmeneti tábláiba: **raw_Tickets**, **raw_customers**, **raw_Events** és **raw_Venue**. A tárolt eljárás ezután átalakítja a nyers adatokat, és feltölti a csillagsématáblákat: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**és **dim_Dates**.

Az előző szakaszban üzembe helyezte és inicializálta a szükséges Azure-erőforrásokat, beleértve az adat-előállítót is. Az üzembe helyezett adatgyár tartalmazza a folyamatok, adatkészletek, csatolt szolgáltatások, stb, a bérlői adatok kinyeréséhez, betöltéséhez és átalakításához szükséges. Vizsgáljuk meg ezeket az objektumokat tovább, majd indítsa el a folyamatot az adatok áthelyezése a bérlői adatbázisokból az adattárházba.

### <a name="data-factory-pipeline-overview"></a>Adatfeldolgozó folyamatok – áttekintés
Ez a szakasz az adat-előállítóban létrehozott objektumokat vizsgálja. Az alábbi ábra az oktatóanyagban használt ADF-folyamat általános munkafolyamatát ismerteti. Ha később szeretné felfedezni a folyamatot, és először az eredményeket szeretné látni, ugorjon a következő szakaszra **A folyamat futtatása eseményindítója**.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

Az áttekintő lapon váltson a bal oldali **panelEn** a Szerző lapra, és vegye figyelembe, hogy három [folyamat](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) és három [adatkészlet](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services) jön létre.
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

A három beágyazott csővezeték a következő: SQLDBToDW, DBCopy és TableCopy.

**1. folyamat – Az SQLDBToDW** megkeresi a katalógus-adatbázisban tárolt bérlői adatbázisok nevét (táblaneve: [__ShardManagement].[ ShardsGlobal]) és minden egyes bérlői adatbázis esetében végrehajtja a **DBCopy-folyamatot.** Befejezését követően a megadott **sp_TransformExtractedData** tárolt eljárásséma kerül végrehajtásra. Ez a tárolt eljárás átalakítja az átmeneti táblákban betöltött adatokat, és feltölti a csillagséma-táblákat.

**2. folyamat – A DBCopy** megkeresi a forrástáblák és oszlopok nevét a blobstorage-ban tárolt konfigurációs fájlból.  A **TableCopy** folyamat ezután fut mind a négy tábla: TicketFacts, CustomerFacts, EventFacts, és VenueFacts. A **[Foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** tevékenység végrehajtása párhuzamosan mind a 20 adatbázis. Az ADF legfeljebb 20 ciklusismétlés párhuzamos futtatását teszi lehetővé. Fontolja meg több folyamat létrehozását több adatbázishoz.    

**3. folyamat – A TableCopy** sorverziószámokat használ az SQL Database _(rowversion)_ rendszerben a módosított vagy frissített sorok azonosítására. Ez a tevékenység megkeresi a kezdő és a zárósor verzióját a sorok forrástáblákból való kibontásához. Az egyes bérlői adatbázisokban tárolt **CopyTracker** tábla nyomon követi az egyes forrástáblákból kinyert utolsó sort az egyes futtatások során. Az új vagy módosított sorokat a program az adatraktár megfelelő átmeneti tábláiba másolja: **raw_Tickets**, **raw_Customers**, **raw_Venues**és **raw_Events**. Végül az utolsó sor verziója a **CopyTracker** táblába kerül, amelyet a következő kinyerés kezdeti sorverziójaként kell használni. 

Három paraméterezett csatolt szolgáltatás is létezik, amelyek az adat-előállítót a forrás SQL-adatbázisokhoz, a cél SQL Data Warehouse-hoz és a köztes Blob-tárhoz kapcsolják. A **Szerző** lapon kattintson a **Kapcsolatok** elemre a csatolt szolgáltatások felfedezéséhez, ahogy az az alábbi képen látható:

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

A három összekapcsolt szolgáltatásnak megfelelően három adatkészlet hivatkozik a folyamattevékenységekben bemenetként vagy kimenetként használt adatokra. Fedezze fel az egyes adatkészleteket a kapcsolatok és a használt paraméterek megfigyeléséhez. _Az AzureBlob_ a forrás- és céltáblákat és -oszlopokat tartalmazó konfigurációs fájlra, valamint az egyes források követőoszlopára mutat.
  
### <a name="data-warehouse-pattern-overview"></a>Adatraktár minta – áttekintés
Az SQL Data Warehouse elemzési tárolóként szolgál a bérlői adatok összesítéséhez. Ebben a példában a PolyBase adatok betöltésére szolgál az SQL Data-raktárba. A nyers adatok olyan átmeneti táblákba töltődnek be, amelyek identitásoszloppal rendelkeznek a csillagsématáblákká átalakított sorok nyomon követéséhez. Az alábbi képen a ![betöltési minta látható: loadingpattern](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

Ebben a példában az 1. Minden dimenzió rendelkezik egy identitásoszlop használatával definiált helyettesítő kulccsal. Ajánlott eljárásként a dátumdimenzió-tábla előre ki van töltve, hogy időt takarítson meg. A többi dimenziótábla esetében a CREATE TABLE AS SELECT... (CTAS) utasítás a meglévő módosított és nem módosított sorokat, valamint a helyettesítő kulcsokat tartalmazó ideiglenes tábla létrehozására szolgál. Ez a IDENTITY_INSERT=ON. Ezután a IDENTITY_INSERT=OFF mezőben új sorokat szúr be a táblázatba. A visszaállítás egyszerű módjaként a meglévő dimenziótáblát átnevezi, az ideiglenes táblát pedig átnevezi, hogy az új dimenziótáblává váljon. Minden futtatás előtt a régi dimenziótábla törlődik.

A dimenziótáblák a ténytábla elé kerülnek. Ez a szekvenálás biztosítja, hogy minden érkező tény esetében már létezik minden hivatkozott dimenzió. A tények betöltése, az üzleti kulcs minden megfelelő dimenzió egyeztetése, és a megfelelő helyettesítő kulcsok at minden tény hozzá.

Az átalakítás utolsó lépése törli a folyamat következő végrehajtására kész átmeneti adatokat.
   
### <a name="trigger-the-pipeline-run"></a>A folyamat futtatásának aktiválása
Az alábbi lépéseket követve futtassa a teljes kibontást, a terhelést és az átalakításfolyamatot az összes bérlői adatbázishoz:
1. Az ADF felhasználói **felületének Szerző** lapján válassza az **SQLDBToDW-folyamat** lehetőséget a bal oldali ablaktáblából.
1. Kattintson az **Eseményindító gombra,** majd a lehúzott menüben kattintson az **Eseményindító gombra.** Ez a művelet azonnal futtatja a folyamatot. Éles környezetben meg kell határoznia egy ütemtervet a folyamat futtatásához az adatok ütemezés szerinti frissítéséhez.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. A **Folyamat futtatása** lapon kattintson a **Befejezés gombra.**
 
### <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása
1. Az ADF felhasználói felületén váltson a bal oldali menü **Monitor** fülére.
1. Kattintson a **Frissítés** gombra, amíg az SQLDBToDW-folyamat állapota **sikeres nem lesz.**
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. Csatlakozzon az adattárházhoz az SSMS segítségével, és kérdezze le a csillagsématáblákat, és ellenőrizze, hogy az adatok be voltak-e töltve ezekben a táblákban.

Miután a folyamat befejeződött, a ténytábla tartalmazza az összes helyszín jegyértékesítési adatait, és a dimenziótáblák a megfelelő helyszínekkel, eseményekkel és ügyfelekkel vannak feltöltve.

## <a name="data-exploration"></a>Adatok feltárása

### <a name="visualize-tenant-data"></a>Bérlői adatok megjelenítése

A csillagsémában szereplő adatok biztosítják az elemzéshez szükséges összes jegyértékesítési adatot. Az adatok grafikus megjelenítése megkönnyíti a nagy adatkészletek trendjeinek megjelenítését. Ebben a szakaszban a **Power BI** segítségével kezelheti és jelenítheti meg a bérlői adatokat az adatraktárban.

Az alábbi lépésekkel csatlakozhat a Power BI-hoz, és importálhatja a korábban létrehozott nézeteket:

1. Indítsa el a Power BI desktopot.
2. A Kezdőlap menüszalagon válassza az **Adatok bekerülése**lehetőséget, és válassza az **Egyebek lehetőséget...** a menüből.
3. Az **Adatok bekerülése** ablakban válassza az **Azure SQL Database lehetőséget.**
4. Az adatbázis bejelentkezési ablakában adja meg a kiszolgáló nevét (**katalógus-dpt-&lt;&gt;User .database.windows.net**). Válassza az **Adatkapcsolat importálása** **módot,** majd kattintson **az OK**gombra. 

    ![bejelentkezés a power-bi-be](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Válassza **az Adatbázis** elemet a bal oldali ablaktáblában, majd írja be a felhasználónév = *fejlesztő*, és írja be a jelszót = *P\@ssword1*. Kattintson a **Csatlakozás** gombra.  

    ![adatbázisba való bejelentkezés](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. A **Navigátor** ablaktáblában az elemzési adatbázis alatt jelölje ki a csillagsématáblákat: **fact_Tickets**, **dim_Events,** **dim_Venues**, **dim_Customers** és **dim_Dates**. Ezután válassza **a Betöltés**lehetőséget. 

Gratulálunk! Sikeresen betöltötte az adatokat a Power BI-ba. Most fedezze fel az érdekes vizualizációkat, hogy betekintést nyerjen a bérlőkbe. Nézzük végig, hogyan elemzés nyújthat néhány adatalapú javaslatokat a Wingtip Jegyek üzleti csapat. A javaslatok segíthetnek az üzleti modell és az ügyfélélmény optimalizálásában.

Kezdje a jegyértékesítési adatok elemzésével, hogy láthassa a felhasználás változásait a helyszínek között. A Power BI-ban látható beállításokkal ábrázolja az egyes helyszínek által értékesített jegyek teljes számát. (A jegygenerátor véletlenszerű eltérése immár eltérő lehet.)
 
![Összes TicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

Az előző minta terület megerősíti, hogy az egyes helyszínek által értékesített jegyek száma változó. A több jegyet értékesítő helyszínek nagyobb mértékben használják a szolgáltatást, mint a kevesebb jegyet értékesítő helyszínek. Itt lehetőség lehet az erőforrás-elosztás taszthatására a különböző bérlői igények nek megfelelően.

Tovább elemezheti az adatokat, hogy lássa, hogyan változnak a jegyeladások az idő múlásával. A Power BI következő képen látható beállításokkal ábrázolva 60 napig minden nap eladott jegyek számát.
 
![Eladó versusdátum](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

Az előző diagram azt mutatja, hogy a jegyeladások megugrása egyes helyszíneken. Ezek a tüskék megerősítik azt az elképzelést, hogy egyes helyszínek aránytalanul nagy mértékben fogyasztják a rendszer erőforrásait. Eddig nincs nyilvánvaló minta, amikor a tüskék fordulnak elő.

Ezután vizsgáljuk meg ezeknek a csúcsértékesítési napoknak a jelentőségét. Mikor történnek ezek a csúcsok a jegyek eladása után? A naponta eladott jegyek nyomtatásához válassza ki a Power BI alábbi képen látható beállításokat.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

Ez a telek azt mutatja, hogy egyes helyszínek eladni nagy számú jegyet az első napon az értékesítés. Amint a jegyek etetik ezeken a helyszíneken, úgy tűnik, hogy őrült rohanás van. Ez a néhány helyszín által leadott tevékenység hatással lehet a szolgáltatás más bérlők számára.

Az adatok at újra beáshatja, hogy lássa, ez az őrült rohanás igaz-e az ezek a helyszínek által üzemeltetett összes eseményre. A korábbi telkek, látta, hogy a Contoso Concert Hall eladja sok jegyet, és hogy a Contoso is van egy tüske a jegyeladások bizonyos napokon. A Power BI lehetőségeivel a Contoso Concert Hall összesített jegyeladásait tervezhet, és az egyes események értékesítési trendjeire összpontosítva. Minden esemény ugyanazt az értékesítési mintát követi? Próbálja meg, hogy készítsen egy telek, mint az alábbi.

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Ez a telek kumulatív jegyeladások idővel contoso koncertterem minden esemény azt mutatja, hogy az őrült rohanás nem történik meg minden esemény. Játssz a szűrőopciókkal, hogy felfedezd a más helyszínek értékesítési trendjeit.

A jegyértékesítési mintákba való betekintést vezethet Wingtip jegyek optimalizálni az üzleti modell. Ahelyett, hogy az összes bérlő egyenlő töltése, talán Wingtip kell bevezetnie a különböző számítási méretű szolgáltatási szintek. A nagyobb helyszínek, amelyeknek naponta több jegyet kell eladniuk, magasabb szintű, magasabb szolgáltatásiszint-szerződéssel (SLA) rendelkező magasabb szintű szintet kínálhatnak. Ezek a helyszínek lehet, hogy az adatbázisok at a készlet magasabb adatbázisonkénti erőforráskorlátok. Minden szolgáltatási szint rendelkezhet egy óránkénti értékesítési allokációval, amely további díjakat számíthat fel a felosztás túllépése miatt. A nagyobb helyszínek, amelyek időszakos értékesítési sorozatokkal rendelkeznek, a magasabb szintek előnyeit élveznék, és a Wingtip jegyek hatékonyabban bevételt hozhatnak a szolgáltatásból.

Eközben néhány Wingtip Jegyek ügyfelek panaszkodnak, hogy küzdenek, hogy eladja elég jegyet, hogy igazolja a szolgáltatás költségeit. Talán ezekben a felismerésekben lehetőség van arra, hogy növeljék a jegyeladásokat az alulteljesítő helyszíneken. A magasabb eladások növelnék a szolgáltatás vélt értékét. Kattintson a jobb gombbal fact_Tickets, és válassza az **Új mérték parancsot.** Adja meg a következő kifejezést az **AverageTicketsSold**nevű új mértékhez:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

A következő vizualizációs beállítások kiválasztásával meg szeretné határozni az egyes helyszínek által értékesített jegyek százalékos arányát a relatív sikerük meghatározásához.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

A fenti telek azt mutatja, hogy bár a legtöbb helyszínen eladni több mint 80%-a jegyeket, néhány küzd, hogy töltse ki több mint a fele helyet. Játssz a Values Well játékkal, hogy kiválasztd az egyes helyszínekre eladott jegyek maximális vagy minimális százalékát.

## <a name="embedding-analytics-in-your-apps"></a>Elemzésbeágy az alkalmazásokban 
Ez az oktatóanyag a bérlők közötti elemzésre összpontosított, amely javítja a szoftverszállító által a bérlők megértését. Az Analytics elemzési adatokat is biztosít a _bérlők számára,_ hogy segítsen nekik a vállalkozás hatékonyabb kezelésében. 

A Wingtip jegyek példában korábban felfedezte, hogy a jegyeladások általában kiszámítható mintákat követnek. Ez a betekintés felhasználható az alulteljesítő helyszínek nek a jegyeladások növelésében. Talán lehetőség van gépi tanulási technikák alkalmazására a jegyeladások előrejelzésére az eseményekhez. Az árváltozások hatásait modellezni is lehetne, hogy előre lehessen jelezni a kedvezmények felajánlásának hatását. A Power BI Embedded integrálható egy eseménykezelő alkalmazásba az előre jelzett értékesítések megjelenítésére, beleértve a kedvezmények hatását az összes eladott ülőhelyre és a bevételt az alacsony értékesítésű eseményekre. A Power BI Embedded segítségével akár a kedvezmény tényleges alkalmazását is integrálhatja a jegyárakba, közvetlenül a vizualizációs élményben.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Telepítsen egy SQL Data Warehouse-t, amely egy csillagsémával van feltöltve a bérlői elemzéshez.
> * Az Azure Data Factory használatával adatokat nyerhet ki az egyes bérlői adatbázisokból az elemzési adattárházba.
> * Optimalizálja a kinyert adatokat (átszervezi egy csillag-séma).
> * Az elemzési adattárház lekérdezése. 
> * A Power BI segítségével az összes bérlő adatainak trendjeit jelenítheti meg.

Gratulálunk!

## <a name="additional-resources"></a>További források

- További [oktatóanyagok, amelyek a Wingtip SaaS alkalmazásra épülnek.](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
