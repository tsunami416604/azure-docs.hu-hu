---
title: Az Azure AD SSPR próbaverziójának engedélyezése
description: Ebben az oktatóanyagban az új Azure AD-jelszó önkiszolgáló kérését fogja engedélyezni felhasználók egy tesztcsoportja számára
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: a86547ad3eddb57328a2a0358ac453c979b84d37
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163363"
---
# <a name="tutorial-complete-an-azure-ad-self-service-password-reset-pilot-roll-out"></a>Oktatóanyag: Új Azure AD-jelszó önkiszolgáló kérése próbaverziójának bevezetése

Ebben az oktatóanyagban engedélyezni fogjuk az új Azure AD-jelszó önkiszolgáló kérésének (SSPR) próbaverzióját a vállalatban, majd teszteljük egy nem rendszergazdai fiók használatával.

Fontos, hogy az új jelszó önkiszolgáló kérésének tesztelése kizárólag nem rendszergazdai fiók használatával végezhető el. A Microsoft kezeli a rendszergazdai fiókok új jelszó kérésére vonatkozó szabályzatát, és erősebb hitelesítési módszereket igényel. Ez a szabályzat nem engedélyezi biztonsági kérdések és válaszok használatát, és két módszert igényel új jelszó kérésére.

> [!div class="checklist"]
> * Új jelszó önkiszolgáló kérésének engedélyezése
> * Az SSPR tesztelése felhasználóként

## <a name="prerequisites"></a>Előfeltételek

* Globális rendszergazdai fiók

## <a name="enable-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy globális rendszergazdai fiókkal.
1. Keresse meg az **Azure Active Directoryt**, és válassza a **Jelszó alaphelyzetbe állítása** lehetőséget.
1. Első lépésként egy tesztcsoport esetében, a vállalat felhasználóinak egy kis része számára engedélyezze az új jelszó önkiszolgáló kérését.
   * A **Tulajdonságok** lapon, az **Új jelszó önkiszolgáló kérésének engedélyezése** lehetőség alatt válassza a **Kiválasztva** elemet, és válasszon egy tesztcsoportot.
      * Csak az Ön által kiválasztott Azure AD-csoport tagjai használhatják az SSPR funkciót. Amikor megvalósíthatósági próbát végez a funkcióval, ezt a beállítást javasoljuk egy meghatározott felhasználói csoporttal. Ebben az esetben támogatott a biztonsági csoportok beágyazása.
      * Győződjön meg róla, hogy a kiválasztott csoportban lévő felhasználók érvényes licenccel rendelkeznek.
   * Kattintson a **Mentés** gombra.
1. A **Hitelesítési módszerek** lapon:
   * Állítsa **Az új jelszó kéréséhez szükséges módszerek számát** **2**-re.
   * A **Felhasználók által választható módszerek** közül válassza azt, amelyet a vállalat engedélyezni szeretne. Ebben az oktatóanyagban jelölje be az **E-mail**, a **Mobiltelefon** és az **Irodai telefon** használatát engedélyező jelölőnégyzeteket.
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
   * Ebben az oktatóanyagban az **Ügyfélszolgálati hivatkozás testreszabása** beállítást **Nem** értéken hagyjuk.

Az új jelszó önkiszolgáló kérése konfigurálva lett a tesztcsoportban lévő felhőalapú felhasználók számára.

## <a name="test-sspr-as-a-user"></a>Az SSPR tesztelése felhasználóként

Az új jelszó önkiszolgáló kérésének teszteléséhez egy nem rendszergazdai jogosultságú tesztfelhasználóra van szükség, aki a tesztcsoport tagja. **Vegye figyelembe, hogy ha az Ön által használt fiókhoz rendszergazdai szerepkörök lettek hozzárendelve, akkor a hitelesítéshez használt módszer és szám eltérhet az eredetileg kiválasztottól, hiszen a rendszergazdai szabályzatot a Microsoft kezeli.**

1. Nyisson meg egy új böngészőablakot InPrivate vagy inkognitó módban.
1. Tesztfelhasználóként regisztráljon új jelszó önkiszolgáló kérésére a következő helyen található regisztrációs portálon: [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
1. Ugyanezzel a tesztfelhasználói fiókkal lépjen az új jelszó önkiszolgáló kéréséhez használható portálra: [https://aka.ms/sspr](https://aka.ms/sspr), majd az előző lépésben megadott információkkal próbáljon meg új jelszót kérni.
1. Az új jelszó kérésének sikeresnek kellene lennie.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az oktatóanyag keretében konfigurált funkciót a továbbiakban már nem szeretné használni, végezze el a következő módosításokat.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg az **Azure Active Directoryt**, és válassza a **Jelszó alaphelyzetbe állítása** lehetőséget.
1. A **Tulajdonságok** lapon, az **Új jelszó önkiszolgáló kérésének engedélyezése** lehetőség alatt válassza a **Senki** elemet.
1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban új Azure AD-jelszó önkiszolgáló kérését engedélyezte. Továbbléphet a következő oktatóanyagra, amelyből megtudhatja, hogyan integrálható az új jelszó önkiszolgáló kérésére szolgáló felület egy helyszíni Active Directory Domain Services-infrastruktúrába.

> [!div class="nextstepaction"]
> [Az SSPR helyszíni visszaírás integrációjának engedélyezése](tutorial-enable-writeback.md)
