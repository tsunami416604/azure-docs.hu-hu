---
title: U-SQL-feladatok futtatása helyileg – Azure Data Lake U-SQL SDK
description: Megtudhatja, hogyan futtathatja és tesztelheti az U-SQL feladatokat helyileg a helyi munkaállomás parancssori és programozási felületeivel.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanacai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 03/01/2017
ms.openlocfilehash: 51d9060eaf4b30c696ef2a3b5f798a31e2f2a98a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309680"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Az U-SQL futtatása és tesztelése az Azure Data Lake U-SQL SDK-val

Az U-SQL-parancsfájl fejlesztése során gyakori, hogy az U-SQL-parancsfájlt helyileg futtatják és tesztelik, mielőtt a felhőbe küldenék. Az Azure Data Lake egy Azure Data Lake U-SQL SDK nevű Nuget csomagot biztosít ehhez a forgatókönyvhöz, amelyen keresztül könnyedén skálázhatja az U-SQL futtatást és tesztelést. Az U-SQL teszt integrálható a CI (Continuous Integration) rendszerrel is a fordítás és a tesztelés automatizálásához.

Ha szeretné, hogyan manuálisan futtathatja és debug U-SQL-parancsfájl GUI eszköz, majd használhatja az Azure Data Lake Tools for Visual Studio számára. Többet itt [.](data-lake-analytics-data-lake-tools-local-run.md)

## <a name="install-azure-data-lake-u-sql-sdk"></a>Az Azure Data Lake U-SQL SDK telepítése

Az Azure Data Lake U-SQL [SDK-t itt,](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) Nuget.org kaphatja meg. És mielőtt használná, meg kell győződnie arról, hogy függőségei vannak az alábbiak szerint.

### <a name="dependencies"></a>Függőségek

A Data Lake U-SQL SDK a következő függőségeket igényli:

