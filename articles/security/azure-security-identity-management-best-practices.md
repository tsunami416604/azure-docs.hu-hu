---
title: Az Azure identity & access ajánlott biztonsági eljárások |} A Microsoft Docs
description: Ez a cikk számos ajánlott eljárások az Identitáskezelés és hozzáférés-vezérlés használatával a beépített Azure-képességek.
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 210781b36f6215afc925266e597031d772a94002
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060557"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Az Azure identitáskezelési és hozzáférés-vezérlés ajánlott biztonsági eljárások

Számos fontolja meg az új határ réteg a biztonság érdekében hagyományos hálózati-központú szempontjából a szerepkör át kell identitás. A biztonsági intézkedést és a beruházások származnak, hogy a hálózati régebben egyre elválasztó váltak, és a külső védelem nem lehet hatékonysága, egyszer felbontására előtt is az elsődleges pivot a rendszergazdánál veszi kezdetét [BYOD ](http://aka.ms/byodcg) eszközök és a felhőbeli alkalmazásokhoz.

Ebben a cikkben bemutatjuk, Azure-Identitáskezelés és hozzáférés vezérlés ajánlott biztonsági eljárások gyűjteménye. Ajánlott eljárások vannak származtatva tapasztalatainkon [Azure ad-ben](../active-directory/fundamentals/active-directory-whatis.md) és az ügyfelek a funkciókat, például saját magának.

A minden egyes ajánlott eljárás az hogy ismertetik:

* Mi az az ajánlott eljárás szerint
* Miért ajánlott eljárás, hogy engedélyezni szeretné
* Mi lehet az eredmény, ha Ön nem engedélyezi az ajánlott eljárás szerint
* Az ajánlott eljárás szerint lehetséges alternatívák
* Hogyan tudhat meg az ajánlott eljárás engedélyezése

Az Azure-Identitáskezelés és hozzáférés-vezérléssel – gyakorlati tanácsok cikk alapján egy konszenzus véleményével és az Azure platform képességeit és szolgáltatáskészleteket, időben léteznek ebben a cikkben íródott. Vélemények és technológiák időbeli változásait, és ez a cikk a változások követése érdekében rendszeresen frissül.

Azure-identitás és hozzáférés vezérlés ajánlott biztonsági eljárások a cikkben leírtak szerint a következők:

* Központosíthatja az Identitáskezelés
* Egyszeri bejelentkezés (SSO) engedélyezése
* Jelszókezelés üzembe helyezése
* Többtényezős hitelesítés (MFA) a felhasználók számára
* Szerepköralapú hozzáférés-vezérlés (RBAC)
* Szabályozhatja a helyek, ahol jönnek létre erőforrások Resource Manager használatával
* Útmutató a fejlesztők így hasznosíthatják a SaaS-alkalmazások identitáskezelési funkciókat
* Aktív monitorozása a gyanús tevékenységek esetén

## <a name="centralize-your-identity-management"></a>Központosíthatja az Identitáskezelés

A személyazonosság védelme felé egy fontos lépés, hogy győződjön meg arról, hogy az informatikai részleg fiókok kezelése egyetlen helyről kapcsolatban, ahol ezt a fiókot létrehozták. Míg a vállalat informatikai szervezetek többsége elsődleges fiók directory a helyszíni, hibrid felhőbeli környezetek fellendülőben van, és fontos, hogy megismerte, hogyan integrálhatja a helyszíni és felhőbeli címtárakban és biztosítanak egy zökkenőmentes élmény a végfelhasználók számára.

Ehhez [hibrid identitás](../active-directory/active-directory-hybrid-identity-design-considerations-overview.md) forgatókönyvben javasoljuk, hogy két lehetőség közül választhat:

* Szinkronizálás a helyszíni címtár Azure AD Connect használatával a felhőalapú címtárral
* Engedélyezze az egyszeri bejelentkezés az [Jelszókivonat-szinkronizálás](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization), [átmenő hitelesítés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq) vagy a helyszíni identitását a felhőbeli címtár használata az összevonni [Active Directory Összevonási szolgáltatások](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) (AD FS)

Szervezetek, amelyek a helyszíni identitás integrálása a felhőbeli identitás nem tapasztalható nagyobb adminisztrációs terhelés a fiókok, kezelése, ami növeli az elágazás hibákkal és biztonsági résekkel szemben.

Az Azure AD szinkronizálási további információkért tekintse meg a cikket [a helyszíni identitások integrálása az Azure Active Directory](../active-directory/active-directory-aadconnect.md).

## <a name="enable-single-sign-on-sso"></a>Egyszeri bejelentkezés (SSO) engedélyezése

Ha több címtár kezelésére, ez lesz a nem csupán a felügyeleti probléma informatikai, hanem az végfelhasználók számára, amelyeket több jelszó megjegyzése. Használatával [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) , adja meg a felhasználók azon képessége, ugyanazokat a hitelesítő adatok használata jelentkezik be, és elérhessék az erőforrásokat, amelyek szükségük van, függetlenül attól, ha az erőforrás a helyszínen vagy a felhőben.

Egyszeri bejelentkezés segítségével a felhasználók számára elérhetővé a [SaaS-alkalmazások](../active-directory/manage-apps/what-is-single-sign-on.md) alapján saját szervezeti fiókjukba, az Azure ad-ben. Ez akkor nem csupán a Microsoft SaaS-alkalmazások, hanem más alkalmazásoknál, mint például [Google Apps](../active-directory/saas-apps/google-apps-tutorial.md) és [Salesforce](../active-directory/saas-apps/salesforce-tutorial.md). Az alkalmazás használatához Azure AD szolgáltatásba konfigurálható egy [SAML-alapú identitás](../active-directory/fundamentals-identity.md) szolgáltató. Biztonsági vezérlőként Azure ad-ben a jogkivonat lehetővé teheti, hogy jelentkezzen be az alkalmazást, kivéve, ha azok kapott hozzáférést az Azure AD-vel nem ad ki. Előfordulhat, hogy hozzáférést közvetlenül, vagy egy csoportot, hogy azok a tagja.

> [!NOTE]
> az egyszeri bejelentkezés használata mellett befolyásolja, hogy hogyan integrálja a helyszíni címtárban a felhőalapú címtárral. Ha azt szeretné, hogy egyszeri Bejelentkezést, meg kell használnia összevonási, mert a címtár-szinkronizálás csak biztosít [azonos a bejelentkezési élmény](../active-directory/active-directory-aadconnect.md).
>
>

Szervezetek számára, amelyek nem tesszük kötelezővé egyszeri Bejelentkezést a felhasználók és alkalmazások számára több jelennek meg a forgatókönyvek, ahol a felhasználók rendelkeznek több jelszóra, amely közvetlenül növeli az elágazás felhasználó újbóli felhasználása a jelszavak, vagy gyenge jelszót.

További információ az Azure AD SSO a cikk elolvasásával [AD FS kezelése és testreszabása az Azure AD Connecttel](../active-directory/active-directory-aadconnect-federation-management.md).

## <a name="deploy-password-management"></a>Jelszókezelés üzembe helyezése

Forgatókönyvek, ahol több bérlő vagy felhasználók számára engedélyezni kívánja a [saját jelszó visszaállítása](../active-directory/user-help/active-directory-passwords-update-your-own-password.md), fontos, hogy a megfelelő biztonsági házirendek visszaélés megakadályozására használja. Az Azure-ban az önkiszolgáló jelszó-visszaállítás funkcióval, és testre szabhatja az üzleti követelmények figyelembevételével biztonsági beállítások.

Fontos visszajelzés beszerezni ezeket a felhasználókat, és ismerje meg tapasztalataikat, amikor ezekről a műveletekről. Ezek a tapasztalatok alapján, kidolgozza a csomag egy nagyobb csoport központi telepítése során esetlegesen előforduló lehetséges problémák megoldásához. Célszerű használni az [jelszó alaphelyzetbe állítása regisztrációs Tevékenységjelentés](../active-directory/active-directory-passwords-get-insights.md) figyelheti a felhasználókat, hogy regisztrálja.

Szervezetek számára, amelyek szeretné kerülni a jelszó módosítása segélykérő hívások számát, de engedélyezi a felhasználóknak jelszavuk átállítását érzékenyebbek egy magasabb hívások mennyisége, a szolgáltatás desk jelszóval kapcsolatos problémák miatt. Azoknál a szervezeteknél, több bérlő amihez elengedhetetlen, hogy az ilyen típusú funkció megvalósítását és engedélyezése a felhasználók jelszó-visszaállítást a biztonsági szabályzatban meghatározott biztonsági határon belül végrehajtani.

További információ a jelszóátállítás című cikkben [jelszókezelés üzembe helyezése és a felhasználók betanítása a használatára](../active-directory/authentication/howto-sspr-deployment.md).

## <a name="enforce-multi-factor-authentication-mfa-for-users"></a>Többtényezős hitelesítés (MFA) a felhasználók számára

Olyan szervezeteknek, amelyek meg kell felelnie az iparági szabványok, például kell [PCI DSS 3.2-es verziójú](http://blog.pcisecuritystandards.org/preparing-for-pci-dss-32), multi-factor authentication a kell hitelesíteni a felhasználókat a képességgel rendelkező. Nem felelnek meg az iparági normák, felhasználók hitelesítése többtényezős hitelesítés kényszerítése is megkönnyíti a szervezetek számára csökkentése érdekében a hitelesítő adatok ellopása típusú támadások, mint például [Pass-the-Hash (PtH)](http://aka.ms/PtHPaper).

Az Azure MFA engedélyezése a felhasználók számára, egy második biztonsági réteggel hozzáadni felhasználói bejelentkezéseket és tranzakciókat. Ebben az esetben egy tranzakció lehetséges, hogy használja a dokumentum egy fájlkiszolgáló vagy a SharePoint Online-ban található. Az Azure MFA is segít, hogy így kevésbé valószínű, hogy egy sérült biztonságú hitelesítő adat rendelkezik-e a munkahelyi adatokhoz való hozzáférést.

Például: az Azure MFA kényszerítéséhez a felhasználók számára, és konfigurálja úgy, hogy az ellenőrzés a telefonhívás vagy SMS-t használja. Ha a felhasználói hitelesítő adatok integritása sérül, a támadó nem sikerül bármely erőforráshoz hozzáférhet, mivel a hozzáférést a felhasználó telefonja nem rendelkeznek. A szervezetek, ne vegyen fel több réteg az identity protection jobban ki a hitelesítő adatok ellopását alkalmazó támadásokkal, amely lehet a biztonsági adatokat.

Olyan szervezeteknek, amelyek szeretné tartani a teljes hitelesítés vezérlőelem a helyszínen egy alternatív használatára [Azure multi-factor Authentication-kiszolgáló](../active-directory/authentication/howto-mfaserver-deploy.md), más néven a helyszíni MFA. Ez a módszer segítségével, továbbra is képesek lesznek a multi-factor authentication, miközben a kiszolgáló helyszíni MFA kényszerítésére.

További információ az Azure MFA, tekintse meg a cikket [Ismerkedés az Azure multi-factor Authentication a felhőben](../active-directory/authentication/howto-mfa-getstarted.md).

## <a name="use-role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)

A hozzáférés korlátozása alapján a [ismernie kell](https://en.wikipedia.org/wiki/Need_to_know) és [legalacsonyabb jogosultsági](https://en.wikipedia.org/wiki/Principle_of_least_privilege) biztonsági alapelveket elengedhetetlen, szervezetek által adatok elérésére vonatkozó biztonsági szabályzatok kikényszerítéséhez. Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) segítségével engedélyek hozzárendelése a felhasználók, csoportok és alkalmazások egy bizonyos hatókörben. Szerepkör-hozzárendelés hatóköre egy előfizetés, erőforráscsoport vagy egyetlen erőforrás lehet.

Kihasználhatja [RBAC beépített](../role-based-access-control/built-in-roles.md) szerepkörök az Azure-ban jogosultságok hozzárendelése a felhasználókhoz. Fontolja meg *Tárfiók-közreműködő* a felhő üzemeltetői, amely szükséges a storage-fiókok kezelése és *klasszikus Tárfiók-közreműködő* szerepkör a klasszikus tárfiókok kezelését. A felhő üzemeltetői igénylő virtuális gépek és a storage-fiók kezeléséhez, vegye figyelembe, hogy hozzáadják őket *virtuális gépek Közreműködője* szerepkör.

Szervezetek számára, amelyek nem tesszük kötelezővé a hozzáférés-vezérlés a képességek, például az RBAC használatával előfordulhat, hogy kell jogosultságot ad mint azok a felhasználók számára szükséges további jogosultságokkal. Ezzel a módszerrel adatok a biztonsági sérülés engedélyezése a felhasználók egyes adattípusok (például a nagy üzletmenetre gyakorolt hatás), az elsőként nem rendelkeznek hozzáféréssel.

További információ az Azure RBAC című cikkben [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md).

## <a name="control-locations-where-resources-are-created-using-resource-manager"></a>Szabályozhatja a helyek, ahol jönnek létre erőforrások Resource Manager használatával

Fontos, amely lehetővé teszi a felhő üzemeltetői megakadályozza, hogy tönkretegye egyezmények a szervezet erőforrásaihoz kezeléséhez szükséges feladatok végrehajtásához. A szervezetek kívánják vezérelni a helyeken, ahol erőforrások jönnek létre, keményen kell kód ezeket a helyeket.

Ennek érdekében a szervezetek, amelyek ismertetik a műveletek vagy elutasított erőforrás definíciókkal rendelkező biztonsági házirendeket hozhatnak létre. Ezen szabályzat-definíciókat a kívánt hatókörben, például az előfizetés, erőforráscsoport vagy egyedi erőforrásokat rendelhet hozzá.

> [!NOTE]
> Ez nem ugyanaz, mint az RBAC, a modul ténylegesen rbac-t, amely rendelkezik jogosultsággal a ezekhez az erőforrásokhoz a felhasználók hitelesítésére.
>
>

Használja ki [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) hozhat létre egyéni szabályzatokat is forgatókönyvek, ahol a szervezet által támogatni kívánt műveletek csak akkor engedélyezi a megfelelő költségközpont társítva; ellenkező esetben ezek visszautasítja a kérelmüket.

Szervezetek számára, amelyek nem elsődlegesek hogyan jönnek létre az erőforrások jobban ki a felhasználók számára, előfordulhat, hogy a szolgáltatás való visszaélés további erőforrásokat kell létrehozásával. Az erőforrás-létrehozási folyamata korlátozására egy több-bérlős forgatókönyvben védelme fontos lépés.

További információ az Azure Resource Manager szabályzatok létrehozásával című cikkben [Mi az Azure Policy?](../azure-policy/azure-policy-introduction.md)

## <a name="guide-developers-to-leverage-identity-capabilities-for-saas-apps"></a>Útmutató a fejlesztők így hasznosíthatják a SaaS-alkalmazások identitáskezelési funkciókat

Felhasználói identitás ki sokféle helyzetben, amikor a felhasználó hozzáfér [SaaS-alkalmazások](https://azure.microsoft.com/marketplace/active-directory/all/) , amely integrálható a helyszíni vagy felhőalapú címtár. Mindenekelőtt, azt javasoljuk, hogy a fejlesztők olyan biztonságos módszert használja ezeket az alkalmazásokat, például a fejlesztéshez [Microsoft biztonságos fejlesztési Életciklussal (SDL)](https://www.microsoft.com/sdl/default.aspx). Az Azure AD leegyszerűsíti a hitelesítést, az iparági szabványnak számító protokollokat például támogatási azáltal, hogy identitás szolgáltatásként, a fejlesztők [OAuth 2.0](http://oauth.net/2/) és [OpenID Connect](http://openid.net/connect/), valamint a nyílt forráskódú a szalagtárak különböző platformokon.

Ügyeljen arra, hogy Regisztráljon minden olyan alkalmazás, amely az Azure AD-hitelesítés outsources, ez egy kötelező eljárást. Ez hibakezelése azért, hogy az Azure AD kell az alkalmazás a kommunikációs koordinációt, amikor a jogkivonatok bejelentkezés (SSO) kezelése vagy cseréje. A felhasználói munkamenet lejár, ha lejár az Azure AD által kiállított jogkivonatok élettartamát. Mindig értékelje ki, ha az alkalmazás most kell használnia, vagy ha ezúttal csökkentheti. Csökkenti a élettartamát, amely kényszeríti jelentkezzen ki, hogy a felhasználók alapján az inaktivitási biztonsági intézkedésként működhet.

Lehet, hogy a szervezetek számára, amelyek nem tesszük kötelezővé identitás-ellenőrző az alkalmazások eléréséhez, és nem útmutató a biztonságos alkalmazások integrálása az identitáskezelési rendszerekkel való fejlesztő több ki van téve a hitelesítőadat-lopás típusú támadások, mint például [gyenge az Open Web Application Security Project (OWASP) első 10 leírt hitelesítést és a munkamenet felügyeleti](https://www.owasp.org/index.php/OWASP_Top_Ten_Cheat_Sheet).

További hitelesítési forgatókönyvek az SaaS-alkalmazásokkal kapcsolatban olvassa el [hitelesítési forgatókönyvek az Azure ad-ben](../active-directory/active-directory-authentication-scenarios.md).

## <a name="actively-monitor-for-suspicious-activities"></a>Aktív monitorozása a gyanús tevékenységek esetén

A következők szerint [Verizon 2016 adatok illetéktelen behatolás jelentés](http://www.verizonenterprise.com/verizon-insights-lab/dbir/2016/), hitelesítő még mindig az okot, és a számítógépes bűnözők váljon a leginkább nyereséges üzleti egyik. Ezért fontos, az aktív identitást figyelő rendszer, amely gyorsan észlelheti a gyanús tevékenységgel és egy riasztást, további vizsgálat. Az Azure AD rendelkezik két fő képességeket, amelyek segítségével a szervezetek figyelhetik az identitások: prémium szintű Azure AD [anomáliadetektálási jelentések](../active-directory/active-directory-view-access-usage-reports.md) és az Azure AD [identity protection](../active-directory/active-directory-identityprotection.md) képesség.

Ügyeljen arra, hogy az anomáliadetektálási jelentések segítségével azonosíthatja regisztrációs kísérletek [nélkül követett](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md), [találgatásos](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) egy bizonyos fiók elleni támadásokat, több helyről történő bejelentkezéshez, jelentkezzen be a [próbál fertőzött eszközökről, és a gyanús IP-címek. Ne feledje, hogy ezek a jelentések. Más szóval folyamatokat és eljárásokat kell rendelkeznie a rendszergazdáknak, hogy ezek a jelentések futtatása, a napi alapon, vagy igény szerint (általában az incidensmegoldási forgatókönyvhöz) helyen.

Ezzel szemben az Azure AD identity protection az egy aktív monitorozási rendszer, és azt megőrzendő tartalomként jelöli meg a saját irányítópulton a jelenlegi kockázatok. Amellett, hogy napi összefoglaló értesítések e-mailen keresztül is kap. Azt javasoljuk, hogy az üzleti igényeknek megfelelően módosíthatja a kockázati szintjét. Egy kockázati esemény kockázati szintje (magas, közepes vagy alacsony), a kockázati esemény súlyosságát jelzi. A kockázati szint segít az Identity Protection rangsorolhatja a műveleteket kell végrehajtaniuk kockázatának csökkentése érdekében a szervezet számára.

A szervezetek, aktívan nem figyeli az identitáskezelési rendszerek fenyegeti, hogy a felhasználói hitelesítő adatok biztonsága sérült. A gyanús tevékenységek tudomása nélkül helyezze el a következő hitelesítő adatokkal, szervezetek nem fogja tudni az ilyen típusú fenyegetés mérséklésére.
További információ az Azure Identity protection olvasásával [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md).
