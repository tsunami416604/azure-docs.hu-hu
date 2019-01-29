---
title: Segítség a elérése és a MyApps portálról az Azure Active Directoryban |} A Microsoft Docs
description: Segítség a bejelentkezéshez, és végrehajtson hétköznapi feladatokat a hozzáférési panelen.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: lizross
ms.reviewer: japere
ms.openlocfilehash: 9613b1d1738b58df4024841339f1579661594f70
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55166307"
---
# <a name="troubleshoot-issues-with-accessing-and-using-the-myapps-portal"></a>Hozzáférés és a MyApps portálról hibáinak elhárítása

Ha olyan jelentkezik be, vagy a MyApps portálról problémákat tapasztal, próbálja meg ezek a hibaelhárítási tippek, mielőtt, segítségért forduljon a segélyszolgálathoz vagy a rendszergazdához.

## <a name="i-am-having-trouble-signing-into-the-myapps-portal"></a>Gondjaim vannak jelentkezik be a MyApps portálról

Próbálja ki a következő általános tippeket:

- Első ellenőrizze, hogy használ-e a megfelelő URL-CÍMÉT, hogy [ https://myapps.microsoft.com ](https://myapps.microsoft.com).
- Próbáljon meg az URL-címet a böngésző megbízható helyekre.
- Győződjön meg arról, hogy a jelszó helyes-e, és nem járt le. További információkért lásd: [a munkahelyi vagy iskolai jelszó visszaállítása](active-directory-passwords-update-your-own-password.md).
- Ellenőrizze, hogy ellenőrizze, hogy a hitelesítési kapcsolattartási adatok dátum-és nem blokkolja a hozzáférést. További információkért lásd: [Mi az Azure multi-factor Authentication jelent a számomra?](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user).
- Törölje a böngésző cookie-kat, és próbálja meg újra bejelentkezni.

Ha továbbra is problémái vannak bejelentkezés során, forduljon a rendszergazdához.

## <a name="i-seem-to-be-having-password-issues"></a>Szerepkörömhöz is biztosítani a jelszóval kapcsolatos problémák

Ha elfelejtette a jelszavát, soha nem kapott az informatikai részleg, használja ki a fiók zárolva vannak, vagy szeretné módosítani a jelszavát, lásd: [segítség, elfelejtettem Azure AD-jelszavamat](active-directory-passwords-update-your-own-password.md).

## <a name="i-need-to-register-for-password-reset"></a>Kell regisztrálni a jelszó-visszaállításhoz

A jelszó alaphelyzetbe állítása, vagy a fiók zárolásának feloldása nélkül valaki mérnökeinkkel, az önkiszolgáló jelszó-visszaállítás (SSPR) használatával. Ez a funkció használata előtt meg kell hitelesítési módszerek regisztrálása, illetve erősítse meg az előre definiált hitelesítési módszereket, amelyek a rendszergazda megköveteli. További információkért lásd: [regisztráció önkiszolgáló jelszó-visszaállításra](active-directory-passwords-reset-register.md).

## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Gondjaim vannak a saját alkalmazások biztonságos bejelentkezési bővítmény telepítése

A MyApps portálról kell egy böngészőben, amely támogatja a JavaScript és CSS engedélyezve van. Jelszavas egyszeri bejelentkezéses alkalmazások használatakor, valamint a hozzájuk tartozó bővítményt kell telepíteni. Ezzel a bővítménnyel automatikusan letöltődik egy alkalmazás, amely konfigurálva van a jelszóalapú egyszeri bejelentkezéses alkalmazások indításakor.

Ellenőrizze, hogy győződjön meg arról, hogy teljesíti-e vannak a következő webböngészőkre vonatkozó követelmények:

- **A Microsoft Edge**: a Windows 10 Évfordulós kiadása vagy újabb.
- **Chrome**: Windows 7-es vagy újabb, és a Mac OS x-en vagy újabb.
- **Firefox 26.0 vagy újabb**: a Windows XP SP2 vagy újabb, és a Mac OS X 10.6 vagy újabb.
- **Az Internet Explorer 11**: Windows 7 vagy újabb (korlátozott támogatást).

Emellett letöltheti a bővítmény közvetlenül a következő helyekről:

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

Ha a bővítmény telepítve van, és továbbra is problémákat tapasztal, amikor, megpróbálkozhat a következőkkel:

- Ellenőrizze a böngésző kiterjesztés beállításait, győződjön meg arról, hogy a bővítmény engedélyezve van.
- Indítsa újra a böngészőt, és jelentkezzen be a MyApps portálról.
- Törölje a böngésző cookie-kat, és jelentkezzen be a MyApps portálról.
- Hozzáférés egy diagnosztikai eszköz, és lépésenkénti útmutatót a bővítmény konfigurálása az Internet Explorer, lásd: [a hozzáférési Panel bővítmény hibaelhárítása az Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Használja a saját alkalmazások biztonságos bejelentkezési bővítménye
* Ha nem használ egy saját alkalmazások URL-cím `https://myapps.microsoft.com`, az alapértelmezett URL-cím konfigurálása a következő tevékenységek végrehajtásával:
   1. Amíg nem *nem* bejelentkezett a bővítményt, kattintson a bővítmény ikonra a jobb gombbal.
   2. Válassza a menü **saját alkalmazások URL-cím**.
   3. Válassza ki az alapértelmezett URL-címe.
   4. A bővítmény ikonra.
   5. Jelentkezzen be a bővítményt, jelölje be **jelentkezzen be a kezdéshez**.

* Közvetlenül a böngészőből olyan alkalmazásba jelentkezik be, tegye a következőket:
   1. Miután telepítette a bővítményt, bejelentkezni kiválasztásával **jelentkezzen be a kezdéshez**.
   2. Jelentkezzen be az alkalmazás bejelentkezési URL-címét.  
       A bejelentkezési URL-cím általában a az URL-címét, amely a bejelentkezési képernyőn jeleníti meg.
      A bővítmény kell módosítani az állapotát, és értesíti róla, hogy rendelkezésre áll-e a jelszó.
   3. Jelentkezzen be, hogy a bővítmény ikonra.

* Indítsa el a bővítményt az alkalmazásokat, tegye a következőket:
   1. Miután telepítette a bővítményt, bejelentkezni kiválasztásával **jelentkezzen be a kezdéshez**.
   2. A bővítmény ikonra a menü megnyitásához.
   3. Keressen rá egy alkalmazást, amely a MyApps portálról érhető el.
   4. A keresési eredmények listájában válassza ki az alkalmazást.  
       Már használta az utolsó három alkalmazások megjelennek a **a legutóbb használt** helyi listát.

> [!NOTE]
> Ezek a beállítások csak a Microsoft Edge, Chrome és a Firefox érhetők el.

## <a name="how-do-i-add-a-new-app"></a>Hogyan adhatok hozzá egy új alkalmazást?

1.  Az a **alkalmazások** lapon jelölje be **alkalmazás hozzáadása**.
2.  Keresse meg az alkalmazást, adja hozzá, és válassza ki a kívánt **Hozzáadás**.

   > [!NOTE]
   > * Ez a beállítás csak akkor, ha a rendszergazda engedélyezte a fiók is elérheti.
   > * Ha az alkalmazás engedélyre van szüksége, szüksége lehet a rendszergazdai jóváhagyás.

## <a name="how-do-i-manage-my-group-memberships"></a>Hogyan kezelhetem a csoporttagságok?

Válassza ki a **csoportok** csempére, majd tegye a következők egyikét:
* Egy csoport létrehozása alatt **saját csoportok**válassza **csoport létrehozása**, majd kövesse az utasításokat.
* Egy csoporthoz való csatlakoztatáshoz, alatt **csoporttagságok**válassza **csatlakozás a csoporthoz**, majd kövesse az utasításokat.

   > [!NOTE]
   > * Ez a beállítás csak akkor, ha a rendszergazda engedélyezte a fiók is elérheti.
   > * Ha egy csoport tagjai, a részletek megtekintéséhez, és hagyja meg a csoport.
   > * Ha egy csoport tulajdonosa, részleteinek megtekintése, hozzáadása vagy a tagok eltávolítása, és hagyja meg a csoport.
