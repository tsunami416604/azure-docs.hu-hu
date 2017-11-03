---
title: "Nem találja az Azure Active Directoryban a személyes alkalmazások portállal |} Microsoft Docs"
description: "Gyakori feladatok végrehajtása, ha a hozzáférési panel dolgozunk annak."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: 9e2f8aa6ad7534ff822907285aa9fe290a4db586
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2017
---
# <a name="do-you-need-help-with-the-my-apps-portal"></a>Van szüksége a saját alkalmazások portál számára?

Valószínűleg elérte a ezen a lapon, mert sajnos futtatása során a problémát a személyes alkalmazások portál használata. Amíg vannak esetek, amelyek igénylik, hogy forduljon az ügyfélszolgálathoz vagy a rendszergazdát a probléma megoldódott, az alábbiakban néhány hibaelhárítási témakörök, amelyek segítségével, először is.

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>Problémáim vannak az alkalmazások saját portál bejelentkezni

Általános problémák kereséséhez:

- Ellenőrizze, hogy ha jelentkezik be a helyes URL-címet: [https://myapps.microsoft.com](https://myapps.microsoft.com)

- Próbálkozzon a böngésző megbízható helyek ad hozzá az URL-címet.

- Győződjön meg arról, hogy a jelszó nem lejárt vagy elfelejtett. Ellenőrizze [Itt](active-directory-passwords-update-your-own-password.md) a további részletekért frissítse a jelszavát.

- Ellenőrizze, hogy van-e a hitelesítés kapcsolattartási adatai és nem blokkolja-e a hozzáférés. Ellenőrizze [Itt](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user) a hitelesítési adatok beállításával kapcsolatos további részletekért.

- Törölje a böngésző cookie-kat, és próbálja meg újból bejelentkezni.

Ha továbbra is problémák adódtak a bejelentkezés közben, a rendszergazdától további segítségért.


## <a name="how-do-i-update-my-password"></a>Hogyan frissíthetők a jelszó?

Ha elfelejtette a jelszavát, soha nem kapott az informatikai munkatársak közül, zárolja a fiókot, vagy szeretné módosítani, lásd: [segítségre van szüksége, az Azure AD-jelszó elfelejtettem](active-directory-passwords-update-your-own-password.md) további részleteket.

## <a name="how-do-i-register-for-password-reset"></a>Hogyan regisztrálhatnak a jelszóváltoztatásra?

Végfelhasználói új jelszót, vagy anélkül, hogy az önkiszolgáló jelszó-változtatási (SSPR) használó személy kommunikáljanak a fiók zárolását kívánja feloldani. Mielőtt használhatná ezt a funkciót, hitelesítési módszereket kell regisztrálnia, vagy jóvá kell hagynia a rendszergazda által betöltött előre definiált hitelesítési módszereket. További részletekért lásd: [az önkiszolgáló jelszó-változtatási regisztrációs](active-directory-passwords-reset-register.md).


## <a name="i-am-having-trouble-installing-the-my-apps-portal-browser-extension"></a>Problémáim vannak az alkalmazások saját portál bővítmény telepítése

Ellenőrizze, hogy ha teljesíti-böngészőre vonatkozó követelményei:

- A portálhoz szükséges, amely támogatja a JavaScript egy böngészőt, és CSS engedélyezte. Ha jelszóalapú egyszeri bejelentkezés alkalmazásokat használ, a hozzá tartozó bővítmény is telepíteni kell. A bővítmény automatikusan letöltődik jelszó-alapú egyszeri bejelentkezés alkalmazásokhoz konfigurált alkalmazás elindítása.

- A bővítmény a böngészőre vonatkozó követelményei a következők:
    - Internet Explorer 8, 9, 10, 11, Windows 7 vagy újabb
    - Peremhálózati Windows 10 évforduló Edition vagy újabb
    - A Windows 7 vagy újabb, és MacOS X rendszeren vagy újabb Chrome
    - Firefox 26.0 vagy újabb, Windows XP SP2 vagy újabb verzióját, és a Mac OS X 10.6 vagy újabb

Az alábbi közvetlen hivatkozásokat a Chrome és peremhálózati is letölthető a bővítmény:

- [Chrome-bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

- [Peremhálózati bővítmény](https://www.microsoft.com/store/apps/9pc9sckkzk84)

Miután a telepítés kövesse az alábbi lépéseket, ha a probléma áll kapcsolatban:

- Ellenőrizze, hogy a böngésző bővítmény beállításait az, hogy a bővítmény engedélyezve van-e.

- Indítsa újra a böngészőt, és jelentkezzen be a saját alkalmazások portálra.

- Törölje a böngésző cookie-kat, és jelentkezzen be a saját alkalmazások portálra.

## <a name="how-do-i-add-a-new-app"></a>Hogyan vehetek fel egy új alkalmazást?

1.  Az a **alkalmazások** kattintson **alkalmazás hozzáadása**.

2.  A kívánt alkalmazást, adja hozzá, és kattintson a Keresés **Hozzáadás**.

**Megjegyzés:**

- Ha a rendszergazda engedélyezte ezt a fiókot csak rendelkezik hozzáféréssel a beállításhoz.

- Ha az alkalmazás engedélyre van szüksége, szükség lehet megvárja a rendszergazdai jóváhagyást.


## <a name="how-do-i-manage-my-group-memberships"></a>Hogyan kezelhetem a csoporttagságok?

1. Kattintson a csoportok alkalmazás csempéjére. 
2. Hozzon létre egy csoportot, a csoportok saját, kattintson a csoport létrehozása, és kövesse az utasításokat.
3. Csatlakozás a csoporthoz, a csoportok vagyok, kattintson a csatlakozás a csoporthoz, és kövesse az utasításokat.

**Megjegyzés:**

- Ha a rendszergazda engedélyezte ezt a fiókot csak rendelkezik hozzáféréssel a beállításhoz.

- Csoportokat, amelyeket Ön tagja lehetővé teszik a részletek megtekintéséhez és a csoport elhagyására.

- Csoportokat, amelyeket Ön a tulajdonosa adja hozzá vagy tagok eltávolítása és a csoport elhagyására teszi lehetővé, a részletek megtekintéséhez.


## <a name="next-steps"></a>Következő lépések

Kapcsolódó információk, tekintse meg [az alkalmazás hozzáférési panel webhelyen vagy a mobilalkalmazás problémák](active-directory-application-access-panel-content-map.md)

