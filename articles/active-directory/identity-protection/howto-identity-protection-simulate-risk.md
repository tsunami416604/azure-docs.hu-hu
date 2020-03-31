---
title: Kockázatészlelés szimulálása az Azure AD Identity Protection szolgáltatásban
description: Ismerje meg, hogyan szimulálhatja a kockázatészlelést az Identitásvédelemben
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e50e406e74c0b78f41830c4ea7c5b10830002ed3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72886934"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Kockázatészlelés szimulálása az identitásvédelemben

A rendszergazdák a következő elemek elérése érdekében szimulálhatják a kockázatot a környezetükben:

- Adatok feltöltése az identitásvédelem környezetben a kockázatészlelések és a biztonsági rések szimulálásával.
- Állítsa be a kockázatalapú feltételes hozzáférési házirendeket, és tesztelje ezeknek a házirendeknek a hatását.

Ez a cikk a következő kockázatészlelési típusok szimulálásának lépéseit tartalmazza:

- Névtelen IP-cím (könnyű)
- Ismeretlen bejelentkezési tulajdonságok (mérsékelt)
- Atipikus utazás (nehéz)

Más kockázatészlelések nem szimulálhatók biztonságos módon.

További információ az egyes kockázatészlelésekről a Cikkben található: [Mi a kockázat.](concept-identity-protection-risks.md)

## <a name="anonymous-ip-address"></a>Névtelen IP-cím

A következő eljárás elvégzése szükséges a következő:

