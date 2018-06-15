---
title: Nem megfelelő felhasználók csoportja alatt kiépített egy Azure AD-katalógusában alkalmazás |} Microsoft Docs
description: Ha szeretné tudni, miért kiépített egy alkalmazás a várt, mint a felhasználók különböző szabálykészleteket útmutató
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
ms.openlocfilehash: 2dca671d4813dac0709dacb17d39cd3af3b3292c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29383137"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Nem megfelelő felhasználók csoportja folyamatban kiépített egy Azure AD-katalógusában alkalmazáshoz

Mely felhasználók törlődnek, az alkalmazás elsősorban célja a felhasználók és csoportok volt **hozzárendelt** az alkalmazáshoz.

A következő források segítségével megtudhatja, hogyan ellenőrizze, hogy mely felhasználók és csoportok hozzárendelt egy alkalmazást az Azure Active Directoryban.

## <a name="assign-a-user-directly-as-an-administrator"></a>Rendelje hozzá a felhasználó közvetlenül rendszergazdaként

Hozzárendelése egy vagy több felhasználó alkalmazás közvetlenül, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes Alkalmazások.**

6.  Válassza ki szeretné osztani a felhasználót, hogy a listában az alkalmazást.

7.  Ha az alkalmazás betölt, kattintson **felhasználók és csoportok** az alkalmazás bal oldali navigációs menüjében.

8.  Megnyitásához a **hozzáadása hozzárendelés** ablaktáblán kattintson a **Hozzáadás** gombra kattint, a a **felhasználók és csoportok** listája.

9.  Kattintson a **felhasználók és csoportok** a választó a **hozzáadása hozzárendelés** ablaktáblán.

10. Írja be a **teljes név** vagy **e-mail cím** érdekli hozzárendelése a felhasználó a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. Vigye a **felhasználói** a listában, hogy láthatóvá váljon a **jelölőnégyzet**. A felhasználói profil fénykép vagy adja hozzá a felhasználót emblémát jelölőnégyzetét, kattintson a **kijelölt** listája.

12. **Választható lehetőség:** Ha azt szeretné, hogy **egynél több felhasználó hozzáadása**, egy másik típus **teljes név** vagy **e-mail cím** azokat a **Keresés név vagy e-mail cím** mező, és a jelölőnégyzet bejelölésével adja hozzá a felhasználót, hogy a **kijelölt** listája.

13. Ha elkészült, válassza a felhasználók, kattintson a **válasszon** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazáshoz.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** a választó a **hozzáadása hozzárendelés** hozzárendelése a kiválasztott felhasználói szerepkör kiválasztása panel.

15. Kattintson a **hozzárendelése** gombra kattintva a kijelölt felhasználók az alkalmazást.

Ha a kiépítés van konfigurálva, és már fut egy olyan alkalmazáshoz, az új felhasználók kell kell létrehozni, hogy egy alkalmazás körülbelül 10 perc múlva. Ellenőrizze a **naplók** részleteiről.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Rendszergazdaként alkalmazáshoz való közvetlenül is kiadhatjuk egy csoportnak

Közvetlenül egy alkalmazás hozzárendelése egy vagy több csoportot, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes Alkalmazások.**

6.  Válassza ki szeretné osztani a felhasználót, hogy a listában az alkalmazást.

7.  Ha az alkalmazás betölt, kattintson **felhasználók és csoportok** az alkalmazás bal oldali navigációs menüjében.

8.  Megnyitásához a **hozzáadása hozzárendelés** ablaktáblán kattintson a **Hozzáadás** gombra kattint, a a **felhasználók és csoportok** listája.

9.  Kattintson a **felhasználók és csoportok** a választó a **hozzáadása hozzárendelés** ablaktáblán.

10. Írja be a **teljes csoportnév** érdekli való hozzárendelése a csoport a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. Vigye a **csoport** a listában, hogy láthatóvá váljon a **jelölőnégyzet**. A csoport profilképet vagy adja hozzá a felhasználót emblémát jelölőnégyzetét, kattintson a **kijelölt** listája.

12. **Nem kötelező:** Ha azt szeretné, hogy **egynél több csoport hozzáadása**, egy másik típus **teljes csoportnév** be a **Keresés név vagy e-mail cím alapján** keresési mezőbe, és a jelölőnégyzet bejelölésével a csoport hozzáadása a **kijelölt** listája.

13. Ha befejezte a csoportok kiválasztásával, kattintson a **válasszon** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazáshoz.

14. **Nem kötelező:** kattintson a **Szerepkörválasztás** a választó a **hozzáadása hozzárendelés** szerepkör hozzárendelése a kijelölt csoportok kiválasztása ablakban.

15. Kattintson a **hozzárendelése** gombra az alkalmazás a kiválasztott csoportok számára.

Ha a kiépítés van konfigurálva, és már fut egy olyan alkalmazáshoz, a csoport tartalmazza az új felhasználók kell kell létrehozni, hogy egy alkalmazás körülbelül 10 perc múlva. Ellenőrizze a **naplók** részleteiről.

>[!IMPORTANT]
>Kiépítése a csoport nevét és a csoport részletes adatait, mellett a tagok, ha bizonyos alkalmazások támogatott. Engedélyezheti vagy letilthatja ezt a funkciót engedélyezésével vagy letiltásával a **leképezési** a csoport objektumainak látható a **kiépítési** fülre. 
>
>

Ha csoportok kiépítés engedélyezve van, feltétlenül olvassa el a attribútum-leképezésekhez ellenőrizze a megfelelő mezőben használja a "egyező azonosító". Ez a megjelenítendő név lehet, és nem e-mail alias, a csoport és a tagok nem építhető ki, ha a megfelelő tulajdonság üres vagy nem a csoport az Azure ad-ben.

## <a name="next-steps"></a>További lépések
[Felhasználói kiépítésének és megszüntetésének biztosítása SaaS-alkalmazásokhoz az Azure Active Directoryval történő automatizálásához](active-directory-saas-app-provisioning.md)
