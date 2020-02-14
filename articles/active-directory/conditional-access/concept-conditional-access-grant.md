---
title: Vezérlők megadása a feltételes hozzáférési házirendben – Azure Active Directory
description: Az Azure AD feltételes hozzáférési szabályzatában szereplő engedélyezési vezérlők
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89063cc8131c28f20153c6fe9b4c71b58794e609
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192115"
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
- [Eszköz megfelelőként való megjelölésének megkövetelése (Microsoft Intune)](https://docs.microsoft.com/intune/protect/device-compliance-get-started)
- [Hibrid Azure AD-hez csatlakoztatott eszköz megkövetelése](../devices/concept-azure-ad-join-hybrid.md)
- [Jóváhagyott ügyfélalkalmazás megkövetelése](app-based-conditional-access.md)
- [Alkalmazás-védelmi házirend megkövetelése](app-protection-based-conditional-access.md)

Ha a rendszergazdák úgy döntenek, hogy kombinálják ezeket a beállításokat, a következő módszereket választhatják ki:

- Az összes kijelölt vezérlő megkövetelése (vezérlés **és** vezérlés)
- A kiválasztott vezérlők (vezérlés **vagy** vezérlés) egyikének megkövetelése

Alapértelmezés szerint a feltételes hozzáférés minden kijelölt vezérlőt igényel.

### <a name="require-multi-factor-authentication"></a>Többtényezős hitelesítés megkövetelése

Ha bejelöli ezt a jelölőnégyzetet, a felhasználóknak az Azure Multi-Factor Authentication kell végrehajtaniuk. Az Azure Multi-Factor Authentication telepítésével kapcsolatos további információkért tekintse meg a [felhőalapú Azure multi-Factor Authentication üzembe helyezésének megtervezése](../authentication/howto-mfa-getstarted.md)című cikket.

### <a name="require-device-to-be-marked-as-compliant"></a>Eszköz megfelelőként való megjelölésének megkövetelése

A Microsoft Intune központilag telepített szervezetek a megfelelőségi követelményeknek megfelelő eszközök azonosítására használhatják az eszközük által visszaadott adatokat. A szabályzat megfelelőségi információi továbbítva lesznek az Intune-ból az Azure AD-ba, ahol a feltételes hozzáférés az erőforrásokhoz való hozzáférés engedélyezésére vagy letiltására vonatkozó döntéseket hozhat. A megfelelőségi szabályzatokkal kapcsolatos további információkért tekintse [meg az eszközökön a szervezet erőforrásaihoz való hozzáférés engedélyezése az Intune-](https://docs.microsoft.com/intune/protect/device-compliance-get-started)nal című cikket.

### <a name="require-hybrid-azure-ad-joined-device"></a>Hibrid Azure AD-hez csatlakoztatott eszköz megkövetelése

A szervezetek dönthetnek úgy, hogy az eszköz identitását használják a feltételes hozzáférési szabályzat részeként. A szervezeteknek meg kell követelniük, hogy az eszközök hibrid Azure AD-hez legyenek csatlakoztatva ezzel a jelölőnégyzettel. Az eszközök identitásával kapcsolatos további információkért tekintse meg a [Mi az eszköz identitása?](../devices/overview.md)című cikket.

### <a name="require-approved-client-app"></a>Jóváhagyott ügyfélalkalmazás megkövetelése

A szervezeteknek meg kell követelniük, hogy a kiválasztott felhőalapú alkalmazásokhoz való hozzáférési kísérletet egy jóváhagyott ügyfélalkalmazás alapján kell végrehajtani.

Ez a beállítás a következő ügyfélalkalmazások esetében érvényes:

- Microsoft Azure Information Protection
- Microsoft-foglalások
- Microsoft-Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- A Microsoft számlázás
- Microsoft Kaizala
- A Microsoft indítója
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- A Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype vállalati verzió
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft-Yammer

**Megjegyzések**

- A jóváhagyott ügyfélalkalmazások támogatják az Intune Mobile Application Management szolgáltatást.
- A **jóváhagyott ügyfélalkalmazás megkövetelése** :
   - A csak az iOS és az Android for Device platform feltételeit támogatja.
- A feltételes hozzáférés nem tekintheti meg a Microsoft Edge-t InPrivate módban egy jóváhagyott ügyfélalkalmazás.

### <a name="require-app-protection-policy"></a>Alkalmazásvédelmi szabályzat megkövetelése

A feltételes hozzáférési házirendben megkövetelheti, hogy az alkalmazás védelmi szabályzata elérhető legyen az ügyfélalkalmazás számára, mielőtt a kiválasztott felhőalapú alkalmazások hozzáférhessenek a hozzáféréshez. 

![Hozzáférés vezérlése az App Protection-házirenddel](./media/technical-reference/22.png)

Ez a beállítás a következő ügyfélalkalmazások esetében érvényes:

- Microsoft-Cortana
- Microsoft OneDrive
- Microsoft Outlook
- A Microsoft Planner

**Megjegyzések**

- Az App Protection-házirend alkalmazásai támogatják az Intune Mobile Application Management szolgáltatást a házirend-védelemmel.
- Az **alkalmazás-védelmi házirend követelményeinek megkövetelése** :
    - A csak az iOS és az Android for Device platform feltételeit támogatja.

## <a name="next-steps"></a>Következő lépések

- [Feltételes hozzáférés: munkamenet-vezérlők](concept-conditional-access-session.md)

- [Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

- [Csak jelentési mód](concept-conditional-access-report-only.md)
