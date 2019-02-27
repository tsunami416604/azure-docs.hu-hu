---
title: Az Azure Active Directory Identity Protection-forgatókönyv |} A Microsoft Docs
description: Ismerje meg, hogyan Azure AD Identity Protection segítségével is korlátozhatja, hogy a támadó biztonsági rés kiaknázása elleni egy sérült biztonságú identitás vagy egy eszközt, és biztonságos identitás vagy egy eszköz, amely korábban gyanús vagy ismert legyen feltörni.
services: active-directory
keywords: az Azure active directory identity protection, a cloud discovery, alkalmazások, biztonság, kockázati, kockázati szint, biztonsági rést, biztonsági házirend kezelése
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: markvi
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc2f2f90029918955f00771acaac10b3cf5f4416
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56868272"
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Az Azure Active Directory Identity Protection-forgatókönyv

Ez a forgatókönyv segítséget:

* Töltse ki az adatokat az Identity Protection környezetben szimuláló kockázati eseményekről és biztonsági rések szerint
* Állítsa be a kockázatalapú feltételes hozzáférési szabályzatokat, és ezek a házirendek hatásának tesztelése


## <a name="simulating-risk-events"></a>Kockázati események szimulálása

Ebben a szakaszban részletes lépéseket a következő kockázati események típusai számos:

* Névtelen IP-címek (egyszerű) történő bejelentkezések
* Bejelentkezések ismeretlen helyekről (közepes)
* Bejelentkezés szokatlan helyekről (nehéz)

Nem lehet szimulált más kockázati események biztonságos módon.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Bejelentkezések névtelen IP-címről

