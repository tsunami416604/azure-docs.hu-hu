---
title: "Teszt meghajtó egy Azure AD B2C webalkalmazás |} Microsoft Docs"
description: "Teszt meghajtó jelentkezzen be, regisztráció, profil szerkesztése, és alaphelyzetbe állítja a jelszót felhasználói útvonal be egy tesztkörnyezetben az Azure AD B2C használatával"
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: krassk
editor: PatAltimore
ms.assetid: 2ffb780d-2c51-4c2e-b8d6-39c40a81a77e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: 2a51f15fd38a901548dcf4c56922f9715c328463
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="test-drive-a-web-application-configured-with-azure-ad-b2c"></a>Azure AD B2C konfigurált webalkalmazás tesztelése

Az Azure Active Directory B2C az alkalmazás, az üzleti és a védett ügyfelek felhő Identitáskezelés biztosít.  A gyors üzembe helyezés használ egy minta tennivaló alkalmazás bemutatása:

* Használja a **regisztráció vagy bejelentkezés** házirend létrehozásához vagy a közösségi identitásszolgáltató vagy e-mail cím használatával helyi fiókkal jelentkezzen be. 
* Az Azure AD B2C létrehozhatja és szerkesztheti a Tennivalólista elemein által védett API felület meghívásakor.

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [Visual Studio 2017](https://www.visualstudio.com/downloads/) szoftvert a következő számítási feladatokkal:
    - **ASP.NET és webfejlesztés**

* Egy közösségi fiók vagy Facebook, Google, vagy a Twitteren. Ha egy közösségi fiók nem rendelkezik, érvényes e-mail címet szükség.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>A minta letöltése

[Töltse le, vagy klónozza a mintaalkalmazást](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) a Githubról.

## <a name="run-the-app-in-visual-studio"></a>Futtassa az alkalmazást a Visual Studióban

A minta alkalmazás projekt mappában nyissa meg a `B2C-WebAPI-DotNet.sln` a Visual Studio megoldás. 

A megoldás két projektet tartalmaz:

* **TaskWebApp** – egy ASP.NET MVC webalkalmazás, ahol a felhasználók kezelhetik a Tennivaló lista.  
* **TaskService** – a felhasználó tennivaló listaelemek végre egy ASP.NET Web API háttéralkalmazás, amely minden CRUD műveleteihez kezeli. A webalkalmazás hívja az API-t, és megjeleníti az eredményeket.

A gyors üzembe helyezés, a kell futtatnia mind a `TaskWebApp` és `TaskService` projektek egyszerre. 

1. A Megoldáskezelőben kattintson a jobb gombbal a megoldás, és válassza **indítási projektek beállítása...** . 
2. Válassza ki **több kezdőprojekt** választógombot.
3. Módosítsa a **művelet** mindkét projektek **Start**. Kattintson az **OK** gombra.

![A Visual Studio indítási lap](media/active-directory-b2c-quickstarts-web-app/setup-startup-projects.png)

Válassza ki **Debug > Start Debugging** össze és futtathatja mindkét alkalmazáshoz. Minden alkalmazás saját böngészőlapon nyílik:

* `https://localhost:44316/`– Ezen a lapon az ASP.NET-webalkalmazás. Ön a szolgáltatóosztályokkal közvetlenül az alkalmazás a gyorsindító.
* `https://localhost:44332/`– Ezen a lapon a webes API-k által az ASP.NET-webalkalmazás.

## <a name="create-an-account"></a>Fiók létrehozása

Kattintson a **regisztráljon / bejelentkezés** indítsa el az ASP.NET-webalkalmazás hivatkozásra a **regisztráció vagy bejelentkezés** munkafolyamat. Egy fiók létrehozásakor egy meglévő közösségi identitás-szolgáltató fiókja vagy egy e-mail fiókot is használhatja.

![ASP.NET webes mintaalkalmazás](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

### <a name="sign-up-using-a-social-identity-provider"></a>Jelentkezzen egy közösségi identitásszolgáltató

Közösségi identitásszolgáltató használatával, kattintson a használni kívánt identitásszolgáltató gombjára. Ha e-mail címmel szeretné végrehajtani, Ugrás a [jelentkezzen egy e-mail címet](#sign-up-using-an-email-address) szakasz.

![Bejelentkezés vagy regisztráció szolgáltató](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

Hitelesítés (bejelentkezés) a közösségi fiókja hitelesítő adatait, és az alkalmazás az adatok olvasása a közösségi fiók használatával kell. Hozzáférés biztosítása, amelyet az alkalmazás profiladatok kérhetnek le a közösségi fiókot, amilyen például a nevét, és a település. 

![Hitelesítését és engedélyezését, közösségi fiók használatával](media/active-directory-b2c-quickstarts-web-app/twitter-authenticate-authorize-web.png)

Fejezze be a bejelentkezési folyamat az identitásszolgáltató számára. Kattintson például **bejelentkezés** Twitter gombra.

Az új profil fiókadatok előre megadott, a közösségi fiók adataival.

![Új fiók regisztrációs profil részleteit](media/active-directory-b2c-quickstarts-web-app/new-account-sign-up-profile-details-web.png)

A megjelenített név, beosztás és város mezők frissítése, és kattintson a **Folytatás**.  Az értékek használják az Azure AD B2C-felhasználói fiók profilját.

Sikeresen létrehozott egy új Azure AD B2C által használt felhasználói fiók egy identitásszolgáltatóval. 

Következő lépés: [megtekintéséhez a jogcímek Jump](#view-your-claims) szakasz.

### <a name="sign-up-using-an-email-address"></a>Jelentkezzen egy e-mail címet

Ha a hitelesítés egy közösségi fiókot használja, egy érvényes e-mail címet használ az Azure AD B2C-felhasználói fiókot is létrehozhat. Egy Azure AD B2C helyi felhasználói fiókot az Azure Active Directory, az identitás-szolgáltatóként. Az e-mail cím használatához kattintson a **nincs fiókja? Feliratkozás most** hivatkozásra.

![Bejelentkezés vagy regisztráció e-mail](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-email-web.png)

Adjon meg egy érvényes e-mail címet, és kattintson a **ellenőrző kód küldése**. Érvényes e-mail címet kell az Azure AD B2C megkapta az ellenőrzőkódot. 

Adja meg az e-mailben kap, és kattintson a **ellenőrizze a kódot**.

A profiladatok, és kattintson **létrehozása**.

![Iratkozzon fel az új e-mail fiók](media/active-directory-b2c-quickstarts-web-app/sign-up-new-account-profile-email-web.png)

Sikeresen létrehozott egy új Azure AD B2C helyi felhasználói fiókot.

## <a name="reset-your-password"></a>Jelszó alaphelyzetbe állítása

Ha létrehozott egy e-mail címet a fiókját, az Azure AD B2C rendelkezik funkció engedélyezése a felhasználók visszaállíthassák a jelszavukat. A létrehozott profil szerkesztéséhez kattintson a menüsávon a profil nevére, és válassza ki **jelszó alaphelyzetbe állítása**.

Igazolja vissza e-mail címét, írja be azt, majd **ellenőrző kód küldése**. Egy megerősítési kódot küldi az e-mail címét.

Adja meg az e-mailben kapott, és kattintson a **ellenőrizze a kódot**.

Az e-mail cím ellenőrzése után kattintson **Folytatás**.

Megadja az új jelszót, majd **Folytatás**.

## <a name="view-your-claims"></a>A jogcímek megtekintése

Kattintson a **jogcímek** a webes alkalmazás menüsávon a jogcímek, az utolsó művelettel társított megtekintéséhez. 

![Iratkozzon fel az új e-mail fiók](media/active-directory-b2c-quickstarts-web-app/view-claims-sign-up-web.png)

Ebben a példában az utolsó művelet volt a *bejelentkezés vagy regisztráció* tapasztalhat. Figyelje meg a **jogcím típusa** `http://schemas.microsoft.com/claims/authnclassreference` van `b2c_1_susi` regisztráció vagy bejelentkezés alkalmával, amely jelzi, az utolsó művelet történt. Ha az utolsó művelet volt a jelszó alaphelyzetbe állítása, az **jogcím típusa** lenne `b2c_1_reset`.

## <a name="edit-your-profile"></a>Szerkesztheti a profilját

Az Azure Active Directory B2C engedélyezése a felhasználók számára, frissítse a profilokat lehetőségeket kínál. A webes alkalmazás menüsávon kattintson a profil nevére, és válassza ki **profilszerkesztés** a létrehozott profil szerkesztése.

![Profil szerkesztése](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

Módosítsa a **megjelenített név** és **Város**.  Kattintson a **Folytatás** a profil frissítése.

![Profil frissítése](media/active-directory-b2c-quickstarts-web-app/update-profile-web.png)

Figyelje meg a megjelenített név frissítések az oldal jobb felső részében a nevének módosítása után. 

Kattintson a **jogcímek**. Végrehajtott módosítások **megjelenített név** és **Város** jelennek meg a jogcímeket.

![Nézet jogcímek](media/active-directory-b2c-quickstarts-web-app/view-claims-update-web.png)

 Figyelje meg a **jogcím típusa** `http://schemas.microsoft.com/claims/authnclassreference` frissített `b2c_1_edit_profile` jelző a legutóbbi műveletet végrehajtani egy profil szerkesztése. Vegye figyelembe azt is, az új értékekkel kell a nevét és a település *CFO S.* és *Budapest*.

## <a name="access-a-resource"></a>Elért egy erőforrást

Kattintson a **feladatlista** adja meg, és a Teendők lista módosítására. Az ASP.NET webalkalmazásként való kezelése a webes API erőforrás kérelmező engedély a felhasználó tennivaló listaelemek a műveletek végrehajtásához a kérelem olyan hozzáférési jogkivonatot tartalmazza. 

Adja meg a szöveget a **új elem** szövegmezőben. Kattintson a **Hozzáadás** hívható meg az Azure AD B2C biztonságos webes API, amely Tennivaló lista egyik eleme.

![Tennivalók Listaelem hozzáadása](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

## <a name="other-scenarios"></a>Egyéb forgatókönyvek

Más esetekben kipróbálására a következők:

* Jelentkezzen ki az alkalmazáshoz, és kattintson **feladatlista**. Figyelje meg, hogyan kéri a bejelentkezéshez, és tárolja a rendszer, a lista elemeit. 
* Hozzon létre egy új fiókot, különböző típusú fiókot használja. Például használja a közösségi identitásszolgáltató, ha a fiókot egy e-mail címet korábban létrehozott.

## <a name="next-steps"></a>Következő lépések

A következő lépés, hogy a saját Azure AD B2C bérlő létrehozása és konfigurálása a minta futtatásához a bérlőnek a használatával. 

> [!div class="nextstepaction"]
> [Azure Active Directory B2C-bérlő létrehozása az Azure-portálon](active-directory-b2c-get-started.md)