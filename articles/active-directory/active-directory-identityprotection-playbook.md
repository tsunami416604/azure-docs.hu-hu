---
title: Az Azure Active Directory Identity Protection-forgatókönyv |} Microsoft Docs
description: Ismerje meg, az Azure AD Identity Protection miként korlátozhatja, hogy a támadó kihasználni a sérült biztonságú identitás vagy az eszköz és identitás vagy egy eszköz, amely korábban gyanús vagy megsértik ismert biztonságossá tételéhez.
services: active-directory
keywords: az Azure active directory azonosító adatok védelmét, a cloud app discovery, alkalmazások, biztonság, kockázat, kockázati szint, biztonsági rés, biztonsági házirend kezelése
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 210d097f0719725a0ecf145ce536875a383b04e6
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Az Azure Active Directory Identity Protection-forgatókönyv

Ez a forgatókönyv segítséget:

* Az Identity Protection környezetben adatok feltöltése a szimuláció kockázati eseményekről és biztonsági rések
* Kockázati-alapú feltételes hozzáférés szabályzatainak beállítása, és ezek a házirendek hatásának tesztelése


## <a name="simulating-risk-events"></a>Kockázati események szimulálása

Ez a szakasz biztosít lépéseket a következő kockázatok eseménytípusok szimulálva:

* Bejelentkezések névtelen IP-címről (egyszerű)
* Bejelentkezések ismeretlen helyekről (közepes)
* Lehetetlen odautazás bejelentkezés szokatlan helyekről (nehéz)

Más kockázati események nem szimulált, biztonságos módon.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Névtelen IP-címről történő bejelentkezések