A kockázati esemény kapcsolatos további információkért lásd: [névtelen IP-címekről történő bejelentkezések](../reports-monitoring/concept-risk-events.md#sign-ins-from-anonymous-ip-addresses). 

A következő eljárás végrehajtásával előírja, hogy:

- A [Tor böngésző](https://www.torproject.org/projects/torbrowser.html.en) névtelen IP-címek szimulálásához. Szüksége lehet egy virtuális gép használja, ha a szervezet korlátozza a Tor böngészővel.
- Egy olyan fiókot, amely még nem regisztrált a multi-factor Authentication hitelesítéshez.

**Szimulálására a bejelentkezés névtelen IP-, a következő lépésekkel**:

1. Használatával a [Tor böngésző](https://www.torproject.org/projects/torbrowser.html.en), navigáljon a [ https://myapps.microsoft.com ](https://myapps.microsoft.com).   
2. Adja meg a megjeleníteni kívánt a fiók hitelesítő adatait a **névtelen IP-címekről történő bejelentkezések** jelentést.

A bejelentkezési megjelenik-e az Identity Protection irányítópulton 10 – 15 percen belül. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Bejelentkezések ismeretlen helyekről

A kockázati esemény kapcsolatos további információkért lásd: [ismeretlen helyről történő bejelentkezések](../reports-monitoring/concept-risk-events.md#sign-in-from-unfamiliar-locations). 

Ismeretlen helyről a szimulálásához kell egy helyet és egy eszköz a tesztfiók nem jelentkezett be, mielőtt jelentkezhessen be.

Az alábbi eljárás egy újonnan létrehozott használja:

- VPN-kapcsolat, az új helyet szimulálása.

- A virtuális gép, egy új eszköz szimulálásához.

A következő eljárás végrehajtásával előírja, hogy egy felhasználói fiókot, amely rendelkezik:

- Legalább egy 30 napos bejelentkezési előzményeit.
- Többtényezős hitelesítés engedélyezve van.


**Szimulálására a bejelentkezés ismeretlen helyről, a következő lépésekkel**:

1. Ha a teszt fiókkal történő bejelentkezés, átadja az MFA-hitelesítést nem adja át az MFA-hitelesítést.
2. Az új VPN használatával keresse meg a [ https://myapps.microsoft.com ](https://myapps.microsoft.com) , és adja meg a teszt-fiókjának hitelesítő adatait.
   

A bejelentkezési megjelenik-e az Identity Protection irányítópulton 10 – 15 percen belül.

### <a name="impossible-travel-to-atypical-location"></a>Szokásostól eltérő helyre való lehetetlen utazás

A kockázati esemény kapcsolatos további információkért lásd: [lehetetlen utazás a szokásostól eltérő helyre való](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations). 

Az algoritmus által használt gépi tanulás, például lehetetlen utazás a jól ismert eszközök, vagy a könyvtárban más felhasználók által használt VPN-eket történő bejelentkezések vakriasztások gyomláláskor szimulálására a lehetetlen utazás feltétel azért bonyolult. Ezenkívül az algoritmus szükséges 14 nap bejelentkezési előzményeit, és 10 bejelentkezések a felhasználó kockázati eseményeket megkezdése előtt. Miatt az összetett gépi tanulási modelleket, és a fenti szabályok van arra, hogy az alábbi lépéseket egy kockázati esemény nem vezet. Előfordulhat, hogy szeretné replikálni a kockázati esemény közzététele több Azure AD-felhasználók ezeket a lépéseket.


**A szokásostól eltérő helyre való lehetetlen utazás szimulálása, a következő lépésekkel**:

1. A szokásos böngésző használatával keresse meg a [ https://myapps.microsoft.com ](https://myapps.microsoft.com).  
2. Adja meg a lehetetlen utazás kockázati esemény létrehozásához használni kívánt fiók hitelesítő adatait.
3. Módosítsa a felhasználói ügynök. Az Internet Explorer felhasználói ügynök módosíthatja a fejlesztői eszközök, vagy módosítsa a felhasználói ügynök, a Firefox vagy a Chrome, a felhasználói ügynök környezetválasztó bővítmény használatával.
4. Módosítsa az IP-címet. Az IP-cím a VPN, egy Tor-bővítmény használatával, vagy egy új gépet az Azure-ban, egy másik adatközpontban felpörög módosíthatja.
5. Jelentkezzen be [ https://myapps.microsoft.com ](https://myapps.microsoft.com) előtt és után a korábbi bejelentkezési néhány percen belül hitelesítő adatokkal.

A bejelentkezési megjelenik-e az Identity Protection irányítópultjának 2 – 4 órán belül.

## <a name="simulating-vulnerabilities"></a>Biztonsági rések kiváltása
Biztonsági rések gyengeségek kihasználhatnák azokat kiderítsék Azure ad-ben környezetben. Jelenleg biztonsági rések 3 típusú illesztett az Azure AD Identity Protection, az Azure ad-ben más funkcióit. A biztonsági rések megjelenik az Identity Protection irányítópultjának automatikusan után ezek a funkciók be vannak-e beállítva.

* Az Azure AD [többtényezős hitelesítés](../authentication/multi-factor-authentication.md)
* Az Azure AD [a Cloud Discovery](https://docs.microsoft.com/cloud-app-security/).
* Az Azure AD [Privileged Identity Management](../privileged-identity-management/pim-configure.md). 


## <a name="testing-security-policies"></a>Biztonsági szabályzatok tesztelése

Ez a szakasz biztosít lépéseket a felhasználói kockázat és bejelentkezési kockázat biztonsági házirend teszteléséhez.


### <a name="user-risk-security-policy"></a>Felhasználói kockázati biztonsági házirend

További információ: [A felhasználó kockázati szabályzat konfigurálása](howto-user-risk-policy.md).

![Felhasználói kockázat](./media/playbook/02.png "forgatókönyv")


**Felhasználói kockázati biztonsági házirendet, hajtsa végre az alábbi lépéseket**:

1. Jelentkezzen be [ https://portal.azure.com ](https://portal.azure.com) a bérlőhöz tartozó globális rendszergazdai hitelesítő adatokkal.
2. Navigáljon a **Identity Protection**. 
3. Az a **Azure AD Identity Protection** kattintson **felhasználói kockázati házirend**.
4. Az a **hozzárendelések** területen válassza ki a kívánt felhasználókat (és csoportokat) és a felhasználói kockázat szintje.

    ![Felhasználói kockázat](./media/playbook/03.png "forgatókönyv")

5. A vezérlők területen válassza ki a kívánt hozzáférés-vezérlés (pl. a jelszómódosítás megkövetelése).
5. Mint **szabályzat kényszerítése**válassza **ki**.
6. Jogosultságszintjének például egy olyan fiókot, amelyet a felhasználó kockázatát szimulálására a kockázati események közül néhány alkalommal.
7. Várjon néhány percet, és ellenőrizze, hogy a felhasználó felhasználói szinten: Közepes. Ha nem, a felhasználó további kockázati események szimulálása.
8. Mint **szabályzat kényszerítése**válassza **a**.
9. Most tesztelheti a felhasználói kockázatalapú feltételes hozzáférés a felhasználó használata az emelt szintű kockázati szint bejelentkezéssel.
    
    

### <a name="sign-in-risk-security-policy"></a>Biztonsági házirend bejelentkezési kockázat

További információ: [A bejelentkezési kockázati szabályzat konfigurálása](howto-sign-in-risk-policy.md).

![Bejelentkezési kockázati](./media/playbook/01.png "forgatókönyv")


**Bejelentkezési kockázati házirend teszteléséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be [ https://portal.azure.com ](https://portal.azure.com) a bérlőhöz tartozó globális rendszergazdai hitelesítő adatokkal.

2. Navigáljon a **az Azure AD Identity Protection**.

3. A fő **Azure AD Identity Protection** kattintson **bejelentkezési kockázati házirend**. 

4. Az a **hozzárendelések** részen válassza ki a kívánt felhasználókat (és csoportokat), és bejelentkezési kockázati szint.

    ![Bejelentkezési kockázati](./media/playbook/04.png "forgatókönyv")


5. Az a **vezérlők** területen válassza ki a kívánt hozzáférés-vezérlés (például **többtényezős hitelesítés megkövetelése**). 

6. Mint **szabályzat kényszerítése**válassza **a**.

7. Kattintson a **Save** (Mentés) gombra.

8. Most tesztelheti bejelentkezési kockázatalapú feltételes hozzáférés a bejelentkezéssel kockázatos munkamenet használatával (például úgy, hogy a Tor böngésző használatával). 

 




## <a name="see-also"></a>Lásd még

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

