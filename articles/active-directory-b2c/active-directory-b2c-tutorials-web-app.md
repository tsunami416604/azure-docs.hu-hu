---
title: Oktatóanyag – Webalkalmazások fiókokkal történő hitelesítésének engedélyezése az Azure Active Directory B2C használatával | Microsoft Docs
description: Arra vonatkozó útmutató, hogyan használhatja az Azure Active Directory B2C-t felhasználói bejelentkezés biztosításához egy ASP.NET-es webalkalmazás esetén.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 1/23/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: e215577fdb39b3dc1a9c5ce641c44e3cdef8fb45
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45604093"
---
# <a name="tutorial-enable-a-web-application-to-authenticate-with-accounts-using-azure-active-directory-b2c"></a>Oktatóanyag: Webalkalmazások fiókokkal történő hitelesítésének engedélyezése az Azure Active Directory B2C használatával

Az oktatóanyag azt mutatja be, hogyan használhatja az Azure Active Directory (Azure AD) B2C-t felhasználók bejelentkeztetéséhez és regisztrálásához egy ASP.NET-webalkalmazásban. Az Azure AD B2C nyílt szabványú protokollokkal teszi lehetővé az alkalmazások hitelesítését közösségi hálózati, vállalati és Azure Active Directory-fiókokon.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egy mintául szolgáló ASP.NET-webalkalmazás regisztrálása az Azure AD B2C-bérlőben.
> * A felhasználói regisztrációra, bejelentkezésre, profil szerkesztésére és új jelszó kérésére vonatkozó szabályzatok létrehozása.
> * A mintául szolgáló webalkalmazás konfigurálása az Azure AD B2C-bérlő használatára. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Saját [Azure AD B2C-bérlő](active-directory-b2c-get-started.md) létrehozása.
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) telepítése **ASP.NET és webfejlesztési** számítási feladattal.

## <a name="register-web-app"></a>Webalkalmazás regisztrációja

