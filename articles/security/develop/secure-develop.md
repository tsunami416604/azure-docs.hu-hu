---
title: Biztonságos alkalmazások fejlesztése a Microsoft Azure-ban
description: Ez a cikk ismerteti a webes alkalmazás projekt megvalósítási és ellenőrzési fázisai során figyelembe vesszen ajánlott eljárásokat.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 9d98660230e0ab9f4edcd9a7af8a3797106dd17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255658"
---
# <a name="develop-secure-applications-on-azure"></a>Biztonságos alkalmazások fejlesztése az Azure szolgáltatásban
Ebben a cikkben bemutatjuk a biztonsági tevékenységek és vezérlők, hogy fontolja meg, amikor alkalmazásokat fejleszt a felhőben. A Microsoft [security development lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) megvalósítási és ellenőrzési fázisai során figyelembe veendő biztonsági kérdések és fogalmak is lefedhetők. A cél az, hogy segítsen meghatározni a tevékenységek et és az Azure-szolgáltatásokat, amelyek segítségével egy biztonságosabb alkalmazás.

Ez a cikk a következő SDL fázisokat ismerteti:

- Megvalósítás
- Ellenőrzés

## <a name="implementation"></a>Megvalósítás
A végrehajtási szakasz középpontjában a korai megelőzésre vonatkozó bevált gyakorlatok kialakítása, valamint a biztonsági problémák felismerése és eltávolítása áll a kódexből.
Tegyük fel, hogy az alkalmazás lesz használva oly módon, hogy nem kívánta használni. Ez segít védekezni az alkalmazás véletlen vagy szándékos helytelen használata ellen.

### <a name="perform-code-reviews"></a>Kódellenőrzések végrehajtása

A kód ellenőrzése előtt végezzen [kódellenőrzéseket](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) az általános kódminőség növelése és a hibák létrehozásának kockázatának csökkentése érdekében. A [Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) segítségével kezelheti a kód-ellenőrzési folyamatot.

### <a name="perform-static-code-analysis"></a>Statikus kódelemzés végrehajtása

