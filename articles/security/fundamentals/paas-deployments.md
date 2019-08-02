---
title: Ajánlott eljárások a biztonságos Pásti üzembe helyezéséhez – Microsoft Azure
description: Ismerje meg az Azure-beli biztonságos felhőalapú alkalmazások tervezésével, létrehozásával és kezelésével kapcsolatos ajánlott eljárásokat, és ismerkedjen meg a Péter és más felhőalapú szolgáltatási modellek biztonsági előnyeivel.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: techlake
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: a562630ef19c134c227ef44b944c1dd921ff2e46
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726812"
---
# <a name="securing-paas-deployments"></a>PaaS üzemelő példányainak biztonságossá tétele

Ez a cikk segítséget nyújt a következőkhöz:

- Ismerje meg a Felhőbeli alkalmazások üzemeltetésének biztonsági előnyeit
- Értékelje ki a szolgáltatásként nyújtott platform (a Pásti) és más felhőalapú szolgáltatási modellek biztonsági előnyeit
- Módosítsa a biztonsági fókuszt a hálózatról az identitás-központú peremhálózati biztonsági megközelítésre
- Az általános Pásti biztonsági eljárásokkal kapcsolatos ajánlott eljárások megvalósítása

A [biztonságos alkalmazások fejlesztése az Azure](abstract-develop-secure-apps.md) -ban általános útmutató a biztonsági kérdésekhez és ellenőrzésekhez, amelyeket érdemes figyelembe venni a szoftverfejlesztés életciklusának minden fázisában a Felhőbeli alkalmazások fejlesztésekor.

## <a name="cloud-security-advantages"></a>Felhőbeli biztonsági előnyök
A felhőben biztonsági előnyökkel jár. A helyszíni környezetben a szervezetek valószínűleg kielégítetlen felelősséggel rendelkeznek, és korlátozott erőforrásokkal ruházzák fel a biztonságot, ami olyan környezetet hoz létre, amelyben a támadók kihasználhatják a biztonsági réseket minden rétegben.

![A Cloud Era biztonsági előnyei](./media/paas-deployments/advantages-of-cloud.png)

A szervezetek a szolgáltató felhőalapú biztonsági képességeivel és a Felhőbeli intelligenciával javíthatják a fenyegetések észlelését és a válaszadás időpontját.  A felelősségi körök felhőbe való áthelyezésével a szervezetek nagyobb biztonsági lefedettséggel rendelkezhetnek, ami lehetővé teszi, hogy a biztonsági erőforrásokat és a költségvetést más üzleti prioritásokhoz lehessen rendelni.

## <a name="division-of-responsibility"></a>A felelősség megosztása
Fontos megérteni az Ön és a Microsoft közötti felelősség megosztását. A helyszíni, a teljes verem tulajdonosa, de a felhőbe való áttérés során bizonyos felelősségek a Microsoft felé irányulnak. A következő felelősségi mátrix a verem azon területeit mutatja be, amelyek az Ön felelőssége, és a Microsoft felelős a IaaS.

![Felelősségi zónák](./media/paas-deployments/responsibility-zones.png)

Az összes felhőalapú központi telepítési típushoz Ön rendelkezik az adataival és az identitásával. Ön felelős az adatok és az identitások, a helyszíni erőforrások, valamint az Ön által felügyelt felhő-összetevők biztonságának védelméért (amely a szolgáltatás típusától függően változik).

Az Ön által mindig megőrzött felelősségek a következők:

- Data
- Végpontok
- Fiók
- Hozzáférés-kezelés

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>A Pásti Cloud Service-modell biztonsági előnyei
Ugyanezzel a felelősségi mátrixtal megvizsgáljuk az Azure Pásti üzembe helyezésének, illetve a helyszíni környezetnek a biztonsági előnyeit.

![A Péter biztonsági előnyei](./media/paas-deployments/advantages-of-paas.png)

A verem alján kezdődően a fizikai infrastruktúra, a Microsoft csökkenti a gyakori kockázatokat és a felelősségi köröket. Mivel a Microsoft-felhőt a Microsoft folyamatosan figyeli, nehéz támadni. Nincs értelme arra, hogy egy támadó a Microsoft-felhőt célként célozza meg. Ha a támadónak sok pénze és erőforrása van, a támadó valószínűleg egy másik célpontba kerül.  

