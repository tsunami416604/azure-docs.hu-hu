---
title: Segítség eléréséhez, és a személyes alkalmazások portál használata az Azure Active Directoryban |} Microsoft Docs
description: Segítség próbál bejelentkezni, és végrehajtson hétköznapi feladatokat a hozzáférési panelen.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: f0604007255d829cab0502e93895ca541da3b93a
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="troubleshoot-issues-with-accessing-and-using-the-my-apps-portal"></a>Való hozzáférés és a személyes alkalmazások portálon elhárítása

Ha történő bejelentkezés vagy a saját alkalmazások portálon problémákat tapasztal, próbálja meg ezek a hibaelhárítási tippek, mielőtt a segélyszolgálathoz vagy a rendszergazda segítségét.

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>Problémáim vannak az alkalmazások saját portál bejelentkezni

Próbálja meg ezek általános tippeket:

- Első, a jelölőnégyzet megtekintéséhez, hogy a helyes URL-címet használ [ https://myapps.microsoft.com ](https://myapps.microsoft.com).
- Próbálkozzon a böngésző megbízható helyek ad hozzá az URL-címet.
- Győződjön meg arról, hogy a jelszó helyes-e, és nem járt le. További információkért lásd: [a munkahelyi vagy iskolai jelszó](active-directory-passwords-update-your-own-password.md).
- Ellenőrizze, hogy ellenőrizze, hogy a hitelesítési kapcsolattartási adatokat és nem blokkolja-e a hozzáférést. További információkért lásd: [mit Azure multi-factor Authentication jelent a számomra?](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user).
- Törölje a böngésző cookie-kat, és próbálja meg újból bejelentkezni.

Ha továbbra is problémák adódtak a bejelentkezés közben, forduljon a rendszergazdához.


## <a name="i-seem-to-be-having-password-issues"></a>I úgy tűnik, hogy a jelszó problémákat bejelentsen

Ha elfelejtette a jelszavát, soha nem kapott az informatikai munkatársak közül, az fiókjából, vagy szeretné módosítani a jelszót, lásd: [segítségre van szüksége, az Azure AD-jelszó elfelejtettem](active-directory-passwords-update-your-own-password.md).

## <a name="i-need-to-register-for-password-reset"></a>Kell regisztrálni a jelszó alaphelyzetbe állítása

A jelszó, vagy anélkül, hogy valaki önkiszolgáló jelszó-változtatási (SSPR) használatával kommunikáljanak a fiók zárolását kívánja feloldani. Ez a funkció használata előtt kell regisztrálni a hitelesítési módszereket, vagy erősítse meg az előre definiált hitelesítési módszereket, amelyek a rendszergazda megköveteli. További információkért lásd: [az önkiszolgáló jelszó-változtatási regisztrációs](active-directory-passwords-reset-register.md).


## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Problémáim vannak az alkalmazások a biztonságos bejelentkezés bővítmény telepítése

A saját alkalmazások portálhoz szükséges, amely támogatja a JavaScript egy böngészőt, és CSS engedélyezte. Ha jelszóalapú egyszeri bejelentkezés alkalmazásokat használ, a hozzá tartozó bővítmény is telepíteni kell. A bővítmény le automatikusan, jelszó-alapú egyszeri bejelentkezés alkalmazásokhoz beállított az alkalmazás indításakor.

Ellenőrizze, hogy, hogy a következő webböngészőkre vonatkozó követelmények teljesítésének:
- **Peremhálózati**: a Windows 10 évforduló Edition vagy újabb.
- **Chrome**: Windows 7-es vagy újabb, és a Mac OS X vagy újabb.
- **Firefox 26.0 vagy újabb**: Windows XP SP2 vagy újabb, és a Mac OS X 10.6 vagy újabb.
- **Az Internet Explorer 11**: Windows 7 vagy újabb (korlátozottan támogatja).

Emellett letöltheti a bővítmény közvetlenül a következő helyekről:

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Peremhálózati](https://go.microsoft.com/fwlink/?linkid=845176)
- [A Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

Ha telepítette a bővítményt, és továbbra is érintő problémákat tapasztal, próbálja meg a következőket:

- Ellenőrizze a bővítmény böngészőbeállítások győződjön meg arról, hogy a bővítmény engedélyezve van-e.
- Indítsa újra a böngészőt, és jelentkezzen be a saját alkalmazások portálra.
- Törölje a böngésző cookie-kat, és jelentkezzen be a saját alkalmazások portálra.
- A diagnosztikai eszköz és részletes ismertetése a bővítmény beállítása az Internet Explorer elérésére, lásd: [hibaelhárítása a hozzáférési Panel bővítményét az Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Használja a alkalmazásaimat biztonságos bejelentkezési bővítmény
* Használata saját alkalmazások URL-címe eltérő `https://myapps.microsoft.com`, az alapértelmezett URL-cím konfigurálása a következő tevékenységek végrehajtásával:
   1. Miközben az *nem* jelentkezve a bővítményt, kattintson a jobb gombbal bővítmény.
   2. Válassza a menü **saját alkalmazások URL-cím**.
   3. Válassza ki az alapértelmezett URL-cím.
   4. Válassza ki a bővítmény ikonra.
   5. Jelentkezzen be a bővítményt, jelölje be **jelentkezzen be a kezdéshez**.

* Jelentkezzen be közvetlenül a böngészőből az alkalmazásba, tegye a következőket:
   1. A bővítmény telepítése után jelentkezzen be az kiválasztásával **jelentkezzen be a kezdéshez**.
   2. Jelentkezzen be az alkalmazás és a bejelentkezési URL-CÍMÉT.  
       A bejelentkezési URL-címet az általában az URL-CÍMÉT, amely megjeleníti a bejelentkezési képernyőt.
      A bővítmény kell módosította az állapotát, és jelzi, hogy rendelkezésre áll-e jelszót.
   3. Jelentkezzen be, válassza a kiterjesztés ikonra.

* Indítsa el az alkalmazást, a bővítmény a, tegye a következőket:
   1. A bővítmény telepítése után jelentkezzen be az kiválasztásával **jelentkezzen be a kezdéshez**.
   2. Válassza ki a bővítmény ikonra a menü megnyitásához.
   3. Keresse meg a személyes alkalmazások portálon elérhető alkalmazás.
   4. A keresési eredmények listájában válassza ki az alkalmazást.  
       Már használta az utolsó három alkalmazások megjelennek a **legutóbbi** helyi listát.

> [!NOTE]
> Ezek a beállítások csak él, Chrome és Firefox érhetők el.

## <a name="how-do-i-add-a-new-app"></a>Hogyan vehetek fel egy új alkalmazást?

1.  Az a **alkalmazások** lapon jelölje be **alkalmazás hozzáadása**.
2.  Keresse meg az alkalmazást, amelyet szeretne hozzáadni, majd válassza ki **Hozzáadás**.

   > [!NOTE]
   > * Ez a beállítás csak akkor, ha a rendszergazda engedélyezte a fiók érheti el.
   > * Ha az alkalmazás engedélyre van szüksége, szükség lehet megvárja a rendszergazdai jóváhagyást.
   > 

## <a name="how-do-i-manage-my-group-memberships"></a>Hogyan kezelhetem a csoporttagságok?

Válassza ki a **csoportok** csempére, majd tegye a következők egyikét: 
* A csoport, létrehozása **saját csoportok**, jelölje be **csoport létrehozása**, és kövesse az utasításokat.
* Egy csoporthoz való csatlakoztatáshoz, a **az vagyok csoportok**, jelölje be **csatlakozás a csoporthoz**, majd kövesse az utasításokat.

   > [!NOTE]
   > * Ez a beállítás csak akkor, ha a rendszergazda engedélyezte a fiók érheti el.
   > * Ha egy csoport tagja, a részletek megtekintéséhez, és a csoport elhagyására.
   > * Ha egy csoport tulajdonosa, a részletek megtekintéséhez, vegye fel vagy tagok eltávolítása, és a csoport elhagyására.
   >


## <a name="next-steps"></a>További lépések

További hibaelhárítási információkért lásd: [problémák az alkalmazás hozzáférési panel webhelyen vagy a mobilalkalmazás](active-directory-application-access-panel-content-map.md).

