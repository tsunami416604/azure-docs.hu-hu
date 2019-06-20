---
title: A Microsoft Azure a biztonságos alkalmazások fejlesztése
description: Ez a cikk ismerteti az ajánlott eljárások az web projektet a megvalósítás és ellenőrzési fázisaiban.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: bcd66d1a8077b4cc87c184f34b43cc5846a83f2f
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144424"
---
# <a name="develop-secure-applications-on-azure"></a>Az Azure-ban a biztonságos alkalmazások fejlesztése
Ebben a cikkben azt jelenthet biztonsági tevékenységeket és a vezérlőket, fontolja meg a felhőalapú alkalmazások fejlesztése során. Biztonsági kérdések és megfontolandó ajánlásoktól végrehajtására és ellenőrzés fázisai a Microsoft a fogalmak [biztonságos fejlesztési Életciklussal (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) terjed ki. A célja, hogy a tevékenységek és az Azure-szolgáltatások, amelyek segítségével egy biztonságosabb alkalmazások fejlesztése a meghatározásához.

Ebben a cikkben a következő SDL fázisok terjed ki:

- Megvalósítás
- Ellenőrzés

## <a name="implementation"></a>Megvalósítás
A megvalósítási fázis célja gyakorlati tanácsok a korai megelőzési létrehozására és észlelése és eltávolítása a biztonsági problémák a kódból.
Tegyük fel, hogy az alkalmazás fogja használni, hogy nem állt szándékában kell módon. Ez segít az alkalmazás véletlen vagy szándékos lekérésére való visszaélés elleni védelme.

### <a name="perform-code-reviews"></a>Hajtsa végre a kódvizsgálat

Ellenőrizheti a kódot, mielőtt magatartási [felülvizsgálatok code](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) növeli a kód általános minőségét és a hibák létrehozása kockázatának csökkentése érdekében. Használhat [Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) kódvizsgálati folyamattal kezeléséhez.

### <a name="perform-static-code-analysis"></a>Statikus kód elemzés végrehajtása

[Statikus kód](https://www.owasp.org/index.php/Static_Code_Analysis) (más néven *kód forrás*) általában egy kódvizsgálat részeként történik. Statikus kód gyakran hivatkozik a potenciális biztonsági rések keresése nem futó kódot eljárások segítségével elemzési eszközökkel hajthat végre statikus kódot futtató [vágófelülettel ellenőrzése](https://en.wikipedia.org/wiki/Taint_checking) és [adatfolyam-elemző](https://en.wikipedia.org/wiki/Data-flow_analysis).

Az Azure piactér ajánlatai [fejlesztői eszközök](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) , amely statikus kód elemzéseket végezhet és kódvizsgálat segítségét.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Ellenőrzése és vírusmentesítése az alkalmazás minden egyes bemenet

Kezelje az összes bemeneti nem megbízható, az alkalmazás elleni a leggyakrabban használt webes alkalmazások biztonsági réseinek. Nem megbízható adatok-injektálási támadások eszközzel helyezhető el. Az alkalmazás bemeneti paramétereket tartalmazza az URL-cím, a felhasználó, az adatokat az adatbázisból vagy API-k, a bemeneti adatok, és semmit, amely az átadott felhasználó potenciálisan módosítására. Egy alkalmazás kell [ellenőrzése](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs) , hogy adat szintaktikailag és szemantikailag érvényes-e előtt az alkalmazás használ az adatok semmilyen módon (beleértve a türközve legyen a felhasználó számára).

Győződjön meg arról, hogy csak a megfelelően formázott adatok kerül, a munkafolyamat az adatok folyamatban korai bemeneti ellenőrzése. Az adatbázis megőrzése és az alsóbb rétegbeli összetevők egy meghibásodás kiváltó helytelen formátumú adatokat nem szeretné.

Blacklisting és az engedélyezés két általános megközelítése bemeneti szintaxis-ellenőrzés végrehajtása a következők:

  - Ellenőrizze, hogy a megadott felhasználói bevitel nem tartalmaz "ismert rosszindulatú is lehet a" tartalom áruházat megkísérli.

  - Engedélyezési próbál ellenőrizze, hogy egy adott felhasználói bevitel megegyezik-e egy készletét "ismert jó" bemenetei között. Karakteres alapú engedélyezési egy engedélyezési formája, ahol egy alkalmazás ellenőrzi, hogy felhasználói bevitel tartalmazza-e egyetlen "ismert jó" karakter, vagy az, hogy a bemenet egy ismert formátuma megegyezik.
    Például ez is járhat, ellenőrzi, hogy a felhasználónév csak alfanumerikus karaktereket tartalmaz, illetve, hogy pontosan két számot tartalmaz.

Engedélyezési biztonságos szoftverek készítése előnyben részesített módszere.
Áruházat gyakran fordul elő hiba, mert nem lehet úgy gondolja, hogy az esetlegesen rossz bemeneti teljes listáját.

Ezt a munkát, a kiszolgálón, nem az ügyfél oldalán (vagy a kiszolgálón és az ügyféloldalon).

### <a name="verify-your-applications-outputs"></a>Ellenőrizze az alkalmazás kimenetek

A kimenetet, amelyek meg vizuálisan vagy egy dokumentumon belül mindig kell kódolni és escape-karakterrel. [Escape-karaktersorozat](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29), más néven *kimeneti kódolás*, használja, győződjön meg arról, hogy a nem megbízható adatok nem egy jármű-injektálásos támadásokkal szemben. Többrétegű védelem a teljes rendszer biztonság növelése escape-karaktersorozat adatérvényesítési kombinálva biztosít.

Escape-karaktersorozat teszi, hogy minden a megjelent *kimeneti.* Escape-karaktersorozat is lehetővé teszi, hogy a jelzi, hogy az adatok nem terjesztésre készült hajtható végre, és ez megakadályozza, hogy támadások működő értelmezője számára készült. Ez az egy másik gyakori támadási módszer nevű *többhelyes scripting* (XSS).

Ha egy harmadik féltől származó egy webes keretrendszert használ, a beállítások használatával a webhelyeken kódolási kimeneti ellenőrizheti a [OWASP XSS megelőzési adatlap](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Amikor kapcsolatba lép az adatbázis paraméteres lekérdezések használata

Soha ne hozzon létre egy beágyazott "menet közben" adatbázis-lekérdezés a kódban, és küldje el közvetlenül az adatbázist. Rosszindulatú kódot szúrt be az alkalmazás hibát okozhat ellopják, megtisztított és módosítani az adatbázist. Az alkalmazás is használható az operációs rendszeren, amelyen az adatbázis rosszindulatú operációsrendszer-parancsok futtatásához.

Ehelyett használja a paraméteres lekérdezések vagy tárolt eljárásokat. A paraméteres lekérdezések használata esetén az eljárás meghívása a kódból biztonságosan, és adja át azt egy karakterlánc nem kell, hogy ez lesz kezelve a lekérdezési utasítás részeként.

### <a name="remove-standard-server-headers"></a>Standard szintű kiszolgáló fejlécek eltávolítása

Fejlécek, mint X-alapú-szerint, a kiszolgáló, és X-AspNet-verzió a kiszolgáló és a mögöttes technológiákat kapcsolatos információk felfedése. Azt javasoljuk, hogy Ön ezen elkerülése érdekében az alkalmazás-ujjlenyomat-fejlécek elrejtése.
Lásd: [eltávolítása az Azure websites standard szintű kiszolgáló fejlécek](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Elkülönítheti a termelési adatok

A termelési adatok vagy a "valódi" adatokat, nem használható a fejlesztési, tesztelési vagy bármely más célra, mint az üzleti szolgál. Egy maszkolt ([anonimizált](https://en.wikipedia.org/wiki/Data_anonymization)) adatkészlet lehet használt valamennyi fejlesztési és tesztelési.

Ez azt jelenti, hogy kevesebb felhasználók is hozzáférhetnek a valós adatokat, ami csökkenti a támadási felületet. Azt is jelenti, kevesebb alkalmazottak tekintse meg a személyes adatokat, így a titkosítás nem a potenciális támadásokról.

### <a name="implement-a-strong-password-policy"></a>Egy erős jelszót a házirend megvalósítása

Találgatásos és a szótár-alapú találgatás elleni védelemre, meg kell valósítani annak érdekében, hogy a felhasználók (például 12 karakter minimális hosszát, és alfanumerikus karaktereket, illetve speciális karakterek megkövetelése) egy összetett jelszót hozzon létre egy erős jelszót szabályzatot.

Identitás-keretrendszer használatával hozzon létre és érvényesítsen jelszóházirendeket. Az Azure AD B2C segítségével a jelszókezelés használatának azáltal, hogy [beépített szabályzatok](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy), [önkiszolgáló jelszó-visszaállítási](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-sspr), stb.

Az alapértelmezett fiókok ellen indított támadások elleni védelemre, ellenőrizze az összes kulcsot és a jelszavak cserélhető, hogy, hogy már létrehozott vagy helyett az erőforrások telepítése után.

Ha az alkalmazás kell előállítja a jelszavak, győződjön meg arról, hogy a létrehozott jelszavakat véletlenszerű, és arról, hogy rendelkezik-e magas vysokou.

### <a name="validate-file-uploads"></a>Fájlok feltöltése ellenőrzése

Ha az alkalmazás lehetővé teszi, hogy [fájlba feltöltésének](https://www.owasp.org/index.php/Unrestricted_File_Upload), fontolja meg a óvintézkedéseket, amelyeket a kockázatos tevékenység is igénybe vehet. Sok első lépése, hogy valamilyen kártevő kódja kaphat a rendszer támadás alatt áll. Ehhez a támadó egy fájlfeltöltési segítségével. OWASP ellenőrzése egy fájlt annak ellenőrzéséhez, hogy a feltöltendő fájl biztonságos megoldásokat kínál.

Kártevőkkel szembeni védelem vírusok, kémprogramok és más, kártevő szoftverek azonosításához és nyújt segítséget. Telepíthet [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) vagy egy Microsoft-partner végpontvédelmi megoldás ([Trend Micro](https://www.trendmicro.com/azure/), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [A Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10), és [System Center Endpoint Protection](https://docs.microsoft.com/sccm/protect/deploy-use/endpoint-protection)).

[A Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) szolgáltatások, mint például a valós idejű védelem, ütemezett vizsgálatát, kártevő szoftverek eltávolítása, aláírás frissítések, kártevőfrissítések, reporting mintákat és kizárási eseménygyűjtés tartalmazza. Integrálható a Microsoft Antimalware-t és a partneri megoldások [az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) könnyű üzembe helyezés és a beépített észlelések (riasztások és incidensek).

### <a name="dont-cache-sensitive-content"></a>Bizalmas tartalom gyorsítótárazása nem

Nem gyorsítótárazzák a bizalmas tartalmat a böngészőben. Böngészők gyorsítótárazás és a korábbi adatok tárolhatók. A gyorsítótárazott fájlok például az ideiglenes internetfájlok mappa esetén az Internet Explorer egy mappában vannak tárolva. Ha ezeket az oldalakat hivatkozunk újra, a böngésző jeleníti meg az oldalakat a gyorsítótárból. Ha a felhasználó bizalmas adatokat (címe, hitelkártya adatait, társadalombiztosítási szám, username) jelenik meg, az adatokat előfordulhat, hogy a böngésző gyorsítótárát kell tárolni, és lekérhető vizsgálata folyamatban van a böngésző gyorsítótárát, vagy egyszerűen csak billentyű a böngészőben  **Vissza** gombra.

## <a name="verification"></a>Ellenőrzés
Az ellenőrzési fázisban egy átfogó annak érdekében, hogy győződjön meg arról, hogy a kód megfelel-e a biztonsági és adatvédelmi tenets az előző fázisok felvett foglalja magában.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Keresse meg és hárítsa el a biztonsági rések a az alkalmazásfüggőségek

Olvassa be az alkalmazás és a függő kódtárak ismert sebezhető összetevők azonosításához. Ez a vizsgálat elérhető termékek tartalmaznak [OWASP függőségi ellenőrizze](https://www.owasp.org/index.php/OWASP_Dependency_Check),[Snyk](https://snyk.io/), és [fekete kacsa](https://www.blackducksoftware.com/).

Biztonsági rések keresése működteti [teljesíteni a Tinfoil Security](https://www.tinfoilsecurity.com/) érhető el az Azure App Service Web Apps. [A tinfoil Security scanning App Service révén](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) lehetővé teszi a fejlesztők és rendszergazdák gyors, integrált és gazdaságos felderítéséhez és a biztonsági rések címzés előtt egy rosszindulatú aktor igénybe veheti őket.

> [!NOTE]
> Emellett [teljesíteni a Tinfoil Security integrálása az Azure ad-vel](https://docs.microsoft.com/azure/active-directory/saas-apps/tinfoil-security-tutorial). Tinfoil Security integrálása az Azure ad-ben nyújt a következő előnyökkel jár:
>  - Az Azure AD-ben szabályozhatja, ki férhet hozzá a Tinfoil Security.
>  - A felhasználók számára is automatikusan megtörténik a Tinfoil Security (egyszeri bejelentkezés), az Azure AD-fiókjaik használatával.
>  - A fiókok, egy egységes, központi helyen, az Azure Portalon kezelheti.

### <a name="test-your-application-in-an-operating-state"></a>Az alkalmazás tesztelése a egy működési állapotot

Dinamikus alkalmazás biztonsági tesztelés (DAST) az a folyamat egy alkalmazás tesztelése a egy működési állapotot biztonsági rések keresése. DAST eszközök elemezheti a programok, például a memóriasérülést, nem biztonságos kiszolgáló konfigurációját, webhelyek közötti scripting, felhasználói jogosultság problémák, SQL-injektálás és más kritikus fontosságú biztonsági aggályokat biztonsági rések keresése vannak végrehajtása közben.

DAST statikus alkalmazásbiztonsági tesztelése (SAST) különbözik. SAST eszközök forráskódja elemzéséhez, és lefordított kód verzióit, amikor a kód nem fut, annak érdekében, hogy keresse meg a biztonsági hibái.

DAST, lehetőleg a szakemberek a rendszervédelemmel közreműködésével hajtsa végre (egy [behatolási tesztelő](https://docs.microsoft.com/azure/security/azure-security-pen-testing) vagy a biztonsági kockázatokat). A szakemberek a rendszervédelemmel nem érhető el, ha elvégezhet DAST saját magának egy webes proxy képolvasó és néhány képzési. Beépülő modul egy DAST szkenner már a legelején, győződjön meg arról, hogy nem indít-e nyilvánvaló biztonsági problémák elhelyezni a kódban. Tekintse meg a [OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) webhelyet biztosít a webes alkalmazás biztonságirés-ellenőrző eszközök listáját.

### <a name="perform-fuzz-testing"></a>Fuzz teszt végrehajtása

A [tesztelés fuzz](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/), egy alkalmazás helytelenül formázott vagy véletlenszerű adatok szándékosan bevezetésével a program hiba idéz elő. Hogy a program hiba segítségével felfedése potenciális biztonsági problémákat, mielőtt az alkalmazás akkor szabadul fel.

[Biztonsági kockázat észlelési](https://docs.microsoft.com/security-risk-detection/) a Microsoft egyedi fuzz tesztelési szolgáltatás a szoftver biztonsági szempontból kritikus fontosságú hibák kereséséhez.

### <a name="conduct-attack-surface-review"></a>Támadási felület áttekintés

Után kódkiegészítés biztosítja, hogy bármilyen tervezési vagy megvalósítási módosítja egy alkalmazás vagy a rendszer fontolóra vette, tekintse át a támadási felületet. Ez segít, győződjön meg arról, hogy minden olyan új támadási felületek kialakulását modelljei, beleértve a módosítások eredményeképpen létrehozott tekintse át és problémák elhárításáról.

Az alkalmazás vizsgálatával hozhat létre egy képet a támadási felületet. A Microsoft kínál a támadási felület elemzése nevű eszközt [támadási Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487). Számos kereskedelmi dinamikus tesztelés és-eszközök vagy a szolgáltatások, beleértve a biztonsági rés választja [OWASP ott a támadás Proxy projekt](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/), [Skipfish](http://code.google.com/p/skipfish/), és [w3af](http://w3af.sourceforge.net/). E beolvasó eszközök feltérképezi az alkalmazását, és képezze le az alkalmazás a részei, amely a weben keresztül érhető el. Az Azure Marketplace is kereshet hasonló [fejlesztői eszközök](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1).

### <a name="perform-security-penetration-testing"></a>Hajtsa végre a biztonsági behatolásvizsgálat

Annak biztosítása, hogy az alkalmazás biztonságos olyan fontos, mint bármely más funkció tesztelése. Győződjön meg arról, [behatolásvizsgálat](https://docs.microsoft.com/azure/security/azure-security-pen-testing) a buildelési és üzembe helyezési folyamat alapvető része. Ütemezheti rendszeres biztonsági vizsgálatok és a biztonsági ellenőrzés a telepített alkalmazások használatát, és figyelje a megnyitott portok, végpontok és támadásokat.

### <a name="run-security-verification-tests"></a>Biztonsági ellenőrzések futtatása

[Secure DevOps Kitet az Azure](https://azsk.azurewebsites.net/index.html) (AzSK) SVTs tartalmazza az Azure platform több szolgáltatáshoz. Futtatja ezeket SVTs rendszeres időközönként, győződjön meg arról, hogy az Azure-előfizetés és a különböző erőforrások, amelyek tartalmazzák az alkalmazás egy biztonságos állapotban van. Ezek a tesztek a folyamatos integráció/folyamatos készregyártás (CI/CD) bővítmények szolgáltatását, AzSK, amely elérhetővé teszi SVTs, egy Visual Studio-bővítmény használatával is automatizálható.

## <a name="next-steps"></a>További lépések
Az alábbi cikkeket azt javasoljuk, hogy a biztonsági vezérlők és tevékenységek, amelyek segítségével tervezését és helyezzen üzembe biztonságos alkalmazásokat.

- [Biztonságos alkalmazások tervezése](secure-design.md)
- [Biztonságos alkalmazások üzembe helyezése](secure-deploy.md)
