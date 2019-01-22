---
title: A a helyszíni alkalmazásokhoz való hozzáférést az Azure ad-ben engedélyezési B2B-felhasználók |} A Microsoft Docs
description: Felhőbeli B2B-felhasználók hozzáférésének biztosítása a helyszíni alkalmazásokba az Azure AD B2B együttműködés mutatja.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: 25decf303023e5a71faa987caf6311f6a6f52b22
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432472"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Támogatás B2B-felhasználók Azure AD-ben a hozzáférést a helyszíni alkalmazások

Azure Active Directory (Azure AD) B2B együttműködési lehetőségeket az Azure ad a fiókpartner-szervezetek vendégfelhasználók használó szervezetek, most már megadhat a B2B-felhasználók a helyszíni alkalmazásokhoz való hozzáférés. Ezek a helyszíni alkalmazások az SAML-alapú hitelesítés vagy az integrált Windows-hitelesítés (IWA) a Kerberos által korlátozott delegálás (KCD).

## <a name="access-to-saml-apps"></a>SAML-alkalmazásokhoz való hozzáférés

Ha a helyszíni alkalmazás SAML-alapú hitelesítést használ, akkor is használhatja egyszerűen ezeket az alkalmazásokat a az Azure AD B2B együttműködés felhasználók számára az Azure Portalon keresztül.

A következő két kell tennie:

