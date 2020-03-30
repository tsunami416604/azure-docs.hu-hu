---
title: Jóváhagyott ügyfélalkalmazások feltételes hozzáféréssel – Azure Active Directory
description: Megtudhatja, hogyan követelheti meg a jóváhagyott ügyfélalkalmazásokat a felhőalapú alkalmazások eléréséhez az Azure Active Directory feltételes hozzáféréssel.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a215e2bb7d9d1cf9013414037383590456296cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480895"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Útmutató: Jóváhagyott ügyfélalkalmazások megkövetelése a felhőalapú alkalmazások eléréséhez feltételes hozzáféréssel

Az emberek rendszeresen használják mobileszközeiket mind személyes, mind munkahelyi feladatokra. Miközben gondoskodik arról, hogy a munkatársak hatékonyan dolgozhassanak, a szervezetek meg akarják akadályozni, hogy az adatvesztés potenciálisan nem biztonságos alkalmazásokból származik. Feltételes hozzáférés esetén a szervezetek korlátozhatják a jóváhagyott (modern hitelesítésre képes) ügyfélalkalmazásokhoz való hozzáférést.

Ez a cikk két forgatókönyvet mutat be az olyan erőforrások feltételes hozzáférési házirendjeinek konfigurálásához, mint az Office 365, az Exchange Online és a SharePoint Online.

