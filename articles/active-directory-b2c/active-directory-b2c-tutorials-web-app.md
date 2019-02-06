---
title: Oktatóanyag – egy webes alkalmazásban történő hitelesítés engedélyezése – Azure Active Directory B2C |} A Microsoft Docs
description: Arra vonatkozó útmutató, hogyan használhatja az Azure Active Directory B2C-t felhasználói bejelentkezés biztosításához egy ASP.NET-es webalkalmazás esetén.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: ced74cc5af829c3677a12aaf4bffdf9a518f6053
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755628"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Oktatóanyag: Egy webalkalmazás, az Azure Active Directory B2C-hitelesítés engedélyezése

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Active Directory (Azure AD) B2C bejelentkeztetéséhez és regisztrálásához egy ASP.NET-alkalmazás a felhasználók. Az Azure AD B2C lehetővé teszi az alkalmazások hitelesítését közösségi hálózati, vállalati fiókok és az Azure Active Directory-fiókok nyílt szabványú protokollokkal.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Frissítse az alkalmazást az Azure AD B2C-vel
> * A minta az alkalmazás konfigurálása
> * Iratkozzon fel a felhasználói folyamat használata

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Felhasználói folyamatok létrehozása](tutorial-create-user-flows.md) ahhoz, hogy az alkalmazás felhasználói élményt. 
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) telepítése **ASP.NET és webfejlesztési** számítási feladattal.

## <a name="update-the-application"></a>Az alkalmazás frissítése

Az oktatóanyagban az Előfeltételek részeként végrehajtott egy webalkalmazás, Azure AD B2C-ben hozzáadott. Ebben az oktatóanyagban a minta való kommunikáció engedélyezése, hogy hozzá kell átirányítási URI-t az alkalmazáshoz az Azure AD B2C-ben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Válassza ki **alkalmazások**, majd válassza ki a *webapp1* alkalmazás.
5. A **válasz URL-cím**, adjon hozzá `https://localhost:44316`.
6. Kattintson a **Mentés** gombra.
7. A Tulajdonságok lapon jegyezze fel az Alkalmazásazonosítót, amelyek a webalkalmazás konfigurálásakor fogja használni.
8. Válassza ki **kulcsok**válassza **kulcs létrehozása**, és válassza ki **mentése**. Jegyezze fel a kulcs, amelyet a webalkalmazás konfigurálásakor fogja használni.

## <a name="configure-the-sample"></a>A minta konfigurálásához

Ebben az oktatóanyagban a minta azt a githubról letölthető fog konfigurálni. A minta egy egyszerű feladatlista használ az ASP.NET. A példa [Microsoft OWIN köztes összetevők](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). [Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip), vagy a klónozza a GitHubon található mintát. A mintafájlt egy olyan mappába kell kibontani, ahol a teljes elérési út rövidebb, mint 260 karakter.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

A következő két projekt vannak a mintául szolgáló megoldásban:

- **TaskWebApp** – létrehozása és a egy feladatlista szerkesztése. A példa a **regisztrálási vagy bejelentkezési** való regisztráció vagy bejelentkezés a felhasználói felhasználói folyamatot.
- **TaskService** – támogatja a létrehozás, Olvasás, frissítés, és a feladatlista törlés. Az API-t az Azure AD B2C által védett és TaskWebApp hívja meg.

Módosítja a mintát használni az alkalmazást, amely regisztrálva van a saját bérlőjében, amely magában foglalja az Alkalmazásazonosítót és a kulcs, amelyet korábban felvett. Is konfigurálhatja a felhasználói folyamatok létrehozott. A minta a konfigurációs értékeket határozza meg a beállításokat a Web.config fájlban. A beállítások módosítása:

1. Nyissa meg a **B2C-WebAPI-DotNet** megoldást a Visual Studióban.
2. Az a **TaskWebApp** projektben nyissa meg a **Web.config** fájlt. Cserélje le az `ida:Tenant` értékét a létrehozott bérlő nevére. Cserélje le az `ida:ClientId` értékét a feljegyzett alkalmazásazonosítóra. Cserélje le az `ida:ClientSecret` értékét a feljegyzett kulcsra.
3. A **Web.config** fájlban cserélje le az `ida:SignUpSignInPolicyId` értéket a `b2c_1_signupsignin1` értékre. Cserélje le az `ida:EditProfilePolicyId` értéket a `b2c_1_profileediting1` értékre. Cserélje le az `ida:ResetPasswordPolicyId` értéket a `b2c_1_passwordreset1` értékre.

## <a name="run-the-sample"></a>Minta futtatása

1. A Megoldáskezelőben kattintson a jobb gombbal a **TaskWebApp** projektre, és kattintson a **Set as StartUp Project**.
2. Nyomja meg **F5**. Az alapértelmezett böngésző elindítja a helyi webhely címét: `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Regisztrálás e-mail-címmel

1. Kattintson a **regisztráció / bejelentkezés** az alkalmazás felhasználói regisztráció. A **b2c_1_signupsignin1** felhasználói folyamat használja.
2. Az Azure AD B2C megjelenít egy bejelentkezési oldalt egy regisztrációs hivatkozással. Mivel még nincs fiókja, válassza **regisztráció**. A regisztrációs munkafolyamat megjelenít egy lapot, amely a felhasználó azonosító adatait gyűjti be és ellenőrzi az e-mail-cím alapján. A munkafolyamat az a felhasználó jelszavát, és a felhasználói folyamat meghatározott attribútumokat is gyűjti.
3. Érvényes e-mail-címet használjon, és ellenőrizze az ellenőrző kód használatával. Állítson be egy jelszót. Adja meg a kért attribútumokhoz tartozó értékeket. 

    ![Regisztrációs munkafolyamat](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. Kattintson a **Létrehozás** gombra egy helyi fiók létrehozására az Azure AD B2C-bérlőben.

A felhasználó most már használhatja e-mail-címükkel jelentkezzen be és használják a webalkalmazást.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Frissítse az alkalmazást az Azure AD B2C-vel
> * A minta az alkalmazás konfigurálása
> * Iratkozzon fel a felhasználói folyamat használata

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Active Directory B2C használata az ASP.NET webes API védelméhez](active-directory-b2c-tutorials-web-api.md)
