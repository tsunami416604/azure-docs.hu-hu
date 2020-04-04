---
title: Alkalmazásvédelmi szabályzatok feltételes hozzáféréssel – Azure Active Directory
description: Megtudhatja, hogyan követelheti meg az alkalmazásvédelmi szabályzatot a felhőalapú alkalmazások eléréséhez az Azure Active Directory feltételes hozzáféréssel.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b282962cc713487b8ee5113b02b8533a1538fff
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631894"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Útmutató: Alkalmazásvédelmi szabályzat és jóváhagyott ügyfélalkalmazás megkövetelése a felhőalapú alkalmazás-hozzáféréshez feltételes hozzáféréssel

Az emberek rendszeresen használják mobileszközeiket mind személyes, mind munkahelyi feladatokra. Miközben gondoskodik arról, hogy a munkatársak hatékonyan dolgozhassanak, a szervezetek meg akarják akadályozni, hogy az adatvesztés potenciálisan nem biztonságos alkalmazásokból származik. Feltételes hozzáférés sel a szervezetek korlátozhatják a hozzáférést a jóváhagyott (modern hitelesítésre képes) ügyfélalkalmazásokhoz az Intune alkalmazásvédelmi szabályzataival.

Ez a cikk két forgatókönyvet mutat be az olyan erőforrások feltételes hozzáférési házirendjeinek konfigurálásához, mint az Office 365, az Exchange Online és a SharePoint Online.

