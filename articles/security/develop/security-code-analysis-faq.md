---
title: Microsoft Azure biztonsági kód elemzése – gyakori kérdések
description: Ez a cikk a biztonsági kód elemzési bővítménnyel kapcsolatos GYIK-információkat tartalmaz
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
ms.openlocfilehash: 1fc5e83c2c46a7da2a4b56879a2d596405fd8bee
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718313"
---
# <a name="frequently-asked-questions"></a>Gyakori kérdések
Kérdése van? További információért tekintse meg az alábbi gyakori kérdéseket.

## <a name="general-faqs"></a>Általános gyakori kérdések

### <a name="can-i-install-the-extension-on-my-tfs-not-azure-devops-server"></a>Telepíthetem a bővítményt a TFS (nem Azure DevOps) kiszolgálóra? 

Nem, a bővítmény nem érhető el a TFS letöltéséhez és telepítéséhez.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Futtatni kell a Microsoft biztonsági kód elemzését a buildtel? 

Igen és nem. Az Analysis Tool típusától függően maga a forráskód lehet az egyetlen szükséges dolog, vagy szükség lehet a Build kimenetére. Mivel például a hitelesítőadat-olvasó elemzi a fájlokat a tár mappastruktúrát, futtathatja a hitelesítőadat-olvasót, és közzéteheti a biztonsági elemzési naplókat egy önálló buildben az eredmények lekéréséhez.
A post Build-összetevőket (például BinSkim) elemező egyéb eszközök esetében először a buildre lesz szükség.

### <a name="can-i-break-my-build-when-results-are-found"></a>Megtörhetem a buildet, ha az eredmények megtalálhatók? 
Igen, bevezetheti a létrehozási megszakítást, ha bármely eszköz egy problémát jelez, a naplófájlban. Csak adja hozzá az elemzés utáni felépítési feladatot, és jelölje be a jelölőnégyzetet minden olyan eszközhöz, amelyre el szeretné törni a buildet. Dönthet úgy is, hogy az elemzés utáni feladat felhasználói felületén megszakítja a buildet, ha bármely eszköz hibát vagy figyelmeztetést vagy hibát jelez.

### <a name="how-are-the-command-line-arguments-different-in-azure-devops-than-they-are-in-the-standalone-desktop-tools"></a>Miben különböznek a parancssori argumentumok az Azure DevOps, mint a különálló asztali eszközökön? 

A legtöbb esetben az Azure DevOps felépítési feladatai a biztonsági eszközök parancssori argumentumai körüli közvetlen burkolók. Bármit, amit általában a parancssorból az asztalról továbbít az eszközre, a felépítési feladat argumentumok bemenetét átadhatja.
Az alábbi lista a észrevehető különbségek listáját tartalmazza:
 - Az eszköz az ügynök $ (Build. SourcesDirectory) vagy% BUILD_SOURCESDIRECTORY% forrás mappájából lesz végrehajtva. Példa: C:\agent\_work\1\s 
 - Az argumentumok elérési útja a fent felsorolt forrás könyvtára gyökeréhez képest relatív lehet, vagy a helyi erőforrások ismert központi telepítési helyével vagy az Azure DevOps Build változók használatával.
 - Az eszközök automatikusan megadja a kimeneti fájl elérési útját vagy mappáját, ha kimeneti elérési utat ad meg, a rendszer eltávolítja és lecseréli a jól ismert naplók helyének elérési útját a Build-ügynökön.
 - Néhány további parancssori paraméter megtisztítása és eltávolítása bizonyos eszközökön történik, például a beállítások hozzáadásával vagy eltávolításával, így biztosítva, hogy a grafikus felhasználói felület ne legyen elindítva.

### <a name="can-i-run-a-build-task-for-example-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Futtathatok egy felépítési feladatot (például a hitelesítőadat-olvasót) több tárházban egy Azure DevOps-Builden? 

Nem, a biztonságos fejlesztési eszközök egyetlen folyamaton belüli több tárházra való futtatása jelenleg nem támogatott.

###  <a name="the-output-file-i-specified-is-not-being-created--i-cant-find-the-output-file-i-specified"></a>A megadott kimeneti fájl nincs létrehozva/nem található a megadott kimeneti fájl 

A felépítési feladatok jelenleg megtisztítják a felhasználói adatokat, és frissítik a létrehozott kimeneti fájl helyét a Build-ügynök közös helyére. További információ erről a helyről: az alábbi kérdések.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Hol jönnek létre a mentett eszközök által létrehozott kimeneti fájlok? 

