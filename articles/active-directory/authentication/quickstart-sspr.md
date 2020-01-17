---
title: Gyors útmutató – Azure AD önkiszolgáló jelszó-visszaállítás
description: Ebből a rövid útmutatóból megtudhatja, hogyan konfigurálhatja az Azure AD önkiszolgáló jelszó-visszaállítást, hogy a felhasználók visszaállítsák a saját jelszavukat, és csökkentsék az informatikai részleg terhelését.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 12/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd178f52665c77f03a48d87a9e73c9019390bb21
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154856"
---
# <a name="quickstart-configure-azure-active-directory-self-service-password-reset"></a>Gyors útmutató: Azure Active Directory önkiszolgáló jelszó-visszaállítás konfigurálása

Ebben a rövid útmutatóban a Azure Active Directory (AD) önkiszolgáló jelszó-visszaállítás (SSPR) konfigurálásával engedélyezheti a felhasználók számára, hogy visszaállítsák a jelszavukat, vagy fel tudják oldani a fiókjaikat. A SSPR segítségével a felhasználók az ügyfélszolgálat vagy a rendszergazda segítsége nélkül is visszaállíthatják saját hitelesítő adataikat. Ez a funkció lehetővé teszi a felhasználók számára, hogy további támogatásra való várakozás nélkül újra hozzáférjenek a fiókjához.

> [!IMPORTANT]
> Ez a rövid útmutató egy rendszergazdát mutat be, amely lehetővé teszi az önkiszolgáló jelszó-visszaállítást. Ha a végfelhasználó már regisztrálva van az önkiszolgáló jelszó-visszaállításhoz, és vissza kell kérnie a fiókját, lépjen a https://aka.ms/sspr.
>
> Ha az informatikai csapat nem engedélyezte a saját jelszavának alaphelyzetbe állítását, további segítségért forduljon az ügyfélszolgálathoz.

## <a name="prerequisites"></a>Előfeltételek

* Egy működő Azure AD-bérlő, legalább próbaverziós licenccel.
    * Ha szükséges, [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy globális rendszergazdai jogosultsággal rendelkező fiók.
* Egy nem rendszergazdai jogosultsággal rendelkező felhasználó, aki ismeri a jelszót, például *tesztfelhasználó*.
    * Ha létre kell hoznia egy felhasználót, tekintse meg a rövid útmutató [: új felhasználók hozzáadása a Azure Active Directoryhoz](../add-users-azure-active-directory.md)című témakört.
* Egy kísérleti csoport, amellyel tesztelhető, hogy a nem rendszergazda teszt felhasználó tagja-e, például *SSPR-test-Group*.
    * Ha csoportot kell létrehoznia, tekintse meg [a csoport létrehozása és a tagok hozzáadása a Azure Active Directory-ben](../active-directory-groups-create-azure-portal.md)című témakört.

## <a name="enable-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének engedélyezése

[A folyamat megtekintése videóként a YouTube-on](https://youtu.be/Pa0eyqjEjvQ)

1. A Azure Portal menüben vagy a **kezdőlapon** válassza a **Azure Active Directory** lehetőséget, majd válassza a **jelszó alaphelyzetbe állítása**lehetőséget.

1. A **Tulajdonságok** lapon, az **önkiszolgáló jelszó-visszaállítás engedélyezése**beállításnál válassza a **kiválasztott**lehetőséget.
1. Válassza a **csoport kiválasztása**lehetőséget, majd válassza ki a cikk előfeltételeinek részeként létrehozott kísérleti csoportot, például *SSPR-test-Group*. Ha elkészült, válassza a **Mentés**lehetőséget.
1. A **hitelesítési módszerek** lapon végezze el a következő beállításokat, majd válassza a **Mentés**lehetőséget:
    * Az új jelszó kéréséhez szükséges módszerek száma: **1**
    * Felhasználók által választható módszerek:
        * **Mobile App Code**
        * **E-mail**

    > [!div class="mx-imgBorder"]
    > ![A SSPR hitelesítési módszereinek kiválasztása][Authentication]

4. A **regisztráció** lapon végezze el a következő beállításokat, majd válassza a **Mentés**lehetőséget:
   * Felhasználói regisztráció megkövetelése bejelentkezéskor: **Igen**
   * A napok számának megadása, amely előtt a rendszer kéri a felhasználóktól a hitelesítési adataik ismételt megerősítését: **365**

## <a name="test-self-service-password-reset"></a>Önkiszolgáló jelszó-visszaállítás tesztelése

Most már lehetővé teszi a SSPR-konfiguráció tesztelését az előző szakaszban kiválasztott csoport részét képező tesztelési felhasználóval, például *tesztfelhasználó*. Mivel a Microsoft szigorú hitelesítési előírásokat tartat be az Azure rendszergazdai fiókjainál, a rendszergazdai fiókkal végzett tesztelés megváltoztathatja az eredményt. A rendszergazdai jelszavakra vonatkozó szabályzatról a [jelszóházirendről szóló cikkünkben](concept-sspr-policy.md) talál további információt.

1. Nyisson meg egy új böngészőablakot InPrivate vagy inkognitó módban, és lépjen a következő oldalra: [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
2. Jelentkezzen be egy nem rendszergazdai jogosultsággal rendelkező felhasználóval, például *tesztfelhasználó*, és regisztrálja a hitelesítési telefont.
3. Ha elkészült, jelölje be a jól látható gombra kattintva **, majd a** böngészőablak bezárásához.
4. Nyisson meg egy új böngészőablakot InPrivate vagy inkognitó módban, és lépjen a következő oldalra: [https://aka.ms/sspr](https://aka.ms/sspr).
5. Adja meg a nem rendszergazda felhasználók felhasználói AZONOSÍTÓját, például a *tesztfelhasználó*, a CAPTCHA karaktereit, majd kattintson a **tovább**gombra.
6. A jelszó alaphelyzetbe állításához kövesse az ellenőrzési lépéseket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az önkiszolgáló jelszó-visszaállítás letiltásához keresse meg és válassza ki **Azure Active Directory** a Azure Portal. Válassza a **jelszó alaphelyzetbe állítása**lehetőséget, majd válassza a **nincs** lehetőséget az **önkiszolgáló jelszó-visszaállítás beállításnál**. Ha elkészült, válassza a **Mentés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan konfigurálhatja az önkiszolgáló jelszó-visszaállítást a csak felhőalapú felhasználók számára. A bevezetés menetének részletesebb ismertetéséért folytassa a bevezetési útmutatóval.

> [!div class="nextstepaction"]
> [Önkiszolgáló jelszó-visszaállítás bevezetése](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Az elérhető Azure AD-hitelesítési módszerek és a szükséges mennyiség"

<!-- INTERNAL LINKS -->
[register-sspr]: ../user-help/active-directory-passwords-reset-register.md
[reset-password]: ../user-help/active-directory-passwords-update-your-own-password.md
