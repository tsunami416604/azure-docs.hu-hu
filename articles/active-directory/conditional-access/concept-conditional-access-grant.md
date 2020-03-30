---
title: Vezérlők megadása a feltételes hozzáférési házirendben – Azure Active Directory
description: Mik azok a támogatási vezérlők egy Azure AD feltételes hozzáférési szabályzatban?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02ec8dace971cd4dc1407c9e8d20839504c9ecc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331841"
---
# <a name="conditional-access-grant"></a>Feltételes hozzáférés: Támogatás

A feltételes hozzáférési házirendeken belül a rendszergazda hozzáférés-vezérléssel biztosíthatja vagy letilthatja az erőforrásokhoz való hozzáférést.

![Feltételes hozzáférési házirend többtényezős hitelesítést igénylő támogatásvezérléssel](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Fájlhozzáférés

A Blokk figyelembe veszi a hozzárendeléseket, és megakadályozza a feltételes hozzáférés házirend-konfigurációján alapuló hozzáférést.

Blokk egy erős ellenőrzés, hogy kell forgatható a megfelelő ismeretekkel. Ez olyasmi, amit a rendszergazdáknak [a csak jelentés módban](concept-conditional-access-report-only.md) kell használniuk a teszteléshez, mielőtt engedélyeznék.

## <a name="grant-access"></a>Hozzáférés biztosítása

A rendszergazdák a hozzáférés megadásakor egy vagy több vezérlőt is kényszeríthetnek. Ezek a vezérlők a következő lehetőségeket tartalmazzák: 

- [Többtényezős hitelesítés megkövetelése (Azure többtényezős hitelesítés)](../authentication/concept-mfa-howitworks.md)
- [Az eszköz megfelelőként való megjelölésének megkövetelése (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Hibrid Azure AD-hez csatlakozott eszköz megkövetelése](../devices/concept-azure-ad-join-hybrid.md)
- [Jóváhagyott ügyfélalkalmazás megkövetelése](app-based-conditional-access.md)
- [Alkalmazásvédelmi szabályzat megkövetelése](app-protection-based-conditional-access.md)

Ha a rendszergazdák úgy döntenek, hogy egyesítik ezeket a beállításokat, a következő módszereket választhatják:

- Az összes kijelölt vezérlő **(vezérlőelem és** vezérlőelem) megkövetelése
- A kijelölt vezérlők **(vezérlőelem vagy** vezérlőelem) megkövetelése

Alapértelmezés szerint a feltételes hozzáféréshez az összes kijelölt vezérlőre szükség van.

### <a name="require-multi-factor-authentication"></a>Többtényezős hitelesítés megkövetelése

Ha bejelöli ezt a jelölőnégyzetet, a felhasználóknak el kell végezniük az Azure többtényezős hitelesítést. Az Azure többtényezős hitelesítésének üzembe helyezéséről a [Felhőalapú Azure többtényezős hitelesítés telepítésének megtervezése](../authentication/howto-mfa-getstarted.md)című cikkben olvashat bővebben.

### <a name="require-device-to-be-marked-as-compliant"></a>Az eszköz megfelelőként való megjelölése

A Microsoft Intune-t üzembe helyező szervezetek az eszközeikről visszaküldött adatok alapján azonosíthatják azokat az eszközöket, amelyek megfelelnek a megfelelőségi követelményeknek. Ezt a szabályzatmegfelelőségi információkat az Intune-ból továbbítja az Azure AD, ahol a feltételes hozzáférés döntéseket hozhat az erőforrásokhoz való hozzáférés engedélyezéséről vagy letiltásáról. A megfelelőségi szabályzatokról további információt a Szabályok beállítása az eszközökre című témakörben talál, [amely lehetővé teszi a hozzáférést a szervezet erőforrásaihoz az Intune használatával.](/intune/protect/device-compliance-get-started)

Az eszközt az Intune (bármely eszközoperációs rendszer) vagy a Windows 10-es eszközök külső gyártótól származó MDM-rendszere megfelelőként jelölheti meg. Jamf pro az egyetlen támogatott harmadik fél MDM rendszer. Az integrációról további információt a [Jamf Pro integrálása az Intune-nal című](/intune/protect/conditional-access-integrate-jamf)cikktartalmaz megfelelőségi célokra című cikkben talál.

Az eszközöket regisztrálni kell az Azure AD-ben, mielőtt megfelelőként megjelölhetők. További információ az eszköz regisztrációmegtalálható a cikkben, [Mi az eszköz identitását](../devices/overview.md).

### <a name="require-hybrid-azure-ad-joined-device"></a>Hibrid Azure AD-hez csatlakozott eszköz megkövetelése

A szervezetek dönthetnek úgy, hogy az eszközidentitást a feltételes hozzáférési házirend részeként használják. A szervezetek megkövetelhetik, hogy az eszközök hibrid Azure AD-hez csatlakozott ezzel a jelölőnégyzetdel. Az eszközidentitásokról a Mi az eszközidentitás című témakörben olvashat [bővebben.](../devices/overview.md)

### <a name="require-approved-client-app"></a>Jóváhagyott ügyfélalkalmazás megkövetelése

A szervezetek megkövetelhetik, hogy a kijelölt felhőalapú alkalmazásokhoz való hozzáférési kísérletet egy jóváhagyott ügyfélalkalmazásból kell elérni. Ezek a jóváhagyott ügyfélalkalmazások támogatják [az Intune alkalmazásvédelmi szabályzatait,](/intune/app-protection-policy) függetlenül a mobileszköz-felügyeleti (MDM) megoldásoktól.

A támogatás vezérlésének kihasználása érdekében a feltételes hozzáférés megköveteli, hogy az eszköz regisztrálva legyen az Azure Active Directoryban, amely egy közvetítőalkalmazás használatát igényli. Ez lehet az iOS-es Microsoft Authenticator vagy az androidos eszközökre készült Microsoft Intune vállalati portál. Ha egy brókeralkalmazás nincs telepítve az eszközön, amikor a felhasználó hitelesítést kísérel meg, a felhasználó átirányítja az alkalmazásboltba a közvetítő alkalmazás telepítéséhez.

Ez a beállítás a következő iOS- és Android-alkalmazásokra vonatkozik:

- Microsoft Azure információvédelem
- Microsoft foglalások
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft számlázás
- Microsoft Kaizala
- Microsoft indítórakéta
- Microsoft Office
- Microsoft Office Hub
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype Vállalati verzió
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft rajztábla

**Megjegyzések**

- A jóváhagyott ügyfélalkalmazások támogatják az Intune mobilalkalmazás-felügyeleti funkcióját.
- A **Jóváhagyott ügyfélalkalmazás-követelmény megkövetelése:**
   - Csak az iOS és az Android rendszert támogatja az eszköz platformállapotához.
   - Az eszköz regisztrálásához egy brókeralkalmazás szükséges. IOS rendszeren a bróker alkalmazás a Microsoft Authenticator, és Androidon az Intune Company Portal alkalmazás.
- A feltételes hozzáférés inPrivate módban nem tekinthető jóváhagyott ügyfélalkalmazásnak.

Tekintse meg a cikket, [hogyan: A jóváhagyott ügyfélalkalmazások megkövetelése a felhőbeli alkalmazás-hozzáférés feltételes hozzáférés konfigurációs](app-based-conditional-access.md) példákat.

### <a name="require-app-protection-policy"></a>Alkalmazásvédelmi szabályzat megkövetelése

A feltételes hozzáférési szabályzatban megkövetelheti, hogy az [Intune-alkalmazásvédelmi szabályzat](/intune/app-protection-policy) jelen legyen az ügyfélalkalmazásban, mielőtt a hozzáférés elérhetővé válik a kiválasztott felhőalapú alkalmazások számára. 

A támogatás vezérlésének kihasználása érdekében a feltételes hozzáférés megköveteli, hogy az eszköz regisztrálva legyen az Azure Active Directoryban, amely egy közvetítőalkalmazás használatát igényli. Ez lehet az iOS-es Microsoft Authenticator vagy az androidos eszközökre készült Microsoft Intune vállalati portál. Ha egy brókeralkalmazás nincs telepítve az eszközön, amikor a felhasználó hitelesítést kísérel meg, a felhasználó átirányítja az alkalmazásboltba a közvetítő alkalmazás telepítéséhez.

Ez a beállítás a következő ügyfélalkalmazásokra vonatkozik:

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Megjegyzések**

- Az alkalmazásvédelmi szabályzathoz készült alkalmazások támogatják az Intune mobilalkalmazás-felügyeleti funkcióját házirend-védelemmel.
- Az alkalmazásvédelmi szabályzat követelményeinek **megkövetelése:**
    - Csak az iOS és az Android rendszert támogatja az eszköz platformállapotához.
    - Az eszköz regisztrálásához egy brókeralkalmazás szükséges. IOS rendszeren a bróker alkalmazás a Microsoft Authenticator, és Androidon az Intune Company Portal alkalmazás.

Tekintse meg a cikket, [hogyan: Az alkalmazásvédelmi szabályzat megkövetelése és egy jóváhagyott ügyfélalkalmazás a felhőalapú alkalmazás-hozzáférés feltételes hozzáférés konfigurációs](app-protection-based-conditional-access.md) példákat.

### <a name="terms-of-use"></a>Használati feltételek

Ha a szervezet használati feltételeket hozott létre, további beállítások is láthatók lehetnek a támogatási vezérlők alatt. Ezek a beállítások lehetővé teszik a rendszergazdák számára, hogy a házirend által védett erőforrások elérésének feltételeként a használati feltételek elismerését követeljék meg. A használati feltételekről további információt az [Azure Active Directory használati feltételei](terms-of-use.md)című cikktartalmaz.

## <a name="next-steps"></a>További lépések

- [Feltételes hozzáférés: Munkamenet-vezérlők](concept-conditional-access-session.md)

- [Feltételes hozzáférés közös házirendjei](concept-conditional-access-policy-common.md)

- [Csak jelentési mód](concept-conditional-access-report-only.md)
