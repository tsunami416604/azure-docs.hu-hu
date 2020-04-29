---
title: Vezérlők megadása a feltételes hozzáférési házirendben – Azure Active Directory
description: Az Azure AD feltételes hozzáférési szabályzatában szereplő engedélyezési vezérlők
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80331841"
---
# <a name="conditional-access-grant"></a>Feltételes hozzáférés: Engedélyezés

Egy feltételes hozzáférési szabályzaton belül a rendszergazda a hozzáférés-vezérlés használatával engedélyezheti vagy letilthatja az erőforrásokhoz való hozzáférést.

![Feltételes hozzáférési szabályzat többtényezős hitelesítést igénylő engedélyezési vezérlővel](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Hozzáférés letiltása

A blokk figyelembe veszi az összes hozzárendelést, és megakadályozza a hozzáférést a feltételes hozzáférési házirend konfigurációja alapján.

A Block egy hatékony vezérlő, amelyet megfelelő ismeretekkel kell megforgatni. Az engedélyezés előtt a rendszergazdáknak [csak jelentési módot](concept-conditional-access-report-only.md) kell használniuk a teszteléshez.

## <a name="grant-access"></a>Hozzáférés biztosítása

A rendszergazdák dönthetnek úgy, hogy egy vagy több vezérlőt kényszerítenek a hozzáférés engedélyezésekor. Ezek a vezérlőelemek a következő beállításokat tartalmazzák: 

- [Többtényezős hitelesítés megkövetelése (Azure Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Eszköz megfelelőként való megjelölésének megkövetelése (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Hibrid Azure AD-hez csatlakoztatott eszköz megkövetelése](../devices/concept-azure-ad-join-hybrid.md)
- [Jóváhagyott ügyfélalkalmazás megkövetelése](app-based-conditional-access.md)
- [Alkalmazásvédelmi szabályzat megkövetelése](app-protection-based-conditional-access.md)

Ha a rendszergazdák úgy döntenek, hogy kombinálják ezeket a beállításokat, a következő módszereket választhatják ki:

- Az összes kijelölt vezérlő megkövetelése (vezérlés **és** vezérlés)
- A kiválasztott vezérlők (vezérlés **vagy** vezérlés) egyikének megkövetelése

Alapértelmezés szerint a feltételes hozzáférés minden kijelölt vezérlőt igényel.

### <a name="require-multi-factor-authentication"></a>Többtényezős hitelesítés megkövetelése

Ha bejelöli ezt a jelölőnégyzetet, a felhasználóknak az Azure Multi-Factor Authentication kell végrehajtaniuk. Az Azure Multi-Factor Authentication telepítésével kapcsolatos további információkért tekintse meg a [felhőalapú Azure multi-Factor Authentication üzembe helyezésének megtervezése](../authentication/howto-mfa-getstarted.md)című cikket.

### <a name="require-device-to-be-marked-as-compliant"></a>Eszköz megfelelőként való megjelölésének megkövetelése

A Microsoft Intune központilag telepített szervezetek a megfelelőségi követelményeknek megfelelő eszközök azonosítására használhatják az eszközük által visszaadott adatokat. A szabályzat megfelelőségi információi továbbítva lesznek az Intune-ból az Azure AD-ba, ahol a feltételes hozzáférés az erőforrásokhoz való hozzáférés engedélyezésére vagy letiltására vonatkozó döntéseket hozhat. A megfelelőségi szabályzatokkal kapcsolatos további információkért tekintse [meg az eszközökön a szervezet erőforrásaihoz való hozzáférés engedélyezése az Intune-](/intune/protect/device-compliance-get-started)nal című cikket.

Az eszközök az Intune (bármilyen eszköz operációs rendszer esetén) vagy külső gyártótól származó, Windows 10-es eszközökhöz tartozó MDM-rendszerek szerint megfelelőként jelölhetők meg. A JAMF Pro az egyetlen támogatott harmadik féltől származó MDM-rendszer. További információ az integrációról: a [JAMF Pro integrálása az Intune](/intune/protect/conditional-access-integrate-jamf)-nal a megfelelőség érdekében.

Az eszközöknek regisztrálva kell lenniük az Azure AD-ben, mielőtt azok megfelelőként jelölhetők meg. Az eszközök regisztrálásával kapcsolatos további információkért tekintse meg a következő cikket: [Mi az az eszköz identitása](../devices/overview.md).

### <a name="require-hybrid-azure-ad-joined-device"></a>Hibrid Azure AD-hez csatlakoztatott eszköz megkövetelése

A szervezetek dönthetnek úgy, hogy az eszköz identitását használják a feltételes hozzáférési szabályzat részeként. A szervezeteknek meg kell követelniük, hogy az eszközök hibrid Azure AD-hez legyenek csatlakoztatva ezzel a jelölőnégyzettel. Az eszközök identitásával kapcsolatos további információkért tekintse meg a [Mi az eszköz identitása?](../devices/overview.md)című cikket.

### <a name="require-approved-client-app"></a>Jóváhagyott ügyfélalkalmazás megkövetelése

A szervezeteknek meg kell követelniük, hogy a kiválasztott felhőalapú alkalmazásokhoz való hozzáférési kísérletet egy jóváhagyott ügyfélalkalmazás alapján kell végrehajtani. Ezek a jóváhagyott ügyfélalkalmazások a mobileszköz-kezelési (MDM) megoldástól függetlenül támogatják az [Intune app Protection-szabályzatokat](/intune/app-protection-policy) .

Ennek a támogatásnak a kihasználása érdekében a feltételes hozzáférés megköveteli, hogy az eszköz regisztrálva legyen Azure Active Directoryban, amelyhez közvetítő alkalmazást kell használni. Ez lehet az iOS-es Microsoft Authenticator vagy az androidos eszközökre készült Microsoft Intune vállalati portál. Ha egy közvetítő alkalmazás nincs telepítve az eszközön, amikor a felhasználó megkísérli a hitelesítést, a rendszer átirányítja a felhasználót az App Store-ba a Broker alkalmazás telepítéséhez.

Ez a beállítás a következő iOS-és Android-alkalmazásokra vonatkozik:

- Microsoft Azure Information Protection
- Microsoft-foglalások
- Microsoft-Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft-számlázás
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- Microsoft Office hub
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
- Microsoft-Yammer
- Microsoft faliújság

**Megjegyzések**

- A jóváhagyott ügyfélalkalmazások támogatják az Intune Mobile Application Management szolgáltatást.
- A **jóváhagyott ügyfélalkalmazás megkövetelése** :
   - A csak az iOS és az Android for Device platform feltételeit támogatja.
   - Az eszköz regisztrálásához egy Broker-alkalmazás szükséges. IOS rendszeren a közvetítő alkalmazás Microsoft Authenticator és Android rendszeren, Intune Céges portál alkalmazás.
- A feltételes hozzáférés nem tekintheti meg a Microsoft Edge-t InPrivate módban egy jóváhagyott ügyfélalkalmazás.

Tekintse meg a következő cikket [: a felhőalapú alkalmazások hozzáférésének megkövetelése a Cloud app Accesshez feltételes hozzáféréssel](app-based-conditional-access.md) a konfigurációs példákhoz.

### <a name="require-app-protection-policy"></a>Alkalmazásvédelmi szabályzat megkövetelése

A feltételes hozzáférési házirendben megkövetelheti, hogy az [Intune app Protection szabályzata](/intune/app-protection-policy) elérhető legyen az ügyfélalkalmazás számára, mielőtt a kiválasztott felhőalapú alkalmazások hozzáférhessenek a hozzáféréshez. 

Ennek a támogatásnak a kihasználása érdekében a feltételes hozzáférés megköveteli, hogy az eszköz regisztrálva legyen Azure Active Directoryban, amelyhez közvetítő alkalmazást kell használni. Ez lehet az iOS-es Microsoft Authenticator vagy az androidos eszközökre készült Microsoft Intune vállalati portál. Ha egy közvetítő alkalmazás nincs telepítve az eszközön, amikor a felhasználó megkísérli a hitelesítést, a rendszer átirányítja a felhasználót az App Store-ba a Broker alkalmazás telepítéséhez.

Ez a beállítás a következő ügyfélalkalmazások esetében érvényes:

- Microsoft-Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Megjegyzések**

- Az App Protection-házirend alkalmazásai támogatják az Intune Mobile Application Management szolgáltatást a házirend-védelemmel.
- Az **alkalmazás-védelmi házirend követelményeinek megkövetelése** :
    - A csak az iOS és az Android for Device platform feltételeit támogatja.
    - Az eszköz regisztrálásához egy Broker-alkalmazás szükséges. IOS rendszeren a közvetítő alkalmazás Microsoft Authenticator és Android rendszeren, Intune Céges portál alkalmazás.

Tekintse meg a következő cikket [: How to: app Protection-házirend és egy jóváhagyott ügyfélalkalmazás a Cloud app Accesshez feltételes hozzáféréssel](app-protection-based-conditional-access.md) a konfigurációs példákhoz.

### <a name="terms-of-use"></a>Használati feltételek

Ha a szervezet létrehozta a használati feltételeket, az engedélyezési vezérlők területen további beállítások is láthatók. Ezek a beállítások lehetővé teszik a rendszergazdák számára, hogy a szabályzat által védett erőforrások elérésének feltétele megkövetelje a használati feltételek visszaigazolását. A használati feltételekkel kapcsolatos további információkért tekintse meg a cikkben [Azure Active Directory használati feltételeket](terms-of-use.md).

## <a name="next-steps"></a>További lépések

- [Feltételes hozzáférés: munkamenet-vezérlők](concept-conditional-access-session.md)

- [Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

- [Csak jelentési mód](concept-conditional-access-report-only.md)
