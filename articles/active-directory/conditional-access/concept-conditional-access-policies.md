---
title: Feltételes hozzáférési szabályzat létrehozása – Azure Active Directory
description: Milyen lehetőségek állnak rendelkezésre a feltételes hozzáférési szabályzat létrehozásához, és mit jelentenek?
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
ms.openlocfilehash: 87224c9e3e697b86aab51d1e922af8ab2130ac40
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186236"
---
# <a name="building-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

Ahogy azt a cikk ismerteti, [Mi a feltételes hozzáférés](overview.md), a feltételes hozzáférési szabályzat a **hozzárendelések** és a **hozzáférés-vezérlések**if-then utasítása. A feltételes hozzáférési szabályzatok együttesen, a döntések meghozatalához és a szervezeti szabályzatok betartatásához biztosítanak jeleket.

Hogyan hozza létre a szervezet ezeket a szabályzatokat? Mi szükséges?

![Feltételes hozzáférés (jelek + döntések + kényszerítés = szabályzatok)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>Hozzárendelések

A hozzárendelések rész a feltételes hozzáférési szabályzatot határozza meg.

### <a name="users-and-groups"></a>Felhasználók és csoportok

A [felhasználók és csoportok](concept-conditional-access-users-groups.md) hozzárendelik a szabályzatot, vagy kizárják azokat. Ez a hozzárendelés magában foglalhatja az összes felhasználót, a felhasználók adott csoportjait, a címtárbeli szerepköröket vagy a külső vendég felhasználókat. 

### <a name="cloud-apps-or-actions"></a>Felhőalapú alkalmazások vagy műveletek

A [felhőalapú alkalmazások vagy műveletek](concept-conditional-access-cloud-apps.md) magukban foglalhatják vagy kizárhatják a szabályzat hatálya alá eső felhőalapú alkalmazásokat vagy felhasználói műveleteket.

### <a name="conditions"></a>Feltételek

Egy házirend több [feltételt](concept-conditional-access-conditions.md)is tartalmazhat.

#### <a name="sign-in-risk"></a>Bejelentkezési kockázat

Az [Azure ad Identity Protection](../identity-protection/overview.md)-t használó szervezetek esetében az ott létrehozott kockázati észlelések befolyásolhatják a feltételes hozzáférési szabályzatokat.

#### <a name="device-platforms"></a>Eszköz platformok

Azok a szervezetek, amelyeken több eszköz operációs rendszere van, különböző platformokon szeretnének kikényszeríteni bizonyos szabályzatokat. 

Az eszköz platformjának kiszámításához használt információk nem ellenőrzött forrásokból, például felhasználói ügynök sztringből származnak.

#### <a name="locations"></a>Helyek

A tartózkodási hely adatai az IP térinformatikai adataival rendelkeznek. A rendszergazdák meghatározhatják a helyszíneket, és dönthetnek úgy, hogy a szervezet hálózati helyeihez hasonlóan megbízhatóként jelölik meg azokat.

#### <a name="client-apps"></a>Ügyfélalkalmazások

Alapértelmezés szerint a feltételes hozzáférési szabályzatok a modern hitelesítést támogató böngészőalapú alkalmazásokra, mobil alkalmazásokra és asztali ügyfelekre vonatkoznak. 

Ez a hozzárendelési feltétel lehetővé teszi, hogy a feltételes hozzáférési szabályzatok meghatározott ügyfélalkalmazások számára célozzanak, amelyek nem a modern hitelesítést használják. Ezek az alkalmazások közé tartoznak az Exchange ActiveSync-ügyfelek, a régebbi Office-alkalmazások, amelyek nem használnak modern hitelesítést, valamint levelezési protokollokat, például az IMAP, a MAPI, a POP és az SMTP protokollt.

#### <a name="device-state"></a>Eszköz állapota

Ez a vezérlő a hibrid Azure AD-hez csatlakoztatott vagy az Intune-ban megfelelőnek jelölt eszközök kizárására szolgál. Ezt a kizárást megteheti a nem felügyelt eszközök blokkolásához. 

## <a name="access-controls"></a>Hozzáférés-vezérlés

A feltételes hozzáférési házirend hozzáférés-vezérlési része szabályozza a szabályzatok betartatásának módját.

### <a name="grant"></a>Grant

A [Grant](concept-conditional-access-grant.md) lehetővé teszi a rendszergazdák számára a szabályzatok betartatását, ahol letilthatják vagy megadhatják a hozzáférést.

#### <a name="block-access"></a>Hozzáférés letiltása

A hozzáférés letiltása csak azt eredményezi, hogy letiltja a hozzáférést a megadott hozzárendelések alatt. A blokk vezérlőelem hatékony, és a megfelelő ismeretekkel kell rendelkeznie.

#### <a name="grant-access"></a>Hozzáférés biztosítása

Az engedélyezési vezérlő egy vagy több vezérlő kényszerítését is elindíthatja. 

- Többtényezős hitelesítés megkövetelése (Azure Multi-Factor Authentication)
- Eszköz megfelelőként való megjelölésének megkövetelése (Intune)
- Hibrid Azure AD-hez csatlakoztatott eszköz megkövetelése
- Jóváhagyott ügyfélalkalmazás megkövetelése
- Alkalmazásvédelmi szabályzat megkövetelése

A rendszergazdák dönthetnek úgy, hogy az alábbi beállítások használatával megkövetelhetik az előző vezérlők vagy az összes kijelölt vezérlő egyikét. A többszörös vezérlők alapértelmezett értéke az összes megkövetelése.

- Az összes kijelölt vezérlő megkövetelése (vezérlés és vezérlés)
- A kiválasztott vezérlők (vezérlés vagy vezérlés) egyikének megkövetelése

### <a name="session"></a>Munkamenet

A [munkamenet-vezérlőelemek](concept-conditional-access-session.md) korlátozhatják a felhasználói élményt 

- Alkalmazás által kényszerített korlátozások használata
   - Jelenleg csak az Exchange Online és a SharePoint Online szolgáltatással működik.
      - Az eszköz adatainak átadásával lehetővé teszi a teljes vagy korlátozott hozzáférést biztosító élmény vezérlését.
- feltételes hozzáférést biztosító alkalmazás-vezérlő használata
   - A Microsoft Cloud App Security jeleket használ a következőkhöz hasonló módon: 
      - Bizalmas dokumentumok letöltésének, kivágásának, másolásának és nyomtatásának letiltása.
      - A kockázatos munkamenet viselkedésének figyelése.
      - Bizalmas fájlok címkézésének megkövetelése.
- Bejelentkezési gyakoriság
   - Lehetőség a modern hitelesítés alapértelmezett bejelentkezési gyakoriságának megváltoztatására.
- Állandó böngésző-munkamenet
   - Lehetővé teszi a felhasználók számára, hogy a böngészőablak bezárása és újbóli megnyitása után is bejelentkezve maradjanak.

## <a name="simple-policies"></a>Egyszerű házirendek

A feltételes hozzáférési szabályzatnak legalább a következőket kell tartalmaznia:

- A házirend **neve** .
- **Hozzárendelések**
   - **Felhasználók és/vagy csoportok** , amelyekre alkalmazni kívánja a szabályzatot.
   - **Felhőalapú alkalmazások vagy műveletek** , amelyekre alkalmazni kell a szabályzatot.
- **Hozzáférés-vezérlés**
   - Vezérlők **engedélyezése** vagy **letiltása**

![Üres feltételes hozzáférési szabályzat](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

Az [általános feltételes hozzáférési szabályzatok](concept-conditional-access-policy-common.md) a legtöbb szervezet számára hasznosnak bizonyuló szabályzatokat tartalmaznak.

## <a name="next-steps"></a>Következő lépések

[Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)

[Felhőalapú Azure-beli Multi-Factor Authentication üzembe helyezés tervezése](../authentication/howto-mfa-getstarted.md)

[Az eszközök megfelelőségének kezelése az Intune-nal](https://docs.microsoft.com/intune/device-compliance-get-started)

[Microsoft Cloud App Security és feltételes hozzáférés](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
