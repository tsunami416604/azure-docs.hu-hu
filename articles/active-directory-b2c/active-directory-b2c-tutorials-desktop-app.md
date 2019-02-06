---
title: Oktatóanyag – a natív ügyfélalkalmazás történő hitelesítés engedélyezése – Azure Active Directory B2C |} A Microsoft Docs
description: Arra vonatkozó útmutató, hogyan használható az Azure Active Directory B2C egy asztali .NET-alkalmazás felhasználói bejelentkezésének biztosításához.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: a1842859723173412df2053a242ebe9ca4cf7f32
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754030"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>Oktatóanyag: Az Azure Active Directory B2C használatával natív ügyfélalkalmazás-hitelesítés engedélyezése

Az oktatóanyag azt mutatja be, hogyan használhatja az Azure Active Directory (Azure AD) B2C-t felhasználók bejelentkeztetéséhez és regisztrálásához egy asztali Windows megjelenítési alaprendszeri (WPF-) alkalmazásban. Az Azure AD B2C lehetővé teszi az alkalmazások hitelesítését közösségi hálózati, vállalati fiókok és az Azure Active Directory-fiókok nyílt szabványú protokollokkal.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A natív ügyfélalkalmazás hozzáadása
> * A minta az alkalmazás konfigurálása
> * Iratkozzon fel a felhasználói folyamat használata

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Felhasználói folyamatok létrehozása](tutorial-create-user-flows.md) ahhoz, hogy az alkalmazás felhasználói élményt. 
- A [Visual Studio 2017](https://www.visualstudio.com/downloads/) telepítése **.NET asztali fejlesztési**, valamint **ASP.NET és webfejlesztési** számítási feladatokkal.

## <a name="add-the-native-client-application"></a>A natív ügyfélalkalmazás hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy nevet az alkalmazásnak. Ha például *nativeapp1*.
6. A **közé tartozik a webalkalmazás vagy webes API**válassza **nem**.
7. A **natív ügyfél belefoglalása**válassza **Igen**.
8. A **átirányítási URI-t**, adjon meg egy érvényes átirányítási URI-t egy egyéni sémával. Két szempontot fontos egy átirányítási URI-JÁNAK kiválasztásakor:

    - **Egyedi** -átirányítási URI sémájának minden alkalmazás esetén egyedinek kell lennie. A példában `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` a séma. Ezt a mintát kell követnie. Ha két alkalmazás ugyanazt a sémát használja, a felhasználó a hibajelentési szolgáltatás egy alkalmazás kiválasztásához. Ha a felhasználó helytelenül választ, a bejelentkezés meghiúsul.
    - **Teljes** -átirányítási URI-JÁNAK rendelkeznie kell egy sémát és a egy elérési utat. Az elérési utat a tartomány után legalább egy perjelet tartalmaznia kell. Ha például `//contoso/` működik és `//contoso` sikertelen lesz. Győződjön meg arról, hogy az átirányítási URI-t nem tartalmazza a különleges karaktereket, például aláhúzásjeleket.

9. Kattintson a **Create** (Létrehozás) gombra.
10. A Tulajdonságok lapon jegyezze fel az Alkalmazásazonosítót, amikor konfigurálja a mintát használni.

## <a name="configure-the-sample"></a>A minta konfigurálásához

Ebben az oktatóanyagban a minta azt a githubról letölthető fog konfigurálni. A mintául szolgáló asztali WPF-alkalmazás bemutatja a regisztrációt, bejelentkezést, és a egy Azure AD B2C-vel védett webes API-hívások. [Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [böngésszen az adattárban](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop), vagy klónozza a GitHubon található mintát.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

A beállítások módosításához cserélje le a `<your-tenant-name>` a bérlő nevét, és cserélje le a`<application-ID`> az alkalmazás azonosítójával feljegyzett.

1. Nyissa meg az `active-directory-b2c-wpf` elemet a Visual Studióban.
2. Az `active-directory-b2c-wpf` projektben nyissa meg az **App.xaml.cs** fájlt, és hajtsa végre a következő frissítéseket:

    ```C#
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "<application-ID>";
    ```

3. Frissítés a **PolicySignUpSignIn** változó nevét a létrehozott felhasználói folyamatot.

    ```C#
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Minta futtatása

Nyomja meg **F5** hozhat létre és futtassa a mintát.

### <a name="sign-up-using-an-email-address"></a>Regisztrálás e-mail-címmel

1. Kattintson a **bejelentkezés** felhasználói regisztráció. Ez a **B2C_1_signupsignin1** felhasználói folyamatot.
2. Az Azure AD B2C megjelenít egy bejelentkezési oldalt egy regisztrációs hivatkozással. Mivel még nincs fiókja, kattintson a **Regisztráció** hivatkozásra. 
3. A regisztrációs munkafolyamat megjelenít egy lapot, amely a felhasználó azonosító adatait gyűjti be és ellenőrzi az e-mail-cím alapján. A munkafolyamat az a felhasználó jelszavát, és a felhasználói folyamat meghatározott attribútumokat is gyűjti.

    Érvényes e-mail-címet használjon, és ellenőrizze az ellenőrző kód használatával. Állítson be egy jelszót. Adja meg a kért attribútumokhoz tartozó értékeket. 

    ![Regisztrációs munkafolyamat](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Kattintson a **Létrehozás** gombra egy helyi fiók létrehozására az Azure AD B2C-bérlőben.

Mostantól a felhasználók bejelentkezhetnek az e-mail-címükkel, és használhatják az asztali alkalmazást.

> [!NOTE]
> Ha az **API meghívása** gombra kattint, jogosulatlan hozzáférést jelző hibaüzenetet kap. Ezt a hibaüzenetet azért kapja, mert a bemutató bérlő egyik erőforrásához próbál meg hozzáférni. Mivel a hozzáférési jogkivonat csak az Ön Azure AD-bérlőjéhez érvényes, az API-hívás nincs engedélyezve. Lépjen tovább a következő oktatóanyagra, amelyben egy védett webes API-t hozhat létre a bérlője számára.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A natív ügyfélalkalmazás hozzáadása
> * A minta az alkalmazás konfigurálása
> * Iratkozzon fel a felhasználói folyamat használata

> [!div class="nextstepaction"]
> [Oktatóanyag: Egy Node.js webes API-t egy asztali alkalmazásból az Azure Active Directory B2C használatával való hozzáférés engedélyezése](active-directory-b2c-tutorials-spa-webapi.md)
