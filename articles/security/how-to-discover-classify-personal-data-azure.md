---
title: "Ismerje meg, azonosítása és besorolni a személyes adatok Microsoft Azure-ban |} Microsoft Docs"
description: "keresési, zárolásának, felderítéséhez és azonosító adatait az Azure-ban segítenek felel meg az általános adatok védelmi szabályozás (GDPR)"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 21308faf5d3efd3908eccab4f37acb0cfccdcb8e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="discover-identify-and-classify-personal-data-in-microsoft-azure"></a>Ismerje meg, azonosítása és besorolni a személyes adatok Microsoft Azure-ban

Ez a cikk felderítése, azonosítása és a több Azure eszközeivel és szolgáltatásaival, beleértve az Azure Data Catalog, Azure Active Directory, az SQL-adatbázis, a Power Query használatával a Hadoop-fürtök az Azure HDInsight, az Azure Information Protection, az Azure Search és az SQL-lekérdezések Azure Cosmos DB a személyes adatok osztályozására nyújt útmutatást. Ezekkel az eszközökkel és eljárások segítségével felel meg az általános adatok védelmi szabályozás (GDPR)

## <a name="scenario-problem-statement-and-goal"></a>Forgatókönyv, problémamegállapítás és célja

Az Egyesült államokbeli sport vállalat különböző személyes és egyéb adatokat gyűjt. Ez magában foglalja az ügyfelek és az alkalmazott adatokat. A vállalati tárolása a több adatbázist, és az Azure környezetben több különböző helyen tárolja. Értékesítési sport berendezések, mellett is futtatni, és kezelése a regisztrációs elite athletic események világszerte, beleértve az EU, és egyes esetekben a gyűjtött ügyféladatok orvosi információkat tartalmaz.

Mivel a vállalat üzemeltető sok nemzetközi bicycling bemutatók évente, és szerződéses alkalmazottjai rendelkezik-e a világ minden táján helyek, adatkészletek néhány igen tekintélyes. A vállalati ügyfelek és az alkalmazottak által használt alkalmazások fejlesztői beépített is rendelkezik.

A vállalat kíván a következő problémákat:

- Ügyfél és az alkalmazott személyes adatokat a más, megfelelő hozzáférést és a biztonság érdekében gyűjt a vállalati adatok besorolásának/különböztetni kell lennie.
- Az adatok rendszergazdának hozzá kell könnyen felfedezheti az Azure környezetbe különböző területei közötti felhasználói személyes adatok helyét.
- Ügyfél és az alkalmazott személyes megjelenő adatok megosztott dokumentumok és e-mail kommunikációt kell címkével, annak biztosítására, hogy folyamatosan biztonságos.
- A vállalat alkalmazásfejlesztők van szükség, a webes és mobilalkalmazások ügyfél és az alkalmazott személyes adatokat könnyen keresni.
- A fejlesztők is kell a dokumentum-adatbázis, a személyes adatok lekérdezésére.

### <a name="company-goals"></a>Vállalati célok

- Az Azure Data Catalog címkézett/feliratozva összes ügyfél és az alkalmazott személyes adatokat kell lennie, ezért egyszerűen található. Ideális esetben ügyfél és az alkalmazottak a személyes adatok is címkézett/feliratozva külön-külön.
- A felhasználói profilok felhasználói és az alkalmazottak és a munkahelyi adatokat az Azure Active Directoryban található személyes adatokat könnyen található kell lennie.
- Több SQL-adatbázisban található személyes adatokat könnyen lehet lekérdezni. 
- A vállalat nagy adatkészletek néhány Azure HDInsight segítségével kezelt és Hadoop tárolja. Akkor kell importálni az Excel, a személyes adatok kérhetők.
- Megosztott dokumentumok és e-mailek kommunikációs személyes adatok kell besorolni, címkével és az Azure Information Protection biztonságba.
- A vállalat alkalmazásfejlesztők számára az ügyfél és az alkalmazott személyes adatok azok létrehozott, az alkalmazásokat, amelyek végezhetnek az Azure Search képesnek kell lennie.
- A fejlesztők a személyes adatok kereséséhez a dokumentum-adatbázisban képesnek kell lennie.

