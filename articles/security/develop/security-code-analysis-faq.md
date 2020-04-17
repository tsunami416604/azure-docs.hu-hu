---
title: Gyakori kérdések a Microsoft biztonsági kódelemzésének dokumentációjában
description: Ez a cikk a Microsoft security code analysis bővítményével kapcsolatos gyakori kérdéseket tartalmazza
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: cb04a8e5a6d8c982a35cb5c448e4b6d93825bf73
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460222"
---
# <a name="frequently-asked-questions"></a>Gyakori kérdések
Kérdése van? További információkért tekintse meg az alábbi GYIK-et.

## <a name="general-faq"></a>Általános gyakori kérdések

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>Telepíthetem a bővítményt a Visual Studio Team Foundation Server-példányomra az Azure DevOps-példány helyett?

Nem. A bővítmény nem tölthető le és telepíthető a Visual Studio Team Foundation Server számára.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>A buildemmel kell futtatnom a Microsoft biztonsági kódelemzést? 

Talán. Ez az elemzőeszköz típusától függ. Lehet, hogy a forráskód az egyetlen dolog, ami szükséges, vagy a build kimenetszükség lehet.

A Hitelesítő adatok olvasója (CredScan) például a kódtár mappastruktúrájában lévő fájlokat elemzi. Az elemzés miatt futtathatja a CredScan és a Security Analysis Logs build feladatok at egy önálló build eredmények et.

Más eszközök, például a BinSkim, amelyek a build elműlés utáni összetevőket elemzik, először a buildszükséges.

### <a name="can-i-break-my-build-when-results-are-found"></a>Megszakíthatom a buildet, ha eredményeket talál?

Igen. A buildelési szünetet akkor vezethet be, ha bármely eszköz problémát vagy problémát jelent a naplófájlban. Csak adja hozzá az Elemzés utáni buildelési feladatot, és jelölje be a jelölőnégyzetet minden olyan eszközhöz, amelynek meg szeretné szakítani a buildet.

Az elemzés utáni feladat felhasználói felületén dönthet úgy, hogy megszakítja a buildet, ha bármely eszköz csak hibákat, vagy hibákat és figyelmeztetéseket is jelent.

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Miben különböznek az Azure DevOps parancssori argumentumai az önálló asztali eszközökben lévő argumentumoktól? 

Az Azure DevOps build feladatok nagy része a biztonsági eszközök parancssori argumentumai közvetlen burkoló. A buildelési feladatnak bármit átadhat argumentumként, amit általában átad egy parancssori eszköznek.

Észrevehető különbségek:

- Az eszközök az ügynök $(Build.SourcesDirectory) forrásmappájából vagy %BUILD_SOURCESDIRECTORY%-ból futnak. Ilyen például a\_C:\agent work\1\s.
- Az argumentumokban lévő elérési utak a korábban felsorolt forráskönyvtár gyökeréhez viszonyíthatók. Az elérési utak is lehetnek abszolútak. Abszolút elérési utakat kap az Azure DevOps Build Variables használatával, vagy egy helyszíni ügynök futtatásával a helyi erőforrások ismert központi telepítési helyekkel.
- Az eszközök automatikusan biztosítanak egy kimeneti fájl elérési útját vagy mappáját. Ha egy buildelési feladat kimeneti helyét adja meg, a rendszer lecseréli a buildügynök naplóinak jól ismert helyére szolgáló elérési utat.
- Egyes eszközök esetében néhány további parancssori argumentum módosul. Egy példa a hozzáadása vagy eltávolítása lehetőségek, amelyek biztosítják, hogy nincs GUI indított.

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Futtathatok egy buildelési feladatot, például a Hitelesítő adatok at az Azure DevOps Build több tárházában?

Nem. A biztonságos fejlesztési eszközök futtatása több adattárak egyetlen folyamat nem támogatott.

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>A megadott kimeneti fájl nem jön létre, vagy nem találom a megadott kimeneti fájlt

A buildfeladatok szűrnek néhány felhasználói bevitelt. Ebben a kérdésben kifejezetten frissítik a létrehozott kimeneti fájl helyét, hogy a buildügynök közös helye legyen. Erről a helyről a következő kérdéseket talál.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Hol vannak a mentett eszközök által generált kimeneti fájlok? 

