---
title: 'Rövid útmutató: Az Azure AD új jelszó kérésére vonatkozó önkiszolgáló folyamata'
description: Ebben az oktatóanyagban gyorsan konfigurálni fogja az Azure AD önkiszolgáló jelszó-visszaállítását, hogy a felhasználók vissza tudják állítani a saját jelszavukat
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a863c8fe81a08f00385cb267bf360994918fe82
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212106"
---
# <a name="quickstart-self-service-password-reset"></a>Gyors útmutató: Új jelszó önkiszolgáló kérése

Ebben az oktatóanyagban az önkiszolgáló jelszó-visszaállítás (SSPR) konfigurálásának menetével fog megismerkedni. Ez egy egyszerű módot kínál a rendszergazdáknak arra, hogy felhatalmazzák a felhasználókat a jelszavuk visszaállítására vagy fiókjuk zárolásának feloldására.

## <a name="prerequisites"></a>Előfeltételek

* Egy működő Azure AD-bérlő, legalább próbaverziós licenccel.
* Egy globális rendszergazdai jogosultsággal rendelkező fiók.
* Egy nem rendszergazdai jogosultsággal rendelkező felhasználó, aki ismeri a jelszavát, ha létre kell hoznia egy felhasználót, lásd [a gyors útmutatót: Új felhasználók hozzáadása a Azure Active Directoryhoz](../add-users-azure-active-directory.md).
* Egy tesztcsoport a teszteléshez, amelynek a nem rendszergazdai szintű tesztfelhasználó a tagja. Ha létre kell hoznia a csoportot, tekintse meg a [csoport létrehozását és a tagok hozzáadását az Azure Active Directoryban](../active-directory-groups-create-azure-portal.md) ismertető cikket.

## <a name="enable-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének engedélyezése

[A folyamat megtekintése videóként a YouTube-on](https://youtu.be/Pa0eyqjEjvQ)

1. A meglévő Azure AD-bérlőjében, az **Azure Portal** **Azure Active Directory** területén válassza az **Új jelszó kérése** lehetőséget.

2. A **Tulajdonságok** lapon, az **Új jelszó önkiszolgáló kérésének engedélyezése** lehetőség alatt válassza a **Kiválasztva** elemet.
    * A **Csoport kiválasztása** alatt válassza ki a cikk az előfeltételeket ismertető szakaszában létrehozott tesztcsoportot.
    * Kattintson a **Save** (Mentés) gombra.

3. A **Hitelesítési módszerek** lapon adja meg a következő beállításokat:
   * Az alaphelyzetbe állításhoz szükséges módszerek száma: **1**
   * Felhasználók által választható módszerek:
      * **E-mail**
      * **Mobile App Code (előzetes verzió)**
   * Kattintson a **Save** (Mentés) gombra.

     ![A SSPR hitelesítési módszereinek kiválasztása][Authentication]

4. A **Regisztráció** lapon adja meg a következő beállításokat:
   * A felhasználók regisztrációjának megkövetelése a bejelentkezéskor: **Igen**
   * Adja meg, hogy hány nap elteltével kéri a rendszer a felhasználóktól a hitelesítési adataik ismételt megerősítését: **365**

## <a name="test-self-service-password-reset"></a>Önkiszolgáló jelszó-visszaállítás tesztelése

Most pedig teszteljük az SSPR konfigurációját egy tesztfelhasználóval. Mivel a Microsoft szigorú hitelesítési előírásokat tartat be az Azure rendszergazdai fiókjainál, a rendszergazdai fiókkal végzett tesztelés megváltoztathatja az eredményt. A rendszergazdai jelszavakra vonatkozó szabályzatról a [jelszóházirendről szóló cikkünkben](concept-sspr-policy.md) talál további információt.

1. Nyisson meg egy új böngészőablakot InPrivate vagy inkognitó módban, és lépjen a következő oldalra: [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
2. Jelentkezzen be egy nem rendszergazdai szintű fiókkal, és regisztrálja a hitelesítéshez használt telefont.
3. Ha végzett, kattintson a **Minden rendben** gombra, és zárja be a böngészőablakot.
4. Nyisson meg egy új böngészőablakot InPrivate vagy inkognitó módban, és lépjen a következő oldalra: [https://aka.ms/sspr](https://aka.ms/sspr).
5. Írja be ide a nem rendszergazdai szintű tesztfelhasználó azonosítóját, végezze el a captcha-tesztet, majd kattintson a **Tovább** gombra.
6. Kövesse a jelszó-visszaállítás megerősítésének lépéseit.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az önkiszolgáló jelszóátállítás letiltása egyszerű feladat. Nyissa meg az Azure ad-bérlőt, és lépjen a **Tulajdonságok** > **jelszó-visszaállítás**elemre, majd válassza a **nincs lehetőséget** az **önkiszolgáló jelszó-visszaállítás**lehetőségnél.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megismerhette, hogyan konfigurálhat gyorsan önkiszolgáló jelszókérést a felhőbeli felhasználói számára. A bevezetés menetének részletesebb ismertetéséért folytassa a bevezetési útmutatóval.

> [!div class="nextstepaction"]
> [Önkiszolgáló jelszó-visszaállítás bevezetése](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Az elérhető Azure AD-hitelesítési módszerek és a szükséges mennyiség"
