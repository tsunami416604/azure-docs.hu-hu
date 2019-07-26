---
title: Azure Active Directory Identity Protection forgatókönyv | Microsoft Docs
description: Ismerje meg, hogy Azure AD Identity Protection lehetővé teszi a támadók számára a feltört identitások vagy eszközök kihasználása, illetve az olyan identitások vagy eszközök biztonságossá tételét, amelyekről korábban gyanúba esett vagy ismertek.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 273a6aca2050676650b955ec078b47b2ffcfe319
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333933"
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Azure Active Directory Identity Protection forgatókönyv

Ez a forgatókönyv a következőket teszi lehetővé:

* Adatok feltöltése az Identity Protection-környezetben a kockázati események és a biztonsági rések szimulálása révén
* Kockázatalapú feltételes hozzáférési szabályzatok beállítása és a házirendek hatásának tesztelése

## <a name="simulating-risk-events"></a>Kockázati események szimulálása

Ez a szakasz a következő kockázati események típusának szimulálása lépéseit tartalmazza:

* Névtelen IP-címekről érkező bejelentkezések (egyszerű)
* Ismeretlen helyekről érkező bejelentkezések (mérsékelt)
* Nem lehet utazni az atipikus helyszínekre (nehéz)

Más kockázati eseményeket nem lehet biztonságos módon szimulálni.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Névtelen IP-címről történő bejelentkezések