A buildfeladatok automatikusan hozzáadnak kimeneti útvonalakat a buildügynök ismert\_helyéhez: $(Agent.BuildDirectory) sdt\logs. Mivel ezen a helyen egységesítjük, a kódelemző naplókat előállító vagy felhasználó összes csapat hozzáférhet a kimenethez.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Várólistára tudok állni egy buildet, hogy ezeket a feladatokat egy üzemeltetett buildügynöken futtassam? 

Igen. A bővítményben lévő összes feladat és eszköz végrehajtható egy üzemeltetett buildelőzolon.

>[!NOTE]
> A Kártevőirtó-olvasó buildelési feladathoz olyan buildügynökszükséges, amelyen a Windows Defender engedélyezve van. A Visual Studio 2017-es és újabb üzemeltetése ilyen ügynököt biztosít. A buildfeladat nem fog futni a Visual Studio 2015 üzemeltetett ügynökén.
>
> Bár az aláírások nem frissíthetők ezeken az ügynökökön, az aláírásoknak mindig három óránál rövidebbnek kell lenniük.

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Futtathatom ezeket a buildelési feladatokat egy kiadási folyamat részeként, nem pedig egy buildfolyamat?

A legtöbb esetben igen.

Az Azure DevOps azonban nem támogatja a kiadási folyamatokon belüli feladatok futtatását, ha ezek a feladatok összetevőket tesznek közzé. Ez a támogatás hiánya megakadályozza, hogy a Biztonsági elemzési naplók közzététele feladat sikeresen fusson egy kiadási folyamatban. A feladat ehelyett leíró hibaüzenettel sikertelen.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>Honnan töltik le a buildfeladatok az eszközöket?

A buildfeladatok letölthetik az eszközök NuGet csomagjait az [Azure DevOps csomagkezelési hírcsatornából.](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json) A buildelési feladatok a Node Package Manager t is használhatják, amelyet elő kell telepíteni a buildelőlegre. Egy példa az ilyen telepítés a parancs **npm telepíteni tslint**.

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>Milyen hatással van a bővítmény telepítése az Azure DevOps-szervezetre? 

A telepítésután a bővítmény által biztosított biztonsági buildfeladatok a szervezet összes felhasználója számára elérhetővé válnak. Amikor létrehoz vagy szerkeszt egy Azure Pipeline-t, ezek a feladatok a build-feladat gyűjtemény listájából érhetők el. Ellenkező esetben a bővítmény telepítése az Azure DevOps-szervezet nincs hatása. A telepítés nem módosítja a fiókbeállításokat, a projektbeállításokat vagy a folyamatokat.

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>A bővítmény telepítése módosítja a meglévő Azure-folyamatokat? 

Nem. A bővítmény telepítése elérhetővé teszi a biztonsági buildelési feladatokat a folyamatok kiegészítésére. Továbbra is szükség van a builddefiníciók hozzáadására vagy frissítésére, hogy az eszközök együttműködhessenek a létrehozási folyamattal.

## <a name="task-specific-faq"></a>Feladatspecifikus gyakori kérdések

A feladatok létrehozásához szükséges kérdéseket ebben a szakaszban sorolja fel.

### <a name="credential-scanner"></a>Hitelesítő adatok atszogatása

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>Mik a gyakori elnyomási forgatókönyvek és példák?

Az alábbiakban a két leggyakoribb elnyomási forgatókönyv részleteit olvashatja.

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Egy adott titkos titok összes előfordulásának letiltása a megadott útvonalon belül

A CredScan kimeneti fájl titkos kulcsának kivonatkulcsa szükséges az alábbi minta szerint.

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
                "_justification": "Secret used by MSDN sample, it is fake."
            }
          ]
        }

>[!WARNING]
> A kivonatkulcsot az egyező érték vagy fájltartalom egy része hozza létre. A forráskód bármely módosítása megváltoztathatja a kivonatkulcsot, és letilthatja az elnyomási szabályt.

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Egy adott fájl összes titkos fájljának letiltása vagy a titkos kulcsok fájljának letiltása

A fájlkifejezés fájlnév is lehet. Ez lehet a teljes fájlelérési út vagy egy fájlnév alapneve is. Helyettesítő karakterek nem használhatók.

