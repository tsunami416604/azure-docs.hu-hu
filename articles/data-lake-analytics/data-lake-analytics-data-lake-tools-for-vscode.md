---
title: Használja az Azure Data Lake Tools for Visual Studio Code
description: Ismerje meg, hogyan használhatja az Azure Data Lake Tools for Visual Studio Code létrehozása, tesztelése és futtatása a U-SQL-parancsfájlok.
services: data-lake-analytics
ms.service: data-lake-analytics
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 5ebd543000c8927f714e5345dfd8eb6033c6301a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820368"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Használja az Azure Data Lake Tools for Visual Studio Code

Ebből a cikkből megtudhatja, hogyan használja az Azure Data Lake Tools for Visual Studio Code (a VS Code) hozhat létre, tesztelheti, és futtassa a U-SQL-parancsfájlok. Az alábbi videó az adatokat is tartalmazza:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Előfeltételek

Az Azure Data Lake Tools for VS Code támogatja a Windows, Linux és MacOS rendszeren. U-SQL helyi futtatási és helyi hibakeresési csak Windows működik.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

MacOS és Linux:
- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](http://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Az Azure Data Lake Tools telepítése

Miután telepítette az előfeltételeket, az Azure Data Lake Tools VS Code is telepítheti.

**Az Azure Data Lake Tools telepítése**

1. Nyissa meg a Visual Studio Code-ot.
2. Válassza ki **bővítmények** a bal oldali panelen. Adja meg **Azure Data Lake Tools** kifejezést a keresőmezőbe.
3. Válassza ki **telepítése** melletti **Azure Data Lake Tools**. 

   ![A Data Lake Tools telepítése szolgáló kiválasztások](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Néhány másodperc elteltével a **telepítése** gomb felirata **Újrabetöltés**.
4. Válassza ki **Újrabetöltés** aktiválása a **Azure Data Lake Tools** bővítmény.
5. Válassza ki **Reload Window** megerősítéséhez. Látható **Azure Data Lake Tools** a a **bővítmények** ablaktáblán.

 
## <a name="activate-azure-data-lake-tools"></a>Az Azure Data Lake Tools aktiválása
Hozzon létre egy .usql fájlt, vagy aktiválása a bővítmény létező .usql fájl megnyitása. 


## <a name="work-with-u-sql"></a>U-SQL használata

Dolgozunk a U-SQL, egy U-SQL-fájl vagy mappa kell megnyitni.

**A minta parancsfájl megnyitása**

Nyissa meg a parancskatalógust (Ctrl + Shift + P), és adja meg **ADL: Nyissa meg a minta parancsfájl**. Ez a minta egy másik példánya nyílik meg. Szerkesztése, konfigurálása, és küldje el a szkriptet ezen a példányon.

**A U-SQL projekt mappa megnyitása**

1. A Visual Studio Code-ban válassza ki a **fájl** menüt, és válassza ki **mappa megnyitása**.
2. Adjon meg egy mappát, és válassza ki **mappa kiválasztása**.
3. Válassza ki a **fájl** menüt, és válassza ki **új**. A projekt egy névtelen-1 fájl kerül.
4. A névtelen-1 fájlban adja meg a következő kódot:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    A szkript létrehoz egy departments.csv fájlt a/output mappában szereplő adatokkal.

5. Mentse a fájlt az **myUSQL.usql** megnyitott mappában.

**A U-SQL parancsfájl összeállítása**

1. Válassza ki a Ctrl + Shift + P a parancskatalógus megnyitásához. 
2. Adja meg **ADL: Parancsfájl összeállítása**. A fordítási eredményei jelennek meg a **kimeneti** ablak. A jobb gombbal is rákattinthat egy parancsfájlt, és válassza **ADL: Parancsfájl összeállítása** összeállítása egy U-SQL-feladatot. A fordítási eredmény pedig megjelenik a **kimeneti** ablaktáblán.
 
**A U-SQL parancsfájl elküldése**

1. Válassza ki a Ctrl + Shift + P a parancskatalógus megnyitásához. 
2. Adja meg **ADL: Feladat elküldése**. A jobb gombbal is rákattinthat egy parancsfájlt, és válassza **ADL: Feladat elküldése**. 

Miután elküldött egy U-SQL-feladatot, a beküldési naplók megjelennek a **kimeneti** ablak a VS Code-ban. A feladat nézet jelenik meg, a jobb oldali ablaktáblán. Ha a küldés sikeres, a feladat URL-címe túl jelenik meg. Megnyithatja a feladat URL-CÍMÉT egy webböngészőben, valós idejű feldolgozás állapotának nyomon követését. 

A feladat nézet **összefoglalás** lapon láthatja a feladat részleteit. Fő funkciók közé tartozik küldje el újra egy szkript, parancsfájl ismétlődő és nyissa meg a portálon. A feladat nézet **adatok** lapon olvassa el a bemeneti fájlokat, a kimeneti fájlok és az erőforrások fájljainak. Fájlok a helyi számítógépen lehet letölteni.

![A feladatok nézetben összefoglaló lap](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Az adatok lapon a feladatok nézetben](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Az alapértelmezett környezet beállítása**

Az alapértelmezett környezet, az összes parancsfájl-fájlra alkalmazza ezt a beállítást, ha a fájlok paraméterek nem állított be külön-külön is beállíthatja.

1. Válassza ki a Ctrl + Shift + P a parancskatalógus megnyitásához. 
2. Adja meg **ADL: Állítsa be az alapértelmezett környezet**. Vagy kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza a **ADL: Állítsa be az alapértelmezett környezet**.
3. Válassza ki a fiókot, adatbázist és sémát. A beállítás a rendszer menti a xxx_settings.json konfigurációs fájl.

   ![Fiók, adatbázis és séma állítja be az alapértelmezett környezet](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Parancsfájl-paraméterek beállítása**

1. Válassza ki a Ctrl + Shift + P a parancskatalógus megnyitásához. 
2. Adja meg **ADL: Állítsa be a parancsfájl-paraméterek**.
3. A xxx_settings.json fájl megnyitása a következő tulajdonságokkal:

   - **Fiók**: Egy Azure Data Lake Analytics-fiók az Azure-előfizetéshez fordításához és a U-SQL-feladatok futtatásához szükséges. Fordítsa le és U-SQL-feladatok futtatása előtt konfigurálnia kell a számítógépfiókot.
   - **Adatbázis**: A fiók alatt egy adatbázist. Az alapértelmezett érték **fő**.
   - **Séma**: Az adatbázis a sémát. Az alapértelmezett érték **dbo**.
   - **optionalSettings**:
        - **Prioritás**: A prioritástartomány 1000, az 1. a legmagasabb prioritás az 1. Az alapértelmezett érték **1000**.
        - **degreeOfParallelism**: A párhuzamosság tartománya 1-től 150. Az alapértelmezett érték a maximális párhuzamossági az Azure Data Lake Analytics-fiókban engedélyezve. 

   ![A JSON-fájl tartalmát](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
> [!NOTE] 
> A konfiguráció mentéséhez, miután a fiókot, adatbázist és adatbázisséma-információk jelennek meg a megfelelő .usql fájl a bal alsó sarkában található állapotsoron Ha nincs beállítva alapértelmezett környezet.

**A Git beállítása figyelmen kívül hagyása**

1. Válassza ki a Ctrl + Shift + P a parancskatalógus megnyitásához. 
2. Adja meg **ADL: Set Git figyelmen kívül hagyása**.

   - Ha nem rendelkezik egy **.gitIgnore** fájlt a VS Code munkamappában egy fájlt **.gitIgnore** hoznak létre a mappában. Négy elem (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) alapértelmezés szerint a fájlt ad hozzá. További frissítések teheti meg, ha szükséges.
   - Ha már rendelkezik egy **.gitIgnore** a VS Code munkamappában, az eszköz fájl hozzáadja a négy elem (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) az a **.gitIgnore** fájlt, ha a fájl nem szereplő négy elemeket.

   ![A .gitIgnore fájlt elemek](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Háttérkód-fájlok használata: C éles, Python és az R

Az Azure Data Lake Tools támogatja a több egyéni kódokat. Útmutatásért lásd: [fejlesztése U-SQL Python, R és az Azure Data Lake Analytics a VS Code-ban a C Sharp](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Szerelvények használata

Szerelvények fejlesztéséről további információért lásd: [Azure Data Lake Analytics-feladatok fejlesztése U-SQL-szerelvények](data-lake-analytics-u-sql-develop-assemblies.md).

A Data Lake Tools segítségével regisztrálja a szerelvények egyéni kód a Data Lake Analytics-katalógusban.

**Egy szerelvény regisztrációja**

A szerelvény segítségével regisztrálhatja a **ADL: Zaregistrovat Sestavení** vagy **ADL: Zaregistrovat Sestavení (speciális)** parancsot.

**Regisztráció az ADL keresztül: Szerelvény parancs regisztrálása**
1.  Válassza ki a Ctrl + Shift + P a parancskatalógus megnyitásához.
2.  Adja meg **ADL: Zaregistrovat Sestavení**. 
3.  Adja meg a helyi szerelvény elérési útja. 
4.  Válassza ki a Data Lake Analytics-fiók.
5.  Válasszon ki egy adatbázist.

A portálon a böngészőben megnyílik, és megjeleníti a szerelvény regisztrációs folyamat.  

Több kényelmesen aktiválása a **ADL: Zaregistrovat Sestavení** parancsot, hogy kattintson a jobb gombbal a .dll fájlt a Fájlkezelőben. 

**Regisztráció az ADL keresztül: Sestavení (speciális) parancs regisztrálása**
1.  Válassza ki a Ctrl + Shift + P a parancskatalógus megnyitásához.
2.  Adja meg **ADL: Zaregistrovat Sestavení (speciális)**. 
3.  Adja meg a helyi szerelvény elérési útja. 
4.  A JSON-fájl jelenik meg. Tekintse át, és szükség esetén szerkessze a szerelvény függőségi viszonyairól és erőforrás-paraméterek. Jelennek útmutató a **kimeneti** ablak. Folytassa a szerelvényregisztráló, mentse a JSON-fájl (Ctrl + S).

    ![Szerelvény függőségeket és erőforrás-paramétereket tartalmazó JSON-fájl](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
>[!NOTE]
>- Az Azure Data Lake-eszközök egy a DLL-je rendelkezik-e szerelvény függőségek. A függőségeket, hogy észlelése után jelennek meg a JSON-fájlt. 
>- A DLL-erőforrások (például .txt, .png és .csv) tölthet fel a szerelvényregisztráló részeként. 

Egy másik módon aktiválhat a **ADL: Zaregistrovat Sestavení (speciális)** parancsot, hogy kattintson a jobb gombbal a .dll fájlt a Fájlkezelőben. 

A következő U-SQL kód bemutatja, hogyan hívhat meg egy szerelvényt. A mintában a szerelvény neve: *tesztelése*.


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


## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Windows felhasználók esetén használja a U-SQL helyi Futtatás és hibakeresés
U-SQL helyi futtatásakor a helyi adatok teszteli, és a szkriptet helyileg ellenőrzi, mielőtt a kód közzé van téve, a Data Lake Analytics az. A helyi hibakeresési funkció segítségével a következő feladatok végezhetők, mielőtt a kód elküldve a Data Lake Analytics: 
- Hibakeresés a C# háttérkód. 
- Végighaladhat a kódot. 
- Ellenőrizze a parancsfájl helyi.

A helyi Futtatás és helyi hibakeresési funkció csak Windows-környezetben működik, és a macOS és Linux-alapú operációs rendszerek nem támogatott.

Helyi Futtatás és hibakeresés kapcsolatos utasításokért lásd: [U-SQL helyi futtatása és a hibakeresés a Visual Studio Code-dal](data-lake-tools-for-vscode-local-run-and-debug.md).


## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Fordítsa le, és a Data Lake Analytics U-SQL-szkriptek futtatása előtt kell kapcsolódnia az Azure-fiókjával.

<b id="sign-in-by-command">Csatlakozás az Azure-bA egy parancs használatával</b>

1.  Válassza ki a Ctrl + Shift + P a parancskatalógus megnyitásához. 
2.  Adja meg **ADL: Bejelentkezési**. A bejelentkezési adatok jobb alsó jelenik meg.

    ![Írja be a bejelentkezési parancsot](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Bejelentkezés és hitelesítés szóló értesítés küldése](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  Válassza ki **nyissa meg a & másolása** megnyitásához a [bejelentkezési weblapra](https://aka.ms/devicelogin). Illessze be a kódot, és válassza ki **Folytatás**.

    ![Bejelentkezési weblapra](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  
     
4.  Kövesse az utasításokat követve jelentkezzen be a weblapra a. Ha csatlakozott, az Azure-fiók neve megjelenik a VS Code-ablak bal alsó sarkában található állapotsoron. 

> [!NOTE] 
>- A Data Lake Tools automatikusan bejelentkezik, a következő alkalommal, ha nem jelentkezik.
>- Ha a fiók engedélyezve van a két tényező, azt javasoljuk, hogy használ-e az PIN-kód helyett telefonos hitelesítés.


Jelentkezzen ki, adja meg a parancs **ADL: Kijelentkezési**.

**Az Explorer csatlakozni az Azure-bA**

Bontsa ki a **AZURE-DATALAKE**, jelölje be **jelentkezzen be Azure**, majd hajtsa végre a 3. lépés és 4. lépés a [parancs használatával csatlakozik az Azure-bA](#sign-in-by-command).

![Az Explorer "Jelentkezzen be az Azure-bA" kiválasztása](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Az Explorer nem kijelentkezés. Jelentkezzen ki, lásd: [parancs használatával csatlakozik az Azure-bA](#sign-in-by-command).


## <a name="create-an-extraction-script"></a>Egy kinyerési parancsfájl létrehozása 
A parancs segítségével hozhat létre egy .txt, .csv és .tsv fájlok kibontása parancsfájl **ADL: Bontsa ki a parancsfájl létrehozása** vagy az Azure Data Lake Explorer.

**A parancs segítségével egy kinyerési parancsprogram létrehozásához**

1. Válassza ki a parancskatalógus megnyitásához, és adja meg a Ctrl + Shift + P **ADL: KINYERÉSI parancsfájl létrehozása**.
2. Adjon meg egy Azure Storage-fájl elérési útját, és az Enter billentyűt.
3. Válasszon egy fiókot.
4. Bontsa ki a fájlt elválasztó karakter kiválasztása a .txt fájl esetén. 

![Egy kinyerési parancsfájl létrehozásának folyamata](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

A kinyerési parancsfájl jön létre a megadott beállítások alapján. Egy parancsfájlt, amely nem észleli az oszlopok válassza ki a két lehetőség közül. Ha nem, csak egy parancsprogram jön létre.

![Eredménye egy kinyerési parancsfájl létrehozása](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**Az Explorer egy kinyerési parancsfájl létrehozása**

Egy másik létrehozása a kibontási parancsfájl módja a kattintson a jobb gombbal (helyi) menüben a .csv, .tsv vagy az Azure Data Lake Store vagy az Azure Blob storage-ban a .txt fájlt.

!["Az EXTRACT parancsfájl létrehozása" parancsot a helyi menü](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrálása az Azure Data Lake Analytics egy paranccsal

Fiókok listázása, a hozzáférés metaadatokat és az analytics-feladatok megtekintése az Azure Data Lake Analytics erőforrásokat érheti el. 

**Az Azure Data Lake Analytics-fiókok alatt az Azure-előfizetés listázásához**

1. Válassza ki a Ctrl + Shift + P a parancskatalógus megnyitásához.
2. Adja meg **ADL: Fiókok listázása**. A fiókok megjelennek a **kimeneti** ablaktáblán.

**Az Azure Data Lake Analytics-metaadatok eléréséhez**

1.  Válassza ki a Ctrl + Shift + P billentyűkombináció lenyomásával, és adja meg **ADL: Táblák listázása**.
2.  Válassza ki a Data Lake Analytics-fiókok egyikét.
3.  Válassza ki a Data Lake Analytics-adatbázisok egyikét.
4.  Válasszon ki egy a sémák. A táblák listáját láthatja.

**Az Azure Data Lake Analytics-feladatok megtekintése**
1.  Nyissa meg a parancskatalógust (Ctrl + Shift + P), és válassza ki **ADL: Feladatok megjelenítése**. 
2.  Válasszon ki egy Data Lake Analytics vagy helyi fiók. 
3.  Várjon, amíg a feladatlistában, megjelenik a fiókhoz.
4.  Válasszon ki egy feladatot a feladatot a listából. A Data Lake Tools a feladatok nézetének megnyitása. a jobb oldali ablaktáblán, és jelenik meg néhány információt a VS Code-kimenetben.

    ![Feladatlista](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>A parancs az Azure Data Lake Store integrálása

Használhatja az Azure Data Lake Store-kapcsolatos parancsok:
 - [Az Azure Data Lake Store-erőforrások tallózása](#list-the-storage-path) 
 - [Az Azure Data Lake Store-fájl megtekintése](#preview-the-storage-file) 
 - Töltse fel a fájlt közvetlenül az Azure Data Lake Store a VS Code-ban
 - Töltse le a fájlt közvetlenül az Azure Data Lake Store a VS Code-ban

### <a name="list-the-storage-path"></a>A tároló elérési útja listázása 

**A lista a parancskatalógus keresztül elérési útja**

1. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza ki **ADL: Elérési út listában**.
2. A listában válassza ki a mappát, vagy válasszon **adjon meg egy elérési utat** vagy **, Útvonalgyökér Tallózás**. (Használunk **adjon meg egy elérési utat** példaként.) 
3. Válassza ki a Data Lake Analytics-fiókját.
4. Keresse meg, vagy adja meg a mappa elérési útjának (például/output /).  

A parancskatalógus sorolja fel az elérési útra vonatkozó információt a bejegyzések alapján.

![Tároló elérési útja eredmények](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

A kényelmesebb listája a következő relatív elérési módja a helyi menüből.

**A lista a helyi menün keresztül elérési útja**

Kattintson a jobb gombbal az útvonal-karakterlánc, és válassza ki **lista elérési útját**.

!["Elérési út list" a helyi menü](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>A storage-fájl megtekintése

1. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza ki **ADL: Előzetes verzió fájl**.
2. Válassza ki a Data Lake Analytics-fiókját. 
3. Adjon meg egy Azure Storage elérési útja (például /output/SearchLog.txt). 

A VS Code-ban nyílik meg.

![Lépéseket és a storage-fájl előnézete eredménye](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

A fájl előnézetének egy másik módja, a helyi menüben a fájl teljes elérési útja vagy a fájl relatív elérési út a parancsfájl-szerkesztőben. 

### <a name="upload-a-file-or-folder"></a>Egy fájl vagy mappa feltöltése

1. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza ki **fájl feltöltése** vagy **mappa feltöltése**.
2. Válassza ki egy vagy több fájl, ha a kiválasztott **fájl feltöltése**, vagy válassza ki a teljes mappát, ha a kiválasztott **mappa feltöltése**. Válassza ki **feltöltése**. 
3. A tároló mappát válasszon a listából, vagy válasszon **adjon meg egy elérési utat** vagy **, Útvonalgyökér Tallózás**. (Használunk **adjon meg egy elérési utat** példaként.) 
4. Válassza ki a Data Lake Analytics-fiókját. 
5. Keresse meg, vagy adja meg a mappa elérési útjának (például/output /). 
6. Válassza ki **válassza ki az aktuális mappa** a feltöltési célhelyének megadásához.

![Lépéseket és a egy fájl vagy mappa feltöltése eredménye](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    

Fájlok feltöltése a storage egy másik módja, a helyi menüben a fájl teljes elérési útja vagy a fájl relatív elérési út a parancsfájl-szerkesztőben.

Is [figyelheti a feltöltési állapotot](#check-storage-tasks-status).


### <a name="download-a-file"></a>Fájl letöltése 
A parancs segítségével letölthető egy fájl **ADL: Fájl letöltése** vagy **ADL: Töltse le a fájlt (speciális)**.

**Az ADL keresztül fájlok letöltéséhez: Töltse le a fájlt (speciális) parancsot.**
1. Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza **fájl letöltése (speciális)**.
2. A VS Code jeleníti meg egy JSON-fájlt. Adja meg a fájlok elérési útja, és egyszerre több fájl letöltéséhez. Jelennek útmutató a **kimeneti** ablak. A fájl vagy fájlok letöltése a folytatáshoz mentése (Ctrl + S) a JSON-fájlt.

    ![Fájl letöltése JSON-fájljának elérési úttal](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

A **kimeneti** ablak fájl letöltése állapotát jeleníti meg.

![A letöltés állapota a kimeneti ablak](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Is [a letöltés állapotának figyelése](#check-storage-tasks-status).

**Az ADL keresztül fájlok letöltéséhez: Töltse le a fájl parancs**

1. Kattintson a jobb gombbal a parancsprogram-szerkesztő, jelölje be **fájl letöltése**, majd válassza ki a célmappát, a **mappa kiválasztása** párbeszédpanel bezárásához.
2. A listában válassza ki a mappát, vagy válasszon **adjon meg egy elérési utat** vagy **, Útvonalgyökér Tallózás**. (Használunk **adjon meg egy elérési utat** példaként.) 
3. Válassza ki a Data Lake Analytics-fiókját. 
4. Keresse meg vagy írja be a mappa elérési útjának (például/output /), és válassza a fájl letöltéséhez.

![Lépéseket és a egy fájl letöltése eredménye](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

Másik módja, hogy a storage-fájlok letöltése a helyi menüben a fájl teljes elérési útja vagy a fájl relatív elérési út a parancsfájl-szerkesztőben keresztül történik.

Is [a letöltés állapotának figyelése](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Tárolási feladatok állapotának ellenőrzése
A feltöltési és letöltési állapot jelenik meg az állapotsorban. Válassza ki az állapotsor, és állapota megjelenik majd a **kimeneti** fülre.

![Állapotsor és kimenetében](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Az Azure Data Lake Analytics integrálja az Explorer

Miután bejelentkezett, az Azure-fiók összes előfizetés alatt szerepelnek a bal oldali panelen **AZURE-DATALAKE**. 

![A Data Lake Explorerrel](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>A Data Lake Analytics metaadat-navigáció

Bontsa ki az Azure-előfizetésében. Alatt a **U-SQL-adatbázisok** csomópont, akkor is böngészhet az U-SQL-adatbázis és mappák megtekintése, például **sémák**, **hitelesítő adatok**, **szerelvények**, **Táblák**, és **Index**.

### <a name="data-lake-analytics-metadata-entity-management"></a>A Data Lake Analytics metaadatok entitáskezelése

Bontsa ki a **U-SQL-adatbázisok**. Kattintson a jobb gombbal a megfelelő csomópont, és kiválasztja az létrehozhat egy adatbázist, séma, tábla, táblatípus, index vagy statisztika **parancsfájl létrehozása** a helyi menüben. Megnyitott script lapon szerkessze a szkriptet, szükség szerint. Ezután kattintson a jobb gombbal, majd válassza a a feladat elküldéséhez **ADL: Feladat elküldése**. 

A létrehozást követően a cikket, kattintson a jobb gombbal a csomópontra, majd **frissítése** megjelenítéséhez a cikk. Törölheti is az elemet, kattintson a jobb gombbal, és válassza a **törlése**.

!["Parancsfájl létrehozása" parancsot a helyi menüben a Data Lake Explorerben](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Az új konfigurációelem parancsfájl lapja](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>A Data Lake Analytics szerelvényregisztráló

Kattintson a jobb gombbal a megfelelő adatbázisban egy szerelvény is regisztrálnia a **szerelvények** csomópontot, és kiválasztja az **Zaregistrovat sestavení**.

!["Szerelvény regisztrálása" parancsot a helyi menüben a szerelvények csomópont](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Az Explorer az Azure Data Lake Store integrálása

Keresse meg a **Data Lake Store**:

- Kattintson a jobb gombbal a mappa csomópontot, és ezután a **frissítése**, **törlése**, **feltöltése**, **mappa feltöltése**, **másolása Relatív elérési út**, és **másolás teljes elérési útja** parancsokat a helyi menüben.

   ![Egy mappa a Data Lake Explorerrel csomópontján a helyi menü parancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Kattintson a jobb gombbal a fájl csomópontot, és ezután a **előzetes**, **letöltése**, **törlése**, **KINYERÉSE parancsfájl létrehozása** (csak a fürt megosztott kötetei szolgáltatás esetén érhető el TSV, és a TXT-fájlokat), **másolási relatív elérési út**, és **másolás teljes elérési útja** parancsokat a helyi menüben.

   ![A Data Lake Explorerrel fájl csomópontja a helyi menü parancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Az Azure Blob storage, az Explorer integrálása

Keresse meg a Blob storage:

- Kattintson a jobb gombbal a blob-tároló csomópontra, és ezután a **frissítése**, **Blob-tároló törlése**, és **Blob feltöltése** parancsokat a helyi menüben.

   ![Egy blob tároló csomópont alatt a Blob storage a helyi menü parancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Kattintson a jobb gombbal a mappa csomópontot, és ezután a **frissítése** és **Blob feltöltése** parancsokat a helyi menüben.

   ![Egy mappa csomópont alatt a Blob storage a helyi menü parancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Kattintson a jobb gombbal a fájl csomópontot, és ezután a **megtekintése/szerkesztése**, **letöltése**, **törlése**, **KINYERÉSE parancsfájl létrehozása** (elérhető csak a CSV, TSV és a TXT-fájlok) **másolási relatív elérési út**, és **másolás teljes elérési útja** a helyi menü parancsai.

    ![Egy fájl csomópont alatt a Blob storage a helyi menü parancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>A Data Lake Explorerrel nyissa meg a portálon
1. Válassza ki a Ctrl + Shift + P a parancskatalógus megnyitásához.
2. Adja meg **Open Web Azure Storage Explorer** , vagy kattintson a jobb gombbal egy relatív elérési utat vagy a parancsprogram-szerkesztő a teljes elérési útja, és válassza **Open Web Azure Storage Explorer**.
3. Válassza ki a Data Lake Analytics-fiók.

A Data Lake Tools megnyílik az Azure Storage elérési utat az Azure Portalon. Keresse meg az elérési utat, és megtekintheti a fájlt az internetről.

## <a name="additional-features"></a>További funkciók

A Data Lake Tools for VS Code a következő szolgáltatásokat támogatja:

-   **Az automatikus kiegészítés IntelliSense**: Javaslatok körül például a kulcsszavak, a metódusok és a változók előugró ablak jelenik meg. Különböző ikonjai a különböző típusú objektumokat:

    - Scala adattípus
    - Az összetett típus
    - Beépített alapján UDT-ket
    - .NET adatgyűjtési és -osztályok
    - C# kifejezések
    - Felhasználói beépített C#-függvények, udo-k és UDAAGs 
    - U-SQL-ben
    - U-SQL ablakkezelési függvényeket
 
    ![Az IntelliSense objektumtípusok](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   **Az automatikus kiegészítés a Data Lake Analytics-metaadatok IntelliSense**: A Data Lake Tools a Data Lake Analytics metaadatait helyileg tölti le. Az IntelliSense funkció automatikusan feltölti a Data Lake Analytics-metaadatok objektumait. Ezek az objektumok közé tartozik az adatbázis, séma, tábla, nézet, táblázat értékű függvényben, eljárások és C#-szerelvények.
 
    ![IntelliSense metadata](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   **Az IntelliSense hiba jelölő**: Data Lake Tools underlines editing errors for U-SQL and C#. 
-   **Szintaxis kiemelések**: A Data Lake Tools színeket használ megkülönböztetéséhez, például a változókat, kulcsszavakat, az adattípusok és funkciók. 

    ![A különböző színek szintaxis](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Azt javasoljuk, hogy frissítsen az Azure Data Lake Tools for Visual Studio 2.3.3000.4 verzió vagy újabb. A korábbi verziók már nem tölthetők le, és mostantól elavultnak számítanak.  
   
## <a name="next-steps"></a>További lépések
- [Fejlesztés a U-SQL, Python, R és a C Sharp az Azure Data Lake Analytics a VS Code-ban](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL helyi futtatása és a hibakeresés a Visual Studio Code-dal](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Oktatóanyag: Az Azure Data Lake Analytics használatának első lépései](data-lake-analytics-get-started-portal.md)
- [Oktatóanyag: U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
