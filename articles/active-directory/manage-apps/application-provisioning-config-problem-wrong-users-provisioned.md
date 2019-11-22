---
title: Nem megfelelő felhasználók vannak kiépítve egy Azure AD Gallery-alkalmazásba
description: Ismerje meg, hogy miként állapítható meg, hogy egy másik felhasználó hogyan legyen kiépítve egy alkalmazásba, mint amennyit várt
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77e8f31bd0adeabea79cd11553fd77a3e5ab6404
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275776"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Helytelenek a felhasználók egy Azure AD Gallery-alkalmazásban való üzembe helyezése

Az alkalmazáshoz kiépített felhasználók elsődlegesen a felhasználók és a csoportok az alkalmazáshoz vannak **rendelve** .

Az alábbi forrásokból megtudhatja, hogyan ellenőrizhető, hogy mely felhasználók és csoportok vannak hozzárendelve egy alkalmazáshoz Azure Active Directoryon belül.

## <a name="assign-a-user-directly-as-an-administrator"></a>Felhasználó közvetlen kijelölése rendszergazdaként

Ha egy vagy több felhasználót közvetlenül szeretne hozzárendelni egy alkalmazáshoz, kövesse az alábbi lépéseket:

1. Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7. Az alkalmazás betöltése után kattintson a **felhasználók és csoportok** elemre az alkalmazás bal oldali navigációs menüjében.

8. A **hozzárendelés hozzáadása** ablaktábla megnyitásához kattintson a **Hozzáadás** gombra a **felhasználók és csoportok** lista tetején.

9. Kattintson a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

10. Írja be a **teljes név** vagy **e-mail-cím** Önt érdeklő való hozzárendelése a felhasználó a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. A kurzort a **felhasználói** megjelenítéséhez a listában egy **jelölőnégyzet**. Jelölje be a jelölőnégyzetet, a felhasználó profilfényképének vagy adja hozzá a felhasználót az embléma mellett a **kijelölt** listája.

12. **Választható lehetőség:** Ha szeretné **egynél több felhasználó hozzáadása**, írjon be egy másik **teljes név** vagy **e-mail-cím** be a **Keresés név alapján, vagy e-mail-cím** keresőmezőbe, majd kattintson a jelölőnégyzet bejelölésével adja hozzá a felhasználót a **kijelölt** listája.

13. Amikor elkészült, válassza a felhasználók, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** hozzárendelése a kiválasztott felhasználói szerepkör kiválasztása panel.

15. Kattintson a **hozzárendelése** gombra kattintva rendelje hozzá az alkalmazás a kijelölt felhasználók számára.

Ha a kiépítés konfigurálva van, és már fut egy alkalmazáshoz, az új felhasználókat körülbelül 10 perc alatt kell kiépíteni egy alkalmazásba. A részletekért ellenőrizze a **naplókat** .

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Csoport kijelölése közvetlenül egy alkalmazáshoz rendszergazdaként

Ha egy vagy több csoportot szeretne közvetlenül egy alkalmazáshoz rendelni, kövesse az alábbi lépéseket:

1. Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7. Az alkalmazás betöltése után kattintson a **felhasználók és csoportok** elemre az alkalmazás bal oldali navigációs menüjében.

8. A **hozzárendelés hozzáadása** ablaktábla megnyitásához kattintson a **Hozzáadás** gombra a **felhasználók és csoportok** lista tetején.

9. Kattintson a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

10. Írja be a **teljes csoportnév** a csoport Önt érdeklő való hozzárendelése a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. A kurzort a **csoport** megjelenítéséhez a listában egy **jelölőnégyzet**. Jelölje be a jelölőnégyzetet, a csoport profilfénykép, vagy adja hozzá a felhasználót az embléma mellett a **kijelölt** listája.

12. **Választható lehetőség:** Ha szeretné **egynél több csoport hozzáadása**, írjon be egy másik **teljes csoportnév** be a **Keresés név vagy e-mail cím alapján** keresőmezőbe, és a jelölőnégyzet bejelölésével adja hozzá a csoport a **kijelölt** listája.

13. Ha befejezte a csoportok kiválasztásával, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** ablaktáblán válassza ki a szerepkör hozzárendelése a kiválasztott csoportokhoz.

15. Kattintson a **hozzárendelése** gombra az alkalmazás a kijelölt csoportokhoz hozzárendelni.

Ha a kiépítés konfigurálva van, és már fut egy alkalmazáshoz, a csoporton belül található új felhasználókat körülbelül 10 perc alatt kell kiépíteni egy alkalmazásba. A részletekért ellenőrizze a **naplókat** .

>[!IMPORTANT]
>A csoport neve és a csoport adatainak kiépítés a tagokon kívül, ha egyes alkalmazások esetében támogatott. A funkció engedélyezéséhez vagy letiltásához engedélyezze vagy tiltsa le a **kiépítés** lapon megjelenő csoportosítási objektumok **leképezését** . 
>
>

Ha a létesítési csoportok engedélyezve vannak, tekintse át az attribútumok leképezéseit, és győződjön meg arról, hogy a megfelelő mező használatban van a "megfeleltetési azonosító" beállításnál. Ez a megfeleltetési azonosító lehet a megjelenítendő név vagy az e-mail-alias. A csoport és a tagjai nincsenek kiépítve, ha a megfelelő tulajdonság üres vagy nincs feltöltve egy csoportra az Azure AD-ben.

## <a name="next-steps"></a>Következő lépések
[Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](user-provisioning.md)
