---
title: Hozzáférést biztosít a B2B-felhasználók számára a helyszíni alkalmazásokhoz – Azure AD
description: Bemutatja, hogyan adhat hozzáférést a felhőbeli B2B-felhasználóknak a helyszíni alkalmazásokhoz az Azure AD B2B együttműködéssel.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 098f464b6af5f10866403e1cd1549d571d883ac1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272803"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>B2B-felhasználók nak az Azure AD-ben való hozzáférés megadása a helyszíni alkalmazásokhoz

Az Azure Active Directory (Azure AD) B2B együttműködési képességeit használó szervezetként a partnerszervezetek vendégfelhasználóit az Azure AD-be hívhatja meg, most már hozzáférést biztosíthat ezeknek a B2B-felhasználóknak a helyszíni alkalmazásokhoz. Ezek a helyszíni alkalmazások saml-alapú hitelesítést vagy integrált Windows-hitelesítést (IWA) használhatnak a Kerberos korlátozott delegálásával (KCD).

## <a name="access-to-saml-apps"></a>Hozzáférés az SAML-alkalmazásokhoz

Ha a helyszíni alkalmazás SAML-alapú hitelesítést használ, ezeket az alkalmazásokat egyszerűen elérhetővé teheti az Azure AD B2B együttműködési felhasználói számára az Azure Portalon keresztül.

Mindkét alábbi teendőt kell tennie:

