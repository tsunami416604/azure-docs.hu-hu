---
title: Az Azure Active Directory – gyakori kérdések |} A Microsoft Docs
description: Ismerje meg a gyakori kérdések és válaszok az Azure és az Azure Active Directory, jelszókezelés és alkalmazás-hozzáférés.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: lizross
ms.openlocfilehash: 5ff9d1cf76188ff38f59e8b74d4bfd380c8c88e1
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408710"
---
# <a name="azure-active-directory-faq"></a>Azure Active Directory – gyakori kérdések
Az Azure Active Directory (Azure AD) egy átfogó szolgáltatott identitási (IDaaS) megoldás, amely az identitások, a hozzáférés-kezelés és a biztonság minden szempontját lefedi.

További információkért lásd: [Mi az az Azure Active Directory?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Az Azure és az Azure Active Directory elérése
**K: Miért kapok „Nem találhatók előfizetések” üzenetet, amikor megpróbálom elérni az Azure AD-t az Azure Portalon?**

**V:** Az Azure Portal eléréséhez mindegyik felhasználónak engedélyekre van szüksége egy Azure-előfizetés keretében. Ha fizetős Office 365- vagy Azure AD előfizetéssel rendelkezik, látogasson el a [https://aka.ms/accessAAD](https://aka.ms/accessAAD) címre egy egyszeri aktiváláshoz. Egyébként aktiválnia kell egy ingyenes [Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/) vagy egy fizetős előfizetést.

További információkért lásd:

* [How Azure subscriptions are associated with Active Directory? (Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directory-hoz?)](active-directory-how-subscriptions-associated-directory.md)

- - -
**K: Milyen kapcsolat áll fenn az Azure AD, az Office 365 és az Azure között?**

**V:** Az Azure AD általános identitás- és hozzáférési képességeket nyújt az összes webes szolgáltatáshoz. Akár az Office 365, a Microsoft Azure, az Intune vagy más rendszert használ, már használja az Azure AD-t a bejelentkezés és a hozzáférés-kezelés bekapcsolásához ezen szolgáltatások mindegyikéhez.

A webes szolgáltatások használatához beállított összes felhasználó felhasználói fiókként van meghatározva egy vagy több Azure AD-példányban. Beállíthatja ezeket a fiókokat az ingyenes Azure AD-képességekhez, például a felhőalkalmazások eléréséhez.

A fizetős Azure AD szolgáltatások, például az Enterprise Mobility + Security, átfogó vállalati méretű felügyeleti és biztonsági megoldásokkal egészítenek ki más webes szolgáltatásokat, például az Office 365-öt és a Microsoft Azure-t.

- - -

**K: Mi a címtárrendszergazda között tulajdonos és a globális rendszergazda?**

**V:** alapértelmezés szerint a személy, aki Azure-előfizetésre regisztrál az Azure-erőforrások tulajdonosi szerepkör van rendelve. Egy olyan tulajdonost Microsoft-fiók vagy a címtárból, amelyhez az Azure-előfizetés társítva van egy munkahelyi vagy iskolai fiókot használhat.  Ez a szerepkör jogosult szolgáltatások kezelésére az Azure Portalon.

Ha másoknak is kell bejelentkezniük és elérniük a szolgáltatásokat az ugyanahhoz az előfizetéshez, hozzárendelheti azokat a megfelelő [beépített szerepkör](../../role-based-access-control/built-in-roles.md). További információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../../role-based-access-control/role-assignments-portal.md).

Alapértelmezés szerint az Azure-előfizetésre feliratkozó személy van rendelve a címtár globális rendszergazdai szerepkörrel. A globális rendszergazda az összes Azure AD-címtár funkciók hozzáféréssel rendelkezik. Az Azure AD különböző rendszergazdai szerepköröket a címtárral és az identitással kapcsolatos funkciók kezeléséhez rendelkezik. Ezek a rendszergazdák különböző funkciókhoz is hozzáférést kap az Azure Portalon. A rendszergazda szerepkör meghatározza, hogy mit tehet, például a létrehozása vagy szerkesztése a felhasználók, rendszergazdai szerepköröket hozzárendelni más, felhasználók új jelszavainak létrehozására, felhasználói licencek kezelése vagy tartományok kezelése.  További információ az Azure AD címtárrendszergazdáival és azok szerepköreivel: [felhasználó hozzárendelése az Azure Active Directory rendszergazdai szerepkörök](active-directory-users-assign-role-azure-portal.md) és [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

Ezenkívül a fizetős Azure AD szolgáltatások, például az Enterprise Mobility + Security, átfogó vállalati méretű felügyeleti és biztonsági megoldásokkal egészítenek ki más webes szolgáltatásokat, például az Office 365-öt és a Microsoft Azure-t.

- - -
**K: Létezik olyan jelentés, amely megmutatja, hogy mikor járnak le az Azure AD-beli felhasználói licenceim?**

**V.:** Nem.  Ez jelenleg nem érhető el.

- - -

## <a name="get-started-with-hybrid-azure-ad"></a>Első lépések a Hybrid Azure AD-ben


**K: Hogyan hagyhatok el egy bérlőt, amikor közreműködőként vagyok hozzáadva?**

**V:** Amikor más szervezet bérlőjéhez van hozzáadva közreműködőként, a jobb felső sarokban lévő „bérlőváltóval” válthat a bérlők között.  Jelenleg nem hagyhatja el a meghívó szervezetet, és a Microsoft dolgozik ezen a funkción.  A funkció eléréséig megkérheti a meghívó szervezetet, hogy távolítsa el Önt a bérlők közül.
- - -
**K: Hogyan csatlakoztathatom a helyszíni címtáramat az Azure AD-hez?**

**V:** A helyszíni címtárát az Azure AD Connect használatával csatlakoztathatja az Azure AD-hez.

További információkért lásd: [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md).

- - -
**K: Hogyan állíthatok be egyszeri bejelentkezést a helyszíni címtár és a felhőalkalmazásaim között?**

**V:** Csak a helyszíni címtár és az Azure AD között kell beállítania egyszeri bejelentkezést (SSO-t). Amíg az Azure AD-n keresztül éri el a felhőalkalmazásokat, a szolgáltatás automatikusan átirányítja a felhasználókat, hogy megfelelően hitelesíteni tudják magukat a helyszíni hitelesítő adataikkal.

Az egyszeri bejelentkezés helyszínről végzett implementálása könnyen elérhető olyan összevonási megoldásokkal, mint az Active Directory összevonási szolgáltatások (AD FS), illetve a jelszókivonat-szinkronizálás konfigurálásával. Könnyedén telepítheti mindkét lehetőséget az Azure AD Connect konfigurációs varázslójával.

További információkért lásd: [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md).

- - -
**K: Az Azure AD-ben elérhető önkiszolgáló portál a szervezetemben lévő felhasználók számára?**

**V:** Igen, az Azure AD az [Azure AD Hozzáférési panelén](http://myapps.microsoft.com) keresztül biztosítja a felhasználóknak az önkiszolgáló felületet és az alkalmazások elérését. Ha Ön Office 365-felhasználó, észreveheti, hogy számos képesség megegyezik az Office 365 portál képességeivel.

További információkért lásd: [Bevezetés a Hozzáférési panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

- - -
**K: Az Azure AD segít a helyszíni infrastruktúrám kezelésében?**

**V:** Igen. Az Azure AD Premium Edition tartalmazza az Azure AD Connect Health eszközt. Az Azure AD Connect Health segít megfigyelni a helyszíni identitás-infrastruktúrát és a szinkronizálási szolgáltatásokat, és további betekintést nyújt a működésükbe.  

További információkért lásd: [A helyszíni identitás-infrastruktúra és a szinkronizálási szolgáltatások megfigyelése a felhőben](../hybrid/whatis-hybrid-identity-health.md).  

- - -
## <a name="password-management"></a>Jelszókezelés
**K: Használhatom az Azure AD jelszóvisszaírást jelszó-szinkronizálás nélkül? (Ebben az esetben használhatom az Azure AD önkiszolgáló jelszó-visszaállítást (SSP) jelszóvisszaírással úgy, hogy nem a felhőben tárolom a jelszavaimat?)**

**V:** Nem kell szinkronizálnia az Active Directory-jelszavakat az Azure AD-vel a visszaírás engedélyezéséhez. Összevont környezetben az Azure AD egyszeri bejelentkezés (SSO) a helyszíni címtárra támaszkodik a felhasználó hitelesítéséhez. Ehhez a forgatókönyvhöz nem kell nyomon követni a helyszíni jelszót az Azure AD-ben.

- - -
**K: Mennyi ideig tart a jelszavak visszaírása az Active Directoryban a helyszínen?**

**V:** A jelszavak visszaírása valós időben történik.

További részletekért lásd: [A jelszókezelés első lépései](../authentication/quickstart-sspr.md).

- - -
**K: Használhatok jelszóvisszaírást rendszergazda által kezelt jelszavakhoz?**

**V:** Igen, ha engedélyezett a jelszóvisszaírás, a rendszergazda által végzett jelszóműveleteket a rendszer visszaírja a helyszíni környezetbe.  

A jelszavakkal kapcsolatos kérdésekre adott további válaszokért lásd: [Jelszókezelés – gyakori kérdések](../authentication/active-directory-passwords-faq.md).
- - -
**K: Nem emlékszem a meglévő Office 365-/Azure AD-jelszavamra, és meg szeretném változtatni. Mit tegyek?**

**V:** Ilyen helyzetben több lehetőség közül választhat.  Használjon önkiszolgáló jelszó-visszaállítást (SSPR), ha elérhető.  Az SSPR konfigurációjától függ, hogy működik-e.  További információkért lásd: [A jelszó-visszaállítási portál működése](../authentication/howto-sspr-deployment.md).

Office 365-felhasználók esetében a rendszergazda alaphelyzetbe állíthatja a jelszót a [Felhasználói jelszavak átállítása](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US) szakaszban ismertetett lépések segítségével.

Azure AD-felhasználók esetében a rendszergazdák a következő módszerek valamelyikével állíthatják alaphelyzetbe a jelszót:

- [Fiókok alaphelyzetbe állítása az Azure Portalon](active-directory-users-reset-password-azure-portal.md)
- [A PowerShell használata](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


- - -
## <a name="security"></a>Biztonság
**K: Zárolja a rendszer a fiókokat egy adott számú hibás kísérlet után, vagy ennél kifinomultabb stratégiát alkalmaz?**

A fiókok zárolásához ennél kifinomultabb stratégiát alkalmazunk.  A zárolás a kérés IP-címe és a megadott jelszavak alapján történik. A zárolás időtartama annál hosszabb, minél inkább valószínűsíthető, hogy támadásról van szó.  

**K: Egyes (gyakori) jelszavakat a rendszer „ezt a jelszót már túl sokszor használták” üzenettel dob vissza. Igaz ez az aktuális Active Directory-környezetben használt jelszavakra is?**

Ez a visszautasítás a világszerte gyakran használt jelszavakra, például a „Jelszó” (Password) és a „123456” karakterláncok különböző változataira vonatkozik.

**K: Blokkolja a rendszer a kétes forrásokból (botnetek, TOR-végpontok) érkező bejelentkezési kéréseket a B2C-bérlőkön, vagy ehhez alap- vagy prémium szintű bérlőre van szükség?**

Az átjárónk szűri a kéréseket és bizonyos fokú védelmet biztosít a botnetek ellen. Ez minden B2C-bérlőre vonatkozik.

## <a name="application-access"></a>Alkalmazás-hozzáférés

**K: Hol találom az Azure Ad-vel előre integrált alkalmazások és azok képességeinek listáját?**

**V:** Az Azure AD a Microsoft vállalat, az alkalmazásszolgáltatók és a partnerek több mint 2600 előre integrált alkalmazásával rendelkezik. Mindegyik előre integrált alkalmazás támogatja az egyszeri bejelentkezést (SSO). Az egyszeri bejelentkezéssel a szervezeti hitelesítő adatokkal érheti el az alkalmazásokat. Néhány alkalmazás az automatikus üzembe helyezést és megszüntetést is támogatja.

Az előre integrált alkalmazások teljes listájáért lásd: [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

- - -
**K: Mit tegyek, ha a kívánt alkalmazás nem szerepel az Azure AD Marketplace-en?**

**V:** Az Azure AD Premiumban bármely alkalmazást felveheti és konfigurálhatja. Az alkalmazás képességeitől és a beállításaitól függően konfigurálhat egyszeri bejelentkezést és automatikus üzembe helyezést.  

További információkért lásd:

* [Egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazáskatalógusában nem szereplő alkalmazásokhoz](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](../manage-apps/use-scim-to-provision-users-and-groups.md)

- - -
**K: Hogyan jelentkeznek be a felhasználók az alkalmazásokba az Azure AD-vel?**

**V:** Az Azure AD több módot nyújt arra, hogy a felhasználók megtekinthessék és elérhessék az alkalmazásaikat, például a következőket:

* Azure AD Hozzáférési panel
* Office 365 alkalmazásindító
* Közvetlen bejelentkezés az összevont alkalmazásokba
* Mélyhivatkozások az összevont, jelszóalapú vagy meglévő alkalmazásokhoz

További információk: [Azure AD integrált alkalmazások üzembe helyezése a felhasználók számára](../manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users).

- - -
**K: Az Azure AD milyen különböző módokon teszi lehetővé a hitelesítést és az egyszeri bejelentkezést az alkalmazásokba?**

**V:** Az Azure AD számos szabványos protokollt támogat a hitelesítéshez és az engedélyezéshez, például ilyen a SAML 2.0, az OpenID Connect, az OAuth 2.0 és a WS-Federation. Az Azure AD a jelszótárolást és az automatikus bejelentkezési képességeket is támogatja olyan alkalmazásoknál, amelyek csak az űrlapalapú hitelesítést támogatják.  

További információkért lásd:

* [Hitelesítési forgatókönyvek az Azure AD-hez](../develop/authentication-scenarios.md)
* [Az Active Directory hitelesítési protokolljai](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Hogyan működik az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work)

- - -
**K: Felvehetek helyszínen futtatott alkalmazásokat?**

**V:** Az Azure AD-alkalmazásproxy egyszerű és biztonságos hozzáférést nyújt az Ön által választott helyszíni webalkalmazásokhoz. Ezeket az alkalmazásokat ugyanúgy érheti el, ahogyan a szolgáltatott szoftverek (SaaS) alkalmazásokat az Azure AD-ben. Nincs szükség VPN-re vagy a hálózati infrastruktúra módosítására.  

További információért lásd: [Helyszíni alkalmazások biztonságos távoli elérése](../manage-apps/application-proxy.md).

- - -
**K: Hogyan követelhetem meg a többtényezős hitelesítést az adott alkalmazásokhoz hozzáféréssel rendelkező felhasználóknál?**

**V:** Az Azure AD feltételes hozzáférésével minden alkalmazáshoz egyedi hozzáférési házirendet rendelhet. A házirendjében mindig megkövetelheti a többtényezős hitelesítést, vagy csak akkor, amikor a felhasználók nem csatlakoznak a helyi hálózathoz.  

További információkért lásd: [Az Office 365 és az Azure Active Directoryhoz csatlakoztatott egyéb alkalmazások hozzáférésének biztonságossá tétele](../active-directory-conditional-access-azure-portal.md).

- - -
**K: Mi a SaaS-alkalmazások automatizált felhasználókiépítése?**

**V:** Az Azure AD-vel automatizálhatja a felhasználói identitások létrehozását, karbantartását és eltávolítását számos népszerű felhőalapú SaaS alkalmazásban.

További információ: [A felhasználókiépítés és -megszüntetés automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../manage-apps/user-provisioning.md).

- - -
**K: Állíthatok be biztonságos LDAP-kapcsolatot az Azure AD-vel?**

**V.:** Nem. Az Azure AD nem támogatja az LDAP protokollt. A secure LDAP azonban konfigurálhatja az Azure AD tartományi szolgáltatásokkal.
