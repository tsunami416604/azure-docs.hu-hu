---
title: A Visual Studio Code-hoz készült Azure Data Lake-eszközök használata
description: Ismerje meg, hogyan hozhatja létre, tesztelheti és futtathatja a U-SQL-parancsfájlokat a Azure Data Lake Tools for Visual Studio Code használatával.
services: data-lake-analytics
ms.service: data-lake-analytics
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: dd3db27160f8fcc0ad3135b0ab87921a636fd155
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565127"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>A Visual Studio Code-hoz készült Azure Data Lake-eszközök használata

Ebből a cikkből megtudhatja, hogyan használhatók a Visual Studio Code-hoz készült Azure Data Lake Tools (VS Code) a U-SQL-parancsfájlok létrehozásához, teszteléséhez és futtatásához. Az adatokat az alábbi videóban is tárgyaljuk:

[![Videolejátszó: Azure Data Lake eszközök a VS Code-hoz](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>Előfeltételek

A VS Code-hoz készült Azure Data Lake Tools támogatja a Windows, a Linux és a macOS rendszert.A U-SQL helyi Futtatás és a helyi hibakeresés csak Windows rendszerben működik.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

MacOS és Linux esetén:

- [.NET Core SDK 2,0](https://www.microsoft.com/net/download/core)
- [Monó 5.2. x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Azure Data Lake eszközök telepítése

Az Előfeltételek telepítése után telepítheti a VS Code Azure Data Lake eszközeit.

### <a name="to-install-azure-data-lake-tools"></a>Azure Data Lake eszközök telepítése

1. Nyissa meg a Visual Studio Code-ot.
2. A bal oldali ablaktáblán válassza a **bővítmények** lehetőséget. A keresőmezőbe írja be **Azure Data Lake eszközöket** .
3. Válassza a **telepítés** lehetőséget **Azure Data Lake eszközök**elem mellett.

   ![Data Lake eszközök telepítésének kiválasztása](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Néhány másodperc elteltével a **telepítés** gombra változik az **újratöltés**.
4. Az **Azure Data Lake-eszközök** bővítmény aktiválásához válassza az **Újratöltés** lehetőséget.
5. A megerősítéshez válassza az **újratöltési ablak** lehetőséget. A **bővítmények** ablaktáblán láthatók **Azure Data Lake eszközök** .

## <a name="activate-azure-data-lake-tools"></a>Azure Data Lake eszközök aktiválása

Hozzon létre egy. usql fájlt, vagy nyisson meg egy meglévő. usql fájlt a bővítmény aktiválásához.

## <a name="work-with-u-sql"></a>A U-SQL használata

A U-SQL-vel való együttműködéshez meg kell nyitnia egy U-SQL-fájlt vagy egy mappát.

### <a name="to-open-the-sample-script"></a>A minta parancsfájl megnyitása

Nyissa meg a parancssort (CTRL + SHIFT + P), és írja be az **ADL: Open minta szkriptet**. Ekkor megnyílik a minta egy másik példánya. Ezen a példányon szerkesztheti, konfigurálhatja és elküldheti a parancsfájlokat is.

### <a name="to-open-a-folder-for-your-u-sql-project"></a>Mappa megnyitása a U-SQL-projekthez

1. A Visual Studio Code-ból válassza a **fájl** menüt, majd a **mappa megnyitása**lehetőséget.
2. Adjon meg egy mappát, majd kattintson a **mappa kiválasztása**lehetőségre.
3. Válassza ki a **fájl** menüt, majd válassza az **új**lehetőséget. Egy névtelen-1 fájl van hozzáadva a projekthez.
4. Adja meg a következő kódot a névtelen-1 fájlban:

   ```usql
   @departments  =
       SELECT * FROM
           (VALUES
               (31,    "Sales"),
               (33,    "Engineering"),
               (34,    "Clerical"),
               (35,    "Marketing")
           ) AS
                 D( DepID, DepName );
   ```

   KIMENET @departments      a következőre: "/output/departments.csv" Outputters.Csv ();

    A szkript létrehoz egy departments.csv fájlt, amely tartalmaz néhány, a/output mappában található adattal.

5. Mentse a fájlt **myUSQL. usql** néven a megnyitott mappában.

### <a name="to-compile-a-u-sql-script"></a>U-SQL-szkript fordítása

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.
2. Adja meg az **ADL: szkript fordítását**. A fordítási eredmények a **kimenet** ablakban jelennek meg. Kattintson a jobb gombbal egy parancsfájlra, majd válassza az **ADL: szkript fordítása** egy U-SQL-feladatok fordításához lehetőséget. A fordítási eredmény megjelenik a **kimenet** ablaktáblán.

### <a name="to-submit-a-u-sql-script"></a>U-SQL-szkript elküldése

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.
2. Adja meg az **ADL: submit feladatot**. Azt is megteheti, hogy a jobb gombbal egy parancsfájlra kattint, majd kiválasztja az **ADL: elküldés feladatot**.

A U-SQL-feladatok elküldése után a küldési naplók a VS Code **kimenet** ablakában jelennek meg. A feladatok nézet a jobb oldali ablaktáblán jelenik meg. Ha a Küldés sikeres, a feladatokhoz tartozó URL-cím is megjelenik. A valós idejű feladatok állapotának nyomon követéséhez megnyithatja a feladatok URL-címét egy böngészőben.

A feladatok nézet **Összegzés** lapján láthatja a feladatok részleteit. A fő függvények közé tartozik például a szkript újraküldése, duplikált parancsfájl, és Megnyitás a portálon. A feladatok nézet **adatok** lapján a bemeneti fájlokra, a kimeneti fájlokra és az erőforrás-fájlokra is hivatkozhat. A fájlok a helyi számítógépre tölthetők le.

![A feladatok nézet összefoglalás lapja](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Adatok lap a feladatok nézetben](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>Az alapértelmezett környezet beállítása

Beállíthatja, hogy az alapértelmezett környezet alkalmazza ezt a beállítást az összes parancsfájl-fájlra, ha a fájlokhoz nem állított be paramétereket külön.

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.
2. Adja meg az **ADL: alapértelmezett környezet beállítása beállítást**. Vagy kattintson a jobb gombbal a parancsfájl-szerkesztőre, és válassza az **ADL: alapértelmezett környezet beállítása**lehetőséget.
3. Válassza ki a kívánt fiókot, adatbázist és sémát. A rendszer a konfigurációs fájl xxx_settings.jsmenti a beállítást.

   ![A fiók, az adatbázis és a séma beállítása alapértelmezett környezetként](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>Parancsfájl paramétereinek megadása

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.
2. Adja meg az **ADL: parancsfájl paramétereinek beállítása**értéket.
3. A fájl xxx_settings.jsmegnyílik a következő tulajdonságokkal:

   - **fiók**: az Azure-előfizetéshez tartozó Azure Data Lake Analytics fiók, amely a U-SQL-feladatok fordításához és futtatásához szükséges. Az U-SQL-feladatok fordítása és futtatása előtt konfigurálnia kell a számítógépfiókot.
   - **adatbázis**: a fiókja alá tartozó adatbázis. Az alapértelmezett érték a **Master**.
   - **séma**: az adatbázishoz tartozó séma. Az alapértelmezett érték a **dbo**.
   - **optionalSettings**:
        - **prioritás**: a prioritási tartomány 1 és 1000 közötti, a legmagasabb prioritással 1. Az alapértelmezett érték **1000**.
        - **analyticsunits**: a párhuzamossági tartomány 1 és 150 között van. Az alapértelmezett érték a Azure Data Lake Analytics-fiókban engedélyezett maximális párhuzamosság.

   ![A JSON-fájl tartalma](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)

> [!NOTE]
> A konfiguráció mentése után a fiók, az adatbázis és a séma adatai megjelennek az állapotsorban a megfelelő. usql fájl bal alsó sarkában, ha nincs beállítva alapértelmezett környezet.

### <a name="to-set-git-ignore"></a>A git mellőzésének beállítása

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.
2. Adja meg az **ADL: set git mellőzése**.

   - Ha a VS Code Working mappában nem található **. gitIgnore** -fájl, a **. gitIgnore** nevű fájl jön létre a mappában. A rendszer alapértelmezés szerint négy elemet (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **. cache**, **obj**) ad hozzá a fájlhoz. Szükség esetén további frissítéseket is megadhat.
   - Ha a VS Code Working mappában már van egy **. gitIgnore** -fájl, az eszköz négy elemet (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **. cache**, **obj**) helyez el a **. gitIgnore** fájlban, ha a fájl nem tartalmaz négy elemet.

   ![A. gitIgnore fájl elemei](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>A kód mögötti fájlok használata: C Sharp, Python és R

Azure Data Lake eszközök több egyéni kódot is támogatnak. Útmutatásért lásd: a [U-SQL fejlesztése a Python, az R és a C Sharp esetében Azure Data Lake Analytics a vs Code-ban](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Szerelvények használata

A szerelvények fejlesztésével kapcsolatos információkért lásd: [U-SQL-szerelvények fejlesztése Azure Data Lake Analytics feladatokhoz](data-lake-analytics-u-sql-develop-assemblies.md).

A Data Lake Analytics-katalógusban Data Lake eszközök használatával regisztrálhat egyéni kód-szerelvényeket.

### <a name="to-register-an-assembly"></a>Szerelvény regisztrálása

A szerelvényt az **ADL: register Assembly** vagy **ADL: register Assembly (Advanced)** parancs használatával regisztrálhatja.

### <a name="to-register-through-the-adl-register-assembly-command"></a>Az ADL: register Assembly parancs használatával regisztrálhat

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.
2. Adja meg az **ADL: Regisztráljon szerelvényt**.
3. A helyi szerelvény elérési útjának megadása.
4. Válasszon Data Lake Analytics fiókot.
5. Válasszon ki egy adatbázist.

A portál egy böngészőben van megnyitva, és megjeleníti a szerelvény regisztrációs folyamatát.  

Az ADL kiváltásának kényelmesebb módja **: az összeállítási parancs regisztrálása** a Fájlkezelőben kattintson a jobb gombbal a. dll fájlra.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>Az ADL: register Assembly (Advanced) parancs használatával történő regisztráció

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.
2. Adja meg az **ADL: Regisztráljon Assembly (speciális)**.
3. A helyi szerelvény elérési útjának megadása.
4. Megjelenik a JSON-fájl. Szükség esetén tekintse át és szerkessze a szerelvény-függőségeket és az erőforrás-paramétereket. Az utasítások a **kimenet** ablakban jelennek meg. A szerelvény regisztrációjának folytatásához mentse (CTRL + S) a JSON-fájlt.

   ![A JSON-fájl szerelvény-függőségekkel és erőforrás-paraméterekkel](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- Azure Data Lake-eszközök automatikusan felismeri, hogy a DLL-fájl rendelkezik-e szerelvény-függőségekkel. A függőségek az észlelés után a JSON-fájlban jelennek meg.
>- A DLL-erőforrásokat (például. txt,. png és. csv) a szerelvény regisztrációjának részeként töltheti fel.

Az ADL következő kiváltásának egy másik módja: a (z) **Register Assembly (speciális)** parancs, ha a jobb gombbal rákattint a. dll fájlra a fájlkezelőben.

A következő U-SQL-kód bemutatja, hogyan hívhat meg egy szerelvényt. A mintában a szerelvény neve *teszt*.

```usql
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
```

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>A U-SQL helyi futtatásának és helyi hibakeresésének használata Windows-felhasználók számára

A U-SQL helyi futtatása ellenőrzi a helyi adatait, és helyileg érvényesíti a szkriptet, mielőtt közzéteszi a programkódot a Data Lake Analytics. A helyi hibakeresési funkcióval a következő feladatokat hajthatja végre, mielőtt elküldte a kódot a Data Lake Analyticsba:

- A C#-kód hibakeresése.
- A kód átlépése.
- A parancsfájl helyi ellenőrzése.

A helyi Futtatás és a helyi hibakeresési funkció csak Windows-környezetekben működik, és macOS és Linux rendszerű operációs rendszereken nem támogatott.

A helyi futtatással és a helyi hibakereséssel kapcsolatos utasításokért lásd: [U-SQL helyi Futtatás és helyi hibakeresés a Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)-ban.

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Mielőtt lefordítja és futtatja a U-SQL-parancsfájlokat a Data Lake Analyticsban, csatlakoznia kell az Azure-fiókjához.

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>Kapcsolódás az Azure-hoz egy parancs használatával

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.

2. Írja be az **ADL: login nevet**. A bejelentkezési adatok a jobb alsó sarokban jelennek meg.

   ![A login parancs beírása](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![Értesítés a bejelentkezésről és a hitelesítésről](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. Válassza a **másolás & Megnyitás** lehetőséget a [bejelentkezési weblap](https://aka.ms/devicelogin)megnyitásához. Illessze be a kódot a mezőbe, majd kattintson a **Continue (folytatás**) gombra.

    ![Bejelentkezési weblap](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. Kövesse az utasításokat a weblapon való bejelentkezéshez. Amikor csatlakozik, az Azure-fiók neve megjelenik az állapotsorban a VS Code ablak bal alsó sarkában.

> [!NOTE]
>
> - Data Lake eszközök automatikusan aláírják Önt a következő alkalommal, ha nem kijelentkezik.
> - Ha a fiókja két tényezőt engedélyez, javasoljuk, hogy PIN-kód használata helyett telefonos hitelesítést használjon.

A kijelentkezéshez írja be az **ADL: kijelentkezés**parancsot.

### <a name="to-connect-to-azure-from-the-explorer"></a>Kapcsolódás az Azure-hoz az Intézőből

Bontsa ki az **Azure DATALAKE**, majd a **Bejelentkezés az Azure**-ba lehetőséget, majd kövesse a 3. lépést és a 4. lépését az [Azure-hoz való kapcsolódáshoz egy parancs használatával](#sign-in-by-command).

!["Bejelentkezés az Azure-ba" kijelölés az Explorerben](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Nem lehet kijelentkezni az Intézőből. A kijelentkezéshez lásd: az Azure-hoz való [Kapcsolódás parancs használatával](#sign-in-by-command).

## <a name="create-an-extraction-script"></a>Kinyerési parancsfájl létrehozása

A. csv, a. TSV és a. txt kiterjesztésű fájlok kinyerési parancsfájlját a következő paranccsal hozhatja létre **: a KInyerési parancsfájl létrehozása** vagy a Azure Data Lake Explorerben.

### <a name="to-create-an-extraction-script-by-using-a-command"></a>Kinyerési parancsfájl létrehozása parancs használatával

1. A CTRL + SHIFT + P billentyűkombinációval nyissa meg a parancssort, és írja be az **ADL: extract szkript létrehozása**elemet.
2. Adja meg az Azure Storage-fájl teljes elérési útját, és válassza az ENTER billentyűt.
3. Válasszon egy fiókot.
4. Egy. txt kiterjesztésű fájl esetén válasszon ki egy határolójelet a fájl kibontásához.

![Kinyerési parancsfájl létrehozási folyamata](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

A kinyerési parancsfájl a bejegyzései alapján jön létre. Egy olyan parancsfájl esetében, amely nem ismeri fel az oszlopokat, válasszon egyet a két lehetőség közül. Ha nem, csak egy parancsfájl lesz létrehozva.

![Kinyerési parancsfájl létrehozásának eredménye](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>Kinyerési parancsfájl létrehozása az Intézőből

A kinyerési parancsfájl létrehozásának egy másik módja a. csv,. TSV vagy. txt fájl helyi menüjében található, Azure Data Lake Store vagy az Azure Blob Storage-ban.

!["A kinyerési parancsfájl létrehozása" parancs a helyi menüből](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrálás Azure Data Lake Analytics paranccsal

Hozzáférés Azure Data Lake Analytics erőforrásokhoz a fiókok listázásához, a metaadatok eléréséhez és az elemzési feladatok megtekintéséhez.

### <a name="to-list-the-azure-data-lake-analytics-accounts-under-your-azure-subscription"></a>Az Azure-előfizetéshez tartozó Azure Data Lake Analytics fiókok listázása

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.
2. Adja meg az **ADL: fiókok listázása**. A fiókok megjelennek a **kimenet** ablaktáblán.

### <a name="to-access-azure-data-lake-analytics-metadata"></a>Azure Data Lake Analytics metaadatok elérése

1. Válassza a CTRL + SHIFT + P billentyűkombinációt, majd írja be az **ADL: List Tables**elemet.
2. Válassza ki a Data Lake Analytics fiókok egyikét.
3. Válassza ki a Data Lake Analytics adatbázisok egyikét.
4. Válassza ki az egyik sémát. Láthatja a táblák listáját.

### <a name="to-view-azure-data-lake-analytics-jobs"></a>Azure Data Lake Analytics feladatok megtekintése

1. Nyissa meg a parancssort (CTRL + SHIFT + P), és válassza az **ADL: feladatok megjelenítése**lehetőséget.
2. Válasszon Data Lake Analytics vagy helyi fiókot.
3. Várjon, amíg megjelenik a Feladatlista a fiókhoz.
4. Válasszon ki egy feladatot a feladatlistából. Data Lake eszközök megnyitják a feladatok nézetet a jobb oldali ablaktáblán, és a VS Code kimenetben jelenítenek meg néhány információt.

    ![Feladatlisták](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrálás Azure Data Lake Store paranccsal

Azure Data Lake Store kapcsolódó parancsok a következőhöz használhatók:

- [Tallózás a Azure Data Lake Store-erőforrások között](#list-the-storage-path)
- [A Azure Data Lake Store fájl előnézetének megtekintése](#preview-the-storage-file)
- Töltse fel a fájlt közvetlenül a VS Code Azure Data Lake Storeba
- A fájl letöltése közvetlenül Azure Data Lake Store a VS Code-ból

### <a name="list-the-storage-path"></a>A tárolási útvonal listázása

### <a name="to-list-the-storage-path-through-the-command-palette"></a>A tár elérési útjának listázása a parancs palettáján

1. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, és válassza az **ADL: lista elérési útja**lehetőséget.
2. Válassza ki a mappát a listában, vagy válassza az **elérési út megadása** vagy a **gyökér elérési útja alapján való Tallózás**lehetőséget. (A **megadott elérési utat** példaként használjuk.)
3. Válassza ki a Data Lake Analytics fiókját.
4. Tallózással keresse meg vagy adja meg a tárolási mappa elérési útját (például/output/).  

A parancs paletta felsorolja az elérési út adatait a bejegyzései alapján.

![Tárolási útvonal eredményei](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

A relatív elérési út kilistázásának kényelmesebb módja a helyi menün keresztül.

### <a name="to-list-the-storage-path-through-the-shortcut-menu"></a>A tár elérési útjának listázása a helyi menüben

Kattintson a jobb gombbal az elérési út sztringre, és válassza a **lista elérési útja**lehetőséget.

!["Lista elérési útja" a helyi menüben](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

### <a name="preview-the-storage-file"></a>A tárolási fájl előnézete

1. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, és válassza az **ADL: előnézet fájl**elemet.
2. Válassza ki a Data Lake Analytics fiókját.
3. Adja meg az Azure Storage-fájl elérési útját (például/output/SearchLog.txt).

A fájl a VS Code-ban nyílik meg.

![A tárolási fájl előzetes megtekintésének lépései és eredményei](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

A fájl előnézetének egy másik módja a fájl teljes elérési útjának helyi menüjében vagy a fájl relatív elérési útja a parancsfájl-szerkesztőben.

### <a name="upload-a-file-or-folder"></a>Fájl vagy mappa feltöltése

1. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, és válassza a **fájl feltöltése** vagy a **feltöltés mappa**lehetőséget.
2. Ha a **fájl feltöltése**lehetőséget választotta, válasszon egy fájlt vagy több fájlt, vagy válassza a teljes mappát, ha a **mappa feltöltése**lehetőséget választotta. Ezután válassza a **Feltöltés** lehetőséget.
3. Válassza ki a Storage mappát a listában, vagy válassza az **elérési út megadása** vagy a **gyökér elérési útja alapján való Tallózás**lehetőséget. (A **megadott elérési utat** példaként használjuk.)
4. Válassza ki a Data Lake Analytics fiókját.
5. Tallózással keresse meg vagy adja meg a tárolási mappa elérési útját (például/output/).
6. A feltöltés célhelyének megadásához válassza az **aktuális mappa kiválasztása** lehetőséget.

![Fájlok vagy mappák feltöltésének lépései és eredményei](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)

A fájlok tárterületre való feltöltésének másik módja a fájl teljes elérési útjának helyi menüjében vagy a fájl relatív elérési útja a parancsfájl-szerkesztőben.

Nyomon követheti [a feltöltés állapotát](#check-storage-tasks-status).

### <a name="download-a-file"></a>Fájl letöltése

A fájl letöltéséhez használja az **ADL: fájl letöltése** vagy **ADL: fájl letöltése (speciális)** parancsot.

### <a name="to-download-a-file-through-the-adl-download-file-advanced-command"></a>Fájl letöltése az ADL: fájl letöltése (speciális) parancs használatával

1. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, majd válassza a **Fájl letöltése (speciális)** lehetőséget.
2. A VS Code egy JSON-fájlt jelenít meg. Megadhatja a fájlelérési utakat, és egyszerre több fájlt is letölthet. Az utasítások a **kimenet** ablakban jelennek meg. A fájl vagy fájlok letöltésének folytatásához mentse (CTRL + S) a JSON-fájlt.

    ![Fájl letöltésére szolgáló elérési úttal rendelkező JSON-fájl](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

A **kimeneti** ablak a fájl letöltési állapotát jeleníti meg.

![Kimeneti ablak letöltési állapottal](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)

Nyomon követheti [a letöltési állapotot](#check-storage-tasks-status).

### <a name="to-download-a-file-through-the-adl-download-file-command"></a>Fájl letöltése az ADL: fájl letöltése paranccsal

1. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, válassza a **Fájl letöltése**parancsot, majd a **mappa kiválasztása** párbeszédpanelen válassza ki a célmappát.

1. Válassza ki a mappát a listában, vagy válassza az **elérési út megadása** vagy a **gyökér elérési útja alapján való Tallózás**lehetőséget. (A **megadott elérési utat** példaként használjuk.)

1. Válassza ki a Data Lake Analytics fiókját.

1. Tallózással keresse meg vagy adja meg a tárolási mappa elérési útját (például/output/), majd válassza ki a letölteni kívánt fájlt.

![A fájlok letöltésének lépései és eredményei](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png)

A tárolási fájlok letöltésének egy másik módja a fájl teljes elérési útjának helyi menüjében vagy a fájl relatív elérési útja a parancsfájl-szerkesztőben.

Nyomon követheti [a letöltési állapotot](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Tárolási feladatok állapotának keresése

Megjelenik a feltöltés és a letöltés állapot az állapotsoron. Válassza ki az állapotsort, majd az állapot megjelenik a **kimenet** lapon.

![Állapotsor és kimenet részletei](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integrálás Azure Data Lake Analytics az Intézőből

A bejelentkezést követően az Azure-fiók összes előfizetése megjelenik a bal oldali panelen az **Azure DATALAKE**alatt.

![Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Data Lake Analytics metaadatok navigációja

Bővítse ki Azure-előfizetését. A **u-SQL-adatbázisok** csomópont alatt böngészhet a u-SQL-adatbázison, és megtekintheti a mappákat, például a **sémákat**, a **hitelesítő adatokat**, a **szerelvényeket**, a **táblákat**és az **indexeket**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Metaadat-entitások Data Lake Analytics kezelése

Bontsa ki az **U-SQL-adatbázisok**csomópontot. Létrehozhat egy adatbázist, sémát, táblázatot, táblát, típust, indexet vagy statisztikát úgy, hogy a jobb gombbal a megfelelő csomópontra kattint, majd kiválasztja a helyi menüben **létrehozni kívánt parancsfájlt** . A megnyitott parancsfájl lapon szerkessze a szkriptet az igényeinek megfelelően. Ezt követően küldje el a feladatot úgy, hogy a jobb gombbal rákattint, és kiválasztja az **ADL: submit feladatot**.

Miután befejezte az elem létrehozását, kattintson a jobb gombbal a csomópontra, majd válassza a **frissítés** lehetőséget az elem megjelenítéséhez. Az elemet úgy is törölheti, ha a jobb gombbal kattint, majd kiválasztja a **Törlés**lehetőséget.

!["Parancsfájl létrehozása" parancs a Data Lake Explorer helyi menüjében](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Az új elemmel kapcsolatos parancsfájl lapja](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Data Lake Analytics szerelvény regisztrációja

A megfelelő adatbázisban lévő szerelvények regisztrálásához kattintson a jobb gombbal a **szerelvények** csomópontra, majd válassza a **szerelvény regisztrálása**lehetőséget.

!["Assembly regisztrálása" parancs a szerelvények csomópont helyi menüjében](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integrálás Azure Data Lake Store az Intézőből

**Data Lake Store**keresése:

- Kattintson a jobb gombbal a mappa csomópontjára, majd a **frissítés**, a **Törlés**, a **feltöltés**, a **mappa feltöltése**, a **relatív elérési út másolása**és a **teljes elérésiút** -parancsok másolása parancsra a helyi menüben.

   ![A Data Lake Explorerben található mappa csomópontjának helyi menü parancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Kattintson a jobb gombbal a fájl csomópontra, majd használja az **előnézet**, a **Letöltés**, a **Törlés**, a **kinyerési parancsfájl létrehozása** (csak CSV-, TSV-és txt-fájlok esetén elérhető), a **relatív elérési út másolása**és a **teljes elérésiút** -parancsok másolása a helyi menüben.

   ![A Data Lake Explorerben található fájlhoz tartozó helyi menüparancsok](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integráció az Azure Blob Storage-nal az Intézőből

Tallózás a blob Storage-ban:

- Kattintson a jobb gombbal a blob-tároló csomópontra, majd használja a helyi menüben a **frissítés**, a **blob-tároló**és a **Blobok feltöltése** parancsot.

   ![BLOB-tároló csomópontjának helyi menü parancsai a blob Storage-ban](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Kattintson a jobb gombbal a mappa csomópontjára, majd használja a helyi menüben a **Blobok** **frissítése** és feltöltése parancsot.

   ![A blob Storage alatti mappa csomópontjának helyi menü parancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Kattintson a jobb gombbal a fájl csomópontra, majd használja az **előnézet/szerkesztés**, **Letöltés**, **Törlés**, **létrehozási parancsfájl** (csak CSV-, TSV-és txt-fájlok esetén elérhető), a **relatív elérési út másolása**és a **teljes elérésiút** -parancsok másolása a helyi menüben.

    ![A blob Storage alatt lévő fájl csomópontjának helyi menü parancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>A Data Lake Explorer megnyitása a portálon

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.
2. Adja meg a **nyílt webes Azure Storage Explorer** , vagy kattintson a jobb gombbal egy relatív elérési útra, vagy a parancsfájl-szerkesztő teljes elérési útjára, majd válassza a **webes Azure Storage Explorer megnyitása**lehetőséget.
3. Válasszon Data Lake Analytics fiókot.

Data Lake eszközök megnyitják az Azure Storage elérési útját a Azure Portalban. A fájl elérési útját és előnézetét a webről tekintheti meg.

## <a name="additional-features"></a>További funkciók

A VS Code-hoz készült Data Lake Tools a következő funkciókat támogatja:

- **IntelliSense automatikus kiegészítés**: a javaslatok az előugró ablakokban jelennek meg, például a kulcsszavakat, a metódusokat és a változókat. A különböző ikonok különböző típusú objektumokat jelölnek:

  - Scala adattípus
  - Összetett adattípus
  - Beépített UDT
  - .NET-gyűjtemény és-osztályok
  - C#-kifejezések
  - Beépített C# UDF, Udo és UDAAGs
  - U-SQL függvények
  - U-SQL-ablakkezelő függvények

    ![IntelliSense-objektumok típusai](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)

- **IntelliSense automatikus kiegészítése Data Lake Analytics metaadatokon**: Data Lake eszközök helyileg töltik le a Data Lake Analytics metaadat-információkat. Az IntelliSense szolgáltatás automatikusan kitölti az objektumokat a Data Lake Analytics metaadataiból. Ezek az objektumok a következők: adatbázis, séma, tábla, nézet, tábla értékű függvény, eljárások és C# szerelvények.

  ![IntelliSense-metaadatok](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

- **IntelliSense-hibajelző**: Data Lake eszközök az U-SQL és a C# szerkesztési hibáit aláhúzzák.
- A **szintaxis kiemeli**: Data Lake eszközök színeket használnak az elemek, például változók, kulcsszavak, adattípusok és függvények megkülönböztetéséhez.

    ![Szintaxis különböző színekkel](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Javasoljuk, hogy a Visual Studio 2.3.3000.4 vagy újabb verziójára frissítsen Azure Data Lake-eszközökre. A korábbi verziók már nem tölthetők le, és mostantól elavultnak számítanak.  

## <a name="next-steps"></a>További lépések

- [A U-SQL fejlesztése Python, R és C Sharp Azure Data Lake Analytics a VS Code-ban](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL helyi Futtatás és helyi hibakeresés a Visual Studio Code-ban](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Oktatóanyag: az Azure Data Lake Analytics első lépései](data-lake-analytics-get-started-portal.md)
- [Oktatóanyag: U-SQL-parancsfájlok fejlesztése a Visual studióhoz készült Data Lake Tools használatával](data-lake-analytics-data-lake-tools-get-started.md)