A Build Tasks automatikusan hozzáadja a kimeneti elérési utakat a következő jól ismert helyhez a Build Agent $ (Agent\_. BuildDirectory) sdt\logs. Ennek a helynek a szabványosításával garantálható, hogy más csapatok is hozzáférjenek a Code Analysis-naplókat előállító más csapatokhoz.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Várólistára helyezhetek egy buildet a feladatok futtatásához egy üzemeltetett Build-ügynökön? 

Igen, a bővítmény összes feladatát és eszközét futtathatja egy üzemeltetett Build-ügynökön.

>[!NOTE]
> A kártevő szoftvereket felépítő feladathoz olyan Build ügynökre van szükség, amelyen engedélyezve van a Windows Defender, ami igaz a "üzemeltetett VS2017" vagy az újabb Build ügynökökre. (Nem fog futni az örökölt/VS2015 "tárolt" ügynökön.) Az aláírások nem frissíthetők ezen ügynökökön, de az aláírásnak mindig viszonylag aktuálisnak kell lennie, és kevesebb, mint 3 óra.
>

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Futtathatom ezeket a felépítési feladatokat a kiadási folyamat részeként (a létrehozási folyamat helyett)? 
A legtöbb esetben igen. Az Azure DevOps azonban nem támogatja az összetevők közzétételét a kiadási folyamatokon belül: "A kiadással nem rendelkező feladatok egyetlen kategóriája az összetevők közzététele. Ennek az az oka, hogy jelenleg nem támogatjuk az összetevők kiadáson belüli közzétételét.
Ez megakadályozza, hogy a "biztonsági elemzési naplók közzététele" feladat sikeresen fusson a kiadási folyamatból; a művelet sikertelen lesz, és leíró hibaüzenetet jelenít meg.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>Honnan töltheti le az eszközöket a Build-feladatok közül? 
A (z) build feladatait letölti a NuGet-csomagokat az alábbi [Azure DevOps csomagkezelő](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json) -hírcsatornából vagy a Node csomagkezelő-kezelőből, amelyet előre kell telepíteni a Build-ügynökön (például: "NPM install tslint").

### <a name="what-effect-will-installing-the-extension-have-on-my-azure-devops-organization"></a>Milyen hatással lesz a bővítmény telepítése az Azure DevOps-szervezetre? 

A telepítés után a bővítmény által biztosított biztonsági Build-feladatok elérhetővé válnak a szervezet összes felhasználója számára. Az Azure-folyamatok létrehozásakor vagy szerkesztésekor ezek a feladatok elérhetők lesznek a felépítési feladatok gyűjteményének listájából való hozzáadáshoz. Ellenkező esetben a bővítmény Azure DevOps-szervezetben való telepítése nincs hatással. Nem módosítja a fiók-és a projekt beállításait, illetve a folyamatokat.

### <a name="will-installing-the-extension-modify-my-existing-azure-pipelines"></a>A bővítmény telepítése a meglévő Azure-folyamatokat módosítja? 

Nem. A bővítmény telepítése az Azure-folyamatokhoz való hozzáadás érdekében elérhetővé teszi a biztonsági Build-feladatokat. A felhasználóknak továbbra is szükségük van a Build-definíciók hozzáadására vagy frissítésére, hogy integrálják az eszközöket a létrehozási folyamatba.

## <a name="task-specific-faqs"></a>Feladat-specifikus gyakori kérdések

Ebben a szakaszban a feladatok felépítésére vonatkozó gyakori kérdések jelennek meg.

### <a name="credential-scanner-faqs"></a>Hitelesítőadat-olvasó – gyakori kérdések

#### <a name="what-are-common-suppressions-scenarios-and-examples"></a>Mik azok a gyakori mellőzési forgatókönyvek és példák? 
A leggyakoribb elnyomási forgatókönyvek közül kettő a következő:
##### <a name="suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Egy adott titok összes előfordulásának letiltása a megadott elérési úton 
A hitelesítő adatok szkennerének kimeneti fájljából származó titkos kód kivonatának kulcsát az alábbi mintában látható módon kell megadni
   
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
> A kivonatoló kulcsot a rendszer az egyező érték vagy a fájl tartalmának egy részével hozza létre. Bármely forráskód-változat módosíthatja a kivonatoló kulcsot, és letilthatja az elnyomási szabályt. 

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Egy adott fájl összes titkának letiltásához (vagy maga a titkos fájl mellőzéséhez) 
A fájl kifejezés lehet a fájl neve vagy a fájl teljes elérési útja/neve. Helyettesítő karakterek nem használhatók. 

