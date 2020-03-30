---
title: Oktatóanyag – Felhasználói folyamatok létrehozása – Azure Active Directory B2C
description: Megtudhatja, hogyan hozhat létre felhasználói folyamatokat az Azure Portalon a regisztráció, a bejelentkezés és a felhasználói profil szerkesztésének engedélyezéséhez az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 51adbb74635f66ca86347b536dc2607566dcb725
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264244"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Oktatóanyag: Felhasználói folyamatok létrehozása az Azure Active Directory B2C-ben

Az alkalmazásokban előfordulhat, hogy [a felhasználói folyamatok,](user-flow-overview.md) amelyek lehetővé teszik a felhasználók számára, hogy regisztráljon, jelentkezzen be, vagy kezelheti a profilt. Az Azure Active Directory B2C (Azure AD B2C) bérlőjében több különböző típusú felhasználói folyamatot hozhat létre, és szükség szerint használhatja őket az alkalmazásokban. A felhasználói folyamatok újra felhasználhatók az alkalmazások között.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Regisztrációs és bejelentkezési felhasználói folyamat létrehozása
> * Profilszerkesztő felhasználói folyamat létrehozása
> * Jelszó-visszaállítási felhasználói folyamat létrehozása

Ez az oktatóanyag bemutatja, hogyan hozhat létre néhány ajánlott felhasználói folyamatot az Azure Portal használatával. Ha az erőforrás-tulajdonosi jelszó-hitelesítő adatok (ROPC) folyamatának beállításával kapcsolatos információkat keres az alkalmazásban, olvassa [el az Erőforrás-tulajdonos jelszóhitelesítő adatainak konfigurálása az Azure AD B2C-ben című témakört.](configure-ropc.md)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

[Regisztrálja a](tutorial-register-applications.md) létrehozni kívánt felhasználói folyamatok részét beálló alkalmazásokat.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Regisztrációs és bejelentkezési felhasználói folyamat létrehozása

