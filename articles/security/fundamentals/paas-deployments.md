---
title: Gyakorlati tanácsok a biztonságos PaaS-telepítésekhez – Microsoft Azure
description: Ismerje meg a biztonságos felhőalkalmazások azure-beli tervezésére, készítésére és kezelésére vonatkozó gyakorlati tanácsokat, és ismerje meg a PaaS és más felhőszolgáltatási modellek biztonsági előnyeit.
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
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 0643ae792c890b65f239d9a0c16a05639dd4f8b9
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811471"
---
# <a name="securing-paas-deployments"></a>PaaS-környezetek védelme

Ez a cikk a következőket ismerteti:

- Ismerje meg az alkalmazások felhőben való üzemeltetésének biztonsági előnyeit
- A platform szolgáltatásként (PaaS) és más felhőszolgáltatási modellek biztonsági előnyeinek kiértékelése
- A biztonsági fókusz hálózatközpontúról identitásközpontú perembiztonsági megközelítésre való módosítása
- Általános PaaS-biztonsági gyakorlati tanácsok megvalósítása

[A biztonságos alkalmazások fejlesztése az Azure-ban](abstract-develop-secure-apps.md) egy általános útmutató a biztonsági kérdésekhez és vezérlőkhöz, amelyeket a szoftverfejlesztési életciklus minden fázisában figyelembe kell vennie a felhőhöz való alkalmazások fejlesztése során.

## <a name="cloud-security-advantages"></a>Felhőalapú biztonsági előnyök
Fontos megérteni, hogy ön és a Microsoft [milyen felelősségmegosztást](shared-responsibility.md) vállal. A helyszíni, a teljes verem a teljes verem, de afelhőbe lépés közben bizonyos felelősségek átad nak a Microsoft.

