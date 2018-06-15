---
title: Az Azure Data Lake Tools használja a Visual Studio Code
description: Ismerje meg, hogyan használható az Azure Data Lake Tools for Visual Studio Code létrehozása, tesztelése és U-SQL-parancsfájlok futtatása.
services: data-lake-analytics
author: Jejiang
ms.author: jejiang
manager: kfile
editor: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 79cd1a04c99891e5146ad20cfd36b8bd4fe4d893
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261484"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Az Azure Data Lake Tools használja a Visual Studio Code

Ebből a cikkből megtudhatja, hogyan Azure Data Lake Tools használhatja a Visual Studio (kód VS) létrehozásához, tesztelheti, és U-SQL-parancsfájlok futtatása. Az adatokat is tartalmazza, az a következő videót:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Előfeltételek

Az Azure Data Lake Tools for Visual STUDIO Code Windows, Linux és MacOS támogatja.  

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

MacOS és Linux rendszerekhez:
- [A .NET core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Monó 5.2.x](http://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Az Azure Data Lake eszközök telepítése

Az előfeltételek telepítése után a Azure Data Lake Tools for VS kód is telepítheti.

**Azure Data Lake Tools telepítése**

1. Nyissa meg a Visual Studio Code-ot.
2. Válassza ki **bővítmények** a bal oldali ablaktáblán. Adja meg **Azure Data Lake Tools** be a keresőmezőbe.
3. Válassza ki **telepítése** melletti **Azure Data Lake Tools**. 

   ![A Data Lake Tools telepítésének megerősítése](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Néhány másodpercen belül a **telepítése** gomb felirata **Újrabetöltés**.
4. Válassza ki **Újrabetöltés** aktiválja a **Azure Data Lake Tools** bővítmény.
5. Válassza ki **Újrabetöltés ablak** megerősítéséhez. Látható **Azure Data Lake Tools** a a **bővítmények** ablaktáblán.

 
## <a name="activate-azure-data-lake-tools"></a>Az Azure Data Lake Tools aktiválása
Hozzon létre egy .usql fájlt, vagy aktiválni a bővítmény létező .usql fájl megnyitása. 


## <a name="work-with-u-sql"></a>U-SQL műveletek

U-SQL használatához egy U-SQL-fájl vagy mappa kell megnyitni.

**A parancsfájlpéldát megnyitása**

Nyissa meg a parancs palettát (Ctrl + Shift + P), és írja be **ADL: Nyissa meg a minta-parancsprogram**. Ez a minta egy másik példánya nyílik meg. Szerkeszthető, konfigurálása, és küldje el a parancsfájl ezen a példányon.

**A U-SQL projekt mappa megnyitása**

1. Visual Studio Code, válassza ki a **fájl** menüben, majd válassza ki **mappa megnyitása**.
2. Adjon meg egy mappát, majd válassza ki **Mappaválasztás**.
3. Válassza ki a **fájl** menüben, majd válassza ki **új**. A projekt egy névtelen-1-fájl kerül.
4. Írja be a következő kódot a névtelen-1 fájlban:

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
2. Adja meg **ADL: elküldeni a feladatot**. Jobb gombbal egy olyan parancsfájlt, és válassza **ADL: feladat elküldése**. 

Miután elküldött egy U-SQL-feladatot, a küldésének naplók megjelennek a **kimeneti** ablak a Visual STUDIO-kódban. A feladat nézet jelenik meg, a jobb oldali ablaktáblán. Ha a küldése sikeres, a feladat URL-címe valószínűleg túl. A feladat URL-címet egy webböngészőben a valós idejű feladat állapotát nyomon nyithatja meg. 

A feladatok nézetben **összegzés** lapon, láthatja a feladat részleteit. Fő ide tartozik a küldje el újra a parancsfájlt, ismétlődő egy parancsfájl, és nyissa meg a portálon. A feladatok nézetben **adatok** lapon hivatkozhat a bemeneti fájlok, a kimeneti fájlok és az erőforrás fájlok. Fájlok a helyi számítógép tölthető le.

![A feladatok nézetben összegző lap](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Az adatok lapon a feladatok nézetben](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Az alapértelmezett környezet beállítása**

Az alapértelmezett környezet a parancsfájl fájlokhoz alkalmazza ezt a beállítást, ha nem állított paramétereket a fájlokat egyenként is beállíthatja.

1. Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát. 
2. Adja meg **ADL: állítsa be az alapértelmezett környezetben**. Vagy kattintson a jobb gombbal a parancsprogram-szerkesztő, és válasszon **ADL: Set Default Context**.
3. Válassza ki a fiókot, az adatbázis és a sémát. A beállítás a xxx_settings.json konfigurációs fájl kerül.

   ![Fiók, adatbázis és séma állítja be az alapértelmezett környezetben](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Parancsfájl-paraméterek beállítása**

1. Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát. 
2. Adja meg **ADL: állítsa be a parancsfájl paramétereit**.
3. A xxx_settings.json fájl megnyitása a következő tulajdonságokkal:

   - **fiók**: az Azure Data Lake Analytics-fiók alatt az Azure-előfizetéssel kell lefordítani és a U-SQL feladatok futtatása szükséges. A számítógépfiók kell konfigurálnia, mielőtt fordítása, és a U-SQL feladatok futtatásához.
   - **adatbázis**: a fiók alatt egy adatbázis. Az alapértelmezett érték **fő**.
   - **séma**: az adatbázis a sémát. Az alapértelmezett érték **dbo**.
   - **optionalSettings**:
        - **prioritás**: A prioritási tartománya: 1 és 1000, az 1. a legmagasabb prioritás. Az alapértelmezett érték **1000**.
        - **degreeOfParallelism**: A párhuzamos végrehajtás tartomány: 1 – 150. Az alapértelmezett érték: az Azure Data Lake Analytics-fiók engedélyezett maximális párhuzamosságát. 

   ![A JSON-fájl tartalmát](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
> [!NOTE] 
> Miután a konfiguráció mentéséhez, a fiók, az adatbázis és a sémaadatok jelennek meg a megfelelő .usql fájlt bal alsó sarkában lévő állapotjelző sáv Ha nincs beállítva alapértelmezett kontextusban.

**Git beállítása figyelmen kívül hagyása**

1. Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát. 
2. Adja meg **ADL: Set Git figyelmen kívül hagyása**.

   - Ha nem rendelkezik egy **.gitIgnore** fájlt a Visual STUDIO Code működő mappában, nevű fájl **.gitIgnore** a mappában jön létre. Négy elemek (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) alapértelmezés szerint a fájlban kerülnek. Szükség esetén teszi lehetővé több frissítést.
   - Ha már rendelkezik egy **.gitIgnore** fájlt a Visual STUDIO Code működő mappában, az eszköz négy elemek hozzáadása (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) található a **.gitIgnore** fájlt, ha a négy elemek nem szerepeltek a fájlt.

   ![A .gitIgnore fájlt szereplő elemek](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Háttérkód fájlok: C Sharp, a Python és az R

Az Azure Data Lake Tools támogatja több egyéni esetében. Útmutatásért lásd: [fejlesztése U-SQL Python, R és az Azure Data Lake Analytics Visual STUDIO Code éles C](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Szerelvények használata

Szerelvények fejlesztéséről további információkért lásd: [fejlesztése U-SQL-szerelvények Azure Data Lake Analytics-feladatok](data-lake-analytics-u-sql-develop-assemblies.md).

A Data Lake Tools segítségével egyéni kód szerelvények regisztrálja a Data Lake Analytics-katalógusban.

**Egy szerelvény regisztrálása**

A szerelvény segítségével regisztrálhatja a **ADL: szerelvény regisztrálása** vagy **ADL: szerelvény regisztrálni (speciális)** parancs.

**Az ADL keresztül regisztrálni: szerelvény regisztrálása parancs**
1.  Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát.
2.  Adja meg **ADL: szerelvény regisztrálása**. 
3.  Adja meg a helyi szerelvény elérési útja. 
4.  Válassza ki a Data Lake Analytics-fiók.
5.  Válasszon ki egy adatbázist.

A portál egy böngészőben meg van nyitva, és megjeleníti a szerelvény regisztrációs folyamat során.  

Több kényelmesen indul el, a **ADL: szerelvény regisztrálása** parancs: kattintson a jobb gombbal a .dll fájlt a Fájlkezelőben. 

**Az ADL keresztül regisztrálni: parancs regisztrálása szerelvény (speciális)**
1.  Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát.
2.  Adja meg **ADL: a szerelvény (speciális) regisztrálása**. 
3.  Adja meg a helyi szerelvény elérési útja. 
4.  A JSON-fájl jelenik meg. Tekintse át, és szükség esetén szerkessze a szerelvény függőségeit és erőforrás-paraméterek. Utasítások jelennek meg a **kimeneti** ablak. Folytassa a szerelvény regisztrációját, mentse a JSON-fájl (Ctrl + S).

    ![A szerelvény függőségeit és erőforrás-paraméterek JSON-fájl](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
>[!NOTE]
>- Azure Data Lake Tools egy DLL-re van-e a szerelvény függőségeit. A függőségek azok még észlelése után jelennek meg a JSON-fájlt. 
>- A szerelvények regisztrálásakor részeként feltöltheti a DLL-erőforrások (például .txt, .png és .csv). 

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


## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>U-SQL helyi futtatásával és a helyi hibakeresési használata a Windows-felhasználók
U-SQL helyi futtatásával teszteli a helyi adatok, és a parancsfájl helyileg ellenőrzi, mielőtt a kódot közzéteszi a Data Lake Analytics az. A helyi hibakeresési szolgáltatás segítségével a következő feladatok végezhetők, mielőtt a kód elküldve a Data Lake Analytics: 
- A C# háttérkód hibakeresési. 
- A kód lépéseit. 
- A parancsfájl helyi ellenőrzése.

A helyi futtatás helyi hibakeresési útmutatásért lásd: [U-SQL helyi futtatásával és a helyi hibakeresése a Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).


## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Mielőtt fordítási, és a Data Lake Analytics U-SQL parancsfájlt, csatlakoznia kell az Azure-fiókjával.

<b id="sign-in-by-command">Csatlakozás az Azure-bA egy parancs használatával</b>

1.  Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát. 
2.  Adja meg **ADL: bejelentkezési**. A bejelentkezési adatok jelenik meg a képernyő jobb alsó sarkában.

    ![A bejelentkezési parancs](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Bejelentkezési és hitelesítési szóló értesítés küldése](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  Válassza ki **nyissa meg a & másolása** megnyitásához a [bejelentkezési weblap](https://aka.ms/devicelogin). Illessze be a kódját, majd válassza ki **Folytatás**.

    ![Bejelentkezési weblap](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  
     
4.  Kövesse a képernyőn látható weblapon a bejelentkezéshez. Amikor a kapcsolat létrejött, az Azure-fiók neve megjelenik a Visual STUDIO Code ablak bal alsó sarkában lévő állapotjelző sáv. 

> [!NOTE] 
>- A Data Lake Tools automatikusan bejelentkezik a legközelebb ha kijelentkezik nem.
>- A fiókjának van engedélyezve, a két tényező, azt javasoljuk, hogy használja-e PIN-kóddal helyett phone hitelesítési.


Jelentkezzen ki, írja be a parancs **ADL: kijelentkezési**.

**Az Explorer Azure való kapcsolódáshoz**

Bontsa ki a **AZURE DATALAKE**, jelölje be **jelentkezzen be Azure**, és hajtsa végre a 3. lépés és 4. lépése, [parancs használatával csatlakozni Azure](#sign-in-by-command).

![A Explorer "Jelentkezzen be Azure" kiválasztása](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

A Intézőből nem kijelentkezés. Jelentkezzen ki, lásd: [parancs használatával csatlakozni Azure](#sign-in-by-command).


## <a name="create-an-extraction-script"></a>Létrehozhat egy kibontási parancsfájlt 
A parancs használatával hozhat létre egy .txt, .csv és .tsv fájlok kibontási parancsfájl **ADL: KIBONTÁSA parancsfájl létrehozása** vagy az Azure Data Lake Explorer.

**A parancs segítségével kibontási parancsfájl létrehozására**

1. Válassza ki a Ctrl + Shift + P nyissa meg a parancs palettát, és írja be **ADL: KIBONTÁSA parancsfájl létrehozása**.
2. Adjon meg egy Azure Storage-fájl elérési útját, és válassza ki az Enter billentyűt.
3. Válasszon egy fiókot.
4. Egy .txt fájlt válassza a bontsa ki a fájlt. 

![Egy kibontási parancsfájl létrehozásának folyamata](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

A kinyerési parancsfájl alapján jön létre. Egy olyan parancsfájlt, amely nem észlelhető oszlop, válassza ki a két lehetőség közül. Ha nem, csak egy parancsprogram jön létre.

![Eredménye egy kibontási parancsfájl létrehozása](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**Kibontási parancsfájl létrehozására a Intézőből**

Egy másik létrehozni a kibontási parancsfájl módja a helyi (helyi) menüben a .csv, .tsv vagy az Azure Data Lake Store- vagy Azure Blob storage .txt fájlt.

!["EXTRACT parancsfájl létrehozása" parancs a helyi menü](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>A parancs az Azure Data Lake Analytics integrálja

Fiókok listázása, a hozzáférés metaadatok és az analytics-feladatok megtekintése az Azure Data Lake Analytics-erőforrásokat érheti el. 

**Az Azure Data Lake Analytics-fiókok alatt az Azure-előfizetéshez listázásához**

1. Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát.
2. Adja meg **ADL: fiókok listában**. A fiókok megjelennek a **kimeneti** ablaktáblán.

**Azure Data Lake Analytics metaadatok eléréséhez**

1.  Válassza ki a Ctrl + Shift + P, és írja be **ADL: listáját táblákat**.
2.  Válasszon egyet a Data Lake Analytics-fiókok.
3.  Válassza ki, egy Data Lake Analytics adatbázis.
4.  Válasszon ki egy séma. A táblák listáját láthatja.

**Azure Data Lake Analytics-feladatok megtekintése**
1.  Nyissa meg a parancs paletta (Ctrl + Shift + P), és válassza ki **ADL: feladatok megjelenítése**. 
2.  Válasszon egy Data Lake Analytics vagy helyi fiók. 
3.  Várjon, amíg a feladatlistában a jelennek meg a fiókhoz.
4.  Jelölje ki a feladatot a feladatot a listából. A Data Lake Tools a feladat nézetének megnyitása. a jobb oldali ablaktáblán, és néhány adatait jeleníti meg a Visual STUDIO kódkimenetet.

    ![Feladatlista](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Azure Data Lake Store integrálható egy paranccsal

Az Azure Data Lake Store-kapcsolódó parancsokat használhatja:
 - [Tallózzon az Azure Data Lake Store-erőforrások](#list-the-storage-path) 
 - [Tekintse meg az Azure Data Lake Store-fájl](#preview-the-storage-file) 
 - [Feltölteni a fájlt közvetlenül az Azure Data Lake Store Visual STUDIO Code](#upload-file-or-folder)
 - [Töltse le a fájlt közvetlenül az Azure Data Lake Store Visual STUDIO Code](#download-file)

### <a name="list-the-storage-path"></a>A tároló elérési útját listában 

**A tárolási elérési útja a parancs paletta keresztül listájára**

1. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza ki **ADL: elérési útja**.
2. A listában válassza ki a mappát, vagy válasszon **adjon meg egy elérési utat** vagy **böngészni az elérési útjának gyökeréhez**. (Használunk **adjon meg egy elérési utat** példaként.) 
3. Válassza ki a Data Lake Analytics-fiókját.
4. Keresse meg, vagy adja meg a tároló mappa elérési útját (például/kimeneti /).  

A parancs paletta sorolja fel az elérési úthoz adatok alapján.

![Tárolási elérési útja eredmények](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Egy kényelmesebbé teszik a relatív elérési útját listában módja a helyi menüből.

**A tárolási elérési útja a helyi menüből listájára**

Kattintson a jobb gombbal az elérési út karakterláncát, és válassza ki **elérési útja**.

!["Elérési útját listában" a helyi menü](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>A tárolófájl megtekintése

1. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza ki **ADL: Preview fájl**.
2. Válassza ki a Data Lake Analytics-fiókját. 
3. Adjon meg egy Azure Storage fájl elérési útját (például /output/SearchLog.txt). 

Visual STUDIO Code nyílik meg.

![Lépéseket és a tároló fájl előnézetének eredménye](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Tekintse meg a fájl másik módja a helyi menüben a fájl elérési útját vagy a fájl relatív elérési út a parancsprogram-szerkesztő az keresztül történik. 

### <a name="upload-a-file-or-folder"></a>Töltse fel a fájl vagy mappa

1. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza ki **fájl feltöltése** vagy **feltöltése mappa**.
2. Válassza a fájl egy vagy több fájl, ha a kiválasztott **fájl feltöltése**, vagy válassza ki a teljes mappát, ha a kiválasztott **feltöltése mappa**. Válassza ki **feltöltése**. 
3. Válassza ki a tároló mappát a listában, vagy válasszon **adjon meg egy elérési utat** vagy **böngészni az elérési útjának gyökeréhez**. (Használunk **adjon meg egy elérési utat** példaként.) 
4. Válassza ki a Data Lake Analytics-fiókját. 
5. Keresse meg, vagy adja meg a tároló mappa elérési útját (például/kimeneti /). 
6. Válassza ki **aktuális mappa kiválasztása** elemre a feltöltési célhelyének megadásához.

![Lépéseket és feltöltése, a fájl vagy mappa eredménye](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    

Egy másik fájlok feltöltése tárolási módja a fájl elérési útját vagy a fájl relatív elérési út a parancsprogram-szerkesztő az a helyi menüből.

Is [figyelheti a feltöltési állapotot](#check-storage-tasks-status).


### <a name="download-a-file"></a>Fájl letöltése 
A parancs segítségével letöltheti a fájlt **ADL: fájl letöltése** vagy **ADL: (speciális) fájl letöltése**.

**Az ADL keresztül fájlok letöltéséhez: parancs fájl letöltése (speciális)**
1. Kattintson a jobb gombbal a parancsprogram-szerkesztő, majd válassza ki **(speciális) fájl letöltése**.
2. Visual STUDIO Code jeleníti meg a JSON-fájl. Adja meg az elérési utat, és egyszerre több fájl letöltéséhez. Utasítások jelennek meg a **kimeneti** ablak. Folytatja a fájl vagy fájlokat letölteni, mentse a JSON-fájl (Ctrl + S).

    ![JSON-fájl elérési utak, letöltése](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

A **kimeneti** ablakban a fájl letöltése állapotát jeleníti meg.

![Kimeneti ablak a letöltési állapotáról](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Is [a letöltési állapotának figyelése](#check-storage-tasks-status).

**Az ADL keresztül fájlok letöltéséhez: fájl letöltése parancs**

1. Kattintson a jobb gombbal a parancsprogram-szerkesztő, jelölje be **fájl letöltése**, majd válassza ki a célmappát, az a **Mappaválasztás** párbeszédpanel.
2. A listában válassza ki a mappát, vagy válasszon **adjon meg egy elérési utat** vagy **böngészni az elérési útjának gyökeréhez**. (Használunk **adjon meg egy elérési utat** példaként.) 
3. Válassza ki a Data Lake Analytics-fiókját. 
4. Keresse meg a, illetve adja meg a tároló mappa elérési útját (például/kimeneti /), majd válassza ki a fájl letöltéséhez.

![Lépéseket és a fájl letöltése eredménye](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

Egy másik tárolási fájlok letöltése módja a fájl elérési útját vagy a fájl relatív elérési út a parancsprogram-szerkesztő az a helyi menüből.

Is [a letöltési állapotának figyelése](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Ellenőrizze a tárolási feladatok állapotát
A feltöltés és letöltés állapota megjelenik az állapotsorban. Válassza ki az állapotsor, és ezután állapota megjelenik a **kimeneti** fülre.

![Állapotsor és kimeneti részletei](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Azure Data Lake Analytics integrálja az Intézőből

Után jelentkezzen be, Azure-fiókját az előfizetések alatt találhatók a bal oldali ablaktáblában **AZURE DATALAKE**. 

![A Data Lake explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>A Data Lake Analytics metaadatok navigációs

Bontsa ki az Azure-előfizetéshez. Az a **U-SQL-adatbázisok** csomópont, tallózhat az U-SQL-adatbázis és a mappák megtekintése, például **sémák**, **hitelesítő adatok**, **szerelvények**, **Táblák**, és **Index**.

### <a name="data-lake-analytics-metadata-entity-management"></a>A Data Lake Analytics metaadatok entitás kezelése

Bontsa ki a **U-SQL-adatbázisok**. Kattintson a jobb gombbal a megfelelő csomópont, és jelölje be létrehozhat egy adatbázist, séma, tábla, táblatípus, index vagy statisztika **parancsfájl létrehozása** a helyi menüben. A megnyitott parancsfájl lapon szükség szerint a parancsfájl szerkesztése. Majd kattintson a jobb gombbal, majd válassza a a feladat elküldéséhez **ADL: feladat elküldése**. 

A cikk létrehozása után kattintson jobb gombbal a csomópontra, és válassza ki **frissítése** elem megjelenítése. Ehhez kattintson a jobb gombbal, és jelölje be a cikk is törölhet **törlése**.

!["Parancsfájl létrehozása" parancs a helyi menüben a Data Lake Explorerben](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Az új elem parancsfájl lap](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>A Data Lake Analytics szerelvények regisztrálásakor

Is regisztrálhat egy szerelvényt a megfelelő adatbázist kattintson a jobb gombbal a **szerelvények** csomópont, és jelölje be **szerelvény regisztrálása**.

!["Szerelvény regisztrálása" parancs a helyi menüben a szerelvények csomópont](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Azure Data Lake Store integrálható az Intézőből

Keresse meg a **Data Lake Store**:

- Kattintson a jobb gombbal a mappa csomópont, és ezután a **frissítése**, **törlése**, **feltöltése**, **feltöltése mappa**, **másolása Relatív elérési út**, és **másolási teljes elérési útja** parancsok a helyi menüben.

   ![A Data Lake explorer egy mappa csomópontjának helyi menüjének parancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Kattintson a jobb gombbal a fájl csomópont, és ezután a **előzetes**, **letöltése**, **törlése**, **KIBONTÁSA parancsfájl létrehozása** (csak a fürt megosztott kötetei szolgáltatás esetén érhető el TSV, és a TXT-fájlok), **másolási relatív elérési út**, és **másolási teljes elérési útja** parancsok a helyi menüben.

   ![A Data Lake-kezelő egy fájl csomópontjának helyi menüjének parancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Azure Blob storage-ának a explorer integrálása

Keresse meg a Blob-tároló:

- Kattintson a jobb gombbal a blob-tároló csomópont, és ezután a **frissítése**, **Blob-tároló törlése**, és **Blob feltöltése** parancsok a helyi menüben.

   ![A blob tároló csomópont alatt a Blob storage helyi menüjének parancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Kattintson a jobb gombbal a mappa csomópont, és ezután a **frissítése** és **Blob feltöltése** parancsok a helyi menüben.

   ![A Blob-tároló mappa csomópontját a helyi menüjének parancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Kattintson a jobb gombbal a fájl csomópont, és ezután a **megtekintése/szerkesztése**, **letöltése**, **törlése**, **KIBONTÁSA parancsfájl létrehozása** (rendelkezésre álló csak CSV TSV és TXT-fájlok) **másolási relatív elérési út**, és **másolási teljes elérési útja** parancsok a helyi menüben.

    ![A fájl csomópont alatt a Blob storage helyi menüjének parancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Nyissa meg a Data Lake explorer a portálon
1. Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát.
2. Adja meg **megnyitása webes Azure Tártallózó** vagy relatív elérési út vagy a teljes elérési útja, a parancsfájl-szerkesztőben kattintson a jobb gombbal, majd válassza ki **megnyitása webes Azure Tártallózó**.
3. Válassza ki a Data Lake Analytics-fiók.

A Data Lake Tools az Azure-portálon az Azure Storage elérési megnyitása. Keresse meg az elérési út, és tekintse meg a fájlt az internetről.

## <a name="additional-features"></a>További funkciók

A Data Lake Tools for Visual STUDIO Code támogatja a következő funkciókat:

-   **IntelliSense autocomplete**: javaslatok megjelennek például a kulcsszavak, a metódusok és a változók előugró ablakokat. Különböző ikonjai különböző típusú objektumok:

    - Scala adattípus
    - Összetett adattípusú
    - Beépített UDTs
    - .NET adatgyűjtési és -osztályok
    - C# kifejezések
    - Beépített C# felhasználó által megadott függvények, udo-k és UDAAGs 
    - U-SQL-funkciók
    - U-SQL Ablakozó függvény
 
    ![IntelliSense objektumtípusok](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   **A Data Lake Analytics metaadatok IntelliSense autocomplete**: Data Lake Tools tölt le helyileg a Data Lake Analytics metaadatait. Az IntelliSense funkció automatikusan tölti fel az objektumok a Data Lake Analytics-metaadatok. Ezek az objektumok közé tartozik az adatbázis, séma, tábla, nézet, táblázat értékű függvényben, eljárások és C#-szerelvények.
 
    ![IntelliSense metaadatok](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   **IntelliSense hiba jelölő**: Data Lake Tools szerkesztési hibák kiemeli a U-SQL és C#. 
-   **Szintaxis emeli ki**: Data Lake Tools színek használja például a változók, kulcsszavakat, az adattípusokat és funkciók megkülönböztetéséhez. 

    ![Szintaxis használatával különböző színek](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Azt javasoljuk, hogy frissítsen Azure Data Lake Tools for Visual Studio verziója 2.3.3000.4 vagy újabb. A korábbi verziók már nincs elérhető letöltésre és mára elavult.  
   
## <a name="next-steps"></a>További lépések
- [U-SQL Python, R és C fejlesztése az Azure Data Lake Analytics Visual STUDIO Code az éles](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL helyi futtatásával és a helyi hibakeresése a Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Oktatóanyag: Ismerkedés az Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Oktatóanyag: U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