**Példa** 

A letiltani kívánt fájl: [InputPath] \src\JS\lib\angular.js 

Érvényes letiltási szabályok: 
- [InputPath] \src\JS\lib\angular.js – a fájl letiltása a megadott elérési úton
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- szögletes. js – minden azonos nevű fájl letiltása

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
> A rendszer a fájlhoz hozzáadott összes jövőbeli titkot is automatikusan letiltja. 

#### <a name="what-are-recommended-secrets-management-guidelines"></a>Mik azok az ajánlott titkokat kezelő irányelvek? 
A nehezen kódolt titkos kulcsok időben történő észlelése és a kockázatok enyhítése mellett hasznos lehet, ha az egyik a titkos kódok teljes körű beadását is megakadályozhatja. Ebben a tekintetben a Microsoft a Visual studióhoz készült [Microsoft DevLabs bővítmény](https://marketplace.visualstudio.com/items?itemName=VSIDEDevOpsMSFT.ContinuousDeliveryToolsforVisualStudio) részeként kiadta a CredScan Code Analyzert. A korai előzetes verzióban a fejlesztők inline élményt biztosítanak a programkódban rejlő lehetséges titkok észleléséhez, így a problémák valós időben való kijavítását is lehetővé teszik. További információkért tekintse meg [ezt](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/) a blogot a titkok biztonságos kezeléséhez a felhőben. Az alábbiakban néhány további erőforrást talál a titkok kezeléséhez és a bizalmas információk biztonságos eléréséhez az alkalmazásokon belül: 
 - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
 - [Azure Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)
 - [Azure AD-Managed Service Identity](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Felügyeltszolgáltatás-identitás (MSI) Azure-erőforrásokhoz](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
 - [Azure-Managed Service Identity](https://docs.microsoft.com/azure/app-service/overview-managed-identity)
 - [AppAuthentication-könyvtár](https://docs.microsoft.com/azure/key-vault/service-to-service-authentication)

#### <a name="can-i-write-my-own-custom-searchers"></a>Írhatok saját egyéni keresőket?

A hitelesítő adatok képolvasó a **buildsearchers. XML** fájlban gyakran definiált tartalomszolgáltatókon alapul. A fájl a ContentSearcher objektumot képviselő XML szerializált objektumok tömbjét tartalmazza. A program egy jól tesztelt keresővel van elterjesztve, de lehetővé teszi saját egyéni keresőik megvalósítását is. 

A tartalmi kereső a következőképpen van definiálva: 

- **Name (név** ) – a hitelesítőadat-olvasó kimeneti fájljában használandó leíró kereső neve. A keresési nevekhez a teve-eset elnevezési konvenció használata javasolt. 
- **RuleId** – a kereső stabil, átlátszatlan azonosítójának azonosítója. 
    - A hitelesítő adatok Lapolvasójának alapértelmezett keresői olyan RuleIds vannak hozzárendelve, mint a CSCAN0010, a CSCAN0020, a CSCAN0030 stb. Az utolsó számjegy a lehetséges kereső regex-csoport egyesítése vagy osztása.
    - A testreszabott keresők RuleId a következő formátumban kell megadni: CSCAN-{Namespace} 0010, CSCAN-{névtér} 0020, CSCAN-{Namespace} 0030 stb.
    - A teljes kereső neve a RuleId és a kereső nevének kombinációja. Példa CSCAN0010. KeyStoreFiles, CSCAN0020. Base64EncodedCertificate stb.
- **ResourceMatchPattern** – a keresőben ellenőrizhető fájlkiterjesztések regexje
- **ContentSearchPatterns** – a megfelelő regex-utasításokat tartalmazó karakterláncok tömbje. Ha nincs megadva keresési minta, a rendszer az erőforrás-egyeztetési mintának megfelelő összes fájlt visszaadja.
- **ContentSearchFilters** – a keresőmotor-specifikus hamis pozitív értékek szűrésére szolgáló regex-utasításokat tartalmazó karakterláncok tömbje.
- **Matchdetails** – leíró üzenet és/vagy kockázatcsökkentő utasítás, amelyet hozzá kell adni a kereső minden egyeztetéséhez.
- **Javaslat** – a javaslatok mező tartalmának megadása az előgyors jelentés formátumát használó egyezésekhez.
- **Súlyosság** – egy egész szám, amely a probléma súlyosságát tükrözi (a legmagasabb = 1).
![Hitelesítőadat-olvasó beállítása](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers-faqs"></a>A és a-elemzők gyakori kérdései

#### <a name="what-are-the-most-common-errors-when-using-the-roslyn-analyzers-task"></a>Melyek a leggyakrabban előforduló hibák a a és a-elemzők feladatának használatakor?

**Hiba: A projekt a Microsoft. NETCore. app x. x. x verziójával lett visszaállítva, de az aktuális beállításokkal az y. y. y verzió lesz használatban. A probléma megoldásához ellenőrizze, hogy ugyanazokat a beállításokat használja-e a rendszer a visszaállításhoz és a későbbi műveletekhez, például a létrehozáshoz vagy a közzétételhez. Ez a probléma általában akkor fordulhat elő, ha a RuntimeIdentifier tulajdonságot a Build vagy a közzététel során beállították, de nem a visszaállítás során:**

A (z) fordításának részeként a (z) rendszerű a faszerkezetű elemzők futnak, ezért a felépített gépnek a forrás fájának kiépíthető állapotban kell lenni Előfordulhat, hogy a fő Build és a (z) "DotNet. exe publish" (valószínűleg "DotNet. exe publish") egyik lépése nem kiépíthető állapotba helyezte a forrás fát. Előfordulhat, hogy a Nuget visszaállítási lépését megkettőzött, közvetlenül a-elemzők lépése előtt, a forrás fáját kiépíthető állapotba állítja vissza.

**"CSC. exe" kilépett a következő hibakóddal: 1 – az Analyzer AAAA-példánya nem hozható létre a C:\BBBB.dll: Nem tölthető be a következő fájl vagy szerelvény: "Microsoft. CodeAnalysis, Version = X. X. x. X, Culture = semleges, PublicKeyToken = 31bf3856ad364e35" vagy annak valamelyik függősége. A számítógép nem találja a megadott fájlt.**

Győződjön meg arról, hogy a fordító támogatja a a a a a a "a" a "CSC. exe Versioning" legalább v 2.6. x jelentést jelent. Bizonyos esetekben előfordulhat, hogy az egyéni. csproj fájlok felülbírálják a Build Machine Visual Studio telepítését a Microsoft.Net. Compilers csomagból való hivatkozással. Ha a fordító egy adott verzióját használja, akkor távolítsa el a Microsoft.Net. Compilers mutató hivatkozásait. Ellenkező esetben győződjön meg arról, hogy a hivatkozott csomag is legalább v 2.6. x. Próbálja meg lekérni a naplófájlt, amelyet a CSC. exe parancssorból a/errorlog: paraméterben talál (ez a (z)-build feladat naplójában található). Valahogy így néz ki:/errorlog: f:\ts-Services-123\_work\456\s\Some\Project\Code\Code.csproj.Sarif

**A C# fordító nem elég új (> = 2,6)**

A C# fordító legújabb verziói itt jelennek meg: https://www.nuget.org/packages/Microsoft.Net.Compilers. A telepített verziójának lekéréséhez futtassa `C:\>csc.exe /version` a parancsot a parancssorból. Győződjön meg arról, hogy nincs hivatkozása a Microsoft.Net. Compilers NuGet-csomagra, amely < v 2.6-os.

**Az MSBuild/VSBuild naplók nem találhatók**

A feladat működése miatt a feladatnak le kell kérdezni az MSBuild-napló Azure-DevOps az MSBuild-Build feladatból. Ha ez a feladat közvetlenül az MSBuild felépítési feladat után fut, a napló még nem lesz elérhető; Helyezzen el más felépítési feladatokat, beleértve a SecDevTools felépítési feladatokat, például a Binskim, a kártevő-ellenőrzéseket és egyebeket. 

## <a name="next-steps"></a>További lépések

Ha további segítségre van szüksége, a Microsoft biztonsági kód elemzésének támogatását hétfőtől péntekig, 9:00-5:00, csendes-óceáni téli idő szerint lehet elérni

  - Előkészítés – a kezdéshez forduljon a technikai menedzserekhez. 
  >[!NOTE] 
  >Ha még nem rendelkezik fizetős támogatási kapcsolattal a Microsofttal, vagy ha olyan támogatási ajánlattal rendelkezik, amely nem teszi lehetővé a szolgáltatások megvásárlását a Phoenix-katalógusból, további információért látogasson el a [támogatási szolgáltatások kezdőlapjára](https://www.microsoft.com/enterprise/services/support) .

  - Támogatás – e-mail-cím a [Microsoft biztonsági kódjának elemzését támogató](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request) csapatnál