- Az SAML-alkalmazásba integrálja a katalógusban nem szereplő alkalmazást sablon használatával, leírtak szerint [konfigurálása egyszeri bejelentkezéshez, az alkalmazásokat, amelyek nem szerepelnek az Azure Active Directory alkalmazáskatalógusában](../manage-apps/configure-single-sign-on-non-gallery-applications.md). Mindenképpen jegyezze fel, a használatért a **bejelentkezési URL-** értéket.
-  A helyszíni alkalmazás közzététele az Azure AD-alkalmazásproxy használatával **Azure Active Directory** a hitelesítési forrásaként beállított. Útmutatásért lásd: [alkalmazások közzététele az Azure AD-alkalmazásproxy](../manage-apps/application-proxy-publish-azure-portal.md). 

   Konfigurálása során a **belső URL-cím** beállítást, a bejelentkezési URL-cím használata a katalógusban nem szereplő alkalmazás sablonban megadott. Ezzel a módszerrel a felhasználók elérhetik az alkalmazást a szervezet határán kívüli. Az alkalmazásproxy a helyszíni alkalmazás a SAML egyszeri bejelentkezést hajt végre.
 
   ![A helyszíni alkalmazás beállítások belső URL-CÍMÉT és a hitelesítési jeleníti meg](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>IWA és KCD alkalmazásokhoz való hozzáférés

B2B-felhasználók hozzáférést biztosítson a helyszíni alkalmazások védett integrált Windows-hitelesítés és Kerberos által korlátozott delegálás, a következő összetevők szükségesek:

- **Hitelesítés az Azure AD-alkalmazásproxyn keresztül**. B2B-felhasználók a helyszíni alkalmazás hitelesítéséhez képesnek kell lennie. Ehhez a helyszíni alkalmazást az Azure AD alkalmazásproxyn keresztül kell közzétennie. További információkért lásd: [alkalmazásproxy – első lépések, és telepítse az összekötőt](../manage-apps/application-proxy-enable.md) és [alkalmazások közzététele az Azure AD-alkalmazásproxy](../manage-apps/application-proxy-publish-azure-portal.md).
- **Engedélyezés a következővel a B2B-felhasználói objektum a helyszíni címtárban**. Az alkalmazás felhasználói hozzáférés-ellenőrzést, és a megfelelő erőforrásokhoz való hozzáférést kell lennie. IWA és kcd Szolgáltatáshoz szükséges egy felhasználói objektumot a helyi Windows Server Active Directory az engedélyezés végrehajtásához. Leírtak szerint [az egyszeri bejelentkezés KCD Works](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works), alkalmazásproxy van szüksége a felhasználó megszemélyesítése és az alkalmazás egy Kerberos-token beszerzése a felhasználói objektum. 

   A B2B-felhasználó forgatókönyvhöz két módszerrel érhető el, hogy a Vendég felhasználói objektumok, amelyek szükségesek a hitelesítéshez a helyszíni címtárban létrehozásához használhatja:

   - A Microsoft Identity Manager (MIM) és a [MIM-kezelőügynök a Microsoft Graph](#create-b2b-guest-user-objects-through-mim-preview). 
   - [Egy PowerShell-parancsprogram](#create-b2b-guest-user-objects-through-a-script-preview). A szkript használatával a MIM nem igénylő egyszerűbb megoldás. 

A következő ábra biztosít magas szintű áttekintését az Azure AD-alkalmazásproxy és a B2B-felhasználói objektum, a helyszíni generációja directory munkahelyi B2B-felhasználók a helyszíni IWA és KCD alkalmazásokhoz való hozzáférés biztosítása érdekében. A diagramot részletesebben olvashat a számozott lépéseit ismerteti.

![A MIM és a B2B parancsfájl megoldások diagramja](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  A fiókpartner-szervezet (a Fabrikam bérlő) egy felhasználó a Contoso bérlő a cégbe.
2.  A Contoso bérlő jön létre a Vendég felhasználói objektum (például egy egyszerű Felhasználóneve guest_fabrikam.com#EXT# a felhasználói objektum@contoso.onmicrosoft.com).
3.  A Fabrikam Vendég rendszer importálja a Contoso MIM vagy a B2B-PowerShell-parancsfájl segítségével.
4.  Egy ábrázolás vagy az "erőforrás-igényű" Fabrikam Vendég felhasználói objektum (Vendég EXT #.) a helyszíni címtárban, a Contoso.com, a MIM vagy a B2B-PowerShell-parancsfájl segítségével jön létre.
5.  A Vendég felhasználó hozzáfér a helyszíni alkalmazás, app.contoso.com.
6.  A hitelesítés engedélyezve van a Kerberos által korlátozott delegálással, alkalmazásproxyn keresztül. 
7.  A Vendég felhasználói objektum létezik helyileg, mert a hitelesítés sikeres.

### <a name="lifecycle-management-policies"></a>Életciklus-kezelési házirendek

A helyszíni B2B felhasználói objektumok életciklus-kezelési házirendek használatával kezelhetők. Példa:

- Beállíthatja a vendégfelhasználót a többtényezős hitelesítés (MFA) szabályzatokat, hogy a többtényezős hitelesítés a Application Proxy hitelesítése során használatos. További információkért lásd: [feltételes hozzáférés B2B-együttműködés felhasználók](conditional-access.md).
- Bármely sponsorship, a hozzáférési felülvizsgálatok, a fiók számán és az egyéb végzett felhasználói vonatkozik, a helyszíni felhasználók felhőbeli B2B. Például az életciklus-kezelési házirendek segítségével a felhőbeli felhasználó törlésekor, helyi felhasználó is törlődik az MIM Sync vagy az Azure AD Connect sync. További információkért lásd: [vendég-hozzáférés kezelése az Azure AD hozzáférési felülvizsgálatok](../governance/manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim"></a>B2B Vendég felhasználói objektumok a MIM keresztül létrehozása

A Vendég felhasználói objektumok létrehozása a helyszíni címtárban a MIM 2016 Service Pack 1 és a MIM-kezelőügynök a Microsoft Graph használatával kapcsolatos információkért lásd: [az Azure AD vállalatközi (B2B) együttműködés a Microsoft Identity Manager (MIM) 2016 SP1-et az Azure-alkalmazásproxyval](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>B2B-vendégként felhasználói objektumok egy parancsfájl (előzetes verzió) segítségével létrehozása

Nincs elérhető, hogy használhatja kiindulási pontként a Vendég felhasználói objektumok létrehozása a helyszíni Active Directory PowerShell példaszkript.

Letöltheti a parancsfájl, és a Readme fájlt a [letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=51495). Válassza ki a **parancsfájlt, és a Readme pull az prem.zip felhasználók Azure AD B2B** fájlt.

Mielőtt a parancsfájlt használja, győződjön meg arról, tekintse át az előfeltételeket és a legfontosabb szempontok a tartozó fontos fájlt. Ezenkívül megismerheti, hogy a parancsfájl lehetőségként elérhetővé tegyen csak egy minta. A fejlesztői csapat vagy egy partner testre szabhatja, és nézze át a parancsfájlt, mielőtt futtatná azt.

## <a name="license-considerations"></a>Licenccel kapcsolatos szempontok

Győződjön meg arról, hogy a megfelelő ügyféllicenc (CAL-EK) a helyszíni alkalmazásokhoz hozzáféréssel rendelkező külső vendégfelhasználók számára. További információkért tekintse meg a "Külső összekötők" szakaszában [ügyféllicencek és felügyeleti licencek](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx). Tekintse meg a Microsoft helyi képviselőjéhez vagy a helyi viszonteladóhoz vonatkozó licencelési igényeinek.

## <a name="next-steps"></a>További lépések

- [Az Azure Active Directory B2B együttműködés hibrid cégekhez és vállalkozásokhoz](hybrid-organizations.md)

- Az Azure AD Connect áttekintése, lásd: [a helyszíni címtárak integrálása az Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