A regisztrációs és bejelentkezési felhasználói folyamat egyetlen konfigurációval kezeli a regisztrációs és a bejelentkezési élményeket is. Az alkalmazás felhasználói a környezettől függően a helyes úton haladnak.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + Előfizetés** ikont a portál eszköztárán, majd válassza ki az Azure AD B2C-bérlőt tartalmazó könyvtárat.

    ![B2C-bérlő, címtár és előfizetés ablaktábla, Azure Portal](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. Az Azure Portalon keresse meg és válassza ki **az Azure AD B2C parancsot.**
1. A **Házirendek**csoportban válassza a **Felhasználói folyamatok (házirendek)** lehetőséget, majd az Új felhasználói **folyamat**lehetőséget.

    ![Felhasználói folyamatok lap a portálon az Új felhasználói folyamat gomb kiemelve](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Az **Ajánlott** lapon válassza a **Regisztráció és bejelentkezés** felhasználói folyamatot.

    ![Felhasználói folyamatlap kiválasztása kiemelt Regisztráció és bejelentkezési folyamat beállítással](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Adja meg a felhasználói folyamat **nevét.** Például *signupsignin1*.
1. **Identitásszolgáltatók**esetén válassza az **E-mail regisztráció**lehetőséget.

    ![Felhasználói folyamatlap létrehozása az Azure Portalon kiemelt tulajdonságokkal](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. **Felhasználói attribútumok és jogcímek**esetén válassza ki azokat a jogcímeket és attribútumokat, amelyeket a regisztráció során össze szeretne gyűjteni és el küldeni a felhasználótól. Válassza például a **Tovább megjelenítése**lehetőséget, majd válassza ki az **Ország/régió,** **a Megjelenítendő név**és az Irányítószám attribútumait és **jogcímeit.** Kattintson az **OK** gombra.

    ![Attribútumok és jogcímek kijelölési oldala három jogcím kijelölve](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. A felhasználói folyamat hozzáadásához kattintson a **Létrehozás** gombra. A *B2C_1* előtag automatikusan hozzáfűzi a nevet.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Jelölje ki a létrehozott felhasználói folyamatot az áttekintő lap megnyitásához, majd válassza **a Felhasználói folyamat futtatása**lehetőséget.
1. Az **alkalmazáshoz**válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie. `https://jwt.ms`
1. Kattintson a Felhasználói folyamat futtatása (Futtatás) **gombra,** majd a **Regisztráció most gombra.**

    ![Felhasználói folyamat lap futtatása a portálon a Kiemelt Felhasználói folyamat futtatása gombbal](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Adjon meg egy érvényes e-mail címet, kattintson az **Ellenőrző kód küldése**gombra, adja meg a kapott ellenőrző kódot, majd válassza a Kód ellenőrzése **lehetőséget.**
1. Adjon meg egy új jelszót, és erősítse meg a jelszót.
1. Válassza ki az országot és a régiót, írja be a megjeleníteni kívánt nevet, adjon meg egy irányítószámot, majd kattintson a **Létrehozás gombra.** A jogkivonat `https://jwt.ms` visszakerül, és meg kell jelennie.
1. Most már újra futtathatja a felhasználói folyamatot, és a létrehozott fiókkal kell bejelentkeznie. A visszaküldött jogkivonat tartalmazza az ország/régió, név és irányítószám közül kiválasztott jogcímeket.

## <a name="create-a-profile-editing-user-flow"></a>Profilszerkesztő felhasználói folyamat létrehozása

Ha engedélyezni szeretné a felhasználóknak, hogy módosítsák a profiljukat az alkalmazásban, használjon profilszerkesztő felhasználói folyamatot.

1. Az Azure AD B2C bérlői áttekintő lap menüjében válassza a **Felhasználói folyamatok (házirendek)** lehetőséget, majd az **Új felhasználói folyamat**lehetőséget.
1. Válassza a **Profilszerkesztés** felhasználói folyamatát az **Ajánlott** lapon.
1. Adja meg a felhasználói folyamat **nevét.** Például *profileediting1*.
1. **Identitásszolgáltatók**esetén válassza a **Helyi fiók signin**lehetőséget.
1. A **Felhasználói attribútumok**mezőben válassza ki azokat az attribútumokat, amelyeket az ügyfélnek a profiljában szerkeszthet. Válassza például a **Tovább megjelenítése**lehetőséget, majd válassza ki a **Megjelenítendő név** és a Beosztás attribútumait és jogcímeit. **Job title** Kattintson az **OK** gombra.
1. A felhasználói folyamat hozzáadásához kattintson a **Létrehozás** gombra. A *B2C_1* előtag automatikusan hozzáfűzi a nevet.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Jelölje ki a létrehozott felhasználói folyamatot az áttekintő lap megnyitásához, majd válassza **a Felhasználói folyamat futtatása**lehetőséget.
1. Az **alkalmazáshoz**válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie. `https://jwt.ms`
1. Kattintson **a Felhasználói folyamat futtatása**elemre, majd jelentkezzen be a korábban létrehozott fiókkal.
1. Most lehetősége van arra, hogy módosítsa a felhasználó megjelenítendő nevét és beosztását. Kattintson a **Folytatás** gombra. A jogkivonat `https://jwt.ms` visszakerül, és meg kell jelennie.

## <a name="create-a-password-reset-user-flow"></a>Jelszó-visszaállítási felhasználói folyamat létrehozása

Ha engedélyezni szeretné az alkalmazás felhasználóinak a jelszó alaphelyzetbe állítását, használjon jelszó-visszaállítási felhasználói folyamatot.

1. Az Azure AD B2C bérlői áttekintése menüben válassza a **Felhasználói folyamatok (házirendek)** lehetőséget, majd az **Új felhasználói folyamat**lehetőséget.
1. Válassza a **Jelszó alaphelyzetbe állítása** felhasználói folyamatot az **Ajánlott** lapon.
1. Adja meg a felhasználói folyamat **nevét.** Például *passwordreset1*.
1. **Identitásszolgáltatók**esetén engedélyezze **a Jelszó alaphelyzetbe állítását e-mail címmel.**
1. Az Alkalmazásjogcímek csoportban kattintson a **Több megjelenítése** elemre, és válassza ki a visszaküldeni kívánt jogcímeket az alkalmazásnak visszaküldött engedélyezési jogkivonatokban. Válassza például a **Felhasználó objektumazonosítója** lehetőséget.
1. Kattintson az **OK** gombra.
1. A felhasználói folyamat hozzáadásához kattintson a **Létrehozás** gombra. A *B2C_1* előtag automatikusan hozzáfűzi a nevet.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Jelölje ki a létrehozott felhasználói folyamatot az áttekintő lap megnyitásához, majd válassza **a Felhasználói folyamat futtatása**lehetőséget.
1. Az **alkalmazáshoz**válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie. `https://jwt.ms`
1. Kattintson a **Felhasználói folyamat futtatása**gombra, ellenőrizze a korábban létrehozott fiók e-mail címét, és válassza a **Folytatás**gombot.
1. Most lehetősége van arra, hogy módosítsa a felhasználó jelszavát. Módosítsa a jelszót, és válassza a **Folytatás**lehetőséget. A jogkivonat `https://jwt.ms` visszakerül, és meg kell jelennie.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan:

> [!div class="checklist"]
> * Regisztrációs és bejelentkezési felhasználói folyamat létrehozása
> * Profilszerkesztő felhasználói folyamat létrehozása
> * Jelszó-visszaállítási felhasználói folyamat létrehozása

Ezután ismerje meg, hogyan ad hozzá identitásszolgáltatókat az alkalmazásokhoz, hogy lehetővé tegye a felhasználói bejelentkezést olyan szolgáltatókkal, mint az Azure AD, az Amazon, a Facebook, a GitHub, a LinkedIn, a Microsoft vagy a Twitter.

> [!div class="nextstepaction"]
> [Identitásszolgáltatók hozzáadása az alkalmazásokhoz >](tutorial-add-identity-providers.md)