[A statikus kódelemzést](https://www.owasp.org/index.php/Static_Code_Analysis) (más néven *forráskód-elemzést)* általában egy kódellenőrzés részeként végzik. A statikus kódelemzés általában statikus kódelemző eszközök futtatására utal, amelyek a nem futó kód potenciális biztonsági réseit keresik olyan technikák alkalmazásával, mint [a behaszni-ellenőrzés](https://en.wikipedia.org/wiki/Taint_checking) és [az adatáramlás-elemzés.](https://en.wikipedia.org/wiki/Data-flow_analysis)

Az Azure Marketplace [olyan fejlesztői eszközöket](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) kínál, amelyek statikus kódelemzést végeznek, és segítséget nyújtanak a kódellenőrzésekhez.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Az alkalmazás minden bemenetének ellenőrzése és fertőtlenítése

Kezelje az összes bemenetet nem megbízhatóként, hogy megvédje az alkalmazást a leggyakoribb webalkalmazás-biztonsági résektől. A nem megbízható adatok az injekciós támadások hordozói. Az alkalmazás bemenete tartalmazza az URL-cím paramétereit, a felhasználó tól érkező adatokat, az adatbázisból vagy egy API-ból származó adatokat, és mindent, ami átkerül, és amit a felhasználó potenciálisan manipulálhat. Az alkalmazásnak [ellenőriznie](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs) kell, hogy az adatok szintaktikailag és szemantikailag érvényesek, mielőtt az alkalmazás bármilyen módon felhasználja az adatokat (beleértve a felhasználó számára visszajelenítendő adatokat is).

Ellenőrizze a bemenetet az adatfolyam korai szakaszában, hogy csak a megfelelően kialakított adatok kerüljön be a munkafolyamatba. Nem szeretné, hogy a hibásan formázott adatok megmaradjanak az adatbázisban, vagy hibás működést váltsanak ki egy alsóbb rétegbeli összetevőben.

A feketelista és az engedélyezési lista a bemeneti szintaxis-ellenőrzés végrehajtásának két általános megközelítése:

  - A feketelista megpróbálja ellenőrizni, hogy egy adott felhasználói adat nem tartalmaz-e "ismerten rosszindulatú" tartalmat.

  - Az engedélyezési jegyzék megpróbálja ellenőrizni, hogy egy adott felhasználói bevitel megfelel-e az "ismert jó" bemenetek készletének. A karakteralapú engedélyezési lista az engedélyezési lista olyan formája, ahol az alkalmazás ellenőrzi, hogy a felhasználói bevitel csak "ismert jó" karaktereket tartalmaz, vagy ha a bevitel megegyezik egy ismert formátummal.
    Ez például annak ellenőrzését jelentheti, hogy egy felhasználónév csak alfanumerikus karaktereket tartalmaz-e, vagy hogy pontosan két számot tartalmaz-e.

Whitelisting az előnyben részesített megközelítés az épület biztonságos szoftver.
A feketelista hajlamos a hibára, mert lehetetlen a potenciálisan rossz bemenetek teljes listájának.

Végezze el ezt a munkát a kiszolgálón, nem az ügyféloldalon (vagy a kiszolgálón és az ügyféloldalon).

### <a name="verify-your-applications-outputs"></a>Az alkalmazás kimenetének ellenőrzése

A vizuálisan vagy a dokumentumon belül megjelenő kimeneteket mindig kódolni kell, és meg kell szabadulni. [A menekülés](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29), más néven *kimeneti kódolás*segítségével biztosítható, hogy a nem megbízható adatok ne legyenek befecskendezéses támadások hordozói. Az adatérvényesítéssel kombinálva a menekülés réteges védelmet nyújt a rendszer egészének biztonságának növelése érdekében.

Menekülés gondoskodik arról, hogy minden jelenik meg *a kimenet.* A szökés azt is lehetővé teszi a tolmács számára, hogy az adatokat nem kívánják végrehajtani, és ez megakadályozza a támadások működését. Ez egy másik gyakori támadási technika, az úgynevezett *helyek közötti parancsfájlok* (XSS).

Ha egy harmadik féltől származó webes keretrendszert használ, az [OWASP XSS megelőzési csalási lap](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)segítségével ellenőrizheti a kimeneti kódolás lehetőségeit a webhelyeken.

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Paraméterezett lekérdezések használata az adatbázissal való kapcsolatfelvételkor

Soha ne hozzon létre "menet közben" szövegközi adatbázis-lekérdezést a kódban, és küldje el közvetlenül az adatbázisba. Az alkalmazásba beszúrt rosszindulatú kód az adatbázis ellopását, letörölését vagy módosítását okozhatja. Az alkalmazás rosszindulatú operációs rendszer-parancsok futtatására is használható az adatbázist üzemeltető operációs rendszeren.

Ehelyett használjon paraméteres lekérdezéseket vagy tárolt eljárásokat. Paraméteres lekérdezések használatakor biztonságosan meghívhatja az eljárást a kódból, és átadhat egy karakterláncot anélkül, hogy aggódnia kellene amiatt, hogy a lekérdezési utasítás részeként lesz kezelve.

### <a name="remove-standard-server-headers"></a>Szabványos kiszolgálófejlécek eltávolítása

Az olyan fejlécek, mint a Server, az X-Powered-By és az X-AspNet-Version, a kiszolgálóval és az alapul szolgáló technológiákkal kapcsolatos információkat tárnak fel. Azt javasoljuk, hogy tiltsa le ezeket a fejléceket, hogy elkerülje az alkalmazás ujjlenyomatvételét.
Lásd: [Szabványos kiszolgálófejlécek eltávolítása az Azure-webhelyeken.](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

### <a name="segregate-your-production-data"></a>A termelési adatok elkülönítése

A termelési adatok, vagy "valós" adatok, nem használható fel fejlesztési, tesztelési, vagy bármely más célra, mint amit a vállalkozás célja. Maszkolt ([anonimizált)](https://en.wikipedia.org/wiki/Data_anonymization)adatkészletet kell használni minden fejlesztéshez és teszteléshez.

Ez azt jelenti, hogy kevesebben férnek hozzá a valós adatokhoz, ami csökkenti a támadási felületet. Ez azt is jelenti, hogy kevesebb alkalmazott látja a személyes adatokat, ami kiküszöböli a titoktartás esetleges megsértését.

### <a name="implement-a-strong-password-policy"></a>Erős jelszóházirend megvalósítása

A találgatásos és szótáralapú találgatások elleni védekezéshez erős jelszóházirendet kell alkalmazni, amely biztosítja, hogy a felhasználók összetett jelszót hozzanak létre (például 12 karakter minimális hosszúságúak, és alfanumerikus és speciális karaktereket igényelnek).

Az identitáskeretrendszer használatával jelszóházirendeket hozhat létre és kényszeríthet ki. Az Azure AD B2C [beépített szabályzatok,](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow) [önkiszolgáló jelszó-visszaállítás](../../active-directory-b2c/user-flow-self-service-password-reset.md)és egyebek biztosításával segíti a jelszókezelést.

Az alapértelmezett fiókok elleni támadások elleni védelem érdekében ellenőrizze, hogy az összes kulcs és jelszó cserélhető-e, és hogy az erőforrások telepítése után létrejönnek vagy kicserélik őket.

Ha az alkalmazásnak automatikusan létre kell hoznia a jelszavakat, győződjön meg arról, hogy a létrehozott jelszavak véletlenszerűek, és hogy magas entrópiával rendelkeznek.

### <a name="validate-file-uploads"></a>Fájlfeltöltések ellenőrzése

Ha az alkalmazás lehetővé teszi [a fájlfeltöltést,](https://www.owasp.org/index.php/Unrestricted_File_Upload)fontolja meg a kockázatos tevékenységgel kapcsolatos óvintézkedéseket. Az első lépés a sok támadás, hogy néhány rosszindulatú kódot egy olyan rendszer, amely támadás alatt áll. A fájlfeltöltés használata segít a támadónak ennek elvégzésében. Az OWASP megoldásokat kínál a fájlok érvényesítésére annak érdekében, hogy a feltöltött fájl biztonságos legyen.

A kártevőirtó védelem segít azonosítani és eltávolítani a vírusokat, kémprogramokat és más rosszindulatú szoftvereket. Telepítheti a [Microsoft Antimalware](../fundamentals/antimalware.md) vagy a Microsoft partnere végpontvédelmi megoldását ([Trend Micro,](https://www.trendmicro.com/azure/) [Broadcom,](https://www.broadcom.com/products) [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)és [Endpoint Protection](https://docs.microsoft.com/configmgr/protect/deploy-use/endpoint-protection)).

[A Microsoft Antimalware](../fundamentals/antimalware.md) olyan funkciókat tartalmaz, mint a valós idejű védelem, az ütemezett vizsgálat, a kártevők szervizelése, az aláírásfrissítések, a motorfrissítések, a mintajelentések és a kizárási események gyűjtése. Integrálhatja a Microsoft antimalware és partner megoldások [az Azure Security Center](../../security-center/security-center-partner-integration.md) a könnyű üzembe helyezés és a beépített észlelések (riasztások és incidensek).

### <a name="dont-cache-sensitive-content"></a>Ne tárolj bizalmas tartalmat

Ne gyorsítótárazásérzékeny tartalmat a böngészőben. A böngészők tárolhatják a gyorsítótárazásés előzmények adatait. A gyorsítótárazott fájlok az Internet Explorer esetében egy mappában tárolódnak, például az Ideiglenes internetfájlok mappában. Amikor ismét hivatkoznak ezekre az oldalakra, a böngésző megjeleníti az oldalakat a gyorsítótárából. Ha bizalmas adatok (cím, hitelkártya adatok, társadalombiztosítási szám, felhasználónév) jelenik meg a felhasználó számára, az adatok a böngésző gyorsítótárában tárolhatók, és a böngésző gyorsítótárának vizsgálatával vagy a böngésző **Vissza** gombjának egyszerű megnyomásával visszakereshetők.

## <a name="verification"></a>Ellenőrzés
Az ellenőrzési szakasz átfogó erőfeszítéseket tesz annak biztosítására, hogy a kódex megfeleljen az előző szakaszokban megállapított biztonsági és adatvédelmi tanoknak.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Az alkalmazásfüggőségek biztonsági rései nek megkeresése és javítása

Az alkalmazás és a függő kódtárak átkezésével azonosíthatja az ismert sebezhető összetevőket. A vizsgálat elvégzéséhez rendelkezésre álló termékek közé tartozik az [OWASP-függőség ellenőrzése](https://www.owasp.org/index.php/OWASP_Dependency_Check),[a Snyk](https://snyk.io/)és a [Fekete Kacsa](https://www.blackducksoftware.com/).

A [Tinfoil Security](https://www.tinfoilsecurity.com/) által működtetett biztonsági résvizsgálat az Azure App Service webalkalmazásokhoz érhető el. [Az Ainfoil Security az App Service-en keresztül történő szkennelés](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) gyors, integrált és gazdaságos eszközöket kínál a fejlesztőknek és a rendszergazdáknak a biztonsági rések felderítésére és kezelésére, mielőtt egy rosszindulatú szereplő kihasználhatja azokat.

> [!NOTE]
> Az [Alufólia-biztonságot integrálhatja az Azure AD-vel](../../active-directory/saas-apps/tinfoil-security-tutorial.md)is. Az Alufólia-biztonság integrálása az Azure AD-vel a következő előnyöket nyújtja:
>  - Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Tinfoil Security szolgáltatáshoz.
>  - A felhasználók automatikusan bejelentkezhetnek a Tinfoil Security (egyszeri bejelentkezés) az Azure AD-fiókok használatával.
>  - Fiókjait egyetlen központi helyen, az Azure Portalon kezelheti.

### <a name="test-your-application-in-an-operating-state"></a>Tesztelje az alkalmazást üzemi állapotban

A dinamikus alkalmazásbiztonsági tesztelés (DAST) egy olyan folyamat, amelynek során egy alkalmazást üzemi állapotban tesztelnek a biztonsági rések megtalálásához. A DAST-eszközök a végrehajtás során elemzik a programokat, hogy olyan biztonsági réseket találjanak, mint a memóriasérülés, a nem biztonságos kiszolgálókonfiguráció, a webhelyek közötti parancsfájlok használata, a felhasználói jogosultságokkal kapcsolatos problémák, az SQL-injektálás és más kritikus biztonsági problémák.

A DAST különbözik a statikus alkalmazásbiztonsági teszteléstől (SAST). A SAST-eszközök elemzik a kód forráskódját vagy lefordított verzióit, ha a kód nem működik a biztonsági hibák keresése érdekében.

Végezze el a DAST-t, lehetőleg biztonsági szakember [(behatolásvizsgáló](../fundamentals/pen-testing.md) vagy sebezhetőség-értékelő) segítségével. Ha egy biztonsági szakember nem érhető el, akkor a DAST-t saját maga is elvégezheti egy webproxy-szkennerrel és néhány képzéssel. A DAST-lapolvasót korán csatlakoztathatja, hogy ne vezessen be nyilvánvaló biztonsági problémákat a kódjába. A webalkalmazások biztonsági résének leolvasóinak listáját az [OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) webhelyén talál.

### <a name="perform-fuzz-testing"></a>Fuzz tesztelés végrehajtása

A [fuzz tesztelés,](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)akkor indukálja a program meghibásodása szándékosan bevezetése hibás vagy véletlenszerű adatokat egy alkalmazás. A programhiba előidézése segít feltárni a lehetséges biztonsági problémákat az alkalmazás kiadása előtt.

[A Security Risk Detection](https://docs.microsoft.com/security-risk-detection/) a Microsoft egyedi fuzz tesztelési szolgáltatása a szoftver ben a biztonság szempontjából kritikus hibák megtalálásához.

### <a name="conduct-attack-surface-review"></a>Támadási felület felülvizsgálatának lefolytatása

A támadási felület áttekintése a kód befejezése után segít biztosítani, hogy az alkalmazás vagy rendszer tervezési vagy megvalósítási módosításait figyelembe vették. Ez segít biztosítani, hogy a módosítások eredményeként létrehozott új támadási vektorok, beleértve a fenyegetésmodelleket is, felül lettek győződve és mérsékelve.

Az alkalmazás beolvasásával képet készíthet a támadási felületről. A Microsoft egy támadási felületelemző eszközt, a [Attack Surface Analyzer-t](https://www.microsoft.com/download/details.aspx?id=24487)kínálja. Számos dinamikus kereskedelmi tesztelési és biztonsági rés-leválasztási eszköz vagy szolgáltatás közül választhat, beleértve [az OWASP Zed Attack Proxy Project,](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project) [Arachni](http://arachni-scanner.com/), [Skipfish](https://code.google.com/p/skipfish/)és [w3af](http://w3af.sourceforge.net/)eszközöket. Ezek a beolvasási eszközök feltérképezik az alkalmazást, és leképezik az alkalmazás azon részeit, amelyek az interneten keresztül érhetők el. Az Azure Piactéren is kereshet hasonló [fejlesztői eszközöket.](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)

### <a name="perform-security-penetration-testing"></a>Biztonsági behatolási vizsgálat végrehajtása

Annak biztosítása, hogy az alkalmazás biztonságos legyen, ugyanolyan fontos, mint bármely más funkció tesztelése. A [penetrációs tesztelést](../fundamentals/pen-testing.md) a létrehozási és üzembe helyezési folyamat szabványos részévé teheti. Ütemezze a rendszeres biztonsági teszteket és a biztonsági rések ellenőrzését az üzembe helyezett alkalmazásokon, és figyelje a nyitott portokat, végpontokat és támadásokat.

### <a name="run-security-verification-tests"></a>Biztonsági ellenőrző tesztek futtatása

[A Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) (AzSK) svt-ket tartalmaz az Azure platform több szolgáltatására vonatkozóan. Ezeket az SVT-ket rendszeresen futtatja annak érdekében, hogy az Azure-előfizetés és az alkalmazás különböző erőforrásai biztonságos állapotban legyenek. Ezeket a teszteket az AzSK folyamatos integrációs/folyamatos üzembe helyezési (CI/CD) bővítményfunkciójával is automatizálhatja, amely az SVT-ket Visual Studio-bővítményként teszi elérhetővé.

## <a name="next-steps"></a>További lépések
Az alábbi cikkekben olyan biztonsági vezérlőket és tevékenységeket javasoljuk, amelyek segítségével biztonságos alkalmazásokat tervezhet és helyezhet üzembe.

- [Biztonságos alkalmazások tervezése](secure-design.md)
- [Biztonságos alkalmazások telepítése](secure-deploy.md)
