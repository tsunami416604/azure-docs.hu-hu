---
title: Támogatás B2B az Azure AD férhetnek hozzá a helyszíni alkalmazások |} Microsoft Docs
description: Bemutatja, hogyan adhat felhőbeli B2B a felhasználók hozzáférést helyszíni alkalmazások az Azure AD B2B együttműködés.
services: active-directory
documentationcenter: ''
author: twooley
manager: mtillman
editor: ''
tags: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 04/20/2018
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 0eb567c8587b0eec367160facc86f163ef6b3c15
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Támogatás B2B az Azure AD férhetnek hozzá a helyszíni alkalmazások

Azure Active Directory (Azure AD) B2B együttműködés szolgáltatásait használja az Azure AD az erőforráspartner-szervezetek Vendég felhasználóknak szánt meghívó szervezetként mostantól megadhatja a B2B felhasználók helyszíni alkalmazásokhoz való hozzáférés. Ezek a helyszíni alkalmazások használhatják SAML-alapú hitelesítés vagy integrált Windows-hitelesítéssel (IWA) és a Kerberos által korlátozott delegálás (KCD).

## <a name="access-to-saml-apps"></a>SAML-alapú alkalmazásokhoz való hozzáférés

Ha a helyszíni alkalmazások SAML-alapú hitelesítést használ, egyszerűen tehet ezek az alkalmazások elérhetővé a Azure AD B2B együttműködés felhasználók számára az Azure portálon keresztül.

A következő két kell tennie:

