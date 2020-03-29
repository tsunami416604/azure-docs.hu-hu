---
title: A Visual Studio Code-hoz készült Azure Data Lake-eszközök használata
description: Ismerje meg, hogyan hozhat létre, tesztelhatossa és futtathat U-SQL-parancsfájlokat az Azure Data Lake Tools for Visual Studio Code használatával.
services: data-lake-analytics
ms.service: data-lake-analytics
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 5042d89f1cb5e928444e4b3c9a23db7bb1d66585
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60509097"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>A Visual Studio Code-hoz készült Azure Data Lake-eszközök használata

Ebből a cikkből megtudhatja, hogyan használhatja az Azure Data Lake Tools for Visual Studio Code (VS Code) segítségével U-SQL-parancsfájlok létrehozásához, teszteléséhez és futtatásához. Az információk a következő videóban is szerepelnek:

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Előfeltételek

Az Azure Data Lake Tools for VS Code támogatja a Windows, Linux és macOS rendszert.Az U-SQL helyi futtatása és a helyi hibakeresés csak a Windows rendszerben működik.

- [Visual Studio kód](https://www.visualstudio.com/products/code-vs.aspx)

MacOS és Linux esetén:
- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Monó 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Az Azure Data Lake tools telepítése

Az előfeltételek telepítése után telepítheti az Azure Data Lake Tools for VS Code.After you install the prerequisites, you can install Azure Data Lake Tools for VS Code.

**Az Azure Data Lake tools telepítése**

1. Nyissa meg a Visual Studio Code-ot.
2. A bal oldali ablaktáblában válassza a **Bővítmények** lehetőséget. Írja be az **Azure Data Lake Tools** kifejezést a keresőmezőbe.
3. Válassza a **Telepítés** lehetőséget az **Azure Data Lake Tools mellett.** 

   ![A Data Lake Tools telepítéséhez szükséges beállítások](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Néhány másodperc múlva a **Telepítés** gomb **újratöltésre**változik.
4. Válassza **az Újratöltés** lehetőséget az **Azure Data Lake Tools** bővítmény aktiválásához.
5. A megerősítéshez válassza az **Ablak újratöltése** lehetőséget. Az **Azure Data Lake Tools** a **Bővítmények** ablaktáblában látható.

 
## <a name="activate-azure-data-lake-tools"></a>Az Azure Data Lake eszközeinek aktiválása
Hozzon létre egy .usql fájlt, vagy nyisson meg egy meglévő .usql fájlt a kiterjesztés aktiválásához. 


## <a name="work-with-u-sql"></a>Az U-SQL megoldás

Az U-SQL működéséhez meg kell nyitnia egy U-SQL fájlt vagy egy mappát.

**A mintaparancsfájl megnyitása**

Nyissa meg a parancspalettát (Ctrl+Shift+P), és írja be az **ADL: Open Sample Script parancsot**. A minta egy másik példányát nyitja meg. Parancsfájlt is szerkeszthet, konfigurálhat és küldhet el ebben a példányban.

**Mappa megnyitása az U-SQL projekthez**

1. A Visual Studio-kód ban válassza a **Fájl menüT,** majd a **Mappa megnyitása parancsot.**
2. Adjon meg egy mappát, majd válassza **a Mappa kijelölése**lehetőséget.
3. Válassza a **Fájl** menü Taborját, majd az **Új parancsot.** A projekthez egy Névtelen-1 fájl kerül.
4. Írja be a következő kódot az Untitled-1 fájlba:

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

    A parancsfájl létrehoz egy departments.csv fájlt a /output mappában található néhány adattal.

5. Mentse a fájlt **myUSQL.usql néven** a megnyitott mappába.

**U-SQL parancsfájl fordítása**

1. A parancspaletta megnyitásához válassza a Ctrl+Shift+P billentyűkombinációt. 
2. Adja meg **aDL: Fordítás Script**. A fordítási eredmények a **Kimenet** ablakban jelennek meg. A jobb gombbal egy parancsfájlra is kattinthat, majd az **ADL: Compile Script parancsra** kattintva lefordíthatja az U-SQL-feladatot. A fordításeredménye megjelenik a **Kimenet** ablaktáblán.
 
**U-SQL parancsfájl elküldése**

1. A parancspaletta megnyitásához válassza a Ctrl+Shift+P billentyűkombinációt. 
2. Adja meg **az ADL: Submit Job**. A jobb gombbal egy parancsfájlra is kattinthat, majd válassza **az ADL: Feladat beküldése parancsot.** 

Az U-SQL-feladat elküldése után a beküldési naplók megjelennek a **VS-kód Kimenet ablakában.** A feladatnézet a jobb oldali ablaktáblában jelenik meg. Ha a küldés sikeres, a feladat URL-címe is megjelenik. A feladat URL-címét egy webböngészőben megnyithatja a valós idejű feladat állapotának nyomon követéséhez. 

A feladatnézet **ÖSSZEGZÉS** lapján megtekintheti a feladat részleteit. A fő funkciók közé tartozik a parancsfájl újraküldése, a parancsfájl másolása és a portálon megnyíló. A feladatnézet **DATA** lapján hivatkozhat a bemeneti fájlokra, a kimeneti fájlokra és az erőforrásfájlokra. A fájlok letölthetők a helyi számítógépre.

![Összegzés lap a feladatnézetben](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Adatok lap a feladatnézetben](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**Az alapértelmezett környezet beállítása**

Beállíthatja, hogy az alapértelmezett környezet az összes parancsfájlra alkalmazza ezt a beállítást, ha nem állította be külön-külön a fájlok paramétereit.

1. A parancspaletta megnyitásához válassza a Ctrl+Shift+P billentyűkombinációt. 
2. Adja meg **az ADL: Alapértelmezett környezet beállítása**beállítást. Vagy kattintson a jobb gombbal a parancsfájlszerkesztőre, és válassza az **ADL: Alapértelmezett környezet beállítása parancsot.**
3. Válassza ki a kívánt fiókot, adatbázist és sémát. A beállítás a xxx_settings.json konfigurációs fájljába kerül.

   ![Fiók, adatbázis és séma beállítása alapértelmezett környezetként](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**Parancsfájl-paraméterek beállítása**

1. A parancspaletta megnyitásához válassza a Ctrl+Shift+P billentyűkombinációt. 
2. Adja meg **az ADL: Script-paraméterek beállítása**.
3. A xxx_settings.json fájl a következő tulajdonságokkal nyílik meg:

   - **fiók:** Azure Data Lake Analytics-fiók az Azure-előfizetésében, amely az U-SQL-feladatok összeállításához és futtatásához szükséges. Az U-SQL-feladatok fordítása és futtatása előtt konfigurálnia kell a számítógépfiókot.
   - **adatbázis**: A fiók alatt található adatbázis. Az alapértelmezett érték a **master**.
   - **séma**: Az adatbázis alatti séma. Az alapértelmezett érték **dbo**.
   - **optionalSettings**:
        - **prioritás**: A prioritási tartomány 1 és 1000 között van, az 1 pedig a legmagasabb prioritás. Az alapértelmezett érték **1000**.
        - **degreeOfParallelism**: A párhuzamosság tartománya 1 és 150 között van. Az alapértelmezett érték az Azure Data Lake Analytics-fiókjában engedélyezett maximális párhuzamosság. 

   ![A JSON-fájl tartalma](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
> [!NOTE] 
> A konfiguráció mentése után a fiókra, az adatbázisra és a sémaadatokra vonatkozó adatok megjelennek a megfelelő .usql fájl bal alsó sarkában lévő állapotsoron, ha nincs beállítva alapértelmezett környezet.

**Git-mellőzés beállítása**

1. A parancspaletta megnyitásához válassza a Ctrl+Shift+P billentyűkombinációt. 
2. Adja meg **az ADL: Állítsa git figyelmen kívül hagyása**.

   - Ha a VS Code munkamappájában nincs **.gitIgnore** fájl, a mappában létrejön egy **.gitIgnore** nevű fájl. Alapértelmezés szerint négy elem **(usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) kerül a fájlba. Szükség esetén további frissítéseket is elérhet.
   - Ha már van **.gitIgnore** fájl a VS Code munkamappában, az eszköz négy elemet **(usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) ad hozzá a **.gitIgnore** fájlhoz, ha a négy elem nem szerepel a fájlban.

   ![A .gitIgnore fájl elemei](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)


## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Kód-mögötti fájlok: C Sharp, Python és R

Az Azure Data Lake Tools több egyéni kódot is támogat. További információt az [U-SQL fejlesztése Python, R és C Sharp használatával az Azure Data Lake Analytics szolgáltatáshoz a VS-kódban című témakörben talál.](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)

## <a name="work-with-assemblies"></a>Összeállítások kal való munka

Az összeállítások fejlesztéséről az [U-SQL-szerelvények fejlesztése az Azure Data Lake Analytics-feladatokhoz című témakörben](data-lake-analytics-u-sql-develop-assemblies.md)talál.

A Data Lake Tools segítségével egyéni kódszerelvényeket regisztrálhat a Data Lake Analytics katalógusban.

**Összeállítás regisztrálása**

A szerelvényt az **ADL: Register Assembly** vagy **ADL: Register Assembly (Advanced)** paranccsal regisztrálhatja.

**Regisztráció az ADL-en keresztül: Assembly regisztráció parancs**
1.  A parancspaletta megnyitásához válassza a Ctrl+Shift+P billentyűkombinációt.
2.  Adja meg **az ADL: Register Assembly**. 
3.  Adja meg a helyi szerelvény elérési útját. 
4.  Válasszon egy Data Lake Analytics-fiókot.
5.  Jelöljön ki egy adatbázist.

A portál böngészőben nyílik meg, és megjeleníti az összeállítás regisztrációs folyamatát.  

Az **ADL: Assembly** parancs aktiválásának kényelmesebb módja, ha a jobb gombbal a .dll fájlra kattint a Fájlkezelőben. 

**Regisztráció az ADL- en keresztül: Assembly (Advanced) regisztrációja parancs**
1.  A parancspaletta megnyitásához válassza a Ctrl+Shift+P billentyűkombinációt.
2.  Adja meg **az ADL: Register Assembly (Advanced)** értéket. 
3.  Adja meg a helyi szerelvény elérési útját. 
4.  Megjelenik a JSON-fájl. Szükség esetén tekintse át és szerkesztse az összeállítási függőségeket és az erőforrás-paramétereket. Az utasítások a **Kimenet** ablakban jelennek meg. Az összeállítási regisztrációhoz mentse a JSON-fájlt (Ctrl+S).

    ![JSON-fájl összeállítási függőségekkel és erőforrás-paraméterekkel](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
>[!NOTE]
>- Az Azure Data Lake Tools automatikusan észleli, hogy a DLL rendelkezik-e összeállítási függőségekkel. A függőségek a JSON-fájlban jelennek meg, miután észlelték őket. 
>- A DLL-erőforrásokat (például .txt, .png és .csv) feltöltheti az összeállítási regisztráció részeként. 

**Az ADL: Assembly (Advanced)** parancs aktiválásának másik módja, ha a jobb gombbal a .dll fájlra kattint a Fájlkezelőben. 

A következő U-SQL-kód bemutatja, hogyan kell hívni egy szerelvényt. A mintában a szerelvény neve *a teszt*.


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


## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>U-SQL helyi futtatás és helyi hibakeresés használata Windows-felhasználók számára
Az U-SQL helyi futtatása teszteli a helyi adatokat, és helyileg ellenőrzi a parancsfájlt, mielőtt a kódot közzétené a Data Lake Analytics szolgáltatásban. A helyi hibakeresési funkcióval a következő feladatokat végezheti el, mielőtt a kódot elküldené a Data Lake Analytics szolgáltatásnak: 
- Hibakeresés a C# kód mögött. 
- Lépj át a kódon. 
- Érvényesítse a parancsfájlt helyileg.

A helyi futtatási és helyi hibakeresési szolgáltatás csak Windows-környezetekben működik, és macOS és Linux alapú operációs rendszereken nem támogatott.

A helyi futtatással és a helyi hibakereséssel kapcsolatos tudnivalókat az [U-SQL helyi futtatása és a Visual Studio-kóddal kapcsolatos helyi hibakeresés című témakörben találja.](data-lake-tools-for-vscode-local-run-and-debug.md)


## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Mielőtt lefordíthatja és futtathatja az U-SQL-parancsfájlokat a Data Lake Analytics szolgáltatásban, csatlakoznia kell az Azure-fiókjához.

<b id="sign-in-by-command">Csatlakozás az Azure-hoz parancs használatával</b>

1.  A parancspaletta megnyitásához válassza a Ctrl+Shift+P billentyűkombinációt. 
2.  Adja meg **a ADL: Bejelentkezés**. A bejelentkezési adatok a jobb alsó sarokban jelennek meg.

    ![A bejelentkezési parancs megadása](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Értesítés a bejelentkezésről és a hitelesítésről](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  A bejelentkezési weblap megnyitásához válassza **& Megnyitás** [másolása lehetőséget.](https://aka.ms/devicelogin) Illessze be a kódot a mezőbe, majd válassza a **Folytatás gombot.**

    ![Bejelentkezési weblap](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  
     
4.  Kövesse az utasításokat a weboldalról való bejelentkezéshez. Amikor csatlakozik, az Azure-fiók neve megjelenik az állapotsorban a VS-kód ablak bal alsó sarkában. 

> [!NOTE] 
>- A Data Lake Tools automatikusan bejelentkezik a következő alkalommal, ha nem jelentkezik ki.
>- Ha fiókjában két tényező van engedélyezve, azt javasoljuk, hogy a PIN-kód helyett a telefonhitelesítést használja.


A kijelentkezéshez írja be az **ADL: Kijelentkezés**parancsot.

**Csatlakozás az Azure-hoz az intézőből**

Bontsa ki **az AZURE DATALAKE**csomópontot, válassza **a Bejelentkezés az Azure-ba**lehetőséget, majd kövesse a Csatlakozás parancs [3.](#sign-in-by-command)

!["Bejelentkezés az Azure-ba" beállítás a felfedezőben](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Nem jelentkezhet ki a felfedezőtől. A kijelentkezéshez olvassa el a [Csatlakozás az Azure-hoz parancs használatával .](#sign-in-by-command)


## <a name="create-an-extraction-script"></a>Kivonási parancsfájl létrehozása 
Létrehozhat egy kivonási parancsfájlt .csv, .tsv és .txt fájlokhoz az **ADL: Extract Script létrehozása** vagy az Azure Data Lake explorer használatával.

**Kivonási parancsfájl létrehozása parancs használatával**

1. A parancspaletta megnyitásához válassza a Ctrl+Shift+P billentyűkombinációt, majd írja be az **ADL: Extract script létrehozása parancsot**.
2. Adja meg egy Azure Storage-fájl teljes elérési útját, és válassza az Enter kulcsot.
3. Válasszon ki egy fiókot.
4. .txt fájl esetén jelöljön ki egy határolójelet a fájl kibontásához. 

![Kivonási parancsfájl létrehozásának folyamata](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

A kibontási parancsfájl a bejegyzések alapján jön létre. Olyan parancsfájl esetén, amely nem ismeri fel az oszlopokat, válasszon egyet a két lehetőség közül. Ha nem, csak egy parancsfájl jön létre.

![Kivonási parancsfájl létrehozásának eredménye](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**Kivonási parancsfájl létrehozása a felfedezőtől**

A kibontási parancsfájl létrehozásának másik módja a .csv, .tsv vagy .tsv fájl jobb gombbal (helyi) menüje az Azure Data Lake Store vagy az Azure Blob storage.csv, .tsv vagy .txt fájlon keresztül.

!["EXTRACT script létrehozása" parancs a helyi menüből](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrálható az Azure Data Lake Analytics szolgáltatással egy paranccsal

Az Azure Data Lake Analytics-erőforrásokhoz hozzáférést biztosítanak a fiókok listázásához, a metaadatok eléréséhez és az elemzési feladatok megtekintéséhez. 

**Az Azure Data Lake Analytics-fiókok listázása az Azure-előfizetésben**

1. A parancspaletta megnyitásához válassza a Ctrl+Shift+P billentyűkombinációt.
2. Adja meg **az ADL: List Accounts**. A fiókok a **Kimenet** ablaktáblán jelennek meg.

**Az Azure Data Lake Analytics metaadatainak elérése**

1.  Jelölje be a Ctrl+Shift+P lehetőséget, majd írja be **az ADL: Listatáblázatok parancsot**.
2.  Válassza ki a Data Lake Analytics-fiókok egyikét.
3.  Válassza ki a Data Lake Analytics-adatbázisok egyikét.
4.  Válassza ki az egyik sémát. Megtekintheti a táblák listáját.

**Az Azure Data Lake Analytics-feladatok megtekintése**
1.  Nyissa meg a parancspalettát (Ctrl+Shift+P), és válassza az **ADL: Feladatok megjelenítése**lehetőséget. 
2.  Válasszon egy Data Lake Analytics vagy helyi fiókot. 
3.  Várja meg, amíg megjelenik a feladatlista a fiókhoz.
4.  Válasszon ki egy feladatot a feladatlistából. A Data Lake Tools megnyitja a feladatnézetet a jobb oldali ablaktáblában, és néhány információt megjelenít a VS Code kimenetben.

    ![Feladatlista](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrálás az Azure Data Lake Store-ral egy paranccsal

Az Azure Data Lake Store-hoz kapcsolódó parancsokat a következőkre használhatja:
 - [Böngésszen az Azure Data Lake Store erőforrásai között](#list-the-storage-path) 
 - [Az Azure Data Lake Store-fájl előnézetének megtekintése](#preview-the-storage-file) 
 - Töltse fel a fájlt közvetlenül az Azure Data Lake Store-ba a VS Code-ban
 - Töltse le a fájlt közvetlenül az Azure Data Lake Store-ból a VS Code-ban

### <a name="list-the-storage-path"></a>A tárolási útvonal felsorolása 

**A tárolási útvonal listázása a parancspalettán keresztül**

1. Kattintson a jobb gombbal a parancsfájlszerkesztőre, és válassza az **ADL: List Path parancsot.**
2. Válassza ki a mappát a listában, vagy válassza az **Elérési út megadása** vagy a **Tallózás a gyökérelérési útból**lehetőséget. (Példaként az **Elérési út megadása.)** 
3. Válassza ki a Data Lake Analytics-fiókját.
4. Tallózással keresse meg vagy írja be a tárolómappa elérési útját (például /output/).  

A parancspaletta a bejegyzések alapján sorolja fel a görbeadatokat.

![Tárolási útvonal eredményei](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

A relatív elérési út listázásának kényelmesebb módja a helyi menü.

**A tárolási útvonal listázása a helyi menün keresztül**

Kattintson a jobb gombbal a görbekarakterláncra, és válassza **a Görbe listázása parancsot.**

!["Görbe listázása" a helyi menüben](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>A tárolófájl előnézetének megtekintése

1. Kattintson a jobb gombbal a parancsfájlszerkesztőre, és válassza az **ADL: Fájl megtekintése parancsot.**
2. Válassza ki a Data Lake Analytics-fiókját. 
3. Adjon meg egy Azure Storage-fájl elérési útját (például /output/SearchLog.txt). 

A fájl a VS-kódban nyílik meg.

![A tárolófájl előnézetének lépései és eredménye](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

A fájl előnézetének másik módja a fájl teljes elérési útjának helyi menüje vagy a fájl relatív elérési útja a parancsfájlszerkesztőben. 

### <a name="upload-a-file-or-folder"></a>Fájl vagy mappa feltöltése

1. Kattintson a jobb gombbal a parancsfájlszerkesztőre, és válassza **a Fájl feltöltése** vagy **mappa feltöltése parancsot.**
2. Ha a **Fájl feltöltése**lehetőséget választotta, válasszon egy vagy több fájlt, ha a teljes mappát választotta, ha a **Mappa feltöltése**lehetőséget választotta. Ezután válassza a **Feltöltés** lehetőséget. 
3. Válassza ki a listában a tárolómappát, vagy válassza az **Elérési út megadása** vagy a **Tallózás a gyökérelérési útból**lehetőséget. (Példaként az **Elérési út megadása.)** 
4. Válassza ki a Data Lake Analytics-fiókját. 
5. Tallózással keresse meg vagy írja be a tárolómappa elérési útját (például /output/). 
6. Válassza **az Aktuális mappa kiválasztása lehetőséget** a feltöltési cél megadásához.

![Fájl vagy mappa feltöltésének lépései és eredménye](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    

A fájlok tárhelyre való feltöltésének másik módja a fájl teljes elérési útjának helyi menüje vagy a fájl relatív elérési útja a parancsfájlszerkesztőben.

[Figyelemmel kísérheti a feltöltés állapotát.](#check-storage-tasks-status)


### <a name="download-a-file"></a>Fájl letöltése 
A fájlokat az **ADL: Download File** vagy **ADL: Download File (Advanced)** paranccsal töltheti le.

**Fájl letöltése az ADL: Fájl letöltése (speciális) parancson keresztül**
1. Kattintson a jobb gombbal a parancsfájlszerkesztőre, és válassza **a Fájl letöltése (Speciális) parancsot.**
2. A VS Code jsonfájlt jelenít meg. Egyszerre beírhatja a fájlelérési utakat, és egyszerre több fájlt is letölthet. Az utasítások a **Kimenet** ablakban jelennek meg. A fájl vagy fájlok letöltéséhez mentse (Ctrl+S) a JSON-fájlt.

    ![JSON-fájl fájlletöltési elérési utakkal](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

A **Kimenet** ablakban megjelenik a fájl letöltési állapota.

![Kimeneti ablak letöltési állapottal](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

[Figyelemmel kísérheti a letöltés állapotát.](#check-storage-tasks-status)

**Fájl letöltése az ADL: Fájl letöltése parancson keresztül**

1. Kattintson a jobb gombbal a parancsfájlszerkesztőre, válassza a **Fájl letöltése**parancsot, majd a **Mappa kiválasztása** párbeszédpanelen jelölje ki a célmappát.
2. Válassza ki a mappát a listában, vagy válassza az **Elérési út megadása** vagy a **Tallózás a gyökérelérési útból**lehetőséget. (Példaként az **Elérési út megadása.)** 
3. Válassza ki a Data Lake Analytics-fiókját. 
4. Tallózással keresse meg vagy írja be a tárolómappa elérési útját (például /output/), majd válasszon egy letöltandó fájlt.

![A fájl letöltésének lépései és eredménye](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

A tárolófájlok letöltésének másik módja a fájl teljes elérési útjának helyi menüje vagy a fájl relatív elérési útja a parancsfájlszerkesztőben.

[Figyelemmel kísérheti a letöltés állapotát.](#check-storage-tasks-status)

### <a name="check-storage-tasks-status"></a>A tárolási feladatok állapotának ellenőrzése
A feltöltési és letöltési állapot megjelenik az állapotsoron. Jelölje ki az állapotsort, majd az állapot jelenjen meg a **KIMENET** lapon.

![Állapotsor és kimeneti adatok](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integrálás az Azure Data Lake Analytics szolgáltatással az explorertől

Miután bejelentkezett, az Azure-fiókjához való összes előfizetés a bal oldali ablaktáblában, az **AZURE DATALAKE**csoportban jelenik meg. 

![Data Lake felfedező](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Data Lake Analytics metaadat-navigáció

Bővítse Kibővítését Azure-előfizetésével. Az **U-SQL adatbázisok** csomópont alatt tallózhat az U-SQL adatbázisban, és megtekintheti az olyan mappákat, mint **a Sémák,** **hitelesítő adatok,** **szerelvények**, **táblák**és **index.**

### <a name="data-lake-analytics-metadata-entity-management"></a>Data Lake Analytics metaadat-entitás kezelése

Bontsa ki **az U-SQL adatbázisok csomópontot.** Adatbázist, sémát, táblát, táblatípust, indexet vagy statisztikát úgy hozhat létre, hogy a jobb gombbal a megfelelő csomópontra kattint, majd a helyi menüben a **Létrehozandó parancsfájl parancsot** választja. A megnyitott parancsfájl lapon az igényeinek megfelelően szerkesztheti a parancsfájlt. Ezután küldje el a feladatot a jobb gombbal, és válassza az **ADL: Feladat beküldése parancsot.** 

Miután befejezte az elem létrehozását, kattintson a jobb gombbal a csomópontra, majd válassza a **Frissítés** parancsot az elem megjelenítéséhez. Az elemet úgy is törölheti, hogy a jobb gombbal rá kattint, majd a **Törlés parancsot választja.**

!["Létrehozandó parancsfájl" parancs a Data Lake-felfedező helyi menüjében](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Az új elem parancsfájllapja](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>A Data Lake Analytics összeállításának regisztrációja

A megfelelő adatbázisban úgy regisztrálhat egy kódösszeállítást, hogy a jobb gombbal a **Szerelvények** csomópontra kattint, majd a **Regiszterszerelvény parancsot**választja.

!["Szerelvény regisztrálása" parancs az Assemblies csomópont helyi menüjében](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integrálás az Azure Data Lake Store-val az explorerből

Tallózás a **Data Lake Store-ban:**

- Kattintson a jobb gombbal a mappacsomópontra, majd használja a helyi menü **Frissítés**, **Törlés**, **Feltöltés,** **Mappa feltöltése,** **Relatív elérési út másolása**és **Teljes elérési út másolása** parancsát.

   ![A Data Lake-kezelő mappacsomópontjának helyi menüparancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Kattintson a jobb gombbal a fájlcsomópontra, majd használja a helyi menü **Előnézet**, **Letöltés**, **Törlés**, **Extract Script létrehozása** (csak CSV-, TSV- és TXT-fájlok esetén érhető el), A relatív elérési út **másolása**és a **Teljes elérési út másolása** parancshasználatát.

   ![Fájlcsomópont helyi menüparancsai a Data Lake-kezelőben](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integrálás az Azure Blob-tárterülettel az explorerből

Tallózással keresse meg a Blob-tárolót:

- Kattintson a jobb gombbal a blobtároló csomópontjára, majd használja a **Frissítés**, **a Blob-tároló törlése**és a **Blob-parancsok feltöltése** parancsokat a helyi menüben.

   ![Blob storage alatti blobtároló-csomópont helyi menüparancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Kattintson a jobb gombbal a mappacsomópontra, majd használja a helyi menü **Blob frissítése** és **feltöltése** parancsát.

   ![A Blob storage mappacsomópont jának helyi menüparancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Kattintson a jobb gombbal a fájlcsomópontra, majd **Download**használja a helyi menü **Előnézet/Szerkesztés**, **Törlés**, Extract **Script létrehozása** (csak CSV-, TSV- és TXT-fájlok esetén érhető el) parancsát, a Relatív elérési út **másolása**és a **Teljes elérési út másolása** parancsot.

    ![A Blob storage fájlcsomópontjának helyi menüparancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>A Data Lake-felfedező megnyitása a portálon
1. A parancspaletta megnyitásához válassza a Ctrl+Shift+P billentyűkombinációt.
2. Írja be **a Web Azure Storage Explorer megnyitása** parancsot, vagy kattintson a jobb gombbal egy relatív útvonalra vagy a teljes elérési útra a parancsfájlszerkesztőben, majd válassza a Web Azure Storage Explorer megnyitása **parancsot.**
3. Válasszon egy Data Lake Analytics-fiókot.

A Data Lake Tools megnyitja az Azure Storage elérési útját az Azure Portalon. Megkeresheti az elérési utat, és megtekintheti a fájl előnézetét az internetről.

## <a name="additional-features"></a>További funkciók

A Data Lake Tools for VS Code a következő funkciókat támogatja:

-   **IntelliSense automatikus kiegészítés**: Javaslatok jelennek meg a pop-up ablakok körül elemek, mint a kulcsszavak, módszerek és változók. A különböző ikonok különböző típusú objektumokat jelölnek:

    - Scala-adattípus
    - Összetett adattípus
    - Beépített UDT-k
    - .NET gyűjtés és osztályok
    - C# kifejezések
    - Beépített C# UDF-ek, UDOs-ek és UDAAG-ok 
    - U-SQL függvények
    - U-SQL ablakos függvények
 
    ![IntelliSense objektumtípusok](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   **Az IntelliSense automatikus kiegészítése a Data Lake Analytics metaadatairól**: A Data Lake Tools helyileg tölti le a Data Lake Analytics metaadatait. Az IntelliSense funkció automatikusan feltölti a Data Lake Analytics metaadataiból származó objektumokat. Ezek az objektumok közé tartozik az adatbázis, séma, tábla, nézet, tábla értékű függvény, eljárások és C# szerelvények.
 
    ![Az IntelliSense metaadatai](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   **IntelliSense hibajelölő**: A Data Lake Tools kiemeli az U-SQL és a C# szerkesztési hibáit. 
-   **Szintaxis kiemelések**: A Data Lake Tools színeket használ az olyan elemek megkülönböztetésére, mint a változók, kulcsszavak, adattípusok és függvények. 

    ![Szintaxis különböző színekkel](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Azt javasoljuk, hogy frissítsen az Azure Data Lake Tools for Visual Studio 2.3.3000.4-es vagy újabb verziójára. A korábbi verziók már nem tölthetők le, és mostantól elavultnak számítanak.  
   
## <a name="next-steps"></a>További lépések
- [Az U-SQL fejlesztése Python, R és C Sharp segítségével az Azure Data Lake Analytics szolgáltatáshoz a VS-kódban](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL helyi futtatás és helyi hibakeresés a Visual Studio-kóddal](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Oktatóanyag: Ismerkedés az Azure Data Lake Analytics szolgáltatással](data-lake-analytics-get-started-portal.md)
- [Oktatóanyag: U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