A verem közepén nincs különbség a Pásti-telepítés és a helyszíni környezet között. Az alkalmazás rétegében és a fiók-és hozzáférés-kezelési rétegben hasonló kockázatokkal jár. A jelen cikk következő lépések szakaszában bemutatjuk a kockázatok kiküszöbölésére vagy minimalizálására vonatkozó ajánlott eljárásokat.

A stack, az adatkezelés és a Rights Management felső részén egy olyan kockázat áll fenn, amely a kulcskezelő szolgáltatással enyhíthető. (A kulcskezelő szolgáltatás az ajánlott eljárásokkal foglalkozik.) Míg a kulcskezelő szolgáltatás egy további felelősség, egy olyan Pásti-telepítésben található területtel rendelkezik, amelyet már nem kell kezelnie, így az erőforrások áthelyezhetők a kulcskezelő szolgáltatásba.

Az Azure platform a különböző hálózati technológiák használatával erős DDoS-védelmet is biztosít. Azonban a hálózat alapú DDoS-védelmi módszerek minden típusa egy-és adatközponton alapul. Annak érdekében, hogy elkerülje a nagy DDoS-támadások hatását, kihasználhatja az Azure alapszintű Felhőbeli képességeit, amellyel gyorsan és automatikusan méretezhető a DDoS-támadások elleni védelem. Részletesebben is bemutatjuk, hogyan teheti meg ezt az ajánlott eljárások cikkeiben.

## <a name="modernizing-the-defenders-mindset"></a>A Defender szemléletének modernizálása
A Pásti üzemelő példányok a biztonság általános megközelítését váltották át. A felelősségnek a Microsofttal való megosztásához saját maga is át kell irányítani.

A Pásti és a hagyományos helyszíni üzemelő példányok közötti jelentős különbség egy új nézet, amely meghatározza az elsődleges biztonsági területet. Történelmileg az elsődleges helyszíni biztonsági terület volt a hálózata, és a legtöbb helyszíni biztonsági terv a hálózatot használja elsődleges biztonsági kimutatásként. A Pásti-környezetek esetében jobban kiszolgálja, hogy az identitás az elsődleges biztonsági határ.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Identitásra vonatkozó szabályzat elfogadása elsődleges biztonsági peremhálózatként
A felhőalapú számítástechnika öt alapvető jellemzője a széles körű hálózati hozzáférés, ami a hálózat-központú gondolkodást kevésbé fontosnak teszi. A felhő-számítástechnika nagy része, hogy lehetővé tegye a felhasználók számára az erőforrások elérését a helytől függetlenül. A legtöbb felhasználó számára a helyükük valahol az interneten lesz.

Az alábbi ábra azt mutatja be, hogy a biztonsági szegély hogyan alakult ki egy hálózati peremhálózat és egy identitás peremhálózati hálózata között. A biztonság kevesebb információt nyújt a hálózat védelméről és az adatai védelméről, valamint az alkalmazások és a felhasználók biztonságának kezeléséről. A legfontosabb különbség az, hogy a biztonságot közelebbről szeretné leküldeni, ami a vállalat számára fontos.

![Identitás új biztonsági kerületként](./media/paas-deployments/identity-perimeter.png)

Kezdetben az Azure Pásti szolgáltatásai (például a webes szerepkörök és az Azure SQL) nem rendelkeznek a hagyományos hálózati peremhálózat-védelemmel. Megértettük, hogy az elem célja az Internet (webes szerepkör), és a hitelesítés az új területet (például a BLOBot vagy az Azure SQL-t) teszi elérhetővé.

A modern biztonsági gyakorlatok feltételezik, hogy a támadó megsértette a hálózati területet. Ezért a modern védelmi gyakorlatok átkerültek az identitásra. A szervezeteknek identitás-alapú biztonsági területet kell létrehozniuk erős hitelesítési és engedélyezési higiéniai (ajánlott eljárások).

A hálózati peremhálózat alapelvei és mintái évtizedek óta rendelkezésre állnak. Ezzel szemben az iparág viszonylag kevesebb tapasztalattal rendelkezik az identitás elsődleges biztonsági kerületként való használatával. Ebben az esetben felhalmozottunk egy elég élményt, amely a mezőben bevált általános ajánlásokat tartalmazza, és szinte minden Péter-szolgáltatásra érvényes.