## <a name="azure-active-directory-data-discovery"></a>Azure Active Directory: Data discovery

[Az Azure Active Directory](https://azure.microsoft.com/services/active-directory/) a Microsoft felhőalapú, több-bérlős directory és az identity management szolgáltatás. Megkeresheti a felhasználói profilok felhasználói és az alkalmazottak és a felhasználó munkahelyi adatokat, amelyek személyes adatokat tartalmaznak a [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD-) környezet használatával a [Azure-portálon](https://portal.azure.com/).

Ez különösen akkor hasznos, ha azt szeretné, vagy módosítani az adott felhasználó személyes adatokat. Is hozzáadhat, vagy módosítsa a felhasználói profil és vonatkozó. A könyvtár egy globális rendszergazdai fiókkal kell bejelentkeznie.

### <a name="how-do-i-locate-or-view-user-profile-and-work-information"></a>Hogyan keresse meg és felhasználói profil megtekintése és vonatkozó?

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.

2. Válassza ki **minden szolgáltatás**, adja meg **felhasználók és csoportok** a szövegmezőbe, majd válassza ki azt a **Enter**.

   ![hogyan keresse meg a felhasználói profil és vonatkozó](media/how-to-discover-classify-personal-data-azure/user-profile.png)

3. Az a **felhasználók és csoportok** panelen válassza **felhasználók**.

      ![Nyitó felhasználók és csoportok](media/how-to-discover-classify-personal-data-azure/users-groups.png)

4. Az a **felhasználók és csoportok - felhasználók** panelen válasszon ki egy felhasználót a listából, és ezt követően a kiválasztott felhasználó paneljén válassza **profil** tartalmazhatnak személyes adatokat, a felhasználói profillal kapcsolatos információk megtekintéséhez.

      ![felhasználó kiválasztása](media/how-to-discover-classify-personal-data-azure/select-user.png)

5. Ha hozzáadása vagy módosítása a felhasználói profillal kapcsolatos információk van szüksége, ehhez, szabadon, majd a parancssávon válassza **mentéséhez.**
6. A kiválasztott felhasználó paneljén válassza **munkahelyi adatai** megtekinteni a felhasználó munkahelyi adatokat, amelyek személyes adatokat is tartalmazhat.

     ![megtekintés munkahely adatai](media/how-to-discover-classify-personal-data-azure/work-info.png)

7. Ha szeretne felhasználói a munkahelyi adatok hozzáadása vagy módosítása után ehhez, is, majd a parancssávon válassza **mentéséhez.**

## <a name="azure-sql-database-data-discovery"></a>Az Azure SQL Database: Adatok felderítése

[Az Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) egy felhő-adatbázis, amely a fejlesztőket létrehozása és alkalmazások karbantartása. Személyes adatok található [Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) szabványos SQL-lekérdezések használatával. Az Azure SQL rugalmas lekérdezés (előzetes verzió) lehetővé teszi, hogy a felhasználók közötti adatbázis-lekérdezések végrehajtásához.

A részletes [SQL-adatbázis](../sql-database/sql-database-technical-overview.md) az oktatóanyag azt ismerteti, SQL-adatbázis, beleértve, hogyan hozhat létre egyet, és a lekérdezések futtatása használata sok aspektusait. A következő oktatóanyagot konkrét szakaszokra mutató hivatkozásokkal adatainak összegzése látható.

### <a name="how-do-i-build-a-sql-database"></a>Hogyan építhető SQL-adatbázis?

Ehhez három módja van:

- Egy Azure SQL Database adatbázist hozhat létre a [Azure-portálon](https://portal.azure.com/). Az oktatóanyag azt ismertetjük a számítási és tárolási erőforrásokat egy erőforráscsoport és a logikai kiszolgáló belül egy adott készlete. Egy fiktív cég, AdventureWorks minta adatait fogja használni. Létre fog hozni egy kiszolgálószintű tűzfalszabályt is. Ennek módjáról további tudnivalókért keresse fel a [Azure SQL-adatbázis létrehozása az Azure portálon](../sql-database/sql-database-get-started-portal.md) oktatóanyag.

  ![Az Azure SQL-adatbázis létrehozása](media/how-to-discover-classify-personal-data-azure/create-database.png)
- SQL-adatbázis is létrehozhatók a [Azure Cloud rendszerhéj](https://azure.microsoft.com/features/cloud-shell/) CLI, egy webböngésző-alapú parancssori eszközt. Az eszköz érhető el az Azure portálon, és közvetlenül is futtatható. Ebben az oktatóanyagban, az eszköz indítása, adja meg a parancsfájl-változókat, hozzon létre egy erőforráscsoportot és a logikai kiszolgáló és tűzfalszabály konfigurálása. Majd hoz létre egy adatbázist mintaadatokkal. Az adatbázis létrehozása ezzel a módszerrel megismeréséhez látogasson el a [az Azure parancssori felület használatával egyetlen Azure SQL-adatbázis létrehozása](../sql-database/sql-database-get-started-cli.md) oktatóanyag.

  ![CLIT oktatóanyag](media/how-to-discover-classify-personal-data-azure/cli-tutorial.png)

>[!NOTE]
Az Azure CLI Linux rendszergazdák és fejlesztők használják. Egyes felhasználók található egyszerűbbé és intuitívabb PowerShell, mint amelyek a harmadik lehetőség.

- Végezetül hozhat létre egy SQL-adatbázist PowerShell, amely Azure és az egyéb erőforrások létrehozásához és kezeléséhez használt parancssori vagy parancsfájl eszközt. Ebben az oktatóanyagban, az eszköz indítása, adja meg a parancsfájl-változókat, hozzon létre egy erőforráscsoportot és a logikai kiszolgáló és tűzfalszabály konfigurálása. Ezután létre fog hozni a egy adatbázist mintaadatokkal.

Az oktatóanyag az Azure PowerShell 4.0-s vagy újabb verziója szükséges. Futtassa a Get-Module - ListAvailable AzureRM a verzió található. Ha telepíteni vagy frissíteni kell, lásd: telepítse az Azure PowerShell modul.

```PowerShell
New-AzureRmSQLDatabase -ResourceGroupName $resourcegroupname `
-ServerName $servername `
-DatabaseName $databasename `
-RequestedServiceObjectiveName "s0"
```

Az adatbázis létrehozása ezzel a módszerrel megismeréséhez látogasson el a [Powershell használatával egyetlen Azure SQL-adatbázis létrehozása](../sql-database/sql-database-get-started-powershell.md) oktatóanyag.

>[!Note]
A Windows rendszergazdák általában a PowerShell segítségével, de némelyikük inkább az Azure parancssori felület.

### <a name="how-do-i-search-for-personal-data-in-sql-database-in-the-azure-portal"></a>Hogyan keresse az SQL-adatbázis az Azure-portálon a személyes adatok?

A beépített lekérdezést szerkesztőt az Azure-portálon belül segítségével keresse meg a személyes adatok. Jelentkezzen be az eszközt, az SQL server rendszergazdai bejelentkezés és jelszavával fog, és majd adjon meg egy lekérdezést.

  ![keresési sql, a portál használatával](media/how-to-discover-classify-personal-data-azure/search-sql-portal.png)

Az oktatóanyag 5. lépés példalekérdezést megjeleníti a lekérdezés-szerkesztő panelen, de nem összpontosítson, személyes vagy bizalmas adatok (emellett egyesít két táblázatok adatait, és hozza létre a forrás oszlop aliasok visszaadott alatt). Az alábbi képernyőfelvételen látható a lekérdezés 5. lépés, valamint az eredmények ablaktábláján visszaadott:

  ![lekérdezésszerkesztő](media/how-to-discover-classify-personal-data-azure/query-editor.png)

Ha az adatbázis MyTable lett meghívva, pedig mintalekérdezést személyes adatokat tartalmazhat neve, társadalombiztosítási szám és azonosítószámát, és néz ki:

"Válassza ki a neve, társadalombiztosítási szám, azonosító szám a MyTable"

A lekérdezés futtatásához, és megjelenik az eredményeket a **eredmények** ablaktáblán.

Az Azure portálon SQL-adatbázis lekérdezése a további tudnivalókért keresse fel a [lekérdezni az SQL-adatbázis](../sql-database/sql-database-get-started-portal.md) az oktatóanyag szakasza.

### <a name="how-do-i-search-for-data-across-multiple-databases"></a>Hogyan kereshető adatokat több adatbázis között?

A rugalmas SQL-lekérdezés (előzetes verzió) segítségével egyetlen eredményt és az adatbázisok közötti és több adatbázis-lekérdezéseinek végrehajtására. A [oktatóanyag – áttekintés](../sql-database/sql-database-elastic-query-overview.md) forgatókönyvek részletes leírását tartalmazza, és ismerteti a függőleges és vízszintes adatbázis particionálási közötti különbség. Vízszintes particionálás neve "horizontális."

  ![Vertikális particionálás](media/how-to-discover-classify-personal-data-azure/vertical-partition.png)

  ![vízszintes particionálás](media/how-to-discover-classify-personal-data-azure/horizontal.png)

A kezdéshez, látogasson el a [Azure SQL Database rugalmas lekérdezési áttekintése (előzetes verzió)](../sql-database/sql-database-elastic-query-overview.md) lap.

#### <a name="power-query-for-importing-azure-hdinsight-hadoop-clusters-data-discovery-for-large-data-sets"></a>Power Query (az Azure HDInsight Hadoop-fürtök importálásához): nagy adatkészletek adatainak felderítése

Hadoop egy nyílt forráskódú nagy méretű adatkészletekhez, elemzése és a Hadoop-fürtök tárolja az Apache tárolás és feldolgozás szolgáltatás. [Az Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) lehetővé teszi a felhasználóknak az Azure-ban Hadoop-fürtök használata. A Power Query az Excel-bővítmény, amely, többek között segít felderíteni a különböző forrásokból származó adatok.

Az Azure HDInsight Hadoop-fürtök a személyes adatait a Power Query az Excel importálhatók. Amint az adatok az Excelben egy lekérdezés segítségével képes azonosítani.

#### <a name="how-do-i-use-excel-power-query-to-import-hadoop-clusters-in-azure-hdinsight-into-excel"></a>Miként használható az Excel Power Query az Excel programba importálhatók az Azure HDInsight Hadoop-fürtök?

A HDInsight az oktatóanyag végigvezeti a teljes folyamat. Előfeltételek ismerteti, és egy hivatkozást tartalmaz egy [Ismerkedés az Azure HDInsight](../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md) oktatóanyag. Útmutatás fedik le a Excel 2016, valamint 2010 és 2013 (lépésekre kissé eltérő, az Excel régebbi verzióira). Ha az Excel Power Query beépülő modul nem rendelkezik, a az oktatóanyag bemutatja, hogyan legyen. Az oktatóanyag kezdi, az Excel programban, és szükség lesz egy, a fürthöz tartozó Azure Blob storage-fiókot.

  ![A lekérdezés az Excel programban](media/how-to-discover-classify-personal-data-azure/excel.png)

Ennek módjáról további tudnivalókért keresse fel a [kapcsolódás Excel és a Hadoop Power Query használatával](../hdinsight/hadoop/apache-hadoop-connect-excel-power-query.md) oktatóanyag.

Forrás: [kapcsolódás Excel és a Hadoop Power Query használatával](../hdinsight/hadoop/apache-hadoop-connect-excel-power-query.md)

## <a name="azure-information-protection-personal-data-classification-for-documents-and-email"></a>Az Azure Information Protection: a dokumentumok és e-mailek személyes adatok besorolása

[Az Azure Information Protection](https://www.microsoft.com/cloud-platform/azure-information-protection) alkalmazható Azure-ügyfél súgó besorolása és a belső vagy külső megosztott dokumentumok védelme és a kommunikáció e-mail címkék. Ezek az elemek egy része az ügyfél vagy az alkalmazott személyes adatokat tartalmazhat. Szabályok és a feltételek meghatározása automatikusan vagy manuálisan, rendszergazdák és felhasználók. Például ha egy felhasználó egy dokumentumot, amely tartalmazza a hitelkártya adatait menti, ő jelenik meg a rendszergazda által beállított címke ajánlást.

### <a name="how-do-i-try-it"></a>Hogyan próbálja ki?

Ha azt szeretné, hogy Azure Information Protection megjelenítéséhez, ha előfordulhat, hogy egy beválik, ha a szervezet egy try, látogasson el a [gyors üzembe helyezési útmutató](https://docs.microsoft.com/information-protection/get-started/infoprotect-quick-start-tutorial). Azt végigvezeti öt alapvető – telepítés jelent meg a besorolási, címkézési és megosztása művelet házirend konfigurálására – és kisebb, mint fél óra kell végrehajtani.

### <a name="how-do-i-deploy-it"></a>Hogyan telepíthetem azt?

Ha azt szeretné, a szervezet Azure Information Protection telepítése, látogasson el a [üzembe helyezési menetrend a besorolás, címkézés és védelem](https://docs.microsoft.com/information-protection/plan-design/deployment-roadmap).

### <a name="is-there-anything-else-i-should-know"></a>Van-e más tisztában van szükségem?

Kiegészítő információt, amelyek azon, hogyan azt gondolja, hogy nyújt segítséget, látogasson el a [kész, állítsa be, védelme!](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)
blogbejegyzést. És az információk az Azure Information Protection bővebben alább felsorolt további hivatkozások ellenőrzése.

## <a name="azure-search-data-discovery-for-developer-apps"></a>Az Azure Search: adatok felderítési fejlesztői alkalmazások

[Az Azure Search](https://azure.microsoft.com/services/search/) egy felhőalapú keresési megoldás a fejlesztők számára, és részletes adatok keresési élményt nyújt az alkalmazások. Az Azure Search segítségével keresse meg az adatok között felhasználói indexek, Azure Cosmo DB, az Azure SQL Database, Azure Blob Storage, Azure Table storage vagy egyéni felhasználói JSON-adatok forrása. Az Azure Search REST API használatával keresse meg a személyes adatok típusok vagy a személyes adatok adott személyek Lucene lekérdezések felépítésének is lehet. Szolgáltatások közé tartozik a teljes szöveges keresés, egyszerű lekérdezés szintaxisát és Lucene lekérdezés szintaxisa. 

## <a name="how-do-i-use-sql-to-query-data"></a>Miként használható az SQL lekérdezni adatokat?

Először az alapok, látogasson el a [Azure CosmosD DB: lekérdezése SQL használatával](../cosmos-db/tutorial-query-documentdb.md) oktatóanyag. Az oktatóanyag egy minta dokumentumot és két minta az SQL-lekérdezések és eredmények biztosít.

További részletes útmutató az SQL-lekérdezések felépítésével, a Microsoft [Azure Cosmos DB dokumentum DB API SQL-lekérdezések.](../cosmos-db/sql-api-sql-query.md)

Azure Cosmos DB ismerkedik, és megtudhatja, hogyan hozhat létre adatbázist szeretne, ha felvesznek egy gyűjteményt, és adatok hozzáadása, látogasson el a [Azure Cosmos DB: egy SQL API-webalkalmazás létrehozása](../cosmos-db/create-sql-api-dotnet.md) gyors üzembe helyezési útmutató. Ha azt szeretné, .NET, Java vagy Python, például nyelven ehhez csak válassza ki a kívánt nyelvet Miután a helyhez.

## <a name="next-steps"></a>További lépések

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50)

[Mi az SQL Database?](../sql-database/sql-database-technical-overview.md)

[SQL adatbázis lekérdezés-szerkesztő elérhető Azure-portálon] (https://azure.microsoft.com/blog/t-sql-query-editor-in-browser-azure-portal/)

[Mi az az Azure Information Protection?](https://docs.microsoft.com/information-protection/understand-explore/what-is-information-protection)

[Mi az az Azure Rights Management?](https://docs.microsoft.com/information-protection/understand-explore/what-is-azure-rms)

[Az Azure Information Protection: Kész, állítsa be, védelme!](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)
