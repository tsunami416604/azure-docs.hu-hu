---
title: 'Oktatóanyag: Felhasználók hitelesítése natív ügyfélalkalmazásban'
titleSuffix: Azure AD B2C
description: Oktatóanyag arról, hogyan használható az Azure Active Directory B2C a .NET asztali alkalmazások felhasználói bejelentkezésének biztosításához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 06d27c3a3daa4702653a2063d0ac70fd094e2d74
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78186199"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>Oktatóanyag: Felhasználók hitelesítése natív asztali ügyfélprogramban az Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Active Directory B2C (Azure AD B2C) segítségével a windowsos megjelenítési alaprendszer (WPF) asztali alkalmazásában való bejelentkezést és a felhasználók regisztrálását. Az Azure AD B2C lehetővé teszi, hogy az alkalmazások hitelesítése a közösségi fiókok, vállalati fiókok és az Azure Active Directory-fiókok nyílt szabványos protokollok használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A natív ügyfélalkalmazás hozzáadása
> * A minta konfigurálása az alkalmazás használatára
> * Regisztráció a felhasználói folyamat használatával

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre felhasználói folyamatokat,](tutorial-create-user-flows.md) hogy lehetővé tegye a felhasználói élményt az alkalmazásban.
- Telepítse [a Visual Studio 2019-et](https://www.visualstudio.com/downloads/) a **.NET asztali fejlesztéssel,** valamint ASP.NET és **webfejlesztési** munkaterheléssel.

## <a name="add-the-native-client-application"></a>A natív ügyfélalkalmazás hozzáadása

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Rögzítse az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.

## <a name="configure-the-sample"></a>A minta konfigurálása

Ebben az oktatóanyagban konfigurálhat egy mintát, amely letölthető a GitHubról. A minta WPF asztali alkalmazás bemutatja a regisztrációt, a bejelentkezést, és meghívhat egy védett webes API-t az Azure AD B2C-ben. [Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip), [böngésszen az adattárban](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop), vagy klónozza a GitHubon található mintát.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Az alkalmazás frissítése az Azure AD B2C-bérlővel való együttműködésre, és a felhasználói folyamatok meghívása az alapértelmezett bemutató bérlő helyett:

1. Nyissa meg az **active-directory-b2c-wpf** megoldást (`active-directory-b2c-wpf.sln`) a Visual Studióban.
2. Az **active-directory-b2c-wpf** projektben nyissa meg a *App.xaml.cs* fájlt, és keresse meg a következő változódefiníciókat. Cserélje `{your-tenant-name}` le az Azure AD B2C bérlőnevét és `{application-ID}` a korábban rögzített alkalmazásazonosítót.

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. Frissítse a házirend névváltozóit az előfeltételek részeként létrehozott felhasználói folyamatok nevével. Példa:

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>Minta futtatása

A minta létrehozásához és futtatásához nyomja le az **F5 billentyűt.**

### <a name="sign-up-using-an-email-address"></a>Regisztrálás e-mail-címmel

1. Válassza **a Bejelentkezés** lehetőséget a regisztrációhoz felhasználóként. Ez a **felhasználói folyamat B2C_1_signupsignin1** használja.
2. Az Azure AD B2C egy bejelentkezési lapot jelenít meg egy **Regisztráció most** hivatkozással. Mivel még nincs fiókja, válassza a **Regisztráció most** hivatkozást.
3. A regisztrációs munkafolyamat megjelenít egy lapot, amely a felhasználó azonosító adatait gyűjti be és ellenőrzi az e-mail-cím alapján. A regisztrációs munkafolyamat a felhasználó jelszavát és a felhasználói folyamatban definiált kért attribútumokat is gyűjti.

    Érvényes e-mail-címet használjon, és ellenőrizze az ellenőrző kód használatával. Állítson be egy jelszót. Adja meg a kért attribútumokhoz tartozó értékeket.

    ![A bejelentkezési/regisztrációs munkafolyamat részeként megjelenő regisztrációs oldal](./media/tutorial-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. Válassza **a Create lehetőséget,** ha helyi fiókot szeretne létrehozni az Azure AD B2C bérlőben.

A felhasználó most már használhatja az e-mail címét, hogy jelentkezzen be, és használja az asztali alkalmazás. Sikeres regisztráció vagy bejelentkezés után a token részletei megjelennek a WPF alkalmazás alsó ablaktáblájában.

![A WPF asztali alkalmazás alsó ablaktáblájában látható tokenrészletei](./media/tutorial-desktop-app/desktop-app-01-post-signin.png)

Ha a **Hívás API** gombot választja, **hibaüzenet** jelenik meg. A hibát azért tapasztalja, mert az alkalmazás jelenlegi állapotában megpróbál hozzáférni a `fabrikamb2c.onmicrosoft.com`demo-bérlő által védett API-hoz. Mivel a hozzáférési jogkivonat csak az Azure AD B2C-bérlőre érvényes, ezért az API-hívás nem engedélyezett.

Folytassa a következő oktatóanyaggal egy védett webes API regisztrálásához a saját bérlőjében, és engedélyezze a **Hívás API-funkciót.**

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A natív ügyfélalkalmazás hozzáadása
> * A minta konfigurálása az alkalmazás használatára
> * Regisztráció a felhasználói folyamat használatával

Ezután a **Hívás API** gomb funkció engedélyezéséhez adjon hozzáférést a WPF asztali alkalmazásnak egy, a saját Azure AD B2C-bérlőben regisztrált webes API-hoz:

> [!div class="nextstepaction"]
> [Oktatóanyag: Hozzáférés a Node.js webes API-hoz egy asztali alkalmazásból >](tutorial-desktop-app-webapi.md)
