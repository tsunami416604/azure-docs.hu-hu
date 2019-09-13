---
title: Oktatóanyag – hitelesítés engedélyezése webalkalmazásban – Azure Active Directory B2C
description: Arra vonatkozó útmutató, hogyan használhatja az Azure Active Directory B2C-t felhasználói bejelentkezés biztosításához egy ASP.NET-es webalkalmazás esetén.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 09/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 2066a7848efaf067dddde3d5db1decfc88d94436
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914219"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Oktatóanyag: Hitelesítés engedélyezése webalkalmazásban Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan használhatja a Azure Active Directory (Azure AD) B2C-t a bejelentkezéshez, és hogyan regisztrálhat felhasználókat egy ASP.NET-webalkalmazásban. Azure AD B2C lehetővé teszi az alkalmazások számára, hogy a nyílt szabványos protokollok használatával hitelesítsék a közösségi fiókokat, a vállalati fiókokat és a Azure Active Directory fiókokat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az alkalmazás frissítése Azure AD B2C
> * A minta beállítása az alkalmazás használatára
> * Regisztráció a felhasználói folyamat használatával

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Felhasználói folyamatokat hozhat létre](tutorial-create-user-flows.md) , amelyekkel engedélyezheti az alkalmazás felhasználói élményét.
* Telepítse a [Visual Studio 2019](https://www.visualstudio.com/downloads/) alkalmazást a **ASP.net és a webes fejlesztési** munkaterheléssel.

## <a name="update-the-application"></a>Az alkalmazás frissítése

Az előfeltételek részeként elvégzett oktatóanyagban egy webalkalmazást adott hozzá Azure AD B2C. Az oktatóanyagban szereplő példával való kommunikáció engedélyezéséhez hozzá kell adnia egy átirányítási URI-t az alkalmazáshoz Azure AD B2C.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza az **alkalmazások**lehetőséget, majd válassza ki a *webapp1* alkalmazást.
5. A **Válasz URL-cím**területen adja hozzá `https://localhost:44316`a címet.
6. Kattintson a **Mentés** gombra.
7. A Tulajdonságok lapon jegyezze fel az alkalmazás AZONOSÍTÓját, amelyet a webalkalmazás konfigurálásakor használni fog.
8. Válassza a **kulcsok**lehetőséget, válassza a **kulcs generálása**, majd a **Mentés**lehetőséget. Jegyezze fel a webalkalmazás konfigurálásakor használni kívánt kulcsot.

## <a name="configure-the-sample"></a>A minta konfigurálása

Ebben az oktatóanyagban egy olyan mintát konfigurál, amelyet letöltheti a GitHubról. A minta a ASP.NET használatával egyszerű feladatlistát biztosít. A minta a [Microsoft OWIN köztes összetevőket](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/)használja. [Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip), vagy a klónozza a GitHubon található mintát. A mintafájlt egy olyan mappába kell kibontani, ahol a teljes elérési út rövidebb, mint 260 karakter.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

A következő két projekt szerepel a minta megoldásban:

- **TaskWebApp** – Feladatlista létrehozása és szerkesztése. A minta a **regisztrációs vagy bejelentkezési** felhasználói folyamat használatával regisztrálja és bejelentkezik a felhasználók számára.
- **TaskService** – támogatja a Feladatlista létrehozását, olvasását, frissítését és törlését. Az API-t a Azure AD B2C és a TaskWebApp által védettnek nevezzük.

A mintát úgy változtathatja meg, hogy a bérlőben regisztrált alkalmazást használja, beleértve az alkalmazás AZONOSÍTÓját és a korábban rögzített kulcsot is. A létrehozott felhasználói folyamatokat is konfigurálhatja. A minta a konfigurációs értékeket a *web. config* fájlban megadott beállításokként határozza meg.

Frissítse a web. config fájlban lévő beállításokat a felhasználói folyamattal való együttműködéshez:

1. Nyissa meg a **B2C-WebAPI-DotNet** megoldást a Visual Studióban.
1. A **TaskWebApp** projektben nyissa meg a **web. config** fájlt.
    1. Cserélje le a `ida:Tenant` és `ida:AadInstance` a értéket a létrehozott bérlő nevére.
    1. Cserélje le a értékét `ida:ClientId` a rögzített alkalmazás-azonosítóra.
    1. Cserélje le az `ida:ClientSecret` értékét a feljegyzett kulcsra. Ahhoz, hogy hozzáadja a web. config fájlhoz, XML-kódolással kell kódolnia az ügyfél titkos kulcsát.
    1. Cserélje le a értékét `ida:SignUpSignInPolicyId` a `b2c_1_signupsignin1`értékre.
    1. Cserélje le a értékét `ida:EditProfilePolicyId` a `b2c_1_profileediting1`értékre.
    1. Cserélje le a értékét `ida:ResetPasswordPolicyId` a `b2c_1_passwordreset1`értékre.

## <a name="run-the-sample"></a>Minta futtatása

1. Megoldáskezelő kattintson a jobb gombbal a **TaskWebApp** projektre, majd kattintson a **beállítás indítási projektként**lehetőségre.
2. Nyomja le az **F5**billentyűt. Az alapértelmezett böngésző elindítja a helyi webhely címét: `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Regisztrálás e-mail-címmel

1. Az alkalmazás felhasználójának regisztrálásához kattintson a **regisztráció/bejelentkezés** lehetőségre. A rendszer a **b2c_1_signupsignin1** felhasználói folyamatot használja.
2. Az Azure AD B2C megjelenít egy bejelentkezési oldalt egy regisztrációs hivatkozással. Mivel még nem rendelkezik fiókkal, válassza a **regisztráció most**lehetőséget. A regisztrációs munkafolyamat megjelenít egy lapot, amely a felhasználó azonosító adatait gyűjti be és ellenőrzi az e-mail-cím alapján. A regisztrációs munkafolyamat a felhasználó jelszavát és a felhasználói folyamatban definiált kért attribútumokat is gyűjti.
3. Érvényes e-mail-címet használjon, és ellenőrizze az ellenőrző kód használatával. Állítson be egy jelszót. Adja meg a kért attribútumokhoz tartozó értékeket.

    ![A bejelentkezési vagy bejelentkezési munkafolyamat részeként megjelenő regisztrációs oldal](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.PNG)

4. Kattintson a **Létrehozás** gombra egy helyi fiók létrehozására az Azure AD B2C-bérlőben.

Most a felhasználó e-mail-címükkel bejelentkezhet, és használhatja a webalkalmazást.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az alkalmazás frissítése Azure AD B2C
> * A minta beállítása az alkalmazás használatára
> * Regisztráció a felhasználói folyamat használatával

> [!div class="nextstepaction"]
> [Oktatóanyag: A ASP.NET webes API-k elleni védelem Azure Active Directory B2C használata](active-directory-b2c-tutorials-web-api.md)
