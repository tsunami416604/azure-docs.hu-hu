---
title: Gyakori kérdések (GYIK) – az Azure Active Directory |} A Microsoft Docs
description: Gyakori kérdések és válaszok az Azure és az Azure Active Directory, jelszókezelés és alkalmazás-hozzáférés.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.openlocfilehash: 2ff8f9764e61eb7f497e688c880b16529d1d6b8a
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55093104"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Azure Active Directoryval kapcsolatos gyakori kérdések
Az Azure Active Directory (Azure AD) egy átfogó szolgáltatott identitási (IDaaS) megoldás, amely az identitások, a hozzáférés-kezelés és a biztonság minden szempontját lefedi.

További információkért lásd: [Mi az az Azure Active Directory?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Az Azure és az Azure Active Directory elérése
**K: Miért kapok "nem találhatók előfizetések" Amikor megpróbálom elérni az Azure Portalon az Azure AD?**

**VÁLASZ:** Az Azure portal eléréséhez, minden egyes felhasználói engedélyre van szüksége az Azure-előfizetéssel. Ha fizetős Office 365- vagy Azure AD előfizetéssel rendelkezik, látogasson el a [https://aka.ms/accessAAD](https://aka.ms/accessAAD) címre egy egyszeri aktiváláshoz. Egyébként aktiválnia kell egy ingyenes [Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/) vagy egy fizetős előfizetést.

További információkért lásd:

* [How Azure subscriptions are associated with Active Directory? (Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directory-hoz?)](active-directory-how-subscriptions-associated-directory.md)

- - -
**K: Mi az Azure AD közötti kapcsolat az Office 365 és az Azure?**

**VÁLASZ:** Az Azure AD-az összes webes szolgáltatáshoz közös identitás- és hozzáférési képességeket nyújt. Akár az Office 365, a Microsoft Azure, az Intune vagy más rendszert használ, már használja az Azure AD-t a bejelentkezés és a hozzáférés-kezelés bekapcsolásához ezen szolgáltatások mindegyikéhez.

A webes szolgáltatások használatához beállított összes felhasználó felhasználói fiókként van meghatározva egy vagy több Azure AD-példányban. Beállíthatja ezeket a fiókokat az ingyenes Azure AD-képességekhez, például a felhőalkalmazások eléréséhez.

A fizetős Azure AD szolgáltatások, például az Enterprise Mobility + Security, átfogó vállalati méretű felügyeleti és biztonsági megoldásokkal egészítenek ki más webes szolgáltatásokat, például az Office 365-öt és a Microsoft Azure-t.

- - -

**K:  Mik a tulajdonos és a globális rendszergazda közötti különbségekről?**

**VÁLASZ:** Alapértelmezés szerint a személy, aki Azure-előfizetésre regisztrál az Azure-erőforrások tulajdonosi szerepkör van rendelve. Egy olyan tulajdonost Microsoft-fiók vagy a címtárból, amelyhez az Azure-előfizetés társítva van egy munkahelyi vagy iskolai fiókot használhat.  Ez a szerepkör jogosult szolgáltatások kezelésére az Azure Portalon.

Ha másoknak is kell bejelentkezniük és elérniük a szolgáltatásokat az ugyanahhoz az előfizetéshez, hozzárendelheti azokat a megfelelő [beépített szerepkör](../../role-based-access-control/built-in-roles.md). További információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../../role-based-access-control/role-assignments-portal.md).

Alapértelmezés szerint az Azure-előfizetésre feliratkozó személy van rendelve a címtár globális rendszergazdai szerepkörrel. A globális rendszergazda az összes Azure AD-címtár funkciók hozzáféréssel rendelkezik. Az Azure AD különböző rendszergazdai szerepköröket a címtárral és az identitással kapcsolatos funkciók kezeléséhez rendelkezik. Ezek a rendszergazdák különböző funkciókhoz is hozzáférést kap az Azure Portalon. A rendszergazda szerepkör meghatározza, hogy mit tehet, például a létrehozása vagy szerkesztése a felhasználók, rendszergazdai szerepköröket hozzárendelni más, felhasználók új jelszavainak létrehozására, felhasználói licencek kezelése vagy tartományok kezelése.  További információ az Azure AD címtárrendszergazdáival és azok szerepköreivel: [felhasználó hozzárendelése az Azure Active Directory rendszergazdai szerepkörök](active-directory-users-assign-role-azure-portal.md) és [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

Ezenkívül a fizetős Azure AD szolgáltatások, például az Enterprise Mobility + Security, átfogó vállalati méretű felügyeleti és biztonsági megoldásokkal egészítenek ki más webes szolgáltatásokat, például az Office 365-öt és a Microsoft Azure-t.

- - -
**K: Van egy jelentést, amely bemutatja, hogy mikor fog lejárni a saját Azure AD-felhasználói licenceket?**

**VÁLASZ:** Nem.  Ez jelenleg nem érhető el.

- - -

## <a name="get-started-with-hybrid-azure-ad"></a>Első lépések a Hybrid Azure AD-ben


**K: Hogyan hagyja meg a bérlőt, amikor közreműködőként vagyok hozzáadva?**

**VÁLASZ:** Amikor közreműködőként más szervezet bérlőjéhez van hozzáadva, a jobb felső sarokban lévő "bérlőváltó" is használhatja a válthat a bérlők között.  Jelenleg nem hagyhatja el a meghívó szervezetet, és a Microsoft dolgozik ezen a funkción.  A funkció eléréséig megkérheti a meghívó szervezetet, hogy távolítsa el Önt a bérlők közül.
- - -
**K: Hogyan csatlakoztathatom a helyszíni címtár Azure ad-val?**

**VÁLASZ:** Kapcsolódás a helyszíni címtár Azure ad-ben az Azure AD Connect használatával.

További információkért lásd: [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md).

- - -
**K: Hogyan tegye állíthatok be egyszeri Bejelentkezést a helyszíni címtár és a felhőalkalmazásaim között?**

**VÁLASZ:** Csak akkor kell beállítani az egyszeri bejelentkezés (SSO) a helyszíni címtár és az Azure AD között. Amíg az Azure AD-n keresztül éri el a felhőalkalmazásokat, a szolgáltatás automatikusan átirányítja a felhasználókat, hogy megfelelően hitelesíteni tudják magukat a helyszíni hitelesítő adataikkal.

Az egyszeri bejelentkezés helyszínről végzett implementálása könnyen elérhető olyan összevonási megoldásokkal, mint az Active Directory összevonási szolgáltatások (AD FS), illetve a jelszókivonat-szinkronizálás konfigurálásával. Könnyedén telepítheti mindkét lehetőséget az Azure AD Connect konfigurációs varázslójával.

További információkért lásd: [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md).

- - -
**K: Biztosítja az Azure AD önkiszolgáló portál a szervezetemben lévő felhasználók számára?**

**VÁLASZ:** Igen, az Azure AD biztosítja a [Azure AD hozzáférési Panel](https://myapps.microsoft.com) a felhasználóknak az önkiszolgáló és az alkalmazások elérését. Ha Ön Office 365-felhasználó, észreveheti, hogy számos képesség megegyezik az Office 365 portál képességeivel.

További információkért lásd: [Bevezetés a Hozzáférési panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

- - -
**K: Nem az Azure AD segít a helyszíni infrastruktúrám kezelésében?**

**VÁLASZ:** Igen. Az Azure AD Premium Edition tartalmazza az Azure AD Connect Health eszközt. Az Azure AD Connect Health segít megfigyelni a helyszíni identitás-infrastruktúrát és a szinkronizálási szolgáltatásokat, és további betekintést nyújt a működésükbe.  

További információkért lásd: [A helyszíni identitás-infrastruktúra és a szinkronizálási szolgáltatások megfigyelése a felhőben](../hybrid/whatis-hybrid-identity-health.md).  

- - -
## <a name="password-management"></a>Jelszókezelés
**K: Használható az Azure AD jelszóvisszaírást jelszó-szinkronizálás nélkül? (Ebben az esetben használhatom az Azure AD önkiszolgáló jelszó-visszaállítást (SSP) jelszóvisszaírással úgy, hogy nem a felhőben tárolom a jelszavaimat?)**

**VÁLASZ:** Nem kell szinkronizálni az Active Directory-jelszavakat az Azure AD-visszaírás engedélyezéséhez. Összevont környezetben az Azure AD egyszeri bejelentkezés (SSO) a helyszíni címtárra támaszkodik a felhasználó hitelesítéséhez. Ehhez a forgatókönyvhöz nem kell nyomon követni a helyszíni jelszót az Azure AD-ben.

- - -
**K: Mennyi ideig tart a jelszavak visszaírását a helyszíni Active Directory?**

**VÁLASZ:** Jelszó visszaírása valós időben.

További részletekért lásd: [A jelszókezelés első lépései](../authentication/quickstart-sspr.md).

- - -
**K: Használhatok jelszóvisszaírást rendszergazda által kezelt jelszavakhoz?**

**VÁLASZ:** Igen, ha engedélyezve van a késleltetve visszaírt jelszó, a rendszergazdai jelszó jelszóműveleteket a rendszer visszaírja a helyszíni környezetben.  

A jelszavakkal kapcsolatos kérdésekre adott további válaszokért lásd: [Jelszókezelés – gyakori kérdések](../authentication/active-directory-passwords-faq.md).
- - -
**K:  Mi a teendő, ha a jelszó megváltoztatása közben nem emlékszem a meglévő Office 365 vagy az Azure AD jelszó?**

**VÁLASZ:** Az ilyen típusú helyzet van több lehetőség közül választhat.  Használjon önkiszolgáló jelszó-visszaállítást (SSPR), ha elérhető.  Az SSPR konfigurációjától függ, hogy működik-e.  További információkért lásd: [A jelszó-visszaállítási portál működése](../authentication/howto-sspr-deployment.md).

Office 365-felhasználók esetében a rendszergazda alaphelyzetbe állíthatja a jelszót a [Felhasználói jelszavak átállítása](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US) szakaszban ismertetett lépések segítségével.

Azure AD-felhasználók esetében a rendszergazdák a következő módszerek valamelyikével állíthatják alaphelyzetbe a jelszót:

- [Fiókok alaphelyzetbe állítása az Azure Portalon](active-directory-users-reset-password-azure-portal.md)
- [A PowerShell használata](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


- - -
## <a name="security"></a>Biztonság
**K: Fiók zárolva vannak bizonyos számú sikertelen bejelentkezési kísérletek után, vagy van-e ennél kifinomultabb stratégiát használni?**

A fiókok zárolásához ennél kifinomultabb stratégiát alkalmazunk.  A zárolás a kérés IP-címe és a megadott jelszavak alapján történik. A zárolás időtartama annál hosszabb, minél inkább valószínűsíthető, hogy támadásról van szó.  

**K:  Egyes (gyakori) jelszavakat üzenettel dob "ezt a jelszót használtak sokszor", nem ez tekintse meg az aktuális active directory-környezetben használt jelszavakra?**

Ez a visszautasítás a világszerte gyakran használt jelszavakra, például a „Jelszó” (Password) és a „123456” karakterláncok különböző változataira vonatkozik.

**K: A rendszer a kétes forrásokból (botnetek, tor-végpontok) érkező bejelentkezési kérelem blokkolja egy B2C-bérlőben vagy igényel Ez egy alapszintű vagy a prémium szintű bérlőre?**

Az átjárónk szűri a kéréseket és bizonyos fokú védelmet biztosít a botnetek ellen. Ez minden B2C-bérlőre vonatkozik.

## <a name="application-access"></a>Alkalmazás-hozzáférés

**K: Hol találok az Azure ad-vel előre integrált alkalmazások listáját és azok képességeinek?**

**VÁLASZ:** Az Azure AD a Microsoft, az alkalmazásszolgáltatók és a partnerek több mint 2,600 előre integrált alkalmazások rendelkezik. Mindegyik előre integrált alkalmazás támogatja az egyszeri bejelentkezést (SSO). Az egyszeri bejelentkezéssel a szervezeti hitelesítő adatokkal érheti el az alkalmazásokat. Néhány alkalmazás az automatikus üzembe helyezést és megszüntetést is támogatja.

Az előre integrált alkalmazások teljes listájáért lásd: [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

- - -
**K: Mi történik, ha van szükségem az alkalmazás nem szerepel az Azure AD Marketplace-en?**

**VÁLASZ:** Az Azure AD Premium szolgáltatással adja hozzá, és konfigurálja a kívánt bármely alkalmazás. Az alkalmazás képességeitől és a beállításaitól függően konfigurálhat egyszeri bejelentkezést és automatikus üzembe helyezést.  

További információkért lásd:

* [Egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazáskatalógusában nem szereplő alkalmazásokhoz](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](../manage-apps/use-scim-to-provision-users-and-groups.md)

- - -
**K: Hogyan jelentkeznek felhasználók alkalmazások Azure AD-vel?**

**VÁLASZ:** Az Azure AD több módot nyújt a felhasználók megtekinthessék és elérhessék az alkalmazásaikat, például:

* Azure AD Hozzáférési panel
* Office 365 alkalmazásindító
* Közvetlen bejelentkezés az összevont alkalmazásokba
* Mélyhivatkozások az összevont, jelszóalapú vagy meglévő alkalmazásokhoz

További információkért lásd: [alkalmazások felhasználói élményének](../manage-apps/end-user-experiences.md).

- - -
**K: Milyen Azure AD különböző módokon teszi lehetővé a hitelesítést és egyszeri bejelentkezés alkalmazásokhoz?**

**VÁLASZ:** Az Azure AD számos szabványos protokollt támogat a hitelesítéshez és engedélyezéshez, például ilyen a SAML 2.0, OpenID Connect, az OAuth 2.0 és WS-Federation. Az Azure AD a jelszótárolást és az automatikus bejelentkezési képességeket is támogatja olyan alkalmazásoknál, amelyek csak az űrlapalapú hitelesítést támogatják.  

További információkért lásd:

* [Hitelesítési forgatókönyvek az Azure AD-hez](../develop/authentication-scenarios.md)
* [Az Active Directory hitelesítési protokolljai](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Egyszeri bejelentkezés az Azure AD-alkalmazások](../manage-apps/what-is-single-sign-on.md)

- - -
**K: A helyszíni futtatok alkalmazásokat adhat hozzá?**

**VÁLASZ:** Az Azure AD-alkalmazásproxy egyszerű és biztonságos hozzáférést, Ön által választott helyszíni webalkalmazásokhoz biztosít. Ezeket az alkalmazásokat ugyanúgy érheti el, ahogyan a szolgáltatott szoftverek (SaaS) alkalmazásokat az Azure AD-ben. Nincs szükség VPN-re vagy a hálózati infrastruktúra módosítására.  

További információért lásd: [Helyszíni alkalmazások biztonságos távoli elérése](../manage-apps/application-proxy.md).

- - -
**K: Hogyan követelhetem meg a multi-factor authentication szolgáltatás egy adott alkalmazásokhoz hozzáféréssel rendelkező felhasználók számára?**

**VÁLASZ:** Az Azure AD feltételes hozzáférésével minden alkalmazáshoz egyedi hozzáférési házirendet rendelhet. A házirendjében mindig megkövetelheti a többtényezős hitelesítést, vagy csak akkor, amikor a felhasználók nem csatlakoznak a helyi hálózathoz.  

További információkért lásd: [Az Office 365 és az Azure Active Directoryhoz csatlakoztatott egyéb alkalmazások hozzáférésének biztonságossá tétele](../active-directory-conditional-access-azure-portal.md).

- - -
**K: Mi az automatizált felhasználókiépítése SaaS-alkalmazások?**

**VÁLASZ:** Az Azure AD használatával automatizálhatja a létrehozását, karbantartási és eltávolítását számos népszerű felhőalapú SaaS-alkalmazások felhasználói identitásokat.

További információ: [A felhasználókiépítés és -megszüntetés automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../manage-apps/user-provisioning.md).

- - -
**K:  Állítható be a biztonságos LDAP-kapcsolatot az Azure ad-vel?**

**VÁLASZ:**  Nem. Az Azure AD nem támogatja az LDAP protokollt. A secure LDAP azonban konfigurálhatja az Azure AD tartományi szolgáltatásokkal.
