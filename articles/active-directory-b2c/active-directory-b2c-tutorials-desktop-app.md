---
title: Oktatóanyag – Asztali alkalmazáson belüli, fiókokkal történő hitelesítés engedélyezése az Azure Active Directory B2C használatával | Microsoft Docs
description: Arra vonatkozó útmutató, hogyan használhatja az Azure Active Directory B2C-t egy asztali .NET-alkalmazás felhasználói bejelentkezésének biztosításához.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 11/30/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: a135dd0b350a6129d94f1c6b0b185c3fb272668f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834493"
---
# <a name="tutorial-enable-desktop-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>Oktatóanyag: Asztali alkalmazáson belüli, fiókokkal történő hitelesítés engedélyezése az Azure Active Directory B2C használatával

Az oktatóanyag azt mutatja be, hogyan használhatja az Azure Active Directory (Azure AD) B2C-t felhasználók bejelentkeztetéséhez és regisztrálásához egy asztali Windows megjelenítési alaprendszeri (WPF-) alkalmazásban. Az Azure AD B2C nyílt szabványú protokollokkal teszi lehetővé az alkalmazások hitelesítését közösségi hálózati, vállalati és Azure Active Directory-fiókokon.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Mintául szolgáló asztali alkalmazás regisztrálása az Azure AD B2C-bérlőben.
> * Hozzon létre felhasználói folyamatokat a felhasználói regisztrációs, bejelentkezési, profil szerkesztésére és új jelszó kérésére.
> * A mintaalkalmazás konfigurálása az Azure AD B2C-bérlő használatára.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Saját [Azure AD B2C-bérlő](active-directory-b2c-get-started.md) létrehozása.
* A [Visual Studio 2017](https://www.visualstudio.com/downloads/) telepítése **.NET asztali fejlesztési**, valamint **ASP.NET és webfejlesztési** számítási feladatokkal.

## <a name="register-desktop-app"></a>Az asztali alkalmazás regisztrálása

Az alkalmazásoknak [regisztrálva](../active-directory/develop/developer-glossary.md#application-registration) kell lenniük a bérlőben, hogy [hozzáférési jogkivonatokat](../active-directory/develop/developer-glossary.md#access-token) kaphassanak az Azure Active Directoryból. Az alkalmazásregisztráció létrehozza a bérlőben található alkalmazás [alkalmazásazonosítóját](../active-directory/develop/developer-glossary.md#application-id-client-id). 

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Az Azure Portalon válassza az **Azure AD B2C** lehetőséget a szolgáltatások listájából. 

2. A B2C beállításaiban kattintson az **Alkalmazások**, majd a **Hozzáadás** lehetőségre. 

    A mintául szolgáló webalkalmazás bérlőben történő regisztrálásához használja a következő beállításokat:
    
    ![Új alkalmazás hozzáadása](media/active-directory-b2c-tutorials-desktop-app/desktop-app-registration.png)
    
    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name (Név)** | Mintául szolgáló saját WPF-alkalmazás | Adjon meg egy olyan **nevet**, amely megfelelően körülírja az alkalmazást a felhasználók számára. | 
    | **Webalkalmazás vagy webes API szerepeltetése** | Nem | Asztali alkalmazások esetében válassza a **Nem** lehetőséget. |
    | **Natív ügyfél belefoglalása** | Igen | Az asztali alkalmazás natív ügyfélnek számít. |
    | **Átirányítási URI** | Alapértelmezett értékek | Egyedi azonosító, amelyhez az Azure AD B2C átirányítja a felhasználói ügynököt egy OAuth 2.0-válaszban. |
    | **Egyéni átirányítási URI** | `com.onmicrosoft.contoso.appname://redirect/path` | Adja meg `com.onmicrosoft.<your tenant name>.<any app name>://redirect/path` felhasználókövetési adatai jogkivonatok küldéséhez ezt az URI. |
    
3. Kattintson a **Létrehozás** gombra az alkalmazás regisztrálásához.

A regisztrált alkalmazások az Azure AD B2C-bérlő alkalmazásainak listájában jelennek meg. Válassza ki az asztali alkalmazást a listáról. Ekkor megjelenik a regisztrált asztali alkalmazás tulajdonságpanelje.

![Az asztali alkalmazás tulajdonságai](./media/active-directory-b2c-tutorials-desktop-app/b2c-desktop-app-properties.png)

Jegyezze fel az **alkalmazás ügyfél-azonosítóját**. Az azonosító egyedi módon azonosítja az alkalmazást, és az oktatóanyag későbbi részében, az alkalmazás konfigurálásakor lesz rá szükség.

## <a name="create-user-flows"></a>Felhasználói folyamatok létrehozása

Az Azure AD B2C felhasználói folyamat a felhasználói élmény identitás tevékenység határozza meg. Bejelentkezés, regisztráció, a jelszó módosítása és a profilok szerkesztése például olyan közös felhasználókövetési adatai.

### <a name="create-a-sign-up-or-sign-in-user-flow"></a>Regisztrálási vagy bejelentkezési felhasználói folyamat létrehozása

Jelentkezzen be az asztali alkalmazás eléréséhez szükséges felhasználói regisztrációhoz, hozzon létre egy **regisztrálási vagy bejelentkezési felhasználói folyamata**.

1. Az Azure AD B2C portáloldalán válassza **felhasználói folyamatok** kattintson **új felhasználói folyamat**.
2. Az a **ajánlott** lapra, majd **jelentkezzen be, és jelentkezzen be a**.

    A felhasználói folyamat konfigurálásához használja a következő beállításokat:

    ![Regisztrálási vagy bejelentkezési felhasználói folyamat hozzáadása](media/active-directory-b2c-tutorials-desktop-app/add-susi-user-flow.png)

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name (Név)** | SiUpIn | Adjon meg egy **neve** a felhasználói folyamat. A felhasználói interakciósorozat neve a következő előtaggal kezdődik **B2C_1_**. A flow felhasználónév használata **B2C_1_SiUpIn** a mintakód. | 
    | **Identitásszolgáltatók** | Regisztráció e-mail-címmel | Az identitásszolgáltató alapján lehet kétséget kizáróan azonosítani a felhasználót. |

3.  A **felhasználói attribútumokról és jogcímekről**, kattintson a **Továbbiak megjelenítése** , és válassza ki a következő beállításokat:

    ![Adjon hozzá egy felhasználói attribútumokról és jogcímekről](media/active-directory-b2c-tutorials-desktop-app/add-attributes-and-claims.png)

    | Oszlop      | Javasolt értékek  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Attribútum gyűjtése** | Megjelenítendő név és postai irányítószám | Válassza ki a regisztráció során a felhasználótól összegyűjtendő attribútumokat. |
    | **Vissza a jogcímet** | Megjelenítendő név, postai irányítószám, új felhasználó, a felhasználó objektumazonosítója | Válassza ki a [hozzáférési jogkivonatban](../active-directory/develop/developer-glossary.md#access-token) szerepeltetni kívánt [jogcímeket](../active-directory/develop/developer-glossary.md#claim). |

4. Kattintson az **OK** gombra.

5. Kattintson a **létrehozás** a felhasználói folyamat létrehozásához. 

### <a name="create-a-profile-editing-user-flow"></a>Profilszerkesztési felhasználói folyamata létrehozása

Ahhoz, hogy a hogy a felhasználók visszaállíthassák a saját felhasználói profil adatait saját, hozzon létre egy **Profilszerkesztési felhasználói folyamata**.

1. Az Azure AD B2C portáloldalán válassza **felhasználói folyamat** kattintson **új felhasználói folyamat**.
2. Az a **ajánlott** lapra, majd **profilszerkesztést**.

    A felhasználói folyamat konfigurálásához használja a következő beállításokat:

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name (Név)** | SiPe | Adjon meg egy **neve** a felhasználói folyamat. A felhasználói interakciósorozat neve a következő előtaggal kezdődik **B2C_1_**. A flow felhasználónév használata **B2C_1_SiPe** a mintakód. | 
    | **Identitásszolgáltatók** | Bejelentkezés helyi fiókba | Az identitásszolgáltató alapján lehet kétséget kizáróan azonosítani a felhasználót. |

3. A **felhasználói attribútumok**, kattintson a **Továbbiak megjelenítése** , és válassza ki a következő beállításokat:

    | Oszlop      | Javasolt értékek  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Attribútum gyűjtése** | Megjelenítendő név és postai irányítószám | Válassza ki azokat az attribútumokat, amelyeket a felhasználók módosíthatnak a profilszerkesztés során. |
    | **Vissza a jogcímet** | Megjelenítendő név, postai irányítószám, a felhasználó objektumazonosítója | Válassza ki azokat a [jogcímeket](../active-directory/develop/developer-glossary.md#claim), amelyeket egy sikeres profilszerkesztés után szerepeltetni szeretne a [hozzáférési jogkivonatban](../active-directory/develop/developer-glossary.md#access-token). |

4. Kattintson az **OK** gombra.
5. Kattintson a **létrehozás** a felhasználói folyamat létrehozásához. 

### <a name="create-a-password-reset-user-flow"></a>Jelszó alaphelyzetbe állítása felhasználói folyamat létrehozása

Engedélyezze az új jelszó kérését az alkalmazásban, meg kell hoznia egy **jelszó-visszaállítási felhasználói folyamat**. A felhasználói folyamat során a jelszó-változtatási és -jogkivonatokat, amelyeket az alkalmazás fogad a tartalmát fogyasztói élményt ismerteti a művelet sikeres elvégzésekor.

1. Az Azure AD B2C portáloldalán válassza **felhasználói folyamatok** kattintson **új felhasználói folyamat**.
2. Az a **ajánlott** lapra, majd **új jelszó kérésére vonatkozó**.

    A felhasználói folyamat konfigurálásához használja a következő beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name (Név)** | SSPR | Adjon meg egy **neve** a felhasználói folyamat. A felhasználói interakciósorozat neve a következő előtaggal kezdődik **B2C_1_**. A flow felhasználónév használata **B2C_1_SSPR** a mintakód. | 
    | **Identitásszolgáltatók** | Új jelszó kérése e-mail-cím használatával | Az identitásszolgáltató alapján lehet kétséget kizáróan azonosítani a felhasználót. |

3. A **alkalmazásjogcímek**, kattintson a **Továbbiak megjelenítése** , és válassza ki az alábbi beállítást:

    | Oszlop      | Ajánlott érték  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Vissza a jogcímet** | A felhasználó objektumazonosítója | Válassza ki azokat a [jogcímeket](../active-directory/develop/developer-glossary.md#claim), amelyeket egy új jelszó sikeres kérése után szerepeltetni kíván a [hozzáférési jogkivonatban](../active-directory/develop/developer-glossary.md#access-token). |

4. Kattintson az **OK** gombra.
5. Kattintson a **létrehozás** a felhasználói folyamat létrehozásához. 

## <a name="update-desktop-app-code"></a>Az asztali alkalmazás kódjának frissítése

Most, hogy regisztrálta az asztali alkalmazást rendelkezik, és a felhasználói folyamatok létrehozása, konfigurálnia kell az alkalmazást az Azure AD B2C-bérlő használatához. Ebben az oktatóanyagban egy mintául szolgáló asztali alkalmazást fog konfigurálni. 

[Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [böngésszen az adattárban](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop), vagy klónozza a GitHubon található mintát.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

A mintául szolgáló asztali WPF-alkalmazás bemutatja, hogyan használhatja egy asztali alkalmazás az Azure AD B2C-t felhasználók regisztrációjához és bejelentkeztetéséhez, illetve egy védett webes API meghívásához.

Az alkalmazás használja az alkalmazás regisztrációját a bérlőben, és konfigurálja a létrehozott felhasználói folyamatok módosítani szeretné. 

Az alkalmazás beállításainak módosításához:

1. Nyissa meg az `active-directory-b2c-wpf` elemet a Visual Studióban.

2. Az `active-directory-b2c-wpf` projektben nyissa meg az **App.xaml.cs** fájlt, és hajtsa végre a következő frissítéseket:

    ```C#
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "The Application ID for your desktop app registered in your tenant";
    ```

3. Frissítés a **PolicySignUpSignIn** változót a *regisztrálási vagy bejelentkezési felhasználói folyamata* az előző lépésben létrehozott nevét. Ne felejtse el hozzáadni a *B2C_1_* előtagot.

    ```C#
    public static string PolicySignUpSignIn = "B2C_1_SiUpIn";
    ```

## <a name="run-the-sample-desktop-application"></a>A mintául szolgáló asztali alkalmazás futtatása

Nyomja le az **F5** billentyűt az asztali alkalmazás létrehozásához és futtatásához. 

A mintaalkalmazás támogatja a regisztrációt, a bejelentkezést, a profil szerkesztését és az új jelszó kérését. Ez az oktatóanyag kiemeli, hogyan regisztrál egy felhasználó az alkalmazás használatára e-mail-címmel. Ha szeretne, más forgatókönyvekkel kísérletezhet önállóan.

### <a name="sign-up-using-an-email-address"></a>Regisztrálás e-mail-címmel

1. Kattintson a **Bejelentkezés** gombra az asztali alkalmazás felhasználójaként történő bejelentkezéshez. Ez a **B2C_1_SiUpIn** az előző lépésben meghatározott felhasználói folyamatot.

2. Az Azure AD B2C megjelenít egy bejelentkezési oldalt egy regisztrációs hivatkozással. Mivel még nincs fiókja, kattintson a **Regisztráció** hivatkozásra. 

3. A regisztrációs munkafolyamat megjelenít egy lapot, amely a felhasználó azonosító adatait gyűjti be és ellenőrzi az e-mail-cím alapján. A munkafolyamat az a felhasználó jelszavát, és a felhasználói folyamat meghatározott attribútumokat is gyűjti.

    Érvényes e-mail-címet használjon, és ellenőrizze az ellenőrző kód használatával. Állítson be egy jelszót. Adja meg a kért attribútumokhoz tartozó értékeket. 

    ![Regisztrációs munkafolyamat](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Kattintson a **Létrehozás** gombra egy helyi fiók létrehozására az Azure AD B2C-bérlőben.

Mostantól a felhasználók bejelentkezhetnek az e-mail-címükkel, és használhatják az asztali alkalmazást.

> [!NOTE]
> Ha az **API meghívása** gombra kattint, jogosulatlan hozzáférést jelző hibaüzenetet kap. Ezt a hibaüzenetet azért kapja, mert a bemutató bérlő egyik erőforrásához próbál meg hozzáférni. Mivel a hozzáférési jogkivonat csak az Ön Azure AD-bérlőjéhez érvényes, az API-hívás nincs engedélyezve. Lépjen tovább a következő oktatóanyagra, amelyben egy védett webes API-t hozhat létre a bérlője számára. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure AD B2C-bérlőt ahhoz is használhatja, ha más Azure AD B2C-oktatóanyagokat is ki szeretne próbálni. Ha már nincs szüksége rá, akkor [törölheti az Azure AD B2C-bérlőt](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan hozzon létre egy Azure AD B2C-bérlő, felhasználói folyamatokat létrehozni és frissíteni a mintául szolgáló asztali alkalmazást az Azure AD B2C-bérlő használatához. Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan lehet regisztrálni, konfigurálni és meghívni egy védett webes API-t egy asztali alkalmazásból.

> [!div class="nextstepaction"]
> 
