---
title: A felhasználók megfelelővel lettek létrehozva az Azure AD katalógusából származó alkalmazásba való |} A Microsoft Docs
description: Ismerje meg, hogyan tudhatja meg, miért érdemes külön készletét felhasználók lettek létrehozva egy alkalmazáshoz, mint a várt
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a68a4caf3d32aa88a61a8e1639bdd08d67599d4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58083300"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>A felhasználók megfelelővel lettek létrehozva egy Azure AD katalógusából származó alkalmazásba

Mely felhasználók érvénybe lépnek az alkalmazásra elsősorban határozzák meg, milyen felhasználókkal és csoportokkal lett **hozzárendelt** az alkalmazáshoz.

Az alábbi forrásanyagokból megtudhatja, hogyan ellenőrizheti, hogy mely felhasználók és csoportok vannak hozzárendelve egy alkalmazást az Azure Active Directoryban.

## <a name="assign-a-user-directly-as-an-administrator"></a>Felhasználó hozzárendelése közvetlenül rendszergazdaként

Közvetlenül rendelni egy vagy több felhasználó egy alkalmazást, kövesse az alábbi lépéseket:

1. Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2. Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7. Ha az alkalmazás betöltött, kattintson a **felhasználók és csoportok** az alkalmazás bal oldali navigációs menüjében.

8. Megnyitásához a **hozzárendelés hozzáadása** ablaktáblán kattintson a **Hozzáadás** a gomb a **felhasználók és csoportok** listája.

9. Kattintson a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

10. Írja be a **teljes név** vagy **e-mail-cím** Önt érdeklő való hozzárendelése a felhasználó a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. A kurzort a **felhasználói** megjelenítéséhez a listában egy **jelölőnégyzet**. Jelölje be a jelölőnégyzetet, a felhasználó profilfényképének vagy adja hozzá a felhasználót az embléma mellett a **kijelölt** listája.

12. **Nem kötelező:** Ha szeretné **egynél több felhasználó hozzáadása**, írjon be egy másik **teljes név** vagy **e-mail-cím** be a **Keresés név vagy e-mail cím alapján** keresőmezőbe, majd kattintson a jelölőnégyzet bejelölésével adja hozzá a felhasználót a **kijelölt** listája.

13. Amikor elkészült, válassza a felhasználók, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** hozzárendelése a kiválasztott felhasználói szerepkör kiválasztása panel.

15. Kattintson a **hozzárendelése** gombra kattintva rendelje hozzá az alkalmazás a kijelölt felhasználók számára.

Ha a kiépítés van beállítva, és a egy alkalmazás fut, új felhasználókat kell létrehozni egy alkalmazás körülbelül 10 perc múlva. Ellenőrizze a **Auditnaplók** részleteiről.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Csoport hozzárendelése közvetlenül egy alkalmazás-rendszergazdaként

Közvetlenül egy alkalmazás hozzárendelése egy vagy több csoportot, kövesse az alábbi lépéseket:

1. Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2. Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3. Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4. Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5. Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6. Válassza ki a kívánt rendelje hozzá egy felhasználót a listáról az alkalmazást.

7. Ha az alkalmazás betöltött, kattintson a **felhasználók és csoportok** az alkalmazás bal oldali navigációs menüjében.

8. Megnyitásához a **hozzárendelés hozzáadása** ablaktáblán kattintson a **Hozzáadás** a gomb a **felhasználók és csoportok** listája.

9. Kattintson a **felhasználók és csoportok** a választó a **hozzárendelés hozzáadása** ablaktáblán.

10. Írja be a **teljes csoportnév** a csoport Önt érdeklő való hozzárendelése a **Keresés név vagy e-mail cím alapján** keresőmezőbe.

11. A kurzort a **csoport** megjelenítéséhez a listában egy **jelölőnégyzet**. Jelölje be a jelölőnégyzetet, a csoport profilfénykép, vagy adja hozzá a felhasználót az embléma mellett a **kijelölt** listája.

12. **Nem kötelező:** Ha szeretné **egynél több csoport hozzáadása**, írjon be egy másik **teljes csoportnév** be a **Keresés név vagy e-mail cím alapján** keresőmezőbe, és kattintson a jelölőnégyzetbe, ez a csoport hozzáadása az a **kijelölt** listája.

13. Ha befejezte a csoportok kiválasztásával, kattintson a **kiválasztása** gombra kattintva vegye fel a listára a felhasználók és csoportok hozzá kell rendelni az alkalmazást.

14. **Választható lehetőség:** kattintson a **Szerepkörválasztás** -választójában jelenítse a **hozzárendelés hozzáadása** ablaktáblán válassza ki a szerepkör hozzárendelése a kiválasztott csoportokhoz.

15. Kattintson a **hozzárendelése** gombra az alkalmazás a kijelölt csoportokhoz hozzárendelni.

Ha a kiépítés van beállítva, és a egy alkalmazás fut, új felhasználókat a csoportban lévő kell létrehozni egy alkalmazás körülbelül 10 perc múlva. Ellenőrizze a **Auditnaplók** részleteiről.

>[!IMPORTANT]
>Ha támogatja az egyes alkalmazások kiépítése a csoport nevét és a csoport részletei mellett a tagok. Engedélyezheti vagy letilthatja ezt a funkciót engedélyezésével vagy letiltásával a **leképezési** csoport objektumainak látható a **kiépítési** fülre. 
>
>

Csoportok kiépítése engedélyezve van, ha mindenképpen tekintse át a annak érdekében, hogy a megfelelő mezőben használatos a "egyező ID" attribútum-leképezéshez. Ez az egyező azonosító a megjelenítési név vagy e-mail-alias lehet. A csoportot és annak tagjait kiépítése nem történt-e a megfelelő tulajdonság üres vagy nem feltöltött csoport az Azure ad-ben.

## <a name="next-steps"></a>További lépések
[Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](user-provisioning.md)