Az alkalmazásoknak [regisztrálva](../active-directory/develop/developer-glossary.md#application-registration) kell lenniük a bérlőben, hogy [hozzáférési jogkivonatokat](../active-directory/develop/developer-glossary.md#access-token) kaphassanak az Azure Active Directoryból. Az alkalmazásregisztráció létrehozza a bérlőben található alkalmazás [alkalmazásazonosítóját](../active-directory/develop/developer-glossary.md#application-id-client-id). 

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t. Ha sikerült, akkor most az előző oktatóanyagban létrehozott bérlőt használja. 

2. A B2C beállításaiban kattintson az **Alkalmazások**, majd a **Hozzáadás** lehetőségre. 

    A mintául szolgáló webalkalmazás bérlőben történő regisztrálásához használja a következő beállításokat:

    ![Új alkalmazás hozzáadása](media/active-directory-b2c-tutorials-web-app/web-app-registration.png)
    
    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name (Név)** | Mintául szolgáló saját webalkalmazás | Adjon meg egy olyan **nevet**, amely megfelelően körülírja az alkalmazást a felhasználók számára. | 
    | **Webalkalmazás vagy webes API szerepeltetése** | Igen | Webalkalmazásokhoz válassza az **Igen** lehetőséget. |
    | **Implicit folyamat engedélyezése** | Igen | Válassza az **Igen** lehetőséget, mivel az alkalmazás [OpenID Connect bejelentkezést](active-directory-b2c-reference-oidc.md) használ. |
    | **Válasz URL-cím** | `https://localhost:44316` | A válasz URL-címek olyan végpontok, amelyeken keresztül az Azure AD B2C visszaadja az alkalmazás által kért jogkivonatokat. Ebben az oktatóanyagban a minta helyileg fut (localhost), és a 44316-os porton figyel. |
    | **Natív ügyfél belefoglalása** | Nem | Mivel ez egy webalkalmazás, nem pedig egy natív ügyfél, válassza a Nem lehetőséget. |
    
3. Kattintson a **Létrehozás** gombra az alkalmazás regisztrálásához.

A regisztrált alkalmazások az Azure AD B2C-bérlő alkalmazásainak listájában jelennek meg. Válassza ki a webalkalmazását a listáról. Ekkor megjelenik a webalkalmazás tulajdonságpanelje.

![Webalkalmazás – Tulajdonságok](./media/active-directory-b2c-tutorials-web-app/b2c-web-app-properties.png)

Jegyezze fel az **alkalmazás azonosítóját**. Az azonosító egyedi módon azonosítja az alkalmazást, és az oktatóanyag későbbi részében, az alkalmazás konfigurálásakor lesz rá szükség.

### <a name="create-a-client-password"></a>Ügyféljelszó létrehozása

Az Azure AD B2C az OAuth2 engedélyezést használja az [ügyfélalkalmazásokhoz](../active-directory/develop/developer-glossary.md#client-application). A webalkalmazások [bizalmas ügyfelek](../active-directory/develop/developer-glossary.md#web-client), és használatukhoz ügyfél- vagy alkalmazásazonosítóra, illetve titkos ügyfélkódra, ügyféljelszóra vagy alkalmazáskulcsra van szükség.

1. Válassza ki a regisztrált webalkalmazás Kulcsok lapját, és kattintson a **Kulcs létrehozása** elemre.

2. Az alkalmazáskulcs megjelenítéséhez kattintson a **Mentés** gombra.

    ![alkalmazás általános kulcsok oldala](media/active-directory-b2c-tutorials-web-app/app-general-keys-page.png)

A portál egyszer megjeleníti a kulcsot. Fontos, hogy kimásolja és elmentse a kulcs értékét. Szüksége lesz rá az alkalmazás konfigurálásakor. A kulcsot tárolja biztonságos helyen, és ne ossza meg nyilvánosan.

## <a name="create-policies"></a>Szabályzatok létrehozása

Az Azure AD B2C-szabályzatok felhasználói munkafolyamatokat határoznak meg. A gyakori munkafolyamatok közé tartozik például a bejelentkezés, a feliratkozás, a jelszó módosítása és a profilok szerkesztése.

### <a name="create-a-sign-up-or-sign-in-policy"></a>Regisztrálási vagy bejelentkezési szabályzat létrehozása

A webalkalmazás eléréséhez szükséges felhasználói regisztrációhoz és bejelentkezéshez hozzon létre egy **regisztrálási vagy bejelentkezési szabályzatot**.

1. Válassza ki az Azure AD B2C portáloldalán a **Regisztrálási vagy bejelentkezési szabályzatok** lehetőséget, és kattintson a **Hozzáadás** elemre.

    A szabályzat konfigurálásához használja a következő beállításokat:

    ![Regisztrálási vagy bejelentkezési szabályzat hozzáadása](media/active-directory-b2c-tutorials-web-app/add-susi-policy.png)

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name (Név)** | SiUpIn | Adja meg a szabályzat **nevét**. A szabályzat neve a következő előtaggal kezdődik: **b2c_1_**. A példakódban a szabályzat teljes **b2c_1_SiUpIn** nevét fogja használni. | 
    | **Identitásszolgáltató** | Regisztráció e-mail-címmel | Az identitásszolgáltató alapján lehet kétséget kizáróan azonosítani a felhasználót. |
    | **Regisztrációs attribútumok** | Megjelenítendő név és postai irányítószám | Válassza ki a regisztráció során a felhasználótól összegyűjtendő attribútumokat. |
    | **Alkalmazásjogcímek** | Megjelenítendő név, postai irányítószám, új felhasználó, a felhasználó objektumazonosítója | Válassza ki a [hozzáférési jogkivonatban](../active-directory/develop/developer-glossary.md#access-token) szerepeltetni kívánt [jogcímeket](../active-directory/develop/developer-glossary.md#claim). |

2. Kattintson a **Létrehozás** gombra a szabályzat létrehozásához. 

### <a name="create-a-profile-editing-policy"></a>Profilszerkesztési szabályzat létrehozása

Ahhoz, hogy a felhasználók saját maguk is alaphelyzetbe állíthassák felhasználói profiljuk adatait, hozzon létre egy **profilszerkesztési szabályzatot**.

1. Válassza ki az Azure AD B2C portáloldalán a **Profilszerkesztési szabályzatok** elemet, és kattintson a **Hozzáadás** gombra.

    A szabályzat konfigurálásához használja a következő beállításokat:

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name (Név)** | SiPe | Adja meg a szabályzat **nevét**. A szabályzat neve a következő előtaggal kezdődik: **b2c_1_**. A példakódban a szabályzat teljes **b2c_1_SiPe** nevét fogja használni. | 
    | **Identitásszolgáltató** | Bejelentkezés helyi fiókba | Az identitásszolgáltató alapján lehet kétséget kizáróan azonosítani a felhasználót. |
    | **Profilattribútumok** | Megjelenítendő név és postai irányítószám | Válassza ki azokat az attribútumokat, amelyeket a felhasználók módosíthatnak a profilszerkesztés során. |
    | **Alkalmazásjogcímek** | Megjelenítendő név, postai irányítószám, a felhasználó objektumazonosítója | Válassza ki azokat a [jogcímeket](../active-directory/develop/developer-glossary.md#claim), amelyeket egy sikeres profilszerkesztés után szerepeltetni szeretne a [hozzáférési jogkivonatban](../active-directory/develop/developer-glossary.md#access-token). |

2. Kattintson a **Létrehozás** gombra a szabályzat létrehozásához. 

### <a name="create-a-password-reset-policy"></a>Új jelszó kérésére vonatkozó szabályzat létrehozása

Ha engedélyezni szeretné az új jelszavak kérését az alkalmazásban, létre kell hoznia egy **új jelszó kérésére** vonatkozó szabályzatot. Ez a szabályzat írja le, hogy mit tapasztalnak a felhasználók az új jelszó kérése során, valamint megadja azokat a jogkivonatokat, amelyeket az alkalmazás fogad a művelet sikeres elvégzésekor.

1. Az Azure AD B2C portáloldalán válassza ki az **Új jelszó kérésére vonatkozó szabályzatok** lehetőséget, és kattintson a **Hozzáadás** gombra.

    A szabályzat konfigurálásához használja a következő beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name (Név)** | SSPR | Adja meg a szabályzat **nevét**. A szabályzat neve a következő előtaggal kezdődik: **b2c_1_**. A példakódban a szabályzat teljes **b2c_1_SSPR** nevét fogja használni. | 
    | **Identitásszolgáltató** | Új jelszó kérése e-mail-cím használatával | Az identitásszolgáltató alapján lehet kétséget kizáróan azonosítani a felhasználót. |
    | **Alkalmazásjogcímek** | A felhasználó objektumazonosítója | Válassza ki azokat a [jogcímeket](../active-directory/develop/developer-glossary.md#claim), amelyeket egy új jelszó sikeres kérése után szerepeltetni kíván a [hozzáférési jogkivonatban](../active-directory/develop/developer-glossary.md#access-token). |

2. Kattintson a **Létrehozás** gombra a szabályzat létrehozásához. 

## <a name="update-web-app-code"></a>Webalkalmazás kódjának frissítése

Most, hogy regisztrálta a webalkalmazást és létrehozta a szabályzatokat, konfigurálnia kell az alkalmazást az Azure AD B2C-bérlő használatához. Ebben az oktatóanyagban a GitHubról letölthető, mintául szolgáló webalkalmazást fog konfigurálni. 

[Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip), vagy a klónozza a mintául szolgáló webalkalmazást a GitHubról. A mintafájlt egy olyan mappába kell kibontani, ahol a teljes elérési út rövidebb, mint 260 karakter.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

A mintául szolgáló ASP.NET-webalkalmazás egy egyszerű feladatlista-alkalmazás, amellyel feladatlistát lehet létrehozni és frissíteni. Az alkalmazás által használt [Microsoft OWIN köztes összetevők](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/) lehetővé teszik, hogy a felhasználók regisztráljanak az alkalmazás Azure AD B2C-bérlőben történő használatához. Egy Azure AD B2C-szabályzat létrehozása lehetővé teszi a felhasználók számára, hogy egy meglévő közösségi hálózati fiókot vagy egy újonnan létrehozott fiókot használjanak azonosítóként az alkalmazás eléréséhez. 

Két projekt szerepel a mintául szolgáló megoldásban:

**Mintául szolgáló webalkalmazás (TaskWebApp):** webalkalmazás feladatlista létrehozáshoz és szerkesztéséhez. A webalkalmazás a **regisztrálási vagy bejelentkezési** szabályzatot használja a felhasználók regisztrálásához és bejelentkeztetéséhez.

**Mintául szolgáló webes API-alkalmazás (TaskService):** webes API, amely támogatja a feladatlista létrehozását, olvasását, frissítését és törlését. A webes API-nak az Azure AD B2C biztosít védelmet, és a webalkalmazással hívható meg.

Módosítania kell az alkalmazást ahhoz, hogy használni tudja az alkalmazás regisztrációját a saját bérlőjében, amely tartalmazza a korábban feljegyzett alkalmazásazonosítót és kulcsot. A létrehozott szabályzatokat konfigurálni is kell. A mintául szolgáló webalkalmazás a konfigurációs értékeket alkalmazásbeállításokként határozza meg a Web.config fájlban. Az alkalmazás beállításainak módosításához:

1. Nyissa meg a **B2C-WebAPI-DotNet** megoldást a Visual Studióban.

2. A **TaskWebApp** webalkalmazás-projektben nyissa meg a **Web.config** fájlt. Cserélje le az `ida:Tenant` értékét a létrehozott bérlő nevére. Cserélje le az `ida:ClientId` értékét a feljegyzett alkalmazásazonosítóra. Cserélje le az `ida:ClientSecret` értékét a feljegyzett kulcsra.

3. A **Web.config** fájlban cserélje le az `ida:SignUpSignInPolicyId` értéket a `b2c_1_SiUpIn` értékre. Cserélje le az `ida:EditProfilePolicyId` értéket a `b2c_1_SiPe` értékre. Cserélje le az `ida:ResetPasswordPolicyId` értéket a `b2c_1_SSPR` értékre.

## <a name="run-the-sample-web-app"></a>A mintául szolgáló webalkalmazás futtatása

A Megoldáskezelőben kattintson a jobb gombbal a **TaskWebApp** projektre, majd kattintson a **Beállítás kezdőprojektként** elemre.

Nyomja le az **F5** gombot a webalkalmazás elindításához. Az alapértelmezett böngésző elindítja a helyi webhely címét: `https://localhost:44316/`. 

A mintaalkalmazás támogatja a regisztrációt, a bejelentkezést, a profil szerkesztését és az új jelszó kérését. Ez az oktatóanyag kiemeli, hogyan regisztrál egy felhasználó az alkalmazás használatára e-mail-címmel. Ha szeretne, más forgatókönyvekkel kísérletezhet önállóan.

### <a name="sign-up-using-an-email-address"></a>Regisztrálás e-mail-címmel

1. Kattintson a felső sávban található **Regisztráció/Bejelentkezés** hivatkozásra, hogy regisztrálja magát a webalkalmazás felhasználójaként. A művelet azt a **b2c_1_SiUpIn** szabályzatot használja, amelyet az egyik előző lépésben határozott meg.

2. Az Azure AD B2C megjelenít egy bejelentkezési oldalt egy regisztrációs hivatkozással. Mivel még nincs fiókja, kattintson a **Regisztráció** hivatkozásra. 

3. A regisztrációs munkafolyamat megjelenít egy lapot, amely a felhasználó azonosító adatait gyűjti be és ellenőrzi az e-mail-cím alapján. A munkafolyamat a felhasználói jelszavakat és a szabályzatban meghatározott attribútumokat is összegyűjti.

    Érvényes e-mail-címet használjon, és ellenőrizze az ellenőrző kód használatával. Állítson be egy jelszót. Adja meg a kért attribútumokhoz tartozó értékeket. 

    ![Regisztrációs munkafolyamat](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. Kattintson a **Létrehozás** gombra egy helyi fiók létrehozására az Azure AD B2C-bérlőben.

Mostantól a felhasználók bejelentkezhetnek az e-mail-címükkel, és használhatják a webalkalmazást.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure AD B2C-bérlőt ahhoz is használhatja, ha más Azure AD B2C-oktatóanyagokat is ki szeretne próbálni. Ha már nincs szüksége rá, akkor [törölheti az Azure AD B2C-bérlőt](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte az Azure AD B2C-bérlő és a szabályzatok létrehozásának folyamatát, valamint azt, hogyan frissítheti a mintául szolgáló webalkalmazást az Azure AD B2C-bérlő használatához. Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan kell regisztrálni, konfigurálni és meghívni az Azure AD B2C-bérlővel védett ASP.NET webes API-t.

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Active Directory B2C használata az ASP.NET webes API védelméhez](active-directory-b2c-tutorials-web-api.md)
