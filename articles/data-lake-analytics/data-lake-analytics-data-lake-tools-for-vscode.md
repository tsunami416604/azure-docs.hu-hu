---
title: "Az Azure Data Lake Tools: Használata Azure Data Lake Tools Visual Studio Code |} Microsoft Docs"
description: "Ismerje meg, hogyan használható az Azure Data Lake Tools for Visual Studio Code létrehozása, tesztelése és U-SQL-parancsfájlok futtatása. "
Keywords: VScode,Azure Data Lake Tools,Local run,Local debug,Local Debug,preview file,upload to storage path,download,upload
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2018
ms.author: jejiang
ms.openlocfilehash: 7e1e2c0a5481a81e9267bcf87076076b377a1496
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2018
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Az Azure Data Lake Tools használja a Visual Studio Code

Tudhat meg az Azure Data Lake Tools for Visual Studio (kód VS) hozhat létre, tesztelheti, és a U-SQL-parancsfájlok futtatása. Az adatokat is tartalmazza, az a következő videót:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Előfeltételek

VSCode az Azure Data Lake Tools támogatja a Windows, Linux és MacOS.  

- [A Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).

MacOS és Linux rendszerekhez:
- [A .NET core SDK 2.0](https://www.microsoft.com/net/download/core). 
- [Monó 5.2.x](http://www.mono-project.com/download/).

## <a name="install-data-lake-tools"></a>A Data Lake Tools telepítése

Az előfeltételek telepítése után a Data Lake Tools for VS kód is telepítheti.

**A Data Lake Tools telepítése**

1. Nyissa meg a Visual Studio Code.
2. Kattintson a **bővítmények** a bal oldali ablaktáblán. Adja meg **Azure Data Lake** be a keresőmezőbe.
3. Kattintson a **telepítése** melletti **Azure Data Lake tools**. Néhány másodpercen belül a **telepítése** gomb változik **Újrabetöltés**.
4. Kattintson a **Újrabetöltés** aktiválja a **Azure Data Lake tools** bővítmény.
5. Kattintson a **Újrabetöltés ablak** megerősítéséhez. Látható **Azure Data Lake tools** a Bővítmények ablaktáblán.

    ![Data Lake Tools for Visual Studio Code bővítmények ablaktábla](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

 
## <a name="activate-azure-data-lake-tools"></a>Az Azure Data Lake Tools aktiválása
Hozzon létre egy új .usql fájlt, és aktiválja a bővítmény létező .usql fájl megnyitása. 

## <a name="open-the-sample-script"></a>Nyissa meg a parancsfájlpéldát
Nyissa meg a parancs palettát (Ctrl + Shift + P), és írja be **ADL: Nyissa meg a minta-parancsprogram**. Ez a minta egy másik példánya nyílik meg. Szerkeszthető, konfigurálása, és küldje el a parancsfájl ezen a példányon.

## <a name="work-with-u-sql"></a>U-SQL műveletek

A U-SQL fájl vagy mappa történő együttműködésre a U-SQL kell megnyitni.

**A U-SQL projekt mappa megnyitása**

1. Visual Studio Code, válassza ki a **fájl** menüben, majd válassza ki **mappa megnyitása**.
2. Adjon meg egy mappát, majd válassza ki **Mappaválasztás**.
3. Válassza ki a **fájl** menüben, majd válassza ki **új**. A projekt egy névtelen-1-fájl kerül.
4. A névtelen-1-fájlba írja be a következő kódot:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    A parancsfájl létrehoz egy departments.csv fájlt a/output mappában szereplő adatokkal.

5. Mentse a fájlt **myUSQL.usql** a megnyitott mappában.

**A U-SQL parancsfájl összeállításához**

1. Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát. 
2. Adja meg **ADL: parancsfájl összeállításához**. A fordítás eredményei jelennek meg a **kimeneti** ablak. Jobb gombbal egy olyan parancsfájlt, és válassza **ADL: parancsfájl összeállításához** fordítási egy U-SQL-feladatot. A fordítási eredménye megjelenik az **kimeneti** ablaktáblán.
 
**A U-SQL parancsfájl elküldeni**

1. Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát. 
2. Adja meg **ADL: elküldeni a feladatot**.  Jobb gombbal egy olyan parancsfájlt, és válassza **ADL: feladat elküldése**. 

 Miután elküldött egy U-SQL-feladatot, a küldésének naplók megjelennek a **kimeneti** ablak a Visual STUDIO-kódban. Feladat nézete is megjelenik a jobb oldali ablaktáblán. Ha a küldése sikeres, a feladat URL-címe valószínűleg is. A feladat URL-címet egy webböngészőben a valós idejű feladat állapotát nyomon nyithatja meg. A feladat megtekintése az Összegzés lapon megtekintheti a feladat részleteit. Főbb funkciók küldje el újból parancsfájl tartalmazzák, ismétlődő parancsfájl, nyissa meg a portálon. A feladat megtekintése az adatok lapon hivatkozhat a bemeneti fájlok, a kimeneti fájlok, erőforrás. Fájlok helyi számítógép tölthető le.

   ![A Data Lake Tools for Visual Studio Code konfigurációs fájl](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

   ![A Data Lake Tools for Visual Studio Code konfigurációs fájl](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Alapértelmezett környezet beállítása**

 A parancsfájl fájlokhoz alkalmazza ezt a beállítást, ha nem állított fájl paramétereinek rendre alapértelmezett környezet állíthatja be.

1. Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát. 
2. Adja meg **ADL: állítsa be az alapértelmezett környezetben**.
3. Vagy kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza ki **ADL: Set Default Context**, majd válassza ki azt a fiókot, adatbázis, sémát. A beállítás mentett xxx_settings.json konfigurációs fájlt.

    ![A Data Lake Tools for Visual Studio Code konfigurációs fájl](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Parancsfájl-paraméterek beállítása**

1. Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát. 
2. Adja meg **ADL: állítsa be a parancsfájl paramétereit**.
3. xxx_settings.JSON fájl megnyitása a következő tulajdonságokkal:

  - Fiók: Egy Data Lake Analytics-fiók alatt az Azure-előfizetéssel ahhoz szükséges, hogy lefordítani a U-SQL-feladatok és futtatása, úgy kell konfigurálnia a számítógépfiók fordítása, és a U-SQL-feladatok futtatása előtt.
    - Adatbázis: A fiók alatt adatbázis. Az alapértelmezett érték **fő**.
    - Séma: Az adatbázis a séma. Az alapértelmezett érték **dbo**.
    - Választható beállítások:
        - Prioritás: A prioritási tartománya: 1 – 1 és 1000 a legmagasabb prioritás. Az alapértelmezett érték **1000**.
        - Párhuzamossági: A párhuzamos végrehajtás tartomány: 1 – 150. Az alapértelmezett érték: az Azure Data Lake Analytics-fiók engedélyezett maximális párhuzamosságát. 

        ![A Data Lake Tools for Visual Studio Code konfigurációs fájl](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
        > [!NOTE] 
        > A konfiguráció mentése után a fiók, adatbázis és séma adatai jelennek meg a megfelelő .usql fájl bal alsó sarkában lévő állapotjelző sáv Ha nincs beállítva alapértelmezett környezet.

**Set Git figyelmen kívül hagyása**

1. Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát. 
2. Adja meg **ADL: Set Git figyelmen kívül hagyása**.

    - Ha nem rendelkezik egy **.gitIgnore** a mappában található fájl VSCode működő, nevű fájl **.gitIgnor** a mappában jön létre. Négy elemek (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) alapértelmezés szerint a fájlba kerülnek. További biztosíthatja a frissítéseket, ha szükséges.
    - Ha már rendelkezik egy **.gitIgnore** a mappában található fájl VSCode működő, az eszköz négy elemek hozzáadása (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) azokat a **.gitIgnore** fájlt, ha a négy elemek nem szerepeltek a fájlt.

    ![A Data Lake Tools for Visual Studio Code konfigurációs fájl](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="use-python-r-and-csharp-code-behind-file"></a>Python, R és CSharp háttérkód fájl használata
Azure Data Lake eszköz támogatja több egyéni esetében, az utasításokat lásd: [fejlesztése U-SQL Python, R és az Azure Data Lake Analytics VSCode a csharp nyelvű](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="use-assemblies"></a>Szerelvények használata

Szerelvények fejlesztéséről további információkért lásd: [fejlesztése U-SQL-szerelvények Azure Data Lake Analytics-feladatok](data-lake-analytics-u-sql-develop-assemblies.md).

A Data Lake Tools segítségével egyéni kód szerelvények regisztrálja a Data Lake Analytics-katalógusban.

**Egy szerelvény regisztrálása**

A szerelvény segítségével regisztrálhatja a **ADL: szerelvény regisztrálása** vagy **ADL: szerelvény regisztrálni (speciális)** parancsok.

**Az ADL keresztül regisztrálni: szerelvény regisztrálása parancs**
1.  Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát.
2.  Adja meg **ADL: szerelvény regisztrálása**. 
3.  Adja meg a helyi szerelvény elérési útja. 
4.  Válassza ki a Data Lake Analytics-fiók.
5.  Válasszon ki egy adatbázist.

Eredmények: A portál egy böngészőben meg van nyitva, és megjeleníti a szerelvény regisztrációs folyamat során.  

Indítás, egy másik kényelmesen a **ADL: szerelvény regisztrálása** parancs: kattintson a jobb gombbal a .dll fájlt a Fájlkezelőben. 

**Regisztrálni, ha az ADL: szerelvény regisztrálni (speciális)**
1.  Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát.
2.  Adja meg **ADL: a szerelvény (speciális) regisztrálása**. 
3.  Adja meg a helyi szerelvény elérési útja. 
4.  A JSON-fájl jelenik meg. Tekintse át, és szükség esetén szerkessze a szerelvény függőségeit és erőforrás-paraméterek. Utasítások jelennek meg a **kimeneti** ablak. Folytassa a szerelvény regisztrációját, mentse a JSON-fájl (Ctrl + S).

    ![A Data Lake Tools for Visual Studio Code háttérkód](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
   >[!NOTE]
   >- Szerelvény függőségek: Azure Data Lake Tools egy DLL-re van-e függőségekről. A függőségek állapotúak után jelennek meg a JSON-fájl. 
   >- Erőforrás: A DLL-erőforrások (például .txt, .png és .csv) feltöltheti a szerelvények regisztrálásakor részeként. 

Úgy is elindítható a **ADL: szerelvény regisztrálni (speciális)** parancs: kattintson a jobb gombbal a .dll fájlt a Fájlkezelőben. 

A következő U-SQL kód bemutatja, hogyan hívhatja meg egy szerelvényt. A szerelvény neve: a minta *tesztelése*.


        REFERENCE ASSEMBLY [test];

        @a = 
            EXTRACT 
                Iid int,
            Starts DateTime,
            Region string,
            Query string,
            DwellTime int,
            Results string,
            ClickedUrls string 
            FROM @"Sample/SearchLog.txt" 
            USING Extractors.Tsv();

        @d =
            SELECT DISTINCT Region 
            FROM @a;

        @d1 = 
            PROCESS @d
            PRODUCE 
                Region string,
            Mkt string
            USING new USQLApplication_codebehind.MyProcessor();

        OUTPUT @d1 
            TO @"Sample/SearchLogtest.txt" 
            USING Outputters.Tsv();


## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Mielőtt fordítási, és a Data Lake Analytics U-SQL parancsfájlt, csatlakoznia kell az Azure-fiókjával.

**Csatlakozás az Azure-bA**

1.  Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát. 
2.  Adja meg **ADL: bejelentkezési**. A felső területen megjelenik a bejelentkezési adatok.

    ![A Data Lake Visual Studio Code parancs paletta eszközei](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![a Data Lake Tools for Visual Studio Code eszköz bejelentkezési adatok](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3.  Kattintson a **nyissa meg a & másolása** megnyitni a bejelentkezési képernyőn látható weblapon URL-cím: https://aka.ms/devicelogin. Illessze be a kódot **G567LX42V** a szövegmezőbe, majd válassza ki azt a **Folytatás**.

   ![A Data Lake Tools for Visual Studio Code bejelentkezési illessze be a kódot](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  Kövesse a képernyőn látható weblapon a bejelentkezéshez. Ha kapcsolódik, az Azure-fiók neve megjelenik a bal alsó sarkában lévő állapotjelző sáv a **Visual STUDIO Code** ablak. 

    > [!NOTE] 
    >- Data Lake eszköz automatikusan bejelentkezik következő alkalommal, ha előtt bejelentkezett, de nem jelentkezik még.
    >- A fiókjának van engedélyezve, a két tényező, azt javasoljuk, hogy használja-e PIN-kóddal helyett phone hitelesítési.


Jelentkezzen ki, írja be a parancs **ADL: kijelentkezési**.

## <a name="list-your-data-lake-analytics-accounts"></a>A Data Lake Analytics-fiókok listázása

A kapcsolat teszteléséhez a Data Lake Analytics-fiókok listájának lekérése.

**A Data Lake Analytics-fiókok az Azure-előfizetéshez tartozó listázásához**

1. Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát.
2. Adja meg **ADL: fiókok listában**. A fiókok megjelennek a **kimeneti** ablaktáblán.


## <a name="access-the-data-lake-analytics-catalog"></a>A Data Lake Analytics-katalógus elérése

Miután csatlakozott az Azure-ba, az alábbi lépések segítségével hozzáférni a U-SQL catalog.

**Azure Data Lake Analytics metaadatok eléréséhez**

1.  Válassza ki a Ctrl + Shift + P, és írja be **ADL: listáját táblákat**.
2.  Válasszon egyet a Data Lake Analytics-fiókok.
3.  Válassza ki, egy Data Lake Analytics adatbázis.
4.  Válasszon ki egy séma. A táblák listáját láthatja.

## <a name="view-data-lake-analytics-jobs"></a>Nézet Data Lake Analytics-feladatok

**Data Lake Analytics-feladatok megtekintése**
1.  Nyissa meg a parancs paletta (Ctrl + Shift + P), és válassza ki **ADL: feladatok megjelenítése**. 
2.  Válasszon egy Data Lake Analytics vagy helyi fiók. 
3.  Várjon, amíg a feladatok listája jelenik meg a fiókhoz.
4.  Jelölje ki a feladatot feladatot a listából, a Data Lake Tools a feladat nézetének megnyitása. a jobb oldali ablaktáblán, és bizonyos információkat jeleníti meg a Visual STUDIO Code **kimeneti**.

    ![A Data Lake Tools for Visual Studio Code IntelliSense objektumtípusok](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-integration"></a>Azure Data Lake tároló integrációja

Az Azure Data Lake tárolással kapcsolatos parancsokat használhatja:
 - Tallózzon az Azure Data Lake tárolási erőforrások. [A tároló elérési útját listában](#list-the-storage-path). 
 - Tekintse meg az Azure Data Lake-tárolási fájl. [Tekintse meg a tárolófájl](#preview-the-storage-file). 
 - Feltölteni a fájlt közvetlenül az Azure Data Lake tárolási VS-kódban. [Töltse fel a fájl vagy mappa](#upload-file-or-folder).
 - Töltse le a fájlt közvetlenül az Azure Data Lake-tárolóból Visual STUDIO Code. [Fájl letöltése](#download-file).

### <a name="list-the-storage-path"></a>A tároló elérési útját listában 

**A tárolási elérési útja a parancs paletta keresztül listájára**

Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza ki **ADL: elérési útja**.

A listában válassza ki a mappát, vagy kattintson a **elérési utat adjon meg** vagy **keresse meg a legfelső szintű** (használ adjon meg egy elérési utat példaként). -> Válassza ki a **ADLA fiók**. Navigálás ->, vagy adja meg a tároló mappa elérési útját (például: / kimeneti /). A parancs paletta listák -> az elérési úthoz adatok alapján.

![A Data Lake Tools for Visual Studio Code tárolási elérési útja eredmények felsorolása](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Több kényelmesen a relatív elérési útját listában kattintson a jobb gombbal a helyi menüből keresztül történik.

**Kattintson a jobb gombbal a tároló elérési úttal listájára**

Kattintson a jobb gombbal az elérési útvonal karakterláncának kiválasztásához **elérési útja** folytatja.

![A Data Lake Tools for Visual Studio Code kattintson a jobb gombbal a helyi menü](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>A tárolófájl megtekintése

Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza ki **ADL: Preview fájl**.

Válassza ki a **ADLA fiók**. Adjon meg egy Azure storage fájl elérési útját (például /output/SearchLog.txt) ->. Result ->: VSCode fájl megnyitása.

   ![A Data Lake Tools for Visual Studio Code előzetes fájl eredménye](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Egy másik preview fájl módja a helyi menüben a fájl elérési útját vagy a fájl relatív elérési út a parancsfájl-szerkesztőben. 

### <a name="upload-file-or-folder"></a>Töltse fel a fájl vagy mappa

1. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza ki **fájl feltöltése** vagy **feltöltése mappa**.

2. Válasszon egy fájl vagy több fájl, ha válassza ki a feltölteni a fájlt, vagy válassza a az egész mappa Ha válassza feltöltési mappa, majd kattintson a **feltöltése**. -> Válassza a tároló mappát a listában, vagy kattintson a **elérési utat adjon meg** vagy **keresse meg a legfelső szintű** (használ adjon meg egy elérési utat példaként). -> Válassza ki a **ADLA fiók**. Navigálás ->, vagy adja meg a tároló mappa elérési útját (például: / kimeneti /). Kattintson -> **aktuális mappa kiválasztása** elemre a feltöltési célhelyének megadásához.

   ![A Data Lake Tools for Visual Studio Code feltöltési állapot](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    


   Egy másik feltöltése fájlok tárolási módja a helyi menüben a fájl elérési útját vagy a fájl relatív elérési út a parancsfájl-szerkesztőben.

Egy időben, figyelheti a [Feltöltés állapota](#check-storage-tasks-status).


### <a name="download-file"></a>Fájl letöltése 
Letöltheti a fájlokat a parancsok beírásával **ADL: fájl letöltése** vagy **ADL: (speciális) fájl letöltése**.

**Töltse le a fájlokat, ha az ADL: fájl letöltése (speciális)**
1. Kattintson a jobb gombbal a parancsprogram-szerkesztő, majd válassza ki **(speciális) fájl letöltése**.
2. Visual STUDIO Code jeleníti meg a JSON-fájl. Adja meg az elérési utat, és egyszerre több fájl letöltéséhez. Utasítások jelennek meg a **kimeneti** ablak. Folytatja a fájl letöltéséhez, mentse a JSON-fájl (Ctrl + S).

    ![A Data Lake Tools for konfigurációs fájlokat Visual Studio Code letöltése](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

3.  Eredmények: A **kimeneti** ablak a fájl feltöltési állapotot jeleníti meg.

    ![A Visual Studio Code letöltése eszközök Data Lake több fájl eredmények](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Egy időben, figyelheti a [letöltés állapota](#check-storage-tasks-status).

**Bár az ADL fájlok letöltéséhez: fájl letöltése**

1. Kattintson a jobb gombbal a parancsprogram-szerkesztő, jelölje be **fájl letöltése**, majd válassza ki a célmappát, az a **Mappaválasztás** párbeszédpanel.

2. A listában válassza ki a mappát, vagy kattintson a **elérési utat adjon meg** vagy **keresse meg a legfelső szintű** (használ adjon meg egy elérési utat példaként). -> Válassza ki a **ADLA fiók**. Navigálás ->, vagy adja meg a tároló mappa elérési útját (például: / kimeneti /) -> Válassza ki a fájl letöltéséhez.

   ![A Data Lake Tools for Visual Studio Code letöltési állapot](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

   
   Egy másik módszer a tárolási fájlok letöltése a helyi menüben a fájl elérési útját vagy a fájl relatív elérési út a parancsfájl-szerkesztőben van.

Egy időben, figyelheti a [letöltés állapota](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Ellenőrizze a tárolási feladatok állapotát
Az állapotsor letöltését, majd ismét feltölteni a befejezésekor alján állapotát jeleníti meg.
1. Kattintson az alábbi állapotsor, és ezután a letöltés, majd ismét feltölteni a állapot megjelenítése **kimeneti** panel.

   ![A Data Lake Tools for Visual Studio Code ellenőrizze tároló állapota](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="vscode-explorer-integration-with-azure-data-lake"></a>Azure Data Lake VSCode Explorer integrációja

**Azure-integráció** 

- Bejelentkezés az Azure-ba, mielőtt mindig bővítheti **DATALAKE EXPLORER**, majd kattintson a **Azur bejelentkezés** történő bejelentkezés az Azure-bA. Bejelentkezés, után megjelenik az Azure-fiókjában az előfizetések jelennek meg a bal oldali paneljének láthatóságát a **DATALAKE EXPLORER**. 

   ![DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/sign-in-datalake-explorer.png)

   ![DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

**ADLA metaadatok navigációs** 

- Bontsa ki az Azure-előfizetéséhez, a U-SQL-adatbázis navigálhat nézet a **sémák**, **hitelesítő adatok**, **szerelvények**, **tábla**, **Index**, és így tovább, a U-SQL-adatbázisok csomópont alatt.

**ADLA metaadat-entitás kezelése**

- Bontsa ki a **U-SQL-adatbázisok**, kattintson a jobb gombbal létrehozhat egy új adatbázist, séma, tábla, táblatípusokban, index, statisztika a **parancsfájl létrehozása** a megfelelő csomópont alatt a helyi menüből. A megnyitott parancsfájl lapon szerkesztheti a parancsprogramot az igényeinek megfelelően, majd kattintson a jobb gombbal a helyi menüben a feladat elküldéséhez **ADL: feladat elküldése**. Végén létrehozni, kattintson a helyi menü **frissítése** az új elem létrehozása megjelenítéséhez. Kattintson a jobb gombbal a helyi menüből is törölheti a cikk **törlése**.

   ![DataLake explorer hoz létre az új elem menü](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

   ![DataLake explorer hoz létre az új elem parancsfájl](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

**ADLA szerelvények regisztrálásakor**

 - Is **szerelvény regisztrálása** kattintson a jobb gombbal a megfelelő adatbázisba a **szerelvények** csomópont.

    ![DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

**ADLS-integráció** 

 - Navigáljon a **Tárfiók**, is **előzetes**, **letöltése**, **törlése**, **másolási relatív elérési út**, **Másolási teljes elérési útja** által a helyi menüben a fájl csomóponton. Is **frissítése**, **feltöltése**, **feltöltése mappa**, **törlése** kattintson a jobb gombbal a helyi menüben a mappa csomóponton.

   ![DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

   ![DataLake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-download-preview-file.png)

## <a name="open-adl-storage-explorer-in-portal"></a>Nyissa meg a portál ADL a Tártallózó
1. Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát.
2. Adja meg **megnyitása webes Azure Tártallózó** vagy relatív elérési út vagy a teljes elérési útja, a parancsfájl-szerkesztőben kattintson a jobb gombbal, majd válassza ki **megnyitása webes Azure Tártallózó**.
3. Válassza ki a Data Lake Analytics-fiók.

A Data Lake Tools az Azure-portálon az Azure storage elérési megnyitása. Keresse meg az elérési út, és tekintse meg a fájlt az internetről.

## <a name="local-run-and-local-debug-for-windows-users"></a>Helyi futtatáskor és a helyi hibakeresése Windows felhasználók
U-SQL helyi futtatásával teszteli a helyi adatok, és a parancsfájl helyileg ellenőrzi, mielőtt a kódot közzéteszi a Data Lake Analytics az. A helyi hibakeresési funkciója lehetővé teszi a következő feladatok végezhetők, mielőtt a kód elküldve a Data Lake Analytics: 
- A C# háttérkód hibakeresési. 
- A kód lépéseit. 
- A parancsfájl helyi ellenőrzése.

A helyi futtatás helyi hibakeresési útmutatásért lásd: [U-SQL helyi futtatásával és a helyi hibakeresése a Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="additional-features"></a>További funkciók

A Data Lake Tools for Visual STUDIO Code támogatja a következő funkciókat:

-   Automatikus kitöltés IntelliSense: javaslatok elemek, például a kulcsszavak, a metódusok és a változók körüli előugró ablak jelenik meg. Az objektumok különböző típusú ikonjai különböző:

    - Scala adattípus
    - Összetett adattípusú
    - Built-in UDTs
    - .NET adatgyűjtési és -osztályok
    - C# kifejezések
    - Beépített C# felhasználó által megadott függvények, udo-k és UDAAGs 
    - U-SQL-funkciók
    - U-SQL Ablakozó függvény
 
    ![A Data Lake Tools for Visual Studio Code IntelliSense objektumtípusok](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Automatikus kitöltés a Data Lake Analytics metaadatok IntelliSense: Data Lake Tools tölt le helyileg a Data Lake Analytics metaadatait. Az IntelliSense funkció automatikusan tölti fel az objektumok, például az adatbázis, séma, tábla, nézet, táblázat értékű függvényben, eljárások és C#-szerelvények, a Data Lake Analytics-metaadatok.
 
    ![A Data Lake Tools for Visual Studio Code IntelliSense metaadatok](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   IntelliSense hiba jelölő: Data Lake Tools kiemeli a szerkesztési hibák U-SQL és C#. 
-   Szintaxis emeli ki: a Data Lake Tools megkülönböztetni azokat az elemeket, például a változók, kulcsszavakat, adattípus és funkciók különböző színt használ. 

    ![A Data Lake Tools for Visual Studio Code szintaxis kiemelések](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>További lépések
- [U-SQL Python, R és az Azure Data Lake Analytics a VSCode a CSharp fejlesztése](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL helyi futtatásával és a helyi hibakeresése a Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Oktatóanyag: Ismerkedés az Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Oktatóanyag: U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
- [Azure Data Lake Analytics-feladatok U-SQL-szerelvények fejlesztése](data-lake-analytics-u-sql-develop-assemblies.md)