- [1. eset: Az Office 365-alkalmazásokhoz jóváhagyott ügyfélalkalmazás szükséges](#scenario-1-office-365-apps-require-an-approved-client-app)
- [2. eset: Az Exchange Online és a SharePoint Online jóváhagyott ügyfélalkalmazásra van szükség](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

A Feltételes hozzáférés ben ezt a funkciót jóváhagyott ügyfélalkalmazás nak nevezzük. A jóváhagyott ügyfélalkalmazások listáját a [Jóváhagyott ügyfélalkalmazás-követelmény](concept-conditional-access-grant.md#require-approved-client-app)tartalmazza.

> [!NOTE]
> Annak érdekében, hogy jóváhagyott kliensalkalmazásokat igényeljen iOS és Android-eszközökre, ezeknek az eszközöknek először regisztrálniuk kell az Azure AD-ben.

## <a name="scenario-1-office-365-apps-require-an-approved-client-app"></a>1. eset: Az Office 365-alkalmazásokhoz jóváhagyott ügyfélalkalmazás szükséges

Ebben az esetben a Contoso úgy döntött, hogy a mobileszközöket használó felhasználók hozzáférhetnek az összes Office 365-szolgáltatáshoz, feltéve, hogy jóváhagyott ügyfélalkalmazásokat használnak, például az Outlook mobile-t, a OneDrive-ot és a Microsoft Teamst. Az összes felhasználójuk már bejelentkezik az Azure AD hitelesítő adataival, és olyan licenccel rendelkeznek, amely tartalmazza az Azure AD Premium P1 vagy P2 és a Microsoft Intune-t.

A szervezeteknek végre kell hajtozniuk a következő három lépést ahhoz, hogy egy jóváhagyott ügyfélalkalmazás mobileszközökön történő használatát igényeljék.

**1. lépés: Szabályzat Android és iOS alapú modern hitelesítési ügyfelek használatát igénylő jóváhagyott ügyfélalkalmazás elérésekor Exchange Online.**

1. Jelentkezzen be az **Azure Portalon** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférés-rendszergazdaként.
1. Tallózással keresse meg az **Azure Active Directory** > **biztonsági** > **feltételes hozzáférését.**
1. Válassza az **Új házirend lehetőséget.**
1. Adjon nevet a szabályzatának. Azt javasoljuk, hogy a szervezetek hozzanak létre egy értelmes szabvány a házirendek nevét.
1. A **Hozzárendelések**csoportban válassza a **Felhasználók és csoportok lehetőséget.**
   1. A **Belefoglalás**csoportban válassza a **Minden felhasználó** vagy azadott felhasználók és **csoportok** lehetőséget, amelyekre alkalmazni szeretné ezt a házirendet. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Felhőalapú alkalmazások és műveletek:** > **Include csoportban**válassza az Office **365 (előzetes verzió) lehetőséget.**
1. A **Feltételek csoportban**válassza **az Eszközplatformok lehetőséget.**
   1. Állítsa **a Konfigurálás** beállítást **Igen**gombra.
   1. Tartalmazza **az Android** és **iOS**.
1. A **Feltételek csoportban**válassza **az Ügyfélalkalmazások (előzetes verzió)** lehetőséget.
   1. Állítsa **a Konfigurálás** beállítást **Igen**gombra.
   1. Válassza a **Mobilalkalmazások és asztali ügyfelek, valamint** **a Modern hitelesítési ügyfelek**lehetőséget.
1. A**Grant** **Access-vezérlők csoportban** > válassza a **Hozzáférési támogatás**lehetőséget, válassza a Jóváhagyott **ügyfélalkalmazás megkövetelése**lehetőséget, majd a **Kijelölés**lehetőséget.
1. Erősítse meg a beállításokat, és állítsa a **Házirend engedélyezése** **be**beállítást.
1. A **Létrehozás gombra** a házirend létrehozásához és engedélyezéséhez válassza a Létrehozás gombot.

**2. lépés: Azure AD feltételes hozzáférési szabályzat konfigurálása az ActiveSync (EAS) szolgáltatással rendelkező Exchange Online-hoz**

1. Tallózással keresse meg az **Azure Active Directory** > **biztonsági** > **feltételes hozzáférését.**
1. Válassza az **Új házirend lehetőséget.**
1. Adjon nevet a szabályzatának. Azt javasoljuk, hogy a szervezetek hozzanak létre egy értelmes szabvány a házirendek nevét.
1. A **Hozzárendelések**csoportban válassza a **Felhasználók és csoportok lehetőséget.**
   1. A **Belefoglalás**csoportban válassza a **Minden felhasználó** vagy azadott felhasználók és **csoportok** lehetőséget, amelyekre alkalmazni szeretné ezt a házirendet. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Felhőalapú alkalmazások és műveletek:** > **Include csoportban**válassza az Office **365 Exchange Online**lehetőséget.
1. **Feltételek mellett:**
   1. **Ügyfélalkalmazások (előzetes verzió):**
      1. Állítsa **a Konfigurálás** beállítást **Igen**gombra.
      1. Válassza a **Mobilalkalmazások és asztali ügyfelek, valamint** **az Exchange ActiveSync-ügyfelek lehetőséget.**
1. A**Grant** **Access-vezérlők csoportban** > válassza a **Hozzáférési támogatás**lehetőséget, válassza a Jóváhagyott **ügyfélalkalmazás megkövetelése**lehetőséget, majd a **Kijelölés**lehetőséget.
1. Erősítse meg a beállításokat, és állítsa a **Házirend engedélyezése** **be**beállítást.
1. A **Létrehozás gombra** a házirend létrehozásához és engedélyezéséhez válassza a Létrehozás gombot.

**3. lépés: Az Intune alkalmazásvédelmi szabályzatának konfigurálása iOS- és Android-ügyfélalkalmazásokhoz.**

Tekintse át az [Alkalmazásvédelmi szabályzatok létrehozása és hozzárendelése című cikket,](/intune/apps/app-protection-policies)amely az Android és iOS alkalmazásvédelmi szabályzatai létrehozásának lépéseit ismerteti. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>2. eset: Az Exchange Online és a SharePoint Online jóváhagyott ügyfélalkalmazásra van szükség

Ebben az esetben a Contoso úgy döntött, hogy a felhasználók csak akkor férhetnek hozzá az e-mailekhez és a SharePoint-adatokhoz mobileszközökön, ha egy jóváhagyott ügyfélalkalmazást, például az Outlook mobile-t használják. Az összes felhasználójuk már bejelentkezik az Azure AD hitelesítő adataival, és olyan licenccel rendelkeznek, amely tartalmazza az Azure AD Premium P1 vagy P2 és a Microsoft Intune-t.

A szervezeteknek végre kell hajtozniuk a következő három lépést ahhoz, hogy jóváhagyott ügyfélalkalmazást igényelhessenek mobileszközökön és Exchange ActiveSync-ügyfeleken.

**1. lépés: Szabályzat Android és iOS alapú modern hitelesítési ügyfelek használatát igénylő jóváhagyott ügyfélalkalmazás elérésekor Exchange Online és a SharePoint Online.**

1. Jelentkezzen be az **Azure Portalon** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférés-rendszergazdaként.
1. Tallózással keresse meg az **Azure Active Directory** > **biztonsági** > **feltételes hozzáférését.**
1. Válassza az **Új házirend lehetőséget.**
1. Adjon nevet a szabályzatának. Azt javasoljuk, hogy a szervezetek hozzanak létre egy értelmes szabvány a házirendek nevét.
1. A **Hozzárendelések**csoportban válassza a **Felhasználók és csoportok lehetőséget.**
   1. A **Belefoglalás**csoportban válassza a **Minden felhasználó** vagy azadott felhasználók és **csoportok** lehetőséget, amelyekre alkalmazni szeretné ezt a házirendet. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Felhőalapú alkalmazások és műveletek:** > **Include csoportban**válassza az Office **365 Exchange Online** és az Office **365 SharePoint Online lehetőséget.**
1. A **Feltételek csoportban**válassza **az Eszközplatformok lehetőséget.**
   1. Állítsa **a Konfigurálás** beállítást **Igen**gombra.
   1. Tartalmazza **az Android** és **iOS**.
1. A **Feltételek csoportban**válassza **az Ügyfélalkalmazások (előzetes verzió)** lehetőséget.
   1. Állítsa **a Konfigurálás** beállítást **Igen**gombra.
   1. Válassza a **Mobilalkalmazások és asztali ügyfelek, valamint** **a Modern hitelesítési ügyfelek**lehetőséget.
1. A**Grant** **Access-vezérlők csoportban** > válassza a **Hozzáférési támogatás**lehetőséget, válassza a Jóváhagyott **ügyfélalkalmazás megkövetelése**lehetőséget, majd a **Kijelölés**lehetőséget.
1. Erősítse meg a beállításokat, és állítsa a **Házirend engedélyezése** **be**beállítást.
1. A **Létrehozás gombra** a házirend létrehozásához és engedélyezéséhez válassza a Létrehozás gombot.

**2. lépés: Az Exchange ActiveSync-ügyfelek szabályzata, amely jóváhagyott ügyfélalkalmazás használatát igényli.**

1. Tallózással keresse meg az **Azure Active Directory** > **biztonsági** > **feltételes hozzáférését.**
1. Válassza az **Új házirend lehetőséget.**
1. Adjon nevet a szabályzatának. Azt javasoljuk, hogy a szervezetek hozzanak létre egy értelmes szabvány a házirendek nevét.
1. A **Hozzárendelések**csoportban válassza a **Felhasználók és csoportok lehetőséget.**
   1. A **Belefoglalás**csoportban válassza a **Minden felhasználó** vagy azadott felhasználók és **csoportok** lehetőséget, amelyekre alkalmazni szeretné ezt a házirendet. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Felhőalapú alkalmazások és műveletek:** > **Include csoportban**válassza az Office **365 Exchange Online**lehetőséget.
1. **Feltételek mellett:**
   1. **Ügyfélalkalmazások (előzetes verzió):**
      1. Állítsa **a Konfigurálás** beállítást **Igen**gombra.
      1. Válassza a **Mobilalkalmazások és asztali ügyfelek, valamint** **az Exchange ActiveSync-ügyfelek lehetőséget.**
1. A**Grant** **Access-vezérlők csoportban** > válassza a **Hozzáférési támogatás**lehetőséget, válassza a Jóváhagyott **ügyfélalkalmazás megkövetelése**lehetőséget, majd a **Kijelölés**lehetőséget.
1. Erősítse meg a beállításokat, és állítsa a **Házirend engedélyezése** **be**beállítást.
1. A **Létrehozás gombra** a házirend létrehozásához és engedélyezéséhez válassza a Létrehozás gombot.

**3. lépés: Az Intune alkalmazásvédelmi szabályzatának konfigurálása iOS- és Android-ügyfélalkalmazásokhoz.**

Tekintse át az [Alkalmazásvédelmi szabályzatok létrehozása és hozzárendelése című cikket,](/intune/apps/app-protection-policies)amely az Android és iOS alkalmazásvédelmi szabályzatai létrehozásának lépéseit ismerteti. 

## <a name="next-steps"></a>További lépések

[Mi a feltételes hozzáférés?](overview.md)

[Feltételes hozzáférési összetevők](concept-conditional-access-policies.md)

[Gyakori feltételes hozzáférési szabályzatok](concept-conditional-access-policy-common.md)