Vannak [biztonsági előnyei, hogy a felhőben](shared-responsibility.md#cloud-security-advantages). A helyszíni környezetben a szervezetek valószínűleg kielégítetlen felelősségi körökkel és korlátozott erőforrásokkal rendelkeznek a biztonságba való befektetéshez, ami olyan környezetet hoz létre, ahol a támadók minden rétegbiztonsági biztonsági réseit ki tudják használni.

A szervezetek javíthatják a fenyegetések észlelési és válaszidejét a szolgáltató felhőalapú biztonsági képességeinek és felhőalapú intelligenciájának használatával.  Azáltal, hogy a feladatokat a felhőszolgáltatóra helyezi át, a szervezetek nagyobb biztonsági lefedettséget kaphatnak, ami lehetővé teszi számukra, hogy a biztonsági erőforrásokat és a költségvetést más üzleti prioritásokhoz csoportosítsák át.

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>A PaaS felhőszolgáltatás-modell biztonsági előnyei
Tekintsük át az Azure PaaS-telepítés és a helyszíni biztonsági előnyeit.

![A PaaS biztonsági előnyei](./media/paas-deployments/advantages-of-paas.png)

A fizikai infrastruktúra, a fizikai infrastruktúra, a fizikai infrastruktúra alján, a Microsoft csökkenti a gyakori kockázatok és felelősségek. Mivel a Microsoft-felhőt folyamatosan figyeli a Microsoft, nehéz támadni. Nincs értelme, hogy a támadó a Microsoft felhőt célként kövesse. Ha a támadó nem rendelkezik sok pénzzel és erőforrással, a támadó valószínűleg egy másik célpontra lép.  

A verem közepén nincs különbség a PaaS-üzembe helyezés és a helyszíni között. Az alkalmazásrétegen, valamint a fiók- és hozzáférés-kezelési rétegben hasonló kockázatokkal rendelkezik. A cikk következő lépéseiben bemutatjuk, hogy mi a legjobb gyakorlat a kockázatok kiküszöbölésére vagy minimalizálására.

A verem, az adatkezelés és a jogkezelés tetején egy olyan kockázatot vállal, amelyet a kulcskezelés mérsékelhet. (A kulcskezelést a legjobb gyakorlatok fedezik.) Bár a kulcskezelés további felelősség, a PaaS-telepítés olyan területeivannak, amelyeket már nem kell kezelnie, így az erőforrásokat kulcskezelésre áthelyezheti.

Az Azure platform is erős DDoS-védelmet biztosít a különböző hálózati technológiák használatával. Azonban minden típusú hálózati ddos védelmi módszerek a korlátok at egy-link és egy adatközpont alapon. A nagy DDoS-támadások hatásának elkerülése érdekében kihasználhatja az Azure alapvető felhőbeli képességét, amely lehetővé teszi, hogy gyorsan és automatikusan skálázható legyen a DDoS-támadások elleni védelem érdekében. Részletesebben kifejtjük, hogyan teheti ezt meg az ajánlott gyakorlati cikkekben.

## <a name="modernizing-the-defenders-mindset"></a>A védő gondolkodásmódjának modernizálása
A PaaS-telepítések jönnek a váltás az általános megközelítés a biztonság. A felelősséget a Microsofttal való megosztására való áttérésről áthelyezheti.

Egy másik jelentős különbség a PaaS és a hagyományos helyszíni telepítések között, egy új nézet, hogy mi határozza meg az elsődleges biztonsági területet. Korábban az elsődleges helyszíni biztonsági peremterület a hálózat volt, és a legtöbb helyszíni biztonsági terv a hálózatot használja elsődleges biztonsági pivotként. A PaaS-telepítések esetén jobban ki szolgálhatja, ha az identitást az elsődleges biztonsági kerületnek tekinti.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Identitási házirend elfogadása elsődleges biztonsági kerületként
A felhőalapú számítástechnika öt alapvető jellemzője közé tartozik a széles körű hálózati hozzáférés, ami kevésbé relevánssá teszi a hálózatközpontú gondolkodást. A felhőalapú számítástechnika nagy részének célja, hogy lehetővé tegye a felhasználók számára az erőforrásokhoz való hozzáférést, függetlenül a helytől. A legtöbb felhasználó számára a tartózkodási helyük valahol az interneten lesz.

Az alábbi ábra bemutatja, hogyan alakult ki a biztonsági peremvonal a hálózat peremétől az identitás pereméig. A biztonság kevésbé szól a hálózat védelméről, hanem az adatok védelméről, valamint az alkalmazások és felhasználók biztonságának kezeléséről. A legfontosabb különbség az, hogy szeretné, hogy a biztonság közelebb, ami fontos, hogy a cég.

![Identitás új biztonsági kerületként](./media/paas-deployments/identity-perimeter.png)

Kezdetben az Azure PaaS-szolgáltatások (például webes szerepkörök és az Azure SQL) nyújtott kevés vagy semmilyen hagyományos hálózati peremvédelmi. Úgy ismerték meg, hogy az elem célja az volt, hogy az internet (webes szerepkör) számára legyen elérhető, és hogy a hitelesítés biztosítja az új kerületet (például BLOB vagy Azure SQL).

A modern biztonsági gyakorlat feltételezi, hogy az ellenség áttörte a hálózat peremét. Ezért a modern védelmi gyakorlatok átkerültek az identitásba. A szervezeteknek identitásalapú biztonsági kerületet kell létrehozniuk erős hitelesítési és engedélyezési higiéniával (ajánlott eljárások).

Elvek és minták a hálózat peremén már évtizedek óta rendelkezésre állnak. Ezzel szemben az iparág viszonylag kevesebb tapasztalattal rendelkezik az identitás elsődleges biztonsági kerületként való használatával kapcsolatban. Ezzel azt mondta, már felhalmozott elég tapasztalat, hogy néhány általános ajánlásokat, amelyek bizonyítottak a területen, és alkalmazni szinte minden PaaS szolgáltatásokat.

Az alábbiakban ajánlott eljárásokat az identitás kerületének kezelésére.

**Ajánlott eljárás:** A kulcsok és a hitelesítő adatok biztonságossá tétele a PaaS-telepítés biztonságossá tétele érdekében.   
**Részlet:** A kulcsok és a hitelesítő adatok elvesztése gyakori probléma. Használhat egy központi megoldást, ahol kulcsok és titkos kulcsok tárolhatók a hardveres biztonsági modulokban (HSM). [Az Azure Key Vault](../../key-vault/key-vault-overview.md) a hitelesítési kulcsok, a tárfiók kulcsai, az adattitkosítási kulcsok, a .pfx-fájlok és a HSM-ek által védett kulcsok használatával történő titkosításával védi a kulcsokat és a titkos kulcsokat.

**Ajánlott eljárás:** Ne helyezzen hitelesítő adatokat és egyéb titkos kulcsokat a forráskódba vagy a GitHubba.   
**Részlet:** Az egyetlen dolog rosszabb, mint a kulcsok és a hitelesítő adatok elvesztése, hogy egy jogosulatlan fél hozzáférhet hozzájuk. A támadók kihasználhatják a robottechnológiák at a kódtárolókban, például a GitHubban tárolt kulcsok és titkos kulcsok megkereséséhez. Ne helyezzen kulcsot és titkos kulcsokat ezekbe a nyilvános kódtárolókba.

**Ajánlott eljárás:** A virtuális gép felügyeleti felületei a hibrid PaaS és IaaS-szolgáltatások egy felügyeleti felület, amely lehetővé teszi, hogy a virtuális gépek közvetlen ülepedése.   
**Részlet:** A távoli felügyeleti protokollok, például [az SSH,](https://en.wikipedia.org/wiki/Secure_Shell) [az RDP](https://support.microsoft.com/kb/186607)és [a PowerShell távoli eljárásátirányítási](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) protokollok használhatók. Általában azt javasoljuk, hogy ne engedélyezze a közvetlen távoli hozzáférést a virtuális gépekhez az internetről.

Ha lehetséges, használjon alternatív módszereket, például virtuális magánhálózatokat egy Azure virtuális hálózatban. Ha nem állnak rendelkezésre alternatív módszerek, győződjön meg arról, hogy összetett jelmondatokat és kétfaktoros hitelesítést (például [Azure multi-factor authentication)](/azure/active-directory/authentication/multi-factor-authentication)használ.

**Ajánlott eljárás:** Használjon erős hitelesítési és engedélyezési platformokat.   
**Részlet:** Az egyéni felhasználói tárolók helyett használjon összevont identitásokat az Azure AD-ben. Ha összevont identitásokat használ, kihasználhatja a platformalapú megközelítést, és átruházza az engedélyezett identitások kezelését a partnereire. Az összevont identitáskezelési megközelítés különösen fontos az alkalmazottak megszüntetésekor, és hogy az információkat több identitás- és engedélyezési rendszeren keresztül kell tükrözni.

Az egyéni kód helyett használjon platformáltal biztosított hitelesítési és engedélyezési mechanizmusokat. Ennek az az oka, hogy az egyéni hitelesítési kód fejlesztése hibaveszélyes lehet. A legtöbb fejlesztő nem biztonsági szakértők, és nem valószínű, hogy tisztában legyenek a finomságok és a legújabb fejlesztések hitelesítés és engedélyezés. A kereskedelmi kódot (például a Microsofttól) gyakran alaposan ellenőrzik a biztonság.

Használjon kétfaktoros hitelesítést. A kétfaktoros hitelesítés a hitelesítés és engedélyezés jelenlegi szabványa, mivel elkerüli a felhasználónév- és jelszótípusokban rejlő biztonsági hiányosságokat. Az Azure-felügyeleti (portál-/távoli PowerShell-) felületekhez és az ügyfélfelé irányuló szolgáltatásokhoz való hozzáférést úgy kell megtervezni és konfigurálni, hogy [az Azure többtényezős hitelesítést használhassa.](/azure/active-directory/authentication/multi-factor-authentication)

Használjon szabványos hitelesítési protokollokat, például Az OAuth2 és a Kerberos protokollt. Ezeket a protokollokat alaposan lektoradtuk, és valószínűleg a platformkódtárak részeként implementálták hitelesítésre és engedélyezésre.

## <a name="use-threat-modeling-during-application-design"></a>Fenyegetésmodellezés használata az alkalmazás tervezése kor
A Microsoft [biztonsági fejlesztési életciklusa](https://www.microsoft.com/en-us/sdl) meghatározza, hogy a csapatoknak a tervezési fázisban részt kell venniük egy fenyegetésmodellezésnek nevezett folyamatban. A folyamat megkönnyítése érdekében a Microsoft létrehozta az [SDL Threat Modeling Tool eszközt.](/azure/security/azure-security-threat-modeling-tool) Az alkalmazástervezésének modellezése és a [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) fenyegetések összes megbízhatósági határon történő számbavétele már korán elkaphatja a tervezési hibákat.

Az alábbi táblázat a STRIDE fenyegetéseket sorolja fel, és néhány példacsökkentést tartalmaz, amelyek az Azure-funkciókat használják. Ezek a mérséklések nem fognak működni minden helyzetben.

| Fenyegetés | Biztonsági tulajdonság | Az Azure platform lehetséges kockázatenyhítései |
| --- | --- | --- |
| Hamisítás | Hitelesítés | HTTPS-kapcsolatok megkövetelése. |
| Hamisít | Integritás | TLS/SSL-tanúsítványok érvényesítése. |
| Megtagadás | Megtagadás elmaradása | Engedélyezze az Azure [figyelését és diagnosztikát.](/azure/architecture/best-practices/monitoring) |
| Az információk nyilvánosságra hozatala | Titkosság | Titkosítsa a bizalmas adatokat [szolgáltatástanúsítványok](/rest/api/appservice/certificates)használatával. |
| Szolgáltatásmegtagadás | Rendelkezésre állás | A szolgáltatásmegtagadási feltételek lehetséges teljesítménymutatóinak figyelése. Kapcsolatszűrők megvalósítása. |
| Jogosultság emelése | Engedélyezés | Használja [a kiemelt identitáskezelést.](/azure/active-directory/privileged-identity-management/subscription-requirements) |

## <a name="develop-on-azure-app-service"></a>Fejlesztés az Azure App Service szolgáltatásban
[Az Azure App Service](/azure/app-service/overview) egy PaaS-ajánlat, amely lehetővé teszi, hogy webes és mobilalkalmazásokat hozzon létre bármilyen platformra vagy eszközre, és csatlakozzon az adatokhoz bárhol, a felhőben vagy a helyszínen. Az App Service tartalmazza azokat a webes és mobilfunkciókat, amelyeket korábban külön szállítottak Azure-webhelyek ként és Az Azure Mobile Services szolgáltatásként. Ezenkívül új lehetőségek is elérhetők az üzleti folyamatok automatizálásához és felhőalapú API-k üzemeltetéséhez. Egyetlen integrált szolgáltatásként az App Service számos funkciót biztosít a webes, mobil- és integrációs forgatókönyvekhez.

Az alábbiakban az App Service használatával kapcsolatos gyakorlati tanácsokat.

**Ajánlott eljárás:** [Hitelesítés az Azure Active Directoryn keresztül.](/azure/app-service/overview-authentication-authorization)   
**Részlet:** Az App Service oauth 2.0 szolgáltatást biztosít az identitásszolgáltató számára. Az OAuth 2.0 az ügyfélfejlesztők egyszerűségére összpontosít, miközben speciális engedélyezési folyamatokat biztosít a webes alkalmazásokhoz, asztali alkalmazásokhoz és mobiltelefonokhoz. Az Azure AD az OAuth 2.0-s használatával engedélyezi a hozzáférést a mobil- és webalkalmazásokhoz.

**Ajánlott eljárás:** Korlátozza a hozzáférést a szükséges ismeretés és a legkisebb jogosultságbiztonsági elvek alapján.   
**Részletes:** A hozzáférés korlátozása elengedhetetlen azon szervezetek számára, amelyek az adatelérésbiztonsági házirendeket szeretnék érvényesíteni. Az RBAC segítségével engedélyeket rendelhet a felhasználókhoz, csoportokhoz és alkalmazásokhoz egy bizonyos hatókörön. Ha többet szeretne tudni arról, hogy miként kaphat hozzáférést a felhasználóknak az alkalmazásokhoz, olvassa el a [Hozzáférés-kezelés – Első lépések a hozzáférés-kezeléssel ( Első lépések a hozzáférés-kezeléssel ) témakört.](/azure/role-based-access-control/overview)

**Ajánlott eljárás:** A kulcsok védelme.   
**Részletes:** Az Azure Key Vault segít megvédeni a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsokat és titkos kulcsokat. A Key Vault segítségével titkosíthatja a kulcsokat és a titkos kulcsokat (például a hitelesítési kulcsokat, a tárfiók kulcsait, az adattitkosítási kulcsokat, a . PFX fájlok és jelszavak) hardveres biztonsági modulokkal (HSM) védett kulcsokkal. A még nagyobb biztonság érdekében lehetőség van arra is, hogy kulcsokat importáljon és generáljon a hardveres biztonsági modulokban. További információért tekintse meg az [Azure Key Vaultot.](/azure/key-vault/key-vault-overview) A Key Vault segítségével is kezelheti a TLS-tanúsítványokat automatikus megújítással.

**Ajánlott eljárás:** Korlátozza a bejövő forrás IP-címeket.   
**Részlet:** [Az App Service Environment](/azure/app-service/environment/intro) rendelkezik egy virtuális hálózati integrációs szolgáltatással, amely segít a bejövő forrás IP-címek hálózati biztonsági csoportokon keresztül történő korlátozásában. A virtuális hálózatok lehetővé teszik, hogy az Azure-erőforrásokat egy nem internetes, irányítható hálózatba helyezze, amelyhez szabályozhatja a hozzáférést. További információ: [Az alkalmazás integrálása Azure virtuális hálózattal.](/azure/app-service/web-sites-integrate-with-vnet)

**Ajánlott eljárás:** Az App Service-környezetek biztonsági állapotának figyelése.   
**Részlet:** Az Azure Security Center segítségével figyelheti az App Service-környezeteket. Amikor a Security Center potenciális biztonsági réseket azonosít, [javaslatokat](../../security-center/security-center-virtual-machine-protection.md) készít, amelyek végigvezetik a szükséges vezérlők konfigurálásának folyamatán.

> [!NOTE]
> Az App Service figyelése előzetes verzióban érhető el, és csak a Security Center [standard szintű szintjén](/azure/security-center/security-center-pricing) érhető el.
>
>

## <a name="install-a-web-application-firewall"></a>Webalkalmazás-tűzfal telepítése
A webalkalmazások egyre inkább ki vannak téve rosszindulatú támadásoknak, amelyek az ismert biztonsági réseket használják ki. Az ilyen jellegű támadások között például gyakoriak az SQL-injektálásos és a webhelyek közötti, parancsprogramot alkalmazó támadások. Az ilyen támadások megakadályozása az alkalmazás kódjában kihívást jelenthet, és szigorú felügyeletet, javítást és megfigyelést igényelhet az alkalmazás topológiájának számos rétegén. A központosított webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és segít az alkalmazás-rendszergazdáknak a fenyegetések vagy a behatolások elleni védekezésben. Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárókat egyszerűen át lehet alakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.

[A webalkalmazás-tűzfal (WAF)](/azure/frontdoor/waf-overview) az Application Gateway egyik szolgáltatása, amely központosított védelmet nyújt a webalkalmazásoknak a gyakori biztonsági rések és biztonsági rések elleni védelemben. A WAF az [Open Web Application Security Project (OWASP) 3.0](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) vagy 2.2.9-es alapszabálykészletének szabályain alapul.

## <a name="monitor-the-performance-of-your-applications"></a>Az alkalmazások teljesítményének figyelése
A figyelés az adatok gyűjtése és elemzése az alkalmazás teljesítményének, állapotának és elérhetőségének meghatározásához. A hatékony monitorozási stratégia pontos információkat nyújt az alkalmazás összetevőinek működéséről. Segít növelni az uptime-ot azáltal, hogy értesíti Önt a kritikus problémákról, hogy megoldhassa azokat, mielőtt problémákká válnak. Emellett segít észlelni a biztonsággal kapcsolatos anomáliákat.

Az [Azure Application Insights](https://azure.microsoft.com/documentation/services/application-insights) segítségével figyelheti az alkalmazás rendelkezésre állását, teljesítményét és használatát, függetlenül attól, hogy az a felhőben vagy a helyszínen van-e. Az Application Insights használatával gyorsan azonosíthatja és diagnosztizálhatja a hibákat az alkalmazásban anélkül, hogy megvárna egy felhasználót, hogy jelentse őket. A gyűjtött információk alapján megalapozott döntéseket hozhat az alkalmazás karbantartásával és továbbfejlesztésével kapcsolatban.

Az Application Insights kiterjedt eszközkészlettel rendelkezik az általa gyűjtött adatok feldolgozásához. Az Application Insights egy általános adattárban tárolja az adatait. Kihasználhatja a megosztott funkciók, például a riasztások, irányítópultok és a Kusto lekérdezési nyelv vel végzett mélyelemzés előnyeit.

## <a name="perform-security-penetration-testing"></a>Biztonsági behatolási vizsgálat végrehajtása
A biztonsági védelem érvényesítése ugyanolyan fontos, mint bármely más funkció tesztelése. A [penetrációs tesztelést](pen-testing.md) a build- és üzembe helyezési folyamat szabványos részévé teheti. Ütemezze a rendszeres biztonsági teszteket és a biztonsági rések ellenőrzését az üzembe helyezett alkalmazásokon, és figyelje a nyitott portokat, végpontokat és támadásokat.

A fuzz tesztelés a programhibák (kódhibák) megtalálásának egyik módszere azáltal, hogy hibás bemeneti adatokat szolgáltat az adatokat elemző és felhasználási programfelületeknek (belépési pontoknak). [A Microsoft Security Risk Detection](https://www.microsoft.com/en-us/security-risk-detection/) egy felhőalapú eszköz, amellyel megkeresheti a szoftver hibáit és egyéb biztonsági réseit, mielőtt telepítené az Azure-ba. Az eszközt úgy tervezték, hogy elkapja a biztonsági réseket a szoftver telepítése előtt, így nem kell kijavítania egy hibát, nem kell kezelnie az összeomlásokat, vagy reagálnia a támadásra a szoftver megjelenése után.


## <a name="next-steps"></a>További lépések
Ebben a cikkben az Azure PaaS-telepítés biztonsági előnyeire és a felhőalapú alkalmazások biztonsági gyakorlati tanácsokra összpontosítottunk. Ezután ismerje meg a PaaS-web- és mobilmegoldások speciális Azure-szolgáltatások használatával történő védelmére vonatkozó ajánlott eljárásokat. Az Azure App Service, az Azure SQL Database és az Azure SQL Data Warehouse, valamint az Azure Storage szolgáltatással kezdjük. Amint más Azure-szolgáltatások ajánlott gyakorlataira vonatkozó cikkek elérhetővé válnak, a hivatkozások az alábbi listában jelennek meg:

- [Azure App Service](paas-applications-using-app-services.md)
- [Azure SQL Database és Azure SQL Data Warehouse](paas-applications-using-sql.md)
- [Azure Storage](paas-applications-using-storage.md)
- Azure Cache for Redis
- Azure Service Bus
- Webalkalmazás-tűzfalak

A felhőhöz való alkalmazások fejlesztésekor a szoftverfejlesztési életciklus minden egyes fázisában figyelembe veheti a biztonságos alkalmazások fejlesztése az [Azure-ban](abstract-develop-secure-apps.md) című témakört.

Tekintse meg [az Azure biztonsági gyakorlati tanácsait és mintáit,](best-practices-and-patterns.md) amelyek további biztonsági gyakorlati tanácsokat tartalmaznak a felhőbeli megoldások tervezése, üzembe helyezése és kezelése során az Azure használatával.

Az alábbi források az Azure biztonságával és a kapcsolódó Microsoft-szolgáltatásokkal kapcsolatos általánosabb információk biztosításához érhetők el:
* [Azure Security Team Blog](https://blogs.msdn.microsoft.com/azuresecurity/) – naprakész információk az Azure Security legújabb adatairól
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – ahol a Microsoft biztonsági rései, beleértve az Azure-ral kapcsolatos problémákat is, jelenthetők, vagy e-mailben asecure@microsoft.com
