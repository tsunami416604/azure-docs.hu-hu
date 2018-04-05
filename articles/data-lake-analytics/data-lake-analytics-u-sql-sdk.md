---
title: Skála U-SQL helyi futtatása és a teszt Azure Data Lake U-SQL-SDK-val |} Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Data Lake U-SQL SDK méretezési U-SQL feladatok helyi futtatása és a vizsgálat a parancssor és a helyi munkaállomáson alkalmazásprogramozási felületek.
services: data-lake-analytics
documentationcenter: ''
author: ''
manager: ''
editor: ''
ms.assetid: ''
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: yanacai
ms.openlocfilehash: 55242bcf644ca0e7f30cfe7eada2130451c36e64
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="scale-u-sql-local-run-and-test-with-azure-data-lake-u-sql-sdk"></a>Skála U-SQL helyi futtatása és a teszt Azure Data Lake U-SQL-SDK-val

U-SQL parancsfájl fejlesztésekor közös futtatásához és tesztelhet U-SQL parancsfájl helyi előtt küldje el a felhőben. Azure Data Lake biztosít az Azure Data Lake U-SQL SDK hívása ebben a forgatókönyvben a Nuget-csomagot, keresztül, amely könnyedén méretezhető U-SQL helyi futtatásával és a vizsgálat. Akkor is a U-SQL teszt integrálása CI (folyamatos integrációt) rendszer automatizálhatja a fordítási és teszteléséhez.

