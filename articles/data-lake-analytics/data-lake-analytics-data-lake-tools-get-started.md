---
title: "U-SQL-szkriptek fejlesztése a Data Lake Tools for Visual Studio használatával | Microsoft Docs"
description: "Ismerje meg, hogyan telepítheti a Data Lake Tools for Visual Studio eszközt, valamint hogyan fejleszthet és tesztelhet U-SQL-parancsfájlokat. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/17/2017
ms.author: edmaca, yanacai
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: f5a27eba14560a56ad5020daf7741f37ac2cc6f2
ms.lasthandoff: 03/21/2017


---
# <a name="tutorial-develop-u-sql-scripts-using-data-lake-tools-for-visual-studio"></a>Oktatóanyag: U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Ismerje meg, hogyan telepítheti a Data Lake Tools for Visual Studio eszközt, és hogyan használhatja U-SQL-parancsfájlok fejlesztésére és tesztelésére.

A U-SQL egy rendkívüli mértékben méretezhető, széles körben bővíthető nyelv, amellyel bármilyen, a Data Lake adattárban vagy azon kívül tárolt adat előkészíthető, átalakítható és elemezhető. További információért lásd az [U-SQL-segédanyagot](http://go.microsoft.com/fwlink/p/?LinkId=691348).

## <a name="prerequisites"></a>Előfeltételek
* **Visual Studio 2015 3. frissítéssel, Visual Studio 2013 4. frissítéssel vagy Visual Studio 2012. Az Enterprise (Ultimate/Prémium), Professional és Community kiadások mind támogatottak; az Express kiadás nem támogatott. A Visual Studio 2017 jelenleg nem támogatott.**
* **Microsoft Azure SDK for .NET 2.7.1-es vagy újabb verzió**.  Telepítse a [Webplatform-telepítővel](http://www.microsoft.com/web/downloads/platform.aspx).
* **[Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs)**.

    A Data Lake Tools for Visual Studio telepítése után a Server Explorer eszközben az „Azure” csomópont alatt megjelenik egy „Data Lake Analytics” csomópont (a Server Explorer a Ctrl+Alt+S billentyűkombináció lenyomásával nyitható meg).

* **Data Lake Analytics-fiókok és mintaadatok** A Data Lake Tools nem támogatja a Data Lake Analytics-fiókok létrehozását. Ilyen fiókokat az Azure Portal, az Azure PowerShell, a .NET SDK vagy az Azure parancssori felület (CLI) használatával hozhat létre.
Hogy megkönnyítsük a munkáját, egy Data Lake Analytics-szolgáltatás létrehozásához és a forrásadatfájl feltöltéséhez használható parancsfájlt találhat itt: [„A” melléklet – PowerShell-példa az oktatóanyag előkészítéséhez](data-lake-analytics-data-lake-tools-get-started.md#appx-a-powershell-sample-for-preparing-the-tutorial).

    További lehetőségként [Az Azure Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md) oktatóanyag következő két szakasza alapján létrehozhatja a saját fiókját, és manuálisan feltöltheti az adatokat.

    1. [Azure Data Lake Data Lake Analytics-fiók létrehozása](data-lake-analytics-get-started-portal.md#create-data-lake-analytics-account).
    2. [A SearchLog.tsv fájl feltöltése az alapértelmezett Data Lake-tárfiókba](data-lake-analytics-get-started-portal.md#prepare-source-data).

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz
**A Data Lake Analytics szolgáltatáshoz való kapcsolódás**

1. Nyissa meg a Visual Studiót.
2. A **View** (Nézet) menüben kattintson a **Server Explorer** elemre a Server Explorer eszköz megnyitásához. Ezt a **[CTRL]+[ALT]+S** billentyűkombináció lenyomásával is megnyithatja.
3. Kattintson a jobb gombbal az **Azure** elemre, majd kattintson a „Connect to Microsoft Azure Subscription” (Csatlakozás egy Microsoft Azure-előfizetéshez) lehetőségre, és kövesse a megjelenő utasításokat.
4. A **Server Explorer** eszközben bontsa ki az **Azure** elemet, majd a **Data Lake Analytics** elemet. Ekkor megjelenik a Data Lake Analytics-fiókok listája, ha vannak ilyenek. A Visual Studio eszközben nem lehet Data Lake Analytics-fiókokat létrehozni. A fiókok létrehozásával kapcsolatos információkért lásd: [Az Azure Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md) vagy [Az Azure Data Lake Analytics használatának első lépései az Azure PowerShell-lel](data-lake-analytics-get-started-powershell.md).

## <a name="upload-source-data-files"></a>Forrásadatfájlok feltöltése
Az oktatóanyag korábbi **Előfeltétel** szakaszában már feltöltött adatokat.  

Ha a saját adatait szeretné használni, a Data Lake Tools szolgáltatásból a következő módszerek használatával tölthet fel adatokat.

**Fájlok feltöltése a függő Azure Data Lake-fiókba**

1. A **Server Explorer** eszközben bontsa ki az **Azure** elemet, majd a **Data Lake Analytics** elemet, bontsa ki a saját Data Lake Analytics-fiókjait, és bontsa ki a **Storage Accounts** (Tárfiókok) elemet. Látni fogja az alapértelmezett Data Lake-tárfiókot, a kapcsolt Data Lake-tárfiókokat és a kapcsolt Azure Storage-fiókokat. Az alapértelmezett Data Lake-fiókot egy „Default Storage Account” (Alapértelmezett tárfiók) címke jelöli.
2. Kattintson a jobb gombbal az alapértelmezett Data Lake-tárfiókra, majd kattintson az **Explorer** elemre.  Ekkor megnyílik a Data Lake Tools for Visual Studio szolgáltatás Explorer ablaktáblája.  Ebben a bal oldalon egy fa nézet, a jobb oldalon pedig a tartalmak láthatók.
3. Lépjen arra a mappára, ahová fájlokat szeretne feltölteni.
4. Kattintson a jobb gombbal egy üres területen, majd kattintson az **Upload** (Feltöltés) parancsra.

    ![U-SQL Visual Studio-projekt U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-upload-files.png)

**Fájlok feltöltése egy társított Azure Blob Storage-fiókba**

1. A **Server Explorer** eszközben bontsa ki az **Azure** elemet, majd a **Data Lake Analytics** elemet, bontsa ki a saját Data Lake Analytics-fiókjait, és bontsa ki a **Storage Accounts** (Tárfiókok) elemet. Látni fogja az alapértelmezett Data Lake-tárfiókot, a kapcsolt Data Lake-tárfiókokat és a kapcsolt Azure Storage-fiókokat.
2. Bontsa ki az Azure-tárfiókot.
3. Kattintson a jobb gombbal arra a tárolóra, amelybe fájlokat szeretne feltölteni, majd kattintson az **Explorer** elemre. Ha nincsenek tárolói, először létre kell hoznia egyet az Azure portál, az Azure PowerShell, vagy egy egyéb eszköz használatával.
4. Lépjen arra a mappára, ahová fájlokat szeretne feltölteni.
5. Kattintson a jobb gombbal egy üres területen, majd kattintson az **Upload** (Feltöltés) parancsra.

## <a name="develop-u-sql-scripts"></a>U-SQL-parancsfájlok fejlesztése
A Data Lake Analytics-feladatok nyelve a U-SQL. További információk a U-SQL-ről: [U-SQL nyelv – első lépések](data-lake-analytics-u-sql-get-started.md) és [U-SQL nyelvi referencia](http://go.microsoft.com/fwlink/?LinkId=691348).

**Data Lake Analytics-feladat létrehozása és elküldése**

1. Kattintson a **File** (Fájl) menüben a **New** (Új), majd a **Project** (Projekt) elemre.
2. Válassza ki a **U-SQL projekt** típust.

    ![új U-SQL Visual Studio-projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Kattintson az **OK** gombra. A Visual Studio létrehoz egy megoldást egy **Script.usql** fájllal.
4. Adja hozzá a következő parancsot a **Script.usql** fájlhoz:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();

        @res =
            SELECT *
            FROM @searchlog;        

        OUTPUT @res   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Ez a U-SQL-parancsfájl beolvassa a forrásadatfájlt az **Extractors.Tsv()** segítségével, majd létrehoz egy csv-fájlt az **Outputters.Csv()** használatával.

    Ne módosítsa az elérési utat, kivéve, ha átmásolta a forrásfájlt egy másik helyre.  A Data Lake Analytics létrehozza a kimeneti mappát, ha az még nem létezik.

    Egyszerűbb relatív útvonalakat használni az alapértelmezett Data Lake-fiókokban tárolt fájlokhoz. De használhat abszolút elérési utakat is.  Példa:

        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    A társított tárfiókokban lévő fájlok eléréséhez abszolút elérési utakat kell használnia.  A társított Azure Storage-fiókban tárolt fájlok szintaxisa:

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

   > [!NOTE]
   > A nyilvános blobokkal vagy a nyilvános tárolókhoz hozzáférési jogosultságokkal rendelkező Azure Blob-tárolók használata jelenleg nem támogatott.  
   >
   >

    Tekintse meg az alábbi funkciókat:

   * **IntelliSense**

       A neveket automatikusan befejezi a rendszer, és megjelennek a sorhalmaz, az osztályok, az adatbázisok, a sémák és a felhasználó által meghatározott objektumok (UDO-k) tagjai.

       A katalógusokhoz (pl. adatbázisokhoz, sémákhoz, táblákhoz, UDO-khoz) tartozó IntelliSense a számítási fiókhoz kapcsolódik. A felső eszköztárban látható a jelenleg aktív számítási fiók, adatbázis és séma, amelyek a legördülő menükben módosíthatók.
   * *** oszlop kibontása**

       Kattintson a *karaktertől jobbra, és egy kék aláhúzást fog látni a* alatt. Mozgassa a mutatót a kék aláhúzásra, majd kattintson a lefelé mutató nyílra.
       ![Kibontás a Data Lake Visual Studio Tools szolgáltatásban *](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-expand-asterisk.png)

       Kattintson az **Expand Columns** (Oszlopok kibontása) lehetőségre, és az eszköz a * jel helyére behelyettesíti az oszlopok neveit.
   * **Automatikus formázás**

       A felhasználók a kód szerkezete alapján módosíthatják az U-SQL szkript behúzásának mértékét az Edit (Szerkesztés) -> (Advanced) Speciális menüben:

     * Dokumentum formázása (Ctrl+E, D): a teljes dokumentumot formázza   
     * Kijelölés formázása (Ctrl+K, Ctrl+F): a kijelölt részletet formázza. Ha semmi sincs kijelölve, ez a parancs azt a sort formázza, amelyben a kurzor áll.  

       A formázási szabályok mindegyike konfigurálható a Tools (Eszközök) -> Options (Beállítások) -> Text Editor (Szövegszerkesztő) > SIP > Formatting (Formázás) menüpont alatt.  
   * **Intelligens behúzás**

       A Data Lake Tools for Visual Studio a parancsok írása közben képes egyes kifejezések automatikus behúzására. Ez a szolgáltatás alapértelmezés szerint le van tiltva, a felhasználó a U-SQL -> Options and Settings (Beállítások) -> Switches (Kapcsolók) -> Enable Smart Indent (Intelligens behúzás engedélyezése) menüpont alatt kapcsolhatja be.
   * **Definícióhoz ugrás és minden hivatkozás keresése**

       Ha a jobb gombbal kattint egy sorhalmaz, paraméter, oszlop, UDO vagy egyéb elem nevére, a Go To Definition (Definícióhoz ugrás) parancsra kattintva (vagy az F12 billentyűvel) a definíciójához léphet. A Find All References (Minden hivatkozás keresése) parancs (vagy a Shift+F12 billentyűkombináció) megmutatja az összes hivatkozást.
   * **Azure-elérési út beszúrása**

       Ahelyett, hogy egy fájl Azure-elérési útját megjegyezné és kézzel beírná a parancsok írásakor, a Data Lake Tools for Visual Studio egy egyszerűbb megoldást kínál: kattintson a szerkesztőben a jobb gombbal, majd kattintson az Insert Azure Path (Azure-elérési út beszúrása) lehetőségre. Lépjen a fájlhoz az Azure Blob Browser (Azure-blobtallózás) párbeszédablakban. Kattintson az **OK** gombra. A szolgáltatás beszúrja a fájl elérési útját a kódba.
5. Adja meg a Data Lake Analytics-fiókot, -adatbázist és -sémát. Tesztelés céljából a parancsot helyileg is futtathatja, ha a **(local)** (helyi) lehetőséget választja. További információk: [Run U-SQL locally](#run-u-sql-locally) (U-SQL helyi futtatása).

    ![U-SQL Visual Studio-projekt elküldése](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

    További információk: [Use U-SQL catalog](data-lake-analytics-use-u-sql-catalog.md) (U-SQL-katalógus használata).
6. A **Solution Explorer** eszközben kattintson a jobb gombbal a **Script.usql** fájlra, majd kattintson a **Build Script** (Parancsfájl létrehozása) elemre. Erősítse meg az eredményt az Output (Kimenet) ablaktáblán.
7. A **Solution Explorer** eszközben kattintson a jobb gombbal a **Script.usql** fájlra, majd kattintson a **Submit Script** (Parancsfájl elküldése) lehetőségre. A **Submit** (Elküldés) gombra is kattinthat a Script.usql ablaktáblán.  Lásd az előző képernyőképet.  Kattintson a Submit (Elküldés) gomb melletti lefelé mutató nyílra az alábbi speciális beállításokkal történő küldéshez:
8. Adja meg a **Feladat nevét**, erősítse meg az **Analytics-fiókot**, majd kattintson a **Submit** Elküldés gombra. Az elküldés után az eredmények és a feladatra mutató hivatkozás megjelenik a Data Lake Tools for Visual Studio Eredmények ablakában.

    ![U-SQL Visual Studio-projekt elküldése](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)
9. Kattintson a Refresh (Frissítés) gombra a feladat legfrissebb állapotának megtekintéséhez. Ha a feladat sikeresen lezajlott, megjelenik a **Job Graph** (Feladat grafikonja), a **Meta Data Operations** (Metaadat-műveletek), a **State History** (Állapotelőzmények) és a **Diagnostics** (Diagnosztika):

    ![U-SQL Visual Studio Data Lake Analytics-feladat teljesítménygrafikonja](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * Feladat összegzése. Megjeleníti az aktuális feladattal kapcsolatos összegző információkat, pl. az állapotot, az előrehaladást, a végrehajtás idejét, a futtatókörnyezet nevét vagy a küldőt.   
   * Feladat részletei. Részletes információkat ad a feladatról, köztük a parancsfájlt, a forrásokat és a Vertex végrehajtási nézetet.
   * Feladat grafikonjai. Négy grafikonon jeleníti meg a feladattal kapcsolatos információkat: az előrehaladást, az olvasott és írt adatok mennyiségét, a végrehajtás idejét, a csomópontonkénti átlagos végrehajtási időt, valamint az átviteli be- és kimenetet.
   * Metaadat-műveletek. Megjelenít minden metaadatokkal kapcsolatos műveletet.
   * Állapotelőzmények.
   * Diagnosztika. A Data Lake Tools for Visual Studio automatikusan diagnosztizálja a feladat végrehajtását. Értesítéseket küld, ha hibák vagy teljesítményproblémák lépnek fel a feladatokban. További információért lásd a Feladatdiagnosztika (hivatkozás később) részt.

**Feladat állapotának ellenőrzése**

1. A Server Explorer eszközben bontsa ki az **Azure** elemet, majd a **Data Lake Analytics** elemet, végül a Data Lake Analytics-fiók nevét
2. Kattintson duplán a **Jobs** (Feladatok) elemre a feladatok listázásához.
3. Kattintson egy feladatra az állapota megtekintéséhez.

**Feladat kimenetének megtekintése**

1. A **Server Explorer** eszközben bontsa ki az **Azure** elemet, majd a **Data Lake Analytics** elemet, bontsa ki a saját Data Lake Analytics-fiókjait, bontsa ki a **Storage Accounts** (Tárfiókok) elemet, kattintson a jobb gombbal az alapértelmezett Data Lake-tárfiókra, majd kattintson az **Explorer** elemre.
2. Kattintson duplán az **output** (kimenet) mappára a megnyitásához
3. Kattintson duplán a **SearchLog-From-adltools.csv** fájlra.

### <a name="job-playback"></a>Feladat visszajátszása
A feladat visszajátszásával végignézheti egy feladat végrehajtásának menetét, és vizuálisan észlelheti a teljesítményanomáliákat és a szűk keresztmetszeteket. Ez a funkció egy feladat végrehajtásának befejeződése előtt (tehát a feladat aktív futása közben) és a végrehajtás befejezése után is használható. A végrehajtás közbeni visszajátszáskor a felhasználó az aktuális állapotig nézheti újra a feladat menetét.

**Feladat végrehajtási folyamatának megtekintése**  

1. Kattintson a **Load Profile** (Profil betöltése) lehetőségre a jobb felső sarokban. Lásd az előző képernyőképet.
2. Kattintson a Play (Lejátszás) gombra a bal alsó sarokban a feladat végrehajtási folyamatának megtekintéséhez.
3. A visszajátszás közben kattintson a **Pause** (Szünet) gombra a megállításhoz, vagy húzza a folyamatjelzőt egy adott pontra.

### <a name="heat-map"></a>Hőtérkép
A Data Lake Tools for Visual Studio szolgáltatás feladatnézetében a felhasználó által választható színsémákkal jelezhető a feladatok előrehaladása, adatbemenete és -kimenete, végrehajtási ideje és az egyes szintek átviteli be- és kimenete. Ennek segítségével a felhasználók közvetlenül és intuitív módon ismerhetik fel a lehetséges problémákat és a feladatok tulajdonságainak eloszlását. A megjelenítendő adatforrás a legördülő listából választható ki.  

## <a name="run-u-sql-locally"></a>U-SQL helyi futtatása

Használhatja az Azure Data Lake Tools for Visual Studio és az Azure Data Lake U-SQL SDK szolgáltatásokat a U-SQL-feladatok munkaállomáson való futtatására, pontosan úgy, ahogyan azt az Azure Data Lake szolgáltatásban is megteheti. Ez a két, helyi futtatású szolgáltatás időt takarít meg a U-SQL feladatok tesztelése és hibakeresése során. 

* [U-SQL feladatok tesztelése és hibakeresése helyi futtatással és az Azure Data Lake U-SQL SDK használatával](data-lake-analytics-data-lake-tools-local-run.md)


## <a name="see-also"></a>Lásd még:
A Data Lake Analytics különböző eszközökkel való használatának megismeréséhez lásd:

* [A Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md)
* [A Data Lake Analytics használatának első lépései az Azure PowerShell-lel](data-lake-analytics-get-started-powershell.md)
* [A Data Lake Analytics használatának első lépései a .NET SDK-val](data-lake-analytics-get-started-net-sdk.md)
* [Hibakeresés a C#-kódban – U-SQL-feladatok](data-lake-analytics-debug-u-sql-jobs.md)

A Data Lake Tools for Visual Studio kódolásának megismeréséhez lásd [az Azure Data Lake Tools for Visual Studio kód használatát](data-lake-analytics-data-lake-tools-for-vscode.md) ismertető cikket.

További fejlesztői témakörökért lásd:

* [Webes naplók elemzése a Data Lake Analytics segítségével](data-lake-analytics-analyze-weblogs.md)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
* [Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével](data-lake-analytics-u-sql-get-started.md)
* [Felhasználó által definiált U-SQL-operátorok fejlesztése Data Lake Analytics-feladatokhoz](data-lake-analytics-u-sql-develop-user-defined-operators.md)

## <a name="appx-a-powershell-sample-for-preparing-the-tutorial"></a>„A” melléklet – PowerShell-példa az oktatóanyag előkészítéséhez
A következő PowerShell-parancsfájl előkészít egy Azure Data Lake Analytics-fiókot és a forrásadatokat. Ezt átugorva az [U-SQL-parancsfájlok fejlesztése](data-lake-analytics-data-lake-tools-get-started.md#develop-u-sql-scripts) részhez léphet.

    #region - used for creating Azure service names
    $nameToken = "<Enter an alias>"
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion

    #region - service names
    $resourceGroupName = $namePrefix + "rg"
    $dataLakeStoreName = $namePrefix + "adas"
    $dataLakeAnalyticsName = $namePrefix + "adla"
    $location = "East US 2"
    #endregion


    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an Azure Data Lake Analytics service account
    Write-Host "Create a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
    New-AzureRmDataLakeStoreAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeStoreName `
        -Location $location

    Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
    New-AzureRmDataLakeAnalyticsAccount `
        -Name $dataLakeAnalyticsName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultDataLake $dataLakeStoreName

    Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
    Get-AzureRmDataLakeAnalyticsAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeAnalyticsName  
    #endregion

    #region - prepare the source data
    Write-Host "Import the source data ..."  -ForegroundColor Green
    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file.
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

    Write-Host "List the source data ..."  -ForegroundColor Green
    Get-AzureRmDataLakeStoreChildItem -Account $dataLakeStoreName -Path  "/Samples/Data/"
    #endregion

