---
title: Oktatóanyag – felhasználói folyamatok létrehozása – Azure Active Directory B2C
description: Megtudhatja, hogyan hozhat létre felhasználói folyamatokat a Azure Portalban, hogy lehetővé tegye a regisztrációt, bejelentkezést és felhasználói profilok szerkesztését az alkalmazásaihoz Azure Active Directory B2Cban.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183567"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Oktatóanyag: felhasználói folyamatok létrehozása Azure Active Directory B2C

Előfordulhat, hogy az alkalmazásaiban [felhasználói folyamatok](user-flow-overview.md) vannak, amelyek lehetővé teszik a felhasználók számára, hogy regisztráljanak, jelentkezzenek be vagy kezelhetik a profiljaikat. A Azure Active Directory B2C (Azure AD B2C) bérlőben több különböző típusú felhasználói folyamatot is létrehozhat, és igény szerint használhatja azokat az alkalmazásokban. A felhasználói folyamatok az alkalmazások között újra felhasználhatók.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Regisztrációs és bejelentkezési felhasználói folyamat létrehozása
> * Profil szerkesztése felhasználói folyamat létrehozása
> * Jelszó-visszaállítási felhasználói folyamat létrehozása

Ez az oktatóanyag bemutatja, hogyan hozhat létre néhány javasolt felhasználói folyamatot a Azure Portal használatával. Ha további információt szeretne arról, hogyan állíthatja be az erőforrás-tulajdonosi jelszó hitelesítő adatait (ROPC) az alkalmazásban, tekintse meg [az erőforrás-tulajdonosi jelszó hitelesítő adatainak konfigurálása Azure ad B2Cban](configure-ropc.md)című témakört.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

[Regisztrálja](tutorial-register-applications.md) a létrehozni kívánt felhasználói folyamatok részét képező alkalmazásokat.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Regisztrációs és bejelentkezési felhasználói folyamat létrehozása

