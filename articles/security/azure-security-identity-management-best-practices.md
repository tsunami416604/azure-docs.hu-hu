---
title: Az Azure identity & access ajánlott biztonsági eljárások |} Microsoft Docs
description: Ez a cikk számos gyakorlati tanácsok az Identitáskezelés, és a beépített hozzáférés-vezérlése segítségével Azure-képességek.
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: yurid
ms.openlocfilehash: 2b77167992704bd342d67dc03b66af1e55955532
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Az Azure Identitáskezelés és hozzáférés szabályozása ajánlott biztonsági eljárások
Számos fontolja meg az identitást kell lennie az új határ réteg a biztonság érdekében, hogy a hagyományos hálózati-központú szempontjából szerepkör tovább tart. Ez az elsődleges pivot fejlődéséhez vonatkozó biztonsági figyelmet és beruházások származik, hogy a hálózati kialakítását egyre elválasztó vált, és a külső védelem nem lehet hatásos azok egyszer volt felbontására előtt [BYOD ](http://aka.ms/byodcg) eszközök és a felhőalapú alkalmazásokhoz.

Ez a cikk arról lesz szó Azure Identitáskezelés és access control ajánlott biztonsági eljárások gyűjteménye. Az alábbi gyakorlati tanácsok a tapasztalatunk származó [az Azure AD](../active-directory/active-directory-whatis.md) és az ügyfelek feladatokat, például saját maga.

A minden ajánlott eljárás az hogy ismertetik:

* Mi az az ajánlott eljárás szerint
* Miért érdemes, hogy a legjobb engedélyezése
* Milyen lehet az eredmény, ha nem engedélyezi az ajánlott eljárás szerint
* Az ajánlott eljárás szerint lehetséges alternatívák
* Hogyan megismerheti az ajánlott eljárás engedélyezése

Az Azure-identitás kezelése és hozzáférés-vezérléssel gyakorlati tanácsok cikk alapuló egy együttműködési véleményt és az Azure platform olyan képességeit és a szolgáltatáskészletek, időpontjában léteznek a cikk írásának. Vélemények és technológiák változnak az idők, és ez a cikk a változások követése érdekében rendszeresen frissül.

Azure-identitás- kezelésben vezérlő ajánlott biztonsági eljárások cikkben említett a következők:

* Az Identitáskezelés központosítása
* Egyszeri bejelentkezés (SSO) engedélyezése
* A jelszókezelés telepítése
* A felhasználók a többtényezős hitelesítést (MFA) kényszerítése
* Szerepköralapú hozzáférés-vezérlést (RBAC) használata
* Szabályozhatja a helyek, ahol erőforrások jönnek létre erőforrás-kezelő használatával
* Útmutató a fejlesztők kihasználhatják identitási képességeibe SaaS-alkalmazásokhoz
* Aktívan figyeli, hogy gyanús tevékenységek

## <a name="centralize-your-identity-management"></a>Az Identitáskezelés központosítása
Egy fontos lépés felé biztonságossá tétele a személyazonosságát, hogy győződjön meg arról, hogy az informatikai részleg fiókok kapcsolatban, ahol ez a fiók létrejött egy egyetlen helyről kezelheti. Bár a legtöbb, a vállalat informatikai szervezetek rendelkezik az elsődleges fiók címtár a helyszínen, hibrid felhőben történő alkalmazáshoz az okot, és fontos, hogy Ön segít megérteni, hogyan integrálható a helyszíni és felhőbeli címtárakban, és zökkenőmentes a felhasználói élmény a végfelhasználók.

Ehhez [hibrid identitás](../active-directory/active-directory-hybrid-identity-design-considerations-overview.md) forgatókönyvben javasoljuk két lehetőség közül választhat:

* A helyszíni címtárral szinkronizálja az Azure AD Connect használatával a felhő címtárral
* Egyszeri bejelentkezés engedélyezése a [Jelszókivonat-szinkronizálást](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization), [átmenő hitelesítés](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq) vagy összevonni a helyszíni identitás a felhő directory használatáról [Active Directory Összevonási szolgáltatások](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/deploying-federation-servers) (AD FS)

A helyszíni identitás integrálható a felhőalapú identitás nem válaszoló szervezetek felhasználói élmény a rendszergazdára felügyelni a fiókok, ami növeli a hibák és a biztonsági résekkel szemben kialakulásának.

Az Azure AD szinkronizálási további információkért lásd: a cikk [a helyszíni identitások integrálása az Azure Active Directoryval](../active-directory/active-directory-aadconnect.md).

## <a name="enable-single-sign-on-sso"></a>Egyszeri bejelentkezés (SSO) engedélyezése
Amikor kezeléséhez több címtárral rendelkezik, ez lesz-e nem csak a felügyeleti probléma informatikai, hanem a végfelhasználók számára, hogy több jelszó megjegyzése. A [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) Megadja, hogy a felhasználók képességét használja ugyanazokat a hitelesítő adatok bejelentkezhet és elérheti az erőforrásokat, amelyek van szükségük, attól függetlenül történik Ha ezt az erőforrást a helyszínen vagy a felhőben.

Engedélyezze a felhasználók hozzáférhessenek az SSO használatával a [SaaS-alkalmazásokhoz](../active-directory/active-directory-appssoaccess-whatis.md) alapján saját szervezeti fiókjukba, az Azure ad-ben. Ez a tulajdonság vonatkozik nem csak a Microsoft SaaS-alkalmazásokhoz, emellett pedig más alkalmazások, például a [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) és [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). Az alkalmazás használatához az Azure AD szolgáltatásba konfigurálható egy [SAML-alapú identitás](../active-directory/fundamentals-identity.md) szolgáltató. Biztonsági ellenőrzés, mint az Azure AD lehetővé teheti, hogy jelentkezzen be az alkalmazáshoz, kivéve, ha azok nem biztosít hozzáférést az Azure AD jogkivonat nem állítanak ki. Előfordulhat, hogy megadta a hozzáférést közvetlenül, vagy egy csoport tagjai legyenek.

> [!NOTE]
> alkalmazással való SSO használata befolyásolja, hogy hogyan integrálja a helyszíni címtár és a a felhő címtárának. Ha azt szeretné, hogy egyszeri Bejelentkezést, meg kell használnia összevonási, mert csak biztosít a címtár-szinkronizálás [ugyanazt a bejelentkezési élményt](../active-directory/active-directory-aadconnect.md).
>
>

A szervezeteknek, amelyek nem kényszerítése egyszeri Bejelentkezést a felhasználók és az alkalmazások több érhetők el forgatókönyveket, amelyekben a felhasználók rendelkeznek több jelszót, amely közvetlenül növeli az elágazás felhasználó újból felhasználja a jelszavak, vagy gyenge jelszóval.

További Azure AD egyszeri Bejelentkezéssel kapcsolatos által a cikk elolvasása [AD FS kezelése és testreszabása az Azure AD Connect](../active-directory/active-directory-aadconnect-federation-management.md).

## <a name="deploy-password-management"></a>A jelszókezelés telepítése
Forgatókönyvek, ahol több bérlő van, vagy a felhasználók számára engedélyezni szeretné a [saját jelszó visszaállítása](../active-directory/active-directory-passwords-update-your-own-password.md), fontos, hogy megfelelő biztonsági házirendek segítségével visszaélés megakadályozására. Az Azure használja az önkiszolgáló jelszó-visszaállítási funkció, és testre szabhatja az üzleti követelményeknek megfelelő biztonsági beállítások.

Fontos visszajelzés beszerezni ezeket a felhasználókat, és azok felületéről ismerje meg, akkor a következő lépésekkel próbálkozhat. Ezek a tapasztalatok alapján, lehetséges, egy nagyobb csoport központi telepítése során esetlegesen előforduló problémák elhárítása érdekében a terv kidolgozása. Használatát is javasoljuk a [jelszó alaphelyzetbe állítása regisztrációs Tevékenységjelentés](../active-directory/active-directory-passwords-get-insights.md) figyelheti a felhasználókat, akik próbál regisztrálni.

A szervezeteknek, amelyek el szeretné kerülni a jelszó módosítása támogatási hívások, de engedélyezi a felhasználók visszaállíthassák a saját jelszavukat több egy magasabb hívások száma a szolgáltatás beérkező jelszóval kapcsolatos problémák miatt. A több bérlő rendelkező szervezeteknek rendkívül fontos, hogy az ilyen típusú funkció végrehajtására, és lehetővé teszi a felhasználók jelszó-visszaállítást a biztonsági szabályzatban létrehozott biztonsági határon belül végrehajtani.

További információ a jelszó alaphelyzetbe állítását a cikk elolvasása [jelszókezelés üzembe helyezése és a felhasználók betanítása a használatára](../active-directory/authentication/howto-sspr-deployment.md).

## <a name="enforce-multi-factor-authentication-mfa-for-users"></a>A felhasználók a többtényezős hitelesítést (MFA) kényszerítése
Meg kell felelnie az ipari szabványok, például a szervezetek [PCI DSS 3.2-es verziójú](http://blog.pcisecuritystandards.org/preparing-for-pci-dss-32), multi-factor authentication egy kell képességgel a felhasználók hitelesítésére. Az iparágban elfogadott megoldásokhoz tesznek, túl hitelesíti a felhasználókat, hogy többtényezős hitelesítés kényszerítése is hozzájárulhat a csökkentése érdekében a hitelesítő adatokkal való visszaéléseket típusú támadás, például a szervezetek [Pass-the-Hash (PtH)](http://aka.ms/PtHPaper).

A felhasználók számára az Azure MFA engedélyezésével ad hozzá egy második biztonsági réteggel felhasználói bejelentkezéseket és tranzakciókat. Ebben az esetben egy tranzakció lehetséges, hogy használja a dokumentum egy fájlkiszolgálón, vagy a SharePoint Online-ban található. Az Azure MFA lehetővé teszi a IT-kevésbé valószínű, hogy sérült biztonságú hitelesítő adatot fér hozzá a szervezet adatait.

Például: Azure MFA kényszerítéséhez a felhasználók számára, és konfigurálja úgy, hogy telefonhívást vagy SMS-üzenet használata ellenőrzése. Ha a felhasználói hitelesítő adatok integritása sérül, a támadó nincs hogy bármilyen olyan erőforrás elérésére, mivel azok nincs hozzáférése a felhasználó telefonjára. A szervezetek, ne vegyen fel további identitás védelmi réteget amelyek jobban ki vannak téve a hitelesítő adatok jelszóellopásos támadáshoz, ami azt eredményezheti, hogy az adatok biztonsági sérülése.

Olyan szervezeteknek, amelyek az egész hitelesítési vezérlő helyszíni megtartja egy alternatív, hogy használja [Azure multi-factor Authentication kiszolgáló](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), más néven az MFA a helyszínen. Ez a módszer használatával is tudja kényszeríteni a többtényezős hitelesítést a multi-factor Authentication kiszolgáló helyszíni megtartásával.

Az Azure MFA további információkért lásd: a cikk [Ismerkedés az Azure multi-factor Authentication a felhőben](../active-directory/authentication/howto-mfa-getstarted.md).

## <a name="use-role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlést (RBAC) használata
Alapján történő hozzáférés a [tudniuk kell, hogy](https://en.wikipedia.org/wiki/Need_to_know) és [legalacsonyabb jogosultsági szint](https://en.wikipedia.org/wiki/Principle_of_least_privilege) biztonsági elveket elengedhetetlen a szervezeteknek, amelyek az adatok biztonsági házirendek kikényszerítéséhez. Azure szerepköralapú hozzáférés-vezérlés (RBAC) segítségével engedélyek hozzárendelése a felhasználók, csoportok és alkalmazások egy adott hatókörben. A szerepkör-hozzárendelés hatóköre lehet előfizetés, egy erőforráscsoport vagy egy erőforrást.

Kihasználhatja [beépített RBAC](../role-based-access-control/built-in-roles.md) szerepkörök az Azure-jogosultságok hozzárendelése felhasználókhoz. Érdemes lehet *tárolási fiók közreműködői* a felhő üzemeltetői, amely szükséges a storage-fiókok kezelése és *klasszikus tárolási fiók közreműködői* szerepkör kezelése a klasszikus tárfiókokat. A felhő üzemeltetői, amelyet a virtuális gépek és a tárfiók kezelése, fontolja meg, hogy *virtuális gép közreműködő* szerepkör.

A szervezeteknek, amelyek kényszeríti ki a hozzáférés-vezérlés képességeinek például RBAC által előfordulhat, hogy kell jogosultságot ad mint azok a felhasználók számára szükséges további engedélyekkel. Ennek eredményeképpen előfordulhat adatok biztonsági sérülés által felhasználóknak hozzáférést bizonyos típusú adatok (például a nagy üzleti jelentőség), amelyek nem rendelkeznek az első alkalommal.

További tudnivalók az Azure RBAC által a cikk elolvasása [átruházásához hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md).

## <a name="control-locations-where-resources-are-created-using-resource-manager"></a>Szabályozhatja a helyek, ahol erőforrások jönnek létre erőforrás-kezelő használatával
A felhő üzemeltetői feladatait közben megakadályozza az egyezmények, amelyek szükségesek ahhoz, hogy a szervezet erőforrásaihoz kezelése megtörje engedélyezése egy fontos. A szervezeteknek, amelyek a helyeken, ahol erőforrások jönnek létre vezérelhető merevlemez kell code ezeket a helyeket.

Ennek érdekében a szervezetek a műveleteket, vagy az elutasított erőforrások leíró definíciókkal rendelkező biztonsági házirendeket hozhat létre. E házirend-definíciók a kívánt hatókörben, például az előfizetés, erőforráscsoport vagy egy egyéni erőforrást lehet kijelölni.

> [!NOTE]
> Ez nem ugyanaz, mint az RBAC, ténylegesen kihasználja a Szerepalapú az erőforrások létrehozásához jogosultsággal rendelkező felhasználók hitelesítésére.
>
>

Használja ki az [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) is az olyan helyzeteket, ahol a szervezet szeretné műveletek engedélyezése csak akkor, ha a megfelelő költségközpont tartozik; ellenkező esetben ezek visszautasítja a kérelmet, egyéni szabályzat létrehozásához.

A szervezeteknek, amelyek nem elsődlegesek erőforrások létrehozását olyan jobban ki vannak téve a felhasználók számára, előfordulhat, hogy a szolgáltatás való visszaélés van szükségük további erőforrásokat létrehozásával. Az erőforrás létrehozásának folyamatát korlátozására egy több-bérlős forgatókönyv biztonságos fontos eleme.

A cikk elolvasása által az Azure Resource Manager házirendek létrehozásával kapcsolatos részletesebb [Mi az Azure-házirendet?](../azure-policy/azure-policy-introduction.md)

## <a name="guide-developers-to-leverage-identity-capabilities-for-saas-apps"></a>Útmutató a fejlesztők kihasználhatják identitási képességeibe SaaS-alkalmazásokhoz
Felhasználói azonosító helyzetekben alkalmazhatók, amikor a felhasználók hozzáférnek [SaaS-alkalmazások](https://azure.microsoft.com/marketplace/active-directory/all/) , amely integrálható a helyszíni vagy felhőalapú könyvtár. Mindenekelőtt azt javasoljuk, hogy a fejlesztők egy biztonságos módszert használja ezeket az alkalmazásokat, például a fejlesztéséhez [Microsoft biztonságos fejlesztési Életciklussal (SDL)](https://www.microsoft.com/sdl/default.aspx). Az Azure AD hitelesítési egyszerűbbé teszi a fejlesztők azáltal identitás szolgáltatásként, például támogatja az ipari szabványnak számító protokollokat [OAuth 2.0](http://oauth.net/2/) és [OpenID Connect](http://openid.net/connect/), valamint a nyílt forráskódú a szalagtárak különböző platformokon.

Ügyeljen arra, hogy regisztrálja az Azure AD hitelesítési outsources bármely olyan alkalmazás, ez egy kötelező eljárást. Mögött ennek oka az mivel az Azure AD az alkalmazásban a kommunikáció koordinálására, amikor bejelentkezés (SSO) kezelése vagy cseréjét jogkivonatokat. A felhasználói munkamenet lejár, ha az Azure AD által kibocsátott jogkivonat élettartamát lejár. Mindig értékelje ki, ha az alkalmazás most kell használni, vagy ha a megadott idő csökkentése érdekében. Csökkenti a élettartama működhet, és egy biztonsági intézkedés, amely arra kényszeríti kijelentkezés felhasználók alapján az adott ideig tartó tétlenség.

Lehet, hogy jobban ki vannak téve a hitelesítő adatok ellopását típusú támadások, mint például a szervezeteknek, amelyek kényszeríti ki az alkalmazások eléréséhez identitás vezérlő nem a és útmutató a fejlesztő biztonságosan alkalmazások integrálása az identity management rendszerének [gyenge hitelesítés és a munkamenet felügyeleti megnyitása webes alkalmazás biztonsági Project (OWASP) első 10 ismertetett](https://www.owasp.org/index.php/OWASP_Top_Ten_Cheat_Sheet).

További hitelesítési forgatókönyvek az SaaS-alkalmazások kapcsolatos olvasásával [hitelesítési forgatókönyvek az Azure AD](../active-directory/active-directory-authentication-scenarios.md).

## <a name="actively-monitor-for-suspicious-activities"></a>Aktívan figyeli, hogy gyanús tevékenységek
A következők szerint [Verizon 2016 adatok biztonsági szabályok megsértésére jelentés](http://www.verizonenterprise.com/verizon-insights-lab/dbir/2016/), támadását még mindig az okot, és váljon egyik a legnyereségesebb vállalkozások számára számítógépes is. Ezért fontos az aktív figyelő identitásrendszere rendelkezik, amely gyorsan viselkedés gyanús tevékenységek észlelése és a további vizsgálatok riasztást vált ki. Azure AD is rendelkezik két főbb képességeket, amelyek segítségével a szervezetek figyelhetik az identitások: prémium szintű Azure AD [anomáliabiztonsági jelentéseket](../active-directory/active-directory-view-access-usage-reports.md) és az Azure AD [identity protection](../active-directory/active-directory-identityprotection.md) funkció.

Ügyeljen arra, hogy a anomáliadetektálási jelentések segítségével azonosíthatja kísérletet [nélkül nyomon követve](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md), [találgatásos](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) egy különös figyelmet elleni támadások, jelentkezzen be több helyről, és jelentkezzen be az [megkísérli fertőzött eszközök, és a gyanús IP-címét. Ne feledje, hogy ezek a jelentések. Ez azt jelenti folyamatok és eljárások kell rendelkeznie az IT-rendszergazdák napi szinten, vagy az igény szerinti (általában az az incidensekre adott reakciók esetén) ezek a jelentések futtatásához helyen.

Ezzel szemben az Azure AD identity protection egy aktív felügyeleti rendszer, és azt észleli, ha az aktuális kockázatok a saját irányítópultra. Amellett, hogy a is napi összefoglaló értesítést küldjenek e-mailt fog kapni. Azt javasoljuk, hogy az üzleti igényeknek megfelelően beállítani a kockázati szintjét. A kockázati események kockázati szintje feltüntetése (magas, közepes vagy alacsony) a kockázat esemény súlyossága. A kockázati szint segít az Identity Protection priorizálhatja azokat a műveleteket, azok a kockázatok csökkentése a szervezet számára szükséges.

A szervezeteknek, amelyek aktívan figyeli a identitáskezelési rendszereket, hogy sérült felhasználói hitelesítő adatok veszélyben van. A gyanús tevékenységek tudomása nélkül helyezze el a következő hitelesítő adatokkal, a szervezet nem fog tudni az ilyen típusú veszély csökkentése.
További tudnivalók az Azure Identity protection olvasásával [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md).