További információ erről a kockázati eseményről: [bejelentkezések névtelen IP-címekről](../reports-monitoring/concept-risk-events.md#sign-ins-from-anonymous-ip-addresses). 

Az alábbi eljárás végrehajtásához a következőket kell használnia:

- A [Tor böngésző](https://www.torproject.org/projects/torbrowser.html.en) névtelen IP-címeket szimulál. Előfordulhat, hogy egy virtuális gépet kell használnia, ha a szervezet a Tor böngésző használatával korlátozza a használatát.
- Olyan tesztelési fiók, amely még nincs regisztrálva a többtényezős hitelesítéshez.

**Egy névtelen IP-címről történő bejelentkezés szimulálása érdekében hajtsa végre a következő lépéseket**:

1. A [Tor-böngésző](https://www.torproject.org/projects/torbrowser.html.en)használatával navigáljon [https://myapps.microsoft.com](https://myapps.microsoft.com)a következőhöz:.   
2. Adja meg annak a fióknak a hitelesítő adatait, amelyet a **Névtelen IP-címek** jelentésből származó bejelentkezésekhez szeretne megjeleníteni.

A bejelentkezés az Identity Protection-irányítópulton 10-15 percen belül megjelenik. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Ismeretlen helyekről történt bejelentkezések

További információ erről a kockázati eseményről: [ismeretlen helyekről érkező bejelentkezések](../reports-monitoring/concept-risk-events.md#sign-in-from-unfamiliar-locations). 

Ismeretlen helyek szimulálásához be kell jelentkeznie egy helyről, és az eszközön a teszt fiók nem jelentkezett be korábban.

Az alábbi eljárás egy újonnan létrehozott műveletet használ:

- VPN-kapcsolat, hogy szimulálja az új helyet.
- Virtuális gép, amely új eszközt szimulál.

Az alábbi eljárás végrehajtásához olyan felhasználói fiókot kell használnia, amely a következő:

- Legalább egy 30 napos bejelentkezési előzmény.
- A többtényezős hitelesítés engedélyezve van.

**Ha a bejelentkezést ismeretlen helyről szeretné szimulálni, hajtsa végre a következő lépéseket**:

1. Ha a teszt fiókjával jelentkezik be, az MFA-kérdés nem kerül át az MFA-kihívásba.
2. Az új VPN használatával lépjen [https://myapps.microsoft.com](https://myapps.microsoft.com) a be, és adja meg a teszt fiókjának hitelesítő adatait.

A bejelentkezés az Identity Protection-irányítópulton 10-15 percen belül megjelenik.

### <a name="impossible-travel-to-atypical-location"></a>Nem lehet utazni szokatlan helyre

További információ erről a kockázati eseményről: [lehetetlen utazás szokatlan helyre](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations). 

A lehetetlen utazási feltétel szimulálása nehézkes, mert az algoritmus gépi tanulást használ a hamis pozitív állapotok kiszűrésére, például az ismerős eszközökről való lehetetlen utazásra vagy a címtárban lévő más felhasználók által használt VPN-ről való bejelentkezésre. Emellett az algoritmushoz a felhasználó 14 napos bejelentkezési előzményeit és 10 bejelentkezést kell megadnia, mielőtt megkezdené a kockázati események generálását. Az összetett gépi tanulási modellek és a fenti szabályok miatt előfordulhat, hogy a következő lépések nem eredményeznek kockázati eseményt. Előfordulhat, hogy ezeket a lépéseket több Azure AD-fiók esetében is replikálni szeretné a kockázati esemény közzétételéhez.

Ha nem szeretné szimulálni a **lehetetlen utazást atipikus helyre, hajtsa végre a következő lépéseket**:

1. A normál böngészővel nyissa meg [https://myapps.microsoft.com](https://myapps.microsoft.com)a következőt:.  
2. Adja meg annak a fióknak a hitelesítő adatait, amelyhez lehetetlen utazási kockázati eseményt kíván készíteni.
3. Módosítsa a felhasználói ügynököt. Megváltoztathatja a felhasználói ügynököt az Internet Explorerben Fejlesztői eszközök, vagy megváltoztathatja a felhasználói ügynököt a Firefoxban vagy a Chrome-ban egy felhasználói ügynök kapcsolójának használatával.
4. Módosítsa az IP-címet. Az IP-címet VPN, Tor-bővítmény használatával vagy egy másik adatközpontban az Azure-ban egy új gép felpörgetésével módosíthatja.
5. Jelentkezzen be, [https://myapps.microsoft.com](https://myapps.microsoft.com) hogy ugyanazokat a hitelesítő adatokat használja, mint a korábbi és az előző bejelentkezést követő néhány percen belül.

A bejelentkezés 2-4 órán belül megjelenik az Identity Protection irányítópultján.

## <a name="simulating-vulnerabilities"></a>Biztonsági rések szimulálása
A biztonsági rések olyan Azure AD-környezetbeli gyengeségek, amelyeket rossz színészek használhatnak fel. A biztonsági rések jelenleg 3 fajta felületét Azure AD Identity Protection, amelyek az Azure AD egyéb funkcióit használják. Ezek a biztonsági rések automatikusan megjelennek az Identity Protection irányítópultján a szolgáltatások beállítása után.

* Azure AD [multi-Factor Authentication](../authentication/multi-factor-authentication.md)
* Azure AD- [Cloud Discovery](https://docs.microsoft.com/cloud-app-security/).
* Azure AD- [Privileged Identity Management](../privileged-identity-management/pim-configure.md). 

## <a name="testing-security-policies"></a>Biztonsági házirendek tesztelése

Ez a szakasz a felhasználói kockázat és a bejelentkezési kockázat biztonsági házirendjének teszteléséhez nyújt útmutatást.

### <a name="user-risk-security-policy"></a>Felhasználói kockázat biztonsági házirendje

További információ: [A felhasználó kockázati szabályzat konfigurálása](howto-user-risk-policy.md).

![Felhasználói kockázat](./media/playbook/02.png "") Forgatókönyv

A **felhasználói kockázatokra vonatkozó biztonsági házirend teszteléséhez hajtsa végre a következő lépéseket**:

1. [https://portal.azure.com](https://portal.azure.com) Jelentkezzen be a bérlő globális rendszergazdai hitelesítő adataival.
2. Navigáljon az **Identity Protection**szolgáltatáshoz. 
3. A **Azure ad Identity Protection** lapon kattintson a **felhasználói kockázati házirend**elemre.
4. A **hozzárendelések** szakaszban válassza ki a kívánt felhasználókat (és csoportokat) és a felhasználói kockázati szintet.

    ![Felhasználói kockázat](./media/playbook/03.png "") Forgatókönyv

5. A vezérlők szakaszban válassza ki a kívánt hozzáférés-vezérlést (például jelszó megkövetelése).
5. A **szabályzat érvénybe léptetéséhez**válassza a **ki**lehetőséget.
6. A tesztelési fiók felhasználói kockázatának megemelése például a kockázati események egyikének szimulálása néhányszor.
7. Várjon néhány percet, majd ellenőrizze, hogy a felhasználó felhasználói szintje közepes-e. Ha nem, Szimuláljon több kockázati eseményt a felhasználó számára.
8. A **szabályzat érvénybe léptetéséhez**válassza **a**be lehetőséget.
9. Most már tesztelheti a felhasználó kockázat alapú feltételes hozzáférését úgy, hogy bejelentkezik egy emelt szintű kockázati szinttel rendelkező felhasználó használatával.

### <a name="sign-in-risk-security-policy"></a>Bejelentkezési kockázatokra vonatkozó biztonsági házirend

További információ: [A bejelentkezési kockázati szabályzat konfigurálása](howto-sign-in-risk-policy.md).

![Bejelentkezési kockázat](./media/playbook/01.png "") Forgatókönyv

**A bejelentkezési kockázati szabályzat teszteléséhez hajtsa végre a következő lépéseket:**

1. [https://portal.azure.com](https://portal.azure.com) Jelentkezzen be a bérlő globális rendszergazdai hitelesítő adataival.
2. Navigáljon **Azure ad Identity Protection**.
3. A fő **Azure ad Identity Protection** lapon kattintson a **bejelentkezési kockázati házirend**elemre. 
4. A **hozzárendelések** szakaszban válassza ki a kívánt felhasználókat (és csoportokat) és a bejelentkezési kockázati szintet.

    ![Bejelentkezési kockázat](./media/playbook/04.png "") Forgatókönyv

5. A **vezérlők** szakaszban válassza ki a kívánt hozzáférés-vezérlést (például többtényezős **hitelesítés**megkövetelése). 
6. A **szabályzat érvénybe léptetéséhez**válassza **a**be lehetőséget.
7. Kattintson a **Save** (Mentés) gombra.
8. A bejelentkezési kockázat alapú feltételes hozzáférést mostantól kockázatos munkamenet használatával is tesztelheti (például a Tor böngésző használatával). 

## <a name="see-also"></a>Lásd még

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