Az alábbi ajánlott eljárások az identitás peremhálózati kezeléséhez szükségesek.

**Ajánlott eljárás**: A kulcsok és a hitelesítő adatok biztonságossá tétele a Pásti-telepítés biztonságossá tételéhez.   
**Részletek**: A kulcsok és a hitelesítő adatok elvesztése gyakori probléma. Olyan központosított megoldást használhat, amelyben a kulcsok és a titkos kódok a hardveres biztonsági modulokban (HSM) tárolhatók. [Azure Key Vault](../../key-vault/key-vault-whatis.md) védi a kulcsokat és a titkokat a hitelesítő kulcsok, a Storage-fiókok kulcsai, az adattitkosítási kulcsok, a. pfx-fájlok és a jelszavak a HSM által védett kulcsokkal történő titkosításával.

**Ajánlott eljárás**: Ne helyezzen el hitelesítő adatokat és más titkokat a forráskódban vagy a GitHubban.   
**Részletek**: A kulcsok és a hitelesítő adatok elvesztésekor az egyetlen dolog rosszabb, mint a jogosulatlan felek hozzáférése. A támadók kihasználhatják a robot-technológiák előnyeit, hogy megtalálják a kódrészletekben, például a GitHubban tárolt kulcsokat és titkokat. Ne helyezze el a kulcsokat és a titkos kódokat ezekben a nyilvános programkódokban.

