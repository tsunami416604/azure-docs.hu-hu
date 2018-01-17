---
title: "Az Azure Active Directory Identity Protection-forgatókönyv |} Microsoft Docs"
description: "Ismerje meg, az Azure AD Identity Protection miként korlátozhatja, hogy a támadó kihasználni a sérült biztonságú identitás vagy az eszköz és identitás vagy egy eszköz, amely korábban gyanús vagy megsértik ismert biztonságossá tételéhez."
services: active-directory
keywords: "az Azure active directory azonosító adatok védelmét, a cloud app discovery, alkalmazások, biztonság, kockázat, kockázati szint, biztonsági rés, biztonsági házirend kezelése"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: b76d6a31dfe600a4639b830bfbbb5cacfc158dd6
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Azure Active Directory Identity Protection playbook
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
A kockázat esemény a névtelen proxy IP-címként azonosított IP-cím sikeresen bejelentkezett felhasználók azonosítja. Ezek a proxyk szeretné az eszköz IP-címet, és a rosszindulatú behatolással szemben használható személyek által használt.

**Szimulálása a bejelentkezés egy névtelen IP-címről, a következő lépésekkel**:

1. Töltse le a [Tor böngésző](https://www.torproject.org/projects/torbrowser.html.en).
2. Keresse meg a Tor böngészővel [https://myapps.microsoft.com](https://myapps.microsoft.com).   
3. Adja meg a fiók, akkor jelenik meg a hitelesítő adatait a **névtelen IP-címekről bejelentkezések** jelentés.

A bejelentkezés jelennek meg az Identity Protection-irányítópult 5 percen belül. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Ismeretlen helyekről történt bejelentkezések
Az ismeretlen helyek kockázat egy valós idejű bejelentkezési értékelési mechanizmus, amely a korábbi bejelentkezési helyek figyelembe veszi (szélesség, IP / hosszúság és a ASN) új / ismeretlen helyek meghatározásához. A rendszer tárolja a korábbi IP-címek, szélesség / hosszúság, és a ASN-eket, hogy egy felhasználó és figyelembe veszi azokat a megszokott helyek lehet. A bejelentkezési helye akkor tekinthető ismeri, ha a bejelentkezési helye nem egyezik a meglévő ismerős helyeken.

Azure Active Directory Identity Protection:  

* kezdeti tanulási időszaka 14 nap során, ami azt nem ez a jelző bármely új helyek ismeretlen helyként.
* figyelmen kívül hagyja az ismerős eszközöket és a meglévő ismerős hely földrajzilag megközelítik helyek bejelentkezések.

Szimulálása ismeretlen helyeket, hogy jelentkezzen be egy helyről, és az eszköz, amely a fiók még nem jelentkezett be az előtt. 

**Szimulálása a bejelentkezés egy ismeretlen helyről, a következő lépésekkel**:

1. Válasszon legalább egy 14 napos bejelentkezési előzményei rendelkező fiókkal. 
2. Módszerekkel:
   
   a. A VPN-en, navigáljon [https://myapps.microsoft.com](https://myapps.microsoft.com) , és írja be a szeretné szimulálni a kockázat esemény fiók hitelesítő adatait.
   
   b. Kérje meg egy társítható, egy másik helyen is bejelentkezhet a fiók hitelesítő adatait (nem ajánlott).

A bejelentkezés jelennek meg az Identity Protection-irányítópult 5 percen belül.

### <a name="impossible-travel-to-atypical-location"></a>Lehetetlen odautazás alapján ezúttal szokatlan helyre
Lehetetlen odautazás feltétel szimulálva nem nehéz, mert az algoritmus által használt gépi tanulással például lehetetlen odautazás ismerős eszközökről vagy a virtuális magánhálózatok, hogy a címtárban más felhasználók által használt bejelentkezések hamis-tömkelegére kimenő gyomláláskor. Ezen felül az algoritmus igényel 3 – 14 nap bejelentkezési előzményeit a felhasználó kockázati események létrehozásának megkezdése előtt.

**Ezzel szimulálva egy lehetetlen odautazás alapján ezúttal szokatlan helyre, a következő lépésekkel**:

1. Keresse meg a szabványos böngészővel [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Adjon meg egy lehetetlen odautazás kockázat eseményt generál a fiók hitelesítő adatait.
3. Módosítsa a felhasználói ügynök. Módosítsa az Internet Explorer felhasználói ügynök fejlesztői eszközök, vagy módosítsa a felhasználói ügynök Firefox vagy felhasználói ügynök kapcsoló bővítményével Chrome.
4. Az IP-címének módosítása. Az IP-címe a VPN-és a Tor bővítmény használatával, vagy egy új gépet az Azure különböző adatközpont dolgozik módosíthatja.
5. Bejelentkezés az [https://myapps.microsoft.com](https://myapps.microsoft.com) hitelesítő adatokkal, mielőtt, és a korábbi bejelentkezés után néhány percen belül.

A bejelentkezés fog megjelenni az Identity Protection-irányítópult 2 – 4 órán belül.<br>
Összetett gépi tanulási modellek jár, mert az beszerzése kivétele nem mentése esély van.<br> Előfordulhat, hogy szeretné ezeket a lépéseket, a több Azure AD-fiókok replikálása.

## <a name="simulating-vulnerabilities"></a>Biztonsági rések szimulálása
Biztonsági rések egy hibás szereplő is kihasználható az Azure AD környezetben gyengeségei miatt. Jelenleg biztonsági rések 3 típusú illesztett az Azure AD Identity Protection használó egyéb szolgáltatásokat az Azure AD. A biztonsági rések jelenik meg az Identity Protection-Irányítópult automatikusan után ezek a funkciók be vannak állítva.

* Az Azure AD [többtényezős hitelesítés?](../multi-factor-authentication/multi-factor-authentication.md)
* Az Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).
* Az Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 

## <a name="user-compromise-risk"></a>Felhasználó biztonsági sérülés kockázata
**Felhasználó biztonsági sérülés kockázat, hajtsa végre a következő lépéseket**:

1. Bejelentkezés az [https://portal.azure.com](https://portal.azure.com) a bérlő globális rendszergazdai hitelesítő adatokkal.
2. Navigáljon a **Identity Protection**. 
3. A fő **Azure AD Identity Protection** panelen kattintson a **beállítások**. 
4. A a **portál beállításait** panelen, a **biztonsági szabályok**, kattintson a **felhasználó biztonsági sérülés kockázati**. 
5. A a **kockázat bejelentkezés** panelen kapcsolja **engedélyezése a szabály** ki, és kattintson a **mentése** beállításait.
6. Egy adott felhasználói fiók szimulálása egy ismeretlen helyekről vagy névtelen IP-cím kockázat esemény. Ez fogja jogosultságszint-emelés, hogy a felhasználó felhasználói kockázati szintjének **Közepes**.
7. Várjon néhány percet, és ellenőrizze, hogy felhasználói szinten, a felhasználó érték **Közepes**.
8. Lépjen a **portál beállításait** panelen.
9. A a **felhasználó biztonsági sérülés kockázati** panel alatt **engedélyezése a szabály**, jelölje be **a** . 
10. A következő lehetőségek közül:
    
    a. Blokk, jelölje be a **Közepes** alatt **blokk bejelentkezés**.
    
    b. Biztonságos jelszó módosítása megköveteléséhez **Közepes** alatt **többtényezős hitelesítést**.
11. Kattintson a **Save** (Mentés) gombra.
12. Kockázati-alapú feltételes hozzáférés egy felhasználó használata egy emelt szintű kockázati szintjét a bejelentkezéssel most tesztelheti. Ha a felhasználó kockázati közepes, attól függően, hogy a házirend konfigurációját, a bejelentkezés vagy letiltása, vagy hogy kényszerítve vannak-e a jelszó módosítása. 
    <br><br>
    ![Playbook](./media/active-directory-identityprotection-playbook/201.png "Playbook")
    <br>

## <a name="sign-in-risk"></a>Bejelentkezési kockázat
**A bejelentkezési kockázatot, hajtsa végre az alábbi lépéseket:**

1. Bejelentkezés az [https://portal.azure.com ](https://portal.azure.com) a bérlő globális rendszergazdai hitelesítő adatokkal.
2. Navigáljon a **Identity Protection**.
3. A fő **Azure AD Identity Protection** panelen kattintson a **beállítások**. 
4. Az a **portál beállításait** panelen, a **biztonsági szabályok**, kattintson a **kockázat bejelentkezés**.
5. Az a ** kockázat bejelentkezés ** panelen válassza **a** alatt **engedélyezése a szabály**. 
6. A következő lehetőségek közül:
   
   a. Letilthatja, válassza ki a **Közepes** alatt **blokk jelentkezzen be**
   
   b. Biztonságos jelszó módosítása megköveteléséhez **Közepes** alatt **többtényezős hitelesítést**.
7. Blokkolja, válassza ki a közepes blokk bejelentkezés alatt.
8. Többtényezős hitelesítés megköveteléséhez **Közepes** alatt **többtényezős hitelesítést**.
9. Kattintson a **Mentés** gombra.
10. Az ismeretlen helyek szimulál most tesztelheti kockázat-alapú feltételes hozzáférés, vagy névtelen IP kockázati események, mivel azok is **Közepes** események kockázatát.


![Playbook](./media/active-directory-identityprotection-playbook/200.png "Playbook")


## <a name="see-also"></a>Lásd még
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

