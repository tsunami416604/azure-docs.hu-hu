---
title: "Teszt meghajtó engedélyezve van az Azure AD B2C webes alkalmazás |} Microsoft Docs"
description: "Teszt meghajtó jelentkezzen be, regisztráció, profil szerkesztése, és alaphelyzetbe állítja a jelszót felhasználói útvonal be egy tesztkörnyezetben az Azure AD B2C használatával"
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: mtillman
editor: PatAltimore
ms.assetid: 2ffb780d-2c51-4c2e-b8d6-39c40a81a77e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: patricka
ms.openlocfilehash: bc56da695145f396a2899fb9dc7add3af9a549e8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="test-drive-an-azure-ad-b2c-enabled-web-app"></a>Tesztelése egy Azure AD B2C engedélyezve van a webes alkalmazás

Az Azure Active Directory B2C az alkalmazás, az üzleti és a védett ügyfelek felhő Identitáskezelés biztosít. A gyors üzembe helyezés használ egy minta tennivaló alkalmazás bemutatása:

> [!div class="checklist"]
> * Jelentkezzen be egy egyéni bejelentkezési oldalt.
> * Jelentkezzen be egy közösségi identitásszolgáltató használatával.
> * Létrehozása és kezelése az Azure AD B2C fiók és felhasználói profil.
> * Hívja meg a webes API-k által az Azure AD B2C védett.

## <a name="prerequisites"></a>Előfeltételek

