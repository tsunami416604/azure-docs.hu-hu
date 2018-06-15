---
title: Problémát az önkiszolgáló alkalmazás-hozzáférés |} Microsoft Docs
description: Önkiszolgáló alkalmazás eléréséhez kapcsolódó problémák megoldása
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: 6d4044414cfae9a79487d02709aab24998fdef0b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29382355"
---
# <a name="problem-using-self-service-application-access"></a>Problémát az önkiszolgáló alkalmazás-hozzáférés

Önkiszolgáló alkalmazás-hozzáférés kiváló módja annak, hogy önálló felderítéséhez az alkalmazások, felhasználók igény szerint engedélyezett az üzleti csoport ezeket az alkalmazásokat a hozzáférést. Engedélyezze az üzleti csoport társítva a hozzáférési panel azoknak a felhasználóknak jelszót egyszeri bejelentkezést az alkalmazások jobb a hitelesítő adatok kezeléséhez.

Mielőtt a felhasználók saját maguk felderíthetők az alkalmazások a hozzáférési panelen, engedélyeznie kell **önkiszolgáló alkalmazás-hozzáférés** olyan alkalmazásokat, amelyek önálló felderítését, és kérjen engedélyezni szeretné a hozzáférést.

## <a name="general-issues-to-check-first"></a>Először ellenőrizze a általános problémák

-   Ellenőrizze, hogy az önkiszolgáló alkalmazás-hozzáférés megfelelően van konfigurálva. "Önkiszolgáló alkalmazás-hozzáférés konfigurálásához lásd" című részben.

-   Győződjön meg arról, hogy a felhasználó vagy csoport engedélyezve van az önkiszolgáló alkalmazás-hozzáférés kéréséhez.

-   Győződjön meg arról, hogy a felhasználó a megfelelő helyen önkiszolgáló alkalmazás-hozzáférési felkeresett. felhasználók saját [alkalmazás hozzáférési Panel](https://myapps.microsoft.com/) , és kattintson a **+ Hozzáadás** gombra kattintva keresse meg az alkalmazások, amelyhez engedélyezte a hozzáférést az önkiszolgáló.

-   Ha önkiszolgáló alkalmazás-hozzáférés csak nemrég lett konfigurálva, próbáljon meg bejelentkezni bejövő és kimenő adatforgalma újra a felhasználó hozzáférési panelre néhány perc múlva megjelenítéséhez, ha az önkiszolgáló változásokat jelentek-e.

## <a name="how-to-configure-self-service-application-access"></a>Önkiszolgáló alkalmazás-hozzáférés konfigurálása

Ahhoz, hogy az alkalmazás önkiszolgáló hozzáférést egy alkalmazáshoz, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes Alkalmazások.**

6.  Válassza ki az önkiszolgáló engedélyezni szeretné a hozzáférést a listából.

7.  Ha az alkalmazás betölt, kattintson **önkiszolgáló** az alkalmazás bal oldali navigációs menüjében.

8.  Ahhoz, hogy az önkiszolgáló alkalmazás hozzáférése az alkalmazáshoz, kapcsolja be a **az alkalmazáshoz való hozzáférés kérését?** kapcsolót **igen.**

9.  A következő, amelyekhez a felhasználók, akik kérnek az alkalmazáshoz való hozzáférés hozzá kell adni a csoport kijelöléséhez kattintson a felirat melletti választó **mely csoporthoz rendelt felhasználók vehet fel?** válasszon ki egy csoportot.

10. **Választható lehetőség:** előtt egy üzleti jóváhagyás megkövetelése, ha a felhasználók jogosultak-e a hozzáférést, és állítsa a **az alkalmazáshoz való hozzáférés megadása előtt jóvá kell hagyni?** kapcsolót **Igen**.

11. **Választható lehetőség: az alkalmazások csak a jelszó egyszeri bejelentkezés használatával** adott üzleti jóváhagyóknak adhatja meg a jelszavakat, a jóváhagyott felhasználók számára az alkalmazás küldött engedélyezni szeretné, ha a **jóváhagyóknak beállítása a felhasználó engedélyezése jelszavak ehhez az alkalmazáshoz?**  kapcsolót **Igen**.

12. **Választható lehetőség:** az üzleti jóváhagyóknak, akik jogosultak a hagyja jóvá az alkalmazáshoz való hozzáférés megadásához kattintson a felirat melletti választó **ki jogosult az alkalmazáshoz való hozzáférés jóváhagyásához?** legfeljebb 10 egyéni kiválasztásához üzleti jóváhagyóknak.

 >[!NOTE]
 > Csoportok használata nem támogatott.
 >
 >

13. **Választható lehetőség:** **az alkalmazások, amelyeknél a szerepkörök**, ha önkiszolgáló jóváhagyott felhasználók hozzárendelése egy szerepkörhöz, kattintson a Tovább gombra a választó a **mely szerepkörhöz felhasználók hozzárendeli az alkalmazásban?** Jelölje be a szerepkör, amelyhez hozzá kell rendelni ezeket a felhasználókat.

14. Kattintson a **mentése** gombra a befejezéshez panel tetején.

Ha befejezte az önkiszolgáló Alkalmazáskonfiguráció, felhasználók lépjen a [alkalmazás hozzáférési Panel](https://myapps.microsoft.com/) , és kattintson a **+ Hozzáadás** gombra kattintva keresse meg az alkalmazások, amelyhez engedélyezte a hozzáférést az önkiszolgáló. Üzleti jóváhagyóknak is megjelenik egy értesítés a saját [alkalmazás hozzáférési Panel](https://myapps.microsoft.com/). Egy e-mailt, amely értesíti őket, amikor a felhasználó által kért a jóváhagyást igénylő alkalmazáshoz való hozzáférés engedélyezéséhez. 

Ezek a jóváhagyások támogatja egyetlen jóváhagyási munkafolyamatok csak, ami azt jelenti, hogy több jóváhagyó ad meg, ha bármely egyetlen jóváhagyó jóváhagyhatja a hozzáférni az alkalmazáshoz.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Ha ezek a hibaelhárítási lépéseket nem oldja meg a problémát 

támogatási jegy megnyitása a következő információkat, ha rendelkezésre áll:

-   Megfelelési hiba azonosítója

-   Egyszerű felhasználónév (felhasználó e-mail címe)

-   TenantID

-   Böngésző típusa

-   Időzóna és idő/időkeretre során hiba történik.

-   Fiddler nyomkövetések

## <a name="next-steps"></a>További lépések
[Azure Active Directory beállítása önkiszolgáló csoportkezelés](active-directory-accessmanagement-self-service-group-management.md)