- [1. eset: Az Office 365-alkalmazásokhoz alkalmazásvédelmi szabályzattal rendelkező jóváhagyott alkalmazásokra van szükség](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [2. eset: Az Exchange Online és a SharePoint Online jóváhagyott ügyfélalkalmazás- és alkalmazásvédelmi szabályzatot igényel](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

A feltételes hozzáférésben ezek az ügyfélalkalmazások ismert, hogy egy alkalmazásvédelmi szabályzattal védett. Az alkalmazásvédelmi szabályzatokról további információt a cikkben talál, [az Alkalmazásvédelmi szabályzatok áttekintése című témakörben.](/intune/apps/app-protection-policy)

A jogosult ügyfélalkalmazások listáját az [Alkalmazásvédelmi szabályzat követelménye](concept-conditional-access-grant.md)című témakörben található.

> [!NOTE]
>    A vagy záradék a szabályzatban használatos, hogy a felhasználók olyan alkalmazásokat használhassanak, amelyek támogatják az **alkalmazásvédelmi szabályzat megkövetelése** vagy **a jóváhagyott ügyfélalkalmazás-támogatási** vezérlők et. További információ, amely alkalmazások támogatják az **alkalmazásvédelmi szabályzat** támogatásának ellenőrzése, olvassa el [az alkalmazásvédelmi szabályzat követelmény.](concept-conditional-access-grant.md)

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>1. eset: Az Office 365-alkalmazásokhoz alkalmazásvédelmi szabályzattal rendelkező jóváhagyott alkalmazásokra van szükség

Ebben az esetben a Contoso úgy döntött, hogy az Office 365-erőforrásokhoz való összes mobilhozzáférésnek jóváhagyott ügyfélalkalmazásokat kell használnia, például az Outlook mobile-t, a OneDrive-ot és a Microsoft Teamst, amelyeket egy alkalmazásvédelmi szabályzat véd a hozzáférés fogadása előtt. Az összes felhasználójuk már bejelentkezik az Azure AD hitelesítő adataival, és olyan licenccel rendelkeznek, amely tartalmazza az Azure AD Premium P1 vagy P2 és a Microsoft Intune-t.

A szervezeteknek végre kell hajtozniuk a következő lépéseket ahhoz, hogy egy jóváhagyott ügyfélalkalmazás mobileszközökön történő használatát igényeljék.

**1. lépés: Azure AD feltételes hozzáférési szabályzat konfigurálása az Office 365-höz**

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
1. A Grant **Hozzáférési vezérlőcsoportban** > **Grant**adja meg a következő beállításokat:
   - **Jóváhagyott ügyfélalkalmazás megkövetelése**
   - **Alkalmazásvédelmi szabályzat megkövetelése (előzetes verzió)**
   - **Az összes kijelölt vezérlő megkövetelése**
1. Erősítse meg a beállításokat, és állítsa a **Házirend engedélyezése** **be**beállítást.
1. A **Létrehozás gombra** a házirend létrehozásához és engedélyezéséhez válassza a Létrehozás gombot.

**2. lépés: Azure AD feltételes hozzáférési szabályzat konfigurálása az ActiveSync (EAS) szolgáltatással rendelkező Exchange Online-hoz**

Ebben a lépésben a feltételes hozzáférési házirendhez konfigurálja a következő összetevőket:

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
1. A**Grant** **Access-vezérlők csoportban** > válassza a **Hozzáférés megadása**lehetőséget, válassza az **Alkalmazásvédelmi házirend megkövetelése**lehetőséget, és válassza **a Kijelölés lehetőséget.**
1. Erősítse meg a beállításokat, és állítsa a **Házirend engedélyezése** **be**beállítást.
1. A **Létrehozás gombra** a házirend létrehozásához és engedélyezéséhez válassza a Létrehozás gombot.

**3. lépés: Az Intune alkalmazásvédelmi házirendjének konfigurálása iOS és Android ügyfélalkalmazásokhoz**

Tekintse át az [Alkalmazásvédelmi szabályzatok létrehozása és hozzárendelése című cikket,](/intune/apps/app-protection-policies)amely az Android és iOS alkalmazásvédelmi szabályzatai létrehozásának lépéseit ismerteti. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>2. eset: Az Exchange Online és a SharePoint Online jóváhagyott ügyfélalkalmazás- és alkalmazásvédelmi szabályzatot igényel

Ebben az esetben a Contoso úgy döntött, hogy a felhasználók csak akkor férhetnek hozzá az e-mailekhez és a SharePoint-adatokhoz mobileszközökön, ha a hozzáférés hez való hozzáférés előtt egy jóváhagyott ügyfélalkalmazást, például az Outlook mobile-t egy alkalmazásvédelmi szabályzat védi. Az összes felhasználójuk már bejelentkezik az Azure AD hitelesítő adataival, és olyan licenccel rendelkeznek, amely tartalmazza az Azure AD Premium P1 vagy P2 és a Microsoft Intune-t.

A szervezeteknek végre kell hajtozniuk a következő három lépést ahhoz, hogy jóváhagyott ügyfélalkalmazást igényelhessenek mobileszközökön és Exchange ActiveSync-ügyfeleken.

**1. lépés: Szabályzat Android és iOS alapú modern hitelesítési ügyfelek számára, amelyek az Exchange Online és a SharePoint Online elérésekor jóváhagyott ügyfélalkalmazás- és alkalmazásvédelmi szabályzatot igényelnek.**

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
1. A Grant **Hozzáférési vezérlőcsoportban** > **Grant**adja meg a következő beállításokat:
   - **Jóváhagyott ügyfélalkalmazás megkövetelése**
   - **Alkalmazásvédelmi szabályzat megkövetelése (előzetes verzió)**
   - **A kijelölt vezérlők egyikének megkövetelése**
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
1. A**Grant** **Access-vezérlők csoportban** > válassza a **Hozzáférés megadása**lehetőséget, válassza az **Alkalmazásvédelmi házirend megkövetelése**lehetőséget, és válassza **a Kijelölés lehetőséget.**
1. Erősítse meg a beállításokat, és állítsa a **Házirend engedélyezése** **be**beállítást.
1. A **Létrehozás gombra** a házirend létrehozásához és engedélyezéséhez válassza a Létrehozás gombot.

**3. lépés: Az Intune alkalmazásvédelmi szabályzatának konfigurálása iOS- és Android-ügyfélalkalmazásokhoz.**

Tekintse át az [Alkalmazásvédelmi szabályzatok létrehozása és hozzárendelése című cikket,](/intune/apps/app-protection-policies)amely az Android és iOS alkalmazásvédelmi szabályzatai létrehozásának lépéseit ismerteti. 

## <a name="next-steps"></a>További lépések

[Mi az a feltételes hozzáférés?](overview.md)

[Feltételes hozzáférési összetevők](concept-conditional-access-policies.md)

[Gyakori feltételes hozzáférési szabályzatok](concept-conditional-access-policy-common.md)

