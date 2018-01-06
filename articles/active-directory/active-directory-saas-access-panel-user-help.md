---
title: "Nem találja az Azure Active Directoryban a személyes alkalmazások portállal |} Microsoft Docs"
description: "Gyakori feladatok végrehajtása, ha a hozzáférési panel dolgozunk annak."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: 7a7a5d04c55adc33db5ccce761efd622935acefb
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2018
---
# <a name="do-you-need-help-with-the-my-apps-portal"></a>Van szüksége a saját alkalmazások portál számára?

Valószínűleg elérte a ezen a lapon, mert sajnos futtatása során a problémát a személyes alkalmazások portál használata. Amíg vannak esetek, amelyek igénylik, hogy forduljon az ügyfélszolgálathoz vagy a rendszergazdát a probléma megoldódott, az alábbiakban néhány hibaelhárítási témakörök, amelyek segítségével, először is.

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>Problémáim vannak az alkalmazások saját portál bejelentkezni

Általános problémák kereséséhez:

- Ellenőrizze, hogy ha jelentkezik be a helyes URL-címet: [https://myapps.microsoft.com](https://myapps.microsoft.com)

- Próbálkozzon a böngésző megbízható helyek ad hozzá az URL-címet.

- Győződjön meg arról, hogy a jelszó nem lejárt vagy elfelejtett. Ellenőrizze [Itt](active-directory-passwords-update-your-own-password.md) a további részletekért frissítse a jelszavát.

- Ellenőrizze, hogy van-e a hitelesítés kapcsolattartási adatai és nem blokkolja-e a hozzáférés. Ellenőrizze [Itt](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user) a hitelesítési adatok beállításával kapcsolatos további részletekért.

- Törölje a böngésző cookie-kat, és próbálja meg újból bejelentkezni.

Ha továbbra is problémák adódtak a bejelentkezés közben, a rendszergazdától további segítségért.


## <a name="how-do-i-update-my-password"></a>Hogyan frissíthetők a jelszó?

Ha elfelejtette a jelszavát, soha nem kapott az informatikai munkatársak közül, zárolja a fiókot, vagy szeretné módosítani, lásd: [segítségre van szüksége, az Azure AD-jelszó elfelejtettem](active-directory-passwords-update-your-own-password.md) további részleteket.

## <a name="how-do-i-register-for-password-reset"></a>Hogyan regisztrálhatnak a jelszóváltoztatásra?

Végfelhasználói új jelszót, vagy anélkül, hogy az önkiszolgáló jelszó-változtatási (SSPR) használó személy kommunikáljanak a fiók zárolását kívánja feloldani. Mielőtt használhatná ezt a funkciót, hitelesítési módszereket kell regisztrálnia, vagy jóvá kell hagynia a rendszergazda által betöltött előre definiált hitelesítési módszereket. További részletekért lásd: [az önkiszolgáló jelszó-változtatási regisztrációs](active-directory-passwords-reset-register.md).


## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Problémáim vannak az alkalmazások a biztonságos bejelentkezés bővítmény telepítése

Ellenőrizze, hogy ha teljesíti-böngészőre vonatkozó követelményei:

- A portálhoz szükséges, amely támogatja a JavaScript egy böngészőt, és CSS engedélyezte. Ha jelszóalapú egyszeri bejelentkezés alkalmazásokat használ, a hozzá tartozó bővítmény is telepíteni kell. A bővítmény automatikusan letöltődik jelszó-alapú egyszeri bejelentkezés alkalmazásokhoz konfigurált alkalmazás elindítása.

- A bővítmény a böngészőre vonatkozó követelményei a következők:
    - Peremhálózati Windows 10 évforduló Edition vagy újabb
    - A Windows 7 vagy újabb, és MacOS X rendszeren vagy újabb Chrome
    - Firefox 26.0 vagy újabb, Windows XP SP2 vagy újabb verzióját, és a Mac OS X 10.6 vagy újabb
    - Internet Explorer 8, 9, 10, 11, Windows 7 vagy újabb (korlátozott támogatás)

Az alábbi közvetlen hivatkozásokat a Chrome és peremhálózati is letölthető a bővítmény:

- [Chrome-bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

- [Peremhálózati bővítmény](https://www.microsoft.com/store/apps/9pc9sckkzk84)

Miután a telepítés kövesse az alábbi lépéseket, ha a probléma áll kapcsolatban:

- Ellenőrizze, hogy a böngésző bővítmény beállításait az, hogy a bővítmény engedélyezve van-e.

- Indítsa újra a böngészőt, és jelentkezzen be a saját alkalmazások portálra.

- Törölje a böngésző cookie-kat, és jelentkezzen be a saját alkalmazások portálra.
- Kövesse a [hibaelhárítása a hozzáférési Panel bővítményét az Internet Explorer](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-ie-troubleshooting) útmutató a hozzáférés olyan diagnosztikai eszköz és részletes útmutatás a bővítmény konfigurálásáról az Internet Explorer.

## <a name="how-do-i-use-the-my-apps-secure-sign-in-extension"></a>A személyes alkalmazások biztonságos bejelentkezési bővítmény használata?
A bővítmény saját alkalmazások alapértelmezett URL-Címének módosítása

Egy másik saját alkalmazások URL-címet mint https://myapps.microsoft.com használata majd konfigurálnia kell az alapértelmezett URL-cím, ha az alábbi lépéseket:
1. Amíg nem írta be a bővítmény **kattintson a jobb gombbal** a bővítmény ikonra.
2. Kattintson a **válassza ki a saját alkalmazások URL-cím** a menüből.
3. **Válassza ki** az alapértelmezett URL-cím.
4. Kattintson a bővítmény ikonra.
5. Jelentkezzen be a bővítmény kiválasztásával **jelentkezzen be a kezdéshez**.

Jelentkezzen be közvetlenül a böngészőből az alkalmazásba
1. A bővítmény bejelentkezés kiválasztásával a bővítmény telepítését követően **jelentkezzen be a kezdéshez**.
2. Keresse meg a **sign-on URL-cím** az alkalmazást szeretne bejelentkezni, és ezt az általában az URL-CÍMÉT, amely a bejelentkezési űrlap jeleníti meg.
3. A bővítmény kell módosította az állapotát, és lehetővé teszik, hogy tudja, hogy a jelszó érhető el, kattintson a a **bővítmény ikon** való bejelentkezéshez

Indítsa el az alkalmazást, a bővítmény
1. A bővítmény bejelentkezés kiválasztásával a bővítmény telepítését követően **jelentkezzen be a kezdéshez**.
2. Kattintson a bővítmény ikonra a menü megnyitásához.
3. **Keresési** a saját alkalmazások portálon elérhető alkalmazás.
4. Kattintson a az alkalmazásból a **keresési eredmények** elindításához.
5. Elindítja az utolsó három alkalmazásokat is megjelenik a **legutóbbi** helyi listája

> [!NOTE]
> A beállítások csak az él, Chrome, Firefox érhetők el.

## <a name="how-do-i-add-a-new-app"></a>Hogyan vehetek fel egy új alkalmazást?

1.  Az a **alkalmazások** kattintson **alkalmazás hozzáadása**.

2.  A kívánt alkalmazást, adja hozzá, és kattintson a Keresés **Hozzáadás**.

**Megjegyzés:**

- Ha a rendszergazda engedélyezte ezt a fiókot csak rendelkezik hozzáféréssel a beállításhoz.

- Ha az alkalmazás engedélyre van szüksége, szükség lehet megvárja a rendszergazdai jóváhagyást.


## <a name="how-do-i-manage-my-group-memberships"></a>Hogyan kezelhetem a csoporttagságok?

1. Kattintson a **csoportok** csempére. 
2. Hozzon létre egy csoportot, a csoportok saját, kattintson a **csoport létrehozása**, majd kövesse az utasításokat.
3. Csatlakozás a csoporthoz, a csoportok vagyok, kattintson a **csatlakozás a csoporthoz**, majd kövesse az utasításokat.

**Megjegyzés:**

- Ha a rendszergazda engedélyezte ezt a fiókot csak rendelkezik hozzáféréssel a beállításhoz.

- Csoportokat, amelyeket Ön tagja lehetővé teszik a részletek megtekintéséhez és a csoport elhagyására.

- Csoportokat, amelyeket Ön a tulajdonosa adja hozzá vagy tagok eltávolítása és a csoport elhagyására teszi lehetővé, a részletek megtekintéséhez.


## <a name="next-steps"></a>További lépések

Kapcsolódó információk, tekintse meg [az alkalmazás hozzáférési panel webhelyen vagy a mobilalkalmazás problémák](active-directory-application-access-panel-content-map.md)

