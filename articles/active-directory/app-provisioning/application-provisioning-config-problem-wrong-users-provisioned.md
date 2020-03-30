---
title: A felhasználók nem megfelelő készlete van kiépítve egy Azure AD Gallery-alkalmazásba
description: Ismerje meg, hogyan állapíthatja meg, hogy miért van kiépítve egy alkalmazáshoz a vártnál eltérő felhasználói készlet
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c94388011605da73666e82011bb8ef56d2af8d30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522781"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>A felhasználók helytelen készlete van kiépítve egy Azure AD Gallery-alkalmazáshoz

Mely felhasználók vannak kiépítve az alkalmazáshoz elsősorban az, amely a felhasználók és csoportok vannak **rendelve** az alkalmazáshoz.

Az alábbi erőforrások segítségével megtudhatja, hogyan ellenőrizheti, hogy mely felhasználók és csoportok vannak hozzárendelve egy alkalmazáshoz az Azure Active Directoryban.

## <a name="assign-a-user-directly-as-an-administrator"></a>Felhasználó hozzárendelése közvetlenül rendszergazdaként

Ha egy vagy több felhasználót közvetlenül szeretne egy alkalmazáshoz rendelni, kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. A listából jelölje ki azt az alkalmazást, amelyhez felhasználót szeretne rendelni.

7. Miután az alkalmazás betöltődik, kattintson az alkalmazás bal oldali navigációs menüjének **Felhasználók és csoportok** parancsára.

8. A **Hozzárendelés hozzáadása** ablaktábla megnyitásához kattintson a Felhasználók **és csoportok** lista tetején a **Hozzáadás** gombra.

9. kattintson a **Felhasználók és csoportok** választóra a Hozzárendelés **hozzáadása** ablaktáblán.

10. Írja be annak a felhasználónak a **teljes nevét** vagy **e-mail címét,** akit szeretne hozzárendelni a **Keresés név vagy e-mail cím** alapján keresőmezőbe.

11. A **jelölőnégyzet**felfedéséhez mutasson a **felhasználó** fölé a listában. Kattintson a felhasználó profilfotója vagy emblémája melletti jelölőnégyzetre, ha hozzá szeretné adni a felhasználót a **Kijelölt** listához.

12. **Nem kötelező:** Ha **egynél több felhasználót**szeretne felvenni, írja be a másik **teljes nevet** vagy **e-mail címet** a Keresés név vagy **e-mail cím** alapján keresőmezőbe, és kattintson a jelölőnégyzetre, ha hozzá szeretné adni a **felhasználót** a Kiválasztott listához.

13. Ha befejezte a felhasználók kiválasztását, a **Kijelölés gombra** kattintva hozzáadhatja őket az alkalmazáshoz rendelendő felhasználók és csoportok listájához.

14. **Nem kötelező:** kattintson a Hozzárendelés **hozzáadása** ablaktábla **Szerepkör kiválasztása** kijelölő gombra a kijelölt felhasználókhoz rendelő szerepkör kiválasztásához.

15. Kattintson a **Hozzárendelés** gombra az alkalmazás kijelölt felhasználókhoz rendeléséhez.

Ha a kiépítés konfigurálva van, és már fut egy alkalmazáshoz, az új felhasználókat körülbelül 10 percen belül ki kell építeni egy alkalmazásba. A **részleteket** a naplókban találja.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Csoport hozzárendelése közvetlenül egy alkalmazáshoz rendszergazdaként

Ha egy vagy több csoportot közvetlenül szeretne egy alkalmazáshoz rendelni, kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. A listából jelölje ki azt az alkalmazást, amelyhez felhasználót szeretne rendelni.

7. Miután az alkalmazás betöltődik, kattintson az alkalmazás bal oldali navigációs menüjének **Felhasználók és csoportok** parancsára.

8. A **Hozzárendelés hozzáadása** ablaktábla megnyitásához kattintson a Felhasználók **és csoportok** lista tetején a **Hozzáadás** gombra.

9. kattintson a **Felhasználók és csoportok** választóra a Hozzárendelés **hozzáadása** ablaktáblán.

10. Írja be annak a csoportnak a **teljes csoportnevét,** amelyet hozzá szeretne rendelni a **Keresés név vagy e-mail cím** alapján keresőmezőbe.

11. A jelölőnégyzet felfedéséhez mutasson a **csoportra** a **listában.** Kattintson a csoport profilfotója vagy emblémája melletti jelölőnégyzetre, ha hozzá szeretné adni felhasználóját a **Kijelölt** listához.

12. **Nem kötelező:** Ha **egynél több csoportot**szeretne hozzáadni, írja be a teljes csoport **nevét** a Keresés név **vagy e-mail cím** alapján keresőmezőbe, és kattintson a jelölőnégyzetre, ha hozzá szeretné adni ezt a csoportot a **Kijelölt** listához.

13. Ha befejezte a csoportok kijelölését, a **Kijelölés gombra** kattintva vegye fel őket az alkalmazáshoz rendelendő felhasználók és csoportok listájára.

14. **Nem kötelező:** kattintson a Hozzárendelés **hozzáadása** ablaktábla **Szerepkör kiválasztása** kijelölő gombra a kijelölt csoportokhoz rendelő szerepkör kiválasztásához.

15. Kattintson a **Hozzárendelés** gombra, ha az alkalmazást a kijelölt csoportokhoz szeretné rendelni.

Ha a kiépítés konfigurálva van, és már fut egy alkalmazáshoz, a csoporton belül található új felhasználókat körülbelül 10 percen belül ki kell építeni egy alkalmazásba. A **részleteket** a naplókban találja.

>[!IMPORTANT]
>A csoport név és a csoport adatainak kiépítése a tagokon kívül, ha bizonyos alkalmazások esetében támogatottak. Ezt a funkciót a **Kiépítés** lapon látható csoportobjektumok **leképezésének** engedélyezésével vagy letiltásával engedélyezheti vagy tilthatja le. 
>
>

Ha a létesítési csoportok engedélyezve van, győződjön meg róla, hogy tekintse át az attribútum leképezések annak érdekében, hogy a megfelelő mező t használja a "megfelelő azonosító". Ez a megfelelő azonosító lehet a megjelenítendő név vagy az e-mail alias. A csoport és annak tagjai nincsenek kiépítve, ha az egyező tulajdonság üres, vagy nem lakott egy csoport az Azure AD-ben.

## <a name="next-steps"></a>További lépések
[A felhasználói kiépítés és a SaaS-alkalmazásokba való megszüntetés automatizálása az Azure Active Directoryval](user-provisioning.md)
