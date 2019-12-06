---
title: Az Azure AD SSPR próbaverziójának engedélyezése
description: Ebben az oktatóanyagban az új Azure AD-jelszó önkiszolgáló kérését fogja engedélyezni felhasználók egy tesztcsoportja számára
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 08/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecdde4ef12c6991fad53f2286ee462fec31606ae
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846283"
---
# <a name="tutorial-complete-an-azure-ad-self-service-password-reset-pilot-roll-out"></a>Oktatóanyag: Új Azure AD-jelszó önkiszolgáló kérése próbaverziójának bevezetése

Ebben az oktatóanyagban engedélyezni fogjuk az új Azure AD-jelszó önkiszolgáló kérésének (SSPR) próbaverzióját a vállalatban, majd teszteljük egy nem rendszergazdai fiók használatával.

Fontos, hogy az önkiszolgáló jelszó-visszaállítás bármilyen tesztelése a nem rendszergazdai fiókokkal történjen. A Microsoft kezeli a rendszergazdai fiókok új jelszó kérésére vonatkozó szabályzatát, és erősebb hitelesítési módszereket igényel. Ez a szabályzat nem engedélyezi biztonsági kérdések és válaszok használatát, és két módszert igényel új jelszó kérésére.

> [!div class="checklist"]
> * Új jelszó önkiszolgáló kérésének engedélyezése
> * Az SSPR tesztelése felhasználóként

## <a name="prerequisites"></a>Előfeltételek

* Globális rendszergazdai fiók

## <a name="enable-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy globális rendszergazdai fiókkal.
1. Keresse meg az **Azure Active Directoryt**, és válassza a **Jelszó alaphelyzetbe állítása** lehetőséget.
1. Első lépésként egy tesztcsoport esetében, a vállalat felhasználóinak egy kis része számára engedélyezze az új jelszó önkiszolgáló kérését.
   * A **Tulajdonságok** lapon, az **önkiszolgáló jelszó-visszaállítás engedélyezése**lehetőségnél válassza a **kijelölés**lehetőséget, és válasszon egy kísérleti csoportot.
      * Csak az Ön által kiválasztott Azure AD-csoport tagjai használhatják az SSPR funkciót. Amikor megvalósíthatósági próbát végez a funkcióval, ezt a beállítást javasoljuk egy meghatározott felhasználói csoporttal. Ebben az esetben támogatott a biztonsági csoportok beágyazása.
      * Győződjön meg róla, hogy a kiválasztott csoportban lévő felhasználók érvényes licenccel rendelkeznek.
   * Kattintson a **Mentés** gombra.
1. A **Hitelesítési módszerek** lapon:
   * Állítsa be az **1** értékre való **visszaállításhoz szükséges metódusok számát** .
   * A **Felhasználók által választható módszerek** közül válassza azt, amelyet a vállalat engedélyezni szeretne. Ebben az oktatóanyagban jelölje be a jelölőnégyzeteket az **e-mailek**, a **mobiltelefon**, az **Office Phone**, a **Mobile App Notification**és a **Mobile App Code**engedélyezéséhez.
   * Kattintson a **Mentés** gombra.
1. A **Regisztráció** lapon:
   * Válassza az **Igen** lehetőséget a **Felhasználói regisztráció megkövetelése bejelentkezéskor** beállításnál.
   * **A napok száma, amely előtt a rendszer kéri a felhasználóktól a hitelesítő adataik ismételt megerősítését** értékét állítsa **180**-ra.
   * Kattintson a **Mentés** gombra.
1. Az **Értesítések** lapon:
   * A **Felhasználók értesítése új jelszó kérésekor** beállítást állítsa **Igen** értékre.
   * A **Minden rendszergazda kapjon értesítést, ha más rendszergazdák új jelszót kérnek** beállítást állítsa **Igen** értékre.
1. A **Testreszabás** lapon:
   * A Microsoft azt javasolja, hogy az **Ügyfélszolgálati hivatkozás testreszabása** beállítást állítsa **Igen** értékre, illetve az **Egyéni ügyfélszolgálati e-mail-cím vagy URL-cím** mezőben adjon meg egy olyan e-mail-címet vagy weblap URL-címet, amelyen a felhasználók további segítséget kaphatnak a vállalattól.
   * Ebben az oktatóanyagban az **ügyfélszolgálati hivatkozás** beállítása **nem**értékre van állítva.

Az új jelszó önkiszolgáló kérése konfigurálva lett a tesztcsoportban lévő felhőalapú felhasználók számára.

## <a name="test-sspr-as-a-user"></a>Az SSPR tesztelése felhasználóként

Az új jelszó önkiszolgáló kérésének teszteléséhez egy nem rendszergazdai jogosultságú tesztfelhasználóra van szükség, aki a tesztcsoport tagja. **Ha olyan fiókot használ, amely az ahhoz hozzárendelt rendszergazdai szerepkörökkel rendelkezik, akkor a hitelesítési módszerek és a számok eltérőek lehetnek, mint amit a Microsoft felügyel a rendszergazdai házirendben.**

1. Nyisson meg egy új böngészőablakot InPrivate vagy inkognitó módban.
1. Tesztfelhasználóként regisztráljon új jelszó önkiszolgáló kérésére a következő helyen található regisztrációs portálon: [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
1. Ugyanezzel a tesztfelhasználói fiókkal lépjen az új jelszó önkiszolgáló kéréséhez használható portálra: [https://aka.ms/sspr](https://aka.ms/sspr), majd az előző lépésben megadott információkkal próbáljon meg új jelszót kérni.
1. Az új jelszó kérésének sikeresnek kellene lennie.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az oktatóanyag keretében konfigurált funkciót a továbbiakban már nem szeretné használni, végezze el a következő módosításokat.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Keresse meg az **Azure Active Directoryt**, és válassza a **Jelszó alaphelyzetbe állítása** lehetőséget.
1. A **Tulajdonságok** lapon, az **Új jelszó önkiszolgáló kérésének engedélyezése** lehetőség alatt válassza a **Senki** elemet.
1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban új Azure AD-jelszó önkiszolgáló kérését engedélyezte. Továbbléphet a következő oktatóanyagra, amelyből megtudhatja, hogyan integrálható az új jelszó önkiszolgáló kérésére szolgáló felület egy helyszíni Active Directory Domain Services-infrastruktúrába.

> [!div class="nextstepaction"]
> [Az SSPR helyszíni visszaírás integrációjának engedélyezése](tutorial-enable-writeback.md)