A regisztrációs és bejelentkezési felhasználói folyamat egyetlen konfigurációval kezeli a regisztrálási és a bejelentkezési élményt. Az alkalmazás felhasználói a környezettől függően a megfelelő útvonalat vezetik le.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.

    ![B2C-bérlő, címtár és előfizetés panel, Azure Portal](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. A Azure Portal keresse meg és válassza a **Azure ad B2C**lehetőséget.
1. A **házirendek**területen válassza a **felhasználói folyamatok (házirendek)** lehetőséget, majd válassza az **új felhasználói folyamat**elemet.

    ![Felhasználói folyamatok lap a portálon új felhasználói folyamat gomb kiemelve](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Az **ajánlott** lapon válassza a **regisztráció és bejelentkezés** felhasználói folyamat elemet.

    ![Válassza ki a felhasználói folyamat lapot, és jelölje ki a regisztrációs és bejelentkezési folyamat elemet](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Adja meg a felhasználói folyamat **nevét** . Például: *signupsignin1*.
1. Az **Identity Providers**esetében válassza az **e-mail regisztráció**lehetőséget.

    ![Felhasználói folyamat létrehozása lap Azure Portal a tulajdonságok kiemelve](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. **Felhasználói attribútumok és jogcímek**esetében válassza ki azokat a jogcímeket és attribútumokat, amelyeket szeretne összegyűjteni, és a felhasználótól a regisztráció során elküldeni. Válassza például a **továbbiak megjelenítése**lehetőséget, majd az **ország/régió**, a **megjelenítendő név**és az **Irányítószám**elemnél válassza az attribútumok és jogcímek lehetőséget. Kattintson az **OK** gombra.

    ![Attribútumok és jogcímek kiválasztása lap három jogcímek kiválasztásával](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. A felhasználói folyamat hozzáadásához kattintson a **Létrehozás** gombra. Az *B2C_1* előtagját a rendszer automatikusan hozzáfűzi a névhez.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Válassza ki a létrehozott felhasználói folyamatot az Áttekintés oldal megnyitásához, majd válassza a **felhasználói folyamat futtatása**lehetőséget.
1. Az **alkalmazás**lapon válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást. A **Válasz URL-címének** `https://jwt.ms`nak kell megjelennie.
1. Kattintson a **felhasználói folyamat futtatása**elemre, majd válassza a **regisztráció most**lehetőséget.

    ![Felhasználói folyamat futtatása lap a portálon a felhasználói folyamat futtatása gomb kiemelve](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Adjon meg egy érvényes e-mail-címet, kattintson az **ellenőrző kód küldése**elemre, adja meg a kapott ellenőrző kódot, majd válassza a **kód ellenőrzése**lehetőséget.
1. Adjon meg egy új jelszót, és erősítse meg a jelszót.
1. Válassza ki az országot és a régiót, adja meg a megjeleníteni kívánt nevet, adjon meg egy irányítószámot, majd kattintson a **Létrehozás**gombra. A rendszer visszaküldi a jogkivonatot a `https://jwt.ms`nek, és megjelenik Önnek.
1. Most már újra futtathatja a felhasználói folyamatot, és be kell tudnia jelentkezni a létrehozott fiókkal. A visszaadott jogkivonat tartalmazza az ország/régió, a név és az irányítószám mezőben kiválasztott jogcímeket.

## <a name="create-a-profile-editing-user-flow"></a>Profil szerkesztése felhasználói folyamat létrehozása

Ha engedélyezni szeretné a felhasználók számára a profil szerkesztését az alkalmazásban, használja a felhasználói folyamatokat szerkesztő profilt.

1. A Azure AD B2C bérlő áttekintése lapon válassza a **felhasználói folyamatok (házirendek)** lehetőséget, majd válassza az **új felhasználói folyamat**elemet.
1. Válassza ki a **profilt szerkesztő** felhasználói folyamatot az **ajánlott** lapon.
1. Adja meg a felhasználói folyamat **nevét** . Például: *profileediting1*.
1. Az **Identity Providers**esetében válassza a **helyi fiók bejelentkezési**lehetőséget.
1. **Felhasználói attribútumok**esetében válassza ki azokat az attribútumokat, amelyeket szeretné, hogy az ügyfél szerkeszteni tudja a profiljában. Válassza például a **továbbiak megjelenítése**lehetőséget, majd válassza ki a **megjelenítendő név** és a **beosztás**attribútumait és jogcímeit. Kattintson az **OK** gombra.
1. A felhasználói folyamat hozzáadásához kattintson a **Létrehozás** gombra. Az *B2C_1* előtagját a rendszer automatikusan hozzáfűzi a névhez.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Válassza ki a létrehozott felhasználói folyamatot az Áttekintés oldal megnyitásához, majd válassza a **felhasználói folyamat futtatása**lehetőséget.
1. Az **alkalmazás**lapon válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást. A **Válasz URL-címének** `https://jwt.ms`nak kell megjelennie.
1. Kattintson a **felhasználói folyamat futtatása**elemre, majd jelentkezzen be a korábban létrehozott fiókkal.
1. Most már lehetősége van módosítani a felhasználó megjelenítendő nevét és a beosztás címét. Kattintson a **Folytatás** gombra. A rendszer visszaküldi a jogkivonatot a `https://jwt.ms`nek, és megjelenik Önnek.

## <a name="create-a-password-reset-user-flow"></a>Jelszó-visszaállítási felhasználói folyamat létrehozása

Ha engedélyezni szeretné, hogy az alkalmazás felhasználói visszaállítsa a jelszavukat, jelszó-visszaállítási felhasználói folyamatot kell használnia.

1. A Azure AD B2C bérlő áttekintő menüjében válassza a **felhasználói folyamatok (házirendek)** lehetőséget, majd válassza az **új felhasználói folyamat**elemet.
1. Válassza a **jelszó alaphelyzetbe állítása** felhasználói folyamat lehetőséget az **ajánlott** lapon.
1. Adja meg a felhasználói folyamat **nevét** . Például: *passwordreset1*.
1. Az **Identity Providers**esetében engedélyezze a **jelszó alaphelyzetbe állítását e-mail-cím használatával**.
1. Az alkalmazás jogcímei területen kattintson a **továbbiak megjelenítése** lehetőségre, és válassza ki azokat a jogcímeket, amelyeket vissza szeretne állítani az alkalmazásnak visszaküldött engedélyezési jogkivonatokban. Válassza például a **Felhasználó objektumazonosítója** lehetőséget.
1. Kattintson az **OK** gombra.
1. A felhasználói folyamat hozzáadásához kattintson a **Létrehozás** gombra. Az *B2C_1* előtagját a rendszer automatikusan hozzáfűzi a névhez.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Válassza ki a létrehozott felhasználói folyamatot az Áttekintés oldal megnyitásához, majd válassza a **felhasználói folyamat futtatása**lehetőséget.
1. Az **alkalmazás**lapon válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást. A **Válasz URL-címének** `https://jwt.ms`nak kell megjelennie.
1. Kattintson a **felhasználói folyamat futtatása**elemre, ellenőrizze a korábban létrehozott fiók e-mail-címét, majd válassza a **Folytatás**lehetőséget.
1. Most lehetősége van a felhasználó jelszavának módosítására. Módosítsa a jelszót, és válassza a **Folytatás**lehetőséget. A rendszer visszaküldi a jogkivonatot a `https://jwt.ms`nek, és megjelenik Önnek.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Regisztrációs és bejelentkezési felhasználói folyamat létrehozása
> * Profil szerkesztése felhasználói folyamat létrehozása
> * Jelszó-visszaállítási felhasználói folyamat létrehozása

Következő lépésként ismerje meg, hogy miként adhat meg identitás-szolgáltatókat az alkalmazásaihoz, így lehetővé teszi a felhasználói bejelentkezést az Azure AD, az Amazon, a Facebook, a GitHub, a LinkedIn, a Microsoft vagy a Twitter használatával.

> [!div class="nextstepaction"]
> [Identitás-szolgáltatók hozzáadása az alkalmazásokhoz >](tutorial-add-identity-providers.md)