A kockázat esemény kapcsolatos további információkért lásd: [névtelen IP-címekről bejelentkezések](active-directory-reporting-risk-events.md#sign-ins-from-anonymous-ip-addresses). 

A következő eljárás olyan kell használnia:

- A [Tor böngésző](https://www.torproject.org/projects/torbrowser.html.en) névtelen IP-címek szimulálásához. Szükség lehet a virtuális gép használja, ha a szervezet korlátozza a Tor böngészővel.
- Egy olyan fiókot, amely még nincs regisztrálva a többtényezős hitelesítés.

**Szimulálása a bejelentkezés egy névtelen IP-címről, a következő lépésekkel**:

1. Használja a [Tor böngésző](https://www.torproject.org/projects/torbrowser.html.en), navigáljon a [ https://myapps.microsoft.com ](https://myapps.microsoft.com).   
2. Adja meg a fiók, akkor jelenik meg a hitelesítő adatait a **névtelen IP-címekről bejelentkezések** jelentés.

A bejelentkezés mutatja az Identity Protection-irányítópult 10 – 15 percen belül. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Ismeretlen helyekről történt bejelentkezések

A kockázat esemény kapcsolatos további információkért lásd: [bejelentkezések ismeretlen helyekről](active-directory-reporting-risk-events.md#sign-in-from-unfamiliar-locations). 

Ismeretlen helyek szimulálása, akkor jelentkezzen be egy helyről, és az eszköz teszt fiókja rendelkezik nem jelentkezett be az előtt.

Az alábbi eljárást használja egy újonnan létrehozott:

- VPN-kapcsolat, az új helyre szimulálásához.

- Virtuális gép, ezzel szimulálva egy új eszközt.

A következő eljárás szükséges, hogy egy felhasználói fiókot használjon, amely rendelkezik:

- Legalább egy 30 napos bejelentkezési előzményei.
- Többtényezős hitelesítés engedélyezve van.


**Szimulálása a bejelentkezés egy ismeretlen helyről, a következő lépésekkel**:

1. Bejelentkezés a fiókot, ha sikertelen az MFA-kérdést az MFA-kérdést nem átadásával.
2. Keresse meg az új VPN-kapcsolattal [ https://myapps.microsoft.com ](https://myapps.microsoft.com) , és adja meg a teszt fiók hitelesítő adatait.
   

A bejelentkezés mutatja az Identity Protection-irányítópult 10 – 15 percen belül.

### <a name="impossible-travel-to-atypical-location"></a>Lehetetlen odautazás alapján ezúttal szokatlan helyre

A kockázat esemény kapcsolatos további információkért lásd: [Impossible utazik, és a hely nem típusos](active-directory-reporting-risk-events.md#impossible-travel-to-atypical-locations). 

Lehetetlen odautazás feltétel szimulálva nem nehéz, mert az algoritmus által használt gépi tanulással például lehetetlen odautazás ismerős eszközökről vagy a virtuális magánhálózatok, hogy a címtárban más felhasználók által használt bejelentkezések hamis-tömkelegére kimenő gyomláláskor. Ezen felül az algoritmus igényel 14 napos bejelentkezési előzményeit és a felhasználó 10 bejelentkezések kockázati események létrehozásának megkezdése előtt. Az összetett gépi tanulási modellek miatt, és a fenti szabályok esély van, amely a következő lépéseket a kockázati események nem vezet. Előfordulhat, hogy szeretné ezeket a lépéseket a kockázat esemény közzététele több Azure AD-fiókok replikálása.


**Ezzel szimulálva egy lehetetlen odautazás alapján ezúttal szokatlan helyre, a következő lépésekkel**:

1. Keresse meg a szabványos böngészővel [ https://myapps.microsoft.com ](https://myapps.microsoft.com).  
2. Adjon meg egy lehetetlen odautazás kockázat eseményt generál a fiók hitelesítő adatait.
3. Módosítsa a felhasználói ügynök. Módosítsa az Internet Explorer felhasználói ügynök fejlesztői eszközök, vagy módosítsa a felhasználói ügynök Firefox vagy felhasználói ügynök kapcsoló bővítményével Chrome.
4. Az IP-címének módosítása. Az IP-címe a VPN-és a Tor bővítmény használatával, vagy egy új gépet az Azure különböző adatközpont dolgozik módosíthatja.
5. Bejelentkezés az [ https://myapps.microsoft.com ](https://myapps.microsoft.com) hitelesítő adatokkal, mielőtt, és a korábbi bejelentkezés után néhány percen belül.

A bejelentkezés megjelennek az Identity Protection-irányítópult 2 – 4 órán belül.

## <a name="simulating-vulnerabilities"></a>Biztonsági rések szimulálása
Biztonsági rések egy hibás szereplő is kihasználható az Azure AD környezetben gyengeségei miatt. Jelenleg biztonsági rések 3 típusú illesztett az Azure AD Identity Protection használó egyéb szolgáltatásokat az Azure AD. A biztonsági rések jelenik meg az Identity Protection-Irányítópult automatikusan után ezek a funkciók be vannak állítva.

* Az Azure AD [többtényezős hitelesítés](authentication/multi-factor-authentication.md)
* Az Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).
* Az Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 


## <a name="testing-security-policies"></a>Biztonsági házirendek tesztelése

Ez a rész nyújt lépéseket a felhasználó kockázat és a bejelentkezési kockázat biztonsági házirend teszteléséhez.


### <a name="user-risk-security-policy"></a>Felhasználói kockázat biztonsági házirend

További információkért lásd: [felhasználói kockázat biztonsági házirend](active-directory-identityprotection.md#user-risk-security-policy).

![Felhasználói kockázati](./media/active-directory-identityprotection-playbook/02.png "forgatókönyv")


**Egy felhasználói kockázat biztonsági házirend, hajtsa végre a következő lépéseket**:

1. Bejelentkezés az [ https://portal.azure.com ](https://portal.azure.com) a bérlő globális rendszergazdai hitelesítő adatokkal.
2. Navigáljon a **Identity Protection**. 
3. Az a **Azure AD Identity Protection** kattintson **felhasználói kockázat házirendnek**.
4. Az a **hozzárendelések** területen válassza ki a kívánt felhasználók (és a csoportok) és a felhasználó kockázati szintjét.

    ![Felhasználói kockázati](./media/active-directory-identityprotection-playbook/03.png "forgatókönyv")

5. A vezérlők területen válassza ki a kívánt hozzáférés-vezérlés (pl. a jelszó módosítása szükséges).
5. Mint **házirend kényszerítése**, jelölje be **ki**.
6. Jogosultságszint-emelés egy olyan fiókot, amelyet a felhasználó kockázatát például szimulálva a kockázati események közül néhány alkalommal.
7. Várjon néhány percet, és ellenőrizze, hogy a felhasználó felhasználói szinten-e a közepes. Ha nem, a felhasználó további kockázati események szimulálása.
8. Mint **házirend kényszerítése**, jelölje be **a**.
9. Felhasználói kockázat-alapú feltételes hozzáférés egy felhasználó használata egy emelt szintű kockázati szintjét a bejelentkezéssel most tesztelheti.
    
    

### <a name="sign-in-risk-security-policy"></a>Bejelentkezési kockázat biztonsági házirend

További információkért lásd: [felhasználói kockázat biztonsági házirend](active-directory-identityprotection.md#user-risk-security-policy).

![Bejelentkezési kockázati](./media/active-directory-identityprotection-playbook/01.png "forgatókönyv")


**A bejelentkezési a kockázat házirendnek, hajtsa végre az alábbi lépéseket:**

1. Bejelentkezés az [ https://portal.azure.com ](https://portal.azure.com) a bérlő globális rendszergazdai hitelesítő adatokkal.

2. Navigáljon a **Azure AD Identity Protection**.

3. A fő **Azure AD Identity Protection** kattintson **bejelentkezési kockázat házirendnek**. 

4. Az a **hozzárendelések** szakaszban válassza ki a kívánt felhasználók (és a csoportok), bejelentkezési kockázati szint.

    ![Bejelentkezési kockázati](./media/active-directory-identityprotection-playbook/04.png "forgatókönyv")


5. Az a **vezérlők** területen válassza ki a kívánt hozzáférés-vezérlés (például **többtényezős hitelesítést**). 

6. Mint **házirend kényszerítése**, jelölje be **a**.

7. Kattintson a **Save** (Mentés) gombra.

8. Most tesztelheti bejelentkezés kockázat-alapú feltételes hozzáférés a bejelentkezéssel kockázatos munkamenet használatával (például úgy, hogy a Tor böngésző használatával). 

 




## <a name="see-also"></a>Lásd még

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

