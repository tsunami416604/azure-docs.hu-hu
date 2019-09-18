---
title: Oktatóanyag – hitelesítés engedélyezése natív ügyfélalkalmazás – Azure Active Directory B2C | Microsoft Docs
description: Útmutató a .NET desktop-alkalmazások felhasználói bejelentkezésének biztosításához a Azure Active Directory B2C használatával.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 9a3c11c7303d467a1a993c49d983131639683239
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064877"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>Oktatóanyag: Hitelesítés engedélyezése natív ügyfélalkalmazás Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan használható a Azure Active Directory B2C (Azure AD B2C) a bejelentkezéshez, és hogyan regisztrálhat felhasználókat egy Windows megjelenítési alaprendszer (WPF) asztali alkalmazásban. Azure AD B2C lehetővé teszi az alkalmazások számára, hogy a nyílt szabványos protokollok használatával hitelesítsék a közösségi fiókokat, a vállalati fiókokat és a Azure Active Directory fiókokat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A natív ügyfélalkalmazás hozzáadása
> * A minta beállítása az alkalmazás használatára
> * Regisztráció a felhasználói folyamat használatával

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Felhasználói folyamatokat hozhat létre](tutorial-create-user-flows.md) , amelyekkel engedélyezheti az alkalmazás felhasználói élményét.
- Telepítse a [Visual Studio 2019](https://www.visualstudio.com/downloads/) -et a **.net desktop fejlesztői** és **ASP.net, valamint a webes fejlesztési** számítási feladatokkal.

## <a name="add-the-native-client-application"></a>A natív ügyfélalkalmazás hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza az **alkalmazások**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
5. Adja meg az alkalmazás nevét. Például: *nativeapp1*.
6. A **Web App/web API**belefoglalásához válassza a **nem**lehetőséget.
7. A **natív ügyfél**belefoglalása beállításnál válassza az **Igen**lehetőséget.
8. Az **átirányítási URI**esetében adjon meg egy érvényes átirányítási URI-t egy egyéni sémával. Az átirányítási URI kiválasztásakor két fontos szempontot kell figyelembe venni:

    - **Egyedi** – az átirányítási URI sémájának minden alkalmazás esetében egyedinek kell lennie. A példában `com.onmicrosoft.contoso.appname://redirect/path` `com.onmicrosoft.contoso.appname` a séma. Ezt a mintát kell követni. Ha két alkalmazás ugyanazt a sémát használja, a felhasználó választhat egy alkalmazást. Ha a felhasználó helytelenül választ, a bejelentkezés sikertelen lesz.
    - **Befejezés** – az átirányítási URI-nak rendelkeznie kell sémával és elérési úttal. Az elérési útnak legalább egy perjelet tartalmaznia kell a tartomány után. Például `//contoso/` működik és `//contoso` sikertelen. Győződjön meg arról, hogy az átirányítási URI nem tartalmaz speciális karaktereket, például aláhúzást.

9. Kattintson a **Create** (Létrehozás) gombra.
10. A Tulajdonságok lapon jegyezze fel a minta konfigurálásakor használni kívánt alkalmazás AZONOSÍTÓját.

## <a name="configure-the-sample"></a>A minta konfigurálása

Ebben az oktatóanyagban egy olyan mintát konfigurál, amelyet letöltheti a GitHubról. A minta WPF asztali alkalmazás a regisztrálást, a bejelentkezést és a védett webes API meghívását mutatja be Azure AD B2Cban. [Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [böngésszen az adattárban](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop), vagy klónozza a GitHubon található mintát.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Az Alkalmazásbeállítások módosításához cserélje le a `<your-tenant-name>` nevet a bérlő nevére, és cserélje le`<application-ID`a >t a rögzített alkalmazás-azonosítóra.

1. Nyissa meg az `active-directory-b2c-wpf` elemet a Visual Studióban.
2. Az `active-directory-b2c-wpf` projektben nyissa meg az **App.xaml.cs** fájlt, és hajtsa végre a következő frissítéseket:

    ```csharp
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "<application-ID>";
    ```

3. Frissítse a **PolicySignUpSignIn** változót a létrehozott felhasználói folyamat nevével.

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Minta futtatása

A minta létrehozásához és futtatásához nyomja le az **F5** billentyűt.

### <a name="sign-up-using-an-email-address"></a>Regisztrálás e-mail-címmel

1. Kattintson a **Bejelentkezés** gombra a felhasználóként való regisztrációhoz. Ez a **B2C_1_signupsignin1** felhasználói folyamatot használja.
2. Az Azure AD B2C megjelenít egy bejelentkezési oldalt egy regisztrációs hivatkozással. Mivel még nincs fiókja, kattintson a **Regisztráció** hivatkozásra.
3. A regisztrációs munkafolyamat megjelenít egy lapot, amely a felhasználó azonosító adatait gyűjti be és ellenőrzi az e-mail-cím alapján. A regisztrációs munkafolyamat a felhasználó jelszavát és a felhasználói folyamatban definiált kért attribútumokat is gyűjti.

    Érvényes e-mail-címet használjon, és ellenőrizze az ellenőrző kód használatával. Állítson be egy jelszót. Adja meg a kért attribútumokhoz tartozó értékeket.

    ![A bejelentkezési vagy bejelentkezési munkafolyamat részeként megjelenő regisztrációs oldal](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

4. Kattintson a **Létrehozás** gombra egy helyi fiók létrehozására az Azure AD B2C-bérlőben.

Mostantól a felhasználók bejelentkezhetnek az e-mail-címükkel, és használhatják az asztali alkalmazást.

> [!NOTE]
> Ha az **API meghívása** gombra kattint, jogosulatlan hozzáférést jelző hibaüzenetet kap. Ezt a hibaüzenetet azért kapja, mert a bemutató bérlő egyik erőforrásához próbál meg hozzáférni. Mivel a hozzáférési jogkivonat csak az Ön Azure AD-bérlőjéhez érvényes, az API-hívás nincs engedélyezve. Lépjen tovább a következő oktatóanyagra, amelyben egy védett webes API-t hozhat létre a bérlője számára.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A natív ügyfélalkalmazás hozzáadása
> * A minta beállítása az alkalmazás használatára
> * Regisztráció a felhasználói folyamat használatával

> [!div class="nextstepaction"]
> [Oktatóanyag: Hozzáférés biztosítása egy Node. js webes API-hoz egy asztali alkalmazásból Azure Active Directory B2C használatával](active-directory-b2c-tutorials-spa-webapi.md)