- Integrálását az SAML-alkalmazás az alkalmazás nem galéria sablonnal, a [konfigurálása egyszeri bejelentkezés alkalmazásokhoz, amelyek nincsenek rajta az Azure Active Directory alkalmazáskatalógusában](active-directory-saas-custom-apps.md). Ügyeljen arra, hogy a használja a **bejelentkezési URL-cím** érték.
-  A helyszíni alkalmazás közzététele az Azure AD alkalmazásproxy segítségével **Azure Active Directory** a hitelesítési forrásaként beállított. Útmutatásért lásd: [alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](application-proxy-publish-azure-portal.md). 

   Amikor konfigurál a **belső URL-cím** beállítás módosításához használja a bejelentkezési URL-címet, az alkalmazás nem galéria-sablonban megadott. Ezzel a módszerrel a felhasználók elérhetik az alkalmazást a szervezeten kívüli. Alkalmazásproxy a helyszíni alkalmazások a SAML-alapú egyszeri bejelentkezést hajt végre.
 
   ![A helyi beállításokat belső URL-címet és a hitelesítési jeleníti meg](media/active-directory-b2b-hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Integrált Windows-Hitelesítést és Kerberos által korlátozott Delegálás alkalmazásokhoz való hozzáférés

B2B felhasználók hozzáférést biztosítson a helyszíni alkalmazások védett integrált Windows-hitelesítés és Kerberos által korlátozott delegálás, a következő összetevők szükségesek:

- **Az Azure AD-proxyn keresztül történő hitelesítési**. B2B felhasználók meg tudjanak a helyszíni alkalmazások felé történő hitelesítésre. Ehhez a helyszíni alkalmazások az Azure AD-alkalmazásproxy kell közzétennie. További információkért lásd: [az alkalmazásproxy első lépései, és az összekötő telepítéséhez](active-directory-application-proxy-enable.md) és [alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](application-proxy-publish-azure-portal.md).
- **A helyszíni címtárban B2B felhasználóobjektum keresztül engedélyezési**. Az alkalmazás felhasználói hozzáférési ellenőrzések elvégzéséhez, és adja meg a megfelelő erőforrásokhoz való hozzáférés képesnek kell lennie. Integrált Windows-Hitelesítést és Kerberos által korlátozott Delegálás igényelnek olyan felhasználói objektum, a helyszíni Windows Server Active Directory az engedélyezés befejezéséhez. A [hogyan egyszeri bejelentkezéshez a Kerberos által korlátozott Delegálás works](active-directory-application-proxy-sso-using-kcd.md#how-single-sign-on-with-kcd-works), alkalmazásproxy kell a felhasználói objektum megszemélyesíthet-e a felhasználót, és a Kerberos-jogkivonata az alkalmazásba. 

   A B2B felhasználói forgatókönyvhöz kétféleképpen érhető el, hogy a Vendég felhasználói objektumok, amelyek szükségesek a hitelesítéshez a helyi címtárban létrehozásához használhatja:

   - [A Microsoft Identity Manager (MIM) és a MIM management Agent ügynököt a Microsoft Graph](#create-b2b-guest-user-objects-through-mim-preview). Ehhez a megoldáshoz szükségesek, hogy rendelkezik-e az Azure AD Premium P1 előfizetéssel. 
   - [PowerShell parancsfájl](#create-b2b-guest-user-objects-through-a-script-preview). A parancsfájl használata több könnyű megoldást nem igénylő MIM vagy az Azure AD Premium. 

A következő ábra biztosít az Azure AD-alkalmazásproxy magas szintű áttekintését és a generáció a B2B felhasználói objektum, a helyszíni címtár B2B felhasználónak hozzáférést biztosít a helyszíni integrált Windows-Hitelesítést és Kerberos által korlátozott Delegálás alkalmazások segítségével. A számozott lépéseket ismertetjük részletesen az alábbiakban a diagram.

![A MIM és B2B parancsfájl megoldások ábrája](media/active-directory-b2b-hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  A fiókpartner-szervezet (a Fabrikam bérlő) egy felhasználót a Contoso bérlő felkérik.
2.  A Vendég felhasználói objektum a Contoso bérlő jön létre (például egy egyszerű guest_fabrikam.com#EXT# a user objektum@contoso.onmicrosoft.com).
3.  A Fabrikam Vendég Contoso vagy a B2B PowerShell parancsfájl keresztül a MIM-ről importálni lehet.
4.  Egy ábrázolását vagy a "erőforrásigényét" Fabrikam Vendég felhasználói objektum (Vendég EXT #.) a helyszíni címtár, a Contoso.com, vagy a B2B PowerShell-parancsfájlt az MIM szolgáltatással jön létre.
5.  A Vendég felhasználó hozzáfér a helyszíni alkalmazások, app.contoso.com.
6.  A hitelesítési kérelem-proxyn keresztül történő, a Kerberos által korlátozott delegálás használata engedélyezett. 
7.  A Vendég felhasználói objektum létezik helyileg, mert a hitelesítés sikeres.

### <a name="lifecycle-management-policies"></a>Életciklus-kezelési házirendek

A helyszíni B2B felhasználói objektumok életciklus-kezelési házirendek használatával kezelhetők. Példa:

- A Vendég felhasználó többtényezős hitelesítés (MFA) házirendek beállíthat, hogy a multi-factor Authentication alkalmazás Proxy hitelesítése során használt. További információkért lásd: [feltételes hozzáférés a B2B együttműködés felhasználók](active-directory-b2b-mfa-instructions.md).
- Bármely sponsorships, hozzáférési értékelést, a fiók ellenőrzéseket, az a felhasználó a helyi felhasználók alkalmaz a felhő B2B stb. Például a felhő felhasználói törlésekor az életciklus-kezelési házirendek segítségével a helyszíni felhasználói is törlődik a MIM Sync vagy az Azure AD Connect szinkronizálási szolgáltatás. További információkért lásd: [ellenőrzi, hogy vendég-hozzáférés kezelése az Azure AD hozzáférési a](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim-preview"></a>Hozzon létre B2B Vendég felhasználói objektumok keresztül MIM (előzetes verzió)

A Vendég felhasználói objektumok a helyszíni címtár létrehozása a MIM 2016 Service Pack 1 és a MIM management Agent ügynököt a Microsoft Graph használatával kapcsolatos információkért lásd: [az Azure AD-vállalatok (B2B) együttműködés a Microsoft Identity Az Azure alkalmazásproxy (nyilvános előzetes verzió) Manager (MIM) 2016 SP1](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Egy parancsfájl (előzetes verzió) segítségével a B2B Vendég felhasználói objektumok létrehozása

Nincs egy PowerShell parancsfájlt érhető el, hogy használhatja kiindulási pontként a Vendég felhasználói objektumok létrehozása a helyszíni Active Directoryban.

A parancsfájl és a Readme fájlt letöltheti a [letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=51495). Válassza ki a **parancsfájl és az információs fájl való lekérésére az prem.zip felhasználók Azure AD B2B** fájlt.

A parancsfájl használata előtt győződjön meg arról, hogy tekintse át az Előfeltételek és szempontokat a tartozó információs fájlban. Tisztában legyenek, hogy a parancsfájl csak egy minta elérhetővé tegyen. A fejlesztői csapat vagy egy partner testreszabása, és nézze át a parancsfájlt, mielőtt futtatná azt.

## <a name="license-considerations"></a>Licenc kapcsolatos szempontok

Győződjön meg arról, hogy rendelkezik-e megfelelő ügyféllicenc (CAL) azon külső Vendég számára, akik a helyszíni alkalmazások eléréséhez. További információkért lásd: a "Külső összekötők" szakaszában [ügyféllicencek és felügyeleti licencek](https://www.microsoft.com/en-us/licensing/product-licensing/client-access-license.aspx). Tekintse át a Microsoft képviselőjével vagy a helyi viszonteladóhoz vonatkozó licencelési igény szerinti.

## <a name="next-steps"></a>További lépések

- [Az Azure Active Directory B2B együttműködés hibrid szervezeteknek](active-directory-b2b-hybrid-organizations.md)
- [Helyileg kezelt partner fiókok hozzáférést biztosít felhőbeli erőforrásokat használó Azure AD B2B együttműködés](active-directory-b2b-hybrid-on-premises-to-cloud.md)
- Az Azure AD Connect áttekintéséért lásd: [integrálása a helyszíni címtárakat az Azure Active Directoryval](connect/active-directory-aadconnect.md).

