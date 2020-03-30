---
title: Feltételes hozzáférési szabályzat létrehozása – Azure Active Directory
description: Milyen lehetőségei vannak a feltételes hozzáférési házirend létrehozásának, és mit jelentenek?
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
ms.openlocfilehash: 907ad8705742e4b2e38b13c3c675ebd333bd27d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295328"
---
# <a name="building-a-conditional-access-policy"></a>Feltételes hozzáférési házirend létrehozása

Ahogy azt a [Mi a feltételes hozzáférés](overview.md)című cikkben ismertetjük, a feltételes hozzáférési házirend a **Hozzárendelések** és **a hozzáférés-vezérlők**if-then utasítása. A feltételes hozzáférési szabályzat egyesíti a jeleket, döntéseket hoz, és kényszeríti a szervezeti házirendeket.

Hogyan hozza létre egy szervezet ezeket a házirendeket? Mire van szükség?

![Feltételes hozzáférés (Jelek + határozatok + végrehajtás = politikák)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>Hozzárendelések

A hozzárendelések rész határozza meg, hogy ki, mit és hol a feltételes hozzáférési házirend.

### <a name="users-and-groups"></a>Felhasználók és csoportok

[A felhasználók és csoportok](concept-conditional-access-users-groups.md) kijelölik, hogy a házirend kit fog tartalmazni vagy kizárni. Ez a hozzárendelés magában foglalhatja az összes felhasználót, adott felhasználói csoportokat, címtárszerepköröket vagy külső vendégfelhasználókat. 

### <a name="cloud-apps-or-actions"></a>Felhőalkalmazások vagy műveletek

[A felhőalapú alkalmazások vagy műveletek](concept-conditional-access-cloud-apps.md) tartalmazhatnak vagy kizárhatnak felhőalapú alkalmazásokat vagy felhasználói műveleteket, amelyekre a szabályzat vonatkozik.

### <a name="conditions"></a>Feltételek

Egy házirend több [feltételt](concept-conditional-access-conditions.md)is tartalmazhat.

#### <a name="sign-in-risk"></a>Bejelentkezési kockázat

Az Azure [AD Identity Protection](../identity-protection/overview.md)használatával rendelkező szervezetek esetében az ott létrehozott kockázatészlelések befolyásolhatják a feltételes hozzáférési szabályzatokat.

#### <a name="device-platforms"></a>Eszközplatformok

A több eszközoperációs rendszerplatformmal rendelkező szervezetek különböző platformokon is érvényesíthetnek bizonyos házirendeket. 

Az eszközplatform kiszámításához használt adatok nem ellenőrzött forrásokból származnak, például a módosítható felhasználói ügynökkarakterláncokból.

#### <a name="locations"></a>Helyek

A helyadatokat az IP-földrajzi helymeghatározási adatok szolgáltatják. A rendszergazdák dönthetnek úgy, hogy helyeket határoznak meg, és néhányat megbízhatóként jelölnek meg, például a szervezet hálózati helyein.

#### <a name="client-apps"></a>Ügyfélalkalmazások

Alapértelmezés szerint a feltételes hozzáférésre vonatkozó irányelvek a modern hitelesítést támogató böngészőalkalmazásokra, mobilalkalmazásokra és asztali ügyfelekre vonatkoznak. 

Ez a hozzárendelési feltétel lehetővé teszi, hogy a feltételes hozzáférési házirendek olyan ügyfélalkalmazásokat célozzanak meg, amelyek nem használnak modern hitelesítést. Ezek közé az alkalmazások közé tartoznak az Exchange ActiveSync-ügyfelek, a modern hitelesítést nem használó régebbi Office-alkalmazások, valamint az olyan levelezési protokollok, mint az IMAP, a MAPI, a POP és az SMTP.

#### <a name="device-state"></a>Eszköz állapota

Ez a vezérlő a hibrid Azure AD-hez csatlakozott vagy az Intune-ban megfelelőként megjelölt eszközök kizárására szolgál. Ez a kizárás a nem felügyelt eszközök blokkolására használható. 

## <a name="access-controls"></a>Hozzáférés-vezérlés

A hozzáférés-vezérlés része a feltételes hozzáférési házirend szabályozza, hogyan házirend kényszeríti.

### <a name="grant"></a>Engedély

[A Grant](concept-conditional-access-grant.md) lehetővé teszi a rendszergazdák számára a házirend-kényszerítés módját, ahol letilthatják vagy hozzáférést adhatnak.

#### <a name="block-access"></a>Fájlhozzáférés

Blokk hozzáférés nem csak, hogy blokkolja a hozzáférést a megadott hozzárendelések. A blokk vezérlés erős, és meg kell forgatható a megfelelő ismeretekkel.

#### <a name="grant-access"></a>Hozzáférés biztosítása

A támogatás ellenőrzése egy vagy több vezérlő végrehajtását válthatja ki. 

- Többtényezős hitelesítés megkövetelése (Azure többtényezős hitelesítés)
- Az eszköz megfelelőként való megjelölésének megkövetelése (Intune)
- Hibrid Azure AD-hez csatlakozott eszköz megkövetelése
- Jóváhagyott ügyfélalkalmazás megkövetelése
- Alkalmazásvédelmi szabályzat megkövetelése

A rendszergazdák az alábbi beállításokkal megkövetelhetik az előző vagy az összes kijelölt vezérlő valamelyikét. Több vezérlő alapértelmezett beállítása az összes előírása.

- Az összes kijelölt vezérlő (vezérlő és vezérlő elem) megkövetelése
- A kijelölt vezérlők (vezérlőelem vagy vezérlőelem) egyikének megkövetelése

### <a name="session"></a>Munkamenet

[A munkamenet-vezérlők](concept-conditional-access-session.md) korlátozhatják a felhasználói élményt 

- Alkalmazás által kényszerített korlátozások használata
   - Jelenleg csak az Exchange Online-nal és a SharePoint Online-nal működik.
      - Átadja az eszközadatokat, hogy lehetővé tegye a teljes vagy korlátozott hozzáférést biztosító élmény vezérlését.
- Feltételes hozzáférésű alkalmazásvezérlő használata
   - A Microsoft Cloud App Security jelzéseit használja a következő dolgokhoz: 
      - Bizalmas dokumentumok letöltésének, kivágásának, másolásának és nyomtatásának letiltása.
      - A munkamenet kockázatos viselkedésének figyelése.
      - Bizalmas fájlok címkézésének megkövetelése.
- Bejelentkezési gyakoriság
   - A modern hitelesítés alapértelmezett bejelentkezési gyakoriságának módosítása.
- Állandó böngészőmunkamenet
   - Lehetővé teszi a felhasználók számára, hogy a böngészőablak bezárása és újbóli megnyitása után is bejelentkezve maradjanak.

## <a name="simple-policies"></a>Egyszerű házirendek

A feltételes hozzáférési házirendnek legalább a következőket kell tartalmaznia a kényszerítéshez:

- A házirend **neve.**
- **Hozzárendelések**
   - **Azok a felhasználók és/vagy csoportok,** akikre a házirendet alkalmazni szeretné.
   - **Felhőalapú alkalmazások vagy a** szabályzat alkalmazására alkalmazott műveletek.
- **Hozzáférés-vezérlés**
   - **Támogatás** vagy **blokk** vezérlők

![Üres feltételes hozzáférési házirend](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

A [cikk közös feltételes hozzáférés házirendek](concept-conditional-access-policy-common.md) tartalmaz néhány szabályzatot, amely szerintünk hasznos lehet a legtöbb szervezet számára.

## <a name="next-steps"></a>További lépések

[Bejelentkezési viselkedés szimulálása a Feltételes hozzáférés Mi ha eszközzel](troubleshoot-conditional-access-what-if.md)

[Felhőalapú Azure Multi-Factor Authentication-telepítés megtervezése](../authentication/howto-mfa-getstarted.md)

[Az eszközök intune-nak való megfelelésének kezelése](/intune/device-compliance-get-started)

[Microsoft Cloud App Security és feltételes hozzáférés](/cloud-app-security/proxy-intro-aad)