- Integrálja az SAML alkalmazást a nem katalógus alkalmazássablon használatával, ahogy az az [Azure Active Directory alkalmazáskatalógusban nem szereplő alkalmazások egyszeri bejelentkezésének konfigurálása](../manage-apps/configure-single-sign-on-non-gallery-applications.md)című témakörben ismertet. Ügyeljen arra, hogy vegye figyelembe, hogy mit használ a **bejelentkezési URL-értékhez.**
-  Az Azure AD alkalmazásproxy használatával tegye közzé a helyszíni alkalmazást, és az **Azure Active Directory** hitelesítési forrásként konfigurálva. További információt az [Alkalmazások közzététele az Azure AD alkalmazásproxy használatával (Közzététel az Azure AD-alkalmazásproxy használatával) (Alkalmazások közzététele az Azure AD-alkalmazásproxy használatával) témakörben talál.](../manage-apps/application-proxy-publish-azure-portal.md) 

   A belső **URL-beállítás** konfigurálásakor használja a nem katalógus alkalmazássablonban megadott bejelentkezési URL-címet. Ily módon a felhasználók hozzáférhetnek az alkalmazáshoz a szervezet határain kívülről. Az alkalmazásproxy elvégzi az SAML egyszeri bejelentkezést a helyszíni alkalmazáshoz.
 
   ![A helyszíni alkalmazásbeállítások belső URL-címének és hitelesítésének megjelenítése](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Hozzáférés az IWA és KCD alkalmazásokhoz

Ahhoz, hogy a B2B-felhasználók számára hozzáférést biztosítson az integrált Windows-hitelesítéssel és a Kerberos korlátozott delegálással védett helyszíni alkalmazásokhoz, a következő összetevőkre van szükség:

- **Hitelesítés az Azure AD alkalmazásproxyn keresztül.** A B2B-felhasználóknak képesnek kell lenniük a helyszíni alkalmazás hitelesítésére. Ehhez közzé kell tennie a helyszíni alkalmazást az Azure AD alkalmazásproxyn keresztül. További információ: [Az alkalmazásproxy használatának első lépései, és telepítse az összekötőt](../manage-apps/application-proxy-enable.md) és [az Alkalmazások közzététele az Azure AD alkalmazásproxy használatával című témakört.](../manage-apps/application-proxy-publish-azure-portal.md)
- **Engedélyezés egy B2B felhasználói objektumon keresztül a helyszíni könyvtárban.** Az alkalmazásnak képesnek kell lennie a felhasználói hozzáférés ellenőrzésére, és hozzáférést kell biztosítania a megfelelő erőforrásokhoz. Az iWA és a KCD az engedélyezés befejezéséhez felhasználói objektumra van szükség a helyszíni Windows Server Active Directoryban. AHogy azt a [Hogyan egyszeri bejelentkezés a KCD-vel működik,](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works)az alkalmazásproxynak szüksége van erre a felhasználói objektumra, hogy megszemélyesítse a felhasználót, és Kerberos-jogkivonatot kapjon az alkalmazáshoz. 

   A B2B felhasználói forgatókönyvhöz két módszer áll rendelkezésre, amelyek segítségével létrehozhatja a vendég felhasználói objektumokat, amelyek a helyszíni címtárban történő engedélyezéshez szükségesek:

   - A Microsoft Identity Manager (MIM) és a MiM felügyeleti ügynök a Microsoft Graph számára. 
   - [PowerShell-parancsfájl](#create-b2b-guest-user-objects-through-a-script-preview). A parancsfájl használata egy könnyebb megoldás, amely nem igényel MIM.Using the script is a lightweight solution that does not require MIM. 

Az alábbi ábra magas szintű áttekintést nyújt arról, hogy az Azure AD alkalmazásproxy és a helyszíni címtárban lévő B2B felhasználói objektum létrehozása hogyan működik együtt, hogy a B2B-felhasználók számára hozzáférést biztosítson a helyszíni IWA és KCD-alkalmazásokhoz. A számozott lépések részletesen ismertetik az alábbi ábrán.

![Mim és B2B parancsfájlmegoldások diagramja](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  Egy partnerszervezet (a Fabrikam-bérlő) felhasználója meghívást kap a Contoso-bérlőhöz.
2.  Egy vendég felhasználói objektum jön létre a Contoso-bérlőben (például egy felhasználói objektum@contoso.onmicrosoft.comguest_fabrikam.com#EXT#.com#EXT# címmel).
3.  A Fabrikam vendég a Contoso-ból a MIM-en keresztül vagy a B2B PowerShell-parancsfájlon keresztül kerül importálásra.
4.  A Fabrikam vendégfelhasználói objektum (Guest#EXT#) egy ábrázolása vagy "lábnyoma" jön létre a helyszíni címtárban, Contoso.com, a MIM-en keresztül vagy a B2B PowerShell-parancsfájlon keresztül.
5.  A vendégfelhasználó hozzáfér a helyszíni alkalmazáshoz, app.contoso.com.
6.  A hitelesítési kérelem az alkalmazásproxyn keresztül engedélyezett, a Kerberos korlátozott delegálásának használatával. 
7.  Mivel a vendégfelhasználói objektum helyileg létezik, a hitelesítés sikeres.

### <a name="lifecycle-management-policies"></a>Életciklus-kezelési irányelvek

A helyszíni B2B felhasználói objektumokat az életciklus-kezelési házirendeken keresztül kezelheti. Példa:

- Beállíthatja a többtényezős hitelesítési (MFA) házirendek a vendég felhasználó, hogy az MFA használata során application proxy hitelesítés. További információ: [Conditional Access for B2B collaboration users](conditional-access.md).
- A felhőbeli B2B-felhasználón végrehajtott szponzorálások, hozzáférési felülvizsgálatok, fiókellenőrzések stb., amelyek a helyszíni felhasználókra vonatkoznak. Ha például a felhőbeli felhasználó törlődik az életciklus-kezelési szabályzatokon keresztül, a helyszíni felhasználó t is törli a MIM Sync vagy az Azure AD Connect szinkronizálása. További információ: [Vendéghozzáférés kezelése az Azure AD-hozzáférési felülvizsgálatokkal.](../governance/manage-guest-access-with-access-reviews.md)

### <a name="create-b2b-guest-user-objects-through-mim"></a>B2B vendégfelhasználói objektumok létrehozása a MIM-en keresztül

A MIM 2016 Service Pack 1 és a MIM felügyeleti ügynök a Microsoft Graph számára a vendégfelhasználói objektumok helyszíni címtárban történő létrehozásához való használatáról az [Azure AD vállalathoz (B2B) együttműködésa a Microsoft Identity Manager (MIM) 2016 SP1 alkalmazásproxyval](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario)című témakörben talál.

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>B2B vendégfelhasználói objektumok létrehozása parancsfájlon keresztül (előzetes verzió)

Van egy PowerShell-mintaparancsfájl, amely kiindulási pontként használhatja a vendég felhasználói objektumok at a helyszíni Active Directoryban.

A parancsfájlt és a Readme fájlt letöltheti a [letöltőközpontból.](https://www.microsoft.com/download/details.aspx?id=51495) Válassza ki a **Script és a Readme az Azure AD B2B-felhasználók on-prem.zip fájl lekérése.**

A parancsfájl használata előtt ellenőrizze a társított fontos fájl előfeltételeit és fontos szempontjait. Azt is, hogy a parancsfájl csak mintaként érhető el. A fejlesztőcsapatnak vagy egy partnernek testre kell szabnia és át kell tekintenie a parancsfájlt, mielőtt futtatja.

## <a name="license-considerations"></a>A licencekkel kapcsolatos szempontok

Győződjön meg arról, hogy a megfelelő ügyfél-hozzáférési licencekkel rendelkezik a helyszíni alkalmazásokat elérő külső vendégfelhasználók számára. További információt az [ügyféllicencek és kezelési licencek](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx)"Külső összekötők" című részében talál. A konkrét licencelési igényekkel kapcsolatban forduljon a Microsoft képviselőjéhez vagy helyi viszonteladójához.

## <a name="next-steps"></a>További lépések

- [Azure Active Directory B2B együttműködés hibrid szervezetek számára](hybrid-organizations.md)

- Az Azure AD Connect áttekintése a [helyszíni könyvtárak integrálása az Azure Active Directoryval című témakörben található.](../hybrid/whatis-hybrid-identity.md)