- A [Tor böngésző](https://www.torproject.org/projects/torbrowser.html.en) szimulálni névtelen IP-címeket. Előfordulhat, hogy virtuális gépet kell használnia, ha a szervezet korlátozza a Tor böngésző használatát.
- Olyan tesztfiók, amely még nincs regisztrálva az Azure többtényezős hitelesítéséhez.

**Névtelen IP-címről történő bejelentkezés szimulálásához hajtsa végre az alábbi lépéseket:**

1. A [Tor böngésző](https://www.torproject.org/projects/torbrowser.html.en)segítségével [https://myapps.microsoft.com](https://myapps.microsoft.com)keresse meg a.   
2. Adja meg annak a fióknak a hitelesítő adatait, amelyet meg szeretne jelensen a **Névtelen IP-címekről szóló bejelentkezések** jelentésben.

A bejelentkezés 10–15 percen belül megjelenik az Identitásvédelem irányítópultján. 

## <a name="unfamiliar-sign-in-properties"></a>Ismeretlen bejelentkezési tulajdonságok

Ismeretlen helyek szimulálása érdekében olyan helyről és eszközről kell bejelentkeznie, ahonnan a tesztfiók korábban nem jelentkezett be.

Az alábbi eljárás egy újonnan létrehozott:

- VPN-kapcsolat, az új hely szimulálására.
- Virtuális gép, szimulálni egy új eszközt.

Az alábbi eljárás elvégzéséhez olyan felhasználói fiókot kell használnia, amely rendelkezik:

- Legalább egy 30 napos bejelentkezési előzmény.
- Az Azure többtényezős hitelesítés engedélyezve van.

**Ismeretlen helyről történő bejelentkezés szimulálásához hajtsa végre az alábbi lépéseket:**

1. Amikor bejelentkezik a tesztfiókkal, sikertelen a többtényezős hitelesítés (MFA) kihívás nem felel meg az MFA kihívás.
2. Az új VPN használatával [https://myapps.microsoft.com](https://myapps.microsoft.com) keresse meg a tesztfiók hitelesítő adatait, és adja meg azt.

A bejelentkezés 10–15 percen belül megjelenik az Identitásvédelem irányítópultján.

## <a name="atypical-travel"></a>Atipikus utazás

Az atipikus utazási feltétel szimulálása nehéz, mert az algoritmus gépi tanulást használ a hamis pozitívok kiig, például az ismerős eszközökről való atipikus utazáshoz, vagy a címtár ban lévő más felhasználók által használt VPN-ek bejelentkezéseihez. Emellett az algoritmus 14 napos bejelentkezési előzményeket és 10 bejelentkezést igényel a felhasználótól, mielőtt megkezdi a kockázatészlelést. Az összetett gépi tanulási modellek és a fenti szabályok miatt fennáll annak az esélye, hogy a következő lépések nem vezet nek kockázatészleléshez. Előfordulhat, hogy szeretné replikálni ezeket a lépéseket több Azure AD-fiókok szimulálni ezt az észlelést.

**Az atipikus utazási kockázat észlelésének szimulálásához hajtsa végre az alábbi lépéseket:**

1. A szokásos böngészőt [https://myapps.microsoft.com](https://myapps.microsoft.com)használva keresse meg a t.  
2. Adja meg annak a fióknak a hitelesítő adatait, amelyhez atipikus utazási kockázatészlelést szeretne létrehozni.
3. Módosítsa a felhasználói ügynököt. A Felhasználói ügynököt a Microsoft Edge fejlesztői eszközeivel (F12) módosíthatja.
4. Változtassa meg az IP-címét. Az IP-cím módosíthatja egy VPN, egy Tor bővítmény, vagy egy új virtuális gép létrehozása az Azure-ban egy másik adatközpontban.
5. Jelentkezzen be, [https://myapps.microsoft.com](https://myapps.microsoft.com) hogy ugyanazokat a hitelesítő adatokat használja, mint korábban és néhány percen belül az előző bejelentkezés után.

A bejelentkezés 2-4 órán belül megjelenik az Identitásvédelem irányítópultján.

## <a name="testing-risk-policies"></a>A kockázati irányelvek tesztelése

Ez a szakasz a felhasználó tesztelésének lépéseit és a Következő cikkben létrehozott bejelentkezési kockázati házirendeket ismerteti, [hogyan: A kockázati házirendek konfigurálása és engedélyezése.](howto-identity-protection-configure-risk-policies.md)

### <a name="user-risk-policy"></a>Felhasználói kockázati házirend

A felhasználói kockázat biztonsági házirendjének teszteléséhez hajtsa végre az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
1. Tallózás az **Azure Active Directory** > **biztonsági** > **áttekintése .**
1. Válassza **a Felhasználói kockázati házirend konfigurálása lehetőséget.**
   1. Hozzárendelések **csoportban**
      1. **Felhasználók** – Válassza a **Minden felhasználó** vagy A személyek és csoportok **kiválasztása lehetőséget,** ha korlátozza a bevezetést.
         1. Tetszés szerint kizárhatja a felhasználókat a házirendből.
      1. **Feltételek** - **A Felhasználó kockázata** A Microsoft ajánlása az, hogy ezt a beállítást **magasra**állítsa.
   1. A **Vezérlők csoportban**
      1. **Access** - A Microsoft javaslata a **hozzáférés engedélyezése** és **a jelszómódosítás megkövetelése**.
   1. **Házirend kényszerítése** - **ki**
   1. **Mentés** – Ez a művelet visszaadja az **Áttekintés** lapot.
1. Növelje a tesztfiók felhasználói kockázatát, például néhányszor szimulálva az egyik kockázatészlelést.
1. Várjon néhány percet, majd ellenőrizze, hogy a kockázat emelkedett-e a felhasználó számára. Ha nem, szimulálja a további kockázatészlelések a felhasználó számára.
1. Térjen vissza a kockázati szabályzathoz, és állítsa a **Házirend kényszerítése** beállítását **be-** és **mentésre.**
1. Most már tesztelheti a felhasználó kockázatalapú feltételes hozzáférés bejelentkezésével egy felhasználó egy emelt kockázati szint.

### <a name="sign-in-risk-security-policy"></a>Bejelentkezési kockázati biztonsági házirend

A bejelentkezési kockázati szabályzat teszteléséhez hajtsa végre a következő lépéseket:

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
1. Tallózás az **Azure Active Directory** > **biztonsági** > **áttekintése .**
1. Válassza **a Bejelentkezési kockázati házirend konfigurálása**lehetőséget.
   1. Hozzárendelések **csoportban**
      1. **Felhasználók** – Válassza a **Minden felhasználó** vagy A személyek és csoportok **kiválasztása lehetőséget,** ha korlátozza a bevezetést.
         1. Tetszés szerint kizárhatja a felhasználókat a házirendből.
      1. **Feltételek** - **Bejelentkezési kockázat** A Microsoft ajánlása szerint ezt a beállítást közepes és újabb **beállításra kell állítania.**
   1. A **Vezérlők csoportban**
      1. **Access** - A Microsoft javaslata a **hozzáférés engedélyezése** és **a többtényezős hitelesítés megkövetelése**.
   1. **Házirend kényszerítése** - **On**
   1. **Mentés** – Ez a művelet visszaadja az **Áttekintés** lapot.
1. Most már tesztelheti a bejelentkezési kockázatalapú feltételes hozzáférés bejelentkezésével egy kockázatos munkamenet (például a Tor böngésző használatával). 

## <a name="next-steps"></a>További lépések

- [Mi a kockázat?](concept-identity-protection-risks.md)

- [Útmutató: Kockázati házirendek konfigurálása és engedélyezése](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory identitásvédelem](overview-identity-protection.md)
