---
title: Futtatása és az Azure Data Lake U-SQL SDK készlet használatával helyben U-SQL feladatok tesztelése
description: Megtudhatja, hogyan futtathatja és tesztelheti a U-SQL feladatok helyben a parancssorból, és alkalmazásprogramozási felület a helyi munkaállomáson.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanacai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 03/01/2017
ms.openlocfilehash: 6a73ef058a76152678099eca3f1bd15590b0b03d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238794"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Futtatása és tesztelése a U-SQL az Azure Data Lake U-SQL SDK-val

U-SQL parancsfájl fejlesztésekor szokás futtassa, és tesztelhet U-SQL parancsfájl helyi küldés előtt, hogy a felhő. Az Azure Data Lake nevű Azure Data Lake U-SQL SDK ebben a forgatókönyvben a Nuget-csomagot, akkor keresztül, amely könnyedén méretezheti a U-SQL futtatása és tesztelése. Akkor is integrálhatja a U-SQL-teszt CI (folyamatos integráció) rendszert a fordítási automatizálása és teszteléséhez.

Ha konkrét hogyan manuálisan a helyi Futtatás és hibakereső U-SQL parancsfájl a grafikus eszközök, akkor is használhat az Azure Data Lake Tools for Visual Studio, amely. Többet is megtudhat [Itt](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>Telepítse az Azure Data Lake U-SQL SDK

Az Azure Data Lake U-SQL SDK-t kaphat [Itt](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) Nuget.org webhelyen. És a használatával előtt ellenőrizze, hogy a következőképpen függőségekkel rendelkeznek kell.

### <a name="dependencies"></a>Függőségek

A Data Lake U-SQL SDK-t igényel a következő függőségeket:

- [A Microsoft .NET-keretrendszer 4.6-os vagy újabb](https://www.microsoft.com/download/details.aspx?id=17851).
- A Microsoft Visual C++ 14 és a Windows SDK-val 10.0.10240.0 vagy újabb (modellvezérelt CppSDK ebben a cikkben). Kétféleképpen CppSDK beolvasásához:

    - Telepítés [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). A Program Files mappában – például, C:\Program Files (x86) \Windows Kits\10\ rendelkezni fog egy \Windows Kits\10 mappát. A Windows 10 SDK-verzió \Windows Kits\10\Lib alatt is találhat. Ha nem látja ezeket a mappákat, telepítse újra a Visual Studióban, és ügyeljen arra, hogy válassza ki a Windows 10 SDK telepítése során. Ha ez telepítve a Visual Studióval, a U-SQL helyi fordító megkeresi azt automatikusan.

    ![A Data Lake Tools for Visual Studio helyi futtatású Windows 10 SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

    - Telepítés [Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs). Az előre összeállított Visual C++ és a Windows SDK-fájlok: C:\Program Files (x86) \Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK találja. Ebben az esetben a U-SQL helyi fordító nem található a függőségek automatikusan. Adja meg a hozzá tartozó CppSDK elérési kell. A fájlok másolása egy másik helyre, vagy használja a jelenlegi állapotában.

## <a name="understand-basic-concepts"></a>Alapvető fogalmak megismerése

### <a name="data-root"></a>Alapvető adatok

A data-gyökérmappában található a helyi számítási fiók "helyi tároló". Ez megegyezik a Data Lake Analytics-fiók az Azure Data Lake Store-fiókjába. Csakúgy, mint egy másik store-fiók vált átvált egy másik data-gyökérmappában található van. Ha azt szeretné, más adatok legfelső szintű mappák általában megosztott adatokhoz való hozzáférést, a parancsfájlok abszolút elérési utakat kell használnia. Vagy a file system szimbolikus hivatkozás létrehozása (például **mklink** NTFS) a data-gyökérmappájában, hogy a megosztott adatok mutasson.

A data-gyökérmappában található a következőkre használható:

- Store helyi metaadatokat, például adatbázisok, táblák, táblaértékű függvények (TVFs) és szerelvényeket.
- Keresse meg a bemeneti és kimeneti elérési utak relatív elérési utakat a U-SQL-ben vannak meghatározva. Relatív elérési utak használata megkönnyíti az Azure-bA a U-SQL-projektek telepítésére.

### <a name="file-path-in-u-sql"></a>U-SQL-fájl elérési útja

U-SQL-parancsfájlok relatív elérési utat és a egy helyi abszolút elérési utat használhatja. A relatív elérési út a megadott adatok-gyökérmappa elérési útja viszonyítva. Azt javasoljuk, hogy használja "/", hogy a parancsfájlok kompatibilis a kiszolgálói oldalon az elérési út elválasztóként. Az alábbiakban néhány példa a relatív elérési utakat és azok egyenértékű abszolút elérési utakat. Ezekben a példákban C:\LocalRunDataRoot a data-gyökérmappában található.

|Relatív elérési út|Abszolút elérési út|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Munkakönyvtár

Ha helyileg futtatja a U-SQL-szkript, egy működő könyvtárba aktuális futtatási könyvtárának a fordítás során jön létre. A fordítási kimenetek mellett a szükséges futtatási fájlokat a helyi végrehajtásra lesz árnyékmásolat lépjen a munkakönyvtárba. Munkamappa directory legfelső szintű "ScopeWorkDir" nevezik, és a a munkakönyvtárban lévő fájlokat a következők:

|Könyvtár|Könyvtár|Könyvtár|Meghatározás|Leírás|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Verze modulu runtime kivonat karakterláncot|Futtatási fájlokat a helyi végrehajtásra szükséges árnyékmásolat|
| |Script_66AE4909AA0ED06C| |Szkript neve + a kivonat karakterláncot a parancsprogram elérési útja|Fordítási kimenetek és végrehajtási lépés a naplózás|
| | |\_script\_.abr|Kompilátor jazyka kimenet|Algebra fájl|
| | |\_ScopeCodeGen\_. *|Kompilátor jazyka kimenet|A felügyelt kód létrehozva|
| | |\_ScopeCodeGenEngine\_. *|Kompilátor jazyka kimenet|Generált nativní kód.|
| | |odkazovaná sestavení|Odkaz sestavení|Hivatkozott tevékenységszerelvény-fájlokhoz|
| | |deployed_resources|Erőforrások üzembe helyezésének|Erőforrás üzembe helyezési fájlokat|
| | |xxxxxxxx.xxx[1..n]\_\*. *|Végrehajtási napló|Naplózása a végrehajtási lépések|


## <a name="use-the-sdk-from-the-command-line"></a>Az SDK-val a parancssorból

### <a name="command-line-interface-of-the-helper-application"></a>Parancssori felület, a segédalkalmazással.

Alatt SDK directory\build\runtime LocalRunHelper.exe kifejezés a parancssori segédprogram, amely a gyakran használt helyi futtatású szolgáltatás legtöbb felületeket biztosít. Fontos, hogy a parancs, mind a argumentum kapcsolók kis-és nagybetűket. Indítsa el:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

LocalRunHelper.exe, argumentumok nélkül, vagy a fusson a **súgó** kapcsoló megjelenítése a súgó információit:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

A Súgó információkat:

-  **A parancs** lehetővé teszi a parancs neve.  
-  **Kötelező argumentum** , meg kell adni argumentumait sorolja fel.  
-  **Nem kötelező argumentum** sorolja fel, ez nem kötelező megadni, az alapértelmezett értékekkel.  Logikai típusú argumentumot nem kötelező paraméterek nem rendelkezik, és azok idővonalnézettel negatív alapértelmezett értékre való jelenti.

### <a name="return-value-and-logging"></a>Visszatérési érték és naplózás

Visszaadja a segédalkalmazással **0** a sikeres és **-1** hiba. Alapértelmezés szerint a segítő összes üzenetet küld a jelenlegi konzolt. Azonban támogatja a legtöbb parancs, a **- MessageOut naplófájl_elérési_útja** nem kötelező argumentum, amely átirányítja a kimenetek naplófájlba.

### <a name="environment-variable-configuring"></a>Környezeti változó konfigurálása

U-SQL helyi futtató van szüksége a megadott adatok legfelső szintű helyi storage-fiókot, valamint egy adott CppSDK elérési függőségek. Mindkét beállíthatja az argumentum parancssori vagy állítson be környezeti változót a számukra.

- Állítsa be a **SCOPE_CPP_SDK** környezeti változót.

    Ha a Microsoft Visual C++ és a Windows SDK-t a Data Lake Tools for Visual Studio telepítésével, győződjön meg arról, hogy rendelkezik-e a következő mappában:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Adjon meg egy új környezeti változót nevű **SCOPE_CPP_SDK** , hogy ez a könyvtár mutasson. Vagy a más helyre másolja a mappát, és adja meg **SCOPE_CPP_SDK** megegyezik.

    A környezeti változó beállítása, mellett megadhatja a **- CppSDK** argumentumként a parancssorban használatakor. Ez az argumentum felülírja az alapértelmezett CppSDK környezeti változót.

- Állítsa be a **LOCALRUN_DATAROOT** környezeti változót.

    Adjon meg egy új környezeti változót nevű **LOCALRUN_DATAROOT** , amely az adatok legfelső szintű mutat.

    A környezeti változó beállítása, mellett megadhatja a **- DataRoot** a data-gyökér elérési úthoz használatakor egy parancssori argumentumot. Ez az argumentum felülírja az alapértelmezett adatgyökere környezeti változót. Ennek az argumentumnak ad hozzá minden parancssorból futtatja, hogy az alapértelmezett adatgyökere környezeti változót a művelet felülírhatja kell.

### <a name="sdk-command-line-usage-samples"></a>SDK-t parancssori használati minták

#### <a name="compile-and-run"></a>Fordítás és futtatás

A **futtatása** parancs segítségével a parancsfájl összeállításához, és ezután hajtsa végre a lefordított eredményeket. Parancssori argumentumok szerintiek kombinációját **összeállítása** és **végrehajtása**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Az alábbiakban a nem kötelező argumentumok **futtatása**:


|Argumentum|Alapértelmezett érték|Leírás|
|--------|-------------|-----------|
|-Háttérkódban|False (Hamis)|A parancsfájl .cs kód tartozik.|
|-CppSDK| |CppSDK Directory|
|-A DataRoot| DataRoot környezeti változó|Helyi futtatás, a "LOCALRUN_DATAROOT" környezeti változó alapértelmezés szerint a DataRoot|
|-MessageOut| |Memóriakép üzeneteket egy fájlba konzolon|
|-Párhuzamos|1|Futtassa a csomag a megadott párhuzamosság|
|-Hivatkozások| |Az elérési utak listája extra referenciaszerelvények vagy háttérkód, az adatfájlok elválasztva ';'|
|-UdoRedirect|False (Hamis)|Udo szerelvény átirányítási konfiguráció létrehozása|
|-UseDatabase|master|Az ideiglenes szerelvényregisztráló háttérkód használni kívánt adatbázist|
|-Verbose|False (Hamis)|A futtatókörnyezet részletes kimenetek megjelenítése|
|-WorkDir|Aktuális címtár|Is a fordító használat és a kimeneti könyvtár|
|-RunScopeCEP|0|ScopeCEP módot használja|
|-ScopeCEPTempPath|TEMP|Ideiglenes útvonalat a streamelési adatok|
|-OptFlags| |Optimalizáló jelzők vesszővel elválasztott listája|


Például:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Kombinálása mellett **összeállítása** és **végrehajtása**, összeállításához, és hajtsa végre a lefordított végrehajtható fájlok külön-külön.

#### <a name="compile-a-u-sql-script"></a>A U-SQL parancsfájl összeállítása

A **összeállítása** parancs segítségével végrehajtható fájlok U-SQL parancsfájl összeállításához.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Az alábbiakban a nem kötelező argumentumok **összeállítása**:


|Argumentum|Leírás|
|--------|-----------|
| -Háttérkódban [alapérték: 'False']|A parancsfájl .cs kód tartozik.|
| -CppSDK [alapértelmezett érték: "%]|CppSDK Directory|
| -DataRoot [alapértelmezett érték "DataRoot környezeti változó"]|Helyi futtatás, a "LOCALRUN_DATAROOT" környezeti változó alapértelmezés szerint a DataRoot|
| -MessageOut [alapértelmezett érték: "%]|Memóriakép üzeneteket egy fájlba konzolon|
| -Hivatkozik [alapértelmezett érték: "%]|Az elérési utak listája extra referenciaszerelvények vagy háttérkód, az adatfájlok elválasztva ';'|
| -Sekély [alapérték: 'False']|Sekély fordítás|
| -UdoRedirect [alapérték: 'False']|Udo szerelvény átirányítási konfiguráció létrehozása|
| -UseDatabase [alapértelmezett érték a "master"]|Az ideiglenes szerelvényregisztráló háttérkód használni kívánt adatbázist|
| -WorkDir [alapértelmezett érték 'Az aktuális könyvtár']|Is a fordító használat és a kimeneti könyvtár|
| -RunScopeCEP [alapértelmezett érték "0"]|ScopeCEP módot használja|
| -ScopeCEPTempPath [alapértelmezett érték "temp"]|Ideiglenes útvonalat a streamelési adatok|
| -OptFlags [alapértelmezett érték: "%]|Optimalizáló jelzők vesszővel elválasztott listája|


Az alábbiakban néhány használati példák.

A U-SQL parancsfájl összeállítása:

    LocalRunHelper compile -Script d:\test\test1.usql

A U-SQL parancsfájl összeállításához, és állítsa be a data-gyökérmappában található. Vegye figyelembe, hogy ezzel felülírja a környezeti változó beállítása.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

U-SQL parancsfájl összeállításához, és egy működő könyvtárba, a referencia-szerelvény és az adatbázis beállítása:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Hajtsa végre a lefordított eredmények

A **végrehajtása** parancs segítségével hajtsa végre a lefordított eredményeket.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Az alábbiakban a nem kötelező argumentumok **végrehajtása**:

|Argumentum|Alapértelmezett érték|Leírás|
|--------|-------------|-----------|
|-A DataRoot | '' |Metaadatok végrehajtási adatok legfelső szintű. A rendszer alapértelmezés szerint a **LOCALRUN_DATAROOT** környezeti változót.|
|-MessageOut | '' |Memóriakép üzeneteket egy fájlba a konzolon.|
|-Párhuzamos | (1) |A megadott párhuzamosság szinttel létrehozott helyi futtatású lépések futtatására mutató.|
|-Verbose | "False" |Mutató futásidejű részletes kimenetei megjelenítéséhez.|

Íme egy példa a használatra:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>Az alkalmazásprogramozási felület SDK használata

Az alkalmazásprogramozási felület a LocalRunHelper.exe mappában találhatók. Integrálható a U-SQL SDK és a C# teszt keretrendszer méretezése a U-SQL parancsfájl helyi tesztelése működése használhatja őket. Ez a cikk e bemutatják, hogyan használhatja ezeket az adaptereket a U-SQL-parancsprogram teszteléséhez használja a szabványos C# egység tesztelő projektet.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>1. lépés: C# egység tesztelő projektet és a konfiguráció létrehozása

- Hozzon létre egy C# egység tesztelő projektet fájl > Új > Projekt > Visual C# > tesztelése > egység tesztelése projekt.
- LocalRunHelper.exe hozzáadása a projekt hivatkozásként. A LocalRunHelper.exe \build\runtime\LocalRunHelper.exe Nuget-csomagot a következő helyen található.

    ![Azure Data Lake U-SQL SDK hivatkozás hozzáadása](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- U-SQL SDK **csak** támogatási x64 környezetben, ügyeljen arra, hogy a build platform cél x64 állítja be. Projekt tulajdonság keresztül is megadhatja, hogy > hozhat létre > Platform cél.

    ![Azure Data Lake U-SQL SDK konfigurálása x64 projekt](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Ellenőrizze, hogy a tesztkörnyezet x64 állítja be. A Visual Studióban keresztül is beállítható, teszt > tesztelési beállítások > alapértelmezett processzorarchitektúra > x64.

    ![Azure Data Lake U-SQL SDK konfigurálása x64 tesztelési környezet](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Ellenőrizze, hogy a projekt munkakönyvtár, amely általában a ProjectFolder\bin\x64\Debug NugetPackage\build\runtime\ található összes függőséget fájlt másolja.

### <a name="step-2-create-u-sql-script-test-case"></a>2. lépés: A U-SQL parancsfájl tesztelési eset létrehozása

Az alábbi, a U-SQL parancsfájl tesztelési mintakódot. A vizsgálat, készítse elő a parancsfájlok, a bemeneti fájlok és a várt kimeneti fájlok kell.

    using System;
    using Microsoft.VisualStudio.TestTools.UnitTesting;
    using System.IO;
    using System.Text;
    using System.Security.Cryptography;
    using Microsoft.Analytics.LocalRun;

    namespace UnitTestProject1
    {
        [TestClass]
        public class USQLUnitTest
        {
            [TestMethod]
            public void TestUSQLScript()
            {
                //Specify the local run message output path
                StreamWriter MessageOutput = new StreamWriter("../../../log.txt");

                LocalRunHelper localrun = new LocalRunHelper(MessageOutput);

                //Configure the DateRoot path, Script Path and CPPSDK path
                localrun.DataRoot = "../../../";
                localrun.ScriptPath = "../../../Script/Script.usql";
                localrun.CppSdkDir = "../../../CppSDK";

                //Run U-SQL script
                localrun.DoRun();

                //Script output 
                string Result = Path.Combine(localrun.DataRoot, "Output/result.csv");

                //Expected script output
                string ExpectedResult = "../../../ExpectedOutput/result.csv";

                Test.Helpers.FileAssert.AreEqual(Result, ExpectedResult);

                //Don't forget to close MessageOutput to get logs into file
                MessageOutput.Close();
            }
        }
    }

    namespace Test.Helpers
    {
        public static class FileAssert
        {
            static string GetFileHash(string filename)
            {
                Assert.IsTrue(File.Exists(filename));

                using (var hash = new SHA1Managed())
                {
                    var clearBytes = File.ReadAllBytes(filename);
                    var hashedBytes = hash.ComputeHash(clearBytes);
                    return ConvertBytesToHex(hashedBytes);
                }
            }

            static string ConvertBytesToHex(byte[] bytes)
            {
                var sb = new StringBuilder();

                for (var i = 0; i < bytes.Length; i++)
                {
                    sb.Append(bytes[i].ToString("x"));
                }
                return sb.ToString();
            }

            public static void AreEqual(string filename1, string filename2)
            {
                string hash1 = GetFileHash(filename1);
                string hash2 = GetFileHash(filename2);

                Assert.AreEqual(hash1, hash2);
            }
        }
    }


### <a name="programming-interfaces-in-localrunhelperexe"></a>A LocalRunHelper.exe programozási felületek

LocalRunHelper.exe az alkalmazásprogramozási felületek biztosít a U-SQL helyi fordítási, futtassa, és így tovább. Azok a felületek módon jelennek meg.

**A konstruktor**

nyilvános LocalRunHelper ([System.IO.TextWriter messageOutput = null])

|Paraméter|Típus|Leírás|
|---------|----|-----------|
|messageOutput|System.IO.TextWriter|kimeneti üzenetek esetében be null értékre, a konzol használata|

**Tulajdonságok**

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|AlgebraPath|sztring|A fájl elérési útját algebra (algebra fájl egyike a fordítás eredménye)|
|CodeBehindReferences|sztring|Ha a parancsfájl mögött hivatkozások további kódot tartalmaz, adja meg az elérési utak elválasztva ';'|
|CppSdkDir|sztring|CppSDK directory|
|CurrentDir|sztring|Aktuális címtár|
|DataRoot|sztring|Adatok elérési útjának gyökeréhez|
|DebuggerMailPath|sztring|A hibakereső mailslot elérési útja|
|GenerateUdoRedirect|Logikai|Szeretnénk szerelvénybetöltési átirányítás felülbírálás konfiguráció létrehozása|
|HasCodeBehind|Logikai|Ha a parancsfájl mögötti kódban|
|InputDir|sztring|Bemeneti adataihoz tartozó könyvtár|
|MessagePath|sztring|Üzenet memóriakép-fájl elérési útja|
|OutputDir|sztring|A kimeneti adatokat könyvtár|
|Párhuzamosság|int|Futtassa a algebra párhuzamosságot|
|ParentPid|int|PID azonosítója, amelyen a szolgáltatás a kilépéshez figyeli a szülő állítsa 0-ra, vagy negatív figyelmen kívül hagyása|
|ResultPath|sztring|Eredmény memóriakép-fájl elérési útja|
|RuntimeDir|sztring|Futásidejű könyvtár|
|ScriptPath|sztring|Hol találhatók a parancsprogram|
|Sekély|Logikai|Fordítási sekély, vagy sem|
|Ideiglenes könyvtárának|sztring|Átmeneti könyvtár|
|UseDataBase|sztring|Adja meg az ideiglenes szerelvényregisztráló, alapértelmezés szerint fő háttérkód használni kívánt adatbázis|
|WorkDir|sztring|Előnyben részesített munkakönyvtár|


**Metódus**

|Módszer|Leírás|a visszaadandó|Paraméter|
|------|-----------|------|---------|
|nyilvános bool DoCompile()|A U-SQL parancsfájl összeállítása|Sikeres művelet igaz| |
|nyilvános bool DoExec()|Hajtsa végre a lefordított eredménye|Sikeres művelet igaz| |
|nyilvános bool DoRun()|Futtassa a U-SQL parancsfájlt (fordítási + végrehajtás)|Sikeres művelet igaz| |
|nyilvános bool IsValidRuntimeDir (karakterlánc elérési útja)|Ellenőrizze, hogy a megadott elérési út érvényes modul elérési útja|Igaz érvényes|Futásidejű könyvtár elérési útja|


## <a name="faq-about-common-issue"></a>Gyakori probléma – gyakori kérdések

### <a name="error-1"></a>1. hiba:
E_CSC_SYSTEM_INTERNAL: Belső hiba! Nem sikerült betölteni a fájl vagy a "ScopeEngineManaged.dll" szerelvény vagy a hozzá tartozó függőségek egyike. A megadott modul nem található.

Ellenőrizze a következőket:

- Ellenőrizze, hogy x64 környezetben. A build target platform és a tesztelési környezetben kell x64, tekintse meg **1. lépés: egység létrehozása a C# projekt és a konfiguráció teszteléséhez** felett.
- Ellenőrizze, hogy a munkakönyvtár projekthez NugetPackage\build\runtime\ található összes függőséget fájlt másolta.


## <a name="next-steps"></a>További lépések

* A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével).
* Tekintse meg a diagnosztikai információk naplózása [Azure Data Lake Analytics diagnosztikai naplóinak elérése](data-lake-analytics-diagnostic-logs.md).
* Egy összetettebb lekérdezés megtekintéséhez lásd: [használata Azure Data Lake Analytics webhelyek naplóinak elemzése](data-lake-analytics-analyze-weblogs.md).
* Feladatok részleteinek megtekintéséhez: [használja a Feladatböngésző és a feladat nézet az Azure Data Lake Analytics-feladatok](data-lake-analytics-data-lake-tools-view-jobs.md).
* A vertex végrehajtási nézet használata, lásd: [a Vertex végrehajtási nézet használata a Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
