---
title: Munkamenet-vezérlők a feltételes hozzáférési házirendben – Azure Active Directory
description: Mik azok a munkamenet-vezérlők az Azure AD feltételes hozzáférési szabályzatában?
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
ms.openlocfilehash: e99b9b87f939d614679fdecf24c9d36d99bf2938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671875"
---
# <a name="conditional-access-session"></a>Feltételes hozzáférés: Munkamenet

A feltételes hozzáférési házirendeken belül a rendszergazda munkamenet-vezérlők használatával engedélyezheti a korlátozott felhasználói élményt az adott felhőalapú alkalmazásokon belül.

![Feltételes hozzáférési házirend többtényezős hitelesítést igénylő támogatásvezérléssel](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Alkalmazás által kényszerített korlátozások

A szervezetek ezt a vezérlőt, hogy az Azure AD-től megkövetelheti az eszközadatok adotttovább-át a kiválasztott felhőalapú alkalmazások. Az eszközadatok lehetővé teszik, hogy a felhőalapú alkalmazások megtudják, hogy a kapcsolatot egy megfelelő vagy tartományhoz csatlakozó eszközről kezdeményezték-e. Ez a vezérlő csak a SharePoint Online-t és az Exchange Online-t támogatja kijelölt felhőalapú alkalmazásként. Ha be van jelölve, a felhőalapú alkalmazás az eszköz adatait arra használja, hogy az eszköz állapotától függően korlátozott vagy teljes felhasználói élményt nyújtson a felhasználóknak.

Az alkalmazásáltal kényszerített korlátozások használatával és konfigurációjával kapcsolatos további információkért lásd az alábbi cikkeket:

- [Korlátozott hozzáférés engedélyezése a SharePoint Online-nal](/sharepoint/control-access-from-unmanaged-devices)
- [Korlátozott hozzáférés engedélyezése az Exchange Online-nal](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Feltételes hozzáférés alkalmazás vezérlése

Feltételes hozzáférésű alkalmazásvezérlés fordított proxy architektúrát használ, és egyedülállóan integrálva van az Azure AD feltételes hozzáféréssel. Az Azure AD feltételes hozzáférés lehetővé teszi, hogy bizonyos feltételek alapján kényszerítse a szervezet alkalmazásainak hozzáférés-vezérlését. A feltételek határozzák meg, hogy kire (felhasználó vagy felhasználócsoport) és mit (mely felhőalapú alkalmazásokra) és hol (mely helyekre és hálózatokra) alkalmazza a feltételes hozzáférési szabályzat. Miután meghatározta a feltételeket, átirányíthatja a felhasználókat a [Microsoft Cloud App Security alkalmazásbiztonsághoz,](/cloud-app-security/what-is-cloud-app-security) ahol hozzáférés- és munkamenet-vezérlők alkalmazásával megvédheti az adatokat a feltételes hozzáférésű alkalmazásvezérléssel.

A feltételes hozzáférésű alkalmazásvezérlés lehetővé teszi a felhasználói alkalmazások hozzáférésének és munkameneteinek valós idejű figyelését és szabályozását a hozzáférési és munkamenet-házirendek alapján. Az Access és a munkamenet-szabályzatok a Cloud App Security portálon belül használatosak a szűrők további finomításához és a felhasználón végrehajtandó műveletek beállításához. A hozzáférési és munkamenet-házirendek segítségével a következőkre van szüksége:

- Az adatok kiszivárgásának megakadályozása: Letilthatja a bizalmas dokumentumok letöltését, kivágását, másolását és nyomtatását például nem felügyelt eszközökön.
- Védelem letöltéskor: Ahelyett, hogy letiltaná a bizalmas dokumentumok letöltését, megkövetelheti, hogy a dokumentumokat az Azure Information Protection címkével és védéssel védje. Ez a művelet biztosítja a dokumentum védelmét, és a felhasználói hozzáférés korlátozott egy potenciálisan kockázatos munkamenetben.
- Címkézetlen fájlok feltöltésének megakadályozása: A bizalmas fájlok feltöltése, terjesztése és mások általi használatelőtt fontos, hogy a fájl megfelelő címkével és védelemmel rendelkezzen. Biztosíthatja, hogy a bizalmas tartalmú címkézetlen fájlok feltöltése mindaddig le legyen tiltva, amíg a felhasználó osztályba nem sorolja a tartalmat.
- A felhasználói munkamenetek figyelése a megfelelőség: Kockázatos felhasználók figyelése, amikor bejelentkezik az alkalmazásokba, és a műveletek et a munkameneten belül naplózza. Megvizsgálhatja és elemezheti a felhasználói viselkedést, hogy megértse, hol és milyen feltételek mellett kell munkamenet-szabályzatokat alkalmazni a jövőben.
- Hozzáférés letiltása: Az egyes alkalmazások és felhasználók hozzáférését több kockázati tényezőtől függően részletesen letilthatja. Letilthatja például őket, ha az ügyféltanúsítványokat használják eszközkezelési űrlapként.
- Egyéni tevékenységek letiltása: Egyes alkalmazások egyedi forgatókönyvekkel rendelkeznek, amelyek kockázatot hordoznak, például bizalmas tartalmú üzeneteket küldenek olyan alkalmazásokban, mint a Microsoft Teams vagy a Slack. Az ilyen típusú esetekben az üzeneteket bizalmas tartalom rakhatja le, és valós időben letilthatja.

További információt a [Kiemelt alkalmazások feltételes hozzáférésű alkalmazásvezérlésének telepítése](/cloud-app-security/proxy-deployment-aad)című cikkben talál.

## <a name="sign-in-frequency-preview"></a>Bejelentkezésgyakoriság (előzetes verzió)

A bejelentkezési gyakoriság határozza meg azt az időszakot, amely alatt a felhasználónak újra be kell jelentkeznie egy erőforrás elérésekor.

A bejelentkezési gyakoriság beállítása olyan alkalmazásokkal működik, amelyek a szabványoknak megfelelően implementálták az OAUTH2 vagy OIDC protokollokat. A legtöbb Microsoft natív Windows, Mac és Mobile alkalmazás, beleértve a következő webalkalmazásokat is, megfelel a beállításnak.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- O365 Felügyeleti portál
- Exchange Online
- SharePoint és OneDrive
- Teams webes ügyfél
- Dynamics CRM Online
- Azure portál

További információt a [Hitelesítési munkamenet-kezelés konfigurálása feltételes hozzáféréssel című témakörben talál.](howto-conditional-access-session-lifetime.md#user-sign-in-frequency)

## <a name="persistent-browser-session-preview"></a>Állandó böngészőmunkamenet (előzetes verzió)

Az állandó böngészőmunkamenet lehetővé teszi a felhasználók számára, hogy a böngészőablak bezárása és újbóli megnyitása után is bejelentkezve maradjanak.

További információt a [Hitelesítési munkamenet-kezelés konfigurálása feltételes hozzáféréssel című témakörben talál.](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions)

## <a name="next-steps"></a>További lépések

- [Feltételes hozzáférés közös házirendjei](concept-conditional-access-policy-common.md)

- [Csak jelentési mód](concept-conditional-access-report-only.md)