Ha az Ön számára legfontosabb hogyan manuálisan a helyi Futtatás és hibakeresési grafikus felhasználói Felülettel tooling U-SQL parancsfájlt, majd használható Azure Data Lake Tools for Visual Studio, amely. Többet is megtudhat [Itt](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>Telepítés Azure Data Lake U-SQL-SDK

Az Azure Data Lake U-SQL SDK kaphat [Itt](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) a Nuget.org. És annak használata előtt győződjön meg arról, hogy a függőségek az alábbiak szerint van szükség.

### <a name="dependencies"></a>Függőségek

A Data Lake U-SQL SDK számára szükséges a következő függőségek:

- [A Microsoft .NET-keretrendszer 4.6-os vagy újabb](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 és a Windows SDK 10.0.10240.0 vagy újabb (amely más néven CppSDK ebben a cikkben). Kétféleképpen CppSDK eléréséhez:

    - Telepítés [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). A Program Files mappában – például a C:\Program Files (x86) \Windows Kits\10\ kell egy \Windows Kits\10 mappát. A Windows 10-es SDK verzió \Windows Kits\10\Lib is találhat. Ha nem látja ezeket a mappákat, telepítse újra a Visual Studio, és ügyeljen arra, hogy a telepítés során válassza ki a Windows 10-es SDK-t. Ha ez a Visual Studio telepítve van, a U-SQL helyi fordító megkeresi azt automatikusan.

    ![A Data Lake Tools for Visual Studio helyi futtatási Windows 10-es SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

    - Telepítés [a Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs). Az előre csomagolt Visual C++ és a Windows SDK-fájlokat a C:\Program Files (x86) \Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK találja. Ebben az esetben a U-SQL helyi fordító nem található a függőségek automatikusan. Meg kell adnia a CppSDK elérési út azt. Másolja a fájlokat egy másik helyre, vagy használja a jelenlegi állapotában.

## <a name="understand-basic-concepts"></a>Alapvető fogalmak megismerése

### <a name="data-root"></a>Adatgyökerében

Az adatok-gyökérmappája "helyi tároló" a helyi számítási fiókhoz. Megegyezik a Data Lake Analytics-fiók az Azure Data Lake Store-fiókjába. Váltás másik adatok-gyökérmappának az csakúgy, mint egy másik store-fiók vált. Ha azt szeretné, más adatok legfelső szintű mappák általában megosztott adatokhoz való hozzáférést, abszolút elérési utakat kell használnia a parancsfájlban. Vagy hozzon létre fájlt rendszer szimbolikus hivatkozásokat (például **mklink** NTFS) a megosztott adatok az adatok-gyökere alatt.

Az adatok gyökérmappa a következőkre használható:

- Helyi metaadatainak, például adatbázisok, táblák, táblaértékű függvények (TVFs) és szerelvényeket tárolják.
- Keresse meg a bemeneti és kimeneti elérési utak relatív elérési utak a U-SQL is meg van adva. Relatív elérési utak használata megkönnyíti a U-SQL projekt telepítése az Azure-bA.

### <a name="file-path-in-u-sql"></a>U-SQL-fájl elérési útja

U-SQL-parancsfájlok használhatja relatív elérési út és a helyi abszolút elérési utat. A relatív elérési út a megadott adatok-gyökérmappa elérési útja viszonyítva. Azt javasoljuk, hogy használjon "/" az elérési út elválasztó annak a parancsfájlokat a kiszolgálóoldali kompatibilis. Az alábbiakban néhány olyan relatív elérési útja és az egyenértékű abszolút elérési utakat. Ezekben a példákban C:\LocalRunDataRoot az adatok-gyökérmappájába.

|Relatív elérési út|Abszolút elérési útja|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Munkakönyvtár

A U-SQL parancsfájl helyben fut, amikor egy működő könyvtárba aktuális futtatási könyvtárának a fordítás során jön létre. A fordítás kimenetek mellett a szükséges futásidejű fájlok helyi végrehajtásra lesz a munkakönyvtárba árnyékmásolat. Working directory gyökérmappájába "ScopeWorkDir" nevezik, és a munkakönyvtárat a fájlok a következők:

|Directory/file|Directory/file|Directory/file|Meghatározás|Leírás|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Kivonat karakterláncot futtatókörnyezet-verzió|A helyi végrehajtásához szükséges futásidejű fájlok árnyékmásolatát|
| |Script_66AE4909AA0ED06C| |Parancsfájl neve + kivonat karakterláncot a parancsprogram elérési útja|Fordítási kimenetek és végrehajtási lépés a naplózás|
| | |\_script\_.abr|A fordítóprogram kimenetének|Algebra fájl|
| | |\_ScopeCodeGen\_. *|A fordítóprogram kimenetének|A felügyelt kód jön létre|
| | |\_ScopeCodeGenEngine\_. *|A fordítóprogram kimenetének|Létrehozott natív kód|
| | |hivatkozott szerelvények|Szerelvényre mutató hivatkozás|Hivatkozott szerelvény fájlok|
| | |deployed_resources|Erőforrások telepítése|Erőforrás-telepítési fájlok|
| | |xxxxxxxx.xxx[1..n]\_\*.*|Végrehajtási napló|A végrehajtási lépések napló|


## <a name="use-the-sdk-from-the-command-line"></a>Az SDK-val a parancssorból

### <a name="command-line-interface-of-the-helper-application"></a>Parancssori felületének a segédalkalmazással.

Az SDK directory\build\runtime LocalRunHelper.exe kifejezés a parancssori segédprogram, amely a gyakran használt helyi futtatási funkciók a legtöbb felületet biztosít. Vegye figyelembe, hogy a parancs, mind a argumentum kapcsolók kis-és nagybetűket. Indítsa el:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Paraméterek nélkül, vagy a LocalRunHelper.exe fusson a **súgó** kapcsolót, hogy az információk megjelenítése:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

A Súgó adatokat:

-  **A parancs** elnevezi a parancsot.  
-  **Kötelező argumentum** , meg kell adni argumentumait sorolja fel.  
-  **Nem kötelező argumentumában** sorolja fel, ez nem kötelező megadni, alapértelmezett értékekkel.  Nem kötelező, logikai típusú argumentumokat nem kell a paramétereket, és azok megjelenésének jelenti negatív alapértelmezett értékét.

### <a name="return-value-and-logging"></a>Visszatérési érték és a naplózás

Visszaadja a segédalkalmazással **0** a sikeres és **-1** sikertelen. Alapértelmezés szerint az segítő minden üzenetet küld a jelenlegi konzolt. Azonban a legtöbb parancs támogatja a **- MessageOut naplófájl_elérési_útja** nem kötelező argumentum, amely átirányítja a kimenetek naplófájlba.

### <a name="environment-variable-configuring"></a>Környezeti változó beállítása

U-SQL helyi futnia kell a megadott adatok legfelső szintű helyi storage-fiók, valamint egy adott CppSDK függőségek elérési úton. Is beállíthat a argumentum parancssori vagy beállított környezeti változóban számukra.

- Állítsa be a **SCOPE_CPP_SDK** környezeti változó.

    Ha a Microsoft Visual C++ és a Windows SDK-t úgy, hogy a Data Lake Tools for Visual Studio telepítése, győződjön meg arról, hogy rendelkezik-e a következő mappát:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Adja meg egy új környezeti változó neve **SCOPE_CPP_SDK** úgy, hogy ez a könyvtár mutasson. Vagy a más helyre másolja a mappát, és adja meg **SCOPE_CPP_SDK** , mint a.

    A környezeti változó mellett, megadhatja a **- CppSDK** argumentum a parancssorban használatakor. Ennek az argumentumnak felülírja az alapértelmezett CppSDK környezeti változó.

- Állítsa be a **LOCALRUN_DATAROOT** környezeti változó.

    Adja meg egy új környezeti változó neve **LOCALRUN_DATAROOT** a adatgyökerében mutat.

    A környezeti változó mellett, megadhatja a **- DataRoot** adatok-gyökérútvonalát parancssor használatakor argumentumot. Ennek az argumentumnak felülírja az alapértelmezett adatgyökerében környezeti változó. Ennek az argumentumnak parancssorhoz adhat hozzá minden futtatja, hogy az alapértelmezett adatgyökerében környezeti változó minden műveletnél felülírhatja kell.

### <a name="sdk-command-line-usage-samples"></a>SDK parancssori használati minták

#### <a name="compile-and-run"></a>Fordítás és futtatás

A **futtatása** parancs segítségével a parancsfájl összeállításához és lefordított eredmények hajthat végre. Parancssori argumentumok szerintiek kombinációja **fordítási** és **hajtható végre**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

A következők: nem kötelező argumentumainak **futtatása**:


|Argumentum|Alapértelmezett érték|Leírás|
|--------|-------------|-----------|
|-Háttérkódban|False (Hamis)|A parancsfájl .cs kód tartozik.|
|-CppSDK| |CppSDK Directory|
|-DataRoot| DataRoot környezeti változó|A helyi futtatáskor, "LOCALRUN_DATAROOT" környezeti változó alapértelmezett DataRoot|
|-MessageOut| |A konzol fájlba memóriakép üzenetek|
|-Párhuzamos|1|A terv a megadott párhuzamos Futtatás|
|-Hivatkozások| |Az elérési utak listája további hivatkozási szerelvények vagy adatforrás adatfájljainak tartozó kódot, szóközzel elválasztva ';'|
|-UdoRedirect|False (Hamis)|Udo szerelvény átirányítási config készítése|
|-UseDatabase|master|Az ideiglenes szerelvények regisztrálásakor háttérkódot használandó adatbázis|
|-Verbose|False (Hamis)|A futásidejű részletes kimenetének megjelenítése|
|-WorkDir|Jelenlegi Active Directory|A fordító használati és a kimeneti könyvtár|
|-RunScopeCEP|0|ScopeCEP módot használja|
|-ScopeCEPTempPath|TEMP|Ideiglenes útvonalat a streamelési adatok|
|-OptFlags| |Optimalizáló jelzők vesszővel elválasztott listája|


Például:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Egyidejű mellett **fordítási** és **hajtható végre**, fordítása, és a lefordított végrehajtható fájlok külön-külön végrehajtani.

#### <a name="compile-a-u-sql-script"></a>A U-SQL parancsfájl összeállítása

A **fordítási** parancs segítségével végrehajtható fájlokat egy U-SQL parancsfájl összeállításához.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

A következők: nem kötelező argumentumainak **fordítási**:


|Argumentum|Leírás|
|--------|-----------|
| -Háttérkódban [alapértelmezett értéke "False"]|A parancsfájl .cs kód tartozik.|
| -CppSDK [alapértelmezett érték "]|CppSDK Directory|
| -DataRoot [alapértelmezett érték "DataRoot környezeti változó"]|A helyi futtatáskor, "LOCALRUN_DATAROOT" környezeti változó alapértelmezett DataRoot|
| -MessageOut [alapértelmezett érték "]|A konzol fájlba memóriakép üzenetek|
| -Hivatkozik [alapértelmezett érték "]|Az elérési utak listája további hivatkozási szerelvények vagy adatforrás adatfájljainak tartozó kódot, szóközzel elválasztva ';'|
| -Sekély [alapértelmezett értéke "False"]|Sekély fordítási|
| -UdoRedirect [alapértelmezett értéke "False"]|Udo szerelvény átirányítási config készítése|
| -UseDatabase [alapértelmezett érték "master"]|Az ideiglenes szerelvények regisztrálásakor háttérkódot használandó adatbázis|
| -WorkDir [alapértelmezett érték "Az aktuális könyvtár"]|A fordító használati és a kimeneti könyvtár|
| -RunScopeCEP [alapértelmezett érték "0"]|ScopeCEP módot használja|
| -ScopeCEPTempPath [alapértelmezett érték "temp"]|Ideiglenes útvonalat a streamelési adatok|
| -OptFlags [alapértelmezett érték "]|Optimalizáló jelzők vesszővel elválasztott listája|


Az alábbiakban néhány használati példák.

A U-SQL parancsfájl összeállítása:

    LocalRunHelper compile -Script d:\test\test1.usql

U-SQL parancsfájl összeállítása, és állítsa be az adatok gyökérmappájában. Vegye figyelembe, hogy ezzel a művelettel felülírja a készlet környezeti változó.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

U-SQL parancsfájl fordítása, és egy működő könyvtárba, a referencia szerelvény és az adatbázis beállítása:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Lefordított eredmények végrehajtása

A **hajtható végre** parancs hajthatók végre a lefordított eredmények.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

A következők: nem kötelező argumentumainak **hajtható végre**:

|Argumentum|Leírás|
|--------|-----------|
|-DataRoot [alapértelmezett érték "]|A metaadatok végrehajtás adatgyökerében. Alapértelmezés szerint az a **LOCALRUN_DATAROOT** környezeti változó.|
|-MessageOut [alapértelmezett érték "]|A konzol egy fájlba az üzenetek dump.|
|-Párhuzamos [alapértelmezett értéke "1"]|A létrehozott helyi futtatási lépéseket futtatni a megadott párhuzamossági szintű mutató.|
|-Verbose [alapértelmezett érték "Hamis"]|A futásidejű részletes kimenetének megjelenítése mutató.|

Íme egy példa:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>Az SDK használatához programozási felületek

Az alkalmazásprogramozási felületek a LocalRunHelper.exe a találhatók. A U-SQL SDK és a C# teszt keretében a U-SQL parancsfájl helyi tesztelése méretezési funkcióinak integrálását használhatja őket. Ebben a cikkben használom bemutatja ezek felületek segítségével tesztelje a U-SQL parancsfájlt a szabványos C# egység teszt-projektet.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>1. lépés: A C# egység teszt projekt és konfigurációs létrehozása

- Hozzon létre egy C# egység teszt projektet fájl > Új > Projekt > Visual C# > tesztelése > egység tesztelése projekt.
- Adja hozzá egy hivatkozást a projekt LocalRunHelper.exe. A LocalRunHelper.exe itt található: \build\runtime\LocalRunHelper.exe a Nuget-csomagot.

    ![Azure Data Lake U-SQL SDK hivatkozás hozzáadása](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- U-SQL SDK **csak** támogatási x64 környezet, ügyeljen arra, hogy x64 build platform célként beállítva. Beállíthatja, hogy a projekt tulajdonságon keresztül > Build > Platform cél.

    ![Azure Data Lake U-SQL SDK Configure x64 Project](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Ügyeljen arra, hogy a tesztkörnyezet beállítása x64. A Visual Studio állíthatja keresztül Test > vizsgálati beállítások > alapértelmezett processzorarchitektúra > x64.

    ![Azure Data Lake U-SQL SDK konfigurálása x64 tesztelési környezetben](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Ügyeljen arra, hogy másolja az összes függőség fájlt a NugetPackage\build\runtime\ munkakönyvtár, amely általában a ProjectFolder\bin\x64\Debug projekthez.

### <a name="step-2-create-u-sql-script-test-case"></a>2. lépés: A U-SQL parancsfájl Teszteset létrehozása

Az alábbiakban van a mintakódot a U-SQL parancsfájl teszteléséhez. Tesztelési, elő kell készíteni a parancsfájlok, a bemeneti fájlok és a várt kimeneti fájlokat.

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


### <a name="programming-interfaces-in-localrunhelperexe"></a>A LocalRunHelper.exe felületek

LocalRunHelper.exe az alkalmazásprogramozási felületek biztosít a U-SQL helyi fordítási, futtassa, stb. Azok a felületek a következőképpen vannak felsorolva.

**Konstruktor**

nyilvános LocalRunHelper ([System.IO.TextWriter messageOutput = null])

|Paraméter|Típus|Leírás|
|---------|----|-----------|
|messageOutput|System.IO.TextWriter|a kimeneti üzenetek be null konzol használata|

**Tulajdonságok**

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|AlgebraPath|karakterlánc|A fájl elérési útját algebra (algebra fájl egyike a fordítási eredmények)|
|CodeBehindReferences|karakterlánc|Ha a parancsfájl hivatkozások további kód tartozik, adja meg az elérési utak határolt ";"|
|CppSdkDir|karakterlánc|CppSDK directory|
|CurrentDir|karakterlánc|Aktuális könyvtárhoz|
|DataRoot|karakterlánc|Adatok elérési útjának gyökeréhez|
|DebuggerMailPath|karakterlánc|A hibakereső mailslot elérési útja|
|GenerateUdoRedirect|logikai érték|Ha azt szeretné, hogy készítése a szerelvény betöltése átirányítási felülbírálása config|
|HasCodeBehind|logikai érték|Ha a parancsfájl kód tartozik.|
|InputDir|karakterlánc|A bemeneti adatok könyvtár|
|MessagePath|karakterlánc|Üzenet memóriakép fájl elérési útja|
|OutputDir|karakterlánc|A kimeneti adatok könyvtár|
|Párhuzamos végrehajtás|int|Párhuzamossági a algebra futtatásához|
|ParentPid|int|Azonosítója (PID), amelyen a szolgáltatás való kilépéshez, figyeli a szülő értéke 0, vagy negatív figyelmen kívül hagyása|
|ResultPath|karakterlánc|Eredmény memóriakép fájl elérési útja|
|RuntimeDir|karakterlánc|Futásidejű könyvtár|
|ScriptPath|karakterlánc|Hol találhatók a parancsprogram|
|Sekély|logikai érték|Vagy nem a fordítási sekély|
|TempDir|karakterlánc|Ideiglenes könyvtár|
|UseDataBase|karakterlánc|Adja meg az adatbázishoz való használatra ideiglenes szerelvények regisztrálásakor, alapértelmezés szerint fő mögötti kódban|
|WorkDir|karakterlánc|Előnyben részesített munkakönyvtár|


**Módszer**

|Módszer|Leírás|Visszatérési érték|Paraméter|
|------|-----------|------|---------|
|nyilvános bool DoCompile()|A U-SQL parancsfájl összeállítása|Sikeres művelet igaz| |
|nyilvános bool DoExec()|A lefordított eredmény végrehajtása|Sikeres művelet igaz| |
|nyilvános bool DoRun()|Futtassa a U-SQL parancsfájlt (fordítási + végrehajtás)|Sikeres művelet igaz| |
|nyilvános bool IsValidRuntimeDir (karakterlánc elérési út)|Annak ellenőrzése, hogy a megadott elérési út érvényes futásidejű elérési útja|Igaz a érvényes|Futásidejű könyvtár elérési útja|


## <a name="faq-about-common-issue"></a>Közös problémával kapcsolatos gyakori kérdések

### <a name="error-1"></a>1. hiba:
E_CSC_SYSTEM_INTERNAL: Belső hiba! Nem sikerült betölteni a fájl vagy szerelvény "ScopeEngineManaged.dll" vagy annak valamelyik függősége. A megadott modul nem található.

Ellenőrizze a következőket:

- Győződjön meg arról, hogy x64 környezetben. A build célplatform és a tesztkörnyezet kell x64, tekintse meg **1. lépés: egység létrehozása a C# projekt és konfigurációs teszteléséhez** felett.
- Ellenőrizze, hogy NugetPackage\build\runtime\ található összes függőség fájl munkakönyvtár projekthez másolta.


## <a name="next-steps"></a>További lépések

* A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével).
* Diagnosztikai adatok naplózására, lásd: [diagnosztikai naplók elérése az Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Azure Data Lake Analytics használatával webhelyek naplóinak elemzése](data-lake-analytics-analyze-weblogs.md).
* Feladat részleteinek megtekintése: [használata feladat böngésző és az Azure Data Lake Analytics-feladatok feladat megtekintése](data-lake-analytics-data-lake-tools-view-jobs.md).
* Ha szeretné használni a vertex végrehajtási nézetet, lásd [a Vertex végrehajtási nézetet használja a Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