**Ajánlott eljárás**: A virtuálisgép-kezelési felületek a hibrid Pásti és a IaaS szolgáltatásokban való ellátásához olyan felügyeleti felületet használhat, amely lehetővé teszi a virtuális gépek távoli felügyeletét közvetlenül.   
**Részletek**: A távoli felügyeleti protokollok, például az [SSH](https://en.wikipedia.org/wiki/Secure_Shell), az [RDP](https://support.microsoft.com/kb/186607)és a [PowerShell-távelérés](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) használhatók. Általánosságban azt javasoljuk, hogy ne engedélyezze a közvetlen távoli hozzáférést az internetről érkező virtuális gépekhez.

Ha lehetséges, alternatív megközelítéseket használhat, például virtuális magánhálózatok használatát egy Azure-beli virtuális hálózaton. Ha az alternatív megoldások nem érhetők el, győződjön meg arról, hogy összetett hozzáférési kódok és kétfaktoros hitelesítés (például az [Azure multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication)) használata szükséges.

**Ajánlott eljárás**: Használjon erős hitelesítési és engedélyezési platformokat.   
**Részletek**: Összevont identitások használata az Azure AD-ben egyéni felhasználói tárolók helyett. Összevont identitások használata esetén kihasználhatja a platform-alapú megközelítést, és delegálhatja a meghatalmazott identitások kezelését a partnerei számára. Az összevont identitások megközelítése különösen fontos az alkalmazottak leállításakor, és az információt több identitás-és engedélyezési rendszeren keresztül kell megjeleníteni.

Egyéni kód helyett platform által biztosított hitelesítési és engedélyezési mechanizmusokat használhat. Ennek az az oka, hogy az egyéni hitelesítési kód fejlesztése hibás lehet. A fejlesztők többsége nem rendelkezik biztonsági szakértőkkel, és nem valószínű, hogy tisztában van a finomságokkal, valamint a hitelesítés és az engedélyezés legújabb fejleményeivel. A kereskedelmi kódokat (például a Microsofttól) gyakran széles körű biztonsági felülvizsgálatnak tekintjük.

Használjon kétfaktoros hitelesítést. A kétfaktoros hitelesítés a hitelesítéshez és az engedélyezéshez használt jelenlegi szabvány, mivel elkerüli a Felhasználónév és jelszó típusú hitelesítéssel kapcsolatos biztonsági gyengeségeket. Az Azure felügyeleti (portál/távoli PowerShell) interfészekhez és az ügyfelek felé irányuló szolgáltatásokhoz való hozzáférést az [Azure multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication)használatára kell tervezni és konfigurálni.

Használjon szabványos hitelesítési protokollokat, például a OAuth2 és a Kerberost. Ezek a protokollok széles körben áttekintve lettek, és valószínűleg a platform könyvtárainak részeként valósulnak meg a hitelesítéshez és engedélyezéshez.

## <a name="use-threat-modeling-during-application-design"></a>Veszélyforrások modellezése az alkalmazás tervezésekor
A Microsoft [biztonsági fejlesztési életciklusa](https://www.microsoft.com/en-us/sdl) azt határozza meg, hogy a csapatok a tervezési fázisban részt vesznek a veszélyforrások modellezése nevű folyamaton. A folyamat elősegítése érdekében a Microsoft létrehozta az [SDL Threat Modeling Tool](/azure/security/azure-security-threat-modeling-tool). Az alkalmazás megtervezése és az [](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) összes bizalmi kapcsolat határainak számbavétele az összes megbízhatósági határ között a tervezési hibák korai befogása.

A következő táblázat felsorolja a Stride-fenyegetéseket, és példákat tartalmaz az Azure-szolgáltatásokat használó megoldásokra. Ezek a enyhítések nem fognak működni minden helyzetben.

| Threat | Biztonsági tulajdonság | Az Azure platform lehetséges enyhítése |
| --- | --- | --- |
| Hamisítási | Authentication | HTTPS-kapcsolatok megkövetelése. |
| Módosítás | Integritás | Ellenőrizze az SSL-tanúsítványokat. |
| Letagadhatóság | Letagadhatatlanság | Az Azure [monitorozásának és diagnosztizálásának](/azure/architecture/best-practices/monitoring)engedélyezése. |
| Információk közzététele | Titoktartási | Bizalmas adatok titkosítása a [szolgáltatás tanúsítványainak](/rest/api/appservice/certificates)használatával. |
| Szolgáltatásmegtagadás | Rendelkezésre állás | Teljesítmény-mérőszámok figyelése a lehetséges szolgáltatásmegtagadási feltételekhez. A kapcsolatok szűrőinek implementálása. |
| Jogok kiterjesztése | Authorization | [Privileged Identity Management](/azure/active-directory/privileged-identity-management/subscription-requirements)használata. |

## <a name="develop-on-azure-app-service"></a>Fejlesztés Azure App Service
A [Azure app Service](/azure/app-service/overview) egy olyan Pásti-ajánlat, amely lehetővé teszi webes és mobil alkalmazások létrehozását bármilyen platformra vagy eszközre, és bárhol, a felhőben vagy a helyszínen csatlakozhat az adatforrásokhoz. A App Service tartalmazza azokat a webes és mobil képességeket, amelyeket korábban az Azure websites és az Azure Mobile Services külön szállítottak el. Ezenkívül új lehetőségek is elérhetők az üzleti folyamatok automatizálásához és felhőalapú API-k üzemeltetéséhez. Egyetlen integrált szolgáltatásként a App Service számos lehetőséget kínál webes, mobil-és integrációs forgatókönyvek kialakítására.

A App Service használatának ajánlott eljárásai a következők:

**Ajánlott eljárás**: [Hitelesítés Azure Active Directoryon keresztül](/azure/app-service/overview-authentication-authorization).   
**Részletek**: A App Service OAuth 2,0 szolgáltatást biztosít az identitás-szolgáltató számára. A OAuth 2,0 a webalkalmazások, asztali alkalmazások és mobiltelefonok speciális engedélyezési folyamatainak biztosítása mellett az ügyfelek fejlesztői egyszerűségére koncentrál. Az Azure AD OAuth 2,0-et használ a mobil-és webalkalmazásokhoz való hozzáférés engedélyezéséhez.

**Ajánlott eljárás**: A hozzáférés korlátozása a szükséges ismeret és a legalacsonyabb szintű biztonsági alapelvek alapján.   
**Részletek**: A hozzáférés korlátozása elengedhetetlen azon szervezetek számára, akik biztonsági házirendeket kívánnak kikényszeríteni az adateléréshez. A RBAC segítségével engedélyeket rendelhet hozzá a felhasználókhoz, csoportokhoz és alkalmazásokhoz egy adott hatókörben. A felhasználók alkalmazásokhoz való hozzáférésének biztosításával kapcsolatos további tudnivalókért lásd: Ismerkedés [a hozzáférés](/azure/role-based-access-control/overview)-kezeléssel.

**Ajánlott eljárás**: A kulcsok megóvása.   
**Részletek**: Azure Key Vault segít megőrizni a Felhőbeli alkalmazások és szolgáltatások által használt titkosítási kulcsokat és titkokat. A Key Vault segítségével titkosíthatja a kulcsokat és a titkos kulcsokat (például a hitelesítési kulcsokat, a Storage-fiók kulcsait, az adattitkosítási kulcsokat). PFX-fájlok és jelszavak) a hardveres biztonsági modulok (HSM-EK) által védett kulcsok használatával. A még nagyobb biztonság érdekében lehetőség van arra is, hogy kulcsokat importáljon és generáljon a hardveres biztonsági modulokban. További információ: [Azure Key Vault](/azure/key-vault/key-vault-whatis) . A TLS-tanúsítványok automatikus megújítással történő kezeléséhez Key Vault is használhatja.

**Ajánlott eljárás**: A bejövő forrás IP-címeinek korlátozása.   
**Részletek**: A [app Service Environment](/azure/app-service/environment/intro) virtuális hálózati integrációs funkciójával a bejövő forrás IP-címeket hálózati biztonsági csoportokon keresztül korlátozhatja. A virtuális hálózatok lehetővé teszik az Azure-erőforrások olyan nem internetes, irányítható hálózatban való elhelyezését, amellyel a hozzáférését szabályozhatja. További információ: [az alkalmazás integrálása egy Azure](/azure/app-service/web-sites-integrate-with-vnet)-beli virtuális hálózattal.

**Ajánlott eljárás**: App Service környezetek biztonsági állapotának figyelése.   
**Részletek**: A App Service-környezetek figyeléséhez használja a Azure Security Center. Ha a Security Center felismeri a lehetséges biztonsági réseket, [javaslatokat](/azure/security-center/security-center-virtual-machine-recommendations) hoz létre, amelyek végigvezetik a szükséges vezérlők konfigurálásának folyamatán.

> [!NOTE]
> A figyelési App Service előzetes verzióban érhető el, és csak a Security Center [standard szintjére](/azure/security-center/security-center-pricing) használható.
>
>

## <a name="install-a-web-application-firewall"></a>Webalkalmazási tűzfal telepítése
A webalkalmazások egyre inkább ki vannak téve rosszindulatú támadásoknak, amelyek az ismert biztonsági réseket használják ki. Az ilyen jellegű támadások között például gyakoriak az SQL-injektálásos és a webhelyek közötti, parancsprogramot alkalmazó támadások. Az ilyen támadások megakadályozása az alkalmazás kódjában kihívást jelenthet, és szigorú felügyeletet, javítást és megfigyelést igényelhet az alkalmazás topológiájának számos rétegén. A központosított webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és segít az alkalmazás-rendszergazdáknak a fenyegetések vagy a behatolások elleni védekezésben. Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárókat egyszerűen át lehet alakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.

A webalkalmazási [tűzfal (WAF)](/azure/frontdoor/waf-overview) a Application Gateway szolgáltatása, amely központosított védelmet biztosít a webalkalmazások számára a gyakori biztonsági rések és sebezhetőségek ellen. A WAF az [Open Web Application Security Project (OWASP) alapszintű szabálykészlet (3,0)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) vagy a 2.2.9.

## <a name="monitor-the-performance-of-your-applications"></a>Az alkalmazások teljesítményének figyelése
A monitorozás az adatok összegyűjtését és elemzését, valamint az alkalmazás teljesítményének, állapotának és rendelkezésre állásának meghatározására szolgáló eljárás. A hatékony monitorozási stratégia pontos információkat nyújt az alkalmazás összetevőinek működéséről. Ez segít az üzemidő növelésében, ha a kritikus fontosságú problémákkal értesíti Önt, hogy feloldja őket, mielőtt problémák lépnek fel. Emellett segít a biztonsággal kapcsolatos rendellenességek észlelésében.

Az [Azure Application Insights](https://azure.microsoft.com/documentation/services/application-insights) segítségével figyelheti az alkalmazás rendelkezésre állását, teljesítményét és használatát, függetlenül attól, hogy a felhőben vagy a helyszínen üzemel. A Application Insights használatával gyorsan azonosíthatja és diagnosztizálhatja az alkalmazás hibáit anélkül, hogy a felhasználónak meg kellene várnia a jelentést. A gyűjtött információk alapján megalapozott döntéseket hozhat az alkalmazás karbantartásával és továbbfejlesztésével kapcsolatban.

Az Application Insights kiterjedt eszközkészlettel rendelkezik az általa gyűjtött adatok feldolgozásához. Az Application Insights egy általános adattárban tárolja az adatait. A Kusto lekérdezési nyelvével kihasználhatja a megosztott funkciók, például a riasztások, az irányítópultok és a mélyreható elemzés előnyeit.

## <a name="perform-security-penetration-testing"></a>Biztonsági behatolási teszt végrehajtása
A biztonsági védelem ellenőrzése olyan fontos, mint bármely más funkció tesztelése. A [](pen-testing.md) létrehozási és üzembe helyezési folyamat standard részét a behatolások tesztelésével teheti meg. Rendszeres biztonsági tesztek és sebezhetőségi vizsgálat ütemezett végrehajtása a telepített alkalmazásokon, valamint a nyitott portok, végpontok és támadások figyelése.

A fuzz Testing metódus a programbeli hibák (hibakódok) megkeresésére, ha helytelenül formázott bemeneti adatokat ad meg az adatok elemzésére és felhasználására szolgáló kezelőfelületekhez (belépési pontokhoz). A [Microsoft biztonsági kockázatok észlelése](https://www.microsoft.com/en-us/security-risk-detection/) egy felhőalapú eszköz, amellyel a szoftverben az Azure-ba történő üzembe helyezése előtt hibákat és egyéb biztonsági réseket kereshet. Az eszköz úgy van kialakítva, hogy a szoftverek központi telepítése előtt elkapjon biztonsági réseket, így nem kell hibát kijavítania, összeomlásokkal foglalkoznia, vagy támadásra reagálni a szoftver felszabadítása után.


## <a name="next-steps"></a>További lépések
Ebben a cikkben az Azure Pásti üzembe helyezésének és a felhőalapú alkalmazásokhoz ajánlott biztonsági eljárásoknak a biztonsági előnyeire összpontosítunk. Ezután Ismerkedjen meg a javasolt eljárásokkal, amelyekkel az adott Azure-szolgáltatásokkal biztonságossá teheti a Pásti web-és mobil megoldásait. A Azure App Service, a Azure SQL Database és a Azure SQL Data Warehouse, valamint az Azure Storage szolgáltatással fogunk kezdeni. Az egyéb Azure-szolgáltatásokra vonatkozó ajánlott eljárásokról szóló cikkek elérhetővé válnak, és az alábbi listán szereplő hivatkozásokat fogjuk megadni:

- [Azure App Service](paas-applications-using-app-services.md)
- [Azure SQL Database és Azure SQL Data Warehouse](paas-applications-using-sql.md)
- [Azure Storage](paas-applications-using-storage.md)
- Azure Cache for Redis
- Azure Service Bus
- Webalkalmazási tűzfalak

Tekintse meg a [biztonságos alkalmazások fejlesztése az Azure](abstract-develop-secure-apps.md) -ban biztonsági kérdéseket és ellenőrzéseket a szoftverfejlesztés életciklusának minden fázisában a Felhőbeli alkalmazások fejlesztésekor.

Az Azure-beli felhőalapú megoldások tervezésekor, üzembe helyezése és kezelése során ajánlott biztonsági eljárásokat az [Azure biztonsági](best-practices-and-patterns.md) eljárásaival és modelljeivel foglalkozó témakörben talál.

Az Azure-biztonsággal és a kapcsolódó Microsoft-szolgáltatásokkal kapcsolatos általános információk az alábbi forrásokból érhetők el:
* Az [Azure Security csapatának blogja](https://blogs.msdn.microsoft.com/azuresecurity/) – naprakész információk az Azure Security legújabb frissítéseiről
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – a Microsoft biztonsági rései, például az Azure-nal kapcsolatos problémák, jelentések vagy e-mailen keresztülsecure@microsoft.com