- [Microsoft .](https://www.microsoft.com/download/details.aspx?id=17851)
- Microsoft Visual C++ 14 és Windows SDK 10.0.10240.0 vagy újabb (a cikkben CppSDK-nak nevezett). A CppSDK kétféleképpen szerezhető be:

  - Telepítse a [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou)alkalmazást . A Program Files mappában egy \Windows Kits\10 mappa található – például C:\Program Files (x86)\Windows Kits\10\. A Windows 10 SDK-verzió a \Windows Kits\10\Lib mappában található. Ha nem látja ezeket a mappákat, telepítse újra a Visual Studio alkalmazást, és a telepítés során mindenképpen válassza ki a Windows 10 SDK-t. Ha ez telepítve van a Visual Studio programmal, az U-SQL helyi fordító automatikusan megtalálja.

    ![Data Lake eszközök a Visual Studio helyi futtatott Windows 10 SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - Telepítse [a Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs)alkalmazást . Az előre csomagolt Visual C++ és Windows SDK fájlok at C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. Ebben az esetben az U-SQL helyi fordító nem találja automatikusan a függőségeket. Meg kell adnia a CppSDK elérési útját. A fájlokat más helyre másolhatja, vagy a többi módon használhatja.

## <a name="understand-basic-concepts"></a>Az alapfogalmak megismerése

### <a name="data-root"></a>Adatgyökér

Az adatgyökér mappa a helyi számítási fiók "helyi tárolója". Ez megegyezik a Data Lake Analytics-fiók Azure Data Lake Store-fiókjával. Egy másik adatgyökér mappára való váltás olyan, mintha egy másik áruházi fiókra váltana. Ha különböző adatgyökérmappákkal szeretne gyakran megosztott adatokat elérni, abszolút elérési utakat kell használnia a parancsfájlokban. Vagy hozzon létre fájlrendszerszimbolikus hivatkozásokat (például **mklinket** az NTFS fájlrendszeren) az adatgyökér-mappa alatt, hogy a megosztott adatokra mutasson.

Az adatgyökér mappa a következőkre szolgál:

- Helyi metaadatok, például adatbázisok, táblák, táblaértékű függvények (TVF-ek) és szerelvények tárolása.
- Keresse meg az U-SQL relatív elérési útjaként definiált bemeneti és kimeneti elérési utakat. A relatív elérési utak használatával egyszerűbben telepítheti u-SQL-projektjeit az Azure-ba.

### <a name="file-path-in-u-sql"></a>Fájl elérési útja az U-SQL-ben

Az U-SQL-parancsfájlokban használhat relatív és helyi abszolút elérési utat is. A relatív elérési út a megadott adatgyökér-mappa elérési útjához viszonyítva van. Azt javasoljuk, hogy használja a "/" útvonal-elválasztót, hogy a parancsfájlok kompatibilisek legyenek a kiszolgálói oldallal. Íme néhány példa a relatív elérési utakra és azok megfelelő abszolút útvonalaira. Ezekben a példákban a C:\LocalRunDataRoot az adatgyökér mappa.

|Relatív elérési út|Abszolút elérési út|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Munkakönyvtár

Ha az U-SQL parancsfájlt helyileg futtatja, egy munkakönyvtár jön létre az aktuális futó könyvtár alatti fordítás során. A fordítási kimenetek en kívül a helyi végrehajtáshoz szükséges futásidejű fájlok árnyékmásolata lesz ebbe a munkakönyvtárba. A munkakönyvtár gyökérmappája a "ScopeWorkDir" névre lép, és a munkakönyvtár alatti fájlok a következők:

|Könyvtár/fájl|Könyvtár/fájl|Könyvtár/fájl|Meghatározás|Leírás|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Futásidejű verzió kivonatsora|A helyi végrehajtáshoz szükséges futásidejű fájlok árnyékmásolata|
| |Script_66AE4909AA0ED06C| |Parancsfájl neve + parancsfájlelérési út kivonata|Összeállítási kimenetek és végrehajtási lépés naplózása|
| | |\_parancsfájl\_.abr|Fordító kimenete|Algebra fájl|
| | |\_ScopeCodeGen\_.*|Fordító kimenete|Létrehozott felügyelt kód|
| | |\_ScopeCodeGenEngine\_.*|Fordító kimenete|Létrehozott natív kód|
| | |hivatkozott szerelvények|Összeállítás hivatkozása|Hivatkozott összeállítási fájlok|
| | |deployed_resources|Erőforrás-telepítés|Erőforrás-telepítési fájlok|
| | |xxxxxxxx.xxx[1..n]\_\*.*|Végrehajtási napló|Végrehajtási lépések naplózása|


## <a name="use-the-sdk-from-the-command-line"></a>Az SDK használata a parancssorból

### <a name="command-line-interface-of-the-helper-application"></a>A segítő alkalmazás parancssori felülete

Az SDK-könyvtár\build\runtime területen a LocalRunHelper.exe az a parancssori segítő alkalmazás, amely a legtöbb általánosan használt helyi futtatási függvényhez biztosít felületeket. Ne feledje, hogy mind a parancs, mind az argumentumkapcsolók a kis- és nagybetűket megkülönböztetők. A meghíváshoz:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Futtassa a LocalRunHelper.exe-t argumentumok nélkül vagy a **súgókapcsolóval** a súgó információk megjelenítéséhez:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

A súgóban:

-  **A parancs** megadja a parancs nevét.  
-  **A Kötelező argumentum** felsorolja azokat az argumentumokat, amelyeket meg kell adni.  
-  **A Választható argumentum argumentumok** nem kötelező, alapértelmezett értékekkel rendelkező argumentumokat sorolnak fel.  A választható logikai argumentumok nem rendelkeznek paraméterekkel, és megjelenésük negatív az alapértelmezett értékhez képest.

### <a name="return-value-and-logging"></a>Visszatérési érték és naplózás

A segítő alkalmazás **0-t** ad vissza a sikeres és **-1** a hiba. Alapértelmezés szerint a segítő az összes üzenetet elküldi az aktuális konzolnak. A legtöbb parancs azonban támogatja a **-MessageOut path_to_log_file** választható argumentumot, amely a kimeneteket egy naplófájlba irányítja át.

### <a name="environment-variable-configuring"></a>Környezeti változó konfigurálása

Az U-SQL helyi futtatása egy megadott adatgyökeret igényel helyi tárfiókként, valamint egy megadott CppSDK-elérési utat a függőségekhez. Az argumentumot egyszerre állíthatja be parancssorban, vagy környezeti változót is.

- Állítsa be a **SCOPE_CPP_SDK** környezeti változót.

    Ha a Microsoft Visual C++ és a Windows SDK alkalmazást a Data Lake Tools for Visual Studio telepítésével kapja meg, ellenőrizze, hogy rendelkezik-e a következő mappával:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Adjon meg egy új környezeti **változót, amelynek neve SCOPE_CPP_SDK,** hogy erre a könyvtárra mutasson. Vagy másolja a mappát a másik helyre, és adja meg **SCOPE_CPP_SDK.**

    A környezeti változó beállítása mellett a **-CppSDK** argumentumot is megadhatja a parancssor használatakor. Ez az argumentum felülírja az alapértelmezett CppSDK környezeti változót.

- Állítsa be a **LOCALRUN_DATAROOT** környezeti változót.

    Definiáljon egy új környezeti **változót, amely nek LOCALRUN_DATAROOT,** amely az adatgyökérre mutat.

    A környezeti változó beállítása mellett a **-DataRoot** argumentumot parancssor használata esetén is megadhatja az adatgyökér elérési úttal. Ez az argumentum felülírja az alapértelmezett adatgyökér-környezeti változót. Ezt az argumentumot hozzá kell adnia minden futtatott parancssorhoz, hogy felülírhassa az alapértelmezett adatgyökér-környezeti változót az összes művelethez.

### <a name="sdk-command-line-usage-samples"></a>SDK parancssori használati minták

#### <a name="compile-and-run"></a>Fordítás és futtatás

A **futtatási** parancs a parancsfájl fordítására, majd a lefordított eredmények végrehajtására szolgál. Parancssori argumentumai a **fordításból** és **a végrehajtásból**származó argumentumok kombinációját képezik.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

A **futtatáshoz**nem kötelező argumentumok tartoznak:


|Argumentum|Alapértelmezett érték|Leírás|
|--------|-------------|-----------|
|-CodeBehind|False (Hamis)|A szkript .cs kód mögött|
|-CppSDK| |CppSDK könyvtár|
|-DataRoot| DataRoot környezeti változó|DataRoot helyi futtatáshoz, alapértelmezés szerint "LOCALRUN_DATAROOT" környezeti változó|
|-MessageOut| |Üzenetek kiírása a konzolon fájlba|
|-Párhuzamos|1|A terv futtatása a megadott párhuzamossszal|
|-Referenciák| |A mögötted lévő kód további hivatkozási szerelvényeihez vagy adatfájljaihoz vezető elérési utak listája, ";"|
|-UdoRedirect|False (Hamis)|Udo-összeállítás átirányításának létrehozása konfiguráció|
|-UseDatabase|master|Ideiglenes kódösszeállítás-regisztráció mögötti kódhoz használandó adatbázis|
|-Részletes|False (Hamis)|Részletes kimenetek megjelenítése a futásidejű|
|-WorkDir|Aktuális könyvtár|Könyvtár a fordító használatához és kimenetéhez|
|-RunScopeCEP|0|ScopeCEP mód használata|
|-ScopeCEPTempPath|Temp|A streamelési adatokhoz használandó ideiglenes elérési út|
|-OptFlags| |Vesszővel tagolt optimalizálójelzők listája|


Például:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Mellett ötvözi **lefordítani** és **végrehajtani**, akkor fordítsa le és hajtsa végre a lefordított futtatható külön-külön.

#### <a name="compile-a-u-sql-script"></a>U-SQL parancsfájl fordítása

A **fordítási** parancs segítségével egy U-SQL parancsfájlt lehet végrehajtható fájlokba fordítani.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

A **fordításhoz**nem kötelező argumentumok:


|Argumentum|Leírás|
|--------|-----------|
| -CodeBehind [alapértelmezett érték"Hamis"]|A szkript .cs kód mögött|
| -CppSDK [alapértelmezett érték '']|CppSDK könyvtár|
| -DataRoot [alapértelmezett érték "DataRoot környezeti változó"]|DataRoot helyi futtatáshoz, alapértelmezés szerint "LOCALRUN_DATAROOT" környezeti változó|
| -MessageOut [alapértelmezett érték '']|Üzenetek kiírása a konzolon fájlba|
| -Hivatkozások [alapértelmezett érték '']|A mögötted lévő kód további hivatkozási szerelvényeihez vagy adatfájljaihoz vezető elérési utak listája, ";"|
| -Shallow [alapértelmezett érték 'Hamis']|Sekély fordítás|
| -UdoRedirect [alapértelmezett érték" Hamis']|Udo-összeállítás átirányításának létrehozása konfiguráció|
| -UseDatabase [alapértelmezett érték 'fő']|Ideiglenes kódösszeállítás-regisztráció mögötti kódhoz használandó adatbázis|
| -WorkDir [alapértelmezett érték 'Current Directory']|Könyvtár a fordító használatához és kimenetéhez|
| -RunScopeCEP [alapértelmezett érték '0']|ScopeCEP mód használata|
| -ScopeCEPTempPath [alapértelmezett érték 'temp']|A streamelési adatokhoz használandó ideiglenes elérési út|
| -OptFlags [alapértelmezett érték '']|Vesszővel tagolt optimalizálójelzők listája|


Íme néhány használati példa.

U-SQL parancsfájl fordítása:

    LocalRunHelper compile -Script d:\test\test1.usql

Állítson le egy U-SQL parancsfájlt, és állítsa be az adatgyökér mappát. Ne feledje, hogy ez felülírja a beállított környezeti változót.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Állítson le egy U-SQL parancsfájlt, és állítson be egy munkakönyvtárat, referenciaszerelvényt és adatbázist:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Lefordított eredmények végrehajtása

A **execute** parancs a lefordított eredmények végrehajtására szolgál.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

A **végrehajtáshoz**nem kötelező argumentumok tartoznak:

|Argumentum|Alapértelmezett érték|Leírás|
|--------|-------------|-----------|
|-DataRoot | '' |Adatgyökér a metaadatok végrehajtásához. Alapértelmezés szerint a **LOCALRUN_DATAROOT** környezeti változó.|
|-MessageOut | '' |Üzenetek kiírása a konzolon egy fájlba.|
|-Párhuzamos | '1' |A létrehozott helyi futtatási lépések futtatására a megadott párhuzamossági szinttel való futtatása jelző.|
|-Részletes | "Hamis" |A futásidejű részletes kimenetek megjelenítésére mutató mutató.|

Íme egy használati példa:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>Az SDK használata programozási felületekkel

A programozási felületek mind a LocalRunHelper.exe-ben találhatók. Ezekkel integrálhatja az U-SQL SDK és a C# teszt keretrendszer funkcióit az U-SQL-parancsfájl helyi tesztjének méretezéséhez. Ebben a cikkben a szabványos C# egység tesztprojektet fogom használni, hogy megmutassam, hogyan használhatja ezeket a felületeket az U-SQL parancsfájl teszteléséhez.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>1. lépés: C# egységteszt-projekt és konfiguráció létrehozása

- C# egységtesztprojektet hozhat létre a File > New > Project > Visual C# > Test > Unit Test Project alkalmazáson keresztül.
- Adja hozzá a LocalRunHelper.exe programot referenciaként a projekthez. A LocalRunHelper.exe a Nuget csomag \build\runtime\LocalRunHelper.exe webhelyén található.

    ![Azure Data Lake U-SQL SDK – hivatkozás hozzáadása](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- Az U-SQL SDK **csak** az x64-es környezetet támogatja, győződjön meg arról, hogy a buildplatform-célt x64-ként állítja be. Ezt beállíthatja a Project Property > Build > Platform cél.

    ![Azure Data Lake U-SQL SDK konfigurálása x64-es projekt](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Ügyeljen arra, hogy a tesztkörnyezetet x64-re állítsa. A Visual Studio programban beállíthatja a Tesztelés i > tesztelési beállítások > alapértelmezett processzorarchitektúrát > x64.

    ![Azure Data Lake U-SQL SDK konfigurálása x64 tesztkörnyezet](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Győződjön meg arról, hogy a NugetPackage\build\runtime\ mappában lévő összes függőségi fájlt átmásolja a project munkakönyvtárába, amely általában a ProjectFolder\bin\x64\Debug mappában található.

### <a name="step-2-create-u-sql-script-test-case"></a>2. lépés: U-SQL parancsfájl teszteset létrehozása

Az alábbiakban az U-SQL parancsfájlteszt mintakódja látható. A teszteléshez parancsfájlokat, bemeneti fájlokat és várt kimeneti fájlokat kell készítenie.

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


### <a name="programming-interfaces-in-localrunhelperexe"></a>Programozási felületek a LocalRunHelper.exe-ben

LocalRunHelper.exe biztosítja a programozási felületek U-SQL helyi fordítás, futtatás, stb. Az interfészek a következőképpen vannak felsorolva.

**Konstruktor**

nyilvános LocalRunHelper([System.IO.TextWriter messageOutput = null])

|Paraméter|Típus|Leírás|
|---------|----|-----------|
|messageOutput|System.IO.TextWriter|kimeneti üzenetek esetén null értékre állítva a konzol használata|

**Tulajdonságok**

|Tulajdonság|Típus|Leírás|
|--------|----|-----------|
|AlgebraPath|sztring|Az algebra fájl elérési útja (az algebrafájl az egyik összeállítási eredmény)|
|CodeBehindReferences|sztring|Ha a parancsfájl további kóddal rendelkezik a hivatkozások mögött, adja meg a ";" szöveggel elválasztott elérési utakat.|
|CppSdkDir|sztring|CppSDK könyvtár|
|CurrentDir (Aktuális könyvtár)|sztring|Aktuális könyvtár|
|DataRoot|sztring|Adatgyökér elérési útja|
|HibakeresőMailPath|sztring|A hibakereső mailslot elérési útja|
|GenerateUdoRedirect|Bool|Ha azt akarjuk, hogy létrehoz szerelvény betöltése átirányítás felülírja config|
|HasCodeMögött|Bool|Ha a szkript mögött kód van|
|Bemeneti könyvtár|sztring|Bemeneti adatok könyvtára|
|MessagePath (MessagePath)|sztring|Üzenetkiírás fájljának elérési útja|
|Kimeneti könyvtár|sztring|A kimeneti adatok könyvtára|
|Párhuzamosság|int|Párhuzamosság futtatni az algebra|
|ParentPid|int|Annak a szülőnek a PID azonosítója, amelyből a szolgáltatás kilép, 0 vagy negatív beállítás figyelmen kívül hagyáshoz|
|ResultPath (ResultPath)|sztring|Eredménykiírási fájl elérési útja|
|RuntimeDir|sztring|Futásidejű könyvtár|
|ScriptPath (Parancsfájlgörbe)|sztring|Hol található a szkript|
|Sekély|Bool|Sekély fordítás vagy nem|
|TempDir|sztring|Ideiglenes könyvtár|
|UseDataBase|sztring|Adja meg az ideiglenes kódösszeállítás-regisztráció mögötti kódhoz használandó adatbázist, alapértelmezés szerint főadat|
|Munkakönyvtár|sztring|Elsődleges munkakönyvtár|


**Módszer**

|Módszer|Leírás|Vissza|Paraméter|
|------|-----------|------|---------|
|nyilvános bool DoCompile()|Az U-SQL parancsfájl fordítása|Igaz a sikerre| |
|nyilvános bool DoExec()|A lefordított eredmény végrehajtása|Igaz a sikerre| |
|nyilvános bool DoRun()|Az U-SQL parancsfájl futtatása (Fordítás + Végrehajtás)|Igaz a sikerre| |
|nyilvános bool IsValidRuntimeDir(karakterlánc elérési útja)|Annak ellenőrzése, hogy az adott elérési út érvényes-e futásidejű elérési út|Igaz érvényes érvényes|A futásidejű könyvtár elérési útja|


## <a name="faq-about-common-issue"></a>Gyakori kérdések a gyakori problémáról

### <a name="error-1"></a>1. hiba:
E_CSC_SYSTEM_INTERNAL: Belső hiba! Nem sikerült betölteni a "ScopeEngineManaged.dll" fájlt vagy szerelvényt vagy annak egyik függőségét. A megadott modul nem található.

Kérjük, ellenőrizze a következőket:

- Győződjön meg róla, hogy x64 környezetben. A build célplatform és a tesztkörnyezet x64, lásd: **1.**
- Győződjön meg arról, hogy a NugetPackage\build\runtime\ mappában lévő összes függőségi fájlt átmásolta a munkakönyvtár kivetítésébe.


## <a name="next-steps"></a>További lépések

* A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével).
* A diagnosztikai adatok naplózásához olvassa el az [Azure Data Lake Analytics diagnosztikai naplóinak elérése című témakört.](data-lake-analytics-diagnostic-logs.md)
* Összetettebb lekérdezés megtekintéséhez olvassa el [a Webhelynaplók elemzése az Azure Data Lake Analytics használatával című témakört.](data-lake-analytics-analyze-weblogs.md)
* A feladat részleteinek megtekintéséhez olvassa el a [Feladatböngésző és a Feladatnézet használata az Azure Data Lake Analytics-feladatokhoz (Feladatböngésző és feladatnézet használata az Azure Data Lake Analytics-feladatokhoz) témakört.](data-lake-analytics-data-lake-tools-view-jobs.md)
* A csúcspont végrehajtási nézetének használatához olvassa [el A Csúcspont végrehajtási nézet használata a Visual Studio Data Lake Tools alkalmazásban című témakört.](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)
