---
title: 'Oktatóanyag: Hitelesítés engedélyezése webalkalmazásban'
titleSuffix: Azure AD B2C
description: Arra vonatkozó útmutató, hogyan használhatja az Azure Active Directory B2C-t felhasználói bejelentkezés biztosításához egy ASP.NET-es webalkalmazás esetén.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e4b56f18bf8a2ed1c22b00b8a57efdbf06eb7fa2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183319"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Oktatóanyag: Hitelesítés engedélyezése webalkalmazásban az Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Active Directory B2C (Azure AD B2C) segítségével a bejelentkezést, és regisztrálhatja a felhasználókat egy ASP.NET webalkalmazásban. Az Azure AD B2C lehetővé teszi, hogy az alkalmazások hitelesítése a közösségi fiókok, vállalati fiókok és az Azure Active Directory-fiókok nyílt szabványos protokollok használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az alkalmazás frissítése az Azure AD B2C-ben
> * A minta konfigurálása az alkalmazás használatára
> * Regisztráció a felhasználói folyamat használatával

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Hozzon létre felhasználói folyamatokat,](tutorial-create-user-flows.md) hogy lehetővé tegye a felhasználói élményt az alkalmazásban.
* Telepítse a [Visual Studio 2019-et](https://www.visualstudio.com/downloads/) a **ASP.NET és a webfejlesztési** munkaterheléssel.

## <a name="update-the-application-registration"></a>Az alkalmazás regisztrációjának frissítése

Az oktatóanyagban, amely az előfeltételek részeként befejeződött, regisztrált egy webalkalmazást az Azure AD B2C-ben. Ahhoz, hogy engedélyezze a kommunikációt a minta ebben az oktatóanyagban, hozzá kell adnia egy átirányítási URI-t, és létre kell hoznia egy ügyféltitkos kulcsot (kulcsot) a regisztrált alkalmazáshoz.

### <a name="add-a-redirect-uri-reply-url"></a>Átirányítási URI hozzáadása (válasz URL-címe)

Használhatja az aktuális **alkalmazások** tapasztalat, vagy az új, egységes **alkalmazás regisztrációk (Előzetes verzió)** az alkalmazás frissítéséhez. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja a bérlőt tartalmazó könyvtárat.
1. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
1. Válassza **az Alkalmazások**lehetőséget, majd válassza ki a *webapp1* alkalmazást.
1. A **Válasz URL-cím csoportban**adja hozzá a t. `https://localhost:44316`
1. Kattintson a **Mentés** gombra.
1. A tulajdonságok lapon rögzítse az alkalmazásazonosítót, amelyet a webalkalmazás konfigurálásakor egy későbbi lépésben használhat.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Válassza **az Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, válassza a **Saját alkalmazások** lapot, majd a *webapp1* alkalmazást.
1. Válassza **a Hitelesítés**lehetőséget, majd válassza az Új felület **kipróbálása** lehetőséget (ha látható).
1. A **Web**csoportban jelölje be `https://localhost:44316`az URI **hozzáadása** hivatkozást, írja be a beadási parancsot, majd kattintson a Mentés **gombra.**
1. Válassza az **Áttekintés** lehetőséget.
1. Rögzítse az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben a webalkalmazás konfigurálásakor.

* * *

### <a name="create-a-client-secret"></a>Ügyféltitok létrehozása

Ezután hozzon létre egy ügyféltitkos kulcsot a regisztrált webalkalmazáshoz. A webalkalmazás-kód minta használja ezt az identitásának igazolására, amikor jogkivonatokat kér.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-the-sample"></a>A minta konfigurálása

Ebben az oktatóanyagban konfigurálhat egy mintát, amely letölthető a GitHubról. A minta ASP.NET használ egy egyszerű teendőlista megadásához. A minta [Microsoft OWIN köztes szoftver összetevőket](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/)használ. [Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip), vagy a klónozza a GitHubon található mintát. A mintafájlt egy olyan mappába kell kibontani, ahol a teljes elérési út rövidebb, mint 260 karakter.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

A mintamegoldásban a következő két projekt szerepel:

* **TaskWebApp** - Feladatlista létrehozása és szerkesztése. A minta a **regisztrációs vagy bejelentkezési** felhasználói folyamat segítségével regisztráljon és jelentkezzen be a felhasználók.
* **TaskService** – Támogatja a feladatlista létrehozásához, olvasásához, frissítéséhez és törléséhez. Az API-t az Azure AD B2C védi, és a TaskWebApp szólítja meg.

A minta módosítása a bérlőben regisztrált alkalmazás használatához, amely tartalmazza az alkalmazás azonosítóját és a korábban rögzített kulcsot. A létrehozott felhasználói folyamatokat is konfigurálhatja. A minta a *web.config* fájl ban beállításokként határozza meg a konfigurációs értékeket.

Frissítse a Web.config fájl beállításait a felhasználói folyamattal való együttműködésre:

1. Nyissa meg a **B2C-WebAPI-DotNet** megoldást a Visual Studióban.
1. A **TaskWebApp** projektben nyissa meg a **Web.config** fájlt.
    1. Frissítse az `ida:Tenant` Azure `ida:AadInstance` AD B2C-bérlő értékét, és a létrehozott Azure AD B2C-bérlő nevével. Cserélje ki `fabrikamb2c` például a helyére. `contoso`
    1. Cserélje le `ida:ClientId` a rögzített alkalmazásazonosító értékét.
    1. Cserélje le az `ida:ClientSecret` értékét a feljegyzett kulcsra. Ha az ügyféltitok olyan előre definiált XML-entitásokat`<`tartalmaz,`>`amelyek például`&`kisebbek, mint`"`( ), nagyobbak, mint ( ), ampersand ( ) vagy dupla idézőjel ( ), akkor az ügyféltitok XML-kódolásával kell elkerülnie ezeket a karaktereket, mielőtt hozzáadná a Web.config fájlhoz.
    1. Cserélje le `ida:SignUpSignInPolicyId` az `b2c_1_signupsignin1`értékét a helyére.
    1. Cserélje le `ida:EditProfilePolicyId` az `b2c_1_profileediting1`értékét a helyére.
    1. Cserélje le `ida:ResetPasswordPolicyId` az `b2c_1_passwordreset1`értékét a helyére.

## <a name="run-the-sample"></a>Minta futtatása

1. A Megoldáskezelőben kattintson a jobb gombbal a **TaskWebApp-projektre,** majd kattintson a **Beállítás indítási projektként parancsra.**
1. Nyomja **le az F5 billentyűt.** Az alapértelmezett böngésző elindítja a helyi webhely címét: `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Regisztrálás e-mail-címmel

1. Válassza **a Regisztráció / Bejelentkezés** lehetőséget az alkalmazás felhasználójaként való regisztrációhoz. A **rendszer a b2c_1_signupsignin1** felhasználói folyamatot használja.
1. Az Azure AD B2C megjelenít egy bejelentkezési oldalt egy regisztrációs hivatkozással. Mivel még nem rendelkezik fiókkal, válassza **a Regisztráció most**lehetőséget. A regisztrációs munkafolyamat megjelenít egy lapot, amely a felhasználó azonosító adatait gyűjti be és ellenőrzi az e-mail-cím alapján. A regisztrációs munkafolyamat a felhasználó jelszavát és a felhasználói folyamatban definiált kért attribútumokat is gyűjti.
1. Érvényes e-mail-címet használjon, és ellenőrizze az ellenőrző kód használatával. Állítson be egy jelszót. Adja meg a kért attribútumokhoz tartozó értékeket.

    ![A bejelentkezési/regisztrációs munkafolyamat részeként megjelenő regisztrációs oldal](./media/tutorial-web-app-dotnet/sign-up-workflow.PNG)

1. Válassza **a Create lehetőséget,** ha helyi fiókot szeretne létrehozni az Azure AD B2C bérlőben.

Az alkalmazás felhasználó most már használhatja az e-mail címét, hogy jelentkezzen be, és használja a webalkalmazást.

A **teendőlista** funkció azonban nem fog működni, amíg el nem fejezi be a sorozat következő [oktatóanyagát, az Oktatóanyag: Az Azure AD B2C használata a ASP.NET webes API védelméhez.](tutorial-web-api-dotnet.md)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az alkalmazás frissítése az Azure AD B2C-ben
> * A minta konfigurálása az alkalmazás használatára
> * Regisztráció a felhasználói folyamat használatával

Most lépjen tovább a következő oktatóanyagra, hogy engedélyezze a webalkalmazás **Teendőlista** funkcióját. Ebben regisztrál egy web API-alkalmazást a saját Azure AD B2C-bérlőben, majd módosítsa a kódmintát a bérlő API-hitelesítéshez való használatához.

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Active Directory B2C használatával megvédheti a ASP.NET webes API->](tutorial-web-api-dotnet.md)
