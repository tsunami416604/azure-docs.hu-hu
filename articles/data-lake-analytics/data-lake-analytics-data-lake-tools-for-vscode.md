---
title: "Az Azure Data Lake Tools: Használata Azure Data Lake Tools Visual Studio Code |} Microsoft Docs"
description: "Ismerje meg, hogyan használható az Azure Data Lake Tools for Visual Studio Code létrehozása, tesztelése és U-SQL-parancsfájlok futtatása. "
Keywords: "VScode, az Azure Data Lake Tools, a helyi futtatáskor, helyi hibakeresése a helyi debug preview tárolófájl feltölteni. tárolási elérési útja, letöltése, feltöltése"
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
ms.date: 10/10/2017
ms.author: jejiang
ms.openlocfilehash: e724a8db4424a1e608ae7ee5625cd4cc16f6078f
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2017
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Az Azure Data Lake Tools használja a Visual Studio Code

Tudhat meg az Azure Data Lake Tools for Visual Studio (kód VS) hozhat létre, tesztelheti, és a U-SQL-parancsfájlok futtatása. Az adatokat is tartalmazza, az a következő videót:

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

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

5. Mentse a fájlt **myUSQL.usql** a megnyitott mappában. A projekt is kerül adltools_settings.json konfigurációs fájlt.
4. Nyissa meg, és adltools_settings.json konfigurálása a következő tulajdonságokkal:

    - Fiók: Egy Data Lake Analytics-fiók alatt az Azure-előfizetéshez.
    - Adatbázis: A fiók alatt adatbázis. Az alapértelmezett érték **fő**.
    - Séma: Az adatbázis a séma. Az alapértelmezett érték **dbo**.
    - Választható beállítások:
        - Prioritás: A prioritási tartománya: 1 – 1 és 1000 a legmagasabb prioritás. Az alapértelmezett érték **1000**.
        - Párhuzamossági: A párhuzamos végrehajtás tartomány: 1 – 150. Az alapértelmezett érték: az Azure Data Lake Analytics-fiók engedélyezett maximális párhuzamosságát. 
        
        > [!NOTE] 
        > Ha a beállítások érvénytelenek, a rendszer az alapértelmezett értékeket használ.

        ![A Data Lake Tools for Visual Studio Code konfigurációs fájl](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

        A Data Lake Analytics-fiók számítási fordításához és a U-SQL feladatok futtatása van szükség. A számítógépfiók kell konfigurálniuk, lefordítása és U-SQL feladatok futtatása előtt.
    
        A konfigurációs mentését követően a fiók, adatbázis és séma információ jelenik meg a megfelelő .usql fájlt bal alsó sarkában lévő állapotjelző sáv. 
 
 
A fájl megnyitásakor, amikor megnyit egy mappát is képest:

- A háttérkód fájl használható. Háttérkód egyfájlos módban nem támogatott.
- A konfigurációs fájlt használja. Ha a mappa megnyitása a működő mappában található parancsfájlok osztják meg egyetlen konfigurációs fájlban.


A U-SQL parancsfájl távolról lefordítja a Data Lake Analytics szolgáltatáson keresztül. Ha ki a **fordítási** parancsot a U-SQL parancsfájlt a rendszer elküldi a Data Lake Analytics-fiók. Később a Visual Studio Code megkapja a fordítási eredménye. A távoli fordítás miatt a Visual Studio Code van szükség, hogy a Data Lake Analytics-fiók a konfigurációs fájlban való kapcsolódáshoz információinak felsorolásához.

**A U-SQL parancsfájl összeállításához**

1. Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát. 
2. Adja meg **ADL: parancsfájl összeállításához**. A fordítás eredményei jelennek meg a **kimeneti** ablak. Jobb gombbal egy olyan parancsfájlt, és válassza **ADL: parancsfájl összeállításához** fordítási egy U-SQL-feladatot. A fordítási eredménye megjelenik az **kimeneti** ablaktáblán.
 

**A U-SQL parancsfájl elküldeni**

1. Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát. 
2. Adja meg **ADL: elküldeni a feladatot**.  Jobb gombbal egy olyan parancsfájlt, és válassza **ADL: feladat elküldése**. 

Miután elküldött egy U-SQL-feladatot, a küldésének naplók megjelennek a **kimeneti** ablak a Visual STUDIO-kódban. Ha a küldése sikeres, a feladat URL-címe valószínűleg is. A feladat URL-címet egy webböngészőben a valós idejű feladat állapotát nyomon nyithatja meg.

Ahhoz, hogy a feladat részleteit a kimenetét, állítsa be **jobInformationOutputPath** a a **vs helykódja a u-sql_settings.json** fájlt.
 
## <a name="use-a-code-behind-file"></a>A háttérkód fájl használata

A háttérkód fájlt egy U-SQL parancsfájl társított C# fájlban. Meghatározhatja egy dedikált parancsfájl UDO, uda-Értékeket, UDT vagy a háttérkód fájlban UDF. A UDO, uda-Értékeket, UDT és UDF használható közvetlenül a parancsfájlban a szerelvény először regisztrálása nélkül. A háttérkód fájl kerül, a mappában, amelyben a társviszony-létesítési U-SQL-parancsfájlt. Ha a parancsfájl xxx.usql neve, a háttérkód xxx.usql.cs, neve. Törölje kézzel a háttérkód fájlt, ha a háttérkód funkció le van tiltva, a társított U-SQL parancsfájlt. További információ a felhasználói kódot a U-SQL parancsfájl: [írási és egyéni kód használata U-SQL: felhasználó által definiált függvényeket]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

Támogatja a háttérkód, meg kell nyitnia egy munkamappa. 

**A háttérkód fájlt létrehozni**

1. A forrásfájl megnyitásakor. 
2. Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát.
3. Adja meg **ADL: mögött kód generálása**. Egy háttérkód fájl ugyanabban a mappában jön létre. 

Jobb gombbal egy olyan parancsfájlt, és válassza **ADL: kód generálása mögött**. 

Fordítási, és küldje el a U-SQL parancsfájl háttérkód fájlt tartalmazó nem azonos az önálló U-SQL parancsfájl fájllal.

A következő két képernyőképeket megjelenítése a háttérkód fájl- és a kapcsolódó U-SQL-parancsfájlt:
 
![A Data Lake Tools for Visual Studio Code háttérkód](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![A Data Lake Tools for Visual Studio Code háttérkód parancsfájl](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

Helyi futtatáskor és helyi hibakeresési támogatott, az utasításokat lásd: [U-SQL helyi futtatásával, és a Visual Studio Code helyi hibakeresési](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="use-assemblies"></a>Szerelvények használata

Szerelvények fejlesztéséről további információkért lásd: [fejlesztése U-SQL-szerelvények Azure Data Lake Analytics-feladatok](data-lake-analytics-u-sql-develop-assemblies.md).

A Data Lake Tools segítségével egyéni kód szerelvények regisztrálja a Data Lake Analytics-katalógusban.

**Egy szerelvény regisztrálása**

A szerelvény segítségével regisztrálhatja a **ADL: szerelvény regisztrálása** vagy **ADL: szerelvény regisztrálása konfigurálással** parancsok.

**Az ADL keresztül regisztrálni: szerelvény regisztrálása parancs**
1.  Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát.
2.  Adja meg **ADL: szerelvény regisztrálása**. 
3.  Adja meg a helyi szerelvény elérési útja. 
4.  Válassza ki a Data Lake Analytics-fiók.
5.  Válasszon ki egy adatbázist.

Eredmények: A portál egy böngészőben meg van nyitva, és megjeleníti a szerelvény regisztrációs folyamat során.  

Indítás, egy másik kényelmesen a **ADL: szerelvény regisztrálása** parancs: kattintson a jobb gombbal a .dll fájlt a Fájlkezelőben. 

**Regisztrálni, ha az ADL: szerelvény regisztrálása konfigurációs paranccsal**
1.  Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát.
2.  Adja meg **ADL: szerelvény konfigurálással regisztrálása**. 
3.  Adja meg a helyi szerelvény elérési útja. 
4.  A JSON-fájl jelenik meg. Tekintse át, és szükség esetén szerkessze a szerelvény függőségeit és erőforrás-paraméterek. Utasítások jelennek meg a **kimeneti** ablak. Folytassa a szerelvény regisztrációját, mentse a JSON-fájl (Ctrl + S).

![A Data Lake Tools for Visual Studio Code háttérkód](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
>[!NOTE]
>- Szerelvény függőségek: Azure Data Lake Tools egy DLL-re van-e függőségekről. A függőségek állapotúak után jelennek meg a JSON-fájl. 
>- Erőforrás: A DLL-erőforrások (például .txt, .png és .csv) feltöltheti a szerelvények regisztrálásakor részeként. 

Úgy is elindítható a **ADL: szerelvény regisztrálása konfigurálással** parancs: kattintson a jobb gombbal a .dll fájlt a Fájlkezelőben. 

A következő U-SQL kód bemutatja, hogyan hívhatja meg egy szerelvényt. A szerelvény neve: a minta *tesztelése*.

```
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

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Mielőtt fordítási, és a Data Lake Analytics U-SQL parancsfájlt, csatlakoznia kell az Azure-fiókjával.

**Csatlakozás az Azure-bA**

1.  Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát. 
2.  Adja meg **ADL: bejelentkezési**. Megjelenik a bejelentkezési adatok a **kimeneti** ablaktáblán.

    ![A Data Lake Visual Studio Code parancs paletta eszközei](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![a Data Lake Tools for Visual Studio Code eszköz bejelentkezési adatok](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3. Válassza ki a Ctrl + kattintson a bejelentkezési URL-címen: https://aka.ms/devicelogin megnyitni a bejelentkezési képernyőn látható weblapon. Írja be a kódot **G567LX42V** a szövegmezőbe, majd válassza ki azt a **Folytatás**.

   ![A Data Lake Tools for Visual Studio Code bejelentkezési illessze be a kódot](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  Kövesse a képernyőn látható weblapon a bejelentkezéshez. Ha kapcsolódik, az Azure-fiók neve megjelenik a bal alsó sarkában lévő állapotjelző sáv a **Visual STUDIO Code** ablak. 

    > [!NOTE] 
    > A fiókjának van engedélyezve, a két tényező, azt javasoljuk, hogy használja-e PIN-kóddal helyett phone hitelesítési.

Jelentkezzen ki, írja be a parancs **ADL: kijelentkezési**.

## <a name="list-your-data-lake-analytics-accounts"></a>A Data Lake Analytics-fiókok listázása

A kapcsolat teszteléséhez a Data Lake Analytics-fiókok listájának lekérése.

**A Data Lake Analytics-fiókok az Azure-előfizetéshez tartozó listázásához**

1. Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát.
2. Adja meg **ADL: fiókok listában**. A fiókok megjelennek a **kimeneti** ablaktáblán.


## <a name="access-the-data-lake-analytics-catalog"></a>A Data Lake Analytics-katalógus elérése

Miután csatlakozott az Azure-ba, az alábbi lépések segítségével hozzáférni a U-SQL catalog.

**Az Azure Data Lake Analytics metaadatok eléréséhez**

1.  Válassza ki a Ctrl + Shift + P, és írja be **ADL: listáját táblákat**.
2.  Válasszon egyet a Data Lake Analytics-fiókok.
3.  Válassza ki, egy Data Lake Analytics adatbázis.
4.  Válasszon ki egy séma. A táblák listáját láthatja.

## <a name="view-data-lake-analytics-jobs"></a>Nézet Data Lake Analytics-feladatok

**Data Lake Analytics-feladatok megtekintése**
1.  Nyissa meg a parancs paletta (Ctrl + Shift + P), és válassza ki **ADL: a feladat megjelenítése**. 
2.  Válasszon egy Data Lake Analytics vagy helyi fiók. 
3.  Várjon, amíg a feladatok listája jelenik meg a fiókhoz.
4.  Jelölje ki a feladatot feladatot a listából, Data Lake Tools nyílik meg a feladat részleteit az Azure-portálon, és a Feladatinformáció fájl és a kód jelenik meg.

    ![A Data Lake Tools for Visual Studio Code IntelliSense objektumtípusok](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-integration"></a>Azure Data Lake tároló integrációja

Az Azure Data Lake tárolással kapcsolatos parancsokat használhatja:
 - Tallózzon az Azure Data Lake tárolási erőforrások. [A tároló elérési útját listában](#list-the-storage-path). 
 - Tekintse meg az Azure Data Lake-tárolási fájl. [Tekintse meg a tárolófájl](#preview-the-storage-file). 
 - Feltölteni a fájlt közvetlenül az Azure Data Lake tárolási VS-kódban. [Fájl feltöltése](#upload-file).
 - Töltse le a fájlt közvetlenül az Azure Data Lake-tárolóból Visual STUDIO Code. [Fájl letöltése](#download-file).

## <a name="list-the-storage-path"></a>A tároló elérési útját listában 

**A tárolási elérési útja a parancs paletta keresztül listájára**

Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza ki **ADL: lista. tárolási elérési útja**.

A listában válassza ki a mappát, vagy kattintson a **elérési utat adjon meg** vagy **keresse meg a legfelső szintű** (használ adjon meg egy elérési utat példaként). -> Válassza ki a **ADLA fiók**. Navigálás ->, vagy adja meg a tároló mappa elérési útját (például: / kimeneti /). A parancs paletta listák -> az elérési úthoz adatok alapján.

![A Data Lake Tools for Visual Studio Code tárolási elérési útja eredmények felsorolása](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Több kényelmesen a relatív elérési útját listában kattintson a jobb gombbal a helyi menüből keresztül történik.

**Kattintson a jobb gombbal a tároló elérési úttal listájára**

Kattintson a jobb gombbal az elérési útvonal karakterláncának kiválasztásához **lista. tárolási elérési útja** folytatja.

![A Data Lake Tools for Visual Studio Code kattintson a jobb gombbal a helyi menü](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


## <a name="preview-the-storage-file"></a>A tárolófájl megtekintése

Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza ki **ADL: Preview tárolófájl**.

Válassza ki a **ADLA fiók**. Adjon meg egy Azure storage fájl elérési útját (például /output/SearchLog.txt) ->. Result ->: VSCode fájl megnyitása.

   ![A Data Lake Tools for Visual Studio Code előzetes fájl eredménye](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Egy másik preview tárolófájl módja a helyi menüben a fájl elérési útját vagy a fájl relatív elérési út a parancsfájl-szerkesztőben. 

## <a name="upload-file"></a>Fájl feltöltése 

Fájlok feltöltheti a parancsok beírásával **ADL: a fájl feltöltése** vagy **ADL: a konfigurációs fájl feltöltése**.

**A feltöltendő fájl, ha az ADL: konfigurációs paranccsal fájl feltöltése**
1.  Kattintson a jobb gombbal a parancsprogram-szerkesztő, majd válassza ki **fájl feltöltése konfigurálással**.
2.  Visual STUDIO Code jeleníti meg a JSON-fájl. Adja meg az elérési utat, és egyszerre több fájl feltöltése. Utasítások jelennek meg a **kimeneti** ablak. Folytatja a fájl feltöltéséhez, mentse a JSON-fájl (Ctrl + S).

       ![A Data Lake Tools for Visual Studio Code-fájl elérési útja](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-file.png)

3.  Eredmények: A **kimeneti** ablak a fájl feltöltési állapotot jeleníti meg.

       ![A Data Lake Tools for Visual Studio Code feltöltési állapot](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)     

Egy időben, figyelheti a [Feltöltés állapota](#check-storage-tasks-status).

**A feltöltendő fájl, ha az ADL: a fájl feltöltése parancs**

Kattintson a jobb gombbal a parancsprogram-szerkesztő, és válassza ki **fájl feltöltése**.

Adjon meg a **helyi fájl elérési útját**. -> Válassza ki a listán található, vagy kattintson a **elérési utat adjon meg** vagy **keresse meg a legfelső szintű** (használ adjon meg egy elérési utat példaként). -> Válassza ki a **ADLA fiók**. Navigálás ->, vagy adja meg a tároló mappa elérési útját (például: / kimeneti /). Kattintson -> **aktuális mappa kiválasztása** elemre a feltöltési célhelyének megadásához.

![A Data Lake Tools for Visual Studio Code feltöltési állapot](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    


Egy másik feltöltése fájlok tárolási módja a helyi menüben a fájl elérési útját vagy a fájl relatív elérési út a parancsfájl-szerkesztőben.

Egy időben, figyelheti a [Feltöltés állapota](#check-storage-tasks-status).

## <a name="download-file"></a>Fájl letöltése 
Letöltheti a fájlokat a parancsok beírásával **ADL: tároló fájl letöltése** vagy **ADL: tároló fájl letöltése konfigurálással**.

**Töltse le a fájlokat, ha az ADL: paranccsal konfigurációs fájl letöltése**
1. Kattintson a jobb gombbal a parancsprogram-szerkesztő, majd válassza ki **tároló fájl letöltése konfigurálással**.
2. Visual STUDIO Code jeleníti meg a JSON-fájl. Adja meg az elérési utat, és egyszerre több fájl letöltéséhez. Utasítások jelennek meg a **kimeneti** ablak. Folytatja a fájl letöltéséhez, mentse a JSON-fájl (Ctrl + S).

    ![A Data Lake Tools for konfigurációs fájlokat Visual Studio Code letöltése](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

3.  Eredmények: A **kimeneti** ablak a fájl feltöltési állapotot jeleníti meg.

    ![A Visual Studio Code letöltése eszközök Data Lake több fájl eredmények](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Egy időben, figyelheti a [letöltés állapota](#check-storage-tasks-status).

**Bár az ADL fájlok letöltéséhez: tároló fájl letöltése parancs**

Kattintson a jobb gombbal a parancsprogram-szerkesztő, majd válassza ki **tároló fájl letöltése**.

A listában válassza ki a mappát, vagy kattintson a **elérési utat adjon meg** vagy **keresse meg a legfelső szintű** (használ adjon meg egy elérési utat példaként). -> Válassza ki a **ADLA fiók**. Navigálás ->, vagy adja meg a tároló mappa elérési útját (például: / kimeneti /) -> Válassza ki a fájl letöltéséhez.

   ![A Data Lake Tools for Visual Studio Code letöltési állapot](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

   Eredmény képen látható a fájl kerül, az ideiglenes mappa. Beállíthatja a letöltés elérési útját a paraméter alapértelmezett **usql.defaultLocalFolderForDownload** egyedül a VSCode menüből **fájl** -> **beállítások**  ->  **Beállítás**.

Egy másik módszer a tárolási fájlok letöltése a helyi menüben a fájl elérési útját vagy a fájl relatív elérési út a parancsfájl-szerkesztőben van.

Egy időben, figyelheti a [letöltés állapota](#check-storage-tasks-status).

## <a name="check-storage-tasks-status"></a>Ellenőrizze a tárolási feladatok állapotát
Az állapotsor letöltését, majd ismét feltölteni a befejezésekor alján állapotát jeleníti meg.
1. Kattintson az alábbi állapotsor, és ezután a letöltés, majd ismét feltölteni a állapot megjelenítése **kimeneti** panel.

   ![A Data Lake Tools for Visual Studio Code ellenőrizze tároló állapota](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="open-azure-storage-explorer"></a>Nyissa meg az Azure storage explorer
Megnyithatja **Azure Tártallózó** parancs beírásával **ADL: Nyissa meg a webalkalmazás Azure Tártallózó** , vagy kattintson a jobb gombbal a helyi menüből való kiválasztása.

**Azure Tártallózó megnyitása**

1. Válassza ki a Ctrl + Shift + U nyissa meg a parancs palettát.
2. Adja meg **megnyitása webes Azure Tártallózó** vagy relatív elérési út vagy a teljes elérési útja, a parancsfájl-szerkesztőben kattintson a jobb gombbal, majd válassza ki **megnyitása webes Azure Tártallózó**.
3. Válassza ki a Data Lake Analytics-fiók.

A Data Lake Tools az Azure-portálon az Azure storage elérési megnyitása. Keresse meg az elérési út, és tekintse meg a fájlt az internetről.

## <a name="local-run-and-local-debug-for-windows-users"></a>Helyi futtatáskor és a helyi hibakeresése Windows felhasználók
U-SQL helyi futtatásával teszteli a helyi adatok, és helyileg, a parancsfájl ellenőrzi a kódot közzéteszi a Data Lake Analytics az előtt. A helyi hibakeresési funkciója lehetővé teszi a következő feladatok végezhetők, mielőtt a kód elküldve a Data Lake Analytics: 
- A C# háttérkód hibakeresési. 
- A kód lépéseit. 
- A parancsfájl helyi ellenőrzése.

A helyi futtatás helyi hibakeresési útmutatásért lásd: [U-SQL helyi futtatásával és a helyi hibakeresése a Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="additional-features"></a>További funkciók

A Data Lake Tools for Visual STUDIO Code támogatja a következő funkciókat:

-   Automatikus kitöltés IntelliSense: javaslatok elemek, például a kulcsszavak, a metódusok és a változók körüli előugró ablak jelenik meg. Az objektumok különböző típusú ikonjai különböző:

    - Scala adattípus
    - Összetett adattípusú
    - Beépített UDTs
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

## <a name="next-steps"></a>Következő lépések

- U-SQL helyi futtatása és a Visual Studio Code helyi debug: [U-SQL helyi futtatásával és a helyi hibakeresése a Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).
- A Data Lake Analytics-bevezető információkat lásd: [oktatóanyag: Ismerkedés az Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
- A Data Lake Tools for Visual Studio információ: [oktatóanyag: fejlesztése U-SQL-parancsfájlok Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md).
- Szerelvények fejlesztéséről további információkért lásd: [fejlesztése U-SQL-szerelvények Azure Data Lake Analytics-feladatok](data-lake-analytics-u-sql-develop-assemblies.md).