Az alábbi példák bemutatják, hogyan tilthatja le az InputPath>\src\JS\lib\angular.js fájl letiltását. \<

Példák érvényes elnyomási szabályokra:

- \<Az InputPath>\src\JS\lib\angular.js - letiltja a megadott elérési úton lévő fájlt
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angular.js - letiltja az azonos nevű fájlokat

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "file": "\\files\\AdditonalSearcher.xml", 
                "_justification": "Additional CredScan searcher specific to my team"
            },
            {
                "file": "\\files\\unittest.pfx", 
                "_justification": "Legitimate UT certificate file with private key"
            }
          ]
        }      

>[!WARNING] 
> A fájlhoz hozzáadott összes jövőbeli titok is automatikusan le lesz tiltva.

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>Mik az ajánlott irányelvek a titkok kezelésére?

A következő erőforrások segítségével biztonságosan kezelheti a titkos kulcsokat, és hozzáférhet az alkalmazásokbizalmas adataihoz:

 - [Azure Key Vault](../../key-vault/index.yml)
 - [Azure Active Directory (Azure AD)](../../sql-database/sql-database-aad-authentication.md)
 - [Az Azure AD felügyelt szolgáltatásidentitása (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Azure-erőforrások felügyelt identitásai](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Felügyelt identitások az Azure App Service-ben és az Azure Functionsben](../../app-service/overview-managed-identity.md)
 - [AppAuthentication könyvtár](../../key-vault/general/service-to-service-authentication.md)


További információt a Titkok [biztonságos kezelése a felhőben című blogbejegyzésben talál.](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/)

#### <a name="can-i-write-my-own-custom-searchers"></a>Írhatok saját egyéni keresőknek?

A Hitelesítő adatok olvasója a buildsearchers.xml fájlban gyakran definiált tartalomkeresők készletére támaszkodik. A fájl xml szerializált objektumok tömbjét tartalmazza, amelyek egy **ContentSearcher** objektumot jelölnek. A program ot jól bevált keresőkkel forgalmazzák. De tudod eszköz -a saját szokás keresők túl.

A tartalomkereső meghatározása a következő:

- **Név**: A Hitelesítő ellenőrző kimeneti fájljaiban használandó leíró keresőnév. Azt javasoljuk, hogy használja a teve-eset elnevezési konvenció a kereső nevét.
- **Szabály:** A kereső stabil átlátszatlan azonosítója:
    - A Hitelesítő adatok olvasó alapértelmezett keresőrendelt **ruleId** érték, mint a CSCAN0010, CSCAN0020, vagy CSCAN0030. Az utolsó számjegy a keresőcsoportok reguláris kifejezéseken (regex) keresztüli potenciális egyesítése vagy felosztása számára van fenntartva.
    - A **szabályazonosító** értéke egy testreszabott kereső saját névtérrel kell rendelkeznie. Ilyenek például\<a\>CSCAN- Namespace\<0010, cscan- namespace\>0020 és CSCAN-\<Namespace\>0030.
    - A teljesen minősített keresőnév a **RuleId** érték és a kereső név kombinációja. Ilyen például a CSCAN0010. KeyStoreFiles és CSCAN0020. Base64EncodedCertificate.
- **ResourceMatchPattern**: Regex a fájl kiterjesztések, hogy ellenőrizze a kereső ellen.
- **ContentSearchPatterns**: Regex-utasításokat tartalmazó karakterláncok tömbje. Ha nincs enek keresési minták definiálva, a rendszer a **ResourceMatchPattern** értéknek megfelelő összes fájlt visszaadja.
- **ContentSearchFilters**: Regex utasításokat tartalmazó karakterláncok tömbje a keresőspecifikus hamis pozitív értékek szűrésére.
- **MatchDetails**: A leíró üzenet, a kockázatcsökkentési utasítások, vagy mindkettő hozzá kell adni minden egyezés a kereső.
- **Javaslat**: A PREfast jelentésformátumban egyezésre vonatkozó javaslatok mezőtartalma.
- **Súlyosság:** Egy probléma súlyossági szintjét tükröző egész szám. A legmagasabb súlyossági szint értéke 1.

  ![A Hitelesítő adatok olvasójának beállítását megjelenítő XML](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Roslyn analizátorok

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Milyen gyakori hibák a Roslyn Analizátorok feladat használatakor?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>A projekt visszaállítása a Microsoft.NETCore.App nem megfelelő verziójával történt

A teljes hibaüzenet:

"Hiba: A projekt a Microsoft.NETCore.App *x.x.x*verziójával lett visszaállítva, de az aktuális beállításokkal az *y.y.y* verzió lenne használva. A probléma megoldásához győződjön meg arról, hogy ugyanazokat a beállításokat használja a visszaállításhoz és a további műveletekhez, például a buildhez vagy a közzétételhez. Ez a probléma általában akkor fordulhat elő, ha a RuntimeIdentifier tulajdonság a létrehozás vagy a közzététel során van beállítva, de a visszaállítás során nem."

Mivel a Roslyn-elemzők feladatok a fordítás részeként futnak, a buildelőgépen lévő forrásfának építhető állapotban kell lennie.

Egy lépés a fő build és a Roslyn Analizátorok lépései között előfordulhat, hogy a forrásfát olyan állapotba helyezték, amely megakadályozza az építést. Ez az extra lépés valószínűleg **dotnet.exe közzé**. Próbálja meg meg kettőzése a lépést, hogy nem a NuGet helyreállítása előtt Roslyn Analizátorok lépés. Ez a duplikált lépés visszahelyezheti a forrásfát egy építhető állapotba.

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>A csc.exe nem tud analizátorpéldányt létrehozni

A teljes hibaüzenet:

"'csc.exe' az 1-es hibakóddal távozott - *Az AAAA* analizátor egy példánya nem hozható létre a\\C:*BBBB*.dll : Nem tölthető be a 'Microsoft.CodeAnalysis, Version=*X.X.X.X.X,*, Culture=neutral, PublicKeyToken=31bf3856ad364e35' fájl vagy kódösszeállítás vagy annak egyik függősége. A rendszer nem találja a megadott fájlt."

Győződjön meg róla, hogy a fordító támogatja a Roslyn Analizátorokat. A **csc.exe /version** parancs futtatása 2.6-os vagy újabb verzióértéket jelent.

Előfordulhat, hogy egy .csproj fájl felülírhatja a buildgép Visual Studio-telepítését a Microsoft.Net.Compilers csomagjának hivatkozásával. Ha nem kívánja a fordító egy adott verzióját használni, távolítsa el a Microsoft.Net.Compilers-re mutató hivatkozásokat. Ellenkező esetben győződjön meg arról, hogy a hivatkozott csomag verziója is 2.6 vagy újabb.

Próbálja meg beszerezni a hibanapló elérési útját, amely a **csc.exe /errorlog** kapcsolóban van megadva. A beállítás és az elérési út megjelenik a Roslyn-elemzők build-feladat naplójában. A következőkre tűnhetnek: **/errorlog:F:\ts-services-123\_work\456\s\Some\Project\Code\Code.csproj.sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>A C# fordító verzió nem elég friss

A C# fordító legújabb verzióinak beszerezéséhez látogasson el a [Microsoft.Net.Compilers .](https://www.nuget.org/packages/Microsoft.Net.Compilers) A telepített verzió beszerezéséhez futtassa a **csc.exe /version parancsot** egy parancssorból. Győződjön meg arról, hogy a Microsoft.Net.Compilers NuGet 2.6-os vagy újabb verziójú csomagjára hivatkozik.

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>Az MSBuild és a VSBuild naplók nem találhatók

A Roslyn-elemzők build feladat kell lekérdezni az Azure DevOps az MSBuild napló az MSBuild build feladat. Ha az elemző feladat közvetlenül az MSBuild feladat után fut, a napló még nem lesz elérhető. Helyezzen el más feladatokat az MSBuild feladat és a Roslyn-elemzők feladat között. Más feladatok ra van példa: BinSkim és Anti-Malware Scanner.

## <a name="next-steps"></a>További lépések

Ha további segítségre van szüksége, a Microsoft biztonsági kódelemzési támogatása hétfőtől péntekig 9:00 és 17:00 óra között érhető el a csendes-óceáni téli idő szerint.

- Bevezetés: Tekintse meg [a bevezetési dokumentációt](security-code-analysis-onboard.md)
  
- Támogatás: E-mailben a [microsoftos biztonsági kódelemzési támogatási szolgálatának](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)