* [A Visual Studio 2017](https://www.visualstudio.com/downloads/) rendelkező a **ASP.NET és a webes fejlesztési** munkaterhelés. 
* Egy közösségi fiók vagy Facebook, Google, vagy a Twitteren.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>A minta letöltése

[Töltse le, vagy klónozza a mintaalkalmazást](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) a Githubról.

## <a name="run-the-app-in-visual-studio"></a>Futtassa az alkalmazást a Visual Studióban

A minta alkalmazás projekt mappában nyissa meg a `B2C-WebAPI-DotNet.sln` a Visual Studio megoldás. 

A megoldás egy Tennivaló lista mintaalkalmazás, amely két projektet:

* **TaskWebApp** – egy ASP.NET MVC webalkalmazás, ahol a felhasználók kezelhetik a Tennivaló lista.  
* **TaskService** – egy ASP.NET Web API háttéralkalmazás, amely kezeli a műveletek végre egy felhasználó tennivaló listaelemek. A webalkalmazás meghívja a webes API-t, és megjeleníti az eredményeket.

A gyors üzembe helyezés, a kell futtatnia mind a `TaskWebApp` és `TaskService` projektek egyszerre. 

1. A Visual Studio menüjében válassza **projektek > indítási projektek beállítása...** . 
2. Válassza ki **több kezdőprojekt** választógombot.
3. Módosítsa a **művelet** mindkét projektek **Start**. Kattintson az **OK** gombra.

![A Visual Studio indítási lap](media/active-directory-b2c-quickstarts-web-app/setup-startup-projects.png)

Válassza ki **Debug > Start Debugging** össze és futtathatja mindkét alkalmazáshoz. Minden alkalmazás saját böngészőlapon nyílik:

`https://localhost:44316/`– Ezen a lapon az ASP.NET-webalkalmazás. Ön a szolgáltatóosztályokkal közvetlenül az alkalmazás a gyorsindító.
`https://localhost:44332/`– Ezen a lapon a webes API-k által az ASP.NET-webalkalmazás.

## <a name="create-an-account"></a>Fiók létrehozása

Kattintson a **regisztráljon / bejelentkezés** indítsa el az ASP.NET-webalkalmazás hivatkozásra a **regisztráció vagy bejelentkezés** munkafolyamat. Egy fiók létrehozásakor egy meglévő közösségi identitás-szolgáltató fiókja vagy egy e-mail fiókot is használhatja. A gyors üzembe helyezés a vagy Facebook, Google, vagy a Twitteren közösségi identity provider fiókot használni.

![ASP.NET webes mintaalkalmazás](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

### <a name="sign-up-using-a-social-identity-provider"></a>Jelentkezzen egy közösségi identitásszolgáltató

Közösségi identitásszolgáltató használatával, kattintson a használni kívánt identitásszolgáltató gombjára. 

![Bejelentkezés vagy regisztráció szolgáltató](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

Hitelesítés (bejelentkezés) a közösségi fiókja hitelesítő adatait, és az alkalmazás az adatok olvasása a közösségi fiók használatával kell. Hozzáférés biztosítása, amelyet az alkalmazás profiladatok kérhetnek le a közösségi fiókot, amilyen például a nevét, és a település. 

Fejezze be a bejelentkezési folyamat az identitásszolgáltató számára. Például, ha úgy dönt, hogy a Twitter, a Twitter hitelesítő adatainak megadását, majd kattintson **bejelentkezés**.

![Hitelesítését és engedélyezését, közösségi fiók használatával](media/active-directory-b2c-quickstarts-web-app/twitter-authenticate-authorize-web.png)

Az új Azure AD B2C profil fiókadatok előre megadott, a közösségi fiók adataival.

A megjelenített név, beosztás és város mezők frissítése, és kattintson a **Folytatás**.  Az értékek használják az Azure AD B2C-felhasználói fiók profilját.

![Új fiók regisztrációs profil részleteit](media/active-directory-b2c-quickstarts-web-app/new-account-sign-up-profile-details-web.png)

Sikeres rendelkeznek:

> [!div class="checklist"]
> * A hitelesítést az identitásszolgáltató használatával.
> * Egy Azure AD B2C-felhasználói fiókot létrehozni. 

## <a name="edit-your-profile"></a>Szerkesztheti a profilját

Az Azure Active Directory B2C engedélyezése a felhasználók számára, frissítse a profilokat lehetőségeket kínál. A webes alkalmazás menüsávon kattintson a profil nevére, és válassza ki **profilszerkesztés** a létrehozott profil szerkesztése.

![Profil szerkesztése](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

Módosítsa a **megjelenített név** és **Város**.  Kattintson a **Folytatás** a profil frissítése.

![Profil frissítése](media/active-directory-b2c-quickstarts-web-app/update-profile-web.png)

Figyelje meg, hogy a megjelenített név, az oldal jobb felső részén található a frissített nevét jeleníti meg. 

## <a name="access-a-secured-web-api-resource"></a>A biztonságos webes API-erőforrás eléréséhez

Kattintson a **feladatlista** adja meg, és a Teendők lista módosítására. Az ASP.NET webalkalmazásként való kezelése a webes API erőforrás kérelmező engedély a felhasználó tennivaló listaelemek a műveletek végrehajtásához a kérelem olyan hozzáférési jogkivonatot tartalmazza. 

Adja meg a szöveget a **új elem** szövegmezőben. Kattintson a **Hozzáadás** hívható meg az Azure AD B2C biztonságos webes API, amely Tennivaló lista egyik eleme.

![Tennivalók Listaelem hozzáadása](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

Az Azure AD B2C felhasználói fiók segítségével sikeresen hívást egy jogosult az Azure AD B2C biztonságos webes API-t.

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezés használt minta segítségével próbálja más Azure AD B2C helyzeteket is beleértve:

* E-mail cím használatával új helyi fiók létrehozása.
* Helyi fiók jelszavának alaphelyzetbe állítása.

Ha készen áll a saját Azure AD B2C bérlő létrehozása elmélyedhet, és konfigurálja a minta futtatásához a saját a bérlőnek a használatával, próbálja ki az alábbi oktatóanyag.

> [!div class="nextstepaction"]
> [Egy ASP.NET-webalkalmazás létrehozása az Azure Active Directory B2C regisztráció, bejelentkezés, profil szerkesztése és a jelszó alaphelyzetbe állítása](active-directory-b2c-devquickstarts-web-dotnet-susi.md)